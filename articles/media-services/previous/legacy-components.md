---
title: Azure Media Services starsze składniki | Microsoft Docs
description: W tym temacie omówiono Azure Media Services starszych składników.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 3487e735e48cb5067515762d6276429ca81e43fe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103008743"
---
# <a name="azure-media-services-legacy-components"></a>Azure Media Services starsze składniki

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

W miarę upływu czasu ulepszamy składniki usługi Media Service i cofają starsze składniki. Ten artykuł ułatwia Migrowanie aplikacji ze starszego składnika do bieżącego składnika.
 
## <a name="retirement-plans-of-legacy-components-and-migration-guidance"></a>Plany wycofania starszych składników i wskazówki dotyczące migracji

Procesory multimediów *Azure Media Encoder systemu Windows* (WAME) i *Azure Media Encoder* (azwa) są przestarzałe.

* [Migrowanie z systemu Windows Azure Media Encoder do Media Encoder Standard](migrate-windows-azure-media-encoder.md)
* [Migrowanie z Azure Media Encoder do Media Encoder Standard](migrate-azure-media-encoder.md)

Następujące Media Analytics procesorów multimediów są przestarzałe lub wkrótce będą przestarzałe:

  
 
| **Nazwa procesora multimediów** | **Data wycofania** | **Uwagi dodatkowe** |
| --- | --- | ---|
| Azure Media Indexer 2 | 1 stycznia, 2020 | Ten procesor multimedialny zostanie zastąpiony przez [tryb Media Services v3 AudioAnalyzerPreset](../latest/analyzing-video-audio-files-concept.md). Aby uzyskać więcej informacji, zobacz [Migrowanie z Azure Media Indexer 2 do Azure Media Services Video Indexer](migrate-indexer-v1-v2.md). |
| Azure Media Indexer | 1 marca 2023 | Ten procesor multimedialny zostanie zastąpiony przez [tryb Media Services v3 AudioAnalyzerPreset](../latest/analyzing-video-audio-files-concept.md). Aby uzyskać więcej informacji, zobacz [Migrowanie z Azure Media Indexer 2 do Azure Media Services Video Indexer](migrate-indexer-v1-v2.md). |
| Wykrywanie ruchu | 1 czerwca, 2020|W tej chwili nie ma żadnych planów zamiany. |
| Podsumowanie wideo |1 czerwca, 2020|W tej chwili nie ma żadnych planów zamiany.|
| Optyczne rozpoznawanie znaków w wideo | 1 czerwca, 2020 |Ten procesor multimedialny został zastąpiony przez [Azure Media Services Video Indexer](../video-indexer/index.yml). Należy również rozważyć użycie [interfejsu API Azure Media Services v3](../latest/analyzing-video-audio-files-concept.md). <br/>Zobacz [porównanie ustawień wstępnych Azure Media Services v3 i video Indexer](../video-indexer/compare-video-indexer-with-media-services-presets.md). |
| Wykrywanie twarzy | 1 czerwca, 2020 | Ten procesor multimedialny został zastąpiony przez [Azure Media Services Video Indexer](../video-indexer/index.yml). Należy również rozważyć użycie [interfejsu API Azure Media Services v3](../latest/analyzing-video-audio-files-concept.md). <br/>Zobacz [porównanie ustawień wstępnych Azure Media Services v3 i video Indexer](../video-indexer/compare-video-indexer-with-media-services-presets.md). |
| Content Moderator | 1 czerwca, 2020 |Ten procesor multimedialny został zastąpiony przez [Azure Media Services Video Indexer](../video-indexer/index.yml). Należy również rozważyć użycie [interfejsu API Azure Media Services v3](../latest/analyzing-video-audio-files-concept.md). <br/>Zobacz [porównanie ustawień wstępnych Azure Media Services v3 i video Indexer](../video-indexer/compare-video-indexer-with-media-services-presets.md). |

## <a name="next-steps"></a>Następne kroki

[Wskazówki dotyczące migracji dotyczące przenoszenia z Media Services V2 do wersji v3](../latest/migrate-v-2-v-3-migration-introduction.md)
