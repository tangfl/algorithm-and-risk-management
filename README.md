# 一、互联网业务风控
## 1.1 不同场景下的业务风控
### 1.1.1 账户业务风控

#### 账户安全
- 账号安全是所有强账号体系应用的基础，强账号体系，如电商、网游、第三方支付、社交网络、即时通讯等；是需要登录后产生数据和交互的应用，而搜索、导航、杀毒客户端不需要登录也能用、属于弱账号体系应用。
- 对用户来说，账号安全涉及用户个人隐私的信息的安全、用户体验以及资金安全等方面；对于互联网平台，账户安全涉及广告投放、流量购买、营销活动以及战略决策等方面。
#### 账户安全环节
- **注册**
  - 对于羊毛党来说、平时需要养号，等到促销之类的活动集中使用。垃圾注册、虚拟小号是垃圾抢购和欺诈钓鱼的源头、账号安全需要从源头收紧。
  - 对抗垃圾注册的手段：图片验证码、邮件验证码、短信验证码、语音验证码。对于使用分布式代理的注册机，如果有账号风控系统的可以根据来源属性和机器人行为特征标注恶意灰度，从而给与不同级别的验证码。
- **登录**
  - 登录环节的问题包括撞库、暴力破解、盗号登录、非常用设备登录、黑产小号和僵尸号登录等。
  - 使用风控系统对比用户画像检测登录地域来源、ip属性、可信设备、登录频率、代理使用习惯等从而推送不同复杂等级的验证码或者开启多要素验证。或者根据离线数据、即一段时间内的请求异常，再调用风控的接口推送二次认证要求做人机识别。
- **密码找回/密保**
  - 在认证设备之间提供异地登录提醒、异常登录提醒、破解账号提醒。对找回密码业务进行人机识别、防止批量找回、密码重置等接口的机器行为。
- **多设备登录**
  -  多平台设备时，应保证同平台不能“串号”----同一账号可以在PC端和APP端同时登录，但不能在两个PC端登录一个账号，或不同的手持设备上登录一个账号。多设备同时登录时应支持交叉验证，例如PC端的可疑登录自动发起2FA请求到手机端，经由手机端确认后才能从PC端登录。
- **账号共享体系**
  -  大型平台一般有很多应用，凭借一个的登录token直接无障碍登录所有子应用在安全上不是一个很好的设计，一旦被XSS盗取token就相当于全线溃防。
  - 所以在功能和应用入口较多的平台，应对业务划分权重、分类分级、涉及个人认证信息、个人隐私、支付类的一般属于高级web安全域，对于高安全域的应用，登录除了SSO token之外，引入第二层的secure token，只有两个token齐备才能继续登录。
#### 账户安全相关算法
维护账户安全的手段主要有网络层防护、数据层防护以及业务层防护。
相应的手段不局限于WAF、设备指纹、验证码、生物探针、数字证书、安全SDK。
这些防护手段从技术原理上可以总结为加密/解密、人机识别两大类。
- 黑产手段：群控设备、养卡平台、养号平台、打码平台
- 算法选择：K-means、高斯混合（GMM）；OPTICS、DBSCAN
- 应用方法：
1. 如果两个注册用户时的ip地址段相同、则两个用户的之间的边权重增加1，如果邮箱后缀也相同，则权重就为2，以此类推，我们就可以把某段时间内子互联网平台上注册的账号关联成一张权重无向图。
2. 再使用**图嵌入（embedding）表征**的方法得到每个账号节点高维向量，然后将这些高维向量联合基础特征进行聚类。可以识别有聚集性的注册垃圾账号的团体。

### 1.1.2 电商平台风控
#### 电商平台安全
电商平台最核心的业务就是交易，而交易又衍生出其他业务。商家为了达到提升排名、引流、商品冷启动、增加商品评论数等目的，会通过多种渠道来做虚假交易。典型的虚假交易是指通过虚构交易流程、伪造物流、资金流信息等手段，提到**DSR**(Detail Seller Rateing，即商铺信用、商品销量和店铺动态评分)分数，实现提升店铺和商品排名的目的。
- **恶意下单**
拍下商品但不付款，旨在侵占库存，一般的对策是在高峰时段下单使用验证码，下单后一段时间不付款订单自动失效，限制下单频率，有风控数据源可以对恶意账单进行标记、冻结下单。
- **黄牛抢单**
黄牛一般事前批量注册小号、抢购前准备好抢单机器人程序。对于恶意养号，风控系统一般会根据小号、僵尸号的行为与正常账户的区别标注、登录的途径、登录地域、登录设备指纹、收货地址来分类标记
- **刷优惠券和奖励**
首先要在账号层面根据大数据标记账号恶意灰度，其次优惠券跟账号绑定，无法流通和交易。跟网游中的经济体系数值类似，建立阶梯模型，给优质账户高额回馈，给低信誉账号小额优惠。
- **反价格爬虫**
价格爬虫主要是竞争对手比价。爬虫所在的IP段，访问的申请头，以及是否会解析JavaScript，浏览器行为等特征可以作为人机识别模型的有用特征。

