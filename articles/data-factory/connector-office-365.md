---
title: Skopiuj dane z pakietu Office 365 przy użyciu Azure Data Factory
description: Informacje o kopiowaniu danych z pakietu Office 365 do obsługiwanych magazynów danych ujścia przy użyciu działania kopiowania w potoku Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/20/2019
ms.author: jingwang
ms.openlocfilehash: a7df69e7c5701074b40d6fa8340d8a0e247f00de
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100393007"
---
# <a name="copy-data-from-office-365-into-azure-using-azure-data-factory"></a>Skopiuj dane z pakietu Office 365 do platformy Azure przy użyciu Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Data Factory integruje się z usługą [Microsoft Graph Data Connect](/graph/data-connect-concept-overview), umożliwiając Ci wprowadzanie rozbudowanych danych organizacji w dzierżawie pakietu Office 365 do platformy Azure w sposób skalowalny i kompilowanie aplikacji analitycznych oraz wyodrębnianie szczegółowych informacji na podstawie tych cennych zasobów danych. Integracja z usługą Privileged Access Management zapewnia bezpieczną kontrolę dostępu dla cennych danych w pakiecie Office 365.  Skorzystaj z [tego linku](/graph/data-connect-concept-overview) , aby zapoznać się z omówieniem Microsoft Graph Data Connect i zapoznaj się z [tym linkiem](/graph/data-connect-policies#licensing) w celu uzyskania informacji dotyczących licencjonowania.

W tym artykule opisano sposób używania działania kopiowania w Azure Data Factory do kopiowania danych z pakietu Office 365. Jest ona oparta na [przeglądzie działania kopiowania](copy-activity-overview.md) , która przedstawia ogólne omówienie działania kopiowania.

## <a name="supported-capabilities"></a>Obsługiwane funkcje
Funkcja połączenia ADF z pakietem Office 365 łącznika i Microsoft Graph danych umożliwiają rozzyskanie różnych typów zestawów danych z skrzynek pocztowych z obsługą poczty E-mail programu Exchange, w tym kontaktów książki adresowej, zdarzeń kalendarza, wiadomości e-mail, informacji o użytkowniku, ustawień skrzynek pocztowych itd.  Zapoznaj się [tutaj](/graph/data-connect-datasets) , aby wyświetlić pełną listę dostępnych zestawów danych.

