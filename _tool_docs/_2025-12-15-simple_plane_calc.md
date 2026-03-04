---
layout: article
title: '【ツール解説】静安定計算と縦運動シミュレーション'
date: 2025-12-15
categories: tool_document
toc: true
image: /assets/images/tools/ellipsecalc_thumb.png
tags: [Physics, Mathematics, JavaScript, Engineering]
# references:
#   - author: 'Abbott, I. H., & von Doenhoff, A. E.'
#     year: '1959'
#     title: 'Theory of Wing Sections'
#     publisher: 'Dover Publications'
#     note: '翼型理論のバイブル'
#     url: 'http://airfoiltools.com/airfoil/naca4digit'
---

本記事では、先日公開した「航空機設計＆フライトシミュレーター」の技術的解説を行います。
このツールは、ユーザーが定義した航空機のパラメータから**静的安定性（Static Stability）**を判定し、さらに運動方程式を数値積分することで**飛行軌跡（Trajectory）**をブラウザ上で即座に可視化するものです。

今回は、その核心となる数理モデルと、JavaScript による実装アプローチについて詳説します。

## 1. 目的と問題定義

### 入力と出力

このシステムの目的は、模型飛行機やドローンの設計段階における「手戻り」を減らすことです。

- **入力 ($Input$)**: 主翼・尾翼の幾何形状（スパン、翼弦長、配置）、質量特性、初期飛行条件。
- **出力 ($Output$)**:
  1.  **静安定余裕 ($Static Margin$)**: 機体が勝手に姿勢を回復しようとする復元力の指標。
  2.  **飛行軌跡**: 時間経過に伴う機体の位置 $(x, z)$ と姿勢角 $\theta$ の変化。

### 扱う物理モデル

航空機の運動は 6 自由度（6-DOF）ですが、今回は「きちんと滑空するか」を確認するために、**縦方向（Longitudinal）の 3 自由度**（前後速度 $u$、上下速度 $w$、ピッチ角速度 $q$）に限定した剛体運動モデルを扱います。

## 2. 数理モデルの解説

ここからは、実装のベースとなった航空力学の数式を解説します。

### 2.1 幾何特性：平均空力翼弦 ($MAC$)

テーパー翼（先細りの翼）を扱う際、揚力計算の基準となる「平均的な翼弦長」が必要です。これを**平均空力翼弦（Mean Aerodynamic Chord: MAC, $\bar{c}$）**と呼びます。翼根を $c_r$、翼端を $c_t$ とすると、以下の積分から導出されます。

$$
\bar{c} = \frac{2}{3} c_r \frac{1 + \lambda + \lambda^2}{1 + \lambda}
$$

ここで $\lambda = c_t / c_r$ はテーパー比です。この $\bar{c}$ が、モーメント計算の基準長となります。

_(ここに台形翼と MAC の位置関係を示す図が入る想定)_

### 2.2 静的安定性と中立点 ($Neutral Point$)

機体がピッチ（縦）方向に安定であるためには、**重心 ($X_{cg}$) が中立点 ($X_{np}$) よりも前方にある**必要があります。

中立点 $X_{np}$ は、機体全体の空力中心（迎角が変化してもピッチングモーメントが変化しない点）です。主翼の空力中心 $X_{ac\_w}$ を基準に、尾翼の効果を加算して求めます。

$$
X_{np} = X_{ac\_w} + \frac{C_{L\alpha\_t}}{C_{L\alpha\_w}} V_H \left( 1 - \frac{d\epsilon}{d\alpha} \right) \eta_t \bar{c}
$$

ここで重要な変数は以下の通りです。

- $V_H$: **尾翼容積比**。尾翼が「どれくらいのモーメントアーム」と「面積」を持つかの指標。 ($V_H = \frac{S_t l_t}{S_w \bar{c}}$)
- $\frac{d\epsilon}{d\alpha}$: **吹き下ろし勾配**。主翼が発生させる渦が尾翼位置での気流角度をどれだけ変化させるか。
- $\eta_t$: 尾翼効率（動圧比）。

静安定余裕（Static Margin, $SM$）は以下で定義され、これが正（$X_{np} > X_{cg}$）であれば安定と判定します。

$$
SM = \frac{X_{np} - X_{cg}}{\bar{c}}
$$

### 2.3 縦の運動方程式 ($Equations of Motion$)

シミュレーションでは、機体に固定された座標系（Body Axis）での運動方程式を解きます。状態ベクトルは $\mathbf{x} = [u, w, q, \theta, x, z]^T$ です。

$$
\begin{cases}
m(\dot{u} + qw) = -mg \sin\theta + F_x \\
m(\dot{w} - qu) = mg \cos\theta + F_z \\
I_y \dot{q} = M
\end{cases}
$$

ここで $F_x, F_z$ は空気力（揚力 $L$ と抗力 $D$）の成分分解、 $M$ はピッチングモーメントです。

$$
\begin{align}
L &= \frac{1}{2}\rho V^2 S C_L \\
D &= \frac{1}{2}\rho V^2 S C_D \\
M &= \frac{1}{2}\rho V^2 S \bar{c} C_m
\end{align}
$$

