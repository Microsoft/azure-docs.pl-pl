---
title: Przewodnik po interfejsie API REST usługi Azure Monitoring
description: Jak uwierzytelniać żądania i używać interfejsu API REST Azure Monitor do pobierania dostępnych definicji metryk i wartości metryk.
ms.subservice: metrics
ms.topic: conceptual
ms.date: 03/19/2018
ms.custom: has-adal-ref
ms.openlocfilehash: 41c2432c3c80be7d28e8f33372dea44ba108c5d3
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100616443"
---
# <a name="azure-monitoring-rest-api-walkthrough"></a>Przewodnik po interfejsie API REST usługi Azure Monitoring

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

W tym artykule pokazano, jak przeprowadzić uwierzytelnianie, aby kod mógł korzystać z [interfejsu API REST monitorowania Microsoft Azure](/rest/api/monitor/).

Interfejs API Azure Monitor umożliwia programowe pobranie dostępnych domyślnych definicji metryk, stopnia szczegółowości i wartości metryk. Dane można zapisywać w osobnym magazynie danych, takim jak Azure SQL Database, Azure Cosmos DB lub Azure Data Lake. W razie konieczności można wykonać dodatkowe analizy.

Oprócz pracy z różnymi punktami danych metryk, interfejs API monitora umożliwia również wyświetlanie listy reguł alertów, wyświetlanie dzienników aktywności i wiele innych. Aby zapoznać się z pełną listą dostępnych operacji, zobacz temat [Informacje o interfejsie API REST monitorowania Microsoft Azure](/rest/api/monitor/).

## <a name="authenticating-azure-monitor-requests"></a>Uwierzytelnianie Azure Monitor żądania

Pierwszym krokiem jest uwierzytelnienie żądania.

Wszystkie zadania wykonywane względem interfejsu API Azure Monitor korzystają z modelu uwierzytelniania Azure Resource Manager. W związku z tym wszystkie żądania muszą być uwierzytelniane za pomocą Azure Active Directory (Azure AD). Jednym z metod uwierzytelniania aplikacji klienckiej jest utworzenie jednostki usługi Azure AD i pobranie tokenu uwierzytelniania (JWT). Poniższy przykładowy skrypt demonstruje Tworzenie jednostki usługi Azure AD za pomocą programu PowerShell. Aby uzyskać bardziej szczegółowy przewodnik, zapoznaj się z dokumentacją dotyczącą [korzystania z programu Azure PowerShell, aby utworzyć jednostkę usługi w celu uzyskania dostępu do zasobów](/powershell/azure/create-azure-service-principal-azureps). Istnieje również możliwość utworzenia jednostki [usługi za pośrednictwem Azure Portal](../../active-directory/develop/howto-create-service-principal-portal.md).

```powershell
$subscriptionId = "{azure-subscription-id}"
$resourceGroupName = "{resource-group-name}"

# Authenticate to a specific Azure subscription.
Connect-AzAccount -SubscriptionId $subscriptionId

# Password for the service principal
$pwd = "{service-principal-password}"
$secureStringPassword = ConvertTo-SecureString -String $pwd -AsPlainText -Force

# Create a new Azure AD application
$azureAdApplication = New-AzADApplication `
                        -DisplayName "My Azure Monitor" `
                        -HomePage "https://localhost/azure-monitor" `
                        -IdentifierUris "https://localhost/azure-monitor" `
                        -Password $secureStringPassword

# Create a new service principal associated with the designated application
New-AzADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

# Assign Reader role to the newly created service principal
New-AzRoleAssignment -RoleDefinitionName Reader `
                          -ServicePrincipalName $azureAdApplication.ApplicationId.Guid

```

Aby wykonać zapytanie dotyczące interfejsu API Azure Monitor, aplikacja kliencka powinna używać wcześniej utworzonej jednostki usługi do uwierzytelniania. Poniższy przykładowy skrypt programu PowerShell przedstawia jedno podejście przy użyciu [Active Directory Authentication Library](../../active-directory/azuread-dev/active-directory-authentication-libraries.md) (ADAL) w celu uzyskania tokenu uwierzytelniania JWT. Token JWT jest przesyłany jako część parametru autoryzacji HTTP w żądaniach do interfejsu API REST Azure Monitor.

```powershell
$azureAdApplication = Get-AzADApplication -IdentifierUri "https://localhost/azure-monitor"

