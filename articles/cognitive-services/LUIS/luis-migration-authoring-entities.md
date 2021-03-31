---
title: Migrowanie do jednostki uczenia maszynowego v3
description: Program v3 Authoring zawiera jeden nowy typ jednostki, jednostkę uczenia maszynowego oraz możliwość dodawania relacji do jednostki uczenia maszynowego i innych obiektów lub funkcji aplikacji.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 05/08/2020
ms.openlocfilehash: 667226770d25ef1687420b1c13bc71863f987e33
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "91324692"
---
# <a name="migrate-to-v3-authoring-entity"></a>Migrowanie do jednostki tworzenia w wersji 3

Program v3 Authoring zawiera jeden nowy typ jednostki, jednostkę uczenia maszynowego oraz możliwość dodawania relacji do jednostki uczenia maszynowego i innych obiektów lub funkcji aplikacji.

## <a name="entities-are-decomposable-in-v3"></a>Jednostki są roztworzące w wersji 3

Jednostki utworzone przy użyciu interfejsów API tworzenia v3, przy użyciu [interfejsów API](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview) lub portalu, umożliwiają tworzenie modelu jednostki warstwowej z elementem nadrzędnym i podrzędnym. Element nadrzędny jest nazywany **jednostką uczenia maszynowego** i elementami podrzędnymi, które są nazywane **podjednostkami** jednostki, której dotyczy dana maszyna.

Każda podjednostka jest również jednostką uczenia maszynowego, ale z dodaniem opcji konfiguracji funkcji.

* **Funkcje wymagane** są regułami, które gwarantują, że jednostka jest wyodrębniana, gdy jest zgodna z funkcją. Reguła jest definiowana przez wymaganą funkcję do modelu:
    * [Wstępnie utworzona jednostka](luis-reference-prebuilt-entities.md)
    * [Jednostka wyrażenia regularnego](reference-entity-regular-expression.md)
    * [Jednostki listy](reference-entity-list.md).

## <a name="how-do-these-new-relationships-compare-to-v2-authoring"></a>Jak te nowe relacje są porównywane z wersjami w wersji 2

Wersja 2 Autorstwo udostępnia hierarchiczne i złożone jednostki wraz z rolami i funkcjami do wykonania tego samego zadania. Ponieważ jednostki, funkcje i role nie zostały jawnie powiązane ze sobą, trudno zrozumieć, w jaki sposób usługa LUIS określa relacje podczas przewidywania.

W przypadku wersji 3 relacja jest jawna i zaprojektowana przez autorów aplikacji. Dzięki temu autor aplikacji może:

* Wizualnie Zobacz, jak LUIS przewiduje te relacje, w przykładzie wyrażenia długości
* Przetestuj te relacje przy użyciu [interaktywnego okienka testów](luis-interactive-test.md) lub w punkcie końcowym
* Użyj tych relacji w aplikacji klienckiej za pośrednictwem dobrze uporządkowanego, nazwanego, zagnieżdżonego [obiektu JSON](reference-entity-machine-learned-entity.md)

## <a name="planning"></a>Planowanie

Podczas migracji należy wziąć pod uwagę następujące kwestie w planie migracji:

* Wykonaj kopię zapasową aplikacji LUIS i Przeprowadź migrację do oddzielnej aplikacji. Gdy aplikacja w wersji 2 i V3 jest dostępna w tym samym czasie, umożliwia zweryfikowanie wymaganych zmian i wpływ na wyniki przewidywania.
* Przechwyć bieżące metryki sukcesu przewidywania
* Przechwyć bieżące informacje pulpitu nawigacyjnego jako migawkę stanu aplikacji
* Przeglądanie istniejących intencji, jednostek, list fraz, wzorców i testów wsadowych
* Następujące elementy można migrować **bez zmiany**:
    * Intencje
    * Jednostki
        * Jednostka wyrażenia regularnego
        * Jednostka listy
    * Funkcje
        * Lista fraz
* Następujące elementy muszą zostać zmigrowane **ze zmianami**:
    * Jednostki
        * Jednostka hierarchiczna
        * Jednostka złożona
    * Role — role można stosować tylko do jednostki uczenia maszynowego (nadrzędnego). Nie można zastosować ról do podjednostek
    * Testy wsadowe i wzorce używające jednostek hierarchicznych i złożonych

