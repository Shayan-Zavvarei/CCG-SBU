# Learning Tmux: from zero to hero :/

## Lesson 1: Installation and Tmux Basics
Get tmux on your system and undrastand why it's important.

### 1.1 Why tmux Matters for Developers

If you write python, train ML models, run experiments, or SSH into remote servers, you need `tmux`.
Without it:
- Your training job dies when your laptop sleeps or SSH drops.
- You can’t multitask efficiently in one terminal.
- You lose your entire workspace on reboot.

With `tmux`, your terminal **persists** -- even across reboots (with plugins, coming later). It's your persistant, shareble, scriptable command center.

### 1.2 Installing tmux
Ubuntu / Debian
```bash
sudo apt update
sudo apt install -y tmux
```
CentOS / Rocky Linux / AlmaLinux
```bash
sudo dnf install -y tmux          # Rocky/Alma 9+
# OR
sudo yum install -y tmux          # Older CentOS
```
Arch Linux
```bash
sudo pacman -S tmux
```
From Source (Recommended if you need latest features)
```bash
# Install build deps
sudo apt install -y build-essential bison pkg-config libevent-dev libncurses-dev

# Clone and build
git clone https://github.com/tmux/tmux.git
cd tmux
sh autogen.sh
./configure && make

# Run without installing (optional)
./tmux -V

# Or install system-wide
sudo make install
```
Verify installation:
```bash 
tmux -V
# Should return: tmux 3.x.x (e.g., tmux 3.4a)
```

### 1.3 Your first tmux session: The 30-Second Onboarding
#### 1. Start New Session:
```bash 
tmux new -s dev
```
You're now inside a **session** named `dev`.
#### 2. Run a Python script (or just `top` to see something alive):
```bash
python3 -c "import time; [print(f'Epoch {i}'); time.sleep(2)] for i in range(100)]"
```
#### 3. **Detach** without killing it:
Press `Ctrl-b` then `d`
you're back to your mormal shell. The session runs in the background.
#### 4. **Re-attach**:
```bash 
tmux attach -t dev
```
Your script is still running exactly as you left it!
*This alone solves 90% of remote-work frustrations.*
---

## Lesson 2: Core Concepts -- Sessions, windows and Panes Demystified
Structure your terminal like a pro developer workspace.
### 2.1: The tmux Mental Model
Think of **tmux** as a terminal IDE with three nested layers:

| Layer    | What It Is                                      | Real-World Analogy                                        |
|----------|--------------------------------------------------|-----------------------------------------------------------|
| Session  | A detached workspace that survives logouts       | A project workspace (e.g., `"ML-Training"`)               |
| Window   | Like a browser tab inside a session              | A task context (e.g., `“Data Prep”`, `“Training”`, `“Monitoring”`) |
| Pane     | A split view inside a window                     | Side-by-side tools (e.g., editor + logs + shell)          |

You can have:

- **Multiple sessions** running simultaneously (e.g., one for work, one for side projects).
- Each **session** has **multiple windows**.
- Each **window** has **multiple panes**.

**Key insight**:  
Sessions persist in the background. Windows and panes live inside sessions.  
Detach from a session → everything inside it keeps running.

### 2.2 Creating & Managing Sessions
#### Start a new named session
```bash 
tmux new -s research
```
**Note** Always name your sessions! Unnamed sessions get random IDs which are unmanagable.

#### List all sessions
like a linux termianl
```bash 
tmux ls
# Output: research: 1 windows (created Fri Dec 12 10:30:22 2025)
```
#### Attached to a session
```bash 
tmux attach -t research    # -t = target
# or shorthand:
tmux a -t research
```

#### Detach from inside tmux
press `Ctrl-b` then `d`
You're back to your shell. Session runs in background.

#### Kill a session
```bash 
tmux kill-session -t research
```
#### Kill all sessions except current session
```bash 
tmux kill-session -a 
```
**Warning**: Killing a session terminates all processes inside it (unless they’re daemonized).

### 2.3: Windows: Your Task Tabs
Inside a **tmux session**:

