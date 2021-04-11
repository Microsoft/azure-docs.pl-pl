---
title: Komercyjne funkcje Transact Marketplace firmy Microsoft
description: W tym artykule opisano Cennik, rozliczenia, fakturowania i wypłaty dla opcji Transact Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2021
ms.author: mingshen
author: mingshen-ms
ms.openlocfilehash: 9797401fd96bf052fdc9de0c2c2ceaf88b3d46b6
ms.sourcegitcommit: c2a41648315a95aa6340e67e600a52801af69ec7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106504213"
---
# <a name="commercial-marketplace-transact-capabilities"></a>Komercyjne funkcje Transact Marketplace

W tym artykule opisano zagadnienia dotyczące cen, rozliczeń, fakturowania i wypłaty dla komercyjnego portalu Microsoft Marketplace.

## <a name="transactions-by-listing-option"></a>Opcja transakcji według listy

Wydawca lub firma Microsoft odpowiada za zarządzanie transakcjami licencji na oprogramowanie dla ofert w portalu komercyjnym. Opcja wystaw wybrana dla oferty określa, kto będzie zarządzał transakcją. Aby uzyskać informacje na temat dostępności i wyjaśnień każdej opcji publikowania, zobacz [wprowadzenie do opcji wyświetlania](determine-your-listing-type.md) .

### <a name="contact-me-free-trial-and-byol-options"></a>Skontaktuj się z nami, bezpłatnej wersji próbnej i opcji BYOL

Wydawcy mogą wybrać opcje _kontakt z_ nami i _bezpłatna wersja próbna_, które umożliwiają promocję i pozyskiwanie użytkowników. W przypadku niektórych typów ofert wydawcy mogą wybrać opcję " _Przenieś własną licencję_ " (BYOL), aby umożliwić klientom kupowanie subskrypcji oferty przy użyciu licencji zakupionej bezpośrednio od Ciebie. Dzięki tym opcjom firma Microsoft nie uczestniczy bezpośrednio w transakcjach licencyjnych dotyczących oprogramowania wydawcy i nie ma żadnej powiązanej opłaty za transakcje, dlatego wydawcy zatrzymują wszystkie te dochody.

Za pomocą tych opcji wydawcy są odpowiedzialni za obsługę wszystkich aspektów transakcji licencjonowania oprogramowania. Obejmuje to, ale nie jest ograniczone do kolejności, realizacji, pomiaru, rozliczeń, fakturowania, płatności i kolekcji. Po wybraniu opcji kontakt ze mną wydawcy zadbają o 100% opłat za Licencjonowanie oprogramowania wydawcy zebranych od klienta.

### <a name="transact-publishing-option"></a>Opcja publikowania Transact

Wprowadzenie do sprzedaży za pośrednictwem firmy Microsoft wykorzystuje możliwości firmy Microsoft w handlu i oferuje kompleksowe środowisko wykrywania i oceny do zakupu i implementacji. Oferta _,_ w której firma Microsoft ułatwia wymianę pieniędzy na licencję na oprogramowanie w imieniu wydawcy. Oferty usługi Transact są rozliczane w oparciu o istniejącą subskrypcję firmy Microsoft lub kartę kredytową, co umożliwia firmie Microsoft hostowanie transakcji w chmurze w imieniu wydawcy.

Wybierz opcję Transact podczas tworzenia nowej oferty w centrum partnerskim. Ta opcja zostanie wyświetlona tylko wtedy, gdy dla typu oferty jest dostępna usługa Transact.

## <a name="transact-overview"></a>Omówienie języka Transact

W przypadku korzystania z opcji Transact firma Microsoft umożliwia sprzedaż oprogramowania innych firm i wdrażanie niektórych typów ofert do subskrypcji platformy Azure klienta. W przypadku wybrania modelu cen dla oferty Wydawca musi rozważyć rozliczenie opłat za infrastrukturę i opłat za korzystanie z licencji na oprogramowanie.

Opcja publikowania Transact jest obecnie obsługiwana dla następujących typów ofert:

| Typ oferty | Erze rozliczeń | Naliczanie opłat | Model cen |
| ------------ | ------------- | ------------- | ------------- |
| Azure Application<br>(Aplikacja zarządzana) | Miesięcznie | Tak | Na podstawie użycia |
| Maszyna wirtualna platformy Azure | Miesięczne | Nie | Oparta na użyciu, BYOL |
| Oprogramowanie jako usługa (SaaS) | Miesięczny i roczny | Tak | Stała stawka dla użytkownika, oparta na użyciu. |
|||||

