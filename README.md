# metodos-numericos
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Factorización LU - Estilo Docente</title>
    <style>
        :root {
            --purple-main: #5e35b1;
            --bg-light: #f0f4f8;
            --card-bg: #ffffff;
            --input-border: #d1d9e6;
            --accent-green: #e8f5e9;
            --text-dark: #333;
        }

        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            background-color: var(--bg-light);
            margin: 0;
            color: var(--text-dark);
        }

        /* Banner Principal */
        .header-banner {
            background-color: var(--purple-main);
            color: white;
            padding: 40px 20px;
            text-align: center;
            box-shadow: 0 4px 10px rgba(0,0,0,0.1);
        }

        .header-banner h1 {
            margin: 0;
            font-size: 2.5rem;
            letter-spacing: 2px;
            text-transform: uppercase;
        }

        .header-banner p {
            margin-top: 10px;
            font-style: italic;
            opacity: 0.9;
        }

        .main-container {
            max-width: 1000px;
            margin: -30px auto 50px;
            padding: 0 20px;
        }

        /* Paneles de Configuración */
        .config-grid {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 20px;
            margin-bottom: 30px;
        }

        .card {
            background: var(--card-bg);
            padding: 25px;
            border-radius: 12px;
            box-shadow: 0 4px 15px rgba(0,0,0,0.05);
            border: 1px solid #e0e0e0;
        }

        .card label {
            display: block;
            margin-bottom: 10px;
            font-weight: bold;
            color: var(--purple-main);
        }

        .card input, .card select {
            width: 100%;
            padding: 12px;
            border: 1px solid var(--input-border);
            border-radius: 8px;
            font-size: 1rem;
            box-sizing: border-box;
        }

        /* Área de Matriz */
        .matrix-section {
            background: white;
            padding: 30px;
            border-radius: 12px;
            box-shadow: 0 4px 15px rgba(0,0,0,0.05);
        }

        .matrix-section h2 {
            margin-top: 0;
            font-size: 1.5rem;
            border-bottom: 2px solid #f0f0f0;
            padding-bottom: 10px;
        }

        .matrix-grid-container {
            display: flex;
            justify-content: center;
            margin: 30px 0;
            overflow-x: auto;
        }

        .matrix-table {
            border-spacing: 10px;
        }

        .matrix-input {
            width: 60px;
            height: 60px;
            text-align: center;
            font-size: 1.2rem;
            border: 1px solid var(--input-border);
            border-radius: 8px;
            transition: all 0.2s;
        }

        .matrix-input:focus {
            border-color: var(--purple-main);
            outline: none;
            box-shadow: 0 0 8px rgba(94, 53, 177, 0.2);
        }

        .vector-b {
            background-color: var(--accent-green);
            border-color: #a5d6a7;
            font-weight: bold;
        }

        .btn-solve {
            display: block;
            width: 250px;
            margin: 30px auto 0;
            padding: 15px;
            background-color: var(--purple-main);
            color: white;
            border: none;
            border-radius: 30px;
            font-size: 1.1rem;
            font-weight: bold;
            cursor: pointer;
            transition: 0.3s;
        }

        .btn-solve:hover {
            background-color: #4527a0;
            transform: translateY(-2px);
        }

        /* Resultados */
        .results-area {
            margin-top: 40px;
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
            gap: 20px;
        }

        .res-table {
            width: 100%;
            border-collapse: collapse;
            margin-top: 10px;
        }

        .res-table td {
            border: 1px solid #eee;
            padding: 10px;
            text-align: center;
            font-family: 'Courier New', monospace;
            background: #fafafa;
        }

        .sol-list {
            list-style: none;
            padding: 0;
            font-size: 1.3rem;
            color: #2e7d32;
        }
    </style>
</head>
<body>

    <div class="header-banner">
        <h1>FACTORIZACIÓN LU Y RESOLUCIÓN DE SISTEMAS</h1>
        <p>Doolittle, Crout y Cholesky</p>
    </div>

    <div class="main-container">
        <div class="config-grid">
            <div class="card">
                <label>Dimensión del Sistema (n x n)</label>
                <input type="number" id="nSize" value="3" min="2" max="6" onchange="renderMatrix()">
            </div>
            <div class="card">
                <label>Método de Factorización</label>
                <select id="methodType">
                    <option value="doolittle">Doolittle (L con 1s en la diagonal)</option>
                    <option value="crout">Crout (U con 1s en la diagonal)</option>
                    <option value="cholesky">Cholesky (A = LLᵀ)</option>
                </select>
            </div>
        </div>

        <div class="matrix-section">
            <h2>Ingrese la Matriz A y el Vector B</h2>
            <div id="matrixWrapper" class="matrix-grid-container">
                </div>
            <button class="btn-solve" onclick="calculateLU()">RESOLVER SISTEMA</button>
        </div>

        <div id="resultsOutput" class="results-area"></div>
    </div>

