Here is the comprehensive documentation for the methods and functions found in the provided file:

---

### 1. `PrismaConn.constructor`

*   **Description**: Initializes a new `PrismaConn` instance, establishing a connection to the database.
*   **Call Stack**:
    *   `Called by`:
        *   `PrismaConn.getInstance`: Creates a new `PrismaConn` instance if one doesn't already exist.
    *   `Calls`:
        *   `PrismaConn.connect`: Establishes the database connection using the provided `replica` flag.
*   **Example Usage**:
    ```typescript
    // This constructor is private and called internally by `PrismaConn.getInstance`.
    // Example:
    // const instance = new PrismaConn(false); // Not directly callable
    ```

---

### 2. `PrismaConn.getInstance`

*   **Description**: Retrieves the singleton instance of `PrismaConn`, creating it if it doesn't already exist for either the primary or replica connection.
*   **Call Stack**:
    *   `Called by`: (Not identifiable within this file)
    *   `Calls`:
        *   `PrismaConn.constructor`: Initializes a new `PrismaConn` instance when a new connection is needed.
*   **Example Usage**:
    ```typescript
    // Get the primary database connection instance
    const prismaMasterConn = PrismaConn.getInstance();

    // Get the replica database connection instance
    const prismaReplicaConn = PrismaConn.getInstance(true);
    ```

---

### 3. `PrismaConn.getConn`

*   **Description**: Returns the initialized `PrismaClient` instance managed by this connection.
*   **Call Stack**:
    *   `Called by`: (Not identifiable within this file)
    *   `Calls`: (None within this file)
*   **Example Usage**:
    ```typescript
    const prismaClient = PrismaConn.getInstance().getConn();
    // Now you can use prismaClient for database operations:
    // const users = await prismaClient.user.findMany();
    ```

---

### 4. `PrismaConn.connect`

*   **Description**: Establishes an asynchronous connection to the PostgreSQL database and initializes the `PrismaClient` instance.
*   **Call Stack**:
    *   `Called by`:
        *   `PrismaConn.constructor`: Called during the initialization of a `PrismaConn` instance.
    *   `Calls`:
        *   `PrismaConn.buildConnectionString`: Asynchronously constructs the PostgreSQL connection string.
        *   `URLWrapper.constructor`:
        *   `URLWrapper.getParam`:
        *   `Pool.constructor`:
        *   `PrismaPg.constructor`:
        *   `PrismaClient.constructor`:
*   **Example Usage**:
    ```typescript
    // This method is called internally when a new PrismaConn instance is created.
    // Example:
    // PrismaConn.getInstance(); // This indirectly triggers the connect method.
    ```

---

### 5. `PrismaConn.buildConnectionString`

*   **Description**: Constructs the PostgreSQL connection string, either from environment variables for non-production environments or from AWS Secrets Manager for production.
*   **Call Stack**:
    *   `Called by`:
        *   `PrismaConn.connect`: Uses the generated connection string to establish the database connection.
    *   `Calls`:
        *   `secretManager.getSecret`: Retrieves a secret string from AWS Secrets Manager.
        *   `JSON.parse`: Parses a JSON string into a JavaScript object.
*   **Example Usage**:
    ```typescript
    // This method is called internally when establishing a database connection.
    // Example:
    // PrismaConn.getInstance(); // This indirectly triggers the buildConnectionString method.
    ```