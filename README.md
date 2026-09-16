# jisuxiang-lzcapp（极速箱）

[极速箱](https://github.com/star7th/jisuxiang)（高效开发工具集成平台）的懒猫微服（LazyCat）打包。

## 模式

- **镜像模式（mirror delivery）**：manifest 经国内加速器 `docker.1ms.run` 引用上游镜像 `star7th/jisuxiang:latest`，不复制到懒猫镜像仓库。
- **自动版本**：上游无 SemVer tag，仅有 mutable `latest`。采用 `bump: patch`：Action 对比 `latest` 的 amd64 digest 与上次投递基线（记录在 manifest 的 `# upstream:` 注释），digest 变化时自动将包版本 patch +1，镜像引用保持 digest-pinned。
- **仅发布喵喵商店**（MiaoMiao private store）；官方平台不发布。

## 结构

| 文件 | 说明 |
| --- | --- |
| `package.yml` | 包元数据（`cloud.lazycat.app.jisuxiang`） |
| `lzc-manifest.yml` | 运行配置（单服务 `jisuxiang`，端口 3000） |
| `lzc-build.yml` | 构建配置 |
| `icon.png` | 图标 |
| `.github/lazycat-action.yml` | [lazycat-github-action](https://github.com/ca-x/lazycat-github-action) 配置 |

## 自动化

`lazycat.yml` 工作流（配置文件 PR 时 dry-run 验证、每日定时 + 手动触发）自动：

1. 检查上游 `star7th/jisuxiang:latest` 的 amd64 digest 是否变化；
2. 变化则更新包版本（patch +1）与 manifest 镜像引用（digest-pinned）；
3. 构建 LPK、发布 GitHub Release（`<package-id>-v<version>.lpk`）；
4. 发布喵喵商店。

## 所需 Secrets

| Secret | 说明 |
| --- | --- |
| `APPSTORE_URL` | 喵喵商店 API 地址 |
| `APPSTORE_TOKEN` | 喵喵商店发布令牌 |
| `APP_ID` | 可选，喵喵商店应用 ID |
| `PRIVATE_STORE_GROUP_CODES` | 可选，私有分组码 |
