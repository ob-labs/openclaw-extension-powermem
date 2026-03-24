---
name: install-memory-powermem
description: OpenClaw skill「快速安装」路径（skill id 与目录名均为 install-memory-powermem）：memory-powermem + PowerMem 最少步骤，面向个人用户；无需单独配置 powermem.env，复用 OpenClaw 里已配好的对话模型。可独立分发；完整选项、工具说明与排错见 install-memory-powermem-full。
triggers:
  - "PowerMem 快速安装"
  - "PowerMem 最简单安装"
  - "memory-powermem 最小安装"
  - "怎么装 PowerMem 记忆"
  - "OpenClaw 记忆 怎么装"
  - "安装长期记忆"
  - "Quick install PowerMem"
  - "PowerMem quickstart"
  - "Easiest PowerMem setup"
  - "Minimal install memory-powermem"
  - "Install memory powermem minimal"
---

# PowerMem 记忆 · 快速安装（个人用户）

本 skill 在 OpenClaw 中的标识与建议目录名为 **`install-memory-powermem`**。若需要 HTTP/多实例、工具表与详细排错，请改用 **`install-memory-powermem-full`**（仓库内 `skills/install-memory-powermem-full/`，需一并拷贝该目录下全部 `.md`）。

你只要记住几件事：**OpenClaw 能正常聊天**、**Python 先确认 ≥ 3.10**、**本机装好 PowerMem**、**装上插件**。不用手写 `powermem.env`，记忆用的模型和 Key 会跟你在 OpenClaw 里配的一样。

---

## 你要做什么（按顺序）

1. **确认 OpenClaw 已经能用**  
   终端执行 `openclaw --version`，并且你已经在 OpenClaw 里配好了**平时对话用的模型**（能正常回复即可）。

2. **先检查 Python 版本（必须 ≥ 3.10）**  
   在创建虚拟环境或执行 `pip install` **之前**必须先确认版本，否则后续容易装失败或运行异常：
   ```bash
   python3 --version
   ```
   输出应为 **Python 3.10.x、3.11.x、3.12.x** 等（次版本号 ≥ 10）。也可用下面命令做一次硬性校验（不通过会报错退出）：
   ```bash
   python3 -c "import sys; assert sys.version_info >= (3, 10), '需要 Python 3.10 或更高'; print(sys.version.split()[0], 'OK')"
   ```
   若版本不够：先升级本机 Python，或安装并使用 `python3.11` / `python3.12` 等满足要求的解释器，并将下面步骤里的 **`python3`** 换成实际命令（例如 `python3.12 -m venv ...`）。

3. **安装 PowerMem（Python）**  
   建议用虚拟环境，然后安装：
   ```bash
   python3 -m venv ~/.openclaw/powermem/.venv
   source ~/.openclaw/powermem/.venv/bin/activate
   pip install powermem
   ```
   装好后执行 `pmem --version`，能输出版本就行。

4. **让网关能找到 `pmem`**  
   如果你启动 `openclaw gateway` 的终端**没有**激活上面的 venv，有两种简单办法二选一：  
   - 每次开网关前先 `source ~/.openclaw/powermem/.venv/bin/activate`；或  
   - 在插件配置里把 **`pmemPath`** 写成 venv 里 `pmem` 的**完整路径**（装完后可用 `which pmem` 查看）。

5. **一键装插件（推荐）**  
   在 **Mac / Linux** 上执行（需已安装 OpenClaw）。脚本**不会**替你执行 `pip install powermem`；装插件前仍需完成上面的 PowerMem 与 `pmem` 步骤。
   ```bash
   curl -fsSL https://raw.githubusercontent.com/ob-labs/memory-powermem/main/install.sh | bash -s -y
   ```
   已克隆本仓库时，也可在仓库根目录执行：`bash install.sh`（可加 `-y`、或 `bash -s -- --workdir ~/.openclaw-second` 指定另一套 OpenClaw 数据目录）。  
   脚本会把插件放进 OpenClaw，并打开「用 OpenClaw 的模型驱动记忆」等默认选项；带 `-y` 时仍可能生成可选模板 **`~/.openclaw/powermem/powermem.env`**（默认路径下通常不必手改）。

6. **重启网关并检查**  
   ```bash
   openclaw gateway
   ```
   另开终端，**先**确认插件已被网关加载：
   ```bash
   openclaw plugins list
   ```
   输出里要有 **memory-powermem**，且其状态为 **loaded**（已加载）。若只有安装记录、状态不是 loaded，先按下面「若某一步失败」处理，**不要**跳过这步直接去测 `ltm`。  
   通过后再检查记忆健康并试写读：
   ```bash
   openclaw ltm health
   ```
   显示健康后试一句：
   ```bash
   openclaw ltm add "我喜欢喝美式"
   openclaw ltm search "咖啡"
   ```

---

## 若某一步失败

| 情况 | 怎么办 |
|------|--------|
| `python3 --version` 低于 3.10 | **先升级或换用** `python3.11` / `python3.12` 等，再重做「检查 Python」与 venv 步骤；不要跳过版本检查强行 `pip install`。 |
| `pip install powermem` 报错 | 再次确认 Python ≥ 3.10；换干净 venv 再试。 |
| `pmem` 找不到 | 激活 venv，或配置 **`pmemPath`** 为绝对路径。 |
| `plugins list` 没有 **memory-powermem**，或状态不是 **loaded** | 确认已执行安装脚本或 `openclaw plugins install`；`plugins.enabled` 为 true、`plugins.slots.memory` 为 **memory-powermem**；改完后**重启 gateway**，再执行 `openclaw plugins list` 复查。 |
| `ltm health` 不健康 | 确认 OpenClaw 里**默认模型**和 API Key 本身能聊天；升级 OpenClaw 到较新版本后再试。 |
| 想要更多选项（多实例、HTTP、`install.sh` 细节、工具与排错表等） | 使用 **`install-memory-powermem-full`** skill（含 **config-reference.md**），或查阅 **[memory-powermem](https://github.com/ob-labs/memory-powermem)** 的 **README**。 |

---

## 说明（一句话）

记忆数据默认存在本机 OpenClaw 数据目录下的 SQLite 里；**不需要**你再单独维护一份 PowerMem 的 `.env`，除非你熟悉进阶配置。
