---
title: Uzyskiwanie dostępu do zasobów za pomocą narzędzi Data Lake
description: Dowiedz się, jak używać narzędzi Azure Data Lake do uzyskiwania dostępu do zasobów Azure Data Lake Analytics.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 02/09/2018
ms.openlocfilehash: d04f108b45070b27c4ff9ed833e8fb77b74cd597
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96754754"
---
# <a name="accessing-resources-with-azure-data-lake-tools"></a>Uzyskiwanie dostępu do zasobów za pomocą narzędzi Azure Data Lake

Możesz uzyskiwać dostęp do zasobów Azure Data Lake Analytics za pomocą poleceń lub akcji narzędzi Azure Data Tools w VS Code łatwo.

## <a name="integrate-with-azure-data-lake-analytics-through-a-command"></a>Integracja z Azure Data Lake Analytics za pomocą polecenia

Możesz uzyskać dostęp do zasobów Azure Data Lake Analytics, aby wyświetlić listę kont, uzyskać dostęp do metadanych i przeglądać zadania analizy.

### <a name="to-list-the-azure-data-lake-analytics-accounts-under-your-azure-subscription"></a>Aby wyświetlić listę kont Azure Data Lake Analytics w ramach subskrypcji platformy Azure

1. Wybierz kombinację klawiszy Ctrl + Shift + P, aby otworzyć paletę poleceń.
2. Wprowadź **ADL: list accounts**. Konta są wyświetlane w okienku **dane wyjściowe** .

### <a name="to-access-azure-data-lake-analytics-metadata"></a>Aby uzyskać dostęp do metadanych Azure Data Lake Analytics

1. Wybierz kombinację klawiszy Ctrl + Shift + P, a następnie wprowadź **ADL: tabele list**.
2. Wybierz jedno z kont Data Lake Analytics.
3. Wybierz jedną z Data Lake Analytics baz danych.
4. Wybierz jeden z schematów. Zostanie wyświetlona lista tabel.

### <a name="to-view-azure-data-lake-analytics-jobs"></a>Aby wyświetlić Azure Data Lake Analytics zadania

1. Otwórz paletę poleceń (Ctrl + Shift + P) i wybierz pozycję **ADL: show Jobs**.
2. Wybierz Data Lake Analytics lub konto lokalne.
3. Poczekaj, aż lista zadań zostanie wyświetlona dla konta.
4. Wybierz zadanie z listy zadań. Narzędzia Data Lake otwierają widok zadania w okienku po prawej stronie i wyświetlają pewne informacje w danych wyjściowych VS Code.

    ![Lista zadań](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-show-job.png)

## <a name="integrate-with-azure-data-lake-store-through-a-command"></a>Integracja z Azure Data Lake Store za pomocą polecenia

Polecenia powiązane z Azure Data Lake Store można użyć do:

- [Przeglądaj zasoby Azure Data Lake Store](#list-the-storage-path)
- [Podgląd pliku Azure Data Lake Store](#preview-the-storage-file)
- Przekaż plik bezpośrednio do Azure Data Lake Store w VS Code
- Pobierz plik bezpośrednio z Azure Data Lake Store w VS Code

### <a name="list-the-storage-path"></a>Wyświetlanie listy ścieżek magazynu

### <a name="to-list-the-storage-path-through-the-command-palette"></a>Aby wyświetlić listę ścieżki magazynu za pomocą palety poleceń

1. Kliknij prawym przyciskiem myszy Edytor skryptów i wybierz pozycję **ADL: ścieżka listy**.
2. Wybierz folder z listy lub wybierz pozycję **wprowadź ścieżkę** lub **Przejdź do lokalizacji głównej**. (Korzystamy z przykładu **wprowadź ścieżkę** ).
3. Wybierz konto Data Lake Analytics.
4. Przejdź do lokalizacji ścieżki folderu przechowywania lub wprowadź ją (na przykład/Output/).  

Paleta poleceń wyświetla informacje o ścieżce na podstawie Twoich wpisów.

![Wyniki ścieżki magazynu](./media/data-lake-analytics-data-lake-tools-for-vscode/list-storage-path.png)

Wygodniejszym sposobem wyświetlania ścieżki względnej jest użycie menu skrótów.

### <a name="to-list-the-storage-path-through-the-shortcut-menu"></a>Aby wyświetlić listę ścieżki magazynu za pomocą menu skrótów

Kliknij prawym przyciskiem myszy ciąg ścieżki i wybierz pozycję **ścieżka listy**.

!["Ścieżka listy" w menu skrótów](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-right-click-path.png)

### <a name="preview-the-storage-file"></a>Podgląd pliku magazynu

1. Kliknij prawym przyciskiem myszy Edytor skryptów i wybierz pozycję **ADL: Podgląd pliku**.
2. Wybierz konto Data Lake Analytics.
3. Wprowadź ścieżkę pliku usługi Azure Storage (na przykład/Output/SearchLog.txt).

Plik zostanie otwarty w VS Code.

![Kroki i wyniki dotyczące podglądu pliku magazynu](./media/data-lake-analytics-data-lake-tools-for-vscode/preview-storage-file.png)

Innym sposobem na podgląd pliku jest użycie menu skrótów dla pełnej ścieżki pliku lub ścieżki względnej pliku w Edytorze skryptów.

### <a name="upload-a-file-or-folder"></a>Przekaż plik lub folder

1. Kliknij prawym przyciskiem myszy Edytor skryptów i wybierz polecenie **Przekaż plik** lub **Przekaż folder**.
2. Wybierz jeden plik lub wiele plików w przypadku wybrania opcji **Przekaż plik** lub wybierz cały folder w przypadku wybrania opcji **Przekaż folder**. Następnie wybierz przycisk **Upload** (Przekaż).
3. Wybierz folder Storage na liście lub wybierz pozycję **wprowadź ścieżkę** lub **Przejdź do lokalizacji głównej**. (Korzystamy z przykładu **wprowadź ścieżkę** ).
4. Wybierz konto Data Lake Analytics.
5. Przejdź do lokalizacji ścieżki folderu przechowywania lub wprowadź ją (na przykład/Output/).
6. Wybierz pozycję **Wybierz bieżący folder** , aby określić miejsce docelowe przekazywania.

![Kroki i wyniki przekazywania pliku lub folderu](./media/data-lake-analytics-data-lake-tools-for-vscode/upload-file.png)

Innym sposobem przekazywania plików do magazynu jest użycie menu skrótów dla pełnej ścieżki pliku lub ścieżki względnej pliku w Edytorze skryptów.

Możesz [monitorować stan przekazywania](#check-storage-tasks-status).

### <a name="download-a-file"></a>Pobieranie pliku

Plik można pobrać przy użyciu polecenia **ADL: Download File** lub **ADL: Download File (Zaawansowane)**.

### <a name="to-download-a-file-through-the-adl-download-file-advanced-command"></a>Aby pobrać plik za pomocą polecenia ADL: Download File (Zaawansowane)

1. Kliknij prawym przyciskiem myszy Edytor skryptów, a następnie wybierz pozycję **Pobierz plik (Zaawansowane)**.
2. VS Code Wyświetla plik JSON. Można wprowadzać ścieżki plików i pobierać wiele plików jednocześnie. Instrukcje są wyświetlane w oknie **dane wyjściowe** . Aby rozpocząć pobieranie pliku lub plików, Zapisz (Ctrl + S) plik JSON.

    ![Plik JSON ze ścieżkami do pobierania plików](./media/data-lake-analytics-data-lake-tools-for-vscode/download-multi-files.png)

W oknie **danych wyjściowych** zostanie wyświetlony stan pobierania pliku.

![Okno danych wyjściowych ze stanem pobierania](./media/data-lake-analytics-data-lake-tools-for-vscode/download-multi-file-result.png)

Możesz [monitorować stan pobierania](#check-storage-tasks-status).

### <a name="to-download-a-file-through-the-adl-download-file-command"></a>Aby pobrać plik za pomocą polecenia ADL: Download File

1. Kliknij prawym przyciskiem myszy Edytor skryptów, wybierz polecenie **Pobierz plik**, a następnie wybierz folder docelowy z okna dialogowego **Wybierz folder** .

1. Wybierz folder z listy lub wybierz pozycję **wprowadź ścieżkę** lub **Przejdź do lokalizacji głównej**. (Korzystamy z przykładu **wprowadź ścieżkę** ).

1. Wybierz konto Data Lake Analytics.

1. Przejdź do lokalizacji lub wprowadź ścieżkę folderu przechowywania (na przykład/Output/), a następnie wybierz plik do pobrania.

![Kroki i wyniki pobierania pliku](./media/data-lake-analytics-data-lake-tools-for-vscode/download-file.png)

Innym sposobem pobierania plików magazynu jest użycie menu skrótów dla pełnej ścieżki pliku lub ścieżki względnej pliku w Edytorze skryptów.

Możesz [monitorować stan pobierania](#check-storage-tasks-status).

### <a name="check-storage-tasks-status"></a>Sprawdź stan zadań magazynu

Stan przekazywania i pobierania pojawia się na pasku stanu. Wybierz pasek stanu, a następnie na karcie **dane wyjściowe** zostanie wyświetlony stan.

![Pasek stanu i szczegóły danych wyjściowych](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-status.png)

## <a name="integrate-with-azure-data-lake-analytics-from-the-explorer"></a>Integracja z programem Azure Data Lake Analytics z poziomu Eksploratora

Po zalogowaniu wszystkie subskrypcje Twojego konta platformy Azure są wyświetlane w okienku po lewej stronie w obszarze **Azure datalake**.

![Eksplorator Data Lake](./media/data-lake-analytics-data-lake-tools-for-vscode/datalake-explorer.png)

### <a name="data-lake-analytics-metadata-navigation"></a>Data Lake Analytics Nawigacja w metadanych

Rozwiń swoją subskrypcję platformy Azure. W węźle **bazy danych u-SQL** można przeglądać bazę danych u-SQL i przeglądać foldery, takie jak **schematy**, **poświadczenia**, **zestawy**, **tabele** i **indeksy**.

### <a name="data-lake-analytics-metadata-entity-management"></a>Data Lake Analytics zarządzanie jednostką metadanych

Rozwiń węzeł **bazy danych U-SQL**. Aby utworzyć bazę danych, schemat, tabelę, typ tabeli, indeks lub statystykę, kliknij prawym przyciskiem myszy odpowiedni węzeł, a następnie wybierz polecenie **skrypt do utworzenia** w menu skrótów. Na otwartej stronie skrypt Edytuj skrypt zgodnie z potrzebami. Następnie Prześlij zadanie, klikając je prawym przyciskiem myszy i wybierając pozycję **ADL: Prześlij zadanie**.

Po zakończeniu tworzenia elementu kliknij prawym przyciskiem myszy węzeł, a następnie wybierz polecenie **Odśwież** , aby wyświetlić element. Możesz również usunąć element, klikając go prawym przyciskiem myszy, a następnie wybierając polecenie **Usuń**.

![Polecenie "skrypt do utworzenia" w menu skrótów w Eksploratorze Data Lake](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-code-explorer-script-create.png)

![Strona skryptu dla nowego elementu](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-code-explorer-script-create-snippet.png)

### <a name="data-lake-analytics-assembly-registration"></a>Rejestracja zestawu Data Lake Analytics

Możesz zarejestrować zestaw w odpowiedniej bazie danych, klikając prawym przyciskiem myszy węzeł **zestawy** , a następnie wybierając polecenie **zarejestruj zestaw**.

![Polecenie "Zarejestruj zestaw" w menu skrótów węzła zestawy](./media/data-lake-analytics-data-lake-tools-for-vscode/datalake-explorer-register-assembly.png)

## <a name="integrate-with-azure-data-lake-store-from-the-explorer"></a>Integracja z programem Azure Data Lake Store z poziomu Eksploratora

Przejdź do **Data Lake Store**:

- Możesz kliknąć prawym przyciskiem myszy węzeł folderu, a następnie użyć polecenia **Odśwież**, **Usuń**, **Przekaż**, **Przekaż folder**, **Kopiuj ścieżkę względną** i **Kopiuj pełną ścieżkę** w menu skrótów.

   ![Polecenia menu skrótów dla węzła folderu w Eksploratorze Data Lake](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-account-folder-menu.png)

- Możesz kliknąć prawym przyciskiem myszy węzeł pliku, a następnie użyć okna **Podgląd**, **Pobierz**, **Usuń**, **Utwórz wyodrębnianie skryptu** (dostępne tylko dla plików CSV, TSV i txt), **skopiuj ścieżkę względną** i **Skopiuj polecenia pełna ścieżka** w menu skrótów.

   ![Polecenia menu skrótów dla węzła plik w Eksploratorze Data Lake](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-account-extract.png)

## <a name="integrate-with-azure-blob-storage-from-the-explorer"></a>Integracja z usługą Azure Blob Storage z poziomu Eksploratora

Przejdź do magazynu obiektów blob:

- Możesz kliknąć prawym przyciskiem myszy węzeł kontenera obiektów blob, a następnie użyć polecenia **Odśwież**, **Usuń kontener obiektów BLOB** i **Przekaż obiekt BLOB** w menu skrótów.

   ![Polecenia menu skrótów dla węzła kontenera obiektów BLOB w usłudze BLOB Storage](./media/data-lake-analytics-data-lake-tools-for-vscode/blob-storage-blob-container-node.png)

- Możesz kliknąć prawym przyciskiem myszy węzeł folderu, a następnie użyć poleceń **Odśwież** i **Przekaż obiekt BLOB** w menu skrótów.

   ![Polecenia menu skrótów dla węzła folderu w usłudze BLOB Storage](./media/data-lake-analytics-data-lake-tools-for-vscode/blob-storage-folder-node.png)

- Możesz kliknąć prawym przyciskiem myszy węzeł pliku, a następnie użyć okna **Podgląd/edycja**, **Pobierz**, **Usuń**, **Utwórz wyodrębnianie skryptu** (dostępne tylko dla plików CSV, TSV i txt), **skopiuj ścieżkę względną** i **Skopiuj polecenia pełna ścieżka** w menu skrótów.

    ![Polecenia menu skrótów dla węzła pliku w usłudze BLOB Storage](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-from-context-menu-2.png)

## <a name="open-the-data-lake-explorer-in-the-portal"></a>Otwórz Eksploratora Data Lake w portalu

1. Wybierz kombinację klawiszy Ctrl + Shift + P, aby otworzyć paletę poleceń.
2. Wprowadź **otwarte Eksplorator usługi Azure Storage sieci Web** lub kliknij prawym przyciskiem myszy ścieżkę względną lub pełną ścieżkę w Edytorze skryptów, a następnie wybierz polecenie **Otwórz Eksplorator usługi Azure Storage sieci Web**.
3. Wybierz konto Data Lake Analytics.

Narzędzia Data Lake otwierają ścieżkę usługi Azure Storage w Azure Portal. Możesz znaleźć ścieżkę i wyświetlić podgląd pliku z sieci Web.

## <a name="additional-features"></a>Dodatkowe funkcje

Narzędzia Data Lake dla VS Code obsługują następujące funkcje:

- **Autouzupełnianie funkcji IntelliSense**: sugestie pojawiają się w wyskakujących oknach wokół elementów, takich jak słowa kluczowe, metody i zmienne. Różne ikony reprezentują różne typy obiektów:

  - Scala — typ danych
  - Typ danych złożonych
  - Wbudowana UDTs
  - Kolekcje i klasy .NET
  - Wyrażenia języka C#
  - Wbudowane Języki C# UDF, udo i UDAAGs
  - Funkcje języka U-SQL
  - Funkcje okien języka U-SQL

    ![Typy obiektów IntelliSense](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-complete-objects.png)

- **Autouzupełnianie funkcji IntelliSense dla metadanych Data Lake Analytics**: narzędzia Data Lake pobierają informacje o metadanych Data Lake Analytics lokalnie. Funkcja IntelliSense automatycznie wypełnia obiekty w metadanych Data Lake Analytics. Te obiekty obejmują bazę danych, schemat, tabelę, widok, funkcję z wartościami przechowywanymi w tabeli, procedury i zestawy języka C#.

  ![Metadane funkcji IntelliSense](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-complete-metastore.png)

- **Znacznik błędu IntelliSense**: narzędzia Data Lake podkreślają edycję błędów dla języka U-SQL i C#.
- **Najważniejsze informacje o składni**: narzędzia Data Lake używają kolorów do rozróżniania elementów, takich jak zmienne, słowa kluczowe, typy danych i funkcje.

    ![Składnia z różnymi kolorami](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-syntax-highlights.png)

> [!NOTE]
> Zalecamy uaktualnienie do Azure Data Lake Tools for Visual Studio w wersji 2.3.3000.4 lub nowszej. Poprzednie wersje są niedostępne do pobrania i przestarzałe.  

## <a name="next-steps"></a>Następne kroki

- [Opracowywanie języka U-SQL przy użyciu języków Python, R i C dla Azure Data Lake Analytics w VS Code](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md)
- [Lokalne uruchamianie skryptu U-SQL i debugowanie lokalne przy użyciu Visual Studio Code](data-lake-tools-for-vscode-local-run-and-debug.md)
- [Samouczek: wprowadzenie do Azure Data Lake Analytics](data-lake-analytics-get-started-portal.md)
- [Samouczek: Tworzenie skryptów U-SQL przy użyciu narzędzi Data Lake Tools for Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)