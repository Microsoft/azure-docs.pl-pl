---
title: TWORZENIE strumienia zewnętrznego (Transact-SQL) — Azure SQL Edge (wersja zapoznawcza)
description: Dowiedz się więcej na temat instrukcji CREATE EXTERNAL STREAM w usłudze Azure SQL Edge (wersja zapoznawcza)
keywords: ''
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: d4ad446d43c90eee1c48ee2ba94585242805fa7d
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83597933"
---
# <a name="create-external-stream-transact-sql"></a>Utwórz strumień zewnętrzny (Transact-SQL)

Obiekt strumienia zewnętrznego ma dwa przeznaczenie zarówno wejścia, jak i wyjścia. Może służyć jako dane wejściowe do wykonywania zapytań dotyczących danych przesyłanych strumieniowo z usług pozyskiwania zdarzeń, takich jak zdarzenia platformy Azure lub centra IoT lub używane jako dane wyjściowe, aby określić, gdzie i jak mają być przechowywane wyniki zapytania przesyłania strumieniowego.

ZEWNĘTRZNY strumień można także określić i utworzyć jako dane wyjściowe oraz dane wejściowe dla usług takich jak centrum zdarzeń lub usługa BLOB Storage. Jest to przeznaczone dla scenariuszy łańcucha, w których zapytanie strumieniowe zachowuje wyniki do strumienia zewnętrznego jako dane wyjściowe i inne zapytanie przesyłane strumieniowo z tego samego strumienia zewnętrznego jako dane wejściowe. 


## <a name="syntax"></a>Składnia

```sql
CREATE EXTERNAL STREAM {external_stream_name}  
(column definition [,column definitions ] * ) --Used for input - optional 
WITH  
(  
  DATA_SOURCE = <data_source_name>, 
  LOCATION = <location_name>, 
  EXTERNAL_FILE_FORMAT = <external_file_format_name>, --Used for input - optional 
   
  INPUT_OPTIONS =  
    ‘CONSUMER_GROUP=<consumer_group_name>; 
    ‘TIME_POLICY=<time_policy>; 
    LATE_EVENT_TOLERANCE=<late_event_tolerance_value>; 
    OUT_OF_ORDER_EVENT_TOLERANCE=<out_of_order_tolerance_value> 
     
    /* Edge options */ 
  , 
  OUTPUT_OPTIONS =  
    ‘REJECT_POLICY=<reject_policy>; 
    MINIMUM_ROWS=<row_value>; 
    MAXIMUM_TIME=<time_value_minutes>; 
    PARTITION_KEY_COLUMN=<partition_key_column_name>; 
    PROPERTY_COLUMNS=(); 
    SYSTEM_PROPERTY_COLUMNS=(); 
    PARTITION_KEY=<partition_key_name>; 
    ROW_KEY=<row_key_name>; 
    BATCH_SIZE=<batch_size_value>; 
    MAXIMUM_BATCH_COUNT=<batch_value>;  
    STAGING_AREA=<blob_data_source>’ 
     
    /* Edge options */ TAGS=<tag_column_value> 
); 
```


## <a name="arguments"></a>Argumenty


