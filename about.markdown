---
layout: default
title: About
---

<script src="https://cdn.plot.ly/plotly-2.24.1.min.js"></script>
<link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">

<div class="container-fluid py-4">
<h1 class="mb-4">翼型プロパティ計算機</h1>
<div class="row">
<div class="col-lg-7 mb-4">
<div class="card h-100 shadow-sm">
<div class="card-header bg-primary text-white">
<h5 class="mb-0">解析結果 & 形状プレビュー</h5>
</div>
<div class="card-body">
<div id="airfoilPlot" style="width:100%; height:400px;"></div>
<hr>
<h5 class="mt-4">幾何特性</h5>
<div class="table-responsive">
<table class="table table-bordered table-striped">
<thead class="table-light">
<tr>
    <th>項目</th>
    <th>値 (正規化 0-1)</th>
    <th>値 (実寸)</th>
    <th>単位 (実寸)</th>
</tr>
</thead>
<tbody id="resultBody">
<tr><td colspan="4" class="text-center text-muted">データを入力して計算ボタンを押してください</td></tr>
</tbody>
</table>
</div>
</div>
</div>
</div>
<div class="col-lg-5">
<div class="card shadow-sm">
<div class="card-header bg-dark text-white">
<h5 class="mb-0">パラメータ入力</h5>
</div>
<div class="card-body">
<div class="mb-3">
<label for="chordLength" class="form-label fw-bold">コード長 (Chord Length)</label>
<div class="input-group">
<input type="number" class="form-control" id="chordLength" value="1000" min="0" step="any">
<span class="input-group-text">mm</span>
</div>
<div class="form-text">結果の実寸換算に使用します。</div>
</div>
<hr>
<div class="mb-2">
<label for="pointCloud" class="form-label fw-bold">点群データ (x y)</label>
<div class="alert alert-info py-2" style="font-size: 0.9rem;">
<strong>フォーマット:</strong><br>
点群は <strong>後縁 (x=1)</strong> から始まり、
<strong>上面 (y>0)</strong> → <strong>前縁 (x=0)</strong> → <strong>下面 (y<0)</strong> を通り、
再び <strong>後縁</strong> で終わる順序にしてください。
<br>※各行に「x座標 y座標」をスペース区切りで入力。
</div>
<textarea class="form-control font-monospace" id="pointCloud" rows="12" placeholder="1.0000 0.0000&#10;0.9500 0.0123&#10;..."></textarea>
</div>
<div class="d-grid gap-2 d-md-flex justify-content-md-end mt-3">
<button class="btn btn-outline-secondary btn-sm me-md-auto" onclick="loadSample()">サンプル(NACA2412)</button>
<button class="btn btn-warning" onclick="normalizeAndUpdate()">正規化 (0-1化)</button>
<button class="btn btn-primary fw-bold px-4" onclick="calculateProperties()">計算・描画</button>
</div>
</div>
</div>
</div>
</div>
</div>

