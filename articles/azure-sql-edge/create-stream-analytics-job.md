---
title: Tworzenie zadania przesyłania strumieniowego T-SQL w usłudze Azure SQL Edge (wersja zapoznawcza)
description: Dowiedz się więcej o tworzeniu Stream Analytics zadań w usłudze Azure SQL Edge (wersja zapoznawcza)
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 4b09df3110907d58badda2c389b9ee39a9b02532
ms.sourcegitcommit: ce44069e729fce0cf67c8f3c0c932342c350d890
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/09/2020
ms.locfileid: "84636193"
---
# <a name="create-stream-analytics-job-in-azure-sql-edge-preview"></a>Tworzenie zadania Stream Analytics w usłudze Azure SQL Edge (wersja zapoznawcza) 

W tym artykule wyjaśniono, jak utworzyć zadanie przesyłania strumieniowego T-SQL w usłudze Azure SQL Edge (wersja zapoznawcza). Aby utworzyć zadanie przesyłania strumieniowego w usłudze SQL Edge, wymagane są następujące kroki

1. Tworzenie zewnętrznych obiektów wejściowych i wyjściowych strumienia strumieniowego
2. Zdefiniuj kwerendę zadania przesyłania strumieniowego w ramach tworzenia zadania przesyłania strumieniowego.

