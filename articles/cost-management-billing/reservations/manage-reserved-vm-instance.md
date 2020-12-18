---
title: Zarządzanie rezerwacjami platformy Azure
description: Dowiedz się, jak zarządzać rezerwacjami platformy Azure. Zobacz kroki zmieniania zakresu rezerwacji, dzielenia rezerwacji i optymalizowania wykorzystania rezerwacji.
ms.service: cost-management-billing
ms.subservice: reservations
author: bandersmsft
ms.reviewer: yashesvi
ms.topic: how-to
ms.date: 12/08/2020
ms.author: banders
ms.openlocfilehash: 2cd0611d5701f5ca407afd6d4e3b1b0ae22b6c12
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97562977"
---
# <a name="manage-reservations-for-azure-resources"></a>Zarządzanie rezerwacjami zasobów platformy Azure

Po zakupieniu rezerwacji platformy Azure może być konieczne zastosowanie rezerwacji do innej subskrypcji, zmiana osoby, która może zarządzać rezerwacją, lub zmiana zakresu rezerwacji. Rezerwację możesz również podzielić na dwie rezerwacje, aby zastosować niektóre kupione wystąpienia w innej subskrypcji.

Jeśli zakupiono zarezerwowane wystąpienia maszyn wirtualnych na platformie Azure, możesz zmienić ustawienie optymalizacji dla rezerwacji. Rabat związany z rezerwacją może być stosowany do maszyn wirtualnych w tej samej serii lub można zarezerwować pojemność centrum danych dla określonego rozmiaru maszyny wirtualnej. Należy spróbować zoptymalizować rezerwacje, aby były w pełni używane.

*Uprawnienia potrzebne do zarządzania rezerwacją są oddzielne od uprawnień do subskrypcji.*

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="reservation-order-and-reservation"></a>Zamówienie rezerwacji i rezerwacja

Podczas zakupu rezerwacji są tworzone dwa obiekty: **Zamówienie rezerwacji** i **Rezerwacja**.

W momencie zakupu zamówienie rezerwacji ma jedną rezerwację. Akcje, takie jak dzielenie, scalanie, częściowy zwrot lub wymiana, tworzą nowe rezerwacje w obszarze **zamówienia rezerwacji**.

Aby wyświetlić zamówienie rezerwacji, przejdź do pozycji **Rezerwacje** > wybierz rezerwację, a następnie wybierz pozycję **identyfikator zamówienia rezerwacji**.

![Przykład szczegółów zamówienia rezerwacji z wyróżnionym identyfikatorem zamówienia rezerwacji ](./media/manage-reserved-vm-instance/reservation-order-details.png)

Rezerwacja dziedziczy uprawnienia z zamówienia rezerwacji.

## <a name="change-the-reservation-scope"></a>Zmienianie zakresu rezerwacji

 Rabat związany z rezerwacją jest stosowany do maszyn wirtualnych, baz danych SQL, usługi Azure Cosmos DB lub innych zasobów, które są zgodne z rezerwacją i działają w jej zakresie. Kontekst rozliczeń zależy od subskrypcji użytej do zakupu rezerwacji.

Aby zaktualizować zakres rezerwacji:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz pozycję **Wszystkie usługi** > **Rezerwacje**.
3. Wybierz rezerwację.
4. Wybierz pozycję **Ustawienia** > **Konfiguracja**.
5. Zmień zakres.

W przypadku zmiany z zakresu udostępnionego na pojedynczy możesz wybrać tylko subskrypcje, w których jesteś właścicielem. Wybrać można tylko subskrypcje w ramach tego samego kontekstu rozliczeń, co rezerwacja.

