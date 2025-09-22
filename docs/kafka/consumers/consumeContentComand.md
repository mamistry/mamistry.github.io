Here is the comprehensive documentation for the methods found in the provided file:

---

### Method Name: `validateContentMessage`

*   **Description**: Validates an incoming Kafka message, ensuring it has a value, can be parsed into a `ContentCommandMessage`, and contains a valid namespace and commanded entity class.
*   **Call Stack**:
    *   **Called by**:
        *   `consumeContentCommand`: Validates the Kafka message payload before further processing.
    *   **Calls**:
        *   `console.error`: Logs error messages if validation fails.
        *   `ContentCommandMessage.fromBinary`: Parses the binary message value into a `ContentCommandMessage` object.
        *   `validateContentCommand`: (External function) Performs business logic validation on the `ContentCommandMessage`.
        *   `JSON.stringify`: Converts an object to a JSON string, typically for error logging.
*   **Example Usage**:

    ```typescript
    import { KafkaMessage } from 'kafkajs';
    // Assume 'mockKafkaMessage' is a KafkaMessage object
    const mockKafkaMessage: KafkaMessage = {
        topic: 'test-topic',
        partition: 0,
        offset: '0',
        timestamp: '1678886400000',
        // Example value for a valid ContentCommandMessage
        value: Buffer.from(
            ContentCommandMessage.toBinary({
                contentCommand: {
                    contentId: { id: '123', namespace: 'urn:wbd:identifier:sp-cms:article-id' },
                    commandedEntityClass: 1, // Example for ARTICLE
                    createdDateTime: { seconds: Date.now() / 1000, nanos: 0 }
                }
            })
        ),
        key: null,
        headers: {},
        size: 0,
        attributes: 0
    };

    const validationResult = validateContentMessage(mockKafkaMessage);
    if (validationResult) {
      console.log(`Message is valid for namespace: ${validationResult.namespace}`);
    } else {
      console.error('Message validation failed.');
    }
    ```

---

### Method Name: `consumeContentCommand`

*   **Description**: Processes an incoming Kafka `ContentCommandMessage`, validates it, records metrics, and dispatches it to the appropriate content processor based on its entity class or namespace.
*   **Call Stack**:
    *   **Called by**: (Not identifiable from the provided code, likely an external Kafka consumer orchestrator or entry point).
    *   **Calls**:
        *   `validateContentMessage`: Validates the incoming Kafka message and extracts key data.
        *   `Date.now`: Retrieves the current timestamp to calculate message delay and processing duration.
        *   `metrics.timing`: (External function) Records the time taken for message delay and total processing.
        *   `metrics.increment`: (External function) Increments a counter for total processed messages.
        *   `tweetProcessor.processMessage`: (External method) Processes commands specifically for social media tweets.
        *   `externalArticleProcessor.processMessage`: (External method) Processes commands specifically for external articles.
        *   `ugcProcessor.processMessage`: (External method) Processes commands specifically for user-generated content (UGC).
        *   `articleProcessor.processMessage`: (External method) Processes commands specifically for articles.
        *   `inclusiveTaxonomyProcessor.processMessage`: (External method) Processes commands specifically for inclusive taxonomy updates.
        *   `logger.info`: (External function) Logs informational messages about the processing outcome.
*   **Example Usage**:

    ```typescript
    import { KafkaMessage } from 'kafkajs';
    // Assume 'rawKafkaMessage' is a KafkaMessage object received from a Kafka consumer
    // This example simulates a message for a social media tweet.
    import { ContentCommandMessage } from '../../wbd/protobuf/messagebus/contentcommandevent/v4/contentcommand_pb';
    import { EntityClass } from '../../wbd/protobuf/metadata/common/v4/model_pb';

    const sampleContentCommandMessage = ContentCommandMessage.create({
        contentCommand: {
            contentId: {
                id: 'tweet-12345',
                namespace: 'urn:wbd:identifier:hydration-station:socialmedia-id',
            },
            commandedEntityClass: EntityClass.SOCIAL_MEDIA_TWEET,
            createdDateTime: {
                seconds: Math.floor(Date.now() / 1000) - 10, // 10 seconds ago
                nanos: 0
            }
        }
    });

    const mockKafkaMessage: KafkaMessage = {
        topic: 'entity.contentCommand.v4',
        partition: 0,
        offset: '1',
        timestamp: String(Date.now()),
        value: Buffer.from(ContentCommandMessage.toBinary(sampleContentCommandMessage)),
        key: null,
        headers: {},
        size: 0,
        attributes: 0
    };

    // This function is typically called within a Kafka consumer loop.
    async function main() {
        console.log('Attempting to consume content command...');
        const contentModules = await consumeContentCommand(mockKafkaMessage);

        if (contentModules) {
            console.log(`Successfully processed command. Generated ${contentModules.length} content modules.`);
            contentModules.forEach((module, index) => {
                console.log(`  Module ${index + 1}: ${JSON.stringify(module, null, 2)}`);
            });
        } else {
            console.log('Failed to process content command or no modules were generated.');
        }
    }

    // Call the main function to demonstrate usage
    main();
    ```