$subscription = Get-AzSubscription -SubscriptionId $subscriptionId

$clientId = $azureAdApplication.ApplicationId.Guid
$tenantId = $subscription.TenantId
$authUrl = "https://login.microsoftonline.com/${tenantId}"

$AuthContext = [Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext]$authUrl
$cred = New-Object -TypeName Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential -ArgumentList ($clientId, $pwd)

$result = $AuthContext.AcquireTokenAsync("https://management.core.windows.net/", $cred).GetAwaiter().GetResult()

# Build an array of HTTP header values
$authHeader = @{
'Content-Type'='application/json'
'Accept'='application/json'
'Authorization'=$result.CreateAuthorizationHeader()
}
```

Po uwierzytelnieniu zapytania można wykonywać w odniesieniu do interfejsu API REST Azure Monitor. Istnieją dwie przydatne zapytania:

1. Wyświetlanie listy definicji metryk dla zasobu
2. Pobieranie wartości metryk

> [!NOTE]
> Dodatkowe informacje o uwierzytelnianiu za pomocą interfejsu API REST platformy Azure można znaleźć w [dokumentacji interfejsu API REST platformy Azure](/rest/api/azure/).
>
>

## <a name="retrieve-metric-definitions-multi-dimensional-api"></a>Pobieranie definicji metryk (wielowymiarowego interfejsu API)

Użyj [interfejsu API REST definicji metryk Azure monitor](/rest/api/monitor/metricdefinitions) , aby uzyskać dostęp do listy metryk, które są dostępne dla usługi.

**Metoda**: pobieranie

**Identyfikator URI żądania**: https: \/ \/ Management.Azure.com/subscriptions/*{Identyfikator subskrypcji}*/resourceGroups/*{resourceGroupName}*/Providers/*{resourceProviderNamespace}* / *{ResourceType}* / *{ResourceType*}/Providers/Microsoft.Insights/metricDefinitions? API-Version =*{apiVersion}*

Na przykład aby pobrać definicje metryk dla konta usługi Azure Storage, żądanie będzie wyglądać w następujący sposób:

```powershell
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage/providers/microsoft.insights/metricDefinitions?api-version=2018-01-01"

Invoke-RestMethod -Uri $request `
                  -Headers $authHeader `
                  -Method Get `
                  -OutFile ".\contosostorage-metricdef-results.json" `
                  -Verbose

