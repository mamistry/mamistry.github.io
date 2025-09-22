Here is the documentation for the `getEnumByValue` function:

---

### `getEnumByValue`

*   **Description**: Retrieves the numeric or string value associated with a given enum's string name, performing a case-insensitive search.

*   **Call Stack**:
    *   **Called By**: (Not identifiable from the provided code)
    *   **Calls**:
        *   `Object.keys()`: Returns an array of a given object's own enumerable property names.
        *   `Array.prototype.find()`: Returns the value of the first element in the array that satisfies the provided testing function.
        *   `String.prototype.toLowerCase()`: Converts a string to lowercase.
        *   `logger.warn()`: (External function from `../../observability/logging`) Logs a warning message.

*   **Example Usage**:

    ```typescript
    enum Colors {
      RED = 0,
      GREEN = 1,
      BLUE = 2,
    }

    const redValue = getEnumByValue(Colors, 'red');
    console.log(redValue); // Output: 0

    const blueName = getEnumByValue(Colors, 'BLUE');
    console.log(blueName); // Output: 2

    const nonExistent = getEnumByValue(Colors, 'YELLOW');
    console.log(nonExistent); // Output: "YELLOW" (and a warning log will be generated)
    ```