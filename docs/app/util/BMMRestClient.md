Here is the comprehensive documentation for the methods and functions found in the provided file:

---

### Method Name: `BMMRestClient.request`

*   **Description**: Initiates an HTTP request to BMM (Business Management Module) using an appropriate underlying HTTP client determined by the environment.
*   **Call Stack**:
    *   **Calls**:
        *   `logger.info`: Logs informational messages (External).
        *   `RequestClientFactory.getRequestInstance`: Retrieves a singleton instance of either `PrivateLinkHttpClient` or `PublicLinkHttpClient` based on the environment.
        *   `httpClient.request`: Executes the actual HTTP request using the obtained client (can be `PrivateLinkHttpClient.request` or `PublicLinkHttpClient.request`).
        *   `logger.error`: Logs error messages if the request fails (External).
*   **Example Usage**:
    ```typescript
    const bmmClient = new BMMRestClient();
    const requestConfig = {
      method: 'POST',
      endPoint: '/api/v1/resource',
      headers: { 'Content-Type': 'application/json' },
      data: { name: 'Test Item' },
    };
    try {
      const response = await bmmClient.request<typeof requestConfig.data, { id: string }>(requestConfig);
      console.log('BMM Response:', response.data);
    } catch (error) {
      console.error('Failed to make BMM request:', error);
    }
    ```

---

### Method Name: `BaseHttpClient.axiosRequest`

*   **Description**: Executes an HTTP request using the `axios` library and returns a standardized response object.
*   **Call Stack**:
    *   **Called by**:
        *   `PrivateLinkHttpClient.request`: Handles requests for private VPC endpoints.
        *   `PublicLinkHttpClient.request`: Handles requests for public/secured endpoints, including retries and token management.
    *   **Calls**:
        *   `axios.request`: Performs the actual HTTP request using `axios` (External).
        *   `logger.error`: Logs error messages if the `axios` request encounters an error (External).
*   **Example Usage**:
    ```typescript
    const baseClient = new BaseHttpClient();
    const config = {
      method: 'GET',
      url: 'https://jsonplaceholder.typicode.com/todos/1',
      headers: { 'Accept': 'application/json' },
    };
    try {
      const response = await baseClient.axiosRequest<null, any>(config);
      console.log(`Status: ${response.status}, Data:`, response.data);
    } catch (error) {
      console.error('Axios request failed:', error);
    }
    ```

---

### Method Name: `PrivateLinkHttpClient.constructor`

*   **Description**: Initializes a new `PrivateLinkHttpClient` instance, setting its base URL from configurations for private VPC connections.
*   **Call Stack**:
    *   **Called by**:
        *   `RequestClientFactory.getRequestInstance`: Creates an instance of `PrivateLinkHttpClient` when not running in a local environment.
    *   **Calls**:
        *   `super()`: Calls the constructor of the `BaseHttpClient` class.
*   **Example Usage**:
    ```typescript
    // Typically instantiated internally by RequestClientFactory
    const privateClient = new PrivateLinkHttpClient();
    // privateClient is now ready to make requests to the private BMM VPC.
    ```

---

### Method Name: `PrivateLinkHttpClient.request`

*   **Description**: Handles HTTP requests for private VPC endpoints, constructing the full URL and performing the request using the base `axiosRequest` method.
*   **Call Stack**:
    *   **Called by**:
        *   `BMMRestClient.request`: The high-level BMM client delegates to this method if a private client is selected by the factory.
    *   **Calls**:
        *   `logger.info`: Logs informational messages about the request being made (External).
        *   `this.axiosRequest`: Executes the actual HTTP request using `axios`.
        *   `logger.error`: Logs error messages if the request fails (External).
*   **Example Usage**:
    ```typescript
    // This method is typically called indirectly via BMMRestClient
    // const bmmClient = new BMMRestClient();
    // const response = await bmmClient.request({
    //   method: 'GET',
    //   endPoint: '/private/api/status',
    //   callIndex: 1 // Internal requirement for IHttpRequestConfig
    // });

    // Direct usage (for testing or specific scenarios):
    const privateClient = new PrivateLinkHttpClient();
    const requestConfig = {
      method: 'GET',
      endPoint: '/some/data',
      callIndex: 1, // required by IHttpRequestConfig
    };
    try {
      const response = await privateClient.request<null, any>(requestConfig);
      console.log('Private Link Response:', response.data);
    } catch (error) {
      console.error('Private Link request failed:', error);
    }
    ```

---

