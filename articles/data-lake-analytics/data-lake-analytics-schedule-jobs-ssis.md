---
title: Planowanie Azure Data Lake Analytics zadań U-SQL przy użyciu usług SSIS
description: Dowiedz się, jak używać SQL Server Integration Services do planowania zadań U-SQL przy użyciu skryptu wbudowanego lub z plików zapytań U-SQL.
services: data-lake-analytics
ms.reviewer: jasonh
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.topic: how-to
ms.workload: big-data
ms.date: 07/17/2018
ms.openlocfilehash: a5c7b9fb6a3431534d743f1ebd0b21f1da9fab7b
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91318708"
---
# <a name="schedule-u-sql-jobs-using-sql-server-integration-services-ssis"></a>Planowanie zadań U-SQL przy użyciu SQL Server Integration Services (SSIS)

W tym dokumencie dowiesz się, jak organizować i tworzyć zadania U-SQL przy użyciu usługi integracji SQL Server (SSIS). 

## <a name="prerequisites"></a>Wymagania wstępne

[Pakiet Azure Feature Pack dla usług Integration Services](https://docs.microsoft.com/sql/integration-services/azure-feature-pack-for-integration-services-ssis?view=sql-server-2017#scenario-managing-data-in-the-cloud) zawiera [zadanie Azure Data Lake Analytics](https://docs.microsoft.com/sql/integration-services/control-flow/azure-data-lake-analytics-task?view=sql-server-2017) i [Menedżera połączeń Azure Data Lake Analytics](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-data-lake-analytics-connection-manager?view=sql-server-2017) , który pomaga połączyć się z usługą Azure Data Lake Analytics. Aby użyć tego zadania, należy się upewnić, że zainstalowano następujące polecenie:

- [Pobierz i zainstaluj narzędzia SQL Server Data Tools (SSDT) dla programu Visual Studio](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017)
- [Zainstaluj pakiet Azure Feature Pack dla usług Integration Services (SSIS)](https://docs.microsoft.com/sql/integration-services/azure-feature-pack-for-integration-services-ssis?view=sql-server-2017)

## <a name="azure-data-lake-analytics-task"></a>Azure Data Lake Analytics, zadanie

Zadanie Azure Data Lake Analytics umożliwia użytkownikom przesyłanie zadań U-SQL do konta Azure Data Lake Analytics. 

[Dowiedz się, jak skonfigurować zadanie Azure Data Lake Analytics](https://docs.microsoft.com/sql/integration-services/control-flow/azure-data-lake-analytics-task?view=sql-server-2017).

![Azure Data Lake Analytics zadania w programie SSIS](./media/data-lake-analytics-schedule-jobs-ssis/data-lake-analytics-azure-data-lake-analytics-task-in-ssis.png)

Możesz uzyskać skrypt U-SQL z różnych miejsc za pomocą wbudowanych funkcji i zadań usług SSIS, poniżej przedstawiono sposób konfigurowania skryptów U-SQL dla różnych przypadków użycia.

## <a name="scenario-1-use-inline-script-call-tvfs-and-stored-procs"></a>Scenariusz 1 — użycie wbudowanego wywołania skryptu TVFs i składowanych

W Azure Data Lake Analytics edytorze zadań Skonfiguruj **sourceType** jako **DirectInput**i umieść instrukcje U-SQL w **USQLStatement**.

Aby zapewnić łatwą konserwację i Zarządzanie kodem, należy umieścić tylko krótki skrypt U-SQL jako skrypty wbudowane, na przykład można wywołać istniejące funkcje z wartościami przechowywanymi w tabeli oraz procedury składowane w bazach danych U-SQL. 

![Edytuj wbudowany skrypt U-SQL w zadaniu usług SSIS](./media/data-lake-analytics-schedule-jobs-ssis/edit-inline-usql-script-in-ssis.png)

Pokrewny artykuł: [jak przekazać parametr do procedur składowanych](#scenario-6-pass-parameters-to-u-sql-script)

## <a name="scenario-2-use-u-sql-files-in-azure-data-lake-store"></a>Scenariusz 2 — używanie plików U-SQL w Azure Data Lake Store

Możesz również użyć plików U-SQL w Azure Data Lake Store przy użyciu **zadania systemu plików Azure Data Lake Store** w pakiecie Azure Feature Pack. Takie podejście umożliwia korzystanie ze skryptów przechowywanych w chmurze.

Postępuj zgodnie z poniższymi instrukcjami, aby skonfigurować połączenie między zadaniem Azure Data Lake Store pliku i Azure Data Lake Analytics zadaniem.

### <a name="set-task-control-flow"></a>Ustaw przepływ sterowania zadaniami

W widoku projektu pakietu usług SSIS Dodaj **zadanie systemu plików Azure Data Lake Store**, **kontenera pętli foreach** i **zadania Azure Data Lake Analytics** w kontenerze pętli Foreach. Zadanie Azure Data Lake Store File System ułatwia pobranie plików U-SQL na koncie ADLS do folderu tymczasowego. Kontener pętli Foreach i zadanie Azure Data Lake Analytics pomagają przesłać każdy plik U-SQL w folderze tymczasowym do konta Azure Data Lake Analytics jako zadanie U-SQL.

![Diagram przedstawiający zadanie systemu plików Azure Data Lake Store dodawane do kontenera pętli Foreach.](./media/data-lake-analytics-schedule-jobs-ssis/use-u-sql-files-in-azure-data-lake-store.png)

### <a name="configure-azure-data-lake-store-file-system-task"></a>Azure Data Lake Store zadanie konfigurowania systemu plików

1. Ustaw **operację** na **CopyFromADLS**.
2. Skonfiguruj **AzureDataLakeConnection**, Dowiedz się więcej o [Menedżerze połączeń Azure Data Lake Store](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-data-lake-store-connection-manager?view=sql-server-2017).
3. Ustaw **AzureDataLakeDirectory**. Wskaż folder, w którym przechowywane są skrypty U-SQL. Użyj ścieżki względnej odnoszącej się do folderu głównego konta Azure Data Lake Store.
4. Ustaw **lokalizację docelową** na folder, w którym są buforowane pobrane skrypty U-SQL. Ta ścieżka folderu zostanie użyta w kontenerze pętli Foreach dla przesłania zadania U-SQL. 

![Azure Data Lake Store zadanie konfigurowania systemu plików](./media/data-lake-analytics-schedule-jobs-ssis/configure-azure-data-lake-store-file-system-task.png)

[Dowiedz się więcej o zadaniu systemu plików Azure Data Lake Store](https://docs.microsoft.com/sql/integration-services/control-flow/azure-data-lake-store-file-system-task?view=sql-server-2017).

### <a name="configure-foreach-loop-container"></a>Konfigurowanie kontenera pętli Foreach

1. Na stronie **Kolekcja** ustaw moduł **wyliczający** na **moduł wyliczający plików foreach**.

2. Ustaw **folder** pod grupą **Konfiguracja modułu wyliczającego** do folderu tymczasowego zawierającego pobrane skrypty U-SQL.

3. Ustaw **pliki** w obszarze **Konfiguracja modułu wyliczającego** na `*.usql` tak, aby kontener pętli przechwytywał tylko pliki kończące się na `.usql` .

    ![Zrzut ekranu, który pokazuje Edytor pętli Foreach z wybraną pozycją "Kolekcja" oraz wyróżnione sekcje konfiguracji modułu wyliczającego i modułu wyliczającego.](./media/data-lake-analytics-schedule-jobs-ssis/configure-foreach-loop-container-collection.png)

4. Na stronie **mapowania zmiennych** Dodaj zmienną zdefiniowaną przez użytkownika w celu pobrania nazwy pliku dla każdego pliku U-SQL. Ustaw **indeks** na wartość 0, aby uzyskać nazwę pliku. W tym przykładzie Zdefiniuj zmienną o nazwie `User::FileName` . Ta zmienna zostanie użyta do dynamicznego pobrania połączenia pliku skryptu U-SQL i ustawienia nazwy zadania U-SQL w Azure Data Lake Analytics zadania.

    ![Skonfiguruj kontener pętli Foreach, aby uzyskać nazwę pliku](./media/data-lake-analytics-schedule-jobs-ssis/configure-foreach-loop-container-variable-mapping.png)

### <a name="configure-azure-data-lake-analytics-task"></a>Konfigurowanie Azure Data Lake Analytics zadania 

1. Ustaw **sourceType** na **FileConnection**.

2. Ustaw **FileConnection** na połączenie z plikiem, które wskazuje obiekty plików zwracane z kontenera pętli Foreach.
    
    Aby utworzyć to połączenie z plikiem:

   1. Wybierz **\<New Connection...>** ustawienie w FileConnection.
   2. Ustaw **Typ użycia** na **istniejący plik**i ustaw **plik** na ścieżkę pliku istniejącego pliku.

       ![Zrzut ekranu przedstawiający Edytor Menedżera połączeń plików z wybranym elementem "typ użycia".](./media/data-lake-analytics-schedule-jobs-ssis/configure-file-connection-for-foreach-loop-container.png)

   3. W widoku **menedżerów połączeń** kliknij prawym przyciskiem myszy utworzone połączenie plików, a następnie wybierz polecenie **Właściwości**.

   4. W oknie **Właściwości** rozwiń pozycję **wyrażenia**, a następnie ustaw parametr **ConnectionString** na zmiennej zdefiniowanej w kontenerze pętli Foreach, na przykład `@[User::FileName]` .

       ![Konfigurowanie kontenera pętli Foreach](./media/data-lake-analytics-schedule-jobs-ssis/configure-file-connection-property-for-foreach-loop-container.png)

3. Ustaw **AzureDataLakeAnalyticsConnection** na konto Azure Data Lake Analytics, do którego chcesz przesłać zadania. Dowiedz się więcej o programie [Azure Data Lake Analytics Connection Manager](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-data-lake-analytics-connection-manager?view=sql-server-2017).

4. Ustaw inne konfiguracje zadań. [Dowiedz się więcej](https://docs.microsoft.com/sql/integration-services/control-flow/azure-data-lake-analytics-task?view=sql-server-2017).

5. Użyj **wyrażeń** do dynamicznego ustawiania nazwy zadania U-SQL:

    1. Na stronie **wyrażenia** Dodaj nową parę klucz-wartość wyrażenia dla **JobName**.
    2. Ustaw wartość JobName na zmienną zdefiniowaną w kontenerze pętli Foreach, na przykład `@[User::FileName]` .
    
        ![Skonfiguruj wyrażenie SSIS dla nazwy zadania U-SQL](./media/data-lake-analytics-schedule-jobs-ssis/configure-expression-for-u-sql-job-name.png)

## <a name="scenario-3-use-u-sql-files-in-azure-blob-storage"></a>Scenariusz 3 — Używanie plików U-SQL na platformie Azure Blob Storage

Możesz użyć plików U-SQL w usłudze Azure Blob Storage przy użyciu **zadania pobierania obiektów blob platformy Azure** w pakiecie Azure Feature Pack. Takie podejście umożliwia korzystanie ze skryptów w chmurze.

Kroki są podobne do [scenariusza 2: Użyj plików U-SQL w Azure Data Lake Store](#scenario-2-use-u-sql-files-in-azure-data-lake-store). Zmień zadanie systemu plików Azure Data Lake Store na zadanie pobierania obiektów blob platformy Azure. [Dowiedz się więcej na temat zadania pobierania obiektów blob platformy Azure](https://docs.microsoft.com/sql/integration-services/control-flow/azure-blob-download-task?view=sql-server-2017).

Przepływ sterowania jest podobny do poniższego.

![Używanie plików U-SQL w Azure Data Lake Store](./media/data-lake-analytics-schedule-jobs-ssis/use-u-sql-files-in-azure-blob-storage.png)

## <a name="scenario-4-use-u-sql-files-on-the-local-machine"></a>Scenariusz 4 — Używanie plików U-SQL na komputerze lokalnym

Oprócz używania plików U-SQL przechowywanych w chmurze można również używać plików na komputerze lokalnym lub w plikach wdrożonych z pakietami SSIS.

1. Kliknij prawym przyciskiem myszy pozycję **menedżerowie połączeń** w projekcie SSIS i wybierz pozycję **Nowy Menedżer połączeń**.

2. Wybierz typ **pliku** , a następnie kliknij przycisk **Dodaj...**.

3. Ustaw **Typ użycia** na **istniejący plik**i ustaw **plik** na komputerze lokalnym.

    ![Dodaj połączenie z plikiem lokalnym](./media/data-lake-analytics-schedule-jobs-ssis/configure-file-connection-for-foreach-loop-container.png)

4. Dodaj zadanie **Azure Data Lake Analytics** i:
    1. Ustaw **sourceType** na **FileConnection**.
    2. Ustaw **FileConnection** na utworzone połączenie z plikiem.

5. Zakończ inne konfiguracje dla Azure Data Lake Analytics zadania.

## <a name="scenario-5-use-u-sql-statement-in-ssis-variable"></a>Scenariusz 5 — Użycie instrukcji języka U-SQL w zmiennej SSIS

W niektórych przypadkach może być konieczne dynamiczne generowanie instrukcji U-SQL. Można użyć **zmiennej SSIS** z **wyrażeniem SSIS** i innych zadań SSIS, takich jak zadanie skryptu, aby ułatwić generowanie instrukcji języka U-SQL dynamicznie.

1. Otwórz okno narzędzi zmiennych w programie **SSIS >** menu najwyższego poziomu zmiennych.

2. Dodaj zmienną SSIS i ustaw wartość bezpośrednio lub Użyj **wyrażenia** , aby wygenerować wartość.

3. Dodaj **zadanie Azure Data Lake Analytics** i:
    1. Ustaw **sourceType** na wartość **zmienna**.
    2. Ustaw **SourceVariable** na zmienną SSIS utworzoną teraz.

4. Zakończ inne konfiguracje dla Azure Data Lake Analytics zadania.

## <a name="scenario-6-pass-parameters-to-u-sql-script"></a>Scenariusz 6 — Przekazywanie parametrów do skryptu U-SQL

W niektórych przypadkach może być konieczne dynamiczne ustawienie wartości zmiennej U-SQL w skrypcie U-SQL. Funkcja **mapowania parametrów** w pomocy zadania Azure Data Lake Analytics w tym scenariuszu. Zwykle istnieją dwa typowe przypadki użycia:

- Ustaw zmienne ścieżki plików wejściowych i wyjściowych dynamicznie na podstawie bieżącej daty i godziny.
- Ustaw parametr dla procedur składowanych.

[Dowiedz się więcej na temat sposobu ustawiania parametrów dla skryptu U-SQL](https://docs.microsoft.com/sql/integration-services/control-flow/azure-data-lake-analytics-task?view=sql-server-2017#parameter-mapping-page-configuration).

## <a name="next-steps"></a>Następne kroki

- [Uruchamianie pakietów SSIS na platformie Azure](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)
- [Pakiet Azure Feature Pack dla usług Integration Services (SSIS)](https://docs.microsoft.com/sql/integration-services/azure-feature-pack-for-integration-services-ssis?view=sql-server-2017#scenario-managing-data-in-the-cloud)
- [Planowanie zadań U-SQL przy użyciu Azure Data Factory](https://docs.microsoft.com/azure/data-factory/transform-data-using-data-lake-analytics)
