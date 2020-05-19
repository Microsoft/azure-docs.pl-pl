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
ms.openlocfilehash: e50dd636c1bd9403d5eca2157e4e46772418f92c
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83592903"
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
|Nieobsługiwane kultury kontenerów| Holenderski ( `nl-NL` )<br>Japoński ( `ja-JP` )<br>Język niemiecki jest obsługiwany tylko w przypadku [1.0.2 tokenizatora](luis-language-support.md#custom-tokenizer-versions).|
|Nieobsługiwane jednostki dla wszystkich kultur|Wstępnie utworzona jednostka [KeyPhrase](luis-reference-prebuilt-keyphrase.md) dla wszystkich kultur|
|Nieobsługiwane jednostki dla kultury angielskiej ( `en-US` )|Wstępnie skompilowane jednostki [GeographyV2](luis-reference-prebuilt-geographyV2.md)|
|Napełnianiu mowy|Zależności zewnętrzne nie są obsługiwane w kontenerze.|
|Analiza tonacji|Zależności zewnętrzne nie są obsługiwane w kontenerze.|
|Sprawdzanie pisowni Bing|Zależności zewnętrzne nie są obsługiwane w kontenerze.|

## <a name="languages-supported"></a>Obsługiwane języki

Kontenery LUIS obsługują podzbiór [języków obsługiwanych](luis-language-support.md#languages-supported) przez Luis. Kontenery LUIS mogą zrozumieć wyrażenia długości w następujących językach:

| Język | Regionalne | Preskompilowana domena | Wstępnie utworzona jednostka | Zalecenia dotyczące listy fraz | **[Analiza tekstu](../text-analytics/language-support.md)<br>(Tonacji i<br>Służąc|
|--|--|:--:|:--:|:--:|:--:|
| Angielski (amerykański) | `en-US` | ✔️ | ✔️ | ✔️ | ✔️ |
| *[Chiński](#chinese-support-notes) |`zh-CN` | ✔️ | ✔️ | ✔️ | ❌ |
| Francuski (Francja) |`fr-FR` | ✔️ | ✔️ | ✔️ | ✔️ |
| francuski (Kanada) |`fr-CA` | ❌ | ❌ | ❌ | ✔️ |
| Niemiecki |`de-DE` | ✔️ | ✔️ | ✔️ | ✔️ |
| Hindi | `hi-IN`| ❌ | ❌ | ❌ | ❌ |
| Włoski |`it-IT` | ✔️ | ✔️ | ✔️ | ✔️ |
| Koreański |`ko-KR` | ✔️ | ❌ | ❌ | Tylko *fraza klucza* |
| Portugalski (Brazylia) |`pt-BR` | ✔️ | ✔️ | ✔️ | nie wszystkie hodowle podrzędne |
| hiszpański (Hiszpania) |`es-ES` | ✔️ | ✔️ |✔️|✔️|
| Hiszpański (Meksyk)|`es-MX` | ❌ | ❌ |✔️|✔️|
| Turecki | `tr-TR` |✔️| ❌ | ❌ | Tylko *tonacji* |

[!INCLUDE [Chinese language support notes](includes/chinese-language-support-notes.md)]

[!INCLUDE [Text Analytics support notes](includes/text-analytics-support-notes.md)]