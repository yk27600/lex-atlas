# Lex Atlas

A law and politics game for kids and students. Players conquer a world map by learning how each country is governed, write their own rules for real problems in the Rule Lab (graded by an AI judge), and spot the fake law in a daily challenge.

- **Play:** https://yk27600.github.io/lex-atlas/
- **claude.ai version:** https://claude.ai/artifact/TGkd4sPvam42NHYt4aRW4L (same game; it has its own separate leaderboard)

## What's in the game

- **World:** a tile map of all 195 countries, each with 3 short lessons and a quiz. A search box finds any country, and a passport shows stamps and badges.
- **Daily challenge:** spot the fake law (25 real and 19 fake laws in the pool), with streaks and bonus rounds.
- **Practice:** a Lightning round of 10 random questions from all countries.
- **Rule Lab:** 14 real cases where players write a rule and an AI judge scores it.
- **Leaderboard:** Lawmaker points and Explorer rankings.
- **Badges:** 15 achievements, from First stamp to Every country.
- **XP limit:** practice and bonus rounds give at most 60 XP a day.

## Files

| File | What it is | Where it runs |
|---|---|---|
| `index.html` | The whole game | GitHub Pages |
| `worker.js` | The server: AI judge and leaderboard | Cloudflare Worker `lex-atlas-judge` |
| `SETUP-AI-JUDGE.md` | How the Cloudflare server was set up | |
| `FACT-CHECK.md` | What was corrected when the lessons were fact-checked | |

No secrets are stored in this repository. The OpenRouter key lives only in Cloudflare.

## How it fits together

- **GitHub Pages** serves `index.html`.
- **Cloudflare Worker** (`https://lex-atlas-judge.yik9097.workers.dev`) runs `worker.js`:
  - `POST /grade` sends a student's rule to a free OpenRouter model and returns scores
  - `POST /join`, `POST /progress` and `GET /leaderboard` run the leaderboard
- **Cloudflare D1 database** `lex-atlas-leaderboard` stores nicknames and scores. It's bound to the Worker as `DB`.
- **Cloudflare secret** `OPENROUTER_API_KEY` holds the OpenRouter key.

Player progress (stamps, XP, streak) is saved in each player's browser. Lawmaker points are recorded by the server, so they can't be faked.

## Updating

- **The game:** edit `index.html` and upload it here (Add file > Upload files > Commit changes). The site updates in a minute or two.
- **The server:** edit `worker.js`, then in Cloudflare open the Worker, click **Edit code**, replace everything with the new code and click **Deploy**. Upload the new `worker.js` here too, so the copy stays current.

## Where things are in index.html

| What | Look for |
|---|---|
| Country lessons and quizzes (all 195 countries) | `const LESSONS = {` |
| Rule Lab cases | `const SCENARIOS = [`. Every case must also be added to `CASES` in `worker.js`, and the server updated first. |
| Daily challenge laws | `const REALS = [` and `const FAKES = [` |
| Badges | `function badgeList` |
| Practice mode | `function renderPractice` |
| All 195 countries and map tile positions | `const COUNTRY_ROWS` and `const POS` |
| Server address | `const GRADER_URL` |
| Colours and fonts | the `:root {` block at the top of the `<style>` |

To add a country, copy one entry in `LESSONS` (for example `SG: { ... }`), change the code to the country's two-letter code from `COUNTRY_ROWS`, and rewrite the lessons and questions. Its map tile turns blue automatically.

## Managing the leaderboard

In Cloudflare, open **Storage & databases > D1 SQL Database > lex-atlas-leaderboard > Console**. To remove a player:

```sql
DELETE FROM grades WHERE player_id IN (SELECT id FROM players WHERE nick = 'NICKNAME'); DELETE FROM players WHERE nick = 'NICKNAME';
```

## Before real students use it

- Read `FACT-CHECK.md`. The first 39 countries (Singapore to Ireland) were not web-checked, and many 2025 to 2026 events are still changing.

- Have a teacher or law student fact-check the lessons and daily-challenge laws.
- Free AI models may log what's sent to them. Before children use the Rule Lab, switch `MODEL` to a paid model from a provider that doesn't keep data, and add a privacy notice.
- If players are under 13, check children's privacy laws (COPPA in the US, GDPR-K and the UK Children's Code, PDPA in Singapore).
