---
title: Planowanie oferty SaaS dla komercyjnej witryny Marketplace firmy Microsoft
description: Jak planować nową ofertę oprogramowania jako usługi (SaaS) na potrzeby aukcji i sprzedaży w Microsoft AppSource, w witrynie Azure Marketplace lub za pośrednictwem programu Cloud Solution Provider (CSP) przy użyciu programu Microsoft Partner Center.
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/30/2020
ms.openlocfilehash: 9034757539a3dfd8b5e43e97ec518479da396456
ms.sourcegitcommit: f6236e0fa28343cf0e478ab630d43e3fd78b9596
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/19/2020
ms.locfileid: "94917549"
---
# <a name="how-to-plan-a-saas-offer-for-the-commercial-marketplace"></a>Planowanie oferty SaaS dla komercyjnej witryny Marketplace

W tym artykule opisano różne opcje i wymagania dotyczące publikowania oferty oprogramowania jako usługi (SaaS) w portalu komercyjnym firmy Microsoft. Oferty SaaS umożliwiają klientom dostarczanie i Licencjonowanie rozwiązań oprogramowania w ramach subskrypcji online, a nie lokalnej instalacji na poszczególnych komputerach. Ten artykuł pomoże Ci w przygotowaniu oferty publikowania na rynku komercyjnym z centrum partnerskim.

## <a name="listing-options"></a>Opcje ofert

Podczas przygotowywania do opublikowania nowej oferty SaaS należy zdecydować, która opcja _listy_ ma zostać wybrana. Wybrana opcja wystawiania określa, jakie dodatkowe informacje należy podać podczas tworzenia oferty w centrum partnerskim. Na stronie  **Konfiguracja oferty** zdefiniujesz opcję wyświetlania, zgodnie z opisem w temacie [Tworzenie oferty SaaS w komercyjnej witrynie Marketplace](create-new-saas-offer.md).

W poniższej tabeli przedstawiono opcje wyświetlania ofert SaaS w komercyjnej witrynie Marketplace.