- [ZEWNĘTRZNE ŹRÓDŁO DANYCH](/sql/t-sql/statements/create-external-data-source-transact-sql/)
- [FORMAT PLIKU ZEWNĘTRZNEGO](/sql/t-sql/statements/create-external-file-format-transact-sql/)
- **Lokalizacja**: Określa nazwę rzeczywistego danych lub lokalizacji w źródle danych. W przypadku obiektu usługi Edge Hub lub strumienia Kafka Lokalizacja określa nazwę centrum krawędzi lub tematu Kafka, z którego ma zostać odczytana lub zapisana.
- **INPUT_OPTIONS**: Określ opcje jako pary klucz-wartość dla usług takich jak zdarzenia i centra IoT, które są danymi wejściowymi zapytań przesyłania strumieniowego
    - CONSUMER_GROUP: Centra zdarzeń i IoT ograniczają liczbę czytelników w ramach jednej grupy odbiorców (do 5). Pozostawienie tego pola pustego spowoduje użycie grupy konsumentów "$Default".
      - Dotyczy centrów zdarzeń i IOT
    - TIME_POLICY: opisuje, czy należy porzucić zdarzenia, czy też dostosować czas zdarzenia, gdy opóźnione zdarzenia lub zdarzenia poza kolejnością przechodzą wartość tolerancji.
      - Dotyczy centrów zdarzeń i IOT
    - LATE_EVENT_TOLERANCE: maksymalny akceptowalny czas oczekiwania. Opóźnienie reprezentuje różnicę między sygnaturą czasową zdarzenia a zegarem systemowym.
      - Dotyczy centrów zdarzeń i IOT
    - OUT_OF_ORDER_EVENT_TOLERANCE: zdarzenia mogą być przychodzące po wykonaniu podróży od danych wejściowych do zapytania przesyłania strumieniowego. Te zdarzenia mogą być akceptowane w stanie takim, w jakim jest lub można wstrzymać pracę w określonym okresie, aby zmienić ich kolejność.
      - Dotyczy centrów zdarzeń i IOT
- **OUTPUT_OPTIONS**: Określ opcje jako pary klucz-wartość dla obsługiwanych usług, które są wyprowadzane strumieniowo zapytania 
  - REJECT_POLICY: UPUŚĆ | Ponów PRÓBę gatunku zasad obsługi błędów danych w przypadku wystąpienia błędów konwersji danych. 
    - Dotyczy wszystkich obsługiwanych danych wyjściowych 
  - MINIMUM_ROWS:  
    Minimalna liczba wymaganych wierszy dla partii zapisywana w danych wyjściowych. Dla Parquet każda partia utworzy nowy plik. 
    - Dotyczy wszystkich obsługiwanych danych wyjściowych 
  - MAXIMUM_TIME:  
    Maksymalny czas oczekiwania na partię. Po upływie tego czasu partia zostanie zapisywana w danych wyjściowych, nawet jeśli nie spełniono wymagań dotyczących minimalnych wierszy. 
    - Dotyczy wszystkich obsługiwanych danych wyjściowych 
  - PARTITION_KEY_COLUMN:  
    Kolumna używana dla klucza partycji. 
    - Dotyczy centrum zdarzeń i Service Bus tematu 
  - PROPERTY_COLUMNS:  
    Rozdzielana przecinkami lista nazw kolumn wyjściowych, które będą dołączane do komunikatów jako właściwości niestandardowe, jeśli zostały podane.  
    - Dotyczy centrum zdarzeń i tematu Service Bus i kolejki 
  - SYSTEM_PROPERTY_COLUMNS:  
    Kolekcja par nazwa-wartość i kolumny wyjściowe w formacie JSON, które mają zostać wypełnione na Service Bus komunikatów. np. {"MessageId": "Kolumna1", "PartitionKey": "Kolumna2"} 
    - Dotyczy tematu Service Bus i kolejki 
  - PARTITION_KEY:  
    Nazwa kolumny wyjściowej zawierającej klucz partycji. Klucz partycji jest unikatowym identyfikatorem partycji w danej tabeli, która stanowi pierwszą część klucza podstawowego jednostki. Jest to wartość ciągu, która może mieć długość do 1 KB. 
    - Dotyczy Table Storage i funkcji platformy Azure 
  - ROW_KEY:  
    Nazwa kolumny wyjściowej zawierającej klucz wiersza. Klucz wiersza jest unikatowym identyfikatorem dla jednostki w ramach danej partycji. Stanowi on drugą część klucza podstawowego jednostki. Klucz wiersza jest wartością ciągu, która może mieć długość do 1 KB. 
    - Dotyczy Table Storage i funkcji platformy Azure 
  - BATCH_SIZE:  
    Oznacza to liczbę transakcji dla magazynu tabel, w których maksymalna wartość może nastąpić do 100 rekordów. W przypadku Azure Functions oznacza to, że rozmiar wsadu w bajtach wysłanych do funkcji dla wywołania — wartość domyślna to 256 kB. 
    - Dotyczy Table Storage i funkcji platformy Azure 
  - MAXIMUM_BATCH_COUNT:  
    Maksymalna liczba zdarzeń wysyłanych do funkcji dla wywołania funkcji platformy Azure — wartość domyślna to 100. W przypadku SQL Database oznacza to maksymalną liczbę rekordów wysłanych z każdą zbiorczą transakcją wstawiania — wartość domyślna to 10 000. 
    - Dotyczy SQL Database i funkcji platformy Azure 
  - STAGING_AREA: obiekt zewnętrznego źródła danych do Blob Storage obszaru przejściowego na potrzeby pozyskiwania danych o wysokiej przepływności w SQL Data Warehouse 
    - Dotyczy SQL Data Warehouse


