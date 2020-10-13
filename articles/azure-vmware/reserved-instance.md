---
title: Oszczędność kosztów dzięki zarezerwowanemu wystąpieniu rozwiązania VMware platformy Azure
description: Dowiedz się, jak kupić wystąpienie zarezerwowane dla rozwiązań VMware platformy Azure.
ms.topic: how-to
ms.date: 10/02/2020
ms.openlocfilehash: fa354d4fbfef868ea1e6783656be7871669f200d
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/12/2020
ms.locfileid: "91951421"
---
# <a name="save-costs-with-azure-vmware-solution"></a>Oszczędność kosztów dzięki rozwiązaniu VMware platformy Azure

Po zatwierdzeniu do zarezerwowanego wystąpienia rozwiązania VMware firmy Azure Możesz zaoszczędzić pieniądze. Rabat rezerwacji jest automatycznie stosowany do liczby uruchomionych hostów rozwiązań VMware platformy Azure, które pasują do zakresu rezerwacji i atrybutów. Nie musisz przypisywać rezerwacji do dedykowanego hosta, aby uzyskać rabaty. Zakup wystąpienia zarezerwowanego obejmuje tylko część obliczeniową użycia i zawiera koszty licencjonowania oprogramowania. Zapoznaj się z [omówieniem rozwiązania Azure VMware](introduction.md).

## <a name="purchase-restriction-considerations"></a>Zagadnienia związane z ograniczeniami zakupu

Wystąpienia zarezerwowane są dostępne z pewnymi wyjątkami.

