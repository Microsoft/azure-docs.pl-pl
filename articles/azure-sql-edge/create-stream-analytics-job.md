---
title: Tworzenie zadania przesyłania strumieniowego T-SQL w usłudze Azure SQL Edge
description: Dowiedz się więcej na temat tworzenia Stream Analytics zadań w usłudze Azure SQL Edge.
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 07/27/2020
ms.openlocfilehash: 97189fd7a232c2467981b23dc20da51ebef08252
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97656346"
---
# <a name="create-a-data-streaming-job-in-azure-sql-edge"></a>Tworzenie zadania przesyłania strumieniowego danych w usłudze Azure SQL Edge 

W tym artykule wyjaśniono, jak utworzyć zadanie przesyłania strumieniowego T-SQL w usłudze Azure SQL Edge. Tworzysz zewnętrzne obiekty strumienia danych wejściowych i wyjściowych, a następnie zdefiniujesz kwerendę zadania przesyłania strumieniowego w ramach tworzenia zadań przesyłania strumieniowego.

## <a name="configure-the-external-stream-input-and-output-objects"></a>Konfigurowanie zewnętrznych obiektów wejściowych i wyjściowych strumienia

Funkcja przesyłania strumieniowego T-SQL używa funkcji zewnętrznego źródła danych SQL Server, aby zdefiniować źródła danych skojarzone z zewnętrznymi danymi wejściowymi strumienia i wyjściem zadania przesyłania strumieniowego. Użyj następujących poleceń T-SQL, aby utworzyć zewnętrzny obiekt wejściowy lub wyjściowy strumienia:

- [CREATE EXTERNAL FILE FORMAT (Transact-SQL)](/sql/t-sql/statements/create-external-file-format-transact-sql)

- [CREATE EXTERNAL DATA SOURCE (Transact-SQL)](/sql/t-sql/statements/create-external-data-source-transact-sql)