```

> [!NOTE]
> Aby pobrać definicje metryk przy użyciu wielowymiarowego interfejsu API REST metryk Azure Monitor, użyj "2018-01-01" jako wersji interfejsu API.
>
>

Utworzona treść odpowiedzi JSON będzie podobna do poniższego przykładu: (należy pamiętać, że druga Metryka ma wymiary)

```json
{
    "value": [
        {
            "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage/providers/microsoft.insights/metricdefinitions/UsedCapacity",
            "resourceId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage",
            "namespace": "Microsoft.Storage/storageAccounts",
            "category": "Capacity",
            "name": {
                "value": "UsedCapacity",
                "localizedValue": "Used capacity"
            },
            "isDimensionRequired": false,
            "unit": "Bytes",
            "primaryAggregationType": "Average",
            "supportedAggregationTypes": [
                "Total",
                "Average",
                "Minimum",
                "Maximum"
            ],
            "metricAvailabilities": [
                {
                    "timeGrain": "PT1H",
                    "retention": "P93D"
                },
                {
                    "timeGrain": "PT6H",
                    "retention": "P93D"
                },
                {
                    "timeGrain": "PT12H",
                    "retention": "P93D"
                },
                {
                    "timeGrain": "P1D",
                    "retention": "P93D"
                }
            ]
        },
        {
            "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage/providers/microsoft.insights/metricdefinitions/Transactions",
            "resourceId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage",
            "namespace": "Microsoft.Storage/storageAccounts",
            "category": "Transaction",
            "name": {
                "value": "Transactions",
                "localizedValue": "Transactions"
            },
            "isDimensionRequired": false,
            "unit": "Count",
            "primaryAggregationType": "Total",
            "supportedAggregationTypes": [
                "Total"
            ],
            "metricAvailabilities": [
                {
                    "timeGrain": "PT1M",
                    "retention": "P93D"
                },
                {
                    "timeGrain": "PT5M",
                    "retention": "P93D"
                },
                {
                    "timeGrain": "PT15M",
                    "retention": "P93D"
                },
                {
                    "timeGrain": "PT30M",
                    "retention": "P93D"
                },
                {
                    "timeGrain": "PT1H",
                    "retention": "P93D"
                },
                {
                    "timeGrain": "PT6H",
                    "retention": "P93D"
                },
                {
                    "timeGrain": "PT12H",
                    "retention": "P93D"
                },
                {
                    "timeGrain": "P1D",
                    "retention": "P93D"
                }
            ],
            "dimensions": [
                {
                    "value": "ResponseType",
                    "localizedValue": "Response type"
                },
                {
                    "value": "GeoType",
                    "localizedValue": "Geo type"
                },
                {
                    "value": "ApiName",
                    "localizedValue": "API name"
                }
            ]
        },
        ...
    ]
}
```

## <a name="retrieve-dimension-values-multi-dimensional-api"></a>Pobieranie wartości wymiarów (wielowymiarowy interfejs API)

Po znalezieniu dostępnych definicji metryk może istnieć kilka metryk, które mają wymiary. Przed wykonaniem zapytania dotyczącego metryki możesz chcieć wykryć zakres wartości dla wymiaru. Na podstawie tych wartości wymiarów można następnie wybrać filtrowanie lub segmentację metryk na podstawie wartości wymiarów podczas zapytania o metryki.  Aby to osiągnąć, użyj [interfejsu API REST metryk Azure monitor](/rest/api/monitor/metrics) .

Użyj nazwy metryki "value" (nie "localizedValue") dla żadnych żądań filtrowania. Jeśli nie określono żadnych filtrów, zwracana jest Metryka domyślna. Użycie tego interfejsu API pozwala tylko jednemu wymiarowi mieć filtr wieloznaczny.

> [!NOTE]
> Aby pobrać wartości wymiarów przy użyciu interfejsu API REST Azure Monitor, użyj "2018-01-01" jako wersji interfejsu API.
>
>

**Metoda**: pobieranie

**Identyfikator URI żądania**: https \: //Management.Azure.com/subscriptions/*{Subscription-ID}*/resourceGroups/*{Resource-Group-Name}*/Providers/*{Resource-Provider-Namespace}* / *{Resource-Type}* / *{Resource-Name}*/Providers/Microsoft.Insights/Metrics? metricnames =*{Metric}*&TimeSpan =*{StartTime/Endtime}*&$Filter =*{Filter}*&Result = Metadata&API-Version =*{apiVersion}*

Na przykład, aby pobrać listę wartości wymiaru, które były emitowane dla metryki "requests" dla "transakcji", gdzie wymiar geotype = "Primary" w określonym przedziale czasu, żądanie będzie w następujący sposób:

```powershell
$filter = "APIName eq '*' and GeoType eq 'Primary'"
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage/providers/microsoft.insights/metrics?metricnames=Transactions&timespan=2018-03-01T00:00:00Z/2018-03-02T00:00:00Z&resultType=metadata&`$filter=${filter}&api-version=2018-01-01"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -OutFile ".\contosostorage-dimension-values.json" `
    -Verbose
