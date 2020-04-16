---
title: Kopiowanie i przekształcanie danych w magazynie obiektów Blob platformy Azure
description: Dowiedz się, jak kopiować dane do i z magazynu obiektów Blob i przekształcać dane w magazynie obiektów Blob przy użyciu usługi Data Factory.
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/09/2020
ms.openlocfilehash: b04ff409c95980a1569a2709a475dd8ec74d59b3
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415483"
---
# <a name="copy-and-transform-data-in-azure-blob-storage-by-using-azure-data-factory"></a>Kopiowanie i przekształcanie danych w magazynie obiektów blob platformy Azure przy użyciu usługi Azure Data Factory

> [!div class="op_single_selector" title1="Wybierz wersję używanej usługi Data Factory:"]
> * [Wersja 1](v1/data-factory-azure-blob-connector.md)
> * [Bieżąca wersja](connector-azure-blob-storage.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule opisano, jak używać działania kopiowania w usłudze Azure Data Factory do kopiowania danych z magazynu obiektów Blob i do usługi Azure oraz do przekształcania danych w magazynie obiektów Blob platformy Azure. Aby dowiedzieć się więcej o usłudze Azure Data Factory, przeczytaj [artykuł wprowadzający](introduction.md).

>[!TIP]
>W przypadku scenariusza migracji usługi data lake lub hurtowni danych dowiedz się więcej od [narzędzia Azure Data Factory do migracji danych z usługi Data Lake lub hurtowni danych na platformę Azure.](data-migration-guidance-overview.md)

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Ten łącznik obiektów blob platformy Azure jest obsługiwany dla następujących działań:

- [Kopiowanie aktywności](copy-activity-overview.md) z [obsługiwaną macierzą źródło/ujście](copy-activity-overview.md)
- [Mapowanie przepływu danych](concepts-data-flow-overview.md)
- [Działanie odnośnika](control-flow-lookup-activity.md)
- [Działanie GetMetadata](control-flow-get-metadata-activity.md)
- [Usuwanie działania](delete-activity.md)

W przypadku działania Kopiowania ten łącznik magazynu obiektów Blob obsługuje:

- Kopiowanie obiektów blob do i z kont magazynu platformy Azure ogólnego przeznaczenia i magazynu obiektów blob hot/cool. 
- Kopiowanie obiektów blob przy użyciu klucza konta, podpisu dostępu współdzielonego usługi, jednostki usługi lub tożsamości zarządzanych dla uwierzytelniania zasobów platformy Azure.
- Kopiowanie obiektów blob z bloków, dołączanie lub strony obiektów blob i kopiowanie danych tylko do blokowania obiektów blob.
- Kopiowanie obiektów blob w stanie przebywającym w formie, analizowanie lub generowanie obiektów blob z [obsługiwanymi formatami plików i kodekami koderów koderów-dekompcyjnych.](supported-file-formats-and-compression-codecs.md)
- [Zachowaj metadane pliku podczas kopiowania](#preserve-metadata-during-copy).

>[!IMPORTANT]
>Jeśli włączysz opcję **Zezwalaj zaufanym usługom firmy Microsoft** na dostęp do tego konta magazynu w ustawieniach zapory usługi Azure Storage i chcesz połączyć się z magazynem tożsamości obiektów Blob za pomocą zarządzanego uwierzytelniania tożsamości, należy użyć [uwierzytelniania tożsamości zarządzanej.](#managed-identity)

## <a name="get-started"></a>Rozpoczęcie pracy

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

W poniższych sekcjach znajdują się szczegółowe informacje o właściwościach, które są używane do definiowania jednostek fabryki danych specyficznych dla magazynu obiektów Blob.

## <a name="linked-service-properties"></a>Połączone właściwości usługi

Łącznik obiektów Blob platformy Azure obsługuje następujące typy uwierzytelniania, zapoznaj się z odpowiednią sekcją dotyczącą szczegółów:

- [Uwierzytelnianie klucza konta](#account-key-authentication)
- [Uwierzytelnianie podpisu dostępu współdzielonego](#shared-access-signature-authentication)
- [Uwierzytelnianie jednostki usługi](#service-principal-authentication)
- [Tożsamości zarządzane do uwierzytelniania zasobów platformy Azure](#managed-identity)

>[!NOTE]
>Podczas korzystania z PolyBase do ładowania danych do magazynu danych SQL, jeśli źródło lub magazyn przemieszczania obiektów Blob jest skonfigurowany z punktem końcowym sieci wirtualnej, należy użyć uwierzytelnienia tożsamości zarządzanej zgodnie z wymaganiami PolyBase i używać self-hosted Integration Runtime w wersji 3.18 lub wyższej. Zobacz sekcję [uwierzytelniania tożsamości zarządzanej](#managed-identity) z więcej wymagań wstępnych konfiguracji.

>[!NOTE]
>Działania usługi HDInsights i usługi Azure Machine Learning obsługują tylko uwierzytelnianie klucza konta magazynu obiektów Blob platformy Azure.

### <a name="account-key-authentication"></a>Uwierzytelnianie klucza konta

Aby użyć uwierzytelniania klucza konta magazynu, obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość typu musi być ustawiona na **AzureBlobStorage** (sugerowane) lub **AzureStorage** (zobacz uwagi poniżej). |Tak |
| Parametry połączenia | Określ informacje potrzebne do nawiązania połączenia z magazynem dla właściwości connectionString. <br/> Można również umieścić klucz konta w usłudze Azure Key Vault i wyciągnąć konfigurację `accountKey` z ciągu połączenia. Więcej informacji można znaleźć w poniższych przykładach i [poświadczeniach sklepu w usłudze Azure Key Vault.](store-credentials-in-key-vault.md) |Tak |
| connectVia | [Środowisko uruchomieniowe integracji,](concepts-integration-runtime.md) które ma być używane do łączenia się z magazynem danych. Można użyć środowiska uruchomieniowego integracji platformy Azure lub środowiska wykonawczego integracji hostowanego samodzielnie (jeśli magazyn danych znajduje się w sieci prywatnej). Jeśli nie zostanie określony, używa domyślnego środowiska wykonawczego integracji platformy Azure. |Nie |

>[!NOTE]
>Punkt końcowy usługi dodatkowej usługi obiektów blob nie jest obsługiwany podczas korzystania z uwierzytelniania klucza konta. Można użyć innych typów uwierzytelniania.

>[!NOTE]
>Jeśli używasz usługi połączonej typu "AzureStorage", nadal jest obsługiwana w stanie takim, w jakim jest, podczas gdy zaleca się użycie tego nowego typu usługi połączonej "AzureBlobStorage" w przyszłości.

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

**Przykład: klucz konta magazynu w usłudze Azure Key Vault**

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

### <a name="shared-access-signature-authentication"></a>Uwierzytelnianie podpisu dostępu współdzielonego

Podpis dostępu współdzielonego zapewnia delegowany dostęp do zasobów na koncie magazynu. Za pomocą podpisu dostępu współdzielonego można udzielić klientowi ograniczonych uprawnień do obiektów na koncie magazynu przez określony czas. Nie musisz udostępniać kluczy dostępu do konta. Sygnatura dostępu współdzielonego jest identyfikatorem URI, który obejmuje w swoich parametrach zapytania wszystkie informacje niezbędne do uwierzytelnionego dostępu do zasobu magazynu. Aby uzyskać dostęp do zasobów magazynu z podpisem dostępu współdzielonego, klient musi przekazać tylko w podpisie dostępu współdzielonego do odpowiedniego konstruktora lub metody. Aby uzyskać więcej informacji na temat podpisów dostępu współdzielonego, zobacz [Podpisy dostępu udostępnionego: Zrozumienie modelu podpisu dostępu współdzielonego](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

> [!NOTE]
>- Usługa Data Factory obsługuje teraz zarówno **podpisy dostępu współdzielonego usługi,** jak i **podpisy dostępu współdzielonego na koncie.** Aby uzyskać więcej informacji na temat podpisów dostępu współdzielonego, zobacz [Udzielanie ograniczonego dostępu do zasobów usługi Azure Storage przy użyciu sygnatur dostępu współdzielonego (SAS).](../storage/common/storage-sas-overview.md)
>- W późniejszej konfiguracji zestawu danych ścieżka folderu jest ścieżką bezwzględną, zaczynając od poziomu kontenera. Należy skonfigurować jeden wyrównany ze ścieżką w identyfikatorze URI sygnatury dostępu Współdzielonego.

Aby korzystać z uwierzytelniania podpisu dostępu współdzielonego, obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość typu musi być ustawiona na **AzureBlobStorage** (sugerowane) lub **AzureStorage** (zobacz uwagi poniżej). |Tak |
| sasuri ( sasuri ) | Określ identyfikator URI podpisu dostępu udostępnionego do zasobów magazynu, takich jak obiekt blob/kontener. <br/>Oznacz to pole jako SecureString, aby bezpiecznie przechowywać go w fabryce danych. Token Sygnatury dostępu Współdzielonego można również umieścić w usłudze Azure Key Vault, aby wykorzystać automatyczną rotację i usunąć część tokenu. Więcej informacji można znaleźć w poniższych przykładach i [poświadczeniach sklepu w usłudze Azure Key Vault.](store-credentials-in-key-vault.md) |Tak |
| connectVia | [Środowisko uruchomieniowe integracji,](concepts-integration-runtime.md) które ma być używane do łączenia się z magazynem danych. Można użyć środowiska uruchomieniowego integracji platformy Azure lub środowiska wykonawczego integracji hostowanego samodzielnie (jeśli magazyn danych znajduje się w sieci prywatnej). Jeśli nie zostanie określony, używa domyślnego środowiska wykonawczego integracji platformy Azure. |Nie |

>[!NOTE]
>Jeśli używasz usługi połączonej typu "AzureStorage", nadal jest obsługiwana w stanie takim, w jakim jest, podczas gdy zaleca się użycie tego nowego typu usługi połączonej "AzureBlobStorage" w przyszłości.

**Przykład:**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
            "sasUri": {
                "type": "SecureString",
                "value": "<SAS URI of the Azure Storage resource e.g. https://<container>.blob.core.windows.net/?sv=<storage version>&amp;st=<start time>&amp;se=<expire time>&amp;sr=<resource>&amp;sp=<permissions>&amp;sip=<ip range>&amp;spr=<protocol>&amp;sig=<signature>>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Przykład: klucz konta magazynu w usłudze Azure Key Vault**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
            "sasUri": {
                "type": "SecureString",
                "value": "<SAS URI of the Azure Storage resource without token e.g. https://<container>.blob.core.windows.net/>"
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

Podczas tworzenia identyfikatora URI podpisu dostępu udostępnionego należy wziąć pod uwagę następujące kwestie:

- Ustaw odpowiednie uprawnienia do odczytu/zapisu dla obiektów na podstawie sposobu, w jaki usługa połączona (odczyt, zapis, odczyt/zapis) jest używana w fabryce danych.
- Ustaw czas **wygaśnięcia** odpowiednio. Upewnij się, że dostęp do obiektów magazynu nie wygasa w aktywnym okresie potoku.
- Identyfikator URI powinny być tworzone w prawym kontenerze/obiekcie blob na podstawie potrzeb. Identyfikator URI podpisu dostępu udostępnionego do obiektu blob umożliwia factory danych dostęp do tego określonego obiektu blob. Identyfikator URI podpisu dostępu współdzielonego do kontenera magazynu obiektów Blob umożliwia usługi Data Factory iteracji za pośrednictwem obiektów blob w tym kontenerze. Aby później zapewnić dostęp do większej liczby obiektów lub zaktualizować identyfikator URI podpisu dostępu udostępnionego, należy pamiętać o zaktualizowaniu połączonej usługi o nowy identyfikator URI.

### <a name="service-principal-authentication"></a>Uwierzytelnianie jednostki usługi

W przypadku uwierzytelniania głównego usługi Usługi Azure Storage w ogóle, zapoznaj się [z uwierzytelniaj dostęp do usługi Azure Storage przy użyciu usługi Azure Active Directory](../storage/common/storage-auth-aad.md).

Aby użyć uwierzytelniania głównego usługi, wykonaj następujące kroki:

1. Zarejestruj jednostkę aplikacji w usłudze Azure Active Directory (Azure AD), wykonując [rejestrację aplikacji u dzierżawy usługi Azure AD.](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant) Zanotuj następujące wartości, których używasz do definiowania połączonej usługi:

    - Identyfikator aplikacji
    - Klucz aplikacji
    - Identyfikator dzierżawy

2. Udziel jednostki usługi prawidłowe uprawnienia w magazynie obiektów Blob platformy Azure. Zapoznaj się [z zarządzania prawami dostępu do danych usługi Azure Storage za pomocą funkcji RBAC,](../storage/common/storage-auth-aad-rbac.md) aby uzyskać więcej informacji na temat ról.

    - **Jako źródło**, w kontroli dostępu (IAM), przyznać co najmniej **pamięci masowego dostępu do roli czytnika danych obiektów blob.**
    - **Jako ujście**, w kontroli dostępu (IAM), przyznać co najmniej **roli współautora danych obiektów blob magazynu.**

Te właściwości są obsługiwane dla usługi połączonej z magazynem obiektów Blob platformy Azure:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość typu musi być ustawiona na **AzureBlobStorage**. |Tak |
| Serviceendpoint | Określ punkt końcowy usługi magazynu obiektów `https://<accountName>.blob.core.windows.net/`Blob platformy Azure ze wzorcem . |Tak |
| servicePrincipalId | Określ identyfikator klienta aplikacji. | Tak |
| servicePrincipalKey | Określ klucz aplikacji. Oznacz to pole jako **SecureString,** aby bezpiecznie przechowywać go w fabryce danych lub [odwołaj się do klucza tajnego przechowywanego w usłudze Azure Key Vault.](store-credentials-in-key-vault.md) | Tak |
| Dzierżawy | Określ informacje o dzierżawie (nazwa domeny lub identyfikator dzierżawy), w którym znajduje się aplikacja. Pobierz go, najeżdżając myszą w prawym górnym rogu witryny Azure portal. | Tak |
| connectVia | [Środowisko uruchomieniowe integracji,](concepts-integration-runtime.md) które ma być używane do łączenia się z magazynem danych. Można użyć środowiska uruchomieniowego integracji platformy Azure lub środowiska wykonawczego integracji hostowanego samodzielnie (jeśli magazyn danych znajduje się w sieci prywatnej). Jeśli nie zostanie określony, używa domyślnego środowiska wykonawczego integracji platformy Azure. |Nie |

>[!NOTE]
>Uwierzytelnianie jednostkowe usługi jest obsługiwane tylko przez usługę połączone typu "AzureBlobStorage", ale nie poprzednia usługa połączona typu AzureStorage.

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

### <a name="managed-identities-for-azure-resources-authentication"></a><a name="managed-identity"></a>Tożsamości zarządzane do uwierzytelniania zasobów platformy Azure

Fabryka danych może być skojarzona z [tożsamością zarządzaną dla zasobów platformy Azure,](data-factory-service-identity.md)która reprezentuje tę fabrykę określonych danych. Można bezpośrednio użyć tej tożsamości zarządzanej dla uwierzytelniania magazynu obiektów Blob podobne do korzystania z własnej jednostki usługi. Umożliwia to wyznaczone fabryki, aby uzyskać dostęp i skopiować dane z/do magazynu obiektów Blob.

Zapoznaj się [z uwierzytelniaj dostęp do usługi Azure Storage przy użyciu usługi Azure Active Directory](../storage/common/storage-auth-aad.md) dla uwierzytelniania usługi Azure Storage w ogóle. Aby użyć tożsamości zarządzanych do uwierzytelniania zasobów platformy Azure, wykonaj następujące kroki:

1. [Pobierz informacje o tożsamości zarządzanej fabrycznie](data-factory-service-identity.md#retrieve-managed-identity) przez skopiowanie wartości **identyfikatora obiektu tożsamości zarządzanej** wygenerowanej wraz z fabryką.

2. Udziel właściwego uprawnienia tożsamości zarządzanej w magazynie obiektów Blob platformy Azure. Zapoznaj się [z zarządzania prawami dostępu do danych usługi Azure Storage za pomocą funkcji RBAC,](../storage/common/storage-auth-aad-rbac.md) aby uzyskać więcej informacji na temat ról.

    - **Jako źródło**, w kontroli dostępu (IAM), przyznać co najmniej **pamięci masowego dostępu do roli czytnika danych obiektów blob.**
    - **Jako ujście**, w kontroli dostępu (IAM), przyznać co najmniej **roli współautora danych obiektów blob magazynu.**

>[!IMPORTANT]
>Jeśli używasz PolyBase do ładowania danych z obiektu Blob (jako źródło lub jako przemieszczania) do usługi SQL Data Warehouse, podczas korzystania z uwierzytelniania tożsamości zarządzanej dla obiektu Blob, upewnij się, że należy wykonać kroki 1 i 2 w [tych wskazówek](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage) do 1) zarejestrować serwer bazy danych SQL z usługi Azure Active Directory (Azure AD) i 2) przypisać rolę współautora danych obiektów magazynu do serwera bazy danych SQL; pozostałe są obsługiwane przez fabrykę danych. Jeśli magazyn obiektów Blob jest skonfigurowany z punktem końcowym sieci wirtualnej platformy Azure, aby załadować dane z usługi PolyBase, należy użyć uwierzytelnienia tożsamości zarządzanej zgodnie z wymaganiami PolyBase.

Te właściwości są obsługiwane dla usługi połączonej z magazynem obiektów Blob platformy Azure:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość typu musi być ustawiona na **AzureBlobStorage**. |Tak |
| Serviceendpoint | Określ punkt końcowy usługi magazynu obiektów `https://<accountName>.blob.core.windows.net/`Blob platformy Azure ze wzorcem . |Tak |
| connectVia | [Środowisko uruchomieniowe integracji,](concepts-integration-runtime.md) które ma być używane do łączenia się z magazynem danych. Można użyć środowiska uruchomieniowego integracji platformy Azure lub środowiska wykonawczego integracji hostowanego samodzielnie (jeśli magazyn danych znajduje się w sieci prywatnej). Jeśli nie zostanie określony, używa domyślnego środowiska wykonawczego integracji platformy Azure. |Nie |

> [!NOTE]
> Tożsamości zarządzane dla uwierzytelniania zasobów platformy Azure jest obsługiwany tylko przez "AzureBlobStorage" typu połączone usługi, ale nie poprzednie "AzureStorage" typu połączone usługi. 

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

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania zestawów danych, zobacz artykuł [Zestawy danych.](concepts-datasets-linked-services.md) 

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Następujące właściwości są obsługiwane dla obiektu `location` blob platformy Azure w ustawieniach w zestawie danych opartym na formacie:

| Właściwość   | Opis                                                  | Wymagany |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | Właściwość typu lokalizacji w zestawie danych musi być ustawiona na **AzureBlobStorageLocation**. | Tak      |
| kontener  | Kontener obiektu blob.                                          | Tak      |
| folderPath | Ścieżka do folderu pod danym kontenerem. Jeśli chcesz użyć symbolu wieloznacznego do filtrowania folderu, pomiń to ustawienie i określ w ustawieniach źródła aktywności. | Nie       |
| fileName   | Nazwa pliku pod danym kontenerem + folderPath. Jeśli chcesz użyć symbolu wieloznacznego do filtrowania plików, pomiń to ustawienie i określ w ustawieniach źródła aktywności. | Nie       |

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

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania działań, zobacz [Pipelines](concepts-pipelines-activities.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez źródło magazynu obiektów Blob i ujście.

### <a name="blob-storage-as-a-source-type"></a>Magazyn obiektów blob jako typ źródłowy

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Następujące właściwości są obsługiwane dla obiektu `storeSettings` Blob platformy Azure w ustawieniach w źródle kopiowania opartym na formacie:

| Właściwość                 | Opis                                                  | Wymagany                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | Właściwość typu `storeSettings` w obszarze musi być ustawiona na **AzureBlobStorageReadSettings**. | Tak                                           |
| Cykliczne                | Wskazuje, czy dane są odczytywane cyklicznie z podfolderów, czy tylko z określonego folderu. Należy zauważyć, że gdy cykliczne jest ustawiona na true i ujście jest magazyn oparty na plikach, pusty folder lub podfolder nie jest kopiowany lub tworzony w zlewie. Dozwolone wartości są **prawdziwe** (domyślnie) i **false**. | Nie                                            |
| Prefiks                   | Prefiks nazwy obiektu blob pod danym kontenerem skonfigurowanym w zestawie danych do filtrowania źródłowych obiektów blob. Obiekty BLOB, których nazwa zaczyna się od tego prefiksu, są zaznaczone. <br>Ma zastosowanie `wildcardFolderPath` tylko `wildcardFileName` wtedy, gdy i właściwości nie są określone. | Nie                                                          |
| Ścieżka wieloznacznaFolderPath       | Ścieżka folderu ze znakami wieloznaczymi pod danym kontenerem skonfigurowanym w zestawie danych do filtrowania folderów źródłowych. <br>Dozwolone symbole wieloznaczne to: `*` (dopasowuje zero lub więcej znaków) i `?` (pasuje do zera lub pojedynczego znaku); użyj, `^` aby uciec, jeśli rzeczywista nazwa folderu ma symbol wieloznaczny lub ten znak ucieczki wewnątrz. <br>Zobacz więcej przykładów w [przykładach filtru folderów i plików](#folder-and-file-filter-examples). | Nie                                            |
| symboli wieloznacznychFileName         | Nazwa pliku ze znakami wieloznacznymi pod danym kontenerem + folderPath/wildcardFolderPath do filtrowania plików źródłowych. <br>Dozwolone symbole wieloznaczne to: `*` (dopasowuje zero lub więcej znaków) i `?` (pasuje do zera lub pojedynczego znaku); użyj, `^` aby uciec, jeśli rzeczywista nazwa folderu ma symbol wieloznaczny lub ten znak ucieczki wewnątrz.  Zobacz więcej przykładów w [przykładach filtru folderów i plików](#folder-and-file-filter-examples). | Tak, `fileName` jeśli nie jest określony w zestawie danych |
| modifiedDatetimeStart    | Filtr plików na podstawie atrybutu: Ostatnia modyfikacja. Pliki zostaną wybrane, jeśli ich ostatni zmodyfikowany czas `modifiedDatetimeStart` `modifiedDatetimeEnd`mieści się w przedziale czasu między i . Czas jest stosowany do strefy czasowej UTC w formacie "2018-12-01T05:00:00Z". <br> Właściwości mogą mieć wartość NULL, co oznacza, że do zestawu danych nie zostanie zastosowany żaden filtr atrybutów pliku.  Gdy `modifiedDatetimeStart` ma wartość `modifiedDatetimeEnd` datetime, ale jest null, oznacza to, że pliki, których ostatni zmodyfikowany atrybut jest większa lub równa z wartości datetime zostaną wybrane.  Gdy `modifiedDatetimeEnd` ma wartość `modifiedDatetimeStart` datetime, ale jest null, oznacza to, że pliki, których ostatni zmodyfikowany atrybut jest mniejsza niż wartość datetime zostaną wybrane. | Nie                                            |
| modifiedDatetimeEnd      | Tak samo jak powyżej.                                               | Nie                                            |
| maxConcurrentConnections (Połączenie maksymalne) | Liczba połączeń do łączenia się z magazynem magazynowym jednocześnie. Określ tylko wtedy, gdy chcesz ograniczyć jednoczesne połączenie z magazynem danych. | Nie                                            |

> [!NOTE]
> W przypadku formatu tekstu parquet/rozdzielanych źródło działania typu **BlobSource** wymienione w następnej sekcji jest nadal obsługiwane w stanie zgodnym z formatem wstecznym. Zaleca się użycie tego nowego modelu w przyszłości, a interfejs użytkownika tworzenia podajnikiem ADF przełączył się na generowanie tych nowych typów.

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

### <a name="blob-storage-as-a-sink-type"></a>Magazyn obiektów blob jako typ ujścia

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Następujące właściwości są obsługiwane dla obiektu `storeSettings` blob platformy Azure w ustawieniach w usłudze sink kopii opartej na formacie:

| Właściwość                 | Opis                                                  | Wymagany |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | Właściwość typu `storeSettings` w obszarze musi być ustawiona na **AzureBlobStorageWriteSettings**. | Tak      |
| copyBehavior             | Definiuje zachowanie kopiowania, gdy źródłem są pliki z magazynu danych opartych na plikach.<br/><br/>Dozwolone wartości to:<br/><b>- PreserveHierarchy (domyślnie):</b>Zachowuje hierarchię plików w folderze docelowym. Względna ścieżka pliku źródłowego do folderu źródłowego jest identyczna ze względną ścieżką pliku docelowego do folderu docelowego.<br/><b>- FlattenHierarchy:</b>Wszystkie pliki z folderu źródłowego znajdują się na pierwszym poziomie folderu docelowego. Pliki docelowe mają automatycznie generowane nazwy. <br/><b>- MergeFiles:</b>Scala wszystkie pliki z folderu źródłowego do jednego pliku. Jeśli nazwa pliku lub obiektu blob jest określona, scalona nazwa pliku jest określoną nazwą. W przeciwnym razie jest to nazwa pliku z automatycznym generacją. | Nie       |
| blockSizeInMB | Określ rozmiar bloku w MB używany do zapisywania danych do blokowania obiektów blob. Dowiedz się więcej [o blokowych obiektach blob](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-block-blobs). <br/>Dozwolona wartość wynosi **od 4 do 100 MB**. <br/>Domyślnie podajnik ADF automatycznie określa rozmiar bloku na podstawie typu i danych magazynu źródłowego. W przypadku kopii niebinarnej do obiektu Blob domyślny rozmiar bloku wynosi 100 MB, aby zmieścić się w co najwyżej 4,95 TB danych. Może nie być optymalne, gdy dane nie są duże, zwłaszcza w przypadku korzystania z self-hosted Integration Runtime ze słabą siecią, co powoduje limit czasu operacji lub problem z wydajnością. Można jawnie określić rozmiar bloku, podczas gdy upewnij się, blockSizeInMB * 50000 jest wystarczająco duży, aby przechowywać dane, w przeciwnym razie uruchomienie działania kopiowania zakończy się niepowodzeniem. | Nie |
| maxConcurrentConnections (Połączenie maksymalne) | Liczba połączeń do łączenia się z magazynem magazynowym jednocześnie. Określ tylko wtedy, gdy chcesz ograniczyć jednoczesne połączenie z magazynem danych. | Nie       |

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

W tej sekcji opisano wynikowe zachowanie ścieżki folderu i nazwy pliku za pomocą filtrów wieloznacznych.

| folderPath | fileName | Cykliczne | Struktura folderów źródłowych i wynik filtru (pliki **pogrubione** są pobierane)|
|:--- |:--- |:--- |:--- |
| `container/Folder*` | (pusty, użyj domyślnie) | false | kontener<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA ( folderA )<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Plik1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Plik2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Podfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;InnyFolderb<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik 6.csv |
| `container/Folder*` | (pusty, użyj domyślnie) | true | kontener<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA ( folderA )<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Plik1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Plik2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Podfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Plik3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Plik4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Plik5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;InnyFolderb<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik 6.csv |
| `container/Folder*` | `*.csv` | false | kontener<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA ( folderA )<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Plik1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Podfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;InnyFolderb<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik 6.csv |
| `container/Folder*` | `*.csv` | true | kontener<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA ( folderA )<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Plik1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Podfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Plik3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Plik5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;InnyFolderb<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik 6.csv |

### <a name="some-recursive-and-copybehavior-examples"></a>Niektóre przykłady cykliczne i copyBehavior

W tej sekcji opisano wynikowe zachowanie operacji Kopiowanie dla różnych kombinacji wartości cyklicznych i copyBehavior.

| Cykliczne | copyBehavior | Struktura folderów źródłowych | Wynikujący cel |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Podfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik5 | Folder docelowy Folder1 jest tworzony z taką samą strukturą jak źródło:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Podfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik5 |
| true |spłaszczyćHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Podfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik5 | Folder docelowy1 jest tworzony z następującą strukturą: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;autogenerowana nazwa pliku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;autogenerowana nazwa pliku2<br/>&nbsp;&nbsp;&nbsp;&nbsp;autogenerowana nazwa pliku3<br/>&nbsp;&nbsp;&nbsp;&nbsp;autogenerowana nazwa pliku4<br/>&nbsp;&nbsp;&nbsp;&nbsp;autogenerowana nazwa pliku5 |
| true |scalanie plików | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Podfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik5 | Folder docelowy1 jest tworzony z następującą strukturą: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Zawartość File1 + File2 + File3 + File4 + File5 są scalane w jeden plik o automatycznie zogenerowanej nazwie pliku. |
| false |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Podfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik5 | Folder docelowy Folder1 jest tworzony z następującą strukturą: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/><br/>Podfolder1 z plikami File3, File4 i File5 nie jest pobierany. |
| false |spłaszczyćHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Podfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik5 | Folder docelowy Folder1 jest tworzony z następującą strukturą: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;autogenerowana nazwa pliku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;autogenerowana nazwa pliku2<br/><br/>Podfolder1 z plikami File3, File4 i File5 nie jest pobierany. |
| false |scalanie plików | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Podfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik5 | Folder docelowy Folder1 jest tworzony z następującą strukturą<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Zawartość file1 + File2 jest scalana w jeden plik o automatycznie generowanej nazwie pliku. autogenerowana nazwa pliku1<br/><br/>Podfolder1 z plikami File3, File4 i File5 nie jest pobierany. |

## <a name="preserve-metadata-during-copy"></a>Zachowywanie metadanych podczas kopiowania

Podczas kopiowania plików z amazon S3/Azure Blob/Azure Data Lake Storage Gen2 do usługi Azure Data Lake Storage Gen2/Azure Blob, można zachować metadane pliku wraz z danymi. Dowiedz się więcej z [tematu Zachowywanie metadanych](copy-activity-preserve-metadata.md#preserve-metadata).

## <a name="mapping-data-flow-properties"></a>Mapowanie właściwości przepływu danych

Podczas przekształcania danych w przepływie danych mapowania można odczytywać i zapisywać pliki z usługi Azure Blob Storage w formacie JSON, Avro, Delimited Text lub Parquet. Aby uzyskać więcej informacji, zobacz [transformację źródła](data-flow-source.md) i [transformację ujścia](data-flow-sink.md) w funkcji przepływu danych mapowania.

### <a name="source-transformation"></a>Transformacja źródła

W transformacji źródła można odczytać z kontenera, folderu lub pojedynczego pliku w usłudze Azure Blob Storage. Karta **Opcje źródła** umożliwia zarządzanie odczytem plików. 

![Opcje źródła](media/data-flow/sourceOptions1.png "Opcje źródła")

**Ścieżka wieloznaczna:** Użycie wzorca symboli wieloznacznych nakaże podajnikowi ADF zapętlenie każdego pasującego folderu i pliku w jednej transformacji źródła. Jest to skuteczny sposób przetwarzania wielu plików w ramach jednego przepływu. Dodaj wiele wzorców dopasowywania symboli wieloznacznych ze znakiem +, który pojawia się po najechaniu kursorem na istniejący wzorzec symboli wieloznacznych.

W kontenerze źródłowym wybierz serię plików pasuszych do wzorca. Tylko kontener można określić w zestawie danych. Ścieżka wieloznaczna musi zatem zawierać również ścieżkę folderu z folderu głównego.

Przykłady symboli wieloznacznych:

* ```*```Reprezentuje dowolny zestaw znaków
* ```**```Reprezentuje zagnieżdżanie katalogów cyklicznych
* ```?```Zastępuje jeden znak
* ```[]```Dopasowuje jedną z kolejnych postaci w nawiasach

* ```/data/sales/**/*.csv```Pobiera wszystkie pliki csv w obszarze /data/sales
* ```/data/sales/20??/**/```Pobiera wszystkie pliki w 20 wieku
* ```/data/sales/*/*/*.csv```Pobiera pliki csv dwa poziomy w obszarze /data/sales
* ```/data/sales/2004/*/12/[XY]1?.csv```Pobiera wszystkie pliki csv w 2004 r. w grudniu, począwszy od X lub Y poprzedzone dwucyfrowym numerem

**Ścieżka główna partycji:** Jeśli foldery podzielone na partycje w ```key=value``` źródle plików mają format (na przykład year=2019), możesz przypisać najwyższy poziom tego drzewa folderów partycji do nazwy kolumny w strumieniu danych przepływu danych.

Najpierw ustaw symbol wieloznaczny, aby uwzględnić wszystkie ścieżki, które są folderami podzielonymi na partycje oraz pliki liściowe, które chcesz odczytać.

![Ustawienia pliku źródłowego partycji](media/data-flow/partfile2.png "Ustawienie pliku partycji")

Użyj ustawienia Ścieżka główna partycji, aby zdefiniować najwyższy poziom struktury folderów. Podczas wyświetlania zawartości danych za pośrednictwem podglądu danych zobaczysz, że podajnik ADF doda rozwiązane partycje znajdujące się na każdym z poziomów folderów.

![Ścieżka główna partycji](media/data-flow/partfile1.png "Podgląd ścieżki głównej partycji")

**Lista plików:** Jest to zestaw plików. Utwórz plik tekstowy zawierający listę plików ścieżek względnych do przetworzenia. Wskaż ten plik tekstowy.

**Kolumna do przechowywania nazwy pliku:** Przechowuj nazwę pliku źródłowego w kolumnie w danych. Wprowadź tutaj nową nazwę kolumny, aby zapisać ciąg nazwy pliku.

**Po zakończeniu:** Wybierz, aby nie robić nic z plikiem źródłowym po uruchomieniu przepływu danych, usunąć plik źródłowy lub przenieść plik źródłowy. Ścieżki dla ruchu są względne.

Aby przenieść pliki źródłowe do innej lokalizacji post-processing, najpierw wybierz "Przenieś" dla operacji pliku. Następnie ustaw katalog "od". Jeśli nie używasz żadnych symboli wieloznacznych dla ścieżki, ustawienie "od" będzie tym samym folderem co folder źródłowy.

Jeśli masz ścieżkę źródłową z symbolami wieloznaczymi, składnia będzie wyglądać następująco poniżej:

```/data/sales/20??/**/*.csv```

Można określić "od" jako

```/data/sales```

I "do" jako

```/backup/priorSales```

W takim przypadku wszystkie pliki, które zostały pozyskane w obszarze /data/sales, są przenoszone do /backup/priorSales.

> [!NOTE]
> Operacje na plikach są uruchamiane tylko po uruchomieniu przepływu danych z uruchomienia potoku (uruchomienie debugowania lub wykonania potoku), które używa działania Wykonaj przepływ danych w potoku. Operacje na plikach *nie są* uruchamiane w trybie debugowania przepływu danych.

**Filtruj według ostatnio zmodyfikowanych:** Można filtrować pliki, które przetwarzasz, określając zakres dat, kiedy zostały ostatnio zmodyfikowane. Wszystkie godziny daty są w czasie UTC. 

### <a name="sink-properties"></a>Właściwości zlewu

W transformacji ujścia można zapisać do kontenera lub folderu w usłudze Azure Blob Storage. Karta **Ustawienia** umożliwia zarządzanie sposóbem pisania plików.

![opcje zlewu](media/data-flow/file-sink-settings.png "opcje zlewu")

**Wyczyść folder:** Określa, czy folder docelowy zostanie wyczyszczony przed zapisaniem danych.

**Opcja nazwy pliku:** Określa, jak pliki docelowe mają nazwy w folderze docelowym. Opcje nazwy pliku to:
   * **Domyślnie**: Zezwalaj spark na nazwy plików na podstawie wartości domyślnych PART.
   * **Wzorzec:** Wprowadź wzorzec, który wylicza pliki wyjściowe na partycję. Na przykład **pożyczki[n].csv** utworzy loans1.csv, loans2.csv i tak dalej.
   * **Na partycję**: Wprowadź jedną nazwę pliku na partycję.
   * **Jako dane w kolumnie**: Ustaw plik wyjściowy na wartość kolumny. Ścieżka jest względem kontenera zestawu danych, a nie folderu docelowego. Jeśli masz ścieżkę folderu w zestawie danych, zostanie zastąpiona.
   * **Dane wyjściowe do pojedynczego pliku**: Łączenie podzielonych na partycje plików wyjściowych w jeden nazwany plik. Ścieżka jest względem folderu zestawu danych. Należy pamiętać, że te merge operacji może ewentualnie zakończyć się niepowodzeniem na podstawie rozmiaru węzła. Ta opcja nie jest zalecana w przypadku dużych zestawów danych.

**Zacytuj wszystkie:** Określa, czy wszystkie wartości mają być ujęte w cudzysłowie

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
| type | Właściwość typu zestawu danych musi być ustawiona na **AzureBlob**. |Tak |
| folderPath | Ścieżka do kontenera i folderu w magazynie obiektów blob. <br/><br/>Filtr symboli wieloznacznych jest obsługiwany dla ścieżki z wyłączeniem nazwy kontenera. Dozwolone symbole wieloznaczne to: `*` (dopasowuje zero lub więcej znaków) i `?` (pasuje do zera lub pojedynczego znaku); użyj, `^` aby uciec, jeśli rzeczywista nazwa folderu ma symbol wieloznaczny lub ten znak ucieczki wewnątrz. <br/><br/>Przykłady: myblobcontainer/myblobfolder/, zobacz więcej przykładów w [przykładach folderów i filtrów plików](#folder-and-file-filter-examples). |Tak dla działania Kopiowania/odnośnika, nie dla działania GetMetadata |
| fileName | **Filtr nazw lub symboli wieloznacznych** dla obiektów blob w obszarze określony "folderPath". Jeśli nie określisz wartości dla tej właściwości, zestaw danych wskazuje wszystkie obiekty blob w folderze. <br/><br/>W przypadku filtru dozwolone symbole wieloznaczne `*` to: `?` (dopasowuje zero lub więcej znaków) i (pasuje do zera lub pojedynczego znaku).<br/>- Przykład 1:`"fileName": "*.csv"`<br/>- Przykład 2:`"fileName": "???20180427.txt"`<br/>Użyj, `^` aby uniknąć, jeśli rzeczywista nazwa pliku ma symbol wieloznaczny lub ten znak ucieczki wewnątrz.<br/><br/>Jeśli nazwa pliku nie jest określona dla wyjściowego zestawu danych i **preserveHierarchy** nie jest określony w ujściu działania, działanie kopiowania automatycznie generuje nazwę obiektu blob z następującym wzorcem: "*Dane.[ identyfikator GUID uruchomienia działania]. [GUID jeśli FlattenHierarchy]. [format, jeśli jest skonfigurowany]. [kompresja, jeśli jest skonfigurowana]*", np. Jeśli kopiujesz ze źródła tabelaryczne przy użyciu nazwy tabeli zamiast kwerendy, wzorzec nazwy to "*[nazwa tabeli].[ w formacie]. [kompresja, jeśli jest skonfigurowana]*", np. |Nie |
| modifiedDatetimeStart | Filtr plików na podstawie atrybutu: Ostatnia modyfikacja. Pliki zostaną wybrane, jeśli ich ostatni zmodyfikowany czas `modifiedDatetimeStart` `modifiedDatetimeEnd`mieści się w przedziale czasu między i . Czas jest stosowany do strefy czasowej UTC w formacie "2018-12-01T05:00:00Z". <br/><br/> Należy pamiętać, że ogólna wydajność przenoszenia danych będzie mieć wpływ, włączając to ustawienie, gdy chcesz wykonać filtr plików z ogromnych ilości plików. <br/><br/> Właściwości mogą mieć wartość NULL, co oznacza, że do zestawu danych nie zostanie zastosowany żaden filtr atrybutów pliku.  Gdy `modifiedDatetimeStart` ma wartość `modifiedDatetimeEnd` datetime, ale jest null, oznacza to, że pliki, których ostatni zmodyfikowany atrybut jest większa lub równa z wartości datetime zostaną wybrane.  Gdy `modifiedDatetimeEnd` ma wartość `modifiedDatetimeStart` datetime, ale jest null, oznacza to, że pliki, których ostatni zmodyfikowany atrybut jest mniejsza niż wartość datetime zostaną wybrane.| Nie |
| modifiedDatetimeEnd | Filtr plików na podstawie atrybutu: Ostatnia modyfikacja. Pliki zostaną wybrane, jeśli ich ostatni zmodyfikowany czas `modifiedDatetimeStart` `modifiedDatetimeEnd`mieści się w przedziale czasu między i . Czas jest stosowany do strefy czasowej UTC w formacie "2018-12-01T05:00:00Z". <br/><br/> Należy pamiętać, że ogólna wydajność przenoszenia danych będzie mieć wpływ, włączając to ustawienie, gdy chcesz wykonać filtr plików z ogromnych ilości plików. <br/><br/> Właściwości mogą mieć wartość NULL, co oznacza, że do zestawu danych nie zostanie zastosowany żaden filtr atrybutów pliku.  Gdy `modifiedDatetimeStart` ma wartość `modifiedDatetimeEnd` datetime, ale jest null, oznacza to, że pliki, których ostatni zmodyfikowany atrybut jest większa lub równa z wartości datetime zostaną wybrane.  Gdy `modifiedDatetimeEnd` ma wartość `modifiedDatetimeStart` datetime, ale jest null, oznacza to, że pliki, których ostatni zmodyfikowany atrybut jest mniejsza niż wartość datetime zostaną wybrane.| Nie |
| format | Jeśli chcesz skopiować pliki, tak jak między magazynami opartymi na plikach (kopia binarna), pomiń sekcję formatu w definicjach wejściowych i wyjściowych zestawów danych.<br/><br/>Jeśli chcesz przeanalizować lub wygenerować pliki w określonym formacie, obsługiwane są następujące typy formatów plików: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**i **ParquetFormat**. Ustaw właściwość **typu** w **formacie** na jedną z tych wartości. Aby uzyskać więcej informacji, zobacz [sekcje Format tekstu,](supported-file-formats-and-compression-codecs-legacy.md#text-format) [Format JSON,](supported-file-formats-and-compression-codecs-legacy.md#json-format) [Format Avro,](supported-file-formats-and-compression-codecs-legacy.md#avro-format) [Format Orków](supported-file-formats-and-compression-codecs-legacy.md#orc-format)i [Format parkietu.](supported-file-formats-and-compression-codecs-legacy.md#parquet-format) |Nie (tylko w przypadku scenariusza kopiowania binarnego) |
| kompresja | Określ typ i poziom kompresji danych. Aby uzyskać więcej informacji, zobacz [Obsługiwane formaty plików i kodeki kompresji](supported-file-formats-and-compression-codecs-legacy.md#compression-support).<br/>Obsługiwane typy to **GZip**, **Deflate**, **BZip2**i **ZipDeflate**.<br/>Obsługiwane poziomy są **optymalne** i **najszybsze.** |Nie |

>[!TIP]
>Aby skopiować wszystkie obiekty BLOB w folderze, określ tylko **folderPath.**<br>Aby skopiować pojedynczy obiekt blob o podanej nazwie, określ **folderPath** z częścią **folderu** i nazwą pliku z nazwą pliku.<br>Aby skopiować podzbiór obiektów blob w folderze, określ **folderPath** z częścią folderu i **fileName** z filtrem symboli wieloznacznych. 

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

### <a name="legacy-copy-activity-source-model"></a>Starszy model źródła działania kopiowania

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość typu źródła działania kopiowania musi być ustawiona na **BlobSource**. |Tak |
| Cykliczne | Wskazuje, czy dane są odczytywane cyklicznie z podfolderów, czy tylko z określonego folderu. Należy zauważyć, że gdy cykliczne jest ustawiona na true i ujście jest magazyn oparty na plikach, pusty folder lub podfolder nie jest kopiowany lub tworzony w zlewie.<br/>Dozwolone wartości są **prawdziwe** (domyślnie) i **false**. | Nie |
| maxConcurrentConnections (Połączenie maksymalne) | Liczba połączeń do łączenia się z magazynem magazynowym jednocześnie. Określ tylko wtedy, gdy chcesz ograniczyć jednoczesne połączenie z magazynem danych. | Nie |

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

### <a name="legacy-copy-activity-sink-model"></a>Starszy model ujścia działania kopiowania

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość typu ujścia działania kopiowania musi być ustawiona na **BlobSink**. |Tak |
| copyBehavior | Definiuje zachowanie kopiowania, gdy źródłem są pliki z magazynu danych opartych na plikach.<br/><br/>Dozwolone wartości to:<br/><b>- PreserveHierarchy (domyślnie):</b>Zachowuje hierarchię plików w folderze docelowym. Względna ścieżka pliku źródłowego do folderu źródłowego jest identyczna ze względną ścieżką pliku docelowego do folderu docelowego.<br/><b>- FlattenHierarchy:</b>Wszystkie pliki z folderu źródłowego znajdują się na pierwszym poziomie folderu docelowego. Pliki docelowe mają automatycznie generowane nazwy. <br/><b>- MergeFiles:</b>Scala wszystkie pliki z folderu źródłowego do jednego pliku. Jeśli nazwa pliku lub obiektu blob jest określona, scalona nazwa pliku jest określoną nazwą. W przeciwnym razie jest to nazwa pliku z automatycznym generacją. | Nie |
| maxConcurrentConnections (Połączenie maksymalne) | Liczba połączeń do łączenia się z magazynem magazynowym jednocześnie. Określ tylko wtedy, gdy chcesz ograniczyć jednoczesne połączenie z magazynem danych. | Nie |

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

Aby uzyskać listę magazynów danych obsługiwanych jako źródła i pochłaniacze przez działanie kopiowania w fabryce danych, zobacz [Obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
