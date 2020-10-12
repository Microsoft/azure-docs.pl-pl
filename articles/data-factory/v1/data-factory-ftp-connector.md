---
title: Przenoszenie danych z serwera FTP przy użyciu Azure Data Factory
description: Dowiedz się więcej na temat przenoszenia danych z serwera FTP przy użyciu Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: eea3bab0-a6e4-4045-ad44-9ce06229c718
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 05/02/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: eeeb122d240d8c3eae4ebe1650f67cf0e4b9dac6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "80992049"
---
# <a name="move-data-from-an-ftp-server-by-using-azure-data-factory"></a>Przenoszenie danych z serwera FTP przy użyciu Azure Data Factory
> [!div class="op_single_selector" title1="Wybierz używaną wersję usługi Data Factory:"]
> * [Wersja 1](data-factory-ftp-connector.md)
> * [Wersja 2 (bieżąca wersja)](../connector-ftp.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącej wersji usługi Data Factory, zobacz [Łącznik FTP w wersji 2](../connector-ftp.md).

W tym artykule wyjaśniono, jak za pomocą działania kopiowania w Azure Data Factory przenieść dane z serwera FTP. Jest on używany w artykule dotyczącym [przenoszenia danych](data-factory-data-movement-activities.md) , który przedstawia ogólne omówienie przenoszenia danych za pomocą działania kopiowania.

Dane z serwera FTP można kopiować do dowolnego obsługiwanego magazynu danych ujścia. Listę magazynów danych obsługiwanych jako ujścia przez działanie kopiowania można znaleźć w tabeli [obsługiwane magazyny danych](data-factory-data-movement-activities.md#supported-data-stores-and-formats) . Data Factory obecnie obsługuje tylko przeniesienie danych z serwera FTP do innych magazynów danych, ale nie przenosi danych z innych magazynów danych na serwer FTP. Obsługuje zarówno serwery FTP lokalne, jak i w chmurze.

> [!NOTE]
> Działanie kopiowania nie usuwa pliku źródłowego po pomyślnym skopiowaniu do miejsca docelowego. Jeśli musisz usunąć plik źródłowy po pomyślnej kopii, Utwórz niestandardowe działanie, aby usunąć plik, i Użyj działania w potoku.

## <a name="enable-connectivity"></a>Włączanie łączności
Jeśli przenosisz dane z lokalnego serwera FTP do magazynu danych **w** chmurze (na przykład do usługi Azure Blob Storage), zainstaluj i użyj bramy zarządzanie danymi. Brama Zarządzanie danymi jest agentem klienta, który jest zainstalowany na maszynie lokalnej i umożliwia usługom w chmurze łączenie się z zasobem lokalnym. Aby uzyskać szczegółowe informacje, zobacz [Zarządzanie danymi Gateway](data-factory-data-management-gateway.md). Aby uzyskać instrukcje krok po kroku dotyczące konfigurowania bramy i korzystania z niej, zobacz temat [przeniesienie danych między lokalizacjami lokalnymi i chmurą](data-factory-move-data-between-onprem-and-cloud.md). Za pomocą bramy można nawiązać połączenie z serwerem FTP, nawet jeśli serwer znajduje się na maszynie wirtualnej usługi Azure Infrastructure as (IaaS).

Można zainstalować bramę na maszynie lokalnej lub IaaS maszyny wirtualnej jako serwer FTP. Zaleca się jednak zainstalowanie bramy na oddzielnym komputerze lub maszynie wirtualnej IaaS, aby uniknąć rywalizacji o zasoby i zwiększyć wydajność. Po zainstalowaniu bramy na oddzielnym komputerze komputer powinien mieć możliwość uzyskania dostępu do serwera FTP.

## <a name="get-started"></a>Rozpoczęcie pracy
Można utworzyć potok za pomocą działania kopiowania, które przenosi dane ze źródła FTP przy użyciu różnych narzędzi lub interfejsów API.

Najprostszym sposobem tworzenia potoku jest użycie **Kreatora kopiowania Data Factory**. Zobacz [Samouczek: Tworzenie potoku za pomocą Kreatora kopiowania](data-factory-copy-data-wizard-tutorial.md) na potrzeby szybkiego instruktażu.

Do utworzenia potoku można także użyć następujących narzędzi: **Visual Studio**, **PowerShell**, **Azure Resource Manager Template**, **.NET API**i **REST API**. Aby uzyskać instrukcje krok po kroku dotyczące tworzenia potoku za pomocą działania kopiowania, zobacz [Samouczek dotyczący działania kopiowania](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) .

Niezależnie od tego, czy używasz narzędzi, czy interfejsów API, wykonaj następujące kroki, aby utworzyć potok służący do przenoszenia danych ze źródłowego magazynu danych do magazynu danych ujścia:

1. Utwórz **połączone usługi** , aby połączyć magazyny danych wejściowych i wyjściowych z fabryką danych.
2. Utwórz **zestawy** danych, aby reprezentować dane wejściowe i wyjściowe dla operacji kopiowania.
3. Utwórz **potok** z działaniem kopiowania, które pobiera zestaw danych jako dane wejściowe i zestaw danych jako dane wyjściowe.

Gdy używasz Kreatora, definicje JSON dla tych Data Factory jednostek (połączone usługi, zestawy danych i potok) są automatycznie tworzone. Korzystając z narzędzi lub interfejsów API (z wyjątkiem interfejsu API .NET), należy zdefiniować te Data Factory jednostki przy użyciu formatu JSON. Aby uzyskać przykład z definicjami JSON dla Data Factory jednostek, które są używane do kopiowania danych z magazynu danych FTP, zobacz [przykład JSON: Kopiuj dane z serwera FTP do usługi Azure Blob](#json-example-copy-data-from-ftp-server-to-azure-blob) w tym artykule.

> [!NOTE]
> Aby uzyskać szczegółowe informacje na temat obsługiwanych formatów plików i kompresji, zobacz [formaty plików i kompresji w Azure Data Factory](data-factory-supported-file-and-compression-formats.md).

Poniższe sekcje zawierają szczegółowe informacje na temat właściwości JSON, które są używane do definiowania jednostek Data Factory specyficznych dla protokołu FTP.

## <a name="linked-service-properties"></a>Właściwości połączonej usługi
W poniższej tabeli opisano elementy JSON specyficzne dla połączonej usługi FTP.

| Właściwość | Opis | Wymagane | Domyślne |
| --- | --- | --- | --- |
| typ |Ustaw tę wartość na FtpServer. |Tak |&nbsp; |
| host |Określ nazwę lub adres IP serwera FTP. |Tak |&nbsp; |
| authenticationType |Określ typ uwierzytelniania. |Tak |Podstawowa, anonimowa |
| nazwa użytkownika |Określ użytkownika, który ma dostęp do serwera FTP. |Nie |&nbsp; |
| hasło |Określ hasło użytkownika (username). |Nie |&nbsp; |
| encryptedCredential |Określ zaszyfrowane poświadczenia, aby uzyskać dostęp do serwera FTP. |Nie |&nbsp; |
| gatewayName |Określ nazwę bramy w Zarządzanie danymi Gateway, aby nawiązać połączenie z lokalnym serwerem FTP. |Nie |&nbsp; |
| port |Określ port, na którym nasłuchuje serwer FTP. |Nie |21 |
| enableSsl |Określ, czy używać FTP za pośrednictwem kanału SSL/TLS. |Nie |true |
| enableServerCertificateValidation |Określ, czy włączyć weryfikację certyfikatu protokołu TLS/SSL serwera podczas korzystania z protokołu FTP za pośrednictwem protokołu SSL/TLS. |Nie |true |

>[!NOTE]
>Łącznik FTP obsługuje dostęp do serwera FTP bez szyfrowania lub jawnego szyfrowania SSL/TLS; nie obsługuje niejawnego szyfrowania SSL/TLS.

### <a name="use-anonymous-authentication"></a>Korzystanie z uwierzytelniania anonimowego

```JSON
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "authenticationType": "Anonymous",
              "host": "myftpserver.com"
        }
    }
}
```

### <a name="use-username-and-password-in-plain-text-for-basic-authentication"></a>Użyj nazwy użytkownika i hasła w postaci zwykłego tekstu na potrzeby uwierzytelniania podstawowego

```JSON
{
    "name": "FTPLinkedService",
    "properties": {
    "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",
            "username": "Admin",
            "password": "123456"
        }
    }
}
```

### <a name="use-port-enablessl-enableservercertificatevalidation"></a>Użyj portu, enableSsl, enableServerCertificateValidation

```JSON
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",
            "username": "Admin",
            "password": "123456",
            "port": "21",
            "enableSsl": true,
            "enableServerCertificateValidation": true
        }
    }
}
```

### <a name="use-encryptedcredential-for-authentication-and-gateway"></a>Korzystanie z encryptedCredential na potrzeby uwierzytelniania i bramy

```JSON
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",
            "encryptedCredential": "xxxxxxxxxxxxxxxxx",
            "gatewayName": "mygateway"
        }
    }
}
```

## <a name="dataset-properties"></a>Właściwości zestawu danych
Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania zestawów danych, zobacz [Tworzenie zestawów danych](data-factory-create-datasets.md). Sekcje, takie jak struktura, dostępność i zasady JSON zestawu danych, są podobne dla wszystkich typów zestawów danych.

Sekcja **typeProperties** jest inna dla każdego typu zestawu danych. Zawiera informacje, które są specyficzne dla typu zestawu danych. Sekcja **typeProperties** dla zestawu danych typu **udziału** ma następujące właściwości:

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| folderPath |Ścieżka podrzędna do folderu. Użyj znaku ucieczki "\" dla znaków specjalnych w ciągu. Przykłady można znaleźć w temacie przykładowe połączone usługi i zestawy danych.<br/><br/>Możesz połączyć tę właściwość z **partitionBy** , aby mieć ścieżki folderu na podstawie daty rozpoczęcia i zakończenia wycinka. |Tak |
| fileName |Określ nazwę pliku w **folderPath** , jeśli chcesz, aby tabela odnosiła się do określonego pliku w folderze. Jeśli nie określisz żadnej wartości dla tej właściwości, tabela wskazuje wszystkie pliki w folderze.<br/><br/>Gdy nie określono **nazwy** pliku wyjściowego zestawu danych, nazwa wygenerowanego pliku jest w następującym formacie: <br/><br/>`Data.<Guid>.txt` (Przykład: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt) |Nie |
| fileFilter |Określ filtr, który ma być używany do wybierania podzbioru plików w **folderPath**, a nie wszystkich plików.<br/><br/>Dozwolone wartości to: `*` (wiele znaków) i `?` (pojedynczy znak).<br/><br/>Przykład 1: `"fileFilter": "*.log"`<br/>Przykład 2: `"fileFilter": 2014-1-?.txt"`<br/><br/> **FileFilter** ma zastosowanie do wejściowego zestawu danych. Ta właściwość nie jest obsługiwana w rozproszony system plików Hadoop (HDFS). |Nie |
| partitionedBy |Służy do określania dynamicznej **folderPath** i **nazwy pliku** dla danych szeregów czasowych. Na przykład można określić **folderPath** , który jest sparametryzowane dla każdej godziny danych. |Nie |
| format | Obsługiwane są następujące typy formatów: **TextFormat**, **formatu jsonformat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Ustaw właściwość **Type** w polu Format na jedną z tych wartości. Aby uzyskać więcej informacji, zobacz sekcję [Format tekstu](data-factory-supported-file-and-compression-formats.md#text-format), [Format JSON](data-factory-supported-file-and-compression-formats.md#json-format), [Format Avro](data-factory-supported-file-and-compression-formats.md#avro-format), [Format Orc](data-factory-supported-file-and-compression-formats.md#orc-format)i [Parquet format](data-factory-supported-file-and-compression-formats.md#parquet-format) . <br><br> Jeśli chcesz skopiować pliki między magazynami opartymi na plikach (kopia binarna), Pomiń sekcję format w definicjach zestawu danych wejściowych i wyjściowych. |Nie |
| kompresja | Określ typ i poziom kompresji danych. Obsługiwane typy to **gzip**, **Wklęśnięcie**, **BZip2**i **ZipDeflate**, a obsługiwane poziomy są **optymalne** i **najszybszy**. Aby uzyskać więcej informacji, zobacz [formaty plików i kompresji w Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Nie |
| useBinaryTransfer |Określ, czy używać trybu transferu binarnego. Wartości mają wartość true w przypadku trybu binarnego (jest to wartość domyślna), a wartość false dla kodu ASCII. Tej właściwości można użyć tylko wtedy, gdy skojarzony typ połączonej usługi jest typu: FtpServer. |Nie |

> [!NOTE]
> **nazwy pliku** i **FileFilter** nie można jednocześnie używać.

### <a name="use-the-partionedby-property"></a>Użyj właściwości partionedBy
Jak wspomniano w poprzedniej sekcji, można określić dynamiczne **folderPath** i **nazwę pliku** dla danych szeregów czasowych z właściwością **partitionedBy** .

Aby dowiedzieć się więcej o zestawach danych, planowaniu i wycinkach szeregów czasowych, zobacz [Tworzenie zestawów danych](data-factory-create-datasets.md), [Planowanie i wykonywanie](data-factory-scheduling-and-execution.md)oraz [Tworzenie potoków](data-factory-create-pipelines.md).

#### <a name="sample-1"></a>Przykład 1

```json
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```
W tym przykładzie {Slice} jest zastępowana wartością Data Factory zmiennej systemowej parametru slicestart, w formacie określonym (YYYYMMDDHH). Parametru slicestart odnosi się do czasu rozpoczęcia wycinka. Ścieżka folderu jest inna dla każdego wycinka. (Na przykład wikidatagateway/wikisampledataout/2014100103 lub wikidatagateway/wikisampledataout/2014100104).

#### <a name="sample-2"></a>Przykład 2

```json
"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
"fileName": "{Hour}.csv",
"partitionedBy":
[
    { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
    { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
    { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
    { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } }
],
```
W tym przykładzie rok, miesiąc, dzień i czas parametru slicestart są wyodrębniane do oddzielnych zmiennych, które są używane przez właściwości **folderPath** i **filename** .

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania
Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania działań, zobacz [Tworzenie potoków](data-factory-create-pipelines.md). Właściwości, takie jak nazwa, opis, tabele wejściowe i wyjściowe, oraz zasady są dostępne dla wszystkich typów działań.

Właściwości dostępne w sekcji **typeProperties** działania, z drugiej strony, różnią się w zależności od typu działania. W przypadku działania kopiowania właściwości typu różnią się w zależności od typów źródeł i ujścia.

W działaniu kopiowania, gdy źródłem jest typ **FileSystemSource**, następująca właściwość jest dostępna w sekcji **typeProperties** :

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| rozpoznawania |Wskazuje, czy dane są odczytane cyklicznie z podfolderów, czy tylko z określonego folderu. |Prawda, FAŁSZ (wartość domyślna) |Nie |

## <a name="json-example-copy-data-from-ftp-server-to-azure-blob"></a>Przykład JSON: kopiowanie danych z serwera FTP do obiektu blob platformy Azure
Ten przykład pokazuje, jak skopiować dane z serwera FTP do usługi Azure Blob Storage. Dane można jednak skopiować bezpośrednio do dowolnego ujścia określonego w [obsługiwanych magazynach i formatach danych](data-factory-data-movement-activities.md#supported-data-stores-and-formats)za pomocą działania kopiowania w Data Factory.

W poniższych przykładach przedstawiono przykładowe definicje JSON, których można użyć do utworzenia potoku przy użyciu [programu Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)lub [programu PowerShell](data-factory-copy-activity-tutorial-using-powershell.md):

* Połączona usługa typu [ftpserver](#linked-service-properties)
* Połączona usługa typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)
* Wejściowy [zestaw danych](data-factory-create-datasets.md) typu [FileShare](#dataset-properties)
* Wyjściowy [zestaw danych](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)
* [Potok](data-factory-create-pipelines.md) z działaniem kopiowania korzystającym z [FileSystemSource](#copy-activity-properties) i [wartość blobsink](data-factory-azure-blob-connector.md#copy-activity-properties)

Przykładowo kopiuje dane z serwera FTP do obiektu blob platformy Azure co godzinę. Właściwości JSON używane w tych przykładach są opisane w sekcjach poniżej przykładów.

### <a name="ftp-linked-service"></a>Połączona usługa FTP

W tym przykładzie jest stosowane uwierzytelnianie podstawowe z nazwą użytkownika i hasłem w postaci zwykłego tekstu. Można również użyć jednej z następujących metod:

* Uwierzytelnianie anonimowe
* Uwierzytelnianie podstawowe z szyfrowanymi poświadczeniami
* FTP za pośrednictwem protokołu SSL/TLS (FTPS)

Zapoznaj się z sekcją [połączonej usługi FTP](#linked-service-properties) , aby poznać różne typy uwierzytelniania, których można użyć.

```JSON
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",
            "username": "Admin",
            "password": "123456"
        }
    }
}
```
### <a name="azure-storage-linked-service"></a>Połączona usługa Azure Storage

```JSON
{
  "name": "AzureStorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
### <a name="ftp-input-dataset"></a>Wejściowy zestaw danych FTP

Ten zestaw danych odwołuje się do folderu `mysharedfolder` i pliku FTP `test.csv` . Potok kopiuje plik do miejsca docelowego.

Ustawienie od **zewnątrz** do **true** informuje usługę Data Factory, że zestaw danych znajduje się poza fabryką danych i nie jest wytwarzany przez działanie w fabryce danych.

```JSON
{
  "name": "FTPFileInput",
  "properties": {
    "type": "FileShare",
    "linkedServiceName": "FTPLinkedService",
    "typeProperties": {
      "folderPath": "mysharedfolder",
      "fileName": "test.csv",
      "useBinaryTransfer": true
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

### <a name="azure-blob-output-dataset"></a>Wyjściowy zestaw danych obiektów blob platformy Azure

Dane są zapisywane w nowym obiekcie blob co godzinę (częstotliwość: godzina, interwał: 1). Ścieżka folderu dla obiektu BLOB jest obliczana dynamicznie na podstawie czasu rozpoczęcia wytworzonego wycinka. Ścieżka folderu używa części roku, miesiąca, dnia i godziny rozpoczęcia.

```JSON
{
    "name": "AzureBlobOutput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/ftp/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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


### <a name="a-copy-activity-in-a-pipeline-with-file-system-source-and-blob-sink"></a>Działanie kopiowania w potoku ze źródłem systemu plików i obiektem sink obiektów BLOB

Potok zawiera działanie kopiowania, które jest skonfigurowane do korzystania z wejściowych i wyjściowych zestawów danych i jest zaplanowane do uruchomienia co godzinę. W definicji JSON potoku typ **źródła** ma wartość **FileSystemSource**, a typ **ujścia** to **wartość blobsink**.

```JSON
{
    "name": "pipeline",
    "properties": {
        "activities": [{
            "name": "FTPToBlobCopy",
            "inputs": [{
                "name": "FtpFileInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "FileSystemSource"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "NewestFirst",
                "retry": 1,
                "timeout": "00:05:00"
            }
        }],
        "start": "2016-08-24T18:00:00Z",
        "end": "2016-08-24T19:00:00Z"
    }
}
```
> [!NOTE]
> Aby zmapować kolumny ze źródłowego zestawu danych do kolumn z obiektu ujścia danych, zobacz [Mapowanie kolumn zestawu danych w Azure Data Factory](data-factory-map-columns.md).

## <a name="next-steps"></a>Następne kroki
Zobacz następujące artykuły:

* Aby dowiedzieć się więcej na temat kluczowych czynników wpływających na wydajność przenoszenia danych (działanie kopiowania) w Data Factory i różne sposoby jego optymalizacji, zobacz [Przewodnik dotyczący wydajności i dostrajania działania kopiowania](data-factory-copy-activity-performance.md).

* Aby uzyskać instrukcje krok po kroku dotyczące tworzenia potoku za pomocą działania kopiowania, zobacz [Samouczek dotyczący działania kopiowania](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
