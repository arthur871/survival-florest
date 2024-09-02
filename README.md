<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Survival Florest</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            background-image: url('https://img.freepik.com/vetores-gratis/rio-na-noite-floresta-montanha-natureza-vetor-paisagem-dos-desenhos-animados-grama-verde-sob-a-cena-da-luz-da-lua-da-arvore-lindo-fluxo-fluindo-perto-do-prado-bosque-vazio-panoramico-com-pirilampos-voadores-vaga-lumes_107791-21443.jpg?t=st=1725300546~exp=1725304146~hmac=842cc050792a0228e0f7c875c7398e539e89952f5fecf78730ef5cbca8a12311&w=1060');
            background-size: cover;
            color: white;
            text-align: center;
        }

        #jogo, #vitoria, #derrota, #exploracao {
            background-color: rgba(0, 0, 0, 0.7); /* Fundo escuro semitransparente */
            padding: 20px;
            border-radius: 10px;
            display: inline-block;
            margin-top: 20px;
        }

        button {
            background-color: #4CAF50; /* Verde para o botão */
            color: white;
            padding: 10px 20px;
            border: none;
            border-radius: 5px;
            cursor: pointer;
        }

        button:hover {
            background-color: #45a049;
        }

        #informacoesPersonagem {
            display: none;
        }
    </style>
