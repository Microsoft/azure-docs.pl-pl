---
title: Kopiowanie danych z usługi Office 365 przy użyciu usługi Azure Data Factory
description: Dowiedz się, jak kopiować dane z usługi Office 365 do obsługiwanych magazynów danych ujścia przy użyciu działania kopiowania w potoku usługi Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/20/2019
ms.author: jingwang
ms.openlocfilehash: ea68fa8d9326e6d9ebb4f475d16ac83959cae6e5
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416870"
---
# <a name="copy-data-from-office-365-into-azure-using-azure-data-factory"></a>Kopiowanie danych z usługi Office 365 na platformę Azure przy użyciu usługi Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Usługa Azure Data Factory integruje się z [danymi programu Microsoft Graph ,](https://docs.microsoft.com/graph/data-connect-concept-overview)co pozwala na wprowadzenie bogatych danych organizacyjnych w dzierżawie usługi Office 365 na platformę Azure w skalowalny sposób i tworzenie aplikacji analitycznych i wyodrębnianie szczegółowych informacji na podstawie tych cennych zasobów danych. Integracja z zarządzaniem dostępem uprzywilejowanym zapewnia bezpieczną kontrolę dostępu dla cennych wyselekcjonowanych danych w usłudze Office 365.  Zapoznaj się z [tym łączem,](https://docs.microsoft.com/graph/data-connect-concept-overview) aby uzyskać omówienie połączenia danych programu Microsoft Graph i zapoznać się z [tym łączem,](https://docs.microsoft.com/graph/data-connect-policies#licensing) aby uzyskać informacje o licencjonowaniu.

W tym artykule opisano, jak skopiować dane z usługi Office 365 za pomocą działania kopiowania w usłudze Azure Data Factory. Opiera się na [omówienie działania kopiowania](copy-activity-overview.md) artykuł, który przedstawia ogólny przegląd działania kopiowania.

