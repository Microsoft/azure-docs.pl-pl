---
title: Utwórz ofertę Dynamics 365 Business Central w Microsoft AppSource
description: Jak utworzyć ofertę Dynamics 365 for Business Central w Microsoft AppSource. Utwórz listę lub Sprzedaj swoją ofertę w programie AppSource lub za pomocą programu Cloud Solution Provider (CSP).
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: navits09
ms.author: navits
ms.date: 12/02/2020
ms.openlocfilehash: 99c36354334701dcd4c7c30c5fd5039c13885525
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97358728"
---
# <a name="create-a-dynamics-365-for-business-central-offer"></a>Tworzenie oferty Dynamics 365 for Business Central

W tym artykule opisano sposób tworzenia nowej oferty Dynamics 365 Business Central. [Microsoft Dynamics 365 Business Central](https://dynamics.microsoft.com/business-central) to usługa planowania zasobów przedsiębiorstwa (ERP), która obsługuje szeroką gamę procesów firmy, w tym finansów, operacji, łańcucha dostaw, CRM i zarządzania projektami oraz handlu elektronicznego. Pakiety premium obsługują również klasyczny model wdrażania i produkcję. Wszystkie oferty dla programu Dynamics 365 Business Central muszą przejść przez nasz proces certyfikacji.

Przed rozpoczęciem [Utwórz komercyjne konto witryny Marketplace w centrum partnerskim](create-account.md) , jeśli jeszcze tego nie zrobiono. Upewnij się, że Twoje konto jest zarejestrowane w komercyjnym programie Marketplace.

>[!NOTE]
> Po opublikowaniu oferty modyfikacje oferty zostaną zaktualizowane tylko w centrum partnerskim i sklepie online po ponownym przesłaniu oferty publikacji.

## <a name="create-a-new-offer"></a>Tworzenie nowej oferty

1. Zaloguj się do [Centrum partnerskiego](https://partner.microsoft.com/dashboard/home).
2. W menu po lewej stronie wybierz pozycję **komercyjne Omówienie witryny Marketplace**  >  .
3. Na stronie Przegląd wybierz pozycję **+ Nowa oferta**  >  **Dynamics 365 Business Central**.

    ![Ilustruje menu nawigacji po lewej stronie.](./media/new-offer-dynamics-365-business-central.png)

## <a name="new-offer"></a>Nowa oferta

Wprowadź **Identyfikator oferty**. To jest unikatowy identyfikator dla każdej oferty na Twoim koncie.

- Ten identyfikator jest widoczny dla klientów w adresie sieci Web dla ofert i szablonów Azure Resource Manager, jeśli ma zastosowanie.
- Identyfikator oferty połączonej z IDENTYFIKATORem wydawcy musi być krótszy niż 40 znaków.
- Użyj tylko małych liter i cyfr. Może zawierać łączniki i podkreślenia, ale nie spacje. Na przykład, jeśli identyfikator wydawcy to `testpublisherid` i wprowadzisz polecenie **test-Offer-1**, adres sieci Web oferty będzie mieć wartość `https://appsource.microsoft.com/product/dynamics-365/testpublisherid.test-offer-1` .
- Tego identyfikatora nie można zmienić po wybraniu opcji **Utwórz**.

Wprowadź **alias oferty**. Jest to nazwa używana dla oferty w centrum partnerskim.

- Ta nazwa nie jest używana w portalu Marketplace i różni się od nazwy oferty i innych wartości widocznych dla klientów.
- Tej nazwy nie można zmienić po wybraniu opcji **Utwórz**.

Wybierz pozycję **Utwórz** , aby wygenerować ofertę i kontynuować.

## <a name="offer-setup"></a>Konfiguracja oferty

### <a name="alias"></a>Alias

Wprowadź opisową nazwę, która będzie używana w celu odwoływania się do tej oferty wyłącznie w centrum partnerskim. Ta nazwa (wstępnie wprowadzona przy tworzeniu oferty) nie będzie używana w portalu Marketplace i różni się od nazwy oferty widocznej dla klientów. Jeśli chcesz później zaktualizować nazwę oferty, przejdź do strony z [listą ofert](#offer-listing) .

### <a name="setup-details"></a>Szczegóły konfiguracji

Wybierz **Typ pakietu** , który ma zastosowanie do oferty:

- Aplikacja **dodatku** rozszerza środowisko i istniejące funkcje programu Dynamics 365 Business Central. Aby uzyskać szczegółowe informacje, zobacz [aplikacje dodatków](/dynamics365/business-central/dev-itpro/developer/readiness/readiness-add-on-apps).
- Aplikacja **Connect** może być używana w scenariuszu, w którym musi zostać ustanowione połączenie punkt-punkt między programem Dynamics 365 Business Central i rozwiązaniem lub usługą innej firmy. Aby uzyskać szczegółowe informacje, zobacz [łączenie aplikacji](/dynamics365/business-central/dev-itpro/developer/readiness/readiness-connect-apps).

**Jak chcesz, aby potencjalni klienci mogli korzystać z tej oferty dotyczącej aukcji?** wybierz opcję, której chcesz użyć dla tej oferty.

- **Pobierz teraz (bezpłatnie)** — wystaw swoją ofertę klientom bezpłatnie.
- **Bezpłatna wersja próbna (lista)** — Lista ofert dla klientów z linkiem do bezpłatnej wersji próbnej. Oferta z listą bezpłatnych wersji próbnych jest tworzona, zarządzana i konfigurowana przez usługę i nie ma subskrypcji zarządzanych przez firmę Microsoft.

    > [!NOTE]
    > Tokeny wysyłane przez aplikację za pomocą linku do wersji próbnej mogą być używane tylko w celu uzyskania informacji o użytkowniku za pomocą usługi Azure Active Directory (Azure AD) w celu zautomatyzowania tworzenia kont w aplikacji. Konta Microsoft nie są obsługiwane na potrzeby uwierzytelniania przy użyciu tego tokenu.

- **Skontaktuj się z** nami — Zbierz informacje kontaktowe klienta, łącząc system zarządzania relacjami z klientami (CRM). Klient zostanie poproszony o zgodę na udostępnienie swoich informacji. Te szczegóły klienta, wraz z nazwą oferty, IDENTYFIKATORem i źródłem witryny Marketplace, gdzie znalazły ofertę, zostaną wysłane do skonfigurowanego systemu CRM. Aby uzyskać więcej informacji o konfigurowaniu programu CRM, zobacz [Customer potencjalni klienci](#customer-leads).

### <a name="test-drive"></a>Wersja testowa

Test jest doskonałym sposobem na pokazanie oferty potencjalnym klientom, dając im możliwość "Wypróbuj przed zakupem", co spowodowało zwiększenie konwersji i generowanie wysoce wykwalifikowanych potencjalnych klientów. Aby dowiedzieć się więcej, Zacznij od [tego, co to jest dysk testowy](../what-is-test-drive.md).

Aby włączyć dysk testowy przez stały okres czasu, zaznacz pole wyboru **Włącz dysk testowy** . Aby usunąć dysk testowy z oferty, wyczyść to pole wyboru.

### <a name="customer-leads"></a>Potencjalni klienci

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

Aby uzyskać więcej informacji, zobacz temat [Zarządzanie potencjalnymi klientami — Omówienie](./commercial-marketplace-get-customer-leads.md).

Wybierz pozycję **Zapisz wersję roboczą** przed kontynuowaniem.

## <a name="properties"></a>Właściwości

Ta strona umożliwia definiowanie kategorii i branż używanych do grupowania oferty w witrynie Marketplace, wersji aplikacji oraz umów prawnych wspierających Twoją ofertę.

### <a name="categories"></a>Kategorie

Wybierz kategorie i podkategorie, aby umieścić ofertę w odpowiednich obszarach wyszukiwania w portalu Marketplace. Pamiętaj, aby opisać, w jaki sposób oferta obsługuje te kategorie w opisie oferty. Wybierz pozycję:

- Co najmniej jeden i maksymalnie dwie kategorie, w tym podstawowa i pomocnicza Kategoria (opcjonalnie).
- Do dwóch podkategorii dla każdej kategorii podstawowej i/lub pomocniczej. Jeśli żadna Podkategoria nie ma zastosowania do oferty, wybierz pozycję **nie dotyczy**.

Zapoznaj się z pełną listą kategorii i podkategorii w artykule [Oferta z najlepszymi rozwiązaniami](../gtm-offer-listing-best-practices.md).

### <a name="industries"></a>Branże

[!INCLUDE [Industry Taxonomy](includes/industry-taxonomy.md)]

### <a name="app-version"></a>Wersja aplikacji

Wprowadź numer wersji oferty. Klienci będą widzieć tę wersję na liście na stronie szczegółów oferty.

### <a name="terms-and-conditions"></a>Warunki i postanowienia

W tym miejscu podaj własne warunki prawne i postanowienia. Możesz również podać adres, pod którym można znaleźć warunki i postanowienia. Klienci będą musieli zaakceptować te warunki, aby wypróbować ofertę.

Wybierz pozycję **Zapisz wersję roboczą** przed kontynuowaniem.

## <a name="offer-listing"></a>Lista oferty

Na tej stronie można zdefiniować szczegóły oferty, takie jak nazwa oferty, opis, linki i kontakty.

> [!NOTE]
> Podaj szczegółowe informacje o ofercie w tylko jednym języku. Nie jest wymagane, aby być w języku angielskim, tak długo, jak opis oferty zaczyna się od frazy "Ta aplikacja jest dostępna tylko w języku innym niż angielski]". Można także zapewnić *przydatny adres URL linku* do oferowania zawartości w języku innym niż ten, który jest używany w ofercie dotyczącej oferty.

Oto przykład sposobu wyświetlania informacji o ofercie w Microsoft AppSource (wszelkie wymienione ceny są przeznaczone wyłącznie do celów i nie są przeznaczone do odzwierciedlenia rzeczywistych kosztów):
<!-- update screen? -->
:::image type="content" source="media/example-d365-business-central.png" alt-text="Ilustruje, w jaki sposób ta oferta pojawia się w Microsoft AppSource.":::

#### <a name="call-out-descriptions"></a>Opisy połączeń

1. Logo
2. Produkty
3. Kategorie
4. Adres pomocy technicznej (link)
5. Warunki użytkowania
6. Zasady ochrony prywatności
7. Nazwa oferty
8. Podsumowanie
9. Opis
10. Zrzuty ekranu/wideo

### <a name="marketplace-details"></a>Szczegóły witryny Marketplace

**Nazwa** wprowadzona w tym miejscu będzie wyświetlana klientom jako tytuł oferty. To pole jest wstępnie wypełnione tekstem wprowadzonym dla **aliasu oferty** podczas tworzenia oferty, ale można zmienić tę wartość. Ta nazwa może być znakiem towarowym (i może zawierać znaki towarowe lub autorskie). Nazwa nie może być dłuższa niż 50 znaków i nie może zawierać żadnych znaków emoji.

Podaj krótki opis oferty (do 100 znaków) dla **podsumowania wyników wyszukiwania**. Ten opis może być używany w wynikach wyszukiwania w portalu Marketplace.

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Rich text editor](./includes/rich-text-editor.md)]

Opcjonalnie możesz wprowadzić do trzech **słów kluczowych wyszukiwania** , aby pomóc klientom w znalezieniu oferty w portalu Marketplace. Aby uzyskać najlepsze wyniki, należy również użyć tych słów kluczowych w opisie.

Jeśli chcesz, aby klienci wiedzieli, **w jakich produktach działa aplikacja**, wprowadź maksymalnie trzy nazwy produktów.

### <a name="helpprivacy-urls"></a>Adresy URL pomocy/prywatności

Wprowadź **link pomocy dla aplikacji** (URL), w którym klienci mogą dowiedzieć się więcej o ofercie. Adres URL pomocy nie może być taki sam jak adres URL pomocy technicznej.

Wprowadź **link zasad ochrony prywatności** (URL) do zasad zachowania poufności informacji organizacji. Użytkownik jest odpowiedzialny za zapewnienie zgodności aplikacji z przepisami i przepisami dotyczącymi ochrony prywatności oraz w celu zapewnienia prawidłowych zasad zachowania poufności informacji.

### <a name="contact-information"></a>Informacje kontaktowe

Podaj nazwę, adres e-mail i numer telefonu dla **kontaktu z pomocą techniczną** i **kontaktu inżynieryjnego**. Te informacje nie są widoczne dla klientów, ale będą dostępne dla firmy Microsoft i mogą być udostępniane partnerom programu CSP.

W sekcji **skontaktuj się z pomocą techniczną** Podaj **adres URL pomocy technicznej** , pod którą partnerzy CSP mogą znaleźć obsługę oferty. Adres URL pomocy technicznej nie może być taki sam jak adres URL pomocy.

### <a name="supporting-documents"></a>Dokumenty pomocnicze

Podaj co najmniej jeden (i maksymalnie trzy) powiązane dokumenty marketingowe, takie jak oficjalne dokumenty, broszury, listy kontrolne lub prezentacje, w formacie PDF.

### <a name="marketplace-media"></a>Multimedia dla portalu Marketplace

Podaj logo i obrazy, które będą używane podczas wyświetlania oferty dla klientów. Wszystkie obrazy muszą mieć format PNG.

[!INCLUDE [logo tips](../includes/graphics-suggestions.md)]

>[!Note]
>Jeśli występuje problem z przekazywaniem plików, upewnij się, że sieć lokalna nie blokuje https://upload.xboxlive.com usługi używanej przez centrum partnerskie.

#### <a name="logos"></a>Graficznych

Podaj plik PNG dla logo o **dużym** rozmiarze. Centrum partnerskie będzie używać tego do tworzenia innych wymaganych rozmiarów. Opcjonalnie można zastąpić ten inny obraz później.

Te logo są używane w różnych miejscach na liście:

[!INCLUDE [logos-appsource-only](../includes/logos-appsource-only.md)]

[!INCLUDE [Logo tips](../includes/graphics-suggestions.md)]

#### <a name="screenshots"></a>Zrzuty ekranu

Dodaj zrzuty ekranu pokazujące, jak działa Twoja oferta. Wymagane są co najmniej trzy zrzuty ekranu i można dodać maksymalnie pięć. Wszystkie zrzuty ekranu muszą mieć 1280 x 720 pikseli i w formacie PNG.

#### <a name="videos"></a>Filmy wideo

Opcjonalnie możesz dodać do czterech filmów wideo, które demonstrują Twoją ofertę. Filmy wideo muszą być hostowane w zewnętrznej witrynie. Dla każdej z nich wprowadź nazwę filmu wideo, jego adres oraz obraz miniatury filmu wideo (1280 x 720 pikseli).

Aby uzyskać dodatkowe zasoby dotyczące aukcji z witryny Marketplace, zobacz [najlepsze rozwiązania dotyczące aukcji z ofertą Marketplace](../gtm-offer-listing-best-practices.md).

Wybierz pozycję **Zapisz wersję roboczą** przed kontynuowaniem.

## <a name="availability"></a>Dostępność

Ta strona umożliwia określenie miejsca i sposobu udostępniania oferty.

### <a name="markets"></a>Wprowadza

Aby określić rynki, w których oferta powinna być dostępna, wybierz pozycję **Edytuj rynki** , aby wyświetlić okno podręczne **wyboru rynku** .

Wybierz co najmniej jeden rynek. Wybierz **pozycję Zaznacz wszystko** , aby udostępnić ofertę na każdym możliwym rynku, lub wybierz tylko wybrane rynki. Gdy skończysz, wybierz pozycję **Zapisz**.

Wybrane tutaj ustawienia dotyczą tylko nowych nabyć; Jeśli ktoś już ma swoją aplikację na określonym rynku, a później usuniesz ten rynek, osoby, które już posiadają ofertę na rynku, będą mogły nadal z nich korzystać, ale żaden nowy klient nie będzie mógł uzyskać swojej oferty.

> [!IMPORTANT]
> Odpowiedzialność za spełnienie wszelkich lokalnych wymagań prawnych, nawet jeśli te wymagania nie są wymienione w tym miejscu lub w centrum partnerskim. Nawet w przypadku wybrania wszystkich rynków, prawa lokalnego, ograniczenia lub inne czynniki mogą uniemożliwić wystawienie niektórych ofert w niektórych krajach i regionach.

### <a name="preview-audience"></a>Podgląd odbiorców

Przed opublikowaniem oferty na żywo w szerszej ofercie z witryny Marketplace musisz najpierw udostępnić ją w ograniczonej **grupie odbiorców w wersji zapoznawczej**. Wprowadź w tym miejscu **klucz ukrycia** (dowolny ciąg, używając tylko małych liter i/lub cyfr). Członkowie Twojej grupy zapoznawczej mogą używać tego klucza Ukryj jako tokenu, aby wyświetlić podgląd oferty w portalu Marketplace.

Gdy wszystko będzie gotowe do udostępnienia oferty i usunięcia ograniczenia wersji zapoznawczej, należy usunąć **klucz Ukryj** i opublikować ponownie.

Wybierz pozycję **Zapisz wersję roboczą** przed kontynuowaniem.

## <a name="technical-configuration"></a>Konfiguracja techniczna

Ta strona definiuje szczegóły techniczne używane do nawiązania połączenia z ofertą. To połączenie umożliwia nam zainicjowanie oferty dla klienta końcowego, jeśli zdecyduje się ją nabyć.

### <a name="file-upload"></a>Przekazywanie plików

Jeśli wcześniej wybrano opcję **Dodaj do, w** której przekazano plik pakietu oferty wraz z plikami pakietu dla każdego rozszerzenia, na którym ma zależności.

#### <a name="extension-package-file"></a>Plik pakietu rozszerzenia

Przekaż plik pakietu rozszerzenia (App) dla oferty.

#### <a name="library-extension-package-file"></a>Plik pakietu rozszerzenia biblioteki

Wymagane, jeśli oferta musi być zainstalowana wraz z innym rozszerzeniem, które nie zostanie opublikowane w portalu Marketplace. Jeśli tak, Przekaż swój plik. app tutaj.

>[!NOTE]
>Plik pakietu zależności nie jest już używany. Zamiast tego Przekaż plik pakietu rozszerzenia biblioteki.

Wybierz pozycję **Zapisz wersję roboczą** przed kontynuowaniem.

<!-- ## Test drive technical configuration

This page lets you set up a demonstration ("test drive") that allows customers to try your offer before purchasing it. Learn more in [What is test drive](../what-is-test-drive.md).

To enable a test drive, select the **Enable a test drive** check box on the [Offer setup](#test-drive) tab. To remove test drive from your offer, clear this check box.

When you've finished setting up your test drive, select **Save draft** before continuing.
-->
## <a name="supplemental-content"></a>Dodatkowa zawartość

Ta strona umożliwia podanie dodatkowych informacji, które pomogą nam w sprawdzeniu oferty. Te informacje nie są widoczne dla klientów ani opublikowane w portalu Marketplace.

### <a name="target-release"></a>Wersja docelowa

Wskaż, która wersja programu Microsoft Dynamics Business Central to rozwiązanie docelowe: **Current**, **Next General** lub **Next pomocniczy**. Te informacje umożliwiają nam odpowiednie testowanie Twojego rozwiązania.

### <a name="supported-editions"></a>Obsługiwane wersje

Jeśli oferta wymaga wersji Premium systemu Microsoft Dynamics 365 Business Central, wybierz opcję tylko **Premium** . W przeciwnym razie wybierz zarówno **podstawowe** , jak i **Premium**.

### <a name="key-usage-scenario"></a>Scenariusz użycia klucza

Przekaż plik PDF, który zawiera listę scenariuszy użycia klucza oferty. Wszystkie scenariusze wymienione w tym miejscu mogą być weryfikowane przez nasz zespół ds. weryfikacji przed zatwierdzeniem oferty dla portalu Marketplace.

### <a name="test-accounts"></a>Konta testowe

Jeśli konto testowe jest konieczne, aby nasz zespół certyfikacji mógł prawidłowo przejrzeć Twoją ofertę, Przekaż plik PDF, doc lub docx do informacji o **kontach testowych** .

### <a name="app-tests-automation"></a>Automatyzacja testów aplikacji

Jeśli Twoja oferta jest aplikacją dodatku, musisz przekazać plik **automatyzacji testów aplikacji** (. app). Ten plik nie ma zastosowania do łączenia aplikacji.

Wybierz pozycję **Zapisz wersję roboczą** przed kontynuowaniem.

## <a name="publish"></a>Publikowanie

### <a name="submit-offer-to-preview"></a>Prześlij ofertę do wersji zapoznawczej

Po ukończeniu wszystkich wymaganych sekcji oferty wybierz pozycję **Przejrzyj i Opublikuj** w prawym górnym rogu portalu.

Jeśli po raz pierwszy publikujesz tę ofertę, możesz:

- Zobacz stan ukończenia dla każdej sekcji oferty.
    - **Nie uruchomiono** — sekcja nie została naruszona i należy ją ukończyć.
    - **Niekompletna** — sekcja zawiera błędy, które muszą zostać naprawione lub wymaga więcej informacji. Wróć do sekcji i zaktualizuj ją.
    - **Pełna** sekcja została ukończona, wszystkie wymagane dane zostały dostarczone i nie występują żadne błędy. Wszystkie sekcje oferty muszą być w stanie kompletnym, zanim będzie możliwe przesłanie oferty.
- W sekcji **uwagi dotyczące certyfikacji** Podaj instrukcje dotyczące testowania dla zespołu certyfikacji, aby upewnić się, że aplikacja została prawidłowo przetestowana, a także dodatkowe uwagi przydatne do poznania aplikacji.
- Prześlij ofertę do opublikowania, wybierając pozycję **Prześlij**. Wyślemy wiadomość e-mail, gdy wersja zapoznawcza oferty będzie dostępna do przeglądu i zatwierdzenia. Wróć do Centrum partnerskiego i wybierz pozycję **Przejdź na żywo** , aby opublikować swoją ofertę publicznie.

## <a name="next-steps"></a>Następne kroki

- [Aktualizowanie istniejącej oferty w witrynie Marketplace dla zastosowań komercyjnych](./update-existing-offer.md)