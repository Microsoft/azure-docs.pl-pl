---
title: Lista zapytań systemowych
description: Dowiedz się więcej o zapytaniach systemowych, których można użyć do programistycznego uzyskiwania danych analitycznych o ofertach w komercyjnym portalu Microsoft Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: f2b5f7eb559e349947f88067a3d2ea53d99b7cbf
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102584008"
---
# <a name="list-of-system-queries"></a>Lista zapytań systemowych

Następujące zapytania systemowe mogą być używane w [interfejsie API tworzenia raportu](analytics-programmatic-access.md#create-report-api) bezpośrednio z `QueryId` . Zapytania systemowe są podobne do raportów eksportu w centrum partnerskim przez okres obliczeń z sześciu miesięcy (6 min).

Aby uzyskać więcej informacji na temat nazw kolumn, atrybutów i opisów, zobacz następujące artykuły:

- [Pulpit nawigacyjny klientów w komercyjnej analizie Marketplace](customer-dashboard.md#customer-details-table)
- [Pulpit nawigacyjny Zamówienia w analizie komercyjnej platformy handlowej](orders-dashboard.md#orders-details-table)
- [Pulpit nawigacyjny użycia w komercyjnej analizie Marketplace](usage-dashboard.md#usage-details-table)
- [Pulpit nawigacyjny Analiza platformy handlowej w analizie komercyjnej platformy handlowej](insights-dashboard.md#marketplace-insights-details-table)

Poniższe sekcje zawierają kwerendy raportów dla różnych raportów.

## <a name="customers-report-query"></a>Zapytanie raportu klientów

**Opis raportu**: klienci raportu dla ostatnich 6 min

**QueryId**:  `b9df4929-073f-4795-b0cb-a2c81b11e28d`

**Zapytanie raportu**:

`SELECT MarketplaceSubscriptionId,DateAcquired,DateLost,ProviderName,ProviderEmail,FirstName,LastName,Email,CustomerCompanyName,CustomerCity,CustomerPostalCode,CustomerCommunicationCulture,CustomerCountryRegion,AzureLicenseType,PromotionalCustomers,CustomerState,CommerceRootCustomer,CustomerId,BillingAccountId,ID  FROM ISVCustomer TIMESPAN LAST_6_MONTHS`

## <a name="orders-report-query"></a>Zapytanie o raport zamówień

**Opis raportu**: Raport dotyczący zamówień dla ostatnich 6 min

**QueryId**:  `fd0f299c-5a1c-4929-9f48-bfc6cc44355d`

**Zapytanie raportu**:

`SELECT MarketplaceSubscriptionId,MonthStartDate,OfferType,AzureLicenseType,MarketplaceLicenseType,Sku,CustomerCountry,IsPreviewSKU,OrderId,OrderQuantity,CloudInstanceName,IsNewCustomer,OrderStatus,OrderCancelDate,CustomerCompanyName,CustomerName,OrderPurchaseDate,OfferName,TrialEndDate,CustomerId,BillingAccountId FROM ISVOrder TIMESPAN LAST_6_MONTHS`

## <a name="usage-report-queries"></a>Zapytania dotyczące raportów użycia

**Opis raportu**: raport użycia znormalizowanej maszyny wirtualnej dla ostatnich 6 min

**QueryId**:  `2c6f384b-ad52-4aed-965f-32bfa09b3778`

**Zapytanie raportu**:

`SELECT MarketplaceSubscriptionId,MonthStartDate,OfferType,AzureLicenseType,MarketplaceLicenseType,SKU,CustomerCountry,IsPreviewSKU,SKUBillingType,IsInternal,VMSize,CloudInstanceName,ServicePlanName,OfferName,DeploymentMethod,CustomerName,CustomerCompanyName,UsageDate,IsNewCustomer,CoreSize,TrialEndDate,CustomerCurrencyCC,PriceCC,PayoutCurrencyPC,EstimatedPricePC,UsageReference,UsageUnit,CustomerId,BillingAccountId,NormalizedUsage,EstimatedExtendedChargeCC,EstimatedExtendedChargePC FROM ISVUsage WHERE OfferType IN ('vm core image', 'Virtual Machine Licenses', 'multisolution') TIMESPAN LAST_6_MONTHS`

**Opis raportu**: Raport nieprzetworzonych użycia maszyny wirtualnej dla ostatnich 6 min

**QueryId**:  `3f19fb95-5bc4-4ee0-872e-cedd22578512`

**Zapytanie raportu**:

`SELECT MarketplaceSubscriptionId,MonthStartDate,OfferType,AzureLicenseType,MarketplaceLicenseType,SKU,CustomerCountry,IsPreviewSKU,SKUBillingType,IsInternal,VMSize,CloudInstanceName,ServicePlanName,OfferName,DeploymentMethod,CustomerName,CustomerCompanyName,UsageDate,IsNewCustomer,CoreSize,TrialEndDate,CustomerCurrencyCC,PriceCC,PayoutCurrencyPC,EstimatedPricePC,UsageReference,UsageUnit,CustomerId,BillingAccountId,RawUsage,EstimatedExtendedChargeCC,EstimatedExtendedChargePC FROM ISVUsage WHERE OfferType IN ('vm core image', 'Virtual Machine Licenses', 'multisolution') TIMESPAN LAST_6_MONTHS`

**Opis raportu**: raport użycia mierzony dla ostatnich 6 min

**QueryId**:  `f0c4927f-1f23-4c99-be4a-1371a5a9a086`

**Zapytanie raportu**:

`SELECT MarketplaceSubscriptionId,MonthStartDate,OfferType,AzureLicenseType,MarketplaceLicenseType,SKU,CustomerCountry,IsPreviewSKU,SKUBillingType,IsInternal,VMSize,CloudInstanceName,ServicePlanName,OfferName,DeploymentMethod,CustomerName,CustomerCompanyName,UsageDate,IsNewCustomer,CoreSize,TrialEndDate,CustomerCurrencyCC,PriceCC,PayoutCurrencyPC,EstimatedPricePC,UsageReference,UsageUnit,CustomerId,BillingAccountId,MeteredUsage,EstimatedExtendedChargeCC,EstimatedExtendedChargePC FROM ISVUsage WHERE OfferType IN ('SaaS', 'Azure Applications') TIMESPAN LAST_6_MONTHS`

## <a name="marketplace-insights-report-query"></a>Zapytanie o raport w portalu Marketplace

**Opis raportu**: Raport z analizy Marketplace dla ostatnich 6 min

**QueryId**:  `6fd7624b-aa9f-42df-a61d-67d42fd00e92`

**Zapytanie raportu**:

`Date,OfferName,ReferralDomain,CountryName,PageVisits,GetItNow,ContactMe,TestDrive,FreeTrial FROM ISVMarketplaceInsights TIMESPAN LAST_6_MONTHS`

## <a name="next-steps"></a>Następne kroki

- [Interfejsy API do uzyskiwania dostępu do komercyjnych danych analitycznych portalu Marketplace](analytics-available-apis.md)
- [Przykładowa aplikacja do uzyskiwania dostępu do komercyjnych danych analitycznych portalu Marketplace](analytics-sample-application.md)
