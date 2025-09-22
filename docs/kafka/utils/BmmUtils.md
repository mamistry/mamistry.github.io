Here is the comprehensive documentation for the methods found within the provided file.

---

### Method Name: `getTagUUIDsFromTaxonomyReferenceGroups`

*   **Description**: Extracts a list of `taxonomyId` UUIDs from an array of `TaxonomyReferenceGroup` objects, including potential duplicates if present across groups or references.
*   **Call Stack**:
    *   **Called By**:
        *   *(Not called by any function within this file)*
    *   **Calls**:
        *   `group.taxonomyReferences`: Accesses the list of taxonomy references within a group.
        *   `ref.taxonomyId?.id`: Safely accesses the ID property of a taxonomy reference's taxonomy ID.
*   **Example Usage**:

    ```typescript
    import { TaxonomyReference, TaxonomyReferenceGroup } from '../../wbd/protobuf/metadata/common/v4/model_pb';
    import { BmmUtils } from './your-file-path'; // Adjust path as necessary

    const mockTaxonomyRef1 = new TaxonomyReference();
    mockTaxonomyRef1.taxonomyId = { id: 'c90c74b8-f80e-436d-927d-9481971775f0' };

    const mockTaxonomyRef2 = new TaxonomyReference();
    mockTaxonomyRef2.taxonomyId = { id: 'a5e6d0a7-1b3c-4d5e-8f9a-1234567890ab' };

    const mockGroup1 = new TaxonomyReferenceGroup();
    mockGroup1.taxonomyReferences = [mockTaxonomyRef1, mockTaxonomyRef2];

    const mockGroup2 = new TaxonomyReferenceGroup();
    mockGroup2.taxonomyReferences = [mockTaxonomyRef1]; // Re-using a reference

    const groups: TaxonomyReferenceGroup[] = [mockGroup1, mockGroup2];
    const tagUUIDs = BmmUtils.getTagUUIDsFromTaxonomyReferenceGroups(groups);

    console.log(tagUUIDs);
    // Expected output:
    // [
    //   'c90c74b8-f80e-436d-927d-9481971775f0',
    //   'a5e6d0a7-1b3c-4d5e-8f9a-1234567890ab',
    //   'c90c74b8-f80e-436d-927d-9481971775f0'
    // ]
    ```

---

### Method Name: `getContentTypeFromWidgetType`

*   **Description**: Maps a string `widgetType` identifier to its corresponding `ContentModuleType` enum value.
*   **Call Stack**:
    *   **Called By**:
        *   *(Not called by any function within this file)*
    *   **Calls**:
        *   `ContentModuleType.UgcTextPoll`: A specific enum value representing a text poll content type.
        *   `ContentModuleType.UgcImagePoll`: A specific enum value representing an image poll content type.
        *   `ContentModuleType.UgcPost`: A specific enum value representing a user-generated post content type.
        *   `Error`: Standard JavaScript `Error` constructor, used to throw an exception for invalid types.
*   **Example Usage**:

    ```typescript
    import { ContentModuleType } from '../../graphql/generated/graphql';
    import { BmmUtils } from './your-file-path'; // Adjust path as necessary

    const pollType = BmmUtils.getContentTypeFromWidgetType('poll');
    console.log(`Poll type: ${pollType} (is UGC Text Poll? ${pollType === ContentModuleType.UgcTextPoll})`);
    // Expected output: Poll type: UgcTextPoll (is UGC Text Poll? true)

    const richPostType = BmmUtils.getContentTypeFromWidgetType('rich_post');
    console.log(`Rich post type: ${richPostType} (is UGC Post? ${richPostType === ContentModuleType.UgcPost})`);
    // Expected output: Rich post type: UgcPost (is UGC Post? true)

    try {
      BmmUtils.getContentTypeFromWidgetType('unsupported_widget');
    } catch (error: any) {
      console.error(`Error: ${error.message}`);
      // Expected output: Error: Invalid widget type
    }
    ```

---

### Method Name: `extractProgrammingOptionsData`

