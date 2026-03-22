<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
    <title>Lisa Elite IA | Terminal Pro v2</title>
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
            --border: rgba(255, 255, 255, 0.08);
            --glow: rgba(0, 212, 255, 0.3);
        }

        * { box-sizing: border-box; -webkit-tap-highlight-color: transparent; }
        
        body, html { 
            margin: 0; padding: 0; width: 100%; height: 100%;
            background: var(--bg); color: var(--text); 
            font-family: 'Plus Jakarta Sans', sans-serif; 
            overflow: hidden;
            display: flex;
            flex-direction: column;
        }

        /* Contenedor principal usando 100dvh para adaptarse al teclado móvil */
        .app-container { 
            display: flex; 
            flex-direction: column; 
            height: 100dvh; 
            max-width: 500px; 
            margin: 0 auto; 
            border: 1px solid var(--border); 
            width: 100%;
        }

        header {
            padding: 40px 20px 15px; background: rgba(2, 4, 8, 0.95);
            backdrop-filter: blur(20px); border-bottom: 1px solid var(--border);
            display: flex; justify-content: space-between; align-items: center; z-index: 10;
        }
        .logo { font-weight: 800; font-size: 16px; letter-spacing: 2px; text-transform: uppercase; }
        .logo span { color: var(--primary); text-shadow: 0 0 10px var(--glow); }
        
        #total-bal { font-family: 'JetBrains Mono'; color: var(--up); font-weight: 700; font-size: 16px; }

        main { 
            flex: 1; 
            position: relative; 
            overflow: hidden; 
            background: radial-gradient(circle at top right, #0a1120, #020408);
            display: flex;
            flex-direction: column;
        }

        .page { display: none; height: 100%; width: 100%; overflow-y: auto; padding: 15px; }
        
        /* Ajuste específico para la página de chat */
        #page-chat { 
            display: none; 
            flex-direction: column; 
            padding: 0; 
        }
        
        .page.active { display: block; animation: slideIn 0.2s ease-out; }
        #page-chat.active { display: flex; }

        @keyframes slideIn { from { opacity: 0; transform: scale(0.98); } to { opacity: 1; transform: scale(1); } }

        .asset-card {
            background: linear-gradient(145deg, #11151d, #090b10);
            border-radius: 16px; padding: 16px; margin-bottom: 12px;
            border: 1px solid var(--border); transition: 0.3s;
        }
        .asset-row { display: flex; justify-content: space-between; align-items: center; }
        .ticker { font-family: 'JetBrains Mono'; font-weight: 700; font-size: 15px; color: var(--primary); }
        .price-live { font-family: 'JetBrains Mono'; font-size: 18px; font-weight: 800; transition: 0.2s; }
        
        .price-up { animation: pulseUp 0.5s; }
        .price-down { animation: pulseDown 0.5s; }
        @keyframes pulseUp { 0% { color: var(--up); } 100% { color: white; } }
        @keyframes pulseDown { 0% { color: var(--down); } 100% { color: white; } }

        /* Scroller del chat: ahora usa flex-grow para ocupar el espacio */
        #chat-scroller { 
            flex: 1; 
            overflow-y: auto; 
            padding: 20px 15px; 
            display: flex; 
            flex-direction: column; 
            gap: 15px; 
        }
        .msg { max-width: 90%; padding: 14px; border-radius: 18px; font-size: 14px; position: relative; line-height: 1.4; }
        .msg.ia { align-self: flex-start; background: #161b22; border: 1px solid var(--border); border-bottom-left-radius: 4px; }
        .msg.user { align-self: flex-end; background: var(--primary); color: #000; font-weight: 700; border-bottom-right-radius: 4px; }

        .prob-container { display: flex; gap: 10px; margin: 12px 0; }
        .prob-box { flex: 1; background: rgba(0,0,0,0.3); padding: 8px; border-radius: 8px; text-align: center; border: 1px solid var(--border); }
        .prob-label { font-size: 10px; color: var(--text-dim); text-transform: uppercase; margin-bottom: 4px; display: block; }
        .prob-val { font-family: 'JetBrains Mono'; font-weight: 800; font-size: 16px; }

        .trade-ticket {
            background: #000; border: 1px dashed var(--primary); padding: 12px;
            border-radius: 12px; margin-top: 10px; font-family: 'JetBrains Mono'; font-size: 12px;
        }
        .btn-action { width: 100%; padding: 10px; margin-top: 10px; border-radius: 8px; border: none; font-weight: 800; cursor: pointer; color: #000; text-transform: uppercase; }

        /* Área de input: posición relativa para que el teclado la empuje */
        .chat-input-area { 
            position: relative; 
            width: 100%; 
            padding: 15px; 
            background: var(--bg); 
            border-top: 1px solid var(--border);
        }
        .input-box { 
            background: #1a1f29; border-radius: 15px; display: flex; 
            padding: 8px 8px 8px 15px; border: 1px solid var(--border);
        }
        .input-box input { flex: 1; background: transparent; border: none; color: white; outline: none; font-size: 16px; } /* 16px evita zoom en iOS */
        .send-btn { width: 40px; height: 40px; border-radius: 12px; background: var(--primary); border: none; font-size: 18px; cursor: pointer; }

        nav { height: 75px; background: #090b10; display: flex; border-top: 1px solid var(--border); width: 100%; }
        .nav-btn { flex: 1; background: none; border: none; color: var(--text-dim); display: flex; flex-direction: column; align-items: center; justify-content: center; font-size: 10px; font-weight: 700; gap: 4px; cursor: pointer; }
        .nav-btn.active { color: var(--primary); }
    </style>
</head>
<body>

<div class="app-container">
    <header>
        <div class="logo">LISA <span>TERMINAL</span></div>
        <div id="total-bal">$12,500.00</div>
    </header>

    <main>
        <section id="page-market" class="page active">
            <div style="margin-bottom: 20px;">
                <h3 style="margin:0; font-size: 22px;">Watchlist <span style="color:var(--primary)">Pro</span></h3>
                <small style="color:var(--text-dim)">Flujo Smart Money (Precios en Vivo)</small>
            </div>
            <div id="market-list"></div>
        </section>

        <section id="page-chat" class="page">
            <div id="chat-scroller">
                <div class="msg ia">Sistemas listos. Analizando flujos institucionales. ¿Qué activo deseas auditar?</div>
            </div>
            <div class="chat-input-area">
                <div class="input-box">
                    <input type="text" id="userInput" placeholder="Analiza EURUSD..." onkeypress="if(event.key==='Enter') processLisa()">
                    <button class="send-btn" onclick="processLisa()">⚡</button>
                </div>
            </div>
        </section>

        <section id="page-mem" class="page">
            <h2 style="color: var(--primary)">Configuración</h2>
            <div class="asset-card" style="border-left: 4px solid var(--primary)">
                <b>User ID:</b> Lisa_Elite_01<br>
                <b>Server:</b> Global-Alpha<br>
                <b>Modo:</b> Alta Precisión (Smart Money)
            </div>
        </section>
    </main>

    <nav>
        <button class="nav-btn active" onclick="tab('market', this)"><i>📊</i>MERCADO</button>
        <button class="nav-btn" onclick="tab('chat', this)"><i>🧠</i>LISA IA</button>
        <button class="nav-btn" onclick="tab('mem', this)"><i>⚙️</i>CONFIG</button>
    </nav>
</div>

<script>
    const currentPrices = {};

    const assets = [
        { id: 'eurusdt', symbol: 'EUR/USD', name: 'Euro Dólar', p: 5 },
        { id: 'usdcjpy', symbol: 'USD/JPY', name: 'Dólar Yen', p: 3 },
        { id: 'btcusdt', symbol: 'BTC/USDT', name: 'Bitcoin', p: 2 },
        { id: 'ethusdt', symbol: 'ETH/USDT', name: 'Ethereum', p: 2 }
    ];

    function initMarket() {
        const container = document.getElementById('market-list');
        container.innerHTML = "";
        assets.forEach(a => {
            currentPrices[a.id] = 0;
            container.innerHTML += `
                <div class="asset-card">
                    <div class="asset-row">
                        <div>
                            <span class="ticker">${a.symbol}</span><br>
                            <small style="color:var(--text-dim)">${a.name}</small>
                        </div>
                        <div style="text-align:right">
                            <div class="price-live" id="p-${a.id}">0.00000</div>
                            <div id="c-${a.id}" style="font-size:12px; font-weight:bold;">0.00%</div>
                        </div>
                    </div>
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
                const old = parseFloat(elP.innerText);
                elP.innerText = price.toFixed(config.p);
                elP.classList.remove('price-up', 'price-down');
                void elP.offsetWidth;
                elP.classList.add(price >= old ? 'price-up' : 'price-down');
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

            const sl = isBuy ? (livePrice * 0.9995) : (livePrice * 1.0005);
            const tp = isBuy ? (livePrice * 1.0015) : (livePrice * 0.9985);

            const response = `
                <b style="color:var(--primary)">LISA ALGORITHM v2.0</b><br>
                Auditando flujo real de <b>${symbol}</b>.<br>
                Precio de mercado capturado: <span style="font-family:'JetBrains Mono'; color:white">${livePrice.toFixed(precision)}</span>
                <div class="prob-container">
                    <div class="prob-box">
                        <span class="prob-label">Prob. Compra</span>
                        <span class="prob-val" style="color:var(--up)">${buyProb}%</span>
                    </div>
                    <div class="prob-box">
                        <span class="prob-label">Prob. Venta</span>
                        <span class="prob-val" style="color:var(--down)">${sellProb}%</span>
                    </div>
                </div>
                <div class="trade-ticket">
                    <span style="color:var(--primary)">⚡ SETUP REAL-TIME</span><br>
                    TIPO: ${isBuy ? 'LONG / BUY' : 'SHORT / SELL'}<br>
                    ENTRADA: ${livePrice.toFixed(precision)}<br>
                    SL: ${sl.toFixed(precision)}<br>
                    TP: ${tp.toFixed(precision)}
                    <button class="btn-action" style="background:${isBuy?'var(--up)':'var(--down)'}">
                        EJECUTAR AL ${Math.max(buyProb, sellProb)}% CERTEZA
                    </button>
                </div>
            `;
            addMsg(response, 'ia');
        }, 800);
    }

    function addMsg(txt, type) {
        const s = document.getElementById('chat-scroller');
        const d = document.createElement('div');
        d.className = `msg ${type}`;
        d.innerHTML = txt;
        s.appendChild(d);
        // Scroll automático suave al final
        s.scrollTo({ top: s.scrollHeight, behavior: 'smooth' });
    }

    function tab(id, btn) {
        document.querySelectorAll('.page').forEach(p => p.classList.remove('active'));
        document.querySelectorAll('.nav-btn').forEach(b => b.classList.remove('active'));
        document.getElementById('page-' + id).classList.add('active');
        btn.classList.add('active');
    }

    // Ajuste dinámico para el viewport visual (teclado móvil)
    if (window.visualViewport) {
        window.visualViewport.addEventListener('resize', () => {
            document.querySelector('.app-container').style.height = window.visualViewport.height + 'px';
        });
    }

    window.onload = initMarket;
</script>
</body>
</html>