## <a name="examples"></a>Przykłady

### <a name="example-1---edgehub"></a>Przykład 1 — EdgeHub

Typ: dane wejściowe lub wyjściowe<br>
Parametry:
- Dane wejściowe lub wyjściowe
  - Alias 
  - Format serializacji zdarzeń 
  - Encoding 
- Tylko dane wejściowe: 
  - Typ kompresji zdarzenia 

Składnia:

```sql
CREATE EXTERNAL DATA SOURCE MyEdgeHub 
WITH  
(      
  LOCATION = 'edgehub://'       
); 
 
CREATE EXTERNAL FILE FORMAT myFileFormat  
WITH (  
   FORMAT_TYPE = 'JSON', 
); 
 
CREATE EXTERNAL STREAM Stream_A  
WITH    
(   
   DATA_SOURCE = MyEdgeHub, 
   EXTERNAL_FILE_FORMAT = myFileFormat, 
   LOCATION = ‘<mytopicname>’, 
   OUTPUT_OPTIONS =   
     ‘REJECT_TYPE: Drop’ 
);
```


### <a name="example-2---azure-sql-database-azure-sql-edge-sql-server"></a>Przykład 2 — Azure SQL Database, Azure SQL Edge, SQL Server

Typ: dane wyjściowe<br>
Parametry:
- Alias danych wyjściowych  
- Baza danych (wymagana dla SQL Database) 
- Serwer (wymagany do SQL Database) 
- Nazwa użytkownika (wymagana dla SQL Database) 
- Hasło (wymagane dla SQL Database) 
- Tabela 
- Scal wszystkie partycje wejściowe w jeden schemat partycji zapisu lub dziedziczenia dla poprzedniego kroku zapytania lub danych wejściowych (wymagane dla SQL Database) 
- Maksymalna liczba partii 

Składnia:

```sql
CREATE DATABASE SCOPED CREDENTIAL SQLCredName 
WITH IDENTITY = '<user>’, 
SECRET = '<password>'; 
 
-- Azure SQL Database 
CREATE EXTERNAL DATA SOURCE MyTargetSQLTabl 
WITH 
(     
  LOCATION = ' <my_server_name>.database.windows.net’, 
  CREDENTIAL = SQLCredName 
); 
 
--SQL Server or Azure SQL Edge
CREATE EXTERNAL DATA SOURCE MyTargetSQLTabl 
WITH 
(     
  LOCATION = ' <sqlserver://<ipaddress>,<port>’, 
  CREDENTIAL = SQLCredName 
); 
 
--SQL Database/Edge 
CREATE EXTERNAL STREAM Stream_A 
WITH   
(  
    DATA_SOURCE = MyTargetSQLTable, 
    LOCATION = ‘<DatabaseName>.<SchemaName>.<TableName>’ 
   --Note: If table is container in the database, <TableName> should be sufficient 
   --Note: Do not need external file format in this case 
    EXTERNAL_FILE_FORMAT = myFileFormat,  
    OUTPUT_OPTIONS =  
      ‘REJECT_TYPE: Drop 
); 
```

