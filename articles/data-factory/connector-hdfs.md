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
ms.date: 05/15/2020
ms.author: jingwang
ms.openlocfilehash: f39fb50c5044cacb04b3b147a0160dd23c9eb2d0
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83657100"
---
# <a name="copy-data-from-hdfs-using-azure-data-factory"></a>Kopiowanie danych z systemu plików HDFS przy użyciu Azure Data Factory
> [!div class="op_single_selector" title1="Wybierz używaną wersję usługi Data Factory:"]
> * [Wersja 1](v1/data-factory-hdfs-connector.md)
> * [Bieżąca wersja](connector-hdfs.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule opisano sposób kopiowania danych z serwera HDFS. Aby dowiedzieć się więcej na temat Azure Data Factory, Przeczytaj [artykuł wprowadzający](introduction.md).

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Ten łącznik systemu plików HDFS jest obsługiwany dla następujących działań:

- [Działanie kopiowania](copy-activity-overview.md) z [obsługiwaną macierzą źródłową/ujścia](copy-activity-overview.md)
- [Działanie Lookup](control-flow-lookup-activity.md)

Ten łącznik systemu plików HDFS obsługuje tylko następujące:

- Kopiowanie plików przy użyciu **systemu Windows** (Kerberos) lub uwierzytelniania **anonimowego** .
- Kopiowanie plików przy użyciu protokołu **webhdfs** lub **wbudowanej obsługi pomocą distcp** .
- Kopiowanie plików jako-is lub analizowanie/generowanie plików z [obsługiwanymi formatami plików i koderami-dekoder kompresji](supported-file-formats-and-compression-codecs.md).

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

> [!NOTE]
> Upewnij się, że Integration Runtime ma dostęp do **wszystkich** elementów [nazwa węzła Server]: [nazwa węzła port] i [serwery węzłów danych]: [port węzła danych] klastra Hadoop. Domyślny [nazwa portu węzła] to 50070, a domyślny [port węzła danych] to 50075.

## <a name="getting-started"></a>Wprowadzenie

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje dotyczące właściwości, które są używane do definiowania jednostek Data Factory specyficznych dla systemu plików HDFS.

## <a name="linked-service-properties"></a>Właściwości połączonej usługi

Dla połączonej usługi HDFS są obsługiwane następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość Type musi mieć wartość: **HDFS**. | Yes |
| url |Adres URL systemu plików HDFS |Yes |
| authenticationType | Dozwolone wartości to: **Anonymous**lub **Windows**. <br><br> Aby skorzystać z **uwierzytelniania Kerberos** dla łącznika HDFS, zapoznaj się z [tą sekcją](#use-kerberos-authentication-for-hdfs-connector) , aby odpowiednio skonfigurować środowisko lokalne. |Yes |
| userName |Nazwa użytkownika dla uwierzytelniania systemu Windows. W przypadku uwierzytelniania Kerberos Określ `<username>@<domain>.com` . |Tak (w przypadku uwierzytelniania systemu Windows) |
| hasło |Hasło do uwierzytelniania systemu Windows. Oznacz to pole jako element SecureString, aby bezpiecznie przechowywać go w Data Factory, lub [odwoływać się do wpisu tajnego przechowywanego w Azure Key Vault](store-credentials-in-key-vault.md). |Tak (w przypadku uwierzytelniania systemu Windows) |
| Właściwością connectvia | [Integration Runtime](concepts-integration-runtime.md) używany do nawiązywania połączenia z magazynem danych. Dowiedz się więcej z sekcji [wymagania wstępne](#prerequisites) . Jeśli nie zostanie określony, zostanie użyta domyślna Azure Integration Runtime. |Nie |

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

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania zestawów danych, zobacz artykuł [zestawy danych](concepts-datasets-linked-services.md) . 

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Następujące właściwości są obsługiwane w systemie plików HDFS `location` w obszarze Ustawienia w zestawie danych opartym na formacie:

| Właściwość   | Opis                                                  | Wymagane |
| ---------- | ------------------------------------------------------------ | -------- |
| typ       | Właściwość Type w `location` elemencie DataSet musi być ustawiona na wartość **HdfsLocation**. | Yes      |
| folderPath | Ścieżka do folderu. Jeśli chcesz użyć symboli wieloznacznych do filtrowania folderów, pomiń to ustawienie i określ ustawienia źródła działania. | Nie       |
| fileName   | Nazwa pliku pod podanym folderPath. Jeśli chcesz użyć symboli wieloznacznych do filtrowania plików, pomiń to ustawienie i określ ustawienia źródła działania. | Nie       |

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

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania działań, zobacz artykuł [potoki](concepts-pipelines-activities.md) . Ta sekcja zawiera listę właściwości obsługiwanych przez źródło systemu plików HDFS.

### <a name="hdfs-as-source"></a>System plików HDFS jako źródło

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Następujące właściwości są obsługiwane w systemie plików HDFS `storeSettings` w obszarze Ustawienia w źródle kopiowania opartego na formacie:

| Właściwość                 | Opis                                                  | Wymagane                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| typ                     | Właściwość Type w obszarze `storeSettings` musi być ustawiona na wartość **HdfsReadSettings**. | Yes                                           |
| ***Zlokalizuj pliki do skopiowania:*** |  |  |
| Opcja 1: ścieżka statyczna<br> | Kopiuj z podanego folderu/ścieżki pliku określonego w zestawie danych. Jeśli chcesz skopiować wszystkie pliki z folderu, należy również określić `wildcardFileName` jako `*` . |  |
| Opcja 2: symbol wieloznaczny<br>- wildcardFolderPath | Ścieżka folderu z symbolami wieloznacznymi do filtrowania folderów źródłowych. <br>Dozwolone symbole wieloznaczne to: `*` (dopasowuje zero lub więcej znaków) i `?` (dopasowuje zero lub pojedynczy znak); Użyj `^` do ucieczki, jeśli rzeczywista nazwa folderu ma symbol wieloznaczny lub ten znak ucieczki wewnątrz. <br>Zobacz więcej przykładów w [przykładach folderów i filtrów plików](#folder-and-file-filter-examples). | Nie                                            |
| Opcja 2: symbol wieloznaczny<br>- wildcardFileName | Nazwa pliku z symbolami wieloznacznymi pod daną folderPath/wildcardFolderPath do filtrowania plików źródłowych. <br>Dozwolone symbole wieloznaczne to: `*` (dopasowuje zero lub więcej znaków) i `?` (dopasowuje zero lub pojedynczy znak); Użyj `^` do ucieczki, jeśli rzeczywista nazwa folderu ma symbol wieloznaczny lub ten znak ucieczki wewnątrz.  Zobacz więcej przykładów w [przykładach folderów i filtrów plików](#folder-and-file-filter-examples). | Yes |
| Opcja 3: Lista plików<br>- fileListPath | Wskazuje, aby skopiować dany zestaw plików. Wskaż plik tekstowy, który zawiera listę plików, które chcesz skopiować, jeden plik na wiersz, który jest ścieżką względną do ścieżki skonfigurowanej w zestawie danych.<br/>W przypadku korzystania z tej opcji nie należy określać nazwy pliku w zestawie danych. Zobacz więcej przykładów na [listach plików](#file-list-examples). |Nie |
| ***Ustawienia dodatkowe:*** |  | |
| rozpoznawania | Wskazuje, czy dane są odczytane cyklicznie z podfolderów, czy tylko z określonego folderu. Należy pamiętać, że gdy wartość cykliczna jest ustawiona na wartość true, a ujścia jest magazynem opartym na plikach, pusty folder lub podfolder nie jest kopiowany ani tworzony w ujścia. <br>Dozwolone wartości to **true** (wartość domyślna) i **false**.<br>Ta właściwość nie ma zastosowania podczas konfigurowania `fileListPath` . |Nie |
| modifiedDatetimeStart    | Filtr plików oparty na atrybucie: ostatnio modyfikowane. <br>Pliki zostaną wybrane, jeśli ich czas ostatniej modyfikacji należy do przedziału czasu między `modifiedDatetimeStart` i `modifiedDatetimeEnd` . Czas jest stosowany do strefy czasowej UTC w formacie "2018 r-12-01T05:00:00Z". <br> Właściwości mogą mieć wartość NULL, co oznacza, że żaden filtr atrybutu pliku nie zostanie zastosowany do zestawu danych.  Gdy `modifiedDatetimeStart` ma wartość DateTime `modifiedDatetimeEnd` , ale jest wartością null, oznacza to, że pliki, których ostatni zmodyfikowany atrybut jest większy lub równy wartości DateTime, zostaną zaznaczone.  Gdy `modifiedDatetimeEnd` ma wartość DateTime `modifiedDatetimeStart` , ale jest wartością null, oznacza to, że pliki, których ostatni zmodyfikowany atrybut jest mniejszy niż wartość DateTime zostanie wybrana.<br/>Ta właściwość nie ma zastosowania podczas konfigurowania `fileListPath` . | Nie                                            |
| maxConcurrentConnections | Liczba połączeń, które mają być jednocześnie połączone z magazynem magazynu. Określ tylko wtedy, gdy chcesz ograniczyć współbieżne połączenie z magazynem danych. | Nie                                            |
| ***Ustawienia pomocą distcp:*** |  | |
| distcpSettings | Grupa właściwości przy użyciu systemu HDFS pomocą distcp. | Nie |
| resourceManagerEndpoint | Punkt końcowy Menedżer zasobów przędzy | Tak, jeśli używasz pomocą distcp |
| tempScriptPath | Ścieżka folderu używana do przechowywania skryptu poleceń temp pomocą distcp. Plik skryptu jest generowany przez Data Factory i zostanie usunięty po zakończeniu zadania kopiowania. | Tak, jeśli używasz pomocą distcp |
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

W tej sekcji opisano skutki zachowania ścieżki folderu i nazwy pliku z filtrami symboli wieloznacznych.

| folderPath | fileName             | rozpoznawania | Źródłowa Struktura folderu i wynik filtru (pliki **pogrubione** są pobierane) |
| :--------- | :------------------- | :-------- | :----------------------------------------------------------- |
| `Folder*`  | (puste, Użyj domyślnego) | fałsz     | Folder<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Plik1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Plik2. JSON**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3. csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |
| `Folder*`  | (puste, Użyj domyślnego) | true      | Folder<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Plik1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Plik2. JSON**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4. JSON**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5. csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |
| `Folder*`  | `*.csv`              | fałsz     | Folder<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Plik1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3. csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |
| `Folder*`  | `*.csv`              | true      | Folder<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Plik1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5. csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |

### <a name="file-list-examples"></a>Przykłady listy plików

W tej sekcji opisano wyniki działania dotyczące korzystania ze ścieżki listy plików w źródle działania kopiowania.

Przy założeniu, że masz następującą źródłową strukturę folderów i chcesz skopiować pliki w pogrubieniu:

| Przykładowa struktura źródła                                      | Zawartość w FileListToCopy. txt                             | Konfiguracja ADF                                            |
| ------------------------------------------------------------ | --------------------------------------------------------- | ------------------------------------------------------------ |
| root<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Plik1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik2. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Metadane<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;FileListToCopy. txt | Plik1. csv<br>Subfolder1/file3. csv<br>Subfolder1/File5. csv | **W zestawie danych:**<br>-Ścieżka folderu:`root/FolderA`<br><br>**W źródle działania kopiowania:**<br>-Ścieżka listy plików:`root/Metadata/FileListToCopy.txt` <br><br>Ścieżka listy plików wskazuje plik tekstowy w tym samym magazynie danych, który zawiera listę plików do skopiowania, jeden plik na wiersz ze ścieżką względną do ścieżki skonfigurowanej w zestawie danych. |

## <a name="use-distcp-to-copy-data-from-hdfs"></a>Używanie pomocą distcp do kopiowania danych z systemu plików HDFS

[Pomocą distcp](https://hadoop.apache.org/docs/current3/hadoop-distcp/DistCp.html) jest natywnym narzędziem wiersza polecenia usługi Hadoop do wykonywania kopii rozproszonej w klastrze usługi Hadoop. Po uruchomieniu polecenia pomocą distcp najpierw zostanie wystawiona lista wszystkich plików do skopiowania, utworzenie kilku zadań mapy w klastrze usługi Hadoop, a każde zadanie mapy spowoduje skopiowanie danych binarnych ze źródła do ujścia.

Obsługa działań kopiowania przy użyciu pomocą distcp do kopiowania plików jako-do obiektów blob platformy Azure (w tym [kopii etapowej](copy-activity-performance.md)) lub Azure Data Lake Store, w którym to przypadku można w pełni wykorzystać możliwości klastra zamiast uruchamiać je na samodzielnym Integration Runtime. Zapewni to lepszą przepływność kopiowania, zwłaszcza jeśli klaster jest bardzo wydajny. W oparciu o konfigurację w Azure Data Factory, działanie Copy automatycznie konstruuje polecenie pomocą distcp, przesłać je do klastra usługi Hadoop i monitorować stan kopiowania.

### <a name="prerequisites"></a>Wymagania wstępne

Aby użyć pomocą distcp do kopiowania plików z systemem HDFS do obiektu blob platformy Azure (w tym kopii przygotowanej) lub Azure Data Lake Store upewnij się, że klaster usługi Hadoop spełnia wymagania poniżej:

1. Usługi MapReduce i przędzy są włączone.
2. Wersja przędzy to 2,5 lub nowsza.
3. Serwer HDFS jest zintegrowany z docelowym magazynem danych — obiektem blob platformy Azure lub Azure Data Lake Store:

    - System plików obiektów blob platformy Azure jest natywnie obsługiwany od wersji Hadoop 2,7. Wystarczy określić ścieżkę jar w konfiguracji ENV usługi Hadoop.
    - System plików Azure Data Lake Store jest spakowany od platformy Hadoop 3.0.0-alpha1. Jeśli klaster usługi Hadoop jest niższy niż ta wersja, należy ręcznie zaimportować powiązane z usługą ADLS pakiety jar (Azure-datalake-Store. jar) do klastra z tego [miejsca](https://hadoop.apache.org/releases.html)i określić ścieżkę jar w konfiguracji ENV usługi Hadoop.

4. Przygotuj folder tymczasowy w systemie plików HDFS. Ten folder tymczasowy służy do przechowywania skryptu powłoki pomocą distcp, więc zajmie miejsce na poziomie KB.
5. Upewnij się, że konto użytkownika podane w połączonej usłudze HDFS ma uprawnienie do przesyłania aplikacji w ramach przędzy; b) mają uprawnienia do tworzenia podfolderu, plików do odczytu i zapisu w powyższym folderze tymczasowym.

### <a name="configurations"></a>Konfiguracje

Zobacz sekcję pomocą distcp powiązane konfiguracje i przykłady w obszarze [HDFS jako źródło](#hdfs-as-source) .

## <a name="use-kerberos-authentication-for-hdfs-connector"></a>Używanie uwierzytelniania Kerberos dla łącznika HDFS

Dostępne są dwie opcje konfigurowania środowiska lokalnego w taki sposób, aby korzystały z uwierzytelniania Kerberos w ramach łącznika systemu plików HDFS. Możesz wybrać ten, który lepiej pasuje do Twojego przypadku.
* Opcja 1: [Przyłącz się do samodzielnej maszyny Integration Runtime w obszarze Kerberos](#kerberos-join-realm)
* Opcja 2: [Włącz wzajemne zaufanie między domeną systemu Windows i obszarem Kerberos](#kerberos-mutual-trust)

### <a name="option-1-join-self-hosted-integration-runtime-machine-in-kerberos-realm"></a><a name="kerberos-join-realm"></a>Opcja 1: Przyłącz się do samodzielnej maszyny Integration Runtime w obszarze Kerberos

#### <a name="requirements"></a>Wymagania

* Samodzielna maszyna Integration Runtime musi przyłączyć obszar Kerberos i nie może dołączać do żadnej domeny systemu Windows.

#### <a name="how-to-configure"></a>Jak skonfigurować

**Na samodzielnym komputerze Integration Runtime:**

1.  Uruchom narzędzie **Ksetup** , aby skonfigurować serwer i obszar KDC protokołu Kerberos.

    Komputer musi być skonfigurowany jako członek grupy roboczej, ponieważ obszar Kerberos różni się od domeny systemu Windows. Można to osiągnąć przez ustawienie obszaru protokołu Kerberos i dodanie serwera KDC w następujący sposób. Zastąp *REALM.com* własnymi obszarami w miarę potrzeb.

            C:> Ksetup /setdomain REALM.COM
            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>

    Po wykonaniu tych dwóch poleceń **ponownie uruchom** maszynę.

2.  Sprawdź konfigurację przy użyciu polecenia **Ksetup** . Dane wyjściowe powinny wyglądać następująco:

            C:> Ksetup
            default realm = REALM.COM (external)
            REALM.com:
                kdc = <your_kdc_server_address>

**W Azure Data Factory:**

* Skonfiguruj łącznik HDFS przy użyciu **uwierzytelniania systemu Windows** razem z nazwą główną i hasłem protokołu Kerberos w celu nawiązania połączenia ze źródłem danych systemu plików HDFS. Sprawdź sekcję [Właściwości połączonej usługi HDFS](#linked-service-properties) w szczegółach konfiguracji.

### <a name="option-2-enable-mutual-trust-between-windows-domain-and-kerberos-realm"></a><a name="kerberos-mutual-trust"></a>Opcja 2: Włącz wzajemne zaufanie między domeną systemu Windows i obszarem Kerberos

#### <a name="requirements"></a>Wymagania

*   Samodzielna maszyna Integration Runtime musi przyłączyć się do domeny systemu Windows.
*   Musisz mieć uprawnienia do aktualizowania ustawień kontrolera domeny.

#### <a name="how-to-configure"></a>Jak skonfigurować

> [!NOTE]
> W razie konieczności Zastąp REALM.COM i AD.COM w poniższym samouczku do własnego obszaru i kontrolera domeny.

**Na serwerze KDC:**

1. Edytuj konfigurację centrum dystrybucji kluczy w pliku **krb5. conf** , aby umożliwić zaufanie centrum dystrybucji kluczy do domeny systemu Windows, odwołując się do następującego szablonu konfiguracji. Domyślnie konfiguracja znajduje się w lokalizacji **/etc/krb5.conf**.

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

   Po skonfigurowaniu należy **ponownie uruchomić** usługę centrum dystrybucji kluczy.

2. Przygotuj podmiot zabezpieczeń o nazwie **KRBTGT/REALM. COM \@ AD.com** na serwerze KDC przy użyciu następującego polecenia:

           Kadmin> addprinc krbtgt/REALM.COM@AD.COM

3. W pliku konfiguracji usługi **Hadoop. Security. auth_to_local** HDFS Dodaj `RULE:[1:$1@$0](.*\@AD.COM)s/\@.*//` .

**Na kontrolerze domeny:**

1.  Uruchom następujące polecenia **Ksetup** , aby dodać wpis obszaru:

            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
            C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM

2.  Ustanów relację zaufania z domeny systemu Windows do obszaru Kerberos. [hasło] jest hasłem dla głównego elementu **KRBTGT/obszaru. COM \@ AD.com**.

            C:> netdom trust REALM.COM /Domain: AD.COM /add /realm /passwordt:[password]

3.  Wybierz algorytm szyfrowania używany w protokole Kerberos.

    1. Przejdź do Menedżer serwera > zasady grupy zarządzanie > domeny > zasady grupy obiektów > domyślnych lub aktywnych zasad domeny i Edytuj.

    2. W oknie podręcznym **Edytor zarządzania zasadami grupy** przejdź do pozycji konfiguracja komputera > zasady > ustawienia systemu Windows > ustawienia zabezpieczeń > zasady lokalne > opcje zabezpieczeń i skonfiguruj **zabezpieczenia sieci: Skonfiguruj typy szyfrowania dozwolone dla protokołu Kerberos**.

    3. Wybierz algorytm szyfrowania, który ma być używany podczas nawiązywania połączenia z centrum dystrybucji kluczy. Zazwyczaj można po prostu wybrać wszystkie opcje.

        ![Typy szyfrowania dla protokołu Kerberos](media/connector-hdfs/config-encryption-types-for-kerberos.png)

    4. Użyj polecenia **Ksetup** , aby określić algorytm szyfrowania, który ma być używany w konkretnym obszarze.

                C:> ksetup /SetEncTypeAttr REALM.COM DES-CBC-CRC DES-CBC-MD5 RC4-HMAC-MD5 AES128-CTS-HMAC-SHA1-96 AES256-CTS-HMAC-SHA1-96

4.  Utwórz mapowanie między kontem domeny i podmiotem zabezpieczeń protokołu Kerberos, aby użyć podmiotu zabezpieczeń Kerberos w domenie systemu Windows.

    1. Uruchom narzędzia administracyjne > **Active Directory Użytkownicy i komputery**.

    2. Skonfiguruj funkcje zaawansowane, klikając pozycję **Wyświetl**  >  **Zaawansowane funkcje**.

    3. Znajdź konto, do którego chcesz utworzyć mapowania, a następnie kliknij prawym przyciskiem myszy, aby wyświetlić **mapowania nazw** > kliknij kartę **nazwy Kerberos** .

    4. Dodaj podmiot zabezpieczeń z obszaru.

        ![Zamapuj tożsamość zabezpieczeń](media/connector-hdfs/map-security-identity.png)

**Na samodzielnym komputerze Integration Runtime:**

* Aby dodać wpis obszaru, uruchom następujące polecenia **Ksetup** .

            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
            C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM

**W Azure Data Factory:**

* Skonfiguruj łącznik HDFS przy użyciu **uwierzytelniania systemu Windows** razem z kontem domeny lub podmiotem zabezpieczeń protokołu Kerberos, aby nawiązać połączenie ze źródłem danych HDFS. Sprawdź sekcję [Właściwości połączonej usługi HDFS](#linked-service-properties) w szczegółach konfiguracji.

## <a name="lookup-activity-properties"></a>Właściwości działania Lookup

Aby dowiedzieć się więcej o właściwościach, sprawdź [działanie Lookup (wyszukiwanie](control-flow-lookup-activity.md)).

## <a name="legacy-models"></a>Starsze modele

>[!NOTE]
>Następujące modele są nadal obsługiwane w celu zapewnienia zgodności z poprzednimi wersjami. Użytkownik chce użyć nowego modelu wymienionego w powyższych sekcjach, przechodząc do przodu, a interfejs użytkownika tworzenia ADF został przełączony w celu wygenerowania nowego modelu.

### <a name="legacy-dataset-model"></a>Model starszego zestawu danych

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość Type zestawu danych musi być ustawiona na **wartość:** dataudział |Yes |
| folderPath | Ścieżka do folderu. Filtr symboli wieloznacznych jest obsługiwany, dozwolone symbole wieloznaczne to: `*` (dopasowuje zero lub więcej znaków) i `?` (dopasowuje zero lub pojedynczy znak); Użyj `^` do wyjścia, jeśli rzeczywista nazwa pliku ma symbol wieloznaczny lub ten znak ucieczki wewnątrz. <br/><br/>Przykłady: RootFolder/subfolder/, Zobacz więcej przykładów w [przykładach folderów i filtrów plików](#folder-and-file-filter-examples). |Yes |
| fileName |  **Nazwa lub filtr symboli wieloznacznych** dla plików w ramach określonego elementu "folderPath". Jeśli nie określisz wartości dla tej właściwości, zestaw danych wskazuje wszystkie pliki w folderze. <br/><br/>W przypadku filtru dozwolone symbole wieloznaczne to: `*` (dopasowuje zero lub więcej znaków) i `?` (dopasowuje zero lub pojedynczy znak).<br/>-Przykład 1:`"fileName": "*.csv"`<br/>-Przykład 2:`"fileName": "???20180427.txt"`<br/>Użyj `^` do ucieczki, jeśli rzeczywista nazwa folderu ma symbol wieloznaczny lub ten znak ucieczki wewnątrz. |Nie |
| modifiedDatetimeStart | Filtr plików oparty na atrybucie: ostatnio modyfikowane. Pliki zostaną wybrane, jeśli ich czas ostatniej modyfikacji należy do przedziału czasu między `modifiedDatetimeStart` i `modifiedDatetimeEnd` . Czas jest stosowany do strefy czasowej UTC w formacie "2018 r-12-01T05:00:00Z". <br/><br/> Należy pamiętać, że będzie to miało wpływ na ogólną wydajność przenoszenia danych przez włączenie tego ustawienia, jeśli chcesz, aby filtr plików był objęty dużą ilością plików. <br/><br/> Właściwości mogą mieć wartość NULL, co oznacza, że żaden filtr atrybutu pliku nie zostanie zastosowany do zestawu danych.  Gdy `modifiedDatetimeStart` ma wartość DateTime `modifiedDatetimeEnd` , ale jest wartością null, oznacza to, że pliki, których ostatni zmodyfikowany atrybut jest większy lub równy wartości DateTime, zostaną zaznaczone.  Gdy `modifiedDatetimeEnd` ma wartość DateTime `modifiedDatetimeStart` , ale jest wartością null, oznacza to, że pliki, których ostatni zmodyfikowany atrybut jest mniejszy niż wartość DateTime zostanie wybrana.| Nie |
| modifiedDatetimeEnd | Filtr plików oparty na atrybucie: ostatnio modyfikowane. Pliki zostaną wybrane, jeśli ich czas ostatniej modyfikacji należy do przedziału czasu między `modifiedDatetimeStart` i `modifiedDatetimeEnd` . Czas jest stosowany do strefy czasowej UTC w formacie "2018 r-12-01T05:00:00Z". <br/><br/> Należy pamiętać, że będzie to miało wpływ na ogólną wydajność przenoszenia danych przez włączenie tego ustawienia, jeśli chcesz, aby filtr plików był objęty dużą ilością plików. <br/><br/> Właściwości mogą mieć wartość NULL, co oznacza, że żaden filtr atrybutu pliku nie zostanie zastosowany do zestawu danych.  Gdy `modifiedDatetimeStart` ma wartość DateTime `modifiedDatetimeEnd` , ale jest wartością null, oznacza to, że pliki, których ostatni zmodyfikowany atrybut jest większy lub równy wartości DateTime, zostaną zaznaczone.  Gdy `modifiedDatetimeEnd` ma wartość DateTime `modifiedDatetimeStart` , ale jest wartością null, oznacza to, że pliki, których ostatni zmodyfikowany atrybut jest mniejszy niż wartość DateTime zostanie wybrana.| Nie |
| format | Jeśli chcesz **skopiować pliki** między magazynami opartymi na plikach (kopia binarna), Pomiń sekcję format w definicjach zestawu danych wejściowych i wyjściowych.<br/><br/>Jeśli chcesz analizować pliki o określonym formacie, obsługiwane są następujące typy formatu plików: **TextFormat**, **formatu jsonformat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Ustaw właściwość **Type** w polu Format na jedną z tych wartości. Aby uzyskać więcej informacji, zobacz [format tekstowy](supported-file-formats-and-compression-codecs-legacy.md#text-format), [Format JSON](supported-file-formats-and-compression-codecs-legacy.md#json-format), [Format Avro](supported-file-formats-and-compression-codecs-legacy.md#avro-format), [Format Orc](supported-file-formats-and-compression-codecs-legacy.md#orc-format)i sekcje [formatu Parquet](supported-file-formats-and-compression-codecs-legacy.md#parquet-format) . |Nie (tylko w przypadku scenariusza kopiowania binarnego) |
| kompresja | Określ typ i poziom kompresji danych. Aby uzyskać więcej informacji, zobacz [obsługiwane formaty plików i kodery-dekoder kompresji](supported-file-formats-and-compression-codecs-legacy.md#compression-support).<br/>Obsługiwane typy to: **gzip**, **Wklęśnięcie**, **BZip2**i **ZipDeflate**.<br/>Obsługiwane poziomy to: **optymalne** i **najszybszy**. |Nie |

>[!TIP]
>Aby skopiować wszystkie pliki w folderze, określ tylko **folderPath** .<br>Aby skopiować pojedynczy plik o podanej nazwie, należy określić **folderPath** z częścią **folderu i nazwą pliku.**<br>Aby skopiować podzbiór plików w folderze, należy określić **folderPath** z częścią folderu i **nazwą pliku** z filtrem symboli wieloznacznych.

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
| typ | Właściwość Type źródła działania Copy musi być ustawiona na wartość: **HdfsSource** |Yes |
| rozpoznawania | Wskazuje, czy dane są odczytane cyklicznie z podfolderów, czy tylko z określonego folderu. Uwaga gdy wartość cykliczna jest ustawiona na wartość true, a obiekt sink jest magazynem opartym na plikach, pusty folder/podfolder nie zostanie skopiowany/utworzony w ujścia.<br/>Dozwolone wartości to: **true** (wartość domyślna), **Fałsz** | Nie |
| distcpSettings | Grupa właściwości przy użyciu systemu HDFS pomocą distcp. | Nie |
| resourceManagerEndpoint | Punkt końcowy Menedżer zasobów przędzy | Tak, jeśli używasz pomocą distcp |
| tempScriptPath | Ścieżka folderu używana do przechowywania skryptu poleceń temp pomocą distcp. Plik skryptu jest generowany przez Data Factory i zostanie usunięty po zakończeniu zadania kopiowania. | Tak, jeśli używasz pomocą distcp |
| distcpOptions | Dodatkowe opcje dostępne dla polecenia pomocą distcp. | Nie |
| maxConcurrentConnections | Liczba połączeń, które mają być jednocześnie połączone z magazynem magazynu. Określ tylko wtedy, gdy chcesz ograniczyć współbieżne połączenie z magazynem danych. | Nie |

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
