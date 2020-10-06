---
title: Interfejsy API na potrzeby automatyzacji rezerwacji platformy Azure | Microsoft Docs
description: Dowiedz się więcej na temat interfejsów API platformy Azure, których można używać do programowego pobierania informacji o rezerwacjach.
author: yashesvi
ms.reviewer: yashesvi
tags: billing
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: banders
ms.openlocfilehash: 12b20f7556a460506067305401fb2379678ee58e
ms.sourcegitcommit: 5dbea4631b46d9dde345f14a9b601d980df84897
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91371698"
---
# <a name="apis-for-azure-reservation-automation"></a>Interfejsy API na potrzeby automatyzacji rezerwacji platformy Azure

Używając interfejsów API platformy Azure, można programowo uzyskiwać informacje dotyczące rezerwacji oprogramowania lub usług platformy Azure dla organizacji.

## <a name="find-reservation-plans-to-buy"></a>Wyszukiwanie planów rezerwacji do zakupu

Za pomocą interfejsu API rekomendacji rezerwacji możesz uzyskiwać rekomendacje dotyczące zakupu planu rezerwacji na podstawie użycia w organizacji. Aby uzyskać więcej informacji, zobacz [Get reservation recommendations](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation) (Uzyskiwanie rekomendacji dotyczących rezerwacji).

