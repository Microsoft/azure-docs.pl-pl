---
title: Kopiowanie danych w Dynamics (Common Data Service)
description: Dowiedz się, jak kopiować dane z programu Microsoft Dynamics CRM lub Microsoft Dynamics 365 (Common Data Service) do obsługiwanych magazynów danych ujścia lub z obsługiwanych magazynów danych źródłowych do programu Dynamics CRM lub Dynamics 365 przy użyciu działania kopiowania w potoku fabryki danych.
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
ms.date: 11/20/2019
ms.openlocfilehash: c891cb4eca2c286b3ac636e5995714accd591772
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417357"
---
# <a name="copy-data-from-and-to-dynamics-365-common-data-service-or-dynamics-crm-by-using-azure-data-factory"></a>Kopiowanie danych z i do dynamics 365 (common data service) lub dynamics CRM przy użyciu usługi Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule opisano, jak kopiować dane z i do programu Microsoft Dynamics 365 lub Microsoft Dynamics CRM za pomocą funkcji Kopiowanie w usłudze Azure Data Factory. Opiera się na [omówienie działania kopiowania,](copy-activity-overview.md) który przedstawia ogólne omówienie działania kopiowania.

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Ten łącznik jest obsługiwany dla następujących działań:

- [Kopiowanie aktywności](copy-activity-overview.md) z [obsługiwaną macierzą źródło/ujście](copy-activity-overview.md)
- [Działanie odnośnika](control-flow-lookup-activity.md)