-   **Chmury**   -Rezerwacje są dostępne tylko w regionach wymienionych na stronie [dostępne produkty według regionów](https://azure.microsoft.com/global-infrastructure/services/?products=azure-vmware) .

-   **Niewystarczające limity przydziału**   -Rezerwacja, która jest objęta zakresem pojedynczej lub udostępnionej subskrypcji, musi mieć przydziały hostów dostępne w subskrypcji dla nowego wystąpienia zarezerwowanego. Aby rozwiązać ten problem, można [utworzyć żądanie zwiększenia limitu przydziału](enable-azure-vmware-solution.md) .

-   **Oferta do oferty**— będziesz potrzebować [platformy Azure Umowa Enterprise (EA)](../cost-management-billing/manage/ea-portal-agreements.md)   z firmą Microsoft.

-   **Ograniczenia pojemności**   W rzadkich przypadkach platforma Azure ogranicza zakup nowych rezerwacji dla jednostek SKU hosta rozwiązań VMware platformy Azure z powodu niskiej wydajności w regionie.

## <a name="buy-a-reservation"></a>Kupowanie rezerwacji

Możesz kupić wystąpienie zarezerwowane wystąpienia hosta rozwiązania VMware platformy Azure w [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22VirtualMachines%22%7D).

Płatność za rezerwację z [góry lub miesięczna płatność](../cost-management-billing/reservations/prepare-buy-reservation.md).

Te wymagania dotyczą kupowania zastrzeżonego dedykowanego wystąpienia hosta:

-   Musisz mieć rolę właściciela dla co najmniej jednej subskrypcji EA lub subskrypcji z stawką płatność zgodnie z rzeczywistym użyciem.

-   W przypadku subskrypcji z umową EA należy włączyć opcję **Dodaj wystąpienia zarezerwowane**   w [portalu EA](https://ea.azure.com/). Jeśli to ustawienie jest wyłączone, wymagane są uprawnienia administratora EA dla subskrypcji.

Aby kupić wystąpienie:

1. Zaloguj się do witryny  [Azure Portal](https://portal.azure.com/).

2. Wybierz pozycję **wszystkie**   >  **rezerwacje**usług.

3. Wybierz pozycję **Dodaj**,   Aby zakupić nową rezerwację, a następnie wybierz pozycję **Azure VMware Solution**.

4. Podaj wartości w wymaganych polach. Uruchamianie hostów rozwiązań VMware platformy Azure pasujących do wybranych atrybutów kwalifikuje się do uzyskania rabatu rezerwacji. Rzeczywista liczba hostów rozwiązań VMware platformy Azure, które pobiera rabat, zależy od wybranego zakresu i ilości.

   Jeśli masz umowę EA, możesz użyć **opcji Dodaj więcej**,   Aby szybko dodać kolejne wystąpienia. Opcja jest niedostępna dla innych typów subskrypcji.

   | Pole        |  Opis |
   | ------------ | ------------ |
   | Subskrypcja | Subskrypcja używana do płacenia za rezerwację. Kosztami rezerwacji jest obciążana forma płatności za subskrypcję. Typ subskrypcji musi być umową Enterprise Agreement (Numer oferty: MS-AZR-0017P lub MS-AZR-0148P) lub umowa klienta firmy Microsoft lub indywidualna subskrypcja z stawką płatność zgodnie z rzeczywistym użyciem (numery ofert: MS-AZR-0003P lub MS-AZR-0023P). Opłaty są odliczane od salda zobowiązania pieniężnego (jeśli jest dostępne) lub naliczane jako nadwyżka. W przypadku subskrypcji z płatnością zgodnie z rzeczywistym użyciem opłaty są naliczane za kartę kredytową lub formę płatności faktury dla subskrypcji. |
   | Zakres        | Zakres rezerwacji może obejmować jedną subskrypcję lub wiele subskrypcji (zakres udostępniony). W przypadku wybrania opcji:<br><ul><li><b>Zakres pojedynczej grupy zasobów — stosuje rabat rezerwacji do zasobów pasujących tylko w wybranej grupie zasobów.</li><li><b>Zakres pojedynczej subskrypcji — stosuje rabat rezerwacji do pasujących zasobów w wybranej subskrypcji.</li><li><b>Zakres udostępniony — stosuje rabat rezerwacji do pasujących zasobów w uprawnionych subskrypcjach, które znajdują się w kontekście rozliczeń. W przypadku klientów z umowami EA kontekst rozliczania to rejestracja. W przypadku indywidualnych subskrypcji ze stawkami płatności zgodnie z rzeczywistym użyciem kontekst rozliczeń stanowią wszystkie kwalifikujące się subskrypcje utworzone przez administratora konta.</li></ul>       |
   | Region       | Region świadczenia usługi Azure objęty rezerwacją.   |
   | Rozmiar hosta    | AV36    |
   | Okres         | Jeden rok lub trzy lata.  |
   | Liczba     | Liczba wystąpień zakupionych w ramach rezerwacji. Ilość to liczba uruchomionych hostów rozwiązań VMware platformy Azure, które mogą uzyskać rabat rozliczeń.    |

## <a name="usage-data-and-reservation-utilization"></a>Dane użycia i użycie rezerwacji

Dane użycia mają wynikową cenę równą zero za użycie, którego dotyczy rabat rezerwacji. Możesz zobaczyć, które wystąpienie rozwiązania Azure VMware otrzymało rabat rezerwacji dla każdej rezerwacji.

Aby uzyskać więcej informacji na temat sposobu wyświetlania rabatów rezerwacji w danych użycia, a jesteś klientem z umową EA, zobacz [Opis użycia usługi Azure Reservation na potrzeby rejestracji w przedsiębiorstwie](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md). Jeśli masz pojedynczą subskrypcję, zobacz [Opis użycia usługi Azure Reservation dla subskrypcji z opcją płatność zgodnie z rzeczywistym użyciem](../cost-management-billing/reservations/understand-reserved-instance-usage.md).

## <a name="change-a-reservation-after-purchase"></a>Zmiana rezerwacji po zakupie

W zakupionej rezerwacji można wprowadzać następujące zmiany:

-   Aktualizacja zakresu rezerwacji

-   Elastyczność rozmiaru wystąpienia (jeśli dotyczy)

-   Własność

Można również podzielić rezerwację na mniejsze fragmenty lub scalić rezerwacje. Żadna ze zmian nie powoduje nowej transakcji komercyjnej lub zmiana daty zakończenia rezerwacji.

>[!NOTE]
>Po zakupieniu rezerwacji nie będzie można wprowadzać następujących typów zmian bezpośrednio:
>
> - Istniejący region rezerwacji
> - SKU
> - Liczba
> - Czas trwania
>
>Istnieje jednak możliwość *wymiany*   rezerwacji, jeśli chcesz wprowadzić zmiany.

## <a name="cancel-exchange-or-refund-reservations"></a>Anulowanie, wymiana lub zwrot rezerwacji

Rezerwacje można anulować, wymieniać lub zwracać, jednak obowiązują przy tym pewne ograniczenia. Aby uzyskać więcej informacji, zobacz samoobsługowe [weksle i zwroty dla Azure Reservations](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).