Możesz również analizować użycie zasobów za pomocą szczegółów użycia interfejsu API zużycia. Aby uzyskać więcej informacji, zobacz [Usage Details - List For Billing Period By Billing Account](/rest/api/consumption/usagedetails/list#billingaccountusagedetailslistforbillingperiod-legacy) (Lista użycia — lista dla okresu rozliczeniowego według konta rozliczeniowego). Zasoby platformy Azure, z których stale korzystasz, są zwykle najlepszym kandydatem do rezerwacji.

## <a name="buy-a-reservation"></a>Kupowanie rezerwacji

Możesz kupować rezerwacje platformy Azure i plany oprogramowania programowo za pomocą interfejsów API REST. Aby dowiedzieć się więcej, zobacz [Reservation Order - Purchase API (Zamówienie rezerwacji — interfejs API zakupu)](/rest/api/reserved-vm-instances/reservationorder/purchase).

Oto przykładowe żądanie zakupu przy użyciu interfejsu API REST:

```
PUT https://management.azure.com/providers/Microsoft.Capacity/reservationOrders/<GUID>?api-version=2019-04-01
```

Treść żądania:

```
{
 "sku": {
    "name": "standard_D1"
  },
 "location": "westus",
 "properties": {
    "reservedResourceType": "VirtualMachines",
    "billingScopeId": "/subscriptions/ed3a1871-612d-abcd-a849-c2542a68be83",
    "term": "P1Y",
    "quantity": "1",
    "displayName": "TestReservationOrder",
    "appliedScopes": null,
    "appliedScopeType": "Shared",
    "reservedResourceProperties": {
      "instanceFlexibility": "On"
    }
  }
}
```

Rezerwację można również kupić w witrynie Azure Portal. Aby uzyskać więcej informacji zobacz następujące artykuły:

Plany usługi:
- [Maszyna wirtualna](../../virtual-machines/windows/prepay-reserved-vm-instances.md?toc=/azure/billing/TOC.json)
-  [Cosmos DB](../../cosmos-db/cosmos-db-reserved-capacity.md?toc=/azure/billing/TOC.json)
- [SQL Database](../../azure-sql/database/reserved-capacity-overview.md?toc=/azure/billing/TOC.json)

Plany oprogramowania:
- [Oprogramowanie SUSE Linux](../../virtual-machines/linux/prepay-suse-software-charges.md?toc=/azure/billing/TOC.json)

## <a name="get-reservations"></a>Pobieranie rezerwacji

Jeśli jesteś klientem platformy Azure z umową Enterprise Agreement (klient z umową EA), możesz uzyskać rezerwacje zakupione przez organizację za pomocą [interfejsu API pobierania opłat za transakcje dotyczące wystąpień zarezerwowanych](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges). W przypadku innych subskrypcji możesz uzyskać listę zakupionych rezerwacji, w przypadku których masz uprawnienia do wyświetlania, korzystając z interfejsu API [Zamówienie rezerwacji — lista](/rest/api/reserved-vm-instances/reservationorder/list). Domyślnie właściciel konta lub osoba, która zakupiła rezerwację, ma uprawnienia do wyświetlania rezerwacji.

## <a name="see-reservation-usage"></a>Wyświetlanie użycia rezerwacji

Jeśli jesteś klientem z umową EA, możesz programowo zobaczyć, w jaki sposób rezerwacje są używane w organizacji. Aby uzyskać więcej informacji, zobacz temat [Get Reserved Instance usage for enterprise customers](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) (Pobieranie użycia wystąpień zarezerwowanych dla klientów w przedsiębiorstwie). W przypadku innych subskrypcji użyj interfejsu API [Podsumowania rezerwacji — lista według zamówienia rezerwacji i rezerwacji](/rest/api/consumption/reservationssummaries/listbyreservationorderandreservation).

Jeśli okaże się, że rezerwacje w organizacji są używane w niewystarczającym stopniu:

- Upewnij się, że maszyny wirtualne tworzone przez organizację są zgodne z rozmiarem maszyny wirtualnej w rezerwacji.
- Upewnij się, że włączono funkcję elastyczności rozmiaru wystąpienia. Aby uzyskać więcej informacji, zobacz [Zarządzanie rezerwacjami — zmienianie ustawienia optymalizacji dla wystąpień zarezerwowanych maszyn wirtualnych](manage-reserved-vm-instance.md#change-optimize-setting-for-reserved-vm-instances).
- Zmień zakres rezerwacji na udostępniony, aby była ona stosowana w szerszym zakresie. Aby uzyskać więcej informacji, zobacz [Zarządzanie rezerwacjami — zmienianie zakresu rezerwacji](manage-reserved-vm-instance.md#change-the-reservation-scope).
- Wymień nieużywaną ilość. Aby uzyskać więcej informacji, zobacz [Zarządzanie rezerwacjami](manage-reserved-vm-instance.md).

## <a name="give-access-to-reservations"></a>Udzielanie dostępu do rezerwacji

Pobierz listę wszystkich rezerwacji, do których użytkownik ma dostęp, przy użyciu [interfejsu API Rezerwacja — operacja — lista](/rest/api/reserved-vm-instances/reservationorder/list). Aby programowo przyznać prawa dostępu do rezerwacji, zobacz jeden z następujących artykułów:

- [Dodawanie lub usuwanie przypisań ról platformy Azure przy użyciu interfejsu API REST](../../role-based-access-control/role-assignments-rest.md)
- [Dodawanie lub usuwanie przypisań ról platformy Azure przy użyciu programu Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md)
- [Dodawanie lub usuwanie przypisań ról platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure](../../role-based-access-control/role-assignments-cli.md)

## <a name="split-or-merge-reservation"></a>Dzielenie lub scalanie rezerwacji

Po zakupieniu więcej niż jednego wystąpienia zasobu w ramach rezerwacji możesz zdecydować się na przypisanie wystąpień w ramach tej rezerwacji do różnych subskrypcji. Zakres rezerwacji można zmienić tak, aby dotyczył wszystkich subskrypcji w ramach tego samego kontekstu rozliczeń. Jednak dla celów związanych z zarządzaniem kosztami lub budżetowaniem można zachować zakres jako „pojedyncza subskrypcja” i przypisać wystąpienia rezerwacji do określonej subskrypcji.

Aby podzielić rezerwację, użyj interfejsu API [Reservation - Split](/rest/api/reserved-vm-instances/reservation/split) (Rezerwacja — dzielenie). Rezerwację można także podzielić przy użyciu programu PowerShell. Aby uzyskać więcej informacji, zobacz temat [Zarządzanie rezerwacjami — dzielenie rezerwacji na dwie rezerwacje](manage-reserved-vm-instance.md#split-a-single-reservation-into-two-reservations).

Aby scalić dwie rezerwacje w jedną rezerwację, użyj interfejsu API [Rezerwacja — scalanie](/rest/api/reserved-vm-instances/reservation/merge).

## <a name="change-scope-for-a-reservation"></a>Zmienianie zakresu rezerwacji

Zakresem rezerwacji może być pojedyncza subskrypcja, pojedyncza grupa zasobów lub wszystkie subskrypcje w kontekście rozliczeń. Jeśli ustawisz zakres na pojedynczą subskrypcję lub pojedynczą grupę zasobów, rezerwacja zostanie dopasowana do uruchomionych zasobów w wybranej subskrypcji. Jeśli usuniesz lub przeniesiesz subskrypcję albo grupę zasobów, rezerwacja nie zostanie wykorzystana.  Jeśli ustawisz zakres jako udostępniony, platforma Azure dopasuje rezerwację do zasobów, które działają we wszystkich subskrypcjach w kontekście rozliczeń. Kontekst rozliczeń zależy od subskrypcji użytej do zakupu rezerwacji. Możesz wybrać zakres podczas dokonywania zakupu lub zmienić go w dowolnym momencie po zakupie. Aby uzyskać więcej informacji, zobacz [Zarządzanie rezerwacjami — zmienianie zakresu](manage-reserved-vm-instance.md#change-the-reservation-scope).

Aby programowo zmienić zakres, użyj interfejsu API [Rezerwacja — aktualizacja](/rest/api/reserved-vm-instances/reservation/update).

## <a name="learn-more"></a>Dowiedz się więcej

- [Co to są rezerwacje platformy Azure](save-compute-costs-reservations.md)
- [Understand how the reservation discount is applied (Jak jest stosowany rabat na rezerwacje maszyn wirtualnych)](../manage/understand-vm-reservation-charges.md)
- [Understand how the SUSE Linux Enterprise software plan discount is applied (Informacje na temat sposobu stosowania rabatu na plan oprogramowania SUSE Linux Enterprise)](understand-suse-reservation-charges.md)
- [Understand how other reservation discounts are applied (Informacje na temat sposobu stosowania innych rabatów przy rezerwacji)](understand-reservation-charges.md)
- [Understand reservation usage for your Pay-As-You-Go subscription (Informacje na temat użycia wystąpień zarezerwowanych w przypadku subskrypcji z płatnością zgodnie z rzeczywistym użyciem)](understand-reserved-instance-usage.md)
- [Understand reservation usage for your Enterprise enrollment (Informacje na temat użycia wystąpień zarezerwowanych w przypadku rejestracji Enterprise)](understand-reserved-instance-usage-ea.md)
- [Koszty oprogramowania systemu Windows nieuwzględniane w przypadku wystąpień zarezerwowanych](reserved-instance-windows-software-costs.md)
- [Rezerwacje platformy Azure w programie Cloud Solution Provider w Centrum partnerskim](https://docs.microsoft.com/partner-center/azure-reservations)
