---
title: Obsługa języków — interfejs API sprawdzanie pisowni Bing
titleSuffix: Azure Cognitive Services
description: Lista języków naturalnych obsługiwana przez interfejs API sprawdzanie pisowni Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: aahi
ms.openlocfilehash: a97bd6bfc1cb7a0760894faaf3fd0617e6165f58
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96351201"
---
# <a name="language-and-region-support-for-bing-spell-check-api"></a>Obsługa języka i regionu sprawdzanie pisowni Bing API

> [!WARNING]
> Interfejsy API wyszukiwania Bing są przenoszone z Cognitive Services do usług Wyszukiwanie Bing. Od **30 października 2020** wszystkie nowe wystąpienia wyszukiwanie Bing muszą być obsługiwane zgodnie z procesem opisanym [tutaj](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> Interfejsy API wyszukiwania Bing obsługa administracyjna przy użyciu Cognitive Services będzie obsługiwana przez kolejne trzy lata lub do końca Umowa Enterprise, w zależności od tego, co nastąpi wcześniej.
> Instrukcje dotyczące migracji znajdują się w temacie [wyszukiwanie Bing Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Te języki są obsługiwane przez interfejs API sprawdzanie pisowni Bing (tylko w `spell` trybie).

Należy pamiętać, że aby korzystać z dowolnego innego języka niż `en-US` , `mkt` powinien być ustawiony i albo `Accept-Language` `setLang` musi być ustawiony, ponieważ został opisany w [interfejs API sprawdzania pisowni odwołanie wersji 7](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference)

| Język    | Kod języka |
|:----------- |:-------------:|
| Arabski      | `ar`          |
| Chiński (Chińska Republika Ludowa)     | `zh-CN`          |
| Chiński (Hongkong SAR)    | `zh-HK`          |
| Chiński (Tajwan)     | `zh-TW`          |
| Duński      | `da`          |
| Holenderski (Belgia)       | `nl-BE`          |
| Niderlandzki (Holandia)      | `nl-NL`          |
| Angielski (Australia)    | `en-AU`          |
| Angielski (Kanada)     | `en-CA`          |
| Angielski (Indie)    | `en-IN`          |
| Angielski (Indonezja)     | `en-ID`          |
| Angielski (Malezja)     | `en-MY`          |
| Angielski (Nowa Zelandia)    | `en-NZ`          |
| Angielski (Filipiny)     | `en-PH`          |
| Angielski (Republika Południowej Afryki)    | `en-ZA`          |
| Angielski (Zjednoczone Królestwo)    | `en-GB`          |
| Angielski (Stany Zjednoczone)    | `en-US`          |
| Fiński     | `fi`          |
| Francuski (Belgia)     | `fr-BE`          |
| francuski (Kanada)     | `fr-CA`          |
| Francuski (Francja)     | `fr-FR`          |
| Francuski (Szwajcaria)      | `fr-CH`          |
| Niemiecki (Austria)      | `de-AT`          |
| Niemiecki (Niemcy)      | `de-DE`          |
| Niemiecki (Szwajcaria)      | `de-CH`          |
| Włoski     | `it`          |
| japoński    | `ja`          |
| Koreański      | `ko`          |
| Norweski   | `no`          |
| polski      | `pl`          |
| portugalski (Brazylia)   | `pt-BR`|
| Portugalski (Portugalia) | `pt-PT`|
| Rosyjski     | `ru`          |
| Hiszpański (Argentyna)    | `es-AR`          |
| Hiszpański (Chile)     | `es-CL`          |
| Hiszpański (Meksyk)    | `es-MX`          |
| hiszpański (Hiszpania)    | `es-ES`          |
| Hiszpański (Stany Zjednoczone)    | `es-US`          |
| Szwedzki     | `sv`          |
| Turecki     | `tr`          |

## <a name="see-also"></a>Zobacz też

- [Strona dokumentacji usług Cognitive Services](../index.yml)
- [Strona produktu usług Cognitive Services](https://azure.microsoft.com/services/cognitive-services/)