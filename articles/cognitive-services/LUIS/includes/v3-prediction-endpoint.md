---
title: Jak uzyskać punkt końcowy przewidywania wersji v3
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 05/05/2020
ms.author: diberry
ms.openlocfilehash: 18f5422202cf972d49349cc04b845c623cabffa3
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83589156"
---
1. W portalu LUIS w sekcji **Zarządzanie** (menu w prawym górnym rogu) na stronie **zasoby platformy Azure** (menu po lewej) na karcie **zasoby predykcyjne** Skopiuj **przykładowe zapytanie** w dolnej części strony.

    Wklej adres URL do nowej karty przeglądarki.

    Adres URL zawiera identyfikator aplikacji, klucz i nazwę gniazda. Adres URL punktu końcowego przewidywania v3 wygląda następująco:

    `https://YOUR-RESOURCE-NAME.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/APP-ID/slots/SLOT-NAME/predict?subscription-key=YOUR-PREDICTION-KEY&<optional-name-value-pairs>&query=YOUR_QUERY_HERE`

