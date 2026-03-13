# codex-continuer

`codexc` is a thin interactive wrapper around `codex`.

It keeps the normal Codex TTY experience, prints this banner once at startup,
and when Codex finishes a turn and returns to the input prompt it automatically
sends a continuation prompt so the session does not stall.

The trigger is heuristic on purpose:

- it waits until Codex has shown a working state first
- then it waits for the prompt to return
- autoplay only starts after you have been idle for `CODEXC_IDLE_TIMEOUT`
- if you start typing again, the idle timer is reset immediately

## Usage

```bash
./codexc --install
./codexc "fix the failing test and keep going until it passes"
./codexc --idle-timeout 15 "keep iterating unless I type again"
./codexc --with "prefer smaller diffs and run tests before stopping" "refactor this module"
./codexc -m gpt-5.4 --dangerously-bypass-approvals-and-sandbox "ship the feature"
```

For another machine, the shortest setup is:

```bash
git clone https://github.com/EdamAme-x/codex-continuer.git
cd codex-continuer
./codexc --install
codexc
```

`--with "..."` is handled by `codexc` itself.
Everything else is forwarded to `codex` unchanged.
`--install` copies the current script into a writable PATH directory, preferring
`~/.local/bin` and then `~/bin`.
`--idle-timeout <seconds>` overrides the inactivity timer for that run.

The built-in continuation message tells Codex to:

- critically evaluate its current direction
- continue if the direction is good
- slightly improve the plan if needed and then continue
- only stop when truly blocked

`--with` appends extra run-specific instructions to that continuation message.

## Environment Variables

```bash
CODEXC_IDLE_TIMEOUT=60          # seconds of no human input before autopilot starts
CODEXC_READY_DELAY=1.5          # prompt-stability delay before an auto-send
CODEXC_SUBMIT_DELAY=0.25        # wait after typing before pressing Enter
CODEXC_AUTO_DELAY=1.5           # legacy alias for CODEXC_READY_DELAY
CODEXC_MAX_AUTOCONTINUES=0      # 0 = unlimited
CODEXC_CONTINUE_PROMPT="..."    # replace the default continuation prompt
CODEXC_DEBUG=1                  # log state transitions to stderr
CODEXC_CODEX_BIN=codex          # override the underlying binary for testing
```

## Notes

- `codexc` expects to run in a real TTY, like normal `codex`.
- Press `Ctrl-C`/normal terminal keys exactly as you would in Codex.
- If stdin/stdout are not TTYs, `codexc` falls back to plain `codex`.
