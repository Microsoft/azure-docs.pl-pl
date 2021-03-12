---
title: Tworzenie zaawansowanych przepływów pracy kodowania za pomocą Projektant przepływu pracy | Microsoft Docs
description: Dowiedz się, jak tworzyć zaawansowane przepływy pracy kodowania przy użyciu Projektant przepływu pracy.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 004815f2-0761-4706-87a1-675ba36e0322
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2021
ms.author: anilmur
ms.reviewer: juliako;johndeu
ms.openlocfilehash: 8173da37792948e267aae2078fee9f864bf7bdc9
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/11/2021
ms.locfileid: "103011157"
---
# <a name="create-advanced-encoding-workflows-with-workflow-designer"></a>Tworzenie zaawansowanych przepływów pracy kodowania za pomocą projektanta przepływu pracy

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

## <a name="overview"></a>Omówienie
**Projektant przepływu pracy** to narzędzie pulpitu systemu Windows, które służy do projektowania i tworzenia niestandardowych przepływów pracy dla kodowania z **Media Encoder Premium Workflow**.
Korzystając z możliwości narzędzia Projektant przepływu pracy, można projektować i tworzyć złożone przepływy pracy, które będą uruchamiane w usłudze **Media Encoder Premium**.  

Przepływy pracy mogą obejmować logikę decyzyjną klienta i rozgałęzianie na podstawie właściwości wejściowego pliku źródłowego. Możesz tworzyć przepływy pracy z właściwościami i wartościami dynamicznymi, aby ułatwić powtarzanie i dostosowywanie bardziej złożonych zadań kodowania w chmurze.

Przykładowe przepływy pracy, które można utworzyć, obejmują:

* Przepływy pracy oparte na decyzjach, które sprawdzają zawartość źródłową do rozpoznawania i kodują tylko żądane ścieżki wyjściowe.  Jest to przydatne w wyniku wyeliminowania niepowstających ścieżek, które zostałyby wygenerowane przez skalowanie zawartości źródłowej przypadkowo.
* Wiele plików wejściowych może służyć do obsługi podpisów, nakładania i łączenia się z zawartością. 

Tego narzędzia można również użyć do zmodyfikowania dowolnego z [opublikowanych przepływów pracy](media-services-workflow-designer.md#existing_workflows). 

> [!NOTE]
> Aby uzyskać kopię narzędzia Projektant przepływu pracy, skontaktuj się z firmą mepd@microsoft.com .

Po utworzeniu pliku przepływu pracy można go przekazać jako zasób, a następnie użyć do kodowania plików multimedialnych. Aby uzyskać informacje na temat kodowania za pomocą **Media Encoder Premium Workflow** przy użyciu **platformy .NET**, zobacz [Zaawansowane kodowanie z Media Encoder Premium Workflow](media-services-encode-with-premium-workflow.md).

## <a name="modify-existing-workflows"></a><a id="existing_workflows"></a>Modyfikuj istniejące przepływy pracy
Domyślne [opublikowane przepływy pracy](media-services-workflow-designer.md#existing_workflows) można modyfikować za pomocą narzędzia Projektant. Domyślne pliki przepływu pracy można uzyskać [tutaj](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows). Folder zawiera również opis tych plików.

Poniższe wideo pokazują, jak używać projektanta.

### <a name="day-1--getting-started"></a>Dzień 1 — Wprowadzenie
:

* Omówienie projektanta
* Podstawowe przepływy pracy — "Hello world"
* Tworzenie wielu wyjściowych plików MP4 do użycia z Azure Media Services Streaming

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-Premium-Encoder-Workflow-Designer-Training-Videos-Day-1/player]
> 
> 

### <a name="day-2"></a>Dzień 2
Okładka wideo:

* Różne scenariusze dotyczące pliku źródłowego — obsługa dźwięku
* Przepływy pracy z logiką zaawansowaną
* Etapy wykresu

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-Premium-Encoder-Workflow-Designer-Training-Videos-Day-2/player]
> 
> 

### <a name="day-3"></a>Dzień 3
Okładka wideo:

* Obsługa skryptów w przepływach pracy/planach
* Ograniczenia z bieżącym koderem
* Pytania i odpowiedzi

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-Premium-Encoder-Workflow-Designer-Training-Videos-Day-3/player]
> 
> 

## <a name="need-help"></a>Potrzebujesz pomocy?

Możesz otworzyć bilet pomocy technicznej, przechodząc do [nowego żądania obsługi](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

## <a name="next-step"></a>Następny krok
Przejrzyj ścieżki szkoleniowe dotyczące usługi Media Services.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Wyraź opinię
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Zobacz też
[Wideo szkoleniowe dotyczące usługi Azure Premium Encoder Projektant przepływu pracy](http://johndeutscher.com/2015/07/06/azure-premium-encoder-workflow-designer-training-videos/)