```

Utworzona treść odpowiedzi JSON będzie podobna do poniższego przykładu:

```json
{
  "timespan": "2018-03-01T00:00:00Z/2018-03-02T00:00:00Z",
  "value": [
    {
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage/providers/Microsoft.Insights/metrics/Transactions",
      "type": "Microsoft.Insights/metrics",
      "name": {
        "value": "Transactions",
        "localizedValue": "Transactions"
      },
      "unit": "Count",
      "timeseries": [
        {
          "metadatavalues": [
            {
              "name": {
                "value": "apiname",
                "localizedValue": "apiname"
              },
              "value": "DeleteBlob"
            }
          ]
        },
        {
          "metadatavalues": [
            {
              "name": {
                "value": "apiname",
                "localizedValue": "apiname"
              },
              "value": "SetBlobProperties"
            }
          ]
        },
        ...
      ]
    }
  ],
  "namespace": "Microsoft.Storage/storageAccounts",
  "resourceregion": "eastus"
}
```

## <a name="retrieve-metric-values-multi-dimensional-api"></a>Pobieranie wartości metryk (wielowymiarowego interfejsu API)

Po znalezieniu dostępnych definicji metryk i możliwych wartości wymiarów można pobrać powiązane wartości metryk.  Aby to osiągnąć, użyj [interfejsu API REST metryk Azure monitor](/rest/api/monitor/metrics) .

Użyj nazwy metryki "value" (nie "localizedValue") dla żadnych żądań filtrowania. Jeśli nie zostaną określone żadne filtry wymiaru, zwracana jest Metryka zagregowana. Jeśli zapytanie Metric zwraca wiele szeregów czasowych, można użyć parametrów zapytania "Top" i "OrderBy" do zwrócenia ograniczonej uporządkowanej listy elementów szeregów czasowych.

> [!NOTE]
> Aby pobrać wielowymiarowe wartości metryk przy użyciu interfejsu API REST Azure Monitor, użyj "2018-01-01" jako wersji interfejsu API.
>
>

**Metoda**: pobieranie

**Identyfikator URI żądania**: https: \/ /Management.Azure.com/subscriptions/*{Subscription-ID}*/resourceGroups/*{Resource-Group-Name}*/Providers/*{Resource-Provider-Namespace}* / *{Resource-Type}* / *{Resource-Name}*/Providers/Microsoft.Insights/Metrics? metricnames =*{Metric}*&TimeSpan *= {StartTime/Endtime}*&$Filter =*{Filter}*&interval =*{timeGrain}*&agregacji =*{aggreation}*&API-Version =*{apiVersion}*

Na przykład aby pobrać 3 najpopularniejsze interfejsy API, w wartości malejącej o liczbę "Transactions" w 5-minutowym zakresie, gdzie GeotType było "Primary", żądanie może być następujące:

```powershell
$filter = "APIName eq '*' and GeoType eq 'Primary'"
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage/providers/microsoft.insights/metrics?metricnames=Transactions&timespan=2018-03-01T02:00:00Z/2018-03-01T02:05:00Z&`$filter=${filter}&interval=PT1M&aggregation=Total&top=3&orderby=Total desc&api-version=2018-01-01"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -OutFile ".\contosostorage-metric-values.json" `
    -Verbose
```

Utworzona treść odpowiedzi JSON będzie podobna do poniższego przykładu:

```json
{
  "cost": 0,
  "timespan": "2018-03-01T02:00:00Z/2018-03-01T02:05:00Z",
  "interval": "PT1M",
  "value": [
    {
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage/providers/Microsoft.Insights/metrics/Transactions",
      "type": "Microsoft.Insights/metrics",
      "name": {
        "value": "Transactions",
        "localizedValue": "Transactions"
      },
      "unit": "Count",
      "timeseries": [
        {
          "metadatavalues": [
            {
              "name": {
                "value": "apiname",
                "localizedValue": "apiname"
              },
              "value": "GetBlobProperties"
            }
          ],
          "data": [
            {
              "timeStamp": "2017-09-19T02:00:00Z",
              "total": 2
            },
            {
              "timeStamp": "2017-09-19T02:01:00Z",
              "total": 1
            },
            {
              "timeStamp": "2017-09-19T02:02:00Z",
              "total": 3
            },
            {
              "timeStamp": "2017-09-19T02:03:00Z",
              "total": 7
            },
            {
              "timeStamp": "2017-09-19T02:04:00Z",
              "total": 2
            }
          ]
        },
        ...
      ]
    }
  ],
  "namespace": "Microsoft.Storage/storageAccounts",
  "resourceregion": "eastus"
}
```

## <a name="retrieve-metric-definitions"></a>Pobierz definicje metryk

Użyj [interfejsu API REST definicji metryk Azure monitor](/rest/api/monitor/metricdefinitions) , aby uzyskać dostęp do listy metryk, które są dostępne dla usługi.

**Metoda**: pobieranie

**Identyfikator URI żądania**: https: \/ \/ Management.Azure.com/subscriptions/*{Identyfikator subskrypcji}*/resourceGroups/*{resourceGroupName}*/Providers/*{resourceProviderNamespace}* / *{ResourceType}* / *{ResourceType*}/Providers/Microsoft.Insights/metricDefinitions? API-Version =*{apiVersion}*

Na przykład aby pobrać definicje metryk dla aplikacji logiki platformy Azure, żądanie będzie wyglądać w następujący sposób:

```powershell
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metricDefinitions?api-version=2016-03-01"

