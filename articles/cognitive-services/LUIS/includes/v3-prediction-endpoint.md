---
title: Jak uzyskać punkt końcowy przewidywania wersji v3
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: include
ms.date: 05/05/2020
ms.author: diberry
ms.openlocfilehash: d73508e8734883f5ffef205b6c1f03905f349f91
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91316492"
---
1. W portalu LUIS w sekcji **Zarządzanie** (menu w prawym górnym rogu) na stronie **zasoby platformy Azure** (menu po lewej) na karcie **zasoby predykcyjne** Skopiuj **przykładowe zapytanie** w dolnej części strony.

    Wklej adres URL do nowej karty przeglądarki.

    Adres URL zawiera identyfikator aplikacji, klucz i nazwę gniazda. Adres URL punktu końcowego przewidywania v3 wygląda następująco:

    `https://YOUR-RESOURCE-NAME.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/APP-ID/slots/SLOT-NAME/predict?subscription-key=YOUR-PREDICTION-KEY&<optional-name-value-pairs>&query=YOUR_QUERY_HERE`

