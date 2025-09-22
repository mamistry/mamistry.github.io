This document provides comprehensive technical documentation for the methods and functions found within the provided file, following the specified structure and constraints.

---

### `fetchHomePage`

1.  **Method Name**: `fetchHomePage`
2.  **Description**: Main entry point for fetching home page content based on a component's semantic ID and user preferences.
3.  **Call Stack**:
    *   **Called by**: (Not identifiable from the provided code)
    *   **Calls**:
        *   `resolveHomeCommunityCollection`: Resolves content for home community collections based on user tags.
        *   `resolveContentPremiumVideo`: Resolves premium video content, potentially personalized.
        *   `resolveHomeFeed`: Resolves content for the general home community feed, personalized or non-personalized.
4.  **Example Usage**:

    ```typescript
    import { SemanticID } from '@warnermediacode/graphql-base-enums';
    import { TagV2 } from '../../../graphql/generated/graphql';
    import { ChannelContentsProps } from '../types';

    const mockComponent: ChannelContentsProps = {
      semanticID: SemanticID.CONTENT_COMMUNITY_FEED,
      id: 123,
      insertedAt: new Date(),
      updatedAt: new Date(),
      tagUUID: 'some-tag-uuid',
      userTags: [],
    };
    const mockUserTags: TagV2[] = [
      { uuid: 'tag-1', slug: 'team-a', type: TagTypeV2.Team, followersCount: 100 },
    ];
    const mockStatesFilter: string[] = ['PUBLISHED'];

    // Example for CONTENT_COMMUNITY_FEED
    const homeFeedContent = await fetchHomePage(mockComponent, mockUserTags, mockStatesFilter, 'home-tab', 3, false);
    console.log(homeFeedContent);

    // Example for CONTENT_HOME_COMMUNITY_COLLECTIONS
    const communityCollections = await fetchHomePage(
      { ...mockComponent, semanticID: SemanticID.CONTENT_HOME_COMMUNITY_COLLECTIONS },
      mockUserTags,
      mockStatesFilter
    );
    console.log(communityCollections);
    ```

---

### `resolveHomeCommunityCollection`

1.  **Method Name**: `resolveHomeCommunityCollection`
2.  **Description**: Fetches and structures content for the home community collections, prioritizing recent tweets, videos, and articles for a given tag.
3.  **Call Stack**:
    *   **Called by**: `fetchHomePage`
    *   **Calls**:
        *   `CacheHelper.readCache`: Reads data from the application cache.
        *   `statesFilterFromString`: Generates a Prisma-compatible states filter from an array of strings.
        *   `createTempContentModule`: Creates an empty `TPackageContentModule` structure.
        *   `ContentModuleRepository.fetchTweetsForHomeCommunityCollection`: Fetches tweet content modules related to a tag.
        *   `ContentModuleRepository.fetchVideosForHomeCommunityCollection`: Fetches video content modules related to a tag.
        *   `ContentModuleRepository.fetchArticlesForHomeCommunityCollection`: Fetches article content modules related to a tag.
        *   `createCompositeContentsFromStandaloneContentModules`: Organizes standalone content modules into a composite structure.
        *   `CacheHelper.writeCache`: Writes data to the application cache.
4.  **Example Usage**:

    ```typescript
    const result = await resolveHomeCommunityCollection(
      { tagUUID: 'a1b2c3d4-e5f6-7890-1234-567890abcdef', semanticID: SemanticID.CONTENT_HOME_COMMUNITY_COLLECTIONS },
      ['PUBLISHED']
    );
    console.log(result);
    ```

---

### `resolveContentPremiumVideo`

1.  **Method Name**: `resolveContentPremiumVideo`
2.  **Description**: Fetches premium video content, prioritizing programmer-curated packages or personalized content based on user-followed creators if no curated content exists.
3.  **Call Stack**:
    *   **Called by**: `fetchHomePage`
    *   **Calls**:
        *   `composeStatusQuery`: Composes a status query string for Prisma filtering.
        *   `fetchPackageByTypeAndTag`: Retrieves content packages by package type and tag.
        *   `getCreatorsTags`: Extracts UUIDs of creator-type tags from a list of user tags.
        *   `fetchRecentContentFromCreators`: Fetches recent content associated with a list of creator tags.
        *   `fetchContentModulesBySemanticIdNotInTagsWithTime`: Fetches content modules by semantic ID, excluding those associated with specified tags.
        *   `interleaveContent`: Blends two arrays of content by alternating elements.
        *   `resolveCreatorVideoMetadata`: Enriches standalone content modules with video-specific metadata.
        *   `sortCreatorContent`: Sorts creator-related content modules.
        *   `fetchRecentCreatorShowsContentGeneric`: Fetches generic recent creator shows content.