### <a name="example-3---kafka"></a>Przykład 3 — Kafka

Typ: dane wejściowe<br>
Parametry:

- Serwer ładowania początkowego Kafka 
- Nazwa tematu Kafka 
- Liczba partycji źródłowych 

Składnia:

```sql
CREATE EXTERNAL DATA SOURCE MyKafka_tweets 
WITH 
( 
  --The location maps to KafkaBootstrapServer 
  LOCATION = ' kafka://<kafkaserver>:<ipaddress>’, 
  CREDENTIAL = kafkaCredName 
 
); 
 
CREATE EXTERNAL FILE FORMAT myFileFormat 
WITH ( 
    FORMAT_TYPE = 'CSV', 
    DATA_COMPRESSION = 'GZIP', 
    ENCODING = ‘UTF-8’, 
    DELIMITER = ‘|’ 
); 
 
 
CREATE EXTERNAL STREAM Stream_A (user_id VARCHAR, tweet VARCHAR) 
WITH   
(  
    DATA_SOURCE = MyKafka_tweets, 
    LOCATION = ‘<KafkaTopicName>’, 
   --JSON: Format, CSV: Delimiter and Encoding, AVRO: None 
    EXTERNAL_FILE_FORMAT = myFileFormat,  
    INPUT_OPTIONS =  
      ‘PARTITIONS: 5’ 
); 
```

### <a name="example-4---blob-storage"></a>Przykład 4 — Magazyn obiektów BLOB

Typ: dane wejściowe lub wyjściowe<br>
Parametry:
- Dane wejściowe lub wyjściowe:
  - Alias 
  - Konto magazynu 
  - Klucz konta magazynu 
  - Kontener 
  - Wzorzec ścieżki 
  - Format daty 
  - Format czasu 
  - Format serializacji zdarzeń 
  - Encoding 
- Tylko dane wejściowe: 
  - Partycje (dane wejściowe) 
  - Typ kompresji zdarzenia (dane wejściowe) 
- Tylko dane wyjściowe: 
  - Minimalna ilość wierszy (dane wyjściowe) 
  - Maksymalny czas (dane wyjściowe) 
  - Tryb uwierzytelniania (dane wyjściowe) 

Składnia:

```sql
CREATE DATABASE SCOPED CREDENTIAL StorageAcctCredName 
WITH IDENTITY = 'Shared Access Signature’, 
SECRET = 'AccountKey'; 
 
CREATE DATABASE SCOPED CREDENTIAL StorageAcctCredNameMSI 
WITH IDENTITY = 'Managed Identity’; 
 
CREATE EXTERNAL DATA SOURCE MyBlobStorage_tweets 
WITH 
(     
  LOCATION = 'sb://my-sb-namespace.servicebus.windows.net’, 
  CREDENTIAL = eventHubCredName 
); 
 
CREATE EXTERNAL FILE FORMAT myFileFormat 
WITH ( 
    FORMAT_TYPE = 'CSV', --Event serialization format 
    DATE_FORMAT = 'YYYY/MM/DD HH', --Both Date and Time format 
    ENCODING = ‘UTF-8’ 
); 
 
CREATE EXTERNAL STREAM Stream_A (user_id VARCHAR, tweet VARCHAR) 
WITH   
(  
    DATA_SOURCE = MyBlobStorage_tweets, 
    LOCATION = ‘<path_pattern>’, 
    EXTERNAL_FILE_FORMAT = myFileFormat,  
    INPUT_OPTIONS =  
      ‘PARTITIONS: 1’, 
  
    OUTPUT_OPTIONS =  
      ‘REJECT_TYPE: Drop, 
      PARTITION_KEY_COLUMN: , 
      PROPERTY_COLUMNS: (), 
      MINUMUM_ROWS: 100000, 
      MAXIMUM_TIME: 60’ 
); 
```

