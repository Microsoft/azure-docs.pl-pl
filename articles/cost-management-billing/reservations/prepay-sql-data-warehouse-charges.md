---
title: Oszczędź na opłatach za usługę Azure Synapse Analytics dzięki pojemności zarezerwowanej platformy Azure
description: Dowiedz się, w jaki sposób zaoszczędzić na opłatach za usługę Azure Synapse Analytics w ramach pojemności zarezerwowanej.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 07/24/2020
ms.author: banders
ms.openlocfilehash: bd43b668c318b825c5c5b6f36fc1da1055863bed
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/20/2021
ms.locfileid: "98599092"
---
# <a name="save-costs-for-azure-synapse-analytics-charges-with-reserved-capacity"></a>Oszczędź na opłatach za usługę Azure Synapse Analytics dzięki pojemności zarezerwowanej

Możesz oszczędzać pieniądze na usłudze Azure Synapse Analytics, zobowiązując się do użycia zarezerwowanych jednostek cDWU przez okres roku lub trzech lat. Aby kupić zarezerwowaną pojemność usługi Azure Synapse Analytics, musisz wybrać region świadczenia usługi Azure oraz okres. Następnie trzeba dodać do koszyka jednostkę SKU usługi Azure Synapse Analytics i wybrać liczbę jednostek cDWU do kupienia.

Gdy wykupisz rezerwację, za użycie usługi Azure Synapse Analytics zgodne z atrybutami rezerwacji nie będą naliczane opłaty po stawkach płatności zgodnie z rzeczywistym użyciem.

Rezerwacja nie pokrywa kosztów magazynu i sieci wynikających z użycia usługi Azure Synapse Analytics.

Gdy zarezerwowana pojemność wygaśnie, wystąpienia usługi Azure Synapse Analytics dalej będą działać, ale będą za nie naliczane opłaty po stawkach płatności zgodnie z rzeczywistym użyciem. Rezerwacje nie są odnawiane automatycznie.

