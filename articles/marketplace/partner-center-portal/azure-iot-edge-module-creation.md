---
title: Tworzenie oferty modułów usługi Azure IoT Edge za pomocą centrum partnerskiego — Azure Marketplace
description: Dowiedz się, jak utworzyć ofertę modułów usługi IoT Edge w portalu Azure Marketplace przy użyciu Centrum partnerskiego
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/03/2020
ms.openlocfilehash: cca54e4e456fe766b190f64657cd1aca1d9520e0
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869151"
---
# <a name="create-an-iot-edge-module-offer"></a>Tworzenie oferty modułu usługi IoT Edge

> [!IMPORTANT]
> Przenosimy zarządzanie ofertami modułu IoT Edge z portalu Cloud Partner do Centrum partnerów. Dopóki oferty nie zostaną zmigrowane, postępuj zgodnie z instrukcjami w module IoT Edge, aby zarządzać ofertami, [postępuj zgodnie z instrukcjami w module IoT Edge.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/iot-edge-module/cpp-offer-process-parts)

W tym artykule opisano sposób tworzenia i publikowania oferty modułu usługi Internet of Things (IoT) dla portalu Azure Marketplace.

Aby można było utworzyć ofertę modułów usługi IoT Edge, musisz mieć konto w portalu Partner Center. Jeśli jeszcze go nie utworzyłeś, zobacz [Tworzenie konta w portalu partnerów](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account).

## <a name="create-a-new-offer"></a>Tworzenie nowej oferty

1. Zaloguj się do Centrum partnerskiego.
2. W menu nawigacji po lewej stronie wybierz pozycję**Przegląd** **portalu Komercyjnego** > .

    ![Ilustruje menu nawigacji po lewej stronie.](./media/cs-menu-overview.png)

3. Wybierz **+ Nowa oferta** > **Moduł IoT Edge**. Zostanie wyświetlone okno dialogowe **Nowa oferta.**

> [!IMPORTANT]
> Po opublikowaniu oferty zmiany wprowadzone w Centrum partnerskim pojawiają się tylko w witrynach sklepowych po ponownym opublikowaniu oferty. Upewnij się, że zawsze ponownie opublikować po dokonaniu zmian.

### <a name="offer-id-and-alias"></a>Identyfikator oferty i alias

Wprowadź identyfikator **oferty**. Jest to unikatowy identyfikator dla każdej oferty na Twoim koncie.

- Ten identyfikator jest widoczny dla klientów w adresie internetowym dla oferty portalu Marketplace i szablonów usługi Azure Resource Manager, jeśli ma to zastosowanie.
- Użyj tylko małych liter i cyfr. Może zawierać łączniki i podkreślenia, ale bez spacji i jest ograniczona do 50 znaków. Na przykład, jeśli wprowadzisz **test-offer-1**, `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`adres internetowy oferty będzie .
- Nie można zmienić identyfikatora oferty po wybraniu opcji Utwórz.

Wprowadź **alias oferty**. Jest to nazwa używana do odwoływania się do oferty w Centrum partnerów.

- Ta nazwa nie jest używana w portalu Marketplace i różni się od nazwy oferty i innych wartości wyświetlanych klientom.
- Nie można tego zmienić po wybraniu opcji **Utwórz**.

Po wprowadzeniu tych dwóch wartości wybierz pozycję **Utwórz** przed przejściem do następnej strony, Przegląd oferty.

## <a name="offer-overview"></a>Przegląd oferty

Na stronie **Przegląd oferty** przedstawiono wizualną reprezentację kroków wymaganych do opublikowania tej oferty (zarówno ukończonych, jak i nadchodzących) oraz czasu, jaki powinien potrwać każdy krok.

Ta strona zawiera łącza do wykonywania operacji w tej ofercie na podstawie dokonanych wyborów. Przykład:

- Jeśli oferta jest wersja robocza — [usuwanie wersji roboczej oferty](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#delete-a-draft-offer)
- Jeśli oferta jest na żywo - [Przestań sprzedawać ofertę](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#stop-selling-an-offer-or-plan)
- Jeśli oferta jest w wersji zapoznawczej - [Go-live](https://docs.microsoft.com/azure/marketplace/partner-center-portal/publishing-status#publisher-approval)
- Jeśli nie zostało ukończone wylogowanie wydawcy — [anuluj publikowanie.](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#cancel-publishing)

## <a name="offer-setup"></a>Konfiguracja oferty

Wykonaj następujące kroki, aby skonfigurować ofertę.

### <a name="connect-lead-management"></a>Łączenie zarządzania potencjalnymi klientami

Publikując ofertę na rynku za pomocą Centrum partnerów, można opcjonalnie połączyć ją z systemem crm (Customer Relationship Management). Dzięki temu możesz otrzymywać informacje kontaktowe klienta, gdy tylko ktoś wyrazi zainteresowanie produktem lub z niego korzysta.

1. Wybierz miejsce docelowe potencjalnych klientów, do którego chcesz wysyłać potencjalnych klientów. Centrum partnerskie obsługuje następujące systemy CRM:

    - [Dynamics 365](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics) dla zaangażowania klienta
    - [Marketo](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-marketo)
    - [SalesForce](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce)

    > [!NOTE]
    > Jeśli systemu CRM nie ma na liście powyżej, użyj [tabeli Azure](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table) lub [punktu końcowego https](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-https) do przechowywania danych potencjalnych klientów, a następnie wyeksportuj dane do systemu CRM.

2. Połącz ofertę z głównym miejscem docelowym podczas publikowania w Centrum partnerów.
3. Upewnij się, że połączenie z miejscem docelowym potencjalnego klienta jest poprawnie skonfigurowane. Po opublikowaniu go w Centrum partnerów, firma Microsoft sprawdź poprawność połączenia i wysłać potencjalnego klienta testowego. Podczas wyświetlania podglądu oferty przed jej wyświetleniem można również przetestować połączenie potencjalnego klienta, próbując samodzielnie kupić ofertę w środowisku podglądu.
4. Upewnij się, że połączenie z miejscem docelowym potencjalnego klienta pozostaje zaktualizowane, aby nie utracić potencjalnych klientów.

Oto kilka dodatkowych zasobów zarządzania potencjalnymi klientami:

- [Omówienie zarządzania potencjalnymi klientami](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-get-customer-leads)
- [Zarządzanie potencjalnymi klientami — często zadawane pytania](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#frequently-asked-questions)
- [Typowe błędy konfiguracji potencjalnych klientów](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#common-lead-configuration-errors-during-publishing-on-cloud-partner-portal)
- [Omówienie zarządzania potencjalnymi klientami](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf) PDF (Upewnij się, że bloker wyskakujących wyskakujących wyskakujących jest wyłączony).

Wybierz **pozycję Zapisz pochyłość** przed przejściem do następnej sekcji Właściwości.

### <a name="properties"></a>Właściwości

Ta strona umożliwia zdefiniowanie kategorii używanych do grupowanie oferty na rynku oraz umów prawnych, które obsługują Twoją ofertę.

#### <a name="category"></a>Kategoria

Wybierz co najmniej jedną i maksymalnie pięć kategorii. Kategorie te służą do umieszczania oferty w odpowiednich obszarach wyszukiwania w portalu marketplace i są wyświetlane na stronie szczegółów oferty. W opisie oferty wyjaśnij, w jaki sposób Twoja oferta obsługuje te kategorie. Na stronach przeglądania wszystkie moduły usługi IoT Edge są wyświetlane w kategorii  **modułu Internet of Things > IoT Edge.**

#### <a name="legal"></a>Informacje prawne

Musisz podać warunki oferty. Dostępne są dwie opcje:

- Użyj umowy standardowej dla witryny Microsoft Commercial Marketplace.
- Podaj własne warunki.

##### <a name="standard-contract-for-the-microsoft-commercial-marketplace"></a>Standardowa umowa dla microsoft commercial marketplace

Oferujemy szablon kontraktu standardowego, aby ułatwić transakcje na rynku komercyjnym. Możesz zaoferować swoje rozwiązanie w ramach umowy standardowej, którą klienci muszą tylko raz sprawdzić i zaakceptować. Jest to dobra opcja, jeśli nie chcesz tworzyć niestandardowych warunków.

Aby dowiedzieć się więcej o umowie standardowej, zobacz [Umowa standardowa dla microsoftowego portalu Marketplace Komercyjnego](https://docs.microsoft.com/azure/marketplace/standard-contract). Możesz również pobrać [standardowy plik](https://go.microsoft.com/fwlink/?linkid=2041178) PDF kontraktu (upewnij się, że bloker wyskakujących wyskakujących wyskakujących jest wyłączony).

Aby użyć kontraktu standardowego, zaznacz pole wyboru **Użyj kontraktu standardowego dla komercyjnego portalu handlowego firmy Microsoft,** a następnie kliknij przycisk **Zaakceptuj**.

> [!NOTE]
> Po opublikowaniu oferty przy użyciu umowy standardowej dla komercyjnego portalu Microsoft nie można używać własnych niestandardowych warunków. Zaoferuj swoje rozwiązanie w ramach umowy standardowej lub na własnych warunkach.

![Ilustruje użycie pola wyboru Umowa standardowa dla komercyjnego rynku firmy Microsoft.](./media/iot-edge-module-creation/iot-edge-module-standard-contract-checkbox.png)

##### <a name="your-own-terms-and-conditions"></a>Twoje własne warunki

Aby podać własne warunki niestandardowe, wprowadź je w polu **Warunki.** W tym polu można wprowadzić nieograniczoną ilość znaków tekstu. Klienci muszą zaakceptować te warunki, zanim będą mogli wypróbować Twoją ofertę.

Wybierz **pozycję Zapisz pochyłość** przed przejściem do następnej sekcji Zaoferuj aukcję.

## <a name="offer-listing"></a>Oferta aukcji

W tym miejscu zdefiniujesz szczegóły oferty wyświetlane w portalu Marketplace. Obejmuje to nazwę oferty, opis, obrazy i tak dalej. Podczas konfigurowania tej oferty należy przestrzegać zasad opisanych na stronie zasad firmy Microsoft.

> [!NOTE]
> Szczegóły oferty nie muszą być w języku angielskim, jeśli opis oferty zaczyna się od wyrażenia"Ta aplikacja jest dostępna tylko w języku [nieanglojęzycznym]." Jest również w porządku, aby udostępnić przydatny link do oferowania treści w języku innym niż ten używany w szczegółach aukcji oferty.

### <a name="name"></a>Nazwa

Nazwa, którą tu wprowadzona zostanie, jest wyświetlana jako tytuł oferty. To pole jest wstępnie wypełnione tekstem wprowadzonym w polu **Alias oferty** podczas tworzenia oferty. Tę nazwę można później zmienić.

Nazwa:

- Może być znakiem towarowym (i możesz zawierać znaki towarowe lub symbole praw autorskich).
- Nie może mieć więcej niż 50 znaków.
- Nie można dołączać emotikonów.

### <a name="search-results-summary"></a>Podsumowanie wyników wyszukiwania

Podaj krótki opis swojej oferty. Może to mieć do 100 znaków i jest używana w wynikach wyszukiwania w portalu Marketplace.

### <a name="long-summary"></a>Długie podsumowanie

Podaj bardziej szczegółowy opis swojej oferty. Może to mieć do 256 znaków i jest używana w wynikach wyszukiwania w marketplace.

### <a name="description"></a>Opis

Podaj dłuższy opis swojej oferty, do 3000 znaków. Jest to wyświetlane klientom w przeglądzie aukcji w portalu marketplace.

W opisie należy uwzględnić co najmniej jedną z następujących opcji:

- Wartość i kluczowe korzyści, jakie zapewnia Twoja oferta
- Kategorie lub stowarzyszenia branżowe, lub oba
- Możliwości zakupu w aplikacji
- Wszelkie wymagane informacje

Oferty modułów usługi IoT Edge muszą zawierać minimalną akapit wymagań sprzętowych u dołu opisu. Przykład:

*Minimalne wymagania sprzętowe: Linux x64 i arm32 OS, 1 GB pamięci RAM, 500 Mb pamięci masowej*

Oto kilka wskazówek dotyczących pisania opisu:

- Wyraźnie opisz wartość swojej oferty w pierwszych kilku zdaniach opisu. Dołącz następujące elementy:
    - Opis oferty.
    - Typ użytkownika, który korzysta z oferty.
    - Klient potrzebuje lub wydaje adresy ofert.
- Pamiętaj, że kilka pierwszych zdań może być wyświetlanych w wynikach wyszukiwania.
- Nie polegaj na funkcjach i funkcjach sprzedaży produktu. Zamiast tego skup się na wartości, która zapewnia twoja oferta.
- Spróbuj użyć słownictwa branżowego lub sformułowania opartego na korzyściach.

Aby twoja oferta **Opis** był bardziej atrakcyjny, użyj edytora tekstu sformatowania opisu. Edytor tekstu sformatowego umożliwia dodawanie liczb, punktorów, pogrubienia, kursywy i wcjęcia, aby opis był bardziej czytelny.

:::image type="content" source="media/text-editor2.png" alt-text="Ilustruje edytor tekstu sformatacyjnego." border="false":::

- Aby zmienić format zawartości, wyróżnij tekst, który chcesz sformatować, i wybierz styl tekstu, jak pokazano na tym zrzucie ekranu:

     :::image type="content" source="media/text-editor3.png" alt-text="Ilustruje kontrolę stylu tekstu w edytorze tekstu sformatacyjnego." border="false":::

- Aby dodać do tekstu listę punktowaną lub ponumerowaną, użyj opcji pokazanych na tym zrzucie ekranu:
  
    :::image type="content" source="media/text-editor4.png" alt-text="Ilustruje kontrolki listy punktowanej i numerowanej w edytorze tekstu sformatowanego." border="false":::

- Aby dodać lub usunąć wcięcie do tekstu, użyj opcji pokazanych na tym zrzucie ekranu:

    :::image type="content" source="media/text-editor5.png" alt-text="Ilustruje formanty wcięci w edytorze tekstu sformatacyjnego." border="false":::

#### <a name="privacy-policy-url"></a>Adres URL polityki prywatności

Wprowadź adres internetowy zasad ochrony prywatności organizacji. Użytkownik jest odpowiedzialny za zapewnienie, że Twoja oferta jest zgodna z przepisami i regulacjami dotyczącymi prywatności. Jesteś również odpowiedzialny za opublikowanie ważnej polityki prywatności na swojej stronie internetowej.

#### <a name="useful-links"></a>Przydatne łącza

Podaj dodatkowe dokumenty online dotyczące swojej oferty. Możesz dodać maksymalnie 25 linków. Aby dodać łącze, wybierz **+ Dodaj łącze,** a następnie wypełnij następujące pola:

- **Tytuł** — na stronie szczegółów oferty klienci zobaczą tytuł.
- **Link (URL)** — wprowadź link dla klientów, aby wyświetlić dokument online. Łącze musi zaczynać się od http:// lub https://.

Pamiętaj, aby dodać co najmniej jedno łącze do dokumentacji i jedno łącze do zgodnych urządzeń usługi IoT Edge z [katalogu urządzeń Usługi Azure IoT](https://catalog.azureiotsolutions.com/).

### <a name="contact-information"></a>Informacje kontaktowe

Musisz podać nazwę, adres e-mail i numer telefonu dla **kontaktu pomocy technicznej** i kontaktu **inżyniera.** Te informacje nie są wyświetlane klientom. Jest on dostępny dla firmy Microsoft i może być dostarczany partnerom dostawcy rozwiązań w chmurze (CSP).

- Kontakt pomocy technicznej (wymagany): w przypadku ogólnych pytań dotyczących pomocy technicznej.
- Kontakt techniczny (wymagany): w przypadku pytań technicznych i zagadnień certyfikacyjnych.
- Kontakt z programem CSP (opcjonalnie): W przypadku pytań odsprzedawców związanych z programem CSP.

W sekcji **Kontakt pomocy technicznej** podaj adres internetowy **witryny pomocy technicznej,** w której partnerzy mogą znaleźć pomoc techniczną dla Twojej oferty na podstawie tego, czy oferta jest dostępna na globalnej platformie Azure, platformie Azure dla instytucji rządowych lub obu.

W sekcji **Kontakt programu CSP** podaj link **(Materiały marketingowe programu CSP),** w którym partnerzy dostawcy CSP mogą znaleźć materiały marketingowe dla Twojej oferty.

#### <a name="additional-marketplace-listing-resources"></a>Dodatkowe zasoby dotyczące aukcji w portalu marketplace

Aby dowiedzieć się więcej o tworzeniu ofert, zobacz [Najlepsze wskazówki dotyczące oferty.](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)

### <a name="marketplace-images"></a>Obrazy z Marketplace

Podaj logo i obrazy do wykorzystania w swojej ofercie. Wszystkie obrazy muszą być w formacie png. Rozmazane obrazy zostaną odrzucone.

>[!Note]
>Jeśli masz problem z przekazywaniem plików, upewnij się, https://upload.xboxlive.com że sieć lokalna nie blokuje usługi używanej przez Centrum partnerów.

#### <a name="store-logos"></a>Logo sklepu

Podaj pliki png logo oferty w każdym z następujących rozmiarów czterech pikseli:

- **Mały (48 x 48)**
- **Średni (90 x 90)**
- **Duży (216 x 216)**
- **Szeroki (255 x 115)**

Wszystkie cztery logo są wymagane i są używane w różnych miejscach na liście marketplace.

#### <a name="screenshots-optional"></a>Zrzuty ekranu (opcjonalnie)

Dodaj do pięciu zrzutów ekranu, które pokazują, jak działa Twoja oferta. Każdy musi mieć rozmiar 1280 x 720 pikseli i w formacie png.

#### <a name="videos-optional"></a>Filmy (opcjonalnie)

Dodaj do pięciu filmów, które pokazują twoją ofertę. Wprowadź nazwę filmu, jego adres internetowy i miniaturę obrazu .png wideo o wymiarach 1280 x 720 pikseli.

#### <a name="offer-examples"></a>Przykłady dotyczące ofert

Poniższe przykłady pokazują, jak pola aukcji oferty są wyświetlane w różnych miejscach oferty.

Ten zrzut ekranu przedstawia stronę **Lista ofert** w portalu Azure Marketplace.

:::image type="content" source="media/iot-edge-module-creation/iot-edge-module-offer-listing-page.png" alt-text="Ilustruje stronę z ofertą w portalu Azure Marketplace.":::

Ten zrzut ekranu przedstawia wyniki wyszukiwania w portalu Azure Marketplace:

:::image type="content" source="media/iot-edge-module-creation/iot-edge-module-marketplace-search-results.png" alt-text="Ilustruje wyniki wyszukiwania w portalu Azure Marketplace.":::

Ten zrzut ekranu przedstawia stronę **Lista ofert** w witrynie Azure portal.

:::image type="content" source="media/iot-edge-module-creation/iot-edge-module-listing-page-azure-portal.png" alt-text="Ilustruje stronę lista ofert w witrynie Azure portal.":::

Ten zrzut ekranu przedstawia wyniki wyszukiwania w witrynie Azure portal.

:::image type="content" source="media/iot-edge-module-creation/iot-edge-module-azure-portal-search-results.png" alt-text="Ilustruje stronę lista ofert w witrynie Azure portal.":::

Wybierz **pozycję Zapisz wersję roboczą** przed przejściem do następnej sekcji, podgląd.

## <a name="preview"></a>Wersja zapoznawcza

Na **karcie Podgląd**możesz wybrać ograniczoną **grupę odbiorców w wersji zapoznawczej,** aby zweryfikować swoją ofertę przed opublikowaniem jej na żywo szerszej grupy odbiorców w rynku.

> [!IMPORTANT]
> Po wyświetleniu oferty w wersji zapoznawczej musisz wybrać **opcję Przejdź na żywo,** aby opublikować ofertę publicznie.

Określ odbiorców w wersji zapoznawczej przy użyciu identyfikatorów GUID subskrypcji platformy Azure wraz z opcjonalnym opisem dla każdego z nich. Żadne z tych pól nie może być postrzegane przez klientów.

> [!NOTE]
> Identyfikator subskrypcji platformy Azure można znaleźć na stronie Subskrypcje w witrynie Azure portal.

Dodaj co najmniej jeden identyfikator subskrypcji platformy Azure, indywidualnie (do 10) lub przekazując plik CSV (do 100). Dodając te identyfikatory subskrypcji, można określić, kto może wyświetlić podgląd oferty przed jej opublikowaniem na żywo. Jeśli oferta jest już dostępna, możesz zdefiniować grupę odbiorców w wersji zapoznawczej, aby przetestować zmiany lub aktualizacje oferty.

> [!NOTE]
> Grupa odbiorców podglądu różni się od grupy prywatnej. Grupa odbiorców **w wersji zapoznawczej** może zobaczyć i potwierdzić wszystkie plany oferty, zanim będą dostępne na rynku, w tym plany, które zostaną opublikowane tylko **dla odbiorców prywatnych** (ustawione na karcie Dostępność).

Wybierz **pozycję Zapisz pochyłość** przed przejściem do następnej sekcji Przegląd planu.

### <a name="plan-overview"></a>Omówienie planu

Ta karta umożliwia udostępnić różne opcje planu w ramach tej samej oferty w Centrum partnerów. Plany te, wcześniej określane jako jednostki SKU lub jednostki przechowywania zapasów. Plany mogą się różnić pod względem tego, jakie chmury są dostępne, takie jak chmury globalne, chmury rządowe i obraz, do którego odwołuje się plan. Aby wyświetlić ofertę na rynku, należy skonfigurować co najmniej jeden plan.

Po utworzeniu planów na karcie **Przegląd planu** jest wyświetlany:

- Nazwy planów
- Model cen
- Dostępność chmury (globalna lub rządowa)
- Bieżący stan publikowania
- Wszelkie dostępne działania

Działania dostępne w przeglądzie planu różnią się w zależności od bieżącego stanu planu. Obejmują one następujące raporty:

- **Usuń wersja robocza**: Jeśli stan planu to Wersja robocza.
- **Zatrzymaj plan sprzedaży:** Jeśli stan planu jest publikowany na żywo.

#### <a name="create-new-plan"></a>Tworzenie nowego planu

Wybierz **pozycję Utwórz nowy plan**. Zostanie wyświetlone okno dialogowe **Nowy plan.**

W polu **Identyfikator planu** utwórz unikatowy identyfikator planu dla każdego planu w tej ofercie. Ten identyfikator będzie widoczny dla klientów w adresie internetowym produktu. Używaj tylko małych liter i cyfr, kresek lub podkreśleń oraz maksymalnie 50 znaków.

W polu **Nazwa planu** wprowadź nazwę tego planu. Klienci widzą tę nazwę przy podejmowaniu decyzji, który plan wybrać w ramach oferty. Utwórz unikatową nazwę dla każdego planu w tej ofercie. Na przykład można użyć nazwy oferty **systemu Windows Server** z planami Windows Server **2016** i **Windows Server 2019**.

> [!NOTE]
> Identyfikator planu nie można zmienić po wybraniu opcji **Utwórz**.

Wybierz pozycję **Utwórz**.

### <a name="plan-setup"></a>Konfiguracja planu

Ta karta umożliwia skonfigurowanie chmur, w których plan jest dostępny. Odpowiedzi na tej karcie mają wpływ na to, które pola są wyświetlane na innych kartach.

#### <a name="cloud-availability"></a>Dostępność chmury

Plan musi być dostępny w co najmniej jednej chmurze przy użyciu usługi Azure IoT Hub.

Wybierz opcję **Azure Global,** aby twój plan mógł być używany przez klientów we wszystkich globalnych regionach platformy Azure korzystających z portalu Marketplace. Aby uzyskać szczegółowe informacje, zobacz [Dostępność geograficzna i obsługa walut](https://docs.microsoft.com/azure/marketplace/marketplace-geo-availability-currencies).

Wybierz opcję [Azure Government Cloud,](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome) aby twoje rozwiązanie pojawiło się tutaj. Jest to chmura społeczności rządowej z kontrolowanym dostępem dla klientów z amerykańskich federalnych, stanowych i lokalnych lub plemiennych agencji rządowych, a także partnerów uprawnionych do ich obsługi. Jako wydawca jesteś odpowiedzialny za wszelkie kontrole zgodności, środki zabezpieczeń i najlepsze rozwiązania dla tej społeczności w chmurze. Platforma Azure Government używa fizycznie odizolowanych centrów danych i sieci (zlokalizowanych tylko w Stanach Zjednoczonych). Przed [opublikowaniem](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners) w usłudze Azure Government należy przetestować i potwierdzić rozwiązanie w tym obszarze, ponieważ wyniki mogą być różne. Aby zaaslić i przetestować rozwiązanie, poproś o konto próbne z [wersji próbnej platformy Microsoft Azure dla instytucji rządowych.](https://azure.microsoft.com/global-infrastructure/government/request/)

> [!NOTE]
> Po opublikowaniu planu i udostępnieniu go w określonej chmurze nie można usunąć tej chmury.

#### <a name="azure-government-cloud-certifications"></a>Certyfikaty azure dla instytucji rządowych w chmurze

Ta opcja jest widoczna tylko wtedy, gdy w obszarze **Dostępność chmury wybrano**usługę **Azure Government Cloud** .

Usługi azure dla instytucji rządowych obsługują dane podlegające określonym przepisom i wymaganiom rządowym. Na przykład FedRAMP, NIST 800.171 (DIB), ITAR, IRS 1075, DoD L4 i CJIS. Aby zwiększyć świadomość certyfikatów dla tych programów, możesz podać maksymalnie 100 linków opisujących certyfikaty. Mogą to być linki do twoich aukcji w programie bezpośrednio lub do własnej strony internetowej. Te łącza są widoczne tylko dla klientów platformy Azure dla instytucji rządowych.

## <a name="plan-listing"></a>Lista planu

Na tej karcie są wyświetlane określone informacje dotyczące każdego planu w ramach tej samej oferty.

### <a name="plan-name"></a>Nazwa planu

Jest to wstępnie wypełnione nazwą, którą nadano planowi podczas jego tworzenia. W razie potrzeby można zmienić tę nazwę. Może mieć do 50 znaków. Ta nazwa jest wyświetlana jako tytuł tego planu w portalu Azure Marketplace i Azure Portal. Jest używany jako domyślna nazwa modułu po plan jest gotowy do użycia.

### <a name="plan-summary"></a>Podsumowanie planu

Podaj krótkie podsumowanie planu (nie ofertę). To podsumowanie jest wyświetlane w wynikach wyszukiwania w portalu Azure Marketplace i może zawierać maksymalnie 100 znaków.

### <a name="plan-description"></a>Opis planu

Opisz, co sprawia, że ten plan jest wyjątkowy, a także różnice między planami w ramach oferty. Nie opisuj oferty, tylko plan. Ten opis pojawi się w portalu Azure Marketplace i w witrynie Azure portal na stronie Lista ofert. Może to być ta sama zawartość, która została podana w podsumowaniu planu i zawierać do 2000 znaków.

Po zakończeniu tych pól wybierz **pozycję Zapisz pochylenia** wersji roboczej.

#### <a name="plan-examples"></a>Przykłady planu

Poniższe przykłady pokazują, jak pola listy planu są wyświetlane w różnych widokach.

Są to pola w portalu Azure Marketplace podczas wyświetlania szczegółów planu:

:::image type="content" source="media/iot-edge-module-creation/iot-edge-module-marketplace-plan-details.png" alt-text="Ilustruje pola wyświetlane podczas wyświetlania szczegółów planu w portalu Azure Marketplace.":::

Oto szczegóły planu w witrynie Azure portal:

:::image type="content" source="media/iot-edge-module-creation/iot-edge-module-azure-portal-plan-details.png" alt-text="Ilustruje szczegóły planu w witrynie Azure portal.":::

## <a name="availability"></a>Dostępność

Jeśli chcesz ukryć opublikowaną ofertę, aby klienci nie mogli jej wyszukiwać, przeglądać ani kupować w portalu marketplace, zaznacz pole wyboru **Ukryj plan** na karcie Dostępność.

To pole jest powszechnie używane, gdy:

- Oferta ma być używana tylko pośrednio, gdy odwołuje się do innej aplikacji.
- Oferty nie należy kupować indywidualnie.
- Plan został użyty do wstępnego testowania i nie jest już istotny.
- Plan był używany do ofert tymczasowych lub sezonowych i nie powinien być już oferowany.

## <a name="technical-configuration"></a>Konfiguracja techniczna

Typ oferty **modułu usługi IoT Edge** jest określonym typem kontenera, który działa na urządzeniu usługi IoT Edge. Na karcie **Konfiguracja techniczna** podasz informacje referencyjne dla repozytorium obrazów kontenera wewnątrz [rejestru kontenerów platformy Azure](https://azure.microsoft.com/services/container-registry/)wraz z ustawieniami konfiguracji, które umożliwiają klientom łatwe korzystanie z modułu.

Po opublikowaniu oferty obraz kontenera usługi IoT Edge jest kopiowany do portalu Azure Marketplace w określonym rejestrze kontenerów publicznych. Wszystkie żądania od użytkowników platformy Azure do korzystania z modułu są obsługiwane z rejestru kontenerów publicznych w portalu Azure Marketplace, a nie z rejestru kontenerów prywatnych.

Można kierować wiele platform i podać kilka wersji obrazu kontenera modułu za pomocą tagów. Aby dowiedzieć się więcej o tagach i przechowywanie wersji, zobacz [Przygotowywanie zasobów technicznych modułu Usługi IoT Edge](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-iot-edge-module-asset).

### <a name="image-repository-details"></a>Szczegóły repozytorium obrazów

Na karcie **Szczegóły repozytorium obrazów** znajdziesz następujące informacje.

**Wybierz źródło obrazu:** Wybierz opcję **Rejestru kontenerów platformy Azure.**

**Identyfikator subskrypcji platformy Azure:** Podaj identyfikator subskrypcji, w którym jest zgłaszane użycie zasobów, a usługi są rozliczane za rejestr kontenerów platformy Azure, który zawiera obraz kontenera. Ten identyfikator można znaleźć na [stronie Subskrypcje](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) w witrynie Azure portal.

**Nazwa grupy zasobów platformy Azure:** Podaj nazwę [grupy zasobów](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resource-groups-portal) zawierającą rejestr kontenerów platformy Azure z obrazem kontenera. Grupa zasobów musi być dostępna w identyfikatorze subskrypcji (powyżej). Nazwę można znaleźć na stronie [Grupy zasobów](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceGroups) w witrynie Azure portal.

**Nazwa rejestru kontenerów platformy Azure:** Podaj nazwę [rejestru kontenerów platformy Azure,](https://docs.microsoft.com/azure/container-registry/container-registry-intro) który ma obraz kontenera. Rejestr kontenerów musi znajdować się w grupie zasobów platformy Azure, która została podana wcześniej. Podaj tylko nazwę rejestru, a nie pełną nazwę serwera logowania. Pamiętaj, aby pominąć **azurecr.io** z nazwy. Nazwę rejestru można znaleźć na [stronie Rejestry kontenerów](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.ContainerRegistry%2Fregistries) w witrynie Azure portal.

**Nazwa użytkownika administratora dla rejestru kontenerów platformy Azure:** Podaj [nazwę użytkownika administratora](https://docs.microsoft.com/azure/container-registry/container-registry-authentication#admin-account) skojarzoną z rejestrem kontenerów platformy Azure, który ma obraz kontenera. Nazwa użytkownika i hasło są wymagane, aby upewnić się, że twoja firma ma dostęp do rejestru. Aby uzyskać nazwę użytkownika i hasło administratora, ustaw właściwość **z włączoną przez administratora** na **True** przy użyciu interfejsu wiersza polecenia platformy Azure (CLI). Opcjonalnie można ustawić **użytkownika administratora,** aby **włączyć** w witrynie Azure portal.

:::image type="content" source="media/iot-edge-module-creation/iot-edge-module-admin-user.png" alt-text="Ilustruje okno dialogowe Aktualizowanie rejestru kontenerów.":::

**Hasło do rejestru kontenerów platformy Azure:** Podaj hasło dla nazwy użytkownika administratora skojarzonego z rejestrem kontenerów platformy Azure i ma obraz kontenera. Nazwa użytkownika i hasło są wymagane, aby upewnić się, że twoja firma ma dostęp do rejestru. Hasło można uzyskać z witryny Azure portal, przechodząc do **kluczy**dostępu rejestru > **kontenerów** lub za pomocą interfejsu wiersza polecenia platformy Azure przy użyciu [polecenia show.](https://docs.microsoft.com/cli/azure/acr/credential?view=azure-cli-latest#az-acr-credential-show)

:::image type="content" source="media/iot-edge-module-creation/iot-edge-module-username-password.png" alt-text="Ilustruje ekran klucza dostępu w witrynie Azure portal.":::

**Nazwa repozytorium w rejestrze kontenerów platformy Azure**. Podaj nazwę repozytorium rejestru kontenerów platformy Azure, które ma obraz. Należy określić nazwę repozytorium podczas wypychania obrazu do rejestru. Nazwę repozytorium można znaleźć, przechodząc na > **stronę Repozytoria** [rejestru kontenerów](https://azure.microsoft.com/services/container-registry/). Aby uzyskać więcej informacji, zobacz [Wyświetlanie repozytoriów rejestru kontenerów w witrynie Azure portal](https://docs.microsoft.com/azure/container-registry/container-registry-repositories). Należy zauważyć, że po ustawieniu nazwy nie można jej zmienić. Użyj unikatowej nazwy dla każdej oferty na swoim koncie.

### <a name="image-tags-for-new-versions-of-your-offer"></a>Znaczniki obrazów dla nowych wersji oferty

Klienci muszą mieć możliwość automatycznego uruchamiania aktualizacji z portalu Azure Marketplace podczas publikowania aktualizacji. Jeśli nie chcą aktualizować, muszą mieć możliwość pozostania w określonej wersji obrazu. Można to zrobić, dodając nowe znaczniki obrazu za każdym razem, gdy wprowadzasz aktualizację do obrazu.

**Znacznik obrazu**. To pole musi zawierać **najnowszy** znacznik, który wskazuje najnowszą wersję obrazu na wszystkich obsługiwanych platformach. Musi również zawierać tag wersji (na przykład, począwszy od xx.xx.xx, gdzie xx jest liczbą). Klienci powinni używać [znaczników manifestu](https://github.com/estesp/manifest-tool) do kierowania na wiele platform. Wszystkie tagi, do których odwołuje się znacznik manifestu, muszą również zostać dodane, abyśmy mogli je przesłać. Wszystkie znaczniki manifestu (z wyjątkiem najnowszego tagu) muszą zaczynać się od X.Y- lub X.Y.Z- gdzie X, Y i Z są liczbami całkowitymi. Na przykład, jeśli najnowszy tag wskazuje na 1.0.1-linux-x64, 1.0.1-linux-arm32 i 1.0.1-windows-arm32, te sześć tagów należy dodać do tego pola. Aby uzyskać szczegółowe informacje na temat tagów i przechowywania wersji, zobacz [Przygotowywanie zasobów technicznych modułu usługi IoT Edge.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/iot-edge-module/cpp-create-technical-assets)

### <a name="default-deployment-settings-optional"></a>Domyślne ustawienia wdrażania (opcjonalnie)

Zdefiniuj najczęściej stosowane ustawienia, aby wdrożyć moduł IoT Edge. Zoptymalizuj wdrożenia klientów, umożliwiając im uruchomienie modułu Usługi IoT Edge po wyjęciu z tych ustawień domyślnych.

**Trasy domyślne**. Centrum usługi IoT Edge Hub zarządza komunikacją między modułami, centrum IoT Hub i urządzeniami. Można ustawić trasy dla danych wejściowych i wyjściowych między modułami i Usługi IoT Hub, co zapewnia elastyczność wysyłania wiadomości, gdzie trzeba przejść bez konieczności dodatkowych usług do przetwarzania wiadomości lub pisania dodatkowego kodu. Trasy są tworzone przy użyciu par nazw/wartości. Można zdefiniować maksymalnie pięć domyślnych nazw tras o długości do 512 znaków.

Pamiętaj, aby użyć poprawnej [składni trasy](https://docs.microsoft.com/azure/iot-edge/module-composition#declare-routes) w wartości trasy (zwykle zdefiniowanej jako FROM/message/* INTO $upstream). Oznacza to, że wszystkie wiadomości wysyłane przez moduły przejść do usługi IoT Hub. Aby odwołać się do modułu, użyj jego domyślnej nazwy modułu, która będzie **nazwą oferty**, bez spacji lub znaków specjalnych. Aby odwołać się do innych modułów, które nie są jeszcze znane, użyj <FROM_MODULE_NAME> konwencji, aby poinformować klientów, że muszą zaktualizować te informacje. Aby uzyskać szczegółowe informacje o trasach IoT Edge, zobacz [Deklarowanie tras](https://docs.microsoft.com/azure/iot-edge/module-composition#declare-routes).

Na przykład jeśli moduł ContosoModule nasłuchuje danych wejściowych na ContosoInput i danych wyjściowych w ContosoOutput, warto zdefiniować następujące dwie trasy domyślne:

- Nazwa #1: ToContosoModule
- Wartość #1: FROM /messages/modules/<FROM_MODULE_NAME>/outputs/* INTO BrokeredEndpoint("/modules/ContosoModule/inputs/ContosoInput")
- Nazwa #2: FromContosoModuleToCloud
- Wartość #2: FROM /messages/modules/ContonsoModule/outputs/ContosoOutput INTO $upstream

**Domyślne modułu bliźniaczej żądane właściwości**. Bliźniacza reprezentacja modułu jest dokumentem JSON w Centrum IoT, który przechowuje informacje o stanie wystąpienia modułu, w tym żądane właściwości. Żądane właściwości są używane wraz ze zgłoszonymi właściwościami w celu synchronizacji konfiguracji lub warunków modułu. Wewnętrznej bazy danych rozwiązania można ustawić żądane właściwości i moduł może je odczytać. Moduł może również odbierać powiadomienia o zmianach w żądanych właściwościach. Żądane właściwości są tworzone przy użyciu maksymalnie pięciu par nazw/wartości, a każda wartość domyślna musi być mniejsza niż 512 znaków. Można zdefiniować maksymalnie pięć nazw/wartości bliźniaczej reprezentacji żądanych właściwości. Wartości wstępnie pożądanych właściwości muszą być prawidłowe JSON, bez zmiany znaczenia, bez tablic o maksymalnej hierarchii zagnieżdżonej czterech poziomów. W scenariuszu, w którym parametr wymagany dla wartości domyślnej nie ma sensu (na przykład adres IP serwera klienta), można dodać parametr jako wartość domyślną. Aby dowiedzieć się więcej o bliźniaczych żądanych właściwościach, zobacz [Definiowanie lub aktualizowanie żądanych właściwości](https://docs.microsoft.com/azure/iot-edge/module-composition#define-or-update-desired-properties).

Na przykład jeśli moduł obsługuje dynamicznie konfigurowalną częstotliwość odświeżania przy użyciu dwóch żądanych właściwości, warto zdefiniować następującą domyślną właściwość bliźniaczej reprezentacji:

- Nazwa #1: RefreshRate
- Wartość #1: 60

**Domyślne zmienne środowiskowe**. Zmienne środowiskowe dostarczają dodatkowych informacji do modułu, który pomaga w procesie konfiguracji. Zmienne środowiskowe są tworzone przy użyciu par nazw/wartości. Każda domyślna nazwa zmiennej środowiskowej i wartość musi być mniejsza niż 512 znaków i można zdefiniować maksymalnie pięć znaków. Jeśli parametr wymagany dla wartości domyślnej nie ma sensu (na przykład adres IP serwera klienta), można dodać parametr jako wartość domyślną.

Na przykład jeśli moduł wymaga zaakceptowania warunków użytkowania przed rozpoczęciem, można zdefiniować następującą zmienną środowiskową:

- Imię #1: ACCEPT_EULA
- Wartość #1: Y

**Domyślne opcje tworzenia kontenera**. Opcje tworzenia kontenera kierują tworzenie kontenera platformy Docker modułu Usługi IoT Edge. Usługa IoT Edge obsługuje opcje tworzenia kontenera z aparatem platformy Docker. Zobacz wszystkie opcje w [kontenerach listy.](https://docs.docker.com/engine/api/v1.30/#operation/ContainerList) Pole opcji tworzenia musi być prawidłowe JSON, bez zmiany znaczenia i mniejsze niż 512 znaków.

Na przykład jeśli moduł wymaga powiązania portów, zdefiniuj następujące opcje tworzenia:

"HostConfig":{"PortBindings":{"5012/tcp":[{"HostPort":"5012"}]}

## <a name="review-and-publish"></a>Przeglądanie i publikowanie

Po wypełnić wszystkie wymagane sekcje oferty, można przesłać go do przeglądu i opublikowania.

W prawym górnym rogu portalu wybierz pozycję **Przejrzyj i opublikuj**.

Na stronie recenzji możesz zobaczyć stan publikowania:

- Zobacz stan ukończenia każdej sekcji oferty. Nie możesz opublikować, dopóki wszystkie sekcje oferty nie zostaną oznaczone jako kompletne.
    - **Nie rozpoczęto** — sekcja nie została uruchomiona i musi zostać ukończona.
    - **Niekompletne** — sekcja zawiera błędy, które muszą zostać naprawione lub wymagają podania większej ilości informacji. Zapoznaj się z sekcjami we wcześniejszej części tego dokumentu, aby uzyskać wskazówki.
    - **Ukończono** — sekcja zawiera wszystkie wymagane dane i nie ma żadnych błędów. Przed złożeniem oferty należy wypełnić wszystkie sekcje oferty.
- Przekaż zespołowi certyfikacyjnemu instrukcje testowania, aby upewnić się, że oferta jest poprawnie przetestowana. Ponadto należy przedstawić wszelkie dodatkowe uwagi, które są pomocne w zrozumieniu twojej oferty.

Aby przesłać ofertę publikacji, wybierz pozycję **Publikuj**.

Wyślemy Ci wiadomość e-mail z powiadomieniem, kiedy wersja zapoznawcza oferty będzie dostępna do przejrzenia i zatwierdzenia. Aby opublikować ofertę publiczną (lub prywatną, dla odbiorców prywatnych), przejdź do Centrum partnerów i wybierz **opcję Przejdź na żywo.**

## <a name="next-steps"></a>Następne kroki

- [Aktualizowanie istniejącej oferty na rynku komercyjnym](https://docs.microsoft.com//azure/marketplace/partner-center-portal/update-existing-offer)