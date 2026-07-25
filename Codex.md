## See available models

**Inside a running session:** type `/model` — it opens a picker showing all models you can switch to live.

**From the terminal (raw catalog):**
```bash
codex debug models
```
This prints the full model catalog Codex sees, as JSON. Add `--bundled` if you just want what's built into your current install without refreshing from the server:
```bash
codex debug models --bundled
```

You can also check the docs' current lineup at any time: https://developers.openai.com/codex/models

## Switch models

**At launch**, use `--model` (or the short flag `-m`):
```bash
codex --model gpt-5.4 "refactor the auth module"
```

**Mid-session**, while Codex is already running, just type:
```
/model
```
then pick from the list — no need to restart.

**Set a default** so you don't have to specify it every time — add this to `~/.codex/config.toml`:
```toml
model = "gpt-5.4"
```

A quick note: model names/availability change often (new snapshots roll out regularly), so `codex debug models` or `/model` will always show you what's *actually* available right now rather than relying on a static list.
