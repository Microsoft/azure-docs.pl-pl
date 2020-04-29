---
title: Jak uzyskać punkt końcowy przewidywania wersji v3
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: 23e7f88b23ac7af7470183877eeb1be89e79779c
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80287751"
---
1. W portalu LUIS w sekcji **Zarządzanie** (menu w prawym górnym rogu) na stronie **zasoby platformy Azure** (menu po lewej) na karcie **zasoby predykcyjne** Skopiuj **przykładowe zapytanie** w dolnej części strony.

    Wklej adres URL do nowej karty przeglądarki.

    Adres URL zawiera identyfikator aplikacji, klucz i nazwę gniazda. Adres URL punktu końcowego przewidywania v3 wygląda następująco:

    `https://REGION.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/APP-ID/slots/SLOT-NAME/predict?subscription-key=YOUR-KEY&<optional-name-value-pairs>&query=YOUR_QUERY_HERE`

