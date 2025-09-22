This document provides comprehensive documentation for the methods and functions found in the provided file.

---

### Method Name: `calculatePosition`

*   **Description**: Calculates and updates the optimal position for a component module, considering existing modules, desired position, and locked status.
*   **Call Stack**:
    *   **Called by**: (Not explicitly called by any function in this file, assumed to be an entry point or called by an external service).
    *   **Calls**:
        *   `replicaConn.getConn()`: (External) Gets the Prisma replica client connection.
        *   `prismaClient.component.findMany()`: (External - Prisma ORM) Fetches multiple components.
        *   `prismaClient.componentModule.findMany()`: (External - Prisma ORM) Fetches multiple component modules.
        *   `calculateNewPositions()`: Calculates the new positions for modules based on current state and desired position.
        *   `updateModulePositions()`: Asynchronously updates the positions of a list of component modules in the database.
*   **Example Usage**:

    ```typescript
    import { calculatePosition } from './your-file'; // Assuming this is your file
    import { ComponentModule } from '../types';

    const componentModuleData: ComponentModule = {
      position: 5,
      isPositionLocked: false,
      // ... other ComponentModule properties
    };
    const tagUUID = 'some-tag-uuid';
    const semanticID = 'some-semantic-id';
    const updatingContentModuleId = 'module-to-exclude-if-updating';

    async function demonstrateCalculatePosition() {
      try {
        const actualNewPosition = await calculatePosition(
          componentModuleData,
          tagUUID,
          semanticID,
          updatingContentModuleId
        );
        console.log(`Actual position assigned: ${actualNewPosition}`);
      } catch (error) {
        console.error('Error calculating position:', error);
      }
    }

    demonstrateCalculatePosition();
    ```

---

### Method Name: `updateModulePositions`

*   **Description**: Asynchronously updates the positions of a list of component modules in the database.
*   **Call Stack**:
    *   **Called by**:
        *   `calculatePosition()`: Orchestrates the calculation and update of module positions.
    *   **Calls**:
        *   `writerConn.getConn()`: (External) Gets the Prisma writer client connection.
        *   `prismaWriter.componentModule.update()`: (External - Prisma ORM) Updates a single component module in the database.
        *   `Promise.allSettled()`: (Built-in) Waits for all promises to settle.
        *   `logger.info()`: (External - Logging) Logs informational messages.
*   **Example Usage**:

    ```typescript
    import { updateModulePositions, ModulePosition } from './your-file'; // Assuming this is your file

    const modulesToUpdate: ModulePosition[] = [
      { componentId: 101, moduleId: 'mod-A', position: 1, isPositionLocked: false },
      { componentId: 102, moduleId: 'mod-B', position: 2, isPositionLocked: true },
    ];

    async function demonstrateUpdateModulePositions() {
      console.log('Updating module positions...');
      await updateModulePositions(modulesToUpdate);
      console.log('Module positions update process initiated.');
      // Actual updates are asynchronous, check DB for confirmation
    }

    demonstrateUpdateModulePositions();
    ```

---

### Method Name: `calculateNewPositions`

*   **Description**: Calculates and returns the adjusted positions for a set of modules, respecting locked positions and filling gaps for unlocked modules.
*   **Call Stack**:
    *   **Called by**:
        *   `calculatePosition()`: Orchestrates the calculation and update of module positions.
    *   **Calls**:
        *   `findNextAvailableUnlockedPosition()`: Finds the next available position in a given list of occupied positions.
*   **Example Usage**:

    ```typescript
    import { calculateNewPositions, ModulePosition } from './your-file'; // Assuming this is your file

    const existingModules: ModulePosition[] = [
      { componentId: 1, moduleId: 'mod-X', position: 1, isPositionLocked: true },
      { componentId: 2, moduleId: 'mod-Y', position: 3, isPositionLocked: false },
      { componentId: 3, moduleId: 'mod-Z', position: 4, isPositionLocked: false },
    ];
    const desiredPosition = 2;
    const isPositionLocked = false; // For a new module being inserted

    const { actualPosition, updatedModules } = calculateNewPositions(
      existingModules,
      desiredPosition,
      isPositionLocked
    );

    console.log('Actual position determined for the new module:', actualPosition);
    console.log('Proposed updated positions for all modules:');
    updatedModules.forEach(m => console.log(`  Module ID: ${m.moduleId}, Position: ${m.position}`));
    ```

---

### Method Name: `sortByPosition`

