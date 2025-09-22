Here is the comprehensive documentation for the methods found in the provided file.

---

### Method Name: `LexicalPositionService.assignPosition`

*   **Description**: Calculates and returns a new lexical position for a module based on a desired position and existing module positions.
*   **Call Stack**:
    *   **Calls**:
        *   `LexicalPositionService.findModulesBeforeAndAfter`: Determines the positions of modules immediately before and after a desired index.
        *   `Math.round`: Rounds a number to the nearest integer.
*   **Example Usage**:

    ```typescript
    const existing = [
      { position: 1000 },
      { position: 2000 },
      { position: 3000 }
    ];

    // Case 1: No existing modules or desired position
    const newPosition1 = LexicalPositionService.assignPosition({
      desiredPosition: undefined,
      existingModules: [],
    });
    // newPosition1 will be 1000 (LexicalPositionService.positionStep)

    // Case 2: Desired position at an index
    const newPosition2 = LexicalPositionService.assignPosition({
      desiredPosition: 2, // Inserts between index 1 and 2 (position 2000 and 3000)
      existingModules: existing,
    });
    // newPosition2 will be Math.round((2000 + 3000) / 2) = 2500

    // Case 3: Desired position at the start (index 1)
    const newPosition3 = LexicalPositionService.assignPosition({
      desiredPosition: 1,
      existingModules: existing,
    });
    // newPosition3 will be existing[0].position + LexicalPositionService.positionStep = 1000 + 1000 = 2000
    ```

---

### Method Name: `LexicalPositionService.findModulesBeforeAndAfter`

*   **Description**: Identifies the lexical positions of the module immediately before and the module at a specified `desiredIndex` within an array of existing modules.
*   **Call Stack**:
    *   **Called by**:
        *   `LexicalPositionService.assignPosition`: Calculates a new lexical position.
    *   **Calls**:
        *   (No explicit function calls within the provided code.)
*   **Example Usage**:

    ```typescript
    const existing = [
      { position: 1000 },
      { position: 2000 },
      { position: 3000 }
    ];

    // Example 1: desiredIndex = 1 (second element)
    const { before: b1, after: a1 } = LexicalPositionService["findModulesBeforeAndAfter"](1, existing);
    // b1 will be 1000 (existing[0].position)
    // a1 will be 2000 (existing[1].position)

    // Example 2: desiredIndex = 0 (first element)
    const { before: b2, after: a2 } = LexicalPositionService["findModulesBeforeAndAfter"](0, existing);
    // b2 will be undefined
    // a2 will be 1000 (existing[0].position)

    // Example 3: desiredIndex out of bounds (beyond last element)
    const { before: b3, after: a3 } = LexicalPositionService["findModulesBeforeAndAfter"](5, existing);
    // b3 will be 3000 (existing[2].position)
    // a3 will be 0 (due to ?? 0 when existing[5] is undefined)
    ```
    *Note: This is a `private static` method, so direct access for example purposes is shown via bracket notation.*

---

### Method Name: `LexicalPositionService.locksReordering`

*   **Description**: Reorders a list of sibling items, prioritizing and placing items with valid locked positions at their specified indices, then filling in the remaining spots with unlocked items.
*   **Call Stack**:
    *   **Calls**:
        *   (No explicit function calls within the provided code.)
*   **Example Usage**:

    ```typescript
    interface MyModule {
      id: string;
      isPositionLocked: boolean;
      lockedPosition: number | null;
    }

    const siblings: MyModule[] = [
      { id: 'A', isPositionLocked: false, lockedPosition: null },
      { id: 'B', isPositionLocked: true, lockedPosition: 3 }, // Will be at index 2
      { id: 'C', isPositionLocked: false, lockedPosition: null },
      { id: 'D', isPositionLocked: true, lockedPosition: 1 }, // Will be at index 0
      { id: 'E', isPositionLocked: false, lockedPosition: null },
    ];

    const reordered = LexicalPositionService.locksReordering(siblings);
    /*
    reordered will contain:
    [
      { id: 'D', isPositionLocked: true, lockedPosition: 1 },  // From lockedPosition 1
      { id: 'A', isPositionLocked: false, lockedPosition: null }, // Unlocked item (order of unlocked is preserved)
      { id: 'B', isPositionLocked: true, lockedPosition: 3 },  // From lockedPosition 3
      { id: 'C', isPositionLocked: false, lockedPosition: null }, // Unlocked item
      { id: 'E', isPositionLocked: false, lockedPosition: null }, // Unlocked item
    ]
    */

    // Another example with invalid locked positions
    const siblings2: MyModule[] = [
      { id: 'X', isPositionLocked: true, lockedPosition: 0 }, // Invalid, treated as unlocked
      { id: 'Y', isPositionLocked: true, lockedPosition: 10 },// Invalid for a list of 2, treated as unlocked
      { id: 'Z', isPositionLocked: false, lockedPosition: null },
    ];
    const reordered2 = LexicalPositionService.locksReordering(siblings2);
    /*
    reordered2 will contain:
    [
      { id: 'X', isPositionLocked: true, lockedPosition: 0 },
      { id: 'Y', isPositionLocked: true, lockedPosition: 10 },
      { id: 'Z', isPositionLocked: false, lockedPosition: null },
    ]
    (The order of X, Y, Z might vary depending on their order in the 'unlocked' array, but no locks will be respected)
    */
    ```