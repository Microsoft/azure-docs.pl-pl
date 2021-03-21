---
title: Wyświetlanie rezerwacji zasobów platformy Azure
description: Dowiedz się, jak wyświetlać rezerwacje platformy Azure w witrynie Azure Portal. Wyświetlaj dane rezerwacji i wykorzystania przy użyciu interfejsów API, programu PowerShell, interfejsu wiersza polecenia i usługi Power BI.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 03/17/2021
ms.author: banders
ms.openlocfilehash: 1c666602f764e8274cb2a30df204e97479c85ba3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104583058"
---
# <a name="view-azure-reservations"></a>Wyświetlanie rezerwacji platformy Azure

W tym artykule opisano, jak wyświetlać rezerwacje platformy Azure w witrynie Azure Portal. Zakupioną rezerwację możesz wyświetlić w witrynie Azure Portal, gdzie możesz też nią zarządzać.

## <a name="who-can-manage-a-reservation-by-default"></a>Kto może domyślnie zarządzać rezerwacją

Domyślnie rezerwacje mogą wyświetlać i zarządzać nimi następujący użytkownicy:

- Do zamówienia rezerwacji jest dodawana osoba, która kupuje rezerwację, i administrator konta subskrypcji rozliczeniowej używanej do zakupu rezerwacji.
- Administratorzy rozliczeń w umowie Enterprise Agreement i umowie klienta firmy Microsoft.

Jeśli chcesz umożliwić innym osobom zarządzanie rezerwacjami, masz dwie możliwości:

- Deleguj zarządzanie dostępem do indywidualnego zamówienia rezerwacji:
    1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
    1. Wybierz pozycję **Wszystkie usługi** > **Rezerwacja** w celu wyświetlenia listy rezerwacji, do których masz dostęp.
    1. Wybierz rezerwację, dla której chcesz delegować dostęp do innych użytkowników.
    1. W obszarze Szczegóły rezerwacji wybierz zamówienie rezerwacji.
    1. Wybierz pozycję **Kontrola dostępu (IAM)** .
    1. Wybierz pozycję **Dodaj przypisanie roli** > **Rola** > **Właściciel**. Jeśli chcesz nadać ograniczony dostęp, wybierz inną rolę.
    1. Wpisz adres e-mail użytkownika, którego chcesz dodać jako właściciela.
    1. Wybierz użytkownika, a następnie wybierz polecenie **Zapisz**.

- Dodaj użytkownika jako administratora rozliczeń do umowy Enterprise Agreement lub umowy klienta firmy Microsoft:
    - W przypadku umowy Enterprise Agreement dodaj użytkowników z rolą _Administrator przedsiębiorstwa_, aby wyświetlać wszystkie zamówienia rezerwacji dotyczące umowy Enterprise Agreement i zarządzać nimi. Administratorzy przedsiębiorstwa wyświetlają rezerwacje i zarządzają nimi w ramach **Cost Management i rozliczeń**, a nie **rezerwacji**. Użytkownicy z rolą _Administrator przedsiębiorstwa (tylko do odczytu_) mogą jedynie wyświetlać rezerwację. Administratorzy działu i właściciele kont nie mogą wyświetlać rezerwacji, _o ile_ nie zostali do nich jawnie dodani przy użyciu funkcji kontroli dostępu (IAM). Aby uzyskać więcej informacji, zobacz [Zarządzanie rolami w usłudze Azure Enterprise](../manage/understand-ea-roles.md).

        _Administratorzy przedsiębiorstwa mogą przejąć na własność zamówienie rezerwacji i dodać do rezerwacji innych użytkowników przy użyciu funkcji kontroli dostępu (IAM)._
    - W przypadku umowy klienta firmy Microsoft użytkownicy z rolą właściciela profilu rozliczeniowego lub współautora profilu rozliczeniowego mogą zarządzać wszystkimi zakupami rezerwacji dokonanymi przy użyciu profilu rozliczeniowego. Czytelnicy profilów rozliczeniowych i menedżerowie faktur mogą wyświetlać wszystkie rezerwacje opłacane za pomocą profilu rozliczeniowego. Jednak nie mogą wprowadzać zmian w rezerwacjach.
    Aby uzyskać więcej informacji, zobacz [Zadania i role profilu rozliczeniowego](../manage/understand-mca-roles.md#billing-profile-roles-and-tasks).

### <a name="how-billing-administrators-view-or-manage-reservations"></a>Jak administratorzy rozliczeń wyświetlają rezerwacje lub zarządzają nimi

Jeśli masz dostęp do rezerwacji lub zamówień rezerwacji z dostępem do usługi Azure RBAC, w przypadku przechodzenia do rezerwacji może być widoczny tylko podzbiór transakcji rezerwacji lub brak. Wykonaj następujące kroki, aby wyświetlić wszystkie rezerwacje i transakcje rezerwacji i zarządzać nimi.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) i przejdź do **Cost Management i rozliczeń**.
    - Jeśli jesteś administratorem EA, w menu po lewej stronie wybierz pozycję **zakresy rozliczeń** , a następnie na liście zakresów rozliczeń wybierz jeden z nich.
    - Jeśli jesteś właścicielem profilu rozliczeń umowy klienta firmy Microsoft, w menu po lewej stronie wybierz pozycję **Profile rozliczeń**. Na liście profilów rozliczeń wybierz jeden z nich.
