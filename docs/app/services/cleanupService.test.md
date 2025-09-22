Here is the comprehensive documentation for the methods and functions defined in the provided file:

---

### Method Name: `beforeEach_callback`

*   **Description**: Resets the database to a clean state and clears all Jest mocks before each test run.
*   **Call Stack**:
    *   Calls `cleanUpDatabase()`: A utility function from `testHelper` that clears database tables.
    *   Calls `jest.clearAllMocks()`: A Jest utility that resets the mock state of all mocks.
*   **Example Usage**:
    ```typescript
    beforeEach(async () => {
      await cleanUpDatabase();
      jest.clearAllMocks();
    });
    ```

---

### Method Name: `afterAll_callback`

*   **Description**: Disconnects the Prisma client from the database after all tests in the suite have completed.
*   **Call Stack**:
    *   Calls `prisma.$disconnect()`: Disconnects the Prisma client instance from the database.
*   **Example Usage**:
    ```typescript
    afterAll(async () => {
      await prisma.$disconnect();
    });
    ```

---

### Method Name: `createTestModule`

*   **Description**: A helper function to create a new module in the database, including its associated `Composite` and `ComponentModule` records.
*   **Call Stack**:
    *   Called by `it_should_delete_duplicate_scheduled_highlights_packages`.
    *   Calls `prisma.module.create()`: Creates a new module record and related composite/component-module records in the database.
*   **Example Usage**:
    ```typescript
    await createTestModule('module-a-comp1', component1.id, PackageType.Highlights, t1);
    ```

---

### Method Name: `it_should_delete_duplicate_scheduled_highlights_packages`

*   **Description**: An integration test that verifies the `deleteDuplicateScheduledHighlightsPackages` service correctly identifies and deletes duplicate Highlights packages, preserving the oldest entry for each component.
*   **Call Stack**:
    *   Calls `prisma.component.create()`: Creates component records in the database for testing.
    *   Calls `createTestModule()`: Creates various test module records, including duplicates, for specific components.
    *   Calls `deleteDuplicateScheduledHighlightsPackages()`: The service function under test, imported from `./cleanupService`.
    *   Calls `expect().toHaveLength()`: Jest assertion to check the number of items in an array.
    *   Calls `expect().toEqual()`: Jest assertion to perform a deep equality check on values.
    *   Calls `expect.arrayContaining()`: Jest matcher to check if an array contains all elements of another array.
*   **Example Usage**:
    ```typescript
    it('should delete duplicate scheduled Highlights packages, keeping the oldest per component', async () => {
      // ... data seeding with createTestModule ...
      const { scheduledForDeletionIds } = await deleteDuplicateScheduledHighlightsPackages();
      expect(scheduledForDeletionIds).toHaveLength(3);
      expect(scheduledForDeletionIds).toEqual(
        expect.arrayContaining(['module-b-comp1', 'module-c-comp1', 'module-d-comp1'])
      );
    });
    ```

---

### Method Name: `it_should_return_an_empty_array_if_no_content_highlights_components_exist`

*   **Description**: An integration test that ensures the `deleteDuplicateScheduledHighlightsPackages` service returns an empty array when no `CONTENT_HIGHLIGHTS` components are found in the database.
*   **Call Stack**:
    *   Calls `cleanUpDatabase()`: Ensures the database is empty before running the test.
    *   Calls `deleteDuplicateScheduledHighlightsPackages()`: The service function under test, imported from `./cleanupService`.
    *   Calls `expect().toEqual()`: Jest assertion to check if the result is an empty array.
    *   Calls `prisma.module.count()`: Queries the database to count existing modules.
    *   Calls `expect().toBe()`: Jest assertion to check for strict equality of values.
*   **Example Usage**:
    ```typescript
    it('should return an empty array if no ContentHighlights components exist', async () => {
      await cleanUpDatabase();
      const result = await deleteDuplicateScheduledHighlightsPackages();
      expect(result.scheduledForDeletionIds).toEqual([]);
      const moduleCount = await prisma.module.count();
      expect(moduleCount).toBe(0);
    });
    ```

---

### Method Name: `it_should_return_an_empty_array_if_no_potential_duplicate_modules_are_found`

*   **Description**: An integration test that verifies the `deleteDuplicateScheduledHighlightsPackages` service returns an empty array if modules exist but none meet the criteria to be considered duplicates for deletion.
*   **Call Stack**:
    *   Calls `prisma.component.create()`: Creates a test component.
    *   Calls `prisma.module.create()`: Creates test modules with package types or dates that do not qualify as duplicates.
    *   Calls `deleteDuplicateScheduledHighlightsPackages()`: The service function under test, imported from `./cleanupService`.
    *   Calls `expect().toEqual()`: Jest assertion to check if the result is an empty array.
*   **Example Usage**:
    ```typescript
    it('should return an empty array if no potential duplicate modules are found', async () => {
      // ... setup code with non-duplicate modules ...
      const result = await deleteDuplicateScheduledHighlightsPackages();
      expect(result.scheduledForDeletionIds).toEqual([]);
    });
    ```

---

### Method Name: `it_should_return_an_empty_array_if_modules_are_found_but_none_are_duplicates`

*   **Description**: An integration test that confirms the `deleteDuplicateScheduledHighlightsPackages` service returns an empty array when multiple modules exist, but each component has only one unique Highlights package, meaning no duplicates are present.
*   **Call Stack**:
    *   Calls `prisma.component.create()`: Creates multiple test components.
    *   Calls `prisma.module.create()`: Creates one valid, non-duplicate Highlights module for each component.
    *   Calls `deleteDuplicateScheduledHighlightsPackages()`: The service function under test, imported from `./cleanupService`.
    *   Calls `expect().toEqual()`: Jest assertion to check if the result is an empty array.
*   **Example Usage**:
    ```typescript
    it('should return an empty array if modules are found but none are duplicates', async () => {
      // ... setup code with unique modules per component ...
      const result = await deleteDuplicateScheduledHighlightsPackages();
      expect(result.scheduledForDeletionIds).toEqual([]);
    });
    ```