| Action                   | Keybinding (`Ctrl-b` +) | Command Alternative        |
|--------------------------|--------------------------|----------------------------|
| Create new window        | `c`                      | `tmux new-window`          |
| Rename current window    | `,` (comma)              | `tmux rename-window logs`  |
| Next window              | `n`                      | —                          |
| Previous window          | `p`                      | —                          |
| Go to window 0, 1, 2...  | `0`, `1`, `2`...         | —                          |
| List all windows         | `w`                      | —                          |
| Kill current window      | `&`                      | `tmux kill-window`         |

**Pro tip**: Name your windows meaningfully:

- `data`
- `train`
- `logs`
- `shell`

Makes navigation instant.

### 2.4: Panes: Your Side-by-Side Views
Panes let you split a window vertically or horizontally.

| Action                          | Keybinding (`Ctrl-b` +)       | What It Does                              |
|----------------------------------|-------------------------------|-------------------------------------------|
| Split vertically (left/right)    | `%`                           | Creates two side-by-side panes            |
| Split horizontally (top/bottom)  | `"`                           | Stacks panes vertically                   |
| Switch to next pane              | `o`                           | Cycle through panes                       |
| Switch to pane by direction      | Arrow keys (↑ ↓ ← →)          | Requires mouse or key nav (enabled by default in tmux 3.x) |
| Close current pane               | `x`                           | Prompts for confirmation                  |
| Resize pane interactively        | `Ctrl-b` + hold `Alt` + arrow | Drag edge (mouse) or hold `Alt`+arrow (keyboard) |
| Resize by 5 cells                | `Ctrl-b` + `Ctrl-↑/↓/←/→`     | Precise resizing                          |

**Game-changer**:  
Run your Python script in one pane, `htop` in another, and `tail -f training.log` in a third—all visible at once.
---
## Lesson 3: Essential Keybindings & Customizing the Prefix
By default, every tmux command starts with `Ctrl-b` (called the prefix key).
But `Ctrl-b` is:
- Far from home row
- Often conflicts with terminal/SSH clients
- Slows you down
Fix: Change it to `Ctrl-a` (historical, screen-compatible) or better—use `Ctrl-\\` or `Ctrl-_` for zero conflicts.

**Recommended**: `Ctrl-a` (most common)
- Easy to press (left hand: `Ctrl`, right hand: `a`)
- Muscle memory from screen users
- Works everywhere
**Warning**: `Ctrl-a` moves cursor to beginning of line in bash.
But inside tmux, prefix takes priority—so it’s safe. Outside tmux, it still works normally.

### 3.2 How to change the Prefix 
Add this to `~/.tmux.conf`:
```bash
# ~/.tmux.conf
# Unbind default prefix
unbind C-b
# Set new prefix to Ctrl-a
set -g prefix C-a
# Allow sending Ctrl-a to shell with double-tap
bind C-a send-prefix
```
**Reload config without restarting tmux** (do this often!):
Inside tmux:  
`Ctrl-a` then `:` → type `source-file ~/.tmux.conf` → `Enter`
Now your prefix is `Ctrl-a`. All commands below assume you’ve made this change!

### 3.3 The 20% of Keybindings You’ll Use 80% of the Time  
*Here’s a developer-focused cheat sheet—only what you actually need daily.*

---

#### Session & Window

| Action                | Keys (`Ctrl-a` +) | Why You Need It                      |
|-----------------------|-------------------|--------------------------------------|
| Detach session        | `d`               | Leave job running, close laptop      |
| Create new window     | `c`               | New task tab                         |
| Rename current window | `,`               | Label as `train`, `logs`, etc.       |
| Next/prev window      | `n` / `p`         | Quick tab switching                  |
| Go to window #        | `0`–`9`           | Instant jump                         |
| Kill window           | `&`               | Clean up finished tasks              |

---

#### Pane Management

| Action                    | Keys (`Ctrl-a` +)        | Pro Tip                                |
|---------------------------|--------------------------|----------------------------------------|
| Split vertically          | `%`                      | Code left, output right                |
| Split horizontally        | `"`                      | Logs on top, shell below               |
| Switch pane               | `o` or arrow keys        | Use arrows if mouse disabled           |
| Close pane                | `x`                      | Safe prompt                            |
| Resize pane (interactive) | `Ctrl` + arrow           | Hold `Ctrl-a`, then `Ctrl-↑/↓/←/→`    |
| Maximize/zoom pane        | `z`                      | Temp full-screen—press again to restore|
| Swap pane positions       | `{` or `}`               | Move pane left/right in layout         |

