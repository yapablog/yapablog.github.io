---
layout: article
title: 'javascriptを用いた無動力滑空機の計算'
date: 2025-10-21
categories: aerodynamics
math: true
toc: true
---

javascript を用いてフリーフライト機（無動力滑空機）の運動を数値積分で解き、姿勢と位置の時間変化を求めます。MathJax を有効にしているので、式は TeX で記します。

## 想定読者

- 航空工学の基礎（剛体の運動方程式、空気力係数、線形化）を理解している学部生

## 何をするか

- 6DoF（剛体の 3 平行移動＋ 3 回転）の非線形方程式を **機体固定座標系** で定式化
- 縦（longitudinal）・横（lateral-directional）の**小擾乱線形モデル**を導出
- これらを **JavaScript（ブラウザ/Node 両対応）** の簡易オイラー/Runge–Kutta 4 次で積分する最小実装を提示

---

# 前提と記号

- 座標系
  - 地表座標（慣性）: \( \{X_N,Y_E,Z_D\} \)（北・東・下）
  - 機体固定: \( \{x_b,y_b,z_b\} \)（右手系、\(x_b\) 機首方向, \(z_b\) 下向き）
- 状態量
  \[
  \mathbf{x}=
  \begin{bmatrix}
  u&v&w&p&q&r&\phi&\theta&\psi&X&Y&Z
  \end{bmatrix}^\top
  \]
  速度 \((u,v,w)\)、角速度 \((p,q,r)\)、オイラー角 \((\phi,\theta,\psi)\)、位置 \((X,Y,Z)\)
- 慣性モーメント行列（対称機体仮定）
  \[
  \mathbf{J}=\begin{bmatrix}
  J*x&0&-J*{xz}\\
  0&J*y&0\\
  -J*{xz}&0&J_z
  \end{bmatrix}
  \]
- 動圧 \(q\_\infty=\tfrac12\rho V^2\)、翼面積 \(S\)、翼幅 \(b\)、翼弦 \(c\)、迎角 \(\alpha=\arctan(w/u)\)、横滑り角 \(\beta=\arcsin(v/V)\)、\(V=\sqrt{u^2+v^2+w^2}\)

- 空気力・モーメント係数（例）
  \[
  \begin{aligned}
  C*L &= C*{L0}+C*{L\alpha}\alpha+C*{L*q}\frac{qc}{2V}+C*{L*{\delta_e}}\delta_e\\
  C_D &= C*{D0}+k C*L^2\\
  C_Y &= C*{Y*\beta}\beta+C*{Y*p}\frac{pb}{2V}+C*{Y*r}\frac{rb}{2V}+C*{Y*{\delta_r}}\delta_r\\
  C_l &= C*{l*\beta}\beta+C*{l*p}\frac{pb}{2V}+C*{l*r}\frac{rb}{2V}+C*{l*{\delta_a}}\delta_a\\
  C_m &= C*{m0}+C*{m*\alpha}\alpha+C*{m_q}\frac{qc}{2V}+C*{m*{\delta_e}}\delta_e\\
  C_n &= C*{n*\beta}\beta+C*{n*p}\frac{pb}{2V}+C*{n*r}\frac{rb}{2V}+C*{n\_{\delta_r}}\delta_r
  \end{aligned}
  \]

- 空力力（機体固定）  
  \[
  \begin{aligned}
  L&=q*\infty S C_L,\quad D=q*\infty S C*D,\quad Y=q*\infty S C_Y\\
  \text{風軸}\to\text{機体軸変換より}\quad
  \mathbf{F}\_a&=
  \begin{bmatrix}
  -D\cos\alpha+L\sin\alpha\\
  Y\\
  -D\sin\alpha-L\cos\alpha
  \end{bmatrix}
  \end{aligned}
  \]
- 重力（機体軸成分）
  \[
  \mathbf{F}\_g=m g
  \begin{bmatrix}
  -\sin\theta\\
  \sin\phi\cos\theta\\
  \cos\phi\cos\theta
  \end{bmatrix}
  \]
- 空力モーメント
  \[
  \mathbf{M}_a=
  \begin{bmatrix}
  q_\infty S b\, C*l\\
  q*\infty S c\, C*m\\
  q*\infty S b\, C_n
  \end{bmatrix}
  \]

---

# 非線形 6DoF 運動方程式

## 並進（機体軸）

\[
\begin{aligned}
m(\dot{u}+qw-rv) &= F*{ax}+F*{gx}\\
m(\dot{v}+ru-pw) &= F*{ay}+F*{gy}\\
m(\dot{w}+pv-qu) &= F*{az}+F*{gz}
\end{aligned}
\]

