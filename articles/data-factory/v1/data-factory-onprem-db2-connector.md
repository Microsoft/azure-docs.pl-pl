---
title: Przenoszenie danych z programu DB2 przy użyciu Azure Data Factory
description: Dowiedz się, jak przenieść dane z lokalnej bazy danych DB2 przy użyciu działania kopiowania Azure Data Factory
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: c1644e17-4560-46bb-bf3c-b923126671f1
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: e5d2c6b0460c3a7566adb17601aceb57e57f4d0b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "74931784"
---
# <a name="move-data-from-db2-by-using-azure-data-factory-copy-activity"></a>Przenoszenie danych z programu DB2 przy użyciu działania kopiowania Azure Data Factory
> [!div class="op_single_selector" title1="Wybierz używaną wersję usługi Data Factory:"]
> * [Wersja 1](data-factory-onprem-db2-connector.md)
> * [Wersja 2 (bieżąca wersja)](../connector-db2.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącej wersji usługi Data Factory, zobacz [łącznik programu DB2 w wersji 2](../connector-db2.md).


W tym artykule opisano, jak za pomocą działania kopiowania w Azure Data Factory skopiować dane z lokalnej bazy danych programu DB2 do magazynu danych. Dane można kopiować do dowolnego magazynu, który jest wymieniony jako obsługiwany, w artykule [Data Factory działania związane z przenoszeniem danych](data-factory-data-movement-activities.md#supported-data-stores-and-formats) . Ten temat jest oparty na Data Factory artykule, który przedstawia przegląd przenoszenia danych za pomocą działania kopiowania i zawiera listę obsługiwanych kombinacji magazynu danych. 

Data Factory obecnie obsługuje tylko przeniesienie danych z bazy danych DB2 do [obsługiwanego magazynu danych ujścia](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Przeniesienie danych z innych magazynów danych do bazy danych DB2 nie jest obsługiwane.

## <a name="prerequisites"></a>Wymagania wstępne
Data Factory obsługuje łączenie z lokalną bazą danych DB2 przy użyciu [bramy zarządzania danymi](data-factory-data-management-gateway.md). Instrukcje krok po kroku dotyczące konfigurowania potoku danych bramy do przenoszenia danych znajdują się w artykule [przenoszenie danych z lokalizacji lokalnej do chmury](data-factory-move-data-between-onprem-and-cloud.md) .

Brama jest wymagana, nawet jeśli DB2 jest hostowana na maszynie wirtualnej IaaS platformy Azure. Możesz zainstalować bramę na tej samej maszynie wirtualnej IaaS, w której znajduje się magazyn danych. Jeśli Brama może nawiązać połączenie z bazą danych, możesz zainstalować bramę na innej maszynie wirtualnej.

Brama zarządzania danymi zawiera wbudowany sterownik programu DB2, dlatego nie trzeba ręcznie instalować sterownika, aby skopiować dane z bazy danych DB2.

> [!NOTE]
> Porady dotyczące rozwiązywania problemów z połączeniem i bramą znajdują się w artykule [Rozwiązywanie problemów z bramą](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) .


## <a name="supported-versions"></a>Obsługiwane wersje
Łącznik Data Factory DB2 obsługuje następujące platformy i wersje programu IBM DB2 z dystrybuowaną architekturą relacyjnej bazy danych (DRDA), wersjami 9, 10 i 11:

* IBM DB2 dla systemu z/OS w wersji 11,1
* IBM DB2 dla systemu z/OS w wersji 10,1
* IBM DB2 for i (systemu AS400) w wersji 7,2
* IBM DB2 for i (systemu AS400) w wersji 7,1
* IBM DB2 dla systemów Linux, UNIX i Windows (LUW) w wersji 11
* IBM DB2 for LUW, wersja 10,5
* IBM DB2 for LUW, wersja 10,1

> [!TIP]
> Jeśli zostanie wyświetlony komunikat o błędzie "nie znaleziono pakietu odpowiadającego żądaniu wykonania instrukcji SQL. SQLSTATE = 51002 SQLCODE =-805 "powód jest niezbędny pakiet dla normalnego użytkownika w systemie operacyjnym. Aby rozwiązać ten problem, wykonaj następujące instrukcje dotyczące typu serwera bazy danych DB2:
> - DB2 for i (systemu AS400): Pozwól użytkownikowi zaawansowanemu utworzyć kolekcję dla normalnego użytkownika przed uruchomieniem działania kopiowania. Aby utworzyć kolekcję, użyj polecenia: `create collection <username>`
> - DB2 dla systemu z/OS lub LUW: Użyj konta o wysokim poziomie uprawnień — użytkownik zaawansowany lub administrator, który ma urzędy i powiązania pakietów, BINDADD, PRZYZNAj uprawnienia wykonywanie do publicznych uprawnień — aby uruchomić kopię raz. Wymagany pakiet jest automatycznie tworzony podczas kopiowania. Później można wrócić do normalnego użytkownika w przypadku kolejnych przebiegów kopiowania.

## <a name="getting-started"></a>Wprowadzenie
Można utworzyć potok z działaniem kopiowania, aby przenieść dane z lokalnego magazynu danych DB2 przy użyciu różnych narzędzi i interfejsów API: 

- Najprostszym sposobem tworzenia potoku jest użycie Kreatora kopiowania Azure Data Factory. Aby uzyskać szybki Przewodnik dotyczący tworzenia potoku przy użyciu Kreatora kopiowania, zobacz [Samouczek: Tworzenie potoku przy użyciu Kreatora kopiowania](data-factory-copy-data-wizard-tutorial.md). 
- Możesz również użyć narzędzi do tworzenia potoku, w tym programu Visual Studio, Azure PowerShell, szablonu Azure Resource Manager, interfejsu API platformy .NET i interfejsu API REST. Aby uzyskać instrukcje krok po kroku dotyczące tworzenia potoku za pomocą działania kopiowania, zobacz [Samouczek dotyczący działania kopiowania](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md). 

Niezależnie od tego, czy używasz narzędzi, czy interfejsów API, wykonaj następujące kroki, aby utworzyć potok służący do przenoszenia danych ze źródłowego magazynu danych do magazynu danych ujścia:

1. Utwórz połączone usługi, aby połączyć magazyny danych wejściowych i wyjściowych z fabryką danych.
2. Utwórz zestawy danych, aby reprezentować dane wejściowe i wyjściowe dla operacji kopiowania. 
3. Utwórz potok z działaniem kopiowania, które pobiera zestaw danych jako dane wejściowe i zestaw danych jako dane wyjściowe. 

W przypadku używania Kreatora kopiowania definicje JSON dla Data Factory połączonych usług, zestawów danych i jednostek potoku są tworzone automatycznie. Korzystając z narzędzi lub interfejsów API (z wyjątkiem interfejsu API platformy .NET), należy zdefiniować jednostki Data Factory przy użyciu formatu JSON. Przykład JSON: kopiowanie danych z programu DB2 do usługi Azure Blob Storage pokazuje definicje JSON dla Data Factory jednostek, które są używane do kopiowania danych z lokalnego magazynu danych programu DB2.

Poniższe sekcje zawierają szczegółowe informacje o właściwościach JSON, które są używane do definiowania jednostek Data Factory, które są specyficzne dla magazynu danych programu DB2.

## <a name="db2-linked-service-properties"></a>Właściwości połączonej usługi DB2
W poniższej tabeli wymieniono właściwości JSON, które są specyficzne dla połączonej usługi bazy danych DB2.

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| **Wprowadź** |Ta właściwość musi być ustawiona na **OnPremisesDb2**. |Tak |
| **Server** |Nazwa serwera bazy danych DB2. |Tak |
| **Database** |Nazwa bazy danych DB2. |Tak |
| **schematy** |Nazwa schematu w bazie danych programu DB2. Ta właściwość jest uwzględniana wielkość liter. |Nie |
| **authenticationType** |Typ uwierzytelniania używany do łączenia się z bazą danych programu DB2. Możliwe wartości to: Anonymous, Basic i Windows. |Tak |
| **uż** |Nazwa konta użytkownika w przypadku korzystania z uwierzytelniania podstawowego lub systemu Windows. |Nie |
| **hasło** |Hasło konta użytkownika. |Nie |
| **gatewayName** |Nazwa bramy, która ma być używana przez usługę Data Factory do łączenia się z lokalną bazą danych DB2. |Tak |

## <a name="dataset-properties"></a>Właściwości zestawu danych
Aby zapoznać się z listą sekcji i właściwości, które są dostępne do definiowania zestawów danych, zobacz artykuł [Tworzenie zestawów danych](data-factory-create-datasets.md) . Sekcje, takie jak **Struktura**, **dostępność**i **zasady** dla JSON zestawu danych, są podobne dla wszystkich typów zestawu danych (Azure SQL, Azure Blob Storage, Azure Table Storage itd.).

Sekcja **typeProperties** jest inna dla każdego typu zestawu danych i zawiera informacje dotyczące lokalizacji danych w magazynie danych. Sekcja **typeProperties** dla zestawu danych typu **relacyjnego**, który zawiera zestaw danych DB2, ma następującą właściwość:

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| **tableName** |Nazwa tabeli w wystąpieniu bazy danych DB2, do której odwołuje się połączona usługa. Ta właściwość jest uwzględniana wielkość liter. |Nie (Jeśli określono Właściwość **zapytania** działania Copy typu **RelationalSource** ) |

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania
Aby zapoznać się z listą sekcji i właściwości, które są dostępne do definiowania działań związanych z kopiowaniem, zobacz artykuł [Tworzenie potoków](data-factory-create-pipelines.md) . Właściwości działania kopiowania, takie jak **Nazwa**, **Opis**, tabela **danych wejściowych** , tabela **wyników** i **zasady**, są dostępne dla wszystkich typów działań. Właściwości, które są dostępne w sekcji **typeProperties** działania, różnią się w zależności od typu działania. W przypadku działania kopiowania właściwości różnią się w zależności od typów źródeł danych i ujścia.

W przypadku działania kopiowania, gdy źródłem jest typ **RelationalSource** (w tym DB2), w sekcji **typeProperties** dostępne są następujące właściwości:

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| **dotyczących** |Użyj zapytania niestandardowego, aby odczytać dane. |Ciąg zapytania SQL. Na przykład: `"query": "select * from "MySchema"."MyTable""` |Nie (Jeśli określono Właściwość **TableName** zestawu danych) |

> [!NOTE]
> W nazwach schematu i tabeli jest rozróżniana wielkość liter. W instrukcji kwerendy należy ująć nazwy właściwości przy użyciu "" (podwójne cudzysłowy).

## <a name="json-example-copy-data-from-db2-to-azure-blob-storage"></a>Przykład JSON: kopiowanie danych z programu DB2 do magazynu obiektów blob platformy Azure
Ten przykład zawiera przykładowe definicje JSON, których można użyć do utworzenia potoku przy użyciu programu [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)lub [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). W przykładzie pokazano, jak skopiować dane z bazy danych programu DB2 do magazynu obiektów BLOB. Dane można jednak kopiować do [dowolnego obsługiwanego typu ujścia magazynu danych](data-factory-data-movement-activities.md#supported-data-stores-and-formats) za pomocą działania kopiowania Azure Data Factory.

Przykład zawiera następujące jednostki Data Factory:

- Połączona usługa DB2 typu [OnPremisesDb2](data-factory-onprem-db2-connector.md)
- Połączona usługa magazynu obiektów blob platformy Azure typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)
- Wejściowy [zestaw danych](data-factory-create-datasets.md) typu [relacyjnego](data-factory-onprem-db2-connector.md#dataset-properties)
- Wyjściowy [zestaw danych](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)
- [Potok](data-factory-create-pipelines.md) z działaniem kopiowania, który używa właściwości [RelationalSource](data-factory-onprem-db2-connector.md#copy-activity-properties) i [wartość blobsink](data-factory-azure-blob-connector.md#copy-activity-properties)

Przykład kopiuje dane z wyniku zapytania w bazie danych programu DB2 do obiektu blob platformy Azure co godzinę. Właściwości JSON, które są używane w przykładzie, są opisane w sekcjach, które są zgodne z definicjami jednostek.

Pierwszym krokiem jest zainstalowanie i skonfigurowanie bramy Data Gateway. Instrukcje dotyczą [przemieszczania danych między lokalizacjami lokalnymi i artykułami w chmurze](data-factory-move-data-between-onprem-and-cloud.md) .

**Połączona usługa DB2**

```json
{
    "name": "OnPremDb2LinkedService",
    "properties": {
        "type": "OnPremisesDb2",
        "typeProperties": {
            "server": "<server>",
            "database": "<database>",
            "schema": "<schema>",
            "authenticationType": "<authentication type>",
            "username": "<username>",
            "password": "<password>",
            "gatewayName": "<gatewayName>"
        }
    }
}
```

**Połączona usługa Azure Blob Storage**

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorageLinkedService",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"
        }
    }
}
```

**Zestaw danych wejściowych DB2**

W przykładzie przyjęto założenie, że utworzono tabelę w bazie danych DB2 o nazwie "MyTable", która ma kolumnę z etykietą "timestamp" (dane szeregów czasowych).

Właściwość **zewnętrzna** jest ustawiona na wartość "true". To ustawienie informuje usługę Data Factory, że ten zestaw danych jest zewnętrzny względem fabryki danych i nie jest wytwarzany przez działanie w fabryce danych. Zauważ, że właściwość **Type** jest ustawiona na wartość **relacyjny**.


```json
{
    "name": "Db2DataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremDb2LinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

**Wyjściowy zestaw danych obiektów blob platformy Azure**

Dane są zapisywane w nowym obiekcie blob co godzinę przez ustawienie właściwości **częstotliwość** na wartość "godzina" i Właściwość **Interwał** na 1. Właściwość **folderPath** obiektu BLOB jest obliczana dynamicznie na podstawie czasu rozpoczęcia przetwarzanego wycinka. Ścieżka folderu używa części roku, miesiąca, dnia i godziny rozpoczęcia.

```json
{
    "name": "AzureBlobDb2DataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/db2/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            },
            "partitionedBy": [
                {
                    "name": "Year",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "yyyy"
                    }
                },
                {
                    "name": "Month",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "MM"
                    }
                },
                {
                    "name": "Day",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "dd"
                    }
                },
                {
                    "name": "Hour",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "HH"
                    }
                }
            ]
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**Potok dla działania kopiowania**

Potok zawiera działanie kopiowania, które jest skonfigurowane do korzystania z określonych zestawów danych wejściowych i wyjściowych oraz zaplanowane do uruchomienia co godzinę. W definicji JSON dla potoku, typ **źródła** ma wartość **RelationalSource** , a typ **ujścia** to **wartość blobsink**. Zapytanie SQL określone dla właściwości **zapytania** wybiera dane z tabeli "Orders" (zamówienia).

```json
{
    "name": "CopyDb2ToBlob",
    "properties": {
        "description": "pipeline for the copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "RelationalSource",
                        "query": "select * from \"Orders\""
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                },
                "inputs": [
                    {
                        "name": "Db2DataSet"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobDb2DataSet"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "Db2ToBlob"
            }
        ],
        "start": "2014-06-01T18:00:00Z",
        "end": "2014-06-01T19:00:00Z"
    }
}
```

## <a name="type-mapping-for-db2"></a>Mapowanie typu dla bazy danych DB2
Jak wspomniano w artykule [działania związane z przenoszeniem danych](data-factory-data-movement-activities.md) , działanie kopiowania wykonuje konwersje typu automatycznego z typu źródłowego na typ ujścia przy użyciu następującego podejścia dwuetapowego:

1. Konwertuj z natywnego typu źródła na typ .NET
2. Konwertuj z typu .NET na natywny typ ujścia

Następujące mapowania są używane, gdy działanie kopiowania konwertuje dane z typu DB2 na typ .NET:

| Typ bazy danych DB2 | Typ programu .NET Framework |
| --- | --- |
| SmallInt |Int16 |
| Liczba całkowita |Int32 |
| BigInt |Int64 |
| Rzeczywiste |Pojedynczy |
| Double |Double |
| Liczba zmiennoprzecinkowa |Double |
| Liczba dziesiętna |Liczba dziesiętna |
| DecimalFloat |Liczba dziesiętna |
| Numeryczne |Liczba dziesiętna |
| Data |Data i godzina |
| Godzina |przedział_czasu |
| Timestamp |DateTime |
| Xml |Byte [] |
| Char |Ciąg |
| VarChar |Ciąg |
| LongVarChar |Ciąg |
| DB2DynArray |Ciąg |
| Binarne |Byte [] |
| Liczby |Byte [] |
| LongVarBinary |Byte [] |
| Zdjęć |Ciąg |
| VarGraphic |Ciąg |
| LongVarGraphic |Ciąg |
| Obiektów CLOB |Ciąg |
| Obiekt blob |Byte [] |
| DbClob |Ciąg |
| SmallInt |Int16 |
| Liczba całkowita |Int32 |
| BigInt |Int64 |
| Rzeczywiste |Pojedynczy |
| Double |Double |
| Liczba zmiennoprzecinkowa |Double |
| Liczba dziesiętna |Liczba dziesiętna |
| DecimalFloat |Liczba dziesiętna |
| Numeryczne |Liczba dziesiętna |
| Data |Data i godzina |
| Godzina |przedział_czasu |
| Timestamp |DateTime |
| Xml |Byte [] |
| Char |Ciąg |

## <a name="map-source-to-sink-columns"></a>Mapowanie źródła do kolumn ujścia
Aby dowiedzieć się, jak mapować kolumny w źródłowym zestawie danych na kolumny w zestawie danych ujścia, zobacz [Mapowanie kolumn zestawu danych w Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-reads-from-relational-sources"></a>Powtarzalne odczyty ze źródeł relacyjnych
Podczas kopiowania danych z magazynu danych relacyjnych należy pamiętać o powtarzaniu, aby uniknąć niezamierzonych wyników. W Azure Data Factory można ręcznie uruchomić ponownie wycinka. Możesz również skonfigurować właściwość **zasady** ponawiania dla zestawu danych, aby ponownie uruchomić wycinek w przypadku wystąpienia błędu. Upewnij się, że te same dane są odczytywane niezależnie od tego, ile razy zostanie uruchomiony wycink, bez względu na sposób ponownego uruchomienia wycinka. Aby uzyskać więcej informacji, zobacz [powtarzalne odczyty ze źródeł relacyjnych](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Wydajności i dostosowywanie
Poznaj kluczowe czynniki wpływające na wydajność działania kopiowania i sposoby optymalizacji wydajności w przewodniku dotyczącym [wydajności i dostrajania działania kopiowania](data-factory-copy-activity-performance.md).
