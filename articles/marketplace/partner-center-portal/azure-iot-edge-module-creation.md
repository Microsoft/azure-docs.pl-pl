---
title: Tworzenie oferty modułu Azure IoT Edge przy użyciu Centrum partnerskiego w portalu Azure Marketplace
description: Dowiedz się, jak tworzyć, konfigurować i publikować oferty modułu IoT Edge w portalu Azure Marketplace przy użyciu Centrum partnerskiego.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: keferna
ms.author: keferna
ms.date: 08/07/2020
ms.openlocfilehash: 62cb8db094cf1bfd7d81b1da06dd7a1b743ec045
ms.sourcegitcommit: 99955130348f9d2db7d4fb5032fad89dad3185e7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93347486"
---
# <a name="create-an-iot-edge-module-offer"></a>Tworzenie oferty modułu usługi IoT Edge

W tym artykule opisano sposób tworzenia i publikowania oferty usługi Edge dla Internet rzeczy (IoT) w witrynie Azure Marketplace. Przed rozpoczęciem [Utwórz komercyjne konto witryny Marketplace w centrum partnerskim](create-account.md) , jeśli jeszcze tego nie zrobiono. Upewnij się, że Twoje konto jest zarejestrowane w komercyjnym programie Marketplace.

## <a name="create-a-new-offer"></a>Tworzenie nowej oferty

