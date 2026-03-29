# tmux-kube-context

A simple tool to find kubeconfig files in ~/.kube/config.d/ using fzf with tmux integration, and open them in tmux sessions with the KUBECONFIG variable set.

> **Caution**  
> THIS TOOL IS AI GENERATED
> 
> While I have tested it, read through the code and am using it myself, I do not guarantee anything about this tool. It seems to work quite well and appears like it is somewhat stable, but if it blows up, it blows up.

## Features

- **Find Kubeconfig Files**: Searches ~/.kube/config.d/ for kubeconfig files
- **Interactive Selection**: Uses fzf with tmux integration (`fzf --tmux`) for selection
- **Session Management**: Creates a new tmux session or switches to an existing one for the selected kubeconfig
- **KUBECONFIG Variable**: Automatically sets the KUBECONFIG environment variable to the selected file path
- **Session Naming**: Sessions are named with `[KUBERNETES]` prefix followed by the filename
- **Startup Hook**: Optional startup hook command (e.g., to run a nix-shell)

## Requirements

- `bash`
- `tmux`
- `fzf`

### Installation on common systems

**Debian/Ubuntu:**
```bash
sudo apt install tmux fzf
```

**Arch Linux:**
```bash
sudo pacman -S tmux fzf
```

**macOS (Homebrew):**
```bash
brew install tmux fzf
```

## Installation

1. Clone this repository or download the `tmux-kube-context` script
2. Make it executable: `chmod +x tmux-kube-context`
3. Move it to a directory in your PATH, e.g., `mv tmux-kube-context ~/.local/bin/`

## Configuration

Copy the example config file to your config directory:

```bash
mkdir -p ~/.config/tmux-kube-context
cp config.example ~/.config/tmux-kube-context/config
```

Then edit the config file to customize your settings.

### Configuration Options

```bash
# Optional: Command to run when creating a new session (e.g., "nix-shell", "exec zsh")
# Leave empty for default behavior (start shell with KUBECONFIG set)
STARTUP_HOOK=""
```

## Usage

### Interactive Mode (Default)

```bash
tmux-kube-context
```

This will:
1. Find all kubeconfig files in ~/.kube/config.d/
2. Present an fzf selection interface (in tmux popup) showing just the filenames
3. On selection, create a new tmux session or switch to an existing one
4. Session name will be `[KUBERNETES]_<filename>` (with special chars replaced)
5. Automatically sets KUBECONFIG environment variable to the selected file path
6. Optionally runs a startup hook command if configured

### List Kubeconfig Files

```bash
tmux-kube-context --list
```

Lists all kubeconfig files in ~/.kube/config.d/ without opening them.

### Help

```bash
tmux-kube-context --help
```

## Tmux Integration

### Bind to a tmux key

Add to your `~/.tmux.conf`:

```bash
bind-key k display-popup -E -w 80% -h 60% "tmux-kube-context"
```

Now press `prefix + k` to launch the finder in a popup.

### Shell alias

Add to your `~/.bashrc` or `~/.zshrc`:

```bash
alias tkc='tmux-kube-context'
```

## How It Works

1. The script searches for files in `~/.kube/config.d/`
2. When you select a file, it creates a tmux session named `[KUBERNETES]_<filename>`
3. In that session, it sets the `KUBECONFIG` environment variable to the full path of the selected file at the session level
4. **All windows** created within this tmux session will automatically have access to the same `KUBECONFIG` variable
5. If configured, it runs the startup hook command (e.g., `nix-shell`)
6. Otherwise, it starts your default shell with the KUBECONFIG variable set

This is particularly useful for:
- Switching between multiple Kubernetes clusters/contexts
- Keeping different kubeconfig files isolated in separate tmux sessions
- Quickly accessing cluster-specific configurations
- Running cluster-specific environments (e.g., with nix-shell)
- Opening multiple windows in the same session to work with the same Kubernetes context

## Example Workflow

1. Store your kubeconfig files in `~/.kube/config.d/`:
   ```
   ~/.kube/config.d/
   ├── production.yaml
   ├── staging.yaml
   └── development.yaml
   ```

2. Run `tmux-kube-context`

3. Select `production.yaml` from the fzf menu

4. A new tmux session `[KUBERNETES]_production_yaml` is created with `KUBECONFIG=~/.kube/config.d/production.yaml`

5. Run kubectl commands which will use the production config

## License

MIT License - see LICENSE file for details
