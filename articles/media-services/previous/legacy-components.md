---
title: Azure Media Services starsze składniki | Microsoft Docs
description: W tym temacie omówiono Azure Media Services starszych składników.
services: media-services
documentationcenter: ''
author: myoungerman
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2020
ms.author: v-myoung
ms.openlocfilehash: d5a10ccc887124b2bc5595d66baad833a32275cc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90600895"
---
# <a name="azure-media-services-legacy-components"></a>Azure Media Services starsze składniki

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

W miarę upływu czasu ulepszamy składniki usługi Media Service i cofają starsze składniki. Ten artykuł ułatwia Migrowanie aplikacji ze starszego składnika do bieżącego składnika.
 
## <a name="retirement-plans-of-legacy-components-and-migration-guidance"></a>Plany wycofania starszych składników i wskazówki dotyczące migracji

Procesory multimediów *Azure Media Encoder systemu Windows* (WAME) i *Azure Media Encoder* (azwa) są przestarzałe.

* [Migrowanie z systemu Windows Azure Media Encoder do Media Encoder Standard](migrate-windows-azure-media-encoder.md)
* [Migrowanie z Azure Media Encoder do Media Encoder Standard](migrate-azure-media-encoder.md)

Następujące Media Analytics procesorów multimediów są przestarzałe lub wkrótce będą przestarzałe:  
 
|Nazwa procesora multimediów|Data wycofania|Uwagi dodatkowe|
|---|---|
|Azure Media Indexer 2|1 stycznia, 2020|Ten procesor multimedialny został zastąpiony przez [Azure Media Services Video Indexer](../video-indexer/index.yml). Aby uzyskać więcej informacji, zobacz [Migrowanie z Azure Media Indexer 2 do Azure Media Services Video Indexer](migrate-indexer-v1-v2.md).|
|[Azure Media Indexer](media-services-index-content.md)|1 marca 2023|Ten procesor multimediów zostanie zastąpiony przez [Azure Media Services Video Indexer](../video-indexer/index.yml). Aby uzyskać więcej informacji, zobacz [Migrowanie z Azure Media Indexer do Azure Media Services Video Indexer](migrate-indexer-v1-v2.md).|
|Wykrywanie ruchu|1 czerwca, 2020|W tej chwili nie ma żadnych planów zamiany.|
|Podsumowanie wideo] (Media-Services-Video-summarization.md|1 czerwca, 2020|W tej chwili nie ma żadnych planów zamiany.|
|Optyczne rozpoznawanie znaków w wideo|1 czerwca, 2020|Ten procesor multimedialny został zastąpiony przez [Azure Media Services Video Indexer](../video-indexer/index.yml). Należy również rozważyć użycie [interfejsu API Azure Media Services v3](../latest/analyzing-video-audio-files-concept.md). <br/>Zobacz [porównanie ustawień wstępnych Azure Media Services v3 i video Indexer](../video-indexer/compare-video-indexer-with-media-services-presets.md).|
|Wykrywanie twarzy|1 czerwca, 2020|Ten procesor multimedialny został zastąpiony przez [Azure Media Services Video Indexer](../video-indexer/index.yml). Należy również rozważyć użycie [interfejsu API Azure Media Services v3](../latest/analyzing-video-audio-files-concept.md). <br/>Zobacz [porównanie ustawień wstępnych Azure Media Services v3 i video Indexer](../video-indexer/compare-video-indexer-with-media-services-presets.md).|
|Content Moderator|1 czerwca, 2020|Ten procesor multimedialny został zastąpiony przez [Azure Media Services Video Indexer](../video-indexer/index.yml). Należy również rozważyć użycie [interfejsu API Azure Media Services v3](../latest/analyzing-video-audio-files-concept.md). <br/>Zobacz [porównanie ustawień wstępnych Azure Media Services v3 i video Indexer](../video-indexer/compare-video-indexer-with-media-services-presets.md).|

## <a name="next-steps"></a>Następne kroki

[Wskazówki dotyczące migracji dotyczące przenoszenia z Media Services V2 do wersji v3](../latest/migrate-from-v2-to-v3.md)
