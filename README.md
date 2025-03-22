<!DOCTYPE html>
<html lang="pt-BR">
<head>
  <meta charset="UTF-8">
  <title>BotEury - Versão Completa Web</title>
  <!-- Importa o Chart.js via CDN -->
  <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
  <style>
    /* Estilos gerais */
    body {
      margin: 0;
      padding: 0;
      font-family: Arial, sans-serif;
      background: #1E1E1E url('https://via.placeholder.com/800x600?text=Fundo') no-repeat center center fixed;
      background-size: cover;
      color: white;
    }
    .container {
      background: rgba(30,30,30,0.9);
      max-width: 800px;
      margin: 20px auto;
      padding: 20px;
      border-radius: 15px;
      box-shadow: 0 0 10px rgba(0,0,0,0.5);
    }
    h1 {
      text-align: center;
      margin-bottom: 20px;
    }
    button {
      cursor: pointer;
      border: none;
      border-radius: 10px;
      padding: 10px 20px;
      font-size: 16px;
      transition: opacity 0.3s ease;
    }
    button:active {
      opacity: 0.7;
    }
    /* Botões principais */
    #accountButton {
      background-color: orange;
      color: white;
      margin-bottom: 10px;
    }
    #startButton {
      background-color: #00CC66;
      color: white;
      margin: 10px;
    }
    #startButton.paused {
      background-color: red;
    }
    /* Indicador de lucro */
    .profit {
      font-size: 28px;
      font-weight: bold;
      margin: 10px 0;
      text-align: center;
    }
    /* Indicador de progresso (3 círculos e linhas) */
    .progress-indicator {
      display: flex;
      align-items: center;
      justify-content: center;
      margin: 20px 0;
    }
    .progress-indicator .circle {
      width: 20px;
      height: 20px;
      border-radius: 50%;
      background-color: white;
      margin: 0 15px;
    }
    .progress-indicator .line {
      width: 40px;
      height: 2px;
      background-color: white;
    }
    /* Tabela de operações */
    table {
      width: 100%;
      border-collapse: collapse;
      margin-top: 20px;
      background-color: rgba(30,30,30,0.8);
      border-radius: 10px;
      overflow: hidden;
    }
    table th, table td {
      padding: 10px;
      text-align: center;
      border: 1px solid #444;
    }
    table th {
      background-color: #333;
    }
    /* Área dos ícones */
    .icon-buttons {
      text-align: right;
      margin-top: 10px;
    }
    .icon-buttons button {
      background: none;
      border: none;
      font-size: 24px;
      margin-left: 10px;
    }
    /* Estilo dos modais */
    .modal {
      display: none; 
      position: fixed; 
      z-index: 100;
      left: 0;
      top: 0;
      width: 100%;
      height: 100%;
      overflow: auto;
      background-color: rgba(0,0,0,0.6);
    }
    .modal-content {
      background-color: #1E1E1E;
      margin: 10% auto;
      padding: 20px;
      border-radius: 10px;
      width: 90%;
      max-width: 500px;
      position: relative;
      animation: fadeIn 0.5s;
    }
    @keyframes fadeIn {
      from { opacity: 0; }
      to { opacity: 1; }
    }
    .modal-header {
      font-size: 20px;
      margin-bottom: 10px;
      text-align: center;
    }
    .modal-close {
      position: absolute;
      top: 10px;
      right: 15px;
      font-size: 28px;
      cursor: pointer;
    }
    .form-group {
      margin-bottom: 15px;
    }
    .form-group input, .form-group select {
      width: 100%;
      padding: 8px;
      font-size: 16px;
      border: 1px solid #444;
      border-radius: 5px;
      background-color: #333;
      color: white;
    }
    .modal-footer {
      text-align: center;
      margin-top: 15px;
    }
    .modal-footer button {
      padding: 10px 20px;
      font-size: 18px;
      border: none;
      border-radius: 5px;
      margin: 5px;
    }
  </style>
