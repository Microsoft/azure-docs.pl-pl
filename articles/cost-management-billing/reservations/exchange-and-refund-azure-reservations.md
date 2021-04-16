---
title: Samoobsługowe wymiany i zwroty kosztów rezerwacji platformy Azure
description: Dowiedz się, w jaki sposób można wymienić lub uzyskać zwrot kosztów rezerwacji platformy Azure. Aby wymienić rezerwację lub uzyskać zwrot jej kosztów, musisz mieć dostęp na poziomie właściciela do zamówienia rezerwacji.
author: yashesvi
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 04/14/2021
ms.author: banders
ms.openlocfilehash: 3e8f50efd04364483c32ecb8ef5020bdd053e55b
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107515485"
---
# <a name="self-service-exchanges-and-refunds-for-azure-reservations"></a>Samoobsługowe wymiany i zwroty kosztów dotyczące rezerwacji na platformie Azure

Rezerwacje platformy Azure zapewniają elastyczność, dzięki której można łatwiej sprostać Twoim zmieniającym się potrzebom. Rezerwacje można wymienić na inną rezerwację tego samego typu. Na przykład możesz zwrócić wiele rezerwacji zasobów obliczeniowych, w tym Azure Dedicated Host, Azure VMware Solution i Azure Virtual Machines ze sobą jednocześnie. Innymi słowy, produkty rezerwacji są wzajemnie wymienne, jeśli są tego samego typu rezerwacją. W innym przykładzie można wymienić wiele typów rezerwacji bazy danych SQL, w tym wystąpienia zarządzane i Pula elastyczna ze sobą.

Nie można jednak wymieniać niesymilowych rezerwacji. Na przykład nie można wymienić rezerwacji Cosmos DB na SQL Database.

Możesz również wymienić rezerwację, aby kupić inną rezerwację podobnego typu w innym regionie. Możesz na przykład wymienić rezerwację w zachodnich stanyach USA 2 na rezerwację w Europie Zachodniej.

Podczas wymiany rezerwacji możesz zmienić okres z jednego roku na trzy lata.

Możesz również zwrócić rezerwacje, ale łączna suma anulowanego zobowiązania z tytułu rezerwacji w zakresie rozliczeniowym (np. umowa EA, Umowa z Klientem Microsoft i umowa Microsoft Partner Agreement) nie może przekroczyć 50 000 USD w 12-miesięcznym kroczącym przedziale czasu.

Pojemność zarezerwowana usługi Azure Databricks, rezerwacja usługi Azure VMware Solution by CloudSimple, rezerwacja usługi Azure Red Hat Open Shift, plany oprogramowania Red Hat i plany oprogramowania SUSE Linux nie podlegają zwrotom.

