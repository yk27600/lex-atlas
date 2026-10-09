# Setting up the AI judge (Cloudflare + OpenRouter)

`worker.js` is a tiny server that grades Rule Lab answers using your OpenRouter key. Your key stays hidden inside Cloudflare and never goes into the game.

## 1. Create the Worker
1. Sign up or sign in at https://dash.cloudflare.com (free plan is fine).
2. In the left sidebar, open **Compute (Workers)** > **Workers & Pages** (the name varies slightly).
3. Click **Create** (or **Create application**), then **Start with Hello World!**.
4. Name it `lex-atlas-judge` and click **Deploy**.

## 2. Paste in the judge's code
1. Click **Edit code**.
2. Select all the code in the editor (Cmd+A) and delete it.
3. Open `worker.js` in TextEdit, copy everything (Cmd+A, Cmd+C), and paste it into the editor.
4. Click **Deploy** (top right).

## 3. Add your OpenRouter key
1. Go back to the Worker's page and open **Settings** > **Variables and Secrets**.
2. Click **Add**. Set **Type** to **Secret**, **Variable name** to `OPENROUTER_API_KEY`, and paste your `sk-or-v1-...` key as the **Value**.
3. Click **Deploy** (or **Save**).

## 3b. Add the leaderboard database
1. In the left sidebar, open **Storage & databases** > **D1 SQL Database** and click **Create**.
2. Name it `lex-atlas-leaderboard` and click **Create**.
3. Back on the Worker, open **Settings** > **Bindings** > **Add binding** > **D1 database**.
4. Set **Variable name** to `DB` and choose `lex-atlas-leaderboard`. Save. The tables are created automatically.

## 4. Check it's running
Open your Worker's address (shown on its page, like `https://lex-atlas-judge.yourname.workers.dev`). You should see `"Lex Atlas judge is running."`, `"keySet":true` and `"leaderboard":true`.

## 5. Connect the game
1. In `index.html`, find the line `const GRADER_URL = "";` and paste your Worker address between the quotes.
2. Upload the new `index.html` to your GitHub repository (Add file > Upload files > Commit changes).
3. After a minute or two, try the Rule Lab on your GitHub Pages site.

## Optional settings (Settings > Variables and Secrets, Type: Text)
- `MODEL`: a different OpenRouter model id. Free ones end in `:free`.
- `ALLOWED_ORIGIN`: the website allowed to use the judge. It defaults to `https://yk27600.github.io`.
