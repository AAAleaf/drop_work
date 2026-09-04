# drop_work

随手放一些 CI / 构建相关的东西。

## 构建 Spark-X2.5-4B 的 llama.cpp runtime

`Spark-X2.5-4B` 是 XHToken 出的自定义架构（`spark2_5`）模型，上游
llama.cpp 还没合入支持（PR #27868），所以 LM Studio / Ollama 直接加载会失败。
官方解法是使用 `XHToken/llama.cpp` fork 编译出的 runtime。

本仓库的 `.github/workflows/build-spark-runtime.yml` 会在 GitHub Actions 上
把该 fork 编译成 Windows / Linux 的 `llama-server` / `llama-cli` 等二进制。

### 用法

1. 进入仓库 **Actions → Build Spark llama.cpp runtime**。
2. 点 **Run workflow**（默认 CPU 构建；可选开 CUDA，需 GPU runner）。
3. 跑完在 **Artifacts** 下载 `llama-spark-win-x64` 或 `llama-spark-linux-x64`。

### 本地跑模型

```bash
# 解压后进入 bin 目录
llama-server -m Spark-X2.5-4B-Q4_K_M.gguf --ctx-size 8192 --port 8080
```

起的是 OpenAI 兼容端点，可接 pot / 各类客户端。
要换 LM Studio UI：把 `build/bin/*` 覆盖进
`~/.lmstudio/extensions/backends/llama.cpp-<平台>-<variant>-<hash>/` 即可。

### 版本钉死

workflow 里 `spark_commit` 默认锁在 `4a3635c32fc9f044c2bde9ebeabf50c7e1ec5991`
（2026-09-04），避免上游改动导致构建漂移。需要更新时改默认值或手动传参。
