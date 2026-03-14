# bb-sites

[bb-browser](https://github.com/epiral/bb-browser) 的社区网站适配器 — 把网站变成 CLI 命令。

每个适配器是一个 JS 函数，通过 `bb-browser eval` 在你的浏览器里运行。浏览器已经登录了 — 不需要 API key，不需要偷 Cookie，不需要反爬。

[English](README.md) · [中文](README.zh-CN.md)

## 快速开始

```bash
bb-browser site update                     # 安装/更新适配器
bb-browser site list                       # 列出所有命令
bb-browser site reddit/me                  # 运行命令
bb-browser site reddit/thread <url>        # 带参数运行
```

## 支持的网站（50+ 适配器）

### Reddit
| 命令 | 参数 | 说明 |
|------|------|------|
| `reddit/me` | — | 当前登录用户信息 |
| `reddit/posts` | `username`（可选） | 用户发帖列表 |
| `reddit/thread` | `url` | 帖子完整讨论树 |
| `reddit/context` | `url` | 评论的 ancestor chain |

### Twitter/X
| 命令 | 参数 | 说明 |
|------|------|------|
| `twitter/user` | `screen_name` | 用户资料 |
| `twitter/thread` | `tweet_id` | 推文 + 所有回复 |

### GitHub
| 命令 | 参数 | 说明 |
|------|------|------|
| `github/me` | — | 当前登录用户信息 |
| `github/repo` | `repo`（owner/repo） | 仓库信息 |
| `github/issues` | `repo`, `state`（可选） | Issue 列表 |
| `github/issue-create` | `repo`, `title`, `body`（可选） | 创建 Issue |
| `github/pr-create` | `repo`, `title`, `head`, `base`（可选） | 创建 Pull Request |
| `github/fork` | `repo` | Fork 仓库 |

### Hacker News
| 命令 | 参数 | 说明 |
|------|------|------|
| `hackernews/top` | `count`（可选） | 热门文章 |
| `hackernews/thread` | `id` | 帖子 + 评论树 |

### 知乎
| 命令 | 参数 | 说明 |
|------|------|------|
| `zhihu/me` | — | 当前登录用户信息 |
| `zhihu/hot` | — | 热榜 |
| `zhihu/question` | `id` | 问题详情 + 高赞回答 |
| `zhihu/search` | `keyword` | 搜索 |

### B站
| 命令 | 参数 | 说明 |
|------|------|------|
| `bilibili/me` | — | 当前登录用户信息 |
| `bilibili/popular` | `page`（可选） | 热门视频 |
| `bilibili/ranking` | `tid`（可选） | 排行榜 |
| `bilibili/search` | `keyword`, `sort`（可选） | 搜索视频 |
| `bilibili/video` | `bvid` | 视频详情 |
| `bilibili/comments` | `bvid`, `sort`（可选） | 视频评论 |
| `bilibili/feed` | — | 关注动态 |
| `bilibili/history` | — | 观看历史 |
| `bilibili/trending` | — | 热搜关键词 |

### 微博
| 命令 | 参数 | 说明 |
|------|------|------|
| `weibo/me` | — | 当前登录用户信息 |
| `weibo/hot` | — | 热搜榜 |
| `weibo/feed` | — | 首页时间线 |
| `weibo/user` | `id`（uid 或昵称） | 用户资料 |
| `weibo/user_posts` | `uid`, `page`（可选） | 用户微博列表 |
| `weibo/post` | `id` | 单条微博详情 |
| `weibo/comments` | `id`, `page`（可选） | 微博评论 |

### 豆瓣
| 命令 | 参数 | 说明 |
|------|------|------|
| `douban/search` | `keyword` | 搜索电影、书籍、音乐 |
| `douban/movie` | `id` | 电影/剧集详情（评分、演员、影评） |
| `douban/movie-hot` | `tag`（可选） | 热门电影 |
| `douban/movie-top` | `genre`（可选） | 分类排行榜 |
| `douban/top250` | `start`（可选） | 豆瓣 Top 250 |
| `douban/comments` | `id`, `sort`（可选） | 电影短评 |

### YouTube
| 命令 | 参数 | 说明 |
|------|------|------|
| `youtube/search` | `query` | 搜索视频 |
| `youtube/video` | `id` | 视频详情 |
| `youtube/comments` | `id` | 视频评论 |
| `youtube/channel` | `id` 或 `handle` | 频道信息 + 最新视频 |
| `youtube/feed` | `type`（可选：home/subscriptions） | 首页或订阅 |
| `youtube/transcript` | `id` | 视频字幕/文稿（带时间戳） |

### 小红书
| 命令 | 参数 | 说明 |
|------|------|------|
| `xiaohongshu/me` | — | 当前登录用户信息 |
| `xiaohongshu/feed` | — | 首页推荐 |
| `xiaohongshu/search` | `keyword` | 搜索笔记 |
| `xiaohongshu/note` | `note_id` | 笔记详情 |
| `xiaohongshu/comments` | `note_id` | 笔记评论 |
| `xiaohongshu/user_posts` | `user_id` | 用户笔记列表 |

> 所有小红书适配器使用 **Pinia Store Actions** — 调用页面自己的 Vue store 函数，走完整的签名 + 拦截器链路。零逆向。

## 开发适配器

运行 `bb-browser guide` 查看完整开发流程，或阅读 [SKILL.md](SKILL.md)。

## 贡献

```bash
# 方式 A：使用 gh CLI
gh repo fork epiral/bb-sites --clone
cd bb-sites && git checkout -b feat-mysite
# 添加适配器文件
git push -u origin feat-mysite
gh pr create

# 方式 B：使用 bb-browser（不需要 gh）
bb-browser site github/fork epiral/bb-sites
git clone https://github.com/你的用户名/bb-sites && cd bb-sites
git checkout -b feat-mysite
# 添加适配器文件
git push -u origin feat-mysite
bb-browser site github/pr-create epiral/bb-sites --title "feat(mysite): 添加适配器" --head "你的用户名:feat-mysite"
```

## 私有适配器

私有适配器放在 `~/.bb-browser/sites/`，同名时优先于社区适配器。

```
~/.bb-browser/
├── sites/          # 私有适配器（优先）
└── bb-sites/       # 本仓库（bb-browser site update）
    ├── reddit/
    ├── twitter/
    ├── zhihu/
    ├── bilibili/
    ├── weibo/
    ├── douban/
    └── youtube/
```