### Method Name: `PublicLinkHttpClient.constructor`

*   **Description**: Initializes a new `PublicLinkHttpClient` instance, configuring a Redis cache wrapper, retry parameters, Okta outage keys, and secure/fallback BMM URLs.
*   **Call Stack**:
    *   **Called by**:
        *   `RequestClientFactory.getRequestInstance`: Creates an instance of `PublicLinkHttpClient` when running in a local environment.
    *   **Calls**:
        *   `super()`: Calls the constructor of the `BaseHttpClient` class.
*   **Example Usage**:
    ```typescript
    // Typically instantiated internally by RequestClientFactory
    const publicClient = new PublicLinkHttpClient();
    // publicClient is now ready with Okta token management and retry logic.
    ```

---

### Method Name: `PublicLinkHttpClient.isOktaOutage`

*   **Description**: Asynchronously checks the Redis cache to determine if an Okta outage status is currently active.
*   **Call Stack**:
    *   **Called by**:
        *   `PublicLinkHttpClient.getTargetURLAndHeaders`: Uses the outage status to decide between secured and fallback BMM URLs.
    *   **Calls**:
        *   `this.redisInstance.get`: Retrieves the Okta outage status from the Redis cache instance.
        *   `redis.fetchKey`: Internally called by `this.redisInstance.get` to fetch a key from Redis (External).
        *   `logger.error`: Logs an error if fetching from Redis fails (External).
*   **Example Usage**:
    ```typescript
    const publicClient = new PublicLinkHttpClient();
    const outageStatus = await publicClient['isOktaOutage'](); // Accessing private method for example
    console.log(`Current Okta outage status: ${outageStatus}`);
    ```

---

### Method Name: `PublicLinkHttpClient.updateOktaOutageStatus`

*   **Description**: Asynchronously sets the Okta outage status to `true` in the Redis cache with a predefined Time To Live (TTL).
*   **Call Stack**:
    *   **Called by**:
        *   `PublicLinkHttpClient.getRetryableStatus`: Updates outage status if a retryable error occurs, indicating a potential token issue.
        *   `PublicLinkHttpClient.getTargetURLAndHeaders`: Updates outage status if `restoreOktaToken` fails.
    *   **Calls**:
        *   `this.redisInstance.set`: Stores the Okta outage status in the Redis cache instance.
        *   `redis.setKey`: Internally called by `this.redisInstance.set` to set a key in Redis (External).
        *   `logger.error`: Logs an error if setting the key in Redis fails (External).
*   **Example Usage**:
    ```typescript
    const publicClient = new PublicLinkHttpClient();
    await publicClient['updateOktaOutageStatus'](); // Accessing private method for example
    console.log('Okta outage status has been updated in Redis.');
    ```

---

### Method Name: `PublicLinkHttpClient.restoreOktaToken`

*   **Description**: Retrieves an Okta access token from the cache; if not found or invalid, it creates a new token and writes it to the cache.
*   **Call Stack**:
    *   **Called by**:
        *   `PublicLinkHttpClient.getTargetURLAndHeaders`: Fetches the token required for authorization headers.
    *   **Calls**:
        *   `fetchAccessTokenFromCache`: Attempts to retrieve the access token from the cache (External).
        *   `logger.info`: Logs informational messages, e.g., when a new token is being created (External).
        *   `createNewOktaAccessToken`: Generates a new Okta access token (External).
        *   `writeAccessTokenToCache`: Writes the newly obtained token to the cache (External).
*   **Example Usage**:
    ```typescript
    const publicClient = new PublicLinkHttpClient();
    try {
      const token = await publicClient['restoreOktaToken'](); // Accessing private method for example
      console.log('Okta Access Token:', token.substring(0, 10) + '...');
    } catch (error) {
      console.error('Failed to restore Okta token:', error);
    }
    ```

---

### Method Name: `PublicLinkHttpClient.getRetryableStatus`

*   **Description**: Determines if an HTTP request should be retried based on the current `callIndex` and if a fallback (and Okta outage status update) is necessary due to specific HTTP error codes.
*   **Call Stack**:
    *   **Called by**:
        *   `PublicLinkHttpClient.request`: Decides whether to re-attempt a failed request.
    *   **Calls**:
        *   `this.updateOktaOutageStatus`: Updates the Okta outage status if the error code suggests a token-related issue requiring fallback.
