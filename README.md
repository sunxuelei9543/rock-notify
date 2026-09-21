# 🏪 洛克王国远行商人商品推送

[![商品推送](https://github.com/zxc135781/rock-notify/actions/workflows/notify.yml/badge.svg)](https://github.com/zxc135781/rock-notify/actions/workflows/notify.yml)

自动抓取 [洛克王国：世界远行商人查询器](https://www.onebiji.com/hykb_tools/comm/lkwgmerchant/preview.php?id=1&immgj=0) 的商品信息，并通过企业微信/飞书机器人推送通知。

## ⏰ 运行时间

项目由 [cron-job.org](https://cron-job.org/) 定时触发。以下示例使用北京时间（`Asia/Shanghai`），每日运行 4 次：

| 时间（北京） |
|------------|
| 08:01      |
| 12:01      |
| 16:01      |
| 20:01      |

每次运行会抓取对应时间段的商人商品并推送。

## 🚀 配置步骤

### 1. Fork 或创建仓库

将本项目推送到你的 GitHub 仓库。

### 2. 配置推送渠道

至少配置以下其中一个渠道（可同时配置两个）：

#### 企业微信（可选）

1. 在企业微信群中添加一个**群机器人**
2. 复制机器人的 Webhook URL
3. 在 GitHub 仓库中：**Settings** > **Secrets and variables** > **Actions**
4. 点击 **New repository secret**
5. 名称填写 `WECOM_WEBHOOK_URL`，值填写你的 Webhook URL

#### 飞书（可选）

1. 在飞书群中添加一个**自定义机器人**
2. 复制机器人的 Webhook URL
3. 在 GitHub 仓库的 Secrets 中添加 `FEISHU_WEBHOOK_URL`

### 3. 配置 cron-job.org 定时触发

GitHub Actions 的 `schedule` 可能延迟或漏触发，因此本项目使用 cron-job.org 通过 `repository_dispatch` 触发工作流。

1. 在 GitHub **Settings** > **Developer settings** > **Personal access tokens** > **Fine-grained tokens** 创建令牌。
2. 资源所有者选择你的账号；仓库访问选择**仅选择存储库**，并选择本仓库。
3. 在 **Repository permissions** 中将 **Contents** 设为 **Read and write**，生成后复制令牌原文。
4. 登录 [cron-job.org](https://cron-job.org/)，创建定时任务，时区选择 `Asia/Shanghai`。
5. 在 cURL 导入框粘贴以下命令，并将 `你的GitHub令牌` 替换为上一步生成的令牌：

```bash
curl --request POST --url https://api.github.com/repos/你的GitHub用户名/你的仓库名/dispatches --header "Accept: application/vnd.github+json" --header "Authorization: Bearer 你的GitHub令牌" --header "X-GitHub-Api-Version: 2022-11-28" --header "Content-Type: application/json" --data "{\"event_type\":\"trigger-notify\"}"
```

6. 将执行计划设为“自定义”，Crontab 表达式填写：

```cron
1 8,12,16,20 * * *
```

这表示每天北京时间 08:01、12:01、16:01、20:01 触发。保存后使用 cron-job.org 的立即执行功能测试，GitHub Actions 中应显示 `repository_dispatch` 类型的运行记录。

### 4. 启用 GitHub Actions

确保仓库的 Actions 功能已启用。cron-job.org 负责触发，GitHub Actions 负责运行爬虫和推送消息。

### 飞书 @所有人

当商品名称包含以下任一关键词时，飞书卡片会自动 `@所有人`：`国王球`、`祝福项坠`、`棱镜球`、`炫彩蛋`、`首领血脉秘药`。请确认飞书群机器人具备 `@所有人` 权限。

## 🧪 本地测试

```bash
# 安装依赖
pip install -r requirements.txt

# 设置环境变量（至少配置一个）
export WECOM_WEBHOOK_URL="https://qyapi.weixin.qq.com/cgi-bin/webhook/send?key=YOUR_KEY"
export FEISHU_WEBHOOK_URL="https://open.feishu.cn/open-apis/bot/v2/hook/YOUR_KEY"

# 运行
python scraper.py
```

## 📁 项目结构

```
rock-notify/
├── scraper.py                 # 爬虫脚本
├── requirements.txt           # Python 依赖
├── .github/workflows/
│   └── notify.yml             # GitHub Actions 任务（由 cron-job.org 触发）
└── README.md                  # 本文件
```
