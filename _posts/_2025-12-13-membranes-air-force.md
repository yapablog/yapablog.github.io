---
layout: article
title: '空気力によるフィルム変形についての考察'
date: 2025-12-13
categories: structure
toc: true
image: /assets/images/tools/ellipsecalc_thumb.png
# references:
#   - author: 'Abbott, I. H., & von Doenhoff, A. E.'
#     year: '1959'
#     title: 'Theory of Wing Sections'
#     publisher: 'Dover Publications'
#     note: '翼型理論のバイブル'
#     url: 'http://airfoiltools.com/airfoil/naca4digit'
---

本記事では、公開中の「[楕円円柱展開図ジェネレーター](/tool-link)」で使用している幾何計算アルゴリズムについて解説します。
3D CAD を使用せずに、JavaScript のみで円柱（および楕円円柱）の交差ラインを求め、板金加工やパイプカットに必要な展開図（型紙）を出力するための数学的アプローチです。

## 1. 問題の定義

空間上に存在する 2 つの無限楕円円柱を定義し、その交差曲線を求めます。

1.  **母管 (Cylinder 1)**: 固定された円柱。中心軸は Z 軸に沿うものとします。
2.  **枝管 (Cylinder 2)**: 母管に対して任意の角度 $\alpha$ で交差する円柱。

それぞれの円柱は、以下のパラメータを持ちます。

- 断面形状：長半径 $R_y$、短半径 $R_x$
- ねじれ角 (Twist)：断面内での回転角 $\theta$

## 2. 幾何学的モデル

### 2.1 母管の定義式

母管の断面は XY 平面上にあり、Z 軸方向に無限に伸びています。
母管のねじれ角を $\theta_1$ とすると、グローバル座標 $(X, Y, Z)$ における母管の表面方程式は、座標回転を考慮して以下のようになります。

まず、断面のローカル座標 $(x', y')$ への変換を行います。

$$
\begin{pmatrix} x' \\ y' \end{pmatrix} = \begin{pmatrix} \cos \theta_1 & \sin \theta_1 \\ -\sin \theta_1 & \cos \theta_1 \end{pmatrix} \begin{pmatrix} X \\ Y \end{pmatrix}
$$

この $(x', y')$ が楕円の方程式を満たします。

$$
\left(\frac{x'}{R_{1x}}\right)^2 + \left(\frac{y'}{R_{1y}}\right)^2 = 1
$$

これが母管の陰関数表現 $F(X, Y) = 0$ となります。

### 2.2 枝管の定義（レイ・トレーシング法）

枝管の表面方程式を解く代わりに、枝管の表面に沿って走る多数の「直線（Ray）」を考え、それらが母管の表面とどこでぶつかるかを計算するアプローチ（レイ・トレーシング）を採用します。これにより、展開図に必要な「長さ」を直接求めることができます。

枝管の断面パラメータ $u$ ($0 \le u < 2\pi$) を考えます。
枝管の軸が基準軸（例：X 軸）から角度 $\alpha$ だけ傾いているとします。

**ステップ 1: 枝管断面上の点 $\mathbf{P}_{local}$**
枝管自身のねじれ角 $\theta_2$ を考慮した断面上の点 $(u_{loc}, v_{loc})$ は以下の通りです。

$$
u_{raw} = R_{2x} \cos u, \quad v_{raw} = R_{2y} \sin u
$$

$$
u_{loc} = u_{raw} \cos \theta_2 - v_{raw} \sin \theta_2
$$

$$
v_{loc} = u_{raw} \sin \theta_2 + v_{raw} \cos \theta_2
$$

**ステップ 2: グローバル座標への変換**
枝管が、原点 $(0,0,0)$ を通り、XZ 平面内で Z 軸に対して角度 $\alpha$ をなすベクトル $\mathbf{d}$ 方向へ伸びているとします。
方向ベクトル $\mathbf{d} = (\sin \alpha, 0, \cos \alpha)$

枝管表面上の任意の直線（母線）は、パラメータ $t$ （原点からの距離）を用いて以下のように表せます。

$$
\mathbf{P}(t) = \mathbf{P}_0(u) + t \cdot \mathbf{d}
$$

ここで、$\mathbf{P}_0(u)$ は $t=0$ における枝管断面のリング上の点であり、交差角度 $\alpha$ に基づいて回転させた座標です。

