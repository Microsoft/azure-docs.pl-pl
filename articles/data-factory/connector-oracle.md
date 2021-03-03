---
title: Kopiowanie danych do i z programu Oracle przy użyciu Azure Data Factory
description: Informacje o kopiowaniu danych z obsługiwanych magazynów źródłowych do bazy danych Oracle lub z programu Oracle do obsługiwanych magazynów ujścia przy użyciu Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/20/2021
ms.author: jingwang
ms.openlocfilehash: ebafac024593767e884be908acbf0efb9ead50e9
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101703307"
---
# <a name="copy-data-from-and-to-oracle-by-using-azure-data-factory"></a>Kopiowanie danych z i do programu Oracle przy użyciu Azure Data Factory

> [!div class="op_single_selector" title1="Wybierz używaną wersję usługi Data Factory:"]
> * [Wersja 1](v1/data-factory-onprem-oracle-connector.md)
> * [Bieżąca wersja](connector-oracle.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule opisano sposób używania działania kopiowania w Azure Data Factory do kopiowania danych z i do bazy danych programu Oracle. Kompilacja jest oparta na [przeglądzie działania kopiowania](copy-activity-overview.md).

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Ten łącznik Oracle jest obsługiwany dla następujących działań:

- [Działanie kopiowania](copy-activity-overview.md) z [obsługiwaną macierzą źródłową/ujścia](copy-activity-overview.md)
- [Działanie Lookup](control-flow-lookup-activity.md)

Dane z bazy danych programu Oracle można kopiować do dowolnego obsługiwanego magazynu danych ujścia. Możesz również skopiować dane z dowolnego obsługiwanego źródłowego magazynu danych do bazy danych programu Oracle. Listę magazynów danych obsługiwanych jako źródła lub ujścia przez działanie kopiowania można znaleźć w tabeli [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) .

W przypadku tego łącznika Oracle obsługuje:

- Następujące wersje bazy danych Oracle:
    - Oracle 19c R1 (19,1) i nowsze
    - Oracle 18c R1 (18,1) i nowsze
    - Oracle 12c R1 (12,1) i nowsze
    - Oracle 11g R1 (11,1) i nowsze
    - Oracle 10g R1 (10,1) i nowsze
    - Oracle 9i R2 (9,2) i nowsze
    - Oracle 8i R3 (8.1.7) i nowsze
    - Oracle Database usługi Cloud Exadata
- Równoległe kopiowanie ze źródła programu Oracle. Aby uzyskać szczegółowe informacje, zobacz sekcję [copy Parallel from Oracle](#parallel-copy-from-oracle) .

> [!Note]
> Serwer proxy Oracle nie jest obsługiwany.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)] 

Środowisko Integration Runtime zapewnia wbudowany sterownik Oracle. W związku z tym nie trzeba ręcznie instalować sterownika podczas kopiowania danych z programu i do programu Oracle.

## <a name="get-started"></a>Rozpoczęcie pracy

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje o właściwościach, które są używane do definiowania jednostek Data Factory specyficznych dla łącznika Oracle.

## <a name="linked-service-properties"></a>Właściwości połączonej usługi

