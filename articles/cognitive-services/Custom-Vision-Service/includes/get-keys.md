---
author: PatrickFarley
ms.service: cognitive-services
ms.topic: include
ms.date: 02/07/2019
ms.author: pafarley
ms.openlocfilehash: 874d76bebdfd3bd0daba1f83cb1f06c093f192ec
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "89411736"
---
## <a name="get-the-training-and-prediction-keys"></a>Uzyskaj klucze interfejsów szkolenia i przewidywania

Aby można było korzystać z usługi, projekt musi mieć prawidłowy zestaw kluczy subskrypcji. Elementy można znaleźć w  [witrynie sieci web Custom Vision](https://customvision.ai). Zaloguj się przy użyciu konta skojarzonego z kontem platformy Azure, które zostało użyte do utworzenia zasobów Custom Vision. Na stronie głównej (na stronie z opcją dodania nowego projektu) wybierz __ikonę koła zębatego__ w prawym górnym rogu. Znajdź na liście zasoby szkoleniowe i predykcyjne i rozwiń je. W tym miejscu można znaleźć wartości klucza szkoleniowego, klucza predykcyjnego i identyfikatora zasobu przewidywania. Zapisz te wartości w tymczasowej lokalizacji.

> [!NOTE]
> Jeśli w celu uzyskania dostępu do Custom Vision jest używany Cognitive Services klucz "All-in-one-in-one-in-one-Only", na ekranie Ustawienia zobaczysz tylko jeden klucz. W takim przypadku użyjesz tego samego klucza dla operacji szkoleniowych i predykcyjnych.

![Obraz interfejsu użytkownika do uzyskiwania kluczy](../media/csharp-tutorial/training-prediction-keys.png)

Można też uzyskać te klucze i identyfikator z [Azure Portal](https://www.portal.azure.com) , wyświetlając Custom Vision szkolenia i zasoby przewidywania i przechodząc do karty __klucze__ . Twój klucz szkoleniowy i klucz predykcyjny zostaną znalezione. Przejdź do karty __Właściwości__ zasobu predykcyjnego, aby uzyskać identyfikator zasobu predykcyjnego.

