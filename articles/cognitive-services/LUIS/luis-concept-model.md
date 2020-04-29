---
title: Projektowanie przy użyciu modeli — LUIS
titleSuffix: Azure Cognitive Services
description: Informacje o języku zawierają kilka typów modeli. Niektóre modele mogą być używane w więcej niż jeden sposób.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: d721ceb25b3ce2408563a0bed16457d05affe7b4
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "79219996"
---
# <a name="design-with-intent-and-entity-models"></a>Projektowanie przy użyciu modeli zamierzeń i jednostek 

Informacje o języku zawierają kilka typów modeli. Niektóre modele mogą być używane w więcej niż jeden sposób. 

## <a name="v3-authoring-uses-machine-teaching"></a>Tworzenie wersji v3 przy użyciu nauczania maszynowego

LUIS umożliwia użytkownikom łatwą naukę koncepcji na komputerze. Komputer może następnie tworzyć modele (zbliżające się koncepcje, takie jak klasyfikatory i wyodrębniania), które mogą być używane do zarządzania inteligentnymi aplikacjami. Mimo że usługa LUIS jest obsługiwana przez uczenie maszynowe, zrozumienie uczenia maszynowego nie jest konieczne do korzystania z niego. Zamiast tego nauczyciele maszyn komunikują się z pojęciami do LUIS, pokazując pozytywne i negatywne przykłady koncepcji i wyjaśniając, w jaki sposób koncepcji należy modelować przy użyciu innych powiązanych koncepcji. Nauczyciele mogą także interaktywnie ulepszyć model LUIS, identyfikując i ustalając błędy przewidywania. 

## <a name="v3-authoring-model-decomposition"></a>Dekompozycja modelu tworzenia w wersji 3

LUIS obsługuje _dekompozycję modelu_ przy użyciu interfejsów API tworzenia v3, dzieląc model na mniejsze części. Pozwala to na kompilowanie modeli bez obaw w sposobie konstruowania i przewidywania różnych części.

Dekompozycja modelu ma następujące części:

* [intencji](#intents-classify-utterances)
    * [deskryptory](#descriptors-are-features) udostępniane przez funkcje
* [jednostki poznanie maszyn](#machine-learned-entities)
    * [podskładniki](#entity-subcomponents-help-extract-data) (również jednostki, które są wyuczyne maszynowo)
        * [deskryptory](#descriptors-are-features) udostępniane przez funkcje 
        * [ograniczenia](#constraints-are-text-rules) zapewniane przez jednostki, które nie są obsługiwane przez maszynę, takie jak wyrażenia regularne i listy

## <a name="v2-authoring-models"></a>Modele tworzenia w wersji 2

LUIS obsługuje jednostki złożone z interfejsami API tworzenia w wersji 2. Zapewnia to podobną dekompozycję modelu, ale nie jest taka sama jak dekompozycja modelu v3. Zalecaną architekturą modelu jest przejście do dekompozycji modelu w interfejsach API tworzenia v3. 

## <a name="intents-classify-utterances"></a>Klasyfikacja wyrażenia długości

Celem jest klasyfikowanie przykładu wyrażenia długości w celu uczenia LUIS na temat zamiaru. Przykład wyrażenia długości w ramach intencji jest używany jako pozytywne przykłady wypowiedź. Te same wyrażenia długości są używane jako przykłady negatywne we wszystkich innych intencjach.

Weź pod uwagę aplikację, która wymaga określenia zamiaru użytkownika, aby zamówić książkę i aplikację, która wymaga adresu wysyłkowego dla klienta. Ta aplikacja ma dwa intencje: `OrderBook` i. `ShippingLocation`

Następujący wypowiedź to **pozytywny przykład** dla `OrderBook` zamiaru i **negatywny przykład** dla `ShippingLocation` i `None` intencji: 

`Buy the top-rated book on bot architecture.`

Wyniki zaprojektowanych założeń, z których przykładem jest wyrażenia długości, jest przewidywanie wysokiej klasy. 

## <a name="entities-extract-data"></a>Jednostki wyodrębniające dane

Jednostka reprezentuje jednostkę danych, które mają zostać wyodrębnione z wypowiedź. 

### <a name="machine-learned-entities"></a>Jednostki poznanie maszyn

Jednostka uczenia maszynowego jest jednostką najwyższego poziomu zawierającą podskładniki, które są również obiektami maszynowymi. 

**Użyj jednostki o Poznaniu maszynowego**:

* gdy podskładniki są wymagane przez aplikację kliencką
* Aby ułatwić rozdzielanie jednostek przez algorytm uczenia maszynowego

Każdy podskładnik może mieć:

* podskładniki zwalniają
* ograniczenia (jednostka wyrażenia regularnego lub jednostka listy)
* deskryptory (funkcje, takie jak lista fraz) 

Przykładem jednostki uczenia maszynowego jest zamówienie dla biletu płaszczyzny. Koncepcyjnie jest to pojedyncza transakcja z wieloma mniejszymi jednostkami danych, takimi jak data, godzina, liczba siedzeń, typ stanowiska, taki jak pierwsza klasa lub autokar, lokalizacja pochodzenia, lokalizacja docelowa i wybór posiłku.


### <a name="entity-subcomponents-help-extract-data"></a>Podskładniki jednostki pomagają wyodrębnić dane

Składnik podrzędny jest jednostką podrzędną dodaną przez maszynę w ramach jednostki nadrzędnej, która jest poznania maszyny. 

**Użyj podskładniku, aby**:

* Rozłóż części jednostki, która jest poznania maszynowego (Jednostka nadrzędna).

Poniżej przedstawiono jednostkę uczenia maszynowego, która obejmuje wszystkie te oddzielne fragmenty danych:

* TravelOrder (jednostka zadana maszynowo)
    * DateTime (prebudowany datetimeV2)
    * Lokalizacja (jednostka pouczenia maszynowego)
        * Źródło (rola znaleziona w kontekście `from`)
        * Miejsce docelowe (rola znaleziona w kontekście `to`)
    * Siedzisko (jednostka pouczenia maszynowego)
        * Ilość (prebudowany numer)
        * Jakość (jednostka poznania maszyn z deskryptorem listy fraz)
    * Posiłki (jednostka poznania maszynowego z ograniczeniami jednostki listy jako opcje żywności)

Niektóre z tych danych, takie jak lokalizacja pochodzenia i lokalizacja docelowa, należy poznać z kontekstu wypowiedź, na przykład takie sformułowanie jak `from` i. `to` Inne części danych można wyodrębnić z dokładnymi dopasowaniami ciągu (`Vegan`) lub wstępnie skompilowanymi jednostkami ( `Seattle` geographyV2 `Cairo`z i). 

Projektujesz, w jaki sposób dane są dopasowywane i wyodrębniane przez wybrane modele oraz sposób ich konfigurowania.

### <a name="constraints-are-text-rules"></a>Ograniczenia są regułami dotyczącymi tekstu

Ograniczenie jest regułą dopasowania tekstu udostępnianą przez jednostkę niezgodną z maszyną, taką jak jednostka wyrażenia regularnego lub jednostki listy. Ograniczenie jest stosowane w czasie przewidywania, aby ograniczyć prognozowanie i zapewnić rozpoznawanie jednostek wymagane przez aplikację kliencką. Te reguły definiuje się podczas tworzenia podskładnika. 

**Użyj ograniczenia**:
* poznanie dokładnego tekstu do wyodrębnienia.

Ograniczenia obejmują:

* jednostki [wyrażenia regularnego](reference-entity-regular-expression.md)
* [Lista](reference-entity-list.md) jednostek 
* [wstępnie utworzone](luis-reference-prebuilt-entities.md) jednostki

Kontynuując przykład biletu płaszczyzny, kody portów lotniczych mogą znajdować się w jednostce listy w celu dokładnego dopasowania tekstu. 

W przypadku listy portów lotniczych wpis listy dla Seattle jest nazwą miasta, `Seattle` a synonimy dla Seattle obejmują kod lotniska dla Seattle oraz otaczające miasta i miejscowości:

|`Seattle`Wyświetl synonimy jednostek|
|--|
|`Sea`|
|`seatac`|
|`Bellevue`|

Jeśli chcesz rozpoznawać tylko kody 3 liter dla kodów portów lotniczych, użyj wyrażenia regularnego jako ograniczenia. 

`/^[A-Z]{3}$/`

## <a name="intents-versus-entities"></a>Intencje i jednostki

Celem jest pożądany wynik _całości_ wypowiedź, podczas gdy jednostki są częścią danych wyodrębnionych z wypowiedź. Zazwyczaj intencje są powiązane z akcjami, które aplikacja kliencka powinna wykonać, a jednostki są informacjami wymaganymi do wykonania tej akcji. Z perspektywy programowania zamiar wywoła wywołanie metody, a jednostki byłyby używane jako parametry dla tego wywołania metody.

Ta wypowiedź _musi_ mieć cel i _może_ mieć jednostki:

`Buy an airline ticket from Seattle to Cairo`

Ten wypowiedź ma jeden zamiar:

* Kupowanie biletu płaszczyzny

Ten wypowiedź _może_ mieć kilka jednostek:

* Lokalizacje Seattle (Origin) i Kair (miejsce docelowe)
* Ilość pojedynczego biletu

## <a name="descriptors-are-features"></a>Deskryptory są funkcjami

Deskryptor jest funkcją zastosowana do modelu w czasie uczenia, w tym listy zwrotów i jednostek. 

**Użyj deskryptora, gdy chcesz**:

* Zwiększ znaczenie słów i fraz identyfikowanych przez deskryptor
* LUIS zaleca nowym tekstom lub frazom zalecanym dla deskryptora
* Naprawianie błędu dotyczącego danych szkoleniowych

## <a name="next-steps"></a>Następne kroki

* Zrozumienie [intencji](luis-concept-intent.md) i [jednostek](luis-concept-entity-types.md). 