4.  **Example Usage**:

    ```typescript
    import { SemanticID, TagTypeV2, TagV2 } from '@warnermediacode/graphql-base-enums';
    import { ChannelContentsProps } from '../types';

    const componentProps: ChannelContentsProps = {
      semanticID: SemanticID.CONTENT_PREMIUM_VIDEO,
      id: 1,
      insertedAt: new Date(),
      updatedAt: new Date(),
      tagUUID: 'some-tag-uuid',
      userTags: [
        { uuid: 'creator-tag-1', type: TagTypeV2.Creator, followersCount: 50 },
        { uuid: 'team-tag-1', type: TagTypeV2.Team, followersCount: 150 },
      ] as TagV2[],
    };
    const userTagsUUIDs = ['creator-tag-1'];
    const statesFilter = ['PUBLISHED'];

    const premiumVideos = await resolveContentPremiumVideo(
      componentProps,
      userTagsUUIDs,
      statesFilter,
      'home-tab'
    );
    console.log(premiumVideos);
    ```

---

### `interleaveContent`

1.  **Method Name**: `interleaveContent`
2.  **Description**: Blends two arrays of content by alternating elements from each, returning a single interleaved array.
3.  **Call Stack**:
    *   **Called by**: `resolveContentPremiumVideo`
    *   **Calls**: (None identifiable from the provided code)
4.  **Example Usage**:

    ```typescript
    const arr1 = [{ id: 1 }, { id: 3 }, { id: 5 }];
    const arr2 = [{ id: 2 }, { id: 4 }, { id: 6 }];
    const result = interleaveContent(arr1, arr2);
    // result will be: [{ id: 1 }, { id: 2 }, { id: 3 }, { id: 4 }, { id: 5 }, { id: 6 }]
    console.log(result);
    ```

---

### `getCreatorsTags`

1.  **Method Name**: `getCreatorsTags`
2.  **Description**: Extracts the UUIDs of creator-type tags from a list of user tags.
3.  **Call Stack**:
    *   **Called by**: `resolveContentPremiumVideo`
    *   **Calls**: (None identifiable from the provided code)
4.  **Example Usage**:

    ```typescript
    import { TagTypeV2, TagV2 } from '../../../graphql/generated/graphql';

    const userTags: TagV2[] = [
      { uuid: 'a1', type: TagTypeV2.Creator, followersCount: 100 },
      { uuid: 'b2', type: TagTypeV2.Team, followersCount: 50 },
      { uuid: 'c3', type: TagTypeV2.Creator, followersCount: 200 },
    ];
    const creatorUuids = getCreatorsTags(userTags);
    // creatorUuids will be: ['a1', 'c3']
    console.log(creatorUuids);
    ```

---

### `generateInterlacedHomeFeedContents`

1.  **Method Name**: `generateInterlacedHomeFeedContents`
2.  **Description**: Generates a blended feed array by interlacing feed-specific content modules with manually programmed content modules at a specified interval.
3.  **Call Stack**:
    *   **Called by**: `resolveHomeFeed`, `buildPersonalizedHomeFeed`, `buildNonPersonalizedHomeFeed`
    *   **Calls**: (None identifiable from the provided code)
4.  **Example Usage**:

    ```typescript
    const feedSpecific = [{ id: 'a' }, { id: 'b' }, { id: 'c' }, { id: 'd' }, { id: 'e' }];
    const programmed = [{ id: 'P1' }, { id: 'P2' }];

    // Interlacing with interval 3 (2 feed-specific, then 1 programmed)
    const blendedFeed = generateInterlacedHomeFeedContents(feedSpecific, programmed, 3);
    // blendedFeed will be: [{ id: 'a' }, { id: 'b' }, { id: 'P1' }, { id: 'c' }, { id: 'd' }, { id: 'P2' }, { id: 'e' }]
    console.log(blendedFeed);

    // No interlacing (interval 0)
    const noInterlace = generateInterlacedHomeFeedContents(feedSpecific, programmed, 0);
    // noInterlace will be: [{ id: 'P1' }, { id: 'P2' }, { id: 'a' }, { id: 'b' }, { id: 'c' }, { id: 'd' }, { id: 'e' }]
    console.log(noInterlace);
    ```

