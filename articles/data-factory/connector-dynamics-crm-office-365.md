---
title: Kopiuj dane w Dynamics (Common Data Service)
description: Informacje o kopiowaniu danych z programu Microsoft Dynamics CRM lub Microsoft Dynamics 365 (Common Data Service) do obsługiwanych magazynów danych ujścia lub z obsługiwanych magazynów danych źródłowych do programu Dynamics CRM lub Dynamics 365 przy użyciu działania kopiowania w potoku usługi Data Factory.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.custom: seo-lt-2019
ms.date: 09/23/2020
ms.openlocfilehash: 942cbda3652692acc8eedf2ec9508bb501a60547
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91332104"
---
# <a name="copy-data-from-and-to-dynamics-365-common-data-service-or-dynamics-crm-by-using-azure-data-factory"></a>Skopiuj dane z programu i do usługi Dynamics 365 (Common Data Service) lub Dynamics CRM przy użyciu Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule opisano sposób używania działania kopiowania w Azure Data Factory do kopiowania danych z programów i do systemu Microsoft Dynamics 365 i Microsoft Dynamics CRM. Jest ona oparta na [przeglądzie działania kopiowania](copy-activity-overview.md) , która przedstawia ogólne omówienie działania kopiowania.

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Ten łącznik jest obsługiwany dla następujących działań:

- [Działanie kopiowania](copy-activity-overview.md) z [obsługiwaną macierzą źródłową i ujścia](copy-activity-overview.md)
- [Działanie Lookup](control-flow-lookup-activity.md)

Dane z usługi Dynamics 365 (Common Data Service) lub Dynamics CRM można skopiować do dowolnego obsługiwanego magazynu danych ujścia. Możesz również skopiować dane z dowolnego obsługiwanego źródłowego magazynu danych do usługi Dynamics 365 (Common Data Service) lub Dynamics CRM. Listę magazynów danych obsługiwanych przez działanie kopiowania jako źródła i ujścia można znaleźć w tabeli [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) .

Ten łącznik systemu Dynamics obsługuje wersje Dynamics 7 do 9 w trybie online i lokalnie. Więcej szczegółów:

- W wersji 7 są mapowane do programu Dynamics CRM 2015.
- W wersji 8 są mapowane do programu Dynamics CRM 2016 i wczesnej wersji programu Dynamics 365.
- Wersja 9 jest mapowana do nowszej wersji systemu Dynamics 365.

Zapoznaj się z poniższą tabelą obsługiwanych typów i konfiguracji uwierzytelniania dla wersji i produktów dla systemu Dynamics.

