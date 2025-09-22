Here is the comprehensive documentation for the methods found in the provided file.

---

### Method Name: `createStandaloneContentModule`
*   **Description**: Creates a new standalone content module, records a composite creation event, and returns its serialized representation.
*   **Call Stack**:
    *   **Called By**: GraphQL Engine (as a `Mutation` resolver).
    *   **Calls**:
        *   `getCountryCode`: Extracts the country code from the provided session context.
        *   `createStandalone`: Creates a new standalone content module in the database.
        *   `sendCompositeCreateEvent`: Sends an event to a metadata service, indicating the creation of a composite content module.
        *   `serializeContentModule`: Serializes the content module object into a format suitable for the API response.
        *   `logger.error`: Logs error messages if an exception occurs.
*   **Example Usage**:
    ```graphql
    mutation CreateStandaloneModule {
      createStandaloneContentModule(
        title: "My New Article",
        type: ARTICLE,
        externalId: "ext-article-001",
        channels: [{ tagUUID: "channel-uuid-1" }]
      ) {
        id
        title
        type
        status
      }
    }
    ```

---

### Method Name: `updateStandaloneContentModule`
*   **Description**: Updates an existing standalone content module, records a composite creation/update event, and returns its serialized representation.
*   **Call Stack**:
    *   **Called By**: GraphQL Engine (as a `Mutation` resolver).
    *   **Calls**:
        *   `getCountryCode`: Extracts the country code from the provided session context.
        *   `updateStandalone`: Updates an existing standalone content module in the database.
        *   `sendCompositeCreateEvent`: Sends an event to a metadata service, indicating the update of a composite content module.
        *   `serializeContentModule`: Serializes the content module object into a format suitable for the API response.
        *   `logger.error`: Logs error messages if an exception occurs.
*   **Example Usage**:
    ```graphql
    mutation UpdateStandaloneModule {
      updateStandaloneContentModule(
        id: "standalone-module-id-123",
        title: "Updated Standalone Title",
        status: PUBLISHED
      ) {
        id
        title
        status
      }
    }
    ```

---

### Method Name: `createPackageContentModule`
*   **Description**: Creates a new package content module, records a composite creation event, and returns its serialized representation.
*   **Call Stack**:
    *   **Called By**: GraphQL Engine (as a `Mutation` resolver).
    *   **Calls**:
        *   `getCountryCode`: Extracts the country code from the provided session context.
        *   `createPackage`: Creates a new package content module in the database.
        *   `sendCompositeCreateEvent`: Sends an event to a metadata service, indicating the creation of a composite content module.
        *   `serializeContentModule`: Serializes the content module object into a format suitable for the API response.
        *   `logger.error`: Logs error messages if an exception occurs.
*   **Example Usage**:
    ```graphql
    mutation CreatePackageModule {
      createPackageContentModule(
        title: "New Featured Highlights Package",
        type: HIGHLIGHTS,
        channels: [{ tagUUID: "package-channel-uuid-1" }]
      ) {
        id
        title
        type
      }
    }
    ```

---

### Method Name: `updatePackageContentModule`
*   **Description**: Updates an existing package content module, records a composite creation/update event, and returns its serialized representation.
*   **Call Stack**:
    *   **Called By**: GraphQL Engine (as a `Mutation` resolver).
    *   **Calls**:
        *   `getCountryCode`: Extracts the country code from the provided session context.
        *   `updatePackage`: Updates an existing package content module in the database.
        *   `sendCompositeCreateEvent`: Sends an event to a metadata service, indicating the update of a composite content module.
        *   `serializeContentModule`: Serializes the content module object into a format suitable for the API response.
        *   `logger.error`: Logs error messages if an exception occurs.
*   **Example Usage**:
    ```graphql
    mutation UpdatePackageModule {
      updatePackageContentModule(
        id: "package-module-id-456",
        title: "Revised Featured Highlights Package",
        status: ARCHIVED
      ) {
        id
        title
        status
      }
    }
    ```

---

