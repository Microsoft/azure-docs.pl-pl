---
title: Utwórz nową ofertę usługi zarządzanej w komercyjnej witrynie Marketplace
description: Tworzenie nowej oferty usługi zarządzanej na potrzeby wyświetlania listy w witrynie Azure Marketplace przy użyciu portalu komercyjnej witryny Marketplace w centrum partnerskim.
author: JnHs
ms.author: jenhayes
manager: evansma
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/08/2020
ms.openlocfilehash: c82c3e0b6e6d11ccc33bf8556b06aeb04a6d37b5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82147906"
---
# <a name="create-a-new-managed-service-offer"></a>Utwórz nową ofertę usługi zarządzanej

> [!IMPORTANT]
> Przenosimy zarządzanie ofertami usług zarządzanych z portal Cloud Partner do Centrum partnerskiego. Dopóki Twoje oferty nie zostaną zmigrowane, postępuj zgodnie z instrukcjami w temacie [Publikowanie oferty usługi zarządzanej w witrynie Azure Marketplace](../../lighthouse/how-to/publish-managed-services-offers.md) , aby zarządzać ofertami w Portal Cloud partner.

Usługa zarządzana oferuje pomoc w włączaniu scenariuszy [usługi Azure Lighthouse](../../lighthouse/overview.md) . Gdy klient zaakceptuje ofertę usługi zarządzanej, może później dołączyć zasoby do [zarządzania zasobami delegowanymi przez platformę Azure](../../lighthouse/concepts/azure-delegated-resource-management.md).

