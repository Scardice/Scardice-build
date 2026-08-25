# Scardice

## 简介

余烬骰工程代码合并仓库，用于实现全平台自动出包。

使用 git submodule 机制整合以下四个仓库的代码
- [Scardice-core](https://github.com/Scardice/Scardice-core)：余烬核心，即余烬骰的后端工程代码；
- [Scardice-ui](https://github.com/Scardice/Scardice-ui)：余烬骰的前端工程代码；
- [sealdice-android](https://github.com/sealdice/sealdice-android)：余烬骰的 Android 工程代码；
- [sealdice-builtins](https://github.com/sealdice/sealdice-builtins)：其他余烬骰所需的资源文件仓库，包括牌堆、helpdoc 等；
- [go-cqhttp](https://github.com/sealdice/go-cqhttp)：go-cqhttp 的 fork。

克隆该项目时需要使用 `git clone --recursive` 命令以将子模块代码一并拉取。

## 细节

### 手动构建

工作流为 [auto-build.yml](.github/workflows/auto-build.yml)，只支持手动触发：

1. 打开 GitHub Actions，选择 `Manual Build`；
2. 点击 `Run workflow`，配置 core/UI 分支、版本信息、单目标或多目标、CGO、兼容命名、UPX 和运行时资源选项；
3. 构建完成后，从 `Scardice-build-*` Artifact 下载 `output/` 中的压缩包。

该工作流直接调用 `Scardice-core/build.sh`，由脚本负责构建 UI、编译 core、下载运行时资源并打包。默认目标为：

```text
linux/amd64,windows/amd64,darwin/arm64
```

如果配置了以下 Repository secrets，工作流会临时写入脚本约定的签名文件：

- `SCARDICE_TRUSTED_CLIENT_PRIVATE_KEY`
- `SCARDICE_SIGN_CLIENT_PRIVATE_KEY`
- `SCARDICE_SIGN_V3_URL`
- `BUILD_PROXY`（可选下载代理）

### Nightly 自动构建

[Update submodules daily](.github/workflows/update-submodules.yml) 每天 03:00 UTC 检查并跟进各个子模块的上游分支。只有检测到子模块指针实际变化时，才会触发 [Nightly Build](.github/workflows/nightly-build.yml)。

Nightly 使用与手动构建相同的 `Scardice-core/build.sh` 流程，默认构建 `linux/amd64`、`windows/amd64` 和 `darwin/arm64`，并把产物更新到唯一的 `Nightly` 预发布版本中；旧的 Nightly 附件会被删除后重新上传。

## 关于 issue 和 pull request

你可以通过 fork 本项目并提交 pull request 的形式贡献代码
