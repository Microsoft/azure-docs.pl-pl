---
title: Dostawca rozwiązań w chmurze — Microsoft Commercial Marketplace
description: Dowiedz się, jak sprzedawać oferty za pośrednictwem kanału partnera programów Microsoft Cloud Provider (CSP) w portalu komercyjnym.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 04/16/2020
ms.openlocfilehash: d6c3af312717627a1719c5f1029ae638e7d4f827
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/08/2020
ms.locfileid: "86104439"
---
# <a name="cloud-solution-provider-program"></a>Program dostawcy rozwiązań w chmurze

W tym artykule opisano sposób konfigurowania oferty w celu udostępnienia jej w programie Cloud Solution Provider (CSP). Oprócz publikowania ofert za pomocą [komercyjnych witryn sieci Web w portalu Marketplace](comparing-appsource-azure-marketplace.md)możesz również sprzedawać za pomocą programu CSP, aby dotrzeć do milionów wykwalifikowanych klientów firmy Microsoft obsługiwanych przez program.

Można skonfigurować nowe lub istniejące oferty dla dostępności w programie CSP, co pozwala partnerom CSP sprzedawać Twoje produkty i tworzyć powiązane rozwiązania dla klientów.

Wydawcy są odpowiedzialni za zapewnienie klientom końcowym pomocy technicznej w zakresie rozwiązywania problemów oraz udostępnienie mechanizmu partnerom w programie CSP i/lub klientom kontaktowania się z Tobą w celu uzyskania pomocy technicznej. Najlepszym rozwiązaniem jest zapewnienie partnerom w programie CSP obsługi dokumentacji, szkoleń i kondycji usługi/powiadomień o przestoju (stosownie do potrzeb), aby partnerzy w programie CSP mogli obsługiwać żądania pomocy technicznej warstwy 1 od klientów.  

Następujące oferty mogą zostać sprzedane przez partnerów w programie CSP:

- Oferty języka Transact jako usługa (SaaS)
- Virtual Machines (maszyny wirtualne)
- Szablony rozwiązań
- Aplikacje zarządzane

> [!NOTE]
> Kontenery i przenoszenie własnych licencji (BYOL) jednostek SKU maszyn wirtualnych są domyślnie sprzedawane przez partnerów w programie CSP.

## <a name="how-to-configure-an-offering"></a>Jak skonfigurować ofertę