---

### `resolveHomeFeed`

1.  **Method Name**: `resolveHomeFeed`
2.  **Description**: Resolves the content for the home community feed, providing either a personalized or non-personalized feed based on user tags and interlacing programmed content.
3.  **Call Stack**:
    *   **Called by**: `fetchHomePage`
    *   **Calls**:
        *   `getTopUserTags`: Retrieves the top user-followed tags by follower count.
        *   `excludedHomeHeadlinesStandAlones`: Fetches IDs of standalone content modules configured as home headlines for exclusion.
        *   `fetchProgrammedContents`: Fetches manually programmed content modules for the feed.
        *   `sortHomeLogo`: Sorts content modules to prioritize a specific tag UUID and adjusts metadata.
        *   `buildPersonalizedHomeFeed`: Constructs the personalized home feed.
        *   `buildNonPersonalizedHomeFeed`: Constructs the non-personalized home feed.
        *   `contentModuleDTOService.mapDBFindManyResultListWithIncludeToModel`: Maps database results to the internal content module model.
5.  **Example Usage**:

    ```typescript
    import { TagTypeV2, TagV2 } from '../../../graphql/generated/graphql';
    import { ChannelContentsProps } from '../types';

    const componentProps: ChannelContentsProps = {
      semanticID: SemanticID.CONTENT_COMMUNITY_FEED,
      id: 1,
      insertedAt: new Date(),
      updatedAt: new Date(),
      tagUUID: 'community-feed-tag-uuid',
      userTags: [],
    };
    const userTags: TagV2[] = [
      { uuid: 'user-tag-1', slug: 'national', type: TagTypeV2.League, followersCount: 200 },
      { uuid: 'user-tag-2', slug: 'team-a', type: TagTypeV2.Team, followersCount: 150 },
    ];

    // Example with personalized feed (user has followed tags)
    const personalizedFeed = await resolveHomeFeed(componentProps, userTags, 3, false);
    console.log('Personalized Feed:', personalizedFeed);

    // Example with non-personalized feed (no followed tags)
    const nonPersonalizedFeed = await resolveHomeFeed(
      { ...componentProps, userTags: [] },
      [],
      3,
      false
    );
    console.log('Non-Personalized Feed:', nonPersonalizedFeed);
    ```

---

### `excludeHookGroupsUserNotFollowing`

1.  **Method Name**: `excludeHookGroupsUserNotFollowing`
2.  **Description**: Filters out components from content modules where the associated tag UUID is not among the user's followed tags.
3.  **Call Stack**:
    *   **Called by**: `fetchAndProcessPersonalizedContent`
    *   **Calls**: (None identifiable from the provided code)
4.  **Example Usage**:

    ```typescript
    const rawContents = [
      {
        id: 'cm1',
        components: [
          { Component: { tagUUID: 'user-tag-1' } },
          { Component: { tagUUID: 'other-tag' } },
        ],
      },
    ];
    const userTagUUIDs = ['user-tag-1', 'user-tag-2'];
    excludeHookGroupsUserNotFollowing(rawContents, userTagUUIDs);
    // rawContents[0].components will now be: [{ Component: { tagUUID: 'user-tag-1' } }]
    console.log(rawContents[0].components);
    ```

---

### `filterOutAutoProgrammedTweets`

1.  **Method Name**: `filterOutAutoProgrammedTweets`
2.  **Description**: Filters out automatically programmed tweet content modules if `Tweet` is not included in the allowed content types.
3.  **Call Stack**:
    *   **Called by**: (Not identifiable from the provided code)
    *   **Calls**: (None identifiable from the provided code)
