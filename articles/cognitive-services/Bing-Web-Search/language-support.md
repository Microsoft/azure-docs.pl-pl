---
title: Obsługa języka — interfejs API wyszukiwania w sieci Web Bing
titleSuffix: Azure Cognitive Services
description: Lista języków naturalnych, krajów i regionów, które są obsługiwane przez interfejs API wyszukiwania w sieci Web Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: aahi
ms.openlocfilehash: b3ce1fdc0b52db2706f81950fff735cf7f95e52b
ms.sourcegitcommit: 8a1ba1ebc76635b643b6634cc64e137f74a1e4da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/09/2020
ms.locfileid: "94379684"
---
# <a name="language-and-region-support-for-the-bing-web-search-api"></a>Obsługa języka i regionu dla interfejs API wyszukiwania w sieci Web Bing

> [!WARNING]
> Interfejsy API wyszukiwania Bing są przenoszone z Cognitive Services do usług Wyszukiwanie Bing. Od **30 października 2020** wszystkie nowe wystąpienia wyszukiwanie Bing muszą być obsługiwane zgodnie z procesem opisanym [tutaj](https://aka.ms/cogsvcs/bingmove).
> Interfejsy API wyszukiwania Bing obsługa administracyjna przy użyciu Cognitive Services będzie obsługiwana przez kolejne trzy lata lub do końca Umowa Enterprise, w zależności od tego, co nastąpi wcześniej.
> Instrukcje dotyczące migracji znajdują się w temacie [wyszukiwanie Bing Services](https://aka.ms/cogsvcs/bingmigration).

Interfejs API wyszukiwania w sieci Web Bing obsługuje ponad trzy dziesiątki krajów lub regionów, wiele z więcej niż jednym językiem. Określenie kraju lub regionu z zapytaniem ułatwia dokładne wyniki wyszukiwania w zależności od danego kraju lub regionu zainteresowania. Wyniki mogą zawierać linki do usługi Bing, a te linki mogą lokalizować środowisko użytkownika Bing w zależności od określonego kraju/regionu lub języka.

Możesz określić kraj lub region przy użyciu `cc` parametru zapytania. W przypadku określenia kraju lub regionu należy określić jeden lub więcej kodów języka z [ `Accept-Language` nagłówkiem](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#headers). Skorzystaj z [tabeli rynków](#markets) , aby zapoznać się z listą języków obsługiwanych na poszczególnych rynkach.

Alternatywnie można określić rynek z `mkt` parametrem zapytania i kodem z tabeli **rynków** . Określenie rynku jednocześnie określa kraj lub region i preferowany język. Można jawnie ustawić język przy użyciu `setLang` parametru zapytania.

## <a name="countriesregions"></a>Kraje/regiony

|Kraj/region|Kod|
|-------|----|
|Argentyna|AR|
|Australia|AU|
|Austria|AT|
|Belgia|BE|
|Brazylia|BR|
|Kanada|CA|
|Chile|CL|
|Dania|DK|
|Finlandia|FI|
|Francja|PW|
|Niemcy|DE|
|SRA Hongkong|HK|
|Indie|IN|
|Indonezja|ID (Identyfikator)|
|Włochy|IT|
|Japonia|JP|
|Korea|KR|
|Malezja|MY|
|Meksyk|MX|
|Holandia|NL|
|Nowa Zelandia|NZ|
|Norwegia|NO|
|Chiny|CN|
|Polska|PL|
|Portugalia|PT|
|Filipiny|PH|
|Rosja|RU|
|Arabia Saudyjska|SA|
|Republika Południowej Afryki|ZA|
|Hiszpania|ES|
|Szwecja|SE|
|Szwajcaria|CH|
|Tajwan|TW|
|Turcja|TR|
|Zjednoczone Królestwo|GB|
|Stany Zjednoczone|USA|

## <a name="markets"></a>Wprowadza

|Kraj/region|Język|Kod rynkowy|
|-------|--------|-----------|
|Argentyna|Hiszpański|ES-AR|
|Australia|Angielski|en-AU|
|Austria|Niemiecki|de-AT|
|Belgia|Niderlandzki|NL-to|
|Belgia|Francuski|fr — należy|
|Brazylia|Portugalski|pt-BR|
|Kanada|Angielski|EN-CA|
|Kanada|Francuski|fr — CA|
|Chile|Hiszpański|ES — CL|
|Dania|Duński|da-DK|
|Finlandia|Fiński|fi-FI|
|Francja|Francuski|fr-FR|
|Niemcy|Niemiecki|de-DE|
|SRA Hongkong|Chiński tradycyjny|zh-HK|
|Indie|Angielski|dodatek EN-IN|
|Indonezja|Angielski|pl-ID|
|Włochy|Włoski|it-IT|
|Japonia|japoński|ja-JP|
|Korea|Koreański|ko-KR|
|Malezja|Angielski|pl — MY|
|Meksyk|Hiszpański|es — MX|
|Holandia|Niderlandzki|nl-NL|
|Nowa Zelandia|Angielski|EN-NZ|
|Norwegia|Norweski|nie — nie|
|Chiny|Chiński|zh-CN|
|Polska|Polski|pl-PL|
|Portugalia|Portugalski|pt-PT|
|Filipiny|Angielski|EN-PH|
|Rosja|Rosyjski|ru-RU|
|Arabia Saudyjska|Arabski|ar-SA|
|Republika Południowej Afryki|Angielski|pl-za|
|Hiszpania|Hiszpański|es-ES|
|Szwecja|Szwedzki|sv-SE|
|Szwajcaria|Francuski|fr-CH|
|Szwajcaria|Niemiecki|Usuń CH|
|Tajwan|Chiński tradycyjny|zh-TW|
|Turcja|Turecki|tr-TR|
|Zjednoczone Królestwo|Angielski|en-GB|
|Stany Zjednoczone|Angielski|en-US|
|Stany Zjednoczone|Hiszpański|es — US|

## <a name="next-steps"></a>Następne kroki

* [Dokumentacja interfejsu API wyszukiwania obrazów Bing](//docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)