$$
\mathbf{P}_0(u) = \begin{pmatrix} u_{loc} \cos \alpha \\ v_{loc} \\ -u_{loc} \sin \alpha \end{pmatrix}
$$

※ 座標変換行列は定義に依存しますが、ここでは Y 軸周りの回転としています。

## 3. 交差計算（二次方程式の解法）

枝管上の直線 $\mathbf{P}(t) = (X(t), Y(t), Z(t))$ が母管の表面方程式を満たす $t$ を求めます。

母管の方程式に直線の式を代入します。
$X(t) = P_{0x} + t d_x$
$Y(t) = P_{0y} + t d_y$

これらを母管の式（回転後の座標系 $x', y'$）に代入すると、変数 $t$ に関する二次方程式が得られます。

$$
A t^2 + B t + C = 0
$$

ここで係数 $A, B, C$ は、各円柱の半径、角度、方向ベクトルから算出される定数です。
この二次方程式を解の公式を用いて解きます。

$$
t = \frac{-B \pm \sqrt{B^2 - 4AC}}{2A}
$$

- **判別式 $D = B^2 - 4AC < 0$**: 交差しない（枝管が母管より太い、あるいは外れている）。
- **解の選択**: 通常 2 つの解が得られます（手前と奥）。展開図として必要なのは「母管の外側」の形状であるため、文脈に応じて適切な解（通常は正の方向、あるいは絶対値が大きい方）を選択します。

## 4. 展開図（フラットニング）の生成

3D 空間上の交点群が求まったら、それを 2 次元の平面データに変換します。

### 4.1 枝管の展開 (Branch Pattern)

枝管の展開図は、横軸に「周長」、縦軸に「長さ $t$」をとったグラフになります。
ここで重要なのは、**楕円の周長は角度に対して線形ではない**という点です。単純に角度 $u$ を横軸にすると、実際の型紙としては歪んでしまいます。

楕円の微小弧長 $ds$ は以下で計算されます。

$$
ds = \sqrt{ \left(\frac{dx}{du}\right)^2 + \left(\frac{dy}{du}\right)^2 } du
$$

$$
x = R_x \cos u, \quad y = R_y \sin u
$$

$$
\frac{dx}{du} = -R_x \sin u, \quad \frac{dy}{du} = R_y \cos u
$$

全周長 $S(u)$ を求めるには、これを数値積分（台形則など）します。

$$
S(u) = \int_0^u \sqrt{ R_x^2 \sin^2 \phi + R_y^2 \cos^2 \phi } \, d\phi
$$

本ツールでは、離散化した点群間のユークリッド距離を積算することで近似しています。

- **X 軸**: 累積周長 $S(u)$
- **Y 軸**: 計算された交差距離 $t$

### 4.2 母管開口部の展開 (Hole Pattern)

母管側の穴の形状を展開するには、交点座標 $(X_{int}, Y_{int}, Z_{int})$ を母管表面の 2D 座標に変換します。

1.  **角度の逆算**: 交点 $(X_{int}, Y_{int})$ から、母管断面上の角度 $\psi$ を `atan2` で求めます。
2.  **周長変換**: 枝管同様、楕円積分を用いて角度 $\psi$ を母管表面上の弧長 $L_{arc}$ に変換します。
3.  **マッピング**:
    - **X 軸**: 母管表面の弧長 $L_{arc}$
    - **Y 軸**: 母管軸方向の高さ $Z_{int}$

## 5. JavaScript による実装例

数値積分の実装は計算コストとのトレードオフになりますが、Web ブラウザ上でも十分高速に動作します。

```javascript
// 楕円弧長の簡易数値積分
function calcEllipseArc(rx, ry, startAngle, endAngle) {
  const steps = 50; // 分割数
  let sum = 0;
  const dt = (endAngle - startAngle) / steps;

  for (let i = 0; i < steps; i++) {
    const t = startAngle + dt * (i + 0.5);
    // ds/dt = sqrt( (-rx sin t)^2 + (ry cos t)^2 )
    const ds = Math.sqrt(Math.pow(-rx * Math.sin(t), 2) + Math.pow(ry * Math.cos(t), 2));
    sum += ds * dt;
  }
  return sum;
}
```

このアルゴリズムにより、任意の楕円率、任意の角度で交差するパイプの正確な展開図（型紙）を動的に生成することが可能になります。
