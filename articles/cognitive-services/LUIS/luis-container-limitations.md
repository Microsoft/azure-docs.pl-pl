---
title: Ograniczenia kontenera — LUIS
titleSuffix: Azure Cognitive Services
description: Obsługiwane języki kontenera LUIS.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 5c76e45f09a0f432d2775eb19d3dcaa668294e6f
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96002351"
---
# <a name="language-understanding-luis-container-limitations"></a>Ograniczenia dotyczące kontenerów Language Understanding (LUIS)

Kontenery LUIS mają kilka istotnych ograniczeń. W przypadku nieobsługiwanych zależności, w tym artykule szczegółowo opisano te ograniczenia.

## <a name="supported-dependencies-for-latest-container"></a>Obsługiwane zależności dla `latest` kontenera

Najnowszy kontener LUIS obsługuje:

* [Nowe prebudowane domeny](luis-reference-prebuilt-domains.md): te domeny ukierunkowane na przedsiębiorstwa obejmują jednostki, przykładowe wyrażenia długości i wzorce. Rozwiń te domeny do własnych potrzeb.

## <a name="unsupported-dependencies-for-latest-container"></a>Nieobsługiwane zależności dla `latest` kontenera

Aby [wyeksportować kontener](luis-container-howto.md#export-packaged-app-from-luis), należy usunąć nieobsługiwane zależności z aplikacji Luis. Podczas próby eksportowania dla kontenera Portal LUIS zgłasza te Nieobsługiwane funkcje, które należy usunąć.

Aplikacji LUIS można użyć, jeśli **nie zawiera** ona żadnych z następujących zależności:

Nieobsługiwane konfiguracje aplikacji|Szczegóły|
|--|--|
|Nieobsługiwane kultury kontenerów| Języki holenderskie ( `nl-NL` ), Japońskie ( `ja-JP` ) i niemiecki ( `de-DE` ) są obsługiwane tylko w [1.0.2 tokenizatora](luis-language-support.md#custom-tokenizer-versions).|
|Nieobsługiwane jednostki dla wszystkich kultur|Wstępnie utworzona jednostka [KeyPhrase](luis-reference-prebuilt-keyphrase.md) dla wszystkich kultur|
|Nieobsługiwane jednostki dla kultury angielskiej ( `en-US` )|Wstępnie skompilowane jednostki [GeographyV2](luis-reference-prebuilt-geographyV2.md)|
|Napełnianiu mowy|Zależności zewnętrzne nie są obsługiwane w kontenerze.|
|Analiza tonacji|Zależności zewnętrzne nie są obsługiwane w kontenerze.|
|Sprawdzanie pisowni Bing|Zależności zewnętrzne nie są obsługiwane w kontenerze.|

## <a name="languages-supported"></a>Obsługiwane języki

Kontenery LUIS obsługują podzbiór [języków obsługiwanych](luis-language-support.md#languages-supported) przez Luis. Kontenery LUIS mogą zrozumieć wyrażenia długości w następujących językach:

| Język | Regionalne | Preskompilowana domena | Wstępnie utworzona jednostka | Zalecenia dotyczące listy fraz | **[Analiza tekstu](../text-analytics/language-support.md)<br>(Tonacji i<br>Służąc|
|--|--|:--:|:--:|:--:|:--:|
| Angielski (Stany Zjednoczone) | `en-US` | ✔️ | ✔️ | ✔️ | ✔️ |
| Arabski (wersja zapoznawcza — nowoczesny Standard arabski) |`ar-AR`|❌|❌|❌|❌|
| *[Chiński](#chinese-support-notes) |`zh-CN` | ✔️ | ✔️ | ✔️ | ❌ |
| Francuski (Francja) |`fr-FR` | ✔️ | ✔️ | ✔️ | ✔️ |
| francuski (Kanada) |`fr-CA` | ❌ | ❌ | ❌ | ✔️ |
| Niemiecki |`de-DE` | ✔️ | ✔️ | ✔️ | ✔️ |
| Hindi | `hi-IN`| ❌ | ❌ | ❌ | ❌ |
| Włoski |`it-IT` | ✔️ | ✔️ | ✔️ | ✔️ |
| Koreański |`ko-KR` | ✔️ | ❌ | ❌ | Tylko *fraza klucza* |
| Marathi | `mr-IN`|❌|❌|❌|❌|
| Portugalski (Brazylia) |`pt-BR` | ✔️ | ✔️ | ✔️ | nie wszystkie hodowle podrzędne |
| hiszpański (Hiszpania) |`es-ES` | ✔️ | ✔️ |✔️|✔️|
| Hiszpański (Meksyk)|`es-MX` | ❌ | ❌ |✔️|✔️|
| Tamilski | `ta-IN`|❌|❌|❌|❌|
| Telugu | `te-IN`|❌|❌|❌|❌|
| Turecki | `tr-TR` |✔️| ❌ | ❌ | Tylko *tonacji* |

[!INCLUDE [Chinese language support notes](includes/chinese-language-support-notes.md)]

[!INCLUDE [Text Analytics support notes](includes/text-analytics-support-notes.md)]
