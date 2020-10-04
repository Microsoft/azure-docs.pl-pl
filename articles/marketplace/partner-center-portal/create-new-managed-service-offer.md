---
title: Tworzenie oferty usługi zarządzanej w witrynie Microsoft Commercial Marketplace
description: Tworzenie nowej oferty usługi zarządzanej na potrzeby wyświetlania listy w witrynie Azure Marketplace przy użyciu portalu komercyjnej witryny Marketplace w centrum partnerskim.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: Microsoft-BradleyWright
ms.author: brwrigh
ms.date: 08/07/2020
ms.openlocfilehash: 34af52b32d9e31ad003c5e95e288f88b157a944a
ms.sourcegitcommit: 19dce034650c654b656f44aab44de0c7a8bd7efe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/04/2020
ms.locfileid: "91710621"
---
# <a name="create-a-managed-service-offer"></a>Tworzenie oferty usługi zarządzanej

Usługa zarządzana oferuje pomoc w włączaniu scenariuszy [usługi Azure Lighthouse](../../lighthouse/overview.md) . Gdy klient zaakceptuje ofertę usługi zarządzanej, może później dołączyć zasoby do [zarządzania zasobami delegowanymi przez platformę Azure](../../lighthouse/concepts/azure-delegated-resource-management.md). Przed rozpoczęciem [Utwórz komercyjne konto witryny Marketplace w centrum partnerskim](create-account.md) , jeśli jeszcze tego nie zrobiono. Upewnij się, że Twoje konto jest zarejestrowane w komercyjnym programie Marketplace.

