---
title: Przenoszenie danych z serwera SFTP przy użyciu Azure Data Factory
description: Dowiedz się więcej na temat przenoszenia danych z serwera lokalnego lub SFTP w chmurze przy użyciu Azure Data Factory.
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2018
robots: noindex
ms.openlocfilehash: b40e9dc83629362da899d2b5ff29ad42e21c4e32
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100382654"
---
# <a name="move-data-from-an-sftp-server-using-azure-data-factory"></a>Przenoszenie danych z serwera SFTP przy użyciu Azure Data Factory
> [!div class="op_single_selector" title1="Wybierz używaną wersję usługi Data Factory:"]
> * [Wersja 1](data-factory-sftp-connector.md)
> * [Wersja 2 (bieżąca wersja)](../connector-sftp.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącej wersji usługi Data Factory, zobacz [SFTPconnector w wersji 2](../connector-sftp.md).

W tym artykule opisano sposób używania działania kopiowania w programie Azure Data Factory do przenoszenia danych z serwera z systemem, który jest używany lokalnie/w chmurze, do obsługiwanego magazynu danych ujścia. Ten artykuł zawiera informacje o [działaniach związanych z przenoszeniem danych](data-factory-data-movement-activities.md) , które przedstawia ogólne omówienie przenoszenia danych za pomocą działania kopiowania i listę magazynów danych obsługiwanych jako źródła/ujścia.

Fabryka danych obsługuje obecnie tylko przeniesienie danych z serwera SFTP do innych magazynów danych, ale nie do przeniesienia danych z innych magazynów danych na serwer SFTP. Obsługuje on zarówno serwery w chmurze, jak i na serwerze SFTP.

> [!NOTE]
> Działanie kopiowania nie usuwa pliku źródłowego po pomyślnym skopiowaniu do miejsca docelowego. Jeśli musisz usunąć plik źródłowy po pomyślnej kopii, Utwórz niestandardowe działanie, aby usunąć plik i użyć działania w potoku.

## <a name="supported-scenarios-and-authentication-types"></a>Obsługiwane scenariusze i typy uwierzytelniania
Tego łącznika SFTP można używać do kopiowania danych z **serwerów SFTP w chmurze i lokalnych serwerów SFTP**. Typy uwierzytelniania **Basic** i **SshPublicKey** są obsługiwane podczas nawiązywania połączenia z serwerem SFTP.

Podczas kopiowania danych z lokalnego serwera SFTP należy zainstalować bramę Zarządzanie danymi w środowisku lokalnym/maszynie wirtualnej platformy Azure. Szczegółowe informacje na temat bramy znajdują się w temacie [Zarządzanie danymi Gateway](data-factory-data-management-gateway.md) . Aby uzyskać instrukcje krok po kroku dotyczące konfigurowania bramy i korzystania z niej, zobacz temat [przeniesienie danych między lokalizacjami lokalnymi i artykułem w chmurze](data-factory-move-data-between-onprem-and-cloud.md) .

## <a name="getting-started"></a>Wprowadzenie
Można utworzyć potok z działaniem kopiowania, które przenosi dane ze źródła SFTP przy użyciu różnych narzędzi/interfejsów API.

- Najprostszym sposobem utworzenia potoku jest użycie **Kreatora kopiowania**. Zobacz [Samouczek: Tworzenie potoku za pomocą Kreatora kopiowania](data-factory-copy-data-wizard-tutorial.md) na potrzeby szybkiego instruktażu dotyczącego tworzenia potoku przy użyciu Kreatora kopiowania danych.

- Do utworzenia potoku można także użyć następujących narzędzi: **Visual Studio**, **Azure PowerShell**, **szablon Azure Resource Manager**, interfejs API **platformy .NET** i **interfejs API REST**. Aby uzyskać instrukcje krok po kroku dotyczące tworzenia potoku za pomocą działania kopiowania, zobacz [Samouczek dotyczący działania kopiowania](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) . Aby uzyskać przykłady JSON kopiowania danych z serwera SFTP do usługi Azure Blob Storage, zobacz [przykład JSON: kopiowanie danych z serwera SFTP do usługi Azure Blob](#json-example-copy-data-from-sftp-server-to-azure-blob) w tym artykule.

## <a name="linked-service-properties"></a>Właściwości połączonej usługi
Poniższa tabela zawiera opis elementów JSON specyficznych dla połączonej usługi FTP.

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| typ | Właściwość Type musi być ustawiona na wartość `Sftp` . |Tak |
| host | Nazwa lub adres IP serwera SFTP. |Tak |
| port |Port, na którym nasłuchuje serwer SFTP. Wartość domyślna to: 21 |Nie |
| authenticationType |Określ typ uwierzytelniania. Dozwolone wartości: **Basic**, **SshPublicKey**. <br><br> Zapoznaj się z sekcją [uwierzytelnianie podstawowe](#using-basic-authentication) i [przy użyciu opcji uwierzytelniania klucza publicznego SSH](#using-ssh-public-key-authentication) , aby uzyskać więcej właściwości i próbek JSON. |Tak |
| skipHostKeyValidation | Określ, czy pominąć sprawdzanie poprawności klucza hosta. | Nie. Wartość domyślna: false |
| hostKeyFingerprint | Określ drukowanie odcisku palca klucza hosta. | Tak, jeśli `skipHostKeyValidation` ma wartość false.  |
| gatewayName |Nazwa bramy Zarządzanie danymi w celu nawiązania połączenia z lokalnym serwerem SFTP. | Tak w przypadku kopiowania danych z lokalnego serwera SFTP. |
| encryptedCredential | Zaszyfrowane poświadczenia w celu uzyskania dostępu do serwera SFTP. Generowane automatycznie po określeniu uwierzytelniania podstawowego (nazwa użytkownika i hasło) lub uwierzytelniania SshPublicKey (nazwa użytkownika i Ścieżka klucza prywatnego lub zawartość) w Kreatorze kopiowania lub podręcznym oknie dialogowym ClickOnce. | Nie. Stosuje się tylko w przypadku kopiowania danych z lokalnego serwera SFTP. |

### <a name="using-basic-authentication"></a>Korzystanie z uwierzytelniania podstawowego

Aby użyć uwierzytelniania podstawowego, ustaw `authenticationType` jako `Basic` i określ następujące właściwości, oprócz ogólnych ŁĄCZNIKa SFTP, które zostały wprowadzone w ostatniej sekcji:

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| nazwa użytkownika | Użytkownik, który ma dostęp do serwera SFTP. |Tak |
| hasło | Hasło użytkownika (username). | Tak |

#### <a name="example-basic-authentication"></a>Przykład: uwierzytelnianie podstawowe
```json
{
    "name": "SftpLinkedService",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "mysftpserver",
            "port": 22,
            "authenticationType": "Basic",
            "username": "xxx",
            "password": "xxx",
            "skipHostKeyValidation": false,
            "hostKeyFingerPrint": "ssh-rsa 2048 xx:00:00:00:xx:00:x0:0x:0x:0x:0x:00:00:x0:x0:00",
            "gatewayName": "mygateway"
        }
    }
}
```

#### <a name="example-basic-authentication-with-encrypted-credential"></a>Przykład: uwierzytelnianie podstawowe z szyfrowanym poświadczeniem

```JSON
{
    "name": "SftpLinkedService",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "mysftpserver",
            "port": 22,
            "authenticationType": "Basic",
            "username": "xxx",
            "encryptedCredential": "xxxxxxxxxxxxxxxxx",
            "skipHostKeyValidation": false,
            "hostKeyFingerPrint": "ssh-rsa 2048 xx:00:00:00:xx:00:x0:0x:0x:0x:0x:00:00:x0:x0:00",
            "gatewayName": "mygateway"
        }
      }
}
```

### <a name="using-ssh-public-key-authentication"></a>Korzystanie z uwierzytelniania za pomocą klucza publicznego SSH

Aby można było użyć uwierzytelniania klucza publicznego SSH, ustaw `authenticationType` jako `SshPublicKey` i określ następujące właściwości, oprócz ogólnych łącznika SFTP, które zostały wprowadzone w ostatniej sekcji:

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| nazwa użytkownika |Użytkownik, który ma dostęp do serwera SFTP |Tak |
| privateKeyPath | Określ ścieżkę bezwzględną do pliku klucza prywatnego, do którego Brama może uzyskać dostęp. | Określ albo `privateKeyPath` `privateKeyContent` . <br><br> Stosuje się tylko w przypadku kopiowania danych z lokalnego serwera SFTP. |
| privateKeyContent | Serializowany ciąg zawartości klucza prywatnego. Kreator kopiowania może odczytać plik klucza prywatnego i automatycznie wyodrębnić zawartość klucza prywatnego. Jeśli używasz innego narzędzia/zestawu SDK, zamiast tego użyj właściwości privateKeyPath. | Określ albo `privateKeyPath` `privateKeyContent` . |
| Danym | Określ wartość Przekaż frazę/hasło w celu odszyfrowania klucza prywatnego, jeśli plik klucza jest chroniony za pomocą frazy Pass. | Tak, jeśli plik klucza prywatnego jest chroniony przez frazę Pass. |

> [!NOTE]
> Łącznik SFTP obsługuje klucz OpenSSH RSA/DSA. Upewnij się, że zawartość pliku klucza rozpoczyna się od "-----BEGIN [RSA/DSA] klucza prywatnego-----". Jeśli plik klucza prywatnego jest plikiem PPK, użyj narzędzia do konwersji z. ppk na format OpenSSH.

#### <a name="example-sshpublickey-authentication-using-private-key-filepath"></a>Przykład: uwierzytelnianie SshPublicKey przy użyciu ścieżki klucza prywatnego

```json
{
    "name": "SftpLinkedServiceWithPrivateKeyPath",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "mysftpserver",
            "port": 22,
            "authenticationType": "SshPublicKey",
            "username": "xxx",
            "privateKeyPath": "D:\\privatekey_openssh",
            "passPhrase": "xxx",
            "skipHostKeyValidation": true,
            "gatewayName": "mygateway"
        }
    }
}
```

#### <a name="example-sshpublickey-authentication-using-private-key-content"></a>Przykład: uwierzytelnianie SshPublicKey przy użyciu zawartości klucza prywatnego

```json
{
    "name": "SftpLinkedServiceWithPrivateKeyContent",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "mysftpserver.westus.cloudapp.azure.com",
            "port": 22,
            "authenticationType": "SshPublicKey",
            "username": "xxx",
            "privateKeyContent": "<base64 string of the private key content>",
            "passPhrase": "xxx",
            "skipHostKeyValidation": true
        }
    }
}
```

## <a name="dataset-properties"></a>Właściwości zestawu danych
Aby uzyskać pełną listę sekcji & właściwości dostępne do definiowania zestawów danych, zobacz artykuł [Tworzenie zestawów danych](data-factory-create-datasets.md) . Sekcje, takie jak struktura, dostępność i zasady JSON zestawu danych, są podobne dla wszystkich typów zestawów danych.

Sekcja **typeProperties** jest inna dla każdego typu zestawu danych. Zawiera informacje, które są specyficzne dla typu zestawu danych. Sekcja typeProperties **dla zestawu danych typu DataSet** ma następujące właściwości:

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| folderPath |Ścieżka podrzędna do folderu. Użyj znaku ucieczki "\" dla znaków specjalnych w ciągu. Przykłady można znaleźć w temacie przykładowe połączone usługi i zestawy danych.<br/><br/>Możesz połączyć tę właściwość z **partitionBy** , aby mieć ścieżki folderu na podstawie daty rozpoczęcia/zakończenia wycinka. |Tak |
| fileName |Określ nazwę pliku w **folderPath** , jeśli chcesz, aby tabela odnosiła się do określonego pliku w folderze. Jeśli nie określisz żadnej wartości dla tej właściwości, tabela wskazuje wszystkie pliki w folderze.<br/><br/>Jeśli nie określono nazwy pliku wyjściowego zestawu danych, nazwa wygenerowanego pliku będzie w następującym formacie: <br/><br/>`Data.<Guid>.txt` (Przykład: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Nie |
| fileFilter |Określ filtr, który ma być używany do wybierania podzbioru plików w folderPath, a nie wszystkich plików.<br/><br/>Dozwolone wartości to: `*` (wiele znaków) i `?` (pojedynczy znak).<br/><br/>Przykłady 1: `"fileFilter": "*.log"`<br/>Przykład 2: `"fileFilter": 2014-1-?.txt"`<br/><br/> fileFilter ma zastosowanie do wejściowego zestawu danych. Ta właściwość nie jest obsługiwana w systemie plików HDFS. |Nie |
| partitionedBy |partitionedBy można użyć, aby określić dynamiczny folderPath, filename dla danych szeregów czasowych. Na przykład folderPath sparametryzowane dla każdej godziny danych. |Nie |
| format | Obsługiwane są następujące typy formatów: **TextFormat**, **formatu jsonformat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Ustaw właściwość **Type** w polu Format na jedną z tych wartości. Aby uzyskać więcej informacji, zobacz [format tekstowy](data-factory-supported-file-and-compression-formats.md#text-format), [Format JSON](data-factory-supported-file-and-compression-formats.md#json-format), [Format Avro](data-factory-supported-file-and-compression-formats.md#avro-format), [Format Orc](data-factory-supported-file-and-compression-formats.md#orc-format)i sekcje [formatu Parquet](data-factory-supported-file-and-compression-formats.md#parquet-format) . <br><br> Jeśli chcesz **skopiować pliki** między magazynami opartymi na plikach (kopia binarna), Pomiń sekcję format w definicjach zestawu danych wejściowych i wyjściowych. |Nie |
| kompresja | Określ typ i poziom kompresji danych. Obsługiwane typy to: **gzip**, **Wklęśnięcie**, **BZip2** i **ZipDeflate**. Obsługiwane poziomy to: **optymalne** i **najszybszy**. Aby uzyskać więcej informacji, zobacz [formaty plików i kompresji w Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Nie |
| useBinaryTransfer |Określ, czy ma być używany tryb transferu danych binarnych. Wartość true dla trybu binarnego i false ASCII. Wartość domyślna: true. Tej właściwości można użyć tylko wtedy, gdy skojarzony typ połączonej usługi jest typu: FtpServer. |Nie |

> [!NOTE]
> nazwy pliku i fileFilter nie można jednocześnie używać.

### <a name="using-partionedby-property"></a>Używanie właściwości partionedBy
Jak wspomniano w poprzedniej sekcji, można określić dynamiczny folderPath, nazwę pliku dla danych szeregów czasowych z partitionedBy. Można to zrobić za pomocą makr Data Factory i zmiennej systemowej parametru slicestart, SliceEnd, która wskazuje logiczny okres czasu dla danego wycinka danych.

Aby dowiedzieć się więcej o zestawach danych, planowaniu i wycinkach szeregów czasowych, zobacz [Tworzenie zestawów danych](data-factory-create-datasets.md), [Planowanie & wykonywania](data-factory-scheduling-and-execution.md)oraz [Tworzenie artykułów potoków](data-factory-create-pipelines.md) .

#### <a name="sample-1"></a>Przykład 1:

```json
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```
W tym przykładzie {Slice} jest zastępowana wartością zmiennej systemowej Data Factory parametru slicestart w formacie (YYYYMMDDHH). Parametru slicestart odnosi się do czasu rozpoczęcia wycinka. FolderPath różni się dla każdego wycinka. Przykład: wikidatagateway/wikisampledataout/2014100103 lub wikidatagateway/wikisampledataout/2014100104.

#### <a name="sample-2"></a>Przykład 2:

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
W tym przykładzie rok, miesiąc, dzień i czas parametru slicestart są wyodrębniane do oddzielnych zmiennych, które są używane przez właściwości folderPath i fileName.

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania
Aby uzyskać pełną listę sekcji & właściwości dostępne do definiowania działań, zobacz artykuł [Tworzenie potoków](data-factory-create-pipelines.md) . Właściwości, takie jak nazwa, opis, tabele wejściowe i wyjściowe, oraz zasady są dostępne dla wszystkich typów działań.

Natomiast właściwości dostępne w sekcji typeProperties działania różnią się w zależności od typu działania. W przypadku działania kopiowania właściwości typu różnią się w zależności od typów źródeł i ujścia.

[!INCLUDE [data-factory-file-system-source](../../../includes/data-factory-file-system-source.md)]

## <a name="supported-file-and-compression-formats"></a>Obsługiwane formaty plików i kompresji
Aby uzyskać szczegółowe informacje [, zobacz formaty plików i kompresji w artykule Azure Data Factory](data-factory-supported-file-and-compression-formats.md) .

## <a name="json-example-copy-data-from-sftp-server-to-azure-blob"></a>Przykład JSON: kopiowanie danych z serwera SFTP do obiektu blob platformy Azure
W poniższym przykładzie przedstawiono przykładowe definicje JSON, których można użyć do utworzenia potoku przy użyciu [programu Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) lub [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Pokazują one sposób kopiowania danych ze źródła SFTP do usługi Azure Blob Storage. Można jednak skopiować dane **bezpośrednio** z dowolnego źródła do dowolnego ujścia w [tym miejscu](data-factory-data-movement-activities.md#supported-data-stores-and-formats) za pomocą działania kopiowania w Azure Data Factory.

> [!IMPORTANT]
> Ten przykład zawiera fragmenty kodu JSON. Zawiera instrukcje krok po kroku dotyczące tworzenia fabryki danych. Instrukcje krok po kroku znajdują się [w sekcji przeniesienie danych między lokalizacjami lokalnymi i artykułem w chmurze](data-factory-move-data-between-onprem-and-cloud.md) .

Przykład zawiera następujące jednostki fabryki danych:

* Połączona usługa typu [SFTP](#linked-service-properties).
* Połączona usługa typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Wejściowy [zestaw danych](data-factory-create-datasets.md) typu [udziału](#dataset-properties).
* Wyjściowy [zestaw danych](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* [Potok](data-factory-create-pipelines.md) z działaniem kopiowania korzystającym z [FileSystemSource](#copy-activity-properties) i [wartość blobsink](data-factory-azure-blob-connector.md#copy-activity-properties).

Przykładowo kopiuje dane z serwera SFTP do obiektu blob platformy Azure co godzinę. Właściwości JSON używane w tych przykładach są opisane w sekcjach poniżej przykładów.

**Połączona usługa SFTP**

Ten przykład używa uwierzytelniania podstawowego z nazwą użytkownika i hasłem w postaci zwykłego tekstu. Można również użyć jednej z następujących metod:

* Uwierzytelnianie podstawowe z szyfrowanymi poświadczeniami
* Uwierzytelnianie klucza publicznego SSH

Zobacz sekcję [połączona usługa FTP](#linked-service-properties) , aby poznać różne typy uwierzytelniania, których można użyć.

```JSON

{
    "name": "SftpLinkedService",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "mysftpserver",
            "port": 22,
            "authenticationType": "Basic",
            "username": "myuser",
            "password": "mypassword",
            "skipHostKeyValidation": false,
            "hostKeyFingerPrint": "ssh-rsa 2048 xx:00:00:00:xx:00:x0:0x:0x:0x:0x:00:00:x0:x0:00",
            "gatewayName": "mygateway"
        }
    }
}
```
**Połączona usługa Azure Storage**

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
**Wejściowy zestaw danych protokołu SFTP**

Ten zestaw danych odwołuje się do folderu `mysharedfolder` i pliku SFTP `test.csv` . Potok kopiuje plik do miejsca docelowego.

Ustawienie "External": "true" informuje usługę Data Factory, że zestaw danych jest zewnętrzny względem fabryki danych i nie jest tworzony przez działanie w fabryce danych.

```JSON
{
  "name": "SFTPFileInput",
  "properties": {
    "type": "FileShare",
    "linkedServiceName": "SftpLinkedService",
    "typeProperties": {
      "folderPath": "mysharedfolder",
      "fileName": "test.csv"
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

**Wyjściowy zestaw danych obiektów blob platformy Azure**

Dane są zapisywane w nowym obiekcie blob co godzinę (częstotliwość: godzina, interwał: 1). Ścieżka folderu dla obiektu BLOB jest obliczana dynamicznie na podstawie czasu rozpoczęcia przetwarzanego wycinka. Ścieżka folderu używa części roku, miesiąca, dnia i godziny rozpoczęcia.

```JSON
{
    "name": "AzureBlobOutput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/sftp/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

**Potok z działaniem kopiowania**

Potok zawiera działanie kopiowania, które jest skonfigurowane do korzystania z wejściowych i wyjściowych zestawów danych i zaplanowane do uruchomienia co godzinę. W definicji JSON potoku typ **źródła** ma wartość **FileSystemSource** , a typ **ujścia** to **wartość blobsink**.

```JSON
{
    "name": "pipeline",
    "properties": {
        "activities": [{
            "name": "SFTPToBlobCopy",
            "inputs": [{
                "name": "SFTPFileInput"
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
        "start": "2017-02-20T18:00:00Z",
        "end": "2017-02-20T19:00:00Z"
    }
}
```

## <a name="performance-and-tuning"></a>Wydajność i dostrajanie
Zobacz [Przewodnik dostrajania wydajności & działania kopiowania](data-factory-copy-activity-performance.md) , aby poznać kluczowe czynniki wpływające na wydajność przenoszenia danych (działanie kopiowania) w Azure Data Factory i różne sposoby jego optymalizacji.

## <a name="next-steps"></a>Następne kroki
Zobacz następujące artykuły:

* [Samouczek działania kopiowania](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) zawiera instrukcje krok po kroku dotyczące tworzenia potoku za pomocą działania kopiowania.
