---
title: Obsługa lokalizacji | Mapy Microsoft Azure
description: Ten artykuł zawiera informacje na temat obsługiwanych języków dla usług w Microsoft Azure Maps.
author: anastasia-ms
ms.author: v-stharr
ms.date: 11/20/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 0d3adc4bc49379a9ec3408ab76b913a096840dbb
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/24/2020
ms.locfileid: "87127897"
---
# <a name="localization-support-in-azure-maps"></a>Obsługa lokalizacji w Azure Maps

Azure Maps obsługuje różne języki i widoki na podstawie kraju/regionu. W tym artykule przedstawiono obsługiwane języki i widoki ułatwiające implementację Azure Maps.


## <a name="azure-maps-supported-languages"></a>Obsługiwane języki Azure Maps

Azure Maps zostały zlokalizowane w różnych językach w ramach usług. W poniższej tabeli przedstawiono obsługiwane kody języka dla każdej usługi.  
  

| ID         | Nazwa                   |  Maps | Wyszukaj | Routing | Pogoda | Zdarzenia dotyczące ruchu | Formant mapy JS |
|------------|------------------------|:-----:|:------:|:-------:|:--------:|:-----------------:|:--------------:|
| AF — za      | Afrikaans              |       |    ✓   |    ✓    |         |                   |                |
| ar-SA      | Arabski                 |   ✓   |    ✓   |    ✓    |    ✓      |         ✓         |        ✓       |
| mld USD — BD      | Bengalski (Bangladesz)    |       |       |         |     ✓    |                   |                |
| mld USD      | Bengalski (Indie)         |       |       |         |     ✓    |                   |                |
| BS — BA      | Bośniacki                 |       |       |         |     ✓    |                   |                |
| EU-ES      | Baskijski                 |       |    ✓   |         |         |                   |                |
| bg-BG      | Bułgarski              |   ✓   |    ✓   |    ✓    |     ✓     |                   |        ✓       |
| ca-ES      | Kataloński                |       |    ✓   |         |    ✓      |                   |                |
| zh-HanS    | Chiński (uproszczony)   |       |  zh-CN |         |     zh-CN   |                   |                |
| zh-HanT    | Chiński (Hongkong SAR)  |  |   |    |    zh-HK   |                   |           |
| zh-HanT    | Chiński (Tajwan)  | zh-TW |  zh-TW |  zh-TW  |    zh-TW   |                   |      zh-TW     |
| hr-HR      | Chorwacki               |       |    ✓   |         |    ✓      |                   |                |
| cs-CZ      | Czeski                  |   ✓   |    ✓   |    ✓    |    ✓      |         ✓         |        ✓       |
| da-DK      | Duński                 |   ✓   |    ✓   |    ✓    |     ✓     |         ✓         |        ✓       |
| NL-to      | Holenderski (Belgia)        |       |    ✓   |         |      ✓    |                   |                |
| nl-NL      | Niderlandzki (Holandia)    |   ✓   |    ✓   |    ✓    |     ✓     |         ✓         |        ✓       |
| en-AU      | Angielski (Australia)    |   ✓   |    ✓   |    ✓    |     ✓     |         ✓         |        ✓       |
| EN-NZ      | Angielski (Nowa Zelandia)  |   ✓   |    ✓   |    ✓    |     ✓     |         ✓         |        ✓       |
| en-GB      | Angielski (Wielka Brytania) |   ✓   |    ✓   |    ✓    |     ✓     |         ✓         |        ✓       |
| pl-PL      | Angielski (USA)          |   ✓   |    ✓   |    ✓    |      ✓    |         ✓         |        ✓       |
| et-EE      | Estoński               |       |    ✓   |         |      ✓    |         ✓         |                |
| PLI — PH     | Filipino               |       |       |         |     ✓    |                   |                |
| fi-FI      | Fiński                |   ✓   |    ✓   |    ✓    |      ✓    |         ✓         |        ✓       |
| fr-FR      | Francuski                 |   ✓   |    ✓   |    ✓    |      ✓    |         ✓         |        ✓       |
| fr — CA      | francuski (Kanada)      |       |    ✓   |         |     ✓     |                   |                |
| gl-ES      | Galicyjski               |       |    ✓   |         |         |                   |                |
| de-DE      | Niemiecki                 |   ✓   |    ✓   |    ✓    |   ✓      |         ✓         |        ✓       |
| el-GR      | Grecki                  |   ✓   |    ✓   |    ✓    |    ✓     |         ✓         |        ✓       |
| gu      | Gudżarati                |       |       |         |     ✓    |                   |                |
| he-IL      | Hebrajski                 |       |    ✓   |         |     ✓    |         ✓         |                |
| hi-IN      | Hindi                  |       |        |         |     ✓    |                   |                |
| hu-HU      | Węgierski              |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| IS-IS      | Islandzki              |       |       |         |     ✓    |                   |                |
| id-ID      | Indonezyjski             |   ✓   |    ✓    |    ✓    |     ✓    |         ✓         |        ✓       |
| it-IT      | Włoski                |   ✓   |    ✓   |    ✓    |      ✓   |         ✓         |        ✓       |
| ja-JP      | Japoński               |       |        |         |     ✓    |                   |                |
| kN-IN      | Kannada                |       |       |         |     ✓    |                   |                |
| kk-KZ      | Kazachski                 |       |    ✓   |         |     ✓    |                   |                |
| ko-KR      | Koreański                 |   ✓   |        |    ✓    |     ✓    |                   |        ✓       |
| ES — 419     | Łaciński (Ameryka Łacińska) |       |    ✓   |         |         |                   |                |
| lv-LV      | Łotewski                |       |    ✓   |         |     ✓    |         ✓         |                |
| lt-LT      | Litewski             |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| mk — MK      | Macedonii             |       |       |         |     ✓    |                   |                |
| ms-MY      | Malajski (łaciński)          |   ✓   |    ✓   |    ✓    |    ✓   |                   |        ✓       |
| Mr      | Marathi                 |       |       |         |     ✓    |                   |                |
| nb-NO      | Norweski (bokmål)       |   ✓   |    ✓   |    ✓    |      ✓   |         ✓         |        ✓       |
| NGT        | Neutralne Języki w języku prawdy dla wszystkich regionów w lokalnych skryptach, jeśli są dostępne |   ✓     |        |         |       |        |      ✓          |
| NGT — Latn   | Neutralna obudowa prawdy-łacińskiego exonyms. Skrypt łaciński zostanie użyty, jeśli jest dostępny |   ✓     |        |         |         |                |        ✓         |
| pl-PL      | Polski                 |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| pt-BR      | Portugalski (Brazylia)    |   ✓   |    ✓   |    ✓    |      ✓   |                   |        ✓       |
| pt-PT      | Portugalski (Portugalia)  |   ✓   |    ✓   |    ✓    |      ✓   |         ✓         |        ✓       |
| PA-IN      | Pendżabski                 |       |       |         |     ✓    |                   |                |
| ro-RO      | Rumuński               |       |    ✓    |         |     ✓    |         ✓         |                |
| ru-RU      | Rosyjski                |   ✓   |    ✓   |    ✓    |      ✓   |         ✓         |        ✓       |
| sr-Cyrl-RS | Serbski (cyrylica)     |       |   Wirtualizacja SR-RS  |         |    Wirtualizacja SR-RS     |                   |                |
| sr-Latn-RS | Serbski (łaciński)        |       |       |         |     Wirtualizacja sr-latn    |                   |                |
| sk-SK      | Słowacki             |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| SL — SL      | Słoweński              |   ✓   |    ✓   |    ✓    |     ✓    |                   |        ✓       |
| es-ES      | Hiszpański                |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| es — MX      | Hiszpański (Meksyk)       |   ✓   |        |    ✓    |     ✓    |                   |        ✓       |
| sv-SE      | Szwedzki                |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| Ta — w      | Tamilski (Indie)                 |       |       |         |     ✓    |                   |                |
| te — IN      | Telugu (Indie)                 |       |       |         |     ✓    |                   |                |
| th-TH      | Tajlandzki                   |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| tr-TR      | Turecki                |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| uk-UA      | Ukraiński               |       |    ✓   |         |     ✓    |                   |                |
| Twoje — klucz podstawowy      | Urdu                 |       |       |         |     ✓    |                   |                |
| uz-Latn-UZ | Uzbecki                 |       |       |         |     ✓    |                   |                |
| vi-VN      | Wietnamski             |       |    ✓   |         |      ✓    |                  |                |


