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
ms.openlocfilehash: d15058126f43fff328acfc563ffd081164a69a90
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "79220194"
---
# <a name="language-and-region-support-for-the-bing-news-search-api"></a>Obsługa języka i regionu dla interfejs API wyszukiwania wiadomości Bing

Interfejs API wyszukiwania wiadomości Bing obsługuje wiele krajów/regionów, wiele z więcej niż jednym językiem. Określenie kraju/regionu z zapytaniem służy głównie do uściślenia wyników wyszukiwania w oparciu o zainteresowania w tym kraju/regionie. Ponadto wyniki mogą zawierać linki do usługi Bing, a te linki mogą lokalizować środowisko użytkownika Bing w zależności od określonego kraju/regionu lub języka.

Możesz określić kraj/region przy użyciu parametru `cc` zapytania. W przypadku określenia kraju/regionu należy również określić jeden lub więcej kodów języka przy użyciu nagłówka `Accept-Language` http. Obsługiwane języki różnią się w zależności od liczby/regionu; są one przyznawane dla każdego kraju/regionu w tabeli rynków.

Alternatywnie można określić rynek przy użyciu parametru `mkt` zapytania i kodu z tabeli **rynków** . Określenie rynku jednocześnie określa kraj/region i preferowany język. W `setLang` tym przypadku parametr zapytania może być ustawiony na kod języka; zwykle jest to ten sam język określony przez `mkt` użytkownika, chyba że użytkownik woli zobaczyć Bing w innym języku.

## <a name="supported-markets-for-news-search-endpoint"></a>Obsługiwane rynki dla punktu końcowego wyszukiwania wiadomości

`/news/search` W poniższej tabeli przedstawiono wartości kodów rynkowych, za pomocą których można określić parametr `mkt` zapytania. Bing zwraca zawartość tylko dla tych rynków. Lista może ulec zmianie.  

Aby uzyskać listę kodów krajów/regionów, które można określić w parametrze `cc` zapytania, zobacz [Kody krajów](#countrycodes).  

|Kraj/region|Język|Kod rynkowy|  
|---------------------|--------------|-----------------|
|Dania|duński|da-DK|
|Austria|niemiecki|de-AT|
|Szwajcaria|niemiecki|Usuń CH|
|Niemcy|niemiecki|de-DE|
|Australia|Angielski|en-AU|
|Kanada|Angielski|EN-CA|
|Wielka Brytania|Angielski|en-GB|
|Indonezja|Angielski|pl-ID|
|Irlandia|Angielski|EN-IE|
|Indie|Angielski|dodatek EN-IN|
|Malezja|Angielski|pl — MY|
|Nowa Zelandia|Angielski|EN-NZ|
|Republika Filipin|Angielski|EN-PH|
|Singapur|Angielski|EN-SG|
|Stany Zjednoczone|Angielski|pl-PL|
|Angielski|Ogólne|pl-WW|
|Angielski|Ogólne|pl-XA|
|Republika Południowej Afryki|Angielski|pl-za|
|Argentyna|Hiszpański|ES-AR|
|Chile|Hiszpański|ES — CL|
|Hiszpania|Hiszpański|es-ES|
|Meksyk|Hiszpański|es — MX|
|Stany Zjednoczone|Hiszpański|es — US|
|Hiszpański|Ogólne|ES — XL|
|Finlandia|fiński|fi-FI|  
|Francja|Francuski|fr — należy|
|Kanada|Francuski|fr — CA|
|Belgia|Niderlandzki|NL-to|
|Szwajcaria|Francuski|fr-CH|
|Francja|Francuski|fr-FR|  
|Włochy|Włoski|it-IT|
|SRA Hongkong|Chiński tradycyjny|zh-HK|  
|Tajwan|Chiński tradycyjny|zh-TW|
|Japonia|japoński|ja-JP|  
|Korea|koreański|ko-KR|  
|Holandia|Niderlandzki|nl-NL|  
|Chińska Republika Ludowa|Chiński|zh-CN|  
|Brazylia|Portugalski|pt-BR|
|Rosja|Rosyjski|ru-RU|  
|Szwecja|szwedzki|sv-SE|  
|Turcja|Turecki|tr-TR|  

## <a name="supported-markets-for-news-endpoint"></a>Obsługiwane rynki dla punktu końcowego aktualności News
`/news` W poniższej tabeli przedstawiono wartości kodów rynkowych, za pomocą których można określić parametr `mkt` zapytania. Bing zwraca zawartość tylko dla tych rynków. Lista może ulec zmianie.  

Aby uzyskać listę kodów krajów/regionów, które można określić w parametrze `cc` zapytania, zobacz [Kody krajów](#countrycodes).  

|Kraj/region|Język|Kod rynkowy|  
|---------------------|--------------|-----------------|
|Dania|duński|da-DK|
|Niemcy|niemiecki|de-DE|
|Australia|Angielski|en-AU|
|Wielka Brytania|Angielski|en-GB|
|Stany Zjednoczone|Angielski|pl-PL|
|Angielski|Ogólne|pl-WW|
|Chile|Hiszpański|ES — CL|
|Meksyk|Hiszpański|es — MX|
|Stany Zjednoczone|Hiszpański|es — US|
|Finlandia|fiński|fi-FI|  
|Kanada|Francuski|fr — CA|
|Francja|Francuski|fr-FR|  
|Włochy|Włoski|it-IT|
|Brazylia|Portugalski|pt-BR|
|Chińska Republika Ludowa|Chiński|zh-CN|

## <a name="supported-markets-for-news-trending-endpoint"></a>Obsługiwane rynki dla punktu końcowego trendów Aktualności
`/news/trendingtopics` W poniższej tabeli przedstawiono wartości kodów rynkowych, za pomocą których można określić parametr `mkt` zapytania. Bing zwraca zawartość tylko dla tych rynków. Lista może ulec zmianie.  

Aby uzyskać listę kodów krajów/regionów, które można określić w parametrze `cc` zapytania, zobacz [Kody krajów](#countrycodes).  

|Kraj/region|Język|Kod rynkowy|  
|---------------------|--------------|-----------------|
|Niemcy|niemiecki|de-DE|
|Australia|Angielski|en-AU|
|Wielka Brytania|Angielski|en-GB|
|Stany Zjednoczone|Angielski|pl-PL|
|Kanada|Angielski|EN-CA|
|Indie|Angielski|dodatek EN-IN|
|Francja|Francuski|fr-FR|
|Kanada|Francuski|fr — CA|
|Brazylia|Portugalski|pt-BR|
|Chińska Republika Ludowa|Chiński|zh-CN|


<a name="countrycodes"></a>   
### <a name="country-codes"></a>Kody krajów  

Poniżej znajdują się kody kraju/regionu, które można określić w parametrze `cc` zapytania. Lista może ulec zmianie.  

|Kraj/region|Kod kraju|  
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
|Indonezja|ID|  
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
|Wielka Brytania|GB|  
|Stany Zjednoczone|USA|

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji o wyszukiwanie wiadomości Bing punktach końcowych, zobacz [Wyszukiwanie wiadomości API wersji 7 Reference](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference).
