---
title: Tworzenie szablonów przy użyciu Visual Studio Code
description: Dowiedz się, jak zainstalować i używać Visual Studio Code i rozszerzenia narzędzi Azure Resource Manager.
author: mumian
ms.topic: conceptual
ms.date: 08/30/2019
ms.author: jgao
ms.openlocfilehash: 585e2773ea5d6af184f85e65b63d39b60d632146
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79273633"
---
# <a name="use-visual-studio-code-to-create-azure-resource-manager-templates"></a>Tworzenie Azure Resource Manager szablonów przy użyciu Visual Studio Code

Visual Studio Code to lekki, wieloplatformowy Edytor typu open-source. Rozszerzenie narzędzi szablonu Azure Resource Manager to wtyczka do tworzenia szablonów Menedżer zasobów. Rozszerzenie dodaje obsługę języka dla szablonów, aby zapewnić funkcję IntelliSense, wyróżnianie składni, pomoc w wierszu i wiele innych funkcji językowych. Wspólnie udostępniają one środowisko programistyczne dla deweloperów.

## <a name="install-visual-studio-code"></a>Instalowanie programu Visual Studio Code

Program Visual Studio Code obsługuje MacOS, Windows i Linux.  Można go zainstalować z [Visual Studio Code](https://code.visualstudio.com/).

## <a name="install-resource-manager-tools-extension"></a>Zainstaluj rozszerzenie narzędzi Menedżer zasobów

1. Otwórz program Visual Studio Code.
1. Z menu po lewej stronie wybierz pozycję **rozszerzenia** . Lub z menu **Widok** wybierz pozycję **rozszerzenia** , aby otworzyć okienko rozszerzenia.

    ![Instalowanie rozszerzenia Visual Studio Code Menedżer zasobów Tools](./media/use-vs-code-to-create-template/resource-manager-visual-studio-code-tools-extension.png)
1. Wyszukaj **Menedżer zasobów**.
1. Wybierz pozycję **Zainstaluj** w obszarze **narzędzia Azure Resource Manager**.

## <a name="the-extension-features"></a>Funkcje rozszerzenia

### <a name="colorization-for-template-language-expressions"></a>Kolorowanie wyrażeń języka szablonu

Parametry, zmienne, funkcje, nazwy i wyrażenia są kodowane kolorami, jak pokazano na poniższym zrzucie ekranu:

![Kolorowanie rozszerzeń narzędzi Menedżer zasobów Visual Studio Code](./media/use-vs-code-to-create-template/resource-manager-tools-extension-colorization.png)

Widok konspektu szablonu ułatwia nawigowanie po dużych szablonach.

### <a name="intellisense"></a>Technologia

Rozszerzenie szablonu Menedżer zasobów wie, że możliwe jest uzupełnianie nazw funkcji, parametrów, zmiennych i odwołań. Sugestie IntelliSense pojawiają się w trakcie wpisywania. Jeśli będziesz kontynuować wpisywanie znaków, lista elementów członkowskich (zmiennych, metod itp.) zostanie przefiltrowana w celu uwzględnienia tylko elementów członkowskich zawierających wpisane znaki. Naciśnięcie klawisza **Tab** lub **Enter** spowoduje wstawienie wybranego elementu członkowskiego.

- Wbudowane nazwy funkcji

    ![Funkcje IntelliSense Visual Studio Code rozszerzenia narzędzi Menedżer zasobów Tools](./media/use-vs-code-to-create-template/resource-manager-tools-extension-intellisense-functions.png)

- Odwołania do parametrów

    ![Parametry funkcji IntelliSense Visual Studio Code rozszerzenia narzędzi Menedżer zasobów](./media/use-vs-code-to-create-template/resource-manager-tools-extension-intellisense-parameters.png)

- Odwołania do zmiennych

    ![Zmienne funkcji IntelliSense Visual Studio Code rozszerzenia narzędzi Menedżer zasobów](./media/use-vs-code-to-create-template/resource-manager-tools-extension-intellisense-variables.png)

- Właściwości: Resources ()

    ![Funkcje IntelliSense Visual Studio Code rozszerzenia narzędzi Menedżer zasobów Tools](./media/use-vs-code-to-create-template/resource-manager-tools-extension-intellisense-resourcegroup.png)

Ponadto technologia IntelliSense działa również z właściwościami subskrypcji () i właściwościami odwołań do zmiennych, które są obiektami.

### <a name="signature-help-for-function-parameters"></a>Pomoc dotycząca podpisu dla parametrów funkcji

Po umieszczeniu wskaźnika myszy w nazwach funkcji rozszerzenie pokazuje pomoc dotyczącą podpisu parametrów funkcji.

![Funkcja podpisu Visual Studio Code narzędzi Menedżer zasobów Tools](./media/use-vs-code-to-create-template/resource-manager-tools-extension-signature-function.png)

### <a name="go-to-definition-for-variable-and-parameter-references"></a>Przejdź do definicji dla odwołań do zmiennych i parametrów

Możesz przejść do definicji za pomocą **kombinacji klawiszy CTRL + kliknięcia**lub menu kontekstowego, jak pokazano na zrzucie ekranu: ![Visual Studio Code Menedżer zasobów narzędzia rozszerzenia przejdź do definicji](./media/use-vs-code-to-create-template/resource-manager-tools-extension-context-menu.png)

Możesz otworzyć definicję po stronie przy użyciu **kombinacji klawiszy Ctrl + Alt i kliknięcia**.

### <a name="peek-for-variable-and-parameter-definitions"></a>Wgląd w definicje zmiennych i parametrów

Aby otworzyć okno edytora z podglądem, użyj menu kontekstowego, jak pokazano na poprzednim zrzucie ekranu.

Poniższy zrzut ekranu przedstawia Edytor wglądu:

![Edytor wglądu do rozszerzeń narzędzi Menedżer zasobów Visual Studio Code](./media/use-vs-code-to-create-template/resource-manager-tools-extension-peek-editor.png)

### <a name="find-all-references-for-variables-and-parameters"></a>Znajdź wszystkie odwołania do zmiennych i parametrów

Aby znaleźć wszystkie odwołania, użyj menu kontekstowego, jak pokazano na poprzednim zrzucie ekranu.

Poniższy zrzut ekranu pokazuje, jak są wyróżnione odwołania:

![Rozszerzenia narzędzi Visual Studio Code Menedżer zasobów Znajdź wszystkie odwołania](./media/use-vs-code-to-create-template/resource-manager-tools-extension-find-all-references.png)

### <a name="rename-all-references-for-variables-and-parameters"></a>Zmień nazwę wszystkich odwołań dla zmiennych i parametrów

Aby zmienić nazwy wszystkich odwołań dla zmiennych i parametrów, użyj menu kontekstowego, jak pokazano na poprzednim zrzucie ekranu.

### <a name="hover-for-parameter-description"></a>Umieść kursor w opisie parametru

![Definicja aktywowania rozszerzeń narzędzi Visual Studio Code Menedżer zasobów](./media/use-vs-code-to-create-template/resource-manager-tools-extension-hover-parameters.png)

### <a name="brace-matching"></a>Dopasowywanie nawiasów klamrowych

Pasujące nawiasy są wyróżnione zaraz po umieszczeniu kursora w jednym z nich. Po kliknięciu nawiasu klamrowego, pasujący nawias klamrowy zostanie również wyróżniony, jak pokazano na poniższym zrzucie ekranu:

![Dopasowywanie nawiasów klamrowych rozszerzenia Visual Studio Code Menedżer zasobów](./media/use-vs-code-to-create-template/resource-manager-tools-extension-brace-matching.png)

### <a name="show-errors-and-warnings"></a>Pokaż błędy i ostrzeżenia

Błędy identyfikowane przez rozszerzenie obejmują:

- Odwołania do niezdefiniowanych parametrów
- Niezdefiniowane odwołania do zmiennych
- Nierozpoznane nazwy funkcji
- Użycie funkcji Reference () w definicji zmiennej
- Niepoprawna liczba argumentów w funkcjach

Ostrzeżenia obejmują:

- Nieużywane parametry
- Nieużywane zmienne

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej na temat tworzenia szablonów Azure Resource Manager, zobacz [Samouczek: Tworzenie i wdrażanie pierwszego szablonu Azure Resource Manager](template-tutorial-create-first-template.md).
- Aby przejść do przewodnika Szybki Start przy użyciu Visual Studio Code, zobacz [Szybki Start: tworzenie Azure Resource Manager szablonów przy użyciu Visual Studio Code](quickstart-create-templates-use-visual-studio-code.md)
