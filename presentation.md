# tmux with Claude Code

**Terminal Multiplexing for AI-Assisted Development**

`tmux` | `Claude Code` | `Terminal` | `Productivity`

> Master terminal multiplexing with tmux -- persistent sessions, split panes, and powerful workflows for Claude Code and beyond.

Session --> Windows --> Panes --> Claude Code --> Ship

Sessions . Windows . Panes . Workflows

---

## Table of Contents

1. [Topics](#01--topics)
2. [What Is tmux?](#02--what-is-tmux)
3. [Installation](#03--installation)
4. [The Prefix Key](#04--the-prefix-key)
5. [Session Management](#05--session-management)
6. [Windows](#06--windows)
7. [Panes](#07--panes)
8. [Copy Mode & Scrollback](#08--copy-mode--scrollback)
9. [Configuration -- .tmux.conf](#09--configuration----tmuxconf)
10. [Status Bar Customisation](#10--status-bar-customisation)
11. [Mouse Mode](#11--mouse-mode)
12. [Surviving Disconnects](#12--surviving-disconnects)
13. [Claude Code in tmux](#13--claude-code-in-tmux)
14. [Headless Claude Code](#14--headless-claude-code)
15. [Multi-Project Workflows](#15--multi-project-workflows)
16. [Cheat Sheet](#16--cheat-sheet)
17. [Advanced Features](#17--advanced-features)
18. [Troubleshooting](#18--troubleshooting)
19. [Complete .tmux.conf](#19--complete-tmuxconf)
20. [Summary & Next Steps](#20--summary--next-steps)

---

## 01 | Topics

### tmux Fundamentals

- What tmux is and why it matters
- Installation and first session
- The prefix key concept
- Sessions, windows, and panes

### Session Management

- Creating and naming sessions
- Detaching and reattaching
- Surviving SSH disconnects
- Session persistence

### Windows & Panes

- Creating and switching windows
- Splitting panes horizontally and vertically
- Resizing and navigating panes
- Pane layouts

### Copy Mode & Scrollback

- Entering and using copy mode
- Searching terminal output
- Copying and pasting text
- Increasing scrollback buffer

### Configuration

- The .tmux.conf file
- Key rebinding
- Status bar customisation
- Mouse mode and vi keys

### Claude Code Workflows

- Running Claude Code inside tmux
- Parallel agent sessions
- Monitoring long-running tasks
- Multi-project layouts

---

## 02 | What Is tmux?

### Terminal Multiplexer

**tmux** lets you run multiple terminal sessions inside a single terminal window. Sessions persist in the background even when you disconnect -- your work keeps running.

### Key Benefits

- **Persistence** -- sessions survive disconnects and reboots
- **Multiplexing** -- split one terminal into many panes
- **Remote work** -- SSH into a server, start tmux, detach safely
- **Reproducibility** -- script your workspace layouts

### How It Works

tmux runs a **server** process in the background. Each terminal you see is a **client** connected to that server. When you close your terminal, the server keeps running -- you just reconnect later.

### Architecture Diagram

```
tmux server (persistent background process)
├── Session: dev (3 windows)
│   ├── Window 0
│   │   ├── Pane 0
│   │   └── Pane 1
│   └── Window 1
└── Session: ops (2 windows)

Server --> Sessions --> Windows --> Panes
```

---

## 03 | Installation

### Linux

```bash
# Debian / Ubuntu
sudo apt install tmux

# Fedora / RHEL
sudo dnf install tmux

# Arch
sudo pacman -S tmux
```

### macOS

```bash
# Homebrew
brew install tmux
```

### Windows (WSL)

```bash
# Inside WSL (Ubuntu)
sudo apt install tmux
```

### Verify Installation

```bash
$ tmux -V
tmux 3.4
```

### Your First Session

```bash
# Start a new session
$ tmux

# Start a named session
$ tmux new -s myproject

# You're now inside tmux!
# Notice the green status bar
# at the bottom of the terminal
```

### Common Gotcha

If you see `no server running` when you try to attach, it just means there are no active sessions yet -- start one with `tmux new`.

---

## 04 | The Prefix Key

### How tmux Commands Work

Every tmux keybinding starts with a **prefix key**, followed by the actual command key. The default prefix is:

**`Ctrl + b`**

Press `Ctrl+b`, **release both keys**, then press the command key.

### Example: Split Pane

```bash
# Split vertically (side by side)
Ctrl+b  then  %

# Split horizontally (top and bottom)
Ctrl+b  then  "
```

The prefix is **not held down** while pressing the command key -- it's a two-step sequence.

### Changing the Prefix

Many users remap the prefix to `Ctrl+a` (closer to the home row) in their `.tmux.conf`:

```bash
# ~/.tmux.conf
unbind C-b
set -g prefix C-a
bind C-a send-prefix
```

### Quick Reference

| Keys | Action |
|------|--------|
| `Ctrl+b` | Prefix (default) |
| `Prefix ?` | List all keybindings |
| `Prefix :` | Command prompt |
| `Prefix t` | Show clock |

### Notation Guide

Throughout this presentation:

- `Prefix` means `Ctrl+b` (or your custom prefix)
- `Prefix x` means: press prefix, release, then press `x`
- `C-b` is shorthand for `Ctrl+b`
- `M-x` means `Alt+x`

---

## 05 | Session Management

### Creating Sessions

```bash
# New session (auto-named 0, 1, 2...)
tmux new

# Named session
tmux new -s project-alpha

# New session starting in a directory
tmux new -s api -c ~/code/api
```

### Detach & Reattach

```bash
# Detach from current session
Prefix d

# List all sessions
tmux ls

# Reattach to a named session
tmux attach -t project-alpha

# Shorthand
tmux a -t project-alpha
```

Detaching does **not** stop processes -- everything keeps running in the background.

### Switching Sessions

```bash
# Interactive session picker
Prefix s

# Next / previous session
Prefix (    --> previous
Prefix )    --> next

# Switch by name from command line
tmux switch -t ops
```

### Session Lifecycle

```bash
# Rename current session
Prefix $

# Kill a session
tmux kill-session -t old-project

# Kill all sessions except current
tmux kill-session -a

# Kill the tmux server entirely
tmux kill-server
```

---

## 06 | Windows

### What Are Windows?

Windows are **tabs** within a session. Each window occupies the full terminal and can contain one or more panes. The status bar at the bottom shows your window list.

### Creating & Closing

```bash
# Create a new window
Prefix c

# Close the current window
Prefix &
# (confirms: kill window? y/n)

# Or just exit the shell
$ exit
```

### Renaming Windows

```bash
# Rename current window
Prefix ,

# Then type the new name and
# press Enter
```

Named windows make it easy to identify what each tab is for: `editor`, `server`, `claude`, etc.

### Switching Windows

| Keys | Action |
|------|--------|
| `Prefix 0-9` | Switch to window by number |
| `Prefix n` | Next window |
| `Prefix p` | Previous window |
| `Prefix l` | Last (most recent) window |
| `Prefix w` | Interactive window picker |
| `Prefix f` | Find window by name |

### Moving & Reordering

```bash
# Move window to a new index
Prefix .
# Then type the new index number

# Swap window positions
tmux swap-window -s 2 -t 0

# Move current window left/right
# (add to .tmux.conf)
bind -r < swap-window -t -1
bind -r > swap-window -t +1
```

---

## 07 | Panes

### Splitting

```bash
# Horizontal split (top / bottom)
Prefix "

# Vertical split (left / right)
Prefix %
```

Splits always divide the **currently focused** pane.

### Navigating Panes

| Keys | Action |
|------|--------|
| `Prefix Arrow keys` | Move to adjacent pane |
| `Prefix o` | Cycle to next pane |
| `Prefix ;` | Toggle to last active pane |
| `Prefix q` | Show pane numbers (press number to jump) |

### Closing Panes

```bash
# Close current pane
Prefix x
# (confirms: kill pane? y/n)

# Or just exit the shell
$ exit
```

### Resizing Panes

```bash
# Resize with prefix + arrow (hold)
Prefix Ctrl+Up      --> taller
Prefix Ctrl+Down    --> shorter
Prefix Ctrl+Left    --> narrower
Prefix Ctrl+Right   --> wider

# Or from the command prompt
Prefix :
resize-pane -D 10  # 10 rows down
resize-pane -R 20  # 20 cols right
```

### Zoom & Layouts

```bash
# Zoom: toggle pane fullscreen
Prefix z
# Press again to un-zoom

# Cycle through built-in layouts
Prefix Space

# Preset layouts
Prefix M-1  --> even-horizontal
Prefix M-2  --> even-vertical
Prefix M-3  --> main-horizontal
Prefix M-4  --> main-vertical
Prefix M-5  --> tiled
```

---

## 08 | Copy Mode & Scrollback

### Entering Copy Mode

```bash
# Enter copy mode
Prefix [

# You can now scroll up/down
# with arrow keys or Page Up/Down

# Exit copy mode
q  or  Escape
```

Copy mode is how you **scroll back** through terminal output in tmux. Without it, scrolling doesn't work as expected.

### Searching

```bash
# In copy mode:
/   --> search forward
?   --> search backward
n   --> next match
N   --> previous match
```

Extremely useful for finding error messages in long Claude Code output.

### Selecting & Copying Text

```bash
# In copy mode (emacs keys):
Ctrl+Space  --> start selection
M-w         --> copy selection

# In copy mode (vi keys):
Space  --> start selection
Enter  --> copy selection

# Paste
Prefix ]
```

### Enable vi Keys (Recommended)

```bash
# ~/.tmux.conf
setw -g mode-keys vi

# Now copy mode uses vi motions:
# h j k l  --> move cursor
# v        --> start selection
# y        --> yank (copy)
# /        --> search
```

### Increase Scrollback

```bash
# Default is 2000 lines
# Increase in .tmux.conf:
set -g history-limit 50000
```

---

## 09 | Configuration -- .tmux.conf

### Starter Configuration

```bash
# ~/.tmux.conf

# Remap prefix to Ctrl+a
unbind C-b
set -g prefix C-a
bind C-a send-prefix

# Enable mouse support
set -g mouse on

# Start windows and panes at 1
set -g base-index 1
setw -g pane-base-index 1

# Vi copy mode
setw -g mode-keys vi

# Large scrollback
set -g history-limit 50000

# Faster key repetition
set -sg escape-time 0

# True colour support
set -g default-terminal "tmux-256color"
set -ag terminal-overrides \
  ",xterm-256color:RGB"
```

### Intuitive Splits

```bash
# Split with | and -
bind | split-window -h -c "#{pane_current_path}"
bind - split-window -v -c "#{pane_current_path}"
unbind '"'
unbind %
```

The `-c` flag opens the new pane in the **same directory** as the current pane.

### Pane Navigation (vim-style)

```bash
# Navigate panes with h j k l
bind h select-pane -L
bind j select-pane -D
bind k select-pane -U
bind l select-pane -R
```

### Reload Config

```bash
# Add to .tmux.conf:
bind r source-file ~/.tmux.conf \;\
  display "Config reloaded!"

# Then reload with:
Prefix r
```

---

## 10 | Status Bar Customisation

### Status Bar Anatomy

The bar at the bottom of every tmux session has three sections:

- **Left** -- session name
- **Centre** -- window list
- **Right** -- system info (time, host, etc.)

### Minimal Dark Theme

```bash
# Colours
set -g status-style "bg=#10101a,fg=#8888a8"

# Left: session name
set -g status-left \
  "#[fg=#0a0a0f,bg=#f5a623,bold] #S "
set -g status-left-length 20

# Right: date and time
set -g status-right \
  "#[fg=#4ecb8d] %H:%M #[fg=#8888a8]%d-%b"
set -g status-right-length 30

# Window tabs
setw -g window-status-format \
  " #I:#W "
setw -g window-status-current-format \
  "#[fg=#0a0a0f,bg=#a78bfa,bold] #I:#W "
```

### Format Variables

| Variable | Value |
|----------|-------|
| `#S` | Session name |
| `#I` | Window index |
| `#W` | Window name |
| `#P` | Pane index |
| `#H` | Hostname |
| `#T` | Pane title |
| `%H:%M` | Time (24h) |
| `%d-%b` | Date (e.g., 04-Apr) |

### Pane Borders

```bash
# Active pane border colour
set -g pane-active-border-style \
  "fg=#f5a623"

# Inactive pane border
set -g pane-border-style \
  "fg=#2a2a3a"
```

---

## 11 | Mouse Mode

### Enable Mouse Support

```bash
# ~/.tmux.conf
set -g mouse on
```

With `mouse on`, you can:

- **Click** to select a pane
- **Drag** pane borders to resize
- **Scroll** to enter copy mode and browse history
- **Click** on window names in the status bar

### Copying with Mouse

When mouse mode is on, click-and-drag selects text in tmux's copy buffer, not the system clipboard. To copy to the system clipboard, hold **Shift** while selecting.

```bash
# Alternatively, pipe to clipboard
# Linux (X11):
bind -T copy-mode-vi y send -X \
  copy-pipe-and-cancel "xclip -sel clip"

# macOS:
bind -T copy-mode-vi y send -X \
  copy-pipe-and-cancel "pbcopy"
```

### Keyboard vs Mouse

| Task | Mouse | Keyboard |
|------|-------|----------|
| Select pane | Click | `Prefix Arrow keys` |
| Resize pane | Drag border | `Prefix Ctrl+arrow` |
| Scroll | Scroll wheel | `Prefix [` |
| Switch window | Click tab | `Prefix 0-9` |

### Trade-offs

- **Pro:** Intuitive for beginners, fast for occasional resizing
- **Con:** Can interfere with terminal app mouse events
- **Tip:** Most power users leave mouse **on** but primarily use keyboard shortcuts

---

## 12 | Surviving Disconnects

### The Killer Feature

tmux sessions persist independently of your terminal connection. If your SSH drops, your laptop goes to sleep, or you accidentally close a tab -- **everything keeps running**.

### SSH Workflow

```bash
# Connect to remote server
$ ssh user@server

# Start (or reattach) tmux session
$ tmux new -s work
# ... or ...
$ tmux a -t work

# Do your work...
# Connection drops? No problem.

# Reconnect later:
$ ssh user@server
$ tmux a -t work
# Everything is exactly as you left it
```

### Long-Running Processes

Perfect for tasks that take hours:

- Model training runs
- Large data migrations
- Compilation of large projects
- Running Claude Code on complex tasks
- Server processes and log monitoring

Start the process in tmux, detach with `Prefix d`, and come back whenever you want.

### Pro Tip: Auto-Attach

```bash
# Add to your .bashrc or .zshrc:
# Auto-attach to tmux on SSH login
if [ -n "$SSH_CONNECTION" ] && \
   [ -z "$TMUX" ]; then
  tmux attach -t ssh || tmux new -s ssh
fi
```

Now you **always** land in a tmux session when connecting via SSH.

---

## 13 | Claude Code in tmux

### Why tmux + Claude Code?

- **Persistence** -- Claude Code can work for hours; tmux keeps it alive if your connection drops
- **Multitasking** -- run Claude in one pane while you test, review, or code in another
- **Monitoring** -- watch Claude's output alongside build logs, test results, or git status
- **Parallel agents** -- run multiple Claude Code instances on different tasks

### Getting Started

```bash
# Create a session for your project
tmux new -s myproject -c ~/code/myproject

# Start Claude Code
claude

# Split a pane to keep a shell handy
Prefix %

# Now you have Claude on the left,
# shell on the right
```

### Recommended Layout

```
+-----------------------------+
|                  |  Shell   |
|   Claude Code    | git,tests|
|   (main pane)    |----------|
|                  |Logs/Watch|
|                  |tail -f   |
+-----------------------------+
| myproject | 0:claude* 1:shell 2:logs | 14:32 |
+-----------------------------+
```

### Useful Shortcuts While Claude Works

| Action | How |
|--------|-----|
| Switch to shell pane | `Prefix Right` |
| Zoom into Claude pane | `Prefix z` |
| Scroll Claude output | `Prefix [` |
| Detach & let it run | `Prefix d` |

---

## 14 | Headless Claude Code

### Non-Interactive Mode

Claude Code supports a **headless** (non-interactive) mode via the `-p` flag -- perfect for fire-and-forget tasks in tmux:

```bash
# Send a prompt non-interactively
claude -p "Refactor the auth module \
  to use JWT tokens"

# With auto-accept for tool use
claude -p "Add unit tests for utils.ts" \
  --allowedTools Edit,Write,Bash
```

### Fire & Forget in tmux

```bash
# Create a new window for a task
Prefix c

# Run Claude headless
claude -p "Review all Python files in \
  src/ for security issues and write \
  a report to SECURITY_REVIEW.md" \
  --allowedTools Read,Write,Glob,Grep

# Detach and let it work
Prefix d

# Come back later to check the result
tmux a
```

### Parallel Tasks

```bash
# Window 0: Claude on backend
claude -p "Add input validation \
  to all API endpoints"

# Window 1: Claude on frontend
claude -p "Add loading states \
  to all data-fetching components"

# Window 2: Claude on tests
claude -p "Write integration tests \
  for the payment flow"
```

Each window runs an **independent Claude instance**, working on a different part of the codebase simultaneously.

### Caution

- Multiple Claude instances can create **merge conflicts** if they edit the same files
- Assign each instance a **distinct area** of the codebase
- Review all changes before committing

---

## 15 | Multi-Project Workflows

### One Session per Project

```bash
# Create sessions for each project
tmux new -s api -c ~/code/api -d
tmux new -s frontend -c ~/code/app -d
tmux new -s infra -c ~/code/terraform -d

# The -d flag creates without attaching

# Now switch between them:
Prefix s   --> session picker
Prefix (   --> previous session
Prefix )   --> next session
```

Each session starts in its **own directory**, with its own windows and panes.

### Naming Convention

Keep session names short and descriptive:

- `api` -- backend REST API
- `web` -- frontend application
- `ops` -- infrastructure / DevOps
- `db` -- database migrations

### Scripted Workspace Setup

```bash
#!/bin/bash
# ~/bin/dev-start.sh

# API session
tmux new -s api -c ~/code/api -d
tmux send-keys -t api "claude" Enter

# Frontend session
tmux new -s web -c ~/code/app -d
tmux send-keys -t web "npm run dev" Enter
tmux split-window -t web -h \
  -c ~/code/app
tmux send-keys -t web "claude" Enter

# Attach to the API session
tmux attach -t api
```

Run `./dev-start.sh` once and your entire dev environment is ready.

### tmux send-keys

The `send-keys` command types text into a pane programmatically -- ideal for automation scripts that launch servers, editors, or Claude Code.

```bash
# Send to a specific session:window
tmux send-keys -t api:0 "make test" Enter
```

---

## 16 | Cheat Sheet

### Sessions

| Command | Action |
|---------|--------|
| `tmux new -s name` | New session |
| `tmux a -t name` | Attach |
| `tmux ls` | List sessions |
| `Prefix d` | Detach |
| `Prefix s` | Session picker |
| `Prefix $` | Rename session |

### Windows

| Command | Action |
|---------|--------|
| `Prefix c` | New window |
| `Prefix ,` | Rename window |
| `Prefix n` | Next window |
| `Prefix p` | Previous window |
| `Prefix 0-9` | Go to window # |
| `Prefix &` | Kill window |

### Panes

| Command | Action |
|---------|--------|
| `Prefix %` | Split vertical |
| `Prefix "` | Split horizontal |
| `Prefix Arrow keys` | Navigate |
| `Prefix z` | Zoom toggle |
| `Prefix x` | Kill pane |
| `Prefix Space` | Cycle layouts |

### Copy Mode

| Command | Action |
|---------|--------|
| `Prefix [` | Enter copy mode |
| `/ or ?` | Search fwd / back |
| `Space` | Start selection (vi) |
| `Enter` | Copy selection (vi) |
| `Prefix ]` | Paste |
| `q` | Exit copy mode |

### Command Line

| Command | Action |
|---------|--------|
| `tmux ls` | List sessions |
| `tmux a -t name` | Attach to session |
| `tmux kill-session -t x` | Kill session |
| `tmux kill-server` | Kill everything |
| `tmux send-keys` | Send input to pane |
| `Prefix :` | Command prompt |

---

## 17 | Advanced Features

### Session Groups

```bash
# Share a session across terminals
# (each can view different windows)
tmux new -s main
# In another terminal:
tmux new -t main -s viewer

# Both see the same session,
# but can look at different windows
```

### Moving Panes Between Windows

```bash
# Break pane into its own window
Prefix !

# Join a pane from another window
# (from command prompt):
Prefix :
join-pane -s 2.1  # source win:pane
join-pane -t 0    # target window
```

### Synchronise Panes

```bash
# Type in ALL panes simultaneously
Prefix :
setw synchronize-panes on

# Useful for running the same command
# on multiple servers at once

# Turn off:
setw synchronize-panes off
```

### Capture & Save Output

```bash
# Capture visible pane content
tmux capture-pane -p > output.txt

# Capture entire scrollback
tmux capture-pane -pS - > full.txt

# Pipe a pane's output to a file
tmux pipe-pane -o "cat >> ~/log.txt"
# Stop piping:
tmux pipe-pane
```

Great for saving Claude Code's responses for later review.

### Environment Variables

```bash
# Pass env vars to new sessions
tmux new -s work \
  -e "NODE_ENV=development" \
  -e "DEBUG=app:*"

# Update environment in session
tmux set-environment -t work \
  API_KEY "sk-..."

# Show session environment
tmux show-environment -t work
```

---

## 18 | Troubleshooting

### Colours Look Wrong

```bash
# Add to .tmux.conf:
set -g default-terminal "tmux-256color"
set -ag terminal-overrides \
  ",xterm-256color:RGB"

# Make sure your terminal emulator
# supports true colour (most do)
```

### Prefix Key Not Working

- Check you're pressing `Ctrl+b`, not `Ctrl+B` (no Shift)
- Make sure you **release** the prefix before pressing the command key
- Verify your config: `tmux show -g prefix`
- If in nested tmux, use `Prefix Prefix key` to send to inner session

### Nested tmux Sessions

```bash
# SSH into server with its own tmux?
# Send prefix to inner session:
Prefix Prefix key

# Or remap inner session to C-a
# and keep outer as C-b
```

### Session Won't Attach

```bash
# "sessions should be nested" error
# You're already in tmux! Either:
unset TMUX   # then attach
# or use session switching instead:
Prefix s

# "no sessions" error
tmux ls  # confirms no sessions
tmux new -s fresh  # start one
```

### Claude Code Display Issues

- If Claude Code's UI renders oddly, ensure your pane is **wide enough** (at least 80 columns)
- Use `Prefix z` to zoom the Claude pane to full screen
- Set `TERM=xterm-256color` if colours are wrong
- Increase scrollback: `set -g history-limit 50000`

### Kill a Frozen Pane

```bash
# If a pane is unresponsive:
Prefix x   --> kill-pane (confirm)

# Nuclear option:
tmux kill-pane -t 2
```

---

## 19 | Complete .tmux.conf

### Recommended Configuration

```bash
# ~/.tmux.conf -- opinionated starter

# -- Prefix --
unbind C-b
set -g prefix C-a
bind C-a send-prefix

# -- General --
set -g mouse on
set -g base-index 1
setw -g pane-base-index 1
set -g renumber-windows on
set -sg escape-time 0
set -g history-limit 50000
setw -g mode-keys vi

# -- Terminal --
set -g default-terminal "tmux-256color"
set -ag terminal-overrides \
  ",xterm-256color:RGB"

# -- Splits --
bind | split-window -h \
  -c "#{pane_current_path}"
bind - split-window -v \
  -c "#{pane_current_path}"
unbind '"'
unbind %

# -- Navigation --
bind h select-pane -L
bind j select-pane -D
bind k select-pane -U
bind l select-pane -R

# -- Resize --
bind -r H resize-pane -L 5
bind -r J resize-pane -D 5
bind -r K resize-pane -U 5
bind -r L resize-pane -R 5

# -- Reload --
bind r source-file ~/.tmux.conf \;\
  display "Config reloaded!"

# -- Status Bar --
set -g status-style "bg=#10101a,fg=#8888a8"
set -g status-left \
  "#[fg=#0a0a0f,bg=#f5a623,bold] #S "
set -g status-right \
  "#[fg=#4ecb8d] %H:%M #[fg=#8888a8]%d-%b"
setw -g window-status-format " #I:#W "
setw -g window-status-current-format \
  "#[fg=#0a0a0f,bg=#a78bfa,bold] #I:#W "

# -- Borders --
set -g pane-active-border-style \
  "fg=#f5a623"
set -g pane-border-style "fg=#2a2a3a"
```

---

## 20 | Summary & Next Steps

### What We Covered

- tmux fundamentals -- server, sessions, windows, panes
- The prefix key and core keybindings
- Session management and persistence
- Copy mode, scrollback, and searching
- Configuration and status bar theming
- Running Claude Code effectively in tmux
- Headless mode and parallel agent workflows
- Multi-project workspace automation

### Key Takeaway

tmux transforms your terminal from a single, fragile window into a **persistent, multi-pane workspace**. Combined with Claude Code, it enables powerful workflows -- fire-and-forget tasks, parallel agents, and uninterrupted long-running sessions.

### Recommended Reading

- **tmux 2** by Brian P. Hogan (Pragmatic Bookshelf)
- The official tmux wiki on GitHub
- `man tmux` -- comprehensive and well-written
- Claude Code documentation at `claude.ai`

### Practice Exercises

1. Create a named tmux session and split it into three panes
2. Detach, close your terminal, then reattach
3. Customise your `.tmux.conf` with the config from slide 19
4. Run Claude Code in one pane while running tests in another
5. Write a shell script that sets up your ideal workspace layout

Learn --> Configure --> Practice --> Master
