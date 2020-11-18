---
title: Kopiowanie danych z usługi Hive przy użyciu Azure Data Factory
description: Informacje o kopiowaniu danych z programu Hive do obsługiwanych magazynów danych ujścia przy użyciu działania kopiowania w potoku Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 11/17/2020
ms.author: jingwang
ms.openlocfilehash: 4207c4ddfcbab325b1ae119dcd200af30fc59f58
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94844954"
---
# <a name="copy-and-transform-data-from-hive-using-azure-data-factory"></a>Kopiowanie i Przekształcanie danych z programu Hive przy użyciu Azure Data Factory 
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule opisano sposób używania działania kopiowania w Azure Data Factory do kopiowania danych z programu Hive. Jest ona oparta na [przeglądzie działania kopiowania](copy-activity-overview.md) , która przedstawia ogólne omówienie działania kopiowania.

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Ten łącznik programu Hive jest obsługiwany dla następujących działań:

- [Działanie kopiowania](copy-activity-overview.md) z [obsługiwaną macierzą źródłową/ujścia](copy-activity-overview.md)
- [Działanie Lookup](control-flow-lookup-activity.md)

Dane z usługi Hive można kopiować do dowolnego obsługiwanego magazynu danych ujścia. Listę magazynów danych obsługiwanych jako źródła/ujścia przez działanie kopiowania można znaleźć w tabeli [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) .

Azure Data Factory udostępnia wbudowany sterownik umożliwiający połączenie, dlatego nie trzeba ręcznie instalować żadnego sterownika przy użyciu tego łącznika.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="getting-started"></a>Wprowadzenie

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje dotyczące właściwości, które są używane do definiowania jednostek Data Factory specyficznych dla łącznika programu Hive.

## <a name="linked-service-properties"></a>Właściwości połączonej usługi