<script>
// 初期化
document.addEventListener('DOMContentLoaded', () => {
    // 空のグラフを表示しておく
    Plotly.newPlot('airfoilPlot', [], {
        title: 'Airfoil Shape',
        xaxis: { title: 'x/c', range: [-0.1, 1.1], zeroline: true },
        yaxis: { title: 'y/c', scaleanchor: "x", scaleratio: 1, zeroline: true },
        margin: { t: 40, r: 20, b: 40, l: 50 }
    });
});

    // サンプルデータのロード (NACA 2412 近似)
    function loadSample() {
        const sampleData = `1.00000 0.00126
0.95006 0.01136
0.90013 0.02075
0.80025 0.03787
0.70038 0.05294
0.60049 0.06543
0.50060 0.07470
0.40069 0.07996
0.30075 0.07997
0.25076 0.07739
0.20077 0.07254
0.15076 0.06486
0.10073 0.05362
0.07570 0.04618
0.05066 0.03706
0.02560 0.02534
0.01256 0.01766
0.00000 0.00000
0.01244 -0.01566
0.02540 -0.02266
0.05034 -0.03194
0.07529 -0.03818
0.10027 -0.04238
0.15024 -0.04714
0.20023 -0.04846
0.25024 -0.04761
0.30025 -0.04547
0.40029 -0.03904
0.50036 -0.03080
0.60045 -0.02197
0.70054 -0.01356
0.80063 -0.00633
0.90071 -0.00125
0.95075 -0.00010
1.00000 -0.00126`;
        document.getElementById('pointCloud').value = sampleData;
        calculateProperties();
    }

    // テキストエリアのパース
    function parsePoints(text) {
        const lines = text.trim().split(/\n+/);
        const points = [];
        for (let line of lines) {
            const parts = line.trim().split(/\s+/);
            if (parts.length >= 2) {
                const x = parseFloat(parts[0]);
                const y = parseFloat(parts[1]);
                if (!isNaN(x) && !isNaN(y)) {
                    points.push({ x, y });
                }
            }
        }
        return points;
    }

    // 正規化処理 (前縁を0, 後縁を1に合わせる回転・スケーリング)
    function normalizeAndUpdate() {
        let points = parsePoints(document.getElementById('pointCloud').value);
        if (points.length < 3) {
            alert("データが不足しています。");
            return;
        }

        // 1. 最も左にある点(min x)を前縁(LE)と仮定、最も右(max x)を後縁(TE)と仮定してシフト
        // ※厳密には最長距離ペアを探すべきですが、簡易的にx座標で判断します
        let minX = Infinity, maxX = -Infinity;
        let leIdx = -1, teIdx = -1;

        points.forEach((p, i) => {
            if (p.x < minX) { minX = p.x; leIdx = i; }
            if (p.x > maxX) { maxX = p.x; teIdx = i; }
        });

        // シフト (LEを原点へ)
        const leP = { ...points[leIdx] };
        points = points.map(p => ({ x: p.x - leP.x, y: p.y - leP.y }));

        // 回転 (TEがx軸上にくるように)
        // 現在のTE座標
        const currentTE = points[teIdx]; 
        const angle = Math.atan2(currentTE.y, currentTE.x);
        const cosA = Math.cos(-angle);
        const sinA = Math.sin(-angle);

        points = points.map(p => ({
            x: p.x * cosA - p.y * sinA,
            y: p.x * sinA + p.y * cosA
        }));

        // スケーリング (弦長を1に)
        const newChord = points[teIdx].x; // 回転後のTEのx座標が弦長
        if (newChord !== 0) {
            points = points.map(p => ({ x: p.x / newChord, y: p.y / newChord }));
        }

        // テキストボックスに書き戻し
        const newText = points.map(p => `${p.x.toFixed(5)} ${p.y.toFixed(5)}`).join("\n");
        document.getElementById('pointCloud').value = newText;
        
        // 再計算
        calculateProperties();
    }

    // ポリゴン特性計算 (Shoelace formula, etc.)
    function calculateProperties() {
        const text = document.getElementById('pointCloud').value;
        const chordLen = parseFloat(document.getElementById('chordLength').value) || 1000;
        let points = parsePoints(text);

        if (points.length < 3) {
            alert("有効な点群データを入力してください（3点以上）。");
            return;
        }

        // 閉合チェック (始点と終点がずれていたら閉じる)
        const first = points[0];
        const last = points[points.length - 1];
        const dist = Math.sqrt((first.x - last.x)**2 + (first.y - last.y)**2);
        if (dist > 1e-4) {
            points.push({ x: first.x, y: first.y });
        }

        // --- 計算処理 (正規化座標系での計算) ---
        let area = 0;
        let Sx = 0; // 断面一次モーメント y
        let Sy = 0; // 断面一次モーメント x
        let Ixx_raw = 0;
        let Iyy_raw = 0;
        let perimeter = 0;
        let maxThick = 0;
        let maxCamber = 0;

        // 幾何特性 (Green's Theorem / Shoelace Formula)
        // 参考: 任意の多角形の面積・重心・慣性モーメント
        for (let i = 0; i < points.length - 1; i++) {
            const p1 = points[i];
            const p2 = points[i+1];
            
            const cross = (p1.x * p2.y - p2.x * p1.y);
            area += cross;
            
            Sy += (p1.x + p2.x) * cross;
            Sx += (p1.y + p2.y) * cross;

            Ixx_raw += (p1.y**2 + p1.y * p2.y + p2.y**2) * cross;
            Iyy_raw += (p1.x**2 + p1.x * p2.x + p2.x**2) * cross;

            perimeter += Math.sqrt((p2.x - p1.x)**2 + (p2.y - p1.y)**2);
        }

        area = area * 0.5;
        // 反時計回りの場合エリアがプラス、時計回りの場合マイナスになるため絶対値をとる
        // 点群の入力順序指定(後縁→上面→前縁→下面)だと、通常は時計回りなのでマイナスになりうる
        const sign = Math.sign(area);
        area = Math.abs(area);

        // 重心位置
        const cx = (Sy / 6) / (area * sign); // 符号を考慮して割る
        const cy = (Sx / 6) / (area * sign);

        // 重心回りの断面二次モーメント
        // I_centroid = I_raw/12 - Area * d^2  (平行軸の定理の逆)
        // 多角形公式では、Ixx_raw は原点周りだが係数が1/12
        let Ixx = Math.abs(Ixx_raw / 12) - area * cy * cy;
        let Iyy = Math.abs(Iyy_raw / 12) - area * cx * cx;

        // 簡易的な最大翼厚とキャンバーの算出
        // 上面と下面をx座標でリサンプリングして差分を取るのが正確ですが、
        // ここでは簡易的に、Yの最大値-最小値を翼厚、(Ymax+Ymin)/2 の最大偏差をキャンバーと推定します
        // ※入力がx軸に平行に正規化されている前提です
        let minY = Infinity, maxY = -Infinity;
        points.forEach(p => {
            if (p.y < minY) minY = p.y;
            if (p.y > maxY) maxY = p.y;
        });
        maxThick = maxY - minY; // 簡易計算
        // キャンバーは正確な算出が難しい(Mean Lineが必要)ため、ここでは省略または幾何的中心とのズレを表示

        // --- 結果表示 ---
        const resultBody = document.getElementById('resultBody');
        resultBody.innerHTML = `
            ${createRow("面積 (Area)", area, area * (chordLen**2), "mm²")}
            ${createRow("周長 (Perimeter)", perimeter, perimeter * chordLen, "mm")}
            ${createRow("重心 X (x_cg)", cx, cx * chordLen, "mm")}
            ${createRow("重心 Y (y_cg)", cy, cy * chordLen, "mm")}
            ${createRow("最大翼厚 (Max Thickness)", maxThick, maxThick * chordLen, "mm (approx)")}
            ${createRow("断面二次モーメント Ixx", Ixx, Ixx * (chordLen**4), "mm⁴")}
            ${createRow("断面二次モーメント Iyy", Iyy, Iyy * (chordLen**4), "mm⁴")}
        `;

        // --- グラフ更新 ---
        const xVals = points.map(p => p.x);
        const yVals = points.map(p => p.y);

        const trace = {
            x: xVals,
            y: yVals,
            mode: 'lines+markers',
            name: 'Airfoil',
            line: {color: '#0d6efd', width: 2},
            marker: {size: 3}
        };

        const layout = {
            title: '翼型形状',
            xaxis: { title: 'x/c', zeroline: true },
            yaxis: { title: 'y/c', scaleanchor: "x", scaleratio: 1, zeroline: true }, // アスペクト比固定
            margin: { t: 30, r: 20, b: 40, l: 50 },
            showlegend: false
        };

        Plotly.newPlot('airfoilPlot', [trace], layout);
    }

    function createRow(label, normVal, realVal, unit) {
        // 数値整形
        const nV = normVal.toExponential(4);
        // 実寸は桁数によって指数表示を切り替え
        let rV = realVal.toFixed(2);
        if (Math.abs(realVal) > 10000 || (Math.abs(realVal) < 0.01 && realVal !== 0)) {
            rV = realVal.toExponential(3);
        }

        return `
            <tr>
                <td>${label}</td>
                <td class="font-monospace">${nV}</td>
                <td class="font-monospace fw-bold">${rV}</td>
                <td>${unit}</td>
            </tr>
        `;
    }
</script>
