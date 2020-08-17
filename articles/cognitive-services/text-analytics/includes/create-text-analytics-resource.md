---
title: Tworzenie zasobu analizy tekstu usług Cognitive Services
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak utworzyć zasób analiza tekstu Cognitive Services.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 6cd653909e26dc5e0484ca289a1d2ab47e20457f
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80876446"
---
## <a name="create-a-cognitive-services-text-analytics-resource"></a>Tworzenie zasobu analizy tekstu usług Cognitive Services

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Wybierz pozycję **Utwórz zasób**, a następnie przejdź do pozycji **SI oraz usługa Machine Learning** > **Analiza tekstu**.
   Lub przejdź do pozycji [utwórz analiza tekstu](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics).
1. Wprowadź wszystkie wymagane ustawienia:

    |Ustawienie|Wartość|
    |--|--|
    |Nazwa|Wprowadź nazwę (2–64 znaków).|
    |Subskrypcja|Wybierz odpowiednią subskrypcję.|
    |Lokalizacja|Wybierz lokalizację w pobliżu.|
    |Warstwa cenowa| Wprowadź **S**, standardową warstwę cenową.|
    |Grupa zasobów|Wybierz dostępną grupę zasobów.|

1. Wybierz pozycję **Utwórz** i zaczekaj na utworzenie zasobu. Przeglądarka automatycznie przekieruje Cię do nowo utworzonej strony zasobów.
1. Zbierz skonfigurowany `endpoint` i klucz interfejsu API:

    |Karta zasób w portalu|Ustawienie|Wartość|
    |--|--|--|
    |**Omówienie**|Punkt końcowy|Skopiuj punkt końcowy. Wygląda podobnie do `https://northeurope.api.cognitive.microsoft.com/text/analytics/v2.0` .|
    |**Klucze**|Klucz interfejsu API|Skopiuj jeden z dwóch kluczy. Jest to 32-znakowy ciąg alfanumeryczny bez spacji ani kresek: <`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`>.|
