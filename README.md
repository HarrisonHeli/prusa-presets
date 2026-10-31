# prusa-presets

Shared PrusaSlicer user presets (print / filament / printer / physical_printer / SLA)
for all my machines. Git is the sync; every machine is a clone.

## How it works
- Each machine's PrusaSlicer preset dirs are symlinks into its clone.
- Launch PrusaSlicer via the menu entry / `prusaslicer-sync`:
  1. commits anything left over, `git fetch` + rebase onto origin (offline → just uses local),
  2. runs PrusaSlicer,
  3. on exit commits changes as `<presets> [hostname]` and pushes.
- Conflicts (same setting changed on two machines): rebase is aborted, your local
  commit is kept but not pushed, and you get a desktop notification. Fix with
  `cd ~/.local/share/prusa-presets && git pull --rebase` (edit the file, `git add`, `git rebase --continue`, `git push`).
- `.machines/<host>` records each machine's PrusaSlicer version; mismatch → notification.
- A git clean filter strips the `on <date> at <time> UTC` stamp from line 1 so saves don't produce noise diffs.

## New machine
1. Add a deploy key (per machine) or use your GitHub SSH key — see "Auth" below.
2. Start PrusaSlicer once, finish the wizard, close it.
3. ```
   git clone git@github.com:<user>/prusa-presets.git ~/.local/share/prusa-presets
   ~/.local/share/prusa-presets/tools/install.sh --dry-run
   ~/.local/share/prusa-presets/tools/install.sh
   ```
4. Launch PrusaSlicer from the menu.

## Auth (per-machine deploy key, write access, this repo only)
```
ssh-keygen -t ed25519 -N '' -C "prusa-presets@$(hostname -s)" -f ~/.ssh/prusa_presets_ed25519
cat >>~/.ssh/config <<'EOF'
Host github-prusa
  HostName github.com
  User git
  IdentityFile ~/.ssh/prusa_presets_ed25519
  IdentitiesOnly yes
EOF
```
Add the `.pub` at GitHub → repo → Settings → Deploy keys → *Allow write access*,
then clone with `git@github-prusa:<user>/prusa-presets.git`.

## Handy
- `prusaslicer-sync --status` — state, last commits, machine versions
- `prusaslicer-sync --sync` — sync without launching
- `git log -p -- print/PLA-TRI.ini` — what changed where; `git revert <sha>` to undo
- Log: `~/.local/state/prusa-presets.log`
