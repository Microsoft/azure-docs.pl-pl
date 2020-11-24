---
title: Tworzenie oferty kontenera platformy Azure — Azure Marketplace
description: Dowiedz się, jak utworzyć i opublikować ofertę kontenera w witrynie Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: keferna
ms.author: keferna
ms.date: 06/17/2020
ms.openlocfilehash: 76211e2aaf27fd28500bb539c94fa409b239e785
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95759049"
---
# <a name="create-an-azure-container-offer-in-azure-marketplace"></a>Tworzenie oferty kontenera platformy Azure w witrynie Azure Marketplace

W tym artykule opisano sposób tworzenia i publikowania oferty kontenera dla portalu Azure Marketplace. Przed rozpoczęciem [Utwórz komercyjne konto witryny Marketplace w centrum partnerskim](./partner-center-portal/create-account.md) , jeśli jeszcze tego nie zrobiono. Upewnij się, że Twoje konto jest zarejestrowane w komercyjnym programie Marketplace.

## <a name="create-a-new-offer"></a>Tworzenie nowej oferty

1. Zaloguj się do [Centrum partnerskiego](https://partner.microsoft.com/dashboard/home).

2. W menu po lewej stronie wybierz pozycję **komercyjne Omówienie witryny Marketplace**  >  **Overview**.

3. Na stronie Przegląd wybierz pozycję **+ nowy oferta**  >  **platformy Azure**.

   ![Ilustruje menu nawigacji po lewej stronie.](./partner-center-portal/media/new-offer-azure-container.png)

> [!TIP]
> Po opublikowaniu oferty zmiany wprowadzone w centrum partnerskim będą widoczne w sklepach online dopiero po ponownym opublikowaniu oferty. Pamiętaj, aby zawsze ponownie publikować po wprowadzeniu zmian.

### <a name="offer-id-and-alias"></a>Identyfikator oferty i alias

Wprowadź **Identyfikator oferty**. To jest unikatowy identyfikator dla każdej oferty na Twoim koncie.

- Ten identyfikator jest widoczny dla klientów w adresie sieci Web dla ofert i szablonów Azure Resource Manager, jeśli ma zastosowanie.
- Użyj tylko małych liter i cyfr. Może zawierać łączniki i podkreślenia, ale nie spacje i jest ograniczone do 50 znaków. Jeśli na przykład wprowadzisz polecenie **test-Offer-1**, adres sieci Web oferty będzie wynosić `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` .
- Nie można zmienić identyfikatora oferty po wybraniu pozycji **Utwórz**.

Wprowadź **alias oferty**. Jest to nazwa używana dla oferty w centrum partnerskim.

- Ta nazwa nie jest używana w portalu Marketplace i różni się od nazwy oferty i innych wartości widocznych dla klientów.
- Tego nie można zmienić po wybraniu opcji **Utwórz**.

Wybierz pozycję **Utwórz** , aby wygenerować ofertę i kontynuować.

## <a name="offer-overview"></a>Przegląd oferty

Na stronie **Przegląd oferty** przedstawiono wizualną reprezentację kroków wymaganych do opublikowania oferty (zarówno ukończonej, jak i nadchodzącej) oraz czasu, w którym należy wykonać poszczególne kroki.

Ta strona zawiera różne linki w zależności od bieżącego stanu oferty. Na przykład:

- Jeśli oferta jest ofertą typu wersja robocza, Usuń wersję roboczą
- Jeśli oferta jest aktywna [, Zatrzymaj sprzedawanie oferty](./partner-center-portal/update-existing-offer.md#stop-selling-an-offer-or-plan)
- Jeśli oferta jest dostępna w wersji zapoznawczej — [Przejdź na żywo](review-publish-offer.md#previewing-and-approving-your-offer)
- Jeśli nie ukończono wylogowania wydawcy — [Anuluj publikowanie.](review-publish-offer.md#cancel-publishing)

## <a name="offer-setup"></a>Konfiguracja oferty

Wykonaj następujące kroki, aby skonfigurować ofertę.

### <a name="customer-leads--optional"></a>Potencjalni klienci — opcjonalne

Po opublikowaniu oferty na komercyjnym rynku w centrum partnerskim można połączyć ją z systemem zarządzania relacjami z klientami (CRM). Pozwala to na otrzymywanie informacji kontaktowych klienta, gdy tylko ktoś wyrazi zainteresowanie lub skorzysta z produktu.

1. **Wybierz miejsce docelowe potencjalnego klienta, na którym chcesz wysłać klientów**. Centrum partnerskie obsługuje następujące systemy CRM:

   - [Dynamics 365](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md) dla zaangażowania klienta
   - [Marketo](./partner-center-portal/commercial-marketplace-lead-management-instructions-marketo.md)
   - [Salesforce](./partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce.md)

   > [!NOTE]
   > Jeśli system CRM nie jest wymieniony powyżej, użyj [tabeli platformy Azure](./partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md) lub [punktu końcowego HTTPS](./partner-center-portal/commercial-marketplace-lead-management-instructions-https.md) do przechowywania danych potencjalnych klientów, a następnie wyeksportuj dane do systemu CRM.

2. Połącz ofertę z miejscem docelowym potencjalnego klienta podczas publikowania w centrum partnerskim.
3. Upewnij się, że połączenie z miejscem docelowym potencjalnego klienta zostało skonfigurowane prawidłowo. Po opublikowaniu go w centrum partnerskim sprawdzimy połączenie i wyślesz potencjalnego klienta. Podczas wyświetlania podglądu oferty przed jej rozpoczęciem możesz również przetestować połączenie z liderem, próbując zakupić ofertę samodzielnie w środowisku wersji zapoznawczej.
4. Upewnij się, że połączenie z miejscem docelowym potencjalnego klienta zostało zaktualizowane, więc nie utracisz żadnych potencjalnych klientów.

Poniżej przedstawiono kilka dodatkowych zasobów zarządzania potencjalnym liderem:

- [Potencjalni klienci z oferty na platformie handlowej](./partner-center-portal/commercial-marketplace-get-customer-leads.md)
- [Często zadawane pytania dotyczące zarządzania potencjalnymi klientami](lead-management-faq.md#common-questions-about-lead-management)
- [Rozwiązywanie problemów z błędami konfiguracji potencjalnego klienta](lead-management-faq.md#publishing-config-errors)
- [Omówienie zarządzania potencjalnymi klientami](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf) PDF (Upewnij się, że blokowanie wyskakujących okienek jest wyłączone).

Wybierz pozycję **Zapisz wersję roboczą** przed kontynuowaniem.

### <a name="properties"></a>Właściwości

Ta strona umożliwia definiowanie kategorii używanych do grupowania oferty w witrynie Marketplace oraz umów prawnych, które obsługują Twoją ofertę.

#### <a name="category"></a>Kategoria

Wybierz kategorie i podkategorie, aby umieścić ofertę w odpowiednich obszarach wyszukiwania w portalu Marketplace. Pamiętaj, aby opisać, w jaki sposób oferta obsługuje te kategorie w opisie oferty. Wybierz pozycję:

- Co najmniej jeden i maksymalnie dwie kategorie, w tym podstawowa i pomocnicza Kategoria (opcjonalnie).
- Do dwóch podkategorii dla każdej kategorii podstawowej i/lub pomocniczej. Jeśli żadna Podkategoria nie ma zastosowania do oferty, wybierz pozycję **nie dotyczy**.

Zapoznaj się z pełną listą kategorii i podkategorii w artykule [Oferta z najlepszymi rozwiązaniami](gtm-offer-listing-best-practices.md). Kontenery są zawsze wyświetlane w obszarze **kontenery** , a następnie Kategoria **obrazy kontenerów** .

#### <a name="legal"></a>Informacje prawne

Musisz podać warunki i postanowienia oferty. Dostępne są dwie opcje:

- Skorzystaj z standardowej umowy dotyczącej komercyjnego portalu Microsoft Marketplace.
- Podaj własne warunki i postanowienia.

#### <a name="standard-contract-for-the-microsoft-commercial-marketplace"></a>Standardowa umowa dla komercyjnego portalu Microsoft Marketplace

Oferujemy standardowy szablon kontraktu, aby ułatwić obsługę transakcji w portalu komercyjnym. Możesz wybrać opcję oferowania rozwiązania w ramach standardowej umowy, którą klienci muszą tylko raz sprawdzić i zaakceptować. Jest to dobra opcja, jeśli nie chcesz tworzyć niestandardowych warunków i postanowień.

Aby dowiedzieć się więcej na temat standardowej umowy, zobacz temat [Standardowy kontrakt dla komercyjnego portalu Microsoft Marketplace](standard-contract.md). Możesz również pobrać plik PDF [kontraktu standardowego](https://go.microsoft.com/fwlink/?linkid=2041178) (Upewnij się, że blokowanie wyskakujących okienek jest wyłączone).

Aby skorzystać z kontraktu standardowego, wybierz pozycję * * Użyj kontraktu standardowego dla komercyjnego rynku firmy Microsoft] (... /standard-contract.md)

> [!NOTE]
> Po opublikowaniu oferty przy użyciu standardowej umowy dotyczącej komercyjnej witryny Marketplace firmy Microsoft nie można używać własnych niestandardowych warunków i postanowień. Oferuj swoje rozwiązanie w ramach standardowej umowy lub w ramach własnych warunków i postanowień.

:::image type="content" source="./partner-center-portal/media/azure-create-container-offer-images/azure-create-1-standard-contract.png" alt-text="Ilustruje pole wyboru Użyj standardowej umowy dotyczącej komercyjnej witryny Marketplace firmy Microsoft.":::

##### <a name="your-own-terms-and-conditions"></a>Własne warunki i postanowienia

Aby zapewnić własne niestandardowe warunki i postanowienia, wprowadź je w polu **warunki i postanowienia** . W tym polu można wprowadzić nieograniczoną liczbę znaków w tekście. Aby wypróbować ofertę, klienci muszą zaakceptować te warunki.

Wybierz pozycję **Zapisz wersję roboczą** przed przejściem do następnej sekcji, na liście oferty.

## <a name="offer-listing"></a>Lista oferty

Ta strona umożliwia zdefiniowanie szczegółów oferty, które są wyświetlane w portalu komercyjnym. Obejmuje to nazwę, opis i obrazy oferty.

> [!NOTE]
> Szczegóły oferty nie muszą znajdować się w języku angielskim, jeśli opis oferty zaczyna się od frazy "Ta aplikacja jest dostępna tylko w języku innym niż angielski]". Istnieje również możliwość udostępnienia przydatnego linku do oferowania zawartości w języku innym niż ten, który jest używany w szczegółach dotyczących oferty.

### <a name="name"></a>Nazwa

Wprowadzona nazwa zostanie wyświetlona jako tytuł oferty. To pole jest wstępnie wypełnione tekstem wprowadzonym w polu **alias oferty** podczas tworzenia oferty. Tę nazwę można później zmienić.

Nazwa:

- Mogą być znakami towarowymi (i może zawierać symbole towarowe i praw autorskich).
- Długość nie może przekraczać 50 znaków.
- Nie może zawierać znaków emoji.

### <a name="search-results-summary"></a>Podsumowanie wyników wyszukiwania

Krótki opis oferty. Może to być maksymalnie 100 znaków i jest używana w wynikach wyszukiwania w portalu Marketplace.

### <a name="long-summary"></a>Długie podsumowanie

Bardziej szczegółowy opis oferty. Może to być maksymalnie 256 znaków i jest używana w wynikach wyszukiwania w portalu Marketplace.

### <a name="description"></a>Opis

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Long description-3](./includes/long-description-3.md)]

#### <a name="privacy-policy-link"></a>Link zasad ochrony prywatności

Wprowadź adres internetowy zasad zachowania poufności informacji organizacji. Użytkownik jest odpowiedzialny za zapewnienie zgodności oferty z przepisami i przepisami dotyczącymi ochrony prywatności. Użytkownik jest odpowiedzialny za publikowanie prawidłowych zasad zachowania poufności informacji w witrynie sieci Web.

#### <a name="useful-links"></a>Przydatne łącza

Podaj uzupełniające dokumenty online dotyczące Twojej oferty. Możesz dodać maksymalnie 25 linków. Aby dodać łącze, wybierz pozycję **+ Dodaj łącze** , a następnie wykonaj następujące pola:

- **Title** — klienci będą widzieć ten temat na stronie szczegółów swojej oferty.
- **Link (adres URL)** — wprowadź link dla klientów, aby wyświetlić dokument w trybie online. Link musi rozpoczynać się od http://lub https://.

### <a name="contact-information"></a>Informacje kontaktowe

Musisz podać nazwę, adres e-mail i numer telefonu dla **kontaktu z pomocą techniczną** i **kontaktu inżynieryjnego**. Te informacje nie są widoczne dla klientów, ale są dostępne dla firmy Microsoft. Mogą być również udostępniane partnerom dostawcy rozwiązań w chmurze (CSP).

- Kontakt z pomocą techniczną (wymagany): w przypadku ogólnych pytań dotyczących pomocy technicznej.
- Kontakt inżynieryjny (wymagany): w przypadku pytań technicznych i problemów z certyfikacją.
- Kontakt programu CSP (opcjonalnie): w przypadku pytań odsprzedawców związanych z programem CSP.

W sekcji **skontaktuj się z pomocą techniczną** Podaj **witrynę sieci Web pomocy technicznej** , w której partnerzy mogą znaleźć pomoc techniczną dotyczącą oferty w zależności od tego, czy oferta jest dostępna na platformie Azure, Azure Government czy w obu tych przypadkach.

W sekcji **kontakt programu z programem CSP** Podaj link (**materiały marketingowe programu CSP**), w którym partnerzy usług kryptograficznych mogą znaleźć materiały marketingowe oferty.

#### <a name="additional-marketplace-listing-resources"></a>Dodatkowe zasoby dotyczące wyświetlania w portalu Marketplace

Aby dowiedzieć się więcej na temat tworzenia list ofert, zobacz [najlepsze rozwiązania dotyczące oferty](gtm-offer-listing-best-practices.md)

### <a name="marketplace-images"></a>Obrazy z witryny Marketplace

Podaj logo i obrazy, które mają być używane z ofertą. Wszystkie obrazy muszą mieć format PNG. Rozmyte obrazy zostaną odrzucone.

[!INCLUDE [logo tips](./includes/graphics-suggestions.md)]

>[!Note]
>Jeśli występuje problem z przekazywaniem plików, upewnij się, że sieć lokalna nie blokuje https://upload.xboxlive.com usługi używanej przez centrum partnerskie.

#### <a name="store-logos"></a>Logo sklepu

Podaj plik PNG dla logo o **dużym** rozmiarze. Centrum partnerskie będzie używać tego do tworzenia **małych** i **średnich** logo. Opcjonalnie można zastąpić je innymi obrazami później.

- **Duże** (od 216 x 216 do 350 x 350 px, wymagane)
- **Średnia** (90 x 90 pikseli, opcjonalnie)
- **Mały** (48 x 48 pikseli, opcjonalnie)

Te logo są używane w różnych miejscach na liście:

[!INCLUDE [logos-azure-marketplace-only](./includes/logos-azure-marketplace-only.md)]

[!INCLUDE [Logo tips](./includes/graphics-suggestions.md)]

#### <a name="screenshots-optional"></a>Zrzuty ekranu (opcjonalnie)

Dodaj maksymalnie pięć zrzutów ekranu, które pokazują, jak działa Twoja oferta. Każdy z nich musi mieć 1280 x 720 pikseli w rozmiarze i formacie PNG.

#### <a name="videos-optional"></a>Wideo (opcjonalnie)

Dodaj do pięciu filmów wideo, które przedstawiają Twoją ofertę. Wprowadź nazwę filmu wideo, jego adres internetowy oraz miniaturę obrazu PNG wideo o rozmiarze 1280 x 720 pikseli.

#### <a name="offer-examples"></a>Przykłady dotyczące ofert

W poniższych przykładach pokazano, jak pola list oferty pojawiają się w różnych miejscach oferty.

Spowoduje to wyświetlenie strony z **listą ofert** w witrynie Azure Marketplace:

:::image type="content" source="./partner-center-portal/media/azure-create-container-offer-images/azure-create-6-offer-listing-mkt-plc.png" alt-text="Przedstawia stronę aukcji oferty w witrynie Azure Marketplace." :::

Spowoduje to wyświetlenie wyników wyszukiwania w witrynie Azure Marketplace:

:::image type="content" source="./partner-center-portal/media/azure-create-container-offer-images/azure-create-7-search-results-mkt-plc.png" alt-text="Ilustruje wyniki wyszukiwania w witrynie Azure Marketplace.":::

Spowoduje to wyświetlenie strony z **listą ofert** w Azure Portal:

:::image type="content" source="./partner-center-portal/media/azure-create-container-offer-images/azure-create-8-offer-listing-portal.png" alt-text="Ilustruje stronę z listą ofert w Azure Portal.":::

Spowoduje to wyświetlenie wyników wyszukiwania w Azure Portal:

:::image type="content" source="./partner-center-portal/media/azure-create-container-offer-images/azure-create-9-search-results-portal.png" alt-text="Ilustruje wyniki wyszukiwania w Azure Portal.":::

## <a name="preview"></a>Wersja zapoznawcza

Na karcie Podgląd możesz wybrać ograniczonych **odbiorców w wersji zapoznawczej** , aby sprawdzić poprawność oferty przed opublikowaniem jej na żywo.

> [!IMPORTANT]
> Po wyświetleniu oferty w **wersji zapoznawczej** musisz wybrać pozycję **Przejdź na żywo** , aby opublikować ofertę publicznie.

Określ odbiorców w wersji zapoznawczej przy użyciu identyfikatorów GUID identyfikatora subskrypcji platformy Azure, a także opcjonalny opis dla każdej z nich. Żadne z tych pól nie może być widoczne dla klientów.

> [!NOTE]
> Identyfikator subskrypcji platformy Azure można znaleźć na stronie Subskrypcje w Azure Portal.

Dodaj co najmniej jeden identyfikator subskrypcji platformy Azure — pojedynczo (do 10) lub przekazując plik CSV (do 100). Dodając te identyfikatory subskrypcji, możesz określić, kto może wyświetlać podgląd oferty przed opublikowaniem jej na żywo. Jeśli Twoja oferta jest już na żywo, możesz wybrać użytkowników wersji zapoznawczej, aby przetestować zmiany lub aktualizacje oferty.

Wybierz pozycję **Zapisz wersję roboczą** przed kontynuowaniem.

## <a name="plan-overview"></a>Przegląd planu

Na tej karcie można podać różne opcje planu w ramach tej samej oferty. Plany (dawniej nazywane jednostkami SKU) mogą różnić się od dostępnych chmur, takich jak chmury globalne, chmury rządowe i obraz, do którego odwołuje się plan. Aby wystawić ofertę w komercyjnej witrynie Marketplace, należy skonfigurować co najmniej jeden plan.

Możesz utworzyć do 100 planów dla każdej oferty: maksymalnie 45 z nich mogą być prywatne. Dowiedz się więcej o planach prywatnych w [ofertach prywatnych w komercyjnym portalu Microsoft Marketplace](private-offers.md).

Po utworzeniu planów na karcie **Przegląd planu** jest wyświetlana wartość:

- Nazwy planów
- Model cen
- Regiony platformy Azure (globalne lub rządowe)
- Bieżący stan publikowania
- Wszystkie dostępne akcje

Akcje dostępne w omówieniu planu różnią się w zależności od bieżącego stanu planu. Obejmują one:

- **Usuń wersję roboczą** — Jeśli plan jest stanem wersja robocza.
- **Zatrzymaj sprzedawanie planu** — Jeśli stan planu jest opublikowany na żywo.

### <a name="create-new-plan"></a>Utwórz nowy plan

Wybierz pozycję **Utwórz nowy plan**. Zostanie wyświetlone okno dialogowe **nowy plan** .

W polu **identyfikator planu** Utwórz unikatowy identyfikator planu dla każdego planu w tej ofercie. Ten identyfikator będzie widoczny dla klientów w adresie sieci Web produktu. Używaj tylko małych liter i cyfr, kresek lub podkreśleń i maksymalnie 50 znaków.

> [!NOTE]
> Nie można zmienić identyfikatora planu po wybraniu opcji **Utwórz**.

W polu **Nazwa planu** wprowadź nazwę dla tego planu. Klienci widzą tę nazwę podczas wybierania planu do wyboru w ramach oferty. Utwórz unikatową nazwę dla każdego planu w tej ofercie. Można na przykład użyć nazwy oferty **systemu Windows Server** z planami **Windows Server 2016** i **Windows Server 2019**.

### <a name="plan-setup"></a>Konfigurowanie planu

Na tej karcie można wybrać chmurę, w której plan jest dostępny. Odpowiedzi na tej karcie mają wpływ na to, które pola są wyświetlane na innych kartach.

#### <a name="azure-regions"></a>Regiony świadczenia usługi Azure

Wszystkie plany dla ofert kontenera platformy Azure są automatycznie udostępniane na **platformie Azure Global**.  Twój plan może być używany przez klientów we wszystkich globalnych regionach platformy Azure, które korzystają z komercyjnej witryny Marketplace. Aby uzyskać szczegółowe informacje, zobacz [dostępność geograficzna i obsługa waluty](marketplace-geo-availability-currencies.md).

Wybierz opcję [Azure Government](/azure/azure-government/documentation-government-welcome.md) , aby rozwiązanie było wyświetlane w tym miejscu. Jest to chmura społecznościowa dla instytucji rządowych, której dostęp jest kontrolowany przez klientów z federalnych, stanowych i lokalnych lub plemienneych instytucji rządowych, a także partnerów uprawnionych do ich używania. Wydawca jest odpowiedzialny za wszelkie kontrole zgodności, miary zabezpieczeń i najlepsze rozwiązania dla tej społeczności w chmurze. Azure Government używa fizycznie wyizolowanych centrów danych i sieci (znajdujących się tylko w Stanach Zjednoczonych). Przed [opublikowaniem](/azure/azure-government/documentation-government-manage-marketplace-partners.md) w celu Azure Government Przetestuj i Potwierdź swoje rozwiązanie w tym obszarze, ponieważ wyniki mogą się różnić. Aby utworzyć i przetestować rozwiązanie, zażądaj konta próbnego od [Microsoft Azure Government wersji próbnej](https://azure.microsoft.com/global-infrastructure/government/request/).

> [!NOTE]
> Po opublikowaniu planu i udostępnieniu go w określonym regionie nie można usunąć tego regionu.

#### <a name="azure-government-certifications"></a>Certyfikaty Azure Government

Tę opcję można zobaczyć tylko w przypadku wybrania **Azure Government** w obszarze **regiony platformy Azure**.

Usługi Azure Government Services obsługują dane, które podlegają określonym przepisom i wymogom rządowym. Na przykład FedRAMP, NIST 800,171 (DIB), ITAR, IRS 1075, DoD P4 i CJIS.

Aby wyświetlić certyfikaty dla tych programów, możesz podać do 100 linków, które je opisują. Mogą to być linki do Twoich aukcji bezpośrednio w programie lub do własnej witryny sieci Web. Te linki są widoczne tylko dla Azure Government klientów.

### <a name="plan-listing"></a>Zaplanuj listę

Na tej karcie są wyświetlane określone informacje dotyczące poszczególnych planów w ramach bieżącej oferty.

### <a name="plan-name"></a>Nazwa planu

Jest to wstępnie wypełnione nazwą, którą wydałeś podczas tworzenia planu. Tę nazwę można zmienić stosownie do potrzeb. Może mieć długość do 50 znaków. Ta nazwa jest wyświetlana jako tytuł tego planu w witrynie Azure Marketplace i Azure Portal. Jest ona używana jako domyślna nazwa modułu po przygotowaniu planu do użycia.

### <a name="plan-summary"></a>Podsumowanie planu

Krótkie podsumowanie planu oprogramowania (nie oferty). To podsumowanie jest wyświetlane w wynikach wyszukiwania portalu Azure Marketplace i może zawierać maksymalnie 100 znaków.

### <a name="plan-description"></a>Opis planu

Opisz, co sprawia, że ten plan oprogramowania jest unikatowy, a także różnice między planami w ramach oferty. Nie opisz oferty, tylko plan. Ten opis zostanie wyświetlony w witrynie Azure Marketplace i w Azure Portal na stronie z **listą ofert** . Może to być taka sama zawartość, którą podano w podsumowaniu planu i zawierający do 2 000 znaków.

Po zakończeniu tych pól wybierz pozycję **Zapisz** .

#### <a name="plan-examples"></a>Przykłady planu

W poniższych przykładach pokazano, jak pola listy planów są wyświetlane w różnych widokach.

Są to pola w portalu Azure Marketplace podczas wyświetlania szczegółów planu:

:::image type="content" source="./partner-center-portal/media/azure-create-container-offer-images/azure-create-10-plan-details-mtplc.png" alt-text="Ilustruje pola wyświetlane podczas przeglądania szczegółów planu w portalu Azure Marketplace.":::

Są to szczegóły planu dotyczące Azure Portal:

:::image type="content" source="./partner-center-portal/media/azure-create-container-offer-images/azure-create-11-plan-details-portal.png" alt-text="Ilustruje szczegóły planu na Azure Portal.":::

### <a name="plan-availability"></a>Planowanie dostępności

Jeśli chcesz ukryć opublikowaną ofertę, aby klienci nie mogli wyszukiwać, przeglądać ani kupować jej w portalu Marketplace, zaznacz pole wyboru **Ukryj plan** na karcie **dostępność** .

To pole jest używane w przypadku:

- Oferta jest przeznaczona do użycia pośrednio, gdy jest przywoływana przez inną aplikację.
- Oferty nie należy kupować osobno.
- Plan został użyty do wstępnego testowania i nie jest już odpowiedni.
- Plan został użyty dla ofert tymczasowych lub sezonowych i nie powinien już być oferowany.

## <a name="technical-configuration"></a>Konfiguracja techniczna

Obrazy kontenerów muszą być hostowane w [Azure Container Registry](https://azure.microsoft.com/services/container-registry/)prywatnym. Na karcie **konfiguracja techniczna** podaj informacje referencyjne dotyczące repozytorium obrazu kontenera w Azure Container Registry.

Po opublikowaniu oferty obraz kontenera jest kopiowany do portalu Azure Marketplace w określonym rejestrze kontenera publicznego. Wszystkie żądania użycia obrazu kontenera są obsługiwane z publicznego rejestru kontenerów platformy Azure Marketplace, a nie Twojego prywatnego. Aby uzyskać szczegółowe informacje, zobacz [Przygotowywanie zasobów technicznych kontenera platformy Azure](create-azure-container-technical-assets.md).

### <a name="image-repository-details"></a>Szczegóły repozytorium obrazów

Podaj poniższe informacje na karcie **szczegóły repozytorium obrazów** .

**Identyfikator subskrypcji platformy Azure** — podaj identyfikator subskrypcji, w której jest raportowane użycie, a usługi są rozliczane dla Azure Container Registry zawierającego obraz kontenera. Ten identyfikator można znaleźć na [stronie Subskrypcje](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) w Azure Portal.

**Nazwa grupy zasobów platformy Azure** — Podaj nazwę [grupy zasobów](/azure/azure-resource-manager/management/manage-resource-groups-portal.md) , która zawiera Azure Container Registry z obrazem kontenera. Grupa zasobów musi być dostępna w IDENTYFIKATORze subskrypcji (powyżej). Nazwę można znaleźć na stronie [grupy zasobów](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceGroups) w Azure Portal.

**Nazwa Azure Container Registry** — podaj nazwę [Azure Container Registry](/azure/container-registry/container-registry-intro.md) zawierającego obraz kontenera. Rejestr kontenerów musi znajdować się w podanej wcześniej grupie zasobów platformy Azure. Uwzględnij tylko nazwę rejestru, a nie pełną nazwę serwera logowania. Pamiętaj, aby pominąć **azurecr.IO** z nazwy. Nazwę rejestru można znaleźć na [stronie rejestry kontenerów](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.ContainerRegistry%2Fregistries) w Azure Portal.

**Nazwa użytkownika administratora dla Azure Container Registry** — Podaj [nazwę użytkownika administratora](/azure/container-registry/container-registry-authentication.md#admin-account)), która jest połączona z Azure Container Registryem zawierającym obraz kontenera. Aby zapewnić firmie dostęp do rejestru, należy podać nazwę użytkownika i hasło. Aby uzyskać nazwę użytkownika i hasło administratora, ustaw właściwość z **obsługą administracyjną** na **wartość true** przy użyciu interfejsu Azure Command-Line Interface (CLI). Opcjonalnie możesz ustawić **użytkownika administracyjnego** , aby **włączyć** go w Azure Portal.

 :::image type="content" source="./partner-center-portal/media/azure-create-container-offer-images/azure-create-12-update-container-registry-edit.png" alt-text="Ilustruje okno dialogowe Aktualizowanie rejestru kontenerów.":::

**Hasło dla Azure Container Registry** — Podaj hasło dla nazwy użytkownika administratora, która jest skojarzona z Azure Container Registry i ma obraz kontenera. Aby zapewnić firmie dostęp do rejestru, należy podać nazwę użytkownika i hasło. Hasło można uzyskać z Azure Portal, przechodząc do **Container Registry**  >  **kluczy dostępu** lub za pomocą interfejsu wiersza polecenia platformy Azure przy użyciu narzędzia [show](/cli/azure/acr/credential#az-acr-credential-show).

:::image type="content" source="./partner-center-portal/media/azure-create-container-offer-images/azure-create-13-access-keys.png" alt-text="Ilustruje menu klucz dostępu.":::

**Nazwa repozytorium w Azure Container Registry**. Podaj nazwę repozytorium Azure Container Registry, w którym znajduje się obraz. Dołącz nazwę repozytorium podczas wypychania obrazu do rejestru. Nazwę repozytorium można znaleźć, przechodząc do [Container Registry](https://azure.microsoft.com/services/container-registry/)  >  strony **repozytoria** Container Registry. Aby uzyskać więcej informacji, zobacz [Wyświetlanie repozytoriów rejestru kontenerów w Azure Portal](/azure/container-registry/container-registry-repositories.md).

> [!NOTE]
> Po ustawieniu nazwy nie można jej zmienić. Użyj unikatowej nazwy dla każdej oferty na koncie.

### <a name="image-tags-for-new-versions-of-your-offer"></a>Tagi obrazu dla nowych wersji oferty

Po opublikowaniu aktualizacji klienci muszą mieć możliwość automatycznego pobrania aktualizacji z portalu Azure Marketplace. Jeśli nie chcesz ich aktualizować, muszą być w stanie pozostać w określonej wersji obrazu. Można to zrobić przez dodanie nowych tagów obrazu przy każdym wprowadzeniu aktualizacji do obrazu.

### <a name="image-tag"></a>Tag obrazu

To pole musi zawierać **najnowszy** tag wskazujący na najnowszą wersję obrazu na wszystkich obsługiwanych platformach. Musi on również zawierać tag Version (na przykład, zaczynając od XX. XX. XX, gdzie XX jest liczbą). Klienci powinni używać [tagów manifestu](https://github.com/estesp/manifest-tool) , aby docelowa była wiele platform. Wszystkie Tagi, do których odwołuje się tag manifestu, również muszą zostać dodane, aby można było je przekazać.

Wszystkie Tagi manifestu (z wyjątkiem najnowszego tagu) muszą zaczynać się od X. Y **-** lub x. y. Z-gdzie x, y i Z są liczbami całkowitymi. Na przykład jeśli **najnowszy** tag wskazuje na 1.0.1-Linux-x64, 1.0.1-Linux-arm32 i 1.0.1-Windows-arm32, te sześć tagów należy dodać do tego pola. Aby uzyskać szczegółowe informacje, zobacz [Przygotowywanie zasobów technicznych kontenera platformy Azure](create-azure-container-technical-assets.md).

> [!NOTE]
> Pamiętaj, aby dodać tag testowy do obrazu, aby można było zidentyfikować obraz podczas testowania.

## <a name="review-and-publish"></a>Przeglądanie i publikowanie

Po zakończeniu wszystkich wymaganych sekcji oferty możesz przesłać ją do przeglądu i publikacji.

W prawym górnym rogu portalu wybierz pozycję **Przejrzyj i** **Opublikuj**.

Na stronie Przegląd możesz:

- Zobacz stan ukończenia dla każdej sekcji oferty. Nie można publikować, dopóki wszystkie sekcje oferty nie zostaną oznaczone jako ukończone.
  - **Nie uruchomiono** — nie uruchomiono i wymaga ukończenia.
  - **Niekompletne** — zawiera błędy, które muszą zostać naprawione lub wymagają podania dodatkowych informacji. Zobacz sekcję wcześniej w tym dokumencie, aby uzyskać pomoc.
  - **Ukończono** — zawiera wszystkie wymagane dane bez błędów. Aby można było przesłać ofertę, wszystkie sekcje oferty muszą zostać ukończone.
- Podaj instrukcje dotyczące testowania dla zespołu certyfikacji, aby upewnić się, że oferta została prawidłowo przetestowana. Ponadto Podaj wszelkie dodatkowe uwagi pomocne przy zrozumieniu oferty.

Aby przesłać ofertę do publikacji, wybierz pozycję **Publikuj**.

Wyślemy Ci wiadomość e-mail z prośbą o poinformowanie o udostępnieniu wersji zapoznawczej oferty do przejrzenia i zatwierdzenia.

Aby opublikować publiczną ofertę, przejdź do Centrum partnerskiego i wybierz pozycję **Przejdź na żywo**.

## <a name="next-step"></a>Następny krok

- [Aktualizowanie istniejącej oferty w komercyjnej witrynie Marketplace](./partner-center-portal/update-existing-offer.md)