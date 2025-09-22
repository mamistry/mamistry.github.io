Here is the comprehensive documentation for the methods found in the provided file:

---

### 1. `sortContentModulesByUpdatedAtDate`

*   **Description**: Sorts a list of content modules by their `updatedAt` date in descending order, also sorting nested package contents in ascending order.
*   **Call Stack**:
    *   **Calls**:
        *   `cloneDeep`: (from `lodash`) Creates a deep copy of an object or array.
        *   `Array.prototype.sort()`: Sorts the elements of an array in place.
        *   `Date.prototype.getTime()`: Returns the number of milliseconds since the epoch.
    *   **Called by**: (Not identifiable from the provided code)
*   **Example Usage**:

    ```typescript
    import { TContentModule, ModuleType } from './ContentModuleTypes';

    const contentModule1: TContentModule = {
      id: "mod1", name: "Module A", type: ModuleType.Module,
      updatedAt: new Date("2023-01-15T10:00:00Z"), components: []
    };
    const contentModule2: TContentModule = {
      id: "mod2", name: "Module B", type: ModuleType.Module,
      updatedAt: new Date("2023-01-10T10:00:00Z"), components: []
    };
    const contentModule3: TContentModule = {
      id: "mod3", name: "Module C", type: ModuleType.Package,
      updatedAt: new Date("2023-01-20T10:00:00Z"), components: [],
      Composite: {
        contents: [
          { Module: { id: "submod1", updatedAt: new Date("2023-01-05T10:00:00Z"), components: [] }, position: 1, isPositionLocked: false },
          { Module: { id: "submod2", updatedAt: new Date("2023-01-08T10:00:00Z"), components: [] }, position: 2, isPositionLocked: false }
        ]
      }
    };

    const contentModules: TContentModule[] = [contentModule1, contentModule2, contentModule3];

    const sortedModules = sortContentModulesByUpdatedAtDate(contentModules);
    console.log(sortedModules.map(m => m.name));
    // Expected output (Module C first as it has latest updatedAt):
    // [ 'Module C', 'Module A', 'Module B' ]
    ```

---

### 2. `sortContentModulesByPosition`

*   **Description**: Sorts a list of content modules by the `position` of their first component in ascending order, also sorting nested package contents.
*   **Call Stack**:
    *   **Calls**:
        *   `cloneDeep`: (from `lodash`) Creates a deep copy of an object or array.
        *   `Array.prototype.sort()`: Sorts the elements of an array in place.
    *   **Called by**: (Not identifiable from the provided code)
*   **Example Usage**:

    ```typescript
    import { TContentModule, ModuleType } from './ContentModuleTypes';

    const contentModule1: TContentModule = {
      id: "mod1", name: "Module A", type: ModuleType.Module, updatedAt: new Date(),
      components: [{ id: "comp1", position: 2 }]
    };
    const contentModule2: TContentModule = {
      id: "mod2", name: "Module B", type: ModuleType.Module, updatedAt: new Date(),
      components: [{ id: "comp2", position: 1 }]
    };
    const contentModule3: TContentModule = {
      id: "mod3", name: "Module C", type: ModuleType.Package, updatedAt: new Date(),
      components: [{ id: "comp3", position: 3 }],
      Composite: {
        contents: [
          { Module: { id: "submod1", updatedAt: new Date(), components: [{ id: "subcomp1", position: 2 }] }, position: 1, isPositionLocked: false },
          { Module: { id: "submod2", updatedAt: new Date(), components: [{ id: "subcomp2", position: 1 }] }, position: 2, isPositionLocked: false }
        ]
      }
    };

    const contentModules: TContentModule[] = [contentModule1, contentModule2, contentModule3];

    const sortedModules = sortContentModulesByPosition(contentModules);
    console.log(sortedModules.map(m => m.name));
    // Expected output (Module B, Module A, Module C based on their components[0].position):
    // [ 'Module B', 'Module A', 'Module C' ]

    // Check nested package contents
    // console.log(sortedModules[2].Composite.contents.map(c => c.Module.id));
    // Expected output: ['submod2', 'submod1'] (based on sub-module components[0].position)
    ```

---

### 3. `ensurePositionLockValueIsRespectedInOrder`

*   **Description**: Adjusts the order of content modules and their nested package contents to respect `isPositionLocked` values, swapping modules if their `programmedPosition` does not match their current index (after adjusting for 0-based vs 1-based indexing).
*   **Call Stack**:
    *   **Calls**:
        *   `cloneDeep`: (from `lodash`) Creates a deep copy of an object or array.
        *   `Array.prototype.forEach()`: Iterates over array elements.
        *   `Array.prototype.find()`: Returns the first element in the provided array that satisfies the provided testing function.
    *   **Called by**: (Not identifiable from the provided code)
*   **Example Usage**:

    ```typescript
    import { TContentModule, ModuleType } from './ContentModuleTypes';

    const semanticID = "mySemanticId";
    const tagUUID = "myTagUuid";

    const contentModuleA: TContentModule = {
      id: "modA", name: "Module A", type: ModuleType.Module, updatedAt: new Date(),
      components: [{ id: "compA", semanticID: "otherId", tagUUID: "otherUuid", position: 1, isPositionLocked: false }]
    };
    const contentModuleB: TContentModule = {
      id: "modB", name: "Module B", type: ModuleType.Module, updatedAt: new Date(),
      components: [{ id: "compB", semanticID, tagUUID, position: 1, isPositionLocked: true }] // Wants to be at index 0 (position 1)
    };
    const contentModuleC: TContentModule = {
      id: "modC", name: "Module C", type: ModuleType.Package, updatedAt: new Date(),
      components: [{ id: "compC", semanticID: "otherId", tagUUID: "otherUuid", position: 3, isPositionLocked: false }],
      Composite: {
        contents: [
          { Module: { id: "submodX", updatedAt: new Date(), components: [] }, position: 2, isPositionLocked: true }, // Wants to be at index 1 (position 2)
          { Module: { id: "submodY", updatedAt: new Date(), components: [] }, position: 1, isPositionLocked: false }
        ]
      }
    };

    const initialContentModules: TContentModule[] = [
      contentModuleA, // Index 0
      contentModuleB, // Index 1, but compB.position is 1 (wants index 0) and isLocked=true -> should swap with A
      contentModuleC  // Index 2
    ];

    const args = {
      semanticID: semanticID,
      tagUUID: tagUUID,
      contentModules: initialContentModules,
    };

    const reorderedModules = ensurePositionLockValueIsRespectedInOrder(args);
    console.log("Top-level reordered modules:", reorderedModules.map(m => m.name));
    // Expected: [ 'Module B', 'Module A', 'Module C' ]

    console.log("Nested reordered modules (Module C):", reorderedModules[2].Composite.contents.map(c => c.Module.id));
    // Expected: [ 'submodY', 'submodX' ] (submodX wants to be at index 1, currently index 0 in the Composite.contents array)
    ```