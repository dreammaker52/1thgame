<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>赛博防线 - Cyber Defense</title>
    <style>
        * { margin: 0; padding: 0; box-sizing: border-box; }
        html, body { height: 100%; overflow: hidden; font-family: 'Segoe UI', Arial, sans-serif; }
        body {
            background: linear-gradient(135deg, #0a0a1a 0%, #1a0a2e 50%, #0a1a2a 100%);
            color: #00ffff;
        }

        #game-container {
            width: 100vw;
            height: 100vh;
            display: flex;
            flex-direction: column;
            min-width: 1200px;
        }

        .header {
            background: linear-gradient(90deg, rgba(138, 43, 226, 0.3), rgba(0, 255, 255, 0.2));
            padding: 10px 20px;
            display: flex;
            justify-content: space-between;
            align-items: center;
            border-bottom: 2px solid rgba(0, 255, 255, 0.5);
            box-shadow: 0 0 20px rgba(0, 255, 255, 0.3);
        }

        .header h1 {
            font-size: 24px;
            text-shadow: 0 0 10px #00ffff, 0 0 20px #ff00ff;
            background: linear-gradient(90deg, #00ffff, #ff00ff);
            -webkit-background-clip: text;
            -webkit-text-fill-color: transparent;
        }

        .stats {
            display: flex;
            gap: 30px;
        }

        .stat {
            display: flex;
            align-items: center;
            gap: 8px;
            font-size: 16px;
        }

        .stat-value {
            font-weight: bold;
            color: #ffd700;
            text-shadow: 0 0 5px #ffd700;
        }

        .stat-label {
            color: #888;
        }

        .health-bar {
            width: 150px;
            height: 20px;
            background: rgba(255, 0, 102, 0.3);
            border: 2px solid #ff0066;
            border-radius: 10px;
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
        }

        #game-canvas {
            flex: 1;
            display: block;
            cursor: crosshair;
            image-rendering: pixelated;
            image-rendering: crisp-edges;
        }

        .sidebar {
            width: 340px;
            min-width: 340px;
            background: linear-gradient(180deg, rgba(20, 10, 40, 0.95), rgba(10, 20, 40, 0.95));
            border-left: 2px solid rgba(0, 255, 255, 0.3);
            padding: 15px;
            display: flex;
            flex-direction: column;
            gap: 12px;
            overflow-y: auto;
            flex-shrink: 0;
        }

        .panel {
            background: rgba(0, 0, 0, 0.4);
            border: 1px solid rgba(0, 255, 255, 0.3);
            border-radius: 10px;
            padding: 12px;
        }

        .panel-title {
            font-size: 13px;
            color: #00ffff;
            margin-bottom: 10px;
            text-transform: uppercase;
            letter-spacing: 2px;
            border-bottom: 1px solid rgba(0, 255, 255, 0.3);
            padding-bottom: 8px;
            display: flex;
            align-items: center;
            gap: 8px;
        }

        .panel-title::before {
            content: '';
            width: 8px;
            height: 8px;
            background: #00ffff;
            border-radius: 50%;
            box-shadow: 0 0 10px #00ffff;
        }

        .wave-info {
            text-align: center;
        }

        .wave-number {
            font-size: 42px;
            font-weight: bold;
            background: linear-gradient(90deg, #00ffff, #ff00ff);
            -webkit-background-clip: text;
            -webkit-text-fill-color: transparent;
        }

        .wave-status {
            font-size: 14px;
            color: #ff6600;
            margin-top: 5px;
        }

        .wave-status.resupply {
            color: #00ff00;
            animation: blink 0.5s ease-in-out infinite;
        }

        @keyframes blink {
            0%, 100% { opacity: 1; }
            50% { opacity: 0.5; }
        }

        .resupply-timer {
            font-size: 28px;
            font-weight: bold;
            color: #00ff00;
            text-shadow: 0 0 15px #00ff00;
            margin-top: 5px;
        }

        .enemy-count {
            font-size: 12px;
            color: #888;
        }

        .tower-grid {
            display: grid;
            grid-template-columns: repeat(2, 1fr);
            gap: 10px;
        }

        .tower-card {
            background: linear-gradient(135deg, rgba(30, 20, 60, 0.9), rgba(20, 30, 60, 0.9));
            border: 2px solid rgba(0, 255, 255, 0.3);
            border-radius: 12px;
            padding: 15px;
            cursor: pointer;
            transition: all 0.3s;
            text-align: center;
            position: relative;
            overflow: hidden;
        }

        .tower-card::before {
            content: '';
            position: absolute;
            top: -50%;
            left: -50%;
            width: 200%;
            height: 200%;
            background: linear-gradient(45deg, transparent, rgba(255,255,255,0.1), transparent);
            transform: rotate(45deg);
            transition: all 0.5s;
        }

        .tower-card:hover::before {
            left: 100%;
        }

        .tower-card:hover {
            border-color: #00ffff;
            box-shadow: 0 0 20px rgba(0, 255, 255, 0.4);
            transform: translateY(-3px);
        }

        .tower-card.selected {
            border-color: #ff00ff;
            box-shadow: 0 0 25px rgba(255, 0, 255, 0.6);
        }

        .tower-card.disabled {
            opacity: 0.4;
            cursor: not-allowed;
        }

        .tower-icon-wrapper {
            width: 60px;
            height: 60px;
            margin: 0 auto 10px;
            position: relative;
        }

        .tower-icon-bg {
            width: 100%;
            height: 100%;
            border-radius: 50%;
            display: flex;
            align-items: center;
            justify-content: center;
            position: relative;
        }

        .tower-icon-bg::after {
            content: '';
            position: absolute;
            inset: -3px;
            border-radius: 50%;
            border: 2px solid;
            border-color: inherit;
            animation: pulse 2s ease-in-out infinite;
        }

        @keyframes pulse {
            0%, 100% { opacity: 0.5; transform: scale(1); }
            50% { opacity: 1; transform: scale(1.05); }
        }

        .tower-icon {
            font-size: 28px;
            z-index: 1;
        }

        .tower-name {
            font-size: 13px;
            color: #fff;
            margin-bottom: 5px;
            font-weight: bold;
        }

        .tower-cost {
            font-size: 12px;
            color: #ffd700;
        }

        .tower-stats {
            font-size: 10px;
            color: #888;
            margin-top: 5px;
        }

        .selected-tower-panel {
            display: none;
        }

        .selected-tower-panel.active {
            display: block;
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
            border-radius: 10px;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 24px;
        }

        .selected-tower-info h4 {
            color: #fff;
            margin-bottom: 3px;
        }

        .selected-tower-info p {
            font-size: 11px;
            color: #888;
        }

        .upgrade-level {
            display: flex;
            gap: 8px;
            margin-bottom: 12px;
        }

        .level-pip {
            flex: 1;
            height: 8px;
            background: rgba(255,255,255,0.1);
            border-radius: 4px;
            overflow: hidden;
        }

        .level-pip.filled {
            background: linear-gradient(90deg, #00ffff, #ff00ff);
            box-shadow: 0 0 10px rgba(0, 255, 255, 0.5);
        }

        .upgrade-stats {
            font-size: 12px;
            color: #aaa;
            margin-bottom: 12px;
        }

        .upgrade-stats div {
            display: flex;
            justify-content: space-between;
            padding: 4px 0;
            border-bottom: 1px solid rgba(255,255,255,0.05);
        }

        .upgrade-stats span {
            color: #00ffff;
        }

        .btn {
            padding: 10px 15px;
            border: none;
            border-radius: 8px;
            font-size: 13px;
            font-weight: bold;
            cursor: pointer;
            transition: all 0.3s;
            text-transform: uppercase;
            letter-spacing: 1px;
            flex: 1;
        }

        .btn-primary {
            background: linear-gradient(135deg, #8a2be2, #00bfff);
            color: white;
            box-shadow: 0 0 15px rgba(138, 43, 226, 0.5);
        }

        .btn-primary:hover:not(:disabled) {
            box-shadow: 0 0 25px rgba(138, 43, 226, 0.8);
            transform: translateY(-2px);
        }

        .btn-primary:disabled {
            opacity: 0.5;
            cursor: not-allowed;
        }

        .btn-danger {
            background: linear-gradient(135deg, #ff0066, #ff6600);
            color: white;
        }

        .btn-danger:hover {
            box-shadow: 0 0 20px rgba(255, 0, 102, 0.6);
        }

        .btn-secondary {
            background: linear-gradient(135deg, #333, #555);
            color: #00ffff;
            border: 1px solid rgba(0, 255, 255, 0.5);
        }

        .controls {
            display: flex;
            flex-direction: column;
            gap: 8px;
        }

        .controls .btn-start-wave {
            background: linear-gradient(135deg, #00ff00, #00cc00);
            color: #000;
            font-size: 15px;
            padding: 12px;
        }

        .controls .btn-start-wave:hover:not(:disabled) {
            box-shadow: 0 0 25px rgba(0, 255, 0, 0.6);
        }

        .controls .btn-start-wave:disabled {
            background: linear-gradient(135deg, #333, #555);
            color: #666;
            cursor: not-allowed;
        }

        #start-screen, #game-over-screen {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: radial-gradient(ellipse at center, #1a0a2e 0%, #0a0a1a 100%);
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            z-index: 100;
        }

        #game-over-screen {
            display: none;
        }

        .title {
            font-size: 72px;
            font-weight: bold;
            margin-bottom: 20px;
            background: linear-gradient(90deg, #00ffff, #ff00ff, #00ffff);
            background-size: 200% auto;
            -webkit-background-clip: text;
            -webkit-text-fill-color: transparent;
            animation: gradient 3s linear infinite;
            text-shadow: 0 0 30px rgba(0, 255, 255, 0.5);
        }

        @keyframes gradient {
            0% { background-position: 0% center; }
            100% { background-position: 200% center; }
        }

        .subtitle {
            font-size: 18px;
            color: #888;
            margin-bottom: 40px;
        }

        .difficulty-container {
            display: flex;
            gap: 20px;
            margin-bottom: 30px;
            flex-wrap: wrap;
            justify-content: center;
        }

        .difficulty-card {
            width: 200px;
            padding: 25px 20px;
            border-radius: 15px;
            cursor: pointer;
            transition: all 0.4s ease;
            text-align: center;
            position: relative;
            overflow: hidden;
        }

        .difficulty-card::before {
            content: '';
            position: absolute;
            top: 0;
            left: -100%;
            width: 100%;
            height: 100%;
            background: linear-gradient(90deg, transparent, rgba(255,255,255,0.1), transparent);
            transition: left 0.5s ease;
        }

        .difficulty-card:hover::before {
            left: 100%;
        }

        .difficulty-card:hover {
            transform: translateY(-8px) scale(1.02);
        }

        .difficulty-card.easy {
            background: linear-gradient(135deg, rgba(0, 255, 0, 0.15), rgba(0, 200, 0, 0.08));
            border: 3px solid #00ff00;
            box-shadow: 0 0 20px rgba(0, 255, 0, 0.3), inset 0 0 30px rgba(0, 255, 0, 0.1);
        }

        .difficulty-card.easy:hover {
            box-shadow: 0 0 40px rgba(0, 255, 0, 0.6), inset 0 0 30px rgba(0, 255, 0, 0.15);
        }

        .difficulty-card.normal {
            background: linear-gradient(135deg, rgba(0, 200, 255, 0.15), rgba(0, 150, 200, 0.08));
            border: 3px solid #00c8ff;
            box-shadow: 0 0 20px rgba(0, 200, 255, 0.3), inset 0 0 30px rgba(0, 200, 255, 0.1);
        }

        .difficulty-card.normal:hover {
            box-shadow: 0 0 40px rgba(0, 200, 255, 0.6), inset 0 0 30px rgba(0, 200, 255, 0.15);
        }

        .difficulty-card.hard {
            background: linear-gradient(135deg, rgba(255, 0, 100, 0.15), rgba(200, 0, 50, 0.08));
            border: 3px solid #ff0064;
            box-shadow: 0 0 20px rgba(255, 0, 100, 0.3), inset 0 0 30px rgba(255, 0, 100, 0.1);
        }

        .difficulty-card.hard:hover {
            box-shadow: 0 0 40px rgba(255, 0, 100, 0.6), inset 0 0 30px rgba(255, 0, 100, 0.15);
        }

        .difficulty-icon {
            font-size: 48px;
            margin-bottom: 15px;
            display: block;
        }

        .difficulty-name {
            font-size: 24px;
            font-weight: bold;
            margin-bottom: 10px;
            text-transform: uppercase;
            letter-spacing: 2px;
        }

        .difficulty-card.easy .difficulty-name { color: #00ff00; }
        .difficulty-card.normal .difficulty-name { color: #00c8ff; }
        .difficulty-card.hard .difficulty-name { color: #ff0064; }

        .difficulty-desc {
            font-size: 13px;
            color: #aaa;
            line-height: 1.6;
        }

        .difficulty-card.selected {
            transform: translateY(-10px) scale(1.05);
        }

        .difficulty-card.easy.selected {
            box-shadow: 0 0 50px rgba(0, 255, 0, 0.8), inset 0 0 40px rgba(0, 255, 0, 0.2);
        }

        .difficulty-card.normal.selected {
            box-shadow: 0 0 50px rgba(0, 200, 255, 0.8), inset 0 0 40px rgba(0, 200, 255, 0.2);
        }

        .difficulty-card.hard.selected {
            box-shadow: 0 0 50px rgba(255, 0, 100, 0.8), inset 0 0 40px rgba(255, 0, 100, 0.2);
        }

        #map-select-screen {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: radial-gradient(ellipse at center, #1a0a2e 0%, #0a0a1a 100%);
            display: none;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            z-index: 100;
        }

        .map-container {
            display: flex;
            gap: 25px;
            margin-bottom: 40px;
            flex-wrap: wrap;
            justify-content: center;
        }

        .map-card {
            width: 260px;
            padding: 20px;
            border-radius: 15px;
            cursor: pointer;
            transition: all 0.4s ease;
            text-align: center;
            position: relative;
            overflow: hidden;
            border: 3px solid transparent;
        }

        .map-card::before {
            content: '';
            position: absolute;
            top: 0;
            left: 0;
            right: 0;
            bottom: 0;
            border-radius: 15px;
            background: linear-gradient(135deg, rgba(0,0,0,0.6), rgba(0,0,0,0.2));
            z-index: 0;
        }

        .map-card > * {
            position: relative;
            z-index: 1;
        }

        .map-card:hover {
            transform: translateY(-8px) scale(1.02);
        }

        .map-card.map1 {
            background: linear-gradient(135deg, rgba(138,43,226,0.15), rgba(0,191,255,0.08));
            border-color: #8a2be2;
            box-shadow: 0 0 20px rgba(138,43,226,0.3), inset 0 0 30px rgba(138,43,226,0.1);
        }

        .map-card.map1:hover {
            box-shadow: 0 0 40px rgba(138,43,226,0.6), inset 0 0 30px rgba(138,43,226,0.15);
        }

        .map-card.map2 {
            background: linear-gradient(135deg, rgba(255,107,53,0.15), rgba(128,128,128,0.08));
            border-color: #ff6b35;
            box-shadow: 0 0 20px rgba(255,107,53,0.3), inset 0 0 30px rgba(255,107,53,0.1);
        }

        .map-card.map2:hover {
            box-shadow: 0 0 40px rgba(255,107,53,0.6), inset 0 0 30px rgba(255,107,53,0.15);
        }

        .map-card.map3 {
            background: linear-gradient(135deg, rgba(0,255,255,0.15), rgba(255,255,255,0.08));
            border-color: #00ffff;
            box-shadow: 0 0 20px rgba(0,255,255,0.3), inset 0 0 30px rgba(0,255,255,0.1);
        }

        .map-card.map3:hover {
            box-shadow: 0 0 40px rgba(0,255,255,0.6), inset 0 0 30px rgba(0,255,255,0.15);
        }

        .map-thumbnail {
            width: 200px;
            height: 120px;
            margin: 0 auto 15px;
            border-radius: 10px;
            display: flex;
            align-items: center;
            justify-content: center;
            position: relative;
            overflow: hidden;
            border: 2px solid rgba(255,255,255,0.2);
        }

        .map-thumbnail.map1 { background: linear-gradient(135deg, #1a0a2e, #0a1a2e); }
        .map-thumbnail.map2 { background: linear-gradient(135deg, #2e1a0a, #1a1a1a); }
        .map-thumbnail.map3 { background: linear-gradient(135deg, #0a2e2e, #0a1a1a); }

        .map-thumbnail-path {
            position: absolute;
            width: 100%;
            height: 100%;
            opacity: 0.3;
        }

        .map-name {
            font-size: 20px;
            font-weight: bold;
            margin-bottom: 8px;
            text-transform: uppercase;
            letter-spacing: 2px;
        }

        .map-card.map1 .map-name { color: #8a2be2; }
        .map-card.map2 .map-name { color: #ff6b35; }
        .map-card.map3 .map-name { color: #00ffff; }

        .map-desc {
            font-size: 12px;
            color: #aaa;
            line-height: 1.5;
        }

        .map-card.selected {
            transform: translateY(-10px) scale(1.03);
        }

        .map-card.map1.selected {
            box-shadow: 0 0 50px rgba(138,43,226,0.8), inset 0 0 40px rgba(138,43,226,0.2);
        }

        .map-card.map2.selected {
            box-shadow: 0 0 50px rgba(255,107,53,0.8), inset 0 0 40px rgba(255,107,53,0.2);
        }

        .map-card.map3.selected {
            box-shadow: 0 0 50px rgba(0,255,255,0.8), inset 0 0 40px rgba(0,255,255,0.2);
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

        .start-btn {
            padding: 20px 60px;
            font-size: 20px;
            background: linear-gradient(135deg, #8a2be2, #00bfff);
            color: white;
            border: none;
            border-radius: 50px;
            cursor: pointer;
            transition: all 0.3s;
            text-transform: uppercase;
            letter-spacing: 3px;
            box-shadow: 0 0 30px rgba(138, 43, 226, 0.6);
        }

        .start-btn:hover:not(:disabled) {
            transform: scale(1.1);
            box-shadow: 0 0 50px rgba(138, 43, 226, 0.9);
        }

        .start-btn:disabled {
            opacity: 0.5;
            cursor: not-allowed;
        }

        .game-over-title {
            font-size: 48px;
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
            font-size: 18px;
            color: #aaa;
            margin-bottom: 30px;
            text-align: center;
        }

        .quick-buy-hint {
            font-size: 10px;
            color: #666;
            text-align: center;
            margin-top: 8px;
        }

        ::-webkit-scrollbar { width: 4px; }
        ::-webkit-scrollbar-track { background: rgba(0,0,0,0.2); }
        ::-webkit-scrollbar-thumb { background: rgba(0, 255, 255, 0.5); border-radius: 2px; }
    </style>
</head>
<body>
    <div id="start-screen">
        <div class="title">CYBER DEFENSE</div>
        <div class="subtitle">赛博防线 - 守护未来城市</div>
        <div class="difficulty-container">
            <div class="difficulty-card easy" onclick="selectDifficulty('easy')">
                <span class="difficulty-icon">🌱</span>
                <div class="difficulty-name">EASY</div>
                <div class="difficulty-desc">适合新手<br>敌人数量减少30%<br>血量降低25%<br>资源获取增加20%</div>
            </div>
            <div class="difficulty-card normal" onclick="selectDifficulty('normal')">
                <span class="difficulty-icon">⚡</span>
                <div class="difficulty-name">NORMAL</div>
                <div class="difficulty-desc">标准难度<br>敌人属性正常<br>资源获取正常<br>适合有经验的玩家</div>
            </div>
            <div class="difficulty-card hard" onclick="selectDifficulty('hard')">
                <span class="difficulty-icon">💀</span>
                <div class="difficulty-name">HARD</div>
                <div class="difficulty-desc">极高挑战<br>敌人数量增加40%<br>血量增加35%<br>资源获取减少15%</div>
            </div>
        </div>
        <button class="start-btn" id="start-game-btn" onclick="goToMapSelect()" disabled>请选择难度</button>
    </div>

    <div id="map-select-screen">
        <button class="back-btn" onclick="goBackToDifficulty()">← 返回选择难度</button>
        <div class="title">选择战场</div>
        <div class="subtitle">选择你的防御阵地</div>
        <div class="map-container">
            <div class="map-card map1" onclick="selectMap(0)">
                <div class="map-thumbnail map1">
                    <div class="map-thumbnail-path" id="map1-preview"></div>
                </div>
                <div class="map-name">Neon Metropolis</div>
                <div class="map-desc">霓虹都市<br>紫蓝主色调，高楼林立<br>路径弯曲复杂</div>
            </div>
            <div class="map-card map2" onclick="selectMap(1)">
                <div class="map-thumbnail map2">
                    <div class="map-thumbnail-path" id="map2-preview"></div>
                </div>
                <div class="map-name">Cyber Wasteland</div>
                <div class="map-desc">赛博废土<br>橙红暗灰主色调<br>路径长且有分支</div>
            </div>
            <div class="map-card map3" onclick="selectMap(2)">
                <div class="map-thumbnail map3">
                    <div class="map-thumbnail-path" id="map3-preview"></div>
                </div>
                <div class="map-name">Orbital Station</div>
                <div class="map-desc">轨道空间站<br>青白主色调<br>路径含环形结构</div>
            </div>
        </div>
        <button class="start-btn" id="start-game-from-map-btn" onclick="startGameFromMap()" disabled>请选择地图</button>
    </div>

    <div id="game-over-screen">
        <div class="game-over-title" id="game-over-title">游戏结束</div>
        <div class="final-stats" id="final-stats"></div>
        <button class="start-btn" onclick="restartGame()">再来一局</button>
    </div>

    <div id="game-container">
        <div class="header">
            <h1>赛博防线</h1>
            <div class="stats">
                <div class="stat">
                    <span class="stat-label">能量:</span>
                    <span class="stat-value" id="credits">500</span>
                </div>
                <div class="stat">
                    <span class="stat-label">基地:</span>
                    <div class="health-bar">
                        <div class="health-fill" id="health-fill" style="width: 100%;"></div>
                    </div>
                </div>
                <div class="stat">
                    <span class="stat-label">得分:</span>
                    <span class="stat-value" id="score">0</span>
                </div>
            </div>
        </div>

        <div class="main-content">
            <canvas id="game-canvas"></canvas>

            <div class="sidebar">
                <div class="panel wave-info">
                    <div class="panel-title">波次信息</div>
                    <div class="wave-number" id="wave-num">0</div>
                    <div class="wave-status" id="wave-status">准备中</div>
                    <div class="resupply-timer" id="resupply-timer"></div>
                    <div class="enemy-count" id="enemy-count"></div>
                </div>

                <div class="panel">
                    <div class="panel-title">建造武器</div>
                    <div class="tower-grid" id="tower-grid"></div>
                </div>

                <div class="panel selected-tower-panel" id="upgrade-panel">
                    <div class="panel-title">武器详情</div>
                    <div class="selected-tower-header">
                        <div class="selected-tower-icon" id="selected-icon"></div>
                        <div class="selected-tower-info">
                            <h4 id="selected-name">激光塔</h4>
                            <p id="selected-desc">高射速激光武器</p>
                        </div>
                    </div>
                    <div class="upgrade-level" id="level-pips">
                        <div class="level-pip filled"></div>
                        <div class="level-pip"></div>
                        <div class="level-pip"></div>
                        <div class="level-pip"></div>
                    </div>
                    <div class="upgrade-stats" id="upgrade-stats">
                        <div>伤害 <span>25</span></div>
                        <div>范围 <span>120</span></div>
                        <div>射速 <span>快速</span></div>
                    </div>
                    <div style="display: flex; gap: 8px;">
                        <button class="btn btn-primary" id="upgrade-btn" onclick="upgradeTower()">升级</button>
                        <button class="btn btn-danger" onclick="sellTower()">出售</button>
                    </div>
                </div>

                <div class="panel controls">
                    <button class="btn btn-primary btn-start-wave" id="wave-btn" onclick="startNextWave()">开始第一波</button>
                    <button class="btn btn-secondary" id="pause-btn" onclick="togglePause()">暂停</button>
                </div>
            </div>
        </div>
    </div>

    <script>
        const TILE_SIZE = 50;
        const GRID_WIDTH = 18;
        const GRID_HEIGHT = 12;
        const RESUPPLY_TIME = 5;

        const MAPS = [
            {
                name: 'Neon Metropolis',
                bgColor: '#0a0a1a',
                pathColor: 'rgba(138,43,226,0.3)',
                pathBorderColor: '#8a2be2',
                groundColor1: 'rgba(40,40,70,0.4)',
                groundColor2: 'rgba(0,191,255,0.1)',
                decorations: 'city',
                path: [
                    { x: 0, y: 6 }, { x: 1, y: 6 }, { x: 2, y: 6 },
                    { x: 2, y: 5 }, { x: 2, y: 4 }, { x: 2, y: 3 },
                    { x: 3, y: 3 }, { x: 4, y: 3 }, { x: 5, y: 3 },
                    { x: 5, y: 4 }, { x: 5, y: 5 }, { x: 5, y: 6 }, { x: 5, y: 7 },
                    { x: 6, y: 7 }, { x: 7, y: 7 }, { x: 8, y: 7 }, { x: 9, y: 7 },
                    { x: 9, y: 6 }, { x: 9, y: 5 }, { x: 9, y: 4 },
                    { x: 10, y: 4 }, { x: 11, y: 4 }, { x: 12, y: 4 },
                    { x: 12, y: 5 }, { x: 12, y: 6 }, { x: 12, y: 7 }, { x: 12, y: 8 },
                    { x: 13, y: 8 }, { x: 14, y: 8 }, { x: 15, y: 8 }, { x: 16, y: 8 }, { x: 17, y: 8 }
                ]
            },
            {
                name: 'Cyber Wasteland',
                bgColor: '#1a1a0a',
                pathColor: 'rgba(255,107,53,0.3)',
                pathBorderColor: '#ff6b35',
                groundColor1: 'rgba(70,60,40,0.4)',
                groundColor2: 'rgba(255,107,53,0.1)',
                decorations: 'wasteland',
                path: [
                    { x: 0, y: 3 }, { x: 1, y: 3 }, { x: 2, y: 3 }, { x: 3, y: 3 }, { x: 4, y: 3 },
                    { x: 4, y: 4 }, { x: 4, y: 5 }, { x: 4, y: 6 },
                    { x: 5, y: 6 }, { x: 6, y: 6 }, { x: 7, y: 6 },
                    { x: 7, y: 5 }, { x: 7, y: 4 }, { x: 7, y: 3 },
                    { x: 8, y: 3 }, { x: 9, y: 3 }, { x: 10, y: 3 },
                    { x: 10, y: 4 }, { x: 10, y: 5 }, { x: 10, y: 6 }, { x: 10, y: 7 }, { x: 10, y: 8 }, { x: 10, y: 9 },
                    { x: 11, y: 9 }, { x: 12, y: 9 }, { x: 13, y: 9 }, { x: 14, y: 9 }, { x: 15, y: 9 }, { x: 16, y: 9 }, { x: 17, y: 9 }
                ]
            },
            {
                name: 'Orbital Station',
                bgColor: '#0a1a1a',
                pathColor: 'rgba(0,255,255,0.3)',
                pathBorderColor: '#00ffff',
                groundColor1: 'rgba(40,70,70,0.4)',
                groundColor2: 'rgba(0,255,255,0.1)',
                decorations: 'station',
                path: [
                    { x: 0, y: 5 }, { x: 1, y: 5 }, { x: 2, y: 5 },
                    { x: 2, y: 4 }, { x: 2, y: 3 },
                    { x: 3, y: 3 }, { x: 4, y: 3 }, { x: 5, y: 3 }, { x: 6, y: 3 },
                    { x: 6, y: 4 }, { x: 6, y: 5 }, { x: 6, y: 6 },
                    { x: 5, y: 6 }, { x: 4, y: 6 }, { x: 3, y: 6 },
                    { x: 3, y: 7 }, { x: 3, y: 8 },
                    { x: 4, y: 8 }, { x: 5, y: 8 }, { x: 6, y: 8 }, { x: 7, y: 8 }, { x: 8, y: 8 },
                    { x: 8, y: 7 }, { x: 8, y: 6 }, { x: 8, y: 5 },
                    { x: 9, y: 5 }, { x: 10, y: 5 }, { x: 11, y: 5 }, { x: 12, y: 5 }, { x: 13, y: 5 }, { x: 14, y: 5 }, { x: 15, y: 5 }, { x: 16, y: 5 }, { x: 17, y: 5 }
                ]
            }
        ];

        const DIFFICULTY_CONFIG = {
            easy: {
                name: '简单',
                enemyCountMult: 0.7,
                enemyHealthMult: 0.75,
                enemySpeedMult: 0.9,
                resourceMult: 1.2
            },
            normal: {
                name: '普通',
                enemyCountMult: 1.0,
                enemyHealthMult: 1.0,
                enemySpeedMult: 1.0,
                resourceMult: 1.0
            },
            hard: {
                name: '困难',
                enemyCountMult: 1.4,
                enemyHealthMult: 1.35,
                enemySpeedMult: 1.15,
                resourceMult: 0.85
            }
        };

        const TOWER_TYPES = {
            laser: {
                name: '激光塔',
                icon: '⚡',
                cost: 100,
                damage: 25,
                range: 120,
                fireRate: 500,
                color: '#00ffff',
                glowColor: 'rgba(0, 255, 255, 0.5)',
                description: '高射速激光武器',
                projectileType: 'beam',
                upgrades: [
                    { cost: 80, damageBonus: 15, rangeBonus: 15, fireRateBonus: 0, sizeBonus: 3 },
                    { cost: 150, damageBonus: 25, rangeBonus: 20, fireRateBonus: -50, sizeBonus: 4 },
                    { cost: 250, damageBonus: 40, rangeBonus: 25, fireRateBonus: -80, sizeBonus: 5 }
                ]
            },
            missile: {
                name: '导弹塔',
                icon: '🚀',
                cost: 150,
                damage: 60,
                range: 150,
                fireRate: 1200,
                color: '#ff6600',
                glowColor: 'rgba(255, 102, 0, 0.5)',
                splash: 45,
                description: '范围伤害导弹',
                projectileType: 'missile',
                upgrades: [
                    { cost: 100, damageBonus: 35, rangeBonus: 20, fireRateBonus: -100, sizeBonus: 4 },
                    { cost: 200, damageBonus: 55, rangeBonus: 25, fireRateBonus: -150, sizeBonus: 5 },
                    { cost: 350, damageBonus: 80, rangeBonus: 35, fireRateBonus: -200, sizeBonus: 6 }
                ]
            },
            emp: {
                name: '电磁塔',
                icon: '💫',
                cost: 200,
                damage: 15,
                range: 110,
                fireRate: 800,
                color: '#ff00ff',
                glowColor: 'rgba(255, 0, 255, 0.5)',
                slow: 0.5,
                slowDuration: 2000,
                description: '减速敌人 + 侦测隐形',
                projectileType: 'pulse',
                upgrades: [
                    { cost: 120, damageBonus: 12, rangeBonus: 20, fireRateBonus: -80, sizeBonus: 3 },
                    { cost: 220, damageBonus: 20, rangeBonus: 30, fireRateBonus: -100, sizeBonus: 4 },
                    { cost: 380, damageBonus: 35, rangeBonus: 40, fireRateBonus: -150, sizeBonus: 5 }
                ]
            },
            plasma: {
                name: '等离子塔',
                icon: '🔥',
                cost: 250,
                damage: 100,
                range: 100,
                fireRate: 1500,
                color: '#00ff00',
                glowColor: 'rgba(0, 255, 0, 0.5)',
                description: '超高伤害等离子炮',
                projectileType: 'plasma',
                upgrades: [
                    { cost: 150, damageBonus: 60, rangeBonus: 15, fireRateBonus: -150, sizeBonus: 5 },
                    { cost: 280, damageBonus: 100, rangeBonus: 20, fireRateBonus: -200, sizeBonus: 6 },
                    { cost: 450, damageBonus: 150, rangeBonus: 30, fireRateBonus: -250, sizeBonus: 8 }
                ]
            }
        };

        const ENEMY_TYPES = {
            drone: { name: '无人机', baseHealth: 50, baseSpeed: 2, reward: 15, color: '#666666', size: 12 },
            cyborg: { name: '生化人', baseHealth: 120, baseSpeed: 1.5, reward: 25, color: '#00ff00', size: 15 },
            tank: { name: '重装甲', baseHealth: 300, baseSpeed: 0.8, reward: 50, color: '#ff6600', size: 18 },
            stealth: { name: '隐形者', baseHealth: 80, baseSpeed: 2.5, reward: 35, color: '#8888ff', size: 14, stealth: true },
            boss: { name: 'BOSS', baseHealth: 1000, baseSpeed: 0.5, reward: 200, color: '#ff0000', size: 25 }
        };

        const WAVES = [
            { enemies: [{ type: 'drone', count: 5 }] },
            { enemies: [{ type: 'drone', count: 8 }] },
            { enemies: [{ type: 'drone', count: 6 }, { type: 'cyborg', count: 3 }] },
            { enemies: [{ type: 'cyborg', count: 6 }] },
            { enemies: [{ type: 'drone', count: 10 }, { type: 'cyborg', count: 5 }, { type: 'tank', count: 2 }] },
            { enemies: [{ type: 'cyborg', count: 8 }, { type: 'tank', count: 3 }] },
            { enemies: [{ type: 'stealth', count: 6 }, { type: 'drone', count: 8 }] },
            { enemies: [{ type: 'tank', count: 4 }, { type: 'cyborg', count: 6 }] },
            { enemies: [{ type: 'stealth', count: 8 }, { type: 'tank', count: 3 }, { type: 'cyborg', count: 5 }] },
            { enemies: [{ type: 'boss', count: 1 }, { type: 'drone', count: 15 }, { type: 'cyborg', count: 5 }] },
            { enemies: [{ type: 'boss', count: 2 }, { type: 'tank', count: 5 }] },
            { enemies: [{ type: 'stealth', count: 10 }, { type: 'cyborg', count: 8 }, { type: 'tank', count: 4 }] }
        ];

        let game = {
            canvas: null, ctx: null,
            credits: 500, health: 20, maxHealth: 20, score: 0,
            wave: 0, waveInProgress: false,
            resupplyCountdown: 0, resupplyTimerId: null,
            gameOver: false, victory: false,
            selectedTowerType: null, selectedTower: null,
            towers: [], enemies: [], projectiles: [], particles: [],
            grid: [], path: [], lastTime: 0,
            spawnQueue: [], spawnIntervalId: null,
            totalEnemiesInWave: 0, spawnedCount: 0, killedCount: 0,
            difficulty: null,
            paused: false,
            portalAngle: 0,
            portalParticles: [],
            baseShieldIntensity: 1,
            currentMap: 0
        };

        let selectedDifficulty = null;
        let selectedMap = null;

        function selectDifficulty(difficulty) {
            selectedDifficulty = difficulty;
            document.querySelectorAll('.difficulty-card').forEach(card => {
                card.classList.remove('selected');
            });
            document.querySelector('.difficulty-card.' + difficulty).classList.add('selected');
            document.getElementById('start-game-btn').disabled = false;
            document.getElementById('start-game-btn').textContent = '继续选择地图';
        }

        function goToMapSelect() {
            if (!selectedDifficulty) return;
            document.getElementById('start-screen').style.display = 'none';
            document.getElementById('map-select-screen').style.display = 'flex';
            drawMapPreviews();
        }

        function goBackToDifficulty() {
            document.getElementById('map-select-screen').style.display = 'none';
            document.getElementById('start-screen').style.display = 'flex';
            selectedMap = null;
            document.querySelectorAll('.map-card').forEach(card => {
                card.classList.remove('selected');
            });
            document.getElementById('start-game-from-map-btn').disabled = true;
            document.getElementById('start-game-from-map-btn').textContent = '请选择地图';
        }

        function selectMap(mapIndex) {
            selectedMap = mapIndex;
            document.querySelectorAll('.map-card').forEach(card => {
                card.classList.remove('selected');
            });
            document.querySelectorAll('.map-card')[mapIndex].classList.add('selected');
            document.getElementById('start-game-from-map-btn').disabled = false;
            document.getElementById('start-game-from-map-btn').textContent = '开始游戏';
        }

        function drawMapPreviews() {
            MAPS.forEach((mapData, index) => {
                const previewId = 'map' + (index + 1) + '-preview';
                const previewDiv = document.getElementById(previewId);
                
                // 清除之前的 canvas
                while (previewDiv.firstChild) {
                    previewDiv.removeChild(previewDiv.firstChild);
                }
                
                const previewCanvas = document.createElement('canvas');
                previewCanvas.width = 200;
                previewCanvas.height = 120;
                const ctx = previewCanvas.getContext('2d');
                
                ctx.fillStyle = mapData.bgColor;
                ctx.fillRect(0, 0, 200, 120);
                
                const scaleX = 200 / (GRID_WIDTH * TILE_SIZE);
                const scaleY = 120 / (GRID_HEIGHT * TILE_SIZE);
                const scale = Math.min(scaleX, scaleY);
                const offsetX = (200 - GRID_WIDTH * TILE_SIZE * scale) / 2;
                const offsetY = (120 - GRID_HEIGHT * TILE_SIZE * scale) / 2;
                
                ctx.save();
                ctx.translate(offsetX, offsetY);
                ctx.scale(scale, scale);
                
                ctx.strokeStyle = mapData.pathBorderColor;
                ctx.lineWidth = 8;
                ctx.lineCap = 'round';
                ctx.lineJoin = 'round';
                ctx.beginPath();
                mapData.path.forEach((p, i) => {
                    const px = p.x * TILE_SIZE + TILE_SIZE / 2;
                    const py = p.y * TILE_SIZE + TILE_SIZE / 2;
                    if (i === 0) ctx.moveTo(px, py);
                    else ctx.lineTo(px, py);
                });
                ctx.stroke();
                
                ctx.restore();
                
                previewDiv.appendChild(previewCanvas);
            });
        }

        function startGameFromMap() {
            if (!selectedDifficulty || selectedMap === null) return;
            
            document.getElementById('map-select-screen').style.display = 'none';
            game.difficulty = selectedDifficulty;
            game.currentMap = selectedMap;
            const diffConfig = DIFFICULTY_CONFIG[game.difficulty];
            
            game.canvas = document.getElementById('game-canvas');
            game.ctx = game.canvas.getContext('2d');
            game.canvas.width = GRID_WIDTH * TILE_SIZE;
            game.canvas.height = GRID_HEIGHT * TILE_SIZE;
            game.credits = Math.floor(500 * diffConfig.resourceMult);
            game.health = 20;
            game.maxHealth = 20;
            game.score = 0;
            game.wave = 0;
            game.waveInProgress = false;
            game.gameOver = false;
            game.victory = false;
            game.towers = [];
            game.enemies = [];
            game.projectiles = [];
            game.particles = [];
            game.spawnQueue = [];
            game.spawnedCount = 0;
            game.killedCount = 0;
            game.paused = false;
            
            initMap();
            initTowerButtons();
            game.canvas.addEventListener('click', handleCanvasClick);
            game.canvas.addEventListener('mousemove', handleCanvasHover);
            
            // 显示游戏界面
            document.getElementById('game-container').style.display = 'flex';
            
            updateUI();
            requestAnimationFrame(gameLoop);
        }

        function getWaveDifficulty(waveNum) {
            const waveMult = 1 + (waveNum - 1) * 0.15;
            const diffConfig = DIFFICULTY_CONFIG[game.difficulty];
            
            return {
                healthMult: waveMult * diffConfig.enemyHealthMult,
                speedMult: Math.min((1 + (waveNum - 1) * 0.05) * diffConfig.enemySpeedMult, 1.8),
                rewardMult: waveMult * diffConfig.resourceMult,
                countMult: diffConfig.enemyCountMult
            };
        }

        function initMap() {
            game.grid = [];
            for (let y = 0; y < GRID_HEIGHT; y++) {
                game.grid[y] = [];
                for (let x = 0; x < GRID_WIDTH; x++) {
                    game.grid[y][x] = 0;
                }
            }

            const mapData = MAPS[game.currentMap];
            game.path = [...mapData.path];

            game.path.forEach(p => {
                if (p.x >= 0 && p.x < GRID_WIDTH && p.y >= 0 && p.y < GRID_HEIGHT) {
                    game.grid[p.y][p.x] = 1;
                }
            });
        }

        function initTowerButtons() {
            const grid = document.getElementById('tower-grid');
            grid.innerHTML = '';

            Object.entries(TOWER_TYPES).forEach(([type, tower]) => {
                const card = document.createElement('div');
                card.className = 'tower-card';
                card.id = `tower-${type}`;
                card.innerHTML = `
                    <div class="tower-icon-wrapper">
                        <div class="tower-icon-bg" style="background: radial-gradient(circle, ${tower.color}33, transparent); border-color: ${tower.color};">
                            <span class="tower-icon">${tower.icon}</span>
                        </div>
                    </div>
                    <div class="tower-name">${tower.name}</div>
                    <div class="tower-cost">💰 ${tower.cost}</div>
                    <div class="tower-stats">伤害 ${tower.damage} | 范围 ${tower.range}</div>
                `;
                card.onclick = () => selectTowerType(type);
                grid.appendChild(card);
            });
        }

        function selectTowerType(type) {
            if (game.credits < TOWER_TYPES[type].cost) return;
            game.selectedTower = null;
            document.getElementById('upgrade-panel').classList.remove('active');

            if (game.selectedTowerType === type) {
                game.selectedTowerType = null;
                document.querySelectorAll('.tower-card').forEach(c => c.classList.remove('selected'));
            } else {
                game.selectedTowerType = type;
                document.querySelectorAll('.tower-card').forEach(c => c.classList.remove('selected'));
                document.getElementById(`tower-${type}`).classList.add('selected');
            }
        }

        let hoverTile = null;

        function handleCanvasHover(e) {
            const rect = game.canvas.getBoundingClientRect();
            const x = Math.floor((e.clientX - rect.left) / TILE_SIZE);
            const y = Math.floor((e.clientY - rect.top) / TILE_SIZE);
            hoverTile = (x >= 0 && x < GRID_WIDTH && y >= 0 && y < GRID_HEIGHT) ? { x, y } : null;
        }

        function handleCanvasClick(e) {
            const rect = game.canvas.getBoundingClientRect();
            const x = Math.floor((e.clientX - rect.left) / TILE_SIZE);
            const y = Math.floor((e.clientY - rect.top) / TILE_SIZE);

            if (x < 0 || x >= GRID_WIDTH || y < 0 || y >= GRID_HEIGHT) return;

            const clickedTower = game.towers.find(t => t.x === x && t.y === y);

            if (game.selectedTowerType && game.grid[y][x] === 0) {
                const towerData = TOWER_TYPES[game.selectedTowerType];
                if (game.credits >= towerData.cost) {
                    game.credits -= towerData.cost;
                    const newTower = {
                        x, y,
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
                    game.grid[y][x] = 2;
                    createParticles(x * TILE_SIZE + TILE_SIZE/2, y * TILE_SIZE + TILE_SIZE/2, towerData.color, 20);
                    updateUI();
                }
            } else if (clickedTower) {
                game.selectedTower = clickedTower;
                game.selectedTowerType = null;
                document.querySelectorAll('.tower-card').forEach(c => c.classList.remove('selected'));
                showTowerInfo(clickedTower);
            } else {
                game.selectedTower = null;
                document.getElementById('upgrade-panel').classList.remove('active');
            }
        }

        function showTowerInfo(tower) {
            const towerData = TOWER_TYPES[tower.type];
            const panel = document.getElementById('upgrade-panel');
            const icon = document.getElementById('selected-icon');
            const name = document.getElementById('selected-name');
            const desc = document.getElementById('selected-desc');
            const pips = document.getElementById('level-pips');
            const stats = document.getElementById('upgrade-stats');
            const upgradeBtn = document.getElementById('upgrade-btn');

            icon.style.background = `radial-gradient(circle, ${towerData.color}44, transparent)`;
            icon.style.border = `2px solid ${towerData.color}`;
            icon.textContent = towerData.icon;
            name.textContent = `${towerData.name} Lv.${tower.level + 1}`;
            desc.textContent = towerData.description;

            let pipsHTML = '';
            for (let i = 0; i < 4; i++) {
                pipsHTML += `<div class="level-pip ${i <= tower.level ? 'filled' : ''}"></div>`;
            }
            pips.innerHTML = pipsHTML;

            const canUpgrade = tower.level < 3;
            const upgradeCost = canUpgrade ? towerData.upgrades[tower.level].cost : 0;
            const fireRateText = tower.fireRate < 400 ? '极快' : tower.fireRate < 800 ? '快速' : tower.fireRate < 1200 ? '中速' : '慢速';

            stats.innerHTML = `
                <div>伤害 <span>${tower.damage}</span></div>
                <div>范围 <span>${tower.range}</span></div>
                <div>射速 <span>${fireRateText}</span></div>
            `;

            if (canUpgrade) {
                stats.innerHTML += `<div style="margin-top: 8px; color: #ffd700;">升级费用: ${upgradeCost}</div>`;
            } else {
                stats.innerHTML += `<div style="margin-top: 8px; color: #00ff00;">已达满级</div>`;
            }

            upgradeBtn.disabled = !canUpgrade || game.credits < upgradeCost;
            upgradeBtn.textContent = canUpgrade ? `升级 Lv.${tower.level + 2}` : '已满级';

            panel.classList.add('active');
        }

        function upgradeTower() {
            if (!game.selectedTower) return;
            const tower = game.selectedTower;
            const towerData = TOWER_TYPES[tower.type];

            if (tower.level >= towerData.upgrades.length) return;
            const upgrade = towerData.upgrades[tower.level];
            if (game.credits < upgrade.cost) return;

            game.credits -= upgrade.cost;
            tower.level++;
            tower.damage += upgrade.damageBonus;
            tower.range += upgrade.rangeBonus;
            tower.fireRate += upgrade.fireRateBonus;

            createParticles(tower.x * TILE_SIZE + TILE_SIZE/2, tower.y * TILE_SIZE + TILE_SIZE/2, '#ffd700', 30);
            showTowerInfo(tower);
            updateUI();
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

            document.getElementById('upgrade-panel').classList.remove('active');
            createParticles(tower.x * TILE_SIZE + TILE_SIZE/2, tower.y * TILE_SIZE + TILE_SIZE/2, '#ff0000', 20);
            updateUI();
        }

        function clearSpawnInterval() {
            if (game.spawnIntervalId) {
                clearInterval(game.spawnIntervalId);
                game.spawnIntervalId = null;
            }
        }

        function clearResupplyTimer() {
            if (game.resupplyTimerId) {
                clearInterval(game.resupplyTimerId);
                game.resupplyTimerId = null;
            }
        }

        function startNextWave() {
            if (game.waveInProgress || game.gameOver) return;
            if (game.wave >= WAVES.length) return;

            clearSpawnInterval();
            clearResupplyTimer();

            game.wave++;
            game.waveInProgress = true;
            game.resupplyCountdown = 0;

            const waveData = WAVES[game.wave - 1];
            const difficulty = getWaveDifficulty(game.wave);

            game.spawnQueue = [];
            waveData.enemies.forEach(e => {
                const adjustedCount = Math.ceil(e.count * difficulty.countMult);
                for (let i = 0; i < adjustedCount; i++) {
                    game.spawnQueue.push(e.type);
                }
            });

            for (let i = game.spawnQueue.length - 1; i > 0; i--) {
                const j = Math.floor(Math.random() * (i + 1));
                [game.spawnQueue[i], game.spawnQueue[j]] = [game.spawnQueue[j], game.spawnQueue[i]];
            }

            game.totalEnemiesInWave = game.spawnQueue.length;
            game.spawnedCount = 0;
            game.killedCount = 0;

            game.spawnIntervalId = setInterval(() => {
                if (game.spawnedCount >= game.totalEnemiesInWave || game.gameOver) {
                    clearSpawnInterval();
                    return;
                }
                spawnEnemy(game.spawnQueue[game.spawnedCount], difficulty);
                game.spawnedCount++;
                updateWaveUI();
            }, 700);

            document.getElementById('wave-btn').disabled = true;
            updateUI();
        }

        function spawnEnemy(type, difficulty) {
            const enemyData = ENEMY_TYPES[type];
            const startPos = game.path[0];
            const health = Math.floor(enemyData.baseHealth * difficulty.healthMult);
            const speed = enemyData.baseSpeed * difficulty.speedMult;
            const reward = Math.floor(enemyData.reward * difficulty.rewardMult);

            game.enemies.push({
                type,
                x: startPos.x * TILE_SIZE + TILE_SIZE / 2,
                y: startPos.y * TILE_SIZE + TILE_SIZE / 2,
                health: health,
                maxHealth: health,
                speed: speed,
                baseSpeed: enemyData.baseSpeed,
                reward: reward,
                pathIndex: 0,
                slowedUntil: 0,
                stealth: enemyData.stealth || false,
                angle: 0,
                waveHealth: health,
                teleporting: true,
                teleportProgress: 0
            });
        }

        function startResupplyCountdown() {
            if (game.wave >= WAVES.length) {
                endGame(true);
                return;
            }

            game.resupplyCountdown = RESUPPLY_TIME;
            document.getElementById('wave-status').textContent = '补给时间';
            document.getElementById('wave-status').className = 'wave-status resupply';
            document.getElementById('resupply-timer').textContent = game.resupplyCountdown;

            game.resupplyTimerId = setInterval(() => {
                game.resupplyCountdown--;
                document.getElementById('resupply-timer').textContent = game.resupplyCountdown;

                if (game.resupplyCountdown <= 0) {
                    clearResupplyTimer();
                    document.getElementById('resupply-timer').textContent = '';
                    document.getElementById('wave-status').className = 'wave-status';
                    startNextWave();
                }
            }, 1000);
        }

        function updateWaveUI() {
            const remaining = game.totalEnemiesInWave - game.killedCount;
            const statusEl = document.getElementById('wave-status');
            const countEl = document.getElementById('enemy-count');

            if (game.waveInProgress) {
                statusEl.textContent = `第 ${game.wave} 波 - ${DIFFICULTY_CONFIG[game.difficulty].name}`;
                statusEl.className = 'wave-status';
                countEl.textContent = `敌人: ${remaining}/${game.totalEnemiesInWave}`;
            }
        }

        function togglePause() {
            game.paused = !game.paused;
            document.getElementById('pause-btn').textContent = game.paused ? '继续' : '暂停';
        }

        function updateUI() {
            document.getElementById('credits').textContent = game.credits;
            document.getElementById('score').textContent = game.score;
            document.getElementById('wave-num').textContent = game.wave || '0';

            if (!game.waveInProgress && game.wave === 0) {
                document.getElementById('wave-status').textContent = `准备中 - ${DIFFICULTY_CONFIG[game.difficulty].name}`;
                document.getElementById('enemy-count').textContent = '';
            } else if (!game.waveInProgress && game.resupplyCountdown > 0) {
                document.getElementById('wave-status').textContent = '补给时间';
            }

            document.getElementById('health-fill').style.width = (game.health / game.maxHealth * 100) + '%';

            Object.entries(TOWER_TYPES).forEach(([type, tower]) => {
                const card = document.getElementById(`tower-${type}`);
                if (card) {
                    card.classList.toggle('disabled', game.credits < tower.cost);
                }
            });

            if (game.selectedTower) {
                showTowerInfo(game.selectedTower);
            }
        }

        function createParticles(x, y, color, count) {
            for (let i = 0; i < count; i++) {
                game.particles.push({
                    x, y,
                    vx: (Math.random() - 0.5) * 10,
                    vy: (Math.random() - 0.5) * 10,
                    life: 40,
                    maxLife: 40,
                    color,
                    size: Math.random() * 5 + 2
                });
            }
        }

        const TARGET_FPS = 120;
        const FRAME_TIME = 1000 / TARGET_FPS;
        let accumulator = 0;

        function gameLoop(timestamp) {
            const deltaTime = timestamp - game.lastTime;
            game.lastTime = timestamp;

            accumulator += deltaTime;

            while (accumulator >= FRAME_TIME) {
                if (!game.paused && !game.gameOver) {
                    update(FRAME_TIME);
                }
                accumulator -= FRAME_TIME;
            }

            render();
            requestAnimationFrame(gameLoop);
        }

        function update(deltaTime) {
            const now = Date.now();
            const timeScale = deltaTime / FRAME_TIME;

            game.enemies.forEach(enemy => {
                if (enemy.teleporting) {
                    enemy.teleportProgress += 0.025 * timeScale;
                    
                    if (enemy.teleportProgress >= 1) {
                        enemy.teleporting = false;
                        enemy.teleportProgress = 1;
                    }
                    
                    return;
                }

                if (enemy.pathIndex < game.path.length - 1) {
                    const target = game.path[enemy.pathIndex + 1];
                    const targetX = target.x * TILE_SIZE + TILE_SIZE / 2;
                    const targetY = target.y * TILE_SIZE + TILE_SIZE / 2;

                    const dx = targetX - enemy.x;
                    const dy = targetY - enemy.y;
                    const dist = Math.sqrt(dx * dx + dy * dy);
                    enemy.angle = Math.atan2(dy, dx);

                    let speed = enemy.speed;
                    if (now < enemy.slowedUntil) speed *= 0.5;

                    if (dist < speed * 2) {
                        enemy.pathIndex++;
                    } else {
                        enemy.x += (dx / dist) * speed * timeScale;
                        enemy.y += (dy / dist) * speed * timeScale;
                    }
                } else {
                    game.health--;
                    enemy.health = 0;
                    game.killedCount++;
                    createParticles(enemy.x, enemy.y, '#ff0000', 25);
                    updateWaveUI();
                    if (game.health <= 0) endGame(false);
                }
            });

            game.enemies = game.enemies.filter(e => e.health > 0);

            game.towers.forEach(tower => {
                const towerData = TOWER_TYPES[tower.type];
                const towerX = tower.x * TILE_SIZE + TILE_SIZE / 2;
                const towerY = tower.y * TILE_SIZE + TILE_SIZE / 2;

                let nearestEnemy = null;
                let nearestDist = Infinity;

                game.enemies.forEach(enemy => {
                    if (enemy.stealth && tower.type !== 'emp') return;
                    const dx = enemy.x - towerX;
                    const dy = enemy.y - towerY;
                    const dist = Math.sqrt(dx * dx + dy * dy);
                    if (dist < tower.range && dist < nearestDist) {
                        nearestDist = dist;
                        nearestEnemy = enemy;
                    }
                });

                if (nearestEnemy) {
                    tower.targetAngle = Math.atan2(nearestEnemy.y - towerY, nearestEnemy.x - towerX);

                    if (now - tower.lastFire >= tower.fireRate) {
                        tower.lastFire = now;
                        tower.angle = tower.targetAngle;

                        game.projectiles.push({
                            x: towerX,
                            y: towerY,
                            targetX: nearestEnemy.x,
                            targetY: nearestEnemy.y,
                            target: nearestEnemy,
                            damage: tower.damage,
                            tower,
                            color: towerData.color,
                            speed: towerData.projectileType === 'beam' ? 25 : 12,
                            type: tower.type,
                            splash: towerData.splash || 0
                        });
                    }
                }

                let angleDiff = tower.targetAngle - tower.angle;
                while (angleDiff > Math.PI) angleDiff -= Math.PI * 2;
                while (angleDiff < -Math.PI) angleDiff += Math.PI * 2;
                tower.angle += angleDiff * 0.15;
            });

            game.projectiles.forEach(proj => {
                const dx = proj.targetX - proj.x;
                const dy = proj.targetY - proj.y;
                const dist = Math.sqrt(dx * dx + dy * dy);

                if (dist < proj.speed) {
                    proj.hit = true;
                    const towerData = TOWER_TYPES[proj.type];

                    if (proj.splash > 0) {
                        game.enemies.forEach(enemy => {
                            const edx = enemy.x - proj.targetX;
                            const edy = enemy.y - proj.targetY;
                            const edist = Math.sqrt(edx * edx + edy * edy);
                            if (edist < proj.splash) {
                                enemy.health -= proj.damage * (1 - edist / proj.splash);
                            }
                        });
                        createParticles(proj.targetX, proj.targetY, proj.color, 30);
                    } else {
                        if (proj.target && proj.target.health > 0) {
                            proj.target.health -= proj.damage;
                            if (towerData.slow) {
                                proj.target.slowedUntil = now + towerData.slowDuration;
                            }
                        }
                        createParticles(proj.targetX, proj.targetY, proj.color, 12);
                    }
                } else {
                    proj.x += (dx / dist) * proj.speed * timeScale;
                    proj.y += (dy / dist) * proj.speed * timeScale;
                }
            });

            game.projectiles = game.projectiles.filter(p => !p.hit);

            game.enemies.forEach(enemy => {
                if (enemy.health <= 0 && !enemy.rewarded) {
                    enemy.rewarded = true;
                    game.credits += enemy.reward;
                    game.score += enemy.reward * 10;
                    game.killedCount++;
                    createParticles(enemy.x, enemy.y, ENEMY_TYPES[enemy.type].color, 20);
                    updateWaveUI();
                }
            });
            game.enemies = game.enemies.filter(e => e.health > 0 || !e.rewarded);

            game.particles.forEach(p => {
                p.x += p.vx * timeScale;
                p.y += p.vy * timeScale;
                p.vx *= 0.94;
                p.vy *= 0.94;
                p.life -= timeScale;
            });
            game.particles = game.particles.filter(p => p.life > 0);

            if (game.waveInProgress && game.killedCount >= game.totalEnemiesInWave && game.enemies.length === 0) {
                clearSpawnInterval();
                game.waveInProgress = false;
                document.getElementById('wave-btn').disabled = false;
                document.getElementById('wave-btn').textContent = `开始第 ${game.wave + 1} 波`;
                startResupplyCountdown();
            }

            updateUI();
        }

        function render() {
            const ctx = game.ctx;
            const canvas = game.canvas;
            const mapData = MAPS[game.currentMap];

            ctx.fillStyle = mapData.bgColor;
            ctx.fillRect(0, 0, canvas.width, canvas.height);

            for (let y = 0; y < GRID_HEIGHT; y++) {
                for (let x = 0; x < GRID_WIDTH; x++) {
                    const tileX = x * TILE_SIZE;
                    const tileY = y * TILE_SIZE;

                    if (game.grid[y][x] === 1) {
                        ctx.fillStyle = mapData.groundColor1;
                        ctx.fillRect(tileX, tileY, TILE_SIZE, TILE_SIZE);
                    }

                    ctx.strokeStyle = mapData.groundColor2;
                    ctx.lineWidth = 1;
                    ctx.strokeRect(tileX, tileY, TILE_SIZE, TILE_SIZE);
                }
            }

            ctx.strokeStyle = mapData.pathBorderColor;
            ctx.lineWidth = 5;
            ctx.lineCap = 'round';
            ctx.lineJoin = 'round';
            ctx.beginPath();
            game.path.forEach((p, i) => {
                const px = p.x * TILE_SIZE + TILE_SIZE / 2;
                const py = p.y * TILE_SIZE + TILE_SIZE / 2;
                if (i === 0) ctx.moveTo(px, py);
                else ctx.lineTo(px, py);
            });
            ctx.stroke();

            ctx.strokeStyle = mapData.pathColor;
            ctx.lineWidth = 20;
            ctx.stroke();

            renderPortal();
            renderBase();

            if (hoverTile && game.selectedTowerType) {
                const towerData = TOWER_TYPES[game.selectedTowerType];
                const canPlace = game.grid[hoverTile.y][hoverTile.x] === 0;
                const previewColor = canPlace ? '#00ff00' : '#ff0000';

                ctx.fillStyle = `${previewColor}20`;
                ctx.beginPath();
                ctx.arc(hoverTile.x * TILE_SIZE + TILE_SIZE/2, hoverTile.y * TILE_SIZE + TILE_SIZE/2, towerData.range, 0, Math.PI * 2);
                ctx.fill();
                ctx.strokeStyle = `${previewColor}60`;
                ctx.lineWidth = 2;
                ctx.stroke();

                ctx.fillStyle = canPlace ? `${towerData.color}40` : '#40000040';
                ctx.fillRect(hoverTile.x * TILE_SIZE + 2, hoverTile.y * TILE_SIZE + 2, TILE_SIZE - 4, TILE_SIZE - 4);
                ctx.strokeStyle = previewColor;
                ctx.lineWidth = 2;
                ctx.strokeRect(hoverTile.x * TILE_SIZE + 2, hoverTile.y * TILE_SIZE + 2, TILE_SIZE - 4, TILE_SIZE - 4);
            }

            if (game.selectedTower) {
                ctx.fillStyle = 'rgba(255, 0, 255, 0.1)';
                ctx.beginPath();
                ctx.arc(game.selectedTower.x * TILE_SIZE + TILE_SIZE/2, game.selectedTower.y * TILE_SIZE + TILE_SIZE/2, game.selectedTower.range, 0, Math.PI * 2);
                ctx.fill();
                ctx.strokeStyle = 'rgba(255, 0, 255, 0.5)';
                ctx.lineWidth = 2;
                ctx.stroke();
            }

            game.towers.forEach(tower => {
                const towerData = TOWER_TYPES[tower.type];
                const x = tower.x * TILE_SIZE + TILE_SIZE / 2;
                const y = tower.y * TILE_SIZE + TILE_SIZE / 2;
                const size = 16 + tower.level * 3;

                ctx.save();
                ctx.translate(x, y);

                const outerGlow = ctx.createRadialGradient(0, 0, size, 0, 0, size + 20);
                outerGlow.addColorStop(0, `${towerData.color}40`);
                outerGlow.addColorStop(1, 'transparent');
                ctx.fillStyle = outerGlow;
                ctx.beginPath();
                ctx.arc(0, 0, size + 20, 0, Math.PI * 2);
                ctx.fill();

                ctx.shadowColor = towerData.color;
                ctx.shadowBlur = 15 + tower.level * 5;

                const baseGradient = ctx.createLinearGradient(-size - 4, -size - 4, size + 4, size + 4);
                baseGradient.addColorStop(0, '#2a2a4a');
                baseGradient.addColorStop(0.5, '#1a1a2a');
                baseGradient.addColorStop(1, '#0a0a1a');
                ctx.fillStyle = baseGradient;
                ctx.beginPath();
                ctx.arc(0, 0, size + 4, 0, Math.PI * 2);
                ctx.fill();

                const ringGradient = ctx.createLinearGradient(-size, 0, size, 0);
                ringGradient.addColorStop(0, towerData.color);
                ringGradient.addColorStop(0.5, '#ffffff');
                ringGradient.addColorStop(1, towerData.color);

                ctx.strokeStyle = ringGradient;
                ctx.lineWidth = 3 + tower.level;
                ctx.beginPath();
                ctx.arc(0, 0, size, 0, Math.PI * 2);
                ctx.stroke();

                for (let i = 0; i < 3 + tower.level; i++) {
                    const arcAngle = (i / (3 + tower.level)) * Math.PI * 2;
                    const arcX = Math.cos(arcAngle) * size;
                    const arcY = Math.sin(arcAngle) * size;
                    ctx.fillStyle = towerData.color;
                    ctx.beginPath();
                    ctx.arc(arcX, arcY, 2, 0, Math.PI * 2);
                    ctx.fill();
                }

                ctx.save();
                ctx.rotate(tower.angle);

                ctx.shadowColor = towerData.color;
                ctx.shadowBlur = 20 + tower.level * 5;

                if (tower.type === 'laser') {
                    ctx.fillStyle = '#1a3a5c';
                    ctx.beginPath();
                    ctx.roundRect(-size * 0.4, -size * 0.4, size * 0.8, size * 0.8, 3);
                    ctx.fill();
                    
                    ctx.fillStyle = towerData.color;
                    ctx.beginPath();
                    ctx.roundRect(-size * 0.2, -size * 0.2, size * 0.4, size * 0.4, 2);
                    ctx.fill();

                    const beamGradient = ctx.createLinearGradient(size * 0.3, 0, size * 1.2, 0);
                    beamGradient.addColorStop(0, towerData.color);
                    beamGradient.addColorStop(0.5, '#ffffff');
                    beamGradient.addColorStop(1, towerData.color);
                    ctx.strokeStyle = beamGradient;
                    ctx.lineWidth = 4 + tower.level;
                    ctx.lineCap = 'round';
                    ctx.beginPath();
                    ctx.moveTo(size * 0.3, 0);
                    ctx.lineTo(size * 1.2, 0);
                    ctx.stroke();

                    ctx.strokeStyle = `rgba(255, 255, 255, ${0.5 + Math.sin(Date.now() / 100) * 0.3})`;
                    ctx.lineWidth = 2;
                    ctx.stroke();
                } else if (tower.type === 'missile') {
                    ctx.fillStyle = '#5a3a2a';
                    ctx.beginPath();
                    ctx.roundRect(-size * 0.5, -size * 0.5, size, size, 4);
                    ctx.fill();
                    ctx.strokeStyle = towerData.color;
                    ctx.lineWidth = 2;
                    ctx.stroke();

                    for (let i = 0; i < 2; i++) {
                        const offsetX = (i === 0 ? -1 : 1) * size * 0.25;
                        ctx.fillStyle = '#3a2a1a';
                        ctx.beginPath();
                        ctx.roundRect(offsetX - size * 0.15, -size * 0.5, size * 0.3, size, 2);
                        ctx.fill();
                        
                        ctx.fillStyle = towerData.color;
                        ctx.shadowBlur = 10;
                        ctx.beginPath();
                        ctx.arc(offsetX, -size * 0.4, 4, 0, Math.PI * 2);
                        ctx.fill();
                    }

                    ctx.fillStyle = towerData.color;
                    ctx.shadowBlur = 15;
                    ctx.beginPath();
                    ctx.arc(0, 0, size * 0.2, 0, Math.PI * 2);
                    ctx.fill();
                } else if (tower.type === 'emp') {
                    ctx.fillStyle = '#3a2a4a';
                    ctx.beginPath();
                    ctx.arc(0, 0, size * 0.6, 0, Math.PI * 2);
                    ctx.fill();

                    ctx.strokeStyle = towerData.color;
                    ctx.lineWidth = 3;
                    ctx.beginPath();
                    ctx.arc(0, 0, size * 0.5, 0, Math.PI * 2);
                    ctx.stroke();

                    for (let i = 0; i < 6; i++) {
                        const angle = (i / 6) * Math.PI * 2 + tower.angle;
                        const innerR = size * 0.2;
                        const outerR = size * 0.5;
                        ctx.strokeStyle = `rgba(255, 0, 255, ${0.3 + Math.sin(Date.now() / 200 + i) * 0.2})`;
                        ctx.lineWidth = 2;
                        ctx.beginPath();
                        ctx.moveTo(Math.cos(angle) * innerR, Math.sin(angle) * innerR);
                        ctx.lineTo(Math.cos(angle) * outerR, Math.sin(angle) * outerR);
                        ctx.stroke();
                    }

                    const pulseSize = size * 0.3 + Math.sin(Date.now() / 150) * size * 0.1;
                    ctx.fillStyle = towerData.color;
                    ctx.shadowBlur = 25;
                    ctx.beginPath();
                    ctx.arc(0, 0, pulseSize, 0, Math.PI * 2);
                    ctx.fill();
                } else if (tower.type === 'plasma') {
                    ctx.fillStyle = '#2a4a2a';
                    ctx.beginPath();
                    ctx.roundRect(-size * 0.5, -size * 0.5, size, size, 5);
                    ctx.fill();
                    ctx.strokeStyle = towerData.color;
                    ctx.lineWidth = 2;
                    ctx.stroke();

                    const coreGradient = ctx.createRadialGradient(0, 0, 0, 0, 0, size * 0.4);
                    coreGradient.addColorStop(0, '#ffffff');
                    coreGradient.addColorStop(0.3, '#00ff00');
                    coreGradient.addColorStop(1, '#004000');
                    ctx.fillStyle = coreGradient;
                    ctx.shadowColor = '#00ff00';
                    ctx.shadowBlur = 20;
                    ctx.beginPath();
                    ctx.arc(0, 0, size * 0.35 + Math.sin(Date.now() / 100) * size * 0.05, 0, Math.PI * 2);
                    ctx.fill();

                    ctx.strokeStyle = '#00ff00';
                    ctx.lineWidth = 1;
                    for (let i = 0; i < 8; i++) {
                        const angle = (i / 8) * Math.PI * 2 + Date.now() / 300;
                        ctx.beginPath();
                        ctx.moveTo(Math.cos(angle) * size * 0.1, Math.sin(angle) * size * 0.1);
                        ctx.lineTo(Math.cos(angle) * size * 0.4, Math.sin(angle) * size * 0.4);
                        ctx.stroke();
                    }
                }

                ctx.restore();
                ctx.restore();
            });

            game.enemies.forEach(enemy => {
                const enemyData = ENEMY_TYPES[enemy.type];
                
                ctx.save();
                ctx.translate(enemy.x, enemy.y);

                if (enemy.teleporting) {
                    const progress = enemy.teleportProgress;
                    const teleportAlpha = Math.sin(progress * Math.PI);
                    const teleportScale = 0.5 + progress * 0.5;
                    const teleportOffset = (1 - progress) * 20;

                    ctx.globalAlpha = teleportAlpha;
                    ctx.scale(teleportScale, teleportScale);
                    ctx.translate(0, -teleportOffset);

                    for (let i = 0; i < 3; i++) {
                        const ringAlpha = (1 - progress) * 0.5 * (1 - i * 0.3);
                        ctx.strokeStyle = `rgba(138, 43, 226, ${ringAlpha})`;
                        ctx.lineWidth = 2;
                        ctx.beginPath();
                        ctx.arc(0, 0, enemyData.size + i * 10, 0, Math.PI * 2);
                        ctx.stroke();
                    }
                } else {
                    ctx.rotate(enemy.angle);
                    ctx.globalAlpha = enemy.stealth ? 0.3 : 1;

                    const glowGradient = ctx.createRadialGradient(0, 0, 0, 0, 0, enemyData.size * 2);
                    glowGradient.addColorStop(0, enemyData.color + '60');
                    glowGradient.addColorStop(0.5, enemyData.color + '30');
                    glowGradient.addColorStop(1, enemyData.color + '00');
                    ctx.fillStyle = glowGradient;
                    ctx.beginPath();
                    ctx.arc(0, 0, enemyData.size * 2, 0, Math.PI * 2);
                    ctx.fill();

                    ctx.shadowColor = enemyData.color;
                    ctx.shadowBlur = 15;

                    if (enemy.type === 'drone') {
                        ctx.fillStyle = '#1a3a5c';
                        ctx.beginPath();
                        ctx.ellipse(0, 0, enemyData.size * 1.3, enemyData.size * 0.6, 0, 0, Math.PI * 2);
                        ctx.fill();
                        ctx.strokeStyle = '#00bfff';
                        ctx.lineWidth = 2;
                        ctx.stroke();
                        
                        ctx.fillStyle = '#00bfff';
                        ctx.beginPath();
                        ctx.arc(-enemyData.size * 0.4, 0, 3, 0, Math.PI * 2);
                        ctx.fill();
                        ctx.beginPath();
                        ctx.arc(enemyData.size * 0.4, 0, 3, 0, Math.PI * 2);
                        ctx.fill();
                    } else if (enemy.type === 'cyborg') {
                        ctx.fillStyle = '#2a4a2a';
                        ctx.beginPath();
                        ctx.roundRect(-enemyData.size, -enemyData.size * 0.8, enemyData.size * 2, enemyData.size * 1.6, 5);
                        ctx.fill();
                        ctx.strokeStyle = '#00ff00';
                        ctx.lineWidth = 2;
                        ctx.stroke();
                        
                        ctx.fillStyle = '#00ff00';
                        ctx.beginPath();
                        ctx.arc(-enemyData.size * 0.5, -enemyData.size * 0.3, 4, 0, Math.PI * 2);
                        ctx.fill();
                        ctx.beginPath();
                        ctx.arc(enemyData.size * 0.5, -enemyData.size * 0.3, 4, 0, Math.PI * 2);
                        ctx.fill();
                        
                        ctx.strokeStyle = '#00ff0040';
                        ctx.lineWidth = 1;
                        ctx.beginPath();
                        ctx.moveTo(-enemyData.size * 0.5, enemyData.size * 0.2);
                        ctx.lineTo(-enemyData.size * 0.5, enemyData.size * 0.6);
                        ctx.moveTo(enemyData.size * 0.5, enemyData.size * 0.2);
                        ctx.lineTo(enemyData.size * 0.5, enemyData.size * 0.6);
                        ctx.stroke();
                    } else if (enemy.type === 'tank') {
                        ctx.fillStyle = '#5a3a2a';
                        ctx.beginPath();
                        ctx.roundRect(-enemyData.size * 1.2, -enemyData.size * 0.9, enemyData.size * 2.4, enemyData.size * 1.8, 8);
                        ctx.fill();
                        ctx.strokeStyle = '#ff6600';
                        ctx.lineWidth = 3;
                        ctx.stroke();
                        
                        ctx.fillStyle = '#8a5a3a';
                        ctx.beginPath();
                        ctx.roundRect(-enemyData.size * 0.8, -enemyData.size * 0.5, enemyData.size * 1.6, enemyData.size, 4);
                        ctx.fill();
                        
                        ctx.fillStyle = '#ff3300';
                        ctx.shadowBlur = 20;
                        ctx.beginPath();
                        ctx.arc(0, 0, enemyData.size * 0.3, 0, Math.PI * 2);
                        ctx.fill();
                    } else if (enemy.type === 'stealth') {
                        ctx.fillStyle = '#3a3a6a';
                        ctx.beginPath();
                        ctx.moveTo(enemyData.size, 0);
                        ctx.lineTo(-enemyData.size * 0.5, -enemyData.size * 0.8);
                        ctx.lineTo(-enemyData.size * 0.5, enemyData.size * 0.8);
                        ctx.closePath();
                        ctx.fill();
                        ctx.strokeStyle = '#8888ff';
                        ctx.lineWidth = 2;
                        ctx.stroke();
                        
                        ctx.fillStyle = '#aaaaff';
                        ctx.beginPath();
                        ctx.arc(0, -enemyData.size * 0.2, 3, 0, Math.PI * 2);
                        ctx.fill();
                    } else if (enemy.type === 'boss') {
                        for (let i = 2; i >= 0; i--) {
                            const layerSize = enemyData.size * (1 + i * 0.3);
                            ctx.fillStyle = i === 0 ? '#ff0000' : `rgba(100, 0, 0, ${0.3 - i * 0.08})`;
                            ctx.beginPath();
                            ctx.arc(0, 0, layerSize, 0, Math.PI * 2);
                            ctx.fill();
                        }
                        
                        ctx.strokeStyle = '#ff6600';
                        ctx.lineWidth = 3;
                        ctx.beginPath();
                        ctx.arc(0, 0, enemyData.size, 0, Math.PI * 2);
                        ctx.stroke();
                        
                        ctx.fillStyle = '#ff0000';
                        ctx.shadowBlur = 30;
                        ctx.beginPath();
                        ctx.arc(0, 0, enemyData.size * 0.4, 0, Math.PI * 2);
                        ctx.fill();
                        
                        ctx.strokeStyle = '#ffff00';
                        ctx.lineWidth = 2;
                        for (let i = 0; i < 6; i++) {
                            const angle = (i / 6) * Math.PI * 2 + Date.now() / 500;
                            ctx.beginPath();
                            ctx.moveTo(Math.cos(angle) * enemyData.size * 0.5, Math.sin(angle) * enemyData.size * 0.5);
                            ctx.lineTo(Math.cos(angle) * enemyData.size, Math.sin(angle) * enemyData.size);
                            ctx.stroke();
                        }
                    } else {
                        ctx.fillStyle = enemyData.color;
                        ctx.beginPath();
                        ctx.arc(0, 0, enemyData.size, 0, Math.PI * 2);
                        ctx.fill();
                    }

                    if (enemy.type !== 'boss' && enemy.type !== 'tank') {
                        const trailGradient = ctx.createLinearGradient(-enemyData.size * 2, 0, -enemyData.size * 0.5, 0);
                        trailGradient.addColorStop(0, 'transparent');
                        trailGradient.addColorStop(1, enemyData.color + '40');
                        ctx.fillStyle = trailGradient;
                        ctx.beginPath();
                        ctx.moveTo(-enemyData.size * 0.5, -enemyData.size * 0.3);
                        ctx.lineTo(-enemyData.size * 2, 0);
                        ctx.lineTo(-enemyData.size * 0.5, enemyData.size * 0.3);
                        ctx.closePath();
                        ctx.fill();
                    }

                    const healthPercent = enemy.health / enemy.maxHealth;
                    const healthWidth = enemyData.size * 2;
                    ctx.fillStyle = '#333';
                    ctx.fillRect(-healthWidth/2, -enemyData.size - 12, healthWidth, 4);
                    ctx.fillStyle = healthPercent > 0.5 ? '#0f0' : healthPercent > 0.25 ? '#ff0' : '#f00';
                    ctx.fillRect(-healthWidth/2, -enemyData.size - 12, healthWidth * healthPercent, 4);
                }

                ctx.restore();
            });

            game.projectiles.forEach(proj => {
                ctx.save();
                ctx.translate(proj.x, proj.y);

                const angle = Math.atan2(proj.targetY - proj.y, proj.targetX - proj.x);
                ctx.rotate(angle);

                ctx.shadowColor = proj.color;
                ctx.shadowBlur = 15;

                if (proj.type === 'laser') {
                    const beamGradient = ctx.createLinearGradient(-15, 0, 15, 0);
                    beamGradient.addColorStop(0, 'transparent');
                    beamGradient.addColorStop(0.5, proj.color);
                    beamGradient.addColorStop(1, '#ffffff');
                    ctx.strokeStyle = beamGradient;
                    ctx.lineWidth = 4;
                    ctx.lineCap = 'round';
                    ctx.beginPath();
                    ctx.moveTo(-15, 0);
                    ctx.lineTo(5, 0);
                    ctx.stroke();

                    ctx.strokeStyle = '#ffffff';
                    ctx.lineWidth = 2;
                    ctx.beginPath();
                    ctx.moveTo(-10, 0);
                    ctx.lineTo(0, 0);
                    ctx.stroke();
                } else if (proj.type === 'missile') {
                    ctx.fillStyle = '#5a3a2a';
                    ctx.beginPath();
                    ctx.roundRect(-12, -4, 20, 8, 3);
                    ctx.fill();

                    ctx.fillStyle = proj.color;
                    ctx.beginPath();
                    ctx.moveTo(8, -4);
                    ctx.lineTo(12, 0);
                    ctx.lineTo(8, 4);
                    ctx.closePath();
                    ctx.fill();

                    ctx.fillStyle = '#ff6600';
                    ctx.beginPath();
                    ctx.moveTo(-12, -3);
                    ctx.lineTo(-16, 0);
                    ctx.lineTo(-12, 3);
                    ctx.closePath();
                    ctx.fill();
                } else if (proj.type === 'emp') {
                    const pulseSize = 8 + Math.sin(Date.now() / 50) * 3;
                    const empGradient = ctx.createRadialGradient(0, 0, 0, 0, 0, pulseSize);
                    empGradient.addColorStop(0, '#ffffff');
                    empGradient.addColorStop(0.3, proj.color);
                    empGradient.addColorStop(1, 'transparent');
                    ctx.fillStyle = empGradient;
                    ctx.beginPath();
                    ctx.arc(0, 0, pulseSize, 0, Math.PI * 2);
                    ctx.fill();

                    ctx.strokeStyle = proj.color;
                    ctx.lineWidth = 2;
                    for (let i = 0; i < 6; i++) {
                        const arcAngle = (i / 6) * Math.PI * 2 + Date.now() / 100;
                        ctx.beginPath();
                        ctx.arc(0, 0, pulseSize + 3, arcAngle, arcAngle + Math.PI / 6);
                        ctx.stroke();
                    }
                } else if (proj.type === 'plasma') {
                    const plasmaGradient = ctx.createRadialGradient(0, 0, 0, 0, 0, 10);
                    plasmaGradient.addColorStop(0, '#ffffff');
                    plasmaGradient.addColorStop(0.3, '#00ff00');
                    plasmaGradient.addColorStop(1, proj.color);
                    ctx.fillStyle = plasmaGradient;
                    ctx.shadowBlur = 25;
                    ctx.beginPath();
                    ctx.arc(0, 0, 8, 0, Math.PI * 2);
                    ctx.fill();

                    ctx.strokeStyle = '#00ff00';
                    ctx.lineWidth = 1;
                    for (let i = 0; i < 4; i++) {
                        const trailAngle = Math.PI + (i / 4) * Math.PI;
                        ctx.beginPath();
                        ctx.moveTo(Math.cos(trailAngle) * 5, Math.sin(trailAngle) * 5);
                        ctx.lineTo(Math.cos(trailAngle) * 15, Math.sin(trailAngle) * 15);
                        ctx.stroke();
                    }
                } else {
                    ctx.fillStyle = proj.color;
                    ctx.beginPath();
                    ctx.arc(0, 0, 5, 0, Math.PI * 2);
                    ctx.fill();
                }

                ctx.restore();
            });

            game.particles.forEach(p => {
                const alpha = p.life / p.maxLife;
                ctx.fillStyle = p.color;
                ctx.globalAlpha = alpha;
                ctx.beginPath();
                ctx.arc(p.x, p.y, p.size * alpha, 0, Math.PI * 2);
                ctx.fill();
            });
            ctx.globalAlpha = 1;
        }

        function renderPortal() {
            const ctx = game.ctx;
            const startPos = game.path[0];
            const x = startPos.x * TILE_SIZE + TILE_SIZE / 2;
            const y = startPos.y * TILE_SIZE + TILE_SIZE / 2;

            game.portalAngle += 0.02;

            ctx.save();
            ctx.translate(x, y);

            const glowRadius = 50;
            const gradient = ctx.createRadialGradient(0, 0, 0, 0, 0, glowRadius);
            gradient.addColorStop(0, 'rgba(138, 43, 226, 0.4)');
            gradient.addColorStop(0.5, 'rgba(0, 191, 255, 0.2)');
            gradient.addColorStop(1, 'rgba(138, 43, 226, 0)');
            ctx.fillStyle = gradient;
            ctx.beginPath();
            ctx.arc(0, 0, glowRadius, 0, Math.PI * 2);
            ctx.fill();

            ctx.shadowColor = '#8a2be2';
            ctx.shadowBlur = 20;

            for (let i = 0; i < 3; i++) {
                const ringRadius = 25 + i * 12;
                const ringAlpha = 0.6 - i * 0.15;
                const ringAngle = game.portalAngle * (1 + i * 0.5);

                ctx.strokeStyle = `rgba(138, 43, 226, ${ringAlpha})`;
                ctx.lineWidth = 3;
                ctx.beginPath();
                ctx.ellipse(0, 0, ringRadius, ringRadius * 0.6, ringAngle, 0, Math.PI * 2);
                ctx.stroke();

                ctx.strokeStyle = `rgba(0, 191, 255, ${ringAlpha * 0.8})`;
                ctx.beginPath();
                ctx.ellipse(0, 0, ringRadius, ringRadius * 0.6, -ringAngle * 0.7, 0, Math.PI * 2);
                ctx.stroke();
            }

            ctx.shadowColor = '#00bfff';
            ctx.shadowBlur = 30;

            for (let i = 0; i < 8; i++) {
                const angle = (i / 8) * Math.PI * 2 + game.portalAngle;
                const innerRadius = 15;
                const outerRadius = 40;
                
                ctx.strokeStyle = `rgba(0, 191, 255, ${0.3 + Math.sin(Date.now() / 500 + i) * 0.2})`;
                ctx.lineWidth = 2;
                ctx.beginPath();
                ctx.moveTo(Math.cos(angle) * innerRadius, Math.sin(angle) * innerRadius * 0.6);
                ctx.lineTo(Math.cos(angle) * outerRadius, Math.sin(angle) * outerRadius * 0.6);
                ctx.stroke();
            }

            const innerGlow = ctx.createRadialGradient(0, 0, 0, 0, 0, 20);
            innerGlow.addColorStop(0, 'rgba(0, 191, 255, 0.6)');
            innerGlow.addColorStop(0.5, 'rgba(138, 43, 226, 0.3)');
            innerGlow.addColorStop(1, 'rgba(0, 0, 0, 0)');
            ctx.fillStyle = innerGlow;
            ctx.beginPath();
            ctx.ellipse(0, 0, 20, 12, 0, 0, Math.PI * 2);
            ctx.fill();

            for (let i = 0; i < 6; i++) {
                const arcAngle = game.portalAngle * 1.5 + (i / 6) * Math.PI * 2;
                const arcLength = Math.PI / 6;
                
                ctx.strokeStyle = `rgba(255, 255, 255, ${0.5 + Math.sin(Date.now() / 300) * 0.3})`;
                ctx.lineWidth = 1;
                ctx.beginPath();
                ctx.arc(0, 0, 18, arcAngle, arcAngle + arcLength);
                ctx.stroke();
            }

            game.portalParticles.forEach(p => {
                p.x += p.vx;
                p.y += p.vy;
                p.life--;
                p.size *= 0.98;
                
                ctx.globalAlpha = p.life / p.maxLife;
                ctx.fillStyle = p.color;
                ctx.beginPath();
                ctx.arc(p.x, p.y, p.size, 0, Math.PI * 2);
                ctx.fill();
            });
            game.portalParticles = game.portalParticles.filter(p => p.life > 0);

            if (Math.random() < 0.3) {
                game.portalParticles.push({
                    x: (Math.random() - 0.5) * 30,
                    y: (Math.random() - 0.5) * 18,
                    vx: (Math.random() - 0.5) * 2,
                    vy: (Math.random() - 0.5) * 2 - 0.5,
                    life: 60,
                    maxLife: 60,
                    color: Math.random() > 0.5 ? '#00bfff' : '#8a2be2',
                    size: Math.random() * 3 + 1
                });
            }

            ctx.restore();
        }

        function renderBase() {
            const ctx = game.ctx;
            const endPos = game.path[game.path.length - 1];
            const x = endPos.x * TILE_SIZE + TILE_SIZE / 2;
            const y = endPos.y * TILE_SIZE + TILE_SIZE / 2;

            const healthPercent = game.health / game.maxHealth;
            game.baseShieldIntensity = 0.3 + healthPercent * 0.7 + Math.sin(Date.now() / 200) * 0.1;

            ctx.save();
            ctx.translate(x, y);

            ctx.fillStyle = '#1a0a2e';
            ctx.beginPath();
            ctx.moveTo(0, -35);
            ctx.lineTo(25, -15);
            ctx.lineTo(25, 15);
            ctx.lineTo(10, 35);
            ctx.lineTo(-10, 35);
            ctx.lineTo(-25, 15);
            ctx.lineTo(-25, -15);
            ctx.closePath();
            ctx.fill();

            const baseColor = healthPercent > 0.5 ? '#00ff00' : healthPercent > 0.25 ? '#ffff00' : '#ff0000';
            ctx.strokeStyle = baseColor;
            ctx.lineWidth = 3;
            ctx.shadowColor = baseColor;
            ctx.shadowBlur = 15;
            ctx.stroke();

            ctx.fillStyle = '#0a0a1a';
            ctx.beginPath();
            ctx.moveTo(0, -25);
            ctx.lineTo(15, -8);
            ctx.lineTo(15, 8);
            ctx.lineTo(5, 25);
            ctx.lineTo(-5, 25);
            ctx.lineTo(-15, 8);
            ctx.lineTo(-15, -8);
            ctx.closePath();
            ctx.fill();

            ctx.fillStyle = baseColor;
            ctx.beginPath();
            ctx.moveTo(0, -15);
            ctx.lineTo(8, -3);
            ctx.lineTo(8, 3);
            ctx.lineTo(0, 15);
            ctx.lineTo(-8, 3);
            ctx.lineTo(-8, -3);
            ctx.closePath();
            ctx.fill();

            ctx.shadowColor = baseColor;
            ctx.shadowBlur = 20 + (1 - healthPercent) * 15;

            const shieldRadius = 45;
            const shieldGradient = ctx.createRadialGradient(0, 0, 0, 0, 0, shieldRadius);
            shieldGradient.addColorStop(0, `rgba(0, 191, 255, 0)`);
            shieldGradient.addColorStop(0.7, `rgba(0, 191, 255, ${game.baseShieldIntensity * 0.1})`);
            shieldGradient.addColorStop(1, `rgba(0, 191, 255, ${game.baseShieldIntensity * 0.3})`);
            ctx.fillStyle = shieldGradient;
            ctx.beginPath();
            ctx.arc(0, 0, shieldRadius, 0, Math.PI * 2);
            ctx.fill();

            ctx.strokeStyle = `rgba(0, 191, 255, ${game.baseShieldIntensity * 0.5})`;
            ctx.lineWidth = 2;
            ctx.beginPath();
            ctx.arc(0, 0, shieldRadius, 0, Math.PI * 2);
            ctx.stroke();

            const scanLineY = ((Date.now() / 100) % (shieldRadius * 2)) - shieldRadius;
            ctx.fillStyle = `rgba(0, 191, 255, ${game.baseShieldIntensity * 0.2})`;
            ctx.fillRect(-shieldRadius, scanLineY - 2, shieldRadius * 2, 4);

            if (healthPercent < 0.5) {
                const flicker = Math.random() > 0.7;
                if (flicker) {
                    ctx.strokeStyle = '#ff0000';
                    ctx.lineWidth = 1;
                    for (let i = 0; i < 3; i++) {
                        ctx.beginPath();
                        ctx.moveTo(0, -30);
                        ctx.lineTo((Math.random() - 0.5) * 20, -10);
                        ctx.stroke();
                    }
                }
            }

            ctx.restore();
        }

        function endGame(victory) {
            game.gameOver = true;
            game.victory = victory;
            
            clearSpawnInterval();
            clearResupplyTimer();
            
            document.getElementById('game-over-screen').style.display = 'flex';
            document.getElementById('game-over-title').textContent = victory ? '🎉 胜利！' : '💀 失败';
            document.getElementById('game-over-title').className = victory ? 'game-over-title victory' : 'game-over-title defeat';
            document.getElementById('final-stats').innerHTML = `
                最终得分：${game.score}<br>
                完成波次：${game.wave}/${WAVES.length}<br>
                消灭敌人：${game.killedCount}
            `;
        }

        function restartGame() {
            document.getElementById('game-over-screen').style.display = 'none';
            document.getElementById('game-container').style.display = 'none';
            document.getElementById('start-screen').style.display = 'flex';
            
            selectedDifficulty = null;
            selectedMap = null;
            
            document.querySelectorAll('.difficulty-card').forEach(card => {
                card.classList.remove('selected');
            });
            document.querySelectorAll('.map-card').forEach(card => {
                card.classList.remove('selected');
            });
            document.getElementById('start-game-btn').disabled = true;
            document.getElementById('start-game-btn').textContent = '请选择难度';
            document.getElementById('start-game-from-map-btn').disabled = true;
            document.getElementById('start-game-from-map-btn').textContent = '请选择地图';
            
            game.canvas = null;
            game.ctx = null;
            game.credits = 500;
            game.health = 20;
            game.maxHealth = 20;
            game.score = 0;
            game.wave = 0;
            game.waveInProgress = false;
            game.resupplyCountdown = 0;
            game.gameOver = false;
            game.victory = false;
            game.selectedTowerType = null;
            game.selectedTower = null;
            game.towers = [];
            game.enemies = [];
            game.projectiles = [];
            game.particles = [];
            game.grid = [];
            game.path = [];
            game.spawnQueue = [];
            game.totalEnemiesInWave = 0;
            game.spawnedCount = 0;
            game.killedCount = 0;
            game.difficulty = null;
            game.paused = false;
            game.portalAngle = 0;
            game.portalParticles = [];
            game.baseShieldIntensity = 1;
            game.currentMap = 0;
        }

        // 初始化游戏
        document.addEventListener('DOMContentLoaded', () => {
            // 确保开始界面显示正确
            document.getElementById('start-screen').style.display = 'flex';
            document.getElementById('map-select-screen').style.display = 'none';
            document.getElementById('game-container').style.display = 'none';
            document.getElementById('game-over-screen').style.display = 'none';
            
            // 添加塔卡片
            createTowerCards();
        });
    </script>
</body>
</html>