4.  **Example Usage**:

    ```typescript
    import { ContentModuleType } from '../../../graphql/generated/graphql';
    import { TContentModule } from '../../services/contentModules/ContentModuleTypes';

    const contents: TContentModule[] = [
      { id: '1', contentType: ContentModuleType.VideoV2, autoProgrammed: false } as TContentModule,
      { id: '2', contentType: ContentModuleType.Tweet, autoProgrammed: true } as TContentModule,
      { id: '3', contentType: ContentModuleType.Tweet, autoProgrammed: false } as TContentModule,
    ];

    // Filter tweets if ContentModuleType.Tweet is not in includeContentTypes
    const filtered1 = filterOutAutoProgrammedTweets([ContentModuleType.VideoV2], contents);
    // filtered1 will be: [{ id: '1', ... }]
    console.log('Filtered 1:', filtered1.map((c) => c.id));

    // Do not filter tweets if ContentModuleType.Tweet is in includeContentTypes
    const filtered2 = filterOutAutoProgrammedTweets(
      [ContentModuleType.VideoV2, ContentModuleType.Tweet],
      contents
    );
    // filtered2 will be: [{ id: '1', ... }, { id: '2', ... }, { id: '3', ... }]
    console.log('Filtered 2:', filtered2.map((c) => c.id));
    ```

---

### `fetchProgrammedContents`

1.  **Method Name**: `fetchProgrammedContents`
2.  **Description**: Fetches content modules that have been manually programmed to a specific semantic ID and tag, optionally excluding specified content IDs.
3.  **Call Stack**:
    *   **Called by**: `resolveHomeFeed`
    *   **Calls**:
        *   `findContentModulesBySemanticIdAndTag`: Finds content modules in the database based on semantic ID, tag UUID, and other filters.
4.  **Example Usage**:

    ```typescript
    import { SemanticId, ContentModuleType } from '../../../graphql/generated/graphql';

    const programmedContent = await fetchProgrammedContents(
      'some-tag-uuid',
      [ContentModuleType.VideoV2, ContentModuleType.Article],
      ['excluded-content-id-1', 'excluded-content-id-2']
    );
    console.log(programmedContent);
    ```

---

### `resolveCreatorVideoMetadata`

1.  **Method Name**: `resolveCreatorVideoMetadata`
2.  **Description**: Enriches standalone content modules with video-specific metadata, specifically the `videoState`, for videos that are 'VOD' or 'LIVE'.
3.  **Call Stack**:
    *   **Called by**: `resolveContentPremiumVideo`
    *   **Calls**:
        *   `replicaConn.getConn`: Obtains a Prisma client instance for read-only operations.
4.  **Example Usage**:

    ```typescript
    import { StandaloneContentModule } from '../types';

    const mockContentModules: StandaloneContentModule[] = [
      { contentId: 'video-1', type: 'standalone' } as StandaloneContentModule,
      { contentId: 'video-2', type: 'standalone' } as StandaloneContentModule,
      { contentId: 'article-1', type: 'standalone' } as StandaloneContentModule, // Will be ignored
    ];
    // Assuming video-1 has videoState 'VOD' and video-2 has 'LIVE' in the DB
    const enrichedModules = await resolveCreatorVideoMetadata(mockContentModules);
    console.log(enrichedModules);
    ```

---

### `fetchStandaloneFromGenericFeed`

1.  **Method Name**: `fetchStandaloneFromGenericFeed`
2.  **Description**: Fetches a generic feed of standalone content modules, applying content type and exclusion filters, and caching the result for performance.
3.  **Call Stack**:
    *   **Called by**: `buildNonPersonalizedHomeFeed`
    *   **Calls**:
        *   `CacheHelper.readCache`: Reads data from the application cache.
        *   `genericFeedAllowedTags`: Retrieves a list of allowed tag UUIDs for the generic feed.
        *   `replicaConn.getConn`: Obtains a Prisma client instance for read-only operations.
        *   `generateContentModuleIncludes`: Generates Prisma `include` options for fetching related content module data.
        *   `uniqBy`: Removes duplicate items from an array based on a key.
        *   `CacheHelper.writeCache`: Writes data to the application cache.
5.  **Example Usage**:

    ```typescript
    import { ContentModuleType } from '../../../graphql/generated/graphql';

    const genericFeedContents = await fetchStandaloneFromGenericFeed(
      [ContentModuleType.VideoV2, ContentModuleType.Article],
      ['excluded-content-id-1']
    );
    console.log(genericFeedContents);
    ```

---

### `excludedHomeHeadlinesStandAlones`

1.  **Method Name**: `excludedHomeHeadlinesStandAlones`
2.  **Description**: Retrieves a list of content module IDs that are programmed as home headlines for a given tag, used for exclusion in other feeds.
3.  **Call Stack**:
    *   **Called by**: `resolveHomeFeed`
    *   **Calls**:
        *   `CacheHelper.readCache`: Reads data from the application cache.
        *   `replicaConn.getConn`: Obtains a Prisma client instance for read-only operations.
        *   `CacheHelper.writeCache`: Writes data to the application cache.
