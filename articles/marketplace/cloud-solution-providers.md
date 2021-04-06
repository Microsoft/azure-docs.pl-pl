---
title: Dostawca rozwiązań w chmurze — Microsoft Commercial Marketplace
description: Dowiedz się, jak sprzedawać oferty za pośrednictwem kanału partnera programów Microsoft Cloud Provider (CSP) w portalu komercyjnym.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: keferna
ms.author: keferna
ms.date: 07/14/2020
ms.openlocfilehash: c906d37a01f0fca2d4114e8ba07078fc46eec88b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93131177"
---
# <a name="cloud-solution-provider-program"></a>Program Dostawca rozwiązań w chmurze

W tym artykule opisano sposób konfigurowania oferty w celu udostępnienia jej w programie Cloud Solution Provider (CSP). Oprócz publikowania ofert przy użyciu [komercyjnych sklepów online Marketplace](overview.md#commercial-marketplace-online-stores)można także sprzedawać za pomocą programu CSP, aby dotrzeć do milionów wykwalifikowanych klientów firmy Microsoft obsługiwanych przez program.

Można skonfigurować nowe lub istniejące oferty dla dostępności w programie CSP, co pozwala partnerom CSP sprzedawać produkty i tworzyć powiązane rozwiązania dla klientów.

Wydawcy są odpowiedzialni za zapewnienie klientom końcowym pomocy technicznej w zakresie rozwiązywania problemów oraz udostępnienie mechanizmu partnerom w programie CSP i/lub klientom kontaktowania się z Tobą w celu uzyskania pomocy technicznej. Najlepszym rozwiązaniem jest zapewnienie partnerom w programie CSP obsługi dokumentacji, szkoleń i kondycji usługi/powiadomień o przestoju (stosownie do potrzeb), aby partnerzy w programie CSP mogli obsługiwać żądania pomocy technicznej warstwy 1 od klientów.  

Następujące oferty mogą zostać sprzedane przez partnerów w programie CSP:

- Oferty języka Transact jako usługa (SaaS)
- Virtual Machines (maszyny wirtualne)
- Szablony rozwiązań
- Aplikacje zarządzane

> [!NOTE]
> Kontenery i przenoszenie własnych licencji (BYOL) plany maszyn wirtualnych są domyślnie sprzedawane przez partnerów w programie CSP.

## <a name="how-to-configure-an-offer"></a>Jak skonfigurować ofertę

Skonfiguruj ustawienie zgody programu CSP w programie podczas tworzenia oferty w centrum partnerskim. [Dowiedz się więcej na temat zmiany środowiska wydawcy](https://www.microsoftpartnercommunity.com/t5/Azure-Marketplace-and-AppSource/Cloud-Marketplace-In-Partner-Center/m-p/9738#M293).

### <a name="partner-center-opt-in"></a>Zgoda na centrum partnerskie

W ramach modułu odbiorców odsprzedawcy dostawcy CSP dostępne są następujące czynności.

![Odbiorcy dostawcy CSP](media/marketplace-publishers-guide/csp-reseller-audience.png)

Wybieraj spośród trzech opcji:

1. Każdy partner w programie CSP.
2. Wybrani partnerzy w wybranym programie dostawcy usług kryptograficznych.
3. Brak partnerów w programie CSP.

#### <a name="option-1-any-partner-in-the-csp-program"></a>Opcja 1: każdy partner w programie CSP

![Dowolnemu partnerowi w programie CSP](media/marketplace-publishers-guide/csp-reseller-option-one.png)

 Po wybraniu tej opcji Wszyscy partnerzy w programie CSP mogą sprzedawać swoją ofertę klientom.

#### <a name="option-2-specific-partners-in-the-csp-program-i-select"></a>Opcja 2: określeni partnerzy w wybranym programie CSP

![Określeni partnerzy w wybranym programie CSP](media/marketplace-publishers-guide/csp-reseller-option-two.png)

Wybranie tej opcji pozwala autoryzować partnerów w programie CSP uprawnionych do odsprzedaży oferty.

Aby autoryzować partnerów, wybierz pozycję **Wybierz partnerów CSP** i wyświetli się menu umożliwiające wyszukiwanie według nazwy partnera lub identyfikatora dzierżawy Azure Active Directory (Azure AD).

![Wybieranie menu CSP](media/marketplace-publishers-guide/csp-pop-up-module.png)

Możesz zastosować filtry wyszukiwania, takie jak **kraj**, **kompetencje** lub **umiejętności**.

![Filtry kraju/regionu, kompetencji i umiejętności wyszukiwania partnerów](media/marketplace-publishers-guide/csp-add-resellers.png)

Po wybraniu listy partnerów wybierz pozycję **Dodaj**.

![Przykładowa lista autoryzowanych partnerów w programie CSP](media/marketplace-publishers-guide/csp-add-resellers-details.png)

Tabela zawierająca listę wybranych partnerów pojawia się na stronie odbiorców odsprzedawcy programu CSP.

![Tabela z listą partnerów na stronie odbiorców odsprzedawcy programu CSP](media/marketplace-publishers-guide/csp-option-two-add-reseller.png)

Wybierz pozycję **Zapisz wersję roboczą** , aby zarejestrować zmiany.

Jeśli ta oferta zostanie cofnięta, należy opublikować ofertę w celu udostępnienia jej wybranym partnerom.

>[!NOTE]
>Jeśli Użytkownik autoryzuje partnera w programie CSP w danym regionie, może sprzedać ofertę do dowolnego klienta, który należy do tego konkretnego regionu. Aby uzyskać więcej informacji o tym, jak oferty CSP są klasyfikowane w regionach, zobacz [regionalne rynki i waluty programu Cloud Solution Provider](/partner-center/regional-authorization-overview).

Jeśli aktualizujesz listę programu CSP dla już opublikowanej oferty, Dodaj dodatkowych partnerów i wybierz pozycję **Synchronizuj odbiorców CSP**.

Jeśli masz ofertę, która ma już listę autoryzowanych partnerów i chcesz używać tej samej listy dla innej oferty, Użyj usługi **Import/Export**. Przejdź do oferty z listą CSP i wybierz pozycję **Eksportuj dostawców CSP**. Funkcja opracowuje plik CSV, który można zaimportować do innej oferty.

#### <a name="option-3-no-partners-in-the-csp-program"></a>Opcja 3: brak partnerów w programie CSP

![Żadnym partnerom w programie CSP](media/marketplace-publishers-guide/csp-reseller-option-three.png)

Wybranie tej opcji spowoduje rezygnację z korzystania z programu CSP. Ten wybór można zmienić w dowolnym momencie.

## <a name="deauthorize-partners-in-the-csp-program"></a>Cofanie autoryzacji partnerów w programie CSP

Jeśli masz autoryzację partnera w programie CSP i ten partner już przekupił produkt klientom, nie będziesz mieć zezwolenia na cofanie autoryzacji tego partnera.

Jeśli partner w programie CSP nie sprzedał Twojego produktu klientom i chcesz usunąć dostawcę CSP po opublikowaniu oferty, wykonaj następujące instrukcje:

1. Przejdź do [strony support Request](https://aka.ms/marketplacepublishersupport). Pierwsze niektóre menu rozwijane są wypełniane automatycznie.

   > [!NOTE]
   > Nie zmieniaj wstępnie wypełnionego menu rozwijanego.

2. W **obszarze Wybierz wersję produktu** wybierz pozycję **zarządzanie ofertami na żywo**.
3. Wybierz **kategorię, która najlepiej opisuje problem**, wybierz kategorię odwołującą się do oferty.
4. W przypadku **wybrania problemu, który najlepiej opisuje problem**, wybierz pozycję **Aktualizuj istniejącą ofertę**.
5. Wybierz pozycję **dalej** , aby skierować do **strony szczegółów problemu** , aby wprowadzić więcej szczegółów dotyczących problemu.
6. Użyj elementu **unautoryzuj dostawcę CSP** jako tytułu problemu i wypełnij pozostałe sekcje wymagane.

## <a name="navigate-between-options"></a>Przechodzenie między opcjami

Ta sekcja umożliwia nawigowanie między opcjami trzech odsprzedawców dostawcy CSP.

### <a name="navigate-from-option-one-any-partner-in-the-csp-program"></a>Przejdź od opcji jeden: dowolny partner w programie CSP

Jeśli oferta jest obecnie **opcją 1: każdy partner w programie CSP** i chcesz przejść do jednej z dwóch innych opcji, wykonaj następujące instrukcje, aby utworzyć żądanie:

1. Przejdź do [strony support Request](https://aka.ms/marketplacepublishersupport). Pierwsze niektóre menu rozwijane są wypełniane automatycznie.

   > [!NOTE]
   > Nie zmieniaj wstępnie wypełnionego menu rozwijanego.

2. W **obszarze Wybierz wersję produktu** wybierz pozycję **zarządzanie ofertami na żywo**.
3. Wybierz **kategorię, która najlepiej opisuje problem**, wybierz kategorię odwołującą się do oferty.
4. W przypadku **wybrania problemu, który najlepiej opisuje problem**, wybierz pozycję **Aktualizuj istniejącą ofertę**.
5. Wybierz pozycję **dalej** , aby skierować do **strony szczegółów problemu** , aby wprowadzić więcej szczegółów dotyczących problemu.
6. Użyj elementu **unautoryzuj dostawcę CSP** jako tytułu problemu i wypełnij pozostałe sekcje wymagane.

> [!NOTE]
> Jeśli próbujesz przejść do opcji dwa, a partner w programie CSP już odsprzedał ofertę klientom, ten partner jest już domyślnie na liście autoryzowanych partnerów.  

### <a name="navigate-from-option-two-specific-partners-in-the-csp-program-i-select"></a>Nawiguj od opcji dwa: określeni partnerzy w wybranym programie CSP

Jeśli oferta zawiera obecnie **opcję 2: określeni partnerzy w wybranym programie CSP** i chcesz przejść do **opcji jeden: dowolny partner w programie CSP**, użyj następujących instrukcji, aby utworzyć żądanie:

1. Przejdź do [strony support Request](https://aka.ms/marketplacepublishersupport). Pierwsze niektóre menu rozwijane są wypełniane automatycznie.

   > [!NOTE]
   > Nie zmieniaj wstępnie wypełnionego menu rozwijanego.

2. W **obszarze Wybierz wersję produktu** wybierz pozycję **zarządzanie ofertami na żywo**.
3. Wybierz **kategorię, która najlepiej opisuje problem**, wybierz kategorię odwołującą się do oferty.
4. W przypadku **wybrania problemu, który najlepiej opisuje problem**, wybierz pozycję **Aktualizuj istniejącą ofertę**.
5. Wybierz pozycję **dalej** , aby skierować do **strony szczegółów problemu** , aby wprowadzić więcej szczegółów dotyczących problemu.
6. Użyj elementu **unautoryzuj dostawcę CSP** jako tytułu problemu i wypełnij pozostałe sekcje wymagane.

 Jeśli oferta jest obecnie **opcją 2: konkretni partnerzy w wybranym programie CSP** i chcesz przejść do **opcji 3: brak partnerów w programie CSP**, będziesz mieć możliwość przechodzenia do tej opcji tylko wtedy, gdy partnerzy w programie CSP, który wcześniej został autoryzowany, nie odsprzedali oferty klientom końcowym. Aby utworzyć żądanie, wykonaj następujące instrukcje:

1. Przejdź do [strony support Request](https://aka.ms/marketplacepublishersupport). Pierwsze niektóre menu rozwijane są wypełniane automatycznie.

   > [!NOTE]
   > Nie zmieniaj wstępnie wypełnionego menu rozwijanego.

2. W **obszarze Wybierz wersję produktu** wybierz pozycję **zarządzanie ofertami na żywo**.
3. Wybierz **kategorię, która najlepiej opisuje problem**, wybierz kategorię odwołującą się do oferty.
4. W przypadku **wybrania problemu, który najlepiej opisuje problem**, wybierz pozycję **Aktualizuj istniejącą ofertę**.
5. Wybierz pozycję **dalej** , aby skierować do **strony szczegółów problemu** , aby wprowadzić więcej szczegółów dotyczących problemu.
6. Użyj elementu **unautoryzuj dostawcę CSP** jako tytułu problemu i wypełnij pozostałe sekcje wymagane.

### <a name="navigate-from-option-3-no-partners-in-the-csp-program"></a>Nawiguj od opcji 3: brak partnerów w programie CSP

Jeśli oferta jest obecnie **Opcja 3: brak partnerów w programie CSP**, możesz w dowolnym momencie przejść do jednej z dwóch innych opcji.

## <a name="sharing-sales-and-support-materials-with-partners-in-the-csp-program"></a>Udostępnianie materiałów sprzedażowych i pomocy technicznej partnerom w programie CSP

Aby pomóc partnerom w programie dostawcy rozwiązań w chmurze, który najbardziej efektywnie reprezentuje ofertę i angażuje się z Twoją organizacją, musisz przesłać materiały sprzedażowe i pomoc techniczna, które będą dostępne dla odsprzedawców. Te zasoby nie będą ujawnione klientom w sklepach online.

### <a name="partner-center-csp-channel"></a>Kanał CSP Centrum partnerskiego

W przypadku wybrania w kanale CSP w Centrum partnerskiego, wydawcy muszą wprowadzić adres URL, który hostuje odpowiednie materiały marketingowe i informacje kontaktowe kanału w ramach modułu aukcji oferty.

![Informacje o zabezpieczeniach dostawcy CSP Centrum partnerskiego](media/marketplace-publishers-guide/pc-csp-channel.png)

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [usługach go-to-Market](https://partner.microsoft.com/reach-customers/gtm).
- Zaloguj się do [Centrum partnerskiego](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) , aby utworzyć i skonfigurować swoją ofertę.