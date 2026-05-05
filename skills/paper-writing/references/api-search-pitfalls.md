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

## CrossRef API — PRIMARY Retrieval Engine ⭐

**当 Semantic Scholar 持续返回 0 或 HTTP 429 时，CrossRef 应是你的 FIRST CHOICE，而非备选。** CrossRef 没有实际限速（礼貌性 0.5s 间隔即可），对复杂多词英文查询容忍度高，返回结构化 JSON 含完整元数据（title, authors, year, journal, DOI, abstract, subjects, is-referenced-by-count）。

### 大规模检索模式（适用于文献计量 / systematic review 类论文）

```python
import urllib.request, urllib.parse, json, time

def search_crossref(query, rows=100, offset=0):
    params = {
        'query': query,
        'rows': str(rows),
        'offset': str(offset),
        'filter': 'type:journal-article',   # 仅期刊论文
        'sort': 'relevance'
    }
    url = "https://api.crossref.org/works?" + urllib.parse.urlencode(params)
    req = urllib.request.Request(url, headers={'User-Agent': 'MyApp/1.0 (mailto:research@example.com)'})
    with urllib.request.urlopen(req, timeout=25) as resp:
        data = json.loads(resp.read())
    return data['message']['items'], data['message']['total-results']
```

**去重**：用 DOI 作为唯一键（`seen_dois` set），因为同一论文可能被多个查询命中。

**分页**：CrossRef 通常允许 offset 最大到约 1000，通过 `for offset in [0, 100, 200, ...]` 遍历。

**年份过滤**：CrossRef 返回的年份取自 `published-print['date-parts'][0][0]` 或 fallback `created['date-parts'][0][0]`——必须在 Python 侧过滤（`filter` 参数不支持年份范围直接过滤）。

**Abstract 截断**：将 abstract 截断至 400-500 字符以减小 JSON 体积（完整 abstract 在 paper 正文中不需要）。

### 用途：验证已知 DOI
```
curl "https://api.crossref.org/works/10.xxx/xxx"
```
返回 title, authors, journal, year 等元数据——用于交叉验证其他来源检索到的论文是否真实存在。

## 文献验证策略

1. Semantic Scholar 检索 → 拿到 paperId 和疑似 DOI
2. CrossRef DOI 验证 → 确认论文真实存在
3. 浏览器访问 DOI 落地页 → 确认期刊/卷期/页码
4. 三者交叉验证通过 → 纳入参考文献列表
