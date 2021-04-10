---
title: Optymalizacja kosztów Azure Files z zarezerwowaną pojemnością
titleSuffix: Azure Files
description: Dowiedz się, jak zaoszczędzić koszty wdrożeń udziałów plików platformy Azure, używając Azure Files zarezerwowanej pojemności.
services: storage
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 03/23/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 918320cdb24442e551249e4e67d65e4ba85846c8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105027666"
---
# <a name="optimize-costs-for-azure-files-with-reserved-capacity"></a>Optymalizacja kosztów Azure Files z zarezerwowaną pojemnością
Możesz zaoszczędzić pieniądze dotyczące kosztów magazynu dla udziałów plików platformy Azure z rezerwacjami pojemności. Azure Files zarezerwowana pojemność oferuje rabat na wydajność magazynu podczas zatwierdzania rezerwacji przez jeden rok lub trzy lata. Rezerwacja zapewnia stałą ilość miejsca w magazynie w przypadku rezerwacji.

Azure Files zarezerwowana pojemność może znacząco obniżyć koszty związane z przechowywaniem danych w udziałach plików platformy Azure. Ilość zapisywanych danych zależy od czasu trwania rezerwacji, całkowitej pojemności wybranej do zarezerwowania oraz ustawień warstwy i nadmiarowości wybranych dla udziałów plików platformy Azure. Zarezerwowana pojemność zapewnia rabat na rozliczenia i nie wpływa na stan udziałów plików platformy Azure.