</head>
<body>
  <div class="container">
    <h1>BotEury</h1>
    <button id="accountButton">Conta Real - 00.00 USD</button>
    <div class="profit" id="profitLabel">Lucro/Prejuízo: <span id="profitValue">0.00</span></div>
    
    <div class="progress-indicator">
      <div class="circle" id="circle1"></div>
      <div class="line"></div>
      <div class="circle" id="circle2"></div>
      <div class="line"></div>
      <div class="circle" id="circle3"></div>
    </div>
    
    <div style="text-align: center;">
      <button id="startButton">Iniciar</button>
      <button id="botSelectButton">Trocar Bot</button>
    </div>
    
    <div class="icon-buttons">
      <button id="configButton">⚙️</button>
      <button id="accountSwitchButton">🔄</button>
      <button id="trashButton">🗑️</button>
      <button id="chartButton">📊</button>
    </div>
    
    <table id="operationsTable">
      <thead>
        <tr>
          <th>Hora</th>
          <th>Preço</th>
          <th>Resultado</th>
        </tr>
      </thead>
      <tbody></tbody>
    </table>
  </div>
  
  <!-- Modal de Configurações -->
  <div id="configModal" class="modal">
    <div class="modal-content">
      <span class="modal-close" data-modal="configModal">&times;</span>
      <div class="modal-header">Configurações</div>
      <div class="form-group">
        <input type="text" id="inputValue" placeholder="Valor de Entrada" value="0.35">
      </div>
      <div class="form-group">
        <input type="text" id="stopLossValue" placeholder="Valor de Stop Loss" value="9999999">
      </div>
      <div class="form-group">
        <input type="text" id="stopWinValue" placeholder="Valor de Stop Win" value="9999999">
      </div>
      <div class="modal-footer">
        <button id="configConfirm" style="background-color: #00CC66; color: white;">✅</button>
        <button id="configCancel" style="background-color: #FF4444; color: white;">❌</button>
      </div>
    </div>
  </div>
  
  <!-- Modal do Gráfico -->
  <div id="chartModal" class="modal">
    <div class="modal-content">
      <span class="modal-close" data-modal="chartModal">&times;</span>
      <div class="modal-header">Distribuição dos Últimos Ticks</div>
      <canvas id="chartCanvas" width="400" height="300"></canvas>
    </div>
  </div>
  
  <!-- Modal de Troca de Conta -->
  <div id="accountSwitchModal" class="modal">
    <div class="modal-content">
      <span class="modal-close" data-modal="accountSwitchModal">&times;</span>
      <div class="modal-header" id="accountSwitchHeader">Trocar Conta</div>
      <div class="modal-footer">
        <button id="accountSwitchConfirm" style="background-color: #00CC66; color: white;">✅</button>
        <button id="accountSwitchCancel" style="background-color: #FF4444; color: white;">❌</button>
      </div>
    </div>
  </div>
  
  <!-- Modal de Seleção de Bot -->
  <div id="botSelectModal" class="modal">
    <div class="modal-content">
      <span class="modal-close" data-modal="botSelectModal">&times;</span>
      <div class="modal-header">Selecionar Bot</div>
      <div class="form-group">
        <select id="botSelect">
          <option value="EMHSKS ^_^">EMHSKS ^_^</option>
          <!-- Adicione outras opções conforme necessário -->
        </select>
      </div>
      <div class="modal-footer">
        <button id="botSelectConfirm" style="background-color: #00CC66; color: white;">Selecionar</button>
      </div>
    </div>
  </div>
  
  <script>
    /* Variáveis de estado e parâmetros */
    let currentAccount = "real";
    let realBalance = 0.0, demoBalance = 0.0;
    let wins = 0, losses = 0, totalOps = 0;
    let profit = 0.0;
    let botRunning = false;
    let configValues = { entrada: "0.35", stop_loss: "9999999", stop_win: "9999999" };
    let tickCounts = Array(10).fill(0);
    let ws = null;
    let lastTradeTime = null;
    let waitingForResult = false;
    let lastTradeContractType = null;
    let lastTradeStake = null;
    
    // Parâmetros para a estratégia Even/Odd com Martingale
    let tradeCycle = ["even", "even", "odd", "odd"];
    let tradeIndex = 0;
    let baseStake = parseFloat(configValues.entrada);
    let currentStake = baseStake;
    let martingaleLoss = 0.0;
    let desiredProfit = 0.02;
    let payout = 0.63; // será atualizado pela API

    // Seleção de Bot
    let selectedBot = "EMHSKS ^_^";

    /* Elementos da interface */
    const accountButton = document.getElementById("accountButton");
    const profitValueEl = document.getElementById("profitValue");
    const startButton = document.getElementById("startButton");
    const operationsTableBody = document.querySelector("#operationsTable tbody");
    const configModal = document.getElementById("configModal");
    const chartModal = document.getElementById("chartModal");
    const accountSwitchModal = document.getElementById("accountSwitchModal");
    const botSelectModal = document.getElementById("botSelectModal");

    /* Funções de atualização da interface */
    function updateAccountButton() {
      if (currentAccount === "real") {
        accountButton.textContent = `Conta Real - ${realBalance.toFixed(2)} USD`;
      } else {
        accountButton.textContent = `Conta Demo - ${demoBalance.toFixed(2)} USD`;
      }
    }

    function updateProfitDisplay() {
      profitValueEl.textContent = profit.toFixed(2);
      profitValueEl.style.color = profit >= 0 ? "#00FF00" : "white";
      // Atualização simples dos círculos (exemplo: primeiro verde, demais vermelhos)
      document.getElementById("circle1").style.backgroundColor = "green";
      document.getElementById("circle2").style.backgroundColor = "red";
      document.getElementById("circle3").style.backgroundColor = "red";
    }

    function addOperation(resultValue) {
      const row = document.createElement("tr");
      const now = new Date();
      row.innerHTML = `<td>${now.toLocaleTimeString()}</td>
                       <td>${(Math.random()*100).toFixed(2)}</td>
                       <td style="color:${resultValue>=0?'green':'white'}">${resultValue.toFixed(2)}</td>`;
      operationsTableBody.insertBefore(row, operationsTableBody.firstChild);
    }

    /* Conexão com a API via WebSocket */
    function connectAPI() {
      const token = currentAccount === "real" ? "wI47V8q7QqxnFL0" : "o7zwtrKf9H0jZ2t";
      ws = new WebSocket("wss://ws.binaryws.com/websockets/v3?app_id=69871");
      ws.onopen = () => {
        ws.send(JSON.stringify({ authorize: token }));
        ws.send(JSON.stringify({ balance: 1 }));
        ws.send(JSON.stringify({
          proposal: 1,
          amount: parseFloat(configValues.entrada),
          basis: "stake",
          contract_type: "DIGITEVEN",
          currency: "USD",
          symbol: "R_100"
        }));
        console.log("Autorização enviada!");
      };
      ws.onmessage = (event) => {
        const data = JSON.parse(event.data);
        console.log("API:", data);
        if (data.authorize) {
          if (data.authorize.client_id) {
            ws.send(JSON.stringify({ ticks: "R_100" }));
            console.log("Inscrição para ticks enviada!");
          }
        }
        if (data.balance) {
          const bal = parseFloat(data.balance);
          if (currentAccount === "real") {
            realBalance = bal;
          } else {
            demoBalance = bal;
          }
          updateAccountButton();
        }
        if (data.proposal && data.proposal.payout) {
          payout = parseFloat(data.proposal.payout);
        }
        if (data.tick) {
          processTick(data.tick);
        }
      };
      ws.onerror = (error) => {
        console.error("Erro:", error);
      };
      ws.onclose = () => {
        console.log("Conexão encerrada");
      };
    }

    /* Processamento de cada tick recebido */
    function processTick(tickData) {
      const currentTick = tickData.quote;
      const currentTime = new Date();
      const digit = parseInt(String(currentTick).slice(-1));
      tickCounts[digit] += 1;
      
      // Se o bot estiver ativo, executa trade de forma simplificada
      if (botRunning) {
        if (!waitingForResult) {
          if (!lastTradeTime || ((currentTime - lastTradeTime) / 1000 >= 6)) {
            executeTrade();
            lastTradeTime = currentTime;
            waitingForResult = true;
          }
        } else if (((currentTime - lastTradeTime) / 1000) >= 6) {
          let win = false;
          if (lastTradeContractType === "DIGITEVEN") {
            win = (digit % 2 === 0);
          } else {
            win = (digit % 2 === 1);
          }
          processTradeResult(win);
          waitingForResult = false;
        }
      }
    }

    /* Função para enviar ordem de trade */
    function executeTrade() {
      const tradeType = tradeCycle[tradeIndex];
      const contractType = tradeType === "even" ? "DIGITEVEN" : "DIGITODD";
      const orderDetails = {
        buy: currentStake,
        contract_type: contractType,
        symbol: "R_100",
        duration: 6,
        duration_unit: "s",
        basis: "stake",
        currency: "USD"
      };
      if (ws && ws.readyState === WebSocket.OPEN) {
        ws.send(JSON.stringify(orderDetails));
        console.log(`Ordem enviada: ${contractType} com stake ${currentStake.toFixed(2)}`);
      }
      lastTradeContractType = contractType;
      lastTradeStake = currentStake;
      tradeIndex = (tradeIndex + 1) % tradeCycle.length;
    }

    /* Processamento do resultado do trade */
    function processTradeResult(win) {
      if (win) {
        console.log("Operação vencedora!");
        profit += currentStake * payout - currentStake;
        currentStake = baseStake;
        martingaleLoss = 0.0;
        wins++;
        addOperation(currentStake * payout - currentStake);
      } else {
        console.log("Operação perdedora!");
        martingaleLoss += lastTradeStake;
        currentStake = (martingaleLoss + desiredProfit) / payout;
        profit -= lastTradeStake;
        losses++;
        addOperation(-lastTradeStake);
      }
      totalOps++;
      updateProfitDisplay();
    }

    /* Funções para abrir e fechar modais */
    function openModal(modalId) {
      document.getElementById(modalId).style.display = "block";
    }
    function closeModal(modalId) {
      document.getElementById(modalId).style.display = "none";
    }

    /* Eventos para os botões e modais */
    document.getElementById("configButton").addEventListener("click", () => {
      openModal("configModal");
    });
    document.getElementById("chartButton").addEventListener("click", () => {
      openModal("chartModal");
      renderChart();
    });
    document.getElementById("accountSwitchButton").addEventListener("click", () => {
      // Prepara mensagem de acordo com a conta atual
      const header = document.getElementById("accountSwitchHeader");
      if (currentAccount === "real") {
        header.textContent = `Conta real com saldo ${realBalance.toFixed(2)} USD. Mudar para conta demo?`;
      } else {
        header.textContent = `Conta demo com saldo ${demoBalance.toFixed(2)} USD. Mudar para conta real?`;
      }
      openModal("accountSwitchModal");
    });
    document.getElementById("botSelectButton").addEventListener("click", () => {
      openModal("botSelectModal");
    });
    document.getElementById("trashButton").addEventListener("click", () => {
      operationsTableBody.innerHTML = "";
      wins = losses = totalOps = 0;
      profit = 0.0;
      updateProfitDisplay();
    });
    
    // Fechar modais ao clicar no "x"
    document.querySelectorAll(".modal-close").forEach(el => {
      el.addEventListener("click", () => {
        closeModal(el.getAttribute("data-modal"));
      });
    });
    
    // Botões de confirmação/cancelamento dos modais
    document.getElementById("configConfirm").addEventListener("click", () => {
      configValues.entrada = document.getElementById("inputValue").value;
      configValues.stop_loss = document.getElementById("stopLossValue").value;
      configValues.stop_win = document.getElementById("stopWinValue").value;
      baseStake = parseFloat(configValues.entrada);
      currentStake = baseStake;
      closeModal("configModal");
    });
    document.getElementById("configCancel").addEventListener("click", () => {
      closeModal("configModal");
    });
    
    document.getElementById("accountSwitchConfirm").addEventListener("click", () => {
      // Alterna a conta e reconecta a API
      currentAccount = currentAccount === "real" ? "demo" : "real";
      updateAccountButton();
      if (ws) ws.close();
      connectAPI();
      closeModal("accountSwitchModal");
    });
    document.getElementById("accountSwitchCancel").addEventListener("click", () => {
      closeModal("accountSwitchModal");
    });
    
    document.getElementById("botSelectConfirm").addEventListener("click", () => {
      selectedBot = document.getElementById("botSelect").value;
      console.log("Bot selecionado:", selectedBot);
      closeModal("botSelectModal");
    });
    
    // Fechar modais ao clicar fora do conteúdo
    window.onclick = function(event) {
      document.querySelectorAll(".modal").forEach(modal => {
        if (event.target === modal) modal.style.display = "none";
      });
    };

    /* Renderiza o gráfico usando Chart.js */
    function renderChart() {
      const ctx = document.getElementById("chartCanvas").getContext("2d");
      const totalTicks = tickCounts.reduce((a, b) => a + b, 0) || 1;
      const percentages = tickCounts.map(val => (val / totalTicks) * 100);
      const colors = Array(10).fill("lightgray");
      colors[1] = "red";
      colors[3] = "teal";
      if (window.myChart) window.myChart.destroy();
      window.myChart = new Chart(ctx, {
        type: 'bar',
        data: {
          labels: [...Array(10).keys()],
          datasets: [{
            label: '%',
            data: percentages,
            backgroundColor: colors,
            borderColor: 'black',
            borderWidth: 1
          }]
        },
        options: {
          scales: {
            y: {
              beginAtZero: true,
              max: 28,
              ticks: {
                stepSize: 4
              }
            }
          }
        }
      });
    }

    /* Inicia a conexão com a API ao carregar a página */
    window.onload = () => {
      connectAPI();
    };

    /* Inicia/pausa o bot */
    startButton.addEventListener("click", () => {
      botRunning = !botRunning;
      startButton.textContent = botRunning ? "Pausar" : "Iniciar";
      startButton.classList.toggle("paused", botRunning);
    });
    
  </script>
</body>
</html>
