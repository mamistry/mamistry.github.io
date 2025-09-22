Here is the comprehensive documentation for the methods and functions found in the provided file:

---

### Method Name: `CMSService.constructor`

*   **Description**: Initializes the `CMSService` instance by setting up an RxJS-based HTTP client configured for the CMS API endpoint with appropriate headers.
*   **Call Stack**:
    *   **Called by**:
        *   `cmsService = new CMSService()`: Creates a new instance of the `CMSService` class.
    *   **Calls**:
        *   `rxHttpClientFactory`: A factory function that creates an instance of an RxJS-compatible HTTP client.
        *   `getCommonHeaders`: A utility function that retrieves common HTTP headers for requests.
*   **Example Usage**:
    ```typescript
    import { CMSService } from './path/to/your/file';

    const cmsServiceInstance = new CMSService();
    // The instance `cmsServiceInstance` is now ready to make CMS API calls.
    ```

---

### Method Name: `CMSService.queryData`

*   **Description**: Executes a GraphQL query against the configured CMS API endpoint, handling both standard and persisted query formats.
*   **Call Stack**:
    *   **Called by**:
        *   `CMSService.getArticleByUUID`: Calls this method to perform the actual GraphQL request for article data.
    *   **Calls**:
        *   `console.log`: Logs debugging information about the request payload.
        *   `this._httpClient.post`: Sends an HTTP POST request using the internal RxJS HTTP client.
        *   `map`: An RxJS operator that transforms the `AxiosResponse` object into its `data` property.
        *   `lastValueFrom`: An RxJS utility that converts the observable stream into a Promise, resolving with the last emitted value.
*   **Example Usage**:
    ```typescript
    import { cmsService } from './path/to/your/file';

    async function fetchCustomData() {
      const query = `query MyCustomQuery($param: String!) {
        someField(param: $param) {
          id
          name
        }
      }`;
      const variables = { param: 'exampleValue' };
      const token = 'your-bearer-token';

      try {
        const result = await cmsService.queryData<{ someField: { id: string, name: string } }>(
          query,
          variables,
          token,
          'MyCustomQuery'
        );
        console.log('Custom data fetched:', result.someField.name);
      } catch (error) {
        console.error('Failed to fetch custom data:', error);
      }
    }

    fetchCustomData();
    ```

---

### Method Name: `CMSService.getArticleByUUID`

*   **Description**: Retrieves an article from the CMS based on its unique identifier (UUID), utilizing either a full GraphQL query or a pre-defined persisted query.
*   **Call Stack**:
    *   **Called by**: (This method is designed to be called by external modules using the exported `cmsService` instance.)
    *   **Calls**:
        *   `logger.info`: Logs an informational message regarding the GraphQL query being executed.
        *   `this.queryData`: Executes the prepared GraphQL query and variables against the CMS API.
*   **Example Usage**:
    ```typescript
    import { cmsService, TGetArticleByUUIDResponse } from './path/to/your/file';

    async function retrieveArticle() {
      const articleUuid = 'a1b2c3d4-e5f6-7890-1234-567890abcdef';
      const userAuthToken = 'your-authentication-token';

      try {
        const article: TGetArticleByUUIDResponse = await cmsService.getArticleByUUID(articleUuid, userAuthToken);
        console.log('Article Title:', article.title);
        if (article.slides.length > 0) {
          console.log('First slide image URL:', article.slides[0].featuredMedia.content.image.url);
        }
      } catch (error) {
        console.error(`Error retrieving article with UUID ${articleUuid}:`, error);
      }
    }

    retrieveArticle();
    ```