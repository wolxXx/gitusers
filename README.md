# gitusers

`git-id` is a small Bash tool for managing multiple Git identities (`user.name` / `user.email`).
Save your identities once, then switch between them per repository or globally with a single command.

## Requirements

- Bash
- Git

## Installation

From GitHub:

```sh
curl -fsSLO https://raw.githubusercontent.com/wolxXx/gitusers/refs/heads/main/git-id && sudo install -m 755 git-id /usr/local/bin/git-id && rm git-id
```

From the repository root:

```sh
sudo install -m 755 git-id /usr/local/bin/git-id
```

That's it – it now works as a regular Git subcommand:

```sh
git id
```

Git automatically runs any executable named `git-<name>` on your `PATH` as `git <name>`, so no alias or extra configuration is needed.

## Usage

```sh
git id                              # Show status: repo, local/global/active identity + saved list
git id list                         # List saved identities
git id add                          # Add interactively (current git config is pre-filled)
git id add work "Max Muster" max@company.com
git id use work                     # Inside a repo: local; outside: global
git id use 2 -g                     # Select by number, explicitly global (-l = local)
git id use                          # Interactive selection
git id work                         # Shorthand for "git id use work"
git id rm work                      # Delete with confirmation (-f skips it)
git id unset [-g|-l]                # Remove user.name/user.email from the config
git id help                         # Show help
```

### Commands

| Command                          | Aliases                     | Description                                                     |
|----------------------------------|-----------------------------|-----------------------------------------------------------------|
| `git id`                         | `status`, `st`, `s`         | Show the current identity and all saved identities              |
| `list`                           | `ls`, `l`                   | List saved identities                                           |
| `add [alias] [name] [email]`     | `a`, `new`                  | Add an identity; missing values are prompted for                |
| `use [alias\|nr] [-g\|-l]`       | `u`, `set`                  | Apply an identity; without an argument, choose from a list      |
| `rm <alias\|nr> [-f]`            | `remove`, `del`, `delete`   | Delete an identity                                              |
| `unset [-g\|-l]`                 |                             | Remove `user.name` / `user.email` from the config               |
| `help`                           | `-h`, `--help`              | Show help                                                       |

### Scope

- `-l`, `--local`: write to the current repository's config (default inside a repo).
- `-g`, `--global`: write to the global config (default outside a repo).

## Example status output

```text
Current Git identity
  Repository: /home/…/repo
  Local:      Max M <max@private.com>
  Global:     Max Muster <max@company.com>
  Active:     Max M <max@private.com>  (local)

Saved identities (~/.git-identities)
     1) work     Max Muster <max@company.com>  [global]
  ●  2) private  Max M <max@private.com>  [local]
```

The `●` marks the identity that is currently active.

## Details

### Storage

Identities are stored in `~/.git-identities`, one per line, in the format:

```text
alias<TAB>name<TAB>email
```

The file is created with permissions `600` and can be edited by hand. Lines that start with `#` are ignored.
Set the `GIT_ID_FILE` environment variable to use a different path:

```sh
export GIT_ID_FILE="$HOME/.config/git-identities"
```

### Validation

- **Alias:** may only contain `A-Z a-z 0-9 . _ -`. It must not consist of digits only, because it could be confused with a list number, and it must be unique.
- **Email:** only a rough format check (`something@something`).
- **Name/email:** must not contain tabs or newlines.

### Status hint

If the active identity is not saved, the status output says so and suggests `git id add`.

### Editing

There is no edit command. To change an identity, delete it with `rm` and add it again with `add`, or edit the storage file directly.
