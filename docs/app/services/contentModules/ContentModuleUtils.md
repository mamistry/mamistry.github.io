# Documentation for `createCompositeContentsFromStandaloneContentModules`

---

### Method Name: `createCompositeContentsFromStandaloneContentModules`

**Description**: Transforms an array of standalone content modules into an array of composite modules, associating them with a given composite and assigning an initial position.

**Call Stack**:

*   **Called By**:
    *   *(Not identifiable from the provided file)*
*   **Calls**:
    *   `cloneDeep` (from `lodash`): Creates a deep copy of the `standaloneModule` to prevent circular dependency issues when assigning it to `Module` within the `TCompositeModule`.
    *   `Array.prototype.map`: Iterates over the `standaloneModules` array, applying a transformation function to each element to create a new `TCompositeModule` array.

**Example Usage**:

```typescript
import { TComposite, TStandaloneContentModule, TCompositeModule } from './ContentModuleTypes';
import { createCompositeContentsFromStandaloneContentModules } from './your-file-name'; // Assuming this is the file name

const myComposite: TComposite = {
  id: 'composite-123',
  name: 'My Awesome Composite',
  // ... other TComposite properties
};

const standaloneModule1: TStandaloneContentModule = {
  id: 'module-a',
  title: 'Introduction',
  type: 'text',
  content: '...',
  // ... other TStandaloneContentModule properties
};

const standaloneModule2: TStandaloneContentModule = {
  id: 'module-b',
  title: 'Conclusion',
  type: 'image',
  content: '...',
  // ... other TStandaloneContentModule properties
};

const standaloneModulesArray: TStandaloneContentModule[] = [
  standaloneModule1,
  standaloneModule2,
];

const compositeContents: TCompositeModule[] = createCompositeContentsFromStandaloneContentModules({
  composite: myComposite,
  standaloneModules: standaloneModulesArray,
});

console.log(compositeContents);
/* Expected output (simplified):
[
  {
    Module: { id: 'module-a', title: 'Introduction', /* ...deep copy... * / },
    compositeId: 'composite-123',
    moduleId: 'module-a',
    position: 1,
    isPositionLocked: false,
    positionLockExpiresAt: null,
  },
  {
    Module: { id: 'module-b', title: 'Conclusion', /* ...deep copy... * / },
    compositeId: 'composite-123',
    moduleId: 'module-b',
    position: 2,
    isPositionLocked: false,
    positionLockExpiresAt: null,
  }
]
*/
```