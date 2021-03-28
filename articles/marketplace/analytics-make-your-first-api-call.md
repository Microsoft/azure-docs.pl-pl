---
title: Utwórz pierwsze wywołanie interfejsu API w celu uzyskania dostępu do komercyjnych danych analitycznych portalu Marketplace
description: Przykłady dotyczące korzystania z interfejsu API do uzyskiwania dostępu do komercyjnych danych analitycznych portalu Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 9e5fbdfca80d19f026a014a89ffbf137bacb521c
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2021
ms.locfileid: "105639578"
---
# <a name="make-your-first-api-call-to-access-commercial-marketplace-analytics-data"></a>Utwórz pierwsze wywołanie interfejsu API w celu uzyskania dostępu do komercyjnych danych analitycznych portalu Marketplace

Aby uzyskać listę interfejsów API do uzyskiwania dostępu do komercyjnych danych analitycznych portalu Marketplace, zobacz [interfejsy API umożliwiające dostęp do komercyjnych danych analitycznych portalu Marketplace](analytics-available-apis.md). Przed rozpoczęciem pierwszego wywołania interfejsu API upewnij się, że spełniono [wymagania wstępne](analytics-prerequisites.md) , aby programowo uzyskać dostęp do komercyjnych danych analitycznych portalu Marketplace.

## <a name="token-generation"></a>Generowanie tokenu

Przed wywołaniem dowolnej metody należy najpierw uzyskać token dostępu Azure Active Directory (Azure AD). Należy przekazać token dostępu usługi Azure AD do nagłówka autoryzacji każdej metody w interfejsie API. Po uzyskaniu tokenu dostępu masz 60 minut, aby użyć go przed wygaśnięciem. Po wygaśnięciu tokenu można odświeżyć token i nadal używać go do dalszych wywołań interfejsu API.

Zapoznaj się z przykładowym żądaniem poniżej w celu wygenerowania tokenu. Trzy wartości wymagane do wygenerowania tokenu to `clientId` , `clientSecret` i `tenantId` . `resource`Parametr powinien być ustawiony na `https://graph.windows.net` .

***Przykład żądania***:

```json
curl --location --request POST 'https://login.microsoftonline.com/{TenantId}/oauth2/token' \
--header 'return-client-request-id: true' \
--header 'Content-Type: application/x-www-form-urlencoded' \
--data-urlencode 'resource=https://graph.windows.net' \
--data-urlencode 'client_id={client_id}' \
--data-urlencode 'client_secret={client_secret}' \
--data-urlencode 'grant_type=client_credentials'
```

***Przykład odpowiedzi***:

```json
{
    "token_type": "Bearer",
    "expires_in": "3599",
    "ext_expires_in": "3599",
    "expires_on": "1612794445",
    "not_before": "1612790545",
    "resource": "https://graph.windows.net",
    "access_token": {Token}
}
```

