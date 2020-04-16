---
title: Kopiowanie danych do i z oracle przy użyciu usługi Azure Data Factory
description: Dowiedz się, jak kopiować dane z obsługiwanych magazynów źródłowych do bazy danych Oracle lub z Oracle do obsługiwanych magazynów zlewów za pomocą usługi Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: jingwang
ms.openlocfilehash: d37a9bd4cc29ee60f9833ffbcb5a2701a19bbaa7
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416823"
---
# <a name="copy-data-from-and-to-oracle-by-using-azure-data-factory"></a>Kopiowanie danych z i do oracle przy użyciu usługi Azure Data Factory
> [!div class="op_single_selector" title1="Wybierz wersję używanej usługi Data Factory:"]
> * [Wersja 1](v1/data-factory-onprem-oracle-connector.md)
> * [Bieżąca wersja](connector-oracle.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule opisano, jak używać działania kopiowania w usłudze Azure Data Factory do kopiowania danych z i do bazy danych Oracle. Opiera się na [przeglądzie działania kopiowania](copy-activity-overview.md).

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Ten łącznik Oracle jest obsługiwany dla następujących działań:

- [Kopiowanie aktywności](copy-activity-overview.md) z [obsługiwaną macierzą źródło/ujście](copy-activity-overview.md)
- [Działanie odnośnika](control-flow-lookup-activity.md)

Dane można kopiować z bazy danych Oracle do dowolnego obsługiwanego magazynu danych ujścia. Można również skopiować dane z dowolnego obsługiwanego magazynu danych źródłowych do bazy danych Oracle. Aby uzyskać listę magazynów danych, które są obsługiwane jako źródła lub pochłaniacze przez działanie kopiowania, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) tabeli.

W szczególności to złącze Oracle obsługuje:

- Następujące wersje bazy danych Oracle:
    - Oracle 19c R1 (19.1) i wyższe
    - Oracle 18c R1 (18.1) i wyższe
    - Oracle 12c R1 (12.1) i wyższe
    - Oracle 11g R1 (11.1) i wyższe
    - Oracle 10g R1 (10.1) i wyższe
    - Oracle 9i R2 (9.2) i wyższe
    - Oracle 8i R3 (8.1.7) i wyższe
    - Usługa Oracle Database Cloud Exadata
- Kopiowanie równoległe ze źródła Oracle. Szczegółowe informacje można znaleźć w sekcji [Kopia równoległa z oracle.](#parallel-copy-from-oracle)

> [!Note]
> Serwer proxy Oracle nie jest obsługiwany.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)] 

Środowisko wykonawcze integracji zapewnia wbudowany sterownik Oracle. W związku z tym nie trzeba ręcznie zainstalować sterownik podczas kopiowania danych z i do Oracle.

## <a name="get-started"></a>Rozpoczęcie pracy

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje o właściwościach, które są używane do definiowania jednostek usługi Data Factory specyficznych dla łącznika Oracle.

## <a name="linked-service-properties"></a>Połączone właściwości usługi

