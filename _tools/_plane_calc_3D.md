---
layout: tools
title: 航空機設計計算
ToolName: 航空機の基本設計と安定性計算ツール
permalink: /planecalc_3D/
Description: 主翼や尾翼の形から機体性能を簡易的に計算するWebツールです。
HowtoUse: |
  1. まず **コード長** と **点群データ** を入力してください。
  2. NACA4桁翼型であれば、コードを入力して「生成」ボタンから自動生成することが可能です。
  3. もし正規化されていない場合は、正規化ボタンを押してください。
  4. 「計算・描画実行」ボタンを押すと、右側（スマホの場合は下側）に形状と解析結果が表示されます。
---

{::nomarkdown}

<script src="https://cdn.plot.ly/plotly-2.24.1.min.js"></script>

<script src="https://polyfill.io/v3/polyfill.min.js?features=es6"></script>
<script id="MathJax-script" async src="https://cdn.jsdelivr.net/npm/mathjax@3/es5/tex-mml-chtml.js"></script>

<div class="w-full max-w-7xl mx-auto px-4 py-8">
  <div class="grid grid-cols-1 lg:grid-cols-12 gap-6">

    <div class="lg:col-span-4 space-y-4">
      <div class="bg-white rounded-xl shadow border border-gray-200 overflow-hidden flex flex-col h-full ">
        <div class="bg-[var(--color-primary)] text-white px-5 py-3 border-b border-gray-100 flex-shrink-0">
          <h2 class="text-lg font-bold m-0 text-white">設計パラメータ</h2>
        </div>

        <div class="p-5 space-y-6 flex-1">

          <div>
            <label class="block text-xs font-bold text-gray-500 uppercase tracking-wider mb-2">機体素材 (面密度)</label>
            <select id="material" class="w-full p-2 text-sm border border-gray-300 rounded focus:ring-2 focus:ring-[var(--color-brand)] bg-gray-50">
                <option value="0.15">バルサ/軽量素材 (約150g/m²)</option>
                <option value="0.25">厚紙/スチレン (約250g/m²)</option>
                <option value="0.40">プラスチック/CFRP (約400g/m²)</option>
            </select>
          </div>

          <div class="border-l-4 border-blue-500 pl-3">
            <h3 class="text-sm font-bold text-gray-800 mb-2">主翼 (Main Wing)</h3>
            <div class="grid grid-cols-2 gap-x-2 gap-y-3 text-sm">
                <div>
                    <span class="text-xs text-gray-500 block">スパン (mm)</span>
                    <input type="number" id="mw_span" value="800" class="w-full p-1.5 border rounded">
                </div>
                <div>
                    <span class="text-xs text-gray-500 block">取り付け角 (deg)</span>
                    <input type="number" id="mw_angle" value="2.0" step="0.1" class="w-full p-1.5 border rounded">
                </div>
                <div>
                    <span class="text-xs text-gray-500 block">翼根コード (mm)</span>
                    <input type="number" id="mw_root" value="180" class="w-full p-1.5 border rounded">
                </div>
                <div>
                    <span class="text-xs text-gray-500 block">翼端コード (mm)</span>
                    <input type="number" id="mw_tip" value="120" class="w-full p-1.5 border rounded">
                </div>
                <div>
                    <span class="text-xs text-gray-500 block">上反角 (deg)</span>
                    <input type="number" id="mw_dihedral" value="5.0" class="w-full p-1.5 border rounded">
                </div>
                <div>
                    <span class="text-xs text-gray-500 block">後退角 (deg)</span>
                    <input type="number" id="mw_sweep" value="5.0" class="w-full p-1.5 border rounded">
                </div>
            </div>
          </div>

          <div class="border-l-4 border-red-500 pl-3">
            <h3 class="text-sm font-bold text-gray-800 mb-2">水平尾翼 (H. Tail)</h3>
            <div class="grid grid-cols-2 gap-x-2 gap-y-3 text-sm">
                <div>
                    <span class="text-xs text-gray-500 block">スパン (mm)</span>
                    <input type="number" id="ht_span" value="320" class="w-full p-1.5 border rounded">
                </div>
                <div>
                    <span class="text-xs text-gray-500 block">取り付け角 (deg)</span>
                    <input type="number" id="ht_angle" value="-1.5" step="0.1" class="w-full p-1.5 border rounded">
                </div>
                <div>
                    <span class="text-xs text-gray-500 block">翼根コード (mm)</span>
                    <input type="number" id="ht_root" value="110" class="w-full p-1.5 border rounded">
                </div>
                <div>
                    <span class="text-xs text-gray-500 block">翼端コード (mm)</span>
                    <input type="number" id="ht_tip" value="80" class="w-full p-1.5 border rounded">
                </div>
                <div class="col-span-2">
                    <span class="text-xs text-gray-500 block">位置 (主翼前縁〜尾翼前縁) (mm)</span>
                    <input type="number" id="ht_dist" value="500" class="w-full p-1.5 border rounded">
                </div>
            </div>
          </div>

          <div class="border-l-4 border-yellow-500 pl-3">
            <h3 class="text-sm font-bold text-gray-800 mb-2">垂直尾翼 (V. Tail)</h3>
            <div class="grid grid-cols-2 gap-x-2 gap-y-3 text-sm">
                <div>
                    <span class="text-xs text-gray-500 block">高さ (mm)</span>
                    <input type="number" id="vt_span" value="140" class="w-full p-1.5 border rounded">
                </div>
                <div>
                    <span class="text-xs text-gray-500 block">位置 (mm)</span>
                    <input type="number" id="vt_dist" value="480" class="w-full p-1.5 border rounded">
                </div>
                <div>
                    <span class="text-xs text-gray-500 block">翼根コード (mm)</span>
                    <input type="number" id="vt_root" value="130" class="w-full p-1.5 border rounded">
                </div>
                <div>
                    <span class="text-xs text-gray-500 block">翼端コード (mm)</span>
                    <input type="number" id="vt_tip" value="70" class="w-full p-1.5 border rounded">
                </div>
            </div>
          </div>

          <div class="bg-gray-50 p-3 rounded border border-gray-200">
            <h3 class="text-sm font-bold text-gray-800 mb-2">調整・環境</h3>
            <div class="grid grid-cols-2 gap-x-2 gap-y-3 text-sm">
                <div>
                    <span class="text-xs text-gray-500 block">おもり重量 (g)</span>
                    <input type="number" id="ballast_mass" value="35" class="w-full p-1.5 border rounded bg-white">
                </div>
                <div>
                    <span class="text-xs text-gray-500 block">おもり位置 (mm)</span>
                    <input type="number" id="ballast_pos" value="0" placeholder="0=主翼先端" class="w-full p-1.5 border rounded bg-white">
                </div>
                <div>
                    <span class="text-xs text-gray-500 block">初速度 (m/s)</span>
                    <input type="number" id="sim_v0" value="9.0" step="0.5" class="w-full p-1.5 border rounded border-amber-200 bg-amber-50">
                </div>
                <div>
                    <span class="text-xs text-gray-500 block">射出角度 (deg)</span>
                    <input type="number" id="sim_pitch" value="0" class="w-full p-1.5 border rounded border-amber-200 bg-amber-50">
                </div>
            </div>
          </div>

          <div class="pt-2 flex gap-3">
             <button onclick="resetDefaults()" class="px-4 py-2 text-sm text-gray-600 bg-white border border-gray-300 rounded hover:bg-gray-50 transition-colors">
              リセット
            </button>
            <button onclick="runCalculation()" class="flex-1 py-3 bg-[var(--color-brand)] text-white font-bold rounded shadow hover:opacity-90 transition-opacity">
              計算・実行
            </button>
          </div>
        </div>
      </div>
    </div>

    <div class="lg:col-span-8 space-y-6">

      <div class="bg-white rounded-xl shadow-sm border border-gray-200 flex flex-col overflow-hidden">
        <div class="bg-gray-50 px-4 py-2 border-b border-gray-200 flex justify-between items-center">
          <h5 class="font-bold text-gray-700 m-0 text-sm">機体形状確認 & 重心位置 (3D)</h5>
          <span class="text-[10px] text-gray-400 uppercase">Interactive 3D</span>
        </div>
        <div class="relative w-full h-[300px] bg-gray-50">
           <div id="plotArea3D" class="absolute inset-0 w-full h-full"></div>
        </div>
      </div>

      <div class="bg-white rounded-xl shadow-sm border border-gray-200 p-5">
          <div id="stabilityAlert" class="mb-5 p-3 rounded text-center font-bold text-sm"></div>

          <div class="grid grid-cols-2 md:grid-cols-4 gap-4 mb-2 text-center">
             <div class="p-2 bg-gray-50 rounded">
                <div class="text-[10px] text-gray-500 uppercase">全重量</div>
                <div class="text-xl font-bold text-gray-800" id="res_weight">-</div>
             </div>
             <div class="p-2 bg-gray-50 rounded">
                <div class="text-[10px] text-gray-500 uppercase">静安定余裕 (SM)</div>
                <div class="text-xl font-bold text-gray-800" id="res_sm">-</div>
             </div>
             <div class="p-2 bg-gray-50 rounded">
                <div class="text-[10px] text-gray-500 uppercase">釣り合い速度</div>
                <div class="text-xl font-bold text-[var(--color-brand)]" id="res_vtrim">-</div>
             </div>
             <div class="p-2 bg-gray-50 rounded">
                <div class="text-[10px] text-gray-500 uppercase">滑空比 (L/D)</div>
                <div class="text-xl font-bold text-gray-800" id="res_ld">-</div>
             </div>
          </div>

          <details class="mt-4">
              <summary class="text-xs text-gray-500 cursor-pointer hover:text-[var(--color-brand)]">詳細データを見る</summary>
              <table class="w-full text-sm text-left border-collapse mt-2">
                <tbody id="resultBody" class="text-gray-700 text-xs"></tbody>
              </table>
          </details>
      </div>

      <div class="bg-white rounded-xl shadow-sm border border-gray-200 flex flex-col overflow-hidden">
        <div class="bg-gray-50 px-4 py-2 border-b border-gray-200 flex justify-between items-center">
          <h5 class="font-bold text-gray-700 m-0 text-sm">縦の運動軌跡 (Trajectory)</h5>
        </div>
        <div class="relative w-full h-[300px] bg-white">
          <div id="plotArea2D" class="absolute inset-0 w-full h-full"></div>
        </div>
      </div>
    </div>

  </div>
</div>

<script>
/**
 * 航空力学計算 & シミュレーションロジック
 */

// --- Constants ---
const RHO = 1.225; // kg/m^3
const G = 9.80665;
const PI = Math.PI;
const D2R = PI / 180;
const R2D = 180 / PI;

// --- Helper Functions ---
const getVal = (id) => parseFloat(document.getElementById(id).value);
const setTxt = (id, txt) => {
    const el = document.getElementById(id);
    if(el) el.innerText = txt;
};

// --- 1. Geometry Calculation ---
function calculateWingProperties(span_mm, root_mm, tip_mm, sweep_deg=0) {
    const b = span_mm / 1000;
    const cr = root_mm / 1000;
    const ct = tip_mm / 1000;
    
    const S = (cr + ct) * b / 2;
    const AR = (S > 0) ? (b**2) / S : 0;
    const taper = (cr > 0) ? ct / cr : 1;
    
    // Mean Aerodynamic Chord (MAC)
    const mac = (cr > 0) ? (2/3) * cr * ((1 + taper + taper**2) / (1 + taper)) : 0;
    // MACのY位置 (スパン方向の中心からの距離)
    const y_mac = (b/6) * ((1 + 2*taper) / (1 + taper));
    // MACの前縁X位置 (後退角考慮)
    const x_mac_le = Math.tan(sweep_deg * D2R) * y_mac; 

    return { S, AR, mac, b, cr, ct, x_mac_le, y_mac, taper };
}

