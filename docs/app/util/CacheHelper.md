Here is the comprehensive documentation for the methods and functions found in the provided file.

---

### Method Name: `CacheHelper.CONTENT_MODULE_KEY_BASE`

*   **Description**: Generates the base Redis key string for a content module using its unique identifier.
*   **Call Stack**:
    *   **Called by**:
        *   `CacheHelper.FEATURE_CONFIG.QUERY_CONTENT_MODULE_BY_ID.keyBuilder`: Constructs the specific cache key for querying a content module by ID.
        *   `CacheHelper.FEATURE_CONFIG.PACKAGE_CONTENTS.keyBuilder`: Constructs the specific cache key for fetching package contents of a content module.
    *   **Calls**: None.
*   **Example Usage**:
    ```typescript
    // This is a private static utility function, typically called internally by other keyBuilders.
    const contentModuleId = "your-content-module-id";
    const redisKey = CacheHelper['CONTENT_MODULE_KEY_BASE'](contentModuleId);
    // Expected output: "sports-content-modules:content-module-id:your-content-module-id"
    ```

---

### Method Name: `CacheHelper.CHANNEL_KEY_BASE`

*   **Description**: Generates the base Redis key string for a channel using its tag UUID and semantic ID.
*   **Call Stack**:
    *   **Called by**:
        *   `CacheHelper.FEATURE_CONFIG.HOME_COMMUNITY_COLLECTIONS.keyBuilder`: Constructs the cache key for home community collections.
        *   `CacheHelper.FEATURE_CONFIG.CONTENT_MODULES_BY_SEMANTICID_AND_TAG.keyBuilder`: Constructs the cache key for content modules filtered by semantic ID and tag.
        *   `CacheHelper.FEATURE_CONFIG.GAMECAST_WHATS_BUZZING.keyBuilder`: Constructs the cache key for Gamecast "What's Buzzing" content.
        *   `CacheHelper.FEATURE_CONFIG.GAMECAST_COMMUNITY_FEED.keyBuilder`: Constructs the cache key for the Gamecast community feed.
        *   `CacheHelper.FEATURE_CONFIG.CONTENT_MODULE_SIBLINGS.keyBuilder`: Constructs the cache key for content module siblings within a channel.
        *   `CacheHelper.FEATURE_CONFIG.EXCLUDED_HOME_HEADLINES.keyBuilder`: Constructs the cache key for excluded home headlines within a channel.
    *   **Calls**: None.
*   **Example Usage**:
    ```typescript
    // This is a private static utility function, typically called internally by other keyBuilders.
    const tagUUID = "your-tag-uuid";
    const semanticID = "your-semantic-id";
    const redisKey = CacheHelper['CHANNEL_KEY_BASE'](tagUUID, semanticID);
    // Expected output: "sports-content-modules:channel:your-tag-uuid_your-semantic-id"
    ```

---

### Method Name: `CacheHelper.CONTENT_MODULE_MANIFEST`

*   **Description**: Generates the Redis manifest key string for a content module, used to track associated cache keys.
*   **Call Stack**:
    *   **Called by**:
        *   `CacheHelper.FEATURE_CONFIG.QUERY_CONTENT_MODULE_BY_ID.cacheManifest`: Constructs the manifest key for content module queries.
        *   `CacheHelper.FEATURE_CONFIG.PACKAGE_CONTENTS.cacheManifest`: Constructs the manifest key for package contents.
    *   **Calls**: None.
*   **Example Usage**:
    ```typescript
    // This is a private static utility function, typically called internally by other cacheManifest builders.
    const contentModuleId = "your-content-module-id";
    const manifestKey = CacheHelper['CONTENT_MODULE_MANIFEST'](contentModuleId);
    // Expected output: "sports-content-modules:manifest:content-module-id:your-content-module-id"
    ```

---

### Method Name: `CacheHelper.CHANNEL_MANIFEST`

