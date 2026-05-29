# InventoryStacks 26.1.2 Uplift - Release Notes

## Date
2026-05-28

## Scope
This release focuses on uplift readiness for Minecraft 26.1.2 (Paper API generation 26.x), including build toolchain alignment, dependency upgrades, and compatibility hardening.

## Highlights

1. Modernized build pipeline for target runtime.
- Maven compiler plugin configured.
- Compiler release updated to Java 25 to match Paper 26.1.2 classfile requirements.

2. Dependency uplift completed.
- paper-api: `26.1.2.build.66-stable`
- adventure-platform-bukkit: `4.4.1`
- adventure-text-minimessage: `5.1.1`
- XSeries: `13.7.0`
- Added annotation support dependencies for compatibility:
  - javax.annotation-api: `1.3.2`
  - jsr305: `3.0.2`

3. Version compatibility hardening.
- Server version detection now supports semantic fallback behavior.
- Unknown future versions (including 26.1.x) no longer hard-disable by default.

4. Metadata and cleanup.
- plugin.yml api-version updated to `1.20`.
- Removed unused legacy utility class and redundant shim methods.

## Build Output
Primary artifact:
- `target/inventorystacks-2.7.1-MC26.jar`

Secondary artifact:
- `target/original-inventorystacks-2.7.1-MC26.jar`

## Build Command
Use JDK 25 for this release:

```bash
JAVA_HOME=/home/jessica/.jdk/jdk-25 PATH=/home/jessica/.jdk/jdk-25/bin:$PATH mvn -q -DskipTests clean package
```

## Known Notes
- Maven may print warnings from older bundled Guava usage under newer JDKs; build still completes successfully.
- Runtime validation is still required before production rollout. Use `UPLIFT_26_1_2_RUNTIME_CHECKLIST.md`.