*   **Description**: Transforms an array of `programmingOptions` from a `ContentCommand` into a flattened `ProgrammingOptionsData` object, extracting semantic IDs, positions, lock statuses, and associated tag UUIDs.
*   **Call Stack**:
    *   **Called By**:
        *   *(Not called by any function within this file)*
    *   **Calls**:
        *   `contentCommand.programmingOptions`: Accesses the programming options array from the content command.
        *   `option.taxonomyReferenceGroups`: Accesses the taxonomy reference groups for a given programming option.
        *   `option.semanticId`: Accesses the semantic ID for a programming option.
        *   `option.position`: Accesses the default position for a programming option.
        *   `option.isPositionLocked`: Accesses the position locked status for a programming option.
        *   `group.taxonomyReferences`: Accesses the list of taxonomy references within a taxonomy reference group.
        *   `tax.taxonomyId?.id`: Safely accesses the ID property of a taxonomy reference's taxonomy ID.
        *   `flatMap()`: A standard JavaScript Array method, used to map and flatten the resulting arrays.
        *   `filter()`: A standard JavaScript Array method, used to filter out invalid taxonomy references.
        *   `map()`: A standard JavaScript Array method, used to transform taxonomy references into `ProgrammingOptionItem` objects.
*   **Example Usage**:

    ```typescript
    import { ContentCommand } from '../../wbd/protobuf/metadata/contentcommand/v4/contentcommand_pb';
    import { TaxonomyReferenceGroup, TaxonomyReference } from '../../wbd/protobuf/metadata/common/v4/model_pb';
    import { BmmUtils } from './your-file-path'; // Adjust path as necessary

    // Mock TaxonomyReferences
    const taxRef1 = new TaxonomyReference();
    taxRef1.taxonomyId = { id: 'tag-uuid-A1' };
    const taxRef2 = new TaxonomyReference();
    taxRef2.taxonomyId = { id: 'tag-uuid-B1' };
    const taxRef3 = new TaxonomyReference();
    taxRef3.taxonomyId = { id: 'tag-uuid-B2' };

    // Mock TaxonomyReferenceGroups
    const group1 = new TaxonomyReferenceGroup();
    group1.taxonomyReferences = [taxRef1];
    const group2 = new TaxonomyReferenceGroup();
    group2.taxonomyReferences = [taxRef2, taxRef3];

    // Mock ContentCommand
    const mockContentCommand = new ContentCommand();
    mockContentCommand.programmingOptions = [
      {
        semanticId: 'SEM_ID_ALPHA',
        position: 1,
        isPositionLocked: true,
        taxonomyReferenceGroups: [group1],
      },
      {
        semanticId: 'SEM_ID_BETA',
        // position will default to index + 1 if undefined, here it's 0-indexed relative to taxonomyReferences
        isPositionLocked: false,
        taxonomyReferenceGroups: [group2], // This group has two references
      },
      {
        semanticId: 'SEM_ID_CHARLIE',
        // No taxonomyReferenceGroups, so this option will be filtered out entirely
        taxonomyReferenceGroups: [],
      }
    ];

    const programmingOptionsData = BmmUtils.extractProgrammingOptionsData(mockContentCommand);
    console.log(JSON.stringify(programmingOptionsData, null, 2));

    /*
    Expected output:
    {
      "options": [
        {
          "semanticId": "SEM_ID_ALPHA",
          "position": 1,
          "isPositionLocked": true,
          "tagUUID": "tag-uuid-A1"
        },
        {
          "semanticId": "SEM_ID_BETA",
          "position": 1, // First item in group2's references (index 0 + 1)
          "isPositionLocked": false,
          "tagUUID": "tag-uuid-B1"
        },
        {
          "semanticId": "SEM_ID_BETA",
          "position": 2, // Second item in group2's references (index 1 + 1)
          "isPositionLocked": false,
          "tagUUID": "tag-uuid-B2"
        }
      ]
    }
    */

    const emptyContentCommand = new ContentCommand();
    emptyContentCommand.programmingOptions = [];
    const emptyOptions = BmmUtils.extractProgrammingOptionsData(emptyContentCommand);
    console.log(JSON.stringify(emptyOptions, null, 2));
    // Expected output: { "options": [] }
    ```