4.  **Example Usage**:

    ```typescript
    const excludedIds = await excludedHomeHeadlinesStandAlones(
      'some-tag-uuid',
      ['user-followed-tag-1', 'user-followed-tag-2']
    );
    console.log(excludedIds);
    ```

---

### `genericFeedAllowedTags`

1.  **Method Name**: `genericFeedAllowedTags`
2.  **Description**: Fetches a list of tag UUIDs that are allowed for the generic home feed, utilizing a cache to improve performance.
3.  **Call Stack**:
    *   **Called by**: `fetchStandaloneFromGenericFeed`
    *   **Calls**:
        *   `CacheHelper.readCache`: Reads data from the application cache.
        *   `replicaConn.getConn`: Obtains a Prisma client instance for read-only operations.
        *   `CacheHelper.writeCache`: Writes data to the application cache.
4.  **Example Usage**:

    ```typescript
    const allowedTags = await genericFeedAllowedTags();
    console.log(allowedTags);
    ```

---

### `dedupeContentModules`

1.  **Method Name**: `dedupeContentModules`
2.  **Description**: Deduplicates an array of content modules based on their `contentId`, selecting the "best" representation using a configurable strategy and tag weighting.
3.  **Call Stack**:
    *   **Called by**: `resolvePersonalizedHomeHeadlines`, `buildPersonalizedHomeFeed`, `buildNonPersonalizedHomeFeed`
    *   **Calls**:
        *   `TaxonomyConfigurationService.fetchTagInformation`: Fetches detailed configuration for a given tag UUID.
        *   `isNotNull`: A type guard to filter out `null` values from an array.
        *   `selectBestContentModuleBase`: Selects the optimal content module from a set of duplicates based on defined criteria.
4.  **Example Usage**:

    ```typescript
    import { TContentModule } from '../../services/contentModules/ContentModuleTypes';

    const mockContentModules: TContentModule[] = [
      { id: 'm1', contentId: 'c1', components: [{ Component: { tagUUID: 'tag-a' } }] } as TContentModule,
      { id: 'm2', contentId: 'c1', components: [{ Component: { tagUUID: 'tag-b' } }] } as TContentModule,
      { id: 'm3', contentId: 'c2', components: [{ Component: { tagUUID: 'tag-c' } }] } as TContentModule,
    ];
    // Assuming TaxonomyConfigurationService.fetchTagInformation provides tagWeight for tag-a and tag-b
    const dedupedContents = await dedupeContentModules(
      'tag-a', // defaultTag
      ['tag-a'], // sortedUserFollowedTagUUIDs
      mockContentModules,
      false, // isInternalRequest
      false // isGeneric
    );
    console.log(dedupedContents);
    ```

---

### `selectBestContentModuleBase`

1.  **Method Name**: `selectBestContentModuleBase`
2.  **Description**: Selects the "best" content module from a list of duplicates based on tag weights derived from taxonomy data and a specified strategy (minimum or maximum weight).
3.  **Call Stack**:
    *   **Called by**: `dedupeContentModules`
    *   **Calls**:
        *   `generateMetaData`: Creates metadata for a content module based on the selected primary tag.
        *   `crypto.randomInt`: Generates a cryptographically strong random integer (external module).
4.  **Example Usage**:

    ```typescript
    import { TContentModule } from '../../services/contentModules/ContentModuleTypes';
    import { TagTypeV2 } from '../../../graphql/generated/graphql';

    const duplicates: TContentModule[] = [
      {
        id: '1',
        contentId: 'content-x',
        components: [{ Component: { tagUUID: 'tag-low-weight' } }],
      } as TContentModule,
      {
        id: '2',
        contentId: 'content-x',
        components: [{ Component: { tagUUID: 'tag-high-weight' } }],
      } as TContentModule,
    ];
    const taxonomyData = [
      {
        tagUUID: 'tag-low-weight',
        tagWeight: 10,
        tagType: TagTypeV2.Team,
        created_at: new Date(),
        updated_at: new Date(),
      },
      {
        tagUUID: 'tag-high-weight',
        tagWeight: 100,
        tagType: TagTypeV2.League,
        created_at: new Date(),
        updated_at: new Date(),
      },
    ];

    // Example with 'max' strategy
    const bestMax = selectBestContentModuleBase({
      defaultTagUUID: 'tag-low-weight',
      duplicates,
      taxonomyData,
      strategy: 'max',
    });
    console.log('Best (max strategy):', bestMax.id); // Should be '2'

    // Example with 'min' strategy
    const bestMin = selectBestContentModuleBase({
      defaultTagUUID: 'tag-high-weight',
      duplicates,
      taxonomyData,
      strategy: 'min',
    });
    console.log('Best (min strategy):', bestMin.id); // Should be '1'
    ```

