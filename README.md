# Agents for claude-swap

An Omarchy bar plugin for people with more than one Claude account in [claude-swap](https://github.com/realiti4/claude-swap). It replaces the stock Agents panel. It shows the usage limits of every account, and it switches Claude Code between the accounts from the keyboard.

The plugin is a copy of the Omarchy Agents panel (`omarchy.agents`) with these changes:

- Each claude-swap account has its own tab. The tab name is the alias from `cswap alias`. If an account has no alias, the tab name is the part of the email address before the @. If two accounts have the same part before the @, the tabs show the full email addresses.
- The Claude tabs stay in the claude-swap account order. Key `1` always shows account 1, also after a switch.
- The active account shows `active` next to its plan.
- An inactive account has a switch command. The switch needs a second key press or click, so a look at the usage never switches the account.

Codex, Fireworks, and the other stock tabs stay the same.

## Requirements

- Omarchy 4 with the Agents panel.
- claude-swap with two or more accounts:

  ```
  uv tool install claude-swap
  cswap add
  ```

  Log in to Claude Code with each account, and run `cswap add` for each account.
- `python3`.

## Install

```
omarchy plugin add https://github.com/jasasonc/omarchy-cswap-agents.git --enable
```

The plugin takes the place of `omarchy.agents` in the bar. Do not enable both plugins at the same time. They use the same panel commands and the same usage folder.

To give the tabs short names, set aliases:

```
cswap alias 1 work
cswap alias 2 personal
```

## Hotkey

Omarchy has no default key for the Agents panel. To open the panel with `Super+Ctrl+U`, add this line to `~/.config/hypr/bindings.lua`:

```lua
o.bind("SUPER + CTRL + U", "Claude usage", "omarchy-shell omarchy.agents toggle")
```

## Keys in the panel

| Key | Action |
|---|---|
| `h`, `l`, or `1` to `9` | Show another account |
| `s`, then `s` again | Switch Claude Code to the shown account |
| `j`, `k` | Scroll |
| `r` or `Enter` | Refresh the numbers |
| `Esc` | Close the panel |

After the first `s`, the switch command waits 3 seconds for the second `s`. If you change the tab or close the panel, the switch command stops. There is no mouse action for the switch.

After a switch, a notification shows the result. Open Claude Code sessions use the new account from their next message.

## How it works

- `bin/cswap-omarchy` runs `cswap list --json`. It writes one usage record for each inactive account to `~/.local/state/omarchy/agents/usage/cswap-<name>.json`. The panel shows each record as a tab.
- The stock Claude collector writes the record of the active account. The plugin gives that tab the name of the active account, from `~/.local/state/cswap-omarchy/active.json`.
- The plugin runs `bin/cswap-omarchy` when the shell starts, every 3 minutes, when the panel opens, and after a switch. claude-swap keeps its own usage cache, so the plugin sends no more usage requests than claude-swap does.
- If claude-swap cannot read the usage of an account, the tab shows the cause, for example `Sign-in expired`, and the last known numbers.

## Update and remove

```
omarchy plugin update cswap.agents
```

To go back to the stock panel:

```
omarchy plugin enable omarchy.agents
omarchy plugin remove cswap.agents
rm -f ~/.local/state/omarchy/agents/usage/cswap-*.json
rm -rf ~/.local/state/cswap-omarchy
```

## License

MIT. The panel code comes from the Omarchy Agents plugin by David Heinemeier Hansson, also under MIT.
