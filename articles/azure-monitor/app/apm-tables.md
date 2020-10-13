---
title: Azure Monitor Application Insights schemat zasobów oparty na obszarze roboczym
description: Zapoznaj się z nową strukturą tabeli i schematem Azure Monitor Application Insights zasobów obszaru roboczego.
ms.topic: conceptual
ms.date: 05/09/2020
ms.openlocfilehash: 8f0bee64d74cfd5b6abef5c918c023974fda3fcf
ms.sourcegitcommit: 50802bffd56155f3b01bfb4ed009b70045131750
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91931057"
---
# <a name="workspace-based-resource-changes-preview"></a>Zmiany zasobów na podstawie obszaru roboczego (wersja zapoznawcza)

Przed wprowadzeniem [zasobów Application Insights opartych na obszarze roboczym](create-workspace-resource.md)Application Insights dane były przechowywane oddzielnie od innych danych dziennika w Azure monitor. Oba są oparte na platformie Azure Eksplorator danych i używają tego samego języka zapytań Kusto (KQL). Opisano to w [dziennikach w Azure monitor](../platform/data-platform-logs.md).

Za pomocą Application Insights danych zasobów opartych na obszarze roboczym są przechowywane w Log Analytics obszarze roboczym z innymi danymi monitorowania i danymi aplikacji. Upraszcza to konfigurację, co pozwala łatwiej analizować dane w wielu rozwiązaniach i korzystać z możliwości obszarów roboczych.

## <a name="table-structure"></a>Struktura tabeli

| Nazwa starszej tabeli | Nazwa nowej tabeli | Opis |
|:---|:---|:---|
| availabilityResults | AppAvailabilityResults |  Dane podsumowujące z testów dostępności.|
| browserTimings | AppBrowserTimings | Dane dotyczące wydajności klienta, takie jak czas przetwarzania danych przychodzących.|
| zależności | AppDependencies | Wywołania z aplikacji do innych składników (w tym składników zewnętrznych) zarejestrowanych przez TrackDependency () — na przykład wywołania interfejsu API REST, bazy danych lub systemu plików.  |
| customEvents | AppEvents | Zdarzenia niestandardowe utworzone przez aplikację. |
| customMetrics | AppMetrics | Metryki niestandardowe utworzone przez aplikację. |
| pageViews | AppPageViews| Dane o każdym widoku witryny sieci Web z informacjami o przeglądarce. |
| Liczniki wydajności | AppPerformanceCounters | Pomiary wydajności z zasobów obliczeniowych obsługujących aplikację, na przykład liczniki wydajności systemu Windows. |
| żądań | AppRequests | Żądania odebrane przez aplikację. Na przykład oddzielny rekord żądania jest rejestrowany dla każdego żądania HTTP, które otrzymuje aplikacja sieci Web.  |
| wyłączenia | AppSystemEvents | Wyjątki zgłoszone przez środowisko uruchomieniowe aplikacji, przechwytuje zarówno wyjątki po stronie serwera, jak i klienta (przeglądarki). |
| ścieżki | AppTraces | Szczegółowe dzienniki (ślady) emitowane za pośrednictwem kodu aplikacji/platform rejestrowania zarejestrowanych za pośrednictwem TrackTrace (). |

## <a name="table-schemas"></a>Schematy tabel

Poniższe sekcje zawierają mapowanie między klasycznymi nazwami właściwości i nowymi nazwami właściwości Application Insights opartych na obszarze roboczym.  Te informacje służą do konwertowania wszelkich zapytań przy użyciu starszych tabel.

Większość kolumn ma taką samą nazwę co różne wielkości liter. Ponieważ KQL jest uwzględniana wielkość liter, należy zmienić nazwę każdej kolumny wraz z nazwami tabel w istniejących zapytaniach. Kolumny z zmianami oprócz wielkich liter są wyróżnione. Można nadal używać klasycznych zapytań Application Insights w okienku **dzienniki** zasobu Application Insights, nawet jeśli jest to zasób oparty na obszarze roboczym. Nowe nazwy właściwości są wymagane do wykonywania zapytań w kontekście środowiska roboczego Log Analytics.

