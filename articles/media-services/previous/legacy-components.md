---
title: Azure Media Services starsze składniki | Microsoft Docs
description: W tym temacie omówiono Azure Media Services starszych składników.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2020
ms.author: juliako
ms.openlocfilehash: 94a70a1234d902787f248890f0cb538a4ba9c2f9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "77921083"
---
# <a name="azure-media-services-legacy-components"></a>Azure Media Services starsze składniki

W miarę upływu czasu wprowadzono stałe ulepszenia i ulepszenia składników usługi Media. W związku z tym niektóre starsze składniki zostały wycofane. Możesz znaleźć instrukcje dotyczące sposobu migrowania aplikacji ze starszego składnika do bieżącego składnika w poniższych artykułach.
 
## <a name="retirement-plans-of-legacy-components-and-migration-guidance"></a>Plany wycofania starszych składników i wskazówki dotyczące migracji

Ogłaszamy przestarzałe procesory multimediów w *systemie Windows Azure Media Encoder* (WAME) i *Azure Media Encoder* (azwa). Te procesory są wycofywane z 31 marca, 2020.

* [Migrowanie z systemu Windows Azure Media Encoder do Media Encoder Standard](migrate-windows-azure-media-encoder.md)
* [Migrowanie z Azure Media Encoder do Media Encoder Standard](migrate-azure-media-encoder.md)

Ogłaszamy również wycofanie następujących Media Analytics procesorów multimedialnych: 
 
|Nazwa procesora multimediów|Data wycofania|Uwagi dodatkowe|
|---|---|
|[Azure Media Indexer 2](media-services-process-content-with-indexer2.md)|1 stycznia, 2020|Ten procesor multimediów jest zastępowany przez [Azure Media Services Video Indexer](https://docs.microsoft.com/azure/media-services/video-indexer/). Aby uzyskać więcej informacji, zobacz [Migrowanie z Azure Media Indexer 2 do Azure Media Services Video Indexer](migrate-indexer-v1-v2.md).|
|[Azure Media Indexer](media-services-index-content.md)|1 marca 2023|Ten procesor multimediów jest zastępowany przez [Azure Media Services Video Indexer](https://docs.microsoft.com/azure/media-services/video-indexer/). Aby uzyskać więcej informacji, zobacz [Migrowanie z Azure Media Indexer do Azure Media Services Video Indexer](migrate-indexer-v1-v2.md)|
|[Wykrywanie ruchu](media-services-motion-detection.md)|1 czerwca, 2020|W tej chwili nie ma żadnych planów zamiany.|
|[Podsumowanie wideo](media-services-video-summarization.md)|1 czerwca, 2020|W tej chwili nie ma żadnych planów zamiany.|
|[Optyczne rozpoznawanie znaków w wideo](media-services-video-optical-character-recognition.md)|1 czerwca, 2020|Ten procesor multimediów jest zastępowany przez [Azure Media Services Video Indexer](https://docs.microsoft.com/azure/media-services/video-indexer/). Należy również rozważyć użycie [interfejsu API Azure Media Services v3](https://docs.microsoft.com/azure/media-services/latest/analyzing-video-audio-files-concept). <br/>Zobacz [porównanie ustawień wstępnych Azure Media Services v3 i video Indexer](https://docs.microsoft.com/azure/media-services/video-indexer/compare-video-indexer-with-media-services-presets)|
|[Wykrywanie twarzy](media-services-face-and-emotion-detection.md)|1 czerwca, 2020|Ten procesor multimediów jest zastępowany przez [Azure Media Services Video Indexer](https://docs.microsoft.com/azure/media-services/video-indexer/). Należy również rozważyć użycie [interfejsu API Azure Media Services v3](https://docs.microsoft.com/azure/media-services/latest/analyzing-video-audio-files-concept). <br/>Zobacz [porównanie ustawień wstępnych Azure Media Services v3 i video Indexer](https://docs.microsoft.com/azure/media-services/video-indexer/compare-video-indexer-with-media-services-presets)|
|[Content Moderator](media-services-content-moderation.md)|1 czerwca, 2020|Ten procesor multimediów jest zastępowany przez [Azure Media Services Video Indexer](https://docs.microsoft.com/azure/media-services/video-indexer/). Należy również rozważyć użycie [interfejsu API Azure Media Services v3](https://docs.microsoft.com/azure/media-services/latest/analyzing-video-audio-files-concept). <br/>Zobacz [porównanie ustawień wstępnych Azure Media Services v3 i video Indexer](https://docs.microsoft.com/azure/media-services/video-indexer/compare-video-indexer-with-media-services-presets)|

## <a name="next-steps"></a>Następne kroki

[Wskazówki dotyczące migracji dotyczące przenoszenia z Media Services V2 do wersji v3](../latest/migrate-from-v2-to-v3.md)
