---
title: Tworzenie oferty Azure IoT Edge modułu za pomocą Partner Center w Azure Marketplace
description: Dowiedz się, jak tworzyć, konfigurować i publikować IoT Edge modułu w Azure Marketplace użyciu Partner Center.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: keferna
ms.author: keferna
ms.date: 08/07/2020
ms.openlocfilehash: 12600cadaa84ae116818eec06459d5db0c05304a
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773424"
---
# <a name="create-an-iot-edge-module-offer"></a>Tworzenie oferty modułu usługi IoT Edge

W tym artykule opisano sposób tworzenia i publikowania Internet rzeczy (IoT) Edge dla Azure Marketplace. Przed rozpoczęciem utwórz [konto komercyjnej](../create-account.md) platformy handlowej w Partner Center, jeśli jeszcze tego nie zrobiono. Upewnij się, że Twoje konto jest zarejestrowane w programie platformy handlowej.

## <a name="create-a-new-offer"></a>Tworzenie nowej oferty

1. Zaloguj się do [Partner Center](https://partner.microsoft.com/dashboard/home).
2. W menu nav po lewej stronie wybierz pozycję **Omówienie komercyjnej**  >  **platformy handlowej.**
3. Na stronie Przegląd wybierz pozycję **+ Nowa oferta** IoT Edge  >  **modułu**.

    ![Ilustruje menu nawigacji po lewej stronie.](./media/new-offer-iot-edge.png)

> [!IMPORTANT]
> Po opublikowaniu oferty zmiany wprowadzone w tej Partner Center są wyświetlane w sklepach online dopiero po jej opublikowaniu. Pamiętaj, aby zawsze ponownie opublikować po wrzucie zmian.

### <a name="offer-id-and-alias"></a>Identyfikator oferty i alias

Wprowadź identyfikator **oferty**. Jest to unikatowy identyfikator dla każdej oferty na Twoim koncie.

- Ten identyfikator jest widoczny dla klientów w adresie internetowym oferty marketplace i Azure Resource Manager, jeśli ma to zastosowanie.
- Użyj tylko małych liter i cyfr. Może zawierać łączniki i podkreślenia, ale bez spacji i może zawierać do 50 znaków. Jeśli na przykład **wpiszemy wartość test-offer-1,** adres internetowy oferty będzie mieć wartość `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` .
- Nie można zmienić identyfikatora oferty po wybraniu opcji **Utwórz.**

Wprowadź alias **oferty**. Jest to nazwa używana dla oferty w Partner Center.

- Ta nazwa nie jest używana na platformie handlowej i różni się od nazwy oferty i innych wartości wyświetlanych klientom.
- Nie można tego zmienić po wybraniu opcji **Utwórz**.

Wybierz **pozycję Utwórz,** aby wygenerować ofertę i kontynuować.

## <a name="offer-overview"></a>Omówienie oferty

Strona **Omówienie oferty** przedstawia wizualną reprezentację kroków wymaganych do opublikowania tej oferty (zarówno ukończonych, jak i nadchodzących) oraz czasu ukończenia każdego kroku.

Ta strona zawiera linki do wykonywania operacji na tej ofercie na podstawie wybranego wyboru. Na przykład:

- Jeśli oferta jest w wersji roboczej — usuń ofertę wersji roboczej
- Jeśli oferta jest żywa — [zatrzymaj sprzedaż oferty](update-existing-offer.md#stop-selling-an-offer-or-plan)
- Jeśli oferta jest w wersji zapoznawczej — [przejdź na żywo](../review-publish-offer.md#previewing-and-approving-your-offer)
- Jeśli wylogowanie wydawcy nie zostało ukończone — anuluj [publikowanie.](../review-publish-offer.md#cancel-publishing)

## <a name="offer-setup"></a>Konfiguracja oferty

Wykonaj następujące kroki, aby skonfigurować ofertę.

### <a name="customer-leads"></a>Potencjalni klienci

Podczas publikowania oferty na platformie handlowej za Partner Center można opcjonalnie połączyć ją z systemem zarządzania relacjami z klientami (CRM). Umożliwia to otrzymywanie informacji kontaktowych klienta, gdy tylko ktoś wyrazi zainteresowanie Twoim produktem lub użyje go.

1. Wybierz miejsce docelowe potencjalnych klientów, do którego chcesz wysyłać potencjalnych klientów. Partner Center obsługuje następujące systemy CRM:

    - [Dynamics 365](commercial-marketplace-lead-management-instructions-dynamics.md) for Customer Engagement
    - [Marketo](commercial-marketplace-lead-management-instructions-marketo.md)
    - [Salesforce](commercial-marketplace-lead-management-instructions-salesforce.md)

    > [!NOTE]
    > Jeśli twojego systemu CRM nie ma na powyższej liście, użyj usługi [Azure Table lub](commercial-marketplace-lead-management-instructions-azure-table.md) Https [Endpoint](commercial-marketplace-lead-management-instructions-https.md) do przechowywania danych potencjalnych klientów, a następnie wyeksportuj dane do systemu CRM.

2. Połącz ofertę z miejscem docelowym potencjalnych klienta podczas publikowania w Partner Center.
3. Upewnij się, że połączenie z miejscem docelowym potencjalnych klienta jest prawidłowo skonfigurowane. Po opublikowaniu go w Partner Center zweryfikujemy połączenie i wyślemy do Ciebie testowego potencjalnego klienta. Podczas wyświetlania podglądu oferty przed jej rozpoczęciem możesz również przetestować połączenie potencjalnych klienta, próbując kupić ofertę samodzielnie w środowisku w wersji zapoznawczej.
4. Upewnij się, że połączenie z miejscem docelowym potencjalnych klientów pozostaje zaktualizowane, aby nie utracić żadnych potencjalnych klientów.

Oto kilka dodatkowych zasobów do zarządzania potencjalnymi klientami:

- [Potencjalni klienci z oferty na platformie handlowej](commercial-marketplace-get-customer-leads.md)
- [Często zadawane pytania dotyczące zarządzania potencjalnymi klientami](../lead-management-faq.md#common-questions-about-lead-management)
- [Rozwiązywanie problemów z błędami konfiguracji potencjalnych klienta](../lead-management-faq.md#publishing-config-errors)
- [Omówienie zarządzania potencjalnymi klientami](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf) PDF (Upewnij się, że blokowanie wyskakujących okienek jest wyłączone).

Przed **kontynuowaniem wybierz pozycję Zapisz** wersje robocze.

### <a name="properties"></a>Właściwości

Ta strona umożliwia zdefiniowanie kategorii używanych do grupowania oferty na platformie handlowej oraz umów prawnych, które obsługują ofertę.

#### <a name="category"></a>Kategoria

Wybierz kategorie i podkategorie, aby umieścić swoją ofertę w odpowiednich obszarach wyszukiwania na platformie handlowej. Pamiętaj, aby w opisie oferty opisać, jak Twoja oferta obsługuje te kategorie. Wybierz pozycję:

- Co najmniej jedna i maksymalnie dwie kategorie, w tym kategoria podstawowa i pomocnicza (opcjonalnie).
- Maksymalnie dwie podkategorie dla każdej kategorii podstawowej i/lub pomocniczej. Jeśli żadna podkategoria nie ma zastosowania do Twojej oferty, wybierz **pozycję Nie dotyczy.**

Zobacz pełną listę kategorii i podkategorii w [tece Najlepsze rozwiązania dotyczące ofert.](../gtm-offer-listing-best-practices.md) W witrynie Marketplace moduły IoT Edge są zawsze wyświetlane w  **kategorii Internet rzeczy**  >  **IoT Edge modułu.**  

#### <a name="legal"></a>Informacje prawne

Musisz podać warunki i postanowienia dotyczące oferty. Dostępne są dwie opcje:

- Skorzystaj z umowa standardowa dla komercyjnej platformy handlowej firmy Microsoft.
- Podaj własne warunki i postanowienia.

##### <a name="standard-contract-for-the-microsoft-commercial-marketplace"></a>Umowa standardowa dla komercyjnej platformy handlowej firmy Microsoft

Oferujemy szablon umowa standardowa ułatwiający transakcje na platformie handlowej. Możesz zaoferować swoje rozwiązanie w ramach umowa standardowa, które klienci muszą sprawdzić i zaakceptować tylko raz. Jest to dobra opcja, jeśli nie chcesz tworzyć niestandardowych warunków i postanowień.

Aby dowiedzieć się więcej na temat umowa standardowa, zobacz umowa standardowa for the Microsoft Commercial Marketplace (Platforma [handlowa firmy Microsoft).](../standard-contract.md) Możesz również pobrać plik [PDF umowa standardowa](https://go.microsoft.com/fwlink/?linkid=2041178) (upewnij się, że blokowanie wyskakujących okienek jest wyłączone).

Aby użyć umowa standardowa, zaznacz pole wyboru Użyj umowa standardowa dla komercyjnej platformy handlowej firmy **Microsoft,** a następnie kliknij przycisk **Akceptuj.**

> [!NOTE]
> Po opublikowaniu oferty przy użyciu umowy standardowej dla platformy handlowej firmy Microsoft nie można używać własnych niestandardowych warunków i postanowień. Możesz zaoferować rozwiązanie zgodnie z umowa standardowa lub na własnych warunkach i postanowieniach.

![Ilustruje użycie umowa standardowa dla platformy handlowej firmy Microsoft pole wyboru.](media//iot-edge-module-standard-contract-checkbox.png)

##### <a name="your-own-terms-and-conditions"></a>Własne warunki i postanowienia

Aby podać własne niestandardowe warunki i postanowienia, wprowadź je w **polu Warunki i** postanowienia. W tym polu można wprowadzić nieograniczoną liczbę znaków tekstowych. Klienci muszą zaakceptować te warunki, aby wypróbować Twoją ofertę.

Wybierz **pozycję Zapisz projekt** przed kontynuowaniem do następnej sekcji Lista ofert.

## <a name="offer-listing"></a>Lista ofert

W tym miejscu zdefiniujesz szczegóły oferty, które są wyświetlane na platformie handlowej. Obejmuje to nazwę oferty, opis, obrazy i tak dalej. Pamiętaj, aby podczas konfigurowania tej oferty postępować zgodnie z zasadami szczegółowymi na stronie zasad firmy Microsoft.

> [!NOTE]
> Szczegóły oferty nie muszą być w języku angielskim, jeśli opis oferty rozpoczyna się od frazy "Ta aplikacja jest dostępna tylko w języku innym niż angielski". Można również udostępnić przydatny link, aby zaoferować zawartość w języku innym niż używany w szczegółach oferty.

### <a name="name"></a>Nazwa

Nazwa w wprowadzana w tym miejscu jest wyświetlana jako tytuł oferty. To pole jest wstępnie wypełnione tekstem wprowadzonym w polu **Alias oferty** podczas tworzenia oferty. Tę nazwę można później zmienić.

Nazwa:

- Może być znakami towarowymi (i może zawierać symbole znaków towarowych lub praw autorskich).
- Nie może mieć więcej niż 50 znaków.
- Nie może zawierać emoji.

### <a name="search-results-summary"></a>Podsumowanie wyników wyszukiwania

Podaj krótki opis oferty. Może mieć do 100 znaków i jest używana w wynikach wyszukiwania na platformie handlowej.

### <a name="long-summary"></a>Długie podsumowanie

Podaj bardziej szczegółowy opis oferty. Może mieć maksymalnie 256 znaków i jest używana w wynikach wyszukiwania na platformie handlowej.

### <a name="description"></a>Opis

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

IoT Edge modułu muszą zawierać akapit minimalnych wymagań sprzętowych w dolnej części opisu, taki jak:

- Minimalne wymagania sprzętowe: system operacyjny Linux x64 i arm32, 1 GB pamięci RAM, 500 MB miejsca do magazynowania

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Long description-3](./includes/long-description-3.md)]

#### <a name="privacy-policy-url"></a>Adres URL zasad ochrony prywatności

Wprowadź adres internetowy zasad ochrony prywatności w organizacji. Odpowiadasz za zapewnienie, że Twoja oferta jest zgodna z przepisami i regulacjami dotyczącymi ochrony prywatności. Odpowiadasz również za publikowanie prawidłowych zasad ochrony prywatności w witrynie internetowej.

#### <a name="useful-links"></a>Przydatne łącza

Podaj uzupełniające dokumenty online dotyczące oferty. Można dodać maksymalnie 25 linków. Aby dodać link, wybierz **pozycję + Dodaj link, a** następnie wypełnij następujące pola:

- **Tytuł** — klienci będą widzieć tytuł na stronie szczegółów oferty.
- **Link (adres URL)** — wprowadź link dla klientów, aby wyświetlić dokument online. Link musi zaczynać się od `http://` lub `https://` .

Pamiętaj o dodaniu co najmniej jednego linku do dokumentacji i jednego linku do zgodnych urządzeń IoT Edge z katalogu urządzeń [Usługi Azure IoT.](https://devicecatalog.azure.com/)

### <a name="contact-information"></a>Informacje kontaktowe

Należy podać nazwę, adres e-mail i numer telefonu osoby kontaktowej pomocy **technicznej** i **inżynieryjnej.** Te informacje nie są widoczne dla klientów. Jest ona dostępna dla firmy Microsoft i może być dostarczana Dostawca rozwiązań w chmurze (CSP).

- Kontakt z pomocą techniczną (wymagane): w przypadku ogólnych pytań pomocy technicznej.
- Kontakt inżynieryjny (wymagany): w przypadku pytań technicznych i problemów z certyfikacją.
- Kontakt z programem CSP (opcjonalnie): w przypadku pytań odsprzedawców związanych z programem CSP.

W sekcji **Kontakt z** pomocą techniczną  podaj adres internetowy witryny sieci Web pomocy technicznej, pod którym partnerzy mogą znaleźć pomoc techniczną dla Twojej oferty w zależności od tego, czy oferta jest dostępna na globalnej platformie Azure, w Azure Government czy obu tych usługach.

W sekcji **kontaktowej programu CSP** podaj link **(Materiały marketingowe programu CSP),** gdzie partnerzy programu CSP mogą znaleźć materiały marketingowe dla Twojej oferty.

#### <a name="additional-marketplace-listing-resources"></a>Dodatkowe zasoby dotyczące listy na platformie handlowej

Aby dowiedzieć się więcej na temat tworzenia ofert, zobacz Najlepsze rozwiązania dotyczące [ofert.](../gtm-offer-listing-best-practices.md)

### <a name="marketplace-images"></a>Obrazy z witryny Marketplace

Podaj logo i obrazy do użycia z ofertą. Wszystkie obrazy muszą być w formacie PNG. Rozmyte obrazy zostaną odrzucone.

[!INCLUDE [logo tips](../includes/graphics-suggestions.md)]

>[!Note]
>Jeśli masz problem z przekazywaniem plików, upewnij się, że sieć lokalna nie blokuje usługi używanej przez https://upload.xboxlive.com Partner Center.

#### <a name="store-logos"></a>Logo sklepu

Podaj plik PNG dla logo **o dużym** rozmiarze. Partner Center użyje tego do utworzenia logo **Small (Mały)** **i Medium (Średni).** Opcjonalnie możesz zastąpić je różnymi obrazami później.

- **Duże** (od 216 x 216 do 350 x 350 pikseli, wymagane)
- **Średni** (90 x 90 pikseli, opcjonalnie)
- **Mały** (48 x 48 pikseli, opcjonalnie)

Te logo są używane w różnych miejscach na liście:

[!INCLUDE [logos-azure-marketplace-only](../includes/logos-azure-marketplace-only.md)]

[!INCLUDE [Logo tips](../includes/graphics-suggestions.md)]

#### <a name="screenshots-optional"></a>Zrzuty ekranu (opcjonalnie)

Dodaj do pięciu zrzutów ekranu przedstawiających sposób działania oferty. Każdy z nich musi mieć rozmiar 1280 x 720 pikseli i mieć format PNG.

#### <a name="videos-optional"></a>Wideo (opcjonalnie)

Dodaj maksymalnie pięć filmów wideo, które pokazują Twoją ofertę. Wprowadź nazwę wideo, jego adres internetowy i miniaturę obrazu PNG wideo o rozmiarze 1280 x 720 pikseli.

#### <a name="marketplace--examples"></a>Przykłady z witryny Marketplace

Oto przykład sposobu, w jaki informacje o ofercie są wyświetlane w Azure Marketplace:

:::image type="content" source="media/example-iot-azure-marketplace-offer.png" alt-text="Ilustruje, jak ta oferta pojawia się w Azure Marketplace.":::

#### <a name="call-out-descriptions"></a>Opisy wywołań

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

<br>Oto przykład sposobu, w jaki informacje o ofercie są wyświetlane Azure Marketplace wynikach wyszukiwania:

:::image type="content" source="media/example-iot-azure-marketplace-offer-search-results.png" alt-text="Ilustruje, jak ta oferta jest wyświetlana Azure Marketplace wynikach wyszukiwania.":::

#### <a name="call-out-descriptions"></a>Opisy wywołań

1. Małe logo
2. Nazwa oferty
3. Podsumowanie wyników wyszukiwania

<br>Oto przykład sposobu, w jaki informacje o ofercie są wyświetlane w Azure Portal:

:::image type="content" source="media/example-iot-azure-portal-offer.png" alt-text="Ilustruje, jak ta oferta pojawia się w Azure Portal.":::

#### <a name="call-out-descriptions"></a>Opisy wywołań

1. Nazwa
2. Opis
3. Przydatne łącza
4. Zrzuty ekranu

<br>Oto przykład sposobu, w jaki informacje o ofercie są wyświetlane w wynikach Azure Portal wyszukiwania:

:::image type="content" source="media/example-iot-azure-portal-offer-search-results.png" alt-text="Ilustruje sposób, w jaki ta oferta jest wyświetlana Azure Portal wynikach wyszukiwania.":::

#### <a name="call-out-descriptions"></a>Opisy wywołań

1. Małe logo
2. Nazwa oferty
3. Podsumowanie wyników wyszukiwania

<br>Wybierz **pozycję Zapisz roboczą** przed przystąpieniem do następnej sekcji Podgląd.

## <a name="preview"></a>Wersja zapoznawcza

Na karcie **Wersja zapoznawcza** możesz  wybrać ograniczoną grupę odbiorców podglądu, aby zweryfikować ofertę przed opublikowaniem jej na żywo wśród szerszej grupy odbiorców na platformie handlowej.

> [!IMPORTANT]
> Po wyświetleniu oferty w wersji zapoznawczej musisz wybrać pozycję Przejdź na **żywo,** aby opublikować ofertę publicznie.

Określ odbiorców wersji zapoznawczej przy użyciu identyfikatorów GUID subskrypcji platformy Azure wraz z opcjonalnym opisem każdego z nich. Żadne z tych pól nie jest widoczne dla klientów.

> [!NOTE]
> Identyfikator subskrypcji platformy Azure można znaleźć na stronie Subskrypcje w Azure Portal.

Dodaj co najmniej jeden identyfikator subskrypcji platformy Azure pojedynczo (maksymalnie 10) lub przez przekazanie pliku CSV (maksymalnie 100). Dodając te identyfikatory subskrypcji, możesz zdefiniować, kto może wyświetlać podgląd oferty przed jej opublikowaniem na żywo. Jeśli Twoja oferta jest już żywa, możesz zdefiniować odbiorców wersji zapoznawczej, aby przetestować zmiany lub aktualizacje oferty.

Wybierz **pozycję Zapisz projekt** przed przystąpieniem do następnej sekcji Omówienie planu.

## <a name="plan-overview"></a>Omówienie planu

Ta karta umożliwia podanie różnych opcji planu w ramach tej samej oferty w Partner Center. Plany (wcześniej nazywane jednostkami SKU) mogą się różnić pod względem dostępnych chmur, takich jak chmury globalne, chmury dla instytucji rządowych i obrazy, do których odwołuje się plan. Aby wyświetlić ofertę na platformie handlowej, musisz skonfigurować co najmniej jeden plan.

Dla każdej oferty można utworzyć maksymalnie 100 planów: maksymalnie 45 z nich może być prywatnych. Dowiedz się więcej o planach prywatnych w [ofertach prywatnych na platformie handlowej firmy Microsoft.](../private-offers.md)

Po utworzeniu planów na karcie **Omówienie planu** są w tym obszarze:

- Nazwy planów
- Model cen
- Regiony platformy Azure (globalne lub rządowe)
- Bieżący stan publikowania
- Wszystkie dostępne akcje

Akcje dostępne w przeglądzie planu różnią się w zależności od bieżącego stanu planu. Obejmują one:

- **Usuń projekt:** jeśli stanem planu jest Wersja robocza.
- **Zatrzymaj plan sprzedaży:** jeśli stan planu jest opublikowany na żywo.

### <a name="create-new-plan"></a>Tworzenie nowego planu

Wybierz **pozycję Utwórz nowy plan.** Zostanie **wyświetlone okno dialogowe** Nowy plan.

W polu **Identyfikator planu** utwórz unikatowy identyfikator planu dla każdego planu w tej ofercie. Ten identyfikator będzie widoczny dla klientów w adresie internetowym produktu. Używaj tylko małych liter i cyfr, łączników lub podkreśleń oraz maksymalnie 50 znaków.

W **polu Nazwa planu** wprowadź nazwę tego planu. Klienci widzą tę nazwę podczas podejmowania decyzji o planie do wybrania w ramach oferty. Utwórz unikatową nazwę dla każdego planu w tej ofercie. Na przykład można użyć nazwy oferty **systemu Windows Server** z planami Windows Server **2016** i **Windows Server 2019.**

> [!NOTE]
> Nie można zmienić identyfikatora planu po wybraniu opcji **Utwórz**.

Wybierz przycisk **Utwórz**.

### <a name="plan-setup"></a>Planowanie konfiguracji

Ta karta umożliwia skonfigurowanie chmur, w których plan jest dostępny. Odpowiedzi na tej karcie wpływają na to, które pola są wyświetlane na innych kartach.

#### <a name="azure-regions"></a>Regiony świadczenia usługi Azure

Wszystkie plany dotyczące IoT Edge modułów są automatycznie udostępniane w globalnej wersji **platformy Azure.**  Twój plan może być używany przez klientów we wszystkich globalnych regionach świadczenia usługi Azure, które korzystają z witryny Marketplace. Aby uzyskać szczegółowe informacje, zobacz [Dostępność geograficzna i obsługa walut.](../marketplace-geo-availability-currencies.md)

Wybierz [Azure Government,](../../azure-government/documentation-government-welcome.md) aby twoje rozwiązanie było wyświetlane w tym miejscu. Jest to chmura społeczności instytucji rządowych z kontrolowanym dostępem dla klientów z federalnych, stanowych i lokalnych lub plemiennych agencji rządowych STANÓW Zjednoczonych, a także partnerów uprawnionych do ich obsługi. Jako wydawca ponosisz odpowiedzialność za wszelkie mechanizmy kontroli zgodności, środki zabezpieczeń i najlepsze rozwiązania dla tej społeczności chmury. Azure Government korzysta z fizycznie izolowanych centrów danych i sieci (znajdujących się tylko w Stanach Zjednoczonych). Przed [opublikowaniem](../../azure-government/documentation-government-manage-marketplace-partners.md) Azure Government, przetestuj i potwierdź rozwiązanie w tym obszarze, ponieważ wyniki mogą się różnić. Aby schować i przetestować rozwiązanie, zażądaj konta w wersji próbnej na stronie [Microsoft Azure Government wersji próbnej.](https://azure.microsoft.com/global-infrastructure/government/request/)

> [!NOTE]
> Po opublikowaniu planu i jego opublikowaniu w określonym regionie nie można usunąć tego regionu.

#### <a name="azure-government-certifications"></a>Azure Government certyfikacji

Ta opcja jest widoczna tylko w **przypadku Azure Government** w obszarze **regionów świadczenia usługi Azure.**

Azure Government obsługują dane, które podlegają określonym regulacjom i wymogom rządowym. Na przykład FedRAMP, NIST 800.171 (DIB), ITAR, IRS 1075, DoD L4 i CJIS. Aby zwiększyć świadomość swoich certyfikatów dla tych programów, możesz udostępnić maksymalnie 100 linków opisujących Certyfikaty. Mogą to być linki do ofert w programie bezpośrednio lub do własnej witryny internetowej. Te linki są widoczne tylko dla Azure Government klientów.

### <a name="plan-listing"></a>Lista planów

Na tej karcie są wyświetlane konkretne informacje dotyczące poszczególnych planów w ramach tej samej oferty.

### <a name="plan-name"></a>Nazwa planu

Ta nazwa jest wstępnie wypełniana nazwą naaną planowi podczas jego tworzenia. W razie potrzeby możesz zmienić tę nazwę. Może mieć maksymalnie 50 znaków. Ta nazwa jest wyświetlana jako tytuł tego planu w Azure Marketplace i Azure Portal. Jest on używany jako domyślna nazwa modułu po przygotowaniu planu do użycia.

### <a name="plan-summary"></a>Podsumowanie planu

Podaj krótkie podsumowanie planu (nie ofertę). To podsumowanie jest wyświetlane Azure Marketplace wynikach wyszukiwania i może zawierać maksymalnie 100 znaków.

### <a name="plan-description"></a>Opis planu

Opisz, co sprawia, że ten plan jest unikatowy, a także różnice między planami w ramach oferty. Nie opisuj oferty, tylko planu. Ten opis będzie wyświetlany w Azure Marketplace i w Azure Portal na stronie oferty. Może to być ta sama zawartość, która znajduje się w podsumowaniu planu i może zawierać maksymalnie 2000 znaków.

Po **wypełnieniu tych** pól wybierz pozycję Zapisz wersje robocze.

#### <a name="plan-examples"></a>Przykłady planów

Oto przykład szczegółów planu Azure Marketplace (wszystkie wymienione ceny są przeznaczone tylko do celów przykładowych i nie mają odzwierciedlać rzeczywistych kosztów):

:::image type="content" source="media/example-iot-azure-marketplace-plan.png" alt-text="Ilustruje Azure Marketplace planu.":::

#### <a name="call-out-descriptions"></a>Opisy wywołań

1. Nazwa oferty
2. Nazwa planu
3. Opis planu

<br>Oto przykład szczegółów planu Azure Portal (wszystkie wymienione ceny są przeznaczone tylko do celów przykładowych i nie mają odzwierciedlać rzeczywistych kosztów):

:::image type="content" source="media/example-iot-azure-marketplace-plan-details.png" alt-text="Ilustruje szczegóły Azure Portal planu.":::

#### <a name="call-out-descriptions"></a>Opisy wywołań

1. Nazwa oferty
2. Nazwa planu
3. Opis planu

### <a name="availability"></a>Dostępność

Jeśli chcesz ukryć opublikowaną ofertę, aby klienci nie mogą jej wyszukiwać, przeglądać ani kupować na platformie handlowej, zaznacz pole wyboru Ukryj **plan** na karcie Dostępność.

To pole jest często używane w przypadku:

- Oferta ma być używana tylko pośrednio, gdy odwołuje się do innej aplikacji.
- Oferty nie należy kupować indywidualnie.
- Plan został użyty do wstępnego testowania i nie jest już odpowiedni.
- Plan został użyty w przypadku ofert tymczasowych lub sezonowych i nie powinien być już oferowany.

## <a name="technical-configuration"></a>Konfiguracja techniczna

Typ **IoT Edge oferty modułu** jest określonym typem kontenera, który jest uruchamiany na IoT Edge urządzeniu. Na karcie **Konfiguracja** techniczna należy podać informacje referencyjne dotyczące repozytorium obrazów kontenerów w repozytorium [Azure Container Registry](https://azure.microsoft.com/services/container-registry/)wraz z ustawieniami konfiguracji, które umożliwiają klientom łatwe korzystanie z modułu.

Po opublikowaniu oferty obraz IoT Edge kontenera zostanie skopiowany do Azure Marketplace w określonym publicznym rejestrze kontenerów. Wszystkie żądania użytkowników platformy Azure dotyczące korzystania z modułu są obsługiwane Azure Marketplace publicznego rejestru kontenerów, a nie prywatnego rejestru kontenerów.

Można określić wiele platform docelowych i udostępnić kilka wersji obrazu kontenera modułu przy użyciu tagów. Aby dowiedzieć się więcej na temat tagów i wersji, zobacz [Przygotowanie zasobów technicznych IoT Edge modułu .](create-iot-edge-module-asset.md)

### <a name="image-repository-details"></a>Szczegóły repozytorium obrazów

Na karcie Szczegóły repozytorium obrazów należy podać **następujące** informacje.

**Wybierz źródło obrazu:** **wybierz** Azure Container Registry obrazu.

**Identyfikator subskrypcji platformy Azure:** podaj identyfikator subskrypcji, w której jest zgłaszane użycie zasobów, a usługi są rozliczane za Azure Container Registry który zawiera obraz kontenera. Ten identyfikator można znaleźć na [stronie Subskrypcje w](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) Azure Portal.

**Nazwa grupy zasobów platformy Azure:** podaj [nazwę grupy](../../azure-resource-manager/management/manage-resource-groups-portal.md) zasobów, która zawiera Azure Container Registry z obrazem kontenera. Grupa zasobów musi być dostępna w ramach identyfikatora subskrypcji (powyżej). Nazwę można znaleźć na stronie [Grupy zasobów](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceGroups) w Azure Portal.

**Nazwa rejestru kontenerów platformy Azure:** podaj nazwę [Azure Container Registry,](../../container-registry/container-registry-intro.md) która zawiera obraz kontenera. Rejestr kontenerów musi znajdować się w podanej wcześniej grupie zasobów platformy Azure. Podaj tylko nazwę rejestru, a nie pełną nazwę serwera logowania. Pamiętaj, aby **pominąć azurecr.io** w nazwie. Nazwę rejestru można znaleźć na stronie [Rejestry kontenerów w](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.ContainerRegistry%2Fregistries) Azure Portal.

**Nazwa użytkownika administratora dla Azure Container Registry:** Podaj nazwę użytkownika [administratora](../../container-registry/container-registry-authentication.md#admin-account)) skojarzoną z Azure Container Registry, która zawiera obraz kontenera. Nazwa użytkownika i hasło są wymagane, aby upewnić się, że firma ma dostęp do rejestru. Aby uzyskać nazwę użytkownika i  hasło administratora, ustaw właściwość z włączoną obsługą administratora na wartość **True** przy użyciu interfejsu wiersza polecenia Command-Line Azure. Opcjonalnie możesz ustawić **opcję Administrator na** wartość **Włącz** w Azure Portal.

:::image type="content" source="media/example-iot-update-container-registry.png" alt-text="Ilustruje okno dialogowe Aktualizowanie rejestru kontenerów.":::

#### <a name="call-out-description"></a>Opis wywołania

1. Administrator

<br>**Hasło dla Azure Container Registry:** podaj hasło dla nazwy użytkownika administratora skojarzonej z Azure Container Registry i zawiera obraz kontenera. Nazwa użytkownika i hasło są wymagane, aby upewnić się, że firma ma dostęp do rejestru. Hasło można uzyskać z witryny Azure Portal, przechodząc do Container Registry Access Keys lub za pomocą interfejsu wiersza polecenia platformy  >   Azure za pomocą [polecenia show.](/cli/azure/acr/credential#az_acr_credential_show)

:::image type="content" source="media/example-iot-access-keys.png" alt-text="Ilustruje ekran klucza dostępu w Azure Portal.":::

#### <a name="call-out-descriptions"></a>Opisy wywołań

1. Klawisze dostępu
2. Nazwa użytkownika
3. Hasło

**Nazwa repozytorium w Azure Container Registry**. Podaj nazwę repozytorium Azure Container Registry, w których znajduje się obraz. Nazwę repozytorium określa się podczas wypychania obrazu do rejestru. Nazwę repozytorium można znaleźć, przechodząc do strony [repozytoriów Container Registry](https://azure.microsoft.com/services/container-registry/)  >  **Repozytoria.** Aby uzyskać więcej informacji, [zobacz Wyświetlanie repozytoriów rejestru kontenerów w Azure Portal](../../container-registry/container-registry-repositories.md). Po skonfigurowaniu nazwy nie można jej zmienić. Użyj unikatowej nazwy dla każdej oferty na twoim koncie.

> [!NOTE]
> Szyfrowanie plików nie jest Azure Container Registry certyfikacji modułu usługi Edge. Azure Container Registry należy utworzyć bez włączonego szyfrowania.

### <a name="image-tags-for-new-versions-of-your-offer"></a>Tagi obrazów dla nowych wersji oferty

Klienci muszą mieć możliwość automatycznego uzyskania aktualizacji z Azure Marketplace podczas publikowania aktualizacji. Jeśli użytkownik nie chce aktualizować obrazu, musi mieć możliwość pozostania w określonej wersji obrazu. Możesz to zrobić, dodając nowe tagi obrazu za każdym razem, gdy aktualizujesz obraz.

**Tag obrazu**. To pole musi zawierać tag **latest,** który wskazuje najnowszą wersję obrazu na wszystkich obsługiwanych platformach. Musi również zawierać tag wersji (na przykład rozpoczynający się od xx.xx.xx, gdzie xx to liczba). Klienci powinni używać [tagów manifestu do](https://github.com/estesp/manifest-tool) wielu platform docelowych. Należy również dodać wszystkie tagi, do których odwołuje się tag manifestu, aby można było je przekazać. Wszystkie tagi manifestu (z wyjątkiem tagu latest) muszą zaczynać się od X.Y- lub X.Y.Z, gdzie X, Y i Z są liczbami całkowitymi. Jeśli na przykład najnowszy tag wskazuje na 1.0.1-linux-x64, 1.0.1-linux-arm32 i 1.0.1-windows-arm32, te sześć tagów należy dodać do tego pola. Aby uzyskać szczegółowe informacje na temat tagów i przechowywania wersji, [zobacz Przygotowanie IoT Edge zasobów technicznych modułu.](create-iot-edge-module-asset.md)

### <a name="default-deployment-settings-optional"></a>Domyślne ustawienia wdrażania (opcjonalnie)

Zdefiniuj najbardziej typowe ustawienia, aby wdrożyć IoT Edge moduł. Zoptymalizuj wdrożenia klientów, umożliwiając im uruchamianie IoT Edge modułu z domyślnymi ustawieniami.

**Trasy domyślne**. Centrum IoT Edge zarządza komunikacją między modułami, IoT Hub i urządzeniami. Można ustawić trasy dla danych wejściowych i wyjściowych między modułami i modułem IoT Hub, co zapewnia elastyczność wysyłania komunikatów tam, gdzie muszą, bez konieczności użycia dodatkowych usług do przetwarzania komunikatów ani pisania dodatkowego kodu. Trasy są konstruowane przy użyciu par nazwa/wartość. Można zdefiniować maksymalnie pięć domyślnych nazw tras, z których każda ma maksymalnie 512 znaków.

Pamiętaj, aby użyć poprawnej składni [trasy](../../iot-edge/module-composition.md#declare-routes)) w wartości trasy (zwykle zdefiniowanej jako FROM/message/* INTO $upstream). Oznacza to, że wszystkie komunikaty wysyłane przez dowolne moduły są wysyłane do IoT Hub. Aby odwołać się do modułu, użyj domyślnej nazwy modułu, która będzie nazwą oferty **bez** spacji ani znaków specjalnych. Aby odwołać się do innych modułów, które nie są jeszcze znane, użyj konwencji <FROM_MODULE_NAME>, aby dać klientom znać, że muszą zaktualizować te informacje. Aby uzyskać szczegółowe informacje IoT Edge tras, zobacz [Deklarowanie tras](../../iot-edge/module-composition.md#declare-routes)).

Jeśli na przykład moduł ContosoModule nasłuchuje danych wejściowych w contosoInput i danych wyjściowych w contosooutput, warto zdefiniować następujące dwie trasy domyślne:

- Nazwa #1: ToContosoModule
- Wartość #1: FROM /messages/modules/<FROM_MODULE_NAME>/outputs/* INTO BrokeredEndpoint("/modules/ContosoModule/inputs/ContosoInput")
- Nazwa #2: FromContosoModuleToCloud
- Wartość #2: FROM /messages/modules/ContonsoModule/outputs/ContosoOutput INTO $upstream

**Żądane właściwości domyślnej bliźniaczej reprezentacji modułu.** Bliźniacze reprezentacji modułu to dokument JSON w IoT Hub który przechowuje informacje o stanie dla wystąpienia modułu, w tym żądane właściwości. Żądane właściwości są używane wraz ze zgłoszonymi właściwościami do synchronizowania konfiguracji lub warunków modułu. Za pomocą zaplecza rozwiązania można ustawić żądane właściwości, a moduł może je odczytać. Moduł może również odbierać powiadomienia o zmianie we żądanych właściwościach. Żądane właściwości są tworzone przy użyciu maksymalnie pięciu par nazwa/wartość, a każda wartość domyślna musi być mniejsza niż 512 znaków. Można zdefiniować maksymalnie pięć żądanych właściwości bliźniaczej reprezentacji nazwy/wartości. Wartości żądanych właściwości bliźniaczej reprezentacji muszą być prawidłowymi wartościami JSON, bez ucieczki, bez tablic z maksymalną zagnieżdżoną hierarchią czterech poziomów. W scenariuszu, w którym parametr wymagany dla wartości domyślnej nie ma sensu (na przykład adres IP serwera klienta), można dodać parametr jako wartość domyślną. Aby dowiedzieć się więcej na temat żądanych właściwości bliźniaczych reprezentacji, [zobacz Define or update desired properties (Definiowanie lub aktualizowanie żądanych właściwości).](../../iot-edge/module-composition.md#define-or-update-desired-properties)

Jeśli na przykład moduł obsługuje dynamicznie konfigurowalna częstotliwość odświeżania przy użyciu żądanych właściwości bliźniaczej reprezentacji, warto zdefiniować następującą domyślną żądaną właściwość bliźniaczej reprezentacji:

- Nazwa #1: RefreshRate
- Wartość #1: 60

**Domyślne zmienne środowiskowe**. Zmienne środowiskowe zapewniają dodatkowe informacje dla modułu, który pomaga w procesie konfiguracji. Zmienne środowiskowe są tworzone przy użyciu par nazwa/wartość. Każda domyślna nazwa i wartość zmiennej środowiskowej musi być mniejsza niż 512 znaków, a można zdefiniować maksymalnie pięć znaków. Jeśli parametr wymagany dla wartości domyślnej nie ma sensu (na przykład adres IP serwera klienta), możesz dodać parametr jako wartość domyślną.

Jeśli na przykład moduł wymaga zaakceptowania warunków użytkowania przed rozpoczęciem pracy, można zdefiniować następującą zmienną środowiskową:

- Nazwa #1: ACCEPT_EULA
- Wartość #1: Y

**Domyślne opcje tworzenia kontenera**. Opcje tworzenia kontenera kierują tworzeniem kontenera IoT Edge kontenera Platformy Docker. IoT Edge obsługuje opcje tworzenia kontenera za pomocą interfejsu API aparatu platformy Docker. Zobacz wszystkie opcje na stronie [Wyświetlanie listy kontenerów.](https://docs.docker.com/engine/api/v1.30/#operation/ContainerList) Pole opcji tworzenia musi mieć prawidłowy kod JSON, nie może zawierać znaków ucieczki i może zawierać mniej niż 512 znaków.

Jeśli na przykład moduł wymaga powiązania portu, zdefiniuj następujące opcje tworzenia:

"HostConfig":{"PortBindings":{"5012/tcp":[{"HostPort":"5012"}]}

## <a name="review-and-publish"></a>Przeglądanie i publikowanie

Po ukończeniu wszystkich wymaganych sekcji oferty możesz przesłać ją do przejrzenia i opublikowania.

W prawym górnym rogu portalu wybierz pozycję Przejrzyj **i opublikuj**.

Na stronie przeglądu można zobaczyć stan publikowania:

- Sprawdź stan ukończenia dla każdej sekcji oferty. Nie można publikować, dopóki wszystkie sekcje oferty nie zostaną oznaczone jako ukończone.
    - **Nie uruchomiono** — sekcja nie została uruchomiona i należy to zrobić.
    - **Niekompletne** — sekcja zawiera błędy, które należy naprawić lub które wymagają podania dodatkowych informacji. Wskazówki można znaleźć w sekcjach we wcześniejszej części tego dokumentu.
    - **Ukończono** — sekcja zawiera wszystkie wymagane dane i nie ma żadnych błędów. Wszystkie sekcje oferty muszą zostać ukończone, zanim będzie można przesłać ofertę.
- Udostępnij zespołowi certyfikacji instrukcje testowania, aby upewnić się, że oferta jest prawidłowo testowana. Ponadto podaj wszelkie uwagi uzupełniające, które są przydatne do zrozumienia Twojej oferty.

Aby przesłać ofertę publikowania, wybierz pozycję **Opublikuj**.

Wyślemy Ci wiadomość e-mail z powiadomieniem, kiedy dostępna jest wersja zapoznawcza oferty do przejrzenia i zatwierdzenia. Aby opublikować ofertę publicznie, przejdź do Partner Center wybierz **pozycję Przejdź na żywo.**

## <a name="next-steps"></a>Następne kroki

- [Aktualizowanie istniejącej oferty na platformie handlowej](update-existing-offer.md)
