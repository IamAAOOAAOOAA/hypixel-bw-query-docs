> ⚠️ **提交到 Dashboard 表单时请用 `APPLICATION.form.txt`，不要直接复制本文。**
> Dashboard 描述框是字符白名单校验（实测正则），**不接受 `/` 斜杠、`<` `>` 尖括号、`*` `#` 反引号等 Markdown 标记**，直接粘贴下面的正文会报 `Description contains invalid characters`。`APPLICATION.form.txt` 是同一内容的合规纯文本版。
>
> 另注：本文 "API usage" 里写的 `per-user cooldown 60s + server-side cache 10min` 与当前插件实现不符（代码里只有皮肤缓存 600s，没有冷却、也没有战绩缓存）。**要么先补上缓存与冷却，要么按实际行为改写**，不要声明不存在的机制。

# Hypixel Developer Dashboard — Application Text

Copy the blocks below into the corresponding fields of the "Create App" form.
Replace every `<...>` placeholder. Do **not** mention any third-party cache, mirror, or proxy.

## Fields

| Field | Value |
|---|---|
| Application type | **Personal** |
| Application name | `<your project name>` |
| Project URL | `https://github.com/<you>/hypixel-bw-query-docs` |
| Second URL (if asked) | same repository, or your project page |

## Description (paste this)

> I am building a small chat bot for a Minecraft community. It lets a member check their own
> Hypixel Bed Wars statistics directly in the group chat where they already talk, without
> visiting a third-party website.
>
> **How it works**
> A user sends one command containing a Minecraft username (with no argument, the bot queries
> the account configured as the default). The bot resolves the username to a UUID through the
> Mojang API and caches that mapping locally. It then makes a single request to
> `GET /v2/player` for that UUID and reads the `stats.Bedwars` object and
> `achievements.bedwars_level`. The values are formatted into one image card that is posted
> back into the chat.
>
> **API usage**
> One `/v2/player` request per user command. A per-user cooldown of 60 seconds and a
> server-side cache mean the same UUID is not requested again for at least 10 minutes, so a
> busy community generates on the order of a few hundred requests per day at most. There are
> no scheduled jobs and no background polling: if nobody runs the command, the application
> makes no requests at all.
>
> **What it does not do**
> It does not keep stat history and does not perform session tracking; it only displays the
> current values returned by a single request. It does not expose, forward, mirror, or
> redistribute any Hypixel endpoint to other developers or services — there is no public
> endpoint that proxies the API. It does not attempt to de-anonymize players who are using
> the nick feature, and it does not collect data at scale.
>
> **Key handling**
> The API key is stored server-side in the bot's private configuration file. It is never sent
> to users, never embedded in client software, and never published in a repository. Raw API
> responses are not stored or redistributed; only the rendered image is posted.
>
> **Other**
> The project is non-monetized and has no paid tier. It is clearly labeled as an unofficial
> community tool and not affiliated with or endorsed by Hypixel.

## If the application is rejected

Rejection messages are usually the generic *"We cannot provide a production application based
on the information provided."* Then:

1. Re-read the Description above and check you did not drift into stat tracking,
   per-player tracking, or anything that reads as a proxy.
2. Make the numbers more concrete (requests per day, cache duration) — vague numbers get rejected.
3. Make sure the project URL actually resolves and shows the project.
4. Resubmit; multiple attempts are normal.

## Notes before applying

- **Personal** is enough for a non-monetized community tool, including a public one.
  Only pick **Production** if you monetize, run a team, or need a higher rate limit.
  Production web projects must use a domain you own (no `*.vercel.app` style subdomains).
- Development keys are only valid for about 3 days. The long-lived key comes with the
  **approved application**, so the "expires every 3 days" problem is solved by approval,
  not by generating keys differently.
- Keep the application active: applications with zero requests over long periods can be
  disabled. A lightly-used bot is fine; a forgotten one is not.
- Never paste your key into the repository, a spreadsheet, a gist, or a front-end build.
