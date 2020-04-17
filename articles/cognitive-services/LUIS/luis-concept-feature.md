---
title: Funkcje — USŁUGA LUIS
titleSuffix: Azure Cognitive Services
description: Dodaj funkcje do modelu języka, aby zapewnić wskazówki dotyczące rozpoznawania danych wejściowych, które chcesz oznaczyć lub sklasyfikować.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: diberry
ms.openlocfilehash: 5b8257e24cf52d01be8065d97db17fd685aa316d
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81531902"
---
# <a name="machine-learned-features"></a>Funkcje maszynowe

W uczeniu maszynowym _funkcja_ jest cechą wyróżniającą lub atrybutem danych, które system obserwuje & uczy się za pośrednictwem. W języku zrozumienia (LUIS) funkcja opisuje i wyjaśnia, co jest istotne dla intencji i jednostek.

W [portalu usługi LUIS w wersji zapoznawczej](https://preview.luis.ai)funkcje są _deskryptorami,_ ponieważ są używane do _opisywania_ intencji lub encji.

## <a name="features-_descriptors_-in-language-understanding"></a>Funkcje _(deskryptory)_ w opisie języka

Funkcje, znany również jako deskryptory, opisują wskazówki, aby ułatwić zrozumienie języka zidentyfikować wypowiedzi przykład. Funkcje obejmują:

* Lista fraz jako funkcja intencji lub encji
* Encje jako funkcje intencji lub encji

Funkcje powinny być traktowane jako niezbędna część schematu dla rozkładu modelu.

## <a name="what-is-a-phrase-list"></a>Co to jest lista fraz

Lista fraz to lista słów, fraz, liczb lub innych znaków, które pomagają zidentyfikować koncepcję, którą próbujesz zidentyfikować. Lista jest niewrażliwa na argumenty.

## <a name="when-to-use-a-phrase-list"></a>Kiedy używać listy fraz

Z listy fraz usługi LUIS uwzględnia kontekst i uogólnia do identyfikowania elementów, które są podobne do, ale nie dokładne dopasowanie tekstu. Jeśli potrzebujesz aplikacji usługi LUIS, aby móc uogólniać i identyfikować nowe elementy, użyj listy fraz.

Jeśli chcesz być w stanie rozpoznać nowe wystąpienia, takie jak harmonogram spotkań, który powinien rozpoznawać nazwy nowych kontaktów lub aplikacja do magazynowania, która powinna rozpoznawać nowe produkty, zacznij od jednostki nauczającej maszyną. Następnie utwórz listę fraz, która pomaga usługi LUIS znaleźć wyrazy o podobnym znaczeniu. Ta lista fraz prowadzi usługę LUIS do rozpoznawania przykładów przez dodanie dodatkowego znaczenia do wartości tych słów.

Listy fraz są podobne do słownictwa specyficznego dla domeny, które pomagają w poprawie jakości zrozumienia intencji i jednostek.

## <a name="considerations-when-using-a-phrase-list"></a>Zagadnienia dotyczące korzystania z listy fraz

Lista fraz jest domyślnie stosowana do wszystkich modeli w aplikacji. Będzie to działać dla list fraz, które mogą przekraczać wszystkie intencje i jednostki. W celu dekompilowania należy zastosować listę fraz tylko do modeli, które są istotne dla.

Jeśli utworzysz listę fraz (utworzoną domyślnie globalnie), a następnie zastosuj ją jako deskryptor (operację) do określonego modelu, zostanie ona usunięta z innych modeli. To usunięcie dodaje znaczenie do listy fraz dla modelu, który jest stosowany do, pomagając poprawić dokładność zapewnia w modelu.

Flaga `enabledForAllModels` steruje tym zakresem modelu w interfejsie API.

<a name="how-to-use-phrase-lists"></a>

### <a name="how-to-use-a-phrase-list"></a>Jak korzystać z listy fraz

[Utwórz listę fraz,](luis-how-to-add-features.md) gdy intencja lub encja zawiera ważne wyrazy lub frazy, takie jak:

* warunki branżowe
* Slang
* Skróty
* język specyficzny dla firmy
* języku pochodzącym z innego języka, ale często używanym w aplikacji
* słowa kluczowe i frazy w wypowiedziach przykładowych

**Nie** dodawaj każdego możliwego słowa lub frazy. Zamiast tego dodaj kilka słów lub fraz naraz, a następnie przekwalifikuj się i opublikuj. Wraz z rozwojem listy w czasie, może się okazać, że niektóre terminy mają wiele form (synonimy). Podziel je na inną listę.

<a name="phrase-lists-help-identify-simple-exchangeable-entities"></a>

## <a name="when-to-use-an-entity-as-a-feature"></a>Kiedy używać encji jako elementu

Jednostki można dodać jako obiekt na poziomie intencji lub jednostki.

### <a name="entity-as-a-feature-to-an-intent"></a>Encja jako funkcja do intencji

Dodaj jednostkę jako deskryptor (operację) do intencji, gdy wykrycie tej jednostki jest istotne dla intencji.

Na przykład jeśli intencją jest rezerwacja lotu, a jednostka jest informacjami o biletach (takimi jak liczba miejsc, pochodzenie i miejsce docelowe), znalezienie jednostki informacji o bilecie powinno zwiększyć wagę do przewidywania intencji lotu książki.

### <a name="entity-as-a-feature-to-another-entity"></a>Jednostka jako element dla innej jednostki

Jednostka (A) powinna zostać dodana jako element do innej jednostki (B), gdy wykrycie tej jednostki (A) jest istotne dla przewidywania jednostki (B).

Na przykład jeśli zostanie wykryta jednostka adresu ulicy (A), znalezienie adresu ulicy (A) zwiększa wagę prognozy dla jednostki adresu wysyłki (B).

## <a name="best-practices"></a>Najlepsze rozwiązania
Poznaj [najlepsze wskazówki](luis-concept-best-practices.md).

## <a name="next-steps"></a>Następne kroki

* [Rozszerzanie](schema-change-prediction-runtime.md) modeli aplikacji w czasie wykonywania prognozowania
* Zobacz [Dodawanie funkcji,](luis-how-to-add-features.md) aby dowiedzieć się więcej o dodaniu funkcji do aplikacji usługi LUIS.