Aby uzyskać więcej informacji na temat uzyskiwania tokenu usługi Azure AD dla aplikacji, zobacz [dostęp do danych analizy przy użyciu usług magazynu](/windows/uwp/monetize/access-analytics-data-using-windows-store-services#step-2-obtain-an-azure-ad-access-token).

## <a name="programmatic-api-call"></a>Programowe wywołanie interfejsu API

Po uzyskaniu tokenu usługi Azure AD zgodnie z opisem w poprzedniej sekcji wykonaj następujące kroki, aby utworzyć pierwszy raport dostępu programowego.

Dane można pobrać z następujących zestawów danych (DataSetName):

- ISVCustomer
- ISVMarketplaceInsights
- ISVUsage
- ISVOrder

W poniższych sekcjach przedstawiono przykłady sposobu programistycznego dostępu `OrderId` z zestawu danych ISVOrder.

### <a name="step-1-make-a-rest-call-using-the-get-datasets-api"></a>Krok 1. wykonywanie wywołania REST za pomocą interfejsu API pobierania zestawów danych

Odpowiedź interfejsu API zawiera nazwę zestawu danych, z którego można pobrać raport. W przypadku określonego zestawu danych odpowiedź interfejsu API zawiera również listę kolumn, które można wybrać, które mogą być używane dla niestandardowego szablonu raportu. W celu uzyskania nazw kolumn można także skorzystać z następujących tabel:

- [Tabela szczegółów zamówień](orders-dashboard.md#orders-details-table)
- [Tabela szczegółów użycia](usage-dashboard.md#usage-details-table)
- [Tabela szczegółów klienta](customer-dashboard.md#customer-details-table)
- [Tabela szczegółów szczegółowych informacji o portalu Marketplace](insights-dashboard.md#marketplace-insights-details-table)

***Przykład żądania***:

```json
curl 
--location 
--request GET 'https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledDataset ' \ 
--header 'Authorization: Bearer <AzureADToken>'
```

***Przykład odpowiedzi***:

```json
{
    "value": [
        {
            "datasetName": "ISVOrder",
            "selectableColumns": [
                "MarketplaceSubscriptionId",
                "MonthStartDate",
                "OfferType",
                "AzureLicenseType",
                "Sku",
                "CustomerCountry",
                "IsPreviewSKU",
                "OrderId",
                "OrderQuantity",
                "CloudInstanceName",
                "IsNewCustomer",
                "OrderStatus",
                "OrderCancelDate",
                "CustomerCompanyName",
                "CustomerName",
                "OrderPurchaseDate",
                "OfferName",
                "TrialEndDate",
                "CustomerId",
                "BillingAccountId"
            ],
            "availableMetrics": [],
            "availableDateRanges": [
                "LAST_MONTH",
                "LAST_3_MONTHS",
                "LAST_6_MONTHS",
                "LIFETIME"
            ]
        },
    ],
    "totalCount": 1,
    "message": "Dataset fetched successfully",
    "statusCode": 200
}
```

### <a name="step-2-create-the-custom-query"></a>Krok 2. Tworzenie kwerendy niestandardowej

W tym kroku użyjemy identyfikatora zamówienia z raportu Orders, aby utworzyć zapytanie niestandardowe dla żądanego raportu. Wartość domyślna, `timespan` Jeśli nie zostanie określona w zapytaniu wynosi sześć miesięcy.

***Przykład żądania***:

```json
curl 
--location 
--request POST ' https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledQueries' \ 
--header ' Authorization: Bearer <AzureAD_Token>' \ 
--header 'Content-Type: application/json' \ 
--data-raw 
            '{ 
                "Query": "SELECT OrderId from ISVOrder", 
                "Name": "ISVOrderQuery1", 
                "Description": "Get a list of all Order IDs" 
             }'
```

***Przykład odpowiedzi***:

```json
{
    "value": [
        {
            "queryId": "78be43f2-e35f-491a-8cd5-78fe14194f9c",
            "name": "ISVOrderQuery1",
            "description": "Get a list of all Order IDs”,
            "query": "SELECT OrderId from ISVOrder",
            "type": "userDefined",
            "user": "142344300",
            "createdTime": "2021-01-06T05:38:34",
            "modifiedTime": null
        }
    ],
    "totalCount": 1,
    "message": "Query created successfully",
    "statusCode": 200
}
```

Po pomyślnym wykonaniu zapytania `queryId` jest generowana, która musi zostać użyta do wygenerowania raportu.

### <a name="step-3-execute-test-query-api"></a>Krok 3. wykonanie interfejsu API zapytania testowego

W tym kroku użyjemy interfejsu API zapytania testowego, aby uzyskać pierwszych 100 wierszy dla utworzonego zapytania.

***Przykład żądania***:

```json
curl 
--location 
--request GET 'https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledQueries/testQueryResult?exportQuery=SELECT%20OrderId%20from%20ISVOrder' \ 
--header ' Authorization: Bearer <AzureADToken>'
```

***Przykład odpowiedzi***:

```json
{
    "value": [
        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2ba8"
        },
        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2bb8"
        },
        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2bc8"
        },
        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2bd8"
        },
        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2be8"
        },
               .
               .
               .

        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2bf0"
        },
        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2bf1"
        },
        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2bf2"
        },
        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2bf3"
        },
        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2bf4"
        }
    ],
    "totalCount": 100,
    "message": null,
    "statusCode": 200
}
```

### <a name="step-4-create-the-report"></a>Krok 4. Tworzenie raportu

W tym kroku użyjemy wcześniej wygenerowanego, `QueryId` Aby utworzyć raport.

***Przykład żądania***:

```json
curl 
--location 
--request POST 'https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledReport' \ 
--header ' Authorization: Bearer <AzureADToken>' \ 
--header 'Content-Type: application/json' \ 
--data-raw 
                 '{
                   “ReportName": "ISVReport1",
                   "Description": "Report for getting list of Order Ids",
                   "QueryId": "78be43f2-e35f-491a-8cd5-78fe14194f9c",
                   "StartTime": "2021-01-06T19:00:00Z”,
                   "RecurrenceInterval": 48,
                   "RecurrenceCount": 20,
                    "Format": "csv"
                  }'
```

<br>

_**Tabela 1: Opis parametrów użytych w tym przykładzie żądania**_

| Parametr | Opis |
| ------------ | ------------- |
| `Description` | Podaj krótki opis generowanego raportu. |
| `QueryId` | Jest to `queryId` wygenerowane, gdy zapytanie zostało utworzone w kroku 2: Tworzenie kwerendy niestandardowej. |
| `StartTime` | Godzina rozpoczęcia pierwszego uruchomienia raportu. |
| `RecurrenceInterval` | Interwał cyklu podany podczas tworzenia raportu. |
| `RecurrenceCount` | Liczba cykli podana podczas tworzenia raportu. |
| `Format` | Formaty plików CSV i TSV są obsługiwane. |
|||

***Przykład odpowiedzi***:

```json
{
    "value": [
        {
            "reportId": "72fa95ab-35f5-4d44-a1ee-503abbc88003",
            "reportName": "ISVReport1",
            "description": "Report for getting list of Order Ids",
            "queryId": "78be43f2-e35f-491a-8cd5-78fe14194f9c",
            "query": "SELECT OrderId from ISVOrder",
            "user": "142344300",
            "createdTime": "2021-01-06T05:46:00Z",
            "modifiedTime": null,
            "startTime": "2021-01-06T19:00:00Z",
            "reportStatus": "Active",
            "recurrenceInterval": 48,
            "recurrenceCount": 20,
            "callbackUrl": null,
            "format": "csv"
        }
    ],
    "totalCount": 1,
    "message": "Report created successfully",
    "statusCode": 200
}
```

W przypadku pomyślnego wykonania `reportId` zostanie wygenerowany element, który ma zostać użyty do zaplanowania pobrania raportu.

### <a name="step-5-execute-report-executions-api"></a>Krok 5. wykonywanie interfejsu API wykonywania raportów

Aby uzyskać bezpieczną lokalizację (adres URL) raportu, teraz zostanie uruchomiony interfejs API wykonywania raportów.

***Przykład żądania***:

```json
Curl
--location
--request GET 'https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledReport/execution/72fa95ab-35f5-4d44-a1ee-503abbc88003' \
--header ' Authorization: Bearer <AzureADToken>' \
```

***Przykład odpowiedzi***:

```json
{
    "value": [
        {
            "executionId": "1f18b53b-df30-4d98-85ee-e6c7e687aeed",
            "reportId": "72fa95ab-35f5-4d44-a1ee-503abbc88003",
            "recurrenceInterval": 48,
            "recurrenceCount": 20,
            "callbackUrl": null,
            "format": "csv",
            "executionStatus": "Pending",
            "reportAccessSecureLink": null,
            "reportExpiryTime": null,
            "reportGeneratedTime": null
        }
    ],
    "totalCount": 1,
    "message": null,
    "statusCode": 200
}
```

## <a name="next-steps"></a>Następne kroki

- Interfejsy API można wypróbować za pomocą [adresu URL interfejsu API struktury Swagger](https://swagger.io/docs/specification/api-host-and-base-path/)
- [Model dostępu programistycznego](analytics-programmatic-access.md)
