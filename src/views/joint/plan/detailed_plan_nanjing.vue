<template>
  <div>
    <div id="exportContent">
      <div style="text-align: center">
        <h1>洪涝灾害预案</h1>
      </div>
      <div>
        <div>
          <h1 v-if="ChosedItem.some(item => item.startsWith('1'))">1 总则</h1>
          <div v-if="ChosedItem.indexOf('1-1')!==-1">
            <h2>1.1 编制目的</h2>
            <p>
              以习近平新时代中国特色社会主义思想为指导，深入贯彻落实“两个坚持、三个转变”防灾减灾救灾新理念，依法、规范、有序地开展防汛工作，
              全面提升我市洪涝灾害预防和应急处置能力，最大限度减少灾害损失，保障人民群众生命财产安全和经济社会稳定发展。
            </p>
          </div>
          <div v-if="ChosedItem.indexOf('1-2')!==-1">
            <h2>1.2 编制依据</h2>
            <p>
              依据《中华人民共和国水法》《中华人民共和国防洪法》《中华人民共和国突发事件应对法》《中华人民共和国防汛条例》
              《国家突发事件总体应急预案》《国家防汛抗旱应急预案》《江苏省防洪条例》《江苏省防汛抗旱应急预案》以及《南京市防洪办法》《南京市突发事件总体应急预案》
              等法律法规和有关规定，制定本预案。
            </p>
          </div>
          <div v-if="ChosedItem.indexOf('1-3')!==-1">
            <h2>1.3 适用范围</h2>
            <p>
              适用于我市境内发生的（以及邻近地区发生但产生重大影响的）洪涝灾害预防和应急处置。洪涝灾害包括：江河湖库洪水、雨涝、山洪和堤防决口、
              水库溃坝、涵闸泵垮塌等及其引发的次生、衍生灾害。
            </p>
          </div>
          <div v-if="ChosedItem.indexOf('1-4')!==-1">
            <h2>1.4 工作原则</h2>
            <p>
              1.4.1 以人为本，安全第一。坚持把人民生命财产安全放在首位，最大限度减少灾害造成的危害和损失。
            </p>
            <p>
              1.4.2 统一指挥，分级分部门负责。各级政府是本行政区域内防汛工作的责任主体，实行行政首长负责制，统一指挥、分级分部门负责。
            </p>
            <p>
              1.4.3 以防为主，防抗救结合。预防与处置相结合，常态与非常态相结合，强化预防和应急处置工作的规范化、制度化、法制化。
            </p>
            <p>
              1.4.4 统筹兼顾，服从全局。坚持因地制宜、城乡统筹、流域区域兼顾，突出重点，局部利益服从全局利益。
            </p>
            <p>
              1.4.5 快速反应，协同应对。坚持第一时间响应，实行公众参与、军民结合、专群结合、平战结合、上下联动，及时、高效地开展预防和应急处置。
            </p>
          </div>
        </div>
        <div>
          <h1 v-if="ChosedItem.some(item => item.startsWith('2'))">2 组织体系</h1>
          <p v-if="ChosedItem.some(item => item.startsWith('2'))">
            市政府设立市防汛防旱指挥部（以下简称市防指），下设滁河、沙洲圩地区防汛联防指挥部。
            区政府设立本级防汛防旱指挥机构。有关单位可根据需要设立防汛指挥机构，负责本单位防汛工作，并服从当地防汛指挥机构的统一指挥。
          </p>
          <div v-if="ChosedItem.indexOf('2-1')!==-1">
            <h2>2.1 市防汛防旱指挥部</h2>
            <p>
              市防指负责组织领导全市防汛工作，其日常办事机构为市防汛防旱指挥部办公室（以下简称市防办）。
            </p>
            <h3>2.1.1 市防指组成</h3>
            <p>
              市防指由市长任指挥，分管副市长、下设滁河、沙洲圩地区防汛联防指挥部。 市政府分管副秘书长、市水务局局长、市应急管理局局长任副指挥，
              市委宣传部，市发展和改革委员会、教育局、工业和信息化局、公安局、财政局、规划和自然资源局、生态环境局、城乡建设委员会、住房保障和房产局、
              交通运输局、城市管理局、绿化园林局、农业农村局、文化和旅游局、卫生健康委员会、人民防空办公室、机关事务管理局、供销合作总社，
              中山陵园管理局、市气象局、市公安局交通管理局、消防救援支队、海事局、水务局、应急管理局、城建集团、交通集团，
            </p>
            <h3>2.1.2 市防指职责</h3>
            <p>
              市防指负责组织领导全市防汛工作，制定防汛政策、规程和工作制度等，部署防汛各项准备工作，决定启动、变更和结束防汛应急响应，组织防汛抢险救灾，
              协调灾后处置。市委宣传部：负责把握全市防汛宣传工作导向，组织、协调、指导新闻媒体做好防汛工作新闻报道。
            </p>
            <h3> 2.1.3 市防指成员单位职责</h3>
            <p>
              南京警备区: 负责组织协调现役部队、预备役部队和民兵，支援地方抗洪抢险，协助组织群众安全转移安置。
            </p>
            <p>
              武警南京支队：负责调动武警支援地方抗洪 抢险，协助公安机关维护社会治安秩序，协助组织群众安全转移安置。
            </p>
            <p>
              市发展和改革委员会：负责统筹安排防汛工程建设、除险加固、水毁工程修复以及防汛抢险救灾资金计划；承担市级救灾物资储备和管理，按照指令组织调运；协调防汛抢险救灾所需电力的供应和调度；负责滁河地区防汛联防指挥工作。
            </p>
            <p>
              市教育局：负责全市学校防汛工作，发布学生转移、学校停课等通知。
            </p>
            <p>
              市公安局：负责维护社会治安秩序，依法打击阻扰防汛工作、造谣惑众和盗窃、哄抢防汛物资以及破坏防汛设施的违法犯罪活动；协助妥善处置因防汛引发的群体性治安事件，协助组织群众安全转移安置。
            </p>
            <p>
              市财政局：负责安排防汛工程建设、水毁工程修复以及抢险救灾、应急管理等资金，并监督使用。
            </p>
            <p>
              市规划和自然资源局：负责地质灾害预防，组织开展监测和预报预警；防治因洪涝影响导致的山体崩塌、滑坡等突发性地质灾害；承担地质灾害应急救援的技术支撑工作。
            </p>
            <p>
              市生态环境局：负责水环境质量监测，组织开展污染成因调查，牵头编制污染物处置方案。
            </p>
            <p>
              市城乡建设委员会：负责城乡建设（建筑工地等）防汛工作；组建应急抢险队伍，配备抢险设备，按照指令投入防汛抢险；负责沙洲圩地区防汛联防指挥工作。
            </p>
            <p>
              市住房保障和房产局：负责全市房屋（老旧危房）安全度汛，指导物业服务企业做好对地下车库等地下构筑物的防汛排涝工作。
            </p>
            <p>
              市交通运输局：负责公路设施、水运设施及交通在建工程的防汛安全，督促铁路运营企业做好地方铁路设施防汛安全；负责发布内河通航水域航行通（警）告，组织做好船只回港避险；负责组织协调抢险救灾物资调运、人员转移安置等所需的交通工具，协助公安机关做好交通疏导和车辆分流，保障抢险救灾道路畅通。
            </p>
            <p>
              市城市管理局：负责职责内的市政设施防汛安全，协助做好人工助排和安全警示；组建巡查队伍，加强防汛期间巡查，按照指令投入防汛抢险，并协助维持抢险现场秩序。
            </p>
            <p>
              市绿化园林局：负责组织绿化园林防汛工作。
            </p>
            <p>
              市农业农村局：负责农业农村防灾、减灾、救灾及恢复生产；协助河湖非法圈围、拦网养殖所造成行泄洪障碍的清除工作；组织做好渔船回港和水面养殖人员上岸避风；负责农业灾害统计。
            </p>
            <p>
              市文化和旅游局：负责等级景区、旅游项目设施、文保单位、公园内古树名木等防汛工作；负责重大活动、等级景区等安全提示信息发布，组织做好人员避险、疏导和转移。
            </p>
            <p>
              市卫生健康委员会：负责灾区医疗救治、卫生防疫和卫生监督，核查报送医疗卫生信息。
            </p>
            <p>
              市人民防空办公室：负责组织人防工程防汛工作。
            </p>
            <p>
              市机关事务管理局：负责市防指后勤保障工作。
            </p>
            <p>
              市供销合作总社：发挥供销合作社系统连锁超市、农产品批发市场的调节作用，做好应急期间农副产品的供应和市场稳价。
            </p>
            <p>
              中山陵园管理局：负责辖区及军民友谊水库防汛工作。
            </p>
            <p>
              市气象局：负责灾害性天气的监测、预报、预警，对重要天气形势和灾害性天气作出滚动预报，及时向市防指提供预报、预警信息服务。
            </p>
            <p>
              市公安局交通管理局：负责维护道路交通秩序，必要时实行交通管制。
            </p>
            <p>
              市消防救援支队：负责参与应急抢险救援，协助组织群众安全转移安置。
            </p>
            <p>
              南京海事局：负责发布长江航行通（警）告，督促和指导船舶安全避险，及时开展遇险船舶搜寻救助。
            </p>
            <p data-c="99_2_1" class="c">
              市水务局：负责组织全市洪涝灾害防治体系建设，组织实施水务工程防汛消险、积淹水点整治等；承担水情监测预警及信息发布；统筹指导全市水务工程安全运行管理，负责水工程调度；承担市级防汛物资管理；承担防御洪水和城市内涝应急抢险的技术支撑工作。
            </p>
            <p data-c="99_2_1" class="c">
              市应急管理局：负责组织协调应急抢险救援力量；组织协调灾害救助，负责调拨市级救灾物资；承担灾情统计。
            </p>
            <p data-c="99_2_1" class="c">
              市城建集团：负责所属企业及其承担建设和管理项目的防汛工作。
            </p>
            <p data-c="99_2_1" class="c">
              市交通集团：负责所属企业及其承担建设和管理项目的防汛工作。
            </p>
            <p>
              南京地铁集团：负责地铁在建工地、地铁运营线路的防汛工作。
            </p>
            <p>
              南京旅游集团：负责所属企业及上、下黄马水库防汛工作。
            </p>
            <p>
              市安居集团：负责所属企业及其承担建设和管理项目的防汛工作。
            </p>
            <p>
              南京水务集团：负责供排水、污水处理等设施防汛工作。
            </p>
            <p>
              中国电信南京分公司：负责做好防汛应急通信保障。
            </p>
            <p>
              南京供电公司：负责电力设施防汛安全，保障防汛供电。
            </p>
            <p>
              河西新城管委会：负责其承担建设和管理项目的防汛工作。
            </p>
            <p>
              市水文局：负责水雨情实时监测，及时提供水雨情信息和水情预测预报。
            </p>
            <h3>
              2.1.4 市防办职责
            </h3>
            <p>
              承担市防指日常工作，负责组织指导协调各区各部门的洪涝灾害预防和应急处置工作。
            </p>
          </div>
          <div v-if="ChosedItem.indexOf('2-2')!==-1">
            <h2>2.2 联防指挥部</h2>
          </div>
          <div v-if="ChosedItem.indexOf('2-3')!==-1">
            <h2>2.3 区防汛防旱指挥部</h2>
            <p>
              区政府设立区防汛防旱指挥部（以下简称区防指），在本级政府和上级防指的领导下，组织协调本地区防汛工作。
            </p>
          </div>
          <div v-if="ChosedItem.indexOf('2-4')!==-1">
            <h2>2.4 其他防汛组织</h2>
            <p>
              镇街、村（社区）和企事业单位按照基层防汛工作要求，明确职责和人员，在区防指和镇政府（街道办事处）的领导下，组织做好本区域和本单位防汛工作。
            </p>
          </div>
          <div v-if="ChosedItem.indexOf('2-5')!==-1">
            <h2>2.5 工作组</h2>
            <p>
              市防指根据工作需要成立综合协调组、工程技术组、水情气象组、应急队伍组、转移安置组、财务物资组、后勤保障组、灾情统计组、宣传报道组等，承担相应工作职责。
            </p>
            <p>
              综合协调组：由市防办负责，协调处理防汛抢险救灾相关事宜，协助市防指领导协调各工作组的处置工作。
            </p>
            <p>
              工程技术组：由市水务局牵头，市城乡建设委员会、住房保障和房产局、城市管理局、规划和自然资源局、农业农村局、交通运输局、中国电信南京分公司、南京供电公司等
              单位组成，负责做好水务、电力、通信、交通、市政等重要基础设施安全度汛及应急抢险救援。
            </p>
            <p>
              水情气象组：由市水务局牵头，市气象局、水文局等单位组成，负责提供天气预报预测和实时气象水文信息，提出洪涝灾情的发展趋势意见和防范对策。
            </p>
            <p>
              应急队伍组：由市应急管理局牵头，南京警备区、武警南京支队、
              市消防救援支队、城乡建设委员会、城市管理局、交通运输局、水务局等单位组成，负责组织抢险应急队伍，根据需要，协调调遣抢险力量。
            </p>
            <p>
              转移安置组：由市防办牵头，南京警备区、武警南京支队、市消防救援支队、
              公安局、工业和信息化局、应急管理局、交通运输局、水务局等单位组成，负责转移受灾的群众和财物，帮助灾区人民恢复生产和生活。
            </p>
            <p>
              财务物资组：由市水务局牵头，市财政局、发展和改革委员会、应急管理局、工业和信息化局、城乡建设委员会、交通运输局等单位组成，负责防汛抢险物资器材和受灾群众生活急需物资的组织供应，防汛资金保障。
            </p>
            <p>
              后勤保障组：由市发展和改革委员会牵头，市卫生健康委员会、水务局、交通运输局、财政局、公安局、机关事务管理局
              、中国电信南京分公司、南京供电公司等
              单位组成，负责组织医疗救护，抢险救援人员、物资等运输，保障供电和通信畅通。
            </p>
            <p>
              灾情统计组：由应急管理局牵头，市发展和改革委员会、规划和自然资源局、城乡建设管委会、水务局、农业农村局、交通运输局等单位组成，负责洪涝灾害核灾工作，开展灾情经济损失调查评估。
            </p>
            <p>
              宣传报道组：由市委宣传部牵头，市水务局、文化和旅游局、教育局、应急管理局等单位组成，负责把握全市防汛宣传工作导向，开展对外宣传报道工作。
            </p>
          </div>
          <div v-if="ChosedItem.indexOf('2-6')!==-1">
            <h2>2.6 专家库</h2>
            <p>
              市防指组建专家库，由相关专业的技术和管理专家组成，为防汛指挥决策、应急处置等提供技术支撑。
            </p>
          </div>
        </div>
        <div>
          <h1 v-if="ChosedItem.some(item => item.startsWith('3'))">3 监测预报、预警和预防</h1>
          <div v-if="ChosedItem.indexOf('3-1')!==-1">
            <h2>3.1 监测预报</h2>
            <p>
              市气象、水务（水文）、规划和自然资源部门应加强暴雨、洪水、地质灾害的监测预报，将结果报市防指，并按权限及时向社会发布有关信息。遭遇重大灾害性天气时，应加强联合监测、会商和预报，尽可能延长预见期，并对未来可能发展趋势及影响作出评估，将评估结果报市防指。
            </p>
          </div>
          <div v-if="ChosedItem.indexOf('3-2')!==-1">
            <h2>3.2 预警</h2>
            <p>
              （1）市气象局负责暴雨、台风等气象灾害预警信息发布。
            </p>
            <p>
              （2）市水务（水文）局负责水情监测预报，及时发布水情预警。
            </p>
            <p>
              （3）市规划和自然资源局负责暴雨洪水诱发的地质灾害预警信息发布。
            </p>
            <p>
              （4）其他有关部门做好相关预警工作。
            </p>
            <p>
              各部门将行业预警信息及时报市防指。
            </p>
          </div>
          <div v-if="ChosedItem.indexOf('3-3')!==-1">
            <h2>3.3 预防</h2>
            <p>
              各级各部门应按照职责要求做好各项预防工作，组织各单位与公民积极开展自我防范。
            </p>
            <h3>3.3.1 预防准备</h3>
            <p>
              （1）思想准备：加强宣传，增强全民预防洪涝灾害和自我保护的意识，全面做好防大汛准备工作。
            </p>
            <p>
              （2）组织准备：建立防汛组织指挥机构，构建洪涝灾害易发重点区域防御机制和监测网络，落实责任人、抢险队伍和预警措施，加强专业抢险队伍建设。
            </p>
            <p>
              （3）工程准备：存在病险的各类工程及基础设施实行应急除险加固，落实防汛安全措施；跨汛期施工的涉水工程落实度汛方案。
            </p>
            <p>
              （4）预案准备：市防指成员单位按照职责分工编制（修订）本系统（部门）防汛应急预案。
            </p>
            <p>
              （5）物资准备：按照分级负责的原则，储备必需的防汛物资，并合理配置，防汛重点部位现场储备一定数量的抢险物资。
            </p>
            <p>
              （6）通信准备：充分利用社会通信公网，保障防汛工作通信畅通；健全水文、气象测报网，确保雨情、水情、工情、灾情信息和指挥调度指令及时传递。
            </p>
            <h3>3.3.2 检查巡查</h3>
            <p>
              （1）各级防指应组织开展防汛工作检查，发现安全问题的，责成限期处理和整改。
            </p>
            <p>
              （2）水务、建委、城管、交通、住房保障、规划和自然资源、教育、文旅、通信、供电等部门加强对水务、建设、市政、交通、房屋、地质灾害隐患点、学校、景区、通信、电力等的检查和巡查，发现问题及时处理。
            </p>

          </div>
        </div>
        <div>
          <h1 v-if="ChosedItem.some(item => item.startsWith('4'))">4 应急响应</h1>
          <div v-if="ChosedItem.indexOf('4-1')!==-1">
            <h2>4.1 总体要求</h2>
            <h3>
              4.1.1 按洪涝、暴雨及水务工程险情的严重程度、影响范围，将应急响应分为Ⅳ（一般）、Ⅲ（较大）、Ⅱ（重大）、Ⅰ（特别重大）四级，分别用蓝、黄、橙、红四种颜色标示。
            </h3>
            <h3>
              4.1.2 进入汛期，各级防指应实行24小时值班制度，密切关注雨情、水情、工情、灾情，并根据不同情况启动相关应急响应。
            </h3>
            <h3>
              4.1.3 不同级别应急响应启动程序：
            </h3>
            <p>
              （1）Ⅳ级应急响应由市防办决定启动；
            </p>
            <p>
              （2）Ⅲ级应急响应由市防指或授权市防办决定启动；
            </p>
            <p>
              （3）Ⅱ级应急响应由市防指决定启动；
            </p>
            <p>
              （4）Ⅰ级应急响应由市防指报请市政府同意后启动。
            </p>
            <p>
              洪涝灾害主要影响地区防指应急响应级别不得低于市级应急响应级别。
            </p>
          </div>
          <div v-if="ChosedItem.indexOf('4-2')!==-1">
            <h2>4.2 应急响应启动条件</h2>
            <h4>
              4.2.1 Ⅳ级响应（蓝色）
            </h4>
            <p>
              出现下列情况之一者，经综合研判后，启动全市或局部Ⅳ级响应：
            </p>
            <p>
              （1）主要江河湖水位达到警戒水位：长江南京高潮位8.70≤Z＜9.20米，秦淮河东山水位8.80≤Z＜9.50米，滁河晓桥水位9.50≤Z＜11.00米，滁河六合水位8.20≤Z＜9.00米，水阳江水碧桥站水位10.50≤Z＜11.50米，固城湖高淳水位10.40≤Z＜11.50米，石臼湖蛇山水位10.40≤Z＜11.50米。
            </p>
            <p>
              （2）市气象局发布暴雨蓝色预警信号，即预计未来12小时降雨量达50毫米以上；或12小时已达50毫米以上且降雨可能持续。
            </p>
            <p>
              （3）受涝面积在50万亩以内。
            </p>
            <p>
              （4）流域性防洪工程、区域性骨干防洪工程、水库大坝出现一般险情。
            </p>
            <h4> 4.2.2 Ⅲ级响应（黄色）</h4>
            <p>
              出现下列情况之一者，经综合研判后，启动全市或局部Ⅲ级响应：
            </p>
            <p>
              （1）主要江河湖水位超过警戒水位：长江南京高潮位9.20≤Z＜9.70米，秦淮河东山水位9.50≤Z＜10.50米，滁河晓桥水位11.00≤Z＜12.00米，滁河六合水位9.00≤Z＜9.50米，水阳江水碧桥站水位11.50≤Z＜12.00米，固城湖高淳水位11.50≤Z＜12.00米，石臼湖蛇山水位11.50≤Z＜12.00米。
            </p>
            <p>
              （2）市气象局发布暴雨黄色预警信号，即预计未来6小时降雨量达50毫米以上；或6小时已达50毫米以上且降雨可能持续。
            </p>
            <p>
              （3）受涝面积在100万亩以下、50万亩以上。
            </p>
            <p>
              （4）流域性防洪工程、区域性骨干防洪工程、水库大坝出现较大险情或塘坝出现溃坝。
            </p>
            <h4>
              4.2.3 Ⅱ级响应（橙色）
            </h4>
            <p>
              出现下列情况之一者，经综合研判后，启动全市或局部Ⅱ级应急响应：
            </p>
            <p>
              （1）主要江河湖水位接近保证水位：长江南京高潮位9.70≤Z＜10.22米，秦淮河东山水位10.50≤Z＜11.44米，滁河晓桥水位12.00≤Z＜12.50米，滁河六合水位9.50≤Z＜9.97米，水阳江水碧桥站水位12.00≤Z＜12.80米，固城湖高淳水位12.00≤Z＜12.50米，石臼湖蛇山水位12.00≤Z＜12.50米。
            </p>
            <p>
              （2）市气象局发布暴雨橙色预警信号，即预计未来3小时降雨量达50毫米以上；或3小时已达50毫米以上且降雨可能持续。
            </p>
            <p>
              （3）受涝面积在200万亩以下、100万亩以上。
            </p>
            <p>
              （4）流域性防洪工程、区域性骨干防洪工程、水库大坝出现重大险情或小型水库出现溃坝。
            </p>
          </div>
          <div v-if="ChosedItem.indexOf('4-3')!==-1">
            <h2>4.3 应急响应行动</h2>
            <h4>
              4.3.1 Ⅳ级响应行动
            </h4>
            <p>
              （1）市防办负责人主持会商，水务、气象、水文等主要成员单位参加，研究分析汛情可能影响情况，做出相应工作安排，并视情连线有关区防指进行动员部署。及时将情况报市政府并通报市防指成员单位。
            </p>
            <p>
              （2）市防办负责人带班，关注水情、雨情、工情等汛情变化，加强预测预报，做好抢险物资队伍准备。每日发布防汛工作通报。
            </p>
            <p>
              （3）市气象局每日至少一次报告天气预报结果，有较明显变化时，随时更新预报；市水务（水文）局每日至少一次报告洪水预报结果；市防指其他成员单位按照职能分工做好相关工作，及时向市防指报告。
            </p>
            <p>
              （4）事发地区防指启动相应应急响应，组织做好防汛工作；按要求向市防办报告工作动态，突发险情、灾情及时上报。
            </p>
            <h4>
              4.3.2 Ⅲ级响应行动
            </h4>
            <p>
              （1）市防指副指挥或委托市防办负责人主持会商，水务、气象、水文、城管、交通、建委、住房保障、规划和自然资源、农业农村、应急管理等主要成员单位参加，部署防汛工作，明确工作目标和重点，连线有关区防指进行动员部署。及时将情况报市政府并通报市防指成员单位，重要情况报省防指。
            </p>
            <p>
              （2）市防指副指挥或防办负责人带班，密切监视汛情、灾情变化，加强预测预报，落实抢险物资队伍调配，向事发地派出工作组或专家组指导工作。每日发布防汛工作通报。
            </p>
            <p>
              （3）市气象局每日至少两次报告天气预报结果，有较明显变化时，随时更新预报；市水务（水文）局每日至少两次报告洪水预报结果；市防指其他成员单位按照职能分工做好相关工作，及时向市防指报告。
            </p>
            <p>
              （4）水务、气象、水文、城管、公安交通管理、南京供电公司等单位派员进驻市防办。
            </p>
            <p>
              （5）事发地区防指启动相应应急响应，组织做好防汛抗洪抢险工作；按要求向市防办报告事情进展及工作动态，突发灾情、险情及时报告。
            </p>
            <h4>
              4.3.3 Ⅱ级响应行动
            </h4>
            <p>
              （1）市防指指挥主持会商，成员单位参加，部署防汛工作，明确工作目标、重点和措施，发布人员转移命令。连线有关区防指进行动员部署。及时将情况报市政府和省防指。
            </p>
            <p>
              （2）市防指副指挥带班，加强值班力量，密切监视汛情、灾情变化，强化预测预报，加大抢险物资队伍投入，向事发地派出工作组或专家组指导工作。每日发布防汛工作通报。
            </p>
            <p>
              （3）市气象局每日至少三次报告天气预报结果，有较明显变化时，随时更新预报；市水务（水文）局每3小时报告洪水预报结果；市防指其他成员单位按照职能分工做好相关工作，及时向市防指报告。
            </p>
            <p>
              （4）水务、气象、水文、城管、公安交通管理、应急管理、交通运输、建委、南京警备区、武警南京支队、消防救援支队、南京供电公司等单位派员进驻市防办。
            </p>
            <p>
              （5）解放军、武警部队、消防等各类抢险队伍做好人员待命准备，视情投入防汛抢险救灾工作。
            </p>
            <p>
              （6）事发地区防指启动相应应急响应，全力组织做好防汛抢险救灾工作，转移危险地区群众；按要求向市防办报告事情进展及工作动态，突发灾情、险情随时报告；视地区汛情、灾情、险情发展，根据《中华人民共和国防洪法》可依法宣布受影响地区进入紧急防汛期。
            </p>
            <h4>
              4.3.4 Ⅰ级响应行动
            </h4>
            <p>
              （1）市防指指挥主持会商，成员单位参加，部署防汛抢险救灾应急工作；组织召开全市防汛视频紧急会议，进行紧急动员部署。市政府发出紧急通知，要求全市全力做好防汛工作。根据汛情，可依法宣布全市或部分区域进入紧急防汛期。立即将情况报市政府和省防指，必要时请求上级和有关方面支援。
            </p>
            <p>
              （2）市防指指挥坐镇指挥，进一步加强值班力量，密切监视汛情、灾情变化，强化预测预报，加大抢险物资队伍投入，向事发地加大工作组或专家组派出数量。每日发布防汛工作通报。
            </p>
            <p>
              （3）市气象局每日至少六次报告天气预报结果，有较明显变化时，随时更新预报；市水务（水文）局随时报告洪水预报结果；市防指其他成员单位按照职能分工做好相关工作，及时向市防指报告。
            </p>
            <p>
              （4）水务、气象、水文、城管、公安交通管理、应急管理、交通运输、建委、消防、宣传、农业农村、南京警备区、武警南京支队、南京供电公司等单位派员进驻市防办。
            </p>
            <p>
              （5）解放军、武警部队、消防等各类抢险队伍全力投入防汛抢险救灾工作。
            </p>
            <p>
              （6）事发地区防指启动相应应急响应，把防汛工作作为首要任务，动员和组织广大干部群众投入防汛抢险救灾工作；按要求向市防办报告事情进展及工作动态，突发灾情、险情随时报告；应依法宣布本地进入紧急防汛期。
            </p>
          </div>
          <div v-if="ChosedItem.indexOf('4-4')!==-1">
            <h2> 4.4 应急响应措施</h2>
            <h4> 4.4.1 江河湖库洪水</h4>
            <p>
              （1）根据江河湖库水情和洪水预报，按照规定的权限和防御洪水方案、洪水调度方案，适时调度水务工程。
            </p>
            <p>
              （2）当江河湖水位超过警戒水位后，当地及有关单位应组织人员巡堤查险。
            </p>
            <p>
              （3）当洪水位继续上涨，危及重点保护对象，或者预报江河洪水接近或超过保证水位、水库接近设计水位时，在充分发挥现状工程体系防洪、泄洪能力的同时，做好抢险物资队伍准备，提前组织危险区域人员转移避险。
            </p>
            <p>
              （4）在实施蒿子圩蓄滞洪区调度运用时，根据洪水预报和批准的滁河流域洪水调度方案，由防指决定做好蓄滞洪区启用的准备工作，当达到启用条件时，按程序实施分洪。
            </p>
            <p>
              （5）在宣布进入紧急防汛期后，防指依法行使相关权利、采取特殊措施，保障防汛抢险救灾的顺利实施。
            </p>
            <h4> 4.4.2 雨涝灾害</h4>
            <p>
              （1）当出现雨涝灾害时，水务部门应科学调度水务工程，开展自排和抽排，尽快排出涝水，恢复正常生产生活秩序。
            </p>
            <p>
              （2）降雨过程中，加大重要路段、重点区域、地下空间等易积淹水点的巡查力度，一旦发现积淹水情况，立即采取措施应对，保证积淹水及时排除。
            </p>
            <p>
              （3）在江河湖库防汛形势紧张时，要正确处理排涝与防洪的关系，避免因排涝而增加防汛压力。
            </p>
            <h4> 4.4.3 山洪灾害</h4>
            <p>
              （1）当发生山洪灾害时，当地防指应组织水务、规划和自然资源、应急管理、建委、生态环境、气象等有关部门的专业人员，及时赶赴现场，加强监测，采取应急措施，以减少损失。
            </p>
            <p>
              （2）视山洪灾害易发区降雨量、山体变形滑动趋势等，及时组织危险区域人员转移安置。
            </p>
            <h4>
              4.4.4 堤防决口、水库溃坝、涵闸泵垮塌
            </h4>
            <p>
              （1）当出现堤防决口、水库溃坝、涵闸泵垮塌前期征兆时，当地防指要迅速调集人力、物力全力组织抢险，尽可能控制险情，及时向下游发出警报，并立即报告上级防指。
            </p>
            <p>
              （2）堤防决口、水库溃坝、涵闸泵垮塌的应急处理，由当地防指负责，迅速组织受影响群众转移安置，视情况抢筑二道防线，控制洪水影响范围，尽可能减少灾害损失。
            </p>
            <p>
              （3）当地防指视情况组织实施堵口抢护，必要时可请求上级和有关方面支援。水务部门调度有关水务工程，为实施堵口抢护创造条件。
            </p>

          </div>
          <div v-if="ChosedItem.indexOf('4-5')!==-1">
            <h2>4.5 信息报告和发布</h2>
            <h4> 4.5.1 信息报告</h4>
            <p>
              （1）汛情、险情、灾情等防汛信息实行分级上报、归口处理、同级共享。重大险情、灾情可越级上报。
            </p>
            <p>
              （2）防汛信息的报送应快速、准确、翔实，重要信息立即上报，因客观原因一时难以准确掌握的信息，应及时报告基本情况，同时抓紧了解情况，随后补报详情。
            </p>
            <p>
              （3）险情、灾情发生后，各区防指、职能部门和防汛责任单位在组织抢险救援同时，及时汇总相关信息并报告。一旦发生重大险情、灾情，必须在接报后半小时内口头、1小时内书面报告市防办；特大或特殊情况，必须立即报告。
            </p>
            <p>
              （4）重大险情、灾情市防办接报后，第一时间组织做好处置工作，半小时内口头、1小时内书面报告市政府和省防指；特大或特殊情况，必须立即报告。
            </p>
            <p>
              （5）跨区域发生的或影响到邻近行政区域的洪涝灾害，防指在报告同级政府和上级防指的同时，应及时向受影响地区的防指通报情况。
            </p>
            <h2>
              4.5.2 信息发布
            </h2>
            <p>
              （1）防汛信息发布应及时、准确、客观、全面。
            </p>
            <p>
              （2）信息发布形式主要包括授权发布、播发新闻稿、组织报道、接受记者采访、举行新闻发布会等。
            </p>
            <p>
              （3）汛情及防汛抢险救灾等信息，由市防指审核和发布；涉及洪涝灾情的，由市应急管理局会同市防办审核和发布；对有重大影响的洪涝灾害，由市防指审核后，会同市委宣传部进行报道。
            </p>
            <p>
              根据灾害发展趋势和对我市影响情况的变化，适时变更应急响应等级。当洪涝灾害得到有效控制时，由启动应急响应的机构宣布应急响应结束。
            </p>
          </div>
        </div>
        <div>
          <h1 v-if="ChosedItem.some(item => item.startsWith('5'))">5 保障措施</h1>
          <div v-if="ChosedItem.indexOf('5-1')!==-1">
            <h2> 5.1 组织保障</h2>
          </div>
          <div v-if="ChosedItem.indexOf('5-2')!==-1">
            <h2> 5.2 资金保障</h2>
            <p>
              财政部门负责安排防汛工作资金，主要用于抢险救灾、洪涝灾害水毁工程修复和建设等，并对资金使用情况进行监管。
            </p>
          </div>
          <div v-if="ChosedItem.indexOf('5-3')!==-1">
            <h2> 5.3 物资保障</h2>
            <p>
              市水务局、发展和改革委员会、应急管理局按职责分工负责储备防汛抢险救灾物资，各相关行业单位应按规范储备防汛专用物资，以备抢险急需。市防指可委托社会企业代储部分防汛物资，紧急防汛期根据需要，可依法征用社会物资。
            </p>
          </div>
          <div v-if="ChosedItem.indexOf('5-4')!==-1">
            <h2>5.4 队伍保障</h2>
            <p>
              防汛抢险队伍由专业抢险队伍、消防综合性救援队伍、解放军和武警部队及社会抢险力量等组成。水务、建委、城管、交通、规划和自然资源、供电、海事等部门组建专业抢险队伍；应急管理部门协调动员社会抢险力量；解放军和武警部队的调动，应按有关规定办理；其他队伍应服从防指的统一调度。
            </p>
          </div>
          <div v-if="ChosedItem.indexOf('5-5')!==-1">
            <h2>5.5 通信保障</h2>
            <p>
              通信运营企业应优先为防汛指挥调度做好通信保障，建立稳定可靠的应急通信系统；安排应急维修抢险队伍，及时维修损坏的通信设施，必要时调度应急通信设备，为防汛通信和现场指挥提供临时通信保障。防指应按照以公用通信网为主的原则，组建防汛专用通信网络，确保通信与信息传递畅通。
            </p>
          </div>
          <div v-if="ChosedItem.indexOf('5-6')!==-1">
            <h2>
              5.6 交通保障
            </h2>
            <p>
              运输防汛抢险救灾人员、物资车辆给予优先通行。公安交通管理、交通运输、海事部门制定相应措施，保证抢险救灾物资运输、人员转移安置、河道航运等安全，必要时实行交通管制，保障防汛抢险救灾工作顺利进行。防指可通过征用社会车辆组建防汛抢险救灾应急车队。
            </p>
          </div>
          <div v-if="ChosedItem.indexOf('5-7')!==-1">
            <h2>5.7 电力保障</h2>
            <p>
              供电部门做好防汛抢险救灾供电调度；安排应急维修抢险队伍，对输电故障及时维修；落实应急发电机组，确保防汛抢险救灾现场临时供电。
            </p>
          </div>
          <div v-if="ChosedItem.indexOf('5-8')!==-1">
            <h2> 5.8 治安保障</h2>
            <p>
              公安部门做好防汛期间社会治安工作，依法严厉打击阻扰防汛抢险救灾和破坏工程设施的行为。必要时依法采取有效管制措施，维护灾区的社会治安秩序。
            </p>
          </div>
          <div v-if="ChosedItem.indexOf('5-9')!==-1">
            <h2> 5.9 卫生保障</h2>
            <p>
              卫健部门加强洪涝灾区的疾病和突发公共卫生事件监测、报告工作，落实各项防病措施；组织医疗卫生队赴灾区巡医问诊、救治伤员、防疫消毒等，防止疾病流行
            </p>
          </div>
          <div v-if="ChosedItem.indexOf('5-10')!==-1">
            <h2> 5.10 宣传培训与演练</h2>
            <h4>5.10.1 宣传</h4>
            <p>
              防指应通过广播、电视、报纸、网络、手机通信等媒体，开展防汛有关政策、法规以及预防、避险、自救、互救等知识宣传教育。
            </p>
            <h4>
              5.10.2 培训
            </h4>
            <p>
              按照分级负责的原则，防指积极开展领导干部、防汛管理人员、抢险队伍等防汛知识和技术培训。培训工作可采取多种形式，定期与不定期相结合，每年汛前至少组织一次。
            </p>
            <h4>
              5.10.3 演练
            </h4>
            <p>
              防指应结合实际，有计划、有重点地开展不同类型的防汛抢险演练。专业抢险队伍应针对自身的业主特长和当地易发生的各类险情，有针对性地进行抢险演练。防指组织的多部门联合演练，一般2—3年举行一次。
            </p>
          </div>
        </div>
        <div>
          <h1 v-if="ChosedItem.some(item => item.startsWith('6'))">6 善后工作</h1>
          <div v-if="ChosedItem.indexOf('6-1')!==-1">
            <h2>6.1 灾后救助</h2>
            <p>
              防指应结合实际，有计划、有重点地开展不同类型的防汛抢险演练。专业抢险队伍应针对自身的业主特长和当地易发生的各类险情，有针对性地进行抢险演练。防指组织的多部门联合演练，一般2—3年举行一次。
            </p>
          </div>
          <div v-if="ChosedItem.indexOf('6-2')!==-1">
            <h2>
              6.2 灾后重建
            </h2>
            <p>
              各级政府应尽快组织开展灾后恢复重建工作，原则上按原标准恢复，条件允许可提高标准重建。
            </p>
          </div>
          <div v-if="ChosedItem.indexOf('6-3')!==-1">
            <h2>6.3 水毁修复</h2>
            <p>
              遭到毁坏的水务、交通、电力、通信、市政等设施尽快组织修复，防洪工程力争在次年汛期到来之前，做到恢复主体功能。针对当年防汛抢险物料消耗情况，按照分级储备的要求，及时补充到位。
            </p>
          </div>
          <div v-if="ChosedItem.indexOf('6-4')!==-1">
            <h2>6.4 补偿要求</h2>
            <p>
              紧急调集、征用的物资、设备、交通运输工具等，应当及时归还或按有关规定给予补偿；调用专业抢险队伍，由申请调用的单位给予适当补助；蓄滞洪区分洪运用后，按照有关规定进行补偿。保险机构应及时对洪涝灾害造成的损失进行评估、审核和赔付。
            </p>
          </div>
          <div v-if="ChosedItem.indexOf('6-5')!==-1">
            <h2>6.5 总结评估</h2>
            <p>
              紧急调集、征用的物资、设备、交通运输工具等，应当及时归还或按有关规定给予补偿；调用专业抢险队伍，由申请调用的单位给予适当补助；蓄滞洪区分洪运用后，按照有关规定进行补偿。保险机构应及时对洪涝灾害造成的损失进行评估、审核和赔付。
            </p>
          </div>
          <div v-if="ChosedItem.indexOf('6-6')!==-1">
            <h2>6.6 奖惩</h2>
            <p>
              防汛抢险救灾工作中作出贡献的集体和个人，视情由市防指给予褒奖。对防汛抢险救灾工作中英勇献身的人员，按有关规定追认为烈士；致伤致残的人员，按有关规定给予工作生活照顾。对防汛抢险救灾工作中玩忽职守造成损失的，依法追究当事人的责任并予以处罚；构成犯罪的，依法追究其刑事责任。
            </p>
          </div>
        </div>
      </div>
    </div>
    <el-button type="primary" style="margin-left: 45%;margin-top: 100px" @click="exportToWord">导出预案</el-button>
  </div>
</template>

<script>
import FileSaver from 'file-saver'
import htmlDocx from 'html-docx-js/dist/html-docx'

export default {
  name: 'detailed_plan_nanjing',
  props:
    {
      ChosedItem: {
        type: Array
      },
      items: {
        type: Array
      }
    },
  data() {
    return {
      // should_show
    }
  },
  methods:
    {
      exportToWord() {
        // 获取要导出的 <div> 元素
        const divElement = document.getElementById('exportContent')

        // 获取 <div> 元素的 HTML 内容
        const htmlContent = divElement.innerHTML
        // 转换 HTML 为 Word 文档
        const convertedContent = htmlDocx.asBlob(htmlContent)

        // 创建一个下载链接并触发下载
        const downloadLink = document.createElement('a')
        downloadLink.href = URL.createObjectURL(convertedContent)
        downloadLink.download = 'flood_plan_nanjing.docx'
        downloadLink.click()
      }
    }
}

</script>

<style>

</style>
