---
title: Tworzenie nowej oferty usługi zarządzanej w portalu Commercial Marketplace
description: How to create a new Managed Service offer for listing in Azure Marketplace using the Commercial Marketplace portal in Partner Center.
author: JnHs
ms.author: jenhayes
manager: evansma
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/08/2020
ms.openlocfilehash: c040907c6101d11ee645f9967278d3cf297bd74a
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869810"
---
# <a name="create-a-new-managed-service-offer"></a>Tworzenie nowej oferty usługi zarządzanej

> [!IMPORTANT]
> Przenosimy zarządzanie ofertami usługi zarządzanej z portalu cloud partner do centrum partnerskiego. Dopóki oferty nie zostaną zmigrowane, postępuj zgodnie z instrukcjami w [obszarze Publikuj ofertę usługi zarządzanej w portalu Azure Marketplace,](../../lighthouse/how-to/publish-managed-services-offers.md) aby zarządzać ofertami w portalu cloud partner.

Usługa zarządzana oferuje pomoc w włączanie scenariuszy [latarni morskiej platformy Azure.](../../lighthouse/overview.md) Gdy klient zaakceptuje ofertę usługi zarządzanej, będzie mógł dołączać zasoby do [zarządzania zasobami delegowanymi platformy Azure.](../../lighthouse/concepts/azure-delegated-resource-management.md)

