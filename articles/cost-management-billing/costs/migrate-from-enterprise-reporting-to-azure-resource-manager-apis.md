---
title: Migracja z interfejsów API raportowania korporacyjnego do interfejsów API usługi Azure Resource Manager
description: Ten artykuł ułatwia zrozumienie różnic między interfejsami API raportowania a interfejsami API usługi Azure Resource Manager, czego można oczekiwać podczas migracji do interfejsów API usługi Azure Resource Manager i nowych funkcji dostępnych w nowych interfejsach API usługi Azure Resource Manager.
author: bandersmsft
ms.reviewer: adwise
ms.service: cost-management-billing
ms.subservice: common
ms.topic: reference
ms.date: 03/10/2021
ms.author: banders
ms.openlocfilehash: 24fbf52c8fd0338537862a54e15e8a249541a701
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/11/2021
ms.locfileid: "102635794"
---
# <a name="migrate-from-enterprise-reporting-to-azure-resource-manager-apis"></a>Migracja z interfejsów API raportowania korporacyjnego do interfejsów API usługi Azure Resource Manager

Ten artykuł ma pomóc deweloperom, którzy utworzyli rozwiązania niestandardowe przy użyciu [Interfejsów API usługi Azure Reporting dla klientów korporacyjnych](../manage/enterprise-api.md), w migracji do interfejsów API usługi Azure Resource Manager dla usługi Cost Management. Obsługa jednostki usługi dla nowszych interfejsów API usługi Azure Resource Manager jest teraz ogólnie dostępna. Interfejsy API usługi Azure Resource Manager są na etapie aktywnego rozwoju. Rozważ przeprowadzenie migracji do nich zamiast korzystania ze starszych interfejsów API usługi Azure Reporting dla klientów korporacyjnych. Starsze interfejsy API stają się przestarzałe. Ten artykuł ułatwia zrozumienie różnic między interfejsami API raportowania a interfejsami API usługi Azure Resource Manager, czego można oczekiwać podczas migracji do interfejsów API usługi Azure Resource Manager i nowych funkcji dostępnych w nowych interfejsach API usługi Azure Resource Manager.

## <a name="api-differences"></a>Różnice między interfejsami API

Poniższe informacje opisują różnice między starszymi interfejsami API raportowania dla klientów korporacyjnych i nowszymi interfejsami API usługi Azure Resource Manager.

| **Zastosowanie** | **Interfejsy API umowy Enterprise Agreement** | **Interfejsy API usługi Azure Resource Manager** |
| --- | --- | --- |
| Uwierzytelnianie | Klucz interfejsu API aprowizowany w portalu umowy Enterprise Agreement (EA) | Uwierzytelnianie usługi Azure Active Directory (Azure AD) przy użyciu tokenów użytkownika lub jednostek usług. Jednostki usług zastępują klucze interfejsu API. |
| Zakresy i uprawnienia | Wszystkie żądania są objęte zakresem rejestracji. Przypisania uprawnień klucza interfejsu API określają, czy zostaną zwrócone dane dotyczące całej rejestracji, działu lub określonego konta. Brak uwierzytelniania użytkowników. | Użytkownicy lub jednostki usług mają przypisany dostęp do zakresu rejestracji, działu lub konta. |
| Punkt końcowy identyfikatora URI | https://consumption.azure.com | https://management.azure.com |
| Etap rozwoju | Tryb konserwacji. Na ścieżce do wycofania. | Aktywnie rozwijane |
| Dostępne interfejsy API | Ograniczone do tych, które obecnie są dostępne | Dostępne są równoważne interfejsy API, aby zastąpić każdy interfejs API umowy EA. <p> Dostępne są również dodatkowe [Interfejsy API usługi Cost Management](/rest/api/cost-management/), w tym: <p> <ul><li>Budżety</li><li>Alerty<li>Eksporty</li></ul> |

## <a name="migration-checklist"></a>Lista kontrolna migracji