特に $C_m$ には、静的な復元モーメントに加え、**ピッチダンピング**（$C_{mq}$：回転を止めようとする抵抗）を含めることが、発散しないシミュレーションには不可欠です。

## 3. アルゴリズムの実装

数式をコードに落とし込む際のポイントは以下の 2 点です。

### 3.1 釣り合い状態（Trim）の計算

いきなりシミュレーションを始めると、機体は激しく振動します。まずは「力が釣り合って真っ直ぐ飛ぶ速度と迎角」を求める必要があります。
これは $C_m(\alpha) = 0$ となる迎角 $\alpha_{trim}$ を求める求根問題となります。今回は**ニュートン法**を用いて実装しました。

1.  初期値 $\alpha_0$ を設定。
2.  モーメント係数 $C_m(\alpha_i)$ とその微分 $C_m'(\alpha_i)$ を計算。
3.  $\alpha_{i+1} = \alpha_i - C_m(\alpha_i) / C_m'(\alpha_i)$ で更新。
4.  収束したら、その時の揚力係数から釣り合い速度 $V_{trim}$ を逆算。

### 3.2 時間発展（数値積分）

微分方程式の解法には、計算コストと精度のバランスから **オイラー法（Euler Method）** を採用しました。時間刻み $\Delta t = 0.01[s]$ 程度であれば、ブラウザ上のフライトシミュレーションとしては十分な精度が得られます。

各ステップで：

1.  現在の速度ベクトルから迎角 $\alpha = \tan^{-1}(w/u)$ を計算。
2.  主翼・尾翼それぞれの $C_L, C_D$ を計算（尾翼は吹き下ろし $\epsilon$ を考慮）。
3.  合力・合モーメントを算出し、加速度・角加速度を得る。
4.  速度・位置・角度を更新。
5.  もし $z > 0$（地面到達）ならループ終了。

## 4. 実装コード（抜粋）

以下は、運動方程式の数値積分を行うコア部分の JavaScript コードです。物理式の項がどのようにコード上の変数に対応しているかに注目してください。

(javascript
// 運動方程式の積分ステップ（オイラー法）
for(let t=0; t<max*t; t+=dt) {
// 1. 現在の総速度 V と 迎角 alpha の算出
const V = Math.sqrt(u**2 + w**2);
const alpha = Math.atan2(w, u); // Body Axis での迎角
const dynQ = 0.5 * RHO \_ V\*\*2; // 動圧 (Dynamic Pressure)

    // 2. 空気力の計算 (係数は別途計算済みとする)
    // 主翼の揚力・抗力
    const CLw = cla_w * (alpha + mw_angle);
    const CDw = CD0 + k_w * CLw**2; // 誘導抗力を考慮した極曲線

    // 尾翼の揚力 (吹き下ろし epsilon と ピッチレート q による誘導角を考慮)
    const eps = de_da * (alpha + mw_angle);
    const q_induced = (q * (x_ac_t - x_cg)) / V; // 回転による尾翼位置の速度変化成分
    const CLt = cla_t * ((alpha + ht_angle) - eps + q_induced);

    // 全機の揚力・抗力・モーメント
    const L_total = dynQ * (S_w * CLw + S_t * CLt);
    const D_total = dynQ * (S_w * CDw + S_t * CDt);
    const M_total = dynQ * (S_w * CLw * (x_cg - x_ac_w) - S_t * CLt * (x_ac_t - x_cg))
                    - damping_factor * q; // 簡易ダンピング項

    // 3. 運動方程式 (Body Axis)
    // Fz = -L cos(a) - D sin(a) + mg cos(theta) ... 重力項含む
    const Fx_aero = -D_total * Math.cos(alpha) + L_total * Math.sin(alpha);
    const Fz_aero = -D_total * Math.sin(alpha) - L_total * Math.cos(alpha);

    const du = -q*w + G*Math.sin(theta) + Fx_aero/m_total;
    const dw = q*u  + G*Math.cos(theta) + Fz_aero/m_total;
    const dq = M_total / Iy;

    // 4. 状態更新
    u += du * dt;
    w += dw * dt;
    q += dq * dt;
    theta += q * dt;

    // 5. 地球座標系(Trajectory)への変換と保存
    // ... (省略) ...

}
)

### 解説

コード内の `q_induced` は、機体が回転（ピッチング）している際に尾翼が感じる「追加の迎角」を表しています。これが空力的な減衰（ダンピング）の主要因となり、シミュレーションの安定性に大きく寄与します。これを考慮しないと、機体は永久に振動し続けてしまいます。

## まとめ

本記事では、航空機の設計ツールにおける数理モデルとその実装について解説しました。

- **静安定余裕**の計算により、設計した機体がまともに飛ぶかを瞬時に判定。
- **非線形な運動方程式**を数値積分することで、短周期モードやフゴイド運動といった実際の飛行特性を再現。

このアルゴリズムは、パラグライダーや紙飛行機、固定翼ドローンの設計など、幅広い分野に応用可能です。次は、横・方向安定（Lateral-Directional Stability）の実装や、よりリアルな 3D 描画への拡張が課題となります。
