---
title: Działanie procedury składowanej SQL Server
description: Dowiedz się, jak za pomocą działania procedury składowanej SQL Server wywołać procedurę składowaną w Azure SQL Database lub analizie Synapse na platformie Azure z potoku Data Factory.
services: data-factory
documentationcenter: ''
ms.assetid: 1c46ed69-4049-44ec-9b46-e90e964a4a8e
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
author: nabhishek
ms.author: abnarain
manager: anandsub
robots: noindex
ms.openlocfilehash: f702633f6311d4ae4a79452e9b1c9176125c56f9
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/15/2020
ms.locfileid: "97508408"
---
# <a name="sql-server-stored-procedure-activity"></a>Działanie procedury składowanej SQL Server
> [!div class="op_single_selector" title1="Działania transformacji"]
> * [Działanie Hive](data-factory-hive-activity.md)
> * [Aktywność trzody chlewnej](data-factory-pig-activity.md)
> * [Działanie MapReduce](data-factory-map-reduce.md)
> * [Działanie przesyłania strumieniowego Hadoop](data-factory-hadoop-streaming-activity.md)
> * [Działanie platformy Spark](data-factory-spark.md)
> * [Działanie wykonywania wsadowego w usłudze Azure Machine Learning Studio (wersja klasyczna)](data-factory-azure-ml-batch-execution-activity.md)
> * [Działanie aktualizacji zasobów w usłudze Azure Machine Learning Studio (wersja klasyczna)](data-factory-azure-ml-update-resource-activity.md)
> * [Działania procedur składowanych](data-factory-stored-proc-activity.md)
> * [Działania języka U-SQL usługi Data Lake Analytics](data-factory-usql-activity.md)
> * [Niestandardowe działanie platformy .NET](data-factory-use-custom-activities.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Azure Data Factory. Jeśli używasz bieżącej wersji usługi Data Factory, zobacz [Przekształć dane za pomocą działania procedury składowanej w Data Factory](../transform-data-using-stored-procedure.md).

## <a name="overview"></a>Omówienie
Korzystając z działań przekształcania danych w [potoku](data-factory-create-pipelines.md) Data Factory, można przekształcać i przetwarzać dane pierwotne w przewidywania i szczegółowe informacje. Działanie procedury składowanej jest jedną z działań transformacji obsługiwanych przez Data Factory. W tym artykule opisano sposób tworzenia artykułu dotyczącego [działań przekształcania danych](data-factory-data-transformation-activities.md) , który przedstawia ogólne omówienie transformacji danych i obsługiwanych działań transformacji w programie Data Factory.

Możesz użyć działania procedury składowanej, aby wywołać procedurę składowaną w jednym z następujących magazynów danych w przedsiębiorstwie lub na maszynie wirtualnej platformy Azure:

- Azure SQL Database
- Azure Synapse Analytics
- Baza danych SQL Server. Jeśli używasz SQL Server, zainstaluj bramę Zarządzanie danymi na tym samym komputerze, na którym znajduje się baza danych programu, lub na oddzielnej maszynie, która ma dostęp do bazy danych. Zarządzanie danymi Gateway to składnik, który łączy źródła danych lokalnie/na maszynie wirtualnej platformy Azure z usługami w chmurze w bezpieczny i zarządzany sposób. Aby uzyskać szczegółowe informacje, zobacz artykuł dotyczący [bramy zarządzanie danymi](data-factory-data-management-gateway.md) .

> [!IMPORTANT]
> Podczas kopiowania danych do Azure SQL Database lub SQL Server, można skonfigurować działanie **sqlsink** w działaniu Copy, aby wywołać procedurę składowaną za pomocą właściwości **sqlWriterStoredProcedureName** . Aby uzyskać więcej informacji, zobacz [wywoływanie procedury składowanej z działania kopiowania](data-factory-invoke-stored-procedure-from-copy-activity.md). Aby uzyskać szczegółowe informacje na temat właściwości, zobacz następujące artykuły dotyczące łącznika: [Azure SQL Database](data-factory-azure-sql-connector.md#copy-activity-properties), [SQL Server](data-factory-sqlserver-connector.md#copy-activity-properties). Wywoływanie procedury składowanej podczas kopiowania danych do usługi Azure Synapse Analytics przy użyciu działania kopiowania nie jest obsługiwane. Można jednak użyć działania procedury składowanej, aby wywołać procedurę składowaną w usłudze Azure Synapse Analytics.
>
> Podczas kopiowania danych z Azure SQL Database lub SQL Server lub z usługi Azure Synapse Analytics można skonfigurować element **sqlsource** w działaniu Copy, aby wywołać procedurę składowaną w celu odczytania danych ze źródłowej bazy danych przy użyciu właściwości **sqlReaderStoredProcedureName** . Aby uzyskać więcej informacji, zobacz następujące artykuły dotyczące łącznika: [Azure SQL Database](data-factory-azure-sql-connector.md#copy-activity-properties), [SQL Server](data-factory-sqlserver-connector.md#copy-activity-properties), [Azure Synapse Analytics](data-factory-azure-sql-data-warehouse-connector.md#copy-activity-properties)

Poniższy przewodnik używa działania procedury składowanej w potoku do wywołania procedury składowanej w Azure SQL Database.

## <a name="walkthrough"></a>Przewodnik
### <a name="sample-table-and-stored-procedure"></a>Przykładowa tabela i procedura składowana
1. Utwórz poniższą **tabelę** w Azure SQL Database przy użyciu SQL Server Management Studio lub dowolnego innego narzędzia, z którym masz doświadczenie. Kolumna znacznikdatygodziny to data i godzina wygenerowania odpowiedniego identyfikatora.

    ```SQL
    CREATE TABLE dbo.sampletable
    (
        Id uniqueidentifier,
        datetimestamp nvarchar(127)
    )
    GO

    CREATE CLUSTERED INDEX ClusteredID ON dbo.sampletable(Id);
    GO
    ```
    Identyfikator jest unikatowym zidentyfikowanym, a kolumna znacznikdatygodziny to data i godzina wygenerowania odpowiedniego identyfikatora.
    
    ![Przykładowe dane](./media/data-factory-stored-proc-activity/sample-data.png)

    W tym przykładzie procedura składowana znajduje się w Azure SQL Database. Jeśli procedura składowana znajduje się w usłudze Azure Synapse Analytics i bazie danych SQL Server, podejście jest podobne. W przypadku bazy danych SQL Server należy zainstalować [bramę zarządzanie danymi](data-factory-data-management-gateway.md).
2. Utwórz następującą **procedurę składowaną** , która wstawia dane do **próbki**.

    ```SQL
    CREATE PROCEDURE usp_sample @DateTime nvarchar(127)
    AS

    BEGIN
        INSERT INTO [sampletable]
        VALUES (newid(), @DateTime)
    END
    ```

   > [!IMPORTANT]
   > **Nazwa** i **wielkość liter** parametru (DateTime w tym przykładzie) muszą być zgodne z parametrem określonym w kodzie JSON potoku/działania. Upewnij się, że w definicji procedury składowanej **\@** użyto jako prefiksu parametru.

### <a name="create-a-data-factory"></a>Tworzenie fabryki danych
1. Zaloguj się do [Azure Portal](https://portal.azure.com/).
2. Kliknij pozycję **Nowy** w menu po lewej stronie, kliknij pozycję **Analiza i analiza**, a następnie kliknij pozycję **Data Factory**.

    ![Nowa fabryka danych 1](media/data-factory-stored-proc-activity/new-data-factory.png)
3. W bloku **Nowa fabryka danych** wprowadź **SProcDF** w polu Nazwa. Nazwy Azure Data Factory są **unikatowe globalnie**. Aby umożliwić pomyślne utworzenie fabryki, należy poprzedzić nazwę fabryki danych nazwą.

   ![Nowa fabryka danych 2](media/data-factory-stored-proc-activity/new-data-factory-blade.png)
4. Wybierz swoją **subskrypcję platformy Azure**.
5. W obszarze **Grupa zasobów** wykonaj jedną z następujących czynności:
   1. Kliknij pozycję **Utwórz nową** i wprowadź nazwę grupy zasobów.
   2. Kliknij pozycję **Użyj istniejącej** i wybierz istniejącą grupę zasobów.
6. Na liście **lokalizacja** wybierz lokalizację fabryki danych.
7. Wybierz pozycję **Przypnij do pulpitu nawigacyjnego** , aby wyświetlić fabrykę danych na pulpicie nawigacyjnym przy następnym logowaniu.
8. Kliknij przycisk **Utwórz** w bloku **Nowa fabryka danych**.
9. Zostanie wyświetlona Fabryka danych utworzona na **pulpicie nawigacyjnym** Azure Portal. Po pomyślnym utworzeniu fabryki danych zostanie wyświetlona strona fabryki danych z zawartością fabryki danych.

   ![Strona główna Data Factory](media/data-factory-stored-proc-activity/data-factory-home-page.png)

### <a name="create-an-azure-sql-linked-service"></a>Tworzenie połączonej usługi Azure SQL
Po utworzeniu fabryki danych utworzysz połączoną usługę Azure SQL, która łączy bazę danych w Azure SQL Database, która zawiera tabelę przykładową i usp_sample procedury składowanej, do fabryki danych.

1. Kliknij przycisk **Utwórz i Wdróż** w bloku **Data Factory** , aby **SProcDF** uruchomić Edytor Data Factory.
2. Kliknij pozycję **nowy magazyn danych** na pasku poleceń i wybierz **Azure SQL Database**. W edytorze powinien zostać wyświetlony skrypt JSON dotyczący tworzenia połączonej usługi Azure SQL.

   ![Nowy magazyn danych 1](media/data-factory-stored-proc-activity/new-data-store.png)
3. W skrypcie JSON wprowadź następujące zmiany:

   1. Zamień `<servername>` na nazwę serwera.
   2. Zamień na `<databasename>` bazę danych, w której utworzono tabelę oraz procedurę przechowywaną.
   3. Zamień na `<username@servername>` konto użytkownika, które ma dostęp do bazy danych.
   4. Zamień na `<password>` hasło dla konta użytkownika.

      ![Nowy magazyn danych 2](media/data-factory-stored-proc-activity/azure-sql-linked-service.png)
4. Aby wdrożyć połączoną usługę, kliknij przycisk **Wdróż** na pasku poleceń. Upewnij się, że AzureSqlLinkedService jest widoczny w widoku drzewa po lewej stronie.

    ![Widok drzewa z połączoną usługą 1](media/data-factory-stored-proc-activity/tree-view.png)

### <a name="create-an-output-dataset"></a>Tworzenie wyjściowego zestawu danych
Należy określić wyjściowy zestaw danych dla działania procedury składowanej, nawet jeśli procedura składowana nie wygenerowała żadnych danych. Jest to spowodowane tym, że jest to wyjściowy zestaw danych, który steruje harmonogramem działania (częstotliwość uruchamiania aktywności — co godzinę, codziennie itd.). Wyjściowy zestaw danych musi używać **połączonej usługi** , która odnosi się do Azure SQL Database lub usługi Azure Synapse Analytics lub bazy danych SQL Server, w której ma zostać uruchomiona procedura składowana. Wyjściowy zestaw danych może stanowić sposób przekazania wyniku procedury składowanej w celu późniejszego przetworzenia przez inne działanie ([łańcuch działań](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline) w potoku. Jednak Data Factory nie zapisuje automatycznie danych wyjściowych procedury składowanej do tego zestawu danych. Jest to procedura składowana, która zapisuje w tabeli SQL, do której wskazuje wyjściowy zestaw danych. W niektórych przypadkach wyjściowy zestaw danych może być **fikcyjnym zestawem danych** (zestawem danych, który wskazuje na tabelę, która nie przechowuje danych wyjściowych procedury składowanej). Ten fikcyjny zestaw danych służy tylko do określenia harmonogramu uruchamiania działania procedury składowanej.

1. Kliknij przycisk **... Więcej** na pasku narzędzi kliknij pozycję **Nowy zestaw danych**, a następnie kliknij pozycję **Azure SQL**. **Nowy zestaw danych** na pasku poleceń i wybierz pozycję **Azure SQL**.

    ![Widok drzewa z połączoną usługą 2](media/data-factory-stored-proc-activity/new-dataset.png)
2. Skopiuj/wklej następujący skrypt JSON w edytorze JSON.

    ```JSON
    {
        "name": "sprocsampleout",
        "properties": {
            "type": "AzureSqlTable",
            "linkedServiceName": "AzureSqlLinkedService",
            "typeProperties": {
                "tableName": "sampletable"
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }
    ```
3. Aby wdrożyć zestaw danych, kliknij przycisk **Wdróż** na pasku poleceń. Upewnij się, że zestaw danych jest widoczny w widoku drzewa.

    ![Widok drzewa z połączonymi usługami](media/data-factory-stored-proc-activity/tree-view-2.png)

### <a name="create-a-pipeline-with-sqlserverstoredprocedure-activity"></a>Tworzenie potoku za pomocą działania SqlServerStoredProcedure
Teraz Utwórzmy potok za pomocą działania procedury składowanej.

Zwróć uwagę na następujące właściwości:

- Właściwość **Type** jest ustawiona na wartość **SqlServerStoredProcedure**.
- **StoredProcedureName** we właściwościach typu jest ustawiona na **usp_sample** (nazwa procedury składowanej).
- Sekcja **storedProcedureParameters** zawiera jeden parametr o nazwie **DateTime**. Nazwa i wielkość liter parametru w formacie JSON muszą być zgodne z nazwą i wielkością liter parametru w definicji procedury składowanej. Jeśli chcesz przekazać wartość null dla parametru, użyj składni: `"param1": null` (wszystkie małe litery).

1. Kliknij przycisk **... Więcej** na pasku poleceń i kliknij opcję **Nowy potok**.
2. Skopiuj/wklej następujący fragment kodu JSON:

    ```JSON
    {
        "name": "SprocActivitySamplePipeline",
        "properties": {
            "activities": [
                {
                    "type": "SqlServerStoredProcedure",
                    "typeProperties": {
                        "storedProcedureName": "usp_sample",
                        "storedProcedureParameters": {
                            "DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)"
                        }
                    },
                    "outputs": [
                        {
                            "name": "sprocsampleout"
                        }
                    ],
                    "scheduler": {
                        "frequency": "Hour",
                        "interval": 1
                    },
                    "name": "SprocActivitySample"
                }
            ],
            "start": "2017-04-02T00:00:00Z",
            "end": "2017-04-02T05:00:00Z",
            "isPaused": false
        }
    }
    ```
3. Aby wdrożyć potok, kliknij przycisk **Wdróż** na pasku narzędzi.

### <a name="monitor-the-pipeline"></a>Monitorowanie potoku
1. Kliknij przycisk **X**, aby zamknąć bloki Edytora fabryki danych i przejść z powrotem do bloku Fabryka danych, a następnie kliknij przycisk **Diagram**.

    ![Diagram kafelka 1](media/data-factory-stored-proc-activity/data-factory-diagram-tile.png)
2. W **widoku diagramu** zostanie wyświetlony przegląd potoków i zestawów danych używanych w tym samouczku.

    ![Diagram kafelka 2](media/data-factory-stored-proc-activity/data-factory-diagram-view.png)
3. W widoku diagramu kliknij dwukrotnie zestaw danych `sprocsampleout` . Wycinki są wyświetlane w stanie gotowe. Powinna być pięć wycinków, ponieważ wycinek jest generowany przez każdą godzinę między czasem rozpoczęcia i czasem zakończenia z pliku JSON.

    ![Diagram kafelka 3](media/data-factory-stored-proc-activity/data-factory-slices.png)
4. Gdy wycinek jest w stanie **gotowe** , uruchom `select * from sampletable` zapytanie względem bazy danych, aby sprawdzić, czy dane zostały wstawione do tabeli przez procedurę składowaną.

   ![Dane wyjściowe](./media/data-factory-stored-proc-activity/output.png)

   Aby uzyskać szczegółowe informacje na temat monitorowania potoków Azure Data Factory [, zobacz Monitorowanie potoku](data-factory-monitor-manage-pipelines.md) .

## <a name="specify-an-input-dataset"></a>Określ wejściowy zestaw danych
W instruktażu działanie procedury składowanej nie ma żadnych wejściowych zestawów danych. Jeśli określisz wejściowy zestaw danych, działanie procedury składowanej nie zostanie uruchomione do momentu udostępnienia wycinka wejściowego zestawu danych (w stanie gotowe). Zestaw danych może być zewnętrznym zestawem danych (który nie jest tworzony przez inne działanie w tym samym potoku) lub wewnętrzny zestaw danych, który jest tworzony przez działanie nadrzędne (działanie uruchomione przed tym działaniem). Można określić wiele zestawów danych wejściowych dla działania procedury składowanej. W takim przypadku działanie procedury składowanej zostanie uruchomione tylko wtedy, gdy wszystkie wycinki zestawu danych wejściowych są dostępne (w stanie gotowe). Wejściowy zestaw danych nie może być używany w procedurze składowanej jako parametr. Jest on używany tylko do sprawdzania zależności przed rozpoczęciem działania procedury składowanej.

## <a name="chaining-with-other-activities"></a>Tworzenie łańcuchów z innymi działaniami
Jeśli chcesz utworzyć łańcuch działania nadrzędnego z tym działaniem, określ dane wyjściowe działania nadrzędnego jako dane wejściowe tego działania. Gdy to zrobisz, działanie procedury składowanej nie zostanie uruchomione do czasu zakończenia działania nadrzędnego, a wyjściowy zestaw danych działania nadrzędnego jest dostępny (w stanie gotowe). Można określić wyjściowe zestawy danych dla wielu działań nadrzędnych jako wejściowe zestawy danych działania procedury składowanej. Gdy to zrobisz, działanie procedury składowanej zostanie uruchomione tylko wtedy, gdy są dostępne wszystkie wycinki zestawu danych wejściowych.

W poniższym przykładzie dane wyjściowe działania Copy to: OutputDataset, czyli dane wejściowe działania procedury składowanej. W związku z tym działanie procedury składowanej nie jest uruchamiane do momentu ukończenia działania kopiowania i OutputDataset wycinka (w stanie gotowe). W przypadku określenia wielu zestawów danych wejściowych działanie procedury składowanej nie zostanie uruchomione do momentu udostępnienia wszystkich wycinków wejściowego zestawu danych (w stanie gotowe). Wejściowe zestawy danych nie mogą być używane bezpośrednio jako parametry działania procedury składowanej.

Aby uzyskać więcej informacji na temat łańcucha działań, zobacz [wiele działań w potoku](data-factory-create-pipelines.md#multiple-activities-in-a-pipeline)

```json
{
    "name": "ADFTutorialPipeline",
    "properties": {
        "description": "Copy data from a blob to blob",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [ { "name": "InputDataset" } ],
                "outputs": [ { "name": "OutputDataset" } ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst"
                },
                "name": "CopyFromBlobToSQL"
            },
            {
                "type": "SqlServerStoredProcedure",
                "typeProperties": {
                    "storedProcedureName": "SPSproc"
                },
                "inputs": [ { "name": "OutputDataset" } ],
                "outputs": [ { "name": "SQLOutputDataset" } ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "retry": 3
                },
                "name": "RunStoredProcedure"
            }
        ],
        "start": "2017-04-12T00:00:00Z",
        "end": "2017-04-13T00:00:00Z",
        "isPaused": false,
    }
}
```

Analogicznie, aby połączyć aktywność procedury magazynu z **działaniami podrzędnymi** (działania wykonywane po zakończeniu działania procedury składowanej), określ wyjściowy zestaw danych działania procedury składowanej jako dane wejściowe działania podrzędnego w potoku.

> [!IMPORTANT]
> Podczas kopiowania danych do Azure SQL Database lub SQL Server, można skonfigurować działanie **sqlsink** w działaniu Copy, aby wywołać procedurę składowaną za pomocą właściwości **sqlWriterStoredProcedureName** . Aby uzyskać więcej informacji, zobacz [wywoływanie procedury składowanej z działania kopiowania](data-factory-invoke-stored-procedure-from-copy-activity.md). Aby uzyskać szczegółowe informacje na temat właściwości, zobacz następujące artykuły dotyczące łącznika: [Azure SQL Database](data-factory-azure-sql-connector.md#copy-activity-properties), [SQL Server](data-factory-sqlserver-connector.md#copy-activity-properties).
> 
> Podczas kopiowania danych z Azure SQL Database lub SQL Server lub z usługi Azure Synapse Analytics można skonfigurować element **sqlsource** w działaniu Copy, aby wywołać procedurę składowaną w celu odczytania danych ze źródłowej bazy danych przy użyciu właściwości **sqlReaderStoredProcedureName** . Aby uzyskać więcej informacji, zobacz następujące artykuły dotyczące łącznika: [Azure SQL Database](data-factory-azure-sql-connector.md#copy-activity-properties), [SQL Server](data-factory-sqlserver-connector.md#copy-activity-properties), [Azure Synapse Analytics](data-factory-azure-sql-data-warehouse-connector.md#copy-activity-properties)

## <a name="json-format"></a>Format JSON
Oto format JSON służący do definiowania działania procedury składowanej:

```JSON
{
    "name": "SQLSPROCActivity",
    "description": "description",
    "type": "SqlServerStoredProcedure",
    "inputs": [ { "name": "inputtable" } ],
    "outputs": [ { "name": "outputtable" } ],
    "typeProperties":
    {
        "storedProcedureName": "<name of the stored procedure>",
        "storedProcedureParameters":
        {
            "param1": "param1Value"
            …
        }
    }
}
```

W poniższej tabeli opisano te właściwości JSON:

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| name | Nazwa działania |Tak |
| description |Tekst opisujący działanie używanego działania |Nie |
| typ | Musi być ustawiona na: **SqlServerStoredProcedure** | Tak |
| danych wejściowych | Opcjonalny. Jeśli określisz wejściowy zestaw danych, musi on być dostępny (w stanie "gotowe") do uruchomienia działania procedury składowanej. Wejściowy zestaw danych nie może być używany w procedurze składowanej jako parametr. Jest on używany tylko do sprawdzania zależności przed rozpoczęciem działania procedury składowanej. |Nie |
| wydajności | Należy określić wyjściowy zestaw danych dla działania procedury składowanej. Wyjściowy zestaw danych określa **harmonogram** działania procedury składowanej (co godzinę, co tydzień, co miesiąc itd.). <br/><br/>Wyjściowy zestaw danych musi używać **połączonej usługi** , która odnosi się do Azure SQL Database lub usługi Azure Synapse Analytics lub bazy danych SQL Server, w której ma zostać uruchomiona procedura składowana. <br/><br/>Wyjściowy zestaw danych może stanowić sposób przekazania wyniku procedury składowanej w celu późniejszego przetworzenia przez inne działanie ([łańcuch działań](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline) w potoku. Jednak Data Factory nie zapisuje automatycznie danych wyjściowych procedury składowanej do tego zestawu danych. Jest to procedura składowana, która zapisuje w tabeli SQL, do której wskazuje wyjściowy zestaw danych. <br/><br/>W niektórych przypadkach wyjściowy zestaw danych może być **fikcyjnym zestawem danych**, który jest używany tylko do określenia harmonogramu uruchamiania działania procedury składowanej. |Tak |
| storedProcedureName |Określ nazwę procedury składowanej w Azure SQL Database, Azure Synapse Analytics lub SQL Server reprezentowanej przez połączoną usługę używaną przez tabelę wyjściową. |Tak |
| storedProcedureParameters |Określ wartości parametrów procedury składowanej. Jeśli musisz przekazać wartość null dla parametru, użyj składni: "param1": null (wszystkie małe litery). Zapoznaj się z poniższym przykładem, aby dowiedzieć się więcej o używaniu tej właściwości. |Nie |

## <a name="passing-a-static-value"></a>Przekazywanie wartości statycznej
Teraz Rozważmy dodanie innej kolumny o nazwie "scenariusz" w tabeli zawierającej wartość statyczną o nazwie "Document sample".

![Dane przykładowe 2](./media/data-factory-stored-proc-activity/sample-data-2.png)

**Tabele**

```SQL
CREATE TABLE dbo.sampletable2
(
    Id uniqueidentifier,
    datetimestamp nvarchar(127),
    scenario nvarchar(127)
)
GO

CREATE CLUSTERED INDEX ClusteredID ON dbo.sampletable2(Id);
```

**Procedura składowana:**

```SQL
CREATE PROCEDURE usp_sample2 @DateTime nvarchar(127) , @Scenario nvarchar(127)

AS

BEGIN
    INSERT INTO [sampletable2]
    VALUES (newid(), @DateTime, @Scenario)
END
```

Teraz Przekaż parametr **scenariusza** i wartość z działania procedury składowanej. Sekcja **typeProperties** w powyższym przykładzie wygląda jak w poniższym fragmencie kodu:

```JSON
"typeProperties":
{
    "storedProcedureName": "usp_sample",
    "storedProcedureParameters":
    {
        "DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)",
        "Scenario": "Document sample"
    }
}
```

**Data Factory zestaw danych:**

```JSON
{
    "name": "sprocsampleout2",
    "properties": {
        "published": false,
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties": {
            "tableName": "sampletable2"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**Potok Data Factory**

```JSON
{
    "name": "SprocActivitySamplePipeline2",
    "properties": {
        "activities": [
            {
                "type": "SqlServerStoredProcedure",
                "typeProperties": {
                    "storedProcedureName": "usp_sample2",
                    "storedProcedureParameters": {
                        "DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)",
                        "Scenario": "Document sample"
                    }
                },
                "outputs": [
                    {
                        "name": "sprocsampleout2"
                    }
                ],
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "SprocActivitySample"
            }
        ],
        "start": "2016-10-02T00:00:00Z",
        "end": "2016-10-02T05:00:00Z"
    }
}
```
