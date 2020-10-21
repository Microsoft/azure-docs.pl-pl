---
title: Przeglądanie danych rozliczeń rejestracji dla przedsiębiorstw na platformie Azure za pomocą interfejsu API REST
description: Dowiedz się, jak za pomocą interfejsów API REST platformy Azure przeglądać informacje rozliczeniowe rejestracji dla przedsiębiorstw.
author: lleonard-msft
ms.service: cost-management-billing
ms.subservice: enterprise
ms.topic: article
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: 777c8549d79f820c1847e711969919df128cebd3
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2020
ms.locfileid: "92132418"
---
# <a name="review-enterprise-enrollment-billing-using-rest-apis"></a>Przeglądanie rozliczeń rejestracji dla przedsiębiorstw przy użyciu interfejsów API REST

Interfejsy API usługi Azure Reporting ułatwiają przeglądanie kosztów korzystania z platformy Azure i zarządzanie nimi.

Ten artykuł zawiera informacje dotyczące pobierania informacji rozliczeniowych skojarzonych z kontami rozliczeniowymi, działem lub kontami rejestracji umowy Enterprise Agreement (EA) przy użyciu interfejsów API REST platformy Azure.

## <a name="individual-account-billing"></a>Rozliczanie indywidualnego konta

Aby uzyskać szczegółowe informacje dotyczące użycia kont w dziale:

```http
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/providers/Microsoft.Consumption/usageDetails?api-version=2018-06-30
Content-Type: application/json   
Authorization: Bearer
```

Parametr `{billingAccountId}` jest wymagany i powinien zawierać identyfikator konta.

Wymagane są następujące nagłówki:

|Nagłówek żądania|Opis|  
|--------------------|-----------------|  
|*Content-Type:*|Wymagany. Ustaw wartość `application/json`.|  
|*Authorization:*|Wymagany. Ustaw prawidłowy [klucz interfejsu API](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based) `Bearer`. |  

Ten przykład przedstawia wywołanie synchroniczne zwracające szczegóły bieżącego cyklu rozliczeniowego. Ze względu na wydajność wywołania synchroniczne zwracają informacje z ostatniego miesiąca.  Można również wywołać [interfejs API asynchronicznie](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based), aby zwrócić dane z 36 miesięcy.


## <a name="response"></a>Odpowiedź  

Kod stanu 200 (OK) jest zwracany dla pomyślnej odpowiedzi, która zawiera listę szczegółowych kosztów dla konta.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/BillingAccounts/1234/providers/Microsoft.Billing/billingPeriods/201702/providers/Microsoft.Consumption/usageDetails/usageDetailsId1",
      "name": "usageDetailsId1",
      "type": "Microsoft.Consumption/usageDetails",
      "properties": {
        ...
        "usageStart": "2017-02-13T00:00:00Z",
        "usageEnd": "2017-02-13T23:59:59Z",
        "instanceName": "shared1",
        "instanceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Default-Web-eastasia/providers/Microsoft.Web/sites/shared1",
        "currency": "USD",
        "usageQuantity": 0.00328,
        "billableQuantity": 0.00328,
        "pretaxCost": 0.67,
        "isEstimated": false,
        ...
      }
    }
  ]
}
```  

Ten przykład jest skrócony. Aby uzyskać pełny opis każdego pola odpowiedzi i obsługi błędów, zobacz [Pobieranie szczegółów użycia dla konta rozliczeniowego](/rest/api/consumption/usagedetails/list#billingaccountusagedetailslist-legacy).

## <a name="department-billing"></a>Rozliczenia działu

Można uzyskać zagregowane szczegóły użycia dla wszystkich kont w dziale.

```http
GET https://management.azure.com/providers/Microsoft.Billing/departments/{departmentId}/providers/Microsoft.Consumption/usageDetails?api-version=2018-06-30
Content-Type: application/json   
Authorization: Bearer
```

Parametr `{departmentId}` jest wymagany i powinien zawierać identyfikator działu na koncie rejestracji.

Wymagane są następujące nagłówki:

|Nagłówek żądania|Opis|  
|--------------------|-----------------|  
|*Content-Type:*|Wymagany. Ustaw wartość `application/json`.|  
|*Authorization:*|Wymagany. Ustaw prawidłowy [klucz interfejsu API](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based) `Bearer`. |  

Ten przykład przedstawia wywołanie synchroniczne zwracające szczegóły bieżącego cyklu rozliczeniowego. Ze względu na wydajność wywołania synchroniczne zwracają informacje z ostatniego miesiąca.  Można również wywołać [interfejs API asynchronicznie](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based), aby zwrócić dane z 36 miesięcy.

### <a name="response"></a>Odpowiedź  

Kod stanu 200 (OK) jest zwracany dla pomyślnej odpowiedzi, która zawiera listę kosztów oraz szczegółowych informacji o użyciu w danym okresie rozliczeniowym i dla konkretnego identyfikatora faktury w dziale.


Poniższy przykład przedstawia dane wyjściowe interfejsu API REST dla działu `1234`.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/Departments/1234/providers/Microsoft.Billing/billingPeriods/201702/providers/Microsoft.Consumption/usageDetails/usageDetailsId1",
      "name": "usageDetailsId1",
      "type": "Microsoft.Consumption/usageDetails",
      "properties": {
        "billingPeriodId": "/providers/Microsoft.Billing/Departments/1234/providers/Microsoft.Billing/billingPeriods/201702",
        "invoiceId": "/providers/Microsoft.Billing/Departments/1234/providers/Microsoft.Billing/invoices/201703-123456789",
        "usageStart": "2017-02-13T00:00:00Z",
        "usageEnd": "2017-02-13T23:59:59Z",
        "instanceName": "shared1",
        "instanceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Default-Web-eastasia/providers/Microsoft.Web/sites/shared1",
        "instanceLocation": "eastasia",
        "currency": "USD",
        "usageQuantity": 0.00328,
        "billableQuantity": 0.00328,
        "pretaxCost": 0.67,
        ...
      }
    }
  ]
}
```  