Aby rozpocząć tworzenie ofert usług zarządzanych, należy najpierw [utworzyć konto Centrum partnerskiego](./create-account.md) i otworzyć [komercyjny pulpit nawigacyjny portalu Marketplace](https://partner.microsoft.com/dashboard/commercial-marketplace/offers)z wybraną stroną **Przegląd** . Aby opublikować ofertę usługi zarządzanej, należy dysponować [poziomem kompetencji](https://partner.microsoft.com/membership/cloud-platform-competency) lub [specjalistą dla chmury platformy Azure](https://partner.microsoft.com/membership/azure-expert-msp) .

![Komercyjny pulpit nawigacyjny portalu Marketplace w centrum partnerskim](./media/new-offer-overview.png)

>[!Note]
> Po opublikowaniu oferty zmiany wprowadzone w centrum partnerskim zostaną zaktualizowane w systemie i przechowane przed ponownym opublikowaniem. Upewnij się, że przesyłasz ofertę do publikacji po wprowadzeniu zmian.

## <a name="create-a-new-offer"></a>Tworzenie nowej oferty

Wybierz przycisk **+ Nowa oferta** , a następnie wybierz element menu **usługi zarządzanej** . Zostanie wyświetlone okno dialogowe **Nowa oferta** .

### <a name="offer-id-and-alias"></a>Identyfikator oferty i alias

- **Identyfikator oferty**: unikatowy identyfikator dla każdej oferty na Twoim koncie. Ten identyfikator będzie widoczny dla klientów w adresie URL dla oferty portalu Marketplace. Ten identyfikator może zawierać tylko małe znaki alfanumeryczne (w tym łączniki i podkreślenia, ale nie odstępy), ograniczony do 50 znaków i nie można go zmienić po wybraniu opcji **Utwórz**.  Jeśli na przykład wprowadzisz polecenie *test-Offer-1* tutaj, adres URL oferty będzie mieć `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`wartość.

- **Alias oferty**: nazwa używana do odwoływania się do oferty w centrum partnerskim. Ta nazwa nie będzie używana w portalu Marketplace i różni się od nazwy oferty i innych wartości, które będą widoczne dla klientów. Tej wartości nie można zmienić po wybraniu opcji **Utwórz**.

Po wprowadzeniu **identyfikatora oferty** i **aliasu oferty**wybierz pozycję **Utwórz**. Następnie będzie można obejść wszystkie różne części oferty.

## <a name="offer-setup"></a>Konfiguracja oferty

Na stronie **Konfiguracja oferty** są wyświetlane poniższe informacje. Pamiętaj, aby po zakończeniu tych pól wybrać opcję **Zapisz** .

## <a name="connect-lead-management"></a>Zarządzanie potencjalnymi klientami

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

Należy pamiętać, że zgodnie z [zasadami certyfikacji usług zarządzanych](https://docs.microsoft.com/legal/marketplace/certification-policies#700-managed-services)wymagany jest **obiekt docelowy potencjalnego klienta** . Spowoduje to utworzenie rekordu w systemie CRM przy każdym wdrożeniu oferty przez klienta.

Aby uzyskać więcej informacji, zobacz temat [Zarządzanie potencjalnymi klientami — Omówienie](./commercial-marketplace-get-customer-leads.md).

Pamiętaj, aby **zapisać** stronę przed przejściem do następnej sekcji.

## <a name="properties"></a>Właściwości

Strona **Właściwości** umożliwia definiowanie kategorii używanych do grupowania oferty w witrynie Marketplace oraz umów prawnych wspierających Twoją ofertę. Wybierz pozycję **Zapisz** po zakończeniu tej strony.

### <a name="category"></a>Kategoria

Wybierz co najmniej jedną i maksymalnie pięć kategorii, które będą używane do umieszczania oferty w odpowiednich obszarach wyszukiwania w portalu Marketplace. Pamiętaj, aby dowiedzieć się, jak Twoja oferta obsługuje te kategorie w opisie oferty.

### <a name="terms-and-conditions"></a>Warunki i postanowienia

Podaj własne warunki prawne i postanowienia w polu Warunki **i** postanowienia. Możesz także podać adres URL, pod którym można znaleźć warunki i postanowienia. Klienci będą musieli zaakceptować te warunki, aby wypróbować ofertę.

## <a name="offer-listing"></a>Lista oferty

Na stronie z **listą ofert** można definiować szczegóły witryny Marketplace (nazwę oferty, opis, obrazy itp.) oferty.

> [!NOTE]
> Oferta zawartości oferty (na przykład opis, dokumenty, zrzuty ekranu, warunki użytkowania itp.) nie jest wymagana w języku angielskim, tak długo, jak opis oferty zaczyna się od frazy "Ta aplikacja jest dostępna tylko w języku innym niż angielski]". Można także zapewnić *przydatny adres URL linku* do oferowania zawartości w języku innym niż ten, który jest używany w ofercie dotyczącej oferty.

### <a name="name"></a>Nazwa

Nazwa wprowadzona w tym miejscu będzie wyświetlana klientom jako tytuł oferty. To pole jest wstępnie wypełniane tekstem wprowadzonym dla **aliasu oferty** podczas tworzenia oferty, ale można zmienić tę wartość. Ta nazwa może być znakiem towarowym (i może zawierać znaki towarowe lub autorskie). Nazwa nie może być dłuższa niż 50 znaków i nie może zawierać żadnych znaków emoji.

### <a name="search-results-summary"></a>Podsumowanie wyników wyszukiwania

Podaj krótki opis oferty (do 100 znaków), która może być używana w wynikach wyszukiwania w portalu Marketplace.

### <a name="long-summary"></a>Długie podsumowanie

Podaj dłuższy opis oferty (do 256 znaków). To długie podsumowanie może być również używane w wynikach wyszukiwania w portalu Marketplace.

### <a name="description"></a>Opis

Podaj dłuższy opis oferty (do 3 000 znaków). Ten opis będzie wyświetlany klientom na liście przeglądów w portalu Marketplace. Uwzględnij swoją propozycję oferty, najważniejsze zalety, kategorie i/lub branżowe skojarzenia, szanse zakupu w aplikacji oraz wszelkie wymagane informacje.

Niektóre porady dotyczące pisania opisu:  

- Jasno opisz swoją wartość oferty w pierwszych kilku zdaniach opisu. Uwzględnij następujące elementy na swojej pozycji wartości:
  - Opis oferty
  - Typ użytkownika, który przynosi korzyści z oferty
  - Klienci muszą lub cierpią, że adres oferty
- Należy pamiętać, że pierwsze niektóre zdania mogą być wyświetlane w wynikach wyszukiwania.  
- Nie należy polegać na funkcjach i funkcjach, aby sprzedawać produkt. Zamiast tego należy skoncentrować się na dostarczanej wartości.  
- Korzystaj z specyficznych dla branży słownictwa lub takich słów, jak to możliwe.

Aby zwiększyć atrakcyjność opisu oferty, użyj edytora tekstu sformatowanego do formatowania opisu.

![Korzystanie z edytora tekstu sformatowanego](./media/text-editor2.png)

Skorzystaj z poniższych instrukcji, aby użyć edytora tekstu sformatowanego:

- Aby zmienić format zawartości, zaznacz tekst, który chcesz sformatować, i wybierz styl tekstu, jak pokazano poniżej:

     ![Zmienianie formatu tekstu przy użyciu edytora tekstu sformatowanego](./media/text-editor3.png)

- Aby dodać listę punktowaną lub numerowaną do tekstu, Użyj poniższych opcji:

     ![Używanie edytora tekstu sformatowanego do dodawania list](./media/text-editor4.png)

- Aby dodać lub usunąć wcięcie do tekstu, Użyj poniższych opcji:

     ![Używanie edytora tekstu sformatowanego do wcięcia](./media/text-editor5.png)

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

>[!Note]
>Jeśli występuje problem z przekazywaniem plików, upewnij się, że sieć lokalna nie blokuje https://upload.xboxlive.com usługi używanej przez centrum partnerskie.

#### <a name="marketplace-logos"></a>Logo witryny Marketplace

Wymagane są cztery rozmiary logo: **małe (40x40)**, **średnie (90x90)**, **duże (115x115)** i **szerokie (255x115)**. Postępuj zgodnie z poniższymi wskazówkami dotyczącymi logo:

- Projekt platformy Azure ma prostą paletę kolorów. Ogranicz liczbę podstawowych i pomocniczych kolorów w logo.
- Kolory motywu portalu to biały i czarny. Nie używaj tych kolorów jako kolorów tła swojego logo. Użyj koloru, który sprawi, że logo będzie się wyróżniać w portalu. Zalecamy proste kolory podstawowe.
- Jeśli używasz przezroczystego tła, upewnij się, że logo i tekst nie są białe, czarne ani niebieskie.
- Logo powinno być proste i bez gradientów. Nie używaj w logo tła gradientowego.
- Nie umieszczaj tekstu w logo, nawet nazwy firmy ani marki.
- Upewnij się, że logo nie jest rozciągnięte.

#### <a name="screenshots"></a>Zrzuty ekranu

Dodaj maksymalnie pięć zrzutów ekranu, które pokazują, jak działa Twoja oferta. Wszystkie zrzuty ekranu muszą mieć 1280 x 720 pikseli.

#### <a name="videos"></a>Filmy wideo

Opcjonalnie możesz dodać maksymalnie pięć filmów wideo, które demonstrują Twoją ofertę. Te filmy wideo powinny być hostowane w usłudze YouTube i/lub Vimeo. Dla każdej z nich wprowadź nazwę filmu wideo, jego adres URL i obraz miniatury filmu wideo (1280 x 720 pikseli).

#### <a name="additional-marketplace-listing-resources"></a>Dodatkowe zasoby dotyczące wyświetlania w portalu Marketplace

- [Najlepsze rozwiązania dotyczące aukcji z ofertą Marketplace](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)

## <a name="preview"></a>Wersja zapoznawcza

Przed opublikowaniem oferty na żywo w szerszej ofercie z witryny Marketplace musisz najpierw udostępnić ją w ograniczonej grupie odbiorców w wersji zapoznawczej. Dzięki temu można potwierdzić, jak oferta jest wyświetlana w portalu Azure Marketplace przed udostępnieniem jej klientom. Zespoły pomocy technicznej i inżynierów firmy Microsoft będą mogli wyświetlać Twoją ofertę w tym okresie zapoznawczym.

Możesz zdefiniować odbiorców wersji zapoznawczej, wprowadzając identyfikatory subskrypcji platformy Azure w sekcji **Podgląd odbiorców** . Możesz wprowadzić maksymalnie 10 identyfikatorów subskrypcji ręcznie lub przekazać plik CSV z maksymalnie 100 identyfikatorami subskrypcji.

Wszyscy klienci powiązani z tymi subskrypcjami będą mogli wyświetlać ofertę w portalu Azure Marketplace przed rozpoczęciem jej używania. Pamiętaj o dołączeniu własnych subskrypcji tutaj, aby można było wyświetlić podgląd oferty.

## <a name="plan-overview"></a>Przegląd planu

Każda oferta musi mieć co najmniej jeden plan (czasami określany jako jednostki SKU). Możesz dodać wiele planów, aby obsługiwać różne zestawy funkcji w różnych cenach lub dostosować konkretny plan dla ograniczonej liczby odbiorców określonych klientów. Klienci mogą przeglądać plany, które są dostępne dla nich w ramach oferty nadrzędnej.

Na stronie **Przegląd planu** wybierz pozycję **+ Utwórz nowy plan**. Następnie wprowadź **identyfikator planu** i **nazwę planu**. Obie te wartości mogą zawierać tylko małe znaki alfanumeryczne, łączniki i podkreślenia, maksymalnie 50 znaków. Te wartości mogą być widoczne dla klientów i nie można ich zmienić po opublikowaniu oferty.

Wybierz pozycję **Utwórz** po wprowadzeniu tych wartości, aby kontynuować pracę z planem. Istnieją trzy sekcje do zakończenia: **Planowanie list**, **cen i dostępności**oraz **konfiguracji technicznej**.

### <a name="plan-listing"></a>Zaplanuj listę

Najpierw Podaj **Podsumowanie wyników wyszukiwania** dla planu. Jest to krótki opis planu (do 100 znaków), który może być używany w wynikach wyszukiwania w portalu Marketplace.

Następnie wprowadź **Opis** , który zawiera bardziej szczegółowe wyjaśnienie planu.

### <a name="pricing-and-availability"></a>Cennik i dostępność

Obecnie istnieje tylko jeden model cenowy, który może być używany na potrzeby oferty usługi zarządzanej: **Bring Your Own License (BYOL)**. Oznacza to, że klienci będą rozliczani bezpośrednio za koszty związane z tą ofertą, a firma Microsoft nie nalicza opłat za Ciebie.

Sekcja **widoczność planu** pozwala wskazać, czy ten plan powinien być [prywatny](../../marketplace/private-offers.md). Jeśli opuścisz **to pole jest** niezaznaczone, Twój plan nie zostanie ograniczony do określonych klientów (lub do określonej liczby klientów).

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
- **Definicja roli**: Wybierz jedną z dostępnych ról wbudowanych usługi Azure AD z listy. Ta rola określi uprawnienia, które użytkownik w polu **Identyfikator obiektu usługi Azure AD** będzie mieć w zasobach klientów. Opisy tych ról można znaleźć w temacie [wbudowane role](../../role-based-access-control/built-in-roles.md) i [Obsługa ról dla delegowanego zarządzania zasobami platformy Azure](../../lighthouse/concepts/tenants-users-roles.md#role-support-for-azure-delegated-resource-management).
  > [!NOTE]
  > Nowe wbudowane role są dodawane do platformy Azure, ale staną się dostępne w tym miejscu, chociaż może wystąpić pewne opóźnienie przed ich wyświetleniem.
- **Role**możliwe do przypisania: Ta opcja będzie wyświetlana tylko wtedy, gdy w **definicji roli** dla tej autoryzacji wybrano administratora dostępu użytkownika. Jeśli tak, należy dodać co najmniej jedną przypisaną rolę w tym miejscu. Użytkownik w polu **Identyfikator obiektu usługi Azure AD** będzie mógł przypisywać te role do [tożsamości zarządzanych](../../active-directory/managed-identities-azure-resources/overview.md), które są wymagane w celu [wdrożenia zasad, które można skorygować](../../lighthouse/how-to/deploy-policy-remediation.md). Należy pamiętać, że żadne inne uprawnienia zwykle skojarzone z rolą administratora dostępu użytkowników będą stosowane dla tego użytkownika.

> [!TIP]
> Aby mieć pewność, że w razie potrzeby będzie można [usunąć dostęp do delegowania](../../lighthouse/how-to/remove-delegation.md) , Uwzględnij **autoryzację** z **definicją roli** ustawioną na [przydział rejestracji usług zarządzanych](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role). Jeśli ta rola nie jest przypisana, delegowane zasoby mogą zostać usunięte tylko przez użytkownika w dzierżawie klienta.

Po zakończeniu wszystkich sekcji planu możesz wybrać pozycję **+ Utwórz nowy plan** dowolną liczbę razy, aby utworzyć dodatkowe plany. Po zakończeniu wybierz pozycję **Zapisz**.

## <a name="publish"></a>Publikowanie

### <a name="submit-offer-to-preview"></a>Prześlij ofertę do wersji zapoznawczej

Po zakończeniu wszystkich wymaganych sekcji oferty wybierz pozycję **Publikuj** w prawym górnym rogu portalu. Nastąpi przekierowanie do strony **Recenzja i publikowanie** .

Jeśli po raz pierwszy publikujesz tę ofertę, możesz:

- Zobacz stan ukończenia dla każdej sekcji oferty.
  - *Nie uruchomiono* — oznacza, że sekcja nie została dotknięcia i należy ją ukończyć.
  - *Niekompletne* — oznacza, że sekcja zawiera błędy, które muszą zostać naprawione lub wymaga podania więcej informacji. Wróć do sekcji i zaktualizuj ją.
  - *Gotowe* — oznacza, że sekcja została ukończona, wszystkie wymagane dane zostały dostarczone i nie występują żadne błędy. Wszystkie sekcje oferty muszą być w stanie kompletnym, zanim będzie możliwe przesłanie oferty.
- W sekcji **uwagi dotyczące certyfikacji** Podaj instrukcje dotyczące testowania dla zespołu certyfikacji, aby upewnić się, że aplikacja została prawidłowo przetestowana, a także dodatkowe uwagi przydatne do poznania aplikacji.
- Prześlij ofertę do opublikowania, wybierając pozycję **Prześlij**. Wyślemy Ci wiadomość e-mail, gdy zostanie udostępniona wersja zapoznawcza oferty, którą możesz przejrzeć i zatwierdzić. Wróć do Centrum partnerskiego i wybierz pozycję **Przejdź na żywo** , aby uzyskać ofertę opublikowania oferty na publiczną (lub w przypadku prywatnej oferty dla odbiorców prywatnych).

### <a name="customer-experience-and-offer-management"></a>Obsługa klienta i zarządzanie ofertami

Po wdrożeniu oferty przez klienta będzie można delegować subskrypcje lub grupy zasobów na potrzeby [zarządzania zasobami delegowanymi przez platformę Azure](../../lighthouse/concepts/azure-delegated-resource-management.md). Aby uzyskać więcej informacji o tym procesie, zobacz proces dołączania [klienta](../../lighthouse/how-to/publish-managed-services-offers.md#the-customer-onboarding-process).

[Zaktualizowaną wersję oferty można opublikować](update-existing-offer.md) w dowolnym momencie. Na przykład możesz chcieć dodać nową definicję roli do wcześniej opublikowanej oferty. Po wykonaniu tej czynności klienci, którzy już dodaliśmy do oferty, będą widzieć ikonę na stronie [**dostawcy usług**](../../lighthouse/how-to/view-manage-service-providers.md) w Azure Portal, która pozwala im znać aktualizację. Każdy klient będzie mógł przejrzeć zmiany i zdecydować, czy chcą one zaktualizować do nowej wersji.

## <a name="next-steps"></a>Następne kroki

- [Aktualizowanie istniejącej oferty w witrynie Marketplace dla zastosowań komercyjnych](./update-existing-offer.md)
- [Dowiedz się więcej o usłudze Azure Lighthouse](../../lighthouse/overview.md)