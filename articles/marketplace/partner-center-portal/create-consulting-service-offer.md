---
title: Tworzenie oferty usługi konsultingowej w centrum partnerskim — Azure Marketplace
description: Dowiedz się, jak opublikować ofertę usługi konsultingowej w witrynie Azure Marketplace lub AppSource przy użyciu Centrum partnerskiego.
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: eff37750f0580a28c9644ee1ffb7fe4e95038709
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81869795"
---
# <a name="create-a-consulting-service-offer"></a>Tworzenie oferty usługi konsultingowej

> [!IMPORTANT]
> Przenosimy zarządzanie ofertami usług konsultingowych z portal Cloud Partner do Centrum partnerskiego. Dopóki Twoje oferty nie zostaną zmigrowane, postępuj zgodnie z instrukcjami zamieszczonymi w temacie [Azure and Dynamics 365 Consulting Service](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/consulting-services/cloud-partner-portal-consulting-services-publishing-offer) , aby Portal Cloud partner zarządzać ofertami.

W tym artykule opisano sposób publikowania oferty usługi konsultingowej w [witrynie Azure Marketplace](https://azuremarketplace.microsoft.com/) lub [AppSource](https://appsource.microsoft.com/). Utwórz listę ofert usługi konsultingowej opartych na systemie Microsoft [Dynamics 365](https://dynamics.microsoft.com/) i platformie AppSource. Wystaw oferty usługi konsultingowej na podstawie Microsoft Azure w witrynie Azure Marketplace.

Aby utworzyć ofertę usługi konsultingowej w witrynie Azure Marketplace lub AppSource Consulting Services, musisz najpierw [mieć konto wydawcy w centrum partnerskim](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account), a Twoje konto musi być zarejestrowane w komercyjnym programie Marketplace. Przed utworzeniem oferty zapoznaj się z wymaganiami wstępnymi dotyczącymi [wymagań wstępnych dotyczących usługi konsultingowej](https://docs.microsoft.com/azure/marketplace/partner-center-portal/consulting-service-prerequisites).

## <a name="publishing-benefits"></a>Korzyści z publikowania

Zalety publikowania na komercyjnej witrynie Marketplace:

- Promuj swoją firmę przy użyciu marki Microsoft.
- Może dotrzeć do ponad 100 000 000 użytkowników pakietu Office 365 i Dynamics 365 na AppSource i więcej niż 200 000 organizacje za pomocą witryny Azure Marketplace.
- Odbieraj wysokiej jakości potencjalni klienci z tych rynków.
- Czy Twoje usługi są promowane przez zespoły pól i telesprzedaży firmy Microsoft

## <a name="create-a-new-offer"></a>Tworzenie nowej oferty

Po spełnieniu opisanych powyżej wymagań wykonaj następujące kroki, aby utworzyć ofertę usługi konsultingowej.

1. Zaloguj się do [Centrum partnerskiego](https://partner.microsoft.com), a następnie wybierz pozycję **pulpit nawigacyjny** z górnego menu.
2. Na pasku nawigacyjnym po lewej stronie wybierz pozycję **komercyjna witryna Marketplace**, a następnie wybierz pozycję **Przegląd**.

    :::image type="content" source="media/cs-menu-overview.png" alt-text="Ilustruje menu dla komercyjnego portalu Marketplace":::

3. Wybierz pozycję **+ Nowa oferta**, a następnie wybierz pozycję **Usługa doradcza**.

    :::image type="content" source="media/cs-menu-newoffer.png" alt-text="Ilustruje przycisk tworzenia nowej oferty.":::

4. Wprowadź **Identyfikator oferty**. To jest unikatowy identyfikator dla każdej oferty na Twoim koncie.

    - Ten identyfikator jest widoczny dla klientów w adresie sieci Web dla oferty portalu Marketplace.
    - Używaj tylko małych liter, cyfr, kresek i podkreśleń, ale bez spacji. Długość jest ograniczona do 50 znaków. Jeśli na przykład wprowadzisz polecenie **test-Offer-1**, adres URL oferty będzie wynosić `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`.
    - Identyfikator oferty może&#39;t zmienić po wybraniu pozycji **Utwórz**.

5. Wprowadź **alias oferty**. Jest to nazwa używana do odwoływania się do oferty w centrum partnerskim.

    - Ta nazwa jest&#39;t użyta w portalu Marketplace. &#39;s różni się od nazwy oferty i innych wartości, które są widoczne dla klientów. To pole służy do przypisywania nazwy do oferty, która jest bardziej przydatna w przypadku identyfikacji oferty wewnętrznie. nie jest on pokazywany klientom.
    - Alias oferty&#39;t można zmienić po wybraniu pozycji **Utwórz**.

Po wprowadzeniu tych dwóch wartości wybierz pozycję **Utwórz** , aby przejść do strony **Konfiguracja oferty** .

## <a name="offer-setup"></a>Konfiguracja oferty

Po wprowadzeniu identyfikatora oferty i aliasu oferty w centrum partnerskim zostanie utworzona oferta robocza i zostanie wyświetlona strona **Konfiguracja oferty** . Wykonaj następujące kroki, aby skonfigurować ofertę.

### <a name="connect-lead-management"></a>Zarządzanie potencjalnymi klientami

Po opublikowaniu oferty w portalu Marketplace przy użyciu Centrum partnerskiego _należy_ połączyć ją z usługą zarządzania relacjami z klientami (CRM) lub w systemie automatyzacji marketingu. Pozwala to na otrzymywanie informacji kontaktowych klienta, gdy tylko ktoś wyrazi zainteresowanie lub skorzysta z produktu.

1. Wybierz pozycję **Połącz** , aby określić, gdzie chcesz wysyłać potencjalnych klientów. Centrum partnerskie obsługuje następujące systemy:

    - [Dynamics 365](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics) dla zaangażowania klienta
    - [Marketo](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-marketo)
    - [SalesForce](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce)

    > [!NOTE]
    > Jeśli system CRM jest&#39;wymieniony powyżej, użyj [usługi Azure Table](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table) lub [https Endpoint](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-https) do przechowywania danych potencjalnych klientów, a następnie wyeksportuj dane do systemu CRM.

2. Połącz ofertę z miejscem docelowym potencjalnego klienta podczas publikowania w centrum partnerskim.
3. Upewnij się, że połączenie z miejscem docelowym potencjalnego klienta jest skonfigurowane prawidłowo. Po opublikowaniu go w centrum partnerskim&#39;wszystkie poprawność połączenia i wysłanie potencjalnego klienta testowego. Podczas wyświetlania podglądu oferty przed jej rozpoczęciem możesz również przetestować połączenie z liderem, próbując zakupić ofertę samodzielnie w środowisku wersji zapoznawczej.
4. Upewnij się, że połączenie z miejscem docelowym potencjalnego klienta zostało zaktualizowane, aby nie&#39;t utracić żadnych potencjalnych klientów.

Poniżej przedstawiono kilka dodatkowych zasobów zarządzania potencjalnym liderem:

- [Omówienie zarządzania potencjalnymi klientami](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-get-customer-leads)
- [Zarządzanie potencjalnymi klientami — często zadawane pytania](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#frequently-asked-questions)
- [Typowe błędy konfiguracji potencjalnych klientów](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#common-lead-configuration-errors-during-publishing-on-cloud-partner-portal)
- [Omówienie zarządzania potencjalnymi klientami](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf) PDF (Upewnij się, że blokowanie wyskakujących okienek jest wyłączone)

Wybierz pozycję **Zapisz wersję roboczą** przed przejściem do następnej sekcji, właściwości.

### <a name="properties"></a>Właściwości

Na tej stronie można ustawić podstawowy produkt, z którym usługa doradcza oferuje najlepsze zagadnienia, ustawić typ usługi konsultingowej i wybrać odpowiednie produkty.

1. Wybierz **podstawowy produkt** z listy rozwijanej.
2. Z listy rozwijanej wybierz **Typ usługi konsultingowej** :

    - **Ocena** : ocena klienta&#39;środowiska w celu określenia możliwości zastosowania rozwiązania oraz oszacowania kosztów i chronometrażu.
    - **Krótki** opis: wprowadzenie do rozwiązania lub usługi konsultingowej w celu narysowania zainteresowania klienta przy użyciu struktur, pokazów i przykładów klientów.
    - **Implementacja** : Pełna instalacja, która powoduje w pełni robocze rozwiązanie. Ogranicz do rozwiązań, które mogą być implementowane w co najmniej dwóch tygodniach.
    - **Weryfikacja koncepcji** : implementacja ograniczonego zakresu, aby określić, czy rozwiązanie spełnia wymagania klientów.
    - **Warsztat** : interaktywne zaangażowanie przeprowadzone na kliencie&#39;. Może to dotyczyć szkoleń, wyliczeń, ocen lub pokazów utworzonych na klientach&#39;danych lub środowiska.

1. Jeśli wybrano podstawowy produkt **platformy Azure**, wybierz maksymalnie trzy **obszary rozwiązań**. Ułatwiają one klientom w witrynie Azure Marketplace znalezienie oferty. Jeśli są niezgodne&#39;t Wybierz platformę Azure, Pomiń ten krok.
2. Jeśli wybrano podstawowy produkt _inny_ niż Azure, wybierz maksymalnie trzy **odpowiednie produkty**. Ułatwiają one klientom usługi AppSource znalezienie oferty. Aby uzyskać szczegółowe informacje, zobacz [Microsoft AppSource wskazówki dotyczące usługi konsultingowej](https://go.microsoft.com/fwlink/?LinkId=828734&amp;clcid=0x409) (PDF).
3. Wybierz maksymalnie sześć **branż** , do których odnosi się oferta. Ułatwi to klientom znalezienie oferty.
4. Dodaj do trzech **kompetencji** , które firma uzyskała do wyświetlenia na liście oferty usługi konsultingowej. Wymagana jest co najmniej jedna kompetencja z wyjątkiem tego, że dla programu MSP platformy Azure&#39;s i MSP usługi Azure Networking&#39;s.

Wybierz pozycję **Zapisz wersję roboczą** przed przejściem do następnej sekcji, na liście oferty.

## <a name="offer-listing"></a>Lista oferty

Tutaj&#39;szystkie definiują szczegóły oferty, które są wyświetlane w portalu Marketplace. Obejmuje to nazwę oferty, opis, obrazy i tak dalej. Podczas konfigurowania tej oferty Pamiętaj, aby postępować zgodnie z zasadami szczegółowymi na [stronie zasady&#39;s firmy Microsoft](https://docs.microsoft.com/legal/marketplace/certification-policies#800-consulting-services) .

> [!NOTE]
> Szczegóły oferty nie znajdują&#39;t musi być w języku angielskim, jeśli opis oferty rozpoczyna się od frazy &quot;, aplikacja jest dostępna tylko w języku [język inny niż angielski]. &quot;&#39;s również mieć możliwość udostępnienia przydatnego linku do oferowania zawartości w języku, który&#39;s różni się od użytych w szczegółach dotyczących oferty.

### <a name="name"></a>Nazwa

Wprowadzona nazwa zostanie wyświetlona jako tytuł oferty. To pole jest wstępnie wypełnione tekstem wprowadzonym w polu **alias oferty** podczas tworzenia oferty. Tę nazwę można później zmienić.

Nazwa:

- Mogą być znakami towarowymi (i może zawierać znaki towarowe lub autorskie).
- &#39;t może być dłuższa niż 50 znaków.
- Może&#39;t include emojis.

### <a name="search-results-summary"></a>Podsumowanie wyników wyszukiwania

Podaj krótki opis oferty. Może to być maksymalnie 100 znaków i jest używana w wynikach wyszukiwania w portalu Marketplace.

### <a name="description"></a>Opis

Podaj dłuższy opis oferty, do 3 000 znaków. Ten komunikat jest wyświetlany klientom w przeglądzie oferty w portalu Marketplace.

W opisie Uwzględnij co najmniej jeden z następujących elementów:

- Wartość i klucz korzyści oferowanych przez Twoją ofertę
- Skojarzenia kategorii lub branżowych
- Szanse zakupu w aplikacji
- Wszelkie wymagane ujawnienie

Oto kilka porad dotyczących pisania opisu:

- Jasno opisz wartość oferty w pierwszych kilku zdaniach opisu. Uwzględnij następujące elementy:
  - Opis oferty.
  - Typ użytkownika, który przynosi korzyści z oferty.
  - Klienci potrzebują lub wystawiają adresy oferty.
- Należy pamiętać, że pierwsze niektóre zdania mogą być wyświetlane w wynikach wyszukiwania.
- Nie&#39;t polegać na funkcjach i funkcjach do sprzedawania produktu. Zamiast tego należy skoncentrować się na wartości, którą oferuje oferta.
- Spróbuj użyć tekstów specyficznych dla branżowych lub opartych na korzyściach.

Aby Twój opis był bardziej atrakcyjny, użyj edytora tekstu sformatowanego do formatowania opisu. Edytor tekstu sformatowanego pozwala dodawać numery, punktory, pogrubienie, kursywę i wcięcia, aby zwiększyć czytelność opisu.

:::image type="content" source="media/cs-rich-text-editor.png" alt-text="Ilustruje Edytor tekstu sformatowanego w celu zapisania opisu oferty." border="false":::

### <a name="keywords"></a>Słowa kluczowe

Wprowadź do trzech słów kluczowych wyszukiwania, które są istotne dla głównego produktu i usługi konsultingowej. Ułatwi to znalezienie oferty.

### <a name="duration"></a>Czas trwania

Ustaw oczekiwany czas trwania tego zaangażowania z klientem.

### <a name="contact-information"></a>Informacje kontaktowe

Musisz podać nazwę, adres e-mail i numer telefonu dla kontaktu **głównego** i **pomocniczego**. Te informacje jest&#39;t widoczne dla klientów. Jest ona dostępna dla firmy Microsoft i może być świadczona partnerom dostawcy rozwiązań w chmurze (CSP).

### <a name="supporting-documents"></a>Dokumenty pomocnicze

Dodaj maksymalnie trzy (ale co najmniej jeden) obsługę dokumentów PDF dla oferty.

### <a name="marketplace-images"></a>Obrazy z witryny Marketplace

Podaj logo i obrazy, które mają być używane z ofertą. Wszystkie obrazy muszą mieć format PNG. Rozmyte obrazy zostaną odrzucone.

>[!Note]
>Jeśli występuje problem z przekazywaniem plików, upewnij się, że sieć lokalna nie blokuje https://upload.xboxlive.com usługi używanej przez centrum partnerskie.

#### <a name="store-logos"></a>Logo sklepu

Udostępniaj pliki. png oferty&#39;s logo w każdym z następujących rozmiarów pikseli:

- **Mały (48 x 48)**
- **Duże (216 x 216)**

Wszystkie logo są wymagane i są używane w różnych miejscach na liście w portalu Marketplace.

#### <a name="screenshots-optional"></a>Zrzuty ekranu (opcjonalnie)

Dodaj maksymalnie pięć zrzutów ekranu, które pokazują, jak działa Twoja oferta. Każdy z nich musi mieć 1280 x 720 pikseli w rozmiarze i formacie PNG.

#### <a name="videos-optional"></a>Wideo (opcjonalnie)

Dodaj do czterech filmów wideo, które przedstawiają Twoją ofertę. Wprowadź nazwę wideo&#39;, jego adres sieci Web (URL) i obraz miniatury PNG wideo o rozmiarze 1280 x 720 pikseli.

Wybierz pozycję **Zapisz wersję roboczą** przed przejściem do następnej sekcji, cennika i dostępności.

## <a name="pricing-and-availability"></a>Cennik i dostępność

W tym miejscu zdefiniujesz elementy, takie jak Cennik, rynek i klucz prywatny.

1. **Rynek**: Ustaw rynek, w którym oferta będzie dostępna. Możesz wybrać tylko jeden rynek dla każdej oferty.
    1. Na rynkach Stany Zjednoczone lub Kanady wybierz pozycję **Edytuj Stany** (lub **prowincje**), aby określić, gdzie będzie dostępna oferta.
2. **Podgląd odbiorców**: Skonfiguruj **klucz ukrycia** używany do ustawiania odbiorców prywatnych oferty.
3. **Cennik**: Określ, czy oferta jest ofertą **bezpłatną** , czy **płatną** .

    > [!NOTE]
    > Oferty usługi konsultingowej są przeznaczone tylko dla aukcji. Wszystkie transakcje będą wykonywane bezpośrednio poza komercyjnym rynkiem Marketplace.

4. W przypadku płatnej oferty Określ **cenę i walutę** oraz czy cena jest **stała** czy **Szacowana**. W przypadku oszacowania należy określić, jakie czynniki mają wpływ na cenę.
5. Wybierz pozycję **Zapisz wersję roboczą**.

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
