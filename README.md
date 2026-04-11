<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>蓝紫立体三角 · 扫雷小游戏</title>
    <style>
        * {
            box-sizing: border-box;
            margin: 0;
            padding: 0;
        }

        body {
            min-height: 100vh;
            background: linear-gradient(145deg, #0b0719 0%, #1f1a3a 100%);
            display: flex;
            justify-content: center;
            align-items: center;
            font-family: 'Segoe UI', 'Quicksand', 'PingFang SC', 'Microsoft YaHei', sans-serif;
            padding: 16px;
            margin: 0;
        }

        .game-container {
            max-width: 520px;
            width: 100%;
            background: rgba(10, 5, 25, 0.5);
            backdrop-filter: blur(4px);
            border-radius: 48px 48px 36px 36px;
            padding: 32px 24px 48px 24px;
            box-shadow: 0 30px 40px -15px rgba(0, 0, 0, 0.8), inset 0 2px 3px rgba(255, 255, 255, 0.1);
            border: 1px solid rgba(180, 140, 255, 0.2);
        }

        /* 标题区 —— 美体字 “扫雷” */
        .title-section {
            text-align: center;
            margin-bottom: 32px;
            position: relative;
        }

        .title-section h1 {
            font-size: 4.2rem;
            font-weight: 600;
            font-family: 'Quicksand', 'Segoe UI', '华文楷体', 'KaiTi', cursive;
            background: linear-gradient(135deg, #f0e4ff, #d9c9ff, #b1a1ff);
            -webkit-background-clip: text;
            background-clip: text;
            color: transparent;
            text-shadow: 0 0 15px #8a7cf0, 0 4px 8px rgba(0, 0, 0, 0.6);
            letter-spacing: 6px;
            transform: perspective(600px) rotateX(2deg);
            filter: drop-shadow(0 8px 4px #140e2b);
            line-height: 1.1;
        }

        .title-section h1::after {
            content: "⚡";
            font-size: 3rem;
            margin-left: 12px;
            opacity: 0.6;
            filter: drop-shadow(0 0 6px #b6a2ff);
        }

        .title-section h1::before {
            content: "⚡";
            font-size: 3rem;
            margin-right: 12px;
            opacity: 0.6;
            filter: drop-shadow(0 0 6px #b6a2ff);
        }

        /* 立体三角背景 (覆盖在格子群后方的装饰) */
        .board-wrapper {
            position: relative;
            display: flex;
            justify-content: center;
            align-items: center;
            padding: 16px;
            margin-bottom: 24px;
        }

        .triangle-bg {
            position: absolute;
            width: 100%;
            height: 100%;
            top: 0;
            left: 0;
            pointer-events: none;  /* 让格子可以穿透点击 */
            z-index: 1;
            overflow: visible;
            display: flex;
            justify-content: center;
            align-items: center;
        }

        .triangle-bg svg {
            width: 110%;
            height: 110%;
            max-width: 500px;
            opacity: 0.6;
            filter: drop-shadow(0 0 20px #5f4bb6) blur(1.5px);
            mix-blend-mode: hard-light;
        }

        /* 格子群 - 3x3 */
        .grid-3x3 {
            display: grid;
            grid-template-columns: repeat(3, 1fr);
            gap: 18px;
            justify-content: center;
            align-items: center;
            width: 100%;
            max-width: 380px;
            margin: 0 auto;
            position: relative;
            z-index: 10;
        }

        .cell {
            aspect-ratio: 1 / 1;
            background: radial-gradient(circle at 30% 30%, #3f2e6e, #190e36);
            border-radius: 24px;
            box-shadow: 0 12px 0 #0d0522, 0 8px 18px rgba(0, 0, 0, 0.9), inset 0 -2px 5px rgba(255, 210, 255, 0.4), inset 0 2px 5px #b59eff;
            cursor: pointer;
            display: flex;
            align-items: center;
            justify-content: center;
            transition: all 0.1s ease;
            font-size: 2rem;
            font-weight: bold;
            color: rgba(255, 255, 255, 0.9);
            text-shadow: 0 0 10px #f0e2ff, 0 2px 2px black;
            transform: translateY(-2px);
            border: 1px solid rgba(230, 200, 255, 0.4);
        }

        .cell:active {
            transform: translateY(6px);
            box-shadow: 0 6px 0 #0d0522, 0 5px 12px black;
        }

        .cell.pressed {
            background: radial-gradient(circle at 30% 30%, #6b4f9e, #2c1b55);
            box-shadow: inset 0 0 0 2px #b6a0ff, 0 4px 0 #0d0522, 0 4px 12px black;
            transform: translateY(4px);
        }

        .cell.mine-pressed {
            background: #b03f5e;
            box-shadow: inset 0 0 15px #ffa6bc, 0 2px 0 #51001c;
            color: white;
        }

        /* 提示框样式 (模拟浏览器弹窗，但为了视觉统一使用自定义轻提示 + 按钮) 
           为了满足题目“弹出提示框有两个按钮” 我们采用悬浮 modal，不干扰玩法 */
        .custom-alert {
            position: fixed;
            top: 0; left: 0; width: 100%; height: 100%;
            background-color: rgba(0, 0, 0, 0.7);
            backdrop-filter: blur(8px);
            display: flex;
            justify-content: center;
            align-items: center;
            z-index: 999;
            transition: opacity 0.2s;
        }

        .alert-box {
            background: linear-gradient(145deg, #2c1d4b, #130a28);
            border: 2px solid #9f8adf;
            border-radius: 56px;
            padding: 36px 48px;
            max-width: 380px;
            width: 90%;
            text-align: center;
            box-shadow: 0 30px 40px #00000099, inset 0 0 30px #6a50c0;
            color: white;
        }

        .alert-box p {
            font-size: 2rem;
            margin: 0 0 32px 0;
            font-weight: 500;
            font-family: 'Quicksand', sans-serif;
            text-shadow: 0 2px 10px violet;
            letter-spacing: 2px;
        }

        .button-group {
            display: flex;
            gap: 24px;
            justify-content: center;
            flex-wrap: wrap;
        }

        .alert-btn {
            background: #34245e;
            border: none;
            border-bottom: 5px solid #0b0520;
            color: white;
            font-size: 1.6rem;
            font-weight: 600;
            padding: 14px 28px;
            border-radius: 60px;
            min-width: 140px;
            cursor: pointer;
            transition: all 0.08s linear;
            box-shadow: 0 10px 15px -5px black;
            font-family: 'Quicksand', 'Segoe UI', sans-serif;
            letter-spacing: 1px;
        }

        .alert-btn:active {
            transform: translateY(4px);
            border-bottom-width: 2px;
        }

        .alert-btn:first-child {
            background: #542e6b;
        }

        .alert-btn:last-child {
            background: #305f6b;
        }

        .hidden {
            display: none !important;
        }

        /* 胜利浮层也一样复用，但文字不同 */
        .victory .alert-box p {
            font-size: 2.8rem;
            background: linear-gradient(135deg, gold, #ffeeaa);
            -webkit-background-clip: text;
            background-clip: text;
            color: transparent;
        }

        /* 一些辅助 */
        .info-row {
            display: flex;
            justify-content: center;
            margin: 24px 0 8px;
            color: #cbb7fe;
            font-size: 1.3rem;
            gap: 36px;
            font-weight: 400;
            letter-spacing: 1px;
        }

        .remain-counter {
            background: #1b1130;
            padding: 6px 24px;
            border-radius: 40px;
            border: 1px solid #7059b0;
            box-shadow: inset 0 0 6px #221a3a;
        }

        footer {
            text-align: center;
            color: #53457a;
            margin-top: 24px;
            font-size: 0.95rem;
        }
    </style>
</head>
<body>
<div class="game-container">
    <div class="title-section">
        <h1>扫雷</h1>
    </div>

    <!-- 蓝紫立体三角背景 (SVG) -->
    <div class="board-wrapper">
        <div class="triangle-bg">
            <svg viewBox="0 0 300 200" preserveAspectRatio="xMidYMid meet">
                <defs>
                    <linearGradient id="triGrad" x1="0%" y1="0%" x2="100%" y2="100%">
                        <stop offset="0%" stop-color="#8f6ef0" stop-opacity="0.8" />
                        <stop offset="40%" stop-color="#4a2c9e" stop-opacity="0.7" />
                        <stop offset="80%" stop-color="#281b50" stop-opacity="0.9" />
                        <stop offset="100%" stop-color="#13082e" stop-opacity="0.7" />
                    </linearGradient>
                    <filter id="glow3d" x="-20%" y="-20%" width="140%" height="140%">
                        <feGaussianBlur stdDeviation="6" result="coloredBlur"/>
                        <feMerge>
                            <feMergeNode in="coloredBlur"/>
                            <feMergeNode in="SourceGraphic"/>
                        </feMerge>
                    </filter>
                </defs>
                <!-- 立体三角组 -->
                <polygon points="30,160 150,10 270,160" fill="url(#triGrad)" filter="url(#glow3d)" stroke="#b292ff" stroke-width="2" opacity="0.9"/>
                <polygon points="50,150 150,40 250,150" fill="#513c9e" filter="url(#glow3d)" stroke="#ccb5ff" stroke-width="1.5" opacity="0.5"/>
                <polygon points="90,180 150,80 210,180" fill="#2f1d62" stroke="#aa92f0" stroke-width="2" opacity="0.7"/>
                <polygon points="20,180 150,70 280,180" fill="#412f79" fill-opacity="0.4" stroke="#bcaaff" stroke-width="2" stroke-dasharray="3 5"/>
            </svg>
        </div>

        <!-- 3x3 格子群 -->
        <div class="grid-3x3" id="gridContainer"></div>
    </div>

    <!-- 剩余空白格子提示（仅作观赏） -->
    <div class="info-row">
        <span class="remain-counter" id="blankCounter">空白剩余 9</span>
    </div>

    <!-- 自定义弹框: 炸弹提示 (两个按钮)  -->
    <div id="bombAlert" class="custom-alert hidden">
        <div class="alert-box">
            <p>💣 踩到炸弹 !</p>
            <div class="button-group">
                <button class="alert-btn" id="restartBtn">🔁 重新游戏</button>
                <button class="alert-btn" id="adKeepBtn">📺 看广告保留</button>
            </div>
        </div>
    </div>

    <!-- 胜利弹框 (自动消失，但为了操作体验也放两个占位按钮, 但按需求显示胜利后三秒自动新游戏，保留一个手动restart也可以但3秒后自动触发) 
         不过既然题目明确显示"已胜利"后三秒开始下一次，我们做一个只展示文字的浮层并且三秒后消失并重置。同时也可以给个确认按钮但无伤大雅。-->
    <div id="victoryAlert" class="custom-alert hidden victory">
        <div class="alert-box">
            <p>🏆 已胜利</p>
            <div class="button-group" style="justify-content: center;">
                <button class="alert-btn" id="victoryOkBtn" style="background:#5a4a8a; min-width:100px;">👌 好的</button>
            </div>
        </div>
    </div>

    <footer style="margin-top: 10px;">⚡ 蓝紫立体三角 · 1/5炸弹概率</footer>
</div>

<script>
    (function(){
        // 游戏配置
        const SIZE = 3;
        const BOMB_PROB = 0.2;   // 1/5

        // 全局状态
        let board = [];             // 二维布尔: true=炸弹
        let revealed = [];          // false=未按, true=已按下(显示)
        let gameActive = true;       // 当炸弹触发或胜利后禁止再点击格子
        let bombAlertEl = document.getElementById('bombAlert');
        let victoryAlertEl = document.getElementById('victoryAlert');
        let gridContainer = document.getElementById('gridContainer');
        let blankCounterSpan = document.getElementById('blankCounter');

        // 辅助: 更新剩余空白计数
        function updateBlankCount() {
            if (!gameActive) return; // 游戏冻结不刷新计数也可以，但保留
            let count = 0;
            for (let r = 0; r < SIZE; r++) {
                for (let c = 0; c < SIZE; c++) {
                    if (!revealed[r][c]) count++;  // 没按的格子（包括炸弹空白未按都算）
                }
            }
            blankCounterSpan.textContent = `空白剩余 ${count}`;
            return count;
        }

        // 初始化/重置棋盘 (随机生成炸弹)
        function initBoard() {
            board = Array.from({ length: SIZE }, () => 
                Array.from({ length: SIZE }, () => Math.random() < BOMB_PROB)
            );
            // 重置revealed
            revealed = Array.from({ length: SIZE }, () => Array(SIZE).fill(false));
            gameActive = true;

            // 重新渲染格子外观
            renderGrid();
            updateBlankCount();

            // 确保所有弹窗隐藏
            bombAlertEl.classList.add('hidden');
            victoryAlertEl.classList.add('hidden');
        }

        // 胜利判定: 所有非炸弹格子都被按下 (且没有触发炸弹)
        function checkVictory() {
            for (let r = 0; r < SIZE; r++) {
                for (let c = 0; c < SIZE; c++) {
                    // 如果当前格子不是炸弹 且 没有被按下 -> 还不能胜利
                    if (!board[r][c] && !revealed[r][c]) {
                        return false;
                    }
                }
            }
            return true;
        }

        // 处理胜利: 展示胜利弹框，3秒后自动重置
        function handleVictory() {
            if (!gameActive) return; // 已结束不重复触发
            gameActive = false;

            // 显示胜利弹窗
            victoryAlertEl.classList.remove('hidden');

            // 3秒后自动开始新游戏 (隐藏弹窗+重置)
            setTimeout(() => {
                // 如果弹窗还在（有可能被手动关闭了，但手动我们加一个监听）
                victoryAlertEl.classList.add('hidden');
                // 重置棋盘
                initBoard();
            }, 3000);
        }

        // 踩到炸弹: 展示炸弹弹窗 (游戏冻结)
        function handleBomb(row, col) {
            if (!gameActive) return;
            gameActive = false;

            // 将当前炸弹格子标记特殊样式（已按炸弹）
            // 可以渲染时加类，这里直接操作dom加效果，不过重新刷新grid用render更好
            // 但为保留按下去的视觉效果，我们调用renderGrid且把对应格子特殊表示
            // 简单在render方法里根据一个额外的bombPressed坐标? 
            // 更简单: 直接重绘，但是炸弹格子显示红色炸弹符号。
            // 同时让炸弹alert显示。
            renderGrid(true, row, col);  // 传入当前踩的炸弹坐标用于高亮

            bombAlertEl.classList.remove('hidden');
        }

        // 格子点击处理
        function onCellClick(row, col) {
            // 如果游戏已冻结，或者格子已经被按下，忽略
            if (!gameActive || revealed[row][col]) return;

            // 如果是炸弹
            if (board[row][col]) {
                // 踩到炸弹
                handleBomb(row, col);
                return;
            }

            // 否则是空白格子: 标记为按下
            revealed[row][col] = true;
            // 更新UI
            renderGrid();

            // 检查胜利 (所有空白按完)
            if (checkVictory()) {
                handleVictory();
            } else {
                updateBlankCount();
            }
        }

        // 绘制格子 (可传入炸弹高亮坐标)
        function renderGrid(highlightBomb = false, bombRow = -1, bombCol = -1) {
            let html = '';
            for (let r = 0; r < SIZE; r++) {
                for (let c = 0; c < SIZE; c++) {
                    let cellClass = 'cell';
                    let content = '';

                    // 已按下格子 或者 游戏冻结时如果是炸弹但未revealed? 我们选择展示? 但为了美观，炸弹只有按下才显示💣
                    if (revealed[r][c]) {
                        cellClass += ' pressed';
                        if (board[r][c]) {
                            // 炸弹被按下 (理论上炸弹按下会触发handleBomb，revealed不会为true, 但以防万一)
                            content = '💣';
                            cellClass += ' mine-pressed';
                        } else {
                            // 空白按下显示安全标志 可以放个小白点或空
                            content = '·';
                        }
                    } else {
                        // 未按下格子: 如果游戏冻结且当前是炸弹坐标高亮（比如触发炸弹后高亮该炸弹）
                        if (highlightBomb && r === bombRow && c === bombCol && board[r][c]) {
                            cellClass += ' mine-pressed';   // 高亮炸弹样式，但不显示revealed符号（可以加个炸弹表情）
                            content = '💣';   // 让玩家看到炸弹位置
                        } else {
                            // 普通未按下格子显示? 可以显示一个小点或者空
                            content = ''; 
                        }
                    }

                    // 额外: 如果游戏冻结但不是高亮炸弹，且格子未revealed但board是炸弹，我们不在未按下时暴露(依旧显示空白)
                    // 但为了友好，炸弹弹窗时不需要全盘暴露，只暴露踩到的那个。题目无要求，合理。

                    html += `<div class="${cellClass}" data-row="${r}" data-col="${c}">${content}</div>`;
                }
            }
            gridContainer.innerHTML = html;

            // 重新绑定事件 (轻量级)
            document.querySelectorAll('.cell').forEach(cellDiv => {
                cellDiv.addEventListener('click', (e) => {
                    const row = parseInt(cellDiv.dataset.row);
                    const col = parseInt(cellDiv.dataset.col);
                    onCellClick(row, col);
                });
            });
            updateBlankCount(); // 确保计数器刷新
        }

        // --- 弹窗按钮事件 ---
        document.getElementById('restartBtn').addEventListener('click', () => {
            // 重新游戏: 隐藏弹窗, 重置棋盘
            bombAlertEl.classList.add('hidden');
            initBoard();
        });

        document.getElementById('adKeepBtn').addEventListener('click', () => {
            // 模拟看广告保留进度: 关闭弹窗，恢复游戏Active，但是保留当前棋盘状态(炸弹依然存在)
            // 不过注意：刚才踩到的炸弹已经触发了炸弹格子，但我们之前把游戏冻结了，revealed没有标记炸弹。
            // 保留进度继续游戏，代表玩家没死，游戏继续，那我们要把踩到的炸弹怎么办？ 
            // 根据常见广告保留逻辑: 一般消除当前炸弹或者复活。但题目简单: “保留进度继续游戏” 且没有广告源，按第二个直接保留进度。
            // 意味着炸弹仍然在，但玩家可以继续点其他格子。那刚刚踩到的炸弹如何处理？为了不立即再次引爆，我们可以选择将那个炸弹格子变成安全（或者移除炸弹？）
            // 但是题目没有明确炸弹消失，但保留进度继续玩若炸弹还在原地，玩家再次点击同一个炸弹又会弹提示。为避免无限循环，我们采取：把当前炸弹格子变为非炸弹(安全)并revealed按下，这样既保留进度又不会再次引爆。逻辑通顺。  
            // 同时玩家可以看到那个格子变安全。符合保留进度的“仁慈”。或者也可标记为已按，但不显示炸弹。我们选用 将该炸弹移除并标记已按。
            if (!gameActive) {
                // 找到最近触发炸弹的格子 (可以从渲染时传入的坐标? 但事件已过, 简单处理: 遍历board找到第一个炸弹且未revealed? 风险。 
                // 但是因为我们之前触发了handleBomb时传入了row,col, 可以存储最后触发炸弹位置。增加一个变量
                if (lastBombRow !== -1 && lastBombCol !== -1) {
                    let r = lastBombRow, c = lastBombCol;
                    // 将该格变为非炸弹，并设为已按下 (安全)
                    board[r][c] = false;          // 移除炸弹
                    revealed[r][c] = true;         // 显示按下
                }
            }
            // 恢复游戏active，关闭弹窗，重绘
            gameActive = true;
            bombAlertEl.classList.add('hidden');
            // 重新渲染，同时检查是否胜利 (因为改变了一个格子)
            renderGrid();
            if (checkVictory()) {
                handleVictory();
            } else {
                updateBlankCount();
            }
        });

        // 胜利弹框的手动关闭按钮 (用户点“好的”直接关闭弹窗，但游戏已胜利冻结? 但我们根据题目3秒后自动重置，手动关闭也不影响重置)
        document.getElementById('victoryOkBtn').addEventListener('click', () => {
            victoryAlertEl.classList.add('hidden');
            // 如果游戏因胜利冻结，手动关闭后未重置，但3秒后自动重置。也可以立即重置（但题目要求三秒后重置，保留自动重置即可）
            // 但玩家点好的后，希望不等待? 我们优化: 点了好的立即重置 (但遵守3秒？稳妥做法：点好的直接重置并关闭)
            // 但是题目明确：“弹出提示框显示已胜利，三秒后开始下一次游戏”，意味着无论是否点击按钮，三秒后重置。 
            // 我们保留自动重置，同时点好的仅关闭弹窗不干扰倒计时。但弹窗关闭后重置时还会再次弹起，没关系。
            // 但是如果玩家点好的后关闭了，三秒后我们会再次隐藏(已经隐藏了没影响)并重置棋盘。那就OK。
            // 不需要额外操作，因为victoryAlert隐藏但timer会触发initBoard。
        });

        // 记录最后踩到的炸弹坐标 (用于广告保留)
        let lastBombRow = -1, lastBombCol = -1;

        // 覆盖handleBomb 记录last
        const originalHandleBomb = handleBomb;
        handleBomb = function(row, col) {
            lastBombRow = row;
            lastBombCol = col;
            originalHandleBomb.call(this, row, col);
        }.bind(this);

        // 绑定函数(确保this, 直接重写)
        // 简单替换
        window.handleBomb = function(row, col) {
            lastBombRow = row;
            lastBombCol = col;
            if (!gameActive) return;
            gameActive = false;
            renderGrid(true, row, col);
            bombAlertEl.classList.remove('hidden');
        };

        // 重新绑定onCellClick里的handleBomb调用
        // 因为上面function hoisting, 我们修改函数引用
        onCellClick = function(row, col) {
            if (!gameActive || revealed[row][col]) return;
            if (board[row][col]) {
                // 调用新的handleBomb
                window.handleBomb(row, col);
                return;
            }
            revealed[row][col] = true;
            renderGrid();
            if (checkVictory()) {
                handleVictory();
            } else {
                updateBlankCount();
            }
        };

        // 启动游戏
        initBoard();

        // 为了保证变量一致，把函数挂载
        window.checkVictory = checkVictory;
        window.handleVictory = handleVictory;
    })();
</script>
</body>
</html>