`*` Usługa Azure Virtual Machine oferuje obsługę planów rozliczania opartych na użyciu. Te plany są rozliczane miesięcznie za okresowe użycie subskrypcji na podstawie rdzenia, na rozmiar podstawowy lub na rynek i rozmiar podstawowy.

### <a name="metered-billing"></a>Naliczanie opłat

_Usługa pomiarów w portalu Marketplace_ pozwala określić opłaty za korzystanie z płatności zgodnie z rzeczywistym użyciem (na podstawie zużycia), a także miesięczne lub roczne opłaty zawarte w umowie (uprawnienia). Możesz naliczać opłaty za użycie dla określonych wymiarów usługi pomiaru w portalu Marketplace, takich jak przepustowość, bilety lub przetworzone wiadomości e-mail. Aby uzyskać więcej informacji o naliczaniu opłat za oferty usługi SaaS, zobacz [naliczanie opłat za SaaS przy użyciu komercyjnej usługi pomiarowej Marketplace](./partner-center-portal/saas-metered-billing.md). Aby uzyskać więcej informacji na temat opłat naliczanych za oferty aplikacji platformy Azure, zobacz [rozliczenia taryfowe aplikacji zarządzanych](./partner-center-portal/azure-app-metered-billing.md).

### <a name="billing-infrastructure-costs"></a>Koszty infrastruktury rozliczeń

W przypadku **maszyn wirtualnych** i **aplikacji platformy** Azure opłaty za użycie infrastruktury platformy Azure są naliczane zgodnie z subskrypcją platformy Azure klienta. Opłaty za użycie infrastruktury są określane i prezentowane na fakturze klienta niezależnie od opłat licencyjnych dostawcy oprogramowania.

W przypadku **aplikacji SaaS** Wydawca musi uwzględnić opłaty za użycie infrastruktury platformy Azure i opłaty za licencję na oprogramowanie jako pojedynczy koszt. Jest ona reprezentowana jako stała opłata dla klienta. Użycie infrastruktury platformy Azure jest zarządzane i rozliczane bezpośrednio przez wydawcę. Klient nie widzi rzeczywistych opłat za użycie infrastruktury. Wydawcy zwykle wybierają opłaty za użycie infrastruktury platformy Azure do cen licencji na oprogramowanie. Opłaty za licencje na oprogramowanie nie są naliczane lub oparte na zużyciu użytkownika.

## <a name="pricing-models"></a>Modele cenowe

W zależności od używanej opcji transakcji opłaty za subskrypcję są następujące:

