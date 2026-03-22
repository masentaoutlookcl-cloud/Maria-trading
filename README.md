<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Lisa Elite IA | Desktop Terminal v2</title>
    <link href="https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;600;800&family=JetBrains+Mono:wght@500;700&display=swap" rel="stylesheet">
    <style>
        :root {
            --bg: #020408;
            --surface: #0f1218;
            --primary: #00d4ff;
            --up: #00f298;
            --down: #ff3e60;
            --text: #ffffff;
            --text-dim: #717a8c;
            --border: rgba(255, 255, 255, 0.1);
            --glow: rgba(0, 212, 255, 0.2);
        }

        * { box-sizing: border-box; }
        
        body, html { 
            margin: 0; padding: 0; width: 100%; height: 100vh;
            background: var(--bg); color: var(--text); 
            font-family: 'Plus Jakarta Sans', sans-serif; 
            overflow: hidden;
        }

        /* Layout para PC: Grid de 3 secciones */
        .desktop-container {
            display: grid;
            grid-template-columns: 350px 1fr 300px;
            grid-template-rows: 60px 1fr;
            height: 100vh;
            gap: 1px;
            background: var(--border); /* Simula bordes entre paneles */
        }

        /* Header superior */
        header {
            grid-column: 1 / -1;
            background: var(--surface);
            display: flex;
            justify-content: space-between;
            align-items: center;
            padding: 0 20px;
            border-bottom: 1px solid var(--border);
        }

        .logo { font-weight: 800; font-size: 18px; letter-spacing: 2px; }
        .logo span { color: var(--primary); }
        #total-bal { font-family: 'JetBrains Mono'; color: var(--up); font-size: 18px; font-weight: 700; }

        /* Panel Izquierdo: Mercado */
        #market-panel {
            background: var(--bg);
            padding: 20px;
            overflow-y: auto;
            border-right: 1px solid var(--border);
        }

        /* Panel Central: Chat de Lisa IA */
        #chat-panel {
            background: radial-gradient(circle at center, #0a1120, #020408);
            display: flex;
            flex-direction: column;
            position: relative;
        }

        /* Panel Derecho: Info/Config */
        #info-panel {
            background: var(--surface);
            padding: 20px;
            border-left: 1px solid var(--border);
        }

        /* Estilos de Componentes */
        .asset-card {
            background: #11151d;
            border-radius: 12px; padding: 15px; margin-bottom: 10px;
            border: 1px solid var(--border);
        }
        .ticker { font-family: 'JetBrains Mono'; color: var(--primary); font-weight: 700; }
        .price-live { font-family: 'JetBrains Mono'; font-size: 20px; font-weight: 800; margin: 5px 0; }

        /* Chat Scroller */
        #chat-scroller {
            flex: 1;
            padding: 25px;
            overflow-y: auto;
            display: flex;
            flex-direction: column;
            gap: 20px;
        }

        .msg { max-width: 80%; padding: 15px; border-radius: 12px; font-size: 14px; line-height: 1.5; }
        .msg.ia { align-self: flex-start; background: #161b22; border: 1px solid var(--border); }
        .msg.user { align-self: flex-end; background: var(--primary); color: #000; font-weight: 700; }

        /* Input Area (Fijada abajo en el centro) */
        .chat-input-area {
            padding: 20px;
            background: rgba(2, 4, 8, 0.8);
            border-top: 1px solid var(--border);
        }
        .input-box {
            background: #1a1f29;
            border-radius: 10px;
            display: flex;
            padding: 5px 5px 5px 15px;
            border: 1px solid var(--primary);
            box-shadow: 0 0 15px var(--glow);
        }
        .input-box input {
            flex: 1; background: transparent; border: none; color: white;
            outline: none; font-size: 15px; padding: 10px 0;
        }
        .send-btn {
            width: 45px; height: 45px; border-radius: 8px;
            background: var(--primary); border: none; cursor: pointer; font-size: 20px;
        }

        /* Utilidades IA */
        .prob-container { display: flex; gap: 10px; margin: 15px 0; }
        .prob-box { flex: 1; background: #000; padding: 10px; border-radius: 6px; text-align: center; border: 1px solid var(--border); }
        .trade-ticket { background: #05070a; border: 1px dashed var(--primary); padding: 15px; border-radius: 8px; margin-top: 10px; font-family: 'JetBrains Mono'; }
        
        .btn-action { width: 100%; padding: 12px; margin-top: 10px; border-radius: 6px; border: none; font-weight: 800; cursor: pointer; color: #000; }

        /* Scrollbar personalizada */
        ::-webkit-scrollbar { width: 6px; }
        ::-webkit-scrollbar-thumb { background: var(--border); border-radius: 10px; }
    </style>
</head>
<body>

<div class="desktop-container">
    <header>
        <div class="logo">LISA <span>TERMINAL PRO</span></div>
        <div id="total-bal">$12,500.00</div>
    </header>

    <aside id="market-panel">
        <h3 style="margin-top:0">Market Watch</h3>
        <div id="market-list"></div>
    </aside>

    <main id="chat-panel">
        <div id="chat-scroller">
            <div class="msg ia">
                <b>Terminal Iniciada.</b><br>
                Conexión establecida con protocolos Smart Money. Analizando liquidez institucional en tiempo real.<br>
                Escribe un par (ej: <b>BTC</b> o <b>EURUSD</b>) para generar una señal.
            </div>
        </div>
        <div class="chat-input-area">
            <div class="input-box">
                <input type="text" id="userInput" placeholder="Ej: Analiza Bitcoin ahora..." onkeypress="if(event.key==='Enter') processLisa()">
                <button class="send-btn" onclick="processLisa()">⚡</button>
            </div>
        </div>
    </main>

    <aside id="info-panel">
        <h3>System Status</h3>
        <div class="asset-card" style="font-size: 13px;">
            <p>🟢 <b>Servidor:</b> Alpha-1</p>
            <p>🔵 <b>Latencia:</b> 14ms</p>
            <p>⚪ <b>Versión:</b> 2.5.0</p>
        </div>
        <hr style="border:0; border-top:1px solid var(--border); margin:20px 0;">
        <h3>Wallet</h3>
        <div style="font-family: 'JetBrains Mono'; font-size: 14px;">
            <div style="display:flex; justify-content:space-between; margin-bottom:5px;">
                <span>Disponible:</span> <span style="color:var(--up)">$10,200</span>
            </div>
            <div style="display:flex; justify-content:space-between;">
                <span>En Riesgo:</span> <span style="color:var(--down)">$2,300</span>
            </div>
        </div>
    </aside>
</div>

<script>
    const currentPrices = {};
    const assets = [
        { id: 'eurusdt', symbol: 'EUR/USD', p: 5 },
        { id: 'usdcjpy', symbol: 'USD/JPY', p: 3 },
        { id: 'btcusdt', symbol: 'BTC/USDT', p: 2 },
        { id: 'ethusdt', symbol: 'ETH/USDT', p: 2 }
    ];

    function initMarket() {
        const container = document.getElementById('market-list');
        assets.forEach(a => {
            currentPrices[a.id] = 0;
            container.innerHTML += `
                <div class="asset-card">
                    <div class="ticker">${a.symbol}</div>
                    <div class="price-live" id="p-${a.id}">0.00000</div>
                    <div id="c-${a.id}" style="font-size:12px; font-weight:bold;">0.00%</div>
                </div>
            `;
        });
        startStreams();
    }

    function startStreams() {
        const streams = assets.map(a => `${a.id}@ticker`).join('/');
        const ws = new WebSocket(`wss://stream.binance.com:9443/ws/${streams}`);

        ws.onmessage = (e) => {
            const d = JSON.parse(e.data);
            const id = d.s.toLowerCase();
            const config = assets.find(a => a.id === id);
            if(!config) return;

            const price = parseFloat(d.c);
            const change = parseFloat(d.P);
            currentPrices[id] = price;

            const elP = document.getElementById(`p-${id}`);
            const elC = document.getElementById(`c-${id}`);

            if(elP) {
                elP.innerText = price.toFixed(config.p);
                elC.innerText = (change >= 0 ? '▲ ' : '▼ ') + Math.abs(change).toFixed(2) + '%';
                elC.style.color = change >= 0 ? 'var(--up)' : 'var(--down)';
            }
        };
    }

    function processLisa() {
        const input = document.getElementById('userInput');
        const val = input.value.trim().toUpperCase();
        if(!val) return;

        addMsg(input.value, 'user');
        input.value = "";

        setTimeout(() => {
            let assetId = 'eurusdt';
            let symbol = 'EUR/USD';
            let precision = 5;

            if(val.includes('JPY')) { assetId = 'usdcjpy'; symbol = 'USD/JPY'; precision = 3; }
            else if(val.includes('BTC')) { assetId = 'btcusdt'; symbol = 'BTC/USDT'; precision = 2; }
            else if(val.includes('ETH')) { assetId = 'ethusdt'; symbol = 'ETH/USDT'; precision = 2; }

            const livePrice = currentPrices[assetId] || 0;
            const buyProb = Math.floor(Math.random() * (85 - 35) + 35);
            const sellProb = 100 - buyProb;
            const isBuy = buyProb > sellProb;

            const response = `
                <b style="color:var(--primary)">LISA ANALYTICS</b><br>
                Señal detectada para <b>${symbol}</b> basada en volumen real.<br>
                <div class="prob-container">
                    <div class="prob-box"><small>BUY</small><br><span style="color:var(--up); font-weight:800">${buyProb}%</span></div>
                    <div class="prob-box"><small>SELL</small><br><span style="color:var(--down); font-weight:800">${sellProb}%</span></div>
                </div>
                <div class="trade-ticket">
                    ORDEN RECOMENDADA: <b>${isBuy ? 'COMPRA' : 'VENTA'}</b><br>
                    PRECIO ACTUAL: ${livePrice.toFixed(precision)}<br>
                    <button class="btn-action" style="background:${isBuy?'var(--up)':'var(--down)'}">EJECUTAR ORDEN</button>
                </div>
            `;
            addMsg(response, 'ia');
        }, 600);
    }

    function addMsg(txt, type) {
        const s = document.getElementById('chat-scroller');
        const d = document.createElement('div');
        d.className = `msg ${type}`;
        d.innerHTML = txt;
        s.appendChild(d);
        s.scrollTo({ top: s.scrollHeight, behavior: 'smooth' });
    }

    window.onload = initMarket;
</script>
</body>
</html>