## 回転（機体軸）

\[
\mathbf{J}\dot{\boldsymbol{\omega}}+\boldsymbol{\omega}\times(\mathbf{J}\boldsymbol{\omega})=\mathbf{M}\_a
\quad(\boldsymbol{\omega}=[p,q,r]^\top)
\]

## キネマティクス（オイラー角）

\[
\begin{bmatrix}
\dot{\phi}\\\dot{\theta}\\\dot{\psi}
\end{bmatrix}=
\begin{bmatrix}
1 & \sin\phi\tan\theta & \cos\phi\tan\theta\\
0 & \cos\phi & -\sin\phi\\
0 & \sin\phi/\cos\theta & \cos\phi/\cos\theta
\end{bmatrix}
\begin{bmatrix}
p\\q\\r
\end{bmatrix}
\]

## 位置

\[
\begin{bmatrix}\dot{X}\\\dot{Y}\\\dot{Z}\end{bmatrix}
=\mathbf{R}_{b\to n}(\phi,\theta,\psi)
\begin{bmatrix}u\\v\\w\end{bmatrix}
\]
（\(\mathbf{R}_{b\to n}\) は機体 → 慣性の回転行列）

---

# 縦の運動方程式（longitudinal）

小擾乱（定常滑空点 \((U*0,0,W_0,\dots)\) まわり）で、横滑り・ロールを無視すると
\[
\delta\mathbf{x}*\text{lon}=
\begin{bmatrix}\delta u&\delta w&\delta q&\delta\theta\end{bmatrix}^\top,\quad
\delta\mathbf{u}_\text{lon}=\begin{bmatrix}\delta\delta_e\end{bmatrix}
\]
線形化により
\[
\dot{\delta\mathbf{x}}_\text{lon}=\mathbf{A}_\text{lon}\,\delta\mathbf{x}_\text{lon}+\mathbf{B}_\text{lon}\,\delta\mathbf{u}_\text{lon}
\]
\[
\mathbf{A}_\text{lon}=
\begin{bmatrix}
X_u/m & X_w/m & 0 & -g\cos\theta_0\\
Z_u/(m-U_0 Z_{\dot{w}}) & Z*w/(m-U_0 Z*{\dot{w}}) & (Z*q+mU_0)/(m-U_0 Z*{\dot{w}}) & -mg\sin\theta*0/(m-U_0 Z*{\dot{w}})\\
M*u/J_y & M_w/J_y & M_q/J_y & 0\\
0&0&1&0
\end{bmatrix},\quad
\mathbf{B}*\text{lon}=
\begin{bmatrix}
X*{\delta_e}/m\\
Z*{\delta*e}/(m-U_0 Z*{\dot{w}})\\
M\_{\delta_e}/J_y\\
0
\end{bmatrix}
\]

---

# 横の運動方程式（lateral–directional）

\[
\delta\mathbf{x}_\text{lat}=
\begin{bmatrix}\delta v&\delta p&\delta r&\delta\phi&\delta\psi\end{bmatrix}^\top,\quad
\delta\mathbf{u}_\text{lat}=\begin{bmatrix}\delta\delta*a&\delta\delta_r\end{bmatrix}^\top
\]
\[
\dot{\delta\mathbf{x}}*\text{lat}=\mathbf{A}_\text{lat}\,\delta\mathbf{x}_\text{lat}+\mathbf{B}_\text{lat}\,\delta\mathbf{u}_\text{lat}
\]

---

# JavaScript による数値積分（最小実装）