| Wersje systemu Dynamics | Typy uwierzytelniania | Przykłady połączonej usługi |
|:--- |:--- |:--- |
| Common Data Service <br/><br/> Dynamics 365 online <br/><br/> Dynamics CRM Online | Nazwa główna usługi Azure Active Directory (Azure AD) <br/><br/> Office 365 | [Dynamics Online i Azure AD Service — uwierzytelnianie podmiotu zabezpieczeń lub pakietu Office 365](#dynamics-365-and-dynamics-crm-online) |
| Lokalne wdrożenie systemu Dynamics 365 z funkcją wdrażania internetowego (IFD) <br/><br/> Program Dynamics CRM 2016 w środowisku lokalnym z IFD <br/><br/> Program Dynamics CRM 2015 w środowisku lokalnym z IFD | IFD | [Lokalne uwierzytelnianie przy użyciu systemu Dynamics z zaifd i IFD](#dynamics-365-and-dynamics-crm-on-premises-with-ifd) |

Dla systemu Dynamics 365 obsługiwane są następujące typy aplikacji:

- Dynamics 365 for Sales
- Dynamics 365 for Customer Service
- Dynamics 365 for Field Service
- Dynamics 365 for Project Service Automation
- Dynamics 365 for Marketing

Ten łącznik nie obsługuje innych typów aplikacji, takich jak finanse, operacje i talent.

Ten łącznik systemu Dynamics jest oparty na [narzędziach programu Dynamics Xrm](https://docs.microsoft.com/dynamics365/customer-engagement/developer/build-windows-client-applications-xrm-tools).

>[!TIP]
>Aby skopiować dane z systemu Dynamics 365 Finanse i operacje, można użyć [łącznika programu Dynamics AX](connector-dynamics-ax.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby użyć tego łącznika z uwierzytelnianiem głównym usługi Azure AD, należy skonfigurować uwierzytelnianie serwer-serwer (S2S) w Common Data Service lub Dynamics. Zapoznaj się z [tym artykułem](https://docs.microsoft.com/powerapps/developer/common-data-service/build-web-applications-server-server-s2s-authentication) , aby uzyskać szczegółowe instrukcje.

## <a name="get-started"></a>Rozpoczęcie pracy

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje dotyczące właściwości, które są używane do definiowania jednostek Data Factory specyficznych dla programu Dynamics.

## <a name="linked-service-properties"></a>Właściwości połączonej usługi

Dla połączonej usługi Dynamics są obsługiwane następujące właściwości.

### <a name="dynamics-365-and-dynamics-crm-online"></a>Dynamics 365 i Dynamics CRM Online

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość Type musi mieć wartość "Dynamics", "DynamicsCrm" lub "CommonDataServiceForApps". | Tak |
| Typ wdrożenia | Typ wdrożenia wystąpienia programu Dynamics. Wartość musi być równa "online" dla usługi Dynamics online. | Tak |
| identyfikator URI usługi | Adres URL usługi wystąpienia programu Dynamics, do którego dostęp jest uzyskiwany z przeglądarki. Przykładem jest "https:// \<organization-name> . CRM [x]. Dynamics. com". | Tak |
| authenticationType | Typ uwierzytelniania służący do nawiązywania połączenia z serwerem Dynamics. Prawidłowe wartości to "AADServicePrincipal" i "Office 365". | Tak |
| servicePrincipalId | Identyfikator klienta aplikacji usługi Azure AD. | Tak, gdy uwierzytelnianie jest "AADServicePrincipal" |
| servicePrincipalCredentialType | Typ poświadczeń, który ma być używany do uwierzytelniania podmiotu zabezpieczeń usługi. Prawidłowe wartości to "ServicePrincipalKey" i "ServicePrincipalCert". | Tak, gdy uwierzytelnianie jest "AADServicePrincipal" |
| servicePrincipalCredential | Poświadczenia podmiotu zabezpieczeń usługi. <br/><br/>Użycie "ServicePrincipalKey" jako typu poświadczenia `servicePrincipalCredential` może być ciągiem, który Azure Data Factory szyfruje podczas wdrażania połączonej usługi. Lub może to być odwołanie do wpisu tajnego w Azure Key Vault. <br/><br/>W przypadku korzystania z poświadczeń "ServicePrincipalCert" `servicePrincipalCredential` musi być odwołaniem do certyfikatu w Azure Key Vault. | Tak, gdy uwierzytelnianie jest "AADServicePrincipal" |
| nazwa użytkownika | Nazwa użytkownika służąca do łączenia się z usługą Dynamics. | Tak, gdy uwierzytelnianie ma wartość "Office 365" |
| hasło | Hasło dla konta użytkownika, które zostało określone jako nazwa użytkownika. Oznacz to pole z "SecureString", aby bezpiecznie przechowywać je w Data Factory, lub [odwoływać się do wpisu tajnego przechowywanego w Azure Key Vault](store-credentials-in-key-vault.md). | Tak, gdy uwierzytelnianie ma wartość "Office 365" |
| Właściwością connectvia | [Środowisko Integration Runtime](concepts-integration-runtime.md) służy do nawiązywania połączenia z magazynem danych. Jeśli wartość nie zostanie określona, właściwość używa domyślnego środowiska Azure Integration Runtime. | Nie dla źródła i tak dla ujścia, jeśli połączona usługa nie ma środowiska Integration Runtime |

>[!NOTE]
>Łącznik Dynamics korzystał wcześniej z opcjonalnej właściwości **organizationName** , aby zidentyfikować wystąpienie usługi Dynamics CRM lub Dynamics 365 online. Mimo że ta właściwość nadal działa, sugerujemy określenie nowej właściwości **ServiceUri** , aby uzyskać lepszą wydajność odnajdywania wystąpień.

#### <a name="example-dynamics-online-using-azure-ad-service-principal-and-key-authentication"></a>Przykład: Dynamics Online przy użyciu usługi Azure AD — uwierzytelnianie główne i klucz

```json
{  
    "name": "DynamicsLinkedService",  
    "properties": {  
        "type": "Dynamics",  
        "typeProperties": {  
            "deploymentType": "Online",  
            "serviceUri": "https://<organization-name>.crm[x].dynamics.com",  
            "authenticationType": "AADServicePrincipal",  
            "servicePrincipalId": "<service principal id>",  
            "servicePrincipalCredentialType": "ServicePrincipalKey",  
            "servicePrincipalCredential": "<service principal key>"
        },  
        "connectVia": {  
            "referenceName": "<name of Integration Runtime>",  
            "type": "IntegrationRuntimeReference"  
        }  
    }  
}  
```
#### <a name="example-dynamics-online-using-azure-ad-service-principal-and-certificate-authentication"></a>Przykład: Dynamics Online przy użyciu usługi Azure AD — uwierzytelnianie główne i certyfikat

```json
{ 
    "name": "DynamicsLinkedService", 
    "properties": { 
        "type": "Dynamics", 
        "typeProperties": { 
            "deploymentType": "Online", 
            "serviceUri": "https://<organization-name>.crm[x].dynamics.com", 
            "authenticationType": "AADServicePrincipal", 
            "servicePrincipalId": "<service principal id>", 
            "servicePrincipalCredentialType": "ServicePrincipalCert", 
            "servicePrincipalCredential": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<AKV reference>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<certificate name in AKV>" 
            } 
        }, 
        "connectVia": { 
            "referenceName": "<name of Integration Runtime>", 
            "type": "IntegrationRuntimeReference" 
        } 
    } 
} 
```

#### <a name="example-dynamics-online-using-office-365-authentication"></a>Przykład: Dynamics Online przy użyciu uwierzytelniania pakietu Office 365

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
        "typeProperties": {
            "deploymentType": "Online",
            "serviceUri": "https://<organization-name>.crm[x].dynamics.com",
            "authenticationType": "Office365",
            "username": "test@contoso.onmicrosoft.com",
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

### <a name="dynamics-365-and-dynamics-crm-on-premises-with-ifd"></a>Dynamics 365 i Dynamics CRM w środowisku lokalnym z IFD

Dodatkowe właściwości, które są porównywane z usługą Dynamics Online, to **Nazwa hosta** i **port**.

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość Type musi mieć wartość "Dynamics", "DynamicsCrm" lub "CommonDataServiceForApps". | Tak. |
| Typ wdrożenia | Typ wdrożenia wystąpienia programu Dynamics. Wartość musi być równa "OnPremisesWithIfd" dla platformy Dynamics w środowisku lokalnym z IFD.| Tak. |
| Nazw | Nazwa hosta lokalnego serwera Dynamics. | Tak. |
| port | Port lokalnego serwera Dynamics. | Nie. Wartość domyślna to 443. |
| Organizacja | Nazwa organizacji wystąpienia programu Dynamics. | Tak. |
| authenticationType | Typ uwierzytelniania do nawiązywania połączenia z serwerem Dynamics. Określ "IFD" dla platformy Dynamics lokalnie z IFD. | Tak. |
| nazwa użytkownika | Nazwa użytkownika służąca do łączenia się z usługą Dynamics. | Tak. |
| hasło | Hasło dla konta użytkownika określonego dla nazwy użytkownika. To pole można oznaczyć jako "SecureString", aby bezpiecznie przechowywać je w Data Factory. Możesz również przechowywać hasło w Key Vault i pozwolić, aby działanie Copy odciągnąć się od tego momentu, gdy dane są kopiowane. Więcej informacji na temat [poświadczeń sklepu znajduje się w Key Vault](store-credentials-in-key-vault.md). | Tak. |
| Właściwością connectvia | [Środowisko Integration Runtime](concepts-integration-runtime.md) służy do nawiązywania połączenia z magazynem danych. Jeśli wartość nie zostanie określona, właściwość używa domyślnego środowiska Azure Integration Runtime. | Nie dla źródła i tak dla ujścia. |

#### <a name="example-dynamics-on-premises-with-ifd-using-ifd-authentication"></a>Przykład: Dynamics lokalnego z IFD przy użyciu uwierzytelniania IFD

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
        "description": "Dynamics on-premises with IFD linked service using IFD authentication",
        "typeProperties": {
            "deploymentType": "OnPremisesWithIFD",
            "hostName": "contosodynamicsserver.contoso.com",
            "port": 443,
            "organizationName": "admsDynamicsTest",
            "authenticationType": "Ifd",
            "username": "test@contoso.onmicrosoft.com",
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

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania zestawów danych, zobacz artykuł [zestawy danych](concepts-datasets-linked-services.md) . Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych Dynamics.

Aby skopiować dane z i do systemu Dynamics, obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość Type zestawu danych musi być ustawiona na wartość "DynamicsEntity", "DynamicsCrmEntity" lub "CommonDataServiceForAppsEntity". |Tak |
| entityName | Logiczna Nazwa jednostki do pobrania. | Nie dla źródła, jeśli źródło działania jest określone jako "Query" i tak dla ujścia |

#### <a name="example"></a>Przykład

```json
{
    "name": "DynamicsDataset",
    "properties": {
        "type": "DynamicsEntity",
        "schema": [],
        "typeProperties": {
            "entityName": "account"
        },
        "linkedServiceName": {
            "referenceName": "<Dynamics linked service name>",
            "type": "linkedservicereference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania działań, zobacz artykuł [potoki](concepts-pipelines-activities.md) . Ta sekcja zawiera listę właściwości obsługiwanych przez typy obiektów źródłowych i obiektów ujścia systemu Dynamics.

### <a name="dynamics-as-a-source-type"></a>Dynamics jako typ źródła

Aby skopiować dane z systemu Dynamics, sekcja **Źródło** działania kopiowania obsługuje następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość Type źródła działania Copy musi mieć wartość "DynamicsSource", "DynamicsCrmSource" lub "CommonDataServiceForAppsSource". | Tak |
| query | FetchXML to własny język zapytań używany w usłudze Dynamics Online i lokalnie. Zobacz poniższy przykład. Aby dowiedzieć się więcej, zobacz [Tworzenie zapytań za pomocą FetchXML](https://msdn.microsoft.com/library/gg328332.aspx). | Nie `entityName` , jeśli w zestawie danych jest określony |

>[!NOTE]
>Kolumna klucza podstawowego będzie zawsze kopiowana nawet wtedy, gdy rzutowanie kolumn skonfigurowane w zapytaniu FetchXML nie zawiera go.

> [!IMPORTANT]
>- Podczas kopiowania danych z systemu Dynamics jawne Mapowanie kolumn z Dynamics do ujścia jest opcjonalne. Jednak zdecydowanie zalecamy mapowanie, aby zapewnić deterministyczny wynik kopiowania.
>- Gdy Data Factory importuje schemat w interfejsie użytkownika tworzenia, zostanie on wytworzony ze schematu. Robi to przez próbkowanie najważniejszych wierszy z wyniku zapytania programu Dynamics w celu zainicjowania listy kolumn źródłowych. W takim przypadku kolumny bez wartości w górnych wierszach nie są pomijane. Takie samo zachowanie ma zastosowanie w przypadku wykonywania kopii w przypadku braku jawnego mapowania. Możesz przejrzeć i dodać więcej kolumn do mapowania, które są honorowane podczas wykonywania kopiowania.

#### <a name="example"></a>Przykład

```json
"activities":[
    {
        "name": "CopyFromDynamics",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Dynamics input dataset>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "DynamicsSource",
                "query": "<FetchXML Query>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="sample-fetchxml-query"></a>Przykładowe zapytanie FetchXML

```xml
<fetch>
  <entity name="account">
    <attribute name="accountid" />
    <attribute name="name" />
    <attribute name="marketingonly" />
    <attribute name="modifiedon" />
    <order attribute="modifiedon" descending="false" />
    <filter type="and">
      <condition attribute ="modifiedon" operator="between">
        <value>2017-03-10 18:40:00z</value>
        <value>2017-03-12 20:40:00z</value>
      </condition>
    </filter>
  </entity>
</fetch>
```

### <a name="dynamics-as-a-sink-type"></a>Dynamics jako typ ujścia

Aby skopiować dane do dynamiki, sekcja **ujścia** działania kopiowania obsługuje następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość Type ujścia działania Copy musi mieć wartość "DynamicsSink", "DynamicsCrmSink" lub "CommonDataServiceForAppsSink". | Tak. |
| writeBehavior | Zachowanie zapisu operacji. Wartość musi być równa "upsert". | Tak |
| alternateKeyName | Alternatywna nazwa klucza zdefiniowana w jednostce do wykonania upsert. | Nie. |
| writeBatchSize | Liczba wierszy danych zapisywana w Dynamics w każdej partii. | Nie. Wartość domyślna to 10. |
| ignoreNullValues | Czy ignorować wartości null z danych wejściowych innych niż pola klucza podczas operacji zapisu.<br/><br/>Prawidłowe wartości to **true** i **false**:<ul><li>**True**: pozostawienie danych w obiekcie docelowym nie zmienia się po wykonaniu operacji upsert lub Update. Wstaw zdefiniowaną wartość domyślną podczas wykonywania operacji wstawiania.</li><li>**Fałsz**: zaktualizuj dane w obiekcie docelowym do wartości null po wykonaniu operacji upsert lub Update. Wstaw wartość null po wykonaniu operacji wstawiania.</li></ul> | Nie. Wartość domyślna to **false**. |

>[!NOTE]
>Wartość domyślna zarówno dla **writeBatchSize** ujścia, jak i działania copy **[ParallelCopies](copy-activity-performance-features.md#parallel-copy)** dla usługi Dynamics sink to 10. W związku z tym rekordy 100 są jednocześnie przesyłane domyślnie do programu Dynamics.

W przypadku usługi Dynamics 365 online obowiązuje limit [dwóch współbieżnych wywołań usługi Batch na organizację](https://msdn.microsoft.com/library/jj863631.aspx#Run-time%20limitations). W przypadku przekroczenia tego limitu zostanie zgłoszony wyjątek "serwer zajęty", zanim pierwsze żądanie zostanie kiedykolwiek uruchomione. Kontynuuj **writeBatchSize** o 10 lub mniej, aby uniknąć tego ograniczenia współbieżnych wywołań.

Optymalna kombinacja **writeBatchSize** i **parallelCopies** zależy od schematu jednostki. Elementy schematu obejmują liczbę kolumn, rozmiar wiersza i liczbę wtyczek, przepływów pracy lub przepływów prac, które podłączono do tych wywołań. Domyślnym ustawieniem **writeBatchSize** (10) &times; **parallelCopies** (10) jest zalecenie zależne od usługi Dynamics. Ta wartość działa w przypadku większości jednostek dynamiki, chociaż może nie dać najlepszej wydajności. Możesz dostosować wydajność przez dostosowanie kombinacji w ustawieniach działania kopiowania.

#### <a name="example"></a>Przykład

```json
"activities":[
    {
        "name": "CopyToDynamics",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Dynamics output dataset>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "DynamicsSink",
                "writeBehavior": "Upsert",
                "writeBatchSize": 10,
                "ignoreNullValues": true
            }
        }
    }
]
```

## <a name="data-type-mapping-for-dynamics"></a>Mapowanie typu danych dla systemu Dynamics

Podczas kopiowania danych z programu Dynamics w poniższej tabeli przedstawiono mapowania z typów danych Dynamics, aby Data Factory pośrednie typy danych. Aby dowiedzieć się, jak działanie kopiowania mapuje do schematu źródłowego, a typ danych jest mapowany na ujścia, zobacz [Mapowanie schematu i typu danych](copy-activity-schema-and-type-mapping.md).

Skonfiguruj odpowiedni typ danych Data Factory w strukturze zestawu danych, który jest oparty na typie danych źródła Dynamics, używając następującej tabeli mapowania:

| Typ danych Dynamics | Data Factory typ danych pośrednich | Obsługiwane jako źródło | Obsługiwane jako ujścia |
|:--- |:--- |:--- |:--- |
| AttributeTypeCode. BigInt | Długo | ✓ | ✓ |
| AttributeTypeCode. Boolean | Boolean | ✓ | ✓ |
| AttributeType. Customer | GUID | ✓ | ✓ (Zobacz [wskazówki](#writing-data-to-a-lookup-field)) |
| AttributeType. DateTime | Datetime (data/godzina) | ✓ | ✓ |
| AttributeType. Decimal | Liczba dziesiętna | ✓ | ✓ |
| AttributeType. Double | Double | ✓ | ✓ |
| AttributeType. EntityName | String | ✓ | ✓ |
| AttributeType. Integer | Int32 | ✓ | ✓ |
| AttributeType. Lookup | GUID | ✓ | ✓ (Zobacz [wskazówki](#writing-data-to-a-lookup-field)) |
| AttributeType. ManagedProperty | Boolean | ✓ | |
| AttributeType. MEMO | String | ✓ | ✓ |
| AttributeType. Money | Liczba dziesiętna | ✓ | ✓ |
| AttributeType. Owner | GUID | ✓ | ✓ (Zobacz [wskazówki](#writing-data-to-a-lookup-field)) |
| AttributeType. Lista wyboru | Int32 | ✓ | ✓ |
| AttributeType. unikatowy identyfikator | GUID | ✓ | ✓ |
| AttributeType. String | String | ✓ | ✓ |
| AttributeType. State | Int32 | ✓ | ✓ |
| AttributeType. status | Int32 | ✓ | ✓ |

> [!NOTE]
> Typy danych Dynamics, AttributeType **. CalendarRules**, **attributeType. MultiSelectPicklist**i **attributeType. PartyList** nie są obsługiwane.

## <a name="writing-data-to-a-lookup-field"></a>Zapisywanie danych w polu odnośnika

Aby zapisać dane w polu odnośnika z wieloma obiektami docelowymi, takimi jak klient i właściciel, postępuj zgodnie z poniższymi wskazówkami i przykładami:

1. Upewnij się, że źródło zawiera zarówno wartość pola, jak i odpowiednią nazwę jednostki docelowej.
   - Jeśli wszystkie rekordy są mapowane na tę samą jednostkę docelową, upewnij się, że jest spełniony jeden z następujących warunków:
      - Dane źródłowe zawierają kolumnę, w której jest przechowywana Nazwa jednostki docelowej.
      - Dodano dodatkową kolumnę w źródle działania kopiowania w celu zdefiniowania jednostki docelowej.
   - Jeśli różne rekordy są mapowane na różne jednostki docelowe, upewnij się, że dane źródłowe zawierają kolumnę przechowującą odpowiednią nazwę jednostki docelowej.

1. Mapuj zarówno kolumny wartości, jak i odwołania do jednostki ze źródła do ujścia. Kolumna odwołania do jednostki musi być zamapowana na kolumnę wirtualną przy użyciu specjalnego wzorca nazewnictwa `{lookup_field_name}@EntityReference` . Kolumna nie istnieje w rzeczywistości w usłudze Dynamics. Służy do wskazywania, że ta kolumna jest kolumną metadanych danego wieloelementowego pola wyszukiwania.

Załóżmy na przykład, że źródło ma te dwie kolumny:

- Kolumna **CustomerField** typu **GUID**, która jest wartością klucza podstawowego jednostki docelowej w Dynamics.
- Kolumna **docelowa** typu **String**, która jest nazwą logiczną jednostki docelowej.

Załóżmy również, że chcesz skopiować takie dane do pola jednostki Dynamics **CustomerField** typu **Klient**.

W mapowaniu kolumn operacji kopiowania Mapuj dwie kolumny w następujący sposób:

- **CustomerField** do **CustomerField**. To mapowanie jest normalnym mapowaniem pól.
- **Docelowa** **CustomerField \@ EntityReference**. Kolumna ujścia jest kolumną wirtualną reprezentującą odwołanie do jednostki. Wprowadzanie takich nazw pól w mapowaniu, ponieważ nie są one wyświetlane przez zaimportowanie schematów.

![Mapowanie kolumn pola wyszukiwania w Dynamics](./media/connector-dynamics-crm-office-365/connector-dynamics-lookup-field-column-mapping.png)

Jeśli wszystkie rekordy źródłowe są mapowane na tę samą jednostkę docelową, a dane źródłowe nie zawierają nazwy jednostki docelowej, Oto skrót: w źródle działania kopiowania Dodaj dodatkową kolumnę. Nazwij nową kolumnę przy użyciu wzorca `{lookup_field_name}@EntityReference` , ustaw wartość na nazwę jednostki docelowej, a następnie kontynuuj Mapowanie kolumn w zwykły sposób. Jeśli nazwy kolumn źródłowych i obiektów ujścia są identyczne, można również pominąć jawne Mapowanie kolumn, ponieważ działanie kopiowania domyślnie mapuje kolumny według nazwy.

![Wyszukiwanie Dynamics-pole Dodawanie kolumny odwołania do jednostki](./media/connector-dynamics-crm-office-365/connector-dynamics-add-entity-reference-column.png)

## <a name="lookup-activity-properties"></a>Właściwości działania Lookup

Aby uzyskać szczegółowe informacje na temat właściwości, zobacz [Wyszukiwanie aktywności](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Następne kroki
Aby uzyskać listę danych, na których jest przechowywane działanie kopiowania w Data Factory obsługuje jako źródła i ujścia, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
