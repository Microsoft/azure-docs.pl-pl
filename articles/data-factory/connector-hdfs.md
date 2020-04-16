---
title: Kopiowanie danych z usługi HDFS przy użyciu usługi Azure Data Factory
description: Dowiedz się, jak kopiować dane z chmury lub lokalnego źródła HDFS do obsługiwanych magazynów danych ujścia przy użyciu działania kopiowania w potoku usługi Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: jingwang
ms.openlocfilehash: 09c39c41b2d31f88fe2b19d8f20cd19e182c9214
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417263"
---
# <a name="copy-data-from-hdfs-using-azure-data-factory"></a>Kopiowanie danych z usługi HDFS przy użyciu usługi Azure Data Factory
> [!div class="op_single_selector" title1="Wybierz wersję używanej usługi Data Factory:"]
> * [Wersja 1](v1/data-factory-hdfs-connector.md)
> * [Bieżąca wersja](connector-hdfs.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule opisano sposób kopiowania danych z serwera HDFS. Aby dowiedzieć się więcej o usłudze Azure Data Factory, przeczytaj [artykuł wprowadzający](introduction.md).

## <a name="supported-capabilities"></a>Obsługiwane możliwości

To złącze HDFS jest obsługiwane dla następujących działań:

- [Kopiowanie aktywności](copy-activity-overview.md) z [obsługiwaną macierzą źródło/ujście](copy-activity-overview.md)
- [Działanie odnośnika](control-flow-lookup-activity.md)

W szczególności to złącze HDFS obsługuje:

- Kopiowanie plików przy użyciu **systemu Windows** (Kerberos) lub uwierzytelniania **anonimowego.**
- Kopiowanie plików przy użyciu protokołu **webhdfs** lub wbudowanej obsługi **DistCp.**
- Kopiowanie plików w stanie jednakowym lub analizowanie/generowanie plików za pomocą [obsługiwanych formatów plików i kodeków kompresji](supported-file-formats-and-compression-codecs.md).

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

> [!NOTE]
> Upewnij się, że środowisko wykonawcze integracji może uzyskać dostęp do **WSZYSTKICH** [serwera węzłów nazw]:[port węzła nazwy] i [serwery węzłów danych]:[port węzła danych] klastra Hadoop. Domyślnie [port węzła nazwy] to 50070, a domyślnie [port węzła danych] to 50075.

## <a name="getting-started"></a>Wprowadzenie

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje o właściwościach, które są używane do definiowania jednostek fabryki danych specyficznych dla usługi HDFS.

## <a name="linked-service-properties"></a>Połączone właściwości usługi

Następujące właściwości są obsługiwane dla usługi połączonej z usługą HDFS:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość typu musi być ustawiona na: **Hdfs**. | Tak |
| url |Adres URL do plików TWARDYCH |Tak |
| authenticationType | Dozwolone wartości to: **Anonimowy**lub **Windows**. <br><br> Aby użyć **uwierzytelniania Kerberos** dla łącznika HDFS, zapoznaj się z [tą sekcją,](#use-kerberos-authentication-for-hdfs-connector) aby odpowiednio skonfigurować środowisko lokalne. |Tak |
| userName |Nazwa użytkownika do uwierzytelniania systemu Windows. W przypadku uwierzytelniania `<username>@<domain>.com`Kerberos określ opcję . |Tak (dla uwierzytelniania systemu Windows) |
| hasło |Hasło do uwierzytelniania systemu Windows. Oznacz to pole jako SecureString, aby bezpiecznie przechowywać go w fabryce danych lub [odwołaj się do klucza tajnego przechowywanego w usłudze Azure Key Vault.](store-credentials-in-key-vault.md) |Tak (dla uwierzytelniania systemu Windows) |
| connectVia | [Środowisko wykonawcze integracji,](concepts-integration-runtime.md) które mają być używane do łączenia się z magazynem danych. Dowiedz się więcej z sekcji [Wymagania wstępne.](#prerequisites) Jeśli nie zostanie określony, używa domyślnego środowiska wykonawczego integracji platformy Azure. |Nie |

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

**Przykład: korzystanie z uwierzytelniania systemu Windows**

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

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania zestawów danych, zobacz artykuł [Zestawy danych.](concepts-datasets-linked-services.md) 

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Następujące właściwości są obsługiwane dla systemu `location` plików HDFS w ustawieniach w zestawie danych opartym na formacie:

| Właściwość   | Opis                                                  | Wymagany |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | Właściwość typu `location` w obszarze w zestawie danych musi być ustawiona na **HdfsLocation**. | Tak      |
| folderPath | Ścieżka do folderu. Jeśli chcesz użyć symbolu wieloznacznego do filtrowania folderu, pomiń to ustawienie i określ w ustawieniach źródła aktywności. | Nie       |
| fileName   | Nazwa pliku pod danym folderPath. Jeśli chcesz użyć symbolu wieloznacznego do filtrowania plików, pomiń to ustawienie i określ w ustawieniach źródła aktywności. | Nie       |

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

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania działań, zobacz [Pipelines](concepts-pipelines-activities.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez źródło HDFS.

### <a name="hdfs-as-source"></a>HDFS jako źródło

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Następujące właściwości są obsługiwane dla systemu `storeSettings` plików HDFS w ustawieniach w źródle kopiowania opartego na formacie:

| Właściwość                 | Opis                                                  | Wymagany                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | Właściwość typu `storeSettings` w obszarze musi być ustawiona na **HdfsReadSettings**. | Tak                                           |
| Cykliczne                | Wskazuje, czy dane są odczytywane cyklicznie z podfolderów, czy tylko z określonego folderu. Należy zauważyć, że gdy cykliczne jest ustawiona na true i ujście jest magazyn oparty na plikach, pusty folder lub podfolder nie jest kopiowany lub tworzony w zlewie. Dozwolone wartości są **prawdziwe** (domyślnie) i **false**. | Nie                                            |
| Ścieżka wieloznacznaFolderPath       | Ścieżka folderu ze znakami wieloznaczowymi do filtrowania folderów źródłowych. <br>Dozwolone symbole wieloznaczne to: `*` (dopasowuje zero lub więcej znaków) i `?` (pasuje do zera lub pojedynczego znaku); użyj, `^` aby uciec, jeśli rzeczywista nazwa folderu ma symbol wieloznaczny lub ten znak ucieczki wewnątrz. <br>Zobacz więcej przykładów w [przykładach filtru folderów i plików](#folder-and-file-filter-examples). | Nie                                            |
| symboli wieloznacznychFileName         | Nazwa pliku ze znakami wieloznacznymi pod danym folderpath/wildcardFolderPath do filtrowania plików źródłowych. <br>Dozwolone symbole wieloznaczne to: `*` (dopasowuje zero lub więcej znaków) i `?` (pasuje do zera lub pojedynczego znaku); użyj, `^` aby uciec, jeśli rzeczywista nazwa folderu ma symbol wieloznaczny lub ten znak ucieczki wewnątrz.  Zobacz więcej przykładów w [przykładach filtru folderów i plików](#folder-and-file-filter-examples). | Tak, `fileName` jeśli nie jest określony w zestawie danych |
| modifiedDatetimeStart    | Filtr plików na podstawie atrybutu: Ostatnia modyfikacja. Pliki zostaną wybrane, jeśli ich ostatni zmodyfikowany czas `modifiedDatetimeStart` `modifiedDatetimeEnd`mieści się w przedziale czasu między i . Czas jest stosowany do strefy czasowej UTC w formacie "2018-12-01T05:00:00Z". <br> Właściwości mogą mieć wartość NULL, co oznacza, że do zestawu danych nie zostanie zastosowany żaden filtr atrybutów pliku.  Gdy `modifiedDatetimeStart` ma wartość `modifiedDatetimeEnd` datetime, ale jest null, oznacza to, że pliki, których ostatni zmodyfikowany atrybut jest większa lub równa z wartości datetime zostaną wybrane.  Gdy `modifiedDatetimeEnd` ma wartość `modifiedDatetimeStart` datetime, ale jest null, oznacza to, że pliki, których ostatni zmodyfikowany atrybut jest mniejsza niż wartość datetime zostaną wybrane. | Nie                                            |
| modifiedDatetimeEnd      | Tak samo jak powyżej.                                               | Nie                                            |
| distcpSettings | Grupa właściwości podczas korzystania z DistCp usługi HDFS. | Nie |
| resourceManagerEndpoint | Punkt końcowy Menedżera zasobów Yarn | Tak w przypadku korzystania z DistCp |
| ścieżka tempScriptPath | Ścieżka folderu używana do przechowywania skryptu polecenia temp DistCp. Plik skryptu jest generowany przez fabrykę danych i zostanie usunięty po zakończeniu zadania kopiowania. | Tak w przypadku korzystania z DistCp |
| distcpOpcje | Dodatkowe opcje dostępne dla polecenia DistCp. | Nie |
| maxConcurrentConnections (Połączenie maksymalne) | Liczba połączeń do łączenia się z magazynem magazynowym jednocześnie. Określ tylko wtedy, gdy chcesz ograniczyć jednoczesne połączenie z magazynem danych. | Nie                                            |

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

W tej sekcji opisano wynikowe zachowanie ścieżki folderu i nazwy pliku za pomocą filtrów wieloznacznych.

| folderPath | fileName             | Cykliczne | Struktura folderów źródłowych i wynik filtru (pliki **pogrubione** są pobierane) |
| :--------- | :------------------- | :-------- | :----------------------------------------------------------- |
| `Folder*`  | (pusty, użyj domyślnie) | false     | FolderA ( folderA )<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Plik1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Plik2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Podfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik5.csv<br/>InnyFolderb<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik 6.csv |
| `Folder*`  | (pusty, użyj domyślnie) | true      | FolderA ( folderA )<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Plik1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Plik2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Podfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Plik3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Plik4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Plik5.csv**<br/>InnyFolderb<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik 6.csv |
| `Folder*`  | `*.csv`              | false     | FolderA ( folderA )<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Plik1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Podfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik5.csv<br/>InnyFolderb<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik 6.csv |
| `Folder*`  | `*.csv`              | true      | FolderA ( folderA )<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Plik1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Podfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Plik3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Plik5.csv**<br/>InnyFolderb<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik 6.csv |

## <a name="use-distcp-to-copy-data-from-hdfs"></a>Kopiowanie danych z usługi HDFS za pomocą protokołu DistCp

[DistCp](https://hadoop.apache.org/docs/current3/hadoop-distcp/DistCp.html) jest natywnym narzędziem wiersza polecenia Hadoop do kopiowania rozproszonego w klastrze Hadoop. Po uruchomieniu polecenia Distcp najpierw wyświetli listę wszystkich plików do skopiowania, utworzy kilka zadań mapy w klastrze Hadoop, a każde zadanie mapy wykona kopię binarną ze źródła do ujścia.

Kopiuj obsługę działań przy użyciu DistCp do kopiowania plików w stanie takim, w jakim jest w obszarze Blob platformy Azure (w tym [kopia etapowa)](copy-activity-performance.md)lub w sklepie Azure Data Lake Store, w którym to przypadku może w pełni wykorzystać moc klastra zamiast działać w własnym czasie wykonywania integracji. Zapewni lepszą przepływność kopiowania, zwłaszcza jeśli klaster jest bardzo wydajny. Na podstawie konfiguracji w usłudze Azure Data Factory, skopiuj działanie automatycznie utworzyć polecenie distcp, przesłać do klastra Hadoop i monitorować stan kopiowania.

### <a name="prerequisites"></a>Wymagania wstępne

Aby użyć protokołu DistCp do kopiowania plików z usługi HDFS do obiektu Blob platformy Azure (w tym kopii etapowej) lub usługi Azure Data Lake Store, upewnij się, że klaster Hadoop spełnia poniższe wymagania:

1. Usługi MapReduce i Yarn są włączone.
2. Wersja przędzy jest 2.5 lub wyższa.
3. Serwer HDFS jest zintegrowany z docelowym magazynem danych — obiektem blob platformy Azure lub sklepem Usługi Azure Data Lake Store:

    - Usługa Azure Blob FileSystem jest natywnie obsługiwana od czasu usługi Hadoop 2.7. Wystarczy określić ścieżkę jar w uduszeniu env config.
    - Usługa Azure Data Lake Store FileSystem jest pakowany począwszy od Usługi Hadoop 3.0.0-alpha1. Jeśli klaster Hadoop jest niższy niż ta wersja, należy ręcznie zaimportować pakiety jar związane z usługą ADLS (azure-datalake-store.jar) do [klastra w tym miejscu](https://hadoop.apache.org/releases.html)i określić ścieżkę jar w usłudze Hadoop env config.

4. Przygotuj folder tymczasowy w hdfs. Ten folder tymczasowy jest używany do przechowywania skryptu powłoki DistCp, dzięki czemu zajmuje miejsce na poziomie KB.
5. Upewnij się, że konto użytkownika podane w usłudze połączonej HDFS ma uprawnienia do a) składania wniosków w Yarn; b) mają uprawnienia do tworzenia podfolderów, odczytu / zapisu plików w powyższym folderze tymczasowym.

### <a name="configurations"></a>Konfiguracje

Zobacz konfiguracje związane z DistCp i przykłady w [hdfs jako sekcji źródłowej.](#hdfs-as-source)

## <a name="use-kerberos-authentication-for-hdfs-connector"></a>Używanie uwierzytelniania Kerberos dla złącza HDFS

Istnieją dwie opcje konfigurowania środowiska lokalnego, tak aby używać uwierzytelniania Kerberos w łączniku HDFS. Możesz wybrać ten, który lepiej pasuje do Twojej sprawy.
* Opcja 1: [Dołącz do samodzielnego współorganizowanego komputera wykonawczego integracji w królestwie Kerberos](#kerberos-join-realm)
* Opcja 2: [Włączanie wzajemnego zaufania między domeną systemu Windows a domeną Protokołu Kerberos](#kerberos-mutual-trust)

### <a name="option-1-join-self-hosted-integration-runtime-machine-in-kerberos-realm"></a><a name="kerberos-join-realm"></a>Opcja 1: Dołącz do samodzielnego współorganizowanego komputera wykonawczego integracji w królestwie Kerberos

#### <a name="requirements"></a>Wymagania

* Komputer środowiska wykonawczego integracji hostowanego samodzielnie musi dołączyć do obszaru Kerberos i nie może dołączyć do żadnej domeny systemu Windows.

#### <a name="how-to-configure"></a>Jak skonfigurować

**Na komputerze środowiska wykonawczego integracji hostowanego samodzielnie:**

1.  Uruchom narzędzie **Ksetup,** aby skonfigurować serwer i obszar KDC protokołu Kerberos.

    Komputer musi być skonfigurowany jako członek grupy roboczej, ponieważ obszar Protokołu Kerberos różni się od domeny systemu Windows. Można to osiągnąć, ustawiając obszar Kerberos i dodając serwer centrum dystrybucji danych w następujący sposób. W razie potrzeby wymień *REALM.COM* na własne, odpowiednie królestwo.

            C:> Ksetup /setdomain REALM.COM
            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>

    **Uruchom ponownie** komputer po wykonaniu tych 2 poleceń.

2.  Sprawdź konfigurację za pomocą polecenia **Ksetup.** Dane wyjściowe powinny być takie:

            C:> Ksetup
            default realm = REALM.COM (external)
            REALM.com:
                kdc = <your_kdc_server_address>

**W fabryce danych platformy Azure:**

* Skonfiguruj łącznik HDFS przy użyciu **uwierzytelniania systemu Windows** wraz z główną nazwą i hasłem protokołu Kerberos, aby połączyć się ze źródłem danych HDFS. Sprawdź sekcję [Właściwości usługi połączonej usługi HDFS,](#linked-service-properties) aby uzyskać szczegółowe informacje o konfiguracji.

### <a name="option-2-enable-mutual-trust-between-windows-domain-and-kerberos-realm"></a><a name="kerberos-mutual-trust"></a>Opcja 2: Włączanie wzajemnego zaufania między domeną systemu Windows a domeną Protokołu Kerberos

#### <a name="requirements"></a>Wymagania

*   Komputer środowiska wykonawczego integracji hostowanego samodzielnie musi dołączyć do domeny systemu Windows.
*   Aby zaktualizować ustawienia kontrolera domeny, potrzebne jest uprawnienie.

#### <a name="how-to-configure"></a>Jak skonfigurować

> [!NOTE]
> W razie potrzeby zamień REALM.COM i AD.COM na własny odpowiedni sferę i kontroler domeny.

**Na serwerze KDC:**

1. Edytuj konfigurację centrum dystrybucji kluczy w pliku **krb5.conf,** aby umożliwić administratorowi usługi Windows zaufanie do domeny systemu Windows w odniesieniu do następującego szablonu konfiguracji. Domyślnie konfiguracja znajduje się na **/etc/krb5.conf**.

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

   **Uruchom ponownie** usługę centrum dystrybucji kluczy po zakończeniu konfiguracji.

2. Przygotuj podmiot o nazwie **krbtgt/REALM.COM\@AD.COM** na serwerze KDC za pomocą następującego polecenia:

           Kadmin> addprinc krbtgt/REALM.COM@AD.COM

3. W pliku konfiguracyjnym usługi **hadoop.security.auth_to_local,** dodaj `RULE:[1:$1@$0](.*\@AD.COM)s/\@.*//`.

**Na kontrolerze domeny:**

1.  Uruchom następujące polecenia **Ksetup,** aby dodać wpis obszaru:

            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
            C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM

2.  Ustanawianie zaufania z domeny systemu Windows do obszaru Kerberos. [hasło] to hasło do głównego **krbtgt/REALM.COM\@AD.COM**.

            C:> netdom trust REALM.COM /Domain: AD.COM /add /realm /passwordt:[password]

3.  Wybierz algorytm szyfrowania używany w protokołu Kerberos.

    1. Przejdź do usługi Menedżer serwera > zarządzanie zasadami grupy > obiekty zasad grupy > domeny > domyślne lub aktywne zasady domeny oraz edytuj.

    2. W oknie **podręcznym Edytor zarządzania zasadami grupy** przejdź do pozycji Konfiguracja komputera > Zasady > ustawienia systemu Windows > ustawienia zabezpieczeń > zasady lokalne > opcje zabezpieczeń i skonfiguruj **zabezpieczenia sieciowe: Konfigurowanie typów szyfrowania dozwolonych dla protokołu Kerberos**.

    3. Wybierz algorytm szyfrowania, którego chcesz użyć podczas łączenia się z kedz. Często można po prostu wybrać wszystkie opcje.

        ![Typy szyfrowania konfiguracji dla protokołu Kerberos](media/connector-hdfs/config-encryption-types-for-kerberos.png)

    4. Użyj polecenia **Ksetup,** aby określić algorytm szyfrowania, który ma być używany w określonym pliku REALM.

                C:> ksetup /SetEncTypeAttr REALM.COM DES-CBC-CRC DES-CBC-MD5 RC4-HMAC-MD5 AES128-CTS-HMAC-SHA1-96 AES256-CTS-HMAC-SHA1-96

4.  Utwórz mapowanie między kontem domeny a podmiotem zabezpieczeń Protokołu Kerberos, aby używać podmiotu zabezpieczeń Protokołu Kerberos w domenie systemu Windows.

    1. Uruchom narzędzia administracyjne > **użytkowników i komputerów usługi Active Directory**.

    2. Skonfiguruj zaawansowane funkcje, klikając pozycję **Wyświetl** > **funkcje zaawansowane**.

    3. Znajdź konto, na którym chcesz utworzyć mapowania, a następnie kliknij prawym przyciskiem myszy, aby wyświetlić **mapowania nazw** > kliknij kartę **Nazwy protokołu Kerberos.**

    4. Dodaj podmiot z obszaru.

        ![Tożsamość zabezpieczeń mapy](media/connector-hdfs/map-security-identity.png)

**Na komputerze środowiska wykonawczego integracji hostowanego samodzielnie:**

* Uruchom następujące polecenia **Ksetup,** aby dodać wpis obszaru.

            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
            C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM

**W fabryce danych platformy Azure:**

* Skonfiguruj łącznik HDFS przy użyciu **uwierzytelniania systemu Windows** wraz z kontem domeny lub podmiotem zabezpieczeń Kerberos, aby połączyć się ze źródłem danych HDFS. Sprawdź sekcję [Właściwości usługi połączonej usługi HDFS,](#linked-service-properties) aby uzyskać szczegółowe informacje o konfiguracji.

## <a name="lookup-activity-properties"></a>Właściwości działania odnośnika

Aby dowiedzieć się więcej o właściwościach, sprawdź [działanie odnośnika](control-flow-lookup-activity.md).

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

### <a name="legacy-copy-activity-source-model"></a>Starszy model źródła działania kopiowania

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość typu źródła działania kopiowania musi być ustawiona na: **HdfsSource** |Tak |
| Cykliczne | Wskazuje, czy dane są odczytywane cyklicznie z podfolderów, czy tylko z określonego folderu. Uwaga, gdy rekursywny jest ustawiony na true i sink jest magazyn oparty na plikach, pusty folder /podfolder nie zostaną skopiowane / utworzone w zlewie.<br/>Dozwolone wartości to: **true** (default), **false** | Nie |
| distcpSettings | Grupa właściwości podczas korzystania z DistCp usługi HDFS. | Nie |
| resourceManagerEndpoint | Punkt końcowy Menedżera zasobów Yarn | Tak w przypadku korzystania z DistCp |
| ścieżka tempScriptPath | Ścieżka folderu używana do przechowywania skryptu polecenia temp DistCp. Plik skryptu jest generowany przez fabrykę danych i zostanie usunięty po zakończeniu zadania kopiowania. | Tak w przypadku korzystania z DistCp |
| distcpOpcje | Dodatkowe opcje dostępne dla polecenia DistCp. | Nie |
| maxConcurrentConnections (Połączenie maksymalne) | Liczba połączeń do łączenia się z magazynem magazynowym jednocześnie. Określ tylko wtedy, gdy chcesz ograniczyć jednoczesne połączenie z magazynem danych. | Nie |

**Przykład: źródło HDFS w aktywności kopiowania przy użyciu DistCp**

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
Aby uzyskać listę magazynów danych obsługiwanych jako źródła i pochłaniacze przez działanie kopiowania w usłudze Azure Data Factory, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
