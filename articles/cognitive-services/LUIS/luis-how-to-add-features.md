---
title: Funkcje — LUIS
titleSuffix: Azure Cognitive Services
description: Użyj Language Understanding (LUIS), aby dodać funkcje aplikacji, które mogą ulepszyć wykrywanie lub przewidywania intencji i jednostek, które są kategoriami i wzorcami
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/06/2020
ms.author: diberry
ms.openlocfilehash: 12445ec5b14f4c274e471bf1b061a3b221664d20
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83592308"
---
# <a name="use-features-to-boost-signal-of-word-list"></a>Korzystaj z funkcji, aby zwiększyć liczbę sygnałów listy wyrazów

Aby poprawić swoją dokładność, możesz dodać funkcje do aplikacji LUIS. Funkcje ułatwiają LUIS, dostarczając wskazówki, że określone słowa i frazy są częścią słownictwa domeny aplikacji.

Zapoznaj się z [pojęciami](luis-concept-feature.md) , aby zrozumieć, kiedy i dlaczego należy używać funkcji.

## <a name="add-phrase-list-as-a-feature"></a>Dodaj listę fraz jako funkcję

1. Zaloguj się do [portalu Luis](https://www.luis.ai)i wybierz swoją **subskrypcję** i **zasób tworzenia** , aby wyświetlić aplikacje przypisane do tego zasobu.
1. Otwórz aplikację, wybierając jej nazwę na stronie **Moje aplikacje** .
1. Wybierz pozycję **kompilacja**, a następnie wybierz pozycję **funkcje** w lewym panelu aplikacji.

1. Na stronie **funkcje** wybierz pozycję **+ Utwórz**.

1. W oknie dialogowym **Tworzenie nowej listy fraz** wprowadź nazwę, taką jak `Cities` . W polu **wartość** wprowadź przykłady miast, na przykład `Seattle` . Można wpisać jedną wartość naraz lub zestaw wartości rozdzielonych przecinkami, a następnie nacisnąć klawisz **Enter**.

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu przedstawiający Dodawanie funkcji (lista fraz) miasta](./media/luis-add-features/add-phrase-list-cities.png)

    Po wprowadzeniu wystarczającej wartości dla LUIS pojawia się sugestii. Możesz **dodać wszystkie** proponowane wartości lub wybrać poszczególne terminy.

1. Należy pamiętać, że **te wartości są sprawdzane zamiennie** , jeśli frazy mogą być używane zamiennie.

1. Lista fraz może dotyczyć całej aplikacji z ustawieniem **globalnym** lub określonym modelem (intencją lub jednostką). Jeśli tworzysz listę fraz, jako _funkcję_ z intencją lub jednostką, przełącznik nie jest ustawiany dla elementu Global. W takim przypadku znaczenie przełącznika polega na tym, że funkcja jest lokalna tylko dla tego modelu, w związku z czym _nie jest globalna_ dla aplikacji.

1. Wybierz pozycję **Done** (Gotowe). Nowa funkcja zostanie dodana do strony **funkcje** w sieci.

<a name="edit-phrase-list"></a>
<a name="delete-phrase-list"></a>
<a name="deactivate-phrase-list"></a>

> [!Note]
> Można usunąć lub dezaktywować listę fraz z kontekstowego paska narzędzi na stronie **funkcje** w sieci.

## <a name="next-steps"></a>Następne kroki

Po dodaniu, edycji, usunięciu lub dezaktywowaniu funkcji należy ponownie [nauczyć i przetestować aplikację](luis-interactive-test.md) , aby sprawdzić, czy wydajność jest zwiększona.
