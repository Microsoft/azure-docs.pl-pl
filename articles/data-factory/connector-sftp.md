---
title: Kopiowanie danych z i do serwera SFTP
description: Dowiedz się, jak kopiować dane z i do serwera SFTP przy użyciu usługi Azure Data Factory.
services: data-factory
documentationcenter: ''
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/02/2020
ms.openlocfilehash: e6d29f73716b04699e0cd250396df7f7d744d4c4
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415245"
---
# <a name="copy-data-from-and-to-sftp-server-using-azure-data-factory"></a>Kopiowanie danych z i do serwera SFTP przy użyciu usługi Azure Data Factory

> [!div class="op_single_selector" title1="Wybierz wersję używanej usługi Data Factory:"]
> * [Wersja 1](v1/data-factory-sftp-connector.md)
> * [Aktualna wersja](connector-sftp.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule opisano sposób kopiowania danych z i do serwera SFTP. Aby dowiedzieć się więcej o usłudze Azure Data Factory, przeczytaj [artykuł wprowadzający](introduction.md).

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Ten łącznik SFTP jest obsługiwany dla następujących działań:

- [Kopiowanie aktywności](copy-activity-overview.md) z [obsługiwaną macierzą źródło/ujście](copy-activity-overview.md)
- [Działanie odnośnika](control-flow-lookup-activity.md)
- [Działanie GetMetadata](control-flow-get-metadata-activity.md)
- [Usuwanie działania](delete-activity.md)

W szczególności to złącze SFTP obsługuje:

- Kopiowanie plików z/do SFTP przy użyciu uwierzytelniania **Basic** lub **SshPublicKey.**
- Kopiowanie plików w stanie jednakowym lub analizowanie/generowanie plików za pomocą [obsługiwanych formatów plików i kodeków kompresji](supported-file-formats-and-compression-codecs.md).

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Rozpoczęcie pracy

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

W poniższych sekcjach znajdują się szczegółowe informacje o właściwościach, które są używane do definiowania jednostek fabryki danych specyficznych dla SFTP.

## <a name="linked-service-properties"></a>Połączone właściwości usługi

Następujące właściwości są obsługiwane dla usługi połączonej z usługą SFTP:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość typu musi być ustawiona na: **Sftp**. |Tak |
| host | Nazwa lub adres IP serwera SFTP. |Tak |
| port | Port, na którym nasłuchuje serwer SFTP.<br/>Dozwolone wartości to: całkowita, wartość domyślna to **22**. |Nie |
| skipHostKeyValidation (SkipHostKeyValidation) | Określ, czy należy pominąć sprawdzanie poprawności klucza hosta.<br/>Dozwolone wartości to: **true**, **false** (domyślnie).  | Nie |
| hostKeyFingerprint | Określ odcisk palca klucza hosta. | Tak, jeśli "skipHostKeyValidation" jest ustawiona na false.  |
| authenticationType | Określ typ uwierzytelniania.<br/>Dozwolone wartości to: **Basic**, **SshPublicKey**. Zapoznaj się [z sekcjami Korzystanie z uwierzytelniania podstawowego](#using-basic-authentication) i [Korzystanie z klucza publicznego SSH](#using-ssh-public-key-authentication) na większej liczbie właściwości i przykładów JSON. |Tak |
| connectVia | [Środowisko wykonawcze integracji,](concepts-integration-runtime.md) które mają być używane do łączenia się z magazynem danych. Dowiedz się więcej z sekcji [Wymagania wstępne.](#prerequisites) Jeśli nie zostanie określony, używa domyślnego środowiska wykonawczego integracji platformy Azure. |Nie |

### <a name="using-basic-authentication"></a>Korzystanie z uwierzytelniania podstawowego

Aby użyć uwierzytelniania podstawowego, należy ustawić właściwość "authenticationType" na **Basic**i określić następujące właściwości oprócz ogólnych właściwości łącznika SFTP wprowadzonych w ostatniej sekcji:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| userName | Użytkownik, który ma dostęp do serwera SFTP. |Tak |
| hasło | Hasło użytkownika (nazwa użytkownika). Oznacz to pole jako SecureString, aby bezpiecznie przechowywać go w fabryce danych lub [odwołaj się do klucza tajnego przechowywanego w usłudze Azure Key Vault.](store-credentials-in-key-vault.md) | Tak |

**Przykład:**

```json
{
    "name": "SftpLinkedService",
    "type": "linkedservices",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<sftp server>",
            "port": 22,
            "skipHostKeyValidation": false,
            "hostKeyFingerPrint": "ssh-rsa 2048 xx:00:00:00:xx:00:x0:0x:0x:0x:0x:00:00:x0:x0:00",
            "authenticationType": "Basic",
            "userName": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="using-ssh-public-key-authentication"></a>Korzystanie z uwierzytelniania klucza publicznego SSH

Aby użyć uwierzytelniania klucza publicznego SSH, należy ustawić właściwość "authenticationType" jako **SshPublicKey**i określić następujące właściwości oprócz ogólnych właściwości łącznika SFTP wprowadzonych w ostatniej sekcji:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| userName | Użytkownik, który ma dostęp do serwera SFTP |Tak |
| ścieżka klucza prywatnego | Określ ścieżkę bezwzględną do pliku klucza prywatnego, do który może uzyskać dostęp środowisko wykonawcze integracji. Ma zastosowanie tylko wtedy, gdy samodzielnie hostowany typ środowiska wykonawczego integracji jest określony w "connectVia". | Określ `privateKeyPath` albo `privateKeyContent`.  |
| prywatny KluczWłasny | Zawartość klucza prywatnego SSH zakodowana w base64. Klucz prywatny SSH powinien być formatem OpenSSH. Oznacz to pole jako SecureString, aby bezpiecznie przechowywać go w fabryce danych lub [odwołaj się do klucza tajnego przechowywanego w usłudze Azure Key Vault.](store-credentials-in-key-vault.md) | Określ `privateKeyPath` albo `privateKeyContent`. |
| Hasło | Określ frazę pass/hasło, aby odszyfrować klucz prywatny, jeśli plik klucza jest chroniony przez frazę przebiegu. Oznacz to pole jako SecureString, aby bezpiecznie przechowywać go w fabryce danych lub [odwołaj się do klucza tajnego przechowywanego w usłudze Azure Key Vault.](store-credentials-in-key-vault.md) | Tak, jeśli plik klucza prywatnego jest chroniony przez frazę przebiegu. |

> [!NOTE]
> Złącze SFTP obsługuje klucz RSA/DSA OpenSSH. Upewnij się, że zawartość pliku klucza zaczyna się od "-----BEGIN [RSA/DSA] PRIVATE KEY-----". Jeśli plik klucza prywatnego jest plikiem w formacie PPK, użyj narzędzia Putty do konwersji z .ppk na format OpenSSH. 

**Przykład 1: Uwierzytelnianie SshPublicKey przy użyciu pliku klucza prywatnegoPath**

```json
{
    "name": "SftpLinkedService",
    "type": "Linkedservices",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<sftp server>",
            "port": 22,
            "skipHostKeyValidation": true,
            "authenticationType": "SshPublicKey",
            "userName": "xxx",
            "privateKeyPath": "D:\\privatekey_openssh",
            "passPhrase": {
                "type": "SecureString",
                "value": "<pass phrase>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Przykład 2: Uwierzytelnianie SshPublicKey przy użyciu zawartości klucza prywatnego**

```json
{
    "name": "SftpLinkedService",
    "type": "Linkedservices",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<sftp server>",
            "port": 22,
            "skipHostKeyValidation": true,
            "authenticationType": "SshPublicKey",
            "userName": "<username>",
            "privateKeyContent": {
                "type": "SecureString",
                "value": "<base64 string of the private key content>"
            },
            "passPhrase": {
                "type": "SecureString",
                "value": "<pass phrase>"
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

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania zestawów danych, zobacz artykuł [Zestawy danych.](concepts-datasets-linked-services.md) 

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Następujące właściwości są obsługiwane dla SFTP w ustawieniach `location` w zestawie danych opartym na formacie:

| Właściwość   | Opis                                                  | Wymagany |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | Właściwość typu `location` w obszarze w zestawie danych musi być ustawiona na **SftpLocation**. | Tak      |
| folderPath | Ścieżka do folderu. Jeśli chcesz użyć symbolu wieloznacznego do filtrowania folderu, pomiń to ustawienie i określ w ustawieniach źródła aktywności. | Nie       |
| fileName   | Nazwa pliku pod danym folderPath. Jeśli chcesz użyć symbolu wieloznacznego do filtrowania plików, pomiń to ustawienie i określ w ustawieniach źródła aktywności. | Nie       |

**Przykład:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<SFTP linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "SftpLocation",
                "folderPath": "root/folder/subfolder"
            },
            "columnDelimiter": ",",
            "quoteChar": "\"",
            "firstRowAsHeader": true,
            "compressionCodec": "gzip"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania działań, zobacz [Pipelines](concepts-pipelines-activities.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez źródło SFTP.

### <a name="sftp-as-source"></a>SFTP jako źródło

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Następujące właściwości są obsługiwane dla SFTP w ustawieniach `storeSettings` w źródle kopiowania opartego na formacie:

| Właściwość                 | Opis                                                  | Wymagany                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | Właściwość type `storeSettings` w obszarze musi być ustawiona na **SftpReadSettings**. | Tak                                           |
| Cykliczne                | Wskazuje, czy dane są odczytywane cyklicznie z podfolderów, czy tylko z określonego folderu. Należy zauważyć, że gdy cykliczne jest ustawiona na true i ujście jest magazyn oparty na plikach, pusty folder lub podfolder nie jest kopiowany lub tworzony w zlewie. Dozwolone wartości są **prawdziwe** (domyślnie) i **false**. | Nie                                            |
| Ścieżka wieloznacznaFolderPath       | Ścieżka folderu ze znakami wieloznaczowymi do filtrowania folderów źródłowych. <br>Dozwolone symbole wieloznaczne to: `*` (dopasowuje zero lub więcej znaków) i `?` (pasuje do zera lub pojedynczego znaku); użyj, `^` aby uciec, jeśli rzeczywista nazwa folderu ma symbol wieloznaczny lub ten znak ucieczki wewnątrz. <br>Zobacz więcej przykładów w [przykładach filtru folderów i plików](#folder-and-file-filter-examples). | Nie                                            |
| symboli wieloznacznychFileName         | Nazwa pliku ze znakami wieloznacznymi pod danym folderpath/wildcardFolderPath do filtrowania plików źródłowych. <br>Dozwolone symbole wieloznaczne to: `*` (dopasowuje zero lub więcej znaków) i `?` (pasuje do zera lub pojedynczego znaku); użyj, `^` aby uciec, jeśli rzeczywista nazwa folderu ma symbol wieloznaczny lub ten znak ucieczki wewnątrz.  Zobacz więcej przykładów w [przykładach filtru folderów i plików](#folder-and-file-filter-examples). | Tak, `fileName` jeśli nie jest określony w zestawie danych |
| modifiedDatetimeStart    | Filtr plików na podstawie atrybutu: Ostatnia modyfikacja. Pliki zostaną wybrane, jeśli ich ostatni zmodyfikowany czas `modifiedDatetimeStart` `modifiedDatetimeEnd`mieści się w przedziale czasu między i . Czas jest stosowany do strefy czasowej UTC w formacie "2018-12-01T05:00:00Z". <br> Właściwości mogą mieć wartość NULL, co oznacza, że do zestawu danych nie zostanie zastosowany żaden filtr atrybutów pliku.  Gdy `modifiedDatetimeStart` ma wartość `modifiedDatetimeEnd` datetime, ale jest null, oznacza to, że pliki, których ostatni zmodyfikowany atrybut jest większa lub równa z wartości datetime zostaną wybrane.  Gdy `modifiedDatetimeEnd` ma wartość `modifiedDatetimeStart` datetime, ale jest null, oznacza to, że pliki, których ostatni zmodyfikowany atrybut jest mniejsza niż wartość datetime zostaną wybrane. | Nie                                            |
| modifiedDatetimeEnd      | Tak samo jak powyżej.                                               | Nie                                            |
| maxConcurrentConnections (Połączenie maksymalne) | Liczba połączeń do łączenia się z magazynem magazynowym jednocześnie. Określ tylko wtedy, gdy chcesz ograniczyć jednoczesne połączenie z magazynem danych. | Nie                                            |

**Przykład:**

```json
"activities":[
    {
        "name": "CopyFromSFTP",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Delimited text input dataset name>",
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
                "type": "DelimitedTextSource",
                "formatSettings":{
                    "type": "DelimitedTextReadSettings",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "SftpReadSettings",
                    "recursive": true,
                    "wildcardFolderPath": "myfolder*A",
                    "wildcardFileName": "*.csv"
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="sftp-as-sink"></a>SFTP jako zlew

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Następujące właściwości są obsługiwane dla SFTP w ustawieniach `storeSettings` w umywalka kopii oparta na formacie:

| Właściwość                 | Opis                                                  | Wymagany |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | Właściwość typu `storeSettings` w obszarze musi być ustawiona na **SftpWriteSettings**. | Tak      |
| copyBehavior             | Definiuje zachowanie kopiowania, gdy źródłem są pliki z magazynu danych opartych na plikach.<br/><br/>Dozwolone wartości to:<br/><b>- PreserveHierarchy (domyślnie):</b>Zachowuje hierarchię plików w folderze docelowym. Względna ścieżka pliku źródłowego do folderu źródłowego jest identyczna ze ścieżką względną pliku docelowego do folderu docelowego.<br/><b>- FlattenHierarchy:</b>Wszystkie pliki z folderu źródłowego znajdują się na pierwszym poziomie folderu docelowego. Pliki docelowe mają automatycznie generowane nazwy. <br/><b>- MergeFiles:</b>Scala wszystkie pliki z folderu źródłowego do jednego pliku. Jeśli nazwa pliku jest określona, scalona nazwa pliku jest określoną nazwą. W przeciwnym razie jest to nazwa pliku z automatycznym generacją. | Nie       |
| maxConcurrentConnections (Połączenie maksymalne) | Liczba połączeń do łączenia się z magazynem danych jednocześnie. Określ tylko wtedy, gdy chcesz ograniczyć jednoczesne połączenie z magazynem danych. | Nie       |
| useTempFileRename | Określ, czy należy przekazać do plików tymczasowych i zmienić jego nazwę, czy też bezpośrednio zapisać do docelowej lokalizacji folderu/pliku. Domyślnie ADF najpierw zapisuj do plików tymczasowych, a następnie do zmiany nazwy pliku po zakończeniu przekazywania, aby uniknąć konfliktu zapisu powodującego uszkodzenie pliku, jeśli masz inny proces zapisu do tego samego pliku, a 2) upewnij się, że oryginalna wersja pliku istnieje podczas całego transferu. Jeśli serwer SFTP nie obsługuje operacji zmiany nazwy, wyłącz tę opcję i upewnij się, że nie masz równoczesnego zapisu w pliku docelowym. Zobacz poradę dotyczącą rozwiązywania problemów poniżej tej tabeli. | Nie. Wartość domyślna jest true. |
| operacjaCzas | Czas oczekiwania przed każdym żądaniem zapisu do serwera SFTP limit czasu. Wartość domyślna to 60 min (01:00:00).|Nie |

>[!TIP]
>Jeśli podczas zapisywania danych w SFTP zostanie wyświetlony błąd "UserErrorSftpPathNotFound", "UserErrorSftpPermissionDenied" lub "SftpOperationFail", a używany użytkownik SFTP ma odpowiednie uprawnienia, sprawdź, czy plik obsługi`useTempFileRename`serwera SFTP zmienia nazwę operacji - jeśli nie, wyłącz opcję "Prześlij z plikiem tymczasowym" ( ) i spróbuj ponownie. Dowiedz się więcej o tej właściwości z powyższej tabeli. Jeśli używasz self-hosted Integration Runtime do kopiowania, upewnij się, że używasz wersji 4.6 lub wyższej.

**Przykład:**

```json
"activities":[
    {
        "name": "CopyToSFTP",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
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
                "type": "<source type>"
            },
            "sink": {
                "type": "BinarySink",
                "storeSettings":{
                    "type": "SftpWriteSettings",
                    "copyBehavior": "PreserveHierarchy"
                }
            }
        }
    }
]
```

### <a name="folder-and-file-filter-examples"></a>Przykłady filtrów folderów i plików

W tej sekcji opisano wynikowe zachowanie ścieżki folderu i nazwy pliku za pomocą filtrów wieloznacznych.

| folderPath | fileName | Cykliczne | Struktura folderów źródłowych i wynik filtru (pliki **pogrubione** są pobierane)|
|:--- |:--- |:--- |:--- |
| `Folder*` | (pusty, użyj domyślnie) | false | FolderA ( folderA )<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Plik1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Plik2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Podfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik5.csv<br/>InnyFolderb<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik 6.csv |
| `Folder*` | (pusty, użyj domyślnie) | true | FolderA ( folderA )<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Plik1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Plik2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Podfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Plik3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Plik4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Plik5.csv**<br/>InnyFolderb<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik 6.csv |
| `Folder*` | `*.csv` | false | FolderA ( folderA )<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Plik1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Podfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik5.csv<br/>InnyFolderb<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik 6.csv |
| `Folder*` | `*.csv` | true | FolderA ( folderA )<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Plik1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Podfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Plik3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Plik5.csv**<br/>InnyFolderb<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik 6.csv |

## <a name="lookup-activity-properties"></a>Właściwości działania odnośnika

Aby dowiedzieć się więcej o właściwościach, sprawdź [działanie odnośnika](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Właściwości działania GetMetadata

Aby dowiedzieć się więcej o właściwościach, sprawdź [działanie GetMetadata](control-flow-get-metadata-activity.md) 

## <a name="delete-activity-properties"></a>Usuwanie właściwości działania

Aby dowiedzieć się więcej o właściwościach, sprawdź [Usuń działanie](delete-activity.md)

## <a name="legacy-models"></a>Starsze modele

>[!NOTE]
>Następujące modele są nadal obsługiwane jako — dla zgodności z powrotem. Zaleca się użycie nowego modelu wymienionego w powyższych sekcjach w przyszłości, a interfejs użytkownika tworzenia podajnikiem ADF przełączył się na generowanie nowego modelu.

### <a name="legacy-dataset-model"></a>Starszy model zestawu danych

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość typu zestawu danych musi być ustawiona na: **FileShare** |Tak |
| folderPath | Ścieżka do folderu. Filtr symboli wieloznacznych jest obsługiwany, `*` dozwolone symbole wieloznaczne `?` to: (dopasowuje zero lub więcej znaków) i (pasuje do zera lub pojedynczego znaku); użyj, `^` aby uciec, jeśli rzeczywista nazwa pliku ma symbol wieloznaczny lub ten znak ucieczki wewnątrz. <br/><br/>Przykłady: rootfolder/subfolder/, zobacz więcej przykładów w [przykładach filtrów folderów i plików](#folder-and-file-filter-examples). |Tak |
| fileName |  **Nazwa lub symbol wieloznaczny filtr** dla plików w ramach określonego "folderPath". Jeśli nie określisz wartości dla tej właściwości, zestaw danych wskazuje wszystkie pliki w folderze. <br/><br/>W przypadku filtru dozwolone symbole wieloznaczne `*` to: `?` (dopasowuje zero lub więcej znaków) i (pasuje do zera lub pojedynczego znaku).<br/>- Przykład 1:`"fileName": "*.csv"`<br/>- Przykład 2:`"fileName": "???20180427.txt"`<br/>Użyj, `^` aby uciec, jeśli rzeczywista nazwa folderu ma symbol wieloznaczny lub ten znak ucieczki wewnątrz. |Nie |
| modifiedDatetimeStart | Filtr plików na podstawie atrybutu: Ostatnia modyfikacja. Pliki zostaną wybrane, jeśli ich ostatni zmodyfikowany czas `modifiedDatetimeStart` `modifiedDatetimeEnd`mieści się w przedziale czasu między i . Czas jest stosowany do strefy czasowej UTC w formacie "2018-12-01T05:00:00Z". <br/><br/> Należy pamiętać, że ogólna wydajność przenoszenia danych będzie mieć wpływ, włączając to ustawienie, gdy chcesz wykonać filtr plików z ogromnych ilości plików. <br/><br/> Właściwości mogą mieć wartość NULL, co oznacza, że do zestawu danych nie zostanie zastosowany żaden filtr atrybutów pliku.  Gdy `modifiedDatetimeStart` ma wartość `modifiedDatetimeEnd` datetime, ale jest null, oznacza to, że pliki, których ostatni zmodyfikowany atrybut jest większa lub równa z wartości datetime zostaną wybrane.  Gdy `modifiedDatetimeEnd` ma wartość `modifiedDatetimeStart` datetime, ale jest null, oznacza to, że pliki, których ostatni zmodyfikowany atrybut jest mniejsza niż wartość datetime zostaną wybrane.| Nie |
| modifiedDatetimeEnd | Filtr plików na podstawie atrybutu: Ostatnia modyfikacja. Pliki zostaną wybrane, jeśli ich ostatni zmodyfikowany czas `modifiedDatetimeStart` `modifiedDatetimeEnd`mieści się w przedziale czasu między i . Czas jest stosowany do strefy czasowej UTC w formacie "2018-12-01T05:00:00Z". <br/><br/> Należy pamiętać, że ogólna wydajność przenoszenia danych będzie mieć wpływ, włączając to ustawienie, gdy chcesz wykonać filtr plików z ogromnych ilości plików. <br/><br/> Właściwości mogą mieć wartość NULL, co oznacza, że do zestawu danych nie zostanie zastosowany żaden filtr atrybutów pliku.  Gdy `modifiedDatetimeStart` ma wartość `modifiedDatetimeEnd` datetime, ale jest null, oznacza to, że pliki, których ostatni zmodyfikowany atrybut jest większa lub równa z wartości datetime zostaną wybrane.  Gdy `modifiedDatetimeEnd` ma wartość `modifiedDatetimeStart` datetime, ale jest null, oznacza to, że pliki, których ostatni zmodyfikowany atrybut jest mniejsza niż wartość datetime zostaną wybrane.| Nie |
| format | Jeśli chcesz **skopiować pliki jako znajdujące się** między magazynami opartymi na plikach (kopia binarna), pomiń sekcję formatu w definicjach danych wejściowych i wyjściowych.<br/><br/>Jeśli chcesz przeanalizować pliki w określonym formacie, obsługiwane są następujące typy formatów plików: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Ustaw właściwość **typu** w formacie na jedną z tych wartości. Aby uzyskać więcej informacji, zobacz [Sekcje Format tekstu,](supported-file-formats-and-compression-codecs-legacy.md#text-format) [Format Json,](supported-file-formats-and-compression-codecs-legacy.md#json-format) [Format Avro,](supported-file-formats-and-compression-codecs-legacy.md#avro-format) [Format orków](supported-file-formats-and-compression-codecs-legacy.md#orc-format)i [Format parkietu.](supported-file-formats-and-compression-codecs-legacy.md#parquet-format) |Nie (tylko w przypadku scenariusza kopiowania binarnego) |
| kompresja | Określ typ i poziom kompresji danych. Aby uzyskać więcej informacji, zobacz [Obsługiwane formaty plików i kodeki kompresji](supported-file-formats-and-compression-codecs-legacy.md#compression-support).<br/>Obsługiwane typy to: **GZip**, **Deflate**, **BZip2**i **ZipDeflate**.<br/>Obsługiwane poziomy to: **Optymalne** i **najszybsze**. |Nie |

>[!TIP]
>Aby skopiować wszystkie pliki w folderze, określ tylko **folderPath.**<br>Aby skopiować pojedynczy plik o podanej nazwie, określ **folderPath** z częścią **folderu** i nazwą pliku z nazwą pliku.<br>Aby skopiować podzbiór plików w folderze, określ **folderPath** z częścią folderu i **fileName** z filtrem wieloznacznym.

>[!NOTE]
>Jeśli używasz "fileFilter" właściwość dla filtru plików, nadal jest obsługiwany w stanie, podczas gdy zaleca się użycie nowej funkcji filtru dodane do "fileName" w przyszłości.

**Przykład:**

```json
{
    "name": "SFTPDataset",
    "type": "Datasets",
    "properties": {
        "type": "FileShare",
        "linkedServiceName":{
            "referenceName": "<SFTP linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "folder/subfolder/",
            "fileName": "*",
            "modifiedDatetimeStart": "2018-12-01T05:00:00Z",
            "modifiedDatetimeEnd": "2018-12-01T06:00:00Z",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ",",
                "rowDelimiter": "\n"
            },
            "compression": {
                "type": "GZip",
                "level": "Optimal"
            }
        }
    }
}
```

### <a name="legacy-copy-activity-source-model"></a>Starszy model źródła działania kopiowania

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość typu źródła działania kopiowania musi być ustawiona na: **FileSystemSource** |Tak |
| Cykliczne | Wskazuje, czy dane są odczytywane cyklicznie z podfolderów, czy tylko z określonego folderu. Uwaga, gdy rekursywny jest ustawiony na true i sink jest magazyn oparty na plikach, pusty folder /podfolder nie zostaną skopiowane / utworzone w zlewie.<br/>Dozwolone wartości to: **true** (default), **false** | Nie |
| maxConcurrentConnections (Połączenie maksymalne) | Liczba połączeń do łączenia się z magazynem magazynowym jednocześnie. Określ tylko wtedy, gdy chcesz ograniczyć jednoczesne połączenie z magazynem danych. | Nie |

**Przykład:**

```json
"activities":[
    {
        "name": "CopyFromSFTP",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SFTP input dataset name>",
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
                "type": "FileSystemSource",
                "recursive": true
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="next-steps"></a>Następne kroki
Aby uzyskać listę magazynów danych obsługiwanych jako źródła i pochłaniacze przez działanie kopiowania w usłudze Azure Data Factory, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
