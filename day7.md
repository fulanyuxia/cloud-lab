# Day 7 — MQTT 第一次跑通

> 与 AI 协作完成，每步在真实服务器执行过。未理解的已标注。
> （笔记里不写服务器 IP，公网地址留在本地记录里就够了。）

## 今天做了

- Docker 跑起 mosquitto：`docker run -d --name mqtt -p 1883:1883 eclipse-mosquitto:2`
- 第一次 pub/sub 成功：`lab/temp` 收到 `hello`
- 验证了主题不匹配收不到：订阅 `lab/temp`，发 `lab/humidity` → 没反应
- 验证通配符 `+` 只吃一层：订 `lab/+/temp`，收到 room1/room2 的 A、B，没收到 `lab/a/b/temp` 的 C
- 凌晨补做了 `-r`（retain）实验，一条命令收到 A、B 两条，自动退出

## 今天懂了

- MQTT 是邮局模式：发布者和订阅者互不认识，全靠 broker 按主题转发
- **不存消息**：发的那一刻没人订阅，直接丢。错过就是错过（这个我撞了三次才信）
- `-r` 例外：broker 保留该主题最后一条，后来者一连上就补发
- 判断程序死活：命令下面有新提示符=死了；只有光标=活着。sub 卡住是正常状态，不是出 bug
- `Error response from daemon: No such container` 是 docker 在门口拦的，跟网络无关——我把容器名 mqtt 打成了 mqitto

## 踩的坑（这部分最值钱）

1. 把 pub 和 sub 的分工搞反了两次：pub=发（秒回提示符），sub=收（一直卡着）
2. 每次 sub 卡住我就以为"坏了"，按 Ctrl+C 杀掉再去发消息——等于把实验对象删了再找证据
3. AI 怀疑我引号打成了全角，猜错了，真正原因是第 2 条。教训：报错要看原文，猜要允许被推翻
4. 用 `mosquitto_sub -t lab/+/temp -C 2` 一条命令自己收自己退，比开两个窗口来回切省事得多——最后就是这么通的

## 明天问

- `-r` 保留的消息，后来者订阅 `lab/+/temp` 时三层的那条 C 会不会也被补发？（感觉不会，但要敲一遍）
- MQTT 的 QoS 0/1/2 是什么？听说和"消息会不会丢"有关
