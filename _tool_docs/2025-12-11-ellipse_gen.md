---
layout: article
title: '楕円円柱同士の交差と展開図生成アルゴリズム'
date: 2025-12-11
categories: geometry
toc: true
image: assets/images/tools/ellipsecalc_thumb.png
# references:
#   - author: 'Abbott, I. H., & von Doenhoff, A. E.'
#     year: '1959'
#     title: 'Theory of Wing Sections'
#     publisher: 'Dover Publications'
#     note: '翼型理論のバイブル'
#     url: 'http://airfoiltools.com/airfoil/naca4digit'
---

本記事では、公開中の「[楕円円柱展開図ジェネレーター](/ellipsecalc/)」で使用している幾何計算アルゴリズムを解説します。  
目的は、2 本の楕円円柱の交差線を計算し、枝管の展開図（型紙）として 2D 出力することです。

## 1. 問題設定

このツールでは、以下の 2 つの無限楕円円柱を扱います。

1. **母管 (Main Pipe)**: 軸方向を **Y 軸** とする固定円柱  
2. **枝管 (Branch Pipe)**: 母管と角度 $\alpha$ で交差する円柱

入力パラメータは次の通りです。

- 断面半径: 短半径 $R_x$、長半径 $R_z$
- 断面回転角: $\theta$（断面内のねじれ）
- 交差条件: 交差角 $\alpha$、芯ズレ `offset`

## 2. 幾何モデル

### 2.1 母管の陰関数

母管の断面は XZ 平面上の楕円で、軸方向は Y です。  
母管回転角を $\theta_1$ とすると、グローバル座標 $(X, Y, Z)$ から断面ローカル座標 $(x', z')$ への変換は

$$
\begin{pmatrix} x' \\ z' \end{pmatrix}
=
\begin{pmatrix}
\cos \theta_1 & \sin \theta_1 \\
-\sin \theta_1 & \cos \theta_1
\end{pmatrix}
\begin{pmatrix} X \\ Z \end{pmatrix}
$$

です。楕円条件は

$$
\left(\frac{x'}{R_{1x}}\right)^2 + \left(\frac{z'}{R_{1z}}\right)^2 = 1
$$

となります。

### 2.2 枝管の母線パラメータ化

枝管は、断面角 $\phi \in [0, 2\pi)$ ごとに 1 本の母線（直線）を持つと考えます。  
この表現にすると、「各母線が母管とどこで交わるか」を解けばよくなり、展開に必要な高さを直接得られます。

枝管断面（回転前）は

$$
u_0 = R_{2x}\cos\phi,\qquad
w_0 = R_{2z}\sin\phi
$$

枝管の断面回転角 $\theta_2$ を反映すると

$$
u = u_0\cos\theta_2 - w_0\sin\theta_2,\qquad
w = u_0\sin\theta_2 + w_0\cos\theta_2
$$

枝管軸方向パラメータを $v$ とし、交差角 $\alpha$（X 軸回り回転）と芯ズレ `offset` を適用すると、母線上の点は

$$
\begin{aligned}
X &= u + \mathrm{offset} \\
Y &= v\cos\alpha - w\sin\alpha \\
Z &= v\sin\alpha + w\cos\alpha
\end{aligned}
$$

で表せます。

## 3. 交差計算（2 次方程式）

上式を母管の楕円条件に代入すると、$v$ に関する 2 次方程式

$$
Av^2 + Bv + C = 0
$$

が得られます。実装では次の中間変数を使っています。

$$
\begin{aligned}
K_1 &= (u+\mathrm{offset})\cos\theta_1 + w\cos\alpha\sin\theta_1 \\
K_2 &= \sin\alpha\sin\theta_1 \\
K_3 &= -(u+\mathrm{offset})\sin\theta_1 + w\cos\alpha\cos\theta_1 \\
K_4 &= \sin\alpha\cos\theta_1
\end{aligned}
$$

$$
\begin{aligned}
A &= \frac{K_2^2}{R_{1x}^2} + \frac{K_4^2}{R_{1z}^2},\quad
B = \frac{2K_1K_2}{R_{1x}^2} + \frac{2K_3K_4}{R_{1z}^2},\\
C &= \frac{K_1^2}{R_{1x}^2} + \frac{K_3^2}{R_{1z}^2} - 1
\end{aligned}
$$

解は

$$
v = \frac{-B \pm \sqrt{B^2 - 4AC}}{2A}
$$

です。

- 判別式 $D = B^2 - 4AC < 0$ なら、その断面角では交差しません。
- $D \ge 0$ のときは 2 解のうち大きい方を `Top`、小さい方を `Bottom` として扱います。

## 4. 展開図への変換

### 4.1 周長座標の作成

展開図の横軸は「角度」ではなく「楕円周長の累積値」を使います。  
これにより、型紙上の距離が実幾何に一致します。

離散化点 $\phi_i$ に対し、回転前楕円点 $(u_{0,i}, w_{0,i})$ から

$$
s_i = s_{i-1} + \sqrt{(u_{0,i}-u_{0,i-1})^2 + (w_{0,i}-w_{0,i-1})^2}
$$

を積算して周長座標 $s_i$ を得ます。

### 4.2 2D プロットと出力

最終的な展開線は以下です。

- `Top`: $(s_i, v_{\text{top},i})$
- `Bottom`: $(s_i, v_{\text{bottom},i})$

これをブラウザ上で可視化し、DXF/PDF に出力します。  
判別式が負になる区間は `null` を挿入して線を分断し、非交差領域を明示します。

## 5. 実装例（係数計算部）

以下は、1 つの $\phi$ に対して $A,B,C$ を組み立てる最小例です。

```javascript
function solveIntersectionV(u, w, offset, alpha, theta1, Rx1, Rz1) {
  const ca = Math.cos(alpha), sa = Math.sin(alpha);
  const c1 = Math.cos(theta1), s1 = Math.sin(theta1);

  const us = u + offset;
  const K1 = us * c1 + w * ca * s1;
  const K2 = sa * s1;
  const K3 = -us * s1 + w * ca * c1;
  const K4 = sa * c1;

  const A = (K2 * K2) / (Rx1 * Rx1) + (K4 * K4) / (Rz1 * Rz1);
  const B = (2 * K1 * K2) / (Rx1 * Rx1) + (2 * K3 * K4) / (Rz1 * Rz1);
  const C = (K1 * K1) / (Rx1 * Rx1) + (K3 * K3) / (Rz1 * Rz1) - 1;

  const D = B * B - 4 * A * C;
  if (!Number.isFinite(A) || Math.abs(A) < 1e-12 || D < 0) return null;

  const root = Math.sqrt(D);
  const v1 = (-B + root) / (2 * A);
  const v2 = (-B - root) / (2 * A);
  return { top: Math.max(v1, v2), bottom: Math.min(v1, v2) };
}
```

このアプローチにより、楕円率・回転角・交差角・芯ズレが変わる任意ケースでも、ブラウザ上で安定して交差線と展開図を生成できます。
