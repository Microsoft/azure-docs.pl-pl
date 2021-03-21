---
title: Przykładowe zapytania dotyczące analizy programowanej
description: Te przykładowe zapytania umożliwiają programowe uzyskiwanie dostępu do komercyjnych danych analitycznych portalu Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 7d788448fb3f8a849f79e43fcb0737898f4c9e15
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102584013"
---
# <a name="sample-queries-for-programmatic-analytics"></a>Przykładowe zapytania dotyczące analizy programowanej

Ten artykuł zawiera przykładowe zapytania dotyczące komercyjnych zamówień, użycia i raportów firmy Microsoft. Za pomocą tych zapytań można wywołać punkt końcowy interfejsu API [kwerendy tworzenia raportu](analytics-programmatic-access.md#create-report-query-api) . W razie potrzeby można zmodyfikować wywołanie interfejsu API [kwerendy tworzenia raportu](analytics-programmatic-access.md#create-report-query-api) , aby dodać więcej kolumn, dostosować okres obliczeń i dodać warunki filtrowania. Obsługiwane okresy to sześć miesięcy (6 min), 12 miesięcy (12M) i niestandardowy okres.

Aby uzyskać szczegółowe informacje na temat nazw kolumn, atrybutów i opisów, zapoznaj się z następującymi tabelami:

- [Tabela szczegółów klienta](customer-dashboard.md#customer-details-table)
- [Tabela szczegółów zamówień](orders-dashboard.md#orders-details-table)
- [Tabela szczegółów użycia](usage-dashboard.md#usage-details-table)

## <a name="customers-report-queries"></a>Zapytania raportujące klientów

Te przykładowe zapytania dotyczą raportu Customers.

| **Opis zapytania** | **Przykładowe zapytanie** |
| --- | --- |
| Aktywni klienci partnera do wybranej daty | `SELECT DateAcquired,CustomerCompanyName,CustomerId FROM ISVCustomer WHERE IsActive = 1` |
| Pozostały klienci z partnerem do momentu wybrania wybranej daty | `SELECT DateAcquired,CustomerCompanyName,CustomerId FROM ISVCustomer WHERE IsActive = 0` |
| Lista nowych klientów z określonej lokalizacji geograficznej w ciągu ostatnich sześciu miesięcy | `SELECT DateAcquired,CustomerCompanyName,CustomerId FROM ISVCustomer WHERE DateAcquired <= ‘2020-06-30’ AND CustomerCountryRegion = ‘United States’` |
|||

## <a name="usage-report-queries"></a>Zapytania dotyczące raportów użycia

Te przykładowe zapytania dotyczą raportu użycia.

| **Opis zapytania** | **Przykładowe zapytanie** |
| --- | --- |
| Znormalizowane użycie maszyny wirtualnej (VM) dla typu licencji Marketplace "rozliczane za pomocą platformy Azure" dla ostatnich 6 min | `SELECT MonthStartDate, NormalizedUsage FROM ISVUsage WHERE MarketplaceLicenseType = ‘Billed Through Azure’ AND OfferType NOT IN (‘Azure Applications’, ‘SaaS’) TIMESPAN LAST_6_MONTHS` |
| Użycie nieprzetworzonej maszyny wirtualnej dla typu licencji Marketplace "rozliczane za pomocą platformy Azure" dla ostatnich 12M | `SELECT MonthStartDate, RawUsage FROM ISVUsage WHERE MarketplaceLicenseType = ‘Billed Through Azure’ AND OfferType NOT IN (‘Azure Applications’, ‘SaaS’) TIMESPAN LAST_1_YEAR` |
| Normalne użycie maszyn wirtualnych dla typu licencji Marketplace "Przenieś własną licencję" dla ostatnich 6 min | `SELECT MonthStartDate, NormalizedUsage FROM ISVUsage WHERE MarketplaceLicenseType = ‘Bring Your Own License’ AND OfferType NOT IN (‘Azure Applications’, ‘SaaS’) TIMESPAN LAST_6_MONTHS` |
| Użycie surowej maszyny wirtualnej dla typu licencji Marketplace "Przenieś własną licencję" dla ostatnich 6 min | `SELECT MonthStartDate, RawUsage FROM ISVUsage WHERE MarketplaceLicenseType = ‘Bring Your Own License’ AND OfferType NOT IN (‘Azure Applications’, ‘SaaS’) TIMESPAN LAST_6_MONTHS` |
| W oparciu o datę użytkowania, dzienne całkowite użycie i "szacowane rozszerzone opłaty (PC/CC)" dla płatnych planów dla ostatniego miesiąca | `SELECT UsageDate, NormalizedUsage, EstimatedExtendedChargePC FROM ISVUsage WHERE SKUBillingType = ‘Paid’ ORDER BY UsageDate DESC TIMESPAN LAST_MONTH` |
| W oparciu o datę użytkowania, codzienne całkowite użycie surowe i "szacowane rozszerzone opłaty (PC/CC)" dla płatnych planów dla ostatniego miesiąca | `SELECT UsageDate, RawUsage, EstimatedExtendedChargePC FROM ISVUsage WHERE SKUBillingType = ‘Paid’ ORDER BY UsageDate DESC TIMESPAN LAST\_MONTH` |
| W przypadku konkretnej nazwy oferty w przypadku maszyny wirtualnej o nazwie "rozliczane za pomocą platformy Azure" dla ostatnich 6 min | `SELECT OfferName, NormalizedUsage FROM ISVUsage WHERE MarketplaceLicenseType = ‘Billed Through Azure’ AND OfferName = ‘Example Offer Name’ TIMESPAN LAST_6_MONTHS` |
| Dla konkretnej nazwy oferty, zmierzone użycie dla ostatnich 6 min | `SELECT OfferName, MeteredUsage FROM ISVUsage WHERE OfferName = ‘Example Offer Name’ AND OfferType IN (‘SaaS’, ‘Azure Applications’) TIMESPAN LAST_6_MONTHS` |
|||

## <a name="orders-report-queries"></a>Zapytania dotyczące raportów zamówień

Te przykładowe zapytania dotyczą raportu Orders.

| **Opis zapytania** | **Przykładowe zapytanie** |
| --- | --- |
| Raport zamówień dla typu licencji platformy Azure jako "Enterprise" dla ostatnich 6 min | `SELECT OrderId, OrderPurchaseDate FROM ISVOrder WHERE AzureLicenseType = ‘Enterprise’ TIMESPAN LAST\_6\_MONTHS` |
| Raport zamówień dla typu licencji platformy Azure jako "płatność zgodnie z rzeczywistym użyciem" dla ostatnich 6 min | `SELECT OrderId, OrderPurchaseDate FROM ISVOrder WHERE AzureLicenseType = ‘Pay as You Go’ TIMESPAN LAST_6_MONTHS` |
| Raport zamówień dla określonej nazwy oferty dla ostatnich 6 min | `SELECT OrderId, OrderPurchaseDate FROM ISVOrder WHERE OfferName = ‘Example Offer Name’ TIMESPAN LAST_6_MONTHS` |
| Raport zamówień dla aktywnych zamówień dla ostatnich 6 min | `SELECT OrderId, OrderPurchaseDate FROM ISVOrder WHERE OrderStatus = ‘Active’ TIMESPAN LAST_6_MONTHS` |
| Raport zamówień dla anulowanych zamówień dla ostatnich 6 min | `SELECT OrderId, OrderPurchaseDate FROM ISVOrder WHERE OrderStatus = ‘Cancelled’ TIMESPAN LAST_6_MONTHS` |
|||

## <a name="next-steps"></a>Następne kroki

- [Interfejsy API do uzyskiwania dostępu do komercyjnych danych analitycznych portalu Marketplace](analytics-available-apis.md)
