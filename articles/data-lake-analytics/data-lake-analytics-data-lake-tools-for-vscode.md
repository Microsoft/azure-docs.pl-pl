---
title: Korzystanie z narzędzi Azure Data Lake Tools for Visual Studio Code
description: Dowiedz się, jak używać kodu Azure Data Lake Tools for Visual Studio, aby tworzyć, testować i uruchamiać skrypty U-SQL.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 02/09/2018
ms.openlocfilehash: e7da5454581e0e414dc832d4fcec50277f3b7f40
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96019059"
---
# <a name="use-azure-data-lake-tools-for-visual-studio-code"></a>Korzystanie z narzędzi Azure Data Lake Tools for Visual Studio Code

W tym artykule dowiesz się, jak za pomocą kodu Azure Data Lake Tools for Visual Studio (VS Code) tworzyć, testować i uruchamiać skrypty U-SQL. Informacje są również zawarte w następującym wideo:

[![Odtwarzacz wideo: narzędzia Azure Data Lake dla VS Code](media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-video.png)](https://channel9.msdn.com/Series/AzureDataLake/Azure-Data-Lake-Tools-for-VSCode?term=ADL%20Tools%20for%20VSCode")

## <a name="prerequisites"></a>Wymagania wstępne

Azure Data Lake Tools for VS Code obsługuje systemy Windows, Linux i macOS. Lokalne uruchamianie skryptu U-SQL i lokalne debugowanie działa tylko w systemie Windows.

- [Visual Studio Code](https://www.visualstudio.com/products/code-vs.aspx)

W przypadku systemów MacOS i Linux:

- [Zestaw .NET Core SDK 2,0](https://www.microsoft.com/net/download/core)
- [Mono 5.2. x](https://www.mono-project.com/download/)

## <a name="install-azure-data-lake-tools"></a>Zainstaluj narzędzia Azure Data Lake

Po zainstalowaniu wymagań wstępnych można zainstalować Azure Data Lake narzędzia dla VS Code.

### <a name="to-install-azure-data-lake-tools"></a>Aby zainstalować narzędzia Azure Data Lake

1. Otwórz program Visual Studio Code.
2. W lewym okienku wybierz pozycję **rozszerzenia** . W polu wyszukiwania wprowadź **Azure Data Lake narzędzia** .
3. Wybierz pozycję **Zainstaluj** obok pozycji **narzędzia Azure Data Lake**.

   ![Opcje instalowania narzędzi Data Lake](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extensions.png)

   Po kilku sekundach przycisk **Instaluj** zostanie zmieniony na **Załaduj ponownie**.
4. Wybierz pozycję **Załaduj ponownie** , aby aktywować rozszerzenie **narzędzi Azure Data Lake** .
5. Wybierz pozycję **Załaduj ponownie okno** , aby potwierdzić. W okienku **rozszerzenia** można zobaczyć **Azure Data Lake narzędzia** .

## <a name="activate-azure-data-lake-tools"></a>Aktywuj narzędzia Azure Data Lake

Utwórz plik. usql lub Otwórz istniejący plik. usql, aby aktywować rozszerzenie.

## <a name="work-with-u-sql"></a>Korzystanie z języka U-SQL

Aby można było korzystać z języka U-SQL, należy otworzyć plik U-SQL lub folder.

### <a name="to-open-the-sample-script"></a>Aby otworzyć przykładowy skrypt

Otwórz paletę poleceń (Ctrl + Shift + P) i wprowadź **ADL: Otwórz przykładowy skrypt**. Otwiera inne wystąpienie tego przykładu. Możesz również edytować, skonfigurować i przesłać skrypt w tym wystąpieniu.

### <a name="to-open-a-folder-for-your-u-sql-project"></a>Aby otworzyć folder dla projektu U-SQL

1. W obszarze Visual Studio Code wybierz menu **plik** , a następnie wybierz polecenie **Otwórz folder**.
2. Określ folder, a następnie wybierz pozycję **Wybierz folder**.
3. Wybierz menu **plik** , a następnie wybierz polecenie **Nowy**. Do projektu zostanie dodany plik bez tytułu-1.
4. Wprowadź następujący kod w pliku bez tytułu — 1:

   ```usql
   @departments  =
       SELECT * FROM
           (VALUES
               (31,    "Sales"),
               (33,    "Engineering"),
               (34,    "Clerical"),
               (35,    "Marketing")
           ) AS
                 D( DepID, DepName );
   ```

   Dane wyjściowe @departments     do "/Output/departments.csv" przy użyciu Outputters.Csv ();

    Skrypt tworzy plik departments.csv z danymi zawartymi w folderze/Output.

5. Zapisz plik jako **myUSQL. usql** w otwartym folderze.

### <a name="to-compile-a-u-sql-script"></a>Aby skompilować skrypt U-SQL

1. Wybierz kombinację klawiszy Ctrl + Shift + P, aby otworzyć paletę poleceń.
2. Wprowadź **ADL: Skompiluj skrypt**. Wyniki kompilacji są wyświetlane w oknie **dane wyjściowe** . Możesz również kliknąć prawym przyciskiem myszy plik skryptu, a następnie wybrać pozycję **ADL: Kompiluj skrypt** , aby skompilować zadanie U-SQL. Wynik kompilacji zostanie wyświetlony w okienku **dane wyjściowe** .

### <a name="to-submit-a-u-sql-script"></a>Aby przesłać skrypt U-SQL

1. Wybierz kombinację klawiszy Ctrl + Shift + P, aby otworzyć paletę poleceń.
2. Wprowadź **ADL: Prześlij zadanie**. Możesz również kliknąć prawym przyciskiem myszy plik skryptu, a następnie wybrać pozycję **ADL: Prześlij zadanie**.

Po przesłaniu zadania U-SQL dzienniki przesyłania są wyświetlane w oknie **dane wyjściowe** w vs Code. Widok zadania zostanie wyświetlony w okienku po prawej stronie. Jeśli przesyłanie zakończy się pomyślnie, adres URL zadania zostanie wyświetlony. Możesz otworzyć adres URL zadania w przeglądarce sieci Web, aby śledzić stan zadania w czasie rzeczywistym.

Na karcie **Podsumowanie** widoku zadania można zobaczyć szczegóły zadania. Główne funkcje obejmują ponowne przesyłanie skryptu, duplikowanie skryptu i otwieranie w portalu. Na karcie **dane** widoku zadania możesz odwoływać się do plików wejściowych, plików wyjściowych i plików zasobów. Pliki można pobrać na komputer lokalny.

![Karta Podsumowanie w widoku zadania](./media/data-lake-analytics-data-lake-tools-for-vscode/job-view-summary.png)

![Karta dane w widoku zadania](./media/data-lake-analytics-data-lake-tools-for-vscode/job-view-data.png)

### <a name="to-set-the-default-context"></a>Aby ustawić kontekst domyślny

Można ustawić kontekst domyślny, aby zastosować to ustawienie do wszystkich plików skryptów, jeśli nie ustawisz osobno parametrów dla plików.

1. Wybierz kombinację klawiszy Ctrl + Shift + P, aby otworzyć paletę poleceń.
2. Wprowadź **ADL: Ustaw kontekst domyślny**. Lub kliknij prawym przyciskiem myszy Edytor skryptów i wybierz pozycję **ADL: Ustaw kontekst domyślny**.
3. Wybierz odpowiednie konto, bazę danych i schemat. To ustawienie zostanie zapisane w xxx_settings.jsw pliku konfiguracyjnym.

   ![Konto, baza danych i zestaw schematów jako kontekst domyślny](./media/data-lake-analytics-data-lake-tools-for-vscode/default-context-sequence.png)

### <a name="to-set-script-parameters"></a>Aby ustawić parametry skryptu

1. Wybierz kombinację klawiszy Ctrl + Shift + P, aby otworzyć paletę poleceń.
2. Wprowadź **ADL: ustaw parametry skryptu**.
3. xxx_settings.jsw pliku jest otwarty z następującymi właściwościami:

   - **konto**: konto Azure Data Lake Analytics w ramach subskrypcji platformy Azure, które jest konieczne do kompilowania i uruchamiania zadań U-SQL. Przed skompilowaniem i uruchomieniem zadań U-SQL należy skonfigurować konto komputera.
   - **baza danych**: baza danych programu w ramach konta. Wartość domyślna to **Master**.
   - **schemat**: schemat w bazie danych. Wartość domyślna to **dbo**.
   - **optionalSettings**:
        - **priorytet**: zakres priorytetów to od 1 do 1000, z 1 jako najwyższy priorytet. Wartość domyślna to **1000**.
        - **degreeOfParallelism**: zakres równoległy to od 1 do 150. Wartość domyślna to maksymalna liczba równoległości dozwolona na koncie Azure Data Lake Analytics.

   ![Zawartość pliku JSON](./media/data-lake-analytics-data-lake-tools-for-vscode/default-context-setting.png)

> [!NOTE]
> Po zapisaniu konfiguracji informacje o koncie, bazie danych i schemacie są wyświetlane na pasku stanu w lewym dolnym rogu odpowiedniego pliku. usql, jeśli nie skonfigurowano domyślnego kontekstu.

### <a name="to-set-git-ignore"></a>Aby ustawić git Ignore

1. Wybierz kombinację klawiszy Ctrl + Shift + P, aby otworzyć paletę poleceń.
2. Wprowadź **ADL: ustaw git Ignoruj**.

   - Jeśli nie masz pliku **. gitIgnore** w folderze roboczym vs Code, w folderze zostanie utworzony plik o nazwie **. gitIgnore** . Cztery elementy (**usqlCodeBehindReference**, **usqlCodeBehindGenerated**, **. cache**, **obj**) są domyślnie dodawane do pliku. W razie konieczności można wprowadzić więcej aktualizacji.
   - Jeśli masz już plik **. gitIgnore** w folderze roboczym vs Code, narzędzie dodaje cztery elementy (**usqlCodeBehindReference**, **usqlCodeBehindGenerated**, **. cache**, **obj**) w pliku **. gitIgnore** , jeśli cztery elementy nie zostały uwzględnione w pliku.

   ![Elementy w pliku. gitIgnore](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-gitignore.png)

## <a name="work-with-code-behind-files-c-sharp-python-and-r"></a>Praca z plikami związanymi z kodem: Sharp, Python i R

Narzędzia Azure Data Lake obsługują wiele niestandardowych kodów. Aby uzyskać instrukcje, zobacz temat [programowanie U-SQL przy użyciu języka Python, R i C Sharp dla Azure Data Lake Analytics w vs Code](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md).

## <a name="work-with-assemblies"></a>Pracuj z zestawami

Aby uzyskać informacje na temat tworzenia zestawów, zobacz [Tworzenie zestawów U-SQL dla zadań Azure Data Lake Analytics]().

Za pomocą narzędzi Data Lake można zarejestrować niestandardowe zestawy kodu w katalogu Data Lake Analytics.

### <a name="to-register-an-assembly"></a>Aby zarejestrować zestaw

Można zarejestrować zestaw za pomocą polecenia **ADL: Register Assembly** lub **ADL: Register Assembly (Advanced)** .

### <a name="to-register-through-the-adl-register-assembly-command"></a>Aby zarejestrować się w ADL: Register Assembly — polecenie

1. Wybierz kombinację klawiszy Ctrl + Shift + P, aby otworzyć paletę poleceń.
2. Wprowadź **ADL: Register Assembly**.
3. Określ ścieżkę zestawu lokalnego.
4. Wybierz konto Data Lake Analytics.
5. Wybierz bazę danych.

Portal zostanie otwarty w przeglądarce i zostanie wyświetlony proces rejestracji zestawu.  

Wygodniejszym sposobem wyzwalania polecenia **ADL: Register Assembly** jest kliknięcie prawym przyciskiem myszy pliku DLL w Eksploratorze plików.

### <a name="to-register-through-the-adl-register-assembly-advanced-command"></a>Aby zarejestrować się w ADL: Zarejestruj zestaw (Zaawansowane), polecenie

1. Wybierz kombinację klawiszy Ctrl + Shift + P, aby otworzyć paletę poleceń.
2. Wprowadź **ADL: Register Assembly (Advanced)**.
3. Określ ścieżkę zestawu lokalnego.
4. Zostanie wyświetlony plik JSON. Przejrzyj i edytuj zależności zestawu i parametry zasobów, w razie konieczności. Instrukcje są wyświetlane w oknie **dane wyjściowe** . Aby przejoć do rejestracji zestawu, Zapisz (Ctrl + S) plik JSON.

   ![Plik JSON z zależnościami zestawu i parametrami zasobów](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-register-assembly-advance.png)

>[!NOTE]
>
>- Narzędzia Azure Data Lake automatycznie wykrywają, czy biblioteka DLL ma jakiekolwiek zależności zestawu. Zależności są wyświetlane w pliku JSON po ich wykryciu.
>- Zasoby biblioteki DLL (na przykład. txt,. png i. csv) można przekazać w ramach rejestracji zestawu.

Innym sposobem na wyzwolenie polecenia **ADL: Register Assembly (Advanced)** jest kliknięcie prawym przyciskiem myszy pliku DLL w Eksploratorze plików.

Poniższy kod U-SQL demonstruje sposób wywoływania zestawu. W przykładzie nazwą zestawu jest *test*.

```usql
REFERENCE ASSEMBLY [test];
@a =
    EXTRACT
        Iid int,
    Starts DateTime,
    Region string,
    Query string,
    DwellTime int,
    Results string,
    ClickedUrls string
    FROM @"Sample/SearchLog.txt"
    USING Extractors.Tsv();
@d =
    SELECT DISTINCT Region
    FROM @a;
@d1 =
    PROCESS @d
    PRODUCE
        Region string,
    Mkt string
    USING new USQLApplication_codebehind.MyProcessor();
OUTPUT @d1
    TO @"Sample/SearchLogtest.txt"
    USING Outputters.Tsv();
```

## <a name="use-u-sql-local-run-and-local-debug-for-windows-users"></a>Używanie lokalnego uruchomienia języka U-SQL i lokalnego debugowania dla użytkowników systemu Windows

Lokalne uruchomienie języka U-SQL umożliwia testowanie lokalnych danych i sprawdzanie poprawności skryptu lokalnie przed opublikowaniem kodu w Data Lake Analytics. Aby wykonać następujące zadania przed przesłaniem kodu do Data Lake Analytics, można użyć funkcji debugowania lokalnego:

- Debugowanie kodu w języku C#.
- Wejdź na kod.
- Sprawdź poprawność skryptu lokalnie.

Lokalna funkcja debugowania run działa tylko w środowiskach systemu Windows i nie jest obsługiwana w systemach operacyjnych opartych na systemie macOS i Linux.

Aby uzyskać instrukcje dotyczące uruchamiania lokalnego i debugowania lokalnego, zobacz skrypt [U-SQL Local Run i Local Debug z Visual Studio Code](data-lake-tools-for-vscode-local-run-and-debug.md).

## <a name="connect-to-azure"></a>Nawiązywanie połączenia z usługą Azure

Aby można było kompilować i uruchamiać skrypty U-SQL w Data Lake Analytics, należy połączyć się z kontem platformy Azure.

<a id="sign-in-by-command"></a>

### <a name="to-connect-to-azure-by-using-a-command"></a>Aby nawiązać połączenie z platformą Azure przy użyciu polecenia

1. Wybierz kombinację klawiszy Ctrl + Shift + P, aby otworzyć paletę poleceń.

2. Wprowadź **ADL: login**. Informacje logowania są wyświetlane w prawym dolnym rogu.

   ![Wprowadzanie polecenia login](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login.png)

   ![Powiadamianie o logowaniu i uwierzytelnianiu](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-login-info.png)

3. Wybierz pozycję **kopiuj & Otwórz** , aby otworzyć [stronę sieci Web logowania](https://aka.ms/devicelogin). Wklej kod do pola, a następnie wybierz pozycję **Kontynuuj**.

    ![Strona sieci Web logowania](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login-paste-code.png)  

4. Postępuj zgodnie z instrukcjami, aby zalogować się ze strony sieci Web. Po nawiązaniu połączenia nazwa konta platformy Azure zostanie wyświetlona na pasku stanu w lewym dolnym rogu okna VS Code.

> [!NOTE]
>
> - Data Lake narzędzia automatycznie logują Cię w następnym czasie, jeśli nie wylogujesz się.
> - Jeśli na koncie są włączone dwa czynniki, zalecamy korzystanie z uwierzytelniania przy użyciu telefonu zamiast numeru PIN.

Aby się wylogować, wprowadź polecenie **ADL: Wyloguj**.

### <a name="to-connect-to-azure-from-the-explorer"></a>Aby nawiązać połączenie z platformą Azure z poziomu Eksploratora

Rozwiń węzeł **Azure datalake**, wybierz pozycję **Zaloguj się do platformy Azure**, a następnie postępuj zgodnie z krokami 3 i 4 [w celu nawiązania połączenia z platformą Azure przy użyciu polecenia](#sign-in-by-command).

![Wybór opcji "Zaloguj się do platformy Azure" w Eksploratorze](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-sign-in-from-explorer.png )  

Nie można wylogować się z Eksploratora. Aby się wylogować, zobacz [Aby nawiązać połączenie z platformą Azure za pomocą polecenia](#sign-in-by-command).

## <a name="create-an-extraction-script"></a>Utwórz skrypt wyodrębniania

Skrypt wyodrębniania dla plików CSV, TSV i txt można utworzyć przy użyciu polecenia **ADL: Create Extract Script** lub The Azure Data Lake Explorer.

### <a name="to-create-an-extraction-script-by-using-a-command"></a>Aby utworzyć skrypt wyodrębniania za pomocą polecenia

1. Wybierz kombinację klawiszy Ctrl + Shift + P, aby otworzyć paletę poleceń, a następnie wprowadź **ADL: Create Extract Script**.
2. Określ pełną ścieżkę do pliku usługi Azure Storage, a następnie wybierz klawisz ENTER.
3. Wybierz jedno konto.
4. W przypadku pliku txt wybierz ogranicznik, aby wyodrębnić plik.

![Proces tworzenia skryptu wyodrębniania](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-process.png)

Skrypt wyodrębniania jest generowany na podstawie Twoich wpisów. Dla skryptu, który nie może wykryć kolumn, wybierz jedną z dwóch opcji. W przeciwnym razie zostanie wygenerowany tylko jeden skrypt.

![Wynik tworzenia skryptu wyodrębniania](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-result.png)

### <a name="to-create-an-extraction-script-from-the-explorer"></a>Aby utworzyć skrypt wyodrębniania z Eksploratora

Innym sposobem tworzenia skryptu wyodrębniania jest kliknięcie prawym przyciskiem myszy (skróty) w pliku CSV,. tsv lub. txt w Azure Data Lake Store lub Azure Blob Storage.

![Polecenie "Utwórz WYODRĘBNIj skrypt" z menu skrótów](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-from-context-menu.png)

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