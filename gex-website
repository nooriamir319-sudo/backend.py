<!DOCTYPE html>
<html lang="de">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>S&P 500 GEX Dashboard</title>
    <script src="https://cdn.jsdelivr.net/npm/chart.js@4.4.0/dist/chart.umd.min.js"></script>
    <style>
        * { margin: 0; padding: 0; box-sizing: border-box; }
        body { background: #0a0a0f; color: #e0e0e0; font-family: 'Segoe UI', sans-serif; padding: 20px; }
        .container { max-width: 900px; margin: 0 auto; }
        .header { text-align: center; margin-bottom: 30px; padding-bottom: 20px; border-bottom: 1px solid #1e1e30; }
        .header h1 { font-size: 2em; background: linear-gradient(135deg, #667eea, #764ba2); -webkit-background-clip: text; -webkit-text-fill-color: transparent; }
        .card { background: #12121a; border: 1px solid #1e1e30; border-radius: 16px; padding: 25px; margin: 15px 0; text-align: center; }
        .gex-big { font-size: 4em; font-weight: 800; }
        .positive { color: #00ff88; } .negative { color: #ff4757; } .neutral { color: #ffa502; }
        .grid-2 { display: grid; grid-template-columns: 1fr 1fr; gap: 15px; }
        .grid-4 { display: grid; grid-template-columns: 1fr 1fr 1fr 1fr; gap: 10px; }
        .metric-label { font-size: 0.75em; color: #666; text-transform: uppercase; }
        .metric-value { font-size: 1.5em; font-weight: 700; margin-top: 5px; color: #00ff88; }
        .info-box { background: #1a1a2e; border-left: 4px solid #667eea; padding: 20px; border-radius: 0 12px 12px 0; margin: 15px 0; line-height: 1.6; }
        button { width: 100%; padding: 15px; background: #667eea; border: none; border-radius: 25px; color: white; font-size: 1em; cursor: pointer; margin: 10px 0; }
        button:hover { background: #764ba2; }
        .update { text-align: center; color: #555; font-size: 0.8em; margin-top: 20px; }
        @media (max-width: 600px) { .grid-2, .grid-4 { grid-template-columns: 1fr; } .gex-big { font-size: 2.5em; } }
    </style>
</head>
<body>
    <div class="container">
        <div class="header">
            <h1>📊 S&P 500 Gamma Exposure</h1>
            <p style="color:#888; margin-top:5px;">Live Optionsdaten • SPY Proxy</p>
        </div>
        
        <div class="card">
            <p style="color:#888; font-size:0.85em;">SPY Spot Preis</p>
            <div style="font-size:2.5em; font-weight:700; color:#fff;" id="spotPrice">---</div>
        </div>
        
        <div class="card" id="gexCard">
            <p style="color:#888; font-size:0.85em;">Net Gamma Exposure (GEX)</p>
            <div class="gex-big positive" id="gexValue">Lade...</div>
            <p id="gexStatus" style="margin-top:10px; font-size:1.2em;">---</p>
        </div>
        
        <div class="grid-4">
            <div class="card">
                <p class="metric-label">Zero Gamma</p>
                <div class="metric-value" id="zeroGamma">---</div>
            </div>
            <div class="card">
                <p class="metric-label">Gamma Flip</p>
                <div class="metric-value" style="color:#ffa502;" id="gammaFlip">---</div>
            </div>
            <div class="card">
                <p class="metric-label">Call Wall</p>
                <div class="metric-value" id="callWall">---</div>
            </div>
            <div class="card">
                <p class="metric-label">Put Wall</p>
                <div class="metric-value" style="color:#ff4757;" id="putWall">---</div>
            </div>
        </div>
        
        <div class="info-box" id="interpretation">
            <h3 style="color:#667eea; margin-bottom:10px;">📖 Interpretation</h3>
            <p>Lade Daten...</p>
        </div>
        
        <button onclick="loadData()">🔄 Jetzt aktualisieren</button>
        <p class="update" id="updateTime">Letzte Aktualisierung: --</p>
    </div>

    <script>
        const API_URL = 'https://backend-py-0fq9.onrender.com/api/gex';
        
        async function loadData() {
            try {
                document.getElementById('gexValue').textContent = 'Lade...';
                const response = await fetch(API_URL);
                const data = await response.json();
                
                if (data.error) {
                    document.getElementById('gexValue').textContent = 'Fehler';
                    return;
                }
                
                // Spot Preis
                document.getElementById('spotPrice').textContent = '$' + data.spot_price.toFixed(2);
                
                // GEX Wert
                const gexEl = document.getElementById('gexValue');
                const gexBillions = data.net_gex_billions;
                gexEl.textContent = '$' + gexBillions.toFixed(2) + 'B';
                
                // Farbe
                gexEl.className = 'gex-big';
                const statusEl = document.getElementById('gexStatus');
                if (data.status === 'positive') {
                    gexEl.classList.add('positive');
                    statusEl.textContent = '✅ POSITIVER GEX - Markt stabilisierend';
                    statusEl.style.color = '#00ff88';
                } else if (data.status === 'negative') {
                    gexEl.classList.add('negative');
                    statusEl.textContent = '⚠️ NEGATIVER GEX - Hohe Volatilität';
                    statusEl.style.color = '#ff4757';
                } else {
                    gexEl.classList.add('neutral');
                    statusEl.textContent = '● NEUTRALER GEX';
                    statusEl.style.color = '#ffa502';
                }
                
                // Levels
                document.getElementById('zeroGamma').textContent = data.zero_gamma.toFixed(0);
                document.getElementById('gammaFlip').textContent = data.gamma_flip.toFixed(0);
                document.getElementById('callWall').textContent = data.call_wall ? data.call_wall.toFixed(0) : '---';
                document.getElementById('putWall').textContent = data.put_wall ? data.put_wall.toFixed(0) : '---';
                
                // Interpretation
                const spot = data.spot_price;
                const zero = data.zero_gamma;
                const flip = data.gamma_flip;
                
                let interpretation = '';
                if (data.status === 'positive') {
                    interpretation = `<strong>Positiver GEX:</strong> Market Maker sind long Gamma. 
                    Das stabilisiert den Markt. Solange SPY über <strong>${flip.toFixed(0)}</strong> bleibt, 
                    ist mit niedriger Volatilität zu rechnen. Der Zero-Gamma-Level liegt bei 
                    <strong>${zero.toFixed(0)}</strong>.`;
                } else if (data.status === 'negative') {
                    interpretation = `<strong>Negativer GEX:</strong> Market Maker sind short Gamma. 
                    <strong>Vorsicht!</strong> Bewegungen werden verstärkt. Ein Bruch unter 
                    <strong>${flip.toFixed(0)}</strong> könnte Abverkäufe beschleunigen.`;
                } else {
                    interpretation = `<strong>Neutraler GEX:</strong> Keine starke Gamma-Beeinflussung. 
                    Normale Marktbedingungen.`;
                }
                document.getElementById('interpretation').innerHTML = `
                    <h3 style="color:#667eea; margin-bottom:10px;">📖 Interpretation</h3>
                    <p>${interpretation}</p>
                `;
                
                // Zeit
                const time = new Date(data.timestamp).toLocaleTimeString('de-DE');
                document.getElementById('updateTime').textContent = 'Live-Daten • ' + time + ' Uhr';
                
            } catch (error) {
                document.getElementById('gexValue').textContent = 'Verbindungsfehler';
                console.error(error);
            }
        }
        
        // Laden beim Start
        loadData();
        
        // Alle 2 Minuten automatisch aktualisieren
        setInterval(loadData, 120000);
    </script>
</body>
</html>