</head>
<body>
    <h1>Bem-vindo ao Survival Florest!</h1>
    <p>Escolha o nome do seu personagem:</p>
    <input type="text" id="nomeJogador" value="JULHÃO baiano" readonly>
    <button onclick="iniciarJogo()">Iniciar Jogo</button>

    <div id="informacoesPersonagem">
        <p id="classeEscolha"></p>
        <p id="status"></p>
    </div>

    <div id="jogo" style="display:none;">
        <p id="resultado"></p>
        <button onclick="escolherAcao(1)">Atacar</button>
        <button onclick="escolherAcao(2)">Fugir</button>
    </div>

    <div id="exploracao" style="display:none;">
        <p id="encontrouInimigo"></p>
        <button onclick="explorarFloresta()">Explorar Floresta</button>
    </div>

    <div id="vitoria" style="display:none;">
        <h2>Parabéns!</h2>
        <p>Você derrotou todos os inimigos e chegou à cidade!</p>
        <button onclick="reiniciarJogo()">Jogar Novamente</button>
    </div>

    <div id="derrota" style="display:none;">
        <h2>Game Over</h2>
        <p>Você foi derrotado...</p>
        <button onclick="reiniciarJogo()">Jogar Novamente</button>
    </div>

    <script>
        let vidaJogador, vidaInimigo, ataqueJogador, ataqueInimigo;
        let nomeJogador = "JULHÃO baiano";
        let classeJogador;
        let inimigosDerrotados = 0;
        let pontuacao = 0;
        let contadorAtaquesJogador = 0;
        let contadorAtaquesInimigo = 0;
        let turnoJogador = true;
        const inimigosTotal = 15;
        const vidaAumento = 50;
        let inimigoAtual = 0;

        const tiposInimigos = [
            { nome: "Tigre", vida: 80, ataque: 12 },
            { nome: "Cobra", vida: 60, ataque: 15 },
            { nome: "Lobo", vida: 70, ataque: 14 },
            { nome: "Jacaré", vida: 90, ataque: 10 }
        ];

        function iniciarJogo() {
            let escolhaClasse = prompt("Escolha sua classe:\n1 - Guerreiro (Mais vida, menos ataque)\n2 - Mago (Menos vida, mais ataque)");

            if (escolhaClasse == 1) {
                classeJogador = "Guerreiro";
                vidaJogador = 100;
                ataqueJogador = 15;
            } else {
                classeJogador = "Mago";
                vidaJogador = 70;
                ataqueJogador = 25;
            }

            document.getElementById("classeEscolha").textContent = "Você escolheu a classe " + classeJogador + " com " + vidaJogador + " de vida e " + ataqueJogador + " de ataque.";
            document.getElementById("status").textContent = "Um inimigo está à espreita!";
            document.getElementById("informacoesPersonagem").style.display = "block";
            document.getElementById("jogo").style.display = "none";
            document.getElementById("exploracao").style.display = "block";
        }

        function explorarFloresta() {
            if (inimigosDerrotados >= inimigosTotal) {
                irParaCidade();
                return;
            }

            let inimigo = tiposInimigos[Math.floor(Math.random() * tiposInimigos.length)];
            vidaInimigo = inimigo.vida;
            ataqueInimigo = inimigo.ataque;

            document.getElementById("encontrouInimigo").textContent = "Você encontrou um " + inimigo.nome + " com " + vidaInimigo + " de vida e " + ataqueInimigo + " de ataque!";
            document.getElementById("exploracao").style.display = "none";
            document.getElementById("jogo").style.display = "block";
        }

        function escolherAcao(escolhaAcao) {
            if (turnoJogador) {
                if (escolhaAcao == 1) {
                    // Jogador ataca
                    contadorAtaquesJogador++;
                    let danoAdicionalJogador = (contadorAtaquesJogador % 3 === 0) ? Math.floor(Math.random() * 10) + 3 : 0;
                    let danoTotalJogador = ataqueJogador + danoAdicionalJogador;

                    vidaInimigo -= danoTotalJogador;
                    document.getElementById("resultado").textContent = "Você atacou o inimigo e causou " + danoTotalJogador + " de dano!";

                    if (vidaInimigo <= 0) {
                        inimigosDerrotados++;
                        pontuacao += 10;

                        document.getElementById("resultado").textContent += "\nVocê derrotou o inimigo e ganhou " + vidaAumento + " de vida!";
                        vidaJogador += vidaAumento;

                        if (inimigosDerrotados >= inimigosTotal) {
                            irParaCidade();
                            return;
                        }

                        document.getElementById("jogo").style.display = "none";
                        document.getElementById("exploracao").style.display = "block";
                        return;
                    }

                    // Tempo de espera para o inimigo atacar
                    setTimeout(() => {
                        combaterInimigo();
                    }, 5000);
                    turnoJogador = false; // Passa para o turno do inimigo
                } else if (escolhaAcao == 2) {
                    // Jogador foge
                    document.getElementById("resultado").textContent = "Você fugiu do combate.";
                    document.getElementById("jogo").style.display = "none";
                    document.getElementById("exploracao").style.display = "block";
                } else {
                    document.getElementById("resultado").textContent = "Escolha inválida! Tente novamente.";
                }
            }
        }

        function combaterInimigo() {
            if (vidaJogador <= 0) return;

            let chanceDesvio = Math.random() < 0.1; // 10% de chance de desviar

            // Inimigo ataca
            contadorAtaquesInimigo++;
            let danoAdicionalInimigo = (contadorAtaquesInimigo % 3 === 0) ? Math.floor(Math.random() * 10) + 3 : 0;
            let danoTotalInimigo = ataqueInimigo + danoAdicionalInimigo;

            if (!chanceDesvio) {
                vidaJogador -= danoTotalInimigo;
                document.getElementById("resultado").textContent += "\nO inimigo atacou você e causou " + danoTotalInimigo + " de dano!";
            } else {
                document.getElementById("resultado").textContent += "\nVocê desviou do ataque do inimigo!";
            }

            if (vidaJogador <= 0) {
                document.getElementById("resultado").textContent += "\nVocê foi derrotado pelo inimigo...";
                document.getElementById("jogo").style.display = "none";
                document.getElementById("derrota").style.display = "block";
                return;
            }

            turnoJogador = true; // Passa para o turno do jogador
        }

        function reiniciarJogo() {
            document.getElementById("vitoria").style.display = "none";
            document.getElementById("derrota").style.display = "none";
            document.getElementById("nomeJogador").value = "JULHÃO baiano";
            document.getElementById("resultado").textContent = "";
            document.getElementById("jogo").style.display = "none";
            document.getElementById("exploracao").style.display = "none";
            document.getElementById("informacoesPersonagem").style.display = "none";
            inimigosDerrotados = 0;
            pontuacao = 0;
            contadorAtaquesJogador = 0;
            contadorAtaquesInimigo = 0;
            turnoJogador = true;
            inimigoAtual = 0;
        }

        function irParaCidade() {
            document.getElementById("jogo").style.display = "none";
            document.getElementById("exploracao").style.display = "none";
            document.getElementById("vitoria").style.display = "block";
        }
    </script>
</body>
</html>
