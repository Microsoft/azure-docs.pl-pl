---
title: Dostawcy rozwiązań w chmurze | Azure Marketplace
description: Wydawcy mogą teraz sprzedawać swoje oferty za pośrednictwem kanału partnerskiego Dostawcy rozwiązań w chmurze firmy Microsoft (CSP).
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/16/2020
ms.author: dsindona
ms.openlocfilehash: c4c8f21234cfbf040d57c0e1c8dbecbb698d7d50
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81685924"
---
# <a name="cloud-solution-providers"></a>Dostawcy rozwiązań w chmurze

Oferty oprogramowania mogą docierać do milionów wykwalifikowanych klientów firmy Microsoft obsługiwanych przez partnerów w programie Cloud Solution Provider (CSP), oprócz publicznej dostępności ofert za pośrednictwem [witryn internetowych firmy Microsoft.](https://docs.microsoft.com/azure/marketplace/comparing-appsource-azure-marketplace)

Wydawcy konfigurują oferty dostępności w programie CSP na zasadzie opt-in, dla nowej oferty lub istniejącej, umożliwiając partnerom sprzedaż produktów i tworzenie powiązanych rozwiązań dla klientów.

Wydawcy są odpowiedzialni za zapewnienie wsparcia w zakresie rozwiązywania napraw klientom końcowym oraz za zapewnienie partnerom programu CSP i/lub klientom mechanizmu kontaktowania się z Tobą w celu uzyskania pomocy technicznej. Najlepszym rozwiązaniem jest zapewnienie partnerom w programie dostawcy usług kryptograficznych dokumentacji użytkownika, szkolenia i powiadomień o kondycji/awarii usługi (w stosownych przypadkach), dzięki czemu partnerzy w programie CSP są przygotowani do obsługi żądań pomocy technicznej warstwy 1 od klientów.  

Następujące oferty mogą zostać zakwalifikowane do sprzedaży przez partnerów w programie CSP:

- Oferty transakcji "Oprogramowanie jako usługa"SaaS)
- Maszyny wirtualne (maszyny wirtualne)
- Szablony rozwiązań
- Aplikacje zarządzane

> [!NOTE]
> Jednostki SKU VM kontenerów i przywrzeź własne licencje (BYOL) są domyślnie sprzedawane przez partnerów w programie CSP.

## <a name="how-to-configure-an-offering"></a>Jak skonfigurować ofertę

