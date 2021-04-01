---
title: Wzorce ułatwiają prognozowanie — LUIS
titleSuffix: Azure Cognitive Services
description: Wzorzec umożliwia uzyskanie większej dokładności dla zamiaru bez udostępniania wielu wyrażenia długości.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/11/2019
ms.openlocfilehash: cda71c6e825ff27ba9b03e1306ccb287663e8613
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "95025963"
---
# <a name="patterns-improve-prediction-accuracy"></a>Wzorce poprawiają dokładność przewidywania
Wzorce zostały zaprojektowane w celu poprawy dokładności, gdy kilka wyrażenia długości jest bardzo podobne.  Wzorzec umożliwia uzyskanie większej dokładności dla zamiaru bez udostępniania wielu wyrażenia długości.

## <a name="patterns-solve-low-intent-confidence"></a>Wzorce rozwiązują niski stopień zaufania
Rozważmy aplikację kadr, która raportuje na schemacie organizacyjnym w odniesieniu do pracownika. Mając daną nazwę i relację pracownika, LUIS zwraca pracowników, których to dotyczy. Weź pod uwagę pracownika, Tomasz, z nazwą Menedżera Alicja i zespół podwładnych o nazwie: Michael, Rebecca i Carl.

![Obraz przedstawiający schemat organizacyjny](./media/luis-concept-patterns/org-chart.png)

|Wypowiedzi|Przewidywany cel|Ocena intencji|
|--|--|--|
|Kto jest podwładnym?|GetOrgChart|.30|
|Kto jest podwładnym elementu "Tomasz"?|GetOrgChart|.30|

Jeśli aplikacja ma od 10 do 20 wyrażenia długości z różnymi długościami zdania, inną kolejnością wyrazów, a nawet różnymi wyrazami (synonimami "podwładnych", "manage", "Report"), LUIS może zwrócić wynik o niskim poziomie pewności. Utwórz wzorzec, aby pomóc LUIS zrozumieć znaczenie kolejności wyrazów.

Wzorce rozwiązują następujące sytuacje:

* Wynik przeznaczenie jest niski
* Poprawna wartość nie jest najważniejszym wynikiem, ale zbyt blisko najwyższego wyniku.

## <a name="patterns-are-not-a-guarantee-of-intent"></a>Wzorce nie są gwarancją zamiaru
Wzorce korzystają z różnych technologii przewidywania. Ustawienie zamiaru dla szablonu wypowiedź w wzorcu nie jest gwarancją przewidywania intencji, ale jest to silny sygnał.

<a name="patterns-do-not-improve-entity-detection"></a>

## <a name="patterns-do-not-improve-machine-learning-entity-detection"></a>Wzorce nie poprawiają wykrywania jednostek uczenia maszynowego

Wzorzec jest głównie przeznaczony do przewidywania intencji i ról. _Wzorzec. Każda_ jednostka służy do wyodrębniania jednostek o dowolnej postaci. Podczas gdy wzorce używają jednostek, wzorzec nie pomaga wykrywać jednostki uczenia maszynowego.

Nie oczekuje się, że w przypadku zwinięcia wielu wyrażenia długości do jednego wzorca nie widzisz ulepszonej przewidywania jednostek. Aby proste jednostki były wyzwalane, należy dodać wyrażenia długości lub użyć jednostek listy, ponieważ wzorzec nie zostanie uruchomiony.

## <a name="patterns-use-entity-roles"></a>Wzorce używają ról jednostek
Jeśli co najmniej dwie jednostki we wzorcu są powiązane z kontekstem, wzorce używają [ról](./luis-concept-entity-types.md) jednostki do wyodrębniania informacji kontekstowych dotyczących jednostek.

## <a name="prediction-scores-with-and-without-patterns"></a>Wyniki przewidywania z wzorcem i bez
Mając wystarczająco dużo przykład wyrażenia długości, LUIS może zwiększyć wiarygodność przewidywania bez wzorców. Wzorce zwiększają wynik pewności bez konieczności podawania tylu wyrażenia długości.

## <a name="pattern-matching"></a>Dopasowanie do wzorca
Wzorzec jest dopasowywany w oparciu o najpierw wykrywanie jednostek wewnątrz wzorca, a następnie sprawdzanie pozostałej części wyrazów i kolejności wyrazów wzorca. Jednostki są wymagane we wzorcu do dopasowania do wzorca. Wzorzec jest stosowany na poziomie tokenu, a nie na poziomie znaku.

## <a name="pattern-only-apps"></a>Aplikacje tylko dla wzorców
Możesz utworzyć aplikację z intencjami, które nie mają przykładowej wyrażenia długości, o ile istnieje wzorzec dla każdego zamiaru. W przypadku aplikacji tylko do wzorca wzorzec nie powinien zawierać jednostek uczenia maszynowego, ponieważ wymagają one przykładowego wyrażenia długości.

## <a name="patternany-entity"></a>Jednostka Pattern.any

[!INCLUDE [Pattern.any entity - concepts](./includes/pattern-any-entity.md)]

## <a name="best-practices"></a>Najlepsze rozwiązania
Poznaj [najlepsze rozwiązania](luis-concept-best-practices.md).

## <a name="pattern-syntax"></a>Składnia wzorca

Poznaj składnię wzorca na podstawie [odwołania do składni wzorców](reference-pattern-syntax.md).

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o wzorcach:

* [Jak dodać wzorce](luis-how-to-model-intent-pattern.md)
* [Jak dodać wzorzec. dowolna jednostka](luis-how-to-add-entities.md#add-a-patternany-entity)
* [Składnia wzorców](reference-pattern-syntax.md)

> [!div class="nextstepaction"]
> [Dowiedz się, jak zaimplementować wzorce w tym samouczku](luis-tutorial-pattern.md)