> [!NOTE]
> - **Aby wymienić istniejącą rezerwację lub uzyskać zwrot jej kosztów, musisz mieć dostęp właściciela do zamówienia rezerwacji**. Możesz [dodawać lub zmieniać użytkowników, którzy mogą zarządzać rezerwacją](./manage-reserved-vm-instance.md#who-can-manage-a-reservation-by-default).
> - Firma Microsoft obecnie nie nalicza opłat za wcześniejsze zakończenie w przypadku zwrotów rezerwacji. Możemy naliczać opłaty za zwroty dokonywane w przyszłości. Obecnie nie mamy ustalonej daty włączenia tej opłaty.

## <a name="how-to-exchange-or-refund-an-existing-reservation"></a>Jak wymienić lub zwrócić istniejącą rezerwację

Możesz wymienić rezerwację w witrynie [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade).

1. Wybierz rezerwacje, za które chcesz uzyskać zwrot kosztów, a następnie wybierz pozycję **Wymień**.  
    [![Przykładowy obraz przedstawiający rezerwacje, które mają zostać zwrócone](./media/exchange-and-refund-azure-reservations/exchange-refund-return.png)](./media/exchange-and-refund-azure-reservations/exchange-refund-return.png#lightbox)
1. Wybierz produkt maszyny wirtualnej, który chcesz kupić, a następnie wpisz ilość. Upewnij się, że nowa suma zakupu przekracza łączny zwrot. [Określ odpowiedni rozmiar przed zakupem](../../virtual-machines/prepay-reserved-vm-instances.md#determine-the-right-vm-size-before-you-buy).  
    [![Przykładowy obraz przedstawiający produkt maszyny wirtualnej do zakupienia przy użyciu programu wymiany](./media/exchange-and-refund-azure-reservations/exchange-refund-select-purchase.png)](./media/exchange-and-refund-azure-reservations/exchange-refund-select-purchase.png#lightbox)
1. Przejrzyj i zakończ transakcję.  
    [![Przykładowy obraz przedstawiający produkt maszyny wirtualnej do zakupienia przy użyciu programu wymiany oraz dokończenie zwrotu](./media/exchange-and-refund-azure-reservations/exchange-refund-confirm-exchange.png)](./media/exchange-and-refund-azure-reservations/exchange-refund-confirm-exchange.png#lightbox)

Aby uzyskać zwrot kosztów rezerwacji, przejdź do obszaru **Szczegóły rezerwacji** i wybierz pozycję **Zwrot**.

## <a name="exchange-multiple-reservations"></a>Wymiana wielu rezerwacji

Podobne typy rezerwacji można zwrócić w jednej akcji.

Podczas wymiany rezerwacji nowa kwota waluty zakupu musi być większa niż kwota zwrotu. Jeśli nowa kwota zakupu jest mniejsza niż kwota zwrotu, wystąpi błąd. Jeśli zostanie wyświetlony błąd, zmniejsz ilość, którą chcesz zwrócić, lub zwiększ kwotę do zakupu.

1. Zaloguj się do witryny Azure Portal i przejdź do obszaru **Rezerwacje**.
1. Na liście rezerwacji zaznacz pole dla każdej rezerwacji, którą chcesz wymienić.
1. W górnej części strony wybierz pozycję **Exchange**.
1. W razie potrzeby znowelizuj ilość, która ma zostać zwrócona dla każdej rezerwacji.
1. Jeśli wybierzesz zwracaną ilość automatycznego wypełnienia,  możesz wybrać opcję Zwrot wszystkich, aby wypełnić listę pełną ilością posiadaną dla każdej rezerwacji, lub pozycję Optymalizuj pod kątem wykorzystania **(7 dni),** aby wypełnić listę ilością zoptymalizowaną pod kątem wykorzystania na podstawie ostatnich siedmiu dni użycia. **Wybierz pozycję Zastosuj.**
1. W dolnej części strony wybierz pozycję **Dalej: Kup**.
1. Na karcie zakup wybierz dostępne produkty, na które chcesz wymienić. Możesz wybrać wiele produktów różnych typów.
1. W okienku Wybierz produkt, który chcesz kupić, wybierz odpowiednie produkty, a następnie wybierz pozycję Dodaj do **koszyka,** a następnie wybierz pozycję **Zamknij.**
1. Gdy wszystko będzie gotowe, wybierz pozycję **Dalej: Przejrzyj**.
1. Przejrzyj rezerwacje do zwrotu i nowe rezerwacje do zakupienia, a następnie wybierz pozycję **Potwierdź wymianę.**

## <a name="exchange-non-premium-storage-for-premium-storage"></a>Wymiana magazynu spoza warstwy Premium na magazyn w warstwie Premium

Rezerwację zakupioną dla rozmiaru maszyny wirtualnej, który nie obsługuje magazynu w warstwie Premium, można wymienić na odpowiedni rozmiar maszyny wirtualnej, który go obsługuje. Na przykład _F1_ na _F1s_. Aby dokonać wymiany, przejdź do Szczegółów rezerwacji i wybierz pozycję **Wymiana**. Wymiana nie resetuje okresu zarezerwowanego wystąpienia ani nie tworzy nowej transakcji.

## <a name="how-transactions-are-processed"></a>Sposób przetwarzania transakcji

Najpierw firma Microsoft anuluje istniejącą rezerwację i zwraca kwotę proporcjonalną do tej rezerwacji. Jeśli dochodzi do wymiany, jest przetwarzany nowy zakup. Firma Microsoft przetwarza zwroty przy użyciu jednej z następujących metod, w zależności od typu konta i formy płatności:

### <a name="enterprise-agreement-customers"></a>Klienci posiadający umowę Enterprise Agreement

Pieniądze są dodawane do opłaty z góry za platformę Azure (wcześniej nazywanej zobowiązaniem pieniężnym) na wymiany i zwroty w przypadku, gdy pierwotny zakup został utworzony przy jego użyciu. Jeśli okres opłaty z góry za platformę Azure korzystający z rezerwacji, który został już zakupiony, nie jest już aktywny, do bieżącego okresu opłaty z góry za platformę Azure w ramach umowy Enterprise Agreement zostaną dodane środki. Środki są ważne przez 90 dni od daty zwrotu pieniędzy. Niewykorzystane środki wygasną po upływie 90 dni.

Jeśli zakup pierwotny został zrealizowany jako nadwyżka, oryginalna faktura, na podstawie której została zakupiona rezerwacja, i wszystkie późniejsze faktury są ponownie otwierane i korygowane. Firma Microsoft wystawia notę kredytową dla zwrotów.

### <a name="pay-as-you-go-invoice-payments-and-csp-program"></a>Płatności za faktury zgodnie z rzeczywistym użyciem i program CSP

Oryginalna faktura zakupu rezerwacji zostanie anulowana, a następnie dla zwrotu zostanie utworzona nowa faktura. W przypadku wymiany nowa faktura przedstawia zwrot i nowy zakup. Kwota zwrotu jest dostosowywana do zakupu. W przypadku uzyskania jedynie zwrotu kosztów rezerwacji kwota proporcjonalna nadal obowiązuje dla firmy Microsoft i zostaje dostosowana do przyszłego zakupu rezerwacji. Jeśli kupiono rezerwację ze stawkami płatności zgodnie z rzeczywistym użyciem, a później zdecydujesz przejść do dostawcy CSP, rezerwacja może zostać zwrócona i ponownie kupiona bez kary.

### <a name="pay-as-you-go-credit-card-customers"></a>Klienci korzystający z karty kredytowej umożliwiającej płatność zgodnie z rzeczywistym użyciem

Faktura oryginalna zostaje anulowana i zostaje utworzona nowa faktura. Pieniądze zostaną zwrócone na kartę kredytową, która została użyta podczas pierwotnego zakupu. Jeśli Twoja karta została zmieniona, [skontaktuj się z pomocą techniczną](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="cancel-exchange-and-refund-policies"></a>Zasady dotyczące anulowania, wymiany i zwrotu

Platforma Azure ma następujące zasady dotyczące anulowania, wymiany i zwrotów.

**Zasady dotyczące wymiany**

- Można zwrócić wiele istniejących rezerwacji w celu zakupienia jednej nowej rezerwacji tego samego typu. Nie można wymieniać rezerwacji jednego typu na inny. Na przykład nie można zwrócić rezerwacji maszyny wirtualnej w celu zakupienia rezerwacji SQL. Podczas wymiany możesz zmienić jakąś właściwość rezerwacji, na przykład rodzinę, serię, wersję, jednostkę SKU, region, ilość i okres.
- Tylko właściciele rezerwacji mogą przetwarzać wymianę. [Dowiedz się, jak dodawać lub zmieniać użytkowników, którzy mogą zarządzać rezerwacją](manage-reserved-vm-instance.md#who-can-manage-a-reservation-by-default).
- Wymiana jest przetwarzana jako zwrot i ponowny zakup — różne transakcje są tworzone dla anulowania i nowego zakupu rezerwacji. Proporcjonalna kwota rezerwacji jest zwracana w przypadku rezerwacji, w których zawarto wymianę. Nastąpi pełne obciążenie za nowy zakup. Proporcjonalna kwota rezerwacji to dzienna proporcjonalna pozostała wartość zwrotu kosztów rezerwacji.
- Możesz wymienić lub uzyskać zwrot kosztów rezerwacji, nawet jeśli umowa Enterprise Agreement użyta do zakupu rezerwacji wygasła i została odnowiona jako nowa umowa.
- Kwota zobowiązania dla okresu istnienia nowej rezerwacji powinna być równa lub większa niż kwota pozostałego zobowiązania dla zwróconej rezerwacji. Przykład: w przypadku 3-letniej rezerwacji ze zobowiązaniem 100 USD miesięcznie wymienianej po osiemnastej płatności zobowiązanie dla okresu istnienia nowej rezerwacji powinno wynosić 1800 USD lub więcej (płatne miesięcznie lub z góry).
- Nowa rezerwacja zakupiona w ramach wymiany ma nowy okres obowiązywania od momentu wymiany.
- Nie ma kar ani rocznych limitów wymian.

**Zasady dotyczące zwrotów**

- Obecnie nie naliczamy opłaty za wcześniejsze zakończenie, ale w przyszłości w przypadku anulowania może być naliczana opłata za wcześniejsze zakończenie w wysokości 12%.
- Łączne anulowane zobowiązanie nie może przekroczyć 50 000 USD w 12-miesięcznym kroczącym przedziale czasu dla profilu rozliczeniowego lub jednej rejestracji. Na przykład w przypadku 3-letniej rezerwacji ze zobowiązaniem 100 USD miesięcznie zwróconej w 18 miesiącu anulowane zobowiązanie to 1800 USD. Po zwrocie nowy dostępny limit zwrotu będzie wynosił 48 200 USD. Po 365 dniach od tego zwrotu limit 48 200 USD zostanie zwiększony o 1800 USD i nowa pula będzie równa 50 000 USD. Każde inne anulowanie rezerwacji dla profilu rozliczeniowego lub rejestracji w ramach umowy EA spowoduje uszczuplenie tej samej puli i stosowana będzie ta sama logika uzupełniania.
- Platforma Azure nie przetworzy żadnych zwrotów przekraczających limit 50 000 USD w okresie 12 miesięcy dla profilu rozliczeniowego lub rejestracji w ramach umowy EA.
    - Zwroty wynikające z wymiany nie są wliczane do limitu zwrotów.
- Zwroty są obliczane na podstawie najniższej z dwóch kwoty: ceny zakupu i bieżącej ceny rezerwacji.
- Tylko właściciele zamówienia rezerwacji mogą przetwarzać zwrot. [Dowiedz się, jak dodawać lub zmieniać użytkowników, którzy mogą zarządzać rezerwacją](manage-reserved-vm-instance.md#who-can-manage-a-reservation-by-default).

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
    - [Rezerwacje platformy Azure w programie CSP](/partner-center/azure-reservations)
