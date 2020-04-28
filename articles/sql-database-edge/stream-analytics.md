---
title: Używanie SQL Database pakietów DAC i Stream Analytics zadań z Azure SQL Database Edge | Microsoft Docs
description: Dowiedz się więcej o używaniu Stream Analytics zadań w SQL Database Edge
keywords: Baza danych SQL Database, Stream Analytics, sqlpackage
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 11/04/2019
ms.openlocfilehash: 21a8bb6953fd879b17816361f536596571678697
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/27/2020
ms.locfileid: "74384155"
---
# <a name="using-sql-database-dac-packages-and-stream-analytics-jobs-with-sql-database-edge"></a>Używanie SQL Database pakietów DAC i zadań Stream Analytics z SQL Database Edge

Azure SQL Database Edge — wersja zapoznawcza to zoptymalizowany aparat relacyjnej bazy danych umożliwiający wdrażanie IoT i Edge. Jest on oparty na najnowszych wersjach aparatu bazy danych Microsoft SQL Server, który zapewnia wiodące w branży funkcje, zabezpieczenia i przetwarzanie zapytań. Dzięki wiodącym w branży funkcjom zarządzania relacyjnymi bazami danych SQL Server Azure SQL Database Edge zapewnia wbudowaną funkcję przesyłania strumieniowego na potrzeby analiz w czasie rzeczywistym i skomplikowanego przetwarzania zdarzeń.

Azure SQL Database Edge zawiera również natywną implementację programu sqlpackage. exe, która umożliwia wdrożenie pakietu [SQL Database DAC](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications) podczas wdrażania SQL Database Edge.

Azure SQL Database Edge uwidacznia dwa opcjonalne parametry za pomocą `module twin's desired properties` opcji modułu IoT Edge:

```json
{
    "properties.desired":
    {
        "SqlPackage": "<Optional_DACPAC_ZIP_SAS_URL>",
        "ASAJobInfo": "<Optional_ASA_Job_ZIP_SAS_URL>"
    }
}
```

