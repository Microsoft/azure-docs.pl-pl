---
title: Migrowanie do jednostki nadanej przez maszynę v3
titleSuffix: Azure Cognitive Services
description: Tworzenie w wersji 3 to jeden nowy typ jednostki, obiekt, który został wytworzony przez maszynę oraz możliwość dodawania relacji do jednostki, która jest poznania maszyn i innych obiektów lub funkcji aplikacji.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: diberry
ms.openlocfilehash: b5dbcd9033d9a41e43ea907d043e0c0486b236db
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "75563835"
---
# <a name="migrate-to-v3-authoring-entity"></a>Migrowanie do jednostki tworzenia w wersji 3

Tworzenie w wersji 3 to jeden nowy typ jednostki, obiekt, który został wytworzony przez maszynę oraz możliwość dodawania relacji do jednostki, która jest poznania maszyn i innych obiektów lub funkcji aplikacji.

## <a name="entities-are-decomposable-in-v3"></a>Jednostki są roztworzące w wersji 3

Jednostki utworzone przy użyciu interfejsów API tworzenia v3, przy użyciu [interfejsów API](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview) lub [portalu w wersji zapoznawczej](https://preview.luis.ai/), umożliwiają utworzenie modelu jednostki warstwowej z elementem nadrzędnym i podrzędnym. Element nadrzędny jest znany jako obiekt, który jest rozpoznawany przez **maszynę** i elementy podrzędne, są znane jako **podskładniki** maszyny, którą nauczył się.

Każdy podskładnik jest również jednostką uczenia maszynowego, ale z dodanymi opcjami konfiguracji ograniczeń i deskryptorów.

* **Ograniczenia** są dokładnymi regułami dopasowania tekstu, które gwarantują, że jednostka jest wyodrębniana, gdy jest zgodna z regułą. Reguła jest definiowana przez dokładną jednostkę zgodną z tekstem, obecnie: [prekompilowana](luis-reference-prebuilt-entities.md)jednostka, [Jednostka wyrażenia regularnego](reference-entity-regular-expression.md)lub [Jednostka listy](reference-entity-list.md).
* **Deskryptory** są [funkcjami](luis-concept-feature.md), takimi jak listy fraz lub jednostki, które są używane do silnie wskazujące jednostkę.

Tworzenie w wersji 3 to jeden nowy typ jednostki, obiekt, który został wytworzony przez maszynę oraz możliwość dodawania relacji do jednostki, która jest poznania maszyn i innych obiektów lub funkcji aplikacji.

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
    * Role — role można stosować tylko do jednostki, która jest wyuczenia maszynowego (nadrzędnego). Nie można zastosować ról do podskładników
    * Testy wsadowe i wzorce używające jednostek hierarchicznych i złożonych

Podczas projektowania planu migracji należy pozostawić czas, aby przejrzeć końcowe jednostki, które zostały wystawione przez maszyny, po przeprowadzeniu migracji wszystkich jednostek hierarchicznych i złożonych. Podczas gdy migracja prosta będzie działała, po dokonaniu zmiany i przejrzeniu wyników testów partii i kodzie JSON przewidywania bardziej ujednolicony kod JSON może prowadzić do wprowadzenia zmian, dzięki czemu ostateczne informacje dostarczane do aplikacji po stronie klienta są zorganizowane inaczej. Jest to podobne do refaktoryzacji kodu i powinna być traktowana przy użyciu tego samego procesu przeglądu, który jest używany w organizacji.

Jeśli nie masz testów wsadowych dla modelu w wersji 2 i Przeprowadź migrację testów wsadowych do modelu V3 w ramach migracji, nie będziesz w stanie sprawdzić, w jaki sposób migracja będzie miała wpływ na wyniki prognozowania punktów końcowych.

## <a name="migrating-from-v2-entities"></a>Migrowanie z jednostek v2

Po rozpoczęciu pracy z modelem tworzenia v3 należy rozważyć sposób przechodzenia do jednostki, w której nauczysz się na maszynę i jej podskładniki, w tym ograniczenia i deskryptory.

W poniższej tabeli przedstawiono, które jednostki muszą zostać zmigrowane z wersji 2 do 3.

|Typ jednostki tworzenia w wersji 2|Typ jednostki tworzenia v3|Przykład|
|--|--|--|
|Jednostka złożona|Jednostka poznania maszyn|[Dowiedz się więcej](#migrate-v2-composite-entity)|
|Jednostka hierarchiczna|Rola jednostki poznania maszynowego|[Dowiedz się więcej](#migrate-v2-hierarchical-entity)|

## <a name="migrate-v2-composite-entity"></a>Migrowanie jednostki złożonej w wersji 2

Każdy element podrzędny elementu złożonego w wersji 2 powinien być reprezentowany za pomocą podskładnika jednostki wystawionej przez maszynę v3. Jeśli złożony element podrzędny jest prekompilowanym, regularnym wyrażeniem lub jednostką listy, należy to zastosować jako **ograniczenie** dla podskładnika reprezentującego element podrzędny.

Zagadnienia dotyczące planowania migracji jednostki złożonej do jednostki, która jest poznania maszynowa:
* Jednostek podrzędnych nie można używać w wzorcach
* Jednostki podrzędne nie są już udostępniane
* Jednostki podrzędne muszą mieć etykietę, jeśli są one używane jako nieoparte na maszynach

### <a name="existing-descriptors"></a>Istniejące deskryptory

Wszystkie listy fraz używane do zwiększenia wyrazów w jednostce złożonej powinny być stosowane jako deskryptor do jednostki, która jest poznania maszynowego (element nadrzędny), jednostki podskładnik (podrzędnego) lub zamiaru (Jeśli lista fraz dotyczy tylko jednego celu). Zaplanuj dodanie deskryptora do jednostki, która powinna się znacznie zwiększyć. Nie należy dodawać deskryptora ogólnie do jednostki uczenia maszynowego (nadrzędnego), jeśli będzie ona najbardziej znacznie zwiększyć prognozowanie podskładnika (elementu podrzędnego).

### <a name="new-descriptors"></a>Nowe deskryptory

W przypadku tworzenia w wersji 3 Dodaj krok planowania, aby oszacować jednostki jako możliwe dla wszystkich jednostek i intencji.

### <a name="example-entity"></a>Przykładowa jednostka

Ta jednostka jest tylko przykładem. Migracja jednostek może wymagać innych zagadnień.

Należy wziąć pod uwagę projekt 2 dla modyfikacji `order` Pizza, który używa:
* wstępnie zbudowany datetimeV2 do czasu dostawy
* Lista fraz w celu zwiększenia określonych wyrazów, takich jak Pizza, kołowy, crust i topping
* Lista jednostek do wykrywania toppings takich jak grzyby, oliwki, Pepperoni.

Przykład wypowiedź dla tej jednostki to:

`Change the toppings on my pie to mushrooms and delivery it 30 minutes later`

W poniższej tabeli przedstawiono proces migracji:

|Modele v2|Modele v3|
|--|--|
|Jednostka składników nadrzędnych o nazwie`Order`|Jednostka zadana przez maszynę nadrzędną o nazwie`Order`|
|DatetimeV2 wstępnie skompilowanych elementów podrzędnych|* Migruj prekompilowany obiekt do nowej aplikacji.<br>* Dodaj ograniczenie elementu nadrzędnego dla wstępnie skompilowanego datetimeV2.|
|Jednostka listy podrzędnej dla toppings|* Migruj jednostkę listy do nowej aplikacji.<br>* Następnie Dodaj ograniczenie do elementu nadrzędnego dla jednostki listy.|


## <a name="migrate-v2-hierarchical-entity"></a>Migrowanie jednostki hierarchicznej v2

W wersji 2 autorstwa jednostka hierarchiczna została udostępniona przed rolami istniejącymi w LUIS. Oba te funkcje są obsługiwane w tym samym celu wyodrębniania jednostek na podstawie użycia kontekstu. Jeśli masz jednostki hierarchiczne, możesz je traktować jako proste jednostki z rolami.

W przypadku tworzenia wersji 3:
* Rolę można zastosować w jednostce, która jest poznania maszynowo (nadrzędną).
* Nie można zastosować roli do żadnych podskładników.

Ta jednostka jest tylko przykładem. Migracja jednostek może wymagać innych zagadnień.

Aby zmodyfikować Pizza `order`, należy rozważyć jednostkę hierarchiczną v2:
* gdzie każdy element podrzędny określa oryginalny topping lub końcowy topping

Przykład wypowiedź dla tej jednostki to:

`Change the topping from mushrooms to olives`

W poniższej tabeli przedstawiono proces migracji:

|Modele v2|Modele v3|
|--|--|
|Jednostka składników nadrzędnych o nazwie`Order`|Jednostka zadana przez maszynę nadrzędną o nazwie`Order`|
|Podmiot podrzędny-hierarchiczny z pierwotną i końcową Pizza topping|* Dodaj rolę do `Order` każdego topping.|

## <a name="next-steps"></a>Następne kroki

* [Zasoby dla deweloperów](developer-reference-resource.md)
