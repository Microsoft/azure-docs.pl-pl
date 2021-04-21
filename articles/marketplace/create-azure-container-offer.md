---
title: Tworzenie oferty kontenera platformy Azure — Azure Marketplace
description: Dowiedz się, jak utworzyć i opublikować ofertę kontenera w Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: keferna
ms.author: keferna
ms.date: 06/17/2020
ms.openlocfilehash: dc7a81f1646fc9f51a4e0bcaf37ef61ca669414e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107780519"
---
# <a name="create-an-azure-container-offer-in-azure-marketplace"></a>Tworzenie oferty kontenera platformy Azure w witrynie Azure Marketplace

W tym artykule opisano sposób tworzenia i publikowania oferty kontenera dla Azure Marketplace. Przed rozpoczęciem utwórz [konto komercyjnej](create-account.md) platformy handlowej w Partner Center, jeśli jeszcze tego nie zrobiono. Upewnij się, że Twoje konto jest zarejestrowane w programie platformy handlowej.

## <a name="create-a-new-offer"></a>Tworzenie nowej oferty

1. Zaloguj się do [Partner Center](https://partner.microsoft.com/dashboard/home).

2. W menu nav po lewej stronie wybierz pozycję **Omówienie komercyjnej platformy**  >  **handlowej.**

3. Na stronie Przegląd wybierz pozycję **+ Nowa oferta** Kontener platformy  >  **Azure.**

   ![Ilustruje menu nawigacji po lewej stronie.](./partner-center-portal/media/new-offer-azure-container.png)

> [!TIP]
> Po opublikowaniu oferty zmiany wprowadzone w tej Partner Center są wyświetlane w sklepach online tylko po jej ponownej opublikowaniu. Pamiętaj, aby zawsze ponownie opublikować po w wprowadzeniem zmian.

### <a name="offer-id-and-alias"></a>Identyfikator oferty i alias

Wprowadź identyfikator **oferty**. Jest to unikatowy identyfikator dla każdej oferty na Twoim koncie.

- Ten identyfikator jest widoczny dla klientów w adresie internetowym oferty marketplace i Azure Resource Manager szablonów, jeśli ma to zastosowanie.
- Użyj tylko małych liter i cyfr. Może zawierać łączniki i podkreślenia, ale bez spacji i może zawierać tylko 50 znaków. Jeśli na przykład **wpiszemy wartość test-offer-1,** adresem internetowym oferty będzie `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` .
- Nie można zmienić identyfikatora oferty po wybraniu opcji **Utwórz**.

Wprowadź alias **oferty**. Jest to nazwa używana dla oferty w Partner Center.

- Ta nazwa nie jest używana na platformie handlowej i różni się od nazwy oferty i innych wartości wyświetlanych klientom.
- Nie można tego zmienić po wybraniu opcji **Utwórz**.

Wybierz **pozycję Utwórz,** aby wygenerować ofertę i kontynuować.

## <a name="offer-overview"></a>Omówienie oferty

Strona **Omówienie oferty** przedstawia wizualną reprezentację kroków wymaganych do opublikowania tej oferty (zarówno ukończonych, jak i nadchodzących) oraz czasu ukończenia każdego kroku.

Ta strona zawiera różne linki w zależności od bieżącego stanu oferty. Na przykład:

- Jeśli oferta jest w wersji roboczej — usuń ofertę wersji roboczej
- Jeśli oferta jest żywa — [zatrzymaj sprzedaż oferty](./partner-center-portal/update-existing-offer.md#stop-selling-an-offer-or-plan)
- Jeśli oferta jest w wersji zapoznawczej — [przejdź na żywo](review-publish-offer.md#previewing-and-approving-your-offer)
- Jeśli wylogowanie wydawcy nie zostało ukończone — anuluj [publikowanie.](review-publish-offer.md#cancel-publishing)

## <a name="offer-setup"></a>Konfiguracja oferty

Wykonaj następujące kroki, aby skonfigurować ofertę.

### <a name="customer-leads--optional"></a>Potencjalni klienci — opcjonalnie

Podczas publikowania oferty na platformie handlowej za Partner Center można połączyć ją z systemem zarządzania relacjami z klientami (CRM). Umożliwia to otrzymywanie informacji kontaktowych klienta, gdy tylko ktoś wyrazi zainteresowanie Twoim produktem lub użyje go.

1. **Wybierz miejsce docelowe potencjalnych klientów, do którego chcesz wysyłać potencjalnych klientów.** Partner Center obsługuje następujące systemy CRM:

   - [Dynamics 365](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md) for Customer Engagement
   - [Marketo](./partner-center-portal/commercial-marketplace-lead-management-instructions-marketo.md)
   - [Salesforce](./partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce.md)

   > [!NOTE]
   > Jeśli twój system CRM nie jest wymieniony powyżej, użyj usługi [Azure Table table](./partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md) lub https [endpoint](./partner-center-portal/commercial-marketplace-lead-management-instructions-https.md) do przechowywania danych potencjalnych klientów, a następnie wyeksportuj dane do systemu CRM.

2. Połącz ofertę z miejscem docelowym potencjalnych klienta podczas publikowania w Partner Center.
3. Upewnij się, że połączenie z miejscem docelowym potencjalnych klienta zostało prawidłowo skonfigurowane. Po opublikowaniu go w Partner Center zweryfikujemy połączenie i wyślemy do Ciebie testowego potencjalnego klienta. Podczas wyświetlania podglądu oferty przed jej rozpoczęciem możesz również przetestować połączenie potencjalnych klienta, próbując kupić ofertę samodzielnie w środowisku w wersji zapoznawczej.
4. Upewnij się, że połączenie z miejscem docelowym potencjalnych klientów pozostaje zaktualizowane, aby nie utracić żadnych potencjalnych klientów.

Oto kilka dodatkowych zasobów do zarządzania potencjalnymi klientami:

- [Potencjalni klienci z oferty na platformie handlowej](./partner-center-portal/commercial-marketplace-get-customer-leads.md)
- [Często zadawane pytania dotyczące zarządzania potencjalnymi klientami](lead-management-faq.md#common-questions-about-lead-management)
- [Rozwiązywanie problemów z błędami konfiguracji potencjalnych klienta](lead-management-faq.md#publishing-config-errors)
- [Omówienie zarządzania potencjalnymi klientami](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf) PDF (Upewnij się, że blokowanie wyskakujących okienek jest wyłączone).

Przed **kontynuowaniem wybierz pozycję Zapisz** wersje robocze.

### <a name="properties"></a>Właściwości

Ta strona umożliwia zdefiniowanie kategorii używanych do grupowania oferty na platformie handlowej oraz umów prawnych, które obsługują ofertę.

#### <a name="category"></a>Kategoria

Wybierz kategorie i podkategorie, aby umieścić swoją ofertę w odpowiednich obszarach wyszukiwania na platformie handlowej. Pamiętaj, aby w opisie oferty opisać, jak Twoja oferta obsługuje te kategorie. Wybierz pozycję:

- Co najmniej jedna i maksymalnie dwie kategorie, w tym kategoria podstawowa i pomocnicza (opcjonalnie).
- Maksymalnie dwie podkategorie dla każdej kategorii podstawowej i/lub pomocniczej. Jeśli żadna podkategoria nie ma zastosowania do Twojej oferty, wybierz **pozycję Nie dotyczy.**

Zobacz pełną listę kategorii i podkategorii w [tece Najlepsze rozwiązania dotyczące ofert.](gtm-offer-listing-best-practices.md) Kontenery są zawsze wyświetlane w **obszarze Kontenery,** a następnie **kategorii Obrazy kontenerów.**

#### <a name="legal"></a>Informacje prawne

Musisz podać warunki i postanowienia dotyczące oferty. Dostępne są dwie opcje:

- Skorzystaj z umowa standardowa platformy handlowej firmy Microsoft.
- Podaj własne warunki i postanowienia.

#### <a name="standard-contract-for-the-microsoft-commercial-marketplace"></a>Umowa standardowa dla platformy handlowej firmy Microsoft

Oferujemy szablon umowa standardowa ułatwiający transakcje na platformie handlowej. Możesz zaoferować swoje rozwiązanie w ramach umowa standardowa, które klienci muszą sprawdzić i zaakceptować tylko raz. Jest to dobra opcja, jeśli nie chcesz tworzyć niestandardowych warunków i postanowień.

Aby dowiedzieć się więcej na temat umowa standardowa, zobacz umowa standardowa for the Microsoft commercial marketplace (Platforma handlowa [firmy Microsoft).](standard-contract.md) Możesz również pobrać plik [PDF umowa standardowa](https://go.microsoft.com/fwlink/?linkid=2041178) (upewnij się, że blokowanie wyskakujących okienek jest wyłączone).

Aby użyć umowa standardowa, wybierz pozycję **Użyj umowa standardowa dla platformy handlowej firmy Microsoft](.. /standard-contract.md)

> [!NOTE]
> Po opublikowaniu oferty przy użyciu umowy standardowej dla platformy handlowej firmy Microsoft nie można używać własnych niestandardowych warunków i postanowień. Możesz zaoferować rozwiązanie zgodnie z umowa standardowa lub na własnych warunkach i postanowieniach.

:::image type="content" source="./partner-center-portal/media/azure-create-container-offer-images/azure-create-1-standard-contract.png" alt-text="Ilustruje pole wyboru Using the umowa standardowa for Microsoft's commercial marketplace (Korzystanie z platformy handlowej firmy Microsoft).":::

##### <a name="your-own-terms-and-conditions"></a>Własne warunki i postanowienia

Aby podać własne niestandardowe warunki i postanowienia, wprowadź je w **polu Warunki i** postanowienia. W tym polu można wprowadzić nieograniczoną liczbę znaków tekstowych. Klienci muszą zaakceptować te warunki, aby wypróbować Twoją ofertę.

Wybierz **pozycję Zapisz projekt** przed kontynuowaniem do następnej sekcji Lista ofert.

## <a name="offer-listing"></a>Lista ofert

Ta strona umożliwia zdefiniowanie szczegółów oferty, które są wyświetlane na platformie handlowej. Obejmuje to nazwę oferty, opis i obrazy.

> [!NOTE]
> Szczegóły oferty nie muszą być w języku angielskim, jeśli opis oferty rozpoczyna się od frazy "Ta aplikacja jest dostępna tylko w języku innym niż angielski". Można również udostępnić przydatny link, aby zaoferować zawartość w języku innym niż używany w szczegółach oferty.

### <a name="name"></a>Nazwa

Nazwa w wprowadzana w tym miejscu jest wyświetlana jako tytuł oferty. To pole jest wstępnie wypełnione tekstem wprowadzonym w polu **Alias oferty** podczas tworzenia oferty. Tę nazwę można później zmienić.

Nazwa:

- Mogą być znakami towarowymi (i może zawierać symbole znaków towarowych i praw autorskich).
- Nie może mieć więcej niż 50 znaków.
- Nie może zawierać emoji.

### <a name="search-results-summary"></a>Podsumowanie wyników wyszukiwania

Krótki opis oferty. Może mieć do 100 znaków i jest używana w wynikach wyszukiwania na platformie handlowej.

### <a name="long-summary"></a>Długie podsumowanie

Bardziej szczegółowy opis oferty. Może mieć maksymalnie 256 znaków i jest używana w wynikach wyszukiwania na platformie handlowej.

### <a name="description"></a>Opis

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Long description-3](./includes/long-description-3.md)]

#### <a name="privacy-policy-link"></a>Link do zasad ochrony prywatności

Wprowadź adres internetowy zasad ochrony prywatności w organizacji. Odpowiadasz za zapewnienie, że Twoja oferta jest zgodna z przepisami i regulacjami dotyczącymi ochrony prywatności. Odpowiadasz również za publikowanie prawidłowych zasad ochrony prywatności w witrynie internetowej.

#### <a name="useful-links"></a>Przydatne łącza

Podaj uzupełniające dokumenty online dotyczące oferty. Można dodać maksymalnie 25 linków. Aby dodać link, wybierz **pozycję + Dodaj link, a** następnie wypełnij następujące pola:

- **Tytuł** — klienci zobaczą to na stronie szczegółów oferty.
- **Link (adres URL)** — wprowadź link dla klientów, aby wyświetlić dokument online. Link musi zaczynać się od http:// lub https://.

### <a name="contact-information"></a>Informacje kontaktowe

Należy podać nazwę, adres e-mail i numer telefonu osoby **kontaktowej** pomocy technicznej i **inżynieryjnej.** Te informacje nie są widoczne dla klientów, ale są dostępne dla firmy Microsoft. Można ją również dostarczać partnerom Dostawca rozwiązań w chmurze (CSP).

- Kontakt z pomocą techniczną (wymagane): w przypadku ogólnych pytań dotyczących pomocy technicznej.
- Kontakt inżynieryjny (wymagany): w przypadku pytań technicznych i problemów z certyfikacją.
- Kontakt z programem CSP (opcjonalnie): w przypadku pytań odsprzedawców związanych z programem CSP.

W sekcji **Kontakt z**  pomocą techniczną podaj witrynę sieci Web pomocy technicznej, w której partnerzy mogą znaleźć pomoc techniczną dla Twojej oferty w zależności od tego, czy oferta jest dostępna na globalnej platformie Azure, w Azure Government czy obu tych usługach.

W sekcji **kontaktowej programu CSP** podaj link (Materiały marketingowe programu **CSP),** gdzie partnerzy programu CSP mogą znaleźć materiały marketingowe dla Twojej oferty.

#### <a name="additional-marketplace-listing-resources"></a>Dodatkowe zasoby dotyczące ofert na platformie handlowej

Aby dowiedzieć się więcej na temat tworzenia ofert ofert, zobacz [Najlepsze rozwiązania dotyczące ofert](gtm-offer-listing-best-practices.md)

### <a name="marketplace-images"></a>Obrazy z witryny Marketplace

Podaj logo i obrazy do użycia z ofertą. Wszystkie obrazy muszą być w formacie PNG. Rozmyte obrazy zostaną odrzucone.

[!INCLUDE [logo tips](./includes/graphics-suggestions.md)]

>[!Note]
>Jeśli masz problem z przekazywaniem plików, upewnij się, że sieć lokalna nie blokuje usługi używanej przez program https://upload.xboxlive.com Partner Center.

#### <a name="store-logos"></a>Logo sklepu

Podaj plik PNG dla logo **o dużym** rozmiarze. Partner Center użyje tego do utworzenia logo **Small (Mały)** **i Medium (Średni).** Opcjonalnie możesz je później zastąpić różnymi obrazami.

- **Duże** (od 216 x 216 do 350 x 350 pikseli, wymagane)
- **Średni** (90 x 90 pikseli, opcjonalnie)
- **Mały** (48 x 48 pikseli, opcjonalnie)

Te logo są używane w różnych miejscach na liście:

[!INCLUDE [logos-azure-marketplace-only](./includes/logos-azure-marketplace-only.md)]

[!INCLUDE [Logo tips](./includes/graphics-suggestions.md)]

#### <a name="screenshots-optional"></a>Zrzuty ekranu (opcjonalnie)

Dodaj maksymalnie pięć zrzutów ekranu przedstawiających sposób działania oferty. Każdy z nich musi mieć rozmiar 1280 x 720 pikseli i format PNG.

#### <a name="videos-optional"></a>Wideo (opcjonalnie)

Dodaj maksymalnie pięć filmów wideo, które pokazują Twoją ofertę. Wprowadź nazwę wideo, jego adres internetowy i miniaturę obrazu PNG wideo o rozmiarze 1280 x 720 pikseli.

#### <a name="offer-examples"></a>Przykłady dotyczące ofert

Poniższe przykłady pokazują, jak pola oferty są wyświetlane w różnych miejscach oferty.

Zostanie **pokazana strona Oferty** w Azure Marketplace:

:::image type="content" source="./partner-center-portal/media/azure-create-container-offer-images/azure-create-6-offer-listing-mkt-plc.png" alt-text="Ilustruje stronę oferty w Azure Marketplace." :::

Wyniki wyszukiwania są w tym Azure Marketplace:

:::image type="content" source="./partner-center-portal/media/azure-create-container-offer-images/azure-create-7-search-results-mkt-plc.png" alt-text="Ilustruje wyniki wyszukiwania w Azure Marketplace.":::

Zostanie **pokazana strona Oferty** w Azure Portal:

:::image type="content" source="./partner-center-portal/media/azure-create-container-offer-images/azure-create-8-offer-listing-portal.png" alt-text="Ilustruje stronę oferty w Azure Portal.":::

Wyniki wyszukiwania są w Azure Portal:

:::image type="content" source="./partner-center-portal/media/azure-create-container-offer-images/azure-create-9-search-results-portal.png" alt-text="Ilustruje wyniki wyszukiwania w Azure Portal.":::

## <a name="preview"></a>Wersja zapoznawcza

Na karcie Wersja zapoznawcza możesz  wybrać ograniczoną grupę odbiorców podglądu do sprawdzania poprawności oferty przed opublikowaniem jej na żywo.

> [!IMPORTANT]
> Po wyświetleniu oferty w wersji **zapoznawczej** musisz wybrać pozycję Przejdź na **żywo,** aby opublikować ofertę publicznie.

Określ odbiorców wersji zapoznawczej przy użyciu identyfikatorów GUID subskrypcji platformy Azure wraz z opcjonalnym opisem każdego z nich. Żadne z tych pól nie jest widoczne dla klientów.

> [!NOTE]
> Identyfikator subskrypcji platformy Azure można znaleźć na stronie Subskrypcje w Azure Portal.

Dodaj co najmniej jeden identyfikator subskrypcji platformy Azure pojedynczo (maksymalnie 10) lub przez przekazanie pliku CSV (maksymalnie 100). Dodając te identyfikatory subskrypcji, można określić, kto może wyświetlać podgląd oferty przed jej opublikowaniem na żywo. Jeśli Twoja oferta jest już żywa, możesz wybrać odbiorców wersji zapoznawczej, aby przetestować zmiany lub aktualizacje oferty.

Wybierz **pozycję Zapisz roboczą** przed kontynuowaniem.

## <a name="plan-overview"></a>Omówienie planu

Ta karta umożliwia podanie różnych opcji planu w ramach tej samej oferty. Plany (wcześniej nazywane jednostkami SKU) mogą różnić się pod względem dostępnych chmur, takich jak chmury globalne, chmury dla instytucji rządowych i obraz, do których odwołuje się plan. Aby wyświetlić ofertę na platformie handlowej, musisz skonfigurować co najmniej jeden plan.

Dla każdej oferty można utworzyć maksymalnie 100 planów: maksymalnie 45 z nich może być prywatnych. Dowiedz się więcej o planach prywatnych w [ofertach prywatnych na platformie handlowej firmy Microsoft.](private-offers.md)

Po utworzeniu planów na **karcie Omówienie planu** są w tym celu:

- Nazwy planów
- Model cen
- Regiony platformy Azure (globalne lub rządowe)
- Bieżący stan publikowania
- Wszystkie dostępne akcje

Akcje dostępne w przeglądzie planu różnią się w zależności od bieżącego stanu planu. Obejmują one:

- **Usuń wersja robocza** — jeśli stan planu to Wersja robocza.
- **Zatrzymaj plan sprzedaży** — jeśli stan planu został opublikowany na żywo.

### <a name="create-new-plan"></a>Tworzenie nowego planu

Wybierz **pozycję Utwórz nowy plan.** Zostanie **wyświetlone okno dialogowe** Nowy plan.

W polu **Identyfikator planu** utwórz unikatowy identyfikator planu dla każdego planu w tej ofercie. Ten identyfikator będzie widoczny dla klientów w adresie internetowym produktu. Używaj tylko małych liter i cyfr, łączników lub podkreśleń oraz maksymalnie 50 znaków.

> [!NOTE]
> Nie można zmienić identyfikatora planu po wybraniu opcji **Utwórz**.

W **polu Nazwa planu** wprowadź nazwę tego planu. Klienci widzą tę nazwę podczas podejmowania decyzji o planie do wybrania w ramach oferty. Utwórz unikatową nazwę dla każdego planu w tej ofercie. Na przykład można użyć nazwy oferty **systemu Windows Server** z planami Windows Server **2016** i **Windows Server 2019.**

### <a name="plan-setup"></a>Planowanie konfiguracji

Ta karta umożliwia wybranie chmur, w których plan jest dostępny. Odpowiedzi na tej karcie wpływają na to, które pola są wyświetlane na innych kartach.

#### <a name="azure-regions"></a>Regiony świadczenia usługi Azure

Wszystkie plany ofert kontenerów platformy Azure są automatycznie udostępniane w **usłudze Azure Global**.  Twój plan może być używany przez klientów we wszystkich globalnych regionach platformy Azure, które korzystają z komercyjnej platformy handlowej. Aby uzyskać szczegółowe informacje, zobacz [Dostępność geograficzna i obsługa walut.](marketplace-geo-availability-currencies.md)

Wybierz [Azure Government,](../azure-government/documentation-government-welcome.md) aby twoje rozwiązanie było wyświetlane w tym miejscu. Jest to chmura społeczności instytucji rządowych z kontrolowanym dostępem dla klientów z federalnych, stanowych i lokalnych lub plemiennych agencji rządowych STANÓW Zjednoczonych, a także partnerów uprawnionych do ich obsługi. Jako wydawca ponosisz odpowiedzialność za wszelkie mechanizmy kontroli zgodności, środki zabezpieczeń i najlepsze rozwiązania dla tej społeczności chmury. Azure Government korzysta z fizycznie izolowanych centrów danych i sieci (znajdujących się tylko w Stanach Zjednoczonych). Przed [opublikowaniem](../azure-government/documentation-government-manage-marketplace-partners.md) Azure Government, przetestuj i potwierdź rozwiązanie w tym obszarze, ponieważ wyniki mogą się różnić. Aby utworzyć i przetestować rozwiązanie, zażądaj konta w wersji próbnej Microsoft Azure Government [wersji próbnej.](https://azure.microsoft.com/global-infrastructure/government/request/)

> [!NOTE]
> Po opublikowaniu i opublikowaniu planu w określonym regionie nie można usunąć tego regionu.

#### <a name="azure-government-certifications"></a>Azure Government certyfikacji

Ta opcja jest dostępna tylko w **przypadku** Azure Government w obszarze **regionów świadczenia usługi Azure.**

Azure Government obsługują dane, które podlegają określonym regulacjom i wymogom rządowym. Na przykład FedRAMP, NIST 800.171 (DIB), ITAR, IRS 1075, DoD L4 i CJIS.

Aby pokazać swoje certyfikaty dla tych programów, możesz podać maksymalnie 100 linków, które je opisują. Mogą to być linki do list w programie bezpośrednio lub do własnej witryny internetowej. Te linki są widoczne tylko dla Azure Government klientów.

### <a name="plan-listing"></a>Lista planów

Na tej karcie są wyświetlane konkretne informacje dotyczące poszczególnych planów w ramach bieżącej oferty.

### <a name="plan-name"></a>Nazwa planu

Ta nazwa jest wstępnie wypełniana nazwą nadaną planowi podczas jego tworzenia. Możesz zmienić tę nazwę zgodnie z potrzebami. Może mieć do 50 znaków. Ta nazwa jest wyświetlana jako tytuł tego planu w Azure Marketplace i Azure Portal. Jest on używany jako domyślna nazwa modułu po przygotowaniu planu do użycia.

### <a name="plan-summary"></a>Podsumowanie planu

Krótkie podsumowanie planu oprogramowania (nie oferty). To podsumowanie jest wyświetlane Azure Marketplace wynikach wyszukiwania i może zawierać do 100 znaków.

### <a name="plan-description"></a>Opis planu

Opisz, co sprawia, że ten plan oprogramowania jest unikatowy, a także różnice między planami w ramach oferty. Nie opisuj oferty, tylko planu. Ten opis będzie wyświetlany w Azure Marketplace i w Azure Portal na stronie **oferty.** Może to być ta sama zawartość, która jest dostarczana w podsumowaniu planu i może zawierać maksymalnie 2000 znaków.

Po **wypełnieniu** tych pól wybierz pozycję Zapisz.

#### <a name="plan-examples"></a>Przykłady planów

Poniższe przykłady pokazują, jak pola listy planów są wyświetlane w różnych widokach.

Poniżej znajdują się pola w Azure Marketplace podczas wyświetlania szczegółów planu:

:::image type="content" source="./partner-center-portal/media/azure-create-container-offer-images/azure-create-10-plan-details-mtplc.png" alt-text="Ilustruje pola wyświetlane podczas wyświetlania szczegółów planu w Azure Marketplace.":::

Poniżej znajdują się szczegółowe informacje o Azure Portal:

:::image type="content" source="./partner-center-portal/media/azure-create-container-offer-images/azure-create-11-plan-details-portal.png" alt-text="Ilustruje szczegóły planu na Azure Portal.":::

### <a name="plan-availability"></a>Dostępność planu

Jeśli chcesz ukryć opublikowaną ofertę, aby klienci nie mogą jej wyszukiwać, przeglądać ani kupować na platformie handlowej, zaznacz pole wyboru Ukryj **plan** na **karcie** Dostępność.

To pole jest używane w przypadku:

- Oferta jest przeznaczona do pośredniego korzystania z odwołania za pośrednictwem innej aplikacji.
- Oferty nie należy kupować indywidualnie.
- Plan został użyty do wstępnego testowania i nie jest już odpowiedni.
- Plan został użyty w przypadku ofert tymczasowych lub sezonowych i nie powinien być już oferowany.

## <a name="technical-configuration"></a>Konfiguracja techniczna

Obrazy kontenerów muszą być hostowane w prywatnym [Azure Container Registry](https://azure.microsoft.com/services/container-registry/). Na karcie **Konfiguracja techniczna** podaj informacje referencyjne dotyczące repozytorium obrazów kontenerów w Azure Container Registry.

Po opublikowaniu oferty obraz kontenera jest kopiowany do Azure Marketplace w określonym publicznym rejestrze kontenerów. Wszystkie żądania użycia obrazu kontenera są obsługiwane z Azure Marketplace publicznego rejestru kontenerów, a nie prywatnego. Aby uzyskać szczegółowe informacje, zobacz Prepare your Azure Container technical assets (Przygotowywanie [zasobów technicznych kontenera platformy Azure).](create-azure-container-technical-assets.md)

### <a name="image-repository-details"></a>Szczegóły repozytorium obrazów

Podaj następujące informacje na karcie **Szczegóły repozytorium** obrazów.

**Identyfikator subskrypcji platformy Azure** — podaj identyfikator subskrypcji, w której jest zgłaszane użycie, a usługi są rozliczane za Azure Container Registry, która zawiera obraz kontenera. Ten identyfikator można znaleźć na [stronie Subskrypcje w](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) Azure Portal.

**Nazwa grupy zasobów platformy Azure** — podaj nazwę [grupy](../azure-resource-manager/management/manage-resource-groups-portal.md) zasobów, która zawiera Azure Container Registry z obrazem kontenera. Grupa zasobów musi być dostępna w ramach identyfikatora subskrypcji (powyżej). Nazwę można znaleźć na stronie [Grupy zasobów](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceGroups) w Azure Portal.

**Azure Container Registry —** podaj nazwę [Azure Container Registry,](../container-registry/container-registry-intro.md) która zawiera obraz kontenera. Rejestr kontenerów musi znajdować się w podanej wcześniej grupie zasobów platformy Azure. Uwzględnij tylko nazwę rejestru, a nie pełną nazwę serwera logowania. Pamiętaj, aby **pominąć azurecr.io** w nazwie. Nazwę rejestru można znaleźć na stronie [Rejestry kontenerów w](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.ContainerRegistry%2Fregistries) Azure Portal.

**Nazwa użytkownika administratora dla Azure Container Registry** — podaj nazwę użytkownika administratora ) powiązaną z Azure Container Registry, która zawiera obraz kontenera. [](../container-registry/container-registry-authentication.md#admin-account) Nazwa użytkownika i hasło są wymagane, aby upewnić się, że firma ma dostęp do rejestru. Aby uzyskać nazwę użytkownika i  hasło administratora, ustaw właściwość z włączoną obsługą administratora na wartość **True** przy użyciu interfejsu wiersza polecenia Command-Line Platformy Azure. Opcjonalnie możesz ustawić opcję **Administrator na** wartość **Włącz w** Azure Portal.

 :::image type="content" source="./partner-center-portal/media/azure-create-container-offer-images/azure-create-12-update-container-registry-edit.png" alt-text="Ilustruje okno dialogowe Aktualizowanie rejestru kontenerów.":::

**Hasło dla Azure Container Registry** — podaj hasło dla nazwy użytkownika administratora skojarzonej z Azure Container Registry i zawiera obraz kontenera. Nazwa użytkownika i hasło są wymagane, aby upewnić się, że firma ma dostęp do rejestru. Hasło można uzyskać z witryny Azure Portal, przechodząc do Container Registry Access Keys lub za pomocą interfejsu wiersza polecenia platformy Azure za  >   pomocą [polecenia show](/cli/azure/acr/credential#az_acr_credential_show).

:::image type="content" source="./partner-center-portal/media/azure-create-container-offer-images/azure-create-13-access-keys.png" alt-text="Ilustruje menu Klucz dostępu.":::

**Nazwa repozytorium w Azure Container Registry**. Podaj nazwę repozytorium Azure Container Registry, w których znajduje się obraz. Dołącz nazwę repozytorium podczas wypychania obrazu do rejestru. Nazwę repozytorium można znaleźć, przechodząc do strony [Container Registry](https://azure.microsoft.com/services/container-registry/)  >  **Repozytoria.** Aby uzyskać więcej informacji, zobacz [Wyświetlanie repozytoriów rejestru kontenerów w Azure Portal](../container-registry/container-registry-repositories.md).

> [!NOTE]
> Po skonfigurowaniu nazwy nie można jej zmienić. Użyj unikatowej nazwy dla każdej oferty na swoim koncie.

### <a name="image-tags-for-new-versions-of-your-offer"></a>Tagi obrazów dla nowych wersji oferty

Klienci muszą mieć możliwość automatycznego uzyskania aktualizacji z Azure Marketplace podczas publikowania aktualizacji. Jeśli użytkownik nie chce aktualizować obrazu, musi mieć możliwość pozostania w określonej wersji obrazu. Możesz to zrobić, dodając nowe tagi obrazu za każdym razem, gdy aktualizujesz obraz.

### <a name="image-tag"></a>Tag obrazu

To pole musi zawierać tag **latest,** który wskazuje najnowszą wersję obrazu na wszystkich obsługiwanych platformach. Musi również zawierać tag wersji (na przykład rozpoczynający się od xx.xx.xx, gdzie xx to liczba). Klienci powinni używać [tagów manifestu do](https://github.com/estesp/manifest-tool) wielu platform docelowych. Należy również dodać wszystkie tagi, do których odwołuje się tag manifestu, aby można było je przekazać.

Wszystkie tagi manifestu (z wyjątkiem tagu latest) muszą zaczynać się od X.Y lub **-** X.Y.Z, gdzie X, Y i Z są liczbami całkowitymi. Jeśli na przykład najnowszy **tag** wskazuje na 1.0.1-linux-x64, 1.0.1-linux-arm32 i 1.0.1-windows-arm32, te sześć tagów należy dodać do tego pola. Aby uzyskać szczegółowe informacje, zobacz Prepare your Azure Container technical assets (Przygotowywanie [zasobów technicznych kontenera platformy Azure).](create-azure-container-technical-assets.md)

> [!NOTE]
> Pamiętaj, aby dodać tag testu do obrazu, aby można było zidentyfikować obraz podczas testowania.

## <a name="review-and-publish"></a>Przeglądanie i publikowanie

Po ukończeniu wszystkich wymaganych sekcji oferty możesz przesłać ją do przejrzenia i opublikowania.

W prawym górnym rogu portalu wybierz pozycję Przejrzyj **i opublikuj.** 

Na stronie przeglądu można:

- Sprawdź stan ukończenia dla każdej sekcji oferty. Nie można publikować, dopóki wszystkie sekcje oferty nie zostaną oznaczone jako ukończone.
  - **Nie uruchomiono** — nie rozpoczęto i należy je ukończyć.
  - **Niekompletne** — zawiera błędy, które należy naprawić lub które wymagają podania dodatkowych informacji. Aby uzyskać pomoc, zapoznaj się z sekcjami we wcześniejszej części tego dokumentu.
  - **Complete** — zawiera wszystkie wymagane dane bez błędów. Wszystkie sekcje oferty muszą zostać ukończone, zanim będzie można przesłać ofertę.
- Udostępnij zespołowi certyfikacji instrukcje dotyczące testowania, aby upewnić się, że oferta jest poprawnie testowana. Ponadto podaj wszelkie uwagi uzupełniające, które są przydatne do zrozumienia Oferty.

Aby przesłać ofertę do opublikowania, wybierz pozycję **Opublikuj.**

Wyślemy Ci wiadomość e-mail z powiadomieniem o tym, kiedy dostępna jest wersja zapoznawcza oferty do przejrzenia i zatwierdzenia.

Aby opublikować ofertę publicznie, przejdź do Partner Center i wybierz **pozycję Przejdź na żywo.**

## <a name="next-step"></a>Następny krok

- [Aktualizowanie istniejącej oferty na platformie handlowej](./partner-center-portal/update-existing-offer.md)