# Pi v1.2.0 书稿验证

验证基准：`06a466935536f8c9bcacb2d5efc0fc021be1bdff`。要求 Node.js ≥ 22.19.0。此目录的三个直接 Pi 依赖及其发布的 Pi 依赖固定为 `1.2.0`，安装锁文件随书稿保存。

```sh
npm ci --ignore-scripts
node extract-examples.mjs
npm run typecheck
npm test
```

`examples/` 的九个文件从 MDX 中标为“完整示例”的代码块原样抽取。不要直接修改生成文件；修改网页书稿后重新抽取。模型调用示例只做类型检查，不运行真实供应商请求；第 7 章扩展另由模拟会话直接加载验证。

`scenarios.test.ts` 使用发布包的 `fauxProvider`，不调用付费模型。覆盖循环准备与收尾、显式继续/结束、保留队列、错误硬退出、整批 terminate、整批串行、并行事件与结果顺序、阻止工具、截断工具调用、系统消息归一化、上下文编辑与恢复，以及扩展续跑和真正收尾。持久化场景只在临时测试目录创建会话。

共 15 项场景；此次新增首条用户输入及分支落盘、显式工具失败与结构化结果脱敏、摘要省略 thinking、用户与自定义消息启动事件及 busy 状态四项回归测试。

源码与资源核查（在 `pi-agent/revisions/` 下）：

```sh
node verify-sources.mjs <固定发布提交的源码快照目录>
node generate-diagrams.mjs
```

`source-map.json` 记录逐章的文件、符号与已验证行号；脚本核查所有书中源码链接的提交、路径和索引行号，以及 25 对当前配图的一致性。行为核对另见 [逐章修订记录](../v1.2.0.md)，路径检查本身不能代替源码阅读。

网站检查（在 `pi-agent/web/` 下）：

```sh
npm run sync:ts
npm run check:sync
npm run check:counterpart
npm run test:counterpart
npm run build
npm run preview -- --host 127.0.0.1 --port 4321
# 另开一个终端，保持预览服务运行
npm run check:reading
```

阅读检查需要已安装 Playwright Chromium。脚本在旧 Python 偏好存在时访问全部十章 TS、十章历史 Python，并检查实战篇导航；重点章节 3、6、7、10 各保存桌面/手机 × 深/浅主题的截图，检查链接、SVG 文本边界、页面溢出和图片放大交互。输出在 `.artifacts/reading/`，不提交截图与测试产生的临时文件。

2026-10-13 的升级验证通过类型检查、15 项离线场景、同步与配对检查，以及 Pages 子路径构建。当前环境缺少 Chromium，安装下载失败，因此此次未完成浏览器截图、文字边界及交互检查；可在有 Chromium 的环境运行上述 `check:reading` 补验。

本次没有生成 PDF。main 分支的站点修改由现有 GitHub Pages 工作流发布。
