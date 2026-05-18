<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>全球冰封：末日安全屋</title>
    <style>
        * { margin: 0; padding: 0; box-sizing: border-box; }
        html, body { height: 100%; overflow: hidden; font-family: 'Arial', sans-serif; }
        body { background: linear-gradient(135deg, #0a0e17 0%, #1a2744 50%, #0d1526 100%); color: #e0e6ed; }
        
        #game-container { height: 100vh; display: flex; flex-direction: column; }
        
        .header { padding: 10px 15px; background: rgba(0,0,0,0.4); display: flex; justify-content: space-between; align-items: center; }
        .header h1 { font-size: 16px; color: #4fc3f7; text-shadow: 0 0 10px rgba(79,195,247,0.5); }
        .header .chapter { font-size: 12px; color: #81d4fa; }
        
        .stats-panel { display: flex; justify-content: space-around; padding: 8px 0; background: rgba(0,0,0,0.3); border-bottom: 1px solid rgba(79,195,247,0.2); }
        .stat-item { text-align: center; }
        .stat-item .label { font-size: 10px; color: #81d4fa; }
        .stat-item .value { font-size: 14px; font-weight: bold; }
        .stat-item .value.normal { color: #66bb6a; }
        .stat-item .value.warning { color: #ffb74d; }
        .stat-item .value.danger { color: #ef5350; }
        
        .story-area { flex: 1; overflow-y: auto; padding: 15px; }
        .story-image { width: 100%; height: 180px; margin-bottom: 15px; border-radius: 8px; background-size: cover; background-position: center; box-shadow: 0 4px 20px rgba(0,0,0,0.5); }
        .story-text { font-size: 16px; line-height: 1.7; color: #e0e6ed; margin-bottom: 20px; text-indent: 2em; }
        .story-text strong { color: #4fc3f7; }
        .story-text .cold { color: #81d4fa; }
        .story-text .danger { color: #ef5350; }
        .story-text .gold { color: #ffd54f; }
        
        .choices-panel { padding: 15px; background: rgba(0,0,0,0.4); border-top: 1px solid rgba(79,195,247,0.2); }
        .choice-btn { display: block; width: 100%; padding: 14px 18px; margin-bottom: 10px; border: none; border-radius: 8px; font-size: 15px; line-height: 1.5; cursor: pointer; transition: all 0.3s; }
        .choice-btn.neutral { background: linear-gradient(135deg, #37474f 0%, #263238 100%); color: #cfd8dc; }
        .choice-btn.neutral:hover { background: linear-gradient(135deg, #455a64 0%, #37474f 100%); }
        .choice-btn.kind { background: linear-gradient(135deg, #2e7d32 0%, #1b5e20 100%); color: #c8e6c9; }
        .choice-btn.kind:hover { background: linear-gradient(135deg, #388e3c 0%, #2e7d32 100%); }
        .choice-btn.cold { background: linear-gradient(135deg, #1565c0 0%, #0d47a1 100%); color: #bbdefb; }
        .choice-btn.cold:hover { background: linear-gradient(135deg, #1976d2 0%, #1565c0 100%); }
        .choice-btn.angry { background: linear-gradient(135deg, #c62828 0%, #b71c1c 100%); color: #ffcdd2; }
        .choice-btn.angry:hover { background: linear-gradient(135deg, #d32f2f 0%, #c62828 100%); }
        
        .game-over-screen { position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: rgba(0,0,0,0.9); display: flex; flex-direction: column; justify-content: center; align-items: center; z-index: 100; }
        .game-over-screen h2 { font-size: 28px; color: #4fc3f7; margin-bottom: 20px; text-shadow: 0 0 20px rgba(79,195,247,0.8); }
        .game-over-screen p { font-size: 16px; color: #90caf9; margin-bottom: 30px; text-align: center; padding: 0 20px; }
        .game-over-screen .ending-type { font-size: 20px; color: #ffd54f; margin-bottom: 10px; }
        .restart-btn { padding: 12px 40px; background: linear-gradient(135deg, #4fc3f7 0%, #29b6f6 100%); color: #0a0e17; border: none; border-radius: 25px; font-size: 16px; font-weight: bold; cursor: pointer; }
        
        .modal { position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: rgba(0,0,0,0.8); display: flex; justify-content: center; align-items: center; z-index: 99; }
        .modal-content { background: linear-gradient(135deg, #1a2744 0%, #0d1526 100%); border: 1px solid rgba(79,195,247,0.3); border-radius: 12px; padding: 20px; margin: 15px; max-height: 80vh; overflow-y: auto; }
        .modal-content h3 { color: #4fc3f7; margin-bottom: 15px; font-size: 18px; }
        .modal-content .item { display: flex; justify-content: space-between; padding: 8px 0; border-bottom: 1px solid rgba(79,195,247,0.1); }
        .modal-content .item-name { color: #e0e6ed; }
        .modal-content .item-count { color: #66bb6a; font-weight: bold; }
        .modal-close { margin-top: 20px; width: 100%; padding: 10px; background: rgba(79,195,247,0.2); color: #4fc3f7; border: none; border-radius: 6px; cursor: pointer; }
        
        ::-webkit-scrollbar { width: 4px; }
        ::-webkit-scrollbar-track { background: rgba(0,0,0,0.2); }
        ::-webkit-scrollbar-thumb { background: rgba(79,195,247,0.5); border-radius: 2px; }
    </style>
</head>
<body>
    <div id="game-container">
        <div class="header">
            <h1>全球冰封</h1>
            <span class="chapter" id="chapter">第1章：重生</span>
        </div>
        
        <div class="stats-panel">
            <div class="stat-item">
                <div class="label">体温</div>
                <div class="value normal" id="stat-bodytemp">36.5°C</div>
            </div>
            <div class="stat-item">
                <div class="label">饥饿</div>
                <div class="value normal" id="stat-hunger">100%</div>
            </div>
            <div class="stat-item">
                <div class="label">水分</div>
                <div class="value normal" id="stat-water">100%</div>
            </div>
            <div class="stat-item">
                <div class="label">物资</div>
                <div class="value normal" id="stat-supplies">0</div>
            </div>
            <div class="stat-item">
                <div class="label">空间</div>
                <div class="value normal" id="stat-space">100/100</div>
            </div>
        </div>
        
        <div class="story-area" id="story-area">
            <div class="story-image" id="story-image" style="background-image: url('https://neeko-copilot.bytedance.net/api/text_to_image?prompt=frozen%20city%20apocalypse%20snow%20storm%20dark%20sky%20ice%20covered%20buildings%20post-apocalyptic&image_size=landscape_16_9');"></div>
            <div class="story-text" id="story-text">
                冰冷的寒气侵入骨髓，张奕猛地睁开眼睛。

                "我...我不是已经死了吗？"

                记忆如潮水般涌来——零下七十度的极寒，被冻僵的身体，还有邻居王胖子那张贪婪的脸，以及未婚妻方雨晴冷漠的眼神...

                "张奕，你的物资我们就笑纳了，谁让你当初不肯分给我们呢？"

                刀子刺入胸口的剧痛仿佛还在。

                他竟然重生了！

                看了一眼日历——2035年11月15日。

                距离全球冰封，还有<strong>30天</strong>！
            </div>
        </div>
        
        <div class="choices-panel" id="choices-panel">
            <button class="choice-btn cold" onclick="makeChoice('wake_up')">
                冷静分析现状，开始囤货计划
            </button>
            <button class="choice-btn angry" onclick="makeChoice('rage')">
                愤怒不已，立刻去找王胖子和方雨晴算账
            </button>
            <button class="choice-btn kind" onclick="makeChoice('deny')">
                这一定是梦，继续睡觉
            </button>
        </div>
    </div>

    <div class="modal" id="inventory-modal" style="display: none;">
        <div class="modal-content">
            <h3>物资仓库</h3>
            <div id="inventory-list"></div>
            <button class="modal-close" onclick="document.getElementById('inventory-modal').style.display='none';">关闭</button>
        </div>
    </div>

    <div class="game-over-screen" id="game-over" style="display: none;">
        <h2>游戏结束</h2>
        <div class="ending-type" id="ending-type">结局：苟活王者</div>
        <p id="ending-text"></p>
        <button class="restart-btn" onclick="restartGame()">重新开始</button>
    </div>

    <script>
        let gameState = {
            chapter: 1,
            day: 1,
            stats: {
                bodytemp: 36.5,
                hunger: 100,
                water: 100,
                supplies: 0,
                space_used: 0,
                space_max: 100
            },
            inventory: {
                food: 0,
                water: 0,
                medicine: 0,
                fuel: 0,
                materials: 0,
                weapons: 0,
                power: 0
            },
            money: 50000,
            villa_level: 0,
            reputation: 0,
            is_zombie: false,
            ending: '',
            flags: {
                woke_up: false,
                got_space: false,
                met_fang: false,
                killed_wang: false,
                saved_lin: false,
                betrayed: false
            }
        };

        const chapters = [
            {
                id: 1, title: '第1章：重生',
                image: 'frozen%20city%20apocalypse%20snow%20storm%20dark%20sky%20ice%20covered%20buildings%20post-apocalyptic',
                text: '冰冷的寒气侵入骨髓，张奕猛地睁开眼睛。\n\n"我...我不是已经死了吗？"\n\n记忆如潮水般涌来——零下七十度的极寒，被冻僵的身体，还有邻居王胖子那张贪婪的脸，以及未婚妻方雨晴冷漠的眼神...\n\n"张奕，你的物资我们就笑纳了，谁让你当初不肯分给我们呢？"\n\n刀子刺入胸口的剧痛仿佛还在。\n\n他竟然重生了！\n\n看了一眼日历——2035年11月15日。\n\n距离全球冰封，还有<strong>30天</strong>！',
                choices: [
                    { id: 'wake_up', text: '冷静分析现状，开始囤货计划', type: 'cold', next: 2 },
                    { id: 'rage', text: '愤怒不已，立刻去找王胖子和方雨晴算账', type: 'angry', next: 'ending_rage' },
                    { id: 'deny', text: '这一定是梦，继续睡觉', type: 'kind', next: 'ending_dream' }
                ]
            },
            {
                id: 2, title: '第2章：觉醒异能',
                image: 'glowing%20blue%20portal%20infinite%20space%20mysterious%20dimension%20energy%20field%20futuristic',
                text: '张奕强迫自己冷静下来。\n\n前世的惨状让他明白，在末日里，善良只会成为别人利用的工具。\n\n"从今天起，我不再是那个任人宰割的张奕！"\n\n就在这时，一股奇异的力量从体内涌出。\n\n<strong>【无限空间异能觉醒！】</strong>\n\n一个无形的空间在他意识中展开，初始空间100立方米，可以随着升级不断扩大。\n\n这是上天赐予他的重生礼物！\n\n现在，他有了空间，有了预知，还有<strong>30天</strong>的准备时间！',
                choices: [
                    { id: 'start_hoard', text: '立刻开始囤货！先去银行贷款', type: 'cold', next: 3 },
                    { id: 'sell_villa', text: '卖掉别墅换取更多资金', type: 'cold', next: 3 },
                    { id: 'contact_fang', text: '联系方雨晴，看看她的态度', type: 'kind', next: 4 }
                ]
            },
            {
                id: 3, title: '第3章：疯狂囤货',
                image: 'warehouse%20full%20of%20supplies%20food%20water%20canned%20goods%20survival%20gear%20prepper',
                text: '张奕来到银行，凭借未来的信息和口才，成功贷到了<strong>500万</strong>！\n\n他马不停蹄地开始采购：\n\n- 食品区：方便面、罐头、压缩饼干、巧克力...<strong>+500单位</strong>\n- 水区：矿泉水、纯净水、净水器...<strong>+300单位</strong>\n- 药品区：感冒药、抗生素、止痛药、急救包...<strong>+100单位</strong>\n- 燃料区：汽油、柴油、煤油...<strong>+200单位</strong>\n\n沃尔玛仓库被他洗劫一空！\n\n看着空间里堆积如山的物资，张奕嘴角露出冷笑。\n\n末日？我准备好了！',
                choices: [
                    { id: 'buy_weapons', text: '购买武器防身', type: 'cold', next: 5 },
                    { id: 'buy_materials', text: '购买建材改造别墅', type: 'cold', next: 6 },
                    { id: 'continue_buy', text: '继续囤货，不放过任何物资', type: 'cold', next: 3 }
                ]
            },
            {
                id: 4, title: '第4章：方雨晴',
                image: 'beautiful%20woman%20in%20modern%20city%20street%20autumn%20fashion%20elegant%20mysterious',
                text: '张奕拨通了方雨晴的电话。\n\n"张奕？怎么突然想起给我打电话？"\n\n电话那头，方雨晴的声音依旧温柔。\n\n"雨晴，我有件重要的事想跟你说..."\n\n"什么事呀？我正和朋友逛街呢。"\n\n张奕沉默了。他想起前世，就是这个女人，在他最困难的时候，和王胖子一起背叛了他。\n\n"没什么，就是想问问你最近过得怎么样。"\n\n"挺好的，你呢？"\n\n"我也很好。对了，听说最近天气有点反常..."\n\n"哎呀，别担心这些了，我先挂了，拜拜~"\n\n电话挂断，张奕眼神冰冷。\n\n<strong>"果然，江山易改，本性难移。"</strong>',
                choices: [
                    { id: 'break_up', text: '直接提出分手，断绝关系', type: 'cold', next: 5 },
                    { id: 'pretend_love', text: '假装还爱着她，利用她', type: 'angry', next: 5 },
                    { id: 'warn_her', text: '警告她末日即将来临', type: 'kind', next: 5 }
                ]
            },
            {
                id: 5, title: '第5章：武器与防御',
                image: 'military%20weapons%20guns%20ammunition%20knives%20tactical%20gear%20survival%20equipment',
                text: '张奕通过特殊渠道，购买了一批武器：\n\n- 手枪x2，子弹500发\n- 军用匕首x3\n- 弓弩x1，箭矢100支\n- 防刺服、头盔\n\n<strong>【武器库已建立！】</strong>\n\n就在这时，门铃响了。\n\n透过监控，张奕看到了那个熟悉的身影——<strong>王胖子</strong>！\n\n他来了，带着一脸虚伪的笑容。',
                choices: [
                    { id: 'open_door', text: '开门看看他想干什么', type: 'cold', next: 7 },
                    { id: 'ignore', text: '假装不在家，无视他', type: 'cold', next: 6 },
                    { id: 'threaten', text: '隔着门警告他滚蛋', type: 'angry', next: 7 }
                ]
            },
            {
                id: 6, title: '第6章：别墅改造',
                image: 'luxury%20villa%20reinforced%20security%20fortress%20modern%20architecture%20winter%20snow',
                text: '张奕开始改造他的独栋别墅：\n\n<strong>第一层改造：</strong>\n- 窗户全部换成<strong>防弹玻璃</strong>\n- 墙体加装<strong>钢板加固</strong>\n- 安装<strong>地暖系统</strong>\n\n<strong>第二层改造：</strong>\n- 建立<strong>发电室</strong>（柴油发电机）\n- 安装<strong>净水系统</strong>\n- 设置<strong>瞭望台</strong>\n\n<strong>第三层改造：</strong>\n- <strong>防御工事</strong>（机枪位）\n- <strong>物资仓库</strong>\n- <strong>安全屋核心</strong>\n\n<strong>【别墅改造完成！等级：顶级安全屋】</strong>',
                choices: [
                    { id: 'wait_apocalypse', text: '等待末日降临', type: 'cold', next: 8 },
                    { id: 'check_neighbors', text: '观察邻居们的动向', type: 'cold', next: 7 },
                    { id: 'upgrade_more', text: '继续升级防御系统', type: 'cold', next: 6 }
                ]
            },
            {
                id: 7, title: '第7章：王胖子的阴谋',
                image: 'fat%20greedy%20man%20knocking%20door%20suspicious%20expression%20neighbor%20bad%20intentions',
                text: '王胖子站在门口，脸上堆着虚伪的笑容。\n\n"小张啊，好久不见！听说你最近发大财了？"\n\n"有事吗？"\n\n"嘿嘿，是这样的，我最近手头有点紧，想跟你借点钱..."\n\n张奕看着他，眼神冰冷："没钱。"\n\n"别这么小气嘛，大家都是邻居！"王胖子脸色一变，"再说了，你一个人住这么大房子，不如分点地方给我住？"\n\n张奕冷笑："滚。"\n\n"你！"王胖子恼羞成怒，"张奕，你给我等着！等末日来了，看你怎么死！"\n\n说完，他恨恨地离开了。',
                choices: [
                    { id: 'kill_now', text: '现在就解决他，永绝后患', type: 'angry', next: 'ending_tyrant' },
                    { id: 'prepare', text: '加强警戒，等待他自投罗网', type: 'cold', next: 8 },
                    { id: 'talk', text: '跟他讲道理，试图和解', type: 'kind', next: 'ending_betrayed' }
                ]
            },
            {
                id: 8, title: '第8章：末日降临',
                image: 'apocalyptic%20snowstorm%20extreme%20cold%20city%20frozen%20people%20struggling%20survival',
                text: '<strong>【全球冰封降临！】</strong>\n\n气温骤降！\n- 第1天：-20°C\n- 第3天：-40°C\n- 第7天：<strong>-65°C</strong>\n\n城市陷入冰封，电力中断，水源冻结，交通瘫痪。\n\n电视里播放着最后的新闻：\n\n"全球气温持续下降，各国政府已宣布进入紧急状态..."\n\n"建议民众尽量待在室内，保持体温..."\n\n信号中断。\n\n张奕站在温暖的安全屋里，看着窗外的冰天雪地，嘴角露出冷笑。\n\n<strong>"末日...开始了。"</strong>',
                choices: [
                    { id: 'stay_safe', text: '坚守安全屋，静观其变', type: 'cold', next: 9 },
                    { id: 'go_out', text: '外出收集更多物资', type: 'cold', next: 10 },
                    { id: 'help_others', text: '出去救助幸存者', type: 'kind', next: 'ending_hero' }
                ]
            },
            {
                id: 9, title: '第9章：敲门声',
                image: 'frozen%20door%20knocking%20hands%20desperate%20survivors%20snow%20storm%20apocalypse',
                text: '三天后，敲门声响起。\n\n透过监控，张奕看到了一群瑟瑟发抖的邻居。\n\n为首的是王胖子，旁边还站着<strong>方雨晴</strong>！\n\n"张奕！快开门！我们快冻死了！"\n\n"求求你，让我们进去吧！"\n\n方雨晴的声音带着哭腔："张奕，我知道你恨我，但看在我们过去的情分上..."\n\n王胖子怒吼："张奕！你要是不开门，我们就把门砸开！"\n\n他们身后，几个男人拿着木棍和铁棍。',
                choices: [
                    { id: 'refuse', text: '冷漠拒绝，让他们自生自灭', type: 'cold', next: 11 },
                    { id: 'fight', text: '开门迎战，清理垃圾', type: 'angry', next: 12 },
                    { id: 'let_in', text: '心软开门，收留他们', type: 'kind', next: 'ending_betrayed' }
                ]
            },
            {
                id: 10, title: '第10章：外出搜索',
                image: 'snowy%20city%20streets%20abandoned%20cars%20frozen%20shops%20searching%20supplies',
                text: '张奕穿上防寒服，带着武器出门。\n\n街道上一片死寂，偶尔能看到冻僵的尸体。\n\n他来到一家超市，里面已经被洗劫过，但还是找到了一些遗漏的物资。\n\n<strong>【获得：食物+50，药品+20】</strong>\n\n就在他准备离开时，听到了微弱的求救声。\n\n一个年轻女孩被压在倒塌的货架下，冻得瑟瑟发抖。\n\n"救...救我..."',
                choices: [
                    { id: 'save_girl', text: '救下这个女孩', type: 'kind', next: 13 },
                    { id: 'leave', text: '无视她，继续搜索', type: 'cold', next: 9 },
                    { id: 'loot_first', text: '先搜刮完再决定', type: 'cold', next: 9 }
                ]
            },
            {
                id: 11, title: '第11章：道德绑架',
                image: 'angry%20mob%20people%20shouting%20demanding%20supplies%20snow%20cold%20desperate',
                text: '"张奕！你这个冷血动物！"\n\n"见死不救，你会遭报应的！"\n\n王胖子在门外叫嚣着，试图煽动其他人。\n\n方雨晴哭喊道："张奕，我知道你有空间异能，你肯定有很多物资！为什么不肯分给我们一点？"\n\n"就是！大家都是邻居，应该互相帮助！"\n\n"开门！不然我们烧了你的房子！"\n\n他们开始用石头砸门，但防弹玻璃纹丝不动。\n\n张奕坐在监控前，冷漠地看着这一切。\n\n<strong>"道德绑架？可笑。"</strong>',
                choices: [
                    { id: 'ignore_all', text: '继续无视，让他们自取灭亡', type: 'cold', next: 'ending_solo' },
                    { id: 'negotiate', text: '提出用物资换他们离开', type: 'cold', next: 12 },
                    { id: 'taunt', text: '嘲讽他们，让他们更加愤怒', type: 'angry', next: 12 }
                ]
            },
            {
                id: 12, title: '第12章：反杀',
                image: 'violent%20confrontation%20snow%20battle%20defending%20fortress%20apocalypse%20survival',
                text: '王胖子他们开始用汽油烧门！\n\n但安全屋的防火系统立刻启动，将火焰扑灭。\n\n"妈的！这房子怎么这么结实！"\n\n就在他们气急败坏时，张奕打开了防御工事的射击口。\n\n冰冷的枪口对准了王胖子。\n\n"最后一次机会，滚。"\n\n"你敢开枪？！"\n\n<strong>"砰！"</strong>\n\n子弹穿透了王胖子的肩膀。\n\n"啊！！！"\n\n其他人吓得四散奔逃。\n\n方雨晴跪在地上，泪流满面："张奕...求你...不要杀我..."',
                choices: [
                    { id: 'kill_fang', text: '杀了她，完成复仇', type: 'angry', next: 'ending_vengeance' },
                    { id: 'spare', text: '放过她，让她自生自灭', type: 'cold', next: 'ending_solo' },
                    { id: 'capture', text: '活捉她，作为奴隶', type: 'angry', next: 'ending_tyrant' }
                ]
            },
            {
                id: 13, title: '第13章：幸存者',
                image: 'young%20woman%20survivor%20thankful%20expression%20winter%20coat%20snow%20apocalypse',
                text: '张奕救下了女孩。\n\n"谢谢你...谢谢你救了我..."\n\n女孩名叫林雪，是一名大学生。末日来临时，她和同学一起出来购物，结果遭遇了灾难。\n\n"你一个人吗？"\n\n"嗯...就我一个了..."\n\n林雪看着张奕，眼中充满了感激和依赖。\n\n"我...我可以跟着你吗？我什么都能做！"\n\n张奕看着她，陷入了沉思。\n\n前世的背叛还历历在目，但这个女孩看起来真的很无助...',
                choices: [
                    { id: 'accept', text: '收留她，组成小队', type: 'kind', next: 'ending_team' },
                    { id: 'reject', text: '拒绝她，独自生存', type: 'cold', next: 'ending_solo' },
                    { id: 'test', text: '考验她，如果通过就收留', type: 'cold', next: 'ending_team' }
                ]
            }
        ];

        const endings = {
            ending_dream: {
                title: '结局：黄粱一梦',
                text: '张奕以为这只是一场噩梦，继续睡觉。\n\n30天后，末日如期而至。\n\n当冰冷的寒风穿透窗户，当王胖子和方雨晴再次出现在门口时，他才明白这一切都是真的。\n\n但一切都晚了...\n\n<strong>【游戏结束：你在睡梦中迎来了末日】</strong>'
            },
            ending_rage: {
                title: '结局：冲动的代价',
                text: '张奕愤怒地冲去找王胖子和方雨晴算账，结果被他们报警抓进了精神病院。\n\n没有人相信他关于末日的预言。\n\n30天后，全球冰封。\n\n精神病院变成了一座冰牢，张奕在绝望中冻饿而死。\n\n<strong>【游戏结束：冲动毁了一切】</strong>'
            },
            ending_betrayed: {
                title: '结局：善良的代价',
                text: '张奕心软打开了门，收留了王胖子和方雨晴。\n\n但他忘记了，狼永远不会感恩。\n\n在一个深夜，他们趁张奕熟睡时，用枕头闷死了他。\n\n"对不起，张奕，你的物资我们就笑纳了。"\n\n<strong>【游戏结束：善良再次被背叛】</strong>'
            },
            ending_hero: {
                title: '结局：英雄的陨落',
                text: '张奕外出救助幸存者，但他忘记了人性的贪婪。\n\n一群暴徒抢走了他的物资，打断了他的腿，将他扔在冰天雪地中。\n\n在生命的最后时刻，他看到方雨晴和王胖子从他身边走过，却没有看他一眼。\n\n<strong>【游戏结束：圣母心害死了自己】</strong>'
            },
            ending_solo: {
                title: '结局：苟活王者',
                text: '张奕坚守在自己的安全屋里，冷漠地看着外面的世界崩溃。\n\n他的物资足够他存活数十年。\n\n一年后，当外面的幸存者都变成了冰雕，张奕依然在温暖的安全屋里，喝着热茶，看着雪景。\n\n<strong>【游戏结束：你成为了末日的唯一王者】</strong>\n\n恭喜你完成了【苟活王者】结局！'
            },
            ending_vengeance: {
                title: '结局：复仇爽文',
                text: '张奕扣动扳机，结束了方雨晴的生命。\n\n血溅在雪地上，染红了一片。\n\n"这是你欠我的。"\n\n他清理了门口的尸体，继续坚守在安全屋中。\n\n复仇的快感让他感到前所未有的满足。\n\n<strong>【游戏结束：大仇得报，你成为了末日的孤独王者】</strong>\n\n恭喜你完成了【复仇爽文】结局！'
            },
            ending_tyrant: {
                title: '结局：末日暴君',
                text: '张奕没有满足于自卫，他开始主动出击。\n\n他猎杀任何靠近他安全屋的人，掠夺他们的物资。\n\n恐惧让他变得越来越残忍，越来越偏执。\n\n最终，他成为了这片冰封土地上人人畏惧的暴君。\n\n<strong>【游戏结束：你活下来了，但你还是人吗？】</strong>\n\n恭喜你完成了【末日暴君】结局！'
            },
            ending_team: {
                title: '结局：团队求生',
                text: '张奕收留了林雪，两人组成了一个小团队。\n\n林雪聪明能干，成为了张奕的得力助手。\n\n他们一起在末日中生存，互相扶持。\n\n也许，在这个冰冷的世界里，信任和合作才是真正的生存之道。\n\n<strong>【游戏结束：你找到了末日中的温暖】</strong>\n\n恭喜你完成了【团队求生】结局！'
            }
        };

        function updateStats() {
            document.getElementById('stat-bodytemp').textContent = gameState.stats.bodytemp + '°C';
            document.getElementById('stat-hunger').textContent = gameState.stats.hunger + '%';
            document.getElementById('stat-water').textContent = gameState.stats.water + '%';
            document.getElementById('stat-supplies').textContent = gameState.stats.supplies;
            document.getElementById('stat-space').textContent = gameState.stats.space_used + '/' + gameState.stats.space_max;
            
            updateStatColors();
        }

        function updateStatColors() {
            const temp = document.getElementById('stat-bodytemp');
            const hunger = document.getElementById('stat-hunger');
            const water = document.getElementById('stat-water');
            
            setStatColor(temp, gameState.stats.bodytemp, 35, 30);
            setStatColor(hunger, gameState.stats.hunger, 50, 20);
            setStatColor(water, gameState.stats.water, 50, 20);
        }

        function setStatColor(element, value, warning, danger) {
            if (value <= danger) {
                element.className = 'value danger';
            } else if (value <= warning) {
                element.className = 'value warning';
            } else {
                element.className = 'value normal';
            }
        }

        function showChapter(chapterId) {
            const chapter = chapters.find(c => c.id === chapterId);
            if (!chapter) return;
            
            gameState.chapter = chapterId;
            document.getElementById('chapter').textContent = chapter.title;
            
            document.getElementById('story-image').style.backgroundImage = 
                `url('https://neeko-copilot.bytedance.net/api/text_to_image?prompt=${chapter.image}&image_size=landscape_16_9')`;
            
            const text = chapter.text.replace(/\\n/g, '<br><br>');
            document.getElementById('story-text').innerHTML = text;
            
            const choicesPanel = document.getElementById('choices-panel');
            choicesPanel.innerHTML = '';
            
            chapter.choices.forEach(choice => {
                const btn = document.createElement('button');
                btn.className = `choice-btn ${choice.type}`;
                btn.textContent = choice.text;
                btn.onclick = () => makeChoice(choice.id);
                choicesPanel.appendChild(btn);
            });
            
            updateStats();
        }

        function makeChoice(choiceId) {
            const currentChapter = chapters.find(c => c.id === gameState.chapter);
            if (!currentChapter) return;
            
            const choice = currentChapter.choices.find(c => c.id === choiceId);
            if (!choice) return;
            
            if (choiceId === 'start_hoard' || choiceId === 'sell_villa') {
                gameState.money += 500000;
                gameState.inventory.food += 500;
                gameState.inventory.water += 300;
                gameState.inventory.medicine += 100;
                gameState.inventory.fuel += 200;
                gameState.stats.supplies += 1100;
                gameState.stats.space_used += 55;
                gameState.flags.got_space = true;
            } else if (choiceId === 'buy_weapons') {
                gameState.inventory.weapons += 10;
                gameState.stats.supplies += 10;
                gameState.stats.space_used += 5;
            } else if (choiceId === 'buy_materials' || choiceId === 'upgrade_more') {
                gameState.villa_level = Math.min(3, gameState.villa_level + 1);
                gameState.inventory.materials += 200;
                gameState.stats.supplies += 200;
                gameState.stats.space_used += 30;
            } else if (choiceId === 'break_up') {
                gameState.flags.met_fang = true;
            } else if (choiceId === 'kill_now') {
                gameState.flags.killed_wang = true;
            } else if (choiceId === 'save_girl') {
                gameState.flags.saved_lin = true;
            } else if (choiceId === 'go_out') {
                gameState.inventory.food += 50;
                gameState.inventory.medicine += 20;
                gameState.stats.supplies += 70;
                gameState.stats.space_used += 10;
                gameState.stats.bodytemp -= 2;
            }
            
            if (choice.next.toString().startsWith('ending')) {
                showEnding(choice.next);
            } else {
                showChapter(choice.next);
            }
        }

        function showEnding(endingId) {
            const ending = endings[endingId];
            if (!ending) return;
            
            document.getElementById('game-over').style.display = 'flex';
            document.getElementById('ending-type').textContent = ending.title;
            const text = ending.text.replace(/\\n/g, '<br><br>');
            document.getElementById('ending-text').innerHTML = text;
        }

        function restartGame() {
            gameState = {
                chapter: 1,
                day: 1,
                stats: {
                    bodytemp: 36.5,
                    hunger: 100,
                    water: 100,
                    supplies: 0,
                    space_used: 0,
                    space_max: 100
                },
                inventory: {
                    food: 0,
                    water: 0,
                    medicine: 0,
                    fuel: 0,
                    materials: 0,
                    weapons: 0,
                    power: 0
                },
                money: 50000,
                villa_level: 0,
                reputation: 0,
                is_zombie: false,
                ending: '',
                flags: {
                    woke_up: false,
                    got_space: false,
                    met_fang: false,
                    killed_wang: false,
                    saved_lin: false,
                    betrayed: false
                }
            };
            
            document.getElementById('game-over').style.display = 'none';
            showChapter(1);
        }

        showChapter(1);
    </script>
</body>
</html>
