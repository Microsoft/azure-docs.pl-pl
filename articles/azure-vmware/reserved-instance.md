---
title: Oszczędność kosztów dzięki zarezerwowanemu wystąpieniu rozwiązania VMware platformy Azure
description: Dowiedz się, jak kupić wystąpienie zarezerwowane dla rozwiązań VMware platformy Azure.
ms.topic: how-to
ms.date: 10/02/2020
ms.openlocfilehash: bac2497c637a301c7ce8cbc44fc6945c3ef43b06
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/22/2020
ms.locfileid: "92370682"
---
# <a name="save-costs-with-azure-vmware-solution"></a>Oszczędność kosztów dzięki rozwiązaniu VMware platformy Azure

Po zatwierdzeniu do zarezerwowanego wystąpienia [rozwiązania Azure VMware](introduction.md)możesz zaoszczędzić pieniądze. Rabat rezerwacji jest automatycznie stosowany do uruchomionych hostów rozwiązań VMware platformy Azure, które pasują do zakresu rezerwacji i atrybutów. Nie musisz przypisywać rezerwacji do dedykowanego hosta, aby uzyskać rabaty. Zakup wystąpienia zarezerwowanego obejmuje tylko część obliczeniową użycia i zawiera koszty licencjonowania oprogramowania. 


## <a name="purchase-restriction-considerations"></a>Zagadnienia związane z ograniczeniami zakupu

Wystąpienia zarezerwowane są dostępne z pewnymi wyjątkami.

