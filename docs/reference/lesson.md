# The lesson

`index.md` is the lesson, and the lab page renders it as Markdown each time the lab opens. Because the file isn't copied onto the disk, you only need to refresh the repository after changing a published lesson, and the next person to open the lab sees the new text.

In the shell you're root, the administrator on the machine, and your home directory is `/root`.

## Commands

A fenced block tagged with one of these languages gets a Run button and a Copy button:

`shell`, `bash`, `sh`, `zsh`, `ksh`

Run sends each line to the shell, as if the learner had typed it and pressed Enter.

A block with any other tag, including `console`, `json`, `yaml`, and `yml`, or with no tag at all, is shown as text that can be copied but has no Run button. Use `console` for output you want the learner to read rather than run. An `env` fence is separate: it runs a command and keeps the output in the shell, as [Capture a shell value](#capture-a-shell-value) describes.

### Capture a shell value

An `env` fence runs a command and saves its output in the shell under the name you give.

````markdown
```env var="GW_IP" interpolated="true"
kubectl get gtw my-gateway -o jsonpath='{.status.addresses[0].value}'
```
````

The learner sees `GW_IP`. Run exports that name in the current shell. The page shows the value, or that the command came back empty. A later shell fence can use `$GW_IP`.

`interpolated="true"` is required. Add `panel="name"` on that same line when the export belongs in one pane, the same way a shell fence does. The value is not written to a file. It dies with that shell. A new shell does not have it.

When the command itself is what you are teaching, keep a shell fence.

## The Complete lab button

On the hosted site, Complete lab starts disabled. It turns on when the learner has had the lab page open for 2 minutes. The clock starts when they first open the page, not when the lab machine starts, and a reload or reconnect keeps the time already spent.

The disabled button shows no hint. The learner sees a disabled Complete lab button, with no time left. The page header shows how long the learner has been in the lab, which is not the time left on the gate. The button turns on by itself, without a reload, when the time runs out. If your lesson should tell learners how to finish, say it in the lesson.

The check is a nudge, not a hard stop. A lock sits beside Complete lab. Until the 2 minutes pass, the learner can open the lock and complete the lab early. That ends the lab and frees the machine. Escape, a click elsewhere, or a second click on the lock closes it again. When the time runs out, the lock opens and stays open, and Complete lab turns on. There is no setting to turn the lock off.

Admin sessions on the hosted site get the same check and the same lock. Trylab on your own machine skips both, so you will not see the gate there. To see it, run the lab on the hosted site as an admin.

## Links

A link in the lesson opens in a new tab. A `bat:` link shows a file in the terminal instead.

## Showing a file

A link whose target is `bat:<path>` shows that file in the terminal. The lesson does not show the command.

```markdown
Read [the route](bat:data/route.yaml) before you apply it.

[Inspect the route](bat:data/route.yaml)

[Inspect the config](bat:~/.config/agentgateway/config.yaml){panel="client"}
```

A link inside a sentence is an ordinary link, and hovering it shows the path. A link that is its own paragraph, or the whole of an important note, is one row: the path on the left, and "view" with an eye on the right. When the link names a pane, that name is shown in the accent color between the path and "view", the same way a command block shows its `panel`. The author's words are the row's tooltip. Hovering the row highlights it, and the eye opens. One click on either shows the file in the terminal. When the note is only that link, the row replaces the note.

Use an inline `bat:` link when you mention a file in passing, or refer to a file a second time, so learners can look at it without scrolling back to the step that first showed it.

`bat:data/route.yaml` types the command in the first terminal on the page. `[Inspect](bat:data/route.yaml){panel="client"}` types it in the pane named `client`. The attribute comes right after the link, the same way a key link names its pane, and the pane has to belong to a layout link above it, the same way a shell block's `panel` does. The older form that put the pane inside the target, `bat:client:data/route.yaml`, is refused, and the message says where the pane goes now.

The path is a file name. Pieces are separated by `/` and use letters, digits, and `-`, `_`, or `.`. A piece that is `.` or `..` is refused, and so is a path that starts with `/`, ends with `/`, contains `//`, or contains `:`. A path that starts with `~/` is the home folder, as in `~/.config/agentgateway/config.yaml`. A `~` anywhere else is refused. Any other path is relative to the folder the terminal is in. A terminal starts in the home folder, and the lab's `data/` folder is `~/data`, so `data/route.yaml` names a file the lab ships. If `setup.sh` copies the files somewhere else, such as into the home folder, use the path they end up at. The lab does not check that the file is there. An example inside a fenced block or inline code is not a link. A command that only uses the pager, such as `kubectl get … | bat`, stays a fenced block.

## A note

A quote whose first line is exactly `[!IMPORTANT]` is drawn as a labeled note. The marker must be on its own line, and no other marker is recognized.

```markdown
> [!IMPORTANT]
> The server is already running.
```

## Services

A service in `config.yaml` can appear in the bar above the lesson. It can also be a link in the lesson, at the step where the learner should open it. Both open in a new tab.

The short name comes from the service name. The name is lowercased. Spaces, underscores, and hyphens become a single hyphen. Anything else is left out. `Agentgateway UI` is `agentgateway-ui`.

```markdown
Open the [Agentgateway UI](service:agentgateway-ui).
```

A path after the name opens a page under the service's path. When that path is `/ui`, this opens `/ui/logs`:

```markdown
Open the [logs](service:agentgateway-ui/logs).
```

The added path is one or more pieces separated by `/`. Each piece uses letters, digits, and `-`, `_`, `.`, or `~`. A piece that is `.` or `..` is refused. A slash at the end is dropped: `service:agentgateway-ui/logs/` opens `/ui/logs`, and `service:agentgateway-ui/` is refused.

A question mark adds a query string, and a hash adds a fragment. When the service path is `/ui`, this opens `/ui/llm/analytics?groupBy=group&metric=cost`:

```markdown
Open the [cost by group](service:agentgateway-ui/llm/analytics?groupBy=group&metric=cost).
```

The query and the fragment use letters, digits, and `-`, `_`, `.`, `~`, `,`, `:`, `=`, `&`, `%`, `+`. A fragment may also contain `?`. Write those characters as themselves. A `?` or `#` with nothing after it is refused.

A link that doesn't match a service stops the lab from opening, and so does a path, query, or fragment the lab refuses.

`header: false` on that service keeps it out of the bar. Leave the flag out, and the bar shows the link from the start. The bar link opens the path from `config.yaml`, with nothing added. An example inside a fenced code block is not a link.

Inside the shell, you can reach that same port at `127.0.0.1`, because that's the machine you're on.

## Panes

The terminal on the lab page runs [Zellij](https://zellij.dev) when the machine has it, and the base disk does. A lesson can split that terminal into named panes and send a command, or a key, to one pane by name. A machine without Zellij gets an ordinary shell.

Three pieces work together:

- A layout file in `data/` that names the panes.
- A layout link in the lesson that opens that layout.
- A `panel="..."` on a shell block, or on a key link, that picks a pane by its name.

### The layout file

A layout is a Zellij layout file at `data/<name>.kdl`. Each pane gets its name with Zellij's own `name="..."` attribute. This is `agw-guardrails/data/two-pane.kdl` in the labs repository:

```kdl
layout {
  pane name="gateway" size="40%" {
    focus true
  }
  pane name="client" size="60%" {
  }
}
```

The layout file name uses letters, digits, hyphens, and underscores, and starts with a letter or a digit. Within one file, each pane name appears once and is never empty.

### The layout link

A link whose target is `zellij:<name>` opens `data/<name>.kdl` in the terminal:

```markdown
> [!IMPORTANT]
> [Configure a two-panel layout](zellij:two-pane) for the terminal.
```

Clicking it replaces the terminal's layout with that file. It doesn't start a second session.

### A command for one pane

`pane name="gateway"` is Zellij's syntax, and it lives in the `.kdl` file. `panel="gateway"` is the lesson's syntax, and it goes after the language on a shell block. The two names have to match.

````markdown
```shell panel="gateway"
agentgateway -f config-llm.yaml
```
````

Run types each line into that pane and presses Enter. Only a shell block (`shell`, `bash`, `sh`, `zsh`, `ksh`) takes a `panel`. A block without one runs in the terminal as before.

### A key for one pane

A link whose target is `key:<chord>`, followed by `{panel="..."}`, is a button that presses that key in that pane. It doesn't press Enter afterward.

```markdown
> [!IMPORTANT]
> [Stop agentgateway](key:Ctrl+C){panel="gateway"}
```

A chord is optional modifiers (`Ctrl`, `Alt`, `Shift`, `Super`) and one key: a letter, a digit, `Enter`, `Tab`, `Esc`, `Backspace`, `Space`, `Up`, `Down`, `Left`, `Right`, `Delete`, or `F1` to `F12`. `Ctrl+C`, `Ctrl-C`, and `Ctrl C` are the same chord. A key link always needs its `{panel="..."}`.

### What the lab checks when it opens

The lesson is read top to bottom. Each `panel` belongs to the closest layout link above it. The lab doesn't open, and the message names `index.md`, when:

- a `panel` has no layout link above it (`panel "gateway" needs a layout link above it`),
- the layout above it has no pane with that name (`layout two-pane has no pane named "gateway"`),
- a layout link names a file that isn't there (`layout two-pane needs data/two-pane.kdl`),
- a `panel` is on a block that isn't a shell block, or
- a key link has no `{panel="..."}` or names an unknown key, or
- a `bat:` link has no path, or a path the lab refuses (`file link "…" has a bad path`), or
- a `bat:` link still names its pane inside the target, as `bat:client:data/route.yaml`; write `bat:data/route.yaml` and then `{panel="client"}` right after the link, or
- a `bat:` link names a pane the layout above it does not have.

An example inside a fenced block or inline code is not checked, so you can show the syntax in a lesson.

The check reads the lesson, not what the learner did. A learner who skips the layout link and then presses Run on a `panel` block gets `no pane named "gateway". Open its layout first`. Put the layout link before the first block that uses a `panel`, and mark it as a note so it stands out.

See `agw-guardrails` in the labs repository for a whole lesson written this way.