Ustawienie zgody na program dostawcy usług kryptograficznych jest skonfigurowane w centrum partnerów lub portalu cloud partnerów. [Dowiedz się więcej o zmieniającym się doświadczeniu wydawcy](https://www.microsoftpartnercommunity.com/t5/Azure-Marketplace-and-AppSource/Cloud-Marketplace-In-Partner-Center/m-p/9738#M293).

### <a name="partner-center-opt-in"></a>Zgodę Centrum partnerów

W Centrum partnerów znajdziesz doświadczenie opt-in w module odbiorczy sprzedawca CSP.

![Odbiorcy sprzedawcy CSP](media/marketplace-publishers-guide/csp-reseller-audience.png)

W module Odbiorca csp odsprzedawcy masz do wyboru trzy opcje:

- Opcja pierwsza: Każdy partner w programie CSP
- Opcja druga: Konkretni partnerzy w programie CSP, który wybieram
- Opcja trzecia: Brak partnerów w programie CSP

#### <a name="option-one-any-partner-in-the-csp-program"></a>Opcja pierwsza: Każdy partner w programie CSP

![Każdy partner w programie CSP](media/marketplace-publishers-guide/csp-reseller-option-one.png)

 Wybierając tę opcję, wszyscy partnerzy programu CSP mogą odsprzedać ofertę swoim klientom.

#### <a name="option-two-specific-partners-in-the-csp-program-i-select"></a>Opcja druga: Konkretni partnerzy w programie CSP, który wybieram

![Konkretni partnerzy w programie CSP, który wybieram](media/marketplace-publishers-guide/csp-reseller-option-two.png)

Wybierając tę opcję, użytkownik autoryzuje, którzy partnerzy w programie CSP mogą odsprzedać Twoją ofertę.

Aby autoryzować partnerów, kliknij pozycję **Wybierz partnerów systemu CSP** i zostanie wyświetlona menu umożliwiające wyszukiwanie według nazwy partnera lub identyfikatora dzierżawy usługi CSP Azure Active Directory (AAD).

![Wybierz menu CSP](media/marketplace-publishers-guide/csp-pop-up-module.png)

Można zastosować filtry wyszukiwania, takie jak **Kraj,** **Kompetencja**lub **Umiejętność**.

![Filtry kraju, kompetencji i umiejętności do wyszukiwania partnerów](media/marketplace-publishers-guide/csp-add-resellers.png)

Po wybraniu listy partnerów wybierz pozycję **Dodaj**.

![Przykładowa lista autoryzowanych partnerów w programie CSP](media/marketplace-publishers-guide/csp-add-resellers-details.png)

Tabela przedstawiająca listę wybranych partnerów pojawi się na stronie Odbiorcy odsprzedawcy programu CSP.

![Tabela z listą partnerów na stronie odbiorców sprzedawcy CSP](media/marketplace-publishers-guide/csp-option-two-add-reseller.png)

Wybierz **pozycję Zapisz wersja robocza,** aby zarejestrować zmiany.

Jeśli ta oferta nie zostanie opublikowana, musisz opublikować ofertę, aby udostępnić ją wybranym partnerom.

>[!NOTE]
>Jeśli autoryzujesz partnera w programie CSP w danym regionie, może on sprzedać ofertę do dowolnego klienta, który należy do tego regionu. Zobacz [Programy dostawcy rozwiązań w chmurze na rynkach regionalnych i walucie,](https://docs.microsoft.com/partner-center/regional-authorization-overview) aby uzyskać więcej informacji na temat sposobu klasyfikowania ofert dostawcy usług kryptograficznych w regionach.

Jeśli aktualizujesz listę dostawców usług kryptograficznych już opublikowanej oferty, dodaj dodatkowych partnerów i wybierz **pozycję Synchronizuj odbiorców CSP**.

Jeśli masz ofertę, która ma już listę autoryzowanych partnerów i chcesz użyć tej samej listy dla innej oferty, użyj **importu/eksportu**. Przejdź do oferty zawierającej listę dostawcy usług kryptograficznych i wybierz pozycję **Eksportuj dostawców usług kryptograficznych**. Funkcja rozwija plik csv, który można zaimportować do innej oferty.

#### <a name="option-three-no-partners-in-the-csp-program"></a>Opcja trzecia: Brak partnerów w programie CSP

![Brak partnerów w programie CSP](media/marketplace-publishers-guide/csp-reseller-option-three.png)

Wybierając tę opcję, rezygnujesz z oferty z programu CSP. Wybór ten można zmienić w dowolnym momencie.

### <a name="cloud-partner-portal-opt-in"></a>Zgodę na portal partnerów w chmurze

W portalu cloud partner portalu wyrażenia zgody jest ustawiane na karcie Marketplace lub Storefront. Możliwość wyboru określonych partnerów w programie CSP jest dostępna tylko w Centrum partnerów.

![Doświadczenie csp opt-in w CPP](media/marketplace-publishers-guide/csp-opt-in.png)

## <a name="deauthorize-partners-in-the-csp-program"></a>Deauthorize Partnerzy w programie CSP

Jeśli autoryzowałeś partnera w programie CSP, a ten partner odsprzedał już produkt swoim klientom, nie będzie można anulować autoryzacji tego partnera.

Jeśli partner programu CSP nie sprzedał produktu swoim klientom i chcesz usunąć CSP po opublikowaniu oferty, skorzystaj z następujących instrukcji:

1. Przejdź do [strony żądania pomocy technicznej](https://aka.ms/marketplacepublishersupport). Pierwsze kilka menu rozwijanych jest automatycznie wypełnianych za Ciebie.

   > [!NOTE]
   > Nie zmieniaj wstępnie wypełnionych opcji menu rozwijanego.

2. W obszarze **Wybierz wersję produktu**wybierz opcję Zarządzanie **ofertami na żywo**.
3. Aby **wybrać kategorię, która najlepiej opisuje problem**, wybierz kategorię, która odnosi się do twojej oferty.
4. Aby **zaznaczyć problem, który najlepiej opisuje problem**, wybierz pozycję Aktualizuj **istniejącą ofertę**.
5. Wybierz **pozycję Dalej,** aby przejść do **strony Szczegóły wydania,** aby wprowadzić więcej szczegółów dotyczących problemu.
6. Użyj **Deauthorize CSP** jako tytuł problemu i wypełnić pozostałe sekcje wymagane.




## <a name="navigate-between-options"></a>Przechodzenie między opcjami

Ta sekcja służy do przechodzenia między trzema opcjami odsprzedawcy CSP.

### <a name="navigate-from-option-one-any-partner-in-the-csp-program"></a>Nawigacja z opcji 1: Każdy partner w programie CSP

Jeśli twoja oferta jest obecnie **opcja 1: Dowolny partner w programie CSP** i chcesz przejść do jednej z dwóch pozostałych opcji, użyj następujących instrukcji, aby utworzyć żądanie:

1. Przejdź do [strony żądania pomocy technicznej](https://aka.ms/marketplacepublishersupport). Pierwsze kilka menu rozwijanych jest automatycznie wypełnianych za Ciebie.

   > [!NOTE]
   > Nie zmieniaj wstępnie wypełnionych opcji menu rozwijanego.

2. W obszarze **Wybierz wersję produktu**wybierz opcję Zarządzanie **ofertami na żywo**.
3. Aby **wybrać kategorię, która najlepiej opisuje problem**, wybierz kategorię, która odnosi się do twojej oferty.
4. Aby **zaznaczyć problem, który najlepiej opisuje problem**, wybierz pozycję Aktualizuj **istniejącą ofertę**.
5. Wybierz **pozycję Dalej,** aby przejść do **strony Szczegóły wydania,** aby wprowadzić więcej szczegółów dotyczących problemu.
6. Użyj **Deauthorize CSP** jako tytuł problemu i wypełnić pozostałe sekcje wymagane.

> [!NOTE]
> Jeśli próbujesz przejść do opcji drugiej, a partner w programie CSP odsprzedał już ofertę swoim klientom, ten partner jest domyślnie już na liście autoryzowanych partnerów.  

### <a name="navigate-from-option-two-specific-partners-in-the-csp-program-i-select"></a>Nawigacja z opcji drugiej: Konkretni partnerzy w wybranym programie CSP

Jeśli twoja oferta jest obecnie **opcja 2: Konkretni partnerzy w programie CSP wybrać** i chcesz przejść do **opcji jeden: Każdy partner w programie CSP**, użyj następujących instrukcji, aby utworzyć żądanie:

1. Przejdź do [strony żądania pomocy technicznej](https://aka.ms/marketplacepublishersupport). Pierwsze kilka menu rozwijanych jest automatycznie wypełnianych za Ciebie.

   > [!NOTE]
   > Nie zmieniaj wstępnie wypełnionych opcji menu rozwijanego.

2. W obszarze **Wybierz wersję produktu**wybierz opcję Zarządzanie **ofertami na żywo**.
3. Aby **wybrać kategorię, która najlepiej opisuje problem**, wybierz kategorię, która odnosi się do twojej oferty.
4. Aby **zaznaczyć problem, który najlepiej opisuje problem**, wybierz pozycję Aktualizuj **istniejącą ofertę**.
5. Wybierz **pozycję Dalej,** aby przejść do **strony Szczegóły wydania,** aby wprowadzić więcej szczegółów dotyczących problemu.
6. Użyj **Deauthorize CSP** jako tytuł problemu i wypełnić pozostałe sekcje wymagane.

 Jeśli twoja oferta jest obecnie **opcja 2: Konkretni partnerzy w programie CSP wybrać** i chcesz przejść do **opcji 3: Brak partnerów w programie CSP,** będziesz mógł przejść do tej opcji tylko wtedy, gdy partnerzy w programie CSP wcześniej autoryzowany nie odsprzedaży oferty do klientów końcowych. Aby utworzyć żądanie, wykonaj następujące instrukcje:

1. Przejdź do [strony żądania pomocy technicznej](https://aka.ms/marketplacepublishersupport). Pierwsze kilka menu rozwijanych jest automatycznie wypełnianych za Ciebie.

   > [!NOTE]
   > Nie zmieniaj wstępnie wypełnionych opcji menu rozwijanego.

2. W obszarze **Wybierz wersję produktu**wybierz opcję Zarządzanie **ofertami na żywo**.
3. Aby **wybrać kategorię, która najlepiej opisuje problem**, wybierz kategorię, która odnosi się do twojej oferty.
4. Aby **zaznaczyć problem, który najlepiej opisuje problem**, wybierz pozycję Aktualizuj **istniejącą ofertę**.
5. Wybierz **pozycję Dalej,** aby przejść do **strony Szczegóły wydania,** aby wprowadzić więcej szczegółów dotyczących problemu.
6. Użyj **Deauthorize CSP** jako tytuł problemu i wypełnić pozostałe sekcje wymagane.

### <a name="navigate-from-option-3-no-partners-in-the-csp-program"></a>Nawigacja z opcji 3: Brak partnerów w programie CSP

Jeśli twoja oferta jest obecnie **opcja 3: Brak partnerów w programie CSP**, można przejść do jednej z dwóch pozostałych opcji w dowolnym momencie.

## <a name="sharing-sales-and-support-materials-with-partners-in-the-csp-program"></a>Dzielenie się materiałami sprzedażowymi i pomocniczymi z partnerami w programie CSP

Aby pomóc partnerom w programie Cloud Solution Provider najskuteczniej reprezentować Twoją ofertę i współpracować z twoją organizacją, należy przesłać materiały sprzedażowe i pomocnicze, które będą dostępne dla sprzedawców. Te zasoby nie będą udostępniane klientom w witrynach sklepu marketplace.

### <a name="partner-center-csp-channel"></a>Kanał CSP Centrum partnerów

Jeśli wybrałeś kanał Dostawcy usług kryptograficznych w Centrum partnerów, wydawcy muszą wprowadzić adres URL zawierający odpowiednie materiały marketingowe i informacje kontaktowe kanału do kanału dostawcy usług kryptograficznych w module aukcji oferty:

![Informacje dodatkowe centrum partnerskiego CSP](media/marketplace-publishers-guide/pc-csp-channel.png)

### <a name="cloud-partner-portal-csp-channel"></a>Kanał CSP portalu partnerów w chmurze

Jeśli użytkownik wybrał kanał dostawcy usług kryptograficznych w portalu Cloud Partner Portal, wydawcy muszą wprowadzić adres URL zawierający odpowiednie materiały marketingowe i informacje kontaktowe kanału CSP:

![Informacje dodatkowe dostawcy usług kryptograficznych portalu partnerów w chmurze](media/marketplace-publishers-guide/cpp-csp-information.png)

## <a name="next-steps"></a>Następne kroki

Odwiedź przewodnik [po portalu Azure Marketplace i appsource dla wydawców](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide).

Aby dowiedzieć się więcej o usługach GTM w marketplace, zobacz [Usługi przejdź na rynek.](https://partner.microsoft.com/reach-customers/gtm)

Zaloguj się do [Centrum partnerów](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) lub [portalu cloud partnerów,](https://cloudpartner.azure.com/) aby utworzyć i skonfigurować ofertę.