### <a name="appavailabilityresults"></a>AppAvailabilityResults

Starsza tabela: dostępność

|ApplicationInsights|Type|LogAnalytics|Type|
|:---|:---|:---|:---|
|appId|ciąg|\_ResourceGUID|ciąg|
|application_Version|ciąg|AppVersion|ciąg|
|Argumentu|ciąg|\_ResourceId|ciąg|
|client_Browser|ciąg|ClientBrowser|ciąg|
|client_City|ciąg|ClientCity|ciąg|
|client_CountryOrRegion|ciąg|ClientCountryOrRegion|ciąg|
|client_IP|ciąg|ClientIP|ciąg|
|client_Model|ciąg|ClientModel|ciąg|
|client_OS|ciąg|ClientOS|ciąg|
|client_StateOrProvince|ciąg|ClientStateOrProvince|ciąg|
|client_Type|ciąg|ClientType|ciąg|
|cloud_RoleInstance|ciąg|AppRoleInstance|ciąg|
|cloud_RoleName|ciąg|AppRoleName|ciąg|
|customDimensions|dynamiczna|Właściwości|Dynamiczny|
|customMeasurements|dynamiczna|Miary|Dynamiczny|
|czas trwania|liczba rzeczywista|DurationMs|liczba rzeczywista|
|`id`|ciąg|`Id`|ciąg|
|iKey|ciąg|IKey|ciąg|
|itemCount|int|ItemCount|int|
|Elementów|ciąg|\_Elementów|ciąg|
|itemType|ciąg|Type|Ciąg|
|location|ciąg|Lokalizacja|ciąg|
|message|ciąg|Wiadomość|ciąg|
|name|ciąg|Nazwa|ciąg|
|operation_Id|ciąg|OperationId|ciąg|
|operation_Name|ciąg|OperationName|ciąg|
|operation_ParentId|ciąg|OperationParentId|ciąg|
|operation_SyntheticSource|ciąg|OperationSyntheticSource|ciąg|
|performanceBucket|ciąg|PerformanceBucket|ciąg|
|sdkVersion|ciąg|SdkVersion|ciąg|
|session_Id|ciąg|SessionId|ciąg|
|size|liczba rzeczywista|Rozmiar|liczba rzeczywista|
|powodzenie|ciąg|Powodzenie|Wartość logiczna|
|sygnatura czasowa|datetime|TimeGenerated|datetime|
|user_AccountId|ciąg|UserAccountId|ciąg|
|user_AuthenticatedId|ciąg|UserAuthenticatedId|ciąg|
|user_Id|ciąg|UserId|ciąg|

### <a name="appbrowsertimings"></a>AppBrowserTimings

Starsza tabela: browserTimings

|ApplicationInsights|Type|LogAnalytics|Type|
|:---|:---|:---|:---|
|appId|ciąg|\_ResourceGUID|ciąg|
|application_Version|ciąg|AppVersion|ciąg|
|Argumentu|ciąg|\_ResourceId|ciąg|
|client_Browser|ciąg|ClientBrowser|ciąg|
|client_City|ciąg|ClientCity|ciąg|
|client_CountryOrRegion|ciąg|ClientCountryOrRegion|ciąg|
|client_IP|ciąg|ClientIP|ciąg|
|client_Model|ciąg|ClientModel|ciąg|
|client_OS|ciąg|ClientOS|ciąg|
|client_StateOrProvince|ciąg|ClientStateOrProvince|ciąg|
|client_Type|ciąg|ClientType|ciąg|
|cloud_RoleInstance|ciąg|AppRoleInstance|ciąg|
|cloud_RoleName|ciąg|AppRoleName|ciąg|
|customDimensions|dynamiczna|Właściwości|Dynamiczny|
|customMeasurements|dynamiczna|Miary|Dynamiczny|
|iKey|ciąg|IKey|ciąg|
|itemCount|int|ItemCount|int|
|Elementów|ciąg|\_Elementów|ciąg|
|itemType|ciąg|Typ|ciąg|
|name|ciąg|Nazwa|datetime|
|networkDuration|liczba rzeczywista|NetworkDurationMs|liczba rzeczywista|
|operation_Id|ciąg|OperationId|ciąg|
|operation_Name|ciąg|OperationName|ciąg|
|operation_ParentId|ciąg|OperationParentId|ciąg|
|operation_SyntheticSource|ciąg|OperationSyntheticSource|ciąg|
|performanceBucket|ciąg|PerformanceBucket|ciąg|
|processingDuration|liczba rzeczywista|ProcessingDurationMs|liczba rzeczywista|
|receiveDuration|liczba rzeczywista|ReceiveDurationMs|liczba rzeczywista|
|sdkVersion|ciąg|SdkVersion|ciąg|
|sendDuration|liczba rzeczywista|SendDurationMs|liczba rzeczywista|
|session_Id|ciąg|SessionId|ciąg|
|sygnatura czasowa|datetime|TimeGenerated|datetime|
|totalDuration|liczba rzeczywista|TotalDurationMs|liczba rzeczywista|
|url|ciąg|Url|ciąg|
|user_AccountId|ciąg|UserAccountId|ciąg|
|user_AuthenticatedId|ciąg|UserAuthenticatedId|ciąg|
|user_Id|ciąg|UserId|ciąg|

