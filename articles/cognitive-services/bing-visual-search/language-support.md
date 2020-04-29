---
title: Obsługa języka — interfejs API wyszukiwania wizualnego Bing
titleSuffix: Azure Cognitive Services
description: Lista języków naturalnych, krajów i regionów, które są obsługiwane przez interfejs API wyszukiwania wizualnego Bing. Interfejs API wyszukiwania wizualnego Bing obsługuje więcej niż trzy dziesiątki krajów/regionów, wiele z więcej niż jednym językiem.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: conceptual
ms.date: 09/25/2018
ms.author: scottwhi
ms.openlocfilehash: b17341bc234ff3dfecc2c6dcd84ef77116a95d61
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "68883544"
---
# <a name="language-and-region-support-for-the-bing-visual-search-api"></a>Obsługa języka i regionu dla interfejs API wyszukiwania wizualnego Bing

Interfejs API wyszukiwania wizualnego Bing obsługuje więcej niż trzy dziesiątki krajów/regionów, wiele z więcej niż jednym językiem. Każde żądanie powinno zawierać kraj/region i język wyboru użytkownika. Wiedząc, że rynek użytkownika pomaga w usłudze Bing zwracać odpowiednie wyniki. Jeśli nie określisz kraju/regionu i języka, Bing najlepiej nadaje się do ustalenia kraju/regionu i języka użytkownika. Ze względu na to, że wyniki mogą zawierać linki do usługi Bing, wiedząc, że kraj/region i język mogą zapewnić preferowany, zlokalizowany interfejs użytkownika Bing, jeśli użytkownik kliknie linki Bing.

Aby określić kraj/region i język, ustaw parametr zapytania `mkt` (rynek) na kod z tabeli **rynków** poniżej. Rynek określa kraj/region i język. Jeśli użytkownik woli zobaczyć tekst wyświetlany w innym języku, należy ustawić `setLang` parametr zapytania na odpowiedni kod języka.

Alternatywnie możesz określić kraj/region przy użyciu parametru `cc` zapytania. W przypadku określenia kraju/regionu należy również określić jeden lub więcej kodów języka przy użyciu nagłówka `Accept-Language` http. Obsługiwane języki różnią się w zależności od kraju/regionu; są one przyznawane dla każdego kraju w tabeli rynków.



> [!NOTE]
> Obowiązują następujące ograniczenia dotyczące rynku:
>
> - Adnotacje rozpoznawania obrazów są dostępne tylko w języku angielskim.
> - Przepis, zakupy i strony — w tym szczegółowe informacje są dostępne tylko na rynku en-US.


## <a name="countriesregions"></a>Kraje/regiony

|Kraj/region|Code|
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
|Wielka Brytania|GB|
|Stany Zjednoczone|USA|


## <a name="markets"></a>Wprowadza

|Kraj/region|Język|Kod rynkowy|
|-------|--------|-----------|
|Argentyna|Hiszpański|ES-AR|
|Australia|Angielski|en-AU|
|Austria|niemiecki|de-AT|
|Belgia|Niderlandzki|NL-to|
|Belgia|Francuski|fr — należy|
|Brazylia|Portugalski|pt-BR|
|Kanada|Angielski|EN-CA|
|Kanada|Francuski|fr — CA|
|Chile|Hiszpański|ES — CL|
|Dania|duński|da-DK|
|Finlandia|fiński|fi-FI|
|Francja|Francuski|fr-FR|
|Niemcy|niemiecki|de-DE|
|SRA Hongkong|Chiński tradycyjny|zh-HK|
|Indie|Angielski|dodatek EN-IN|
|Indonezja|Angielski|pl-ID|
|Włochy|Włoski|it-IT|
|Japonia|japoński|ja-JP|
|Korea|koreański|ko-KR|
|Malezja|Angielski|pl — MY|
|Meksyk|Hiszpański|es — MX|
|Holandia|Niderlandzki|nl-NL|
|Nowa Zelandia|Angielski|EN-NZ|
|Chiny|Chiński|zh-CN|
|Polska|Polski|pl-PL|
|Portugalia|Portugalski|pt-PT|
|Filipiny|Angielski|EN-PH|
|Rosja|Rosyjski|ru-RU|
|Arabia Saudyjska|Arabski|ar-SA|
|Republika Południowej Afryki|Angielski|pl-za|
|Hiszpania|Hiszpański|es-ES|
|Szwecja|szwedzki|sv-SE|
|Szwajcaria|Francuski|fr-CH|
|Szwajcaria|niemiecki|Usuń CH|
|Tajwan|Chiński tradycyjny|zh-TW|
|Turcja|Turecki|tr-TR|
|Wielka Brytania|Angielski|en-GB|
|Stany Zjednoczone|Angielski|pl-PL|
|Stany Zjednoczone|Hiszpański|es — US|