```js
// 6DoF glider simulator (minimal, browser/Node)
const g = 9.80665;

function skew(w) {
  return [0, -w[2], w[1], w[2], 0, -w[0], -w[1], w[0], 0];
}

function mul3(A, b) {
  return [
    A[0] * b[0] + A[1] * b[1] + A[2] * b[2],
    A[3] * b[0] + A[4] * b[1] + A[5] * b[2],
    A[6] * b[0] + A[7] * b[1] + A[8] * b[2],
  ];
}

function Rb2n(phi, theta, psi) {
  const cφ = Math.cos(phi),
    sφ = Math.sin(phi);
  const cθ = Math.cos(theta),
    sθ = Math.sin(theta);
  const cψ = Math.cos(psi),
    sψ = Math.sin(psi);
  // Z(psi)Y(theta)X(phi)
  return [
    cθ * cψ,
    sφ * sθ * cψ - cφ * sψ,
    cφ * sθ * cψ + sφ * sψ,
    cθ * sψ,
    sφ * sθ * sψ + cφ * cψ,
    cφ * sθ * sψ - sφ * cψ,
    -sθ,
    sφ * cθ,
    cφ * cθ,
  ];
}

function eulerRates(p, q, r, phi, theta) {
  const cφ = Math.cos(phi),
    sφ = Math.sin(phi);
  const cθ = Math.cos(theta),
    sθ = Math.sin(theta);
  const tθ = Math.tan(theta);
  return [p + sφ * tθ * q + cφ * tθ * r, cφ * q - sφ * r, (sφ / cθ) * q + (cφ / cθ) * r];
}

function aeroForcesMoments(state, params, ctrl) {
  const { rho, S, b, c } = params.geom;
  const [u, v, w, p, q, r, phi, theta, psi] = state;
  const V = Math.max(1e-3, Math.hypot(u, v, w));
  const qbar = 0.5 * rho * V * V;
  const alpha = Math.atan2(w, u);
  const beta = Math.asin(Math.max(-1, Math.min(1, v / V)));

  const { delta_e = 0, delta_a = 0, delta_r = 0 } = ctrl;

  const C = params.coeffs;
  const CL = C.CL0 + C.CLa * alpha + C.CLq * ((q * c) / (2 * V)) + C.CLde * delta_e;
  const CD = C.CD0 + C.k * CL * CL;
  const CY =
    C.CYb * beta + C.CYp * ((p * b) / (2 * V)) + C.CYr * ((r * b) / (2 * V)) + C.CYdr * delta_r;
  const Cl =
    C.Clb * beta + C.Clp * ((p * b) / (2 * V)) + C.Clr * ((r * b) / (2 * V)) + C.Clda * delta_a;
  const Cm = C.Cm0 + C.Cma * alpha + C.Cmq * ((q * c) / (2 * V)) + C.Cmde * delta_e;
  const Cn =
    C.Cnb * beta + C.Cnp * ((p * b) / (2 * V)) + C.Cnr * ((r * b) / (2 * V)) + C.Cndr * delta_r;

  const L = qbar * S * CL,
    D = qbar * S * CD,
    Y = qbar * S * CY;

  const sa = Math.sin(alpha),
    ca = Math.cos(alpha);
  const Fax = -D * ca + L * sa;
  const Fay = Y;
  const Faz = -D * sa - L * ca;

  const Lm = qbar * S * b * Cl;
  const Mm = qbar * S * c * Cm;
  const Nm = qbar * S * b * Cn;

  return { F: [Fax, Fay, Faz], M: [Lm, Mm, Nm], V, alpha, beta };
}

function derivs(t, x, params, ctrlFunc) {
  const [u, v, w, p, q, r, phi, theta, psi, X, Y, Z] = x;
  const { m, J } = params.mass;
  const ctrl = ctrlFunc(t, x);
  const { F, M } = aeroForcesMoments(x, params, ctrl);

  const fg = [
    -m * g * Math.sin(theta),
    m * g * Math.sin(phi) * Math.cos(theta),
    m * g * Math.cos(phi) * Math.cos(theta),
  ];

  const du = (F[0] + fg[0]) / m + r * v - q * w;
  const dv = (F[1] + fg[1]) / m + p * w - r * u;
  const dw = (F[2] + fg[2]) / m + q * u - p * v;

  const Jx = J.Jx,
    Jy = J.Jy,
    Jz = J.Jz,
    Jxz = J.Jxz;
  const Gamma = Jx * Jz - Jxz * Jxz;
  const Gamma1 = (Jxz * (Jx - Jz + Jy)) / Gamma;
  const Gamma2 = (Jz * (Jz - Jy) + Jxz * Jxz) / Gamma;
  const Gamma3 = Jz / Gamma;
  const Gamma4 = Jxz / Gamma;
  const Gamma5 = (Jz - Jx) / Jy;
  const Gamma6 = Jxz / Jy;
  const Gamma7 = ((Jx - Jy) * Jx + Jxz * Jxz) / Gamma;
  const Gamma8 = Jx / Gamma;

  const pdot = Gamma3 * M[0] + Gamma4 * M[2] + Gamma1 * p * q + Gamma2 * q * r;
  const qdot = (M[1] + Gamma5 * p * r + Gamma6 * (p * p - r * r)) / Jy;
  const rdot = Gamma4 * M[0] + Gamma8 * M[2] + Gamma7 * p * q + Gamma1 * q * r;

  const [phidot, thetadot, psidot] = eulerRates(p, q, r, phi, theta);

  const R = Rb2n(phi, theta, psi);
  const Vn = mul3(R, [u, v, w]); // [Xdot,Ydot,Zdot]

  return [du, dv, dw, pdot, qdot, rdot, phidot, thetadot, psidot, Vn[0], Vn[1], Vn[2]];
}
```