Aby uzyskać informacje o cenach na temat zdolności rezerwacji Azure Files, zobacz [Cennik usługi Azure Files](https://azure.microsoft.com/pricing/details/storage/files/).

## <a name="reservation-terms-for-azure-files"></a>Warunki rezerwacji dla Azure Files
W poniższych sekcjach opisano warunki rezerwacji pojemności Azure Files.

### <a name="reservation-capacity"></a>Pojemność rezerwacji
Azure Files zarezerwowaną pojemność można zakupić w jednostkach 10 TiB i 100 TiB miesięcznie przez okres jednego roku lub trzech lat.

### <a name="reservation-scope"></a>Zakres rezerwacji
Azure Files zarezerwowana pojemność jest dostępna dla jednej subskrypcji lub dla wielu subskrypcji (zakres udostępniony). W przypadku zakresu pojedynczej subskrypcji rabat rezerwacji jest stosowany tylko do wybranej subskrypcji. W przypadku zakresów dla wielu subskrypcji rabat w ramach rezerwacji jest współużytkowany przez te subskrypcje w kontekście rozliczeń klienta. Rezerwacja dotyczy użycia w ramach zakupionego zakresu i nie może być ograniczona do określonego konta magazynu, kontenera lub obiektu w ramach subskrypcji.

Rezerwacja pojemności dla Azure Files obejmuje tylko ilość danych przechowywanych w ramach subskrypcji lub udostępnionej grupy zasobów. Opłaty za transakcje, przepustowość i transfer danych nie są uwzględniane w rezerwacji. Po zakupie rezerwacji opłaty za pojemność, które pasują do atrybutów rezerwacji, są naliczane według stawek rabatu zamiast stawek płatności zgodnie z rzeczywistym użyciem. Aby uzyskać więcej informacji na temat rezerwacji platformy Azure, zobacz [co to są Azure Reservations?](../../cost-management-billing/reservations/save-compute-costs-reservations.md).

### <a name="supported-tiers-and-redundancy-options"></a>Obsługiwane warstwy i opcje nadmiarowości
Zarezerwowana pojemność Azure Files jest dostępna tylko w przypadku standardowych udziałów plików wdrożonych na kontach magazynu ogólnego przeznaczenia w wersji 2 (GPv2). Zarezerwowana pojemność nie jest dostępna dla udziałów plików platformy Azure w warstwach w warstwie Premium lub zoptymalizowanych pod kątem transakcji.

Obecnie tylko udziały plików platformy Azure w warstwach gorąca i chłodna obsługują rezerwacje. Wszystkie nadmiarowość magazynu obsługują rezerwacje. Aby uzyskać więcej informacji na temat opcji nadmiarowości, zobacz [Azure Files nadmiarowości](storage-files-planning.md#redundancy).

### <a name="security-requirements-for-purchase"></a>Wymagania dotyczące zabezpieczeń w przypadku zakupu
Aby zakupić zastrzeżoną pojemność:

- Musisz być w roli **właściciela** dla co najmniej jednej subskrypcji przedsiębiorstwa lub indywidualnej, która ma stawki płatność zgodnie z rzeczywistym użyciem.
- W przypadku subskrypcji przedsiębiorstwa w portalu EA należy włączyć opcję **Dodawanie wystąpień zarezerwowanych** . Jeśli to ustawienie jest wyłączone, musisz być administratorem EA w subskrypcji.
- W przypadku programu Cloud Solution Provider (CSP) tylko agenci administracyjni lub agenci sprzedaży mogą kupować Azure Files zarezerwowaną pojemność.

## <a name="determine-required-capacity-before-purchase"></a>Określ wymaganą pojemność przed zakupem
W przypadku zakupu rezerwacji Azure Files należy wybrać opcję region, warstwa i nadmiarowość dla rezerwacji. Rezerwacja jest prawidłowa tylko dla danych przechowywanych w tym regionie, warstwie i poziomie nadmiarowości. Załóżmy na przykład, że zakupisz rezerwację danych w zachodnich stanach USA dla warstwy gorąca przy użyciu magazynu Strefowo nadmiarowego (ZRS). Ta rezerwacja nie będzie dotyczyć danych w regionie Wschodnie stany USA, danych w warstwie chłodna lub danych w magazynie geograficznie nadmiarowym (GRS). Można jednak zakupić kolejną rezerwację w celu uzyskania dodatkowych potrzeb.  

Rezerwacje są dostępne dla bloków 10 TiB lub 100 TiB z wyższymi rabatami dla bloków TiB 100. Po zakupieniu rezerwacji w Azure Portal firma Microsoft może dostarczyć rekomendacje w oparciu o poprzednie użycie, aby pomóc w ustaleniu, która rezerwacja należy zakupić.

## <a name="purchase-azure-files-reserved-capacity"></a>Zakup zarezerwowanej Azure Files pojemności
Azure Files zarezerwowaną pojemność można zakupić za pomocą [Azure Portal](https://portal.azure.com). Płatność za rezerwację jest wnoszona z góry lub w ratach miesięcznych. Aby uzyskać więcej informacji na temat kupowania miesięcznych płatności, zobacz [kupowanie rezerwacji platformy Azure z góry lub miesięcznymi opłatami](../../cost-management-billing/reservations/prepare-buy-reservation.md).

Aby uzyskać pomoc dotyczącą określania warunków rezerwacji właściwych dla danego scenariusza, zobacz [Opis rabatu zarezerwowanej pojemności usługi Azure Storage](../../cost-management-billing/reservations/understand-storage-charges.md).

Wykonaj następujące kroki, aby zakupić zastrzeżoną pojemność:

1. Przejdź do bloku [rezerwacje zakupów](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/Browse_AddCommand) w Azure Portal.  
1. Wybierz **Azure Files** , aby kupić nową rezerwację.  
1. Wypełnij pola wymagane zgodnie z opisem w poniższej tabeli:

    ![Zrzut ekranu przedstawiający sposób zakupu pojemności zarezerwowanej](./media/files-reserve-capacity/select-reserved-capacity.png)

   |Pole  |Opis  |
   |---------|---------|
   |**Zakres**   |  Wskazuje, ile subskrypcji może korzystać z zalet rozliczeń skojarzonych z rezerwacją. Kontroluje również sposób zastosowania rezerwacji do określonych subskrypcji. <br/><br/> W przypadku wybrania opcji **udostępnione** rabat za rezerwację zostanie zastosowany do Azure Files pojemności w ramach kontekstu rozliczania. Kontekst rozliczania jest oparty na sposobie rejestracji w usłudze Azure. W przypadku klientów korporacyjnych zakresem udostępnionym jest rejestracja i uwzględnianie wszystkich subskrypcji w ramach rejestracji. W przypadku klientów z systemem płatność zgodnie z rzeczywistym użyciem zakres udostępniony obejmuje wszystkie poszczególne subskrypcje z opłatami z rabatem zgodnie z rzeczywistym użyciem utworzonymi przez administratora konta.  <br/><br/>  W przypadku wybrania opcji **Pojedyncza subskrypcja** rabat zostanie zastosowany do Azure Files pojemności w ramach wybranej subskrypcji. <br/><br/> W przypadku wybrania opcji **pojedyncze grupy zasobów** rabat rezerwacji zostanie zastosowany do Azure Files pojemności wybranej subskrypcji i wybranej grupy zasobów w ramach tej subskrypcji. <br/><br/> Można zmienić zakres rezerwacji po zakupie rezerwacji.  |
   |**Subskrypcja**  | Subskrypcja, która jest używana do płacenia za rezerwację Azure Files. Metoda płatności w ramach wybranej subskrypcji jest używana w celu naliczania kosztów. Subskrypcja musi być jednym z następujących typów: <br/><br/>  Enterprise Agreement (numery ofert: MS-AZR-0017P lub MS-AZR-0148P): w przypadku subskrypcji przedsiębiorstwa opłaty są naliczane od przedpłaty za subskrypcję platformy Azure (wcześniej zwanej zobowiązaniem pieniężnym) lub za użycie nadwyżkowe. <br/><br/> Indywidualna subskrypcja z stawką płatności zgodnie z rzeczywistym użyciem (numery ofert: MS-AZR-0003P lub MS-AZR-0023P): w przypadku pojedynczej subskrypcji z stawką płatności zgodnie z rzeczywistym użyciem opłaty są naliczane za kartę kredytową lub formę płatności faktury dla subskrypcji.    |
   | **Region** | Region, w którym rezerwacja jest obowiązująca. |
   | **Warstwa** | Warstwa, w której obowiązuje rezerwacja. Dostępne opcje to *gorąca* i *chłodna*. |
   | **Nadmiarowość** | Opcja nadmiarowości dla rezerwacji. Dostępne opcje to *LRS*, *ZRS*, *GRS* i *GZRS*. Aby uzyskać więcej informacji na temat opcji nadmiarowości, zobacz [Azure Files nadmiarowości](storage-files-planning.md#redundancy). |
   | **Częstotliwość rozliczeń** | Wskazuje, jak często konto jest rozliczane dla rezerwacji. Opcje obejmują *co miesiąc* lub z *góry*. |
   | **Rozmiar** | Ilość pojemności do zarezerwowania. |
   |**Okres**  | Jeden rok lub trzy lata.   |

1. Po wybraniu parametrów dla rezerwacji Azure Portal zostanie wyświetlony koszt. W portalu jest również pokazywana wartość procentowa rabatu w porównaniu z płatnościami zgodnie z rzeczywistym użyciem.

1. W bloku **rezerwacje zakupu** Przejrzyj łączny koszt rezerwacji. Możesz również podać nazwę rezerwacji.

Po zakupieniu rezerwacji zostanie ona automatycznie zastosowana do wszystkich istniejących udziałów plików platformy Azure, które pasują do warunków rezerwacji. Jeśli nie utworzono jeszcze żadnych udziałów plików platformy Azure, rezerwacja zostanie zastosowana po każdym utworzeniu zasobu odpowiadającego postanowieniom rezerwacji. W obu przypadkach okres rezerwacji rozpoczyna się natychmiast po pomyślnym zakupie.

## <a name="exchange-or-refund-a-reservation"></a>Program Exchange lub zwrot rezerwacji
Możesz wymienić lub refundować rezerwację z pewnymi ograniczeniami. Te ograniczenia są opisane w poniższych sekcjach.

Aby wymienić lub zwrócić zastrzeżenie, przejdź do szczegółów rezerwacji w Azure Portal. Wybierz pozycję **Exchange** lub **refunda** i postępuj zgodnie z instrukcjami, aby przesłać żądanie pomocy technicznej. Gdy żądanie zostało przetworzone, firma Microsoft wyśle do Ciebie wiadomość e-mail w celu potwierdzenia zakończenia żądania.

Aby uzyskać więcej informacji na temat zasad Azure Reservations, zobacz samoobsługowe [wymianę i zwroty za Azure Reservations](../../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

### <a name="exchange-a-reservation"></a>Wymiana rezerwacji
Wymiana rezerwacji pozwala uzyskać proporcjonalną refundację w oparciu o nieużywaną część rezerwacji. Następnie można zastosować zwrot do ceny zakupu nowej rezerwacji Azure Files.

Nie ma żadnego limitu liczby wymian, które możesz wprowadzić. Ponadto nie jest naliczana opłata skojarzona z programem Exchange. Nowe rezerwacji, które należy zakupić, muszą mieć wartość równą lub większą niż proporcjonalny kredyt z oryginalnej rezerwacji. Rezerwacja Azure Files może być wymieniana tylko dla innej rezerwacji Azure Files, a nie dla rezerwacji dla żadnej innej usługi platformy Azure.

### <a name="refund-a-reservation"></a>Zwrot rezerwacji
W dowolnym momencie możesz anulować rezerwację Azure Files. Po anulowaniu opłata zostanie naliczona proporcjonalnie do pozostałego okresu rezerwacji, pomniejszonej o 12% opłaty za wczesne zakończenie. Maksymalna zwrot za rok to $50 000.

Anulowanie rezerwacji powoduje natychmiastowe zakończenie rezerwacji i zwrócenie pozostałych miesięcy do firmy Microsoft. Pozostałe saldo (proporcjonalnie do wartości) pomniejszone o opłatę zostanie zwrócone do oryginalnej formy zakupu.

## <a name="expiration-of-a-reservation"></a>Wygaśnięcie rezerwacji
Po wygaśnięciu rezerwacji wszystkie Azure Files pojemności używanej w ramach tej rezerwacji są rozliczane według stawki płatność zgodnie z rzeczywistym użyciem. Rezerwacje nie są odnawiane automatycznie.

Otrzymasz powiadomienie e-mail za 30 dni przed wygaśnięciem rezerwacji, a następnie datę wygaśnięcia. Aby nadal korzystać z zalet oszczędności kosztów rezerwacji, Odnów ją nie później niż data wygaśnięcia.

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami
Jeśli masz pytania lub potrzebujesz pomocy, [utwórz wniosek o pomoc techniczną](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Następne kroki
- [Co to są rezerwacje platformy Azure?](../../cost-management-billing/reservations/save-compute-costs-reservations.md)
- [Informacje na temat sposobu stosowania rabatów rezerwacji do usług Azure Storage](../../cost-management-billing/reservations/understand-storage-charges.md)