---

### `getTopUserTags`

1.  **Method Name**: `getTopUserTags`
2.  **Description**: Retrieves a specified number of top user-followed tag UUIDs, sorted by followers count in descending order.
3.  **Call Stack**:
    *   **Called by**: `resolveHomeFeed`, `resolvePersonalizedHomeHeadlines`, `fetchAndProcessPersonalizedContent` (indirectly, as the result is passed as `tagUUIDs`)
    *   **Calls**: (None identifiable from the provided code)
4.  **Example Usage**:

    ```typescript
    import { TagV2, TagTypeV2 } from '../../../graphql/generated/graphql';

    const userTags: TagV2[] = [
      { uuid: 'tag-a', followersCount: 150, type: TagTypeV2.Team } as TagV2,
      { uuid: 'tag-b', followersCount: 300, type: TagTypeV2.League } as TagV2,
      { uuid: 'tag-c', followersCount: 50, type: TagTypeV2.Interest } as TagV2,
      { uuid: 'tag-d', followersCount: 200, type: TagTypeV2.Team } as TagV2,
    ];
    // Assuming HOME_FEED_TAGS_AMOUNT is not set, defaults to 10
    const topTags = getTopUserTags(userTags);
    // topTags will be: ['tag-b', 'tag-d', 'tag-a', 'tag-c']
    console.log(topTags);
    ```

---

### `generateMetaData`

1.  **Method Name**: `generateMetaData`
2.  **Description**: Creates metadata for a content module, specifying whether it's associated with a creator tag or a community tag based on the provided tag UUID.
3.  **Call Stack**:
    *   **Called by**: `selectBestContentModuleBase`
    *   **Calls**: (None identifiable from the provided code)
4.  **Example Usage**:

    ```typescript
    import { TagTypeV2 } from '../../../graphql/generated/graphql';

    const taxonomyData = [
      { tagUUID: 'creator-tag-id', tagType: TagTypeV2.Creator, created_at: new Date(), updated_at: new Date() },
      { tagUUID: 'community-tag-id', tagType: TagTypeV2.Team, created_at: new Date(), updatedAt: new Date() },
    ];

    const creatorMetaData = generateMetaData(taxonomyData, 'creator-tag-id');
    // creatorMetaData will be: { author: { tagUUID: 'creator-tag-id' } }
    console.log('Creator MetaData:', creatorMetaData);

    const communityMetaData = generateMetaData(taxonomyData, 'community-tag-id');
    // communityMetaData will be: { communityTag: { tagUUID: 'community-tag-id' } }
    console.log('Community MetaData:', communityMetaData);

    const noTagMetaData = generateMetaData(taxonomyData, null);
    // noTagMetaData will be: {}
    console.log('No Tag MetaData:', noTagMetaData);
    ```

---

### `fetchAndProcessPersonalizedContent`

1.  **Method Name**: `fetchAndProcessPersonalizedContent`
2.  **Description**: Fetches personalized standalone content modules for given tag UUIDs, applies content type and semantic ID exclusions, then filters out forum-only polls and content from un-followed hook groups.
3.  **Call Stack**:
    *   **Called by**: `buildPersonalizedHomeFeed`
    *   **Calls**:
        *   `ContentModuleRepository.fetchStandaloneContentModulesFromTagsTheUserFollow`: Fetches standalone modules from tags followed by the user.
        *   `excludeHookGroupsUserNotFollowing`: Filters out components associated with tags not followed by the user.
4.  **Example Usage**:

    ```typescript
    import { SemanticID } from '@warnermediacode/graphql-base-enums';
    const tagUUIDs = ['followed-tag-1', 'followed-tag-2'];
    const semanticID = SemanticID.CONTENT_COMMUNITY_FEED;
    const excludeIds = ['excluded-content-id'];

    const personalizedContents = await fetchAndProcessPersonalizedContent(tagUUIDs, semanticID, excludeIds);
    console.log(personalizedContents);
    ```

