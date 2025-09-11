
<!DOCTYPE html>
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Calculadora - Divisão de Valores</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            margin: 20px;
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
            padding: 8px;
            text-align: left;
        }

        button {
            padding: 10px 15px;
            background-color: #4CAF50;
            color: white;
            border: none;
            cursor: pointer;
            margin: 5px;
        }

        button:hover {
            background-color: #45a049;
        }

        #total {
            margin-top: 20px;
            font-size: 18px;
        }

        .input-section {
            margin-bottom: 20px;
        }

        /* Estilos para o tema claro e escuro */
        .light-theme {
            background-color: #fff;
            color: #000;
        }

        .dark-theme {
            background-color: #333;
            color: #fff;
        }

        /* Estilo dos inputs e select */
        input, select {
            padding: 8px;
            margin: 5px;
        }

        .remove-btn {
            background-color: #f44336;
        }

        .remove-btn:hover {
            background-color: #d32f2f;
        }
    </style>
</head>
<body class="light-theme">

    <h1>Calculadora - Divisão de Valores</h1>

    <!-- Botão para mudar tema -->
    <button onclick="toggleTheme()">Mudar Tema</button>

    <!-- Formulário para inserir dados -->
    <div class="input-section">
        <label for="dia">Dia:</label>
        <input type="number" id="dia" placeholder="Dia" required>
        <label for="mes">Mês:</label>
        <input type="number" id="mes" placeholder="Mês" required>
        <label for="valor">Valor R$:</label>
        <input type="number" id="valor" placeholder="Valor" required>
        <button id="adicionarBtn">Adicionar</button>
    </div>

    <!-- Tabela para exibir os dados -->
    <table id="tabela">
        <thead>
            <tr>
                <th>Dia</th>
                <th>Mês</th>
                <th>Valor R$</th>
                <th>Fernanda (Valor)</th>
                <th>Amanda (Valor)</th>
                <th>Ação</th> <!-- Coluna de ação para remover -->
            </tr>
        </thead>
        <tbody>
            <!-- Linhas serão adicionadas aqui -->
        </tbody>
    </table>

    <div id="total">Total: R$ 0.00</div>
    <div id="totalFernanda">Total Fernanda: R$ 0.00</div>
    <div id="totalAmanda">Total Amanda: R$ 0.00</div>

    <!-- Botão para salvar -->
    <button id="salvarBtn">Salvar Tabela em PDF</button>

    <script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>

    <script>
        let total = 0;
        let totalFernanda = 0;
        let totalAmanda = 0;
        let rowsData = JSON.parse(localStorage.getItem('rowsData')) || []; // Carregar histórico de dados do localStorage

        // Função para adicionar nova linha à tabela
        function adicionarLinha() {
            const nome1 = "Fernanda"; // Nome fixado da 1ª amiga
            const nome2 = "Amanda";  // Nome fixado da 2ª amiga
            const dia = document.getElementById("dia").value;
            const mes = document.getElementById("mes").value;
            const valor = parseFloat(document.getElementById("valor").value);

            // Verificar se os campos estão preenchidos corretamente
            if (dia && mes && valor && !isNaN(valor)) {
                // Calcular o valor dividido
                const valorDividido = valor / 2;

                // Adicionar a linha à tabela
                const tabela = document.getElementById("tabela").getElementsByTagName('tbody')[0];
                const novaLinha = tabela.insertRow();

                // Criar células para "Dia", "Mês", "Valor", "Amiga 1", "Amiga 2" e "Ação"
                const celulaDia = novaLinha.insertCell(0);
                const celulaMes = novaLinha.insertCell(1);
                const celulaValor = novaLinha.insertCell(2);
                const celulaAmiga1 = novaLinha.insertCell(3);
                const celulaAmiga2 = novaLinha.insertCell(4);
                const celulaAcao = novaLinha.insertCell(5);

                // Preencher células com os valores
                celulaDia.textContent = dia;
                celulaMes.textContent = mes;
                celulaValor.textContent = `R$ ${valor.toFixed(2)}`;
                celulaAmiga1.textContent = `${nome1}: R$ ${valorDividido.toFixed(2)}`;
                celulaAmiga2.textContent = `${nome2}: R$ ${valorDividido.toFixed(2)}`;

                // Criar botão de remover
                const removerBtn = document.createElement("button");
                removerBtn.textContent = "Remover";
                removerBtn.classList.add("remove-btn");
                removerBtn.addEventListener("click", function() {
                    removerLinha(novaLinha, valor, valorDividido);
                });

                celulaAcao.appendChild(removerBtn);

                // Armazenar os dados da linha no array
                rowsData.push({ dia, mes, valor, nome1, nome2, valorDividido });
                total += valor;
                totalFernanda += valorDividido;
                totalAmanda += valorDividido;

                // Atualizar os totais na página
                document.getElementById("total").textContent = `Total: R$ ${total.toFixed(2)}`;
                document.getElementById("totalFernanda").textContent = `Total Fernanda: R$ ${totalFernanda.toFixed(2)}`;
                document.getElementById("totalAmanda").textContent = `Total Amanda: R$ ${totalAmanda.toFixed(2)}`;

                // Salvar os dados no localStorage
                localStorage.setItem('rowsData', JSON.stringify(rowsData));

                // Limpar os campos de entrada
                document.getElementById("dia").value = '';
                document.getElementById("mes").value = '';
                document.getElementById("valor").value = '';
            } else {
                alert("Por favor, preencha todos os campos corretamente.");
            }
        }

        // Função para remover uma linha
        function removerLinha(linha, valor, valorDividido) {
            // Remover a linha da tabela
            linha.remove();

            // Atualizar os totais
            total -= valor;
            totalFernanda -= valorDividido;
            totalAmanda -= valorDividido;

            document.getElementById("total").textContent = `Total: R$ ${total.toFixed(2)}`;
            document.getElementById("totalFernanda").textContent = `Total Fernanda: R$ ${totalFernanda.toFixed(2)}`;
            document.getElementById("totalAmanda").textContent = `Total Amanda: R$ ${totalAmanda.toFixed(2)}`;

            // Remover a linha dos dados armazenados no localStorage
            rowsData = rowsData.filter(item => item.valor !== valor);

            // Salvar novamente no localStorage
            localStorage.setItem('rowsData', JSON.stringify(rowsData));
        }

        // Função para salvar a tabela em PDF
        function salvarTabela() {
            const { jsPDF } = window.jspdf;
            const doc = new jsPDF();

            // Título do PDF
            doc.text("Tabela de Divisão de Uber", 20, 20);

            // Tabela
            let y = 30;

            // Adicionar título da tabela
            doc.text("Dia", 10, y);
            doc.text("Mês", 20, y);
            doc.text("Valor R$", 30, y);
            doc.text("Fernanda", 80, y);
            doc.text("Amanda", 160, y);
            y += 10;

            // Adicionar dados da tabela (as linhas armazenadas no array rowsData)
            rowsData.forEach((item) => {
                doc.text(item.dia.toString(), 10, y);
                doc.text(item.mes.toString(), 20, y);
                doc.text(`R$ ${item.valor.toFixed(2)}`, 30, y);
                doc.text(`${item.nome1}: R$ ${item.valorDividido.toFixed(2)}`, 80, y);
                doc.text(`${item.nome2}: R$ ${item.valorDividido.toFixed(2)}`, 160, y);
                y += 10;
            });

            // Total
            doc.text(`Total: R$ ${total.toFixed(2)}`, 20, y);
            doc.text(`Total Fernanda: R$ ${totalFernanda.toFixed(2)}`, 20, y + 10);
            doc.text(`Total Amanda: R$ ${totalAmanda.toFixed(2)}`, 20, y + 20);

            // Gerar e baixar o PDF
            doc.save("tabela_divisao_uber.pdf");
        }

        // Função para trocar entre os temas claro e escuro
        function toggleTheme() {
            const body = document.body;
            body.classList.toggle("dark-theme");
            body.classList.toggle("light-theme");
        }

        // Adicionar os event listeners aos botões
        document.getElementById("adicionarBtn").addEventListener("click", adicionarLinha);
        document.getElementById("salvarBtn").addEventListener("click", salvarTabela);

        // Carregar os dados salvos no localStorage na tabela ao iniciar
        window.onload = function() {
            const tabela = document.getElementById("tabela").getElementsByTagName('tbody')[0];
            rowsData.forEach((item) => {
                const novaLinha = tabela.insertRow();
                const celulaDia = novaLinha.insertCell(0);
                const celulaMes = novaLinha.insertCell(1);
                const celulaValor = novaLinha.insertCell(2);
                const celulaAmiga1 = novaLinha.insertCell(3);
                const celulaAmiga2 = novaLinha.insertCell(4);
                const celulaAcao = novaLinha.insertCell(5);

                celulaDia.textContent = item.dia;
                celulaMes.textContent = item.mes;
                celulaValor.textContent = `R$ ${item.valor.toFixed(2)}`;
                celulaAmiga1.textContent = `${item.nome1}: R$ ${item.valorDividido.toFixed(2)}`;
                celulaAmiga2.textContent = `${item.nome2}: R$ ${item.valorDividido.toFixed(2)}`;

                // Criar o botão de remover
                const removerBtn = document.createElement("button");
                removerBtn.textContent = "Remover";
                removerBtn.classList.add("remove-btn");
                removerBtn.addEventListener("click", function() {
                    removerLinha(novaLinha, item.valor, item.valorDividido);
                });

                celulaAcao.appendChild(removerBtn);

                // Atualizar os totais ao carregar os dados
                total += item.valor;
                totalFernanda += item.valorDividido;
                totalAmanda += item.valorDividido;
            });

            // Exibir os totais
            document.getElementById("total").textContent = `Total: R$ ${total.toFixed(2)}`;
            document.getElementById("totalFernanda").textContent = `Total Fernanda: R$ ${totalFernanda.toFixed(2)}`;
            document.getElementById("totalAmanda").textContent = `Total Amanda: R$ ${totalAmanda.toFixed(2)}`;
        }
    </script>

</body>
</html>