### <a name="appdependencies"></a>AppDependencies

Starsza tabela: zależności

|ApplicationInsights|Type|LogAnalytics|Type|
|:---|:---|:---|:---|
|appId|ciąg|\_ResourceGUID|ciąg|
|application_Version|ciąg|AppVersion|ciąg|
|Argumentu|ciąg|\_ResourceId|ciąg|
|client_Browser|ciąg|ClientBrowser|ciąg|
|client_City|ciąg|ClientCity|ciąg|
|client_CountryOrRegion|ciąg|ClientCountryOrRegion|ciąg|
|client_IP|ciąg|ClientIP|ciąg|
|client_Model|ciąg|ClientModel|ciąg|
|client_OS|ciąg|ClientOS|ciąg|
|client_StateOrProvince|ciąg|ClientStateOrProvince|ciąg|
|client_Type|ciąg|ClientType|ciąg|
|cloud_RoleInstance|ciąg|AppRoleInstance|ciąg|
|cloud_RoleName|ciąg|AppRoleName|ciąg|
|customDimensions|dynamiczna|Właściwości|Dynamiczny|
|customMeasurements|dynamiczna|Miary|Dynamiczny|
|dane|ciąg|Dane|ciąg|
|czas trwania|liczba rzeczywista|DurationMs|liczba rzeczywista|
|`id`|ciąg|`Id`|ciąg|
|iKey|ciąg|IKey|ciąg|
|itemCount|int|ItemCount|int|
|Elementów|ciąg|\_Elementów|ciąg|
|itemType|ciąg|Type|Ciąg|
|name|ciąg|Nazwa|ciąg|
|operation_Id|ciąg|OperationId|ciąg|
|operation_Name|ciąg|OperationName|ciąg|
|operation_ParentId|ciąg|OperationParentId|ciąg|
|operation_SyntheticSource|ciąg|OperationSyntheticSource|ciąg|
|performanceBucket|ciąg|PerformanceBucket|ciąg|
|resultCode|ciąg|ResultCode|ciąg|
|sdkVersion|ciąg|SdkVersion|ciąg|
|session_Id|ciąg|SessionId|ciąg|
|powodzenie|ciąg|Powodzenie|Wartość logiczna|
|obiektów|ciąg|Cel|ciąg|
|sygnatura czasowa|datetime|TimeGenerated|datetime|
|typ|ciąg|DependencyType|ciąg|
|user_AccountId|ciąg|UserAccountId|ciąg|
|user_AuthenticatedId|ciąg|UserAuthenticatedId|ciąg|
|user_Id|ciąg|UserId|ciąg|

### <a name="appevents"></a>AppEvents

Starsza tabela: customEvents

