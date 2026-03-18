# OpenAI 自动注册（免 GPTMail API）

## 功能亮点
- 内置 GPTMail 浏览器模拟，无需 GPTMAIL_API_KEY，自动收码。
- 支持 CPA 自动上号（上传 token）与失效账号清理（探测 401/用量超阈值后删除）。
- 账号密码、token 自动落盘：`openai-register/tokens/`。

## 环境准备
```bash
cd openai-register
python3 -m venv venv
source venv/bin/activate          # Windows 用 venv\Scripts\activate
pip install curl_cffi requests aiohttp   # 若不做 CPA 清理，可不装 aiohttp
```

## 运行示例
```bash
cd openai-register
source venv/bin/activate
# 可选代理： --proxy http://127.0.0.1:7890
python openai_register.py --once
```

启用 CPA 上传 + 清理示例（需准备管理端）
```bash
cd openai-register
source venv/bin/activate
CPA_BASE_URL=https://your-cpa.example CPA_TOKEN=Bearer_xxx \
python openai_register.py --once --cpa-upload --cpa-clean \
  --cpa-workers 20 --cpa-timeout 12 --cpa-retries 1 --cpa-used-threshold 95
```

## 参数说明
- `--proxy`：可选，HTTP/S 代理地址。
- `--once`：只跑一轮；不加则循环运行。
- `--sleep-min` / `--sleep-max`：循环模式下两轮之间的随机等待秒数。
- `--cpa-base-url` / `--cpa-token`：CPA 管理地址与 Bearer token（可用环境变量 CPA_BASE_URL、CPA_TOKEN 覆盖）。
- `--cpa-workers` / `--cpa-timeout` / `--cpa-retries` / `--cpa-used-threshold`：CPA 清理探测并发、超时、重试、用量判定阈值（默认 95）。
- `--cpa-upload`：注册成功后把 token 文件上传到 CPA。
- `--cpa-clean`：注册成功后探测并删除 CPA 中失效/用量超阈值的账号（需要安装 aiohttp）。

## 输出位置
- 账号密码：`tokens/accounts.txt`（email----password）。
- Token JSON：`tokens/token_<email>_<timestamp>.json`。

## 注意
- 需能访问 https://auth.openai.com；代理地区避开 CN/HK。
- 验证码超时可换代理或重试；workspace 为空多重试几次。
