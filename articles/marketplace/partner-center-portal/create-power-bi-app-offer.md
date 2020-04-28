---
title: Tworzenie oferty aplikacji usługi Power BI — Azure Marketplace
description: Dowiedz się, jak utworzyć i opublikować ofertę aplikacji usługi Power BI w witrynie Microsoft AppSource.
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/02/2020
ms.openlocfilehash: dc036ae3cba6aa4d3a689562afffb991fadc8c0a
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81867596"
---
# <a name="create-a-power-bi-app"></a>Tworzenie aplikacji usługi Power BI

> [!IMPORTANT]
> Przenosimy zarządzanie ofertami aplikacji Usługi Power BI z portalu Cloud Partner do Centrum partnerów. Dopóki oferty nie zostaną zmigrowane, [postępuj](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/power-bi/cpp-create-offer) zgodnie z instrukcjami w aplikacji Tworzenie aplikacji Usługi Power BI dla portalu Cloud Partner Portal, aby zarządzać ofertami.

W tym artykule opisano sposób tworzenia i publikowania oferty aplikacji usługi Power BI w witrynie Microsoft [AppSource](https://appsource.microsoft.com/).

Aby można było utworzyć ofertę aplikacji usługi Power BI, musisz mieć konto w portalu Partner Center. Jeśli jeszcze go nie utworzyłeś, zobacz [Tworzenie konta w portalu Partner Marketplace na](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account) koncie Centrum partnerów.

## <a name="create-a-new-offer"></a>Tworzenie nowej oferty

1. Zaloguj się do [Centrum partnerów](https://partner.microsoft.com/dashboard/home).
2. W menu nawigacji po lewej stronie wybierz pozycję**Przegląd** **portalu Komercyjnego** > .

    :::image type="content" source="media/power-bi-menu-overview.png" alt-text="przegląd menu rynku komercyjnego" border="false":::

3. Wybierz **+ Nowa oferta** > **Aplikacja Power BI**. Zostanie wyświetlone okno dialogowe **Nowa oferta.**

> [!IMPORTANT]
> Jeśli opcja **aplikacji Power BI App** jest&#39;t wyświetlana lub jest&#39;nie włączona, twoje konto&#39;nie ma uprawnień do tworzenia tego typu oferty. Sprawdź, czy&#39;spełniłeś wszystkie [wymagania](create-power-bi-app-overview.md) dotyczące tego typu oferty, w tym rejestrację konta dewelopera

### <a name="offer-id-and-alias"></a>Identyfikator oferty i alias

Wprowadź identyfikator **oferty**. Jest to unikatowy identyfikator dla każdej oferty na Twoim koncie.

- Ten identyfikator jest widoczny dla klientów w adresie internetowym dla oferty portalu Marketplace i szablonów usługi Azure Resource Manager, jeśli ma to zastosowanie.
- Użyj tylko małych liter i cyfr. Może zawierać łączniki i podkreślenia, ale bez spacji i jest ograniczona do 50 znaków. Na przykład, jeśli wprowadzisz **test-offer-1** , `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`adres internetowy oferty będzie .

- Nie można zmienić identyfikatora oferty po wybraniu opcji **Utwórz**.

Wprowadź **alias oferty**. Jest to nazwa używana do odwoływania się do oferty w Centrum partnerów.

- Ta nazwa nie jest używana w portalu Marketplace i różni się od nazwy oferty i innych wartości wyświetlanych klientom.
- Nie można tego zmienić po wybraniu opcji **Utwórz**.

Po wprowadzeniu tych dwóch wartości wybierz pozycję **Utwórz,** aby przejść do strony Przegląd oferty.

## <a name="offer-overview"></a>Przegląd oferty

Na stronie **Przegląd oferty** przedstawiono wizualną reprezentację kroków wymaganych do opublikowania tej oferty (zarówno ukończonych, jak i nadchodzących) oraz czasu, jaki powinien potrwać każdy krok.

Zawiera łącza do wykonywania operacji na tej ofercie na podstawie wyboru, którego dokonujesz. Przykład:

- Jeśli oferta jest wersja robocza — [usuwanie wersji roboczej oferty](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#delete-a-draft-offer)
- Jeśli oferta jest na żywo - [Przestań sprzedawać ofertę](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#stop-selling-an-offer-or-plan)
- Jeśli oferta jest w wersji zapoznawczej - [Go-live](https://docs.microsoft.com/azure/marketplace/partner-center-portal/publishing-status#publisher-approval)
- Jeśli nie zostało zakończone wylogowanie wydawcy — [anulowanie publikowania](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#cancel-publishing)

## <a name="offer-setup"></a>Konfiguracja oferty

Wykonaj następujące kroki, aby skonfigurować ofertę.

### <a name="connect-lead-management"></a>Łączenie zarządzania potencjalnymi klientami

Publikując ofertę na rynku za pomocą Centrum partnerów, należy połączyć ją z systemem crm (Customer Relationship Management). Dzięki temu możesz otrzymywać informacje kontaktowe klienta, gdy tylko ktoś wyrazi zainteresowanie produktem lub z niego korzysta.

1. Wybierz miejsce docelowe potencjalnych klientów, do którego chcesz wysyłać potencjalnych klientów. Centrum partnerskie obsługuje następujące systemy CRM:

    - [Dynamics 365](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics) dla zaangażowania klienta
    - [Marketo](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-marketo)
    - [SalesForce](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce)

    > [!NOTE]
    > Jeśli systemu CRM nie ma na liście powyżej, użyj [tabeli Azure](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table) lub [punktu końcowego https](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-https) do przechowywania danych potencjalnych klientów. Następnie wyeksportuj dane do systemu CRM.

2. Połącz ofertę z głównym miejscem docelowym podczas publikowania w Centrum partnerów.
3. Upewnij się, że połączenie z miejscem docelowym potencjalnego klienta jest poprawnie skonfigurowane. Po opublikowaniu go w Centrum partnerów, firma Microsoft sprawdź poprawność połączenia i wysłać potencjalnego klienta testowego. Podczas wyświetlania podglądu oferty przed jej wyświetleniem można również przetestować połączenie potencjalnego klienta, próbując samodzielnie kupić ofertę w środowisku podglądu.
4. Upewnij się, że połączenie z miejscem docelowym potencjalnego klienta pozostaje zaktualizowane, aby nie utracić potencjalnych klientów.

Oto kilka dodatkowych zasobów zarządzania potencjalnymi klientami:

- [Omówienie zarządzania potencjalnymi klientami](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-get-customer-leads)
- [Zarządzanie potencjalnymi klientami — często zadawane pytania](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#frequently-asked-questions)
- [Typowe błędy konfiguracji potencjalnych klientów](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#common-lead-configuration-errors-during-publishing-on-cloud-partner-portal)
- [Omówienie zarządzania potencjalnymi klientami](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf) PDF (upewnij się, że bloker wyskakujących wyskakujących jest wyłączony)

Wybierz **pozycję Zapisz pochyłość** przed przejściem do następnej sekcji Właściwości.

## <a name="properties"></a>Właściwości

Ta strona umożliwia zdefiniowanie kategorii i branż używanych do grupowanie oferty w portalu Marketplace, wersja aplikacji i umowy prawne, które obsługują twoją ofertę.

### <a name="category"></a>Kategoria

Wybierz co najmniej jedną i maksymalnie trzy kategorie. Kategorie te służą do umieszczania oferty w odpowiednich obszarach wyszukiwania w portalu marketplace i są wyświetlane na stronie szczegółów oferty. W opisie oferty wyjaśnij, w jaki sposób Twoja oferta obsługuje te kategorie.

### <a name="industry"></a>Branża

Opcjonalnie wybierz do dwóch branż i dwie branże w każdej branży. Podczas gdy kategorie są używane do wyświetlania oferty, przemysł i piony są używane w filtrach wyszukiwania i są stosowane w Witrynie Sklepowej. Jeśli oferta jest skierowana do konkretnej branży i/lub pionowej, użyj opisu oferty, aby wyjaśnić, w jaki sposób oferta obsługuje wybrane branże lub branże. Jeśli oferta jest&#39;specyficzna dla branży, pozostaw tę sekcję pustą.

> [!NOTE]
> W miarę wprowadzania nowych branż i branż w celu poprawy doświadczeń związanych z odkrywaniem ofert niektóre branże lub branże mogą nie być jeszcze widoczne na stronie sklepu. Branże i branże oznaczone znakiem (*) będą dostępne w przyszłości. Wszystkie opublikowane oferty można wykrywać za pomocą wyszukiwania słów kluczowych.
<p>&nbsp;

| **Branża** | **Podindustry** |
| --- | --- |
| *Motoryzacja | *Motoryzacja |
| Rolnictwo | *Inne - Niesegmentowane |
| Dystrybucja | *Sprzedaż hurtowa<br>Wysyłka paczek i paczek |
| Edukacja | *Szkolnictwo<br> wyższe* Wykształcenie podstawowe i średnie / K-12<br>*Biblioteki i muzea |
| Usługi finansowe | *Bankowość<br> i rynki kapitałowe* Ubezpieczenia |
| Instytucje rządowe | *Obrona i wywiad (kiedyś nazywane<br> bezpieczeństwa narodowego i publicznego)* Bezpieczeństwo publiczne i sprawiedliwość<br>*Rząd cywilny |
| Opieka zdrowotna (kiedyś nazywana zdrowiem) | *Płatnik<br> zdrowia* Dostawca usług zdrowotnych<br>*Farmaceutyki |
| Produkcja i zasoby (kiedyś nazywane produkcją) | *Chemiczne i<br> agrochemiczne* Produkcja dyskretna<br>*Energia |
| Handel detaliczny i towary konsumpcyjne (kiedyś nazywane detalicznymi) | *Towary<br> konsumpcyjne* Sprzedawców |
| * Media i komunikacja (kiedyś nazywane Media i Rozrywka) | *Media i<br> rozrywka* Telekomunikacyjnych |
| Usługi specjalistyczne | *Z<br> o.o.* Profesjonalne usługi dla partnerów |
| * Architektura i budownictwo (kiedyś nazywane Architecture Engineering) | *Inne - Niesegmentowane |
| * Gościnność i podróże | *Hotele<br> i obiekty rekreacyjne* Podróże i transport<br>*Restauracje i usługi gastronomiczne |
| *Inne branże sektora publicznego | *Leśnictwo<br> i rybołówstwo* Organizacji non-profit |
| *Nieruchomości | *Inne - Niesegmentowane |

### <a name="legal"></a>Informacje prawne

#### <a name="terms-and-conditions"></a>Warunki i postanowienia

Aby podać własne warunki niestandardowe, wpisz do 10 000 znaków w polu **Warunki.** Jeśli warunki wymagają dłuższego opisu, wpisz jeden link do miejsca, w którym można je znaleźć. Będzie wyświetlany klientom jako aktywne łącze.

Klienci muszą zaakceptować te warunki, zanim będą mogli wypróbować Twoją ofertę.

Wybierz **pozycję Zapisz pochyłość** przed przejściem do następnej sekcji Zaoferuj aukcję.

## <a name="offer-listing"></a>Oferta aukcji

W tym miejscu zdefiniujesz szczegóły oferty wyświetlane w portalu Marketplace. Obejmuje to nazwę oferty, opis, obrazy i tak dalej.

### <a name="language"></a>Język

Wybierz język, w którym twoja oferta będzie wyświetlana. Obecnie jedyną dostępną opcją jest **angielski (Stany Zjednoczone).**

Zdefiniuj szczegóły rynku (takie jak nazwa oferty, opis i obrazy) dla każdego języka/rynku. Wybierz język/nazwę rynku, aby podać te informacje.

> [!NOTE]
> Szczegóły oferty nie muszą być w języku angielskim, jeśli &quot;opis oferty zaczyna się od wyrażenia, Ta aplikacja jest dostępna tylko w języku [nieanglojęzycznym]. &quot; To&#39;również w porządku, aby zapewnić przydatny link do zaoferowania treści w języku, który&#39;s różni się od tego używanego w aukcji oferty.

### <a name="name"></a>Nazwa

Nazwa, którą tu wprowadzona zostanie, jest wyświetlana jako tytuł oferty. To pole jest wstępnie wypełnione tekstem wprowadzonym w polu **Alias oferty** podczas tworzenia oferty. Tę nazwę można później zmienić.

Nazwa:

- Może być znakiem towarowym (i możesz zawierać znaki towarowe lub symbole praw autorskich).
- Nie może mieć więcej niż 50 znaków.
- Nie można dołączać emotikonów.

### <a name="search-results-summary"></a>Podsumowanie wyników wyszukiwania

Podaj krótki opis swojej oferty. Może to mieć do 100 znaków i jest używana w wynikach wyszukiwania w portalu Marketplace.

### <a name="description"></a>Opis

Podaj dłuższy opis swojej oferty, do 3000 znaków. Jest to wyświetlane klientom w przeglądzie aukcji w portalu marketplace.

W opisie należy uwzględnić co najmniej jedną z następujących opcji:

- Wartość i kluczowe korzyści, jakie zapewnia Twoja oferta.
- Kategorii lub stowarzyszeń branżowych, lub obu.
- Możliwości zakupu w aplikacji.
- Wszelkie wymagane informacje.

Oto kilka wskazówek dotyczących pisania opisu:

- Wyraźnie opisz wartość swojej oferty w pierwszych kilku zdaniach opisu. Dołącz następujące elementy:
  - Opis oferty.
  - Typ użytkownika, który korzysta z oferty.
  - Klient potrzebuje lub wydaje adresy ofert.
- Pamiętaj, że kilka pierwszych zdań może być wyświetlanych w wynikach wyszukiwania.
- Nie polegaj na funkcjach i funkcjach sprzedaży produktu. Zamiast tego skup się na wartości, która zapewnia twoja oferta.
- Spróbuj użyć słownictwa branżowego lub sformułowania opartego na korzyściach.

Aby twoja oferta **Opis** był bardziej atrakcyjny, użyj edytora tekstu sformatowania opisu. Edytor tekstu sformatowego umożliwia dodawanie liczb, punktorów, pogrubienia, kursywy i wcjęcia, aby opis był bardziej czytelny.

:::image type="content" source="media/power-bi-rich-text-editor.png" alt-text="ilustruje edytor tekstu sformatacyjnego" border="false":::

### <a name="search-keywords"></a>Szukaj słów kluczowych

Wprowadź maksymalnie trzy opcjonalne słowa kluczowe wyszukiwania, aby pomóc klientom znaleźć ofertę w portalu marketplace. Aby uzyskać najlepsze wyniki, użyj tych słów kluczowych w opisie.

### <a name="helpprivacy-web-addresses"></a>Adresy internetowe Pomocy/Prywatności

Podaj linki, aby pomóc klientom lepiej zrozumieć twoją ofertę.

#### <a name="help-link"></a>Link pomocy

Wpisz adres internetowy, pod którym klienci mogą dowiedzieć się więcej o Twojej ofercie.

#### <a name="privacy-policy-url"></a>Adres URL polityki prywatności

Wprowadź adres internetowy do zasad ochrony prywatności organizacji. Użytkownik jest odpowiedzialny za zapewnienie, że Twoja oferta jest zgodna z przepisami i regulacjami dotyczącymi prywatności. Jesteś również odpowiedzialny za opublikowanie ważnej polityki prywatności na swojej stronie internetowej.

### <a name="contact-information"></a>Informacje kontaktowe

Należy podać nazwę, adres e-mail i numer telefonu dla **kontaktu pomocy technicznej** i kontaktu **inżyniera.** Te informacje są&#39;wyświetlane klientom. Jest on dostępny dla firmy Microsoft i może być dostarczany partnerom dostawcy rozwiązań w chmurze (CSP).

- Kontakt pomocy technicznej (wymagany): w przypadku ogólnych pytań dotyczących pomocy technicznej.
- Kontakt techniczny (wymagany): w przypadku pytań technicznych i zagadnień certyfikacyjnych.
- Kontakt z programem CSP (opcjonalnie): W przypadku pytań odsprzedawców związanych z programem CSP.

W sekcji **Kontakt pomocy technicznej** podaj adres internetowy **witryny pomocy technicznej,** gdzie partnerzy mogą znaleźć pomoc techniczną dla Twojej oferty.

### <a name="supporting-documents"></a>Dokumenty uzupełniające

Podaj co najmniej jeden i maksymalnie trzy powiązane dokumenty marketingowe w formacie PDF. Na przykład białe księgi, broszury, listy kontrolne lub prezentacje.

### <a name="marketplace-images"></a>Obrazy z Marketplace

Podaj logo i obrazy do wykorzystania w swojej ofercie. Wszystkie obrazy muszą być w formacie png. Rozmazane obrazy zostaną odrzucone.

>[!Note]
>Jeśli masz problem z przekazywaniem plików, upewnij się, https://upload.xboxlive.com że sieć lokalna nie blokuje usługi używanej przez Centrum partnerów.

#### <a name="store-logos"></a>Logo sklepu

Udostępnij pliki .png swojej oferty&#39;logo s w dwóch rozmiarach: **Mały** (48 x 48 pikseli) i **Duży** (216 x 216 pikseli).

Oba logo są wymagane i są używane w różnych miejscach na liście marketplace.

#### <a name="screenshots"></a>Zrzuty ekranu

Dodaj co najmniej jeden i maksymalnie pięć zrzutów ekranu, które pokazują, jak działa Twoja oferta. Każdy musi mieć rozmiar 1280 x 720 pikseli i w formacie png.

#### <a name="videos-optional"></a>Filmy (opcjonalnie)

Dodaj do pięciu filmów, które pokazują twoją ofertę. Wprowadź nazwę&#39;wideo, jego adres internetowy i miniaturę obrazu .png wideo o wymiarach 1280 x 720 pikseli.

#### <a name="additional-marketplace-listing-resources"></a>Dodatkowe zasoby dotyczące aukcji w portalu marketplace

Aby dowiedzieć się więcej o tworzeniu ofert, zobacz [Najlepsze wskazówki dotyczące oferty.](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)

## <a name="technical-configuration"></a>Konfiguracja techniczna

Promuj aplikację w usłudze Power BI do produkcji i podaj łącze instalatora aplikacji usługi Power BI, które umożliwia klientom zainstalowanie aplikacji. Aby uzyskać więcej informacji, zobacz [Publikowanie aplikacji z pulpitami nawigacyjnymi i raportami w usłudze Power BI](https://docs.microsoft.com/power-bi/service-create-distribute-apps).

## <a name="supplemental-content"></a>Treść uzupełniająca

Podaj dodatkowe informacje na temat swojej oferty, aby pomóc nam ją zweryfikować. Te informacje są&#39;wyświetlane klientom ani publikowane w portalu marketplace.

### <a name="validation-assets"></a>Zasoby sprawdzania poprawności

Opcjonalnie dodaj instrukcje (do 3000 znaków), aby ułatwić zespołowi sprawdzania poprawności firmy Microsoft konfigurowanie, łączenie i testowanie aplikacji. Obejmują typowe ustawienia konfiguracji, konta, parametry lub inne informacje, których można użyć do przetestowania opcji Połącz dane. Te informacje są widoczne tylko dla zespołu sprawdzania poprawności i jest używany tylko do celów sprawdzania poprawności.

## <a name="review-and-publish"></a>Przeglądanie i publikowanie

Po&#39;wypełniłeś wszystkie wymagane sekcje oferty, możesz przesłać swoją ofertę do przeglądu i opublikowania.

W prawym górnym rogu portalu wybierz pozycję **Przejrzyj i opublikuj**.

Na stronie recenzji możesz:

- Zobacz stan ukończenia każdej sekcji oferty. Możesz&#39;nie publikować, dopóki wszystkie sekcje oferty nie zostaną oznaczone jako kompletne.
  - **Nie rozpoczęto** — sekcja nie została uruchomiona i musi zostać ukończona.
  - **Niekompletne** — sekcja zawiera błędy, które muszą zostać naprawione lub wymagają podania większej ilości informacji. Zapoznaj się z sekcjami we wcześniejszej części tego dokumentu, aby uzyskać wskazówki.
  - **Ukończono** — sekcja zawiera wszystkie wymagane dane i nie ma żadnych błędów. Przed złożeniem oferty należy wypełnić wszystkie sekcje oferty.
- Przekaż zespołowi certyfikacyjne instrukcje testowania, aby upewnić się, że aplikacja jest poprawnie testowana. Ponadto należy przedstawić wszelkie dodatkowe uwagi, które są pomocne w zrozumieniu twojej oferty.

Aby przesłać ofertę publikacji, wybierz pozycję **Publikuj**.

Wyślemy Ci wiadomość e-mail z powiadomieniem, kiedy wersja zapoznawcza oferty będzie dostępna do przejrzenia i zatwierdzenia. Aby opublikować ofertę publiczną (lub prywatną, dla odbiorców prywatnych), przejdź do Centrum partnerów i wybierz **opcję Przejdź na żywo.**