- **Pobierz teraz (bezpłatnie)**: Brak opłat za licencje na oprogramowanie. Bezpłatnych ofert nie można przekonwertować na płatną ofertę. Klienci muszą zamówić płatną ofertę.
- **Bring Your Own License** (BYOL): Jeśli w komercyjnej witrynie Marketplace zostanie wyświetlona oferta, wszelkie stosowne opłaty za licencje na oprogramowanie są zarządzane bezpośrednio między wydawcą a klientem. Firma Microsoft nalicza opłaty za korzystanie z infrastruktury platformy Azure na konto subskrypcji platformy Azure klienta.
- **Cennik subskrypcji**: opłaty za licencje na oprogramowanie są prezentowane jako comiesięczne lub roczne, opłata za subskrypcję cykliczną rozliczaną jako stała stawka lub za stanowisko. Opłaty za bieżącą subskrypcję nie są naliczane proporcjonalnie za krótkoterminowe anulowania klientów lub nieużywane usługi. Opłaty za bieżącą subskrypcję mogą być naliczane proporcjonalnie w przypadku, gdy klient uaktualni lub obniży swoją subskrypcję w trakcie okresu subskrypcji.
- **Cennik oparty na użyciu**: w przypadku ofert związanych z maszynami wirtualnymi platformy Azure opłaty są naliczone w oparciu o zakres korzystania z oferty. W przypadku obrazów maszyn wirtualnych klienci są obciążani opłatą za godzinę w witrynie Azure Marketplace ustawioną przez wydawcę w celu korzystania z maszyn wirtualnych wdrożonych na podstawie obrazów maszyny wirtualnej. Opłata godzinowa może być jednorodna lub różna w rozmiarach maszyn wirtualnych. Godziny częściowe są rozliczone przez minutę. Plany są rozliczane miesięcznie.
- **Cennik taryfowy**: w przypadku ofert opartych na platformie Azure i SaaS, wydawcy mogą używać [usługi pomiaru Marketplace](./partner-center-portal/marketplace-metering-service-apis.md) do rozliczania za użycie w oparciu o niestandardowe wymiary mierników, które konfigurują. Te zmiany są uzupełnieniem miesięcznych lub rocznych opłat uwzględnionych w umowie (uprawnienia). Przykładowe wymiary mierników niestandardowych to przepustowość, bilety lub przetworzone wiadomości e-mail. Wydawcy mogą zdefiniować co najmniej jeden wymiar mierzony dla każdego planu, ale maksymalnie 30 dla każdej oferty. Wydawcy są odpowiedzialni za śledzenie użycia poszczególnych klientów, z każdym licznikiem zdefiniowanym w ofercie. Zdarzenia powinny być zgłaszane firmie Microsoft w ciągu godziny. Firma Microsoft obciąża klientów na podstawie informacji o użyciu zgłoszonych przez wydawców w odpowiednim okresie rozliczeniowym.
- **Bezpłatna wersja próbna**: Brak opłat za licencje na oprogramowanie, które są w zakresie od 30 dni do sześciu miesięcy, w zależności od typu oferty. Jeśli wydawcy przestaną korzystać z bezpłatnej wersji próbnej wielu planów w ramach tej samej oferty, klienci mogą przełączać się do bezpłatnej wersji próbnej w ramach innego planu, ale okres próbny nie zostanie uruchomiony ponownie. W przypadku ofert związanych z maszynami wirtualnymi klienci są obciążani kosztami infrastruktury platformy Azure za korzystanie z oferty w okresie próbnym. Po zakończeniu okresu próbnego klienci są obciążani automatycznie opłatą za ostatni plan, który podjął próbę w oparciu o stawki standardowe, chyba że zostaną anulowane przed końcem okresu próbnego.

> [!NOTE]
> Oferty, które są rozliczane zgodnie z zużyciem po użyciu rozwiązania, nie kwalifikują się do zwrotów.

