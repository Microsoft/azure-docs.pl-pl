---
title: Tworzenie oferty aplikacji Power BI w Microsoft AppSource
description: Dowiedz się, jak utworzyć i opublikować ofertę Power BI aplikacji Microsoft AppSource.
author: navits09
ms.author: navits
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 07/22/2020
ms.openlocfilehash: bff20468e8185073f5c192c1e115bc405dd089eb
ms.sourcegitcommit: b6267bc931ef1a4bd33d67ba76895e14b9d0c661
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/19/2020
ms.locfileid: "97693610"
---
# <a name="create-a-power-bi-app-offer"></a>Tworzenie oferty aplikacji Power BI

W tym artykule opisano sposób tworzenia i publikowania oferty aplikacji Power BI w [Microsoft AppSource](https://appsource.microsoft.com/).

Przed rozpoczęciem [Utwórz komercyjne konto witryny Marketplace w centrum partnerskim](create-account.md) , jeśli jeszcze tego nie zrobiono. Upewnij się, że Twoje konto jest zarejestrowane w komercyjnym programie Marketplace.

## <a name="create-a-new-offer"></a>Tworzenie nowej oferty

1. Zaloguj się do [Centrum partnerskiego](https://partner.microsoft.com/dashboard/home).
2. W menu po lewej stronie wybierz pozycję **komercyjne Omówienie witryny Marketplace**  >  .
3. Na stronie Przegląd wybierz pozycję **+ Nowa oferta**  >  **Power BI aplikacji usługi**.

   ![Ilustruje menu nawigacji po lewej stronie.](./media/new-offer-power-bi-app.png)

> [!NOTE]
> Po opublikowaniu oferty zmiany wprowadzone w centrum partnerskim będą widoczne w sklepach online dopiero po ponownym opublikowaniu oferty. Pamiętaj, aby zawsze ponownie publikować po wprowadzeniu zmian.

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

- Jeśli oferta jest ofertą typu wersja robocza, Usuń wersję roboczą
- Jeśli oferta jest aktywna [, Zatrzymaj sprzedawanie oferty](update-existing-offer.md#stop-selling-an-offer-or-plan)
- Jeśli oferta jest dostępna w wersji zapoznawczej — [Przejdź na żywo](../review-publish-offer.md#previewing-and-approving-your-offer)
- Jeśli nie ukończono wylogowania wydawcy — [Anuluj publikowanie.](../review-publish-offer.md#cancel-publishing)

## <a name="offer-setup"></a>Konfiguracja oferty

### <a name="customer-leads"></a>Potencjalni klienci

Po opublikowaniu oferty w portalu Marketplace przy użyciu Centrum partnerskiego należy połączyć ją z systemem zarządzania relacjami z klientami (CRM). Pozwala to na otrzymywanie informacji kontaktowych klienta, gdy tylko ktoś wyrazi zainteresowanie lub skorzysta z produktu.

1. Wybierz miejsce docelowe potencjalnych klientów, do którego chcesz wysyłać potencjalnych klientów. Centrum partnerskie obsługuje następujące systemy CRM:

    - [Dynamics 365](commercial-marketplace-lead-management-instructions-dynamics.md) dla zaangażowania klienta
    - [Marketo](commercial-marketplace-lead-management-instructions-marketo.md)
    - [Salesforce](commercial-marketplace-lead-management-instructions-salesforce.md)

    > [!NOTE]
    > Jeśli Twój system CRM nie znajduje się na tej liście, użyj [usługi Azure Table](commercial-marketplace-lead-management-instructions-azure-table.md) lub i [punktu końcowego HTTPS](commercial-marketplace-lead-management-instructions-https.md) do przechowywania danych potencjalnych klientów. Następnie wyeksportuj dane do systemu CRM.

2. Połącz ofertę z miejscem docelowym potencjalnego klienta podczas publikowania w centrum partnerskim.
3. Upewnij się, że połączenie z miejscem docelowym potencjalnego klienta jest skonfigurowane prawidłowo. Po opublikowaniu go w centrum partnerskim sprawdzimy połączenie i wyślesz potencjalnego klienta. Podczas wyświetlania podglądu oferty przed jej rozpoczęciem możesz również przetestować połączenie z liderem, próbując zakupić ofertę samodzielnie w środowisku wersji zapoznawczej.
4. Upewnij się, że połączenie z miejscem docelowym potencjalnego klienta zostało zaktualizowane, więc nie utracisz żadnych potencjalnych klientów.

Poniżej przedstawiono kilka dodatkowych zasobów zarządzania potencjalnym liderem:

- [Potencjalni klienci z oferty na platformie handlowej](commercial-marketplace-get-customer-leads.md)
- [Często zadawane pytania dotyczące zarządzania potencjalnymi klientami](../lead-management-faq.md#common-questions-about-lead-management)
- [Rozwiązywanie problemów z błędami konfiguracji potencjalnego klienta](../lead-management-faq.md#publishing-config-errors)
- [Omówienie zarządzania potencjalnymi klientami](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf) PDF (Upewnij się, że blokowanie wyskakujących okienek jest wyłączone).

Wybierz pozycję **Zapisz wersję roboczą** przed kontynuowaniem.

## <a name="properties"></a>Właściwości

Ta strona umożliwia definiowanie kategorii i branż używanych do grupowania oferty w witrynie Marketplace, wersji aplikacji oraz umów prawnych, które obsługują Twoją ofertę.

### <a name="category"></a>Kategoria

Wybierz kategorie i podkategorie, aby umieścić ofertę w odpowiednich obszarach wyszukiwania w portalu Marketplace. Pamiętaj, aby opisać, w jaki sposób oferta obsługuje te kategorie w opisie oferty. Wybierz pozycję:

- Co najmniej jeden i maksymalnie dwie kategorie, w tym podstawowa i pomocnicza Kategoria (opcjonalnie).
- Do dwóch podkategorii dla każdej kategorii podstawowej i/lub pomocniczej. Jeśli żadna Podkategoria nie ma zastosowania do oferty, wybierz pozycję **nie dotyczy**.

Zapoznaj się z pełną listą kategorii i podkategorii w artykule [Oferta z najlepszymi rozwiązaniami](../gtm-offer-listing-best-practices.md).

### <a name="industry"></a>Branża

[!INCLUDE [Industry Taxonomy](./includes/industry-taxonomy.md)]

### <a name="legal"></a>Informacje prawne

#### <a name="terms-and-conditions"></a>Warunki i postanowienia

Aby zapewnić własne niestandardowe warunki i postanowienia, wprowadź do 10 000 znaków w polu **warunki i** postanowienia. Aby wypróbować ofertę, klienci muszą zaakceptować te warunki.

Wybierz pozycję **Zapisz wersję roboczą** przed przejściem do następnej sekcji, na liście oferty.

## <a name="offer-listing"></a>Lista oferty

W tym miejscu zdefiniujesz szczegóły oferty, które są wyświetlane w portalu Marketplace. Obejmuje to nazwę oferty, opis, obrazy i tak dalej.

### <a name="language"></a>Język

Wybierz język, w którym zostanie wyświetlona oferta. Obecnie tylko w **języku angielskim (Stany Zjednoczone)** jest jedyną dostępną opcją.

Zdefiniuj szczegóły witryny Marketplace (takie jak nazwa oferty, opis i obrazy) dla każdego języka/rynku. Wybierz nazwę języka/rynku, aby podać te informacje.

> [!NOTE]
> Szczegóły oferty nie muszą znajdować się w języku angielskim, jeśli opis oferty zaczyna się od frazy "Ta aplikacja jest dostępna tylko w języku innym niż angielski]". Istnieje również możliwość udostępnienia przydatnego linku do oferowania zawartości w języku innym niż ten, który jest używany na liście ofert.

Oto przykład sposobu wyświetlania informacji o ofercie w Microsoft AppSource (wszelkie wymienione ceny są przeznaczone wyłącznie do celów i nie są przeznaczone do odzwierciedlenia rzeczywistych kosztów):

:::image type="content" source="media/example-power-bi-app.png" alt-text="Ilustruje, w jaki sposób ta oferta pojawia się w Microsoft AppSource.":::

#### <a name="call-out-descriptions"></a>Opisy połączeń

1. Logo
2. Produkty
3. Kategorie
4. Branże
5. Adres pomocy technicznej (link)
6. Warunki użytkowania
7. Zasady ochrony prywatności
8. Nazwa oferty
9. Podsumowanie
10. Opis
11. Zrzuty ekranu/wideo

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

[!INCLUDE [logo tips](../includes/graphics-suggestions.md)]

>[!NOTE]
>Jeśli występuje problem z przekazywaniem plików, upewnij się, że sieć lokalna nie blokuje `https://upload.xboxlive.com` usługi używanej przez centrum partnerskie.

#### <a name="store-logos"></a>Logo sklepu

Podaj plik PNG dla logo o **dużym** rozmiarze. Centrum partnerskie będzie używać tego do tworzenia **małych** logo. Opcjonalnie można zastąpić ten inny obraz później.

- **Duże** (od 216 x 216 do 350 x 350 px, wymagane)
- **Mały** (48 x 48 pikseli, opcjonalnie)

Te logo są używane w różnych miejscach na liście:

[!INCLUDE [logos-appsource-only](../includes/logos-appsource-only.md)]

[!INCLUDE [logo tips](../includes/graphics-suggestions.md)]

#### <a name="screenshots"></a>Zrzuty ekranu

Dodaj co najmniej jeden i maksymalnie pięć zrzutów ekranu, które pokazują, jak działa Twoja oferta. Każdy z nich musi mieć 1280 x 720 pikseli w rozmiarze i formacie PNG.

#### <a name="videos-optional"></a>Wideo (opcjonalnie)

Dodaj do pięciu filmów wideo, które przedstawiają Twoją ofertę. Wprowadź nazwę filmu wideo, jego adres sieci Web i obraz miniatury PNG wideo o rozmiarze 1280 x 720 pikseli.

#### <a name="additional-marketplace-listing-resources"></a>Dodatkowe zasoby dotyczące wyświetlania w portalu Marketplace

Aby dowiedzieć się więcej na temat tworzenia list ofert, zobacz [najlepsze rozwiązania dotyczące oferty](../gtm-offer-listing-best-practices.md).

## <a name="technical-configuration"></a>Konfiguracja techniczna

Promuj swoją aplikację w usłudze Power BI Service w środowisku produkcyjnym i podaj link do Instalatora aplikacji Power BI, który umożliwia klientom zainstalowanie aplikacji. Aby uzyskać więcej informacji, zobacz [publikowanie aplikacji za pomocą pulpitów nawigacyjnych i raportów w programie Power BI](/power-bi/service-create-distribute-apps).

## <a name="supplemental-content"></a>Dodatkowa zawartość

Podaj dodatkowe informacje na temat oferty, aby pomóc nam w jej sprawdzeniu. Te informacje nie są widoczne dla klientów ani opublikowane w portalu Marketplace.

### <a name="validation-assets"></a>Zasoby weryfikacji

Opcjonalnie możesz dodać instrukcje (do 3 000 znaków), aby pomóc zespołowi ds. weryfikacji firmy Microsoft w konfigurowaniu, łączeniu i testowaniu aplikacji. Uwzględnij typowe ustawienia konfiguracji, konta, parametry lub inne informacje, których można użyć do testowania opcji Połącz dane. Te informacje są widoczne tylko dla zespołu weryfikacji i są używane tylko do celów weryfikacji.

## <a name="review-and-publish"></a>Przeglądanie i publikowanie

Po zakończeniu wszystkich wymaganych sekcji oferty możesz przesłać swoją ofertę do przeglądu i publikacji.

W prawym górnym rogu portalu wybierz pozycję **Przejrzyj i Opublikuj**.

Na stronie Przegląd możesz:

- Zobacz stan ukończenia dla każdej sekcji oferty. Nie można publikować, dopóki wszystkie sekcje oferty nie zostaną oznaczone jako ukończone.
  - **Nierozpoczęte** — sekcja nie została uruchomiona i musi zostać ukończona.
  - **Niekompletne** — sekcja zawiera błędy, które muszą zostać naprawione lub wymaga podania dodatkowych informacji. Zapoznaj się z sekcjami wcześniej w tym dokumencie, aby uzyskać wskazówki.
  - **Ukończono** — sekcja zawiera wszystkie wymagane dane i nie ma błędów. Aby można było przesłać ofertę, wszystkie sekcje oferty muszą zostać ukończone.
- Podaj instrukcje testowania dla zespołu certyfikacji, aby upewnić się, że aplikacja jest poprawnie testowana. Ponadto Podaj wszelkie dodatkowe uwagi pomocne przy zrozumieniu oferty.

Aby przesłać ofertę do publikacji, wybierz pozycję **Publikuj**.

Wyślemy Ci wiadomość e-mail z prośbą o poinformowanie o udostępnieniu wersji zapoznawczej oferty do przejrzenia i zatwierdzenia. Aby opublikować publiczną ofertę, przejdź do Centrum partnerskiego i wybierz pozycję **Przejdź na żywo**.
