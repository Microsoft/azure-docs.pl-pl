---
title: Wyświetlanie rezerwacji zasobów platformy Azure | Microsoft Docs
description: Dowiedz się, jak wyświetlać rezerwacje platformy Azure w witrynie Azure Portal. Wyświetlaj dane rezerwacji i wykorzystania przy użyciu interfejsów API, programu PowerShell, interfejsu wiersza polecenia i usługi Power BI.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 07/24/2020
ms.author: banders
ms.openlocfilehash: 926e0c8bc0fdff580b4d1e84be5940e0945d52ae
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2020
ms.locfileid: "92146270"
---
# <a name="view-azure-reservations"></a>Wyświetlanie rezerwacji platformy Azure

Zakupioną rezerwację możesz wyświetlić w witrynie Azure Portal, gdzie możesz też nią zarządzać.

## <a name="permissions-to-view-a-reservation"></a>Uprawnienia do wyświetlania rezerwacji

Aby wyświetlić rezerwację lub zarządzać nią, musisz mieć do niej uprawnienie czytelnika lub właściciela. Domyślnie przy zakupie rezerwacji osoba dokonująca zakupu oraz administrator konta automatycznie otrzymują rolę właściciela dla rezerwacji oraz zamówienia rezerwacji. Aby umożliwić innej osobie wyświetlanie rezerwacji, należy dodać tę osobę jako **Właściciela** lub **Czytelnika** w rezerwacji lub w zamówieniu rezerwacji. Dodanie osoby do subskrypcji podanej dla rozliczeń rezerwacji nie powoduje automatycznego dodania jej do rezerwacji. 

Aby uzyskać więcej informacji, zobacz [Dodawanie lub zmienianie użytkowników, którzy mogą zarządzać rezerwacją](manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation).

## <a name="view-reservation-and-utilization-in-the-azure-portal"></a>Wyświetlanie rezerwacji i użycia w witrynie Azure Portal

Aby wyświetlić rezerwację jako właściciel lub czytelnik:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Przejdź do pozycji [Rezerwacje](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade).
3. Lista zawiera wszystkie rezerwacje, dla których masz rolę właściciela lub czytelnika. Dla każdej rezerwacji pokazany jest ostatni znany procent użycia.
4. Kliknij pozycję procentu wykorzystania, aby wyświetlić historię wykorzystania i szczegóły. Wideo poniżej zawiera szczegółowe informacje.
   > [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4sYwk] 

## <a name="get-reservations-and-utilization-using-apis-powershell-cli"></a>Uzyskiwanie danych rezerwacji i wykorzystania przy użyciu interfejsów API, programu PowerShell, interfejsu wiersza polecenia

Pobieranie listy wszystkich rezerwacji przy użyciu następujących zasobów
- [Interfejs API: zamówienie rezerwacji — lista](/rest/api/reserved-vm-instances/reservationorder/list)
- [Program PowerShell: zamówienie rezerwacji — lista](/powershell/module/azurerm.reservations/get-azurermreservationorder)
- [Interfejs wiersza polecenia: zamówienie rezerwacji — lista](/cli/azure/reservations/reservation-order#az-reservations-reservation-order-list)

Dane dotyczące [wykorzystania rezerwacji](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) możesz także uzyskać za pomocą interfejsu API użycia wystąpienie zarezerwowanego. 

## <a name="see-reservations-and-utilization-in-power-bi"></a>Wyświetlanie rezerwacji i wykorzystania w usłudze Power BI

Dostępne są dwie opcje dla użytkowników usług Power BI
- Pakiet zawartości: Dane zakupów i wykorzystania rezerwacji są dostępne w [pakiecie zawartości łącznika Consumption Insights usługi Power BI](/power-bi/desktop-connect-azure-cost-management). Twórz potrzebne Ci raporty za pomocą tego pakietu zawartości. 
- Aplikacja Cost Management: Użyj [aplikacji Cost Management](https://appsource.microsoft.com/product/power-bi/costmanagement.azurecostmanagementapp) udostępniającej wstępnie utworzone raporty, które można dostosować.

## <a name="next-steps"></a>Następne kroki

- [Zarządzanie rezerwacjami platformy Azure](manage-reserved-vm-instance.md).
- [Understand reservation usage for your Pay-As-You-Go subscription (Informacje na temat użycia wystąpień zarezerwowanych w przypadku subskrypcji z płatnością zgodnie z rzeczywistym użyciem)](understand-reserved-instance-usage.md)
- [Understand reservation usage for your Enterprise enrollment (Informacje na temat użycia wystąpień zarezerwowanych w przypadku rejestracji Enterprise)](understand-reserved-instance-usage-ea.md)
- [Understand reservation usage for CSP subscriptions (Informacje na temat użycia wystąpień zarezerwowanych w przypadku rejestracji CSP)](/partner-center/azure-reservations)

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami

Jeśli masz pytania lub potrzebujesz pomocy, [utwórz wniosek o pomoc techniczną](https://go.microsoft.com/fwlink/?linkid=2083458).