# 建站助手（云指 SAAS 建站 Skill）

用于云指（72e）SAAS 建站系统的页面创建、修改与排障技能。当用户需要新建网站、从零规划站点、搬站/仿站、生成或修改企业网站页面（自由页 HTML/CSS/JS、Twig 风格模板）、调用产品/文章/下载/相册/门店/栏目/FAQ 数据、做自定义表单/产品询盘/在线查询、上传或修改产品/文章数据等时均可使用。

技能通过用户指定站点的 MCP 服务查询与写入站点内容；与 WorkBuddy、Codex 等智能体协同使用时，可实现从需求到页面的端到端自动交付，建站流程大幅简化。

## 功能特性

- **页面生成**：静态区块、自由页、动态列表页、详情页，输出可直接粘贴到建站编辑器的完整 HTML/CSS/JS 代码
- **模板语法**：基于 Twig 1.3 的 AIPage 模板，支持系统数据源 `DataTag.sys` / 自定义数据源 `DataTag.custom`
- **动态数据**：通过 `FnRewrite` / `FnGetHost` / `FnGet*` 系列函数获取菜单、产品、文章、下载、相册、门店、栏目、自定义表单、在线查询、SQLite、FAQ 等站点数据
- **表单能力**：自定义表单、产品询盘、在线查询（沿用系统动态表单，保留校验与提交逻辑）
- **内容管理**：文章/产品的查询、新增、修改与批量导入，图片/文件上传
- **整站规划**：新建网站的信息架构与常见页面（首页/关于我们/产品中心/新闻资讯/联系我们等）
- **搬站/仿站**：仅处理用户有权使用的公开内容，将源站页面/产品/文章/素材迁移至目标站点
- **SQLite 扩展**：为系统未覆盖的功能提供轻量数据存储（仅当前站点，< 5000 行）
- **SEO**：按页面类型输出 JSON-LD 结构化数据，详情页 TDK 规范

## 快速开始

1. **领取试用站点**：到云指官网注册会员并领取试用站点：<https://www.72e.net/autoweb/edition.aspx>
2. **获取凭证**：向站点索取「MCP 完整地址」+「Bearer Token」（二者缺一不可，未拿到前不调用任何 MCP 工具）
3. **验证连通**：先调用 `test` 验证凭证有效性
4. **开始使用**：直接对智能体说出需求，例如「帮我建个企业官网」「做一个产品询盘页」「把 A 站的产品搬到我的站点」

> 切换站点时需重新索取地址和 Token，并重新 `test`。Token 仅由 MCP 客户端放入 `Authorization: Bearer <token>` 请求头，不会写入任何文件或日志。

## 标准工作流

1. 明确交付物：静态区块 / 自由页 / 动态列表页 / 详情页 / 自定义表单 / 产品询盘 / 在线查询 / 新建整站
2. 判断是否需要在线站点：需要则索取 MCP 凭证并 `test` 验证；不需要则离线生成代码
3. 新建整站先确认：企业名称、产品/服务、核心受众、网站语言、参考网站
4. 优先用 MCP 查询工具拿真实 ID（`list_page`、`get_product_list`、`list_news_class` 等），修改页面必须先 `get_page_info` 读取源码
5. 选最小合适的数据能力：公司信息用 `DataTag.sys`，后台业务数据用 `DataTag.custom`，系统内容用 `FnGet*` 函数
6. 输出完整可粘贴代码，保存前用 `list_page` 判断新建还是更新，由用户决定
7. 收尾时主动询问是否上线、是否设为首页

## 参考文档

| 文档 | 内容 |
| --- | --- |
| [`references/mcp-tools.md`](references/mcp-tools.md) | MCP 工具的参数、返回值、限制与推荐调用顺序 |
| [`references/AIPage.md`](references/AIPage.md) | Twig 1.3 模板语法、系统/自定义数据源 |
| [`references/advanced-data-interface.md`](references/advanced-data-interface.md) | `FnRewrite` / `FnGetHost` / `FnGet*` 系列动态数据接口 |
| [`references/customform-democode-base.md`](references/customform-democode-base.md) | 自定义表单基础代码（含不可删的校验/提交 JS） |
| [`references/product-enquiry.md`](references/product-enquiry.md) | 产品询盘规则 |
| [`references/product-enquiry-democode.md`](references/product-enquiry-democode.md) | 产品询盘示例代码 |
| [`references/online_query_democode.html`](references/online_query_democode.html) | 在线查询示例代码 |
| [`references/website-common-pages.md`](references/website-common-pages.md) | 常见页面组成、信息架构与页面编写规范 |
| [`references/FAQ.md`](references/FAQ.md) | 常见问题排查（MCP、Twig、动态数据、表单、搬站等） |

## 重要约束（摘要）

完整强制规则见 [`SKILL.md`](SKILL.md)，要点如下：

- **编码**：所有页面与数据使用 UTF-8
- **URL 动态化**：拼接完整 URL 时用 `{{ FnGetHost($type) }}`，不写死域名；本站链接保持相对链接
- **模板约束**：控制语句只能放在完整 HTML 元素外部，禁止写入标签属性或 `<title>` 之间；语法遵循 Twig 1.3，`|default` 最多一层
- **写操作确认**：保存/覆盖/删除/导入/上传/清缓存等写操作，调用前必须向用户展示目标与影响并取得确认
- **删除页面不可恢复**：必须核对页面 ID、名称、URL 并取得明确的永久删除确认
- **结构化数据**：JSON-LD 数据必须来自真实渲染内容，不得编造价格/评分/库存/日期
- **资源地址**：引用本站图片/CSS/JS 尽量使用相对 URL
- **搬站合规**：仅采集用户有权使用的公开内容，不采集登录后内容、用户数据、支付流程、第三方密钥

## 目录结构

```
.
├── SKILL.md          # 技能主文件：强制规则与工作流
├── README.md         # 本文件
└── references/       # 按需加载的参考文档
    ├── mcp-tools.md
    ├── AIPage.md
    ├── advanced-data-interface.md
    ├── customform-democode-base.md
    ├── product-enquiry.md
    ├── product-enquiry-democode.md
    ├── online_query_democode.html
    ├── website-common-pages.md
    └── FAQ.md
```