| Opcja wyświetlania | Proces transakcji |
| ------------ | ------------- |
| Skontaktuj się z nami | Klient kontaktuje się bezpośrednio z informacjami na liście.``*`` |
| Bezpłatna wersja próbna | Klient zostanie przekierowany do docelowego adresu URL za pośrednictwem Azure Active Directory (Azure AD).``*`` |
| Pobierz teraz (bezpłatnie) | Klient zostanie przekierowany do docelowego adresu URL za pośrednictwem usługi Azure AD.``*`` |
| Sprzedawanie przez firmę Microsoft  | Oferty sprzedawane za poorednictwem firmy Microsoft są nazywane ofertami _transakcyjnymi_ . Oferta, która jest transakcyjna, jest taka, w której firma Microsoft ułatwia wymianę pieniędzy na licencję na oprogramowanie w imieniu wydawcy. Firma Microsoft SaaSe oferty przy użyciu wybranego modelu cen i zarządzania transakcjami klientów w Twoim imieniu. Opłaty za użycie infrastruktury platformy Azure są naliczane za Ciebie, bezpośrednio przez partnera. Należy uwzględnić koszty związane z infrastrukturą w modelu cen. Wyjaśniono tu więcej szczegółów na temat [rozliczeń SaaS](#saas-billing) .  |
|||

``*`` Wydawca jest odpowiedzialny za obsługę wszystkich aspektów transakcji związanych z licencją na oprogramowanie, w tym między innymi kolejności, realizacji, rozliczeń, fakturowania, płatności i kolekcji.

Aby uzyskać więcej informacji na temat tych opcji, zobacz [komercyjne funkcje Transact Marketplace](marketplace-commercial-transaction-capabilities-and-considerations.md).

Po opublikowaniu oferty opcja wystaw wybrana dla oferty zostanie wyświetlona jako przycisk w lewym górnym rogu strony aukcji oferty. Na przykład poniższy zrzut ekranu przedstawia stronę aukcji oferty w witrynie Azure Marketplace przy użyciu przycisków **kontakt ze mną** i **Test Drive** .

![Przedstawia listę ofert w sklepie online.](./media/listing-options.png)

## <a name="technical-requirements"></a>Wymagania techniczne

Wymagania techniczne różnią się w zależności od opcji aukcji wybranej dla oferty.

Opcja wyświetlania _kontaktów ze mną_ nie ma wymagań technicznych. Istnieje możliwość połączenia systemu zarządzania relacjami z klientami (CRM) w celu zarządzania klientami klientów. Opisano to w sekcji [potencjalni klienci](#customer-leads) w dalszej części tego artykułu.

Opcja _Pobierz teraz (bezpłatna)_, _bezpłatna wersja próbna_ i _sprzedawanie przy użyciu_ opcji tworzenia oferty firmy Microsoft mają następujące wymagania techniczne:

- Aplikacja SaaS musi być rozwiązaniem wielodostępnym.
- Aby uwierzytelniać użytkowników, możesz włączyć zarówno konto Microsoft (MSA), jak i [Azure Active Directory (Azure AD)](https://azure.microsoft.com/services/active-directory/) .
- Należy utworzyć stronę docelową. Po zakupie oferty przez użytkownika są one kierowane do strony docelowej. Pomaga to w realizacji wszelkich dodatkowych czynności związanych z obsługą lub konfiguracją. Aby uzyskać wskazówki dotyczące tworzenia strony docelowej, zobacz następujące artykuły:
  - [Utwórz stronę docelową dla oferty SaaS z transakcyjnymi w komercyjnej witrynie Marketplace](azure-ad-transactable-saas-landing-page.md)
  - [Utwórz stronę docelową oferty bezpłatnej usługi SaaS lub wersji próbnej na komercyjnej platformie Marketplace](azure-ad-free-or-trial-landing-page.md)

Te dodatkowe wymagania techniczne mają zastosowanie tylko do oferty zakupu _przez firmę Microsoft_ (transactly):

- Usługa Azure AD z logowaniem jednokrotnym (SSO) i uwierzytelnianiem jest wymagana dla użytkownika kupowania, który uzyskuje dostęp do strony docelowej. Aby uzyskać szczegółowe wskazówki, zobacz [oferty usługi Azure AD i transacting SaaS w portalu komercyjnym](azure-ad-saas.md).
- Aby przeprowadzić integrację z platformą Azure Marketplace i Microsoft AppSource, musisz użyć [interfejsów API realizacji SaaS](./partner-center-portal/pc-saas-fulfillment-api-v2.md) . Należy uwidocznić usługę, która może współistnieć z subskrypcją SaaS, aby utworzyć, zaktualizować i usunąć konto użytkownika i plan usług. Krytyczne zmiany interfejsu API muszą być obsługiwane w ciągu 24 godzin. Niekrytyczne zmiany interfejsu API będą okresowo publikowane. Diagramy i szczegółowe wyjaśnienia opisujące użycie zebranych pól są dostępne w dokumentacji dotyczącej [interfejsów API](./partner-center-portal/pc-saas-fulfillment-api-v2.md).
- Musisz utworzyć co najmniej jeden plan dla swojej oferty. Cena Twojego planu jest naliczana na podstawie modelu cen wybranych przed opublikowaniem: _stawka ryczałtowa_ lub _dla poszczególnych użytkowników_. Więcej szczegółowych informacji na temat [planów](#plans) znajduje się w dalszej części tego artykułu.
- Klient może w dowolnej chwili zrezygnować z oferty.

### <a name="technical-information"></a>Informacje techniczne

W przypadku tworzenia oferty transakcyjnej należy zebrać poniższe informacje na stronie **konfiguracja techniczna** . W przypadku wybrania opcji przetwarzania transakcji niezależnie od tworzenia oferty transakcyjnej, Pomiń tę sekcję i przejdź do pozycji [testowanie dysków](#test-drives).

- **Adres URL strony docelowej**: adres URL witryny SaaS (na przykład: `https://contoso.com/signup` ), do której użytkownicy będą kierowani po uzyskaniu oferty od komercyjnej witryny Marketplace, wyzwalając proces konfiguracji od nowo utworzonej subskrypcji SaaS. Ten adres URL otrzyma token, który może służyć do wywoływania interfejsów API realizacji w celu uzyskania szczegółowych informacji o aprowizacji na stronie rejestracji interaktywnej.

  Ten adres URL zostanie wywołany za pomocą parametru tokenu zakupu w portalu Marketplace, który jednoznacznie identyfikuje określonego klienta SaaS zakupu. Ten token należy wymienić dla odpowiednich szczegółów subskrypcji SaaS za pomocą [interfejsu API rozpoznawania](./partner-center-portal/pc-saas-fulfillment-api-v2.md#resolve-a-purchased-subscription). Te szczegóły i wszystkie inne, które mają być zbierane, powinny być używane jako część strony sieci Web interaktywnej klienta wbudowanej w środowisko użytkownika w celu ukończenia rejestracji klienta i aktywowania zakupu. Na tej stronie użytkownik powinien zarejestrować się, korzystając z usługi Azure Active Directory (Azure AD).

  Ten adres URL z parametrem tokena identyfikacji zakupu w witrynie Marketplace zostanie również wywołany, gdy klient uruchomi zarządzane środowisko SaaS z poziomu Centrum administracyjnego usługi Azure Portal lub M365. Należy obsługiwać oba przepływy: gdy token jest dostarczany po raz pierwszy po nowym zakupie klienta i gdy zostanie on ponownie udostępniony dla istniejącego klienta zarządzającego rozwiązaniem SaaS.

    Skonfigurowana Strona docelowa powinna mieć wartość 24/7. Jest to jedyny sposób, w jaki otrzymasz powiadomienie o nowych zakupach ofert SaaS oferowanych w komercyjnym portalu Marketplace lub żądaniach konfiguracji dla aktywnej subskrypcji oferty.

- **Element webhook połączenia**: dla wszystkich zdarzeń asynchronicznych wymaganych przez firmę Microsoft (na przykład po anulowaniu subskrypcji SaaS) wymagane jest podanie adresu URL elementu webhook połączenia. Wywołamy ten adres URL, aby powiadomić Cię o zdarzeniu.

  Podano element webhook, który powinien działać w 24/7. Jest to jedyny sposób powiadomienia o aktualizacjach subskrypcji SaaS klientów zakupionych za pośrednictwem komercyjnej witryny Marketplace.

  > [!NOTE]
  > W ramach Azure Portal wymagamy utworzenia aplikacji Azure Active Directory z jedną dzierżawą [(Azure AD)](../active-directory/develop/howto-create-service-principal-portal.md) w celu umożliwienia użycia jednego identyfikatora aplikacji Azure do uwierzytelniania połączenia między naszymi dwiema usługami. Aby znaleźć [Identyfikator dzierżawy](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in), przejdź do Azure Active Directory i wybierz pozycję **Właściwości**, a następnie wyszukaj numer identyfikatora katalogu, który jest wymieniony. Na przykład `50c464d3-4930-494c-963c-1e951d15360e`.

- **Azure Active Directory identyfikator dzierżawy**: (znany również jako identyfikator katalogu). W Azure Portal wymagamy [zarejestrowania aplikacji Azure Active Directory (AD)](../active-directory/develop/howto-create-service-principal-portal.md) , aby można ją było dodać do listy kontroli dostępu (ACL) interfejsu API, aby upewnić się, że masz autoryzację do wywołania go. Aby znaleźć identyfikator dzierżawy dla aplikacji Azure Active Directory (AD), przejdź do bloku [rejestracje aplikacji](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) w Azure Active Directory. W kolumnie **Nazwa wyświetlana** wybierz aplikację. Następnie poszukaj podanego numeru **identyfikatora dzierżawy** (na przykład `50c464d3-4930-494c-963c-1e951d15360e` ).

- **Identyfikator aplikacji Azure Active Directory**: potrzebny jest również [Identyfikator aplikacji](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in). Aby uzyskać wartość, przejdź do bloku [rejestracje aplikacji](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) w Azure Active Directory. W kolumnie **Nazwa wyświetlana** wybierz aplikację. Następnie wyszukaj numer IDENTYFIKACYJNy aplikacji (klienta) na liście (na przykład `50c464d3-4930-494c-963c-1e951d15360e` ).

  Identyfikator aplikacji usługi Azure AD jest skojarzony z IDENTYFIKATORem wydawcy na koncie Centrum partnerskiego. Musisz użyć tego samego identyfikatora aplikacji dla wszystkich ofert na tym koncie.

  > [!NOTE]
  > Jeśli Wydawca ma co najmniej dwa różne konta w centrum partnerskim, należy użyć co najmniej dwóch identyfikatorów aplikacji usługi Azure AD, z których każde będzie miało konto. Każde konto partnera w centrum partnerskim musi używać unikatowego identyfikatora aplikacji usługi Azure AD dla wszystkich ofert SaaS opublikowanych za pośrednictwem tego konta.

## <a name="test-drives"></a>Wersje testowe
Możesz włączyć dysk testowy dla aplikacji SaaS. Dyski testowe zapewniają klientom dostęp do wstępnie skonfigurowanego środowiska przez określoną liczbę godzin. Możesz włączyć dyski testowe dla dowolnej opcji publikowania, jednak ta funkcja ma dodatkowe wymagania. Aby dowiedzieć się więcej na temat dysków testowych, zobacz [co to jest dysk testowy?](what-is-test-drive.md) Aby uzyskać informacje o konfigurowaniu różnych rodzajów dysków testowych, zobacz temat [konfiguracja techniczna systemu testowego](test-drive-technical-configuration.md).

> [!TIP]
> Stacja testowa jest różna od [bezpłatnej wersji próbnej](plans-pricing.md#free-trials). Możesz zaoferować wersję testową, bezpłatny okres próbny lub oba te elementy. Oba te osoby zapewniają klientom swoje rozwiązanie w ustalonym czasie. Jednak dysk testowy zawiera również praktyczny przewodnik po najważniejszych funkcjach produktu i korzyści, jakie można przedstawić w rzeczywistym scenariuszu implementacji.

## <a name="customer-leads"></a>Potencjalni klienci

Aby zbierać informacje o klientach, należy połączyć swoją ofertę z systemem zarządzania relacjami z klientami (CRM). Klient zostanie poproszony o zgodę na udostępnienie swoich informacji. Te szczegóły klienta, wraz z nazwą oferty, IDENTYFIKATORem i sklepem online, gdzie znalazły ofertę, zostaną wysłane do skonfigurowanego systemu CRM. Komercyjna witryna Marketplace obsługuje różne systemy CRM, a także opcję używania tabeli platformy Azure lub konfigurowania punktu końcowego HTTPS przy użyciu automatyzacji.

Połączenie programu CRM można dodać lub zmodyfikować w dowolnym momencie podczas tworzenia oferty lub po niej. Aby uzyskać szczegółowe wskazówki, zobacz temat [potencjalni klienci z oferty komercyjnej witryny Marketplace](partner-center-portal/commercial-marketplace-get-customer-leads.md).

## <a name="selecting-an-online-store"></a>Wybieranie sklepu online

Po opublikowaniu oferty SaaS zostanie ona wyświetlona w Microsoft AppSource, Azure Marketplace lub obu. Każdy sklep online obsługuje unikatowe wymagania klientów. AppSource dotyczy rozwiązań firmowych, a platforma Azure Marketplace dotyczy rozwiązań IT. Typ oferty, możliwości Transact i kategorie określają, gdzie Twoja oferta zostanie opublikowana. Kategorie i podkategorie są mapowane na każdy sklep online na podstawie typu rozwiązania. 

Jeśli Twoja oferta *SaaS to rozwiązanie* IT (Azure Marketplace) i rozwiązanie biznesowe (AppSource), wybierz kategorię i podkategorię, która ma zastosowanie do każdego sklepu online. Oferty opublikowane w ramach obu magazynów online powinny mieć wartość propozycja jako rozwiązanie IT *i* rozwiązanie biznesowe.

> [!IMPORTANT]
> Oferty SaaS z [naliczanymi opłatami](partner-center-portal/saas-metered-billing.md) są dostępne w witrynie Azure Marketplace i Azure Portal. Oferty SaaS z tylko planami prywatnymi są dostępne za pomocą Azure Portal.

| Naliczanie opłat | Plan publiczny | Plan prywatny | Dostępne w: |
|---|---|---|---|
| Tak             | Tak         | Nie           | Portal Azure Marketplace i Azure Portal |
| Tak             | Tak         | Tak          | Azure Marketplace i Azure Portal * |
| Tak             | Nie          | Tak          | Tylko Azure Portal |
| Nie              | Nie          | Tak          | Tylko Azure Portal |
|||||

&#42; plan prywatny oferty będzie dostępny tylko za pośrednictwem Azure Portal

Na przykład oferta z naliczaniem opłatowym i tylko planem prywatnym (bez planu publicznego) zostanie zakupiona przez klientów w Azure Portal. Dowiedz się więcej o [ofertach prywatnych w komercyjnym portalu Microsoft Marketplace](private-offers.md).

Aby uzyskać szczegółowe informacje na temat opcji listy obsługiwanych przez sklepy online, zobacz [Opcje wyświetlania i cennika według sklepu online](determine-your-listing-type.md#listing-and-pricing-options-by-online-store). Aby uzyskać więcej informacji na temat kategorii i podkategorii, zobacz [Kategorie i podkategorie w komercyjnym portalu Marketplace](categories.md).

## <a name="legal-contracts"></a>Umowy prawne

Aby uprościć proces zakupów dla klientów i zmniejszyć złożoność prawną dla dostawców oprogramowania, firma Microsoft oferuje standardowy kontrakt, którego możesz użyć dla ofert w portalu komercyjnym. W przypadku oferowania oprogramowania zgodnie z umową standardowa klienci muszą tylko raz odczytywać i akceptować ją i nie muszą tworzyć niestandardowych warunków i postanowień.

Jeśli zdecydujesz się na korzystanie z kontraktu standardowego, możesz dodać postanowienia dotyczące uniwersalnej poprawki i maksymalnie 10 niestandardowych zmian do standardowej umowy. Możesz również użyć własnych warunków i postanowień zamiast standardowej umowy. Te szczegóły będą zarządzane na stronie **Właściwości** . Aby uzyskać szczegółowe informacje, zobacz [Standard Contract for Microsoft Commercial Marketplace](standard-contract.md).

> [!NOTE]
> Po opublikowaniu oferty przy użyciu standardowej umowy dotyczącej komercyjnej witryny Marketplace nie można użyć własnych niestandardowych warunków i postanowień. Jest to scenariusz "lub". Możesz zaoferować swoje rozwiązanie w ramach standardowej umowy lub własnych warunków i postanowień. Jeśli chcesz zmodyfikować warunki kontraktu standardowego, możesz to zrobić za pomocą standardowych poprawek kontraktu.

## <a name="offer-listing-details"></a>Szczegóły listy ofert

Gdy tworzysz [nową ofertę SaaS](create-new-saas-offer.md) w centrum partnerskim, wprowadzisz tekst, obrazy, opcjonalne filmy wideo i inne szczegóły na stronie z **listą ofert** . Są to informacje, które użytkownicy zobaczą po znalezieniu oferty w portalu komercyjnym, jak pokazano w poniższym przykładzie.

:::image type="content" source="./media/example-saas-1.png" alt-text="Ilustruje, w jaki sposób ta oferta pojawia się w Microsoft AppSource.":::

**Opisy połączeń**

1. Logo
2. Kategorie
3. Branże
4. Adres pomocy technicznej (link)
5. Warunki użytkowania
6. Zasady ochrony prywatności
7. Nazwa oferty
8. Podsumowanie
9. Opis
10. Zrzuty ekranu/wideo
11. Dokumenty

W poniższym przykładzie przedstawiono listę ofert w Azure Portal.

![Ilustruje listę ofert w Azure Portal.](./media/example-managed-services.png)

**Wywołaj opisy**

1. Tytuł
1. Opis
1. Przydatne łącza
1. Zrzuty ekranu

> [!NOTE]
> Zawartość oferty nie jest wymagana w języku angielskim, jeśli opis oferty zaczyna się od frazy "Ta aplikacja jest dostępna tylko w języku [język inny niż angielski]".

Aby ułatwić szybkie tworzenie oferty, przygotuj niektóre z tych elementów przed czasem. Poniższe elementy są wymagane, o ile nie zaznaczono inaczej.

- **Nazwa**: Ta nazwa zostanie wyświetlona jako tytuł oferty w komercyjnej witrynie Marketplace. Nazwa może być znakiem towarowym. Nie może zawierać znaków emoji (chyba że są to symbole towarowe i praw autorskich) i muszą być ograniczone do 50 znaków.
- **Podsumowanie wyników wyszukiwania**: opisz przeznaczenie lub funkcję oferty jako pojedyncze zdanie bez podziałów wierszy na 100 znaków lub mniej. To podsumowanie jest używane w komercyjnych wynikach wyszukiwania na liście w portalu Marketplace.
- **Opis**: ten opis zostanie wyświetlony na liście komercyjnych informacji o ofercie Marketplace. Rozważ uwzględnienie propozycji wartości, najważniejszych korzyści, planowanej bazy użytkowników, wszelkich kategorii lub związków branżowych, możliwości zakupu w aplikacji, wszelkie wymagane ujawnienie oraz link, aby dowiedzieć się więcej.

    To pole tekstowe zawiera kontrolki edytora tekstu sformatowanego, których można użyć, aby dowiedzieć się więcej. Możesz również użyć tagów HTML do sformatowania opisu. W tym polu można wprowadzić do 3 000 znaków tekstu, w tym znaczników HTML. Aby uzyskać dodatkowe wskazówki, zobacz [Napisz doskonały opis aplikacji](/windows/uwp/publish/write-a-great-app-description).

- **Instrukcje wprowadzenie**: Jeśli wybierzesz opcję sprzedaży oferty za poorednictwem firmy Microsoft (oferta z transakcją), to pole jest wymagane. Te instrukcje pomagają klientom połączyć się z ofertą SaaS. Możesz dodać do 3 000 znaków tekstu i linki do bardziej szczegółowej dokumentacji online.
- **Wyszukaj słowa kluczowe** (opcjonalnie): Podaj do trzech słów kluczowych wyszukiwania, których klienci mogą używać w celu znalezienia oferty w sklepach online. Nie musisz zawierać **nazwy** oferty i **opisu**: ten tekst jest automatycznie uwzględniany w wyszukiwaniu.
- **Link zasad ochrony prywatności**: adres URL firmowych zasad zachowania poufności informacji. Należy podać prawidłowe zasady ochrony prywatności i są one odpowiedzialne za zapewnienie zgodności aplikacji z przepisami i przepisami dotyczącymi ochrony prywatności.
- **Informacje kontaktowe**: należy podać następujące kontakty w organizacji:
  - **Kontakt z pomocą techniczną**: Podaj nazwę, numer telefonu i adres E-mail partnerów firmy Microsoft do użycia, gdy klienci otworzą bilety. Należy również uwzględnić adres URL witryny sieci Web pomocy technicznej.
  - **Kontakt inżynieryjny**: Podaj nazwę, numer telefonu i adres E-mail firmy Microsoft do użycia bezpośrednio w przypadku wystąpienia problemów z ofertą. Te informacje kontaktowe nie są wymienione na rynku komercyjnym.
  - **Kontakt programu z programem CSP** (opcjonalnie): Podaj nazwę, numer telefonu i adres e-mail, Jeśli zrezygnujesz z programu CSP, aby ci partnerzy mogli skontaktować się z Tobą w odpowiedzi na pytania. Możesz również dodać adres URL do materiałów marketingowych.
- **Przydatne linki** (opcjonalnie): możesz udostępnić linki do różnych zasobów użytkownikom oferty. Na przykład fora, często zadawane pytania i informacje o wersji.
- **Dokumenty pomocnicze**: można dostarczyć do trzech dokumentów dostępnych dla klientów, takich jak oficjalne dokumenty, broszury, listy kontrolne lub prezentacje programu PowerPoint.
- **Multimedia — logo**: podaj plik PNG dla **dużego** logo. Centrum partnerskie będzie używać tego do tworzenia **małych** i **średnich** logo. Opcjonalnie można zastąpić je innymi obrazami później.

   - Duże (od 216 x 216 do 350 x 350 px, wymagane)
   - Średnia (90 x 90 pikseli, opcjonalnie)
   - Mały (48 x 48 pikseli, opcjonalnie)

  Te logo są używane w różnych miejscach w sklepach online:

  - Małe logo pojawia się w wynikach wyszukiwania Azure Marketplace i na stronie głównej Microsoft AppSource i wynikach wyszukiwania.
  - Średnie logo pojawia się podczas tworzenia nowego zasobu w Microsoft Azure.
  - Duże logo pojawia się na stronie aukcji oferty w witrynie Azure Marketplace i Microsoft AppSource.

- **Zrzuty ekranu**: należy dodać co najmniej jeden i maksymalnie pięć zrzutów ekranu z następującymi wymaganiami, które pokazują, jak działa Twoja oferta:
  - 1280 x 720 pikseli
  - plik PNG
  - Musi zawierać podpis
- **Multimedia — wideo** (opcjonalnie): możesz dodać do czterech filmów wideo z następującymi wymaganiami, które demonstrują Twoją ofertę:
  - Nazwa
  - Adres URL: musi być hostowany tylko w serwisie YouTube lub Vimeo.
  - Miniatura: plik 1280 x 720. png

> [!Note]
> Oferta musi być zgodna z ogólnymi [komercyjnymi zasadami certyfikacji portalu Marketplace](/legal/marketplace/certification-policies#100-general) oraz [oprogramowaniem jako zasady usługi](/legal/marketplace/certification-policies#1000-software-as-a-service-saas) do opublikowania na komercyjnym rynku.

## <a name="preview-audience"></a>Podgląd odbiorców
Odbiorca w wersji zapoznawczej może uzyskać dostęp do oferty przed opublikowaniem jej w sklepach online w celu przetestowania kompleksowej funkcjonalności przed opublikowaniem jej na żywo. Na stronie **Podgląd odbiorców** można zdefiniować ograniczonych odbiorców w wersji zapoznawczej. To ustawienie jest niedostępne, jeśli zdecydujesz się na niezależne przetwarzanie transakcji zamiast sprzedawania oferty przez firmę Microsoft. Jeśli tak, możesz pominąć tę sekcję i przejść do [dodatkowych możliwości sprzedaży](#additional-sales-opportunities).

> [!NOTE]
> Odbiorca w wersji zapoznawczej różni się od planu prywatnego. Plan prywatny jest dostępny tylko dla określonych odbiorców. Pozwala to na negocjowanie planu niestandardowego z konkretnymi klientami. Aby uzyskać więcej informacji, zobacz następną sekcję: plany.

Możesz wysyłać zaproszenia do adresów e-mail konta Microsoft (MSA) lub Azure Active Directory (Azure AD). Ręcznie Dodaj maksymalnie 10 adresów e-mail lub zaimportuj do 20 za pomocą pliku CSV. Jeśli Twoja oferta już istnieje, możesz zdefiniować odbiorcę w wersji zapoznawczej do testowania wszelkich zmian lub aktualizacji oferty.

## <a name="plans"></a>Plany

Oferty transakcyjne wymagają co najmniej jednego planu. Plan definiuje zakres i limity rozwiązania oraz powiązane ceny. Możesz utworzyć wiele planów dla oferty, aby dać klientom różne opcje techniczne i cenowe. Jeśli zdecydujesz się na przetwarzanie transakcji niezależnie zamiast tworzenia oferty transakcyjnej, Strona **plany** nie będzie widoczna. Jeśli tak, Pomiń tę sekcję i przejdź do [dodatkowych możliwości sprzedaży](#additional-sales-opportunities).

Zobacz [plany i cenniki dla ofert komercyjnych Marketplace](plans-pricing.md) , aby uzyskać ogólne wytyczne dotyczące planów, w tym modeli cenowych, bezpłatnych wersji próbnych i planów prywatnych. W poniższych sekcjach omówiono dodatkowe informacje specyficzne dla ofert SaaS.

### <a name="saas-pricing-models"></a>Modele cenowe SaaS

Oferty SaaS mogą korzystać z jednego z dwóch modeli cenowych w każdym planie: _stawka ryczałtowa_ lub _na użytkownika_. Wszystkie plany w tej samej ofercie muszą być skojarzone z tym samym modelem cen. Na przykład oferta nie może mieć jednego planu, który ma stałą stawkę i inny plan, który jest na użytkownika.

Stała **stawka** — umożliwia dostęp do oferty przy użyciu jednej ceny miesięcznej lub rocznej stawki ryczałtowej. Jest to czasami określane jako Cennik oparty na witrynie. Korzystając z tego modelu cen, można opcjonalnie zdefiniować plany taryfowe używające interfejsu API usługi pomiarowej Marketplace do naliczania opłat klientom za użycie, które nie są objęte stałą stawką. Aby uzyskać więcej informacji na temat rozliczeń naliczanych, zobacz [naliczanie opłat za SaaS przy użyciu komercyjnej usługi pomiarowej Marketplace](./partner-center-portal/saas-metered-billing.md). Należy również użyć tej opcji, jeśli zachowanie użycia usługi SaaS jest w serii.

**Za użytkownika** — umożliwia dostęp do oferty przy użyciu ceny na podstawie liczby użytkowników, którzy mają dostęp do oferty lub zajmują stanowiska. Korzystając z tego modelu opartego na użytkownikach, można ustawić minimalną i maksymalną liczbę użytkowników obsługiwaną przez plan. Można utworzyć wiele planów w celu skonfigurowania różnych punktów cenowych na podstawie liczby użytkowników. Te pola są opcjonalne. Jeśli pole pozostanie puste, liczba użytkowników zostanie zinterpretowana jako nie mającej limitu (minimum od 1 do maksimum, tak jak usługa może obsługiwać). Te pola mogą być edytowane w ramach aktualizacji planu.

> [!IMPORTANT]
> Po opublikowaniu oferty nie można zmienić modelu cen. Ponadto wszystkie plany dla tej samej oferty muszą mieć ten sam model cen.

### <a name="saas-billing"></a>Rozliczenia SaaS

W przypadku aplikacji SaaS, które są uruchamiane w ramach subskrypcji platformy Azure (wydawcy), użycie infrastruktury jest rozliczane bezpośrednio. Klienci nie widzą rzeczywistych opłat za użycie infrastruktury. Opłaty za użycie infrastruktury platformy Azure należy powiązać z cenami licencji na oprogramowanie, aby zrekompensować koszt infrastruktury wdrożonej w celu uruchomienia rozwiązania.

Oferty aplikacji SaaS, które są sprzedawane za pośrednictwem firmy Microsoft, są comiesięczne lub roczne w oparciu o stałą opłatę za użytkownika lub opłaty za użycie przy użyciu [usługi naliczania](./partner-center-portal/saas-metered-billing.md)opłat. Komercyjny Portal rynku działa na modelu agencji, zgodnie z którym wydawcy ustawili ceny, klienci z firmą Microsoft Bills, a firma Microsoft płaci przychody dla wydawców w trakcie wstrzymania opłaty za Agencję.

W poniższym przykładzie przedstawiono przykładowy podział kosztów i wypłaty w celu przedstawienia modelu agencji. W tym przykładzie firma Microsoft rozlicza $100,00 od klienta na potrzeby licencji na oprogramowanie i płaci do wydawcy $80,00.

| Koszt licencji | $100 miesięcznie |
| ------------ | ------------- |
| Koszt użycia platformy Azure (D1/1-rdzeniowe) | Rozliczana bezpośrednio z wydawcą, a nie klientem |
| Klient jest rozliczany przez firmę Microsoft | $100,00 miesięcznie (w przypadku wydawców należy uwzględnić koszty związane z założeniami lub przekazaniem infrastruktury w ramach opłaty licencyjnej) |
| **Rachunki firmy Microsoft** | **$100 miesięcznie** |
| Firma Microsoft płaci 80% kosztu licencji<br>`*` W przypadku kwalifikujących się aplikacji SaaS firma Microsoft płaci 90% kosztu licencji| $80,00 miesięcznie<br>``*`` $90,00 miesięcznie |
|||

**`*` Zmniejszona opłata za usługę Marketplace** — w przypadku niektórych ofert SaaS opublikowanych w komercyjnej witrynie Marketplace firma Microsoft obniży opłatę za usługę portalu Marketplace z 20% (zgodnie z opisem w umowie Microsoft Publisher) do 10%. Oferty, które chcesz zakwalifikować, muszą zostać wystawione przez firmę Microsoft jako usługa Azure IP zachęcani. Przed końcem każdego miesiąca kalendarzowego należy spełnić co najmniej pięć dni roboczych, aby otrzymać obniżoną opłatę za usługę Marketplace dla danego miesiąca. Zmniejszona opłata za usługę Marketplace ma również zastosowanie do zachęcani maszyn wirtualnych platformy Azure, aplikacji zarządzanych i innych oferowanych z nich ofert IaaS z obsługą transakcji dostępnych za pośrednictwem komercyjnej witryny Marketplace.

## <a name="additional-sales-opportunities"></a>Dodatkowe możliwości sprzedaży

Możesz zrezygnować z obsługi kanałów marketingowych i sprzedaży obsługiwanych przez firmę Microsoft. Podczas tworzenia oferty w centrum partnerskim na końcu procesu zobaczysz dwie karty:

- Odsprzedaż **za pomocą dostawców CSP**: Użyj tej opcji, aby zezwolić partnerom dostawcy rozwiązań Microsoft Cloud (CSP) na odsprzedaż Twojego rozwiązania w ramach powiązanej oferty. Aby uzyskać więcej informacji, zobacz [program Cloud Solution Provider](cloud-solution-providers.md) .

- **Sprzedawanie z firmą Microsoft**: Ta opcja umożliwia zespołom ds. sprzedaży firmy Microsoft rozpatrywanie odpowiednich rozwiązań dla adresów IP podczas oceny potrzeb klientów. Aby uzyskać szczegółowe informacje na temat przygotowywania oferty do oceny, zobacz temat [sprzedawanie w centrum partnerskim](./partner-center-portal/commercial-marketplace-co-sell.md) .

## <a name="next-steps"></a>Następne kroki

- [Jak utworzyć ofertę SaaS w komercyjnej witrynie Marketplace](create-new-saas-offer.md)
- [Najlepsze rozwiązania dotyczące ofert](gtm-offer-listing-best-practices.md)