### Method Name: `addContentToPackage`
*   **Description**: Adds new content components to an existing package content module, records a composite update event, and returns the serialized package.
*   **Call Stack**:
    *   **Called By**: GraphQL Engine (as a `Mutation` resolver).
    *   **Calls**:
        *   `getCountryCode`: Extracts the country code from the provided session context.
        *   `addContentToPackage`: Adds specified content components to the given package module in the database.
        *   `sendCompositeCreateEvent`: Sends an event to a metadata service, indicating the update of a composite content module.
        *   `serializeContentModule`: Serializes the updated package content module object for the API response.
        *   `logger.error`: Logs error messages if an exception occurs.
*   **Example Usage**:
    ```graphql
    mutation AddComponentsToPackage {
      addContentToPackage(
        packageContentModuleId: "package-module-id-789",
        components: [{
          tagUUID: "component-uuid-1",
          componentType: ARTICLE,
          position: 0
        }, {
          tagUUID: "component-uuid-2",
          componentType: VIDEO,
          position: 1
        }]
      ) {
        id
        components {
          Component {
            tagUUID
          }
        }
      }
    }
    ```

---

### Method Name: `deleteContentModule`
*   **Description**: Deletes a content module (standalone or package) by its ID and sends a composite deletion event.
*   **Call Stack**:
    *   **Called By**: GraphQL Engine (as a `Mutation` resolver).
    *   **Calls**:
        *   `deleteContentModule`: Deletes the specified content module from the database.
        *   `sendDeleteCompositeEvent`: Sends an event to a metadata service, indicating the deletion of a composite content module.
        *   `logger.error`: Logs error messages if an exception occurs.
*   **Example Usage**:
    ```graphql
    mutation DeleteModule {
      deleteContentModule(id: "module-id-to-delete-123")
    }
    ```

---

### Method Name: `upsertStandaloneContentModules`
*   **Description**: Performs bulk creation, update, and deletion operations for multiple standalone content modules, sending corresponding composite events, and returns the serialized results.
*   **Call Stack**:
    *   **Called By**: GraphQL Engine (as a `Mutation` resolver).
    *   **Calls**:
        *   `logger.info`: Logs informational messages during execution.
        *   `ContentModuleValidationError`: Throws a validation error if neither data for upsert nor IDs for deletion are provided.
        *   `getCountryCode`: Extracts the country code from the provided session context.
        *   `upsertStandaloneContentModules`: Executes the bulk upsert and delete operations for standalone modules in the database.
        *   `sendCompositeCreateEvent`: Sends events for each created or updated composite content module.
        *   `sendDeleteCompositeEvent`: Sends events for each deleted composite content module.
        *   `serializeContentModule`: Serializes the resulting content module objects for the API response.
        *   `logger.error`: Logs error messages if an exception occurs.
*   **Example Usage**:
    ```graphql
    mutation BulkUpsertStandalone {
      upsertStandaloneContentModules(
        data: [
          { id: "s-1", title: "Updated Standalone 1", type: ARTICLE },
          { title: "New Standalone 3", type: VIDEO, externalId: "v-3" }
        ],
        delete: ["s-2"]
      ) {
        id
        title
        status
      }
    }
    ```

---

### Method Name: `createExternalArticle`
*   **Description**: Finds an existing external article or creates a new one using the external article service.
*   **Call Stack**:
    *   **Called By**: GraphQL Engine (as a `Mutation` resolver).
    *   **Calls**:
        *   `externalArticleService.findOrCreate`: A service method responsible for finding an external article by ID or creating it if it doesn't exist.
        *   `logger.error`: Logs error messages if an exception occurs.
*   **Example Usage**:
    ```graphql
    mutation CreateOrFindArticle {
      createExternalArticle(
        externalId: "ext-article-456",
        title: "Breaking News Story",
        url: "https://example.com/breaking-news",
        type: ARTICLE
      ) {
        id
        title
        url
      }
    }
    ```

---