- **交易风控**
1. 账户安全 
2. 客户端安全（反钓鱼、反木马）
3. 认证机制（证书、令牌、多因素认证）
4. 风险评估（账号历史行为、账户历史征信、交易和用户异常、模型和策略）


#### 刷单流程和危害及其措施
刷单主体：商家、刷手、刷单中介和上下游利益团体（泛指卡商、账号批发商、物流）。
- 刷单流程：
1. 商家发布刷单需求。
2. 刷手接任务、浏览、下单、付款、发货、收货、评论。
3. 商家和刷手按协议返回佣金
- 刷单危害：
1. 虚假交易产生的商誉、信用、评论会严重误导消费者、影响购物决策。
2. 刷单会验证干扰算法所需数据的准确性，并且刷单短期的ROI高于广告、因此广告主会减少广告投入而转向刷单、导致平台收入减少。
- 刷单识别角度：
1. 交易的行为主体：主体包括商家和用户，可以从商家和用户的账号中提取业务特征。
2. 交易的行为流程：我们可以在每个交易的环节提取虚假交易的特定模式，如注册和登陆的渠道占比、商品浏览时长与分布、购物车操作的模式等。
3. 交易的第三方依赖：主要有支付和物流，支付账号的资金流转情况和物流的走件与时效性都可以从中去判断交易的真实性


#### 电商平台风控手段
- 虚假交易识别技术体系的发展
1. 初期阶段：专家规则、人工审核
2. 中期阶段：机器学习、近实时计算
3. 发展阶段：深度学习、实时计算
4. 未来阶段：无监督学习、图网络学习、实时图计算。
- 数据源
1. 交易信息：账户、金额、日期、时间、币种、渠道、商户、产品信息等；
2. 欺诈记录：是或者否；
3. 其他信息：用户IP、账户、设备等。
- 算法选择：Louvain算法（风险时间的后置挖掘）、FRAUDAR算法（全量事件的前置挖掘）
- Louvain算法介绍
1. 模块度：描述社区内的紧密程度。
2. 模块度增量：把孤立的节点放入社区后，模块度前后的变化。3.节点/社区权重:社区内指向节点的边权重之和
3. 算法过程：先把每个点做社区，然后考虑每个社区的邻居节点，将其合并到邻居节点的社区，分别计算模块度增量，将点合并到最大增量的社区，将新社区作为新的节点，依次迭代，直至社区划分不在变动。
4. 算法复杂度：速度非常快，时间复杂度O(N)

### 1.1.3 社交平台风控
#### 社交平台风控手段
- 黑产手段：垃圾邮件、虚假关注/转发、水军评论、买卖粉丝、群控点赞。
- 算法选择：CatchSync算法
- CatchSync算法介绍
  - 基于欺诈者行为模式具有同步性和稀有性的特点构建的图异常检测算法。

### 1.1.4 内容业务风控
#### 内容业务风控手段
- 垃圾内容形式：文本、语音、图片以及复合嵌套形式。
- 模型类型：行为识别模型、文本识别模型、图像分类模型
- 模型指标：可以使用用户的回复率作为垃圾内容的Y
- 算法选择：CNN、LSTM、GCN
- 应用方法：
1. 垃圾文本识别
    利用CBOW、SKIPGRAM和GLOVE等词向量作为输入利用Keras建立卷积神经网络，模型训练完毕后将其序列化到磁盘，可以利用python的Tornado框架搭建HTTP服务包装模型的预测输出。
2. 垃圾图片识别
    构建残差神经网络