---

### `resolvePersonalizedHomeHeadlines`

1.  **Method Name**: `resolvePersonalizedHomeHeadlines`
2.  **Description**: Resolves personalized home headlines either by sorting existing content (if no followed tags) or by deduplicating and merging with content matching followed tags.
3.  **Call Stack**:
    *   **Called by**: (Not identifiable from the provided code)
    *   **Calls**:
        *   `getTopUserTags`: Retrieves the top user-followed tags by follower count.
        *   `sortHomeLogo`: Sorts content modules to prioritize a specific tag UUID and adjusts metadata.
        *   `getContentModulesByWrapperIds`: Retrieves content modules based on a list of wrapper content module IDs.
        *   `dedupeContentModules`: Deduplicates an array of content modules.
4.  **Example Usage**:

    ```typescript
    import { TContentModule } from '../../services/contentModules/ContentModuleTypes';
    import { TagV2 } from '../../../graphql/generated/graphql';

    const defaultTag = 'default-home-tag-uuid';
    const userFollowedTags: TagV2[] = [
      { uuid: 'followed-tag-1', followersCount: 100 } as TagV2,
    ];
    const existingContentModules: TContentModule[] = [
      { id: 'cm1', wrapperContentModuleId: 'wcm1', components: [{ Component: { tagUUID: defaultTag } }] } as TContentModule,
    ];
    const limit = 5;

    const personalizedHeadlines = await resolvePersonalizedHomeHeadlines(
      defaultTag,
      userFollowedTags,
      existingContentModules,
      limit,
      false
    );
    console.log(personalizedHeadlines);
    ```

---

### `buildPersonalizedHomeFeed`

1.  **Method Name**: `buildPersonalizedHomeFeed`
2.  **Description**: Constructs a personalized home feed by fetching and deduplicating personalized content, then interlacing it with manually programmed content.
3.  **Call Stack**:
    *   **Called by**: `resolveHomeFeed`
    *   **Calls**:
        *   `fetchAndProcessPersonalizedContent`: Fetches and processes personalized content modules based on user tags.
        *   `dedupeContentModules`: Deduplicates content modules based on content ID and tag weighting.
        *   `deduplicateAgainstProgrammed`: Filters personalized content to avoid duplicates already present in programmed content.
        *   `generateInterlacedHomeFeedContents`: Blends personalized and programmed content into a single, interlaced feed.
4.  **Example Usage**:

    ```typescript
    import { SemanticID } from '@warnermediacode/graphql-base-enums';
    import { TContentModule } from '../../services/contentModules/ContentModuleTypes';
    import { ChannelContentsProps } from '../types';

    const componentProps: ChannelContentsProps = {
      semanticID: SemanticID.CONTENT_COMMUNITY_FEED,
      tagUUID: 'home-feed-tag',
      id: 1,
      insertedAt: new Date(),
      updatedAt: new Date(),
    };
    const sortedUserFollowedTags = ['tag-a', 'tag-b'];
    const excludeHomeTabContents: any[] = [];
    const programmedContents: TContentModule[] = [
      { id: 'p1', contentId: 'c_prog1' } as TContentModule,
    ];

    const personalizedFeed = await buildPersonalizedHomeFeed(
      componentProps,
      sortedUserFollowedTags,
      excludeHomeTabContents,
      programmedContents,
      false, // isInternalRequest
      3 // interlacingInterval
    );
    console.log(personalizedFeed);
    ```

---

### `buildNonPersonalizedHomeFeed`

1.  **Method Name**: `buildNonPersonalizedHomeFeed`
2.  **Description**: Constructs a non-personalized home feed by fetching generic content, deduplicating it, and then interlacing it with manually programmed content.
3.  **Call Stack**:
    *   **Called by**: `resolveHomeFeed`
    *   **Calls**:
        *   `fetchStandaloneFromGenericFeed`: Fetches generic standalone content modules.
        *   `dedupeContentModules`: Deduplicates content modules based on content ID and tag weighting.
        *   `logger.info`: Logs an informational message about the feed length.
        *   `generateInterlacedHomeFeedContents`: Blends generic and programmed content into a single, interlaced feed.
