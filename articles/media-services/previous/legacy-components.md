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
ms.openlocfilehash: 1c3e50fd64c113aafd2d70cc76c7f789e080ad23
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/01/2020
ms.locfileid: "89269526"
---
# <a name="azure-media-services-legacy-components"></a>Azure Media Services starsze składniki

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

W miarę upływu czasu wprowadzono stałe ulepszenia i ulepszenia składników usługi Media. W związku z tym niektóre starsze składniki zostały wycofane. Możesz znaleźć instrukcje dotyczące sposobu migrowania aplikacji ze starszego składnika do bieżącego składnika w poniższych artykułach.
 
## <a name="retirement-plans-of-legacy-components-and-migration-guidance"></a>Plany wycofania starszych składników i wskazówki dotyczące migracji

Ogłaszamy przestarzałe procesory multimediów w *systemie Windows Azure Media Encoder* (WAME) i *Azure Media Encoder* (azwa). Te procesory są wycofywane z 31 marca, 2020.

* [Migrowanie z systemu Windows Azure Media Encoder do Media Encoder Standard](migrate-windows-azure-media-encoder.md)
* [Migrowanie z Azure Media Encoder do Media Encoder Standard](migrate-azure-media-encoder.md)

Ogłaszamy również wycofanie następujących Media Analytics procesorów multimedialnych: 
 
|Nazwa procesora multimediów|Data wycofania|Uwagi dodatkowe|
|---|---|
|[Azure Media Indexer 2](media-services-process-content-with-indexer2.md)|1 stycznia, 2020|Ten procesor multimediów jest zastępowany przez [Azure Media Services Video Indexer](../video-indexer/index.yml). Aby uzyskać więcej informacji, zobacz [Migrowanie z Azure Media Indexer 2 do Azure Media Services Video Indexer](migrate-indexer-v1-v2.md).|
|[Azure Media Indexer](media-services-index-content.md)|1 marca 2023|Ten procesor multimediów jest zastępowany przez [Azure Media Services Video Indexer](../video-indexer/index.yml). Aby uzyskać więcej informacji, zobacz [Migrowanie z Azure Media Indexer do Azure Media Services Video Indexer](migrate-indexer-v1-v2.md)|
|[Wykrywanie ruchu](media-services-motion-detection.md)|1 czerwca, 2020|W tej chwili nie ma żadnych planów zamiany.|
|[Podsumowanie wideo](media-services-video-summarization.md)|1 czerwca, 2020|W tej chwili nie ma żadnych planów zamiany.|
|[Optyczne rozpoznawanie znaków w wideo](media-services-video-optical-character-recognition.md)|1 czerwca, 2020|Ten procesor multimediów jest zastępowany przez [Azure Media Services Video Indexer](../video-indexer/index.yml). Należy również rozważyć użycie [interfejsu API Azure Media Services v3](../latest/analyzing-video-audio-files-concept.md). <br/>Zobacz [porównanie ustawień wstępnych Azure Media Services v3 i video Indexer](../video-indexer/compare-video-indexer-with-media-services-presets.md)|
|[Wykrywanie twarzy](media-services-face-and-emotion-detection.md)|1 czerwca, 2020|Ten procesor multimediów jest zastępowany przez [Azure Media Services Video Indexer](../video-indexer/index.yml). Należy również rozważyć użycie [interfejsu API Azure Media Services v3](../latest/analyzing-video-audio-files-concept.md). <br/>Zobacz [porównanie ustawień wstępnych Azure Media Services v3 i video Indexer](../video-indexer/compare-video-indexer-with-media-services-presets.md)|
|[Content Moderator](media-services-content-moderation.md)|1 czerwca, 2020|Ten procesor multimediów jest zastępowany przez [Azure Media Services Video Indexer](../video-indexer/index.yml). Należy również rozważyć użycie [interfejsu API Azure Media Services v3](../latest/analyzing-video-audio-files-concept.md). <br/>Zobacz [porównanie ustawień wstępnych Azure Media Services v3 i video Indexer](../video-indexer/compare-video-indexer-with-media-services-presets.md)|

## <a name="next-steps"></a>Następne kroki

[Wskazówki dotyczące migracji dotyczące przenoszenia z Media Services V2 do wersji v3](../latest/migrate-from-v2-to-v3.md)
