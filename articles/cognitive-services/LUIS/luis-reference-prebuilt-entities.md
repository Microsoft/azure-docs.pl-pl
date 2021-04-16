---
title: Wszystkie wstępnie utworzone jednostki — USŁUGA LUIS
titleSuffix: Azure Cognitive Services
description: Ten artykuł zawiera listy wstępnie utworzonych jednostek, które są zawarte w Language Understanding (LUIS).
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 04/13/2021
ms.openlocfilehash: 7155a829655645e13e0485ed7d51305ec50e5b0a
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107502763"
---
# <a name="entities-per-culture-in-your-luis-model"></a>Jednostki na kulturę w modelu usługi LUIS

Language Understanding (LUIS) udostępnia wstępnie utworzone jednostki.

## <a name="entity-resolution"></a>Rozpoznawania jednostek
Po dojściu wstępnie utworzonej jednostki do aplikacji usługa LUIS uwzględnia odpowiednie rozwiązanie jednostki w odpowiedzi punktu końcowego. Wszystkie przykładowe wypowiedzi są również oznaczone etykietą jednostki.

Nie można zmodyfikować zachowania wstępnie utworzonych jednostek, ale można poprawić rozwiązanie, dodając wstępnie spreprecyzowany obiekt jako funkcję do jednostki lub podencji uczenia [maszynowego.](luis-concept-entity-types.md#prebuilt-entity)

## <a name="availability"></a>Dostępność
Jeśli nie określono inaczej, wstępnie utworzone jednostki są dostępne we wszystkich lokalizacjach regionalnych (kulturach) aplikacji usługi LUIS. W poniższej tabeli przedstawiono wstępnie utworzone jednostki, które są obsługiwane dla każdej kultury.

|Kultura|Subkultur|Uwagi|
|--|--|--|
|Chiński|[zh-CN](#chinese-entity-support)||
|Niderlandzki|[nl-NL](#dutch-entity-support)||
|Angielski|[en-US (American)](#english-american-entity-support)||
|Francuski|[fr-CA (Kanada),](#french-canadian-entity-support) [fr-FR (Francja)](#french-france-entity-support), ||
|Niemiecki|[de-DE](#german-entity-support)||
|Włoski|[it-IT](#italian-entity-support)||
|japoński|[ja-JP](#japanese-entity-support)||
|Koreański|[ko-KR](#korean-entity-support)||
|Portugalski|[pt-BR (Brazylia)](#portuguese-brazil-entity-support)||
|Hiszpański|[es-ES (Hiszpania),](#spanish-spain-entity-support) [es-MX (Meksyk)](#spanish-mexico-entity-support)||
|Turecki|[Turecki](#turkish-entity-support)||

## <a name="prediction-endpoint-runtime"></a>Środowisko uruchomieniowe punktu końcowego przewidywania

Dostępność wstępnie utworzonej jednostki w określonym języku zależy od wersji środowiska uruchomieniowego punktu końcowego przewidywania.

## <a name="chinese-entity-support"></a>Obsługa jednostek w języku chińskim

Obsługiwane są następujące jednostki:

| Wstępnie sprecyzowany obiekt | zh-CN |
| --------------- | :---: |
[Wiek:](luis-reference-prebuilt-age.md)<br>rok<br>miesiąc<br>Tygodniu<br>dzień   |    V2, V3   |
[Waluta (pieniądze):](luis-reference-prebuilt-currency.md)<br>Dolar<br>fractional unit (np. pens)  |    V2, V3   |
[DatetimeV2:](luis-reference-prebuilt-datetimev2.md)<br>data<br>daterange<br>time<br>kolejność czasu   |    Wersja 2, 3   |
[Wymiar:](luis-reference-prebuilt-dimension.md)<br>wolumin<br>obszar<br>waga<br>informacje (np. bit/bajt)<br>length (np. miernik)<br>szybkość (np. mila na godzinę)  |    V2, V3   |
[Poczta e-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[Keyphrase](luis-reference-prebuilt-keyphrase.md)   |    -   |
[Liczba](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Liczba porządkowa](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[PorządkoweV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Procent](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[PersonName](luis-reference-prebuilt-person.md)   |    V2, V3   |
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Temperatura:](luis-reference-prebuilt-temperature.md)<br>Fahrenheita<br>Kelvin<br>rankine<br>Delisle<br>Celsjusza   |    V2, V3   |
[Adres URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="dutch-entity-support"></a>Obsługa jednostek holenderskich

Obsługiwane są następujące jednostki:

| Wstępnie sprecyzowany obiekt | nl-NL |
| --------------- | :---: |
[Wiek:](luis-reference-prebuilt-age.md)<br>rok<br>miesiąc<br>Tygodniu<br>dzień   |    Wersja 2, 3   |
[Waluta (pieniądze):](luis-reference-prebuilt-currency.md)<br>Dolar<br>fractional unit (np. pens)  |    Wersja 2, 3   |
[Datetime](luis-reference-prebuilt-deprecated.md)   |    -   |
[Wymiar:](luis-reference-prebuilt-dimension.md)<br>wolumin<br>obszar<br>waga<br>informacje (np. bit/bajt)<br>length (np. meter)<br>szybkość (np. mila na godzinę)  |    V2, V3   |
[Poczta e-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[Keyphrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Liczba](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Liczba porządkowa](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[PorządkoweV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Procent](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[PersonName](luis-reference-prebuilt-person.md)   |    -   |
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Temperatura:](luis-reference-prebuilt-temperature.md)<br>Fahrenheita<br>Kelvin<br>rankine<br>Delisle<br>Celsjusza   |    V2, V3   |
[Adres URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="english-american-entity-support"></a>Obsługa jednostek w języku angielskim (American)

Obsługiwane są następujące jednostki:

| Wstępnie sprecyzowany obiekt | en-US |
| --------------- | :---: |
[Wiek:](luis-reference-prebuilt-age.md)<br>rok<br>miesiąc<br>Tygodniu<br>dzień   |    Wersja 2, 3   |
[Waluta (pieniądze):](luis-reference-prebuilt-currency.md)<br>Dolar<br>fractional unit (np. pens)  |    V2, V3   |
[DatetimeV2:](luis-reference-prebuilt-datetimev2.md)<br>data<br>daterange<br>time<br>timerange   |    V2, V3   |
[Wymiar:](luis-reference-prebuilt-dimension.md)<br>wolumin<br>obszar<br>waga<br>informacje (np. bit/bajt)<br>length (np. miernik)<br>szybkość (np. mila na godzinę)  |    V2, V3   |
[Poczta e-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    V2, V3   |
[Keyphrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Liczba](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Liczba porządkowa](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[PorządkoweV2](luis-reference-prebuilt-ordinal-v2.md)   |    V2, V3   |
[Procent](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[PersonName](luis-reference-prebuilt-person.md)   |    V2, V3   |
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Temperatura:](luis-reference-prebuilt-temperature.md)<br>Fahrenheita<br>Kelvin<br>rankine<br>Delisle<br>Celsjusza   |    V2, V3   |
[Adres URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="french-france-entity-support"></a>Obsługa jednostek francuskich (Francja)

Obsługiwane są następujące jednostki:

| Wstępnie sprecyzowany obiekt | fr-FR |
| --------------- | :---: |
[Wiek:](luis-reference-prebuilt-age.md)<br>rok<br>miesiąc<br>Tygodniu<br>dzień   |    Wersja 2, 3   |
[Waluta (pieniądze):](luis-reference-prebuilt-currency.md)<br>Dolar<br>fractional unit (np. pens)  |    V2, V3   |
[DatetimeV2:](luis-reference-prebuilt-datetimev2.md)<br>data<br>daterange<br>time<br>kolejność czasu   |    Wersja 2, 3   |
[Wymiar:](luis-reference-prebuilt-dimension.md)<br>wolumin<br>obszar<br>waga<br>informacje (np. bit/bajt)<br>length (np. meter)<br>szybkość (np. mila na godzinę)  |    Wersja 2, 3   |
[Poczta e-mail](luis-reference-prebuilt-email.md)   |    Wersja 2, 3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[Keyphrase](luis-reference-prebuilt-keyphrase.md)   |    Wersja 2, 3   |
[Liczba](luis-reference-prebuilt-number.md)   |    Wersja 2, 3   |
[Liczba porządkowa](luis-reference-prebuilt-ordinal.md)   |    Wersja 2, 3   |
[PorządkowaV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Procent](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[PersonName](luis-reference-prebuilt-person.md)   |   -   |
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Temperatura:](luis-reference-prebuilt-temperature.md)<br>Fahrenheita<br>Kelvin<br>rankine<br>Delisle<br>Celsjusza   |    V2, V3   |
[Adres URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="french-canadian-entity-support"></a>Obsługa jednostek francuskich (Kanada)

Obsługiwane są następujące jednostki:

| Wstępnie sprecyzowany obiekt | fr-CA |
| --------------- | :---: |
[Wiek:](luis-reference-prebuilt-age.md)<br>rok<br>miesiąc<br>Tygodniu<br>dzień   |    V2, V3   |
[Waluta (pieniądze)](luis-reference-prebuilt-currency.md):<br>Dolar<br>fractional unit (np. pens)  |    Wersja 2, 3   |
[DatetimeV2:](luis-reference-prebuilt-datetimev2.md)<br>data<br>daterange<br>time<br>kolejność czasu   |    V2, V3   |
[Wymiar:](luis-reference-prebuilt-dimension.md)<br>wolumin<br>obszar<br>waga<br>informacje (np. bit/bajt)<br>length (np. miernik)<br>szybkość (np. mila na godzinę)  |    V2, V3   |
[Poczta e-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[Keyphrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Liczba](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Liczba porządkowa](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Procent](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[PersonName](luis-reference-prebuilt-person.md)   |    -   |
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Temperatura:](luis-reference-prebuilt-temperature.md)<br>Fahrenheita<br>Kelvin<br>rankine<br>Delisle<br>Celsjusza   |    V2, V3   |
[Adres URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="german-entity-support"></a>Obsługa jednostek w języku niemieckim

Obsługiwane są następujące jednostki:

|Wstępnie sprecyzowany obiekt | de-DE |
| -------------- | :---: |
[Wiek:](luis-reference-prebuilt-age.md)<br>rok<br>miesiąc<br>Tygodniu<br>dzień   |    V2, V3   |
[Waluta (pieniądze)](luis-reference-prebuilt-currency.md):<br>Dolar<br>jednostka ułamkowa (np. pens)  |    V2, V3   |
[DatetimeV2:](luis-reference-prebuilt-datetimev2.md)<br>data<br>daterange<br>time<br>timerange   |    V2, V3   |
[Wymiar:](luis-reference-prebuilt-dimension.md)<br>wolumin<br>obszar<br>waga<br>informacje (np. bit/bajt)<br>length (np. miernik)<br>szybkość (np. mila na godzinę)  |    V2, V3   |
[Poczta e-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[Keyphrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Liczba](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Liczba porządkowa](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Procent](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[PersonName](luis-reference-prebuilt-person.md)   |    -   |
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Temperatura:](luis-reference-prebuilt-temperature.md)<br>Fahrenheita<br>Kelvin<br>rankine<br>Delisle<br>Celsjusza   |    V2, V3   |
[Adres URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="italian-entity-support"></a>Obsługa jednostek włoskich

Wstępnie utworzony wiek, waluta, wymiar, liczba, rozdzielczość procentowa włoskich wersji zapoznawczych zostały zmienione z wersji 2 i 3. 

Obsługiwane są następujące jednostki:

| Wstępnie sprecyzowany obiekt | it-IT |
| --------------- | :---: |
[Wiek:](luis-reference-prebuilt-age.md)<br>rok<br>miesiąc<br>Tygodniu<br>dzień   |    V2, V3   |
[Waluta (pieniądze)](luis-reference-prebuilt-currency.md):<br>Dolar<br>jednostka ułamkowa (np. pens)  |    V2, V3   |
[Datetime](luis-reference-prebuilt-deprecated.md)   |    -   |
[Wymiar:](luis-reference-prebuilt-dimension.md)<br>wolumin<br>obszar<br>waga<br>informacje (np. bit/bajt)<br>length (np. meter)<br>szybkość (np. mila na godzinę)  |    Wersja 2, 3   |
[Poczta e-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[Keyphrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Liczba](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Liczba porządkowa](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Procent](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[PersonName](luis-reference-prebuilt-person.md)   |    -   |
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Temperatura:](luis-reference-prebuilt-temperature.md)<br>Fahrenheita<br>Kelvin<br>rankine (ranga)<br>Delisle<br>Celsjusza   |    V2, V3   |
[Adres URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="japanese-entity-support"></a>Obsługa jednostek japońskich

Obsługiwane są następujące jednostki:

|Wstępnie sprecyzowany obiekt | ja-JP |
| -------------- | :---: |
[Wiek:](luis-reference-prebuilt-age.md)<br>rok<br>miesiąc<br>Tygodniu<br>dzień   |    Wersja 2, -   |
[Waluta (pieniądze)](luis-reference-prebuilt-currency.md):<br>Dolar<br>jednostka ułamkowa (np. pens)  |    Wersja 2, -   |
[Datetime](luis-reference-prebuilt-deprecated.md)   |    -   |
[Wymiar:](luis-reference-prebuilt-dimension.md)<br>wolumin<br>obszar<br>waga<br>informacje (np. bit/bajt)<br>length (np. miernik)<br>szybkość (np. mila na godzinę)  |    Wersja 2, -   |
[Poczta e-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[Keyphrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Liczba](luis-reference-prebuilt-number.md)   |    Wersja 2, —   |
[Liczba porządkowa](luis-reference-prebuilt-ordinal.md)   |    Wersja 2, —   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Procent](luis-reference-prebuilt-percentage.md)   |    Wersja 2, —   |
[PersonName (Nazwa Osoby)](luis-reference-prebuilt-person.md)   |    -   |
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Temperatura:](luis-reference-prebuilt-temperature.md)<br>Fahrenheita<br>Kelvin<br>rankine (ranga)<br>Delisle<br>Celsjusza   |    Wersja 2, —   |
[Adres URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="korean-entity-support"></a>Obsługa jednostek koreańskich

Obsługiwane są następujące jednostki:

| Wstępnie sprecyzowany obiekt | ko-KR |
| --------------- | :---: |
[Wiek:](luis-reference-prebuilt-age.md)<br>rok<br>miesiąc<br>Tygodniu<br>dzień   |    -   |
[Waluta (pieniądze)](luis-reference-prebuilt-currency.md):<br>Dolar<br>jednostka ułamkowa (np. pens)  |    -   |
[Datetime](luis-reference-prebuilt-deprecated.md)   |    -   |
[Wymiar:](luis-reference-prebuilt-dimension.md)<br>wolumin<br>obszar<br>waga<br>informacje (np. bit/bajt)<br>length (np. miernik)<br>szybkość (np. mila na godzinę)  |    -   |
[Poczta e-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[Keyphrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Liczba](luis-reference-prebuilt-number.md)   |    -   |
[Liczba porządkowa](luis-reference-prebuilt-ordinal.md)   |    -   |
[PorządkoweV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Procent](luis-reference-prebuilt-percentage.md)   |    -   |
[PersonName](luis-reference-prebuilt-person.md)   |    -   |
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Temperatura:](luis-reference-prebuilt-temperature.md)<br>Fahrenheita<br>Kelvin<br>rankine<br>Delisle<br>Celsjusza   |    -   |
[Adres URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="portuguese-brazil-entity-support"></a>Obsługa jednostek portugalskich (Brazylia)

Obsługiwane są następujące jednostki:

| Wstępnie sprecyzowany obiekt | pt-BR |
| --------------- | :---: |
[Wiek:](luis-reference-prebuilt-age.md)<br>rok<br>miesiąc<br>Tygodniu<br>dzień   |    V2, V3   |
[Waluta (pieniądze):](luis-reference-prebuilt-currency.md)<br>Dolar<br>fractional unit (np. pens)  |    V2, V3   |
[DatetimeV2:](luis-reference-prebuilt-datetimev2.md)<br>data<br>daterange<br>time<br>timerange   |    V2, V3   |
[Wymiar:](luis-reference-prebuilt-dimension.md)<br>wolumin<br>obszar<br>waga<br>informacje (np. bit/bajt)<br>length (np. miernik)<br>szybkość (np. mila na godzinę)  |    V2, V3   |
[Poczta e-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[Keyphrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Liczba](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Liczba porządkowa](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[PorządkoweV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Procent](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[PersonName](luis-reference-prebuilt-person.md)   |    -   |
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Temperatura:](luis-reference-prebuilt-temperature.md)<br>Fahrenheita<br>Kelvin<br>rankine<br>Delisle<br>Celsjusza   |    V2, V3   |
[Adres URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

Fraza KeyPhrase nie jest dostępna we wszystkich elementach podrzędnych języka portugalskiego (Brazylia) — ```pt-BR``` .

## <a name="spanish-spain-entity-support"></a>Obsługa jednostek w języku hiszpańskim (Hiszpania)

Obsługiwane są następujące jednostki:

| Wstępnie sprecyzowany obiekt | es-ES |
| --------------- | :---: |
[Wiek:](luis-reference-prebuilt-age.md)<br>rok<br>miesiąc<br>Tygodniu<br>dzień   |    V2, V3   |
[Waluta (pieniądze):](luis-reference-prebuilt-currency.md)<br>Dolar<br>fractional unit (np. pens)  |    V2, V3   |
[DatetimeV2:](luis-reference-prebuilt-datetimev2.md)<br>data<br>daterange<br>time<br>timerange   |    V2, V3   |
[Wymiar:](luis-reference-prebuilt-dimension.md)<br>wolumin<br>obszar<br>waga<br>informacje (np. bit/bajt)<br>length (np. meter)<br>szybkość (np. mila na godzinę)  |    Wersja 2, 3   |
[Poczta e-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[Keyphrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Liczba](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Liczba porządkowa](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[PorządkoweV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Procent](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[PersonName](luis-reference-prebuilt-person.md)   |    -   |
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Temperatura:](luis-reference-prebuilt-temperature.md)<br>Fahrenheita<br>Kelvin<br>rankine<br>Delisle<br>Celsjusza   |    V2, V3   |
[Adres URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="spanish-mexico-entity-support"></a>Obsługa jednostek w języku hiszpańskim (Meksyk)

Obsługiwane są następujące jednostki:

| Wstępnie sprecyzowany obiekt | es-MX |
| --------------- | :---: |
[Wiek:](luis-reference-prebuilt-age.md)<br>rok<br>miesiąc<br>Tygodniu<br>dzień   |    -   |
[Waluta (pieniądze):](luis-reference-prebuilt-currency.md)<br>Dolar<br>fractional unit (np. pens)  |    -   |
[DatetimeV2:](luis-reference-prebuilt-datetimev2.md)<br>data<br>daterange<br>time<br>timerange   |    -   |
[Wymiar:](luis-reference-prebuilt-dimension.md)<br>wolumin<br>obszar<br>waga<br>informacje (np. bit/bajt)<br>length (np. miernik)<br>szybkość (np. mila na godzinę)  |    -   |
[Poczta e-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[Keyphrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Liczba](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Liczba porządkowa](luis-reference-prebuilt-ordinal.md)   |    -   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Procent](luis-reference-prebuilt-percentage.md)   |    -   |
[PersonName](luis-reference-prebuilt-person.md)   |    -   |
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Temperatura:](luis-reference-prebuilt-temperature.md)<br>Fahrenheita<br>Kelvin<br>rankine<br>Delisle<br>Celsjusza   |    -   |
[Adres URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

Zobacz uwagi dotyczące [przestarzałych wstępnie utworzonych jednostek](luis-reference-prebuilt-deprecated.md)

## <a name="turkish-entity-support"></a>Obsługa jednostek tureckich

| Wstępnie sprecyzowany obiekt | tr-tr |
| --------------- | :---: |
[Wiek:](luis-reference-prebuilt-age.md)<br>rok<br>miesiąc<br>Tygodniu<br>dzień   |    -   |
[Waluta (pieniądze)](luis-reference-prebuilt-currency.md):<br>Dolar<br>jednostka ułamkowa (np. pens)  |    -   |
[DatetimeV2:](luis-reference-prebuilt-datetimev2.md)<br>data<br>daterange<br>time<br>timerange   |    -   |
[Wymiar:](luis-reference-prebuilt-dimension.md)<br>wolumin<br>obszar<br>waga<br>informacje (np. bit/bajt)<br>length (np. miernik)<br>szybkość (np. mila na godzinę)  |    -   |
[Poczta e-mail](luis-reference-prebuilt-email.md)   |    -   |
[Keyphrase](luis-reference-prebuilt-keyphrase.md)   |    -   |
[Liczba](luis-reference-prebuilt-number.md)   |    -   |
[Liczba porządkowa](luis-reference-prebuilt-ordinal.md)   |    -   |
[Procent](luis-reference-prebuilt-percentage.md)   |    -   |
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    -   |
[Temperatura:](luis-reference-prebuilt-temperature.md)<br>Fahrenheita<br>Kelvin<br>rankine (ranga)<br>Delisle<br>Celsjusza   |    -   |
[Adres URL](luis-reference-prebuilt-url.md)   |    -   |

<!---
See notes on [Deprecated prebuilt entities](luis-reference-prebuilt-deprecated.md)
KeyPhrase is not available.
-->

## <a name="contribute-to-prebuilt-entity-cultures"></a>Współtworzenie wstępnie utworzonych kultur jednostek
Wstępnie utworzone jednostki są opracowywane w Recognizers-Text open source. [](https://github.com/Microsoft/Recognizers-Text) Współtwomentuj projekt. Ten projekt zawiera przykłady waluty na kulturę.

Wartości GeographyV2 i PersonName nie są uwzględnione w Recognizers-Text projektu. W przypadku problemów ze wstępnie utworzonymi jednostkami otwórz żądanie [pomocy technicznej](../../azure-portal/supportability/how-to-create-azure-support-request.md).

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej [o jednostkach](luis-reference-prebuilt-number.md)liczbowych , [datetimeV2](luis-reference-prebuilt-datetimev2.md) [i currency.](luis-reference-prebuilt-currency.md)