Na razie w ramach jednego działania kopiowania można **kopiować dane z pakietu Office 365 do [platformy Azure Blob Storage](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md)i [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) w formacie JSON** (typ setOfObjects). Jeśli chcesz załadować pakiet Office 365 do innych typów magazynów danych lub w innych formatach, można utworzyć łańcuch pierwszego działania kopiowania z kolejnym działaniem kopiowania, aby dodatkowo załadować dane do dowolnego z [obsługiwanych magazynów docelowych ADF](copy-activity-overview.md#supported-data-stores-and-formats) (w tabeli "obsługiwane magazyny danych i formaty").

>[!IMPORTANT]
>- Subskrypcja platformy Azure zawierająca fabrykę danych i magazyn danych ujścia musi znajdować się w tej samej dzierżawie usługi Azure Active Directory (Azure AD) jako dzierżawy pakietu Office 365.
>- Upewnij się, że region Azure Integration Runtime używany do działania kopiowania oraz miejsce docelowe znajdują się w tym samym regionie, w którym znajduje się Skrzynka pocztowa użytkowników dzierżawy pakietu Office 365. Zapoznaj się z [tym](concepts-integration-runtime.md#integration-runtime-location) artykułem, aby zrozumieć, jak jest określana lokalizacja Azure IR. Zapoznaj się z [tabelą tutaj](/graph/data-connect-datasets#regions) , aby zapoznać się z listą obsługiwanych regionów biura i odpowiednich regionów świadczenia usługi Azure.
>- Uwierzytelnianie jednostki usługi jest jedynym mechanizmem uwierzytelniania obsługiwanym przez usługę Azure Blob Storage, Azure Data Lake Storage Gen1 i Azure Data Lake Storage Gen2 jako magazyny docelowe.

## <a name="prerequisites"></a>Wymagania wstępne

Aby skopiować dane z pakietu Office 365 do platformy Azure, należy wykonać następujące czynności wstępne:

- Administrator dzierżawy pakietu Office 365 musi wykonać czynności związane z dołączaniem, zgodnie z opisem w [tym miejscu](/graph/data-connect-get-started).
- Utwórz i skonfiguruj aplikację sieci Web usługi Azure AD w Azure Active Directory.  Aby uzyskać instrukcje, zobacz [Tworzenie aplikacji usługi Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal).
- Należy pamiętać o następujących wartościach, które będą używane do definiowania połączonej usługi dla pakietu Office 365:
    - Identyfikator dzierżawy. Aby uzyskać instrukcje, zobacz [Pobieranie identyfikatora dzierżawy](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in).
    - Identyfikator aplikacji i klucz aplikacji.  Aby uzyskać instrukcje, zobacz [Pobieranie identyfikatora aplikacji i klucza uwierzytelniania](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in).
- Dodaj tożsamość użytkownika, która będzie wprowadzać żądanie dostępu do danych jako właściciel aplikacji sieci Web usługi Azure AD (z poziomu > aplikacji sieci Web usługi Azure AD > właściciele > Dodaj właściciela). 
    - Tożsamość użytkownika musi znajdować się w organizacji pakietu Office 365, z której są odbierane dane, i nie może być użytkownikiem-gościem.

## <a name="approving-new-data-access-requests"></a>Zatwierdzanie nowych żądań dostępu do danych

Jeśli żądasz danych dla tego kontekstu po raz pierwszy (połączenie z tabelą danych uzyskuje dostęp, na którym koncie docelowym są ładowane dane, a tożsamość użytkownika, która wprowadza żądanie dostępu do danych, zobaczysz stan działania kopiowania jako "w toku" i tylko po kliknięciu [linku "Szczegóły" w obszarze Akcje](copy-activity-overview.md#monitoring) zobaczysz stan jako "RequestingConsent".  Członek grupy osoby zatwierdzającej dostęp do danych musi zatwierdzić żądanie w Privileged Access Management zanim będzie możliwe kontynuowanie wyodrębniania danych.

Zapoznaj się z [tym tutaj](/graph/data-connect-tips#approve-pam-requests-via-office-365-admin-portal) , jak osoba zatwierdzająca może zatwierdzić żądanie dostępu do danych, i zapoznaj się z [tym](/graph/data-connect-pam) opisem, aby uzyskać wyjaśnienie ogólnej integracji z Privileged Access Management, w tym informacje dotyczące konfigurowania grupy osób zatwierdzających dostęp do danych.

## <a name="policy-validation"></a>Weryfikacja zasad

Jeśli w ramach aplikacji zarządzanej jest tworzony automatyczny ADF i przypisano zasady platformy Azure do zasobów w grupie zasobów zarządzania, w przypadku każdego uruchomienia działania kopiowania zostanie wykonane sprawdzenie, czy są wymuszane przydziały zasad. Zapoznaj [się](/graph/data-connect-policies#policies) z listą obsługiwanych zasad.

## <a name="getting-started"></a>Wprowadzenie

>[!TIP]
>Aby zapoznać się z przewodnikiem dotyczącym korzystania z łącznika pakietu Office 365, zobacz artykuł [ładowanie danych z pakietu office 365](load-office-365-data.md) .

Można utworzyć potok z działaniem kopiowania przy użyciu jednego z następujących narzędzi lub zestawów SDK. Wybierz link, aby przejść do samouczka z instrukcjami krok po kroku, aby utworzyć potok z działaniem kopiowania. 

- [Witryna Azure Portal](quickstart-create-data-factory-portal.md)
- [Zestaw SDK platformy .NET](quickstart-create-data-factory-dot-net.md)
- [Zestaw SDK dla języka Python](quickstart-create-data-factory-python.md)
- [Azure PowerShell](quickstart-create-data-factory-powershell.md)
- [Interfejs API REST](quickstart-create-data-factory-rest-api.md)
- [Szablon Azure Resource Manager](quickstart-create-data-factory-resource-manager-template.md). 

Poniższe sekcje zawierają szczegółowe informacje o właściwościach, które są używane do definiowania jednostek Data Factory specyficznych dla łącznika pakietu Office 365.

## <a name="linked-service-properties"></a>Właściwości połączonej usługi

Dla połączonej usługi Office 365 są obsługiwane następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość Type musi być ustawiona na wartość: **Office 365** | Tak |
| office365TenantId | Identyfikator dzierżawy platformy Azure, do której należy konto usługi Office 365. | Tak |
| servicePrincipalTenantId | Określ informacje o dzierżawie, w których znajduje się aplikacja sieci Web usługi Azure AD. | Tak |
| servicePrincipalId | Określ identyfikator klienta aplikacji. | Tak |
| servicePrincipalKey | Określ klucz aplikacji. Oznacz to pole jako element SecureString, aby bezpiecznie przechowywać go w Data Factory. | Tak |
| Właściwością connectvia | Integration Runtime używany do nawiązywania połączenia z magazynem danych.  Jeśli nie zostanie określony, zostanie użyta domyślna Azure Integration Runtime. | Nie |

>[!NOTE]
> Różnica między **office365TenantId** i **servicePrincipalTenantId** i odpowiadającą jej wartością, która zapewnia:
>- Jeśli jesteś deweloperem przedsiębiorstwa, który opracowuje aplikację na podstawie danych z pakietu Office 365 na potrzeby użycia w organizacji, należy podać ten sam identyfikator dzierżawy dla obu właściwości, który jest IDENTYFIKATORem dzierżawy usługi AAD w organizacji.
>- Jeśli jesteś deweloperem niezależnego dostawcy oprogramowania, opracowując aplikację dla klientów, office365TenantId będzie identyfikator dzierżawy usługi AAD klienta (Instalator aplikacji), a servicePrincipalTenantId będzie IDENTYFIKATORem dzierżawy usługi AAD firmy.

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

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania zestawów danych, zobacz artykuł [zestawy danych](concepts-datasets-linked-services.md) . Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych pakietu Office 365.

Aby skopiować dane z pakietu Office 365, obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość Type zestawu danych musi być ustawiona na wartość: **Office365Table** | Tak |
| tableName | Nazwa zestawu danych do wyodrębnienia z pakietu Office 365. Zapoznaj [się](/graph/data-connect-datasets#datasets) z listą zestawów danych pakietu Office 365 dostępnych do wyodrębnienia. | Tak |

Jeśli ustawienia `dateFilterColumn` , `startTime` , `endTime` , i `userScopeFilterUri` w zestawie danych, nadal są obsługiwane jako-is, podczas gdy sugerowane jest użycie nowego modelu w źródle aktywności.

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

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania działań, zobacz artykuł [potoki](concepts-pipelines-activities.md) . Ta sekcja zawiera listę właściwości obsługiwanych przez źródło pakietu Office 365.

### <a name="office-365-as-source"></a>Pakiet Office 365 jako źródło

Aby skopiować dane z pakietu Office 365, w sekcji **Źródło** działania kopiowania są obsługiwane następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość Type źródła działania Copy musi być ustawiona na wartość: **Office365Source** | Tak |
| allowedGroups | Predykat wyboru grupy.  Ta właściwość umożliwia wybranie do 10 grup użytkowników, dla których zostaną pobrane dane.  Jeśli nie określono żadnych grup, dane będą zwracane dla całej organizacji. | Nie |
| userScopeFilterUri | Jeśli `allowedGroups` Właściwość nie jest określona, można użyć wyrażenia predykatu, które jest stosowane dla całej dzierżawy do filtrowania konkretnych wierszy do wyodrębnienia z pakietu Office 365. Format predykatu powinien być zgodny z formatem zapytania Microsoft Graph interfejsów API, np. `https://graph.microsoft.com/v1.0/users?$filter=Department eq 'Finance'` . | Nie |
| dateFilterColumn | Nazwa kolumny filtru DateTime. Ta właściwość umożliwia ograniczenie zakresu czasu, dla którego są wyodrębniane dane pakietu Office 365. | Tak, jeśli zestaw danych ma jedną lub więcej kolumn DateTime. Zapoznaj się [tutaj](/graph/data-connect-filtering#filtering) z listą zestawów danych, które wymagają tego filtru DateTime. |
| startTime | Rozpocznij wartość daty/godziny do odfiltrowania. | Tak, jeśli `dateFilterColumn` jest określony |
| endTime | Końcowa wartość daty/godziny do odfiltrowania. | Tak, jeśli `dateFilterColumn` jest określony |
| outputColumns | Tablica kolumn do skopiowania do ujścia. | Nie |

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
Listę magazynów danych obsługiwanych jako źródła i ujścia przez działanie kopiowania w Azure Data Factory można znaleźć w temacie [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).