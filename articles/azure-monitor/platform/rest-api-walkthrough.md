---
title: Przewodnik interfejsu API interfejsu REST monitorowania platformy Azure
description: Jak uwierzytelnić żądania i używać interfejsu API REST usługi Azure Monitor do pobierania dostępnych definicji metryk i wartości metryk.
ms.subservice: metrics
ms.topic: conceptual
ms.date: 03/19/2018
ms.openlocfilehash: 6b0e321747e0f84be5a75ab96749311ff0071e8d
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687410"
---
# <a name="azure-monitoring-rest-api-walkthrough"></a>Przewodnik interfejsu API interfejsu REST monitorowania platformy Azure

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

W tym artykule pokazano, jak przeprowadzić uwierzytelnianie, aby kod mógł korzystać z [odwołania interfejsu API REST monitora platformy Microsoft Azure](https://docs.microsoft.com/rest/api/monitor/).

Interfejs API usługi Azure Monitor umożliwia programowe pobieranie dostępnych domyślnych definicji metryk, szczegółowości i wartości metryk. Dane można zapisać w oddzielnym magazynie danych, takim jak Azure SQL Database, Azure Cosmos DB lub Azure Data Lake. Stamtąd można przeprowadzić dodatkową analizę w razie potrzeby.

Oprócz pracy z różnymi punktami danych metryki interfejs API monitora umożliwia również wyświetlanie reguł alertów, wyświetlanie dzienników aktywności i wiele więcej. Aby uzyskać pełną listę dostępnych operacji, zobacz [odwołanie interfejsu API REST monitora platformy Microsoft Azure](https://docs.microsoft.com/rest/api/monitor/).

## <a name="authenticating-azure-monitor-requests"></a>Uwierzytelnianie żądań usługi Azure Monitor

Pierwszym krokiem jest uwierzytelnienie żądania.

Wszystkie zadania wykonywane za pomocą interfejsu API usługi Azure Monitor korzystają z modelu uwierzytelniania usługi Azure Resource Manager. W związku z tym wszystkie żądania muszą być uwierzytelnione za pomocą usługi Azure Active Directory (Azure AD). Jednym z podejść do uwierzytelniania aplikacji klienckiej jest utworzenie jednostki usługi Azure AD i pobranie tokenu uwierzytelniania (JWT). Poniższy przykładowy skrypt demonstruje tworzenie jednostki usługi Azure AD za pośrednictwem programu PowerShell. Aby uzyskać bardziej szczegółowe informacje, zapoznaj się z dokumentacją [dotyczącą korzystania z programu Azure PowerShell w celu utworzenia jednostki usługi w celu uzyskania dostępu do zasobów.](https://docs.microsoft.com/powershell/azure/create-azure-service-principal-azureps) Istnieje również możliwość [utworzenia jednostki usługi za pośrednictwem witryny Azure portal.](../../active-directory/develop/howto-create-service-principal-portal.md)

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

Aby zbadać interfejs API usługi Azure Monitor, aplikacja kliencka powinna używać wcześniej utworzonego podmiotu usługi do uwierzytelniania. Poniższy przykładowy skrypt programu PowerShell przedstawia jedno podejście, korzystając z [biblioteki uwierzytelniania usługi Active Directory](../../active-directory/azuread-dev/active-directory-authentication-libraries.md) (ADAL) w celu uzyskania tokenu uwierzytelniania JWT. Token JWT jest przekazywany jako część parametru autoryzacji HTTP w żądaniach do interfejsu API REST usługi Azure Monitor.

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

Po uwierzytelnieniu kwerendy mogą być następnie wykonywane za pomocą interfejsu API REST usługi Azure Monitor. Istnieją dwa przydatne zapytania:

1. Wyświetlanie definicji metryki zasobu
2. Pobieranie wartości metryk

> [!NOTE]
> Aby uzyskać dodatkowe informacje na temat uwierzytelniania za pomocą interfejsu API usługi Azure REST, zapoznaj się z [odwołaniem](https://docs.microsoft.com/rest/api/azure/)do interfejsu API usługi Azure REST .
>
>

## <a name="retrieve-metric-definitions-multi-dimensional-api"></a>Pobieranie definicji metryk (wielowymiarowy interfejs API)

Użyj [interfejsu API REST definicje metryk usługi Azure Monitor,](https://docs.microsoft.com/rest/api/monitor/metricdefinitions) aby uzyskać dostęp do listy metryk dostępnych dla usługi.

**Metoda**: GET

**Żądanie identyfikatora URI**\/\/: https: management.azure.com/subscriptions/*{subscriptionId}*/resourceGroups/*{resourceGroupName}*/providers/*{resourceProviderNamespace}*/*{resourceType}*/{resourceName} /providers/microsoft.insights/metricDefinitions?api-version=*{apiVersion} {apiVersion} {resourceName}* /providers/microsoft.insights/metricDefinitions?api-version= {apiVersion}*{resourceName}*

Na przykład, aby pobrać definicje metryki dla konta usługi Azure Storage, żądanie będzie wyświetlane w następujący sposób:

```powershell
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage/providers/microsoft.insights/metricDefinitions?api-version=2018-01-01"

Invoke-RestMethod -Uri $request `
                  -Headers $authHeader `
                  -Method Get `
                  -OutFile ".\contosostorage-metricdef-results.json" `
                  -Verbose

```

> [!NOTE]
> Aby pobrać definicje metryk przy użyciu wielowymiarowego interfejsu API REST metryk usługi Azure Monitor, użyj "2018-01-01" jako wersji interfejsu API.
>
>

Wynikowa treść odpowiedzi JSON będzie podobna do następującego przykładu: (Należy zauważyć, że druga metryka ma wymiary)

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

Po znane są dostępne definicje metryk, mogą istnieć pewne metryki, które mają wymiary. Przed zapytaniem o metrykę można odkryć, jaki zakres wartości ma wymiar. Na podstawie tych wartości wymiarów można następnie filtrować lub segmentować metryki na podstawie wartości wymiarów podczas wykonywania zapytań o metryki.  Użyj [interfejsu API REST metryki usługi Azure Monitor,](https://docs.microsoft.com/rest/api/monitor/metrics) aby to osiągnąć.

Użyj nazwy metryki "value" (nie "localizedValue") dla wszystkich żądań filtrowania. Jeśli nie określono żadnych filtrów, zwracana jest domyślna metryka. Użycie tego interfejsu API umożliwia tylko jeden wymiar mieć filtr wieloznaczny.

> [!NOTE]
> Aby pobrać wartości wymiarów przy użyciu interfejsu API REST usługi Azure Monitor, użyj "2018-01-01" jako wersji interfejsu API.
>
>

**Metoda**: GET

**Żądanie identyfikatora URI:**\:https //management.azure.com/subscriptions/*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/*{resource-provider-namespace}*/*{resource-type}*/*{resource-name}*/providers/microsoft.insights/metrics?metricnames=*{metric}*&timespan=*{starttime/endtime}*&$filter=*{filter}*&resultType=metadata&api-version=*{apiVersion}*

Na przykład, aby pobrać listę wartości wymiarów, które zostały wyemitowane dla "wymiaru nazwa interfejsu API" dla metryki "Transakcje", gdzie wymiar GeoType = "Podstawowy" w określonym zakresie czasu, żądanie będzie następujące:

```powershell
$filter = "APIName eq '*' and GeoType eq 'Primary'"
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage/providers/microsoft.insights/metrics?metricnames=Transactions&timespan=2018-03-01T00:00:00Z/2018-03-02T00:00:00Z&resultType=metadata&`$filter=${filter}&api-version=2018-01-01"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -OutFile ".\contosostorage-dimension-values.json" `
    -Verbose
```

Wynikowy obiekt odpowiedzi JSON będzie podobny do następującego przykładu:

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

## <a name="retrieve-metric-values-multi-dimensional-api"></a>Pobieranie wartości metryk (wielowymiarowy interfejs API)

Po znane są dostępne definicje metryki i możliwe wartości wymiarów, możliwe jest pobranie powiązanych wartości metryk.  Użyj [interfejsu API REST metryki usługi Azure Monitor,](https://docs.microsoft.com/rest/api/monitor/metrics) aby to osiągnąć.

Użyj nazwy metryki "value" (nie "localizedValue") dla wszystkich żądań filtrowania. Jeśli nie określono filtrów wymiarów, zwracana jest zwinięta zagregowana metryka. Jeśli kwerenda metryczna zwraca wiele wieloetorów, można użyć parametrów kwerendy "Top" i "OrderBy", aby zwrócić ograniczoną uporządkowaną listę wieloetorowych.

> [!NOTE]
> Aby pobrać wielowymiarowe wartości metryki przy użyciu interfejsu API REST usługi Azure Monitor, użyj "2018-01-01" jako wersji interfejsu API.
>
>

**Metoda**: GET

**Identyfikator żądania :**\/https: /management.azure.com/subscriptions/*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/*{resource-provider-namespace}*/*{resource-type}*/*{resource-name}*/providers/microsoft.insights/metrics?metricnames?metricnames =*{metric}*&timespan=*{starttime/endtime}*&$filter=*{filter}*&interval=*{timeGrain}*&aggregation=*{aggreation}*&api-version=*{apiVersion}*

Na przykład, aby pobrać 3 najlepsze interfejsy API, w wartości malejącej, przez liczbę "Transakcje" w zakresie 5 minut, gdzie GeotType był "Podstawowy", żądanie będzie w następujący sposób:

```powershell
$filter = "APIName eq '*' and GeoType eq 'Primary'"
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage/providers/microsoft.insights/metrics?metricnames=Transactions&timespan=2018-03-01T02:00:00Z/2018-03-01T02:05:00Z&`$filter=${filter}&interval=PT1M&aggregation=Total&top=3&orderby=Total desc&api-version=2018-01-01"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -OutFile ".\contosostorage-metric-values.json" `
    -Verbose
```

Wynikowy obiekt odpowiedzi JSON będzie podobny do następującego przykładu:

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

## <a name="retrieve-metric-definitions"></a>Pobieranie definicji metryk

Użyj [interfejsu API REST definicje metryk usługi Azure Monitor,](https://msdn.microsoft.com/library/mt743621.aspx) aby uzyskać dostęp do listy metryk dostępnych dla usługi.

**Metoda**: GET

**Żądanie identyfikatora URI**\/\/: https: management.azure.com/subscriptions/*{subscriptionId}*/resourceGroups/*{resourceGroupName}*/providers/*{resourceProviderNamespace}*/*{resourceType}*/{resourceName} /providers/microsoft.insights/metricDefinitions?api-version=*{apiVersion} {apiVersion} {resourceName}* /providers/microsoft.insights/metricDefinitions?api-version= {apiVersion}*{resourceName}*

Na przykład, aby pobrać definicje metryki dla aplikacji logiki Azure, żądanie będzie wyświetlane w następujący sposób:

```powershell
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metricDefinitions?api-version=2016-03-01"

Invoke-RestMethod -Uri $request `
                  -Headers $authHeader `
                  -Method Get `
                  -OutFile ".\contosotweets-metricdef-results.json" `
                  -Verbose
```

> [!NOTE]
> Aby pobrać definicje metryk przy użyciu interfejsu API REST usługi Azure Monitor, użyj "2016-03-01" jako wersji interfejsu API.
>
>

Wynikowy obiekt odpowiedzi JSON będzie podobny do następującego przykładu:

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

Aby uzyskać więcej informacji, zobacz [listę definicji metryki dla zasobu w](https://msdn.microsoft.com/library/azure/mt743621.aspx) dokumentacji interfejsu API interfejsu REST usługi Azure Monitor.

## <a name="retrieve-metric-values"></a>Pobieranie wartości metryk

Gdy dostępne definicje metryki są znane, jest wtedy możliwe do pobrania powiązanych wartości metryki. Użyj nazwy metryki "value" (nie "localizedValue") dla wszystkich żądań filtrowania (na przykład pobrać punkty danych metryki "CpuTime" i "Żądania"). Jeśli nie określono żadnych filtrów, zwracana jest domyślna metryka.

> [!NOTE]
> Aby pobrać wartości metryki przy użyciu interfejsu API REST usługi Azure Monitor, użyj "2016-09-01" jako wersji interfejsu API.
>
>

**Metoda**: GET

**Prośba o identyfikator URI:**`https:\//management.azure.com/subscriptions/\*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/*{resource-provider-namespace}*/*{resource-type}*/*{resource-name}*/providers/microsoft.insights/metrics?$filter=*{filter}*&api-version=*{apiVersion}*`

Na przykład, aby pobrać runssucceededed punktów danych metryki dla danego zakresu czasu i dla ziarna czasu 1 godzinę, żądanie będzie w następujący sposób:

```powershell
$filter = "(name.value eq 'RunsSucceeded') and aggregationType eq 'Total' and startTime eq 2017-08-18T19:00:00 and endTime eq 2017-08-18T23:00:00 and timeGrain eq duration'PT1H'"
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metrics?`$filter=${filter}&api-version=2016-09-01"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -OutFile ".\contosotweets-metrics-results.json" `
    -Verbose
```

Wynikowy obiekt odpowiedzi JSON będzie podobny do następującego przykładu:

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

Aby pobrać wiele danych lub punktów agregacji, dodaj nazwy definicji metryki i typy agregacji do filtru, jak pokazano w poniższym przykładzie:

```powershell
$filter = "(name.value eq 'ActionsCompleted' or name.value eq 'RunsSucceeded') and (aggregationType eq 'Total' or aggregationType eq 'Average') and startTime eq 2017-08-18T21:00:00 and endTime eq 2017-08-18T21:30:00 and timeGrain eq duration'PT1M'"
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metrics?`$filter=${filter}&api-version=2016-09-01"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -OutFile ".\contosotweets-metrics-multiple-results.json" `
    -Verbose
```

Wynikowy obiekt odpowiedzi JSON będzie podobny do następującego przykładu:

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

Dodatkowym podejściem jest użycie [armclient](https://github.com/projectkudu/armclient) na komputerze z systemem Windows. ARMClient automatycznie obsługuje uwierzytelnianie usługi Azure AD (i wynikowy token JWT). W poniższych krokach opisano użycie programu ARMClient do pobierania danych metrycznych:

1. Zainstaluj [Chocolatey](https://chocolatey.org/) i [ARMClient](https://github.com/projectkudu/armclient).
2. W oknie terminala wpisz *login armclient.exe*. W ten sposób monituje o zalogowanie się na platformie Azure.
3. Wpisz *armclient GET [your_resource_id]/providers/microsoft.insights/metricdefinitions?api-version=2016-03-01*
4. Typ *armclient GET [your_resource_id]/providers/microsoft.insights/metrics?api-version=2016-09-01*

Na przykład w celu pobrania definicji metryki dla określonej aplikacji logiki, należy wydać następujące polecenie:

```console
armclient GET /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metricDefinitions?api-version=2016-03-01
```

## <a name="retrieve-the-resource-id"></a>Pobieranie identyfikatora zasobu

Za pomocą interfejsu API REST może naprawdę pomóc w zrozumieniu dostępnych definicji metryki, szczegółowości i powiązanych wartości. Te informacje są przydatne podczas korzystania z [biblioteki zarządzania usługą Azure](https://msdn.microsoft.com/library/azure/mt417623.aspx).

Dla poprzedniego kodu identyfikator zasobu do użycia jest pełną ścieżkę do żądanego zasobu platformy Azure. Na przykład do kwerendy względem aplikacji Azure Web App, identyfikator zasobu będzie:

*/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/sites/{site-name}/*

Poniższa lista zawiera kilka przykładów formatów identyfikatorów zasobów dla różnych zasobów platformy Azure:

* **Centrum IoT** - /subscriptions/*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/Microsoft.Devices/IotHubs/*{iot-hub-name}*
* **Elastyczna pula SQL** - /subscriptions/*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/Microsoft.Sql/servers/*{pool-db}*/elasticpools/*{sql-pool-name}*
* **Baza danych SQL (w wersji 12)** - /subscriptions/*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/Microsoft.Sql/servers/*{server-name}*/databases/*{database-name}*
* **Usługa Service Bus** - /subscriptions/*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/Microsoft.ServiceBus/*{namespace}*/*{servicebus-name}*
* **Zestawy skalowania maszyny wirtualnej** - /subscriptions/*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/Microsoft.Compute/virtualMachineScaleSets/*{vm-name}*
* **Maszyny wirtualne** - /subscriptions/*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/Microsoft.Compute/virtualMachines/*{vm-name}*
* **Centra zdarzeń** — /subscriptions/*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/Microsoft.EventHub/namespaces/*{eventhub-namespace}*

Istnieją alternatywne podejścia do pobierania identyfikatora zasobu, w tym przy użyciu Usługi Azure Resource Explorer, wyświetlanie żądanego zasobu w witrynie Azure portal i za pośrednictwem programu PowerShell lub interfejsu wiersza polecenia platformy Azure.

### <a name="azure-resource-explorer"></a>Eksplorator zasobów Azure

Aby znaleźć identyfikator zasobu dla żądanego zasobu, jednym z przydatnych metod jest użycie narzędzia [Eksploratora zasobów platformy Azure.](https://resources.azure.com) Przejdź do żądanego zasobu, a następnie spójrz na wyświetlany identyfikator, jak na poniższym zrzucie ekranu:

![Alt "Eksplorator zasobów platformy Azure"](./media/rest-api-walkthrough/azure_resource_explorer.png)

### <a name="azure-portal"></a>Azure Portal

Identyfikator zasobu można również uzyskać z witryny Azure portal. Aby to zrobić, przejdź do żądanego zasobu, a następnie wybierz pozycję Właściwości. Identyfikator zasobu jest wyświetlany w sekcji Właściwości, jak pokazano na poniższym zrzucie ekranu:

![Alt "Identyfikator zasobu wyświetlany w bloku Właściwości w witrynie Azure portal"](./media/rest-api-walkthrough/resourceid_azure_portal.png)

### <a name="azure-powershell"></a>Azure PowerShell

Identyfikator zasobu można pobrać przy użyciu poleceń cmdlet programu Azure PowerShell, jak również. Na przykład, aby uzyskać identyfikator zasobu dla aplikacji logiki azure, należy wykonać polecenie cmdlet Get-AzureLogicApp, jak w poniższym przykładzie:

```powershell
Get-AzLogicApp -ResourceGroupName azmon-rest-api-walkthrough -Name contosotweets
```

Wynik powinien być podobny do następującego przykładu:

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

Aby pobrać identyfikator zasobu dla konta usługi Azure Storage `az storage account show` przy użyciu interfejsu wiersza polecenia platformy Azure, wykonaj polecenie, jak pokazano w poniższym przykładzie:

```azurecli
az storage account show -g azmon-rest-api-walkthrough -n contosotweets2017
```

Wynik powinien być podobny do następującego przykładu:

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
> Usługa Azure Logic Apps nie są jeszcze dostępne za pośrednictwem interfejsu wiersza polecenia platformy Azure, w związku z tym konto usługi Azure Storage jest wyświetlane w poprzednim przykładzie.
>
>

## <a name="retrieve-activity-log-data"></a>Pobieranie danych dziennika aktywności

Oprócz definicji metryk i powiązanych wartości można również użyć interfejsu API REST usługi Azure Monitor, aby pobrać dodatkowe interesujące szczegółowe informacje związane z zasobami platformy Azure. Na przykład można zbadać dane [dziennika aktywności.](https://msdn.microsoft.com/library/azure/dn931934.aspx) Poniższy przykład pokazuje przy użyciu interfejsu API REST usługi Azure Monitor do kwerendy danych dziennika aktywności w określonym zakresie dat dla subskrypcji platformy Azure:

```powershell
$apiVersion = "2015-04-01"
$filter = "eventTimestamp ge '2017-08-18' and eventTimestamp le '2017-08-19'and eventChannels eq 'Admin, Operation'"
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/providers/microsoft.insights/eventtypes/management/values?api-version=${apiVersion}&`$filter=${filter}"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -Verbose
```

## <a name="next-steps"></a>Następne kroki

* Zapoznaj się [z przeglądem monitorowania](../../azure-monitor/overview.md).
* Wyświetl [obsługiwane metryki za pomocą usługi Azure Monitor](metrics-supported.md).
* Zapoznaj się z odwołaniem [interfejsu API REST monitora platformy Microsoft Azure](https://msdn.microsoft.com/library/azure/dn931943.aspx).
* Przejrzyj [bibliotekę zarządzania platformy Azure](https://msdn.microsoft.com/library/azure/mt417623.aspx).
