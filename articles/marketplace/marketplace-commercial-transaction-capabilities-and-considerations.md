---
title: Komercyjne funkcje Transact Marketplace firmy Microsoft
description: W tym artykule opisano Cennik, rozliczenia, fakturowania i wypłaty dla opcji Transact Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: mingshen
author: mingshen-ms
ms.openlocfilehash: c6ce256db907589ace7e77157397257732564fb6
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93130327"
---
# <a name="commercial-marketplace-transact-capabilities"></a>Komercyjne funkcje Transact Marketplace

W tym artykule opisano zagadnienia dotyczące cen, rozliczeń, fakturowania i wypłaty dla komercyjnego portalu Microsoft Marketplace. 

## <a name="transactions-by-listing-option"></a>Opcja transakcji według listy

Wydawca lub firma Microsoft odpowiada za zarządzanie transakcjami licencji na oprogramowanie dla ofert w portalu komercyjnym. Opcja wystaw wybrana dla oferty określa, kto będzie zarządzał transakcją. Zobacz [Wybierz opcję wyświetlania listy](./determine-your-listing-type.md#choose-a-listing-option) dostępności i wyjaśnień każdej opcji publikacji.

### <a name="contact-me-free-trial-and-byol-options"></a>Skontaktuj się z nami, bezpłatnej wersji próbnej i opcji BYOL

Wydawcy mogą wybrać opcje _kontakt z_ nami i _bezpłatna wersja próbna_ , które umożliwiają promocję i pozyskiwanie użytkowników. W przypadku niektórych typów ofert wydawcy mogą wybrać opcję "Przenieś własną licencję" (BYOL), aby umożliwić klientom kupowanie subskrypcji oferty przy użyciu licencji zakupionej bezpośrednio od Ciebie. Dzięki tym opcjom firma Microsoft nie uczestniczy bezpośrednio w transakcjach licencji na oprogramowanie wydawcy i nie ma żadnej powiązanej opłaty za transakcje. 

Wydawca jest odpowiedzialny za obsługę wszystkich aspektów transakcji licencjonowania oprogramowania. Obejmuje to, ale nie jest ograniczone do kolejności, realizacji, pomiaru, rozliczeń, fakturowania, płatności i kolekcji. Po wybraniu opcji kontakt ze mną wydawcy zadbają o 100% opłat za Licencjonowanie oprogramowania wydawcy zebranych od klienta.

### <a name="transact-publishing-option"></a>Opcja publikowania Transact

Wprowadzenie do sprzedaży za pośrednictwem firmy Microsoft wykorzystuje możliwości firmy Microsoft w handlu i oferuje kompleksowe środowisko wykrywania i oceny do zakupu i implementacji. Dostępna jest oferta, w której firma Microsoft ułatwia wymianę pieniędzy na licencję na oprogramowanie w imieniu wydawcy. Oferty transakcyjne są rozliczane w oparciu o istniejącą subskrypcję firmy Microsoft lub kartę kredytową, co umożliwia firmie Microsoft hostowanie transakcji w chmurze Marketplace w imieniu wydawcy.

Wybierz opcję Transact podczas tworzenia nowej oferty w centrum partnerskim. Ta opcja będzie wyświetlana tylko wtedy, gdy dla typu oferty jest dostępna wartość Transact.

## <a name="transact-overview"></a>Omówienie języka Transact

W przypadku korzystania z opcji Transact firma Microsoft umożliwia sprzedaż oprogramowania innych firm i wdrażanie niektórych typów ofert do subskrypcji platformy Azure klienta. Po wybraniu modelu cen dla oferty Wydawca musi rozważyć rozliczenie opłat za infrastrukturę i opłat za korzystanie z licencji na oprogramowanie.

Opcja publikowania Transact jest obecnie obsługiwana dla następujących typów ofert:

- Maszyny wirtualne
- Aplikacje platformy Azure
- Aplikacje SaaS

### <a name="billing-infrastructure-costs"></a>Koszty infrastruktury rozliczeń

W przypadku **maszyn wirtualnych** i **aplikacji platformy** Azure opłaty za użycie infrastruktury platformy Azure są naliczane zgodnie z subskrypcją platformy Azure klienta. Opłaty za użycie infrastruktury są wyceniane i prezentowane niezależnie od opłat za Licencjonowanie dostawcy oprogramowania na fakturze klienta.

W przypadku **aplikacji SaaS** Wydawca musi uwzględnić opłaty za użycie infrastruktury platformy Azure i opłaty za licencję na oprogramowanie jako pojedynczy koszt.  Jest ona reprezentowana jako stała opłata dla klienta. Użycie infrastruktury platformy Azure jest zarządzane i rozliczane bezpośrednio przez wydawcę. Klient nie widzi rzeczywistych opłat za użycie infrastruktury. Wydawcy zwykle wybierają opłaty za użycie infrastruktury platformy Azure do cen licencji na oprogramowanie. Opłaty za licencje na oprogramowanie nie są naliczane lub oparte na zużyciu użytkownika.

## <a name="pricing-models"></a>Modele cenowe

W zależności od używanej opcji transakcji opłaty za subskrypcję są następujące:

- **Pobierz teraz (bezpłatnie)** — bez opłat za licencje na oprogramowanie. Klienci nie są obciążani opłatami za korzystanie z bezpłatnej oferty usługi Azure Marketplace. Bezpłatnych ofert nie można przekonwertować na płatną ofertę. Klienci muszą zamówić płatną ofertę.
- **Bring Your Own License** (BYOL) — wszelkie stosowne opłaty za licencje na oprogramowanie są zarządzane bezpośrednio przez wydawcę i klienta. Firma Microsoft przechodzi wyłącznie przez opłaty za użycie infrastruktury platformy Azure. Jeśli oferta zostanie wyświetlona w komercyjnej witrynie Marketplace, klienci, którzy uzyskują dostęp lub korzystanie z oferty poza komercyjnym rynkiem Marketplace, nie są obciążani opłatami komercyjnymi.
- **Cennik subskrypcji** — opłaty za licencje na oprogramowanie są prezentowane jako miesięczna lub roczna opłata za subskrypcję cykliczną rozliczaną jako stała stawka lub za stanowisko. Opłaty za bieżącą subskrypcję nie są naliczane proporcjonalnie za krótkoterminowe anulowania klientów lub nieużywane usługi. Opłaty za bieżącą subskrypcję mogą być naliczane proporcjonalnie w przypadku, gdy klient uaktualni lub obniży swoją subskrypcję w trakcie okresu subskrypcji.
- **Cennik oparty na użyciu** — w przypadku ofert związanych z maszynami wirtualnymi platformy Azure opłaty są naliczone w oparciu o zakres korzystania z oferty. W przypadku obrazów maszyn wirtualnych klienci są obciążani opłatą za godzinę w witrynie Azure Marketplace ustawioną przez wydawcę w celu korzystania z maszyn wirtualnych wdrożonych w obrazach maszyny wirtualnej. Opłata godzinowa może być jednorodna lub różna w rozmiarach maszyn wirtualnych. Godziny częściowe są rozliczone przez minutę. Plany są rozliczane miesięcznie.
- **Cennik taryfowy** — w przypadku ofert opartych na usłudze Azure Applications i SaaS, wydawcy mogą używać [usługi pomiaru Marketplace](./partner-center-portal/marketplace-metering-service-apis.md) do rozliczania za użycie w oparciu o wybrane przez siebie wymiary miernika. Na przykład przepustowość, bilety lub przetworzone wiadomości e-mail. Wydawcy mogą definiować jeden lub więcej wymiarów taryfowych dla każdego planu. Wydawcy są odpowiedzialni za śledzenie użycia poszczególnych klientów, przy czym każdy licznik został zdefiniowany w ofercie. Zdarzenia powinny być zgłaszane firmie Microsoft w ciągu godziny. Firma Microsoft obciąża klientów na podstawie informacji o użyciu zgłoszonych przez wydawców w odpowiednim okresie rozliczeniowym.
- **Bezpłatna wersja próbna** — bez opłat za licencje na oprogramowanie należące do zakresu od 30 dni do sześciu miesięcy, w zależności od typu oferty. Jeśli wydawcy przestaną korzystać z bezpłatnej wersji próbnej dla wielu planów w ramach tej samej oferty, klienci mogą przejść do bezpłatnej wersji próbnej w innym planie, ale okres próbny nie zostanie uruchomiony ponownie. W przypadku ofert związanych z maszynami wirtualnymi klienci są obciążani kosztami infrastruktury platformy Azure za korzystanie z oferty w okresie próbnym. Po zakończeniu okresu próbnego klienci są obciążani automatycznie opłatą za ostatni plan, który podjął próbę w oparciu o stawki standardowe, chyba że zostaną anulowane przed końcem okresu próbnego.

> [!NOTE]
> Oferty, które są rozliczane zgodnie z zużyciem po użyciu rozwiązania, nie kwalifikują się do zwrotów.

Wydawcy, którzy chcą zmienić opłaty za użycie skojarzone z ofertą, powinni najpierw usunąć ofertę (lub plan z oferty) z komercyjnej witryny Marketplace. Usuwanie powinno odbywać się zgodnie z wymaganiami [umowy wydawcy firmy Microsoft](https://go.microsoft.com/fwlink/?LinkID=699560). Następnie Wydawca może opublikować nową ofertę (lub plan w ramach oferty), która obejmuje nowe opłaty za użycie. Aby uzyskać więcej informacji o usuwaniu oferty lub planu, zobacz sekcję [stop sprzedawanie oferty lub planu](./partner-center-portal/update-existing-offer.md#stop-selling-an-offer-or-plan).

### <a name="free-contact-me-and-bring-your-own-license-byol-pricing"></a>Bezpłatna, skontaktuj się z nami i przeniesiej własne ceny licencji (BYOL)

Podczas publikowania oferty przy użyciu opcji Pobierz teraz (bezpłatnie), skontaktuj się z nami lub BYOL, firma Microsoft nie odgrywa roli w celu ułatwienia transakcji sprzedaży dla opłat za licencję na oprogramowanie. Podobnie jak w przypadku opcji publikowania listy i bezpłatnej wersji próbnej, Wydawca zachowuje 100% opłat za licencje na oprogramowanie.

### <a name="usage-based-and-subscription-pricing"></a>Cennik oparty na użyciu i subskrypcja

Przy publikowaniu oferty opartej na użytkowniku lub subskrypcji firma Microsoft udostępnia technologie i usługi do przetwarzania zakupów licencji na oprogramowanie, zwraca i obciążeń zwrotnych. W tym scenariuszu Wydawca autoryzuje firmę Microsoft do działania jako Agent w tym celu. Wydawca pozwala firmie Microsoft na ułatwienie transakcji licencjonowania oprogramowania przy zachowaniu ich wyznaczenia jako sprzedawcy, dostawcy, dystrybutora i licencjodawcy.

Firma Microsoft umożliwia klientom zamawianie, Licencjonowanie i używanie oprogramowania zgodnie z warunkami i postanowieniami komercyjnego portalu firmy Microsoft oraz z umową licencyjną użytkownika końcowego. Musisz podać własną umowę licencyjną użytkownika oprogramowania lub wybrać [kontrakt standardowy](./standard-contract.md) podczas tworzenia oferty.

### <a name="free-software-trials"></a>Bezpłatne wersje próbne oprogramowania

W przypadku scenariuszy publikowania Transact można bezpłatnie udostępnić licencje na oprogramowanie od 30 do 120 dni, w zależności od subskrypcji. Ta funkcja dyskontowania nie obejmuje kosztu użycia infrastruktury platformy Azure, który jest oparty na użyciu rozwiązania partnerskiego.

### <a name="private-offers"></a>Oferty prywatne

Oprócz korzystania z typów ofert i modeli rozliczeń w celu Zarabiaj oferty, możesz wykonać transakcję prywatną, zapełnić z użyciem wynegocjowanych, cenników specyficznych dla transakcji lub konfiguracji niestandardowych. Oferty prywatne są obsługiwane przez wszystkie trzy opcje publikacji Transact.

Ta opcja umożliwia korzystanie z wyższych lub niższych cen niż publicznie dostępna oferta. Oferty prywatne mogą służyć do rabatu lub dodania wersji Premium do oferty. Oferty prywatne można udostępnić jednemu lub większej liczbie klientów, korzystając z białej listy subskrypcji platformy Azure na poziomie oferty.

### <a name="examples"></a>Przykłady

**Na podstawie użycia** 

Cennik oparty na użyciu ma następującą strukturę kosztów:

|Koszt licencji  | $1,00 za godzinę   |
|---------|---------|
|Koszt użycia platformy Azure (D1/1-rdzeniowe)    |   $0,14 za godzinę     |
|*Klient jest rozliczany przez firmę Microsoft*    |  *$1,14 za godzinę*       |
||

W tym scenariuszu firma Microsoft rozlicza $1,14 za godzinę w celu użycia opublikowanego obrazu maszyny wirtualnej.

|Rachunki firmy Microsoft  | $1,14 za godzinę  |
|---------|---------|
|Firma Microsoft płaci 80% kosztu licencji|   $0,80 za godzinę     |
|Firma Microsoft utrzymuje 20% kosztów licencji  |  $0,20 za godzinę       |
|Firma Microsoft utrzymuje 100% kosztów użycia platformy Azure | $0,14 za godzinę |
||

**Bring Your Own License (BYOL)**

BYOL ma następującą strukturę kosztów:

|Koszt licencji  | Opłata licencyjna wynegocjowana i rozliczona przez użytkownika  |
|---------|---------|
|Koszt użycia platformy Azure (D1/1-rdzeniowe)    |   $0,14 za godzinę     |
|*Klient jest rozliczany przez firmę Microsoft*    |  *$0,14 za godzinę*       |
||

W tym scenariuszu firma Microsoft rozlicza $0,14 za godzinę w celu użycia opublikowanego obrazu maszyny wirtualnej.

|Rachunki firmy Microsoft  | $0,14 za godzinę  |
|---------|---------|
|Firma Microsoft utrzymuje koszt użycia platformy Azure    |   $0,14 za godzinę     |
|Firma Microsoft utrzymuje 0% kosztu licencji   |  $0,00 za godzinę       |
||

**Subskrypcja aplikacji SaaS**

Ta opcja musi być skonfigurowana do sprzedaży za pomocą firmy Microsoft i może być naliczana według stawki miesięcznej lub rocznej. Po włączeniu opcji **sprzedaży za pomocą firmy Microsoft** dla oferty SaaS masz następującą strukturę kosztów:

| Koszt licencji       | $100,00 miesięcznie  |
|--------------|---------|
| Koszt użycia platformy Azure (D1/1-rdzeniowe)    | Rozliczana bezpośrednio z wydawcą, a nie klientem |
| *Klient jest rozliczany przez firmę Microsoft*    |  *$100,00 miesięcznie (w przypadku wydawców należy uwzględnić koszty związane z założeniami lub przekazaniem infrastruktury w ramach opłaty licencyjnej)*  |
||

W tym scenariuszu firma Microsoft Bills $100,00 na potrzeby licencji na oprogramowanie i płaci wydawcę $80,00.

W tym scenariuszu firma Microsoft Bills $100,00 na potrzeby licencji na oprogramowanie i $90,00 płaci wydawcę:

|Rachunki firmy Microsoft  | $100,00 miesięcznie  |
|---------|---------|
|Firma Microsoft płaci 80% kosztu licencji <br> \* Firma Microsoft płaci 90% kosztu licencji dla wszystkich zakwalifikowanych aplikacji SaaS   |   $80,00 miesięcznie <br> \* $90,00 miesięcznie    |
|Firma Microsoft utrzymuje 20% kosztów licencji <br> \* Firma Microsoft utrzymuje 10% kosztów licencji na wszystkie kwalifikowane aplikacje SaaS.  |  $20,00 miesięcznie <br> \* $10,00     |

### <a name="reduced-service-fee"></a>Obniżona opłata za usługę

W przypadku niektórych ofert opublikowanych w portalu komercyjnym firma Microsoft obniży opłatę za usługę Marketplace z 20% (zgodnie z opisem w [umowie Microsoft Publisher](https://go.microsoft.com/fwlink/?LinkID=699560)) do 10%. Oferty, które chcesz zakwalifikować, muszą zostać wystawione przez firmę Microsoft jako usługa Azure IP zachęcani. Aby otrzymać obniżoną opłatę za usługę Marketplace w danym miesiącu, należy spełnić co najmniej pięć dni roboczych przed końcem każdego miesiąca kalendarzowego. Zmniejszona opłata za usługę Marketplace dotyczy współsprzedaży platformy Azure zachęcani SaaS, maszyn wirtualnych, aplikacji zarządzanych i innych oferowanych z nich ofert IaaS z obsługą transakcji dostępnych za pośrednictwem komercyjnej witryny Marketplace. Płatne oferty SaaS skojarzone z jedną aplikacją Microsoft Teams lub co najmniej dwa dodatki Microsoft 365 (Excel, PowerPoint, Word, Outlook i SharePoint) i opublikowane w witrynie AppSource otrzymują również rabat.

### <a name="customer-invoicing-payment-billing-and-collections"></a>Faktury dla klientów, płatności, rozliczeń i kolekcji

**Fakturowanie i płatność** — za pomocą preferowanej metody fakturowania klienta możesz dostarczać subskrypcję lub PAYGO opłaty za licencje na oprogramowanie.

**Umowa Enterprise** — Jeśli preferowana metoda fakturowania klienta to Microsoft Umowa Enterprise, opłaty za licencje na oprogramowanie będą naliczane przy użyciu tej metody fakturowania jako koszt określony przez użytkownika, niezależnie od kosztów użycia specyficznych dla platformy Azure.

**Karty kredytowe i faktury miesięczne** — klienci mogą również skorzystać z karty kredytowej i faktury miesięcznej. W tym przypadku opłaty za licencję na oprogramowanie będą rozliczane w taki sam sposób jak scenariusz Umowa Enterprise, jako koszt określony, niezależnie od kosztów użycia specyficznych dla platformy Azure.

**Bezpłatne kredyty i zobowiązania pieniężne** — niektórzy klienci wybierają przedpłatę platformy Azure z zobowiązaniem pieniężnym w ramach Umowa Enterprise lub otrzymali bezpłatne kredyty na korzystanie z platformy Azure. Chociaż te kredyty mogą być używane do płacenia za użycie platformy Azure, nie mogą być używane do płacenia opłat za licencje na oprogramowanie wydawcy.

**Rozliczenia i kolekcje** — rozliczanie licencji na oprogramowanie wydawcy jest prezentowane przy użyciu metody fakturowania w wybranym przez klienta i postępuj według czasu fakturowania. Klienci bez Umowa Enterprise są rozliczani miesięcznie za licencje na oprogramowanie w portalu Marketplace. Klienci z Umowa Enterprise są rozliczani miesięcznie za pośrednictwem faktury prezentowanej kwartalnie.

W przypadku wybrania modeli cenowych subskrypcja lub płatność zgodnie z rzeczywistym użyciem firma Microsoft działa jako agent wydawcy i odpowiada za wszystkie aspekty rozliczeń, płatności i kolekcji.

### <a name="publisher-payout-and-reporting"></a>Wypłata i raportowanie wydawcy

Wszelkie opłaty za licencje na oprogramowanie zebrane przez firmę Microsoft jako agent podlegają 20% opłatą za transakcję, chyba że określono inaczej i są one odejmowane w momencie wypłaty wydawcy.

Klienci zazwyczaj kupują przy użyciu Umowa Enterprise lub z włączoną kartą kredytową płatność zgodnie z rzeczywistym użyciem. Typ umowy określa rozliczenia, fakturowanie, zbieranie i czas wypłaty.

>[!NOTE]
>Wszystkie raporty i szczegółowe informacje dotyczące opcji publikowania Transact są dostępne za pośrednictwem sekcji Analiza w centrum partnerskim.

#### <a name="billing-questions-and-support"></a>Pytania i pomoc techniczna dotyczące rozliczeń

Aby uzyskać więcej informacji i zasad prawnych, zobacz [umowę wydawcy firmy Microsoft](https://go.microsoft.com/fwlink/?LinkID=699560) (dostępną w centrum partnerskim).

Aby uzyskać pomoc dotyczącą pytań dotyczących rozliczeń, skontaktuj się z [pomocą techniczną wydawcy portalu Marketplace](https://aka.ms/marketplacepublishersupport).

## <a name="transact-requirements"></a>Wymagania dotyczące języka Transact

Ta sekcja obejmuje wymagania dotyczące usługi Transact dla różnych typów ofert.

### <a name="requirements-for-all-offer-types"></a>Wymagania dotyczące wszystkich typów ofert

- Dla opcji publikowania Transact są wymagane konto Microsoft i informacje finansowe, niezależnie od modelu cen oferty.
- Obowiązkowe informacje finansowe obejmują konto płatne i profil podatkowy.
- Wydawca musi znajdować się w [obsługiwanym kraju lub regionie](sell-from-countries.md).

Aby uzyskać więcej informacji na temat konfigurowania tych kont, zobacz [Zarządzanie kontem komercyjnej witryny Marketplace w centrum partnerskim](partner-center-portal/manage-account.md).

### <a name="requirements-for-specific-offer-types"></a>Wymagania dotyczące konkretnych typów ofert

Opcja publikowania Transact jest dostępna tylko dla następujących typów ofert w portalu Marketplace:

- **Maszyna wirtualna platformy Azure** — umożliwia wybór opcji bezpłatnych, samodzielnych i opartych na użyciu modeli cenowych i dostępnych jako planów zdefiniowanych na poziomie oferty. Na rachunku na platformie Azure klienta firma Microsoft przedstawia opłaty za licencję na oprogramowanie wydawcy niezależnie od opłat związanych z infrastrukturą platformy Azure. Opłaty za infrastrukturę platformy Azure są oparte na oprogramowaniu wydawcy.

- **Aplikacja platformy Azure: szablon rozwiązania lub aplikacja zarządzana** — należy udostępnić co najmniej jedną maszynę wirtualną i pobrać sumę cen maszyn wirtualnych. W przypadku aplikacji zarządzanych w ramach jednego planu można wybrać jako model cenowy miesięczną subskrypcję ryczałtową, a zamiast cennika maszyny wirtualnej. W niektórych przypadkach opłaty za użycie infrastruktury platformy Azure są przesyłane do klienta oddzielnie od opłat za licencje na oprogramowanie, ale w tej samej zestawie rozliczeń. Jednak w przypadku skonfigurowania oferty zarządzanej aplikacji dla dostawców niezależnego dostawcy oprogramowania zasoby platformy Azure są rozliczane dla wydawcy, a klient otrzymuje stałą opłatę obejmującą koszt infrastruktury, licencje na oprogramowanie i usługi zarządzania.

- **Aplikacja SaaS** — musi być rozwiązaniem z wieloma dzierżawcami, używać [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) do uwierzytelniania i integrują się z [interfejsami API realizacji SaaS](partner-center-portal/pc-saas-fulfillment-api-v2.md). Użycie infrastruktury platformy Azure jest zarządzane i rozliczane bezpośrednio dla Ciebie (partner), więc musisz uwzględnić opłaty za użycie infrastruktury platformy Azure i opłaty za licencję na oprogramowanie jako pojedynczy koszt. Aby uzyskać szczegółowe wskazówki, zobacz [Tworzenie nowej oferty SaaS w komercyjnej witrynie Marketplace](./create-new-saas-offer.md).

## <a name="next-steps"></a>Następne kroki

- Zapoznaj się z wymaganiami dotyczącymi kwalifikacji w sekcji Opcje publikowania według typu oferty, aby zakończyć wybór i konfigurację oferty.
- Zapoznaj się z wzorcem publikowania według sklepu online, aby zapoznać się z przykładami dotyczącymi sposobu mapowania rozwiązania na typ oferty i konfigurację.