|ApplicationInsights|Type|LogAnalytics|Type|
|:---|:---|:---|:---|
|appId|ciąg|\_ResourceGUID|ciąg|
|application_Version|ciąg|AppVersion|ciąg|
|Argumentu|ciąg|\_ResourceId|ciąg|
|client_Browser|ciąg|ClientBrowser|ciąg|
|client_City|ciąg|ClientCity|ciąg|
|client_CountryOrRegion|ciąg|ClientCountryOrRegion|ciąg|
|client_IP|ciąg|ClientIP|ciąg|
|client_Model|ciąg|ClientModel|ciąg|
|client_OS|ciąg|ClientOS|ciąg|
|client_StateOrProvince|ciąg|ClientStateOrProvince|ciąg|
|client_Type|ciąg|ClientType|ciąg|
|cloud_RoleInstance|ciąg|AppRoleInstance|ciąg|
|cloud_RoleName|ciąg|AppRoleName|ciąg|
|customDimensions|dynamiczna|Właściwości|Dynamiczny|
|customMeasurements|dynamiczna|Miary|Dynamiczny|
|iKey|ciąg|IKey|ciąg|
|itemCount|int|ItemCount|int|
|Elementów|ciąg|\_Elementów|ciąg|
|itemType|ciąg|Typ|ciąg|
|name|ciąg|Nazwa|ciąg|
|operation_Id|ciąg|OperationId|ciąg|
|operation_Name|ciąg|OperationName|ciąg|
|operation_ParentId|ciąg|OperationParentId|ciąg|
|operation_SyntheticSource|ciąg|OperationSyntheticSource|ciąg|
|sdkVersion|ciąg|SdkVersion|ciąg|
|session_Id|ciąg|SessionId|ciąg|
|sygnatura czasowa|datetime|TimeGenerated|datetime|
|user_AccountId|ciąg|UserAccountId|ciąg|
|user_AuthenticatedId|ciąg|UserAuthenticatedId|ciąg|
|user_Id|ciąg|UserId|ciąg|

### <a name="appmetrics"></a>AppMetrics

Starsza tabela: customMetrics

|ApplicationInsights|Type|LogAnalytics|Type|
|:---|:---|:---|:---|
|appId|ciąg|\_ResourceGUID|ciąg|
|application_Version|ciąg|AppVersion|ciąg|
|Argumentu|ciąg|\_ResourceId|ciąg|
|client_Browser|ciąg|ClientBrowser|ciąg|
|client_City|ciąg|ClientCity|ciąg|
|client_CountryOrRegion|ciąg|ClientCountryOrRegion|ciąg|
|client_IP|ciąg|ClientIP|ciąg|
|client_Model|ciąg|ClientModel|ciąg|
|client_OS|ciąg|ClientOS|ciąg|
|client_StateOrProvince|ciąg|ClientStateOrProvince|ciąg|
|client_Type|ciąg|ClientType|ciąg|
|cloud_RoleInstance|ciąg|AppRoleInstance|ciąg|
|cloud_RoleName|ciąg|AppRoleName|ciąg|
|customDimensions|dynamiczna|Właściwości|Dynamiczny|
|iKey|ciąg|IKey|ciąg|
|Elementów|ciąg|\_Elementów|ciąg|
|itemType|ciąg|Typ|ciąg|
|name|ciąg|Nazwa|ciąg|
|operation_Id|ciąg|OperationId|ciąg|
|operation_Name|ciąg|OperationName|ciąg|
|operation_ParentId|ciąg|OperationParentId|ciąg|
|operation_SyntheticSource|ciąg|OperationSyntheticSource|ciąg|
|sdkVersion|ciąg|SdkVersion|ciąg|
|session_Id|ciąg|SessionId|ciąg|
|sygnatura czasowa|datetime|TimeGenerated|datetime|
|user_AccountId|ciąg|UserAccountId|ciąg|
|user_AuthenticatedId|ciąg|UserAuthenticatedId|ciąg|
|user_Id|ciąg|UserId|ciąg|
|value|liczba rzeczywista|usunięte||
|valueCount|int|ValueCount|int|
|valueMax|liczba rzeczywista|ValueMax|liczba rzeczywista|
|valueMin|liczba rzeczywista|ValueMin|liczba rzeczywista|
|valueStdDev|liczba rzeczywista|ValueStdDev|liczba rzeczywista|
|valueSum|liczba rzeczywista|ValueSum|liczba rzeczywista|

### <a name="apppageviews"></a>AppPageViews

