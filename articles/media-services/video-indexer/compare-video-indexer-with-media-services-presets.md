---
title: Porównanie ustawień wstępnych Video Indexer i Azure Media Services v3
description: W tym artykule porównano możliwości Video Indexer i Azure Media Services ustawień wstępnych v3.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.subservice: video-indexer
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/24/2020
ms.author: juliako
ms.openlocfilehash: ceb105409ff218cd633eb36d793e8fc16c7d135c
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105961544"
---
# <a name="compare-azure-media-services-v3-presets-and-video-indexer"></a>Porównanie ustawień wstępnych Azure Media Services v3 i Video Indexer 

W tym artykule porównano możliwości **interfejsów api Video Indexer** i **interfejsów API Media Services v3**. 

Obecnie istnieje nakładanie się między funkcjami oferowanymi przez [interfejsy api Video Indexer](https://api-portal.videoindexer.ai/) i [interfejsy API Media Services v3](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/Encoding.json). W poniższej tabeli przedstawiono bieżące wytyczne dotyczące interpretacji różnic i podobieństw. 

## <a name="compare"></a>Porównaj

|Cecha|Interfejsy API Video Indexer |Analizator wideo i ustawienia wstępne analizatora dźwięku<br/>w interfejsach API Media Services v3|
|---|---|---|
|Szczegółowe informacje o multimediach|[Rozszerzone](video-indexer-output-json-v2.md) |[Podstawy](../latest/analyze-video-audio-files-concept.md)|
|Możliwości|Zapoznaj się z pełną listą obsługiwanych funkcji: <br/> [Omówienie](video-indexer-overview.md)|Zwraca tylko wgląd w dane wideo|
|Rozliczenia|[Cennik Media Services](https://azure.microsoft.com/pricing/details/media-services/#analytics)|[Cennik Media Services](https://azure.microsoft.com/pricing/details/media-services/#analytics)|
|Zgodność|Aby zapoznać się z najnowszymi aktualizacjami zgodności, odwiedź stronę [zgodność platformy Azure Offerings.pdf](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942/file/178110/23/Microsoft%20Azure%20Compliance%20Offerings.pdf) i wyszukaj ciąg "Video Indexer", aby sprawdzić, czy jest on zgodny z certyfikatem zainteresowań.|Aby zapoznać się z najnowszymi aktualizacjami zgodności, odwiedź stronę [zgodność platformy Azure Offerings.pdf](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942/file/178110/23/Microsoft%20Azure%20Compliance%20Offerings.pdf) i wyszukaj ciąg "Media Services", aby sprawdzić, czy jest on zgodny z certyfikatem zainteresowań.|
|Bezpłatna wersja próbna|East US|Niedostępne|
|Dostępność w danym regionie|Zobacz [dostępność Cognitive Services według regionów](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)|Zobacz [dostępność Media Services według regionów](https://azure.microsoft.com/global-infrastructure/services/?products=media-services).|

## <a name="next-steps"></a>Następne kroki

[Omówienie usługi Video Indexer](video-indexer-overview.md)

[Media Services wersja 3 — Omówienie](../latest/media-services-overview.md)
