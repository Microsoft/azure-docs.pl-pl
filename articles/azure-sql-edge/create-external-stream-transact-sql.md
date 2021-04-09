---
title: TWORZENIE strumienia zewnętrznego (Transact-SQL) — Azure SQL Edge
description: Dowiedz się więcej na temat instrukcji CREATE EXTERNAL STREAM w usłudze Azure SQL Edge
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 07/27/2020
ms.openlocfilehash: 203abe2b6def478dc1747dd4ce638b5b62707612
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101659226"
---
# <a name="create-external-stream-transact-sql"></a>Utwórz strumień zewnętrzny (Transact-SQL)

Obiekt strumienia zewnętrznego ma dwa przeznaczenie zarówno strumienia wejściowego, jak i wyjściowego. Może służyć jako dane wejściowe do wykonywania zapytań dotyczących danych przesyłanych strumieniowo z usług pozyskiwania zdarzeń, takich jak usługa Azure Event Hub, Azure IoT Hub (lub centrum brzegowe) lub Kafka. może służyć jako dane wyjściowe, aby określić, gdzie i jak mają być przechowywane wyniki zapytania przesyłania strumieniowego.

ZEWNĘTRZNY strumień można także określić i utworzyć jako dane wyjściowe oraz dane wejściowe dla usług takich jak centrum zdarzeń lub usługa BLOB Storage. Ułatwia to scenariusze łańcucha, w których zapytanie przesyłane strumieniowo utrzymuje wyniki do zewnętrznego strumienia jako dane wyjściowe i inne zapytanie przesyłane strumieniowo z tego samego strumienia zewnętrznego jako dane wejściowe.

Usługa Azure SQL Edge obecnie obsługuje tylko następujące źródła danych jako dane wejściowe i wyjściowe strumienia.