## <a name="supported-capabilities"></a>Obsługiwane możliwości
Łącznik usługi ADF Office 365 i połączenie danych programu Microsoft Graph umożliwiają skalowanie pozyskiwania różnych typów zestawów danych ze skrzynek pocztowych obsługujących usługę Exchange Email, w tym kontaktów z książki adresowej, zdarzeń kalendarza, wiadomości e-mail, informacji o użytkowniku, ustawień skrzynki pocztowej itd.  Zapoznaj się [tutaj,](https://docs.microsoft.com/graph/data-connect-datasets) aby zobaczyć pełną listę dostępnych zestawów danych.

Na razie w ramach działania pojedynczej kopii można kopiować tylko **dane z usługi Office 365 do usługi Azure [Blob Storage](connector-azure-blob-storage.md), Usługi Azure Data Lake Storage [Gen1](connector-azure-data-lake-store.md)i [Usługi Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) w formacie JSON** (typ setOfObjects). Jeśli chcesz załadować pakiet Office 365 do innych typów magazynów danych lub w innych formatach, możesz skierować pierwszą aktywność kopiowania z kolejnym działaniem kopiowania, aby dodatkowo załadować dane do dowolnego [z obsługiwanych magazynów docelowych podajnika ADF](copy-activity-overview.md#supported-data-stores-and-formats) (patrz kolumna "obsługiwane jako zlew" w tabeli "Obsługiwane magazyny i formaty danych").

>[!IMPORTANT]
>- Subskrypcja platformy Azure zawierająca fabrykę danych i magazyn danych ujścia musi znajdować się w tej samej dzierżawie usługi Azure Active Directory (Azure AD) jako dzierżawa usługi Office 365.
>- Upewnij się, że region środowiska wykonawczego integracji platformy Azure używany do wykonywania kopiowania, a także miejsce docelowe znajduje się w tym samym regionie, w którym znajduje się skrzynka pocztowa użytkowników dzierżawy usługi Office 365. Zapoznaj się [tutaj,](concepts-integration-runtime.md#integration-runtime-location) aby dowiedzieć się, jak określana jest lokalizacja podczerwenia platformy Azure. Zobacz [tabelę tutaj,](https://docs.microsoft.com/graph/data-connect-datasets#regions) aby uzyskać listę obsługiwanych regionów pakietu Office i odpowiednich regionów platformy Azure.
>- Uwierzytelnianie jednostki usługi jest jedynym mechanizmem uwierzytelniania obsługiwanym dla usługi Azure Blob Storage, Usługi Azure Data Lake Storage Gen1 i Usługi Azure Data Lake Storage Gen2 jako magazynów docelowych.

## <a name="prerequisites"></a>Wymagania wstępne

Aby skopiować dane z usługi Office 365 na platformę Azure, należy wykonać następujące kroki wymagań wstępnych:

- Administrator dzierżawy usługi Office 365 musi wykonać czynności przy wejściu na pokład, jak opisano [w tym miejscu.](https://docs.microsoft.com/graph/data-connect-get-started)
- Tworzenie i konfigurowanie aplikacji sieci web usługi Azure AD w usłudze Azure Active Directory.  Aby uzyskać instrukcje, zobacz [Tworzenie aplikacji usługi Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application).
- Zanotuj następujące wartości, których użyjesz do zdefiniowania połączonej usługi dla usługi Office 365:
    - Identyfikator dzierżawy. Aby uzyskać instrukcje, zobacz [Get Tenant ID](../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in).
    - Identyfikator aplikacji i klucz aplikacji.  Aby uzyskać instrukcje, zobacz [Pobierz identyfikator aplikacji i klucz uwierzytelniania](../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in).
- Dodaj tożsamość użytkownika, który będzie żądać dostępu do danych jako właściciel aplikacji sieci web usługi Azure AD (z aplikacji sieci web usługi Azure AD > Ustawienia > Właściciele > Dodaj właściciela). 
    - Tożsamość użytkownika musi znajdować się w organizacji usługi Office 365, od której otrzymujesz dane, i nie może być użytkownikiem-gościem.

## <a name="approving-new-data-access-requests"></a>Zatwierdzanie nowych żądań dostępu do danych

Jeśli jest to pierwszy raz, gdy żądasz danych w tym kontekście (kombinacja, do której tabeli danych jest dostęp, które konto docelowe jest ładowane do danych i która tożsamość użytkownika sprawia, że żądanie dostępu do danych), zobaczysz stan działania kopiowania jako "W toku", a dopiero po kliknięciu [łącza "Szczegóły" w obszarze Akcje](copy-activity-overview.md#monitoring) zobaczysz stan jako "RequestingConsent".  Członek grupy zatwierdzającej dostępu do danych musi zatwierdzić żądanie w zarządzania dostępem uprzywilejowanym, zanim będzie można kontynuować wyodrębnianie danych.

Zapoznaj się [tutaj,](https://docs.microsoft.com/graph/data-connect-tips#approve-pam-requests-via-office-365-admin-portal) w jaki sposób osoba zatwierdzająca może zatwierdzić żądanie dostępu do danych, i zapoznaj się [z tym poniżej,](https://docs.microsoft.com/graph/data-connect-pam) aby uzyskać wyjaśnienie dotyczące ogólnej integracji z zarządzaniem dostępem uprzywilejowanym, w tym sposobu konfigurowania grupy osób zatwierdzającej dostęp do danych.

## <a name="policy-validation"></a>Weryfikacja zasad

Jeśli usługa ADF jest tworzona jako część zarządzanej aplikacji, a przypisania zasad platformy Azure są dokonywane na zasobach w ramach grupy zasobów zarządzania, a następnie dla każdego uruchomienia działania kopiowania usługa ADF sprawdza, czy przypisania zasad są wymuszane. Lista obsługiwanych zasad znajduje się [tutaj.](https://docs.microsoft.com/graph/data-connect-policies#policies)

## <a name="getting-started"></a>Wprowadzenie

>[!TIP]
>Aby zapoznać się z instruktażem dotyczących korzystania z łącznika usługi Office 365, zobacz [Artykuł ładowania danych z usługi Office 365.](load-office-365-data.md)

Potoku z działaniem kopiowania można utworzyć za pomocą jednego z następujących narzędzi lub zestawów SDK. Wybierz łącze, aby przejść do samouczka z instrukcjami krok po kroku, aby utworzyć potok z działaniem kopiowania. 

- [Azure Portal](quickstart-create-data-factory-portal.md)
- [Zestaw SDK .NET](quickstart-create-data-factory-dot-net.md)
- [Zestaw SDK dla języka Python](quickstart-create-data-factory-python.md)
- [Azure PowerShell](quickstart-create-data-factory-powershell.md)
- [Interfejs API REST](quickstart-create-data-factory-rest-api.md)
- [Szablon usługi Azure Resource Manager](quickstart-create-data-factory-resource-manager-template.md). 

W poniższych sekcjach znajdują się szczegółowe informacje o właściwościach, które są używane do definiowania jednostek usługi Data Factory specyficznych dla łącznika usługi Office 365.

## <a name="linked-service-properties"></a>Połączone właściwości usługi

Następujące właściwości są obsługiwane dla połączonej usługi Office 365:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość typu musi być ustawiona na: **Office365** | Tak |
| office365TenantId | Identyfikator dzierżawy platformy Azure, do którego należy konto usługi Office 365. | Tak |
| servicePrincipalTenantId | Określ informacje o dzierżawie, w ramach których znajduje się aplikacja sieci web usługi Azure AD. | Tak |
| servicePrincipalId | Określ identyfikator klienta aplikacji. | Tak |
| servicePrincipalKey | Określ klucz aplikacji. Oznacz to pole jako SecureString, aby bezpiecznie przechowywać go w fabryce danych. | Tak |
| connectVia | Środowisko wykonawcze integracji, które mają być używane do łączenia się z magazynem danych.  Jeśli nie zostanie określony, używa domyślnego środowiska wykonawczego integracji platformy Azure. | Nie |

>[!NOTE]
> Różnica między **office365TenantId** i **servicePrincipalTenantId** i odpowiadającą mu wartość:
>- Jeśli jesteś deweloperem korporacyjnym opracowujesięce dla danych usługi Office 365 dla użycia własnej organizacji, należy podać ten sam identyfikator dzierżawy dla obu właściwości, który jest identyfikatorem dzierżawy usługi AAD twojej organizacji.
>- Jeśli jesteś deweloperem niezależnego dostawcy oprogramowania, który tworzy aplikację dla klientów, a następnie office365TenantId będzie identyfikatorem dzierżawy AAD klienta (instalatora aplikacji) ID dzierżawy AAD i servicePrincipalTenantId będzie identyfikatorem dzierżawy usługi AAD twojej firmy.

**Przykład:**

```json
{
    "name": "Office365LinkedService",
    "properties": {
        "type": "Office365",
        "typeProperties": {
            "office365TenantId": "<Office 365 tenant id>",
            "servicePrincipalTenantId": "<AAD app service principal tenant id>",
            "servicePrincipalId": "<AAD app service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<AAD app service principal key>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Właściwości zestawu danych

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania zestawów danych, zobacz artykuł [o zestawach danych.](concepts-datasets-linked-services.md) Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych usługi Office 365.

Aby skopiować dane z usługi Office 365, obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość typu zestawu danych musi być ustawiona na: **Office365Table** | Tak |
| tableName | Nazwa zestawu danych do wyodrębnienia z usługi Office 365. Zapoznaj się [z listą](https://docs.microsoft.com/graph/data-connect-datasets#datasets) zestawów danych usługi Office 365 dostępnych do wyodrębnienia. | Tak |

Jeśli ustawienie `dateFilterColumn`, `startTime` `endTime`, `userScopeFilterUri` i w zestawie danych, jest nadal obsługiwane w stanie, podczas gdy zaleca się użycie nowego modelu w źródle aktywności w przyszłości.

**Przykład**

```json
{
    "name": "DS_May2019_O365_Message",
    "properties": {
        "type": "Office365Table",
        "linkedServiceName": {
            "referenceName": "<Office 365 linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [],
        "typeProperties": {
            "tableName": "BasicDataSet_v0.Event_v1"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania działań, zobacz [Pipelines](concepts-pipelines-activities.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez źródło usługi Office 365.

### <a name="office-365-as-source"></a>Usługa Office 365 jako źródło

Aby skopiować dane z usługi Office 365, w sekcji **źródła** działania kopiowania są obsługiwane następujące właściwości:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość typu źródła działania kopiowania musi być ustawiona na: **Office365Source** | Tak |
| dozwolone Grupy | Predykat wyboru grupy.  Ta właściwość służy do wybierania maksymalnie 10 grup użytkowników, dla których zostaną pobrane dane.  Jeśli nie zostaną określone żadne grupy, dane zostaną zwrócone dla całej organizacji. | Nie |
| userScopeFilterUri | Gdy `allowedGroups` właściwość nie jest określona, można użyć wyrażenia predykatu, które jest stosowane w całej dzierżawie, aby filtrować określone wiersze, aby wyodrębnić z usługi Office 365. Format predykatu powinien być zgodny z formatem zapytań interfejsów API programu Microsoft Graph, np. `https://graph.microsoft.com/v1.0/users?$filter=Department eq 'Finance'` | Nie |
| dataFilterKolumna | Nazwa kolumny filtru DateTime. Ta właściwość służy do ograniczania zakresu czasu, dla którego wyodrębniane są dane usługi Office 365. | Tak, jeśli zestaw danych ma jedną lub więcej kolumn DateTime. Lista zestawów danych wymagających tego filtru DateTime można znaleźć [tutaj.](https://docs.microsoft.com/graph/data-connect-filtering#filtering) |
| startTime | Wartość DataTime rozpoczęcia do filtrowania. | Tak, `dateFilterColumn` jeśli jest określony |
| endTime | Koniec DateTime wartość do filtrowania. | Tak, `dateFilterColumn` jeśli jest określony |
| outputKolumny | Tablica kolumn do skopiowania do ujścia. | Nie |

**Przykład:**

```json
"activities": [
    {
        "name": "CopyFromO365ToBlob",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Office 365 input dataset name>",
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
                "type": "Office365Source",
                "dateFilterColumn": "CreatedDateTime",
                "startTime": "2019-04-28T16:00:00.000Z",
                "endTime": "2019-05-05T16:00:00.000Z",
                "userScopeFilterUri": "https://graph.microsoft.com/v1.0/users?$filter=Department eq 'Finance'",
                "outputColumns": [
                    {
                        "name": "Id"
                    },
                    {
                        "name": "CreatedDateTime"
                    },
                    {
                        "name": "LastModifiedDateTime"
                    },
                    {
                        "name": "ChangeKey"
                    },
                    {
                        "name": "Categories"
                    },
                    {
                        "name": "OriginalStartTimeZone"
                    },
                    {
                        "name": "OriginalEndTimeZone"
                    },
                    {
                        "name": "ResponseStatus"
                    },
                    {
                        "name": "iCalUId"
                    },
                    {
                        "name": "ReminderMinutesBeforeStart"
                    },
                    {
                        "name": "IsReminderOn"
                    },
                    {
                        "name": "HasAttachments"
                    },
                    {
                        "name": "Subject"
                    },
                    {
                        "name": "Body"
                    },
                    {
                        "name": "Importance"
                    },
                    {
                        "name": "Sensitivity"
                    },
                    {
                        "name": "Start"
                    },
                    {
                        "name": "End"
                    },
                    {
                        "name": "Location"
                    },
                    {
                        "name": "IsAllDay"
                    },
                    {
                        "name": "IsCancelled"
                    },
                    {
                        "name": "IsOrganizer"
                    },
                    {
                        "name": "Recurrence"
                    },
                    {
                        "name": "ResponseRequested"
                    },
                    {
                        "name": "ShowAs"
                    },
                    {
                        "name": "Type"
                    },
                    {
                        "name": "Attendees"
                    },
                    {
                        "name": "Organizer"
                    },
                    {
                        "name": "WebLink"
                    },
                    {
                        "name": "Attachments"
                    },
                    {
                        "name": "BodyPreview"
                    },
                    {
                        "name": "Locations"
                    },
                    {
                        "name": "OnlineMeetingUrl"
                    },
                    {
                        "name": "OriginalStart"
                    },
                    {
                        "name": "SeriesMasterId"
                    }
                ]
            },
            "sink": {
                "type": "BlobSink"
            }
        }
    }
]
```

## <a name="next-steps"></a>Następne kroki
Aby uzyskać listę magazynów danych obsługiwanych jako źródła i pochłaniacze przez działanie kopiowania w usłudze Azure Data Factory, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