> [!NOTE]
> Aby włączyć funkcję przesyłania strumieniowego T-SQL w usłudze Azure SQL Edge, należy włączyć TF 11515 jako opcję uruchamiania lub użyć polecenia [DBCC TRACEON]( https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-transact-sql) . Aby uzyskać więcej informacji na temat włączania flag śledzenia przy użyciu pliku MSSQL. conf, zobacz [Konfigurowanie przy użyciu pliku MSSQL. conf](configure.md#configure-by-using-an-mssqlconf-file). Ten wymóg zostanie usunięty w przyszłych aktualizacjach usługi Azure SQL Edge (wersja zapoznawcza).

## <a name="configure-an-external-stream-input-and-output-object"></a>Skonfiguruj zewnętrzny obiekt danych wejściowych i wyjściowych strumienia

Funkcja przesyłania strumieniowego T-SQL korzysta z funkcji zewnętrznego źródła danych SQL Server, aby zdefiniować źródła danych skojarzone z zewnętrznymi danymi wejściowymi strumienia i wyjściem zadania przesyłania strumieniowego. Następujące polecenia języka T-SQL są wymagane do utworzenia zewnętrznego obiektu wejściowego lub wyjściowego strumienia.

[CREATE EXTERNAL FILE FORMAT (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-external-file-format-transact-sql)

[CREATE EXTERNAL DATA SOURCE (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-external-data-source-transact-sql)

[Utwórz strumień zewnętrzny (Transact-SQL)](#example-create-an-external-stream-object-sql-database)

Ponadto w przypadku programu SQL Edge (lub SQL Server, Azure SQL) używanego jako strumień wyjściowy, polecenie [CREATE DATABASE scoped Credential (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-database-scoped-credential-transact-sql) T-SQL jest wymagane do zdefiniowania poświadczeń w celu uzyskania dostępu do bazy danych SQL.

### <a name="supported-input-and-output-stream-data-sources"></a>Obsługiwane źródła danych wejściowych i wyjściowych strumieni

Usługa Azure SQL Edge obecnie obsługuje tylko następujące źródła danych jako dane wejściowe i wyjściowe strumienia.

| Typ źródła danych | Dane wejściowe | Dane wyjściowe | Opis |
|------------------|-------|--------|------------------|
| Azure IoT Edge Hub | T | T | Źródło danych do odczytu/zapisu danych przesyłanych strumieniowo do centrum Azure IoT Edge. Aby uzyskać więcej informacji na Azure IoT Edge centrum, zapoznaj się z [centrum IoT Edge](https://docs.microsoft.com/azure/iot-edge/iot-edge-runtime#iot-edge-hub)|
| SQL Database | N | T | Połączenie ze źródłem danych do zapisywania danych przesyłanych strumieniowo do SQL Database. SQL Database może być lokalną bazą danych SQL Edge lub SQL Server zdalnego lub Azure SQL Database|
| Azure Blob Storage | N | T | Źródło danych służące do zapisywania danych w obiekcie BLOB na koncie usługi Azure Storage. |
| Kafka | T | N | Źródło danych do odczytu danych przesyłanych strumieniowo z tematu Kafka. Ta karta jest obecnie dostępna tylko w wersji Intel/AMD usługi Azure SQL Edge i nie jest dostępna dla ARM64 wersji programu SQL Edge.|

### <a name="example-create-an-external-stream-inputoutput-object-for-azure-iot-edge-hub"></a>Przykład: Tworzenie zewnętrznego obiektu strumienia danych wejściowych/wyjściowych dla centrum Azure IoT Edge

Poniższy przykład tworzy obiekt strumienia zewnętrznego dla centrum brzegowego. Aby utworzyć zewnętrzne źródło danych wejściowych/wyjściowych strumienia dla Azure IoT Edge centrum, najpierw musisz utworzyć zewnętrzny format pliku dla SQL, aby zrozumieć układ danych, które są również odczytywane/zapisywane.

1. Utwórz format pliku zewnętrznego z typem formatu JSON.

    ```sql
    Create External file format InputFileFormat
    WITH (  
       format_type = JSON,
    )
    go
    ```

2. Utwórz zewnętrzne źródło danych dla Centrum IoT Edge. Poniższy skrypt T-SQL tworzy połączenie ze źródłem danych z koncentratorem brzegowym działającym na tym samym hoście platformy Docker co SQL Edge.

    ```sql
    CREATE EXTERNAL DATA SOURCE EdgeHubInput WITH (
    LOCATION = 'edgehub://'
    )
    go
    ```

3. Utwórz obiekt zewnętrznego strumienia dla Centrum IoT Edge. Poniższy skrypt T-SQL umożliwia utworzenie obiektu strumienia dla centrum brzegowego. W przypadku obiektu strumienia centrum granicznego parametr LOCATION jest nazwą tematu/kanału, w którym jest odczytywany lub zapisywany.

    ```sql
    CREATE EXTERNAL STREAM MyTempSensors WITH (
    DATA_SOURCE = EdgeHubInput,
    FILE_FORMAT = InputFileFormat,
    LOCATION = N'TemperatureSensors',
    INPUT_OPTIONS = N'',
    OUTPUT_OPTIONS = N''
    )
    go
    ```

### <a name="example-create-an-external-stream-object-sql-database"></a>Przykład: Utwórz zewnętrzny obiekt strumienia SQL Database

Poniższy przykład tworzy obiekt strumienia zewnętrznego w lokalnej bazie danych SQL Edge. 

1. Utwórz klucz główny w bazie danych. Jest to wymagane do szyfrowania klucza tajnego poświadczeń.

    ```sql
    CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<<Strong_Password_For_Master_Key_Encryption>>';
    ```

2. Utwórz poświadczenie o zakresie bazy danych w celu uzyskania dostępu do źródła SQL Server. Poniższy przykład tworzy poświadczenie do zewnętrznego źródła danych o tożsamości = "username" i SECRET = "Password".

    ```sql
    CREATE DATABASE SCOPED CREDENTIAL SQLCredential
    WITH IDENTITY = '<SQL_Login>', SECRET = '<SQL_Login_PASSWORD>'
    go
    ```

3. Utwórz zewnętrzne źródło danych z opcją Utwórz zewnętrzne źródło danych. Poniższy przykład:

    * Tworzy zewnętrzne źródło danych o nazwie LocalSQLOutput
    * Identyfikuje zewnętrzne źródło danych (LOCATION = " <vendor> :// <server> [: <port> ]"). W przykładzie wskazuje lokalne wystąpienie programu SQL Edge.
    * Na koniec przykład używa poświadczenie utworzone wcześniej.

    ```sql
    CREATE EXTERNAL DATA SOURCE LocalSQLOutput WITH (
    LOCATION = 'sqlserver://tcp:.,1433'
    ,CREDENTIAL = SQLCredential
    )
    go
    ```

4. Utwórz obiekt strumienia zewnętrznego. Poniższy przykład tworzy obiekt strumienia zewnętrznego wskazujący na tabelę *dbo. TemperatureMeasurements* w bazie danych *MySQLDatabase*.

    ```sql
    CREATE EXTERNAL STREAM TemperatureMeasurements WITH (
    DATA_SOURCE = LocalSQLOutput,
    LOCATION = N'MySQLDatabase.dbo.TemperatureMeasurements',
    INPUT_OPTIONS = N'',
    OUTPUT_OPTIONS = N''
    )
    ```

## <a name="create-the-streaming-job-and-the-streaming-queries"></a>Tworzenie zadania przesyłania strumieniowego i zapytań przesyłania strumieniowego

Użyj procedury składowanej **sys. sp_create_streaming_job** , aby zdefiniować zapytania przesyłania strumieniowego i utworzyć zadanie przesyłania strumieniowego. Procedura składowana **sp_create_streaming_job** przyjmuje dwa parametry

- job_name — nazwa zadania przesyłania strumieniowego. Nazwy zadań przesyłania strumieniowego są unikatowe w ramach wystąpienia.
- instrukcja- [Stream Analytics kwerendy](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference?) przesyłania strumieniowego oparte na języku zapytań.

Poniższy przykład tworzy proste zadanie przesyłania strumieniowego z jednym zapytaniem przesyłania strumieniowego. To zapytanie odczytuje dane wejściowe z centrum brzegowego i zapisuje je w *dbo. TemperatureMeasurements* w bazie danych.

```sql
EXEC sys.sp_create_streaming_job @name=N'StreamingJob1',
@statement= N'Select * INTO TemperatureMeasurements from MyEdgeHubInput'
```

Poniższy przykład tworzy bardziej złożone zadanie przesyłania strumieniowego z wieloma różnymi zapytaniami, w tym zapytanie, które używa wbudowanej funkcji AnomalyDetection_ChangePoint do identyfikowania anomalii w danych temperatury.

```sql
EXEC sys.sp_create_streaming_job @name=N'StreamingJob2', @statement=
N' Select * INTO TemperatureMeasurements1 from MyEdgeHubInput1

Select * Into TemperatureMeasurements2 from MyEdgeHubInput2

Select * Into TemperatureMeasurements3 from MyEdgeHubInput3

SELECT
Timestamp as [Time],
[Temperature] As [Temperature],
GetRecordPropertyValue(AnomalyDetection_ChangePoint(Temperature, 80, 1200) OVER(LIMIT DURATION(minute, 20)), ''Score'') as ChangePointScore,
GetRecordPropertyValue(AnomalyDetection_ChangePoint(Temperature, 80, 1200) OVER(LIMIT DURATION(minute, 20)), ''IsAnomaly'') as IsChangePointAnomaly,
INTO TemperatureAnomalies FROM MyEdgeHubInput2;
'
go
```

## <a name="start-stop-drop-and-monitor-streaming-jobs"></a>Uruchamianie, zatrzymywanie, porzucanie i monitorowanie zadań przesyłania strumieniowego

Aby rozpocząć zadanie przesyłania strumieniowego w programie SQL Edge, wykonaj procedurę składowaną **sys. sp_start_streaming_job** . Procedura składowana wymaga, aby zadanie przesyłania strumieniowego było uruchamiane jako dane wejściowe.

```sql
exec sys.sp_start_streaming_job @name=N'StreamingJob1'
go
```

Aby zatrzymać zadanie przesyłania strumieniowego w programie SQL Edge, wykonaj procedurę składowaną **sys. sp_stop_streaming_job** . Procedura składowana wymaga, aby zadanie przesyłania strumieniowego zostało zatrzymane jako dane wejściowe.

```sql
exec sys.sp_stop_streaming_job @name=N'StreamingJob1'
go
```

Aby porzucić (lub usunąć) zadanie przesyłania strumieniowego w programie SQL Edge, wykonaj procedurę składowaną **sys. sp_drop_streaming_job** . Procedura składowana wymaga, aby zadanie przesyłania strumieniowego było upuszczane jako dane wejściowe.

```sql
exec sys.sp_drop_streaming_job @name=N'StreamingJob1'
go
```

Aby uzyskać bieżący stan zadania przesyłania strumieniowego w programie SQL Edge, wykonaj procedurę składowaną **sys. sp_get_streaming_job** . Procedura składowana wymaga tego samego zadania przesyłania strumieniowego, aby można było je porzucić jako dane wejściowe i wyprowadzać nazwę oraz bieżący stan zadania przesyłania strumieniowego.

```sql
exec sys.sp_get_streaming_job @name=N'StreamingJob1'
        WITH RESULT SETS
(
       (
       name nvarchar(256),
       status nvarchar(256)
       )
)
```

Zadanie przesyłania strumieniowego może mieć jeden z następujących stanów

| Stan | Opis |
|--------| ------------|
| Utworzone | Zadanie przesyłania strumieniowego zostało utworzone, ale nie zostało jeszcze uruchomione |
| Uruchamianie | Zadanie przesyłania strumieniowego jest w fazie początkowej |
| Okresy | Zadanie przesyłania strumieniowego jest uruchomione, ale nie ma danych wejściowych do przetworzenia |
| Przetwarzanie | Zadanie przesyłania strumieniowego jest uruchomione i przetwarza dane wejściowe. Ten stan wskazuje na prawidłowy stan zadania przesyłania strumieniowego |
| Obniżona wydajność | Zadanie przesyłania strumieniowego jest uruchomione, ale wystąpiły Niekrytyczne błędy serializacji/deserializacji operacji wejścia/wyjścia podczas przetwarzania danych wejściowych. Zadanie wejściowe będzie nadal uruchamiane, ale spowoduje porzucenie danych wejściowych, które napotkały błędy |
| Zatrzymano | Zadanie przesyłania strumieniowego zostało zatrzymane |
| Niepowodzenie | Zadanie przesyłania strumieniowego nie powiodło się. Jest to zazwyczaj wskaźnik błędu krytycznego podczas przetwarzania |

## <a name="next-steps"></a>Następne kroki

- [Wyświetlanie metadanych skojarzonych z zadaniami przesyłania strumieniowego w usłudze Azure SQL Edge (wersja zapoznawcza)](streaming-catalog-views.md) 
- [Utwórz strumień zewnętrzny](create-external-stream-transact-sql.md)