*   **Description**: Generates the Redis manifest key string for a channel, used to track associated cache keys.
*   **Call Stack**:
    *   **Called by**:
        *   `CacheHelper.FEATURE_CONFIG.HOME_COMMUNITY_COLLECTIONS.cacheManifest`: Constructs the manifest key for home community collections.
        *   `CacheHelper.FEATURE_CONFIG.CONTENT_MODULES_BY_SEMANTICID_AND_TAG.cacheManifest`: Constructs the manifest key for content modules filtered by semantic ID and tag.
        *   `CacheHelper.FEATURE_CONFIG.GAMECAST_WHATS_BUZZING.cacheManifest`: Constructs the manifest key for Gamecast "What's Buzzing" content.
        *   `CacheHelper.FEATURE_CONFIG.GAMECAST_COMMUNITY_FEED.cacheManifest`: Constructs the manifest key for the Gamecast community feed.
        *   `CacheHelper.FEATURE_CONFIG.CONTENT_MODULE_SIBLINGS.cacheManifest`: Constructs the manifest key for content module siblings.
        *   `CacheHelper.FEATURE_CONFIG.EXCLUDED_HOME_HEADLINES.cacheManifest`: Constructs the manifest key for excluded home headlines.
    *   **Calls**: None.
*   **Example Usage**:
    ```typescript
    // This is a private static utility function, typically called internally by other cacheManifest builders.
    const tagUUID = "your-tag-uuid";
    const semanticID = "your-semantic-id";
    const manifestKey = CacheHelper['CHANNEL_MANIFEST'](tagUUID, semanticID);
    // Expected output: "sports-content-modules:manifest:channel:your-tag-uuid_your-semantic-id"
    ```

---

### Method Name: `CacheHelper.readCache`

*   **Description**: Retrieves cached data of a specified type for a given feature and its parameters from Redis.
*   **Call Stack**:
    *   **Called by**: (Intended for external use, not called within this file)
    *   **Calls**:
        *   `CacheHelper.fetchCacheKey`: Determines the correct Redis key for the specified feature.
        *   `redis.fetchKey<T>`: Fetches the value associated with the generated cache key from Redis.
*   **Example Usage**:
    ```typescript
    import { CacheHelper, FeatureName } from './path/to/this/file';

    async function getCachedContentModule() {
      const params = { contentModuleId: 'module-123' };
      const content = await CacheHelper.readCache<any>(FeatureName.QUERY_CONTENT_MODULE_BY_ID, params);
      if (content) {
        console.log('Retrieved cached content:', content);
      } else {
        console.log('Content not found in cache.');
      }
    }
    getCachedContentModule();
    ```

---

### Method Name: `CacheHelper.writeCache`

*   **Description**: Writes data to Redis for a specific feature, managing the cache key, its time-to-live (TTL), and updating an optional manifest.
*   **Call Stack**:
    *   **Called by**: (Intended for external use, not called within this file)
    *   **Calls**:
        *   `CacheHelper.fetchFeatureTtl`: Determines the cache TTL for the feature.
        *   `CacheHelper.fetchCacheKey`: Generates the primary Redis key for the data.
        *   `CacheHelper.fetchCacheManifest`: Generates an optional Redis manifest key for tracking related cache entries.
        *   `redis.setAdd`: Adds the generated cache key to the manifest set (if a manifest exists).
        *   `redis.setKey`: Stores the `value` in Redis under the `cacheKey` with the determined `ttl`.
*   **Example Usage**:
    ```typescript
    import { CacheHelper, FeatureName, ChannelCacheParams } from './path/to/this/file';

    async function storeGamecastBuzzing() {
      const params: ChannelCacheParams = { tagUUID: 'game-1-tag', semanticID: 'buzzing-feed' };
      const dataToCache = { articles: ['article1', 'article2'], lastUpdated: new Date() };
      await CacheHelper.writeCache(FeatureName.GAMECAST_WHATS_BUZZING, dataToCache, params);
      console.log('Gamecast buzzing data written to cache.');
    }
    storeGamecastBuzzing();
    ```

---

### Method Name: `CacheHelper.expireCache`

*   **Description**: Invalidates cache entries for a given feature and parameters, either by deleting keys from a manifest or a single cache key directly.
*   **Call Stack**:
    *   **Called by**: (Intended for external use, not called within this file)
    *   **Calls**:
        *   `CacheHelper.fetchCacheManifest`: Attempts to retrieve a manifest key for the feature.
        *   `redis.setMembers`: (If manifest exists) Fetches all cache keys stored within the manifest set.
        *   `CacheHelper.deleteCacheKey`: Deletes individual cache keys and the manifest key itself from Redis.
        *   `CacheHelper.fetchCacheKey`: (If no manifest exists) Determines the single cache key to be deleted.
*   **Example Usage**:
    ```typescript
    import { CacheHelper, FeatureName, TagsCacheParams } from './path/to/this/file';

    async function clearFollowedTagsCache() {
      const params: TagsCacheParams = { tags: 'tech,gaming' };
      await CacheHelper.expireCache(FeatureName.HOME_TAB_FOLLOWED_TAGS, params);
      console.log('Cache for followed tags (tech,gaming) has been expired.');
    }
    clearFollowedTagsCache();
    ```

