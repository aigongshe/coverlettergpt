在本地运行

在克隆此存储库后，您可以按照以下步骤在本地运行它：

1. 在终端中运行 curl -sSL https://get.wasp-lang.dev/installer.sh | sh来安装Wasp
2. 在项目的根目录中创建 .env.server文件
3. 复制 env.server.example 文件内容到 .env.server ，并填写您的API密钥
4. 确保您有一个连接和运行的数据库。这里有两个快速的选择：

• 从项目根目录运行 wasp start db 。您需要安装 Docker（如果没有，在 MacOS 上运行 brew install docker-machine docker 并启动 Docker 应用程序）。这将启动一个 Postgres 数据库并为您配置它。不需要做任何其他事情！
• 或者在Railway上提供一个 Postgres 数据库，进入设置并复制连接 url。将其粘贴为 DATABASE_URL= 输入到您的 env.server 文件。

1. 运行 wasp db migrate-dev
2. 运行 wasp start
3. 在您的浏览器中转到 localhost:3000（您的 NodeJS 服务器将在端口 3001上运行）
4. 安装Wasp 的 VSCode 扩展以获得最佳的 DX

工作原理

coverlettergpt.xyz使用了几个非常酷的工具在几天内构建：

• 🐝 Wasp - 允许你用减少10倍的样板代码构建全栈应用
• 🎨 Chakra-ui - 对于 React 来说易于使用且外观良好的 UI 组件
• 🤖 OpenAI - GPT-3.5 游戏和 GPT-4 API
• 💸 Stripe - 用于付款
• ⚡️ Lightning / Bolt11 - 用于比特币闪电支付

Wasp作为全栈框架，允许你在根目录的 main.wasp 配置文件中描述你的应用的核心功能。然后，它将这些功能构建并粘贴到一个 React-Express-Prisma 应用中，这样你就可以集中精力编写客户端和服务器端逻辑，而不是配置。例如，我不必使用任何第三方库来进行谷歌认证。我只在配置文件中写了几行代码，说明我想要使用谷歌验证，Wasp 为我配置了。查看 main.wasp 文件以获取更多信息。

此外，Chakra-ui非常适合快速轻松地构建漂亮的 UI。有些人对它们是 React 组件感到失望，但我发现它们易于定制和配置，并且比 tailwind 更快地开始设计并使用更少的代码。

有关我为OpenAI API使用的提示和配置的更多信息，请查看 src/server/actions.ts 文件。

Stripe使付款功能超级容易。我配置了两个订阅产品，一个是 GPT-3.5 turbo，另一个是 GPT-4。用户支付后，我更新数据库中的 hasPaid 和 datePaid 字段。

Lightning / Bolt11是一个处理比特币闪电支付的伟大库。我用它为用户生成了一个闪电发票。用户支付后，我更新数据库中的 LnPayment.status 字段(参见 src/server/ln.ts)，允许用户在前端执行一次生成。我个人接受支付到我的Alby闪电地址。

我还实现了一个 cron 任务，用于在用户订阅结束前两周发送电子邮件通知他们。我使用SendGrid作为电子邮件服务。

如果您有任何其他问题，请随时在twitter上联系我。
