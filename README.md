# NJIT MCL 组织 · 变更记录与仓库手册

本仓库（`njit-fkcy/njitmc-changelog`，公开）用来回答三个问题：

1. **谁**在**什么时候**对**什么**做了**哪些更改**
2. 组织里每个仓库**是干什么的**、**由谁维护**、**什么时候要更新**
3. 改动之后，记录放在哪、怎么查

---

## 一、组织与基础设施

### 1.1 组织仓库总览（全量 8 个）

组织：<https://github.com/njit-fkcy>（2026-07-12 创建，2026-10-29 起承载发布仓库；公开 6 个、私有 2 个）。

> 下表统计时间为 **2026-11-04**，数据来自 `GET /orgs/njit-fkcy/repos?type=all` 与逐仓的 `releases`、`tags`。

| 仓库 | 可见性 | 作用 | 最新版本 / 统计 | 最近推送 | 维护时机 |
|---|---|---|---|---|---|
| [PCL-NJITMCL](https://github.com/njit-fkcy/PCL-NJITMCL) | 公开 | **启动器主仓库**：二进制发布（每版 10 个资产：9 个运行文件 + 1 个完整 zip）+ **启动器源码**（`source/`）+ 发版说明。根目录只有 `README.md`（10,369 B）与 `source/` | 24 个 Release / 238 个资产 / 25 个 tag（含 `v1.5.6-source`）；最新 `v1.5.6`（id 430622853，2026-11-01 14:03） | 2026-11-04 | 发版：建 `v<版本>` Release、上传 10 资产、提交源码快照、打 `v<版本>-source` |
| [PCL-NJITMCL-Modpacks](https://github.com/njit-fkcy/PCL-NJITMCL-Modpacks) | 公开 | **整合包与存档发布仓库**：每个整合包 / 存档是一个 Release，资产为 `.mrpack` 与分片补丁 | 17 个 Release / 39 个资产 / 17 个 tag；最新 `zshh-1.0.0` | 2026-10-29 | 开新包 / 改包：新建 Release、上传补丁、登记中控 |
| [PCL-NJITMCL-Source](https://github.com/njit-fkcy/PCL-NJITMCL-Source) | 公开（已归档） | **历史源码仓库**（2026-11-02 建）。源码已并入 `PCL-NJITMCL/source/`，本仓库只读留档 | 0 个 Release / 1 个 tag / 约 12.8 MB | 2026-11-02 | 不再更新 |
| [njitmcl-tools](https://github.com/njit-fkcy/njitmcl-tools) | 公开 | **发布工具仓库**：根目录 `README.md`（3,039 B）+ `njitmcl-publisher.py`（99,205 B）；`latest` Release 固定 3 资产 —— py 99,205 B、`publisher.json` 356 B、`config.example.json` 384 B | 1 个 Release（`latest`，id 427538843）/ 3 个资产 | 2026-11-04 | 工具改动：递增 `PUBLISHER_VERSION_CODE`、覆盖 `latest` 同名资产 |
| [njitmc-changelog](https://github.com/njit-fkcy/njitmc-changelog) | 公开 | 本仓库：按日期的变更记录 + 组织仓库与服务器手册。根目录 `README.md`（12,230 B）+ `changes/`（7 份） | 0 个 Release / 0 个 tag | 2026-11-04 | 每次改动追加 `changes/<日期>.md`；仓库或服务器变化时更新本文件 |
| [njitmc-website](https://github.com/njit-fkcy/njitmc-website) | 私有 | **官网源码**（Flask 中控 + 前端 `app.js`），同时作为 `docs/` 运维文档的 GitHub 副本 | 4 个 Release / 13 个资产 / 5 个 tag；最新 `v0.1.0` | 2026-11-04 | 官网功能改动；文档同步 |
| [njitmc-fkcy](https://github.com/njit-fkcy/njitmc-fkcy) | 公开 | **组织主页仓库**：社团介绍与组织级说明 | 1 个 Release / 1 个资产 / 1 个 tag，`v1.0.0` | 2026-08-13 | 社团介绍变更时 |
| `archive` | 私有 | 社团档案备份（当前为空仓库） | 0 个 Release / 0 个 tag | 2026-11-02 | 极少动 |

**下载入口**：官网 <https://njitmc.club>；更新清单 <https://njitmc.club/update/njitmcl.json>；整合包与存档走 `PCL-NJITMCL-Modpacks`。


### 1.2 内网服务器（WireGuard 组网）

> 数据为 2026-11-04 经 SSH 只读实测（`wg show`、`ss -lunp`、`ls`、`du -sh`、`df -h`、`cat /etc/nginx/sites-enabled/wg-portal`）。

| 项目 | 实测值 |
|---|---|
| 域名 / 地址 | `wg.njit-fkcy.top`；公共 DNS（阿里）解析为 `172.21.17.188`（私网地址，只有接入组网的设备可达） |
| 系统 | Debian GNU/Linux 13 (trixie)，内核 `6.12.94+deb13-amd64`，4 核 / 3.7 GiB 内存 |
| WireGuard | 接口 `wg0`，UDP `51820`；**115 个 peer**，地址分布在 `10.10.x.x`～`10.13.x.x`（每个 `/32`）；采集时仅 2 个 peer 有近期握手 |
| nginx | 唯一站点配置 `/etc/nginx/sites-enabled/wg-portal`：80 端口 301 跳 443；证书 `/etc/nginx/ssl/wg.njit-fkcy.top.{crt,key}`（2026-10-21 签发，acme.sh 每日 03:17 续期，下次 2026-12-20） |
| 静态分发 | `location ^~ /modpacks/` → `alias /var/www/modpacks/`，`Accept-Ranges: bytes`（断点续传），`client_max_body_size 550M` |
| 门户 | 其余路径反代 `127.0.0.1:3001`（`node /opt/website/网站/server.js`，监听 `0.0.0.0:3001`） |
| 磁盘 | 根分区 228G，已用 50G（23%） |

目录职责（容量为实测）：

| 路径 | 内容 | 现状 |
|---|---|---|
| `/var/www/modpacks/` | 整合包与存档镜像根目录 | **11G** |
| `/var/www/modpacks/<服务器ID>/` | 该服务器的 `.mrpack` 与 `saves/` 存档 | **12 个目录**：`changzhu` 1.3G、`cjzd` 2.1G、`hello-xzc` 2.6G、`wxcs` 1.8G、`zshh` 853M、`thefool` 707M、`tfcr2` 467M、`vanilla-age` 352M、`hgjh` 91M、`lgg` 65M、`mechanomania-aero-0012` 5.8M、`hardcore-tech` 3.0M |
| `/var/www/modpacks/_launcher/v<版本>/` | 启动器镜像：**每版 10 个条目**（9 个运行文件 + 完整 zip） | **23 个版本**（`v1.0.3`、`v1.1.0`~`v1.1.2`、`v1.2.0`~`v1.2.3`、`v1.3.0`、`v1.4.0`~`v1.4.6`、`v1.5.0`~`v1.5.6`），合计 **540M** |
| `/var/www/modpacks/_launcher/_source/v<版本>/` | `PCL-NJITMCL-Source-<版本>.tar.gz` + `SHA256.txt` | 仅 `v1.5.6`：tar.gz 13,325,094 B、`SHA256.txt` 98 B、内容 `c3ad5f2c…ac3e` |
| `/opt/website/网站/docs/` | 社团文档副本（提交侧，不参与网站运行） | 84K / 10 个条目：7 份日志（10-29～11-04）+ 1 个 `.bak` + OAuth 接入方案 + 本手册 |
| `/opt/website/` | 门户站点与资源（`公共资源`、`私人资源`、`资源`、`用户数据`、`客户端配置`、`网站`） | 18G（其中 `网站/` 19M） |

- 上传方式：发布工具（`paramiko`，连接参数在 `发布工具/config.json`，**凭据不写入文档**），上传后文件权限 644。
- 校验方式：`curl -r 0-1023 <url>` 返回 `206`；HEAD 返回 `200` 且 `Content-Length` 与本地一致（例：`_launcher/v1.5.6/PCL-NJITMCL.exe` = 6,086,144 B，`zshh-1.0.0.mrpack` = 167,046,706 B）。
- 注意：镜像目录数是 12，但中控 `servers` 有 **13 条**记录（3 条在营、10 条已归档；`zhumidamaoxian` 只有记录、无镜像目录），两者不要混为一谈。
- 注意：从本机 `git clone/push github.com:443` 常被校园网出口阻断，改走 GitHub API；`api.github.com` 与 `uploads.github.com` 正常。


### 1.3 公网服务

> 数据为 2026-11-04 实测（`curl`、HEAD、以及从线上 `app.js` 提取 `/api/` 路径后逐个 GET）。

| 项目 | 实测值 |
|---|---|
| 域名解析 | `njitmc.club`、`test.njitmc.club` → `120.26.205.237`（阿里公共 DNS 结果） |
| 首页 | 正式站 200 / 4,697 B；测试站 200 / 4,525 B（`text/html; charset=utf-8`） |
| 前端资源 | `data.js` 16,875 B（两站相同）；`app.js` 正式 126,533 B、测试 103,249 B |
| 版本一致性 | 两站线上 `app.js` 均含 `1.5.6` **11 处**，`1.5.3`/`1.5.4`/`1.5.5` 各 **0 处**，`njit-fkcy` 5 处 |
| 更新清单 | <https://njitmc.club/update/njitmcl.json> 200 / 7,421 B，`version=1.5.6`、`versionCode=444`、`files=9`，每个文件 `urls=5`；源域名 = `github.com`、`gh-proxy.com`、`ghfast.top`、`gh.llkk.cc`、`wg.njit-fkcy.top` |
| 登录 | `GET /api/mua/oauth/start` → **302** 到 `https://skin.mualliance.ltd/oauth/authorize?client_id=41&redirect_uri=https%3A%2F%2Fnjitmc.club%2Fapi%2Fmua%2Foauth%2Fcallback&response_type=code&state=<随机>`；`/api/mua/oauth/callback` 在 state 缺失或非法时 **302** 到 `/#sso-error?reason=state` |
| 服务器数据 | `GET /api/launcher/servers` 200 / 461,493 B：**13 条**记录，**3 条在营**（`changzhu`、`lgg`、`mechanomania-aero-0012`），10 条已归档 |

线上接口一览（均为 GET，实测状态码）：

| 接口 | 正式站 | 测试站 | 说明 |
|---|---|---|---|
| `/api/site` | 200 / 544,457 B | 200 / 74,266 B | 全站聚合数据（最大的一个） |
| `/api/servers` | 200 / 463,505 B | 200 / 8,891 B | 服务器列表（公开版） |
| `/api/servers/check` | 200 | 200 | 触发/读取在线状态检查 |
| `/api/launcher/servers` | 200 / 461,493 B | — | 启动器专用服务器列表 |
| `/api/news` | 200 / 76,666 B | 200 / 62,061 B | 新闻 |
| `/api/server-activity` | 200 / 177,697 B | 200 / 176,161 B | 玩家活跃数据 |
| `/api/polls` | 200 | 未部署 | 投票 |
| `/api/tutorials` | 200 / 3,563 B | 200 / 2,465 B | 教程 |
| `/api/feedback/session` | 200 | 200 | 反馈会话 |
| `/api/mua/session` | 200 / 38 B | 200 / 38 B | 当前登录态（未登录返回空对象） |
| `/api/resources` | 401 | 401 | 需登录 |
| `/api/feedback`、`/api/mua/logout` | 405 | 405 / 未部署 | 只接受 POST |

公网主机的目录、中控服务、SMTP 等细节见档案-04（`档案-04-社团官网njitmc-club.md`），本手册只记录可复验的对外表现。


---

## 二、记录规范

每条记录必须能独立看懂，包含六要素：

| 字段 | 要求 |
|---|---|
| 时间 | `YYYY-MM-DD`，必要时带时分 |
| 操作者 | GitHub 账号 / 社团成员（多人协作时必填） |
| 对象 | 具体到仓库、文件、配置项、服务器路径 |
| 改了什么 | 用「新增 / 修改 / 删除 / 修复 / 更正」开头，写清前后差异 |
| 验证 | 实际执行过的检查与结果（状态码、大小、sha256、日志摘录） |
| 回滚 | 备份文件名或反向操作步骤 |

**三条硬规则**

1. 只写**实际执行并通过验证**的操作，禁止「改完即报成功」。
2. **不记录凭据**：Token、Client Secret、SMTP 授权码、SSH 密码一律只写「存放位置」。
3. 档案只增不改；发现写错时追加一条「更正」，注明「发现档案过时」。

---

## 三、目录结构

```
njitmc-changelog/
├── README.md                     本文件：仓库手册 + 服务器手册 + 变更索引
└── changes/
    ├── 2026-10-29.md             按日期，一天一份
    ├── 2026-10-30.md
    ├── 2026-10-31.md
    ├── 2026-11-01.md
    ├── 2026-11-02.md
    ├── 2026-11-03.md
    └── 2026-11-04.md
```

本文件另有副本：内网服务器 `/opt/website/网站/docs/组织仓库与服务器手册.md`、官网仓库 `njitmc-website/docs/`。

启动器源码不放在本仓库，位置见 [PCL-NJITMCL](https://github.com/njit-fkcy/PCL-NJITMCL) 的 `source/` 目录（每个版本对应一个 `v<版本>` tag）。

---

## 四、变更索引

| 日期 | 范围 | 摘要 | 记录 |
|---|---|---|---|
| 2026-10-29 | 分发仓库 / 发布工具 / 官网登录 | 启动器发布线迁回 `PCL-NJITMCL`；两个发布仓库转入组织 `njit-fkcy`（清单、两站 `app.js`、中控 173 处链接同步）；发布工具加自更新并建 `njitmcl-tools`；官网登录改为 MUA 皮肤站 OAuth，随后**删除**密码登录与启动器授权登录（含一次误删前端函数并修复） | [changes/2026-10-29.md](changes/2026-10-29.md) |
| 2026-10-30 | 启动器 | 发布 **1.5.4 / 442**：移除客户端「官网授权登录」（`ModSiteAuth.vb`、`njitmcl://` 协议、token 提交） | [changes/2026-10-30.md](changes/2026-10-30.md) |
| 2026-10-31 | 启动器 / 发布工具 | 发布 **1.5.5 / 443**：修 Forge 47.4.10 未覆盖 Java 上限导致的 Java 25 误选；新增整合包 `javaVersion` 字段；发布工具 **1.0.1**（原始包自动识别、自定义项） | [changes/2026-10-31.md](changes/2026-10-31.md) |
| 2026-11-01 | 启动器 / 分发数据 | 发布 **1.5.6 / 444**：修复新下载客户端无法连组网（缺 `WireGuard\` 子目录 + 错误提示误导）；修正清单主程序 `path` 与本地文件名不一致导致的「更新清单中没有启动器主程序」 | [changes/2026-11-01.md](changes/2026-11-01.md) |
| 2026-11-02 | 中控数据 | 修 `changzhu-1.0.0` r17 补丁 `sources` 为空（0→5 源）；恢复 `javaVersion=21`（重发时被重置为 0）；重启 `blockdimension` 并验证 | [changes/2026-11-02.md](changes/2026-11-02.md) |
| 2026-11-03 | 发布流程 / 源码 | 新建 `PCL-NJITMCL-Source` 源码仓库，源码快照 351 文件 / 17.1 MB；内网上传 `_source/v1.5.6/`（tar.gz 13,325,094 B）；发布流程由九步改**十步**（新增「源码同步」） | [changes/2026-11-03.md](changes/2026-11-03.md) |
| 2026-11-04 | 仓库治理 / 工具分发 / 手册 | 新建本仓库 `njitmc-changelog`（公开）作为统一变更记录；启动器源码并入 `PCL-NJITMCL`（`PCL-NJITMCL-Source` 转为归档）；发布工具改为分发单文件 `njitmcl-publisher.py`，不再提供打包 exe；手册补全为组织 **8 个仓库**全量信息 + **内网 wg 服务器 / 公网服务**章节 | [changes/2026-11-04.md](changes/2026-11-04.md) |

**注意**：2026-10-28 及更早的改动不在本仓库，见社团权威档案（本机档案目录 `ai对系统的全局改动及权威索引\`，含档案-01 本机、档案-03 内网 wg、档案-04 官网、档案-05 启动器）。

---

## 五、维护流程

### 每次改动

1. 动手前先在本仓库 `changes/<YYYY-MM-DD>.md` 建文件（一天一份，不存在就创建）。
2. **每完成一个可验证的步骤就追加一行**：

   ```
   | # | 操作 | 结果与证据 |
   ```

3. 记录必须含备份文件名与关键验证数据（状态码 / 大小 / sha256 / 日志摘录）。
4. 未推送前不得删除过程记录；失忆时先读这份文件恢复上下文。

### 发布（推送）时

1. 先写一段「本次概括」：改了什么、影响范围、回滚方式。
2. 再把过程细节压缩为概括 + 关键验证。
3. 把概括同步三处：
   - 本仓库 `changes/<日期>.md`
   - `PCL-NJITMCL/source/docs/`（启动器相关）或 `njitmc-website/docs/`（官网相关）
   - 校内服务器 `wg.njit-fkcy.top:/opt/website/网站/docs/`
4. 在本文件 §四 的索引表**新增一行**，指向当天的记录。
5. 同步更新社团权威档案（档案-01/03/04/05 对应章节）。

### 仓库角色变化时

新增 / 改名 / 归档仓库、调整下载源顺序、更换发布工具分发方式 —— 都要更新本文件 §1.1 的仓库总览，并在当天 `changes/` 里记一条。

### 服务器/基础设施变化时

- 内网 wg 服务器：换 IP、改 nginx 站点、加/停某个服务器目录、调整磁盘或 WireGuard 端口 —— 更新 §1.2。
- 公网服务器：换域名、改中控路径、改更新清单地址 —— 更新 §1.3。
- 本文件里的数字都带采集日期；改动后重跑下面这组命令，按新结果改表。

```bash
# 组织与仓库（需要 token）
curl -s -H "Authorization: Bearer $TOKEN" https://api.github.com/orgs/njit-fkcy/repos?per_page=100&type=all
curl -s -H "Authorization: Bearer $TOKEN" https://api.github.com/repos/njit-fkcy/PCL-NJITMCL/releases?per_page=100

# 更新清单
curl -s https://njitmc.club/update/njitmcl.json          # 期望 version/versionCode/files=9/urls=5
curl -s https://njitmc.club/app.js | grep -o '1\.5\.[0-9]' | sort | uniq -c

# 五源可达性（任一文件）
curl -o /dev/null -w '%{http_code}\n' -r 0-1023 <GitHub 直链>   # 302
curl -o /dev/null -w '%{http_code}\n' -r 0-1023 <中转/内网链接>  # 206

# 内网服务器（组网内）
ssh root@172.21.17.188 'wg show wg0 | grep -c "^peer:"; du -sh /var/www/modpacks /var/www/modpacks/_launcher; ls -1 /var/www/modpacks/_launcher | grep -vc _source'
```


---

## 六、常见问题

| 问题 | 说明 |
|---|---|
| 为什么记录不按仓库拆？ | 一次改动常跨多个仓库（例如发版同时动二进制、源码、清单、官网），按日期记录更容易还原当时的完整上下文 |
| 仓库可见性 | 公开仓库，任何人可查看；写入权限仅组织成员。记录里不含凭据，内网 IP 与服务器路径属运维信息 |
| 记录和档案有什么区别？ | 本仓库是**按日期的过程流水**，适合查「某天怎么改的」；社团档案是**按系统的现状说明**，适合查「现在是怎样」 |
| 找不到某次改动 | 先看 §四 索引，再在 `changes/` 里全文搜索关键词（版本号、文件名、服务器名） |
| 本文件的数字会不会过期？ | 会。每张表都标了采集日期与采集方式；改动后按 §五「服务器/基础设施变化时」的命令重跑并更新 |
| GitHub 仓库描述与最新版本不一致 | 已知：`PCL-NJITMCL` 的仓库描述仍停留在「v1.0.3 ~ v1.5.3」。判断最新版请以 Release / tag / 更新清单为准，描述只是写仓库时填的一句话 |