### <a name="example-5---event-hub"></a>Przykład 5 — centrum zdarzeń

Typ: dane wejściowe lub wyjściowe<br>
Parametry:
- Dane wejściowe lub wyjściowe:
  - Alias 
  - Service Bus przestrzeń nazw 
  - Nazwa centrum zdarzeń 
  - Nazwa zasad centrum zdarzeń 
  - Klucz zasad centrum zdarzeń 
  - Format serializacji zdarzeń 
  - Encoding 
- Tylko dane wejściowe: 
  - Grupa konsumentów centrum zdarzeń 
  - Typ kompresji zdarzenia 
- Tylko dane wyjściowe: 
  - Kolumna klucza partycji 
  - Kolumny właściwości 

Składnia:

```sql
CREATE DATABASE SCOPED CREDENTIAL eventHubCredName 
WITH IDENTITY = 'Shared Access Signature’, 
SECRET = '<policyName>'; 
 
CREATE EXTERNAL DATA SOURCE MyEventHub_tweets 
WITH 
(     
  LOCATION = 'sb://my-sb-namespace.servicebus.windows.net’, 
  CREDENTIAL = eventHubCredName 
); 
 
CREATE EXTERNAL FILE FORMAT myFileFormat 
WITH ( 
    FORMAT_TYPE = 'CSV', 
    DATA_COMPRESSION = 'GZIP', 
    ENCODING = ‘UTF-8’, 
    DELIMITER = ‘|’ 
); 
 
 
CREATE EXTERNAL STREAM Stream_A (user_id VARCHAR, tweet VARCHAR) 
WITH   
(  
    DATA_SOURCE = MyEventHub_tweets, 
    LOCATION = ‘<topicname>’, 
   --JSON: Format, CSV: Delimiter and Encoding, AVRO: None 
    EXTERNAL_FILE_FORMAT = myFileFormat,  
 
    INPUT_OPTIONS =  
      ‘CONSUMER_GROUP: FirstConsumerGroup’, 
          
    OUTPUT_OPTIONS =  
      ‘REJECT_TYPE: Drop, 
      PARTITION_KEY_COLUMN: , 
      PROPERTY_COLUMNS: ()’ 
);
```

### <a name="example-6---iot-hub"></a>Przykład 6 — IOT Hub

Typ: dane wejściowe<br>
Parametry:

- Alias danych wejściowych 
- Usługa IoT Hub 
- Endpoint 
- Nazwa zasad dostępu współdzielonego 
- Klucz zasad dostępu współdzielonego 
- Grupa konsumentów 
- Format serializacji zdarzeń 
- Encoding 
- Typ kompresji zdarzenia 

Składnia:

```sql
CREATE DATABASE SCOPED CREDENTIAL IoTHubCredName 
WITH IDENTITY = 'Shared Access Signature’, 
SECRET = '<policyName>'; 
 
CREATE EXTERNAL DATA SOURCE MyIoTHub_tweets 
WITH 
(     
  LOCATION = ' iot://iot_hub_name.azure-devices.net’, 
  CREDENTIAL = IoTHubCredName 
);  

CREATE EXTERNAL FILE FORMAT myFileFormat 
WITH ( 
    FORMAT_TYPE = 'CSV', --Event serialization format 
    DATA_COMPRESSION = 'GZIP', 
    ENCODING = ‘UTF-8’ 
); 
 
CREATE EXTERNAL STREAM Stream_A (user_id VARCHAR, tweet VARCHAR) 
WITH   
(  
    DATA_SOURCE = MyIoTHub_tweets, 
    LOCATION = ‘<name>’, 
    EXTERNAL_FILE_FORMAT = myFileFormat,  
    INPUT_OPTIONS =  
      ‘ENDPOINT: Messaging, 
      CONSUMER_GROUP: ‘FirstConsumerGroup’ 
); 
```

