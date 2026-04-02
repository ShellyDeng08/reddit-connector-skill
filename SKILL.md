---
name: reddit-connector
description: Browse Reddit - search posts, read subreddits, view comments, and check user profiles using Reddit's public JSON API
---

# Reddit Connector Skill

You can access Reddit's public JSON API using `curl` and `jq`. No API key or authentication is required.

**Every request MUST include the User-Agent header:** `-H "User-Agent: reddit-connector-skill/1.0"`

**Rate limit:** ~60 requests/minute for unauthenticated access. Space requests if making many calls.

## Search Posts

Search across all of Reddit or within a specific subreddit.

**All of Reddit:**
```bash
curl -s -H "User-Agent: reddit-connector-skill/1.0" \
  "https://www.reddit.com/search.json?q=QUERY&limit=25" \
  | jq '[.data.children[].data | {title, author, score, subreddit, num_comments, permalink: ("https://reddit.com" + .permalink), selftext: (.selftext[:500])}]'
```

**Within a subreddit:**
```bash
curl -s -H "User-Agent: reddit-connector-skill/1.0" \
  "https://www.reddit.com/r/SUBREDDIT/search.json?q=QUERY&restrict_sr=1&limit=25" \
  | jq '[.data.children[].data | {title, author, score, num_comments, permalink: ("https://reddit.com" + .permalink), selftext: (.selftext[:500])}]'
```

Query parameters: `q` (required), `limit` (1-100, default 25), `sort` (relevance, hot, top, new), `t` (hour, day, week, month, year, all — for time filtering).

## Browse Subreddit Posts

Get posts from a subreddit sorted by hot, new, top, or rising.

```bash
curl -s -H "User-Agent: reddit-connector-skill/1.0" \
  "https://www.reddit.com/r/SUBREDDIT/SORT.json?limit=25" \
  | jq '[.data.children[].data | {title, author, score, num_comments, permalink: ("https://reddit.com" + .permalink), selftext: (.selftext[:500])}]'
```

Replace `SORT` with: `hot`, `new`, `top`, or `rising`.

For `top`, add `?t=TIME` where TIME is: `hour`, `day`, `week`, `month`, `year`, or `all`.

Example — top posts this week from r/programming:
```bash
curl -s -H "User-Agent: reddit-connector-skill/1.0" \
  "https://www.reddit.com/r/programming/top.json?t=week&limit=10" \
  | jq '[.data.children[].data | {title, author, score, num_comments, permalink: ("https://reddit.com" + .permalink)}]'
```

## Read Post Comments

Append `.json` to any Reddit post URL.

```bash
curl -s -H "User-Agent: reddit-connector-skill/1.0" \
  "https://www.reddit.com/r/SUBREDDIT/comments/POST_ID/SLUG.json?limit=25" \
  | jq '{
    post: .[0].data.children[0].data | {title, author, score, selftext: (.selftext[:500]), num_comments},
    comments: [.[1].data.children[] | select(.kind == "t1") | .data | {author, body: (.body[:500]), score}]
  }'
```

If you have a full Reddit URL, just append `.json`:
```bash
curl -s -H "User-Agent: reddit-connector-skill/1.0" \
  "https://www.reddit.com/r/programming/comments/abc123/my_post/.json?limit=25" \
  | jq '{
    post: .[0].data.children[0].data | {title, author, score, selftext: (.selftext[:500]), num_comments},
    comments: [.[1].data.children[] | select(.kind == "t1") | .data | {author, body: (.body[:500]), score}]
  }'
```

## User Profile

```bash
curl -s -H "User-Agent: reddit-connector-skill/1.0" \
  "https://www.reddit.com/user/USERNAME/about.json" \
  | jq '.data | {name, link_karma, comment_karma, created_utc, created_date: (.created_utc | todate)}'
```

## User Posts

```bash
curl -s -H "User-Agent: reddit-connector-skill/1.0" \
  "https://www.reddit.com/user/USERNAME/submitted.json?limit=25" \
  | jq '[.data.children[].data | {title, subreddit, score, num_comments, permalink: ("https://reddit.com" + .permalink)}]'
```

## User Comments

```bash
curl -s -H "User-Agent: reddit-connector-skill/1.0" \
  "https://www.reddit.com/user/USERNAME/comments.json?limit=25" \
  | jq '[.data.children[].data | {subreddit, body: (.body[:500]), score, link_title}]'
```

## Error Handling

- **403 Forbidden**: Subreddit is private or quarantined. Inform the user.
- **404 Not Found**: Subreddit, user, or post does not exist (or user is suspended/deleted).
- **429 Too Many Requests**: Rate limited. Wait a few seconds and retry.
- **5xx errors**: Reddit is having issues. Wait and retry with backoff.
- **Empty `.data.children`**: Valid response but no results matched the query.

If a response is very large, reduce `limit` or truncate output to avoid flooding context.

## Tips

- Always URL-encode query parameters (spaces become `+` or `%20`).
- Deleted users show as `[deleted]` in the `author` field.
- The `selftext` field contains post body text; `url` contains link for link posts.
- `created_utc` is a Unix timestamp — convert with `| todate` in jq.
- For pagination, use the `after` parameter with the `name` field of the last item: `&after=t3_abc123`.