// --- 2. Inertia Calculation ---
function calculateAccurateInertia(mw, ht, vt, inputs, m_total, x_cg) {
    // 各パーツの質量概算
    const mat_density = inputs.mat / 1000; // kg/m2
    const m_w = mw.S * mat_density;
    const m_h = ht.S * mat_density;
    const m_v = vt.S * mat_density;
    const m_ballast = inputs.ball_m;

    let Ix = 0, Iy = 0, Iz = 0, Ixz = 0;

    // Helper: Add Point Mass Inertia
    const addPoint = (m, x, y, z) => {
        // x,y,z are relative to CG
        Ix += m * (y**2 + z**2);
        Iy += m * (x**2 + z**2);
        Iz += m * (x**2 + y**2);
        Ixz += m * (x * z);
    };

    // Helper: Add Wing Strip Inertia
    const addWingInertia = (comp, ox, oz, sweep, dihedral, isVert) => {
        const strips = 10;
        const dy = (comp.b / 2) / strips;
        const density = (comp.S * mat_density) / comp.S; // kg/m2
        
        for(let i=0; i<strips; i++) {
            const y_pos = (i + 0.5) * dy;
            const chord = comp.cr + (comp.ct - comp.cr) * (y_pos / (comp.b/2));
            const area = chord * dy;
            const dm = area * density;

            const x_loc = y_pos * Math.tan(sweep*D2R) + chord*0.4; // Local AC approx
            
            // Transform to global relative to CG
            let dx, dy_g, dz_g;
            if(isVert) {
                dx = (ox + x_loc) - x_cg;
                dy_g = 0;
                dz_g = oz + y_pos; // Up
                addPoint(dm, dx, dy_g, dz_g); // Single vertical tail
            } else {
                const z_dh = y_pos * Math.sin(dihedral*D2R); // Dihedral up
                const y_dh = y_pos * Math.cos(dihedral*D2R);
                dx = (ox + x_loc) - x_cg;
                
                // Right
                addPoint(dm, dx, y_dh, oz - z_dh); // Z is down, so Up is -Z. offset oz usually 0
                // Left
                addPoint(dm, dx, -y_dh, oz - z_dh);
            }
        }
    };

    addWingInertia(mw, 0, 0, inputs.mw_sweep, inputs.mw_dihedral, false);
    addWingInertia(ht, inputs.ht_dist/1000, 0, 0, 0, false);
    addWingInertia(vt, inputs.vt_dist/1000, 0, 15, 0, true);

    // Ballast (Point mass)
    addPoint(m_ballast, inputs.ball_p - x_cg, 0, 0);
    
    const min_Iy = m_total * (inputs.ht_dist/1000)**2 * 0.05; 
    if(Iy < min_Iy) Iy = min_Iy;

    return { Ix, Iy, Iz, Ixz };
}


/**
 * 1. モーメントの方程式
 * 外部モーメントから角加速度を計算します。
 * * @param {Object} dMoments - 摂動モーメント { dL, dM, dN }
 * @param {Object} consts - 機体慣性諸元 { Ix, Iy, Iz, Ixz }
 * @returns {Object} 角加速度 { dotP, dotQ, dotR }
 */
function calculateAngularAccelerations(dMoments, consts) {
    const { dL, dM, dN } = dMoments;
    const { Ix, Iy, Iz, Ixz } = consts;

    // 行列式 (Determinant for lateral coupling)
    const Gamma = (Ix * Iz) - (Ixz * Ixz);

    // 縦系 (Longitudinal): ピッチのみ独立
    const dotQ = dM / Iy;

    // 横・方向系 (Lateral-Directional): ロールとヨーは連成する
    // 連立方程式を解いた形:
    const dotP = (Iz * dL + Ixz * dN) / Gamma;
    const dotR = (Ixz * dL + Ix * dN) / Gamma;

    return { dotP, dotQ, dotR };
}

/**
 * 2. キネマティクス方程式
 * 機体角速度からオイラー角速度（レート）を計算します。
 * 線形化近似により、p, q, r はそのままオイラー角速度となります。
 * * @param {Object} angVel - 角速度 { p, q, r }
 * @returns {Object} オイラー角速度 { dotPhi, dotTheta, dotPsi } (Roll, Pitch, Yaw rates)
 */
function calculateEulerRates(angVel, trim) {
    const { U0, Theta0 } = trim;
    // 線形化理論では、微小角において角速度とオイラー角速度は等しいと近似します
    const c0 = Math.cos(Theta0);
    const t0 = Math.tan(Theta0);

    const dotPhi   = angVel.p + angVel.r * t0;     // roll rate
    const dotTheta = angVel.q;              // pitch rate
    const dotPsi   = angVel.r / c0;         // yaw rate

    return { dotPhi, dotTheta, dotPsi };
}

/**
 * 3. 力の方程式
 * 外部力と重力・慣性項から、機体軸速度の微分（加速度）を計算します。
 * * @param {Object} dForces - 摂動空気力 { dFx, dFy, dFz }
 * @param {Object} state - 現在の状態 { p, q, r, theta, phi } (摂動量)
 * @param {Object} trim - 平衡状態 { U0, Theta0 }
 * @param {Object} consts - 物理定数 { m, g }
 * @returns {Object} 線形加速度 { dotU, dotV, dotW }
 */
function calculateLinearAccelerations(dForces, state, trim, consts) {
    const { dFx, dFy, dFz } = dForces;
    const { q, r, theta, phi } = state; // theta, phiは摂動角
    const { U0, Theta0 } = trim;
    const { m, g } = consts;

    const sinTheta0 = Math.sin(Theta0);
    const cosTheta0 = Math.cos(Theta0);

    // X軸 (Axial): 推力・抗力変化 - 重力分力
    const dotU = (dFx / m) - (g * theta * cosTheta0);

    // Y軸 (Lateral): 横力 + 重力分力 - コリオリ項(遠心力近似)
    // ※V0=0, W0=0を仮定
    const dotV = (dFy / m) + (g * phi * cosTheta0) - (U0 * r);

    // Z軸 (Normal): 揚力変化 - 重力分力 + コリオリ項
    const dotW = (dFz / m) - (g * theta * sinTheta0) + (U0 * q);

    return { dotU, dotV, dotW };
}

/**
 * 4. 航法方程式
 * 機体軸速度から慣性座標系（地面固定）での速度を計算します。
 * * @param {Object} vel - 摂動速度 { u, v, w }
 * @param {Object} angles - 摂動角 { theta, psi } (注: phiは一次近似で位置に影響小)
 * @param {Object} trim - 平衡状態 { U0, Theta0 }
 * @returns {Object} 慣性座標系速度 { dotXg, dotYg, dotZg }
 */
function calculateInertialVelocity(vel, angles, trim) {
    const { u, v, w } = vel;
    const { theta, psi } = angles;
    const { U0, Theta0 } = trim;

    const sinT0 = Math.sin(Theta0);
    const cosT0 = Math.cos(Theta0);

    // 地面固定座標系 X軸速度 (North/Forward)
    // uとwを平衡ピッチ角で投影し、ピッチ角摂動(theta)によるU0の成分変化を引く
    const dotXg = (u * cosT0) + (w * sinT0) - (U0 * theta * sinT0);

    // 地面固定座標系 Y軸速度 (East/Right)
    // 横滑り速度v + ヨー角(psi)による前方速度U0の成分
    const dotYg = v + (U0 * psi);

    // 地面固定座標系 Z軸速度 (Down)
    // uとwの投影 + ピッチ角摂動による影響
    const dotZg = -(u * sinT0) + (w * cosT0) - (U0 * theta * cosT0);

    return { dotXg, dotYg, dotZg };
}

/**
 * タイムステップ計算 (オイラー積分)
 * * @param {Object} currentState - 現在の状態 { u,v,w, p,q,r, phi,theta,psi, x,y,z }
 * @param {Object} inputs - 制御・外乱入力 { dFx, dFy, dFz, dL, dM, dN }
 * @param {number} dt - 時間刻み [s]
 */
function updateState(currentState, inputs, dt) {
    // 1. モーメント方程式 -> 角加速度
    const angAcc = calculateAngularAccelerations(inputs, CONSTANTS);

    // 2. キネマティクス -> オイラー角速度
    const eulerRates = calculateEulerRates(currentState, TRIM); // p,q,rを使用

    // 3. 力の方程式 -> 線形加速度
    const linAcc = calculateLinearAccelerations(inputs, currentState, TRIM, CONSTANTS);

    // 4. 航法方程式 -> 位置速度
    const posVel = calculateInertialVelocity(currentState, currentState, TRIM); // u,v,w, theta,psiを使用

    // 積分 (Euler Integration)
    // ※実際のシミュレーションではRunge-Kutta法などが望ましい
    return {
        // 速度更新
        u: currentState.u + linAcc.dotU * dt,
        v: currentState.v + linAcc.dotV * dt,
        w: currentState.w + linAcc.dotW * dt,

        // 角速度更新
        p: currentState.p + angAcc.dotP * dt,
        q: currentState.q + angAcc.dotQ * dt,
        r: currentState.r + angAcc.dotR * dt,

        // 姿勢角更新
        phi: currentState.phi + eulerRates.dotPhi * dt,
        theta: currentState.theta + eulerRates.dotTheta * dt,
        psi: currentState.psi + eulerRates.dotPsi * dt,

        // 位置更新
        x: currentState.x + posVel.dotXg * dt,
        y: currentState.y + posVel.dotYg * dt,
        z: currentState.z + posVel.dotZg * dt
    };
}

/**
 * 状態微分（変化率）を計算する関数
 * 先ほどの updateState の中身から「積分計算」を除き、
 * 「変化率(dot...)」だけを返すようにしたものと仮定します。
 */
function getDerivatives(state, inputs, trim, consts) {
    const angAcc = calculateAngularAccelerations(inputs, consts);
    const eulerRates = calculateEulerRates(state); // state内のp,q,rを使用
    const linAcc = calculateLinearAccelerations(inputs, state, trim, consts);
    const posVel = calculateInertialVelocity(state, state, trim);

    return {
        // 変化率のみを返す
        u: linAcc.dotU,
        v: linAcc.dotV,
        w: linAcc.dotW,
        p: angAcc.dotP,
        q: angAcc.dotQ,
        r: angAcc.dotR,
        phi: eulerRates.dotPhi,
        theta: eulerRates.dotTheta,
        psi: eulerRates.dotPsi,
        x: posVel.dotXg,
        y: posVel.dotYg,
        z: posVel.dotZg
    };
}

/**
 * 4次のルンゲ・クッタ法による積分ステップ
 */
function rungeKuttaStep(currentState, inputs, dt) {
    // ヘルパー: 状態に変化量を足した一時的な状態を作る
    const addState = (s, k, factor) => {
        const newState = {};
        for (let key in s) {
            newState[key] = s[key] + k[key] * factor;
        }
        return newState;
    };

    // k1: 現在の状態での傾き
    const k1 = getDerivatives(currentState, inputs, TRIM, CONSTANTS);

    // k2: dtの半分進んだ地点(k1の傾きで予測)での傾き
    const state2 = addState(currentState, k1, dt * 0.5);
    const k2 = getDerivatives(state2, inputs, TRIM, CONSTANTS);

    // k3: dtの半分進んだ地点(k2の傾きで予測)での傾き
    const state3 = addState(currentState, k2, dt * 0.5);
    const k3 = getDerivatives(state3, inputs, TRIM, CONSTANTS);

    // k4: dt進んだ地点(k3の傾きで予測)での傾き
    const state4 = addState(currentState, k3, dt);
    const k4 = getDerivatives(state4, inputs, TRIM, CONSTANTS);

    // 重み付け平均をして次の状態を決定
    const nextState = {};
    for (let key in currentState) {
        // シンプソンの公式に基づく重み付け (1 : 2 : 2 : 1)
        const delta = (k1[key] + 2 * k2[key] + 2 * k3[key] + k4[key]) / 6.0;
        nextState[key] = currentState[key] + delta * dt;
    }

    return nextState;
}

/**
 * 空気力およびモーメントの計算
 *
 * @param {Object} state - 摂動状態量 { u, v, w, p, q, r }
 * @param {Object} controls - 操舵角 { de, da, dr } (ラジアン: エレベータ, エルロン, ラダー)
 * @param {Object} trim - 平衡状態 { U0 }
 * @returns {Object} { dFx, dFy, dFz, dL, dM, dN }
 */
