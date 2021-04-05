---
title: Obsługa języka — interfejs API wyszukiwania wiadomości Bing
titleSuffix: Azure Cognitive Services
description: Lista języków naturalnych, krajów i regionów, które są obsługiwane przez interfejs API wyszukiwania wiadomości Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: conceptual
ms.date: 1/10/2019
ms.author: aahi
ms.openlocfilehash: 9c7fd03c2239cea05dc79ad4dd1965fe253a2ce9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "96341601"
---
# <a name="language-and-region-support-for-the-bing-news-search-api"></a>Obsługa języka i regionu dla interfejs API wyszukiwania wiadomości Bing

> [!WARNING]
> Interfejsy API wyszukiwania Bing są przenoszone z Cognitive Services do usług Wyszukiwanie Bing. Od **30 października 2020** wszystkie nowe wystąpienia wyszukiwanie Bing muszą być obsługiwane zgodnie z procesem opisanym [tutaj](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> Interfejsy API wyszukiwania Bing obsługa administracyjna przy użyciu Cognitive Services będzie obsługiwana przez kolejne trzy lata lub do końca Enterprise Agreement, w zależności od tego, co nastąpi wcześniej.
> Instrukcje dotyczące migracji znajdują się w temacie [wyszukiwanie Bing Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Interfejs API wyszukiwania wiadomości Bing obsługuje wiele krajów/regionów, wiele z więcej niż jednym językiem. Określenie kraju/regionu z zapytaniem służy głównie do uściślenia wyników wyszukiwania w oparciu o zainteresowania w tym kraju/regionie. Ponadto wyniki mogą zawierać linki do usługi Bing, a te linki mogą lokalizować środowisko użytkownika Bing w zależności od określonego kraju/regionu lub języka.

Możesz określić kraj/region przy użyciu `cc` parametru zapytania. W przypadku określenia kraju/regionu należy również określić jeden lub więcej kodów języka przy użyciu `Accept-Language` nagłówka HTTP. Obsługiwane języki różnią się w zależności od liczby/regionu; są one przyznawane dla każdego kraju/regionu w tabeli rynków.

Alternatywnie można określić rynek przy użyciu `mkt` parametru zapytania i kodu z tabeli **rynków** . Określenie rynku jednocześnie określa kraj/region i preferowany język. `setLang`Parametr zapytania może być ustawiony na kod języka w tym przypadku; zazwyczaj jest to ten sam język określony przez użytkownika, `mkt` chyba że użytkownik woli zobaczyć Bing w innym języku.

## <a name="supported-markets-for-news-search-endpoint"></a>Obsługiwane rynki dla punktu końcowego wyszukiwania wiadomości

W `/news/search` poniższej tabeli przedstawiono wartości kodów rynkowych, za pomocą których można określić `mkt` parametr zapytania. Bing zwraca zawartość tylko dla tych rynków. Lista może ulec zmianie.  

Aby uzyskać listę kodów krajów/regionów, które można określić w `cc` parametrze zapytania, zobacz [Kody krajów](#countrycodes).  

|Kraj/region|Język|Kod rynkowy|  
|---------------------|--------------|-----------------|
|Dania|Duński|da-DK|
|Austria|Niemiecki|de-AT|
|Szwajcaria|Niemiecki|Usuń CH|
|Niemcy|Niemiecki|de-DE|
|Australia|Angielski|en-AU|
|Kanada|Angielski|EN-CA|
|Zjednoczone Królestwo|Angielski|en-GB|
|Indonezja|Angielski|pl-ID|
|Irlandia|Angielski|EN-IE|
|Indie|Angielski|dodatek EN-IN|
|Malezja|Angielski|pl — MY|
|Nowa Zelandia|Angielski|EN-NZ|
|Republika Filipin|Angielski|EN-PH|
|Singapur|Angielski|EN-SG|
|Stany Zjednoczone|Angielski|en-US|
|Angielski|ogólne|pl-WW|
|Angielski|ogólne|pl-XA|
|Republika Południowej Afryki|Angielski|pl-za|
|Argentyna|Hiszpański|ES-AR|
|Chile|Hiszpański|ES — CL|
|Hiszpania|Hiszpański|es-ES|
|Meksyk|Hiszpański|es — MX|
|Stany Zjednoczone|Hiszpański|es — US|
|Hiszpański|ogólne|ES — XL|
|Finlandia|Fiński|fi-FI|  
|Francja|Francuski|fr — należy|
|Kanada|Francuski|fr — CA|
|Belgia|Niderlandzki|NL-to|
|Szwajcaria|Francuski|fr-CH|
|Francja|Francuski|fr-FR|  
|Włochy|Włoski|it-IT|
|SRA Hongkong|Chiński tradycyjny|zh-HK|  
|Tajwan|Chiński tradycyjny|zh-TW|
|Japonia|japoński|ja-JP|  
|Korea|Koreański|ko-KR|  
|Holandia|Niderlandzki|nl-NL|  
|Chińska Republika Ludowa|Chiński|zh-CN|  
|Brazylia|Portugalski|pt-BR|
|Rosja|Rosyjski|ru-RU|  
|Szwecja|Szwedzki|sv-SE|  
|Turcja|Turecki|tr-TR|  

## <a name="supported-markets-for-news-endpoint"></a>Obsługiwane rynki dla punktu końcowego aktualności News
W `/news` poniższej tabeli przedstawiono wartości kodów rynkowych, za pomocą których można określić `mkt` parametr zapytania. Bing zwraca zawartość tylko dla tych rynków. Lista może ulec zmianie.  

Aby uzyskać listę kodów krajów/regionów, które można określić w `cc` parametrze zapytania, zobacz [Kody krajów](#countrycodes).  

|Kraj/region|Język|Kod rynkowy|  
|---------------------|--------------|-----------------|
|Dania|Duński|da-DK|
|Niemcy|Niemiecki|de-DE|
|Australia|Angielski|en-AU|
|Zjednoczone Królestwo|Angielski|en-GB|
|Stany Zjednoczone|Angielski|en-US|
|Angielski|ogólne|pl-WW|
|Chile|Hiszpański|ES — CL|
|Meksyk|Hiszpański|es — MX|
|Stany Zjednoczone|Hiszpański|es — US|
|Finlandia|Fiński|fi-FI|  
|Kanada|Francuski|fr — CA|
|Francja|Francuski|fr-FR|  
|Włochy|Włoski|it-IT|
|Brazylia|Portugalski|pt-BR|
|Chińska Republika Ludowa|Chiński|zh-CN|

## <a name="supported-markets-for-news-trending-endpoint"></a>Obsługiwane rynki dla punktu końcowego trendów Aktualności
W `/news/trendingtopics` poniższej tabeli przedstawiono wartości kodów rynkowych, za pomocą których można określić `mkt` parametr zapytania. Bing zwraca zawartość tylko dla tych rynków. Lista może ulec zmianie.  

Aby uzyskać listę kodów krajów/regionów, które można określić w `cc` parametrze zapytania, zobacz [Kody krajów](#countrycodes).  

|Kraj/region|Język|Kod rynkowy|  
|---------------------|--------------|-----------------|
|Niemcy|Niemiecki|de-DE|
|Australia|Angielski|en-AU|
|Zjednoczone Królestwo|Angielski|en-GB|
|Stany Zjednoczone|Angielski|en-US|
|Kanada|Angielski|EN-CA|
|Indie|Angielski|dodatek EN-IN|
|Francja|Francuski|fr-FR|
|Kanada|Francuski|fr — CA|
|Brazylia|Portugalski|pt-BR|
|Chińska Republika Ludowa|Chiński|zh-CN|


<a name="countrycodes"></a>   
### <a name="country-codes"></a>Kody krajów  

Poniżej znajdują się kody kraju/regionu, które można określić w `cc` parametrze zapytania. Lista może ulec zmianie.  

|Kraj/region|Numer kierunkowy kraju|  
|---------------------|------------------|  
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
|Chińska Republika Ludowa|CN|  
|Polska|PL|  
|Portugalia|PT|  
|Republika Filipin|PH|  
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

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji o wyszukiwanie wiadomości Bing punktach końcowych, zobacz [Wyszukiwanie wiadomości API wersji 7 Reference](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference).