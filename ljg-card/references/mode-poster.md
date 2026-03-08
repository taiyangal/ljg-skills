# 模具：多卡（-c）

## 步骤 1：读取模板

Read `~/.claude/skills/ljg-card/assets/poster_template.html`

## 步骤 2：内容预处理

- 识别标题行（`#`/`##`/`###` 开头，或独立短行）
- 识别引用块（`>` 开头）
- 识别加粗（`**text**`）
- **识别金句**：独立成段的短句（通常 < 25 字），承载核心洞察，用 `.highlight` 渲染
- 按空行分割为段落列表

## 步骤 3：计算视觉重量

模板在 1080x1440 全分辨率渲染，正文 36px，行高 1.7。

- 普通段落：字符数 × 1.4
- 标题行（h1 首卡 84px）：字符数 × 6.0
- 金句（`.highlight` 40px + 左边框 + 上下留白）：字符数 × 3.0
- `.item` 条目组（label + 正文）：字符数 × 1.8
- 引用块：字符数 × 1.7
- 分割线（divider）：固定 60 权重
- 代码块：字符数 × 2.2
- Running title（续页头部）：固定 70 权重

## 步骤 4：贪心切分

- 阈值：每卡约 **380** 字符等价视觉重量
- 逐段累加，超过阈值时在当前段之前切分
- **切分规则**：
  - 绝不在句子中间切
  - 优先在段落/条目/章节边界切
  - 标题不落单（必须跟至少一个内容元素在同一卡）
  - 超长单段在句号处强制切
  - 一个章节（h2 + 3 items）通常刚好一卡

**特殊情况**：
- 只有一张卡：不显示页码
- 多张卡：显示 `1 / N` 格式页码

## 步骤 5：格式化为 HTML

**基础元素：**
- 普通段落 → `<p>文本</p>`
- 章节标题（##/### 级别） → `<h2>标题</h2>`
- 引用 → `<blockquote><p>引用</p></blockquote>`
- 加粗 → `<strong>文本</strong>`
- 列表 → `<ul><li>...</li></ul>`

**金句（独立成段的核心洞察短句，视觉突出）：**
```html
<p class="highlight">金句文本</p>
```
判断标准：独立成段、< 25 字、承载关键洞察。用 `.highlight` 而非 `<p><strong>`。

**条目组（有标题+正文的并列条目）：**
```html
<div class="item">
  <p class="label">条目标题</p>
  <p>条目正文</p>
</div>
```

**副标题标签：**
```html
<p class="subtitle">标签文字</p>
```

**分割线（章节之间）：**
```html
<div class="divider"></div>
```

## 步骤 6：渲染模板

对每张卡片，替换模板变量：

| 变量 | 规则 |
|------|------|
| `{{HEADER_BLOCK}}` | 续页卡：`<div class="header"><span class="running-title">文章标题</span></div>`；首卡或单卡：空字符串 |
| `{{TITLE_BLOCK}}` | 首卡有标题时：`<div class="title-area"><h1>标题</h1></div>`；续页卡或无标题时：空字符串 |
| `{{BODY_HTML}}` | 步骤 5 生成的 HTML |
| `{{SOURCE}}` | 来源/作者信息（用户提供则填入，否则 `李继刚`） |
| `{{PAGE_INFO}}` | 多卡时 `1 / 3`，单卡时空字符串 |

写入：`/tmp/ljg_cast_poster_{name}_{N}.html`

## 步骤 7：截图

```bash
node ~/.claude/skills/ljg-card/assets/capture.js /tmp/ljg_cast_poster_{name}_{N}.html ~/Downloads/{name}_{N}.png 1080 1440
```

多张卡片可并行截图。

交付时报告卡片数量 + 每张摘要（前 30 字）。