1. W menu po lewej stronie wybierz pozycję **transakcje rezerwacji**. Zostanie wyświetlona lista transakcji rezerwacji.
1. Transparent u góry strony odczytuje *teraz Administratorzy rozliczeń mogą zarządzać rezerwacjami. Kliknij tutaj, aby zarządzać rezerwacjami.* Wybierz transparent.
1. Zostanie wyświetlona pełna lista rezerwacji dla rejestracji EA lub profilu rozliczeń.
1. Jeśli chcesz przejąć na własność rezerwację, wybierz ją. Następnie na stronie Ustawianie uprawnień wybierz pozycję **Udziel dostępu**. Masz dostęp właściciela do rezerwacji i zamówienia rezerwacji.

## <a name="view-reservation-and-utilization-in-the-azure-portal"></a>Wyświetlanie rezerwacji i użycia w witrynie Azure Portal

Aby wyświetlić rezerwację jako właściciel lub czytelnik:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Przejdź do pozycji [Rezerwacje](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade).
3. Lista zawiera wszystkie rezerwacje, w których masz rolę właściciela lub czytelnika. Dla każdej rezerwacji pokazany jest ostatni znany procent użycia.
4. Wybierz pozycję procentu wykorzystania, aby wyświetlić historię wykorzystania i szczegóły. Wideo poniżej zawiera szczegółowe informacje.
   > [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4sYwk] 

## <a name="get-reservations-and-utilization-using-apis-powershell-and-cli"></a>Uzyskiwanie danych rezerwacji i wykorzystania przy użyciu interfejsów API, programu PowerShell i interfejsu wiersza polecenia

Pobieranie listy wszystkich rezerwacji przy użyciu następujących zasobów:

- [Interfejs API: zamówienie rezerwacji — lista](/rest/api/reserved-vm-instances/reservationorder/list)
- [Program PowerShell: zamówienie rezerwacji — lista](/powershell/module/azurerm.reservations/get-azurermreservationorder)
- [Interfejs wiersza polecenia: zamówienie rezerwacji — lista](/cli/azure/reservations/reservation-order#az-reservations-reservation-order-list)

Dane dotyczące [wykorzystania rezerwacji](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) możesz także uzyskać za pomocą interfejsu API użycia wystąpienie zarezerwowanego. 

## <a name="see-reservations-and-utilization-in-power-bi"></a>Wyświetlanie rezerwacji i wykorzystania w usłudze Power BI

Dostępne są dwie opcje dla użytkowników usług Power BI
- Pakiet zawartości: Dane zakupów i dane wykorzystania rezerwacji są dostępne w [pakiecie zawartości łącznika Consumption Insights usługi Power BI](/power-bi/desktop-connect-azure-cost-management). Twórz potrzebne Ci raporty za pomocą pakietu zawartości. 
- Aplikacja Cost Management: Użyj [aplikacji Cost Management](https://appsource.microsoft.com/product/power-bi/costmanagement.azurecostmanagementapp) udostępniającej wstępnie utworzone raporty, które można dostosować.

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami

Jeśli masz pytania lub potrzebujesz pomocy, [utwórz wniosek o pomoc techniczną](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Następne kroki

- [Zarządzanie rezerwacjami platformy Azure](manage-reserved-vm-instance.md).
- [Understand reservation usage for your Pay-As-You-Go subscription (Informacje na temat użycia wystąpień zarezerwowanych w przypadku subskrypcji z płatnością zgodnie z rzeczywistym użyciem)](understand-reserved-instance-usage.md).
- [Understand reservation usage for your Enterprise enrollment (Informacje na temat użycia wystąpień zarezerwowanych w przypadku rejestracji Enterprise)](understand-reserved-instance-usage-ea.md).
- [Understand reservation usage for CSP subscriptions (Informacje na temat użycia wystąpień zarezerwowanych w przypadku rejestracji CSP)](/partner-center/azure-reservations).