Starsza tabela: pageViews

|ApplicationInsights|Type|LogAnalytics|Type|
|:---|:---|:---|:---|
|appId|ciąg|\_ResourceGUID|ciąg|
|application_Version|ciąg|AppVersion|ciąg|
|Argumentu|ciąg|\_ResourceId|ciąg|
|client_Browser|ciąg|ClientBrowser|ciąg|
|client_City|ciąg|ClientCity|ciąg|
|client_CountryOrRegion|ciąg|ClientCountryOrRegion|ciąg|
|client_IP|ciąg|ClientIP|ciąg|
|client_Model|ciąg|ClientModel|ciąg|
|client_OS|ciąg|ClientOS|ciąg|
|client_StateOrProvince|ciąg|ClientStateOrProvince|ciąg|
|client_Type|ciąg|ClientType|ciąg|
|cloud_RoleInstance|ciąg|AppRoleInstance|ciąg|
|cloud_RoleName|ciąg|AppRoleName|ciąg|
|customDimensions|dynamiczna|Właściwości|Dynamiczny|
|customMeasurements|dynamiczna|Miary|Dynamiczny|
|czas trwania|liczba rzeczywista|DurationMs|liczba rzeczywista|
|`id`|ciąg|`Id`|ciąg|
|iKey|ciąg|IKey|ciąg|
|itemCount|int|ItemCount|int|
|Elementów|ciąg|\_Elementów|ciąg|
|itemType|ciąg|Type|Ciąg|
|name|ciąg|Nazwa|ciąg|
|operation_Id|ciąg|OperationId|ciąg|
|operation_Name|ciąg|OperationName|ciąg|
|operation_ParentId|ciąg|OperationParentId|ciąg|
|operation_SyntheticSource|ciąg|OperationSyntheticSource|ciąg|
|performanceBucket|ciąg|PerformanceBucket|ciąg|
|sdkVersion|ciąg|SdkVersion|ciąg|
|session_Id|ciąg|SessionId|ciąg|
|sygnatura czasowa|datetime|TimeGenerated|datetime|
|url|ciąg|Url|ciąg|
|user_AccountId|ciąg|UserAccountId|ciąg|
|user_AuthenticatedId|ciąg|UserAuthenticatedId|ciąg|
|user_Id|ciąg|UserId|ciąg|

### <a name="appperformancecounters"></a>AppPerformanceCounters

Starsza tabela: Liczniki wydajności

|ApplicationInsights|Type|LogAnalytics|Type|
|:---|:---|:---|:---|
|appId|ciąg|\_ResourceGUID|ciąg|
|application_Version|ciąg|AppVersion|ciąg|
|Argumentu|ciąg|\_ResourceId|ciąg|
|category|ciąg|Kategoria|ciąg|
|client_Browser|ciąg|ClientBrowser|ciąg|
|client_City|ciąg|ClientCity|ciąg|
|client_CountryOrRegion|ciąg|ClientCountryOrRegion|ciąg|
|client_IP|ciąg|ClientIP|ciąg|
|client_Model|ciąg|ClientModel|ciąg|
|client_OS|ciąg|ClientOS|ciąg|
|client_StateOrProvince|ciąg|ClientStateOrProvince|ciąg|
|client_Type|ciąg|ClientType|ciąg|
|cloud_RoleInstance|ciąg|AppRoleInstance|ciąg|
|cloud_RoleName|ciąg|AppRoleName|ciąg|
|counter|ciąg|usunięte||
|customDimensions|dynamiczna|Właściwości|Dynamiczny|
|iKey|ciąg|IKey|ciąg|
|np|ciąg|Wystąpienie|ciąg|
|Elementów|ciąg|\_Elementów|ciąg|
|itemType|ciąg|Typ|ciąg|
|name|ciąg|Nazwa|ciąg|
|operation_Id|ciąg|OperationId|ciąg|
|operation_Name|ciąg|OperationName|ciąg|
|operation_ParentId|ciąg|OperationParentId|ciąg|
|operation_SyntheticSource|ciąg|OperationSyntheticSource|ciąg|
|sdkVersion|ciąg|SdkVersion|ciąg|
|session_Id|ciąg|SessionId|ciąg|
|sygnatura czasowa|datetime|TimeGenerated|datetime|
|user_AccountId|ciąg|UserAccountId|ciąg|
|user_AuthenticatedId|ciąg|UserAuthenticatedId|ciąg|
|user_Id|ciąg|UserId|ciąg|
|value|liczba rzeczywista|Wartość|liczba rzeczywista|

