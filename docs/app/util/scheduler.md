This document provides a comprehensive overview of the methods and functions found within the provided file, detailing their purpose, execution flow, and example usage.

---

### Method Name: `updateExpiredLocks`
*   **Description**: Asynchronously finds component modules with expired position locks and updates them to an unlocked state.
*   **Call Stack**:
    *   **Called by**:
        *   `expirationLocks`: Schedules this function to run periodically via a mutex.
    *   **Calls**:
        *   `replicaConn.getConn()`: Retrieves a read-only Prisma client instance.
        *   `prismaConn.getConn()`: Retrieves a writable Prisma client instance.
        *   `prismaReplica.componentModule.findMany()`: Queries the replica database for component modules with expired position locks.
        *   `logger.info()`: Logs informational messages about expired and updated locks.
        *   `prismaWritter.componentModule.updateMany()`: Updates the `isPositionLocked` status for multiple component modules in the database.
        *   `prismaWritter.$transaction()`: Executes a batch of database updates as a single transaction.
        *   `logger.error()`: Logs any errors encountered during the update process.
*   **Example Usage**:
    ```typescript
    await updateExpiredLocks();
    // This function is primarily called by a scheduled job.
    ```

---

### Method Name: `cleanupExpiredGamecasts`
*   **Description**: Initiates the cleanup process for old and expired gamecasts.
*   **Call Stack**:
    *   **Called by**:
        *   `gamecastCleanUp`: Schedules this function to run at specific delays after deployment.
    *   **Calls**:
        *   `gamecastProcessor.updateOldGamecasts()`: An external function responsible for the actual gamecast cleanup logic.
        *   `logger.info()`: Logs a success message upon completion.
        *   `logger.error()`: Logs any errors encountered during the cleanup.
*   **Example Usage**:
    ```typescript
    await cleanupExpiredGamecasts();
    // This function is primarily called by a scheduled job.
    ```

---

### Method Name: `cleanupDuplicateHighlightsPackages`
*   **Description**: Triggers a service to find and schedule for deletion duplicate Highlights packages.
*   **Call Stack**:
    *   **Called by**:
        *   `duplicatedHighlightsCleanUp`: Schedules this function to run once shortly after startup.
    *   **Calls**:
        *   `deleteDuplicateScheduledHighlightsPackages()`: An external function that handles the logic for identifying and scheduling duplicate packages for deletion.
        *   `logger.info()`: Logs information about the number of packages scheduled for deletion.
        *   `logger.error()`: Logs any errors encountered during the cleanup.
*   **Example Usage**:
    ```typescript
    await cleanupDuplicateHighlightsPackages();
    // This function is primarily called by a scheduled job.
    ```

---

### Method Name: `startScheduler`
*   **Description**: Initializes and starts all scheduled background tasks for the application.
*   **Call Stack**:
    *   **Called by**: (External to this file, typically an application entry point)
    *   **Calls**:
        *   `expirationLocks()`: Configures and schedules the task for updating expired locks.
        *   `gamecastCleanUp()`: Configures and schedules the gamecast cleanup task.
        *   `duplicatedHighlightsCleanUp()`: Configures and schedules the duplicate Highlights cleanup task.
        *   `packageContentsNormalization()`: Configures and schedules the package contents normalization task.
        *   `channelContentsNormalization()`: Configures and schedules the channel contents normalization task.
        *   `logger.info()`: Logs a message indicating that scheduled tasks have been initialized.
*   **Example Usage**:
    ```typescript
    startScheduler();
    // Typically called once during application startup.
    ```

---

### Method Name: `expirationLocks`
*   **Description**: Schedules the `updateExpiredLocks` function to run at a regular interval defined by environment variables, ensuring only one instance runs at a time using a mutex.
*   **Call Stack**:
    *   **Called by**:
        *   `startScheduler`: Initializes this scheduled task.
    *   **Calls**:
        *   `parseInt()`: Parses the `CRON_INTERVAL_MINUTES` environment variable.
        *   `process.env.CRON_INTERVAL_MINUTES`: Retrieves the cron interval from environment variables.
        *   `cron.schedule()`: Schedules the provided asynchronous function to run based on the cron expression.
        *   `useMutex()`: Executes the `updateExpiredLocks` function under a mutex lock.
        *   `updateExpiredLocks()`: The actual function to update expired locks.
*   **Example Usage**:
    ```typescript
    // This function is called internally by `startScheduler`
    // expirationLocks();
    ```

---