Połączona usługa Oracle obsługuje następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość Type musi mieć wartość **Oracle**. | Tak |
| Parametry połączenia | Określa informacje, które są konieczne do nawiązania połączenia z wystąpieniem Oracle Database. <br/>Możesz również wprowadzić hasło w Azure Key Vault i ściągnąć `password` konfigurację z parametrów połączenia. Zapoznaj się z poniższymi przykładami i [Zapisz poświadczenia w Azure Key Vault](store-credentials-in-key-vault.md) , aby uzyskać więcej szczegółów. <br><br>**Obsługiwany typ połączenia**: możesz użyć **identyfikatora SID Oracle** lub **nazwy usługi Oracle** , aby zidentyfikować swoją bazę danych:<br>— Jeśli używasz identyfikatora SID: `Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;`<br>— Jeśli używasz nazwy usługi: `Host=<host>;Port=<port>;ServiceName=<servicename>;User Id=<username>;Password=<password>;`<br>Aby skorzystać z zaawansowanych opcji połączenia z natywnym oprogramowaniem Oracle, możesz dodać wpis w [pliku tnsnames. Plik ORA](http://www.orafaq.com/wiki/Tnsnames.ora) na serwerze Oracle i w połączonej usłudze Oracle ADF, wybierz, aby użyć typu połączenia nazwa usługi Oracle i skonfigurować odpowiednią nazwę usługi. | Tak |
| Właściwością connectvia | [Środowisko Integration Runtime](concepts-integration-runtime.md) służy do nawiązywania połączenia z magazynem danych. Dowiedz się więcej z sekcji [wymagania wstępne](#prerequisites) . Jeśli nie zostanie określony, zostanie użyta domyślna Azure Integration Runtime. |Nie |

>[!TIP]
>Jeśli wystąpi błąd, "ORA-01025: UPI parametr spoza zakresu", a wersja Oracle to 8i, Dodaj `WireProtocolMode=1` do parametrów połączenia. Następnie spróbuj ponownie.

Jeśli masz wiele wystąpień programu Oracle do scenariusza pracy awaryjnej, możesz utworzyć połączoną usługę Oracle i wypełnić podstawowy host, port, nazwę użytkownika, hasło itp., a następnie dodać nowe "**dodatkowe właściwości połączenia**" z nazwą właściwości `AlternateServers` i wartością jako-nie `(HostName=<secondary host>:PortNumber=<secondary port>:ServiceName=<secondary service name>)` przegap nawiasów i zwrócić uwagę na dwukropek ( `:` ) jako separator. Na przykład następująca wartość serwerów alternatywnych definiuje dwa alternatywne serwery baz danych do przełączania w tryb failover: `(HostName=AccountingOracleServer:PortNumber=1521:SID=Accounting,HostName=255.201.11.24:PortNumber=1522:ServiceName=ABackup.NA.MyCompany)` .

Więcej właściwości połączenia, które można ustawić w parametrach połączenia dla danego przypadku:

| Właściwość | Opis | Dozwolone wartości |
|:--- |:--- |:--- |
| Rozmiaru tablicy |Liczba bajtów, które łącznik może pobrać w pojedynczej rejsie sieci. Np `ArraySize=‭10485760‬` ..<br/><br/>Większe wartości zwiększają przepływność przez skrócenie liczby prób pobrania danych w sieci. Mniejsze wartości zwiększają czas odpowiedzi, ponieważ trwają mniejsze opóźnienie oczekiwania na przesłanie danych przez serwer. | Liczba całkowita z przedziału od 1 do 4294967296 (4 GB). Wartość domyślna to `60000`. Wartość 1 nie definiuje liczby bajtów, ale wskazuje na przydzielanie miejsca dla dokładnie jednego wiersza danych. |

Aby włączyć szyfrowanie na połączeniu z programem Oracle, dostępne są dwie opcje:

-   Aby użyć **algorytmu Triple-DES Encryption (3DES) i Advanced Encryption Standard (AES)**, po stronie serwera Oracle przejdź do pozycji Oracle Advanced Security (OAS) i skonfiguruj ustawienia szyfrowania. Aby uzyskać szczegółowe informacje, zobacz tę [dokumentację firmy Oracle](https://docs.oracle.com/cd/E11882_01/network.112/e40393/asointro.htm#i1008759). Łącznik programu Oracle Application Development Framework (ADF) automatycznie negocjuje metodę szyfrowania, aby użyć konfiguracji w OAS podczas nawiązywania połączenia z bazą danych Oracle.

-   Aby korzystać z **protokołu TLS**:

    1.  Pobierz informacje o certyfikacie TLS/SSL. Pobierz informacje o certyfikacie protokołu TLS/SSL w formacie Distinguished Encoding Rules (DER) i Zapisz dane wyjściowe (-----Rozpocznij certyfikat... Zakończ-----certyfikatów) jako plik tekstowy.

        ```
        openssl x509 -inform DER -in [Full Path to the DER Certificate including the name of the DER Certificate] -text
        ```

        **Przykład:** Wyodrębnij informacje o certyfikatach z DERcert. cer, a następnie Zapisz dane wyjściowe do cert.txt.

        ```
        openssl x509 -inform DER -in DERcert.cer -text
        Output:
        -----BEGIN CERTIFICATE-----
        XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
        XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
        XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
        XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
        XXXXXXXXX
        -----END CERTIFICATE-----
        ```
    
    2.  Kompiluj `keystore` lub `truststore` . Następujące polecenie tworzy `truststore` plik z hasłem lub bez niego w formacie PKCS-12.

        ```
        openssl pkcs12 -in [Path to the file created in the previous step] -out [Path and name of TrustStore] -passout pass:[Keystore PWD] -nokeys -export
        ```

        **Przykład:** Utwórz plik PKCS12 `truststore` o nazwie MyTrustStoreFile z hasłem.

        ```
        openssl pkcs12 -in cert.txt -out MyTrustStoreFile -passout pass:ThePWD -nokeys -export  
        ```

    3.  Umieść `truststore` plik na urządzeniu z własnym obsługą podczerwieni. Na przykład Umieść plik w lokalizacji C:\MyTrustStoreFile.
    4.  W Azure Data Factory Skonfiguruj parametry połączenia Oracle z `EncryptionMethod=1` i odpowiednią `TrustStore` / `TrustStorePassword` wartością. Na przykład `Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;EncryptionMethod=1;TrustStore=C:\\MyTrustStoreFile;TrustStorePassword=<trust_store_password>`.

**Przykład:**

```json
{
    "name": "OracleLinkedService",
    "properties": {
        "type": "Oracle",
        "typeProperties": {
            "connectionString": "Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Przykład: Przechowuj hasło w Azure Key Vault**

```json
{
    "name": "OracleLinkedService",
    "properties": {
        "type": "Oracle",
        "typeProperties": {
            "connectionString": "Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;",
            "password": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```
## <a name="dataset-properties"></a>Właściwości zestawu danych

Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych Oracle. Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania zestawów danych, zobacz [zestawy danych](concepts-datasets-linked-services.md). 

Aby skopiować dane z i do programu Oracle, należy ustawić Właściwość Type zestawu danych na `OracleTable` . Obsługiwane są następujące właściwości.

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość Type zestawu danych musi być ustawiona na wartość `OracleTable` . | Tak |
| schema | Nazwa schematu. |Nie dla źródła, tak dla ujścia  |
| tabela | Nazwa tabeli/widoku. |Nie dla źródła, tak dla ujścia  |
| tableName | Nazwa tabeli/widoku ze schematem. Ta właściwość jest obsługiwana w celu zapewnienia zgodności z poprzednimi wersjami. W przypadku nowych obciążeń Użyj `schema` i `table` . | Nie dla źródła, tak dla ujścia |

**Przykład:**

```json
{
    "name": "OracleDataset",
    "properties":
    {
        "type": "OracleTable",
        "schema": [],
        "typeProperties": {
            "schema": "<schema_name>",
            "table": "<table_name>"
        },
        "linkedServiceName": {
            "referenceName": "<Oracle linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Ta sekcja zawiera listę właściwości obsługiwanych przez źródło i ujścia programu Oracle. Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania działań, zobacz [potoki](concepts-pipelines-activities.md). 

### <a name="oracle-as-source"></a>Oracle as Source

>[!TIP]
>Aby wydajnie ładować dane z programu Oracle przy użyciu partycjonowania danych, Dowiedz się więcej z [kopii równoległej od firmy Oracle](#parallel-copy-from-oracle).

Aby skopiować dane z programu Oracle, należy ustawić typ źródła w działaniu kopiowania na `OracleSource` . W sekcji **Źródło** działania kopiowania są obsługiwane następujące właściwości.

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość Type źródła działania Copy musi być ustawiona na wartość `OracleSource` . | Tak |
| oracleReaderQuery | Użyj niestandardowego zapytania SQL, aby odczytać dane. Może to być na przykład `"SELECT * FROM MyTable"`.<br>Po włączeniu obciążenia partycjonowanego należy podłączyć wszystkie odpowiednie wbudowane parametry partycji w zapytaniu. Przykłady można znaleźć w sekcji [Kopiowanie równoległe z programu Oracle](#parallel-copy-from-oracle) . | Nie |
| partitionOptions | Określa opcje partycjonowania danych używane do ładowania danych z programu Oracle. <br>Dozwolone wartości to **none** (wartość domyślna), **PhysicalPartitionsOfTable** i **DynamicRange**.<br>Gdy opcja partycji jest włączona (to nie jest `None` ), stopień równoległości do współbieżnego ładowania danych z bazy danych Oracle jest kontrolowany przez [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) ustawienie działania kopiowania. | Nie |
| partitionSettings | Określ grupę ustawień partycjonowania danych. <br>Zastosuj, gdy opcja partycji nie jest `None` . | Nie |
| partitionNames | Lista partycji fizycznych, które muszą zostać skopiowane. <br>Zastosuj, gdy opcja partycji to `PhysicalPartitionsOfTable` . Jeśli używasz zapytania do pobierania danych źródłowych, hak `?AdfTabularPartitionName` w klauzuli WHERE. Aby zapoznać się z przykładem, zobacz sekcję [copy Parallel from Oracle](#parallel-copy-from-oracle) . | Nie |
| partitionColumnName | Określ nazwę kolumny źródłowej **w typie liczb całkowitych** , która będzie używana przez partycjonowanie zakresu do kopiowania równoległego. Jeśli nie zostanie określony, klucz podstawowy tabeli zostanie wykryty i użyty jako kolumna partycji. <br>Zastosuj, gdy opcja partycji to `DynamicRange` . Jeśli używasz zapytania do pobierania danych źródłowych, hak  `?AdfRangePartitionColumnName` w klauzuli WHERE. Aby zapoznać się z przykładem, zobacz sekcję [copy Parallel from Oracle](#parallel-copy-from-oracle) . | Nie |
| partitionUpperBound | Maksymalna wartość kolumny partycji, w której mają zostać skopiowane dane. <br>Zastosuj, gdy opcja partycji to `DynamicRange` . Jeśli używasz zapytania do pobierania danych źródłowych, hak `?AdfRangePartitionUpbound` w klauzuli WHERE. Aby zapoznać się z przykładem, zobacz sekcję [copy Parallel from Oracle](#parallel-copy-from-oracle) . | Nie |
| partitionLowerBound | Minimalna wartość kolumny partycji, w której mają zostać skopiowane dane. <br>Zastosuj, gdy opcja partycji to `DynamicRange` . Jeśli używasz zapytania do pobierania danych źródłowych, hak `?AdfRangePartitionLowbound` w klauzuli WHERE. Aby zapoznać się z przykładem, zobacz sekcję [copy Parallel from Oracle](#parallel-copy-from-oracle) . | Nie |

**Przykład: Kopiuj dane przy użyciu podstawowego zapytania bez partycji**

```json
"activities":[
    {
        "name": "CopyFromOracle",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Oracle input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "OracleSource",
                "oracleReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="oracle-as-sink"></a>Oracle as sink

Aby skopiować dane do programu Oracle, należy ustawić typ ujścia w działaniu kopiowania na `OracleSink` . W sekcji **ujścia** działania kopiowania są obsługiwane następujące właściwości.

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość Type ujścia działania Copy musi być ustawiona na wartość `OracleSink` . | Tak |
| writeBatchSize | Wstawia dane do tabeli SQL, gdy rozmiar buforu osiągnie wartość `writeBatchSize` .<br/>Dozwolone wartości to liczba całkowita (liczba wierszy). |Nie (domyślnie 10 000) |
| writeBatchTimeout | Czas oczekiwania na zakończenie operacji wstawiania partii przed przekroczeniem limitu czasu.<br/>Dozwolone wartości to TimeSpan. Przykładem jest 00:30:00 (30 minut). | Nie |
| preCopyScript | Określ zapytanie SQL dla działania kopiowania, które ma zostać uruchomione przed zapisaniem danych w programie Oracle w każdym przebiegu. Ta właściwość służy do czyszczenia wstępnie załadowanych danych. | Nie |

**Przykład:**

```json
"activities":[
    {
        "name": "CopyToOracle",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Oracle output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "OracleSink"
            }
        }
    }
]
```

## <a name="parallel-copy-from-oracle"></a>Kopiowanie równoległe z programu Oracle

Łącznik Data Factory Oracle zapewnia wbudowane Partycjonowanie danych do kopiowania danych ze środowiska Oracle równolegle. Opcje partycjonowania danych można znaleźć na karcie **Źródło** działania kopiowania.

![Zrzut ekranu przedstawiający opcje partycji](./media/connector-oracle/connector-oracle-partition-options.png)

Po włączeniu kopiowania partycjonowanego Data Factory uruchamia zapytania równoległe względem źródła programu Oracle w celu załadowania danych przez partycje. Stopień równoległy jest kontrolowany przez [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) ustawienie działania kopiowania. Jeśli na przykład ustawisz `parallelCopies` cztery, Data Factory współbieżnie generuje i uruchamia cztery zapytania w oparciu o określoną opcję partycji i ustawienia, a każde zapytanie pobiera część danych z bazy danych Oracle.

Zalecane jest włączenie kopiowania równoległego przy użyciu partycjonowania danych, szczególnie podczas ładowania dużej ilości danych z bazy danych programu Oracle. Poniżej przedstawiono sugerowane konfiguracje dla różnych scenariuszy. Podczas kopiowania danych do magazynu danych opartego na plikach, należy ponownie wykonać zapis do folderu jako wiele plików (Określ tylko nazwę folderu), w którym to przypadku wydajność jest lepsza niż zapis do pojedynczego pliku.

| Scenariusz                                                     | Sugerowane ustawienia                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Pełne ładowanie z dużej tabeli z partycjami fizycznymi.          | **Opcja partycji**: partycje fizyczne tabeli. <br><br/>Podczas wykonywania Data Factory automatycznie wykrywa partycje fizyczne i kopiuje dane przez partycje. |
| Pełne ładowanie z dużej tabeli, bez partycji fizycznych, z kolumną liczb całkowitych na potrzeby partycjonowania danych. | **Opcje partycji**: dynamiczna partycja zakresu.<br>**Kolumna partycji**: określ kolumnę używaną do partycjonowania danych. Jeśli nie zostanie określony, zostanie użyta kolumna klucza podstawowego. |
| Załaduj dużą ilość danych przy użyciu zapytania niestandardowego z partycjami fizycznymi. | **Opcja partycji**: partycje fizyczne tabeli.<br>**Zapytanie**: `SELECT * FROM <TABLENAME> PARTITION("?AdfTabularPartitionName") WHERE <your_additional_where_clause>` .<br>**Nazwa partycji**: Określ nazwy partycji, z których mają zostać skopiowane dane. Jeśli nie zostanie określony, Data Factory automatycznie wykrywa partycje fizyczne w tabeli określonej w zestawie danych Oracle.<br><br>Podczas wykonywania Data Factory zastępuje `?AdfTabularPartitionName` z rzeczywistą nazwą partycji i wysyła do firmy Oracle. |
| Załaduj dużą ilość danych przy użyciu zapytania niestandardowego bez partycji fizycznych, jednocześnie z kolumną liczb całkowitych na potrzeby partycjonowania danych. | **Opcje partycji**: dynamiczna partycja zakresu.<br>**Zapytanie**: `SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>` .<br>**Kolumna partycji**: określ kolumnę używaną do partycjonowania danych. Można podzielić na kolumny z typem danych Integer.<br>**Górna** granica partycji i **Dolna granica partycji**: Określ, czy chcesz filtrować względem kolumny partycji, aby pobierać dane tylko między niższym i górnym zakresem.<br><br>Podczas wykonywania Data Factory zamienia `?AdfRangePartitionColumnName` , `?AdfRangePartitionUpbound` , i `?AdfRangePartitionLowbound` z rzeczywistą nazwą kolumny i zakresem wartości dla każdej partycji i wysyła do firmy Oracle. <br>Na przykład jeśli kolumna partycji "ID" jest ustawiona z dolną granicą 1 i górną granicą 80, z kopią równoległą ustawioną na wartość 4, Data Factory pobiera dane przez 4 partycje. Ich identyfikatory należą do zakresu od [1, 20], [21, 40], [41, 60] i [61, 80]. |

> [!TIP]
> Podczas kopiowania danych z tabeli niepartycjonowanej, można użyć opcji partycji zakres dynamiczny, aby podzielić na kolumnę z liczbą całkowitą. Jeśli dane źródłowe nie mają tego typu kolumny, można wykorzystać funkcję [ORA_HASH]( https://docs.oracle.com/database/121/SQLRF/functions136.htm) w zapytaniu źródłowym, aby wygenerować kolumnę i użyć jej jako kolumny partycji.

**Przykład: zapytanie z partycją fizyczną**

```json
"source": {
    "type": "OracleSource",
    "query": "SELECT * FROM <TABLENAME> PARTITION(\"?AdfTabularPartitionName\") WHERE <your_additional_where_clause>",
    "partitionOption": "PhysicalPartitionsOfTable",
    "partitionSettings": {
        "partitionNames": [
            "<partitionA_name>",
            "<partitionB_name>"
        ]
    }
}
```

**Przykład: zapytanie z dynamiczną partycją zakresu**

```json
"source": {
    "type": "OracleSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>",
    "partitionOption": "DynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<partition_column_name>",
        "partitionUpperBound": "<upper_value_of_partition_column>",
        "partitionLowerBound": "<lower_value_of_partition_column>"
    }
}
```

## <a name="data-type-mapping-for-oracle"></a>Mapowanie typu danych dla programu Oracle

Podczas kopiowania danych z programu i do programu Oracle są stosowane następujące mapowania. Aby dowiedzieć się, jak działanie kopiowania mapuje schemat źródłowy i typ danych na ujścia, zobacz [Mapowanie schematu i typu danych](copy-activity-schema-and-type-mapping.md).

| Typ danych Oracle | Data Factory typ danych pośrednich |
|:--- |:--- |
| DOTYCZĄCE |Byte [] |
| TWORZENIA |Byte []<br/>(obsługiwane tylko w systemach Oracle 10g i nowszych) |
| DELIKATN |Ciąg |
| OBIEKTÓW CLOB |Ciąg |
| DATE |DateTime |
| FLOAT |Decimal, String (jeśli precyzja > 28) |
| LICZBA CAŁKOWITA |Decimal, String (jeśli precyzja > 28) |
| DŁUGO |Ciąg |
| DŁUGI NIEPRZETWORZONY |Byte [] |
| NCHAR |Ciąg |
| NCLOB |Ciąg |
| Liczba (p, s) |Decimal, String (Jeśli p > 28) |
| Liczba bez dokładności i skali |Double |
| NVARCHAR2 |Ciąg |
| SUROWCÓW |Byte [] |
| Właściwość |Ciąg |
| TIMESTAMP |DateTime |
| SYGNATURA CZASOWA Z LOKALNĄ STREFĄ CZASOWĄ |Ciąg |
| SYGNATURA CZASOWA ZE STREFĄ CZASOWĄ |Ciąg |
| LICZBA CAŁKOWITA BEZ ZNAKU |Liczba |
| VARCHAR2 |Ciąg |
| XML |Ciąg |

> [!NOTE]
> Typy danych INTERWAŁu od roku do miesiąca oraz INTERWAŁu od dnia do sekundy nie są obsługiwane.

## <a name="lookup-activity-properties"></a>Właściwości działania Lookup

Aby dowiedzieć się więcej o właściwościach, sprawdź [działanie Lookup (wyszukiwanie](control-flow-lookup-activity.md)).

## <a name="next-steps"></a>Następne kroki
Listę magazynów danych obsługiwanych jako źródła i ujścia przez działanie kopiowania w Data Factory można znaleźć w temacie [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
