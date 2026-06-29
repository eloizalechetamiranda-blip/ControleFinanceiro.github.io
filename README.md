<!DOCTYPE html>
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Controle Financeiro</title>
    <style>
        body { font-family: Arial, sans-serif; background-color: #f4f4f4; margin: 0; padding: 20px; }
        .container { max-width: 800px; margin: 30px auto; padding: 20px; background-color: white; box-shadow: 0px 0px 10px rgba(0, 0, 0, 0.1); }
        h1 { text-align: center; color: #333; }
        table { width: 100%; border-collapse: collapse; margin-top: 20px; }
        table, th, td { border: 1px solid #ddd; }
        th, td { padding: 10px; text-align: left; }
        th { background-color: #f4f4f4; }
        .form-group { margin: 15px 0; }
        .form-group input, .form-group select { width: 100%; padding: 10px; margin-top: 5px; box-sizing: border-box; }
        .button { background-color: #4CAF50; color: white; border: none; padding: 10px 20px; cursor: pointer; }
        .button-delete { background-color: #f44336; color: white; border: none; padding: 5px 10px; cursor: pointer; }
        .saldo { font-size: 18px; text-align: right; margin-top: 20px; font-weight: bold; }
    </style>
</head>
<body>
    <div class="container">
        <h1>Controle Financeiro</h1>
        <div class="form-group">
            <label>Descrição</label> <input type="text" id="descricao" placeholder="Descrição">
        </div>
        <div class="form-group">
            <label>Valor</label> <input type="number" id="valor" placeholder="Valor">
        </div>
        <div class="form-group">
            <label>Tipo</label>
            <select id="tipo">
                <option value="entrada">Entrada</option>
                <option value="saida">Saída</option>
            </select>
        </div>
        <button class="button" onclick="adicionarTransacao()">Adicionar Transação</button>

        <h2>Histórico</h2>
        <table id="tabelaTransacoes">
            <thead>
                <tr><th>Descrição</th><th>Valor</th><th>Tipo</th><th>Ações</th></tr>
            </thead>
            <tbody></tbody>
        </table>
        <div class="saldo">
            Saldo Atual: R$ <span id="saldoAtual">0.00</span>
        </div>
    </div>

    <script>
        function carregarTransacoes() {
            const transacoes = JSON.parse(localStorage.getItem('transacoes')) || [];
            const tbody = document.querySelector("#tabelaTransacoes tbody");
            tbody.innerHTML = "";
            let saldo = 0;

            transacoes.forEach(t => {
                const row = tbody.insertRow();
                row.innerHTML = `<td>${t.descricao}</td><td>R$ ${parseFloat(t.valor).toFixed(2)}</td><td>${t.tipo}</td>`;
                const cell = row.insertCell(3);
                const btn = document.createElement("button");
                btn.textContent = "Excluir";
                btn.className = "button-delete";
                btn.onclick = () => excluirTransacao(t.id);
                cell.appendChild(btn);
                saldo += t.tipo === "entrada" ? parseFloat(t.valor) : -parseFloat(t.valor);
            });
            document.getElementById("saldoAtual").textContent = saldo.toFixed(2);
        }

        function adicionarTransacao() {
            const descricao = document.getElementById("descricao").value;
            const valor = parseFloat(document.getElementById("valor").value);
            const tipo = document.getElementById("tipo").value;
            if (!descricao || isNaN(valor)) return;
            const transacoes = JSON.parse(localStorage.getItem('transacoes')) || [];
            transacoes.push({ id: Date.now(), descricao, valor, tipo });
            localStorage.setItem('transacoes', JSON.stringify(transacoes));
            carregarTransacoes();
        }

        function excluirTransacao(id) {
            let transacoes = JSON.parse(localStorage.getItem('transacoes')).filter(t => t.id !== id);
            localStorage.setItem('transacoes', JSON.stringify(transacoes));
            carregarTransacoes();
        }

        window.onload = carregarTransacoes;
    </script>
</body>
</html>
