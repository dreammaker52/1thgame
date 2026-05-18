<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>永冻冰封 - 末日抉择</title>
    <style>
        * { margin: 0; padding: 0; box-sizing: border-box; }
        body {
            font-family: 'Microsoft YaHei', sans-serif;
            background: linear-gradient(135deg, #0a1628 0%, #1a3a5c 50%, #0d2137 100%);
            min-height: 100vh;
            color: #e0f0ff;
        }
        .container { max-width: 800px; margin: 0 auto; padding: 20px; }
        .title { text-align: center; color: #00d4ff; font-size: 2.5em; margin-bottom: 10px; }
        .subtitle { text-align: center; color: #8ab8d8; margin-bottom: 30px; }
        .scene-img { width: 100%; height: 250px; object-fit: cover; border-radius: 10px; margin: 20px 0; }
        .story { font-size: 1.1em; line-height: 1.8; margin-bottom: 20px; white-space: pre-wrap; }
        .story strong { color: #00d4ff; }
        .choice {
            display: block;
            width: 100%;
            padding: 15px;
            margin: 10px 0;
            background: rgba(0, 80, 130, 0.8);
            border: 1px solid rgba(100, 150, 200, 0.4);
            border-radius: 10px;
            color: #e0f0ff;
            font-size: 1em;
            text-align: left;
            cursor: pointer;
            transition: all 0.3s;
        }
        .choice:hover { background: rgba(0, 100, 150, 0.9); border-color: #00d4ff; }
        .hidden { display: none; }
        .btn {
            padding: 15px 40px;
            font-size: 1.1em;
            background: linear-gradient(135deg, #0088cc, #0055aa);
            border: none;
            border-radius: 30px;
            color: white;
            cursor: pointer;
            margin-top: 20px;
        }
        .end-screen { text-align: center; padding: 40px; }
        .end-title { color: #00d4ff; font-size: 1.8em; margin-bottom: 20px; }
    </style>
</head>
<body>
    <div class="container">
        <div id="start">
            <h1 class="title">永冻冰封</h1>
            <p class="subtitle">末日抉择 - 冰封世界生存指南</p>
            <img src="https://images.unsplash.com/photo-1544005313-94ddf0286df2?w=800" class="scene-img">
            <p class="story">末日降临，全球急速冰封。你重生回到寒潮爆发前三天，觉醒空间异能，准备迎接末日！</p>
            <button class="btn" onclick="startGame()">开始游戏</button>
        </div>

        <div id="game" class="hidden">
            <img id="sceneImg" class="scene-img" src="">
            <div id="storyText" class="story"></div>
            <div id="choices"></div>
        </div>

        <div id="endScreen" class="end-screen hidden">
            <h2 id="endTitle" class="end-title"></h2>
            <p id="endText" class="story"></p>
            <button class="btn" onclick="restartGame()">重新开始</button>
        </div>
    </div>

    <script>
    const scenes = {
        prologue: {
            img: "https://images.unsplash.com/photo-1544005313-94ddf0286df2?w=800",
            text: "头痛欲裂，我猛地睁开眼睛。\n\n这不是冰封世界里那间冰冷的避难所！\n\n手机日期显示：**2050年11月28日**，距离全球冰封爆发还有三天。\n\n上一世的记忆涌来：零下八十度的极寒、为半块面包互相残杀的人们、被邻居背叛......\n\n不！我重生了！这一次，我绝不会重蹈覆辙！",
            choices: [
                { text: "检查身体，觉醒空间异能", next: "check_body" },
                { text: "立刻清点家中物资", next: "check_supplies" },
                { text: "回忆前世信息制定计划", next: "remember_past" }
            ]
        },
        check_body: {
            img: "https://images.unsplash.com/photo-1531306728370-e2ebd9d7bb99?w=800",
            text: "我闭上眼睛，感受体内的力量。\n\n一股奇异波动升起，意识海中展开无边空间！\n\n这是**随身空间**！时间静止，物资永久保鲜，空间无限！\n\n**空间异能觉醒！Lv.1**！\n\n上一世我错过最佳囤货时机，这一世我要把全世界收入囊中！",
            choices: [
                { text: "立刻出门囤货", next: "start_hoarding" },
                { text: "制定囤货计划", next: "make_plan" }
            ]
        },
        check_supplies: {
            img: "https://images.unsplash.com/photo-1507003211169-0a1dd7228f2d?w=800",
            text: "冰箱里只有剩菜和鸡蛋，柜子里5包方便面、10公斤大米、6罐罐头。\n\n这点物资撑不过三天！\n\n上一世寒潮后电力崩溃、超市被洗劫的记忆清晰无比。\n\n我必须立刻行动！",
            choices: [
                { text: "觉醒空间异能", next: "check_body" },
                { text: "制定囤货计划", next: "make_plan" }
            ]
        },
        remember_past: {
            img: "https://images.unsplash.com/photo-1518709766631-a6a7f45921c3?w=800",
            text: "前世记忆涌现：\n\n第1天：气温-40°C\n第3天：城市混乱\n第7天：变异冰兽出现\n\n关键信息：\n- 安全屋在城南别墅区3号栋（有防空洞）\n- 西郊批发市场物资充足\n- 邻居王强会抢劫\n- 前女友林小雨会背叛",
            choices: [
                { text: "开始囤货", next: "start_hoarding" },
                { text: "前往安全屋选址", next: "find_safehouse" }
            ]
        },
        make_plan: {
            img: "https://images.unsplash.com/photo-1454165804606-c3d57bc86b40?w=800",
            text: "囤货清单：\n- 大米10吨\n- 罐头1000罐\n- 方便面500箱\n- 药品各1000份\n- 防寒服50套\n- 猎枪2支\n- 发电机5台\n\n安全屋选址城南别墅区3号栋。\n\n抵押房产贷款1000万！",
            choices: [
                { text: "前往超市囤货", next: "supermarket" },
                { text: "前往安全屋选址", next: "find_safehouse" },
                { text: "先去银行贷款", next: "get_money" }
            ]
        },
        start_hoarding: {
            img: "https://images.unsplash.com/photo-1530866495561-507c9faab2ed?w=800",
            text: "凌晨三点直奔西郊批发市场。\n\n"大米10吨、面粉5吨、方便面500箱、罐头1000罐、矿泉水2000箱！"\n\n批发商惊讶："你开粮店？"\n\n"比那更重要。"\n\n付款后全部收入空间！",
            choices: [
                { text: "采购药品", next: "pharmacy" },
                { text: "采购武器", next: "gun_shop" },
                { text: "采购防寒装备", next: "outdoor_shop" }
            ]
        },
        supermarket: {
            img: "https://images.unsplash.com/photo-1604719312566-8912e9227c6a?w=800",
            text: "超市开门我就开始清仓：\n\n方便面100箱、压缩饼干50箱、罐头200罐、盐50包、食用油20桶、卫生纸100提。\n\n收银员看傻了："需要送货吗？"\n\n"不用。"刷卡收入空间！",
            choices: [
                { text: "前往医药公司", next: "pharmacy" },
                { text: "前往户外店", next: "outdoor_shop" }
            ]
        },
        pharmacy: {
            img: "https://images.unsplash.com/photo-1587854692152-cbe660dbde88?w=800",
            text: "出示医院采购单进入医药仓库：\n\n抗生素500盒、感冒药1000盒、退烧药500盒、冻伤膏1000支、急救包100个。\n\n管理员疑惑："流感季备货？"\n\n我随口答应。药品采购完成！",
            choices: [
                { text: "继续采购药品", next: "pharmacy" },
                { text: "采购武器", next: "gun_shop" },
                { text: "采购防寒装备", next: "outdoor_shop" }
            ]
        },
        gun_shop: {
            img: "https://images.unsplash.com/photo-1515886657613-9f3515b0c78f?w=800",
            text: "黑市武器店：\n\n猎枪2支、子弹500发、军用匕首5把、战术刀10把、弓弩1把。\n\n"现金还是转账？"\n\n"现金。"\n\n武器到手！这将是末日中保护自己的利器！",
            choices: [
                { text: "前往户外店", next: "outdoor_shop" },
                { text: "前往安全屋选址", next: "find_safehouse" }
            ]
        },
        outdoor_shop: {
            img: "https://images.unsplash.com/photo-1551698618-1dfe5d97d256?w=800",
            text: "极地装备店：\n\n防寒服50套(-80°C)、羽绒睡袋50个、保暖内衣100套、雪地靴30双、头灯50个。\n\n老板："南极考察队？"\n\n"比那更重要。"\n\n防寒装备完成！",
            choices: [
                { text: "前往安全屋选址", next: "find_safehouse" },
                { text: "采购燃料", next: "buy_fuel" }
            ]
        },
        buy_fuel: {
            img: "https://images.unsplash.com/photo-1498084393753-b411b2d26b34?w=800",
            text: "燃料店：\n\n煤炭5吨、木炭1000斤、酒精500瓶、固体燃料500块。\n\n老板惊讶："这么多？"\n\n"备用。"\n\n燃料是末日生存关键！",
            choices: [
                { text: "前往安全屋选址", next: "find_safehouse" }
            ]
        },
        get_money: {
            img: "https://images.unsplash.com/photo-1519452635265-9b1c22a7c31c?w=800",
            text: "来到银行，用房产证抵押贷款1000万。\n\n银行经理："先生，这利率很高..."\n\n"没关系，我不会还太久的。"\n\n开玩笑，寒潮来了谁还还贷款！",
            choices: [
                { text: "开始囤货", next: "supermarket" }
            ]
        },
        find_safehouse: {
            img: "https://images.unsplash.com/photo-1482192505345-5655af888cc4?w=800",
            text: "城南别墅区3号栋！\n\n有建国时期的地下防空洞，非常坚固。\n\n别墅三层加地下两层。\n\n开始改造：加固墙体、安装防弹玻璃、太阳能发电、地热设备、电网陷阱。\n\n安全屋变成堡垒！",
            choices: [
                { text: "开始改造安全屋", next: "setup_safehouse" },
                { text: "转移物资到安全屋", next: "move_supplies" }
            ]
        },
        setup_safehouse: {
            img: "https://images.unsplash.com/photo-1504328345606-18bbc8c9d7d1?w=800",
            text: "用了一整天改造安全屋：\n\n大门换成银行级防爆门\n窗户装上防弹玻璃\n电网覆盖整个围栏\n太阳能板铺满屋顶\n地热系统深入地下\n\n现在这里固若金汤！",
            choices: [
                { text: "转移物资到安全屋", next: "move_supplies" }
            ]
        },
        move_supplies: {
            img: "https://images.unsplash.com/photo-1507003211169-0a1dd7228f2d?w=800",
            text: "将空间中的物资转移到安全屋：\n\n大米、面粉、罐头、药品、武器......\n\n仓库堆满了。\n\n看着如山物资，安全感前所未有的强烈！",
            choices: [
                { text: "等待寒潮降临", next: "day3_final" }
            ]
        },
        day3_final: {
            img: "https://images.unsplash.com/photo-1482784160150-8d23a7800eed?w=800",
            text: "第三天！\n\n手机推送："气温异常下降！政府呼吁储备物资！"\n\n晚了！\n\n新闻播报极端天气，交通瘫痪。\n\n我坐在安全屋里，看着堆积如山的物资。\n\n距离寒潮爆发不到24小时！",
            choices: [
                { text: "最后检查一遍物资和安全屋", next: "final_check" },
                { text: "冒险外出做最后的准备", next: "final_risk" }
            ]
        },
        final_check: {
            img: "https://images.unsplash.com/photo-1504328345606-18bbc8c9d7d1?w=800",
            text: "检查安全屋：\n\n✅ 物资够吃三年\n✅ 能源稳定\n✅ 防御坚固\n✅ 医疗齐全\n✅ 生活舒适\n\n一切就绪！\n\n坐在沙发看着阴沉天空：\n\n"寒潮，来吧。我准备好了！"",
            choices: [
                { text: "迎接寒潮降临", next: "freeze_begins" }
            ]
        },
        final_risk: {
            img: "https://images.unsplash.com/photo-1507003211169-0a1dd7228f2d?w=800",
            text: "街上乱成一团！\n\n人们疯狂抢购。\n\n用空间异能穿梭收集遗漏物资。\n\n看到一个小女孩在寒风中发抖，父母失散了。",
            choices: [
                { text: "带上小女孩", next: "rescue_child" },
                { text: "自己离开", next: "final_check" }
            ]
        },
        rescue_child: {
            img: "https://images.unsplash.com/photo-1541481069435-967c78b91743?w=800",
            text: "抱起小女孩用异能返回安全屋。\n\n"别怕，我会保护你。"\n\n她叫小雨，七岁，父母失散。\n\n也许他们已经......\n\n我决定照顾她，她将是我在末日中要保护的人。",
            choices: [
                { text: "迎接寒潮降临", next: "freeze_begins" }
            ]
        },
        freeze_begins: {
            img: "https://images.unsplash.com/photo-1483664852095-d6cc6870702d?w=800",
            text: "**寒潮降临！**\n\n天空惨白，气温骤降，窗户结满冰花。\n\n街道上人们惊恐奔跑，但来不及了。\n\n**气温：-40°C**！\n\n电力崩溃，城市黑暗。\n\n我坐在温暖安全屋里，听着外面的风声尖叫。\n\n末日，正式开始！",
            choices: [
                { text: "等待第二天", next: "day2_freeze" },
                { text: "外出探索", next: "first_explore" }
            ]
        },
        day2_freeze: {
            img: "https://images.unsplash.com/photo-1483664852095-d6cc6870702d?w=800",
            text: "**第二天**\n\n气温-89°C！\n\n窗外死寂的白色世界，高楼变成冰雕。\n\n手机没信号了。\n\n但我知道，某处有其他幸存者......\n\n还有前世记忆中可怕的变异冰兽！\n\n明天开始，我将踏足冰封地狱！",
            choices: [
                { text: "继续等待", next: "day3_wait" },
                { text: "外出探索", next: "first_explore" }
            ]
        },
        day3_wait: {
            img: "https://images.unsplash.com/photo-1507003211169-0a1dd7228f2d?w=800",
            text: "继续等待。\n\n整理物资、修炼异能。\n\n气温降至-105°C，风雪未减。\n\n但我准备好了！",
            choices: [
                { text: "外出探索", next: "first_explore" }
            ]
        },
        first_explore: {
            img: "https://images.unsplash.com/photo-1478719059408-592965723cbc?w=800",
            text: "穿上防寒服打开安全屋大门。\n\n**气温：-95°C**！\n\n街道一片狼藉，车辆冻成冰块。\n\n突然，一只变异冰兽冲出来！\n\n锋利爪子，冰蓝眼睛，非常危险！",
            choices: [
                { text: "使用空间异能战斗", next: "fight_beast" },
                { text: "退回安全屋", next: "return_safehouse" },
                { text: "尝试绕开它", next: "avoid_beast" }
            ]
        },
        fight_beast: {
            img: "https://images.unsplash.com/photo-15573085209c98cb70f5a5?w=800",
            text: "冰兽扑来！\n\n集中精神发动空间异能！\n\n**空间切割！**\n\n无形刃斩向冰兽！\n\n惨叫中冰兽被切成两半，冰屑飞溅！\n\n战斗胜利！获得经验，发现能量晶体！",
            choices: [
                { text: "继续探索", next: "explore_city" },
                { text: "返回安全屋", next: "return_safehouse" }
            ]
        },
        return_safehouse: {
            img: "https://images.unsplash.com/photo-1504328345606-18bbc8c9d7d1?w=800",
            text: "退回安全屋，关上大门。\n\n外面太危险了。\n\n整理收获，开始修炼空间异能。",
            choices: [
                { text: "修炼异能", next: "train_ability" },
                { text: "等待", next: "day2_freeze" }
            ]
        },
        avoid_beast: {
            img: "https://images.unsplash.com/photo-1478719059408-592965723cbc?w=800",
            text: "小心绕开冰兽继续探索。\n\n在废弃超市发现物资：罐头、方便面、水、日用品。\n\n虽然不多，但聊胜于无。",
            choices: [
                { text: "继续探索", next: "explore_city" },
                { text: "返回安全屋", next: "return_safehouse" }
            ]
        },
        explore_city: {
            img: "https://images.unsplash.com/photo-1519681393784-d120267933ba?w=800",
            text: "在废弃商场发现更多物资：食物、日用品、种子！\n\n还发现寒潮研究资料......\n\n提到寒潮可能不是自然现象，与人为或外星干预有关！",
            choices: [
                { text: "返回安全屋", next: "rest_and_plan" }
            ]
        },
        rest_and_plan: {
            img: "https://images.unsplash.com/photo-1507003211169-0a1dd7228f2d?w=800",
            text: "返回安全屋整理收获。\n\n物资足够支撑一段时间，但需要寻找更多资源。\n\n也许该找其他幸存者了，或者寻找传说中的地下避难所......",
            choices: [
                { text: "寻找幸存者", next: "find_survivors" },
                { text: "修炼异能", next: "train_ability" }
            ]
        },
        train_ability: {
            img: "https://images.unsplash.com/photo-1531306728370-e2ebd9d7bb99?w=800",
            text: "修炼空间异能！\n\n获得：\n- **空间感知(Lv.1)** - 感知能量波动\n- **空间压缩(Lv.1)** - 压缩空间产生力量\n- **空间跳跃(Lv.1)** - 短距离瞬移\n\n异能进化中！",
            choices: [
                { text: "继续修炼高级技能", next: "train_advanced" },
                { text: "外出探索", next: "first_explore" },
                { text: "寻找幸存者", next: "find_survivors" }
            ]
        },
        train_advanced: {
            img: "https://images.unsplash.com/photo-1531306728370-e2ebd9d7bb99?w=800",
            text: "深入修炼！\n\n获得：\n- **空间切割(Lv.2)** - 产生锋利空间刃\n- **空间屏障(Lv.2)** - 制造防御屏障\n\n还能融合其他元素！",
            choices: [
                { text: "融合冰元素", next: "fusion_ice" },
                { text: "继续提升空间异能", next: "maximize_space" },
                { text: "外出探索", next: "first_explore" }
            ]
        },
        fusion_ice: {
            img: "https://images.unsplash.com/photo-1531306728370-e2ebd9d7bb99?w=800",
            text: "尝试融合空间与冰元素！\n\n**嗡——**\n\n强大力量爆发！\n\n获得：\n- **冰空间(Lv.1)** - 创造冰封空间\n- **空间冻结(Lv.1)** - 冻结空间物体\n\n异能达到新境界！",
            choices: [
                { text: "继续修炼", next: "train_advanced" },
                { text: "外出探索", next: "first_explore" }
            ]
        },
        maximize_space: {
            img: "https://images.unsplash.com/photo-1531306728370-e2ebd9d7bb99?w=800",
            text: "专注提升空间异能！\n\n获得：\n- **空间压缩(Lv.3)** - 制造黑洞引力\n- **空间跳跃(Lv.3)** - 长距离跳跃\n- **空间领域(Lv.1)** - 创造自己的空间领域\n\n空间异能达到巅峰！",
            choices: [
                { text: "继续修炼", next: "train_advanced" },
                { text: "外出探索", next: "first_explore" }
            ]
        },
        find_survivors: {
            img: "https://images.unsplash.com/photo-1529257414772-1960b7bea4eb?w=800",
            text: "发现亮灯建筑！\n\n幸存者营地！\n\n门口有人站岗。\n\n"站住！"\n\n举起手表示无恶意。\n\n进入营地，约50人，由退伍军人老张领导。",
            choices: [
                { text: "加入营地", next: "join_camp" },
                { text: "分享物资", next: "share_supplies" },
                { text: "询问情报后离开", next: "leave_camp" }
            ]
        },
        join_camp: {
            img: "https://images.unsplash.com/photo-1529257414772-1960b7bea4eb?w=800",
            text: "加入营地！\n\n老张感激："物资快没了，很多人挨饿。"\n\n拿出食物药品分给大家。\n\n认识了医生林雪、工程师小王、厨师小李。\n\n这里可以成为真正的避难所！",
            choices: [
                { text: "帮助营地加强防御", next: "help_defense" },
                { text: "继续外出探索", next: "first_explore" }
            ]
        },
        share_supplies: {
            img: "https://images.unsplash.com/photo-1507003211169-0a1dd7228f2d?w=800",
            text: "分享物资给营地！\n\n取出食物、药品、燃料。\n\n老张激动："太感谢了！"\n\n看着人们感激的眼神，感受到久违的温暖。\n\n末日中，信任和互助才是希望！",
            choices: [
                { text: "帮助营地加强防御", next: "help_defense" },
                { text: "离开营地", next: "leave_camp" }
            ]
        },
        help_defense: {
            img: "https://images.unsplash.com/photo-1504328345606-18bbc8c9d7d1?w=800",
            text: "用空间异能加强防御！\n\n设置陷阱预警系统，用冰块建造防御墙。\n\n幸存者惊叹："有你在我们安全多了！"",
            choices: [
                { text: "前往军火库探索", next: "arsenal" }
            ]
        },
        leave_camp: {
            img: "https://images.unsplash.com/photo-1519681393784-d120267933ba?w=800",
            text: ""谢谢情报，我该走了。"\n\n转身消失在风雪中。\n\n不信任任何人是生存铁律。\n\n但看着身后温暖灯火，心中有一丝说不出的滋味......",
            choices: [
                { text: "前往军火库", next: "arsenal" }
            ]
        },
        arsenal: {
            img: "https://images.unsplash.com/photo-1527192491265-7e15c55b1ed2?w=800",
            text: "找到军用物资储备库！\n\n发现大量武器弹药、军用防寒装备、医疗物资、先进科技设备！\n\n还有秘密通道通向实验室，里面有寒潮研究资料......\n\n提到远古文明！",
            choices: [
                { text: "深入探索实验室", next: "deep_arsenal" },
                { text: "返回安全屋研究资料", next: "frozen_world_start" }
            ]
        },
        deep_arsenal: {
            img: "https://images.unsplash.com/photo-1569230516306-5a8cb5586399?w=800",
            text: "深入探索！\n\n发现隐藏地下室，有更先进武器和研究资料！\n\n资料显示寒潮与远古遗迹、高维文明有关！",
            choices: [
                { text: "前往北方寻找远古遗迹", next: "journey_north" },
                { text: "返回安全屋研究资料", next: "frozen_world_start" }
            ]
        },
        frozen_world_start: {
            img: "https://images.unsplash.com/photo-1478719059408-592965723cbc?w=800",
            text: "返回安全屋研究资料。\n\n发现寒潮可能与远古文明实验有关。\n\n需要前往北方寻找遗迹解开真相......",
            choices: [
                { text: "向北方的冰原进发", next: "journey_north" },
                { text: "寻找其他幸存者基地", next: "find_survivors" },
                { text: "深入修炼空间异能", next: "train_advanced" }
            ]
        },
        journey_north: {
            img: "https://images.unsplash.com/photo-1478719059408-592965723cbc?w=800",
            text: "踏上北方旅程！\n\n无边冰原延伸，寒风如刀。\n\n遇到巨大冰裂缝，对面有山洞入口！",
            choices: [
                { text: "用空间异能通过", next: "use_space_ability" },
                { text: "绕路寻找通路", next: "find_alternate_path" },
                { text: "冒险跳过去", next: "jump_gap" }
            ]
        },
        use_space_ability: {
            img: "https://images.unsplash.com/photo-1531306728370-e2ebd9d7bb99?w=800",
            text: "发动空间跳跃！\n\n身体消失又在对面出现！\n\n无视距离障碍！\n\n成功到达！山洞入口就在前方......",
            choices: [
                { text: "进入山洞", next: "explore_cave" }
            ]
        },
        find_alternate_path: {
            img: "https://images.unsplash.com/photo-1478719059408-592965723cbc?w=800",
            text: "绕路寻找通路，走了很长一段终于找到。\n\n继续前进，山洞越来越近......",
            choices: [
                { text: "进入山洞", next: "explore_cave" }
            ]
        },
        jump_gap: {
            img: "https://images.unsplash.com/photo-1531306728370-e2ebd9d7bb99?w=800",
            text: "助跑！跳跃！\n\n空中感受到死亡威胁......\n\n成功了！稳稳落地！\n\n惊险但节省时间！",
            choices: [
                { text: "进入山洞", next: "explore_cave" }
            ]
        },
        explore_cave: {
            img: "https://images.unsplash.com/photo-1518791841217-8f162f1e1131?w=800",
            text: "山洞温暖！\n\n深处有巨大地下空间！建筑遗迹！\n\n古老石柱刻满符文！\n\n发光蓝色晶体！\n\n这是......古代文明遗址？",
            choices: [
                { text: "靠近观察晶体", next: "observe_crystal" },
                { text: "寻找更多信息", next: "search_temple" }
            ]
        },
        observe_crystal: {
            img: "https://images.unsplash.com/photo-1518791841217-8f162f1e1131?w=800",
            text: "走近发光晶体！\n\n悬浮空中散发柔和光芒！\n\n手靠近时能量涌入体内！\n\n空间异能升级！\n\n听到声音......",
            choices: [
                { text: "收集晶体", next: "collect_crystals" },
                { text: "隐藏起来观察", next: "hide_and_watch" }
            ]
        },
        collect_crystals: {
            img: "https://images.unsplash.com/photo-1531306728370-e2ebd9d7bb99?w=800",
            text: "收集能量晶体！\n\n蕴含强大空间能量！\n\n收集时通道口出现身影......",
            choices: [
                { text: "隐藏起来观察", next: "hide_and_watch" },
                { text: "主动现身询问", next: "meet_newcomer" }
            ]
        },
        search_temple: {
            img: "https://images.unsplash.com/photo-1518791841217-8f162f1e1131?w=800",
            text: "搜索遗迹！\n\n墙上发现壁画文字！\n\n解读得知这里研究空间能量......\n\n寒潮可能与此有关！",
            choices: [
                { text: "观察晶体", next: "observe_crystal" },
                { text: "继续深入探索", next: "deep_explore_temple" }
            ]
        },
        hide_and_watch: {
            img: "https://images.unsplash.com/photo-1475924156734-496f6cac6ec1?w=800",
            text: "躲在石柱后！\n\n穿防寒服女子出现！\n\n手持武器警惕张望："有人吗？"\n\n她在找晶体？",
            choices: [
                { text: "出来与她交流", next: "meet_newcomer" },
                { text: "继续观察", next: "continue_observe" }
            ]
        },
        continue_observe: {
            img: "https://images.unsplash.com/photo-1475924156734-496f6cac6ec1?w=800",
            text: "继续观察！\n\n女子小心收集晶体，似乎很熟悉这里。\n\n突然停下："找到了......"\n\n拿出古老盒子要放东西......",
            choices: [
                { text: "现身询问", next: "meet_newcomer" }
            ]
        },
        meet_newcomer: {
            img: "https://images.unsplash.com/photo-1475924156734-496f6cac6ec1?w=800",
            text: ""你是谁？"走出来！\n\n女子吓一跳举武器，见无恶意放松。\n\n她是考古学家林雪，一直在研究遗迹。\n\n"这里是远古文明研究基地，他们研究空间能量，但突然消失了。寒潮可能与此有关......"",
            choices: [
                { text: "与她一起探索遗迹", next: "deep_explore_temple" },
                { text: "分享信息后离开", next: "share_and_leave" }
            ]
        },
        share_and_leave: {
            img: "https://images.unsplash.com/photo-1507003211169-0a1dd7228f2d?w=800",
            text: "分享物资信息！\n\n"谢谢，也许会再见。"\n\n互相留联系方式后离开。\n\n末日中多一个朋友不是坏事......",
            choices: [
                { text: "继续探索遗迹", next: "deep_explore_temple" }
            ]
        },
        deep_explore_temple: {
            img: "https://images.unsplash.com/photo-1518791841217-8f162f1e1131?w=800",
            text: "和林雪深入探索！\n\n在最深处，我们发现了一个巨大的大厅。\n\n大厅中央，一块巨大的紫色晶体悬浮在空中。\n\n它散发着强大的能量波动，似乎是整个遗迹的核心。\n\n"这就是传说中的'虚空水晶'，"林雪说，"它是远古文明的能量源泉。"\n\n但我们也发现，这块水晶正在不稳定地波动......",
            choices: [
                { text: "触碰核心", next: "touch_core" },
                { text: "谨慎研究", next: "study_core" }
            ]
        },
        study_core: {
            img: "https://images.unsplash.com/photo-1531306728370-e2ebd9d7bb99?w=800",
            text: "我决定谨慎研究，不贸然触碰。\n\n通过林雪的专业知识和我的空间感知能力，我们发现了一些惊人的事实：\n\n1. 这块晶体是远古文明的能量核心\n2. 寒潮的爆发可能与这个核心的能量波动有关\n3. 触碰它可能会带来巨大的力量，但也可能引发灾难",
            choices: [
                { text: "决定触碰核心", next: "touch_core" },
                { text: "返回地面", next: "final_chapter_start" }
            ]
        },
        touch_core: {
            img: "https://images.unsplash.com/photo-1531306728370-e2ebd9d7bb99?w=800",
            text: "我伸出手，触碰那块悬浮的紫色晶体。\n\n一股强大的能量涌入我的身体！\n\n**空间异能突破！Lv.MAX！**\n\n我获得了创造空间的能力！\n\n但与此同时，一段信息涌入我的脑海......\n\n原来，这场寒潮是远古文明的实验失败导致的。",
            choices: [
                { text: "进入最终章", next: "final_chapter_start" }
            ]
        },
        final_chapter_start: {
            img: "https://images.unsplash.com/photo-1419242902214-272b3f66ee7a?w=800",
            text: "**最终章：星辰归途**\n\n十年后......\n\n冰封的世界正在缓慢复苏。\n\n我站在安全屋的瞭望塔上，看着远处正在重建的城市。\n\n林雪、小雨、老张......所有曾经并肩作战的伙伴都在这里。\n\n人类文明，终于在废墟中找到了新的希望。\n\n但我知道，这只是开始。\n\n远古遗迹中的秘密，高维文明的存在，都在呼唤着我......",
            choices: [
                { text: "前往远古遗迹，揭开最终秘密", next: "final_secret" },
                { text: "继续领导幸存者联盟", next: "lead_alliance" },
                { text: "闭关修炼，突破终极异能", next: "final_evolution" }
            ]
        },
        final_secret: {
            img: "https://images.unsplash.com/photo-1518709766631-a6a7f45921c3?w=800",
            text: "我再次来到远古遗迹。\n\n这一次，我已经足够强大。\n\n核心晶体散发出耀眼的光芒，一段段信息涌入我的脑海......\n\n原来，这场寒潮并非自然灾难，而是高维文明的实验！\n\n他们在测试人类的生存极限，观察文明的韧性。",
            choices: [
                { text: "拒绝高维文明，守护人类", next: "reject_higher" },
                { text: "接受邀请，超越维度", next: "ascend_ending" },
                { text: "谈判争取权益", next: "negotiate_ending" }
            ]
        },
        reject_higher: {
            img: "https://images.unsplash.com/photo-1507525428034-b723cf961d3e?w=800",
            text: ""我拒绝！人类不需要被实验，我们有自己的路！"\n\n高维存在意外又赞赏："有趣......看看人类能走多远。"\n\n光芒笼罩地球，寒潮消退，阳光照耀！\n\n**人类赢得生存权利！**",
            choices: [
                { text: "迎接新世界", next: "good_ending" }
            ]
        },
        ascend_ending: {
            img: "https://images.unsplash.com/photo-1506905925346-21bda4d32df4?w=800",
            text: "选择接受！\n\n耀眼光芒包裹身体蜕变！\n\n感受超越时空的力量！\n\n最后看一眼蓝色星球......\n\n也许有一天会以新形态回来......\n\n**【飞升结局：维度超越者】**",
            isEnding: true,
            title: "飞升结局：维度超越者"
        },
        negotiate_ending: {
            img: "https://images.unsplash.com/photo-1507003211169-0a1dd7228f2d?w=800",
            text: ""我可以成为一员，但有条件：让地球恢复，人类自由发展！"\n\n高维沉默："可以，但你必须成为使者引导人类。"\n\n点头同意！寒潮退去！\n\n成为连接两个维度的桥梁！\n\n**【使者结局：维度使者】**",
            isEnding: true,
            title: "使者结局：维度使者"
        },
        lead_alliance: {
            img: "https://images.unsplash.com/photo-1522708323590-d24dbb6b0267?w=800",
            text: "继续领导幸存者联盟！\n\n人类文明快速复苏：重建城市、建立政府、发展科技、与其他基地联系！\n\n十年后崭新国家崛起！\n\n站在新首都城墙看繁荣景象！\n\n**【领袖结局：新纪元开创者】**",
            isEnding: true,
            title: "领袖结局：新纪元开创者"
        },
        final_evolution: {
            img: "https://images.unsplash.com/photo-1531306728370-e2ebd9d7bb99?w=800",
            text: "闭关突破空间异能极限！\n\n感受到空间本质！\n\n**空间异能突破！Lv.MAX！**\n\n获得创造独立位面能力！\n\n创造温暖安全新世界，带领幸存者迁移！\n\n**【创世结局：新世界创造者】**",
            isEnding: true,
            title: "创世结局：新世界创造者"
        },
        good_ending: {
            img: "https://images.unsplash.com/photo-1470071459604-3b5ec3a7fe05?w=800",
            text: "**大结局：星辰归途**\n\n阳光重新照耀大地，冰雪开始融化。\n\n十年的冰封岁月终于结束。\n\n我站在重建的城市中，看着人们忙碌的身影。\n\n林雪成为了新政府的科技部长，小雨已经长大，成为了一名医生。\n\n老张的幸存者营地发展成了一座城市。\n\n而我，选择了一个新的身份——一个普通的公民。\n\n经历了这么多，我终于可以过上平静的生活了。\n\n现在，让我们享受这来之不易的和平吧。\n\n**人类，终将走向星辰大海！**\n\n**【大结局：星辰归途】**",
            isEnding: true,
            title: "大结局：星辰归途"
        }
    };

    function startGame() {
        document.getElementById("start").classList.add("hidden");
        document.getElementById("game").classList.remove("hidden");
        document.getElementById("endScreen").classList.add("hidden");
        showScene("prologue");
    }

    function showScene(sceneId) {
        const scene = scenes[sceneId];
        if (!scene) {
            console.error("Scene not found: " + sceneId);
            return;
        }
        document.getElementById("sceneImg").src = scene.img;
        document.getElementById("storyText").innerHTML = scene.text.replace(/\*\*(.*?)\*\*/g, "<strong>$1</strong>");
        const choicesDiv = document.getElementById("choices");
        choicesDiv.innerHTML = "";
        if (scene.isEnding) {
            document.getElementById("game").classList.add("hidden");
            document.getElementById("endScreen").classList.remove("hidden");
            document.getElementById("endTitle").textContent = scene.title;
            document.getElementById("endText").innerHTML = scene.text.replace(/\*\*(.*?)\*\*/g, "<strong>$1</strong>");
            return;
        }
        scene.choices.forEach(function(choice) {
            const btn = document.createElement("button");
            btn.className = "choice";
            btn.textContent = choice.text;
            btn.onclick = function() { showScene(choice.next); };
            choicesDiv.appendChild(btn);
        });
    }

    function restartGame() {
        document.getElementById("endScreen").classList.add("hidden");
        document.getElementById("start").classList.remove("hidden");
    }
    </script>
</body>
</html>