### <a name="apprequests"></a>AppRequests

Starsza tabela: żądania

|ApplicationInsights|Type|LogAnalytics|Type|
|:---|:---|:---|:---|
|appId|ciąg|\_ResourceGUID|ciąg|
|application_Version|ciąg|AppVersion|ciąg|
|Argumentu|ciąg|\_ResourceId|ciąg|
|client_Browser|ciąg|ClientBrowser|ciąg|
|client_City|ciąg|ClientCity|ciąg|
|client_CountryOrRegion|ciąg|ClientCountryOrRegion|ciąg|
|client_IP|ciąg|ClientIP|ciąg|
|client_Model|ciąg|ClientModel|ciąg|
|client_OS|ciąg|ClientOS|ciąg|
|client_StateOrProvince|ciąg|ClientStateOrProvince|ciąg|
|client_Type|ciąg|ClientType|ciąg|
|cloud_RoleInstance|ciąg|AppRoleInstance|ciąg|
|cloud_RoleName|ciąg|AppRoleName|ciąg|
|customDimensions|dynamiczna|Właściwości|Dynamiczny|
|customMeasurements|dynamiczna|Miary|Dynamiczny|
|czas trwania|liczba rzeczywista|DurationMs|Rzeczywiste|
|`id`|ciąg|`Id`|Ciąg|
|iKey|ciąg|IKey|ciąg|
|itemCount|int|ItemCount|int|
|Elementów|ciąg|\_Elementów|ciąg|
|itemType|ciąg|Type|Ciąg|
|name|ciąg|Nazwa|Ciąg|
|operation_Id|ciąg|OperationId|ciąg|
|operation_Name|ciąg|OperationName|ciąg|
|operation_ParentId|ciąg|OperationParentId|ciąg|
|operation_SyntheticSource|ciąg|OperationSyntheticSource|ciąg|
|performanceBucket|ciąg|PerformanceBucket|Ciąg|
|resultCode|ciąg|ResultCode|Ciąg|
|sdkVersion|ciąg|SdkVersion|ciąg|
|session_Id|ciąg|SessionId|ciąg|
|source|string|Element źródłowy|Ciąg|
|powodzenie|ciąg|Powodzenie|Wartość logiczna|
|sygnatura czasowa|datetime|TimeGenerated|datetime|
|url|ciąg|Url|Ciąg|
|user_AccountId|ciąg|UserAccountId|ciąg|
|user_AuthenticatedId|ciąg|UserAuthenticatedId|ciąg|
|user_Id|ciąg|UserId|ciąg|

### <a name="appsystemevents"></a>AppSystemEvents

Starsza tabela: wyjątki

