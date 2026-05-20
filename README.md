<!DOCTYPE html>
<html lang="zh-CN">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
  <title>AI 陪聊</title>
  <style>
    * { margin: 0; padding: 0; box-sizing: border-box; }
    html, body, #app { height: 100%; width: 100%; }
    body {
      font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;
      background: #0f0f0f;
      color: #e5e5e5;
      overflow: hidden;
    }
    ::-webkit-scrollbar { width: 6px; }
    ::-webkit-scrollbar-track { background: #1a1a1a; }
    ::-webkit-scrollbar-thumb { background: #6366f1; border-radius: 3px; }
    @keyframes fadeIn { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }
    .animate-fade-in { animation: fadeIn 0.3s ease-out forwards; }
    @keyframes pulse { 0%, 100% { opacity: 1; } 50% { opacity: 0.5; } }
    .animate-pulse-slow { animation: pulse 2s ease-in-out infinite; }
    .btn {
      padding: 10px 20px;
      border: none;
      border-radius: 8px;
      cursor: pointer;
      font-weight: 500;
      transition: all 0.2s;
    }
    .btn-primary { background: #6366f1; color: white; }
    .btn-primary:hover { background: #5558e3; }
    .btn-primary:disabled { background: #374151; cursor: not-allowed; }
    .btn-danger { background: #ef4444; color: white; }
    .btn-danger:hover { background: #dc2626; }
    .btn-yellow { background: #eab308; color: black; }
    .btn-yellow:hover { background: #ca8a04; }
    .input {
      width: 100%;
      padding: 12px 16px;
      background: #1a1a1a;
      border: 1px solid #374151;
      border-radius: 8px;
      color: #e5e5e5;
      font-size: 14px;
      outline: none;
      transition: border-color 0.2s;
    }
    .input:focus { border-color: #6366f1; }
    .input::placeholder { color: #6b7280; }
    .card {
      background: #1a1a1a;
      border-radius: 12px;
      padding: 20px;
    }
    .gradient-text {
      background: linear-gradient(to right, #6366f1, #f472b6);
      -webkit-background-clip: text;
      -webkit-text-fill-color: transparent;
      background-clip: text;
    }
    .tag { padding: 4px 12px; border-radius: 9999px; font-size: 12px; }
    .tag-primary { background: rgba(99, 102, 241, 0.2); color: #818cf8; }
    .tag-red { background: rgba(239, 68, 68, 0.2); color: #f87171; }
    .tag-yellow { background: rgba(234, 179, 8, 0.2); color: #facc15; }

    /* Login Screen */
    .login-screen { height: 100%; display: flex; align-items: center; justify-content: center; padding: 20px; }
    .login-box { width: 100%; max-width: 400px; }
    .login-box h1 { font-size: 28px; text-align: center; margin-bottom: 8px; }
    .login-box p { text-align: center; color: #9ca3af; margin-bottom: 24px; font-size: 14px; }
    .login-box .input { margin-bottom: 12px; }
    .login-box .btn { width: 100%; margin-top: 8px; }
    .login-box .link { display: block; margin-top: 20px; text-align: center; color: #6b7280; font-size: 13px; text-decoration: none; }
    .login-box .link:hover { color: #9ca3af; }

    /* Chat Screen */
    .chat-screen { height: 100%; display: flex; flex-direction: column; overflow: hidden; }
    .chat-header {
      flex-shrink: 0;
      padding: 12px 16px;
      background: #1a1a1a;
      border-bottom: 1px solid #374151;
      display: flex;
      align-items: center;
      justify-content: space-between;
    }
    .chat-header-info h2 { font-size: 16px; font-weight: 600; }
    .chat-header-info p { font-size: 12px; color: #6b7280; }
    .chat-header-status { display: flex; align-items: center; gap: 8px; }
    .status-dot { width: 8px; height: 8px; background: #22c55e; border-radius: 50%; animation: pulse 2s infinite; }
    .chat-header-status span { font-size: 13px; color: #9ca3af; }
    .chat-messages {
      flex: 1;
      overflow-y: auto;
      overflow-x: hidden;
      padding: 16px;
      -webkit-overflow-scrolling: touch;
      scroll-behavior: smooth;
    }
    .chat-empty { text-align: center; padding: 48px 0; color: #6b7280; }
    .message {
      display: flex;
      gap: 12px;
      margin-bottom: 16px;
      animation: fadeIn 0.3s ease-out;
      max-width: 100%;
      overflow-x: hidden;
    }
    .message.user { flex-direction: row-reverse; }
    .message-avatar {
      width: 36px; height: 36px; border-radius: 50%;
      display: flex; align-items: center; justify-content: center;
      flex-shrink: 0; font-size: 13px; font-weight: 600;
    }
    .message.user .message-avatar { background: #f472b6; }
    .message.ai .message-avatar { background: #6366f1; }
    .message.admin .message-avatar { background: #eab308; color: black; }
    .message-content {
      max-width: 75%;
      min-width: 0;
      padding: 12px 16px;
      border-radius: 12px;
      font-size: 14px;
      line-height: 1.5;
      word-wrap: break-word;
      overflow-wrap: break-word;
    }
    .message.user .message-content {
      background: rgba(99, 102, 241, 0.2);
      border-bottom-right-radius: 4px;
    }
    .message.ai .message-content {
      background: #1a1a1a;
      border-bottom-left-radius: 4px;
    }
    .message.admin .message-content {
      background: rgba(234, 179, 8, 0.2);
      border-bottom-left-radius: 4px;
    }
    .message-time { font-size: 11px; color: #6b7280; margin-top: 4px; }
    .typing-indicator { display: flex; gap: 4px; padding: 8px 0; }
    .typing-indicator span {
      width: 8px; height: 8px; background: #6b7280; border-radius: 50%;
      animation: bounce 0.6s infinite;
    }
    .typing-indicator span:nth-child(2) { animation-delay: 0.15s; }
    .typing-indicator span:nth-child(3) { animation-delay: 0.3s; }
    @keyframes bounce { 0%, 100% { transform: translateY(0); } 50% { transform: translateY(-4px); } }
    .chat-input-area {
      flex-shrink: 0;
      padding: 16px;
      background: #1a1a1a;
      border-top: 1px solid #374151;
      position: sticky;
      bottom: 0;
      z-index: 10;
    }
    .chat-input-form { display: flex; gap: 12px; max-width: 800px; margin: 0 auto; }
    .chat-input-form .input { flex: 1; }
    .admin-link { position: fixed; bottom: 16px; right: 16px; color: #6b7280; font-size: 12px; text-decoration: none; z-index: 20; }
    .admin-link:hover { color: #9ca3af; }

    /* Admin Screen */
    .admin-screen { height: 100%; display: flex; }
    .admin-sidebar {
      width: 220px;
      background: #1a1a1a;
      border-right: 1px solid #374151;
      display: flex;
      flex-direction: column;
    }
    .admin-sidebar-header { padding: 16px; border-bottom: 1px solid #374151; }
    .admin-sidebar-header h1 { font-size: 16px; font-weight: 600; }
    .admin-nav { flex: 1; padding: 8px; }
    .admin-nav-btn {
      width: 100%;
      display: flex;
      align-items: center;
      gap: 10px;
      padding: 12px;
      border: none;
      background: transparent;
      color: #9ca3af;
      font-size: 14px;
      border-radius: 8px;
      cursor: pointer;
      transition: all 0.2s;
      text-align: left;
    }
    .admin-nav-btn:hover { background: #0f0f0f; color: #e5e5e5; }
    .admin-nav-btn.active { background: rgba(99, 102, 241, 0.2); color: #818cf8; }
    .admin-content { flex: 1; overflow-y: auto; padding: 24px; }
    .admin-section { max-width: 800px; margin: 0 auto; }
    .admin-section-header { display: flex; align-items: center; justify-content: space-between; margin-bottom: 20px; }
    .admin-section-header h2 { font-size: 20px; font-weight: 600; }
    .form-group { margin-bottom: 16px; }
    .form-group label { display: block; font-size: 13px; color: #9ca3af; margin-bottom: 6px; }
    .form-row { display: grid; grid-template-columns: 1fr 1fr; gap: 16px; }
    .catchphrase-list { display: flex; flex-wrap: wrap; gap: 8px; }
    .catchphrase-tag {
      display: flex;
      align-items: center;
      gap: 4px;
      padding: 4px 10px;
      background: rgba(99, 102, 241, 0.2);
      color: #818cf8;
      border-radius: 6px;
      font-size: 13px;
    }
    .catchphrase-tag button { background: none; border: none; color: inherit; cursor: pointer; font-size: 14px; line-height: 1; }

    /* Chat History */
    .chat-history { display: flex; height: 100%; overflow: hidden; }
    .session-list { width: 280px; border-right: 1px solid #374151; overflow-y: auto; overflow-x: hidden; -webkit-overflow-scrolling: touch; }
    .session-list-header { padding: 16px; border-bottom: 1px solid #374151; }
    .session-list-header h3 { font-size: 14px; color: #9ca3af; }
    .session-item { padding: 16px; border-bottom: 1px solid #374151; cursor: pointer; transition: background 0.2s; }
    .session-item:hover { background: #0f0f0f; }
    .session-item.active { background: #0f0f0f; border-left: 3px solid #6366f1; }
    .session-name { font-weight: 500; font-size: 14px; }
    .session-meta { display: flex; justify-content: space-between; font-size: 12px; color: #6b7280; margin-top: 4px; }
    .chat-detail {
      flex: 1;
      display: flex;
      flex-direction: column;
      overflow: hidden;
    }
    .chat-detail-inner {
      flex: 1;
      padding: 20px;
      overflow-y: auto;
      overflow-x: hidden;
      -webkit-overflow-scrolling: touch;
      scroll-behavior: smooth;
    }
    .chat-detail-header { margin-bottom: 20px; flex-shrink: 0; }
    .chat-detail-header h3 { font-size: 16px; font-weight: 600; }
    .takeover-form { display: flex; gap: 8px; margin-bottom: 20px; flex-shrink: 0; }
    .takeover-form .input { flex: 1; }
    .message-list { display: flex; flex-direction: column; gap: 12px; max-width: 100%; }
    .message-item {
      padding: 12px;
      border-radius: 8px;
      word-wrap: break-word;
      overflow-wrap: break-word;
    }
    .message-item.user-msg { background: rgba(244, 114, 182, 0.1); margin-left: 20px; }
    .message-item.ai-msg { background: rgba(99, 102, 241, 0.1); margin-right: 20px; }
    .message-item.admin-msg { background: rgba(234, 179, 8, 0.1); margin-left: 20px; }
    .message-item-header { display: flex; justify-content: space-between; font-size: 12px; margin-bottom: 6px; }
    .message-item.user-msg .message-item-header { color: #f472b6; }
    .message-item.ai-msg .message-item-header { color: #818cf8; }
    .message-item.admin-msg .message-item-header { color: #facc15; }
    .message-item-content { font-size: 14px; line-height: 1.5; }

    .config-box { background: #1a1a1a; border-radius: 12px; padding: 24px; }
    .config-box h3 { font-size: 16px; font-weight: 600; margin-bottom: 16px; }
    .config-example { margin-top: 16px; padding-top: 16px; border-top: 1px solid #374151; }
    .config-example p { font-size: 13px; color: #818cf8; margin-bottom: 8px; }
    .config-example code { display: block; font-size: 12px; color: #9ca3af; font-family: monospace; }

    .toggle-row { display: flex; align-items: center; justify-content: space-between; padding: 16px 0; border-bottom: 1px solid #374151; }
    .toggle-row:last-child { border-bottom: none; }
    .toggle-info p { font-size: 14px; }
    .toggle-info span { font-size: 12px; color: #6b7280; }
    .toggle { width: 48px; height: 26px; background: #374151; border-radius: 13px; position: relative; cursor: pointer; transition: background 0.2s; }
    .toggle.active { background: #22c55e; }
    .toggle::after { content: ''; position: absolute; top: 3px; left: 3px; width: 20px; height: 20px; background: white; border-radius: 50%; transition: transform 0.2s; }
    .toggle.active::after { transform: translateX(22px); }

    @media (max-width: 768px) {
      .admin-sidebar { display: none; }
      .session-list { width: 100%; }
      .chat-history { flex-direction: column; }
      .form-row { grid-template-columns: 1fr; }
      .chat-messages {
        -webkit-overflow-scrolling: touch;
        overscroll-behavior-y: contain;
      }
      .message-content {
        max-width: 85%;
      }
      body {
        overscroll-behavior-y: none;
      }
    }
  </style>
</head>
<body>
  <div id="app"></div>
  <script>
    // ============ State Management ============
    const APP_VERSION = '1.0.0';

    // Default persona
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

    // State
    let state = {
      view: 'chat', // chat | admin
      adminLoggedIn: false,
      adminTab: 'chats', // chats | persona | model | settings
      // Chat
      visitorName: '',
      joined: false,
      messages: [],
      isLoading: false,
      sessionId: null,
      // Admin
      sessions: [],
      selectedSession: null,
      config: {
        adminPassword: 'admin123',
        persona: { ...DEFAULT_PERSONA },
        modelConfig: { apiUrl: '', apiKey: '', model: 'gpt-4o-mini' },
        serviceEnabled: true,
        bannedUsers: []
      }
    };

    // Load state from localStorage
    function loadState() {
      try {
        const saved = localStorage.getItem('ai-chat-state');
        if (saved) {
          const parsed = JSON.parse(saved);
          state.config = { ...state.config, ...parsed.config };
          state.sessions = parsed.sessions || [];
          if (parsed.sessionId) state.sessionId = parsed.sessionId;
          if (parsed.messages) state.messages = parsed.messages;
          if (parsed.visitorName) state.visitorName = parsed.visitorName;
          if (parsed.joined) state.joined = parsed.joined;
        }
      } catch (e) {}
    }

    // Save state to localStorage
    function saveState() {
      try {
        localStorage.setItem('ai-chat-state', JSON.stringify({
          config: state.config,
          sessions: state.sessions,
          sessionId: state.sessionId,
          messages: state.messages,
          visitorName: state.visitorName,
          joined: state.joined
        }));
      } catch (e) {}
    }

    // Generate ID
    function generateId() {
      return Date.now().toString(36) + Math.random().toString(36).substr(2);
    }

    // Format time
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
6. 绝对不能说伤害她自尊的话`;
    }

    async function callAI(userMessage) {
      const { apiUrl, apiKey, model } = state.config.modelConfig;

      if (apiUrl && apiKey) {
        try {
          const response = await fetch(apiUrl, {
            method: 'POST',
            headers: {
              'Content-Type': 'application/json',
              'Authorization': `Bearer ${apiKey}`
            },
            body: JSON.stringify({
              model: model || 'gpt-4o-mini',
              messages: [
                { role: 'system', content: buildSystemPrompt(state.config.persona) },
                { role: 'user', content: userMessage }
              ],
              max_tokens: 500,
              temperature: 0.9
            })
          });
          if (response.ok) {
            const data = await response.json();
            return data.choices?.[0]?.message?.content || '...';
          }
        } catch (e) {
          console.error('API Error:', e);
        }
      }

      // Demo mode
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
          <div class="login-box card">
            <h1 class="gradient-text">AI 陪聊</h1>
            <p>输入你的昵称开始聊天</p>
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
            <div class="chat-header-info">
              <h2>AI 陪聊</h2>
              <p>Powered by AI</p>
            </div>
            <div class="chat-header-status">
              <div class="status-dot"></div>
              <span>在线</span>
            </div>
          </div>
          <div class="chat-messages" id="chat-messages">
            ${state.messages.length === 0 ? '<div class="chat-empty">开始聊天吧！</div>' : ''}
            ${state.messages.map(m => renderMessage(m)).join('')}
          </div>
          <div class="chat-input-area">
            <form class="chat-input-form" id="chat-form">
              <input type="text" class="input" id="message-input" placeholder="输入消息..." autocomplete="off">
              <button type="submit" class="btn btn-primary" id="send-btn" ${state.isLoading ? 'disabled' : ''}>发送</button>
            </form>
          </div>
          <a href="/admin" class="admin-link" id="admin-float-link">⚙️ 管理</a>
        </div>
      `;
    }

    function renderMessage(msg) {
      const roleClass = msg.role === 'user' ? 'user' : msg.role === 'admin' ? 'admin' : 'ai';
      const avatar = msg.role === 'user' ? '我' : msg.role === 'admin' ? '管' : 'AI';

      return `
        <div class="message ${roleClass}">
          <div class="message-avatar">${avatar}</div>
          <div class="message-content">
            <div>${escapeHtml(msg.content)}</div>
            <div class="message-time">${formatTime(msg.createdAt)}</div>
          </div>
        </div>
      `;
    }

    function renderAdminLogin() {
      return `
        <div class="login-screen">
          <div class="login-box card">
            <h1 class="gradient-text">管理员登录</h1>
            <p style="margin-top: 16px;">输入管理密码进入后台</p>
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
          <div class="chat-detail">
            ${state.selectedSession ? renderChatDetail() : '<div style="text-align: center; color: #6b7280; padding: 48px;">选择一个会话查看详情</div>'}
          </div>
        </div>
      `;
    }

    function renderChatDetail() {
      const s = state.selectedSession;
      return `
        <div class="chat-detail-header">
          <h3>与 ${escapeHtml(s.visitorName)} 的对话</h3>
        </div>
        <div class="takeover-form">
          <input type="text" class="input" id="takeover-input" placeholder="输入接管回复...">
          <button class="btn btn-yellow" id="takeover-btn">接管</button>
        </div>
        <div class="chat-detail-inner">
          <div class="message-list">
            ${(s.messages || []).map(m => `
              <div class="message-item ${m.role === 'user' ? 'user-msg' : m.role === 'admin' ? 'admin-msg' : 'ai-msg'}">
                <div class="message-item-header">
                  <span>${m.role === 'user' ? s.visitorName : m.role === 'admin' ? '管理员接管' : 'AI'}</span>
                  <span>${formatTime(m.createdAt)}</span>
                </div>
                <div class="message-item-content">${escapeHtml(m.content)}</div>
              </div>
            `).join('')}
          </div>
        </div>
      `;
    }

    function renderAdminPersona() {
      const p = state.config.persona;
      return `
        <div class="admin-section">
          <div class="admin-section-header">
            <h2>AI 人设配置</h2>
            <button class="btn btn-primary" id="save-persona">保存更改</button>
          </div>

          <div class="card" style="margin-bottom: 20px;">
            <h3 style="margin-bottom: 16px;">基本信息</h3>
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
            <div class="form-group">
              <label>态度</label>
              <input type="text" class="input" id="p-attitude" value="${escapeHtml(p.attitude)}">
            </div>
          </div>

          <div class="card" style="margin-bottom: 20px;">
            <div style="display: flex; justify-content: space-between; align-items: center; margin-bottom: 12px;">
              <h3>口头禅</h3>
              <button class="btn btn-primary" style="padding: 6px 12px; font-size: 12px;" id="add-catchphrase">+ 添加</button>
            </div>
            <div class="catchphrase-list" id="catchphrase-list">
              ${p.catchphrases.map((c, i) => `
                <div class="catchphrase-tag">
                  <input type="text" value="${escapeHtml(c)}" data-index="${i}" class="catchphrase-input" style="background: none; border: none; color: inherit; width: 80px; font-size: 13px;">
                  <button data-index="${i}">×</button>
                </div>
              `).join('')}
            </div>
          </div>

          <div class="card" style="margin-bottom: 20px;">
            <h3 style="margin-bottom: 16px;">三种状态</h3>
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

          <div class="card" style="margin-bottom: 20px;">
            <h3 style="margin-bottom: 12px;">禁止说的话</h3>
            <div class="catchphrase-list">
              ${p.forbidden.map(f => `<span class="tag tag-red">${escapeHtml(f)}</span>`).join('')}
            </div>
            <p style="font-size: 12px; color: #6b7280; margin-top: 8px;">功能开发中</p>
          </div>

          <div class="card">
            <h3 style="margin-bottom: 12px;">喜欢的话题</h3>
            <div class="catchphrase-list">
              ${p.interests.map(i => `<span class="tag tag-primary">${escapeHtml(i)}</span>`).join('')}
            </div>
            <p style="font-size: 12px; color: #6b7280; margin-top: 8px;">功能开发中</p>
          </div>
        </div>
      `;
    }

    function renderAdminModel() {
      const m = state.config.modelConfig;
      return `
        <div class="admin-section">
          <div class="admin-section-header">
            <h2>AI 模型配置</h2>
            <button class="btn btn-primary" id="save-model">保存更改</button>
          </div>

          <div class="config-box">
            <div class="form-group">
              <label>API 地址</label>
              <input type="text" class="input" id="m-apiUrl" value="${escapeHtml(m.apiUrl)}" placeholder="https://api.openai.com/v1/chat/completions">
              <p style="font-size: 12px; color: #6b7280; margin-top: 6px;">支持 OpenAI 兼容接口，如 OpenAI、Claude、硅基流动等</p>
            </div>
            <div class="form-group">
              <label>API 密钥</label>
              <input type="password" class="input" id="m-apiKey" value="${escapeHtml(m.apiKey)}" placeholder="sk-...">
            </div>
            <div class="form-group">
              <label>模型名称</label>
              <input type="text" class="input" id="m-model" value="${escapeHtml(m.model)}" placeholder="gpt-4o-mini">
              <p style="font-size: 12px; color: #6b7280; margin-top: 6px;">常用模型：gpt-4o、gpt-4o-mini、claude-3-haiku、Qwen/Qwen2.5-7B-Instruct</p>
            </div>

            <div class="config-example">
              <p>配置示例</p>
              <div style="font-size: 13px; color: #9ca3af; line-height: 1.8;">
                <div><strong style="color: #818cf8;">OpenAI</strong></div>
                <div>API: https://api.openai.com/v1/chat/completions</div>
                <div>Key: sk-...</div>
                <div>Model: gpt-4o-mini</div>
                <br>
                <div><strong style="color: #818cf8;">硅基流动</strong></div>
                <div>API: https://api.siliconflow.cn/v1/chat/completions</div>
                <div>Key: sk-...</div>
                <div>Model: Qwen/Qwen2.5-7B-Instruct</div>
              </div>
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

          <div class="card" style="margin-bottom: 20px;">
            <h3 style="margin-bottom: 16px;">服务控制</h3>
            <div class="toggle-row">
              <div class="toggle-info">
                <p>聊天服务</p>
                <span>${state.config.serviceEnabled ? '当前正在服务' : '服务已暂停'}</span>
              </div>
              <div class="toggle ${state.config.serviceEnabled ? 'active' : ''}" id="service-toggle"></div>
            </div>
          </div>

          <div class="card">
            <h3 style="margin-bottom: 16px;">修改管理密码</h3>
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
              <label>确认新密码</label>
              <input type="password" class="input" id="pwd-confirm" placeholder="确认新密码">
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
      // Join chat
      const joinBtn = document.getElementById('join-btn');
      if (joinBtn) {
        joinBtn.addEventListener('click', handleJoin);
        document.getElementById('name-input').addEventListener('keydown', e => {
          if (e.key === 'Enter') handleJoin();
        });
      }

      // Chat form
      const chatForm = document.getElementById('chat-form');
      if (chatForm) {
        chatForm.addEventListener('submit', handleSendMessage);
      }

      // Admin links
      const adminLink = document.getElementById('admin-link') || document.getElementById('admin-float-link');
      if (adminLink) {
        adminLink.addEventListener('click', e => {
          e.preventDefault();
          state.view = 'admin';
          render();
        });
      }

      // Admin login
      const adminLoginBtn = document.getElementById('admin-login-btn');
      if (adminLoginBtn) {
        adminLoginBtn.addEventListener('click', handleAdminLogin);
        document.getElementById('admin-password').addEventListener('keydown', e => {
          if (e.key === 'Enter') handleAdminLogin();
        });
      }

      // Admin tabs
      document.querySelectorAll('.admin-nav-btn[data-tab]').forEach(btn => {
        btn.addEventListener('click', () => {
          state.adminTab = btn.dataset.tab;
          render();
        });
      });

      // Session selection
      document.querySelectorAll('.session-item').forEach(item => {
        item.addEventListener('click', () => {
          const sessionId = item.dataset.sessionId;
          state.selectedSession = state.sessions.find(s => s.id === sessionId);
          document.querySelectorAll('.session-item').forEach(i => i.classList.remove('active'));
          item.classList.add('active');
          document.getElementById('admin-content').innerHTML = renderAdminContent();
          attachAdminContentListeners();
        });
      });

      // Admin content specific listeners
      attachAdminContentListeners();
    }

    function attachAdminContentListeners() {
      // Takeover
      const takeoverBtn = document.getElementById('takeover-btn');
      if (takeoverBtn) {
        takeoverBtn.addEventListener('click', handleTakeover);
      }

      // Save persona
      const savePersonaBtn = document.getElementById('save-persona');
      if (savePersonaBtn) {
        savePersonaBtn.addEventListener('click', handleSavePersona);
      }

      // Add catchphrase
      const addCatchphraseBtn = document.getElementById('add-catchphrase');
      if (addCatchphraseBtn) {
        addCatchphraseBtn.addEventListener('click', () => {
          state.config.persona.catchphrases.push('');
          saveState();
          document.getElementById('admin-content').innerHTML = renderAdminContent();
          attachAdminContentListeners();
        });
      }

      // Catchphrase inputs
      document.querySelectorAll('.catchphrase-input').forEach(input => {
        input.addEventListener('change', e => {
          const idx = parseInt(e.target.dataset.index);
          state.config.persona.catchphrases[idx] = e.target.value;
          saveState();
        });
      });

      // Remove catchphrase
      document.querySelectorAll('.catchphrase-tag button').forEach(btn => {
        btn.addEventListener('click', () => {
          const idx = parseInt(btn.dataset.index);
          state.config.persona.catchphrases.splice(idx, 1);
          saveState();
          document.getElementById('admin-content').innerHTML = renderAdminContent();
          attachAdminContentListeners();
        });
      });

      // Save model
      const saveModelBtn = document.getElementById('save-model');
      if (saveModelBtn) {
        saveModelBtn.addEventListener('click', handleSaveModel);
      }

      // Service toggle
      const serviceToggle = document.getElementById('service-toggle');
      if (serviceToggle) {
        serviceToggle.addEventListener('click', () => {
          state.config.serviceEnabled = !state.config.serviceEnabled;
          saveState();
          render();
        });
      }

      // Change password
      const changePwdBtn = document.getElementById('change-pwd-btn');
      if (changePwdBtn) {
        changePwdBtn.addEventListener('click', handleChangePassword);
      }
    }

    async function handleJoin() {
      const nameInput = document.getElementById('name-input');
      const name = nameInput?.value.trim();
      if (!name) return;

      state.visitorName = name;
      state.sessionId = generateId();
      state.joined = true;
      state.messages = [];

      // Create session
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
      e.preventDefault();
      const input = document.getElementById('message-input');
      const text = input?.value.trim();
      if (!text || state.isLoading) return;

      if (!state.config.serviceEnabled) {
        alert('服务已暂停');
        return;
      }

      state.isLoading = true;
      input.value = '';

      // Add user message
      const userMsg = {
        id: generateId(),
        sessionId: state.sessionId,
        role: 'user',
        content: text,
        createdAt: Date.now()
      };
      state.messages.push(userMsg);

      // Update session
      const session = state.sessions.find(s => s.id === state.sessionId);
      if (session) {
        session.messages = [...state.messages];
        session.lastMessageAt = Date.now();
      }

      render();
      scrollToBottom();

      try {
        const aiResponse = await callAI(text);

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

      // Update main session if it's the same
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
        attitude: document.getElementById('p-attitude')?.value || '',
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
        model: document.getElementById('m-model')?.value || 'gpt-4o-mini'
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
        msgEl.style.color = '#ef4444';
        msgEl.textContent = '当前密码错误';
        return;
      }
      if (!newPwd) {
        msgEl.style.color = '#ef4444';
        msgEl.textContent = '请输入新密码';
        return;
      }
      if (newPwd !== confirm) {
        msgEl.style.color = '#ef4444';
        msgEl.textContent = '新密码确认不一致';
        return;
      }

      state.config.adminPassword = newPwd;
      saveState();
      msgEl.style.color = '#22c55e';
      msgEl.textContent = '密码修改成功！';
      document.getElementById('pwd-current').value = '';
      document.getElementById('pwd-new').value = '';
      document.getElementById('pwd-confirm').value = '';
    }

    // ============ Init ============
    loadState();

    // Check URL hash for admin
    if (window.location.hash === '#admin') {
      state.view = 'admin';
    }

    render();
  </script>
</body>
</html>
