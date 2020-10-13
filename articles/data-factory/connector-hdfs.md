---
title: Kopiowanie danych z systemu plików HDFS przy użyciu Azure Data Factory
description: Informacje o kopiowaniu danych z chmury lub lokalnego źródła systemu plików HDFS do obsługiwanych magazynów danych ujścia przy użyciu działania kopiowania w potoku Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/28/2020
ms.author: jingwang
ms.openlocfilehash: 96603de7014419b142cc35714b891f9e4b15ec99
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91405089"
---
# <a name="copy-data-from-the-hdfs-server-by-using-azure-data-factory"></a>Kopiowanie danych z serwera HDFS przy użyciu Azure Data Factory

> [!div class="op_single_selector" title1="Wybierz używaną wersję usługi Data Factory:"]
> * [Wersja 1](v1/data-factory-hdfs-connector.md)
> * [Bieżąca wersja](connector-hdfs.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule opisano sposób kopiowania danych z serwera usługi Hadoop rozproszony system plików (HDFS). Aby dowiedzieć się więcej na temat Azure Data Factory, Przeczytaj [artykuł wprowadzający](introduction.md).

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Łącznik HDFS jest obsługiwany dla następujących działań:

- [Działanie kopiowania](copy-activity-overview.md) z [obsługiwaną macierzą źródłową i ujścia](copy-activity-overview.md)
- [Działanie Lookup](control-flow-lookup-activity.md)
- [Usuń działanie](delete-activity.md)

W ramach tego łącznika HDFS obsługuje:

- Kopiowanie plików przy użyciu *systemu Windows* (Kerberos) lub uwierzytelniania *anonimowego* .
- Kopiowanie plików przy użyciu protokołu *webhdfs* lub *wbudowanej obsługi pomocą distcp* .
- Kopiowanie plików w postaci lub przez analizowanie lub generowanie plików z [obsługiwanymi formatami plików i kodekami kompresji](supported-file-formats-and-compression-codecs.md).

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

> [!NOTE]
> Upewnij się, że środowisko Integration Runtime ma dostęp do *wszystkich* elementów [nazwa węzła Server]: [nazwa węzła port] i [serwery węzłów danych]: [port węzła danych] klastra Hadoop. Domyślną wartością [nazwa portu węzła] jest 50070, a domyślnym [port węzła danych] jest 50075.

## <a name="get-started"></a>Rozpoczęcie pracy

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje dotyczące właściwości, które są używane do definiowania jednostek Data Factory specyficznych dla systemu plików HDFS.

## <a name="linked-service-properties"></a>Właściwości połączonej usługi

Dla połączonej usługi HDFS są obsługiwane następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość *Type* musi być ustawiona na system plików *HDFS*. | Tak |
| url |Adres URL systemu plików HDFS |Tak |
| authenticationType | Dozwolone wartości to *anonimowe* lub *Windows*. <br><br> Aby skonfigurować środowisko lokalne, zobacz sekcję [używanie uwierzytelniania Kerberos w przypadku łącznika HDFS](#use-kerberos-authentication-for-the-hdfs-connector) . |Tak |
| userName |Nazwa użytkownika dla uwierzytelniania systemu Windows. W przypadku uwierzytelniania Kerberos Określ ** \<username> @ \<domain> . com**. |Tak (w przypadku uwierzytelniania systemu Windows) |
| hasło |Hasło do uwierzytelniania systemu Windows. Oznacz to pole jako element SecureString, aby bezpiecznie przechowywać go w fabryce danych, lub [odwoływać się do wpisu tajnego przechowywanego w magazynie kluczy platformy Azure](store-credentials-in-key-vault.md). |Tak (w przypadku uwierzytelniania systemu Windows) |
| Właściwością connectvia | [Środowisko Integration Runtime](concepts-integration-runtime.md) służy do nawiązywania połączenia z magazynem danych. Aby dowiedzieć się więcej, zobacz sekcję [wymagania wstępne](#prerequisites) . Jeśli środowisko Integration Runtime nie jest określone, usługa używa Azure Integration Runtime domyślnego. |Nie |

**Przykład: używanie uwierzytelniania anonimowego**

```json
{
    "name": "HDFSLinkedService",
    "properties": {
        "type": "Hdfs",
        "typeProperties": {
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "authenticationType": "Anonymous",
            "userName": "hadoop"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Przykład: używanie uwierzytelniania systemu Windows**

```json
{
    "name": "HDFSLinkedService",
    "properties": {
        "type": "Hdfs",
        "typeProperties": {
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "authenticationType": "Windows",
            "userName": "<username>@<domain>.com (for Kerberos auth)",
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

## <a name="dataset-properties"></a>Właściwości zestawu danych

Aby zapoznać się z pełną listą sekcji i właściwości, które są dostępne do definiowania zestawów danych, zobacz [zestawy danych w Azure Data Factory](concepts-datasets-linked-services.md). 

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Następujące właściwości są obsługiwane w systemie plików HDFS w obszarze `location` Ustawienia w zestawie danych opartym na formacie:

| Właściwość   | Opis                                                  | Wymagane |
| ---------- | ------------------------------------------------------------ | -------- |
| typ       | Właściwość *typu* w `location` elemencie DataSet musi być ustawiona na *HdfsLocation*. | Tak      |
| folderPath | Ścieżka do folderu. Jeśli chcesz użyć symbolu wieloznacznego do filtrowania folderu, pomiń to ustawienie i określ ścieżkę w ustawieniach źródła działania. | Nie       |
| fileName   | Nazwa pliku pod określonym folderPath. Jeśli chcesz użyć symbolu wieloznacznego do filtrowania plików, pomiń to ustawienie i określ nazwę pliku w ustawieniach źródła działania. | Nie       |

**Przykład:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<HDFS linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "HdfsLocation",
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

Aby zapoznać się z pełną listą sekcji i właściwości, które są dostępne do definiowania działań, zobacz [potoki i działania w Azure Data Factory](concepts-pipelines-activities.md). Ta sekcja zawiera listę właściwości, które są obsługiwane przez źródło systemu plików HDFS.

### <a name="hdfs-as-source"></a>System plików HDFS jako źródło

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Następujące właściwości są obsługiwane dla systemu plików HDFS w obszarze `storeSettings` Ustawienia w źródle kopiowania na podstawie formatu:

| Właściwość                 | Opis                                                  | Wymagane                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| typ                     | Właściwość *Type* w obszarze `storeSettings` musi być ustawiona na wartość **HdfsReadSettings**. | Tak                                           |
| ***Znajdź pliki do skopiowania*** |  |  |
| Opcja 1: ścieżka statyczna<br> | Skopiuj z folderu lub ścieżki pliku określonej w zestawie danych. Jeśli chcesz skopiować wszystkie pliki z folderu, należy również określić `wildcardFileName` jako `*` . |  |
| Opcja 2: symbol wieloznaczny<br>- wildcardFolderPath | Ścieżka folderu z symbolami wieloznacznymi do filtrowania folderów źródłowych. <br>Dozwolone symbole wieloznaczne to: `*` (dopasowuje zero lub więcej znaków) i `?` (dopasowuje zero lub pojedynczy znak). Użyj `^` do wyjścia, jeśli rzeczywista nazwa folderu ma symbol wieloznaczny lub ten znak ucieczki wewnątrz. <br>Aby uzyskać więcej przykładów, zobacz [przykłady dotyczące folderów i plików](#folder-and-file-filter-examples). | Nie                                            |
| Opcja 2: symbol wieloznaczny<br>- wildcardFileName | Nazwa pliku z symbolami wieloznacznymi pod określonym folderPath/wildcardFolderPath do filtrowania plików źródłowych. <br>Dozwolone symbole wieloznaczne to: `*` (dopasowuje zero lub więcej znaków) i `?` (dopasowuje zero lub pojedynczy znak); Użyj `^` do ucieczki, jeśli rzeczywista nazwa folderu ma symbol wieloznaczny lub ten znak ucieczki wewnątrz.  Aby uzyskać więcej przykładów, zobacz [przykłady dotyczące folderów i plików](#folder-and-file-filter-examples). | Tak |
| Opcja 3: Lista plików<br>- fileListPath | Wskazuje na skopiowanie określonego zestawu plików. Wskaż plik tekstowy zawierający listę plików, które chcesz skopiować (jeden plik w wierszu, ze ścieżką względną do ścieżki skonfigurowanej w zestawie danych).<br/>Korzystając z tej opcji, nie należy określać nazwy pliku w zestawie danych. Aby uzyskać więcej przykładów, zobacz [przykłady plików z listą](#file-list-examples). |Nie |
| ***Ustawienia dodatkowe*** |  | |
| rozpoznawania | Wskazuje, czy dane są odczytane cyklicznie z podfolderów, czy tylko z określonego folderu. Gdy `recursive` ma wartość *true* , a ujścia jest magazynem opartym na plikach, pusty folder lub podfolder nie jest kopiowany ani tworzony w ujścia. <br>Dozwolone wartości to *true* (wartość domyślna) i *false*.<br>Ta właściwość nie ma zastosowania podczas konfigurowania `fileListPath` . |Nie |
| deleteFilesAfterCompletion | Wskazuje, czy pliki binarne zostaną usunięte z magazynu źródłowego po pomyślnym przeniesieniu do magazynu docelowego. Plik jest usuwany dla każdego pliku, więc w przypadku niepowodzenia działania kopiowania niektóre pliki zostały już skopiowane do lokalizacji docelowej i usunięte ze źródła, podczas gdy inne nadal pozostają w magazynie źródłowym. <br/>Ta właściwość jest prawidłowa tylko w scenariuszu kopiowania plików binarnych. Wartość domyślna: false. |Nie |
| modifiedDatetimeStart    | Pliki są filtrowane na podstawie *ostatnio modyfikowanego*atrybutu. <br>Pliki są wybierane, jeśli ich czas ostatniej modyfikacji mieści się w zakresie od `modifiedDatetimeStart` do `modifiedDatetimeEnd` . Czas jest stosowany do strefy czasowej UTC w formacie *2018 r-12-01T05:00:00Z*. <br> Właściwości mogą mieć wartość NULL, co oznacza, że żaden filtr atrybutu pliku nie jest stosowany do zestawu danych.  Gdy `modifiedDatetimeStart` ma wartość DateTime, ale `modifiedDatetimeEnd` jest wartością null, oznacza, że pliki, których ostatni zmodyfikowany atrybut jest większy lub równy wartości DateTime, są zaznaczone.  Gdy `modifiedDatetimeEnd` ma wartość DateTime, ale `modifiedDatetimeStart` jest wartością null, oznacza, że pliki, których ostatni zmodyfikowany atrybut jest mniejszy niż wartość DateTime, są zaznaczone.<br/>Ta właściwość nie ma zastosowania podczas konfigurowania `fileListPath` . | Nie                                            |
| modifiedDatetimeEnd      | Tak samo jak powyżej.  
| enablePartitionDiscovery | W przypadku plików, które są partycjonowane, określ, czy przeanalizować partycje ze ścieżki pliku i dodać je jako dodatkowe kolumny źródłowe.<br/>Dozwolone wartości to **false** (wartość domyślna) i **true**. | Nie                                            |
| partitionRootPath | Gdy odnajdywanie partycji jest włączone, określ bezwzględną ścieżkę katalogu głównego, aby odczytać partycjonowane foldery jako kolumny danych.<br/><br/>Jeśli nie jest określony, domyślnie,<br/>— Jeśli używasz ścieżki pliku w zestawie danych lub liście plików w źródle, ścieżka katalogu głównego partycji jest ścieżką skonfigurowaną w zestawie danych.<br/>— Jeśli używasz wieloznacznego filtru folderów, ścieżka katalogu głównego partycji jest ścieżką podrzędną przed pierwszym symbolem wieloznacznym.<br/><br/>Na przykład przy założeniu, że ścieżka w zestawie danych jest konfigurowana jako "root/folder/Year = 2020/miesiąc = 08/Day = 27":<br/>— W przypadku określenia ścieżki katalogu głównego partycji jako "root/folder/Year = 2020" działanie Copy (kopiowanie) wygeneruje dwie kolejne kolumny `month` i `day` wartości "08" i "27" (oprócz kolumn wewnątrz plików).<br/>— Jeśli ścieżka katalogu głównego partycji nie zostanie określona, nie zostanie wygenerowane żadne dodatkowe kolumny. | Nie                                            |
| maxConcurrentConnections | Liczba połączeń, które mogą łączyć się z magazynem magazynu współbieżnie. Określ wartość tylko wtedy, gdy chcesz ograniczyć współbieżne połączenie z magazynem danych. | Nie                                            |
| ***Ustawienia pomocą distcp*** |  | |
| distcpSettings | Grupa właściwości, która ma być używana podczas korzystania z systemu HDFS pomocą distcp. | Nie |
| resourceManagerEndpoint | Nowy punkt końcowy PRZĘDZy | Tak, jeśli używasz pomocą distcp |
| tempScriptPath | Ścieżka folderu, która jest używana do przechowywania tymczasowego skryptu poleceń pomocą distcp. Plik skryptu jest generowany przez Data Factory i zostanie usunięty po zakończeniu zadania kopiowania. | Tak, jeśli używasz pomocą distcp |
| distcpOptions | Dodatkowe opcje dostępne dla polecenia pomocą distcp. | Nie |

**Przykład:**

```json
"activities":[
    {
        "name": "CopyFromHDFS",
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
                    "type": "HdfsReadSettings",
                    "recursive": true,
                    "distcpSettings": {
                        "resourceManagerEndpoint": "resourcemanagerendpoint:8088",
                        "tempScriptPath": "/usr/hadoop/tempscript",
                        "distcpOptions": "-m 100"
                    }
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="folder-and-file-filter-examples"></a>Przykłady filtrów folderów i plików

W tej sekcji opisano zachowanie, jeśli używasz filtru symboli wieloznacznych z ścieżką do folderu i nazwą pliku.

| folderPath | fileName             | rozpoznawania | Źródłowa Struktura folderu i wynik filtru (pliki **pogrubione** są pobierane) |
| :--------- | :------------------- | :-------- | :----------------------------------------------------------- |
| `Folder*`  | (puste, Użyj domyślnego) | fałsz     | Folder<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.jsna**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.jsna<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*`  | (puste, Użyj domyślnego) | true      | Folder<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.jsna**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.jsna**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*`  | `*.csv`              | fałsz     | Folder<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.jsna<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.jsna<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*`  | `*.csv`              | true      | Folder<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.jsna<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.jsna<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="file-list-examples"></a>Przykłady listy plików

W tej sekcji opisano zachowanie wynikające z używania ścieżki listy plików w źródle działania kopiowania. Przyjęto założenie, że masz następującą źródłową strukturę folderów i chcesz skopiować pliki, które są pogrubione:

| Przykładowa struktura źródła                                      | Zawartość w FileListToCopy.txt                             | Konfiguracja Azure Data Factory                                            |
| ------------------------------------------------------------ | --------------------------------------------------------- | ------------------------------------------------------------ |
| root<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.jsna<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.jsna<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Metadane<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;FileListToCopy.txt | File1.csv<br>Subfolder1/File3.csv<br>Subfolder1/File5.csv | **W zestawie danych:**<br>-Ścieżka folderu: `root/FolderA`<br><br>**W źródle działania kopiowania:**<br>-Ścieżka listy plików: `root/Metadata/FileListToCopy.txt` <br><br>Ścieżka listy plików wskazuje plik tekstowy w tym samym magazynie danych, który zawiera listę plików, które mają zostać skopiowane (jeden plik w wierszu, ze ścieżką względną do ścieżki skonfigurowanej w zestawie danych). |

## <a name="use-distcp-to-copy-data-from-hdfs"></a>Używanie pomocą distcp do kopiowania danych z systemu plików HDFS

[Pomocą distcp](https://hadoop.apache.org/docs/current3/hadoop-distcp/DistCp.html) to natywne narzędzie wiersza polecenia usługi Hadoop umożliwiające wykonywanie kopii rozproszonej w klastrze usługi Hadoop. Po uruchomieniu polecenia w pomocą distcp, najpierw wyświetla wszystkie pliki do skopiowania, a następnie tworzy kilka zadań mapy w klastrze usługi Hadoop. Każde zadanie mapy wykonuje kopię binarną ze źródła do ujścia.

Działanie kopiowania obsługuje używanie pomocą distcp do kopiowania plików jako do magazynu obiektów blob platformy Azure (łącznie z [przygotowaną kopią](copy-activity-performance.md)) lub usługi Azure Data Lake Store. W takim przypadku pomocą distcp może korzystać z mocy tego klastra, zamiast uruchamiać go w środowisku Integration Runtime. Korzystanie z usługi pomocą distcp zapewnia lepszą przepływność kopiowania, zwłaszcza jeśli klaster jest bardzo wydajny. W oparciu o konfigurację w fabryce danych, działanie kopiowania automatycznie konstruuje polecenie pomocą distcp, przesyła je do klastra usługi Hadoop i monitoruje stan kopiowania.

### <a name="prerequisites"></a>Wymagania wstępne

Aby użyć pomocą distcp do kopiowania plików z programu z systemu plików HDFS do magazynu obiektów blob platformy Azure (w tym kopii przygotowanej) lub usługi Azure Data Lake Store, upewnij się, że klaster Hadoop spełnia następujące wymagania:

* Usługi MapReduce i PRZĘDZy są włączone.  
* Wersja PRZĘDZy to 2,5 lub nowsza.  
* Serwer HDFS jest zintegrowany z docelowym magazynem danych: **Azure Blob Storage** lub **Azure Data Lake Store (ADLS Gen1)**: 

    - System plików obiektów blob platformy Azure jest natywnie obsługiwany od wersji Hadoop 2,7. Wystarczy określić ścieżkę JAR w konfiguracji środowiska Hadoop.
    - System plików Azure Data Lake Store jest spakowany od platformy Hadoop 3.0.0-alpha1. Jeśli wersja klastra usługi Hadoop jest wcześniejsza niż ta wersja, należy ręcznie zaimportować pakiety JAR powiązane z Azure Data Lake Store (Azure-datalake-Store. jar) do klastra z tego [miejsca](https://hadoop.apache.org/releases.html)i określić ścieżkę pliku JAR w konfiguracji środowiska Hadoop.

* Przygotuj folder tymczasowy w systemie plików HDFS. Ten folder tymczasowy służy do przechowywania skryptu powłoki pomocą distcp, dzięki czemu będzie zajmował przestrzeń na poziomie KB.
* Upewnij się, że konto użytkownika, które jest podane w połączonej usłudze HDFS, ma uprawnienia do:
   * Przesyłanie aplikacji w PRZĘDZe.
   * Utwórz podfolder i pliki do odczytu/zapisu w folderze temp.

### <a name="configurations"></a>Konfiguracje

Aby zapoznać się z konfiguracjami i przykładami związanymi z pomocą distcp, przejdź do sekcji system plików [HDFS jako źródło](#hdfs-as-source) .

## <a name="use-kerberos-authentication-for-the-hdfs-connector"></a>Używanie uwierzytelniania Kerberos dla łącznika HDFS

Dostępne są dwie opcje konfigurowania środowiska lokalnego do korzystania z uwierzytelniania Kerberos dla łącznika systemu plików HDFS. Możesz wybrać ten, który lepiej pasuje do Twojej sytuacji.
* Opcja 1: [dołączanie do samodzielnej maszyny Integration Runtime w obszarze Kerberos](#kerberos-join-realm)
* Opcja 2: [Włącz wzajemne zaufanie między domeną systemu Windows i obszarem Kerberos](#kerberos-mutual-trust)

Dla każdej opcji upewnij się, że webhdfs dla klastra Hadoop:

1. Utwórz podmiot HTTP i plik KEYTAB dla webhdfs.

    > [!IMPORTANT]
    > Podmiot zabezpieczeń protokołu HTTP Kerberos musi rozpoczynać się od "**http/**" zgodnie ze SPECYFIKACJą SPNEGO protokołu Kerberos protokołu HTTP.

    ```bash
    Kadmin> addprinc -randkey HTTP/<namenode hostname>@<REALM.COM>
    Kadmin> ktadd -k /etc/security/keytab/spnego.service.keytab HTTP/<namenode hostname>@<REALM.COM>
    ```

2. Opcje konfiguracji systemu plików HDFS: Dodaj następujące trzy właściwości w temacie `hdfs-site.xml` .
    ```xml
    <property>
        <name>dfs.webhdfs.enabled</name>
        <value>true</value>
    </property>
    <property>
        <name>dfs.web.authentication.kerberos.principal</name>
        <value>HTTP/_HOST@<REALM.COM></value>
    </property>
    <property>
        <name>dfs.web.authentication.kerberos.keytab</name>
        <value>/etc/security/keytab/spnego.service.keytab</value>
    </property>
    ```

### <a name="option-1-join-a-self-hosted-integration-runtime-machine-in-the-kerberos-realm"></a><a name="kerberos-join-realm"></a>Opcja 1: dołączanie do samodzielnej maszyny Integration Runtime w obszarze Kerberos

#### <a name="requirements"></a>Wymagania

* Samodzielna maszyna Integration Runtime musi przyłączyć obszar Kerberos i nie może dołączać do żadnej domeny systemu Windows.

#### <a name="how-to-configure"></a>Jak skonfigurować

**Na serwerze KDC:**

Utwórz podmiot zabezpieczeń do Azure Data Factory do użycia i określ hasło.

> [!IMPORTANT]
> Nazwa użytkownika nie powinna zawierać nazwy hosta.

```bash
Kadmin> addprinc <username>@<REALM.COM>
```

**Na komputerze, na którym działa środowisko Integration Runtime:**

1.  Uruchom narzędzie Ksetup, aby skonfigurować serwer i obszar protokołu Kerberos centrum dystrybucji kluczy (KDC).

    Komputer musi być skonfigurowany jako członek grupy roboczej, ponieważ obszar protokołu Kerberos różni się od domeny systemu Windows. Tę konfigurację można osiągnąć przez ustawienie obszaru protokołu Kerberos i dodanie serwera z centrum dystrybucji kluczy, uruchamiając następujące polecenia. Zastąp *REALM.com* własną nazwą obszaru.

    ```cmd
    C:> Ksetup /setdomain REALM.COM
    C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
    ```

    Po uruchomieniu tych poleceń Uruchom ponownie komputer.

2.  Sprawdź konfigurację przy użyciu `Ksetup` polecenia. Dane wyjściowe powinny wyglądać następująco:

    ```cmd
    C:> Ksetup
    default realm = REALM.COM (external)
    REALM.com:
        kdc = <your_kdc_server_address>
    ```

**W fabryce danych:**

* Skonfiguruj łącznik HDFS przy użyciu uwierzytelniania systemu Windows razem z nazwą główną i hasłem protokołu Kerberos, aby nawiązać połączenie ze źródłem danych HDFS. Aby uzyskać szczegółowe informacje dotyczące konfiguracji, zapoznaj się z sekcją [Właściwości połączonej usługi HDFS](#linked-service-properties) .

### <a name="option-2-enable-mutual-trust-between-the-windows-domain-and-the-kerberos-realm"></a><a name="kerberos-mutual-trust"></a>Opcja 2: Włącz wzajemne zaufanie między domeną systemu Windows i obszarem Kerberos

#### <a name="requirements"></a>Wymagania

*   Samodzielna maszyna Integration Runtime musi przyłączyć się do domeny systemu Windows.
*   Musisz mieć uprawnienia do aktualizowania ustawień kontrolera domeny.

#### <a name="how-to-configure"></a>Jak skonfigurować

> [!NOTE]
> Zastąp REALM.COM i AD.COM w poniższym samouczku własną nazwą obszaru i kontrolerem domeny.

**Na serwerze KDC:**

1. Edytuj konfigurację centrum dystrybucji kluczy w pliku *krb5. conf* , aby umożliwić usłudze KDC zaufanie do domeny systemu Windows, odwołując się do poniższego szablonu konfiguracji. Domyślnie konfiguracja znajduje się w lokalizacji */etc/krb5.conf*.

   ```config
   [logging]
    default = FILE:/var/log/krb5libs.log
    kdc = FILE:/var/log/krb5kdc.log
    admin_server = FILE:/var/log/kadmind.log
            
   [libdefaults]
    default_realm = REALM.COM
    dns_lookup_realm = false
    dns_lookup_kdc = false
    ticket_lifetime = 24h
    renew_lifetime = 7d
    forwardable = true
            
   [realms]
    REALM.COM = {
     kdc = node.REALM.COM
     admin_server = node.REALM.COM
    }
   AD.COM = {
    kdc = windc.ad.com
    admin_server = windc.ad.com
   }
            
   [domain_realm]
    .REALM.COM = REALM.COM
    REALM.COM = REALM.COM
    .ad.com = AD.COM
    ad.com = AD.COM
            
   [capaths]
    AD.COM = {
     REALM.COM = .
    }
    ```

   Po skonfigurowaniu pliku Uruchom ponownie usługę centrum dystrybucji kluczy.

2. Przygotuj podmiot zabezpieczeń o nazwie *KRBTGT/obszaru. COM \@ AD.com* na serwerze KDC przy użyciu następującego polecenia:

    ```cmd
    Kadmin> addprinc krbtgt/REALM.COM@AD.COM
    ```

3. W pliku konfiguracji usługi *Hadoop.Security.auth_to_local* HDFS Dodaj `RULE:[1:$1@$0](.*\@AD.COM)s/\@.*//` .

**Na kontrolerze domeny:**

1.  Uruchom następujące `Ksetup` polecenia, aby dodać wpis obszaru:

    ```cmd
    C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
    C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM
    ```

2.  Ustanów relację zaufania z domeny systemu Windows do obszaru Kerberos. [hasło] jest hasłem dla głównego elementu *KRBTGT/obszaru. COM \@ AD.com*.

    ```cmd
    C:> netdom trust REALM.COM /Domain: AD.COM /add /realm /password:[password]
    ```

3.  Wybierz algorytm szyfrowania używany w protokole Kerberos.

    a. Wybierz pozycję **Menedżer serwera**  >  **zasady grupy**  >  **domenę**zarządzania  >  **zasady grupy obiekty**  >  **domyślne lub aktywne zasady domeny**, a następnie wybierz pozycję **Edytuj**.

    b. W okienku **Edytor zarządzania zasadami grupy** wybierz kolejno pozycje **Konfiguracja komputera**  >  **zasady**  >  **Ustawienia systemu Windows**  >  **zabezpieczenia ustawienia**  >  **Zasady lokalne**  >  **Opcje zabezpieczeń**, a następnie skonfiguruj **zabezpieczenia sieci: Konfigurowanie typów szyfrowania dozwolonych dla protokołu Kerberos**.

    c. Wybierz algorytm szyfrowania, który ma być używany podczas nawiązywania połączenia z serwerem centrum dystrybucji kluczy. Można wybrać wszystkie opcje.

    ![Zrzut ekranu przedstawiający okienko "zabezpieczenia sieci: Konfigurowanie typów szyfrowania dozwolonych dla protokołu Kerberos"](media/connector-hdfs/config-encryption-types-for-kerberos.png)

    d. Użyj `Ksetup` polecenia, aby określić algorytm szyfrowania, który ma być używany w określonym obszarze.

    ```cmd
    C:> ksetup /SetEncTypeAttr REALM.COM DES-CBC-CRC DES-CBC-MD5 RC4-HMAC-MD5 AES128-CTS-HMAC-SHA1-96 AES256-CTS-HMAC-SHA1-96
    ```

4.  Utwórz mapowanie między kontem domeny i podmiotem zabezpieczeń protokołu Kerberos, aby można było używać podmiotu zabezpieczeń protokołu Kerberos w domenie systemu Windows.

    a. Wybierz pozycję **Narzędzia administracyjne**  >  **Active Directory Użytkownicy i komputery**.

    b. Skonfiguruj funkcje zaawansowane, wybierając opcję **Wyświetl**  >  **Zaawansowane funkcje**.

    c. W okienku **funkcje zaawansowane** kliknij prawym przyciskiem myszy konto, dla którego chcesz utworzyć mapowania, a następnie w okienku **mapowania nazw** wybierz kartę **nazwy Kerberos** .

    d. Dodaj podmiot zabezpieczeń z obszaru.

       ![Okienko "mapowanie tożsamości zabezpieczeń"](media/connector-hdfs/map-security-identity.png)

**Na komputerze, na którym działa środowisko Integration Runtime:**

* Uruchom następujące `Ksetup` polecenia, aby dodać wpis obszaru.

   ```cmd
   C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
   C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM
   ```

**W fabryce danych:**

* Skonfiguruj łącznik HDFS przy użyciu uwierzytelniania systemu Windows wraz z kontem domeny lub podmiotem zabezpieczeń protokołu Kerberos w celu nawiązania połączenia ze źródłem danych systemu plików HDFS. Szczegóły konfiguracji znajdują się w sekcji [Właściwości połączonej usługi](#linked-service-properties) systemu plików HDFS.

## <a name="lookup-activity-properties"></a>Właściwości działania Lookup

Aby uzyskać informacje o właściwościach działania wyszukiwania, zobacz [aktywność Lookup w Azure Data Factory](control-flow-lookup-activity.md).

## <a name="delete-activity-properties"></a>Usuń właściwości działania

Informacje o usuwaniu właściwości działania znajdują się [w temacie Usuwanie działania w Azure Data Factory](delete-activity.md).

## <a name="legacy-models"></a>Starsze modele

>[!NOTE]
>Następujące modele są nadal obsługiwane w celu zapewnienia zgodności z poprzednimi wersjami. Zalecamy użycie wcześniej omówionego nowego modelu, ponieważ interfejs użytkownika tworzenia Azure Data Factory został przełączony w celu wygenerowania nowego modelu.

### <a name="legacy-dataset-model"></a>Model starszego zestawu danych

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość *Type* zestawu danych musi być *ustawiona na wartość* właściwości. |Tak |
| folderPath | Ścieżka do folderu. Obsługiwany jest filtr symboli wieloznacznych. Dozwolone symbole wieloznaczne to `*` (dopasowuje zero lub więcej znaków) i `?` (dopasowuje zero lub jeden znak); Użyj `^` do ucieczki, jeśli rzeczywista nazwa pliku ma symbol wieloznaczny lub ten znak ucieczki wewnątrz. <br/><br/>Przykłady: RootFolder/subfolder/, Zobacz więcej przykładów w [przykładach folderów i filtrów plików](#folder-and-file-filter-examples). |Tak |
| fileName |  Nazwa lub filtr symboli wieloznacznych dla plików pod określonym "folderPath". Jeśli nie określisz wartości dla tej właściwości, zestaw danych wskazuje wszystkie pliki w folderze. <br/><br/>W przypadku filtru dozwolone symbole wieloznaczne to `*` (dopasowuje zero lub więcej znaków) i `?` (dopasowuje zero lub jeden znak).<br/>-Przykład 1: `"fileName": "*.csv"`<br/>-Przykład 2: `"fileName": "???20180427.txt"`<br/>Użyj `^` do wyjścia, jeśli rzeczywista nazwa folderu ma symbol wieloznaczny lub ten znak ucieczki wewnątrz. |Nie |
| modifiedDatetimeStart | Pliki są filtrowane na podstawie *ostatnio modyfikowanego*atrybutu. Pliki są wybierane, jeśli ich czas ostatniej modyfikacji mieści się w zakresie od `modifiedDatetimeStart` do `modifiedDatetimeEnd` . Czas jest stosowany do strefy czasowej UTC w formacie *2018 r-12-01T05:00:00Z*. <br/><br/> Należy pamiętać, że w przypadku, gdy chcesz zastosować filtr plików do dużej liczby plików, będzie to miało wpływ na ogólną wydajność przenoszenia danych. <br/><br/> Właściwości mogą mieć wartość NULL, co oznacza, że żaden filtr atrybutu pliku nie jest stosowany do zestawu danych.  Gdy `modifiedDatetimeStart` ma wartość DateTime, ale `modifiedDatetimeEnd` jest wartością null, oznacza, że pliki, których ostatni zmodyfikowany atrybut jest większy lub równy wartości DateTime, są zaznaczone.  Gdy `modifiedDatetimeEnd` ma wartość DateTime, ale `modifiedDatetimeStart` jest wartością null, oznacza, że pliki, których ostatni zmodyfikowany atrybut jest mniejszy niż wartość DateTime, są zaznaczone.| Nie |
| modifiedDatetimeEnd | Pliki są filtrowane na podstawie *ostatnio modyfikowanego*atrybutu. Pliki są wybierane, jeśli ich czas ostatniej modyfikacji mieści się w zakresie od `modifiedDatetimeStart` do `modifiedDatetimeEnd` . Czas jest stosowany do strefy czasowej UTC w formacie *2018 r-12-01T05:00:00Z*. <br/><br/> Należy pamiętać, że w przypadku, gdy chcesz zastosować filtr plików do dużej liczby plików, będzie to miało wpływ na ogólną wydajność przenoszenia danych. <br/><br/> Właściwości mogą mieć wartość NULL, co oznacza, że żaden filtr atrybutu pliku nie jest stosowany do zestawu danych.  Gdy `modifiedDatetimeStart` ma wartość DateTime, ale `modifiedDatetimeEnd` jest wartością null, oznacza, że pliki, których ostatni zmodyfikowany atrybut jest większy lub równy wartości DateTime, są zaznaczone.  Gdy `modifiedDatetimeEnd` ma wartość DateTime, ale `modifiedDatetimeStart` jest wartością null, oznacza, że pliki, których ostatni zmodyfikowany atrybut jest mniejszy niż wartość DateTime, są zaznaczone.| Nie |
| format | Jeśli chcesz skopiować pliki w postaci między magazynami opartymi na plikach (kopia binarna), Pomiń sekcję format w definicjach zestawu danych wejściowych i wyjściowych.<br/><br/>Jeśli chcesz analizować pliki o określonym formacie, obsługiwane są następujące typy formatu plików: *TextFormat*, *formatu jsonformat*, *AvroFormat*, *OrcFormat*, *ParquetFormat*. Ustaw właściwość *Type* w polu Format na jedną z tych wartości. Aby uzyskać więcej informacji, zobacz sekcję [Format tekstu](supported-file-formats-and-compression-codecs-legacy.md#text-format), [Format JSON](supported-file-formats-and-compression-codecs-legacy.md#json-format), [Format Avro](supported-file-formats-and-compression-codecs-legacy.md#avro-format), [Format Orc](supported-file-formats-and-compression-codecs-legacy.md#orc-format)i [Parquet format](supported-file-formats-and-compression-codecs-legacy.md#parquet-format) . |Nie (tylko w przypadku scenariusza kopiowania binarnego) |
| kompresja | Określ typ i poziom kompresji danych. Aby uzyskać więcej informacji, zobacz [obsługiwane formaty plików i kodery-dekoder kompresji](supported-file-formats-and-compression-codecs-legacy.md#compression-support).<br/>Obsługiwane typy to: *gzip*, *Wklęśnięcie*, *bzip2*i *ZipDeflate*.<br/>Obsługiwane poziomy to: *optymalne* i *najszybszy*. |Nie |

>[!TIP]
>Aby skopiować wszystkie pliki w folderze, określ tylko **folderPath** .<br>Aby skopiować pojedynczy plik o określonej nazwie, określ **folderPath** z częścią **folderu i nazwą pliku o** nazwie.<br>Aby skopiować podzbiór plików w folderze, należy określić **folderPath** z częścią folderu i **nazwą pliku** z filtrem symboli wieloznacznych.

**Przykład:**

```json
{
    "name": "HDFSDataset",
    "properties": {
        "type": "FileShare",
        "linkedServiceName":{
            "referenceName": "<HDFS linked service name>",
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
| typ | Właściwość *Type* źródła działania Copy musi być ustawiona na wartość *HdfsSource*. |Tak |
| rozpoznawania | Wskazuje, czy dane są odczytane cyklicznie z podfolderów, czy tylko z określonego folderu. Gdy wartość cykliczna jest ustawiona na *wartość true* , a ujścia jest magazynem opartym na plikach, pusty folder lub podfolder nie zostanie skopiowany ani utworzony w ujścia.<br/>Dozwolone wartości to *true* (wartość domyślna) i *false*. | Nie |
| distcpSettings | Grupa właściwości, gdy jest używany system HDFS pomocą distcp. | Nie |
| resourceManagerEndpoint | Punkt końcowy Menedżer zasobów PRZĘDZy | Tak, jeśli używasz pomocą distcp |
| tempScriptPath | Ścieżka folderu, która jest używana do przechowywania tymczasowego skryptu poleceń pomocą distcp. Plik skryptu jest generowany przez Data Factory i zostanie usunięty po zakończeniu zadania kopiowania. | Tak, jeśli używasz pomocą distcp |
| distcpOptions | Dodatkowe opcje są dostępne dla polecenia pomocą distcp. | Nie |
| maxConcurrentConnections | Liczba połączeń, które mogą łączyć się z magazynem magazynu współbieżnie. Określ wartość tylko wtedy, gdy chcesz ograniczyć współbieżne połączenie z magazynem danych. | Nie |

**Przykład: Źródło HDFS w działaniu kopiowania przy użyciu pomocą distcp**

```json
"source": {
    "type": "HdfsSource",
    "distcpSettings": {
        "resourceManagerEndpoint": "resourcemanagerendpoint:8088",
        "tempScriptPath": "/usr/hadoop/tempscript",
        "distcpOptions": "-m 100"
    }
}
```

## <a name="next-steps"></a>Następne kroki
Listę magazynów danych obsługiwanych jako źródła i ujścia przez działanie kopiowania w Azure Data Factory można znaleźć w temacie [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
