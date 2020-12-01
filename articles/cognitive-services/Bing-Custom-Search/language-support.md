---
title: Obsługa języka — interfejs API wyszukiwania niestandardowego Bing
titleSuffix: Azure Cognitive Services
description: Lista obsługiwanych języków i regionów dla interfejs API wyszukiwania niestandardowego Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 09/25/2018
ms.author: aahi
ms.openlocfilehash: 73b25ce1093ce697aa0937161d2af49526c231b7
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96352358"
---
# <a name="language-and-region-support-for-the-bing-custom-search-api"></a>Obsługa języka i regionu dla interfejs API wyszukiwania niestandardowego Bing

> [!WARNING]
> Interfejsy API wyszukiwania Bing są przenoszone z Cognitive Services do usług Wyszukiwanie Bing. Od **30 października 2020** wszystkie nowe wystąpienia wyszukiwanie Bing muszą być obsługiwane zgodnie z procesem opisanym [tutaj](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> Interfejsy API wyszukiwania Bing obsługa administracyjna przy użyciu Cognitive Services będzie obsługiwana przez kolejne trzy lata lub do końca Umowa Enterprise, w zależności od tego, co nastąpi wcześniej.
> Instrukcje dotyczące migracji znajdują się w temacie [wyszukiwanie Bing Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Interfejs API wyszukiwania niestandardowego Bing obsługuje więcej niż trzy dziesiątki krajów/regionów, wiele z więcej niż jednym językiem.

Chociaż jest to opcjonalne, żądanie powinno określać parametr zapytania [MKT](/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#mkt) , który identyfikuje rynek, z którego mają pochodzić wyniki. Aby uzyskać listę opcjonalnych parametrów zapytania, zobacz [parametry zapytania](/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#query-parameters)

Możesz określić kraj/region przy użyciu `cc` parametru zapytania. W przypadku określenia kraju/regionu należy również określić jeden lub więcej kodów języka przy użyciu `Accept-Language` nagłówka. Obsługiwane języki różnią się w zależności od kraju/regionu; są one przyznawane dla każdego kraju/regionu w tabeli **rynków** .

`Accept-Language`Nagłówek i `setLang` parametr zapytania wzajemnie się wykluczają — nie należy określać obu tych wartości. Aby uzyskać szczegółowe informacje, zobacz [Accept-Language](/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#acceptlanguage).

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
|Indonezja|ID|
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