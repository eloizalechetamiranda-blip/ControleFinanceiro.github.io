<html lang="pt-br">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Controle Financeiro</title>
<style>
body {
    font-family: Arial, sans-serif;
    background-color: #f4f4f4;
    margin: 0;
    padding: 0;
}
.container {
    max-width: 800px;
    margin: 30px auto;
    padding: 20px;
    background-color: white;
    box-shadow: 0px 0px 10px rgba(0, 0, 0, 0.1);
}
h1 {
    text-align: center;
    color: #333;
}
table {
    width: 100%;
    border-collapse: collapse;
    margin-top: 20px;
}
table, th, td {
    border: 1px solid #ddd;
}
th, td {
    padding: 10px;
    text-align: left;
}
th {
    background-color: #f4f4f4;
}
.form-group {
    margin: 15px 0;
}
.form-group input, .form-group select {
    width: 100%;
    padding: 10px;
    margin-top: 5px;
    font-size: 16px;
}
.button {
    background-color: #4CAF50;
    color: white;
    border: none;
    padding: 10px 20px;
    font-size: 16px;
    cursor: pointer;
}
.button:hover {
    background-color: #45a049;
}
.button-delete {
    background-color: #f44336;
    color: white;
    border: none;
    padding: 5px 10px;
    cursor: pointer;
}
.button-delete:hover {
    background-color: #e53935;
}
.saldo {
    font-size: 18px;
    text-align: right;
    margin-top: 20px;
}
</style>
</head>
<body>
<div class="container"> 
    <h1>Controle Financeiro</h1>
    <div class="form-group">
        <label for="descricao">Descrição</label> 
        <input type="text" id="descricao" placeholder="Descrição da transação"> 
    </div>
    <div class="form-group">
        <label for="valor">Valor</label> 
        <input type="number" id="valor" placeholder="Valor da transação">
    </div>
    <div class="form-group">
        <label for="tipo">Tipo de transação</label>
        <select id="tipo">
            <option value="entrada">Entrada</option>
            <option value="saida">Saida</option>
        </select>
    </div>
    <button class="button" onclick="adicionarTransacao()">Adicionar Transação</button>

    <h2>Histórico de Transações</h2> 
    <table id="tabelaTransacoes"> 
        <thead>
            <tr>
                <th>Descrição</th>
                <th>Valor</th>
                <th>Tipo</th>
                <th>Ações</th>
            </tr>
        </thead>
        <body>
        </body>
    </table>
    <div class="saldo">
        <strong>Saldo Atual:</strong> R$ <span id="saldoAtual">0.00</span>
    </div>
</div>

<script>
let saldo = 0;
const tabelaTransacoes = document.getElementById("tabelaTransacoes").getElementsByTagName("tbody")[0]; 
const saldoAtual = document.getElementById("saldoAtual");

function carregarTransacoes() {
    const transacoes = JSON.parse(localStorage.getItem('transacoes')) || [];
    saldo = 0;
    tabelaTransacoes.innerHTML = "";
    transacoes.forEach(transacao => {
        const novaLinha = tabelaTransacoes.insertRow(); 
        const colunaDescricao = novaLinha.insertCell(0); 
        const colunaValor = novaLinha.insertCell(1); 
        const colunaTipo = novaLinha.insertCell(2); 
        const colunaAcoes = novaLinha.insertCell(3);

        colunaDescricao.textContent = transacao.descricao;
        colunaValor.textContent = 'R$ ' + parseFloat(transacao.valor).toFixed(2);
        colunaTipo.textContent = transacao.tipo === "entrada" ? "Entrada" : "Saída";
        
        const botaoExcluir = document.createElement("button");
        botaoExcluir.textContent = "Excluir";
        botaoExcluir.classList.add("button-delete");
        botaoExcluir.onclick = () => excluirTransacao(novaLinha, transacao.id);
        colunaAcoes.appendChild(botaoExcluir);
        
        saldo += transacao.tipo === "entrada" ? parseFloat(transacao.valor) : -parseFloat(transacao.valor);
    });
    atualizarSaldo();
}

function adicionarTransacao() { 
    const descricao = document.getElementById("descricao").value; 
    const valor = parseFloat(document.getElementById("valor").value); 
    const tipo = document.getElementById("tipo").value;

    if (!descricao || isNaN(valor) || valor <= 0) { 
        alert("Por favor, insira uma descrição válida e um valor positivo.");
        return;
    }

    const transacao = {
        id: Date.now(),
        descricao: descricao,
        valor: valor,
        tipo: tipo
    };

    let transacoes = JSON.parse(localStorage.getItem('transacoes')) || []; 
    transacoes.push(transacao); 
    localStorage.setItem('transacoes', JSON.stringify(transacoes));

    carregarTransacoes();

    document.getElementById("descricao").value = "";
    document.getElementById("valor").value = "";
}

function excluirTransacao(linha, id) {
    let transacoes = JSON.parse(localStorage.getItem('transacoes'));
    transacoes = transacoes.filter(transacao => transacao.id != id);
    localStorage.setItem('transacoes', JSON.stringify(transacoes));
    carregarTransacoes();
}

function atualizarSaldo() {
    saldoAtual.textContent = saldo.toFixed(2);
}

window.onload = carregarTransacoes;
</script>
</body>
</html>
