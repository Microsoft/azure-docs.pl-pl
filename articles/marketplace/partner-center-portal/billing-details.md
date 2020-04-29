---
title: Komercyjne rozliczenia w portalu Marketplace | Portal Azure Marketplace
description: W tym artykule omówiono tematy związane z handlem przeznaczone dla komercyjnych rynków marketingowych.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.topic: guide
ms.date: 12/12/2019
ms.openlocfilehash: a0912e1dca63fabfe6bc546305824a1c582b9a2d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80279746"
---
# <a name="commercial-marketplace-billing"></a>Rozliczenia komercyjnej platformy handlowej

W tym artykule omówiono tematy związane z handlem dla komercyjnego portalu Marketplace:

- [Opcje publikowania witryny Marketplace](#marketplace-publishing-options)
- [Ogólne omówienie języka Transact](#transact-general-overview)
- [Modele rozliczeń Transact](#transact-billing-models)

## <a name="marketplace-publishing-options"></a>Opcje publikowania witryny Marketplace

Komercyjne witryny Marketplace oferują kilka opcji publikowania dla wydawców.

### <a name="list-and-trial-publishing-options"></a>Opcje publikowania list i wersji próbnej

Wydawcy mogą korzystać z listy, wersji próbnej i zapewnić własne opcje publikowania licencji (BYOL) na potrzeby promocji i pozyskiwania użytkowników. Dzięki tym opcjom firma Microsoft nie uczestniczy bezpośrednio w transakcjach licencji na oprogramowanie wydawcy i nie ma żadnej skojarzonej opłaty za transakcje. Wydawca jest odpowiedzialny za obsługę wszystkich aspektów transakcji związanych z licencją na oprogramowanie, w tym między innymi: kolejności, realizacji, pomiaru, rozliczeń, fakturowania, płatności i kolekcji. Dzięki opcjom publikacji list i wersji próbnej wydawcy przechowują opłaty za licencje na oprogramowanie wydawcy 100% pobrane od klienta.

### <a name="transact-publishing-option"></a>Opcja publikowania Transact

Oprócz opcji publikowania listy i wersji próbnej opcja publikacji Transact jest dostępna dla wydawców. Ta opcja wykorzystuje dostępne globalnie funkcje handlu firmy Microsoft i umożliwia firmie Microsoft hostowanie transakcji w chmurze w imieniu wydawcy.

## <a name="transact-general-overview"></a>Ogólne omówienie języka Transact

W przypadku korzystania z opcji publikowania Transact firma Microsoft umożliwia sprzedaż oprogramowania innych firm oraz wdrażanie niektórych typów ofert do subskrypcji platformy Azure klienta. W przypadku wybrania modelu rozliczeń i typu oferty Wydawca musi rozważyć rozliczenie opłat za infrastrukturę oraz własne opłaty za korzystanie z licencjonowania oprogramowania.

Opcja publikowania Transact jest obecnie obsługiwana dla następujących typów ofert: maszyn wirtualnych, aplikacji platformy Azure i aplikacji SaaS.

![Język Transact w portalu Azure Marketplace](./media/transact-amp.png)

### <a name="billing-infrastructure-costs"></a>Koszty infrastruktury rozliczeń

#### <a name="for-virtual-machines-and-azure-applications"></a>Dla maszyn wirtualnych i aplikacji platformy Azure

W przypadku maszyn wirtualnych i aplikacji platformy Azure opłaty za użycie infrastruktury platformy Azure są naliczane zgodnie z subskrypcją platformy Azure klienta. Opłaty za użycie infrastruktury są wyceniane i prezentowane niezależnie od opłat za Licencjonowanie dostawcy oprogramowania na fakturze klienta.

#### <a name="for-saas-apps"></a>Dla aplikacji SaaS

W przypadku aplikacji SaaS Wydawca musi uwzględnić opłaty za użycie infrastruktury platformy Azure oraz opłaty za licencję na oprogramowanie jako pojedynczy koszt. Jest ona reprezentowana jako stała opłata dla klienta. Użycie infrastruktury platformy Azure jest zarządzane i rozliczane bezpośrednio do partnera. Klient nie widzi rzeczywistych opłat za użycie infrastruktury. Wydawcy zwykle wybierają opłaty za użycie infrastruktury platformy Azure do cen licencji na oprogramowanie. Opłaty za licencje na oprogramowanie nie są naliczane ani zużywane.

## <a name="transact-billing-models"></a>Modele rozliczeń Transact

W zależności od używanej opcji transakcji można przedstawić opłaty za licencję na oprogramowanie wydawcy w następujący sposób:

- *Bezpłatna*: Brak opłat za licencje na oprogramowanie.
- *Bring Your Own License (BYOL)*: wszelkie stosowne opłaty za licencje na oprogramowanie są zarządzane bezpośrednio przez wydawcę i klienta. Firma Microsoft przechodzi wyłącznie przez opłaty za użycie infrastruktury platformy Azure. (Tylko aplikacje dla Virtual Machines i platformy Azure).
- *Płatność zgodnie z rzeczywistym*użyciem: opłaty za licencje na oprogramowanie są prezentowane jako stawka cenowa za godzinę (vCPU) na podstawie używanej infrastruktury platformy Azure. Ten model ma zastosowanie tylko do maszyn wirtualnych i aplikacji platformy Azure.
- *Cennik subskrypcji*: opłaty za licencje na oprogramowanie są prezentowane jako comiesięczne lub roczne, opłata cykliczna jest naliczana jako stała stawka lub za stanowisko. Ten model ma zastosowanie tylko do aplikacji SaaS i aplikacji zarządzanych przez aplikacje platformy Azure.
- *Bezpłatna wersja próbna oprogramowania*: Brak opłat za licencje na oprogramowanie przez 30 dni lub 90 dni.

### <a name="free-and-bring-your-own-license-byol-pricing"></a>Bezpłatna i dowolna cena licencji (BYOL)

Przy publikowaniu oferty bezpłatnej licencji na swoją licencję firma Microsoft nie odgrywa roli w celu ułatwienia transakcji sprzedaży dla opłat za korzystanie z licencji na oprogramowanie. Podobnie jak w przypadku opcji publikowania listy i wersji próbnej, Wydawca zachowuje 100% opłat za licencje na oprogramowanie.

### <a name="pay-as-you-go-and-subscription-site-based-pricing"></a>Płatność zgodnie z rzeczywistym użyciem i subskrypcja (oparta na witrynie)

Przy publikowaniu oferty transakcji płatność zgodnie z rzeczywistym użyciem lub subskrypcji firma Microsoft udostępnia technologie i usługi do przetwarzania zakupów licencji na oprogramowanie, powrotu i naliczania opłat. W tym scenariuszu Wydawca autoryzuje firmę Microsoft do działania jako Agent w tym celu. Wydawca pozwala firmie Microsoft na ułatwienie transakcji licencjonowania oprogramowania przy zachowaniu ich wyznaczenia jako sprzedawcy, dostawcy, dystrybutora i licencjodawcy.

Firma Microsoft umożliwia klientom zamówienie, licencję i korzystanie z oprogramowania wydawcy, z uwzględnieniem warunków i postanowień firmy Microsoft komercyjnych Marketplace oraz umowy licencyjnej użytkownika oprogramowania wydawcy. Wydawcy muszą zapewnić swoją umowę licencyjną użytkownika końcowego lub wybrać [kontrakt standardowy](https://docs.microsoft.com/azure/marketplace/standard-contract) podczas tworzenia oferty.

### <a name="free-software-trials"></a>Bezpłatne wersje próbne oprogramowania

W przypadku scenariuszy publikowania Transact Wydawca może bezpłatnie udostępnić licencję na oprogramowanie przez 30 dni lub 90 dni. Ta funkcja dyskontowania nie obejmuje kosztu użycia infrastruktury platformy Azure, który jest oparty na użyciu rozwiązania partnerskiego.

### <a name="private-offers"></a>Oferty prywatne

Oprócz korzystania z typów ofert i modeli rozliczeń w celu Zarabiaj oferty, wydawcy mogą wykonać transakcję prywatną, zapełniać z użyciem wynegocjowanych i specyficznych dla transakcji cen lub konfiguracji niestandardowych. Oferty prywatne są obsługiwane przez wszystkie 3 opcje publikowania Transact.

Ta opcja umożliwia korzystanie z wyższych lub niższych cen niż publicznie dostępna oferta. Oferty prywatne mogą służyć do rabatu lub dodania wersji Premium oferty. Oferty prywatne można udostępnić jednemu lub większej liczbie klientów, wymieniając swoją subskrypcję platformy Azure na poziomie oferty.

### <a name="examples"></a>Przykłady

#### <a name="pay-as-you-go"></a>Płatność zgodnie z rzeczywistym użyciem

* Jeśli włączysz opcję płatność zgodnie z rzeczywistym użyciem, będziesz mieć poniższą strukturę kosztów.

|Koszt licencji  | $1,00 za godzinę  |
|---------|---------|
|Koszt użycia platformy Azure (D1/1-rdzeniowe)    |   $0,14 za godzinę     |
|*Klient jest rozliczany przez firmę Microsoft*    |  *$1,14 za godzinę*       |

* W tym scenariuszu firma Microsoft rozlicza $1,14 za godzinę w celu użycia opublikowanego obrazu maszyny wirtualnej.

|Rachunki firmy Microsoft  | $1,14 za godzinę  |
|---------|---------|
|Firma Microsoft płaci 80% kosztu licencji|   $0,80 za godzinę     |
|Firma Microsoft utrzymuje 20% kosztów licencji  |  $0,20 za godzinę       |
|Firma Microsoft utrzymuje 100% kosztów użycia platformy Azure | $0,14 za godzinę |

### <a name="bring-your-own-license-byol"></a>Korzystanie z własnej licencji (BYOL)

* Po włączeniu opcji BYOL będzie dostępna następująca struktura kosztów.

|Koszt licencji  | Opłata licencyjna wynegocjowana i rozliczona przez użytkownika  |
|---------|---------|
|Koszt użycia platformy Azure (D1/1-rdzeniowe)    |   $0,14 za godzinę     |
|*Klient jest rozliczany przez firmę Microsoft*    |  *$0,14 za godzinę*       |

* W tym scenariuszu firma Microsoft rozlicza $0,14 za godzinę w celu użycia opublikowanego obrazu maszyny wirtualnej.

|Rachunki firmy Microsoft  | $0,14 za godzinę  |
|---------|---------|
|Firma Microsoft utrzymuje koszt użycia platformy Azure    |   $0,14 za godzinę     |
|Firma Microsoft utrzymuje 0% kosztu licencji   |  $0,00 za godzinę       |

### <a name="saas-app-subscription"></a>Subskrypcja aplikacji SaaS

Ta opcja musi być skonfigurowana do sprzedaży za pomocą firmy Microsoft i może być naliczana według stawki miesięcznej lub rocznej.

• Jeśli włączysz opcję sprzedaży za pomocą firmy Microsoft dla oferty SaaS, będziesz mieć poniższą strukturę kosztów.

|Koszt licencji       | $100,00 miesięcznie  |
|--------------|---------|
|Koszt użycia platformy Azure (D1/1-rdzeniowe)    | Rozliczana bezpośrednio z wydawcą, a nie klientem |
|*Klient jest rozliczany przez firmę Microsoft*    |  *$100,00 miesięcznie (Uwaga: Wydawca musi uwzględnić koszty związane z infrastrukturą lub przekazaniem usługi w ramach opłaty licencyjnej)*  |

* W tym scenariuszu firma Microsoft Bills $100,00 na potrzeby licencji na oprogramowanie i płaci wydawcę $80,00.
* Partnerzy, którzy zakwalifikowani do obniżonej opłaty za usługę Marketplace, będą widzieć obniżoną opłatę za transakcje w ofertach SaaS od maja 2019 do 2020 czerwca. W tym scenariuszu firma Microsoft Bills $100,00 na potrzeby licencji na oprogramowanie i płaci wydawcę $90,00.

|Rachunki firmy Microsoft  | $100,00 miesięcznie  |
|---------|---------|
|Firma Microsoft płaci 80% kosztu licencji <br> \*Firma Microsoft płaci 90% kosztu licencji dla wszystkich zakwalifikowanych aplikacji SaaS   |   $80,00 miesięcznie <br> \*$90,00 miesięcznie    |
|Firma Microsoft utrzymuje 20% kosztów licencji <br> \*Firma Microsoft utrzymuje 10% kosztów licencji na wszystkie kwalifikowane aplikacje SaaS.  |  $20,00 miesięcznie <br> \*$10,00     |

**Zmniejszona opłata za usługę Marketplace:** W przypadku niektórych produktów SaaS opublikowanych na naszej komercyjnej witrynie Marketplace firma Microsoft obniży opłatę za usługę Marketplace z 20% (zgodnie z opisem w umowie Microsoft Publisher) do 10%.  Aby produkt mógł kwalifikować się, co najmniej jeden z Twoich produktów musi być wyznaczone przez firmę Microsoft jako gotowy do użycia w przypadku współsprzedażowego adresu IP lub do współsprzedawanego adresu IP. Aby otrzymać obniżoną opłatę za usługę Marketplace w danym miesiącu, należy spełnić co najmniej pięć (5) dni roboczych przed końcem poprzedniego miesiąca kalendarzowego. Zmniejszona opłata za usługę Marketplace nie będzie stosowana do maszyn wirtualnych, zarządzanych aplikacji ani innych produktów udostępnianych za pośrednictwem komercyjnej witryny Marketplace.  Ta zmniejszona opłata za usługę Marketplace będzie dostępna dla kwalifikujących się ofert, a opłaty licencyjne zebrane przez firmę Microsoft od 1 maja 2019 do 30 czerwca 2020.  Po upływie tego czasu opłata za usługę portalu Marketplace powróci do normalnej kwoty.
