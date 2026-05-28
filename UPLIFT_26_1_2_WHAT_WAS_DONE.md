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

* `target/inventorystacks-2.7.0-MC26.jar`
* `target/original-inventorystacks-2.7.0-MC26.jar`

---

# Current Status

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