### <a name="example-7---azure-synapse-analytics-formerly-sql-data-warehouse"></a>Przykład 7 — Analiza Synapse Azure (dawniej SQL Data Warehouse)

Typ: dane wyjściowe<br>
Parametry:
- Alias danych wyjściowych 
- baza danych 
- Serwer 
- Nazwa użytkownika 
- Hasło 
- Tabela 
- Obszar przejściowy (na potrzeby kopiowania) 

Składnia:

```sql
CREATE DATABASE SCOPED CREDENTIAL SQLCredName 
WITH IDENTITY = '<user>’, 
SECRET = '<password>'; 
 
CREATE EXTERNAL DATA SOURCE MyTargetSQLTable 
WITH 
(     
  LOCATION = ' <my_server_name>.database.windows.net’, 
  CREDENTIAL = SQLCredName 
); 
 
CREATE EXTERNAL STREAM MySQLTableOutput 
WITH ( 
   DATA_SOURCE = MyTargetSQLTable, 
   LOCATION = ‘<TableName>’ 
   --Note: Do not need external file format in this case 
   OUTPUT_OPTIONS =  
     ‘REJECT_TYPE: Drop, 
     STAGING_AREA: staging_area_data_source’, 
); 
```


### <a name="example-8---table-storage"></a>Przykład 8 — magazyn tabel

Typ: dane wyjściowe<br>
Parametry:
- Alias danych wyjściowych 
- Konto magazynu 
- Klucz konta magazynu 
- Nazwa tabeli 
- Klucz partycji 
- Klucz wiersza 
- Rozmiar partii 

Składnia:

```sql
CREATE DATABASE SCOPED CREDENTIAL TableStorageCredName 
WITH IDENTITY = ‘Storage account Key’, 
SECRET = '<storage_account_key>'; 
 
CREATE EXTERNAL DATA SOURCE MyTargetTableStorage 
WITH 
(     
  LOCATION = 'abfss://<storage_account>.dfs.core.windows.net’, 
  CREDENTIAL = TableStorageCredName 
); 
 
CREATE EXTERNAL STREAM MyTargetTableStorageOutput 
WITH ( 
   DATA_SOURCE = MyTargetTableStorage, 
   LOCATION = ‘<TableName>’, 
   OUTPUT_OPTIONS =  
     ‘REJECT_TYPE: Drop, 
     PARTITION_KEY: <column_partition_key>, 
     ROW_KEY: <column_row_key>, 
     BATCH_SIZE: 100’ 
); 
```


### <a name="example-9---service-bus-topic-same-properties-as-queue"></a>Przykład 9 — temat Service Bus (takie same właściwości jak kolejka)

Typ: dane wyjściowe<br>
Parametry:
- Alias danych wyjściowych 
- Service Bus przestrzeń nazw 
- Nazwa tematu 
- Nazwa zasad tematu 
- Klucz zasad tematu 
- Kolumny właściwości 
- Kolumny właściwości systemu 
- Format serializacji zdarzeń 
- Encoding 

Składnia:

```sql
CREATE DATABASE SCOPED CREDENTIAL serviceBusCredName 
WITH IDENTITY = 'Shared Access Signature’, 
SECRET = '<policyName>'; 
 
CREATE EXTERNAL DATA SOURCE MyServiceBus_tweets 
WITH 
(     
  LOCATION = 'sb://my-sb-namespace.servicebus.windows.net’, 
  CREDENTIAL = serviceBusCredName 
); 
 
CREATE EXTERNAL FILE FORMAT myFileFormat 
WITH ( 
    FORMAT_TYPE = 'CSV', --Event serialization format 
    DATA_COMPRESSION = 'GZIP', 
    ENCODING = ‘UTF-8’ 
); 
 
CREATE EXTERNAL STREAM MyServiceBusOutput 
WITH ( 
   DATA_SOURCE = MyServiceBus_tweets, 
   LOCATION = ‘<topic_name>’, 
   EXTERNAL_FILE_FORMAT = myFileFormat 
       OUTPUT_OPTIONS =  
     ‘REJECT_TYPE: Drop, 
     PARTITION_KEY_COLUMN: , 
     PROPERTY_COLUMNS: (), 
     SYSTEM_PROPERTY_COLUMNS: ()’ 
   --JSON: Format, CSV: Delimiter and Encoding, AVRO: None 
           
); 
```


