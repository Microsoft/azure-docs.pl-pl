---
title: Tworzenie oferty aplikacji Power BI w portalu komercyjnym firmy Microsoft
description: Dowiedz się, jak utworzyć i opublikować ofertę Power BI aplikacji Microsoft AppSource.
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/19/2020
ms.openlocfilehash: 9c31c6ca4ccb5ff328faa5db1803134aa6b0c873
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2020
ms.locfileid: "83701138"
---
# <a name="create-a-power-bi-app-for-microsoft-appsource"></a>Tworzenie aplikacji Power BI dla Microsoft AppSource

W tym artykule opisano sposób tworzenia i publikowania oferty aplikacji Power BI w programie Microsoft [AppSource](https://appsource.microsoft.com/).

Przed rozpoczęciem [Utwórz komercyjne konto witryny Marketplace w centrum partnerskim](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account) , jeśli jeszcze tego nie zrobiono. Upewnij się, że Twoje konto jest zarejestrowane w komercyjnym programie Marketplace.

## <a name="create-a-new-offer"></a>Tworzenie nowej oferty

1. Zaloguj się do [Centrum partnerskiego](https://partner.microsoft.com/dashboard/home).
2. W menu po lewej stronie wybierz pozycję **komercyjne Omówienie witryny Marketplace**  >  **Overview**.
3. Na stronie Przegląd wybierz pozycję **+ Nowa oferta**  >  **Power BI aplikacji usługi**.

   ![Ilustruje menu nawigacji po lewej stronie.](./media/new-offer-pbi-app.png)

> [!NOTE]
> Po opublikowaniu oferty zmiany wprowadzone w centrum partnerskim są wyświetlane w obszarze witryny w sklepie po ponownym opublikowaniu oferty. Pamiętaj, aby zawsze ponownie publikować po wprowadzeniu zmian.

> [!IMPORTANT]
> Jeśli **aplikacja usługi Power BI** nie jest wyświetlana lub nie jest włączona, Twoje konto nie ma uprawnień do utworzenia tego typu oferty. Sprawdź, czy zostały spełnione wszystkie [wymagania](create-power-bi-app-overview.md) dotyczące tego typu oferty, w tym rejestrowanie dla konta dewelopera.

## <a name="new-offer"></a>Nowa oferta

Wprowadź **Identyfikator oferty**. To jest unikatowy identyfikator dla każdej oferty na Twoim koncie.

- Ten identyfikator jest widoczny dla klientów w adresie sieci Web dla ofert i szablonów Azure Resource Manager, jeśli ma zastosowanie.
- Użyj tylko małych liter i cyfr. Może zawierać łączniki i podkreślenia, ale nie spacje i jest ograniczone do 50 znaków. Jeśli na przykład wprowadzisz polecenie **test-Offer-1** tutaj, adres internetowy oferty będzie miał wartość `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` .
- Nie można zmienić identyfikatora oferty po wybraniu pozycji **Utwórz**.

Wprowadź **alias oferty**. Jest to nazwa używana dla oferty w centrum partnerskim.

- Ta nazwa nie jest używana w portalu Marketplace i różni się od nazwy oferty i innych wartości widocznych dla klientów.
- Aliasu oferty nie można zmienić po wybraniu pozycji **Utwórz**.

Wybierz pozycję **Utwórz** , aby wygenerować ofertę i kontynuować.

## <a name="offer-overview"></a>Przegląd oferty

Na tej stronie przedstawiono wizualną reprezentację kroków wymaganych do opublikowania oferty (zarówno ukończona, jak i nadchodząca) oraz czas, w którym należy wykonać poszczególne kroki.

Zawiera ona linki do wykonywania operacji na tej ofercie w zależności od dokonanego wyboru. Na przykład:

- Jeśli oferta jest ofertą typu wersja robocza, [Usuń wersję roboczą](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#delete-a-draft-offer)
- Jeśli oferta jest aktywna [, Zatrzymaj sprzedawanie oferty](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#stop-selling-an-offer-or-plan)
- Jeśli oferta jest dostępna w wersji zapoznawczej — [Przejdź na żywo](https://docs.microsoft.com/azure/marketplace/partner-center-portal/publishing-status#publisher-approval)
- Jeśli nie ukończono wylogowania wydawcy — [Anulowanie publikowania](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#cancel-publishing)

## <a name="offer-setup"></a>Konfiguracja oferty

### <a name="connect-lead-management"></a>Zarządzanie potencjalnymi klientami

Po opublikowaniu oferty w portalu Marketplace przy użyciu Centrum partnerskiego należy połączyć ją z systemem zarządzania relacjami z klientami (CRM). Pozwala to na otrzymywanie informacji kontaktowych klienta, gdy tylko ktoś wyrazi zainteresowanie lub skorzysta z produktu.

1. Wybierz miejsce docelowe potencjalnych klientów, do którego chcesz wysyłać potencjalnych klientów. Centrum partnerskie obsługuje następujące systemy CRM:

    - [Dynamics 365](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics) dla zaangażowania klienta
    - [Marketo](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-marketo)
    - [Salesforce](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce)

    > [!NOTE]
    > Jeśli Twój system CRM nie jest wymieniony powyżej, użyj [tabeli platformy Azure](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table) lub [punktu końcowego HTTPS](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-https) do przechowywania danych potencjalnych klientów. Następnie wyeksportuj dane do systemu CRM.

2. Połącz ofertę z miejscem docelowym potencjalnego klienta podczas publikowania w centrum partnerskim.
3. Upewnij się, że połączenie z miejscem docelowym potencjalnego klienta jest skonfigurowane prawidłowo. Po opublikowaniu go w centrum partnerskim sprawdzimy połączenie i wyślesz potencjalnego klienta. Podczas wyświetlania podglądu oferty przed jej rozpoczęciem możesz również przetestować połączenie z liderem, próbując zakupić ofertę samodzielnie w środowisku wersji zapoznawczej.
4. Upewnij się, że połączenie z miejscem docelowym potencjalnego klienta zostało zaktualizowane, więc nie utracisz żadnych potencjalnych klientów.

Poniżej przedstawiono kilka dodatkowych zasobów zarządzania potencjalnym liderem:

- [Omówienie zarządzania potencjalnymi klientami](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-get-customer-leads)
- [Zarządzanie potencjalnymi klientami — często zadawane pytania](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#frequently-asked-questions)
- [Typowe błędy konfiguracji potencjalnych klientów](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#common-lead-configuration-errors-during-publishing-on-cloud-partner-portal)
- [Omówienie zarządzania potencjalnymi klientami](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf) PDF (Upewnij się, że blokowanie wyskakujących okienek jest wyłączone)

Wybierz pozycję **Zapisz wersję roboczą** przed kontynuowaniem.

## <a name="properties"></a>Właściwości

Ta strona umożliwia definiowanie kategorii i branż używanych do grupowania oferty w witrynie Marketplace, wersji aplikacji oraz umów prawnych, które obsługują Twoją ofertę.

### <a name="category"></a>Kategoria

Wybierz co najmniej jedną i maksymalnie trzy kategorie. Te kategorie służą do umieszczania oferty w odpowiednich obszarach wyszukiwania w witrynie Marketplace i są wyświetlane na stronie szczegółów oferty. W opisie oferty Wyjaśnij, w jaki sposób oferta obsługuje te kategorie.

### <a name="industry"></a>Branża

Opcjonalnie możesz wybrać maksymalnie dwie branże i dwie pionowe w poszczególnych branżach. Chociaż kategorie są używane do wyświetlania oferty, branżowe i pionowe są używane w filtrach wyszukiwania i są stosowane w sklepie. Jeśli Twoja oferta jest przeznaczona dla określonej branży i/lub w pionie, Skorzystaj z opisu oferty, aby wyjaśnić, w jaki sposób oferta obsługuje wybrane branże lub pionowo. Jeśli Twoja oferta nie jest zależna od branży, pozostaw tę sekcję pustą.

> [!NOTE]
> Gdy pracujemy nad wprowadzeniem nowych branż i pionowych w celu usprawnienia funkcji odnajdywania ofert, niektóre branże lub pionowe mogą jeszcze nie być widoczne w witrynie sklepu. Branże i pionowe z oznaczeniem (*) będą dostępne w przyszłości. Wszystkie opublikowane oferty są wykrywalne za pośrednictwem wyszukiwania słów kluczowych.
<p>&nbsp;

| **Branża** | **Podbranża** |
| --- | --- |
| * Motoryzacyjne | * Motoryzacyjne |
| Rolnictwo | * Inne — niesegmentacja |
| Dystrybucja | * Sprzedaż hurtowa<br>Paczka i wysyłka pakietu |
| Education | *Szkolnictwo <br> wyższe* Edukacja podstawowa i dodatkowa/K-12<br>* Biblioteki i muzea |
| Usługi finansowe | *Bankowość i rynki <br> kapitałowe* Zakład |
| Instytucje rządowe | *Obronność i analiza (używana do nazywania bezpieczeństwa narodowego i publicznego <br> )* Bezpieczeństwo publiczne i wymiar sprawiedliwości<br>* Rząd cywilny |
| Opieka zdrowotna (używana do nazywania kondycji) | *Płatnik <br> kondycji* Dostawca kondycji<br>* Środki farmaceutyczne |
| Produkcja i zasoby (używane do wywoływania produkcji) | *Chemiczne i Agrochemical <br> * Produkcja dyskretna<br>* Energia |
| Sprzedaż detaliczna i towary konsumenckie (używane do nazywania sprzedaży detalicznej) | *Towary <br> konsumenckie* Detalicznych |
| * Multimedia i komunikacja (używane do nazywania multimediów i rozrywki) | *Multimedia i rozrywka <br> * Komunikacyjny |
| Usługi specjalistyczne | *Informacje <br> prawne* Profesjonalne usługi partnerskie |
| * Architektura i konstrukcja (używana do nazywania inżynierii architektury) | * Inne — niesegmentacja |
| * Hotelarstwo i podróż | *Hotele i wypoczynek <br> * Podróże i transport<br>* Usługi dla restauracji i żywności |
| * Inne branże sektora publicznego | *Leśnictwo i rybołówstwo <br> * Organizacji niedochodowych |
| * Nieruchomości | * Inne — niesegmentacja |

### <a name="legal"></a>Informacje prawne

#### <a name="terms-and-conditions"></a>Warunki i postanowienia

Aby zapewnić własne niestandardowe warunki i postanowienia, wprowadź do 10 000 znaków w polu **warunki i** postanowienia. Jeśli warunki i postanowienia wymagają dłuższego opisu, wprowadź jedno łącze sieci Web, w którym można je znaleźć. Będzie ona wyświetlana klientom jako aktywne łącze.

Aby wypróbować ofertę, klienci muszą zaakceptować te warunki.

Wybierz pozycję **Zapisz wersję roboczą** przed przejściem do następnej sekcji, na liście oferty.

## <a name="offer-listing"></a>Lista oferty

W tym miejscu zdefiniujesz szczegóły oferty, które są wyświetlane w portalu Marketplace. Obejmuje to nazwę oferty, opis, obrazy i tak dalej.

### <a name="language"></a>Język

Wybierz język, w którym zostanie wyświetlona oferta. Obecnie tylko w **języku angielskim (Stany Zjednoczone)** jest jedyną dostępną opcją.

Zdefiniuj szczegóły witryny Marketplace (takie jak nazwa oferty, opis i obrazy) dla każdego języka/rynku. Wybierz nazwę języka/rynku, aby podać te informacje.

> [!NOTE]
> Szczegóły oferty nie muszą znajdować się w języku angielskim, jeśli opis oferty zaczyna się od frazy "Ta aplikacja jest dostępna tylko w języku innym niż angielski]". Istnieje również możliwość udostępnienia przydatnego linku do oferowania zawartości w języku innym niż ten, który jest używany na liście ofert.

### <a name="name"></a>Nazwa

Wprowadzona nazwa zostanie wyświetlona jako tytuł oferty. To pole jest wstępnie wypełnione tekstem wprowadzonym w polu **alias oferty** podczas tworzenia oferty. Tę nazwę można później zmienić.

Nazwa:

- Mogą być znakami towarowymi (i może zawierać znaki towarowe lub autorskie).
- Długość nie może przekraczać 50 znaków.
- Nie może zawierać znaków emoji.

### <a name="search-results-summary"></a>Podsumowanie wyników wyszukiwania

Podaj krótki opis oferty. Może to być maksymalnie 100 znaków i jest używana w wynikach wyszukiwania w portalu Marketplace.

### <a name="description"></a>Opis

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Long description-3](./includes/long-description-3.md)]

### <a name="search-keywords"></a>Słowa kluczowe wyszukiwania

Wprowadź do trzech opcjonalnych słów kluczowych wyszukiwania, aby pomóc klientom w znalezieniu oferty w portalu Marketplace. Aby uzyskać najlepsze wyniki, należy również użyć tych słów kluczowych w opisie.

### <a name="helpprivacy-web-addresses"></a>Adresy sieci Web pomocy/prywatności

Podaj linki, aby pomóc klientom w lepszym zrozumieniu oferty.

#### <a name="help-link"></a>Link pomocy

Wprowadź adres sieci Web, na którym klienci mogą dowiedzieć się więcej o ofercie.

#### <a name="privacy-policy-url"></a>Adres URL zasad ochrony prywatności

Wprowadź adres internetowy zasad zachowania poufności informacji organizacji. Użytkownik jest odpowiedzialny za zapewnienie zgodności oferty z przepisami i przepisami dotyczącymi ochrony prywatności. Użytkownik jest odpowiedzialny za publikowanie prawidłowych zasad zachowania poufności informacji w witrynie sieci Web.

### <a name="contact-information"></a>Informacje kontaktowe

Musisz podać nazwę, adres e-mail i numer telefonu dla **kontaktu z pomocą techniczną** i **kontaktu inżynieryjnego**. Te informacje nie są widoczne dla klientów. Jest ona dostępna dla firmy Microsoft i może być świadczona partnerom dostawcy rozwiązań w chmurze (CSP).

- Kontakt z pomocą techniczną (wymagany): w przypadku ogólnych pytań dotyczących pomocy technicznej.
- Kontakt inżynieryjny (wymagany): w przypadku pytań technicznych i problemów z certyfikacją.
- Kontakt programu CSP (opcjonalnie): w przypadku pytań odsprzedawców związanych z programem CSP.

W sekcji **skontaktuj się z pomocą techniczną** Podaj adres internetowy **witryny internetowej pomocy technicznej** , w której partnerzy mogą znaleźć pomoc techniczną dla Twojej oferty.

### <a name="supporting-documents"></a>Dokumenty pomocnicze

Podaj co najmniej jeden i maksymalnie trzy powiązane dokumenty marketingowe w formacie PDF. Na przykład oficjalne dokumenty, broszury, listy kontrolne lub prezentacje.

### <a name="marketplace-images"></a>Obrazy z witryny Marketplace

Podaj logo i obrazy, które mają być używane z ofertą. Wszystkie obrazy muszą mieć format PNG. Rozmyte obrazy zostaną odrzucone.

>[!NOTE]
>Jeśli występuje problem z przekazywaniem plików, upewnij się, że sieć lokalna nie blokuje https://upload.xboxlive.com usługi używanej przez centrum partnerskie.

#### <a name="store-logos"></a>Logo sklepu

Udostępniaj pliki PNG logo oferty w postaci dwóch pikseli:
- **Mały** (48 x 48)
- **Duże** (216 x 216)

Oba logo są wymagane i są używane w różnych miejscach na liście w portalu Marketplace.

#### <a name="screenshots"></a>Zrzuty ekranu

Dodaj co najmniej jeden i maksymalnie pięć zrzutów ekranu, które pokazują, jak działa Twoja oferta. Każdy z nich musi mieć 1280 x 720 pikseli w rozmiarze i formacie PNG.

#### <a name="videos-optional"></a>Wideo (opcjonalnie)

Dodaj do pięciu filmów wideo, które przedstawiają Twoją ofertę. Wprowadź nazwę filmu wideo, jego adres sieci Web i obraz miniatury PNG wideo o rozmiarze 1280 x 720 pikseli.

#### <a name="additional-marketplace-listing-resources"></a>Dodatkowe zasoby dotyczące wyświetlania w portalu Marketplace

Aby dowiedzieć się więcej na temat tworzenia list ofert, zobacz [najlepsze rozwiązania dotyczące oferty](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices).

## <a name="technical-configuration"></a>Konfiguracja techniczna

Promuj swoją aplikację w usłudze Power BI Service w środowisku produkcyjnym i podaj link do Instalatora aplikacji Power BI, który umożliwia klientom zainstalowanie aplikacji. Aby uzyskać więcej informacji, zobacz [publikowanie aplikacji za pomocą pulpitów nawigacyjnych i raportów w programie Power BI](https://docs.microsoft.com/power-bi/service-create-distribute-apps).

## <a name="supplemental-content"></a>Dodatkowa zawartość

Podaj dodatkowe informacje na temat oferty, aby pomóc nam w jej sprawdzeniu. Te informacje nie są widoczne dla klientów ani opublikowane w portalu Marketplace.

### <a name="validation-assets"></a>Zasoby weryfikacji

Opcjonalnie możesz dodać instrukcje (do 3 000 znaków), aby pomóc zespołowi ds. weryfikacji firmy Microsoft w konfigurowaniu, łączeniu i testowaniu aplikacji. Uwzględnij typowe ustawienia konfiguracji, konta, parametry lub inne informacje, których można użyć do testowania opcji Połącz dane. Te informacje są widoczne tylko dla zespołu weryfikacji i są używane tylko do celów weryfikacji.

## <a name="review-and-publish"></a>Przejrzyj i Opublikuj

Po zakończeniu wszystkich wymaganych sekcji oferty możesz przesłać swoją ofertę do przeglądu i publikacji.

W prawym górnym rogu portalu wybierz pozycję **Przejrzyj i Opublikuj**.

Na stronie Przegląd możesz:

- Zobacz stan ukończenia dla każdej sekcji oferty. Nie można publikować, dopóki wszystkie sekcje oferty nie zostaną oznaczone jako ukończone.
  - **Nierozpoczęte** — sekcja nie została uruchomiona i musi zostać ukończona.
  - **Niekompletne** — sekcja zawiera błędy, które muszą zostać naprawione lub wymaga podania dodatkowych informacji. Zapoznaj się z sekcjami wcześniej w tym dokumencie, aby uzyskać wskazówki.
  - **Ukończono** — sekcja zawiera wszystkie wymagane dane i nie ma błędów. Aby można było przesłać ofertę, wszystkie sekcje oferty muszą zostać ukończone.
- Podaj instrukcje testowania dla zespołu certyfikacji, aby upewnić się, że aplikacja jest poprawnie testowana. Ponadto Podaj wszelkie dodatkowe uwagi pomocne przy zrozumieniu oferty.

Aby przesłać ofertę do publikacji, wybierz pozycję **Publikuj**.

Wyślemy Ci wiadomość e-mail z prośbą o poinformowanie o udostępnieniu wersji zapoznawczej oferty do przejrzenia i zatwierdzenia. Aby opublikować ofertę dla publiczną (lub, jeśli publiczną, prywatną), przejdź do Centrum partnerskiego i wybierz pozycję **Przejdź na żywo**.