Dane z dynamics 365 (common data service) lub dynamics CRM można kopiować do dowolnego obsługiwanego magazynu danych ujścia. Można również skopiować dane z dowolnego obsługiwanego magazynu danych źródłowych do Dynamics 365 (Common Data Service) lub Dynamics CRM. Aby uzyskać listę magazynów danych obsługiwanych jako źródła lub pochłaniacze przez działanie kopiowania, zobacz tabelę [Obsługiwane magazyny danych.](copy-activity-overview.md#supported-data-stores-and-formats)

Ten łącznik Dynamics obsługuje system Dynamics w wersji od 7.x do 9.x zarówno w trybie online, jak i lokalnym. W szczególności,

- Wersja 7.x jest mapowana na program Dynamics CRM 2015
- Wersja 8.x jest mapowana na program Dynamics CRM 2016 i wczesną wersję dynamics 365
- Wersja 9.x jest mapowana na nowszą wersję dynamics 365

Zobacz poniższą tabelę na temat obsługiwanych typów uwierzytelniania i konfiguracji dla odpowiednich wersji/produktów dynamics. (IFD jest skrótem od internet-facing deployment.)

| Wersje Dynamics | Typy uwierzytelniania | Połączone przykłady usług |
|:--- |:--- |:--- |
| Common Data Service <br> Dynamics 365 online <br> Dynamics CRM Online | Główny zobowiązany usługi AAD <br> Usługa Office365 | [Dynamics online + podmiot usługi AAD lub auth usługi Office365](#dynamics-365-and-dynamics-crm-online) |
| Dynamics 365 lokalnie z IFD <br> Dynamics CRM 2016 lokalnie z IFD <br> Dynamics CRM 2015 lokalnie z IFD | IFD | [Dynamika lokalna z erą IFD + IFD](#dynamics-365-and-dynamics-crm-on-premises-with-ifd) |

W szczególności w przypadku dynamics 365 obsługiwane są następujące typy aplikacji:

- Dynamics 365 for Sales
- Dynamics 365 for Customer Service
- Dynamics 365 for Field Service
- Dynamics 365 for Project Service Automation
- Dynamics 365 for Marketing

Inne typy aplikacji, takie jak finanse i operacje, talenty itp.

To złącze Dynamics jest oparte na [oprzyrządowaniu Dynamics XRM](https://docs.microsoft.com/dynamics365/customer-engagement/developer/build-windows-client-applications-xrm-tools).

>[!TIP]
>Aby skopiować dane z **programu Dynamics 365 Finance and Operations,** można użyć [łącznika Dynamics AX](connector-dynamics-ax.md).

## <a name="get-started"></a>Rozpoczęcie pracy

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

W poniższych sekcjach znajdują się szczegółowe informacje o właściwościach, które są używane do definiowania jednostek fabryki danych specyficznych dla dynamics.

## <a name="linked-service-properties"></a>Połączone właściwości usługi

Następujące właściwości są obsługiwane dla usługi połączonej Dynamics.

### <a name="dynamics-365-and-dynamics-crm-online"></a>Dynamics 365 i Dynamics CRM Online

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość typu musi być ustawiona na **Dynamics,** **DynamicsCrm**lub **CommonDataServiceForApps**. | Tak |
| Deploymenttype | Typ wdrożenia instancji Dynamics. Musi to być **"Online"** dla Dynamics online. | Tak |
| identyfikator URI usługi | Adres URL usługi wystąpienia Dynamics, np. `https://adfdynamics.crm.dynamics.com` | Tak |
| authenticationType | Typ uwierzytelniania do łączenia się z serwerem Dynamics. Dozwolone wartości to: **AADServicePrincipal** lub **"Office365"**. | Tak |
| servicePrincipalId | Określ identyfikator klienta aplikacji usługi Azure Active Directory. | Tak podczas `AADServicePrincipal` korzystania z uwierzytelniania |
| servicePrincipalCredentialType | Określ typ poświadczeń, który ma być używany do uwierzytelniania jednostkowego usługi. Dozwolone wartości to: **ServicePrincipalKey** lub **ServicePrincipalCert**. | Tak podczas `AADServicePrincipal` korzystania z uwierzytelniania |
| servicePrincipalCredential | Określ poświadczenie jednostki usługi. <br>Podczas `ServicePrincipalKey` używania jako `servicePrincipalCredential` typ poświadczeń, może być ciąg (ADF będzie szyfrować go po wdrożeniu usługi połączonej) lub odwołanie do klucza tajnego w AKV. <br>Podczas `ServicePrincipalCert` używania `servicePrincipalCredential` jako poświadczenia, powinny być odwołanie do certyfikatu w AKV. | Tak podczas `AADServicePrincipal` korzystania z uwierzytelniania | 
| nazwa użytkownika | Określ nazwę użytkownika, który ma się połączyć z dynamics. | Tak podczas `Office365` korzystania z uwierzytelniania |
| hasło | Określ hasło dla konta użytkownika określonego dla nazwy użytkownika. Oznacz to pole jako SecureString, aby bezpiecznie przechowywać go w fabryce danych lub [odwołaj się do klucza tajnego przechowywanego w usłudze Azure Key Vault.](store-credentials-in-key-vault.md) | Tak podczas `Office365` korzystania z uwierzytelniania |
| connectVia | [Środowisko uruchomieniowe integracji,](concepts-integration-runtime.md) które ma być używane do łączenia się z magazynem danych. Jeśli nie zostanie określony, używa domyślnego środowiska wykonawczego integracji platformy Azure. | Nie dla źródła, Tak dla ujścia, jeśli usługa połączona ze źródłem nie ma środowiska wykonawczego integracji |

>[!NOTE]
>Łącznik Dynamics używany do używania opcjonalnej właściwości "organizationName" do identyfikowania wystąpienia dynamics CRM/365 Online. Podczas gdy nadal działa, zaleca się, aby określić nową właściwość "serviceUri", zamiast tego, aby uzyskać lepszą wydajność dla odnajdywania wystąpienia.

**Przykład: Dynamics online przy użyciu jednostki usługi AAD + uwierzytelnianie klucza**

```json
{  
    "name": "DynamicsLinkedService",  
    "properties": {  
        "type": "Dynamics",  
        "typeProperties": {  
            "deploymentType": "Online",  
            "serviceUri": "https://adfdynamics.crm.dynamics.com",  
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
**Przykład: Dynamics online przy użyciu jednostki usługi AAD + uwierzytelnianie certyfikatów**

```json
{ 
    "name": "DynamicsLinkedService", 
    "properties": { 
        "type": "Dynamics", 
        "typeProperties": { 
            "deploymentType": "Online", 
            "serviceUri": "https://adfdynamics.crm.dynamics.com", 
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

**Przykład: Dynamics online przy użyciu uwierzytelniania usługi Office365**

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
        "typeProperties": {
            "deploymentType": "Online",
            "serviceUri": "https://adfdynamics.crm.dynamics.com",
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

### <a name="dynamics-365-and-dynamics-crm-on-premises-with-ifd"></a>Dynamics 365 i Dynamics CRM lokalnie z IFD

*Dodatkowe właściwości, które można porównać do dynamics online to "hostName" i "port".*

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość typu musi być ustawiona na **Dynamics,** **DynamicsCrm**lub **CommonDataServiceForApps**. | Tak |
| Deploymenttype | Typ wdrożenia instancji Dynamics. Musi to być **"OnPremisesWithIfd"** dla dynamics lokalnie z IFD.| Tak |
| Nazwa hosta | Nazwa hosta lokalnego serwera Dynamics. | Tak |
| port | Port lokalnego serwera Dynamics. | Nie, wartość domyślna to 443 |
| nazwa organizacji | Nazwa organizacji instancji Dynamics. | Tak |
| authenticationType | Typ uwierzytelniania do łączenia się z serwerem Dynamics. Określ **"Ifd"** dla dynamics lokalnie z IFD. | Tak |
| nazwa użytkownika | Określ nazwę użytkownika, który ma się połączyć z dynamics. | Tak |
| hasło | Określ hasło dla konta użytkownika określonego dla nazwy użytkownika. Możesz zaznaczyć to pole jako SecureString, aby bezpiecznie przechowywać je w usłudze ADF lub przechowywać hasło w usłudze Azure Key Vault i pozwolić, aby działanie kopiowania było stamtąd pobierane podczas wykonywania kopiowania danych — dowiedz się więcej z [poświadczenia sklepu w magazynie kluczy](store-credentials-in-key-vault.md). | Tak |
| connectVia | [Środowisko uruchomieniowe integracji,](concepts-integration-runtime.md) które ma być używane do łączenia się z magazynem danych. Jeśli nie zostanie określony, używa domyślnego środowiska wykonawczego integracji platformy Azure. | Nie dla źródła, tak dla zlewu |

**Przykład: Dynamics lokalnie z IFD przy użyciu uwierzytelniania IFD**

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

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania zestawów danych, zobacz artykuł [Zestawy danych.](concepts-datasets-linked-services.md) Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych Dynamics.

Aby skopiować dane z i do Dynamics, obsługiwane są następujące właściwości.

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość typu zestawu danych musi być ustawiona na **DynamicsEntity,** **DynamicsCrmEntity**lub **CommonDataServiceForAppsEntity**. |Tak |
| nazwa podmiotu | Logiczna nazwa jednostki do pobrania. | Nie dla źródła (jeśli określono "zapytanie" w źródle działania), Tak dla ujścia |

**Przykład:**

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

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania działań, zobacz [Pipelines](concepts-pipelines-activities.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez typy źródła i ujścia Dynamics.

### <a name="dynamics-as-a-source-type"></a>Dynamika jako typ źródła

Aby skopiować dane z systemu Dynamics, w sekcji **źródła** działania kopiowania są obsługiwane następujące właściwości.

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość typu źródła działania kopiowania musi być ustawiona na **DynamicsSource**, **DynamicsCrmSource**lub **CommonDataServiceForAppsSource**. | Tak |
| query | FetchXML jest zastrzeżonym językiem zapytań używanym w dynamics (online i lokalnie). Zobacz poniższy przykład. Aby dowiedzieć się więcej, zobacz [Tworzenie zapytań za pomocą pliku FetchXML](https://msdn.microsoft.com/library/gg328332.aspx). | Nie (jeśli określono "entityName" w zestawie danych) |

>[!NOTE]
>Kolumna PK będzie zawsze kopiowana, nawet jeśli projekcja kolumny skonfigurowana w kwerendzie FetchXML nie zawiera jej.

> [!IMPORTANT]
>- Podczas kopiowania danych z dynamics jawne mapowanie kolumn z Dynamics do sink jest opcjonalne, ale wysoce zalecane w celu zapewnienia deterministycznego wyniku kopiowania.
>- Podczas importowania schematu w interfejsie użytkownika ADF wywnioskował schemat, pobierając próbki górnych wierszy z wyniku kwerendy Dynamics, aby zainicjować listę kolumn źródłowych, w którym to przypadku kolumny bez wartości w górnych wierszach zostaną pominięte. To samo zachowanie dotyczy wykonywania kopiowania, jeśli nie ma jawnego mapowania. Można przejrzeć i dodać więcej kolumn do mapowania, które będą honorowane podczas wykonywania kopii.

**Przykład:**

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

### <a name="sample-fetchxml-query"></a>Przykładowa kwerenda FetchXML

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

### <a name="dynamics-as-a-sink-type"></a>Dynamika jako typ zlewu

Aby skopiować dane do dynamics, następujące właściwości są obsługiwane w sekcji ujście działania **kopiowania.**

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość typu ujścia działania kopiowania musi być ustawiona na **DynamicsSink**, **DynamicsCrmSink**lub **CommonDataServiceForAppsSink**. | Tak |
| writeZachody | Zachowanie zapisu operacji.<br/>Dozwoloną wartością jest **"Upsert".** | Tak |
| alternateKeyName | Określ nazwę klucza alternatywnego zdefiniowaną w encji, aby wykonać "Upsert". | Nie |
| writeBatchSize | Liczba wierszy danych zapisanych w dynamics w każdej partii. | Nie (wartość domyślna to 10) |
| ignoreNullValues | Wskazuje, czy podczas operacji zapisu należy zignorować wartości null z danych wejściowych (z wyjątkiem pól klucza).<br/>Dozwolone wartości są **prawdziwe** i **fałszywe**.<br>- **Prawda:** Po wykonaniu operacji upsert/update dane w obiekcie docelowym pozostają niezmienione. Wstaw zdefiniowaną wartość domyślną podczas wykonywania operacji wstawiania.<br/>- **False**: Zaktualizuj dane w obiekcie docelowym do wartości NULL podczas wykonywania operacji upsert/update. Wstaw wartość NULL podczas wykonywania operacji wstawiania. | Nie (wartość domyślna jest false) |

>[!NOTE]
>Domyślna wartość ujścia "**writeBatchSize**" i działania kopiowania "**[parallelCopies](copy-activity-performance-features.md#parallel-copy)**" dla ujścia Dynamics są zarówno 10. W związku z tym 100 rekordów są przesyłane do dynamics jednocześnie.

W przypadku dynamics 365 online istnieje limit [2 równoczesnych wywołań wsadowych na organizację.](https://msdn.microsoft.com/library/jj863631.aspx#Run-time%20limitations) Jeśli ten limit zostanie przekroczony, błąd "Serwer zajęty" jest generowany przed wykonaniem pierwszego żądania. Utrzymanie "writeBatchSize" mniej lub równa 10 pozwoli uniknąć takiego ograniczania równoczesnych wywołań.

Optymalna kombinacja "**writeBatchSize**" i "**parallelCopies**" zależy od schematu jednostki, np. Domyślne ustawienie 10 writeBatchSize * 10 parallelCopies jest zaleceniem zgodnie z usługą Dynamics, która będzie działać dla większości jednostek Dynamics, choć może nie być najlepszą wydajnością. Wydajność można dostroić, dostosowując kombinację w ustawieniach aktywności kopiowania.

**Przykład:**

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

## <a name="data-type-mapping-for-dynamics"></a>Mapowanie typów danych dla systemu Dynamics

Podczas kopiowania danych z systemu Dynamics używane są następujące mapowania z typów danych Dynamics do tymczasowych typów danych data factory. Aby dowiedzieć się, jak działanie kopiowania mapuje schemat źródłowy i typ danych do ujścia, zobacz [Mapowanie schematu i typów danych](copy-activity-schema-and-type-mapping.md).

Skonfiguruj odpowiedni typ danych Data Factory w strukturze zestawu danych na podstawie źródłowego typu danych Dynamics przy użyciu poniższej tabeli mapowania.

| Typ danych dynamics | Tymczasowy typ danych data factory | Obsługiwane jako źródło | Obsługiwane jako zlew |
|:--- |:--- |:--- |:--- |
| Kod Typu Atrybutu.BigInt | Długi | ✓ | ✓ |
| AttributeTypeCode.Boolean | Wartość logiczna | ✓ | ✓ |
| Typ atrybutu.Customer | Guid (identyfikator GUID) | ✓ | |
| Typ atrybutu.DateTime | Datetime (data/godzina) | ✓ | ✓ |
| Typ atrybutu.Dziesiętny | Wartość dziesiętna | ✓ | ✓ |
| Typ atrybutu.Double | Double | ✓ | ✓ |
| Nazwa typu atrybutu.entityname | Ciąg | ✓ | ✓ |
| AtrybutType.Integer | Int32 | ✓ | ✓ |
| Typ atrybutu.Lookup | Guid (identyfikator GUID) | ✓ | ✓ (z jednym celem powiązanych) |
| AtrybutType.ManagedProperty | Wartość logiczna | ✓ | |
| Typ atrybutu.Memo | Ciąg | ✓ | ✓ |
| Typ atrybutu.Pieniądze | Wartość dziesiętna | ✓ | ✓ |
| AttributeType.Owner | Guid (identyfikator GUID) | ✓ | |
| Lista typów atrybutów.picklist | Int32 | ✓ | ✓ |
| Typ atrybutu.Uniqueidentifier | Guid (identyfikator GUID) | ✓ | ✓ |
| Typ atrybutu.String | Ciąg | ✓ | ✓ |
| Typ atrybutu.Stan | Int32 | ✓ | ✓ |
| Typ atrybutu.Status | Int32 | ✓ | ✓ |

> [!NOTE]
> Typy danych Dynamics AttributeType.CalendarRules, AttributeType.MultiSelectPicklist i AttributeType.PartyList nie są obsługiwane.

## <a name="lookup-activity-properties"></a>Właściwości działania odnośnika

Aby dowiedzieć się więcej o właściwościach, sprawdź [działanie odnośnika](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Następne kroki
Aby uzyskać listę magazynów danych obsługiwanych jako źródła i pochłaniacze przez działanie kopiowania w fabryce danych, zobacz [Obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
