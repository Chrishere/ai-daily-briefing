# 每日 AI 资讯生成任务

今天是 {{TODAY}}。你的任务是更新当前工作目录下的 `index.html`——一个名为"AI 每日资讯"的静态页面。

## 输出操作（关键、一次性指令）

1. 用 Read 读取 `index.html` 的完整内容。
2. **收集去重 URL**：把当前文件里所有 `<article class="card">` 里 `<div class="source"><a href="...">` 的 URL 都列出来。这些 URL 今天**绝对不能再选**——即便发生了新的进展，也要换一篇不同 URL 的文章。
3. 按下面"来源铁律 + 选条标准"挑 5-8 条**全新**的资讯。
4. 用 Edit / Write 替换 `index.html` 里 `<div id="items">…</div>` 内的 `<article class="card">` 列表；保留 `<style>`、`<header>`、`<script>`、`<div class="footer-note">` 等其他所有 HTML。
5. 把 `<span id="count">N 条精选</span>` 改成今天实际条数。
6. 完成后**不要做任何 git 操作**——CI 会自己处理 commit & push。

## 来源铁律

**只用这两类：**

1. **AI 公司一手**（官方博客、工程文章、研究公告）：
   OpenAI（openai.com/news, openai.com/research）、Anthropic（anthropic.com/news, /engineering, /research）、Google DeepMind（deepmind.google/discover/blog）、Meta AI（ai.meta.com/blog）、xAI、Mistral、NVIDIA、Microsoft AI、阿里 Qwen（qwenlm.github.io）、字节 Seed（seed.bytedance.com）、智谱、DeepSeek、Hugging Face（huggingface.co/blog）

2. **深度博主**（必须有原作者署名 + 长文/播客原文链接）：
   - 技术向：Simon Willison（simonwillison.net）、Andrej Karpathy（karpathy.bearblog.dev）、Sebastian Raschka（magazine.sebastianraschka.com）、Lilian Weng（lilianweng.github.io）、Chip Huyen（huyenchip.com）、Jay Alammar、Eugene Yan（eugeneyan.com）
   - 产品/战略向：Stratechery（付费墙只引免费摘要）、Interconnects（interconnects.ai，Nathan Lambert）、Latent Space（latent.space，swyx）、Import AI（jack-clark.net）、One Useful Thing（oneusefulthing.org，Ethan Mollick）、The Pragmatic Engineer、AI Snake Oil
   - 研究/数据：Epoch AI（epochai.org）、The Gradient（thegradient.pub）
   - 中文圈：宝玉（X @dotey）、黄建同学、晚点 LatePost（深度专题）、乱翻书、硅星人（仅深度专题，不是新闻短稿）

**❌ 严格禁止用的来源**（即便它们报道了相关消息也不能用作 source）：TechCrunch、VentureBeat、The Verge、Wired、Ars Technica、CNBC、Reuters、Bloomberg、WSJ、AdExchanger、Digiday、AdAge、Adweek、eMarketer、Marketing Brew、Search Engine Land、Martech.org、机器之心、量子位、新智元

## 选条标准

- 优先 24-72 小时内发布的新内容；**战略级**（Anthropic / OpenAI / DeepMind 重大公告，或核心博主的长文）可放宽到 14 天
- 必须有真实新内容（不是评论 N 个月前的事件）
- 同一事件最多保留一条（优先选博主的深度拆解，其次选 AI 公司官方）
- 如果某天深度源里没有 5 条够份量的内容，宁可只放 3-4 条好的，**绝对不要为了凑数而引用泛泛媒体**

## 工作流程

1. 用 Bash 跑 `date "+%Y-%m-%d %A"` 拿到当天日期（确认）。
2. 用 WebSearch 跑 6-10 个查询，**直接 site: 限定到上面的域名**：
   - `site:simonwillison.net [本月]`、`site:interconnects.ai [本月]`、`site:karpathy.bearblog.dev`、`site:stratechery.com [本月]`、`site:latent.space [本月]`、`site:anthropic.com news [本月]`、`site:openai.com news [本月]`、`site:deepmind.google blog [本月]`、`Lilian Weng [本月]`、`Sebastian Raschka [本月]`
   - **不要做开放性搜索**（如 "AI news today"），那会把泛泛媒体搜出来
3. 对挑中的每篇文章用 WebFetch 抓原文。**英文摘录必须是原文里的实际段落直接引用**，不能从搜索摘要里拼凑。
4. 为每条准备：
   - 中文标题（≤30 字）
   - 来源链接（指向具体那篇博文/官方文章 URL，不是首页）
   - 中文总结（2-4 句，"作者讲了什么 + 关键观点/数据"，不是机械直译标题）
   - 英文原文摘录（30-100 词，**必须是原文直接引用**）
   - 中文翻译（对上述英文段落的忠实翻译）
5. tag 分类：
   - `<span class="tag ai">AI · 公司名</span>` — AI 公司一手
   - `<span class="tag blog">博主 · 博主名</span>` — 深度博主

## 每条 article 的 HTML 模板

```html
<article class="card">
  <span class="tag blog">博主 · 博主名</span>   <!-- 或 tag ai / AI · 公司名 -->
  <h2 class="title">中文标题</h2>
  <div class="source">来源：<a href="原文具体 URL" target="_blank" rel="noopener">域名 · 文章名（日期）</a></div>
  <div class="summary"><b>总结：</b>2-4 句中文总结</div>
  <details>
    <summary>查看英文原文摘录</summary>
    <div class="body en">English direct excerpt from the original article, 30-100 words.</div>
  </details>
  <details>
    <summary>查看中文翻译</summary>
    <div class="body">英文段落的忠实中文翻译</div>
  </details>
</article>
```

## 质量约束（再次强调）

- **绝对去重**：今天的 5-8 条 URL 必须**全部不在**步骤 2 收集的现有 URL 列表里
- 英文摘录必须是原文直接引用，不能是改写。拿不到原文就放弃那条
- 来源链接必须指向具体那篇博文/官方文章，不是网站首页或聚合页
- HTML 引号正确转义

完成 Write/Edit `index.html` 即可，**不要 git commit 或 push**。