### Method Name: `gamecastCleanUp`
*   **Description**: Schedules the `cleanupExpiredGamecasts` function to run at specific delays (e.g., 2 and 5 hours) after deployment.
*   **Call Stack**:
    *   **Called by**:
        *   `startScheduler`: Initializes this scheduled task.
    *   **Calls**:
        *   `cleanupDelays.forEach()`: Iterates through the predefined cleanup delays.
        *   `setTimeout()`: Schedules the `cleanupExpiredGamecasts` call after a calculated delay.
        *   `cleanupExpiredGamecasts()`: The function that performs the gamecast cleanup.
        *   `logger.info()`: Logs success messages for gamecast cleanup.
        *   `logger.error()`: Logs error messages if gamecast cleanup fails.
*   **Example Usage**:
    ```typescript
    // This function is called internally by `startScheduler`
    // gamecastCleanUp();
    ```

---

### Method Name: `duplicatedHighlightsCleanUp`
*   **Description**: Schedules a one-time execution of `cleanupDuplicateHighlightsPackages` shortly after startup (20 minutes).
*   **Call Stack**:
    *   **Called by**:
        *   `startScheduler`: Initializes this scheduled task.
    *   **Calls**:
        *   `setTimeout()`: Schedules the `cleanupDuplicateHighlightsPackages` call after a fixed delay.
        *   `cleanupDuplicateHighlightsPackages()`: The function that cleans up duplicate highlights packages.
*   **Example Usage**:
    ```typescript
    // This function is called internally by `startScheduler`
    // duplicatedHighlightsCleanUp();
    ```

---

### Method Name: `packageContentsNormalization`
*   **Description**: Schedules the `normalizePackageContentsPositions` function to run periodically at a specified interval (default 12 hours), ensuring only one instance runs at a time using a mutex.
*   **Call Stack**:
    *   **Called by**:
        *   `startScheduler`: Initializes this scheduled task.
    *   **Calls**:
        *   `parseInt()`: Parses the `PACKAGE_NORMALIZATION_HOURS_INTERVAL` environment variable.
        *   `process.env.PACKAGE_NORMALIZATION_HOURS_INTERVAL`: Retrieves the normalization interval from environment variables.
        *   `cron.schedule()`: Schedules the provided asynchronous function to run based on the cron expression.
        *   `useMutex()`: Executes the `normalizePackageContentsPositions` function under a mutex lock.
        *   `normalizePackageContentsPositions()`: An external function that normalizes package content positions.
*   **Example Usage**:
    ```typescript
    // This function is called internally by `startScheduler`
    // packageContentsNormalization();
    ```

---

### Method Name: `channelContentsNormalization`
*   **Description**: Schedules the `normalizeStandalonesContentsPositions` function to run periodically at a specified interval (default 12 hours), ensuring only one instance runs at a time using a mutex.
*   **Call Stack**:
    *   **Called by**:
        *   `startScheduler`: Initializes this scheduled task.
    *   **Calls**:
        *   `parseInt()`: Parses the `CHANNEL_NORMALIZATION_HOURS_INTERVAL` environment variable.
        *   `process.env.CHANNEL_NORMALIZATION_HOURS_INTERVAL`: Retrieves the normalization interval from environment variables.
        *   `cron.schedule()`: Schedules the provided asynchronous function to run based on the cron expression.
        *   `useMutex()`: Executes the `normalizeStandalonesContentsPositions` function under a mutex lock.
        *   `normalizeStandalonesContentsPositions()`: An external function that normalizes standalone content positions.
*   **Example Usage**:
    ```typescript
    // This function is called internally by `startScheduler`
    // channelContentsNormalization();
    ```

---

### Method Name: `useMutex`
*   **Description**: Acquires a distributed mutex lock before executing a provided asynchronous function (`func`), and releases the lock afterwards, preventing concurrent execution of the same job across multiple processes.
*   **Call Stack**:
    *   **Called by**:
        *   `expirationLocks`: Uses mutex for updating expired locks.
        *   `packageContentsNormalization`: Uses mutex for package contents normalization.
        *   `channelContentsNormalization`: Uses mutex for channel contents normalization.
    *   **Calls**:
        *   `new MutexUtil()`: Creates a new mutex utility instance.
        *   `mutex.tryAquire()`: Attempts to acquire the mutex lock.
        *   `func()`: The callback function provided to `useMutex`, executed if the lock is acquired.
        *   `logger.error()`: Logs any errors that occur during the mutex-protected task execution.
        *   `mutex.release()`: Releases the acquired mutex lock, regardless of success or failure.
*   **Example Usage**:
    ```typescript
    import MutexUtil from '../../lib/mutexUtil';
    import logger from '../../observability/logging';

    async function myProtectedTask() {
        console.log("Executing protected task...");
        await new Promise(resolve => setTimeout(resolve, 1000));
        console.log("Protected task finished.");
    }

    // To be called by a scheduler or any part of the app that needs mutex protection
    await useMutex('my-unique-job', { acquireAttemptsLimit: 1 }, async () => {
        await myProtectedTask();
    });
    ```