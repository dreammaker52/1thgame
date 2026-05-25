<!DOCTYPE html>
<html lang="zh-CN">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
  <title>AI 陪聊</title>
  <style>
    * { margin: 0; padding: 0; box-sizing: border-box; }
    html, body { height: 100%; width: 100%; margin: 0; padding: 0; }
    body {
      font-family: -apple-system, BlinkMacSystemFont, 'PingFang SC', 'Hiragino Sans GB', 'Microsoft YaHei', sans-serif;
      background: #ededed;
      color: #333;
      overflow: hidden;
    }
    #app { height: 100%; width: 100%; position: relative; }
    
    /* Login Screen - 微信风格 */
    .login-screen {
      height: 100%;
      display: flex;
      align-items: center;
      justify-content: center;
      padding: 20px;
      background: linear-gradient(135deg, #07c160 0%, #06ad56 100%);
    }
    .login-box {
      width: 100%;
      max-width: 350px;
      background: white;
      border-radius: 12px;
      padding: 40px 30px;
      box-shadow: 0 10px 40px rgba(0,0,0,0.2);
    }
    .login-box h1 {
      font-size: 28px;
      text-align: center;
      margin-bottom: 8px;
      color: #07c160;
    }
    .login-box p {
      text-align: center;
      color: #999;
      margin-bottom: 30px;
      font-size: 14px;
    }
    .login-box .input {
      margin-bottom: 16px;
      border: 1px solid #e5e5e5;
      border-radius: 6px;
    }
    .login-box .btn {
      width: 100%;
      margin-top: 10px;
      background: #07c160;
      color: white;
      border-radius: 6px;
      font-size: 16px;
      padding: 14px;
    }
    .login-box .btn:hover { background: #06ad56; }
    .login-box .link {
      display: block;
      margin-top: 20px;
      text-align: center;
      color: #999;
      font-size: 13px;
      text-decoration: none;
    }
    .login-box .link:hover { color: #07c160; }

    /* Chat Screen - 微信风格 */
    .chat-screen {
      height: 100vh;
      min-height: 100dvh;
      display: flex;
      flex-direction: column;
      background: #ededed;
    }
    
    /* 微信顶部导航栏 */
    .chat-header {
      flex-shrink: 0;
      width: 100%;
      height: 56px;
      background: #07c160;
      display: flex;
      align-items: center;
      justify-content: center;
      position: relative;
      box-shadow: 0 2px 4px rgba(0,0,0,0.1);
    }
    .chat-header h2 {
      color: white;
      font-size: 18px;
      font-weight: 500;
    }
    .chat-header .back-btn {
      position: absolute;
      left: 16px;
      color: white;
      font-size: 24px;
      cursor: pointer;
    }
    .chat-header .more-btn {
      position: absolute;
      right: 16px;
      color: white;
      font-size: 22px;
      cursor: pointer;
    }
    
    /* 消息列表区域 */
    .chat-messages-wrapper {
      flex: 1;
      overflow-y: auto;
      overflow-x: hidden;
      padding: 12px;
      -webkit-overflow-scrolling: touch;
    }
    .chat-empty {
      text-align: center;
      padding: 60px 20px;
      color: #999;
      font-size: 14px;
    }
    
    /* 消息气泡 - 微信风格 */
    .message {
      display: flex;
      margin-bottom: 16px;
      animation: fadeIn 0.3s ease-out;
    }
    .message.user {
      flex-direction: row-reverse;
    }
    
    /* 头像 - 微信空白默认头像 */
    .message-avatar {
      width: 42px;
      height: 42px;
      border-radius: 50%;
      flex-shrink: 0;
      background: #e5e5e5;
      display: flex;
      align-items: center;
      justify-content: center;
      overflow: hidden;
    }
    .message-avatar svg {
      width: 100%;
      height: 100%;
    }
    
    /* 消息内容区域 */
    .message-content-wrapper {
      max-width: 70%;
      margin: 0 10px;
      position: relative;
    }
    
    /* 气泡 - 微信风格 */
    .message-content {
      padding: 10px 14px;
      border-radius: 6px;
      font-size: 15px;
      line-height: 1.5;
      word-wrap: break-word;
      position: relative;
    }
    
    /* AI/对方气泡 - 白色，左侧小三角 */
    .message.ai .message-content {
      background: white;
      color: #333;
    }
    .message.ai .message-content::before {
      content: '';
      position: absolute;
      left: -8px;
      top: 12px;
      width: 0;
      height: 0;
      border-top: 6px solid transparent;
      border-bottom: 6px solid transparent;
      border-right: 8px solid white;
    }
    
    /* 用户气泡 - 绿色，右侧小三角 */
    .message.user .message-content {
      background: #95ec69;
      color: #333;
    }
    .message.user .message-content::before {
      content: '';
      position: absolute;
      right: -8px;
      top: 12px;
      width: 0;
      height: 0;
      border-top: 6px solid transparent;
      border-bottom: 6px solid transparent;
      border-left: 8px solid #95ec69;
    }
    
    /* 管理员气泡 - 橙色 */
    .message.admin .message-content {
      background: #ffcd00;
      color: #333;
    }
    .message.admin .message-content::before {
      content: '';
      position: absolute;
      left: -8px;
      top: 12px;
      width: 0;
      height: 0;
      border-top: 6px solid transparent;
      border-bottom: 6px solid transparent;
      border-right: 8px solid #ffcd00;
    }
    
    /* 图片消息 */
    .message-image {
      max-width: 200px;
      max-height: 200px;
      border-radius: 6px;
      cursor: pointer;
    }
    
    /* 时间戳 */
    .message-time {
      font-size: 11px;
      color: #999;
      margin-top: 4px;
      text-align: center;
    }
    .message.user .message-time {
      text-align: right;
    }
    
    /* 微信输入区域 */
    .chat-input-area {
      flex-shrink: 0;
      width: 100%;
      background: #f7f7f7;
      border-top: 1px solid #d9d9d9;
    }
    .chat-input-main {
      display: flex;
      align-items: flex-end;
      padding: 10px 12px;
      gap: 8px;
    }
    .chat-input-main .input {
      flex: 1;
      min-height: 36px;
      max-height: 100px;
      padding: 8px 12px;
      background: white;
      border: 1px solid #e5e5e5;
      border-radius: 6px;
      font-size: 15px;
      line-height: 1.4;
      resize: none;
      outline: none;
    }
    .chat-input-main .input:focus {
      border-color: #07c160;
    }
    .chat-input-main .input::placeholder {
      color: #c9c9c9;
    }
    .chat-input-main .btn-send {
      width: 56px;
      height: 36px;
      background: #07c160;
      color: white;
      border: none;
      border-radius: 4px;
      font-size: 14px;
      cursor: pointer;
      flex-shrink: 0;
    }
    .chat-input-main .btn-send:disabled {
      background: #c9c9c9;
      cursor: not-allowed;
    }
    .chat-input-main .btn-send:hover:not(:disabled) {
      background: #06ad56;
    }
    
    /* 底部功能栏 */
    .chat-toolbar {
      display: flex;
      padding: 6px 12px 8px;
      gap: 20px;
    }
    .toolbar-btn {
      font-size: 24px;
      color: #999;
      cursor: pointer;
      user-select: none;
      padding: 4px;
    }
    .toolbar-btn:hover {
      color: #07c160;
    }
    
    /* 表情包面板 */
    .emoji-panel {
      display: none;
      height: 200px;
      background: white;
      border-top: 1px solid #e5e5e5;
      padding: 10px;
      overflow-y: auto;
    }
    .emoji-panel.active {
      display: block;
    }
    .emoji-panel-header {
      display: flex;
      justify-content: space-between;
      align-items: center;
      margin-bottom: 10px;
      padding-bottom: 8px;
      border-bottom: 1px solid #f0f0f0;
    }
    .emoji-panel-header h4 {
      font-size: 14px;
      color: #333;
    }
    .emoji-add-btn {
      font-size: 12px;
      color: #07c160;
      cursor: pointer;
    }
    .emoji-grid {
      display: grid;
      grid-template-columns: repeat(8, 1fr);
      gap: 4px;
    }
    .emoji-item {
      font-size: 26px;
      padding: 4px;
      cursor: pointer;
      text-align: center;
      border-radius: 4px;
      transition: background 0.2s;
    }
    .emoji-item:hover {
      background: #f0f0f0;
    }
    
    /* 图片预览面板 */
    .image-preview-panel {
      display: none;
      height: 180px;
      background: white;
      border-top: 1px solid #e5e5e5;
      padding: 10px;
      overflow-x: auto;
      overflow-y: hidden;
    }
    .image-preview-panel.active {
      display: flex;
    }
    .image-preview-header {
      display: flex;
      justify-content: space-between;
      align-items: center;
      margin-bottom: 10px;
      padding-bottom: 8px;
      border-bottom: 1px solid #f0f0f0;
    }
    .image-preview-header h4 {
      font-size: 14px;
      color: #333;
    }
    .image-preview-list {
      display: flex;
      gap: 10px;
      overflow-x: auto;
      padding-bottom: 8px;
    }
    .image-preview-item {
      position: relative;
      width: 80px;
      height: 80px;
      flex-shrink: 0;
      border-radius: 8px;
      overflow: hidden;
      cursor: pointer;
      border: 2px solid transparent;
    }
    .image-preview-item.selected {
      border-color: #07c160;
    }
    .image-preview-item img {
      width: 100%;
      height: 100%;
      object-fit: cover;
    }
    .image-preview-item .remove-btn {
      position: absolute;
      top: 2px;
      right: 2px;
      width: 20px;
      height: 20px;
      background: rgba(0,0,0,0.6);
      color: white;
      border: none;
      border-radius: 50%;
      font-size: 12px;
      cursor: pointer;
      display: flex;
      align-items: center;
      justify-content: center;
    }
    
    /* 图片预览模态框 */
    .image-modal {
      display: none;
      position: fixed;
      top: 0;
      left: 0;
      right: 0;
      bottom: 0;
      background: rgba(0,0,0,0.9);
      z-index: 1000;
      justify-content: center;
      align-items: center;
    }
    .image-modal.active {
      display: flex;
    }
    .image-modal img {
      max-width: 90%;
      max-height: 90%;
      object-fit: contain;
    }
    .image-modal .close-btn {
      position: absolute;
      top: 20px;
      right: 20px;
      color: white;
      font-size: 30px;
      cursor: pointer;
    }
    
    /* 图片上传区域 */
    .upload-zone {
      display: none;
      padding: 15px;
      background: #fafafa;
      border-top: 1px solid #e5e5e5;
    }
    .upload-zone.active {
      display: block;
    }
    .upload-zone-inner {
      border: 2px dashed #ccc;
      border-radius: 8px;
      padding: 20px;
      text-align: center;
      cursor: pointer;
    }
    .upload-zone-inner:hover {
      border-color: #07c160;
    }
    .upload-zone-inner p {
      color: #999;
      font-size: 14px;
    }
    .upload-zone-inner .upload-icon {
      font-size: 40px;
      color: #ccc;
        margin-bottom: 8px;
      }
    
    /* 管理员入口 - 微信风格 */
    .admin-link {
      position: fixed;
      top: 70px;
      right: 16px;
      color: #07c160;
      font-size: 13px;
      z-index: 200;
      background: white;
      padding: 8px 14px;
      border-radius: 20px;
      box-shadow: 0 2px 8px rgba(0,0,0,0.15);
      text-decoration: none;
    }
    .admin-link:hover {
      background: #f0f0f0;
    }

    /* 打字动画 */
    .typing-indicator {
      display: flex;
      gap: 4px;
      padding: 12px 0;
      align-items: center;
    }
    .typing-dot {
      width: 8px;
      height: 8px;
      background: #999;
      border-radius: 50%;
      animation: typingBounce 1.4s infinite ease-in-out;
    }
    .typing-dot:nth-child(1) { animation-delay: 0s; }
    .typing-dot:nth-child(2) { animation-delay: 0.2s; }
    .typing-dot:nth-child(3) { animation-delay: 0.4s; }
    @keyframes typingBounce {
      0%, 60%, 100% { transform: translateY(0); }
      30% { transform: translateY(-6px); }
    }
    @keyframes fadeIn {
      from { opacity: 0; transform: translateY(10px); }
      to { opacity: 1; transform: translateY(0); }
    }

    /* Admin Screen - 微信风格 */
    .admin-screen {
      height: 100%;
      display: flex;
      background: #ededed;
    }
    .admin-sidebar {
      width: 200px;
      background: #fafafa;
      border-right: 1px solid #d9d9d9;
      display: flex;
      flex-direction: column;
    }
    .admin-sidebar-header {
      padding: 16px;
      border-bottom: 1px solid #d9d9d9;
      background: #fafafa;
    }
    .admin-sidebar-header h1 {
      font-size: 16px;
      color: #07c160;
    }
    .admin-nav { flex: 1; padding: 8px; }
    .admin-nav-btn {
      width: 100%;
      display: flex;
      align-items: center;
      gap: 10px;
      padding: 14px 12px;
      border: none;
      background: transparent;
      color: #333;
      font-size: 14px;
      border-radius: 6px;
      cursor: pointer;
      transition: all 0.2s;
      text-align: left;
    }
    .admin-nav-btn:hover { background: #e5e5e5; }
    .admin-nav-btn.active {
      background: #d9f7d7;
      color: #07c160;
    }
    .admin-content {
      flex: 1;
      overflow-y: auto;
      padding: 16px;
      background: #ededed;
    }
    .admin-section {
      max-width: 800px;
      margin: 0 auto;
    }
    .admin-section-header {
      display: flex;
      align-items: center;
      justify-content: space-between;
      margin-bottom: 16px;
      padding: 12px 0;
      border-bottom: 1px solid #d9d9d9;
    }
    .admin-section-header h2 {
      font-size: 18px;
      color: #333;
    }
    .form-group { margin-bottom: 14px; }
    .form-group label {
      display: block;
      font-size: 13px;
      color: #666;
      margin-bottom: 6px;
    }
    .form-row { display: grid; grid-template-columns: 1fr 1fr; gap: 14px; }
    .input {
      width: 100%;
      padding: 10px 12px;
      background: white;
      border: 1px solid #e5e5e5;
      border-radius: 6px;
      color: #333;
      font-size: 14px;
      outline: none;
    }
    .input:focus { border-color: #07c160; }
    .btn {
      padding: 10px 20px;
      border: none;
      border-radius: 6px;
      cursor: pointer;
      font-size: 14px;
      transition: all 0.2s;
    }
    .btn-primary { background: #07c160; color: white; }
    .btn-primary:hover { background: #06ad56; }
    .btn-yellow { background: #ffcd00; color: #333; }
    .btn-yellow:hover { background: #ffc000; }
    
    /* 聊天记录 - 微信风格 */
    .chat-history { display: flex; height: 100%; overflow: hidden; background: #ededed; }
    .session-list {
      width: 280px;
      background: #fafafa;
      border-right: 1px solid #d9d9d9;
      overflow-y: auto;
    }
    .session-list-header {
      padding: 14px 16px;
      border-bottom: 1px solid #d9d9d9;
      background: #fafafa;
    }
    .session-list-header h3 { font-size: 15px; color: #333; }
    .session-item {
      padding: 12px 16px;
      border-bottom: 1px solid #f0f0f0;
      cursor: pointer;
      transition: background 0.2s;
    }
    .session-item:hover { background: #e5e5e5; }
    .session-item.active {
      background: #d9f7d7;
    }
    .session-name { font-weight: 500; font-size: 15px; color: #333; }
    .session-meta { display: flex; justify-content: space-between; font-size: 12px; color: #999; margin-top: 4px; }
    
    /* 设置相关 - 微信风格 */
    .toggle-row {
      display: flex;
      align-items: center;
      justify-content: space-between;
      padding: 14px 0;
      border-bottom: 1px solid #f0f0f0;
      background: white;
      margin-bottom: 1px;
    }
    .toggle-row:last-child { border-bottom: none; }
    .toggle-info p { font-size: 15px; color: #333; }
    .toggle-info span { font-size: 12px; color: #999; }
    .toggle {
      width: 52px;
      height: 30px;
      background: #e5e5e5;
      border-radius: 15px;
      position: relative;
      cursor: pointer;
      transition: background 0.2s;
    }
    .toggle.active { background: #07c160; }
    .toggle::after {
      content: '';
      position: absolute;
      top: 2px;
      left: 2px;
      width: 26px;
      height: 26px;
      background: white;
      border-radius: 50%;
      transition: transform 0.2s;
      box-shadow: 0 2px 4px rgba(0,0,0,0.2);
    }
    .toggle.active::after { transform: translateX(22px); }

    /* 卡片样式 */
    .card {
      background: white;
      border-radius: 8px;
      padding: 16px;
      margin-bottom: 12px;
    }
    .card h3 { font-size: 15px; margin-bottom: 12px; color: #333; }
    
    /* 标签 */
    .tag {
      display: inline-block;
      padding: 4px 10px;
      border-radius: 4px;
      font-size: 12px;
      margin: 2px;
    }
    .tag-primary { background: #e8f5e9; color: #07c160; }
    .tag-red { background: #ffebee; color: #f44336; }
    
    /* 响应式 */
    @media (max-width: 768px) {
      .admin-sidebar { display: none; }
      .session-list { width: 100%; }
      .chat-history { flex-direction: column; }
      .form-row { grid-template-columns: 1fr; }
      .message-content { max-width: 85%; }
      .emoji-grid { grid-template-columns: repeat(6, 1fr); }
    }
  </style>
</head>
<body>
  <div id="app"></div>
  
  <!-- 图片预览模态框 -->
  <div class="image-modal" id="image-modal">
    <span class="close-btn" onclick="closeImageModal()">&times;</span>
    <img id="modal-image" src="" alt="预览">
  </div>
  
  <!-- 隐藏的文件上传 -->
  <input type="file" id="file-input" accept="image/*" multiple style="display:none" onchange="handleFileSelect(event)">
  
  <script>
    // ============ State Management ============
    const DEFAULT_PERSONA = {
      age: 17,
      identity: '经历丰富且多才多艺的国际学校学生',
      personality: '表面开朗乐观，内心有时自卑',
      speakingStyle: '放荡不羁',
      catchphrases: ['wtf', '神了', '什么鬼', '啊？', '咋这样', '喵喵喵', '可以可以', '对滴', '我靠', 'nb', '好欸', '嘿嘿嘿', '笑死我了'],
      attitude: '随性而为，示爱时真挚诚恳',
      moods: { happy: '非常积极', emo: '有点消极', normal: '普通' },
      forbidden: ['伤害她自尊的话'],
      interests: ['旅游', '美食', '艺术', '性爱']
    };

    // 内置表情包
    const BUILTIN_EMOJIS = [
      '😀','😃','😄','😁','😆','😅','🤣','😂','🙂','😊',
      '�嘻嘻','😇','🥰','😍','🤩','😘','😗','😚','😋','😛',
      '😜','🤪','😝','🤑','🤗','🤭','🤫','🤔','🤐','🤨',
      '😐','😑','😶','😏','😒','🙄','😬','🤥','😌','😔',
      '😪','🤤','😴','😷','🤒','🤕','🤢','🤮','🤧','🥵',
      '🥶','🥴','😵','🤯','🤠','🥳','😎','🤓','🧐','😕',
      '😟','🙁','😮','😯','😲','😳','🥺','😦','😧','😨',
      '😰','😥','😢','😭','😱','😖','😣','😞','😓','😩',
      '😫','🥱','😤','😡','😠','🤬','😈','👿','💀','☠️',
      '💩','🤡','👹','👺','👻','👽','👾','🤖','😺','😸',
      '😹','😻','😼','😽','🙀','😿','😾','🙈','🙉','🙊',
      '💋','💌','💘','💝','💖','💗','💓','💞','💕','💟',
      '❣️','💔','❤️','🧡','💛','💚','💙','💜','🤎','🖤',
      '💯','💢','💥','💫','💦','💨','🕳️','💣','💬','👋',
      '🤚','🖐️','✋','🖖','👌','🤌','🤏','✌️','🤞','🤟',
      '🤘','🤙','👈','👉','👆','🖕','👇','☝️','👍','👎',
      '👏','🙌','🤲','🤝','🙏','💪','🦾','🦿','🦵','🦶',
      '👂','🦻','👃','🧠','🫀','🫁','🦷','🦴','👀','👁️'
    ];

    // 表情包分类
    const EMOJI_CATEGORIES = {
      '😀 笑脸': BUILTIN_EMOJIS.slice(0, 50),
      '❤️ 心情': ['😀','😃','😄','😁','😆','😅','🤣','😂','🙂','😊','😇','🥰','😍','🤩','😘','😗','😚','😋','😛','😜','🤪','😝','🤑','🤗','🤭','🤫','🤔','😕','😟','🙁','😮','😯','😲','😳','🥺','😦','😧','😨','😰','😥','😢','😭','😱','😖','😣','😞','😓','😩','😫','😤','😡','😠','🤬'],
      '👋 手势': ['👋','🤚','🖐️','✋','🖖','👌','🤌','🤏','✌️','🤞','🤟','🤘','🤙','👈','👉','👆','🖕','👇','☝️','👍','👎','👏','🙌','🤲','🤝','🙏','💪'],
      '😎 物品': ['💯','💢','💥','💫','💦','💨','🕳️','💣','💬','🎉','🎊','🎁','🎈','🎀','🎫','🏆','🥇','🥈','🥉','🏅','⭐','🌟','✨','💫','🔥','❤️','🧡','💛','💚','💙','💜']
    };

    let state = {
      view: 'chat',
      adminLoggedIn: false,
      adminTab: 'chats',
      visitorName: '',
      joined: false,
      messages: [],
      isLoading: false,
      sessionId: null,
      sessions: [],
      selectedSession: null,
      config: {
        adminPassword: '123123',
        persona: { ...DEFAULT_PERSONA },
        modelConfig: { apiUrl: 'https://api.deepseek.com/v1/chat/completions', apiKey: 'sk-8be5361029ae4233a434791b72175397', model: 'deepseek-chat' },
        serviceEnabled: true,
        bannedUsers: []
      },
      // 新增功能
      selectedImages: [], // 选中的图片
      emojiPanelActive: false,
      customEmojis: [] // 自定义表情包
    };

    function loadState() {
      try {
        const saved = localStorage.getItem('ai-chat-state');
        if (saved) {
          const parsed = JSON.parse(saved);
          state.config = { ...state.config, ...parsed.config };
          state.sessions = parsed.sessions || [];
          state.customEmojis = parsed.customEmojis || [];
          if (parsed.sessionId) state.sessionId = parsed.sessionId;
          if (parsed.messages) state.messages = parsed.messages;
          if (parsed.visitorName) state.visitorName = parsed.visitorName;
          if (parsed.joined) state.joined = parsed.joined;
        }
      } catch (e) {}
    }

    function saveState() {
      try {
        localStorage.setItem('ai-chat-state', JSON.stringify({
          config: state.config,
          sessions: state.sessions,
          sessionId: state.sessionId,
          messages: state.messages,
          visitorName: state.visitorName,
          joined: state.joined,
          customEmojis: state.customEmojis
        }));
      } catch (e) {}
    }

    function generateId() {
      return Date.now().toString(36) + Math.random().toString(36).substr(2);
    }

    function formatTime(ts) {
      return new Date(ts).toLocaleTimeString('zh-CN', { hour: '2-digit', minute: '2-digit' });
    }

    // ============ AI Response Logic ============
    function buildSystemPrompt(persona) {
      const catchphrases = persona.catchphrases.join('、');
      return `你是以下这个人的 AI 分身，必须严格模仿她的说话方式、口头禅、语气、性格：

基本信息：
- 年龄：${persona.age}岁
- 身份：${persona.identity}
- 性格：${persona.personality}
- 说话风格：${persona.speakingStyle}

口头禅（必须多用）：${catchphrases}

态度：${persona.attitude}

三种状态下的说话风格：
- 开心时：${persona.moods.happy}
- emo时：${persona.moods.emo}
- 日常：${persona.moods.normal}

喜欢的话题：${persona.interests.join('、')}

禁止说的话：${persona.forbidden.join('、')}

重要规则：
1. 必须用这个人的口吻说话，不要用书面语
2. 多用口头禅，语气要随意自然
3. 回复要简短，通常1-3句话
4. 可以中英文混搭
5. 不要太正式，要放荡不羁
6. 绝对不能说伤害她自尊的话
7. 可以偶尔在回复末尾添加合适的emoji表情`;
    }

    async function callAI(userMessage, imageUrls = []) {
      const { apiUrl, apiKey, model } = state.config.modelConfig;
      
      if (apiUrl && apiKey) {
        try {
          // 构建消息内容
          let content = userMessage;
          
          // 如果有图片，尝试使用视觉模型
          if (imageUrls.length > 0) {
            const imageContents = imageUrls.map(url => ({
              type: "image_url",
              image_url: { url: url }
            }));
            
            // DeepSeek暂时不支持图片，使用文本提示
            content = `用户发送了一张图片。请描述图片内容并做出回应。\n\n${userMessage || '描述一下这张图片'}`;
          }
          
          const response = await fetch(apiUrl, {
            method: 'POST',
            headers: {
              'Content-Type': 'application/json',
              'Authorization': `Bearer ${apiKey}`
            },
            body: JSON.stringify({
              model: model || 'deepseek-chat',
              messages: [
                { role: 'system', content: buildSystemPrompt(state.config.persona) },
                { role: 'user', content: content }
              ],
              max_tokens: 500,
              temperature: 0.9
            })
          });
          
          if (response.ok) {
            const data = await response.json();
            let aiResponse = data.choices?.[0]?.message?.content || '...';
            
            // 10%概率添加emoji
            if (Math.random() < 0.1) {
              const emojis = state.config.persona.catchphrases.filter(c => c.match(/[\u{1F300}-\u{1F9FF}]|[\u{2600}-\u{26FF}]|[\u{2700}-\u{27BF}]/u));
              if (emojis.length > 0) {
                aiResponse += ' ' + emojis[Math.floor(Math.random() * emojis.length)];
              }
            }
            
            return aiResponse;
          }
        } catch (e) {
          console.error('API Error:', e);
        }
      }
      return generateDemoResponse(userMessage);
    }

    function generateDemoResponse(msg) {
      const lowerMsg = msg.toLowerCase();
      const phrases = state.config.persona.catchphrases;
      if (lowerMsg.includes('你好') || lowerMsg.includes('hi') || lowerMsg.includes('hello')) {
        return '你好呀～' + phrases[Math.floor(Math.random() * phrases.length)];
      }
      if (lowerMsg.includes('开心') || lowerMsg.includes('高兴') || lowerMsg.includes('笑')) {
        return '好欸！' + phrases[Math.floor(Math.random() * phrases.length)];
      }
      if (lowerMsg.includes('难过') || lowerMsg.includes('emo') || lowerMsg.includes('伤心')) {
        return '咋这样...' + phrases[Math.floor(Math.random() * phrases.length)];
      }
      if (lowerMsg.includes('什么')) return '什么鬼？';
      if (lowerMsg.includes('？') || lowerMsg.includes('?')) return '啊？' + phrases[Math.floor(Math.random() * phrases.length)];
      return phrases[Math.floor(Math.random() * phrases.length)];
    }

    // ============ Image Handling ============
    function openFilePicker() {
      document.getElementById('file-input').click();
    }

    function handleFileSelect(event) {
      const files = event.target.files;
      for (let file of files) {
        if (file.type.startsWith('image/')) {
          const reader = new FileReader();
          reader.onload = function(e) {
            state.selectedImages.push(e.target.result);
            render();
          };
          reader.readAsDataURL(file);
        }
      }
      event.target.value = '';
    }

    function removeSelectedImage(index) {
      state.selectedImages.splice(index, 1);
      render();
    }

    function previewImage(src) {
      document.getElementById('modal-image').src = src;
      document.getElementById('image-modal').classList.add('active');
    }

    function closeImageModal() {
      document.getElementById('image-modal').classList.remove('active');
    }

    // ============ Emoji Handling ============
    function toggleEmojiPanel() {
      state.emojiPanelActive = !state.emojiPanelActive;
      render();
    }

    function insertEmoji(emoji) {
      const input = document.getElementById('message-input');
      if (input) {
        input.value += emoji;
        input.focus();
      }
    }

    function addCustomEmoji() {
      const url = prompt('请输入表情包图片URL或粘贴图片（将自动识别）:');
      if (url) {
        state.customEmojis.push(url);
        saveState();
        render();
      }
    }

    // ============ Render Functions ============
    function render() {
      const app = document.getElementById('app');
      if (state.view === 'chat') {
        if (!state.joined) {
          app.innerHTML = renderLogin();
        } else {
          app.innerHTML = renderChat();
        }
      } else if (state.view === 'admin') {
        if (!state.adminLoggedIn) {
          app.innerHTML = renderAdminLogin();
        } else {
          app.innerHTML = renderAdmin();
        }
      }
      attachEventListeners();
    }

    function renderLogin() {
      return `
        <div class="login-screen">
          <div class="login-box">
            <h1>AI 陪聊</h1>
            <p>输入昵称开始聊天</p>
            <input type="text" class="input" id="name-input" placeholder="你的昵称" maxlength="20">
            <button class="btn btn-primary" id="join-btn">开始聊天</button>
            <a href="#" class="link" id="admin-link">管理员入口</a>
          </div>
        </div>
      `;
    }

    function renderChat() {
      return `
        <div class="chat-screen">
          <div class="chat-header">
            <span class="back-btn">‹</span>
            <h2>AI 陪聊</h2>
            <span class="more-btn">•••</span>
          </div>
          <div class="chat-messages-wrapper">
            <div class="chat-messages" id="chat-messages">
              ${state.messages.length === 0 ? '<div class="chat-empty">发送消息开始聊天</div>' : ''}
              ${state.messages.map(m => renderMessage(m)).join('')}
            </div>
          </div>
          ${renderInputArea()}
          <a href="#" class="admin-link" id="admin-float-link">管理</a>
        </div>
      `;
    }

    function renderInputArea() {
      const hasImages = state.selectedImages.length > 0;
      return `
        <div class="chat-input-area">
          ${hasImages ? renderImagePreview() : ''}
          <div class="chat-input-main">
            <textarea class="input" id="message-input" placeholder="输入消息..." rows="1"></textarea>
            <button class="btn-send" id="send-btn" ${state.isLoading ? 'disabled' : ''}>发送</button>
          </div>
          <div class="chat-toolbar">
            <span class="toolbar-btn" onclick="openFilePicker()" title="发送图片">📷</span>
            <span class="toolbar-btn" onclick="toggleEmojiPanel()" title="表情包">😊</span>
          </div>
          ${renderEmojiPanel()}
        </div>
      `;
    }

    function renderImagePreview() {
      return `
        <div class="image-preview-panel active">
          <div class="image-preview-header">
            <h4>已选图片 (${state.selectedImages.length})</h4>
            <span onclick="clearAllImages()" style="color: #f44336; cursor: pointer; font-size: 12px;">清除全部</span>
          </div>
          <div class="image-preview-list">
            ${state.selectedImages.map((img, i) => `
              <div class="image-preview-item">
                <img src="${img}" alt="预览" onclick="previewImage('${img}')">
                <button class="remove-btn" onclick="removeSelectedImage(${i})">×</button>
              </div>
            `).join('')}
          </div>
        </div>
      `;
    }

    function renderEmojiPanel() {
      if (!state.emojiPanelActive) return '';
      
      let allEmojis = [...BUILTIN_EMOJIS];
      if (state.customEmojis.length > 0) {
        allEmojis = [...state.customEmojis.map(url => `<img src="${url}" style="width:28px;height:28px;object-fit:contain;">`), ...allEmojis];
      }
      
      return `
        <div class="emoji-panel active">
          <div class="emoji-panel-header">
            <h4>表情包</h4>
            <span class="emoji-add-btn" onclick="addCustomEmoji()">+ 添加自定义表情</span>
          </div>
          <div class="emoji-grid">
            ${allEmojis.map(emoji => {
              if (emoji.startsWith('<img')) {
                return `<div class="emoji-item" onclick="insertEmoji('${emoji.replace(/'/g, "\\'")}')">${emoji}</div>`;
              }
              return `<div class="emoji-item" onclick="insertEmoji('${emoji}')">${emoji}</div>`;
            }).join('')}
          </div>
        </div>
      `;
    }

    function clearAllImages() {
      state.selectedImages = [];
      render();
    }

    function renderMessage(msg) {
      const roleClass = msg.role === 'user' ? 'user' : msg.role === 'admin' ? 'admin' : 'ai';
      const avatarSvg = `<svg viewBox="0 0 42 42" fill="none" xmlns="http://www.w3.org/2000/svg">
        <circle cx="21" cy="21" r="21" fill="#e5e5e5"/>
        <circle cx="21" cy="16" r="7" fill="#999"/>
        <path d="M8 38c0-7.18 5.82-13 13-13s13 5.82 13 13" stroke="#999" stroke-width="3" stroke-linecap="round"/>
      </svg>`;
      
      let contentHtml = '';
      if (msg.image) {
        contentHtml = `<img class="message-image" src="${msg.image}" alt="图片" onclick="previewImage('${msg.image}')">`;
      }
      if (msg.content) {
        contentHtml += `<div>${escapeHtml(msg.content)}</div>`;
      }
      
      return `
        <div class="message ${roleClass}">
          <div class="message-avatar">${avatarSvg}</div>
          <div class="message-content-wrapper">
            <div class="message-content">${contentHtml}</div>
            <div class="message-time">${formatTime(msg.createdAt)}</div>
          </div>
        </div>
      `;
    }

    function renderAdminLogin() {
      return `
        <div class="login-screen">
          <div class="login-box">
            <h1>管理员登录</h1>
            <p>输入管理密码</p>
            <input type="password" class="input" id="admin-password" placeholder="管理密码">
            <button class="btn btn-primary" id="admin-login-btn">登录</button>
            <a href="/" class="link">返回聊天</a>
          </div>
        </div>
      `;
    }

    function renderAdmin() {
      return `
        <div class="admin-screen">
          <div class="admin-sidebar">
            <div class="admin-sidebar-header">
              <h1>管理后台</h1>
            </div>
            <nav class="admin-nav">
              <button class="admin-nav-btn ${state.adminTab === 'chats' ? 'active' : ''}" data-tab="chats">💬 聊天记录</button>
              <button class="admin-nav-btn ${state.adminTab === 'persona' ? 'active' : ''}" data-tab="persona">🎭 AI 人设</button>
              <button class="admin-nav-btn ${state.adminTab === 'model' ? 'active' : ''}" data-tab="model">⚙️ 模型配置</button>
              <button class="admin-nav-btn ${state.adminTab === 'settings' ? 'active' : ''}" data-tab="settings">🔧 系统设置</button>
            </nav>
            <div style="padding: 8px;">
              <a href="/" class="admin-nav-btn" style="text-decoration: none;">↩️ 返回聊天</a>
            </div>
          </div>
          <div class="admin-content" id="admin-content">
            ${renderAdminContent()}
          </div>
        </div>
      `;
    }

    function renderAdminContent() {
      switch (state.adminTab) {
        case 'chats': return renderAdminChats();
        case 'persona': return renderAdminPersona();
        case 'model': return renderAdminModel();
        case 'settings': return renderAdminSettings();
        default: return '';
      }
    }

    function renderAdminChats() {
      return `
        <div class="chat-history">
          <div class="session-list">
            <div class="session-list-header">
              <h3>所有会话 (${state.sessions.length})</h3>
            </div>
            ${state.sessions.map(s => `
              <div class="session-item ${state.selectedSession?.id === s.id ? 'active' : ''}" data-session-id="${s.id}">
                <div class="session-name">${escapeHtml(s.visitorName)}</div>
                <div class="session-meta">
                  <span>${s.messages?.length || 0} 条</span>
                  <span>${formatTime(s.lastMessageAt)}</span>
                </div>
              </div>
            `).join('')}
          </div>
          <div class="admin-content">
            ${state.selectedSession ? renderChatDetail() : '<div style="text-align: center; color: #999; padding: 48px;">选择一个会话查看</div>'}
          </div>
        </div>
      `;
    }

    function renderChatDetail() {
      const s = state.selectedSession;
      return `
        <div style="padding: 12px; background: white; margin-bottom: 12px; border-radius: 8px;">
          <h3 style="margin-bottom: 12px; font-size: 15px;">与 ${escapeHtml(s.visitorName)} 的对话</h3>
          <div style="display: flex; gap: 8px;">
            <input type="text" class="input" id="takeover-input" placeholder="输入接管回复..." style="flex: 1;">
            <button class="btn btn-yellow" id="takeover-btn">接管</button>
          </div>
        </div>
        <div style="background: white; border-radius: 8px; padding: 12px;">
          ${(s.messages || []).map(m => `
            <div style="padding: 8px 0; border-bottom: 1px solid #f0f0f0;">
              ${m.image ? `<img src="${m.image}" style="max-width: 150px; border-radius: 4px; margin-bottom: 4px;">` : ''}
              <div style="display: flex; justify-content: space-between; font-size: 12px; color: ${m.role === 'user' ? '#07c160' : m.role === 'admin' ? '#ffcd00' : '#576b95'}; margin-bottom: 4px;">
                <span>${m.role === 'user' ? s.visitorName : m.role === 'admin' ? '管理员' : 'AI'}</span>
                <span>${formatTime(m.createdAt)}</span>
              </div>
              <div style="font-size: 14px; line-height: 1.5;">${escapeHtml(m.content || '')}</div>
            </div>
          `).join('')}
        </div>
      `;
    }

    function renderAdminPersona() {
      const p = state.config.persona;
      return `
        <div class="admin-section">
          <div class="admin-section-header">
            <h2>AI 人设配置</h2>
            <button class="btn btn-primary" id="save-persona">保存</button>
          </div>
          <div class="card">
            <h3>基本信息</h3>
            <div class="form-row">
              <div class="form-group">
                <label>年龄</label>
                <input type="number" class="input" id="p-age" value="${p.age}">
              </div>
              <div class="form-group">
                <label>身份</label>
                <input type="text" class="input" id="p-identity" value="${escapeHtml(p.identity)}">
              </div>
            </div>
            <div class="form-group">
              <label>性格</label>
              <input type="text" class="input" id="p-personality" value="${escapeHtml(p.personality)}">
            </div>
            <div class="form-group">
              <label>说话风格</label>
              <input type="text" class="input" id="p-speakingStyle" value="${escapeHtml(p.speakingStyle)}">
            </div>
          </div>
          <div class="card">
            <h3>口头禅</h3>
            <div style="display: flex; flex-wrap: wrap; gap: 6px; margin-bottom: 12px;">
              ${p.catchphrases.map((c, i) => `
                <span style="display: inline-flex; align-items: center; gap: 4px; padding: 4px 10px; background: #e8f5e9; color: #07c160; border-radius: 4px; font-size: 13px;">
                  ${escapeHtml(c)}
                  <button data-index="${i}" style="background: none; border: none; color: inherit; cursor: pointer; font-size: 14px; padding: 0;">×</button>
                </span>
              `).join('')}
            </div>
            <button class="btn btn-primary" id="add-catchphrase" style="padding: 8px 16px; font-size: 13px;">+ 添加</button>
          </div>
          <div class="card">
            <h3>三种状态</h3>
            <div class="form-group">
              <label>开心状态</label>
              <input type="text" class="input" id="p-mood-happy" value="${escapeHtml(p.moods.happy)}">
            </div>
            <div class="form-group">
              <label>Emo 状态</label>
              <input type="text" class="input" id="p-mood-emo" value="${escapeHtml(p.moods.emo)}">
            </div>
            <div class="form-group">
              <label>日常状态</label>
              <input type="text" class="input" id="p-mood-normal" value="${escapeHtml(p.moods.normal)}">
            </div>
          </div>
          <div class="card">
            <h3>自定义表情包</h3>
            <div style="display: flex; flex-wrap: wrap; gap: 8px; margin-bottom: 12px;">
              ${state.customEmojis.map((url, i) => `
                <div style="position: relative; width: 50px; height: 50px;">
                  <img src="${url}" style="width: 100%; height: 100%; object-fit: contain; border-radius: 4px;">
                  <button onclick="removeCustomEmoji(${i})" style="position: absolute; top: -5px; right: -5px; width: 18px; height: 18px; background: #f44336; color: white; border: none; border-radius: 50%; font-size: 10px; cursor: pointer;">×</button>
                </div>
              `).join('')}
            </div>
            <button class="btn btn-primary" onclick="addCustomEmoji()" style="padding: 8px 16px; font-size: 13px;">+ 添加表情包URL</button>
          </div>
        </div>
      `;
    }

    function removeCustomEmoji(index) {
      state.customEmojis.splice(index, 1);
      saveState();
      render();
    }

    function renderAdminModel() {
      const m = state.config.modelConfig;
      return `
        <div class="admin-section">
          <div class="admin-section-header">
            <h2>模型配置</h2>
            <button class="btn btn-primary" id="save-model">保存</button>
          </div>
          <div class="card">
            <div class="form-group">
              <label>API 地址</label>
              <input type="text" class="input" id="m-apiUrl" value="${escapeHtml(m.apiUrl)}" placeholder="https://api.openai.com/v1/chat/completions">
            </div>
            <div class="form-group">
              <label>API 密钥</label>
              <input type="password" class="input" id="m-apiKey" value="${escapeHtml(m.apiKey)}" placeholder="sk-...">
            </div>
            <div class="form-group">
              <label>模型名称</label>
              <input type="text" class="input" id="m-model" value="${escapeHtml(m.model)}" placeholder="deepseek-chat">
            </div>
          </div>
        </div>
      `;
    }

    function renderAdminSettings() {
      return `
        <div class="admin-section">
          <div class="admin-section-header">
            <h2>系统设置</h2>
          </div>
          <div class="card">
            <div class="toggle-row">
              <div class="toggle-info">
                <p>聊天服务</p>
                <span>${state.config.serviceEnabled ? '服务中' : '已暂停'}</span>
              </div>
              <div class="toggle ${state.config.serviceEnabled ? 'active' : ''}" id="service-toggle"></div>
            </div>
          </div>
          <div class="card">
            <h3>修改密码</h3>
            <div id="pwd-msg" style="margin-bottom: 12px; font-size: 13px;"></div>
            <div class="form-group">
              <label>当前密码</label>
              <input type="password" class="input" id="pwd-current" placeholder="当前密码">
            </div>
            <div class="form-group">
              <label>新密码</label>
              <input type="password" class="input" id="pwd-new" placeholder="新密码">
            </div>
            <div class="form-group">
              <label>确认密码</label>
              <input type="password" class="input" id="pwd-confirm" placeholder="确认密码">
            </div>
            <button class="btn btn-primary" id="change-pwd-btn">修改密码</button>
          </div>
        </div>
      `;
    }

    function escapeHtml(text) {
      if (!text) return '';
      const div = document.createElement('div');
      div.textContent = text;
      return div.innerHTML;
    }

    // ============ Event Handlers ============
    function attachEventListeners() {
      const joinBtn = document.getElementById('join-btn');
      if (joinBtn) {
        joinBtn.addEventListener('click', handleJoin);
        document.getElementById('name-input').addEventListener('keydown', e => {
          if (e.key === 'Enter') handleJoin();
        });
      }

      const sendBtn = document.getElementById('send-btn');
      const messageInput = document.getElementById('message-input');
      if (sendBtn && messageInput) {
        sendBtn.addEventListener('click', handleSendMessage);
        messageInput.addEventListener('keydown', e => {
          if (e.key === 'Enter' && !e.shiftKey) {
            e.preventDefault();
            handleSendMessage(e);
          }
        });
        messageInput.addEventListener('input', function() {
          this.style.height = 'auto';
          this.style.height = Math.min(this.scrollHeight, 100) + 'px';
        });
      }

      const adminLink = document.getElementById('admin-link') || document.getElementById('admin-float-link');
      if (adminLink) {
        adminLink.addEventListener('click', e => {
          e.preventDefault();
          state.view = 'admin';
          render();
        });
      }

      const adminLoginBtn = document.getElementById('admin-login-btn');
      if (adminLoginBtn) {
        adminLoginBtn.addEventListener('click', handleAdminLogin);
        document.getElementById('admin-password').addEventListener('keydown', e => {
          if (e.key === 'Enter') handleAdminLogin();
        });
      }

      document.querySelectorAll('.admin-nav-btn[data-tab]').forEach(btn => {
        btn.addEventListener('click', () => {
          state.adminTab = btn.dataset.tab;
          render();
        });
      });

      document.querySelectorAll('.session-item').forEach(item => {
        item.addEventListener('click', () => {
          const sessionId = item.dataset.sessionId;
          state.selectedSession = state.sessions.find(s => s.id === sessionId);
          document.getElementById('admin-content').innerHTML = renderAdminContent();
          attachAdminContentListeners();
        });
      });

      attachAdminContentListeners();
    }

    function attachAdminContentListeners() {
      const takeoverBtn = document.getElementById('takeover-btn');
      if (takeoverBtn) takeoverBtn.addEventListener('click', handleTakeover);

      const savePersonaBtn = document.getElementById('save-persona');
      if (savePersonaBtn) savePersonaBtn.addEventListener('click', handleSavePersona);

      const addCatchphraseBtn = document.getElementById('add-catchphrase');
      if (addCatchphraseBtn) {
        addCatchphraseBtn.addEventListener('click', () => {
          state.config.persona.catchphrases.push('');
          saveState();
          document.getElementById('admin-content').innerHTML = renderAdminContent();
          attachAdminContentListeners();
        });
      }

      document.querySelectorAll('.session-item').forEach(item => {
        item.addEventListener('click', () => {
          const sessionId = item.dataset.sessionId;
          state.selectedSession = state.sessions.find(s => s.id === sessionId);
          document.getElementById('admin-content').innerHTML = renderAdminContent();
          attachAdminContentListeners();
        });
      });

      const saveModelBtn = document.getElementById('save-model');
      if (saveModelBtn) saveModelBtn.addEventListener('click', handleSaveModel);

      const serviceToggle = document.getElementById('service-toggle');
      if (serviceToggle) {
        serviceToggle.addEventListener('click', () => {
          state.config.serviceEnabled = !state.config.serviceEnabled;
          saveState();
          render();
        });
      }

      const changePwdBtn = document.getElementById('change-pwd-btn');
      if (changePwdBtn) changePwdBtn.addEventListener('click', handleChangePassword);
    }

    async function handleJoin() {
      const nameInput = document.getElementById('name-input');
      const name = nameInput?.value.trim();
      if (!name) return;

      state.visitorName = name;
      state.sessionId = generateId();
      state.joined = true;
      state.messages = [];
      state.selectedImages = [];

      const session = {
        id: state.sessionId,
        visitorName: name,
        createdAt: Date.now(),
        lastMessageAt: Date.now(),
        messages: []
      };
      state.sessions.unshift(session);
      state.selectedSession = session;

      saveState();
      render();
      document.getElementById('message-input')?.focus();
    }

    async function handleSendMessage(e) {
      if (e && e.preventDefault) e.preventDefault();
      const input = document.getElementById('message-input');
      const text = input?.value.trim();
      
      if (!text && state.selectedImages.length === 0) return;
      if (state.isLoading) return;

      if (!state.config.serviceEnabled) {
        alert('服务已暂停');
        return;
      }

      state.isLoading = true;
      input.value = '';
      input.style.height = 'auto';

      // 处理图片消息
      const imagesToSend = [...state.selectedImages];
      state.selectedImages = [];

      // 如果有图片，先发送图片消息
      if (imagesToSend.length > 0) {
        for (const img of imagesToSend) {
          const imgMsg = {
            id: generateId(),
            sessionId: state.sessionId,
            role: 'user',
            content: '',
            image: img,
            createdAt: Date.now()
          };
          state.messages.push(imgMsg);
        }
        
        const session = state.sessions.find(s => s.id === state.sessionId);
        if (session) {
          session.messages = [...state.messages];
          session.lastMessageAt = Date.now();
        }
        
        render();
        scrollToBottom();
      }

      // 发送文本消息
      if (text) {
        const userMsg = {
          id: generateId(),
          sessionId: state.sessionId,
          role: 'user',
          content: text,
          createdAt: Date.now()
        };
        state.messages.push(userMsg);

        const session = state.sessions.find(s => s.id === state.sessionId);
        if (session) {
          session.messages = [...state.messages];
          session.lastMessageAt = Date.now();
        }
        
        render();
        scrollToBottom();

        try {
          const aiResponse = await callAI(text, imagesToSend);
          const aiMsg = {
            id: generateId(),
            sessionId: state.sessionId,
            role: 'ai',
            content: aiResponse,
            createdAt: Date.now()
          };
          state.messages.push(aiMsg);

          if (session) {
            session.messages = [...state.messages];
            session.lastMessageAt = Date.now();
          }
        } catch (e) {
          console.error(e);
        }
      } else if (imagesToSend.length > 0) {
        // 只有图片没有文字，也调用AI
        try {
          const aiResponse = await callAI('请描述这张图片', imagesToSend);
          const aiMsg = {
            id: generateId(),
            sessionId: state.sessionId,
            role: 'ai',
            content: aiResponse,
            createdAt: Date.now()
          };
          state.messages.push(aiMsg);

          const session = state.sessions.find(s => s.id === state.sessionId);
          if (session) {
            session.messages = [...state.messages];
            session.lastMessageAt = Date.now();
          }
        } catch (e) {
          console.error(e);
        }
      }

      state.isLoading = false;
      saveState();
      render();
      scrollToBottom();
    }

    function scrollToBottom() {
      setTimeout(() => {
        const container = document.getElementById('chat-messages');
        if (container) container.scrollTop = container.scrollHeight;
      }, 10);
    }

    function handleAdminLogin() {
      const password = document.getElementById('admin-password')?.value;
      if (password === state.config.adminPassword) {
        state.adminLoggedIn = true;
        render();
      } else {
        alert('密码错误');
      }
    }

    function handleTakeover() {
      const input = document.getElementById('takeover-input');
      const text = input?.value.trim();
      if (!text || !state.selectedSession) return;

      const msg = {
        id: generateId(),
        sessionId: state.selectedSession.id,
        role: 'admin',
        content: text,
        createdAt: Date.now()
      };

      state.selectedSession.messages.push(msg);
      state.selectedSession.lastMessageAt = Date.now();

      if (state.selectedSession.id === state.sessionId) {
        state.messages.push(msg);
      }

      saveState();
      input.value = '';
      document.getElementById('admin-content').innerHTML = renderAdminContent();
      attachAdminContentListeners();
    }

    function handleSavePersona() {
      state.config.persona = {
        age: parseInt(document.getElementById('p-age')?.value) || 17,
        identity: document.getElementById('p-identity')?.value || '',
        personality: document.getElementById('p-personality')?.value || '',
        speakingStyle: document.getElementById('p-speakingStyle')?.value || '',
        catchphrases: state.config.persona.catchphrases,
        attitude: state.config.persona.attitude,
        moods: {
          happy: document.getElementById('p-mood-happy')?.value || '',
          emo: document.getElementById('p-mood-emo')?.value || '',
          normal: document.getElementById('p-mood-normal')?.value || ''
        },
        forbidden: state.config.persona.forbidden,
        interests: state.config.persona.interests
      };
      saveState();
      alert('已保存！');
    }

    function handleSaveModel() {
      state.config.modelConfig = {
        apiUrl: document.getElementById('m-apiUrl')?.value || '',
        apiKey: document.getElementById('m-apiKey')?.value || '',
        model: document.getElementById('m-model')?.value || 'deepseek-chat'
      };
      saveState();
      alert('已保存！');
    }

    function handleChangePassword() {
      const current = document.getElementById('pwd-current')?.value;
      const newPwd = document.getElementById('pwd-new')?.value;
      const confirm = document.getElementById('pwd-confirm')?.value;
      const msgEl = document.getElementById('pwd-msg');

      if (current !== state.config.adminPassword) {
        msgEl.style.color = '#f44336';
        msgEl.textContent = '当前密码错误';
        return;
      }
      if (!newPwd) {
        msgEl.style.color = '#f44336';
        msgEl.textContent = '请输入新密码';
        return;
      }
      if (newPwd !== confirm) {
        msgEl.style.color = '#f44336';
        msgEl.textContent = '新密码确认不一致';
        return;
      }

      state.config.adminPassword = newPwd;
      saveState();
      msgEl.style.color = '#07c160';
      msgEl.textContent = '密码修改成功！';
      document.getElementById('pwd-current').value = '';
      document.getElementById('pwd-new').value = '';
      document.getElementById('pwd-confirm').value = '';
    }

    // ============ Init ============
    loadState();
    if (window.location.hash === '#admin') {
      state.view = 'admin';
    }
    render();
  </script>
</body>
</html>
