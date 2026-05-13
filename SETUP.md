Here's the complete setup from absolute zero — fresh machine, no `uv`, cloning from GitHub. I'll do Windows since that's what your screenshots show, with macOS/Linux notes where they differ.

## Step 0: Prerequisites you probably already have

**Git** — to clone the repo.
```cmd
git --version
```
If "command not found": download from https://git-scm.com/downloads, install with all default options, then **close and reopen your terminal**.

**Python 3.10 or newer** — `uv` will use this.
```cmd
python --version
```
If you see 3.9 or older, or "command not found": go to https://www.python.org/downloads/ and install the latest. **During installation, check the box that says "Add Python to PATH."** This is the most commonly missed step. Reopen terminal after.

## Step 1: Install `uv`

`uv` is what the spec mandates — it manages the virtual environment and dependencies in one command.

**Windows (PowerShell, not CMD):**
```powershell
powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/install.ps1 | iex"
```

**macOS / Linux:**
```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
```

**Critical:** after the installer finishes, **close the terminal window completely and open a fresh one**. The installer adds `uv` to your PATH, but already-open terminals don't pick that up.

Verify:
```cmd
uv --version
```
Should print something like `uv 0.5.x`. If still "not recognized," restart your computer (PATH refresh) and try again.

## Step 2: Clone the repo

Open a terminal in whatever folder you want the project to live in (e.g. `Documents/` or `Desktop/`):

```cmd
cd Documents
git clone <YOUR-REPO-URL> zuma_td
cd zuma_td
```

Replace `<YOUR-REPO-URL>` with whatever GitHub gives you when you click the green **Code → HTTPS** button on the repo page. It looks like `https://github.com/yourgroup/zuma-tower-defense.git`.

Quick check — you should now be inside the folder:
```cmd
dir
```
Expect to see `game.py`, `settings.json`, `pyproject.toml`, `README.md`, `src` folder, etc.

## Step 3: Create the virtual environment + install Pyxel

One command:
```cmd
uv sync
```

This reads `pyproject.toml`, figures out it needs Pyxel, creates a `.venv/` folder right there in your project, downloads Pyxel into it. Takes 10–60 seconds depending on your connection.

You'll see output like:
```
Using CPython 3.12.x
Creating virtual environment at: .venv
Resolved 2 packages
Installed 2 packages
 + pyxel==2.x.x
```

## Step 4: Activate the virtual environment

**Windows (CMD):**
```cmd
.venv\Scripts\activate.bat
```

**Windows (PowerShell):**
```powershell
.venv\Scripts\Activate.ps1
```

If PowerShell complains about "execution policy," run this **once ever** to permit script execution for your user, then re-try activate:
```powershell
Set-ExecutionPolicy -Scope CurrentUser -ExecutionPolicy RemoteSigned
```

**macOS / Linux:**
```bash
source .venv/bin/activate
```

After activation, your prompt changes to show `(zuma-tower-defense)` at the front:
```
(zuma-tower-defense) C:\Users\you\Documents\zuma_td>
```
That prefix is how you know the venv is active.

## Step 5: Run the game

```cmd
pyxel run game.py
```

A window pops up with the title screen. Click anywhere or press SPACE to start.

---

## The condensed version (for the README)

Once everything from Steps 0–1 is installed system-wide, the per-clone workflow is just three lines (exactly what the spec asks for):

```bash
git clone <repo-url> zuma_td
cd zuma_td
uv sync
source .venv/bin/activate    # or .venv\Scripts\activate.bat on Windows
pyxel run game.py
```

That's the workflow your group will use every day. Steps 0 and 1 are once-per-machine.

---

## Common gotchas, in order of likelihood

**"uv: command not found" after install** → didn't reopen the terminal. Close it completely, open a new one.

**"git: command not found"** → Git not installed, or Git installed but terminal hasn't picked up the new PATH. Reopen terminal.

**"python: command not found" or wrong version** → reinstall Python from python.org and check "Add Python to PATH" during install.

**`uv sync` fails with "No `pyproject.toml`"** → you're not in the project root. Run `dir` (or `ls`), and `cd` into the folder that contains `pyproject.toml`.

**`uv sync` fails with SSL or network errors** → corporate/school firewall blocking PyPI. Try a different network or hotspot.

**`pyxel run game.py` fails with `ModuleNotFoundError: No module named 'src'`** → the `src/` folder is missing or empty. Check `dir src` shows `__init__.py`, `model.py`, `view.py`, `controller.py`.

**`pyxel run game.py` fails with `ModuleNotFoundError: No module named 'pyxel'`** → venv isn't activated. Look for `(zuma-tower-defense)` in your prompt. If absent, redo Step 4.

**Game window appears but immediately closes** → an exception in the first frame. Run `python game.py` instead (note: `python`, not `pyxel run`) to see the traceback printed.

**On Linux: window opens but no sound** → `sudo apt install libsdl2-mixer-2.0-0`. Game still runs without it.

---

## What the group needs to know once they've cloned

After the first-time setup above, daily workflow is just:

```cmd
cd zuma_td
.venv\Scripts\activate.bat    # or source .venv/bin/activate
pyxel run game.py
```

If someone pulls new changes and the dependency list changed (e.g. you add a new library to `pyproject.toml` later), they re-run `uv sync` — it's smart enough to only install what's new.