Następujące właściwości są obsługiwane dla połączonej usługi Hive:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość Type musi być ustawiona na wartość: **Hive** | Tak |
| host | Adres IP lub nazwa hosta serwera Hive, oddzielona znakiem ";" dla wielu hostów (tylko po włączeniu funkcji ServiceDiscoveryMode).  | Tak |
| port | Port TCP, którego serwer Hive używa do nasłuchiwania połączeń klientów. W przypadku nawiązywania połączenia z usługą Azure HDInsight określ port jako 443. | Tak |
| serverType | Typ serwera Hive. <br/>Dozwolone wartości to: **HiveServer1**, **serwera hiveserver2**, **HiveThriftServer** | Nie |
| thriftTransportProtocol | Protokół transportowy do użycia w warstwie Thrift. <br/>Dozwolone wartości to: **Binary**, **SASL**, **http** | Nie |
| authenticationType | Metoda uwierzytelniania używana do uzyskiwania dostępu do serwera Hive. <br/>Dozwolone wartości to: **Anonymous**, **username**, **UsernameAndPassword**, **WindowsAzureHDInsightService**. Uwierzytelnianie Kerberos nie jest teraz obsługiwane. | Tak |
| ServiceDiscoveryMode | wartość true oznacza użycie usługi dozorcy.  | Nie |
| zooKeeperNameSpace | Przestrzeń nazw na dozorcy, w której dodawane są węzły serwera Hive 2.  | Nie |
| useNativeQuery | Określa, czy sterownik używa natywnych zapytań HiveQL, czy konwertuje je do równoważnej formy w HiveQL.  | Nie |
| nazwa użytkownika | Nazwa użytkownika służąca do uzyskiwania dostępu do serwera Hive.  | Nie |
| hasło | Hasło odpowiednie dla użytkownika. Oznacz to pole jako element SecureString, aby bezpiecznie przechowywać go w Data Factory, lub [odwoływać się do wpisu tajnego przechowywanego w Azure Key Vault](store-credentials-in-key-vault.md). | Nie |
| httpPath | Częściowy adres URL odpowiadający serwerowi Hive.  | Nie |
| enableSsl | Określa, czy połączenia z serwerem są szyfrowane przy użyciu protokołu TLS. Wartość domyślna to false.  | Nie |
| trustedCertPath | Pełna ścieżka pliku PEM zawierającego certyfikaty zaufanych urzędów certyfikacji w celu zweryfikowania serwera podczas łączenia za pośrednictwem protokołu TLS. Tę właściwość można ustawić tylko w przypadku korzystania z protokołu TLS na samoobsługowym środowisku IR. Wartość domyślna to plik cacerts. pem instalowany z programem IR.  | Nie |
| useSystemTrustStore | Określa, czy certyfikat urzędu certyfikacji ma być używany z magazynu zaufania systemu czy z określonego pliku PEM. Wartość domyślna to false.  | Nie |
| allowHostNameCNMismatch | Określa, czy ma być wymagana nazwa certyfikatu TLS/SSL wystawionego przez urząd certyfikacji w celu dopasowania do nazwy hosta serwera podczas łączenia za pośrednictwem protokołu TLS. Wartość domyślna to false.  | Nie |
| allowSelfSignedServerCert | Określa, czy zezwalać na certyfikaty z podpisem własnym z serwera. Wartość domyślna to false.  | Nie |
| Właściwością connectvia | [Integration Runtime](concepts-integration-runtime.md) używany do nawiązywania połączenia z magazynem danych. Dowiedz się więcej z sekcji [wymagania wstępne](#prerequisites) . Jeśli nie zostanie określony, zostanie użyta domyślna Azure Integration Runtime. |Nie |
| storageReference | Odwołanie do połączonej usługi konta magazynu używanego dla danych przemieszczania w mapowaniu przepływu danych. Jest to wymagane tylko w przypadku używania połączonej usługi Hive w przepływie danych mapowania | Nie |

**Przykład:**

```json
{
    "name": "HiveLinkedService",
    "properties": {
        "type": "Hive",
        "typeProperties": {
            "host" : "<cluster>.azurehdinsight.net",
            "port" : "<port>",
            "authenticationType" : "WindowsAzureHDInsightService",
            "username" : "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Właściwości zestawu danych

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania zestawów danych, zobacz artykuł [zestawy danych](concepts-datasets-linked-services.md) . Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych programu Hive.

Aby skopiować dane z Hive, ustaw właściwość Type zestawu danych na **hiveobject**. Obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość Type zestawu danych musi być ustawiona na wartość: **hiveobject** | Tak |
| schema | Nazwa schematu. |Nie (Jeśli określono "zapytanie" w źródle aktywności)  |
| table (stolik) | Nazwa tabeli. |Nie (Jeśli określono "zapytanie" w źródle aktywności)  |
| tableName | Nazwa tabeli, w tym część schematu. Ta właściwość jest obsługiwana w celu zapewnienia zgodności z poprzednimi wersjami. W przypadku nowych obciążeń Użyj `schema` i `table` . | Nie (Jeśli określono "zapytanie" w źródle aktywności) |

**Przykład**

```json
{
    "name": "HiveDataset",
    "properties": {
        "type": "HiveObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Hive linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania działań, zobacz artykuł [potoki](concepts-pipelines-activities.md) . Ta sekcja zawiera listę właściwości obsługiwanych przez źródło Hive.

### <a name="hivesource-as-source"></a>HiveSource jako źródło

Aby skopiować dane z programu Hive, ustaw typ źródła w działaniu Copy na **HiveSource**. W sekcji **Źródło** działania kopiowania są obsługiwane następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość Type źródła działania Copy musi być ustawiona na wartość: **HiveSource** | Tak |
| query | Użyj niestandardowego zapytania SQL, aby odczytać dane. Na przykład: `"SELECT * FROM MyTable"`. | Nie (Jeśli określono "TableName" w zestawie danych) |

**Przykład:**

```json
"activities":[
    {
        "name": "CopyFromHive",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Hive input dataset name>",
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
                "type": "HiveSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="mapping-data-flow-properties"></a>Mapowanie właściwości przepływu danych

Łącznik programu Hive jest obsługiwany jako [wbudowane źródło DataSet](data-flow-source.md#inline-datasets) w mapowaniu przepływów danych. Odczytywanie przy użyciu zapytania lub bezpośrednio z tabeli programu Hive w usłudze HDInsight. Dane programu Hive są przygotowywane na koncie magazynu jako pliki Parquet przed przeprowadzeniem transformacji w ramach przepływu danych. 

### <a name="source-properties"></a>Właściwości źródła

Poniższa tabela zawiera listę właściwości obsługiwanych przez źródło Hive. Można edytować te właściwości na karcie **Opcje źródła** .

| Nazwa | Opis | Wymagane | Dozwolone wartości | Właściwość skryptu przepływu danych |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Przechowywanie | Magazyn musi być `hive` | yes |  `hive` | store | 
| Format | Bez względu na to, czy czytasz z tabeli czy zapytania | yes | `table` lub `query` | format |
| Nazwa schematu | W przypadku odczytywania danych z tabeli schemat tabeli źródłowej |  tak, jeśli format jest `table` | String | schemaName |
| Nazwa tabeli | W przypadku odczytywania z tabeli Nazwa tabeli |   tak, jeśli format jest `table` | String | tableName |
| Zapytanie | Jeśli format to `query` , zapytanie źródłowe w połączonej usłudze Hive | tak, jeśli format jest `query` | String | query |
| Przygotowane | Tabela programu Hive zostanie zawsze przemieszczona. | yes | `true` | przygotowane |
| Kontener magazynu | Kontener magazynu używany do przygotowywania danych przed przeczytaniem z programu Hive lub zapisem w usłudze Hive. Klaster programu Hive musi mieć dostęp do tego kontenera. | yes | String | storageContainer |
| Tymczasowa baza danych | Schemat/baza danych, w której konto użytkownika określone w połączonej usłudze ma dostęp do usługi. Służy do tworzenia tabel zewnętrznych podczas przemieszczania i porzucenia później | nie | `true` lub `false` | stagingDatabaseName |
| Wstępnie zdefiniowane skrypty SQL | Kod SQL do uruchomienia w tabeli Hive przed odczytaniem danych | nie | String | preSQLs |

#### <a name="source-example"></a>Przykład źródła

Poniżej znajduje się przykład konfiguracji źródła Hive:

![Przykład źródła Hive](media/data-flow/hive-source.png "[Przykład źródła Hive")

Te ustawienia przekładają się na następujący skrypt przepływu danych:

```
source(
    allowSchemaDrift: true,
    validateSchema: false,
    ignoreNoFilesFound: false,
    format: 'table',
    store: 'hive',
    schemaName: 'default',
    tableName: 'hivesampletable',
    staged: true,
    storageContainer: 'khive',
    storageFolderPath: '',
    stagingDatabaseName: 'default') ~> hivesource
```
### <a name="known-limitations"></a>Znane ograniczenia

* Typy złożone, takie jak tablice, mapy, struktury i unie, nie są obsługiwane w celu odczytu. 
* Łącznik programu Hive obsługuje tylko tabele Hive w usłudze Azure HDInsight w wersji 4,0 lub nowszej (Apache Hive 3.1.0)

## <a name="lookup-activity-properties"></a>Właściwości działania Lookup

Aby dowiedzieć się więcej o właściwościach, sprawdź [działanie Lookup (wyszukiwanie](control-flow-lookup-activity.md)).


## <a name="next-steps"></a>Następne kroki
Listę magazynów danych obsługiwanych jako źródła i ujścia przez działanie kopiowania w Azure Data Factory można znaleźć w temacie [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