function calculateAerodynamics(state, controls, trim) {
    const { u, v, w, p, q, r } = state;
    const { de, da, dr } = controls;
    const { U0 } = trim;

    // 1. 補助変数の計算
    // 動圧 Q = 0.5 * rho * U0^2
    const Q = 0.5 * AERO.rho * (U0 * U0);

    // 微小擾乱近似による迎え角(alpha)と横滑り角(beta)
    const alpha = w / U0;
    const beta  = v / U0;

    // 無次元化された角速度 (Normalized rates)
    // 縦系は翼弦長 c、横系は翼幅 b で無次元化するのが通例
    const q_hat = (q * AERO.c) / (2 * U0);
    const p_hat = (p * AERO.b) / (2 * U0);
    const r_hat = (r * AERO.b) / (2 * U0);

    // 速度の無次元化摂動 (u / U0)
    const u_hat = u / U0;

    // 2. 縦系 (Longitudinal) の計算 [X, Z, M]
    // 力 F = Q * S * C
    // モーメント M = Q * S * c * C

    // X軸力 (Drag方向だが機体軸)
    const CX = (AERO.CX_u * u_hat) +
               (AERO.CX_alpha * alpha) +
               (AERO.CX_de * de);
    const dFx = Q * AERO.S * CX;

    // Z軸力 (Lift方向の逆)
    const CZ = (AERO.CZ_u * u_hat) +
               (AERO.CZ_alpha * alpha) +
               (AERO.CZ_q * q_hat) +
               (AERO.CZ_de * de);
    const dFz = Q * AERO.S * CZ;

    // ピッチングモーメント
    const Cm = (AERO.Cm_u * u_hat) +
               (AERO.Cm_alpha * alpha) +
               (AERO.Cm_q * q_hat) +
               (AERO.Cm_de * de);
    const dM = Q * AERO.S * AERO.c * Cm;

    // 3. 横・方向系 (Lateral-Directional) の計算 [Y, L, N]
    // モーメント (Roll, Yaw) は翼幅 b をかける

    // Y軸力 (横力)
    const CY = (AERO.CY_beta * beta) +
               (AERO.CY_p * p_hat) +
               (AERO.CY_r * r_hat) +
               (AERO.CY_dr * dr); // エルロンの影響は通常無視または微小
    const dFy = Q * AERO.S * CY;

    // ローリングモーメント
    const Cl = (AERO.Cl_beta * beta) +
               (AERO.Cl_p * p_hat) +
               (AERO.Cl_r * r_hat) +
               (AERO.Cl_da * da) +
               (AERO.Cl_dr * dr);
    const dL = Q * AERO.S * AERO.b * Cl;

    // ヨーイングモーメント
    const Cn = (AERO.Cn_beta * beta) +
               (AERO.Cn_p * p_hat) +
               (AERO.Cn_r * r_hat) +
               (AERO.Cn_da * da) +
               (AERO.Cn_dr * dr);
    const dN = Q * AERO.S * AERO.b * Cn;

    return { dFx, dFy, dFz, dL, dM, dN };
}

// --- Constants ---
const RHO = 1.225; // 空気密度 kg/m^3
const G = 9.80665;
const PI = Math.PI;
const D2R = PI / 180;
const R2D = 180 / PI;

// --- Helper Functions ---
const getVal = (id) => parseFloat(document.getElementById(id).value);
const setTxt = (id, txt) => document.getElementById(id).innerText = txt;

// 翼の幾何学特性を計算
function calculateWingProperties(span_mm, root_mm, tip_mm, sweep_deg=0) {
    const b = span_mm / 1000; // m
    const cr = root_mm / 1000; // m
    const ct = tip_mm / 1000; // m

    const S = (cr + ct) * b / 2;
    const AR = (b**2) / S;
    const taper = ct / cr;

    // Mean Aerodynamic Chord (MAC)
    const mac = (2/3) * cr * ((1 + taper + taper**2) / (1 + taper));

    // MACの前縁位置 (Y位置から後退角を考慮してXを算出)
    // 重心計算のために、MACの空力中心ではなく、MAC自体の幾何学的なY位置を求める
    const y_mac = (b/6) * ((1 + 2*taper) / (1 + taper));
    const x_mac_le = Math.tan(sweep_deg * D2R) * y_mac;

    return { S, AR, mac, b, cr, ct, x_mac_le, y_mac };
}

// --- 慣性モーメント計算用ヘルパー関数 (ストリップ法) ---
    // component: 翼オブジェクト(S, b, cr, ctなど)
    // totalMass: そのパーツの全質量
    // offset_x: 全機基準点(鼻先や主翼前縁)から、そのパーツのルート前縁までの距離
    // offset_z: 胴体中心線からの高さ（通常は0、垂直尾翼などで使用）
    // global_cg_x: 全機の重心位置
    // sweep: 後退角(度)
    // dihedral: 上反角(度)
    // isVertical: 垂直尾翼フラグ(trueなら翼幅をZ方向に展開)
    function calculateComponentInertia(component, totalMass, offset_x, offset_z, global_cg_x, sweep, dihedral, isVertical = false) {

        let Ix = 0, Iy = 0, Iz = 0, Ixz = 0;

        // 積分精度（片側を何分割するか）
        const STRIPS = 20;
        const dy = (component.b / 2) / STRIPS; // ストリップの幅 (m)
        const tan_sweep = Math.tan(sweep * D2R);
        const sin_dihedral = Math.sin(dihedral * D2R);
        const cos_dihedral = Math.cos(dihedral * D2R);

        // 単位面積あたりの質量 (kg/m^2)
        const density = totalMass / component.S;

        // ループ（左翼と右翼を同時に計算、または垂直尾翼）
        // iはルートからチップへのインデックス
        for (let i = 0; i < STRIPS; i++) {
            // 現在のストリップのY位置（翼根からの距離）
            const y_local_plane = (i + 0.5) * dy; // ストリップの中心

            // テーパー比を考慮した現在のコード長
            // chord(y) = cr + (ct - cr) * (y / (b/2))
            const chord = component.cr + (component.ct - component.cr) * (y_local_plane / (component.b / 2));

            // ストリップの面積と質量
            const area = chord * dy;
            const dm = area * density; // 微小質量

            // ストリップの幾何学的中心位置 (ローカル座標)
            // X: ルート前縁からの距離 (後退角 + コードの40%位置と仮定)
            const x_local = (y_local_plane * tan_sweep) + (chord * 0.4);

            // 3次元座標への変換 (全機重心に対する相対位置)
            let dx, dy_pos, dz;

            if (isVertical) {
                // 垂直尾翼の場合 (Y方向の広がりをZとみなす)
                // 上反角は無視、あるいは90度とみなす
                dx = (offset_x + x_local) - global_cg_x;
                dy_pos = 0; // 左右対称面上にあると仮定
                dz = (offset_z + y_local_plane); // ルート高さ + スパン方向高さ

                // 垂直尾翼は1枚として加算
                Ix += dm * (dy_pos**2 + dz**2);
                Iy += dm * (dx**2 + dz**2);
                Iz += dm * (dx**2 + dy_pos**2);
                Ixz += dm * (dx * dz);

            } else {
                // 水平翼（主翼・水平尾翼）の場合
                // 上反角を考慮してY, Zを分解
                const y_proj = y_local_plane * cos_dihedral;
                const z_proj = y_local_plane * sin_dihedral;

                dx = (offset_x + x_local) - global_cg_x;

                // 右翼 (+Y)
                let dy_r = y_proj;
                let dz_r = offset_z + z_proj;

                Ix += dm * (dy_r**2 + dz_r**2);
                Iy += dm * (dx**2 + dz_r**2);
                Iz += dm * (dx**2 + dy_r**2);
                Ixz += dm * (dx * dz_r);

                // 左翼 (-Y) - 対称形状
                let dy_l = -y_proj;
                let dz_l = offset_z + z_proj; // 上反角は両翼とも上に上がるためZは同じ

                Ix += dm * (dy_l**2 + dz_l**2);
                Iy += dm * (dx**2 + dz_l**2);
                Iz += dm * (dx**2 + dy_l**2);
                Ixz += dm * (dx * dz_l);
            }
        }

        return { Ix, Iy, Iz, Ixz };
    }

// 3Dメッシュ生成 (Plotly mesh3d用)
function createWingMesh(geo, offsetX, angle_deg, dihedral_deg, color, isVertical=false) {
    // 座標系: X(後), Y(右), Z(上)
    // 原点: 指定されたoffsetX (各翼のルート前縁)

    const angle = angle_deg * D2R;
    const dihedral = dihedral_deg * D2R;

    // 頂点定義 (ローカル)
    // Right Wing
    // 0: Root LE, 1: Root TE, 2: Tip TE, 3: Tip LE
    const x_local = [0, geo.cr, geo.ct, 0]; // 後退角0仮定
    const y_local = [0, 0, geo.b/2, geo.b/2];
    const z_local = [0, 0, 0, 0];

    // 回転変換関数
    const transform = (x, y, z, mirror=false) => {
        let y_mod = mirror ? -y : y;
        let dihedral_mod = mirror ? -dihedral : dihedral;

        // 1. Pitch (Y軸回転) -> X, Z変化
        // X' = X cos(th) + Z sin(th)
        // Z' = -X sin(th) + Z cos(th)
        let x1 = x * Math.cos(angle) + z * Math.sin(angle);
        let z1 = -x * Math.sin(angle) + z * Math.cos(angle);

        // 2. Dihedral (X軸回転) -> Y, Z変化
        // Y'' = Y cos(phi) - Z' sin(phi)
        // Z'' = Y sin(phi) + Z' cos(phi)
        let y2 = y_mod * Math.cos(dihedral_mod) - z1 * Math.sin(dihedral_mod);
        let z2 = y_mod * Math.sin(dihedral_mod) + z1 * Math.cos(dihedral_mod);

        return { x: x1 + offsetX, y: y2, z: z2 };
    };

    let X=[], Y=[], Z=[];

    if(isVertical) {
        // 垂直尾翼 (90度回転して配置)
        // Root: (0,0,0)-(cr,0,0), Tip: (0,0,b)-(ct,0,b)
        // 簡易的にZ方向に伸ばす
        const v_pts = [
            {x:0, y:0, z:0}, {x:geo.cr, y:0, z:0},
            {x:geo.ct, y:0, z:geo.b}, {x:0, y:0, z:geo.b}
        ];
        v_pts.forEach(p => {
            X.push(p.x + offsetX); Y.push(p.y); Z.push(p.z);
        });
    } else {
        // 右翼
        for(let i=0; i<4; i++) {
            const p = transform(x_local[i], y_local[i], z_local[i], false);
            X.push(p.x); Y.push(p.y); Z.push(p.z);
        }
        // 左翼
        for(let i=0; i<4; i++) {
            const p = transform(x_local[i], y_local[i], z_local[i], true);
            X.push(p.x); Y.push(p.y); Z.push(p.z);
        }
    }

    // Mesh Index
    // Right: 0-1-2, 0-2-3. Left: 4-5-6, 4-6-7
    let I, J, K;
    if(isVertical) {
        I=[0,0]; J=[1,2]; K=[2,3];
    } else {
        I=[0,0,4,4]; J=[1,2,5,6]; K=[2,3,6,7];
    }

    return {
        type: 'mesh3d',
        x: X, y: Y, z: Z,
        i: I, j: J, k: K,
        color: color, opacity: 0.8, flatshading: true,
        hoverinfo: 'none'
    };
}

/**
 * 安定微係数算出関数
 * * 古典的な飛行力学の近似式（Roskam, Perkins & Hage等に基づく）を使用して
 * 機体形状から安定微係数を推定します。
 */
