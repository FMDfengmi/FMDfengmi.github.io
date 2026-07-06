<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>墨痕中转站</title>
    <!-- 引入 Google Font 行书字体：马善政毛笔行书 -->
    <link href="https://fonts.googleapis.com/css2?family=Ma+Shan+Zheng&display=swap" rel="stylesheet" />
    <style>
        /* ===== 全局重置 & 基础 ===== */
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            background-color: #ffffff;
            font-family: "PingFang SC", "Microsoft YaHei", sans-serif;
            min-height: 100vh;
            padding: 30px 40px 60px;
            /* 为左上角标题留出空间 */
            padding-top: 100px;
        }

        /* ===== 左上角标题 ===== */
        .site-title {
            position: fixed;
            top: 28px;
            left: 40px;
            font-family: "Ma Shan Zheng", "华文行楷", "STXingkai", cursive;
            font-size: 40px;
            font-weight: normal;
            color: #1a1a1a;
            letter-spacing: 6px;
            z-index: 100;
            text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.06);
            user-select: none;
            pointer-events: none;
        }
        /* 小屏适配 */
        @media (max-width: 768px) {
            .site-title {
                font-size: 28px;
                top: 18px;
                left: 20px;
                letter-spacing: 4px;
            }
            body {
                padding: 70px 16px 40px;
                padding-top: 80px;
            }
        }

        /* ===== 网格容器 ===== */
        .grid {
            display: grid;
            grid-template-columns: repeat(6, 1fr);
            gap: 18px;
            max-width: 1440px;
            margin: 0 auto;
        }

        /* ===== 每个卡片（框） ===== */
        .card {
            background-color: #ffffff;
            border: 4px solid #1a1a1a;
            /* 较厚黑色边框 */
            border-radius: 18px;
            /* 圆角长方形 */
            padding: 18px 14px 16px;
            cursor: pointer;
            transition: transform 0.15s ease, box-shadow 0.15s ease;
            display: flex;
            flex-direction: column;
            justify-content: center;
            min-height: 118px;
            user-select: none;
            position: relative;
        }
        .card:hover {
            transform: translateY(-3px);
            box-shadow: 0 8px 24px rgba(0, 0, 0, 0.10);
        }
        .card:active {
            transform: scale(0.97);
        }

        /* 卡片内序号 */
        .card .index {
            font-size: 13px;
            font-weight: 600;
            color: #888;
            margin-bottom: 4px;
            letter-spacing: 0.5px;
        }
        /* 卡片内网站名称 */
        .card .name {
            font-size: 20px;
            font-weight: 700;
            color: #1a1a1a;
            line-height: 1.3;
            margin-bottom: 4px;
            word-break: break-word;
        }
        /* 卡片内 URL */
        .card .url {
            font-size: 12px;
            color: #999;
            word-break: break-all;
            line-height: 1.4;
            font-family: "SF Mono", "Menlo", monospace;
            opacity: 0.75;
        }

        /* ===== 响应式：不同屏幕下的列数 ===== */
        @media (max-width: 1200px) {
            .grid {
                grid-template-columns: repeat(5, 1fr);
                gap: 16px;
            }
        }
        @media (max-width: 992px) {
            .grid {
                grid-template-columns: repeat(4, 1fr);
                gap: 14px;
            }
            .card .name {
                font-size: 18px;
            }
        }
        @media (max-width: 640px) {
            .grid {
                grid-template-columns: repeat(3, 1fr);
                gap: 12px;
            }
            .card {
                padding: 14px 10px 12px;
                min-height: 96px;
                border-width: 3px;
                border-radius: 14px;
            }
            .card .name {
                font-size: 16px;
            }
            .card .url {
                font-size: 10px;
            }
            .card .index {
                font-size: 11px;
            }
        }
        @media (max-width: 440px) {
            .grid {
                grid-template-columns: repeat(2, 1fr);
                gap: 10px;
            }
            .card {
                min-height: 80px;
                padding: 10px 8px 10px;
            }
            .card .name {
                font-size: 14px;
            }
            .card .url {
                font-size: 9px;
            }
        }

        /* ===== 弹窗遮罩 ===== */
        .modal-overlay {
            display: none;
            /* 默认隐藏 */
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background-color: rgba(0, 0, 0, 0.55);
            z-index: 999;
            justify-content: center;
            align-items: center;
            backdrop-filter: blur(4px);
            animation: fadeIn 0.25s ease;
        }
        .modal-overlay.active {
            display: flex;
        }

        /* ===== 弹窗主体 ===== */
        .modal-box {
            background: #ffffff;
            border-radius: 24px;
            max-width: 520px;
            width: 92%;
            padding: 36px 32px 30px;
            box-shadow: 0 30px 60px rgba(0, 0, 0, 0.30);
            position: relative;
            animation: scaleIn 0.30s ease;
            text-align: center;
            border: 3px solid #1a1a1a;
        }
        @media (max-width: 480px) {
            .modal-box {
                padding: 26px 18px 22px;
                border-radius: 20px;
            }
        }

        /* 弹窗标题（提示文字） */
        .modal-title {
            font-size: 22px;
            font-weight: 600;
            color: #1a1a1a;
            margin-bottom: 18px;
            line-height: 1.5;
            letter-spacing: 1px;
        }
        .modal-title .emoji {
            font-size: 26px;
            display: inline-block;
            margin-right: 6px;
        }

        /* ===== 广告区域（初始隐藏） ===== */
        .ad-container {
            display: none;
            /* 默认隐藏 */
            background: #f7f7f9;
            border-radius: 16px;
            padding: 20px 18px 22px;
            margin: 12px 0 20px;
            border: 2px dashed #bbb;
            transition: all 0.3s ease;
        }
        .ad-container.show {
            display: block;
            animation: fadeIn 0.4s ease;
        }

        .ad-container .ad-header {
            font-size: 18px;
            font-weight: 700;
            color: #d43b3b;
            margin-bottom: 10px;
            letter-spacing: 1px;
        }
        .ad-container .ad-body {
            font-size: 16px;
            color: #333;
            line-height: 1.7;
            margin-bottom: 14px;
            padding: 0 4px;
        }
        .ad-container .ad-body .highlight {
            color: #c0392b;
            font-weight: 600;
        }
        /* 进度条外壳 */
        .ad-progress-wrap {
            width: 100%;
            height: 8px;
            background: #e0e0e0;
            border-radius: 6px;
            overflow: hidden;
            margin-top: 6px;
        }
        .ad-progress-bar {
            height: 100%;
            width: 0%;
            background: linear-gradient(90deg, #f39c12, #e74c3c);
            border-radius: 6px;
            transition: width 0.3s linear;
        }
        .ad-timer {
            font-size: 14px;
            color: #888;
            margin-top: 10px;
            font-weight: 500;
        }

        /* ===== 弹窗按钮组 ===== */
        .modal-buttons {
            display: flex;
            gap: 14px;
            justify-content: center;
            flex-wrap: wrap;
            margin-top: 6px;
        }
        .modal-buttons button {
            flex: 1 1 auto;
            min-width: 110px;
            padding: 13px 22px;
            border: none;
            border-radius: 60px;
            font-size: 17px;
            font-weight: 600;
            cursor: pointer;
            transition: all 0.2s ease;
            letter-spacing: 1px;
            border: 2px solid transparent;
        }
        .modal-buttons button:active {
            transform: scale(0.95);
        }

        /* 观看广告 按钮 */
        .btn-watch {
            background: #1a1a1a;
            color: #ffffff;
            border-color: #1a1a1a;
        }
        .btn-watch:hover:not(:disabled) {
            background: #333;
            border-color: #333;
            transform: translateY(-2px);
            box-shadow: 0 6px 16px rgba(0, 0, 0, 0.15);
        }
        .btn-watch:disabled {
            background: #b0b0b0;
            border-color: #b0b0b0;
            cursor: not-allowed;
            opacity: 0.7;
            transform: none !important;
            box-shadow: none !important;
        }

        /* 直接跳转 按钮 */
        .btn-skip {
            background: #ffffff;
            color: #1a1a1a;
            border-color: #1a1a1a;
        }
        .btn-skip:hover {
            background: #f0f0f0;
            transform: translateY(-2px);
            box-shadow: 0 6px 16px rgba(0, 0, 0, 0.08);
        }

        /* ===== 动画 ===== */
        @keyframes fadeIn {
            from {
                opacity: 0;
            }
            to {
                opacity: 1;
            }
        }
        @keyframes scaleIn {
            from {
                transform: scale(0.88);
                opacity: 0;
            }
            to {
                transform: scale(1);
                opacity: 1;
            }
        }

        /* ===== 滚动条美化（可选） ===== */
        ::-webkit-scrollbar {
            width: 6px;
            height: 6px;
        }
        ::-webkit-scrollbar-track {
            background: #f1f1f1;
        }
        ::-webkit-scrollbar-thumb {
            background: #bbb;
            border-radius: 8px;
        }
        ::-webkit-scrollbar-thumb:hover {
            background: #999;
        }
    </style>
</head>
<body>

    <!-- ===== 左上角标题 ===== -->
    <div class="site-title">墨痕中转站</div>

    <!-- ===== 网格容器 ===== -->
    <div class="grid" id="gridContainer"></div>

    <!-- ===== 弹窗遮罩 ===== -->
    <div class="modal-overlay" id="modalOverlay">
        <div class="modal-box">
            <!-- 提示文字 -->
            <div class="modal-title">
                <span class="emoji">📢</span> 请观看广告以示对我们的支持
            </div>

            <!-- 广告区域 -->
            <div class="ad-container" id="adContainer">
                <div class="ad-header">🎬 广告时间</div>
                <div class="ad-body" id="adBody">
                    <span class="highlight">✨ 精选好物 · 品质生活 ✨</span><br />
                    限时特惠 · 全场低至3折 · 点击了解详情
                </div>
                <!-- 进度条 -->
                <div class="ad-progress-wrap">
                    <div class="ad-progress-bar" id="adProgressBar"></div>
                </div>
                <div class="ad-timer" id="adTimer">⏳ 剩余 5 秒</div>
            </div>

            <!-- 按钮组 -->
            <div class="modal-buttons">
                <button class="btn-watch" id="watchAdBtn">观看广告</button>
                <button class="btn-skip" id="skipBtn">直接跳转</button>
            </div>
        </div>
    </div>

    <script>
        // ============================================================
        //  1. 数据：30 个网站
        // ============================================================
        const sites = [
            { id: 1, name: '百度', url: 'https://www.baidu.com' },
            { id: 2, name: '知乎', url: 'https://www.zhihu.com' },
            { id: 3, name: '今日头条', url: 'https://www.toutiao.com' },
            { id: 4, name: '澎湃新闻', url: 'https://www.thepaper.cn' },
            { id: 5, name: '豆瓣', url: 'https://www.douban.com' },
            { id: 6, name: '淘宝', url: 'https://www.taobao.com' },
            { id: 7, name: '京东', url: 'https://www.jd.com' },
            { id: 8, name: '拼多多', url: 'https://www.pinduoduo.com' },
            { id: 9, name: '唯品会', url: 'https://www.vip.com' },
            { id: 10, name: '1688', url: 'https://www.1688.com' },
            { id: 11, name: 'B站', url: 'https://www.bilibili.com' },
            { id: 12, name: '腾讯视频', url: 'https://v.qq.com' },
            { id: 13, name: '网易云音乐', url: 'https://music.163.com' },
            { id: 14, name: '爱奇艺', url: 'https://www.iqiyi.com' },
            { id: 15, name: '芒果TV', url: 'https://www.mgtv.com' },
            { id: 16, name: '石墨文档', url: 'https://shimo.im' },
            { id: 17, name: '腾讯文档', url: 'https://docs.qq.com' },
            { id: 18, name: '中国大学MOOC', url: 'https://www.icourse163.org' },
            { id: 19, name: '道客巴巴', url: 'https://www.doc88.com' },
            { id: 20, name: '菜鸟工具', url: 'https://c.runoob.com' },
            { id: 21, name: '12306', url: 'https://www.12306.cn' },
            { id: 22, name: '高德地图网页版', url: 'https://amap.com' },
            { id: 23, name: '国家政务服务平台', url: 'https://gjzwfw.www.gov.cn' },
            { id: 24, name: '美团网页版', url: 'https://www.meituan.com' },
            { id: 25, name: '携程旅行', url: 'https://www.ctrip.com' },
            { id: 26, name: '稿定设计', url: 'https://www.gaoding.com' },
            { id: 27, name: '千库网', url: 'https://www.588ku.com' },
            { id: 28, name: '快递100', url: 'https://www.kuaidi100.com' },
            { id: 29, name: '天气预报', url: 'http://www.weather.com.cn' },
            { id: 30, name: '支付宝网页版', url: 'https://www.alipay.com' }
        ];

        // ============================================================
        //  2. 渲染网格
        // ============================================================
        const grid = document.getElementById('gridContainer');

        sites.forEach(site => {
            const card = document.createElement('div');
            card.className = 'card';
            // 用 data-url 存储目标地址
            card.dataset.url = site.url;

            const idxDiv = document.createElement('div');
            idxDiv.className = 'index';
            idxDiv.textContent = site.id + '.';

            const nameDiv = document.createElement('div');
            nameDiv.className = 'name';
            nameDiv.textContent = site.name;

            const urlDiv = document.createElement('div');
            urlDiv.className = 'url';
            urlDiv.textContent = site.url;

            card.appendChild(idxDiv);
            card.appendChild(nameDiv);
            card.appendChild(urlDiv);

            // 点击卡片 → 打开弹窗
            card.addEventListener('click', function(e) {
                const targetUrl = this.dataset.url;
                openModal(targetUrl);
            });

            grid.appendChild(card);
        });

        // ============================================================
        //  3. 弹窗控制
        // ============================================================
        const overlay = document.getElementById('modalOverlay');
        const adContainer = document.getElementById('adContainer');
        const adProgressBar = document.getElementById('adProgressBar');
        const adTimer = document.getElementById('adTimer');
        const watchBtn = document.getElementById('watchAdBtn');
        const skipBtn = document.getElementById('skipBtn');

        let currentTargetUrl = ''; // 当前要跳转的 URL
        let adTimerId = null; // 倒计时定时器
        let adSeconds = 5; // 广告倒计时秒数
        let isAdPlaying = false; // 是否正在播放广告

        // ---------- 打开弹窗 ----------
        function openModal(url) {
            // 重置弹窗状态
            resetModal();

            currentTargetUrl = url;
            overlay.classList.add('active');

            // 禁用页面滚动（可选）
            document.body.style.overflow = 'hidden';
        }

        // ---------- 关闭弹窗（重置） ----------
        function closeModal() {
            overlay.classList.remove('active');
            document.body.style.overflow = '';
            // 清理定时器
            if (adTimerId) {
                clearInterval(adTimerId);
                adTimerId = null;
            }
            isAdPlaying = false;
            // 重置按钮状态
            watchBtn.disabled = false;
            watchBtn.textContent = '观看广告';
            // 隐藏广告区域
            adContainer.classList.remove('show');
            adProgressBar.style.width = '0%';
            adTimer.textContent = '⏳ 剩余 5 秒';
            adSeconds = 5;
        }

        // ---------- 重置弹窗（保留遮罩） ----------
        function resetModal() {
            if (adTimerId) {
                clearInterval(adTimerId);
                adTimerId = null;
            }
            isAdPlaying = false;
            watchBtn.disabled = false;
            watchBtn.textContent = '观看广告';
            adContainer.classList.remove('show');
            adProgressBar.style.width = '0%';
            adTimer.textContent = '⏳ 剩余 5 秒';
            adSeconds = 5;
        }

        // ---------- 执行跳转 ----------
        function doRedirect() {
            if (currentTargetUrl) {
                // 清理定时器
                if (adTimerId) {
                    clearInterval(adTimerId);
                    adTimerId = null;
                }
                // 关闭弹窗
                closeModal();
                // 跳转
                window.location.href = currentTargetUrl;
            }
        }

        // ---------- 开始播放广告 ----------
        function startAd() {
            if (isAdPlaying) return;
            if (!currentTargetUrl) return;

            // 重置广告状态
            if (adTimerId) {
                clearInterval(adTimerId);
                adTimerId = null;
            }
            adSeconds = 5;
            adProgressBar.style.width = '0%';
            adTimer.textContent = '⏳ 剩余 5 秒';

            // 显示广告区域
            adContainer.classList.add('show');

            // 按钮状态
            watchBtn.disabled = true;
            watchBtn.textContent = '广告播放中...';
            isAdPlaying = true;

            // 开始倒计时
            adTimerId = setInterval(function() {
                adSeconds -= 1;
                // 更新进度条
                const progress = ((5 - adSeconds) / 5) * 100;
                adProgressBar.style.width = Math.min(progress, 100) + '%';
                adTimer.textContent = '⏳ 剩余 ' + adSeconds + ' 秒';

                if (adSeconds <= 0) {
                    // 广告播放完成
                    clearInterval(adTimerId);
                    adTimerId = null;
                    adTimer.textContent = '✅ 广告播放完成，即将跳转...';
                    adProgressBar.style.width = '100%';
                    isAdPlaying = false;

                    // 延迟一点点跳转，让用户看到完成状态
                    setTimeout(function() {
                        doRedirect();
                    }, 400);
                }
            }, 1000);
        }

        // ============================================================
        //  4. 事件绑定
        // ============================================================

        // 观看广告
        watchBtn.addEventListener('click', function() {
            if (this.disabled) return;
            startAd();
        });

        // 直接跳转
        skipBtn.addEventListener('click', function() {
            // 如果广告正在播放，清理定时器
            if (adTimerId) {
                clearInterval(adTimerId);
                adTimerId = null;
                isAdPlaying = false;
            }
            doRedirect();
        });

        // 点击遮罩空白区域关闭弹窗（但用户要求必须通过按钮操作，所以禁用）
        // 但为了防止误触，我们让点击遮罩不关闭，只通过按钮操作。
        overlay.addEventListener('click', function(e) {
            if (e.target === overlay) {
                // 不关闭，只做提醒（或者忽略）
                // 为了更好的体验，可以轻轻闪烁提示
                const box = this.querySelector('.modal-box');
                box.style.animation = 'none';
                requestAnimationFrame(() => {
                    box.style.animation = 'scaleIn 0.3s ease';
                });
            }
        });

        // 键盘 ESC 关闭（但不跳转），用户按 ESC 相当于关闭弹窗但不跳转
        // 但用户期望的是通过按钮操作，所以我们让 ESC 关闭弹窗但不跳转，重置状态
        document.addEventListener('keydown', function(e) {
            if (e.key === 'Escape' && overlay.classList.contains('active')) {
                // 如果广告正在播放，清理定时器
                if (adTimerId) {
                    clearInterval(adTimerId);
                    adTimerId = null;
                    isAdPlaying = false;
                }
                // 重置并关闭
                closeModal();
                // 不跳转
            }
        });

        // ============================================================
        //  5. 页面加载完成后的一些小优化
        // ============================================================
        // 防止页面加载时弹窗闪现
        overlay.style.display = 'none';
        // 用 active 类控制显示
        // 修改 CSS：.modal-overlay 默认 display:none，.active 时 display:flex
        // 但上面 CSS 已定义 .modal-overlay { display:none; } .modal-overlay.active { display:flex; }
        // 所以不需要额外操作

        console.log('🖋️ 墨痕中转站 · 已加载 ' + sites.length + ' 个站点');
    </script>

</body>
</html>