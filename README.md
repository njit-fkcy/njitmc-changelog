# NJIT MCL 组织 · 变更记录与仓库手册

本仓库（`njit-fkcy/njitmc-changelog`，公开）用来回答三个问题：

1. **谁**在**什么时候**对**什么**做了**哪些更改**
2. 组织里每个仓库**是干什么的**、**由谁维护**、**什么时候要更新**
3. 改动之后，记录放在哪、怎么查

---

## 一、组织与基础设施

### 1.1 组织仓库总览（全量 8 个）

组织：<https://github.com/njit-fkcy>（2026-10-29 由个人账号升级为组织）。

| 仓库 | 可见性 | 作用 | 最新版本 / 统计 | 最近推送 | 维护时机 |
|---|---|---|---|---|---|
| [PCL-NJITMCL](https://github.com/njit-fkcy/PCL-NJITMCL) | 公开 | **启动器主仓库**：二进制发布（每版 10 个资产：9 个运行文件 + 1 个完整 zip）+ **启动器源码**（`source/`）+ 发版说明 | 24 个 Release，最新 `v1.5.6`；tag 25 个 | 2026-11-04 | 发版：建 `v<版本>` Release、上传 10 资产、提交源码快照、打 `v<版本>-source` |
| [PCL-NJITMCL-Modpacks](https://github.com/njit-fkcy/PCL-NJITMCL-Modpacks) | 公开 | **整合包与存档发布仓库**：每个整合包 / 存档是一个 Release，资产为 `.mrpack` 与分片补丁 | 17 个 Release，最新 `zshh-1.0.0` | 2026-10-29 | 开新包 / 改包：新建 Release、上传补丁、登记中控 |
| [PCL-NJITMCL-Source](https://github.com/njit-fkcy/PCL-NJITMCL-Source) | 公开（已归档） | **历史源码仓库**（2026-11-03 建）。源码已并入 `PCL-NJITMCL/source/`，本仓库只读留档 | 0 个 Release，1 个 tag | 2026-11-02 | 不再更新 |
| [njitmcl-tools](https://github.com/njit-fkcy/njitmcl-tools) | 公开 | **发布工具仓库**：`latest` Release 固定 3 资产 —— `njitmcl-publisher.py`（单文件）、`publisher.json`（自更新清单）、`config.example.json` | 1 个 Release（`latest`），最新 tag `latest` | 2026-11-04 | 工具改动：递增 `PUBLISHER_VERSION_CODE`、覆盖 `latest` 同名资产 |
| [njitmc-changelog](https://github.com/njit-fkcy/njitmc-changelog) | 公开 | 本仓库：按日期的变更记录 + 组织仓库与服务器手册 | — | 2026-11-04 | 每次改动追加 `changes/<日期>.md`；仓库或服务器变化时更新本文件 |
| [njitmc-website](https://github.com/njit-fkcy/njitmc-website) | 私有 | **官网源码**（Flask 中控 + 前端 `app.js`），同时作为 `docs/` 运维文档的 GitHub 副本 | 4 个 Release，最新 `v0.1.0`；tag 5 个 | 2026-11-02 | 官网功能改动；文档同步 |
| [njitmc-fkcy](https://github.com/njit-fkcy/njitmc-fkcy) | 公开 | **组织主页仓库**：社团介绍与组织级说明 | 1 个 Release，`v1.0.0` | 2026-08-13 | 社团介绍变更时 |
| `archive` | 私有 | 社团档案备份（当前为空仓库） | 0 个 Release | 2026-11-02 | 极少动 |

**下载入口**：官网 <https://njitmc.club>；更新清单 <https://njitmc.club/update/njitmcl.json>；整合包与存档走 `PCL-NJITMCL-Modpacks`。

### 1.2 内网服务器（WireGuard 组网）

| 项目 | 值 |
|---|---|
| 域名 | `wg.njit-fkcy.top`（80 → 301 → 443） |
| 组网内地址 | `172.21.17.188` |
| 系统 | Debian GNU/Linux 13 (trixie)，内核 6.12.94+deb13-amd64 |
| WireGuard | 接口 `wg0`，UDP `51820`；成员节点在 `10.10.x.x` / `10.11.x.x` 段 |
| nginx | 站点配置 `/etc/nginx/sites-enabled/wg-portal`；证书 `/etc/nginx/ssl/wg.njit-fkcy.top.{crt,key}`，acme.sh 每日 03:17 续期 |
| 静态分发 | `location ^~ /modpacks/` → `alias /var/www/modpacks/`，带 `Accept-Ranges`（支持断点续传），`client_max_body_size 550M` |
| 门户 | 其余路径反代 `127.0.0.1:3001`（`/opt/website/网站/server.js`，静态页 `index.html` / `auth.html`） |
| 磁盘 | 根分区 228G，已用 50G（23%） |

目录职责：

| 路径 | 内容 | 现状 |
|---|---|---|
| `/var/www/modpacks/` | 整合包与存档镜像根目录 | 11G |
| `/var/www/modpacks/<服务器ID>/` | 该服务器的 `.mrpack` 与 `saves/` 存档（如 `zshh/zshh-1.0.0.mrpack`） | 12 个服务器：`changzhu`、`cjzd`、`hardcore-tech`、`hello-xzc`、`hgjh`、`lgg`、`mechanomania-aero-0012`、`tfcr2`、`thefool`、`vanilla-age`、`wxcs`、`zshh` |
| `/var/www/modpacks/_launcher/v<版本>/` | 启动器镜像：9 个运行文件 + 完整 zip | 从 `v1.0.3` 到 `v1.5.6`，共 23 个版本，合计 540M |
| `/var/www/modpacks/_launcher/_source/v<版本>/` | 启动器源码 `PCL-NJITMCL-Source-<版本>.tar.gz` + `SHA256.txt` | 目前只有 `v1.5.6`（13M） |
| `/opt/website/网站/docs/` | 社团文档副本（提交侧，不参与网站运行） | 8 份变更日志 + OAuth 接入方案 |
| `/opt/website/{公共资源,私人资源,资源,用户数据,客户端配置}` | 门户资源目录 | 运行中 |

- 上传方式：发布工具（`paramiko`，连接参数在 `发布工具/config.json`，**凭据不写入文档**），上传后文件权限 644。
- 校验方式：`curl -r 0-1023 <url>` 应返回 `206`；GitHub 直链返回 `302`。
- 注意：从本机 `git clone/push github.com:443` 常被校园网出口阻断，改走 GitHub API；`api.github.com` 与 `uploads.github.com` 正常。

### 1.3 公网服务

| 项目 | 值 |
|---|---|
| 正式站 | <https://njitmc.club>（中控 `blockdimension`，源码 `/opt/blockdimension/`） |
| 测试站 | <https://test.njitmc.club>（`/opt/blockdimension-test/`） |
| 更新清单 | <https://njitmc.club/update/njitmcl.json>（9 个文件 × 5 个下载源） |
| 主机 | 阿里云 `120.26.205.237` |
| 登录 | MUA 皮肤站 OAuth，回调 `/api/mua/oauth/callback` |

公网主机的中控数据、SMTP、服务重启等细节见档案-04（`档案-04-社团官网njitmc-club.md`）。

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
- 复核方式（每次发版或大改后）：`curl -r 0-1023` 内网文件应 206、GitHub 直链应 302、`/update/njitmcl.json` 应 200 且 `files=9`、每个文件 `urls=5`。

---

## 六、常见问题

| 问题 | 说明 |
|---|---|
| 为什么记录不按仓库拆？ | 一次改动常跨多个仓库（例如发版同时动二进制、源码、清单、官网），按日期记录更容易还原当时的完整上下文 |
| 仓库可见性 | 公开仓库，任何人可查看；写入权限仅组织成员。记录里不含凭据，内网 IP 与服务器路径属运维信息 |
| 记录和档案有什么区别？ | 本仓库是**按日期的过程流水**，适合查「某天怎么改的」；社团档案是**按系统的现状说明**，适合查「现在是怎样」 |
| 找不到某次改动 | 先看 §四 索引，再在 `changes/` 里全文搜索关键词（版本号、文件名、服务器名） |