### 1.1.5 信息反爬业务风控
互联网平台对流量监控在大多数情况下，宁可放宽监管力度，也不允许有太多的错杀的情况。
- 算法选择：**wide&deep + LSTM + Attention**
- 应用方法：
使用IP地址访问的时间序列、类目序列、URL序列以及用户访问的时间间隔，来判断用户访问网站是否正常用户。
1. 注意力机制：为了防止序列时间过长使模型的注意力被分散，并且让模型更多的关注URL等序列的跳转信息，增加了注意力机制
2. 增加序列数据的长度：意识尝试不同的序列长度，二是尝试不同的数据截取方式。
3. 增加更多的序列数据：通过对URL中多级类目进行分类处理，增加了类目之间的转移序列和refer的转移序列等信息。 
### 1.1.6 信贷业务风控

#### 1) 信贷欺诈风险类型
从作案规模上，信贷欺诈一般可分为个人欺诈和团队欺诈。从风险归因上，可分为以下类别：
1.  **白户风险**：借款人信息缺失，没有足够的数据来对借款人进行风险评估。包括内部白户（新注册用户、无申贷历史记录）和外部白户（央行征信、第三方民间征信无覆盖）。由于从未或较少有申贷记录，因此黑名单规则、多头规则等都会失效。在对待白户时，应当谨慎其被黑产利用的可能。
2.  **黑户风险**：借款人存在逾期、失信、欺诈的记录。包括内部黑户（历史多笔订单出现逾期、在途订单催收失联等）、外部黑户（央行征信花/黑、第三方民间征信黑）。出于炫耀等心理，很多黑户会在口子论坛、QQ群等交流撸贷经验，因此可通过论坛舆情监控、黑产群卧底等来发现新的作案手法。
3.  **恶意欺诈**：借款人通过伪造资料，蓄意骗贷。例如，伪造账单流水记录来企图骗取更高的额度。一般都是借款老哥，熟悉各平台流程，深谙套路。恶意欺诈人群往往到处借钱，广撒网来提高放款概率。同时，可能涉及不良嗜好（黄赌毒）。
4.  **身份冒用**：伪冒他人身份进行欺诈骗贷。包括熟人冒用（亲戚朋友、同学等）和他人盗用（购买他人四件套、个人隐私信息泄漏等）。一般可通过信审、人脸识别、活体验证等方式来核验借款人身份。
5.  **以贷养贷**：通过拆东墙补西墙的方式，来维持不良消费（黄赌毒、奢侈品等）。借款人现金流收入主要靠不断借款，借下家的钱，还上家的债。这种击鼓传花的游戏，会将共债风险杠杆逐渐放大。一旦借款人再也借不到钱，资金链便会立刻断裂，所有平台全线逾期。目前市场上，同盾、百融、亿美等第三方征信机构都提供此类多头借贷产品服务。
6.  **中介风险**：黑中介哄骗或招揽客户实施骗贷。网贷中介有利有弊，好中介可帮助平台导流（类似贷款超市），提高市场份额；黑中介将对平台风控漏洞进行大规模攻击，造成巨大资损。黑中介可利用白户轻松突破风控防线，并骗取白户的高额手续费。黑中介通讯录一般会存客户的号码；若是远程贷款操作，可能会采取视频通话、翻拍照片来应对活体识别核身。

#### 2) 信贷反欺诈方法
1.  **实时大盘监控**：基于订单、用户维度，监控设备聚集性风险（LBS、Wi-Fi）、地域欺诈风险（如朋克村）。大盘监控对于识别黑中介风险、传销风险等团伙欺诈相对更有效，需要设置报警阈值，并人工介入分析。
2.  **信审催收反馈**：信审通过电话外呼，核验客户身份，咨询借款动机，往往会发现某些欺诈用户。例如，身份伪冒风险场景中，借款人支支吾吾无法正确回答问题。催收中发现失联用户等，这些兄弟部门的案件将反馈至调研组。因此，需要建设好案件管理平台，联通各部门。
3.  **论坛舆情监控**：通过对各大口子论坛、戒赌吧、上岸交流区等内容，提取近期市场动向。特别是需要去理解欺诈人群的心理特征、社会身份等。例如，2018年，在714高炮行业风险初见苗头时，论坛上就出现“青铜系”、“宁波系”等借贷口子系列。
4.  **黑产卧底调研**：线上渠道可尝试加撸口子QQ群、网贷中介微信等方式，利用老哥们的集体智慧，以及网贷中介的丰富经验。站在对方的立场上，你更会发现自己风控系统的弱点。线下渠道可去一些欺诈案件多发地，实地调研来学习黑产的手法。实地调研的难度相对较高。


#### 3) 反欺诈模型
##### **反欺诈常用数据**
**1.设备指纹**
唯一标识出该设备的设备特征或者独特的设备标识；从技术方法上，可分为：
主动式设备指纹和被动式设备指纹。