<script>
    function renderMatrix() {
        const n = parseInt(document.getElementById('nSize').value);
        let html = '<table class="matrix-table">';
        for (let i = 0; i < n; i++) {
            html += '<tr>';
            for (let j = 0; j < n; j++) {
                html += `<td><input type="number" step="any" class="matrix-input" id="a_${i}_${j}"></td>`;
            }
            html += '<td style="width: 20px;"></td>';
            html += `<td><input type="number" step="any" class="matrix-input vector-b" id="b_${i}"></td>`;
            html += '</tr>';
        }
        html += '</table>';
        document.getElementById('matrixWrapper').innerHTML = html;
        document.getElementById('resultsOutput').innerHTML = '';
    }

    function calculateLU() {
        const n = parseInt(document.getElementById('nSize').value);
        const method = document.getElementById('methodType').value;
        let A = [], b = [], L = Array.from({length: n}, () => Array(n).fill(0)), U = Array.from({length: n}, () => Array(n).fill(0));

        try {
            for (let i = 0; i < n; i++) {
                A[i] = [];
                b[i] = parseFloat(document.getElementById(`b_${i}`).value) || 0;
                for (let j = 0; j < n; j++) A[i][j] = parseFloat(document.getElementById(`a_${i}_${j}`).value) || 0;
            }

            if (method === 'doolittle') {
                for (let i = 0; i < n; i++) {
                    L[i][i] = 1;
                    for (let k = i; k < n; k++) {
                        let s = 0; for (let j = 0; j < i; j++) s += L[i][j] * U[j][k];
                        U[i][k] = A[i][k] - s;
                    }
                    for (let k = i + 1; k < n; k++) {
                        let s = 0; for (let j = 0; j < i; j++) s += L[k][j] * U[j][i];
                        L[k][i] = (A[k][i] - s) / U[i][i];
                    }
                }
            } else if (method === 'crout') {
                for (let i = 0; i < n; i++) {
                    U[i][i] = 1;
                    for (let k = i; k < n; k++) {
                        let s = 0; for (let j = 0; j < i; j++) s += L[k][j] * U[j][i];
                        L[k][i] = A[k][i] - s;
                    }
                    for (let k = i + 1; k < n; k++) {
                        let s = 0; for (let j = 0; j < i; j++) s += L[i][j] * U[j][k];
                        U[i][k] = (A[i][k] - s) / L[i][i];
                    }
                }
            } else if (method === 'cholesky') {
                for (let i = 0; i < n; i++) {
                    for (let j = 0; j <= i; j++) {
                        let s = 0; for (let k = 0; k < j; k++) s += L[i][k] * L[j][k];
                        if (i === j) L[i][j] = Math.sqrt(A[i][i] - s);
                        else L[i][j] = (A[i][j] - s) / L[j][j];
                    }
                }
                for(let i=0; i<n; i++) for(let j=0; j<n; j++) U[i][j] = L[j][i];
            }

            // Solución de sistemas
            let y = Array(n).fill(0), x = Array(n).fill(0);
            for(let i=0; i<n; i++){
                let s = 0; for(let j=0; j<i; j++) s += L[i][j] * y[j];
                y[i] = (b[i] - s) / L[i][i];
            }
            for(let i=n-1; i>=0; i--){
                let s = 0; for(let j=i+1; j<n; j++) s += U[i][j] * x[j];
                x[i] = (y[i] - s) / U[i][i];
            }

            renderOutput(L, U, x, n);
        } catch (e) { alert("Error matemático: " + e.message); }
    }

    function renderOutput(L, U, x, n) {
        let html = `
            <div class="card"><h3>Matriz L</h3><table class="res-table">${tableBody(L, n)}</table></div>
            <div class="card"><h3>Matriz U</h3><table class="res-table">${tableBody(U, n)}</table></div>
            <div class="card"><h3>Solución Vector X</h3><ul class="sol-list">
                ${x.map((val, i) => `<li><b>x${i+1} =</b> ${val.toFixed(4)}</li>`).join('')}
            </ul></div>
        `;
        document.getElementById('resultsOutput').innerHTML = html;
    }

    function tableBody(M, n) {
        return M.map(row => `<tr>${row.map(v => `<td>${v.toFixed(3)}</td>`).join('')}</tr>`).join('');
    }

    renderMatrix();
</script>
</body>
</html>
