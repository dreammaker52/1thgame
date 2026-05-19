<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
    <meta name="theme-color" content="#0a0a1a">
    <title>赛博防线 - Cyber Defense</title>
    <style>
        * { margin: 0; padding: 0; box-sizing: border-box; }
        html, body { height: 100%; overflow: hidden; font-family: 'Segoe UI', Arial, sans-serif; }
        body {
            background: linear-gradient(135deg, #0a0a1a 0%, #1a0a2e 50%, #0a1a2a 100%);
            color: #00ffff;
            touch-action: manipulation;
            -webkit-font-smoothing: antialiased;
        }

        #game-container {
            width: 100vw;
            height: 100vh;
            display: flex;
            flex-direction: column;
            overflow: hidden;
            position: relative;
        }

        .header {
            background: linear-gradient(90deg, rgba(138, 43, 226, 0.3), rgba(0, 255, 255, 0.2));
            padding: 8px 12px;
            display: flex;
            justify-content: space-between;
            align-items: center;
            border-bottom: 2px solid rgba(0, 255, 255, 0.5);
            box-shadow: 0 0 20px rgba(0, 255, 255, 0.3);
            position: relative;
            z-index: 100;
        }

        .header h1 {
            font-size: 18px;
            text-shadow: 0 0 10px #00ffff, 0 0 20px #ff00ff;
            letter-spacing: 2px;
        }

        .header .stats {
            display: flex;
            gap: 15px;
            align-items: center;
        }

        .stat {
            display: flex;
            flex-direction: column;
            align-items: center;
            gap: 2px;
        }

        .stat-value {
            font-weight: bold;
            color: #ffd700;
            text-shadow: 0 0 5px #ffd700;
            font-size: 16px;
        }

        .stat-label {
            color: #888;
            font-size: 10px;
        }

        .health-bar {
            width: 80px;
            height: 12px;
            background: rgba(255, 0, 102, 0.3);
            border: 1px solid #ff0066;
            border-radius: 6px;
            overflow: hidden;
        }

        .health-fill {
            height: 100%;
            background: linear-gradient(90deg, #ff0066, #ff6600);
            transition: width 0.3s;
            box-shadow: 0 0 10px #ff0066;
        }

        .main-content {
            flex: 1;
            display: flex;
            overflow: hidden;
            position: relative;
        }

        #game-canvas {
            flex: 1;
            display: block;
            cursor: crosshair;
            image-rendering: pixelated;
            image-rendering: crisp-edges;
            background: #0a0a1a;
            touch-action: none;
        }

        .game-ui-overlay {
            position: absolute;
            top: 0;
            left: 0;
            right: 0;
            padding: 8px;
            display: flex;
            justify-content: space-between;
            align-items: flex-start;
            pointer-events: none;
            z-index: 50;
        }

        .wave-info {
            background: rgba(0, 0, 0, 0.7);
            border: 1px solid rgba(0, 255, 255, 0.5);
            border-radius: 8px;
            padding: 8px 12px;
            pointer-events: auto;
            backdrop-filter: blur(5px);
        }

        .wave-number {
            font-size: 16px;
            font-weight: bold;
            color: #00ffff;
            text-shadow: 0 0 10px #00ffff;
        }

        .wave-status {
            font-size: 12px;
            color: #888;
        }

        .resupply-timer {
            font-size: 14px;
            color: #ffd700;
            font-weight: bold;
            text-shadow: 0 0 10px #ffd700;
        }

        .mobile-bottom-panel {
            position: absolute;
            bottom: 0;
            left: 0;
            right: 0;
            background: linear-gradient(180deg, rgba(0, 0, 0, 0.95), rgba(10, 10, 30, 0.98));
            border-top: 2px solid rgba(0, 255, 255, 0.5);
            box-shadow: 0 -5px 30px rgba(0, 255, 255, 0.2);
            padding: 12px;
            display: flex;
            flex-direction: column;
            gap: 10px;
            z-index: 80;
            transform: translateY(100%);
            transition: transform 0.3s ease;
        }

        .mobile-bottom-panel.active {
            transform: translateY(0);
        }

        .panel-tabs {
            display: flex;
            gap: 8px;
        }

        .tab-btn {
            flex: 1;
            padding: 10px 12px;
            background: rgba(0, 0, 0, 0.5);
            border: 1px solid rgba(0, 255, 255, 0.3);
            border-radius: 8px;
            color: #00ffff;
            font-size: 14px;
            cursor: pointer;
            transition: all 0.2s;
        }

        .tab-btn.active {
            background: rgba(0, 255, 255, 0.2);
            border-color: #00ffff;
            box-shadow: 0 0 15px rgba(0, 255, 255, 0.5);
        }

        .panel-content {
            display: none;
            max-height: 200px;
            overflow-y: auto;
        }

        .panel-content.active {
            display: block;
        }

        .tower-grid-mobile {
            display: grid;
            grid-template-columns: repeat(4, 1fr);
            gap: 10px;
        }

        .tower-card-mobile {
            background: rgba(0, 0, 0, 0.6);
            border: 2px solid rgba(0, 255, 255, 0.3);
            border-radius: 10px;
            padding: 12px 8px;
            text-align: center;
            cursor: pointer;
            transition: all 0.2s;
            display: flex;
            flex-direction: column;
            gap: 6px;
        }

        .tower-card-mobile:hover, .tower-card-mobile.selected {
            border-color: #00ffff;
            box-shadow: 0 0 15px rgba(0, 255, 255, 0.5);
            transform: scale(1.05);
        }

        .tower-card-mobile .icon {
            font-size: 24px;
        }

        .tower-card-mobile .name {
            font-size: 12px;
            font-weight: bold;
        }

        .tower-card-mobile .price {
            font-size: 13px;
            color: #ffd700;
            font-weight: bold;
        }

        .upgrade-panel-mobile {
            padding: 10px;
        }

        .selected-tower-header {
            display: flex;
            align-items: center;
            gap: 12px;
            margin-bottom: 12px;
        }

        .selected-tower-icon {
            width: 50px;
            height: 50px;
            border-radius: 50%;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 24px;
        }

        .selected-tower-info h4 {
            font-size: 16px;
            margin-bottom: 2px;
        }

        .selected-tower-info p {
            font-size: 12px;
            color: #888;
        }

        .upgrade-level {
            display: flex;
            gap: 5px;
            margin-bottom: 12px;
        }

        .level-pip {
            width: 12px;
            height: 12px;
            border-radius: 50%;
            background: rgba(255, 255, 255, 0.2);
            border: 1px solid rgba(0, 255, 255, 0.3);
        }

        .level-pip.filled {
            background: #00ffff;
            box-shadow: 0 0 10px #00ffff;
        }

        .upgrade-stats {
            font-size: 13px;
            line-height: 1.6;
            color: #aaa;
        }

        .upgrade-stats span {
            color: #00ffff;
        }

        .upgrade-preview {
            margin-top: 12px;
            padding-top: 12px;
            border-top: 1px solid rgba(0, 255, 255, 0.2);
        }

        .btn-mobile {
            padding: 12px;
            font-size: 14px;
            font-weight: bold;
            border-radius: 8px;
            border: 2px solid;
            cursor: pointer;
            transition: all 0.2s;
            text-transform: uppercase;
            letter-spacing: 1px;
        }

        .btn-upgrade {
            background: linear-gradient(135deg, #2a2a4a, #1a1a3a);
            border-color: #00ffff;
            color: #00ffff;
        }

        .btn-upgrade:hover:not(:disabled) {
            box-shadow: 0 0 20px rgba(0, 255, 255, 0.5);
            transform: scale(1.02);
        }

        .btn-upgrade:disabled {
            opacity: 0.5;
            cursor: not-allowed;
            border-color: #666;
            color: #666;
        }

        .btn-sell {
            background: linear-gradient(135deg, #4a2a2a, #3a1a1a);
            border-color: #ff4444;
            color: #ff4444;
            margin-top: 8px;
        }

        .btn-sell:hover {
            box-shadow: 0 0 20px rgba(255, 68, 68, 0.5);
        }

        .mobile-toggle-btn {
            position: absolute;
            bottom: calc(100% + 5px);
            left: 50%;
            transform: translateX(-50%);
            width: 40px;
            height: 20px;
            background: rgba(0, 255, 255, 0.3);
            border: 1px solid rgba(0, 255, 255, 0.5);
            border-radius: 10px 10px 0 0;
            cursor: pointer;
            display: flex;
            align-items: center;
            justify-content: center;
            transition: all 0.2s;
        }

        .mobile-toggle-btn::after {
            content: '▼';
            font-size: 10px;
            color: #00ffff;
            transition: transform 0.3s;
        }

        .mobile-bottom-panel.active .mobile-toggle-btn::after {
            transform: rotate(180deg);
        }

        .start-screen, .map-select-screen {
            position: fixed;
            top: 0;
            left: 0;
            right: 0;
            bottom: 0;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            background: linear-gradient(135deg, #0a0a1a 0%, #1a0a2e 50%, #0a1a2a 100%);
            z-index: 200;
            padding: 20px;
        }

        .start-screen.hidden, .map-select-screen.hidden {
            display: none;
        }

        .title {
            font-size: 32px;
            font-weight: bold;
            color: #00ffff;
            text-shadow: 0 0 20px #00ffff, 0 0 40px #ff00ff;
            margin-bottom: 10px;
            letter-spacing: 4px;
        }

        .subtitle {
            font-size: 14px;
            color: #888;
            margin-bottom: 30px;
        }

        .difficulty-container, .map-container {
            display: flex;
            flex-direction: column;
            gap: 15px;
            width: 100%;
            max-width: 320px;
        }

        .difficulty-card, .map-card {
            padding: 20px;
            border-radius: 15px;
            cursor: pointer;
            transition: all 0.3s ease;
            text-align: center;
            position: relative;
            border: 2px solid;
        }

        .difficulty-card {
            border-color: transparent;
        }

        .difficulty-card.easy {
            background: linear-gradient(135deg, rgba(0, 128, 0, 0.15), rgba(0, 255, 0, 0.08));
            border-color: #00ff00;
            box-shadow: 0 0 20px rgba(0, 255, 0, 0.3);
        }

        .difficulty-card.normal {
            background: linear-gradient(135deg, rgba(0, 191, 255, 0.15), rgba(138, 43, 226, 0.08));
            border-color: #00bfff;
            box-shadow: 0 0 20px rgba(0, 191, 255, 0.3);
        }

        .difficulty-card.hard {
            background: linear-gradient(135deg, rgba(255, 0, 102, 0.15), rgba(138, 43, 226, 0.08));
            border-color: #ff0066;
            box-shadow: 0 0 20px rgba(255, 0, 102, 0.3);
        }

        .difficulty-card:hover, .difficulty-card.selected {
            transform: translateY(-5px);
            box-shadow: 0 0 30px currentColor;
        }

        .difficulty-icon {
            font-size: 32px;
            margin-bottom: 10px;
        }

        .difficulty-name {
            font-size: 18px;
            font-weight: bold;
            margin-bottom: 8px;
        }

        .difficulty-card.easy .difficulty-name { color: #00ff00; }
        .difficulty-card.normal .difficulty-name { color: #00bfff; }
        .difficulty-card.hard .difficulty-name { color: #ff0066; }

        .difficulty-desc {
            font-size: 12px;
            color: #aaa;
            line-height: 1.5;
        }

        .back-btn {
            padding: 12px 30px;
            font-size: 14px;
            background: linear-gradient(135deg, #333, #555);
            color: #00ffff;
            border: 2px solid rgba(0,255,255,0.5);
            border-radius: 25px;
            cursor: pointer;
            transition: all 0.3s;
            margin-bottom: 20px;
            text-transform: uppercase;
            letter-spacing: 1px;
        }

        .back-btn:hover {
            box-shadow: 0 0 15px rgba(0,255,255,0.4);
        }

        .map-card.map1 {
            background: linear-gradient(135deg, rgba(138,43,226,0.15), rgba(0,191,255,0.08));
            border-color: #8a2be2;
        }

        .map-card.map2 {
            background: linear-gradient(135deg, rgba(255,107,53,0.15), rgba(128,128,128,0.08));
            border-color: #ff6b35;
        }

        .map-card.map3 {
            background: linear-gradient(135deg, rgba(0,255,255,0.15), rgba(255,255,255,0.08));
            border-color: #00ffff;
        }

        .map-card:hover {
            transform: translateY(-5px);
        }

        .map-card.map1:hover { box-shadow: 0 0 30px rgba(138,43,226,0.5); }
        .map-card.map2:hover { box-shadow: 0 0 30px rgba(255,107,53,0.5); }
        .map-card.map3:hover { box-shadow: 0 0 30px rgba(0,255,255,0.5); }

        .map-card.selected {
            transform: translateY(-5px) scale(1.02);
        }

        .map-name {
            font-size: 16px;
            font-weight: bold;
            margin-bottom: 8px;
        }

        .map-card.map1 .map-name { color: #8a2be2; }
        .map-card.map2 .map-name { color: #ff6b35; }
        .map-card.map3 .map-name { color: #00ffff; }

        .map-desc {
            font-size: 12px;
            color: #aaa;
            line-height: 1.5;
        }

        .map-hint {
            font-size: 11px;
            color: #00ffff;
            text-transform: uppercase;
            letter-spacing: 1px;
            padding: 6px 12px;
            border: 1px solid rgba(0, 255, 255, 0.3);
            border-radius: 20px;
            display: inline-block;
            background: rgba(0, 255, 255, 0.1);
            margin-top: 10px;
        }

        .game-over-screen {
            position: fixed;
            top: 0;
            left: 0;
            right: 0;
            bottom: 0;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            background: rgba(0, 0, 0, 0.95);
            z-index: 300;
            padding: 20px;
        }

        .game-over-screen.hidden {
            display: none;
        }

        .game-over-title {
            font-size: 36px;
            margin-bottom: 20px;
        }

        .game-over-title.victory {
            color: #00ff00;
            text-shadow: 0 0 20px #00ff00;
        }

        .game-over-title.defeat {
            color: #ff0000;
            text-shadow: 0 0 20px #ff0000;
        }

        .final-stats {
            font-size: 16px;
            color: #aaa;
            margin-bottom: 30px;
            text-align: center;
            line-height: 1.6;
        }

        .start-btn {
            padding: 15px 40px;
            font-size: 16px;
            background: linear-gradient(135deg, #8a2be2, #00bfff);
            color: white;
            border: none;
            border-radius: 30px;
            cursor: pointer;
            transition: all 0.3s;
            text-transform: uppercase;
            letter-spacing: 2px;
            box-shadow: 0 0 30px rgba(138, 43, 226, 0.6);
        }

        .start-btn:hover {
            transform: scale(1.1);
            box-shadow: 0 0 50px rgba(138, 43, 226, 0.9);
        }

        @keyframes fadeInOut {
            0% { opacity: 0; transform: translate(-50%, -50%) scale(0.5); }
            20% { opacity: 1; transform: translate(-50%, -50%) scale(1.1); }
            80% { opacity: 1; transform: translate(-50%, -50%) scale(1); }
            100% { opacity: 0; transform: translate(-50%, -50%) scale(0.8); }
        }

        .message-popup {
            position: fixed;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            padding: 15px 30px;
            background: rgba(0, 0, 0, 0.8);
            border: 2px solid;
            border-radius: 10px;
            color: white;
            font-size: 16px;
            font-weight: bold;
            text-shadow: 0 0 10px currentColor;
            z-index: 1000;
            animation: fadeInOut 1.5s ease-in-out forwards;
        }

        @media screen and (min-width: 768px) {
            .header h1 {
                font-size: 24px;
            }

            .stat-value {
                font-size: 18px;
            }

            .stat-label {
                font-size: 12px;
            }

            .health-bar {
                width: 120px;
                height: 16px;
            }

            .title {
                font-size: 48px;
            }

            .subtitle {
                font-size: 16px;
            }

            .difficulty-container, .map-container {
                flex-direction: row;
                flex-wrap: wrap;
                justify-content: center;
                max-width: none;
            }

            .difficulty-card, .map-card {
                width: 260px;
            }

            .mobile-bottom-panel {
                display: none;
            }

            .sidebar-desktop {
                position: absolute;
                right: 0;
                top: 0;
                bottom: 0;
                width: 300px;
                background: linear-gradient(180deg, rgba(20, 10, 40, 0.95), rgba(10, 20, 40, 0.95));
                border-left: 2px solid rgba(0, 255, 255, 0.3);
                padding: 15px;
                display: flex;
                flex-direction: column;
                gap: 15px;
                overflow-y: auto;
                z-index: 60;
            }

            .panel-desktop {
                background: rgba(0, 0, 0, 0.4);
                border: 1px solid rgba(0, 255, 255, 0.3);
                border-radius: 10px;
                padding: 15px;
            }

            .panel-title-desktop {
                font-size: 13px;
                color: #00ffff;
                margin-bottom: 10px;
                text-transform: uppercase;
                letter-spacing: 2px;
                border-bottom: 1px solid rgba(0, 255, 255, 0.3);
                padding-bottom: 8px;
            }

            .tower-grid-desktop {
                display: grid;
                grid-template-columns: repeat(2, 1fr);
                gap: 10px;
            }

            .tower-card-desktop {
                background: rgba(0, 0, 0, 0.6);
                border: 2px solid rgba(0, 255, 255, 0.3);
                border-radius: 10px;
                padding: 12px;
                text-align: center;
                cursor: pointer;
                transition: all 0.2s;
            }

            .tower-card-desktop:hover, .tower-card-desktop.selected {
                border-color: #00ffff;
                box-shadow: 0 0 15px rgba(0, 255, 255, 0.5);
            }

            .tower-card-desktop .icon {
                font-size: 28px;
                margin-bottom: 8px;
            }

            .tower-card-desktop .name {
                font-size: 13px;
                font-weight: bold;
                margin-bottom: 4px;
            }

            .tower-card-desktop .price {
                font-size: 14px;
                color: #ffd700;
            }

            .upgrade-panel-desktop {
                display: none;
            }

            .upgrade-panel-desktop.active {
                display: block;
            }
        }

        @media screen and (max-width: 767px) {
            .sidebar-desktop {
                display: none;
            }

            .game-ui-overlay {
                padding: 4px;
            }

            .wave-info {
                padding: 6px 10px;
            }

            .wave-number {
                font-size: 14px;
            }

            .wave-status {
                font-size: 10px;
            }

            .resupply-timer {
                font-size: 12px;
            }
        }

        ::-webkit-scrollbar { width: 4px; }
        ::-webkit-scrollbar-track { background: rgba(0,0,0,0.2); }
        ::-webkit-scrollbar-thumb { background: rgba(0, 255, 255, 0.5); border-radius: 2px; }
    </style>
</head>
<body>
    <div class="start-screen" id="start-screen">
        <div class="title">CYBER DEFENSE</div>
        <div class="subtitle">赛博防线 - 守护未来城市</div>
        <div class="difficulty-container">
            <div class="difficulty-card easy" onclick="selectDifficulty('easy')">
                <span class="difficulty-icon">🌱</span>
                <div class="difficulty-name">EASY</div>
                <div class="difficulty-desc">适合新手<br>敌人减少30%<br>血量降低25%<br>资源+20%</div>
            </div>
            <div class="difficulty-card normal" onclick="selectDifficulty('normal')">
                <span class="difficulty-icon">⚡</span>
                <div class="difficulty-name">NORMAL</div>
                <div class="difficulty-desc">标准难度<br>属性正常<br>资源正常</div>
            </div>
            <div class="difficulty-card hard" onclick="selectDifficulty('hard')">
                <span class="difficulty-icon">💀</span>
                <div class="difficulty-name">HARD</div>
                <div class="difficulty-desc">极高挑战<br>敌人+40%<br>血量+35%<br>资源-15%</div>
            </div>
        </div>
    </div>

    <div class="map-select-screen hidden" id="map-select-screen">
        <button class="back-btn" onclick="goBackToDifficulty()">← 返回选择难度</button>
        <div class="title">选择战场</div>
        <div class="subtitle">点击地图开始游戏</div>
        <div class="map-container">
            <div class="map-card map1" onclick="selectMap(0)">
                <div class="map-name">Neon Metropolis</div>
                <div class="map-desc">霓虹都市<br>紫蓝主色调</div>
                <div class="map-hint">点击开始</div>
            </div>
            <div class="map-card map2" onclick="selectMap(1)">
                <div class="map-name">Cyber Wasteland</div>
                <div class="map-desc">赛博废土<br>橙红暗灰</div>
                <div class="map-hint">点击开始</div>
            </div>
            <div class="map-card map3" onclick="selectMap(2)">
                <div class="map-name">Orbital Station</div>
                <div class="map-desc">轨道空间站<br>青白主色调</div>
                <div class="map-hint">点击开始</div>
            </div>
        </div>
    </div>

    <div id="game-container">
        <div class="header">
            <h1>赛博防线</h1>
            <div class="stats">
                <div class="stat">
                    <span class="stat-label">能量</span>
                    <span class="stat-value" id="credits">500</span>
                </div>
                <div class="stat">
                    <span class="stat-label">基地</span>
                    <div class="health-bar">
                        <div class="health-fill" id="health-fill" style="width: 100%;"></div>
                    </div>
                </div>
                <div class="stat">
                    <span class="stat-label">得分</span>
                    <span class="stat-value" id="score">0</span>
                </div>
            </div>
        </div>

        <div class="main-content">
            <canvas id="game-canvas"></canvas>
            
            <div class="game-ui-overlay">
                <div class="wave-info" id="wave-info">
                    <div class="wave-number" id="wave-num">0</div>
                    <div class="wave-status" id="wave-status">准备中</div>
                    <div class="resupply-timer" id="resupply-timer"></div>
                </div>
                <button class="start-btn" id="start-wave-btn" onclick="startWave()" style="pointer-events: auto; padding: 10px 20px; font-size: 14px;">开始第一波</button>
            </div>

            <div class="sidebar-desktop" id="sidebar-desktop">
                <div class="panel-desktop">
                    <div class="panel-title-desktop">建造武器</div>
                    <div class="tower-grid-desktop" id="tower-grid-desktop"></div>
                </div>
                <div class="panel-desktop upgrade-panel-desktop" id="upgrade-panel-desktop">
                    <div class="panel-title-desktop">武器详情</div>
                    <div class="selected-tower-header">
                        <div class="selected-tower-icon" id="selected-icon-desktop"></div>
                        <div class="selected-tower-info">
                            <h4 id="selected-name-desktop">激光塔</h4>
                            <p id="selected-desc-desktop">高射速激光武器</p>
                        </div>
                    </div>
                    <div class="upgrade-level" id="level-pips-desktop">
                        <div class="level-pip filled"></div>
                        <div class="level-pip"></div>
                        <div class="level-pip"></div>
                        <div class="level-pip"></div>
                    </div>
                    <div class="upgrade-stats" id="upgrade-stats-desktop"></div>
                    <button class="btn-mobile btn-upgrade" id="upgrade-btn-desktop" onclick="upgradeTower()">升级</button>
                    <button class="btn-mobile btn-sell" onclick="sellTower()">出售</button>
                </div>
            </div>

            <div class="mobile-bottom-panel" id="mobile-panel">
                <div class="mobile-toggle-btn" onclick="toggleMobilePanel()"></div>
                <div class="panel-tabs">
                    <button class="tab-btn active" onclick="switchTab('build')">建造</button>
                    <button class="tab-btn" onclick="switchTab('upgrade')">升级</button>
                </div>
                <div class="panel-content active" id="build-tab">
                    <div class="tower-grid-mobile" id="tower-grid-mobile"></div>
                </div>
                <div class="panel-content" id="upgrade-tab">
                    <div class="upgrade-panel-mobile" id="upgrade-panel-mobile">
                        <div class="selected-tower-header">
                            <div class="selected-tower-icon" id="selected-icon-mobile"></div>
                            <div class="selected-tower-info">
                                <h4 id="selected-name-mobile">选择塔</h4>
                                <p id="selected-desc-mobile">点击地图上的塔</p>
                            </div>
                        </div>
                        <div class="upgrade-level" id="level-pips-mobile">
                            <div class="level-pip"></div>
                            <div class="level-pip"></div>
                            <div class="level-pip"></div>
                            <div class="level-pip"></div>
                        </div>
                        <div class="upgrade-stats" id="upgrade-stats-mobile">点击塔查看详情</div>
                        <button class="btn-mobile btn-upgrade" id="upgrade-btn-mobile" onclick="upgradeTower()" disabled>升级</button>
                        <button class="btn-mobile btn-sell" onclick="sellTower()" disabled>出售</button>
                    </div>
                </div>
            </div>
        </div>
    </div>

    <div class="game-over-screen hidden" id="game-over-screen">
        <div class="game-over-title" id="game-over-title">游戏结束</div>
        <div class="final-stats" id="final-stats"></div>
        <button class="start-btn" onclick="restartGame()">再来一局</button>
    </div>

    <script>
        const GRID_WIDTH = 15;
        const GRID_HEIGHT = 10;
        const TILE_SIZE = 64;
        const TARGET_FPS = 60;
        const TARGET_TIME_STEP = 1000 / TARGET_FPS;

        const DIFFICULTY_CONFIG = {
            easy: { enemyCountMult: 0.7, enemyHealthMult: 0.75, enemySpeedMult: 0.9, resourceMult: 1.2 },
            normal: { enemyCountMult: 1.0, enemyHealthMult: 1.0, enemySpeedMult: 1.0, resourceMult: 1.0 },
            hard: { enemyCountMult: 1.4, enemyHealthMult: 1.35, enemySpeedMult: 1.15, resourceMult: 0.85 }
        };

        const TOWER_TYPES = {
            laser: { name: '激光塔', icon: '⚡', color: '#00bfff', description: '高射速激光武器', cost: 60, damage: 15, range: 120, fireRate: 400, upgrades: [
                { cost: 80, damageBonus: 8, rangeBonus: 20, fireRateBonus: -50 },
                { cost: 150, damageBonus: 10, rangeBonus: 30, fireRateBonus: -50 },
                { cost: 250, damageBonus: 15, rangeBonus: 40, fireRateBonus: -50 }
            ]},
            missile: { name: '导弹塔', icon: '🚀', color: '#ff6600', description: '范围伤害导弹', cost: 80, damage: 40, range: 100, fireRate: 1200, upgrades: [
                { cost: 100, damageBonus: 20, rangeBonus: 20, fireRateBonus: -100 },
                { cost: 200, damageBonus: 30, rangeBonus: 30, fireRateBonus: -150 },
                { cost: 350, damageBonus: 40, rangeBonus: 40, fireRateBonus: -150 }
            ]},
            em: { name: '电磁塔', icon: '🔮', color: '#8a2be2', description: '减速电磁脉冲', cost: 100, damage: 25, range: 80, fireRate: 800, upgrades: [
                { cost: 120, damageBonus: 15, rangeBonus: 15, fireRateBonus: -80 },
                { cost: 220, damageBonus: 20, rangeBonus: 25, fireRateBonus: -100 },
                { cost: 380, damageBonus: 25, rangeBonus: 30, fireRateBonus: -100 }
            ]},
            plasma: { name: '等离子塔', icon: '🔥', color: '#00ff00', description: '持续伤害', cost: 120, damage: 35, range: 90, fireRate: 600, upgrades: [
                { cost: 150, damageBonus: 20, rangeBonus: 20, fireRateBonus: -60 },
                { cost: 280, damageBonus: 25, rangeBonus: 30, fireRateBonus: -80 },
                { cost: 450, damageBonus: 30, rangeBonus: 35, fireRateBonus: -80 }
            ]}
        };

        const ENEMY_TYPES = {
            drone: { name: '无人机', health: 40, speed: 2.5, reward: 15, color: '#00bfff', size: 12 },
            cyborg: { name: '生化人', health: 80, speed: 1.8, reward: 25, color: '#00ff00', size: 16 },
            tank: { name: '重装甲', health: 150, speed: 1.0, reward: 40, color: '#ff6600', size: 20 },
            stealth: { name: '隐形者', health: 60, speed: 3.0, reward: 30, color: '#8a2be2', size: 14 },
            boss: { name: 'BOSS', health: 500, speed: 0.8, reward: 150, color: '#ff0066', size: 30 }
        };

        const MAPS = [
            { name: 'Neon Metropolis', bgColor: '#0a0a1a', groundColor1: '#1a1a3a', groundColor2: '#2a2a4a', pathColor: '#8a2be2', pathBorderColor: '#00ffff',
              path: [[0, 4], [1, 4], [2, 4], [2, 3], [2, 2], [3, 2], [4, 2], [4, 3], [4, 4], [4, 5], [4, 6], [5, 6], [6, 6], [6, 5], [6, 4], [7, 4], [8, 4], [8, 5], [8, 6], [8, 7], [9, 7], [10, 7], [10, 6], [10, 5], [11, 5], [12, 5], [12, 4], [13, 4], [14, 4]] },
            { name: 'Cyber Wasteland', bgColor: '#0a0505', groundColor1: '#2a1515', groundColor2: '#3a2020', pathColor: '#ff6600', pathBorderColor: '#ffaa00',
              path: [[0, 5], [1, 5], [2, 5], [2, 4], [2, 3], [3, 3], [4, 3], [5, 3], [5, 4], [5, 5], [5, 6], [5, 7], [6, 7], [7, 7], [8, 7], [8, 6], [8, 5], [8, 4], [8, 3], [9, 3], [10, 3], [10, 4], [10, 5], [11, 5], [12, 5], [13, 5], [14, 5]] },
            { name: 'Orbital Station', bgColor: '#051515', groundColor1: '#152525', groundColor2: '#203535', pathColor: '#00ffff', pathBorderColor: '#ffffff',
              path: [[0, 4], [1, 4], [2, 4], [3, 4], [4, 4], [4, 3], [4, 2], [5, 2], [6, 2], [7, 2], [7, 3], [7, 4], [7, 5], [7, 6], [8, 6], [9, 6], [10, 6], [10, 5], [10, 4], [11, 4], [12, 4], [13, 4], [14, 4]] }
        ];

        let selectedDifficulty = null;
        let selectedMap = null;
        let game = null;
        let canvas = null;
        let ctx = null;
        let animationId = null;
        let accumulator = 0;
        let lastTime = performance.now();

        function initGame() {
            canvas = document.getElementById('game-canvas');
            ctx = canvas.getContext('2d');
            resizeCanvas();
            window.addEventListener('resize', resizeCanvas);
            canvas.addEventListener('click', handleCanvasClick);
            canvas.addEventListener('touchstart', handleTouchStart);
            
            buildTowerGrid();
        }

        function resizeCanvas() {
            const container = document.getElementById('game-container');
            const sidebarWidth = window.innerWidth >= 768 ? 300 : 0;
            canvas.width = container.clientWidth - sidebarWidth;
            canvas.height = container.clientHeight - 60;
        }

        function buildTowerGrid() {
            const desktopGrid = document.getElementById('tower-grid-desktop');
            const mobileGrid = document.getElementById('tower-grid-mobile');
            
            for (const [type, data] of Object.entries(TOWER_TYPES)) {
                const desktopCard = document.createElement('div');
                desktopCard.className = 'tower-card-desktop';
                desktopCard.innerHTML = `<div class="icon">${data.icon}</div><div class="name">${data.name}</div><div class="price">💰 ${data.cost}</div>`;
                desktopCard.onclick = () => selectTowerType(type);
                desktopCard.style.borderColor = data.color;
                desktopGrid.appendChild(desktopCard);

                const mobileCard = document.createElement('div');
                mobileCard.className = 'tower-card-mobile';
                mobileCard.innerHTML = `<div class="icon">${data.icon}</div><div class="name">${data.name}</div><div class="price">💰 ${data.cost}</div>`;
                mobileCard.onclick = () => selectTowerType(type);
                mobileCard.style.borderColor = data.color;
                mobileGrid.appendChild(mobileCard);
            }
        }

        function selectDifficulty(difficulty) {
            selectedDifficulty = difficulty;
            document.querySelectorAll('.difficulty-card').forEach(card => card.classList.remove('selected'));
            document.querySelector(`.difficulty-card.${difficulty}`).classList.add('selected');
            
            setTimeout(() => {
                document.getElementById('start-screen').classList.add('hidden');
                document.getElementById('map-select-screen').classList.remove('hidden');
            }, 300);
        }

        function goBackToDifficulty() {
            document.getElementById('map-select-screen').classList.add('hidden');
            document.getElementById('start-screen').classList.remove('hidden');
            selectedMap = null;
        }

        function selectMap(mapIndex) {
            selectedMap = mapIndex;
            document.querySelectorAll('.map-card').forEach(card => card.classList.remove('selected'));
            document.querySelectorAll('.map-card')[mapIndex].classList.add('selected');
            
            setTimeout(() => {
                document.getElementById('map-select-screen').classList.add('hidden');
                document.getElementById('game-container').style.display = 'flex';
                startGame(mapIndex);
            }, 300);
        }

        function startGame(mapIndex) {
            game = {
                difficulty: selectedDifficulty,
                currentMap: mapIndex,
                credits: 500,
                score: 0,
                lives: 20,
                wave: 0,
                isPlaying: false,
                enemies: [],
                towers: [],
                bullets: [],
                particles: [],
                selectedTower: null,
                selectedTowerType: null,
                portalAngle: 0,
                baseShieldIntensity: 1,
                isResupplying: false,
                resupplyTime: 0
            };

            initMap();
            updateUI();
            gameLoop();
        }

        function initMap() {
            const mapData = MAPS[game.currentMap];
            game.grid = [];
            for (let y = 0; y < GRID_HEIGHT; y++) {
                game.grid[y] = [];
                for (let x = 0; x < GRID_WIDTH; x++) {
                    game.grid[y][x] = 0;
                }
            }

            for (const [x, y] of mapData.path) {
                game.grid[y][x] = 1;
            }

            game.spawnPoint = { x: mapData.path[0][0], y: mapData.path[0][1] };
            game.basePoint = { x: mapData.path[mapData.path.length - 1][0], y: mapData.path[mapData.path.length - 1][1] };
        }

        function handleCanvasClick(e) {
            const rect = canvas.getBoundingClientRect();
            const clickX = e.clientX - rect.left;
            const clickY = e.clientY - rect.top;
            const gridX = Math.floor(clickX / (canvas.width / GRID_WIDTH));
            const gridY = Math.floor(clickY / (canvas.height / GRID_HEIGHT));

            if (gridX < 0 || gridX >= GRID_WIDTH || gridY < 0 || gridY >= GRID_HEIGHT) return;

            let clickedTower = null;
            const tileWidth = canvas.width / GRID_WIDTH;
            const tileHeight = canvas.height / GRID_HEIGHT;
            
            for (const tower of game.towers) {
                const towerCenterX = tower.x * tileWidth + tileWidth / 2;
                const towerCenterY = tower.y * tileHeight + tileHeight / 2;
                const dx = clickX - towerCenterX;
                const dy = clickY - towerCenterY;
                const distance = Math.sqrt(dx * dx + dy * dy);
                
                if (distance < tileWidth * 0.6) {
                    clickedTower = tower;
                    break;
                }
            }

            if (game.selectedTowerType && game.grid[gridY][gridX] === 0) {
                const towerData = TOWER_TYPES[game.selectedTowerType];
                if (game.credits >= towerData.cost) {
                    game.credits -= towerData.cost;
                    const newTower = {
                        x: gridX, y: gridY,
                        type: game.selectedTowerType,
                        level: 0,
                        damage: towerData.damage,
                        range: towerData.range,
                        fireRate: towerData.fireRate,
                        lastFire: 0,
                        angle: 0,
                        targetAngle: 0
                    };
                    game.towers.push(newTower);
                    game.grid[gridY][gridX] = 2;
                    updateUI();
                }
            } else if (clickedTower) {
                game.selectedTower = clickedTower;
                game.selectedTowerType = null;
                showTowerInfo(clickedTower);
            } else {
                game.selectedTower = null;
                hideTowerInfo();
            }
        }

        function handleTouchStart(e) {
            e.preventDefault();
            const touch = e.touches[0];
            const rect = canvas.getBoundingClientRect();
            const clickX = touch.clientX - rect.left;
            const clickY = touch.clientY - rect.top;
            const gridX = Math.floor(clickX / (canvas.width / GRID_WIDTH));
            const gridY = Math.floor(clickY / (canvas.height / GRID_HEIGHT));

            if (gridX < 0 || gridX >= GRID_WIDTH || gridY < 0 || gridY >= GRID_HEIGHT) return;

            let clickedTower = null;
            const tileWidth = canvas.width / GRID_WIDTH;
            const tileHeight = canvas.height / GRID_HEIGHT;
            
            for (const tower of game.towers) {
                const towerCenterX = tower.x * tileWidth + tileWidth / 2;
                const towerCenterY = tower.y * tileHeight + tileHeight / 2;
                const dx = clickX - towerCenterX;
                const dy = clickY - towerCenterY;
                const distance = Math.sqrt(dx * dx + dy * dy);
                
                if (distance < tileWidth * 0.7) {
                    clickedTower = tower;
                    break;
                }
            }

            if (game.selectedTowerType && game.grid[gridY][gridX] === 0) {
                const towerData = TOWER_TYPES[game.selectedTowerType];
                if (game.credits >= towerData.cost) {
                    game.credits -= towerData.cost;
                    const newTower = {
                        x: gridX, y: gridY,
                        type: game.selectedTowerType,
                        level: 0,
                        damage: towerData.damage,
                        range: towerData.range,
                        fireRate: towerData.fireRate,
                        lastFire: 0,
                        angle: 0,
                        targetAngle: 0
                    };
                    game.towers.push(newTower);
                    game.grid[gridY][gridX] = 2;
                    updateUI();
                }
            } else if (clickedTower) {
                game.selectedTower = clickedTower;
                game.selectedTowerType = null;
                showTowerInfo(clickedTower);
            }
        }

        function selectTowerType(type) {
            game.selectedTowerType = game.selectedTowerType === type ? null : type;
            game.selectedTower = null;
            hideTowerInfo();

            document.querySelectorAll('.tower-card-desktop').forEach(c => c.classList.remove('selected'));
            document.querySelectorAll('.tower-card-mobile').forEach(c => c.classList.remove('selected'));

            if (game.selectedTowerType) {
                const towerData = TOWER_TYPES[type];
                document.querySelector(`.tower-card-desktop:has(.icon:contains("${towerData.icon}"))`)?.classList.add('selected');
                document.querySelector(`.tower-card-mobile:has(.icon:contains("${towerData.icon}"))`)?.classList.add('selected');
            }
        }

        function showTowerInfo(tower) {
            const towerData = TOWER_TYPES[tower.type];
            
            const canUpgrade = tower.level < 3;
            const upgradeCost = canUpgrade ? towerData.upgrades[tower.level].cost : 0;
            const canAfford = game.credits >= upgradeCost;

            const updatePanel = (prefix) => {
                const icon = document.getElementById(`selected-icon-${prefix}`);
                const name = document.getElementById(`selected-name-${prefix}`);
                const desc = document.getElementById(`selected-desc-${prefix}`);
                const pips = document.getElementById(`level-pips-${prefix}`);
                const stats = document.getElementById(`upgrade-stats-${prefix}`);
                const upgradeBtn = document.getElementById(`upgrade-btn-${prefix}`);

                if (!icon) return;

                icon.style.background = `radial-gradient(circle, ${towerData.color}44, transparent)`;
                icon.style.border = `2px solid ${towerData.color}`;
                icon.textContent = towerData.icon;
                name.textContent = `${towerData.name} Lv.${tower.level + 1}`;
                desc.textContent = towerData.description;

                pips.innerHTML = '';
                for (let i = 0; i < 4; i++) {
                    const pip = document.createElement('div');
                    pip.className = `level-pip ${i <= tower.level ? 'filled' : ''}`;
                    pips.appendChild(pip);
                }

                const fireRateText = tower.fireRate < 400 ? '极快' : tower.fireRate < 800 ? '快速' : tower.fireRate < 1200 ? '中速' : '慢速';
                
                stats.innerHTML = `
                    <div>伤害 <span>${tower.damage}</span></div>
                    <div>范围 <span>${tower.range}</span></div>
                    <div>射速 <span>${fireRateText}</span></div>
                `;

                if (canUpgrade) {
                    const nextUpgrade = towerData.upgrades[tower.level];
                    const nextDamage = tower.damage + nextUpgrade.damageBonus;
                    const nextRange = tower.range + nextUpgrade.rangeBonus;
                    const nextFireRate = tower.fireRate + nextUpgrade.fireRateBonus;
                    const nextFireRateText = nextFireRate < 400 ? '极快' : nextFireRate < 800 ? '快速' : nextFireRate < 1200 ? '中速' : '慢速';
                    
                    stats.innerHTML += `
                        <div class="upgrade-preview">
                            <div style="font-size: 11px; color: #00ffff; margin-bottom: 8px;">升级到 Lv.${tower.level + 2}</div>
                            <div style="display: grid; grid-template-columns: 2fr 1fr; gap: 4px; font-size: 12px;">
                                <div style="color: #888;">伤害:</div>
                                <div style="color: #ff6600;">${tower.damage} → <span style="color: #00ff00;">${nextDamage}</span></div>
                                <div style="color: #888;">范围:</div>
                                <div style="color: #ff6600;">${tower.range} → <span style="color: #00ff00;">${nextRange}</span></div>
                                <div style="color: #888;">射速:</div>
                                <div style="color: #ff6600;">${fireRateText} → <span style="color: #00ff00;">${nextFireRateText}</span></div>
                            </div>
                            <div style="margin-top: 8px; font-size: 13px;">
                                <span style="color: #${canAfford ? 'ffd700' : 'ff4444'}">💰 ${upgradeCost}</span>
                                ${canAfford ? '<span style="color: #00ff00;">✓ 可升级</span>' : '<span style="color: #ff4444;">✗ 资源不足</span>'}
                            </div>
                        </div>
                    `;
                } else {
                    stats.innerHTML += `<div style="margin-top: 8px; color: #00ff00; text-align: center; padding: 8px; background: rgba(0, 255, 0, 0.1); border-radius: 5px;">✓ 已达满级</div>`;
                }

                upgradeBtn.disabled = !canUpgrade || !canAfford;
                upgradeBtn.textContent = canUpgrade ? (canAfford ? `升级 Lv.${tower.level + 2}` : '能量不足') : '已满级';
            };

            updatePanel('desktop');
            updatePanel('mobile');

            document.getElementById('upgrade-panel-desktop')?.classList.add('active');
            switchTab('upgrade');
        }

        function hideTowerInfo() {
            document.getElementById('upgrade-panel-desktop')?.classList.remove('active');
        }

        function upgradeTower() {
            if (!game.selectedTower) return;
            const tower = game.selectedTower;
            const towerData = TOWER_TYPES[tower.type];

            if (tower.level >= 3) {
                showMessage('已达满级！', '#ffd700');
                return;
            }
            
            const upgrade = towerData.upgrades[tower.level];
            if (game.credits < upgrade.cost) {
                showMessage('能量不足！', '#ff4444');
                return;
            }

            game.credits -= upgrade.cost;
            tower.level++;
            tower.damage += upgrade.damageBonus;
            tower.range += upgrade.rangeBonus;
            tower.fireRate += upgrade.fireRateBonus;

            updateUI();
            showTowerInfo(tower);
            showMessage('升级成功！', '#00ff00');
        }

        function sellTower() {
            if (!game.selectedTower) return;
            const tower = game.selectedTower;
            const towerData = TOWER_TYPES[tower.type];

            let sellValue = Math.floor(towerData.cost * 0.6);
            for (let i = 0; i < tower.level; i++) {
                sellValue += Math.floor(towerData.upgrades[i].cost * 0.6);
            }

            game.credits += sellValue;
            game.towers = game.towers.filter(t => t !== tower);
            game.grid[tower.y][tower.x] = 0;
            game.selectedTower = null;

            hideTowerInfo();
            updateUI();
            showMessage(`出售获得 ${sellValue} 能量！`, '#ffd700');
        }

        function showMessage(message, color) {
            const el = document.createElement('div');
            el.className = 'message-popup';
            el.textContent = message;
            el.style.borderColor = color;
            el.style.color = color;
            document.body.appendChild(el);
            setTimeout(() => document.body.removeChild(el), 1500);
        }

        function startWave() {
            if (game.isPlaying) return;
            game.wave++;
            game.isPlaying = true;
            game.isResupplying = false;
            document.getElementById('start-wave-btn').style.display = 'none';
            spawnWave();
        }

        function spawnWave() {
            const waveConfig = getWaveConfig();
            let spawned = 0;
            const total = Math.floor(waveConfig.count);
            
            const spawnInterval = setInterval(() => {
                if (spawned >= total || !game.isPlaying) {
                    clearInterval(spawnInterval);
                    return;
                }

                const types = ['drone', 'cyborg', 'tank'];
                if (game.wave >= 5) types.push('stealth');
                if (game.wave >= 8 && spawned === Math.floor(total / 2)) {
                    spawnEnemy('boss', waveConfig);
                    spawned++;
                    return;
                }

                const type = types[Math.floor(Math.random() * types.length)];
                spawnEnemy(type, waveConfig);
                spawned++;
            }, 800);
        }

        function getWaveConfig() {
            const diffConfig = DIFFICULTY_CONFIG[game.difficulty];
            const baseCount = 5 + game.wave * 2;
            const baseHealth = 40 + game.wave * 15;
            
            return {
                count: baseCount * diffConfig.enemyCountMult,
                healthMult: baseHealth * diffConfig.enemyHealthMult,
                speedMult: diffConfig.enemySpeedMult,
                rewardMult: diffConfig.resourceMult
            };
        }

        function spawnEnemy(type, config) {
            const mapData = MAPS[game.currentMap];
            const start = mapData.path[0];
            const enemyData = ENEMY_TYPES[type];
            
            game.enemies.push({
                type,
                x: start[0],
                y: start[1],
                health: enemyData.health * config.healthMult,
                maxHealth: enemyData.health * config.healthMult,
                speed: enemyData.speed * config.speedMult,
                reward: Math.floor(enemyData.reward * config.rewardMult),
                color: enemyData.color,
                size: enemyData.size,
                pathIndex: 0,
                teleporting: true,
                teleportProgress: 0
            });
        }

        function gameLoop() {
            const now = performance.now();
            accumulator += now - lastTime;
            lastTime = now;

            while (accumulator >= TARGET_TIME_STEP) {
                update();
                accumulator -= TARGET_TIME_STEP;
            }

            render();
            animationId = requestAnimationFrame(gameLoop);
        }

        function update() {
            if (game.isResupplying) {
                game.resupplyTime -= TARGET_TIME_STEP;
                if (game.resupplyTime <= 0) {
                    game.isResupplying = false;
                    game.isPlaying = true;
                    spawnWave();
                }
                return;
            }

            updateEnemies();
            updateTowers();
            updateBullets();
            updateParticles();
            checkWaveComplete();
        }

        function updateEnemies() {
            for (const enemy of game.enemies) {
                if (enemy.teleporting) {
                    enemy.teleportProgress += 0.02;
                    if (enemy.teleportProgress >= 1) {
                        enemy.teleporting = false;
                    }
                    continue;
                }

                const mapData = MAPS[game.currentMap];
                if (enemy.pathIndex >= mapData.path.length - 1) {
                    game.lives--;
                    game.enemies = game.enemies.filter(e => e !== enemy);
                    updateUI();
                    continue;
                }

                const target = mapData.path[enemy.pathIndex + 1];
                const dx = target[0] - enemy.x;
                const dy = target[1] - enemy.y;
                const dist = Math.sqrt(dx * dx + dy * dy);

                if (dist < 0.1) {
                    enemy.pathIndex++;
                } else {
                    const speed = enemy.speed * (TARGET_TIME_STEP / 16);
                    enemy.x += (dx / dist) * speed;
                    enemy.y += (dy / dist) * speed;
                }
            }
        }

        function updateTowers() {
            const now = performance.now();
            const tileWidth = canvas.width / GRID_WIDTH;
            const tileHeight = canvas.height / GRID_HEIGHT;

            for (const tower of game.towers) {
                if (now - tower.lastFire < tower.fireRate) continue;

                let target = null;
                let minDist = Infinity;

                for (const enemy of game.enemies) {
                    if (enemy.teleporting) continue;
                    
                    const towerScreenX = tower.x * tileWidth + tileWidth / 2;
                    const towerScreenY = tower.y * tileHeight + tileHeight / 2;
                    const enemyScreenX = enemy.x * tileWidth + tileWidth / 2;
                    const enemyScreenY = enemy.y * tileHeight + tileHeight / 2;
                    const dx = enemyScreenX - towerScreenX;
                    const dy = enemyScreenY - towerScreenY;
                    const dist = Math.sqrt(dx * dx + dy * dy);

                    if (dist < tower.range && dist < minDist) {
                        minDist = dist;
                        target = enemy;
                    }
                }

                if (target) {
                    const towerScreenX = tower.x * tileWidth + tileWidth / 2;
                    const towerScreenY = tower.y * tileHeight + tileHeight / 2;
                    const targetScreenX = target.x * tileWidth + tileWidth / 2;
                    const targetScreenY = target.y * tileHeight + tileHeight / 2;
                    
                    tower.targetAngle = Math.atan2(targetScreenY - towerScreenY, targetScreenX - towerScreenX);
                    tower.lastFire = now;

                    game.bullets.push({
                        x: towerScreenX,
                        y: towerScreenY,
                        target,
                        damage: tower.damage,
                        type: tower.type,
                        angle: tower.targetAngle,
                        speed: tower.type === 'missile' ? 3 : 8
                    });
                }

                tower.angle += (tower.targetAngle - tower.angle) * 0.1;
            }
        }

        function updateBullets() {
            game.bullets = game.bullets.filter(bullet => {
                const targetScreenX = bullet.target.x * (canvas.width / GRID_WIDTH) + (canvas.width / GRID_WIDTH) / 2;
                const targetScreenY = bullet.target.y * (canvas.height / GRID_HEIGHT) + (canvas.height / GRID_HEIGHT) / 2;
                const dx = targetScreenX - bullet.x;
                const dy = targetScreenY - bullet.y;
                const dist = Math.sqrt(dx * dx + dy * dy);

                if (dist < 10) {
                    bullet.target.health -= bullet.damage;
                    createParticles(bullet.x, bullet.y, TOWER_TYPES[bullet.type].color, 15);
                    
                    if (bullet.target.health <= 0) {
                        game.credits += bullet.target.reward;
                        game.score += bullet.target.reward;
                        game.enemies = game.enemies.filter(e => e !== bullet.target);
                        createParticles(targetScreenX, targetScreenY, bullet.target.color, 30);
                    }
                    updateUI();
                    return false;
                }

                bullet.x += Math.cos(bullet.angle) * bullet.speed;
                bullet.y += Math.sin(bullet.angle) * bullet.speed;
                return true;
            });
        }

        function updateParticles() {
            game.particles = game.particles.filter(p => {
                p.x += p.vx;
                p.y += p.vy;
                p.life--;
                p.alpha = p.life / p.maxLife;
                return p.life > 0;
            });
        }

        function checkWaveComplete() {
            if (game.isPlaying && game.enemies.length === 0 && !game.isResupplying) {
                game.isPlaying = false;
                game.isResupplying = true;
                game.resupplyTime = 5000;
                
                if (game.wave >= 12) {
                    endGame(true);
                    return;
                }
            }
        }

        function endGame(victory) {
            cancelAnimationFrame(animationId);
            const title = document.getElementById('game-over-title');
            const stats = document.getElementById('final-stats');
            
            title.textContent = victory ? '胜利！' : '失败！';
            title.className = `game-over-title ${victory ? 'victory' : 'defeat'}`;
            stats.innerHTML = `
                <div>最终波次: ${game.wave}</div>
                <div>最终得分: ${game.score}</div>
                <div>剩余能量: ${game.credits}</div>
            `;
            
            document.getElementById('game-over-screen').classList.remove('hidden');
        }

        function restartGame() {
            cancelAnimationFrame(animationId);
            document.getElementById('game-over-screen').classList.add('hidden');
            document.getElementById('game-container').style.display = 'none';
            document.getElementById('start-screen').classList.remove('hidden');
            selectedDifficulty = null;
            selectedMap = null;
            game = null;
        }

        function updateUI() {
            document.getElementById('credits').textContent = game.credits;
            document.getElementById('score').textContent = game.score;
            document.getElementById('health-fill').style.width = `${(game.lives / 20) * 100}%`;
            document.getElementById('wave-num').textContent = `波次 ${game.wave}`;
            
            if (game.isResupplying) {
                document.getElementById('wave-status').textContent = '补给中';
                document.getElementById('resupply-timer').textContent = `⏱️ ${Math.ceil(game.resupplyTime / 1000)}s`;
            } else if (game.isPlaying) {
                document.getElementById('wave-status').textContent = '战斗中';
                document.getElementById('resupply-timer').textContent = `👾 ${game.enemies.length} 敌人`;
            } else {
                document.getElementById('wave-status').textContent = '准备中';
                document.getElementById('resupply-timer').textContent = '';
            }

            if (game.lives <= 0) {
                endGame(false);
            }
        }

        function createParticles(x, y, color, count) {
            for (let i = 0; i < count; i++) {
                const angle = Math.random() * Math.PI * 2;
                const speed = Math.random() * 3 + 1;
                game.particles.push({
                    x, y,
                    vx: Math.cos(angle) * speed,
                    vy: Math.sin(angle) * speed,
                    color,
                    life: 30,
                    maxLife: 30,
                    alpha: 1,
                    size: Math.random() * 4 + 2
                });
            }
        }

        function render() {
            const tileWidth = canvas.width / GRID_WIDTH;
            const tileHeight = canvas.height / GRID_HEIGHT;
            const mapData = MAPS[game.currentMap];

            ctx.fillStyle = mapData.bgColor;
            ctx.fillRect(0, 0, canvas.width, canvas.height);

            for (let y = 0; y < GRID_HEIGHT; y++) {
                for (let x = 0; x < GRID_WIDTH; x++) {
                    const tileX = x * tileWidth;
                    const tileY = y * tileHeight;

                    if (game.grid[y][x] === 1) {
                        ctx.fillStyle = mapData.groundColor1;
                        ctx.fillRect(tileX, tileY, tileWidth, tileHeight);
                    }

                    ctx.strokeStyle = mapData.groundColor2;
                    ctx.lineWidth = 1;
                    ctx.strokeRect(tileX, tileY, tileWidth, tileHeight);
                }
            }

            ctx.strokeStyle = mapData.pathBorderColor;
            ctx.lineWidth = 3;
            ctx.lineCap = 'round';
            ctx.lineJoin = 'round';

            ctx.beginPath();
            ctx.moveTo(mapData.path[0][0] * tileWidth + tileWidth / 2, mapData.path[0][1] * tileHeight + tileHeight / 2);
            for (let i = 1; i < mapData.path.length; i++) {
                ctx.lineTo(mapData.path[i][0] * tileWidth + tileWidth / 2, mapData.path[i][1] * tileHeight + tileHeight / 2);
            }
            ctx.stroke();

            renderPortal(tileWidth, tileHeight);
            renderBase(tileWidth, tileHeight);

            game.towers.forEach(tower => {
                const towerData = TOWER_TYPES[tower.type];
                const x = tower.x * tileWidth + tileWidth / 2;
                const y = tower.y * tileHeight + tileHeight / 2;
                const size = 12 + tower.level * 2;

                ctx.save();
                ctx.translate(x, y);
                ctx.rotate(tower.angle);

                if (game.selectedTower === tower) {
                    const pulse = Math.sin(Date.now() / 200) * 0.3 + 0.7;
                    ctx.shadowColor = '#00ffff';
                    ctx.shadowBlur = 25 * pulse;
                    ctx.strokeStyle = '#00ffff';
                    ctx.lineWidth = 2;
                    ctx.beginPath();
                    ctx.arc(0, 0, size + 20, 0, Math.PI * 2);
                    ctx.stroke();
                }

                ctx.shadowColor = towerData.color;
                ctx.shadowBlur = 10 + tower.level * 3;

                ctx.fillStyle = '#1a1a2a';
                ctx.beginPath();
                ctx.arc(0, 0, size, 0, Math.PI * 2);
                ctx.fill();

                ctx.strokeStyle = towerData.color;
                ctx.lineWidth = 2;
                ctx.stroke();

                for (let i = 0; i < 3 + tower.level; i++) {
                    const angle = (i / (3 + tower.level)) * Math.PI * 2;
                    ctx.fillStyle = towerData.color;
                    ctx.beginPath();
                    ctx.arc(Math.cos(angle) * (size - 4), Math.sin(angle) * (size - 4), 3, 0, Math.PI * 2);
                    ctx.fill();
                }

                ctx.restore();
            });

            game.enemies.forEach(enemy => {
                const x = enemy.x * tileWidth + tileWidth / 2;
                const y = enemy.y * tileHeight + tileHeight / 2;

                ctx.save();
                ctx.translate(x, y);

                if (enemy.teleporting) {
                    const scale = enemy.teleportProgress;
                    const alpha = enemy.teleportProgress;
                    ctx.globalAlpha = alpha;
                    ctx.scale(scale, scale);
                    
                    ctx.shadowColor = '#00ffff';
                    ctx.shadowBlur = 20;
                    ctx.strokeStyle = '#00ffff';
                    ctx.lineWidth = 2;
                    for (let i = 0; i < 3; i++) {
                        const r = 20 + i * 10;
                        const offset = (Date.now() / 200 + i) % (Math.PI * 2);
                        ctx.beginPath();
                        ctx.arc(0, 0, r * scale, offset, offset + Math.PI * 1.5);
                        ctx.stroke();
                    }
                }

                ctx.shadowColor = enemy.color;
                ctx.shadowBlur = 10;
                ctx.fillStyle = enemy.color;
                ctx.beginPath();
                ctx.arc(0, 0, enemy.size, 0, Math.PI * 2);
                ctx.fill();

                ctx.fillStyle = '#ffffff';
                ctx.beginPath();
                ctx.arc(-3, -2, enemy.size * 0.3, 0, Math.PI * 2);
                ctx.arc(3, -2, enemy.size * 0.3, 0, Math.PI * 2);
                ctx.fill();

                ctx.restore();

                const healthPercent = enemy.health / enemy.maxHealth;
                const barWidth = enemy.size * 2;
                const barHeight = 4;
                ctx.fillStyle = '#333';
                ctx.fillRect(x - barWidth / 2, y - enemy.size - 10, barWidth, barHeight);
                ctx.fillStyle = healthPercent > 0.5 ? '#00ff00' : healthPercent > 0.25 ? '#ffd700' : '#ff0000';
                ctx.fillRect(x - barWidth / 2, y - enemy.size - 10, barWidth * healthPercent, barHeight);
            });

            game.bullets.forEach(bullet => {
                ctx.save();
                ctx.translate(bullet.x, bullet.y);
                ctx.rotate(bullet.angle);

                ctx.shadowColor = TOWER_TYPES[bullet.type].color;
                ctx.shadowBlur = 15;

                if (bullet.type === 'missile') {
                    ctx.fillStyle = '#4a4a4a';
                    ctx.beginPath();
                    ctx.moveTo(8, 0);
                    ctx.lineTo(-8, -4);
                    ctx.lineTo(-4, 0);
                    ctx.lineTo(-8, 4);
                    ctx.closePath();
                    ctx.fill();
                    
                    ctx.fillStyle = '#ff6600';
                    ctx.beginPath();
                    ctx.moveTo(-6, -2);
                    ctx.lineTo(-12, 0);
                    ctx.lineTo(-6, 2);
                    ctx.closePath();
                    ctx.fill();
                } else {
                    ctx.fillStyle = TOWER_TYPES[bullet.type].color;
                    ctx.beginPath();
                    ctx.arc(0, 0, 4, 0, Math.PI * 2);
                    ctx.fill();
                }

                ctx.restore();
            });

            game.particles.forEach(p => {
                ctx.globalAlpha = p.alpha;
                ctx.fillStyle = p.color;
                ctx.beginPath();
                ctx.arc(p.x, p.y, p.size * p.alpha, 0, Math.PI * 2);
                ctx.fill();
            });
            ctx.globalAlpha = 1;
        }

        function renderPortal(tileWidth, tileHeight) {
            const x = game.spawnPoint.x * tileWidth + tileWidth / 2;
            const y = game.spawnPoint.y * tileHeight + tileHeight / 2;
            
            ctx.save();
            ctx.translate(x, y);
            
            game.portalAngle += 0.02;

            for (let i = 0; i < 3; i++) {
                const radius = 25 + i * 10;
                const speed = (3 - i) * 0.02;
                const angle = game.portalAngle * speed;
                
                const gradient = ctx.createRadialGradient(0, 0, radius - 5, 0, 0, radius + 5);
                gradient.addColorStop(0, 'transparent');
                gradient.addColorStop(0.5, i === 0 ? '#8a2be2' : i === 1 ? '#00bfff' : '#ff00ff');
                gradient.addColorStop(1, 'transparent');
                
                ctx.strokeStyle = gradient;
                ctx.lineWidth = 3;
                ctx.shadowColor = i === 0 ? '#8a2be2' : i === 1 ? '#00bfff' : '#ff00ff';
                ctx.shadowBlur = 15;
                
                ctx.beginPath();
                ctx.arc(0, 0, radius, angle, angle + Math.PI * 1.8);
                ctx.stroke();
            }

            const innerGradient = ctx.createRadialGradient(0, 0, 0, 0, 0, 20);
            innerGradient.addColorStop(0, 'rgba(138, 43, 226, 0.8)');
            innerGradient.addColorStop(0.5, 'rgba(0, 191, 255, 0.4)');
            innerGradient.addColorStop(1, 'transparent');
            
            ctx.fillStyle = innerGradient;
            ctx.beginPath();
            ctx.arc(0, 0, 20, 0, Math.PI * 2);
            ctx.fill();

            for (let i = 0; i < 6; i++) {
                const angle = (i / 6) * Math.PI * 2 + game.portalAngle;
                const len = 25 + Math.sin(Date.now() / 200 + i) * 5;
                ctx.strokeStyle = `rgba(0, 255, 255, ${0.5 + Math.sin(Date.now() / 300 + i) * 0.3})`;
                ctx.lineWidth = 2;
                ctx.beginPath();
                ctx.moveTo(Math.cos(angle) * 15, Math.sin(angle) * 15);
                ctx.lineTo(Math.cos(angle) * len, Math.sin(angle) * len);
                ctx.stroke();
            }

            ctx.restore();
        }

        function renderBase(tileWidth, tileHeight) {
            const x = game.basePoint.x * tileWidth + tileWidth / 2;
            const y = game.basePoint.y * tileHeight + tileHeight / 2;
            
            ctx.save();
            ctx.translate(x, y);

            const healthPercent = game.lives / 20;
            game.baseShieldIntensity = 0.5 + healthPercent * 0.5;

            const shieldRadius = 35 + Math.sin(Date.now() / 500) * 5;
            const shieldGradient = ctx.createRadialGradient(0, 0, shieldRadius * 0.8, 0, 0, shieldRadius);
            shieldGradient.addColorStop(0, `rgba(255, 0, 102, ${0.1 * game.baseShieldIntensity})`);
            shieldGradient.addColorStop(1, 'transparent');
            
            ctx.fillStyle = shieldGradient;
            ctx.beginPath();
            ctx.arc(0, 0, shieldRadius, 0, Math.PI * 2);
            ctx.fill();

            ctx.strokeStyle = healthPercent > 0.5 ? '#00ff00' : healthPercent > 0.25 ? '#ffd700' : '#ff0000';
            ctx.lineWidth = 2;
            ctx.shadowColor = ctx.strokeStyle;
            ctx.shadowBlur = 15 * game.baseShieldIntensity;
            ctx.globalAlpha = 0.5 + Math.sin(Date.now() / 300) * 0.3;
            ctx.beginPath();
            ctx.arc(0, 0, shieldRadius, 0, Math.PI * 2);
            ctx.stroke();
            ctx.globalAlpha = 1;

            ctx.fillStyle = '#2a2a4a';
            ctx.beginPath();
            for (let i = 0; i < 6; i++) {
                const angle = (i / 6) * Math.PI * 2;
                const px = Math.cos(angle) * 20;
                const py = Math.sin(angle) * 20;
                if (i === 0) ctx.moveTo(px, py);
                else ctx.lineTo(px, py);
            }
            ctx.closePath();
            ctx.fill();

            ctx.fillStyle = healthPercent > 0.5 ? '#00ff00' : healthPercent > 0.25 ? '#ffd700' : '#ff0000';
            ctx.shadowColor = ctx.fillStyle;
            ctx.shadowBlur = 20;
            ctx.beginPath();
            ctx.arc(0, 0, 12, 0, Math.PI * 2);
            ctx.fill();

            ctx.restore();
        }

        function toggleMobilePanel() {
            const panel = document.getElementById('mobile-panel');
            panel.classList.toggle('active');
        }

        function switchTab(tab) {
            document.querySelectorAll('.tab-btn').forEach(b => b.classList.remove('active'));
            document.querySelectorAll('.panel-content').forEach(c => c.classList.remove('active'));
            document.querySelector(`.tab-btn:contains("${tab === 'build' ? '建造' : '升级'}")`)?.classList.add('active');
            document.getElementById(`${tab}-tab`)?.classList.add('active');
        }

        document.addEventListener('DOMContentLoaded', initGame);
    </script>
</body>
</html>
