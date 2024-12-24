<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Тогуз коргоол</title>
    <style>
        body {
            font-family: 'Arial', sans-serif;
            text-align: center;
            background: linear-gradient(to bottom, #9ACD32, #228B22); /* Цвета гор Кыргызстана */
            color: #333;
            margin: 0;
            padding: 0;
        }
        h1 {
            font-size: 48px;
            color: #2E8B57;
            margin: 20px 0;
        }
        .board-container {
            display: flex;
            flex-direction: column;
            align-items: center;
            margin: 20px 0;
        }
        .row {
            display: flex;
            justify-content: center;
            margin: 5px 0;
        }
        .hole {
            width: 70px;
            height: 70px;
            background-color: #D3D3D3; /* Светло-серый */
            border-radius: 50%;
            display: flex;
            align-items: center;
            justify-content: center;
            border: 3px solid #8B0000;
            font-size: 20px;
            cursor: pointer;
            position: relative;
            box-shadow: 0 4px 6px rgba(0, 0, 0, 0.2);
        }
        .hole:hover {
            background-color: #FFD700;
            transform: scale(1.1);
        }
        .stones {
            display: flex;
            flex-wrap: wrap;
            justify-content: center;
            width: 60px;
            height: 60px;
        }
        .stone {
            width: 15px;
            height: 15px;
            background-color: #0000FF; /* Синий */
            border-radius: 50%;
            margin: 2px;
        }
        button {
            margin: 10px;
            padding: 10px;
            font-size: 16px;
            background-color: #8B0000;
            color: #FFF;
            border: none;
            border-radius: 8px;
            cursor: pointer;
        }
        button:hover {
            background-color: #A52A2A;
        }
        .scores {
            margin-bottom: 20px;
            font-size: 18px;
        }
        .history-rules {
            width: 80%;
            margin: 20px auto;
            background: #FFF;
            padding: 15px;
            border-radius: 8px;
            box-shadow: 0 4px 10px rgba(0, 0, 0, 0.3);
        }
    </style>
</head>
<body>
    <h1>Тогуз коргоол</h1>
    <div class="scores">
        <span id="player1-score">Игрок 1: 0</span> |
        <span id="player2-score">Игрок 2: 0</span>
    </div>
    <div class="board-container">
        <div id="player2-row" class="row"></div>
        <div id="player1-row" class="row"></div>
    </div>
    <div>
        <button onclick="setMode('bot')">Игра с ботом</button>
        <button onclick="setMode('twoPlayers')">Два игрока</button>
        <button onclick="restartGame()">Начать заново</button>
        <button onclick="endGame()">Завершить игру</button>
    </div>
    <div class="history-rules">
        <h2>Оюндун шарттары:</h2>
        <p>Тогуз коргоол - кыргыз элинин улуттук акыл оюну. Атайын жасалган тактада 2 адам ойнойт. Такта 18 үйдөн жана 2 казынадан турат. Ар бир оюнчуга 9 үй, 1 казына таандык. Эгерде үйлөрдү солдон оңго карай 1ден 9га чейинки сандар менен белгилесек, анда ал сандарга туура келген үйлөрдүн аттары төмөнкүдөй болот: 1-үй - куйрук, 2-үй - ат өтпөс, 3-үй - текилдек, 4-үй - далы, 5-үй - бел, 6-үй - ак колтук, 7-үй - эки тишти, 8-үй - көк моюн, 9-үй - ооз. Тогуз коргоол оюну көп жабдууларды талап кылбайт. Оюнга бардыгы 162 коргол керек. Ар бир оюнчу жеңип чыгышы үчүн 81ден коргол жыйноого умтулат.</p>
    </div>
    <script>
        let board = [
            Array(9).fill(9), // Лунки игрока 1
            Array(9).fill(9)  // Лунки игрока 2
        ];
        let scores = [0, 0];
        let currentPlayer = 0; // 0 - Игрок 1, 1 - Игрок 2 или бот
        let mode = 'bot'; // Возможные значения: 'bot', 'twoPlayers'
        function setMode(selectedMode) {
            mode = selectedMode;
            restartGame();
        }
        function createBoard() {
            const player1Row = document.getElementById('player1-row');
            const player2Row = document.getElementById('player2-row');
            player1Row.innerHTML = '';
            player2Row.innerHTML = '';
            for (let i = 0; i < 9; i++) {
                const hole2 = document.createElement('div');
                hole2.className = 'hole';
                hole2.innerHTML = `<div class="stones">${createStones(board[1][i])}</div>`;
                if (mode === 'twoPlayers') {
                    hole2.onclick = () => makeMove(1, i);
                }
                player2Row.appendChild(hole2);
                const hole1 = document.createElement('div');
                hole1.className = 'hole';
                hole1.innerHTML = `<div class="stones">${createStones(board[0][i])}</div>`;
                hole1.onclick = () => makeMove(0, i);
                player1Row.appendChild(hole1);
            }
        }
        function createStones(count) {
            let stonesHTML = '';
            for (let i = 0; i < count; i++) {
                stonesHTML += '<div class="stone"></div>';
            }
            return stonesHTML;
        }
        async function makeMove(player, holeIndex) {
            if (player !== currentPlayer) {
                alert("Сейчас не ваш ход!");
                return;
            }
            let stones = board[player][holeIndex];
            if (stones === 0) {
                alert("Эта лунка пуста!");
                return;
            }
            board[player][holeIndex] = 0; // Опустошаем выбранную лунку
            let side = player;
            let hole = holeIndex;
            // Распределяем камни по лункам с анимацией
            while (stones > 0) {
                await new Promise(resolve => setTimeout(resolve, 300));
                hole += (side === 0 ? 1 : -1); // Направление хода
                if (hole > 8) {
                    hole = 0;
                    side = 1; // Переход на сторону соперника
                } else if (hole < 0) {
                    hole = 8;
                    side = 0; // Переход на свою сторону
                }
                board[side][hole]++;
                updateBoard();
                stones--;
            }
            // Захват коргоолов
            if (board[side][hole] % 2 === 0 && side !== player) {
                scores[player] += board[side][hole];
                board[side][hole] = 0;
            }
            updateBoard();
            // Проверка на конец игры
            if (checkGameOver()) {
                return;
            }
            // Переключение игрока
            currentPlayer = 1 - currentPlayer;
            if (mode === 'bot' && currentPlayer === 1) {
                setTimeout(botMove, 1000);
            }
        }
        function botMove() {
            let validMoves = board[1]
                .map((stones, index) => (stones > 0 ? index : -1))
                .filter(index => index !== -1);
            if (validMoves.length === 0) {
                alert("Бот не может сделать ход!");
                return;
            }
            let randomMove = validMoves[Math.floor(Math.random() * validMoves.length)];
            makeMove(1, randomMove);
        }
        function updateBoard() {
            createBoard();
            document.getElementById('player1-score').textContent = `Игрок 1: ${scores[0]}`;
            document.getElementById('player2-score').textContent = `Игрок 2: ${scores[1]}`;
        }
        function checkGameOver() {
            if (board[0].every(hole => hole === 0) || board[1].every(hole => hole === 0)) {
                endGame();
                return true;
            }
            return false;
        }
        function endGame() {
            let winner = scores[0] > scores[1] ? 'Игрок 1' : 'Игрок 2';
            if (scores[0] === scores[1]) {
                winner = 'Ничья';
            }
            alert(`Игра окончена! Победитель: ${winner}\nОчки Игрока 1: ${scores[0]}\nОчки Игрока 2: ${scores[1]}`);
            restartGame();
        }
        function restartGame() {
            board = [
                Array(9).fill(9),
                Array(9).fill(9)
            ];
            scores = [0, 0];
            currentPlayer = 0;
            updateBoard();
        }
        // Инициализация игры
        createBoard();
    </script>
</body>
</html>
