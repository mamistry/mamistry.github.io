Here is the comprehensive documentation for the methods and functions found in the provided file:

---

### Method Name: `constructor` (of `URLWrapper` class)

*   **Description**: Initializes a new `URLWrapper` instance by parsing a raw URL string and its search parameters.
*   **Call Stack**:
    *   **Calls**:
        *   `URL()`: A built-in JavaScript constructor that parses a URL string into a `URL` object.
        *   `URLSearchParams()`: A built-in JavaScript constructor that parses the query string of a URL into a `URLSearchParams` object.
    *   **Called by**: External code when creating a new `URLWrapper` object.
*   **Example Usage**:
    ```typescript
    const wrapper = new URLWrapper('https://www.example.com/path?foo=bar&utm_campaign=test');
    ```

### Method Name: `getParam`

*   **Description**: Retrieves the value of a specified URL search parameter, treating 'undefined' or 'null' string values as actual `undefined`.
*   **Call Stack**:
    *   **Calls**:
        *   `this._params.get(name)`: Retrieves the first value associated with the given search parameter name from the `URLSearchParams` object.
    *   **Called by**: Other methods within `URLWrapper` (not present in this file) or external code needing to read URL parameters.
*   **Example Usage**:
    ```typescript
    const wrapper = new URLWrapper('https://www.example.com?id=123&name=undefined');
    const id = wrapper.getParam('id'); // '123'
    const name = wrapper.getParam('name'); // undefined
    const category = wrapper.getParam('category'); // undefined
    ```

### Method Name: `updateUTMSearchParams`

*   **Description**: Updates the URL's search parameters with predefined UTM tracking query parameters and returns the modified URL string.
*   **Call Stack**:
    *   **Calls**:
        *   `Object.entries(utmQueryParams)`: Returns an array of a given object's own enumerable string-keyed property `[key, value]` pairs.
        *   `this._url.searchParams.set(key, value)`: Sets a new value for a specified search parameter in the URL's query string, or updates an existing one.
        *   `this._url.toString()`: Returns the full URL as a string.
    *   **Called by**: External code requiring the URL to have standard UTM parameters applied.
*   **Example Usage**:
    ```typescript
    const wrapper = new URLWrapper('https://www.example.com/page');
    const updatedURL = wrapper.updateUTMSearchParams();
    // updatedURL might be 'https://www.example.com/page?utm_source=bleacherreport&utm_medium=referral'
    ```