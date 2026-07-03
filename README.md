
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Crucigrama - Matemático</title>
    <style>
        :root {
            --primary: #3498db;
            --secondary: #e67e22;
            --success: #2ecc71;
            --error: #e74c3c;
            --bg: #ecf0f1;
            --cell: clamp(40px, 12vw, 55px);
        }

        * { box-sizing: border-box; margin: 0; padding: 0; font-family: 'Comic Sans MS', 'Arial', sans-serif; }

        body {
            background-color: var(--bg);
            display: flex; flex-direction: column; align-items: center;
            min-height: 100vh; padding: 10px;
        }

        header { text-align: center; margin-bottom: 10px; color: #2c3e50; }
        .avatar { font-size: 50px; margin-bottom: 5px; }

        .stats-container {
            display: flex; gap: 20px; margin-bottom: 15px;
            background: white; padding: 10px 20px; border-radius: 50px;
            box-shadow: 0 4px 6px rgba(0,0,0,0.1);
        }
        .stat-item { font-size: 18px; font-weight: bold; color: #2c3e50; }
        #score-val { color: var(--success); font-size: 22px; }

        .game-container {
            display: flex; flex-direction: column; gap: 20px; width: 100%;
            max-width: 900px; background: white; padding: 20px;
            border-radius: 25px; box-shadow: 0 10px 25px rgba(0,0,0,0.1);
        }

        @media (min-width: 768px) { .game-container { flex-direction: row; } }

        .board {
            display: grid; grid-template-columns: repeat(6, var(--cell));
            grid-template-rows: repeat(6, var(--cell));
            gap: 5px; background: #bdc3c7; padding: 10px; border-radius: 15px;
            margin: 0 auto;
        }

        .cell { position: relative; background: white; border-radius: 8px; border: 2px solid #ddd; }
        .cell.blocked { background: #34495e; border: none; }
        .cell input {
            width: 100%; height: 100%; border: none; text-align: center;
            font-size: 24px; font-weight: bold; color: #2c3e50;
            background: transparent; outline: none;
        }
        .cell.correct { background: #d4edda; border-color: var(--success); }
        .cell-num {
            position: absolute; top: 2px; left: 4px; font-size: 11px;
            font-weight: bold; color: #7f8c8d; pointer-events: none;
        }

        .clues { flex: 1; display: flex; flex-direction: column; gap: 15px; }
        .clue-box { 
            background: #f9f9f9; padding: 15px; border-radius: 15px; 
            border-left: 8px solid var(--primary); max-height: 250px; overflow-y: auto;
        }
        .clue-item { font-size: 16px; margin-bottom: 8px; padding: 5px; border-bottom: 1px solid #eee; }

        .btn-refresh {
            margin-top: 15px; padding: 12px 30px; border: none; border-radius: 50px;
            background: var(--secondary); color: white; font-weight: bold;
            cursor: pointer; font-size: 16px; transition: 0.3s;
        }
        .btn-refresh:hover { transform: scale(1.05); }

        /* Modal */
        .modal {
            display: none; position: fixed; inset: 0; background: rgba(0,0,0,0.8);
            align-items: center; justify-content: center; z-index: 100;
        }
        .modal-content { background: white; padding: 40px; border-radius: 30px; text-align: center; width: 85%; max-width: 400px; }
     

.btn-flotante-mateken {
    position: fixed;
    bottom: 25px;
    right: 25px; 
    background-color: #FFD166;
    color: #073B4C;
    padding: 15px 22px;
    font-size: 1.1rem;
    font-weight: bold;
    text-decoration: none;
    border-radius: 50px;
    box-shadow: 0 6px 15px rgba(0,0,0,0.4);
    display: flex;
    align-items: center;
    gap: 10px;
    transition: transform 0.2s, background-color 0.2s;
    z-index: 9999;
    font-family: sans-serif;
}
.btn-flotante-mateken:hover {
    transform: scale(1.1);
    background-color: #fffde7;
}
    </style>
</head>

<body>

    <header>
        <div class="avatar">👨‍🏫</div>
        <h1>Crucigrama Matemático</h1>
        <p>¡Resuelve las sumas y restas para ganar!</p>
    </header>

    <div class="stats-container">
        <div class="stat-item">Puntaje: <span id="score-val">100</span>/100</div>
        <div class="stat-item">Errores: <span id="error-val" style="color:var(--error)">0</span></div>
    </div>

    <div class="game-container">
        <div id="board" class="board"></div>

        <div class="clues">
            <div class="clue-box">
                <h3 style="color:var(--primary); margin-bottom:10px;">Horizontales ➔</h3>
                <div id="h-clues"></div>
            </div>
            <div class="clue-box" style="border-left-color: var(--secondary);">
                <h3 style="color:var(--secondary); margin-bottom:10px;">Verticales ↓</h3>
                <div id="v-clues"></div>
            </div>
        </div>
    </div>

    <button class="btn-refresh" onclick="location.reload()">Nuevo Juego</button>

    <div id="win-modal" class="modal">
        <div class="modal-content">
            <h2 id="win-title">¡Increíble! 🌟</h2>
            <p style="font-size: 18px; margin: 15px 0;">Has completado el reto.</p>
            <div id="final-score" style="font-size: 32px; font-weight: bold; color: var(--success); margin-bottom: 20px;"></div>
            <button class="btn-refresh" style="background: var(--primary)" onclick="location.reload()">¡Otro nivel!</button>
        </div>
    </div>

    <script>
        const layout = [
            [0, 0, 1, 0, 0, 0],
            [0, 1, 0, 0, 1, 0],
            [0, 0, 0, 1, 0, 0],
            [1, 0, 1, 0, 0, 1],
            [0, 0, 0, 0, 1, 0],
            [0, 0, 1, 0, 0, 0]
        ];

        let solution = [];
        let score = 100;
        let errors = 0;
        let audioCtx = new (window.AudioContext || window.webkitAudioContext)();

        function sound(freq, type = 'sine', duration = 0.2) {
            const osc = audioCtx.createOscillator();
            const gain = audioCtx.createGain();
            osc.type = type;
            osc.frequency.setValueAtTime(freq, audioCtx.currentTime);
            gain.gain.setValueAtTime(0.1, audioCtx.currentTime);
            gain.gain.exponentialRampToValueAtTime(0.01, audioCtx.currentTime + duration);
            osc.connect(gain); gain.connect(audioCtx.destination);
            osc.start(); osc.stop(audioCtx.currentTime + duration);
        }

        function getMathProblem(targetValue) {
            const isAddition = Math.random() > 0.5;
            if (isAddition) {
                const a = Math.floor(Math.random() * (targetValue - 5)) + 2;
                const b = targetValue - a;
                return `${a} + ${b}`;
            } else {
                const b = Math.floor(Math.random() * 40) + 10;
                const a = targetValue + b;
                return `${a} - ${b}`;
            }
        }

        function getWords() {
            let wordsH = [], wordsV = [], count = 1;
            let numbered = Array(6).fill().map(() => Array(6).fill(0));

            for(let r=0; r<6; r++) {
                for(let c=0; c<6; c++) {
                    if(layout[r][c] === 1) continue;
                    let isH = (c === 0 || layout[r][c-1] === 1) && (c < 5 && layout[r][c+1] === 0);
                    let isV = (r === 0 || layout[r-1][c] === 1) && (r < 5 && layout[r+1][c] === 0);
                    
                    if(isH || isV) {
                        numbered[r][c] = count;
                        if(isH) {
                            let len = 0; while(c+len < 6 && layout[r][c+len] === 0) len++;
                            wordsH.push({ r, c, len, num: count });
                        }
                        if(isV) {
                            let len = 0; while(r+len < 6 && layout[r+len][c] === 0) len++;
                            wordsV.push({ r, c, len, num: count });
                        }
                        count++;
                    }
                }
            }
            return { wordsH, wordsV, numbered };
        }

        function initGame() {
            const boardEl = document.getElementById('board');
            const hCluesEl = document.getElementById('h-clues');
            const vCluesEl = document.getElementById('v-clues');
            
            // Generar matriz de solución aleatoria
            solution = Array(6).fill().map(() => Array(6).fill(null).map(() => Math.floor(Math.random()*9)+1));
            const { wordsH, wordsV, numbered } = getWords();

            for(let r=0; r<6; r++) {
                for(let c=0; c<6; c++) {
                    const cell = document.createElement('div');
                    cell.className = 'cell' + (layout[r][c] === 1 ? ' blocked' : '');
                    if(layout[r][c] === 0) {
                        if(numbered[r][c] > 0) {
                            const num = document.createElement('span');
                            num.className = 'cell-num'; num.innerText = numbered[r][c];
                            cell.appendChild(num);
                        }
                        const input = document.createElement('input');
                        input.type = 'tel'; input.maxLength = 1;
                        input.dataset.r = r; input.dataset.c = c;
                        input.oninput = (e) => checkCell(e.target);
                        cell.appendChild(input);
                    }
                    boardEl.appendChild(cell);
                }
            }

            wordsH.forEach(w => {
                let valStr = ""; for(let i=0; i<w.len; i++) valStr += solution[w.r][w.c+i];
                hCluesEl.innerHTML += `<div class="clue-item"><strong>${w.num}:</strong> ${getMathProblem(parseInt(valStr))}</div>`;
            });
            wordsV.forEach(w => {
                let valStr = ""; for(let i=0; i<w.len; i++) valStr += solution[w.r+i][w.c];
                vCluesEl.innerHTML += `<div class="clue-item"><strong>${w.num}:</strong> ${getMathProblem(parseInt(valStr))}</div>`;
            });
        }

        function checkCell(input) {
            const r = input.dataset.r, c = input.dataset.c;
            if(input.value === solution[r][c].toString()) {
                input.parentElement.classList.add('correct');
                input.readOnly = true;
                sound(800, 'sine', 0.1);
                
                // Mover foco
                const inputs = Array.from(document.querySelectorAll('input:not([readonly])'));
                if(inputs[0]) inputs[0].focus();
            } else if(input.value !== "") {
                sound(200, 'square', 0.2);
                errors++;
                score = Math.max(0, 100 - (errors * 4)); // Cada error quita 4 puntos
                input.value = "";
                document.getElementById('error-val').innerText = errors;
                document.getElementById('score-val').innerText = score;
            }

            if(document.querySelectorAll('input:not([readonly])').length === 0) {
                showWin();
            }
        }

        function showWin() {
            sound(523, 'sine', 0.3);
            setTimeout(() => sound(659, 'sine', 0.3), 150);
            setTimeout(() => sound(783, 'sine', 0.5), 300);
            
            const title = score > 80 ? "¡Excelente Trabajo! 🏆" : (score > 50 ? "¡Bien hecho! 👍" : "¡Sigue practicando! 💪");
            document.getElementById('win-title').innerText = title;
            document.getElementById('final-score').innerText = `${score} / 100`;
            document.getElementById('win-modal').style.display = 'flex';
        }

        window.onload = () => {
            document.body.onclick = () => audioCtx.resume();
            initGame();
        };
    </script>
    <a href="https://matheken2.netlify.app/" target="_blank" class="btn-flotante-mateken">
    🤖 Ir a MateKen2
     </a>

</body>
</html>
