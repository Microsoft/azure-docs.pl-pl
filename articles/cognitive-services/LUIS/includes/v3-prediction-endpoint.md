---
title: Jak uzyskać punkt końcowy przewidywania wersji v3
titleSuffix: Azure Cognitive Services
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: include
ms.date: 05/05/2020
ms.openlocfilehash: 85fcd0308083350a02afd4eadac2b361337f3b33
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93128222"
---
1. W portalu LUIS w sekcji **Zarządzanie** (menu w prawym górnym rogu) na stronie **zasoby platformy Azure** (menu po lewej) na karcie **zasoby predykcyjne** Skopiuj **przykładowe zapytanie** w dolnej części strony. Adres URL zawiera identyfikator aplikacji, klucz i nazwę gniazda. Adres URL punktu końcowego przewidywania v3 ma postać: `https://YOUR-RESOURCE-NAME.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/APP-ID/slots/SLOT-NAME/predict?subscription-key=YOUR-PREDICTION-KEY&<optional-name-value-pairs>&query=YOUR_QUERY_HERE`

    :::image type="content" source="../media/prediction-resources-example-query.png" alt-text="Przykładowe zapytanie w sekcji zasobów predykcyjnych" lightbox="../media/prediction-resources-example-query.png":::
    
    Wklej adres URL do nowej karty przeglądarki. Jeśli adres URL nie jest widoczny, nie masz zasobu predykcyjnego i należy go utworzyć. 

    

    

