---
title: Tworzenie oferty usługi konsultingowej — Microsoft Commercial Marketplace
description: Dowiedz się, jak opublikować ofertę usługi konsultingowej w Microsoft AppSource lub witrynie Azure Marketplace przy użyciu Centrum partnerskiego.
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/19/2020
ms.openlocfilehash: 52b97e772897289f0c54c4520da7d7af80f44dc3
ms.sourcegitcommit: 6fd28c1e5cf6872fb28691c7dd307a5e4bc71228
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2020
ms.locfileid: "85213729"
---
# <a name="create-a-consulting-service-offer"></a>Tworzenie oferty usługi konsultingowej

W tym artykule opisano sposób publikowania oferty usługi konsultingowej w [Microsoft AppSource](https://appsource.microsoft.com/) lub [witrynie Azure Marketplace](https://azuremarketplace.microsoft.com/). Utwórz listę ofert usługi konsultingowej opartych na systemie Microsoft [Dynamics 365](https://dynamics.microsoft.com/) i platformie AppSource. Wystaw oferty usługi konsultingowej na podstawie Microsoft Azure w witrynie Azure Marketplace. Przed rozpoczęciem [Utwórz komercyjne konto witryny Marketplace w centrum partnerskim](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account) , jeśli jeszcze tego nie zrobiono. Upewnij się, że Twoje konto jest zarejestrowane w komercyjnym programie Marketplace.

Przed utworzeniem oferty zapoznaj się z wymaganiami wstępnymi dotyczącymi [wymagań wstępnych dotyczących usługi konsultingowej](https://docs.microsoft.com/azure/marketplace/partner-center-portal/consulting-service-prerequisites).

## <a name="publishing-benefits"></a>Korzyści z publikowania

Zalety publikowania na komercyjnej witrynie Marketplace:

- Promuj swoją firmę przy użyciu marki Microsoft.
- Może dotrzeć do ponad 100 000 000 użytkowników pakietu Office 365 i Dynamics 365 na AppSource i więcej niż 200 000 organizacje za pomocą witryny Azure Marketplace.
- Odbieraj wysokiej jakości potencjalni klienci z tych rynków.
- Czy Twoje usługi są promowane przez zespoły pól i telesprzedaży firmy Microsoft

## <a name="create-a-new-offer"></a>Tworzenie nowej oferty

1. Zaloguj się do [Centrum partnerskiego](https://partner.microsoft.com/dashboard/home).
2. W menu po lewej stronie wybierz pozycję **komercyjne Omówienie witryny Marketplace**  >  **Overview**.
3. Na stronie Przegląd wybierz pozycję **+ Nowa**  >  **Usługa konsultingowa**oferty.

    ![Ilustruje menu nawigacji po lewej stronie.](./media/new-offer-consulting-service.png)

>[!NOTE]
>Po opublikowaniu oferty zmiany wprowadzone w centrum partnerskim są wyświetlane w obszarze witryny w sklepie po ponownym opublikowaniu oferty. Pamiętaj, aby zawsze ponownie publikować po wprowadzeniu zmian.

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

    - [Dynamics 365](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics) dla zaangażowania klienta
    - [Marketo](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-marketo)
    - [Salesforce](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce)

    > [!NOTE]
    > Jeśli system CRM nie jest wymieniony powyżej, użyj [tabeli platformy Azure](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table) lub [punktu końcowego HTTPS](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-https) do przechowywania danych potencjalnych klientów, a następnie wyeksportuj dane do systemu CRM.

2. Połącz ofertę z miejscem docelowym potencjalnego klienta podczas publikowania w centrum partnerskim.
3. Upewnij się, że połączenie z miejscem docelowym potencjalnego klienta jest skonfigurowane prawidłowo. Po opublikowaniu go w centrum partnerskim sprawdzimy połączenie i wyślesz potencjalnego klienta. Podczas wyświetlania podglądu oferty przed jej rozpoczęciem możesz również przetestować połączenie z liderem, próbując zakupić ofertę samodzielnie w środowisku wersji zapoznawczej.
4. Upewnij się, że połączenie z miejscem docelowym potencjalnego klienta zostało zaktualizowane, więc nie utracisz żadnych potencjalnych klientów.

Poniżej przedstawiono kilka dodatkowych zasobów zarządzania potencjalnym liderem:

- [Omówienie zarządzania potencjalnymi klientami](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-get-customer-leads)
- [Zarządzanie potencjalnymi klientami — często zadawane pytania](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#frequently-asked-questions)
- [Typowe błędy konfiguracji potencjalnych klientów](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#publishing-config-errors)
- [Omówienie zarządzania potencjalnymi klientami](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf) PDF (Upewnij się, że blokowanie wyskakujących okienek jest wyłączone)

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

3. Jeśli wybrano podstawowy produkt **platformy Azure**, wybierz maksymalnie trzy **obszary rozwiązań**. Ułatwiają one klientom w witrynie Azure Marketplace znalezienie oferty. Jeśli nie wybrano platformy Azure, Pomiń ten krok.
4. Jeśli wybrano podstawowy produkt _inny_ niż Azure, wybierz maksymalnie trzy **odpowiednie produkty**. Ułatwiają one klientom usługi AppSource znalezienie oferty. Aby uzyskać szczegółowe informacje, zobacz [Microsoft AppSource wskazówki dotyczące usługi konsultingowej](https://go.microsoft.com/fwlink/?LinkId=828734&amp;clcid=0x409) (PDF).
5. Wybierz maksymalnie sześć **branż** , do których odnosi się oferta. Ułatwi to klientom znalezienie oferty.
6. Dodaj do trzech **kompetencji** , które firma uzyskała do wyświetlenia na liście oferty usługi konsultingowej. Wymagana jest co najmniej jedna kompetencje z wyjątkiem usług Azure ekspercki MSP i Azure Network msp.

Wybierz pozycję **Zapisz wersję roboczą** przed kontynuowaniem.

## <a name="offer-listing"></a>Lista oferty

W tym miejscu zdefiniujesz szczegóły oferty, które są wyświetlane w portalu Marketplace. Obejmuje to nazwę oferty, opis, obrazy i tak dalej. Podczas konfigurowania tej oferty Pamiętaj, aby zgodnie z zasadami szczegółowymi na [stronie komercyjne zasady certyfikacji witryny Marketplace](https://docs.microsoft.com/legal/marketplace/certification-policies#800-consulting-services) .

> [!NOTE]
> Szczegóły oferty nie muszą znajdować się w języku angielskim, jeśli opis oferty zaczyna się od frazy, &quot; Ta aplikacja jest dostępna tylko w języku [język inny niż angielski]. &quot; Istnieje również możliwość udostępnienia przydatnego linku do oferowania zawartości w języku innym niż ten, który jest używany w szczegółach dotyczących oferty.

Oto przykład sposobu wyświetlania informacji o ofercie w witrynie Azure Marketplace (wszystkie wymienione ceny są przeznaczone wyłącznie do celów i nie są przeznaczone do odzwierciedlenia rzeczywistych kosztów):

:::image type="content" source="media/example-consulting-service-offer.png" alt-text="Ilustruje, jak ta oferta pojawia się w witrynie Azure Marketplace.":::

#### <a name="call-out-descriptions"></a>Opisy połączeń

1. Logo
2. Price
3. Obszary rozwiązania
4. Branże
5. Nazwa oferty
6. Podsumowanie
7. Opis
8. Zrzuty ekranu/wideo

<br>Oto przykład sposobu wyświetlania informacji o ofercie w Microsoft AppSource (wszelkie wymienione ceny są przeznaczone wyłącznie do celów i nie są przeznaczone do odzwierciedlenia rzeczywistych kosztów):

:::image type="content" source="media/example-consulting-service-offer-appsource.png" alt-text="Ilustruje, w jaki sposób ta oferta pojawia się w Microsoft AppSource.":::

#### <a name="call-out-descriptions"></a>Opisy połączeń

1. Logo
2. Price
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

>[!Note]
>Jeśli występuje problem z przekazywaniem plików, upewnij się, że sieć lokalna nie blokuje https://upload.xboxlive.com usługi używanej przez centrum partnerskie.

#### <a name="store-logos"></a>Logo sklepu

Podaj pliki PNG logo oferty w każdym z następujących rozmiarów pikseli:

- **Mały (48 x 48)**
- **Duże (216 x 216)**

Wszystkie logo są wymagane i są używane w różnych miejscach na liście w portalu Marketplace.

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

## <a name="review-and-publish"></a>Przejrzyj i Opublikuj

Po zakończeniu wszystkich wymaganych sekcji oferty możesz przesłać swoją ofertę do przeglądu i publikacji.

1. Gdy wszystko będzie gotowe do opublikowania oferty usługi konsultingowej, kliknij pozycję **Przejrzyj i Opublikuj**.
2. Zapoznaj się ze szczegółami na stronie ostatecznego przesłania.
3. W razie potrzeby Napisz uwagę do zespołu certyfikacji, jeśli uważasz, że wszystkie szczegóły oferty wymagają wyjaśnienia.
4. Gdy wszystko będzie gotowe, wybierz pozycję **Prześlij**.
5. Na stronie **Przegląd oferty** przedstawiono etap publikowania, w którym znajduje się Twoja oferta.

Aby uzyskać więcej informacji o tym, jak długo oferta będzie znajdować się na każdym etapie publikowania, zobacz [Sprawdzanie stanu publikacji komercyjnej oferty portalu Marketplace](https://docs.microsoft.com/azure/marketplace/partner-center-portal/publishing-status).

## <a name="update-your-existing-consulting-service-offers"></a>Zaktualizuj istniejące oferty usługi konsultingowej

- [Aktualizowanie istniejącej oferty w komercyjnej witrynie Marketplace](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer)
