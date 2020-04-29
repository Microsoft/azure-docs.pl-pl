---
title: Obsługa kontenerów
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 2647c604bebc2885f5c8516eb997792b65751e88
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80876096"
---
## <a name="create-an-computer-vision-resource"></a>Tworzenie zasobu przetwarzanie obrazów

1. Zaloguj się do [Azure Portal](https://portal.azure.com).
1. Kliknij pozycję [Utwórz zasób **Przetwarzanie obrazów** ](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision) .
1. Wprowadź wszystkie wymagane ustawienia:

    |Ustawienie|Wartość|
    |--|--|
    |Nazwa|Wymagana nazwa (2-64 znaków)|
    |Subskrypcja|Wybierz odpowiednią subskrypcję|
    |Lokalizacja|Wybierz dowolną lokalizację w pobliżu i dostępną|
    |Warstwa cenowa|`F0`— minimalna warstwa cenowa|
    |Grupa zasobów|Wybierz dostępną grupę zasobów|

1. Kliknij przycisk **Utwórz** i poczekaj na utworzenie zasobu. Po jego utworzeniu przejdź do strony zasobów.
1. Zbierz skonfigurowane `{ENDPOINT_URI}` i `{API_KEY}`zobacz [zbieranie wymaganych parametrów](../computer-vision-how-to-install-containers.md#gathering-required-parameters) , aby uzyskać szczegółowe informacje.