Aby rozpocząć tworzenie ofert usługi zarządzanej, upewnij się, że najpierw [utwórz konto Centrum partnerów](./create-account.md) i otwórz [pulpit nawigacyjny portalu komercyjnego w portalu Marketplace](https://partner.microsoft.com/dashboard/commercial-marketplace/offers)z zaznaczoną stroną **Przegląd.** Aby opublikować ofertę usługi zarządzanej, musisz mieć [poziom kompetencji Silver lub Gold Cloud Platform](https://partner.microsoft.com/membership/cloud-platform-competency) lub być [usługą Azure Expert MSP.](https://partner.microsoft.com/membership/azure-expert-msp)

![Pulpit nawigacyjny portalu Marketplace w Centrum partnerów](./media/new-offer-overview.png)

>[!Note]
> Po opublikowaniu oferty zmiany w ofercie złożonej w Centrum partnerskim zostaną zaktualizowane dopiero po ponownym opublikowaniu w systemie i na frontach sklepu. Po dokonaniu zmian należy przesłać ofertę do publikacji.

## <a name="create-a-new-offer"></a>Tworzenie nowej oferty

Wybierz przycisk **+ Nowa oferta,** a następnie wybierz pozycję menu **Usługa zarządzana.** Zostanie wyświetlone okno dialogowe **Nowa oferta.**

### <a name="offer-id-and-alias"></a>Identyfikator oferty i alias

- **Identyfikator oferty:** Unikatowy identyfikator dla każdej oferty na twoim koncie. Ten identyfikator będzie widoczny dla klientów w adresie URL oferty marketplace. Ten identyfikator może zawierać tylko małe litery alfanumeryczne (w tym łączniki i podkreślenia, ale bez odstępów), ograniczone do 50 znaków i nie można go zmienić po wybraniu opcji **Utwórz**.  Na przykład, jeśli wprowadzisz *test-offer-1* tutaj, `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`adres URL oferty będzie .

- **Alias oferty**: Nazwa używana do odwoływania się do oferty w Centrum partnerów. Ta nazwa nie będzie używana w portalu marketplace i różni się od nazwy oferty i innych wartości, które będą wyświetlane klientom. Tej wartości nie można zmienić po wybraniu opcji **Utwórz**.

Po wprowadzeniu identyfikatora **oferty** i **aliasu oferty**wybierz pozycję **Utwórz**. Będziesz wtedy mógł pracować nad wszystkimi częściami swojej oferty.

## <a name="offer-setup"></a>Konfiguracja oferty

Strona **Ustawienia oferty** prosi o następujące informacje. Pamiętaj, aby wybrać **pozycję Zapisz** po zakończeniu tych pól.

## <a name="connect-lead-management"></a>Łączenie zarządzania potencjalnymi klientami

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

Należy zauważyć, że zgodnie z [zasadami certyfikacji usług zarządzanych](https://docs.microsoft.com/legal/marketplace/certification-policies#700-managed-services)wymagane jest **miejsce docelowe potencjalnego klienta.** Spowoduje to utworzenie rekordu w systemie CRM za każdym razem, gdy klient wdraża ofertę.

Aby uzyskać więcej informacji, zobacz [Omówienie zarządzania potencjalnymi klientami](./commercial-marketplace-get-customer-leads.md).

Pamiętaj, **aby zapisać** stronę przed przejściem do następnej sekcji.

## <a name="properties"></a>Właściwości

Strona **Właściwości** umożliwia zdefiniowanie kategorii używanych do grupowania oferty na rynku oraz umów prawnych wspierających ofertę. Wybierz **pozycję Zapisz** po zakończeniu tej strony.

### <a name="category"></a>Kategoria

Wybierz co najmniej jedną i maksymalnie pięć kategorii, które zostaną wykorzystane do umieszczenia oferty w odpowiednich obszarach wyszukiwania w rynku. Pamiętaj, aby sprawdzić, w jaki sposób Twoja oferta obsługuje te kategorie w opisie oferty.

### <a name="terms-and-conditions"></a>Warunki i postanowienia

Podaj własne warunki prawne w polu **Warunki.** Możesz też podać adres URL, pod którym można znaleźć warunki. Klienci będą musieli zaakceptować te warunki, zanim będą mogli wypróbować Twoją ofertę.

## <a name="offer-listing"></a>Oferta aukcji

Strona **Oferta aukcji** umożliwia zdefiniowanie szczegółów rynku (nazwa oferty, opis, obrazy itp.) dla oferty.

> [!NOTE]
> Zawartość aukcji oferty (taka jak opis, dokumenty, zrzuty ekranu, warunki użytkowania itp.) nie musi być w języku angielskim, o ile opis oferty zaczyna się od wyrażenia" Ta aplikacja jest dostępna tylko w języku [nieanglojęzycznym]". Dopuszczalne jest również podanie *użytecznego adresu URL linku,* aby oferować treści w języku innym niż ten używany w treści aukcji Oferty.

### <a name="name"></a>Nazwa

Nazwa, którą wpiszesz w tym miejscu, będzie wyświetlana klientom jako tytuł twojej oferty. To pole jest wstępnie wypełnione tekstem wprowadzonym dla **aliasu Oferty** podczas tworzenia oferty, ale można zmienić tę wartość. Nazwa ta może być znakiem towarowym (i możesz zawierać znaki towarowe lub symbole praw autorskich). Nazwa nie może zawierać więcej niż 50 znaków i nie może zawierać żadnych emotikonów.

### <a name="search-results-summary"></a>Podsumowanie wyników wyszukiwania

Podaj krótki opis oferty (do 100 znaków), która może być używana w wynikach wyszukiwania w marketplace.

### <a name="long-summary"></a>Długie podsumowanie

Podaj dłuższy opis swojej oferty (do 256 znaków). To długie podsumowanie może być również używane w wynikach wyszukiwania w portalu Marketplace.

### <a name="description"></a>Opis

Podaj dłuższy opis swojej oferty (do 3000 znaków). Ten opis będzie wyświetlany klientom w przeglądzie aukcji w portalu marketplace. Dołącz propozycję wartości oferty, kluczowe korzyści, kategorie i/lub stowarzyszenia branżowe, możliwości zakupu w aplikacji i wszelkie wymagane informacje.

Kilka wskazówek dotyczących pisania opisu:  

- Wyraźnie opisz propozycję wartości swojej oferty w pierwszych kilku zdaniach opisu. Dołącz do swojej propozycji wartości następujące elementy:
  - Opis oferty
  - Typ użytkownika, który korzysta z oferty
  - Klient potrzebuje lub bólu, który dotyczy oferty
- Pamiętaj, że kilka pierwszych zdań może być wyświetlanych w wynikach wyszukiwania.  
- Nie polegaj na funkcjach i funkcjach sprzedaży produktu. Zamiast tego skup się na wartości, którą dostarczasz.  
- W miarę możliwości używaj słownictwa branżowego lub opartego na korzyściach.

Aby opis oferty był bardziej atrakcyjny, użyj edytora tekstu sformatowania opisu.

![Korzystanie z edytora tekstu sformatowego](./media/text-editor2.png)

Użyj następujących instrukcji, aby użyć edytora tekstu sformatowego:

- Aby zmienić format zawartości, wyróżnij tekst, który chcesz sformatować, i zaznacz styl tekstu, jak pokazano poniżej:

     ![Zmiana formatu tekstu za pomocą edytora tekstu sformatowania](./media/text-editor3.png)

- Aby dodać do tekstu listę punktowaną lub ponumerowaną, użyj poniższych opcji:

     ![Dodawanie list za pomocą edytora tekstu sformatowego](./media/text-editor4.png)

- Aby dodać lub usunąć wcięcie do tekstu, użyj poniższych opcji:

     ![Używanie edytora tekstu sformatowego do wcięcie](./media/text-editor5.png)

### <a name="privacy-policy-link"></a>Link do polityki prywatności

Wprowadź adres URL do zasad ochrony prywatności organizacji (hostowanych w witrynie). Użytkownik jest odpowiedzialny za zapewnienie zgodności aplikacji z przepisami i regulacjami dotyczącymi prywatności oraz za zapewnienie ważnej polityki prywatności.

### <a name="useful-links"></a>Przydatne łącza

Podaj opcjonalne dodatkowe dokumenty online dotyczące rozwiązania. Dodaj dodatkowe przydatne łącza, klikając **+ Dodaj łącze**.

### <a name="contact-information"></a>Informacje kontaktowe

W tej sekcji należy podać nazwę, adres e-mail i numer telefonu dla **kontaktu pomocy technicznej** i kontaktu **inżyniera.** Te informacje nie są wyświetlane klientom, ale będą dostępne dla firmy Microsoft i mogą być dostarczane partnerom CSP.

### <a name="support-urls"></a>Obsługa adresów URL

Jeśli masz witryny pomocy technicznej dla **klientów globalnych platformy Azure** i/lub klientów platformy Azure dla instytucji **rządowych,** podaj te adresy URL tutaj.

### <a name="marketplace-images"></a>Obrazy z Marketplace

W tej sekcji możesz podać logo i obrazy, które będą używane podczas wyświetlania oferty klientowi. Wszystkie obrazy muszą być w formacie png.

>[!Note]
>Jeśli masz problem z przekazywaniem plików, upewnij się, https://upload.xboxlive.com że sieć lokalna nie blokuje usługi używanej przez Centrum partnerów.

#### <a name="marketplace-logos"></a>Logo Marketplace

Wymagane są cztery rozmiary logo: **Mały (40x40),** **Średni (90x90),** **Duży (115x115)** i **Szeroki (255x115)**. Postępuj zgodnie z tymi wskazówkami dotyczącymi logo:

- Projekt platformy Azure ma prostą paletę kolorów. Ogranicz liczbę podstawowych i pomocniczych kolorów w logo.
- Kolory motywu portalu to biały i czarny. Nie używaj tych kolorów jako kolorów tła swojego logo. Użyj koloru, który sprawi, że logo będzie się wyróżniać w portalu. Zalecamy proste kolory podstawowe.
- Jeśli używasz przezroczystego tła, upewnij się, że logo i tekst nie są białe, czarne ani niebieskie.
- Logo powinno być proste i bez gradientów. Nie używaj w logo tła gradientowego.
- Nie umieszczaj tekstu w logo, nawet nazwy firmy ani marki.
- Upewnij się, że logo nie jest rozciągnięte.

#### <a name="screenshots"></a>Zrzuty ekranu

Dodaj do pięciu zrzutów ekranu, które pokazują, jak działa Twoja oferta. Wszystkie zrzuty ekranu muszą mieć rozmiary 1280 x 720 pikseli.

#### <a name="videos"></a>Filmy wideo

Opcjonalnie możesz dodać maksymalnie pięć filmów, które zademonstrują Twoją ofertę. Te filmy powinny być hostowane na YouTube i / lub Vimeo. Dla każdego z nich wprowadź nazwę filmu, jego adres URL i miniaturę filmu (1280 x 720 pikseli).

#### <a name="additional-marketplace-listing-resources"></a>Dodatkowe zasoby dotyczące aukcji w portalu marketplace

- [Najważniejsze wskazówki dotyczące ofert w marketplace](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)

## <a name="preview"></a>Wersja zapoznawcza

Zanim opublikujesz ofertę na żywo w szerszej ofercie marketplace, musisz najpierw udostępnić ją ograniczonej widowni w wersji zapoznawczej. Dzięki temu można potwierdzić, jak oferta pojawia się w portalu Azure Marketplace przed udostępnieniem jej klientom. Zespoły pomocy technicznej i inżynierów firmy Microsoft będą również mogły wyświetlać ofertę w tym okresie wersji zapoznawczej.

Odbiorców w wersji zapoznawczej można zdefiniować, wprowadzając identyfikatory subskrypcji platformy Azure w sekcji Grupa odbiorców w **wersji zapoznawczej.** Możesz ręcznie wprowadzić maksymalnie 10 identyfikatorów subskrypcji lub przesłać plik csv o maksymalnie 100 identyfikatorach subskrypcji.

Każdy klient skojarzony z tymi subskrypcjami będzie mógł wyświetlić ofertę w portalu Azure Marketplace, zanim zostanie ona wyjęta z tej usługi. Pamiętaj, aby dołączyć własne subskrypcje tutaj, aby móc wyświetlić podgląd oferty.

## <a name="plan-overview"></a>Omówienie planu

Każda oferta musi mieć jeden lub więcej planów (czasami nazywanych jednostkami SKU). Możesz dodać wiele planów do obsługi różnych zestawów funkcji w różnych cenach lub dostosować określony plan dla ograniczonej grupy odbiorców określonych klientów. Klienci mogą wyświetlać plany, które są dla nich dostępne w ramach oferty nadrzędnej.

Na stronie **Przegląd planu** wybierz pozycję **+ Utwórz nowy plan**. Następnie wprowadź identyfikator **planu** i **nazwę planu**. Obie te wartości mogą zawierać tylko małe litery alfanumeryczne, kreski i podkreślenia, maksymalnie 50 znaków. Te wartości mogą być widoczne dla klientów i nie można ich zmienić po opublikowaniu oferty.

Wybierz **pozycję Utwórz** po wprowadzeniu tych wartości, aby kontynuować pracę nad planem. Do ukończenia znajdują się trzy sekcje: **Lista planu,** **Ceny i dostępność**oraz **Konfiguracja techniczna.**

### <a name="plan-listing"></a>Lista planu

Najpierw podaj **podsumowanie wyników wyszukiwania** dla planu. Jest to krótki opis planu (do 100 znaków), który może być używany w wynikach wyszukiwania w marketplace.

Następnie wprowadź **opis,** który zawiera bardziej szczegółowe wyjaśnienie planu.

### <a name="pricing-and-availability"></a>Cennik i dostępność

Obecnie istnieje tylko jeden model cenowy, który może być używany do oferty usługi **zarządzanej: Przynieś własną licencję (BYOL)**. Oznacza to, że użytkownik będzie rozliczał klientów bezpośrednio za koszty związane z tą ofertą, a firma Microsoft nie pobiera żadnych opłat.

Sekcja **Widoczność planu** umożliwia wskazanie, czy plan ten powinien być [prywatny](../../marketplace/private-offers.md). Jeśli pole **To jest plan prywatny** niezaznaczone, plan nie będzie ograniczony do określonych klientów (lub do określonej liczby klientów).

Aby udostępnić ten plan tylko określonym klientom, wybierz opcję **Tak**. Gdy to zrobisz, musisz zidentyfikować klientów, podając ich identyfikatory subskrypcji. Można je wprowadzić jeden po drugim (dla maksymalnie 10 subskrypcji) lub przesłać plik csv (maksymalnie 10 000 subskrypcji we wszystkich planach). Pamiętaj, aby dołączyć własne subskrypcje tutaj, dzięki czemu można przetestować i zweryfikować ofertę.

> [!IMPORTANT]
> Po opublikowaniu planu jako publicznego nie można go zmienić na prywatny. Aby kontrolować, którzy klienci mogą akceptować ofertę i delegować zasoby, użyj planu prywatnego. W przypadku planu publicznego nie można ograniczyć dostępności do niektórych klientów, a nawet do określonej liczby klientów (chociaż możesz całkowicie zaprzestać sprzedaży planu, jeśli to zrobisz). Dostęp [do delegowania](../../lighthouse/how-to/onboard-customer.md#remove-access-to-a-delegation) można usunąć po zaakceptowaniu oferty przez klienta tylko wtedy, gdy podczas publikowania oferty została uwzględniona **autoryzacja** z **definicją roli** ustawioną na [Rolę usuwania przypisania rejestracji usług zarządzanych.](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) Możesz również skontaktować się z klientem i poprosić go o [usunięcie dostępu.](../../lighthouse/how-to/view-manage-service-providers.md#add-or-remove-service-provider-offers)

### <a name="technical-configuration"></a>Konfiguracja techniczna

Ta sekcja planu tworzy manifest z informacjami o autoryzacji do zarządzania zasobami klienta. Te informacje są wymagane w celu umożliwienia [zarządzania zasobami delegowanymi platformy Azure](../../lighthouse/concepts/azure-delegated-resource-management.md).

Pamiętaj, aby przejrzeć [dzierżawy, role i użytkowników w scenariuszach latarni morskiej platformy Azure,](../../lighthouse/concepts/tenants-users-roles.md#best-practices-for-defining-users-and-roles) aby dowiedzieć się, które role są obsługiwane i najlepsze rozwiązania dotyczące definiowania autoryzacji.

> [!NOTE]
> Jak wspomniano powyżej, użytkownicy i role we wpisach **autoryzacji** będą miały zastosowanie do każdego klienta, który zakupi plan. Jeśli chcesz ograniczyć dostęp do określonego klienta, musisz opublikować prywatny plan do wyłącznego użytku.

#### <a name="manifest"></a>Manifest

Najpierw podaj **wersję** dla manifestu. Użyj formatu *n.n.n* (na przykład 1.2.5).

Następnie wprowadź identyfikator **dzierżawy**. Jest to identyfikator GUID skojarzony z identyfikatorem dzierżawy usługi Azure Active Directory (Azure AD) w organizacji; oznacza to, że dzierżawa zarządzająca, z której będzie można uzyskać dostęp do zasobów swoich klientów. Jeśli nie masz tego pod ręką, możesz go znaleźć, najeżdżając kursorem na nazwę konta w prawym górnym rogu **witryny**Azure portal lub wybierając katalog Przełącznik .

Jeśli publikujesz nową wersję oferty i musisz utworzyć zaktualizowany manifest, wybierz **+ Nowy manifest**. Pamiętaj, aby zwiększyć numer wersji z poprzedniej wersji manifestu.

#### <a name="authorization"></a>Autoryzacja

Autoryzacje definiują jednostki w dzierżawie zarządzającej, które mogą uzyskiwać dostęp do zasobów i subskrypcji dla klientów, którzy zakupią plan. Każdej z tych jednostek są przypisane wbudowaną rolę, która przyznaje określonego poziomu dostępu.

Dla każdego planu można utworzyć maksymalnie dwadzieścia autoryzacji.

> [!TIP]
> W większości przypadków należy przypisać role do grupy użytkowników usługi Azure AD lub jednostki usługi, a nie do serii kont poszczególnych użytkowników. Dzięki temu można dodać lub usunąć dostęp dla poszczególnych użytkowników bez konieczności aktualizowania i ponownego publikowania planu po zmianie wymagań dostępu. Podczas przypisywania ról do grup usługi Azure AD [upewnij się, że **typem grupy** są **zabezpieczenia,** a nie **usługa Office 365**](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md). Aby uzyskać dodatkowe zalecenia, zobacz [dzierżawy, role i użytkowników w scenariuszach latarni morskiej platformy Azure.](../../lighthouse/concepts/tenants-users-roles.md)

Dla każdej **autoryzacji**należy podać następujące informacje. Następnie można wybrać **+ Dodaj autoryzację** tyle razy, ile potrzeba, aby dodać więcej użytkowników i definicje ról.

- **Identyfikator obiektu usługi Azure AD:** identyfikator usługi Azure AD użytkownika, grupy użytkowników lub aplikacji, który otrzyma określone uprawnienia (zgodnie z definicją roli) do zasobów klientów.
- **Nazwa wyświetlania obiektu usługi Azure AD:** przyjazna nazwa ułatwiające klientowi zrozumienie celu tej autoryzacji. Klient zobaczy tę nazwę podczas delegowannia zasobów.
- **Definicja roli:** Wybierz jedną z dostępnych ról wbudowanych usługi Azure AD z listy. Ta rola określi uprawnienia, które użytkownik w polu **identyfikator obiektu usługi Azure AD** będzie miał w zasobach klientów. Aby uzyskać opisy tych ról, zobacz [Wbudowane role](../../role-based-access-control/built-in-roles.md) i [Obsługa ról dla zarządzania zasobami delegowanymi platformy Azure](../../lighthouse/concepts/tenants-users-roles.md#role-support-for-azure-delegated-resource-management).
  > [!NOTE]
  > W stosownych przypadkach nowe wbudowane role są dodawane do platformy Azure, staną się one dostępne w tym miejscu, chociaż może wystąpić pewne opóźnienie, zanim się pojawią.
- **Przypisywalne role:** Ta opcja pojawi się tylko wtedy, gdy w **definicji roli** dla tej autoryzacji wybrano administratora dostępu użytkownika. Jeśli tak, należy dodać w tym miejscu jedną lub więcej ról, które można przypisać. Użytkownik w polu **Identyfikator obiektu usługi Azure AD** będzie mógł przypisać te role do tożsamości [zarządzanych,](../../active-directory/managed-identities-azure-resources/overview.md)co jest wymagane do [wdrożenia zasad, które można skorygować.](../../lighthouse/how-to/deploy-policy-remediation.md) Należy zauważyć, że żadne inne uprawnienia zwykle skojarzone z rolą Administrator dostępu użytkownika nie będą miały zastosowania do tego użytkownika.

> [!TIP]
> Aby upewnić się, że w razie potrzeby można [usunąć dostęp do delegacji,](../../lighthouse/how-to/onboard-customer.md#remove-access-to-a-delegation) dołącz **autoryzację** z **definicją roli** ustawioną na Rolę [usuwania przydziału rejestracji usług zarządzanych](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role). Jeśli ta rola nie jest przypisana, delegowane zasoby mogą zostać usunięte tylko przez użytkownika w dzierżawie klienta.

Po zakończeniu wszystkich sekcji planu możesz wybrać **+ Utwórz nowy plan** tyle razy, ile potrzeba do utworzenia dodatkowych planów. Po zakończeniu wybierz pozycję **Zapisz**.

## <a name="publish"></a>Publikowanie

### <a name="submit-offer-to-preview"></a>Prześlij ofertę do wersji zapoznawczej

Po wypełnieniu wszystkich wymaganych sekcji oferty wybierz **pozycję publikuj** w prawym górnym rogu portalu. Zostaniesz przekierowany do strony **Recenzja i publikowanie.**

Jeśli po raz pierwszy publikujesz tę ofertę, możesz:

- Zobacz stan ukończenia każdej sekcji oferty.
  - *Nie rozpoczęto* - oznacza, że sekcja nie została dotknięta i musi zostać ukończona.
  - *Niekompletne* - oznacza, że sekcja zawiera błędy, które muszą zostać naprawione lub wymaga więcej informacji, które mają być dostarczone. Wróć do sekcji i zaktualizuj ją.
  - *Complete* - oznacza, że sekcja jest kompletna, wszystkie wymagane dane zostały dostarczone i nie ma żadnych błędów. Wszystkie sekcje oferty muszą być w pełnym stanie, zanim będzie można złożyć ofertę.
- W sekcji **Uwagi dotyczące certyfikacji** podaj zespołowi certyfikacyjnemu instrukcje testowania, aby upewnić się, że aplikacja jest poprawnie testowana, a także wszelkie dodatkowe uwagi przydatne do zrozumienia aplikacji.
- Prześlij ofertę do publikacji, wybierając pozycję **Prześlij**. Wyślemy Ci wiadomość e-mail, gdy wersja zapoznawcza oferty będzie dostępna do przejrzenia i zatwierdzenia. Wróć do Centrum partnerskiego i wybierz **opcję Przejdź na żywo,** aby oferta opublikowała ofertę publicznie (lub prywatną ofertę dla odbiorców prywatnych).

### <a name="customer-experience-and-offer-management"></a>Obsługa klienta i zarządzanie ofertami

Gdy klient wdraża ofertę, będzie mógł delegować subskrypcje lub grupy zasobów do [zarządzania zasobami delegowanymi platformy Azure.](../../lighthouse/concepts/azure-delegated-resource-management.md) Aby uzyskać więcej informacji na temat tego procesu, zobacz [Proces dołączania klienta](../../lighthouse/how-to/publish-managed-services-offers.md#the-customer-onboarding-process).

W każdej chwili możesz [opublikować zaktualizowaną wersję swojej oferty.](update-existing-offer.md) Na przykład można dodać nową definicję roli do wcześniej opublikowanej oferty. Gdy to zrobisz, klienci, którzy już dodali ofertę zobaczą ikonę na stronie [**Dostawcy usług**](../../lighthouse/how-to/view-manage-service-providers.md) w witrynie Azure portal, która informuje ich, że dostępna jest aktualizacja. Każdy klient będzie mógł przejrzeć zmiany i zdecydować, czy chce zaktualizować do nowej wersji.

## <a name="next-steps"></a>Następne kroki

- [Aktualizowanie istniejącej oferty w witrynie Marketplace dla zastosowań komercyjnych](./update-existing-offer.md)
- [Dowiedz się więcej o latarni morskiej platformy Azure](../../lighthouse/overview.md)