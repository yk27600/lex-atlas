# Lex Atlas

A law and politics game for kids and students. Players conquer a world map by learning how each country is governed, write their own rules for real problems in the Rule Lab, and spot the fake law in a daily challenge.

The whole game is a single file: `index.html`. There's no build step and nothing to install.

## Play it

- **On claude.ai (all features):** https://claude.ai/artifact/TGkd4sPvam42NHYt4aRW4L
  The AI judge and the shared leaderboard only work here, for signed-in viewers.
- **On your own website (GitHub Pages):** the World map, lessons, quizzes and Daily challenge all work. The Rule Lab shows a message that the AI judge isn't available, and the leaderboard shows only your own scores.
- **Offline:** double-click `index.html` to open it in any browser.

Progress (stamps, XP, streak) is saved in the browser you play in, so it doesn't carry between devices.

## Put it on GitHub Pages (free, about 5 minutes)

1. Sign in at https://github.com (or create a free account).
2. Click **+** (top right), then **New repository**. Name it `lex-atlas`, choose **Public**, and click **Create repository**.
3. On the new repo page, click **uploading an existing file**. Drag in `index.html` and `README.md`, then click **Commit changes**.
4. Go to **Settings**, then **Pages**. Under "Branch", pick **main** and **/ (root)**, then click **Save**.
5. After a minute or two, your game is live at `https://<your-username>.github.io/lex-atlas/`.

To update the game later, upload a new `index.html` to the same repo. The site refreshes on its own.

## Where things are in index.html

Everything is in the `<script>` near the bottom of the file:

| What | Look for |
|---|---|
| Country lessons and quizzes (24 countries) | `const LESSONS = {` |
| Rule Lab cases | `const SCENARIOS = [` |
| Daily challenge laws (real and fake) | `const REALS = [` and `const FAKES = [` |
| All 195 countries and their map tile positions | `const COUNTRY_ROWS` and `const POS` |
| AI judge prompt and scoring | `async function gradeRule` |
| Leaderboard | `async function initRemote` and `function renderLb` |
| Colours and fonts | the `:root {` block at the top of the `<style>` |

To add a country, copy one entry in `LESSONS` (for example `SG: { ... }`), change the two-letter code to the country's code from `COUNTRY_ROWS`, and rewrite the lessons and questions. Its map tile turns blue automatically.

## Making the AI judge and leaderboard work everywhere

Outside claude.ai, these need a small server of your own: one endpoint that sends the student's rule to the Anthropic API (using your API key, which you pay for per grade), and a database for leaderboard scores. Never put an API key inside `index.html`, because anyone could copy it.

## Before real students use it

- Have a teacher or law student fact-check the lessons and daily-challenge laws.
- If players are under 13, check children's privacy laws (COPPA in the US, GDPR-K and the UK Children's Code, PDPA in Singapore) before collecting any data.