| Typ źródła danych | Dane wejściowe | Dane wyjściowe | Opis |
|------------------|-------|--------|------------------|
| Azure IoT Edge Hub | Y | Y | Źródło danych do odczytu i zapisu danych przesyłanych strumieniowo do centrum Azure IoT Edge. Aby uzyskać więcej informacji, zobacz [IoT Edge Hub](../iot-edge/iot-edge-runtime.md#iot-edge-hub).|
| SQL Database | N | Y | Połączenie ze źródłem danych do zapisywania danych przesyłanych strumieniowo do SQL Database. Baza danych może być lokalną bazą danych w usłudze Azure SQL Edge lub zdalną bazą danych w SQL Server lub Azure SQL Database.|
| Kafka | Y | N | Źródło danych do odczytu danych przesyłanych strumieniowo z tematu Kafka. Obsługa Kafka jest niedostępna dla wersji ARM64 usługi Azure SQL Edge.|



## <a name="syntax"></a>Składnia

```sql
CREATE EXTERNAL STREAM {external_stream_name}  
( <column_definition> [, <column_definition> ] * ) -- Used for Inputs - optional 
WITH  ( <with_options> )

<column_definition> ::=
  column_name <column_data_type>

<data_type> ::=
[ type_schema_name . ] type_name
    [ ( precision [ , scale ] | max ) ]

<with_options> ::=
  DATA_SOURCE = data_source_name, 
  LOCATION = location_name, 
  [FILE_FORMAT = external_file_format_name], --Used for Inputs - optional 
  [<optional_input_options>],
  [<optional_output_options>], 
  TAGS = <tag_column_value>

<optional_input_options> ::= 
  INPUT_OPTIONS = '[<Input_options_data>]'

<Input_option_data> ::= 
      <input_option_values> [ , <input_option_values> ]

<input_option_values> ::=
  PARTITIONS: [number_of_partitions]
  | CONSUMER_GROUP: [ consumer_group_name ] 
  | TIME_POLICY: [ time_policy ] 
  | LATE_EVENT_TOLERANCE: [ late_event_tolerance_value ] 
  | OUT_OF_ORDER_EVENT_TOLERANCE: [ out_of_order_tolerance_value ]
  
<optional_output_options> ::= 
  OUTPUT_OPTIONS = '[<output_option_data>]'

<output_option_data> ::= 
      <output_option_values> [ , <output_option_values> ]

<output_option_values> ::=
   REJECT_POLICY: [ reject_policy ] 
   | MINIMUM_ROWS: [ row_value ] 
   | MAXIMUM_TIME: [ time_value_minutes] 
   | PARTITION_KEY_COLUMN: [ partition_key_column_name ] 
   | PROPERTY_COLUMNS: [ ( [ output_col_name ] ) ] 
   | SYSTEM_PROPERTY_COLUMNS: [ ( [ output_col_name ] ) ] 
   | PARTITION_KEY: [ partition_key_name ] 
   | ROW_KEY: [ row_key_name ] 
   | BATCH_SIZE: [ batch_size_value ] 
   | MAXIMUM_BATCH_COUNT: [ batch_value ] 
   | STAGING_AREA: [ blob_data_source ]
 
<tag_column_value> ::= -- Reserved for Future Usage
); 
```

## <a name="arguments"></a>Argumenty

- [DATA_SOURCE](/sql/t-sql/statements/create-external-data-source-transact-sql/)
- [FILE_FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql/)
- **Lokalizacja**: Określa nazwę rzeczywistego danych lub lokalizacji w źródle danych. 
   - W przypadku obiektów usługi Edge Hub lub strumienia Kafka Lokalizacja określa nazwę centrum krawędzi lub tematu Kafka, z którego ma zostać odczytana lub zapisana.
   - W przypadku obiektów usługi SQL Stream (SQL Server, Azure SQL Database lub Azure SQL Edge) określa nazwę tabeli. Jeśli strumień jest tworzony w tej samej bazie danych i schemacie co tabela docelowa, wystarczy tylko nazwa tabeli. W przeciwnym razie należy w pełni kwalifikować się do nazwy tabeli (<database_name. schema_name. table_name).
   - W przypadku lokalizacji obiektu usługi Azure Blob Storage Stream odwołuje się do wzorca ścieżki, który ma być używany wewnątrz kontenera obiektów BLOB. Aby uzyskać więcej informacji na temat tej funkcji, zobacz (/articles/Stream-Analytics/Stream-Analytics-define-Outputs.MD # BLOB-Storage-and-Azure-Data-Lake-Gen2)

- **INPUT_OPTIONS**: Określ opcje jako pary klucz-wartość dla usług takich jak Kafka, IoT Edge Hub, które są danymi wejściowymi zapytań przesyłania strumieniowego
    - PARTYCJE: liczba partycji zdefiniowanych dla tematu. Maksymalna liczba partycji, których można użyć, jest ograniczona do 32.
      - Dotyczy strumieni danych wejściowych Kafka
    - CONSUMER_GROUP: Centra zdarzeń i IoT ograniczają liczbę czytelników w ramach jednej grupy odbiorców (do 5). Pozostawienie tego pola pustego spowoduje użycie grupy konsumentów "$Default".
      - Zarezerwowane do użycia w przyszłości. Nie ma zastosowania do usługi Azure SQL Edge.  
    - TIME_POLICY: opisuje, czy należy porzucić zdarzenia, czy też dostosować czas zdarzenia, gdy opóźnione zdarzenia lub zdarzenia poza kolejnością przechodzą wartość tolerancji.
      - Zarezerwowane do użycia w przyszłości. Nie ma zastosowania do usługi Azure SQL Edge.
    - LATE_EVENT_TOLERANCE: maksymalny akceptowalny czas oczekiwania. Opóźnienie reprezentuje różnicę między sygnaturą czasową zdarzenia a zegarem systemowym.
      - Zarezerwowane do użycia w przyszłości. Nie ma zastosowania do usługi Azure SQL Edge.
    - OUT_OF_ORDER_EVENT_TOLERANCE: zdarzenia mogą być przychodzące po wykonaniu podróży od danych wejściowych do zapytania przesyłania strumieniowego. Te zdarzenia mogą być akceptowane w stanie takim, w jakim jest lub można wstrzymać pracę w określonym okresie, aby zmienić ich kolejność.
      - Zarezerwowane do użycia w przyszłości. Nie ma zastosowania do usługi Azure SQL Edge.
        
- **OUTPUT_OPTIONS**: Określ opcje jako pary klucz-wartość dla obsługiwanych usług, które są wyprowadzane strumieniowo zapytania 
  - REJECT_POLICY: UPUŚĆ | Ponów PRÓBę gatunku zasad obsługi błędów danych w przypadku wystąpienia błędów konwersji danych. 
    - Dotyczy wszystkich obsługiwanych danych wyjściowych 
  - MINIMUM_ROWS:  
    Minimalna liczba wymaganych wierszy dla partii zapisywana w danych wyjściowych. Dla Parquet każda partia utworzy nowy plik. 
    - Dotyczy wszystkich obsługiwanych danych wyjściowych 
  - MAXIMUM_TIME:  
    Maksymalny czas oczekiwania (w minutach) na partię. Po upływie tego czasu partia zostanie zapisywana w danych wyjściowych, nawet jeśli nie spełniono wymagań dotyczących minimalnych wierszy. 
    - Dotyczy wszystkich obsługiwanych danych wyjściowych 
  - PARTITION_KEY_COLUMN:  
    Kolumna używana dla klucza partycji. 
    - Zarezerwowane do użycia w przyszłości. Nie ma zastosowania do usługi Azure SQL Edge.
  - PROPERTY_COLUMNS:  
    Rozdzielana przecinkami lista nazw kolumn wyjściowych, które będą dołączane do komunikatów jako właściwości niestandardowe, jeśli zostały podane.  
    - Zarezerwowane do użycia w przyszłości. Nie ma zastosowania do usługi Azure SQL Edge. 
  - SYSTEM_PROPERTY_COLUMNS:  
    Kolekcja par nazwa-wartość i kolumny wyjściowe w formacie JSON, które mają zostać wypełnione na Service Bus komunikatów. np. {"MessageId": "Kolumna1", "PartitionKey": "Kolumna2"} 
    - Zarezerwowane do użycia w przyszłości. Nie ma zastosowania do usługi Azure SQL Edge. 
  - PARTITION_KEY:  
    Nazwa kolumny wyjściowej zawierającej klucz partycji. Klucz partycji jest unikatowym identyfikatorem partycji w danej tabeli, która stanowi pierwszą część klucza podstawowego jednostki. Jest to wartość ciągu, która może mieć długość do 1 KB. 
    - Zarezerwowane do użycia w przyszłości. Nie ma zastosowania do usługi Azure SQL Edge.
  - ROW_KEY:  
    Nazwa kolumny wyjściowej zawierającej klucz wiersza. Klucz wiersza jest unikatowym identyfikatorem dla jednostki w ramach danej partycji. Stanowi on drugą część klucza podstawowego jednostki. Klucz wiersza jest wartością ciągu, która może mieć długość do 1 KB. 
    - Zarezerwowane do użycia w przyszłości. Nie ma zastosowania do usługi Azure SQL Edge.
  - BATCH_SIZE:  
    Oznacza to liczbę transakcji dla magazynu tabel, w których maksymalna wartość może nastąpić do 100 rekordów. W przypadku Azure Functions oznacza to, że rozmiar wsadu w bajtach wysłanych do funkcji dla wywołania — wartość domyślna to 256 kB. 
    - Zarezerwowane do użycia w przyszłości. Nie ma zastosowania do usługi Azure SQL Edge. 
  - MAXIMUM_BATCH_COUNT:  
    Maksymalna liczba zdarzeń wysyłanych do funkcji dla wywołania funkcji platformy Azure — wartość domyślna to 100. W przypadku SQL Database oznacza to maksymalną liczbę rekordów wysłanych z każdą zbiorczą transakcją wstawiania — wartość domyślna to 10 000. 
    - Dotyczy wszystkich danych wyjściowych opartych na języku SQL 
  - STAGING_AREA: obiekt zewnętrznego źródła danych do Blob Storage obszaru przejściowego na potrzeby pozyskiwania danych o wysokiej przepływności w usłudze Azure Synapse Analytics 
    - Zarezerwowane do użycia w przyszłości. Nie ma zastosowania do usługi Azure SQL Edge.


## <a name="examples"></a>Przykłady

### <a name="example-1---edgehub"></a>Przykład 1 — EdgeHub

Typ: dane wejściowe lub wyjściowe<br>

Składnia:

```sql
CREATE EXTERNAL DATA SOURCE MyEdgeHub 
WITH  
(      
  LOCATION = 'edgehub://'       
); 
 
CREATE EXTERNAL FILE FORMAT myFileFormat  
WITH (  
   FORMAT_TYPE = JSON, 
); 
 
CREATE EXTERNAL STREAM Stream_A  
WITH    
(   
   DATA_SOURCE = MyEdgeHub, 
   FILE_FORMAT = myFileFormat, 
   LOCATION = '<mytopicname>', 
   OUTPUT_OPTIONS =   
     'REJECT_TYPE: Drop'
);
```

### <a name="example-2---azure-sql-database-azure-sql-edge-sql-server"></a>Przykład 2 — Azure SQL Database, Azure SQL Edge, SQL Server

Typ: dane wyjściowe<br>

Składnia:

```sql
CREATE DATABASE SCOPED CREDENTIAL SQLCredName 
WITH IDENTITY = '<user>', 
SECRET = '<password>'; 
 
-- Azure SQL Database 
CREATE EXTERNAL DATA SOURCE MyTargetSQLTabl 
WITH 
(     
  LOCATION = '<my_server_name>.database.windows.net', 
  CREDENTIAL = SQLCredName 
); 
 
--SQL Server or Azure SQL Edge
CREATE EXTERNAL DATA SOURCE MyTargetSQLTabl 
WITH 
(     
  LOCATION = ' <sqlserver://<ipaddress>,<port>', 
  CREDENTIAL = SQLCredName 
); 

CREATE EXTERNAL STREAM Stream_A 
WITH   
(  
    DATA_SOURCE = MyTargetSQLTable, 
    LOCATION = '<DatabaseName>.<SchemaName>.<TableName>' ,
   --Note: If table is contained in the database, <TableName> should be sufficient 
    OUTPUT_OPTIONS =  
      'REJECT_TYPE: Drop'
); 
```

### <a name="example-3---kafka"></a>Przykład 3 — Kafka

Typ: dane wejściowe<br>

Składnia:
```sql
CREATE EXTERNAL DATA SOURCE MyKafka_tweets 
WITH 
( 
  --The location maps to KafkaBootstrapServer 
  LOCATION = 'kafka://<kafkaserver>:<ipaddress>', 
  CREDENTIAL = kafkaCredName 
); 
 
CREATE EXTERNAL FILE FORMAT myFileFormat 
WITH ( 
    FORMAT_TYPE = JSON, 
    DATA_COMPRESSION = 'org.apache.hadoop.io.compress.GzipCodec'
); 

CREATE EXTERNAL STREAM Stream_A (user_id VARCHAR, tweet VARCHAR) 
WITH   
(  
    DATA_SOURCE = MyKafka_tweets, 
    LOCATION = '<KafkaTopicName>', 
    FILE_FORMAT = myFileFormat,  
    INPUT_OPTIONS =  
      'PARTITIONS: 5'
); 
```

## <a name="see-also"></a>Zobacz też

- [Usuń strumień zewnętrzny (Transact-SQL)](drop-external-stream-transact-sql.md)
