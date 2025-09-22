```markdown
# Documentation for `EmbedderService`

## Method: `getEmbedderMetadata`

*   **Description**: Fetches oEmbed metadata for a given URL using an external embedder service and transforms the response into a standardized format.

*   **Call Stack**:
    *   **Called By**: None identifiable within this file.
    *   **Calls**:
        *   `_httpClient.get()`: (Method of `RxjsAxios`) Initiates an HTTP GET request to the specified URL.
        *   `configs.EMBEDDER_URL`: (Configuration variable) Accesses the base URL for the embedder service from the application configuration.
        *   `pipe()`: (RxJS operator) Chains RxJS operators to transform the stream of data received from the HTTP request.
        *   `map()`: (RxJS operator) Transforms the `TEmbedderMetadataResponse` received from the API into the `IEmbedderResponse` format.
        *   `lastValueFrom()`: (RxJS utility function) Converts the final observable stream into a Promise, which resolves with the last value emitted by the observable.
    *   **Decorators**:
        *   `@asyncLogger`: (Decorator) Wraps this asynchronous method, presumably for logging its execution.

*   **Example Usage**:

    ```typescript
    import { embedderService } from './path/to/this/file'; // Adjust path as needed

    async function fetchMetadata() {
      const url = 'https://www.youtube.com/watch?v=dQw4w9WgXcQ';
      try {
        const metadata = await embedderService.getEmbedderMetadata(url);
        console.log('Embedder Metadata:', metadata);
        /*
        Example output for YouTube:
        {
          providerName: 'YouTube',
          title: 'Rick Astley - Never Gonna Give You Up (Official Music Video)',
          description: 'The official video for “Never Gonna Give You Up” by Rick Astley',
          thumbnailUrl: 'https://i.ytimg.com/vi/dQw4w9WgXcQ/hqdefault.jpg'
        }
        */
      } catch (error) {
        console.error('Failed to get embedder metadata:', error);
      }
    }

    fetchMetadata();
    ```