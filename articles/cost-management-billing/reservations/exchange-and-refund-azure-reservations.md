---
title: Samoobsługowe wymiany i zwroty kosztów rezerwacji platformy Azure
description: Dowiedz się, w jaki sposób można wymienić lub uzyskać zwrot kosztów rezerwacji platformy Azure.
author: yashesvi
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: banders
ms.openlocfilehash: 174ed17056bf49b541d55719f4058141e88e7ea5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82192114"
---
# <a name="self-service-exchanges-and-refunds-for-azure-reservations"></a>Samoobsługowe wymiany i zwroty kosztów dotyczące rezerwacji na platformie Azure

Rezerwacje platformy Azure zapewniają elastyczność, dzięki której można łatwiej sprostać Twoim zmieniającym się potrzebom. Rezerwację można wymienić na inną rezerwację tego samego typu. Jeśli rezerwacja nie jest już potrzebna, można uzyskać zwrot jej kosztów, maksymalnie 50 000 USD rocznie. Maksymalny limit zwrotu dotyczy wszystkich rezerwacji w zakresie umowy z firmą Microsoft.

Możliwości samoobsługowej wymiany i anulowania nie są dostępne dla klientów z umową Enterprise Agreement dla instytucji rządowych USA. Inne typy subskrypcji dla instytucji rządowych USA, w tym płatność zgodnie z rzeczywistym użyciem i dostawca CSP, są obsługiwane.

Aby wymienić istniejącą rezerwację lub uzyskać zwrot jej kosztów, musisz mieć dostęp właściciela do zamówienia rezerwacji.

## <a name="exchange-an-existing-reserved-instance"></a>Wymiana istniejącego wystąpienia zarezerwowanego

Możesz wymienić rezerwację, wykonując trzy krótkie kroki w witrynie [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade).

1. Wybierz rezerwacje, za które chcesz uzyskać zwrot kosztów, a następnie wybierz pozycję **Wymień**.  
    ![Przykładowy obraz przedstawiający rezerwacje, za które będą zwracane koszty](./media/exchange-and-refund-azure-reservations/exchange-refund-return.png)
