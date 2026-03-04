---
layout: tools
title: 航空機設計計算
ToolName: 航空機の基本設計と安定性計算ツール
permalink: /plane_calc/
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
    // 今回はテーパー翼の前縁後退角は0(直線)と仮定するが、将来拡張用に
    const x_mac_le = Math.tan(sweep_deg * D2R) * y_mac; 

    return { S, AR, mac, b, cr, ct, x_mac_le, y_mac };
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


// === メイン計算処理 ===
function runCalculation() {
    // 1. 入力取得
    const mw_span = getVal('mw_span');
    const mw_root = getVal('mw_root');
    const mw_tip = getVal('mw_tip');
    const mw_angle = getVal('mw_angle');
    const mw_dihedral = getVal('mw_dihedral');

    const ht_span = getVal('ht_span');
    const ht_root = getVal('ht_root');
    const ht_tip = getVal('ht_tip');
    const ht_angle = getVal('ht_angle');
    const ht_dist = getVal('ht_dist'); // mm

    const vt_span = getVal('vt_span');
    const vt_root = getVal('vt_root');
    const vt_tip = getVal('vt_tip');
    const vt_dist = getVal('vt_dist'); // mm

    const mat_gsm = getVal('material') * 1000; // g/m2 input -> convert later
    const ballast_mass_kg = getVal('ballast_mass') / 1000;
    const ballast_pos_m = getVal('ballast_pos') / 1000;
    const sim_v0 = getVal('sim_v0');
    const sim_pitch = getVal('sim_pitch');

    // 2. 幾何学計算
    const mw = calculateWingProperties(mw_span, mw_root, mw_tip);
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

    // 慣性モーメント Iy (Pitch軸)
    // Iy = Sum( I_local + m * d^2 )
    // 平板翼の I_local_y は chord方向の長さ c に対して m*c^2/12 程度だが、平行軸定理の項(m*d^2)が支配的
    const calcI = (m, x_part, mac) => m * ((x_part - x_cg)**2 + (mac**2)/18); // 係数は概算
    const Iy = calcI(m_w, x_cg_w, mw.mac) + calcI(m_h, x_cg_h, ht.mac) + calcI(m_v, x_cg_v, vt.mac) + ballast_mass_kg * (x_cg_b - x_cg)**2;

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
    // Cm_cg = Cmw + Cmt
    // Cmw = CLw * (xcg - xacw)/mac  (注: xcg > xacwで不安定モーメント正)
    // Cmt = -CLt * eta * (xact - xcg)/mac * (St/Sw)
    
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


    // 6. 運動シミュレーション (Body Axis Equations)
    // 状態変数: u, w, q, theta, x, z
    let u = sim_v0 * Math.cos(a_trim); // Trim状態に近いと仮定してスタート
    let w = sim_v0 * Math.sin(a_trim); // 初期迎角はTrim値などを入れても良いが、ここではv0方向=機軸として一旦0スタート
    // ユーザー入力のPitch角適用
    u = sim_v0; 
    w = 0; 
    
    let q = 0;
    let theta = sim_pitch * D2R;
    let x = 0;
    let z = -1.5; // 高度1.5m (NED座標系で上空は負)

    const dt = 0.01;
    const max_t = 8.0;
    const trajX = [], trajZ = [];

    // ピッチダンピング係数 Cmq
    // Cmq = -2 * eta * Vh * (lt/mac) * cla_t  (Approx)
    // 実際は物理次元のモーメント係数として計算内で処理
    
    for(let t=0; t<max_t; t+=dt) {
        const V = Math.sqrt(u**2 + w**2);
        const alpha = Math.atan2(w, u); // Body axis alpha
        
        // Forces
        const dynQ = 0.5 * RHO * V**2;
        
        // Wing
        const aw = alpha + mw_angle * D2R;
        const CLw = cla_w * aw;
        const CDw = 0.01 + k_w * CLw**2;
        
        // Tail
        // alpha_tail includes q induced angle: q * lt / V
        const eps = de_da * aw;
        const q_ind = (q * (x_ac_t - x_cg)) / V;
        const at = (alpha + ht_angle * D2R) - eps + q_ind;
        const CLt = cla_t * at;
        const CDt = 0.01;

        // Lift & Drag (Wind Axis)
        const L_main = dynQ * mw.S * CLw;
        const D_main = dynQ * mw.S * CDw;
        const L_tail = dynQ * ht.S * CLt * eta_t;
        const D_tail = dynQ * ht.S * CDt * eta_t;

        const L_tot = L_main + L_tail;
        const D_tot = D_main + D_tail + dynQ * mw.S * 0.01; // Body drag

        // Convert to Body Axis Forces (Fx, Fz)
        // Fx = L sin(a) - D cos(a) ... No, definition:
        // Drag is parallel to V, Lift perpendicular.
        // Fx (forward) = -D cos(a) + L sin(a)
        // Fz (downward) = -D sin(a) - L cos(a)
        const Fx_aero = -D_tot * Math.cos(alpha) + L_tot * Math.sin(alpha);
        const Fz_aero = -D_tot * Math.sin(alpha) - L_tot * Math.cos(alpha);

        // Moment
        const Mw = L_main * Math.cos(alpha) * (x_cg - x_ac_w); // approx lever arm
        const Mt = -L_tail * Math.cos(alpha) * (x_ac_t - x_cg);
        
        // Damping (Tail lift change due to rotation is already in q_ind, add explicit damping for fuselage/wing if needed)
        // Usually q_ind in alpha_tail covers the main aerodynamic damping.
        // Add small parasitic damping
        const M_damp = -0.1 * q; 

        const M_tot = Mw + Mt + M_damp;

        // Equations of Motion
        const du = -q*w + G*Math.sin(theta) + Fx_aero/m_total;
        const dw = q*u + G*Math.cos(theta) + Fz_aero/m_total;
        const dq = M_tot / Iy;
        const dtheta = q;

        // Euler Integrate
        u += du * dt;
        w += dw * dt;
        q += dq * dt;
        theta += dtheta * dt;

        // Position (Earth Axis) - NED (z is down)
        const dx = u * Math.cos(theta) + w * Math.sin(theta);
        const dz = -u * Math.sin(theta) + w * Math.cos(theta);
        
        x += dx * dt;
        z += dz * dt;

        trajX.push(x);
        trajZ.push(-z); // Plot altitude as positive

        if(z > 0) break; // Crash (Ground)
    }


    // 7. 結果表示 (DOM)
    setTxt('res_weight', (m_total*1000).toFixed(0) + ' g');
    setTxt('res_sm', (static_margin*100).toFixed(1) + ' %');
    setTxt('res_vtrim', v_trim.toFixed(1) + ' m/s');
    setTxt('res_ld', LD.toFixed(1));

    // 詳細テーブル
    const resHTML = `
        <tr><td class="px-2 py-1 border-b">翼面荷重</td><td class="px-2 py-1 border-b font-mono">${(m_total/mw.S).toFixed(2)} kg/m²</td></tr>
        <tr><td class="px-2 py-1 border-b">中立点 (NP)</td><td class="px-2 py-1 border-b font-mono">${(x_np*1000).toFixed(0)} mm</td></tr>
        <tr><td class="px-2 py-1 border-b">重心 (CG)</td><td class="px-2 py-1 border-b font-mono">${(x_cg*1000).toFixed(0)} mm</td></tr>
        <tr><td class="px-2 py-1 border-b">慣性モーメントIy</td><td class="px-2 py-1 border-b font-mono">${Iy.toFixed(4)} kg·m²</td></tr>
    `;
    document.getElementById('resultBody').innerHTML = resHTML;

    // アラート
    const alertEl = document.getElementById('stabilityAlert');
    if(static_margin > 0.05 && static_margin < 0.25) {
        alertEl.className = "mb-5 p-3 rounded text-center font-bold text-sm bg-green-100 text-green-800";
        alertEl.innerText = "設計良好: 適切な静安定性があります";
    } else if (static_margin > 0) {
        alertEl.className = "mb-5 p-3 rounded text-center font-bold text-sm bg-yellow-100 text-yellow-800";
        alertEl.innerText = "注意: 安定性がわずかです (過敏な可能性があります)";
    } else {
        alertEl.className = "mb-5 p-3 rounded text-center font-bold text-sm bg-red-100 text-red-800";
        alertEl.innerText = "設計不良: 静不安定です (重心を前にするか尾翼を大きくしてください)";
    }


    // 8. Plotly 描画

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


    // === 2D Trajectory ===
    const trace2D = {
        x: trajX, y: trajZ,
        mode: 'lines', line: {color: '#2563eb', width: 3},
        name: '軌跡'
    };
    
    // 地面
    const ground = {
        x: [Math.min(0, ...trajX), Math.max(10, ...trajX)], y: [0, 0],
        mode: 'lines', line: {color: '#16a34a', width: 2, dash:'dot'},
        name: '地面'
    };

    const layout2D = {
        margin: {l:40, r:20, t:20, b:30},
        xaxis: {title: '距離 (m)', zeroline: false},
        yaxis: {title: '高度 (m)', zeroline: false, scaleanchor: 'x', scaleratio: 1}, // 1:1比率
        showlegend: false,
        hovermode: 'closest',
        autosize: true
    };

    Plotly.react('plotArea2D', [trace2D, ground], layout2D, {responsive: true});
}

// 初期実行
window.onload = runCalculation;

</script>

{:/}
