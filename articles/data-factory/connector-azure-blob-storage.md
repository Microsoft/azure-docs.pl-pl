---
title: Kopiowanie i Przekształcanie danych w usłudze Azure Blob Storage
description: Dowiedz się, jak kopiować dane do i z magazynu obiektów blob oraz przekształcać dane w usłudze BLOB Storage przy użyciu Data Factory.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/17/2021
ms.openlocfilehash: c1e0dffafafa76e90ec57ce1a00fb8e155ff4edf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104608099"
---
# <a name="copy-and-transform-data-in-azure-blob-storage-by-using-azure-data-factory"></a>Kopiowanie i Przekształcanie danych w usłudze Azure Blob Storage za pomocą Azure Data Factory

> [!div class="op_single_selector" title1="Wybierz używaną wersję usługi Data Factory:"]
> - [Wersja 1](v1/data-factory-azure-blob-connector.md)
> - [Bieżąca wersja](connector-azure-blob-storage.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule opisano sposób używania działania kopiowania w Azure Data Factory do kopiowania danych z i do usługi Azure Blob Storage. Opisano w nim również, jak używać działania przepływu danych do przekształcania danych w usłudze Azure Blob Storage. Aby dowiedzieć się więcej na temat Azure Data Factory, Przeczytaj [artykuł wprowadzający](introduction.md).

>[!TIP]
>Aby dowiedzieć się więcej na temat scenariusza migracji usługi Data Lake lub magazynu danych, zobacz [używanie Azure Data Factory do migrowania danych z usług Data Lake lub Data Warehouse do platformy Azure](data-migration-guidance-overview.md).

## <a name="supported-capabilities"></a>Obsługiwane funkcje

Ten łącznik usługi Azure Blob Storage jest obsługiwany dla następujących działań:

- [Działanie kopiowania](copy-activity-overview.md) z [obsługiwaną macierzą źródłową/ujścia](copy-activity-overview.md)
- [Mapowanie przepływu danych](concepts-data-flow-overview.md)
- [Działanie Lookup](control-flow-lookup-activity.md)
- [Działanie GetMetadata](control-flow-get-metadata-activity.md)
- [Usuń działanie](delete-activity.md)

Dla działania kopiowania ten łącznik magazynu obiektów BLOB obsługuje:

- Kopiowanie obiektów BLOB do i z kont usługi Azure Storage ogólnego przeznaczenia i gorącą/chłodny magazyn obiektów BLOB.
- Kopiowanie obiektów BLOB przy użyciu klucza konta, sygnatury dostępu współdzielonego (SAS) usługi lub tożsamości zarządzanych dla uwierzytelniania zasobów platformy Azure.
- Kopiowanie obiektów blob z bloków, dołączania lub stronicowych obiektów blob i kopiowanie danych tylko do blokowych obiektów BLOB.
- Kopiowanie obiektów BLOB jako programu lub analizowanie lub Generowanie obiektów blob z [obsługiwanymi formatami plików i koderami-dekoder kompresji](supported-file-formats-and-compression-codecs.md).
- [Zachowywanie metadanych plików podczas kopiowania](#preserving-metadata-during-copy).

## <a name="get-started"></a>Rozpoczęcie pracy

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje dotyczące właściwości, które są używane do definiowania jednostek Data Factory specyficznych dla magazynu obiektów BLOB.

## <a name="linked-service-properties"></a>Właściwości połączonej usługi

Ten łącznik magazynu obiektów BLOB obsługuje następujące typy uwierzytelniania. Szczegółowe informacje znajdują się w odpowiednich sekcjach.

- [Uwierzytelnianie klucza konta](#account-key-authentication)
- [Uwierzytelnianie sygnatury dostępu współdzielonego](#shared-access-signature-authentication)
- [Uwierzytelnianie jednostki usługi](#service-principal-authentication)
- [Zarządzane tożsamości na potrzeby uwierzytelniania zasobów platformy Azure](#managed-identity)

>[!NOTE]
>- Jeśli chcesz używać publicznego środowiska Azure Integration Runtime do nawiązywania połączenia z usługą BLOB Storage, korzystając z opcji **Zezwalaj zaufanym usługom firmy Microsoft na dostęp do tego konta magazynu** w zaporze usługi Azure Storage, musisz użyć [uwierzytelniania tożsamości zarządzanej](#managed-identity).
>- Jeśli używasz instrukcji Base lub COPY do ładowania danych do usługi Azure Synapse Analytics, Jeśli źródłowy lub przejściowy magazyn obiektów BLOB jest skonfigurowany za pomocą punktu końcowego usługi Azure Virtual Network, musisz użyć uwierzytelniania tożsamości zarządzanej zgodnie z wymaganiami Synapse. Więcej informacji na temat wymagań wstępnych dotyczących konfiguracji można znaleźć w sekcji [uwierzytelnianie tożsamości zarządzanej](#managed-identity) .

>[!NOTE]
>Działania usługi Azure HDInsight i Azure Machine Learning obsługują tylko uwierzytelnianie wykorzystujące klucze konta usługi Azure Blob Storage.

### <a name="account-key-authentication"></a>Uwierzytelnianie klucza konta

Data Factory obsługuje następujące właściwości uwierzytelniania klucza konta magazynu:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | `type`Właściwość musi być ustawiona na `AzureBlobStorage` (sugerowany) lub `AzureStorage` (Zobacz poniższe uwagi). | Tak |
| Parametry połączenia | Określ informacje, które są konieczne do nawiązania połączenia z magazynem dla `connectionString` właściwości. <br/> Możesz również umieścić klucz konta w Azure Key Vault i ściągnąć `accountKey` konfigurację z parametrów połączenia. Aby uzyskać więcej informacji, zobacz następujące przykłady i [poświadczenia sklepu w artykule Azure Key Vault](store-credentials-in-key-vault.md) . | Tak |
| Właściwością connectvia | [Środowisko Integration Runtime](concepts-integration-runtime.md) służy do nawiązywania połączenia z magazynem danych. Możesz użyć środowiska Azure Integration Runtime lub własnego środowiska Integration Runtime (Jeśli magazyn danych znajduje się w sieci prywatnej). Jeśli ta właściwość nie jest określona, usługa używa domyślnego środowiska Azure Integration Runtime. | Nie |

>[!NOTE]
>Pomocniczy punkt końcowy Blob service nie jest obsługiwany w przypadku korzystania z uwierzytelniania klucza konta. Można użyć innych typów uwierzytelniania.

>[!NOTE]
>Jeśli używasz `AzureStorage` połączonej usługi typu, jest ona nadal obsługiwana jako. Ale sugerujemy użycie nowego `AzureBlobStorage` typu połączonej usługi do przodu.

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

**Przykład: Przechowuj klucz konta w Azure Key Vault**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;",
            "accountKey": {
                "type": "AzureKeyVaultSecret",
                "store": {
                    "referenceName": "<Azure Key Vault linked service name>",
                    "type": "LinkedServiceReference"
                },
                "secretName": "<secretName>"
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

Sygnatura dostępu współdzielonego zapewnia delegowany dostęp do zasobów na koncie magazynu. Za pomocą sygnatury dostępu współdzielonego można udzielić klientowi ograniczonych uprawnień do obiektów na koncie magazynu przez określony czas. 

Nie musisz udostępniać kluczy dostępu do konta. Sygnatura dostępu współdzielonego to identyfikator URI, który obejmuje parametry zapytania wszystkie informacje niezbędne do uzyskania dostępu uwierzytelnionego do zasobu magazynu. Aby uzyskać dostęp do zasobów magazynu za pomocą sygnatury dostępu współdzielonego, klient musi przekazać sygnaturę dostępu współdzielonego tylko do odpowiedniego konstruktora lub metody.

Aby uzyskać więcej informacji na temat sygnatur dostępu współdzielonego, zobacz [sygnatury dostępu współdzielonego: Opis modelu sygnatury dostępu współdzielonego](../storage/common/storage-sas-overview.md).

> [!NOTE]
>- Data Factory obsługuje teraz zarówno *sygnatury dostępu współdzielonego usługi* , jak i *sygnatury dostępu współdzielonego konta*. Aby uzyskać więcej informacji na temat sygnatur dostępu współdzielonego, zobacz [udzielanie ograniczonego dostępu do zasobów usługi Azure Storage przy użyciu sygnatur dostępu współdzielonego](../storage/common/storage-sas-overview.md).
>- W późniejszych konfiguracjach zestawu danych ścieżka folderu jest ścieżką bezwzględną rozpoczynającą się od poziomu kontenera. Należy skonfigurować jeden wyrównany z ścieżką w identyfikatorze URI SAS.

Data Factory obsługuje następujące właściwości używane do uwierzytelniania sygnatury dostępu współdzielonego:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | `type`Właściwość musi być ustawiona na `AzureBlobStorage` (sugerowany) lub `AzureStorage` (zobacz poniższą uwagę). | Tak |
| sasUri | Określ identyfikator URI sygnatury dostępu współdzielonego do zasobów magazynu, takich jak obiekt BLOB lub kontener. <br/>Oznacz to pole jako, `SecureString` Aby bezpiecznie przechowywać je w Data Factory. Można również umieścić token sygnatury dostępu współdzielonego w Azure Key Vault, aby użyć instrukcji AutoRotation i usunąć część tokenu. Aby uzyskać więcej informacji, zobacz następujące przykłady i [przechowywanie poświadczeń w Azure Key Vault](store-credentials-in-key-vault.md). | Tak |
| Właściwością connectvia | [Środowisko Integration Runtime](concepts-integration-runtime.md) służy do nawiązywania połączenia z magazynem danych. Możesz użyć środowiska Azure Integration Runtime lub własnego środowiska Integration Runtime (Jeśli magazyn danych znajduje się w sieci prywatnej). Jeśli ta właściwość nie jest określona, usługa używa domyślnego środowiska Azure Integration Runtime. | Nie |

>[!NOTE]
>Jeśli używasz `AzureStorage` połączonej usługi typu, jest ona nadal obsługiwana jako. Ale sugerujemy użycie nowego `AzureBlobStorage` typu połączonej usługi do przodu.

**Przykład:**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
            "sasUri": {
                "type": "SecureString",
                "value": "<SAS URI of the Azure Storage resource e.g. https://<accountname>.blob.core.windows.net/?sv=<storage version>&st=<start time>&se=<expire time>&sr=<resource>&sp=<permissions>&sip=<ip range>&spr=<protocol>&sig=<signature>>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Przykład: Przechowuj klucz konta w Azure Key Vault**

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
                "type": "AzureKeyVaultSecret",
                "store": {
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference"
                },
                "secretName": "<secretName with value of SAS token e.g. ?sv=<storage version>&st=<start time>&se=<expire time>&sr=<resource>&sp=<permissions>&sip=<ip range>&spr=<protocol>&sig=<signature>>"
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
- Identyfikator URI należy utworzyć w odpowiednim kontenerze lub na obiekcie blob zależnie od potrzeb. Identyfikator URI sygnatury dostępu współdzielonego do obiektu BLOB umożliwia Data Factory dostępu do określonego obiektu BLOB. Identyfikator URI sygnatury dostępu współdzielonego do kontenera magazynu obiektów BLOB umożliwia Data Factory iteracji za pomocą obiektów BLOB w tym kontenerze. Aby zapewnić dostęp do większej lub mniejszej liczby obiektów później lub zaktualizować identyfikator URI sygnatury dostępu współdzielonego, pamiętaj, aby zaktualizować połączoną usługę nowym identyfikatorem URI.

### <a name="service-principal-authentication"></a>Uwierzytelnianie jednostki usługi

Aby uzyskać ogólne informacje o uwierzytelnianiu głównym usługi Azure Storage, zobacz temat [uwierzytelnianie dostępu do usługi Azure Storage przy użyciu Azure Active Directory](../storage/common/storage-auth-aad.md).

Aby użyć uwierzytelniania jednostki usługi, wykonaj następujące kroki:

1. Zarejestruj jednostkę aplikacji w Azure Active Directory (Azure AD), wykonując czynności opisane w temacie [Rejestrowanie aplikacji w dzierżawie usługi Azure AD](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant). Zanotuj te wartości, które są używane do definiowania połączonej usługi:

    - Identyfikator aplikacji
    - Klucz aplikacji
    - Identyfikator dzierżawy

2. Przyznaj jednostce usługi odpowiednie uprawnienia w usłudze Azure Blob Storage. Aby uzyskać więcej informacji na temat ról, zobacz [używanie Azure Portal do przypisywania roli platformy Azure na potrzeby dostępu do danych obiektów blob i kolejek](../storage/common/storage-auth-aad-rbac-portal.md).

    - **Jako źródło** w **kontroli dostępu (IAM)** Przydziel co najmniej rolę **czytnika danych obiektów blob magazynu** .
    - **Jako ujścia** w **kontroli dostępu (IAM)** Przydziel co najmniej rolę **współautor danych obiektu blob magazynu** .

Te właściwości są obsługiwane dla połączonej usługi Azure Blob Storage:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość **Type** musi być ustawiona na wartość **AzureBlobStorage**. | Tak |
| Końcowego | Określ punkt końcowy usługi Azure Blob Storage z wzorcem `https://<accountName>.blob.core.windows.net/` . | Tak |
| accountKind | Określ rodzaj konta magazynu. Dozwolone wartości to: **Storage** (ogólnego przeznaczenia w wersji 1), **StorageV2** (ogólnego przeznaczenia w wersji 2), **BlobStorage** lub **BlockBlobStorage**. <br/><br/>W przypadku korzystania z połączonej usługi Azure BLOB w przepływie danych tożsamość zarządzana lub uwierzytelnianie nazwy głównej usługi nie są obsługiwane, jeśli typ konta jest pusty lub "magazyn". Określ odpowiedni rodzaj konta, wybierz inne uwierzytelnianie lub Uaktualnij konto magazynu do ogólnego przeznaczenia w wersji 2. | Nie |
| servicePrincipalId | Określ identyfikator klienta aplikacji. | Tak |
| servicePrincipalKey | Określ klucz aplikacji. Oznacz to pole jako element **SecureString** , aby bezpiecznie przechowywać go w Data Factory, lub [odwoływać się do wpisu tajnego przechowywanego w Azure Key Vault](store-credentials-in-key-vault.md). | Tak |
| dzierżaw | Określ informacje o dzierżawie (nazwę domeny lub identyfikator dzierżawy), w których znajduje się Twoja aplikacja. Pobierz ją przez umieszczenie kursora w prawym górnym rogu Azure Portal. | Tak |
| azureCloudType | W polu Uwierzytelnianie nazwy głównej usługi Określ typ środowiska chmury platformy Azure, do którego zarejestrowano aplikację Azure Active Directory. <br/> Dozwolone wartości to **AzurePublic**, **AzureChina**, **AzureUsGovernment** i **AzureGermany**. Domyślnie używane jest środowisko chmury fabryki danych. | Nie |
| Właściwością connectvia | [Środowisko Integration Runtime](concepts-integration-runtime.md) służy do nawiązywania połączenia z magazynem danych. Możesz użyć środowiska Azure Integration Runtime lub własnego środowiska Integration Runtime (Jeśli magazyn danych znajduje się w sieci prywatnej). Jeśli ta właściwość nie jest określona, usługa używa domyślnego środowiska Azure Integration Runtime. | Nie |

>[!NOTE]
>
>- Jeśli konto obiektu BLOB umożliwia [usuwanie nietrwałe](../storage/blobs/soft-delete-blob-overview.md), w przepływie danych uwierzytelnianie jednostki usługi nie jest obsługiwane.
>- Jeśli dostęp do usługi BLOB Storage odbywa się za pośrednictwem prywatnego punktu końcowego przy użyciu przepływu danych, należy pamiętać, że użycie funkcji przepływ danych przez uwierzytelnianie jednostki usługi spowoduje nawiązanie połączenia z punktem końcowym ADLS Gen2 zamiast punktu końcowego obiektu BLOB Upewnij się, że utworzono odpowiedni prywatny punkt końcowy w podajniku APD, aby umożliwić dostęp.

>[!NOTE]
>Uwierzytelnianie nazwy głównej usługi jest obsługiwane tylko przez połączoną usługę typu "AzureBlobStorage", a nie poprzednią połączoną usługę typu "AzureStorage".

**Przykład:**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {            
            "serviceEndpoint": "https://<accountName>.blob.core.windows.net/",
            "accountKind": "StorageV2",
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

### <a name="managed-identities-for-azure-resource-authentication"></a><a name="managed-identity"></a> Zarządzane tożsamości na potrzeby uwierzytelniania zasobów platformy Azure

Fabryka danych może być skojarzona z [zarządzaną tożsamością dla zasobów platformy Azure](data-factory-service-identity.md), która reprezentuje tę konkretną fabrykę danych. Można bezpośrednio użyć tej tożsamości zarządzanej do uwierzytelniania usługi BLOB Storage, która jest podobna do użycia własnej nazwy głównej usługi. Umożliwia to wyznaczeniu fabryki dostęp do danych z lub do usługi BLOB Storage.

Aby uzyskać ogólne informacje o uwierzytelnianiu usługi Azure Storage, zobacz temat [uwierzytelnianie dostępu do usługi Azure Storage przy użyciu Azure Active Directory](../storage/common/storage-auth-aad.md). Aby używać tożsamości zarządzanych do uwierzytelniania zasobów platformy Azure, wykonaj następujące kroki:

1. [Pobierz Data Factory Informacje o tożsamości zarządzane](data-factory-service-identity.md#retrieve-managed-identity) przez skopiowanie wartości identyfikatora obiektu tożsamości zarządzanej, który został wygenerowany wraz z fabryką.

2. Udziel uprawnienia zarządzanej tożsamości w usłudze Azure Blob Storage. Aby uzyskać więcej informacji na temat ról, zobacz [używanie Azure Portal do przypisywania roli platformy Azure na potrzeby dostępu do danych obiektów blob i kolejek](../storage/common/storage-auth-aad-rbac-portal.md).

    - **Jako źródło** w **kontroli dostępu (IAM)** Przydziel co najmniej rolę **czytnika danych obiektów blob magazynu** .
    - **Jako ujścia** w **kontroli dostępu (IAM)** Przydziel co najmniej rolę **współautor danych obiektu blob magazynu** .

>[!IMPORTANT]
>Jeśli używasz instrukcji Base lub COPY do ładowania danych z magazynu obiektów BLOB (jako źródło lub jako tymczasowe) do usługi Azure Synapse Analytics, jeśli używasz uwierzytelniania tożsamości zarządzanej dla magazynu obiektów blob, upewnij się, że w [tych wskazówkach](../azure-sql/database/vnet-service-endpoint-rule-overview.md#impact-of-using-virtual-network-service-endpoints-with-azure-storage)opisano kroki od 1 do 3. Te kroki spowodują zarejestrowanie serwera w usłudze Azure AD i przypisanie roli współautor danych obiektów blob magazynu do serwera. Data Factory obsługuje resztę. W przypadku skonfigurowania usługi BLOB Storage za pomocą punktu końcowego usługi Azure Virtual Network należy również **zezwolić zaufanym usługom firmy Microsoft na dostęp do tego konta magazynu** w obszarze zapory konta usługi Azure Storage i menu ustawienia **sieci wirtualnych** zgodnie z wymaganiami Synapse.

Te właściwości są obsługiwane dla połączonej usługi Azure Blob Storage:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość **Type** musi być ustawiona na wartość **AzureBlobStorage**. | Tak |
| Końcowego | Określ punkt końcowy usługi Azure Blob Storage z wzorcem `https://<accountName>.blob.core.windows.net/` . | Tak |
| accountKind | Określ rodzaj konta magazynu. Dozwolone wartości to: **Storage** (ogólnego przeznaczenia w wersji 1), **StorageV2** (ogólnego przeznaczenia w wersji 2), **BlobStorage** lub **BlockBlobStorage**. <br/><br/>W przypadku korzystania z połączonej usługi Azure BLOB w przepływie danych tożsamość zarządzana lub uwierzytelnianie nazwy głównej usługi nie są obsługiwane, jeśli typ konta jest pusty lub "magazyn". Określ odpowiedni rodzaj konta, wybierz inne uwierzytelnianie lub Uaktualnij konto magazynu do ogólnego przeznaczenia w wersji 2. | Nie |
| Właściwością connectvia | [Środowisko Integration Runtime](concepts-integration-runtime.md) służy do nawiązywania połączenia z magazynem danych. Możesz użyć środowiska Azure Integration Runtime lub własnego środowiska Integration Runtime (Jeśli magazyn danych znajduje się w sieci prywatnej). Jeśli ta właściwość nie jest określona, usługa używa domyślnego środowiska Azure Integration Runtime. | Nie |

> [!NOTE]
>
> - Jeśli konto obiektu BLOB umożliwia [usuwanie nietrwałe](../storage/blobs/soft-delete-blob-overview.md), uwierzytelnianie tożsamości zarządzanej nie jest obsługiwane w przepływie danych.
> - Jeśli dostęp do usługi BLOB Storage odbywa się za pośrednictwem prywatnego punktu końcowego przy użyciu przepływu danych, należy pamiętać, że użycie funkcji przepływ danych przez uwierzytelnianie tożsamości zarządzanej łączy się z punktem końcowym ADLS Gen2 zamiast punktu końcowego obiektu BLOB. Upewnij się, że utworzono odpowiedni prywatny punkt końcowy w podajniku APD, aby umożliwić dostęp.

> [!NOTE]
> Zarządzane tożsamości dla uwierzytelniania zasobów platformy Azure są obsługiwane tylko przez połączoną usługę typu "AzureBlobStorage", a nie poprzednią połączoną usługę typu "AzureStorage".

**Przykład:**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {            
            "serviceEndpoint": "https://<accountName>.blob.core.windows.net/",
            "accountKind": "StorageV2" 
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

Następujące właściwości są obsługiwane w przypadku usługi Azure Blob Storage w obszarze `location` Ustawienia w zestawie danych opartym na formacie:

| Właściwość   | Opis                                                  | Wymagane |
| ---------- | ------------------------------------------------------------ | -------- |
| typ       | Właściwość **Type** lokalizacji w zestawie danych musi być ustawiona na wartość **AzureBlobStorageLocation**. | Tak      |
| kontener  | Kontener obiektów BLOB.                                          | Tak      |
| folderPath | Ścieżka do folderu pod podanym kontenerem. Jeśli chcesz użyć symbolu wieloznacznego do filtrowania folderu, pomiń to ustawienie i określ je w obszarze Ustawienia źródła działania. | Nie       |
| fileName   | Nazwa pliku w podanym kontenerze i ścieżce folderu. Jeśli chcesz użyć symboli wieloznacznych do filtrowania plików, pomiń to ustawienie i określ je w obszarze Ustawienia źródła działania. | Nie       |

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

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania działań, zobacz artykuł [potoki](concepts-pipelines-activities.md) . Ta sekcja zawiera listę właściwości, które są obsługiwane przez źródło i ujścia magazynu obiektów BLOB.

### <a name="blob-storage-as-a-source-type"></a>BLOB Storage jako typ źródła

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Następujące właściwości są obsługiwane w przypadku usługi Azure Blob Storage w obszarze `storeSettings` Ustawienia w źródle kopiowania opartym na formacie:

| Właściwość                 | Opis                                                  | Wymagane                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| typ                     | Właściwość **Type** w obszarze `storeSettings` musi być ustawiona na wartość **AzureBlobStorageReadSettings**. | Tak                                           |
| ***Zlokalizuj pliki do skopiowania:*** |  |  |
| Opcja 1: ścieżka statyczna<br> | Kopiuj z danego kontenera lub folderu/ścieżki pliku określonego w zestawie danych. Jeśli chcesz skopiować wszystkie obiekty blob z kontenera lub folderu, należy również określić `wildcardFileName` jako `*` . |  |
| Opcja 2: prefiks obiektu BLOB<br>-prefix | Prefiks nazwy obiektu BLOB w danym kontenerze skonfigurowanym w zestawie danych do filtrowania źródłowych obiektów BLOB. Obiekty blob, których nazwy rozpoczynają się od, `container_in_dataset/this_prefix` są zaznaczone. Korzysta ona z filtru po stronie usług dla usługi BLOB Storage, który zapewnia lepszą wydajność niż filtr symboli wieloznacznych.<br><br>W przypadku użycia prefiksu i wybrania kopiowania do ujścia opartego na plikach z zachowaniem hierarchii należy pamiętać, że Ścieżka podrzędna po ostatnim "/" w prefiksie zostanie zachowana. Na przykład użytkownik ma źródło  `container/folder/subfolder/file.txt` i konfiguruje prefiks jako `folder/sub` , a następnie zachowaną ścieżkę pliku `subfolder/file.txt` . | Nie                                                          |
| Opcja 3: symbol wieloznaczny<br>- wildcardFolderPath | Ścieżka folderu z symbolami wieloznacznymi pod podanym kontenerem skonfigurowanym w zestawie danych do filtrowania folderów źródłowych. <br>Dozwolone symbole wieloznaczne to: `*` (dopasowuje zero lub więcej znaków) i `?` (dopasowuje zero lub pojedynczy znak). Użyj `^` do ucieczki, jeśli nazwa folderu ma symbol wieloznaczny lub ten znak ucieczki wewnątrz. <br>Zobacz więcej przykładów w [przykładach folderów i filtrów plików](#folder-and-file-filter-examples). | Nie                                            |
| Opcja 3: symbol wieloznaczny<br>- wildcardFileName | Nazwa pliku z symbolami wieloznacznymi w danym kontenerze i ścieżce folderu (lub ścieżki do symboli wieloznacznych) do filtrowania plików źródłowych. <br>Dozwolone symbole wieloznaczne to: `*` (dopasowuje zero lub więcej znaków) i `?` (dopasowuje zero lub pojedynczy znak). Użyj `^` do ucieczki, jeśli nazwa pliku ma symbol wieloznaczny lub ten znak ucieczki wewnątrz. Zobacz więcej przykładów w [przykładach folderów i filtrów plików](#folder-and-file-filter-examples). | Tak |
| OPCJA 4: Lista plików<br>- fileListPath | Wskazuje, aby skopiować dany zestaw plików. Wskaż plik tekstowy zawierający listę plików, które chcesz skopiować, jeden plik w wierszu, który jest ścieżką względną do ścieżki skonfigurowanej w zestawie danych.<br/>W przypadku korzystania z tej opcji nie należy określać nazwy pliku w zestawie danych. Zobacz więcej przykładów na [listach plików](#file-list-examples). | Nie |
| ***Ustawienia dodatkowe:*** |  | |
| rozpoznawania | Wskazuje, czy dane są odczytane cyklicznie z podfolderów, czy tylko z określonego folderu. Należy pamiętać, że gdy wartość **cykliczna** jest ustawiona na **wartość true** , a ujścia jest magazynem opartym na plikach, pusty folder lub podfolder nie jest kopiowany ani tworzony w ujścia. <br>Dozwolone wartości to **true** (wartość domyślna) i **false**.<br>Ta właściwość nie ma zastosowania podczas konfigurowania `fileListPath` . | Nie |
| deleteFilesAfterCompletion | Wskazuje, czy pliki binarne zostaną usunięte z magazynu źródłowego po pomyślnym przeniesieniu do magazynu docelowego. Plik jest usuwany dla każdego pliku, więc w przypadku niepowodzenia działania kopiowania niektóre pliki zostały już skopiowane do lokalizacji docelowej i usunięte ze źródła, podczas gdy inne nadal pozostają w magazynie źródłowym. <br/>Ta właściwość jest prawidłowa tylko w scenariuszu kopiowania plików binarnych. Wartość domyślna: false. | Nie |
| modifiedDatetimeStart    | Pliki są filtrowane na podstawie atrybutu: Ostatnia modyfikacja. <br>Pliki zostaną wybrane, jeśli czas ostatniej modyfikacji mieści się w przedziale czasu między `modifiedDatetimeStart` i `modifiedDatetimeEnd` . Czas jest stosowany do strefy czasowej UTC w formacie "2018 r-12-01T05:00:00Z". <br> Właściwości mogą mieć **wartość null**, co oznacza, że żaden filtr atrybutu pliku nie zostanie zastosowany do zestawu danych.  Gdy `modifiedDatetimeStart` ma wartość DateTime, ale `modifiedDatetimeEnd` jest **równa null**, to pliki, których ostatni zmodyfikowany atrybut jest większy lub równy wartości DateTime zostanie zaznaczone.  Gdy `modifiedDatetimeEnd` ma wartość DateTime, ale `modifiedDatetimeStart` jest **równa null**, pliki, których ostatni zmodyfikowany atrybut jest mniejszy niż wartość DateTime zostanie wybrana.<br/>Ta właściwość nie ma zastosowania podczas konfigurowania `fileListPath` . | Nie                                            |
| modifiedDatetimeEnd      | Jak wyżej.                                               | Nie                                            |
| enablePartitionDiscovery | W przypadku plików, które są partycjonowane, określ, czy przeanalizować partycje ze ścieżki pliku i dodać je jako dodatkowe kolumny źródłowe.<br/>Dozwolone wartości to **false** (wartość domyślna) i **true**. | Nie                                            |
| partitionRootPath | Gdy odnajdywanie partycji jest włączone, określ bezwzględną ścieżkę katalogu głównego, aby odczytać partycjonowane foldery jako kolumny danych.<br/><br/>Jeśli nie jest określony, domyślnie,<br/>— Jeśli używasz ścieżki pliku w zestawie danych lub liście plików w źródle, ścieżka katalogu głównego partycji jest ścieżką skonfigurowaną w zestawie danych.<br/>— Jeśli używasz wieloznacznego filtru folderów, ścieżka katalogu głównego partycji jest ścieżką podrzędną przed pierwszym symbolem wieloznacznym.<br/>— Jeśli używasz prefiksu, ścieżka katalogu głównego partycji jest ścieżką podrzędną przed ostatnią "/". <br/><br/>Na przykład przy założeniu, że ścieżka w zestawie danych jest konfigurowana jako "root/folder/Year = 2020/miesiąc = 08/Day = 27":<br/>— W przypadku określenia ścieżki katalogu głównego partycji jako "root/folder/Year = 2020" działanie Copy (kopiowanie) wygeneruje dwie kolejne kolumny `month` i `day` wartości "08" i "27" (oprócz kolumn wewnątrz plików).<br/>— Jeśli ścieżka katalogu głównego partycji nie zostanie określona, nie zostanie wygenerowane żadne dodatkowe kolumny. | Nie                                            |
| maxConcurrentConnections |Górny limit równoczesnych połączeń ustanowiony dla magazynu danych podczas uruchamiania działania. Określ wartość tylko wtedy, gdy chcesz ograniczyć połączenia współbieżne.| Nie                                            |

> [!NOTE]
> W przypadku formatu tekstu Parquet/Unlimited typ **BlobSource** dla źródła działania Copy wymienionego w następnej sekcji jest nadal obsługiwany jako w celu zapewnienia zgodności z poprzednimi wersjami. Zalecamy użycie nowego modelu do momentu przełączenia interfejsu użytkownika Data Factory tworzenia w celu wygenerowania tych nowych typów.

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

> [!NOTE]
> `$logs`Kontener, który jest tworzony automatycznie, gdy analityka magazynu jest włączona dla konta magazynu, nie jest wyświetlany, gdy operacja wyświetlania kontenera jest wykonywana za pośrednictwem interfejsu użytkownika Data Factory. Ścieżka pliku musi być podana bezpośrednio dla Data Factory, aby można było używać plików z `$logs` kontenera.

### <a name="blob-storage-as-a-sink-type"></a>BLOB Storage jako typ ujścia

[!INCLUDE [data-factory-v2-file-sink-formats](../../includes/data-factory-v2-file-sink-formats.md)] 

Następujące właściwości są obsługiwane w przypadku usługi Azure Blob Storage w obszarze `storeSettings` Ustawienia w obiekcie ujścia kopiowania opartym na formacie:

| Właściwość                 | Opis                                                  | Wymagane |
| ------------------------ | ------------------------------------------------------------ | -------- |
| typ                     | `type`Właściwość w obszarze `storeSettings` musi być ustawiona na `AzureBlobStorageWriteSettings` . | Tak      |
| copyBehavior             | Definiuje zachowanie kopiowania, gdy źródłem są pliki z magazynu danych opartego na plikach.<br/><br/>Dozwolone wartości to:<br/><b>-PreserveHierarchy (domyślnie)</b>: zachowuje hierarchię plików w folderze docelowym. Ścieżka względna pliku źródłowego do folderu źródłowego jest taka sama jak ścieżka względna pliku docelowego do folderu docelowego.<br/><b>-FlattenHierarchy</b>: wszystkie pliki z folderu źródłowego znajdują się na pierwszym poziomie folderu docelowego. Pliki docelowe mają automatycznie generowane nazwy. <br/><b>-MergeFiles</b>: Scala wszystkie pliki z folderu źródłowego do jednego pliku. Jeśli nazwa pliku lub obiektu BLOB jest określona, scalona nazwa pliku jest podaną nazwą. W przeciwnym razie jest to automatycznie wygenerowana nazwa pliku. | Nie       |
| blockSizeInMB | Określ rozmiar bloku (w megabajtach), który będzie używany do zapisywania danych w blokowych obiektach Blob. Dowiedz się więcej na [temat blokowych obiektów BLOB](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-block-blobs). <br/>Dozwolona wartość wynosi *od 4 MB do 100 MB*. <br/>Domyślnie Data Factory automatycznie określa rozmiar bloku na podstawie typu i danych magazynu źródłowego. W przypadku kopiowania niebinarnego do magazynu obiektów BLOB domyślny rozmiar bloku to 100 MB, co może zmieścić się w (maksymalnie) 4,95 TB danych. Nie jest to optymalne, gdy dane nie są duże, szczególnie w przypadku korzystania z własnego środowiska Integration Runtime z nieprawidłowymi połączeniami sieciowymi, które powodują problemy z upływem limitu czasu operacji lub wydajności. Można jawnie określić rozmiar bloku, dzięki czemu `blockSizeInMB*50000` jest wystarczająco duży, aby przechowywać dane. W przeciwnym razie uruchomienie działania kopiowania zakończy się niepowodzeniem. | Nie |
| maxConcurrentConnections |Górny limit równoczesnych połączeń ustanowiony dla magazynu danych podczas uruchamiania działania. Określ wartość tylko wtedy, gdy chcesz ograniczyć połączenia współbieżne.| Nie       |

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
| `container/Folder*` | (puste, Użyj domyślnego) | fałsz | kontener<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File2.jsna**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.jsna<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `container/Folder*` | (puste, Użyj domyślnego) | true | kontener<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File2.jsna**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.jsna**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `container/Folder*` | `*.csv` | fałsz | kontener<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.jsna<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.jsna<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `container/Folder*` | `*.csv` | true | kontener<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.jsna<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.jsna<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="file-list-examples"></a>Przykłady listy plików

W tej sekcji opisano wyniki działania dotyczące korzystania ze ścieżki listy plików w źródle działania kopiowania.

Załóżmy, że masz następującą źródłową strukturę folderów i chcesz skopiować pliki w pogrubieniu:

| Przykładowa struktura źródła                                      | Zawartość w FileListToCopy.txt                             | Konfiguracja Data Factory                                            |
| ------------------------------------------------------------ | --------------------------------------------------------- | ------------------------------------------------------------ |
| kontener<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.jsna<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.jsna<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Metadane<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;FileListToCopy.txt | File1.csv<br>Subfolder1/File3.csv<br>Subfolder1/File5.csv | **W zestawie danych:**<br>Wbudowane `container`<br>-Ścieżka folderu: `FolderA`<br><br>**W źródle działania kopiowania:**<br>-Ścieżka listy plików: `container/Metadata/FileListToCopy.txt` <br><br>Ścieżka listy plików wskazuje plik tekstowy w tym samym magazynie danych, który zawiera listę plików do skopiowania, jeden plik w wierszu, ze ścieżką względną do ścieżki skonfigurowanej w zestawie danych. |

### <a name="some-recursive-and-copybehavior-examples"></a>Niektóre przykłady cykliczne i copyBehavior

W tej sekcji opisano zachowanie operacji kopiowania dla różnych kombinacji wartości **cyklicznych** i **copyBehavior** .

| rozpoznawania | copyBehavior | Struktura folderu źródłowego | Wyniki docelowe |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Folder docelowy, Folder1, jest tworzony z tą samą strukturą co Źródło:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 |
| true |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Folder docelowy, Folder1, jest tworzony przy użyciu następującej struktury: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatycznie wygenerowana nazwa dla plik1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatycznie wygenerowana nazwa dla plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatycznie wygenerowana nazwa dla file3<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatycznie wygenerowana nazwa dla File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatycznie wygenerowana nazwa dla File5 |
| true |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Folder docelowy, Folder1, jest tworzony przy użyciu następującej struktury: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik1 + plik2 + file3 + File4 + File5 zawartość jest scalana w jeden plik z automatycznie wygenerowaną nazwą pliku. |
| fałsz |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Folder docelowy, Folder1, jest tworzony przy użyciu następującej struktury: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/><br/>Subfolder1 z file3, File4 i File5 nie są pobierane. |
| fałsz |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Folder docelowy, Folder1, jest tworzony przy użyciu następującej struktury: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatycznie wygenerowana nazwa dla plik1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatycznie wygenerowana nazwa dla plik2<br/><br/>Subfolder1 z file3, File4 i File5 nie są pobierane. |
| fałsz |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Folder docelowy, Folder1, jest tworzony przy użyciu następującej struktury:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Wartość plik1 + plik2 jest scalana w jeden plik z automatycznie wygenerowaną nazwą pliku. automatycznie wygenerowana nazwa dla plik1<br/><br/>Subfolder1 z file3, File4 i File5 nie są pobierane. |

## <a name="preserving-metadata-during-copy"></a>Zachowywanie metadanych podczas kopiowania

Podczas kopiowania plików z usługi Amazon S3, Azure Blob Storage lub Azure Data Lake Storage Gen2 do Azure Data Lake Storage Gen2 lub magazynu obiektów blob platformy Azure można wybrać opcję zachowywania metadanych plików wraz z danymi. Dowiedz się więcej na temat [zachowywania metadanych](copy-activity-preserve-metadata.md#preserve-metadata).

## <a name="mapping-data-flow-properties"></a>Mapowanie właściwości przepływu danych

Gdy przekształcasz dane w celu mapowania przepływów danych, możesz odczytywać i zapisywać pliki z usługi Azure Blob Storage w następujących formatach:

- [Avro](format-avro.md#mapping-data-flow-properties)
- [Tekst rozdzielany](format-delimited-text.md#mapping-data-flow-properties)
- [Delta](format-delta.md#mapping-data-flow-properties)
- [Excel](format-excel.md#mapping-data-flow-properties)
- [JSON](format-json.md#mapping-data-flow-properties)
- [Parquet](format-parquet.md#mapping-data-flow-properties)

Ustawienia specyficzne dla formatu znajdują się w dokumentacji dla tego formatu. Aby uzyskać więcej informacji, zobacz temat [przekształcanie źródła w mapowaniu przepływu danych](data-flow-source.md) i [transformacji ujścia w mapowaniu przepływu danych](data-flow-sink.md).

### <a name="source-transformation"></a>Transformacja źródła

W transformacji źródłowej można odczytywać z kontenera, folderu lub pojedynczego pliku w usłudze Azure Blob Storage. Użyj karty **Opcje źródła** , aby zarządzać sposobem odczytywania plików. 

![Opcje źródła](media/data-flow/sourceOptions1.png "Opcje źródła")

**Ścieżki symboli wieloznacznych:** Użycie wzorca wieloznacznego poinstruuje Data Factory pętlą przez każdy pasujący folder i plik w jednym transformację źródła. Jest to efektywny sposób przetwarzania wielu plików w ramach pojedynczego przepływu. Dodaj wiele symboli wieloznacznych wzorców ze znakiem plus, który pojawia się po umieszczeniu wskaźnika myszy na istniejącym wzorcu symboli wieloznacznych.

Z kontenera źródłowego wybierz serię plików, które pasują do wzorca. W zestawie danych można określić tylko kontener. Ścieżka symbolu wieloznacznego musi zawierać również ścieżkę folderu z folderu głównego.

Przykłady symboli wieloznacznych:

- `*` Reprezentuje dowolny zestaw znaków.
- `**` Reprezentuje zagnieżdżanie katalogów cyklicznych.
- `?` Zamienia jeden znak.
- `[]` Dopasowuje jeden lub więcej znaków w nawiasach.

- `/data/sales/**/*.csv` Pobiera wszystkie pliki CSV w obszarze/Data/Sales.
- `/data/sales/20??/**/` Pobiera wszystkie pliki w 20-wieku.
- `/data/sales/*/*/*.csv` Pobiera pliki CSV dwa poziomy w obszarze/Data/Sales.
- `/data/sales/2004/*/12/[XY]1?.csv` Pobiera wszystkie pliki CSV w grudniu 2004 rozpoczynające się od X lub Y poprzedzone dwucyfrowym numerem.

**Ścieżka katalogu głównego partycji:** Jeśli masz partycjonowane foldery w źródle plików o `key=value` formacie (na przykład `year=2019` ), możesz przypisać najwyższy poziom tego drzewa folderów partycji do nazwy kolumny w strumieniu danych przepływu danych.

Najpierw ustaw symbol wieloznaczny, aby uwzględnić wszystkie ścieżki, które są folderami partycjonowanymi oraz pliki liści, które mają zostać odczytane.

![Ustawienia pliku źródłowego partycji](media/data-flow/partfile2.png "Ustawienie pliku partycji")

Użyj ustawienia **ścieżka katalogu głównego partycji** , aby określić, jaki jest najwyższy poziom struktury folderów. Gdy przeglądasz zawartość danych za pośrednictwem wersji zapoznawczej, zobaczysz, że Data Factory doda rozpoznane partycje znalezione na każdym z poziomów folderów.

![Ścieżka katalogu głównego partycji](media/data-flow/partfile1.png "Podgląd ścieżki katalogu głównego partycji")

**Lista plików:** To jest zestaw plików. Utwórz plik tekstowy, który zawiera listę plików ścieżek względnych do przetworzenia. Wskaż ten plik tekstowy.

**Kolumna do przechowywania nazwy pliku:** Zapisz nazwę pliku źródłowego w kolumnie w danych. Wprowadź tutaj nazwę nowej kolumny, aby zapisać ciąg nazw plików.

**Po zakończeniu:** Wybierz, aby nic nie robić z plikiem źródłowym po uruchomieniu przepływu danych, usuń plik źródłowy lub Przenieś plik źródłowy. Ścieżki do przenoszenia są względne.

Aby przenieść pliki źródłowe do innej lokalizacji po przetworzeniu, najpierw wybierz pozycję "Przenieś" dla operacji na pliku. Następnie ustaw katalog "z". Jeśli nie używasz symboli wieloznacznych dla ścieżki, ustawienie "od" będzie takie samo jak folder źródłowy.

Jeśli masz ścieżkę źródłową z symbolem wieloznacznym, składnia będzie wyglądać następująco:

`/data/sales/20??/**/*.csv`

Możesz określić wartość "od" jako:

`/data/sales`

Można też określić wartość "do" jako:

`/backup/priorSales`

W takim przypadku wszystkie pliki, które zostały objęte źródłem, `/data/sales` są przenoszone do `/backup/priorSales` .

> [!NOTE]
> Operacje na plikach są uruchamiane tylko wtedy, gdy rozpoczyna się przepływ danych z uruchomienia potoku (debugowania lub przebiegu wykonywania) używającego działania wykonywania przepływu danych w potoku. Operacje na plikach *nie są* uruchamiane w trybie debugowania przepływu danych.

**Filtruj według ostatniej modyfikacji:** Można filtrować, które pliki są przetwarzane, określając zakres dat, po którym były ostatnio modyfikowane.  Wszystkie daty i godziny są podane w formacie UTC. 

### <a name="sink-properties"></a>Właściwości ujścia

W transformacji ujścia można pisać do kontenera lub folderu w usłudze Azure Blob Storage. Użyj karty **Ustawienia** , aby zarządzać sposobem, w jaki pliki są zapisywane.

![Opcje ujścia](media/data-flow/file-sink-settings.png "Opcje ujścia")

**Wyczyść folder:** Określa, czy folder docelowy jest usuwany przed zapisaniem danych.

**Opcja nazwy pliku:** Określa sposób nazywania plików docelowych w folderze docelowym. Nazwy plików są następujące:
   - **Domyślnie**: Zezwalaj platformie Spark na nazwy plików na podstawie wartości domyślnych części.
   - **Wzorzec**: wprowadź wzorzec, który wylicza pliki wyjściowe na partycję. Na przykład program `loans[n].csv` utworzy `loans1.csv` , `loans2.csv` i tak dalej.
   - **Na partycję**: Wprowadź jedną nazwę pliku na partycję.
   - **Jako dane w kolumnie**: Ustaw plik wyjściowy na wartość kolumny. Ścieżka jest względna wobec kontenera DataSet, a nie folderu docelowego. Jeśli masz ścieżkę folderu w zestawie danych, zostanie ona zastąpiona.
   - **Dane wyjściowe do pojedynczego pliku**: Połącz podzielone na partycje pliki wyjściowe w jeden nazwany plik. Ścieżka jest określana względem folderu DataSet. Należy pamiętać, że operacja scalania może być niemożliwa w zależności od rozmiaru węzła. Nie zalecamy tej opcji w przypadku dużych zestawów danych.

**Wszystkie oferty:** Określa, czy wszystkie wartości mają być ujęte w cudzysłów.

## <a name="lookup-activity-properties"></a>Właściwości działania Lookup

Aby dowiedzieć się więcej o właściwościach, sprawdź [działanie Lookup (wyszukiwanie](control-flow-lookup-activity.md)).

## <a name="getmetadata-activity-properties"></a>Właściwości działania GetMetadata

Aby uzyskać szczegółowe informacje na temat właściwości, sprawdź [działanie GetMetadata](control-flow-get-metadata-activity.md). 

## <a name="delete-activity-properties"></a>Usuń właściwości działania

Aby uzyskać szczegółowe informacje na temat właściwości, zaznacz opcję [Usuń działanie](delete-activity.md).

## <a name="legacy-models"></a>Starsze modele

>[!NOTE]
>Następujące modele są nadal obsługiwane w celu zapewnienia zgodności z poprzednimi wersjami. Zalecamy użycie nowego modelu wymienionego wcześniej. Interfejs użytkownika tworzenia Data Factory został przełączony w celu wygenerowania nowego modelu.

### <a name="legacy-dataset-model"></a>Model starszego zestawu danych

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | `type`Właściwość zestawu danych musi być ustawiona na wartość `AzureBlob` . | Tak |
| folderPath | Ścieżka do kontenera i folderu w usłudze BLOB Storage. <br/><br/>Filtr symboli wieloznacznych jest obsługiwany dla ścieżki, z wyłączeniem nazwy kontenera. Dozwolone symbole wieloznaczne to: `*` (dopasowuje zero lub więcej znaków) i `?` (dopasowuje zero lub pojedynczy znak). Użyj `^` do ucieczki, jeśli nazwa folderu ma symbol wieloznaczny lub ten znak ucieczki wewnątrz. <br/><br/>Przykład: `myblobcontainer/myblobfolder/` . Zobacz więcej przykładów w [przykładach folderów i filtrów plików](#folder-and-file-filter-examples). | Tak dla działania kopiowania lub wyszukiwania — nie dla działania GetMetadata |
| fileName | Nazwa lub filtr symboli wieloznacznych dla obiektów BLOB pod określoną `folderPath` wartością. Jeśli nie określisz wartości tej właściwości, zestaw danych wskazuje wszystkie obiekty blob w folderze. <br/><br/>W przypadku filtru dozwolone symbole wieloznaczne to: `*` (dopasowuje zero lub więcej znaków) i `?` (dopasowuje zero lub pojedynczy znak).<br/>-Przykład 1: `"fileName": "*.csv"`<br/>-Przykład 2: `"fileName": "???20180427.txt"`<br/>Użyj `^` do ucieczki, jeśli nazwa pliku ma symbol wieloznaczny lub ten znak ucieczki wewnątrz.<br/><br/>Gdy `fileName` nie jest określony dla wyjściowego zestawu danych i `preserveHierarchy` nie jest określony w obiekcie ujścia działania, działanie kopiowania automatycznie generuje nazwę obiektu BLOB z następującym wzorcem: "*Data. [ Identyfikator GUID przebiegu działania]. [GUID if FlattenHierarchy]. [Format, jeśli skonfigurowano]. [kompresja, jeśli jest skonfigurowana]*". Na przykład: "Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt. gz". <br/><br/>Jeśli skopiujesz ze źródła tabelarycznego przy użyciu nazwy tabeli zamiast zapytania, wzorzec nazwy to `[table name].[format].[compression if configured]` . Na przykład: "MyTable.csv". | Nie |
| modifiedDatetimeStart | Pliki są filtrowane na podstawie atrybutu: Ostatnia modyfikacja. Pliki zostaną wybrane, jeśli czas ostatniej modyfikacji mieści się w przedziale czasu między `modifiedDatetimeStart` i `modifiedDatetimeEnd` . Czas jest stosowany do strefy czasowej UTC w formacie "2018 r-12-01T05:00:00Z". <br/><br/> Należy pamiętać, że włączenie tego ustawienia wpłynie na ogólną wydajność przenoszenia danych, gdy chcesz filtrować ogromne ilości plików. <br/><br/> Właściwości mogą być `NULL` , co oznacza, że żaden filtr atrybutu pliku nie zostanie zastosowany do zestawu danych.  Gdy `modifiedDatetimeStart` ma wartość DateTime, ale `modifiedDatetimeEnd` to `NULL` , pliki, których atrybut Last Modified jest większy lub równy wartości DateTime, zostanie wybrany.  Gdy `modifiedDatetimeEnd` ma wartość DateTime, ale `modifiedDatetimeStart` to `NULL` , pliki, których ostatni zmodyfikowany atrybut jest mniejszy niż wartość DateTime zostanie wybrana.| Nie |
| modifiedDatetimeEnd | Pliki są filtrowane na podstawie atrybutu: Ostatnia modyfikacja. Pliki zostaną wybrane, jeśli czas ostatniej modyfikacji mieści się w przedziale czasu między `modifiedDatetimeStart` i `modifiedDatetimeEnd` . Czas jest stosowany do strefy czasowej UTC w formacie "2018 r-12-01T05:00:00Z". <br/><br/> Należy pamiętać, że włączenie tego ustawienia wpłynie na ogólną wydajność przenoszenia danych, gdy chcesz filtrować ogromne ilości plików. <br/><br/> Właściwości mogą być `NULL` , co oznacza, że żaden filtr atrybutu pliku nie zostanie zastosowany do zestawu danych.  Gdy `modifiedDatetimeStart` ma wartość DateTime, ale `modifiedDatetimeEnd` to `NULL` , pliki, których atrybut Last Modified jest większy lub równy wartości DateTime, zostanie wybrany.  Gdy `modifiedDatetimeEnd` ma wartość DateTime, ale `modifiedDatetimeStart` to `NULL` , pliki, których ostatni zmodyfikowany atrybut jest mniejszy niż wartość DateTime zostanie wybrana.| Nie |
| format | Jeśli chcesz skopiować pliki w postaci między magazynami opartymi na plikach (kopia binarna), Pomiń sekcję format w definicjach zestawu danych wejściowych i wyjściowych.<br/><br/>Jeśli chcesz analizować lub generować pliki o określonym formacie, obsługiwane są następujące typy formatu plików: **TextFormat**, **formatu jsonformat**, **AvroFormat**, **OrcFormat** i **ParquetFormat**. Ustaw właściwość **Type** w polu **Format** na jedną z tych wartości. Aby uzyskać więcej informacji, zobacz sekcję [Format tekstu](supported-file-formats-and-compression-codecs-legacy.md#text-format), [Format JSON](supported-file-formats-and-compression-codecs-legacy.md#json-format), [Format Avro](supported-file-formats-and-compression-codecs-legacy.md#avro-format), [Format Orc](supported-file-formats-and-compression-codecs-legacy.md#orc-format)i [Parquet format](supported-file-formats-and-compression-codecs-legacy.md#parquet-format) . | Nie (tylko w przypadku scenariusza kopiowania binarnego) |
| kompresja | Określ typ i poziom kompresji danych. Aby uzyskać więcej informacji, zobacz [obsługiwane formaty plików i kodery-dekoder kompresji](supported-file-formats-and-compression-codecs-legacy.md#compression-support).<br/>Obsługiwane typy to **gzip**, **Wklęśnięcie**, **BZip2** i **ZipDeflate**.<br/>Obsługiwane poziomy są **optymalne** i **najszybciej**. | Nie |

>[!TIP]
>Aby skopiować wszystkie obiekty blob w folderze, określ tylko **folderPath** .<br>Aby skopiować pojedynczy obiekt BLOB o podanej nazwie, należy określić **folderPath** dla części folderu **i nazwy pliku w nazwie** .<br>Aby skopiować podzestaw obiektów BLOB w folderze, określ **folderPath** dla części folderu i **nazwy pliku** z filtrem symboli wieloznacznych. 

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

### <a name="legacy-source-model-for-the-copy-activity"></a>Starszy model źródłowy dla działania kopiowania

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | `type`Właściwość źródła działania kopiowania musi być ustawiona na wartość `BlobSource` . | Tak |
| rozpoznawania | Wskazuje, czy dane są odczytane cyklicznie z podfolderów, czy tylko z określonego folderu. Należy pamiętać, że gdy `recursive` jest ustawiona na, `true` a ujścia jest magazynem opartym na plikach, pusty folder lub podfolder nie jest kopiowany ani tworzony w ujścia.<br/>Dozwolone wartości to `true` (domyślne) i `false` . | Nie |
| maxConcurrentConnections |Górny limit równoczesnych połączeń ustanowiony dla magazynu danych podczas uruchamiania działania. Określ wartość tylko wtedy, gdy chcesz ograniczyć połączenia współbieżne.| Nie |

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

### <a name="legacy-sink-model-for-the-copy-activity"></a>Starszy model ujścia dla działania kopiowania

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | `type`Właściwość ujścia działania kopiowania musi być ustawiona na wartość `BlobSink` . | Tak |
| copyBehavior | Definiuje zachowanie kopiowania, gdy źródłem są pliki z magazynu danych opartego na plikach.<br/><br/>Dozwolone wartości to:<br/><b>-PreserveHierarchy (domyślnie)</b>: zachowuje hierarchię plików w folderze docelowym. Ścieżka względna pliku źródłowego do folderu źródłowego jest taka sama jak ścieżka względna pliku docelowego do folderu docelowego.<br/><b>-FlattenHierarchy</b>: wszystkie pliki z folderu źródłowego znajdują się na pierwszym poziomie folderu docelowego. Pliki docelowe mają automatycznie generowane nazwy. <br/><b>-MergeFiles</b>: Scala wszystkie pliki z folderu źródłowego do jednego pliku. Jeśli nazwa pliku lub obiektu BLOB jest określona, scalona nazwa pliku jest podaną nazwą. W przeciwnym razie jest to automatycznie wygenerowana nazwa pliku. | Nie |
| maxConcurrentConnections |Górny limit równoczesnych połączeń ustanowiony dla magazynu danych podczas uruchamiania działania. Określ wartość tylko wtedy, gdy chcesz ograniczyć połączenia współbieżne.| Nie |

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

Aby uzyskać listę magazynów danych, które działanie kopiowania w Data Factory obsługuje jako źródła i ujścia, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