*   **Description**: Sorts an array of `ContentModule` objects based on the position of their first component in either ascending or descending order.
*   **Call Stack**:
    *   **Called by**: (Not explicitly called by any function in this file).
    *   **Calls**:
        *   `sortFunc()`: Compares two component position objects based on a specified direction.
*   **Example Usage**:

    ```typescript
    import { sortByPosition } from './your-file'; // Assuming this is your file
    import { ContentModule } from '../types';

    const modules: ContentModule[] = [
      { id: '1', components: [{ position: 3, otherData: 'A' }] },
      { id: '2', components: [{ position: 1, otherData: 'B' }] },
      { id: '3', components: [{ position: 2, otherData: 'C' }] },
    ];

    console.log('Original module positions:', modules.map(m => m.components?.[0]?.position));

    const sortedAsc = sortByPosition([...modules], 'asc'); // Use spread to avoid mutating original
    console.log('Sorted Ascending:', sortedAsc.map(m => m.components?.[0]?.position));

    const sortedDesc = sortByPosition([...modules], 'desc');
    console.log('Sorted Descending:', sortedDesc.map(m => m.components?.[0]?.position));
    ```

---

### Method Name: `sortFunc`

*   **Description**: Compares two objects with a `position` property and returns a value suitable for array sorting based on the specified order direction.
*   **Call Stack**:
    *   **Called by**:
        *   `sortByPosition()`: Sorts an array of `ContentModule` objects.
    *   **Calls**: (None internal)
*   **Example Usage**:

    ```typescript
    import { sortFunc } from './your-file'; // Assuming this is your file

    const compA = { position: 5 };
    const compB = { position: 10 };
    const compC = { position: 5 };

    console.log('Ascending (5 vs 10):', sortFunc(compA, compB, 'asc'));  // Expected negative value
    console.log('Descending (5 vs 10):', sortFunc(compA, compB, 'desc')); // Expected positive value
    console.log('Ascending (5 vs 5):', sortFunc(compA, compC, 'asc'));   // Expected zero
    ```

---

### Method Name: `ContentModulePositionError` (Constructor)

*   **Description**: Initializes a new instance of a custom error class used for issues related to content module positioning.
*   **Call Stack**:
    *   **Called by**: (Not explicitly called by any function in this file, likely used by external services to throw errors when position-related issues occur).
    *   **Calls**:
        *   `super()`: (Built-in) Calls the constructor of the `Error` base class.
*   **Example Usage**:

    ```typescript
    import { ContentModulePositionError } from './your-file'; // Assuming this is your file

    function processModulePosition(position: number) {
      if (position < 1) {
        throw new ContentModulePositionError('Module position cannot be less than 1.');
      }
      console.log(`Processing module at valid position: ${position}`);
    }

    try {
      processModulePosition(0);
    } catch (error) {
      if (error instanceof ContentModulePositionError) {
        console.error(`Caught custom error: ${error.name} - ${error.message}`);
      } else {
        console.error('Caught unexpected error:', error);
      }
    }
    ```

---

### Method Name: `findNextAvailableUnlockedPosition`

*   **Description**: Finds the next available integer position that is not present in a given list of occupied positions, starting from `startPosition` or 1 if the module is not locked.
*   **Call Stack**:
    *   **Called by**:
        *   `calculateNewPositions()`: Calculates the new positions for modules.
    *   **Calls**: (None internal)
*   **Example Usage**:

    ```typescript
    import { findNextAvailableUnlockedPosition } from './your-file'; // Assuming this is your file

    const occupiedPositions = [1, 3, 4, 7];

    // Scenario 1: Unlocked module, always starts looking from 1
    console.log('Unlocked (start 5, occupied [1,3,4,7]):', findNextAvailableUnlockedPosition(occupiedPositions, 5, false)); // Expected 2
    console.log('Unlocked (start 1, occupied [1,3,4,7]):', findNextAvailableUnlockedPosition(occupiedPositions, 1, false)); // Expected 2

    // Scenario 2: Locked module, starts looking from startPosition
    console.log('Locked (start 1, occupied [1,3,4,7]):', findNextAvailableUnlockedPosition(occupiedPositions, 1, true)); // Expected 2
    console.log('Locked (start 2, occupied [1,3,4,7]):', findNextAvailableUnlockedPosition(occupiedPositions, 2, true)); // Expected 2
    console.log('Locked (start 3, occupied [1,3,4,7]):', findNextAvailableUnlockedPosition(occupiedPositions, 3, true)); // Expected 5
    console.log('Locked (start 8, occupied [1,3,4,7]):', findNextAvailableUnlockedPosition(occupiedPositions, 8, true)); // Expected 8 (since 8 is not in occupiedPositions)
    ```