主动式设备指纹：
一般采用JS代码或SDK，在客户端主动地收集与设备相关的信息和特征，包括： 
- 浏览器特征，包括UA、版本、OS、插件的配置、Canvas特征等； 
- 设备的传感器特征，比如麦克风、加速传感器的特征等； 
- 设备OS的特征，比如是否越狱等；
- 设备的配置，比如网络配置，系统flash的配置等；

主动式设备指纹算法一般将这些信息组合起来，通过特定的hash算法得到一个设备指纹ID值，作为该设备的唯一标识符。

被动式设备指纹：
在终端设备与服务器通信的过程中，从数据报文的协议中提取出该终端设备的OS、协议栈和网络状态相关的特征集，并结合机器学习算法来标识和跟踪具体的终端设备。
**2.生物探针**
采集用户使用手机时的传感器数据和屏幕轨迹数据的技术；
优点： • 无需其他硬件支持 • 用户无感知 • 行为习惯不易改变 • 可以进行连续判断
缺点： • 需要一定的算法支持
**3.设备行为**
APP安装列表；APP行为（包括安装、卸载、打开等）；通话记录、短信等
**4.WiFi列表**
WiFi 名称、接入时间、接入时长等
**5.LBS**
设备与地理空间位置映射数据；
包括IP、基站、WiFi、身份证、手机号及银行卡等多维度的地理位置信息的信息库
用户稳定性判断登录、交易等多环节多重验证
**6.运营商**
特点：完整、真实；
涵盖： • 在网时长 • 通话记录、短信记录 • 网站、APP访问记录 • 位置信息
**7.关联数据**
前提假设：人是社会关系的总和、人以类聚物以群分；
应用：人的社会关系网反映人的特征；
目的：信息核验、度量身份和关系风险、社群团伙发现
**8.多头**
信贷全流程数据；涵盖注册、申请、审批、放款、还款成功、还款失败、逾期等整个信贷周期；场景贴合度高

##### **模型开发难度**
1.  大部分情况下没有标签，无法使用有监督学习算法
2.  欺诈定义模糊，即没有明确的界限将欺诈和非欺诈区分开来
3.  噪声点和异常点（欺诈点）易混淆，需要区分
4.  不了解每种诈骗定义，很难区分不同诈骗类型
5.  在有标签的情况下，也很难单纯依靠监督模型，因为欺诈的方式在不断进化，而监督模型只能识别出现过的欺诈行为
6.  表面看是二分类，实际是多分类问题（每种欺诈类型为一类）

##### **反欺诈常用手段**

1.  可视化了解数据，分析异常情况。如相关矩阵、多维尺度变换(MDS)
2.  时间序列分析，查看欺诈行为是否依赖时间变化
3.  无监督学习，如Isolation Forest、各种Density Based的聚类方法(CBLOF)、KNN
4.  不完全依赖监督模型，采用规则+模型的组合策略
5.  分析异常点是否显著不同
6.  GNN图形神经网络
7.  基于关系网络的图算法
8.  关系网络和知识图谱技术，发现团伙欺诈
##### **基于复杂网络反欺诈**
###### 传统反欺诈的困境：
1. 只覆盖申请人自身的单点属性
2. 只覆盖了静态信息
3. 动态事件数据的处理很繁琐低效
4. 无法整合碎片化、异构数据
###### 应用案例
**app特征挖掘：**
  1. 对APP列表进行拆分，one-hot处理
  2. 删除低频APP
  3. app embedding

**关联特征挖掘：**
  1. 根据id间关联的类型（both/in/out）对边属性的处理 
  • 哪些月份发生关联 
  • 发生关联的月份数量 
  • 关联次数/权重的mean/std/sum 
  • 关联次数/权重的month on month的difference
  2. 一度关联“点”的属性处理
  计算一度关联id的各类风险、分类、APP类型属性衍生变量
  3. “点”与“边”交叉特征
  将（1）一度关系id的风险、分类、APP类型（2）关联类型进行交叉后统计衍生变量

**其他特征挖掘**
  1. 将Node tag与Edge属性交叉后，统计训练集样本中一度关系Node与Edge属性的交叉数据。
  2. 采用Network Embedding的方法，找到一种映射函数，该函数将网络中的每个节点转换为低维度的潜在表示。如Deep Walk、Node2Vec
# 二、大数据应用
# 三、复杂网络应用
# 四、深度学习应用
# 五、机器学习分布式应用
