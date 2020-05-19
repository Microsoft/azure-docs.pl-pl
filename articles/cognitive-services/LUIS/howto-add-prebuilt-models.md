---
title: Wstępnie utworzone modele dla Language Understanding
titleSuffix: Azure Cognitive Services
description: LUIS zawiera zestaw wstępnie skompilowanych modeli umożliwiających szybkie dodawanie wspólnych scenariuszy użytkowników.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/07/2020
ms.author: diberry
ms.openlocfilehash: 653882db4b62d7731123faf7b177da44dbd74e3f
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83584998"
---
# <a name="add-prebuilt-models-for-common-usage-scenarios"></a>Dodaj wstępnie zbudowane modele dla typowych scenariuszy użycia

LUIS zawiera zestaw wstępnie skompilowanych modeli umożliwiających szybkie dodawanie wspólnych scenariuszy użytkowników. Jest to szybki i łatwy sposób dodawania możliwości do konwersacji aplikacji klienckiej bez konieczności projektowania modeli dla tych możliwości.

## <a name="add-a-prebuilt-domain"></a>Dodawanie wstępnie utworzonej domeny

1. Zaloguj się do [portalu Luis](https://www.luis.ai)i wybierz swoją **subskrypcję** i **zasób tworzenia** , aby wyświetlić aplikacje przypisane do tego zasobu.
1. Otwórz aplikację, wybierając jej nazwę na stronie **Moje aplikacje** .

1. Wybierz opcję **prekompilowane domeny** na pasku narzędzi po lewej stronie.

1. Znajdź domenę, którą chcesz dodać do aplikacji, a następnie wybierz przycisk **Dodaj domenę** .

    > [!div class="mx-imgBorder"]
    > ![Dodaj prezbudowaną domenę kalendarza](./media/luis-prebuilt-domains/add-prebuilt-domain.png)

## <a name="add-a-prebuilt-intent"></a>Dodawanie wstępnie skompilowanego zamiaru

1. Zaloguj się do [portalu Luis](https://www.luis.ai)i wybierz swoją **subskrypcję** i **zasób tworzenia** , aby wyświetlić aplikacje przypisane do tego zasobu.
1. Otwórz aplikację, wybierając jej nazwę na stronie **Moje aplikacje** .

1. Na stronie **intencje** wybierz pozycję **Dodaj prebudowany cel domeny** z paska narzędzi powyżej listy intencje.

1. Wybierz opcję **Narzędzia. Cancel** w wyskakującym oknie dialogowym.

    > [!div class="mx-imgBorder"]
    > ![Dodaj wstępnie utworzony cel](./media/luis-prebuilt-domains/add-prebuilt-domain-intents.png)

1. Wybierz przycisk **gotowe** .

## <a name="add-a-prebuilt-entity"></a>Dodaj wstępnie utworzoną jednostkę
1. Zaloguj się do [portalu Luis](https://www.luis.ai)i wybierz swoją **subskrypcję** i **zasób tworzenia** , aby wyświetlić aplikacje przypisane do tego zasobu.
1. Otwórz aplikację, wybierając jej nazwę na stronie **Moje aplikacje** .
1. Wybierz **jednostki** z lewej strony.

1. Na stronie **jednostki** wybierz pozycję **Dodaj prekompilowaną jednostkę**.

1. W oknie dialogowym **Dodawanie wstępnie utworzonych jednostek** Wybierz wstępnie utworzoną jednostkę.

    > [!div class="mx-imgBorder"]
    > ![Okno dialogowe Dodawanie wstępnie skompilowanej jednostki](./media/luis-prebuilt-domains/add-prebuilt-entity.png)

1. Wybierz pozycję **Done** (Gotowe). Po dodaniu jednostki nie ma potrzeby uczenia aplikacji.

## <a name="publish-to-view-prebuilt-model-from-prediction-endpoint"></a>Publikuj w celu wyświetlenia prekompilowanego modelu z poziomu punktu końcowego przewidywania

Najprostszym sposobem wyświetlenia wartości prekompilowanego modelu jest zapytanie z opublikowanego punktu końcowego.

## <a name="entities-containing-a-prebuilt-entity-token"></a>Jednostki zawierające wstępnie utworzony token jednostki

Jeśli masz zainstalowaną maszynę wymagającą wymaganej funkcji wstępnie skompilowanej jednostki, Dodaj podskładnik do jednostki, której dotyczy dana maszyna, a następnie Dodaj _wymaganą_ funkcję wstępnie skompilowanej jednostki.

## <a name="next-steps"></a>Następne kroki
> [!div class="nextstepaction"]
> [Kompilowanie modelu z pliku CSV przy użyciu interfejsów API REST](./luis-tutorial-node-import-utterances-csv.md)
