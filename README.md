# qwe123.github.io
玩机源 - iOS jailbreak APT repository
# 一键建源 · 电脑端版

手机版「一键建源 v3.00」（TrollStore 应用）的电脑端等价实现：把 `.deb` 包一键生成 **Cydia / Sileo 静态软件源**（Packages 索引 + Release 签名校验），部署到 GitHub Pages 或任意服务器即可供越狱设备添加。

## 目录结构

```
repo/
├── debs/            # ← 把你的 .deb 包放这里（支持混放多个架构）
├── build.sh         # 一键建源脚本
├── CydiaIcon.png    # 源图标（Cydia 列表里显示）
└── README.md
```

运行 `./build.sh` 后自动生成：

```
Packages            # 包索引（纯文本）
Packages.gz / .bz2 / .xz / .zst   # 四种压缩格式，Sileo 优先用 .zst
Release             # 源元信息 + 所有索引文件的 MD5/SHA1/SHA256 校验值
```

## 快速开始

1. 把所有 `.deb` 包放进 `debs/` 目录（支持 `iphoneos-arm`、`iphoneos-arm64`、`iphoneos-arm64e` 混放，客户端按设备自动选择）。
2. 运行：

```bash
./build.sh          # 一键建源
./build.sh --dedupe # 建源前按「包名+架构」去重，保留最高版本
```

> 需要环境里有 `dpkg-scanpackages`、`gzip`、`bzip2`、`xz` 和 `python3-zstandard`（本机已全部具备）。

## 自定义源信息

通过环境变量覆盖（默认值见下）：

```bash
REPO_ORIGIN="我的源"  REPO_LABEL="我的源"  REPO_DESC="我的 Cydia 源" ./build.sh
```

## 部署到 GitHub Pages

1. 在 GitHub 新建仓库（如 `my-repo`），把 `repo/` **整个目录**推到仓库根目录：

```bash
cd repo
git init && git add -A && git commit -m "init source"
git remote add origin https://github.com/<你的用户名>/my-repo.git
git push -u origin main
```

2. 仓库 Settings → Pages → Source 选 `main` 分支（根目录），等待部署完成（约 1 分钟）。
3. 源地址为：

```
https://<你的用户名>.github.io/my-repo/
```

> 结尾的 `/` 不能省。

## 部署到自有服务器

把 `repo/` 整个目录上传到服务器 Web 根目录即可（如 Nginx/Apache 的网站根下），确保目录有读权限。源地址为 `https://你的域名/目录路径/`。

## 客户端添加源

- **Sileo**：源 → 添加 → 粘贴上面的源地址
- **Cydia**：软件源 → 编辑 → 添加 → 粘贴源地址

## 更新源

往 `debs/` 里新增/删除 `.deb` 后，**必须重新运行 `./build.sh`** 再推送——只改 debs 不重新生成 Packages 和 Release 校验值，客户端会因校验失败无法刷新。

## 与手机版「一键建源」的关系

手机版 App（`com.minis.yiweiyuan`）本质是用 GitHub API 在手机上完成同样的工作（上传 debs → 生成 Packages/Release）。本电脑版做的是同一件事的本地等价实现，适用于批量包、电脑端批量管理，部署产物完全兼容。