2. Wybierz produkt maszyny wirtualnej, który chcesz kupić, a następnie wpisz ilość. Upewnij się, że nowa suma zakupu przekracza łączny zwrot. [Określ odpowiedni rozmiar przed zakupem](../../virtual-machines/windows/prepay-reserved-vm-instances.md#determine-the-right-vm-size-before-you-buy).  
    ![Przykładowy obraz przedstawiający produkt maszyny wirtualnej do zakupienia przy użyciu programu wymiany](./media/exchange-and-refund-azure-reservations/exchange-refund-select-purchase.png)
3. Przejrzyj i zakończ transakcję.  
    ![Przykładowy obraz przedstawiający produkt maszyny wirtualnej do zakupienia przy użyciu programu wymiany oraz dokończenie zwrotu](./media/exchange-and-refund-azure-reservations/exchange-refund-confirm-exchange.png)

Aby uzyskać zwrot kosztów rezerwacji, przejdź do obszaru **Szczegóły rezerwacji** i wybierz pozycję **Zwrot**.

## <a name="how-transactions-are-processed"></a>Sposób przetwarzania transakcji

Najpierw firma Microsoft anuluje istniejącą rezerwację i zwraca kwotę proporcjonalną do tej rezerwacji. Jeśli dochodzi do wymiany, jest przetwarzany nowy zakup. Firma Microsoft przetwarza zwroty przy użyciu jednej z następujących metod, w zależności od typu konta i formy płatności:

### <a name="enterprise-agreement-customers"></a>Klienci posiadający umowę Enterprise Agreement

Pieniądze są dodawane do zobowiązania pieniężnego na wymiany i zwroty w przypadku, gdy pierwotny zakup został utworzony przy jego użyciu. Wszystkie faktury nadwyżkowe od pierwotnych zakupów są ponownie otwierane i klasyfikowane w celu upewnienia się, że zobowiązanie pieniężne jest używane. Jeśli okres zobowiązania pieniężnego korzystający z rezerwacji, który został już zakupiony, nie jest już aktywny, do bieżącego okresu zobowiązania pieniężnego w ramach umowy Enterprise Agreement zostaną dodane środki. Środki są ważne przez 90 dni od daty zwrotu pieniędzy. Niewykorzystane środki wygasną po upływie 90 dni.

Jeśli zakup pierwotny został zrealizowany jako nadwyżka, firma Microsoft wystawi fakturę korygującą.

### <a name="pay-as-you-go-invoice-payments-and-csp-program"></a>Płatności za faktury zgodnie z rzeczywistym użyciem i program CSP

Oryginalna faktura zakupu rezerwacji zostanie anulowana, a następnie dla zwrotu zostanie utworzona nowa faktura. W przypadku wymiany nowa faktura przedstawia zwrot i nowy zakup. Kwota zwrotu jest dostosowywana do zakupu. W przypadku uzyskania jedynie zwrotu kosztów rezerwacji kwota proporcjonalna nadal obowiązuje dla firmy Microsoft i zostaje dostosowana do przyszłego zakupu rezerwacji.

### <a name="pay-as-you-go-credit-card-customers"></a>Klienci korzystający z karty kredytowej umożliwiającej płatność zgodnie z rzeczywistym użyciem

Faktura oryginalna zostaje anulowana i zostaje utworzona nowa faktura. Pieniądze zostaną zwrócone na kartę kredytową, która została użyta podczas pierwotnego zakupu. Jeśli Twoja karta została zmieniona, [skontaktuj się z pomocą techniczną](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="cancel-exchange-and-refund-policies"></a>Zasady dotyczące anulowania, wymiany i zwrotu

Platforma Azure ma następujące zasady dotyczące anulowania, wymiany i zwrotów.

**Zasady dotyczące wymiany**

- Można uzyskać zwrot kosztów wielu istniejących rezerwacji, aby zakupić nową rezerwację tego samego typu. Nie można wymieniać rezerwacji jednego typu na inny. Na przykład nie można zwrócić rezerwacji maszyny wirtualnej w celu zakupienia rezerwacji SQL.
- Tylko właściciele rezerwacji mogą przetwarzać wymianę. [Dowiedz się, jak dodawać lub zmieniać użytkowników, którzy mogą zarządzać rezerwacją](manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation).
- Wymiana jest przetwarzana jako zwrot i ponowny zakup — różne transakcje są tworzone dla anulowania i nowego zakupu. Proporcjonalna kwota rezerwacji jest zwracana w przypadku rezerwacji, w których zawarto wymianę. Nastąpi pełne obciążenie za nowy zakup. Proporcjonalna kwota rezerwacji to dzienna proporcjonalna pozostała wartość zwrotu kosztów rezerwacji.
- Możesz wymienić lub uzyskać zwrot kosztów rezerwacji, nawet jeśli umowa Enterprise Agreement użyta do zakupu rezerwacji wygasła i została odnowiona jako nowa umowa.
- Podczas wymiany możesz zmienić jakąś właściwość rezerwacji, na przykład rodzinę, serię, wersję, jednostkę SKU, region, ilość i okres.
- Nowa suma zakupu powinna być równa lub większa niż zwracana kwota.
- Nowa rezerwacja zakupiona w ramach wymiany ma nowy okres obowiązywania od momentu wymiany.
- Nie ma kar ani rocznych limitów wymian.

**Zasady dotyczące zwrotów**
- W przyszłości w przypadku anulowania może być naliczana opłata w wysokości 12% za wcześniejsze zakończenie. Obecnie ta kara nie jest naliczana.
- Łączna kwota zwrotu nie może przekroczyć 50 000 USD w 12-miesięcznym kroczącym przedziale czasu.
- Zwroty są obliczane na podstawie najniższej z dwóch kwoty: ceny zakupu i bieżącej ceny rezerwacji.
- Tylko właściciele zamówienia rezerwacji mogą przetwarzać zwrot. [Dowiedz się, jak dodawać lub zmieniać użytkowników, którzy mogą zarządzać rezerwacją](manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation).

## <a name="exchange-non-premium-storage-for-premium-storage"></a>Wymiana magazynu spoza warstwy Premium na magazyn w warstwie Premium

Rezerwację zakupioną dla rozmiaru maszyny wirtualnej, który nie obsługuje magazynu w warstwie Premium, można wymienić na odpowiedni rozmiar maszyny wirtualnej, który go obsługuje. Na przykład _F1_ na _F1s_. Aby dokonać wymiany, przejdź do Szczegółów rezerwacji i wybierz pozycję **Wymiana**. Wymiana nie resetuje okresu zarezerwowanego wystąpienia ani nie tworzy nowej transakcji.

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami.

Jeśli masz pytania lub potrzebujesz pomocy, [utwórz wniosek o pomoc techniczną](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się, jak zarządzać wystąpieniem zarezerwowanym, zobacz temat [Manage Azure Reservations](manage-reserved-vm-instance.md) (Zarządzanie wystąpieniami zarezerwowanymi na platformie Azure).
- Aby dowiedzieć się więcej na temat rezerwacji platformy Azure, zobacz następujące artykuły:
    - [Co to są rezerwacje platformy Azure?](save-compute-costs-reservations.md)
    - [Zarządzanie rezerwacjami na platformie Azure](manage-reserved-vm-instance.md)
    - [Jak jest stosowany rabat na rezerwacje](../manage/understand-vm-reservation-charges.md)
    - [Understand reservation usage for your Pay-As-You-Go subscription (Informacje na temat użycia wystąpień zarezerwowanych w przypadku subskrypcji z płatnością zgodnie z rzeczywistym użyciem)](understand-reserved-instance-usage.md)
    - [Understand reservation usage for your Enterprise enrollment (Informacje na temat użycia wystąpień zarezerwowanych w przypadku rejestracji Enterprise)](understand-reserved-instance-usage-ea.md)
    - [Koszty oprogramowania systemu Windows nieuwzględniane w przypadku wystąpień zarezerwowanych](reserved-instance-windows-software-costs.md)
    - [Rezerwacje platformy Azure w programie Cloud Solution Provider w Centrum partnerskim](/partner-center/azure-reservations)
