---
title: Wbudowane odwołanie do domeny — LUIS
titleSuffix: Azure Cognitive Services
description: Dokumentacja wstępnie skompilowanych domen, które są wstępnie skompilowanymi kolekcjami intencji i jednostek z Language Understanding inteligentnych usług (LUIS).
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/27/2019
ms.openlocfilehash: 73b279f98011181b329cdb010041022ab0da57f0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "95018637"
---
# <a name="prebuilt-domain-reference-for-your-luis-app"></a>Wstępnie skompilowane odwołanie do domeny dla aplikacji LUIS
Ta dokumentacja zawiera informacje na temat [wstępnie skompilowanych domen](./howto-add-prebuilt-models.md), które są wstępnie skompilowanymi kolekcjami intencji i jednostek, które Luis oferty.

[Domeny niestandardowe](luis-how-to-start-new-app.md), z kolei, zaczynają się od żadnych zamiar i modeli. Do modelu niestandardowego można dodać wszystkie wbudowane intencje domeny i jednostki.

## <a name="prebuilt-domains-per-language"></a>Prebudowane domeny na język

W poniższej tabeli zestawiono aktualnie obsługiwane domeny. Obsługa języka angielskiego jest zwykle większa niż inne.

| Typ jednostki       | PL-US      | ZH-CN   | DE    | PW     | ES    | IT      | PT-BR |  JP  |      KO |        NL |    TR |
|:-----------------:|:-------:|:-------:|:-----:|:------:|:-----:|:-------:| :-------:| :-------:| :-------:| :-------:|  :-------:|
| Kalendarz  | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
|Komunikacja  | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| E-mail     | ✓    | ✓       | ✓   | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| HomeAutomation          | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| Uwagi     | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| Składa   | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| RestaurantReservation  | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| Czynność     | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| Narzędzia      | ✓    | ✓        | ✓    | ✓      | ✓     | ✓       | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| Pogoda        | ✓    | ✓        | ✓    | ✓      | ✓     | ✓       | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| Internet    | ✓    | ✓        | ✓    | ✓      | ✓     | ✓       | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |

Wbudowane domeny nie są **obsługiwane** w programie:

* Francuski (kanadyjski)
* Hindi
* Hiszpański (Meksyk)

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [prostą jednostką](reference-entity-simple.md).