function calculateStabilityDerivatives(mw, ht, vt, m_total, trimData, aeroParams, controlParams) {
    // ------------------------------------------------
    // 1. 幾何学的パラメータの整理
    // ------------------------------------------------

    // 重心位置 (CG)
    const x_cg = aeroParams.x_cg;

    // 翼の空力中心位置 (Aerodynamic Centers)
    // 通常、亜音速では前縁からMACの25%位置
    const x_ac_w = mw.x_mac_le + (0.25 * mw.mac);
    const x_ac_t = ht.x_mac_le + (0.25 * ht.mac) + aeroParams.x_ac_t_offset; // オフセット調整
    const x_ac_v = vt.x_mac_le + (0.25 * vt.mac) + aeroParams.x_ac_v_offset;

    // 尾翼アーム長 (Lever Arms)
    const lt = x_ac_t - x_cg; // 水平尾翼アーム
    const lv = x_ac_v - x_cg; // 垂直尾翼アーム

    // 尾翼容積比 (Tail Volumes) - 安定性の指標
    // Vh: 縦安定性 (大きいほどピッチが安定)
    const Vh = (ht.S * lt) / (mw.S * mw.mac);
    // Vv: 方向安定性 (大きいほどヨーが安定)
    const Vv = (vt.S * lv) / (mw.S * mw.b);

    // 動圧比 (eta): 尾翼位置での動圧低下（主翼の後流など）
    const eta_t = aeroParams.eta_t || 0.9;
    const eta_v = aeroParams.eta_v || 0.95;

    // ------------------------------------------------
    // 2. 揚力傾斜の補正 (3D Lift Curve Slope)
    // ------------------------------------------------
    // 2次元(断面)データから3次元(有限翼)データへの変換
    // Formula: CLa = CLa_2d / (1 + CLa_2d / (pi * e * AR))
    // ※ここでは簡易的に入力された cla_w を3次元値として扱いますが、
    //   もし2次元値(約2pi)なら変換が必要です。
    const CLa_w = aeroParams.cla_w;
    const CLa_t = aeroParams.cla_t;
    const CLa_v = aeroParams.cla_v || 4.0; // 垂直尾翼用(なければデフォルト)

    // 洗い流し (Downwash) de/da
    // 主翼が尾翼に与える吹き下ろしの影響
    const de_da = aeroParams.de_da || (2 * CLa_w) / (Math.PI * mw.AR);

    // ------------------------------------------------
    // 3. 縦系微係数 (Longitudinal)
    // ------------------------------------------------

    // ■ CL_alpha (全機揚力傾斜)
    // 主翼 + 尾翼の効果
    const CL_alpha = CLa_w + CLa_t * (ht.S / mw.S) * eta_t * (1 - de_da);

    // ■ Cm_alpha (静安定) ※重要
    // 負である必要があります。
    // 項1: 主翼のモーメント (空力中心とCGのズレ)
    // 項2: 尾翼の安定効果 (尾翼容積比 Vh に比例)
    const hn = x_ac_w / mw.mac; // 主翼空力中心の無次元位置
    const h = x_cg / mw.mac;    // CGの無次元位置
    const term_wing = CLa_w * (h - hn);
    const term_tail = -CLa_t * eta_t * Vh * (1 - de_da);
    const Cm_alpha = term_wing + term_tail;

    // ■ Cm_q (ピッチダンピング)
    // 回転を止める力。主に水平尾翼が寄与。
    // 式: -2 * CLa_t * eta_t * Vh * (lt / mac)
    const Cm_q = -2 * CLa_t * eta_t * Vh * (lt / mw.mac);

    // ■ 舵効き (Control Derivatives)
    // 面積比から効率係数(tau)を簡易推定 (線形近似)
    // tau_e approx sqrt(S_elev / S_tail) 程度だがここでは係数で調整
    const tau_e = Math.sqrt(controlParams.ratio_elev || 0.3) * 0.8; // 0.8は経験的な効率

    // Cm_de (エレベータ効き)
    const Cm_de = -CLa_t * eta_t * Vh * tau_e;

    // CZ_de (エレベータによる揚力変化)
    const CZ_de = -CLa_t * (ht.S / mw.S) * eta_t * tau_e; // Zは下向き正なので揚力増はマイナス

    // ------------------------------------------------
    // 4. 横・方向系微係数 (Lateral-Directional)
    // ------------------------------------------------

    // ■ Cn_beta (方向静安定 - 風見鶏効果)
    // 正である必要があります。垂直尾翼容積比 Vv に比例。
    // 胴体は不安定要素(-)、垂直尾翼は安定要素(+)
    // 簡易式: Cn_beta = CLa_v * Vv * eta_v
    const Cn_beta = CLa_v * Vv * eta_v * 0.95; // 0.95は胴体による干渉係数(概算)

    // ■ Cl_beta (上反角効果)
    // 負である必要があります（左から風を受けたら右にロールする=風上にバンクする）。
    // 上反角(Gamma)と後退角(Lambda)の影響
    const Gamma = (aeroParams.mw_dihedral || 0) * D2R;
    const Lambda = (aeroParams.mw_sweep || 0) * D2R; // 1/4弦後退角と仮定

    // 近似式: 上反角効果 + 後退角効果(揚力係数に依存)
    // Cl_beta ≈ -CLa_w * Gamma * (修正係数) - CL * sin(2*Lambda) / ...
    // ここでは簡易的に上反角成分を主とする
    const Cl_beta = -1.0 * (CLa_w * Gamma / mw.b) * mw.mac * 10; // ※係数は経験的なスケーリングが必要
    // ※本来はもっと複雑ですが、上反角1度あたり -0.002 程度になるよう調整します

    // ■ Cn_r (ヨーダンピング)
    // ヨー回転を止める力。垂直尾翼アームの2乗に効く。
    const Cn_r = -2 * CLa_v * eta_v * Vv * (lv / mw.b);

    // ■ Cl_p (ロールダンピング)
    // ロール回転を止める力。主翼のアスペクト比に依存。
    // 近似式: - (CLa_w / 8) (通常 -0.4 ~ -0.6 程度)
    const Cl_p = -(CLa_w + CLa_t) / 8; // 簡易式

    // ■ 舵効き (Rudder / Aileron)
    const tau_r = Math.sqrt(controlParams.ratio_rudder || 0.3) * 0.7;
    const Cn_dr = -CLa_v * Vv * eta_v * tau_r; // ラダーで首を振る力
    const Cy_dr = CLa_v * (vt.S / mw.S) * eta_v * tau_r; // ラダーによる横力

    const tau_a = Math.sqrt(controlParams.ratio_aileron || 0.1) * 0.6;
    // エルロン効き (Cl_da) は翼幅方向の積分が必要だが、簡易的に
    const Cl_da = -0.15 * tau_a * mw.AR; // ARが大きいと効きが良い傾向

    // ------------------------------------------------
    // 5. 結果の格納 (AEROオブジェクト形式)
    // ------------------------------------------------
    return {
        // 諸元
        S: mw.S,
        b: mw.b,
        c: mw.mac,
        rho: 1.225,

        // 縦系
        CX_u: -0.05,       // 抗力の速度依存性 (簡易値)
        CX_alpha: trimData.CD_total || 0.1, // 抗力係数
        CX_de: 0.0,

        CZ_u: -trimData.CL_total * 2, // 速度が増えると揚力(下向き力負)が増える
        CZ_alpha: -CL_alpha,          // Z軸は下向きなので、揚力傾斜はマイナス
        CZ_dot_alpha: -CL_alpha / 2,  // 洗い流し遅れ (簡易値)
        CZ_q: -2.0 * CL_alpha,        // ピッチレートによる揚力
        CZ_de: CZ_de,

        Cm_u: 0.0,                    // マッハ数が低いので無視
        Cm_alpha: Cm_alpha,           // ★計算結果
        Cm_dot_alpha: Cm_q / 2,       // 洗い流し遅れ (Cm_qに関連)
        Cm_q: Cm_q,                   // ★計算結果
        Cm_de: Cm_de,                 // ★計算結果

        // 横・方向系
        CY_beta: -0.5, // 横力傾斜 (垂直尾翼+胴体)
        CY_p: 0.0,
        CY_r: Cy_dr * 2, // ヨーレートによる横力
        CY_da: 0.0,
        CY_dr: Cy_dr,

        Cl_beta: Cl_beta,             // ★計算結果 (上反角効果)
        Cl_p: Cl_p,                   // ★計算結果 (ロール減衰)
        Cl_r: CL_alpha / 4,           // 揚力による連成
        Cl_da: Cl_da,                 // ★計算結果
        Cl_dr: 0.01,                  // ラダーによるロール (通常小さい)

        Cn_beta: Cn_beta,             // ★計算結果 (方向安定)
        Cn_p: -0.05,                  // ロールによるヨー (アドバース等)
        Cn_r: Cn_r,                   // ★計算結果 (ヨー減衰)
        Cn_da: -Cl_da / 10,           // アドバースヨー (簡易値)
        Cn_dr: Cn_dr                  // ★計算結果
    };
}