### <a name="example-10---cosmos-db"></a>Przykład 10 Cosmos DB

Typ: dane wyjściowe<br>
Parametry:
- Alias danych wyjściowych 
- Identyfikator konta 
- Klucz konta 
- baza danych 
- Nazwa kontenera 
- Identyfikator dokumentu 


Składnia:

```sql
CREATE DATABASE SCOPED CREDENTIAL cosmosDBCredName 
WITH IDENTITY = ‘Storage Account Key’, 
SECRET = '<accountKey>'; 
 
CREATE EXTERNAL DATA SOURCE MyCosmosDB_tweets 
WITH 
(     
  LOCATION = ' cosmosdb://accountid.documents.azure.com:443/ database’, 
  CREDENTIAL = cosmosDBCredName 
); 
 
CREATE EXTERNAL STREAM MyCosmosDBOutput 
WITH ( 
   DATA_SOURCE = MyCosmosDB_tweets, 
   LOCATION = ‘<container/documentID>’ 
   OUTPUT_OPTIONS =  
     ‘REJECT_TYPE: Drop', 
     --Note: Do not need external file format in this case 
          
);
```



### <a name="example-11---power-bi"></a>Przykład 11 — Power BI

Typ: dane wyjściowe<br>
Parametry:
- Alias danych wyjściowych 
- Nazwa zestawu danych 
- Nazwa tabeli 


Składnia:

```sql
CREATE DATABASE SCOPED CREDENTIAL PBIDBCredName 
WITH IDENTITY = ‘Managed Identity’; 
 
CREATE EXTERNAL DATA SOURCE MyPbi_tweets 
WITH 
( 
  LOCATION = 'pbi://dataset/’, 
  CREDENTIAL = PBIDBCredName 
 
); 
 
CREATE EXTERNAL STREAM MyPbiOutput 
WITH ( 
   DATA_SOURCE = MyPbi_tweets, 
   LOCATION = 'tableName', 
   OUTPUT_OPTIONS =  
     ‘REJECT_TYPE: Drop' 
        
);
```

### <a name="example-12---azure-function"></a>Przykład 12 — funkcja platformy Azure

Typ: dane wyjściowe<br>
Parametry:
- Aplikacja funkcji 
- Funkcja 
- Klucz 
- Maksymalny rozmiar wsadu 
- Maksymalna liczba partii 

Składnia:

```sql
CREATE DATABASE SCOPED CREDENTIAL AzureFunctionCredName 
WITH IDENTITY = ‘Function Key’, 
SECRET = '<function_key>'; 
 
CREATE EXTERNAL DATA SOURCE MyTargetTableStorage 
WITH 
(     
  LOCATION = 'abfss://<storage_account>.dfs.core.windows.net’, 
  CREDENTIAL = TableStorageCredName 
); 
 
CREATE EXTERNAL STREAM MyTargetTableStorageOutput 
WITH ( 
   DATA_SOURCE = MyTargetTableStorage, 
   LOCATION = ‘<TableName>’, 
   OUTPUT_OPTIONS =  
     ‘REJECT_TYPE: 'Drop'      
     PARTITION_KEY: ‘<column_partition_key>, 
     ROW_KEY: <column_row_key>, 
     BATCH_SIZE: 100’ 
); 
```


## <a name="see-also"></a>Zobacz także

- [ALTER EXTERNAL STREAM (Transact-SQL)](alter-external-stream-transact-sql.md) 
- [Usuń strumień zewnętrzny (Transact-SQL)](drop-external-stream-transact-sql.md) 

