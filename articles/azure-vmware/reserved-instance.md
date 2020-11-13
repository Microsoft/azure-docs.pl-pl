---
title: Oszczędność kosztów dzięki zarezerwowanemu wystąpieniu rozwiązania VMware platformy Azure
description: Dowiedz się, jak kupić wystąpienie zarezerwowane dla rozwiązań VMware platformy Azure.
ms.topic: how-to
ms.date: 11/12/2020
ms.openlocfilehash: b57e985068adabccecbbdb43dd11bcf6596bf422
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/13/2020
ms.locfileid: "94578969"
---
# <a name="save-costs-with-azure-vmware-solution"></a>Oszczędność kosztów dzięki rozwiązaniu VMware platformy Azure

Po zatwierdzeniu do zarezerwowanego wystąpienia [rozwiązania Azure VMware](introduction.md)możesz zaoszczędzić pieniądze.  Rabat rezerwacji jest automatycznie stosowany do uruchomionych hostów rozwiązań VMware platformy Azure, które pasują do zakresu rezerwacji i atrybutów. Zakup wystąpienia zarezerwowanego obejmuje tylko część obliczeniową użycia i zawiera koszty licencjonowania oprogramowania. 

## <a name="purchase-restriction-considerations"></a>Zagadnienia związane z ograniczeniami zakupu

Wystąpienia zarezerwowane są dostępne z pewnymi wyjątkami.