Zakres ma zastosowanie tylko do pojedynczych subskrypcji płatności zgodnie z rzeczywistym użyciem (oferty MS-AZR-0003P lub MS-AZR-0023P), ofert Enterprise MS-AZR-0017P lub MS-AZR-0148P albo typów subskrypcji dostawcy rozwiązań w chmurze.

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
    - W przypadku umowy Enterprise Agreement dodaj użytkowników z rolą _Administrator przedsiębiorstwa_, aby wyświetlać wszystkie zamówienia rezerwacji dotyczące umowy Enterprise Agreement i zarządzać nimi. Użytkownicy z rolą _Administrator przedsiębiorstwa (tylko do odczytu_) mogą jedynie wyświetlać rezerwację. Administratorzy działu i właściciele kont nie mogą wyświetlać rezerwacji, _o ile_ nie zostali do nich jawnie dodani przy użyciu funkcji kontroli dostępu (IAM). Aby uzyskać więcej informacji, zobacz [Zarządzanie rolami w usłudze Azure Enterprise](../manage/understand-ea-roles.md).

        _Administratorzy przedsiębiorstwa mogą przejąć na własność zamówienie rezerwacji i dodać do rezerwacji innych użytkowników przy użyciu funkcji kontroli dostępu (IAM)._
    - W przypadku umowy klienta firmy Microsoft użytkownicy z rolą właściciela profilu rozliczeniowego lub współautora profilu rozliczeniowego mogą zarządzać wszystkimi zakupami rezerwacji dokonanymi przy użyciu profilu rozliczeniowego. Czytelnicy profilów rozliczeniowych i menedżerowie faktur mogą wyświetlać wszystkie rezerwacje opłacane za pomocą profilu rozliczeniowego. Jednak nie mogą wprowadzać zmian w rezerwacjach.
    Aby uzyskać więcej informacji, zobacz [Zadania i role profilu rozliczeniowego](../manage/understand-mca-roles.md#billing-profile-roles-and-tasks).

### <a name="how-billing-administrators-view-or-manage-reservations"></a>Jak administratorzy rozliczeń wyświetlają rezerwacje lub zarządzają nimi

1. Przejdź do obszaru **Zarządzanie kosztami i rozliczenia**, a następnie po lewej stronie wybierz pozycję **Transakcje rezerwacji**.
2. Jeśli masz wymagane uprawnienia do rozliczania, możesz wyświetlać rezerwacje i zarządzać nimi. Jeśli nie widzisz żadnych rezerwacji, zaloguj się przy użyciu dzierżawy usługi Azure AD, w której zostały utworzone rezerwacje.

## <a name="split-a-single-reservation-into-two-reservations"></a>Dzielenie pojedynczej rezerwacji na dwie rezerwacje

 Po zakupieniu więcej niż jednego wystąpienia zasobu w ramach rezerwacji możesz zdecydować się na przypisanie wystąpień w ramach tej rezerwacji do różnych subskrypcji. Domyślnie wszystkie wystąpienia mają jeden zakres — pojedynczą subskrypcję, grupę zasobów lub subskrypcję udostępnioną. Załóżmy, że zakupiono rezerwację dla 10 wystąpień maszyn wirtualnych i określono zakres jako subskrypcję A. Teraz chcesz zmienić zakres, tak aby obejmował siedem wystąpień maszyn wirtualnych w subskrypcji A i trzy w subskrypcji B. Można to zrobić dzięki funkcji dzielenia rezerwacji. Po rozdzieleniu rezerwacji oryginalny identyfikator rezerwacji jest anulowany i tworzone są dwie nowe rezerwacje. Ten podział nie ma wpływu na zamówienie rezerwacji — nie ma żadnej nowej podzielonej transakcji komercyjnej, a nowe rezerwacje mają tę samą datę zakończenia co rozdzielona rezerwacja.

 Rezerwację można podzielić na dwie rezerwacje za pomocą programu PowerShell, interfejsu wiersza polecenia lub interfejsu API.

### <a name="split-a-reservation-by-using-powershell"></a>Dzielenie rezerwacji przy użyciu programu PowerShell

1. Pobierz identyfikator zamówienia rezerwacji, uruchamiając następujące polecenie:

    ```powershell
    # Get the reservation orders you have access to
    Get-AzReservationOrder
    ```

2. Pobierz szczegóły rezerwacji:

    ```powershell
    Get-AzReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId b8be062a-fb0a-46c1-808a-5a844714965a
    ```

3. Podziel rezerwację na dwie rezerwacje i dystrybuuj wystąpienia:

    ```powershell
    # Split the reservation. The sum of the reservations, the quantity, must equal the total number of instances in the reservation that you're splitting.
    Split-AzReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId b8be062a-fb0a-46c1-808a-5a844714965a -Quantity 3,2
    ```
4. Aby zaktualizować zakres, możesz uruchomić następujące polecenie:

    ```powershell
    Update-AzReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId 5257501b-d3e8-449d-a1ab-4879b1863aca -AppliedScopeType Single -AppliedScope /subscriptions/15bb3be0-76d5-491c-8078-61fe3468d414
    ```

## <a name="cancel-exchange-or-refund-reservations"></a>Anulowanie, wymiana lub zwrot rezerwacji

Rezerwacje można anulować, wymieniać lub zwracać, jednak obowiązują przy tym pewne ograniczenia. Aby uzyskać więcej informacji, zobacz temat [Self-service exchanges and refunds for Azure Reservations](exchange-and-refund-azure-reservations.md) (Samoobsługowe wymiany i zwroty kosztów dla rezerwacji platformy Azure).

## <a name="change-optimize-setting-for-reserved-vm-instances"></a>Zmienianie ustawienia optymalizacji dla wystąpień zarezerwowanych maszyn wirtualnych

 Podczas zakupu wystąpienia zarezerwowanego maszyny wirtualnej wybierasz elastyczność rozmiaru wystąpienia lub priorytet pojemności. Elastyczność wystąpienia maszyny wirtualnej powoduje zastosowanie rabatu związanego z rezerwacją do innych maszyn wirtualnych w tej samej [grupie rozmiarów maszyn wirtualnych](../../virtual-machines/reserved-vm-instance-size-flexibility.md). Priorytet pojemności wyznacza pojemność centrum danych najważniejszą dla wdrożeń. Ta opcja oferuje dodatkową gwarancję możliwości uruchamiania wystąpień maszyn wirtualnych, gdy ich potrzebujesz.

Domyślnie, gdy zakres rezerwacji jest współużytkowany, elastyczność rozmiaru wystąpienia jest włączona. W przypadku wdrożeń maszyn wirtualnych nie ma priorytetu wydajności centrum danych.

W przypadku rezerwacji z pojedynczym zakresem można zoptymalizować rezerwację na potrzeby priorytetu pojemności zamiast elastyczności rozmiaru wystąpienia maszyny wirtualnej.

Aby zaktualizować ustawienie optymalizacji dla rezerwacji:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz pozycję **Wszystkie usługi** > **Rezerwacje**.
3. Wybierz rezerwację.
4. Wybierz pozycję **Ustawienia** > **Konfiguracja**.
  ![Przykład pokazujący element Konfiguracja](./media/manage-reserved-vm-instance/add-product03.png)
5. Zmień ustawienie **Optymalizuj dla**.
  ![Przykład pokazujący ustawienie Optymalizuj dla](./media/manage-reserved-vm-instance/instance-size-flexibility-option.png)

## <a name="optimize-reservation-use"></a>Optymalizowanie użycia rezerwacji

Oszczędności związane z rezerwacjami platformy Azure wynikają jedynie ze stałego użycia zasobów. Podczas dokonywania zakupu rezerwacji zasadniczo płacisz 100% możliwego użycia zasobów w okresie jednego roku lub trzech lat. Staraj się zmaksymalizować rezerwację, aby uzyskać jak najwięcej użycia i oszczędności. W poniższych sekcjach wyjaśniono, jak monitorować rezerwację i optymalizować jej użycie.

### <a name="view-reservation-use-in-the-azure-portal"></a>Wyświetlanie użycia rezerwacji w witrynie Azure Portal

Jeden ze sposobów wyświetlania użycia rezerwacji jest dostępny w witrynie Azure Portal.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Wybierz pozycję **Wszystkie usługi** > [**Rezerwacje**](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade) i zwróć uwagę na wartość **Wykorzystanie (%)** dla rezerwacji.  
  ![Obraz przedstawiający listę rezerwacji](./media/manage-reserved-vm-instance/reservation-list.png)
3. Wybierz rezerwację.
4. Przejrzyj trend użycia rezerwacji w czasie.
  ![Obraz przedstawiający użycie rezerwacji](./media/manage-reserved-vm-instance/reservation-utilization-trend.png)

### <a name="view-reservation-use-with-api"></a>Wyświetlanie użycia rezerwacji przy użyciu interfejsu API

Jeśli jesteś klientem z umową Enterprise Agreement (EA), możesz programowo zobaczyć, w jaki sposób rezerwacje są używane w organizacji. Nieużywane rezerwacje są uzyskiwane za pośrednictwem danych użycia. Przeglądając opłaty za rezerwację, pamiętaj, że dane są dzielone między kosztami rzeczywistymi i amortyzowanymi. Koszt rzeczywisty zawiera dane wymagane do rozliczenia rachunku miesięcznego. Obejmuje również koszty zakupu rezerwacji oraz szczegóły zastosowania rezerwacji. Amortyzowany koszt jest podobny do kosztu rzeczywistego, z tą różnicą, że efektywna cena użycia rezerwacji jest naliczana proporcjonalnie. Niewykorzystane godziny rezerwacji są wyświetlane w danych kosztu amortyzowanego. Aby uzyskać więcej informacji na temat danych użycia dla klientów z umowami EA, zobacz [Get Enterprise Agreement reservation costs and usage](understand-reserved-instance-usage-ea.md) (Pobieranie kosztów i użycia rezerwacji w ramach umowy Enterprise Agreement).

W przypadku innych typów subskrypcji użyj interfejsu API [Podsumowania rezerwacji — lista według zamówienia rezerwacji i rezerwacji](/rest/api/consumption/reservationssummaries/listbyreservationorderandreservation).

### <a name="optimize-your-reservation"></a>Optymalizowanie rezerwacji

Jeśli okaże się, że rezerwacje w organizacji są używane w niewystarczającym stopniu:

- Upewnij się, że maszyny wirtualne tworzone przez organizację są zgodne z rozmiarem maszyny wirtualnej dla rezerwacji.
- Upewnij się, że włączono funkcję elastyczności rozmiaru wystąpienia. Aby uzyskać więcej informacji, zobacz [Zarządzanie rezerwacjami — zmienianie ustawienia optymalizacji dla wystąpień zarezerwowanych maszyn wirtualnych](#change-optimize-setting-for-reserved-vm-instances).
- Zmień zakres rezerwacji na _udostępniony_, aby była ona stosowana w szerszym zakresie. Aby uzyskać więcej informacji, zobacz [Zmienianie zakresu rezerwacji](#change-the-reservation-scope).
- Rozważ wymianę niewykorzystanej ilości. Aby uzyskać więcej informacji, zobacz [Anulowania i wymiany](#cancel-exchange-or-refund-reservations).


## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami.

Jeśli masz pytania lub potrzebujesz pomocy, [utwórz wniosek o pomoc techniczną](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat rezerwacji platformy Azure, zobacz następujące artykuły:

- [Co to są rezerwacje platformy Azure?](save-compute-costs-reservations.md)

Kup plan usługi:
- [Prepay for Virtual Machines with Azure Reserved VM Instances (Opłacanie maszyn wirtualnych z góry przy użyciu usługi Azure Reserved VM Instances)](../../virtual-machines/prepay-reserved-vm-instances.md)
- [Prepay for SQL Database compute resources with Azure SQL Database reserved capacity (Opłacanie zasobów obliczeniowych usługi SQL Database z góry przy użyciu zarezerwowanej pojemności usługi Azure SQL Database)](../../azure-sql/database/reserved-capacity-overview.md)
- [Opłacanie zasobów usługi Azure Cosmos DB z góry za pomocą zarezerwowanej pojemności usługi Azure Cosmos DB](../../cosmos-db/cosmos-db-reserved-capacity.md)

Kup plan oprogramowania:
- [Opłacanie planów oprogramowania Red Hat z góry z poziomu usługi Azure Reservations](../../virtual-machines/linux/prepay-suse-software-charges.md)
- [Prepay for SUSE software plans from Azure Reservations (Opłacanie planów oprogramowania SUSE z góry z poziomu usługi Azure Reservations)](../../virtual-machines/linux/prepay-suse-software-charges.md)

Informacje o rabacie i użyciu:
- [Understand how the reservation discount is applied (Jak jest stosowany rabat na rezerwacje maszyn wirtualnych)](../manage/understand-vm-reservation-charges.md)
- [Understand how the Red Hat Enterprise Linux software plan discount is applied (Informacje na temat sposobu stosowania rabatu na plan oprogramowania Red Hat Enterprise Linux)](understand-rhel-reservation-charges.md)
- [Understand how the SUSE Linux Enterprise software plan discount is applied (Informacje na temat sposobu stosowania rabatu na plan oprogramowania SUSE Linux Enterprise)](understand-suse-reservation-charges.md)
- [Understand how other reservation discounts are applied (Informacje na temat sposobu stosowania innych rabatów przy rezerwacji)](understand-reservation-charges.md)
- [Understand reservation usage for your Pay-As-You-Go subscription (Informacje na temat użycia wystąpień zarezerwowanych w przypadku subskrypcji z płatnością zgodnie z rzeczywistym użyciem)](understand-reserved-instance-usage.md)
- [Understand reservation usage for your Enterprise enrollment (Informacje na temat użycia wystąpień zarezerwowanych w przypadku rejestracji Enterprise)](understand-reserved-instance-usage-ea.md)
- [Windows software costs not included with Reservations (Koszty oprogramowania systemu Windows nieuwzględniane w przypadku rezerwacji)](reserved-instance-windows-software-costs.md)