// === メイン計算処理 ===
function runCalculation() {
    // 1. 入力取得
    const mw_span = getVal('mw_span');
    const mw_root = getVal('mw_root');
    const mw_tip = getVal('mw_tip');
    const mw_angle = getVal('mw_angle');
    const mw_dihedral = getVal('mw_dihedral');
    const mw_sweep = getVal('mw_sweep')

    const ht_span = getVal('ht_span');
    const ht_root = getVal('ht_root');
    const ht_tip = getVal('ht_tip');
    const ht_angle = getVal('ht_angle');
    const ht_dist = getVal('ht_dist'); // mm

    const vt_span = getVal('vt_span');
    const vt_root = getVal('vt_root');
    const vt_tip = getVal('vt_tip');
    const vt_dist = getVal('vt_dist'); // mm

    const mat_gsm = getVal('material'); // g/m2
    const ballast_mass_kg = getVal('ballast_mass') / 1000;
    const ballast_pos_m = getVal('ballast_pos') / 1000;
    const sim_v0 = getVal('sim_v0');
    const sim_pitch = getVal('sim_pitch');

    // 2. 幾何学計算
    const mw = calculateWingProperties(mw_span, mw_root, mw_tip, mw_sweep);
    const ht = calculateWingProperties(ht_span, ht_root, ht_tip);
    const vt = calculateWingProperties(vt_span, vt_root, vt_tip);

    // 3. 重量・重心・慣性モーメント
    const mat_kgm2 = mat_gsm / 1000; // kg/m^2
    const m_w = mw.S * mat_kgm2;
    const m_h = ht.S * mat_kgm2;
    const m_v = vt.S * mat_kgm2;
    const m_total = m_w + m_h + m_v + ballast_mass_kg;

    // 各パーツの重心位置（簡易的にMACの40%位置とする）
    const x_cg_w = mw.x_mac_le + mw.mac * 0.4;
    const x_cg_h = (ht_dist/1000) + ht.x_mac_le + ht.mac * 0.4;
    const x_cg_v = (vt_dist/1000) + vt.x_mac_le + vt.mac * 0.4;
    const x_cg_b = ballast_pos_m;

    // 全機重心
    const x_cg = (m_w*x_cg_w + m_h*x_cg_h + m_v*x_cg_v + ballast_mass_kg*x_cg_b) / m_total;

    // 各コンポーネントの慣性モーメントを計算

    // 主翼 (基準位置 0,0)
    const i_mw = calculateComponentInertia(mw, m_w, 0, 0, x_cg, mw_sweep, mw_dihedral, false);

    // 水平尾翼 (基準位置 ht_dist)
    const i_ht = calculateComponentInertia(ht, m_h, ht_dist/1000, 0, x_cg, 0, 0, false);

    // 垂直尾翼 (基準位置 vt_dist, 垂直フラグ true)
    // ※垂直尾翼のオフセット高さが必要ですが、ここでは0(胴体中心線)から生えていると仮定します
    const i_vt = calculateComponentInertia(vt, m_v, vt_dist/1000, 0, x_cg, 0, 0, true);

    // バラスト (質点として計算)
    // 質点の慣性モーメント I = m * r^2
    const dx_b = ballast_pos_m - x_cg;
    const dy_b = 0; // 中心線上と仮定
    const dz_b = 0; // 中心線上と仮定

    const i_ballast = {
        Ix: ballast_mass_kg * (dy_b**2 + dz_b**2),
        Iy: ballast_mass_kg * (dx_b**2 + dz_b**2),
        Iz: ballast_mass_kg * (dx_b**2 + dy_b**2),
        Ixz: ballast_mass_kg * (dx_b * dz_b)
    };

    // 結果の格納
    // 慣性モーメントデータ
    const inertias = {
        Ix:i_mw.Ix + i_ht.Ix + i_vt.Ix + i_ballast.Ix,
        Iy:i_mw.Iy + i_ht.Iy + i_vt.Iy + i_ballast.Iy,
        Iz: i_mw.Iz + i_ht.Iz + i_vt.Iz + i_ballast.Iz,
        Ixy:0,
        Ixz:i_mw.Ixz + i_ht.Ixz + i_vt.Ixz + i_ballast.Ixz,
    }

    // 4. 空力係数・安定計算
    // 空力中心 (AC) = MACの25%
    const x_ac_w = mw.x_mac_le + mw.mac * 0.25;
    const x_ac_t = (ht_dist/1000) + ht.x_mac_le + ht.mac * 0.25;

    // 揚力傾斜 CLa (per rad)
    // Finite wing theory: a = a0 / (1 + a0 / (pi * AR * e)) approximation
    const wing_efficiency = 0.9; // e
    const cla_w = (2 * PI) / (1 + 2 / (mw.AR * wing_efficiency));
    const cla_t = (2 * PI) / (1 + 2 / (ht.AR * wing_efficiency));

    // 吹き下ろし勾配 (de/da)
    // de/da = 2 * CL_alpha / (pi * AR) approx
    const de_da = (2 * cla_w) / (PI * mw.AR);

    // 尾翼効率 (動圧比)
    const eta_t = 0.9; // 胴体後流などで少し落ちる

    // 中立点 (Neutral Point) Xnp
    // Vh = (Sh * lt) / (Sw * mac)
    const lt = x_ac_t - x_ac_w; // AC間距離
    const Vh = (ht.S * lt) / (mw.S * mw.mac);

    // Xnp = Xac + Vh * (at/aw) * (1 - de/da) * eta_t
    // ここでのXnpは正規化前(距離)
    const np_margin_len = Vh * (cla_t / cla_w) * (1 - de_da) * eta_t * mw.mac;
    const x_np = x_ac_w + np_margin_len;

    // スタティックマージン
    const static_margin = (x_np - x_cg) / mw.mac;

    // 5. トリム解析
    Cm_cg = Cmw + Cmt
    Cmw = CLw * (xcg - xacw)/mac  (注: xcg > xacwで不安定モーメント正)
    Cmt = -CLt * eta * (xact - xcg)/mac * (St/Sw)

    const dist_w = (x_cg - x_ac_w) / mw.mac;
    const dist_t = (x_ac_t - x_cg) / mw.mac;
    const S_ratio = ht.S / mw.S;

    // alpha: 胴体基準迎角
    const getCm = (alpha) => {
        const aw = alpha + mw_angle * D2R;
        const CLw = cla_w * aw;

        // Tail angle: at = (alpha + it) - epsilon
        const eps = de_da * aw;
        const at = (alpha + ht_angle * D2R) - eps;
        const CLt = cla_t * at;

        const Cmw = CLw * dist_w;
        const Cmt = -CLt * eta_t * dist_t * S_ratio;
        return Cmw + Cmt;
    };

    // ニュートン法でTrim Alpha探索
    let a_trim = 0;
    for(let i=0; i<20; i++) {
        const val = getCm(a_trim);
        const grad = (getCm(a_trim + 0.0001) - val) / 0.0001;
        if(Math.abs(grad) < 1e-6) break;
        const next = a_trim - val / grad;
        if(Math.abs(next - a_trim) < 1e-6) { a_trim = next; break; }
        a_trim = next;
    }

    // Trim時の性能
    const aw_trim = a_trim + mw_angle * D2R;
    const eps_trim = de_da * aw_trim;
    const at_trim = (a_trim + ht_angle * D2R) - eps_trim;
    const CLw_trim = cla_w * aw_trim;
    const CLt_trim = cla_t * at_trim;
    const CL_total = CLw_trim + CLt_trim * (ht.S/mw.S);

    // Trim速度
    // W = 1/2 rho V^2 S CL
    let v_trim = 0;
    if(CL_total > 0) {
        v_trim = Math.sqrt((2 * m_total * G) / (RHO * mw.S * CL_total));
    }

    // 抗力係数
    const CD0 = 0.02 + (vt.S/mw.S)*0.005; // 寄生抗力概算
    const k_w = 1 / (PI * mw.AR * 0.85);
    const CD = CD0 + k_w * (CL_total**2);
    const LD = CL_total / CD;

    // トリムデータのまとめ
    const trimData = {
        alpha: a_trim,
        v_trim: v_trim,
        CL_total: CL_total,
        CD_total: CD, // 計算済みのCD
    };

    // 空力計算用パラメータのまとめ
    const aeroParams = {
        cla_w,
        cla_t,
        de_da,
        eta_t,
        x_cg,
        x_ac_w,
        x_ac_t // ※ここ重要: 変数名 x_ac_t が計算済みであること
    };

    // -----------------
    // シミュレーション開始
    // -----------------

    // 簡易ヘルパー関数
    function setTxt(id, val) {
        const el = document.getElementById(id);
        if(el) el.innerText = val;
    }

    // 1. 空力微係数モデルの取得 (実装済みの関数を使用)
    const AERO = calculateStabilityDerivatives(mw, ht, vt, m_total, trimData, aeroParams, controlParams);

    // 2. シミュレーション初期設定
    const dt = 0.01;        // 時間刻み [s]
    const T_max = 10.0;     // シミュレーション時間 [s]
    const steps = Math.floor(T_max / dt);

    // 物理定数
    const CONSTS = {
        m: m_total,
        g: 9.80665,
        Ix: inertias.Ix, Iy: inertias.Iy, Iz: inertias.Iz, Ixz: inertias.Ixz
    };

    // 平衡状態 (Trim Condition)
    const TRIM = {
        U0: trimData.v_trim, // トリム速度
        Theta0: 0.0,         // 水平飛行を仮定 (厳密には迎角分だけ上向くが微小として0)
        W0: 0
    };

    // 状態変数の初期化 (すべて0 = 平衡状態からスタート)
    let state = {
        u: 0, v: 0, w: 0,
        p: 0, q: 0, r: 0,
        phi: 0, theta: 0, psi: 0,
        x: 0, y: 0, z: 0 // ここでのx,zは「平衡点からのずれ」
    };

    // 結果格納用配列
    const timeHist = [];
    const trajX = []; // グラフ用 X (距離)
    const trajZ = []; // グラフ用 Y (高度) ※PlotlyのY軸は高度
    const pitchHist = []; // ピッチ角履歴

    // --- 3. メインループ (Time Integration) ---
    for (let i = 0; i < steps; i++) {
        const t = i * dt;

        // 【テスト入力】
        // 1秒後にエレベータを少し(2度)動かして、すぐ戻す (Doublet/Impulse)
        // これにより「機体の揺れ」を励起し、安定性を確認する
        let de_input = 0;
        if (t > 1.0 && t < 1.2) {
            de_input = -2.0 * (Math.PI / 180); // 機首上げ入力
        } else if (t > 1.2 && t < 1.4) {
            de_input = 2.0 * (Math.PI / 180);  // 機首下げ入力
        }

        const inputs = {
            de: de_input, da: 0, dr: 0
        };

        // 1ステップ計算 (RK4)
        // ※rungeKuttaStep内部で calculateAerodynamics 等が呼ばれる前提
        // ※入力を微係数計算用フォーマットに変換 (calculateAerodynamics内で係数を掛けるため)
        // ここでは簡易的に、rungeKuttaStepに渡すinputsは「力とモーメント」ではなく「舵角」とし、
        // getDerivatives関数内で calculateAerodynamics を呼ぶ構造を想定しています。

        // もし以前の構造(inputs=力)のままであれば、ここで力を計算します：
        const forces = calculateAerodynamics(state, inputs, TRIM);
        // forces = { dFx, dFy, dFz, dL, dM, dN }

        // 状態更新
        state = rungeKuttaStep(state, forces, dt);

        // --- データの保存と座標変換 ---

        // 1. 全体位置の計算 (Total Position)
        // 線形化モデルの (x, z) は「トリム位置からのズレ」です。
        // 実際の軌跡を描くには、トリム速度で進む分を足します。
        const totalX = (TRIM.U0 * t) + state.x;

        // 高度 (Zは下向き正なので、高度としては -Z)
        // 初高度を仮に 50m とします
        const initialAlt = 50;
        const totalAlt = initialAlt - state.z; // zは摂動(沈下)

        timeHist.push(t);
        trajX.push(totalX);
        trajZ.push(totalAlt);
        pitchHist.push(state.theta * (180/Math.PI)); // 度数法で保存
    }

    // --- 4. 結果表示 (DOM更新) ---
    // 既存のコードを統合・修正

    const static_margin = (aeroParams.x_np - aeroParams.x_cg) / mw.mac; // NPは別途計算済みと仮定

    setTxt('res_weight', (m_total*1000).toFixed(0) + ' g');
    setTxt('res_sm', (static_margin*100).toFixed(1) + ' %');
    setTxt('res_vtrim', trimData.v_trim.toFixed(1) + ' m/s');
    // L/D はトリム計算データから取得
    const ld_ratio = trimData.CL_total / trimData.CD_total;
    setTxt('res_ld', ld_ratio.toFixed(1));

    // 詳細テーブル HTML
    const resHTML = `
        <tr><td class="px-2 py-1 border-b">翼面荷重</td><td class="px-2 py-1 border-b font-mono">${(m_total/mw.S).toFixed(2)} kg/m²</td></tr>
        <tr><td class="px-2 py-1 border-b">空力中心 (AC)</td><td class="px-2 py-1 border-b font-mono">${(aeroParams.x_ac_w*1000).toFixed(0)} mm</td></tr>
        <tr><td class="px-2 py-1 border-b">中立点 (NP)</td><td class="px-2 py-1 border-b font-mono">${(aeroParams.x_np*1000).toFixed(0)} mm</td></tr>
        <tr><td class="px-2 py-1 border-b">重心 (CG)</td><td class="px-2 py-1 border-b font-mono">${(aeroParams.x_cg*1000).toFixed(0)} mm</td></tr>
        <tr><td class="px-2 py-1 border-b">慣性モーメントIy</td><td class="px-2 py-1 border-b font-mono">${inertias.Iy.toFixed(3)} kg·m²</td></tr>
        <tr><td class="px-2 py-1 border-b">周期モード</td><td class="px-2 py-1 border-b font-mono text-xs text-gray-500">グラフ参照</td></tr>
    `;
    document.getElementById('resultBody').innerHTML = resHTML;

    // アラート表示 (静安定判別)
    const alertEl = document.getElementById('stabilityAlert');
    if(static_margin > 0.05 && static_margin < 0.25) {
        alertEl.className = "mb-5 p-3 rounded text-center font-bold text-sm bg-green-100 text-green-800";
        alertEl.innerText = "設計良好: 適切な静安定性があります (SM: " + (static_margin*100).toFixed(1) + "%)";
    } else if (static_margin > 0) {
        alertEl.className = "mb-5 p-3 rounded text-center font-bold text-sm bg-yellow-100 text-yellow-800";
        alertEl.innerText = "注意: 安定性がわずかです。過敏な可能性があります (SM: " + (static_margin*100).toFixed(1) + "%)";
    } else {
        alertEl.className = "mb-5 p-3 rounded text-center font-bold text-sm bg-red-100 text-red-800";
        alertEl.innerText = "設計不良: 静不安定です。重心を前にするか尾翼を大きくしてください";
    }

    // --- 5. グラフ描画 (Plotly) ---
    // 高度軌跡 (Trajectory)
    const traceTraj = {
        x: trajX, y: trajZ,
        mode: 'lines', line: {color: '#2563eb', width: 3},
        name: '飛行軌跡'
    };

    // 地面ライン
    const minX = Math.min(...trajX);
    const maxX = Math.max(...trajX);
    const ground = {
        x: [minX, maxX], y: [0, 0],
        mode: 'lines', line: {color: '#16a34a', width: 2, dash:'dot'},
        name: '地面'
    };

    const layout2D = {
        title: '垂直面内の軌跡 (外乱応答)',
        margin: {l:50, r:20, t:40, b:40},
        xaxis: {title: '水平距離 (m)', zeroline: false},
        yaxis: {title: '高度 (m)', zeroline: false, scaleanchor: 'x', scaleratio: 1}, // 縦横比固定
        showlegend: true,
        hovermode: 'closest',
        autosize: true
    };

    // 第2のグラフ: ピッチ角の時系列変化 (安定性の減衰を見るのに最適)
    const tracePitch = {
        x: timeHist, y: pitchHist,
        mode: 'lines', line: {color: '#ef4444', width: 2},
        name: 'ピッチ角'
    };

    const layoutPitch = {
        title: 'ピッチ角応答 (短周期モード)',
        margin: {l:50, r:20, t:40, b:40},
        xaxis: {title: '時間 (s)'},
        yaxis: {title: 'ピッチ角 (deg)'},
        height: 300 // 少し高さを抑える
    };

    // Plotly描画
    // HTML側に <div id="plotArea2D"></div> と <div id="plotAreaPitch"></div> がある想定
    Plotly.react('plotArea2D', [traceTraj, ground], layout2D, {responsive: true});

    // もしHTMLにピッチ角用のdivがあれば描画 (なければスキップ)
    const pitchDiv = document.getElementById('plotAreaPitch');
    if(pitchDiv) {
        Plotly.react('plotAreaPitch', [tracePitch], layoutPitch, {responsive: true});
    }


    // === 3D View ===
    const meshMW = createWingMesh(mw, 0, mw_angle, mw_dihedral, '#3b82f6');
    const meshHT = createWingMesh(ht, ht_dist/1000, ht_angle, 0, '#ef4444');
    const meshVT = createWingMesh(vt, vt_dist/1000, 0, 0, '#eab308', true);

    // 重心マーカー
    const cgTrace = {
        type: 'scatter3d', x: [x_cg], y: [0], z: [0],
        mode: 'markers', marker: {color:'black', size: 6, symbol:'diamond'},
        name: 'CG'
    };

    // レイアウト調整 (アスペクト比固定)
    // 範囲を機体サイズに合わせる
    const range = Math.max(mw.b, (ht_dist/1000 + ht.cr)) * 0.8;
    const centerX = ht_dist/2000;

    const layout3D = {
        margin: {l:0, r:0, t:0, b:0},
        scene: {
            xaxis: {range: [centerX-range, centerX+range], title: '', showgrid:false, zeroline:false, showticklabels:false},
            yaxis: {range: [-range, range], title: '', showgrid:false, zeroline:false, showticklabels:false},
            zaxis: {range: [-range, range], title: '', showgrid:false, zeroline:false, showticklabels:false},
            aspectmode: 'cube', // cube or data. dataだと軸範囲に依存するが、ここではrangeを手動制御してcubeにする方が安定する
            camera: {
                eye: {x: -1.2, y: 1.2, z: 1.2},
                center: {x: 0, y: 0, z: 0}
            },
            bgcolor: '#f9fafb'
        },
        showlegend: false
    };

    Plotly.react('plotArea3D', [meshMW, meshHT, meshVT, cgTrace], layout3D, {responsive: true});

}
// 初期実行
window.onload = runCalculation;

