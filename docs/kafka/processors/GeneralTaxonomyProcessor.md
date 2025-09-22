Here is the comprehensive documentation for the methods and functions found in the provided file:

---

### Function: `getTagWeight`

*   **Description**: Retrieves the numerical weight associated with a given tag type string, defaulting to `TagWeight.Default` (0) if the type is not found.
*   **Call Stack**:
    *   **Called by**:
        *   `GeneralTaxonomyProcessor.processMessage`: Processes an incoming `TaxonomyMessage` to upsert tag configuration.
    *   **Calls**:
        *   `getEnumByValue`: (External function)
*   **Example Usage**:
    ```typescript
    import { TagWeight } from './your-file-name'; // Assuming this file's name for import

    const leagueWeight = getTagWeight('League'); // Returns 1000
    const teamWeight = getTagWeight('Team');     // Returns 600
    const unknownWeight = getTagWeight('NonExistentType'); // Returns 0 (TagWeight.Default)
    ```

---

### Method: `GeneralTaxonomyProcessor.processMessage`

*   **Description**: Processes an incoming `TaxonomyMessage`, extracts the tag's UUID, type, and calculates its weight, then upserts this information into the taxonomy configuration service.
*   **Call Stack**:
    *   **Called by**: (None identifiable in the provided file, typically called by an external message consumer.)
    *   **Calls**:
        *   `logger.debug`: (External function) Logs debug information.
        *   `logger.error`: (External function) Logs error information.
        *   `getTagWeight`: Retrieves the numerical weight associated with a given tag type string.
        *   `TaxonomyConfigurationService.upsertTagInformation`: (External method) Stores or updates tag configuration information.
        *   `logger.info`: (External function) Logs informational messages.
*   **Example Usage**:
    ```typescript
    import { GeneralTaxonomyProcessor } from './your-file-name'; // Assuming this file's name for import
    import { TaxonomyMessage } from '../../wbd/protobuf/messagebus/taxonomyevent/v4/taxonomy_pb';

    // Create a mock TaxonomyMessage
    const mockTaxonomyMessage = new TaxonomyMessage();
    // Simulate setting the nested properties of the protobuf message
    // Note: Protobuf message setters can vary based on generated code.
    // This is a common pattern.
    mockTaxonomyMessage.setTaxonomy({
      id: { id: 'a1b2c3d4-e5f6-7890-1234-567890abcdef' },
      kind: 'League',
    });

    const processor = new GeneralTaxonomyProcessor();
    await processor.processMessage(mockTaxonomyMessage);

    // Or using the exported instance directly:
    // import { generalTaxonomyProcessor } from './your-file-name';
    // await generalTaxonomyProcessor.processMessage(mockTaxonomyMessage);
    ```