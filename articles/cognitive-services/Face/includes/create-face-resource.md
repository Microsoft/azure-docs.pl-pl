---
title: Obsługa kontenerów
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 7/5/2019
ms.author: aahi
ms.openlocfilehash: 0a330c2401412e1e3d5e2e49bf8121a681342808
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "80878373"
---
## <a name="create-an-face-resource"></a>Tworzenie zasobu kroju

1. Zaloguj się do [Azure Portal](https://portal.azure.com)
1. Kliknij pozycję [Utwórz zasób **czołowy** ](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFace)
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
    |**Omówienie**|Punkt końcowy|Skopiuj punkt końcowy. Wygląda podobnie do `https://face.cognitiveservices.azure.com/face/v1.0`|
    |**Klucze**|Klucz interfejsu API|Kopiuj 1 z dwóch kluczy. Jest to 32 ciąg znaków alfanumerycznych bez spacji ani kresek `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` .|
