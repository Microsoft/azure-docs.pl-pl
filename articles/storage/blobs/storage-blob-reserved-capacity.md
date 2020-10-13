---
title: Optymalizowanie kosztów magazynu obiektów blob przy użyciu pojemności zarezerwowanej
titleSuffix: Azure Storage
description: Dowiedz się więcej o kupowaniu zarezerwowanej pojemności usługi Azure Storage, aby zaoszczędzić koszty dotyczące blokowych obiektów blob i Azure Data Lake Storage Gen2 zasobów.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 10/08/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: cf96906b0dab9a94febe83468f813c7cae0675b0
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91874819"
---
# <a name="optimize-costs-for-blob-storage-with-reserved-capacity"></a>Optymalizowanie kosztów magazynu obiektów blob przy użyciu pojemności zarezerwowanej

Możesz zaoszczędzić oszczędności związane z magazynowaniem danych obiektów BLOB w usłudze Azure Storage. Zarezerwowana pojemność usługi Azure Storage oferuje rabat na wydajność blokowych obiektów blob oraz Azure Data Lake Storage Gen2 danych na kontach magazynu w warstwie Standardowa po zatwierdzeniu do rezerwacji przez jeden rok lub trzy lata. Rezerwacja zapewnia stałą ilość miejsca w magazynie w przypadku rezerwacji.

Zarezerwowana pojemność usługi Azure Storage może znacząco obniżyć koszty pojemności dla blokowych obiektów blob i danych Azure Data Lake Storage Gen2. Uzyskane oszczędności kosztów zależą od czasu obowiązywania rezerwacji, całkowitej pojemności wybranej do zarezerwowania i warstwy dostępu oraz typu nadmiarowości wybranego dla konta magazynu. Zarezerwowana pojemność zapewnia rabat na rozliczenia i nie wpływa na stan zasobów usługi Azure Storage.

