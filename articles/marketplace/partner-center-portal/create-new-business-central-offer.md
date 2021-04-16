---
title: Tworzenie oferty Dynamics 365 Business Central w u Microsoft AppSource
description: Jak utworzyć ofertę usługi Dynamics 365 for Business Central w Microsoft AppSource. Lista lub sprzedaż oferty w usłudze AppSource lub za pośrednictwem Dostawca rozwiązań w chmurze (CSP).
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: navits09
ms.author: navits
ms.date: 12/02/2020
ms.openlocfilehash: 001f7453c29e7a8525fb88a96dd9a867468460e3
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107501437"
---
# <a name="create-a-dynamics-365-for-business-central-offer"></a>Tworzenie oferty dynamics 365 for Business Central

W tym artykule opisano sposób tworzenia nowej oferty usługi Dynamics 365 Business Central. [Microsoft Dynamics 365 Business Central](https://dynamics.microsoft.com/business-central) to usługa do planowania zasobów przedsiębiorstwa (ERP), która obsługuje szeroką gamę procesów biznesowych, w tym finanse, operacje, łańcuch dostaw, CRM oraz zarządzanie projektami i handel elektroniczny. Pakiety Premium obsługują również klasyczny model wdrażania i produkcja. Wszystkie oferty usługi Dynamics 365 Business Central muszą przejść przez proces certyfikacji.

Przed rozpoczęciem utwórz [konto komercyjnej](../create-account.md) platformy handlowej w Partner Center, jeśli jeszcze tego nie zrobiono. Upewnij się, że Twoje konto jest zarejestrowane w programie platformy handlowej.

>[!NOTE]
> Po opublikowaniu oferty zmiany w ofercie będą aktualizowane tylko w Partner Center i sklepie online po przesłaniu oferty do opublikowania ponownie.

## <a name="create-a-new-offer"></a>Tworzenie nowej oferty

1. Zaloguj się do [Partner Center](https://partner.microsoft.com/dashboard/home).
2. W menu nav po lewej stronie wybierz pozycję **Omówienie komercyjnej platformy**  >  **handlowej.**
3. Na stronie Przegląd wybierz pozycję **+ Nowa oferta** Dynamics  >  **365 Business Central.**

    ![Ilustruje menu nawigacji po lewej stronie.](./media/new-offer-dynamics-365-business-central.png)

## <a name="new-offer"></a>Nowa oferta

Wprowadź identyfikator **oferty**. Ta wartość jest unikatowym identyfikatorem dla każdej oferty na Twoim koncie.

- Ten identyfikator jest widoczny dla klientów w adresie internetowym oferty marketplace i Azure Resource Manager szablonów, jeśli ma to zastosowanie.
- Identyfikator oferty w połączeniu z identyfikatorem wydawcy musi mieć długość poniżej 40 znaków.
- Użyj tylko małych liter i cyfr. Może zawierać łączniki i podkreślenia, ale nie może zawierać spacji. Na przykład jeśli identyfikatorem wydawcy jest , a po wprowadzeniu ciągu `testpublisherid` **test-offer-1** adres internetowy oferty będzie miał wartość `https://appsource.microsoft.com/product/dynamics-365/testpublisherid.test-offer-1` .
- Tego identyfikatora nie można zmienić po wybraniu opcji **Utwórz**.

Wprowadź alias **oferty**. Jest to nazwa używana dla oferty w Partner Center.

- Ta nazwa nie jest używana na platformie handlowej i różni się od nazwy oferty i innych wartości widocznych dla klientów.
- Tej nazwy nie można zmienić po wybraniu opcji **Utwórz**.

Wybierz **pozycję Utwórz,** aby wygenerować ofertę i kontynuować.

## <a name="offer-setup"></a>Konfiguracja oferty

### <a name="alias"></a>Alias

Wprowadź opisową nazwę, która będzie używane do odwoływać się do tej oferty wyłącznie w Partner Center. Ta nazwa (wstępnie wypełniona wartością wprowadzona podczas tworzenia oferty) nie będzie używana na platformie handlowej i jest inna niż nazwa oferty wyświetlana klientom. Jeśli chcesz później zaktualizować nazwę oferty, przejdź do strony [Oferta.](#offer-listing)

### <a name="setup-details"></a>Szczegóły konfiguracji

Wybierz typ **pakietu,** który ma zastosowanie do Twojej oferty:

- Aplikacja **dodatku rozszerza** środowisko i istniejące funkcje usługi Dynamics 365 Business Central. Aby uzyskać szczegółowe informacje, [zobacz Add-on apps (Aplikacje dodatku).](/dynamics365/business-central/dev-itpro/developer/readiness/readiness-add-on-apps)
- Aplikacja **Connect** może być używana w scenariuszu, w którym musi zostać nawiązane połączenie typu punkt-punkt między usługą lub rozwiązaniem dynamics 365 Business Central a rozwiązaniem lub usługą innej firmy. Aby uzyskać szczegółowe informacje, zobacz [Łączenie aplikacji.](/dynamics365/business-central/dev-itpro/developer/readiness/readiness-connect-apps)

W **pozycji Jak chcesz, aby potencjalni** klienci wchodzili w interakcje z tą ofertą oferty? wybierz opcję, której chcesz użyć dla tej oferty.

- **Pobierz teraz (bezpłatnie)** — bezpłatnie wyekscyj swoją ofertę dla klientów.
- **Bezpłatna wersja próbna (lista)** — lista oferty dla klientów z linkiem do bezpłatnej wersji próbnej. Bezpłatne wersje próbne oferty są tworzone, zarządzane i konfigurowane przez usługę i nie mają subskrypcji zarządzanych przez firmę Microsoft.

    > [!NOTE]
    > Tokeny, które aplikacja otrzyma za pośrednictwem linku wersji próbnej, mogą być używane tylko do uzyskiwania informacji o użytkowniku za pośrednictwem usługi Azure Active Directory (Azure AD) w celu zautomatyzowania tworzenia konta w aplikacji. Konta Microsoft nie są obsługiwane w przypadku uwierzytelniania przy użyciu tego tokenu.

- **Skontaktuj się ze mną** — zbieraj informacje kontaktowe klienta, łącząc system zarządzania relacjami z klientami (CRM). Klient zostanie poproszony o zgodę na udostępnianie swoich informacji. Te szczegóły klienta wraz z nazwą oferty, identyfikatorem i źródłem witryny Marketplace, w którym znalazła Ofertę, zostaną wysłane do skonfigurowanego systemu CRM. Aby uzyskać więcej informacji na temat konfigurowania systemu CRM, zobacz [Customer leads (Potencjalni klienci).](#customer-leads)

### <a name="test-drive"></a>Dysk testowy

Dysk testowy to doskonały sposób na zaprezentowanie oferty potencjalnym klientom przez zapewnienie im opcji "wypróbowania przed zakupem", co skutkuje zwiększoną konwersją i generowaniem wysoce kwalifikowanych potencjalnych klientów. Aby dowiedzieć się więcej, zacznij od [tematu Co to jest dysk testowy.](../what-is-test-drive.md)

Aby włączyć dysk testowy przez stały czas, zaznacz pole wyboru Włącz **dysk** testowy. Aby usunąć dysk testowy z oferty, wyczyść to pole wyboru.

### <a name="customer-leads"></a>Potencjalni klienci

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

Aby uzyskać więcej informacji, zobacz [Omówienie zarządzania potencjalnymi użytkownikami](./commercial-marketplace-get-customer-leads.md).

Wybierz **pozycję Zapisz roboczą** przed kontynuowaniem.

## <a name="properties"></a>Właściwości

Ta strona umożliwia zdefiniowanie kategorii i branż używanych do grupowania oferty na platformie handlowej, wersji aplikacji i umów prawnych wspierających tę ofertę.

### <a name="categories"></a>Kategorie

Wybierz kategorie i podkategorie, aby umieścić swoją ofertę w odpowiednich obszarach wyszukiwania na platformie handlowej. Pamiętaj, aby w opisie oferty opisać, jak Twoja oferta obsługuje te kategorie. Wybierz pozycję:

- Co najmniej jedna i maksymalnie dwie kategorie, w tym kategoria podstawowa i pomocnicza (opcjonalnie).
- Maksymalnie dwie podkategorie dla każdej kategorii podstawowej i/lub pomocniczej. Jeśli żadna podkategoria nie ma zastosowania do Twojej oferty, wybierz **pozycję Nie dotyczy.**

Zobacz pełną listę kategorii i podkategorii w [tece Najlepsze rozwiązania dotyczące ofert.](../gtm-offer-listing-best-practices.md)

### <a name="industries"></a>Branże

[!INCLUDE [Industry Taxonomy](includes/industry-taxonomy.md)]

### <a name="app-version"></a>Wersja aplikacji

Wprowadź numer wersji oferty. Ta wersja będzie dostępna dla klientów na stronie szczegółów oferty.

### <a name="terms-and-conditions"></a>Warunki i postanowienia

Podaj tutaj własne warunki i postanowienia prawne. Możesz również podać adres, pod którym można znaleźć warunki i postanowienia. Klienci będą zobowiązani do zaakceptowania tych warunków, zanim będą oni mieli możliwość wypróbowania Twojej oferty.

Przed **kontynuowaniem wybierz pozycję Zapisz** wersje robocze.

## <a name="offer-listing"></a>Lista ofert

Ta strona umożliwia zdefiniowanie szczegółów oferty, takich jak nazwa oferty, opis, linki i kontakty.

> [!NOTE]
> Podaj szczegóły oferty tylko w jednym języku. Nie musi być w języku angielskim, o ile opis oferty zaczyna się od frazy "Ta aplikacja jest dostępna tylko w języku innym niż angielski". Dopuszczalne jest również podanie przydatnego adresu *URL linku* w celu zaoferowania zawartości w języku innym niż używany w zawartości oferty.

Oto przykład sposobu, w jaki informacje o ofercie są wyświetlane w Microsoft AppSource (wszystkie wymienione ceny są tylko przykładami i nie mają odzwierciedlać rzeczywistych kosztów):

:::image type="content" source="media/example-d365-business-central.png" alt-text="Ilustruje, jak ta oferta jest wyświetlana w Microsoft AppSource.":::

### <a name="call-out-descriptions"></a>Opisy wywołań

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

Nazwa **wana** w tym miejscu będzie wyświetlana klientom jako tytuł oferty. To pole jest wstępnie wypełnione tekstem wprowadzonym dla aliasu **oferty** podczas tworzenia oferty, ale możesz zmienić tę wartość. Ta nazwa może być znakami towarowymi (i może zawierać symbole znaków towarowych lub praw autorskich). Nazwa nie może zawierać więcej niż 50 znaków i nie może zawierać żadnych emoji.

Podaj krótki opis oferty (maksymalnie 100 znaków) dla podsumowania **wyników wyszukiwania.** Ten opis może być używany w wynikach wyszukiwania na platformie handlowej.

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Rich text editor](./includes/rich-text-editor.md)]

Opcjonalnie możesz wprowadzić maksymalnie trzy słowa kluczowe **wyszukiwania,** aby ułatwić klientom znalezienie Twojej oferty na platformie handlowej. Aby uzyskać najlepsze wyniki, użyj tych słów kluczowych w opisie.

Jeśli chcesz, aby klienci wiedzieli, z jakimi produktami współpracuje **Twoja** aplikacja, wprowadź maksymalnie trzy nazwy produktów.

### <a name="helpprivacy-urls"></a>Adresy URL pomocy/prywatności

Wprowadź link **Pomoc dla aplikacji** (adresu URL), w którym klienci mogą dowiedzieć się więcej na temat Twojej oferty. Adres URL pomocy nie może być taki sam jak adres URL pomocy technicznej.

Wprowadź link **(adres** URL) zasad ochrony prywatności do zasad ochrony prywatności organizacji. Użytkownik jest odpowiedzialny za zapewnienie, że aplikacja jest zgodna z przepisami i regulacjami dotyczącymi ochrony prywatności oraz za dostarczenie ważnych zasad ochrony prywatności.

### <a name="contact-information"></a>Informacje kontaktowe

Podaj nazwę, adres e-mail i numer telefonu osoby kontaktowej pomocy **technicznej** i kontaktu **inżynieryjnego.** Te informacje nie są widoczne dla klientów, ale będą dostępne dla firmy Microsoft i mogą być udostępniane partnerom programu CSP.

W sekcji **Kontakt z pomocą** techniczną podaj adres URL pomocy **technicznej,** pod którym partnerzy CSP mogą znaleźć pomoc techniczną dla Twojej oferty. Adres URL pomocy technicznej nie może być taki sam jak adres URL pomocy.

### <a name="supporting-documents"></a>Dokumenty obsługi

W tym miejscu podaj co najmniej jeden (i maksymalnie trzy) powiązane dokumenty marketingowe, takie jak dokumenty oficjalnych, schoweki, listy kontrolne lub prezentacje, w formacie PDF.

### <a name="marketplace-media"></a>Multimedia w witrynie Marketplace

Podaj logo i obrazy, które będą używane podczas wyświetlania oferty klientom. Wszystkie obrazy muszą być w formacie PNG.

[!INCLUDE [logo tips](../includes/graphics-suggestions.md)]

>[!Note]
>Jeśli masz problem z przekazywaniem plików, upewnij się, że sieć lokalna nie blokuje usługi używanej przez program `https://upload.xboxlive.com` Partner Center.

#### <a name="logos"></a>Logo

Podaj plik PNG dla logo **o dużym** rozmiarze. Partner Center użyje tego początkowego pliku do utworzenia innych wymaganych rozmiarów. Opcjonalnie możesz zastąpić obraz o zmienionym rozmiarze własnym obrazem później.

Te logo są używane w różnych miejscach na liście:

[!INCLUDE [logos-appsource-only](../includes/logos-appsource-only.md)]

[!INCLUDE [Logo tips](../includes/graphics-suggestions.md)]

#### <a name="screenshots"></a>Zrzuty ekranu

Dodaj zrzuty ekranu, które pokazują, jak działa Oferta. Wymagane są co najmniej trzy zrzuty ekranu. Można dodać maksymalnie pięć. Wszystkie zrzuty ekranu muszą mieć format PNG 1280 x 720 pikseli.

#### <a name="videos"></a>Filmy wideo

Opcjonalnie możesz dodać do czterech filmów wideo, które pokazują Twoją ofertę. Filmy wideo muszą być hostowane w witrynie zewnętrznej. Dla każdego z nich wprowadź nazwę wideo, jego adres i miniaturę wideo (1280 x 720 pikseli).

Aby uzyskać więcej zasobów na temat ofert na platformie handlowej, zobacz [Najlepsze rozwiązania dotyczące ofert w witrynie Marketplace.](../gtm-offer-listing-best-practices.md)

Przed **kontynuowaniem wybierz pozycję Zapisz** wersje robocze.

## <a name="availability"></a>Dostępność

Ta strona umożliwia zdefiniowanie, gdzie i jak udostępnić ofertę.

### <a name="markets"></a>Rynków

Aby określić rynki, na których powinna być dostępna Twoja oferta, wybierz pozycję Edytuj **rynki,** aby wyświetlić okno podręczne **Wyboru** rynku.

Wybierz co najmniej jeden rynek. Wybierz **pozycję Wybierz wszystko,** aby udostępnić swoją ofertę na każdym możliwym rynku, lub wybierz tylko wybrane rynki. Po zakończeniu wybierz pozycję **Zapisz.**

Wybrane w tym miejscu opcje dotyczą tylko nowych pozysków. Jeśli ktoś ma już Twoją aplikację na określonym rynku i później usuniesz ten rynek, osoby, które już mają ofertę na tym rynku, mogą nadal z niego korzystać, ale żadni nowi klienci na tym rynku nie będą mogli uzyskać Twojej oferty.

> [!IMPORTANT]
> Odpowiadasz za spełnienie lokalnych wymagań prawnych, nawet jeśli te wymagania nie zostały wymienione w tym miejscu lub w Partner Center. Nawet w przypadku wybrania wszystkich rynków, przepisów lokalnych, ograniczeń lub innych czynników niektóre oferty mogą nie być wyświetlane w niektórych krajach i regionach.

### <a name="preview-audience"></a>Odbiorcy wersji zapoznawczej

Przed opublikowaniem oferty na żywo w szerszej ofercie platformy handlowej należy najpierw udostępnić ją ograniczonej gronu odbiorców **wersji zapoznawczej.** Wprowadź klucz **Ukryj** (dowolny ciąg używający tylko małych liter i/lub cyfr) w tym miejscu. Członkowie odbiorców wersji zapoznawczej mogą użyć tego klucza ukrywania jako tokenu, aby wyświetlić podgląd oferty na platformie handlowej.

Następnie, gdy wszystko będzie gotowe do udostępnić ofertę i usunąć ograniczenie wersji zapoznawczej, musisz usunąć klucz **Ukryj** i opublikować ponownie.

Wybierz **pozycję Zapisz roboczą** przed kontynuowaniem.

## <a name="technical-configuration"></a>Konfiguracja techniczna

Na tej stronie zdefiniowano szczegóły techniczne używane do nawiązywania połączenia z ofertą. To połączenie umożliwia aprowizować Ofertę dla klienta końcowego, jeśli zdecyduje się ją uzyskać.

Rozszerzenia przesłane dla oferty muszą spełniać wymagania określone na liście kontrolnej [weryfikacji technicznej.](/dynamics365/business-central/dev-itpro/developer/devenv-checklist-submission)

### <a name="file-upload"></a>Przekazywanie plików

Jeśli wcześniej **wybrano** pozycję Dodaj do , do której należy przekazać plik pakietu oferty wraz z plikami pakietów dla dowolnego rozszerzenia, od którego ma zależności.

#### <a name="extension-package-file"></a>Plik pakietu rozszerzenia

Przekaż plik pakietu rozszerzenia (.app) dla oferty.

#### <a name="library-extension-package-file"></a>Plik pakietu rozszerzenia biblioteki

Wymagane, jeśli oferta musi zostać zainstalowana wraz z innym rozszerzeniem, które nie zostanie opublikowane w witrynie Marketplace. Jeśli tak, przekaż plik app tutaj.

>[!NOTE]
>Plik pakietu zależności nie jest już używany. Zamiast tego przekaż plik pakietu rozszerzenia biblioteki.

Przed **kontynuowaniem wybierz pozycję Zapisz** wersje robocze.

## <a name="supplemental-content"></a>Zawartość uzupełniająca

Ta strona umożliwia podanie dodatkowych informacji, które pomogą nam zweryfikować Twoją ofertę. Te informacje nie są widoczne dla klientów ani publikowane na platformie handlowej.

### <a name="target-release"></a>Wersja docelowa

Wskaż wersję usługi Microsoft Dynamics Business Central, której dotyczy Twoje rozwiązanie: **Bieżąca,** **Następna główna** lub **Następna pomocnicza.** Te informacje umożliwiają nam odpowiednie przetestowanie Twojego rozwiązania.

### <a name="supported-editions"></a>Obsługiwane wersje

Jeśli Twoja oferta wymaga wersji Premium usługi Microsoft Dynamics 365 Business Central, wybierz tylko **wersję Premium.** W przeciwnym razie wybierz opcję **Essentials (Podstawowe)** i **Premium (Wersja Premium).**

### <a name="key-usage-scenario"></a>Scenariusz użycia klucza

Przekaż plik PDF, który zawiera listę kluczowych scenariuszy użycia oferty. Wszystkie wymienione tutaj scenariusze mogą zostać zweryfikowane przez nasz zespół weryfikacyjny przed zatwierdzeniem Twojej oferty na platformie handlowej.

### <a name="test-accounts"></a>Konta testowe

Jeśli do poprawnego przejrzenia oferty przez nasz zespół certyfikacji jest wymagane konto testowe, przekaż plik PDF, DOC lub DOCX z informacjami o kontach **testowych.**

### <a name="app-tests-automation"></a>Automatyzacja testów aplikacji

Jeśli Twoja oferta jest aplikacją dodatku, musisz przekazać plik **automatyzacji testów** aplikacji (.app). Ten plik nie ma zastosowania do łączenia aplikacji.

Wybierz **pozycję Zapisz roboczą** przed kontynuowaniem.

## <a name="publish"></a>Publikowanie

### <a name="submit-offer-to-preview"></a>Przesyłanie oferty do wersji zapoznawczej

Po ukończeniu wszystkich wymaganych sekcji oferty wybierz pozycję Przejrzyj i **opublikuj** w prawym górnym rogu portalu.

Jeśli po raz pierwszy publikujesz tę ofertę, możesz:

- Sprawdź stan ukończenia dla każdej sekcji oferty.
  - **Nie uruchomiono** — sekcja nie została dotkowana i należy zakończyć.
  - **Niekompletne** — sekcja zawiera błędy, które należy naprawić lub które wymagają dodatkowych informacji. Wstecz do sekcji i zaktualizuj je.
  - **Ukończono** — sekcja została ukończona, podano wszystkie wymagane dane i nie ma żadnych błędów. Wszystkie sekcje oferty muszą być w stanie kompletnym, zanim będzie można przesłać ofertę.
- W sekcji **Uwagi dotyczące certyfikacji** udostępnij zespołowi certyfikacji instrukcje dotyczące testowania, aby upewnić się, że aplikacja została prawidłowo przetestowana, oprócz wszelkich dodatkowych uwag przydatnych do zrozumienia aplikacji.
- Prześlij ofertę do opublikowania, wybierając pozycję **Prześlij.** Gdy dostępna jest wersja zapoznawcza oferty do przejrzenia i zatwierdzenia, zostanie do Ciebie wysłana wiadomość e-mail. Wróć do Partner Center i wybierz **pozycję Przejdź na żywo,** aby opublikować ofertę publicznie.

## <a name="next-steps"></a>Następne kroki

- [Aktualizowanie istniejącej oferty w witrynie Marketplace dla zastosowań komercyjnych](./update-existing-offer.md)
