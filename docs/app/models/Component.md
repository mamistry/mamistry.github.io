Here is the comprehensive documentation for the methods found in your provided file:

---

### Method Name: `findComponentByTagAndSemanticId`

*   **Description**: Retrieves a unique component record from the database using its tag UUID and semantic ID.
*   **Call Stack**:
    *   **Calls**:
        *   `prismaConn.getConn()`: Gets the Prisma client instance.
        *   `prismaClient.component.findUnique()`: Performs a database query to find a unique component.
*   **Example Usage**:

    ```typescript
    import { findComponentByTagAndSemanticId } from './your-file-name'; // Adjust path as needed

    async function exampleFindComponent() {
      const tag = "your-tag-uuid-here";
      const id = "your-semantic-id-here";
      try {
        const component = await findComponentByTagAndSemanticId(tag, id);
        if (component) {
          console.log("Found component:", component);
        } else {
          console.log("Component not found for tag:", tag, "and semantic ID:", id);
        }
      } catch (error) {
        console.error("Error finding component:", error);
      }
    }

    exampleFindComponent();
    ```

---

### Method Name: `findOrCreateComponent`

*   **Description**: Inserts a new component record into the database or updates its `updatedAt` timestamp if a component with the given tag UUID and semantic ID already exists, then returns the component.
*   **Call Stack**:
    *   **Calls**:
        *   `prismaConn.getConn()`: Gets the Prisma client instance.
        *   `prismaClient.$queryRaw`: Executes a raw SQL query.
*   **Example Usage**:

    ```typescript
    import { findOrCreateComponent } from './your-file-name'; // Adjust path as needed

    async function exampleFindOrCreateComponent() {
      const componentData = {
        tagUUID: "new-or-existing-tag",
        semanticID: "new-or-existing-id",
      };
      try {
        const component = await findOrCreateComponent(componentData);
        console.log("Component found or created:", component);
      } catch (error) {
        console.error("Error finding or creating component:", error);
      }
    }

    exampleFindOrCreateComponent();
    ```