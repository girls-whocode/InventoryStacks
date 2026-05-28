# Runtime Checklist (Minecraft 26.1.2)

Use this checklist after installing the newly built plugin jar.

## Environment

- [X] Server is running Minecraft 26.1.2 (Paper or compatible)
- [X] Plugin starts without errors in console
- [X] No reflection setup errors during startup
- [X] Config is loaded as expected

## Core Stack Behavior

- [X] Custom stack size applies to configured item (example: POTION 16)
- [ ] Non-configured item keeps vanilla max stack size
- [ ] /stack command (HAND) behaves correctly
- [ ] /stack command (ALL) behaves correctly
- [X] /stacks reload applies changes after config edit

## Inventory and Interaction Flows

- [X] Inventory click merge/split behaves correctly with custom stack sizes
- [ ] Hopper transfer (InventoryMoveItemEvent) respects custom stack sizes
- [X] Item pickup by player/entity respects custom stack sizes
- [ ] Furnace smelt output respects custom stack sizes
- [ ] Dispenser output respects custom stack sizes

## Consumables and Buckets

- [X] Milk bucket consume returns bucket as expected
- [X] Soup/stew consume returns bowl as expected
- [X] Bucket empty behavior updates hand stack correctly

## Damageable Items

- [ ] Damageable item stack behavior is stable
- [ ] No duplication or durability anomalies observed

## Modern vs Legacy Mode

- [X] Default mode (use-legacy-reflection: false) is stable
- [ ] Optional legacy mode test completed (if needed)
- [ ] In legacy mode, no critical regressions observed

## Folia/Paper Scheduling

- [ ] Delayed item update tasks execute correctly
- [ ] No scheduler-related exceptions under load

## Cleanup Behavior

- [ ] auto-stack-cleanup true removes stale custom max-stack metadata
- [ ] auto-stack-cleanup false preserves custom metadata as expected

## Logging and Stability

- [X] No repeating warnings/errors in normal gameplay loop
- [X] No severe errors after 30+ minutes of mixed gameplay

## Sign-off

- [ ] Runtime validation complete
- [ ] Build approved for release candidate tagging
