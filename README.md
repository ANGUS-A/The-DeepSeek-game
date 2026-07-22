<!DOCTYPE html>
<html lang="zh-TW">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no">
    <title>終極收藏家：幻想扭蛋機</title>
    <!-- Three.js 3D 引擎與軌道控制器 -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/three@0.128.0/examples/js/controls/OrbitControls.js"></script>
    <!-- FontAwesome 圖示庫 -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <!-- Google Auth API (模擬/對接) -->
    <script src="https://accounts.google.com/gsi/client" async defer></script>
    
    <style>
        :root {
            --bg-dark: #0f111a;
            --panel-bg: rgba(22, 26, 42, 0.85);
            --gold: #ffd700;
            --purple: #a855f7;
            --blue: #3b82f6;
            --green: #22c55e;
            --gray: #64748b;
        }

        * {
            box-sizing: border-box;
            margin: 0;
            padding: 0;
            user-select: none;
            -webkit-user-select: none;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
        }

        body {
            background-color: var(--bg-dark);
            color: #fff;
            overflow-x: hidden;
            min-height: 100vh;
        }

        /* 頂部導覽列 */
        header {
            position: fixed;
            top: 0;
            left: 0;
            right: 0;
            height: 65px;
            background: var(--panel-bg);
            backdrop-filter: blur(10px);
            display: flex;
            justify-content: space-between;
            align-items: center;
            padding: 0 20px;
            z-index: 100;
            border-bottom: 1px solid rgba(255,255,255,0.1);
        }

        .user-profile {
            display: flex;
            align-items: center;
            gap: 12px;
            cursor: pointer;
            background: rgba(255,255,255,0.05);
            padding: 6px 14px;
            border-radius: 30px;
            border: 1px solid rgba(255,255,255,0.1);
            transition: 0.3s;
        }

        .user-profile:hover {
            background: rgba(255,255,255,0.15);
        }

        .avatar {
            width: 38px;
            height: 38px;
            border-radius: 50%;
            background: linear-gradient(135deg, #6366f1, #a855f7);
            display: flex;
            align-items: center;
            justify-content: center;
            font-weight: bold;
            font-size: 18px;
        }

        .user-info {
            display: flex;
            flex-direction: column;
        }

        .username {
            font-weight: 600;
            font-size: 14px;
        }

        .money-display {
            font-size: 12px;
            color: var(--gold);
            font-weight: bold;
        }

        .star-relief-btn {
            width: 45px;
            height: 45px;
            background: radial-gradient(circle, #ffe066, #e6a100);
            border-radius: 50%;
            display: flex;
            align-items: center;
            justify-content: center;
            box-shadow: 0 0 15px rgba(255, 215, 0, 0.5);
            cursor: pointer;
            transition: transform 0.2s;
        }

        .star-relief-btn:hover {
            transform: scale(1.1) rotate(15deg);
        }

        .star-relief-btn i {
            font-size: 24px;
            color: #fff;
            text-shadow: 0 2px 4px rgba(0,0,0,0.4);
        }

        /* 主區域容器 */
        main {
            padding-top: 80px;
            display: flex;
            flex-direction: column;
            align-items: center;
            gap: 40px;
            padding-bottom: 60px;
        }

        /* 扭蛋機樣式 */
        .gacha-stage {
            position: relative;
            width: 320px;
            height: 420px;
            background: linear-gradient(180deg, #1e293b, #0f172a);
            border-radius: 40px;
            border: 4px solid #334155;
            box-shadow: 0 20px 50px rgba(0,0,0,0.6);
            display: flex;
            flex-direction: column;
            align-items: center;
            padding: 20px;
        }

        .gacha-glass {
            width: 240px;
            height: 220px;
            background: rgba(255,255,255,0.05);
            border-radius: 120px 120px 30px 30px;
            border: 3px solid rgba(255,255,255,0.2);
            position: relative;
            overflow: hidden;
            box-shadow: inset 0 0 20px rgba(255,255,255,0.1);
        }

        .capsule-container {
            width: 100%;
            height: 100%;
            position: relative;
        }

        .gacha-knob-container {
            margin-top: 25px;
            position: relative;
            width: 80px;
            height: 80px;
            background: #475569;
            border-radius: 50%;
            display: flex;
            align-items: center;
            justify-content: center;
            border: 4px solid #1e293b;
            box-shadow: 0 4px 10px rgba(0,0,0,0.5);
        }

        .gacha-knob {
            width: 60px;
            height: 18px;
            background: #94a3b8;
            border-radius: 10px;
            transition: transform 0.8s ease-in-out;
            cursor: pointer;
        }

        /* 抽卡按鈕組 */
        .pull-controls {
            display: flex;
            gap: 15px;
            margin-top: 10px;
        }

        .btn-pull {
            padding: 12px 20px;
            border-radius: 12px;
            border: none;
            background: linear-gradient(135deg, #38bdf8, #0284c7);
            color: white;
            font-weight: bold;
            cursor: pointer;
            box-shadow: 0 4px 12px rgba(2, 132, 199, 0.4);
            transition: 0.2s;
        }

        .btn-pull:hover {
            transform: translateY(-2px);
            box-shadow: 0 6px 16px rgba(2, 132, 199, 0.6);
        }

        .btn-pull:disabled {
            opacity: 0.5;
            cursor: not-allowed;
            transform: none;
        }

        /* 點擊機區域 */
        .clicker-section {
            width: 100%;
            max-width: 400px;
            background: var(--panel-bg);
            border-radius: 20px;
            padding: 25px;
            text-align: center;
            border: 1px solid rgba(255,255,255,0.1);
            box-shadow: 0 10px 30px rgba(0,0,0,0.4);
        }

        .clicker-btn {
            width: 120px;
            height: 120px;
            border-radius: 50%;
            border: none;
            background: linear-gradient(135deg, #10b981, #059669);
            color: white;
            font-size: 20px;
            font-weight: bold;
            cursor: pointer;
            box-shadow: 0 8px 25px rgba(16, 185, 129, 0.4);
            margin-top: 15px;
            transition: transform 0.1s;
        }

        .clicker-btn:active {
            transform: scale(0.92);
        }

        /* 收藏展示介面 */
        .view-screen {
            display: none;
            position: fixed;
            top: 0;
            left: 0;
            right: 0;
            bottom: 0;
            background: var(--bg-dark);
            z-index: 200;
            overflow-y: auto;
            padding: 80px 20px 40px 20px;
        }

        .view-header {
            position: fixed;
            top: 0;
            left: 0;
            right: 0;
            height: 65px;
            background: var(--panel-bg);
            backdrop-filter: blur(10px);
            display: flex;
            align-items: center;
            justify-content: space-between;
            padding: 0 20px;
            z-index: 210;
            border-bottom: 1px solid rgba(255,255,255,0.1);
        }

        .btn-back {
            background: rgba(255,255,255,0.1);
            border: none;
            color: white;
            padding: 8px 16px;
            border-radius: 8px;
            cursor: pointer;
            font-size: 16px;
            display: flex;
            align-items: center;
            gap: 8px;
        }

        .rarity-tabs {
            display: flex;
            justify-content: center;
            gap: 10px;
            margin-bottom: 25px;
        }

        .tab-btn {
            padding: 8px 16px;
            border-radius: 20px;
            border: 1px solid rgba(255,255,255,0.2);
            background: rgba(255,255,255,0.05);
            color: white;
            cursor: pointer;
        }

        .tab-btn.active {
            background: var(--gold);
            color: #000;
            font-weight: bold;
        }

        .collection-grid {
            display: grid;
            grid-template-columns: repeat(5, 1fr);
            gap: 15px;
            max-width: 1000px;
            margin: 0 auto;
        }

        .item-card {
            aspect-ratio: 1;
            background: rgba(255,255,255,0.03);
            border-radius: 12px;
            border: 1px solid rgba(255,255,255,0.1);
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            padding: 10px;
            position: relative;
            cursor: pointer;
            transition: 0.2s;
        }

        .item-card:hover {
            transform: translateY(-4px);
            background: rgba(255,255,255,0.08);
        }

        .item-card .count-badge {
            position: absolute;
            top: 6px;
            right: 6px;
            background: rgba(0,0,0,0.6);
            padding: 2px 6px;
            border-radius: 10px;
            font-size: 11px;
            color: #94a3b8;
        }

        .item-card .item-name {
            font-size: 12px;
            text-align: center;
            margin-top: 8px;
            word-break: break-all;
        }

        .item-unknown {
            font-size: 32px;
            color: #475569;
            font-weight: bold;
        }

        /* 抽卡動畫 Modal Overlay */
        .gacha-animation-overlay {
            display: none;
            position: fixed;
            top: 0;
            left: 0;
            right: 0;
            bottom: 0;
            background: rgba(0,0,0,0.92);
            z-index: 300;
            align-items: center;
            justify-content: center;
            flex-direction: column;
        }

        .golden-glow-flash {
            position: absolute;
            width: 100%;
            height: 100%;
            background: radial-gradient(circle, rgba(255,215,0,0.8) 0%, rgba(255,215,0,0) 70%);
            opacity: 0;
            pointer-events: none;
            transition: opacity 0.5s;
        }

        /* 詳情與 3D 檢視 Modal */
        .detail-modal {
            display: none;
            position: fixed;
            top: 0; left: 0; right: 0; bottom: 0;
            background: rgba(0,0,0,0.85);
            z-index: 400;
            align-items: center;
            justify-content: center;
        }

        .detail-content {
            background: var(--panel-bg);
            border-radius: 20px;
            width: 90%;
            max-width: 500px;
            padding: 25px;
            border: 1px solid rgba(255,255,255,0.2);
            display: flex;
            flex-direction: column;
            align-items: center;
            position: relative;
        }

        .canvas-3d-container {
            width: 280px;
            height: 280px;
            border-radius: 15px;
            background: radial-gradient(circle, rgba(255,255,255,0.1), transparent);
            margin: 15px 0;
        }

        /* 2.5D CSS 視差卡片 */
        .parallax-25d-box {
            width: 200px;
            height: 200px;
            perspective: 1000px;
            margin: 15px 0;
        }

        .parallax-25d-card {
            width: 100%;
            height: 100%;
            position: relative;
            transform-style: preserve-3d;
            transition: transform 0.1s ease-out;
            border-radius: 15px;
            background: linear-gradient(135deg, #1e1b4b, #312e81);
            border: 2px solid #6366f1;
            box-shadow: 0 10px 20px rgba(0,0,0,0.5);
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 50px;
        }

        /* RWD */
        @media (max-width: 600px) {
            .collection-grid {
                grid-template-columns: repeat(3, 1fr);
            }
        }
    </style>
</head>
<body>

    <!-- 頂部導覽列 -->
    <header>
        <div class="user-profile" id="profileBtn">
            <div class="avatar" id="userAvatar">G</div>
            <div class="user-info">
                <div class="username" id="userNameDisplay">玩家暱稱 (點擊修改)</div>
                <div class="money-display"><i class="fa-solid fa-coins"></i> $<span id="userMoney">10</span></div>
            </div>
        </div>
        <div class="star-relief-btn" id="openCollectionBtn" title="觀看收藏品">
            <i class="fa-solid fa-star"></i>
        </div>
    </header>

    <!-- 主介面 (扭蛋機與點擊機) -->
    <main id="mainScreen">
        <div class="gacha-stage">
            <div class="gacha-glass">
                <div class="capsule-container" id="capsulePool"></div>
            </div>
            <div class="gacha-knob-container">
                <div class="gacha-knob" id="gachaKnob"></div>
            </div>
            <p style="margin-top: 15px; font-size: 12px; color: #94a3b8;">1元 / 扭一次</p>
        </div>

        <div class="pull-controls">
            <button class="btn-pull" onclick="executePull(1)">扭 1 次 ($1)</button>
            <button class="btn-pull" onclick="executePull(10)">扭 10 次 ($10)</button>
            <button class="btn-pull" onclick="executePull(100)">扭 100 次 ($100)</button>
        </div>

        <!-- 滑動下去後顯示的點擊機 -->
        <div class="clicker-section">
            <h3><i class="fa-solid fa-hand-pointer"></i> 採礦點擊機</h3>
            <p style="font-size: 12px; color: #94a3b8; margin-top: 5px;">點擊一下獲得 $1 元籌碼</p>
            <button class="clicker-btn" id="clickerBtn"><i class="fa-solid fa-hammer"></i> 敲擊</button>
        </div>
    </main>

    <!-- 收藏品介面 -->
    <div class="view-screen" id="collectionView">
        <div class="view-header">
            <button class="btn-back" id="closeCollectionBtn"><i class="fa-solid fa-arrow-left"></i> 返回主頁</button>
            <h2>圖鑑收藏庫</h2>
            <div style="width: 80px;"></div>
        </div>

        <div class="rarity-tabs">
            <button class="tab-btn active" onclick="filterRarity(5)">5星神裝</button>
            <button class="tab-btn" onclick="filterRarity(4)">4星史詩</button>
            <button class="tab-btn" onclick="filterRarity(3)">3星稀有</button>
            <button class="tab-btn" onclick="filterRarity(2)">2星平民</button>
        </div>

        <div class="collection-grid" id="collectionGrid"></div>
    </div>

    <!-- 抽卡動畫 Modal Overlay -->
    <div class="gacha-animation-overlay" id="animationOverlay">
        <div class="golden-glow-flash" id="goldenFlash"></div>
        <div id="animContent" style="text-align: center;">
            <i class="fa-solid fa-box-open fa-bounce" style="font-size: 80px; color: #38bdf8;"></i>
            <h2 style="margin-top: 20px;">扭蛋開啟中...</h2>
        </div>
    </div>

    <!-- 物品 3D/2.5D 詳情 Modal -->
    <div class="detail-modal" id="detailModal">
        <div class="detail-content">
            <button style="position: absolute; top: 15px; right: 15px; background: none; border: none; color: white; font-size: 20px; cursor: pointer;" onclick="closeDetailModal()">✕</button>
            <h2 id="detailTitle" style="color: var(--gold);">物品名稱</h2>
            <p id="detailRarity" style="font-size: 12px; color: #94a3b8; margin-bottom: 10px;"></p>
            
            <!-- 3D Viewport Zone -->
            <div class="canvas-3d-container" id="canvas3dContainer" style="display: none;"></div>
            
            <!-- 2.5D Viewport Zone -->
            <div class="parallax-25d-box" id="box25dContainer" style="display: none;">
                <div class="parallax-25d-card" id="card25d">
                    <i id="icon25d" class="fa-solid fa-shield-halved"></i>
                </div>
            </div>

            <!-- 2D/Standard Zone -->
            <div id="box2dContainer" style="display: none; font-size: 60px; margin: 30px 0;">
                <i id="icon2d" class="fa-solid fa-cube"></i>
            </div>

            <p id="detailDesc" style="text-align: center; font-size: 14px; color: #cbd5e1;"></p>
        </div>
    </div>

    <script>
        /* ================= 遊戲核心資料庫 (完整資料清單) ================= */
        const ITEMS = {
            5: [
                "傳說之劍", "永恆王冠", "龍神之戒", "暗影斗篷", "星辰法杖",
                "不死鳥之羽", "時空懷錶", "創世之盾", "虛空之眼", "命運骰子",
                "神聖聖杯", "雷神之鎚", "冰霜之心", "地獄火契約", "天使豎琴",
                "賢者之石", "風靈之靴", "海洋女神的項鍊", "黃昏之書", "黎明之星"
            ],
            4: [
                "精靈短劍", "騎士鎧甲", "月光手環", "獵人長弓", "巫師帽", "符文石", "銀製護符", "影狼之爪", "聖光權杖", "龍鱗盾",
                "疾風戒指", "冰晶吊墜", "火焰寶珠", "自然之杖", "暗殺者匕首", "聖騎士頭盔", "妖精之翼", "雷鳴戰斧", "水神之淚", "大地之鎚",
                "幻影面具", "幸運硬幣", "咒術卷軸", "鋼鐵護腿", "毒蛇之牙", "鳳凰之淚", "靈魂燈籠", "黃金羅盤", "祕法水晶", "暗夜披風",
                "龍骨弓", "聖女之杖", "狂戰士護腕", "幻獸哨子", "魔導書", "銀月彎刀", "荊棘鞭", "寒冰手套", "火焰披風", "雷電護符",
                "暗影之靴", "光輝頭冠", "獸人戰斧", "精靈豎琴", "祕銀甲", "詛咒之戒", "時光沙漏", "星塵粉末", "龍牙匕首", "聖徽吊墜"
            ],
            3: [
                "木劍", "皮甲", "布帽", "銅盾", "短弓", "鐵匕首", "草藥包", "火把", "水壺", "繩索",
                "指南針", "地圖碎片", "銅幣袋", "鐵礦石", "木盾", "皮靴", "羊毛斗篷", "魚竿", "捕獸夾", "磨刀石",
                "蠟燭", "筆記本", "羽毛筆", "墨水", "釀酒桶", "麵包", "乾糧", "藥水（小）", "繃帶", "火絨盒",
                "鈴鐺", "鏡子（小）", "梳子", "口琴", "骰子（普通）", "撲克牌", "小刀", "鏟子", "十字鎬", "漁網",
                "陷阱", "籠子", "繩梯", "燈油", "雨衣", "手套", "圍巾", "帽子", "腰包", "背包",
                "水袋", "乾肉", "果乾", "堅果", "糖", "鹽", "胡椒", "香料", "茶杯", "碗",
                "木湯匙", "鐵鍋", "烤肉架", "打火石", "蠟封", "信紙", "信封", "圖釘", "釘子", "鐵絲",
                "木板", "布料", "皮革碎片", "骨頭", "牙齒", "羽毛", "貝殼", "珊瑚", "琥珀", "水晶碎片",
                "玻璃珠", "陶瓷碎片", "黏土", "沙袋", "磨石", "刷子", "掃帚", "抹布", "水桶", "花盆",
                "種子", "肥料", "澆水壺", "捕蟲網", "標本瓶", "放大鏡", "溫度計", "風向標", "日晷", "星圖"
            ],
            2: [
                "斷掉的木劍", "發霉的麵包", "缺角的碗", "生鏽的鐵釘", "破洞的襪子", "用過的繃帶", "髒抹布", "沒水的筆", "撕一半的紙", "扁掉的皮球",
                "斷弦的吉他", "掉漆的鈴鐺", "裂開的鏡子（真的會倒楣那種）", "少一顆的棋子", "缺頁的書", "卡住的時鐘", "沒電的電池", "褪色的布條", "歪掉的湯匙", "燒焦的鍋子",
                "軟掉的蠟燭", "打不開的鎖頭", "沒鑰匙的鑰匙圈", "斷掉的鞋帶", "磨平的硬幣", "空瓶子（有灰塵）", "黏在一起的紙牌", "少輪子的玩具車", "沒磁力的磁鐵", "斷掉的橡皮筋",
                "破掉的氣球", "濕掉的火柴", "沒氣的哨子", "彎掉的吸管", "少一頁的日曆", "褪色的貼紙", "斷掉的梳子", "缺牙的叉子", "生鏽的剪刀", "卡住的拉鍊頭",
                "破掉的漁網（補不起來）", "沒水的墨水罐", "乾掉的膠水", "破掉的燈籠", "歪掉的釘書機", "沒釘書針的釘書機", "斷掉的錶帶", "磨損的橡皮擦", "少一截的鉛筆", "髒掉的棉花棒",
                "破掉的扇子", "缺腳的桌子（模型）", "掉漆的棋子", "斷掉的釣竿", "空的飼料袋", "褪色的旗幟", "破掉的帳篷布", "少螺絲的眼鏡", "歪掉的衣架", "斷掉的項鍊",
                "掉鑽的戒指", "褪色的手環", "破掉的書包", "缺輪的滑板", "斷掉的球拍", "沒彈性的彈簧", "破掉的沙包", "褪色的彩帶", "斷掉的螢光棒", "乾掉的指甲油",
                "破掉的鏡子碎片（裝袋）", "少一耳的杯子", "歪掉的湯碗", "裂開的盤子", "掉漆的湯匙", "缺牙的梳子", "斷掉的髮圈", "褪色的髮夾", "生鏽的別針", "破掉的徽章",
                "少一塊的拼圖", "斷掉的尺", "磨損的三角板", "缺角的量角器", "斷掉的圓規", "沒水的白板筆", "乾掉的印章", "褪色的印泥", "破掉的資料夾", "少一頁的筆記本",
                "斷掉的書籤", "褪色的名牌", "破掉的識別證套", "生鏽的夾子", "斷掉的曬衣夾", "褪色的繩子", "起毛球的圍巾", "破掉的手套（一隻）", "缺鈕扣的外套", "斷掉的拉鍊"
            ]
        };

        /* ================= 全局狀態管理 ================= */
        let state = {
            money: 10,
            nickname: "探索者" + Math.floor(Math.random()*1000),
            inventory: {} // { "傳說之劍": 數量 }
        };

        // 載入與保存機制
        function saveGame() {
            localStorage.setItem('gacha_game_data', JSON.stringify(state));
            updateUI();
        }

        function loadGame() {
            const data = localStorage.getItem('gacha_game_data');
            if(data) {
                state = JSON.parse(data);
            }
            updateUI();
        }

        function updateUI() {
            document.getElementById('userMoney').innerText = state.money;
            document.getElementById('userNameDisplay').innerText = state.nickname;
            document.getElementById('userAvatar').innerText = state.nickname.charAt(0).toUpperCase();
        }

        /* ================= 互動性：更改暱稱與模擬登入 ================= */
        document.getElementById('profileBtn').addEventListener('click', () => {
            const newName = prompt("請輸入您的新暱稱：", state.nickname);
            if(newName && newName.trim() !== "") {
                state.nickname = newName.trim();
                saveGame();
            }
        });

        /* ================= 點擊機邏輯 ================= */
        document.getElementById('clickerBtn').addEventListener('click', () => {
            state.money += 1;
            saveGame();
        });

        /* ================= 扭蛋核心演算法與動畫 ================= */
        function getRandomItem() {
            const rand = Math.random() * 100;
            let rarity = 1;
            
            // 機率分布計算: 5星(0.001%), 4星(5%), 3星(25%), 2星(60%), 1星(其餘現金返還)
            if(rand < 0.001) rarity = 5;
            else if(rand < 5.001) rarity = 4;
            else if(rand < 30.001) rarity = 3;
            else if(rand < 90.001) rarity = 2;
            else rarity = 1;

            if(rarity === 1) {
                const reward = Math.random() < 0.5 ? 1 : 2;
                return { rarity: 1, name: `現金返還 $${reward}`, cash: reward };
            }

            const list = ITEMS[rarity];
            const itemName = list[Math.floor(Math.random() * list.length)];
            return { rarity: rarity, name: itemName };
        }

        function executePull(count) {
            if(state.money < count) {
                alert("金幣不足！請使用點擊機賺取金幣。");
                return;
            }

            state.money -= count;
            const knob = document.getElementById('gachaKnob');
            knob.style.transform = 'rotate(360deg)';

            const overlay = document.getElementById('animationOverlay');
            const goldenFlash = document.getElementById('goldenFlash');
            overlay.style.display = 'flex';

            let hasFiveStar = false;
            let results = [];

            for(let i = 0; i < count; i++) {
                const item = getRandomItem();
                results.push(item);
                if(item.rarity === 5) hasFiveStar = true;

                // 紀錄進背包
                if(item.rarity > 1) {
                    state.inventory[item.name] = (state.inventory[item.name] || 0) + 1;
                } else {
                    state.money += item.cash; // 返還現金
                }
            }

            // 出 5 星時展現金色閃光
            if(hasFiveStar) {
                goldenFlash.style.opacity = '1';
            } else {
                goldenFlash.style.opacity = '0';
            }

            setTimeout(() => {
                knob.style.transform = 'rotate(0deg)';
                overlay.style.display = 'none';
                saveGame();
                showPullResults(results);
            }, 1200);
        }

        function showPullResults(results) {
            let msg = `🎉 獲得物品清單 (${results.length} 抽)：\n\n`;
            results.forEach(r => {
                msg += `[${'★'.repeat(r.rarity)}] ${r.name}\n`;
            });
            alert(msg);
        }

        /* ================= 圖鑑與展示介面 ================= */
        let currentFilterRarity = 5;

        document.getElementById('openCollectionBtn').addEventListener('click', () => {
            document.getElementById('collectionView').style.display = 'block';
            renderCollectionGrid();
        });

        document.getElementById('closeCollectionBtn').addEventListener('click', () => {
            document.getElementById('collectionView').style.display = 'none';
        });

        function filterRarity(r) {
            currentFilterRarity = r;
            document.querySelectorAll('.tab-btn').forEach(b => b.classList.remove('active'));
            event.target.classList.add('active');
            renderCollectionGrid();
        }

        function renderCollectionGrid() {
            const grid = document.getElementById('collectionGrid');
            grid.innerHTML = '';

            const list = ITEMS[currentFilterRarity];
            list.forEach(itemName => {
                const count = state.inventory[itemName] || 0;
                const card = document.createElement('div');
                card.className = 'item-card';

                if(count > 0) {
                    card.innerHTML = `
                        <div class="count-badge">x${count}</div>
                        <i class="fa-solid fa-gem" style="font-size:32px; color: ${getStarColor(currentFilterRarity)};"></i>
                        <div class="item-name">${itemName}</div>
                    `;
                    card.onclick = () => openItemDetail(itemName, currentFilterRarity);
                } else {
                    card.innerHTML = `
                        <div class="item-unknown">?</div>
                        <div class="item-name" style="color:#64748b;">???</div>
                    `;
                }
                grid.appendChild(card);
            });
        }

        function getStarColor(rarity) {
            if(rarity === 5) return 'var(--gold)';
            if(rarity === 4) return 'var(--purple)';
            if(rarity === 3) return 'var(--blue)';
            return 'var(--gray)';
        }

        /* ================= 3D / 2.5D / 2D 建模與互動展示 ================= */
        let activeScene, activeCamera, activeRenderer, activeMesh;

        function openItemDetail(name, rarity) {
            document.getElementById('detailTitle').innerText = name;
            document.getElementById('detailRarity').innerText = '★'.repeat(rarity) + ` (${rarity}星等級)`;
            document.getElementById('detailDesc').innerText = `這是獨一無二的 ${name}，點擊或滑動可以進行互動。`;

            const c3d = document.getElementById('canvas3dContainer');
            const c25d = document.getElementById('box25dContainer');
            const c2d = document.getElementById('box2dContainer');

            c3d.style.display = 'none';
            c25d.style.display = 'none';
            c2d.style.display = 'none';

            if(rarity === 5) {
                c3d.style.display = 'block';
                init3DModel(name);
            } else if(rarity === 4) {
                c25d.style.display = 'block';
                init25DInteraction();
            } else {
                c2d.style.display = 'block';
            }

            document.getElementById('detailModal').style.display = 'flex';
        }

        function closeDetailModal() {
            document.getElementById('detailModal').style.display = 'none';
            if(activeRenderer) {
                activeRenderer.dispose();
                activeRenderer = null;
            }
        }

        // 5星 3D 建模生成器 (Three.js 獨立Procedural 3D Model)
        function init3DModel(itemName) {
            const container = document.getElementById('canvas3dContainer');
            container.innerHTML = '';

            activeScene = new THREE.Scene();
            activeCamera = new THREE.PerspectiveCamera(45, 1, 0.1, 1000);
            activeCamera.position.set(0, 0, 5);

            activeRenderer = new THREE.WebGLRenderer({ antialias: true, alpha: true });
            activeRenderer.setSize(280, 280);
            container.appendChild(activeRenderer.domElement);

            const ambientLight = new THREE.AmbientLight(0xffffff, 0.8);
            activeScene.add(ambientLight);

            const pointLight = new THREE.PointLight(0xffd700, 1.5);
            pointLight.position.set(5, 5, 5);
            activeScene.add(pointLight);

            // 根據名稱生成對應的 3D 幾何形體
            let geometry;
            if(itemName.includes("劍") || itemName.includes("杖")) {
                geometry = new THREE.CylinderGeometry(0.1, 0.3, 2.5, 32);
            } else if(itemName.includes("冠") || itemName.includes("戒") || itemName.includes("眼")) {
                geometry = new THREE.TorusGeometry(1, 0.3, 16, 100);
            } else if(itemName.includes("盾") || itemName.includes("心")) {
                geometry = new THREE.OctahedronGeometry(1.2, 0);
            } else {
                geometry = new THREE.IcosahedronGeometry(1.2, 0);
            }

            const material = new THREE.MeshStandardMaterial({
                color: 0xffd700,
                metalness: 0.8,
                roughness: 0.2,
                wireframe: false
            });

            activeMesh = new THREE.Mesh(geometry, material);
            activeScene.add(activeMesh);

            // OrbitControls 讓使用者可用手指旋轉 3D 物件
            const controls = new THREE.OrbitControls(activeCamera, activeRenderer.domElement);
            controls.enableDamping = true;

            function animate() {
                if(!activeRenderer) return;
                requestAnimationFrame(animate);
                activeMesh.rotation.y += 0.01;
                controls.update();
                activeRenderer.render(activeScene, activeCamera);
            }
            animate();
        }

        // 4星 2.5D 視差互動
        function init25DInteraction() {
            const card = document.getElementById('card25d');
            const container = document.getElementById('box25dContainer');

            container.onmousemove = (e) => {
                const rect = container.getBoundingClientRect();
                const x = e.clientX - rect.left - rect.width/2;
                const y = e.clientY - rect.top - rect.height/2;
                card.style.transform = `rotateY(${x / 5}deg) rotateX(${-y / 5}deg)`;
            };

            container.onmouseleave = () => {
                card.style.transform = `rotateY(0deg) rotateX(0deg)`;
            };
        }

        // 初始化遊戲
        window.onload = loadGame;
    </script>
</body>
</html>
