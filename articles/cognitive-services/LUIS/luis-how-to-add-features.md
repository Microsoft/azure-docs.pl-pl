---
title: Deskryptory — LUIS
titleSuffix: Azure Cognitive Services
description: Użyj Language Understanding (LUIS), aby dodać funkcje aplikacji, które mogą ulepszyć wykrywanie lub przewidywania intencji i jednostek, które są kategoriami i wzorcami
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/02/2020
ms.author: diberry
ms.openlocfilehash: 7560fdcbfc77ea2655e8af641794478ead4c11c7
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80631450"
---
# <a name="use-descriptors-to-boost-signal-of-word-list"></a>Użyj deskryptorów, aby zwiększyć sygnał listy słów

Aby poprawić swoją dokładność, możesz dodać funkcje do aplikacji LUIS. Funkcje ułatwiają LUIS, dostarczając wskazówki, że określone słowa i frazy są częścią słownictwa domeny aplikacji.

[Deskryptor](luis-concept-feature.md) (lista fraz) zawiera grupę wartości (wyrazów lub fraz), które należą do tej samej klasy i muszą być traktowane podobnie (na przykład nazwy miast lub produktów). LUIS informacje o jednym z nich są również automatycznie stosowane do innych. Ta lista nie jest taka sama jak [Jednostka listy](reference-entity-list.md) (dokładne dopasowanie tekstu) pasujących wyrazów.

Deskryptor dodaje do słownictwa domeny aplikacji jako drugi sygnał LUIS o tych słowach.

Zapoznaj się z [pojęciami dotyczącymi funkcji](luis-concept-feature.md) , aby zrozumieć, kiedy i dlaczego należy używać deskryptora.

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="add-descriptor"></a>Dodawanie deskryptora

1. Otwórz aplikację, klikając jej nazwę na stronie **Moje aplikacje** , a następnie kliknij pozycję **Kompiluj**, a następnie kliknij pozycję **deskryptory** w lewym panelu aplikacji.

1. Na stronie **deskryptorów** kliknij pozycję **+ Dodaj deskryptor**.

1. W oknie dialogowym **Tworzenie nowego deskryptora listy wyrazów** wprowadź nazwę, `Cities` na przykład dla deskryptora. W polu **wartość** wpisz wartości deskryptorów, na przykład `Seattle`. Można wpisać jedną wartość naraz lub zestaw wartości rozdzielonych przecinkami, a następnie nacisnąć klawisz **Enter**.

    > [!div class="mx-imgBorder"]
    > ![Dodawanie miejscowości deskryptorów](./media/luis-add-features/add-phrase-list-cities.png)

    Po wprowadzeniu wystarczającej wartości dla LUIS pojawia się sugestii. Możesz **dodać wszystkie** proponowane wartości lub wybrać poszczególne terminy.

1. Należy pamiętać, że **te wartości są sprawdzane zamiennie** , jeśli dodawane wartości deskryptora są alternatywami, które mogą być używane zamiennie.

1. Lista fraz może dotyczyć całej aplikacji z ustawieniem **globalnym** lub określonym modelem (intencją lub jednostką). Jeśli tworzysz listę fraz jako _deskryptora_ z zamiaru lub obiektu, przełącznik ma wartość nie Global. W takim przypadku znaczenie przełącznika polega na tym, że deskryptor jest funkcją tylko dla tego modelu, w związku z czym _nie jest globalna_ dla aplikacji.

1. Wybierz pozycję **Gotowe**. Nowy deskryptor zostanie dodany do strony **deskryptorów** .

<a name="edit-phrase-list"></a>
<a name="delete-phrase-list"></a>
<a name="deactivate-phrase-list"></a>

> [!Note]
> Można usunąć lub dezaktywować deskryptor z kontekstowego paska narzędzi na stronie **deskryptorów** .

## <a name="next-steps"></a>Następne kroki

Po dodaniu, edycji, usunięciu lub dezaktywowaniu deskryptora ponownie [nauczenie i przetestowanie aplikacji](luis-interactive-test.md) w celu sprawdzenia, czy wydajność jest zwiększona.