---

#### Copy Mode (Scrolling, Searching, Copying)

| Action                | Keys (`Ctrl-a` +) | Notes                              |
|-----------------------|-------------------|------------------------------------|
| Enter copy mode       | `[`               | Now use arrow/pgup/pgdn to scroll  |
| Page up/down          | `PageUp/PageDown` or `b`/`Space` (vi mode) |                                    |
| Search backward       | `/`               | Then type pattern                  |
| Search forward        | `?`               | Rarely used                        |
| Copy selection        | `Space` → move → `Enter` | Starts/stops selection         |
| Paste                 | `]`               | Paste last copied text             |

### 3.4 Bonus: Mouse Support (Yes, Really!)

Modern **tmux (3.0+)** has excellent mouse support:

- Click to switch panes/windows  
- Drag to resize panes  
- Scroll in panes (even without copy mode)  
- Select text with mouse  

Enable it in `~/.tmux.conf` (we’ll add this permanently in Lesson 6):
```bash
set -g mouse on
```
---
## Lesson 4: Professional Session, Window & Pane Management
*Orchestrate complex terminal workflows like a senior engineer*
### 4.1 Advanced Session Management
#### Naming & Organization
Always name sessions **descriptively**:
```bash
tmux new -s llm-finetune        # Good
tmux new -s project-x-2025-12   # Better
```
**Note**: Avoid generic names like `work` or `dev`.

#### List, Filter and Kill
```bash 
# List all sessions
tmux ls

# Kill a specific session
tmux kill-session -t llm-finetune

# Kill all sessions ( dangerous!)
tmux kill-server

# Kill all *except* current session
tmux kill-session -a
```
#### Attach with Options
```bash
# Attach or create if missing (great for scripts!)
tmux new-session -A -s monitoring

# Attach in read-only mode (for observation)
tmux attach -t training -r
```
Use `-A` in automation scripts:

```bash
tmux new-session -d -A -s train-job 'python train.py'
```
### 4.2: Window Mastery
#### Rename Dynamically
Inside tmux:
- Press `Ctrl-a` -> type new name -> Enter
From shell:
```bash 
tmux rename-window -t monitoring:0 tensorboard
```
#### MOve windows
- `Ctrl-a` + `a` -> enter new index (e.g., move window to position 2)

### 4.3: Pane Power: Beyond Basic Splits:
#### Split with Custom Commands
Don’t just split—launch tools directly:
```bash 
# In tmux, split vertically and run htop immediately
Ctrl-a % htop

# Or from shell:
tmux split-window -h 'tail -f training.log'
tmux split-window -v 'python eval.py'
```
---

## Lesson 5: Developer & Python-Specific Workflows
### 5.1: Running Long-Running Python Jobs That Survive Anything
The Core Pattern
Always wrap long jobs in a named tmux session:
```bash
tmux new -s train-job -d 'python train.py --epochs 100 > train.log 2>&1'
```
- `-d` → start **detached** (no need to attach unless debugging)  
- `> train.log 2>&1` → capture **all output** (stdout + stderr) to a file (**critical for later analysis**)

**Best practice**: Every training script should log to a **timestamped file**:

```bash
LOG="logs/train_$(date +%Y%m%d_%H%M).log"
tmux new -s train-neutrino -d "python train.py > $LOG 2>&1"
```
For your cosmology work: Wrap your Fisher matrix or PyTorch training runs in tmux sessions named by experiment ID 

### 5.2: Ideal tmux layout for Python Development
Here’s a battle-tested layout for local or remote development:
```bash 
+---------------------+---------------------+
| vim / code editor   | python train.py     |
| (or nano, code)     | (live output)       |
+---------------------+----------+----------+
| pytest / tests      | ipython  | tail logs|
| (or pytest-watch)   | REPL     |          |
+---------------------+----------+----------+
```
#### How to build it:
1. `tmux new -s pydev`
2. Split vertically (`Ctrl-a %`) --> Left: editor, right: shell
3. In right pane, split horizontally (`Ctrl-a "`) → top: run script, bottom: shell
4. In bottom-right, split vertically again → left: `ipython`, right: `tail -f app.log`

### 5.3 Managing Multiple Python Versions & Virtual Environments
#### Scenario: Test code across Python 3.9, 3.10, 3.11
#### Method 1: One pane per virtualenv