-   **Chmury** — rezerwacje są dostępne tylko w regionach wymienionych na stronie [dostępne produkty według regionów](https://azure.microsoft.com/global-infrastructure/services/?products=azure-vmware) .

-   **Niewystarczające limity przydziału** — w ramach subskrypcji dla nowego wystąpienia zarezerwowanego w ramach rezerwacji należącej do jednej lub udostępnionej subskrypcji musi być dostępny przydział hostów. Aby rozwiązać ten problem, można [utworzyć żądanie zwiększenia limitu przydziału](enable-azure-vmware-solution.md) .

-   **Oferta do oferty**— będziesz potrzebować [platformy Azure Umowa Enterprise (EA)](../cost-management-billing/manage/ea-portal-agreements.md) z firmą Microsoft.

-   **Ograniczenia pojemności** — w rzadkich przypadkach platforma Azure ogranicza zakup nowych rezerwacji dla jednostek SKU hosta rozwiązań VMware platformy Azure z powodu niskiej wydajności w regionie.

## <a name="buy-a-reservation"></a>Kupowanie rezerwacji

Możesz kupić wystąpienie zarezerwowane wystąpienia hosta rozwiązania VMware platformy Azure w [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22VirtualMachines%22%7D).

Płatność za rezerwację jest wnoszona [z góry lub w ratach miesięcznych](../cost-management-billing/reservations/prepare-buy-reservation.md).

Te wymagania dotyczą kupowania zastrzeżonego dedykowanego wystąpienia hosta:

-   Musisz mieć rolę właściciela dla co najmniej jednej subskrypcji EA lub subskrypcji z stawką płatność zgodnie z rzeczywistym użyciem.

-   W przypadku subskrypcji z umową EA należy włączyć opcję **Dodaj wystąpienia zarezerwowane** w [portalu EA](https://ea.azure.com/). Jeśli to ustawienie jest wyłączone, wymagane są uprawnienia administratora EA dla subskrypcji.

Aby kupić wystąpienie:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

2. Wybierz pozycję **Wszystkie usługi** > **Rezerwacje**.

3. Wybierz pozycję **Dodaj** , aby zakupić nową rezerwację, a następnie wybierz pozycję **Azure VMware Solution**.

4. Wprowadź wymagane pola. W przypadku uruchamiania hostów rozwiązań VMware platformy Azure, które pasują do atrybutów, wybierz pozycję kwalifikuj, aby uzyskać rabat rezerwacji. Rzeczywista liczba hostów rozwiązań VMware platformy Azure, które uzyskują rabat w zależności od wybranego zakresu i ilości.

   Jeśli masz umowę EA, możesz użyć **opcji Dodaj więcej** , aby szybko dodać kolejne wystąpienia. Opcja jest niedostępna dla innych typów subskrypcji.

   | Pole        |  Opis |
   | ------------ | ------------ |
   | Subskrypcja | Subskrypcja używana do płacenia za rezerwację. Kosztami rezerwacji jest obciążana forma płatności za subskrypcję. Typ subskrypcji musi być umową Enterprise Agreement (Numer oferty: MS-AZR-0017P lub MS-AZR-0148P) lub umowa klienta firmy Microsoft lub indywidualna subskrypcja z stawką płatność zgodnie z rzeczywistym użyciem (numery ofert: MS-AZR-0003P lub MS-AZR-0023P). Opłaty są odliczane od salda zobowiązania pieniężnego (jeśli jest dostępne) lub naliczane jako nadwyżka. W przypadku subskrypcji z płatnością zgodnie z rzeczywistym użyciem opłaty są naliczane za kartę kredytową lub formę płatności faktury dla subskrypcji. |
   | Zakres        | Zakres rezerwacji może obejmować jedną subskrypcję lub wiele subskrypcji (zakres udostępniony). W przypadku wybrania opcji:<br><ul><li><b>Zakres pojedynczej grupy zasobów — stosuje rabat rezerwacji do zasobów pasujących tylko w wybranej grupie zasobów.</li><li><b>Zakres pojedynczej subskrypcji — stosuje rabat rezerwacji do pasujących zasobów w wybranej subskrypcji.</li><li><b>Zakres udostępniony — stosuje rabat rezerwacji do pasujących zasobów w uprawnionych subskrypcjach, które znajdują się w kontekście rozliczeń. W przypadku klientów z umowami EA kontekst rozliczania to rejestracja. W przypadku indywidualnych subskrypcji ze stawkami płatności zgodnie z rzeczywistym użyciem kontekst rozliczeń stanowią wszystkie kwalifikujące się subskrypcje utworzone przez administratora konta.</li></ul>       |
   | Region       | Region świadczenia usługi Azure objęty rezerwacją.   |
   | Rozmiar hosta    | AV36    |
   | Termin         | Jeden rok lub trzy lata.  |
   | Liczba     | Liczba wystąpień zakupionych w ramach rezerwacji. Ilość to liczba uruchomionych hostów rozwiązań VMware platformy Azure, które mogą uzyskać rabat rozliczeń.    |

## <a name="usage-data-and-reservation-usage"></a>Dane użycia i użycie rezerwacji

Użycie, które pobiera rabat rezerwacji, ma efektywną cenę równą zero. Możesz zobaczyć, które wystąpienie rozwiązania Azure VMware otrzymało rabat rezerwacji dla każdej rezerwacji.

Aby uzyskać więcej informacji na temat sposobu wyświetlania rabatów rezerwacji w danych użycia:

- W przypadku klientów z umowami EA zapoznaj się z tematem [Korzystanie z usługi Azure Reservation na potrzeby rejestracji przedsiębiorstwa](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
- W przypadku poszczególnych subskrypcji zobacz [Opis użycia usługi Azure Reservation dla subskrypcji z opcją płatność zgodnie z rzeczywistym użyciem](../cost-management-billing/reservations/understand-reserved-instance-usage.md)

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
> - Jednostka SKU
> - Liczba
> - Czas trwania
>
>Istnieje jednak możliwość *wymiany* rezerwacji, jeśli chcesz wprowadzić zmiany.

## <a name="cancel-exchange-or-refund-reservations"></a>Anulowanie, wymiana lub zwrot rezerwacji

Rezerwacje można anulować, wymieniać lub zwracać, jednak obowiązują przy tym pewne ograniczenia. Aby uzyskać więcej informacji, zobacz temat [Self-service exchanges and refunds for Azure Reservations](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md) (Samoobsługowe wymiany i zwroty kosztów dla rezerwacji platformy Azure).