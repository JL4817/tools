# Codex CLI - Terminal Commands

These commands are run directly in your normal terminal (bash, zsh, PowerShell, etc.), before or outside an active Codex session.

---

## View Available Models

Show every model available to your Codex installation.

```bash
codex debug models
```

Show only the models bundled with your current installation.

```bash
codex debug models --bundled
```

---

## Start Codex

Start an interactive Codex session.

```bash
codex
```

---

## Start Codex With a Prompt

Instead of entering interactive mode, immediately give Codex a task.

```bash
codex "Explain this project"
```

Example:

```bash
codex "Refactor my authentication module"
```

---

## Start Codex Using a Specific Model

Specify the model when launching.

```bash
codex --model gpt-5.4
```

or

```bash
codex -m gpt-5.4
```

Example:

```bash
codex -m gpt-5.4 "Refactor the auth module"
```

---

## Set Your Default Model

Edit your configuration file.

Location:

```text
~/.codex/config.toml
```

Example:

```toml
model = "gpt-5.4"
```

Now every new Codex session uses this model unless you override it.

---

## Official Model List

Current official models:

https://developers.openai.com/codex/models

Note:
Model names change over time, so use

```bash
codex debug models
```

to see what is actually available on your machine.
