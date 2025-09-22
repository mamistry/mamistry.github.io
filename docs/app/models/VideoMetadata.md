This document provides comprehensive documentation for the methods defined in the provided TypeScript file.

---

### Method Name: `upsertVideoMetadata`

*   **Description**: Creates a new video metadata record or updates an existing one based on the provided video ID and new state.
*   **Call Stack**:
    *   **Called By**: (Cannot be determined from the provided file.)
    *   **Calls**:
        *   `writterConn.getConn()`: Retrieves a Prisma client instance configured for write operations.
        *   `prismaClient.videoMetadata.upsert()`: Executes an atomic database operation to either create a new `videoMetadata` record or update an existing one.
        *   `getEnumByValue(VideoState, dbResult.videoState)`: Converts a string value from the database into its corresponding `VideoState` enum member.
*   **Example Usage**:
    ```typescript
    import { VideoMetadataInput, VideoState } from '../../graphql/generated/graphql';
    import { VideoMetadata, TVideoMetadata } from './this-file'; // Assuming this is the path to the documented file

    async function exampleUpsert() {
      const input: VideoMetadataInput = {
        videoId: "some-unique-video-id-123",
        newState: VideoState.Published,
      };

      try {
        const result: TVideoMetadata = await VideoMetadata.upsertVideoMetadata(input);
        console.log("Upserted video metadata:", result);
      } catch (error) {
        console.error("Error upserting video metadata:", error);
      }
    }

    exampleUpsert();
    ```

---

### Method Name: `fetchVideoMetadata`

*   **Description**: Retrieves a single video metadata record by its video ID, returning `null` if not found or if the video ID is invalid.
*   **Call Stack**:
    *   **Called By**: (Cannot be determined from the provided file.)
    *   **Calls**:
        *   `readerConn.getConn()`: Retrieves a Prisma client instance configured for read operations.
        *   `prismaClient.videoMetadata.findFirst()`: Queries the database to find the first `videoMetadata` record that matches the given criteria.
        *   `getEnumByValue(VideoState, dbResult.videoState)`: Converts a string value from the database into its corresponding `VideoState` enum member.
*   **Example Usage**:
    ```typescript
    import { TVideoMetadata, VideoMetadata } from './this-file'; // Assuming this is the path to the documented file

    async function exampleFetch() {
      const existingVideoId = "some-unique-video-id-123";
      const nonExistentVideoId = "non-existent-video-id";

      try {
        const result: TVideoMetadata | null = await VideoMetadata.fetchVideoMetadata(existingVideoId);
        if (result) {
          console.log("Fetched video metadata:", result);
        } else {
          console.log(`No metadata found for video ID: ${existingVideoId}`);
        }

        const nullResult: TVideoMetadata | null = await VideoMetadata.fetchVideoMetadata(nonExistentVideoId);
        if (nullResult) {
          console.log("Fetched video metadata (unexpected):", nullResult);
        } else {
          console.log(`No metadata found for video ID: ${nonExistentVideoId}`);
        }

        const invalidResult: TVideoMetadata | null = await VideoMetadata.fetchVideoMetadata("");
        console.log(`Result for empty video ID:`, invalidResult); // Expected: null

      } catch (error) {
        console.error("Error fetching video metadata:", error);
      }
    }

    exampleFetch();
    ```