## <a name="azure-maps-supported-views"></a>Azure Maps obsługiwane widoki

> [!Note]
> 1 sierpnia 2019 Azure Maps zostało wydane w następujących krajach/regionach:
>  * Argentyna
>  * Indie
>  * Maroko
>  * Pakistan
>
> Po 1 sierpnia 2019 parametr **widoku** określi zawartość zamapowanej mapy dla nowych regionów/krajów wymienionych powyżej. Parametr **widoku** Azure Maps (określany również jako "parametr regionu użytkownika") to dwuliterowy kod kraju ISO-3166, który będzie pokazywał poprawne mapy dla danego kraju/regionu, określając, który zestaw geopoznawczych treści jest zwracany przez usługi Azure Maps, w tym obramowania i etykiety wyświetlane na mapie. 

Upewnij się, że parametr **View** jest skonfigurowany jako wymagany dla interfejsów API REST i zestawów SDK, których usługi używają.
>  
>
>  **Interfejsy API REST:**
>  
>  Upewnij się, że parametr View został skonfigurowany zgodnie z wymaganiami. Parametr widoku Określa, który zestaw zawartości o treści geopolitycznej jest zwracany przez Azure Maps usług. 
>
>  Azure Maps usługi REST:
>    
>    * Pobierz kafelek mapy
>    * Pobierz obraz mapy 
>    * Pobierz rozmyte wyszukiwanie
>    * Pobierz punkt POI wyszukiwania
>    * Pobierz kategorię wyszukiwania punkt POI
>    * Rozpocznij wyszukiwanie w pobliżu
>    * Pobierz adres wyszukiwania
>    * Pobierz strukturę adresu wyszukiwania
>    * Uzyskaj zwrotny adres wyszukiwania
>    * Pobierz adres wyszukiwania odwrotnie
>    * Opublikuj wyszukiwanie w geometrii
>    * Zapoznawcza partia adresów wyszukiwania
>    * Adres wyszukiwania zwrotnego — wersja zapoznawcza
>    * Publikuj wyszukiwanie wzdłuż trasy
>    * Przeszukiwanie rozmytej partii w wersji zapoznawczej
>
>    
>  **Zestawy**
>
>  Upewnij się, że skonfigurowano parametr **View** zgodnie z wymaganiami i masz najnowszą wersję zestawu Web SDK i Android SDK. Uwzględnione zestawy SDK:
>
>    * Azure Maps Web SDK
>    * Azure Maps Android SDK

