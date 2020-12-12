---
title: Kopiuj dane z serwera i do SFTP
description: Dowiedz się, jak skopiować dane z serwera i do SFTP przy użyciu Azure Data Factory.
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
ms.date: 08/28/2020
ms.openlocfilehash: c1f49fffae091dd0d069c48cea75c3da40def645
ms.sourcegitcommit: fa807e40d729bf066b9b81c76a0e8c5b1c03b536
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/11/2020
ms.locfileid: "97346082"
---
# <a name="copy-data-from-and-to-the-sftp-server-by-using-azure-data-factory"></a>Skopiuj dane z i do serwera SFTP przy użyciu Azure Data Factory

> [!div class="op_single_selector" title1="Wybierz używaną wersję usługi Data Factory:"]
> * [Wersja 1](v1/data-factory-sftp-connector.md)
> * [Bieżąca wersja](connector-sftp.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule opisano sposób kopiowania danych z i do serwera Secure FTP (SFTP). Aby dowiedzieć się więcej na temat Azure Data Factory, Przeczytaj [artykuł wprowadzający](introduction.md).

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Łącznik SFTP jest obsługiwany dla następujących działań:

- [Działanie kopiowania](copy-activity-overview.md) z [obsługiwaną macierzą źródłową/ujścia](copy-activity-overview.md)
- [Działanie Lookup](control-flow-lookup-activity.md)
- [Działanie GetMetadata](control-flow-get-metadata-activity.md)
- [Usuń działanie](delete-activity.md)

W przypadku łącznika SFTP obsługiwane są następujące:

- Kopiowanie plików z i do serwera SFTP przy użyciu uwierzytelniania *podstawowego* lub *SshPublicKey* .
- Kopiowanie plików w postaci lub przez analizowanie lub generowanie plików z [obsługiwanymi formatami plików i kodekami kompresji](supported-file-formats-and-compression-codecs.md).

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Wprowadzenie

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje o właściwościach, które są używane do definiowania jednostek Data Factory specyficznych dla protokołu SFTP.

## <a name="linked-service-properties"></a>Właściwości połączonej usługi

Dla połączonej usługi SFTP są obsługiwane następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość Type musi mieć wartość *SFTP*. |Tak |
| host | Nazwa lub adres IP serwera SFTP. |Tak |
| port | Port, na którym nasłuchuje serwer SFTP.<br/>Dozwolona wartość jest liczbą całkowitą, a wartość domyślna to *22*. |Nie |
| skipHostKeyValidation | Określ, czy pominąć sprawdzanie poprawności klucza hosta.<br/>Dozwolone wartości to *true* i *false* (wartość domyślna).  | Nie |
| hostKeyFingerprint | Określ odcisk palca klucza hosta. | Tak, jeśli wartość "skipHostKeyValidation" jest ustawiona na wartość false.  |
| authenticationType | Określ typ uwierzytelniania.<br/>Dozwolone wartości to *Basic* i *SshPublicKey*. Aby uzyskać więcej właściwości, zobacz sekcję [Korzystanie z uwierzytelniania podstawowego](#use-basic-authentication) . Aby zapoznać się z przykładami JSON, zobacz sekcję [używanie uwierzytelniania publicznego klucza SSH](#use-ssh-public-key-authentication) . |Tak |
| Właściwością connectvia | [Środowisko Integration Runtime](concepts-integration-runtime.md) służy do nawiązywania połączenia z magazynem danych. Aby dowiedzieć się więcej, zobacz sekcję [wymagania wstępne](#prerequisites) . Jeśli środowisko Integration Runtime nie jest określone, usługa używa Azure Integration Runtime domyślnego. |Nie |

### <a name="use-basic-authentication"></a>Użyj uwierzytelniania podstawowego

Aby użyć uwierzytelniania podstawowego, należy ustawić właściwość *AuthenticationType* na wartość *podstawowa* i określić następujące właściwości oprócz właściwości ogólnych łącznika SFTP, które zostały wprowadzone w poprzedniej sekcji:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| userName | Użytkownik, który ma dostęp do serwera SFTP. |Tak |
| hasło | Hasło użytkownika (userName). Oznacz to pole jako element SecureString, aby bezpiecznie przechowywać go w fabryce danych, lub [odwoływać się do wpisu tajnego przechowywanego w magazynie kluczy platformy Azure](store-credentials-in-key-vault.md). | Tak |

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
            "referenceName": "<name of integration runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="use-ssh-public-key-authentication"></a>Użyj uwierzytelniania klucza publicznego SSH

Aby użyć uwierzytelniania klucza publicznego SSH, ustaw właściwość "AuthenticationType" jako **SshPublicKey**, a następnie określ następujące właściwości poza ogólnymi ŁĄCZNIKami SFTP, które zostały wprowadzone w ostatniej sekcji:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| userName | Użytkownik, który ma dostęp do serwera SFTP. |Tak |
| privateKeyPath | Określ ścieżkę bezwzględną do pliku klucza prywatnego, do którego może uzyskać dostęp środowisko Integration Runtime. Ma to zastosowanie tylko wtedy, gdy typ samodzielnego środowiska Integration Runtime jest określony w "właściwością connectvia". | Określ albo `privateKeyPath` `privateKeyContent` .  |
| privateKeyContent | Zawartość klucza prywatnego SSH szyfrowanego algorytmem Base64. Klucz prywatny SSH powinien mieć format OpenSSH. Oznacz to pole jako element SecureString, aby bezpiecznie przechowywać go w fabryce danych, lub [odwoływać się do wpisu tajnego przechowywanego w magazynie kluczy platformy Azure](store-credentials-in-key-vault.md). | Określ albo `privateKeyPath` `privateKeyContent` . |
| Danym | Określ frazę lub hasło do odszyfrowania klucza prywatnego, jeśli plik klucza lub zawartość klucza są chronione za pomocą frazy Pass. Oznacz to pole jako element SecureString, aby bezpiecznie przechowywać go w fabryce danych, lub [odwoływać się do wpisu tajnego przechowywanego w magazynie kluczy platformy Azure](store-credentials-in-key-vault.md). | Tak, jeśli plik klucza prywatnego lub zawartość klucza jest chroniona za pomocą frazy Pass. |

> [!NOTE]
> Łącznik SFTP obsługuje klucz OpenSSH RSA/DSA. Upewnij się, że zawartość pliku klucza rozpoczyna się od "-----BEGIN [RSA/DSA] klucza prywatnego-----". Jeśli plik klucza prywatnego jest plikiem PPK, użyj narzędzia do konwersji z PPK na OpenSSH format. 

**Przykład 1: uwierzytelnianie SshPublicKey przy użyciu ścieżki klucza prywatnego**

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
            "referenceName": "<name of integration runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Przykład 2: uwierzytelnianie SshPublicKey przy użyciu zawartości klucza prywatnego**

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
            "referenceName": "<name of integration runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Właściwości zestawu danych

Aby zapoznać się z pełną listą sekcji i właściwości, które są dostępne do definiowania zestawów danych, zobacz artykuł [zestawy danych](concepts-datasets-linked-services.md) . 

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Następujące właściwości są obsługiwane w przypadku protokołu SFTP w obszarze `location` Ustawienia w zestawie danych opartym na formacie:

| Właściwość   | Opis                                                  | Wymagane |
| ---------- | ------------------------------------------------------------ | -------- |
| typ       | Właściwość *Type* w `location` elemencie DataSet musi być ustawiona na wartość *SftpLocation*. | Tak      |
| folderPath | Ścieżka do folderu. Jeśli chcesz użyć symbolu wieloznacznego do filtrowania folderu, pomiń to ustawienie i określ ścieżkę w ustawieniach źródła działania. | Nie       |
| fileName   | Nazwa pliku pod określonym folderPath. Jeśli chcesz użyć symbolu wieloznacznego do filtrowania plików, pomiń to ustawienie i określ nazwę pliku w ustawieniach źródła działania. | Nie       |

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

Aby zapoznać się z pełną listą sekcji i właściwości, które są dostępne do definiowania działań, zobacz artykuł [potoki](concepts-pipelines-activities.md) . Ta sekcja zawiera listę właściwości, które są obsługiwane przez źródło SFTP.

### <a name="sftp-as-source"></a>SFTP jako źródło

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Następujące właściwości są obsługiwane w przypadku protokołu SFTP w `storeSettings` ustawieniach źródła kopii opartego na formacie:

| Właściwość                 | Opis                                                  | Wymagane                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| typ                     | Właściwość *Type* w obszarze `storeSettings` musi być ustawiona na wartość *SftpReadSettings*. | Tak                                           |
| ***Znajdź pliki do skopiowania** _ |  |  |
| Opcja 1: ścieżka statyczna<br> | Skopiuj ze ścieżki folderu/pliku określonego w zestawie danych. Jeśli chcesz skopiować wszystkie pliki z folderu, należy również określić `wildcardFileName` jako `_` . |  |
| Opcja 2: symbol wieloznaczny<br>- wildcardFolderPath | Ścieżka folderu z symbolami wieloznacznymi do filtrowania folderów źródłowych. <br>Dozwolone symbole wieloznaczne to `*` (dopasowuje zero lub więcej znaków) i `?` (dopasowuje zero lub jeden znak); Użyj `^` do ucieczki, jeśli rzeczywista nazwa folderu ma symbol wieloznaczny lub ten znak ucieczki wewnątrz. <br>Aby uzyskać więcej przykładów, zobacz [przykłady dotyczące folderów i plików](#folder-and-file-filter-examples). | Nie                                            |
| Opcja 2: symbol wieloznaczny<br>- wildcardFileName | Nazwa pliku z symbolami wieloznacznymi pod określonym folderPath/wildcardFolderPath do filtrowania plików źródłowych. <br>Dozwolone symbole wieloznaczne to `*` (dopasowuje zero lub więcej znaków) i `?` (dopasowuje zero lub jeden znak); Użyj `^` do ucieczki, jeśli rzeczywista nazwa pliku ma symbol wieloznaczny lub ten znak ucieczki wewnątrz.  Aby uzyskać więcej przykładów, zobacz [przykłady dotyczące folderów i plików](#folder-and-file-filter-examples). | Tak |
| Opcja 3: Lista plików<br>- fileListPath | Wskazuje na skopiowanie określonego zestawu plików. Wskaż plik tekstowy zawierający listę plików, które chcesz skopiować (jeden plik w wierszu, ze ścieżką względną do ścieżki skonfigurowanej w zestawie danych).<br/>Korzystając z tej opcji, nie należy określać nazwy pliku w zestawie danych. Aby uzyskać więcej przykładów, zobacz [przykłady plików z listą](#file-list-examples). |Nie |
| ***Ustawienia dodatkowe** _ |  | |
| rozpoznawania | Wskazuje, czy dane są odczytane cyklicznie z podfolderów, czy tylko z określonego folderu. Gdy wartość cykliczna jest ustawiona na wartość true, a ujścia jest magazynem opartym na plikach, pusty folder lub podfolder nie jest kopiowany ani tworzony w ujścia. <br>Dozwolone wartości to _true * (wartość domyślna) i *Fałsz*.<br>Ta właściwość nie ma zastosowania podczas konfigurowania `fileListPath` . |Nie |
| deleteFilesAfterCompletion | Wskazuje, czy pliki binarne zostaną usunięte z magazynu źródłowego po pomyślnym przeniesieniu do magazynu docelowego. Plik jest usuwany dla każdego pliku, więc w przypadku niepowodzenia działania kopiowania niektóre pliki zostały już skopiowane do lokalizacji docelowej i usunięte ze źródła, podczas gdy inne nadal pozostają w magazynie źródłowym. <br/>Ta właściwość jest prawidłowa tylko w scenariuszu kopiowania plików binarnych. Wartość domyślna: false. |Nie |
| modifiedDatetimeStart    | Pliki są filtrowane na podstawie *ostatnio modyfikowanego* atrybutu. <br>Pliki są wybierane, jeśli ich czas ostatniej modyfikacji mieści się w zakresie od `modifiedDatetimeStart` do `modifiedDatetimeEnd` . Czas jest stosowany do strefy czasowej UTC w formacie *2018 r-12-01T05:00:00Z*. <br> Właściwości mogą mieć wartość NULL, co oznacza, że żaden filtr atrybutu pliku nie jest stosowany do zestawu danych.  Gdy `modifiedDatetimeStart` ma wartość DateTime, ale `modifiedDatetimeEnd` jest wartością null, oznacza, że pliki, których ostatni zmodyfikowany atrybut jest większy lub równy wartości DateTime, są zaznaczone.  Gdy `modifiedDatetimeEnd` ma wartość DateTime, ale `modifiedDatetimeStart` jest wartością null, oznacza, że pliki, których ostatni zmodyfikowany atrybut jest mniejszy niż wartość DateTime, są zaznaczone.<br/>Ta właściwość nie ma zastosowania podczas konfigurowania `fileListPath` . | Nie                                            |
| modifiedDatetimeEnd      | Tak samo jak powyżej.                                               | Nie                                            |
| enablePartitionDiscovery | W przypadku plików, które są partycjonowane, określ, czy przeanalizować partycje ze ścieżki pliku i dodać je jako dodatkowe kolumny źródłowe.<br/>Dozwolone wartości to **false** (wartość domyślna) i **true**. | Nie                                            |
| partitionRootPath | Gdy odnajdywanie partycji jest włączone, określ bezwzględną ścieżkę katalogu głównego, aby odczytać partycjonowane foldery jako kolumny danych.<br/><br/>Jeśli nie jest określony, domyślnie,<br/>— Jeśli używasz ścieżki pliku w zestawie danych lub liście plików w źródle, ścieżka katalogu głównego partycji jest ścieżką skonfigurowaną w zestawie danych.<br/>— Jeśli używasz wieloznacznego filtru folderów, ścieżka katalogu głównego partycji jest ścieżką podrzędną przed pierwszym symbolem wieloznacznym.<br/><br/>Na przykład przy założeniu, że ścieżka w zestawie danych jest konfigurowana jako "root/folder/Year = 2020/miesiąc = 08/Day = 27":<br/>— W przypadku określenia ścieżki katalogu głównego partycji jako "root/folder/Year = 2020" działanie Copy (kopiowanie) wygeneruje dwie kolejne kolumny `month` i `day` wartości "08" i "27" (oprócz kolumn wewnątrz plików).<br/>— Jeśli ścieżka katalogu głównego partycji nie zostanie określona, nie zostanie wygenerowane żadne dodatkowe kolumny. | Nie                                            |
| maxConcurrentConnections | Liczba połączeń, które mogą łączyć się z magazynem magazynu współbieżnie. Określ wartość tylko wtedy, gdy chcesz ograniczyć współbieżne połączenie z magazynem danych. | Nie                                            |

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

### <a name="sftp-as-a-sink"></a>SFTP jako ujścia

[!INCLUDE [data-factory-v2-file-sink-formats](../../includes/data-factory-v2-file-sink-formats.md)]

Następujące właściwości są obsługiwane w przypadku protokołu SFTP w obszarze `storeSettings` Ustawienia w ujściach kopiowania opartych na formacie:

| Właściwość                 | Opis                                                  | Wymagane |
| ------------------------ | ------------------------------------------------------------ | -------- |
| typ                     | Właściwość *Type* w obszarze `storeSettings` musi być ustawiona na wartość *SftpWriteSettings*. | Tak      |
| copyBehavior             | Definiuje zachowanie kopiowania, gdy źródłem są pliki z magazynu danych opartego na plikach.<br/><br/>Dozwolone wartości to:<br/><b>-PreserveHierarchy (domyślnie)</b>: zachowuje hierarchię plików w folderze docelowym. Ścieżka względna pliku źródłowego do folderu źródłowego jest taka sama jak ścieżka względna pliku docelowego do folderu docelowego.<br/><b>-FlattenHierarchy</b>: wszystkie pliki z folderu źródłowego znajdują się na pierwszym poziomie folderu docelowego. Pliki docelowe mają automatycznie generowane nazwy. <br/><b>-MergeFiles</b>: Scala wszystkie pliki z folderu źródłowego do jednego pliku. Jeśli nazwa pliku jest określona, scalona nazwa pliku jest podaną nazwą. W przeciwnym razie jest to automatycznie wygenerowana nazwa pliku. | Nie       |
| maxConcurrentConnections | Liczba połączeń, które mogą łączyć się z magazynem magazynu współbieżnie. Określ wartość tylko wtedy, gdy chcesz ograniczyć współbieżne połączenie z magazynem danych. | Nie       |
| useTempFileRename | Wskaż, czy przekazywać do plików tymczasowych i zmieniać ich nazwy, czy bezpośrednio zapisywać do folderu docelowego lub lokalizacji plików. Domyślnie Azure Data Factory pierwsze zapis do plików tymczasowych, a następnie zmienia nazwy po zakończeniu przekazywania. Ta sekwencja pomaga (1) uniknąć konfliktów, które mogą spowodować uszkodzenie pliku, jeśli istnieją inne procesy zapisujące do tego samego pliku, a (2) Upewnij się, że w trakcie transferu istnieje oryginalna wersja pliku. Jeśli serwer SFTP nie obsługuje operacji zmiany nazwy, wyłącz tę opcję i upewnij się, że nie masz współbieżnego zapisu w pliku docelowym. Aby uzyskać więcej informacji, zobacz Wskazówki dotyczące rozwiązywania problemów na końcu tej tabeli. | Nie. Wartość domyślna to *true*. |
| operationTimeout | Czas oczekiwania przed upływem limitu czasu dla każdego żądania zapisu na serwer SFTP. Wartość domyślna to 60 min (01:00:00).|Nie |

>[!TIP]
>Jeśli zostanie wyświetlony komunikat o błędzie "UserErrorSftpPathNotFound", "UserErrorSftpPermissionDenied" lub "SftpOperationFail" podczas zapisywania danych w SFTP, *a używany użytkownik SFTP ma odpowiednie* uprawnienia, sprawdź, czy serwer SFTP obsługujący operację zmiany nazwy pliku jest w działaniu. Jeśli tak nie jest, wyłącz opcję **Przekaż z plikiem tymczasowym** ( `useTempFileRename` ) i spróbuj ponownie. Aby dowiedzieć się więcej na temat tej właściwości, zobacz poprzednią tabelę. W przypadku korzystania z własnego środowiska Integration Runtime dla działania kopiowania należy pamiętać, aby użyć wersji 4,6 lub nowszej.

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

W tej sekcji opisano zachowanie, które wynika z używania filtrów symboli wieloznacznych z ścieżkami folderów i nazwami plików.

| folderPath | fileName | rozpoznawania | Źródłowa Struktura folderu i wynik filtru (pliki **pogrubione** są pobierane)|
|:--- |:--- |:--- |:--- |
| `Folder*` | (puste, Użyj domyślnego) | fałsz | Folder<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.jsna**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.jsna<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | (puste, Użyj domyślnego) | true | Folder<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.jsna**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.jsna**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | fałsz | Folder<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.jsna<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.jsna<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | true | Folder<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.jsna<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.jsna<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="file-list-examples"></a>Przykłady listy plików

W tej tabeli opisano zachowanie wynikające z używania ścieżki listy plików w źródle działania kopiowania. Przyjęto założenie, że masz następującą źródłową strukturę folderów i chcesz skopiować pliki, które są pogrubione:

| Przykładowa struktura źródła                                      | Zawartość w FileListToCopy.txt                             | Konfiguracja Azure Data Factory                                            |
| ------------------------------------------------------------ | --------------------------------------------------------- | ------------------------------------------------------------ |
| root<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.jsna<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.jsna<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Metadane<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;FileListToCopy.txt | File1.csv<br>Subfolder1/File3.csv<br>Subfolder1/File5.csv | **W zestawie danych:**<br>-Ścieżka folderu: `root/FolderA`<br><br>**W źródle działania kopiowania:**<br>-Ścieżka listy plików: `root/Metadata/FileListToCopy.txt` <br><br>Ścieżka listy plików wskazuje plik tekstowy w tym samym magazynie danych, który zawiera listę plików, które mają zostać skopiowane (jeden plik w wierszu, ze ścieżką względną do ścieżki skonfigurowanej w zestawie danych). |

## <a name="lookup-activity-properties"></a>Właściwości działania Lookup

Aby uzyskać informacje o właściwościach działania wyszukiwania, zobacz [aktywność Lookup w Azure Data Factory](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Właściwości działania GetMetadata

Aby uzyskać informacje o właściwościach działania GetMetadata, zobacz [GetMetadata Activity in Azure Data Factory](control-flow-get-metadata-activity.md). 

## <a name="delete-activity-properties"></a>Usuń właściwości działania

Informacje o usuwaniu właściwości działania znajdują się [w temacie Usuwanie działania w Azure Data Factory](delete-activity.md).

## <a name="legacy-models"></a>Starsze modele

>[!NOTE]
>Następujące modele są nadal obsługiwane w celu zapewnienia zgodności z poprzednimi wersjami. Zalecamy użycie wcześniej omówionego nowego modelu, ponieważ interfejs użytkownika tworzenia Azure Data Factory został przełączony w celu wygenerowania nowego modelu.

### <a name="legacy-dataset-model"></a>Model starszego zestawu danych

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość *Type* zestawu danych musi być ustawiona na wartość *udziału*. |Tak |
| folderPath | Ścieżka do folderu. Obsługiwany jest filtr symboli wieloznacznych. Dozwolone symbole wieloznaczne to `*` (dopasowuje zero lub więcej znaków) i `?` (dopasowuje zero lub jeden znak); Użyj `^` do ucieczki, jeśli rzeczywista nazwa pliku ma symbol wieloznaczny lub ten znak ucieczki wewnątrz. <br/><br/>Przykłady: RootFolder/subfolder/, Zobacz więcej przykładów w [przykładach folderów i filtrów plików](#folder-and-file-filter-examples). |Tak |
| fileName |  **Nazwa lub filtr symboli wieloznacznych** dla plików pod określonym "folderPath". Jeśli nie określisz wartości dla tej właściwości, zestaw danych wskazuje wszystkie pliki w folderze. <br/><br/>W przypadku filtru dozwolone symbole wieloznaczne to `*` (dopasowuje zero lub więcej znaków) i `?` (dopasowuje zero lub jeden znak).<br/>-Przykład 1: `"fileName": "*.csv"`<br/>-Przykład 2: `"fileName": "???20180427.txt"`<br/>Użyj `^` do ucieczki, jeśli rzeczywista nazwa folderu ma symbol wieloznaczny lub ten znak ucieczki wewnątrz. |Nie |
| modifiedDatetimeStart | Pliki są filtrowane na podstawie *ostatnio modyfikowanego* atrybutu. Pliki są wybierane, jeśli ich czas ostatniej modyfikacji mieści się w zakresie od `modifiedDatetimeStart` do `modifiedDatetimeEnd` . Czas jest stosowany do strefy czasowej UTC w formacie *2018 r-12-01T05:00:00Z*. <br/><br/> Włączenie tego ustawienia będzie miało wpływ na ogólną wydajność przenoszenia danych, co pozwala na filtrowanie plików z dużej liczby plików. <br/><br/> Właściwości mogą mieć wartość NULL, co oznacza, że żaden filtr atrybutu pliku nie jest stosowany do zestawu danych.  Gdy `modifiedDatetimeStart` ma wartość DateTime, ale `modifiedDatetimeEnd` jest wartością null, oznacza, że pliki, których ostatni zmodyfikowany atrybut jest większy lub równy wartości DateTime, są zaznaczone.  Gdy `modifiedDatetimeEnd` ma wartość DateTime, ale `modifiedDatetimeStart` jest wartością null, oznacza, że pliki, których ostatni zmodyfikowany atrybut jest mniejszy niż wartość DateTime, są zaznaczone.| Nie |
| modifiedDatetimeEnd | Pliki są filtrowane na podstawie *ostatnio modyfikowanego* atrybutu. Pliki są wybierane, jeśli ich czas ostatniej modyfikacji mieści się w zakresie od `modifiedDatetimeStart` do `modifiedDatetimeEnd` . Czas jest stosowany do strefy czasowej UTC w formacie *2018 r-12-01T05:00:00Z*. <br/><br/> Włączenie tego ustawienia będzie miało wpływ na ogólną wydajność przenoszenia danych, co pozwala na filtrowanie plików z dużej liczby plików. <br/><br/> Właściwości mogą mieć wartość NULL, co oznacza, że żaden filtr atrybutu pliku nie jest stosowany do zestawu danych.  Gdy `modifiedDatetimeStart` ma wartość DateTime, ale `modifiedDatetimeEnd` jest wartością null, oznacza, że pliki, których ostatni zmodyfikowany atrybut jest większy lub równy wartości DateTime, są zaznaczone.  Gdy `modifiedDatetimeEnd` ma wartość DateTime, ale `modifiedDatetimeStart` jest wartością null, oznacza, że pliki, których ostatni zmodyfikowany atrybut jest mniejszy niż wartość DateTime, są zaznaczone.| Nie |
| format | Jeśli chcesz skopiować pliki w postaci między magazynami opartymi na plikach (kopia binarna), Pomiń sekcję format w definicjach zestawu danych wejściowych i wyjściowych.<br/><br/>Jeśli chcesz analizować pliki o określonym formacie, obsługiwane są następujące typy formatu plików: *TextFormat*, *formatu jsonformat*, *AvroFormat*, *OrcFormat* i *ParquetFormat*. Ustaw właściwość *Type* w polu Format na jedną z tych wartości. Aby uzyskać więcej informacji, zobacz [format tekstowy](supported-file-formats-and-compression-codecs-legacy.md#text-format), [Format JSON](supported-file-formats-and-compression-codecs-legacy.md#json-format), [Format Avro](supported-file-formats-and-compression-codecs-legacy.md#avro-format), [Format Orc](supported-file-formats-and-compression-codecs-legacy.md#orc-format)i sekcje [formatu Parquet](supported-file-formats-and-compression-codecs-legacy.md#parquet-format) . |Nie (tylko w przypadku scenariusza kopiowania binarnego) |
| kompresja | Określ typ i poziom kompresji danych. Aby uzyskać więcej informacji, zobacz [obsługiwane formaty plików i kodery-dekoder kompresji](supported-file-formats-and-compression-codecs-legacy.md#compression-support).<br/>Obsługiwane typy to *gzip*, *Wklęśnięcie*, *BZip2* i *ZipDeflate*.<br/>Obsługiwane poziomy są *optymalne* i *najszybciej*. |Nie |

>[!TIP]
>Aby skopiować wszystkie pliki w folderze, określ tylko *folderPath* .<br>Aby skopiować pojedynczy plik o określonej nazwie, należy określić *folderPath* z częścią *folderu i nazwą pliku o* nazwie.<br>Aby skopiować podzbiór plików w folderze, należy określić *folderPath* z częścią folderu i *nazwą pliku* z filtrem symboli wieloznacznych.

>[!NOTE]
>Jeśli używasz właściwości *FileFilter* dla filtru plików, nadal jest to obsługiwane, ale zalecamy używanie nowej funkcji filtrowania dodanej do *pliku* od teraz.

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

### <a name="legacy-copy-activity-source-model"></a>Model źródłowy starszego działania kopiowania

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość *Type* źródła działania Copy musi być ustawiona na wartość *FileSystemSource* |Tak |
| rozpoznawania | Wskazuje, czy dane są odczytane cyklicznie z podfolderów, czy tylko z określonego folderu. Gdy wartość cykliczna jest ustawiona na *wartość true* , a ujścia jest magazynem opartym na plikach, puste foldery i podfoldery nie będą kopiowane ani tworzone w ujściach.<br/>Dozwolone wartości to *true* (wartość domyślna) i *Fałsz* | Nie |
| maxConcurrentConnections | Liczba połączeń, które mogą łączyć się z magazynem magazynu jednocześnie. Określ liczbę tylko wtedy, gdy chcesz ograniczyć współbieżne połączenia z magazynem danych. | Nie |

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
Listę magazynów danych obsługiwanych jako źródła i ujścia przez działanie kopiowania w Azure Data Factory można znaleźć w temacie [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