</script>
<!--
<!DOCTYPE html>
<html lang="ja">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>CalcPlane 3D Simulator</title>
<script src="https://cdn.plot.ly/plotly-2.24.1.min.js"></script>
<script src="https://cdn.tailwindcss.com"></script>
<style>
  :root { --color-primary: #2563eb; --color-brand: #3b82f6; }
  body { font-family: sans-serif; background-color: #f3f4f6; }
</style>
</head>
<body>

<div class="w-full max-w-7xl mx-auto px-4 py-8">
  <div class="grid grid-cols-1 lg:grid-cols-12 gap-6">

    <div class="lg:col-span-4 space-y-4">
      <div class="bg-white rounded-xl shadow border border-gray-200 overflow-hidden flex flex-col h-full ">
        <div class="bg-[var(--color-primary)] text-white px-5 py-3 border-b border-gray-100 flex-shrink-0">
          <h2 class="text-lg font-bold m-0 text-white">設計パラメータ</h2>
        </div>

        <div class="p-5 space-y-6 flex-1 overflow-y-auto max-h-[80vh]">

          <div>
            <label class="block text-xs font-bold text-gray-500 uppercase tracking-wider mb-2">機体素材 (面密度)</label>
            <select id="material" class="w-full p-2 text-sm border border-gray-300 rounded focus:ring-2 focus:ring-[var(--color-brand)] bg-gray-50">
                <option value="0.15">バルサ/軽量素材 (約150g/m²)</option>
                <option value="0.25">厚紙/スチレン (約250g/m²)</option>
                <option value="0.40">プラスチック/CFRP (約400g/m²)</option>
            </select>
          </div>

          <div class="border-l-4 border-blue-500 pl-3">
            <h3 class="text-sm font-bold text-gray-800 mb-2">主翼 (Main Wing)</h3>
            <div class="grid grid-cols-2 gap-x-2 gap-y-3 text-sm">
                <div><span class="text-xs text-gray-500 block">スパン (mm)</span><input type="number" id="mw_span" value="800" class="w-full p-1.5 border rounded"></div>
                <div><span class="text-xs text-gray-500 block">取り付け角 (deg)</span><input type="number" id="mw_angle" value="2.0" step="0.1" class="w-full p-1.5 border rounded"></div>
                <div><span class="text-xs text-gray-500 block">翼根コード (mm)</span><input type="number" id="mw_root" value="180" class="w-full p-1.5 border rounded"></div>
                <div><span class="text-xs text-gray-500 block">翼端コード (mm)</span><input type="number" id="mw_tip" value="120" class="w-full p-1.5 border rounded"></div>
                <div><span class="text-xs text-gray-500 block">上反角 (deg)</span><input type="number" id="mw_dihedral" value="5.0" class="w-full p-1.5 border rounded"></div>
                <div><span class="text-xs text-gray-500 block">後退角 (deg)</span><input type="number" id="mw_sweep" value="5.0" class="w-full p-1.5 border rounded"></div>
            </div>
          </div>

          <div class="border-l-4 border-red-500 pl-3">
            <h3 class="text-sm font-bold text-gray-800 mb-2">水平尾翼 (H. Tail)</h3>
            <div class="grid grid-cols-2 gap-x-2 gap-y-3 text-sm">
                <div><span class="text-xs text-gray-500 block">スパン (mm)</span><input type="number" id="ht_span" value="320" class="w-full p-1.5 border rounded"></div>
                <div><span class="text-xs text-gray-500 block">取り付け角 (deg)</span><input type="number" id="ht_angle" value="-1.5" step="0.1" class="w-full p-1.5 border rounded"></div>
                <div><span class="text-xs text-gray-500 block">翼根コード (mm)</span><input type="number" id="ht_root" value="110" class="w-full p-1.5 border rounded"></div>
                <div><span class="text-xs text-gray-500 block">翼端コード (mm)</span><input type="number" id="ht_tip" value="80" class="w-full p-1.5 border rounded"></div>
                <div class="col-span-2"><span class="text-xs text-gray-500 block">位置 (主翼前縁〜尾翼前縁) (mm)</span><input type="number" id="ht_dist" value="500" class="w-full p-1.5 border rounded"></div>
            </div>
          </div>

          <div class="border-l-4 border-yellow-500 pl-3">
            <h3 class="text-sm font-bold text-gray-800 mb-2">垂直尾翼 (V. Tail)</h3>
            <div class="grid grid-cols-2 gap-x-2 gap-y-3 text-sm">
                <div><span class="text-xs text-gray-500 block">高さ (mm)</span><input type="number" id="vt_span" value="140" class="w-full p-1.5 border rounded"></div>
                <div><span class="text-xs text-gray-500 block">位置 (mm)</span><input type="number" id="vt_dist" value="480" class="w-full p-1.5 border rounded"></div>
                <div><span class="text-xs text-gray-500 block">翼根コード (mm)</span><input type="number" id="vt_root" value="130" class="w-full p-1.5 border rounded"></div>
                <div><span class="text-xs text-gray-500 block">翼端コード (mm)</span><input type="number" id="vt_tip" value="70" class="w-full p-1.5 border rounded"></div>
            </div>
          </div>

          <div class="bg-gray-50 p-3 rounded border border-gray-200">
            <h3 class="text-sm font-bold text-gray-800 mb-2">調整・環境</h3>
            <div class="grid grid-cols-2 gap-x-2 gap-y-3 text-sm">
                <div><span class="text-xs text-gray-500 block">おもり重量 (g)</span><input type="number" id="ballast_mass" value="35" class="w-full p-1.5 border rounded bg-white"></div>
                <div><span class="text-xs text-gray-500 block">おもり位置 (mm)</span><input type="number" id="ballast_pos" value="0" placeholder="0=主翼先端" class="w-full p-1.5 border rounded bg-white"></div>
            </div>
          </div>

          <div class="pt-2 flex gap-3">
             <button onclick="window.location.reload()" class="px-4 py-2 text-sm text-gray-600 bg-white border border-gray-300 rounded hover:bg-gray-50 transition-colors">リセット</button>
            <button onclick="runCalculation()" class="flex-1 py-3 bg-[var(--color-brand)] text-white font-bold rounded shadow hover:opacity-90 transition-opacity">計算・実行</button>
          </div>
        </div>
      </div>
    </div>

    <div class="lg:col-span-8 space-y-6">

      <div class="bg-white rounded-xl shadow-sm border border-gray-200 flex flex-col overflow-hidden">
        <div class="bg-gray-50 px-4 py-2 border-b border-gray-200 flex justify-between items-center">
          <h5 class="font-bold text-gray-700 m-0 text-sm">機体形状確認 & 重心位置 (3D)</h5>
          <span class="text-[10px] text-gray-400 uppercase">Interactive 3D</span>
        </div>
        <div class="relative w-full h-[350px] bg-gray-50">
           <div id="plotArea3D" class="absolute inset-0 w-full h-full"></div>
        </div>
      </div>

      <div class="bg-white rounded-xl shadow-sm border border-gray-200 p-5">
          <div id="stabilityAlert" class="mb-5 p-3 rounded text-center font-bold text-sm bg-gray-100">計算を実行してください</div>

          <div class="grid grid-cols-2 md:grid-cols-4 gap-4 mb-2 text-center">
             <div class="p-2 bg-gray-50 rounded">
                <div class="text-[10px] text-gray-500 uppercase">全重量</div>
                <div class="text-xl font-bold text-gray-800" id="res_weight">-</div>
             </div>
             <div class="p-2 bg-gray-50 rounded">
                <div class="text-[10px] text-gray-500 uppercase">静安定余裕 (SM)</div>
                <div class="text-xl font-bold text-gray-800" id="res_sm">-</div>
             </div>
             <div class="p-2 bg-gray-50 rounded">
                <div class="text-[10px] text-gray-500 uppercase">釣り合い速度</div>
                <div class="text-xl font-bold text-[var(--color-brand)]" id="res_vtrim">-</div>
             </div>
             <div class="p-2 bg-gray-50 rounded">
                <div class="text-[10px] text-gray-500 uppercase">滑空比 (L/D)</div>
                <div class="text-xl font-bold text-gray-800" id="res_ld">-</div>
             </div>
          </div>

          <details class="mt-4">
              <summary class="text-xs text-gray-500 cursor-pointer hover:text-[var(--color-brand)]">詳細データを見る</summary>
              <table class="w-full text-sm text-left border-collapse mt-2">
                <tbody id="resultBody" class="text-gray-700 text-xs"></tbody>
              </table>
          </details>
      </div>

      <div class="bg-white rounded-xl shadow-sm border border-gray-200 flex flex-col overflow-hidden">
        <div class="bg-gray-50 px-4 py-2 border-b border-gray-200 flex justify-between items-center">
          <h5 class="font-bold text-gray-700 m-0 text-sm">縦の運動軌跡 (外乱応答シミュレーション)</h5>
        </div>
        <div class="relative w-full h-[300px] bg-white">
          <div id="plotArea2D" class="absolute inset-0 w-full h-full"></div>
        </div>
      </div>

      <div class="bg-white rounded-xl shadow-sm border border-gray-200 flex flex-col overflow-hidden">
        <div class="bg-gray-50 px-4 py-2 border-b border-gray-200 flex justify-between items-center">
          <h5 class="font-bold text-gray-700 m-0 text-sm">姿勢変化 (Pitch Angle)</h5>
        </div>
        <div class="relative w-full h-[250px] bg-white">
          <div id="plotAreaPitch" class="absolute inset-0 w-full h-full"></div>
        </div>
      </div>
    </div>

  </div>
</div>

<script>
// --- Constants ---
const RHO = 1.225;
const G = 9.80665;
const PI = Math.PI;
const D2R = PI / 180;
const R2D = 180 / PI;

// --- Helper Functions ---
const getVal = (id) => parseFloat(document.getElementById(id).value);
const setTxt = (id, txt) => {
    const el = document.getElementById(id);
    if(el) el.innerText = txt;
};

// --- Geometry Calculation ---
function calculateWingProperties(span_mm, root_mm, tip_mm, sweep_deg=0) {
    const b = span_mm / 1000;
    const cr = root_mm / 1000;
    const ct = tip_mm / 1000;

    const S = (cr + ct) * b / 2;
    const AR = (S > 0) ? (b**2) / S : 0;
    const taper = (cr > 0) ? ct / cr : 1;

    const mac = (cr > 0) ? (2/3) * cr * ((1 + taper + taper**2) / (1 + taper)) : 0;
    const y_mac = (b/6) * ((1 + 2*taper) / (1 + taper));
    const x_mac_le = Math.tan(sweep_deg * D2R) * y_mac;

    return { S, AR, mac, b, cr, ct, x_mac_le, y_mac };
}

function calculateComponentInertia(component, totalMass, offset_x, offset_z, global_cg_x, sweep, dihedral, isVertical = false) {
    let Ix = 0, Iy = 0, Iz = 0, Ixz = 0;
    const STRIPS = 10;
    const dy = (component.b / 2) / STRIPS;
    const tan_sweep = Math.tan(sweep * D2R);
    const sin_dihedral = Math.sin(dihedral * D2R);
    const cos_dihedral = Math.cos(dihedral * D2R);

    if (component.S <= 0) return { Ix:0, Iy:0, Iz:0, Ixz:0 };
    const density = totalMass / component.S;

    for (let i = 0; i < STRIPS; i++) {
        const y_local_plane = (i + 0.5) * dy;
        const chord = component.cr + (component.ct - component.cr) * (y_local_plane / (component.b / 2));
        const area = chord * dy;
        const dm = area * density;

        // Local aerodynamic center approx
        const x_local = (y_local_plane * tan_sweep) + (chord * 0.4);

        let dx, dy_pos, dz;

        if (isVertical) {
            dx = (offset_x + x_local) - global_cg_x;
            dy_pos = 0;
            dz = (offset_z + y_local_plane);
            // 垂直尾翼は1枚
            Ix += dm * (dy_pos**2 + dz**2);
            Iy += dm * (dx**2 + dz**2);
            Iz += dm * (dx**2 + dy_pos**2);
            Ixz += dm * (dx * dz);
        } else {
            const y_proj = y_local_plane * cos_dihedral;
            const z_proj = y_local_plane * sin_dihedral;
            dx = (offset_x + x_local) - global_cg_x;

            // Right Wing
            let dy_r = y_proj;
            let dz_r = offset_z - z_proj; // Z is down, dihedral goes UP (-Z)
            Ix += dm * (dy_r**2 + dz_r**2);
            Iy += dm * (dx**2 + dz_r**2);
            Iz += dm * (dx**2 + dy_r**2);
            Ixz += dm * (dx * dz_r);

            // Left Wing
            let dy_l = -y_proj;
            let dz_l = offset_z - z_proj;
            Ix += dm * (dy_l**2 + dz_l**2);
            Iy += dm * (dx**2 + dz_l**2);
            Iz += dm * (dx**2 + dy_l**2);
            Ixz += dm * (dx * dz_l);
        }
    }
    return { Ix, Iy, Iz, Ixz };
}

// --- Dynamics Class (Fixes Scope Issues) ---
class FlightDynamics {
    constructor(mw, ht, vt, inertia, mass, trimData, aeroParams) {
        this.mw = mw; this.ht = ht; this.vt = vt;
        this.I = inertia;
        this.m = mass;
        this.trim = trimData;
        this.ap = aeroParams; // Aero Params

        // 微係数の計算
        this.derivs = this.calcDerivatives();
    }

    calcDerivatives() {
        // 簡易推定
        const { mw, ht, vt, ap } = this;

        // 尾翼容積比
        const lt = ap.x_ac_t - ap.x_cg;
        const lv = ap.x_ac_v - ap.x_cg;
        const Vh = (ht.S * lt) / (mw.S * mw.mac);
        const Vv = (vt.S * lv) / (mw.S * mw.b);

        // 揚力傾斜
        const CLa_w = ap.cla_w;
        const CLa_t = ap.cla_t;
        const CLa_v = 3.0; // 垂直尾翼概算

        const eta_t = 0.9;
        const de_da = ap.de_da;

        // 縦系
        // Cm_alpha = CLa_w*(h - hn) - CLa_t*eta*Vh*(1-de/da)
        const hn = ap.x_ac_w / mw.mac;
        const h = ap.x_cg / mw.mac;
        const Cm_alpha = CLa_w * (h - hn) - CLa_t * eta_t * Vh * (1 - de_da);
        const Cm_q = -2 * CLa_t * eta_t * Vh * (lt / mw.mac); // Damping

        // 横・方向系
        const Cn_beta = CLa_v * Vv * 0.95; // 方向安定
        const Cl_beta = -CLa_w * (ap.dihedral * D2R) * 0.5; // 上反角効果(簡易)
        const Cl_p = -0.4; // ロールダンピング
        const Cn_r = -2 * CLa_v * Vv * (lv / mw.b); // ヨーダンピング

        return {
            Cm_alpha, Cm_q,
            Cn_beta, Cn_r,
            Cl_beta, Cl_p,
            CLa_w, CLa_t
        };
    }

    // 力とモーメントの計算
    calcForces(state, controls) {
        const { u, v, w, p, q, r } = state;
        const { de } = controls;
        const V = this.trim.v_trim;

        // 摂動量
        const alpha = w / V; // 近似
        const beta = v / V;

        // 無次元レート
        const q_hat = (q * this.mw.mac) / (2 * V);
        const p_hat = (p * this.mw.b) / (2 * V);
        const r_hat = (r * this.mw.b) / (2 * V);

        const Q = 0.5 * RHO * V**2;
        const S = this.mw.S;
        const c = this.mw.mac;
        const b = this.mw.b;
        const d = this.derivs;

        // X: Drag (簡易)
        const dFx = -Q * S * 0.05 * (u/V);

        // Z: Lift
        // dCL = CLa * alpha + CLq * q_hat + CLde * de
        const dCL = (d.CLa_w + d.CLa_t) * alpha;
        const dFz = -Q * S * dCL; // Z is down

        // M: Pitch Moment
        // dCm = Cma * alpha + Cmq * q_hat + Cmde * de
        // Cm_de は Vh に依存
        const Cm_de = -d.CLa_t * 0.9 * ((this.ht.S * (this.ap.x_ac_t - this.ap.x_cg))/(S*c));
        const dCm = d.Cm_alpha * alpha + d.Cm_q * q_hat + Cm_de * de;
        const dM = Q * S * c * dCm;

        // Y: Side Force (省略)
        const dFy = Q * S * d.Cn_beta * beta; // 簡易

        // L: Roll Moment
        const dCl = d.Cl_beta * beta + d.Cl_p * p_hat;
        const dL = Q * S * b * dCl;

        // N: Yaw Moment
        const dCn = d.Cn_beta * beta + d.Cn_r * r_hat;
        const dN = Q * S * b * dCn;

        return { Fx: dFx, Fy: dFy, Fz: dFz, L: dL, M: dM, N: dN };
    }

    // 状態微分
    getDerivatives(state, controls) {
        const forces = this.calcForces(state, controls);
        const { m, I, trim } = this;
        const V0 = trim.v_trim;

        // 運動方程式 (線形化・分離)
        // 1. 並進 (u, v, w)
        // m(du + qw - rv) = Fx - mg sin... (摂動)
        // 簡易線形: dot_u = Fx/m
        const dot_u = forces.Fx / m;
        const dot_v = forces.Fy / m - V0 * state.r + G * state.phi; // phi成分
        const dot_w = forces.Fz / m + V0 * state.q;

        // 2. 回転 (p, q, r)
        // I dot_omega = Moment
        const Gamma = I.Ix * I.Iz - I.Ixz ** 2;
        const dot_q = forces.M / I.Iy;
        const dot_p = (I.Iz * forces.L + I.Ixz * forces.N) / Gamma;
        const dot_r = (I.Ixz * forces.L + I.Ix * forces.N) / Gamma;

        // 3. 姿勢角 (phi, theta, psi)
        const dot_phi = state.p;   // 近似
        const dot_theta = state.q;
        const dot_psi = state.r;

        // 4. 位置 (x, y, z)
        const dot_x = V0 + state.u; // 全速度
        const dot_y = V0 * state.psi + state.v;
        const dot_z = -V0 * state.theta + state.w; // 沈下速度

        return {
            u: dot_u, v: dot_v, w: dot_w,
            p: dot_p, q: dot_q, r: dot_r,
            phi: dot_phi, theta: dot_theta, psi: dot_psi,
            x: dot_x, y: dot_y, z: dot_z
        };
    }

    step(state, controls, dt) {
        // RK4 Integration
        const k1 = this.getDerivatives(state, controls);

        const s2 = this.addState(state, k1, dt * 0.5);
        const k2 = this.getDerivatives(s2, controls);

        const s3 = this.addState(state, k2, dt * 0.5);
        const k3 = this.getDerivatives(s3, controls);

        const s4 = this.addState(state, k3, dt);
        const k4 = this.getDerivatives(s4, controls);

        const nextState = {};
        for (let key in state) {
            nextState[key] = state[key] + (dt / 6.0) * (k1[key] + 2*k2[key] + 2*k3[key] + k4[key]);
        }
        return nextState;
    }

    addState(s, k, f) {
        const res = {};
        for(let key in s) res[key] = s[key] + k[key] * f;
        return res;
    }
}


// === Main Function ===
function runCalculation() {
    try {
        // 1. 入力取得
        const inputs = {
            mw_span: getVal('mw_span'), mw_root: getVal('mw_root'), mw_tip: getVal('mw_tip'),
            mw_angle: getVal('mw_angle'), mw_dihedral: getVal('mw_dihedral'), mw_sweep: getVal('mw_sweep'),
            ht_span: getVal('ht_span'), ht_root: getVal('ht_root'), ht_tip: getVal('ht_tip'),
            ht_angle: getVal('ht_angle'), ht_dist: getVal('ht_dist'),
            vt_span: getVal('vt_span'), vt_root: getVal('vt_root'), vt_tip: getVal('vt_tip'),
            vt_dist: getVal('vt_dist'),
            mat: getVal('material'), ball_m: getVal('ballast_mass')/1000, ball_p: getVal('ballast_pos')/1000
        };

        // 2. 幾何学計算
        const mw = calculateWingProperties(inputs.mw_span, inputs.mw_root, inputs.mw_tip, inputs.mw_sweep);
        const ht = calculateWingProperties(inputs.ht_span, inputs.ht_root, inputs.ht_tip, 0);
        const vt = calculateWingProperties(inputs.vt_span, inputs.vt_root, inputs.vt_tip, 15); // VT sweep仮定

        // 3. 重量・重心
        const m_w = mw.S * (inputs.mat/1000);
        const m_h = ht.S * (inputs.mat/1000);
        const m_v = vt.S * (inputs.mat/1000);
        const m_total = m_w + m_h + m_v + inputs.ball_m;

        const x_cg_w = mw.x_mac_le + mw.mac * 0.4;
        const x_cg_h = (inputs.ht_dist/1000) + ht.x_mac_le + ht.mac * 0.4;
        const x_cg_v = (inputs.vt_dist/1000) + vt.x_mac_le + vt.mac * 0.4;
        const x_cg = (m_w*x_cg_w + m_h*x_cg_h + m_v*x_cg_v + inputs.ball_m*inputs.ball_p) / m_total;

        // 4. 慣性モーメント
        const i_mw = calculateComponentInertia(mw, m_w, 0, 0, x_cg, inputs.mw_sweep, inputs.mw_dihedral);
        const i_ht = calculateComponentInertia(ht, m_h, inputs.ht_dist/1000, 0, x_cg, 0, 0);
        const i_vt = calculateComponentInertia(vt, m_v, inputs.vt_dist/1000, 0, x_cg, 15, 0, true);

        const dx_b = inputs.ball_p - x_cg;
        const i_ballast = {
            Ix: inputs.ball_m * 0, // 点とみなす
            Iy: inputs.ball_m * (dx_b**2),
            Iz: inputs.ball_m * (dx_b**2),
            Ixz: 0
        };

        const inertias = {
            Ix: i_mw.Ix + i_ht.Ix + i_vt.Ix + i_ballast.Ix,
            Iy: i_mw.Iy + i_ht.Iy + i_vt.Iy + i_ballast.Iy,
            Iz: i_mw.Iz + i_ht.Iz + i_vt.Iz + i_ballast.Iz,
            Ixz: i_mw.Ixz + i_ht.Ixz + i_vt.Ixz + i_ballast.Ixz
        };

        // 5. 空力パラメータ & トリム解析
        const x_ac_w = mw.x_mac_le + mw.mac * 0.25;
        const x_ac_t = (inputs.ht_dist/1000) + ht.x_mac_le + ht.mac * 0.25;
        const x_ac_v = (inputs.vt_dist/1000) + vt.x_mac_le + vt.mac * 0.25;

        // 効率係数
        const cla_w = 2 * PI * (mw.AR / (mw.AR + 2));
        const cla_t = 2 * PI * (ht.AR / (ht.AR + 2));
        const de_da = (2 * cla_w) / (PI * mw.AR);
        const eta_t = 0.9;

        // 中立点
        const lt = x_ac_t - x_ac_w;
        const Vh = (ht.S * lt) / (mw.S * mw.mac);
        const x_np = x_ac_w + (Vh * eta_t * (cla_t/cla_w) * (1 - de_da)) * mw.mac;
        const static_margin = (x_np - x_cg) / mw.mac;

        // トリム計算 (Moment Balance)
        // Cm = Cmw + Cmt = 0 となる alpha を探す
        const calcCm = (alpha) => {
            const aw = alpha + inputs.mw_angle * D2R;
            const CLw = cla_w * aw;
            const eps = de_da * aw;
            const at = (alpha + inputs.ht_angle * D2R) - eps;
            const CLt = cla_t * at;

            // Moment about CG
            const arm_w = x_cg - x_ac_w; // 正なら頭上げ
            const arm_t = x_ac_t - x_cg; // 正なら頭下げ
            const M_w = CLw * mw.S * arm_w; // Lift w * arm
            const M_t = -CLt * ht.S * eta_t * arm_t; // Lift t * arm (Down force is positive moment?) -> Normal convention: Nose up +

            // Standard convention:
            // Cm = CLw(h - hn) - Vh*CLt*...
            // 簡易モーメントバランス:
            return (M_w + M_t);
        };

        // 単純反復法でトリム迎角探索
        let alpha_trim = 0;
        for(let k=0; k<10; k++) {
            const m = calcCm(alpha_trim);
            const m_d = (calcCm(alpha_trim+0.001) - m)/0.001;
            if(Math.abs(m_d) < 1e-5) break;
            alpha_trim -= m / m_d;
        }

        // トリム状態の性能
        const aw_trim = alpha_trim + inputs.mw_angle * D2R;
        const CL_total = cla_w * aw_trim + cla_t * ((alpha_trim+inputs.ht_angle*D2R)-de_da*aw_trim) * (ht.S/mw.S);
        const v_trim = (CL_total > 0.1) ? Math.sqrt((2 * m_total * G) / (RHO * mw.S * CL_total)) : 0;

        const CD = 0.02 + (CL_total**2)/(PI * mw.AR * 0.85);

        // 結果オブジェクト
        const trimData = { v_trim, alpha_trim, CL: CL_total, CD: CD };
        const aeroParams = {
            cla_w, cla_t, de_da,
            x_cg, x_ac_w, x_ac_t, x_ac_v,
            dihedral: inputs.mw_dihedral
        };

        // 6. 表示更新
        setTxt('res_weight', (m_total*1000).toFixed(0) + ' g');
        setTxt('res_sm', (static_margin*100).toFixed(1) + ' %');
        setTxt('res_vtrim', v_trim.toFixed(1) + ' m/s');
        setTxt('res_ld', (CL_total/CD).toFixed(1));

        const alertEl = document.getElementById('stabilityAlert');
        if(static_margin < 0) {
            alertEl.className = "mb-5 p-3 rounded text-center font-bold text-sm bg-red-100 text-red-800";
            alertEl.innerText = `静不安定 (SM: ${(static_margin*100).toFixed(1)}%) - 重心を前にしてください`;
        } else if(static_margin < 0.05) {
            alertEl.className = "mb-5 p-3 rounded text-center font-bold text-sm bg-yellow-100 text-yellow-800";
            alertEl.innerText = `安定性不足 (SM: ${(static_margin*100).toFixed(1)}%) - 注意`;
        } else {
            alertEl.className = "mb-5 p-3 rounded text-center font-bold text-sm bg-green-100 text-green-800";
            alertEl.innerText = `設計良好 (SM: ${(static_margin*100).toFixed(1)}%)`;
        }

        document.getElementById('resultBody').innerHTML = `
            <tr><td class="px-2 py-1 border-b">重心位置 (CG)</td><td class="px-2 py-1 border-b font-mono">${(x_cg*1000).toFixed(0)} mm</td></tr>
            <tr><td class="px-2 py-1 border-b">中立点 (NP)</td><td class="px-2 py-1 border-b font-mono">${(x_np*1000).toFixed(0)} mm</td></tr>
            <tr><td class="px-2 py-1 border-b">翼面荷重</td><td class="px-2 py-1 border-b font-mono">${(m_total/mw.S).toFixed(2)} kg/m²</td></tr>
            <tr><td class="px-2 py-1 border-b">慣性モーメントIy</td><td class="px-2 py-1 border-b font-mono">${inertias.Iy.toFixed(4)}</td></tr>
        `;

        // 7. シミュレーション実行
        if(v_trim > 0) {
            const fd = new FlightDynamics(mw, ht, vt, inertias, m_total, trimData, aeroParams);

            let state = { u:10, v:0, w:0, p:0, q:0, r:0, phi:0, theta:0, psi:0, x:0, y:0, z:0 };
            const dt = 0.02;
            const timeData = [], hData = [], pData = [];

            for(let t=0; t<10; t+=dt) {
                // Doublet Input (Elevator)
                let de = 0;
                if(t > 1.0 && t < 1.3) de = -5 * D2R; // Up
                if(t > 1.3 && t < 1.6) de = 5 * D2R; // Down

                state = fd.step(state, {de, da:0, dr:0}, dt);

                timeData.push(t);
                hData.push(-state.z); // Altitude deviation
                pData.push(state.theta * R2D);
            }

            // Plot 2D (Height)
            Plotly.newPlot('plotArea2D', [{
                x: timeData, y: hData, mode:'lines', name:'高度変位', line:{color:'#2563eb'}
            }], {
                margin: {t:30, b:40, l:50, r:20},
                title: '高度変化 (m)',
                yaxis: {title: 'dZ (m)'},
                xaxis: {title: 'Time (s)'}
            });

            // Plot Pitch
            Plotly.newPlot('plotAreaPitch', [{
                x: timeData, y: pData, mode:'lines', name:'ピッチ角', line:{color:'#ef4444'}
            }], {
                margin: {t:30, b:40, l:50, r:20},
                title: 'ピッチ角 (deg)',
                yaxis: {title: 'Theta (deg)'}
            });
        }

        // 8. 3D描画
        draw3DPlane(mw, ht, vt, inputs, x_cg);

    } catch(e) {
        console.error(e);
        alert("計算エラー: " + e.message);
    }
}

function draw3DPlane(mw, ht, vt, inputs, x_cg) {
    // Helper to generate mesh
    const createMesh = (geo, ox, oz, angle, dihedral, color, isV=false) => {
        // Simple rectangular approximation for visualization
        // Points: RootLE, RootTE, TipTE, TipLE
        const cr = geo.cr, ct = geo.ct, b2 = geo.b/2;
        const sw = Math.tan(inputs.mw_sweep*D2R)*b2; // Sweep offset for wing only
        const sweep_off = (geo === mw) ? sw : (isV ? b2*Math.tan(15*D2R) : 0);

        // Local coords (Right wing)
        // 0:0,0  1:cr,0  2:ct+sw, b/2  3:sw, b/2
        const p = [
            {x:0, y:0, z:0}, {x:cr, y:0, z:0},
            {x:sweep_off+ct, y:b2, z:0}, {x:sweep_off, y:b2, z:0}
        ];

        // Transform
        const X=[], Y=[], Z=[];
        const rotX = (x,z,th) => ({x: x*Math.cos(th)+z*Math.sin(th), z: -x*Math.sin(th)+z*Math.cos(th)});

        // Right & Left
        const sides = isV ? [1] : [1, -1];
        sides.forEach(side => {
            p.forEach(pt => {
                // Pitch
                let r1 = rotX(pt.x, pt.z, angle*D2R);
                let x = r1.x + ox;
                let z = r1.z + oz;
                let y = pt.y * side;

                // Dihedral (Rotate around X)
                if(!isV) {
                    let z_new = z * Math.cos(dihedral*D2R) + y * Math.sin(dihedral*D2R) * side; // side flips dihedral dir
                    let y_new = y * Math.cos(dihedral*D2R) - z * Math.sin(dihedral*D2R) * side;
                    z = z_new; y = y_new;
                } else {
                    // Vertical Tail: Swap Y and Z roughly
                    // Map local Y(span) to global Z(up)
                    let z_v = pt.y;
                    let y_v = 0;
                    z = oz - z_v; // Z is down, so Up is -Z
                    y = 0;
                }
                X.push(x); Y.push(y); Z.push(-z); // Plotly Z is up
            });
        });

        // Indices
        let I=[], J=[], K=[];
        if(isV) {
            I=[0,0]; J=[1,2]; K=[2,3];
        } else {
            I=[0,0,4,4]; J=[1,2,5,6]; K=[2,3,6,7];
        }

        return {
            type: 'mesh3d',
            x: X, y: Y, z: Z,
            i: I, j: J, k: K,
            color: color, opacity: 0.9, flatshading: true
        };
    };

    const meshMW = createMesh(mw, 0, 0, inputs.mw_angle, inputs.mw_dihedral, '#3b82f6');
    const meshHT = createMesh(ht, inputs.ht_dist/1000, 0, inputs.ht_angle, 0, '#ef4444');
    const meshVT = createMesh(vt, inputs.vt_dist/1000, 0, 0, 0, '#eab308', true);

    const cgMark = {
        type: 'scatter3d', x:[x_cg], y:[0], z:[0], mode:'markers', marker:{size:5, color:'black'}, name:'CG'
    };

    const layout = {
        margin: {l:0, r:0, t:0, b:0},
        scene: {
            aspectmode: 'data',
            camera: { eye: {x: -1.5, y: 1.5, z: 1.5} },
            xaxis: {title: 'X (Back)'}, yaxis: {title: 'Y (Right)'}, zaxis: {title: 'Z (Up)'}
        }
    };

    Plotly.react('plotArea3D', [meshMW, meshHT, meshVT, cgMark], layout);
}

// Init
window.onload = runCalculation;
</script>
</body>
</html> -->

{:/}
