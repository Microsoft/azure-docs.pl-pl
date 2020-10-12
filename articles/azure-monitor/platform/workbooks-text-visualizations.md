---
title: Azure Monitor wizualizacje tekstu skoroszytu
description: Dowiedz się więcej na temat wszystkich wizualizacji tekstu w skoroszycie Azure Monitor.
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/04/2020
ms.author: lagayhar
ms.openlocfilehash: e8f2d9495484b781b26962c2946b5bada6c38b4c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89664923"
---
# <a name="text-visualizations"></a>Wizualizacje tekstu

Skoroszyty umożliwiają autorom dołączenie bloków tekstowych do ich skoroszytów. Tekst może być analizą danych telemetrycznych, informacjami, które pomagają użytkownikom interpretować dane, nagłówki sekcji itd.

[![Zrzut ekranu przedstawiający tabelę Apdex tekstu](./media/workbooks-text-visualizations/apdex.png)](./media/workbooks-text-visualizations/apdex.png#lightbox)

Tekst jest dodawany za pomocą kontrolki promocji, która zapewnia pełną kontrolę formatowania. Obejmują one różne style nagłówków i czcionek, hiperłącza, tabele itd.

Tryb edycji:

![Zrzut ekranu przedstawiający krok tekstu w trybie edycji.](./media/workbooks-text-visualizations/text-edit-mode.png)

Tryb podglądu:

![Zrzut ekranu przedstawiający krok tekstu w trybie edycji na karcie Podgląd.](./media/workbooks-text-visualizations/text-edit-mode-preview.png)

## <a name="add-a-text-control"></a>Dodawanie kontrolki tekstu

1. Przełącz skoroszyt do trybu edycji, klikając element paska narzędzi **Edytowanie** .
2. Użyj linku **Dodaj tekst** , aby dodać kontrolkę tekstu do skoroszytu.
3. Dodaj wartość promocji w polu Edytor.
4. Użyj opcji *styl tekstu* , aby przełączać się między zwykłymi promocji i promocji zawiniętymi przy użyciu standardowych informacji/ostrzeżeń i stylów błędów w Azure Portal.
5. Użyj karty **Podgląd** , aby zobaczyć, jak będzie wyglądać zawartość. Podczas edytowania Podgląd wyświetli zawartość wewnątrz obszaru paska przewijania, aby ograniczyć jej rozmiar; Jednak w środowisku uruchomieniowym zawartość promocji zostanie rozwinięta, aby wypełnić wszystkie potrzebne miejsce, bez pasków przewijania.
6. Wybierz przycisk **gotowe do edycji** , aby zakończyć edytowanie kroku.

> [!TIP]
> Skorzystaj z tego [arkusza Ściągawka z promocji](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet) , aby poznać różne opcje formatowania.

## <a name="text-styles"></a>Style tekstu

Następujące style tekstu są dostępne dla etapu tekstu:

| Styl     | Objaśnienie                                                                               |
|-----------|-------------------------------------------------------------------------------------------|
| `plain`   | Nie jest stosowane żadne dodatkowe formatowanie.                                                      |
| `info`    | Styl portalu "informacje" z  `ℹ` lub podobną ikoną i ogólnie niebieskim tłem.      |
| `error`   | Styl portalu "Error" z `❌` lub podobną ikoną i ogólnie czerwonym tłem.     |
| `success` | Styl portalu "powodzenie" z `✔` lub podobną ikoną i ogólnie zielonym tłem.  |
| `upsell`  | Styl portalu "do sprzedawania" z `🚀` lub podobną ikoną i ogólnie purpurowym tłem. |
| `warning` | Styl portalu "ostrzeżenie" z `⚠` lub podobną ikoną i ogólnie niebieskim tłem.   |

Zamiast wybierania określonego stylu można także wybrać parametr tekstowy jako źródło stylu. Wartość parametru musi być jedną z powyższych wartości tekstowych. Brak wartości lub jakakolwiek Nierozpoznana wartość będzie traktowana jako `plain` styl.

Przykład stylu informacyjnego:

![Zrzut ekranu przedstawiający wygląd stylu informacji.](./media/workbooks-text-visualizations/text-preview-info-style.png)

Przykład stylu ostrzeżenia:

![Zrzut ekranu przedstawiający wygląd stylu ostrzeżenia.](./media/workbooks-text-visualizations/text-warning-style.png)

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak utworzyć [Wykres w skoroszytach](workbooks-chart-visualizations.md).
* Dowiedz się, jak utworzyć [siatkę w skoroszytach](workbooks-grid-visualizations.md).
