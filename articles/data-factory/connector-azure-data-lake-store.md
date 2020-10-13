---
title: Kopiuj dane do lub z Azure Data Lake Storage Gen1
description: Informacje o kopiowaniu danych z obsługiwanych magazynów danych źródłowych do Azure Data Lake Store lub z Data Lake Store do obsługiwanych magazynów ujścia przy użyciu Data Factory.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/31/2020
ms.openlocfilehash: 842e3ecae28a23bd294cd0671daecb1adda55dea
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91332206"
---
# <a name="copy-data-to-or-from-azure-data-lake-storage-gen1-using-azure-data-factory"></a>Kopiowanie danych do lub z Azure Data Lake Storage Gen1 przy użyciu Azure Data Factory

> [!div class="op_single_selector" title1="Wybierz używaną wersję Azure Data Factory:"]
>
> * [Wersja 1](v1/data-factory-azure-datalake-connector.md)
> * [Bieżąca wersja](connector-azure-data-lake-store.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule opisano sposób kopiowania danych do i z Azure Data Lake Storage Gen1. Aby dowiedzieć się więcej na temat Azure Data Factory, Przeczytaj [artykuł wprowadzający](introduction.md).

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Ten łącznik Azure Data Lake Storage Gen1 jest obsługiwany dla następujących działań:

- [Działanie kopiowania](copy-activity-overview.md) z [obsługiwaną macierzą źródłową/ujścia](copy-activity-overview.md) 
- [Mapowanie przepływu danych](concepts-data-flow-overview.md)
- [Działanie Lookup](control-flow-lookup-activity.md)
- [Działanie GetMetadata](control-flow-get-metadata-activity.md)
- [Usuń działanie](delete-activity.md)

W przypadku tego łącznika możesz:

- Skopiuj pliki przy użyciu jednej z następujących metod uwierzytelniania: nazwy głównej usługi lub tożsamości zarządzanych dla zasobów platformy Azure.
- Kopiuj pliki jako lub Analizuj lub generuj pliki z [obsługiwanymi formatami plików i koderami-dekoder kompresji](supported-file-formats-and-compression-codecs.md).
- [Zachowaj listy ACL](#preserve-acls-to-data-lake-storage-gen2) podczas kopiowania do Azure Data Lake Storage Gen2.

> [!IMPORTANT]
> W przypadku kopiowania danych przy użyciu własnego środowiska Integration Runtime Skonfiguruj zaporę firmową tak, aby zezwalała na ruch wychodzący do `<ADLS account name>.azuredatalakestore.net` i `login.microsoftonline.com/<tenant>/oauth2/token` na porcie 443. Jest to usługa tokenu zabezpieczającego platformy Azure, z którą musi się komunikować środowisko Integration Runtime w celu uzyskania tokenu dostępu.

## <a name="get-started"></a>Rozpoczęcie pracy

> [!TIP]
> Aby zapoznać się z przewodnikiem dotyczącym korzystania z łącznika Azure Data Lake Store, zobacz [ładowanie danych do Azure Data Lake Store](load-azure-data-lake-store.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają informacje o właściwościach, które są używane do definiowania Data Factory jednostek specyficznych dla Azure Data Lake Store.

## <a name="linked-service-properties"></a>Właściwości połączonej usługi

Następujące właściwości są obsługiwane dla Azure Data Lake Store połączonej usługi:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | `type`Właściwość musi być ustawiona na **AzureDataLakeStore**. | Tak |
| dataLakeStoreUri | Informacje o koncie Azure Data Lake Store. Te informacje mają jeden z następujących formatów: `https://[accountname].azuredatalakestore.net/webhdfs/v1` lub `adl://[accountname].azuredatalakestore.net/` . | Tak |
| subscriptionId | Identyfikator subskrypcji platformy Azure, do której należy konto Data Lake Store. | Wymagane dla ujścia |
| resourceGroupName | Nazwa grupy zasobów platformy Azure, do której należy konto Data Lake Store. | Wymagane dla ujścia |
| Właściwością connectvia | [Środowisko Integration Runtime](concepts-integration-runtime.md) służy do nawiązywania połączenia z magazynem danych. Jeśli magazyn danych znajduje się w sieci prywatnej, możesz użyć środowiska Azure Integration Runtime lub własnego środowiska Integration Runtime. Jeśli ta właściwość nie jest określona, zostanie użyta domyślna usługa Azure Integration Runtime. |Nie |

### <a name="use-service-principal-authentication"></a>Użyj uwierzytelniania nazwy głównej usługi

Aby użyć uwierzytelniania jednostki usługi, wykonaj następujące kroki.

1. Zarejestruj jednostkę aplikacji w Azure Active Directory i Udziel jej dostępu do Data Lake Store. Aby uzyskać szczegółowe instrukcje, zobacz [uwierzytelnianie między usługami](../data-lake-store/data-lake-store-authenticate-using-active-directory.md). Należy zwrócić uwagę na następujące wartości, których można użyć do zdefiniowania połączonej usługi:

    - Identyfikator aplikacji
    - Klucz aplikacji
    - Identyfikator dzierżawy

2. Przyznaj jednostce usługi odpowiednie uprawnienia. Zapoznaj się z przykładami dotyczącymi działania uprawnień w Data Lake Storage Gen1 z poziomu [kontroli dostępu w programie Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md#common-scenarios-related-to-permissions).

    - **Jako źródło**: w programie dostęp do **Eksploratora danych**  >  **Access**Udziel co najmniej uprawnień **Execute** dla wszystkich folderów nadrzędnych, łącznie z uprawnieniami do **odczytu** plików do skopiowania. Można wybrać opcję dodania do **tego folderu i wszystkich elementów podrzędnych** dla opcji cykliczne oraz dodać jako **uprawnienie dostępu i domyślny wpis uprawnienia**. Kontrola dostępu na poziomie konta (IAM) nie jest wymagana.
    - **Jako ujścia**: w oknie dostęp do **Eksploratora danych**  >  **Access**Udziel co najmniej uprawnień **Execute** dla wszystkich folderów nadrzędnych, w tym katalogu głównego, a także uprawnienia do **zapisu** dla folderu ujścia. Można wybrać opcję dodania do **tego folderu i wszystkich elementów podrzędnych** dla opcji cykliczne oraz dodać jako **uprawnienie dostępu i domyślny wpis uprawnienia**.

Obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| servicePrincipalId | Określ identyfikator klienta aplikacji. | Tak |
| servicePrincipalKey | Określ klucz aplikacji. Oznacz to pole jako, `SecureString` Aby bezpiecznie przechowywać je w Data Factory, lub [odwoływać się do wpisu tajnego przechowywanego w Azure Key Vault](store-credentials-in-key-vault.md). | Tak |
| dzierżaw | Określ informacje o dzierżawie, takie jak nazwa domeny lub identyfikator dzierżawy, w której znajduje się aplikacja. Możesz ją pobrać, aktywując wskaźnik myszy w prawym górnym rogu Azure Portal. | Tak |
| azureCloudType | W polu Uwierzytelnianie nazwy głównej usługi Określ typ środowiska chmury platformy Azure, do którego zarejestrowano aplikację Azure Active Directory. <br/> Dozwolone wartości to **AzurePublic**, **AzureChina**, **AzureUsGovernment**i **AzureGermany**. Domyślnie używane jest środowisko chmury fabryki danych. | Nie |

**Przykład:**

```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="use-managed-identities-for-azure-resources-authentication"></a><a name="managed-identity"></a> Korzystanie z tożsamości zarządzanych do uwierzytelniania zasobów platformy Azure

Fabryka danych może być skojarzona z [zarządzaną tożsamością dla zasobów platformy Azure](data-factory-service-identity.md), która reprezentuje tę konkretną fabrykę danych. Tej tożsamości zarządzanej można bezpośrednio użyć do uwierzytelniania Data Lake Store, podobnie jak w przypadku korzystania z własnej nazwy głównej usługi. Umożliwia to wyznaczeniu fabryki dostęp do danych i ich kopiowanie do lub z Data Lake Store.

Aby używać tożsamości zarządzanych do uwierzytelniania zasobów platformy Azure, wykonaj następujące kroki.

1. [Pobierz informacje o tożsamości zarządzanej fabryki danych](data-factory-service-identity.md#retrieve-managed-identity) przez skopiowanie wartości "Identyfikator aplikacji tożsamości usługi", która jest generowana wraz z fabryką.

2. Przyznaj zarządzanej tożsamości dostęp do Data Lake Store. Zapoznaj się z przykładami dotyczącymi działania uprawnień w Data Lake Storage Gen1 z poziomu [kontroli dostępu w programie Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md#common-scenarios-related-to-permissions).

    - **Jako źródło**: w programie dostęp do **Eksploratora danych**  >  **Access**Udziel co najmniej uprawnień **Execute** dla wszystkich folderów nadrzędnych, łącznie z uprawnieniami do **odczytu** plików do skopiowania. Można wybrać opcję dodania do **tego folderu i wszystkich elementów podrzędnych** dla opcji cykliczne oraz dodać jako **uprawnienie dostępu i domyślny wpis uprawnienia**. Kontrola dostępu na poziomie konta (IAM) nie jest wymagana.
    - **Jako ujścia**: w oknie dostęp do **Eksploratora danych**  >  **Access**Udziel co najmniej uprawnień **Execute** dla wszystkich folderów nadrzędnych, w tym katalogu głównego, a także uprawnienia do **zapisu** dla folderu ujścia. Można wybrać opcję dodania do **tego folderu i wszystkich elementów podrzędnych** dla opcji cykliczne oraz dodać jako **uprawnienie dostępu i domyślny wpis uprawnienia**.

W Azure Data Factory nie trzeba określać żadnych właściwości oprócz ogólnych informacji Data Lake Store w połączonej usłudze.

**Przykład:**

```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
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

Następujące właściwości są obsługiwane dla Azure Data Lake Store Gen1 w obszarze `location` Ustawienia w zestawie danych opartym na formacie:

| Właściwość   | Opis                                                  | Wymagane |
| ---------- | ------------------------------------------------------------ | -------- |
| typ       | Właściwość typu w `location` elemencie DataSet musi być ustawiona na **AzureDataLakeStoreLocation**. | Tak      |
| folderPath | Ścieżka do folderu. Jeśli chcesz użyć symbolu wieloznacznego do filtrowania folderów, pomiń to ustawienie i określ je w ustawieniach źródła działania. | Nie       |
| fileName   | Nazwa pliku pod podanym folderPath. Jeśli chcesz użyć symbolu wieloznacznego do filtrowania plików, pomiń to ustawienie i określ je w ustawieniach źródła działania. | Nie       |

**Przykład:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<ADLS Gen1 linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureDataLakeStoreLocation",
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

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania działań, zobacz [potoki](concepts-pipelines-activities.md). Ta sekcja zawiera listę właściwości obsługiwanych przez Azure Data Lake Store źródła i ujścia.

### <a name="azure-data-lake-store-as-source"></a>Azure Data Lake Store jako źródło

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Następujące właściwości są obsługiwane dla Azure Data Lake Store Gen1 w obszarze `storeSettings` Ustawienia w źródle kopiowania opartym na formacie:

| Właściwość                 | Opis                                                  | Wymagane                                     |
| ------------------------ | ------------------------------------------------------------ | -------------------------------------------- |
| typ                     | Właściwość Type w obszarze `storeSettings` musi być ustawiona na wartość **AzureDataLakeStoreReadSettings**. | Tak                                          |
| ***Zlokalizuj pliki do skopiowania:*** |  |  |
| Opcja 1: ścieżka statyczna<br> | Kopiuj z podanego folderu/ścieżki pliku określonego w zestawie danych. Jeśli chcesz skopiować wszystkie pliki z folderu, należy również określić `wildcardFileName` jako `*` . |  |
| Opcja 2: Zakres nazw<br>- listAfter | Pobierz foldery/pliki, których nazwa jest po tej wartości alfabetycznie (wyłącznie). Wykorzystuje filtr po stronie usługi dla ADLS Gen1, który zapewnia lepszą wydajność niż filtr symboli wieloznacznych. <br/>Fabryka danych stosuje ten filtr do ścieżki zdefiniowanej w zestawie danych, a tylko jeden poziom jednostki jest obsługiwany. Zobacz więcej przykładów w [przykładach filtru zakresu nazw](#name-range-filter-examples). | Nie |
| Opcja 2: Zakres nazw<br/>- listBefore | Pobierz foldery/pliki, których nazwa jest wcześniejsza niż ta wartość (włącznie). Wykorzystuje filtr po stronie usługi dla ADLS Gen1, który zapewnia lepszą wydajność niż filtr symboli wieloznacznych.<br>Fabryka danych stosuje ten filtr do ścieżki zdefiniowanej w zestawie danych, a tylko jeden poziom jednostki jest obsługiwany. Zobacz więcej przykładów w [przykładach filtru zakresu nazw](#name-range-filter-examples). | Nie |
| Opcja 3: symbol wieloznaczny<br>- wildcardFolderPath | Ścieżka folderu z symbolami wieloznacznymi do filtrowania folderów źródłowych. <br>Dozwolone symbole wieloznaczne to: `*` (dopasowuje zero lub więcej znaków) i `?` (dopasowuje zero lub pojedynczy znak); Użyj `^` do ucieczki, jeśli rzeczywista nazwa folderu ma symbol wieloznaczny lub ten znak ucieczki wewnątrz. <br>Zobacz więcej przykładów w [przykładach folderów i filtrów plików](#folder-and-file-filter-examples). | Nie                                            |
| Opcja 3: symbol wieloznaczny<br>- wildcardFileName | Nazwa pliku z symbolami wieloznacznymi pod daną folderPath/wildcardFolderPath do filtrowania plików źródłowych. <br>Dozwolone symbole wieloznaczne to: `*` (dopasowuje zero lub więcej znaków) i `?` (dopasowuje zero lub pojedynczy znak); Użyj `^` do ucieczki, jeśli rzeczywista nazwa folderu ma symbol wieloznaczny lub ten znak ucieczki wewnątrz.  Zobacz więcej przykładów w [przykładach folderów i filtrów plików](#folder-and-file-filter-examples). | Tak |
| OPCJA 4: Lista plików<br>- fileListPath | Wskazuje, aby skopiować dany zestaw plików. Wskaż plik tekstowy zawierający listę plików, które chcesz skopiować, jeden plik w wierszu, który jest ścieżką względną do ścieżki skonfigurowanej w zestawie danych.<br/>W przypadku korzystania z tej opcji nie należy określać nazwy pliku w zestawie danych. Zobacz więcej przykładów na [listach plików](#file-list-examples). |Nie |
| ***Ustawienia dodatkowe:*** |  | |
| rozpoznawania | Wskazuje, czy dane są odczytane cyklicznie z podfolderów, czy tylko z określonego folderu. Należy pamiętać, że gdy wartość cykliczna jest ustawiona na wartość true, a ujścia jest magazynem opartym na plikach, pusty folder lub podfolder nie jest kopiowany ani tworzony w ujścia. <br>Dozwolone wartości to **true** (wartość domyślna) i **false**.<br>Ta właściwość nie ma zastosowania podczas konfigurowania `fileListPath` . |Nie |
| deleteFilesAfterCompletion | Wskazuje, czy pliki binarne zostaną usunięte z magazynu źródłowego po pomyślnym przeniesieniu do magazynu docelowego. Plik jest usuwany dla każdego pliku, więc w przypadku niepowodzenia działania kopiowania niektóre pliki zostały już skopiowane do lokalizacji docelowej i usunięte ze źródła, podczas gdy inne nadal pozostają w magazynie źródłowym. <br/>Ta właściwość jest prawidłowa tylko w scenariuszu kopiowania plików binarnych. Wartość domyślna: false. |Nie |
| modifiedDatetimeStart    | Filtr plików oparty na atrybucie: ostatnio modyfikowane. <br>Pliki zostaną wybrane, jeśli czas ostatniej modyfikacji mieści się w przedziale czasu między `modifiedDatetimeStart` i `modifiedDatetimeEnd` . Czas jest stosowany do strefy czasowej UTC w formacie "2018 r-12-01T05:00:00Z". <br> Właściwości mogą mieć wartość NULL, co oznacza, że żaden filtr atrybutu pliku nie zostanie zastosowany do zestawu danych.  Gdy `modifiedDatetimeStart` ma wartość DateTime `modifiedDatetimeEnd` , ale jest wartością null, oznacza to, że pliki, których ostatni zmodyfikowany atrybut jest większy lub równy wartości DateTime, zostaną zaznaczone.  Gdy `modifiedDatetimeEnd` ma wartość DateTime `modifiedDatetimeStart` , ale jest wartością null, oznacza to, że pliki, których ostatni zmodyfikowany atrybut jest mniejszy niż wartość DateTime zostanie wybrana.<br/>Ta właściwość nie ma zastosowania podczas konfigurowania `fileListPath` . | Nie                                            |
| modifiedDatetimeEnd      | Tak samo jak powyżej.                                               | Nie                                           |
| enablePartitionDiscovery | W przypadku plików, które są partycjonowane, określ, czy przeanalizować partycje ze ścieżki pliku i dodać je jako dodatkowe kolumny źródłowe.<br/>Dozwolone wartości to **false** (wartość domyślna) i **true**. | Nie                                            |
| partitionRootPath | Gdy odnajdywanie partycji jest włączone, określ bezwzględną ścieżkę katalogu głównego, aby odczytać partycjonowane foldery jako kolumny danych.<br/><br/>Jeśli nie jest określony, domyślnie,<br/>— Jeśli używasz ścieżki pliku w zestawie danych lub liście plików w źródle, ścieżka katalogu głównego partycji jest ścieżką skonfigurowaną w zestawie danych.<br/>— Jeśli używasz wieloznacznego filtru folderów, ścieżka katalogu głównego partycji jest ścieżką podrzędną przed pierwszym symbolem wieloznacznym.<br/><br/>Na przykład przy założeniu, że ścieżka w zestawie danych jest konfigurowana jako "root/folder/Year = 2020/miesiąc = 08/Day = 27":<br/>— W przypadku określenia ścieżki katalogu głównego partycji jako "root/folder/Year = 2020" działanie Copy (kopiowanie) wygeneruje dwie kolejne kolumny `month` i `day` wartości "08" i "27" (oprócz kolumn wewnątrz plików).<br/>— Jeśli ścieżka katalogu głównego partycji nie zostanie określona, nie zostanie wygenerowane żadne dodatkowe kolumny. | Nie                                            |
| maxConcurrentConnections | Liczba połączeń do współbieżnego połączenia z magazynem magazynu. Określ tylko wtedy, gdy chcesz ograniczyć współbieżne połączenie z magazynem danych. | Nie                                           |

**Przykład:**

```json
"activities":[
    {
        "name": "CopyFromADLSGen1",
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
                    "type": "AzureDataLakeStoreReadSettings",
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

### <a name="azure-data-lake-store-as-sink"></a>Azure Data Lake Store jako ujścia

[!INCLUDE [data-factory-v2-file-sink-formats](../../includes/data-factory-v2-file-sink-formats.md)]

Następujące właściwości są obsługiwane dla Azure Data Lake Store Gen1 w obszarze `storeSettings` Ustawienia w ujściach kopiowania opartych na formacie:

| Właściwość                 | Opis                                                  | Wymagane |
| ------------------------ | ------------------------------------------------------------ | -------- |
| typ                     | Właściwość Type w obszarze `storeSettings` musi być ustawiona na wartość **AzureDataLakeStoreWriteSettings**. | Tak      |
| copyBehavior             | Definiuje zachowanie kopiowania, gdy źródłem są pliki z magazynu danych opartego na plikach.<br/><br/>Dozwolone wartości to:<br/><b>-PreserveHierarchy (domyślnie)</b>: zachowuje hierarchię plików w folderze docelowym. Ścieżka względna pliku źródłowego do folderu źródłowego jest taka sama jak ścieżka względna pliku docelowego do folderu docelowego.<br/><b>-FlattenHierarchy</b>: wszystkie pliki z folderu źródłowego znajdują się na pierwszym poziomie folderu docelowego. Pliki docelowe mają automatycznie generowane nazwy. <br/><b>-MergeFiles</b>: Scala wszystkie pliki z folderu źródłowego do jednego pliku. Jeśli nazwa pliku jest określona, scalona nazwa pliku jest podaną nazwą. W przeciwnym razie jest to automatycznie wygenerowana nazwa pliku. | Nie       |
| expiryDateTime | Określa czas wygaśnięcia zapisanych plików. Czas jest stosowany do czasu UTC w formacie "2020-03-01T08:00:00Z". Domyślnie jest to wartość NULL, co oznacza, że zapisywane pliki nigdy nie wygasły. | Nie |
| maxConcurrentConnections | Liczba połączeń do równoczesnego połączenia z magazynem danych. Określ tylko wtedy, gdy chcesz ograniczyć współbieżne połączenie z magazynem danych. | Nie       |

**Przykład:**

```json
"activities":[
    {
        "name": "CopyToADLSGen1",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Parquet output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "ParquetSink",
                "storeSettings":{
                    "type": "AzureDataLakeStoreWriteSettings",
                    "copyBehavior": "PreserveHierarchy"
                }
            }
        }
    }
]
```
### <a name="name-range-filter-examples"></a>Przykłady filtru zakresu nazw

W tej sekcji opisano wyniki działania filtrów zakresu nazw.

| Przykładowa struktura źródła | Konfiguracja ADF | Wynik |
|:--- |:--- |:--- |
|root<br/>&nbsp;&nbsp;&nbsp;&nbsp;z<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;file.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;oś<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;file2.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;ax.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;b<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;file3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;bx.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;s<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;file4.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;cx.csv| **W zestawie danych:**<br>-Ścieżka folderu: `root`<br><br>**W źródle działania kopiowania:**<br>-Lista po: `a`<br>-Lista przed: `b`| Następnie zostaną skopiowane następujące pliki:<br><br>root<br/>&nbsp;&nbsp;&nbsp;&nbsp;oś<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;file2.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;ax.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;b<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;file3.csv |

### <a name="folder-and-file-filter-examples"></a>Przykłady filtrów folderów i plików

W tej sekcji opisano skutki zachowania ścieżki folderu i nazwy pliku z filtrami symboli wieloznacznych.

| folderPath | fileName | rozpoznawania | Źródłowa Struktura folderu i wynik filtru (pliki **pogrubione** są pobierane)|
|:--- |:--- |:--- |:--- |
| `Folder*` | (Puste, Użyj domyślnego) | fałsz | Folder<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.jsna**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.jsna<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | (Puste, Użyj domyślnego) | true | Folder<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.jsna**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.jsna**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | fałsz | Folder<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.jsna<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.jsna<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | true | Folder<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.jsna<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.jsna<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="file-list-examples"></a>Przykłady listy plików

W tej sekcji opisano wyniki działania dotyczące korzystania ze ścieżki listy plików w źródle działania kopiowania.

Przy założeniu, że masz następującą źródłową strukturę folderów i chcesz skopiować pliki w pogrubieniu:

| Przykładowa struktura źródła                                      | Zawartość w FileListToCopy.txt                             | Konfiguracja ADF                                            |
| ------------------------------------------------------------ | --------------------------------------------------------- | ------------------------------------------------------------ |
| root<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.jsna<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.jsna<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Metadane<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;FileListToCopy.txt | File1.csv<br>Subfolder1/File3.csv<br>Subfolder1/File5.csv | **W zestawie danych:**<br>-Ścieżka folderu: `root/FolderA`<br><br>**W źródle działania kopiowania:**<br>-Ścieżka listy plików: `root/Metadata/FileListToCopy.txt` <br><br>Ścieżka listy plików wskazuje plik tekstowy w tym samym magazynie danych, który zawiera listę plików do skopiowania, jeden plik na wiersz ze ścieżką względną do ścieżki skonfigurowanej w zestawie danych. |

### <a name="examples-of-behavior-of-the-copy-operation"></a>Przykłady zachowania operacji kopiowania

W tej sekcji opisano zachowanie operacji kopiowania dla różnych kombinacji `recursive` i `copyBehavior` wartości.

| rozpoznawania | copyBehavior | Struktura folderu źródłowego | Wyniki docelowe |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Element docelowy Folder1 jest tworzony z tą samą strukturą co Źródło:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. |
| true |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Docelowy Folder1 jest tworzony z następującą strukturą: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatycznie wygenerowana nazwa dla plik1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatycznie wygenerowana nazwa dla plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatycznie wygenerowana nazwa dla file3<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatycznie wygenerowana nazwa dla File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatycznie wygenerowana nazwa dla File5 |
| true |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Docelowy Folder1 jest tworzony z następującą strukturą: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik1 + plik2 + file3 + File4 + File5 zawartość jest scalana w jeden plik z automatycznie wygenerowaną nazwą pliku. |
| fałsz |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Docelowy Folder1 jest tworzony z następującą strukturą:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/><br/>Subfolder1 z file3, File4 i File5 nie są odbierane. |
| fałsz |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Docelowy Folder1 jest tworzony z następującą strukturą:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatycznie wygenerowana nazwa dla plik1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatycznie wygenerowana nazwa dla plik2<br/><br/>Subfolder1 z file3, File4 i File5 nie są odbierane. |
| fałsz |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Docelowy Folder1 jest tworzony z następującą strukturą:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Wartość plik1 + plik2 jest scalana w jeden plik z automatycznie wygenerowaną nazwą pliku. automatycznie wygenerowana nazwa dla plik1<br/><br/>Subfolder1 z file3, File4 i File5 nie są odbierane. |

## <a name="preserve-acls-to-data-lake-storage-gen2"></a>Zachowaj listy ACL do Data Lake Storage Gen2

>[!TIP]
>Aby w ogólny sposób skopiować dane z Azure Data Lake Storage Gen1 do Gen2, zobacz artykuł [Kopiowanie danych z Azure Data Lake Storage Gen1 do Gen2 z Azure Data Factoryami](load-azure-data-lake-storage-gen2-from-gen1.md) w celu uzyskania wskazówek i najlepszych rozwiązań.

Jeśli chcesz replikować listy kontroli dostępu (ACL) wraz z plikami danych podczas uaktualniania z Data Lake Storage Gen1 do Data Lake Storage Gen2, zobacz temat [zachowywanie list ACL z Data Lake Storage Gen1](copy-activity-preserve-metadata.md#preserve-acls).

## <a name="mapping-data-flow-properties"></a>Mapowanie właściwości przepływu danych

Gdy przekształcasz dane w celu mapowania przepływów danych, możesz odczytywać i zapisywać pliki z Azure Data Lake Storage Gen1 w następujących formatach:
* [Avro](format-avro.md#mapping-data-flow-properties)
* [Tekst rozdzielany](format-delimited-text.md#mapping-data-flow-properties)
* [Excel](format-excel.md#mapping-data-flow-properties)
* [JSON](format-json.md#mapping-data-flow-properties)
* [Parquet](format-parquet.md#mapping-data-flow-properties)

Ustawienia specyficzne dla formatu znajdują się w dokumentacji dla tego formatu. Aby uzyskać więcej informacji, zobacz temat [przekształcanie źródła w mapowaniu przepływu danych](data-flow-source.md) i [transformacji ujścia w mapowaniu przepływu danych](data-flow-sink.md).

### <a name="source-transformation"></a>Transformacja źródła

W transformacji źródłowej można odczytywać z kontenera, folderu lub pojedynczego pliku w Azure Data Lake Storage Gen1. Karta **Opcje źródła** umożliwia zarządzanie sposobem odczytywania plików. 

![Opcje źródła](media/data-flow/sourceOptions1.png "Opcje źródła")

**Ścieżka symboli wieloznacznych:** Użycie wzorca wieloznacznego spowoduje, że ADF będzie przełączać pętlę do każdego pasującego folderu i pliku w ramach pojedynczego przekształcenia źródła. Jest to efektywny sposób przetwarzania wielu plików w ramach pojedynczego przepływu. Dodaj wiele symboli wieloznacznych wzorców ze znakiem +, który pojawia się po umieszczeniu wskaźnika myszy na istniejącym wzorcu symboli wieloznacznych.

Z kontenera źródłowego wybierz serię plików, które pasują do wzorca. W zestawie danych można określić tylko kontener. Ścieżka symbolu wieloznacznego musi zawierać również ścieżkę folderu z folderu głównego.

Przykłady symboli wieloznacznych:

* ```*``` Reprezentuje dowolny zestaw znaków
* ```**``` Reprezentuje zagnieżdżanie katalogów cyklicznych
* ```?``` Zamienia jeden znak
* ```[]``` Dopasowuje jeden z więcej znaków w nawiasach

* ```/data/sales/**/*.csv``` Pobiera wszystkie pliki CSV w obszarze/Data/Sales
* ```/data/sales/20??/**/``` Pobiera wszystkie pliki w 20-wieku
* ```/data/sales/*/*/*.csv``` Pobiera pliki CSV dwa poziomy w obszarze/Data/Sales
* ```/data/sales/2004/*/12/[XY]1?.csv``` Pobiera wszystkie pliki CSV w 2004 w grudniu, zaczynając od X lub Y poprzedzone znakiem dwucyfrowym

**Ścieżka katalogu głównego partycji:** Jeśli masz partycjonowane foldery w źródle plików o ```key=value``` formacie (na przykład Year = 2019), możesz przypisać najwyższy poziom tego drzewa folderów partycji do nazwy kolumny w strumieniu danych przepływu danych.

Najpierw ustaw symbol wieloznaczny, aby uwzględnić wszystkie ścieżki, które są folderami partycjonowanymi oraz pliki liści, które chcesz odczytać.

![Ustawienia pliku źródłowego partycji](media/data-flow/partfile2.png "Ustawienie pliku partycji")

Użyj ustawienia ścieżka katalogu głównego partycji, aby określić, jaki jest najwyższy poziom struktury folderów. Gdy przeglądasz zawartość danych za pośrednictwem wersji zapoznawczej, zobaczysz, że na AUTOMATYCZNYm ekranie zostaną dodane rozpoznane partycje znalezione na każdym z poziomów folderów.

![Ścieżka katalogu głównego partycji](media/data-flow/partfile1.png "Podgląd ścieżki katalogu głównego partycji")

**Lista plików:** To jest zestaw plików. Utwórz plik tekstowy, który zawiera listę plików ścieżek względnych do przetworzenia. Wskaż ten plik tekstowy.

**Kolumna do przechowywania nazwy pliku:** Zapisz nazwę pliku źródłowego w kolumnie w danych. Wprowadź tutaj nazwę nowej kolumny, aby zapisać ciąg nazw plików.

**Po zakończeniu:** Wybierz, aby nic nie robić z plikiem źródłowym po uruchomieniu przepływu danych, usuń plik źródłowy lub Przenieś plik źródłowy. Ścieżki do przenoszenia są względne.

Aby przenieść pliki źródłowe do innej lokalizacji po przetworzeniu, najpierw wybierz pozycję "Przenieś" dla operacji na pliku. Następnie ustaw katalog "z". Jeśli nie używasz symboli wieloznacznych dla ścieżki, ustawienie "od" będzie takie samo jak folder źródłowy.

Jeśli masz ścieżkę źródłową z symbolem wieloznacznym, składnia będzie wyglądać następująco:

```/data/sales/20??/**/*.csv```

Możesz określić wartość "od" jako

```/data/sales```

I "do" jako

```/backup/priorSales```

W takim przypadku wszystkie pliki, które zostały objęte usługą/Data/Sales, są przenoszone do/backup/priorSales.

> [!NOTE]
> Operacje na plikach są uruchamiane tylko wtedy, gdy rozpoczyna się przepływ danych z uruchomienia potoku (debugowania lub przebiegu wykonywania) używającego działania wykonywania przepływu danych w potoku. Operacje na plikach *nie są* uruchamiane w trybie debugowania przepływu danych.

**Filtruj według ostatniej modyfikacji:** Można filtrować, które pliki są przetwarzane, określając zakres dat, po którym były ostatnio modyfikowane. Wszystkie daty i godziny są w formacie UTC. 

### <a name="sink-properties"></a>Właściwości ujścia

W transformacji ujścia można pisać do kontenera lub folderu w Azure Data Lake Storage Gen1. Karta **Ustawienia** umożliwia zarządzanie sposobem pisania plików.

![Opcje ujścia](media/data-flow/file-sink-settings.png "Opcje ujścia")

**Wyczyść folder:** Określa, czy folder docelowy jest usuwany przed zapisaniem danych.

**Opcja nazwy pliku:** Określa sposób nazywania plików docelowych w folderze docelowym. Nazwy plików są następujące:
   * **Domyślnie**: Zezwalaj platformie Spark na nazwy plików na podstawie wartości domyślnych części.
   * **Wzorzec**: wprowadź wzorzec, który wylicza pliki wyjściowe na partycję. Na przykład **pożyczki [n]. csv** utworzy loans1.csv, loans2.csv i tak dalej.
   * **Na partycję**: Wprowadź jedną nazwę pliku na partycję.
   * **Jako dane w kolumnie**: Ustaw plik wyjściowy na wartość kolumny. Ścieżka jest względna wobec kontenera DataSet, a nie folderu docelowego. Jeśli masz ścieżkę folderu w zestawie danych, zostanie ona zastąpiona.
   * **Dane wyjściowe do pojedynczego pliku**: Połącz podzielone na partycje pliki wyjściowe w jeden nazwany plik. Ścieżka jest określana względem folderu DataSet. Należy pamiętać, że operacja scalania może się nie powieść w zależności od rozmiaru węzła. Ta opcja nie jest zalecana w przypadku dużych zestawów danych.

**Wszystkie oferty:** Określa, czy wszystkie wartości mają być ujęte w cudzysłowy

## <a name="lookup-activity-properties"></a>Właściwości działania Lookup

Aby dowiedzieć się więcej o właściwościach, sprawdź [działanie Lookup (wyszukiwanie](control-flow-lookup-activity.md)).

## <a name="getmetadata-activity-properties"></a>Właściwości działania GetMetadata

Aby uzyskać szczegółowe informacje na temat właściwości, sprawdź [działanie GetMetadata](control-flow-get-metadata-activity.md) 

## <a name="delete-activity-properties"></a>Usuń właściwości działania

Aby uzyskać szczegółowe informacje na temat właściwości, zaznacz pozycję [Usuń działanie](delete-activity.md) .

## <a name="legacy-models"></a>Starsze modele

>[!NOTE]
>Następujące modele są nadal obsługiwane w celu zapewnienia zgodności z poprzednimi wersjami. Użytkownik chce użyć nowego modelu wymienionego w powyższych sekcjach, przechodząc do przodu, a interfejs użytkownika tworzenia ADF został przełączony w celu wygenerowania nowego modelu.

### <a name="legacy-dataset-model"></a>Model starszego zestawu danych

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość Type zestawu danych musi być ustawiona na wartość **AzureDataLakeStoreFile**. |Tak |
| folderPath | Ścieżka do folderu w Data Lake Store. Jeśli nie zostanie określony, wskazuje na katalog główny. <br/><br/>Filtr symboli wieloznacznych jest obsługiwany. Dozwolone symbole wieloznaczne to `*` (dopasowuje zero lub więcej znaków) i `?` (dopasowuje zero lub pojedynczy znak). Użyj `^` do ucieczki, jeśli rzeczywista nazwa folderu ma symbol wieloznaczny lub ten znak ucieczki wewnątrz. <br/><br/>Na przykład: RootFolder/subfolder/. Zobacz więcej przykładów w [przykładach folderów i filtrów plików](#folder-and-file-filter-examples). |Nie |
| fileName | Nazwa lub filtr symboli wieloznacznych dla plików pod określonym "folderPath". Jeśli nie określisz wartości dla tej właściwości, zestaw danych wskazuje wszystkie pliki w folderze. <br/><br/>W przypadku filtru, dozwolone symbole wieloznaczne to `*` (dopasowuje zero lub więcej znaków) i `?` (dopasowuje zero lub pojedynczy znak).<br/>-Przykład 1: `"fileName": "*.csv"`<br/>-Przykład 2: `"fileName": "???20180427.txt"`<br/>Użyj `^` , aby wyjść, jeśli rzeczywista nazwa pliku ma symbol wieloznaczny lub ten znak ucieczki wewnątrz.<br/><br/>Jeśli nazwa pliku nie została określona dla wyjściowego zestawu danych, a **preserveHierarchy** nie jest określona w ujścia aktywności, działanie kopiowania automatycznie generuje nazwę pliku z następującym wzorcem: "*Data. [ Identyfikator GUID przebiegu działania]. [GUID if FlattenHierarchy]. [Format, jeśli skonfigurowano]. [kompresja, jeśli jest skonfigurowana]*", na przykład" Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt. gz ". Jeśli skopiujesz ze źródła tabelarycznego przy użyciu nazwy tabeli zamiast zapytania, wzorzec nazwy to "*[nazwa tabeli]. [ Format]. [kompresja, jeśli jest skonfigurowana]*", na przykład" MyTable.csv ". |Nie |
| modifiedDatetimeStart | Filtr plików oparty na ostatniej modyfikacji atrybutu. Pliki są wybierane, jeśli czas ostatniej modyfikacji mieści się w przedziale czasu między `modifiedDatetimeStart` i `modifiedDatetimeEnd` . Czas jest stosowany do strefy czasowej UTC w formacie "2018 r-12-01T05:00:00Z". <br/><br/> Aby można było wykonać filtr plików z ogromną ilością plików, należy włączyć ogólną wydajność przenoszenia danych. <br/><br/> Właściwości mogą mieć wartość NULL, co oznacza, że żaden filtr atrybutu pliku nie jest stosowany do zestawu danych. Gdy `modifiedDatetimeStart` ma wartość DateTime, ale `modifiedDatetimeEnd` jest wartością null, oznacza to, że pliki, których ostatni zmodyfikowany atrybut jest większy lub równy wartości DateTime, są zaznaczone. Gdy `modifiedDatetimeEnd` ma wartość DateTime, ale `modifiedDatetimeStart` jest wartością null, oznacza to, że pliki, których ostatni zmodyfikowany atrybut jest mniejszy niż wartość DateTime, są zaznaczone.| Nie |
| modifiedDatetimeEnd | Filtr plików oparty na ostatniej modyfikacji atrybutu. Pliki są wybierane, jeśli czas ostatniej modyfikacji mieści się w przedziale czasu między `modifiedDatetimeStart` i `modifiedDatetimeEnd` . Czas jest stosowany do strefy czasowej UTC w formacie "2018 r-12-01T05:00:00Z". <br/><br/> Aby można było wykonać filtr plików z ogromną ilością plików, należy włączyć ogólną wydajność przenoszenia danych. <br/><br/> Właściwości mogą mieć wartość NULL, co oznacza, że żaden filtr atrybutu pliku nie jest stosowany do zestawu danych. Gdy `modifiedDatetimeStart` ma wartość DateTime, ale `modifiedDatetimeEnd` jest wartością null, oznacza to, że pliki, których ostatni zmodyfikowany atrybut jest większy lub równy wartości DateTime, są zaznaczone. Gdy `modifiedDatetimeEnd` ma wartość DateTime, ale `modifiedDatetimeStart` jest wartością null, oznacza to, że pliki, których ostatni zmodyfikowany atrybut jest mniejszy niż wartość DateTime, są zaznaczone.| Nie |
| format | Jeśli chcesz skopiować pliki w postaci między magazynami opartymi na plikach (kopia binarna), Pomiń sekcję format w definicjach zestawu danych wejściowych i wyjściowych.<br/><br/>Jeśli chcesz analizować lub generować pliki o określonym formacie, obsługiwane są następujące typy formatu plików: **TextFormat**, **formatu jsonformat**, **AvroFormat**, **OrcFormat**i **ParquetFormat**. Ustaw właściwość **Type** w polu **Format** na jedną z tych wartości. Aby uzyskać więcej informacji, zobacz sekcję [Format tekstu](supported-file-formats-and-compression-codecs-legacy.md#text-format), [Format JSON](supported-file-formats-and-compression-codecs-legacy.md#json-format), [Format Avro](supported-file-formats-and-compression-codecs-legacy.md#avro-format), [Format Orc](supported-file-formats-and-compression-codecs-legacy.md#orc-format)i [Parquet format](supported-file-formats-and-compression-codecs-legacy.md#parquet-format) . |Nie (tylko w przypadku scenariusza kopiowania binarnego) |
| kompresja | Określ typ i poziom kompresji danych. Aby uzyskać więcej informacji, zobacz [obsługiwane formaty plików i kodery-dekoder kompresji](supported-file-formats-and-compression-codecs-legacy.md#compression-support).<br/>Obsługiwane typy to **gzip**, **Wklęśnięcie**, **BZip2**i **ZipDeflate**.<br/>Obsługiwane poziomy są **optymalne** i **najszybciej**. |Nie |

>[!TIP]
>Aby skopiować wszystkie pliki w folderze, określ tylko **folderPath** .<br>Aby skopiować pojedynczy plik o określonej nazwie, należy określić **folderPath** z częścią **folderu i nazwą pliku o** nazwie.<br>Aby skopiować podzestaw plików w folderze, należy określić **folderPath** z częścią folderu i **nazwą pliku** z filtrem symboli wieloznacznych. 

**Przykład:**

```json
{
    "name": "ADLSDataset",
    "properties": {
        "type": "AzureDataLakeStoreFile",
        "linkedServiceName":{
            "referenceName": "<ADLS linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "datalake/myfolder/",
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
| typ | `type`Właściwość źródła działania kopiowania musi być ustawiona na wartość **AzureDataLakeStoreSource**. |Tak |
| rozpoznawania | Wskazuje, czy dane są odczytane cyklicznie z podfolderów, czy tylko z określonego folderu. Gdy `recursive` ma wartość true, a ujścia jest magazynem opartym na plikach, pusty folder lub podfolder nie jest kopiowany ani tworzony w ujścia. Dozwolone wartości to **true** (wartość domyślna) i **false**. | Nie |
| maxConcurrentConnections | Liczba połączeń do równoczesnego połączenia z magazynem danych. Określ tylko wtedy, gdy chcesz ograniczyć współbieżne połączenie z magazynem danych. | Nie |

**Przykład:**

```json
"activities":[
    {
        "name": "CopyFromADLSGen1",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<ADLS Gen1 input dataset name>",
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
                "type": "AzureDataLakeStoreSource",
                "recursive": true
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="legacy-copy-activity-sink-model"></a>Model ujścia starszej aktywności kopiowania

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | `type`Właściwość ujścia działania kopiowania musi być ustawiona na wartość **AzureDataLakeStoreSink**. |Tak |
| copyBehavior | Definiuje zachowanie kopiowania, gdy źródłem są pliki z magazynu danych opartego na plikach.<br/><br/>Dozwolone wartości to:<br/><b>-PreserveHierarchy (domyślnie)</b>: zachowuje hierarchię plików w folderze docelowym. Ścieżka względna pliku źródłowego do folderu źródłowego jest taka sama jak ścieżka względna pliku docelowego do folderu docelowego.<br/><b>-FlattenHierarchy</b>: wszystkie pliki z folderu źródłowego znajdują się na pierwszym poziomie folderu docelowego. Pliki docelowe mają automatycznie generowane nazwy. <br/><b>-MergeFiles</b>: Scala wszystkie pliki z folderu źródłowego do jednego pliku. Jeśli nazwa pliku jest określona, scalona nazwa pliku jest podaną nazwą. W przeciwnym razie nazwa pliku jest generowana automatycznie. | Nie |
| maxConcurrentConnections | Liczba połączeń do równoczesnego połączenia z magazynem danych. Określ tylko wtedy, gdy chcesz ograniczyć współbieżne połączenie z magazynem danych. | Nie |

**Przykład:**

```json
"activities":[
    {
        "name": "CopyToADLSGen1",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<ADLS Gen1 output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureDataLakeStoreSink",
                "copyBehavior": "PreserveHierarchy"
            }
        }
    }
]
```

## <a name="next-steps"></a>Następne kroki

Listę magazynów danych obsługiwanych jako źródła i ujścia przez działanie kopiowania w Azure Data Factory można znaleźć w temacie [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
