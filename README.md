
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Financeiro - Grêmio</title>
    <style>
        :root {
            --primary-bg: #0a192f;
            --card-bg: #112240;
            --accent: #64ffda;
            --danger: #ff4d4d;
            --success: #2ecc71;
            --warning: #f1c40f;
            --save-btn: #1e70e6;
            --text-main: #e6f1ff;
            --text-sub: #8892b0;
        }

        body, html { margin: 0; padding: 0; width: 100%; min-height: 100%; background-color: var(--primary-bg); }
        body { font-family: 'Segoe UI', sans-serif; color: var(--text-main); display: flex; justify-content: center; align-items: flex-start; padding: 20px 0; }

        #login-screen { 
            background: var(--card-bg); padding: 40px; border-radius: 20px; border: 1px solid var(--accent); 
            text-align: center; width: 320px; box-shadow: 0 10px 30px rgba(0,0,0,0.5); margin-top: 10vh; 
        }

        .login-input { 
            width: 100%; padding: 12px; margin: 10px 0; background: var(--primary-bg); 
            border: 1px solid #233554; color: white; border-radius: 8px; box-sizing: border-box; 
        }

        .container { 
            background: var(--card-bg); padding: 25px; border-radius: 20px; 
            width: 90%; max-width: 850px; border: 1px solid #233554; display: none; margin-bottom: 50px; 
        }

        .header { margin-bottom: 20px; padding-bottom: 10px; position: relative; border-bottom: 1px solid #233554; }
        .btn-logout { 
            position: absolute; right: 0; top: 0; background: rgba(255, 77, 77, 0.1); 
            border: 1px solid var(--danger); color: var(--danger); padding: 5px 12px; 
            border-radius: 6px; cursor: pointer; font-size: 0.8rem;
        }

        .nav-menu { display: flex; gap: 10px; margin-bottom: 25px; justify-content: center; }
        .nav-btn {
            flex: 1; padding: 15px; border-radius: 10px; border: none;
            font-weight: bold; cursor: pointer; text-transform: uppercase;
            font-size: 0.8rem; transition: 0.3s;
        }
        .btn-retirada { background: var(--danger); color: white; }
        .btn-deposito { background: var(--success); color: white; }
        .btn-fechar { background: var(--warning); color: black; }

        /* MODAL DE TRANSAÇÃO */
        #trans-modal {
            display: none; position: fixed; top: 50%; left: 50%; transform: translate(-50%, -50%);
            background: var(--card-bg); padding: 20px; border-radius: 15px; border: 2px solid var(--accent);
            z-index: 100; width: 280px; box-shadow: 0 0 50px rgba(0,0,0,0.8);
        }

        .grid-container { display: grid; grid-template-columns: repeat(auto-fit, minmax(120px, 1fr)); gap: 12px; }
        .card { background: var(--primary-bg); border: 1px solid #233554; border-radius: 12px; padding: 12px; text-align: center; }
        .qtd-input { 
            width: 85%; padding: 8px; background: #112240; border: 1px solid var(--accent); 
            border-radius: 8px; text-align: center; font-size: 1.1em; color: var(--accent); font-weight: bold; 
        }

        .summary-panel { 
            margin-top: 25px; padding: 20px; background: #0d1b33; border-radius: 15px; 
            border: 2px solid var(--accent); display: flex; justify-content: space-between; 
            align-items: center; flex-wrap: wrap; gap: 15px;
        }

        .res-box { text-align: left; }
        .res-label { font-size: 0.7rem; color: var(--text-sub); text-transform: uppercase; }
        .res-value { font-size: 1.6rem; font-weight: bold; color: var(--accent); }

        .btn-group { display: flex; flex-direction: column; gap: 8px; }
        .btn { border: none; border-radius: 6px; font-weight: bold; cursor: pointer; text-transform: uppercase; padding: 10px 20px; font-size: 0.75rem; }
        
        .btn-save { background: var(--save-btn); color: white; }
        .btn-clear { background: #444; color: white; }

        #secret-logs { margin-top: 20px; background: #020c1b; padding: 15px; border-radius: 10px; border: 1px solid var(--accent); display: none; }
        .log-header { display: flex; justify-content: space-between; align-items: center; border-bottom: 1px solid #233554; margin-bottom: 10px; }
        .log-entry { font-family: monospace; font-size: 0.75rem; padding: 4px; border-bottom: 1px solid #112240; color: #8892b0; }
        .code-input { background: none; border: 1px solid #233554; color: var(--text-sub); width: 60px; text-align: center; display: block; margin: 25px auto 10px auto; font-size: 0.7rem; }

        @media print { .no-print, .btn-logout, .btn-group, .nav-menu, .code-input, #secret-logs, #trans-modal { display: none !important; } body { background: white; color: black; padding: 0; } .container { display: block !important; border: none; width: 100%; margin: 0; } }
    </style>
</head>
<body>

<div id="trans-modal" class="no-print">
    <h3 id="modal-title" style="margin-top:0; color:var(--accent)">Ação</h3>
    <input type="number" id="modal-valor" class="login-input" placeholder="Valor R$">
    <input type="text" id="modal-motivo" class="login-input" placeholder="Motivo/Descrição">
    <div style="display:flex; gap:10px; margin-top:10px;">
        <button class="btn btn-save" style="flex:1" onclick="confirmarTransicao()">Confirmar</button>
        <button class="btn btn-clear" style="flex:1" onclick="fecharModal()">Cancelar</button>
    </div>
</div>

<div id="login-screen">
    <h2 style="color: var(--accent); margin-bottom: 20px;">ACESSO RESTRITO</h2>
    <select id="user-select" class="login-input">
        <option value="">Selecione o Usuário</option>
        <option value="Thiago Pelegrineli">Thiago Pelegrineli</option>
        <option value="Davi Algusto">Davi Algusto</option>
    </select>
    <input type="password" id="pass-input" class="login-input" placeholder="Senha" onkeydown="if(event.key==='Enter') autenticar()">
    <button class="btn btn-save" style="width:100%" onclick="autenticar()">Entrar</button>
</div>

<div class="container" id="main-system">
    <div class="header">
        <button class="btn-logout no-print" onclick="location.reload()">Sair</button>
        <div id="user-info" style="color: var(--accent); font-weight: bold; font-size: 0.9rem;"></div>
        <div style="color: var(--text-sub); font-size: 0.7rem; margin-top: 4px;">Tesouraria Interna</div>
    </div>

    <div class="nav-menu no-print">
        <button class="nav-btn btn-retirada" onclick="abrirModal('RETIRADA')">Retirada</button>
        <button class="nav-btn btn-deposito" onclick="abrirModal('DEPOSITO')">Depósito</button>
        <button class="nav-btn btn-fechar" onclick="fazerFechamento()">Fechar o Caixa</button>
    </div>

    <div id="grid-caixa" class="grid-container"></div>

    <div class="summary-panel">
        <div class="res-box">
            <div class="res-label">Total Atual</div>
            <div id="res-total" class="res-value">R$ 0,00</div>
        </div>
        <div class="res-box">
            <div class="res-label">Diferença</div>
            <div id="res-diff" style="font-size: 1.2rem; font-weight: bold;">R$ 0,00</div>
        </div>
        <div class="btn-group no-print">
            <button onclick="salvarProgresso()" class="btn btn-save">💾 Salvar Alterações</button>
            <button onclick="zerarDiferencaManual()" class="btn btn-fechar" style="color: black;">⚖️ Ajustar Diferença</button>
            <button onclick="limparTudo()" class="btn btn-clear">🧹 Limpar Tudo</button>
        </div>
    </div>

    <input type="password" id="secret-code" class="code-input no-print" placeholder="...." onkeydown="if(event.key==='Enter') verLogs()">

    <div id="secret-logs">
        <div class="log-header">
            <h3 style="color: var(--accent); font-size: 0.9rem; margin: 0;">Logs de Auditoria</h3>
            <button onclick="limparLogs()" class="btn" style="background: var(--danger); padding: 5px 10px; font-size: 0.6rem;">Limpar Logs</button>
        </div>
        <div id="log-list"></div>
    </div>
</div>

<script>
    const users = { "Thiago Pelegrineli": "2012", "Davi Algusto": "2009" };
    let userAtivo = "";
    let acaoAtual = "";
    const valores = [200, 100, 50, 20, 10, 5, 2, 1, 0.5, 0.25, 0.1, 0.05];

    function autenticar() {
        const u = document.getElementById('user-select').value;
        const p = document.getElementById('pass-input').value;
        if (users[u] === p) {
            userAtivo = u;
            document.getElementById('login-screen').style.display = 'none';
            document.getElementById('main-system').style.display = 'block';
            document.getElementById('user-info').innerText = "👤 " + userAtivo;
            addLog(`LOGIN: ${userAtivo}`);
            desenhar();
        } else { alert("Senha Incorreta!"); }
    }

    /* MECANICA DE MODAL */
    function abrirModal(tipo) {
        acaoAtual = tipo;
        document.getElementById('modal-title').innerText = tipo;
        document.getElementById('trans-modal').style.display = 'block';
        document.getElementById('modal-valor').value = "";
        document.getElementById('modal-motivo').value = "";
    }

    function fecharModal() { document.getElementById('trans-modal').style.display = 'none'; }

    function confirmarTransicao() {
        const val = document.getElementById('modal-valor').value;
        const mot = document.getElementById('modal-motivo').value;
        if(!val || !mot) return alert("Preencha valor e motivo!");
        
        addLog(`${acaoAtual}: R$ ${val} | Motivo: ${mot} | Por: ${userAtivo}`);
        fecharModal();
        alert("Registrado com sucesso!");
    }

    function verLogs() {
        if(document.getElementById('secret-code').value === "2712") 
            document.getElementById('secret-logs').style.display = 'block';
    }

    function addLog(m) {
        const l = JSON.parse(localStorage.getItem('g_logs') || '[]');
        l.unshift({ t: new Date().toLocaleString(), m: m });
        localStorage.setItem('g_logs', JSON.stringify(l));
        renderLogs();
    }

    function renderLogs() {
        const l = JSON.parse(localStorage.getItem('g_logs') || '[]');
        document.getElementById('log-list').innerHTML = l.map(x => `<div class="log-entry">[${x.t}] ${x.m}</div>`).join('');
    }

    function limparLogs() {
        if (prompt("Senha para limpar logs:") === "2025") {
            localStorage.removeItem('g_logs');
            renderLogs();
            addLog(`LOGS LIMPOS POR ${userAtivo}`);
        }
    }

    function calcular() {
        let total = 0; let dados = [];
        valores.forEach((v, i) => {
            const q = parseFloat(document.getElementById(`input-${i}`).value) || 0;
            total += (q * v);
            dados.push(q);
        });
        document.getElementById('res-total').innerText = total.toLocaleString('pt-BR', {style:'currency', currency:'BRL'});
        localStorage.setItem('g_dados', JSON.stringify(dados));

        const ult = parseFloat(localStorage.getItem('g_ultimo') || 0);
        const diff = total - ult;
        const dEl = document.getElementById('res-diff');
        dEl.innerText = (diff >= 0 ? "+ " : "- ") + Math.abs(diff).toLocaleString('pt-BR', {style:'currency', currency:'BRL'});
        dEl.style.color = diff >= 0 ? "var(--success)" : "var(--danger)";
    }

    function salvarProgresso() {
        calcular();
        addLog(`SALVO: Dados atualizados por ${userAtivo}`);
        alert("Dados salvos!");
    }

    function zerarDiferencaManual() {
        if (prompt("Senha para ajustar diferença:") === "2016") {
            const totalAtual = valores.reduce((acc, v, i) => acc + (parseFloat(document.getElementById(`input-${i}`).value) || 0) * v, 0);
            localStorage.setItem('g_ultimo', totalAtual);
            addLog(`DIFERENÇA AJUSTADA POR ${userAtivo}.`);
            calcular();
        }
    }

    function fazerFechamento() {
        if(confirm("Deseja fechar o caixa e imprimir?")) {
            const total = valores.reduce((acc, v, i) => acc + (parseFloat(document.getElementById(`input-${i}`).value) || 0) * v, 0);
            localStorage.setItem('g_ultimo', total);
            addLog(`CAIXA FECHADO: R$ ${total.toFixed(2)} por ${userAtivo}`);
            window.print();
            calcular();
        }
    }

    function limparTudo() {
        if(confirm("Zerar campos?")) {
            valores.forEach((v, i) => document.getElementById(`input-${i}`).value = 0);
            calcular();
        }
    }

    function desenhar() {
        const grid = document.getElementById('grid-caixa');
        grid.innerHTML = valores.map((v, i) => `
            <div class="card">
                <div style="font-size:0.65rem; color:var(--text-sub); margin-bottom:5px;">R$ ${v.toFixed(2)}</div>
                <input type="number" class="qtd-input" id="input-${i}" value="0" min="0" oninput="calcular()">
            </div>
        `).join('');
        const d = JSON.parse(localStorage.getItem('g_dados'));
        if(d) d.forEach((q, i) => { if(document.getElementById(`input-${i}`)) document.getElementById(`input-${i}`).value = q; });
        calcular(); renderLogs();
    }
</script>
</body>
</html>
