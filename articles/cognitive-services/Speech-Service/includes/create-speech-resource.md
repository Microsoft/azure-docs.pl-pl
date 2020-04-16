---
title: Obsługa kontenerów
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 7/5/2019
ms.author: trbye
ms.openlocfilehash: 8abd520a7bb80f1f9a2d8ebcbe46b90fe59a96ec
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81421724"
---
## <a name="create-a-speech-resource"></a>Tworzenie zasobu Mowy

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com)
1. Kliknij [pozycję Utwórz **Speech** ](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices) zasób Mowy
1. Wprowadź wszystkie wymagane ustawienia:

    |Ustawienie|Wartość|
    |--|--|
    |Nazwa|Żądana nazwa (2-64 znaki)|
    |Subskrypcja|Wybierz odpowiednią subskrypcję|
    |Lokalizacja|Wybierz dowolną lokalizację w pobliżu i do dyspozycji|
    |Warstwa cenowa|`F0`- minimalny poziom cenowy|
    |Grupa zasobów|Wybieranie dostępnej grupy zasobów|

1. Kliknij **przycisk Utwórz** i poczekaj na utworzenie zasobu. Po utworzeniu przejdź do strony zasobu
1. Zbieranie skonfigurowanych `endpoint` i klucz interfejsu API:

    |Karta Zasoby w portalu|Ustawienie|Wartość|
    |--|--|--|
    |**Omówienie**|Endpoint|Skopiuj punkt końcowy. Wygląda podobnie do`https://speech.cognitiveservices.azure.com/sts/v1.0/issuetoken`|
    |**Klucze**|Klucz interfejsu API|Skopiuj 1 z dwóch klawiszy. Jest to ciąg 32 znaków alfanumerycznej bez spacji lub kresek, `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.|