*   **Example Usage**:
    ```typescript
    const publicClient = new PublicLinkHttpClient();
    const errorWithAuthIssue = { status: 401 };
    const shouldRetry = await publicClient['getRetryableStatus'](1, errorWithAuthIssue); // Accessing private method for example
    console.log(`Should retry for 401 on first attempt: ${shouldRetry}`); // Likely true

    const errorWithOtherIssue = { status: 500 };
    const shouldRetryOnLastAttempt = await publicClient['getRetryableStatus'](3, errorWithOtherIssue); // Assuming retryCount is 2
    console.log(`Should retry for 500 on last attempt: ${shouldRetryOnLastAttempt}`); // Likely false
    ```

---

### Method Name: `PublicLinkHttpClient.getTargetURLAndHeaders`

*   **Description**: Determines the appropriate target URL (secured BMM or fallback BMM) and configures authorization headers based on the current Okta outage status and token availability.
*   **Call Stack**:
    *   **Called by**:
        *   `PublicLinkHttpClient.request`: Prepares the URL and headers before initiating the HTTP request.
    *   **Calls**:
        *   `this.isOktaOutage`: Checks if an Okta outage is currently active.
        *   `this.restoreOktaToken`: Fetches or generates an Okta access token to be included in headers.
        *   `this.updateOktaOutageStatus`: Updates the Okta outage status if token restoration fails.
*   **Example Usage**:
    ```typescript
    const publicClient = new PublicLinkHttpClient();
    const endpoint = '/public/data';
    const initialHeaders = { 'X-Correlation-Id': 'abc-123' };
    const config = await publicClient['getTargetURLAndHeaders'](endpoint, initialHeaders); // Accessing private method for example
    console.log('Resolved URL:', config.url);
    console.log('Resolved Headers:', config.headers);
    ```

---

### Method Name: `PublicLinkHttpClient.request`

*   **Description**: Executes an HTTP request for public or secured BMM endpoints, incorporating complex logic for Okta token management, automatic retries, and fallback URL handling.
*   **Call Stack**:
    *   **Called by**:
        *   `BMMRestClient.request`: The high-level BMM client delegates to this method if a public client is selected by the factory.
        *   `PublicLinkHttpClient.request`: Recursively calls itself when a request needs to be retried.
    *   **Calls**:
        *   `this.getTargetURLAndHeaders`: Determines the final URL and headers for the request.
        *   `this.axiosRequest`: Executes the actual HTTP request using `axios`.
        *   `logger.error`: Logs error messages if the request fails (External).
        *   `this.getRetryableStatus`: Checks if the failed request should be retried.
        *   `logger.info`: Logs informational messages during a retry attempt (External).
*   **Example Usage**:
    ```typescript
    // This method is typically called indirectly via BMMRestClient
    // const bmmClient = new BMMRestClient();
    // const response = await bmmClient.request({
    //   method: 'GET',
    //   endPoint: '/public/api/items',
    //   callIndex: 1 // Internal requirement for IHttpRequestConfig
    // });

    // Direct usage (for testing or specific scenarios):
    const publicClient = new PublicLinkHttpClient();
    const requestConfig = {
      method: 'GET',
      endPoint: '/inventory',
      callIndex: 1, // required by IHttpRequestConfig
      headers: { 'X-Custom-Trace': 'trace-123' }
    };
    try {
      const response = await publicClient.request<null, any>(requestConfig);
      console.log('Public Link Response:', response.data);
    } catch (error) {
      console.error('Public Link request failed:', error);
    }
    ```

---

### Method Name: `RequestClientFactory.getRequestInstance`

*   **Description**: A static factory method that provides a singleton instance of either `PrivateLinkHttpClient` or `PublicLinkHttpClient` based on the `ENV` environment variable.
*   **Call Stack**:
    *   **Called by**:
        *   `BMMRestClient.request`: Obtains the appropriate HTTP client to fulfill a BMM request.
    *   **Calls**:
        *   `PrivateLinkHttpClient` constructor: Creates a new instance of `PrivateLinkHttpClient` if the environment is not 'local'.
        *   `PublicLinkHttpClient` constructor: Creates a new instance of `PublicLinkHttpClient` if the environment is 'local'.
*   **Example Usage**:
    ```typescript
    const httpClient = RequestClientFactory.getRequestInstance();
    // httpClient will be an instance of either PrivateLinkHttpClient or PublicLinkHttpClient
    // based on process.env.ENV.
    // Example call:
    // const requestConfig = { method: 'GET', endPoint: '/status', callIndex: 1 };
    // const response = await httpClient.request<null, any>(requestConfig);
    ```