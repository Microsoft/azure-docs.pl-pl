---
title: Korzystanie z narzędzi Azure Data Lake Tools for Visual Studio Code
description: Dowiedz się, jak używać Azure Data Lake Tools for Visual Studio Code do tworzenia, testowania i uruchamiania skryptów U-SQL.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 02/09/2018
ms.openlocfilehash: e1d74795fd25019e205f4b7b1d2bac1b67107e2d
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107878582"
---
# <a name="use-azure-data-lake-tools-for-visual-studio-code"></a>Korzystanie z narzędzi Azure Data Lake Tools for Visual Studio Code

W tym artykule dowiesz się, jak za pomocą Azure Data Lake Tools for Visual Studio Code (VS Code) tworzyć, testować i uruchamiać skrypty U-SQL. Informacje zostały również uwzględnione w następującym filmie wideo:

[![Odtwarzacz wideo: Narzędzia usługi Azure Data Lake dla VS Code](media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-video.png)](https://channel9.msdn.com/Series/AzureDataLake/Azure-Data-Lake-Tools-for-VSCode?term=ADL%20Tools%20for%20VSCode")

## <a name="prerequisites"></a>Wymagania wstępne

Narzędzia Azure Data Lake Tools for VS Code obsługują systemy Windows, Linux i macOS. Lokalne uruchamianie i debugowanie lokalne języka U-SQL działa tylko w systemie Windows.

- [Visual Studio Code](https://www.visualstudio.com/products/code-vs.aspx)

W przypadku systemów macOS i Linux:

- [.NET 5.0 SDK](https://dotnet.microsoft.com/download)
- [Mono 5.2.x](https://www.mono-project.com/download/)

## <a name="install-azure-data-lake-tools"></a>Instalowanie narzędzi Azure Data Lake Tools

Po zainstalowaniu wymagań wstępnych możesz zainstalować narzędzia Azure Data Lake Tools for VS Code.

### <a name="to-install-azure-data-lake-tools"></a>Aby zainstalować narzędzia Azure Data Lake Tools

1. Otwórz program Visual Studio Code.
2. Wybierz **pozycję Rozszerzenia** w okienku po lewej stronie. W polu wyszukiwania wprowadź azure **data lake tools.**
3. Wybierz **pozycję Zainstaluj** obok opcji Azure Data Lake **Tools.**

   ![Opcje instalacji narzędzi Data Lake Tools](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extensions.png)

   Po kilku sekundach przycisk Zainstaluj **zmieni się** na Załaduj **ponownie**.
4. Wybierz **pozycję Załaduj** ponownie, aby **aktywować rozszerzenie Azure Data Lake Tools.**
5. Wybierz **pozycję Załaduj ponownie okno,** aby potwierdzić. Narzędzia **Azure Data Lake Tools są** dostępne w **okienku** Rozszerzenia.

## <a name="activate-azure-data-lake-tools"></a>Aktywowanie narzędzi usługi Azure Data Lake Tools

Utwórz plik usql lub otwórz istniejący plik usql, aby aktywować rozszerzenie.

## <a name="work-with-u-sql"></a>Praca z językem U-SQL

Aby pracować z językiem U-SQL, musisz otworzyć plik lub folder U-SQL.

### <a name="to-open-the-sample-script"></a>Aby otworzyć przykładowy skrypt

Otwórz paletę poleceń (Ctrl+Shift+P) i wprowadź **ADL: Otwórz przykładowy skrypt**. Spowoduje to otwarcie kolejnego wystąpienia tego przykładu. Możesz również edytować, konfigurować i przesyłać skrypt w tym wystąpieniu.

### <a name="to-open-a-folder-for-your-u-sql-project"></a>Aby otworzyć folder dla projektu U-SQL

1. Z Visual Studio Code wybierz menu **Plik,** a następnie wybierz pozycję **Otwórz folder**.
2. Określ folder, a następnie wybierz pozycję **Wybierz folder**.
3. Wybierz menu **Plik,** a następnie wybierz pozycję **Nowy.** Do projektu zostanie dodany plik Bez tytułu-1.
4. Wprowadź następujący kod w pliku Bez tytułu-1:

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

   DANE @departments     WYJŚCIOWE DO "/Output/departments.csv" PRZY UŻYCIU Outputters.Csv();

    Skrypt tworzy plik departments.csv danych zawartych w folderze /output.

5. Zapisz plik jako **myUSQL.usql** w otwartych folderach.

### <a name="to-compile-a-u-sql-script"></a>Aby skompilować skrypt U-SQL

1. Naciśnij klawisze Ctrl+Shift+P, aby otworzyć paletę poleceń.
2. Wprowadź **wartość ADL: Compile Script (ADL: skompiluj skrypt).** Wyniki kompilacji zostaną wyświetlone w **oknie Dane** wyjściowe. Możesz również kliknąć prawym przyciskiem myszy plik skryptu, a następnie wybrać pozycję **ADL: Kompilowanie** skryptu, aby skompilować zadanie U-SQL. Wynik kompilacji zostanie wyświetlony w **okienku Dane** wyjściowe.

### <a name="to-submit-a-u-sql-script"></a>Aby przesłać skrypt U-SQL

1. Naciśnij klawisze Ctrl+Shift+P, aby otworzyć paletę poleceń.
2. Wprowadź **wartość ADL: Submit Job (ADL: Prześlij zadanie).** Możesz również kliknąć prawym przyciskiem myszy plik skryptu, a następnie wybrać polecenie **ADL: Submit Job (ADL: Prześlij zadanie).**

Po przesłaniu zadania U-SQL dzienniki przesyłania są wyświetlane w oknie **Dane** wyjściowe w VS Code. Widok zadania zostanie wyświetlony w okienku po prawej stronie. Jeśli przesyłanie powiedzie się, zostanie również wyświetlony adres URL zadania. Możesz otworzyć adres URL zadania w przeglądarce internetowej, aby śledzić stan zadania w czasie rzeczywistym.

Na karcie PODSUMOWANIE **widoku** zadania można zobaczyć szczegóły zadania. Główne funkcje obejmują ponowne prześlij skrypt, zduplikowanie skryptu i otwarcie w portalu. Na karcie DANE **widoku** zadania można odwoływać się do plików wejściowych, plików wyjściowych i plików zasobów. Pliki można pobrać na komputer lokalny.

![Karta Podsumowanie w widoku zadania](./media/data-lake-analytics-data-lake-tools-for-vscode/job-view-summary.png)

![Karta Dane w widoku zadania](./media/data-lake-analytics-data-lake-tools-for-vscode/job-view-data.png)

### <a name="to-set-the-default-context"></a>Aby ustawić kontekst domyślny

Można ustawić domyślny kontekst, aby zastosować to ustawienie do wszystkich plików skryptów, jeśli nie ustawiono osobno parametrów dla plików.

1. Naciśnij klawisze Ctrl+Shift+P, aby otworzyć paletę poleceń.
2. Wprowadź **wartość ADL: ustaw domyślny kontekst**. Możesz też kliknąć prawym przyciskiem myszy edytor skryptów i wybrać pozycję **ADL: Ustaw domyślny kontekst.**
3. Wybierz konto, bazę danych i odpowiedni schemat. To ustawienie jest zapisywane w pliku xxx_settings.jskonfiguracji.

   ![Konto, baza danych i schemat ustawione jako kontekst domyślny](./media/data-lake-analytics-data-lake-tools-for-vscode/default-context-sequence.png)

### <a name="to-set-script-parameters"></a>Aby ustawić parametry skryptu

1. Naciśnij klawisze Ctrl+Shift+P, aby otworzyć paletę poleceń.
2. Wprowadź **wartość ADL: ustaw parametry skryptu**.
3. Plik xxx_settings.jsjest otwierany z następującymi właściwościami:

   - **account:** konto Azure Data Lake Analytics subskrypcji platformy Azure potrzebne do skompilowania i uruchomienia zadań U-SQL. Konto komputera należy skonfigurować przed skompilowanie i uruchomienie zadań U-SQL.
   - **database:** baza danych w ramach Twojego konta. Wartość domyślna to **master**.
   - **schema**: schemat w bazie danych. Wartość domyślna to **dbo**.
   - **optionalSettings**:
        - **priority**: zakres priorytetów to od 1 do 1000, a najwyższy priorytet to 1. Wartość domyślna to **1000**.
        - **degreeOfParallelism:** zakres równoległości wynosi od 1 do 150. Wartość domyślna to maksymalna równoległość dozwolona na Azure Data Lake Analytics konta.

   ![Zawartość pliku JSON](./media/data-lake-analytics-data-lake-tools-for-vscode/default-context-setting.png)

> [!NOTE]
> Po zapisaniu konfiguracji informacje o koncie, bazie danych i schemacie są wyświetlane na pasku stanu w lewym dolnym rogu odpowiedniego pliku usql, jeśli nie masz ustawionego kontekstu domyślnego.

### <a name="to-set-git-ignore"></a>Aby ustawić ignorowanie w usłudze Git

1. Naciśnij klawisze Ctrl+Shift+P, aby otworzyć paletę poleceń.
2. Wprowadź **wartość ADL: ustaw wartość Ignoruj w usłudze Git.**

   - Jeśli nie masz pliku **gitIgnore** w folderze VS Code, w folderze zostanie utworzony plik o nazwie **.gitIgnore.** Domyślnie w pliku są dodawane cztery elementy (**usqlCodeBehindReference,** **usqlCodeBehindGenerated,** **.cache,** **obj).** W razie potrzeby można wprowadzić więcej aktualizacji.
   - Jeśli masz już plik **gitIgnore** w folderze VS Code, narzędzie dodaje cztery elementy **(usqlCodeBehindReference,** **usqlCodeBehindGenerated,** **.cache,** **obj**) w pliku **gitIgnore,** jeśli te cztery elementy nie zostały uwzględnione w pliku.

   ![Elementy w pliku gitIgnore](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-gitignore.png)

## <a name="work-with-code-behind-files-c-sharp-python-and-r"></a>Praca z plikami kodu: C Sharp, Python i R

Narzędzia Azure Data Lake Tools obsługują wiele kodów niestandardowych. Aby uzyskać instrukcje, zobacz [Develop U-SQL with Python, R, and C Sharp for Azure Data Lake Analytics in VS Code](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md).

## <a name="work-with-assemblies"></a>Praca z zestawami

Aby uzyskać informacje na temat tworzenia zestawów, zobacz [Develop U-SQL assemblies for Azure Data Lake Analytics jobs]()(Tworzenie zestawów U-SQL dla Azure Data Lake Analytics zadań).

Za pomocą narzędzi Data Lake Tools można rejestrować niestandardowe zestawy kodu w Data Lake Analytics katalogu.

### <a name="to-register-an-assembly"></a>Aby zarejestrować zestaw

Zestaw można zarejestrować za pomocą polecenia **ADL: Register Assembly** lub **ADL: Register Assembly (Advanced).**

### <a name="to-register-through-the-adl-register-assembly-command"></a>Aby zarejestrować się za pomocą polecenia ADL: Register Assembly

1. Naciśnij klawisze Ctrl+Shift+P, aby otworzyć paletę poleceń.
2. Wprowadź **wartość ADL: Register Assembly (ADL: zarejestruj zestaw).**
3. Określ lokalną ścieżkę zestawu.
4. Wybierz Data Lake Analytics konto.
5. Wybierz bazę danych.

Portal zostanie otwarty w przeglądarce i zostanie wyświetlony proces rejestracji zestawu.  

Wygodniejszym sposobem wyzwalania **polecenia ADL: Register Assembly** jest kliknięcie prawym przyciskiem myszy pliku dll w Eksplorator plików.

### <a name="to-register-through-the-adl-register-assembly-advanced-command"></a>Aby zarejestrować się za pomocą polecenia ADL: Register Assembly (Advanced)

1. Naciśnij klawisze Ctrl+Shift+P, aby otworzyć paletę poleceń.
2. Wprowadź **wartość ADL: Register Assembly (Advanced) (ADL: zarejestruj zestaw (zaawansowane).**
3. Określ lokalną ścieżkę zestawu.
4. Zostanie wyświetlony plik JSON. W razie potrzeby przejrzyj i edytuj zależności zestawu oraz parametry zasobów. Instrukcje są wyświetlane w **oknie Dane** wyjściowe. Aby przejść do rejestracji zestawu, zapisz plik JSON (Ctrl+S).

   ![Plik JSON z zależnościami zestawu i parametrami zasobów](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-register-assembly-advance.png)

>[!NOTE]
>
>- Narzędzia Azure Data Lake Tools automatycznie monitoruje, czy biblioteka DLL ma jakiekolwiek zależności zestawu. Zależności są wyświetlane w pliku JSON po ich wykryciu.
>- Zasoby bibliotek DLL (na przykład txt, png i csv) można przekazać w ramach rejestracji zestawu.

Innym sposobem wyzwolenia **polecenia ADL: Register Assembly (Advanced)** jest kliknięcie prawym przyciskiem myszy pliku dll w Eksplorator plików.

Poniższy kod U-SQL pokazuje, jak wywołać zestaw. W przykładzie nazwa zestawu to *test*.

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

## <a name="use-u-sql-local-run-and-local-debug-for-windows-users"></a>Używanie lokalnego uruchamiania i debugowania lokalnego języka U-SQL dla użytkowników systemu Windows

Uruchomienie lokalne języka U-SQL testuje dane lokalne i weryfikuje skrypt lokalnie przed opublikowaniem kodu w Data Lake Analytics. Funkcja debugowania lokalnego umożliwia wykonywanie następujących zadań przed przesłaną do programu Data Lake Analytics:

- Debuguj kod w języku C#.
- Przekszukaj kod.
- Zweryfikuj skrypt lokalnie.

Lokalna funkcja uruchamiania i debugowania lokalnego działa tylko w środowiskach Windows i nie jest obsługiwana w systemach operacyjnych macOS i Linux.

Aby uzyskać instrukcje dotyczące lokalnego uruchamiania i debugowania lokalnego, zobacz Lokalne uruchamianie i debugowanie lokalne języka [U-SQL](data-lake-tools-for-vscode-local-run-and-debug.md)za pomocą Visual Studio Code .

## <a name="connect-to-azure"></a>Nawiązywanie połączenia z usługą Azure

Aby można było skompilować i uruchomić skrypty U-SQL w Data Lake Analytics, musisz połączyć się ze swoim kontem platformy Azure.

<a id="sign-in-by-command"></a>

### <a name="to-connect-to-azure-by-using-a-command"></a>Aby nawiązać połączenie z platformą Azure za pomocą polecenia

1. Naciśnij klawisze Ctrl+Shift+P, aby otworzyć paletę poleceń.

2. Wprowadź **wartość ADL: Zaloguj się.** Informacje logowania są wyświetlane w prawym dolnym rogu.

   ![Wprowadzanie polecenia logowania](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login.png)

   ![Powiadomienie dotyczące logowania i uwierzytelniania](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-login-info.png)

3. Wybierz **pozycję Kopiuj & Otwórz,** aby otworzyć stronę internetową [logowania.](https://aka.ms/devicelogin) Wklej kod w polu, a następnie wybierz pozycję **Kontynuuj.**

    ![Strona internetowa logowania](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login-paste-code.png)  

4. Postępuj zgodnie z instrukcjami, aby zalogować się ze strony internetowej. Po na połączeniu nazwa konta platformy Azure zostanie wyświetlona na pasku stanu w lewym dolnym rogu okna VS Code usługi.

> [!NOTE]
>
> - Narzędzia Data Lake Tools automatycznie logują Cię następnym razem, jeśli się nie wylogujesz.
> - Jeśli twoje konto ma włączone dwa czynniki, zalecamy korzystanie z uwierzytelniania za pomocą telefonu, a nie numeru PIN.

Aby się wylogować, wprowadź polecenie **ADL: Logout (Wyloguj).**

### <a name="to-connect-to-azure-from-the-explorer"></a>Aby nawiązać połączenie z platformą Azure z eksploratora

Rozwiń **pozycję AZURE DATALAKE,** wybierz pozycję Zaloguj się do platformy **Azure,** a następnie wykonaj kroki 3 i 4 z kroku Aby nawiązać połączenie z platformą Azure za [pomocą polecenia](#sign-in-by-command).

![Wybór opcji "Zaloguj się do platformy Azure" w eksploratorze](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-sign-in-from-explorer.png )  

Nie można wylogować się z eksploratora. Aby się wylogować, zobacz [Aby nawiązać połączenie z platformą Azure za pomocą polecenia](#sign-in-by-command).

## <a name="create-an-extraction-script"></a>Tworzenie skryptu wyodrębniania

Skrypt wyodrębniania dla plików csv, tsv i txt można utworzyć przy użyciu polecenia **ADL: Create EXTRACT Script** lub z eksploratora usługi Azure Data Lake.

### <a name="to-create-an-extraction-script-by-using-a-command"></a>Aby utworzyć skrypt wyodrębniania za pomocą polecenia

1. Naciśnij klawisze Ctrl+Shift+P, aby otworzyć paletę poleceń, a następnie wprowadź **ADL: Create EXTRACT Script**( Utwórz skrypt EXTRACT).
2. Określ pełną ścieżkę pliku usługi Azure Storage i wybierz klawisz Enter.
3. Wybierz jedno konto.
4. W przypadku pliku txt wybierz ogranicznik, aby wyodrębnić plik.

![Proces tworzenia skryptu wyodrębniania](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-process.png)

Skrypt wyodrębniania jest generowany na podstawie wpisów. W przypadku skryptu, który nie może wykryć kolumn, wybierz jedną z dwóch opcji. Jeśli nie, zostanie wygenerowany tylko jeden skrypt.

![Wynik tworzenia skryptu wyodrębniania](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-result.png)

### <a name="to-create-an-extraction-script-from-the-explorer"></a>Aby utworzyć skrypt wyodrębniania z eksploratora

Innym sposobem utworzenia skryptu wyodrębniania jest kliknięcie prawym przyciskiem myszy (skrót) menu w pliku csv, tsv lub txt w usłudze Azure Data Lake Store lub Azure Blob Storage.

![Polecenie "Create EXTRACT Script" (Utwórz skrypt WYODRĘBNIJ) z menu skrótów](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-from-context-menu.png)

## <a name="next-steps"></a>Następne kroki

- [Tworzenie języka U-SQL przy użyciu języka Python, R i C Sharp na Azure Data Lake Analytics w VS Code](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md)
- [Lokalne uruchamianie i debugowanie lokalne języka U-SQL przy użyciu Visual Studio Code](data-lake-tools-for-vscode-local-run-and-debug.md)
- [Samouczek: wprowadzenie do Azure Data Lake Analytics](data-lake-analytics-get-started-portal.md)
- [Samouczek: tworzenie skryptów U-SQL przy użyciu narzędzi Data Lake Tools for Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
