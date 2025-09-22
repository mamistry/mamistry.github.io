# Kafka Documentation

Welcome to the Kafka module documentation. This module handles all Apache Kafka integration, including message consumption, processing, and event-driven architecture components.

---

## Consumers

Kafka consumers handle the ingestion of messages from various Kafka topics, providing the entry point for event-driven processing.

### [Client](consumers/client.md)
Core Kafka client configuration and connection management for establishing reliable connections to Kafka brokers.

### [Consume Content Command](consumers/consumeContentComand.md)
Consumer implementation for handling content command messages and content-related operations.

### [Consume Push Notification](consumers/consumePushNotification.md)
Consumer for processing push notification events and managing notification delivery workflows.

### [Consume Taxonomy](consumers/consumeTaxonomy.md)
Consumer handling taxonomy-related messages for content categorization and metadata management.

---

## Processors

Message processors contain the business logic for handling different types of Kafka messages, transforming and routing them appropriately.

### Content Processors
- **[Article Processor](processors/ArticleProcessor.md)** - Processing logic for article content messages
- **[External Article Processor](processors/ExternalArticleProcessor.md)** - Handling external article content from third-party sources
- **[Tweet Processor](processors/TweetProcessor.md)** - Processing Twitter/social media content messages
- **[UGC Processor](processors/UGCProcessor.md)** - User-generated content processing and validation

### Media Processors
- **[Gamecast Processor](processors/GamecastProcessor.md)** - Live sports gamecast event processing

### Taxonomy Processors
- **[General Taxonomy Processor](processors/GeneralTaxonomyProcessor.md)** - General content categorization and tagging
- **[Inclusive Taxonomy Processor](processors/InclusiveTaxonomyProcessor.md)** - Specialized taxonomy processing for inclusive content classification

### Notification Processors
- **[Push Notification Processor](processors/PushNotificationProcessor.md)** - Processing and formatting push notification payloads

---

## Utilities

### [BMM Utils](utils/BmmUtils.md)
Utility functions and helpers for Kafka message processing, including serialization, validation, and common operations.

---

## Overview

The Kafka module provides:

- **Event-Driven Architecture**: Asynchronous message processing for scalable content operations
- **Message Consumption**: Reliable consumption of messages from multiple Kafka topics
- **Content Processing**: Specialized processors for different content types and formats
- **Taxonomy Management**: Automated content categorization and metadata processing
- **Notification Handling**: Real-time processing of notification events
- **Error Handling**: Robust error handling and retry mechanisms for failed message processing
- **Monitoring & Logging**: Comprehensive logging and monitoring of Kafka operations

This module serves as the backbone for asynchronous processing, enabling the system to handle high-volume content ingestion and processing while maintaining performance and reliability.