Ten przykład jest skrócony. Aby uzyskać pełny opis każdego pola odpowiedzi i obsługi błędów, zobacz [Pobieranie szczegółów użycia dla działu](/rest/api/consumption/usagedetails/list#departmentusagedetailslist-legacy).

## <a name="enrollment-account-billing"></a>Rozliczanie konta rejestracji

Można pobrać szczegóły użycia zagregowane dla konta rejestracji.

```http
GET https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts/{enrollmentAccountId}/providers/Microsoft.Consumption/usageDetails?api-version=2018-06-30
Content-Type: application/json   
Authorization: Bearer
```

Parametr `{enrollmentAccountId}` jest wymagany i powinien zawierać identyfikator konta rejestracji.

Wymagane są następujące nagłówki:

|Nagłówek żądania|Opis|  
|--------------------|-----------------|  
|*Content-Type:*|Wymagany. Ustaw wartość `application/json`.|  
|*Authorization:*|Wymagany. Ustaw prawidłowy [klucz interfejsu API](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based) `Bearer`. |  

Ten przykład przedstawia wywołanie synchroniczne zwracające szczegóły bieżącego cyklu rozliczeniowego. Ze względu na wydajność wywołania synchroniczne zwracają informacje z ostatniego miesiąca.  Można również wywołać [interfejs API asynchronicznie](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based), aby zwrócić dane z 36 miesięcy.

### <a name="response"></a>Odpowiedź  

Kod stanu 200 (OK) jest zwracany dla pomyślnej odpowiedzi, która zawiera listę kosztów oraz szczegółowych informacji o użyciu w danym okresie rozliczeniowym i dla konkretnego identyfikatora faktury w dziale.

Poniższy przykład przedstawia dane wyjściowe interfejsu API REST dla rejestracji przedsiębiorstwa `1234`.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/EnrollmentAccounts/1234/providers/Microsoft.Billing/billingPeriods/201702/providers/Microsoft.Consumption/usageDetails/usageDetailsId1",
      "name": "usageDetailsId1",
      "type": "Microsoft.Consumption/usageDetails",
      "properties": {
        "billingPeriodId": "/providers/Microsoft.Billing/EnrollmentAccounts/1234/providers/Microsoft.Billing/billingPeriods/201702",
        "invoiceId": "/providers/Microsoft.Billing/EnrollmentAccounts/1234/providers/Microsoft.Billing/invoices/201703-123456789",
        "usageStart": "2017-02-13T00:00:00Z",
        "usageEnd": "2017-02-13T23:59:59Z",
        ....
        "currency": "USD",
        "usageQuantity": 0.00328,
        "billableQuantity": 0.00328,
        "pretaxCost": 0.67,
        ...
      }
    }
  ]
}
```

Ten przykład jest skrócony. Aby uzyskać pełny opis każdego pola odpowiedzi i obsługi błędów, zobacz [Pobieranie szczegółów użycia dla konta rejestracji](/rest/api/consumption/usagedetails/list#enrollmentaccountusagedetailslist-legacy).

## <a name="next-steps"></a>Następne kroki
- Przejrzyj temat [Przegląd raportowania korporacyjnego](./enterprise-api.md)
- Zapoznaj się z tematem [Interfejs API REST rozliczania korporacyjnego](/rest/api/billing/)   
- [Rozpoczynanie pracy z interfejsem API REST platformy Azure](/rest/api/azure/)