1. Zaloguj się do [Centrum partnerskiego](https://partner.microsoft.com/dashboard/home).
2. W menu po lewej stronie wybierz pozycję **komercyjne Omówienie witryny Marketplace**  >  **Overview**.
3. Na stronie Przegląd wybierz pozycję **+ Nowa oferta**  >  **IoT Edge module**.

    ![Ilustruje menu nawigacji po lewej stronie.](./media/new-offer-iot-edge.png)

> [!IMPORTANT]
> Po opublikowaniu oferty zmiany wprowadzone w centrum partnerskim będą widoczne w sklepach online dopiero po ponownym opublikowaniu oferty. Pamiętaj, aby zawsze ponownie publikować po wprowadzeniu zmian.

### <a name="offer-id-and-alias"></a>Identyfikator oferty i alias

Wprowadź **Identyfikator oferty**. To jest unikatowy identyfikator dla każdej oferty na Twoim koncie.

- Ten identyfikator jest widoczny dla klientów w adresie sieci Web dla ofert i szablonów Azure Resource Manager, jeśli ma zastosowanie.
- Użyj tylko małych liter i cyfr. Może zawierać łączniki i podkreślenia, ale nie spacje i jest ograniczone do 50 znaków. Jeśli na przykład wprowadzisz polecenie **test-Offer-1** , adres sieci Web oferty będzie wynosić `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` .
- Nie można zmienić identyfikatora oferty po wybraniu pozycji **Utwórz**.

Wprowadź **alias oferty**. Jest to nazwa używana dla oferty w centrum partnerskim.

- Ta nazwa nie jest używana w portalu Marketplace i różni się od nazwy oferty i innych wartości widocznych dla klientów.
- Tego nie można zmienić po wybraniu opcji **Utwórz**.

Wybierz pozycję **Utwórz** , aby wygenerować ofertę i kontynuować.

## <a name="offer-overview"></a>Przegląd oferty

Na stronie **Przegląd oferty** przedstawiono wizualną reprezentację kroków wymaganych do opublikowania oferty (zarówno ukończonej, jak i nadchodzącej) oraz czasu, w którym należy wykonać poszczególne kroki.

Ta strona zawiera linki do wykonywania operacji na tej ofercie w zależności od dokonanego wyboru. Na przykład:

- Jeśli oferta jest ofertą typu wersja robocza, Usuń wersję roboczą
- Jeśli oferta jest aktywna [, Zatrzymaj sprzedawanie oferty](update-existing-offer.md#stop-selling-an-offer-or-plan)
- Jeśli oferta jest dostępna w wersji zapoznawczej — [Przejdź na żywo](../review-publish-offer.md#previewing-and-approving-your-offer)
- Jeśli nie ukończono wylogowania wydawcy — [Anuluj publikowanie.](../review-publish-offer.md#cancel-publishing)

## <a name="offer-setup"></a>Konfiguracja oferty

Wykonaj następujące kroki, aby skonfigurować ofertę.

### <a name="customer-leads"></a>Potencjalni klienci

Po opublikowaniu oferty w witrynie Marketplace z centrum partnerskim możesz opcjonalnie połączyć ją z systemem zarządzania relacjami z klientami (CRM). Pozwala to na otrzymywanie informacji kontaktowych klienta, gdy tylko ktoś wyrazi zainteresowanie lub skorzysta z produktu.

1. Wybierz miejsce docelowe potencjalnych klientów, do którego chcesz wysyłać potencjalnych klientów. Centrum partnerskie obsługuje następujące systemy CRM:

    - [Dynamics 365](commercial-marketplace-lead-management-instructions-dynamics.md) dla zaangażowania klienta
    - [Marketo](commercial-marketplace-lead-management-instructions-marketo.md)
    - [Salesforce](commercial-marketplace-lead-management-instructions-salesforce.md)

    > [!NOTE]
    > Jeśli system CRM nie jest wymieniony powyżej, użyj [tabeli platformy Azure](commercial-marketplace-lead-management-instructions-azure-table.md) lub [punktu końcowego HTTPS](commercial-marketplace-lead-management-instructions-https.md) do przechowywania danych potencjalnych klientów, a następnie wyeksportuj dane do systemu CRM.

2. Połącz ofertę z miejscem docelowym potencjalnego klienta podczas publikowania w centrum partnerskim.
3. Upewnij się, że połączenie z miejscem docelowym potencjalnego klienta jest skonfigurowane prawidłowo. Po opublikowaniu go w centrum partnerskim sprawdzimy połączenie i wyślesz potencjalnego klienta. Podczas wyświetlania podglądu oferty przed jej rozpoczęciem możesz również przetestować połączenie z liderem, próbując zakupić ofertę samodzielnie w środowisku wersji zapoznawczej.
4. Upewnij się, że połączenie z miejscem docelowym potencjalnego klienta zostało zaktualizowane, więc nie utracisz żadnych potencjalnych klientów.

Poniżej przedstawiono kilka dodatkowych zasobów zarządzania potencjalnym liderem:

- [Potencjalni klienci z oferty komercyjnej witryny Marketplace](commercial-marketplace-get-customer-leads.md)
- [Często zadawane pytania dotyczące zarządzania potencjalnymi klientami](../lead-management-faq.md#common-questions-about-lead-management)
- [Rozwiązywanie problemów z błędami konfiguracji potencjalnego klienta](../lead-management-faq.md#publishing-config-errors)
- [Omówienie zarządzania potencjalnymi klientami](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf) PDF (Upewnij się, że blokowanie wyskakujących okienek jest wyłączone).

Wybierz pozycję **Zapisz wersję roboczą** przed kontynuowaniem.

### <a name="properties"></a>Właściwości

Ta strona umożliwia definiowanie kategorii używanych do grupowania oferty w witrynie Marketplace oraz umów prawnych, które obsługują Twoją ofertę.

#### <a name="category"></a>Kategoria

Wybierz kategorie i podkategorie, aby umieścić ofertę w odpowiednich obszarach wyszukiwania w portalu Marketplace. Pamiętaj, aby opisać, w jaki sposób oferta obsługuje te kategorie w opisie oferty. Wybierz pozycję:

- Co najmniej jeden i maksymalnie dwie kategorie, w tym podstawowa i pomocnicza Kategoria (opcjonalnie).
- Do dwóch podkategorii dla każdej kategorii podstawowej i/lub pomocniczej. Jeśli żadna Podkategoria nie ma zastosowania do oferty, wybierz pozycję **nie dotyczy**.

Zapoznaj się z pełną listą kategorii i podkategorii w artykule [Oferta z najlepszymi rozwiązaniami](../gtm-offer-listing-best-practices.md). W portalu Marketplace moduły IoT Edge są zawsze wyświetlane w kategorii modułu  **Internet rzeczy**  >  **IoT Edge**   .

#### <a name="legal"></a>Informacje prawne

Musisz podać warunki i postanowienia oferty. Dostępne są dwie opcje:

- Skorzystaj z standardowej umowy dotyczącej komercyjnego portalu Microsoft Marketplace.
- Podaj własne warunki i postanowienia.

##### <a name="standard-contract-for-the-microsoft-commercial-marketplace"></a>Standardowa umowa dla komercyjnego portalu Microsoft Marketplace

Oferujemy standardowy szablon kontraktu, aby ułatwić obsługę transakcji w portalu komercyjnym. Możesz wybrać opcję oferowania rozwiązania w ramach standardowej umowy, którą klienci muszą tylko raz sprawdzić i zaakceptować. Jest to dobra opcja, jeśli nie chcesz utworzyć niestandardowych warunków i postanowień.

Aby dowiedzieć się więcej na temat standardowej umowy, zobacz temat [Standardowy kontrakt dla komercyjnego portalu Microsoft Marketplace](../standard-contract.md). Możesz również pobrać plik PDF [kontraktu standardowego](https://go.microsoft.com/fwlink/?linkid=2041178) (Upewnij się, że blokowanie wyskakujących okienek jest wyłączone).

Aby korzystać z kontraktu standardowego, zaznacz pole wyboru **Użyj kontraktu standardowego dla komercyjnego portalu firmy Microsoft** , a następnie kliknij przycisk **Akceptuj**.

> [!NOTE]
> Po opublikowaniu oferty przy użyciu standardowej umowy dotyczącej komercyjnej witryny Marketplace firmy Microsoft nie można używać własnych niestandardowych warunków i postanowień. Oferuj swoje rozwiązanie w ramach standardowej umowy lub w ramach własnych warunków i postanowień.

![Ilustruje użycie standardowego kontraktu dla komercyjnej witryny Marketplace firmy Microsoft.](media//iot-edge-module-standard-contract-checkbox.png)

##### <a name="your-own-terms-and-conditions"></a>Własne warunki i postanowienia

Aby zapewnić własne niestandardowe warunki i postanowienia, wprowadź je w polu **warunki i postanowienia** . W tym polu można wprowadzić nieograniczoną liczbę znaków w tekście. Aby wypróbować ofertę, klienci muszą zaakceptować te warunki.

Wybierz pozycję **Zapisz wersję roboczą** przed przejściem do następnej sekcji, na liście oferty.

## <a name="offer-listing"></a>Lista oferty

W tym miejscu zdefiniujesz szczegóły oferty, które są wyświetlane w portalu Marketplace. Obejmuje to nazwę oferty, opis, obrazy i tak dalej. Podczas konfigurowania tej oferty należy postępować zgodnie z zasadami szczegółowymi dotyczącymi zasad firmy Microsoft.

> [!NOTE]
> Szczegóły oferty nie muszą znajdować się w języku angielskim, jeśli opis oferty zaczyna się od frazy "Ta aplikacja jest dostępna tylko w języku innym niż angielski]". Istnieje również możliwość udostępnienia przydatnego linku do oferowania zawartości w języku innym niż ten, który jest używany w szczegółach dotyczących oferty.

### <a name="name"></a>Nazwa

Wprowadzona nazwa zostanie wyświetlona jako tytuł oferty. To pole jest wstępnie wypełnione tekstem wprowadzonym w polu **alias oferty** podczas tworzenia oferty. Tę nazwę można później zmienić.

Nazwa:

- Mogą być znakami towarowymi (i może zawierać znaki towarowe lub autorskie).
- Długość nie może przekraczać 50 znaków.
- Nie może zawierać znaków emoji.

### <a name="search-results-summary"></a>Podsumowanie wyników wyszukiwania

Podaj krótki opis oferty. Może to być maksymalnie 100 znaków i jest używana w wynikach wyszukiwania w portalu Marketplace.

### <a name="long-summary"></a>Długie podsumowanie

Podaj bardziej szczegółowy opis oferty. Może to być maksymalnie 256 znaków i jest używana w wynikach wyszukiwania w portalu Marketplace.

### <a name="description"></a>Opis

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

Oferty modułu IoT Edge muszą obejmować minimalny zakres wymagań sprzętowych w dolnej części opisu, taki jak:

- Minimalne wymagania sprzętowe: Linux x64 i arm32 OS, 1 GB pamięci RAM, 500 MB magazynu

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Long description-3](./includes/long-description-3.md)]

#### <a name="privacy-policy-url"></a>Adres URL zasad ochrony prywatności

Wprowadź adres internetowy zasad zachowania poufności informacji organizacji. Użytkownik jest odpowiedzialny za zapewnienie zgodności oferty z przepisami i przepisami dotyczącymi ochrony prywatności. Użytkownik jest odpowiedzialny za publikowanie prawidłowych zasad zachowania poufności informacji w witrynie sieci Web.

#### <a name="useful-links"></a>Przydatne łącza

Podaj uzupełniające dokumenty online dotyczące Twojej oferty. Możesz dodać maksymalnie 25 linków. Aby dodać łącze, wybierz pozycję **+ Dodaj łącze** , a następnie wykonaj następujące pola:

- **Tytuł** — klienci będą widzieć tytuł na stronie szczegółów oferty.
- **Link (adres URL)** — wprowadź link dla klientów, aby wyświetlić dokument w trybie online. Link musi rozpoczynać się od `http://` lub `https://` .

Upewnij się, że dodano co najmniej jeden link do dokumentacji i jeden link do zgodnych urządzeń IoT Edge z [wykazu urządzeń usługi Azure IoT](https://catalog.azureiotsolutions.com/).

### <a name="contact-information"></a>Informacje kontaktowe

Musisz podać nazwę, adres e-mail i numer telefonu dla **kontaktu z pomocą techniczną** i **kontaktu inżynieryjnego.** Te informacje nie są widoczne dla klientów. Jest ona dostępna dla firmy Microsoft i może być świadczona partnerom dostawcy rozwiązań w chmurze (CSP).

- Kontakt z pomocą techniczną (wymagany): w przypadku ogólnych pytań dotyczących pomocy technicznej.
- Kontakt inżynieryjny (wymagany): w przypadku pytań technicznych i problemów z certyfikacją.
- Kontakt programu CSP (opcjonalnie): w przypadku pytań odsprzedawców związanych z programem CSP.

W sekcji **skontaktuj się z pomocą techniczną** Podaj adres internetowy **witryny internetowej pomocy technicznej** , w której partnerzy mogą znaleźć pomoc techniczną dotyczącą oferty w zależności od tego, czy oferta jest dostępna na platformie Azure, Azure Government czy w obu tych przypadkach.

W sekcji **kontakt programu z programem CSP** Podaj link ( **materiały marketingowe programu CSP** ), w którym partnerzy usług kryptograficznych mogą znaleźć materiały marketingowe oferty.

#### <a name="additional-marketplace-listing-resources"></a>Dodatkowe zasoby dotyczące wyświetlania w portalu Marketplace

Aby dowiedzieć się więcej na temat tworzenia list ofert, zobacz [najlepsze rozwiązania dotyczące oferty](../gtm-offer-listing-best-practices.md).

### <a name="marketplace-images"></a>Obrazy z witryny Marketplace

Podaj logo i obrazy, które mają być używane z ofertą. Wszystkie obrazy muszą mieć format PNG. Rozmyte obrazy zostaną odrzucone.

[!INCLUDE [logo tips](../includes/graphics-suggestions.md)]

>[!Note]
>Jeśli występuje problem z przekazywaniem plików, upewnij się, że sieć lokalna nie blokuje https://upload.xboxlive.com usługi używanej przez centrum partnerskie.

#### <a name="store-logos"></a>Logo sklepu

Podaj plik PNG dla logo o **dużym** rozmiarze. Centrum partnerskie będzie używać tego do tworzenia **małych** i **średnich** logo. Opcjonalnie można zastąpić je innymi obrazami później.

- **Duże** (od 216 x 216 do 350 x 350 px, wymagane)
- **Średnia** (90 x 90 pikseli, opcjonalnie)
- **Mały** (48 x 48 pikseli, opcjonalnie)

Te logo są używane w różnych miejscach na liście:

[!INCLUDE [logos-azure-marketplace-only](../includes/logos-azure-marketplace-only.md)]

[!INCLUDE [Logo tips](../includes/graphics-suggestions.md)]

#### <a name="screenshots-optional"></a>Zrzuty ekranu (opcjonalnie)

Dodaj maksymalnie pięć zrzutów ekranu, które pokazują, jak działa Twoja oferta. Każdy z nich musi mieć 1280 x 720 pikseli w rozmiarze i formacie PNG.

#### <a name="videos-optional"></a>Wideo (opcjonalnie)

Dodaj do pięciu filmów wideo, które przedstawiają Twoją ofertę. Wprowadź nazwę filmu wideo, jego adres internetowy oraz miniaturę obrazu PNG wideo o rozmiarze 1280 x 720 pikseli.

#### <a name="marketplace--examples"></a>Przykłady dla witryny Marketplace

Oto przykład sposobu wyświetlania informacji o ofercie w witrynie Azure Marketplace:

:::image type="content" source="media/example-iot-azure-marketplace-offer.png" alt-text="Ilustruje, jak ta oferta pojawia się w witrynie Azure Marketplace.":::

#### <a name="call-out-descriptions"></a>Opisy połączeń

1. Duże logo
2. Kategorie
3. Adres pomocy technicznej (link)
4. Warunki i postanowienia
5. Adres zasad ochrony prywatności (link)
6. Nazwa
7. Podsumowanie
8. Opis
9. Przydatne łącza
10. Zrzuty ekranu/wideo

<br>Oto przykład sposobu wyświetlania informacji o ofercie w wynikach wyszukiwania w portalu Azure Marketplace:

:::image type="content" source="media/example-iot-azure-marketplace-offer-search-results.png" alt-text="Ilustruje, w jaki sposób ta oferta pojawia się w wynikach wyszukiwania w portalu Azure Marketplace.":::

#### <a name="call-out-descriptions"></a>Opisy połączeń

1. Małe logo
2. Nazwa oferty
3. Podsumowanie wyników wyszukiwania

<br>Oto przykład sposobu wyświetlania informacji o ofercie w Azure Portal:

:::image type="content" source="media/example-iot-azure-portal-offer.png" alt-text="Ilustruje, w jaki sposób ta oferta pojawia się w Azure Portal.":::

#### <a name="call-out-descriptions"></a>Opisy połączeń

1. Nazwa
2. Opis
3. Przydatne łącza
4. Zrzuty ekranu

<br>Oto przykład sposobu wyświetlania informacji o ofercie w Azure Portal wynikach wyszukiwania:

:::image type="content" source="media/example-iot-azure-portal-offer-search-results.png" alt-text="Ilustruje, w jaki sposób ta oferta pojawia się w wynikach wyszukiwania Azure Portal.":::

#### <a name="call-out-descriptions"></a>Opisy połączeń

1. Małe logo
2. Nazwa oferty
3. Podsumowanie wyników wyszukiwania

<br>Wybierz pozycję **Zapisz wersję roboczą** przed przejściem do następnej sekcji, wersja zapoznawcza.

## <a name="preview"></a>Wersja zapoznawcza

Na **karcie Podgląd** możesz wybrać ograniczonej **odbiorców w wersji zapoznawczej** , aby sprawdzić poprawność oferty przed opublikowaniem jej na żywo w szerszej grupie odbiorców w portalu Marketplace.

> [!IMPORTANT]
> Po wyświetleniu oferty w wersji zapoznawczej musisz wybrać pozycję **Przejdź na żywo** , aby opublikować ofertę publicznie.

Określ odbiorców w wersji zapoznawczej przy użyciu identyfikatorów GUID identyfikatora subskrypcji platformy Azure, a także opcjonalny opis dla każdej z nich. Żadne z tych pól nie może być widoczne dla klientów.

> [!NOTE]
> Identyfikator subskrypcji platformy Azure można znaleźć na stronie Subskrypcje w Azure Portal.

Dodaj co najmniej jeden identyfikator subskrypcji platformy Azure — pojedynczo (do 10) lub przekazując plik CSV (do 100). Dodając te identyfikatory subskrypcji, można zdefiniować, kto może wyświetlać podgląd oferty przed opublikowaniem jej na żywo. Jeśli oferta już istnieje, możesz zdefiniować odbiorców wersji zapoznawczej w celu przetestowania zmian lub aktualizacji oferty.

Wybierz pozycję **Zapisz wersję roboczą** przed przejściem do następnej sekcji, Zaplanuj przegląd.

## <a name="plan-overview"></a>Przegląd planu

Na tej karcie można podać różne opcje planu w ramach tej samej oferty w centrum partnerskim. Plany (dawniej nazywane jednostkami SKU) mogą różnić się od dostępnych chmur, takich jak chmury globalne, chmury rządowe i obraz, do którego odwołuje się plan. Aby wystawić ofertę w portalu Marketplace, należy skonfigurować co najmniej jeden plan.

Możesz utworzyć do 100 planów dla każdej oferty: maksymalnie 45 z nich mogą być prywatne. Dowiedz się więcej o planach prywatnych w [ofertach prywatnych w komercyjnym portalu Microsoft Marketplace](../private-offers.md).

Po utworzeniu planów na karcie **Przegląd planu** jest wyświetlana wartość:

- Nazwy planów
- Model cen
- Regiony platformy Azure (globalne lub rządowe)
- Bieżący stan publikowania
- Wszystkie dostępne akcje

Akcje dostępne w omówieniu planu różnią się w zależności od bieżącego stanu planu. Obejmują one:

- **Usuń wersję roboczą** : Jeśli plan jest stanem wersja robocza.
- **Zatrzymaj sprzedawanie planu** : Jeśli stan planu jest opublikowany na żywo.

### <a name="create-new-plan"></a>Utwórz nowy plan

Wybierz pozycję **Utwórz nowy plan**. Zostanie wyświetlone okno dialogowe **nowy plan** .

W polu **identyfikator planu** Utwórz unikatowy identyfikator planu dla każdego planu w tej ofercie. Ten identyfikator będzie widoczny dla klientów w adresie sieci Web produktu. Używaj tylko małych liter i cyfr, kresek lub podkreśleń i maksymalnie 50 znaków.

W polu **Nazwa planu** wprowadź nazwę dla tego planu. Klienci widzą tę nazwę podczas wybierania planu do wyboru w ramach oferty. Utwórz unikatową nazwę dla każdego planu w tej ofercie. Można na przykład użyć nazwy oferty **systemu Windows Server** z planami **Windows Server 2016** i **Windows Server 2019**.

> [!NOTE]
> Nie można zmienić identyfikatora planu po wybraniu opcji **Utwórz**.

Wybierz przycisk **Utwórz**.

### <a name="plan-setup"></a>Konfigurowanie planu

Na tej karcie można skonfigurować chmurę, w której plan jest dostępny. Odpowiedzi na tej karcie mają wpływ na to, które pola są wyświetlane na innych kartach.

#### <a name="azure-regions"></a>Regiony świadczenia usługi Azure

Wszystkie plany dla ofert modułu IoT Edge są automatycznie udostępniane na **platformie Azure Global**.  Twój plan może być używany przez klientów we wszystkich globalnych regionach platformy Azure korzystających z portalu Marketplace. Aby uzyskać szczegółowe informacje, zobacz [dostępność geograficzna i obsługa waluty](../marketplace-geo-availability-currencies.md).

Wybierz opcję [Azure Government](../../azure-government/documentation-government-welcome.md) , aby rozwiązanie było wyświetlane w tym miejscu. Jest to chmura społecznościowa dla instytucji rządowych, której dostęp jest kontrolowany przez klientów z federalnych, stanowych i lokalnych lub plemienneych instytucji rządowych, a także partnerów uprawnionych do ich używania. Wydawca jest odpowiedzialny za wszelkie kontrole zgodności, miary zabezpieczeń i najlepsze rozwiązania dla tej społeczności w chmurze. Azure Government używa fizycznie wyizolowanych centrów danych i sieci (znajdujących się tylko w Stanach Zjednoczonych). Przed [opublikowaniem](../../azure-government/documentation-government-manage-marketplace-partners.md) w celu Azure Government Przetestuj i Potwierdź swoje rozwiązanie w tym obszarze, ponieważ wyniki mogą się różnić. Aby przygotować i przetestować rozwiązanie, zażądaj konta próbnego od [Microsoft Azure Government wersji próbnej](https://azure.microsoft.com/global-infrastructure/government/request/).

> [!NOTE]
> Po opublikowaniu planu i udostępnieniu go w określonym regionie nie można usunąć tego regionu.

#### <a name="azure-government-certifications"></a>Certyfikaty Azure Government

Ta opcja jest widoczna tylko w przypadku wybrania **Azure Government** w obszarze **regiony platformy Azure**.

Usługi Azure Government Services obsługują dane, które podlegają określonym przepisom i wymogom rządowym. Na przykład FedRAMP, NIST 800,171 (DIB), ITAR, IRS 1075, DoD P4 i CJIS. Aby zapewnić świadomość certyfikatów dla tych programów, możesz udostępnić do 100 linków opisujących certyfikaty. Mogą to być linki do Twoich aukcji bezpośrednio w programie lub do własnej witryny sieci Web. Te linki są widoczne tylko dla Azure Government klientów.

### <a name="plan-listing"></a>Zaplanuj listę

Na tej karcie są wyświetlane określone informacje dotyczące poszczególnych planów w ramach tej samej oferty.

### <a name="plan-name"></a>Nazwa planu

Jest to wstępnie wypełnione nazwą, którą wydałeś podczas tworzenia planu. W razie konieczności można zmienić tę nazwę. Może mieć długość do 50 znaków. Ta nazwa jest wyświetlana jako tytuł tego planu w witrynie Azure Marketplace i Azure Portal. Jest ona używana jako domyślna nazwa modułu po przygotowaniu planu do użycia.

### <a name="plan-summary"></a>Podsumowanie planu

Podaj krótkie podsumowanie planu (nie oferty). To podsumowanie jest wyświetlane w wynikach wyszukiwania portalu Azure Marketplace i może zawierać maksymalnie 100 znaków.

### <a name="plan-description"></a>Opis planu

Opisz, co sprawia, że ten plan jest unikatowy, oraz różnice między planami w ramach oferty. Nie opisz oferty, tylko plan. Ten opis zostanie wyświetlony w witrynie Azure Marketplace i w Azure Portal na stronie z listą ofert. Może to być taka sama zawartość, którą podano w podsumowaniu planu i zawierający do 2 000 znaków.

Po zakończeniu tych pól wybierz pozycję **Zapisz wersję roboczą** .

#### <a name="plan-examples"></a>Przykłady planu

Oto przykładowe szczegóły dotyczące planu witryny Azure Marketplace (wszystkie wymienione ceny są przeznaczone wyłącznie do celów i nie są przeznaczone do odzwierciedlenia rzeczywistych kosztów):

:::image type="content" source="media/example-iot-azure-marketplace-plan.png" alt-text="Ilustruje szczegóły planu witryny Azure Marketplace.":::

#### <a name="call-out-descriptions"></a>Opisy połączeń

1. Nazwa oferty
2. Nazwa planu
3. Opis planu

<br>Poniżej przedstawiono przykładowe szczegóły planu Azure Portal (wszelkie wymienione ceny są przeznaczone wyłącznie do celów i nie są przeznaczone do odzwierciedlenia rzeczywistych kosztów):

:::image type="content" source="media/example-iot-azure-marketplace-plan-details.png" alt-text="Ilustruje szczegóły planu Azure Portal.":::

#### <a name="call-out-descriptions"></a>Opisy połączeń

1. Nazwa oferty
2. Nazwa planu
3. Opis planu

### <a name="availability"></a>Dostępność

Jeśli chcesz ukryć opublikowaną ofertę, aby klienci nie mogli wyszukiwać, przeglądać ani kupować jej w portalu Marketplace, zaznacz pole wyboru **Ukryj plan** na karcie dostępność.

To pole jest często stosowane w przypadku:

- Oferta jest przeznaczona do użycia tylko pośrednio, gdy istnieje odwołanie do innej aplikacji.
- Oferty nie należy kupować osobno.
- Plan został użyty do wstępnego testowania i nie jest już odpowiedni.
- Plan został użyty dla ofert tymczasowych lub sezonowych i nie powinien już być oferowany.

## <a name="technical-configuration"></a>Konfiguracja techniczna

Typ oferty **modułu IoT Edge** jest określonym typem kontenera, który działa na urządzeniu IoT Edge. Na karcie **konfiguracja techniczna** podaj informacje referencyjne dotyczące repozytorium obrazów kontenerów w ramach [Azure Container Registry](https://azure.microsoft.com/services/container-registry/), a także ustawienia konfiguracji umożliwiające klientom korzystanie z modułu.

Po opublikowaniu oferty obraz kontenera IoT Edge jest kopiowany do portalu Azure Marketplace w określonym rejestrze kontenera publicznego. Wszystkie żądania od użytkowników platformy Azure korzystające z Twojego modułu są udostępniane z rejestru publicznego kontenera usługi Azure Marketplace, a nie z prywatnego rejestru kontenerów.

Można wskazać wiele platform i udostępnić kilka wersji obrazu kontenera modułu przy użyciu tagów. Aby dowiedzieć się więcej na temat tagów i przechowywania wersji, zobacz [Przygotowywanie zasobów technicznych modułu IoT Edge](create-iot-edge-module-asset.md).

### <a name="image-repository-details"></a>Szczegóły repozytorium obrazów

Na karcie **szczegóły repozytorium obrazów** uzyskasz następujące informacje.

**Wybierz źródło obrazu** : wybierz opcję **Azure Container Registry** .

**Identyfikator subskrypcji platformy Azure** : podaj identyfikator subskrypcji, w której raportowane jest użycie zasobów, a usługi są rozliczane dla Azure Container Registry zawierającego obraz kontenera. Ten identyfikator można znaleźć na [stronie Subskrypcje](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) w Azure Portal.

**Nazwa grupy zasobów platformy Azure** : Podaj nazwę [grupy zasobów](../../azure-resource-manager/management/manage-resource-groups-portal.md) , która zawiera Azure Container Registry z obrazem kontenera. Grupa zasobów musi być dostępna w IDENTYFIKATORze subskrypcji (powyżej). Nazwę można znaleźć na stronie [grupy zasobów](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceGroups) w Azure Portal.

**Nazwa rejestru kontenerów platformy Azure** : podaj nazwę [Azure Container Registry](../../container-registry/container-registry-intro.md) , w którym znajduje się obraz kontenera. Rejestr kontenerów musi znajdować się w podanej wcześniej grupie zasobów platformy Azure. Podaj tylko nazwę rejestru, a nie pełną nazwę serwera logowania. Pamiętaj, aby pominąć **azurecr.IO** z nazwy. Nazwę rejestru można znaleźć na [stronie rejestry kontenerów](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.ContainerRegistry%2Fregistries) w Azure Portal.

**Nazwa użytkownika administratora dla Azure Container Registry** : Podaj [nazwę użytkownika administratora](../../container-registry/container-registry-authentication.md#admin-account)) skojarzoną z Azure Container Registryem zawierającym obraz kontenera. Aby zapewnić firmie dostęp do rejestru, należy podać nazwę użytkownika i hasło. Aby uzyskać nazwę użytkownika i hasło administratora, ustaw właściwość z **obsługą administracyjną** na **wartość true** przy użyciu interfejsu Azure Command-Line Interface (CLI). Opcjonalnie możesz ustawić **użytkownika administracyjnego** , aby **włączyć** go w Azure Portal.

:::image type="content" source="media/example-iot-update-container-registry.png" alt-text="Ilustruje okno dialogowe Aktualizowanie rejestru kontenerów.":::

#### <a name="call-out-description"></a>Opis połączenia

1. Administrator

<br>**Hasło dla Azure Container Registry** : Podaj hasło dla nazwy użytkownika administratora, która jest skojarzona z Azure Container Registry i ma obraz kontenera. Aby zapewnić firmie dostęp do rejestru, należy podać nazwę użytkownika i hasło. Hasło można uzyskać z Azure Portal, przechodząc do **Container Registry**  >  **kluczy dostępu** lub za pomocą interfejsu wiersza polecenia platformy Azure przy użyciu narzędzia [show.](/cli/azure/acr/credential#az-acr-credential-show)

:::image type="content" source="media/example-iot-access-keys.png" alt-text="Ilustruje ekran klucz dostępu w Azure Portal.":::

#### <a name="call-out-descriptions"></a>Opisy połączeń

1. Klawisze dostępu
2. Nazwa użytkownika
3. Hasło

**Nazwa repozytorium w Azure Container Registry**. Podaj nazwę repozytorium Azure Container Registry, w którym znajduje się obraz. Nazwa repozytorium jest określana podczas wypychania obrazu do rejestru. Nazwę repozytorium można znaleźć, przechodząc do [Container Registry](https://azure.microsoft.com/services/container-registry/)  >  **strony repozytoria** Container Registry. Aby uzyskać więcej informacji, zobacz [Wyświetlanie repozytoriów rejestru kontenerów w Azure Portal](../../container-registry/container-registry-repositories.md). Po ustawieniu nazwy nie można jej zmienić. Użyj unikatowej nazwy dla każdej oferty na koncie.

### <a name="image-tags-for-new-versions-of-your-offer"></a>Tagi obrazu dla nowych wersji oferty

Po opublikowaniu aktualizacji klienci muszą mieć możliwość automatycznego pobrania aktualizacji z portalu Azure Marketplace. Jeśli nie chcesz ich aktualizować, muszą być w stanie pozostać w określonej wersji obrazu. Można to zrobić przez dodanie nowych tagów obrazu przy każdym wprowadzeniu aktualizacji do obrazu.

**Tag obrazu**. To pole musi zawierać **najnowszy** tag wskazujący na najnowszą wersję obrazu na wszystkich obsługiwanych platformach. Musi on również zawierać tag Version (na przykład, zaczynając od XX. XX. XX, gdzie XX jest liczbą). Klienci powinni używać [tagów manifestu](https://github.com/estesp/manifest-tool) , aby docelowa była wiele platform. Wszystkie Tagi, do których odwołuje się tag manifestu, również muszą zostać dodane, aby można było je przekazać. Wszystkie Tagi manifestu (z wyjątkiem najnowszego tagu) muszą rozpoczynać się od litery X. Y-lub X. Y. Z-gdzie X, Y i Z są liczbami całkowitymi. Na przykład jeśli najnowszy tag wskazuje na 1.0.1-Linux-x64, 1.0.1-Linux-arm32 i 1.0.1-Windows-arm32, te sześć tagów należy dodać do tego pola. Aby uzyskać szczegółowe informacje o tagach i wersji, zobacz [Przygotowywanie zasobów technicznych modułu IoT Edge.](create-iot-edge-module-asset.md)

### <a name="default-deployment-settings-optional"></a>Domyślne ustawienia wdrożenia (opcjonalnie)

Zdefiniuj najczęściej używane ustawienia w celu wdrożenia modułu IoT Edge. Zoptymalizuj wdrożenia klientów, umożliwiając im uruchamianie modułu IoT Edge za pomocą ustawień domyślnych.

**Trasy domyślne**. IoT Edge Hub zarządza komunikacją między modułami, IoT Hub i urządzeniami. Można ustawić trasy dla danych wejściowych i wyjściowych między modułami i IoT Hub, co zapewnia elastyczność wysyłania wiadomości, gdy wymagają one przechodzenia bez konieczności stosowania dodatkowych usług do przetwarzania komunikatów lub pisania dodatkowych kodów. Trasy są konstruowane przy użyciu par nazwa/wartość. Można zdefiniować maksymalnie pięć domyślnych nazw tras, które mają długość do 512 znaków.

Należy pamiętać, aby użyć prawidłowej [składni trasy](../../iot-edge/module-composition.md#declare-routes)) w wartości trasy (zazwyczaj zdefiniowanej jako z/Message/* w $upstream). Oznacza to, że wszystkie komunikaty wysyłane przez dowolne moduły przejdą do IoT Hub. Aby odwołać się do modułu, użyj jego domyślnej nazwy, która będzie **nazwą oferty** , bez spacji i znaków specjalnych. Aby odwołać się do innych modułów, które nie są jeszcze znane, użyj <FROM_MODULE_NAME> Konwencji, aby poinformować klientów, że potrzebują oni zaktualizować te informacje. Aby uzyskać szczegółowe informacje na temat tras IoT Edge, zobacz [deklarowanie tras](../../iot-edge/module-composition.md#declare-routes)).

Na przykład, jeśli moduł ContosoModule nasłuchuje danych wejściowych na ContosoInput i dane wyjściowe w ContosoOutput, warto zdefiniować następujące dwie domyślne trasy:

- Nazwa #1: ToContosoModule
- Wartość #1: z/messages/modules/<FROM_MODULE_NAME>/Outputs/* do BrokeredEndpoint ("/modules/ContosoModule/inputs/ContosoInput")
- Nazwa #2: FromContosoModuleToCloud
- Wartość #2: od/messages/modules/ContonsoModule/outputs/ContosoOutput do $upstream

**Domyślne właściwości sznurka modułu**. Sznurek modułu jest dokumentem JSON w IoT Hub, w którym przechowywane są informacje o stanie wystąpienia modułu, łącznie z żądanymi właściwościami. Odpowiednie właściwości są używane wraz z zgłoszonymi właściwościami do synchronizacji konfiguracji lub warunków modułu. Zaplecze rozwiązania może ustawić odpowiednie właściwości i moduł może je odczytać. Moduł może również odbierać powiadomienia o zmianach w odpowiednich właściwościach. Żądane właściwości są tworzone przy użyciu maksymalnie pięciu par nazwa/wartość, a każda wartość domyślna musi być krótsza niż 512 znaków. Można zdefiniować maksymalnie pięć pożądanych właściwości nazwa/wartość. Wartości właściwości przędzy muszą być prawidłowymi wartościami JSON, niezmienionymi bez użycia tablic z maksymalną zagnieżdżoną hierarchią czterech poziomów. W scenariuszu, w którym parametr wymagany dla wartości domyślnej nie ma sensu (na przykład adres IP serwera klienta), można dodać parametr jako wartość domyślną. Aby dowiedzieć się więcej o właściwościach bliźniaczych, zobacz [Definiowanie lub aktualizowanie żądanych właściwości](../../iot-edge/module-composition.md#define-or-update-desired-properties).

Na przykład, jeśli moduł obsługuje dynamicznie konfigurowalne częstotliwość odświeżania przy użyciu odpowiednich właściwości przędzy, warto zdefiniować następującą domyślną właściwość splotu:

- Nazwa #1: RefreshRate
- Wartość #1:60

**Domyślne zmienne środowiskowe**. Zmienne środowiskowe zawierają dodatkowe informacje dotyczące modułu, który pomaga w procesie konfiguracji. Zmienne środowiskowe są tworzone przy użyciu par nazwa/wartość. Każda domyślna nazwa zmiennej środowiskowej i wartość musi być krótsza niż 512 znaków i można zdefiniować maksymalnie pięć. Jeśli parametr wymagany dla wartości domyślnej nie ma sensu (na przykład adres IP serwera klienta), można dodać parametr jako wartość domyślną.

Jeśli na przykład moduł wymaga zaakceptowania warunków użytkowania przed rozpoczęciem, można zdefiniować następującą zmienną środowiskową:

- Nazwa #1: ACCEPT_EULA
- Wartość #1: Y

**Domyślne opcje tworzenia kontenera**. Opcje tworzenia kontenera kierują tworzeniem kontenera Docker modułu IoT Edge. IoT Edge obsługuje opcje tworzenia kontenera interfejsu API aparatu platformy Docker. Zobacz wszystkie opcje w [kontenerach listy.](https://docs.docker.com/engine/api/v1.30/#operation/ContainerList) Pole Create Options musi być prawidłowym kodem JSON, bez znaku ucieczki i mniej niż 512 znaków.

Na przykład, jeśli moduł wymaga powiązania portu, Zdefiniuj następujące opcje tworzenia:

"HostConfig": {"PortBindings": {"5012/TCP": [{"HostPort": "5012"}]}

## <a name="review-and-publish"></a>Przeglądanie i publikowanie

Po zakończeniu wszystkich wymaganych sekcji oferty możesz przesłać ją do przeglądu i publikacji.

W prawym górnym rogu portalu wybierz pozycję **Przejrzyj i Opublikuj**.

Na stronie Przegląd można zobaczyć stan publikowania:

- Zobacz stan ukończenia dla każdej sekcji oferty. Nie można publikować, dopóki wszystkie sekcje oferty nie zostaną oznaczone jako ukończone.
    - **Nierozpoczęte** — sekcja nie została uruchomiona i musi zostać ukończona.
    - **Niekompletne** — sekcja zawiera błędy, które muszą zostać naprawione lub wymaga podania dodatkowych informacji. Zapoznaj się z sekcjami wcześniej w tym dokumencie, aby uzyskać wskazówki.
    - **Ukończono** — sekcja zawiera wszystkie wymagane dane i nie ma błędów. Aby można było przesłać ofertę, wszystkie sekcje oferty muszą zostać ukończone.
- Podaj instrukcje dotyczące testowania dla zespołu certyfikacji, aby upewnić się, że oferta została prawidłowo przetestowana. Ponadto Podaj wszelkie dodatkowe uwagi pomocne przy zrozumieniu oferty.

Aby przesłać ofertę do publikacji, wybierz pozycję **Publikuj**.

Wyślemy Ci wiadomość e-mail z prośbą o poinformowanie o udostępnieniu wersji zapoznawczej oferty do przejrzenia i zatwierdzenia. Aby opublikować publiczną ofertę, przejdź do Centrum partnerskiego i wybierz pozycję **Przejdź na żywo**.

## <a name="next-steps"></a>Następne kroki

- [Aktualizowanie istniejącej oferty w komercyjnej witrynie Marketplace](update-existing-offer.md)