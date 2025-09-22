Here is the comprehensive documentation for the methods found in the provided file.

---

### `mapDBFindManyResultListWithIncludeToModel`

*   **Description**: Maps a list of raw Prisma content module payloads, including related data (composites and components), to a list of `TContentModule` models.
*   **Call Stack**:
    *   **Called by**: (Not identifiable from the provided code)
    *   **Calls**:
        *   `this.mapDBResultWithIncludeToModel`: Maps a single raw content module with its included relations to a `TContentModule` model.
*   **Example Usage**:
    ```typescript
    const service = new ContentModuleDTOService();
    const rawModulesWithInclude = [
      // ... array of TModuleFindManyReturnPayloadWithInclude & { metaData?: TModuleMetaData | null; }
      {
        id: 'module-1-id',
        type: ModuleType.Standalone,
        // ... other properties and includes
      },
      {
        id: 'module-2-id',
        type: ModuleType.Package,
        Composite: { /* ... */ },
        // ... other properties and includes
      },
    ];
    const contentModules = service.mapDBFindManyResultListWithIncludeToModel(rawModulesWithInclude);
    // contentModules will be an array of TContentModule
    ```

### `mapDBFindManyResultListToModel`

*   **Description**: Maps a list of raw Prisma content module payloads without detailed inclusions to a list of `TContentModule` models.
*   **Call Stack**:
    *   **Called by**: (Not identifiable from the provided code)
    *   **Calls**:
        *   `this.mapDBResultToModel`: Maps a single raw content module to a `TContentModule` model.
*   **Example Usage**:
    ```typescript
    const service = new ContentModuleDTOService();
    const rawModules = [
      // ... array of Prisma.ModuleGetPayload<boolean> & { metaData?: TModuleMetaData | null; }
      {
        id: 'module-a-id',
        type: ModuleType.Standalone,
        title: 'Title A',
        // ... other basic properties
      },
      {
        id: 'module-b-id',
        type: ModuleType.Package,
        title: 'Title B',
        // ... other basic properties
      },
    ];
    const contentModules = service.mapDBFindManyResultListToModel(rawModules);
    // contentModules will be an array of TContentModule
    ```

### `mapDBResultToModel`

*   **Description**: Maps a single raw Prisma content module payload to a `TContentModule` model, handling both standalone and composite types.
*   **Call Stack**:
    *   **Called by**:
        *   `mapDBFindManyResultListToModel`: Iterates over a list of raw modules and maps each one.
    *   **Calls**:
        *   `ContentModuleMockFactory.composite`: (External) Creates a mock `TComposite` object for package type modules.
*   **Example Usage**:
    ```typescript
    const service = new ContentModuleDTOService();
    const rawStandaloneModule = {
      id: 'single-module-id',
      type: ModuleType.Standalone,
      title: 'A Standalone Module',
      contentId: 'abc',
      // ... other raw module properties
    };
    const standaloneContentModule = service.mapDBResultToModel(rawStandaloneModule);
    // standaloneContentModule will be a TContentModule with type ModuleType.Standalone

    const rawPackageModule = {
      id: 'package-module-id',
      type: ModuleType.Package,
      title: 'A Package Module',
      contentType: 'Mixed',
      // ... other raw module properties
    };
    const packageContentModule = service.mapDBResultToModel(rawPackageModule);
    // packageContentModule will be a TContentModule with type ModuleType.Package and a mock Composite
    ```

### `mapDBResultWithIncludeToModel`

*   **Description**: Maps a single raw Prisma content module payload, which includes its related `composites` or `Composite` data, to a `TContentModule` model.
*   **Call Stack**:
    *   **Called by**:
        *   `mapDBFindManyResultListWithIncludeToModel`: Iterates over a list of raw modules with includes and maps each one.
    *   **Calls**:
        *   `getEnumByValue`: (External) Retrieves an enum member by its string value.
*   **Example Usage**:
    ```typescript
    const service = new ContentModuleDTOService();
    const rawStandaloneWithComposites = {
      id: 'module-id-1',
      type: ModuleType.Standalone,
      title: 'Parent Module',
      composites: [
        {
          compositeId: 101,
          moduleId: 'child-module-id',
          position: 1,
          Module: {
            id: 'child-module-id',
            type: ModuleType.Standalone,
            title: 'Child Module',
            // ...
          },
        },
      ],
      // ... other raw module properties with includes
    };
    const contentModuleWithComposites = service.mapDBResultWithIncludeToModel(rawStandaloneWithComposites);
    // contentModuleWithComposites will be a TContentModule with composites array populated

    const rawPackageWithContents = {
      id: 'module-id-2',
      type: ModuleType.Package,
      title: 'A Package Module',
      Composite: {
        id: 201,
        contentType: 'Mixed',
        packageType: 'Default',
        contents: [
          {
            moduleId: 'component-a-id',
            position: 1,
            Module: { id: 'component-a-id', type: ModuleType.Standalone, title: 'Component A' },
          },
        ],
      },
      // ... other raw module properties with includes
    };
    const packageContentModule = service.mapDBResultWithIncludeToModel(rawPackageWithContents);
    // packageContentModule will be a TContentModule with Composite and its contents populated
    ```

### `mapDBCompositeModuleResultListToModel`

*   **Description**: Maps a list of raw `TCompositeModulePayload` objects (representing modules within a composite) to a list of `TCompositeModule` models.
*   **Call Stack**:
    *   **Called by**: (Not identifiable from the provided code)
    *   **Calls**:
        *   `this.mapDBCompositeModuleToModel`: Maps a single raw `TCompositeModulePayload` to a `TCompositeModule` model.
*   **Example Usage**:
    ```typescript
    const service = new ContentModuleDTOService();
    const rawCompositeModulesPayload = [
      // ... array of TCompositeModulePayload
      {
        compositeId: 1,
        moduleId: 'mod-1',
        position: 1,
        Module: { id: 'mod-1', type: ModuleType.Standalone, title: 'Module One' },
      },
      {
        compositeId: 1,
        moduleId: 'mod-2',
        position: 2,
        Module: { id: 'mod-2', type: ModuleType.Standalone, title: 'Module Two' },
      },
    ];
    const compositeModules = service.mapDBCompositeModuleResultListToModel(rawCompositeModulesPayload);
    // compositeModules will be an array of TCompositeModule
    ```

### `mapDBCompositeModuleToModel`

*   **Description**: Maps a single raw `TCompositeModulePayload` object (representing a module within a composite) to a `TCompositeModule` model.
*   **Call Stack**:
    *   **Called by**:
        *   `mapDBCompositeModuleResultListToModel`: Iterates over a list of raw composite module payloads and maps each one.
    *   **Calls**: (None identifiable from the provided code)
*   **Example Usage**:
    ```typescript
    const service = new ContentModuleDTOService();
    const rawCompositeModulePayload = {
      compositeId: 123,
      moduleId: 'component-module-id-xyz',
      position: 5,
      isPositionLocked: true,
      positionLockExpiresAt: new Date('2024-12-31T23:59:59Z'),
      excludedAt: null,
      Module: {
        id: 'component-module-id-xyz',
        type: ModuleType.Standalone,
        commentsEnabled: true,
        title: 'Component Title',
        // ... other module properties
      },
    };
    const compositeModule = service.mapDBCompositeModuleToModel(rawCompositeModulePayload);
    // compositeModule will be a TCompositeModule
    ```