|Pole | Opis |
|------|-------------|
| SqlPackage | Identyfikator URI magazynu obiektów blob platformy Azure dla pliku *. zip, który zawiera pakiet SQL Database DAC.
| ASAJobInfo | Identyfikator URI magazynu obiektów blob platformy Azure dla zadania ASA Edge. Aby uzyskać więcej informacji, zobacz [Publikowanie zadania ASA Edge dla SQL Database Edge](/azure/sql-database-edge/stream-analytics#using-streaming-jobs-with-sql-database-edge).

## <a name="using-sql-database-dac-packages-with-sql-database-edge"></a>Używanie SQL Database pakietów DAC z SQL Database Edge

Aby użyć SQL Database pakietu DAC (*. dacpac) z SQL Database Edge, wykonaj następujące kroki:

1. Utwórz lub Wyodrębnij SQL Database pakiet DAC. Zobacz [wyodrębnianie aplikacji DAC z bazy danych,](/sql/relational-databases/data-tier-applications/extract-a-dac-from-a-database/) Aby uzyskać informacje na temat generowania pakietu DAC dla istniejącej bazy danych SQL Server.

2. Zip *. dacpac i przekaż go do konta usługi Azure Blob Storage. Aby uzyskać więcej informacji na temat przekazywania plików do usługi Azure Blob Storage, zobacz [przekazywanie, pobieranie i wyświetlanie listy obiektów BLOB za pomocą Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md).

3. Wygeneruj sygnaturę dostępu współdzielonego dla pliku zip przy użyciu Azure Portal. Aby uzyskać więcej informacji, zobacz [delegowanie dostępu za pomocą sygnatur dostępu współdzielonego (SAS)](../storage/common/storage-sas-overview.md).

4. Zaktualizuj konfigurację modułu SQL Database Edge, aby uwzględnić identyfikator URI dostępu współdzielonego dla pakietu DAC. Aby zaktualizować moduł SQL Database Edge, wykonaj następujące kroki:

    1. W Azure Portal przejdź do wdrożenia IoT Hub.

    2. W lewym okienku wybierz pozycję **IoT Edge**.

    3. Na stronie **IoT Edge** Znajdź i wybierz pozycję IoT Edge, w której wdrożono moduł SQL Database Edge.

    4. Na stronie urządzenie **IoT Edge** wybierz pozycję **Ustaw moduł**.

    5. Na stronie **Ustawianie modułów** wybierz pozycję **konfiguruj** względem modułu SQL Database Edge.

    6. W okienku **IoT Edge modułów niestandardowych** wybierz opcję **Ustaw odpowiednie właściwości sznurka modułu**. Zaktualizuj odpowiednie właściwości, aby uwzględnić identyfikator URI dla `SQLPackage` opcji, jak pokazano w poniższym przykładzie.

        > [!NOTE]
        > Identyfikator URI sygnatury dostępu współdzielonego w poniższym kodzie JSON jest tylko przykładem. Zastąp identyfikator URI rzeczywistym identyfikatorem URI z wdrożenia.

        ```json
            {
                "properties.desired":
                {
                    "SqlPackage": "https://StorageAccountName.blob.core.windows.net/SQLpackageFiles/MeasurementsDB.zip?sp=r&st=2019-09-01T00:00:00Z&se=2019-09-30T00:00:00Z&spr=https&sv=2019-02-02&sr=b&sig=Uh8X0E50qzlxkiKVBJKU3ccVQYwF235qTz7AXp4R3gI%3D",
                }
            }
        ```

    7. Wybierz pozycję **Zapisz**.

    8. Na stronie **Ustawianie modułów** wybierz pozycję **dalej**.

    9. Na stronie **Ustawianie modułów** wybierz pozycję **dalej** , a następnie **Prześlij**.

5. Po aktualizacji modułu plik pakietu DAC zostanie pobrany, rozpakowany i wdrożony w odniesieniu do wystąpienia SQL Database krawędzi.

## <a name="using-streaming-jobs-with-sql-database-edge"></a>Używanie zadań przesyłania strumieniowego z SQL Database Edge

Azure SQL Database Edge ma natywną implementację środowiska uruchomieniowego usługi Stream Analytics. Ta implementacja pozwala utworzyć zadanie Azure Stream Analytics Edge i wdrożyć to zadanie jako SQL Database zadanie przesyłania strumieniowego Edge. Aby utworzyć zadanie Stream Analytics Edge, wykonaj następujące kroki:

1. Przejdź do Azure Portal przy użyciu [adresu URL](https://portal.azure.com/?microsoft_azure_streamanalytics_edgeadapterspreview=true)w wersji zapoznawczej. Ten adres URL wersji zapoznawczej umożliwia skonfigurowanie SQL Database danych wyjściowych dla zadania Stream Analytics Edge.

2. Utwórz nowe zadanie **Azure Stream Analytics na urządzeniach IoT Edge** . Wybierz środowisko hostingu, które jest przeznaczone dla **krawędzi**.

3. Zdefiniuj dane wejściowe i wyjściowe dla zadania Azure Stream Analytics. Wszystkie dane wyjściowe SQL, które należy skonfigurować w tym miejscu, są powiązane z pojedynczą tabelą w bazie danych. Jeśli zachodzi potrzeba przesyłania strumieniowego danych do wielu tabel, należy utworzyć wiele danych wyjściowych SQL Database. Można skonfigurować dane wyjściowe SQL, aby wskazywały na różne bazy danych.

    **Dane wejściowe**. Wybierz EdgeHub jako dane wejściowe dla zadania brzegowego i podaj informacje o zasobie.

    **Dane wyjściowe**. Wybierz SQL Database jako dane wyjściowe. Wybierz opcję **podaj SQL Database ustawienia ręcznie**. Podaj szczegóły konfiguracji bazy danych i tabeli.

    |Pole      | Opis |
    |---------------|-------------|
    |Alias danych wyjściowych | Nazwa aliasu danych wyjściowych.|
    |baza danych | Nazwa bazy danych SQL. Musi być prawidłową nazwą bazy danych, która istnieje w wystąpieniu SQL Database Edge.|
    |Nazwa serwera | Nazwa (lub adres IP) i numer portu dla wystąpienia programu SQL Server. W przypadku wdrożenia SQL Database Edge można użyć **protokołu TCP:., 1433** jako nazwy serwera.|
    |Nazwa użytkownika | Konto logowania SQL, które ma dostęp do czytnika danych i składnika zapisywania danych do bazy danych określonej wcześniej.|
    |Hasło | Hasło do konta logowania SQL, które zostało określone wcześniej.|
    |Tabela | Nazwa tabeli, która będzie wyprowadzana dla zadania przesyłania strumieniowego.|
    |Dziedzicz partycjonowanie| Włącza dziedziczenie schematu partycjonowania poprzedniego kroku zapytania lub danych wejściowych. Gdy ta opcja jest włączona, można oczekiwać, że lepsza przepływność jest wyświetlana podczas zapisywania w tabeli opartej na dyskach i ma w pełni równoległą topologię dla danego zadania.|
    |Rozmiar wsadu| Maksymalna liczba rekordów wysyłanych z każdą zbiorczą transakcją wstawiania.|

    Oto Przykładowa konfiguracja danych wejściowych/wyjściowych:

    ```txt
        Input:
            Input from EdgeHub
            Input alias: input
            Event serialization format: JSON
            Encoding: UTF-8
            Event compression type: None

        Output:
            Output alias: output
            Database:  MeasurementsDB
            Server name: tcp:.,1433
            Username: sa
            Password: <Password>
            Table: TemperatureMeasurements
            Inherit Partitioning: Merge all input partitions into a single writer
            Max batch count: 10000
    ```

    > [!NOTE]
    > Aby uzyskać więcej informacji na temat karty danych wyjściowych SQL dla Azure Stream Analytics, zobacz [Azure Stream Analytics danych wyjściowych do Azure SQL Database](../stream-analytics/stream-analytics-sql-output-perf.md).

4. Zdefiniuj zapytanie dotyczące zadania ASA dla zadania Edge. To zapytanie powinno używać zdefiniowanych aliasów Input/Output jako nazw wejściowych i wyjściowych w zapytaniu. Aby uzyskać więcej informacji, zobacz [Stream Analytics Dokumentacja języka zapytań](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference).

5. Ustaw ustawienia konta magazynu dla zadania brzegowego. Konto magazynu jest używane jako miejsce docelowe publikowania dla zadania brzegowego.

6. W obszarze **Konfiguracja**wybierz pozycję **Publikuj**, a następnie wybierz przycisk **Publikuj** . Zapisz identyfikator URI sygnatury dostępu współdzielonego do użycia z modułem SQL Database Edge.

### <a name="deploy-the-stream-analytics-edge-job-to-sql-database-edge"></a>Wdróż zadanie Stream Analytics Edge w SQL Database Edge

Aby wdrożyć zadanie przesyłania strumieniowego do modułu SQL Database Edge, zaktualizuj konfigurację modułu SQL Database Edge w celu uwzględnienia identyfikatora URI sygnatury dostępu współdzielonego dla zadania przesyłania strumieniowego z wcześniejszego kroku. Aby zaktualizować moduł SQL Database Edge:

1. W Azure Portal przejdź do wdrożenia IoT Hub.

2. W lewym okienku wybierz pozycję **IoT Edge**.

3. Na stronie **IoT Edge** Znajdź i wybierz pozycję IoT Edge, w której wdrożono moduł SQL Database Edge.

4. Na stronie urządzenie **IoT Edge** wybierz pozycję **Ustaw moduł**.

5. Na stronie **Ustawianie modułów** wybierz pozycję **konfiguruj** względem modułu SQL Database Edge.

6. W okienku **IoT Edge modułów niestandardowych** wybierz opcję **Ustaw odpowiednie właściwości sznurka modułu**. Zaktualizuj odpowiednie właściwości, aby uwzględnić identyfikator URI dla `ASAJobInfo` opcji, jak pokazano w poniższym przykładzie.

    > [!NOTE]
    > Identyfikator URI sygnatury dostępu współdzielonego w poniższym kodzie JSON jest tylko przykładem. Zastąp identyfikator URI rzeczywistym identyfikatorem URI z wdrożenia.

    ```json
        {
            "properties.desired":
            {
                "ASAJobInfo": "https://storageAccountName.blob.core.windows.net/asajobs/ASAEdgeJobs/5a9b34db-7a5c-4606-adae-4c8609eaa1c7/d85b5aa6-4d38-4703-bb34-af7f0bd7916d/ASAEdgeJobDefinition.zip?sv=2018-03-28&sr=b&sig=HH%2BFMsEy378RaTxIy2Xo6rM6wDaqoBaZ5zFDBqdZiS0%3D&st=2019-09-01T22%3A24%3A34Z&se=2019-09-30T22%3A34%3A34Z&sp=r"   
            }
        }
    ```

7. Wybierz pozycję **Zapisz**.

8. Na stronie **Ustawianie modułów** wybierz pozycję **dalej**.

9. Na stronie **Ustawianie modułów** wybierz pozycję **dalej** , a następnie **Prześlij**.

10. Po aktualizacji modułu plik zadania usługi Stream Analytics zostanie pobrany, rozpakowany i wdrożony dla wystąpienia SQL Database Edge.

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać szczegółowe informacje o cenach i dostępności, zobacz [Azure SQL Database Edge](https://azure.microsoft.com/services/sql-database-edge/).
- Żądanie włączenia Azure SQL Database krawędzi dla subskrypcji.
- Aby rozpocząć, zobacz [wdrażanie SQL Database Edge przez Azure Portal](deploy-portal.md).
