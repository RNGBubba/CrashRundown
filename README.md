
---
# Minecraft Server Crash Reports Summary

>This document summarizes and analyzes Minecraft server crash reports, organized by date and time. Each section includes the main error, a detailed walkthrough, and the likely cause. Use this as a reference for troubleshooting and learning from these crash scenarios.

---

## Table of Contents

1. [Crash: 2025-06-30 13:25:29](#crash-2025-06-30_132529-servertxt)
2. [Crash: 2025-06-30 13:31:52](#crash-2025-06-30_133152-servertxt)
3. [Crash: 2025-06-30 13:36:57](#crash-2025-06-30_133657-servertxt)
4. [Crash: 2025-06-30 13:44:55](#crash-2025-06-30_134455-servertxt)
5. [Crash: 2025-06-30 13:57:47](#crash-2025-06-30_135747-servertxt)
6. [General Notes](#general-notes)

---

## Crash: `crash-2025-06-30_13.25.29-server.txt`

### Main Error
```text
java.lang.Error: ServerHangWatchdog detected that a single server tick took 60000004.00 seconds (should be max 0.05)
    at net.p3pp3rf1y.sophisticatedcore.inventory.CachedFailedInsertInventoryHandler.getStackInSlot(CachedFailedInsertInventoryHandler.java:34)
    at org.cyclops.commoncapabilities.ingredient.storage.IngredientComponentStorageWrapperHandlerItemStack$ComponentStorageWrapper.storageExtractItem(IngredientComponentStorageWrapperHandlerItemStack.java:270)
    ...
```

### Detailed Walkthrough
- **Crash Description:**
  - The server's watchdog detected that a single server tick took far too long (over 60000004 seconds), which is a sign of a deadlock, infinite loop, or a mod/plugin causing the main server thread to freeze.
- **Key Stacktrace:**
  - The error originated in `sophisticatedcore` (specifically `CachedFailedInsertInventoryHandler.getStackInSlot`).
  - The call chain involves `commoncapabilities`, `integrateddynamics`, `cyclopscore`, and `integratedtunnels` mods, all related to inventory and item movement/handling.
  - The stacktrace shows repeated calls through these mods, suggesting a possible infinite loop or deadlock in item transfer logic.
- **Thread Dump:**
  - Many ForkJoinPool worker threads are in WAITING state, which can indicate a deadlock or resource starvation.
  - The main server thread is stuck in the inventory/item transfer logic.

### Likely Cause
- A mod interaction (likely between `sophisticatedcore`, `commoncapabilities`, `integrateddynamics`, and `integratedtunnels`) caused the server to hang during an inventory/item transfer operation.
- This could be due to a bug, infinite loop, or deadlock in the item movement logic between these mods.

---

## Crash: `crash-2025-06-30_13.31.52-server.txt`

### Main Error
```text
java.lang.Error: ServerHangWatchdog detected that a single server tick took 60000204.00 seconds (should be max 0.05)
    at it.unimi.dsi.fastutil.objects.Object2LongOpenHashMap$MapIterator.nextEntry(Object2LongOpenHashMap.java:716)
    at appeng.me.cells.BasicCellInventory.getAvailableStacks(BasicCellInventory.java:243)
    ...
```

### Detailed Walkthrough
- **Crash Description:**
  - The server's watchdog detected that a single server tick took far too long (over 60000204 seconds), which is a sign of a deadlock, infinite loop, or a mod/plugin causing the main server thread to freeze.
- **Key Stacktrace:**
  - The error originated in the `fastutil` library, specifically in the `Object2LongOpenHashMap` iterator.
  - The call chain involves the `appliedenergistics2` (AE2) mod, particularly its ME storage and inventory handling (`BasicCellInventory`, `MEInventoryHandler`, `NetworkStorage`).
  - The stacktrace shows repeated calls through AE2's storage system, suggesting a possible infinite loop or deadlock in item stack iteration or ME network cache update.
- **Thread Dump:**
  - Many ForkJoinPool worker threads are in WAITING or TIMED_WAITING state, which can indicate a deadlock or resource starvation.
  - The main server thread is stuck in the AE2 ME storage stack iteration logic.

### Likely Cause
- A mod interaction or bug (likely within `appliedenergistics2` or its dependencies) caused the server to hang during ME storage stack iteration or cache update.
- This could be due to a bug, infinite loop, or deadlock in the ME network's item stack handling or a corrupted ME network state.

---

## Crash: `crash-2025-06-30_13.36.57-server.txt`

### Main Error
```text
java.lang.Error: ServerHangWatchdog detected that a single server tick took 60000004.00 seconds (should be max 0.05)
    at net.minecraft.core.component.DataComponentMap.has(DataComponentMap.java:91)
    at org.cyclops.commoncapabilities.api.capability.itemhandler.ItemMatch.areItemStackDataComponentsEqual(ItemMatch.java:57)
    ...
```

### Detailed Walkthrough
- **Crash Description:**
  - The server's watchdog detected that a single server tick took far too long (over 60000004 seconds), which is a sign of a deadlock, infinite loop, or a mod/plugin causing the main server thread to freeze.
- **Key Stacktrace:**
  - The error originated in Minecraft's core `DataComponentMap` and was triggered during item stack comparison logic in the `commoncapabilities` mod.
  - The call chain involves `commoncapabilities`, `cyclopscore`, `integrateddynamics`, and `integratedtunnels` mods, all related to inventory and item movement/handling.
  - The stacktrace shows repeated calls through these mods, suggesting a possible infinite loop or deadlock in item comparison or transfer logic.
- **Thread Dump:**
  - Many ForkJoinPool worker threads are in WAITING or TIMED_WAITING state, which can indicate a deadlock or resource starvation.
  - The main server thread is stuck in the item stack comparison and transfer logic.

### Likely Cause
- A mod interaction (likely between `commoncapabilities`, `cyclopscore`, `integrateddynamics`, and `integratedtunnels`) caused the server to hang during an item stack comparison or transfer operation.
- This could be due to a bug, infinite loop, or deadlock in the item comparison or movement logic between these mods.

---

## Crash: `crash-2025-06-30_13.44.55-server.txt`

### Main Error
```text
java.lang.Error: ServerHangWatchdog detected that a single server tick took 60000004.00 seconds (should be max 0.05)
    at com.google.common.collect.AbstractIterator.tryToComputeNext(AbstractIterator.java:145)
    at net.minecraft.world.entity.Entity.isInWall(Entity.java:1907)
    ...
```

### Detailed Walkthrough
- **Crash Description:**
  - The server's watchdog detected that a single server tick took far too long (over 60000004 seconds), which is a sign of a deadlock, infinite loop, or a mod/plugin causing the main server thread to freeze.
- **Key Stacktrace:**
  - The error originated in the Guava library's `AbstractIterator` and was triggered during entity collision logic in Minecraft's `Entity.isInWall` method.
  - The call chain involves Java streams and entity logic, with many mods applying mixins to the entity code (see the long list of mixins in the stacktrace).
  - The stacktrace shows repeated calls through entity and stream logic, suggesting a possible infinite loop or deadlock in entity collision or tick logic, possibly due to a modded entity or a mixin bug.
- **Thread Dump:**
  - Many ForkJoinPool worker threads are in WAITING or TIMED_WAITING state, which can indicate a deadlock or resource starvation.
  - The main server thread is stuck in entity collision/tick logic, likely due to a modded entity or a mixin issue.

### Likely Cause
- A mod interaction or bug (possibly involving entity mixins or collision logic) caused the server to hang during entity collision or tick processing.
- This could be due to a bug, infinite loop, or deadlock in entity logic, or a problematic modded entity or mixin.

---

## Crash: `crash-2025-06-30_13.57.47-server.txt`

### Main Error
```text
java.lang.Error: ServerHangWatchdog detected that a single server tick took 60000004.00 seconds (should be max 0.05)
    at net.minecraft.world.level.chunk.PalettedContainer.get(PalettedContainer.java:158)
    at net.minecraft.world.level.chunk.LevelChunkSection.getBlockState(LevelChunkSection.java:39)
    ...
```

### Detailed Walkthrough
- **Crash Description:**
  - The server's watchdog detected that a single server tick took far too long (over 60000004 seconds), which is a sign of a deadlock, infinite loop, or a mod/plugin causing the main server thread to freeze.
- **Key Stacktrace:**
  - The error originated in Minecraft's chunk storage logic (`PalettedContainer.get`), triggered during block state access in chunk or entity AI logic.
  - The call chain involves entity AI goals (e.g., `RemoveBlockGoal`, `MoveToBlockGoal`), chunk/block state access, and many mods applying mixins to chunk and entity code.
  - The stacktrace shows repeated calls through chunk/block state and entity AI logic, suggesting a possible infinite loop or deadlock in chunk access or entity AI, possibly due to a modded entity, worldgen, or a mixin bug.
- **Thread Dump:**
  - Many ForkJoinPool worker threads are in WAITING or TIMED_WAITING state, which can indicate a deadlock or resource starvation.
  - The main server thread is stuck in chunk/block state access and entity AI logic, likely due to a modded entity, worldgen, or a mixin issue.

### Likely Cause
- A mod interaction or bug (possibly involving chunk/block state access, entity AI, or mixins) caused the server to hang during chunk or entity processing.
- This could be due to a bug, infinite loop, or deadlock in chunk access, entity AI, or a problematic modded entity or mixin.

---

## General Notes

- All crashes are caused by the ServerHangWatchdog detecting that the server tick took far too long.
- The most common causes are deadlocks, infinite loops, or problematic mods/plugins that block the main server thread.
- The thread dumps can help identify which mod or plugin is responsible by looking for threads that are stuck or consuming resources.
- To resolve, check for recently added or updated mods/plugins, and review server logs for additional context. Try removing mods one by one to isolate the cause.