- [Utwórz strumień zewnętrzny (Transact-SQL)](#example-create-an-external-stream-object-to-azure-sql-database)

Ponadto, jeśli usługa Azure SQL Edge, SQL Server lub Azure SQL Database jest używana jako strumień wyjściowy, konieczne jest [utworzenie poświadczeń o zakresie tworzenia bazy danych (Transact-SQL)](/sql/t-sql/statements/create-database-scoped-credential-transact-sql). To polecenie T-SQL definiuje poświadczenia dostępu do bazy danych.

### <a name="supported-input-and-output-stream-data-sources"></a>Obsługiwane źródła danych wejściowych i wyjściowych strumieni

Usługa Azure SQL Edge obecnie obsługuje tylko następujące źródła danych jako dane wejściowe i wyjściowe strumienia.

| Typ źródła danych | Dane wejściowe | Dane wyjściowe | Opis |
|------------------|-------|--------|------------------|
| Azure IoT Edge Hub | Y | Y | Źródło danych do odczytu i zapisu danych przesyłanych strumieniowo do centrum Azure IoT Edge. Aby uzyskać więcej informacji, zobacz [IoT Edge Hub](../iot-edge/iot-edge-runtime.md#iot-edge-hub).|
| SQL Database | N | Y | Połączenie ze źródłem danych do zapisywania danych przesyłanych strumieniowo do SQL Database. Baza danych może być lokalną bazą danych w usłudze Azure SQL Edge lub zdalną bazą danych w SQL Server lub Azure SQL Database.|
| Kafka | Y | N | Źródło danych do odczytu danych przesyłanych strumieniowo z tematu Kafka. Ten adapter jest obecnie dostępny tylko dla wersji Intel lub AMD usługi Azure SQL Edge. Nie jest ona dostępna dla ARM64 wersji usługi Azure SQL Edge.|

### <a name="example-create-an-external-stream-inputoutput-object-for-azure-iot-edge-hub"></a>Przykład: Tworzenie zewnętrznego obiektu strumienia danych wejściowych/wyjściowych dla centrum Azure IoT Edge

Poniższy przykład tworzy obiekt strumienia zewnętrznego dla Azure IoT Edge centrum. Aby utworzyć zewnętrzne źródło danych wejściowych/wyjściowych strumienia dla Azure IoT Edge Hub, najpierw musisz utworzyć zewnętrzny format pliku do układu danych, które są w nich odczytywane lub zapisywane.

1. Utwórz zewnętrzny format pliku JSON.

    ```sql
    Create External file format InputFileFormat
    WITH 
    (  
       format_type = JSON,
    )
    go
    ```

2. Utwórz zewnętrzne źródło danych dla centrum Azure IoT Edge. Poniższy skrypt T-SQL tworzy połączenie ze źródłem danych z Centrum IoT Edge, które działa na tym samym hoście platformy Docker co usługa Azure SQL Edge.

    ```sql
    CREATE EXTERNAL DATA SOURCE EdgeHubInput 
    WITH 
    (
        LOCATION = 'edgehub://'
    )
    go
    ```

3. Utwórz obiekt zewnętrznego strumienia dla Azure IoT Edge Hub. Poniższy skrypt T-SQL tworzy obiekt Stream dla Centrum IoT Edge. W przypadku obiektu strumienia Centrum IoT Edge parametr LOCATION jest nazwą tematu Centrum IoT Edge lub odczytywanym lub zapisywanym kanałem.

    ```sql
    CREATE EXTERNAL STREAM MyTempSensors 
    WITH 
    (
        DATA_SOURCE = EdgeHubInput,
        FILE_FORMAT = InputFileFormat,
        LOCATION = N'TemperatureSensors',
        INPUT_OPTIONS = N'',
        OUTPUT_OPTIONS = N''
    );
    go
    ```

### <a name="example-create-an-external-stream-object-to-azure-sql-database"></a>Przykład: Utwórz obiekt strumienia zewnętrznego do Azure SQL Database

Poniższy przykład tworzy obiekt strumienia zewnętrznego w lokalnej bazie danych w usłudze Azure SQL Edge. 

1. Utwórz klucz główny w bazie danych. Jest to wymagane do szyfrowania klucza tajnego poświadczeń.

    ```sql
    CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<<Strong_Password_For_Master_Key_Encryption>>';
    ```

2. Utwórz poświadczenia w zakresie bazy danych na potrzeby uzyskiwania dostępu do źródła SQL Server. Poniższy przykład tworzy poświadczenie do zewnętrznego źródła danych, przy użyciu tożsamości = username i SECRET = Password.

    ```sql
    CREATE DATABASE SCOPED CREDENTIAL SQLCredential
    WITH IDENTITY = '<SQL_Login>', SECRET = '<SQL_Login_PASSWORD>'
    go
    ```

3. Utwórz zewnętrzne źródło danych z opcją Utwórz zewnętrzne źródło danych. Poniższy przykład:

    * Tworzy zewnętrzne źródło danych o nazwie *LocalSQLOutput*.
    * Identyfikuje zewnętrzne źródło danych (LOCATION = " <vendor> :// <server> [: <port> ]"). W tym przykładzie wskazuje lokalne wystąpienie usługi Azure SQL Edge.
    * Używa wcześniej utworzonego poświadczenia.

    ```sql
    CREATE EXTERNAL DATA SOURCE LocalSQLOutput 
    WITH 
    (
        LOCATION = 'sqlserver://tcp:.,1433',
        CREDENTIAL = SQLCredential
    )
    go
    ```

4. Utwórz obiekt strumienia zewnętrznego. Poniższy przykład tworzy obiekt strumienia zewnętrznego wskazujący na tabelę *dbo. TemperatureMeasurements*, w bazie danych *MySQLDatabase*.

    ```sql
    CREATE EXTERNAL STREAM TemperatureMeasurements 
    WITH 
    (
        DATA_SOURCE = LocalSQLOutput,
        LOCATION = N'MySQLDatabase.dbo.TemperatureMeasurements',
        INPUT_OPTIONS = N'',
        OUTPUT_OPTIONS = N''
    );
    ```

### <a name="example-create-an-external-stream-object-for-kafka"></a>Przykład: Tworzenie zewnętrznego obiektu strumienia dla Kafka

Poniższy przykład tworzy obiekt strumienia zewnętrznego w lokalnej bazie danych w usłudze Azure SQL Edge. W tym przykładzie przyjęto założenie, że serwer Kafka jest skonfigurowany do anonimowego dostępu. 

1. Utwórz zewnętrzne źródło danych z opcją Utwórz zewnętrzne źródło danych. Poniższy przykład:

    ```sql
    Create EXTERNAL DATA SOURCE [KafkaInput] 
    With
    (
        LOCATION = N'kafka://<kafka_bootstrap_server_name_ip>:<port_number>'
    )
    GO
    ```
2. Utwórz zewnętrzny format pliku dla danych wejściowych Kafka. Poniższy przykład został utworzony w formacie pliku JSON z kompresją formacie gzip. 

   ```sql
   CREATE EXTERNAL FILE FORMAT JsonGzipped  
    WITH 
    (  
        FORMAT_TYPE = JSON , 
        DATA_COMPRESSION = 'org.apache.hadoop.io.compress.GzipCodec' 
    )
   ```

3. Utwórz obiekt strumienia zewnętrznego. Poniższy przykład tworzy obiekt strumienia zewnętrznego wskazujący temat Kafka `*TemperatureMeasurement*` .

    ```sql
    CREATE EXTERNAL STREAM TemperatureMeasurement 
    WITH 
    (  
        DATA_SOURCE = KafkaInput, 
        FILE_FORMAT = JsonGzipped,
        LOCATION = 'TemperatureMeasurement',
        INPUT_OPTIONS = 'PARTITIONS: 10' 
    ); 
    ```

## <a name="create-the-streaming-job-and-the-streaming-queries"></a>Tworzenie zadania przesyłania strumieniowego i zapytań przesyłania strumieniowego

Użyj `sys.sp_create_streaming_job` systemowej procedury składowanej, aby zdefiniować zapytania przesyłania strumieniowego i utworzyć zadanie przesyłania strumieniowego. `sp_create_streaming_job`Procedura składowana przyjmuje następujące parametry:

- `job_name`: Nazwa zadania przesyłania strumieniowego. Nazwy zadań przesyłania strumieniowego są unikatowe w ramach wystąpienia.
- `statement`: [Stream Analytics kwerendy](/stream-analytics-query/stream-analytics-query-language-reference)przesyłania strumieniowego oparte na języku zapytań.

Poniższy przykład tworzy proste zadanie przesyłania strumieniowego z jednym zapytaniem przesyłania strumieniowego. To zapytanie odczytuje dane wejściowe z Centrum IoT Edge i zapisuje je `dbo.TemperatureMeasurements` w bazie danych.

```sql
EXEC sys.sp_create_streaming_job @name=N'StreamingJob1',
@statement= N'Select * INTO TemperatureMeasurements from MyEdgeHubInput'
```

Poniższy przykład tworzy bardziej złożone zadanie przesyłania strumieniowego z wieloma różnymi zapytaniami. Te zapytania obejmują jeden, który używa wbudowanej `AnomalyDetection_ChangePoint` funkcji do identyfikowania anomalii w danych temperatury.

```sql
EXEC sys.sp_create_streaming_job @name=N'StreamingJob2', @statement=
N' Select * INTO TemperatureMeasurements1 from MyEdgeHubInput1

Select * Into TemperatureMeasurements2 from MyEdgeHubInput2

Select * Into TemperatureMeasurements3 from MyEdgeHubInput3

SELECT
Timestamp as [Time],
[Temperature] As [Temperature],
GetRecordPropertyValue(AnomalyDetection_ChangePoint(Temperature, 80, 1200) OVER(LIMIT DURATION(minute, 20)), ''Score'') as ChangePointScore,
GetRecordPropertyValue(AnomalyDetection_ChangePoint(Temperature, 80, 1200) OVER(LIMIT DURATION(minute, 20)), ''IsAnomaly'') as IsChangePointAnomaly
INTO TemperatureAnomalies FROM MyEdgeHubInput2;
'
go
```

## <a name="start-stop-drop-and-monitor-streaming-jobs"></a>Uruchamianie, zatrzymywanie, porzucanie i monitorowanie zadań przesyłania strumieniowego

Aby rozpocząć zadanie przesyłania strumieniowego w usłudze Azure SQL Edge, uruchom `sys.sp_start_streaming_job` procedurę składowaną. Procedura składowana wymaga, aby nazwa zadania przesyłania strumieniowego była uruchamiana jako dane wejściowe.

```sql
exec sys.sp_start_streaming_job @name=N'StreamingJob1'
go
```

Aby zatrzymać zadanie przesyłania strumieniowego, uruchom `sys.sp_stop_streaming_job` procedurę składowaną. Procedura składowana wymaga, aby nazwa zadania przesyłania strumieniowego była zatrzymywana jako dane wejściowe.

```sql
exec sys.sp_stop_streaming_job @name=N'StreamingJob1'
go
```

Aby porzucić (lub usunąć) zadanie przesyłania strumieniowego, uruchom `sys.sp_drop_streaming_job` procedurę składowaną. Procedura składowana wymaga, aby nazwa zadania przesyłania strumieniowego była porzucana jako dane wejściowe.

```sql
exec sys.sp_drop_streaming_job @name=N'StreamingJob1'
go
```

Aby uzyskać bieżący stan zadania przesyłania strumieniowego, uruchom `sys.sp_get_streaming_job` procedurę składowaną. Procedura składowana wymaga, aby nazwa zadania przesyłania strumieniowego była porzucana jako dane wejściowe. Wyświetla nazwę i bieżący stan zadania przesyłania strumieniowego.

```sql
exec sys.sp_get_streaming_job @name=N'StreamingJob1'
        WITH RESULT SETS
(
       (
       name nvarchar(256),
       status nvarchar(256),
       error nvarchar(256)
       )
)
```

Zadanie przesyłania strumieniowego może mieć jeden z następujących stanów:

| Stan | Opis |
|--------| ------------|
| Utworzone | Zadanie przesyłania strumieniowego zostało utworzone, ale jeszcze nie zostało uruchomione. |
| Uruchamianie | Zadanie przesyłania strumieniowego jest w fazie początkowej. |
| Okresy | Zadanie przesyłania strumieniowego jest uruchomione, ale nie ma danych wejściowych do przetworzenia. |
| Przetwarzanie | Zadanie przesyłania strumieniowego jest uruchomione i przetwarza dane wejściowe. Ten stan wskazuje na prawidłowy stan zadania przesyłania strumieniowego. |
| Obniżona wydajność | Zadanie przesyłania strumieniowego jest uruchomione, ale podczas przetwarzania danych wejściowych wystąpiły pewne Błędy niekrytyczne. Zadanie wejściowe będzie nadal uruchamiane, ale spowoduje porzucenie danych wejściowych, które napotkały błędy. |
| Zatrzymano | Zadanie przesyłania strumieniowego zostało zatrzymane. |
| Niepowodzenie | Zadanie przesyłania strumieniowego nie powiodło się. Jest to zazwyczaj wskaźnik błędu krytycznego podczas przetwarzania. |

## <a name="next-steps"></a>Następne kroki

- [Wyświetlanie metadanych skojarzonych z zadaniami przesyłania strumieniowego w usłudze Azure SQL Edge](streaming-catalog-views.md) 
- [Utwórz strumień zewnętrzny](create-external-stream-transact-sql.md)