Pane 1: `source venv39/bin/activate && python --version`
Pane 2: `source venv310/bin/activate && python --version`
Pane 3: `source venv311/bin/activate && python --version`
Enable `synchronize-panes` → run `pip install -e .` in all at once!

#### Method 2: Use `pyenv` + pane-specific shells
```bash 
# In pane 1
pyenv shell 3.9.18
python train.py

# In pane 2
pyenv shell 3.11.7
python train.py
```
### 5.4 Interactive Python Workflows
#### IPython / Jupyter Console in a Pane
Run an enhanced REPL alongside your code:
```bash
# In a dedicated pane
ipython
# or
jupyter console --kernel=python3
```
#### Why not Jupyter Notebook?
Jupyter Lab/Notebook needs a browser. Jupyter Console gives you rich REPL in terminal—with history, tab completion, and inline plots (if using `matplotlib` with `--matplotlib` flag).

#### Pair Editor + REPL
- Left pane: `vim model.py`
- Right pane: `ipython` → `from model import *`
- Edit → save → reimport in REPL:
```python
%load_ext autoreload
%autoreload 2
```
### 5.5 Watching Files & Auto-Testing
#### Auto-run tests on save
Install `entr` (available on all distros):
```bash
# Run tests whenever Python files change
find . -name "*.py" | entr -c pytest -xvs

# Re-run training script on code change (for fast experiments)
echo train.py | entr -r python train.py
```
`-c`→ clear screen before each run
`-r` → restart the command if it’s still running (perfect for long jobs)
Put this in a dedicated `watch` pane—never miss a failed test.

#### Log Monitoring
Always tail your logs:
```bash
# In a pane
tail -f training.log | grep -E "(loss|epoch|ERROR)"
```
**Pro tip**: Pipe through ccze or grc for colored logs:
```bash
tail -f app.log | ccze -A
```
---

## Case Study: Running fisher_analysis.py on jupyterhub server:
### 1. Log in to JupyterHub -> Open Terminal
-> Clic **Terminal** in JupyterHub launcher.
### 2. Creat project folder and get your code
```bash
mkdir -p ~/projects/fisher-job
cd ~/projects/fisher-job
# EITHER upload via Jupyter file browser (drag & drop fisher_analysis.py)
# OR clone from Git:
git clone https://github.com/yourname/your-repo.git .
```
### 3. Creat a named tmux session (never use default)
```bash 
tmux new -s fisher-run
```
-> You’re now *inside* tmux. Your prompt may look the same—no worries.

### 4. (If using Conda) Active your environment
```bash 
conda activate myenv  # replace "myenv" with your env name
# OR if using micromamba
micromamba activate myenv
```

### 5. Run your script with full output logging
```bash
python fisher_analysis.py > fisher.log 2>&1
```
#### Why this works:
> saves all print statements to `fisher.log`
`2>&1` captures errors too (so nothing is lost)
Your script must save results to disk (e.g., `.npy`, `.txt`, `.h5`)

### 6. Detach safely (job keeps running)
press these **two keys**:
```bash 
Ctrl-b  d
```
→ You’ll see: `[detached (from session fisher-run)]`
→ **Now you can close your laptop.**

### (Optional) Check if still running (from any new terminal)
```bash
tmux ls 
```
→ If you see `fisher-run: 1 windows...`, it’s alive.

→ If nothing shows, the job finished or crashed.

### Re-attach to see live progress or final output
```bash 
tmux attach -t fisher-run
```
- Use `Ctrl-c` to stop if needed (only if script supports graceful exit).
- When done watching, detach again with `Ctrl-b d`.

### When finished: save, download, and clean up
- In JupyterHub file browser: navigate to `projects/fisher-job`
Download:
- `fisher.npy` (or your result file)
- `fisher.log` (for full output/error history)

#### Then kill the session (optional but makes your environment clean)
```bash
tmux kill-session -t fisher-run
```
### Final Tips:
- Always log to file (`> log.txt 2>&1`) — don’t rely on tmux scrollback.
- Name sessions clearly (`fisher-run`, not `1` or `dev`).
- Detach with `Ctrl-b d` — never just close the browser tab without detaching.
- Verify results are saved to disk in your Python script (use `np.save`, `pickle.dump`, etc.).