Aby uzyskać informacje o cenach, zobacz [ofertę dotyczącą zarezerwowanej pojemności usługi Azure Synapse Analytics](https://azure.microsoft.com/pricing/details/synapse-analytics/).

Zarezerwowaną pojemność usługi Azure Synapse Analytics można kupić w witrynie [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade). Płatność za rezerwację jest wnoszona [z góry lub w ratach miesięcznych](./prepare-buy-reservation.md). Aby kupić pojemność zarezerwowaną:

- Musisz mieć rolę właściciela dla co najmniej jednej subskrypcji Enterprise lub z opcją płatności zgodnie z rzeczywistym użyciem.
- W przypadku subskrypcji Enterprise w witrynie [EA portal](https://ea.azure.com/) musi być włączona opcja **Dodaj wystąpienia zarezerwowane**. Jeśli to ustawienie jest wyłączone, musisz być administratorem EA.
- W przypadku programu Cloud Solution Provider (CSP) wydajność rezerwową usługi Azure Synapse Analytics mogą kupić tylko agenci administracyjni lub agenci sprzedaży.

Aby uzyskać więcej informacji na temat sposobu, w jaki klienci korzystający z umowy Enterprise i klienci z opcją płatności zgodnie z rzeczywistym użyciem są obciążani za zakupy rezerwacji, zobacz [opis użycia rezerwacji platformy Azure w przypadku rejestracji Enterprise](understand-reserved-instance-usage-ea.md) i [opis użycia rezerwacji platformy Azure w przypadku subskrypcji z opcją płatności zgodnie z rzeczywistym użyciem](understand-reserved-instance-usage.md).

## <a name="choose-the-right-size-before-purchase"></a>Wybieranie odpowiedniego rozmiaru przed zakupem

Rozmiar rezerwacji usługi Azure Synapse Analytics powinien opierać się na całkowitej liczbie zużywanych obliczeniowych jednostek magazynu danych (cDWU). Kupuje się wielokrotność ilości 100 cDWU.

Załóżmy na przykład, że całkowite zużycie usługi Azure Synapse Analytics jest na poziomie DW3000c. Chcesz zakupić zarezerwowaną pojemność dla całego zużycia. W związku z tym należy zakupić 30 jednostek zarezerwowanej pojemności cDWU.

## <a name="buy-azure-synapse-analytics-reserved-capacity"></a>Kupowanie pojemności zarezerwowanej usługi Azure Synapse Analytics

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Wybierz pozycję **Wszystkie usługi** > **Rezerwacje**.
3. Wybierz subskrypcję. Z listy Subskrypcja wybierz subskrypcję, w ramach której jest opłacana zarezerwowana pojemność. Kosztami zarezerwowanej pojemności jest obciążana forma płatności subskrypcji. Wymagany typ subskrypcji to Enterprise Agreement (numery ofert: MS-AZR-0017P lub MS-AZR-0148P) albo Płatność zgodnie z rzeczywistym użyciem (numery ofert: MS-AZR-0003P lub MS-AZR-0023P).
   - W przypadku subskrypcji dla przedsiębiorstw opłaty są odliczane od salda opłaty z góry za platformę Azure (wcześniej nazywanej zobowiązaniem pieniężnym) rejestracji lub naliczane jako nadwyżka.
   - W przypadku subskrypcji z płatnością zgodnie z rzeczywistym użyciem opłaty obciążają kartę kredytową lub metodę płatności faktury powiązaną z subskrypcją.
4. Wybierz zakres. Użyj listy Zakres w celu wybrania zakresu subskrypcji.
   - **Zakres pojedynczej grupy zasobów** — rabat na rezerwację jest stosowany do odpowiednich zasobów tylko w wybranej grupie zasobów.
   - **Zakres pojedynczej subskrypcji** — rabat na rezerwację jest stosowany do odpowiednich zasobów w wybranej subskrypcji.
   - **Zakres udostępniony** — rabat na rezerwację jest stosowany do odpowiednich zasobów w kwalifikujących się subskrypcjach w ramach kontekstu rozliczeń. W przypadku klientów korzystających z umowy Enterprise Agreement kontekstem rozliczeń jest rejestracja. W przypadku indywidualnych subskrypcji ze stawkami płatności zgodnie z rzeczywistym użyciem kontekst rozliczeń stanowią wszystkie kwalifikujące się subskrypcje utworzone przez administratora konta.
   - W przypadku klientów z subskrypcją Enterprise kontekstem rozliczeń jest rejestracja umowy EA.
   - W przypadku klientów z płatnością zgodnie z rzeczywistym użyciem zakresem udostępnionym są wszystkie subskrypcje z opcją płatności zgodnie z rzeczywistym użyciem utworzone przez administratora konta.
5. Wybierz region, aby wskazać region świadczenia usługi Azure, którego ma dotyczyć zarezerwowana pojemność.
6. Wybierz ilość. Wprowadź liczbę wskazującą wielokrotność 100 jednostek magazynu danych (cDWU), którą chcesz kupić.    
   Na przykład liczba 30 zapewni pojemność zarezerwowaną wynoszącą 3000 jednostek cDWU w każdej godzinie.
7. Zapoznaj się z kosztem rezerwacji zarezerwowanej pojemności usługi Azure Synapse Analytics w sekcji **Koszty**.
8. Wybierz pozycję **Kup**.
9. Wybierz pozycję **Wyświetl tę rezerwację**, aby wyświetlić stan zakupu.

## <a name="cancel-exchange-or-refund-reservations"></a>Anulowanie, wymiana lub zwrot rezerwacji

Rezerwacje można anulować, wymieniać lub zwracać, jednak obowiązują przy tym pewne ograniczenia. Aby uzyskać więcej informacji, zobacz temat [Self-service exchanges and refunds for Azure Reservations](exchange-and-refund-azure-reservations.md) (Samoobsługowe wymiany i zwroty kosztów dla rezerwacji platformy Azure).

Rabat na rezerwację jest automatycznie stosowany do tych wystąpień usługi Azure Synapse Analytics, które pasują do zakresu i regionu zarezerwowanej pojemności usługi Azure Synapse Analytics. Zakres zarezerwowanej pojemności usługi Azure Synapse Analytics można zaktualizować za pomocą witryny [Azure Portal](https://portal.azure.com/), programu PowerShell, interfejsu wiersza polecenia lub interfejsu API.

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami

Jeśli masz pytania lub potrzebujesz pomocy, [utwórz wniosek o pomoc techniczną](https://portal.azure.com/).

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej na temat sposobu stosowania rabatów na rezerwację do usługi Azure Synapse Analytics, zobacz artykuł [Stosowanie rabatów na rezerwacje usługi Azure Synapse Analytics](prepay-sql-data-warehouse-charges.md).

- Aby dowiedzieć się więcej na temat rezerwacji platformy Azure, zobacz następujące artykuły:
  - [Co to jest Azure Reservations?](save-compute-costs-reservations.md)
  - [Zarządzanie usługą Azure Reservations](manage-reserved-vm-instance.md)
  - [Informacje na temat rabatu na rezerwacje platformy Azure](understand-reservation-charges.md)
  - [Understand reservation usage for your Pay-As-You-Go subscription (Informacje na temat użycia wystąpień zarezerwowanych w przypadku subskrypcji z płatnością zgodnie z rzeczywistym użyciem)](understand-reserved-instance-usage.md)
  - [Understand reservation usage for your Enterprise enrollment (Informacje na temat użycia wystąpień zarezerwowanych w przypadku rejestracji Enterprise)](understand-reserved-instance-usage-ea.md)