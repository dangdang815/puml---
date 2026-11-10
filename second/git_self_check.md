# 实验二活动四 Git diff逐项核对自查记录
## Git提交版本信息
基准手工版本commit：71ee83b 【实验二手工建模部分，second/arch.puml】
第一轮AI方案commit：289eb3f 【第一轮AI建模方案A，second/projectA.puml】
第二轮AI方案commit：85524cd 【第二轮AI建模方案B，second/projectB.puml】

## 使用的diff对比命令
1. 手工模型 VS 第一轮AI方案A：
`git diff 71ee83b:second/arch.puml 289eb3f:second/projectA.puml`
2. 手工模型 VS 第二轮AI方案B：
`git diff 71ee83b:second/arch.puml 85524cd:second/projectB.puml`

说明：
- `-`：手工模型存在，AI方案缺失/被替换的内容
- `+`：AI方案新增、手工模型原本不存在的节点/注释
- 依据diff输出差异，对照报告表16‑3逐项识别缺陷；识别完成后修改arch.puml完成缺陷修正。

|序号|表16‑3缺陷描述|缺陷来源（A/B）|diff中观察到的差异现象|对arch.puml的修正操作|
| ---- | ---- | ---- | ---- | ---- |
|1|首期引入Redis分布式锁、热点缓存|B|projectB新增`Redis缓存服务器`节点，实现会话、热点缓存、分布式锁；手工版本无Redis组件|不加入Redis节点；仅增加注释：Redis可作为后期演进增强选项，首期依靠MySQL乐观锁实现防超售|
|2|引入MySQL主从读写分离|B|projectB新增MySQL主库、从库节点，读写分离；手工版本为单MySQL实例|首期保持单MySQL8.0实例；注释标记主从架构为后期演进项|
|3|应用双实例+Nginx负载均衡+Redis共享会话|B|projectB出现应用服务器1、应用服务器2双实例、Nginx负载均衡，依赖Redis做会话共享；手工为单应用服务器|删除双应用实例；保留单SpringBoot应用服务器；Nginx不作为首期强制组件，标记为可扩展项|
|4|粘性会话/Spring Session JDBC|B|projectB依靠Redis实现共享会话，隐含分布式会话方案；手工无分布式会话相关逻辑|删除分布式会话相关实现，注释说明后期可采用JWT无状态会话方案|
|5|微服务/API网关/MQ/服务注册发现/分布式事务|无(A、B均未出现)|A、B两份AI输出均未引入微服务、网关、MQ组件，该缺陷在本两份架构输出中未体现|维持单体分层架构，增加注释：模块化分包，保留未来微服务拆分潜力|
|6|独立定时任务/对账批处理节点|无(A、B均未出现)|A、B均没有画出独立定时任务物理节点，该缺陷未出现|注释说明定时、对账批处理逻辑内嵌在SpringBoot应用实例内部实现|
|7|限流组件作为首期核心|无(A、B均未出现)|A、B均没有限流组件节点，该缺陷未出现|限流标记为可选后期增强功能，首期不部署该组件|
|8|部署节点过多，运维成本高于收益|B|B方案节点包含Nginx、双应用实例、Redis、MySQL主从，节点数量多，运维复杂度高；手工只有浏览器‑应用‑数据库三层|裁剪部署节点集合为：浏览器客户端 → SpringBoot应用服务器 → MySQL数据库服务器|
|9|REST当作传输协议，标注“HTTPS/REST”|A、B|A、B链路标注写`HTTPS/REST`；手工原始使用`HTTPS / REST`，REST是架构风格不是传输协议|修改链路注释，统一写`HTTPS（RESTful API）`；层内调用标注`本地方法调用`|
|10|未明确HTTPS终止与Vue3静态资源托管|A、B|手工原始缺少专门注释；A把静态资源交给Nginx，B将Vue3 SPA交由Nginx托管|增加note注释：首期可由SpringBoot托管Vue3静态资源，后续可接入Nginx做HTTPS终止|
|11|未显式写“客户端不得直接访问数据库”约束|A、B|A、B新增note写了该约束，手工原始puml缺少该note|在arch.puml增加note，明确禁止客户端直接访问数据库，全部请求经过应用服务器|
|12|未体现“并发预订不超售”策略|B|B方案依靠Redis分布式锁防超售；手工原始缺少防超售策略note|补充note注释：依靠Service层事务 + MySQL乐观锁version字段，实现预订防超售，不依赖分布式锁|
|13|未体现“前台高峰可用”策略|A、B|手工原始缺少可用性相关注释；A/B只在扩展节点体现高可用能力|补充注释：依靠数据库连接池、定时备份、快速重启保障可用性；多实例部署作为演进项|
|14|未体现“可维护”策略|A、B|手工原始缺少可维护策略note|puml内增加note：严格分层、MyBatis外置SQL、统一日志与全局异常处理保障可维护性|
|15|未体现“可扩展”演进接口|A、B|A/B演示了扩展组件，但是没有演进说明注释|增加演进注释：采用无状态设计、业务模块化分包，预留Nginx、Redis、主从数据源配置扩展点|
|16|技术栈与节点命名不统一|A、B|A中MySQL简写为MySQL，B完整写MySQL8.0；手工统一为MySQL8.0|统一所有节点文字描述：Spring Boot3.2（内嵌Tomcat）、MySQL 8.0，消除命名不一致|
|17|前台工作站被误写C/S客户端|A、B|A/B全部节点均为浏览器，没有C/S客户端，该缺陷未出现|维持B/S浏览器访问模型；注释说明前台工作站使用浏览器访问系统|
|18|未体现报表/会员模块部署组织|A、B|A/B只笼统写Controller/Service，没有业务子系统分包说明|增加note注释：Controller与Service内部按照预订、前台接待、客房、会员、系统管理五个业务子系统分包组织|

## 自查结论
1. 使用两条`git diff 提交哈希:文件路径`跨版本对比命令，将71ee83b手工基准arch.puml分别和289eb3f(projectA.puml)、85524cd(projectB.puml)进行比对，读取diff输出的增删差异，对照表16‑3逐项识别AI架构方案缺陷。
2. projectA.puml、projectB.puml保持原始AI输出，不做任何修改，作为评估原始证据保存在Git仓库，不覆盖手工建模文件。
3. 根据diff识别得到的全部缺陷，手动编辑arch.puml完成缺陷修正，提交新版本commit得到报告正式使用的体系结构图。
4. Git时序校验：手工基准commit（71ee83b）提交时间早于两轮AI方案的commit，严格满足“活动一手工建模在AI环节开启前入库”实验指导书要求。
5. 本次自查记录文档、diff原始补丁文件一并提交存入Git仓库，可供教师复核完整评估流程。
