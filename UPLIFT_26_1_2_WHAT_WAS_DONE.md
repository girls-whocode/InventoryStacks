# InventoryStacks Uplift Log (Target: Minecraft 26.1.2)

## Summary

This document tracks the uplift and modernization work performed to bring InventoryStacks forward to support Minecraft/Paper 26.1.2.

---

## Session: 2026-05-28

### Completed Work

#### 1. Updated Maven Compiler Configuration for Java 25

**File:** `pom.xml`

**Changes:**

* Added:

  ```xml
  <maven.compiler.release>25</maven.compiler.release>
  ```
* Added modern `maven-compiler-plugin` configuration using:

  ```xml
  <release>${maven.compiler.release}</release>
  ```

**Reason:**
The project was defaulting to extremely old Java source/target levels which caused immediate build failures on modern JDKs.

---

#### 2. Reworked Version Detection for Modern Server Generations

**File:** `src/main/java/com/codingguru/inventorystacks/handlers/ItemHandler.java`

**Changes:**

* Removed rigid 1.20/1.21 version string handling.
* Replaced it with semantic version parsing and fallback logic.
* Added:

  ```java
  fallbackBySemver(int major, int minor)
  ```
* Any version >= 1.21 now routes through the modern compatibility path instead of hard-disabling the plugin.

**Reason:**
The plugin was auto-disabling itself on newer server generations, including 26.1.2, simply because the version string was unknown.

---

#### 3. Added Temporary Compatibility Shim Methods

**File:** `src/main/java/com/codingguru/inventorystacks/handlers/ItemHandler.java`

**Changes:**
Added deprecated shim methods:

* `getCachedMaterialSizes()`
* `cacheMaterial(XMaterial, int)`
* `applyStackSizeToNmsItem(Object, int)`

**Reason:**
Several older classes still referenced APIs that no longer existed in `ItemHandler`. These shims stabilized the codebase and allowed successful compilation without altering runtime behavior.

---

#### 4. Updated Plugin Metadata Baseline

**File:** `src/main/resources/plugin.yml`

**Changes:**

* Updated:

  ```yaml
  api-version: 1.13
  ```

  to:

  ```yaml
  api-version: 1.20
  ```

**Reason:**
Align plugin metadata with modern Paper API expectations.

---

#### 5. Dependency Modernization for 26.1.2 Runtime

**File:** `pom.xml`

### Updated Dependencies

| Dependency                   | Old Version            | New Version              |
| ---------------------------- | ---------------------- | ------------------------ |
| `paper-api`                  | `1.21.9-R0.1-SNAPSHOT` | `26.1.2.build.66-stable` |
| `adventure-platform-bukkit`  | `4.3.4`                | `4.4.1`                  |
| `adventure-text-minimessage` | `4.17.0`               | `5.1.1`                  |
| `XSeries`                    | `13.6.0`               | `13.7.0`                 |

**Reason:**
Move the project forward to the current Paper runtime and modern dependency ecosystem.

---

## Validation Performed

### 1. Initial Compile Validation

**Command:**

```bash
mvn -q -DskipTests compile
```

**Result:**
Successful compile with no errors.

---

### 2. Post-Metadata Validation

**Command:**

```bash
mvn -q -DskipTests compile
```

**Result:**
Successful compile after plugin metadata updates.

---

### 3. Dependency Upgrade Discovery

**Command:**

```bash
mvn versions:display-dependency-updates
```

**Result:**
Discovered newer compatible dependencies including:

```text
paper-api -> 26.1.2.build.66-stable
```

---

### 4. Post-Upgrade Compile Validation

**Command:**

```bash
mvn -DskipTests compile
```

**Result:**
Successful compile after dependency uplift.

---

### 5. Cleanup of Redundant Compatibility Code

**Files:**

* `ItemHandler.java`
* `ReflectionUtil.java`

**Changes:**
Removed obsolete compatibility methods:

* `cacheMaterial(XMaterial, int)`
* `applyStackSizeToNmsItem(Object, int)`

Deleted unused:

* `ReflectionUtil`

**Reason:**
These paths were no longer used by runtime code and only increased maintenance complexity.

---

### 6. Java 25 Runtime Compatibility Resolution

#### Issue Encountered

Paper `26.1.2.build.66-stable` ships as:

```text
classfile version 69
```

Building under Java 21 failed with:

```text
class file has wrong version 69.0, should be 65.0
```

#### Resolution

* Updated project compiler target to Java 25.
* Built using local JDK 25:

  ```bash
  JAVA_HOME=/home/jessica/.jdk/jdk-25
  ```

Added missing annotation dependencies required by modern `XSeries` handling:

* `javax.annotation-api:1.3.2`
* `jsr305:3.0.2`

---

### 7. Packaging Validation

**Command:**

```bash
JAVA_HOME=/home/jessica/.jdk/jdk-25 PATH=/home/jessica/.jdk/jdk-25/bin:$PATH mvn -q -DskipTests clean package
```

**Result:**
Successful package build.

Generated artifacts:

* `target/inventorystacks-2.7.2-MC26.jar`
* `target/original-inventorystacks-2.7.2-MC26.jar`

---

# Current Status

## Session: 2026-05-29

### Completed Work

#### 1. Released Maintenance Patch 2.7.2-MC26

**Files:**

* `build.gradle.kts`
* `pom.xml`
* `src/main/resources/plugin.yml`
* `README.md`
* `UPLIFT_26_1_2_RELEASE_NOTES.md`

**Changes:**

* Updated project version from `2.7.1-MC26` to `2.7.2-MC26`.
* Updated documented build artifact names to the new version.

#### 2. Made Java 25 the Default WSL Java for Jessica

**Files:**

* `/home/jessica/.profile`
* `/home/jessica/.bashrc`

