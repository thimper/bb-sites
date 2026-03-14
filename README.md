# bb-sites

Community site adapters for [bb-browser](https://github.com/epiral/bb-browser) — turning websites into CLI commands.

Each site adapter is a JS function that runs inside your browser via `bb-browser eval`. The browser is already logged in — no API keys, no cookie extraction, no anti-bot bypass.

## Quick Start

```bash
bb-browser site update                     # install/update site adapters
bb-browser site list                       # list available commands
bb-browser site reddit/me                  # run a command
bb-browser site reddit/thread <url>        # run with args
```

## Available Sites (50+ adapters)

### Reddit
| Command | Args | Description |
|---------|------|-------------|
| `reddit/me` | — | Current logged-in user info |
| `reddit/posts` | `username` (optional) | User's submitted posts (auto-paginated) |
| `reddit/thread` | `url` | Full discussion tree for a post |
| `reddit/context` | `url` | Ancestor chain for a specific comment |

### Twitter/X
| Command | Args | Description |
|---------|------|-------------|
| `twitter/user` | `screen_name` | User profile |
| `twitter/thread` | `tweet_id` | Tweet + all replies (supports URL or numeric ID) |

### GitHub
| Command | Args | Description |
|---------|------|-------------|
| `github/me` | — | Current logged-in user info |
| `github/repo` | `repo` (owner/repo) | Repository info |
| `github/issues` | `repo`, `state` (optional) | Issue list |
| `github/issue-create` | `repo`, `title`, `body` (optional) | Create an issue |
| `github/pr-create` | `repo`, `title`, `head`, `base` (optional), `body` (optional) | Create a pull request |
| `github/fork` | `repo` | Fork a repository |

### Hacker News
| Command | Args | Description |
|---------|------|-------------|
| `hackernews/top` | `count` (optional) | Top stories |
| `hackernews/thread` | `id` | Post + comment tree |

### Zhihu (知乎)
| Command | Args | Description |
|---------|------|-------------|
| `zhihu/me` | — | Current logged-in user info |
| `zhihu/hot` | — | Trending hot list (热榜) |
| `zhihu/question` | `id` | Question detail + top answers |
| `zhihu/search` | `keyword` | Search questions and articles |

### Bilibili (B站)
| Command | Args | Description |
|---------|------|-------------|
| `bilibili/me` | — | Current logged-in user info |
| `bilibili/popular` | `page` (optional) | Popular/trending videos |
| `bilibili/ranking` | `tid` (optional) | Top ranking by category |
| `bilibili/search` | `keyword`, `sort` (optional) | Search videos |
| `bilibili/video` | `bvid` | Video details + stats |
| `bilibili/comments` | `bvid`, `sort` (optional) | Video comments |
| `bilibili/feed` | — | Dynamic timeline from followed users |
| `bilibili/history` | — | Watch history |
| `bilibili/trending` | — | Hot search keywords |

### Weibo (微博)
| Command | Args | Description |
|---------|------|-------------|
| `weibo/me` | — | Current logged-in user info |
| `weibo/hot` | — | Trending topics (热搜) |
| `weibo/feed` | — | Home timeline |
| `weibo/user` | `id` (uid or screen_name) | User profile |
| `weibo/user_posts` | `uid`, `page` (optional) | User's posts |
| `weibo/post` | `id` | Single post detail |
| `weibo/comments` | `id`, `page` (optional) | Post comments |

### Douban (豆瓣)
| Command | Args | Description |
|---------|------|-------------|
| `douban/search` | `keyword` | Search movies, books, music |
| `douban/movie` | `id` | Movie/TV detail (rating, cast, reviews) |
| `douban/movie-hot` | `tag` (optional) | Hot/trending movies |
| `douban/movie-top` | `genre` (optional) | Top rated by genre |
| `douban/top250` | `start` (optional) | Douban Top 250 |
| `douban/comments` | `id`, `sort` (optional) | Movie short reviews |

### YouTube
| Command | Args | Description |
|---------|------|-------------|
| `youtube/search` | `query` | Search videos |
| `youtube/video` | `id` | Video details (title, stats, description) |
| `youtube/comments` | `id` | Video comments |
| `youtube/channel` | `id` or `handle` | Channel info + recent videos |
| `youtube/feed` | `type` (optional: home/subscriptions) | Home or subscription feed |
| `youtube/transcript` | `id` | Video transcript/captions with timestamps |

### Xiaohongshu (小红书)
| Command | Args | Description |
|---------|------|-------------|
| `xiaohongshu/me` | — | Current logged-in user info |
| `xiaohongshu/feed` | — | Homepage recommended feed |
| `xiaohongshu/search` | `keyword` | Search notes |
| `xiaohongshu/note` | `note_id` | Note detail |
| `xiaohongshu/comments` | `note_id` | Note comments |
| `xiaohongshu/user_posts` | `user_id` | User's published notes |

> All XHS adapters use **pinia store actions** — calling the page's own Vue store functions, which go through the complete signing + interceptor chain. Zero reverse engineering needed.

## Writing a Site Adapter

Run `bb-browser guide` for the full development workflow, or read [SKILL.md](SKILL.md).

```javascript
/* @meta
{
  "name": "platform/command",
  "description": "What this adapter does",
  "domain": "www.example.com",
  "args": {
    "query": {"required": true, "description": "Search query"}
  },
  "readOnly": true,
  "example": "bb-browser site platform/command value1"
}
*/

async function(args) {
  if (!args.query) return {error: 'Missing argument: query'};
  const resp = await fetch('/api/search?q=' + encodeURIComponent(args.query), {credentials: 'include'});
  if (!resp.ok) return {error: 'HTTP ' + resp.status, hint: 'Not logged in?'};
  return await resp.json();
}
```

## Contributing

```bash
# Option A: with gh CLI
gh repo fork epiral/bb-sites --clone
cd bb-sites && git checkout -b feat-mysite
# add adapter files
git push -u origin feat-mysite
gh pr create

# Option B: with bb-browser (no gh needed)
bb-browser site github/fork epiral/bb-sites
git clone https://github.com/YOUR_USER/bb-sites && cd bb-sites
git checkout -b feat-mysite
# add adapter files
git push -u origin feat-mysite
bb-browser site github/pr-create epiral/bb-sites --title "feat(mysite): add adapters" --head "YOUR_USER:feat-mysite"
```

## Private Adapters

Put private adapters in `~/.bb-browser/sites/`. They override community adapters with the same name.

```
~/.bb-browser/
├── sites/          # Your private adapters (priority)
│   └── internal/
│       └── deploy.js
└── bb-sites/       # This repo (bb-browser site update)
    ├── reddit/
    ├── twitter/
    ├── zhihu/
    ├── bilibili/
    ├── weibo/
    ├── douban/
    └── youtube/
```
