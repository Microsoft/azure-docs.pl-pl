---
title: Ulepszanie listy wyrazów LUIS za pomocą funkcji
titleSuffix: Azure Cognitive Services
description: Użyj Language Understanding (LUIS), aby dodać funkcje aplikacji, które mogą ulepszyć wykrywanie lub przewidywania intencji i jednostek, które są kategoriami i wzorcami
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 05/17/2020
ms.openlocfilehash: 33435d2367b92f312d41f0e32fdbb3a93d59a9fd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "95999930"
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

1. Kliknij **Gotowe**. Nowa funkcja zostanie dodana do strony **funkcje** w sieci.

<a name="edit-phrase-list"></a>
<a name="delete-phrase-list"></a>
<a name="deactivate-phrase-list"></a>


> [!Note]
> Można usunąć lub dezaktywować listę fraz z kontekstowego paska narzędzi na stronie **funkcje** w sieci.

## <a name="global-phrase-list-applies-to-entire-app"></a>Globalna lista fraz ma zastosowanie do całej aplikacji

Lista fraz powinna zostać zastosowana do zamiaru lub jednostki, która ma być pomocna, ale mogą wystąpić sytuacje, w których lista fraz powinna zostać zastosowana do całej aplikacji jako funkcja **globalna** .

Na stronie funkcje sieci, wybierz listę frazy, a następnie wybierz pozycję **Ustaw jako globalną** na górny Kontekstowy pasek narzędzi.

## <a name="model-as-a-feature"></a>Model jako funkcja

Jednostką może być [Funkcja do zamiaru lub jednostki](luis-concept-feature.md).

Aby dodać jednostkę jako funkcję do celu, wybierz zamiar na stronie intencje, a następnie wybierz pozycję **+ Dodaj funkcję** powyżej kontekstowego paska narzędzi. Lista zawiera wszystkie listy fraz i jednostki, które mogą być stosowane jako funkcje.

Aby dodać jednostkę jako funkcję do innej jednostki, możesz ją dodać na stronie szczegółów intencji przy użyciu [palety Entity](label-entity-example-utterance.md#adding-entity-as-a-feature-from-the-entity-palette) lub [dodać tę funkcję](luis-how-to-add-entities.md#add-a-feature-to-a-machine-learned-entity) na stronie szczegółów jednostki.

## <a name="next-steps"></a>Następne kroki

Po dodaniu, edycji, usunięciu lub dezaktywowaniu funkcji należy ponownie [nauczyć i przetestować aplikację](luis-interactive-test.md) , aby sprawdzić, czy wydajność jest zwiększona.
