# InventoryStacks

InventoryStacks lets you customize max stack sizes per item and keep inventory behavior stable across normal gameplay interactions.

This repository is currently uplifted for the MC26 line.

## Release Line and Strict Compatibility

### Current build in this repository

- Plugin version: `2.7.2-MC26`
- Target server API: Paper `26.1.2.build.66-stable`
- Required Java runtime and build toolchain: Java `25`

### Hard requirements

- You must run this build on a Paper-compatible `26.1.x` generation server.
- You must run the server on Java `25`.

### Not supported by this build

- 1.21.x servers
- Java 21 runtimes

If you need 1.21.x support, use that separately maintained release located here:
https://www.spigotmc.org/resources/%E2%AD%90-inventorystacks-%E2%AD%90-change-default-64-stack-size-for-any-item-1-7-1-21-11.116877/?utm_source=chatgpt.com

## Features

- Per-item custom max stack size configuration.
- Regex-based item mapping in config (`items:` keys are regex patterns).
- Optional global stack size for all items, with whitelist exemptions.
- Modern stack sizing path using `ItemMeta#setMaxStackSize` where available.
- Optional legacy reflection path toggle (`use-legacy-reflection`).
- Automatic cleanup of stale max-stack metadata (`auto-stack-cleanup`).
- Runtime reload of config values (`/stacks reload` or `/inventorystacks reload`).
- Stack helper command for players (`/stack`, `/stack HAND`, `/stack ALL`).
- MiniMessage output support when enabled.

## Commands and Permissions

### Commands

- `/stack [HAND|ALL]`
- `/stacks reload` (also accepts `rl`)
- `/inventorystacks reload` (alias to reload executor)

### Permissions

- `STACKS.*` (full access)
- `STACKS.COMMAND` (use `/stack`)
- `STACKS.RELOAD` (use reload commands)

## Installation (Strict)

1. Confirm your server is on Paper-compatible `26.1.x` generation.
2. Confirm Java `25` is installed and used by your server startup.
3. Build or use the packaged artifact:
	- `target/inventorystacks-2.7.2-MC26.jar`
4. Place the jar into your server `plugins` folder.
5. Start the server once to generate default config files.
6. Edit `config.yml` and restart or run `/stacks reload`.

## Build from Source

Use JDK 25:

```bash
JAVA_HOME=/path/to/your/jdk-25 PATH=/path/to/your/jdk-25/bin:$PATH mvn -q -DskipTests clean package
```

Expected artifacts:

- `target/inventorystacks-2.7.2-MC26.jar`
- `target/original-inventorystacks-2.7.2-MC26.jar`

## Key Configuration

- `items:` per-item stack configuration.
- `max-stack-for-all-items.enabled` global override mode.
- `max-stack-for-all-items.whitelist` items excluded from global override.
- `use-legacy-reflection` force legacy path on modern versions.
- `auto-stack-cleanup` remove stale custom metadata from non-configured items.
- `item-change-delay` scheduling delay for bucket/consume/damage updates.
- `stack-command.enabled` and `stack-command.default-stack-type` command behavior.

## Release Status

This build is now promoted for release under the MC26 line.

Validation details are tracked in `UPLIFT_26_1_2_RUNTIME_CHECKLIST.md`.
