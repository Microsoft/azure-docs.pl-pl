---
title: Projektowanie przy użyciu modeli — LUIS
description: Informacje o języku zawierają kilka typów modeli. Niektóre modele mogą być używane w więcej niż jeden sposób.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/30/2020
ms.openlocfilehash: 576ba945018d13db9cd24888f3c41a2215857694
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91316515"
---
# <a name="design-with-intent-and-entity-models"></a>Projektowanie przy użyciu modeli zamierzeń i jednostek

Zrozumienie języka zapewnia dwa typy modeli służących do definiowania schematu aplikacji. Schemat aplikacji określa, jakie informacje są otrzymywane z przewidywania nowego użytkownika wypowiedź.

Schemat aplikacji jest zbudowany na podstawie modeli tworzonych przy użyciu funkcji [Uczenie maszynowe](#authoring-uses-machine-teaching):
* [Intencje](#intents-classify-utterances) klasyfikowania użytkownika wyrażenia długości
* [Jednostki](#entities-extract-data) wyodrębniają dane z wypowiedź

## <a name="authoring-uses-machine-teaching"></a>Tworzenie przy użyciu nauczania maszynowego

Metodologia nauczania maszynowego LUIS umożliwia łatwe uczenie koncepcji na komputerze. Zrozumienie _uczenia maszynowego_ nie jest konieczne do korzystania z Luis. Zamiast tego jesteś autorem, komunikuje koncepcję z LUIS, dostarczając przykłady koncepcji i wyjaśniając, w jaki sposób koncepcji należy modelować przy użyciu innych powiązanych koncepcji. Jak nauczyciel, można także interaktywnie ulepszyć model LUIS, identyfikując i rozwiązując błędy przewidywania.

<a name="v3-authoring-model-decomposition"></a>

## <a name="intents-classify-utterances"></a>Intencje — klasyfikowanie wypowiedzi

Celem jest klasyfikowanie przykładu wyrażenia długości w celu uczenia LUIS na temat zamiaru. Przykład wyrażenia długości w ramach intencji jest używany jako pozytywne przykłady wypowiedź. Te same wyrażenia długości są używane jako przykłady negatywne we wszystkich innych intencjach.

Weź pod uwagę aplikację, która wymaga określenia zamiaru użytkownika, aby zamówić książkę i aplikację, która wymaga adresu wysyłkowego dla klienta. Ta aplikacja ma dwa intencje: `OrderBook` i `ShippingLocation` .

Następujący wypowiedź to **pozytywny przykład** dla `OrderBook` zamiaru i **negatywny przykład** dla `ShippingLocation` i `None` intencji:

`Buy the top-rated book on bot architecture.`

## <a name="entities-extract-data"></a>Jednostki — wyodrębnianie danych

Jednostka reprezentuje jednostkę danych, które mają zostać wyodrębnione z wypowiedź. Jednostka uczenia maszynowego jest jednostką najwyższego poziomu zawierającą podjednostki, które są również jednostkami uczenia maszynowego.

Przykładem jednostki uczenia maszynowego jest zamówienie dla biletu płaszczyzny. Koncepcyjnie jest to pojedyncza transakcja z wieloma mniejszymi jednostkami danych, takimi jak data, godzina, liczba siedzeń, typ stanowiska, taki jak pierwsza klasa lub autokar, lokalizacja pochodzenia, lokalizacja docelowa i wybór posiłku.

## <a name="intents-versus-entities"></a>Intencje i jednostki

Celem jest pożądany wynik _całości_ wypowiedź, podczas gdy jednostki są częścią danych wyodrębnionych z wypowiedź. Zazwyczaj intencje są powiązane z akcjami, które powinny być wykonywane przez aplikację kliencką. Jednostki są informacjami wymaganymi do wykonania tej akcji. Z perspektywy programowania zamiar wywoła wywołanie metody, a jednostki byłyby używane jako parametry dla tego wywołania metody.

Ta wypowiedź _musi_ mieć cel i _może_ mieć jednostki:

`Buy an airline ticket from Seattle to Cairo`

Ten wypowiedź ma jeden zamiar:

* Kupowanie biletu płaszczyzny

Ten wypowiedź _może_ mieć kilka jednostek:

* Lokalizacje Seattle (Origin) i Kair (miejsce docelowe)
* Ilość pojedynczego biletu

## <a name="entity-model-decomposition"></a>Dekompozycja modelu jednostki

LUIS obsługuje _dekompozycję modelu_ przy użyciu interfejsów API tworzenia, dzieląc koncepcję na mniejsze części. Pozwala to na kompilowanie modeli bez obaw w sposobie konstruowania i przewidywania różnych części.

Dekompozycja modelu ma następujące części:

* [intencji](#intents-classify-utterances)
    * [oferowanych](#features)
* [jednostki uczenia maszynowego](reference-entity-machine-learned-entity.md)
    * podjednostki (również jednostki uczenia maszynowego)
        * [oferowanych](#features)
            * [Lista fraz](luis-concept-feature.md)
            * [jednostki niezwiązane z uczeniem maszynowym](luis-concept-feature.md) , takie jak [wyrażenia regularne](reference-entity-regular-expression.md), [listy](reference-entity-list.md)i [wstępnie zbudowane jednostki](luis-reference-prebuilt-entities.md)

<a name="entities-extract-data"></a>
<a name="machine-learned-entities"></a>

## <a name="features"></a>Funkcje

[Funkcja](luis-concept-feature.md) jest odróżnianą cechą lub atrybutem danych, które są przestrzegane przez system. Funkcje uczenia maszynowego zapewniają LUIS ważne wskaźniki dotyczące lokalizacji, w których należy odróżnić koncepcje. Są to wskazówki, których LUIS mogą używać, ale nie są trudne. Te wskazówki są używane w połączeniu z etykietami w celu znalezienia danych.

## <a name="patterns"></a>Wzorce

[Wzorce](luis-concept-patterns.md) zostały zaprojektowane w celu poprawy dokładności, gdy kilka wyrażenia długości jest bardzo podobne. Wzorzec umożliwia uzyskanie większej dokładności dla zamiaru bez udostępniania wielu wyrażenia długości.

## <a name="extending-the-app-at-runtime"></a>Rozszerzanie aplikacji w czasie wykonywania

Schemat aplikacji (modele i funkcje) jest szkolony i publikowany w punkcie końcowym przewidywania. Można [przekazać nowe informacje](schema-change-prediction-runtime.md)wraz z wypowiedź użytkownika do punktu końcowego przewidywania, aby rozszerzyć prognozę.

## <a name="next-steps"></a>Następne kroki

* Zrozumienie [intencji](luis-concept-intent.md) i [jednostek](luis-concept-entity-types.md).
* Dowiedz się więcej o [funkcjach](luis-concept-feature.md)