Domyślnie parametr widoku jest ustawiany na **zunifikowany**, nawet jeśli nie został zdefiniowany w żądaniu. Określ lokalizację użytkowników. Następnie ustaw parametr **widoku** poprawnie dla tej lokalizacji. Alternatywnie można ustawić wartość "View = Auto", która zwróci dane mapy na podstawie adresu IP żądania.  Parametr **widoku** w Azure Maps musi być używany zgodnie z obowiązującymi przepisami, łącznie z tymi prawami dotyczącymi mapowania kraju/regionu, w którym mapy, obrazy i inne dane oraz zawartość innych firm, do których masz uprawnienia dostępu za pośrednictwem Azure Maps są udostępniane.


W poniższej tabeli przedstawiono obsługiwane widoki.

| Widok         | Opis                            |  Maps | Wyszukaj | kontrolka mapy JS |
|--------------|----------------------------------------|:-----:|:------:|:--------------:|
| AE           | Zjednoczone Emiraty Arabskie (widok arabski)    |   ✓   |        |     ✓          |
| AR           | Argentyna (widok argentyński)           |   ✓   |    ✓   |     ✓          |
| BH           | Bahrajn (widok arabski)                 |   ✓   |        |     ✓          |
| IN           | Indie (widok Indyjski)                    |   ✓   |   ✓     |     ✓          |
| IQ           | Irak (widok arabski)                    |   ✓   |        |     ✓          |
| JO           | Jordania (widok arabski)                  |   ✓   |        |     ✓          |
| KW           | Kuwejt (widok arabski)                  |   ✓   |        |     ✓          |
| LB           | Liban (widok arabski)                 |   ✓   |        |     ✓          |
| MA           | Maroko (widok marokański)                |   ✓   |   ✓     |     ✓          |
| OM           | Oman (widok arabski)                    |   ✓   |        |     ✓          |
| PK           | Pakistan (widok pakistański)              |   ✓   |    ✓    |     ✓          |
| PS           | Autonomia Palestyńska (widok arabski)    |   ✓   |        |     ✓          |
| QA           | Katar (widok arabski)                   |   ✓   |        |     ✓          |
| SA           | Arabia Saudyjska (widok arabski)            |   ✓   |        |     ✓          |
| SY           | Syria (widok arabski)                   |   ✓   |        |     ✓          |
| YE           | Jemen (widok arabski)                   |   ✓   |        |     ✓          |
| Auto         | Zwróć dane mapy na podstawie adresu IP żądania.|   ✓   |    ✓   |     ✓          |
| Ujednolicon      | Ujednolicony widok (inne)                  |   ✓   |   ✓     |     ✓          |
