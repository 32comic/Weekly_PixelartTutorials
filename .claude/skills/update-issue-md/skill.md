---
name: update-issue-md
description: 自动完善 Weekly_PixelartTutorials 项目的 issue md 文件，抓取微信文章标题和内容，格式化后提交并推送到 GitHub
version: "1.0"
triggers:
  - "完善 issue"
  - "处理 issue"
  - "更新 issue"
  - "issue md"
  - "issue-"
---

# Update Issue MD Skill

当用户要求处理、完善或更新 `doc/issue-XXX.md` 文件时，自动执行以下完整流程。

## 前置检查

1. 确认当前工作目录是 Weekly_PixelartTutorials 项目根目录
2. 确认目标 `doc/issue-XXX.md` 文件存在
3. 读取文件内容，提取其中的微信公众号文章链接（`https://mp.weixin.qq.com/s/...`）

## 执行流程

### 1. 读取参考格式
读取最近的 1-2 个已完善的 issue 文件（如 `doc/issue-363.md`）作为格式参考，确认标准格式：
- `(N) 文章标题`
- `[url](url)` markdown 链接
- 一行简介（说明核心内容和适合谁看）
- `## 鸡汤摘录`

### 2. 抓取微信文章信息
微信公众号有反爬验证，必须使用 **Chrome debug 模式 + CDP Proxy** 获取内容：

```bash
# 启动 Chrome debug 模式（指定独立 profile，避免干扰日常浏览器）
nohup /Applications/Google\ Chrome.app/Contents/MacOS/Google\ Chrome --remote-debugging-port=9222 --user-data-dir=/tmp/chrome-debug-profile --no-first-run --no-default-browser-check >/dev/null 2>&1 &

# 等待 Chrome 启动后，启动 CDP Proxy
node ~/.claude/skills/web-access/scripts/cdp-proxy.mjs >/tmp/cdp-proxy.log 2>&1 &

# 检查 proxy 健康状态
curl -s http://127.0.0.1:3456/health
```

对每个微信文章链接执行：
1. `GET /new?url=<文章URL>` — 创建新 tab 并导航
2. 等待 3 秒加载
3. `GET /info?target=<targetId>` — 获取文章标题
4. `POST /eval` — 执行 JS 获取正文内容摘要（`document.querySelector("#js_content")?.innerText?.slice(0, 300)`）

### 3. 完善 issue md

根据抓取到的标题和内容，按标准格式重写 issue md：

```markdown
摘要：教你画像素画每周分享
关键词：教你画，像素画，像素图，马赛克画，8bit，欣赏，素材，学习参考，本周教程

## 本周教程

(N) 文章标题

[url](url)
一句话简介（说明核心内容 + 适合谁看）。

...

## 鸡汤摘录

原文内容
```

### 4. 更新 README 索引
在 `README.md` 对应月份的列表中添加新条目：
```markdown
- 第XXX期：[鸡汤标题](doc/issue-XXX.md)
```

### 5. 提交并推送

```bash
git add README.md doc/issue-XXX.md
git commit -m "docs: add issue XXX and <月份> <年份> README entry

Co-Authored-By: Claude Sonnet 4.6 <noreply@anthropic.com>"
git push origin master
```

### 6. 清理

```bash
kill <chrome_pid> <proxy_pid> 2>/dev/null
rm -rf /tmp/chrome-debug-profile /tmp/cdp-proxy.log 2>/dev/null
```

## 注意事项

- 微信文章标题可能包含引号，写入 markdown 时保持原样
- 简介要从正文提炼，不要凭空编造
- 如果某篇文章内容无法获取（如验证失败），保留原始链接，标题写"待补充"
- 鸡汤摘录保留原文，只做最小化格式整理（如统一大小写）
- commit message 风格参考近期历史：`docs: add issue XXX and <月份> <年份> README entry`
