This document provides comprehensive documentation for the methods and functions found in the provided file.

---

### Method Name: `deleteDuplicateScheduledHighlightsPackages`

*   **Description**: Identifies duplicate 'Highlights' packages that are in a 'SCHEDULED' state for any component within the 'ContentHighlights' semantic ID, keeping the oldest package, and schedules the duplicates for asynchronous deletion.
*   **Call Stack**:
    *   **Called by**:
        *   `cleanupDuplicateHighlightsPackages`: Schedules this function to run after a delay as a cleanup task.
    *   **Calls**:
        *   `PrismaConn.getInstance()`: Retrieves the singleton instance of the Prisma connection manager.
        *   `PrismaConn.getInstance().getConn()`: Obtains the `PrismaClient` instance from the connection manager.
        *   `prismaClient.component.findMany()`: Fetches multiple component records from the database based on criteria.
        *   `prismaClient.module.findMany()`: Fetches multiple module records (packages) from the database based on criteria, including related `Composite` and `Component` data.
        *   `console.log()`: Logs information about identified duplicate packages to the console, including batch context.
        *   `backgroundPrismaClient.module.deleteMany()`: Deletes multiple module records from the database in batches as part of a background process.
        *   `logger.error()`: Logs error messages related to the duplicate package identification or background deletion process.
*   **Example Usage**:

    ```typescript
    import { deleteDuplicateScheduledHighlightsPackages } from './your-module-path';

    async function triggerHighlightsCleanup() {
      console.log('Initiating duplicate highlights package cleanup...');
      try {
        const result = await deleteDuplicateScheduledHighlightsPackages();
        if (result.scheduledForDeletionIds.length > 0) {
          console.log(
            `Successfully scheduled ${result.scheduledForDeletionIds.length} duplicate highlight packages for deletion.`
          );
          console.log('IDs scheduled:', result.scheduledForDeletionIds);
        } else {
          console.log('No duplicate highlight packages found or scheduled for deletion.');
        }
      } catch (error) {
        console.error('Failed to run highlights cleanup:', error);
      }
    }

    triggerHighlightsCleanup();
    ```

---

### Method Name: `cleanupDuplicateHighlightsPackages`

*   **Description**: Schedules the `deleteDuplicateScheduledHighlightsPackages` function to run after a 5-minute delay to perform cleanup of duplicate highlights packages.
*   **Call Stack**:
    *   **Called by**: (Not explicitly called by any function within this file; likely intended to be called at application startup or by a task scheduler.)
    *   **Calls**:
        *   `setTimeout()`: Schedules the provided asynchronous callback function to execute after a specified delay.
        *   `deleteDuplicateScheduledHighlightsPackages()`: Identifies and schedules duplicate 'Highlights' packages for deletion.
        *   `logger.error()`: Logs any errors that occur during the execution of the scheduled cleanup task.
*   **Example Usage**:

    ```typescript
    import { cleanupDuplicateHighlightsPackages } from './your-module-path';

    // This function is typically called once to kick off a background task.
    // For example, in your application's main entry point or a dedicated task runner.
    cleanupDuplicateHighlightsPackages();
    console.log('Duplicate highlights cleanup task has been scheduled to run in 5 minutes.');
    ```