Usługa połączona Oracle obsługuje następujące właściwości:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość typu musi być ustawiona na **Oracle**. | Tak |
| Parametry połączenia | Określa informacje potrzebne do nawiązania połączenia z wystąpieniem bazy danych Oracle Database. <br/>Można również umieścić hasło w usłudze Azure `password` Key Vault i wyciągnąć konfigurację z ciągu połączenia. Więcej informacji można znaleźć w poniższych przykładach i [poświadczeniach sklepu w usłudze Azure Key Vault.](store-credentials-in-key-vault.md) <br><br>**Obsługiwany typ połączenia:** Do identyfikacji bazy danych można użyć **identyfikatora Oracle SID** lub **oracle service name:**<br>- Jeśli używasz IDENTYFIKATORA SID:`Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;`<br>- Jeśli używasz nazwy usługi:`Host=<host>;Port=<port>;ServiceName=<servicename>;User Id=<username>;Password=<password>;`<br>W przypadku zaawansowanych opcji połączeń natywnych Oracle można dodać wpis w [tnsnames. Ora](http://www.orafaq.com/wiki/Tnsnames.ora) na serwerze Oracle i w usłudze połączonej ADF Oracle wybierz opcję użycia typu połączenia Oracle Service Name i skonfiguruj odpowiednią nazwę usługi. | Tak |
| connectVia | [Środowisko uruchomieniowe integracji,](concepts-integration-runtime.md) które ma być używane do łączenia się z magazynem danych. Dowiedz się więcej z sekcji [Wymagania wstępne.](#prerequisites) Jeśli nie zostanie określony, używany jest domyślny środowiska wykonawczego integracji platformy Azure. |Nie |

>[!TIP]
>Jeśli zostanie wyświetlony błąd "ORA-01025: UPI parametr out of range", a `WireProtocolMode=1` wersja Oracle jest 8i, dodać do ciągu połączenia. Następnie spróbuj ponownie.

Więcej właściwości połączenia można ustawić w ciągu połączenia w przypadku:

| Właściwość | Opis | Dozwolone wartości |
|:--- |:--- |:--- |
| Rozmiar tablicy |Liczba bajtów, które łącznik może pobrać w jednej sieci w obie strony. `ArraySize=‭10485760‬`Np.<br/><br/>Większe wartości zwiększają przepustowość, zmniejszając liczbę razy pobieranie danych w sieci. Mniejsze wartości zwiększają czas odpowiedzi, ponieważ opóźnienie jest mniejsze, ponieważ serwer przesyła dane. | Całkowitej od 1 do 4294967296 (4 GB). Wartością `60000`domyślną jest . Wartość 1 nie definiuje liczby bajtów, ale wskazuje przydzielanie miejsca dla dokładnie jednego wiersza danych. |

Aby włączyć szyfrowanie połączenia Oracle, dostępne są dwie opcje:

-   Aby użyć **szyfrowania Triple-DES (3DES) i advanced encryption standard (AES)** po stronie serwera Oracle, przejdź do programu Oracle Advanced Security (OAS) i skonfiguruj ustawienia szyfrowania. Szczegółowe informacje można znaleźć w dokumentacji [oracle](https://docs.oracle.com/cd/E11882_01/network.112/e40393/asointro.htm#i1008759). Łącznik Oracle Application Development Framework (ADF) automatycznie negocjuje metodę szyfrowania, aby używać metody skonfigurowanego w USŁUDZE OAS podczas ustanawiania połączenia z oracle.

-   Aby użyć **protokołu TLS:**

    1.  Uzyskaj informacje o certyfikacie TLS/SSL. Pobierz informacje o certyfikatach certyfikatu TLS/SSL kodowane reguły kodowania (DER) i zapisz dane wyjściowe (----- rozpocznij certyfikat ... Zakończ certyfikat -----) jako plik tekstowy.

        ```
        openssl x509 -inform DER -in [Full Path to the DER Certificate including the name of the DER Certificate] -text
        ```

        **Przykład:** Wyodrębnij informacje o cert z pliku DERcert.cer, a następnie zapisz dane wyjściowe na cert.txt.

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
    
    2.  Zbuduj `keystore` lub `truststore`. Następujące polecenie tworzy `truststore` plik, z hasłem lub bez hasła, w formacie PKCS-12.

        ```
        openssl pkcs12 -in [Path to the file created in the previous step] -out [Path and name of TrustStore] -passout pass:[Keystore PWD] -nokeys -export
        ```

        **Przykład:** Utwórz plik PKCS12 `truststore` o nazwie MyTrustStoreFile z hasłem.

        ```
        openssl pkcs12 -in cert.txt -out MyTrustStoreFile -passout pass:ThePWD -nokeys -export  
        ```

    3.  Umieść `truststore` plik na samodzielnym komputerze podczerwonym. Na przykład umieść plik w C:\MyTrustStoreFile.
    4.  W usłudze Azure Data Factory skonfiguruj parametry połączenia Oracle `EncryptionMethod=1` z odpowiednią wartością i odpowiednią. `TrustStore` / `TrustStorePassword` Na przykład `Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;EncryptionMethod=1;TrustStore=C:\\MyTrustStoreFile;TrustStorePassword=<trust_store_password>`.

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

**Przykład: hasło magazynu w usłudze Azure Key Vault**

```json
{
    "name": "OracleLinkedService",
    "properties": {
        "type": "Oracle",
        "typeProperties": {
            "connectionString": "Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;",
            "password": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
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

Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych Oracle. Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania zestawów danych, zobacz [Zestawy danych](concepts-datasets-linked-services.md). 

Aby skopiować dane z i do oracle, ustaw `OracleTable`właściwość typu zestawu danych na . Obsługiwane są następujące właściwości.

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość typu zestawu danych musi `OracleTable`być ustawiona na . | Tak |
| Schematu | Nazwa schematu. |Nie dla źródła, tak dla zlewu  |
| tabela | Nazwa tabeli/widoku. |Nie dla źródła, tak dla zlewu  |
| tableName | Nazwa tabeli/widoku ze schematem. Ta właściwość jest obsługiwana w celu zapewnienia zgodności z powrotem. W przypadku nowego `schema` `table`obciążenia, użycia i programu . | Nie dla źródła, tak dla zlewu |

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

Ta sekcja zawiera listę właściwości obsługiwanych przez źródło Oracle i ujście. Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania działań, zobacz [Potoki](concepts-pipelines-activities.md). 

### <a name="oracle-as-source"></a>Oracle jako źródło

>[!TIP]
>Aby wydajnie ładować dane z oracle za pomocą partycjonowania danych, dowiedz się więcej z [kopiowania równoległego z Oracle](#parallel-copy-from-oracle).

Aby skopiować dane z oracle, ustaw typ `OracleSource`źródła w działaniu kopiowania na . Następujące właściwości są obsługiwane w sekcji **źródła** działania kopiowania.

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość typu źródła działania kopiowania `OracleSource`musi być ustawiona na . | Tak |
| oracleReaderQuery (wyroczniaReaderQuery) | Użyj niestandardowej kwerendy SQL, aby odczytać dane. Może to być na przykład `"SELECT * FROM MyTable"`.<br>Po włączeniu obciążenia partycjonowanego, należy podłączyć wszelkie odpowiednie wbudowane parametry partycji w zapytaniu. Na przykład zobacz [kopiowanie równoległe z Oracle](#parallel-copy-from-oracle) sekcji. | Nie |
| partitionOptions (opcje partycji) | Określa opcje partycjonowania danych używane do ładowania danych z oracle. <br>Dozwolone wartości to: **Brak** (domyślnie), **PhysicalPartitionsOfTable** i **DynamicRange**.<br>Gdy opcja partycji jest włączona `None`(czyli nie), stopień równoległości równoczesnych ładowania danych z [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) bazy danych Oracle jest kontrolowany przez ustawienie działania kopiowania. | Nie |
| podziałY | Określ grupę ustawień partycjonowania danych. <br>Zastosuj, gdy opcja partycji nie `None`jest . | Nie |
| nazwy partycji | Lista partycji fizycznych, które muszą zostać skopiowane. <br>Zastosuj, gdy opcja `PhysicalPartitionsOfTable`partycji jest . Jeśli używasz kwerendy do pobierania danych `?AdfTabularPartitionName` źródłowych, należy podłączyć w klauzuli WHERE. Na przykład zobacz [kopię równoległą z Oracle](#parallel-copy-from-oracle) sekcji. | Nie |
| partitionColumnName | Określ nazwę kolumny źródłowej **w typie liczby całkowitej,** która będzie używana przez partycjonowanie zakresu dla kopiowania równoległego. Jeśli nie zostanie określony, klucz podstawowy tabeli jest wykrywany automatycznie i używany jako kolumna partycji. <br>Zastosuj, gdy opcja `DynamicRange`partycji jest . Jeśli używasz kwerendy do pobierania danych `?AdfRangePartitionColumnName` źródłowych, należy podłączyć w klauzuli WHERE. Na przykład zobacz [kopię równoległą z Oracle](#parallel-copy-from-oracle) sekcji. | Nie |
| partitionUpperBound | Maksymalna wartość kolumny partycji do skopiowania danych. <br>Zastosuj, gdy opcja `DynamicRange`partycji jest . Jeśli używasz kwerendy do pobierania danych `?AdfRangePartitionUpbound` źródłowych, należy podłączyć w klauzuli WHERE. Na przykład zobacz [kopię równoległą z Oracle](#parallel-copy-from-oracle) sekcji. | Nie |
| partycjaLowerBound | Minimalna wartość kolumny partycji do skopiowania danych. <br>Zastosuj, gdy opcja `DynamicRange`partycji jest . Jeśli używasz kwerendy do pobierania danych `?AdfRangePartitionLowbound` źródłowych, należy podłączyć w klauzuli WHERE. Na przykład zobacz [kopię równoległą z Oracle](#parallel-copy-from-oracle) sekcji. | Nie |

**Przykład: kopiowanie danych przy użyciu podstawowej kwerendy bez partycji**

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

### <a name="oracle-as-sink"></a>Oracle jako zlew

Aby skopiować dane do oracle, ustaw typ `OracleSink`ujścia w działaniu kopiowania na . Następujące właściwości są obsługiwane w sekcji ujście działania **kopiowania.**

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość typu ujścia działania kopiowania `OracleSink`musi być ustawiona na . | Tak |
| writeBatchSize | Wstawia dane do tabeli SQL, `writeBatchSize`gdy rozmiar buforu osiągnie .<br/>Dozwolone wartości to Liczba całkowita (liczba wierszy). |Nie (wartość domyślna to 10 000) |
| writeBatchTimeout | Czas oczekiwania na operację wstawiania partii, aby zakończyć przed jej limit czasu.<br/>Dozwolone wartości są Timespan. Przykładem jest 00:30:00 (30 minut). | Nie |
| preCopyScript | Określ kwerendę SQL dla działania kopiowania do uruchomienia przed zapisaniem danych w Oracle w każdym uruchomieniu. Ta właściwość służy do czyszczenia wstępnie załadowanych danych. | Nie |

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

## <a name="parallel-copy-from-oracle"></a>Kopia równoległa firmy Oracle

Łącznik Oracle fabryki danych zapewnia wbudowane partycjonowanie danych w celu równoległego kopiowania danych z oracle. Opcje partycjonowania danych można znaleźć na karcie **Źródło** działania kopiowania.

![Zrzut ekranu przedstawiający opcje partycji](./media/connector-oracle/connector-oracle-partition-options.png)

Po włączeniu kopii podzielonej na partycje usługa Data Factory uruchamia równoległe kwerendy względem źródła Oracle, aby załadować dane według partycji. Stopień równoległy jest [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) kontrolowany przez ustawienie działania kopiowania. Na przykład jeśli `parallelCopies` ustawisz na cztery, data factory jednocześnie generuje i uruchamia cztery kwerendy na podstawie określonej opcji partycji i ustawień, a każda kwerenda pobiera część danych z bazy danych Oracle.

Zaleca się włączenie kopiowania równoległego z partycjonowanie danych, zwłaszcza podczas ładowania dużej ilości danych z bazy danych Oracle. Poniżej przedstawiono sugerowane konfiguracje dla różnych scenariuszy. Podczas kopiowania danych do magazynu danych opartych na plikach zaleca się zapisywanie w folderze jako wielu plików (określanie tylko nazwy folderu), w którym to przypadku wydajność jest lepsza niż zapisywanie do jednego pliku.

| Scenariusz                                                     | Sugerowane ustawienia                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Pełne obciążenie z dużej tabeli, z partycjami fizycznymi.          | **Opcja partycji**: Partycje fizyczne tabeli. <br><br/>Podczas wykonywania usługa Data Factory automatycznie wykrywa partycje fizyczne i kopiuje dane według partycji. |
| Pełne obciążenie z dużej tabeli, bez partycji fizycznych, podczas gdy z kolumną całkowitą do partycjonowania danych. | **Opcje partycji**: Partycja zakresu dynamicznego.<br>**Kolumna partycji**: Określ kolumnę używaną do partycjonowania danych. Jeśli nie zostanie określona, używana jest kolumna klucza podstawowego. |
| Załaduj dużą ilość danych przy użyciu kwerendy niestandardowej z partycjami fizycznymi. | **Opcja partycji**: Partycje fizyczne tabeli.<br>**Zapytanie**: `SELECT * FROM <TABLENAME> PARTITION("?AdfTabularPartitionName") WHERE <your_additional_where_clause>`.<br>**Nazwa partycji**: Określ nazwy partycji, z na które mają być kopiowane dane. Jeśli nie zostanie określony, usługa Data Factory automatycznie wykrywa partycje fizyczne w tabeli określonej w zestawie danych Oracle.<br><br>Podczas wykonywania usługa Data `?AdfTabularPartitionName` Factory zastępuje rzeczywistą nazwę partycji i wysyła do oracle. |
| Załaduj dużą ilość danych przy użyciu kwerendy niestandardowej, bez partycji fizycznych, podczas gdy z kolumną całkowitą do partycjonowania danych. | **Opcje partycji**: Partycja zakresu dynamicznego.<br>**Zapytanie**: `SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>`.<br>**Kolumna partycji**: Określ kolumnę używaną do partycjonowania danych. Można podzielić na kolumnę z typem danych liczby całkowitej.<br>**Górna granica partycji** i **dolna granica partycji:** Określ, czy chcesz filtrować względem kolumny partycji, aby pobierać dane tylko między dolnym i górnym zakresem.<br><br>Podczas wykonywania usługa Data `?AdfRangePartitionColumnName` `?AdfRangePartitionUpbound`Factory `?AdfRangePartitionLowbound` zastępuje program , a rzeczywista nazwa kolumny i zakresy wartości dla każdej partycji i wysyła do oracle. <br>Na przykład jeśli kolumna partycji "ID" jest ustawiona z dolną granicą jako 1 i górną granicą jako 80, z kopią równoległą ustawioną jako 4, usługa Data Factory pobiera dane przez 4 partycje. Ich identyfikatory znajdują się odpowiednio między [1,20], [21, 40], [41, 60] i [61, 80]. |

**Przykład: kwerenda z partycją fizyczną**

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

**Przykład: kwerenda z partycją zakresu dynamicznego**

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

## <a name="data-type-mapping-for-oracle"></a>Mapowanie typów danych dla Oracle

Podczas kopiowania danych z i do Oracle mają zastosowanie następujące mapowania. Aby dowiedzieć się, jak działanie kopiowania mapuje schemat źródłowy i typ danych do ujścia, zobacz [Mapowanie schematu i typów danych](copy-activity-schema-and-type-mapping.md).

| Typ danych Oracle | Tymczasowy typ danych data factory |
|:--- |:--- |
| Bfile |Bajt[] |
| Blob |Bajt[]<br/>(obsługiwane tylko w oracle 10g lub nowszym) |
| Char |Ciąg |
| Clob |Ciąg |
| DATE |DateTime |
| Float |Dziesiętny, Ciąg (jeśli dokładność > 28) |
| LICZBA CAŁKOWITA |Dziesiętny, Ciąg (jeśli dokładność > 28) |
| Długi |Ciąg |
| DŁUGIE RAW |Bajt[] |
| Nchar |Ciąg |
| Nclob |Ciąg |
| Numer |Dziesiętny, Ciąg (jeśli dokładność > 28) |
| NVARCHAR2 |Ciąg |
| Raw |Bajt[] |
| Rowid |Ciąg |
| Sygnatury czasowej |DateTime |
| SYGNATURA CZASOWA Z LOKALNĄ STREFĄ CZASOWĄ |Ciąg |
| SYGNATURA CZASOWA ZE STREFĄ CZASOWĄ |Ciąg |
| NIEPODPISANA GNILIZNA |Liczba |
| VARCHAR2 (VARCHAR2) |Ciąg |
| XML |Ciąg |

> [!NOTE]
> Typy danych INTERVAL ROK DO MIESIĄCA i INTERWAŁ DZIEŃ NA DRUGI NIE są obsługiwane.

## <a name="lookup-activity-properties"></a>Właściwości działania odnośnika

Aby dowiedzieć się więcej o właściwościach, sprawdź [działanie odnośnika](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Następne kroki
Aby uzyskać listę magazynów danych obsługiwanych jako źródła i pochłaniacze przez działanie kopiowania w fabryce danych, zobacz [Obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