Invoke-RestMethod -Uri $request `
                  -Headers $authHeader `
                  -Method Get `
                  -OutFile ".\contosotweets-metricdef-results.json" `
                  -Verbose
```

> [!NOTE]
> Aby pobrać definicje metryk przy użyciu interfejsu API REST Azure Monitor, użyj "2016-03-01" jako wersji interfejsu API.
>
>

Utworzona treść odpowiedzi JSON będzie podobna do poniższego przykładu:

```json
{
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metricdefinitions",
  "value": [
    {
      "name": {
        "value": "RunsStarted",
        "localizedValue": "Runs Started"
      },
      "category": "AllMetrics",
      "startTime": "0001-01-01T00:00:00Z",
      "endTime": "0001-01-01T00:00:00Z",
      "unit": "Count",
      "primaryAggregationType": "Total",
      "resourceUri": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets",
      "resourceId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets",
      "metricAvailabilities": [
        {
          "timeGrain": "PT1M",
          "retention": "P30D",
          "location": null,
          "blobLocation": null
        },
        {
          "timeGrain": "PT1H",
          "retention": "P30D",
          "location": null,
          "blobLocation": null
        }
      ],
      "properties": null,
      "dimensions": null,
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metricdefinitions/RunsStarted",
      "supportedAggregationTypes": [ "None", "Average", "Minimum", "Maximum", "Total", "Count" ]
    }
  ]
}
```

Aby uzyskać więcej informacji, zobacz [listę definicji metryk dla zasobu w dokumentacji interfejsu API REST Azure monitor](/rest/api/monitor/metricdefinitions) .

## <a name="retrieve-metric-values"></a>Pobierz wartości metryk

Po znalezieniu dostępnych definicji metryk można pobrać powiązane wartości metryk. Użyj nazwy metryki "value" (nie "localizedValue") dla żadnych żądań filtrowania (na przykład Pobierz punkty danych metryki "CpuTime" i "requests"). Jeśli nie określono żadnych filtrów, zwracana jest Metryka domyślna.

> [!NOTE]
> Aby pobrać wartości metryk przy użyciu interfejsu API REST Azure Monitor, użyj "2016-09-01" jako wersji interfejsu API.
>
>

**Metoda**: pobieranie

**Identyfikator URI żądania**: `https:\//management.azure.com/subscriptions/\*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/*{resource-provider-namespace}*/*{resource-type}*/*{resource-name}*/providers/microsoft.insights/metrics?$filter=*{filter}*&api-version=*{apiVersion}*`

Na przykład aby pobrać punkty danych metryki RunsSucceeded dla danego przedziału czasu i dla ziarna o godzinie 1 godziny, żądanie będzie w następujący sposób:

```powershell
$filter = "(name.value eq 'RunsSucceeded') and aggregationType eq 'Total' and startTime eq 2017-08-18T19:00:00 and endTime eq 2017-08-18T23:00:00 and timeGrain eq duration'PT1H'"
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metrics?`$filter=${filter}&api-version=2016-09-01"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -OutFile ".\contosotweets-metrics-results.json" `
    -Verbose