Aby opublikować ofertę usługi zarządzanej, należy dysponować [poziomem kompetencji](https://partner.microsoft.com/membership/cloud-platform-competency) lub [specjalistą dla chmury platformy Azure](https://partner.microsoft.com/membership/azure-expert-msp) .

## <a name="create-a-new-offer"></a>Tworzenie nowej oferty

1. Zaloguj się do [Centrum partnerskiego](https://partner.microsoft.com/dashboard/home).
2. W menu po lewej stronie wybierz pozycję **komercyjne Omówienie witryny Marketplace**  >  **Overview**.
3. Na stronie Przegląd wybierz pozycję **+ Nowa oferowana**  >  **usługa zarządzana**.

    ![Ilustruje menu nawigacji po lewej stronie.](./media/new-offer-managed-service.png)

>[!NOTE]
>Po opublikowaniu oferty zmiany wprowadzone w centrum partnerskim będą widoczne w sklepach online dopiero po ponownym opublikowaniu oferty. Pamiętaj, aby zawsze ponownie publikować po wprowadzeniu zmian.

## <a name="new-offer"></a>Nowa oferta

Wprowadź **Identyfikator oferty**. To jest unikatowy identyfikator dla każdej oferty na Twoim koncie.

* Ten identyfikator jest widoczny dla klientów w adresie sieci Web dla ofert i szablonów Azure Resource Manager, jeśli ma zastosowanie.
* Użyj tylko małych liter i cyfr. Może zawierać łączniki i podkreślenia, ale nie spacje i jest ograniczone do 50 znaków. Jeśli na przykład wprowadzisz polecenie **test-Offer-1**, adres sieci Web oferty będzie wynosić `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` .
* Nie można zmienić identyfikatora oferty po wybraniu pozycji **Utwórz**.

Wprowadź **alias oferty**. Jest to nazwa używana dla oferty w centrum partnerskim.

* Ta nazwa nie jest używana w portalu Marketplace i różni się od nazwy oferty i innych wartości widocznych dla klientów.
* Aliasu oferty nie można zmienić po wybraniu pozycji **Utwórz**.

Wybierz pozycję **Utwórz** , aby wygenerować ofertę i kontynuować.

## <a name="offer-setup"></a>Konfiguracja oferty

### <a name="customer-leads"></a>Potencjalni klienci

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

Dla [zasad certyfikacji usług zarządzanych](https://docs.microsoft.com/legal/marketplace/certification-policies#700-managed-services)wymagane jest **miejsce docelowe lidera** . Spowoduje to utworzenie rekordu w systemie CRM przy każdym wdrożeniu oferty przez klienta.

Aby uzyskać więcej informacji, zobacz temat [Zarządzanie potencjalnymi klientami — Omówienie](./commercial-marketplace-get-customer-leads.md).

Wybierz pozycję **Zapisz wersję roboczą** przed kontynuowaniem.

## <a name="properties"></a>Właściwości

Ta strona umożliwia definiowanie kategorii używanych do grupowania oferty w witrynie Marketplace oraz umów prawnych wspierających Twoją ofertę.

### <a name="category"></a>Kategoria

Wybierz co najmniej jedną i maksymalnie pięć kategorii, które będą używane do umieszczania oferty w odpowiednich obszarach wyszukiwania w portalu Marketplace. Pamiętaj, aby dowiedzieć się, jak Twoja oferta obsługuje te kategorie w opisie oferty.

### <a name="terms-and-conditions"></a>Warunki i postanowienia

Podaj własne warunki prawne i postanowienia w polu Warunki **i** postanowienia. Możesz także podać adres URL, pod którym można znaleźć warunki i postanowienia. Klienci będą musieli zaakceptować te warunki, aby wypróbować ofertę.

Wybierz pozycję **Zapisz wersję roboczą** przed kontynuowaniem.

## <a name="offer-listing"></a>Lista oferty

Na tej stronie można definiować szczegóły witryny Marketplace (takie jak nazwa oferty, opis i obrazy).

> [!NOTE]
> Oferta zawartości oferty (na przykład opis, dokumenty, zrzuty ekranu i warunki użytkowania) nie jest wymagana w języku angielskim, o ile opis oferty zaczyna się od frazy "Ta aplikacja jest dostępna tylko w języku innym niż angielski]". Można także zapewnić *przydatny adres URL linku* do oferowania zawartości w języku innym niż ten, który jest używany w ofercie dotyczącej oferty.

Oto przykład sposobu wyświetlania informacji o ofercie w Azure Portal:

:::image type="content" source="media/example-managed-services.png" alt-text="Ilustruje, w jaki sposób ta oferta pojawia się w Azure Portal.":::

#### <a name="call-out-descriptions"></a>Opisy połączeń

1. Tytuł
2. Opis
3. Przydatne łącza
4. Zrzuty ekranu

### <a name="name"></a>Nazwa

Nazwa wprowadzona w tym miejscu będzie wyświetlana klientom jako tytuł oferty. To pole jest wstępnie wypełnione tekstem wprowadzonym dla **aliasu oferty** podczas tworzenia oferty, ale można zmienić tę wartość. Ta nazwa może być znakiem towarowym (i może zawierać znaki towarowe lub autorskie). Nazwa nie może być dłuższa niż 50 znaków i nie może zawierać żadnych znaków emoji.

### <a name="search-results-summary"></a>Podsumowanie wyników wyszukiwania

Podaj krótki opis oferty (do 100 znaków), która może być używana w wynikach wyszukiwania w portalu Marketplace.

### <a name="long-summary"></a>Długie podsumowanie

Podaj dłuższy opis oferty (do 256 znaków). To długie podsumowanie może być również używane w wynikach wyszukiwania w portalu Marketplace.

### <a name="description"></a>Opis

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Long description-3](./includes/long-description-3.md)]

### <a name="privacy-policy-link"></a>Link zasad ochrony prywatności

Wprowadź adres URL zasad zachowania poufności informacji organizacji (hostowanych w Twojej witrynie). Użytkownik jest odpowiedzialny za zapewnienie zgodności aplikacji z przepisami i przepisami dotyczącymi ochrony prywatności oraz w celu zapewnienia prawidłowych zasad zachowania poufności informacji.

### <a name="useful-links"></a>Przydatne łącza

Podaj opcjonalne uzupełniające dokumenty online dotyczące Twojego rozwiązania. Dodaj dodatkowe przydatne linki, klikając pozycję **+ Dodaj link**.

### <a name="contact-information"></a>Informacje kontaktowe

W tej sekcji należy podać nazwę, adres e-mail i numer telefonu dla **kontaktu z pomocą techniczną** i **kontaktu inżynieryjnego**. Te informacje nie są widoczne dla klientów, ale będą dostępne dla firmy Microsoft i mogą być udostępniane partnerom programu CSP.

### <a name="support-urls"></a>Adresy URL pomocy technicznej

Jeśli masz pomoc techniczną dla klientów **globalnych platformy Azure** i/lub **Azure Government klientów**, podaj te adresy URL tutaj.

### <a name="marketplace-images"></a>Obrazy z witryny Marketplace

W tej sekcji można podać logo i obrazy, które będą używane podczas wyświetlania oferty dla klienta. Wszystkie obrazy muszą mieć format PNG.

[!INCLUDE [logo tips](../includes/graphics-suggestions.md)]

>[!NOTE]
>Jeśli występuje problem z przekazywaniem plików, upewnij się, że sieć lokalna nie blokuje https://upload.xboxlive.com usługi używanej przez centrum partnerskie.

#### <a name="store-logos"></a>Logo sklepu

Podaj plik PNG dla logo o **dużym** rozmiarze. Centrum partnerskie będzie używać tego do tworzenia **małych** i **średnich** logo. Opcjonalnie można zastąpić je innymi obrazami później.

- **Duże** (od 216 x 216 do 350 x 350 px, wymagane)
- **Średnia** (90 x 90 pikseli, opcjonalnie)
- **Mały** (48 x 48 pikseli, opcjonalnie)

Te logo są używane w różnych miejscach na liście:

[!INCLUDE [logos-azure-marketplace-only](../includes/logos-azure-marketplace-only.md)]

[!INCLUDE [logo tips](../includes/graphics-suggestions.md)]

#### <a name="screenshots"></a>Zrzuty ekranu

Dodaj maksymalnie pięć zrzutów ekranu, które pokazują, jak działa Twoja oferta. Wszystkie zrzuty ekranu muszą mieć 1280 x 720 pikseli.

#### <a name="videos"></a>Filmy wideo

Opcjonalnie możesz dodać maksymalnie pięć filmów wideo, które demonstrują Twoją ofertę. Te filmy wideo powinny być hostowane w usłudze YouTube i/lub Vimeo. Dla każdej z nich wprowadź nazwę filmu wideo, jego adres URL i obraz miniatury filmu wideo (1280 x 720 pikseli).

#### <a name="additional-marketplace-listing-resources"></a>Dodatkowe zasoby dotyczące wyświetlania w portalu Marketplace

- [Najlepsze rozwiązania dotyczące aukcji z ofertą Marketplace](../gtm-offer-listing-best-practices.md)

Wybierz pozycję **Zapisz wersję roboczą** przed kontynuowaniem.

## <a name="preview"></a>Wersja zapoznawcza

Przed opublikowaniem oferty na żywo w szerszej ofercie z witryny Marketplace musisz najpierw udostępnić ją w ograniczonej grupie odbiorców w wersji zapoznawczej. Dzięki temu można potwierdzić, jak oferta jest wyświetlana w portalu Azure Marketplace przed udostępnieniem jej klientom. Zespoły pomocy technicznej i inżynierów firmy Microsoft będą mogli wyświetlać Twoją ofertę w tym okresie zapoznawczym.

Możesz zdefiniować odbiorców wersji zapoznawczej, wprowadzając identyfikatory subskrypcji platformy Azure w sekcji **Podgląd odbiorców** . Możesz wprowadzić maksymalnie 10 identyfikatorów subskrypcji ręcznie lub przekazać plik CSV z maksymalnie 100 identyfikatorami subskrypcji.

Wszyscy klienci powiązani z tymi subskrypcjami będą mogli wyświetlać ofertę w portalu Azure Marketplace przed rozpoczęciem jej używania. Pamiętaj o dołączeniu własnych subskrypcji tutaj, aby można było wyświetlić podgląd oferty.

Wybierz pozycję **Zapisz wersję roboczą** przed kontynuowaniem.

## <a name="plan-overview"></a>Przegląd planu

Każda oferta musi mieć jeden lub więcej planów (dawniej nazywanych jednostkami SKU). Możesz dodać wiele planów, aby obsługiwać różne zestawy funkcji w różnych cenach lub dostosować konkretny plan dla ograniczonej liczby odbiorców określonych klientów. Klienci mogą przeglądać plany, które są dostępne dla nich w ramach oferty nadrzędnej.

Możesz utworzyć do 100 planów dla każdej oferty: maksymalnie 45 z nich mogą być prywatne. Dowiedz się więcej o planach prywatnych w [ofertach prywatnych w komercyjnym portalu Microsoft Marketplace](../private-offers.md).

Na stronie **Przegląd planu** wybierz pozycję **+ Utwórz nowy plan**. Następnie wprowadź **identyfikator planu** i **nazwę planu**. Obie te wartości mogą zawierać tylko małe znaki alfanumeryczne, łączniki i podkreślenia, maksymalnie 50 znaków. Te wartości mogą być widoczne dla klientów i nie można ich zmienić po opublikowaniu oferty.

Wybierz pozycję **Utwórz** po wprowadzeniu tych wartości, aby kontynuować pracę z planem. Istnieją trzy sekcje do zakończenia: **Planowanie list**, **cen i dostępności**oraz **konfiguracji technicznej**.

### <a name="plan-listing"></a>Zaplanuj listę

Najpierw Podaj **Podsumowanie wyników wyszukiwania** dla planu. Jest to krótki opis planu (do 100 znaków), który może być używany w wynikach wyszukiwania w portalu Marketplace.

Następnie wprowadź **Opis** , który zawiera bardziej szczegółowe wyjaśnienie planu.

### <a name="pricing-and-availability"></a>Cennik i dostępność

Obecnie istnieje tylko jeden model cenowy, który może być używany na potrzeby oferty usługi zarządzanej: **Bring Your Own License (BYOL)**. Oznacza to, że klienci będą rozliczani bezpośrednio za koszty związane z tą ofertą, a firma Microsoft nie nalicza opłat za Ciebie.

Sekcja **widoczność planu** pozwala wskazać, czy ten plan powinien być [prywatny](../../marketplace/private-offers.md). Jeśli opuścisz **to pole jest** niezaznaczone, Twój plan nie zostanie ograniczony do określonych klientów (lub do określonej liczby klientów).

> [!NOTE]
> Plany prywatne nie są obsługiwane w przypadku subskrypcji ustanowionych przez odsprzedawcę programu w programie Cloud Solution Provider (CSP).

Aby ten plan był dostępny tylko dla określonych klientów, wybierz opcję **tak**. W takim przypadku należy zidentyfikować klientów, podając ich identyfikatory subskrypcji. Można je wprowadzić jeden po jednym (dla maksymalnie 10 subskrypcji) lub przekazując plik CSV (maksymalnie 10 000 subskrypcji we wszystkich planach). Pamiętaj o dołączeniu własnych subskrypcji tutaj, aby móc testować i sprawdzać poprawność oferty.

> [!IMPORTANT]
> Po opublikowaniu planu jako publicznego nie można go zmienić na prywatny. Aby kontrolować, którzy klienci mogą akceptować ofertę i delegować zasoby, należy użyć planu prywatnego. W przypadku planu publicznego nie można ograniczyć dostępności do określonych klientów, a nawet do określonej liczby klientów (mimo że można już zrezygnować z sprzedaży planu w przypadku wybrania tej opcji). [Dostęp do delegowania można usunąć](../../lighthouse/how-to/remove-delegation.md) , gdy klient zaakceptuje ofertę tylko wtedy, gdy została uwzględniona **autoryzacja** z **definicją roli** ustawioną na [przypisanie rejestracji usług zarządzanych](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) , po opublikowaniu oferty. Możesz również skontaktować się z klientem i poproś o [usunięcie dostępu](../../lighthouse/how-to/view-manage-service-providers.md#add-or-remove-service-provider-offers).

### <a name="technical-configuration"></a>Konfiguracja techniczna

W tej części planu tworzony jest manifest z informacjami o autoryzacji do zarządzania zasobami klientów. Te informacje są wymagane w celu włączenia [zarządzania zasobami delegowanymi przez platformę Azure](../../lighthouse/concepts/azure-delegated-resource-management.md).

Pamiętaj, aby zapoznać się z [dzierżawcami, rolami i użytkownikami w scenariuszach usługi Azure Lighthouse](../../lighthouse/concepts/tenants-users-roles.md#best-practices-for-defining-users-and-roles) , aby zrozumieć, które role są obsługiwane, oraz najlepsze rozwiązania dotyczące definiowania autoryzacji.

> [!NOTE]
> Jak wspomniano powyżej, użytkownicy i role w wpisach **autoryzacji** będą stosowane do każdego klienta, który kupuje plan. Jeśli chcesz ograniczyć dostęp do określonego klienta, musisz opublikować plan prywatny do ich wyłącznego użytku.

#### <a name="manifest"></a>Manifest

Najpierw Podaj **wersję** manifestu. Użyj formatu *n. n. n* (na przykład 1.2.5).

Następnie wprowadź swój **Identyfikator dzierżawy**. Jest to identyfikator GUID skojarzony z IDENTYFIKATORem dzierżawy usługi Azure Active Directory (Azure AD) organizacji; oznacza to, że dzierżawy zarządzającej, z której będziesz uzyskiwać dostęp do zasobów klientów. Jeśli go nie masz, możesz go znaleźć, umieszczając kursor nad nazwą swojego konta w prawym górnym rogu Azure Portal lub wybierając pozycję **Przełącz katalog**.

Jeśli opublikujesz nową wersję oferty i chcesz utworzyć zaktualizowany manifest, wybierz pozycję **+ nowy manifest**. Pamiętaj, aby zwiększyć numer wersji z poprzedniej wersji manifestu.

#### <a name="authorization"></a>Autoryzacja

Autoryzacje definiują jednostki w dzierżawie zarządzającej, które mają dostęp do zasobów i subskrypcji dla klientów, którzy kupują plan. Każda z tych jednostek ma przypisaną wbudowaną rolę, która przyznaje określony poziom dostępu.

Dla każdego planu można utworzyć maksymalnie dwadzieścia autoryzacji.

> [!TIP]
> W większości przypadków chcesz przypisać role do grupy użytkowników usługi Azure AD lub nazwy głównej usług, a nie do serii poszczególnych kont użytkowników. Pozwala to na dodawanie lub usuwanie dostępu dla poszczególnych użytkowników bez konieczności aktualizacji i ponownego publikowania planu, gdy zmienią się wymagania dotyczące dostępu. Podczas przypisywania ról do grup usługi Azure AD upewnij się, [że **typem grupy** jest **zabezpieczenia** , a nie **pakiet Office 365**](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md). Aby uzyskać dodatkowe zalecenia, zobacz [dzierżawy, role i użytkowników w scenariuszach usługi Azure Lighthouse](../../lighthouse/concepts/tenants-users-roles.md).

Dla każdej **autoryzacji**należy podać następujące dane. Następnie można wybrać opcję **+ Dodaj autoryzację** dowolną liczbę razy, aby dodać więcej użytkowników i definicje ról.

- **Identyfikator obiektu usługi Azure AD**: identyfikator usługi Azure AD użytkownika, grupy użytkowników lub aplikacji, do której zostaną przyznane określone uprawnienia (zgodnie z definicją roli) do zasobów klientów.
- **Nazwa wyświetlana obiektu usługi Azure AD**: przyjazna nazwa, która pomaga klientowi zrozumieć cel tej autoryzacji. Klient zobaczy tę nazwę podczas delegowania zasobów.
- **Definicja roli**: Wybierz jedną z dostępnych ról wbudowanych usługi Azure AD z listy. Ta rola określi uprawnienia, które użytkownik w polu **Identyfikator obiektu usługi Azure AD** będzie mieć w zasobach klientów. Opisy tych ról można znaleźć w temacie [role wbudowane](../../role-based-access-control/built-in-roles.md) i [Obsługa ról w usłudze Azure Lighthouse](../../lighthouse/concepts/tenants-users-roles.md#role-support-for-azure-lighthouse).
  > [!NOTE]
  > Nowe wbudowane role są dodawane do platformy Azure, ale staną się dostępne w tym miejscu, chociaż może wystąpić pewne opóźnienie przed ich wyświetleniem.
- **Role**możliwe do przypisania: Ta opcja będzie wyświetlana tylko wtedy, gdy w **definicji roli** dla tej autoryzacji wybrano administratora dostępu użytkownika. Jeśli tak, należy dodać co najmniej jedną przypisaną rolę w tym miejscu. Użytkownik w polu **Identyfikator obiektu usługi Azure AD** będzie mógł przypisywać te role do [tożsamości zarządzanych](../../active-directory/managed-identities-azure-resources/overview.md), które są wymagane w celu [wdrożenia zasad, które można skorygować](../../lighthouse/how-to/deploy-policy-remediation.md). Należy pamiętać, że żadne inne uprawnienia zwykle skojarzone z rolą administratora dostępu użytkowników będą stosowane dla tego użytkownika.

> [!TIP]
> Aby mieć pewność, że w razie potrzeby będzie można [usunąć dostęp do delegowania](../../lighthouse/how-to/remove-delegation.md) , Uwzględnij **autoryzację** z **definicją roli** ustawioną na [przydział rejestracji usług zarządzanych](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role). Jeśli ta rola nie jest przypisana, delegowane zasoby mogą zostać usunięte tylko przez użytkownika w dzierżawie klienta.

Po zakończeniu wszystkich sekcji planu możesz wybrać pozycję **+ Utwórz nowy plan** dowolną liczbę razy, aby utworzyć dodatkowe plany. Po zakończeniu wybierz pozycję **Zapisz**.

Wybierz pozycję **Zapisz wersję roboczą** przed kontynuowaniem.

## <a name="publish"></a>Opublikuj

### <a name="submit-offer-to-preview"></a>Prześlij ofertę do wersji zapoznawczej

Po zakończeniu wszystkich wymaganych sekcji oferty wybierz pozycję **Przejrzyj i Opublikuj** w prawym górnym rogu portalu.

Jeśli po raz pierwszy publikujesz tę ofertę, możesz:

- Zobacz stan ukończenia dla każdej sekcji oferty.
  - **Nie uruchomiono** — sekcja nie została dotknięcia i należy ją ukończyć.
  - **Niekompletne** — sekcja zawiera błędy, które muszą zostać naprawione lub aby można było dostarczyć więcej informacji. Wróć do sekcji i zaktualizuj ją.
  - **Ukończono** — sekcja została ukończona, wszystkie wymagane dane zostały dostarczone i nie występują żadne błędy. Wszystkie sekcje oferty muszą być w stanie kompletnym, zanim będzie możliwe przesłanie oferty.
- W sekcji **uwagi dotyczące certyfikacji** Podaj instrukcje dotyczące testowania dla zespołu certyfikacji, aby upewnić się, że aplikacja została prawidłowo przetestowana, a także dodatkowe uwagi przydatne do poznania aplikacji.
- Prześlij ofertę do opublikowania, wybierając pozycję **Prześlij**. Wyślemy Ci wiadomość e-mail, gdy zostanie udostępniona wersja zapoznawcza oferty, którą możesz przejrzeć i zatwierdzić. Wróć do Centrum partnerskiego i wybierz pozycję **Przejdź na żywo** , aby uzyskać ofertę opublikowania oferty na publiczną (lub w przypadku prywatnej oferty dla odbiorców prywatnych).

### <a name="customer-experience-and-offer-management"></a>Obsługa klienta i zarządzanie ofertami

Po wdrożeniu oferty przez klienta będzie można delegować subskrypcje lub grupy zasobów na potrzeby [zarządzania zasobami delegowanymi przez platformę Azure](../../lighthouse/concepts/azure-delegated-resource-management.md). Aby uzyskać więcej informacji o tym procesie, zobacz proces dołączania [klienta](../../lighthouse/how-to/publish-managed-services-offers.md#the-customer-onboarding-process).

[Zaktualizowaną wersję oferty można opublikować](update-existing-offer.md) w dowolnym momencie. Na przykład możesz chcieć dodać nową definicję roli do wcześniej opublikowanej oferty. Po wykonaniu tej czynności klienci, którzy już dodaliśmy do oferty, będą widzieć ikonę na stronie [**dostawcy usług**](../../lighthouse/how-to/view-manage-service-providers.md) w Azure Portal, która pozwala im znać aktualizację. Każdy klient będzie mógł przejrzeć zmiany i zdecydować, czy chcą one zaktualizować do nowej wersji.

## <a name="next-steps"></a>Następne kroki

- [Aktualizowanie istniejącej oferty w witrynie Marketplace dla zastosowań komercyjnych](./update-existing-offer.md)
- [Dowiedz się więcej o usłudze Azure Lighthouse](../../lighthouse/overview.md)