- Zapoznaj się z [Interfejsami API REST usługi Azure Resource Manager](/rest/api/azure).
- Ustal, których interfejsów API umowy EA używasz, i zobacz, które interfejsy API usługi Azure Resource Manager przenieść, zapoznając się z artykułem [Mapowanie interfejsu API umowy EA do nowych interfejsów API usługi Azure Resource Manager](#ea-api-mapping-to-new-azure-resource-manager-apis).
- Skonfiguruj autoryzację i uwierzytelnianie usługi dla interfejsów API usługi Azure Resource Manager
  - Jeśli jeszcze nie używasz interfejsów API usługi Azure Resource Manager, [zarejestruj aplikację kliencką w usłudze Azure AD](/rest/api/azure/#register-your-client-application-with-azure-ad). Rejestracja tworzy jednostkę usługi, której będziesz używać do wywoływania interfejsów API.
  - Przypisz jednostce usługi dostęp do wymaganych zakresów, jak opisano poniżej.
  - Zaktualizuj wszelki kod, aby korzystać z [uwierzytelniania usługi Azure AD](/rest/api/azure/#create-the-request) przy użyciu jednostki usługi.
- Przetestuj interfejsy API, a następnie zaktualizuj wszelki kod, aby zastąpić wywołania interfejsu API umowy EA wywołaniami interfejsu API usługi Azure Resource Manager.
- Zaktualizuj obsługę błędów, wprowadzając nowe kody błędów. Niektóre zagadnienia do rozważenia:
  - Interfejsy API usługi Azure Resource Manager mają limit czasu 60 sekund.
  - Interfejsy API usługi Azure Resource Manager mają lokalne ograniczenia szybkości. Powoduje to, że w przypadku przekroczenia szybkości zostanie zwrócony błąd 429 o ograniczeniu przepustowości. Buduj swoje rozwiązania tak, aby uniknąć zbyt wielu wywołań interfejsu API w krótkim przedziale czasie.
- Zapoznaj się z innymi interfejsami API usługi Cost Management dostępnymi w usłudze Azure Resource Manager i oceń je pod kątem przyszłego użycia. Aby uzyskać więcej informacji, zobacz [Używanie dodatkowych interfejsów API usługi Cost Management](#use-additional-cost-management-apis).

## <a name="assign-service-principal-access-to-azure-resource-manager-apis"></a>Przypisywanie jednostce usługi dostępu do interfejsów API usługi Azure Resource Manager

Po utworzeniu jednostki usługi w celu programowego wywoływania interfejsów API usługi Azure Resource Manager należy przypisać im odpowiednie uprawnienia na potrzeby autoryzacji i wykonywania żądań w usłudze Azure Resource Manager. Dla różnych scenariuszy istnieją dwie struktury uprawnień.

### <a name="azure-billing-hierarchy-access"></a>Dostęp do hierarchii rozliczeń platformy Azure

Aby przypisać uprawnienia nazwy głównej usługi do konta rozliczeń przedsiębiorstwa, działów lub zakresów kont rejestracji, zobacz [Przypisywanie ról do usługi Azure Enterprise Agreement nazw głównych usług](../manage/assign-roles-azure-service-principals.md).

### <a name="azure-role-based-access-control"></a>Kontrola dostępu na podstawie ról na platformie Azure

Nowa obsługa jednostki usługi obejmuje zakresy specyficzne dla platformy Azure, takie jak grupy zarządzania, subskrypcje i grupy zasobów. Uprawnienia jednostki usługi można przypisywać do tych zakresów bezpośrednio w witrynie [Azure Portal](../../active-directory/develop/howto-create-service-principal-portal.md#assign-a-role-to-the-application) lub przy użyciu programu [Azure PowerShell](../../active-directory/develop/howto-authenticate-service-principal-powershell.md#assign-the-application-to-a-role).

## <a name="ea-api-mapping-to-new-azure-resource-manager-apis"></a>Mapowanie interfejsu API umowy EA do nowych interfejsów API usługi Azure Resource Manager

Skorzystaj z poniższej tabeli, aby zidentyfikować używane obecnie interfejsy API umowy EA oraz interfejs API usługi Azure Resource Manager, którego można użyć w zastępstwie.

| **Scenariusz** | **Interfejsy API umowy EA** | **Interfejsy API usługi Azure Resource Manager** |
| --- | --- | --- |
| Podsumowanie salda | [/balancesummary](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) |[Microsoft.Consumption/balances](/rest/api/consumption/balances/getbybillingaccount) |
| Arkusz cen | [/pricesheet](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) | [Microsoft.Consumption/pricesheets/default](/rest/api/consumption/pricesheet) — użyj w przypadku cen negocjowanych <p> [Interfejs API cen detalicznych](/rest/api/cost-management/retail-prices/azure-retail-prices) — użyj w przypadku cen detalicznych |
| Szczegóły wystąpienia zarezerwowanego | [/reservationdetails](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) | [Microsoft.CostManagement/generateReservationDetailsReport](/rest/api/cost-management/generatereservationdetailsreport) |
| Podsumowanie wystąpienia zarezerwowanego | [/reservationsummaries](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) | [Microsoft.Consumption/reservationSummaries](/rest/api/consumption/reservationssummaries/list#reservationsummariesdailywithbillingaccountid) |
| Rekomendacje wystąpienia zarezerwowanego | [/SharedReservationRecommendations](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation)<p>[/SingleReservationRecommendations](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation) | [Microsoft.Consumption/reservationRecommendations](/rest/api/consumption/reservationrecommendations/list) |
| Opłaty za wystąpienie zarezerwowane | [/reservationcharges](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges) | [Microsoft.Consumption/reservationTransactions](/rest/api/consumption/reservationtransactions/list) |

## <a name="migration-details-by-api"></a>Szczegóły migracji według interfejsu API

W poniższych sekcjach zestawiono stare przykłady żądań interfejsu API z nowymi przykładami zastępczych interfejsów API.

### <a name="balance-summary-api"></a>Interfejs API podsumowania salda

Podczas wywoływania nowego interfejsu API podsumowania salda użyj następujących identyfikatorów URI żądania. Numer rejestracji powinien być używany jako identyfikator billingAccountId.

#### <a name="supported-requests"></a>Obsługiwane żądania

[Pobierz na potrzeby rejestracji](/rest/api/consumption/balances/getbybillingaccount)

```json
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/providers/Microsoft.Consumption/balances?api-version=2019-10-01
```

### <a name="response-body-changes"></a>Zmiany treści odpowiedzi

_Stara treść odpowiedzi_:

```json
{
        "id": "enrollments/100/billingperiods/201507/balancesummaries",
          "billingPeriodId": 201507,
          "currencyCode": "USD",
          "beginningBalance": 0,
          "endingBalance": 1.1,
          "newPurchases": 1,
          "adjustments": 1.1,
          "utilized": 1.1,
          "serviceOverage": 1,
          "chargesBilledSeparately": 1,
          "totalOverage": 1,
          "totalUsage": 1.1,
          "azureMarketplaceServiceCharges": 1,
          "newPurchasesDetails": [
            {
              "name": "",
              "value": 1
            }
          ],
          "adjustmentDetails": [
            {
              "name": "Promo Credit",
              "value": 1.1
            },
            {
              "name": "SIE Credit",
              "value": 1.0
            }
          ]
    }

```

_Nowa treść odpowiedzi_:

Te same dane są teraz dostępne w polu `properties` odpowiedzi nowego interfejsu API. W przypadku niektórych nazw pól mogą wystąpić drobne zmiany pisowni.

```json
{
  "id": "/providers/Microsoft.Billing/billingAccounts/123456/providers/Microsoft.Billing/billingPeriods/201702/providers/Microsoft.Consumption/balances/balanceId1",
  "name": "balanceId1",
  "type": "Microsoft.Consumption/balances",
  "properties": {
    "currency": "USD  ",
    "beginningBalance": 3396469.19,
    "endingBalance": 2922371.02,
    "newPurchases": 0,
    "adjustments": 0,
    "utilized": 474098.17,
    "serviceOverage": 0,
    "chargesBilledSeparately": 0,
    "totalOverage": 0,
    "totalUsage": 474098.17,
    "azureMarketplaceServiceCharges": 609.82,
    "billingFrequency": "Month",
    "priceHidden": false,
    "newPurchasesDetails": [
      {
        "name": "Promo Purchase",
        "value": 1
      }
    ],
    "adjustmentDetails": [
      {
        "name": "Promo Credit",
        "value": 1.1
      },
      {
        "name": "SIE Credit",
        "value": 1
      }
    ]
  }
}

```

### <a name="price-sheet"></a>Arkusz cen

Podczas wywoływania nowego interfejsu API arkusza cen użyj następujących identyfikatorów URI żądania.

#### <a name="supported-requests"></a>Obsługiwane żądania

 Interfejs API można wywołać przy użyciu następujących zakresów:

- Rejestracja: `providers/Microsoft.Billing/billingAccounts/{billingAccountId}`
- Subskrypcje: `subscriptions/{subscriptionId}`

[_Pobierz dla bieżącego okresu rozliczeniowego_](/rest/api/consumption/pricesheet/get)


```json
https://management.azure.com/{scope}/providers/Microsoft.Consumption/pricesheets/default?api-version=2019-10-01
```

[_Pobierz dla określonego okresu rozliczeniowego_](/rest/api/consumption/pricesheet/getbybillingperiod)

```json
https://management.azure.com/{scope}/providers/Microsoft.Billing/billingPeriods/{billingPeriodName}/providers/Microsoft.Consumption/pricesheets/default?api-version=2019-10-01
```

#### <a name="response-body-changes"></a>Zmiany treści odpowiedzi

_Stara odpowiedź_:

```json
      [
        {
              "id": "enrollments/57354989/billingperiods/201601/products/343/pricesheets",
              "billingPeriodId": "201704",
            "meterId": "dc210ecb-97e8-4522-8134-2385494233c0",
              "meterName": "A1 VM",
              "unitOfMeasure": "100 Hours",
              "includedQuantity": 0,
              "partNumber": "N7H-00015",
              "unitPrice": 0.00,
              "currencyCode": "USD"
        },
        {
              "id": "enrollments/57354989/billingperiods/201601/products/2884/pricesheets",
              "billingPeriodId": "201404",
            "meterId": "dc210ecb-97e8-4522-8134-5385494233c0",
              "meterName": "Locally Redundant Storage Premium Storage - Snapshots - AU East",
              "unitOfMeasure": "100 GB",
              "includedQuantity": 0,
              "partNumber": "N9H-00402",
              "unitPrice": 0.00,
              "currencyCode": "USD"
        },
        ...
    ]

```

_Nowa odpowiedź_:

Stare dane są teraz w polu `pricesheets` odpowiedzi nowego interfejsu API. Podano również szczegółowe informacje dotyczące miernika.

```json
{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Billing/billingPeriods/201702/providers/Microsoft.Consumption/pricesheets/default",
  "name": "default",
  "type": "Microsoft.Consumption/pricesheets",
  "properties": {
    "nextLink": "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/microsoft.consumption/pricesheets/default?api-version=2018-01-31&$skiptoken=AQAAAA%3D%3D&$expand=properties/pricesheets/meterDetails",
    "pricesheets": [
      {
        "billingPeriodId": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Billing/billingPeriods/201702",
        "meterId": "00000000-0000-0000-0000-000000000000",
        "unitOfMeasure": "100 Hours",
        "includedQuantity": 100,
        "partNumber": "XX-11110",
        "unitPrice": 0.00000,
        "currencyCode": "EUR",
        "offerId": "OfferId 1",
        "meterDetails": {
          "meterName": "Data Transfer Out (GB)",
          "meterCategory": "Networking",
          "unit": "GB",
          "meterLocation": "Zone 2",
          "totalIncludedQuantity": 0,
          "pretaxStandardRate": 0.000
        }
      }
    ]
  }
}

```

### <a name="reserved-instance-usage-details"></a>Szczegóły użycia wystąpienia zarezerwowanego

Firma Microsoft nie pracuje aktywnie nad interfejsami API szczegółów rezerwacji opartymi na synchroniczności. W ramach migracji zalecamy przechodzenie do nowszego wzorca wywołań asynchronicznego interfejsu API obsługiwanego przez jednostkę usługi. Żądania asynchroniczne lepiej obsługują duże ilości danych i zmniejszają liczbę błędów przekroczenia limitu czasu.

#### <a name="supported-requests"></a>Obsługiwane żądania

Podczas wywoływania nowego asynchronicznego interfejsu API szczegółów rezerwacji użyj następujących identyfikatorów URI żądania. Numer rejestracji powinien być użyty jako identyfikator `billingAccountId`. Interfejs API można wywołać z następującymi zakresami:

- Rejestracja: `providers/Microsoft.Billing/billingAccounts/{billingAccountId}`

#### <a name="sample-request-to-generate-a-reservation-details-report"></a>Przykładowe żądanie wygenerowania raportu szczegółów rezerwacji

```json
POST 
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/providers/Microsoft.CostManagement/generateReservationDetailsReport?startDate={startDate}&endDate={endDate}&api-version=2019-11-01

```

#### <a name="sample-request-to-poll-report-generation-status"></a>Przykładowe żądanie dotyczące stanu generowania raportu sondowania

```json
GET
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/providers/Microsoft.CostManagement/reservationDetailsOperationResults/{operationId}?api-version=2019-11-01

```

#### <a name="sample-poll-response"></a>Przykładowa odpowiedź na sondę

```json
{
  "status": "Completed",
  "properties": {
    "reportUrl": "https://storage.blob.core.windows.net/details/20200911/00000000-0000-0000-0000-000000000000?sv=2016-05-31&sr=b&sig=jep8HT2aphfUkyERRZa5LRfd9RPzjXbzB%2F9TNiQ",
    "validUntil": "2020-09-12T02:56:55.5021869Z"
  }
}

```

#### <a name="response-body-changes"></a>Zmiany treści odpowiedzi

Poniżej znajduje się odpowiedź starszego synchronicznego interfejsu API szczegółów rezerwacji.

_Stara odpowiedź_:

```json
{
    "reservationOrderId": "00000000-0000-0000-0000-000000000000",
    "reservationId": "00000000-0000-0000-0000-000000000000",
    "usageDate": "2018-02-01T00:00:00",
    "skuName": "Standard_F2s",
    "instanceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/resourvegroup1/providers/microsoft.compute/virtualmachines/VM1",
    "totalReservedQuantity": 18.000000000000000,
    "reservedHours": 432.000000000000000,
    "usedHours": 400.000000000000000
}

```

_Nowa odpowiedź_:

Nowy interfejs API tworzy plik CSV. Zobacz następujące pola pliku.

| **Stara właściwość** | **Nowa właściwość** | **Uwagi** |
| --- | --- | --- |
|  | InstanceFlexibilityGroup | Nowa właściwość elastyczności wystąpienia. |
|  | InstanceFlexibilityRatio | Nowa właściwość elastyczności wystąpienia. |
| instanceId | InstanceName |  |
|  | Rodzaj | Jest to nowa właściwość. Jej wartość to `None`, `Reservation` lub `IncludedQuantity`. |
| reservationId | ReservationId |  |
| reservationOrderId | ReservationOrderId |  |
| reservedHours | ReservedHours |  |
| skuName | SkuName |  |
| totalReservedQuantity | TotalReservedQuantity |  |
| usageDate | UsageDate |  |
| usedHours | UsedHours |  |

### <a name="reserved-instance-usage-summary"></a>Podsumowanie użycia wystąpień zarezerwowanych

Użyj następujących identyfikatorów URI żądania, aby wywołać nowy interfejs API podsumowań rezerwacji.

#### <a name="supported-requests"></a>Obsługiwane żądania

 Wywołaj interfejs API z następującymi zakresami:

- Rejestracja: `providers/Microsoft.Billing/billingAccounts/{billingAccountId}`

[_Pobierz podsumowanie rezerwacji codziennie_](/rest/api/consumption/reservationssummaries/list#reservationsummariesdailywithbillingaccountid)

```json
https://management.azure.com/{scope}/Microsoft.Consumption/reservationSummaries?grain=daily&$filter=properties/usageDate ge 2017-10-01 AND properties/usageDate le 2017-11-20&api-version=2019-10-01
```

[_Pobierz podsumowanie rezerwacji miesięcznie_](/rest/api/consumption/reservationssummaries/list#reservationsummariesmonthlywithbillingaccountid)

```json
https://management.azure.com/{scope}/Microsoft.Consumption/reservationSummaries?grain=daily&$filter=properties/usageDate ge 2017-10-01 AND properties/usageDate le 2017-11-20&api-version=2019-10-01
```

#### <a name="response-body-changes"></a>Zmiany treści odpowiedzi

_Stara odpowiedź_:

```json
[
     {
        "reservationOrderId": "00000000-0000-0000-0000-000000000000",
        "reservationId": "00000000-0000-0000-0000-000000000000",
        "skuName": "Standard_F1s",
        "reservedHours": 24,
        "usageDate": "2018-05-01T00:00:00",
        "usedHours": 23,
        "minUtilizationPercentage": 0,
        "avgUtilizationPercentage": 95.83,
        "maxUtilizationPercentage": 100
    }
]

```

_Nowa odpowiedź_:

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/12345/providers/Microsoft.Consumption/reservationSummaries/reservationSummaries_Id1",
      "name": "reservationSummaries_Id1",
      "type": "Microsoft.Consumption/reservationSummaries",
      "tags": null,
      "properties": {
        "reservationOrderId": "00000000-0000-0000-0000-000000000000",
        "reservationId": "00000000-0000-0000-0000-000000000000",
        "skuName": "Standard_B1s",
        "reservedHours": 720,
        "usageDate": "2018-09-01T00:00:00-07:00",
        "usedHours": 0,
        "minUtilizationPercentage": 0,
        "avgUtilizationPercentage": 0,
        "maxUtilizationPercentage": 0
      }
    }
  ]
}

```

### <a name="reserved-instance-recommendations"></a>Rekomendacje wystąpień zarezerwowanych

Użyj następujących identyfikatorów URI żądania, aby wywołać nowy interfejs API rekomendacji dotyczących rezerwacji.

#### <a name="supported-requests"></a>Obsługiwane żądania

 Wywołaj interfejs API z następującymi zakresami:

- Rejestracja: `providers/Microsoft.Billing/billingAccounts/{billingAccountId}`
- Subskrypcje: `subscriptions/{subscriptionId}`
- Grupy zasobów: `subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}`

[_Uzyskaj rekomendacje_](/rest/api/consumption/reservationrecommendations/list)

W tym interfejsie API dostępne są rekomendacje dotyczące zarówno udostępnionego, jak i pojedynczego zakresu. Filtrowanie zakresu jako opcjonalny parametr interfejsu API również jest możliwe.

```json
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/123456/providers/Microsoft.Consumption/reservationRecommendations?api-version=2019-10-01
```

#### <a name="response-body-changes"></a>Zmiany treści odpowiedzi

Rekomendacje dotyczące udostępnionych i pojedynczych zakresów są połączone w jeden interfejs API.

_Stara odpowiedź_:

```json
[{
    "subscriptionId": "1111111-1111-1111-1111-111111111111",
    "lookBackPeriod": "Last7Days",
    "meterId": "2e3c2132-1398-43d2-ad45-1d77f6574933",
    "skuName": "Standard_DS1_v2",
    "term": "P1Y",
    "region": "westus",
    "costWithNoRI": 186.27634908960002,
    "recommendedQuantity": 9,
    "totalCostWithRI": 143.12931642978083,
    "netSavings": 43.147032659819189,
    "firstUsageDate": "2018-02-19T00:00:00"
}
]

```

_Nowa odpowiedź_:

```json
{
  "value": [
    {
      "id": "billingAccount/123456/providers/Microsoft.Consumption/reservationRecommendations/00000000-0000-0000-0000-000000000000",
      "name": "00000000-0000-0000-0000-000000000000",
      "type": "Microsoft.Consumption/reservationRecommendations",
      "location": "westus",
      "sku": "Standard_DS1_v2",
      "kind": "legacy",
      "properties": {
        "meterId": "00000000-0000-0000-0000-000000000000",
        "term": "P1Y",
        "costWithNoReservedInstances": 12.0785105,
        "recommendedQuantity": 1,
        "totalCostWithReservedInstances": 11.4899644807748,
        "netSavings": 0.588546019225182,
        "firstUsageDate": "2019-07-07T00:00:00-07:00",
        "scope": "Shared",
        "lookBackPeriod": "Last7Days",
        "instanceFlexibilityRatio": 1,
        "instanceFlexibilityGroup": "DSv2 Series",
        "normalizedSize": "Standard_DS1_v2",
        "recommendedQuantityNormalized": 1,
        "skuProperties": [
          {
            "name": "Cores",
            "value": "1"
          },
          {
            "name": "Ram",
            "value": "1"
          }
        ]
      }
    },
   ]
}

```

### <a name="reserved-instance-charges"></a>Opłaty za wystąpienie zarezerwowane

Użyj następujących identyfikatorów URI żądania, aby wywołać nowy interfejs API opłat za wystąpienie zarezerwowane.

#### <a name="supported-requests"></a>Obsługiwane żądania

[_Pobierz opłaty za rezerwację według zakresu dat_](/rest/api/consumption/reservationtransactions/list)

```json
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/providers/Microsoft.Consumption/reservationTransactions?$filter=properties/eventDate+ge+2020-05-20+AND+properties/eventDate+le+2020-05-30&api-version=2019-10-01
```

#### <a name="response-body-changes"></a>Zmiany treści odpowiedzi

_Stara odpowiedź_:

```json
[
    {
        "purchasingEnrollment": "string",
        "armSkuName": "Standard_F1s",
        "term": "P1Y",
        "region": "eastus",
        "PurchasingsubscriptionGuid": "00000000-0000-0000-0000-000000000000",
        "PurchasingsubscriptionName": "string",
        "accountName": "string",
        "accountOwnerEmail": "string",
        "departmentName": "string",
        "costCenter": "",
        "currentEnrollment": "string",
        "eventDate": "string",
        "reservationOrderId": "00000000-0000-0000-0000-000000000000",
        "description": "Standard_F1s eastus 1 Year",
        "eventType": "Purchase",
        "quantity": int,
        "amount": double,
        "currency": "string",
        "reservationOrderName": "string"
    }
]

```
_Nowa odpowiedź_:

```json
{
  "value": [
    {
      "id": "/billingAccounts/123456/providers/Microsoft.Consumption/reservationtransactions/201909091919",
      "name": "201909091919",
      "type": "Microsoft.Consumption/reservationTransactions",
      "tags": {},
      "properties": {
        "eventDate": "2019-09-09T19:19:04Z",
        "reservationOrderId": "00000000-0000-0000-0000-000000000000",
        "description": "Standard_DS1_v2 westus 1 Year",
        "eventType": "Cancel",
        "quantity": 1,
        "amount": -21,
        "currency": "USD",
        "reservationOrderName": "Transaction-DS1_v2",
        "purchasingEnrollment": "123456",
        "armSkuName": "Standard_DS1_v2",
        "term": "P1Y",
        "region": "westus",
        "purchasingSubscriptionGuid": "11111111-1111-1111-1111-11111111111",
        "purchasingSubscriptionName": "Infrastructure Subscription",
        "accountName": "Microsoft Infrastructure",
        "accountOwnerEmail": "admin@microsoft.com",
        "departmentName": "Unassigned",
        "costCenter": "",
        "currentEnrollment": "123456",
        "billingFrequency": "recurring"
      }
    },
  ]
}

```

## <a name="use-additional-cost-management-apis"></a>Używanie dodatkowego interfejsu API usługi Cost Management

Po przeprowadzeniu migracji do interfejsu API usługi Azure Resource Manager dla istniejących scenariuszy raportowania można również użyć wielu innych interfejsów API. Interfejsy API są również dostępne w usłudze Azure Resource Manager i można je zautomatyzować przy użyciu uwierzytelniania opartego na jednostce usługi. Poniżej przedstawiono krótkie podsumowanie nowych funkcji, których można użyć.

- [Budżety](/rest/api/consumption/budgets/createorupdate) — służy do ustawiania progów umożliwiających aktywne monitorowanie kosztów, zgłaszanie alertów właściwym uczestnikom projektu i automatyzowanie akcji w odpowiedzi na naruszenia progów.

- [Alerty](/rest/api/cost-management/alerts) — służy do wyświetlania m.in. informacji o alertach, w tym alertów budżetu, alertów dotyczących faktur, alertów dotyczących środków i alertów dotyczących limitów przydziału.

- [Eksporty](/rest/api/cost-management/exports) — służy do planowania cyklicznego eksportu danych dotyczących opłat na wybrane konto usługi Azure Storage. Jest to zalecane rozwiązanie dla klientów z dużą obecnością platformy Azure, którzy chcą analizować swoje dane i używać ich we własnych systemach wewnętrznych.

## <a name="next-steps"></a>Następne kroki

- Zapoznaj się z [Interfejsami API REST usługi Azure Resource Manager](/rest/api/azure).
- W razie potrzeby ustal, których interfejsów API umowy EA używasz, i zobacz, które interfejsy API usługi Azure Resource Manager przenieść zapoznając się z artykułem [Mapowanie interfejsu API umowy EA do nowych interfejsów API usługi Azure Resource Manager](#ea-api-mapping-to-new-azure-resource-manager-apis).
- Jeśli jeszcze nie używasz interfejsów API usługi Azure Resource Manager, [zarejestruj aplikację kliencką w usłudze Azure AD](/rest/api/azure/#register-your-client-application-with-azure-ad).
- W razie potrzeby zaktualizuj wszelki kod, aby korzystać z [uwierzytelniania usługi Azure AD](/rest/api/azure/#create-the-request) przy użyciu jednostki usługi.