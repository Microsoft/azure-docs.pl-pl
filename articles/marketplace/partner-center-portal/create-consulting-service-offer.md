---
title: Tworzenie oferty usługi konsultingowej — Microsoft Commercial Marketplace
description: Dowiedz się, jak opublikować ofertę usługi konsultingowej w Microsoft AppSource lub witrynie Azure Marketplace przy użyciu Centrum partnerskiego.
author: Microsoft-BradleyWright
ms.author: brwrigh
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 06/17/2020
ms.openlocfilehash: 464e75e55bc67ce619134be01ba00f2606a271a4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91709074"
---
# <a name="create-a-consulting-service-offer"></a>Tworzenie oferty usługi konsultingowej

W tym artykule opisano sposób publikowania oferty usługi konsultingowej w [Microsoft AppSource](https://appsource.microsoft.com/) lub [witrynie Azure Marketplace](https://azuremarketplace.microsoft.com/). Utwórz listę ofert usługi konsultingowej opartych na systemie Microsoft [Dynamics 365](https://dynamics.microsoft.com/) i platformie AppSource. Wystaw oferty usługi konsultingowej na podstawie Microsoft Azure w witrynie Azure Marketplace. Przed rozpoczęciem [Utwórz komercyjne konto witryny Marketplace w centrum partnerskim](create-account.md) , jeśli jeszcze tego nie zrobiono. Upewnij się, że Twoje konto jest zarejestrowane w komercyjnym programie Marketplace.

Przed utworzeniem oferty zapoznaj się z wymaganiami wstępnymi dotyczącymi [wymagań wstępnych dotyczących usługi konsultingowej](consulting-service-prerequisites.md).

## <a name="publishing-benefits"></a>Korzyści z publikowania

Zalety publikowania na komercyjnej witrynie Marketplace:

- Promuj swoją firmę przy użyciu marki Microsoft.
- Może dotrzeć do ponad 100 000 000 Microsoft 365 i użytkowników usługi Dynamics 365 w AppSource i więcej niż 200 000 organizacje w witrynie Azure Marketplace.
- Odbieraj wysokiej jakości potencjalni klienci z tych rynków.
- Czy Twoje usługi są promowane przez zespoły pól i telesprzedaży firmy Microsoft

## <a name="create-a-new-offer"></a>Tworzenie nowej oferty

1. Zaloguj się do [Centrum partnerskiego](https://partner.microsoft.com/dashboard/home).
2. W menu po lewej stronie wybierz pozycję **komercyjne Omówienie witryny Marketplace**  >  **Overview**.
3. Na stronie Przegląd wybierz pozycję **+ Nowa**  >  **Usługa konsultingowa**oferty.

    ![Ilustruje menu nawigacji po lewej stronie.](./media/new-offer-consulting-service.png)

>[!NOTE]
>Po opublikowaniu oferty zmiany wprowadzone w centrum partnerskim będą widoczne w sklepach online dopiero po ponownym opublikowaniu oferty. Pamiętaj, aby zawsze ponownie publikować po wprowadzeniu zmian.

## <a name="new-offer"></a>Nowa oferta

Wprowadź **Identyfikator oferty**. To jest unikatowy identyfikator dla każdej oferty na Twoim koncie.

- Ten identyfikator jest widoczny dla klientów w adresie sieci Web dla oferty portalu Marketplace.
- Użyj tylko małych liter i cyfr. Może zawierać łączniki i podkreślenia, ale nie spacje i jest ograniczone do 50 znaków. Jeśli na przykład wprowadzisz polecenie **test-Offer-1**, adres sieci Web oferty będzie wynosić `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` .
- Nie można zmienić identyfikatora oferty po wybraniu pozycji **Utwórz**.

Wprowadź **alias oferty**. Jest to nazwa używana dla oferty w centrum partnerskim.

- Ta nazwa nie jest używana w portalu Marketplace i różni się od nazwy oferty i innych wartości widocznych dla klientów.
- Aliasu oferty nie można zmienić po wybraniu pozycji **Utwórz**.

Wybierz pozycję **Utwórz** , aby wygenerować ofertę i kontynuować.

## <a name="offer-setup"></a>Konfiguracja oferty

### <a name="connect-lead-management"></a>Zarządzanie potencjalnymi klientami

Po opublikowaniu oferty w portalu Marketplace przy użyciu Centrum partnerskiego _należy_ połączyć ją z usługą zarządzania relacjami z klientami (CRM) lub w systemie automatyzacji marketingu. Pozwala to na otrzymywanie informacji kontaktowych klienta, gdy tylko ktoś wyrazi zainteresowanie lub skorzysta z produktu.

1. Wybierz pozycję **Połącz** , aby określić, gdzie chcesz wysyłać potencjalnych klientów. Centrum partnerskie obsługuje następujące systemy:

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

Na tej stronie można ustawić podstawowy produkt, z którym usługa doradcza oferuje najlepsze zagadnienia, ustawić typ usługi konsultingowej i wybrać odpowiednie produkty.

1. Wybierz **podstawowy produkt** z listy rozwijanej.
2. Z listy rozwijanej wybierz **Typ usługi konsultingowej** :

    - **Ocena**: Ocena środowiska klienta w celu określenia możliwości zastosowania rozwiązania oraz oszacowania kosztów i chronometrażu.
    - **Krótki**opis: wprowadzenie do rozwiązania lub usługi konsultingowej w celu narysowania zainteresowania klienta przy użyciu struktur, pokazów i przykładów klientów.
    - **Implementacja**: Pełna instalacja, która powoduje w pełni robocze rozwiązanie. Ogranicz do rozwiązań, które mogą być implementowane w co najmniej dwóch tygodniach.
    - **Weryfikacja koncepcji**: implementacja ograniczonego zakresu, aby określić, czy rozwiązanie spełnia wymagania klientów.
    - **Warsztat**: Interaktywna zaangażowanie w siedzibie klienta. Może to dotyczyć szkoleń, krótkich, ocen lub pokazów utworzonych na danych lub środowisku klienta.

3. Jeśli wybrano **platformę Azure** jako produkt podstawowy, należy wybrać maksymalnie trzy **obszary rozwiązań**. Ułatwiają one klientom w witrynie Azure Marketplace znalezienie oferty. Jeśli nie wybrano platformy Azure, Pomiń ten krok.

    - Analiza
    - Modernizacja aplikacji
    - Archiwum
    - SI i uczenie maszynowe
    - Backup
    - Dane big data
    - Platforma danych
    - Zarządzanie centrum danych
    - DevOps
    - Odzyskiwanie po awarii
    - Tożsamość
    - Internet rzeczy
    - Migracja
    - Sieć
    - Zabezpieczenia
    - Storage

1. Jeśli wybrano **platformę Azure** jako produkt podstawowy, można wybrać maksymalnie sześć **branż**. Ułatwiają one klientom w witrynie Azure Marketplace znalezienie oferty. Zapoznaj się z pełną listą branż w [ofercie z ofertami dotyczącymi najlepszych](../gtm-offer-listing-best-practices.md)rozwiązań. Jeśli nie wybrano platformy Azure, Pomiń ten krok.
1. Jeśli wybrano podstawowy produkt *inny* niż Azure, wybierz maksymalnie trzy **odpowiednie produkty**. Ułatwiają one klientom usługi AppSource znalezienie oferty. Aby uzyskać szczegółowe informacje, zobacz [Microsoft AppSource wskazówki dotyczące usługi konsultingowej](https://go.microsoft.com/fwlink/?LinkId=828734&amp;clcid=0x409) (PDF).
1. W przypadku wybrania produktu podstawowego *innego* niż Azure można wybrać maksymalnie dwie **branże** i dwie **pionowe** dla każdej branży. Ułatwiają one klientom usługi AppSource znalezienie oferty. Zapoznaj się z pełną listą branż i pionowych w temacie z [ofertami dotyczącymi najlepszych](../gtm-offer-listing-best-practices.md)rozwiązań.
1. Dodaj do trzech **kompetencji** , które firma uzyskała do wyświetlenia na liście oferty usługi konsultingowej. Wymagana jest co najmniej jedna kompetencje z wyjątkiem usług Azure ekspercki MSP i Azure Network msp.

Wybierz pozycję **Zapisz wersję roboczą** przed kontynuowaniem.

## <a name="offer-listing"></a>Lista oferty

W tym miejscu zdefiniujesz szczegóły oferty, które są wyświetlane w portalu Marketplace. Obejmuje to nazwę oferty, opis, obrazy i tak dalej. Podczas konfigurowania tej oferty Pamiętaj, aby zgodnie z zasadami szczegółowymi na [stronie komercyjne zasady certyfikacji witryny Marketplace](https://docs.microsoft.com/legal/marketplace/certification-policies#800-consulting-services) .

> [!NOTE]
> Szczegóły oferty nie muszą znajdować się w języku angielskim, jeśli opis oferty zaczyna się od frazy, &quot; Ta aplikacja jest dostępna tylko w języku [język inny niż angielski]. &quot; Istnieje również możliwość udostępnienia przydatnego linku do oferowania zawartości w języku innym niż ten, który jest używany w szczegółach dotyczących oferty.

Oto przykład sposobu wyświetlania informacji o ofercie w witrynie Azure Marketplace (wszystkie wymienione ceny są przeznaczone wyłącznie do celów i nie są przeznaczone do odzwierciedlenia rzeczywistych kosztów):

:::image type="content" source="media/example-consulting-service-offer.png" alt-text="Ilustruje, jak ta oferta pojawia się w witrynie Azure Marketplace.":::

#### <a name="call-out-descriptions"></a>Opisy połączeń

1. Logo
2. Cena
3. Obszary rozwiązania
4. Branże
5. Nazwa oferty
6. Podsumowanie
7. Opis
8. Zrzuty ekranu/wideo

<br>Oto przykład sposobu wyświetlania informacji o ofercie w Microsoft AppSource (wszelkie wymienione ceny są przeznaczone wyłącznie do celów i nie są przeznaczone do odzwierciedlenia rzeczywistych kosztów):

:::image type="content" source="media/example-consulting-service-offer-appsource.png" alt-text="Ilustruje, jak ta oferta pojawia się w witrynie Azure Marketplace.":::

#### <a name="call-out-descriptions"></a>Opisy połączeń

1. Logo
2. Cena
3. Produkty
4. Branże
5. Nazwa oferty
6. Podsumowanie
7. Opis
8. Zrzuty ekranu/wideo
9. Dokumenty

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

### <a name="keywords"></a>Słowa kluczowe

Wprowadź do trzech słów kluczowych wyszukiwania, które są istotne dla głównego produktu i usługi konsultingowej. Ułatwi to znalezienie oferty.

### <a name="duration"></a>Czas trwania

Ustaw oczekiwany czas trwania tego zaangażowania z klientem.

### <a name="contact-information"></a>Informacje kontaktowe

Musisz podać nazwę, adres e-mail i numer telefonu dla kontaktu **głównego** i **pomocniczego**. Te informacje nie są widoczne dla klientów. Jest ona dostępna dla firmy Microsoft i może być świadczona partnerom dostawcy rozwiązań w chmurze (CSP).

### <a name="supporting-documents"></a>Dokumenty pomocnicze

Dodaj maksymalnie trzy (ale co najmniej jeden) obsługę dokumentów PDF dla oferty.

### <a name="marketplace-images"></a>Obrazy z witryny Marketplace

Podaj logo i obrazy, które mają być używane z ofertą. Wszystkie obrazy muszą mieć format PNG. Rozmyte obrazy zostaną odrzucone.

[!INCLUDE [logo tips](../includes/graphics-suggestions.md)]

>[!Note]
>Jeśli występuje problem z przekazywaniem plików, upewnij się, że sieć lokalna nie blokuje https://upload.xboxlive.com usługi używanej przez centrum partnerskie.

#### <a name="store-logos"></a>Logo sklepu

Podaj plik PNG dla logo o **dużym** rozmiarze. Centrum partnerskie będzie używać tego do tworzenia **małych** logo. Opcjonalnie można zastąpić ten inny obraz później.

- **Duże** (od 216 x 216 do 350 x 350 px, wymagane)
- **Mały** (48 x 48 pikseli, opcjonalnie)

Te logo są używane w różnych miejscach na liście.

[!INCLUDE [Logo tips](../includes/graphics-suggestions.md)]

#### <a name="screenshots-optional"></a>Zrzuty ekranu (opcjonalnie)

Dodaj maksymalnie pięć zrzutów ekranu, które pokazują, jak działa Twoja oferta. Każdy z nich musi mieć 1280 x 720 pikseli w rozmiarze i formacie PNG.

#### <a name="videos-optional"></a>Wideo (opcjonalnie)

Dodaj do czterech filmów wideo, które przedstawiają Twoją ofertę. Wprowadź nazwę filmu wideo, jego adres internetowy (URL) i miniaturowy obraz PNG wideo o rozmiarze 1280 x 720 pikseli.

Wybierz pozycję **Zapisz wersję roboczą** przed kontynuowaniem.

## <a name="pricing-and-availability"></a>Cennik i dostępność

W tym miejscu zdefiniujesz elementy, takie jak Cennik, rynek i klucz prywatny.

1. **Rynek**: Ustaw rynek, w którym oferta będzie dostępna. Możesz wybrać tylko jeden rynek dla każdej oferty.
    1. Na rynkach Stany Zjednoczone lub Kanady wybierz pozycję **Edytuj Stany** (lub **prowincje**), aby określić, gdzie będzie dostępna oferta.
2. **Podgląd odbiorców**: Skonfiguruj **klucz ukrycia** używany do ustawiania odbiorców prywatnych oferty.
3. **Cennik**: Określ, czy oferta jest ofertą **bezpłatną** , czy **płatną** .

    > [!NOTE]
    > Oferty usługi konsultingowej są przeznaczone tylko dla aukcji. Wszystkie transakcje będą wykonywane bezpośrednio poza komercyjnym rynkiem Marketplace.

4. W przypadku płatnej oferty Określ **cenę i walutę** oraz czy cena jest **stała** czy **Szacowana**. W przypadku oszacowania należy określić, jakie czynniki mają wpływ na cenę.
5. Wybierz pozycję **Zapisz wersję roboczą** przed kontynuowaniem.

## <a name="review-and-publish"></a>Przeglądanie i publikowanie

Po zakończeniu wszystkich wymaganych sekcji oferty możesz przesłać swoją ofertę do przeglądu i publikacji.

1. Gdy wszystko będzie gotowe do opublikowania oferty usługi konsultingowej, wybierz pozycję **Przejrzyj i Opublikuj**.
2. Zapoznaj się ze szczegółami na stronie ostatecznego przesłania.
3. W razie potrzeby Napisz uwagę do zespołu certyfikacji, jeśli uważasz, że wszystkie szczegóły oferty wymagają wyjaśnienia.
4. Gdy wszystko będzie gotowe, wybierz pozycję **Prześlij**.
5. Na stronie **Przegląd oferty** przedstawiono etap publikowania, w którym znajduje się Twoja oferta.

Aby uzyskać więcej informacji o tym, jak długo oferta będzie znajdować się na każdym etapie publikowania, zobacz [Sprawdzanie stanu publikacji komercyjnej oferty portalu Marketplace](publishing-status.md).

## <a name="update-your-existing-consulting-service-offers"></a>Zaktualizuj istniejące oferty usługi konsultingowej

- [Aktualizowanie istniejącej oferty w komercyjnej witrynie Marketplace](update-existing-offer.md)
