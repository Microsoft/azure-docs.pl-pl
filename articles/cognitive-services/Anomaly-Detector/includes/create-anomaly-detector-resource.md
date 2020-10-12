---
title: Obsługa kontenerów
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/10/2020
ms.author: aahi
ms.openlocfilehash: 8f93e0ffe74ade79059fc845788faf36ab5b4f13
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91025341"
---
## <a name="create-an-anomaly-detector-resource"></a>Tworzenie zasobu wykrywania anomalii

1. Zaloguj się do [Azure Portal](https://portal.azure.com)
1. Kliknij pozycję [Utwórz zasób **wykrywania anomalii** ](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector)
1. Wprowadź wszystkie wymagane ustawienia:

    |Ustawienie|Wartość|
    |--|--|
    |Nazwa|Wymagana nazwa (2-64 znaków)|
    |Subskrypcja|Wybierz odpowiednią subskrypcję|
    |Lokalizacja|Wybierz dowolną lokalizację w pobliżu i dostępną|
    |Warstwa cenowa|`F0` — minimalna warstwa cenowa|
    |Grupa zasobów|Wybierz dostępną grupę zasobów|

1. Kliknij przycisk **Utwórz** i poczekaj na utworzenie zasobu. Po jego utworzeniu przejdź do strony zasobów
1. Skonfigurowane usługi zbierania `endpoint` i klucz interfejsu API:

    |Karta zasób w portalu|Ustawienie|Wartość|
    |--|--|--|
    |**Omówienie**|Punkt końcowy|Skopiuj punkt końcowy. Wygląda podobnie do `https://westus2.api.cognitive.microsoft.com/`|
    |**Klucze**|Klucz interfejsu API|Kopiuj 1 z dwóch kluczy. Jest to 32 ciąg znaków alfanumerycznych bez spacji ani kresek `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` .|



