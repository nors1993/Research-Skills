# API 文献检索坑点与应对

## Semantic Scholar API

### 坑点 1：URL 编码——`+` vs `%20`
- ❌ `terminal` 中 curl 使用 `query=word1+word2` → 返回 0 结果（shell 可能将 `+` 解释为空格或截断）
- ✅ `terminal` 中 curl 使用 `query=word1%20word2` → 正常返回
- ✅ Python `urllib.parse.urlencode()` 在 execute_code 中直接用 `urllib.request`（不用 terminal 中转）→ 避免 shell 二次处理 `&`

### 坑点 2：HTTP 429 限速
- Semantic Scholar 免费 API 限速为 1 req/s
- 每两次请求间隔 ≥ 1.5 秒
- 被限速后等待 10-30 秒再重试

### 坑点 3：多词查询返回空
- 部分多词组合（尤其含"industry""market""trend"等通用词）返回 0 结果
- 解决：尝试拆分为更短的关键词组合，或用单一核心词分散搜索后人工筛选
- 测试连通性：先用单关键词 `query=space` 确认 API 正常

## arXiv API

### 坑点 1：HTTP 429 和 Timeout
- 限速约 1 req / 3 秒
- 大规模并行请求容易触发 429 或连接超时
- 内置 `search_arxiv.py` 脚本有 15s timeout，网络波动时可能不够

### 坑点 2：检索范围
- arXiv 以 STEM 预印本为主，产业经济/政策类论文较少
- 适合查找卫星通信、轨道力学、遥感算法等技术论文
- 不适合查找产业经济学、空间政策类文献

## Google Scholar

### 坑点：数据中心 IP 封禁
- 从云服务器 IP 访问 Google Scholar 几乎必然被 CAPTCHA 拦截
- **不要作为首选检索渠道**
- 备选：通过 Semantic Scholar、CrossRef、直接 DOI 验证

## CrossRef API

### 用途 1：作为 PRIMARY 检索工具（当 Semantic Scholar 大面积失败时）

当 Semantic Scholar 持续返回 0 结果时，CrossRef 的 `query.title` 端点可作为主检索手段：

```python
params = urllib.parse.urlencode({'query.title': 'paper title here', 'rows': '2'})
url = f"https://api.crossref.org/works?{params}"
```

优点：限速宽松（无需刻意等 1.5s），返回结构化 JSON，对多词英文查询容忍度高。
缺点：中文论文覆盖率较低，部分会议论文可能未收录。

### 用途 2：验证已知 DOI 的真实性
```
curl "https://api.crossref.org/works/10.xxx/xxx"
```
返回 title, authors, journal, year 等元数据——用于交叉验证 Semantic Scholar 检索到的论文是否真实存在。

## 文献验证策略

1. Semantic Scholar 检索 → 拿到 paperId 和疑似 DOI
2. CrossRef DOI 验证 → 确认论文真实存在
3. 浏览器访问 DOI 落地页 → 确认期刊/卷期/页码
4. 三者交叉验证通过 → 纳入参考文献列表