Aby uzyskać informacje na temat cen rezerwacji usługi Azure Storage, zobacz [Ceny blokowych obiektów blob](https://azure.microsoft.com/pricing/details/storage/blobs/) i [Cennik usługi Azure Data Lake Storage Gen 2](https://azure.microsoft.com/pricing/details/storage/data-lake/).

## <a name="reservation-terms-for-azure-storage"></a>Warunki rezerwacji usługi Azure Storage

W poniższych sekcjach opisano warunki rezerwacji usługi Azure Storage.

### <a name="reservation-capacity"></a>Pojemność rezerwacji

Możesz zakupić zarezerwowaną pojemność usługi Azure Storage w jednostkach 100 TiB i 1 PiB miesięcznie przez okres jednego roku lub trzy lata.

### <a name="reservation-scope"></a>Zakres rezerwacji

Zarezerwowana pojemność usługi Azure Storage jest dostępna dla jednej subskrypcji lub dla wielu subskrypcji (zakres udostępniony). W przypadku zakresu pojedynczej subskrypcji rabat rezerwacji jest stosowany tylko do wybranej subskrypcji. W przypadku zakresów dla wielu subskrypcji rabat w ramach rezerwacji jest współużytkowany przez te subskrypcje w kontekście rozliczeń klienta.

W przypadku zakupu zarezerwowanej pojemności usługi Azure Storage można użyć rezerwacji zarówno dla blokowych obiektów blob, jak i danych Azure Data Lake Storage Gen2. Rezerwacja jest stosowana do użycia w ramach zakupionego zakresu i nie może być ograniczona do określonego konta magazynu, kontenera lub obiektu w ramach subskrypcji.

Rezerwacja usługi Azure Storage obejmuje tylko ilość danych przechowywanych w ramach subskrypcji lub udostępnionej grupy zasobów. Opłaty za wcześniejsze usunięcie, operacje, przepustowość i transfer danych nie są uwzględniane w rezerwacji. Po zakupieniu rezerwacji opłaty za pojemność, które pasują do atrybutów rezerwacji, są naliczane według stawek rabatu, a nie według stawek płatności zgodnie z rzeczywistym użyciem. Aby uzyskać więcej informacji na temat rezerwacji platformy Azure, zobacz [co to są Azure Reservations?](/azure/billing/billing-save-compute-costs-reservations).

### <a name="supported-account-types-tiers-and-redundancy-options"></a>Obsługiwane typy kont, warstwy i opcje nadmiarowości

Zarezerwowana pojemność usługi Azure Storage jest dostępna dla zasobów w standardowych kontach magazynu, w tym w przypadku kont ogólnego przeznaczenia w wersji 2 (GPv2) i BLOB Storage.

Wszystkie warstwy dostępu (gorąca, chłodna i archiwalna) są obsługiwane dla rezerwacji. Aby uzyskać więcej informacji na temat warstw dostępu, zobacz [Azure Blob Storage: warstwy dostępu gorąca, chłodna i archiwalna](storage-blob-storage-tiers.md).

Wszystkie typy nadmiarowości są obsługiwane dla rezerwacji. Aby uzyskać więcej informacji na temat opcji nadmiarowości, zobacz [nadmiarowość usługi Azure Storage](../common/storage-redundancy.md).

> [!NOTE]
> Zarezerwowana pojemność usługi Azure Storage nie jest dostępna dla kont magazynu w warstwie Premium, kont magazynu ogólnego przeznaczenia w wersji 1 (GPv1), Azure Data Lake Storage Gen1, stronicowych obiektów blob, Azure queue storage, Azure Table Storage lub Azure Files.  

### <a name="security-requirements-for-purchase"></a>Wymagania dotyczące zabezpieczeń w przypadku zakupu

Aby zakupić zastrzeżoną pojemność:

- Musisz być w roli **właściciela** dla co najmniej jednej subskrypcji przedsiębiorstwa lub indywidualnej, która ma stawki płatność zgodnie z rzeczywistym użyciem.
- W przypadku subskrypcji przedsiębiorstwa w portalu EA należy włączyć opcję **Dodawanie wystąpień zarezerwowanych** . Jeśli to ustawienie jest wyłączone, musisz być administratorem EA w subskrypcji.
- W przypadku programu Cloud Solution Provider (CSP) tylko agenci administracyjni lub agenci sprzedaży mogą kupić zarezerwowaną pojemność platformy Azure Blob Storage.

## <a name="determine-required-capacity-before-purchase"></a>Określ wymaganą pojemność przed zakupem

W przypadku zakupu rezerwacji usługi Azure Storage należy wybrać opcję region, warstwa dostępu i nadmiarowość dla rezerwacji. Rezerwacja jest prawidłowa tylko dla danych przechowywanych w tym regionie, warstwy dostępu i na poziomie nadmiarowości. Załóżmy na przykład, że zakupisz rezerwację danych w zachodnich stanach USA dla warstwy gorąca przy użyciu magazynu Strefowo nadmiarowego (ZRS). Nie można używać tej samej rezerwacji dla danych w regionach Wschodnie stany USA, danych w warstwie archiwum ani danych w magazynie geograficznie nadmiarowym (GRS). Można jednak zakupić kolejną rezerwację w celu uzyskania dodatkowych potrzeb.  

Rezerwacje są obecnie dostępne dla bloków 100 TiB lub 1 PiB z wyższymi rabatami dla 1 bloków PiB. Po zakupieniu rezerwacji w Azure Portal firma Microsoft może dostarczyć rekomendacje w oparciu o poprzednie użycie, aby pomóc w ustaleniu, która rezerwacja należy zakupić.

## <a name="purchase-azure-storage-reserved-capacity"></a>Kupowanie zarezerwowanej pojemności usługi Azure Storage

Możesz zakupić zarezerwowaną pojemność usługi Azure Storage za pomocą [Azure Portal](https://portal.azure.com). Płatność za rezerwację jest wnoszona z góry lub w ratach miesięcznych. Aby uzyskać więcej informacji na temat kupowania miesięcznych płatności, zobacz [kupowanie rezerwacji platformy Azure z góry lub miesięcznymi opłatami](/azure/billing/billing-monthly-payments-reservations).

Aby uzyskać pomoc dotyczącą określania warunków rezerwacji odpowiednich dla danego scenariusza, zobacz [Opis rabatu zarezerwowanej pojemności usługi Azure Storage](../../cost-management-billing/reservations/understand-storage-charges.md).

Wykonaj następujące kroki, aby zakupić zastrzeżoną pojemność:

1. Przejdź do okienka [rezerwacje zakupów](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/Browse_AddCommand) w Azure Portal.  
1. Wybierz pozycję **Azure Blob Storage** , aby kupić nową rezerwację.  
1. Wypełnij pola wymagane zgodnie z opisem w poniższej tabeli:

    ![Zrzut ekranu przedstawiający sposób zakupu pojemności zarezerwowanej](media/storage-blob-reserved-capacity/select-reserved-capacity.png)

   |Pole  |Opis  |
   |---------|---------|
   |**Zakres**   |  Wskazuje, ile subskrypcji może korzystać z zalet rozliczeń skojarzonych z rezerwacją. Kontroluje również sposób zastosowania rezerwacji do określonych subskrypcji. <br/><br/> W przypadku wybrania opcji **udostępnione**rabat zostanie zastosowany do pojemności usługi Azure Storage w dowolnej subskrypcji w kontekście rozliczania. Kontekst rozliczania jest oparty na sposobie rejestracji w usłudze Azure. W przypadku klientów korporacyjnych zakresem udostępnionym jest rejestracja i uwzględnianie wszystkich subskrypcji w ramach rejestracji. W przypadku klientów z systemem płatność zgodnie z rzeczywistym użyciem zakres udostępniony obejmuje wszystkie poszczególne subskrypcje z opłatami z rabatem zgodnie z rzeczywistym użyciem utworzonymi przez administratora konta.  <br/><br/>  W przypadku wybrania opcji **Pojedyncza subskrypcja**rabat zostanie zastosowany do pojemności usługi Azure Storage w ramach wybranej subskrypcji. <br/><br/> W przypadku wybrania opcji **pojedynczy zasób**rabat zostanie zastosowany do pojemności usługi Azure Storage w ramach wybranej subskrypcji i wybranej grupy zasobów w ramach tej subskrypcji. <br/><br/> Można zmienić zakres rezerwacji po zakupie rezerwacji.  |
   |**Subskrypcja**  | Subskrypcja, która jest używana do płacenia za rezerwację usługi Azure Storage. Metoda płatności w ramach wybranej subskrypcji jest używana w celu naliczania kosztów. Subskrypcja musi być jednym z następujących typów: <br/><br/>  Umowa Enterprise (numery ofert: MS-AZR-0017P lub MS-AZR-0148P): w przypadku subskrypcji przedsiębiorstwa opłaty są naliczane od salda zobowiązania pieniężnego rejestracji lub opłaty są naliczane jako nadwyżkowe. <br/><br/> Indywidualna subskrypcja z stawką płatności zgodnie z rzeczywistym użyciem (numery ofert: MS-AZR-0003P lub MS-AZR-0023P): w przypadku pojedynczej subskrypcji z stawką płatności zgodnie z rzeczywistym użyciem opłaty są naliczane za kartę kredytową lub formę płatności faktury dla subskrypcji.    |
   | **Region** | Region, w którym rezerwacja jest obowiązująca. |
   | **Warstwa dostępu** | Warstwa dostępu, w której obowiązuje rezerwacja. Dostępne opcje to *gorąca*, *chłodna*lub *archiwalna*. Aby uzyskać więcej informacji o warstwach dostępu, zobacz [Azure Blob Storage: warstwy dostępu gorąca, chłodna i archiwalna](storage-blob-storage-tiers.md). |
   | **Nadmiarowość** | Opcja nadmiarowości dla rezerwacji. Dostępne opcje to: *LRS*, *ZRS*, *GRS*, *GZRS*, *RA-GRS*i *RA-GZRS*. Aby uzyskać więcej informacji na temat opcji nadmiarowości, zobacz [nadmiarowość usługi Azure Storage](../common/storage-redundancy.md). |
   | **Częstotliwość rozliczeń** | Wskazuje, jak często konto jest rozliczane dla rezerwacji. Opcje obejmują *co miesiąc* lub z *góry*. |
   | **Rozmiar** | Ilość pojemności do zarezerwowania. |
   |**Okres**  | Jeden rok lub trzy lata.   |

1. Po wybraniu parametrów dla rezerwacji Azure Portal zostanie wyświetlony koszt. W portalu jest również pokazywana wartość procentowa rabatu w porównaniu z płatnościami zgodnie z rzeczywistym użyciem.

1. W okienku **rezerwacje zakupu** Przejrzyj łączny koszt rezerwacji. Możesz również podać nazwę rezerwacji.

    ![Zrzut ekranu przedstawiający sposób zakupu rezerwacji](media/storage-blob-reserved-capacity/purchase-reservations.png)

Po zakupieniu rezerwacji zostanie ona automatycznie zastosowana do wszystkich istniejących obiektów blob lub Azure Data Lake Storage Gen2 zasobów usługi Azure Storage, które pasują do warunków rezerwacji. Jeśli nie utworzono jeszcze żadnych zasobów usługi Azure Storage, rezerwacja zostanie zastosowana po każdym utworzeniu zasobu odpowiadającego postanowieniom rezerwacji. W obu przypadkach okres rezerwacji rozpoczyna się natychmiast po pomyślnym zakupie.

## <a name="exchange-or-refund-a-reservation"></a>Program Exchange lub zwrot rezerwacji

Możesz wymienić lub refundować rezerwację z pewnymi ograniczeniami. Te ograniczenia są opisane w poniższych sekcjach.

Aby wymienić lub zwrócić zastrzeżenie, przejdź do szczegółów rezerwacji w Azure Portal. Wybierz pozycję **Exchange** lub **refunda**i postępuj zgodnie z instrukcjami, aby przesłać żądanie pomocy technicznej. Gdy żądanie zostało przetworzone, firma Microsoft wyśle do Ciebie wiadomość e-mail w celu potwierdzenia zakończenia żądania.

Aby uzyskać więcej informacji na temat zasad Azure Reservations, zobacz samoobsługowe [wymianę i zwroty za Azure Reservations](../../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

### <a name="exchange-a-reservation"></a>Wymiana rezerwacji

Wymiana rezerwacji pozwala uzyskać proporcjonalną refundację w oparciu o nieużywaną część rezerwacji. Następnie można zastosować zwrot do ceny zakupu nowej rezerwacji usługi Azure Storage.

Nie ma żadnego limitu liczby wymian, które możesz wprowadzić. Ponadto nie jest naliczana opłata skojarzona z programem Exchange. Nowe rezerwacji, które należy zakupić, muszą mieć wartość równą lub większą niż proporcjonalny kredyt z oryginalnej rezerwacji. Rezerwacja usługi Azure Storage może być wymieniana tylko z inną rezerwacją usługi Azure Storage, a nie na potrzeby rezerwacji żadnej innej usługi platformy Azure.

### <a name="refund-a-reservation"></a>Zwrot rezerwacji

Rezerwację usługi Azure Storage można anulować w dowolnym momencie. Po anulowaniu opłata zostanie naliczona proporcjonalnie do pozostałego okresu rezerwacji, pomniejszonej o 12% opłaty za wczesne zakończenie. Maksymalna zwrot za rok to $50 000.

Anulowanie rezerwacji powoduje natychmiastowe zakończenie rezerwacji i zwrócenie pozostałych miesięcy do firmy Microsoft. Pozostałe saldo (proporcjonalnie do wartości) pomniejszone o opłatę zostanie zwrócone do oryginalnej formy zakupu.

## <a name="expiration-of-a-reservation"></a>Wygaśnięcie rezerwacji

Po wygaśnięciu rezerwacji wszystkie pojemności usługi Azure Storage używane w ramach tej rezerwacji są rozliczane według stawki płatności zgodnie z rzeczywistym użyciem. Rezerwacje nie są odnawiane automatycznie.

Otrzymasz powiadomienie e-mail za 30 dni przed wygaśnięciem rezerwacji, a następnie datę wygaśnięcia. Aby nadal korzystać z zalet oszczędności kosztów rezerwacji, Odnów ją nie później niż data wygaśnięcia.

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami

Jeśli masz pytania lub potrzebujesz pomocy, [utwórz wniosek o pomoc techniczną](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Następne kroki

- [Co to są rezerwacje platformy Azure?](../../cost-management-billing/reservations/save-compute-costs-reservations.md)
- [Omówienie stosowania rabatu za rezerwację do usługi Azure Storage](../../cost-management-billing/reservations/understand-storage-charges.md)
