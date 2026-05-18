<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>永冻冰封 - 末日抉择</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            font-family: 'Microsoft YaHei', 'PingFang SC', sans-serif;
            background: linear-gradient(135deg, #0a1628 0%, #1a3a5c 50%, #0d2137 100%);
            min-height: 100vh;
            color: #e0f0ff;
            overflow-x: hidden;
        }

        .game-container {
            max-width: 1200px;
            margin: 0 auto;
            min-height: 100vh;
            display: flex;
            flex-direction: column;
            position: relative;
        }

        /* 雪花飘落效果 */
        .snowflakes {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            pointer-events: none;
            z-index: 1;
            overflow: hidden;
        }

        .snowflake {
            position: absolute;
            top: -10px;
            color: rgba(255, 255, 255, 0.6);
            font-size: 1em;
            animation: fall linear infinite;
        }

        @keyframes fall {
            0% {
                transform: translateY(-10px) rotate(0deg);
                opacity: 1;
            }
            100% {
                transform: translateY(100vh) rotate(360deg);
                opacity: 0.3;
            }
        }

        /* 顶部状态栏 */
        .status-bar {
            background: linear-gradient(180deg, rgba(20, 50, 80, 0.95) 0%, rgba(10, 30, 60, 0.9) 100%);
            border-bottom: 2px solid #2a5a8a;
            padding: 15px 20px;
            display: none;
            gap: 20px;
            flex-wrap: wrap;
            justify-content: space-between;
            align-items: center;
            position: sticky;
            top: 0;
            z-index: 100;
            box-shadow: 0 4px 20px rgba(0, 100, 150, 0.3);
        }

        .status-bar.visible {
            display: flex;
        }

        .status-item {
            display: flex;
            align-items: center;
            gap: 8px;
            background: rgba(0, 50, 100, 0.5);
            padding: 8px 15px;
            border-radius: 20px;
            border: 1px solid #3a6a9a;
        }

        .status-item .icon {
            font-size: 1.2em;
        }

        .status-item .label {
            font-size: 0.85em;
            color: #8ab4d8;
        }

        .status-item .value {
            font-weight: bold;
            color: #fff;
        }

        .status-item.power .value {
            color: #00ffcc;
        }

        .status-item.day .value {
            color: #ffaa00;
        }

        /* 主游戏区域 */
        .game-main {
            flex: 1;
            display: flex;
            flex-direction: column;
            padding: 20px;
            position: relative;
            z-index: 10;
        }

        /* 场景图片区域 */
        .scene-image-container {
            width: 100%;
            max-height: 450px;
            border-radius: 15px;
            overflow: hidden;
            border: 3px solid #3a6a9a;
            box-shadow: 0 10px 40px rgba(0, 100, 200, 0.3);
            margin-bottom: 20px;
            position: relative;
        }

        .scene-image {
            width: 100%;
            height: 450px;
            object-fit: cover;
            transition: opacity 0.5s ease;
        }

        .scene-image.loading {
            opacity: 0.5;
        }

        .scene-overlay {
            position: absolute;
            bottom: 0;
            left: 0;
            right: 0;
            padding: 20px;
            background: linear-gradient(transparent, rgba(0, 20, 40, 0.9));
        }

        .scene-location {
            font-size: 1.1em;
            color: #7ac0ff;
            text-shadow: 0 2px 4px rgba(0,0,0,0.8);
        }

        /* 剧情文本区域 */
        .story-panel {
            background: linear-gradient(135deg, rgba(15, 40, 70, 0.95) 0%, rgba(10, 30, 60, 0.95) 100%);
            border-radius: 15px;
            border: 2px solid #2a5a8a;
            padding: 25px;
            margin-bottom: 20px;
            box-shadow: 0 5px 25px rgba(0, 50, 100, 0.3);
        }

        .story-text {
            font-size: 1.1em;
            line-height: 1.9;
            color: #d0e8ff;
            text-shadow: 0 1px 2px rgba(0,0,0,0.5);
        }

        .story-text.narrator {
            color: #a0c8e8;
            font-style: italic;
        }

        .story-text.dialogue {
            color: #ffffff;
            padding-left: 20px;
            border-left: 3px solid #4a8ac4;
        }

        .character-name {
            color: #00ccff;
            font-weight: bold;
            display: block;
            margin-bottom: 5px;
        }

        /* 物资/异能状态面板 */
        .info-panels {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(280px, 1fr));
            gap: 15px;
            margin-bottom: 20px;
        }

        .info-panel {
            background: linear-gradient(135deg, rgba(20, 50, 80, 0.9) 0%, rgba(15, 35, 60, 0.9) 100%);
            border-radius: 12px;
            border: 1px solid #3a6a9a;
            padding: 15px;
        }

        .info-panel h3 {
            color: #7ac0ff;
            font-size: 1em;
            margin-bottom: 10px;
            display: flex;
            align-items: center;
            gap: 8px;
            border-bottom: 1px solid #2a5a8a;
            padding-bottom: 8px;
        }

        .info-panel .content {
            font-size: 0.9em;
            color: #b0d0f0;
        }

        .supply-item {
            display: flex;
            justify-content: space-between;
            padding: 5px 0;
            border-bottom: 1px solid rgba(60, 100, 140, 0.3);
        }

        .supply-item:last-child {
            border-bottom: none;
        }

        .ability-tag {
            display: inline-block;
            background: linear-gradient(135deg, #1a5a8a 0%, #0d4070 100%);
            color: #00ffcc;
            padding: 3px 10px;
            border-radius: 12px;
            font-size: 0.85em;
            margin: 3px;
            border: 1px solid #2a8acc;
        }

        /* 选项按钮 */
        .choices-container {
            display: flex;
            flex-direction: column;
            gap: 12px;
            margin-top: 20px;
        }

        .choice-btn {
            background: linear-gradient(135deg, rgba(30, 70, 110, 0.9) 0%, rgba(20, 50, 90, 0.9) 100%);
            border: 2px solid #4a8ac4;
            border-radius: 10px;
            padding: 15px 20px;
            color: #e0f0ff;
            font-size: 1em;
            cursor: pointer;
            transition: all 0.3s ease;
            text-align: left;
            position: relative;
            overflow: hidden;
        }

        .choice-btn::before {
            content: '';
            position: absolute;
            top: 0;
            left: -100%;
            width: 100%;
            height: 100%;
            background: linear-gradient(90deg, transparent, rgba(100, 180, 255, 0.2), transparent);
            transition: left 0.5s ease;
        }

        .choice-btn:hover {
            background: linear-gradient(135deg, rgba(50, 100, 150, 0.9) 0%, rgba(30, 70, 120, 0.9) 100%);
            border-color: #6ab0e4;
            transform: translateX(5px);
            box-shadow: 0 5px 20px rgba(60, 140, 200, 0.4);
        }

        .choice-btn:hover::before {
            left: 100%;
        }

        .choice-btn .choice-type {
            font-size: 0.8em;
            color: #8ab8d8;
            display: block;
            margin-top: 5px;
        }

        .choice-btn.good .choice-type {
            color: #7aff9a;
        }

        .choice-btn.evil .choice-type {
            color: #ff7a7a;
        }

        /* 开始界面 */
        .start-screen {
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            min-height: 100vh;
            text-align: center;
            padding: 40px 20px;
        }

        .game-title {
            font-size: 3em;
            font-weight: bold;
            background: linear-gradient(135deg, #7ac0ff 0%, #00ccff 50%, #7ac0ff 100%);
            -webkit-background-clip: text;
            -webkit-text-fill-color: transparent;
            background-clip: text;
            text-shadow: none;
            margin-bottom: 10px;
            animation: glow 3s ease-in-out infinite;
        }

        @keyframes glow {
            0%, 100% { filter: drop-shadow(0 0 20px rgba(0, 200, 255, 0.5)); }
            50% { filter: drop-shadow(0 0 40px rgba(0, 200, 255, 0.8)); }
        }

        .game-subtitle {
            font-size: 1.3em;
            color: #8ab4d8;
            margin-bottom: 40px;
        }

        .start-btn {
            background: linear-gradient(135deg, #1a5a8a 0%, #0d4070 100%);
            border: 3px solid #00ccff;
            border-radius: 30px;
            padding: 20px 60px;
            color: #fff;
            font-size: 1.3em;
            cursor: pointer;
            transition: all 0.3s ease;
            box-shadow: 0 0 30px rgba(0, 200, 255, 0.4);
        }

        .start-btn:hover {
            background: linear-gradient(135deg, #2a7ab8 0%, #1a5a9a 100%);
            transform: scale(1.05);
            box-shadow: 0 0 50px rgba(0, 200, 255, 0.6);
        }

        .intro-text {
            max-width: 600px;
            margin: 30px auto;
            padding: 20px;
            background: rgba(10, 30, 60, 0.8);
            border-radius: 15px;
            border: 1px solid #3a6a9a;
            color: #b0d0f0;
            line-height: 1.8;
        }

        /* 游戏结束界面 */
        .game-over {
            text-align: center;
            padding: 60px 20px;
        }

        .game-over h2 {
            font-size: 2.5em;
            color: #ff6b6b;
            margin-bottom: 20px;
        }

        .game-over.victory h2 {
            color: #00ffcc;
        }

        .game-over p {
            color: #b0d0f0;
            margin-bottom: 30px;
            line-height: 1.8;
        }

        .restart-btn {
            background: linear-gradient(135deg, #5a1a1a 0%, #3a0d0d 100%);
            border: 2px solid #ff6b6b;
            border-radius: 25px;
            padding: 15px 40px;
            color: #fff;
            font-size: 1.1em;
            cursor: pointer;
            transition: all 0.3s ease;
        }

        .restart-btn:hover {
            background: linear-gradient(135deg, #7a2a2a 0%, #5a1a1a 100%);
            box-shadow: 0 0 20px rgba(255, 100, 100, 0.4);
        }

        /* 异能展示动画 */
        .ability-awaken {
            animation: abilityGlow 2s ease-out;
        }

        @keyframes abilityGlow {
            0% {
                box-shadow: 0 0 0 rgba(0, 255, 204, 0);
            }
            50% {
                box-shadow: 0 0 100px rgba(0, 255, 204, 0.8);
            }
            100% {
                box-shadow: 0 0 0 rgba(0, 255, 204, 0);
            }
        }

        /* 进度指示 */
        .progress-indicator {
            position: absolute;
            top: 20px;
            right: 20px;
            background: rgba(10, 30, 60, 0.9);
            padding: 10px 15px;
            border-radius: 20px;
            border: 1px solid #3a6a9a;
            font-size: 0.85em;
            color: #8ab4d8;
            z-index: 50;
        }

        /* 隐藏状态 */
        .hidden {
            display: none !important;
        }

        /* 响应式 */
        @media (max-width: 768px) {
            .game-title {
                font-size: 2em;
            }

            .scene-image {
                height: 280px;
            }

            .status-bar {
                padding: 10px;
                gap: 10px;
            }

            .status-item {
                padding: 5px 10px;
                font-size: 0.85em;
            }
        }
    </style>
</head>
<body>
    <div class="game-container">
        <!-- 雪花效果 -->
        <div class="snowflakes" id="snowflakes"></div>

        <!-- 顶部状态栏 -->
        <div class="status-bar" id="statusBar">
            <div class="status-item power">
                <span class="icon">⚡</span>
                <span class="label">异能值</span>
                <span class="value" id="powerValue">0</span>
            </div>
            <div class="status-item day">
                <span class="icon">📅</span>
                <span class="label">第</span>
                <span class="value" id="dayValue">1</span>
                <span class="label">天</span>
            </div>
            <div class="status-item">
                <span class="icon">🎒</span>
                <span class="label">物资</span>
                <span class="value" id="supplyValue">0</span>
            </div>
            <div class="status-item">
                <span class="icon">❤️</span>
                <span class="label">生命</span>
                <span class="value" id="healthValue">100</span>
            </div>
            <div class="status-item">
                <span class="icon">🌡️</span>
                <span class="label">体温</span>
                <span class="value" id="tempValue">36.5°C</span>
            </div>
        </div>

        <!-- 主游戏区域 -->
        <div class="game-main" id="gameMain">
            <!-- 开始界面 -->
            <div class="start-screen" id="startScreen">
                <h1 class="game-title">永冻冰封</h1>
                <p class="game-subtitle">末日抉择 - 冰封世界生存指南</p>
                <div class="intro-text">
                    <p>末日降临，全球急速冰封。</p>
                    <p>气温骤降至零下百度，万物冻结，文明崩塌。</p>
                    <p>你，将在冰封的世界中如何抉择？</p>
                </div>
                <button class="start-btn" onclick="startGame()">开始游戏</button>
            </div>

            <!-- 进度指示 -->
            <div class="progress-indicator hidden" id="progressIndicator">
                <span id="currentScene">序章</span>
            </div>

            <!-- 场景图片 -->
            <div class="scene-image-container hidden" id="sceneImageContainer">
                <img class="scene-image" id="sceneImage" src="" alt="场景图片">
                <div class="scene-overlay">
                    <div class="scene-location" id="sceneLocation">---</div>
                </div>
            </div>

            <!-- 剧情文本 -->
            <div class="story-panel hidden" id="storyPanel">
                <div class="story-text" id="storyText"></div>
            </div>

            <!-- 物资异能面板 -->
            <div class="info-panels hidden" id="infoPanels">
                <div class="info-panel" id="suppliesPanel">
                    <h3>🎒 物资储备</h3>
                    <div class="content" id="suppliesContent"></div>
                </div>
                <div class="info-panel" id="abilitiesPanel">
                    <h3>⚡ 异能列表</h3>
                    <div class="content" id="abilitiesContent"></div>
                </div>
            </div>

            <!-- 选项区域 -->
            <div class="choices-container hidden" id="choicesContainer"></div>

            <!-- 游戏结束 -->
            <div class="game-over hidden" id="gameOver">
                <h2 id="gameOverTitle">游戏结束</h2>
                <p id="gameOverText"></p>
                <button class="restart-btn" onclick="restartGame()">重新开始</button>
            </div>
        </div>
    </div>

    <script>
        // ==================== 游戏数据 ====================
        const GameData = {
            scenes: {
                // 序章：重生
                prologue: {
                    id: 'prologue',
                    location: '序章 - 重生的黎明',
                    image: 'https://images.unsplash.com/photo-1517783999520-f068d7431a60?w=1200',
                    text: `剧烈的头痛袭来，我猛然睁开双眼。

冰冷的地板，昏暗的房间，窗外还是那熟悉的夜色。

我颤抖着看向手机屏幕——日期让我心脏几乎停止跳动。

**距离寒潮爆发，还有三天。**

上一世的记忆如潮水般涌来：无尽的冰雪、崩塌的文明、绝望的求生......最终，我在饥寒交迫中失去了意识。

但现在，我重生了。

我必须抓紧这最后的三天时间。`,
                    textType: 'narrator',
                    choices: [
                        {
                            text: '检查自己的身体状态',
                            type: 'explore',
                            next: 'check_body'
                        },
                        {
                            text: '立刻起身清点家中物资',
                            type: 'explore',
                            next: 'check_supplies'
                        },
                        {
                            text: '回忆前世的记忆和信息',
                            type: 'explore',
                            next: 'remember_past'
                        }
                    ]
                },

                check_body: {
                    id: 'check_body',
                    location: '重生后的身体',
                    image: 'https://images.unsplash.com/photo-1557308520-9c98cb70f5a5?w=1200',
                    text: `我低头看着自己的双手——温暖、完整、充满力量。

这不是在冰雪中冻得发紫、满身冻疮的手。

我能感受到体内有一股奇异的力量在流动，仿佛空间本身都在回应我的意志。

**空间异能——正在觉醒。**

我深吸一口气，尝试集中精神。眼前的空间似乎微微扭曲，一个巨大的储物空间在意识中展开。

这个空间......比我预想的还要庞大得多！`,
                    textType: 'narrator',
                    effect: { ability: '空间异能', abilityLevel: 1 },
                    choices: [
                        {
                            text: '激动不已，立刻测试异能的储物功能',
                            type: 'explore',
                            next: 'test_ability'
                        },
                        {
                            text: '冷静下来，先制定详细的生存计划',
                            type: 'plan',
                            next: 'make_plan'
                        }
                    ]
                },

                check_supplies: {
                    id: 'check_supplies',
                    location: '清点物资',
                    image: 'https://images.unsplash.com/photo-1507003211169-0a1dd7228f2d?w=1200',
                    text: `我快步走向厨房和储物间，开始清点现有物资。

打开冰箱——有些剩饭剩菜、几瓶饮料、一点蔬菜。

柜子里有几包方便面、半袋大米、几罐罐头。

这些......远远不够。

上一世的记忆告诉我，寒潮来临时气温会骤降到零下百度左右。更糟糕的是，电力会迅速中断，暖气将成为奢望。

我必须在天亮后立即出门采购大量物资。`,
                    textType: 'narrator',
                    effect: { supplies: { '方便面': 3, '大米': 5, '罐头': 2 } },
                    choices: [
                        {
                            text: '列出必须采购的物资清单',
                            type: 'plan',
                            next: 'make_plan'
                        },
                        {
                            text: '先尝试觉醒体内的异能',
                            type: 'explore',
                            next: 'test_ability'
                        }
                    ]
                },

                remember_past: {
                    id: 'remember_past',
                    location: '前世的记忆',
                    image: 'https://images.unsplash.com/photo-1518709766631-a6a7f45921c3?w=1200',
                    text: `我闭上眼睛，努力回忆前世的种种。

寒潮爆发的第一天：政府发布紧急警报，但无人重视。

第二天：气温骤降到零下四十度，开始出现大规模停电。

第三天：道路被冰雪封锁，城市陷入混乱。

一周后：室外气温已降至零下一百度，幸存者寥寥无几。

我还记得有几个幸存者后来觉醒了异能......那些抢夺物资的掠夺者......地下避难所的传言......

这些信息，将在末日中变得无比珍贵。`,
                    textType: 'narrator',
                    effect: { knowledge: true },
                    choices: [
                        {
                            text: '用这些信息制定求生计划',
                            type: 'plan',
                            next: 'make_plan'
                        },
                        {
                            text: '立即行动，连夜开始囤积物资',
                            type: 'action',
                            next: 'start_hoarding'
                        }
                    ]
                },

                test_ability: {
                    id: 'test_ability',
                    location: '异能觉醒',
                    image: 'https://images.unsplash.com/photo-1534796636912-3b95b3ab5986?w=1200',
                    text: `我集中精神，感受体内那股奇异的力量。

刹那间，眼前的空气微微扭曲。一个巨大的空间门户在我面前开启——这正是我的异能：**储物空间**。

我尝试着将手边的一本书放入空间......成功了！书本瞬间消失，出现在那个神秘的空间中。

更令人惊喜的是，这个空间比我预想的还要巨大！这简直是一个移动的超级仓库！

**异能觉醒：空间储物（Lv.1）**`,
                    textType: 'narrator',
                    effect: { ability: '空间储物', abilityLevel: 1 },
                    choices: [
                        {
                            text: '立刻出门采购物资',
                            type: 'action',
                            next: 'start_hoarding'
                        },
                        {
                            text: '先规划好需要采购什么',
                            type: 'plan',
                            next: 'make_plan'
                        }
                    ]
                },

                make_plan: {
                    id: 'make_plan',
                    location: '制定计划',
                    image: 'https://images.unsplash.com/photo-1454165804606-c3d57bc86b40?w=1200',
                    text: `我找来纸笔，开始制定详细的计划。

**首要任务：囤积物资**

1. 食物：方便面、罐头、压缩饼干、干粮、纯净水
2. 取暖：暖宝宝、保温毯、燃料（酒精块、煤炭）
3. 药品：感冒药、退烧药、消炎药、冻伤膏
4. 防寒：羽绒服、保暖内衣、帽子、手套、围巾
5. 工具：手电筒、电池、打火机、绳索、工具箱

**次要任务：寻找或建立安全屋**

**最终目标：在冰封世界中活下去**

写完计划，天边已经泛起鱼肚白。行动的时候到了。`,
                    textType: 'narrator',
                    effect: { plan: true },
                    choices: [
                        {
                            text: '前往最近的超市采购',
                            type: 'action',
                            next: 'supermarket'
                        },
                        {
                            text: '先去医院获取药品',
                            type: 'action',
                            next: 'hospital'
                        },
                        {
                            text: '去户外用品店购买防寒装备',
                            type: 'action',
                            next: 'outdoor_shop'
                        }
                    ]
                },

                start_hoarding: {
                    id: 'start_hoarding',
                    location: '连夜行动',
                    image: 'https://images.unsplash.com/photo-1530866495561-507c9faab2ed?w=1200',
                    text: `时间紧迫，我必须立即行动！

披上外套，拿上钱包和手机，我冲出了家门。

深夜的街道格外冷清，但我知道这份宁静即将被打破。

我直奔24小时便利店，用现金疯狂采购——方便面、罐头、饼干、矿泉水......

便利店的店员用奇怪的眼神看着我，但我顾不上那么多。

**还剩三天。每一分钟都至关重要。**`,
                    textType: 'narrator',
                    effect: { supplies: { '方便面': 10, '罐头': 5, '矿泉水': 10, '饼干': 3 } },
                    choices: [
                        {
                            text: '继续去超市大批量采购',
                            type: 'action',
                            next: 'supermarket'
                        },
                        {
                            text: '回家整理物资，明天再去别处',
                            type: 'action',
                            next: 'organize_supplies'
                        }
                    ]
                },

                supermarket: {
                    id: 'supermarket',
                    location: '永辉超市',
                    image: 'https://images.unsplash.com/photo-1604719312566-8912e9227c6a?w=1200',
                    text: `清晨七点，超市刚开门。

我没有浪费时间在货架间闲逛，而是直接开始"清仓"模式。

推着购物车，我快速扫荡着物资区：
- **食物区**：方便面30包、压缩饼干20盒、罐头50罐、纯净水100瓶
- **调味品区**：盐、糖、食用油
- **日用品区**：湿巾、垃圾袋、保暖毯

收银员目瞪口呆地看着我堆积如山的购物车。

"先生，您这是......要开超市吗？"

我只是笑笑，继续刷卡。`,
                    textType: 'narrator',
                    effect: {
                        supplies: {
                            '方便面': 30,
                            '压缩饼干': 20,
                            '罐头': 50,
                            '纯净水': 100,
                            '盐': 5,
                            '食用油': 3,
                            '保暖毯': 2
                        },
                        money: -3000
                    },
                    choices: [
                        {
                            text: '前往药店采购药品',
                            type: 'action',
                            next: 'pharmacy',
                            good: true
                        },
                        {
                            text: '去户外用品店购买专业装备',
                            type: 'action',
                            next: 'outdoor_shop'
                        },
                        {
                            text: '回家整理物资，休息一下',
                            type: 'action',
                            next: 'organize_supplies'
                        }
                    ]
                },

                pharmacy: {
                    id: 'pharmacy',
                    location: '大药房',
                    image: 'https://images.unsplash.com/photo-1587854692152-cbe660dbde88?w=1200',
                    text: `药房里弥漫着消毒水的味道。

我快速找到所需药品区域，开始搜集：

- **退烧药**：布洛芬、阿司匹林
- **感冒药**：白加黑、感冒灵
- **消炎药**：阿莫西林、头孢
- **冻伤药**：冻疮膏、维生素E
- **绷带、酒精、碘伏**

"先生，您要这么多药？"药剂师疑惑地问。

"家里有老人，备着以防万一。"我平静地回答。

刷完医保卡，又一笔不小的开支。但比起末日中药品的稀缺，这点钱根本不算什么。`,
                    textType: 'narrator',
                    effect: {
                        supplies: {
                            '退烧药': 10,
                            '感冒药': 10,
                            '消炎药': 8,
                            '冻伤膏': 5,
                            '医疗包': 3
                        },
                        money: -1500
                    },
                    choices: [
                        {
                            text: '去五金店购买工具和燃料',
                            type: 'action',
                            next: 'hardware_store'
                        },
                        {
                            text: '寻找保暖设备',
                            type: 'action',
                            next: 'find_warm_gear'
                        }
                    ]
                },

                outdoor_shop: {
                    id: 'outdoor_shop',
                    location: '户外探险装备店',
                    image: 'https://images.unsplash.com/photo-1551698618-1dfe5d97d256?w=1200',
                    text: `专业的户外装备店，商品琳琅满目。

我找到了最关键的防寒装备：

- **羽绒睡袋**：极限温度-40°C，专业级
- **防风防寒服**：连体设计，保暖性极佳
- **保暖手套**：加绒加厚，防风防水
- **防寒面罩**：保护面部免受冻伤
- **登山靴**：防滑耐磨，适合雪地行走
- **头灯**：LED高亮度，续航100小时
- **防滑冰爪**：冰雪路面行走必备

老板是个资深驴友，向我推荐了各种装备的使用方法。

"兄弟，你要穿越冰川吗？"他好奇地问。

"......差不多吧。"`,
                    textType: 'narrator',
                    effect: {
                        supplies: {
                            '羽绒睡袋': 2,
                            '防寒服': 2,
                            '保暖手套': 3,
                            '防寒面罩': 2,
                            '登山靴': 2,
                            '头灯': 3,
                            '防滑冰爪': 2
                        },
                        money: -5000
                    },
                    choices: [
                        {
                            text: '继续采购燃料和取暖设备',
                            type: 'action',
                            next: 'hardware_store',
                            good: true
                        },
                        {
                            text: '回家整理所有物资',
                            type: 'action',
                            next: 'organize_supplies'
                        }
                    ]
                },

                hardware_store: {
                    id: 'hardware_store',
                    location: '五金工具店',
                    image: 'https://images.unsplash.com/photo-1533090161767-e6ffed986c88?w=1200',
                    text: `五金店里杂乱无章地堆放着各种工具。

在这里，我找到了寒潮中至关重要的物资：

- **酒精块**：高效燃料，可用于烹饪和取暖
- **便携炉具**：丁烷气罐，野外生存必备
- **保暖物资**：毛毯、暖宝宝、保温杯
- **工具**：多功能刀、工具箱、绳索、手电筒
- **电池**：各种型号，干电池组

老板正在看新闻，报道说最近气温异常下降。

"今年冬天来得早啊，"他感叹道。

我默默点头，心里却在想：何止是早，简直是末日。`,
                    textType: 'narrator',
                    effect: {
                        supplies: {
                            '酒精块': 20,
                            '便携炉': 3,
                            '丁烷气罐': 30,
                            '暖宝宝': 100,
                            '保温杯': 3,
                            '多功能刀': 2,
                            '工具箱': 1,
                            '绳索': 5,
                            '手电筒': 4,
                            '电池': 50
                        },
                        money: -2000
                    },
                    choices: [
                        {
                            text: '回家整理物资',
                            type: 'action',
                            next: 'organize_supplies'
                        },
                        {
                            text: '寻找安全屋',
                            type: 'action',
                            next: 'find_safehouse'
                        }
                    ]
                },

                hospital: {
                    id: 'hospital',
                    location: '市立医院',
                    image: 'https://images.unsplash.com/photo-1519494026892-80bbd2d6fd0d?w=1200',
                    text: `医院的发热门诊排着长队。

我以家人的名义，挂号购买了大量的药品和医疗物资：

- 退烧药、止痛药、消炎药
- 绷带、纱布、酒精、碘伏
- 冻伤膏、创可贴、医疗剪刀
- 维生素片、增强免疫力的保健品

医护人员忙得不可开交，最近感冒发烧的人特别多。

他们不知道，真正的考验即将到来。

我将药品一箱箱搬上车，目光扫过医院这座庞大的建筑。

也许，这里以后会成为避难所？但那不是现在的我该考虑的事。`,
                    textType: 'narrator',
                    effect: {
                        supplies: {
                            '退烧药': 20,
                            '止痛药': 15,
                            '消炎药': 20,
                            '医疗包': 5,
                            '维生素': 10
                        },
                        money: -3000
                    },
                    choices: [
                        {
                            text: '继续采购防寒物资',
                            type: 'action',
                            next: 'outdoor_shop'
                        },
                        {
                            text: '寻找合适的安全屋',
                            type: 'action',
                            next: 'find_safehouse'
                        }
                    ]
                },

                organize_supplies: {
                    id: 'organize_supplies',
                    location: '整理物资',
                    image: 'https://images.unsplash.com/photo-158股份制1234567890123456-0?w=1200',
                    text: `回到家中，我开始整理这疯狂一天采购的物资。

将所有东西摊开在客厅，密密麻麻堆成小山：

**食物储备**：
方便面、压缩饼干、罐头、纯净水、大米、食用油、盐......

**药品医疗**：
退烧药、消炎药、冻伤膏、创可贴、医疗包......

**防寒装备**：
羽绒睡袋、防寒服、保暖手套、登山靴、暖宝宝......

**工具燃料**：
酒精块、便携炉、丁烷气罐、手电筒、工具箱......

我运用异能，将这些物资一一收入空间。挥手间，所有东西整齐地分类存放在储物空间中。

**空间异能已升级！容量大幅提升！**`,
                    textType: 'narrator',
                    effect: {
                        ability: '空间异能',
                        abilityLevel: 2,
                        day: 2
                    },
                    choices: [
                        {
                            text: '休息一晚，明天继续采购',
                            type: 'action',
                            next: 'day2_morning'
                        },
                        {
                            text: '连夜寻找安全屋位置',
                            type: 'action',
                            next: 'find_safehouse'
                        }
                    ]
                },

                find_safehouse: {
                    id: 'find_safehouse',
                    location: '寻找安全屋',
                    image: 'https://images.unsplash.com/photo-1482192505345-5655af888cc4?w=1200',
                    text: `安全屋的选择至关重要。

我回忆着前世的记忆，筛选出几个备选地点：

1. **郊区独栋别墅**：偏僻但目标明显，容易被掠夺者盯上
2. **老旧防空洞**：坚固但潮湿，通风不佳
3. **高层公寓**：视野好但需要大量燃料取暖
4. **乡下农家院**：偏僻隐蔽，有井水和土地可种植

我在城市中穿梭，考察每个地点的优劣。

最终，一个念头闪过——

**地下室！** 地下空间温度相对稳定，而且不易被外人发现。

我找到了一个废弃的地下仓库，位置偏僻，空间巨大。`,
                    textType: 'narrator',
                    effect: { location: '地下仓库' },
                    choices: [
                        {
                            text: '立即开始改造这个地下仓库',
                            type: 'action',
                            next: 'setup_safehouse',
                            good: true
                        },
                        {
                            text: '继续寻找更好的地点',
                            type: 'action',
                            next: 'find_safehouse_better'
                        }
                    ]
                },

                find_safehouse_better: {
                    id: 'find_safehouse_better',
                    location: '继续寻找',
                    image: 'https://images.unsplash.com/photo-1507003211169-0a1dd7228f2d?w=1200',
                    text: `我决定继续寻找更好的安全屋。

在城市中继续探索，我发现了一个更好的选择——

**地铁站地下商城！**

这里空间巨大，可以容纳更多人，而且有现成的基础设施。

更重要的是，这里靠近水源和通风系统。

这是一个理想的避难所！`,
                    textType: 'narrator',
                    effect: { location: '地铁站' },
                    choices: [
                        {
                            text: '开始改造地铁站',
                            type: 'action',
                            next: 'setup_safehouse',
                            good: true
                        },
                        {
                            text: '还是回到之前的地下仓库',
                            type: 'action',
                            next: 'setup_safehouse'
                        }
                    ]
                },

                // 第二天剧情
                day2_morning: {
                    id: 'day2_morning',
                    location: '第二天清晨',
                    image: 'https://images.unsplash.com/photo-1542273917363-3b1817f69a2d?w=1200',
                    text: `第二天清晨，阳光透过窗帘洒进房间。

看了眼手机——距离寒潮爆发还有**两天**。

天气预报显示气温已经开始异常下降，但专家说是"倒春寒"，很快就会恢复。

呵，无知的民众。

我快速洗漱，吞了几口早餐。

今天的计划：继续囤积物资，寻找并改造安全屋。`,
                    textType: 'narrator',
                    effect: { day: 2 },
                    choices: [
                        {
                            text: '继续去市场采购食物',
                            type: 'action',
                            next: 'market_day2'
                        },
                        {
                            text: '去建材市场购买改造材料',
                            type: 'action',
                            next: 'materials_shop'
                        },
                        {
                            text: '改造并布置安全屋',
                            type: 'action',
                            next: 'setup_safehouse'
                        }
                    ]
                },

                market_day2: {
                    id: 'market_day2',
                    location: '农贸市场',
                    image: 'https://images.unsplash.com/photo-1488459716781-31db52582fe9?w=1200',
                    text: `农贸市场里人声鼎沸。

虽然气温有所下降，但大家还在正常生活。

我找到了一个批发商，开始大量采购：

- **干菜**：白菜干、萝卜干、脱水蔬菜
- **主食**：面粉、挂面、玉米面
- **调料**：酱油、醋、辣椒、盐
- **其他**：蜂蜜、糖、巧克力（高热量）

老板乐开了花："老弟，你这是要开餐馆？"

"差不多，备点货。"`,
                    textType: 'narrator',
                    effect: {
                        supplies: {
                            '干菜': 20,
                            '面粉': 20,
                            '挂面': 30,
                            '调料包': 20,
                            '蜂蜜': 5,
                            '巧克力': 20
                        },
                        money: -2000
                    },
                    choices: [
                        {
                            text: '去建材市场购买改造材料',
                            type: 'action',
                            next: 'materials_shop'
                        },
                        {
                            text: '开始改造安全屋',
                            type: 'action',
                            next: 'setup_safehouse'
                        }
                    ]
                },

                materials_shop: {
                    id: 'materials_shop',
                    location: '建材批发市场',
                    image: 'https://images.unsplash.com/photo-1504307651254-35680f356dfd?w=1200',
                    text: `建材市场里冷冷清清，没什么人。

我采购了大量改造安全屋所需的材料：

- **保温材料**：泡沫板、保温棉、密封条
- **搭建材料**：木板、铁丝网、钉子
- **照明设备**：蜡烛、LED灯带、应急灯
- **通风设备**：小型排气扇、通风管

老板好奇地看着我这个只买材料不装修的顾客。

我心想，等寒潮一来，这些材料就是无价之宝。`,
                    textType: 'narrator',
                    effect: {
                        supplies: {
                            '保温板': 20,
                            '保温棉': 10,
                            '密封条': 30,
                            '木板': 50,
                            '铁丝网': 10,
                            '蜡烛': 50,
                            'LED灯带': 10,
                            '通风扇': 2
                        },
                        money: -2500
                    },
                    choices: [
                        {
                            text: '开始改造安全屋',
                            type: 'action',
                            next: 'setup_safehouse'
                        },
                        {
                            text: '继续采购燃料',
                            type: 'action',
                            next: 'buy_fuel'
                        }
                    ]
                },

                setup_safehouse: {
                    id: 'setup_safehouse',
                    location: '改造地下仓库',
                    image: 'https://images.unsplash.com/photo-1504328345606-18bbc8c9d7d1?w=1200',
                    text: `我找到的地下仓库位于城市边缘的工业区，周围人烟稀少。

用钥匙打开锈迹斑斑的大锁，一股霉味扑面而来。

仓库面积约200平方米，层高3米，足够容纳多人。

我开始用采购的材料进行改造：

1. 用保温板封闭墙面缝隙
2. 铺设保温棉增加隔热效果
3. 用密封条处理门窗
4. 安装通风扇保持空气流通
5. 布置LED灯带照明
6. 用木板搭建简易床铺和工作台

**安全屋第一阶段改造完成！**

接下来，我用异能将物资存入仓库。

看着这个即将成为末日中庇护所的地方，我心中涌起一丝希望。`,
                    textType: 'narrator',
                    effect: {
                        base: true,
                        day: 2
                    },
                    choices: [
                        {
                            text: '继续完善安全屋设施',
                            type: 'action',
                            next: 'upgrade_safehouse'
                        },
                        {
                            text: '回城继续囤积最后一批物资',
                            type: 'action',
                            next: 'final_hoarding'
                        }
                    ]
                },

                upgrade_safehouse: {
                    id: 'upgrade_safehouse',
                    location: '升级安全屋',
                    image: 'https://images.unsplash.com/photo-1504328345606-18bbc8c9d7d1?w=1200',
                    text: `我继续完善安全屋。

安装了太阳能电池板和风力发电机，确保有稳定的电力供应。
建造了简易的净水系统，可以将冰雪融化后净化饮用。
设置了陷阱和防御工事，防止不速之客闯入。

**安全屋升级完成！**

现在这里已经是一个功能完善的避难所了。`,
                    textType: 'narrator',
                    effect: { day: 2 },
                    choices: [
                        {
                            text: '休息一晚，准备迎接寒潮',
                            type: 'action',
                            next: 'day3_final'
                        },
                        {
                            text: '再去采购一些物资',
                            type: 'action',
                            next: 'final_hoarding'
                        }
                    ]
                },

                final_hoarding: {
                    id: 'final_hoarding',
                    location: '最后的囤货',
                    image: 'https://images.unsplash.com/photo-1488459716781-31db52582fe9?w=1200',
                    text: `我再次出门，进行最后的物资采购。

在这最后的时间里，我采购了：
- 更多的食物和水
- 备用电池和充电设备
- 加固安全屋的材料
- 一些书籍和娱乐用品

街上已经开始出现恐慌的迹象，物价也在飞涨。

但我已经准备好了。`,
                    textType: 'narrator',
                    effect: {
                        supplies: { '方便面': 20, '罐头': 20, '纯净水': 50 },
                        day: 2
                    },
                    choices: [
                        {
                            text: '返回安全屋',
                            type: 'action',
                            next: 'day3_final'
                        }
                    ]
                },

                buy_electronics: {
                    id: 'buy_electronics',
                    location: '电子设备',
                    image: 'https://images.unsplash.com/photo-1510519138101-570d1dca3d66?w=1200',
                    text: `我购买了一些关键的电子设备：

- 太阳能充电器和大容量充电宝
- 对讲机和收音机
- 便携式投影仪（用于娱乐）
- 备用手机和电池

这些设备在末日中会非常有用。`,
                    textType: 'narrator',
                    effect: { supplies: { '充电宝': 5, '对讲机': 2 } },
                    choices: [
                        {
                            text: '返回安全屋',
                            type: 'action',
                            next: 'day3_final'
                        }
                    ]
                },

                buy_fuel: {
                    id: 'buy_fuel',
                    location: '燃料采购',
                    image: 'https://images.unsplash.com/photo-1498084393753-b411b2d26b34?w=1200',
                    text: `燃料在末日中至关重要。

我找到了一个燃料专卖店：

- **煤炭**：燃烧时间长，热值高，适合长期取暖
- **木炭**：易于点燃，可用于快速取暖
- **酒精**：高效燃料，可用于烹饪
- **固体燃料块**：便携方便，野外生存必备

"你要这么多燃料？"老板惊讶地问。

"开个小厂，备用。"我平静地回答。

付完钱，看着一箱箱燃料被装上车，我知道这些燃料在冰封中将价值连城。`,
                    textType: 'narrator',
                    effect: {
                        supplies: {
                            '煤炭': 500,
                            '木炭': 200,
                            '酒精': 50,
                            '固体燃料': 100
                        },
                        money: -3000
                    },
                    choices: [
                        {
                            text: '回安全屋储存燃料',
                            type: 'action',
                            next: 'setup_safehouse'
                        },
                        {
                            text: '继续采购电器和照明',
                            type: 'action',
                            next: 'buy_electronics'
                        }
                    ]
                },

                // 第三天剧情
                final_risk: {
                    id: 'final_risk',
                    location: '最后的冒险',
                    image: 'https://images.unsplash.com/photo-1507003211169-0a1dd7228f2d?w=1200',
                    text: `我决定冒一次险，在寒潮来临前最后出去一趟。

街上已经乱成一团，人们疯狂抢购物资。

我利用空间异能快速穿梭，收集了一些遗漏的重要物资。

就在我准备离开时，看到一个小女孩在寒风中瑟瑟发抖。

她的父母似乎失散了。`,
                    textType: 'narrator',
                    effect: { supplies: { '医疗包': 2 }, karma: 5 },
                    choices: [
                        {
                            text: '带上小女孩一起走',
                            type: 'good',
                            next: 'rescue_child',
                            good: true
                        },
                        {
                            text: '自己离开，顾不上她',
                            type: 'evil',
                            next: 'final_check',
                            evil: true
                        }
                    ]
                },

                rescue_child: {
                    id: 'rescue_child',
                    location: '救助小女孩',
                    image: 'https://images.unsplash.com/photo-1541481069435-967c78b91743?w=1200',
                    text: `我抱起小女孩，用异能快速返回安全屋。

"别怕，我会保护你的。"

小女孩紧紧抓住我的衣服，眼中充满了恐惧。

她叫小雨，今年七岁。她的父母在混乱中走失了。

也许，她的父母已经......

我决定照顾她，让她成为这个末日中我要保护的人。`,
                    textType: 'narrator',
                    effect: { allies: ['小雨'], karma: 15 },
                    choices: [
                        {
                            text: '继续剧情',
                            type: 'action',
                            next: 'freeze_begins'
                        }
                    ]
                },

                day3_final: {
                    id: 'day3_final',
                    location: '第三天 - 最后一天',
                    image: 'https://images.unsplash.com/photo-1482784160150-8d23a7800eed?w=1200',
                    text: `第三天。

手机推送消息疯狂弹出："紧急通知！气温异常下降！""政府呼吁市民储备物资！"

晚了。

但我已经准备好了。

新闻里播报着各地开始出现极端天气，道路交通逐渐瘫痪。

而我，坐在改造完毕的安全屋里，看着空间异能中堆积如山的物资。

**距离寒潮全面爆发，还有不到24小时。**

明天，这个世界将彻底改变。`,
                    textType: 'narrator',
                    effect: { day: 3 },
                    choices: [
                        {
                            text: '最后检查一遍物资和安全屋',
                            type: 'action',
                            next: 'final_check',
                            good: true
                        },
                        {
                            text: '冒险外出做最后的准备',
                            type: 'action',
                            next: 'final_risk'
                        }
                    ]
                },

                final_check: {
                    id: 'final_check',
                    location: '最后的准备',
                    image: 'https://images.unsplash.com/photo-1517825738774-7de9363ef735?w=1200',
                    text: `我仔细检查了安全屋的每一个角落。

**物资储备**：充足，可支撑至少半年

**异能状态**：空间储物已完全掌控

**安全屋**：保温完善，电力设备齐全

**武器**：一把工兵铲，必要时可防身

窗外，天空开始飘起雪花。

这是寒潮的前兆。

我深吸一口气，准备迎接末日的到来。

突然，手机响了——

"喂？请问是陈先生吗？我是之前的邻居李叔的女儿......我爸说看到你这两天一直在忙......能请教一下怎么储备物资吗？"

**抉择时刻——如何回应？**`,
                    textType: 'narrator',
                    effect: { day: 3 },
                    choices: [
                        {
                            text: '警告他们即将到来的危机，教会他们生存',
                            type: 'good',
                            next: 'help_neighbors',
                            good: true
                        },
                        {
                            text: '含糊其辞，不透露任何信息',
                            type: 'neutral',
                            next: 'ignore_neighbors'
                        },
                        {
                            text: '拒绝帮助，保护自己的资源',
                            type: 'evil',
                            next: 'refuse_neighbors',
                            evil: true
                        }
                    ]
                },

                help_neighbors: {
                    id: 'help_neighbors',
                    location: '帮助邻居',
                    image: 'https://images.unsplash.com/photo-1542601906990-b4d3fb778b09?w=1200',
                    text: `"李叔，你们听我说，情况比你们想象的严重得多......"

我用最快的速度向他们解释即将发生的事情。

并利用最后的几小时，教会他们基本的生存技能。

李叔的女儿小李感激地看着我："陈哥，谢谢你！我永远记得这份恩情！"

在末日中，多一个伙伴就多一分希望。

而他们，也将成为我在冰封世界中最初的战友。`,
                    textType: 'narrator',
                    effect: {
                        allies: ['李叔', '小李'],
                        karma: 10
                    },
                    choices: [
                        {
                            text: '继续剧情',
                            type: 'action',
                            next: 'freeze_begins'
                        }
                    ]
                },

                ignore_neighbors: {
                    id: 'ignore_neighbors',
                    location: '沉默以对',
                    image: 'https://images.unsplash.com/photo-1517260739c3-44a5d46245e5?w=1200',
                    text: `"哦，随便备点就行。"

我含糊地回答，然后挂断了电话。

不是我不善良。

在这个即将崩溃的世界里，善良可能意味着死亡。

我需要保护好自己和仅有的资源。

至于其他人......就看他们自己的造化吧。

窗外的雪越下越大，末日即将来临。`,
                    textType: 'narrator',
                    effect: {
                        karma: 0
                    },
                    choices: [
                        {
                            text: '继续剧情',
                            type: 'action',
                            next: 'freeze_begins'
                        }
                    ]
                },

                refuse_neighbors: {
                    id: 'refuse_neighbors',
                    location: '冷酷拒绝',
                    image: 'https://images.unsplash.com/photo-1517311307972-6aa2d3f79f89?w=1200',
                    text: `"不好意思，我很忙。"

我直接挂断了电话。

在这个世界，每个人都只能靠自己。

如果我帮助了别人，就意味着我的资源会减少，我的生存几率会降低。

我不能冒险。

我不能心软。

这是我能在末日中活下去的代价。

窗外的雪越下越大，我拉上窗帘，不再看外界。`,
                    textType: 'narrator',
                    effect: {
                        karma: -10
                    },
                    choices: [
                        {
                            text: '继续剧情',
                            type: 'action',
                            next: 'freeze_begins'
                        }
                    ]
                },

                freeze_begins: {
                    id: 'freeze_begins',
                    location: '冰封降临',
                    image: 'https://images.unsplash.com/photo-1491002052546-bf38f186af56?w=1200',
                    text: `**第一天**

窗外，狂风大作，暴雪倾盆。

手机上的气温显示：-47°C。

而这，才只是开始。

新闻里不断播报着紧急状态、停工停课、交通瘫痪的消息。

但真正的末日，才刚刚拉开序幕。

电力开始不稳定，手机信号时断时续。

我关掉手机，坐在安全屋里，听着外面的风雪呼啸。

**活下去。**

这是我在这个冰封世界中唯一的信念。`,
                    textType: 'narrator',
                    effect: {
                        day: 1,
                        temperature: -47
                    },
                    choices: [
                        {
                            text: '等待寒潮高峰过去',
                            type: 'wait',
                            next: 'day2_freeze'
                        },
                        {
                            text: '冒险外出探索情况',
                            type: 'risk',
                            next: 'first_explore'
                        }
                    ]
                },

                day3_wait: {
                    id: 'day3_wait',
                    location: '第三天 - 等待',
                    image: 'https://images.unsplash.com/photo-1507003211169-0a1dd7228f2d?w=1200',
                    text: `我选择继续等待。

在安全屋里，我整理物资、修炼异能，为未来做准备。

时间一天天过去，气温继续下降。

**气温：-105°C**

窗外的风雪似乎没有减弱的迹象。

但我已经准备好了。`,
                    textType: 'narrator',
                    effect: {
                        day: 3,
                        temperature: -105
                    },
                    choices: [
                        {
                            text: '出门探索',
                            type: 'action',
                            next: 'first_explore'
                        }
                    ]
                },

                day2_freeze: {
                    id: 'day2_freeze',
                    location: '第二天 - 极寒',
                    image: 'https://images.unsplash.com/photo-1483664852095-d6cc6870702d?w=1200',
                    text: `**第二天**

气温：-89°C

窗外是一片死寂的白色世界。

曾经的繁华都市已经被冰封，高楼大厦变成了巨大的冰雕。

我在安全屋里升起炉火，温暖着这个小小的庇护所。

手机已经完全没信号了。

外面的世界，已经与我无关。

但我知道，在某个角落，也许还有其他幸存者......

还有那些，前世记忆中可怕的变异冰兽......

**明天开始，我将踏足这片冰封的地狱。**`,
                    textType: 'narrator',
                    effect: {
                        day: 2,
                        temperature: -89
                    },
                    choices: [
                        {
                            text: '继续等待，暂时不出门',
                            type: 'wait',
                            next: 'day3_wait'
                        },
                        {
                            text: '是时候出去探索了',
                            type: 'action',
                            next: 'first_explore'
                        }
                    ]
                },

                study_beast: {
                    id: 'study_beast',
                    location: '研究冰兽',
                    image: 'https://images.unsplash.com/photo-1557308520-9c98cb70f5a5?w=1200',
                    text: `我开始研究这些变异冰兽的习性。

根据前世的记忆和这次的遭遇，我总结出一些规律：

1. **弱点**：它们虽然耐寒，但似乎害怕高温和强光
2. **习性**：喜欢在黑暗中活动，听觉和嗅觉异常灵敏
3. **攻击方式**：主要依靠利爪和冰霜吐息

也许，我可以利用这些弱点来对付它们。`,
                    textType: 'narrator',
                    effect: { exp: 5 },
                    choices: [
                        {
                            text: '继续修炼异能',
                            type: 'action',
                            next: 'train_ability'
                        },
                        {
                            text: '再次外出探索',
                            type: 'action',
                            next: 'first_explore'
                        }
                    ]
                },

                rest_and_plan: {
                    id: 'rest_and_plan',
                    location: '休整计划',
                    image: 'https://images.unsplash.com/photo-1507003211169-0a1dd7228f2d?w=1200',
                    text: `我回到安全屋，整理收获并制定新的计划。

目前的物资足够支撑一段时间，但我需要寻找更多资源。

也许，是时候去寻找其他幸存者了。

或者，我可以尝试寻找那个传说中的地下避难所......`,
                    textType: 'narrator',
                    effect: { day: 1 },
                    choices: [
                        {
                            text: '寻找幸存者',
                            type: 'action',
                            next: 'find_survivors'
                        },
                        {
                            text: '修炼异能',
                            type: 'action',
                            next: 'train_ability'
                        }
                    ]
                },

                first_explore: {
                    id: 'first_explore',
                    location: '冰封废墟',
                    image: 'https://images.unsplash.com/photo-1519681393784-d120267933ba?w=1200',
                    text: `我穿上全套防寒装备，打开了安全屋的门。

刺骨的寒风瞬间涌入，几乎让我窒息。

呼出的气体瞬间变成冰晶。

我艰难地迈出脚步，踩在齐膝深的积雪中。

周围的景象让我心悸——

汽车被冰封在道路上， abandoned的人们倒在路边变成了冰雕。

曾经的便利店、超市早已被洗劫一空。

就在我收集物资时——

**一声低沉的咆哮从远处传来。**

我猛然回头，看到一个巨大的身影在风雪中移动。

那是一头变异冰兽！`,
                    textType: 'narrator',
                    effect: { danger: true },
                    choices: [
                        {
                            text: '立刻躲回安全屋',
                            type: 'safe',
                            next: 'escape_beast'
                        },
                        {
                            text: '尝试用异能对抗它',
                            type: 'risk',
                            next: 'fight_beast'
                        },
                        {
                            text: '悄悄绕路避开它',
                            type: 'stealth',
                            next: 'avoid_beast'
                        }
                    ]
                },

                escape_beast: {
                    id: 'escape_beast',
                    location: '紧急撤退',
                    image: 'https://images.unsplash.com/photo-1517022812141-23620dba5c23?w=1200',
                    text: `我转身就跑，以最快的速度冲回安全屋！

身后传来沉重的脚步声和咆哮声，越来越近！

**砰！**

我重重地关上门，用尽全力抵住。

一声巨响，冰兽撞在了门上！

门板发出可怕的嘎吱声，但我成功地抵住了它！

片刻后，那头野兽似乎失去了兴趣，转身离开了。

我瘫坐在地上，心脏狂跳。

**太危险了......**`,
                    textType: 'narrator',
                    effect: {
                        health: -10,
                        escape: true
                    },
                    choices: [
                        {
                            text: '好好休息，恢复体力',
                            type: 'rest',
                            next: 'rest_after_escape'
                        },
                        {
                            text: '研究如何对抗这些冰兽',
                            type: 'study',
                            next: 'study_beast'
                        }
                    ]
                },

                fight_beast: {
                    id: 'fight_beast',
                    location: '对战冰兽',
                    image: 'https://images.unsplash.com/photo-1578662996442-48f60103fc96?w=1200',
                    text: `我深吸一口气，体内的异能涌动！

空间的力量在我手中凝聚——

**"碎空斩！"**

空气撕裂的声音，那头冰兽被空间刃击中，发出痛苦的嘶吼！

但这头野兽皮糙肉厚，只是受了轻伤！

它愤怒地朝我扑来，锋利的爪子划过我的手臂！

我强忍剧痛，再次发动异能，将它击退！

趁着它眩晕的瞬间，我转身逃跑。

**战斗经验 +1**`,
                    textType: 'narrator',
                    effect: {
                        health: -25,
                        exp: 1,
                        abilityUp: true
                    },
                    choices: [
                        {
                            text: '立即返回安全屋疗伤',
                            type: 'rest',
                            next: 'rest_after_fight'
                        }
                    ]
                },

                avoid_beast: {
                    id: 'avoid_beast',
                    location: '潜行避开',
                    image: 'https://images.unsplash.com/photo-1508193638397-1c4234db14d8?w=1200',
                    text: `我压低身体，悄悄地绕过那头冰兽。

它似乎没有注意到我，继续在废墟中游荡。

我小心翼翼地收集了一些散落的物资，然后安全返回。

这次探索虽然没有收获太多，但至少知道了——

**这片冰封的土地上，不只有寒冷。**

还有那些变异的怪物。`,
                    textType: 'narrator',
                    effect: {
                        supplies: {
                            '罐头': 2,
                            '保暖手套': 1
                        },
                        stealth: true
                    },
                    choices: [
                        {
                            text: '回安全屋整理收获',
                            type: 'rest',
                            next: 'rest_and_plan'
                        }
                    ]
                },

                rest_after_escape: {
                    id: 'rest_after_escape',
                    location: '安全屋 - 休养',
                    image: 'https://images.unsplash.com/photo-1517411032315-54ef2cb783bb?w=1200',
                    text: `回到安全屋，我处理了手臂上的擦伤。

还好防寒服够厚，只是皮外伤。

吃了点东西补充体力，我开始思考接下来该怎么办。

那头冰兽的恐怖，让我意识到——

**光有物资是不够的。**

我需要更强的异能，才能在这个世界生存。

也许，该找找有没有其他幸存者，了解一下他们的情况......`,
                    textType: 'narrator',
                    effect: {
                        health: 10
                    },
                    choices: [
                        {
                            text: '继续探索，寻找幸存者',
                            type: 'explore',
                            next: 'find_survivors'
                        },
                        {
                            text: '专心修炼异能',
                            type: 'train',
                            next: 'train_ability'
                        }
                    ]
                },

                rest_after_fight: {
                    id: 'rest_after_fight',
                    location: '安全屋 - 疗伤',
                    image: 'https://images.unsplash.com/photo-1518655048521-f130df041f66?w=1200',
                    text: `伤口传来阵阵剧痛，我咬牙处理着伤口。

还好有消炎药和绷带，不至于感染。

这头冰兽比我想象的更加危险。

但我也发现了——**异能可以伤害到它！**

这意味着，只要不断提升异能，就有可能在这个世界站稳脚跟。

休息过后，我的伤势恢复了一些。

现在，是时候考虑下一步了。`,
                    textType: 'narrator',
                    effect: {
                        health: 15,
                        ability: '空间异能',
                        abilityLevel: 2
                    },
                    choices: [
                        {
                            text: '寻找其他幸存者',
                            type: 'explore',
                            next: 'find_survivors'
                        },
                        {
                            text: '继续探索物资点',
                            type: 'explore',
                            next: 'explore_more'
                        }
                    ]
                },

                explore_more: {
                    id: 'explore_more',
                    location: '继续探索',
                    image: 'https://images.unsplash.com/photo-1519681393784-d120267933ba?w=1200',
                    text: `我继续在冰封的城市中探索。

在一个废弃的商场里，我发现了一些遗漏的物资：
- 更多的食物和水
- 一些日用品
- 甚至还有一些种子（也许未来可以种植）

在探索过程中，我还发现了一些关于寒潮的研究资料......`,
                    textType: 'narrator',
                    effect: {
                        supplies: { '种子': 10, '罐头': 5 },
                        exp: 5
                    },
                    choices: [
                        {
                            text: '返回安全屋',
                            type: 'action',
                            next: 'rest_and_plan'
                        }
                    ]
                },

                find_survivors: {
                    id: 'find_survivors',
                    location: '寻找幸存者',
                    image: 'https://images.unsplash.com/photo-1529257414772-1960b7bea4eb?w=1200',
                    text: `几天后，我在风雪中发现了一座亮着灯火的建筑。

是幸存者营地！

我小心翼翼地靠近，被巡逻的人拦下。

"站住！你是什么人？"

看着他们警惕的眼神，我缓缓举起双手。

"我叫陈默，只是路过的幸存者。"

一个中年男人走出人群，仔细打量着我。

"我是这个营地的负责人，叫老张。看你一个人在外面跑，胆子不小。"

**遇到幸存者营地！**`,
                    textType: 'narrator',
                    effect: {
                        camp: true,
                        npc: '老张'
                    },
                    choices: [
                        {
                            text: '表示愿意加入营地',
                            type: 'good',
                            next: 'join_camp'
                        },
                        {
                            text: '只是交换情报，不加入',
                            type: 'neutral',
                            next: 'exchange_info'
                        },
                        {
                            text: '警惕地离开，不信任他们',
                            type: 'evil',
                            next: 'leave_camp',
                            evil: true
                        }
                    ]
                },

                join_camp: {
                    id: 'join_camp',
                    location: '加入营地',
                    image: 'https://images.unsplash.com/photo-1464037866556-6812c9d1c72e?w=1200',
                    text: `老张向我介绍了营地的情况：

这里有二十多个幸存者，大多是附近的居民。

他们占据了郊区的一座学校，用发电机维持着基本的生活。

"我们这里缺医少药，你能帮忙吗？"老张问道。

看着这些在末日中挣扎求生的人们，我心中涌起一股责任感。

也许，是时候为这个破碎的世界做点什么了。`,
                    textType: 'narrator',
                    effect: {
                        allies: ['幸存者营地'],
                        karma: 20,
                        base: '学校营地'
                    },
                    choices: [
                        {
                            text: '分享一部分物资给营地',
                            type: 'good',
                            next: 'share_supplies'
                        },
                        {
                            text: '用异能帮助营地加强防御',
                            type: 'action',
                            next: 'help_defense'
                        }
                    ]
                },

                exchange_info: {
                    id: 'exchange_info',
                    location: '交换情报',
                    image: 'https://images.unsplash.com/photo-1531206715517-5c0ba140b2b8?w=1200',
                    text: `"我们可以交换一些情报。"我说。

老张点点头，和我分享了他们的发现：

- 城市中心有大量变异冰兽聚集
- 郊区地下有一个军火库
- 据说北边有更大的幸存者聚居地

作为交换，我也分享了一些我的生存经验。

我们保持了松散的联盟关系，但并未深入交往。

在这个世界，信任是奢侈品。`,
                    textType: 'narrator',
                    effect: {
                        info: true
                    },
                    choices: [
                        {
                            text: '前往军火库探索',
                            type: 'action',
                            next: 'arsenal'
                        },
                        {
                            text: '继续独自生存之路',
                            type: 'action',
                            next: 'solo_path'
                        }
                    ]
                },

                share_supplies: {
                    id: 'share_supplies',
                    location: '分享物资',
                    image: 'https://images.unsplash.com/photo-1507003211169-0a1dd7228f2d?w=1200',
                    text: `我决定分享一部分物资给营地。

从空间异能中取出食物、药品和燃料，分给那些需要的人。

"这......这太感谢你了！"老张激动地说。

看着人们感激的眼神，我感受到了一种久违的温暖。

也许，在这个末日中，人与人之间的信任和互助，才是真正的希望。`,
                    textType: 'narrator',
                    effect: {
                        karma: 20,
                        supplies: { '方便面': -10, '罐头': -5, '医疗包': -2 }
                    },
                    choices: [
                        {
                            text: '帮助营地加强防御',
                            type: 'action',
                            next: 'help_defense'
                        },
                        {
                            text: '离开营地，继续探索',
                            type: 'action',
                            next: 'arsenal'
                        }
                    ]
                },

                help_defense: {
                    id: 'help_defense',
                    location: '加强防御',
                    image: 'https://images.unsplash.com/photo-1504328345606-18bbc8c9d7d1?w=1200',
                    text: `我用空间异能帮助营地加强防御。

在营地周围设置了陷阱和预警系统，用冰块建造了坚固的防御墙。

幸存者们对我的能力感到惊叹。

"有你在，我们安全多了。"老张说。

也许，这里可以成为一个真正的避难所......`,
                    textType: 'narrator',
                    effect: {
                        karma: 10,
                        exp: 5
                    },
                    choices: [
                        {
                            text: '离开营地，去军火库探索',
                            type: 'action',
                            next: 'arsenal'
                        }
                    ]
                },

                solo_path: {
                    id: 'solo_path',
                    location: '独自之路',
                    image: 'https://images.unsplash.com/photo-1519681393784-d120267933ba?w=1200',
                    text: `我选择继续独自生存。

在这个末日中，独自行动更加灵活，可以更快地探索和收集物资。

我告别了老张的营地，继续我的旅程。`,
                    textType: 'narrator',
                    effect: { solo: true },
                    choices: [
                        {
                            text: '前往军火库',
                            type: 'action',
                            next: 'arsenal'
                        },
                        {
                            text: '继续探索',
                            type: 'action',
                            next: 'first_explore'
                        }
                    ]
                },

                continue_alone: {
                    id: 'continue_alone',
                    location: '独自探索',
                    image: 'https://images.unsplash.com/photo-1519681393784-d120267933ba?w=1200',
                    text: `我继续独自在冰封的世界中探索。

没有同伴，但也没有背叛的风险。

在废墟中，我找到了更多物资和一些关于寒潮的线索......`,
                    textType: 'narrator',
                    effect: {
                        supplies: { '罐头': 3 },
                        exp: 5
                    },
                    choices: [
                        {
                            text: '前往军火库',
                            type: 'action',
                            next: 'arsenal'
                        }
                    ]
                },

                leave_camp: {
                    id: 'leave_camp',
                    location: '独自离去',
                    image: 'https://images.unsplash.com/photo-1519681393784-d120267933ba?w=1200',
                    text: `"谢谢你们的情报，我该走了。"

我转身消失在风雪中，没有回头。

不信任任何人，是活下去的铁律。

在这个崩坏的世界里，每个人都可能是威胁。

我不能因为一时的软弱，而让自己陷入危险。

但是......

看着身后那盏温暖的灯火，我心中有一丝说不出的滋味。

**也许有一天，我会后悔这个决定。**`,
                    textType: 'narrator',
                    effect: {
                        karma: -20,
                        solo: true
                    },
                    choices: [
                        {
                            text: '继续探索这个冰封的世界',
                            type: 'explore',
                            next: 'continue_alone'
                        }
                    ]
                },

                train_ability: {
                    id: 'train_ability',
                    location: '异能修炼',
                    image: 'https://images.unsplash.com/photo-1507413245164-6160d8298b31?w=1200',
                    text: `在安全屋里，我开始专心修炼异能。

感受着空间的力量在体内流转......

**空间储物 → 空间感知 → 空间切割**

每一天，我都能感觉到异能在变强。

终于——

**异能突破！空间异能达到Lv.3！**

我可以感知更广阔的空间，甚至能够进行短距离的瞬间移动！

**新技能解锁：空间闪烁（短距离瞬移）**`,
                    textType: 'narrator',
                    effect: {
                        ability: '空间异能',
                        abilityLevel: 3,
                        skill: '空间闪烁'
                    },
                    choices: [
                        {
                            text: '带着新能力继续探索',
                            type: 'action',
                            next: 'explore_new_ability'
                        }
                    ]
                },

                arsenal: {
                    id: 'arsenal',
                    location: '废弃军火库',
                    image: 'https://images.unsplash.com/photo-1569230516306-5a8cb5586399?w=1200',
                    text: `根据幸存者提供的线索，我找到了这座废弃的军火库。

大门半掩着，里面漆黑一片。

我用头灯照亮入口，小心翼翼地走进去。

架子上摆满了各种武器弹药——虽然年代久远，但大部分还能使用。

我收集了一些有用的东西：
- 工兵铲
- 防爆盾牌
- 战术手电筒
- 一些步枪和弹药

**获得重要物资！**`,
                    textType: 'narrator',
                    effect: {
                        supplies: {
                            '工兵铲': 1,
                            '防爆盾牌': 1,
                            '战术手电': 2,
                            '步枪': 2,
                            '弹药': 50
                        },
                        weapon: true
                    },
                    choices: [
                        {
                            text: '安全返回安全屋',
                            type: 'safe',
                            next: 'return_with_weapons'
                        },
                        {
                            text: '继续深入探索',
                            type: 'risk',
                            next: 'deep_explore_arsenal'
                        }
                    ]
                },

                deep_explore_arsenal: {
                    id: 'deep_explore_arsenal',
                    location: '深入军火库',
                    image: 'https://images.unsplash.com/photo-1569230516306-5a8cb5586399?w=1200',
                    text: `我决定继续深入探索军火库。

在更深处，我发现了一个隐藏的地下室。

这里存放着更先进的武器和一些研究资料......

资料显示，在寒潮来临之前，这里曾进行过一项秘密研究。

也许，这和寒潮的爆发有关？`,
                    textType: 'narrator',
                    effect: {
                        supplies: { '弹药': 30 },
                        exp: 10
                    },
                    choices: [
                        {
                            text: '返回安全屋研究资料',
                            type: 'action',
                            next: 'return_with_weapons'
                        }
                    ]
                },

                return_with_weapons: {
                    id: 'return_with_weapons',
                    location: '满载而归',
                    image: 'https://images.unsplash.com/photo-1527192491265-7e15c55b1ed2?w=1200',
                    text: `带着这些武器弹药，我安全返回了安全屋。

现在，我不仅有了充足的食物和物资，还有了足以防身的武器。

面对那些变异冰兽，我终于有了反击的能力。

**生存能力大幅提升！**

但是，这片冰封世界的秘密，还远远没有揭开......

**【第一章 完】**`,
                    textType: 'narrator',
                    effect: {
                        chapter: 1,
                        complete: true
                    },
                    choices: [
                        {
                            text: '开始第二章',
                            type: 'continue',
                            next: 'chapter2_start'
                        }
                    ]
                },

                explore_new_ability: {
                    id: 'explore_new_ability',
                    location: '新能力探索',
                    image: 'https://images.unsplash.com/photo-1531306728370-e2ebd9d7bb99?w=1200',
                    text: `我开始探索新获得的空间闪烁能力。

通过不断练习，我发现可以进行更远距离的瞬移。

而且，我似乎还能感知到空间中的能量波动......

也许，还有更多的能力等待我去发掘。`,
                    textType: 'narrator',
                    effect: {
                        ability: '空间闪烁',
                        abilityLevel: 2,
                        exp: 5
                    },
                    choices: [
                        {
                            text: '开始第二章',
                            type: 'continue',
                            next: 'chapter2_start'
                        }
                    ]
                },

                // 第二章开始
                chapter2_start: {
                    id: 'chapter2_start',
                    location: '第二章 - 冰原征途',
                    image: 'https://images.unsplash.com/photo-1518173946687-a4c8892bbd9f?w=1200',
                    text: `**第二章：冰原征途**

冰封世界已经持续了一个月。

我的安全屋运转良好，物资充足。

但我知道，躲藏不是长久之计。

是时候，去寻找这个世界的真相了。

根据前世模糊的记忆，北方的冰原深处，似乎隐藏着什么......

**整装待发，开始新的旅程。**`,
                    textType: 'narrator',
                    effect: {
                        chapter: 2,
                        day: 30
                    },
                    choices: [
                        {
                            text: '向北方的冰原进发',
                            type: 'action',
                            next: 'journey_north'
                        },
                        {
                            text: '先去寻找其他幸存者',
                            type: 'explore',
                            next: 'find_more_survivors'
                        }
                    ]
                },

                find_more_survivors: {
                    id: 'find_more_survivors',
                    location: '寻找更多幸存者',
                    image: 'https://images.unsplash.com/photo-1529257414772-1960b7bea4eb?w=1200',
                    text: `我决定先寻找更多幸存者。

在冰封的城市中，我发现了更多的幸存者营地。

有的营地秩序井然，有的则陷入混乱。

通过交流，我了解到各地的情况都很艰难，但也有人找到了生存的方法。`,
                    textType: 'narrator',
                    effect: { exp: 5 },
                    choices: [
                        {
                            text: '继续向北探索',
                            type: 'action',
                            next: 'journey_north'
                        },
                        {
                            text: '建立幸存者联盟',
                            type: 'action',
                            next: 'build_alliance'
                        }
                    ]
                },

                journey_north: {
                    id: 'journey_north',
                    location: '冰封荒原',
                    image: 'https://images.unsplash.com/photo-1478719059408-592965723cbc?w=1200',
                    text: `离开安全屋，我踏上了前往北方的旅程。

无边无际的冰原在眼前延伸，天地间只有白茫茫的一片。

寒风如刀，每呼吸一口都觉得肺在燃烧。

走了大约两个小时，我遇到了一个巨大的冰裂缝。

裂缝宽约十米，深不见底。

对面似乎有一个山洞入口......`,
                    textType: 'narrator',
                    effect: {
                        temperature: -112
                    },
                    choices: [
                        {
                            text: '用异能找到通过的方法',
                            type: 'use_ability',
                            next: 'use_space_ability'
                        },
                        {
                            text: '绕路寻找其他通路',
                            type: 'explore',
                            next: 'find_alternate_path'
                        },
                        {
                            text: '冒险跳过去',
                            type: 'risk',
                            next: 'jump_gap'
                        }
                    ]
                },

                find_alternate_path: {
                    id: 'find_alternate_path',
                    location: '寻找通路',
                    image: 'https://images.unsplash.com/photo-1478719059408-592965723cbc?w=1200',
                    text: `我决定绕路寻找其他通路。

走了很长一段路，终于找到了一个可以通过的地方。

虽然路程更远，但更加安全。

继续前进，山洞入口越来越近了......`,
                    textType: 'narrator',
                    effect: { health: -5 },
                    choices: [
                        {
                            text: '进入山洞',
                            type: 'action',
                            next: 'explore_cave'
                        }
                    ]
                },

                jump_gap: {
                    id: 'jump_gap',
                    location: '冒险跳跃',
                    image: 'https://images.unsplash.com/photo-1531306728370-e2ebd9d7bb99?w=1200',
                    text: `我深吸一口气，助跑，跳跃！

在空中的瞬间，我感受到了死亡的威胁。

**成功了！**

我稳稳地落在了对面。

虽然有些惊险，但节省了不少时间。`,
                    textType: 'narrator',
                    effect: {
                        health: -10,
                        exp: 5
                    },
                    choices: [
                        {
                            text: '进入山洞',
                            type: 'action',
                            next: 'explore_cave'
                        }
                    ]
                },

                use_space_ability: {
                    id: 'use_space_ability',
                    location: '异能跨越',
                    image: 'https://images.unsplash.com/photo-1531306728370-e2ebd9d7bb99?w=1200',
                    text: `我集中精神，发动空间异能！

**空间闪烁！**

一瞬间，我感觉到自己的身体被空间拉扯——

下一秒，我已经站在了裂缝的另一边！

**异能使用成功！**

那个山洞入口就在眼前，里面似乎有微弱的亮光......

是天然形成的冰晶，还是人造的照明？`,
                    textType: 'narrator',
                    effect: {
                        ability: '空间闪烁',
                        exp: 2
                    },
                    choices: [
                        {
                            text: '进入山洞探索',
                            type: 'explore',
                            next: 'explore_cave'
                        },
                        {
                            text: '谨慎地在外围观察',
                            type: 'cautious',
                            next: 'observe_cave'
                        }
                    ]
                },

                observe_cave: {
                    id: 'observe_cave',
                    location: '观察山洞',
                    image: 'https://images.unsplash.com/photo-1518791841217-8f162f1e1131?w=1200',
                    text: `我谨慎地在外围观察。

山洞里似乎没有危险，但我不能掉以轻心。

从远处观察，我发现山洞深处有微弱的光芒。

也许，那里有什么特别的东西......`,
                    textType: 'narrator',
                    effect: { exp: 3 },
                    choices: [
                        {
                            text: '进入山洞',
                            type: 'action',
                            next: 'explore_cave'
                        }
                    ]
                },

                search_temple: {
                    id: 'search_temple',
                    location: '探索遗迹',
                    image: 'https://images.unsplash.com/photo-1518791841217-8f162f1e1131?w=1200',
                    text: `我在遗迹中搜索，寻找更多关于这个古代文明的信息。

在一面墙上，我发现了一些壁画和文字。

根据我的解读，这里曾经是一个研究"空间能量"的地方。

也许，寒潮的爆发和这里有关？`,
                    textType: 'narrator',
                    effect: {
                        exp: 10,
                        knowledge: '古代文明'
                    },
                    choices: [
                        {
                            text: '观察晶体',
                            type: 'action',
                            next: 'observe_crystal'
                        },
                        {
                            text: '继续深入探索',
                            type: 'action',
                            next: 'deep_explore_temple'
                        }
                    ]
                },

                explore_cave: {
                    id: 'explore_cave',
                    location: '神秘山洞',
                    image: 'https://images.unsplash.com/photo-1518791841217-8f162f1e1131?w=1200',
                    text: `山洞内部出奇地温暖。

这里的温度比外面高出许多，仿佛有一个天然的热源。

走着走着，我看到了不可思议的景象——

**山洞深处，竟然有一个巨大的地下空间！**

这里有建筑遗迹！古老的石柱上刻满了奇怪的符文。

而那些亮光，来自于一种发光的蓝色晶体。

这是......古代文明的遗址？`,
                    textType: 'narrator',
                    effect: {
                        discovery: '古代遗迹',
                        rare: '蓝色晶体'
                    },
                    choices: [
                        {
                            text: '靠近观察那些晶体',
                            type: 'explore',
                            next: 'observe_crystal'
                        },
                        {
                            text: '寻找更多关于这个遗迹的信息',
                            type: 'search',
                            next: 'search_temple'
                        }
                    ]
                },

                observe_crystal: {
                    id: 'observe_crystal',
                    location: '能量晶体',
                    image: 'https://images.unsplash.com/photo-1511137255798-a6c1a16f8f6a?w=1200',
                    text: `那些蓝色晶体散发出幽冷的光芒。

当我靠近时，体内的异能突然躁动起来！

**这是......异能晶石！**

我小心翼翼地触碰其中一块——

能量瞬间涌入我的身体！

**异能大幅提升！空间异能达到Lv.4！**

这些晶体，蕴含着强大的能量！

也许，这就是这个冰封世界的秘密之一......`,
                    textType: 'narrator',
                    effect: {
                        ability: '空间异能',
                        abilityLevel: 4,
                        exp: 10
                    },
                    choices: [
                        {
                            text: '收集更多晶体',
                            type: 'collect',
                            next: 'collect_crystals'
                        },
                        {
                            text: '继续探索遗迹深处',
                            type: 'explore',
                            next: 'deep_explore_temple'
                        }
                    ]
                },

                collect_crystals: {
                    id: 'collect_crystals',
                    location: '收集能量晶体',
                    image: 'https://images.unsplash.com/photo-1507413245164-6160d8298b31?w=1200',
                    text: `我开始收集这些能量晶体。

每一块晶体都蕴含着惊人的能量。当我将它们收入空间时，感觉到异能在不断进化。

**获得：能量晶体 x20**

这些晶体不仅能提升异能，似乎还能作为能源使用......

就在我收集时，远处传来了脚步声。

是其他幸存者？还是......`,
                    textType: 'narrator',
                    effect: {
                        supplies: { '能量晶体': 20 },
                        exp: 5
                    },
                    choices: [
                        {
                            text: '隐藏起来观察',
                            type: 'stealth',
                            next: 'hide_and_watch'
                        },
                        {
                            text: '主动现身询问',
                            type: 'action',
                            next: 'meet_newcomer'
                        }
                    ]
                },

                continue_observe: {
                    id: 'continue_observe',
                    location: '继续观察',
                    image: 'https://images.unsplash.com/photo-1475924156734-496f6cac6ec1?w=1200',
                    text: `我继续暗中观察。

那个女子小心翼翼地收集着晶体，看起来对这个地方很熟悉。

她似乎在寻找什么特定的东西......

突然，她停了下来，似乎发现了什么。

"找到了......"她轻声说。

她从怀里拿出一个古老的盒子，似乎要将什么东西放进去......`,
                    textType: 'narrator',
                    effect: { stealth: true },
                    choices: [
                        {
                            text: '现身询问',
                            type: 'action',
                            next: 'meet_newcomer'
                        },
                        {
                            text: '继续观察',
                            type: 'action',
                            next: 'continue_observe'
                        }
                    ]
                },

                rob_her: {
                    id: 'rob_her',
                    location: '抢夺',
                    image: 'https://images.unsplash.com/photo-1552519507-da3b142c6e3d?w=1200',
                    text: `我趁她不备，发动空间异能！

**空间束缚！**

她被空间之力困住，无法动弹。

"你......你想干什么？"她惊恐地问。

我拿走了她的物资和晶体，然后解除了束缚。

"滚。"我说。

她不敢反抗，转身跑掉了。

看着她的背影，我心中没有任何波澜。

在这个世界，弱肉强食。`,
                    textType: 'narrator',
                    effect: {
                        karma: -30,
                        supplies: { '晶体': 5, '罐头': 5 }
                    },
                    choices: [
                        {
                            text: '继续探索',
                            type: 'action',
                            next: 'collect_crystals'
                        }
                    ]
                },

                hide_and_watch: {
                    id: 'hide_and_watch',
                    location: '暗中观察',
                    image: 'https://images.unsplash.com/photo-1475924156734-496f6cac6ec1?w=1200',
                    text: `我躲在石柱后面，屏住呼吸。

一个穿着防寒服的身影出现在视野中。

是一个年轻女子，看起来也是幸存者。她手里拿着武器，警惕地四处张望。

"有人吗？"她轻声呼唤。

她似乎在寻找什么......也许是这些晶体？

我该如何选择？`,
                    textType: 'narrator',
                    effect: { stealth: true },
                    choices: [
                        {
                            text: '出来与她交流',
                            type: 'good',
                            next: 'meet_newcomer'
                        },
                        {
                            text: '继续观察，了解她的目的',
                            type: 'cautious',
                            next: 'continue_observe'
                        },
                        {
                            text: '趁她不备抢走她的物资',
                            type: 'evil',
                            next: 'rob_her',
                            evil: true
                        }
                    ]
                },

                share_and_leave: {
                    id: 'share_and_leave',
                    location: '分享后离开',
                    image: 'https://images.unsplash.com/photo-1507003211169-0a1dd7228f2d?w=1200',
                    text: `我和林雪分享了一些物资和信息。

"谢谢你，"她说，"也许有一天，我们还会再见面。"

我们互相留下了联系方式，然后各自离开。

也许，在这个末日中，多一个朋友不是坏事......`,
                    textType: 'narrator',
                    effect: {
                        karma: 10,
                        allies: ['林雪']
                    },
                    choices: [
                        {
                            text: '继续探索遗迹',
                            type: 'action',
                            next: 'collect_crystals'
                        }
                    ]
                },

                meet_newcomer: {
                    id: 'meet_newcomer',
                    location: '相遇',
                    image: 'https://images.unsplash.com/photo-1544005313-94ddf0286df2?w=1200',
                    text: `"你好，我叫陈默。"

我从暗处走出，举起双手表示没有敌意。

那女子吓了一跳，手中的武器差点掉在地上。

"你......你是谁？这里怎么会有人？"

"和你一样，只是一个幸存者。"

她自我介绍叫林雪，是一名考古学家。寒潮来临前，她正在研究这片区域的地质异常。

"这些晶体......它们不是普通的矿石。"她指着那些发光的蓝色晶体，"我怀疑这是某种远古文明留下的能量源。"`,
                    textType: 'narrator',
                    effect: {
                        allies: ['林雪'],
                        npc: '林雪'
                    },
                    choices: [
                        {
                            text: '和她一起探索遗迹',
                            type: 'good',
                            next: 'explore_together'
                        },
                        {
                            text: '分享情报后各自行动',
                            type: 'neutral',
                            next: 'share_and_leave'
                        }
                    ]
                },

                explore_together: {
                    id: 'explore_together',
                    location: '共同探索',
                    image: 'https://images.unsplash.com/photo-1578662996442-48f60103fc96?w=1200',
                    text: `林雪带领我深入遗迹。

她解释说，这些建筑风格不属于任何已知的古代文明。根据她的研究，这个遗迹可能来自数万年前的一个高度发达的文明。

"他们似乎掌握了某种空间技术，"林雪指着墙上的符文，"这些符号描述了一种跨维度的能量传输系统。"

我们来到遗迹的核心区域——一个巨大的圆形大厅。

大厅中央，悬浮着一块巨大的紫色晶体，散发着令人心悸的能量。

**这是......核心能量源！**`,
                    textType: 'narrator',
                    effect: {
                        discovery: '遗迹核心',
                        exp: 15
                    },
                    choices: [
                        {
                            text: '尝试接触核心晶体',
                            type: 'risk',
                            next: 'touch_core'
                        },
                        {
                            text: '谨慎研究，不贸然触碰',
                            type: 'cautious',
                            next: 'study_core'
                        }
                    ]
                },

                study_core: {
                    id: 'study_core',
                    location: '研究核心',
                    image: 'https://images.unsplash.com/photo-1531306728370-e2ebd9d7bb99?w=1200',
                    text: `我决定谨慎研究，不贸然触碰。

通过林雪的专业知识和我的空间感知能力，我们发现了一些惊人的事实：

1. 这块晶体是远古文明的能量核心
2. 寒潮的爆发可能与这个核心的能量波动有关
3. 触碰它可能会带来巨大的力量，但也可能引发灾难

也许，我们需要找到更多的线索......`,
                    textType: 'narrator',
                    effect: {
                        exp: 10,
                        knowledge: '核心秘密'
                    },
                    choices: [
                        {
                            text: '决定触碰核心',
                            type: 'action',
                            next: 'touch_core'
                        },
                        {
                            text: '返回地面寻找更多线索',
                            type: 'action',
                            next: 'return_to_surface'
                        }
                    ]
                },

                touch_core: {
                    id: 'touch_core',
                    location: '接触核心',
                    image: 'https://images.unsplash.com/photo-1531306728370-e2ebd9d7bb99?w=1200',
                    text: `我伸出手，触碰那块悬浮的紫色晶体。

一股巨大的能量瞬间涌入我的身体！

**啊啊啊！！！**

剧痛席卷全身，仿佛身体要被撕裂开来！

但同时，我感觉到一种前所未有的力量在觉醒......

空间在我眼中变得透明，无数维度的信息涌入脑海......

**异能突破！空间异能达到Lv.5！**

**终极技能解锁：空间位面（创造独立空间）！**`,
                    textType: 'narrator',
                    effect: {
                        ability: '空间异能',
                        abilityLevel: 5,
                        skill: '空间位面',
                        exp: 20
                    },
                    choices: [
                        {
                            text: '用新能力创造安全位面',
                            type: 'action',
                            next: 'create_new_world'
                        },
                        {
                            text: '先稳定力量，返回地面',
                            type: 'safe',
                            next: 'return_to_surface'
                        }
                    ]
                },

                create_new_world: {
                    id: 'create_new_world',
                    location: '创造新世界',
                    image: 'https://images.unsplash.com/photo-1506905925346-21bda4d32df4?w=1200',
                    text: `我集中所有力量，发动终极技能——

**"空间位面！"**

空间开始扭曲，一个全新的维度在我面前展开！

这是一个温暖、安全的空间，没有寒冷，没有冰雪，只有鸟语花香。

林雪目瞪口呆地看着这一切。

"这......这是神迹！"

我知道，这就是人类的未来。

在这个冰封的世界之外，我创造了一个新的家园。`,
                    textType: 'narrator',
                    effect: {
                        ability: '空间位面',
                        abilityLevel: 5,
                        flag: 'created_world'
                    },
                    choices: [
                        {
                            text: '带领所有幸存者进入新世界',
                            type: 'good',
                            next: 'lead_survivors'
                        },
                        {
                            text: '先返回地面组织迁移',
                            type: 'action',
                            next: 'organize_migration'
                        }
                    ]
                },

                lead_survivors: {
                    id: 'lead_survivors',
                    location: '带领幸存者',
                    image: 'https://images.unsplash.com/photo-1544005313-94ddf0286df2?w=1200',
                    text: `我回到地面，开始寻找所有幸存者。

老张的营地、李叔一家、林雪......还有更多在冰原上挣扎的人们。

我打开空间通道，让他们一个个进入那个温暖的新世界。

"这是真的吗？"有人不敢相信。

"我们有新家了。"我微笑着说。

越来越多的人穿过通道，脸上充满了希望的光芒。

**人类的火种，终于找到了新的家园。**`,
                    textType: 'narrator',
                    effect: {
                        flag: 'saved_humanity',
                        karma: 100
                    },
                    choices: [
                        {
                            text: '继续寻找更多幸存者',
                            type: 'action',
                            next: 'rescue_more'
                        },
                        {
                            text: '开始建设新世界',
                            type: 'action',
                            next: 'build_new_world'
                        }
                    ]
                },

                build_new_world: {
                    id: 'build_new_world',
                    location: '建设新世界',
                    image: 'https://images.unsplash.com/photo-1507525428034-b723cf961d3e?w=1200',
                    text: `在新的位面中，我们开始重建文明。

用空间异能，我们创造了肥沃的土地、清澈的水源、温暖的阳光。

幸存者们各司其职，建造房屋、种植作物、照顾孩子。

林雪成为了新世界的首席科学家，研究如何让这个位面更加稳定。

而我，成为了守护者，不断拓展这个空间的边界。

**十年后......**

曾经的冰封末日已经成为传说。

新的城市拔地而起，孩子们在草地上奔跑嬉戏。

人类文明，在这个新的世界中，重新绽放。`,
                    textType: 'narrator',
                    effect: {
                        ending: 'utopia',
                        victory: true
                    },
                    choices: []
                },

                rescue_more: {
                    id: 'rescue_more',
                    location: '全球救援',
                    image: 'https://images.unsplash.com/photo-1523961131990-5ea7c61b2107?w=1200',
                    text: `我继续在冰封世界中寻找幸存者。

从亚洲到欧洲，从美洲到非洲......

每找到一个幸存者，就多一份希望。

各国的幸存者们汇聚在一起，放下了曾经的隔阂。

在末日面前，人类终于团结起来。

**五年后，全球幸存者都被转移到了新世界。**

冰封的地球逐渐恢复生机，而人类，在新的家园中开始了全新的篇章。`,
                    textType: 'narrator',
                    effect: {
                        ending: 'unification',
                        victory: true
                    },
                    choices: []
                },

                deep_explore_temple: {
                    id: 'deep_explore_temple',
                    location: '遗迹深处',
                    image: 'https://images.unsplash.com/photo-1441974231531-c6227db76b6e?w=1200',
                    text: `我独自深入遗迹深处。

这里的建筑更加宏伟，符文也更加复杂。

墙壁上的壁画讲述了一个古老的故事：

数万年前，这个文明发现了一种能够操控空间的能量源。

但他们的实验失控了，引发了一场全球性的灾难——

**正是这场灾难，导致了冰河时代的降临！**

而现在，寒潮的再次降临......难道是历史的重演？

我必须找到阻止这一切的方法。`,
                    textType: 'narrator',
                    effect: {
                        discovery: '远古真相',
                        exp: 10
                    },
                    choices: [
                        {
                            text: '寻找阻止灾难的方法',
                            type: 'action',
                            next: 'find_solution'
                        },
                        {
                            text: '收集信息后返回地面',
                            type: 'safe',
                            next: 'return_to_surface_with_info'
                        }
                    ]
                },

                find_solution: {
                    id: 'find_solution',
                    location: '寻找解决方案',
                    image: 'https://images.unsplash.com/photo-1526379095098-d400fd0bf935?w=1200',
                    text: `根据壁画的指引，我找到了遗迹的控制中心。

这里有一个巨大的控制台，上面布满了闪烁的符文。

我集中精神，尝试与控制台建立连接......

**连接成功！**

符文开始响应我的异能。

我看到了一个选项：

【释放能量】- 可能会加剧寒潮
【封印能量】- 可能会平息寒潮，但需要巨大的能量

**抉择时刻——如何处理这份远古力量？**`,
                    textType: 'narrator',
                    effect: {
                        flag: 'found_control'
                    },
                    choices: [
                        {
                            text: '封印能量，平息寒潮',
                            type: 'good',
                            next: 'seal_energy',
                            good: true
                        },
                        {
                            text: '释放能量，获得更强力量',
                            type: 'risk',
                            next: 'release_energy',
                            evil: true
                        },
                        {
                            text: '先离开，寻求其他幸存者的意见',
                            type: 'neutral',
                            next: 'consult_others'
                        }
                    ]
                },

                seal_energy: {
                    id: 'seal_energy',
                    location: '封印能量',
                    image: 'https://images.unsplash.com/photo-1433086966358-54859d0ed716?w=1200',
                    text: `我选择了封印。

"为了人类的未来......"

能量开始倒流，我能感觉到巨大的力量在体内涌动。

**封印开始！**

地面开始震动，天空中的风雪渐渐平息。

远处的冰裂缝开始愈合，气温缓缓回升......

**寒潮正在消退！**

但这个过程消耗了我大量的异能。

我倒在地上，看着逐渐放晴的天空，露出了微笑。`,
                    textType: 'narrator',
                    effect: {
                        abilityLevel: -1,
                        flag: 'saved_world',
                        karma: 50
                    },
                    choices: [
                        {
                            text: '迎接新世界的到来',
                            type: 'action',
                            next: 'new_world'
                        }
                    ]
                },

                release_energy: {
                    id: 'release_energy',
                    location: '释放能量',
                    image: 'https://images.unsplash.com/photo-1506905925346-21bda4d32df4?w=1200',
                    text: `我选择了释放。

能量如洪流般涌入我的身体！

**啊啊啊！！！**

剧痛过后，是前所未有的力量感！

**空间异能突破至Lv.6！**

我感觉自己可以操控整个世界的空间！

但当我睁开眼睛，却看到了可怕的景象——

能量失控，寒潮更加猛烈，大地开始崩裂......

**我做错了吗？**`,
                    textType: 'narrator',
                    effect: {
                        ability: '空间异能',
                        abilityLevel: 6,
                        flag: 'energy_unleashed',
                        karma: -50
                    },
                    choices: [
                        {
                            text: '尝试控制失控的能量',
                            type: 'action',
                            next: 'control_chaos'
                        },
                        {
                            text: '逃离这个即将毁灭的世界',
                            type: 'escape',
                            next: 'escape_destruction'
                        }
                    ]
                },

                control_chaos: {
                    id: 'control_chaos',
                    location: '掌控混沌',
                    image: 'https://images.unsplash.com/photo-1534796636912-3b95b3ab5986?w=1200',
                    text: `我集中全部精神，试图控制这失控的能量。

空间在我手中扭曲、重组......

我将失控的能量引导回遗迹深处，重新封印。

虽然过程艰难，但最终成功了。

寒潮渐渐平息，世界恢复了平静。

但这次经历让我明白——

**力量越大，责任越大。**`,
                    textType: 'narrator',
                    effect: {
                        flag: 'controlled_chaos',
                        karma: 20,
                        exp: 30
                    },
                    choices: [
                        {
                            text: '继续守护这个世界',
                            type: 'action',
                            next: 'guardian_ending'
                        }
                    ]
                },

                escape_destruction: {
                    id: 'escape_destruction',
                    location: '逃离毁灭',
                    image: 'https://images.unsplash.com/photo-1517311307972-6aa2d3f79f89?w=1200',
                    text: `我发动空间闪烁，逃离了即将毁灭的区域。

身后是崩塌的大地和肆虐的暴风雪。

我独自站在一片废墟之上，感受着刺骨的寒风。

也许，我可以创造一个新的位面独自生活......

但那样的话，人类的未来又在哪里？

**一个人的永生，又有什么意义？**`,
                    textType: 'narrator',
                    effect: {
                        ending: 'lonely_immortal',
                        karma: -100
                    },
                    choices: []
                },

                return_to_surface: {
                    id: 'return_to_surface',
                    location: '返回地面',
                    image: 'https://images.unsplash.com/photo-1507003211169-0a1dd7228f2d?w=1200',
                    text: `我带着林雪返回地面。

外面的世界依然寒冷，但我已经掌握了更强大的力量。

"我们需要组织幸存者，"林雪说，"这个遗迹的秘密可能是拯救人类的关键。"

我点点头。

是时候，建立一个真正的势力，带领人类走出寒冬了。`,
                    textType: 'narrator',
                    effect: {
                        flag: 'returned_with_knowledge'
                    },
                    choices: [
                        {
                            text: '建立统一的幸存者联盟',
                            type: 'action',
                            next: 'build_alliance'
                        },
                        {
                            text: '先寻找其他幸存者据点',
                            type: 'explore',
                            next: 'find_more_camps'
                        }
                    ]
                },

                build_alliance: {
                    id: 'build_alliance',
                    location: '建立联盟',
                    image: 'https://images.unsplash.com/photo-1527192491265-7e15c55b1ed2?w=1200',
                    text: `我开始联络各个幸存者营地。

凭借强大的空间异能和丰富的物资，我很快成为了幸存者中的核心人物。

老张的营地、其他城市的幸存者团体、甚至一些拥有异能的强者......

**人类幸存者联盟正式成立！**

我被推举为盟主，林雪成为首席科学家。

我们开始系统性地收集物资、研究异能、探索遗迹。

**人类，不再是一盘散沙。**`,
                    textType: 'narrator',
                    effect: {
                        flag: 'alliance_formed',
                        karma: 30
                    },
                    choices: [
                        {
                            text: '与其他国家的幸存者建立联系',
                            type: 'action',
                            next: 'diplomacy'
                        },
                        {
                            text: '专注于国内的重建工作',
                            type: 'action',
                            next: 'reconstruction'
                        }
                    ]
                },

                diplomacy: {
                    id: 'diplomacy',
                    location: '外交建交',
                    image: 'https://images.unsplash.com/photo-1507721999472-8ed4421c4af2?w=1200',
                    text: `通过空间传送，我开始与世界各地的幸存者建立联系。

北美、欧洲、亚洲各地都有幸存者营地。

我们交换物资、分享技术、共同研究对抗寒潮的方法。

**全球幸存者联盟正式成立！**

各国代表齐聚一堂，签署了《冰封世界和平协议》。

在末日面前，人类终于放下了隔阂，携手共进。`,
                    textType: 'narrator',
                    effect: {
                        flag: 'global_alliance',
                        karma: 50
                    },
                    choices: [
                        {
                            text: '共同探索远古遗迹',
                            type: 'action',
                            next: 'global_expedition'
                        },
                        {
                            text: '开始全球性的重建计划',
                            type: 'action',
                            next: 'global_reconstruction'
                        }
                    ]
                },

                global_expedition: {
                    id: 'global_expedition',
                    location: '全球探险',
                    image: 'https://images.unsplash.com/photo-1470071459604-3b5ec3a7fe05?w=1200',
                    text: `全球联盟组织了一支探险队，深入世界各地的远古遗迹。

我们发现了更多关于这个远古文明的秘密：

- 他们曾经创造过多个空间位面
- 寒潮是他们实验失败的产物
- 他们留下了重启世界的方法

在南极的冰盖下，我们找到了最终的答案——

**一个可以逆转寒潮的装置！**

但启动它需要付出巨大的代价......`,
                    textType: 'narrator',
                    effect: {
                        discovery: '终极秘密',
                        exp: 25
                    },
                    choices: [
                        {
                            text: '牺牲自己启动装置',
                            type: 'good',
                            next: 'sacrifice',
                            good: true
                        },
                        {
                            text: '寻找其他解决方案',
                            type: 'neutral',
                            next: 'find_alternative'
                        }
                    ]
                },

                sacrifice: {
                    id: 'sacrifice',
                    location: '牺牲',
                    image: 'https://images.unsplash.com/photo-1493606374473-91118926746a?w=1200',
                    text: `"我来启动它。"

我走向装置，将手放在控制面板上。

能量开始涌入我的身体，又从我的身体流向装置。

我能感觉到生命正在流逝，但我没有退缩。

"再见了，朋友们......"

装置发出耀眼的光芒，寒潮开始消退，冰雪开始融化......

**十年后**

孩子们在草地上奔跑，阳光温暖而明媚。

没有人记得那个拯救世界的英雄，但他的牺牲永远铭刻在人类的历史中。`,
                    textType: 'narrator',
                    effect: {
                        ending: 'heroic_sacrifice',
                        victory: true,
                        karma: 200
                    },
                    choices: []
                },

                reconstruction: {
                    id: 'reconstruction',
                    location: '重建家园',
                    image: 'https://images.unsplash.com/photo-1506905925346-21bda4d32df4?w=1200',
                    text: `我们开始了大规模的重建工作。

利用空间异能，我们快速清理废墟、建造房屋、恢复基础设施。

异能者们各展所长：
- 火元素异能者提供温暖和能源
- 土元素异能者平整土地
- 空间异能者运输物资

**五年后**

一座座新城市拔地而起，人类文明重新焕发生机。

虽然寒潮仍未完全消退，但我们已经学会了与自然共存。

**【和平重建结局】**`,
                    textType: 'narrator',
                    effect: {
                        ending: 'reconstruction',
                        victory: true
                    },
                    choices: []
                },

                global_reconstruction: {
                    id: 'global_reconstruction',
                    location: '全球重建',
                    image: 'https://images.unsplash.com/photo-1447752875215-b2761acb3c5d?w=1200',
                    text: `全球联盟开始了宏伟的重建计划。

各国幸存者携手合作，共享资源和技术。

空间传送网络连接了各大洲，物资和人员可以快速流动。

异能者们成为了新世界的中坚力量，用他们的能力帮助重建。

**二十年过去了......**

冰封的世界已经恢复了大部分生机。

城市重新繁荣，科技飞速发展，人类文明达到了前所未有的高度。

**【全球统一结局】**`,
                    textType: 'narrator',
                    effect: {
                        ending: 'global_unification',
                        victory: true
                    },
                    choices: []
                },

                guardian_ending: {
                    id: 'guardian_ending',
                    location: '守护者结局',
                    image: 'https://images.unsplash.com/photo-1485846234645-a62644f84728?w=1200',
                    text: `我成为了这个世界的守护者。

独自居住在遗迹附近，守护着这份远古的力量。

时不时地，我会帮助那些遇到困难的幸存者。

人们称我为"冰原守望者"，一个传说中的存在。

也许有一天，当人类足够成熟，我会再次出现，分享这些秘密......

但现在，我选择等待。

**【守护者结局】**`,
                    textType: 'narrator',
                    effect: {
                        ending: 'guardian'
                    },
                    choices: []
                },

                // 结局剧情
                good_ending: {
                    id: 'good_ending',
                    location: '希望的曙光',
                    image: 'https://images.unsplash.com/photo-1470252649378-9c29740c9fa8?w=1200',
                    text: `**结局：希望的曙光**

多年后......

冰封世界逐渐开始转暖，人类文明在废墟中重生。

我建立的幸存者联盟成为了新世界的核心力量。

那些曾经和我并肩作战的伙伴们，都有了自己的生活。

而我，站在新的城市高处，看着这片焕发生机的土地。

末日的寒冬终于过去。

**希望的曙光，终于降临。**

**【好结局】**`,
                    textType: 'narrator',
                    effect: {
                        ending: 'good',
                        victory: true
                    },
                    choices: []
                },

                normal_ending: {
                    id: 'normal_ending',
                    location: '独自前行',
                    image: 'https://images.unsplash.com/photo-1519681393784-d120267933ba?w=1200',
                    text: `**结局：独自前行**

多年后......

冰封世界依然寒冷，但人类找到了共存的方式。

我依然独自一人，在这片荒原上漂泊。

有时候会想念那些曾经的伙伴，但我不后悔自己的选择。

每个人都有自己的路要走。

在这片永冻的土地上，我将继续寻找答案。

**也许有一天，我能找到回家的路。**

**【普通结局】**`,
                    textType: 'narrator',
                    effect: {
                        ending: 'normal'
                    },
                    choices: []
                },

                bad_ending: {
                    id: 'bad_ending',
                    location: '冰封永眠',
                    image: 'https://images.unsplash.com/photo-1483664852095-d6cc6870702d?w=1200',
                    text: `**结局：冰封永眠**

那个冬天特别寒冷。

一个人的力量，终究是有限的。

当最后一块燃料用尽，当最后一瓶水结成冰......

我闭上眼睛，感受着周围的寂静。

也许，这就是最好的结局吧。

至少，我曾经挣扎过。

在这个冰封的世界里，没有人是赢家。

**【坏结局】**`,
                    textType: 'narrator',
                    effect: {
                        ending: 'bad',
                        gameOver: true
                    },
                    choices: []
                }
            },

            abilityTypes: {
                '空间系': ['空间储物', '空间感知', '空间切割', '空间闪烁', '空间门'],
                '元素类': ['火焰操控', '冰霜掌控', '雷电召唤', '大地之力'],
                '控制类': ['心灵感应', '精神控制', '预知未来', '时间减缓'],
                '身体强化类': ['超级力量', '极速再生', '钢铁皮肤', '完美视力']
            }
        };

        // ==================== 游戏状态 ====================
        let gameState = {
            currentScene: 'prologue',
            player: {
                name: '陈默',
                health: 100,
                temperature: 36.5,
                power: 100,
                level: 1,
                exp: 0,
                day: 0,
                karma: 0
            },
            abilities: [],
            supplies: {},
            money: 100000,
            allies: [],
            discovered: [],
            choices: [],
            flags: {}
        };

        // ==================== 界面更新函数 ====================
        function updateStatusBar() {
            document.getElementById('statusBar').classList.add('visible');
            document.getElementById('powerValue').textContent = gameState.player.power;
            document.getElementById('dayValue').textContent = gameState.player.day;
            document.getElementById('healthValue').textContent = gameState.player.health;
            document.getElementById('tempValue').textContent = gameState.player.temperature + '°C';

            const supplyCount = Object.values(gameState.supplies).reduce((a, b) => a + b, 0);
            document.getElementById('supplyValue').textContent = supplyCount;
        }

        function updateSuppliesPanel() {
            const content = document.getElementById('suppliesContent');
            let html = '';

            const categories = {
                '食物': ['方便面', '罐头', '纯净水', '压缩饼干', '大米', '干菜', '巧克力'],
                '药品': ['退烧药', '消炎药', '冻伤膏', '医疗包'],
                '装备': ['防寒服', '保暖手套', '登山靴', '羽绒睡袋'],
                '燃料': ['酒精块', '煤炭', '木炭', '固体燃料']
            };

            for (let category in categories) {
                let items = categories[category].filter(item => gameState.supplies[item] > 0);
                if (items.length > 0) {
                    html += `<div style="margin-top:8px;font-weight:bold;color:#7ac0ff">${category}</div>`;
                    items.forEach(item => {
                        html += `<div class="supply-item"><span>${item}</span><span>x${gameState.supplies[item]}</span></div>`;
                    });
                }
            }

            if (!html) {
                html = '<div style="color:#8ab4d8">暂无物资</div>';
            }

            content.innerHTML = html;
        }

        function updateAbilitiesPanel() {
            const content = document.getElementById('abilitiesContent');
            let html = '';

            if (gameState.abilities.length === 0) {
                html = '<div style="color:#8ab4d8">尚未觉醒异能</div>';
            } else {
                gameState.abilities.forEach(ability => {
                    const level = ability.level || 1;
                    html += `<span class="ability-tag">${ability.name} Lv.${level}</span>`;
                });
            }

            content.innerHTML = html;
        }

        function showScene(scene) {
            const sceneData = GameData.scenes[scene];
            if (!sceneData) {
                console.error('Scene not found:', scene);
                return;
            }

            gameState.currentScene = scene;

            // 更新进度指示
            document.getElementById('currentScene').textContent = sceneData.location;
            document.getElementById('progressIndicator').classList.remove('hidden');

            // 更新场景图片
            const imageContainer = document.getElementById('sceneImageContainer');
            const sceneImage = document.getElementById('sceneImage');
            const sceneLocation = document.getElementById('sceneLocation');

            imageContainer.classList.remove('hidden');
            sceneImage.classList.add('loading');
            sceneImage.src = sceneData.image;
            sceneImage.onload = () => sceneImage.classList.remove('loading');
            sceneLocation.textContent = sceneData.location;

            // 更新剧情文本
            const storyPanel = document.getElementById('storyPanel');
            const storyText = document.getElementById('storyText');

            storyPanel.classList.remove('hidden');
            storyText.innerHTML = sceneData.text.replace(/\n/g, '<br>').replace(/\*\*(.*?)\*\*/g, '<strong>$1</strong>');
            storyText.className = 'story-text ' + (sceneData.textType || 'narrator');

            // 应用效果
            if (sceneData.effect) {
                applyEffect(sceneData.effect);
            }

            // 更新状态面板
            updateStatusBar();
            updateSuppliesPanel();
            updateAbilitiesPanel();

            // 显示信息面板
            document.getElementById('infoPanels').classList.remove('hidden');

            // 显示选项
            const choicesContainer = document.getElementById('choicesContainer');
            choicesContainer.classList.remove('hidden');
            choicesContainer.innerHTML = '';

            if (sceneData.choices && sceneData.choices.length > 0) {
                sceneData.choices.forEach(choice => {
                    const btn = document.createElement('button');
                    btn.className = 'choice-btn ' + (choice.good ? 'good' : '') + (choice.evil ? 'evil' : '');

                    let choiceHtml = `<div class="choice-main">${choice.text}</div>`;
                    if (choice.type) {
                        let typeText = '';
                        if (choice.good) typeText = '【善良选择】';
                        else if (choice.evil) typeText = '【邪恶选择】';
                        else if (choice.type === 'explore') typeText = '【探索】';
                        else if (choice.type === 'action') typeText = '【行动】';
                        else if (choice.type === 'safe') typeText = '【安全】';
                        else if (choice.type === 'risk') typeText = '【冒险】';
                        else if (choice.type === 'rest') typeText = '【休息】';

                        choiceHtml += `<span class="choice-type">${typeText}</span>`;
                    }

                    btn.innerHTML = choiceHtml;
                    btn.onclick = () => {
                        recordChoice(choice);
                        showScene(choice.next);
                    };

                    choicesContainer.appendChild(btn);
                });
            } else {
                // 结束场景
                showEnding(sceneData);
            }

            // 滚动到顶部
            window.scrollTo(0, 0);
        }

        function applyEffect(effect) {
            if (effect.health) {
                gameState.player.health = Math.max(0, Math.min(100, gameState.player.health + effect.health));
            }

            if (effect.power) {
                gameState.player.power += effect.power;
            }

            if (effect.day) {
                gameState.player.day = effect.day;
            }

            if (effect.temperature) {
                gameState.player.temperature = effect.temperature;
            }

            if (effect.ability) {
                const existing = gameState.abilities.find(a => a.name === effect.ability);
                if (existing) {
                    existing.level = effect.abilityLevel;
                } else {
                    gameState.abilities.push({
                        name: effect.ability,
                        level: effect.abilityLevel || 1
                    });
                }
            }

            if (effect.skill) {
                if (!gameState.abilities.find(a => a.name === effect.skill)) {
                    gameState.abilities.push({
                        name: effect.skill,
                        level: 1
                    });
                }
            }

            if (effect.supplies) {
                for (let item in effect.supplies) {
                    if (!gameState.supplies[item]) {
                        gameState.supplies[item] = 0;
                    }
                    gameState.supplies[item] += effect.supplies[item];
                }
            }

            if (effect.money) {
                gameState.money += effect.money;
            }

            if (effect.allies) {
                effect.allies.forEach(ally => {
                    if (!gameState.allies.includes(ally)) {
                        gameState.allies.push(ally);
                    }
                });
            }

            if (effect.karma) {
                gameState.player.karma += effect.karma;
            }

            if (effect.exp) {
                gameState.player.exp += effect.exp;
                checkLevelUp();
            }

            if (effect.location) {
                gameState.flags.currentLocation = effect.location;
            }

            if (effect.plan) {
                gameState.flags.hasPlan = true;
            }

            if (effect.knowledge) {
                gameState.flags.hasFutureKnowledge = true;
            }

            if (effect.base) {
                gameState.flags.hasBase = true;
            }

            if (effect.danger) {
                gameState.flags.encounteredDanger = true;
            }

            if (effect.escape) {
                gameState.flags.escapedFromBeast = true;
            }

            if (effect.camp) {
                gameState.flags.foundCamp = true;
            }

            if (effect.npc) {
                gameState.flags.lastNPC = effect.npc;
            }

            if (effect.weapon) {
                gameState.flags.hasWeapon = true;
            }

            if (effect.solo) {
                gameState.flags.stayedSolo = true;
            }

            if (effect.chapter) {
                gameState.flags.currentChapter = effect.chapter;
            }

            if (effect.complete) {
                gameState.flags.chapterComplete = true;
            }

            if (effect.discovery) {
                gameState.discovered.push(effect.discovery);
            }

            if (effect.rare) {
                if (!gameState.supplies[effect.rare]) {
                    gameState.supplies[effect.rare] = 0;
                }
                gameState.supplies[effect.rare] += 5;
            }

            if (effect.ending) {
                gameState.flags.ending = effect.ending;
            }

            if (effect.victory) {
                gameState.flags.victory = true;
            }

            if (effect.gameOver) {
                setTimeout(() => {
                    showGameOver();
                }, 100);
            }
        }

        function recordChoice(choice) {
            gameState.choices.push({
                scene: gameState.currentScene,
                choice: choice.text,
                type: choice.type,
                good: choice.good,
                evil: choice.evil
            });
        }

        function checkLevelUp() {
            const expNeeded = gameState.player.level * 10;
            if (gameState.player.exp >= expNeeded) {
                gameState.player.level++;
                gameState.player.exp -= expNeeded;
                gameState.player.power += 10;
            }
        }

        function showEnding(sceneData) {
            document.getElementById('choicesContainer').classList.add('hidden');
        }

        function showGameOver() {
            const endType = gameState.flags.ending || (gameState.player.karma >= 0 ? 'normal' : 'bad');

            document.getElementById('sceneImageContainer').classList.add('hidden');
            document.getElementById('storyPanel').classList.add('hidden');
            document.getElementById('infoPanels').classList.add('hidden');
            document.getElementById('choicesContainer').classList.add('hidden');
            document.getElementById('progressIndicator').classList.add('hidden');

            const gameOver = document.getElementById('gameOver');
            gameOver.classList.remove('hidden', 'victory');

            const title = document.getElementById('gameOverTitle');
            const text = document.getElementById('gameOverText');

            const abilityText = gameState.abilities.length > 0 ? 
                gameState.abilities.map(a => a.name + ' Lv.' + a.level).join('、') : '无';

            const endingConfigs = {
                utopia: {
                    victory: true,
                    title: '恭喜通关！',
                    name: '【完美结局】新世界的创造者',
                    description: '你创造了一个全新的空间位面，带领人类在新世界中重建文明。你的名字将被永远铭记。',
                    highlight: '创造了独立空间，延续人类文明'
                },
                unification: {
                    victory: true,
                    title: '恭喜通关！',
                    name: '【全球统一结局】人类的守护者',
                    description: '你统一了全球幸存者，建立了全球联盟，带领人类走向新的未来。',
                    highlight: '统一全球势力，建立新世界秩序'
                },
                heroic_sacrifice: {
                    victory: true,
                    title: '恭喜通关！',
                    name: '【英雄结局】牺牲与重生',
                    description: '你牺牲自己启动了逆转寒潮的装置，用生命换来了人类的未来。',
                    highlight: '牺牲自我，拯救世界'
                },
                global_unification: {
                    victory: true,
                    title: '恭喜通关！',
                    name: '【全球重建结局】新纪元的开启',
                    description: '通过外交建交，你建立了全球幸存者联盟，开启了人类文明的新纪元。',
                    highlight: '与各国建交，开启全球合作'
                },
                reconstruction: {
                    victory: true,
                    title: '恭喜通关！',
                    name: '【和平重建结局】家园的重建',
                    description: '你带领幸存者重建家园，在冰封世界中开辟了新的生存空间。',
                    highlight: '统一全国乱局，重建人类家园'
                },
                guardian: {
                    victory: false,
                    title: '游戏结束',
                    name: '【守护者结局】冰原守望者',
                    description: '你选择守护远古遗迹的秘密，成为传说中的冰原守望者。',
                    highlight: '守护远古力量，等待时机'
                },
                lonely_immortal: {
                    victory: false,
                    title: '游戏结束',
                    name: '【孤独结局】永生的代价',
                    description: '你获得了强大的力量，却失去了一切。永生的孤独，是最大的惩罚。',
                    highlight: '力量至上，孤独永生'
                },
                good: {
                    victory: true,
                    title: '恭喜通关！',
                    name: '【好结局】希望的曙光',
                    description: '你的善良和智慧拯救了无数生命，成为了新世界的领导者。',
                    highlight: '善良选择，点燃希望'
                },
                normal: {
                    victory: false,
                    title: '游戏结束',
                    name: '【普通结局】独自前行',
                    description: '你在末日中艰难求生，最终找到了自己的道路。',
                    highlight: '独善其身，独自前行'
                },
                bad: {
                    victory: false,
                    title: '游戏结束',
                    name: '【坏结局】冰封永眠',
                    description: '在末日面前，个人的力量终究有限。你的选择让你渐渐失去了生存的机会......',
                    highlight: '失去希望，冰封永眠'
                }
            };

            const config = endingConfigs[endType] || endingConfigs.normal;

            if (config.victory) {
                gameOver.classList.add('victory');
            }

            title.textContent = config.title;
            text.innerHTML = `
                <p style="font-size:1.2em;margin-bottom:15px">${config.name}</p>
                <p>${config.description}</p>
                <p style="margin-top:15px;color:#00ccff">成就：<strong>${config.highlight}</strong></p>
                <p style="margin-top:10px">生存天数：<strong>${gameState.player.day}</strong>天</p>
                <p>最终异能：<strong>${abilityText}</strong></p>
                <p>道德值：<strong>${gameState.player.karma}</strong></p>
                <p style="margin-top:15px;font-size:0.9em;color:#8ab4d8">
                    盟友：${gameState.allies.length > 0 ? gameState.allies.join('、') : '无'}
                </p>
            `;
        }

        // ==================== 游戏控制函数 ====================
        function startGame() {
            document.getElementById('startScreen').classList.add('hidden');
            gameState = {
                currentScene: 'prologue',
                player: {
                    name: '陈默',
                    health: 100,
                    temperature: 36.5,
                    power: 100,
                    level: 1,
                    exp: 0,
                    day: 0,
                    karma: 0
                },
                abilities: [],
                supplies: {},
                money: 100000,
                allies: [],
                discovered: [],
                choices: [],
                flags: {}
            };
            showScene('prologue');
        }

        function restartGame() {
            document.getElementById('gameOver').classList.add('hidden');
            document.getElementById('statusBar').classList.remove('visible');
            startGame();
        }

        // ==================== 雪花效果 ====================
        function createSnowflakes() {
            const container = document.getElementById('snowflakes');
            const snowflakeChars = ['❄', '❅', '❆', '✧', '✦'];

            for (let i = 0; i < 30; i++) {
                const snowflake = document.createElement('div');
                snowflake.className = 'snowflake';
                snowflake.innerHTML = snowflakeChars[Math.floor(Math.random() * snowflakeChars.length)];
                snowflake.style.left = Math.random() * 100 + '%';
                snowflake.style.fontSize = (Math.random() * 10 + 8) + 'px';
                snowflake.style.opacity = Math.random() * 0.6 + 0.2;
                snowflake.style.animationDuration = (Math.random() * 10 + 10) + 's';
                snowflake.style.animationDelay = Math.random() * 10 + 's';
                container.appendChild(snowflake);
            }
        }

        // ==================== 初始化 ====================
        window.onload = function() {
            createSnowflakes();
        };
    </script>
</body>
</html>
