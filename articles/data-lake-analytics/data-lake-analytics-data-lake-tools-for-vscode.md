---
title: Korzystanie z narzędzi Azure Data Lake Tools for Visual Studio Code
description: Dowiedz się, jak używać kodu Azure Data Lake Tools for Visual Studio, aby tworzyć, testować i uruchamiać skrypty U-SQL.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 02/09/2018
ms.openlocfilehash: 40e3ce17e036312e7c3fdee95fcb42d06f5845e9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96751363"
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

## <a name="next-steps"></a>Następne kroki

- [Opracowywanie języka U-SQL przy użyciu języków Python, R i C dla Azure Data Lake Analytics w VS Code](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md)
- [Lokalne uruchamianie skryptu U-SQL i debugowanie lokalne przy użyciu Visual Studio Code](data-lake-tools-for-vscode-local-run-and-debug.md)
- [Samouczek: wprowadzenie do Azure Data Lake Analytics](data-lake-analytics-get-started-portal.md)
- [Samouczek: Tworzenie skryptów U-SQL przy użyciu narzędzi Data Lake Tools for Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)