Wydawcy, którzy chcą zmienić opłaty za użycie skojarzone z ofertą, powinni najpierw usunąć ofertę (lub plan z oferty) z komercyjnej witryny Marketplace. Usuwanie powinno odbywać się zgodnie z wymaganiami [umowy wydawcy firmy Microsoft](https://go.microsoft.com/fwlink/?LinkID=699560). Następnie Wydawca może opublikować nową ofertę (lub plan w ramach oferty), która obejmuje nowe opłaty za użycie. Aby uzyskać więcej informacji o usuwaniu oferty lub planu, zobacz sekcję [stop sprzedawanie oferty lub planu](./partner-center-portal/update-existing-offer.md#stop-selling-an-offer-or-plan).

### <a name="free-contact-me-and-bring-your-own-license-byol-pricing"></a>Bezpłatna, skontaktuj się z nami i przeniesiej własne ceny licencji (BYOL)

Podczas publikowania oferty przy użyciu opcji Pobierz teraz (bezpłatnie), skontaktuj się z nami lub BYOL, firma Microsoft nie odgrywa roli w celu ułatwienia transakcji sprzedaży dla opłat za licencję na oprogramowanie. Wydawca utrzymuje 100% opłat za licencje na oprogramowanie.

### <a name="usage-based-and-subscription-pricing"></a>Cennik oparty na użyciu i subskrypcja

W przypadku publikowania oferty jako transakcji opartej na użyciu lub subskrypcji firma Microsoft udostępnia technologie i usługi do przetwarzania zakupów licencji na oprogramowanie, powrotu i naliczania opłat. W tym scenariuszu Wydawca autoryzuje firmę Microsoft do działania jako Agent w tym celu. Wydawca pozwala firmie Microsoft na ułatwienie transakcji licencjonowania oprogramowania. Wydawca zachowuje oznaczenie jako sprzedawcy, dostawcy, dystrybutora i licencjodawcy.

Firma Microsoft umożliwia klientom zamawianie, Licencjonowanie i używanie oprogramowania zgodnie z warunkami i postanowieniami komercyjnego portalu firmy Microsoft oraz z umową licencyjną użytkownika końcowego. Musisz podać własną umowę licencyjną użytkownika oprogramowania lub wybrać [kontrakt standardowy](./standard-contract.md) podczas tworzenia oferty.

### <a name="free-software-trials"></a>Bezpłatne wersje próbne oprogramowania

W przypadku scenariuszy publikowania Transact można bezpłatnie udostępnić licencje na oprogramowanie od 30 do 120 dni, w zależności od subskrypcji. Klienci będą mogli zmienić odpowiednie użycie infrastruktury platformy Azure.

### <a name="examples-of-pricing-and-store-fees"></a>Przykłady cen i opłat za Sklep

**Na podstawie użycia**

Cennik oparty na użyciu ma następującą strukturę kosztów:

| **Koszt licencji** | **$1,00 za godzinę** |
|---------|---------|
| Koszt użycia platformy Azure (D1/1-rdzeniowe) | $0,14 za godzinę |
| _Klient jest rozliczany przez firmę Microsoft_ | _$1,14 za godzinę_ |
||

W tym scenariuszu firma Microsoft rozlicza $1,14 za godzinę w celu użycia opublikowanego obrazu maszyny wirtualnej.

| **Rachunki firmy Microsoft** | **$1,14 za godzinę**  |
|---------|---------|
| Firma Microsoft płaci 80% kosztu licencji | $0,80 za godzinę |
| Firma Microsoft utrzymuje 20% kosztów licencji  |  $0,20 za godzinę |
| Firma Microsoft utrzymuje 100% kosztów użycia platformy Azure | $0,14 za godzinę |
||

**Korzystanie z własnej licencji (BYOL)**

BYOL ma następującą strukturę kosztów:

| **Koszt licencji** | **Opłata licencyjna wynegocjowana i rozliczona przez użytkownika** |
|---------|---------|
|Koszt użycia platformy Azure (D1/1-rdzeniowe)    |   $0,14 za godzinę     |
| _Klient jest rozliczany przez firmę Microsoft_ | _$0,14 za godzinę_ |
||

W tym scenariuszu firma Microsoft rozlicza $0,14 za godzinę w celu użycia opublikowanego obrazu maszyny wirtualnej.

| **Rachunki firmy Microsoft** | **$0,14 za godzinę** |
|---------|---------|
| Firma Microsoft utrzymuje koszt użycia platformy Azure | $0,14 za godzinę |
| Firma Microsoft utrzymuje 0% kosztu licencji | $0,00 za godzinę |
||

**Subskrypcja aplikacji SaaS**

Subskrypcje SaaS mogą być wyceniane według stawki ryczałtowej lub za użytkownika miesięcznie lub rocznie. Po włączeniu opcji  **sprzedaży za pomocą firmy Microsoft** dla oferty SaaS masz następującą strukturę kosztów:

| **Koszt licencji** | **$100,00 miesięcznie** |
|--------------|---------|
| Koszt użycia platformy Azure (D1/1-rdzeniowe) | Rozliczana bezpośrednio z wydawcą, a nie klientem |
| _Klient jest rozliczany przez firmę Microsoft_ | _$100,00 miesięcznie (w przypadku wydawców należy uwzględnić koszty związane z założeniami lub przekazaniem infrastruktury w ramach opłaty licencyjnej)_ |
||

W tym scenariuszu firma Microsoft rozlicza $100,00 w ramach licencji na oprogramowanie i płaci $80,00 lub $90,00 w zależności od tego, czy oferta jest zgodna z opłatą za usługę magazynu.

| **Rachunki firmy Microsoft** | **$100,00 miesięcznie** |
|---------|---------|
| Firma Microsoft płaci 80% kosztu licencji <br> \* Firma Microsoft płaci 90% kosztu licencji dla wszystkich zakwalifikowanych aplikacji SaaS | $80,00 miesięcznie <br> \* $90,00 miesięcznie |
| Firma Microsoft utrzymuje 20% kosztów licencji <br> \* Firma Microsoft utrzymuje 10% kosztów licencji na wszystkie kwalifikowane aplikacje SaaS. | $20,00 miesięcznie <br> \* $10,00 |

### <a name="commercial-marketplace-service-fees"></a>Komercyjne opłaty za usługę Marketplace

Opłata za usługę magazynu w warstwie Standardowa jest naliczana 20%, gdy klienci kupują ofertę Transact z komercyjnej witryny Marketplace. Aby uzyskać szczegółowe informacje o tej opłacie, zobacz sekcję 5c [umowy Microsoft Publisher](https://go.microsoft.com/fwlink/?LinkID=699560).

W przypadku niektórych ofert Transact opublikowanych w portalu komercyjnym możesz zakwalifikować się do obniżenia opłaty za usługę magazynu o 10%. Aby oferta zakwalifikować się, musi zostać wystawiona przez firmę Microsoft jako _usługa Azure IP zachęcani_. Aby otrzymać obniżoną opłatę za usługę Marketplace, należy spełnić co najmniej pięć dni roboczych przed końcem każdego miesiąca kalendarzowego. Po osiągnięciu uprawnień zmniejszona opłata za usługę zostanie naliczona wszystkim transakcjom, które zaczęły obowiązywać pierwszego dnia następnego dnia miesiąca, do momentu utraty zachęcani stanu usługi _Azure IP współsprzedażowej_ . Aby uzyskać szczegółowe informacje na temat uprawnień do współsprzedawanych adresów IP, zobacz [wymagania dotyczące stanu współsprzedażu](/legal/marketplace/certification-policies#3000-requirements-for-co-sell-status).

Zmniejszona opłata za usługę Marketplace dotyczy współsprzedaży platformy Azure z zachęcani SaaS, maszyn wirtualnych, aplikacji zarządzanych i innych, które są dostępne w ramach komercyjnych rozwiązań IaaS dostępnych w portalu komercyjnym. Płatne oferty SaaS skojarzone z jedną aplikacją Microsoft Teams lub co najmniej dwoma dodatkami Microsoft 365 (Excel, PowerPoint, Word, Outlook i SharePoint) i opublikowane w Microsoft AppSource mogą również kwalifikować się do tego rabatu.

### <a name="customer-invoicing-payment-billing-and-collections"></a>Faktury dla klientów, płatności, rozliczeń i kolekcji

**Fakturowanie i płatność**: możesz użyć preferowanej metody fakturowania klienta, aby dostarczyć subskrypcję lub [PAYGO](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) opłaty za licencję na oprogramowanie.

**Enterprise Agreement**: Jeśli preferowana metoda fakturowania klienta to Microsoft Enterprise Agreement, opłaty za licencje na oprogramowanie będą naliczane przy użyciu tej metody fakturowania jako koszt określony przez użytkownika, niezależnie od kosztów użycia specyficznych dla platformy Azure.

**Karty kredytowe i faktury miesięczne**: klienci mogą skorzystać z karty kredytowej i faktury miesięcznej. W tym przypadku opłaty za licencję na oprogramowanie będą rozliczane w taki sam sposób jak scenariusz Enterprise Agreement, jako koszt określony, niezależnie od kosztów użycia specyficznych dla platformy Azure.

**Bezpłatne kredyty i zobowiązania pieniężne**: niektórzy klienci wybierają przedpłatę platformy Azure z zobowiązaniem pieniężnym w ramach Enterprise Agreement lub otrzymali bezpłatne środki na korzystanie z platformy Azure. Chociaż te kredyty mogą być używane do płacenia za użycie platformy Azure, nie mogą być używane do płacenia opłat za licencje na oprogramowanie wydawcy.

**Rozliczenia i kolekcje**: rozliczanie licencji na oprogramowanie wydawcy jest prezentowane przy użyciu metody fakturowania w wybranym przez klienta i zgodnie z osią czasu fakturowania. Klienci bez Enterprise Agreement są rozliczani miesięcznie za licencje na oprogramowanie w portalu Marketplace. Klienci z Enterprise Agreement są rozliczani miesięcznie za pośrednictwem faktury prezentowanej kwartalnie.

W przypadku wybrania pozycji subskrypcja lub model płatności zgodnie z rzeczywistym użyciem, firma Microsoft działa jako agent wydawcy i odpowiada za wszystkie aspekty rozliczeń, płatności i kolekcji.

### <a name="publisher-payout-and-reporting"></a>Wypłata i raportowanie wydawcy

Wszelkie opłaty za licencje na oprogramowanie zebrane przez firmę Microsoft jako agent podlegają 20% opłatą za transakcję, chyba że określono inaczej i są one odejmowane w momencie wypłaty wydawcy.

Klienci zazwyczaj kupują przy użyciu Enterprise Agreement lub z włączoną kartą kredytową płatność zgodnie z rzeczywistym użyciem. Typ umowy określa rozliczenia, fakturowanie, zbieranie i czas wypłaty.

>[!NOTE]
>Wszystkie raporty i szczegółowe informacje dotyczące opcji publikowania Transact są dostępne za pośrednictwem sekcji Analiza w centrum partnerskim.

#### <a name="billing-questions-and-support"></a>Pytania i pomoc techniczna dotyczące rozliczeń

Aby uzyskać więcej informacji i zasad prawnych, zobacz [umowę wydawcy firmy Microsoft](https://go.microsoft.com/fwlink/?LinkID=699560). Aby uzyskać pomoc dotyczącą pytań dotyczących rozliczeń, skontaktuj się z [pomocą techniczną wydawcy portalu Marketplace](https://aka.ms/marketplacepublishersupport).

## <a name="transact-requirements"></a>Wymagania dotyczące języka Transact

Ta sekcja obejmuje wymagania dotyczące usługi Transact dla różnych typów ofert.

### <a name="requirements-for-all-offer-types"></a>Wymagania dotyczące wszystkich typów ofert

- Dla opcji publikowania Transact są wymagane konto Microsoft i informacje finansowe, niezależnie od modelu cen oferty.
- Obowiązkowe informacje finansowe obejmują konto płatne i profil podatkowy.

Aby uzyskać więcej informacji na temat konfigurowania tych kont, zobacz [Zarządzanie kontem komercyjnej witryny Marketplace w centrum partnerskim](partner-center-portal/manage-account.md).

### <a name="requirements-for-specific-offer-types"></a>Wymagania dotyczące konkretnych typów ofert

Możliwość wykonywania operacji transakcyjnych przez firmę Microsoft jest dostępna tylko dla następujących komercyjnych typów ofert w portalu Marketplace. Ta lista zawiera wymagania dotyczące tworzenia tych typów ofert w celach komercyjnych w komercyjnej witrynie Marketplace.

- **Aplikacje platformy Azure (szablon rozwiązania i zarządzane plany aplikacji**): w niektórych przypadkach opłaty za użycie infrastruktury platformy Azure są przesyłane do klienta osobno od opłat za licencje na oprogramowanie, ale w tej samej zestawie rozliczeń. Jednak w przypadku skonfigurowania planu zarządzanej aplikacji dla opłat za infrastrukturę niezależnego dostawcy oprogramowania zasoby platformy Azure są rozliczane dla wydawcy, a klient otrzymuje stałą opłatę obejmującą koszt infrastruktury, licencje na oprogramowanie i usługi zarządzania.

- **Maszyna wirtualna platformy Azure**: Wybieraj spośród modeli cenowych BYOL lub opartych na użyciu. Na rachunku na platformie Azure klienta firma Microsoft przedstawia opłaty za licencję na oprogramowanie wydawcy niezależnie od opłat związanych z infrastrukturą platformy Azure. Opłaty za infrastrukturę platformy Azure są oparte na oprogramowaniu wydawcy.

- **Aplikacja SaaS**: musi być rozwiązaniem wielodostępnym, używać [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) do uwierzytelniania i integrują się z [interfejsami API realizacji SaaS](partner-center-portal/pc-saas-fulfillment-api-v2.md). Użycie infrastruktury platformy Azure jest zarządzane i rozliczane bezpośrednio dla Ciebie (wydawca), więc musisz uwzględnić opłaty za użycie infrastruktury platformy Azure i opłaty za licencję na oprogramowanie jako pojedynczy koszt. Aby uzyskać szczegółowe wskazówki, zobacz artykuł [jak zaplanować ofertę SaaS dla komercyjnej witryny Marketplace](plan-saas-offer.md#plans).

## <a name="private-plans"></a>Plany prywatne

Możesz utworzyć plan prywatny dla oferty, zakończyć z użyciem wynegocjowanych, cen specyficznych dla transakcji lub konfiguracji niestandardowych.

Plany prywatne umożliwiają oferowanie klientom wyższych lub niższych cen niż w przypadku oferty oferowanej publicznie. Plany prywatne mogą służyć do rabatu lub dodania wersji Premium do oferty. Plany prywatne można udostępnić jednemu lub większej liczbie klientów, wymieniając swoją subskrypcję platformy Azure na poziomie planu.

## <a name="next-steps"></a>Następne kroki

- Zapoznaj się z wzorcem publikowania według sklepu online, aby zapoznać się z przykładami dotyczącymi sposobu mapowania rozwiązania na typ oferty i konfigurację.
- [Publikowanie przewodnika według typu oferty](publisher-guide-by-offer-type.md).