4.  **Example Usage**:

    ```typescript
    import { TContentModule } from '../../services/contentModules/ContentModuleTypes';
    import { ChannelContentsProps } from '../types';

    const componentProps: ChannelContentsProps = {
      semanticID: SemanticID.CONTENT_COMMUNITY_FEED,
      tagUUID: 'home-feed-tag',
      id: 1,
      insertedAt: new Date(),
      updatedAt: new Date(),
    };
    const excludeHomeTabContents: any[] = [];
    const programmedContents: TContentModule[] = [
      { id: 'p1', contentId: 'c_prog1' } as TContentModule,
    ];

    const nonPersonalizedFeed = await buildNonPersonalizedHomeFeed(
      componentProps,
      excludeHomeTabContents,
      programmedContents,
      false, // isInternalRequest
      3 // interlacingInterval
    );
    console.log(nonPersonalizedFeed);
    ```

---

### `deduplicateAgainstProgrammed`

1.  **Method Name**: `deduplicateAgainstProgrammed`
2.  **Description**: Filters out personalized content modules that have the same `contentId` as manually programmed content modules, while ensuring programmed content takes precedence.
3.  **Call Stack**:
    *   **Called by**: `buildPersonalizedHomeFeed`
    *   **Calls**: (None identifiable from the provided code)
4.  **Example Usage**:

    ```typescript
    import { TContentModule } from '../../services/contentModules/ContentModuleTypes';

    const programmedContents: TContentModule[] = [
      { id: 'p1', contentId: 'shared-content-id-1' } as TContentModule,
      { id: 'p2', contentId: 'programmed-only-id' } as TContentModule,
    ];
    const personalizedContents: TContentModule[] = [
      { id: 'per1', contentId: 'shared-content-id-1' } as TContentModule, // Will be replaced by p1
      { id: 'per2', contentId: 'personalized-only-id-1' } as TContentModule,
    ];

    const { dedupeProgrammedContents, personalizedContentsFiltered } = deduplicateAgainstProgrammed(
      programmedContents,
      personalizedContents
    );
    // dedupeProgrammedContents will contain p1 (which may have been modified by per1's data if it was in the map) and p2
    // personalizedContentsFiltered will contain per2
    console.log('Dedupe Programmed:', dedupeProgrammedContents.map((c) => c.id));
    console.log('Personalized Filtered:', personalizedContentsFiltered.map((c) => c.id));
    ```

---

### `sortHomeLogo`

1.  **Method Name**: `sortHomeLogo`
2.  **Description**: Sorts content modules to prioritize a specific tag UUID in their `components` array and sets appropriate metadata for home logo display.
3.  **Call Stack**:
    *   **Called by**: `resolveHomeFeed`, `resolvePersonalizedHomeHeadlines`
    *   **Calls**: (None identifiable from the provided code)
4.  **Example Usage**:

    ```typescript
    import { TContentModule } from '../../services/contentModules/ContentModuleTypes';
    import { TagV2 } from '../../../graphql/generated/graphql';

    const contentModules: TContentModule[] = [
      {
        id: 'cm1',
        components: [
          { Component: { tagUUID: 'other-tag' } },
          { Component: { tagUUID: 'primary-tag-uuid' } },
        ],
      } as TContentModule,
      {
        id: 'cm2',
        components: [{ Component: { tagUUID: 'another-tag' } }],
      } as TContentModule,
    ];
    const tagUUID = 'primary-tag-uuid';

    // Example for a non-internal request (components array will be truncated to 1)
    const sortedExternal = sortHomeLogo(false, contentModules, tagUUID);
    console.log('Sorted External:', sortedExternal[0].components?.[0].Component.tagUUID); // Should be 'primary-tag-uuid'

    // Example for an internal request (components array remains unsorted)
    const sortedInternal = sortHomeLogo(true, contentModules, tagUUID);
    console.log('Sorted Internal:', sortedInternal[0].components?.map((c: any) => c.Component.tagUUID)); // Should be ['primary-tag-uuid', 'other-tag']
    ```

---

### `isNotNull`

1.  **Method Name**: `isNotNull`
2.  **Description**: A TypeScript type guard function that asserts a value is not null, useful for filtering arrays where nulls might be present.
3.  **Call Stack**:
    *   **Called by**: `dedupeContentModules`
    *   **Calls**: (None identifiable from the provided code)
4.  **Example Usage**:

    ```typescript
    const numbers: (number | null)[] = [1, null, 2, 3, null];
    const nonNullNumbers = numbers.filter(isNotNull);
    // nonNullNumbers will be: [1, 2, 3]
    console.log(nonNullNumbers);
    ```