```

Utworzona treść odpowiedzi JSON będzie podobna do poniższego przykładu:

```json
{
  "value": [
    {
      "data": [
        {
          "timeStamp": "2017-08-18T19:00:00Z",
          "total": 0.0
        },
        {
          "timeStamp": "2017-08-18T20:00:00Z",
          "total": 159.0
        },
        {
          "timeStamp": "2017-08-18T21:00:00Z",
          "total": 174.0
        },
        {
          "timeStamp": "2017-08-18T22:00:00Z",
          "total": 97.0
        }
      ],
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/Microsoft.Insights/metrics/RunsSucceeded",
      "name": {
        "value": "RunsSucceeded",
        "localizedValue": "Runs Succeeded"
      },
      "type": "Microsoft.Insights/metrics",
      "unit": "Count"
    }
  ]
}
```

Aby pobrać dane lub punkty agregacji, Dodaj do filtru nazwy definicji metryk i typy agregacji, jak pokazano w następującym przykładzie:

```powershell
$filter = "(name.value eq 'ActionsCompleted' or name.value eq 'RunsSucceeded') and (aggregationType eq 'Total' or aggregationType eq 'Average') and startTime eq 2017-08-18T21:00:00 and endTime eq 2017-08-18T21:30:00 and timeGrain eq duration'PT1M'"
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metrics?`$filter=${filter}&api-version=2016-09-01"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -OutFile ".\contosotweets-metrics-multiple-results.json" `
    -Verbose
