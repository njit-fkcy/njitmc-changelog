# NJIT MCL 组织 · 变更记录与仓库手册

本仓库（`njit-fkcy/njitmc-changelog`，公开）用来回答三个问题：

1. **谁**在**什么时候**对**什么**做了**哪些更改**
2. 组织里每个仓库**是干什么的**、**由谁维护**、**什么时候要更新**
3. 改完的服务器配置、数据、日志**放在哪、去哪查**（见 §1.4 索引）

---

## 一、组织与基础设施

### 1.1 组织仓库总览（全量 8 个）

组织：<https://github.com/njit-fkcy>（2026-07-12 创建，2026-10-29 起承载发布仓库；公开 6 个、私有 2 个）。

> 下表统计时间为 **2026-11-09**，数据来自 `GET /orgs/njit-fkcy/repos?type=all` 与逐仓的 `releases`、`tags`。

| 仓库 | 可见性 | 作用 | 最新版本 / 统计 | 最近推送 | 维护时机 |
|---|---|---|---|---|---|
| [PCL-NJITMCL](https://github.com/njit-fkcy/PCL-NJITMCL) | 公开 | **启动器主仓库**：二进制发布（每版 10 个资产：9 个运行文件 + 1 个完整 zip）+ **启动器源码**（`source/`）+ 发版说明。根目录只有 `README.md`（10,369 B）与 `source/` | 31 个 Release / 308 个资产 / 40 个 tag（含 `v1.7.0-source`）；最新 `v1.7.0`（id 440034972，2026-11-10 14:55） | 2026-11-10 | 发版：建 `v<版本>` Release、上传 10 资产、提交源码快照、打 `v<版本>-source` |
| [PCL-NJITMCL-Modpacks](https://github.com/njit-fkcy/PCL-NJITMCL-Modpacks) | 公开 | **整合包与存档发布仓库**：每个整合包 / 存档是一个 Release，资产为 `.mrpack` 与分片补丁 | 17 个 Release / 39 个资产 / 17 个 tag；最新 `zshh-1.0.0` | 2026-10-29 | 开新包 / 改包：新建 Release、上传补丁、登记中控 |
| [PCL-NJITMCL-Source](https://github.com/njit-fkcy/PCL-NJITMCL-Source) | 公开（已归档） | **历史源码仓库**（2026-11-02 建）。源码已并入 `PCL-NJITMCL/source/`，本仓库只读留档 | 0 个 Release / 1 个 tag / 约 12.8 MB | 2026-11-02 | 不再更新 |
| [njitmcl-tools](https://github.com/njit-fkcy/njitmcl-tools) | 公开 | **发布工具仓库**：根目录 `README.md`（3,039 B）+ `njitmcl-publisher.py`（99,205 B）；`latest` Release 固定 3 资产 —— py 99,205 B、`publisher.json` 356 B、`config.example.json` 384 B | 1 个 Release（`latest`，id 427538843）/ 3 个资产 | 2026-11-04 | 工具改动：递增 `PUBLISHER_VERSION_CODE`、覆盖 `latest` 同名资产 |
| [njitmc-changelog](https://github.com/njit-fkcy/njitmc-changelog) | 公开 | 本仓库：按日期的变更记录 + 组织仓库与服务器手册。根目录 `README.md` + `changes/`（11 份） | 0 个 Release / 0 个 tag | 2026-11-09 | 每次改动追加 `changes/<日期>.md`；仓库或服务器变化时更新本文件 |
| [njitmc-website](https://github.com/njit-fkcy/njitmc-website) | 私有 | **官网源码**（Flask 中控 + 前端 `app.js`），同时作为 `docs/` 运维文档的 GitHub 副本 | 4 个 Release / 13 个资产 / 5 个 tag；最新 `v0.1.0` | 2026-11-04 | 官网功能改动；文档同步 |
| [njitmc-fkcy](https://github.com/njit-fkcy/njitmc-fkcy) | 公开 | **组织主页仓库**：社团介绍与组织级说明 | 1 个 Release / 1 个资产 / 1 个 tag，`v1.0.0` | 2026-08-13 | 社团介绍变更时 |
| `archive` | 私有 | 社团档案备份（当前为空仓库） | 0 个 Release / 0 个 tag | 2026-11-02 | 极少动 |

**下载入口**：官网 <https://njitmc.club>；更新清单 <https://njitmc.club/update/njitmcl.json>；整合包与存档走 `PCL-NJITMCL-Modpacks`。


### 1.2 内网服务器（WireGuard 组网、资源与整合包中枢）

> 数据为 2026-11-04 经 SSH 只读实测。**本机是社团唯一一台可 SSH 的自管服务器**，同时扮演「组网枢纽 + WG 门户网站 + 整合包/启动器/公共资源分发」四个角色。

#### 主机与网络全貌

| 项目 | 实测值 |
|---|---|
| 主机名 / 系统 | `debian`；Debian GNU/Linux 13 (trixie)，内核 `6.12.94+deb13-amd64` |
| 硬件 | 物理机（技嘉 `B85M-D3V-A`，Intel `i5-4590` 4C4T @3.3GHz，3.7 GiB 内存，无 swap） |
| 磁盘 | `sda3` ext4 228G，已用 50G（23%）；`sda2` ESP 300M |
| 网卡① eth0 | `172.21.17.188/24`（校园**有线内网**，网关 `172.21.17.253`） |
| 网卡② wlan0 | `10.51.175.234/14`（校园 **WiFi 上公网出口**，网关 `10.48.0.1`） |
| 网卡③ wg0 | `10.8.0.1/13`（**WireGuard 服务端**，监听 UDP `51820`，115 个 peer） |
| 域名 | `wg.njit-fkcy.top` → 公共 DNS 解析为 `172.21.17.188`（私网，只有组网内设备可达） |

> 双出口（有线内网 + WiFi 公网）由 `update-routes.sh` + `mua-failover.sh` 管理（见下），目的：让「校园内网访问」走 eth0、「MUA 皮肤站 / 樱花穿透 / 各 DDNS 服务商」这几个必须出公网的流量走 wlan0。

#### 服务清单（systemd）

| 服务（systemd 单元） | 端口 | 用途 | 关键配置 / 数据 |
|---|---|---|---|
| `nginx.service` | 80、443 | WG 门户反代 + `/modpacks/` 静态分发 | `/etc/nginx/sites-available/wg-portal` |
| `website.service` | 3001（node） | **WireGuard 门户网站后端**（自研 Express 应用） | `/opt/website/网站/server.js`、`用户数据/` |
| `wg-quick@wg0` | 51820/udp | WireGuard 组网 | `/etc/wireguard/wg0.conf` |
| `ddns-go.service` | 9876（本机） | `wifi.njit-fkcy.top` 动态 DNS | `/var/lib/ddns-go/.ddns_go_config.yaml` |
| `natfrp.service` | 7102（本机） | 樱花（SakuraFrp）内网穿透 | `/etc/natfrp/config.json` |
| `cron`（`/etc/cron.d/mua-failover`） | — | 每 2 分钟检测 WiFi、双出口故障转移 | `/usr/local/bin/{mua-failover,update-routes}.sh` |
| `ssh.service` | 22 | 运维入口 | `/etc/ssh/sshd_config` |
| `nftables.service` | — | 防火墙 | `/etc/nftables.conf` |
| `exim4.service` | 25（本机） | 系统邮件（默认） | — |
| `wifi-usb-reset.service` | — | USB WiFi 复位 | — |

#### 核心服务是怎么实现的

1. **WireGuard 组网（wg0）**——社团内网的地基
   - 服务端地址 `10.8.0.1/13`，`ListenPort=51820`；`ip_forward=1` 已开（`/etc/sysctl.conf`）。
   - `wg0.conf` 里每个 `[Peer]` 上方都有一行注释标出**归属人/客户端名**（如 `# xwan58401-server1`、`# yaomq-哦哦学妹`、`# pool-003`）。
   - peer 分两类：**个人固定客户端**（`xwan58401` 名下 server1/R7000/xiaomi15/openwrt/hongshikuai/wangrongrong 等，`yaomq` 等成员）+ **临时客户端池** `pool-001`~`pool-107`（租约制，见第 2 点）。
   - 地址段 `10.8.0.0/13`（即 10.8.0.0 ~ 10.15.255.255），每个 peer 的 `AllowedIPs` 是一枚 `/32`（落在 10.10~10.13 段）。
   - 密钥：服务端私钥存放 `/etc/wireguard/server_private.key`，公钥 `/etc/wireguard/server_public.key`。

2. **门户网站 `wireguard-portal`（Node.js + Express）**——组网自助/管理入口
   - 技术栈：Node 22 + Express 4，依赖 `body-parser`、`express-session`、`multer`（上传）、`tweetnacl`（WireGuard 密钥推导）、`fs-extra`。`package.json` 名即 `wireguard-portal`。
   - 登录：MUA 皮肤站 Yggdrasil 认证（`skin.mualliance.ltd/api/yggdrasil/authserver/authenticate`）+ 管理员 TOTP（管理员用户名 `3278226233`，即 `网站/3278226233.jpg` 头像对应账号）。
   - `server.js` 路由一览（实现哪些能力）：
     | 路由 | 能力 |
     |---|---|
     | `/api/check-session` `/api/login` `/api/logout` | 会话与登录 |
     | `/api/lease` `/api/lease-ms` | **临时客户端租约**（登录后自动申领/续租；毫秒版） |
     | `/api/regenerate-config` | 重新生成某客户端配置 |
     | `/api/admin/generate-client` `/download-client` `/delete-client` | 管理端增删客户端、下载 `.conf` |
     | `/api/admin/pool` `/pool/admin-client` `/pool/release` | 客户端池查询 / 池内客户端转管理员 / 释放 |
     | `/api/admin/resources/folder` `/upload` `/delete` | 公共资源目录管理 |
     | `/public-api/list` `/public-api/download` | 公开资源列表与下载 |
     | `/api/admin/users` `/delete-user` | 用户管理 |
     | `/api/admin/ports` `/ports/add` `/ports/remove` | 允许端口（`allowed_ports.json`） |
     | `/api/admin/forwards` `/forwards/add` `/forwards/remove` | **端口转发 DNAT**（`port_forwards.json`） |
   - 关键机制（`server.js` 常量区）：
     - **客户端池**：`POOL_SIZE=100`，临时客户端 `pool-001~107`；`LEASE_TIMEOUT_MS` = 1 小时无握手即回收。
     - **地址自动分配**：`ADDR_START=168427522`（=10.10.0.2）到 `ADDR_END=168689662`，`intToIp()` 转换。
     - **端口转发**：把服务器公网/组网端口 DNAT 到组网内某客户端。实测两条：`25565→10.10.226.170`（net-toolkit，MC 服务器）、`3389→10.11.103.96`（net-toolkit，RDP）。
   - 数据落地 `/opt/website/网站/用户数据/`：`users.json`（用户+其客户端）、`pool.json`（池）、`port_forwards.json`（端口转发）、`allowed_ports.json`（允许端口）。
   - 客户端 `.conf` 输出到 `/opt/website/客户端配置/<用户名>/<客户端名>.conf`，池内为 `_pool/pool-*.conf`；生成后重载 wg0 生效。

3. **nginx**——门户与整合包的统一入口
   - `wg-portal` 站点：80 → 301 → 443；443 上 `/modpacks/` 走 `alias /var/www/modpacks/`（`Accept-Ranges` 断点续传），其余路径反代 `127.0.0.1:3001`。
   - `client_max_body_size 550M`（管理员上传公共资源上限）。
   - TLS 证书 `/etc/nginx/ssl/wg.njit-fkcy.top.{crt,key}`（2026-10-21 签发，acme.sh 每日 03:17 续期，下次 2026-12-20）。

4. **整合包 / 启动器分发（静态）**详情见下方「目录职责」。

5. **启动器自更新清单**
   - 内网侧旧清单 `/opt/website/网站/update/njitmcl.json`：`2.99.1.0` / `versionCode 415` / sha256，指向 `wg.njit-fkcy.top/update/PCL-NJITMCL-2.99.1.0.exe`（6,020,608 B）。
   - 公网侧现役清单 `<https://njitmc.club/update/njitmcl.json>`：`1.6.2` / `450`（见 §1.3）。

6. **ddns-go**——把 WiFi 出口动态 IP 映射成域名
   - 把 `wlan0` 的当前 IP 通过**阿里云 DNS**（alidns）更新到 `wifi.njit-fkcy.top` 的 A 记录；探测频率 `-f 600`（300s）。密钥在 config，凭据只记位置。

7. **SakuraFrp（natfrp）**——把内网服务穿透到公网
   - 樱花内网穿透客户端（`natfrp-service` + `frpc`），隧道 ID `28996239`（`auto_start_tunnels`）。`frpc --stdin_config` 表示隧道清单从樱花云端下发，本地 `/etc/natfrp/config.json` 只存 token / 隧道 id / 远程管理 key（凭据只记位置）。

8. **双出口故障转移（update-routes.sh + mua-failover.sh）**
   - `update-routes.sh`：把 **DNS 服务器、MUA 皮肤站、樱花节点/API、各 DDNS 服务商**的域名解析成 `/32` 主机路由强制走 wlan0，其余流量走 eth0（默认路由表）。/32 清单实时写 `/run/ddns-wifi-routes.list`。
   - `mua-failover.sh`：`cron` 每 2 分钟跑一次；ping `223.5.5.5` 判断 WiFi 是否出网，WiFi 断了就清掉 wlan0 的路由/表/iptables 规则，改走 eth0；恢复则调 `update-routes.sh` 重建。
   - 校园网认证有 `campus_login.py`（`update-routes.sh` 注释提及，绑定 wlan0 源 IP）。
   - 历史痕迹：`/root/fix-asymmetric-route.sh`、`/root/route.bak.*`、`/usr/local/bin/update-routes.sh.bak_snat`、`/root/netbak/`（iptables/nftables/pool/users/wg0.conf/port_forwards 迁移快照）。

#### 目录职责（容量为实测）

| 路径 | 内容 | 现状 |
|---|---|---|
| `/var/www/modpacks/` | 整合包与存档镜像根目录 | **11G** |
| `/var/www/modpacks/<服务器ID>/` | 该服务器的 `.mrpack` 与 `saves/` 存档 | **12 个目录**：`changzhu` 1.3G、`cjzd` 2.1G、`hello-xzc` 2.6G、`wxcs` 1.8G、`zshh` 853M、`thefool` 707M、`tfcr2` 467M、`vanilla-age` 352M、`hgjh` 91M、`lgg` 65M、`mechanomania-aero-0012` 5.8M、`hardcore-tech` 3.0M |
| `/var/www/modpacks/_launcher/v<版本>/` | 启动器镜像：**每版 10 个条目**（9 运行文件 + 完整 zip） | **30 个版本**（`v1.0.3`、`v1.1.0~1.1.2`、`v1.2.0~1.2.3`、`v1.3.0`、`v1.4.0~1.4.6`、`v1.5.0~1.5.9`、`v1.6.0`、`v1.6.1`、`v1.6.2`、`v1.7.0`），合计 **730M** |
| `/var/www/modpacks/_launcher/_source/v<版本>/` | 源码快照 tar.gz + SHA256 | `v1.5.6`：tar.gz 13,325,094 B；`v1.6.0`：tar.gz 13,326,649 B、sha256 `ea5ad4558ab06f48d21714e54dcc45d6665bced91345cd84ea1b61d94ae9a264`、`SHA256.txt` 98 B |
| `/opt/website/网站/` | 门户站点根（服务端 + 前端 + 文档 + 用户数据 + 更新清单） | 19M |
| `/opt/website/网站/server.js` | 门户后端主文件（含路由/常量/密钥） | 见上 |
| `/opt/website/网站/用户数据/` | `users.json`/`pool.json`/`port_forwards.json`/`allowed_ports.json` | 门户运行时数据 |
| `/opt/website/网站/update/` | 启动器自更新清单 + exe | `njitmcl.json` + `PCL-NJITMCL-2.99.1.0.exe` |
| `/opt/website/网站/docs/` | 社团文档副本（提交侧，不参与网站运行） | 11 个条目（含本手册、7 份日志、OAuth 方案） |
| `/opt/website/客户端配置/` | 生成的 `.conf`（`<用户名>/`、`_pool/`） | 组网配置副本 |
| `/opt/website/公共资源/` | 公共下载库（Java、RustDesk、WireGuard、系统镜像、MC 客户端） | 18G |
| `/opt/website/私人资源/` | 成员私有资源 | — |
| `/etc/wireguard/` | `wg0.conf`（115 peer）+ 密钥 + `client_template.conf` + 多份备份 | 组网唯一真源 |
| `/etc/nginx/ssl/` | `wg.njit-fkcy.top.{crt,key}` | TLS 证书 |
| `/root/website_backup_.tar.gz` | 历史整站备份 | 19G |
| `/root/netbak/` | 网络配置 / 池 / 端口转发迁移快照 | 116K |

- 上传方式：发布工具（`paramiko`，连接参数在 `发布工具/config.json`，**凭据不写入文档**），上传后文件权限 644。
- 校验方式：`curl -r 0-1023 <url>` 返回 `206`；HEAD 返回 `200` 且 `Content-Length` 与本地一致（例：`_launcher/v1.5.6/PCL-NJITMCL.exe` = 6,086,144 B，`zshh-1.0.0.mrpack` = 167,046,706 B）。
- 注意：镜像目录数是 12，但中控 `servers` 有 **13 条**记录（3 条在营、10 条已归档；`zhumidamaoxian` 只有记录、无镜像目录）。
- 注意：从本机 `git clone/push github.com:443` 常被校园网出口阻断，改走 GitHub API；`api.github.com` 与 `uploads.github.com` 正常。


### 1.3 公网服务（官网服务器）

> 数据为 2026-11-04 经 **SSH（root@120.26.205.237）实测** + 之前的 HTTP/socket 探测。

| 项目 | 实测值 |
|---|---|
| 域名解析 | `njitmc.club`、`test.njitmc.club` → `120.26.205.237`（阿里公共 DNS 结果） |
| 主机 | 阿里云 ECS（KVM），Ubuntu 24.04.4 LTS，内核 6.8.0-124；2 vCPU（Intel Xeon Platinum）/ 1.6 GiB 内存 / 无 swap；磁盘 `vda3` 40G（用 5.5G/15%）；内网 `192.168.1.249/24`（VPC，公网 `120.26.205.237` 为 EIP）；uptime 114 天 |
| 端口 | 对外 22（sshd）、80、443（nginx）；本地 `127.0.0.1:8080`（正式站）、`127.0.0.1:8081`（测试站）；无 MySQL/PostgreSQL/Redis、无 Docker |
| 首页 | 正式站 200 / 4,697 B；测试站 200 / 4,525 B（`text/html; charset=utf-8`） |
| 前端资源 | `data.js` 16,875 B（两站相同）；`app.js` 正式 126,533 B、测试 103,249 B |
| 更新清单 | <https://njitmc.club/update/njitmcl.json>（即 `/var/www/update/njitmcl.json`）`version=1.7.0`、`versionCode=452`、`files=9`，每文件 `urls=4`（github / 内网 wg / gh-proxy / ghproxy.net） |
| 登录 | `GET /api/mua/oauth/start` → **302** `skin.mualliance.ltd/oauth/authorize?client_id=41&redirect_uri=…&response_type=code&state=<随机>`；callback 非法 state → **302** `/#sso-error?reason=state` |
| 服务器数据 | `GET /api/launcher/servers` 200 / 461,493 B：**13 条**，**3 条在营**（`changzhu`、`lgg`、`mechanomania-aero-0012`） |

**是怎么实现的（SSH 实测）**——官网是自研 Flask 应用「BlockDimension（方块次元网站）」，正式 / 测试两套并行实例：

- **技术栈**：Flask 3.1.3 + Werkzeug 3.1.8 + waitress 3.0.2 + Jinja2 + `mcstatus`（MC 服务器状态查询）+ dnspython / cryptography。进程 `venv/bin/python app.py --prod`，正式站监听 `127.0.0.1:8080`、测试站 `127.0.0.1:8081`，由 systemd `blockdimension.service` / `blockdimension-test.service` 拉起（`EnvironmentFile=/etc/blockdimension[.test].env`）。
- **代码位置**：`/opt/blockdimension/`（正式）、`/opt/blockdimension-test/`（测试），历史快照 `blockdimension-release-0233262`、`blockdimension-sqlite-bedecae`。核心 `app.py`（约 33 条路由，见下）+ `storage.py`（数据层）+ `modern_site.py` / `modern_admin.py` + 模块 `activity / avatars / error_reports / feedback_api / polls_api`；前端在 `modern-public-20261008-01/`（`app.js` 等）与 `manage-public-20261008-01/`。
- **数据（SQLite 多库分离）**：`/var/lib/blockdimension/data/` 下 `website.sqlite3`（主库 733K：服务器/新闻/资源/教程）、`activity.sqlite3`（25.9M 玩家活跃）、`error_reports.sqlite3`（8.6M）、`feedback.sqlite3`、`polls.sqlite3`、`mua_oauth.json`；带多个 `website.sqlite3.bak-*`。
- **nginx**（`/etc/nginx/sites-available/njitmc`）：`default_server` 拦截非 `njitmc.club` 域名 → `return 444`（防恶意域名爬取）；`njitmc.club` 80→301、443 反代 `127.0.0.1:8080`；`/promo/`→`/var/www/promo`、`/update/`→`/var/www/update`（`no-cache`）；正式站证书 `/root/web/njitmc.club.{pem,key}`，测试站证书 `/etc/letsencrypt/live/test.njitmc.club/`。
- **部署/版本管理**：`/opt/blockdimension` 是 git 仓库，remote=`https://github.com/njit-fkcy/njitmc-website.git`（私有）；但服务器有**大量未提交改动**（`app.py`、`app.js`、`manage.js` 等 M 态，`error_reports.py` / `polls_api.py` / `polls_store.py` 未跟踪）——**服务器是运行权威，GitHub 副本滞后**。部署走 `/opt/deploy.sh test|prod`（git fetch + `--ff-only` pull + 重启）。注：`njitmc-website` 仍是官网 `docs/` 运维文档的 GitHub 落点。
- **阿里云组件**：`aliyun.service`（云助手）、`cloudmonitor`（argusagent 云监控）、`AliYunDun`（云盾安骑士）；`ufw` inactive、iptables 默认 ACCEPT（端口管控在阿里云安全组）。
- **凭据位置**：SSH 私钥 `E:\Users\1\Desktop\密钥\njitmc.club\laptop.pem`（登录用户 root）；`.git-credentials`、`.totp_key`、`/etc/blockdimension.env` 含敏感值，**均只记位置不记明文**。

线上接口一览（除单独标注外均为 GET，实测状态码）：

| 接口 | 正式站 | 测试站 | 说明 |
|---|---|---|---|
| `/api/site` | 200 / 544,457 B | 200 / 74,266 B | 全站聚合数据（最大的一个） |
| `/api/servers` | 200 / 463,505 B | 200 / 8,891 B | 服务器列表（公开版） |
| `/api/servers/check` | 200 | 200 | 触发/读取在线状态检查 |
| `/api/launcher/servers` | 200 / 461,493 B | — | 启动器专用服务器列表 |
| `/api/news` | 200 / 76,666 B | 200 / 62,061 B | 新闻 |
| `/api/server-activity` | 200 / 177,697 B | 200 / 176,161 B | 玩家活跃数据 |
| `/api/polls` | 200 | 200 | 投票 |
| `/api/club/events` | 200 | 200 | 社团生活活动列表（公开） |
| `/api/club/profile` | 401 | 401 | 信息模板与个人中心，需登录 |
| `/api/account` | 401 | 401 | 统一账号（MUA / 正版），需登录 |
| `POST /api/club/events/<id>/cancel-signup` | 401 | 401 | 取消报名；需登录 + CSRF，报名截止前且未签到可用 |
| `/api/tutorials` | 200 / 3,563 B | 200 / 2,465 B | 教程 |
| `/api/feedback/session` | 200 | 200 | 反馈会话 |
| `/api/mua/session` | 200 / 38 B | 200 / 38 B | 当前登录态（未登录返回空对象） |
| `/api/resources` | 401 | 401 | 需登录 |
| `/api/feedback`、`/api/mua/logout` | 405 | 405 / 未部署 | 只接受 POST |


### 1.4 服务器资源索引（什么内容去哪查）

> 这是「内容 → 位置」速查表。改配置、查数据、翻日志、找备份，先看这张表。

| 想知道 / 要改 | 去服务器哪里（内网 wg / 公网官网） | 说明 |
|---|---|---|
| WireGuard 所有 peer、谁连了组网 | `/etc/wireguard/wg0.conf` | 每个 peer 上方的注释是归属人/客户端名 |
| WireGuard 服务端密钥 | `/etc/wireguard/server_{private,public}.key` | 私钥还硬编码在 `server.js` 顶部常量 |
| 门户后端逻辑/接口/密钥 | `/opt/website/网站/server.js` | 路由表见 §1.2 第 2 点；含 WG 私钥、TOTP、session secret |
| 门户前端页面 | `/opt/website/网站/{index.html,auth.html}` | 登录页 `auth.html`（有 `.bak` 备份） |
| 门户依赖清单 | `/opt/website/网站/package.json` | `wireguard-portal` 1.0.0 |
| 用户与客户端 | `/opt/website/网站/用户数据/users.json` | 每个用户及名下客户端/地址/配置路径 |
| 临时客户端池 | `/opt/website/网站/用户数据/pool.json` + `/opt/website/客户端配置/_pool/` | 100 个临时客户端，1h 无握手回收 |
| 端口转发（DNAT） | `/opt/website/网站/用户数据/port_forwards.json` | 服务器端口→组网内客户端 |
| 允许端口 | `/opt/website/网站/用户数据/allowed_ports.json` | |
| 生成的客户端 .conf | `/opt/website/客户端配置/<用户名>/<名>.conf` | 服务端 `wg0.conf` 的对应蓝本 |
| nginx 反代/分发规则 | `/etc/nginx/sites-available/wg-portal`（软链到 sites-enabled） | `/modpacks/` 别名、3001 反代、550M 上限 |
| TLS 证书 | `/etc/nginx/ssl/wg.njit-fkcy.top.{crt,key}`；acme 在 `/root/.acme.sh/` | 每日 03:17 续期 |
| DDNS（wifi.njit-fkcy.top） | `/var/lib/ddns-go/.ddns_go_config.yaml` | alidns，凭据只记位置 |
| 樱花穿透隧道 | `/etc/natfrp/config.json`；日志 `/etc/natfrp/Logs/` | 隧道 id 28996239，token 只记位置 |
| 双出口路由/故障转移 | `/usr/local/bin/{update-routes,mua-failover}.sh`；cron `/etc/cron.d/mua-failover` | /32 清单 `/run/ddns-wifi-routes.list`；日志 `/var/log/mua-failover.log` |
| 防火墙规则 | `/etc/nftables.conf`；快照 `/root/netbak/` | 历史 iptables 备份也在 netbak |
| 整合包镜像 | `/var/www/modpacks/<服务器ID>/` | 12 个目录 + `saves/` |
| 启动器版本镜像 | `/var/www/modpacks/_launcher/v<版本>/`、`_source/` | 23 个版本 |
| 启动器自更新清单（内网旧版） | `/opt/website/网站/update/njitmcl.json` | 2.99.1.0；现役清单在公网 njitmc.club |
| 公共资源库 | `/opt/website/公共资源/` | Java/RustDesk/系统镜像/MC 客户端 |
| 门户/资源/整站历史备份 | `/root/website_backup_.tar.gz`（19G）、`/root/netbak/`、`wg0.conf*`、`server.js.bak*` | 改动前先看有无现成备份 |
| 系统盘/分区 | `lsblk -f`、`df -h` | sda3 228G（22%） |
| nginx 日志 | `/var/log/nginx/` | |
| SSH 登录凭据 | 内网：本机 `发布工具/config.json`（`wgHost/wgUser/wgPassword`）；公网：`E:\Users\1\Desktop\密钥\njitmc.club\laptop.pem`（root） | **不写入本文档** |
| 官网后端逻辑/接口（公网） | `/opt/blockdimension/app.py`（正式）、`/opt/blockdimension-test/app.py`（测试） | Flask，约 33 条路由 |
| 官网数据（服务器/新闻/资源/教程） | `/var/lib/blockdimension/data/website.sqlite3` | 主库 733K；另有 activity / error_reports / feedback / polls 各库 |
| 官网前端 JS/CSS | `/opt/blockdimension/modern-public-20261008-01/`（`app.js` 等） | 管理端 `manage-public-20261008-01/` |
| 官网 nginx 反代 | `/etc/nginx/sites-available/{njitmc,njitmc-test}` | `default_server` 444 防爬；`/promo` `/update` 静态 |
| 启动器更新清单（公网现役） | `/var/www/update/njitmcl.json` + `files/` | `1.6.2/450`；`/update/` 静态 `no-cache` |
| 官网正式站证书 | `/root/web/njitmc.club.{pem,key}` | 测试站 `/etc/letsencrypt/live/test.njitmc.club/` |
| 官网部署 | `/opt/deploy.sh test\|prod` | git fetch + `--ff-only` pull + systemctl restart |

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
├── README.md                     本文件：仓库手册 + 服务器手册 + 资源索引 + 变更索引
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
| 2026-10-29 | 分发仓库 / 发布工具 / 官网登录 | 启动器发布线迁回 `PCL-NJITMCL`；两个发布仓库转入组织 `njit-fkcy`（清单、两站 `app.js`、中控 173 处链接同步）；发布工具加自更新并建 `njitmcl-tools`；官网登录改为 MUA 皮肤站 OAuth，随后**删除**密码登录与启动器授权登录 | [changes/2026-10-29.md](changes/2026-10-29.md) |
| 2026-10-30 | 启动器 | 发布 **1.5.4 / 442**：移除客户端「官网授权登录」（`ModSiteAuth.vb`、`njitmcl://` 协议、token 提交） | [changes/2026-10-30.md](changes/2026-10-30.md) |
| 2026-10-31 | 启动器 / 发布工具 | 发布 **1.5.5 / 443**：修 Forge 47.4.10 未覆盖 Java 上限导致的 Java 25 误选；新增整合包 `javaVersion` 字段；发布工具 **1.0.1** | [changes/2026-10-31.md](changes/2026-10-31.md) |
| 2026-11-01 | 启动器 / 分发数据 | 发布 **1.5.6 / 444**：修复新下载客户端无法连组网；修正清单主程序 `path` 不一致 | [changes/2026-11-01.md](changes/2026-11-01.md) |
| 2026-11-02 | 中控数据 | 修 `changzhu-1.0.0` r17 补丁 `sources` 为空；恢复 `javaVersion=21`；重启 `blockdimension` 并验证 | [changes/2026-11-02.md](changes/2026-11-02.md) |
| 2026-11-03 | 发布流程 / 源码 | 新建 `PCL-NJITMCL-Source`，源码快照 351 文件；内网上传 `_source/v1.5.6/`；发布流程九步改**十步** | [changes/2026-11-03.md](changes/2026-11-03.md) |
| 2026-11-04 | 仓库治理 / 工具分发 / 手册 | 新建 `njitmc-changelog`；源码并入 `PCL-NJITMCL`；发布工具改单文件 py；手册补全为 8 仓库 + 内网 wg 服务器 / 公网服务；**并为内网服务器补全「服务实现 + 资源索引」** | [changes/2026-11-04.md](changes/2026-11-04.md) |
| 2026-11-05 | 启动器 / 本机协议残留 | 新增「退出自动断开组网」并发布 **1.5.7 / 445**；随后发布 **1.5.8 / 446**（更新改整包替换、WireGuard 组件统一到 `WireGuard\`）；清理 `ModSiteAuth.vb` 源码引用与 `njitmcl://` 注册表残留 | [changes/2026-11-05.md](changes/2026-11-05.md) |
| 2026-11-08 | 启动器 | 发布 **1.5.9 / 447**：MUA 登录地址固定（保留 LittleSkin 等第三方登录）；修复 6 项登录 / 组网缺陷（换服缓存、无效 validate、组网账密覆盖、错误提示、显示名、空引用） | [changes/2026-11-08.md](changes/2026-11-08.md) |
| 2026-11-09 | 官网 / 社团生活 | 新增「社团生活」板块：活动报名（信息模板、双坐标表头、签到接口、信誉与自动拉黑、xlsx 导出）、投票迁入、个人中心（游玩时长 / 活动记录 / MC 角色绑定）；管理端合并为「活动报名 / 投票管理」两个标签 | [changes/2026-11-09.md](changes/2026-11-09.md) |
| 2026-11-09 | 官网 / 启动器 | 统一账号：网站支持正版账号登录（启动器转交 accessToken），账号可同时绑定 MUA 与正版角色并合并游玩时长；启动器恢复 `njitmcl://` 唤起与底部进度条，修复 MUA 登录跳转；活动支持取消报名，信息模板除 MUA 账号外全部必填；**发布 1.6.0 / 448**（Release 10 资产、清单上线、内网镜像与源码快照） | [changes/2026-11-09.md](changes/2026-11-09.md) |
| 2026-11-09 | 启动器 | **发布 1.6.1 / 449**（纯修复）：更新下载改为源池 + 超时 + 重试，中转站 5xx/超时不再一票否决；给 exe 改过名的安装可正常更新且不多出重复文件；改名后自动补齐同名 `.config`。清单源池剔除两个死源、换入 `ghproxy.net`；四源实测 206。详见 §十四 | [changes/2026-11-09.md](changes/2026-11-09.md) |
| 2026-11-09 | 启动器 | **发布 1.6.2 / 450**（纯修复）：服务器列表接口追加 `pack=2` 更换缓存键，避开旧版残留在用户本机的损坏 HTTP 缓存；GET / HEAD 遇本地缓存读取异常时自动清空 `Cache\Http` 并重试一次；顺带修正清单中文乱码。详见 §十六 | [changes/2026-11-09.md](changes/2026-11-09.md) |
| 2026-11-10 | 启动器 / 官网 | **发布 1.7.0 / 452**：GitHub 直链 IP 优选（默认开启）、官网资源「启动器下载」、`njitmcl://download` 协议与下载目录设置 | [changes/2026-11-10.md](changes/2026-11-10.md) |

**注意**：2026-10-28 及更早的改动不在本仓库，见社团权威档案（本机档案目录 `ai对系统的全局改动及权威索引\`，含档案-01 本机、档案-03 内网 wg、档案-04 官网、档案-05 启动器）。

---

## 五、维护流程

### 每次改动

1. 动手前先在本仓库 `changes/<YYYY-MM-DD>.md` 建文件（一天一份，不存在就创建）。
2. **每完成一个可验证的步骤就追加一行**：`| # | 操作 | 结果与证据 |`。
3. 记录必须含备份文件名与关键验证数据（状态码 / 大小 / sha256 / 日志摘录）。
4. 未推送前不得删除过程记录；失忆时先读这份文件恢复上下文。

### 发布（推送）时

1. 先写「本次概括」：改了什么、影响范围、回滚方式。
2. 再把过程细节压缩为概括 + 关键验证。
3. 把概括同步三处：
   - 本仓库 `changes/<日期>.md`
   - `PCL-NJITMCL/source/docs/`（启动器相关）或 `njitmc-website/docs/`（官网相关）
   - 校内服务器 `wg.njit-fkcy.top:/opt/website/网站/docs/`
4. 在本文件 §四 索引表**新增一行**，指向当天的记录。
5. 同步更新社团权威档案（档案-01/03/04/05 对应章节）。

### 仓库角色变化时

新增 / 改名 / 归档仓库、调整下载源顺序、更换发布工具分发方式 —— 都要更新本文件 §1.1，并在当天 `changes/` 记一条。

### 服务器/基础设施变化时

- 内网 wg 服务器：换 IP、改 nginx 站点、加/停服务器目录、调磁盘或 WireGuard 端口 —— 更新 §1.2。
- 公网服务器：换域名、改中控路径、改更新清单地址 —— 更新 §1.3。
- 新增服务 / 新增关键文件：**同步更新 §1.4 索引表**（这是「内容去哪查」的速查表，不能漏）。
- 本文件里的数字都带采集日期；改动后重跑下面这组命令，按新结果改表。

```bash
# 组织与仓库（需要 token）
curl -s -H "Authorization: Bearer $TOKEN" https://api.github.com/orgs/njit-fkcy/repos?per_page=100&type=all
curl -s -H "Authorization: Bearer $TOKEN" https://api.github.com/repos/njit-fkcy/PCL-NJITMCL/releases?per_page=100

# 更新清单
curl -s https://njitmc.club/update/njitmcl.json          # 期望 version/versionCode/files=9/urls=4
curl -s https://njitmc.club/app.js | grep -o '1\.5\.[0-9]' | sort | uniq -c

# 五源可达性（任一文件）
curl -o /dev/null -w '%{http_code}\n' -r 0-1023 <GitHub 直链>   # 302
curl -o /dev/null -w '%{http_code}\n' -r 0-1023 <中转/内网链接>  # 206

# 内网服务器（组网内）
ssh root@172.21.17.188 'wg show wg0 | grep -c "^peer:"; du -sh /var/www/modpacks /var/www/modpacks/_launcher; ls -1 /var/www/modpacks/_launcher | grep -vc _source; systemctl is-active website natfrp ddns-go nginx'
```


---

## 六、常见问题

| 问题 | 说明 |
|---|---|
| 为什么记录不按仓库拆？ | 一次改动常跨多个仓库，按日期记录更容易还原当时的完整上下文 |
| 仓库可见性 | 公开仓库，任何人可查看；写入权限仅组织成员。记录里不含凭据，内网 IP 与服务器路径属运维信息 |
| 记录和档案有什么区别？ | 本仓库是**按日期的过程流水**，适合查「某天怎么改的」；社团档案是**按系统的现状说明**，适合查「现在是怎样」 |
| 找不到某次改动 | 先看 §四 索引，再在 `changes/` 里全文搜索关键词（版本号、文件名、服务器名） |
| 某文件/配置/数据在服务器哪里 | 看 **§1.4 资源索引**，它按「想知道/要改什么 → 去哪个路径」组织 |
| 本文件的数字会不会过期？ | 会。每张表都标了采集日期与采集方式；改动后按 §五「服务器/基础设施变化时」的命令重跑并更新 |
| GitHub 仓库描述与最新版本不一致 | 已知：`PCL-NJITMCL` 的仓库描述仍停留在「v1.0.3 ~ v1.5.3」。判断最新版请以 Release / tag / 更新清单为准 |