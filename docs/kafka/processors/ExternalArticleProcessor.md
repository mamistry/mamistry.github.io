Here's the comprehensive documentation for the methods found in the provided file:

---

## `ExternalArticleProcessor.validateMessage`

*   **Method Name**: `validateMessage`
*   **Description**: Validates an incoming `ContentCommandMessage` for external article processing, extracts relevant data, and fetches embedder metadata.
*   **Call Stack**:
    *   **Called by**:
        *   `ExternalArticleProcessor.processMessage`: Orchestrates the main processing logic for an external article message.
    *   **Calls**:
        *   `Date.now()`: Returns the current time in milliseconds.
        *   `HashUtils.fromHash(url)`: Converts a hashed URL back to its original form.
        *   `embedderService.getEmbedderMetadata(url)`: Fetches rich metadata for a given URL from an external embedder service.
        *   `BmmUtils.getTagUUIDsFromTaxonomyReferenceGroups(taxonomyReferenceGroups)`: Extracts tag UUIDs from a list of taxonomy reference groups.
        *   `logger.info(...)`: Logs informational messages.
*   **Example Usage**:
    ```typescript
    import { ContentCommandMessage } from '../../wbd/protobuf/messagebus/contentcommandevent/v4/contentcommand_pb';
    import { externalArticleProcessor } from './your-file-path'; // Adjust path as needed

    const message: ContentCommandMessage = new ContentCommandMessage();
    // Populate message with necessary data for an external article command
    message.setContentCommand({
      contentId: { id: "hashOfUrl", namespace: "br:some:service:name" },
      taxonomyReferenceGroups: [/* ... */]
    });

    try {
      const validatedData = await externalArticleProcessor.validateMessage(message);
      console.log(`Validated URL: ${validatedData.url}, Title: ${validatedData.title}`);
    } catch (error) {
      console.error("Validation failed:", error);
    }
    ```

---

## `ExternalArticleProcessor.alreadyProcessedTags`

*   **Method Name**: `alreadyProcessedTags`
*   **Description**: Checks the database to identify which of the given tag UUIDs have already been processed for a specific external article URL.
*   **Call Stack**:
    *   **Called by**:
        *   `ExternalArticleProcessor.processMessage`: Orchestrates the main processing logic for an external article message.
    *   **Calls**:
        *   `Date.now()`: Returns the current time in milliseconds.
        *   `replicaConn.getConn()`: Retrieves a Prisma client instance configured for read-only operations.
        *   `prismaReplica.module.findMany(...)`: Queries the database for existing content modules that match the provided URL, content type, and any of the given tag UUIDs.
        *   `logger.info(...)`: Logs informational messages.
*   **Example Usage**:
    ```typescript
    import { externalArticleProcessor } from './your-file-path'; // Adjust path as needed

    const articleUrl = "https://example.com/some-article";
    const potentialTagUUIDs = ["tag-uuid-1", "tag-uuid-2", "tag-uuid-3"];

    const processedTags = await externalArticleProcessor.alreadyProcessedTags(articleUrl, potentialTagUUIDs);
    console.log("Tags already processed for this article:", processedTags);
    // Example output: ["tag-uuid-1", "tag-uuid-3"]
    ```

---

## `ExternalArticleProcessor.processMessage`

*   **Method Name**: `processMessage`
*   **Description**: Processes an incoming `ContentCommandMessage` for an external article by validating it, checking for already processed tags, and creating new standalone content modules for any unprocessed tags.
*   **Call Stack**:
    *   **Called by**: (This method is the primary public entry point of the `ExternalArticleProcessor` class, typically called by an external service or handler after the `externalArticleProcessor` instance is initialized.)
    *   **Calls**:
        *   `Date.now()`: Returns the current time in milliseconds.
        *   `ExternalArticleProcessor.validateMessage(message)`: Validates the incoming content command message and extracts necessary article data.
        *   `ExternalArticleProcessor.alreadyProcessedTags(url, tagUUIDs)`: Identifies which tags for the given article have already been processed and exist in the database.
        *   `createStandalone(...)`: Creates a new standalone content module in the database.
        *   `logger.info(...)`: Logs informational messages.
        *   `logger.error(...)`: Logs error messages, particularly in the catch block.
*   **Example Usage**:
    ```typescript
    import { ContentCommandMessage } from '../../wbd/protobuf/messagebus/contentcommandevent/v4/contentcommand_pb';
    import { externalArticleProcessor } from './your-file-path'; // Adjust path as needed

    const message: ContentCommandMessage = new ContentCommandMessage();
    // Populate message with complete content command data for an external article
    message.setContentCommand({
      id: { namespace: "br:test:service:example" },
      contentId: { id: "hashOfUrl", namespace: "br:some:service:name" },
      taxonomyReferenceGroups: [
        { /* group 1 */ },
        { /* group 2 */ }
      ]
    });

    try {
      const updatedContentModules = await externalArticleProcessor.processMessage(message);
      if (updatedContentModules.length > 0) {
        console.log(`Successfully created ${updatedContentModules.length} new content modules.`);
      } else {
        console.log("No new content modules were created (all tags already processed or no tags).");
      }
    } catch (error) {
      console.error("An error occurred during external article processing:", error);
    }
    ```