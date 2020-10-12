---
title: Uruchamianie zadań U-SQL lokalnie — Azure Data Lake zestawu U-SQL SDK
description: Dowiedz się, jak uruchamiać i testować zadania U-SQL lokalnie przy użyciu wiersza polecenia i interfejsów programowania na lokalnej stacji roboczej.
services: data-lake-analytics
ms.service: data-lake-analytics
author: yanacai
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 03/01/2017
ms.openlocfilehash: daf72fcf7baba289b4145d06d878c8a7232f1c6a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87132419"
---
# <a name="run-and-test-u-sql-with-azure-data-lake-u-sql-sdk"></a>Uruchamianie i testowanie U-SQL przy użyciu zestawu SDK U-SQL Azure Data Lake

Podczas tworzenia skryptu U-SQL często można uruchomić i przetestować skrypt U-SQL lokalnie przed przesłaniem go do chmury. Azure Data Lake udostępnia pakiet NuGet o nazwie Azure Data Lake zestawu SDK U-SQL w tym scenariuszu, za pomocą którego można łatwo skalować przebiegi i testy w języku U-SQL. Można również zintegrować ten test U-SQL z systemem CI (ciągłej integracji) w celu zautomatyzowania kompilowania i testowania.

Jeśli chcesz zadbać o ręczne uruchamianie i debugowanie skryptu U-SQL z graficznym interfejsem użytkownika, możesz użyć Azure Data Lake Tools for Visual Studio dla tego programu. Więcej informacji można znaleźć [tutaj](data-lake-analytics-data-lake-tools-local-run.md).

## <a name="install-azure-data-lake-u-sql-sdk"></a>Zainstaluj Azure Data Lake U-SQL SDK

W [witrynie NuGet.org](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/) możesz Azure Data Lake uzyskać zestaw SDK U-SQL. Przed użyciem należy upewnić się, że istnieją zależności w następujący sposób.

### <a name="dependencies"></a>Zależności

Zestaw SDK U-SQL Data Lake wymaga następujących zależności:

- [Microsoft .NET Framework 4,6 lub nowszy](https://www.microsoft.com/download/details.aspx?id=17851).
- Microsoft Visual C++ 14 i Windows SDK 10.0.10240.0 lub nowszy (o nazwie CppSDK w tym artykule). Istnieją dwa sposoby uzyskania CppSDK:

  - Zainstaluj [program Visual Studio Community Edition](https://developer.microsoft.com/downloads/vs-thankyou). W folderze Program Files znajduje się folder \Windows Kits\10 — na przykład C:\Program Files (x86) \Windows Kits\10\. Zostanie również znaleziona wersja zestawu SDK systemu Windows 10 w obszarze \Windows Kits\10\Lib. Jeśli te foldery nie są widoczne, zainstaluj ponownie program Visual Studio i upewnij się, że podczas instalacji wybrano zestaw SDK systemu Windows 10. Jeśli masz zainstalowany program Visual Studio, kompilator lokalny U-SQL znajdzie go automatycznie.

    ![Narzędzia Data Lake Tools for Visual Studio Local-Run Windows 10 SDK](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-for-visual-studio-local-run-windows-10-sdk.png)

  - Zainstaluj [narzędzia Data Lake Tools for Visual Studio](https://aka.ms/adltoolsvs). Pliki Visual C++ i Windows SDK w pakiecie są dostępne pod adresem `C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Microsoft\ADL Tools\X.X.XXXX.X\CppSDK.`

    W takim przypadku kompilator lokalny U-SQL nie może znaleźć zależności automatycznie. Należy określić ścieżkę CppSDK. Możesz skopiować pliki do innej lokalizacji lub użyć jej jako.

## <a name="understand-basic-concepts"></a>Opis podstawowych pojęć

### <a name="data-root"></a>Katalog główny danych

Folder danych-root to "Magazyn lokalny" dla lokalnego konta obliczeniowego. Jest on odpowiednikiem konta Azure Data Lake Store konta Data Lake Analytics. Przełączenie do innego folderu głównego danych jest takie samo jak przełączanie do innego konta magazynu. Jeśli chcesz uzyskać dostęp do często udostępnionych danych z różnymi folderami głównymi danych, musisz użyć ścieżek bezwzględnych w skryptach. Lub Utwórz linki symboliczne systemu plików (na przykład **mklink** w systemie plików NTFS) pod folderem głównym danych, aby wskazać udostępnione dane.

Folder główny danych służy do:

- Przechowywanie lokalnych metadanych, w tym baz danych, tabel, funkcji z wartościami przechowywanymi w tabeli (TVFs) i zestawów.
- Wyszukiwanie ścieżek wejściowych i wyjściowych, które są zdefiniowane jako ścieżki względne w języku U-SQL. Używanie ścieżek względnych ułatwia wdrażanie projektów U-SQL na platformie Azure.

### <a name="file-path-in-u-sql"></a>Ścieżka pliku w języku U-SQL

Możesz użyć ścieżki względnej i lokalnej ścieżki bezwzględnej w skryptach U-SQL. Ścieżka względna jest względna względem określonej ścieżki folderu Data-root. Zalecamy użycie znaku "/" jako separatora ścieżki, aby zapewnić zgodność skryptów z serwerem. Poniżej przedstawiono kilka przykładów ścieżek względnych i ich równoważne ścieżki bezwzględne. W tych przykładach C:\LocalRunDataRoot jest folderem głównym danych.

|Ścieżka względna|Ścieżka bezwzględna|
|-------------|-------------|
|/ABC/DEF/input.csv |C:\LocalRunDataRoot\abc\def\input.csv|
|ABC/DEF/input.csv  |C:\LocalRunDataRoot\abc\def\input.csv|
|D:/ABC/DEF/input.csv |D:\abc\def\input.csv|

### <a name="working-directory"></a>Katalog roboczy

Podczas lokalnego uruchamiania skryptu U-SQL, katalog roboczy jest tworzony podczas kompilacji w bieżącym uruchomionym katalogu. Oprócz danych wyjściowych kompilacji, potrzebne pliki środowiska uruchomieniowego do wykonania lokalnego zostaną skopiowane do tego katalogu roboczego. Folder główny katalogu roboczego nosi nazwę "ScopeWorkDir", a pliki znajdujące się w katalogu roboczym są następujące:

|Katalog/plik|Katalog/plik|Katalog/plik|Definicja|Opis|
|--------------|--------------|--------------|----------|-----------|
|C6A101DDCB470506| | |Ciąg skrótu wersji środowiska uruchomieniowego|Kopia w tle plików czasu wykonywania wymagana do wykonania lokalnego|
| |Script_66AE4909AA0ED06C| |Nazwa skryptu i ciąg skrótu ścieżki skryptu|Dane wyjściowe kompilacji i rejestrowanie kroków wykonywania|
| | |\_skrypt \_ . ABR|Dane wyjściowe kompilatora|Plik algebry|
| | |\_ScopeCodeGen \_ . *|Dane wyjściowe kompilatora|Wygenerowany kod zarządzany|
| | |\_ScopeCodeGenEngine \_ . *|Dane wyjściowe kompilatora|Wygenerowany kod natywny|
| | |przywoływane zestawy|Odwołanie do zestawu|Przywoływane pliki zestawu|
| | |deployed_resources|Wdrażanie zasobów|Pliki wdrożeń zasobów|
| | |XXXXXXXX. xxx [1.. n] \_ \* . *|Dziennik wykonywania|Dziennik kroków wykonywania|

## <a name="use-the-sdk-from-the-command-line"></a>Korzystanie z zestawu SDK z poziomu wiersza polecenia

### <a name="command-line-interface-of-the-helper-application"></a>Interfejs wiersza polecenia aplikacji pomocnika

W obszarze SDK directory\build\runtime, LocalRunHelper.exe to aplikacja pomocnicza wiersza polecenia, która udostępnia interfejsy do większości najczęściej używanych funkcji uruchamiania lokalnego. Należy zauważyć, że oba polecenia i argumentu są rozróżniane wielkości liter. Aby wywołać go:

```console
LocalRunHelper.exe <command> <Required-Command-Arguments> [Optional-Command-Arguments]
```

Uruchom LocalRunHelper.exe bez argumentów lub za pomocą przełącznika **pomocy** , aby wyświetlić informacje pomocy:

```console
> LocalRunHelper.exe help
    Command 'help' :  Show usage information
    Command 'compile' :  Compile the script
    Required Arguments :
        -Script param
                Script File Path
    Optional Arguments :
        -Shallow [default value 'False']
                Shallow compile
```

W oknie informacje pomocy:

- **Polecenie** przypisuje nazwę polecenia.  
- **Argument wymagany** zawiera listę argumentów, które muszą zostać dostarczone.  
- **Argument opcjonalny** zawiera listę opcjonalnych argumentów z wartościami domyślnymi.  Opcjonalne argumenty logiczne nie mają parametrów, a ich wygląd oznacza wartość ujemną do wartości domyślnej.

### <a name="return-value-and-logging"></a>Wartość zwracana i rejestrowanie

Aplikacja pomocnika zwraca **wartość 0** dla sukcesu i **-1** w przypadku niepowodzenia. Domyślnie pomocnik wysyła wszystkie komunikaty do bieżącej konsoli. Większość poleceń obsługuje jednak **MessageOut path_to_log_file** opcjonalny argument, który przekierowuje dane wyjściowe do pliku dziennika.

### <a name="environment-variable-configuring"></a>Konfigurowanie zmiennej środowiskowej

Lokalne uruchomienie języka U-SQL wymaga określonego katalogu głównego danych jako lokalnego konta magazynu, a także określonej ścieżki CppSDK dla zależności. Można zarówno ustawić argument w wierszu polecenia, jak i ustawić dla nich zmienną środowiskową.

- Ustaw zmienną środowiskową **SCOPE_CPP_SDK** .

  Jeśli uzyskasz Microsoft Visual C++ i Windows SDK przez zainstalowanie narzędzi Data Lake Tools for Visual Studio, upewnij się, że masz następujący folder:

    `C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Microsoft\Microsoft Azure Data Lake Tools for Visual Studio 2015\X.X.XXXX.X\CppSDK`

  Zdefiniuj nową zmienną środowiskową o nazwie **SCOPE_CPP_SDK** , aby wskazywała na ten katalog. Lub skopiuj folder do innej lokalizacji i określ **SCOPE_CPP_SDK** .

  Oprócz ustawiania zmiennej środowiskowej, można określić argument **-CppSDK** podczas korzystania z wiersza polecenia. Ten argument zastępuje domyślną zmienną środowiskową CppSDK.

- Ustaw zmienną środowiskową **LOCALRUN_DATAROOT** .

  Zdefiniuj nową zmienną środowiskową o nazwie **LOCALRUN_DATAROOT** , która wskazuje na katalog główny danych.

  Oprócz ustawiania zmiennej środowiskowej, można określić argument **-dataroot** z ścieżką katalogu głównego danych podczas korzystania z wiersza polecenia. Ten argument zastępuje domyślną zmienną środowiskową danych głównych. Ten argument należy dodać do każdego uruchomionego wiersza polecenia, aby można było zastąpić domyślną zmienną środowiskową danych dla wszystkich operacji.

### <a name="sdk-command-line-usage-samples"></a>Przykłady użycia wiersza polecenia zestawu SDK

#### <a name="compile-and-run"></a>Kompiluj i uruchom

Polecenie **Run** służy do kompilowania skryptu, a następnie wykonywania skompilowanych wyników. Argumenty wiersza polecenia są kombinacją elementów z **Kompiluj** i **Execute**.

```console
LocalRunHelper run -Script path_to_usql_script.usql [optional_arguments]
```

Poniżej podano opcjonalne argumenty dla **przebiegu**:

|Argument|Wartość domyślna|Opis|
|--------|-------------|-----------|
|-CodeBehind|Fałsz|Skrypt zawiera kod CS|
|-CppSDK| |Katalog CppSDK|
|— Dataroot| Zmienna środowiskowa dataroot|Element dataroot dla lokalnego uruchomienia, domyślnie jako zmienna środowiskowa "LOCALRUN_DATAROOT"|
|-MessageOut| |Zrzuć komunikaty w konsoli do pliku|
|-Parallel|1|Uruchamianie planu z określoną równoległością|
|-Odwołania| |Lista ścieżek do dodatkowych zestawów referencyjnych lub plików danych w kodzie, oddzielonych znakami ";"|
|-UdoRedirect|Fałsz|Generuj konfigurację przekierowania zestawu udo|
|-UseDatabase|master|Baza danych do użycia dla kodu za tymczasową rejestracją zestawów|
|-Verbose|Fałsz|Pokaż szczegółowe dane wyjściowe z środowiska uruchomieniowego|
|-WorkDir|Bieżący katalog|Katalog do użycia i wyjść kompilatora|
|-RunScopeCEP|0|Tryb ScopeCEP do użycia|
|-ScopeCEPTempPath|tymczasowe|Tymczasowa ścieżka do użycia na potrzeby przesyłania strumieniowego danych|
|-OptFlags| |Rozdzielana przecinkami lista flag Optymalizatora|

Oto przykład:

`LocalRunHelper run -Script d:\test\test1.usql -WorkDir d:\test\bin -CodeBehind -References "d:\asm\ref1.dll;d:\asm\ref2.dll" -UseDatabase testDB –Parallel 5 -Verbose`

Oprócz łączenia **kompilacji** i **wykonania**, można oddzielnie kompilować i wykonywać skompilowane pliki wykonywalne.

#### <a name="compile-a-u-sql-script"></a>Kompilowanie skryptu U-SQL

Polecenie **Kompiluj** służy do kompilowania skryptu U-SQL do plików wykonywalnych.

```console
LocalRunHelper compile -Script path_to_usql_script.usql [optional_arguments]
```

Poniżej przedstawiono opcjonalne argumenty dla **kompilowania**:

|Argument|Opis|
|--------|-----------|
| -CodeBehind [wartość domyślna "false"]|Skrypt zawiera kod CS|
| -CppSDK [wartość domyślna ""]|Katalog CppSDK|
| — Dataroot [zmienna środowiskowa dataroot "]|Element dataroot dla lokalnego uruchomienia, domyślnie jako zmienna środowiskowa "LOCALRUN_DATAROOT"|
| -MessageOut [wartość domyślna ""]|Zrzuć komunikaty w konsoli do pliku|
| -References [wartość domyślna ""]|Lista ścieżek do dodatkowych zestawów referencyjnych lub plików danych w kodzie, oddzielonych znakami ";"|
| -Płytki [wartość domyślna "false"]|Kompilowanie płytki|
| -UdoRedirect [wartość domyślna "false"]|Generuj konfigurację przekierowania zestawu udo|
| -UseDatabase [wartość domyślna "Master"]|Baza danych do użycia dla kodu za tymczasową rejestracją zestawów|
| -WorkDir [wartość domyślna "Current Directory"]|Katalog do użycia i wyjść kompilatora|
| -RunScopeCEP [wartość domyślna "0"]|Tryb ScopeCEP do użycia|
| -ScopeCEPTempPath [wartość domyślna "Temp"]|Tymczasowa ścieżka do użycia na potrzeby przesyłania strumieniowego danych|
| -OptFlags [wartość domyślna ""]|Rozdzielana przecinkami lista flag Optymalizatora|

Poniżej przedstawiono kilka przykładów użycia.

Kompiluj skrypt U-SQL:

```console
LocalRunHelper compile -Script d:\test\test1.usql
```

Skompiluj skrypt U-SQL i ustaw folder Data-root. Należy zauważyć, że spowoduje to zastąpienie ustawionej zmiennej środowiskowej.

```console
LocalRunHelper compile -Script d:\test\test1.usql –DataRoot c:\DataRoot
```

Skompiluj skrypt U-SQL i Ustaw katalog roboczy, zestaw odwołań i bazę danych:

```console
LocalRunHelper compile -Script d:\test\test1.usql -WorkDir d:\test\bin -References "d:\asm\ref1.dll;d:\asm\ref2.dll" -UseDatabase testDB
```

#### <a name="execute-compiled-results"></a>Wykonaj skompilowane wyniki

Polecenie **Execute** służy do wykonywania skompilowanych wyników.

```console
LocalRunHelper execute -Algebra path_to_compiled_algebra_file [optional_arguments]
```

Poniżej podano opcjonalne argumenty dla **Execute**:

|Argument|Wartość domyślna|Opis|
|--------|-------------|-----------|
|— Dataroot | '' |Katalog główny danych do wykonania metadanych. Domyślnie jest to zmienna środowiskowa **LOCALRUN_DATAROOT** .|
|-MessageOut | '' |Zrzuć komunikaty z konsoli programu do pliku.|
|-Parallel | jedno |Wskaźnik uruchamiający wygenerowane kroki uruchamiania lokalnego z określonym poziomem równoległości.|
|-Verbose | False |Wskaźnik umożliwiający wyświetlenie szczegółowych danych wyjściowych z środowiska uruchomieniowego.|

Oto przykład użycia:

```console
LocalRunHelper execute -Algebra d:\test\workdir\C6A101DDCB470506\Script_66AE4909AA0ED06C\__script__.abr –DataRoot c:\DataRoot –Parallel 5
```

## <a name="use-the-sdk-with-programming-interfaces"></a>Korzystanie z zestawu SDK z interfejsami programowania

Wszystkie interfejsy programowania znajdują się w LocalRunHelper.exe. Można ich użyć do zintegrowania funkcjonalności zestawu U-SQL i środowiska testowego C# w celu skalowania lokalnego testu skryptu U-SQL. W tym artykule użyjemy standardowego projektu testowego w języku C#, aby zobaczyć, jak używać tych interfejsów do testowania skryptu U-SQL.

### <a name="step-1-create-c-unit-test-project-and-configuration"></a>Krok 1. Tworzenie projektu i konfiguracji testu jednostkowego w języku C#

- Utwórz projekt testu jednostkowego w języku C# za pomocą pliku > nowym > Project > projekcie testów jednostkowych Visual C# > test >.
- Dodaj LocalRunHelper.exe jako odwołanie do projektu. LocalRunHelper.exe znajduje się w \build\runtime\LocalRunHelper.exe w pakiecie NuGet.

   ![Azure Data Lake Dodaj odwołanie do zestawu SDK U-SQL](./media/data-lake-analytics-u-sql-sdk/data-lake-analytics-u-sql-sdk-add-reference.png)

- Zestaw SDK języka U-SQL obsługuje **tylko** środowisko x64, pamiętaj, aby ustawić cel platformy kompilacji jako 64. Można ustawić tę wartość przy użyciu właściwości projektu > kompilacja > celu platformy.

   ![Azure Data Lake konfiguracji zestawu SDK U-SQL dla projektu x64](./media/data-lake-analytics-u-sql-sdk/data-lake-analytics-u-sql-sdk-configure-x64.png)

- Upewnij się, że środowisko testowe jest ustawione jako 64. W programie Visual Studio można ustawić je za poorednictwem ustawień testu > testu > domyślnej architektury procesora > x64.

   ![Azure Data Lake konfiguracja zestawu SDK U-SQL dla środowiska testowego x64](./media/data-lake-analytics-u-sql-sdk/data-lake-analytics-u-sql-sdk-configure-test-x64.png)

- Pamiętaj o skopiowaniu wszystkich plików zależności w obszarze NugetPackage\build\runtime\ do katalogu roboczego projektu, który zwykle znajduje się w ProjectFolder\bin\x64\Debug.

### <a name="step-2-create-u-sql-script-test-case"></a>Krok 2. Tworzenie przypadku testowego skryptu U-SQL

Poniżej znajduje się przykładowy kod dla testu skryptu U-SQL. Do testowania należy przygotować skrypty, pliki wejściowe i oczekiwane pliki wyjściowe.

```usql
using System;
using Microsoft.VisualStudio.TestTools.UnitTesting;
using System.IO;
using System.Text;
using System.Security.Cryptography;
using Microsoft.Analytics.LocalRun;
namespace UnitTestProject1
{
    [TestClass]
    public class USQLUnitTest
    {
        [TestMethod]
        public void TestUSQLScript()
        {
            //Specify the local run message output path
            StreamWriter MessageOutput = new StreamWriter("../../../log.txt");
            LocalRunHelper localrun = new LocalRunHelper(MessageOutput);
            //Configure the DateRoot path, Script Path and CPPSDK path
            localrun.DataRoot = "../../../";
            localrun.ScriptPath = "../../../Script/Script.usql";
            localrun.CppSdkDir = "../../../CppSDK";
            //Run U-SQL script
            localrun.DoRun();
            //Script output
            string Result = Path.Combine(localrun.DataRoot, "Output/result.csv");
            //Expected script output
            string ExpectedResult = "../../../ExpectedOutput/result.csv";
            Test.Helpers.FileAssert.AreEqual(Result, ExpectedResult);
            //Don't forget to close MessageOutput to get logs into file
            MessageOutput.Close();
        }
    }
}
namespace Test.Helpers
{
    public static class FileAssert
    {
        static string GetFileHash(string filename)
        {
            Assert.IsTrue(File.Exists(filename));
            using (var hash = new SHA1Managed())
            {
                var clearBytes = File.ReadAllBytes(filename);
                var hashedBytes = hash.ComputeHash(clearBytes);
                return ConvertBytesToHex(hashedBytes);
            }
        }
        static string ConvertBytesToHex(byte[] bytes)
        {
            var sb = new StringBuilder();
            for (var i = 0; i < bytes.Length; i++)
            {
                sb.Append(bytes[i].ToString("x"));
            }
            return sb.ToString();
        }
        public static void AreEqual(string filename1, string filename2)
        {
            string hash1 = GetFileHash(filename1);
            string hash2 = GetFileHash(filename2);
            Assert.AreEqual(hash1, hash2);
        }
    }
}
```

### <a name="programming-interfaces-in-localrunhelperexe"></a>Programowanie interfejsów w LocalRunHelper.exe

LocalRunHelper.exe udostępnia interfejsy programowania dla lokalnego kompilowania U-SQL, uruchamiania itp. Interfejsy są wymienione w następujący sposób.

### <a name="constructor"></a>Konstruktor

Public LocalRunHelper ([System. IO. TextWriter messageOutput = null])

|Parametr|Typ|Opis|
|---------|----|-----------|
|messageOutput|System. IO. TextWriter|dla wiadomości wyjściowych ustaw wartość null, aby użyć konsoli|

### <a name="properties"></a>Właściwości

|Właściwość|Type|Opis|
|--------|----|-----------|
|AlgebraPath|ciąg|Ścieżka do pliku algebry (plik algebry jest jednym z wyników kompilacji)|
|CodeBehindReferences|ciąg|Jeśli skrypt ma dodatkowy kod związany z odwołaniami, określ ścieżki rozdzielone znakami ";"|
|CppSdkDir|ciąg|Katalog CppSDK|
|CurrentDir|ciąg|Bieżący katalog|
|DataRoot|ciąg|Ścieżka katalogu głównego danych|
|DebuggerMailPath|ciąg|Ścieżka do debugera skrzynki mailslot|
|GenerateUdoRedirect|bool|Jeśli chcemy generować konfigurację zastępowania przekierowań ładowania zestawu|
|HasCodeBehind|bool|Jeśli skrypt zawiera kod|
|InputDir|ciąg|Katalog danych wejściowych|
|MessagePath|ciąg|Ścieżka pliku zrzutu wiadomości|
|OutputDir|ciąg|Katalog danych wyjściowych|
|Równoległości|int|Równoległość do uruchamiania algebry|
|ParentPid|int|Identyfikator PID elementu nadrzędnego, w którym monitorowana jest usługa, ustaw wartość 0 lub ujemną, aby zignorować|
|ResultPath|ciąg|Ścieżka pliku zrzutu wyniku|
|RuntimeDir|ciąg|Katalog środowiska uruchomieniowego|
|ScriptPath|ciąg|Gdzie można znaleźć skrypt|
|Mała|bool|Kompilowanie płytki|
|TempDir|ciąg|Katalog temp|
|UseDataBase|ciąg|Określ bazę danych, która ma być używana dla kodu za tymczasową rejestrację zestawów, domyślnie Master|
|WorkDir|ciąg|Preferowany katalog roboczy|

### <a name="method"></a>Metoda

|Metoda|Opis|Przesłać|Parametr|
|------|-----------|------|---------|
|Public bool DoCompile ()|Kompilowanie skryptu U-SQL|Wartość true dla sukcesu| |
|Public bool DoExec ()|Wykonaj skompilowany wynik|Wartość true dla sukcesu| |
|Public bool DoRun ()|Uruchamianie skryptu U-SQL (kompilacja + wykonywanie)|Wartość true dla sukcesu| |
|Public bool IsValidRuntimeDir (ścieżka ciągu)|Sprawdź, czy dana ścieżka jest prawidłową ścieżką środowiska uruchomieniowego|Wartość true dla prawidłowego|Ścieżka katalogu środowiska uruchomieniowego|

## <a name="faq-about-common-issue"></a>Często zadawane pytania dotyczące typowego problemu

### <a name="error-1"></a>Błąd 1

E_CSC_SYSTEM_INTERNAL: błąd wewnętrzny! Nie można załadować pliku lub zestawu "ScopeEngineManaged.dll" lub jednej z jego zależności. Nie można odnaleźć określonego modułu.

Sprawdź następujące kwestie:

- Upewnij się, że masz środowisko x64. Platforma docelowa kompilacji i środowisko testowe powinny być w wersji x64, zapoznaj się z **krok 1. Tworzenie projektu testów jednostkowych C# i konfiguracji** powyżej.
- Upewnij się, że skopiowano wszystkie pliki zależności w obszarze NugetPackage\build\runtime\ do katalogu roboczego projektu.

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej o języku U-SQL, zobacz [Wprowadzenie do języka U-SQL w usłudze Azure Data Lake Analytics](data-lake-analytics-u-sql-get-started.md).
- Aby rejestrować informacje diagnostyczne, zobacz [Uzyskiwanie dostępu do dzienników diagnostycznych dla Azure Data Lake Analytics](data-lake-analytics-diagnostic-logs.md).
- Aby wyświetlić bardziej złożone zapytanie, zobacz [Analizowanie dzienników witryn sieci Web przy użyciu Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md).
- Aby wyświetlić szczegóły zadania, zobacz [Korzystanie z przeglądarki zadań i widoku zadań dla Azure Data Lake Analytics zadań](data-lake-analytics-data-lake-tools-view-jobs.md).
- Aby skorzystać z widoku wykonywania wierzchołków, zobacz temat [Używanie widoku wykonywania wierzchołka w Data Lake narzędzia dla programu Visual Studio](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md).