|ApplicationInsights|Type|LogAnalytics|Type|
|:---|:---|:---|:---|
|appId|ciąg|\_ResourceGUID|ciąg|
|application_Version|ciąg|AppVersion|ciąg|
|Argumentu|ciąg|\_ResourceId|ciąg|
|zestaw|ciąg|Zestaw|ciąg|
|client_Browser|ciąg|ClientBrowser|ciąg|
|client_City|ciąg|ClientCity|ciąg|
|client_CountryOrRegion|ciąg|ClientCountryOrRegion|ciąg|
|client_IP|ciąg|ClientIP|ciąg|
|client_Model|ciąg|ClientModel|ciąg|
|client_OS|ciąg|ClientOS|ciąg|
|client_StateOrProvince|ciąg|ClientStateOrProvince|ciąg|
|client_Type|ciąg|ClientType|ciąg|
|cloud_RoleInstance|ciąg|AppRoleInstance|ciąg|
|cloud_RoleName|ciąg|AppRoleName|ciąg|
|customDimensions|dynamiczna|Właściwości|dynamiczna|
|customMeasurements|dynamiczna|Miary|dynamiczna|
|uzyskać|dynamiczna|Szczegóły|dynamiczna|
|handledAt|ciąg|HandledAt|ciąg|
|iKey|ciąg|IKey|ciąg|
|innermostAssembly|ciąg|InnermostAssembly|ciąg|
|innermostMessage|ciąg|InnermostMessage|ciąg|
|innermostMethod|ciąg|InnermostMethod|ciąg|
|wewnętrznatype|ciąg|Wewnętrznatype|ciąg|
|itemCount|int|ItemCount|int|
|Elementów|ciąg|\_Elementów|ciąg|
|itemType|ciąg|Typ|ciąg|
|message|ciąg|Wiadomość|ciąg|
|method|ciąg|Metoda|ciąg|
|operation_Id|ciąg|OperationId|ciąg|
|operation_Name|ciąg|OperationName|ciąg|
|operation_ParentId|ciąg|OperationParentId|ciąg|
|operation_SyntheticSource|ciąg|OperationSyntheticSource|ciąg|
|outerAssembly|ciąg|OuterAssembly|ciąg|
|outerMessage|ciąg|OuterMessage|ciąg|
|outerMethod|ciąg|OuterMethod|ciąg|
|outertype|ciąg|Outertype|ciąg|
|problemId|ciąg|ProblemId|ciąg|
|sdkVersion|ciąg|SdkVersion|ciąg|
|session_Id|ciąg|SessionId|ciąg|
|severityLevel|int|SeverityLevel|int|
|sygnatura czasowa|datetime|TimeGenerated|datetime|
|typ|ciąg|Typ|ciąg|
|user_AccountId|ciąg|UserAccountId|ciąg|
|user_AuthenticatedId|ciąg|UserAuthenticatedId|ciąg|
|user_Id|ciąg|UserId|ciąg|

### <a name="apptraces"></a>AppTraces

Starsza tabela: ślady

|ApplicationInsights|Type|LogAnalytics|Type|
|:---|:---|:---|:---|
|appId|ciąg|\_ResourceGUID|ciąg|
|application_Version|ciąg|AppVersion|ciąg|
|Argumentu|ciąg|\_ResourceId|ciąg|
|client_Browser|ciąg|ClientBrowser|ciąg|
|client_City|ciąg|ClientCity|ciąg|
|client_CountryOrRegion|ciąg|ClientCountryOrRegion|ciąg|
|client_IP|ciąg|ClientIP|ciąg|
|client_Model|ciąg|ClientModel|ciąg|
|client_OS|ciąg|ClientOS|ciąg|
|client_StateOrProvince|ciąg|ClientStateOrProvince|ciąg|
|client_Type|ciąg|ClientType|ciąg|
|cloud_RoleInstance|ciąg|AppRoleInstance|ciąg|
|cloud_RoleName|ciąg|AppRoleName|ciąg|
|customDimensions|dynamiczna|Właściwości|dynamiczna|
|customMeasurements|dynamiczna|Miary|dynamiczna|
|iKey|ciąg|IKey|ciąg|
|itemCount|int|ItemCount|int|
|Elementów|ciąg|\_Elementów|ciąg|
|itemType|ciąg|Typ|ciąg|
|message|ciąg|Wiadomość|ciąg|
|operation_Id|ciąg|OperationId|ciąg|
|operation_Name|ciąg|OperationName|ciąg|
|operation_ParentId|ciąg|OperationParentId|ciąg|
|operation_SyntheticSource|ciąg|OperationSyntheticSource|ciąg|
|sdkVersion|ciąg|SdkVersion|ciąg|
|session_Id|ciąg|SessionId|ciąg|
|severityLevel|int|SeverityLevel|int|
|sygnatura czasowa|datetime|TimeGenerated|datetime|
|user_AccountId|ciąg|UserAccountId|ciąg|
|user_AuthenticatedId|ciąg|UserAuthenticatedId|ciąg|
|user_Id|ciąg|UserId|ciąg|

## <a name="next-steps"></a>Następne kroki

* [Eksploruj metryki](../platform/metrics-charts.md)
* [Pisanie zapytań analitycznych](../log-query/log-query-overview.md)

