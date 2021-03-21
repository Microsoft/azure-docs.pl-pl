---
title: Tworzenie zasobu analizy tekstu usług Cognitive Services
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak utworzyć zasób analiza tekstu Cognitive Services.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 02/09/2021
ms.author: aahi
ms.openlocfilehash: 5b6479d48a51ba962f2f6bfba16dac3b0886a9ff
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101750837"
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
    |**Omówienie**|Punkt końcowy|Skopiuj punkt końcowy. Wygląda podobnie do `https://my-resource.cognitiveservices.azure.com/text/analytics/v3.0` .|
    |**Klucze**|Klucz interfejsu API|Skopiuj jeden z dwóch kluczy. Jest to 32-znakowy ciąg alfanumeryczny bez spacji ani kresek: <`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`>.|