Podczas projektowania planu migracji należy pozostawić czas, aby przejrzeć końcowe jednostki uczenia maszynowego po przeprowadzeniu migracji wszystkich jednostek hierarchicznych i złożonych. Podczas gdy migracja prosta będzie działała, po dokonaniu zmiany i przejrzeniu wyników testów partii i kodzie JSON przewidywania bardziej ujednolicony kod JSON może prowadzić do wprowadzenia zmian, dzięki czemu ostateczne informacje dostarczane do aplikacji po stronie klienta są zorganizowane inaczej. Jest to podobne do refaktoryzacji kodu i powinna być traktowana przy użyciu tego samego procesu przeglądu, który jest używany w organizacji.

Jeśli nie masz testów wsadowych dla modelu w wersji 2 i Przeprowadź migrację testów wsadowych do modelu V3 w ramach migracji, nie będziesz w stanie sprawdzić, w jaki sposób migracja będzie miała wpływ na wyniki prognozowania punktów końcowych.

## <a name="migrating-from-v2-entities"></a>Migrowanie z jednostek v2

Po rozpoczęciu pracy z modelem tworzenia wersji v3 należy rozważyć sposób przejścia do jednostki uczenia maszynowego oraz jej podjednostek i funkcji.

W poniższej tabeli przedstawiono, które jednostki muszą zostać zmigrowane z wersji 2 do 3.

