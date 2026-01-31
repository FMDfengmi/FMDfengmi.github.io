# FMDfengmi.github.io
FMDfengmi.github.io
<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>5×5扫雷小游戏</title>
    <style>
        /* 页面基础样式 - 蓝紫三角形背景+全屏铺满 */
        * { margin: 0; padding: 0; box-sizing: border-box; }
        body { 
            background: linear-gradient(135deg, #4158D0 0%, #C850C0 50%, #FFCC70 100%);
            background-image: 
                repeating-linear-gradient(45deg, rgba(255,255,255,0.05) 0px, rgba(255,255,255,0.05) 2px, transparent 2px, transparent 10px),
                repeating-linear-gradient(135deg, rgba(255,255,255,0.05) 0px, rgba(255,255,255,0.05) 2px, transparent 2px, transparent 10px);
            min-height: 100vh;
            font-family: Arial, sans-serif; display: flex; flex-direction: column; align-items: center; padding: 20px 0;
        }
        h1 { margin-bottom: 25px; color: #fff; font-size: 24px; text-shadow: 0 0 8px rgba(0,0,0,0.3); }
        /* 胜利次数显示 */
        .win-count { color: #fff; font-size: 18px; margin-bottom: 10px; text-shadow: 0 0 4px rgba(0,0,0,0.3); }

        /* 扫雷格子容器 */
        .mine-grid { display: grid; grid-template-columns: repeat(5, 60px); gap: 8px; }
        /* 格子样式 - 未点击=纯红色 重点修改！ */
        .grid-cell { 
            width: 60px; height: 60px; 
            background: #ff3333; border: 2px solid #b82222; border-radius: 6px; 
            cursor: pointer; display: flex; align-items: center; justify-content: center; 
            font-size: 20px; font-weight: bold; transition: all 0.2s; color:#fff;
        }
        .grid-cell:hover { background: #e02e2e; border-color: #991e1e; }
        /* 点击后的格子=蓝紫渐变背景色 重点修改！ */
        .grid-cell.clicked { 
            background: linear-gradient(45deg, #6a7efc, #c06cf0); 
            border-color: #4158D0; cursor: default; color:#fff;
        }
        /* 炸弹格子保留红底突出警示 */
        .grid-cell.bomb { background: #ff0000; color: #fff; border-color: #990000; }

        /* 弹窗遮罩 */
        .modal-mask { position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: rgba(0,0,0,0.7); display: none; align-items: center; justify-content: center; z-index: 100; }
        /* 弹窗内容 */
        .modal-content { background: #fff; padding: 30px; border-radius: 10px; width: 80%; max-width: 300px; text-align: center; }
        .modal-content h2 { margin-bottom: 20px; color: #333; }
        /* 弹窗按钮 */
        .modal-btn { padding: 10px 20px; margin: 0 10px; border: none; border-radius: 6px; cursor: pointer; font-size: 16px; }
        .restart-btn { background: #4CAF50; color: #fff; }
        .ad-btn { background: #2196F3; color: #fff; }
    </style>
</head>
<body>
    <h1>5×5扫雷小游戏</h1>
    <!-- 胜利次数显示 -->
    <div class="win-count">当前胜利次数：<span id="winCount">0</span></div>
    <!-- 扫雷格子容器 -->
    <div class="mine-grid" id="mineGrid"></div>

    <!-- 炸弹触发弹窗 -->
    <div class="modal-mask" id="bombModal">
        <div class="modal-content">
            <h2>踩到炸弹啦！</h2>
            <button class="modal-btn restart-btn" id="restartBtn">重新游戏</button>
            <button class="modal-btn ad-btn" id="adBtn">观看广告继续游戏</button>
        </div>
    </div>

    <!-- 背景音乐+点击音效 -->
    <audio id="bgMusic" loop autoplay muted>
        <source src="https://assets.mixkit.co/sfx/preview/mixkit-game-level-music-689.mp3" type="audio/mpeg">
    
    <audio id="clickSound">
        <source src="https://assets.mixkit.co/sfx/preview/mixkit-select-click-1109.mp3" type="audio/mpeg">
    
    <audio id="bombSound">
        <source src="https://assets.mixkit.co/sfx/preview/mixkit-arcade-retro-game-over-213.mp3" type="audio/mpeg">
    

    <script>
        const gridEl = document.getElementById('mineGrid'); // 格子容器
        const modalEl = document.getElementById('bombModal'); // 弹窗
        const restartBtn = document.getElementById('restartBtn'); // 重新游戏按钮
        const adBtn = document.getElementById('adBtn'); // 广告续玩按钮
        const winCountEl = document.getElementById('winCount'); // 胜利次数显示元素
        let gridCells = []; // 格子数组（存储每个格子状态）
        let winCount = 0; // 胜利次数累计
        // 音乐音效获取
        const bgMusic = document.getElementById('bgMusic');
        const clickSound = document.getElementById('clickSound');
        const bombSound = document.getElementById('bombSound');
        // 打开背景音乐（静音取消）
        bgMusic.muted = false;

        // 初始化游戏（创建格子、随机埋炸弹）
        function initGame() {
            gridEl.innerHTML = ''; // 清空旧格子
            gridCells = []; // 重置格子状态
            // 循环创建25个格子
            for (let i = 0; i < 25; i++) {
                const cell = document.createElement('div');
                cell.className = 'grid-cell';
                gridEl.appendChild(cell);
                // 随机设炸弹：1/4概率为true（炸弹），3/4为false（空白）
                const isBomb = Math.random() < 0.25;
                gridCells.push({ el: cell, isBomb, clicked: false });

                // 格子点击事件
                cell.addEventListener('click', () => handleCellClick(i));
            }
        }

        // 格子点击处理
        function handleCellClick(index) {
            const cell = gridCells[index];
            if (cell.clicked) return; // 已点击过不重复触发
            cell.clicked = true;
            cell.el.classList.add('clicked');
            // 播放点击音效
            clickSound.currentTime = 0;
            clickSound.play();

            // 踩到炸弹：显示弹窗+炸弹音效
            if (cell.isBomb) {
                cell.el.classList.add('bomb');
                cell.el.textContent = '💣'; // 显示炸弹图标
                bombSound.currentTime = 0;
                bombSound.play();
                modalEl.style.display = 'flex'; // 打开弹窗
            } else {
                // 空白格子：计算周围炸弹数量
                const bombCount = countSurroundBombs(index);
                cell.el.textContent = bombCount > 0 ? bombCount : '';
                // 检查是否胜利（所有非炸弹格子都被点击）
                checkWin();
            }
        }

        // 计算当前格子周围炸弹数量
        function countSurroundBombs(index) {
            let count = 0;
            const row = Math.floor(index / 5); // 当前格子行号（0-4）
            const col = index % 5; // 当前格子列号（0-4）
            // 循环遍历周围8个格子
            for (let r = row - 1; r <= row + 1; r++) {
                for (let c = col - 1; c <= col + 1; c++) {
                    // 排除自身、超出5×5范围的格子
                    if (r === row && c === col) continue;
                    if (r >= 0 && r < 5 && c >= 0 && c < 5) {
                        const surroundIndex = r * 5 + c;
                        if (gridCells[surroundIndex].isBomb) count++;
                    }
                }
            }
            return count;
        }

        // 检查游戏胜利条件
        function checkWin() {
            // 统计未点击的非炸弹格子数量
            const unclickedSafe = gridCells.filter(cell => !cell.isBomb && !cell.clicked).length;
            if (unclickedSafe === 0) {
                // 胜利次数+1
                winCount++;
                winCountEl.textContent = winCount;
                // 胜利提示
                alert('恭喜胜利！');
                // 检查是否累计3次胜利
                if (winCount === 3) {
                    alert('未中奖');
                    // 可选：重置胜利次数
                    // winCount = 0;
                    // winCountEl.textContent = winCount;
                }
                // 胜利后初始化新游戏
                initGame();
            }
        }

        // 重新游戏按钮：关闭弹窗+初始化新游戏，重置当前局进度
        restartBtn.addEventListener('click', () => {
            modalEl.style.display = 'none';
            initGame();
        });

        // 广告续玩按钮：关闭弹窗，移除炸弹标记，保留已点击进度
        adBtn.addEventListener('click', () => {
            alert('模拟观看广告中...广告播放完毕'); // 替代实际广告源
            modalEl.style.display = 'none'; // 关闭弹窗
            // 清除所有炸弹格子的样式和文字
            gridCells.forEach(cell => {
                if (cell.isBomb) {
                    cell.el.classList.remove('bomb');
                    cell.el.textContent = '';
                }
            });
        });

        // 首次启动游戏
        initGame();
    </script>
</body>
</html>