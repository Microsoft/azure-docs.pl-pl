---
title: Komercyjne funkcje Transact Marketplace firmy Microsoft
description: W tym artykule opisano Cennik, rozliczenia, fakturowania i wypłaty dla opcji Transact Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/22/2020
ms.author: mingshen
author: mingshen-ms
ms.openlocfilehash: 33c3cf4b9da33b15d7a7d1c14b0c3fb892b60121
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/28/2020
ms.locfileid: "87317811"
---
# <a name="commercial-marketplace-transact-capabilities"></a>Komercyjne funkcje Transact Marketplace

## <a name="transactions-by-publishing-option"></a>Transakcje według opcji publikowania

Wydawca lub firma Microsoft odpowiada za zarządzanie transakcjami licencji na oprogramowanie dla ofert w portalu komercyjnym. Opcja publikowania wybrana dla oferty określi, kto będzie zarządzać transakcją. Zobacz [Określanie opcji publikowania](./determine-your-listing-type.md#choose-a-publishing-option) pod kątem dostępności i wyjaśnień każdej opcji publikacji.

### <a name="list-trial-and-byol-publishing-options"></a>Opcje publikowania list, wersji próbnych i BYOL

Wydawcy z istniejącymi możliwościami handlowymi mogą wybierać opcje wyświetlania listy, wersji próbnej i udostępniania licencji (BYOL) na potrzeby promocji i pozyskiwania użytkowników. Dzięki tym opcjom firma Microsoft nie uczestniczy bezpośrednio w transakcjach licencji na oprogramowanie wydawcy i nie ma żadnej powiązanej opłaty za transakcje. Wydawca jest odpowiedzialny za obsługę wszystkich aspektów transakcji związanych z licencją na oprogramowanie, w tym między innymi kolejności, realizacji, rozliczeń, fakturowania, płatności i kolekcji. Dzięki opcjom publikacji list i wersji próbnej wydawcy przechowują opłaty za licencje na oprogramowanie wydawcy 100% pobrane od klienta.

### <a name="transact-publishing-option"></a>Opcja publikowania Transact

Opcja publikowania w języku Transact wykorzystuje funkcje firmy Microsoft w handlu i oferuje kompleksowe środowisko odnajdywania i oceny do zakupu i wdrażania. Oferty usługi Transact są rozliczane w oparciu o istniejącą subskrypcję firmy Microsoft lub kartę kredytową, co pozwala firmie Microsoft na hostowanie transakcji w chmurze w witrynie Marketplace w imieniu wydawcy.

Wybierz opcję Transact podczas tworzenia nowej oferty w centrum partnerskim. Na stronie **Konfiguracja oferty** w obszarze **szczegóły instalacji**wybierz pozycję "tak, chcę sprzedawać w firmie Microsoft i mieć transakcje hosta Microsoft w moim imieniu". Ta opcja będzie wyświetlana tylko wtedy, gdy dla typu oferty jest dostępna wartość Transact.

## <a name="transact-overview"></a>Omówienie języka Transact

W przypadku korzystania z opcji publikowania w języku Transact firma Microsoft umożliwia sprzedaż oprogramowania innych firm oraz wdrażanie niektórych typów ofert do subskrypcji platformy Azure klienta. Po wybraniu modelu rozliczeń i typu oferty Wydawca musi rozważyć rozliczenie opłat za infrastrukturę oraz opłaty za korzystanie z licencji na oprogramowanie.

Opcja publikowania Transact jest obecnie obsługiwana dla następujących typów ofert:

- Maszyny wirtualne
- Aplikacje platformy Azure
- Aplikacje SaaS

### <a name="billing-infrastructure-costs"></a>Koszty infrastruktury rozliczeń

W przypadku **maszyn wirtualnych** i **aplikacji platformy**Azure opłaty za użycie infrastruktury platformy Azure są naliczane zgodnie z subskrypcją platformy Azure klienta. Opłaty za użycie infrastruktury są wyceniane i prezentowane niezależnie od opłat za Licencjonowanie dostawcy oprogramowania na fakturze klienta.

W przypadku **aplikacji SaaS**Wydawca musi uwzględnić opłaty za użycie infrastruktury platformy Azure i opłaty za licencję na oprogramowanie jako pojedynczy koszt.  Jest ona reprezentowana jako stała opłata dla klienta. Użycie infrastruktury platformy Azure jest zarządzane i rozliczane bezpośrednio do partnera. Klient nie widzi rzeczywistych opłat za użycie infrastruktury. Wydawcy zwykle wybierają opłaty za użycie infrastruktury platformy Azure do cen licencji na oprogramowanie. Opłaty za licencje na oprogramowanie nie są naliczane ani zużywane.

## <a name="transact-billing-models"></a>Modele rozliczeń Transact

W zależności od używanej opcji transakcji opłaty za licencje na oprogramowanie są następujące:

- **Bezpłatna** — brak opłat za licencje na oprogramowanie.
- **Bring Your Own License** (BYOL) — wszelkie stosowne opłaty za licencje na oprogramowanie są zarządzane bezpośrednio przez wydawcę i klienta. Firma Microsoft przechodzi wyłącznie przez opłaty za użycie infrastruktury platformy Azure. Dotyczy to tylko maszyn wirtualnych i aplikacji platformy Azure.
- **Płatność zgodnie z rzeczywistym** użyciem — opłaty za licencje na oprogramowanie są prezentowane jako stawka cenowa za godzinę (vCPU) na podstawie używanej infrastruktury platformy Azure. Dotyczy to tylko maszyn wirtualnych i aplikacji platformy Azure.
- **Cennik subskrypcji** — opłaty za licencje na oprogramowanie są prezentowane jako opłata miesięczna lub roczna, za stawkę cykliczną rozliczaną jako stawka ryczałtowa lub dla każdego stanowiska. Dotyczy to aplikacji SaaS (comiesięcznych lub rocznych) oraz aplikacji zarządzanych przez aplikacje platformy Azure (miesięcznie).
- **Bezpłatna wersja próbna oprogramowania** — brak opłat za licencje na oprogramowanie przez 30 lub 90 dni.

### <a name="free-and-bring-your-own-license-byol-pricing"></a>Bezpłatna i dowolna cena licencji (BYOL)

Przy publikowaniu oferty bezpłatnej licencji na swoją licencję firma Microsoft nie odgrywa roli w celu ułatwienia transakcji sprzedaży dla opłat za korzystanie z licencji na oprogramowanie. Podobnie jak w przypadku opcji publikowania listy i wersji próbnej, Wydawca zachowuje 100% opłat za licencje na oprogramowanie.

### <a name="pay-as-you-go-and-subscription-site-based-pricing"></a>Płatność zgodnie z rzeczywistym użyciem i subskrypcja (oparta na witrynie)

Przy publikowaniu oferty transakcji płatność zgodnie z rzeczywistym użyciem lub subskrypcji firma Microsoft udostępnia technologię i usługi do przetwarzania zakupów, zwrotów i obciążeń zwrotnych licencji na oprogramowanie. W tym scenariuszu Wydawca autoryzuje firmę Microsoft do działania jako Agent w tym celu. Wydawca pozwala firmie Microsoft na ułatwienie transakcji licencjonowania oprogramowania przy zachowaniu ich wyznaczenia jako sprzedawcy, dostawcy, dystrybutora i licencjodawcy.

Firma Microsoft umożliwia klientom zamawianie, Licencjonowanie i używanie oprogramowania zgodnie z warunkami i postanowieniami komercyjnego portalu firmy Microsoft oraz z umową licencyjną użytkownika końcowego. Musisz podać własną umowę licencyjną użytkownika oprogramowania lub wybrać [kontrakt standardowy](./standard-contract.md) podczas tworzenia oferty.

### <a name="free-software-trials"></a>Bezpłatne wersje próbne oprogramowania

W przypadku scenariuszy publikowania Transact można bezpłatnie korzystać z licencji na oprogramowanie przez 30 lub 90 dni. Ta funkcja dyskontowania nie obejmuje kosztu użycia infrastruktury platformy Azure, który jest oparty na użyciu rozwiązania partnerskiego.

### <a name="private-offers"></a>Oferty prywatne

Oprócz korzystania z typów ofert i modeli rozliczeń w celu Zarabiaj oferty, możesz wykonać transakcję prywatną, zapełnić z użyciem wynegocjowanych, cenników specyficznych dla transakcji lub konfiguracji niestandardowych. Oferty prywatne są obsługiwane przez wszystkie trzy opcje publikacji Transact.

Ta opcja umożliwia korzystanie z wyższych lub niższych cen niż publicznie dostępna oferta. Oferty prywatne mogą służyć do rabatu lub dodania wersji Premium do oferty. Oferty prywatne można udostępnić jednemu lub większej liczbie klientów, korzystając z białej listy subskrypcji platformy Azure na poziomie oferty.

### <a name="examples"></a>Przykłady

**Płatność zgodnie z rzeczywistym użyciem** 

Płatność zgodnie z rzeczywistym użyciem ma następującą strukturę kosztów:

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

Partnerzy, którzy zakwalifikowani do obniżonej opłaty za usługę Marketplace, będą widzieć obniżoną opłatę za transakcje w ofertach SaaS od maja 2019 do 2020 czerwca.

W tym scenariuszu firma Microsoft Bills $100,00 na potrzeby licencji na oprogramowanie i $90,00 płaci wydawcę:

|Rachunki firmy Microsoft  | $100,00 miesięcznie  |
|---------|---------|
|Firma Microsoft płaci 80% kosztu licencji <br> \*Firma Microsoft płaci 90% kosztu licencji dla wszystkich zakwalifikowanych aplikacji SaaS   |   $80,00 miesięcznie <br> \*$90,00 miesięcznie    |
|Firma Microsoft utrzymuje 20% kosztów licencji <br> \*Firma Microsoft utrzymuje 10% kosztów licencji na wszystkie kwalifikowane aplikacje SaaS.  |  $20,00 miesięcznie <br> \*$10,00     |

W przypadku niektórych ofert opublikowanych w portalu komercyjnym firma Microsoft obniży opłatę za usługę Marketplace z 20% (zgodnie z opisem w umowie Microsoft Publisher) do 10%. Oferty, które chcesz zakwalifikować, muszą zostać wystawione przez firmę Microsoft jako usługa Azure IP incetivized. Przed końcem każdego miesiąca kalendarzowego należy spełnić co najmniej pięć dni roboczych, aby otrzymać obniżoną opłatę za usługę Marketplace dla danego miesiąca. Zmniejszona opłata za usługę Marketplace dotyczy współsprzedaży platformy Azure z zachęcani SaaS, maszyn wirtualnych, aplikacji zarządzanych i innych ofert IaaS z obsługą transakcji, które są udostępniane za pośrednictwem komercyjnej witryny Marketplace.

### <a name="customer-invoicing-payment-billing-and-collections"></a>Faktury dla klientów, płatności, rozliczeń i kolekcji

**Fakturowanie i płatność** — za pomocą preferowanej metody fakturowania klienta możesz dostarczać subskrypcję lub PAYGO opłaty za licencje na oprogramowanie.

**Umowa Enterprise** — Jeśli preferowana metoda fakturowania klienta to Microsoft Umowa Enterprise, opłaty za licencje na oprogramowanie będą naliczane przy użyciu tej metody fakturowania jako koszt określony przez użytkownika, niezależnie od kosztów użycia specyficznych dla platformy Azure.

**Karty kredytowe i faktury miesięczne** — klienci mogą również skorzystać z karty kredytowej i faktury miesięcznej. W tym przypadku opłaty za licencję na oprogramowanie będą rozliczane w taki sam sposób jak scenariusz Umowa Enterprise, jako koszt określony, niezależnie od kosztów użycia specyficznych dla platformy Azure.

**Bezpłatne kredyty i zobowiązania pieniężne** — niektórzy klienci wybierają przedpłatę platformy Azure z zobowiązaniem pieniężnym w ramach Umowa Enterprise lub otrzymali bezpłatne kredyty na korzystanie z platformy Azure. Chociaż te kredyty mogą być używane do płacenia za użycie platformy Azure, nie mogą być używane do płacenia opłat za licencje na oprogramowanie wydawcy.

**Rozliczenia i kolekcje** — rozliczanie licencji na oprogramowanie wydawcy jest prezentowane przy użyciu metody wystawiania przez klienta w ramach faktury, która jest zgodna z osią czasu fakturowania. Klienci bez Umowa Enterprise są rozliczani miesięcznie za licencje na oprogramowanie w portalu Marketplace. Klienci z Umowa Enterprise są rozliczani miesięcznie za pośrednictwem faktury prezentowanej kwartalnie.

W przypadku wybrania modeli cenowych subskrypcja lub płatność zgodnie z rzeczywistym użyciem firma Microsoft działa jako agent wydawcy i odpowiada za wszystkie aspekty rozliczeń, płatności i kolekcji.

### <a name="publisher-payout-and-reporting"></a>Wypłata i raportowanie wydawcy

Wszelkie opłaty za licencje na oprogramowanie zebrane przez firmę Microsoft jako agent podlegają 20% opłatą za transakcję, chyba że określono inaczej i są one odejmowane w momencie wypłaty wydawcy.

Klienci zazwyczaj kupują przy użyciu Umowa Enterprise lub z włączoną kartą kredytową płatność zgodnie z rzeczywistym użyciem. Typ umowy określa rozliczenia, fakturowanie, zbieranie i czas wypłaty.

>[!NOTE]
>Wszystkie raporty i szczegółowe informacje dotyczące opcji publikowania Transact są dostępne za pośrednictwem sekcji Analiza w centrum partnerskim.

#### <a name="billing-questions-and-support"></a>Pytania i pomoc techniczna dotyczące rozliczeń

Aby uzyskać więcej informacji i zasad prawnych, zobacz [umowę wydawcy](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE3ypvt) (dostępną w centrum partnerskim).

Aby uzyskać pomoc dotyczącą pytań dotyczących rozliczeń, skontaktuj się z [pomocą techniczną wydawcy portalu Marketplace](https://aka.ms/marketplacepublishersupport).

## <a name="transact-requirements"></a>Wymagania dotyczące języka Transact

Ta sekcja obejmuje wymagania dotyczące usługi Transact dla różnych typów ofert.

### <a name="requirements-for-all-offer-types"></a>Wymagania dotyczące wszystkich typów ofert

- Dla opcji publikowania Transact są wymagane konto Microsoft i informacje finansowe, niezależnie od modelu cen oferty.
- Obowiązkowe informacje finansowe obejmują konto płatne i profil podatkowy.

Aby uzyskać więcej informacji na temat konfigurowania tych kont, zobacz [Zarządzanie kontem komercyjnej witryny Marketplace w centrum partnerskim](partner-center-portal/manage-account.md).

### <a name="requirements-for-specific-offer-types"></a>Wymagania dotyczące konkretnych typów ofert

Opcja publikowania Transact jest dostępna tylko dla następujących typów ofert w portalu Marketplace:

- **Maszyna wirtualna** — wybierz opcję bezpłatnie, Przenieś własną licencję lub model z płatność zgodnie z rzeczywistym użyciem i zapoznaj się z planami zdefiniowanymi na poziomie oferty. Na rachunku na platformie Azure klienta firma Microsoft przedstawia opłaty za licencję na oprogramowanie wydawcy niezależnie od opłat związanych z infrastrukturą platformy Azure. Opłaty za infrastrukturę platformy Azure są oparte na oprogramowaniu wydawcy.

- **Aplikacja platformy Azure: szablon rozwiązania lub aplikacja zarządzana** — należy udostępnić co najmniej jedną maszynę wirtualną i pobrać sumę cen maszyn wirtualnych. W przypadku aplikacji zarządzanych w ramach jednego planu można wybrać jako model cenowy miesięczną subskrypcję ryczałtową, a zamiast cennika maszyny wirtualnej. W niektórych przypadkach opłaty za użycie infrastruktury platformy Azure są przesyłane do klienta oddzielnie od opłat za licencje na oprogramowanie, ale w tej samej zestawie rozliczeń. Jednak w przypadku skonfigurowania oferty zarządzanej aplikacji dla dostawców niezależnego dostawcy oprogramowania zasoby platformy Azure są rozliczane dla wydawcy, a klient otrzymuje stałą opłatę obejmującą koszt infrastruktury, licencje na oprogramowanie i usługi zarządzania.

- **Aplikacja SaaS** — musi być rozwiązaniem z wieloma dzierżawcami, używać [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) do uwierzytelniania i integrują się z [interfejsami API realizacji SaaS](partner-center-portal/pc-saas-fulfillment-api-v2.md). Użycie infrastruktury platformy Azure jest zarządzane i rozliczane bezpośrednio dla Ciebie (partner), więc musisz uwzględnić opłaty za użycie infrastruktury platformy Azure i opłaty za licencję na oprogramowanie jako pojedynczy koszt. Aby uzyskać szczegółowe wskazówki, zobacz [Tworzenie nowej oferty SaaS w komercyjnej witrynie Marketplace](partner-center-portal/create-new-saas-offer.md).

## <a name="next-steps"></a>Następne kroki

- Zapoznaj się z wymaganiami dotyczącymi kwalifikacji w sekcji Opcje publikowania według typu oferty, aby zakończyć wybór i konfigurację oferty.
- Zapoznaj się z wzorcem publikowania według witryny sklepu, aby zapoznać się z przykładami dotyczącymi sposobu mapowania rozwiązania na typ oferty i konfigurację.