**Changes:**

* Exported:

  ```bash
  JAVA_HOME=/home/jessica/.jdk/jdk-25
  ```

* Prepended:

  ```bash
  /home/jessica/.jdk/jdk-25/bin
  ```

  to `PATH`.

**Reason:**
Minecraft/Paper 26.1.2 requires Java 25 classfile compatibility.

#### 3. Aligned Gradle Packaging with the Maven Shaded Jar

**Files:**

* `build.gradle.kts`
* `gradle.properties`
* `gradle/libs.versions.toml`
* `.github/workflows/gradle.yml`
* `/home/jessica/.gradle/gradle.properties`

**Changes:**

* Added the Shadow plugin to the Gradle build.
* Configured `shadowJar` as the primary Gradle artifact.
* Disabled the plain Gradle jar so CI upload only publishes the dependency-inclusive plugin jar.
* Mirrored the Maven shade exclusions for trimmed XSeries packaging.
* Configured GitHub Actions to write hosted Java 21 and Java 25 paths to runner-local `~/.gradle/gradle.properties`.
* Configured Jessica's local Gradle user properties to run Gradle on local Java 21 while resolving local Java 25 as the compile toolchain.

**Reason:**
CI uploaded `build/libs/*.jar`, but Gradle previously produced a plain jar without bundled runtime dependencies.
Gradle 8.14.3's Kotlin DSL bootstrap does not parse the local Java `25.0.2` runtime version cleanly, so the build process must run on Java 21 and compile with the Java 25 toolchain. The Java 21 runtime path is intentionally not committed in project `gradle.properties` because GitHub-hosted runners use different installation paths.

#### 4. Fixed MiniMessage Reload Lifecycle

**Files:**

* `InventoryStacks.java`
* `ReloadCmd.java`

**Changes:**

* Added `reloadMessaging()` to close and recreate `BukkitAudiences` when config is reloaded.
* Added `onDisable()` cleanup for `BukkitAudiences`.

**Reason:**
Toggling `use-mini-message` on and then running `/stacks reload` could leave `BukkitAudiences` null and cause message delivery failures.

#### 5. Restored Damageable Shift-Merge Protection Setting

**Files:**

* `listeners/correction/InventoryClick.java`
* `listeners/InventoryClick.java`

**Changes:**

* Corrected the listener to read `prevent-shift-combining-damageable-items`.
* Kept fallback support for the older misspelled/internal key.

**Reason:**
The config default enabled `prevent-shift-combining-damageable-items`, but the listener checked a different key, so the protection was disabled by default.

#### 6. Hardened Delayed Player Tasks

**Files:**

* `ChangeItemInHandTask.java`
* `ChangeItemInHandWithItemTask.java`
* `DamageItemTask.java`
* `InventoryUpdateTask.java`

**Changes:**

* Added null and online checks before delayed tasks mutate player inventories.

**Reason:**
Delayed work could run after a player disconnected, causing null pointer failures.

#### 7. Removed Explicit Folia Support Declaration

**File:**

* `src/main/resources/plugin.yml`

**Changes:**

* Removed `folia-supported: true`.

**Reason:**
The current delayed item/inventory mutations are not fully region/entity scheduler safe. Folia support should be re-enabled only after a dedicated scheduler redesign and live Folia validation.

#### 8. Stopped Tracking Generated Gradle/Build Cache Files

**Files:**

* `.gradle/**`
* `build/**`

**Changes:**

* Removed generated Gradle/build cache files from Git tracking while leaving them ignored by `.gitignore`.

**Reason:**
Tracked generated files caused routine build attempts to dirty the repository.

### Validation Performed

#### 1. WSL Java Default Validation

**Command:**

```bash
bash -lc "declare -p JAVA_HOME; command -v java; java -version"
```

**Result:**

* `JAVA_HOME` resolves to `/home/jessica/.jdk/jdk-25`.
* `java` resolves to `/home/jessica/.jdk/jdk-25/bin/java`.
* Runtime reports Temurin `25.0.2`.

#### 2. Gradle Build Validation

**Command:**

```bash
./gradlew clean build --no-daemon
```

**Result:**
Successful build.

Generated artifact:

* `build/libs/inventorystacks-2.7.2-MC26.jar`

Confirmed shaded dependencies are present in the Gradle jar, including:

* `com/cryptomorin/xseries/XMaterial.class`
* `net/kyori/adventure/platform/bukkit/BukkitAudiences.class`

#### 3. Maven Package Validation

**Command:**

```bash
mvn -q -DskipTests clean package
```

**Result:**
Successful package build.

Generated artifacts:

* `target/inventorystacks-2.7.2-MC26.jar`
* `target/original-inventorystacks-2.7.2-MC26.jar`

### Working

* Project compiles successfully under Java 25.
* Plugin no longer hard-fails on unknown future versions.
* Modern dependency alignment is complete.
* Packaging succeeds successfully against Paper 26.1.2.

### Remaining Work

Runtime validation still needs to be completed against live server behavior.

---

# Next Phase / Runtime Validation

## Functional Validation Targets

* Inventory interaction behavior
* Bucket fill/empty handling
* Bottle stacking behavior
* Hopper interaction validation
* Furnace/dispenser interaction validation
* Item consumption handling
* Durability and item damage behavior

---

# Optional Future Hardening

Potential cleanup tasks:

* Deprecate or remove older reflection fallback paths if modern ItemMeta handling proves stable.
* Additional optimization and compatibility cleanup for future server generations.
* Runtime profiling under large inventory stress conditions.

---

# Notes

This uplift focused primarily on:

* Modern build compatibility
* Runtime version tolerance
* Dependency alignment
* Preventing hard-fail startup behavior on newer Paper generations

The project is now in a usable state for runtime testing under Paper 26.1.2.
