---
title: Sign up for Azure Video Indexer and upload your first video - Azure
description: Learn how to sign up and upload your first video using the Azure Video Indexer portal.
ms.topic: quickstart
ms.date: 01/25/2021
ms.author: juliako
ms.custom: mode-other
---

# Quickstart: How to sign up and upload your first video

This getting started quickstart shows how to sign in to the Azure Video Indexer website and how to upload your first video.

When creating an Azure Video Indexer account, you can choose a free trial account (where you get a certain number of free indexing minutes) or a paid option (where you aren't limited by the quota). With free trial, Azure Video Indexer provides up to 600 minutes of free indexing to website users and up to 2400 minutes of free indexing to API users. With paid option, you create an Azure Video Indexer account that is [connected to your Azure subscription and an Azure Media Services account](connect-to-azure.md). You pay for minutes indexed, for more information, see [Media Services pricing](https://azure.microsoft.com/pricing/details/media-services/).

## Sign up for Azure Video Indexer

To start developing with Azure Video Indexer, browse to the [Azure Video Indexer](https://www.videoindexer.ai/) website and sign up.

Once you start using Azure Video Indexer, all your stored data and uploaded content are encrypted at rest with a Microsoft managed key.

> [!NOTE]
> Review [planned Azure Video Indexer website authenticatication changes](./release-notes.md#planned-azure-video-indexer-website-authenticatication-changes).

## Upload a video using the Azure Video Indexer website

### Supported file formats for Azure Video Indexer

See the [input container/file formats](/azure/media-services/latest/encode-media-encoder-standard-formats-reference) article for a list of file formats that you can use with Azure Video Indexer.

### Upload a video

1. Sign in on the [Azure Video Indexer](https://www.videoindexer.ai/) website.
1. To upload a video, press the **Upload** button or link.

    > [!NOTE]
    > The name of the video must be no greater than 80 characters.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/video-indexer-get-started/video-indexer-upload.png" alt-text="Upload":::
1. Once your video has been uploaded, Azure Video Indexer starts indexing and analyzing the video. As a result a JSON output with insights is produced. 

    You see the progress.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/video-indexer-get-started/progress.png" alt-text="Progress of the upload"::: 

    The produced JSON output contains  `Insights` and `SummarizedInsights` elements. We highly recommend using `Insights` and not using `SummarizedInsights` (which is present for backward compatibility). 
1. Once Azure Video Indexer is done analyzing, you'll get an email with a link to your video and a short description of what was found in your video. For example: people, spoken and written words, topics, and named entities.
1. You can later find your video in the library list and perform different operations. For example: search, reindex, edit.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/video-indexer-get-started/uploaded.png" alt-text="Uploaded the upload":::

After you upload and index a video, you can continue using [Azure Video Indexer website](video-indexer-view-edit.md) or [Azure Video Indexer Developer Portal](video-indexer-use-apis.md) to see the insights of the video (see [Examine the Azure Video Indexer output](video-indexer-output-json-v2.md)).

For more details, see [Upload and index videos](upload-index-videos.md).

To start using the APIs, see [use APIs](video-indexer-use-apis.md)

## Supported browsers

For more information, see [supported browsers](video-indexer-overview.md#supported-browsers).

## Next steps

For detailed introduction please visit our [introduction lab](https://github.com/Azure-Samples/media-services-video-indexer/blob/master/IntroToVideoIndexer.md).

At the end of the workshop, you'll have a good understanding of the kind of information that can be extracted from video and audio content, you'll be more prepared to identify opportunities related to content intelligence, pitch video AI on Azure, and demo several scenarios on Azure Video Indexer.
