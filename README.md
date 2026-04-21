# Masan Dice — Owlbear Rodeo Extension (MVP)

A d8-pool dice roller for the Masan TTRPG. Sits in the Owlbear Rodeo toolbar as a popover.

Handles:
- d8 pools (1–10 dice), TN 6 hit counting
- Variable Difficulty (0–6)
- Push (reroll non-hits) with visual callout
- Botch detection (ones > hits)
- Shared table log via `OBR.broadcast` — all connected clients see each roll, tagged with player name and colour
- Organ selector (Bones / Sinew / Nerves / Heart) — cosmetic tagging only for now

**Not yet included** (deferred for later iteration):
- Resistance contests (attacker/defender coupled rolls)
- Push cost tracking (spending Domain / Organ points)
- Roll persistence across sessions (currently session-only, resets when the popover is closed)
- Character sheet integration

---

## Deploy

Because Owlbear needs an HTTPS URL to load the manifest, you need a host. GitHub Pages is free and takes 60 seconds:

1. Create a new GitHub repo (e.g. `masan-dice`) and push these four files to the root:
   - `manifest.json`
   - `index.html`
   - `icon.svg`
   - `README.md`
2. Repo → **Settings → Pages → Source: Deploy from a branch → `main` / root**.
3. Wait ~30s. GitHub gives you a URL like `https://<you>.github.io/masan-dice/`.
4. Your manifest URL is `https://<you>.github.io/masan-dice/manifest.json`.

## Install in Owlbear Rodeo

1. Open any Owlbear room you GM (or have install rights in).
2. **Settings → Extensions → Add Extension**.
3. Paste the manifest URL from step 4 above.
4. Confirm. A `Masan Dice` button appears in the toolbar.

All connected players need to install it too if you want them to see each others' rolls. Owlbear extensions are per-user.

## Local dev

You can iterate on `index.html` by serving the folder locally — it needs HTTPS-ish access because the SDK is pulled from `esm.sh`. The simplest approach:

```
cd masan-dice
python3 -m http.server 8080
```

Then open `http://localhost:8080/`. The popover will render in a "standalone · broadcast disabled" mode — the roller works fully, just without the shared log across clients. Good for UI iteration.

## What to test

- **Basic roll.** Select an organ, set pool 4 / D2, hit Roll. Verify hit count, MoS calculation, and the visual hit/miss/one highlighting on each die.
- **Push.** Roll something that fails (e.g. pool 3 / D3). The Push button appears. Click. Non-hits reroll in place; the summary updates; a second log entry appears tagged with ↻.
- **Botch.** Roll pool 2 / D2 a few times until you get more 1s than 6–8s. Verify the botch tag renders in ember red and the Push button does *not* appear (botches can't be pushed).
- **Broadcast.** With two clients both running the extension in the same OBR room, roll on client A and confirm it appears on client B's log within a second, tagged with A's player name and colour.

## Open questions flagged for design review

1. **Push cost display.** Currently Push has no cost shown. Once Domain Point recovery is codified, the roller should show "Push (1 Domain pt)" / "Push (1 Organ pt)" and let the player pick which to spend. For MVP this is left to the fiction/honour-system.
2. **Difficulty range.** UI caps at D6, but most play is D0–D4. Worth capping at D5 if the higher values are noise.
3. **Botch & Push interaction.** Currently Push is blocked on botches. Confirm this matches intended rule — a player might want to "push a botch" with Domain to try to salvage, though that contradicts the botch's narrative finality.
4. **Stale-die visualisation on push.** The current implementation replaces rerolled dice in place rather than showing both the old (crossed out) and new values side-by-side. Side-by-side is more transparent but clutters the popover at pool 8+. Worth deciding.
