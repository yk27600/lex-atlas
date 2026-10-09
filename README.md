# Lex Atlas

A law and politics game for kids and students. Players conquer a world map by learning how each country is governed, write their own rules for real problems in the Rule Lab (graded by an AI judge), and spot the fake law in a daily challenge.

- **Play:** https://yk27600.github.io/lex-atlas/
- **claude.ai version:** https://claude.ai/artifact/TGkd4sPvam42NHYt4aRW4L (same game; it has its own separate leaderboard)

## Files

| File | What it is | Where it runs |
|---|---|---|
| `index.html` | The whole game | GitHub Pages |
| `worker.js` | The server: AI judge and leaderboard | Cloudflare Worker `lex-atlas-judge` |
| `SETUP-AI-JUDGE.md` | How the Cloudflare server was set up | |

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
| Country lessons and quizzes (24 countries) | `const LESSONS = {` |
| Rule Lab cases | `const SCENARIOS = [` (also copy changes to `CASES` in `worker.js`) |
| Daily challenge laws | `const REALS = [` and `const FAKES = [` |
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

- Have a teacher or law student fact-check the lessons and daily-challenge laws.
- Free AI models may log what's sent to them. Before children use the Rule Lab, switch `MODEL` to a paid model from a provider that doesn't keep data, and add a privacy notice.
- If players are under 13, check children's privacy laws (COPPA in the US, GDPR-K and the UK Children's Code, PDPA in Singapore).
