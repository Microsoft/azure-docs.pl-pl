---
title: Kopiowanie i Przekształcanie danych w usłudze Azure Blob Storage
description: Dowiedz się, jak kopiować dane do i z magazynu obiektów blob oraz przekształcać dane w usłudze BLOB Storage przy użyciu Data Factory.
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/15/2020
ms.openlocfilehash: ad24f8db2f8f212ebcccf661241016b438ec730c
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83655392"
---
# <a name="copy-and-transform-data-in-azure-blob-storage-by-using-azure-data-factory"></a>Kopiowanie i Przekształcanie danych w usłudze Azure Blob Storage za pomocą Azure Data Factory

> [!div class="op_single_selector" title1="Wybierz używaną wersję usługi Data Factory:"]
> * [Wersja 1](v1/data-factory-azure-blob-connector.md)
> * [Bieżąca wersja](connector-azure-blob-storage.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule opisano sposób używania działania kopiowania w programie Azure Data Factory do kopiowania danych z i do usługi Azure Blob Storage oraz do przekształcania danych w usłudze Azure Blob Storage. Aby dowiedzieć się więcej na temat Azure Data Factory, Przeczytaj [artykuł wprowadzający](introduction.md).

>[!TIP]
>Aby uzyskać informacje na temat scenariusza migracji usługi Data Lake lub magazynu danych, Dowiedz się więcej na temat [używania Azure Data Factory migracji danych z usług Data Lake lub hurtowni danych do platformy Azure](data-migration-guidance-overview.md).

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Ten łącznik obiektów blob platformy Azure jest obsługiwany dla następujących działań:

- [Działanie kopiowania](copy-activity-overview.md) z [obsługiwaną macierzą źródłową/ujścia](copy-activity-overview.md)
- [Mapowanie przepływu danych](concepts-data-flow-overview.md)
- [Działanie Lookup](control-flow-lookup-activity.md)
- [Działanie GetMetadata](control-flow-get-metadata-activity.md)
- [Usuń działanie](delete-activity.md)

W przypadku działania kopiowania ten łącznik usługi BLOB Storage obsługuje:

- Kopiowanie obiektów BLOB do i z kont usługi Azure Storage ogólnego przeznaczenia i gorącą/chłodny magazyn obiektów BLOB. 
- Kopiowanie obiektów BLOB przy użyciu klucza konta, sygnatury dostępu współdzielonego usługi, nazwy głównej usługi lub tożsamości zarządzanych dla uwierzytelniania zasobów platformy Azure.
- Kopiowanie obiektów blob z bloków, dołączania lub stronicowych obiektów blob i kopiowanie danych tylko do blokowych obiektów BLOB.
- Kopiowanie obiektów BLOB jako lub analizowanie lub Generowanie obiektów blob z [obsługiwanymi formatami plików i kodekami kompresji](supported-file-formats-and-compression-codecs.md).
- [Zachowywanie metadanych plików podczas kopiowania](#preserve-metadata-during-copy).

>[!IMPORTANT]
>Jeśli włączysz opcję **Zezwalaj zaufanym usługom firmy Microsoft na dostęp do tego konta magazynu** w ustawieniach zapory usługi Azure Storage i chcesz używać usługi Azure Integration Runtime do nawiązywania połączenia z BLOB Storage, musisz użyć [uwierzytelniania tożsamości zarządzanej](#managed-identity).

## <a name="get-started"></a>Rozpoczęcie pracy

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje dotyczące właściwości, które są używane do definiowania jednostek Data Factory specyficznych dla magazynu obiektów BLOB.

## <a name="linked-service-properties"></a>Właściwości połączonej usługi

Łącznik obiektów blob platformy Azure obsługuje następujące typy uwierzytelniania, zapoznaj się z odpowiednią sekcją, aby uzyskać szczegółowe informacje:

- [Uwierzytelnianie klucza konta](#account-key-authentication)
- [Uwierzytelnianie sygnatury dostępu współdzielonego](#shared-access-signature-authentication)
- [Uwierzytelnianie jednostki usługi](#service-principal-authentication)
- [Zarządzane tożsamości na potrzeby uwierzytelniania zasobów platformy Azure](#managed-identity)

>[!NOTE]
>W przypadku ładowania danych do SQL Data Warehouse przy użyciu bazy kodu bazowego, Jeśli źródłowy lub przejściowy magazyn obiektów BLOB jest skonfigurowany z Virtual Network punktem końcowym, należy użyć uwierzytelniania tożsamości zarządzanej, zgodnie z wymaganiami GeoBase, i korzystać z samodzielnej Integration Runtime z wersją 3,18 lub nowszą. Zapoznaj się z sekcją [uwierzytelnianie tożsamości zarządzanej](#managed-identity) , podając więcej wymagań wstępnych dotyczących konfiguracji.

>[!NOTE]
>Działania HDInsight i Azure Machine Learning obsługują tylko uwierzytelnianie klucza konta usługi Azure Blob Storage.

### <a name="account-key-authentication"></a>Uwierzytelnianie klucza konta

Aby użyć uwierzytelniania klucza konta magazynu, obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość Type musi mieć wartość **AzureBlobStorage** (Sugerowana) lub **AzureStorage** (Zobacz uwagi poniżej). |Yes |
| Parametry połączenia | Określ informacje, które są konieczne do nawiązania połączenia z magazynem dla właściwości connectionString. <br/> Możesz również umieścić klucz konta w Azure Key Vault i ściągnąć `accountKey` konfigurację z parametrów połączenia. Zapoznaj się z poniższymi przykładami i [Zapisz poświadczenia w Azure Key Vault](store-credentials-in-key-vault.md) artykule, aby uzyskać więcej szczegółów. |Yes |
| Właściwością connectvia | [Środowisko Integration Runtime](concepts-integration-runtime.md) służy do nawiązywania połączenia z magazynem danych. Możesz użyć Azure Integration Runtime lub samodzielnego Integration Runtime (Jeśli magazyn danych znajduje się w sieci prywatnej). Jeśli nie zostanie określony, zostanie użyta domyślna Azure Integration Runtime. |Nie |

>[!NOTE]
>Dodatkowy punkt końcowy usługi BLOB Service nie jest obsługiwany w przypadku korzystania z uwierzytelniania klucza konta. Można użyć innych typów uwierzytelniania.

>[!NOTE]
>Jeśli używasz połączonej usługi typu "AzureStorage", jest ona nadal obsługiwana w stanie takim, w jakim będziesz mieć możliwość użycia nowego typu połączonej usługi "AzureBlobStorage" do przodu.

**Przykład:**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Przykład: Zapisz klucz konta w Azure Key Vault**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;",
            "accountKey": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }            
    }
}
```

### <a name="shared-access-signature-authentication"></a>Uwierzytelnianie sygnatury dostępu współdzielonego

Sygnatura dostępu współdzielonego zapewnia delegowany dostęp do zasobów na koncie magazynu. Za pomocą sygnatury dostępu współdzielonego można udzielić klientowi ograniczonych uprawnień do obiektów na koncie magazynu przez określony czas. Nie musisz udostępniać kluczy dostępu do konta. Sygnatura dostępu współdzielonego to identyfikator URI, który obejmuje parametry zapytania wszystkie informacje niezbędne do uzyskania dostępu uwierzytelnionego do zasobu magazynu. Aby uzyskać dostęp do zasobów magazynu za pomocą sygnatury dostępu współdzielonego, klient musi przekazać sygnaturę dostępu współdzielonego tylko do odpowiedniego konstruktora lub metody. Aby uzyskać więcej informacji na temat sygnatur dostępu współdzielonego, zobacz [sygnatury dostępu współdzielonego: Opis modelu sygnatury dostępu współdzielonego](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

> [!NOTE]
>- Data Factory obsługuje teraz zarówno **sygnatury dostępu współdzielonego usługi** , jak i **sygnatury dostępu współdzielonego konta**. Aby uzyskać więcej informacji na temat sygnatur dostępu współdzielonego, zobacz [udzielanie ograniczonego dostępu do zasobów usługi Azure Storage za pomocą sygnatur dostępu współdzielonego (SAS)](../storage/common/storage-sas-overview.md).
>- W konfiguracji późniejszego zestawu danych ścieżka folderu jest ścieżką bezwzględną rozpoczynającą się od poziomu kontenera. Należy skonfigurować jeden wyrównany z ścieżką w identyfikatorze URI SAS.

Aby można było korzystać z uwierzytelniania sygnatury dostępu współdzielonego, obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość Type musi mieć wartość **AzureBlobStorage** (Sugerowana) lub **AzureStorage** (Zobacz uwagi poniżej). |Yes |
| sasUri | Określ identyfikator URI sygnatury dostępu współdzielonego do zasobów magazynu, takich jak BLOB/Container. <br/>Oznacz to pole jako element SecureString, aby bezpiecznie przechowywać go w Data Factory. Token sygnatury dostępu współdzielonego można również umieścić w Azure Key Vault, aby wykorzystać funkcję autorotacji i usunąć część tokenu. Zapoznaj się z poniższymi przykładami i [Zapisz poświadczenia w Azure Key Vault](store-credentials-in-key-vault.md) artykule, aby uzyskać więcej szczegółów. |Yes |
| Właściwością connectvia | [Środowisko Integration Runtime](concepts-integration-runtime.md) służy do nawiązywania połączenia z magazynem danych. Możesz użyć Azure Integration Runtime lub samodzielnego Integration Runtime (Jeśli magazyn danych znajduje się w sieci prywatnej). Jeśli nie zostanie określony, zostanie użyta domyślna Azure Integration Runtime. |Nie |

>[!NOTE]
>Jeśli używasz połączonej usługi typu "AzureStorage", jest ona nadal obsługiwana w stanie takim, w jakim będziesz mieć możliwość użycia nowego typu połączonej usługi "AzureBlobStorage" do przodu.

**Przykład:**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
            "sasUri": {
                "type": "SecureString",
                "value": "<SAS URI of the Azure Storage resource e.g. https://<accountname>.blob.core.windows.net/?sv=<storage version>&amp;st=<start time>&amp;se=<expire time>&amp;sr=<resource>&amp;sp=<permissions>&amp;sip=<ip range>&amp;spr=<protocol>&amp;sig=<signature>>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Przykład: Zapisz klucz konta w Azure Key Vault**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
            "sasUri": {
                "type": "SecureString",
                "value": "<SAS URI of the Azure Storage resource without token e.g. https://<accountname>.blob.core.windows.net/>"
            },
            "sasToken": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

Podczas tworzenia identyfikatora URI sygnatury dostępu współdzielonego należy wziąć pod uwagę następujące kwestie:

- Ustaw odpowiednie uprawnienia do odczytu/zapisu dla obiektów na podstawie sposobu używania połączonej usługi (odczyt, zapis, odczyt/zapis) w fabryce danych.
- Ustaw odpowiednio **czas wygaśnięcia** . Upewnij się, że dostęp do obiektów magazynu nie wygasa w aktywnym okresie potoku.
- Identyfikator URI należy utworzyć w odpowiednim kontenerze/obiekcie blob zależnie od potrzeb. Identyfikator URI sygnatury dostępu współdzielonego do obiektu BLOB umożliwia Data Factory dostępu do określonego obiektu BLOB. Identyfikator URI sygnatury dostępu współdzielonego do kontenera magazynu obiektów BLOB umożliwia Data Factory iteracji za pomocą obiektów BLOB w tym kontenerze. Aby zapewnić dostęp do większej lub mniejszej liczby obiektów później lub zaktualizować identyfikator URI sygnatury dostępu współdzielonego, pamiętaj, aby zaktualizować połączoną usługę nowym identyfikatorem URI.

### <a name="service-principal-authentication"></a>Uwierzytelnianie jednostki usługi

Aby uzyskać ogólne informacje na temat uwierzytelniania jednostki usługi Azure Storage, zapoznaj się z tematem [uwierzytelnianie dostępu do usługi Azure Storage przy użyciu Azure Active Directory](../storage/common/storage-auth-aad.md).

Aby użyć uwierzytelniania jednostki usługi, wykonaj następujące kroki:

1. Zarejestruj jednostkę aplikacji w Azure Active Directory (Azure AD), wykonując czynności opisane w temacie [Rejestrowanie aplikacji w dzierżawie usługi Azure AD](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant). Należy zwrócić uwagę na następujące wartości, których można użyć do zdefiniowania połączonej usługi:

    - Identyfikator aplikacji
    - Klucz aplikacji
    - Identyfikator dzierżawy

2. Przyznaj jednostce usługi odpowiednie uprawnienia w usłudze Azure Blob Storage. Więcej informacji na temat ról można znaleźć w tematach [Zarządzanie prawami dostępu do danych usługi Azure Storage za pomocą RBAC](../storage/common/storage-auth-aad-rbac.md) .

    - **Jako źródło**w kontroli dostępu (IAM) Przydziel co najmniej rolę **czytnika danych obiektów blob magazynu** .
    - **Jako ujścia**w kontroli dostępu (IAM) Przydziel co najmniej rolę **współautor danych obiektu blob magazynu** .

Te właściwości są obsługiwane dla połączonej usługi Azure Blob Storage:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość Type musi być ustawiona na wartość **AzureBlobStorage**. |Yes |
| Końcowego | Określ punkt końcowy usługi Azure Blob Storage z wzorcem `https://<accountName>.blob.core.windows.net/` . |Yes |
| servicePrincipalId | Określ identyfikator klienta aplikacji. | Yes |
| servicePrincipalKey | Określ klucz aplikacji. Oznacz to pole jako element **SecureString** , aby bezpiecznie przechowywać go w Data Factory, lub [odwoływać się do wpisu tajnego przechowywanego w Azure Key Vault](store-credentials-in-key-vault.md). | Yes |
| dzierżaw | Określ informacje o dzierżawie (nazwę domeny lub identyfikator dzierżawy), w których znajduje się Twoja aplikacja. Pobierz go, aktywując wskaźnik myszy w prawym górnym rogu Azure Portal. | Yes |
| Właściwością connectvia | [Środowisko Integration Runtime](concepts-integration-runtime.md) służy do nawiązywania połączenia z magazynem danych. Możesz użyć Azure Integration Runtime lub samodzielnego Integration Runtime (Jeśli magazyn danych znajduje się w sieci prywatnej). Jeśli nie zostanie określony, zostanie użyta domyślna Azure Integration Runtime. |Nie |

>[!NOTE]
>Uwierzytelnianie nazwy głównej usługi jest obsługiwane tylko przez połączoną usługę typu "AzureBlobStorage", ale nie poprzednią połączoną z typem "AzureStorage".

**Przykład:**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {            
            "serviceEndpoint": "https://<accountName>.blob.core.windows.net/",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>" 
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="managed-identities-for-azure-resources-authentication"></a><a name="managed-identity"></a>Zarządzane tożsamości na potrzeby uwierzytelniania zasobów platformy Azure

Fabryka danych może być skojarzona z [zarządzaną tożsamością dla zasobów platformy Azure](data-factory-service-identity.md), która reprezentuje tę konkretną fabrykę danych. Tej tożsamości zarządzanej można bezpośrednio użyć do uwierzytelniania za pomocą usługi BLOB Storage, podobnie jak w przypadku korzystania z własnej nazwy głównej. Umożliwia to wyznaczeniu fabryki dostęp do danych z/do magazynu obiektów blob i ich kopiowanie.

Zapoznaj się z ogólnym uwierzytelnianiem [dostępu do usługi Azure Storage przy użyciu Azure Active Directory](../storage/common/storage-auth-aad.md) na potrzeby uwierzytelniania usługi Azure Storage. Aby używać tożsamości zarządzanych do uwierzytelniania zasobów platformy Azure, wykonaj następujące kroki:

1. [Pobierz informacje o tożsamości zarządzanej fabryki danych](data-factory-service-identity.md#retrieve-managed-identity) przez skopiowanie wartości **identyfikatora obiektu tożsamości zarządzanej** , który został wygenerowany wraz z fabryką.

2. Przyznaj zarządzanej tożsamości odpowiednie uprawnienia w usłudze Azure Blob Storage. Więcej informacji na temat ról można znaleźć w tematach [Zarządzanie prawami dostępu do danych usługi Azure Storage za pomocą RBAC](../storage/common/storage-auth-aad-rbac.md) .

    - **Jako źródło**w kontroli dostępu (IAM) Przydziel co najmniej rolę **czytnika danych obiektów blob magazynu** .
    - **Jako ujścia**w kontroli dostępu (IAM) Przydziel co najmniej rolę **współautor danych obiektu blob magazynu** .

>[!IMPORTANT]
>W przypadku korzystania z bazy danych w celu załadowania z magazynu obiektów BLOB (jako źródła lub jako tymczasowego) do SQL Data Warehouse, w przypadku używania uwierzytelniania tożsamości zarządzanej dla obiektów blob, należy wykonać kroki 1 i 2 w [tych wskazówkach](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage) do 1) zarejestrować serwer SQL Database za pomocą Azure Active Directory (Azure AD) i 2) przypisać rolę współautor danych obiektów blob magazynu do serwera SQL Database; pozostałe są obsługiwane przez Data Factory. Jeśli magazyn obiektów BLOB jest skonfigurowany za pomocą punktu końcowego usługi Azure Virtual Network, aby można było załadować z niego dane, należy użyć uwierzytelniania tożsamości zarządzanej, zgodnie z wymaganiami firmy Base.

Te właściwości są obsługiwane dla połączonej usługi Azure Blob Storage:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość Type musi być ustawiona na wartość **AzureBlobStorage**. |Yes |
| Końcowego | Określ punkt końcowy usługi Azure Blob Storage z wzorcem `https://<accountName>.blob.core.windows.net/` . |Yes |
| Właściwością connectvia | [Środowisko Integration Runtime](concepts-integration-runtime.md) służy do nawiązywania połączenia z magazynem danych. Możesz użyć Azure Integration Runtime lub samodzielnego Integration Runtime (Jeśli magazyn danych znajduje się w sieci prywatnej). Jeśli nie zostanie określony, zostanie użyta domyślna Azure Integration Runtime. |Nie |

> [!NOTE]
> Zarządzane tożsamości dla uwierzytelniania zasobów platformy Azure są obsługiwane tylko przez połączoną usługę typu "AzureBlobStorage", ale nie poprzednią połączoną usługę typu "AzureStorage". 

**Przykład:**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {            
            "serviceEndpoint": "https://<accountName>.blob.core.windows.net/"
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

Następujące właściwości są obsługiwane w przypadku obiektów blob platformy Azure w obszarze `location` Ustawienia w zestawie danych opartym na formacie:

| Właściwość   | Opis                                                  | Wymagane |
| ---------- | ------------------------------------------------------------ | -------- |
| typ       | Właściwość Type lokalizacji w zestawie danych musi być ustawiona na wartość **AzureBlobStorageLocation**. | Yes      |
| kontener  | Kontener obiektów BLOB.                                          | Yes      |
| folderPath | Ścieżka do folderu pod podanym kontenerem. Jeśli chcesz użyć symboli wieloznacznych do filtrowania folderów, pomiń to ustawienie i określ ustawienia źródła działania. | Nie       |
| fileName   | Nazwa pliku pod podanym kontenerem i folderPath. Jeśli chcesz użyć symboli wieloznacznych do filtrowania plików, pomiń to ustawienie i określ ustawienia źródła działania. | Nie       |

**Przykład:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<Azure Blob Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "container": "containername",
                "folderPath": "folder/subfolder"
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

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania działań, zobacz artykuł [potoki](concepts-pipelines-activities.md) . Ta sekcja zawiera listę właściwości obsługiwanych przez źródło i ujścia magazynu obiektów BLOB.

### <a name="blob-storage-as-a-source-type"></a>BLOB Storage jako typ źródła

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Następujące właściwości są obsługiwane w przypadku obiektów blob platformy Azure w obszarze `storeSettings` Ustawienia w źródle kopiowania opartego na formacie:

| Właściwość                 | Opis                                                  | Wymagane                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| typ                     | Właściwość Type w obszarze `storeSettings` musi być ustawiona na wartość **AzureBlobStorageReadSettings**. | Yes                                           |
| ***Zlokalizuj pliki do skopiowania:*** |  |  |
| Opcja 1: ścieżka statyczna<br> | Kopiuj z danego kontenera lub folderu/ścieżki pliku określonego w zestawie danych. Jeśli chcesz skopiować wszystkie obiekty blob z kontenera/folderu, należy również określić `wildcardFileName` jako `*` . |  |
| Opcja 2: prefiks obiektu BLOB<br>-prefix | Prefiks nazwy obiektu BLOB w danym kontenerze skonfigurowanym w zestawie danych do filtrowania źródłowych obiektów BLOB. Obiekty blob, których nazwy zaczynają się od, `container_in_dataset/this_prefix` są zaznaczone. Wykorzystuje filtr po stronie usługi obiektów blob, który zapewnia lepszą wydajność niż filtr symboli wieloznacznych. | Nie                                                          |
| Opcja 3: symbol wieloznaczny<br>- wildcardFolderPath | Ścieżka folderu z symbolami wieloznacznymi pod podanym kontenerem skonfigurowanym w zestawie danych do filtrowania folderów źródłowych. <br>Dozwolone symbole wieloznaczne to: `*` (dopasowuje zero lub więcej znaków) i `?` (dopasowuje zero lub pojedynczy znak); Użyj `^` do ucieczki, jeśli rzeczywista nazwa folderu ma symbol wieloznaczny lub ten znak ucieczki wewnątrz. <br>Zobacz więcej przykładów w [przykładach folderów i filtrów plików](#folder-and-file-filter-examples). | Nie                                            |
| Opcja 3: symbol wieloznaczny<br>- wildcardFileName | Nazwa pliku z symbolami wieloznacznymi pod podanym kontenerem + folderPath/wildcardFolderPath do filtrowania plików źródłowych. <br>Dozwolone symbole wieloznaczne to: `*` (dopasowuje zero lub więcej znaków) i `?` (dopasowuje zero lub pojedynczy znak); Użyj `^` do ucieczki, jeśli rzeczywista nazwa folderu ma symbol wieloznaczny lub ten znak ucieczki wewnątrz.  Zobacz więcej przykładów w [przykładach folderów i filtrów plików](#folder-and-file-filter-examples). | Yes |
| OPCJA 4: Lista plików<br>- fileListPath | Wskazuje, aby skopiować dany zestaw plików. Wskaż plik tekstowy, który zawiera listę plików, które chcesz skopiować, jeden plik na wiersz, który jest ścieżką względną do ścieżki skonfigurowanej w zestawie danych.<br/>W przypadku korzystania z tej opcji nie należy określać nazwy pliku w zestawie danych. Zobacz więcej przykładów na [listach plików](#file-list-examples). |Nie |
| ***Ustawienia dodatkowe:*** |  | |
| rozpoznawania | Wskazuje, czy dane są odczytane cyklicznie z podfolderów, czy tylko z określonego folderu. Należy pamiętać, że gdy wartość cykliczna jest ustawiona na wartość true, a ujścia jest magazynem opartym na plikach, pusty folder lub podfolder nie jest kopiowany ani tworzony w ujścia. <br>Dozwolone wartości to **true** (wartość domyślna) i **false**.<br>Ta właściwość nie ma zastosowania podczas konfigurowania `fileListPath` . |Nie |
| modifiedDatetimeStart    | Filtr plików oparty na atrybucie: ostatnio modyfikowane. <br>Pliki zostaną wybrane, jeśli ich czas ostatniej modyfikacji należy do przedziału czasu między `modifiedDatetimeStart` i `modifiedDatetimeEnd` . Czas jest stosowany do strefy czasowej UTC w formacie "2018 r-12-01T05:00:00Z". <br> Właściwości mogą mieć wartość NULL, co oznacza, że żaden filtr atrybutu pliku nie zostanie zastosowany do zestawu danych.  Gdy `modifiedDatetimeStart` ma wartość DateTime `modifiedDatetimeEnd` , ale jest wartością null, oznacza to, że pliki, których ostatni zmodyfikowany atrybut jest większy lub równy wartości DateTime, zostaną zaznaczone.  Gdy `modifiedDatetimeEnd` ma wartość DateTime `modifiedDatetimeStart` , ale jest wartością null, oznacza to, że pliki, których ostatni zmodyfikowany atrybut jest mniejszy niż wartość DateTime zostanie wybrana.<br/>Ta właściwość nie ma zastosowania podczas konfigurowania `fileListPath` . | Nie                                            |
| modifiedDatetimeEnd      | Tak samo jak powyżej.                                               | Nie                                            |
| maxConcurrentConnections | Liczba połączeń, które mają być jednocześnie połączone z magazynem magazynu. Określ tylko wtedy, gdy chcesz ograniczyć współbieżne połączenie z magazynem danych. | Nie                                            |

> [!NOTE]
> W przypadku formatu tekstu Parquet/Unlimited Źródło działania kopiowania typu **BlobSource** wymienione w następnej sekcji jest nadal obsługiwane w przypadku zgodności z poprzednimi wersjami. Zalecamy użycie nowego modelu do przechodzenia do przodu, a interfejs użytkownika tworzenia ADF został przełączony w celu wygenerowania tych nowych typów.

**Przykład:**

```json
"activities":[
    {
        "name": "CopyFromBlob",
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
                    "type": "AzureBlobStorageReadSettings",
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

### <a name="blob-storage-as-a-sink-type"></a>BLOB Storage jako typ ujścia

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Następujące właściwości są obsługiwane w przypadku obiektów blob platformy Azure w obszarze `storeSettings` Ustawienia w ujściach kopiowania opartych na formacie:

| Właściwość                 | Opis                                                  | Wymagane |
| ------------------------ | ------------------------------------------------------------ | -------- |
| typ                     | Właściwość Type w obszarze `storeSettings` musi być ustawiona na wartość **AzureBlobStorageWriteSettings**. | Yes      |
| copyBehavior             | Definiuje zachowanie kopiowania, gdy źródłem są pliki z magazynu danych opartego na plikach.<br/><br/>Dozwolone wartości to:<br/><b>-PreserveHierarchy (domyślnie)</b>: zachowuje hierarchię plików w folderze docelowym. Ścieżka względna pliku źródłowego do folderu źródłowego jest taka sama jak ścieżka względna pliku docelowego do folderu docelowego.<br/><b>-FlattenHierarchy</b>: wszystkie pliki z folderu źródłowego znajdują się na pierwszym poziomie folderu docelowego. Pliki docelowe mają automatycznie generowane nazwy. <br/><b>-MergeFiles</b>: Scala wszystkie pliki z folderu źródłowego do jednego pliku. Jeśli nazwa pliku lub obiektu BLOB jest określona, scalona nazwa pliku jest podaną nazwą. W przeciwnym razie jest to automatycznie wygenerowana nazwa pliku. | Nie       |
| blockSizeInMB | Określ rozmiar bloku (w MB) służący do zapisywania danych w blokowych obiektach Blob. Dowiedz się więcej na [temat blokowych obiektów BLOB](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-block-blobs). <br/>Dozwolona wartość wynosi **od 4 do 100 MB**. <br/>Domyślnie ADF automatycznie ustala rozmiar bloku na podstawie typu i danych magazynu źródłowego. W przypadku kopiowania niebinarnego do obiektu BLOB domyślny rozmiar bloku to 100 MB, aby dopasować go do maksymalnie 4,95 TB danych. Może to nie być optymalne, gdy dane nie są duże, szczególnie w przypadku korzystania z samodzielnej Integration Runtime z niską siecią, co spowodowało przekroczenie limitu czasu operacji lub problemów z wydajnością. Można jawnie określić rozmiar bloku, a przy zapewnieniu, że blockSizeInMB * 50000 jest wystarczająco duży do przechowywania danych, w przeciwnym razie uruchomienie działania kopiowania zakończy się niepowodzeniem. | Nie |
| maxConcurrentConnections | Liczba połączeń, które mają być jednocześnie połączone z magazynem magazynu. Określ tylko wtedy, gdy chcesz ograniczyć współbieżne połączenie z magazynem danych. | Nie       |

**Przykład:**

```json
"activities":[
    {
        "name": "CopyFromBlob",
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
                    "type": "AzureBlobStorageWriteSettings",
                    "copyBehavior": "PreserveHierarchy"
                }
            }
        }
    }
]
```

### <a name="folder-and-file-filter-examples"></a>Przykłady filtrów folderów i plików

W tej sekcji opisano skutki zachowania ścieżki folderu i nazwy pliku z filtrami symboli wieloznacznych.

| folderPath | fileName | rozpoznawania | Źródłowa Struktura folderu i wynik filtru (pliki **pogrubione** są pobierane)|
|:--- |:--- |:--- |:--- |
| `container/Folder*` | (puste, Użyj domyślnego) | fałsz | kontener<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Plik1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Plik2. JSON**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3. csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |
| `container/Folder*` | (puste, Użyj domyślnego) | true | kontener<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Plik1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Plik2. JSON**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4. JSON**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |
| `container/Folder*` | `*.csv` | fałsz | kontener<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Plik1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik2. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3. csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |
| `container/Folder*` | `*.csv` | true | kontener<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Plik1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik2. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |

### <a name="file-list-examples"></a>Przykłady listy plików

W tej sekcji opisano wyniki działania dotyczące korzystania ze ścieżki listy plików w źródle działania kopiowania.

Przy założeniu, że masz następującą źródłową strukturę folderów i chcesz skopiować pliki w pogrubieniu:

| Przykładowa struktura źródła                                      | Zawartość w FileListToCopy. txt                             | Konfiguracja ADF                                            |
| ------------------------------------------------------------ | --------------------------------------------------------- | ------------------------------------------------------------ |
| kontener<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Plik1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik2. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Metadane<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;FileListToCopy. txt | Plik1. csv<br>Subfolder1/file3. csv<br>Subfolder1/File5. csv | **W zestawie danych:**<br>Wbudowane`container`<br>-Ścieżka folderu:`FolderA`<br><br>**W źródle działania kopiowania:**<br>-Ścieżka listy plików:`container/Metadata/FileListToCopy.txt` <br><br>Ścieżka listy plików wskazuje plik tekstowy w tym samym magazynie danych, który zawiera listę plików do skopiowania, jeden plik na wiersz ze ścieżką względną do ścieżki skonfigurowanej w zestawie danych. |

### <a name="some-recursive-and-copybehavior-examples"></a>Niektóre przykłady cykliczne i copyBehavior

W tej sekcji opisano zachowanie operacji kopiowania dla różnych kombinacji wartości cyklicznych i copyBehavior.

| rozpoznawania | copyBehavior | Struktura folderu źródłowego | Wyniki docelowe |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Folder docelowy Folder1 jest tworzony z tą samą strukturą co Źródło:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 |
| true |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Docelowy Folder1 jest tworzony z następującą strukturą: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatycznie wygenerowana nazwa dla plik1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatycznie wygenerowana nazwa dla plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatycznie wygenerowana nazwa dla file3<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatycznie wygenerowana nazwa dla File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatycznie wygenerowana nazwa dla File5 |
| true |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Docelowy Folder1 jest tworzony z następującą strukturą: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik1 + plik2 + file3 + File4 + File5 zawartość jest scalana w jeden plik z automatycznie wygenerowaną nazwą pliku. |
| fałsz |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Folder docelowy Folder1 jest tworzony z następującą strukturą: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/><br/>Subfolder1 z file3, File4 i File5 nie są pobierane. |
| fałsz |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Folder docelowy Folder1 jest tworzony z następującą strukturą: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatycznie wygenerowana nazwa dla plik1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatycznie wygenerowana nazwa dla plik2<br/><br/>Subfolder1 z file3, File4 i File5 nie są pobierane. |
| fałsz |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Folder docelowy Folder1 jest tworzony z następującą strukturą<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Wartość plik1 + plik2 jest scalana w jeden plik z automatycznie wygenerowaną nazwą pliku. automatycznie wygenerowana nazwa dla plik1<br/><br/>Subfolder1 z file3, File4 i File5 nie są pobierane. |

## <a name="preserve-metadata-during-copy"></a>Zachowaj metadane podczas kopiowania

Podczas kopiowania plików z usługi Amazon S3/Azure Blob/Azure Data Lake Storage Gen2 do obiektu blob Azure Data Lake Storage Gen2/Azure można zachować metadane plików wraz z danymi. Dowiedz się więcej na temat [zachowywania metadanych](copy-activity-preserve-metadata.md#preserve-metadata).

## <a name="mapping-data-flow-properties"></a>Mapowanie właściwości przepływu danych

Podczas przekształcania danych w mapowaniu przepływu danych można odczytywać i zapisywać pliki z usługi Azure Blob Storage w formacie JSON, Avro, rozdzielanym tekstem lub Parquet. Aby uzyskać więcej informacji, zobacz [przekształcenie źródłowe](data-flow-source.md) i [transformacja ujścia](data-flow-sink.md) w funkcji przepływu danych mapowania.

### <a name="source-transformation"></a>Transformacja źródła

W transformacji źródłowej można odczytywać informacje z kontenera, folderu lub pojedynczego pliku na platformie Azure Blob Storage. Karta **Opcje źródła** umożliwia zarządzanie sposobem odczytywania plików. 

![Opcje źródła](media/data-flow/sourceOptions1.png "Opcje źródła")

**Ścieżka symboli wieloznacznych:** Użycie wzorca wieloznacznego spowoduje, że ADF będzie przełączać pętlę do każdego pasującego folderu i pliku w ramach pojedynczego przekształcenia źródła. Jest to efektywny sposób przetwarzania wielu plików w ramach pojedynczego przepływu. Dodaj wiele symboli wieloznacznych wzorców ze znakiem +, który pojawia się po umieszczeniu wskaźnika myszy na istniejącym wzorcu symboli wieloznacznych.

Z kontenera źródłowego wybierz serię plików, które pasują do wzorca. W zestawie danych można określić tylko kontener. Ścieżka symbolu wieloznacznego musi zawierać również ścieżkę folderu z folderu głównego.

Przykłady symboli wieloznacznych:

* ```*```Reprezentuje dowolny zestaw znaków
* ```**```Reprezentuje zagnieżdżanie katalogów cyklicznych
* ```?```Zamienia jeden znak
* ```[]```Dopasowuje jeden z więcej znaków w nawiasach

* ```/data/sales/**/*.csv```Pobiera wszystkie pliki CSV w obszarze/Data/Sales
* ```/data/sales/20??/**/```Pobiera wszystkie pliki w 20-wieku
* ```/data/sales/*/*/*.csv```Pobiera pliki CSV dwa poziomy w obszarze/Data/Sales
* ```/data/sales/2004/*/12/[XY]1?.csv```Pobiera wszystkie pliki CSV w 2004 w grudniu, zaczynając od X lub Y poprzedzone znakiem dwucyfrowym

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

W transformacji ujścia można pisać do kontenera lub folderu w usłudze Azure Blob Storage. Karta **Ustawienia** umożliwia zarządzanie sposobem pisania plików.

![Opcje ujścia](media/data-flow/file-sink-settings.png "Opcje ujścia")

**Wyczyść folder:** Określa, czy folder docelowy jest usuwany przed zapisaniem danych.

**Opcja nazwy pliku:** Określa sposób nazywania plików docelowych w folderze docelowym. Nazwy plików są następujące:
   * **Domyślnie**: Zezwalaj platformie Spark na nazwy plików na podstawie wartości domyślnych części.
   * **Wzorzec**: wprowadź wzorzec, który wylicza pliki wyjściowe na partycję. Na przykład **pożyczki [n]. csv** utworzy loans1. csv, loans2. csv i tak dalej.
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
| typ | Właściwość Type zestawu danych musi być ustawiona na wartość **AzureBlob**. |Yes |
| folderPath | Ścieżka do kontenera i folderu w magazynie obiektów BLOB. <br/><br/>Filtr symboli wieloznacznych jest obsługiwany dla ścieżki wykluczającej nazwę kontenera. Dozwolone symbole wieloznaczne to: `*` (dopasowuje zero lub więcej znaków) i `?` (dopasowuje zero lub pojedynczy znak); Użyj `^` do ucieczki, jeśli rzeczywista nazwa folderu ma symbol wieloznaczny lub ten znak ucieczki wewnątrz. <br/><br/>Przykłady: myblobcontainer/myblobfolder/, Zobacz więcej przykładów w [przykładach folderów i filtrów plików](#folder-and-file-filter-examples). |Tak dla działania kopiowania/wyszukiwania, nie dla działania GetMetadata |
| fileName | **Nazwa lub filtr symboli wieloznacznych** dla obiektów BLOB w ramach określonego elementu "folderPath". Jeśli nie określisz wartości tej właściwości, zestaw danych wskazuje wszystkie obiekty blob w folderze. <br/><br/>W przypadku filtru dozwolone symbole wieloznaczne to: `*` (dopasowuje zero lub więcej znaków) i `?` (dopasowuje zero lub pojedynczy znak).<br/>-Przykład 1:`"fileName": "*.csv"`<br/>-Przykład 2:`"fileName": "???20180427.txt"`<br/>Użyj `^` , aby wyjść, jeśli rzeczywista nazwa pliku ma symbol wieloznaczny lub ten znak ucieczki wewnątrz.<br/><br/>Jeśli nazwa pliku nie została określona dla wyjściowego zestawu danych, a **preserveHierarchy** nie jest określona w obiekcie ujścia aktywności, działanie kopiowania automatycznie generuje nazwę obiektu BLOB z następującym wzorcem: "*Data. [ Identyfikator GUID przebiegu działania]. [GUID if FlattenHierarchy]. [Format, jeśli skonfigurowano]. [kompresja, jeśli jest skonfigurowana]*", np." Data. 0a405f8a-93ff-4c6f-B3BE-f69616f1df7a. txt. gz "; Jeśli skopiujesz ze źródła tabelarycznego przy użyciu nazwy tabeli zamiast zapytania, wzorzec nazwy to "*[nazwa tabeli]. [ Format]. [kompresja, jeśli jest skonfigurowana]*", np." MyTable. csv ". |Nie |
| modifiedDatetimeStart | Filtr plików oparty na atrybucie: ostatnio modyfikowane. Pliki zostaną wybrane, jeśli ich czas ostatniej modyfikacji należy do przedziału czasu między `modifiedDatetimeStart` i `modifiedDatetimeEnd` . Czas jest stosowany do strefy czasowej UTC w formacie "2018 r-12-01T05:00:00Z". <br/><br/> Należy pamiętać, że będzie to miało wpływ na ogólną wydajność przenoszenia danych przez włączenie tego ustawienia, jeśli chcesz, aby filtr plików był objęty dużą ilością plików. <br/><br/> Właściwości mogą mieć wartość NULL, co oznacza, że żaden filtr atrybutu pliku nie zostanie zastosowany do zestawu danych.  Gdy `modifiedDatetimeStart` ma wartość DateTime `modifiedDatetimeEnd` , ale jest wartością null, oznacza to, że pliki, których ostatni zmodyfikowany atrybut jest większy lub równy wartości DateTime, zostaną zaznaczone.  Gdy `modifiedDatetimeEnd` ma wartość DateTime `modifiedDatetimeStart` , ale jest wartością null, oznacza to, że pliki, których ostatni zmodyfikowany atrybut jest mniejszy niż wartość DateTime zostanie wybrana.| Nie |
| modifiedDatetimeEnd | Filtr plików oparty na atrybucie: ostatnio modyfikowane. Pliki zostaną wybrane, jeśli ich czas ostatniej modyfikacji należy do przedziału czasu między `modifiedDatetimeStart` i `modifiedDatetimeEnd` . Czas jest stosowany do strefy czasowej UTC w formacie "2018 r-12-01T05:00:00Z". <br/><br/> Należy pamiętać, że będzie to miało wpływ na ogólną wydajność przenoszenia danych przez włączenie tego ustawienia, jeśli chcesz, aby filtr plików był objęty dużą ilością plików. <br/><br/> Właściwości mogą mieć wartość NULL, co oznacza, że żaden filtr atrybutu pliku nie zostanie zastosowany do zestawu danych.  Gdy `modifiedDatetimeStart` ma wartość DateTime `modifiedDatetimeEnd` , ale jest wartością null, oznacza to, że pliki, których ostatni zmodyfikowany atrybut jest większy lub równy wartości DateTime, zostaną zaznaczone.  Gdy `modifiedDatetimeEnd` ma wartość DateTime `modifiedDatetimeStart` , ale jest wartością null, oznacza to, że pliki, których ostatni zmodyfikowany atrybut jest mniejszy niż wartość DateTime zostanie wybrana.| Nie |
| format | Jeśli chcesz skopiować pliki w postaci między magazynami opartymi na plikach (kopia binarna), Pomiń sekcję format w definicjach zestawu danych wejściowych i wyjściowych.<br/><br/>Jeśli chcesz analizować lub generować pliki o określonym formacie, obsługiwane są następujące typy formatu plików: **TextFormat**, **formatu jsonformat**, **AvroFormat**, **OrcFormat**i **ParquetFormat**. Ustaw właściwość **Type** w polu **Format** na jedną z tych wartości. Aby uzyskać więcej informacji, zobacz sekcję [Format tekstu](supported-file-formats-and-compression-codecs-legacy.md#text-format), [Format JSON](supported-file-formats-and-compression-codecs-legacy.md#json-format), [Format Avro](supported-file-formats-and-compression-codecs-legacy.md#avro-format), [Format Orc](supported-file-formats-and-compression-codecs-legacy.md#orc-format)i [Parquet format](supported-file-formats-and-compression-codecs-legacy.md#parquet-format) . |Nie (tylko w przypadku scenariusza kopiowania binarnego) |
| kompresja | Określ typ i poziom kompresji danych. Aby uzyskać więcej informacji, zobacz [obsługiwane formaty plików i kodery-dekoder kompresji](supported-file-formats-and-compression-codecs-legacy.md#compression-support).<br/>Obsługiwane typy to **gzip**, **Wklęśnięcie**, **BZip2**i **ZipDeflate**.<br/>Obsługiwane poziomy są **optymalne** i **najszybciej**. |Nie |

>[!TIP]
>Aby skopiować wszystkie obiekty blob w folderze, określ tylko **folderPath** .<br>Aby skopiować pojedynczy obiekt BLOB o podanej nazwie, należy określić **folderPath** z częścią **folderu i nazwą pliku.**<br>Aby skopiować podzestaw obiektów BLOB w folderze, należy określić **folderPath** z częścią folderu i **nazwą pliku** z filtrem symboli wieloznacznych. 

**Przykład:**

```json
{
    "name": "AzureBlobDataset",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": {
            "referenceName": "<Azure Blob storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "mycontainer/myfolder",
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
| typ | Właściwość Type źródła działania Copy musi być ustawiona na wartość **BlobSource**. |Yes |
| rozpoznawania | Wskazuje, czy dane są odczytane cyklicznie z podfolderów, czy tylko z określonego folderu. Należy pamiętać, że gdy wartość cykliczna jest ustawiona na wartość true, a ujścia jest magazynem opartym na plikach, pusty folder lub podfolder nie jest kopiowany ani tworzony w ujścia.<br/>Dozwolone wartości to **true** (wartość domyślna) i **false**. | Nie |
| maxConcurrentConnections | Liczba połączeń, które mają być jednocześnie połączone z magazynem magazynu. Określ tylko wtedy, gdy chcesz ograniczyć współbieżne połączenie z magazynem danych. | Nie |

**Przykład:**

```json
"activities":[
    {
        "name": "CopyFromBlob",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure Blob input dataset name>",
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
                "type": "BlobSource",
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
| typ | Właściwość Type ujścia działania Copy musi być ustawiona na wartość **wartość blobsink**. |Yes |
| copyBehavior | Definiuje zachowanie kopiowania, gdy źródłem są pliki z magazynu danych opartego na plikach.<br/><br/>Dozwolone wartości to:<br/><b>-PreserveHierarchy (domyślnie)</b>: zachowuje hierarchię plików w folderze docelowym. Ścieżka względna pliku źródłowego do folderu źródłowego jest taka sama jak ścieżka względna pliku docelowego do folderu docelowego.<br/><b>-FlattenHierarchy</b>: wszystkie pliki z folderu źródłowego znajdują się na pierwszym poziomie folderu docelowego. Pliki docelowe mają automatycznie generowane nazwy. <br/><b>-MergeFiles</b>: Scala wszystkie pliki z folderu źródłowego do jednego pliku. Jeśli nazwa pliku lub obiektu BLOB jest określona, scalona nazwa pliku jest podaną nazwą. W przeciwnym razie jest to automatycznie wygenerowana nazwa pliku. | Nie |
| maxConcurrentConnections | Liczba połączeń, które mają być jednocześnie połączone z magazynem magazynu. Określ tylko wtedy, gdy chcesz ograniczyć współbieżne połączenie z magazynem danych. | Nie |

**Przykład:**

```json
"activities":[
    {
        "name": "CopyToBlob",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Blob output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "BlobSink",
                "copyBehavior": "PreserveHierarchy"
            }
        }
    }
]
```

## <a name="next-steps"></a>Następne kroki

Listę magazynów danych obsługiwanych jako źródła i ujścia przez działanie kopiowania w Data Factory można znaleźć w temacie [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
