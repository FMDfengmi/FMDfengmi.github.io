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
        t