Ustawienie zgody programu CSP w programie jest konfigurowane w centrum partnerskim lub portal Cloud Partner środowisku tworzenia ofert. [Dowiedz się więcej na temat zmiany środowiska wydawcy](https://www.microsoftpartnercommunity.com/t5/Azure-Marketplace-and-AppSource/Cloud-Marketplace-In-Partner-Center/m-p/9738#M293).

### <a name="partner-center-opt-in"></a>Zgoda na centrum partnerskie

W centrum partnerskim można znaleźć możliwość skorzystania z modułu odbiorcy odsprzedawcy dostawcy CSP.

![Odbiorcy dostawcy CSP](media/marketplace-publishers-guide/csp-reseller-audience.png)

W module odbiorców odsprzedawcy dostawcy CSP dostępne są trzy opcje do wyboru:

- Opcja 1: każdy partner w programie CSP
- Opcja dwie: określeni partnerzy w wybranym programie CSP
- Opcja 3: brak partnerów w programie CSP

#### <a name="option-one-any-partner-in-the-csp-program"></a>Opcja 1: każdy partner w programie CSP

![Każdy partner w programie CSP](media/marketplace-publishers-guide/csp-reseller-option-one.png)

 Po wybraniu tej opcji Wszyscy partnerzy w programie CSP mogą sprzedawać swoją ofertę klientom.

#### <a name="option-two-specific-partners-in-the-csp-program-i-select"></a>Opcja dwie: określeni partnerzy w wybranym programie CSP

![Określeni partnerzy w wybranym programie CSP](media/marketplace-publishers-guide/csp-reseller-option-two.png)

Wybranie tej opcji pozwala autoryzować partnerów w programie CSP uprawnionych do odsprzedaży oferty.

Aby autoryzować partnerów, kliknij przycisk **Wybierz partnerów CSP** i zostanie wyświetlone menu umożliwiające wyszukiwanie według nazwy partnera lub identyfikatora dzierżawy Azure Active Directory (AAD).

![Wybieranie menu CSP](media/marketplace-publishers-guide/csp-pop-up-module.png)

Możesz zastosować filtry wyszukiwania, takie jak **kraj**, **kompetencje**lub **umiejętności**.

![Filtry kraju/regionu, kompetencji i umiejętności wyszukiwania partnerów](media/marketplace-publishers-guide/csp-add-resellers.png)

Po wybraniu listy partnerów wybierz pozycję **Dodaj**.

![Przykładowa lista autoryzowanych partnerów w programie CSP](media/marketplace-publishers-guide/csp-add-resellers-details.png)

Tabela zawierająca listę wybranych partnerów pojawia się na stronie odbiorców odsprzedawcy programu CSP.

![Tabela z listą partnerów na stronie odbiorców odsprzedawcy programu CSP](media/marketplace-publishers-guide/csp-option-two-add-reseller.png)

Wybierz pozycję **Zapisz wersję roboczą** , aby zarejestrować zmiany.

Jeśli ta oferta zostanie cofnięta, należy opublikować ofertę w celu udostępnienia jej wybranym partnerom.

>[!NOTE]
>Jeśli Użytkownik autoryzuje partnera w programie CSP w danym regionie, może sprzedać ofertę do dowolnego klienta, który należy do tego konkretnego regionu. Aby uzyskać więcej informacji o tym, jak oferty CSP są klasyfikowane w regionach, zobacz artykuł [regionalny rynek i waluta programu Cloud Solution Provider](https://docs.microsoft.com/partner-center/regional-authorization-overview) .

Jeśli aktualizujesz listę programu CSP dla już opublikowanej oferty, Dodaj dodatkowych partnerów i wybierz pozycję **Synchronizuj odbiorców CSP**.

Jeśli masz ofertę, która ma już listę autoryzowanych partnerów i chcesz używać tej samej listy dla innej oferty, Użyj usługi **Import/Export**. Przejdź do oferty z listą CSP i wybierz pozycję **Eksportuj dostawców CSP**. Funkcja opracowuje plik CSV, który można zaimportować do innej oferty.

#### <a name="option-three-no-partners-in-the-csp-program"></a>Opcja 3: brak partnerów w programie CSP

![Brak partnerów w programie CSP](media/marketplace-publishers-guide/csp-reseller-option-three.png)

Wybranie tej opcji spowoduje rezygnację z korzystania z programu CSP. Ten wybór można zmienić w dowolnym momencie.

### <a name="cloud-partner-portal-opt-in"></a>portal Cloud Partner zgodę

W portal Cloud Partner, zgoda jest ustawiana na karcie Marketplace lub witrynie portalu. Możliwość wyboru określonych partnerów w programie CSP jest dostępna tylko w centrum partnerskim.

![Możliwość korzystania z dostawcy usług kryptograficznych w programie CPP](media/marketplace-publishers-guide/csp-opt-in.png)

## <a name="deauthorize-partners-in-the-csp-program"></a>Cofanie autoryzacji partnerów w programie CSP

Jeśli masz autoryzację partnera w programie CSP i ten partner już przekupił produkt klientom, nie będziesz mieć zezwolenia na cofanie autoryzacji tego partnera.

Jeśli partner w programie CSP nie sprzedał Twojego produktu klientom i chcesz usunąć dostawcę CSP po opublikowaniu oferty, wykonaj następujące instrukcje:

1. Przejdź do [strony support Request](https://aka.ms/marketplacepublishersupport). Pierwsze niektóre menu rozwijane są wypełniane automatycznie.

   > [!NOTE]
   > Nie zmieniaj wstępnie wypełnionego menu rozwijanego.

2. W **obszarze Wybierz wersję produktu**wybierz pozycję **zarządzanie ofertami na żywo**.
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

2. W **obszarze Wybierz wersję produktu**wybierz pozycję **zarządzanie ofertami na żywo**.
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

2. W **obszarze Wybierz wersję produktu**wybierz pozycję **zarządzanie ofertami na żywo**.
3. Wybierz **kategorię, która najlepiej opisuje problem**, wybierz kategorię odwołującą się do oferty.
4. W przypadku **wybrania problemu, który najlepiej opisuje problem**, wybierz pozycję **Aktualizuj istniejącą ofertę**.
5. Wybierz pozycję **dalej** , aby skierować do **strony szczegółów problemu** , aby wprowadzić więcej szczegółów dotyczących problemu.
6. Użyj elementu **unautoryzuj dostawcę CSP** jako tytułu problemu i wypełnij pozostałe sekcje wymagane.

 Jeśli oferta jest obecnie **opcją 2: konkretni partnerzy w wybranym programie CSP** i chcesz przejść do **opcji 3: brak partnerów w programie CSP**, będziesz mieć możliwość przechodzenia do tej opcji tylko wtedy, gdy partnerzy w programie CSP, który wcześniej został autoryzowany, nie odsprzedali oferty klientom końcowym. Aby utworzyć żądanie, wykonaj następujące instrukcje:

1. Przejdź do [strony support Request](https://aka.ms/marketplacepublishersupport). Pierwsze niektóre menu rozwijane są wypełniane automatycznie.

   > [!NOTE]
   > Nie zmieniaj wstępnie wypełnionego menu rozwijanego.

2. W **obszarze Wybierz wersję produktu**wybierz pozycję **zarządzanie ofertami na żywo**.
3. Wybierz **kategorię, która najlepiej opisuje problem**, wybierz kategorię odwołującą się do oferty.
4. W przypadku **wybrania problemu, który najlepiej opisuje problem**, wybierz pozycję **Aktualizuj istniejącą ofertę**.
5. Wybierz pozycję **dalej** , aby skierować do **strony szczegółów problemu** , aby wprowadzić więcej szczegółów dotyczących problemu.
6. Użyj elementu **unautoryzuj dostawcę CSP** jako tytułu problemu i wypełnij pozostałe sekcje wymagane.

### <a name="navigate-from-option-3-no-partners-in-the-csp-program"></a>Nawiguj od opcji 3: brak partnerów w programie CSP

Jeśli oferta jest obecnie **Opcja 3: brak partnerów w programie CSP**, możesz w dowolnym momencie przejść do jednej z dwóch innych opcji.

## <a name="sharing-sales-and-support-materials-with-partners-in-the-csp-program"></a>Udostępnianie materiałów sprzedażowych i pomocy technicznej partnerom w programie CSP

Aby ułatwić partnerom w programie dostawcy rozwiązań w chmurze najbardziej efektywne przedstawianie oferty i współpracę z Twoją organizacją, musisz przesłać materiały sprzedażowe i pomoc techniczna, które będą dostępne dla odsprzedawców. Te zasoby nie zostaną ujawnione klientom w witrynie sklepu Marketplace.

### <a name="partner-center-csp-channel"></a>Kanał CSP Centrum partnerskiego

W przypadku wybrania w kanale CSP w Centrum partnerskiego, wydawcy muszą wprowadzić adres URL, który hostuje odpowiednie materiały marketingowe i informacje kontaktowe kanału do kanału CSP w ramach modułu aukcji oferty:

![Informacje o zabezpieczeniach dostawcy CSP Centrum partnerskiego](media/marketplace-publishers-guide/pc-csp-channel.png)

### <a name="cloud-partner-portal-csp-channel"></a>portal Cloud Partner kanał CSP

W przypadku wybrania w portal Cloud Partner kanału dostawcy CSP wydawcy muszą wprowadzić adres URL, który hostuje odpowiednie materiały marketingowe i informacje kontaktowe kanału do kanału dostawcy CSP:

![Informacje dodatkowe dotyczące portal Cloud Partner dostawcy usług kryptograficznych](media/marketplace-publishers-guide/cpp-csp-information.png)

## <a name="next-steps"></a>Następne kroki

Odwiedź witrynę [Azure Marketplace i Podręcznik wydawcy AppSource](marketplace-publishers-guide.md).

Aby dowiedzieć się więcej na temat usług GTM Marketplace, zobacz temat [usługi "przejdź do rynku"](https://partner.microsoft.com/reach-customers/gtm).

Zaloguj się do [Centrum partnerskiego](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) , aby utworzyć i skonfigurować swoją ofertę.
