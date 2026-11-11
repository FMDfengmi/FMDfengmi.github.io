<title>墨痕中转站</title>
<style> /* ===== 全局重置 & 基础 ===== */ * { margin: 0; padding: 0; box-sizing: border-box; }
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
<!-- ===== PopAds广告脚本 ===== -->
<script type="text/javascript" data-cfasync="false">
/*<![CDATA[/* */
(function(){var z=window,k="d1ec8b28f9b924ef8c6e1e4b8fc7e197",e=[["siteId",595+635+773+5313164],["minBid",0],["popundersPerIP","0"],["delayBetween",0],["default",false],["defaultPerDay",0],["topmostLayer","auto"]],w=["d3d3LmJldHRlcmFkc3lzdGVtLmNvbS9Rdy9sanN0b3JhZ2UubWluLmpz","ZDJrazBvM2ZyN2VkMDEuY2xvdWRmcm9udC5uZXQvRC96eFJmL3Jqc3ppcC11dGlscy5taW4uY3Nz"],l=-1,u,p,q=function(){clearTimeout(p);l++;if(w[l]&&!(1820329893000<(new Date).getTime()&&1<l)){u=z.document.createElement("script");u.type="text/javascript";u.async=!0;var m=z.document.getElementsByTagName("script")[0];u.src="https://"+atob(w[l]);u.crossOrigin="anonymous";u.onerror=q;u.onload=function(){clearTimeout(p);z[k.slice(0,16)+k.slice(0,16)]||q()};p=setTimeout(q,5E3);m.parentNode.insertBefore(u,m)}};if(!z[k]){try{Object.freeze(z