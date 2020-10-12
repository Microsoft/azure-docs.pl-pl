---
title: Przenoszenie danych z lokalnego systemu plików HDFS
description: Dowiedz się więcej na temat przenoszenia danych z lokalnego systemu plików HDFS przy użyciu Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: 3215b82d-291a-46db-8478-eac1a3219614
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: e3f158bb4e8208d00fdfbc44b4afaf067183b6d2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86087320"
---
# <a name="move-data-from-on-premises-hdfs-using-azure-data-factory"></a>Przenoszenie danych z lokalnego systemu plików HDFS przy użyciu Azure Data Factory
> [!div class="op_single_selector" title1="Wybierz używaną wersję usługi Data Factory:"]
> * [Wersja 1](data-factory-hdfs-connector.md)
> * [Wersja 2 (bieżąca wersja)](../connector-hdfs.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącej wersji usługi Data Factory, zobacz temat łącznik systemu plików [HDFS w wersji 2](../connector-hdfs.md).

W tym artykule wyjaśniono, jak za pomocą działania kopiowania w Azure Data Factory przenieść dane z lokalnego systemu plików HDFS. Jest on używany w artykule dotyczącym [przenoszenia danych](data-factory-data-movement-activities.md) , który przedstawia ogólne omówienie przenoszenia danych za pomocą działania kopiowania.

Dane z systemu plików HDFS można kopiować do dowolnego obsługiwanego magazynu danych ujścia. Listę magazynów danych obsługiwanych jako ujścia przez działanie kopiowania można znaleźć w tabeli [obsługiwane magazyny danych](data-factory-data-movement-activities.md#supported-data-stores-and-formats) . Fabryka danych obsługuje obecnie tylko przeniesienie danych z lokalnego systemu plików HDFS do innych magazynów danych, ale nie do przeniesienia danych z innych magazynów danych do lokalnego systemu plików HDFS.

> [!NOTE]
> Działanie kopiowania nie usuwa pliku źródłowego po pomyślnym skopiowaniu do miejsca docelowego. Jeśli musisz usunąć plik źródłowy po pomyślnej kopii, Utwórz niestandardowe działanie, aby usunąć plik i użyć działania w potoku. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="enabling-connectivity"></a>Włączanie łączności
Usługa Data Factory obsługuje łączenie się z lokalnym systemem plików HDFS przy użyciu bramy Zarządzanie danymi. Zobacz temat [przeniesienie danych między lokalizacjami lokalnymi i artykułem w chmurze](data-factory-move-data-between-onprem-and-cloud.md) , aby dowiedzieć się więcej na temat bramy zarządzanie danymi i instrukcje krok po kroku dotyczące konfigurowania bramy. Użyj bramy, aby połączyć się z systemem plików HDFS nawet wtedy, gdy jest ona hostowana na maszynie wirtualnej IaaS platformy Azure.

> [!NOTE]
> Upewnij się, że brama Zarządzanie danymi może uzyskać dostęp do **wszystkich** elementów [nazwa węzła Server]: [nazwa węzła port] i [serwery węzłów danych]: [port węzła danych] klastra Hadoop. Domyślny [nazwa portu węzła] to 50070, a domyślny [port węzła danych] to 50075.

Aby zainstalować bramę na tej samej maszynie lokalnej lub w maszynie wirtualnej platformy Azure jako systemu plików HDFS, zalecamy zainstalowanie bramy na oddzielnej maszynie wirtualnej lub na platformie Azure IaaS. Brama na oddzielnej maszynie zmniejsza rywalizację o zasoby i zwiększa wydajność. Po zainstalowaniu bramy na oddzielnym komputerze komputer powinien mieć możliwość uzyskania dostępu do komputera z systemem plików HDFS.

## <a name="getting-started"></a>Wprowadzenie
Można utworzyć potok za pomocą działania kopiowania, które przenosi dane ze źródła HDFS przy użyciu różnych narzędzi/interfejsów API.

Najprostszym sposobem utworzenia potoku jest użycie **Kreatora kopiowania**. Zobacz [Samouczek: Tworzenie potoku za pomocą Kreatora kopiowania](data-factory-copy-data-wizard-tutorial.md) na potrzeby szybkiego instruktażu dotyczącego tworzenia potoku przy użyciu Kreatora kopiowania danych.

Za pomocą następujących narzędzi można także utworzyć potok: **Azure Portal**, **Visual Studio**, **Azure POWERSHELL**, **szablon Azure Resource Manager**, **interfejs API platformy .NET**i **interfejs API REST**. Aby uzyskać instrukcje krok po kroku dotyczące tworzenia potoku za pomocą działania kopiowania, zobacz [Samouczek dotyczący działania kopiowania](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) .

Niezależnie od tego, czy używasz narzędzi, czy interfejsów API, wykonaj następujące kroki, aby utworzyć potok służący do przenoszenia danych ze źródłowego magazynu danych do magazynu danych ujścia:

1. Utwórz **połączone usługi** , aby połączyć magazyny danych wejściowych i wyjściowych z fabryką danych.
2. Utwórz **zestawy** danych, aby reprezentować dane wejściowe i wyjściowe dla operacji kopiowania.
3. Utwórz **potok** z działaniem kopiowania, które pobiera zestaw danych jako dane wejściowe i zestaw danych jako dane wyjściowe.

Gdy używasz Kreatora, definicje JSON dla tych Data Factory jednostek (połączone usługi, zestawy danych i potok) są automatycznie tworzone. Korzystając z narzędzi/interfejsów API (z wyjątkiem interfejsu API .NET), należy zdefiniować te Data Factory jednostki przy użyciu formatu JSON.  Aby uzyskać przykład z definicjami JSON dla Data Factory jednostek, które są używane do kopiowania danych z magazynu danych HDFS, zobacz [przykład JSON: kopiowanie danych z lokalnego systemu plików HDFS do usługi Azure Blob](#json-example-copy-data-from-on-premises-hdfs-to-azure-blob) w tym artykule.

Poniższe sekcje zawierają szczegółowe informacje na temat właściwości JSON, które są używane do definiowania jednostek Data Factory specyficznych dla systemu plików HDFS:

## <a name="linked-service-properties"></a>Właściwości połączonej usługi
Połączona usługa łączy magazyn danych z fabryką danych. Utworzysz połączoną usługę typu **HDFS** , aby połączyć lokalny system plików HDFS z fabryką danych. Poniższa tabela zawiera opis elementów JSON specyficznych dla połączonej usługi systemu plików HDFS.

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| typ |Właściwość Type musi mieć wartość: **HDFS** |Tak |
| url |Adres URL systemu plików HDFS |Tak |
| authenticationType |Anonimowe lub Windows. <br><br> Aby skorzystać z **uwierzytelniania Kerberos** dla łącznika HDFS, zapoznaj się z [tą sekcją](#use-kerberos-authentication-for-hdfs-connector) , aby odpowiednio skonfigurować środowisko lokalne. |Tak |
| userName |Nazwa użytkownika dla uwierzytelniania systemu Windows. W przypadku uwierzytelniania Kerberos Określ `<username>@<domain>.com` . |Tak (w przypadku uwierzytelniania systemu Windows) |
| hasło |Hasło do uwierzytelniania systemu Windows. |Tak (w przypadku uwierzytelniania systemu Windows) |
| gatewayName |Nazwa bramy, która ma być używana przez usługę Data Factory do łączenia się z systemem plików HDFS. |Tak |
| encryptedCredential |[Nowe-AzDataFactoryEncryptValue](https://docs.microsoft.com/powershell/module/az.datafactory/new-azdatafactoryencryptvalue) dane wyjściowe poświadczeń dostępu. |Nie |

### <a name="using-anonymous-authentication"></a>Korzystanie z uwierzytelniania anonimowego

```JSON
{
    "name": "hdfs",
    "properties":
    {
        "type": "Hdfs",
        "typeProperties":
        {
            "authenticationType": "Anonymous",
            "userName": "hadoop",
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "gatewayName": "mygateway"
        }
    }
}
```

### <a name="using-windows-authentication"></a>Korzystanie z uwierzytelniania systemu Windows

```JSON
{
    "name": "hdfs",
    "properties":
    {
        "type": "Hdfs",
        "typeProperties":
        {
            "authenticationType": "Windows",
            "userName": "<username>@<domain>.com (for Kerberos auth)",
            "password": "password",
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "gatewayName": "mygateway"
        }
    }
}
```
## <a name="dataset-properties"></a>Właściwości zestawu danych
Aby uzyskać pełną listę sekcji & właściwości dostępne do definiowania zestawów danych, zobacz artykuł [Tworzenie zestawów danych](data-factory-create-datasets.md) . Sekcje, takie jak struktura, dostępność i zasady JSON zestawu danych są podobne dla wszystkich typów zestawu danych (Azure SQL, Azure Blob, Azure Table itp.).

Sekcja **typeProperties** jest inna dla każdego typu zestawu danych i zawiera informacje dotyczące lokalizacji danych w magazynie danych. Sekcja typeProperties dla zestawu **danych typu,** który zawiera zestaw danych HDFS, ma następujące właściwości

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| folderPath |Ścieżka do folderu. Przykład: `myfolder`<br/><br/>Użyj znaku ucieczki "\" dla znaków specjalnych w ciągu. Na przykład: dla folder\subfolder określ podfolder folderu \\ \\ i dla d:\samplefolder, określ d: \\ \\ samplefolder.<br/><br/>Możesz połączyć tę właściwość z **partitionBy** , aby mieć ścieżki folderu na podstawie daty rozpoczęcia/zakończenia wycinka. |Tak |
| fileName |Określ nazwę pliku w **folderPath** , jeśli chcesz, aby tabela odnosiła się do określonego pliku w folderze. Jeśli nie określisz żadnej wartości dla tej właściwości, tabela wskazuje wszystkie pliki w folderze.<br/><br/>Jeśli nie określono nazwy pliku wyjściowego zestawu danych, nazwa wygenerowanego pliku będzie w następującym formacie: <br/><br/>`Data.<Guid>.txt` (na przykład:: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Nie |
| partitionedBy |partitionedBy można użyć, aby określić dynamiczny folderPath, filename dla danych szeregów czasowych. Przykład: folderPath sparametryzowane dla każdej godziny danych. |Nie |
| format | Obsługiwane są następujące typy formatów: **TextFormat**, **formatu jsonformat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Ustaw właściwość **Type** w polu Format na jedną z tych wartości. Aby uzyskać więcej informacji, zobacz [format tekstowy](data-factory-supported-file-and-compression-formats.md#text-format), [Format JSON](data-factory-supported-file-and-compression-formats.md#json-format), [Format Avro](data-factory-supported-file-and-compression-formats.md#avro-format), [Format Orc](data-factory-supported-file-and-compression-formats.md#orc-format)i sekcje [formatu Parquet](data-factory-supported-file-and-compression-formats.md#parquet-format) . <br><br> Jeśli chcesz **skopiować pliki** między magazynami opartymi na plikach (kopia binarna), Pomiń sekcję format w definicjach zestawu danych wejściowych i wyjściowych. |Nie |
| kompresja | Określ typ i poziom kompresji danych. Obsługiwane typy to: **gzip**, **Wklęśnięcie**, **BZip2**i **ZipDeflate**. Obsługiwane poziomy to: **optymalne** i **najszybszy**. Aby uzyskać więcej informacji, zobacz [formaty plików i kompresji w Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Nie |

> [!NOTE]
> nazwy pliku i fileFilter nie można jednocześnie używać.

### <a name="using-partionedby-property"></a>Używanie właściwości partionedBy
Jak wspomniano w poprzedniej sekcji, można określić dynamiczne folderPath i nazwa pliku dla danych szeregów czasowych z właściwością **partitionedBy** , [funkcjami Data Factory i zmiennymi systemowymi](data-factory-functions-variables.md).

Aby dowiedzieć się więcej o zestawach danych, planowaniu i wycinkach szeregów czasowych, zobacz [Tworzenie zestawów danych](data-factory-create-datasets.md), [Planowanie & wykonywania](data-factory-scheduling-and-execution.md)i [Tworzenie artykułów potoków](data-factory-create-pipelines.md) .

#### <a name="sample-1"></a>Przykład 1:

```JSON
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```
W tym przykładzie {Slice} jest zastępowana wartością zmiennej systemowej Data Factory parametru slicestart w formacie (YYYYMMDDHH). Parametru slicestart odnosi się do czasu rozpoczęcia wycinka. FolderPath różni się dla każdego wycinka. Na przykład: wikidatagateway/wikisampledataout/2014100103 lub wikidatagateway/wikisampledataout/2014100104.

#### <a name="sample-2"></a>Przykład 2:

```JSON
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

Natomiast właściwości dostępne w sekcji typeProperties działania różnią się w zależności od typu działania. W przypadku działania kopiowania różnią się w zależności od typów źródeł i ujścia.

W przypadku działania kopiowania, gdy źródło jest typu **FileSystemSource** , w sekcji typeProperties są dostępne następujące właściwości:

**FileSystemSource** obsługuje następujące właściwości:

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| rozpoznawania |Wskazuje, czy dane są odczytane cyklicznie z podfolderów, czy tylko z określonego folderu. |Prawda, FAŁSZ (wartość domyślna) |Nie |

## <a name="supported-file-and-compression-formats"></a>Obsługiwane formaty plików i kompresji
Aby uzyskać szczegółowe informacje [, zobacz formaty plików i kompresji w artykule Azure Data Factory](data-factory-supported-file-and-compression-formats.md) .

## <a name="json-example-copy-data-from-on-premises-hdfs-to-azure-blob"></a>Przykład JSON: kopiowanie danych z lokalnego systemu plików HDFS do obiektu blob platformy Azure
Ten przykład pokazuje, jak skopiować dane z lokalnego systemu plików HDFS na platformę Azure Blob Storage. Dane można jednak skopiować **bezpośrednio** do dowolnego ujścia w [tym miejscu](data-factory-data-movement-activities.md#supported-data-stores-and-formats) za pomocą działania kopiowania w Azure Data Factory.  

Przykład zawiera definicje JSON dla następujących jednostek Data Factory. Za pomocą tych definicji można utworzyć potok służący do kopiowania danych z systemu plików HDFS na platformę Azure Blob Storage przy użyciu [programu Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) lub [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md).

1. Połączona usługa typu [OnPremisesHdfs](#linked-service-properties).
2. Połączona usługa typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Wejściowy [zestaw danych](data-factory-create-datasets.md) typu [udziału](#dataset-properties).
4. Wyjściowy [zestaw danych](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. [Potok](data-factory-create-pipelines.md) z działaniem kopiowania korzystającym z [FileSystemSource](#copy-activity-properties) i [wartość blobsink](data-factory-azure-blob-connector.md#copy-activity-properties).

Przykład kopiuje dane z lokalnego systemu plików HDFS do obiektu blob platformy Azure co godzinę. Właściwości JSON używane w tych przykładach są opisane w sekcjach poniżej przykładów.

Najpierw należy skonfigurować bramę zarządzania danymi. Instrukcje dotyczące [przemieszczania danych między lokalizacjami lokalnymi i artykułem w chmurze](data-factory-move-data-between-onprem-and-cloud.md) .

**Połączona usługa systemu plików HDFS:** W tym przykładzie jest stosowane uwierzytelnianie systemu Windows. Zapoznaj się z sekcją [połączonej usługi HDFS](#linked-service-properties) dla różnych typów uwierzytelniania, których można użyć.

```JSON
{
    "name": "HDFSLinkedService",
    "properties":
    {
        "type": "Hdfs",
        "typeProperties":
        {
            "authenticationType": "Windows",
            "userName": "Administrator",
            "password": "password",
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "gatewayName": "mygateway"
        }
    }
}
```

**Połączona usługa Azure Storage:**

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

**Wejściowy zestaw danych systemu plików HDFS:** Ten zestaw danych odwołuje się do folderu HDFS DataTransfer/testu jednostkowego/. Potok kopiuje wszystkie pliki z tego folderu do miejsca docelowego.

Ustawienie "External": "true" informuje usługę Data Factory, że zestaw danych jest zewnętrzny względem fabryki danych i nie jest tworzony przez działanie w fabryce danych.

```JSON
{
    "name": "InputDataset",
    "properties": {
        "type": "FileShare",
        "linkedServiceName": "HDFSLinkedService",
        "typeProperties": {
            "folderPath": "DataTransfer/UnitTest/"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval":  1
        }
    }
}
```

**Wyjściowy zestaw danych obiektów blob platformy Azure:**

Dane są zapisywane w nowym obiekcie blob co godzinę (częstotliwość: godzina, interwał: 1). Ścieżka folderu dla obiektu BLOB jest obliczana dynamicznie na podstawie czasu rozpoczęcia przetwarzanego wycinka. Ścieżka folderu używa części roku, miesiąca, dnia i godziny rozpoczęcia.

```JSON
{
    "name": "OutputDataset",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/hdfs/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

**Działanie kopiowania w potoku ze źródłem systemu plików i obiektem sink obiektów blob:**

Potok zawiera działanie kopiowania, które jest skonfigurowane do używania tych wejściowych i wyjściowych zestawów danych i zaplanowane do uruchomienia co godzinę. W definicji JSON potoku typ **źródła** ma wartość **FileSystemSource** , a typ **ujścia** to **wartość blobsink**. Zapytanie SQL określone dla właściwości **zapytania** wybiera dane w ciągu ostatniej godziny do skopiowania.

```JSON
{
    "name": "pipeline",
    "properties":
    {
        "activities":
        [
            {
                "name": "HdfsToBlobCopy",
                "inputs": [ {"name": "InputDataset"} ],
                "outputs": [ {"name": "OutputDataset"} ],
                "type": "Copy",
                "typeProperties":
                {
                    "source":
                    {
                        "type": "FileSystemSource"
                    },
                    "sink":
                    {
                        "type": "BlobSink"
                    }
                },
                "policy":
                {
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1,
                    "timeout": "00:05:00"
                }
            }
        ],
        "start": "2014-06-01T18:00:00Z",
        "end": "2014-06-01T19:00:00Z"
    }
}
```

## <a name="use-kerberos-authentication-for-hdfs-connector"></a>Używanie uwierzytelniania Kerberos dla łącznika HDFS
Dostępne są dwie opcje konfigurowania środowiska lokalnego w taki sposób, aby korzystały z uwierzytelniania Kerberos w ramach łącznika systemu plików HDFS. Możesz wybrać ten, który lepiej pasuje do Twojego przypadku.
* Opcja 1: [dołączanie maszyny bramy w obszarze Kerberos](#kerberos-join-realm)
* Opcja 2: [Włącz wzajemne zaufanie między domeną systemu Windows i obszarem Kerberos](#kerberos-mutual-trust)

### <a name="option-1-join-gateway-machine-in-kerberos-realm"></a><a name="kerberos-join-realm"></a>Opcja 1: dołączanie maszyny bramy w obszarze Kerberos

#### <a name="requirement"></a>Wymog

* Komputer bramy musi przyłączyć obszar Kerberos i nie może dołączać do żadnej domeny systemu Windows.

#### <a name="how-to-configure"></a>Jak skonfigurować:

**Na maszynie bramy:**

1.  Uruchom narzędzie **Ksetup** , aby skonfigurować serwer i obszar KDC protokołu Kerberos.

    Komputer musi być skonfigurowany jako członek grupy roboczej, ponieważ obszar Kerberos różni się od domeny systemu Windows. Można to osiągnąć przez ustawienie obszaru protokołu Kerberos i dodanie serwera KDC w następujący sposób. Zastąp *REALM.com* własnymi obszarami w miarę potrzeb.

    ```cmd
    C:> Ksetup /setdomain REALM.COM
    C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
    ```

    Po wykonaniu tych dwóch poleceń **ponownie uruchom** maszynę.

2.  Sprawdź konfigurację przy użyciu polecenia **Ksetup** . Dane wyjściowe powinny wyglądać następująco:

    ```cmd
    C:> Ksetup
    default realm = REALM.COM (external)
    REALM.com:
        kdc = <your_kdc_server_address>
        ```

**In Azure Data Factory:**

* Configure the HDFS connector using **Windows authentication** together with your Kerberos principal name and password to connect to the HDFS data source. Check [HDFS Linked Service properties](#linked-service-properties) section on configuration details.

### <a name="kerberos-mutual-trust"></a>Option 2: Enable mutual trust between Windows domain and Kerberos realm

#### Requirement:
*   The gateway machine must join a Windows domain.
*   You need permission to update the domain controller's settings.

#### How to configure:

> [!NOTE]
> Replace REALM.COM and AD.COM in the following tutorial with your own respective realm and domain controller as needed.

**On KDC server:**

1. Edit the KDC configuration in **krb5.conf** file to let KDC trust Windows Domain referring to the following configuration template. By default, the configuration is located at **/etc/krb5.conf**.

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

   Po skonfigurowaniu należy **ponownie uruchomić** usługę centrum dystrybucji kluczy.

2. Przygotuj podmiot zabezpieczeń o nazwie **KRBTGT/REALM. COM \@ AD.com** na serwerze KDC przy użyciu następującego polecenia:

   ```cmd
   Kadmin> addprinc krbtgt/REALM.COM@AD.COM
   ```

3. W pliku konfiguracji usługi **Hadoop.Security.auth_to_local** HDFS Dodaj `RULE:[1:$1@$0](.*\@AD.COM)s/\@.*//` .

**Na kontrolerze domeny:**

1.  Uruchom następujące polecenia **Ksetup** , aby dodać wpis obszaru:

    ```cmd
    C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
    C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM
    ```

2.  Ustanów relację zaufania z domeny systemu Windows do obszaru Kerberos. [hasło] jest hasłem dla głównego elementu **KRBTGT/obszaru. COM \@ AD.com**.

    ```cmd
    C:> netdom trust REALM.COM /Domain: AD.COM /add /realm /passwordt:[password]
    ```

3.  Wybierz algorytm szyfrowania używany w protokole Kerberos.

    1. Przejdź do Menedżer serwera > zasady grupy zarządzanie > domeny > zasady grupy obiektów > domyślnych lub aktywnych zasad domeny i Edytuj.

    2. W oknie podręcznym **Edytor zarządzania zasadami grupy** przejdź do pozycji konfiguracja komputera > zasady > ustawienia systemu Windows > ustawienia zabezpieczeń > zasady lokalne > opcje zabezpieczeń i skonfiguruj **zabezpieczenia sieci: Skonfiguruj typy szyfrowania dozwolone dla protokołu Kerberos**.

    3. Wybierz algorytm szyfrowania, który ma być używany podczas nawiązywania połączenia z centrum dystrybucji kluczy. Zazwyczaj można po prostu wybrać wszystkie opcje.

        ![Typy szyfrowania dla protokołu Kerberos](media/data-factory-hdfs-connector/config-encryption-types-for-kerberos.png)

    4. Użyj polecenia **Ksetup** , aby określić algorytm szyfrowania, który ma być używany w konkretnym obszarze.

       ```cmd
       C:> ksetup /SetEncTypeAttr REALM.COM DES-CBC-CRC DES-CBC-MD5 RC4-HMAC-MD5 AES128-CTS-HMAC-SHA1-96 AES256-CTS-HMAC-SHA1-96
       ```

4.  Utwórz mapowanie między kontem domeny i podmiotem zabezpieczeń protokołu Kerberos, aby użyć podmiotu zabezpieczeń Kerberos w domenie systemu Windows.

    1. Uruchom narzędzia administracyjne > **Active Directory Użytkownicy i komputery**.

    2. Skonfiguruj funkcje zaawansowane, klikając pozycję **Wyświetl**  >  **Zaawansowane funkcje**.

    3. Znajdź konto, do którego chcesz utworzyć mapowania, a następnie kliknij prawym przyciskiem myszy, aby wyświetlić **mapowania nazw** > kliknij kartę **nazwy Kerberos** .

    4. Dodaj podmiot zabezpieczeń z obszaru.

        ![Zamapuj tożsamość zabezpieczeń](media/data-factory-hdfs-connector/map-security-identity.png)

**Na maszynie bramy:**

* Aby dodać wpis obszaru, uruchom następujące polecenia **Ksetup** .

   ```cmd
   C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
   C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM
   ```

**W Azure Data Factory:**

* Skonfiguruj łącznik HDFS przy użyciu **uwierzytelniania systemu Windows** razem z kontem domeny lub podmiotem zabezpieczeń protokołu Kerberos, aby nawiązać połączenie ze źródłem danych HDFS. Sprawdź sekcję [Właściwości połączonej usługi HDFS](#linked-service-properties) w szczegółach konfiguracji.

> [!NOTE]
> Aby zmapować kolumny ze źródłowego zestawu danych do kolumn z obiektu ujścia danych, zobacz [Mapowanie kolumn zestawu danych w Azure Data Factory](data-factory-map-columns.md).


## <a name="performance-and-tuning"></a>Wydajność i dostrajanie
Zobacz [Przewodnik dostrajania wydajności & działania kopiowania](data-factory-copy-activity-performance.md) , aby poznać kluczowe czynniki wpływające na wydajność przenoszenia danych (działanie kopiowania) w Azure Data Factory i różne sposoby jego optymalizacji.