-   **Chmury** — rezerwacje są dostępne tylko w regionach wymienionych na stronie [dostępne produkty według regionów](https://azure.microsoft.com/global-infrastructure/services/?products=azure-vmware) .

-   **Niewystarczające limity przydziału** — w ramach subskrypcji dla nowego wystąpienia zarezerwowanego w ramach rezerwacji należącej do jednej lub udostępnionej subskrypcji musi być dostępny przydział hostów. Aby rozwiązać ten problem, można [utworzyć żądanie zwiększenia limitu przydziału](enable-azure-vmware-solution.md) .

-   **Oferta do oferty** — będziesz potrzebować [platformy Azure Umowa Enterprise (EA)](../cost-management-billing/manage/ea-portal-agreements.md) z firmą Microsoft.

-   **Ograniczenia pojemności** — w rzadkich przypadkach platforma Azure ogranicza zakup nowych rezerwacji dla jednostek SKU hosta rozwiązań VMware platformy Azure z powodu niskiej wydajności w regionie.

## <a name="buy-a-reservation"></a>Kupowanie rezerwacji

Możesz kupić wystąpienie zarezerwowane wystąpienia hosta rozwiązania VMware platformy Azure w [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22VirtualMachines%22%7D).

Płatność za rezerwację można uzyskać [z góry lub za miesięczne płatności](../cost-management-billing/reservations/prepare-buy-reservation.md).

Te wymagania dotyczą kupowania zastrzeżonego dedykowanego wystąpienia hosta:

-   Musisz mieć rolę *właściciela* dla co najmniej jednej subskrypcji EA lub subskrypcji z stawką płatność zgodnie z rzeczywistym użyciem.

-   W przypadku subskrypcji z umową EA należy włączyć opcję **Dodaj wystąpienia zarezerwowane** w [portalu EA](https://ea.azure.com/). Jeśli ta wartość jest wyłączona, należy być administratorem EA, aby można było ją włączyć.

-   W przypadku subskrypcji w ramach planu platformy Azure w ramach dostawcy rozwiązań w chmurze (CSP) partner musi zakupić zarezerwowane wystąpienia w Azure Portal dla klienta. 

### <a name="buy-reserved-instances-for-an-ea-subscription"></a>Kupowanie wystąpień zarezerwowanych dla subskrypcji EA

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

2. Wybierz pozycję **Wszystkie usługi** > **Rezerwacje**.

3. Wybierz pozycję **Kup teraz** , a następnie wybierz pozycję **Azure VMware rozwiązanie**.

4. Wprowadź wymagane pola. Wybrane atrybuty, które pasują do uruchomionych hostów rozwiązań VMware platformy Azure, kwalifikują się do rabatu rezerwacji.  Atrybuty obejmują jednostkę SKU, regiony (tam, gdzie ma to zastosowanie) i zakres. Zakres rezerwacji określa, gdzie wystąpią oszczędności z rezerwacji.

   Jeśli masz umowę EA, możesz użyć **opcji Dodaj więcej** , aby szybko dodać wystąpienia. Opcja jest niedostępna dla innych typów subskrypcji.

   | Pole        |  Opis |
   | ------------ | ------------ |
   | Subskrypcja | Subskrypcja używana do płacenia za rezerwację. Kosztami rezerwacji jest obciążana forma płatności za subskrypcję. Typ subskrypcji musi być umową Enterprise Agreement (Numer oferty: MS-AZR-0017P lub MS-AZR-0148P), umowę klienta firmy Microsoft lub indywidualna subskrypcja z stawką płatność zgodnie z rzeczywistym użyciem (numery ofert: MS-AZR-0003P lub MS-AZR-0023P). Opłaty są odliczane od salda zobowiązania pieniężnego (jeśli jest dostępne) lub naliczane jako nadwyżka. W przypadku subskrypcji z płatnością zgodnie z rzeczywistym użyciem opłaty są naliczane za kartę kredytową subskrypcji lub formę płatności faktury. |
   | Zakres        | Zakres rezerwacji może obejmować jedną subskrypcję lub wiele subskrypcji (zakres udostępniony). W przypadku wybrania opcji:<br><ul><li><b>Zakres pojedynczej grupy zasobów</b> — stosuje rabat rezerwacji do zasobów pasujących tylko w wybranej grupie zasobów.</li><li><b>Zakres pojedynczej subskrypcji</b> — stosuje rabat rezerwacji do pasujących zasobów w wybranej subskrypcji.</li><li><b>Zakres udostępniony</b> — stosuje rabat rezerwacji do pasujących zasobów w uprawnionych subskrypcjach, które znajdują się w kontekście rozliczeń. W przypadku klientów z umowami EA kontekst rozliczania to rejestracja. W przypadku indywidualnych subskrypcji ze stawkami płatności zgodnie z rzeczywistym użyciem kontekst rozliczeń stanowią wszystkie kwalifikujące się subskrypcje utworzone przez administratora konta.</li></ul>       |
   | Region (Region)       | Region świadczenia usługi Azure objęty rezerwacją.   |
   | Rozmiar hosta    | AV36    |
   | Termin         | Jeden rok lub trzy lata.  |
   | Ilość     | Liczba wystąpień do zakupu w ramach rezerwacji. Ilość to liczba uruchomionych hostów rozwiązań VMware platformy Azure, które mogą uzyskać rabat rozliczeń.    |

### <a name="buy-reserved-instances-for-a-csp-subscription"></a>Kupowanie wystąpień zarezerwowanych dla subskrypcji dostawcy CSP

Dostawcy usług kryptograficznych, którzy chcą zakupić wystąpienia zarezerwowane dla swoich klientów, muszą korzystać z procedury **administratora w imieniu** (AOBO) z [dokumentacji Centrum partnerskiego](https://docs.microsoft.com/partner-center/azure-plan-manage). Aby uzyskać więcej informacji, zobacz plik wideo [administratora w imieniu (AOBO)](https://channel9.msdn.com/Series/cspdev/Module-11-Admin-On-Behalf-Of-AOBO) .

1. Zaloguj się do [Centrum partnerskiego](https://partner.microsoft.com).

2. Wybierz **dostawcę CSP** , aby uzyskać dostęp do obszaru **Customers** .

3. Rozwiń węzeł szczegóły klienta i wybierz pozycję **Portal zarządzania Microsoft Azure**. 

   :::image type="content" source="media/reserved-instances/csp-partner-center-aobo.png" alt-text="Obszar klientów Centrum partnerskiego firmy Microsoft" lightbox="media/reserved-instances/csp-partner-center-aobo.png":::

4. W Azure Portal wybierz pozycję **wszystkie**  >  **rezerwacje** usług.

5. Wybierz pozycję **Kup teraz** , a następnie wybierz pozycję **Azure VMware rozwiązanie**.

   :::image type="content" source="media/reserved-instances/csp-buy-ri-azure-portal.png" alt-text="Rezerwacje Microsoft Azure Portal" lightbox="media/reserved-instances/csp-buy-ri-azure-portal.png":::

6. Wprowadź wymagane pola. Wybrane atrybuty, które pasują do uruchomionych hostów rozwiązań VMware platformy Azure, kwalifikują się do rabatu rezerwacji.  Atrybuty obejmują jednostkę SKU, regiony (tam, gdzie ma to zastosowanie) i zakres. Zakres rezerwacji określa, gdzie wystąpią oszczędności z rezerwacji.

   | Pole        |  Opis |
   | ------------ | ------------ |
   | Subskrypcja | Subskrypcja używana do płacenia za rezerwację. Kosztami rezerwacji jest obciążana forma płatności za subskrypcję. Typ subskrypcji musi być typem kwalifikującym się, co w tym przypadku jest subskrypcją dostawcy usług kryptograficznych.|
   | Zakres        | Zakres rezerwacji może obejmować jedną subskrypcję lub wiele subskrypcji (zakres udostępniony). W przypadku wybrania opcji:<br><ul><li><b>Zakres pojedynczej grupy zasobów</b> — stosuje rabat rezerwacji do zasobów pasujących tylko w wybranej grupie zasobów.</li><li><b>Zakres pojedynczej subskrypcji</b> — stosuje rabat rezerwacji do pasujących zasobów w wybranej subskrypcji.</li><li><b>Zakres udostępniony</b> — stosuje rabat rezerwacji do pasujących zasobów w uprawnionych subskrypcjach, które znajdują się w kontekście rozliczeń. W przypadku klientów z umowami EA kontekst rozliczania to rejestracja. W przypadku indywidualnych subskrypcji ze stawkami płatności zgodnie z rzeczywistym użyciem kontekst rozliczeń stanowią wszystkie kwalifikujące się subskrypcje utworzone przez administratora konta.</li></ul>       |
   | Region (Region)       | Region świadczenia usługi Azure objęty rezerwacją.   |
   | Rozmiar hosta    | AV36    |
   | Termin         | Jeden rok lub trzy lata.  |
   | Ilość     | Liczba wystąpień do zakupu w ramach rezerwacji. Ilość to liczba uruchomionych hostów rozwiązań VMware platformy Azure, które mogą uzyskać rabat rozliczeń.     |

Aby dowiedzieć się więcej na temat sposobu wyświetlania zakupionych rezerwacji dla klienta, zobacz temat [Wyświetlanie zastrzeżeń platformy Azure jako artykułu dostawcy rozwiązań w chmurze (CSP)](../cost-management-billing/reservations/how-to-view-csp-reservations.md) .

## <a name="usage-data-and-reservation-usage"></a>Dane użycia i użycie rezerwacji

Użycie, które pobiera rabat rezerwacji, ma efektywną cenę równą zero. Możesz zobaczyć, które wystąpienie rozwiązania Azure VMware otrzymało rabat rezerwacji dla każdej rezerwacji.

Aby uzyskać więcej informacji na temat sposobu wyświetlania rabatów rezerwacji w danych użycia:

- W przypadku klientów z umowami EA zapoznaj się z tematem [Korzystanie z usługi Azure Reservation na potrzeby rejestracji przedsiębiorstwa](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
- W przypadku poszczególnych subskrypcji zobacz [Opis użycia usługi Azure Reservation dla subskrypcji z opcją płatność zgodnie z rzeczywistym użyciem](../cost-management-billing/reservations/understand-reserved-instance-usage.md)

## <a name="change-a-reservation-after-purchase"></a>Zmiana rezerwacji po zakupie

Zmiany w rezerwacji można wprowadzić po zakupie:

-   Aktualizacja zakresu rezerwacji

-   Elastyczność rozmiaru wystąpienia (jeśli dotyczy)

-   Własność

Można również podzielić rezerwację na mniejsze fragmenty lub scalić rezerwacje. Żadna ze zmian nie powoduje nowej transakcji komercyjnej lub zmiana daty zakończenia rezerwacji.

Aby uzyskać szczegółowe informacje na temat rezerwacji zarządzanych przez dostawcę usług kryptograficznych, zobacz [sprzedawanie Microsoft Azure rezerwacji klientom przy użyciu Centrum partnerskiego, Azure Portal lub interfejsów API](https://docs.microsoft.com/partner-center/azure-reservations).



>[!NOTE]
>Po zakupieniu rezerwacji nie będzie można wprowadzać tych zmian bezpośrednio:
>
> - Istniejący region rezerwacji
> - Jednostka SKU
> - Ilość
> - Czas trwania
>
>Istnieje jednak możliwość *wymiany* rezerwacji, jeśli chcesz wprowadzić zmiany.

## <a name="cancel-exchange-or-refund-reservations"></a>Anulowanie, wymiana lub zwrot rezerwacji

Rezerwacje można anulować, wymieniać lub zwracać, jednak obowiązują przy tym pewne ograniczenia. Aby uzyskać więcej informacji, zobacz temat [Self-service exchanges and refunds for Azure Reservations](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md) (Samoobsługowe wymiany i zwroty kosztów dla rezerwacji platformy Azure).

Dostawcy usług kryptograficznych mogą anulować rezerwacje, wymienić je lub refundować, z pewnymi ograniczeniami zakupionymi dla klientów. Aby uzyskać więcej informacji, zobacz artykuł [dotyczący zarządzania zastrzeżeń, anulowania, wymiany lub Microsoft Azure zwrotu dla klientów](https://docs.microsoft.com/partner-center/azure-reservations-manage).