---

### Method Name: `CacheHelper.deleteCacheKey`

*   **Description**: Deletes a single specified key from Redis.
*   **Call Stack**:
    *   **Called by**:
        *   `CacheHelper.expireCache`: Used to delete individual keys from a manifest or a direct cache key.
    *   **Calls**:
        *   `redis.delKey`: Executes the Redis DEL command to remove the key.
*   **Example Usage**:
    ```typescript
    // This is a private method, primarily used internally by CacheHelper.expireCache.
    async function internalDeleteExample() {
      const keyToDelete = "some:internal:cache:key";
      await CacheHelper['deleteCacheKey'](keyToDelete);
      console.log(`Key "${keyToDelete}" deleted.`);
    }
    internalDeleteExample();
    ```

---

### Method Name: `CacheHelper.fetchFeatureTtl`

*   **Description**: Retrieves the time-to-live (TTL) for a given feature, looking up an environment variable first, then falling back to a default.
*   **Call Stack**:
    *   **Called by**:
        *   `CacheHelper.writeCache`: Obtains the TTL to set when writing data to cache.
    *   **Calls**:
        *   `process.env[cfg.ttlEnvVar]`: Accesses the environment variable corresponding to the feature's TTL configuration.
        *   `parseInt`: Converts the string value from the environment variable to an integer.
        *   `RedisClient.defaultCacheTTL()`: Retrieves a default TTL value if the environment variable is not found or invalid.
*   **Example Usage**:
    ```typescript
    // This is a private method, primarily used internally by CacheHelper.writeCache.
    const ttl = CacheHelper['fetchFeatureTtl'](FeatureName.HOME_TAB_GENERIC_FEED);
    console.log(`TTL for HOME_TAB_GENERIC_FEED: ${ttl} seconds`);
    ```

---

### Method Name: `CacheHelper.fetchCacheKey`

*   **Description**: Determines and returns the appropriate Redis cache key string based on the provided feature name and parameters.
*   **Call Stack**:
    *   **Called by**:
        *   `CacheHelper.readCache`: Gets the key to retrieve data from Redis.
        *   `CacheHelper.writeCache`: Gets the key to store data in Redis.
        *   `CacheHelper.expireCache`: Gets the key to expire a single, non-manifest-managed cache entry.
    *   **Calls**:
        *   `CacheHelper.FEATURE_CONFIG[feature].keyBuilder`: Invokes the specific key-building function configured for the given `feature`. These internal `keyBuilder` functions may call `CacheHelper.CONTENT_MODULE_KEY_BASE` or `CacheHelper.CHANNEL_KEY_BASE`.
*   **Example Usage**:
    ```typescript
    // This is a private method, primarily used internally by other CacheHelper methods.
    const params = { tagUUID: 'abc', semanticID: 'xyz' };
    const cacheKey = CacheHelper['fetchCacheKey'](FeatureName.CONTENT_MODULES_BY_SEMANTICID_AND_TAG, params);
    console.log(`Generated cache key: ${cacheKey}`);
    ```

---

### Method Name: `CacheHelper.fetchCacheManifest`

*   **Description**: Determines and returns the Redis cache manifest key string for a given feature and parameters, if a manifest configuration exists for that feature.
*   **Call Stack**:
    *   **Called by**:
        *   `CacheHelper.writeCache`: Retrieves the manifest key to add the cache key to it.
        *   `CacheHelper.expireCache`: Retrieves the manifest key to get all members for deletion.
    *   **Calls**:
        *   `CacheHelper.FEATURE_CONFIG[feature].cacheManifest`: Invokes the specific manifest key-building function configured for the given `feature`, if one exists. These internal `cacheManifest` functions may call `CacheHelper.CONTENT_MODULE_MANIFEST` or `CacheHelper.CHANNEL_MANIFEST`.
*   **Example Usage**:
    ```typescript
    // This is a private method, primarily used internally by other CacheHelper methods.
    const params = { contentModuleId: 'wrapper-456' };
    const manifestKey = CacheHelper['fetchCacheManifest'](FeatureName.QUERY_CONTENT_MODULE_BY_ID, params);
    if (manifestKey) {
      console.log(`Generated cache manifest key: ${manifestKey}`);
    } else {
      console.log('No manifest configured for this feature.');
    }
    ```