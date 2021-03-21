---
title: 'Samouczek JavaScript: Dodawanie wyszukiwania do aplikacji sieci Web'
titleSuffix: Azure Cognitive Search
description: Tworzenie indeksu i importowanie danych CSV do indeksu wyszukiwania za pomocą języka JavaScript przy użyciu zestawu SDK npm @azure/search-documents .
manager: nitinme
author: diberry
ms.author: diberry
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 03/18/2021
ms.custom: devx-track-js
ms.devlang: javascript
ms.openlocfilehash: 0fd28262f4a4b852386fa354037e69c5097109c5
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "104726979"
---
# <a name="2---create-and-load-search-index-with-javascript"></a>2 — Tworzenie i ładowanie indeksu wyszukiwania przy użyciu języka JavaScript

Kontynuuj, aby skompilować witrynę sieci Web z obsługą wyszukiwania:
* Tworzenie zasobu wyszukiwania przy użyciu rozszerzenia VS Code
* Tworzenie nowego indeksu i importowanie danych za pomocą języka JavaScript przy użyciu skryptu przykładowego i zestawu Azure SDK [@azure/search-documents](https://www.npmjs.com/package/@azure/search-documents) .

## <a name="create-an-azure-search-resource"></a>Tworzenie zasobu Azure Search 

Utwórz nowy zasób wyszukiwania za pomocą rozszerzenia [Wyszukiwanie poznawcze platformy Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurecognitivesearch) dla Visual Studio Code.

1. W Visual Studio Code Otwórz [Pasek aktywności](https://code.visualstudio.com/docs/getstarted/userinterface), a następnie wybierz ikonę platformy Azure. 

1. Na pasku bocznym **kliknij prawym przyciskiem myszy subskrypcję platformy Azure** w obszarze, `Azure: Cognitive Search` a następnie wybierz pozycję **Utwórz nową usługę wyszukiwania**.

    :::image type="content" source="./media/tutorial-javascript-create-load-index/visual-studio-code-create-search-resource.png" alt-text="Na pasku bocznym kliknij prawym przyciskiem myszy subskrypcję platformy Azure w obszarze * * Azure: Wyszukiwanie poznawcze * * i wybierz pozycję * * Utwórz nową usługę wyszukiwania * *.":::

1. Postępuj zgodnie z monitami, aby podać następujące informacje:

    |Monit|Enter|
    |--|--|
    |Wprowadź globalnie unikatową nazwę dla nowego Search Service.|**Zapamiętaj tę nazwę**. Ta nazwa zasobu jest częścią punktu końcowego zasobu.|
    |Wybierz grupę zasobów dla nowych zasobów|Użyj grupy zasobów utworzonej dla tego samouczka.|
    |Wybierz jednostkę SKU dla usługi wyszukiwania.|Wybierz opcję **bezpłatnie** dla tego samouczka. Po utworzeniu usługi nie można zmienić warstwy cenowej jednostki SKU.|
    |Wybierz lokalizację dla nowych zasobów.|Wybierz region w pobliżu.|

1. Po wykonaniu tych czynności zostanie utworzony nowy zasób wyszukiwania. 

## <a name="get-your-search-resource-admin-key"></a>Pobierz klucz administratora zasobów wyszukiwania

Pobierz klucz administratora zasobów wyszukiwania przy użyciu rozszerzenia Visual Studio Code. 

1. W Visual Studio Code na pasku bocznym kliknij prawym przyciskiem myszy zasób wyszukiwania i wybierz polecenie **Kopiuj klucz administratora**.

    :::image type="content" source="./media/tutorial-javascript-create-load-index/visual-studio-code-copy-admin-key.png" alt-text="Na pasku bocznym kliknij prawym przyciskiem myszy zasób wyszukiwania i wybierz pozycję * * skopiuj klucz administracyjny * *.":::

1. Zachowaj ten klucz administratora, który będzie potrzebny w [dalszej części](#prepare-the-bulk-import-script-for-search). 

## <a name="prepare-the-bulk-import-script-for-search"></a>Przygotuj skrypt importu zbiorczego do wyszukiwania

Skrypt używa zestawu Azure SDK dla Wyszukiwanie poznawcze:

* [Pakiet npm @azure/search-documents](https://www.npmjs.com/package/@azure/search-documents)
* [Dokumentacja referencyjna](/javascript/api/overview/azure/search-documents-readme)

1. W Visual Studio Code Otwórz `bulk_insert_books.js` plik w podkatalogu,  `search-web/bulk-insert` Zastąp następujące zmienne własnymi wartościami, aby uwierzytelnić się za pomocą zestawu Azure Search SDK:

    * TWOJE-SEARCH-RESOURCE-NAME
    * KLUCZ-SEARCH-ADMIN-KEY

    :::code language="javascript" source="~/azure-search-javascript-samples/search-website/bulk-insert/bulk_insert_books.js" highlight="16,17" :::

1. Otwórz zintegrowany terminal w programie Visual Studio dla podkatalogu projektu, `search-web/bulk-insert` a następnie uruchom następujące polecenie, aby zainstalować zależności. 

    ```bash
    npm install 
    ```

## <a name="run-the-bulk-import-script-for-search"></a>Uruchom skrypt importu zbiorczego na potrzeby wyszukiwania

1. Kontynuuj używanie zintegrowanego terminalu w programie Visual Studio dla podkatalogu projektu, `search-web/bulk-insert` Aby uruchomić następujące polecenie bash w celu uruchomienia `bulk_insert_books.js` skryptu:

    ```javascript
    npm start
    ```

1. Po uruchomieniu kodu konsola wyświetla postęp. 
1. Po zakończeniu przekazywania ostatnią instrukcją wydrukowaną do konsoli jest "gotowe".

## <a name="review-the-new-search-index"></a>Przejrzyj nowy indeks wyszukiwania

Po zakończeniu przekazywania indeks wyszukiwania jest gotowy do użycia. Przejrzyj nowy indeks.

1. W Visual Studio Code Otwórz rozszerzenie Azure Wyszukiwanie poznawcze i wybierz zasób wyszukiwania.  

    :::image type="content" source="media/tutorial-javascript-create-load-index/visual-studio-code-search-extension-view-resource.png" alt-text="W Visual Studio Code Otwórz rozszerzenie Azure Wyszukiwanie poznawcze i Otwórz zasób wyszukiwania.":::

1. Rozwiń węzeł indeksy, następnie dokumenty, a następnie `good-books` Wybierz dokument, aby wyświetlić wszystkie dane specyficzne dla dokumentu.
 
    :::image type="content" source="media/tutorial-javascript-create-load-index/visual-studio-code-search-extension-view-docs.png" lightbox="media/tutorial-javascript-create-load-index/visual-studio-code-search-extension-view-docs.png" alt-text="Rozwiń węzeł indeksy, a następnie pozycję &quot;dobra książka&quot;, a następnie wybierz dokument.":::

## <a name="copy-your-search-resource-name"></a>Kopiuj nazwę zasobu wyszukiwania

Zanotuj **nazwę zasobu wyszukiwania**. Będzie to konieczne, aby połączyć aplikację funkcji platformy Azure z zasobem wyszukiwania. 

> [!CAUTION]
> Użytkownik może być skłonny do korzystania z klucza administratora wyszukiwania w funkcji platformy Azure, która nie jest niższa od zasady najniższych uprawnień. Funkcja platformy Azure będzie używać klucza zapytania do zapewnienia zgodności z najniższymi uprawnieniami. 

## <a name="next-steps"></a>Następne kroki

[Wdrażanie statycznej aplikacji sieci Web](tutorial-javascript-deploy-static-web-app.md)