### Method Name: `createStaticPackages`
*   **Description**: Creates multiple static content packages and returns their serialized forms.
*   **Call Stack**:
    *   **Called By**: GraphQL Engine (as a `Mutation` resolver).
    *   **Calls**:
        *   `getCountryCode`: Extracts the country code from the provided session context.
        *   `createStaticPackages`: Creates multiple static content packages in the database.
        *   `serializeContentModule`: Serializes each created package content module for the API response.
        *   `logger.error`: Logs error messages if an exception occurs.
*   **Example Usage**:
    ```graphql
    mutation CreateMultipleStaticPackages {
      createStaticPackages(
        data: [{
          title: "Static Package Alpha",
          type: HIGHLIGHTS,
          status: PUBLISHED
        }, {
          title: "Static Package Beta",
          type: SCHEDULED_HIGHLIGHTS
        }]
      ) {
        id
        title
        type
        status
      }
    }
    ```

---

### Method Name: `deleteContentFromPackage`
*   **Description**: Deletes specific content components from a package content module.
*   **Call Stack**:
    *   **Called By**: GraphQL Engine (as a `Mutation` resolver).
    *   **Calls**:
        *   `deleteContentFromPackageControl`: Handles the logic for deleting content components from a package.
        *   `logger.error`: Logs error messages if an exception occurs.
*   **Example Usage**:
    ```graphql
    mutation RemoveComponents {
      deleteContentFromPackage(
        packageContentModuleId: "package-module-id-XYZ",
        componentIds: ["component-id-A", "component-id-B"]
      )
    }
    ```

---

### Method Name: `upsertPackageContentModules`
*   **Description**: Performs bulk creation, update, and deletion operations for multiple package content modules, sending corresponding composite events, and returns the serialized results.
*   **Call Stack**:
    *   **Called By**: GraphQL Engine (as a `Mutation` resolver).
    *   **Calls**:
        *   `ContentModuleValidationError`: Throws a validation error if neither packages for upsert nor IDs for deletion are provided.
        *   `getCountryCode`: Extracts the country code from the provided session context.
        *   `upsertPackageContentModules`: Executes the bulk upsert and delete operations for package modules in the database.
        *   `sendCompositeCreateEvent`: Sends events for each created or updated composite content module.
        *   `sendDeleteCompositeEvent`: Sends events for each deleted composite content module.
        *   `serializeContentModule`: Serializes the resulting content module objects for the API response.
        *   `logger.error`: Logs error messages if an exception occurs.
*   **Example Usage**:
    ```graphql
    mutation BulkUpsertPackages {
      upsertPackageContentModules(
        packages: [
          { id: "p-1", title: "Updated Package 1", type: CURATED_EDITORIAL },
          { title: "New Package 2", type: HIGHLIGHTS }
        ],
        delete: ["p-3"]
      ) {
        id
        title
        type
      }
    }
    ```

---

### Method Name: `upsertVideoState`
*   **Description**: Creates or updates the metadata for a video, specifically its state, and returns the updated state.
*   **Call Stack**:
    *   **Called By**: GraphQL Engine (as a `Mutation` resolver).
    *   **Calls**:
        *   `VideoMetadata.upsertVideoMetadata`: A static method to create or update video metadata entries in the database.
        *   `logger.error`: Logs error messages if an exception occurs.
*   **Example Usage**:
    ```graphql
    mutation SetVideoStatus {
      upsertVideoState(
        data: {
          externalId: "video-external-id-123",
          videoState: PUBLISHED,
          title: "My Great Video",
          duration: 300
        }
      ) {
        state
      }
    }
    ```

---

### Method Name: `deleteDuplicateScheduledHighlightsPackages`
*   **Description**: Initiates a cleanup process to identify and delete duplicate scheduled highlights packages.
*   **Call Stack**:
    *   **Called By**: GraphQL Engine (as a `Mutation` resolver).
    *   **Calls**:
        *   `deleteDuplicateScheduledHighlightsPackages`: A service function that contains the logic for finding and deleting duplicate packages.
        *   `logger.error`: Logs error messages if an exception occurs.
*   **Example Usage**:
    ```graphql
    mutation RunDuplicateCleanup {
      deleteDuplicateScheduledHighlightsPackages
    }
    ```