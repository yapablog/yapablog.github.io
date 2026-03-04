---
layout: tools
title: 航空機設計計算
ToolName: 航空機の基本設計と安定性計算ツール
permalink: /planecalc_3D_new/
Description: 主翼や尾翼の形から機体性能を簡易的に計算するWebツールです。
HowtoUse: |
  1. まず **コード長** と **点群データ** を入力してください。
  2. NACA4桁翼型であれば、コードを入力して「生成」ボタンから自動生成することが可能です。
  3. もし正規化されていない場合は、正規化ボタンを押してください。
  4. 「計算・描画実行」ボタンを押すと、右側（スマホの場合は下側）に形状と解析結果が表示されます。
---

{::nomarkdown}

<!DOCTYPE html>
<html lang="ja">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>CalcPlane 3D - Advanced Dynamics</title>
<script src="https://cdn.plot.ly/plotly-2.24.1.min.js"></script>
<script src="https://cdn.tailwindcss.com"></script>
<style>
  :root { --color-primary: #2563eb; --color-brand: #3b82f6; }
  body { font-family: "Helvetica Neue", Arial, sans-serif; background-color: #f3f4f6; }
  input[type="number"] { font-family: monospace; }
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
        
        <div class="p-5 space-y-6 flex-1 overflow-y-auto">
            
          <div>
            <label class="block text-xs font-bold text-gray-500 uppercase tracking-wider mb-2">機体素材</label>
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
            <h3 class="text-sm font-bold text-gray-800 mb-2">調整・バラスト</h3>
            <div class="grid grid-cols-2 gap-x-2 gap-y-3 text-sm">
                <div><span class="text-xs text-gray-500 block">おもり重量 (g)</span><input type="number" id="ballast_mass" value="30" class="w-full p-1.5 border rounded bg-white"></div>
                <div><span class="text-xs text-gray-500 block">おもり位置 (mm)</span><input type="number" id="ballast_pos" value="-20" placeholder="0=主翼先端" class="w-full p-1.5 border rounded bg-white"></div>
            </div>
             <p class="text-[10px] text-gray-400 mt-2">※おもり位置は、主翼前縁より前ならマイナス、後ろならプラス</p>
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
          <h5 class="font-bold text-gray-700 m-0 text-sm">機体形状 & 重心 (3D Preview)</h5>
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
              <summary class="text-xs text-gray-500 cursor-pointer hover:text-[var(--color-brand)]">詳細データを見る (重心・慣性・微係数)</summary>
              <table class="w-full text-sm text-left border-collapse mt-2">
                <tbody id="resultBody" class="text-gray-700 text-xs"></tbody>
              </table>
          </details>
      </div>

      <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
          <div class="bg-white rounded-xl shadow-sm border border-gray-200 flex flex-col overflow-hidden">
            <div class="bg-gray-50 px-4 py-2 border-b border-gray-200">
              <h5 class="font-bold text-gray-700 m-0 text-sm">高度変化 (Trajectory)</h5>
            </div>
            <div class="relative w-full h-[250px] bg-white">
              <div id="plotArea2D" class="absolute inset-0 w-full h-full"></div>
            </div>
          </div>

          <div class="bg-white rounded-xl shadow-sm border border-gray-200 flex flex-col overflow-hidden">
            <div class="bg-gray-50 px-4 py-2 border-b border-gray-200">
              <h5 class="font-bold text-gray-700 m-0 text-sm">姿勢変化 (Pitch Angle)</h5>
            </div>
            <div class="relative w-full h-[250px] bg-white">
              <div id="plotAreaPitch" class="absolute inset-0 w-full h-full"></div>
            </div>
          </div>
      </div>

    </div>

  </div>
</div>

<script>
/**
 * CALCPLANE 3D - Fixed Dynamics Engine
 * 修正版: 重力項を含む非線形運動方程式の実装、慣性計算の適正化
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

// --- 2. Inertia Calculation (Improved) ---
// 胴体の慣性を考慮するために改良
function calculateAccurateInertia(mw, ht, vt, inputs, m_total, x_cg) {
    // 各パーツの質量概算
    const mat_density = inputs.mat / 1000; // kg/m2
    const m_w = mw.S * mat_density;
    const m_h = ht.S * mat_density;
    const m_v = vt.S * mat_density;
    const m_ballast = inputs.ball_m;
    
    // 胴体(Fuselage)質量の推定: 全質量の残り半分くらいが構造材と仮定したり、
    // ここでは簡易的に「尾翼までの距離」を持つ棒として慣性を追加
    // 軽い紙飛行機などは主翼・尾翼以外の重量は無視できる場合もあるが、慣性への寄与は大きい。
    // 今回は安全側として、素材面密度から計算した質量のみを使うが、分布をストリップ法で計算する。

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

    // 【重要】胴体ロッドの慣性 (Fuselage stick inertia)
    // これがないとIyが過小になり発散しやすい
    // 簡易的に主翼から尾翼までの棒とみなす（質量は全体の10%程度を仮想的に割り当て、または無視して慣性だけ加算）
    // ここでは質量を変えず、既存の部材が「棒」でつながっている剛性などを考慮し、
    // 数値安定性のためにIyに最低限の値を保証する
    const min_Iy = m_total * (inputs.ht_dist/1000)**2 * 0.05; 
    if(Iy < min_Iy) Iy = min_Iy;

    return { Ix, Iy, Iz, Ixz };
}


// --- 3. Dynamics Engine (Non-Linear) ---
class FlightDynamics {
    constructor(mw, ht, vt, inertia, mass, trimData, aeroParams) {
        this.mw = mw; this.ht = ht; this.vt = vt;
        this.I = inertia;
        this.m = mass;
        this.trim = trimData;
        this.ap = aeroParams;
        
        // 効率係数などの事前計算
        this.calcCoefficients();
    }

    calcCoefficients() {
        const { mw, ht, vt, ap } = this;
        // 揚力傾斜 CLa (3次元補正)
        // a = a0 / (1 + a0/(pi*e*AR))
        const e = 0.9; // Oswald efficiency
        this.CLa_w = (2 * PI) / (1 + (2 * PI) / (PI * e * mw.AR));
        this.CLa_t = (2 * PI) / (1 + (2 * PI) / (PI * e * ht.AR));
        this.CLa_v = 3.0; // 垂直尾翼概算

        // 減衰微係数 (Damping Derivatives)
        // Cmq: ピッチダンピング
        const lt = ap.x_ac_t - ap.x_cg;
        const Vh = (ht.S * lt) / (mw.S * mw.mac);
        // Cmq = -2 * a_t * Vh * eta * (lt/c)
        // 尾翼効果を主とする
        this.Cmq = -2.2 * this.CLa_t * 0.9 * Vh * (lt / mw.mac);

        // Clp: ロールダンピング (Main wing dominant)
        this.Clp = -0.4 * mw.AR / 6.0; // 簡易近似

        // Cnr: ヨーダンピング (Vertical tail dominant)
        const lv = ap.x_ac_v - ap.x_cg;
        const Vv = (vt.S * lv) / (mw.S * mw.b);
        this.Cnr = -2.0 * this.CLa_v * Vv * (lv / mw.b);
    }

    // 状態微分方程式 (Equations of Motion)
    // state: [u, v, w, p, q, r, phi, theta, psi, x, y, z] (Body axis velocities, Euler angles, Earth pos)
    getDerivatives(s, controls) {
        // Unpack state
        const u=s[0], v=s[1], w=s[2];
        const p=s[3], q=s[4], r=s[5];
        const phi=s[6], theta=s[7], psi=s[8];
        
        const Vsq = u*u + v*v + w*w;
        const V = Math.sqrt(Vsq);
        if(V < 0.1) return new Float64Array(12); // Stopped

        // 1. Angle of Attack & Sideslip
        const alpha = Math.atan2(w, u);
        const beta = Math.asin(v / V);

        // 2. Dynamic Pressure
        const Q = 0.5 * RHO * Vsq;

        // 3. Aerodynamic Forces (Wind Axis -> Body Axis)
        // Lift & Drag
        // Main Wing Lift
        const aw = alpha + this.ap.mw_incidence; // body incidence added
        const CLw = this.CLa_w * aw;
        
        // Tail Lift (Downwash considered)
        const eps = this.ap.de_da * aw;
        const at = alpha - eps + this.ap.ht_incidence;
        const CLt = this.CLa_t * at;

        const CL = CLw + CLt * (this.ht.S / this.mw.S) + (controls.de * 0.3); // Simple flap effect

        // Drag (Polar)
        const CD0 = 0.025;
        const k = 1 / (PI * 0.85 * this.mw.AR);
        const CD = CD0 + k * CL * CL;

        // Forces in Wind Axis
        const Lift = Q * this.mw.S * CL;
        const Drag = Q * this.mw.S * CD;
        const Side = Q * this.mw.S * ( -0.5 * beta ); // Simple restoring sideforce

        // Transform to Body Axis (Fx, Fz)
        // Fx = L sin(a) - D cos(a)
        // Fz = -L cos(a) - D sin(a)
        const ca = Math.cos(alpha);
        const sa = Math.sin(alpha);
        
        let Fx_aero = Lift * sa - Drag * ca;
        let Fz_aero = -Lift * ca - Drag * sa;
        let Fy_aero = Side; // Approximate for small beta

        // 4. Moments
        const c = this.mw.mac;
        const b = this.mw.b;
        
        // Pitching Moment (Cm)
        // Cm_static: from static margin logic (already integrated in trim, but here we calculate from components)
        // Moment = M_wing + M_tail
        const arm_w = this.ap.x_cg - this.ap.x_ac_w; // + if CG is aft of AC (unstable contribution)
        const arm_t = this.ap.x_cg - this.ap.x_ac_t; // - usually (stable contribution)
        
        // Wing Moment (around CG)
        const Mw = (Q * this.mw.S * CLw) * arm_w; 
        // Tail Moment (around CG)
        // Tail lift acts at tail AC. Fz_tail approx -Lift_tail
        const Mt = (Q * this.ht.S * CLt * 0.9) * arm_t; 

        // Damping Moment
        const Mdamp = Q * this.mw.S * c * (this.Cmq * (q * c) / (2 * V));

        let M_aero = Mw + Mt + Mdamp;
        
        // Control Moment (Elevator)
        // dCm_de approx -Vh * eta * CLat ... integrated roughly:
        const M_ctrl = Q * this.mw.S * c * (-1.5 * controls.de); 
        M_aero += M_ctrl;


        // Roll & Yaw (simplified)
        let L_aero = Q * this.mw.S * b * (this.Clp * (p * b)/(2*V) - 2.0 * beta); // Dihedral effect (-beta)
        let N_aero = Q * this.mw.S * b * (this.Cnr * (r * b)/(2*V) + 1.0 * beta); // Weathercock stability (+beta)

        // 5. Equations of Motion (Rigid Body)
        // Linear Accelerations
        // m(du/dt + qw - rv) = Fx + Fg_x
        // Fg_x = -mg sin(theta)
        // Fg_y = mg cos(theta)sin(phi)
        // Fg_z = mg cos(theta)cos(phi)
        
        const m = this.m;
        const g = G;
        const st = Math.sin(theta);
        const ct = Math.cos(theta);
        const sph = Math.sin(phi);
        const cph = Math.cos(phi);

        const Fx = Fx_aero - m * g * st;
        const Fy = Fy_aero + m * g * ct * sph;
        const Fz = Fz_aero + m * g * ct * cph;

        const du = Fx/m - (q*w - r*v);
        const dv = Fy/m - (r*u - p*w);
        const dw = Fz/m - (p*v - q*u);

        // Angular Accelerations
        // Ix dot_p - Ixz dot_r = L - qr(Iz - Iy) + Ixz pq
        // Iz dot_r - Ixz dot_p = N - pq(Iy - Ix) - Ixz qr
        // Iy dot_q = M - pr(Ix - Iz) - Ixz(p^2 - r^2)
        
        const { Ix, Iy, Iz, Ixz } = this.I;
        
        // Solve for dot_q
        const dq = (M_aero - p*r*(Ix - Iz) - Ixz*(p*p - r*r)) / Iy;

        // Solve coupled p, r
        const rhs_L = L_aero - q*r*(Iz - Iy) + Ixz*p*q;
        const rhs_N = N_aero - p*q*(Iy - Ix) - Ixz*q*r;
        
        const det = Ix*Iz - Ixz*Ixz;
        const dp = (Iz * rhs_L + Ixz * rhs_N) / det;
        const dr = (Ixz * rhs_L + Ix * rhs_N) / det;

        // Kinematics (Euler rates)
        const dphi = p + (q*sph + r*cph)*Math.tan(theta);
        const dtheta = q*cph - r*sph;
        const dpsi = (q*sph + r*cph) / ct;

        // Navigation (Earth velocities)
        // dx = u cosT cosP + v ... (Body to Earth)
        // Simplified rotation matrix
        const dx = u*ct*Math.cos(psi) + v*(Math.sin(phi)*st*Math.cos(psi) - cph*Math.sin(psi)) + w*(cph*st*Math.cos(psi) + sph*Math.sin(psi));
        // Altitude (dz is Down)
        const dz = -u*st + v*sph*ct + w*cph*ct;
        // y is ignored for plot mostly but calculated
        const dy = u*ct*Math.sin(psi) + v*(Math.sin(phi)*st*Math.sin(psi) + cph*Math.cos(psi)) + w*(cph*st*Math.sin(psi) - sph*Math.cos(psi));

        return [du, dv, dw, dp, dq, dr, dphi, dtheta, dpsi, dx, dy, dz];
    }

    // RK4 Integration
    step(state, controls, dt) {
        const k1 = this.getDerivatives(state, controls);
        const s2 = state.map((v, i) => v + k1[i]*dt*0.5);
        const k2 = this.getDerivatives(s2, controls);
        const s3 = state.map((v, i) => v + k2[i]*dt*0.5);
        const k3 = this.getDerivatives(s3, controls);
        const s4 = state.map((v, i) => v + k3[i]*dt);
        const k4 = this.getDerivatives(s4, controls);

        const nextState = [];
        for(let i=0; i<state.length; i++) {
            nextState[i] = state[i] + (dt/6.0)*(k1[i] + 2*k2[i] + 2*k3[i] + k4[i]);
        }
        return nextState;
    }
}


// === Main Calculation ===
function runCalculation() {
    try {
        // 1. Inputs
        const inputs = {
            mw_span: getVal('mw_span'), mw_root: getVal('mw_root'), mw_tip: getVal('mw_tip'),
            mw_angle: getVal('mw_angle'), mw_dihedral: getVal('mw_dihedral'), mw_sweep: getVal('mw_sweep'),
            ht_span: getVal('ht_span'), ht_root: getVal('ht_root'), ht_tip: getVal('ht_tip'),
            ht_angle: getVal('ht_angle'), ht_dist: getVal('ht_dist'),
            vt_span: getVal('vt_span'), vt_root: getVal('vt_root'), vt_tip: getVal('vt_tip'),
            vt_dist: getVal('vt_dist'),
            mat: getVal('material'), ball_m: getVal('ballast_mass')/1000, ball_p: getVal('ballast_pos')/1000
        };

        // 2. Geometry
        const mw = calculateWingProperties(inputs.mw_span, inputs.mw_root, inputs.mw_tip, inputs.mw_sweep);
        const ht = calculateWingProperties(inputs.ht_span, inputs.ht_root, inputs.ht_tip, 0);
        const vt = calculateWingProperties(inputs.vt_span, inputs.vt_root, inputs.vt_tip, 15);

        // 3. Mass & CG
        const m_w = mw.S * (inputs.mat/1000);
        const m_h = ht.S * (inputs.mat/1000);
        const m_v = vt.S * (inputs.mat/1000);
        const m_total = m_w + m_h + m_v + inputs.ball_m;

        const x_cg_w = mw.x_mac_le + mw.mac * 0.4;
        const x_cg_h = (inputs.ht_dist/1000) + ht.x_mac_le + ht.mac * 0.4;
        const x_cg_v = (inputs.vt_dist/1000) + vt.x_mac_le + vt.mac * 0.4;
        const x_cg = (m_w*x_cg_w + m_h*x_cg_h + m_v*x_cg_v + inputs.ball_m*inputs.ball_p) / m_total;

        // 4. Inertia (Accurate)
        const inertias = calculateAccurateInertia(mw, ht, vt, inputs, m_total, x_cg);

        // 5. Aerodynamics & Trim
        const x_ac_w = mw.x_mac_le + mw.mac * 0.25;
        const x_ac_t = (inputs.ht_dist/1000) + ht.x_mac_le + ht.mac * 0.25;
        const x_ac_v = (inputs.vt_dist/1000) + vt.x_mac_le + vt.mac * 0.25;

        // Lift Slopes & Downwash
        const e = 0.9;
        const cla_w = (2 * PI) / (1 + 2 / (mw.AR * e));
        const cla_t = (2 * PI) / (1 + 2 / (ht.AR * e));
        const de_da = (2 * cla_w) / (PI * mw.AR);
        const eta_t = 0.9;

        // Neutral Point (Static Margin)
        const lt = x_ac_t - x_ac_w;
        const Vh = (ht.S * lt) / (mw.S * mw.mac);
        // Stick-fixed Neutral Point position
        const x_np = x_ac_w + (Vh * eta_t * (cla_t/cla_w) * (1 - de_da)) * mw.mac;
        const static_margin = (x_np - x_cg) / mw.mac;

        // Trim Calculation (Find Alpha where Cm=0)
        // Cm = Cm_ac_w + CLw(h - hn_w) - Vh*eta*CLt
        // But simpler to just balance Moments around CG:
        // M_w + M_t = 0
        const findTrim = () => {
            let a = 0; // alpha
            for(let i=0; i<20; i++) {
                const aw = a + inputs.mw_angle*D2R;
                const eps = de_da * aw;
                const at = a - eps + inputs.ht_angle*D2R;
                
                const CLw = cla_w * aw;
                const CLt = cla_t * at;
                
                // Moment Coefficient contributions
                const Cmw = CLw * ((x_cg - x_ac_w) / mw.mac); // Positive if CG aft
                const Cmt = -CLt * eta_t * Vh; // Positive if Tail Downforce
                
                const Cm = Cmw + Cmt;
                // Derivative approx dCm/da
                const dCmw = cla_w * ((x_cg - x_ac_w) / mw.mac);
                const dCmt = -cla_t * (1 - de_da) * eta_t * Vh;
                const dCm = dCmw + dCmt;
                
                if(Math.abs(Cm) < 1e-6) return a;
                a = a - Cm / dCm;
            }
            return a;
        };
        const alpha_trim = findTrim();
        
        // Trim Speed
        const aw_trim = alpha_trim + inputs.mw_angle*D2R;
        const eps_trim = de_da * aw_trim;
        const at_trim = alpha_trim - eps_trim + inputs.ht_angle*D2R;
        const CL_total = cla_w * aw_trim + cla_t * at_trim * (ht.S/mw.S);
        
        let v_trim = 0;
        if(CL_total > 0) {
            v_trim = Math.sqrt((2 * m_total * G) / (RHO * mw.S * CL_total));
        }

        // 6. UI Update
        setTxt('res_weight', (m_total*1000).toFixed(0) + ' g');
        setTxt('res_sm', (static_margin*100).toFixed(1) + ' %');
        setTxt('res_vtrim', v_trim.toFixed(1) + ' m/s');
        
        // L/D estimation
        const CD0 = 0.025;
        const k = 1/(PI*0.85*mw.AR);
        const CD = CD0 + k*CL_total*CL_total;
        setTxt('res_ld', (CL_total/CD).toFixed(1));

        const alertEl = document.getElementById('stabilityAlert');
        if(static_margin < 0) {
            alertEl.className = "mb-5 p-3 rounded text-center font-bold text-sm bg-red-100 text-red-800";
            alertEl.innerText = `危険: 静不安定 (SM: ${(static_margin*100).toFixed(1)}%) - 重心を前にしてください`;
        } else if(static_margin < 0.05) {
            alertEl.className = "mb-5 p-3 rounded text-center font-bold text-sm bg-yellow-100 text-yellow-800";
            alertEl.innerText = `注意: 安定性不足 (SM: ${(static_margin*100).toFixed(1)}%) - 過敏です`;
        } else {
            alertEl.className = "mb-5 p-3 rounded text-center font-bold text-sm bg-green-100 text-green-800";
            alertEl.innerText = `設計良好 (SM: ${(static_margin*100).toFixed(1)}%)`;
        }

        document.getElementById('resultBody').innerHTML = `
            <tr><td class="px-2 py-1 border-b">重心位置 (CG)</td><td class="px-2 py-1 border-b font-mono">${(x_cg*1000).toFixed(0)} mm</td></tr>
            <tr><td class="px-2 py-1 border-b">中立点 (NP)</td><td class="px-2 py-1 border-b font-mono">${(x_np*1000).toFixed(0)} mm</td></tr>
            <tr><td class="px-2 py-1 border-b">慣性モーメントIy</td><td class="px-2 py-1 border-b font-mono">${inertias.Iy.toFixed(5)} kg·m²</td></tr>
            <tr><td class="px-2 py-1 border-b">トリム迎角</td><td class="px-2 py-1 border-b font-mono">${(alpha_trim*R2D).toFixed(2)} deg</td></tr>
        `;

        // 7. Simulation
        if(v_trim > 0 && static_margin > -0.05) {
            const aeroParams = {
                mw_incidence: inputs.mw_angle * D2R,
                ht_incidence: inputs.ht_angle * D2R,
                de_da, x_cg, x_ac_w, x_ac_t, x_ac_v
            };
            const fd = new FlightDynamics(mw, ht, vt, inertias, m_total, {v_trim}, aeroParams);

            // Initial State: [u, v, w, p, q, r, phi, theta, psi, x, y, z]
            // Start at Trim condition. 
            // u = V cos(alpha), w = V sin(alpha)
            const u0 = v_trim * Math.cos(alpha_trim);
            const w0 = v_trim * Math.sin(alpha_trim);
            
            // Initial Theta needs to match climb/descent angle for equilibrium
            // Gamma = Theta - Alpha. For level flight trim, Gamma = 0 => Theta = Alpha.
            const theta0 = alpha_trim;

            let state = [u0, 0, w0, 0, 0, 0, 0, theta0, 0, 0, 0, 0];
            
            const dt = 0.001; // Smaller step for stability
            const timeData = [], hData = [], pData = [];
            
            for(let t=0; t<10.0; t+=dt) {
                // Elevator Doublet Input (Kick the system)
                let de = 0;
                if(t > 1.0 && t < 1.2) de = -5 * D2R; // Up (Negative de is usually nose up?) depends on sign convention. Here: Nose Up
                if(t > 1.2 && t < 1.4) de = 5 * D2R;  // Down

                state = fd.step(state, {de}, dt);
                
                timeData.push(t);
                hData.push(-state[11]); // Altitude = -z
                pData.push(state[7] * R2D); // Theta
                
                // Safety break if diverged
                if(Math.abs(state[7]) > 80*D2R || Math.abs(state[11]) > 500) break;
            }

            // Plot
            Plotly.newPlot('plotArea2D', [{
                x: timeData, y: hData, mode:'lines', name:'高度', line:{color:'#2563eb', width:2}
            }], {
                margin: {t:30, b:40, l:50, r:20},
                title: false,
                yaxis: {title: '高度 (m)'},
                xaxis: {title: '時間 (s)'}
            });

            Plotly.newPlot('plotAreaPitch', [{
                x: timeData, y: pData, mode:'lines', name:'ピッチ角', line:{color:'#ef4444', width:2}
            }], {
                margin: {t:30, b:40, l:50, r:20},
                title: false,
                yaxis: {title: 'ピッチ角 (deg)'},
                xaxis: {title: '時間 (s)'}
            });
        }

        // 8. Draw 3D
        draw3DPlane(mw, ht, vt, inputs, x_cg);

    } catch(e) {
        console.error(e);
        alert("計算エラー: " + e.message);
    }
}

// 3D Drawing Helper
function draw3DPlane(mw, ht, vt, inputs, x_cg) {
    const createMesh = (geo, ox, oz, angle, dihedral, color, isV=false) => {
        const cr = geo.cr, ct = geo.ct, b2 = geo.b/2;
        const sw = Math.tan(inputs.mw_sweep*D2R)*b2; 
        const sweep_off = (geo === mw) ? sw : (isV ? b2*Math.tan(15*D2R) : 0);

        // Local coords
        const p = [
            {x:0, y:0, z:0}, {x:cr, y:0, z:0}, 
            {x:sweep_off+ct, y:b2, z:0}, {x:sweep_off, y:b2, z:0}
        ];
        
        const X=[], Y=[], Z=[];
        const rotX = (x,z,th) => ({x: x*Math.cos(th)+z*Math.sin(th), z: -x*Math.sin(th)+z*Math.cos(th)});
        
        const sides = isV ? [1] : [1, -1];
        sides.forEach(side => {
            p.forEach(pt => {
                let r1 = rotX(pt.x, pt.z, angle*D2R);
                let x = r1.x + ox;
                let z = r1.z + oz;
                let y = pt.y * side;
                
                if(!isV) {
                    let z_new = z * Math.cos(dihedral*D2R) + y * Math.sin(dihedral*D2R) * side;
                    let y_new = y * Math.cos(dihedral*D2R) - z * Math.sin(dihedral*D2R) * side;
                    z = z_new; y = y_new;
                } else {
                    z = oz - pt.y; 
                    y = 0;
                }
                X.push(x); Y.push(y); Z.push(-z); 
            });
        });

        let I=[], J=[], K=[];
        if(isV) { I=[0,0]; J=[1,2]; K=[2,3]; } else { I=[0,0,4,4]; J=[1,2,5,6]; K=[2,3,6,7]; }
        
        return {
            type: 'mesh3d', x: X, y: Y, z: Z, i: I, j: J, k: K,
            color: color, opacity: 0.9, flatshading: true
        };
    };

    const meshMW = createMesh(mw, 0, 0, inputs.mw_angle, inputs.mw_dihedral, '#3b82f6');
    const meshHT = createMesh(ht, inputs.ht_dist/1000, 0, inputs.ht_angle, 0, '#ef4444');
    const meshVT = createMesh(vt, inputs.vt_dist/1000, 0, 0, 0, '#eab308', true);
    const cgMark = { type: 'scatter3d', x:[x_cg], y:[0], z:[0], mode:'markers', marker:{size:6, color:'black'}, name:'CG' };

    const layout = {
        margin: {l:0, r:0, t:0, b:0},
        scene: {
            aspectmode: 'data', camera: { eye: {x: -1.5, y: 1.5, z: 1.5} },
            xaxis: {title: '', showticklabels:false}, yaxis: {title: '', showticklabels:false}, zaxis: {title: '', showticklabels:false}
        }
    };
    Plotly.react('plotArea3D', [meshMW, meshHT, meshVT, cgMark], layout);
}

window.onload = runCalculation;
</script>
</body>
</html>

{:/}
