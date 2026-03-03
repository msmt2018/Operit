# GitHub -> Gitee 同步说明

当前方案分为两段：

1. GitHub Actions：只负责触发 Gitee 镜像拉取（`remote_mirror/pull`）
2. Gitee 流水线：负责把 GitHub Release 二进制上传到 Gitee Release

这样可以避免在 GitHub Runner 里做大文件中转上传。

## GitHub 仓库配置

工作流文件：`.github/workflows/sync-github-to-gitee.yml`

GitHub 仓库需要配置：

1. Repository Variables
   - `GITEE_OWNER`：Gitee 命名空间
   - `GITEE_REPO`：Gitee 仓库名
2. Repository Secrets
   - `GITEE_TOKEN`：Gitee 访问令牌（需仓库写权限）

触发时机：`release`、`delete`、`workflow_dispatch`、`schedule`。

## Gitee 流水线配置

同步脚本：`.github/scripts/sync-release-to-gitee.sh`

建议在 Gitee 仓库的流水线里配置环境变量：

- `SOURCE_GITHUB_REPOSITORY`：例如 `AAswordman/Operit`
- `GITEE_OWNER`
- `GITEE_REPO`
- `GITEE_TOKEN`
- `GITHUB_TOKEN`（可选，私有仓库或防限流时建议配置）

脚本用法：

```bash
# 同步最新一个 release
bash .github/scripts/sync-release-to-gitee.sh latest

# 同步指定 tag 的 release
bash .github/scripts/sync-release-to-gitee.sh tag v1.2.3

# 首次全量补齐历史 release
bash .github/scripts/sync-release-to-gitee.sh all
```

说明：

- 脚本会创建/更新 Gitee Release 元信息。
- 附件按文件名增量上传；已存在同名附件会跳过。