```

Utworzona treść odpowiedzi JSON będzie podobna do poniższego przykładu:

```json
{
  "value": [
    {
      "data": [
        {
          "timeStamp": "2017-08-18T21:03:00Z",
          "total": 5.0,
          "average": 1.0
        },
        {
          "timeStamp": "2017-08-18T21:04:00Z",
          "total": 7.0,
          "average": 1.0
        }
      ],
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/Microsoft.Insights/metrics/ActionsCompleted",
      "name": {
        "value": "ActionsCompleted",
        "localizedValue": "Actions Completed "
      },
      "type": "Microsoft.Insights/metrics",
      "unit": "Count"
    },
    {
      "data": [
        {
          "timeStamp": "2017-08-18T21:03:00Z",
          "total": 5.0,
          "average": 1.0
        },
        {
          "timeStamp": "2017-08-18T21:04:00Z",
          "total": 7.0,
          "average": 1.0
        }
      ],
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/Microsoft.Insights/metrics/RunsSucceeded",
      "name": {
        "value": "RunsSucceeded",
        "localizedValue": "Runs Succeeded"
      },
      "type": "Microsoft.Insights/metrics",
      "unit": "Count"
    }
  ]
}
```

### <a name="use-armclient"></a>Użyj ARMClient

Dodatkowym podejściem jest użycie [ARMClient](https://github.com/projectkudu/armclient) na komputerze z systemem Windows. ARMClient obsługuje uwierzytelnianie w usłudze Azure AD (a w efekcie token JWT) automatycznie. Poniższe kroki przedstawiają użycie ARMClient do pobierania danych metryk:

1. Zainstaluj [czekoladę](https://chocolatey.org/) i [ARMClient](https://github.com/projectkudu/armclient).
2. W oknie terminalu wpisz *armclient.exe login*. W ten sposób zostanie wyświetlony komunikat z prośbą o zalogowanie się do platformy Azure.
3. Wpisz *ARMCLIENT Get [your_resource_id]/Providers/Microsoft.Insights/metricdefinitions? API-Version = 2016-03-01*
4. Wpisz *ARMCLIENT Get [your_resource_id]/Providers/Microsoft.Insights/Metrics? API-Version = 2016-09-01*

Na przykład w celu pobrania definicji metryk dla określonej aplikacji logiki należy wydać następujące polecenie:

```console
armclient GET /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metricDefinitions?api-version=2016-03-01
```

## <a name="retrieve-the-resource-id"></a>Pobieranie identyfikatora zasobu

Korzystanie z interfejsu API REST może pomóc zrozumieć dostępne definicje metryk, stopień szczegółowości i powiązane wartości. Te informacje są przydatne podczas korzystania z [biblioteki zarządzania systemu Azure](/previous-versions/azure/reference/mt417623(v=azure.100)).

W przypadku poprzedniego kodu identyfikator zasobu, który ma być używany, to pełna ścieżka do żądanego zasobu platformy Azure. Na przykład, aby wykonać zapytanie dotyczące aplikacji sieci Web platformy Azure, identyfikator zasobu to:

*/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/sites/{site-name}/*

Poniższa lista zawiera kilka przykładów formatów identyfikatorów zasobów dla różnych zasobów platformy Azure:

* **IoT Hub** -/subscriptions/*{Subscription-ID}*/resourceGroups/*{Resource-Group-Name}*/Providers/Microsoft.Devices/IotHubs/*{IoT-Hub-Name}*
* **Elastyczna Pula SQL** -/subscriptions/*{Subscription-ID}*/resourceGroups/*{Resource-Group-Name}*/Providers/Microsoft.SQL/Servers/*{Pool-DB}*/elasticpools/*{SQL-Pool-Name}*
* **SQL Database (V12)** —/subscriptions/*{Subscription-ID}*/resourceGroups/*{Resource-Group-Name}*/Providers/Microsoft.SQL/Servers/*{Server-Name}*/Databases/*{Database-Name}*
* **Service Bus** -/subscriptions/*{Subscription-ID}*/resourceGroups/*{Resource-Group-Name}*/Providers/Microsoft.ServiceBus/*{Namespace}* / *{ServiceBus-Name}*
* **Zestawy skalowania maszyn wirtualnych** —/subscriptions/*{Subscription-ID}*/resourceGroups/*{Resource-Group-Name}*/Providers/Microsoft.COMPUTE/virtualMachineScaleSets/*{VM-Name}*
* **Maszyny wirtualne** —/subscriptions/*{Subscription-ID}*/resourceGroups/*{Resource-Group-Name}*/Providers/Microsoft.COMPUTE/virtualMachines/*{VM-Name}*
* **Event Hubs** /subscriptions/*{Subscription-ID}*/resourceGroups/*{Resource-Group-Name}*/Providers/Microsoft.EventHub/Namespaces/*{EventHub-Namespace}*

Istnieją alternatywne podejścia do pobierania identyfikatora zasobu, w tym przy użyciu Azure Resource Explorer, wyświetlania żądanego zasobu w Azure Portal oraz za pomocą programu PowerShell lub interfejsu wiersza polecenia platformy Azure.

### <a name="azure-resource-explorer"></a>Eksplorator zasobów Azure

Aby znaleźć identyfikator zasobu dla żądanego zasobu, pomocne podejście polega na użyciu narzędzia [Azure Resource Explorer](https://resources.azure.com) . Przejdź do żądanego zasobu, a następnie poszukaj pokazanego identyfikatora, tak jak na poniższym zrzucie ekranu:

![Alt "Azure Resource Explorer"](./media/rest-api-walkthrough/azure_resource_explorer.png)

### <a name="azure-portal"></a>Azure Portal

Identyfikator zasobu można również uzyskać z Azure Portal. W tym celu przejdź do żądanego zasobu, a następnie wybierz pozycję Właściwości. Identyfikator zasobu jest wyświetlany w sekcji właściwości, jak pokazano na poniższym zrzucie ekranu:

![Alt "Identyfikator zasobu wyświetlany w bloku właściwości w Azure Portal"](./media/rest-api-walkthrough/resourceid_azure_portal.png)

### <a name="azure-powershell"></a>Azure PowerShell

Identyfikator zasobu można także pobrać przy użyciu Azure PowerShell poleceń cmdlet. Aby na przykład uzyskać identyfikator zasobu dla aplikacji logiki platformy Azure, uruchom polecenie cmdlet Get-AzureLogicApp, jak w poniższym przykładzie:

```powershell
Get-AzLogicApp -ResourceGroupName azmon-rest-api-walkthrough -Name contosotweets
```

Wynik powinien wyglądać podobnie do poniższego przykładu:

```output
Id             : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets
Name           : ContosoTweets
Type           : Microsoft.Logic/workflows
Location       : centralus
ChangedTime    : 8/21/2017 6:58:57 PM
CreatedTime    : 8/18/2017 7:54:21 PM
AccessEndpoint : https://prod-08.centralus.logic.azure.com:443/workflows/f3a91b352fcc47e6bff989b85446c5db
State          : Enabled
Definition     : {$schema, contentVersion, parameters, triggers...}
Parameters     : {[$connections, Microsoft.Azure.Management.Logic.Models.WorkflowParameter]}
SkuName        :
AppServicePlan :
PlanType       :
PlanId         :
Version        : 08586982649483762729
```

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Aby pobrać identyfikator zasobu dla konta usługi Azure Storage za pomocą interfejsu wiersza polecenia platformy Azure, wykonaj `az storage account show` polecenie, jak pokazano w następującym przykładzie:

```azurecli
az storage account show -g azmon-rest-api-walkthrough -n contosotweets2017
```

Wynik powinien wyglądać podobnie do poniższego przykładu:

```json
{
  "accessTier": null,
  "creationTime": "2017-08-18T19:58:41.840552+00:00",
  "customDomain": null,
  "enableHttpsTrafficOnly": false,
  "encryption": null,
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/contosotweets2017",
  "identity": null,
  "kind": "Storage",
  "lastGeoFailoverTime": null,
  "location": "centralus",
  "name": "contosotweets2017",
  "networkAcls": null,
  "primaryEndpoints": {
    "blob": "https://contosotweets2017.blob.core.windows.net/",
    "file": "https://contosotweets2017.file.core.windows.net/",
    "queue": "https://contosotweets2017.queue.core.windows.net/",
    "table": "https://contosotweets2017.table.core.windows.net/"
  },
  "primaryLocation": "centralus",
  "provisioningState": "Succeeded",
  "resourceGroup": "azmon-rest-api-walkthrough",
  "secondaryEndpoints": null,
  "secondaryLocation": "eastus2",
  "sku": {
    "name": "Standard_GRS",
    "tier": "Standard"
  },
  "statusOfPrimary": "available",
  "statusOfSecondary": "available",
  "tags": {},
  "type": "Microsoft.Storage/storageAccounts"
}
```

> [!NOTE]
> Azure Logic Apps nie są jeszcze dostępne za pośrednictwem interfejsu wiersza polecenia platformy Azure, więc konto usługi Azure Storage jest wyświetlane w poprzednim przykładzie.
>
>

## <a name="retrieve-activity-log-data"></a>Pobieranie danych dziennika aktywności

Oprócz definicji metryk i pokrewnych wartości można również użyć interfejsu API REST Azure Monitor, aby uzyskać dodatkowe interesujące informacje związane z zasobami platformy Azure. Przykładowo można wykonywać zapytania dotyczące danych [dziennika aktywności](/rest/api/monitor/activitylogs) . Poniższe przykładowe żądania używają interfejsu API REST Azure Monitor do wysyłania zapytań do dziennika aktywności.

Pobierz dzienniki aktywności z filtrem:

``` HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '2018-01-21T20:00:00Z' and eventTimestamp le '2018-01-23T20:00:00Z' and resourceGroupName eq 'MSSupportGroup'
```

Pobierz dzienniki aktywności z filtrem i wybierz:

```HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '2015-01-21T20:00:00Z' and eventTimestamp le '2015-01-23T20:00:00Z' and resourceGroupName eq 'MSSupportGroup'&$select=eventName,id,resourceGroupName,resourceProviderName,operationName,status,eventTimestamp,correlationId,submissionTimestamp,level
```

Pobierz dzienniki aktywności przy użyciu funkcji Select:

```HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&$select=eventName,id,resourceGroupName,resourceProviderName,operationName,status,eventTimestamp,correlationId,submissionTimestamp,level
```

Pobierz dzienniki aktywności bez filtrowania lub wybierz:

```HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01
```

## <a name="next-steps"></a>Następne kroki

* Zapoznaj się z [omówieniem monitorowania](../overview.md).
* Wyświetl [obsługiwane metryki przy użyciu Azure monitor](../platform/metrics-supported.md).
* Przejrzyj [Informacje o interfejsie API REST monitorowania Microsoft Azure](/rest/api/monitor/).
* Przejrzyj [bibliotekę zarządzania platformy Azure](/previous-versions/azure/reference/mt417623(v=azure.100)).