|Typ jednostki tworzenia w wersji 2|Typ jednostki tworzenia v3|Przykład|
|--|--|--|
|Jednostka złożona|Jednostka poznania maszyn|[Dowiedz się więcej](#migrate-v2-composite-entity)|
|Jednostka hierarchiczna|rola jednostki uczenia maszynowego|[Dowiedz się więcej](#migrate-v2-hierarchical-entity)|

## <a name="migrate-v2-composite-entity"></a>Migrowanie jednostki złożonej w wersji 2

Każdy element podrzędny programu w wersji 2 musi być reprezentowany za pomocą podjednostki jednostki uczenia maszynowego v3. Jeśli złożony element podrzędny jest prekompilowanym, regularnym wyrażeniem lub jednostką listy, należy to zastosować jako wymaganą funkcję w podjednostce.

Zagadnienia dotyczące planowania migracji jednostki złożonej do jednostki uczenia maszynowego:
* Jednostek podrzędnych nie można używać w wzorcach
* Jednostki podrzędne nie są już udostępniane
* Jednostki podrzędne muszą mieć etykietę, jeśli są one używane jako nieoparte na maszynach

### <a name="existing-features"></a>Istniejące funkcje

Wszelkie listy fraz używane do zwiększenia wyrazów w jednostce złożonej powinny być stosowane jako funkcja do jednostki uczenia maszynowego (nadrzędnego), jednostki podjednostki (podrzędnej) lub zamiaru (Jeśli lista fraz ma zastosowanie tylko do jednego celu). Zaplanuj dodanie funkcji do jednostki, w której ma ona zwiększyć się znacznie. Nie należy dodawać funkcji ogólnie do jednostki uczenia maszynowego (nadrzędnego), jeśli będzie ona najbardziej znacznie zwiększać prognozowanie podjednostki (elementu podrzędnego).

### <a name="new-features"></a>Nowe funkcje

W przypadku tworzenia wersji 3 Dodaj krok planowania, aby oszacować jednostki jako możliwe funkcje dla wszystkich jednostek i intencji.

### <a name="example-entity"></a>Przykładowa jednostka

Ta jednostka jest tylko przykładem. Migracja jednostek może wymagać innych zagadnień.

Należy wziąć pod uwagę projekt 2 dla modyfikacji Pizza `order` , który używa:
* wstępnie zbudowany datetimeV2 do czasu dostawy
* Lista fraz w celu zwiększenia określonych wyrazów, takich jak Pizza, kołowy, crust i topping
* Lista jednostek do wykrywania toppings takich jak grzyby, oliwki, Pepperoni.

Przykład wypowiedź dla tej jednostki to:

`Change the toppings on my pie to mushrooms and delivery it 30 minutes later`

W poniższej tabeli przedstawiono proces migracji:

|Modele v2|Modele v3|
|--|--|
|Jednostka składników nadrzędnych o nazwie `Order`|Jednostka "Parent-Machine-Learning" o nazwie `Order`|
|DatetimeV2 wstępnie skompilowanych elementów podrzędnych|* Migruj prekompilowany obiekt do nowej aplikacji.<br>* Dodaj wymaganą funkcję w obiekcie nadrzędnym dla wstępnie skompilowanego datetimeV2.|
|Jednostka listy podrzędnej dla toppings|* Migruj jednostkę listy do nowej aplikacji.<br>* Następnie Dodaj wymaganą funkcję w obiekcie nadrzędnym dla jednostki listy.|


## <a name="migrate-v2-hierarchical-entity"></a>Migrowanie jednostki hierarchicznej v2

W wersji 2 autorstwa jednostka hierarchiczna została udostępniona przed rolami istniejącymi w LUIS. Oba te funkcje są obsługiwane w tym samym celu wyodrębniania jednostek na podstawie użycia kontekstu. Jeśli masz jednostki hierarchiczne, możesz je traktować jako proste jednostki z rolami.

W przypadku tworzenia wersji 3:
* Rolę można zastosować w jednostce Uczenie maszynowe (nadrzędne).
* Nie można zastosować roli do żadnych podjednostek.

Ta jednostka jest tylko przykładem. Migracja jednostek może wymagać innych zagadnień.

Aby zmodyfikować Pizza, należy rozważyć jednostkę hierarchiczną v2 `order` :
* gdzie każdy element podrzędny określa oryginalny topping lub końcowy topping

Przykład wypowiedź dla tej jednostki to:

`Change the topping from mushrooms to olives`

W poniższej tabeli przedstawiono proces migracji:

|Modele v2|Modele v3|
|--|--|
|Jednostka składników nadrzędnych o nazwie `Order`|Jednostka "Parent-Machine-Learning" o nazwie `Order`|
|Podmiot podrzędny-hierarchiczny z pierwotną i końcową Pizza topping|* Dodaj rolę do `Order` każdego topping.|

## <a name="api-change-constraint-replaced-with-required-feature"></a>Ograniczenie zmiany interfejsu API zostało zastąpione wymaganą funkcją

Ta zmiana została wprowadzona w maju 2020 na konferencji Build i dotyczy tylko interfejsów API tworzenia v3, w których aplikacja korzysta z ograniczonej funkcji. Jeśli przeprowadzasz migrację z wersji 2 Autorstwo do V3 Authoring lub nie korzystasz z ograniczonych funkcji v3, Pomiń tę sekcję.

**Funkcjonalność** — możliwość wymagania istniejącej jednostki jako funkcji do innego modelu i wyodrębnienia tego modelu tylko wtedy, gdy jednostka zostanie wykryta. Funkcjonalność nie uległa zmianie, ale interfejs API i terminologia zostały zmienione.

|Poprzednia terminologia|Nowa terminologia|
|--|--|
|`constrained feature`<br>`constraint`<br>`instanceOf`|`required feature`<br>`isRequired`|

#### <a name="automatic-migration"></a>Automatyczna migracja

Od **czerwca 19 2020** nie będzie można programowo tworzyć ograniczeń przy użyciu poprzedniego interfejsu API tworzenia, który uwidoczni tę funkcję.

Wszystkie istniejące funkcje ograniczenia zostaną automatycznie zmigrowane do wymaganej flagi funkcji. Do interfejsu API przewidywania nie są wymagane żadne zmiany programistyczne i nie wprowadzono żadnych zmian w jakości dokładności przewidywania.

#### <a name="luis-portal-changes"></a>Zmiany w portalu LUIS

Portal LUIS w wersji zapoznawczej przywołuje tę funkcję jako **ograniczenie**. Bieżący Portal LUIS określa tę funkcję jako **wymaganą funkcję**.

#### <a name="previous-authoring-api"></a>Poprzedni interfejs API tworzenia

Ta funkcja została zastosowana w **[interfejsie podrzędnym tworzenia](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5d86cf3c6a25a45529767d77)** w wersji zapoznawczej Tworzenie jednostki podrzędnej jako części definicji jednostki, przy użyciu `instanceOf` Właściwości podrzędnej jednostki:

```json
{
    "name" : "dayOfWeek",
    "instanceOf": "datetimeV2",
    "children": [
        {
           "name": "dayNumber",
           "instanceOf": "number",
           "children": []
        }
    ]
}
```

#### <a name="new-authoring-api"></a>Nowy interfejs API tworzenia

Ta funkcja jest teraz stosowana wraz z **[interfejsem API dodawania relacji funkcji jednostki](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5d9dc1781e38aaec1c375f26)** przy użyciu `featureName` `isRequired` właściwości i. Wartość `featureName` właściwości jest nazwą modelu.

```json
{
    "featureName": "YOUR-MODEL-NAME-HERE",
    "isRequired" : true
}
```


## <a name="next-steps"></a>Następne kroki

* [Zasoby dla deweloperów](developer-reference-resource.md)
