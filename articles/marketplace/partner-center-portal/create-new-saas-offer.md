---
title: Tworzenie nowej oferty SaaS dla komercyjnego rynku firmy Microsoft
description: Jak utworzyć nową ofertę Oprogramowania jako usługi (SaaS) do wystawiania lub sprzedawania w witrynie Microsoft AppSource, Azure Marketplace lub za pośrednictwem programu Dostawcy rozwiązań w chmurze (CSP) przy użyciu komercyjnego programu marketplace firmy Microsoft w Centrum partnerów firmy Microsoft.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/13/2020
ms.openlocfilehash: a39e1e19f65722b4b5ae809ca943da719a3c6e22
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869773"
---
# <a name="create-a-new-saas-offer"></a>Tworzenie nowej oferty SaaS

Aby rozpocząć tworzenie ofert oprogramowania jako usługi (SaaS), należy najpierw [utworzyć konto Centrum partnerów](./create-account.md) i otworzyć [pulpit nawigacyjny portalu komercyjnego w portalu ,](https://partner.microsoft.com/dashboard/commercial-marketplace/offers)z wybraną kartą **Przegląd.**

![Pulpit nawigacyjny portalu Marketplace w Centrum partnerów](./media/new-offer-overview.png)

>[!Note]
> Po opublikowaniu oferty zmiany w ofercie złożonej w Centrum partnerskim zostaną zaktualizowane dopiero po ponownym opublikowaniu w systemie i na frontach sklepu. Po dokonaniu zmian upewnij się, że składasz ofertę do publikacji.

Wybierz + **Nowa oferta...** , wybierz pozycję menu **Oprogramowanie jako usługę.**

Jeśli wybierzesz inny typ oferty, możesz zostać przekierowany do starszego [portalu Cloud Partner.](https://cloudpartner.azure.com/) W tej chwili w portalu Commercial Marketplace w Centrum partnerów dostępne są tylko oferty SaaS i Dynamics 365.

![Tworzenie okna oferty w Centrum partnerów](./media/new-offer-click.png)

Zostanie wyświetlone okno dialogowe **Nowa oferta.**

![Okno dialogowe Nowa oferta](./media/new-offer-popup.png)

## <a name="offer-id-and-alias"></a>Identyfikator oferty i alias

- **Identyfikator oferty:** Unikatowy identyfikator dla każdej oferty na twoim koncie. Ten identyfikator będzie widoczny dla klientów w adresie URL oferty portalu Marketplace i szablonach usługi Azure Resource Manager (jeśli dotyczy). Identyfikator oferty musi być małe, alfanumeryczne (w tym łączniki i podkreślenia, ale nie odstępy). Identyfikator **oferty** jest ograniczony do 50 znaków i nie można go zmienić po wybraniu opcji *Utwórz*.  
Przykład: test-offer-1
<br>Wynikowy adres URL:`https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`

- **Alias oferty**: Nazwa używana do odwoływania się do oferty w portalu Centrum partnerów. Ta nazwa nie będzie używana w portalu marketplace i różni się od *nazwy oferty* i innych wartości, które będą wyświetlane klientom. Tej wartości nie można zmienić po wybraniu opcji *Utwórz*.

<br>Przykład: Oferta testowa 1&#8482;

Wybierz pozycję **Utwórz**.  Dla tej oferty zostanie utworzona strona **Przegląd oferty.**  

<!---
![Offer overview on Partner Center](./media/commercial-marketplace-offer-overview.png)
-->

## <a name="offer-overview"></a>Przegląd oferty

Strona **Przegląd oferty** zawiera:

- **Stan publikowania** wyświetla wizualną reprezentację kroków wymaganych do opublikowania tej oferty i czasu, jaki zajmie wykonanie każdego kroku. Niekompletne ikony kroków publikowania zostaną wyszarzone.

- Menu **Przegląd oferty** zawiera listę łączy do wykonywania operacji w tej ofercie. Ta lista operacji zmieni się w zależności od wyboru oferty.  
    - Jeśli oferta jest wersja robocza — usuwanie wersji roboczej
    - Jeśli oferta jest na żywo - Zatrzymaj ofertę sprzedaży
    - Jeśli oferta jest w wersji zapoznawczej - Go-live
    - Jeśli nie zostało ukończone wylogowanie wydawcy — anulowanie publikowania

## <a name="offer-setup"></a>Konfiguracja oferty

Karta **Ustawienia oferty** prosi o następujące informacje. Wybierz **pozycję Zapisz** po wypełnieniu tych pól.

- **Czy chcesz sprzedawać za pośrednictwem firmy Microsoft?** (Tak/Nie)
    - **Tak,** chcesz sprzedać swoją ofertę za pośrednictwem firmy Microsoft, a microsoft hosting transakcji marketplace w Twoim imieniu; Lub 
    - **Nie,** wolisz po prostu wyświetlić listę ofert za pośrednictwem rynków, przetwarzając wszelkie transakcje pieniężne niezależnie od firmy Microsoft.

### <a name="sell-through-microsoft"></a>Sprzedawaj za pośrednictwem firmy Microsoft

Sprzedaż za pośrednictwem firmy Microsoft zapewnia lepsze wykrywanie i pozyskiwanie klientów, umożliwia firmie Microsoft hostowanie transakcji w portalu Marketplace w twoim imieniu i korzysta z dostępnych na całym świecie możliwości handlowych firmy Microsoft.

#### <a name="saas-offer-requirements"></a>Wymagania oferty SaaS

Aby wymienić oferty Oprogramowania jako usługi (SaaS) w Commercial Marketplace on Partner Center, muszą być spełnione następujące kryteria:

- Oferta musi używać [usługi Azure Active Directory (Azure AD)](https://azure.microsoft.com/services/active-directory/) do zarządzania tożsamościami i uwierzytelniania.
- Oferta musi używać interfejsów API realizacji usługi [SaaS](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-fulfillment-api-v2) do integracji z portalem Azure Marketplace.
- Aby uzyskać bardziej rozbudowane wymagania, zobacz [Przewodnik publikowania ofert SaaS](https://docs.microsoft.com/azure/marketplace/marketplace-saas-applications-technical-publishing-guide).

#### <a name="saas-pricing-and-billing-options"></a>Opcje cen i rozliczeń SaaS

Dzięki rozwiązaniom SaaS uruchomionym w ramach subskrypcji platformy Azure wydawcy opłaty licencyjne uiszczane przez klientów obejmują koszt infrastruktury, na której oprogramowanie jest wdrażane. Użycie infrastruktury platformy Azure jest zarządzane i rozliczane bezpośrednio z tobą, partnerem. Rzeczywiste opłaty za korzystanie z infrastruktury nie są widoczne dla klienta. Wydawcy powinni łączyć opłaty za korzystanie z infrastruktury platformy Azure z cenami licencji na oprogramowanie. 

SaaS oferuje obsługę techniczną miesięczne lub roczne rozliczenia na podstawie opłaty ryczałtowej, opłaty za użytkownika lub opłaty za zużycie za pomocą usługi rozliczeniowej taryfowej. Komercyjny rynek firmy Microsoft działa w modelu agencji, w którym wydawcy ustalają ceny, Microsoft rozlicza klientów, a microsoft płaci wydawcy przychody, jednocześnie u źródła opłaty agencyjnej.

W poniższej tabeli przedstawiono przykładowy podział kosztów i wypłat w celu zademonstrowania modelu agencji.

|**Koszt licencji**|**$100 miesięcznie**|
|:---|:---|
|Koszt użycia platformy Azure (D1/1-Core)|Rozliczane bezpośrednio wydawcy, a nie klientowi|
|Klient jest rozliczany przez firmę Microsoft|$100.00 miesięcznie (Wydawca musi uwzględnić wszelkie poniesione lub przenoszone koszty infrastruktury w opłacie licencyjnej)|

|**Rachunki Firmy Microsoft**|**$100 miesięcznie**|
|:---|:---|
|Microsoft płaci 80% kosztów licencji <br>**W przypadku kwalifikujących się aplikacji SaaS firma Microsoft płaci 90% kosztów licencji*|$80.00 miesięcznie <br>*$* 90.00 miesięcznie*|

- W tym przykładzie firma Microsoft rozlicza klientowi 100,00 USD za licencję na oprogramowanie i wypłaca wydawcy 80,00 USD.
- Od maja 2019 r. do czerwca 2020 r. partnerzy, którzy zakwalifikowali się do **obniżonej opłaty za usługę w marketplace,** otrzymają obniżoną opłatę transakcyjną w ofertach SaaS. W tym scenariuszu Microsoft rozlicza $100.00 za licencję oprogramowania i wypłaca wydawcy $90.00.

> [!NOTE]
> **Obniżona opłata za usługę Marketplace:** W przypadku niektórych ofert SaaS opublikowanych w naszym komercyjnym portalu Marketplace firma Microsoft obniży opłatę za usługę Marketplace z 20% (zgodnie z opisem w Umowie wydawcy firmy Microsoft) do 10%. Aby oferta mogła się zakwalifikować, co najmniej jedna z twoich ofert musi zostać wyznaczona przez firmę Microsoft jako będąca albo współsprzedaniem IP gotowym, albo priorytetem współsprzedaży IP.  Aby otrzymać obniżoną opłatę za usługę marketplace w danym miesiącu, należy spełnić uprawnienia co najmniej pięć (5) dni roboczych przed końcem każdego miesiąca kalendarzowego.  Obniżona opłata za usługę Marketplace nie ma zastosowania do maszyn wirtualnych, aplikacji zarządzanych ani żadnych innych produktów udostępnianych za pośrednictwem naszego komercyjnego portalu Marketplace.  Obniżona opłata za usługę Marketplace będzie dostępna tylko dla kwalifikowanych ofert opłat licencyjnych pobranych przez firmę Microsoft w okresie od 1 maja 2019 r. do 30 czerwca 2020 r.  Po tym czasie opłata za usługę marketplace powróci do normalnej kwoty.

### <a name="list-through-microsoft"></a>Lista za pośrednictwem firmy Microsoft

Promuj swoją firmę w firmie Microsoft, tworząc listę w portalu marketplace. Wybranie opcji, aby wyświetlić listę tylko ofert i nie dokonywać transakcji za pośrednictwem firmy Microsoft, oznacza, że firma Microsoft nie uczestniczy bezpośrednio w transakcjach licencji na oprogramowanie. Nie ma powiązanej opłaty transakcyjnej, a wydawca przechowuje 100% wszelkich opłat licencyjnych za oprogramowanie pobranych od klienta. Wydawca jest jednak odpowiedzialny za obsługę wszystkich aspektów transakcji licencji na oprogramowanie, w tym między innymi: realizacji zamówień, pomiaru, rozliczeń, fakturowania, płatności i zbierania.

- **Jak chcesz, aby potencjalni klienci wchodzili w interakcje z tą ofertą aukcji?**

#### <a name="get-it-now-free"></a>Pobierz go teraz (za darmo)

Wystaw swoją ofertę klientom bezpłatnie, podając prawidłowy adres URL (zaczynający się od *http* lub *https),* gdzie mogą uzyskać wersję próbną za pomocą [uwierzytelniania jednym kliknięciem przy użyciu usługi Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/marketplace/marketplace-saas-applications-technical-publishing-guide#using-azure-active-directory-to-enable-trials).  Na przykład: `https://contoso.com/saas-app`

#### <a name="free-trial-listing"></a>Bezpłatna wersja próbna (aukcja)

Wystaw ofertę klientom z łączem do bezpłatnej wersji próbnej, podając prawidłowy adres URL (zaczynający się od *http* lub *https),* gdzie mogą uzyskać wersję próbną za pomocą [uwierzytelniania jednym kliknięciem przy użyciu usługi Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/marketplace/marketplace-saas-applications-technical-publishing-guide#using-azure-active-directory-to-enable-trials).  Na przykład: `https://contoso.com/trial/saas-app`. Oferta bezpłatna wersje próbna są tworzone, zarządzane i konfigurowane przez usługę i nie mają subskrypcji zarządzanych przez firmę Microsoft.

> [!NOTE]
> Tokeny, które aplikacja otrzyma za pośrednictwem łącza próbnego, mogą służyć tylko do uzyskiwania informacji o użytkowniku za pośrednictwem usługi Azure AD w celu zautomatyzowania tworzenia konta w aplikacji. Konta Microsoft (MSA) nie są obsługiwane do uwierzytelniania przy użyciu tego tokenu.

#### <a name="contact-me"></a>Skontaktuj się ze mną

Zbieraj informacje kontaktowe klientów, łącząc system CRM (Customer Relationship Management). Klient zostanie poproszony o pozwolenie na udostępnienie swoich informacji. Te dane klienta, wraz z nazwą oferty, identyfikatorem i źródłem witryny, w której znaleźli Twoją ofertę, zostaną wysłane do skonfigurowanym systemu CRM. Aby uzyskać więcej informacji na temat konfigurowania programu CRM, zobacz [Łączenie zarządzania potencjalnymi klientami](#connect-lead-management).

## <a name="example-marketplace-offer-listing"></a>Przykładowa lista ofert platformy handlowej

![Przykładowa aukcja oferty w portalu marketplace z notatkami](./media/marketplace-offer.svg)

## <a name="enable-a-test-drive"></a>Włączanie dysku testowego

Jazda próbna to świetny sposób na zaprezentowanie oferty potencjalnym klientom, dając im możliwość "wypróbuj przed zakupem", co skutkuje zwiększoną konwersją i generowaniem wysoko wykwalifikowanych potencjalnych klientów. Aby dowiedzieć się więcej, zobacz [Zezwalaj klientom na testowanie oferty.](./test-drive.md)

- **Włączanie dysku testowego** (pole wyboru)

Włączenie jazdy próbnej zostanie wyświetlony monit o skonfigurowanie środowiska demonstracyjnego dla klientów, aby wypróbować ofertę na określony czas. 

### <a name="test-drive-resources"></a>Zasoby dysku testowego

- [Sprawdzone rozwiązania marketingowe](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/marketing-and-best-practices)- [na dysku testowym Najlepsze rozwiązania techniczne na dysku testowym](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [Omówienie dysku testowego (pobieranie w formacie PDF)](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf)

## <a name="connect-lead-management"></a>Łączenie zarządzania potencjalnymi klientami

[!INCLUDE [Connect lead management](./includes/connect-lead-management-a.md)]

#### <a name="additional-lead-management-resources"></a>Dodatkowe zasoby do zarządzania potencjalnymi klientami
- [Zarządzanie potencjalnymi klientami — często zadawane pytania](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#frequently-asked-questions)
- [Typowe błędy konfiguracji potencjalnych klientów](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#common-lead-configuration-errors-during-publishing-on-cloud-partner-portal)
- [Omówienie zarządzania potencjalnymi klientami Jeden Pager](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf)

Pamiętaj, **aby zapisać** przed przejściem do następnej sekcji.

## <a name="properties"></a>Właściwości

Karta **Właściwości** prosi o zdefiniowanie kategorii i branż używanych do grupowania oferty na rynkach, umów prawnych obsługujących ofertę i wersji aplikacji.

Wybierz **pozycję Zapisz** po wypełnieniu tych pól.

### <a name="category"></a>Kategoria

Wybierz co najmniej jedną (1) i maksymalnie trzy (3) kategorie używane do grupowania oferty w odpowiednich obszarach wyszukiwania w portalu marketplace. Sprawdź, w jaki sposób Twoja oferta obsługuje te kategorie w opisie oferty.

### <a name="industry"></a>Branża

[!INCLUDE [Industry Taxonomy](./includes/industry-taxonomy.md)]

### <a name="app-version"></a>Wersja aplikacji

To pole jest opcjonalne i używane w portalu AppSource marketplace do identyfikowania numeru wersji oferty.

### <a name="standard-contract-for-the-microsoft-commercial-marketplace"></a>Umowa standardowa dla komercyjnego rynku firmy Microsoft

Firma Microsoft udostępnia szablon kontraktu standardowego.

- **Czy korzystać z umowy standardowej dla komercyjnego rynku firmy Microsoft?**

Aby uprościć proces zaopatrzenia dla klientów i zmniejszyć złożoność prawną dostawców oprogramowania, firma Microsoft oferuje umowę standardową dla komercyjnego rynku firmy Microsoft, aby ułatwić transakcje na rynku. Zamiast tworzyć niestandardowe warunki, wydawcy komercyjnego rynku mogą zdecydować się na oferowanie swojego oprogramowania w ramach standardowej umowy, którą klienci muszą zweryfikować i zaakceptować tylko raz. Umowę standardową można znaleźć https://go.microsoft.com/fwlink/?linkid=2041178tutaj: .

Możesz wybrać opcję korzystania z umowy standardowej zamiast udostępniać własne niestandardowe warunki, wybierając pole wyboru "Użyj standardowego kontraktu dla komercyjnego rynku".

![Korzystanie z pola wyboru Umowa standardowa](./media/use-standard-contract.png)

> [!NOTE]
> Po opublikowaniu oferty przy użyciu umowy standardowej dla komercyjnego portalu Microsoft marketplace nie możesz korzystać z własnych niestandardowych warunków. Jest to scenariusz "lub". Oferujesz swoje rozwiązanie w ramach umowy standardowej **lub** własnych warunków. Jeśli chcesz zmodyfikować warunki Umowy Standardowej, możesz to zrobić poprzez standardowe zmiany umowy.

#### <a name="standard-contract-amendments"></a>Zmiany w umowie standardowej

Standardowe zmiany umowy pozwalają wydawcom wybrać warunki umowy standardowej dla uproszczenia i dostosować warunki dla swojego produktu lub firmy. Klienci muszą zapoznać się ze zmianami umowy tylko wtedy, gdy już przejrzeli i zaakceptowali umowę standardową firmy Microsoft.

Wydawcy rynku komercyjnego mają do dyspozycji dwa rodzaje zmian:

- Uniwersalne zmiany: Zmiany te są powszechnie stosowane do umowy standardowej dla wszystkich klientów. Uniwersalne zmiany są wyświetlane każdemu klientowi oferty w przepływie zakupu. Klienci muszą zaakceptować warunki Umowy Standardowej i zmiany, zanim będą mogli skorzystać z Twojej oferty.
- Zmiany niestandardowe: Te zmiany są specjalnymi zmianami umowy standardowej, które są przeznaczone dla określonych klientów tylko za pośrednictwem identyfikatorów dzierżawy platformy Azure. Wydawcy mogą wybrać dzierżawę, na którą mają być kierowane. Tylko klienci z najemcy zostaną przedstawieni z niestandardowymi warunkami zmian w przepływie zakupu oferty.  Klienci muszą zaakceptować warunki Umowy Standardowej i zmiany, zanim będą mogli skorzystać z Twojej oferty.

>[!NOTE]
> Te dwa rodzaje poprawek kumulują się jeden na drugim. Klienci objęci niestandardowymi zmianami otrzymają również uniwersalną zmianę umowy standardowej podczas zakupu.

**Uniwersalne warunki zmiany do standardowej umowy dla komercyjnego rynku Microsoftu:** Wprowadź uniwersalne warunki zmiany w tym polu. Możesz podać jedną uniwersalną poprawkę na ofertę. W tym polu można wprowadzić nieograniczoną liczbę znaków. Te warunki są wyświetlane klientom w usłudze AppSource, Azure Marketplace i/lub witrynie Azure portal podczas odnajdowania i zakupu.

**Niestandardowe warunki zmiany do standardowego kontraktu dla komercyjnego rynku firmy Microsoft:** Zacznij od wybrania **opcji Dodaj niestandardowe warunki zmiany**. Możesz podać maksymalnie 10 niestandardowych warunków zmiany na ofertę.

- **Niestandardowe warunki poprawek:** Wprowadź niestandardowe warunki poprawek w polu niestandardowe warunki poprawek. W tym polu można wprowadzić nieograniczoną liczbę znaków. Tylko klienci z identyfikatorów dzierżawy, które określisz dla tych warunków niestandardowych, zostaną przedstawieni z niestandardowymi warunkami poprawek w przepływie zakupu oferty w witrynie Azure portal.  
- **Identyfikatory dzierżawy** (wymagane): Każda poprawka niestandardowa może być kierowana do maksymalnie 20 identyfikatorów dzierżawy. Jeśli dodasz poprawkę niestandardową, musisz podać co najmniej jeden identyfikator dzierżawy. Identyfikator dzierżawy identyfikuje klienta na platformie Azure. Możesz poprosić klienta o ten identyfikator, a oni mogą go znaleźć, przechodząc do portal.azure.com > usługi Azure Active Directory > Properties. Wartość identyfikatora katalogu jest identyfikatorem dzierżawy (na przykład 50c464d3-4930-494c-963c-1e951d15360e). Można również wyszukać identyfikator dzierżawy klienta w organizacji przy użyciu adresu URL nazwy domeny w [miejscu Co to jest mój identyfikator dzierżawy platformy Microsoft Azure i usługi Office 365?](https://www.whatismytenantid.com).
- **Opis** (opcjonalnie): Opcjonalnie podaj przyjazny opis identyfikatora dzierżawy, który pomaga zidentyfikować klienta, na który kierujesz reklamy, za pomocą poprawki.

#### <a name="terms-and-conditions"></a>Warunki i postanowienia

Jeśli chcesz podać własne niestandardowe warunki, możesz wprowadzić je w polu warunki. W tym polu można wprowadzić maksymalnie 10 000 znaków tekstu. Jeśli warunki wymagają dłuższego opisu, wprowadź w tym polu jeden link URL, w którym można znaleźć warunki. Będzie wyświetlany klientom jako aktywne łącze.

Klienci muszą zaakceptować te warunki, zanim będą mogli wypróbować Twoją ofertę.

Pamiętaj, **aby zapisać** przed przejściem do następnej sekcji.

## <a name="offer-listing"></a>Oferta aukcji

Na karcie Oferta lista są wyświetlane języki (i rynki), w których oferta jest dostępna, obecnie w języku angielskim (Stany Zjednoczone) jest jedyną dostępną lokalizacją. Ponadto na tej stronie jest wyświetlany stan listy specyficznej dla języka oraz data/godzina dodania. Musisz zdefiniować szczegóły rynku (nazwa oferty, opis, wyszukiwane terminy itp.) dla każdego języka / rynku.

> [!NOTE]
> Zawartość oferty (taka jak opis oferty, dokumenty, zrzuty ekranu, warunki użytkowania i polityka prywatności) nie musi być w języku angielskim, o ile opis oferty zaczyna się od wyrażenia" Ta aplikacja jest dostępna tylko w języku [nieanglojęzycznym]". Dopuszczalne jest również podanie *użytecznego adresu URL linku,* aby oferować treści w języku innym niż ten używany w treści aukcji Oferty.

### <a name="offer-listings"></a>Oferty ofert

Podaj szczegóły, które mają być wyświetlane na rynku, w tym opisy oferty i zasobów marketingowych.

- **Nazwa** (wymagana): nazwa zdefiniowana w tym miejscu będzie wyświetlana jako tytuł oferty na wybranych marketplace.) Nazwa jest wstępnie wypełniona na podstawie poprzedniego wpisu **Nowej Oferty.** Nazwa może być znakiem towarowym. Nie może zawierać emotikonów (chyba że są to znaki towarowe i symbole praw autorskich) i musi być ograniczona do 50 znaków.
- **Podsumowanie** (wymagane): podaj krótki opis oferty, która ma być używana w wynikach wyszukiwania w witrynie marketplace. W tym polu można wprowadzić maksymalnie 100 znaków tekstu.
- **Opis** (wymagany): podaj opis oferty, która ma być wyświetlana w przeglądzie aukcji w portalu marketplace. Rozważ uwzględnienie propozycji wartości, kluczowych korzyści, dowolnej kategorii lub stowarzyszeń branżowych, możliwości zakupu w aplikacji, wszelkich wymaganych ujawnień i linku, aby dowiedzieć się więcej.
W tym polu można wprowadzić maksymalnie 3000 znaków tekstu, w tym znaczniki. Aby uzyskać dodatkowe wskazówki, zobacz artykuł [Pisanie wspaniałego opisu aplikacji](https://docs.microsoft.com/windows/uwp/publish/write-a-great-app-description).
- **Słowa kluczowe wyszukiwania:** Wprowadź maksymalnie trzy słowa kluczowe wyszukiwania, których klienci mogą użyć, aby znaleźć Twoją ofertę w marketplace(s).
- **Instrukcje rozpoczynania** pracy (wymagane): wyjaśnij, jak skonfigurować i rozpocząć korzystanie z aplikacji dla potencjalnych klientów.  Ten przewodnik Szybki start może zawierać łącza do bardziej szczegółowej dokumentacji online. W tym polu można wprowadzić maksymalnie 3000 znaków tekstu.

#### <a name="description"></a>**Opis**

To pole jest wymagane. Elementy do uwzględnienia w **opisie**:

* Wyraźnie opisz propozycję wartości swojej oferty w pierwszych kilku zdaniach opisu.  
* Pamiętaj, że kilka pierwszych zdań może być wyświetlanych w wynikach wyszukiwania.  
* Nie polegaj na funkcjach i funkcjach sprzedaży produktu. Zamiast tego skup się na wartości, którą dostarczasz.  
* W miarę możliwości używaj słownictwa branżowego lub opartego na korzyściach.

Podstawowe składniki twojej propozycji wartości powinny zawierać następujące informacje:

* Opis produktu.
* Typ użytkownika, który korzysta z produktu.
* Klient potrzebuje lub ból, że produkt adresuje.

Aby twoja oferta **Opis** był bardziej atrakcyjny, użyj edytora tekstu sformatowania opisu.

![Korzystanie z edytora tekstu sformatowego](./media/text-editor2.png)

Użyj następujących instrukcji, aby użyć edytora tekstu sformatowego:

- Aby zmienić format zawartości, wyróżnij tekst, który chcesz sformatować, i zaznacz styl tekstu, jak pokazano poniżej:

     ![Zmiana formatu tekstu za pomocą edytora tekstu sformatowania](./media/text-editor3.png)

- Aby dodać do tekstu listę punktowaną lub ponumerowaną, użyj poniższych opcji:

     ![Dodawanie list za pomocą edytora tekstu sformatowego](./media/text-editor4.png)

- Aby dodać lub usunąć wcięcie do tekstu, użyj poniższych opcji:

     ![Używanie edytora tekstu sformatowego do wcięcie](./media/text-editor5.png)

#### <a name="links"></a>Linki

- **Polityka prywatności** (wymagana): Link do zasad ochrony prywatności organizacji. Użytkownik jest odpowiedzialny za zapewnienie zgodności aplikacji z przepisami i regulacjami dotyczącymi prywatności oraz za zapewnienie ważnej polityki prywatności
- **Materiały marketingowe programu CSP** (opcjonalnie): Podaj łącze do materiałów marketingowych, jeśli zdecydujesz się rozszerzyć ofertę na program [Dostawcy rozwiązań w chmurze (CSP).](https://docs.microsoft.com/azure/marketplace/cloud-solution-providers) CSP rozszerza ofertę na szerszą gamę wykwalifikowanych klientów, umożliwiając partnerom CSP łączenie, sprzedawanie i odsprzedawanie oferty. Ci sprzedawcy będą potrzebować dostępu do materiałów do marketingu Twojej oferty. Aby uzyskać więcej informacji, zobacz [Usługi przejdź do rynku](https://partner.microsoft.com/reach-customers/gtm).
- **Przydatne linki** (opcjonalnie): opcjonalne dodatkowe dokumenty online dotyczące aplikacji lub powiązanych usług na liście, podając **tytuł** i **adres URL**. Dodaj dodatkowe przydatne linki, klikając **+ Dodaj adres URL**.

#### <a name="contact-information"></a>Informacje kontaktowe

- **Kontakty**: Dla każdego kontaktu z klientem podaj **nazwę**pracownika, **numer telefonu**i adres **e-mail.**  (Nie *będą* one wyświetlane publicznie). **Adres URL pomocy technicznej** jest również wymagany dla grupy Kontakt pomocy **technicznej.**  (Informacje te *będą* wyświetlane publicznie).

**Kontakt pomocy technicznej** (wymagany): w przypadku ogólnych pytań dotyczących pomocy technicznej.

**Kontakt techniczny** (wymagany): W przypadku pytań technicznych.

**Kontakt menedżera kanału** (wymagane): W przypadku pytań odsprzedawców związanych z programem CSP.

#### <a name="files-and-images"></a>Pliki i obrazy

- **Dokumenty** (wymagane): Dodaj powiązane dokumenty marketingowe do oferty w formacie PDF, zapewniając co najmniej jeden (1) i maksymalnie trzy (3) dokumenty na ofertę.
- **Obrazy** (opcjonalnie): Istnieje wiele miejsc, w których obrazy logo oferty mogą pojawiać się na całym rynku(-ach), wymagające następujących rozmiarów - Małe: 48 x 48 pikseli _(wymagane),_ Średnie: 90 x 90 pikseli _(wymagane)_, Duży: 216 x 216 pikseli _(wymagane),_ Szeroki: 255 x 115 pikseli, i Bohater: 815 x 290 pikseli. Wszystkie obrazy muszą być w pliku . W formacie PNG.
- **Zrzuty ekranu** (wymagane): Dodaj zrzuty ekranu przedstawiające twoją ofertę. Maksymalnie pięć (5) zrzuty ekranu mogą być dodawane i powinny być wielkości 1280 x 720 pikseli. Wszystkie obrazy muszą być w pliku . W formacie PNG.
- **Filmy** (opcjonalnie): dodaj linki do filmów demonstrujących ofertę. Możesz korzystać z linków do filmów z YouTube i/lub Vimeo, które są wyświetlane wraz z Twoją ofertą dla klientów. Należy również wprowadzić miniaturę obrazu wideo o wymiarach 1280 x 720 pikseli w formacie PNG. Możesz wyświetlić maksymalnie cztery filmy na ofertę.

Pamiętaj, **aby zapisać** przed przejściem do następnej sekcji.

>[!Note]
>Jeśli masz problem z przekazywaniem plików, upewnij się, https://upload.xboxlive.com że sieć lokalna nie blokuje usługi używanej przez Centrum partnerów.

#### <a name="additional-marketplace-listing-resources"></a>Dodatkowe zasoby dotyczące aukcji w portalu marketplace

- [Najważniejsze wskazówki dotyczące ofert w marketplace](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)

## <a name="preview"></a>Wersja zapoznawcza

Karta **Podgląd** umożliwia zdefiniowanie ograniczonej **grupy odbiorców w wersji zapoznawczej** w celu udostępnienia oferty przed opublikowaniem oferty na żywo szerszej grupy odbiorców w marketplace.

> [!IMPORTANT]
> Po sprawdzeniu oferty w wersji zapoznawczej wybierz **pozycję Przejdź na żywo,** aby twoja oferta mogła zostać opublikowana na żywo w publicznej reklamie marketplace.

- **Zdefiniuj grupę odbiorców w wersji zapoznawczej: dodaj pojedynczą wiadomość e-mail konta AAD/MSA w wierszu wraz z opcjonalnym opisem.**

Dodaj maksymalnie 10 adresów e-mail ręcznie lub 20 w przypadku przekazywania pliku CSV dla istniejących kont Microsoft Account (MSA) lub usługi Azure Active Directory, aby ułatwić sprawdzanie poprawności oferty przed opublikowaniem na żywo. Dodając te konta, definiujesz grupę odbiorców, która będzie mogła uzyskać dostęp w wersji zapoznawczej do oferty przed jej opublikowaniem w witrynie marketplace(-ów). Jeśli twoja oferta jest już dostępna, możesz zdefiniować grupę odbiorców w wersji zapoznawczej, aby przetestować wszelkie zmiany lub aktualizacje oferty.

> [!NOTE]
> Grupa odbiorców podglądu różni się od grupy prywatnej. Grupa odbiorców w wersji zapoznawczej ma dostęp do twojej oferty _przed_ opublikowaniem na żywo na rynku. Możesz też utworzyć plan i udostępnić go tylko odbiorcom prywatnym. Na karcie **Lista planów** możesz zdefiniować grupę odbiorców prywatnych za pomocą pola wyboru **To jest plan prywatny.** Następnie można zdefiniować prywatną grupę odbiorców do 20 000 klientów przy użyciu identyfikatorów dzierżawy platformy Azure.

## <a name="technical-configuration"></a>Konfiguracja techniczna

Karta **Konfiguracja techniczna** definiuje szczegóły techniczne (ścieżka adresu URL, element webhook, identyfikator dzierżawy i identyfikator aplikacji) używane do łączenia się z ofertą. To połączenie umożliwia nam udostępnienie oferty klientowi końcowemu, jeśli zdecyduje się ją nabyć. Diagramy opisujące użycie zebranych pól są dostępne w dokumentacji [interfejsów API realizacji SaaS](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-fulfillment-api-v2).

- **Adres URL strony docelowej** (wymagany): zdefiniuj adres URL witryny, na który klienci będą lądować po odebraniu oferty z marketplace. Ten adres URL będzie punktem końcowym, który odbiera token, gdy klient jest kierowany do strony. Ten token można wymienić na inicjowanie obsługi administracyjnej szczegóły przy użyciu resolve w interfejsach API realizacji. Te dane i wszelkie inne zbierane mogą być wykorzystane jako część interaktywnej strony internetowej klienta wbudowanej w twoje doświadczenie, aby zakończyć rejestrację i aktywować ich zakup.

- **Element webhook połączenia** (wymagane): Dla wszystkich zdarzeń asynchronicznych, które firma Microsoft musi wysłać do Ciebie w imieniu klienta (przykład: Subskrypcja SaaS została nieprawidłowa), wymagamy, aby zapewnić element webhook połączenia. Jeśli nie masz jeszcze systemu webhook w miejscu, najprostszą konfiguracją jest mieć HTTP Endpoint Logic App, który będzie nasłuchiwać wszelkich zdarzeń\/są publikowane do niego, a następnie obsługiwać je odpowiednio (na przykład https: /prod-1westus.logic.azure.com:443/work). Aby uzyskać więcej informacji, zobacz [Wywołanie, wyzwalanie lub zagnieżdżanie przepływów pracy z punktami końcowymi HTTP w aplikacjach logiki](https://docs.microsoft.com/azure/logic-apps/logic-apps-http-endpoint).

- **Identyfikator dzierżawy usługi Azure AD** (wymagany): w witrynie Azure portal wymagamy [utworzenia aplikacji usługi Azure Active Directory (AD),](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) abyśmy mogli sprawdzić poprawność połączenia między naszymi dwiema usługami za uwierzytelnioną komunikacją. Aby znaleźć [identyfikator dzierżawy,](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)przejdź do usługi Azure Active Directory i wybierz **pozycję Właściwości**, a następnie poszukaj numeru **identyfikatora katalogu** na liście (na przykład 50c464d3-4930-494c-963c-1e951d15360e).

- **Identyfikator aplikacji usługi Azure AD** (wymagany): potrzebny jest również identyfikator [aplikacji](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) i klucz uwierzytelniania. Aby uzyskać te wartości, przejdź do usługi Azure Active Directory i wybierz **pozycję Rejestracje aplikacji,** a następnie poszukaj numeru **identyfikatora aplikacji** na liście (na przykład 50c464d3-4930-494c-963c-1e951d15360e). Aby znaleźć klucz uwierzytelniania, przejdź do **pozycji Ustawienia** i wybierz pozycję **Klawisze**. Musisz podać opis i czas trwania, a następnie otrzyma wartość liczbową.

>[!Note]
>Identyfikator aplikacji platformy Azure jest skojarzony z identyfikatorem wydawcy, więc upewnij się, że ten sam identyfikator aplikacji jest używany we wszystkich ofertach.

## <a name="plan-overview"></a>Omówienie planu

Karta **Przegląd planu** umożliwia udostępnienie różnych opcji planu w ramach tej samej oferty. Te plany (czasami określane jako jednostki SKU) mogą się różnić pod względem wersji, monetyzacji lub warstw usług. Aby sprzedać swoją ofertę na rynku, musisz skonfigurować co najmniej jeden plan.

Po utworzeniu zobaczysz nazwy planu, identyfikatory, modele cenowe, dostępność (publiczne lub prywatne), aktualny stan publikowania i wszystkie dostępne akcje.

**Działania** dostępne w **przeglądzie planu** różnią się w zależności od aktualnego stanu planu i mogą obejmować:

- Jeśli stan planu to **Wersja robocza** — usuwanie wersji roboczej
- Jeśli stan planu to **Live** - Stop sell plan lub Sync private audience

**Tworzenie nowego planu** (minimum jednego planu dla tych, którzy zdą tych, którzy zdąmek do sprzedaży za pośrednictwem firmy Microsoft)

- **Identyfikator planu:** Utwórz unikatowy identyfikator planu dla każdego planu w tej ofercie. Ten identyfikator będzie widoczny dla klientów w adresie URL produktu i szablonach usługi Azure Resource Manager (jeśli dotyczy). Używaj tylko małych liter, znaków alfanumerycznych, kresek lub podkreśleń. Dla tego identyfikatora planu dozwolony jest maksymalnie 50 znaków. Identyfikator nie może być modyfikowany po wybraniu create.
- **Nazwa planu:** Klienci zobaczą tę nazwę przy podejmowaniu decyzji, który plan wybrać w ramach oferty. Utwórz unikatową nazwę oferty dla każdego planu w tej ofercie. Nazwa planu służy do rozróżniania planów oprogramowania, które mogą być częścią tej samej oferty (na przykład Nazwa oferty: Windows Server; plany: Windows Server 2016, Windows Server 2019).

### <a name="plan-listing"></a>Lista planu

Na karcie **Lista planów** są wyświetlane języki (i rynki), w których plan jest dostępny, obecnie w języku angielskim (Stany Zjednoczone) jest jedyną dostępną lokalizacją. Ponadto na tej stronie jest wyświetlany stan listy specyficznej dla języka oraz data/godzina dodania. Musisz zdefiniować szczegóły rynku (nazwa oferty, opis, wyszukiwane terminy itp.) dla każdego języka / rynku.

#### <a name="plan-listing-details"></a>Szczegóły aukcji planu

Wybranie jednego z języków planu spowoduje wyświetlenie informacji o **liście planu,** w tym **nazwy** i **opisu.**

- **Nazwa:** Wstępnie wypełniona na podstawie podglądu **Wpis nowego planu** i pojawi się jako tytuł "Planu oprogramowania" oferty wyświetlanego na rynku.
- **Opis:** Ten opis jest okazją do wyjaśnienia, co sprawia, że ten plan oprogramowania jest wyjątkowy i jakie są różnice w stosunku do innych planów oprogramowania w ramach oferty. Może zawierać maksymalnie 500 znaków.

Wybierz **pozycję Zapisz** po wypełnieniu tych pól.

#### <a name="plan-pricing-and-availability"></a>Ceny planu i dostępność

Karta **Ceny i dostępność** umożliwia skonfigurowanie rynków, na których będzie dostępny ten plan, pożądanego modelu zarabiania, ceny i terminu rozliczeniowego. Ponadto można wskazać, czy plan ma być widoczny dla wszystkich, czy tylko dla konkretnych klientów (odbiorców prywatnych).

##### <a name="enabling-free-trials"></a>Włączanie bezpłatnych wersji próbnych

Oferty SaaS za pośrednictwem komercyjnego rynku umożliwiają zapewnienie miesięcznej bezpłatnej wersji próbnej podczas sprzedaży za pośrednictwem firmy Microsoft. W przypadku wszystkich modeli rozliczeń i warunków z wyjątkiem planów taryfowych obsługiwane są bezpłatne wersje próbne. Ta opcja pozwala klientom mieć niską barierę wejścia przez jeden miesiąc bezpłatnego dostępu.  Jeśli zdecydujesz się włączyć bezpłatną wersję próbną dla planów w ramach oferty, klient nie będzie mógł przekonwertować na płatną subskrypcję przed końcem początkowego okresu jednego miesiąca.  W tym czasie klienci kupujący ofertę mogą wypróbować wszystkie obsługiwane plany, które mają włączoną bezpłatną wersję próbną i konwertować między nimi.  Konwersja na płatną subskrypcję odbywa się automatycznie na koniec okresu.

>[!Note]
>Jeśli klient zdecyduje się na konwersję na plan bez bezpłatnych wersji próbnych, konwersja nastąpi, ale bezpłatna wersja próbna zostanie natychmiast utracona.  Ponadto, gdy klient zaczyna płacić za plan, nie może już uzyskać bezpłatną wersję próbną w tej samej subskrypcji ponownie, nawet jeśli konwertują na jednostkę SKU, która obsługuje bezpłatne wersje próbne.

Możliwość skonfigurowania bezpłatnej wersji próbnej jest dostępna dla każdego planu w ofercie. Przejdź do ceny i dostępności dla każdej oferty i zaznacz pole wyboru, aby zezwolić na miesięczny okres próbny.

![Jednomiesięczne pole wyboru bezpłatnego okresu próbnego](./media/free-trial-enable.png)

>[!Note]
>Po opublikowaniu oferty transakcji z bezpłatną próbą nie można jej wyłączyć dla tego planu. Upewnij się, że to ustawienie jest poprawne dla pierwszego publikowania, aby uniknąć konieczności ponownego tworzenia planu.

Aby uzyskać informacje na temat subskrypcji klientów, którzy obecnie uczestniczą `isFreeTrial`w bezpłatnej wersji próbnej, użyj nowej właściwości INTERFEJSU API, która zostanie oznaczona jako prawdziwa lub fałszywa. Aby uzyskać więcej informacji, zobacz [interfejs API SaaS Get Subscription](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-fulfillment-api-v2#get-subscription).

>[!Note]
>Bezpłatne wersje próbne nie są obsługiwane w przypadku planów, które wykorzystują usługę pomiaru rynku.

#### <a name="markets"></a>Rynków

- **Edytowanie rynków** (opcjonalnie)

Każdy plan musi być dostępny na co najmniej jednym rynku. Zaznacz pole wyboru dla dowolnej lokalizacji rynku, w której chcesz udostępnić ten plan. Pole wyszukiwania i przycisk wyboru krajów "Umorzenie podatków", w których firma Microsoft przekazuje podatek od sprzedaży i korzystać z niego w twoim imieniu, są dołączone do pomocy.

Jeśli masz już ustawione ceny planu w dolarach amerykańskich (USD) i dodasz inną lokalizację rynkową, cena nowego rynku zostanie obliczona zgodnie z aktualnymi kursami wymiany. Przed opublikowaniem sprawdź cenę dla każdego rynku. Ceny można przeglądać za pomocą linku "Ceny eksportowe (xlsx)" po zapisaniu zmian.

#### <a name="pricing"></a>Cennik

- **Model cenowy**: Stawka ryczałtowa lub oparta na fotelu

**Stawka ryczałtowa:** Włącz dostęp do oferty za jedną miesięczną lub roczną cenę zryczałtowaną. Jest to czasami określane jako ceny oparte na witrynie. Za pomocą tego modelu cenowego można opcjonalnie zdefiniować plany taryfowe, które używają interfejsu API usługi pomiaru portalu marketplace do pobierania opłat od klientów zgodnie z niestandardowymi jednostkami.  Aby uzyskać więcej informacji na temat rozliczeń taryfowych, zobacz [rozliczanie taryfowe za pomocą usługi pomiaru marketplace](./saas-metered-billing.md).

**Na użytkownika:** Włącz dostęp do swojej oferty z ceną w oparciu o liczbę użytkowników uzyskujących dostęp do oferty lub zajmujących miejsca. Ten model oparty na użytkowniku umożliwia ustawienie minimalnej i maksymalnej liczby dozwolonych użytkowników na podstawie ceny. W ten sposób różne punkty cenowe można skonfigurować na podstawie liczby użytkowników, konfigurując wiele planów.  Te pola są opcjonalne. Jeśli nie zostanie zaznaczona, liczba użytkowników będzie interpretowana jako nie mająca limitu (min 1 i maksymalnie tyle, ile może obsługiwać system). Te pola mogą być edytowane w ramach aktualizacji planu.

Po opublikowaniu nie można zmienić wyboru modelu cen rozliczeniowych. Ponadto wszystkie plany dla tej samej oferty muszą mieć ten sam model cenowy.

- **Okres rozliczeniowy:** miesięczny lub roczny

Wybierz częstotliwość, z jaką klienci muszą zapłacić podana cena. Należy podać co najmniej jedną cenę miesięczną lub roczną lub obie opcje mogą być dostępne dla klientów.

- **Cena:** USD miesięcznie lub USD za rok

Ceny ustalone w USD (USD = Dolar amerykański) są przeliczane na walutę lokalną wszystkich wybranych rynków przy użyciu bieżących kursów wymiany po zapisaniu. Sprawdź te ceny przed opublikowaniem, eksportując arkusz kalkulacyjny cen i przeglądając cenę na każdym rynku. Jeśli chcesz ustawić ceny niestandardowe na poszczególnych rynkach, zmodyfikuj i zaimportuj arkusz kalkulacyjny cen. Użytkownik jest odpowiedzialny za sprawdzanie poprawności tej ceny i jest właścicielem tych ustawień.
*\*Aby włączyć eksport danych cenowych, należy najpierw zapisać zmiany cen.*

Przejrzyj uważnie ceny przed opublikowaniem, ponieważ istnieją pewne ograniczenia dotyczące tego, co może się zmienić po opublikowaniu planu:

- Po opublikowaniu planu nie można zmienić modelu cenowego.
- Po opublikowaniu terminu rozliczeniowego dla planu nie można go później usunąć.
- Po opublikowaniu ceny na rynku w planie nie można jej później zmienić.

### <a name="plan-audience"></a>Zaplanuj grupę odbiorców

Możesz skonfigurować każdy plan tak, aby był widoczny dla wszystkich lub dla wybranych przez Ciebie odbiorców. Członkostwo w tej ograniczonej grupy odbiorców można przypisać przy użyciu identyfikatorów dzierżawy usługi Azure AD.

#### <a name="privacy"></a>Ochrona prywatności

- **Jest to plan prywatny** (opcjonalne pole wyboru)

Zaznacz to pole, aby plan był prywatny i widoczny tylko dla wybranych odbiorców z ograniczeniami. Po opublikowaniu planu prywatnego możesz zaktualizować odbiorców lub udostępnić plan wszystkim. Gdy plan zostanie opublikowany jako widoczny dla wszystkich, musi pozostać widoczny dla wszystkich. (Planu nie można ponownie skonfigurować jako planu prywatnego).

- **Ograniczona liczba odbiorców (identyfikatory dzierżawy)**

Przypisz odbiorców, którzy będą mieli dostęp do tego prywatnego planu. Program Access jest przypisywany przy użyciu identyfikatorów dzierżawców z opcją dołączania opisu każdego przypisanego identyfikatora dzierżawy. Podczas importowania pliku arkusza kalkulacyjnego csv można dodać maksymalnie 10 identyfikatorów dzierżawy lub 20 000 identyfikatorów dzierżawców klientów.

Dzierżawca jest reprezentacją organizacji o identyfikatorze reprezentowanym jako identyfikator GUID (Global unique Identifier, 128-bitowy numer całkowity używany do identyfikowania zasobów). Jest to dedykowane wystąpienie usługi Azure AD, które organizacja lub deweloper aplikacji otrzymuje, gdy organizacja lub deweloper aplikacji tworzy relację z firmą Microsoft, na przykład podczas rejestracji na platformie Azure, usłudze Microsoft Intune lub usłudze Microsoft 365. Każda dzierżawa usługi Azure AD jest unikatowa i oddzielona od innych dzierżaw usługi Azure AD. Aby sprawdzić dzierżawę, zaloguj się w witrynie Azure Portal przy użyciu konta, którego chcesz użyć do zarządzania aplikacją. Jeśli masz dzierżawę, nastąpi automatyczne zalogowanie do niej, a nazwa dzierżawy zostanie wyświetlona bezpośrednio pod nazwą Twojego konta. Umieść wskaźnik myszy na nazwie konta w prawym górnym rogu witryny Azure Portal, aby wyświetlić swoją nazwę, adres e-mail, identyfikator katalogu/dzierżawy (GUID) oraz domenę. Jeśli Twoje konto jest skojarzone z wieloma dzierżawami, możesz wybrać nazwę swojego konta, aby otworzyć menu, w którym można przełączać się między dzierżawami. Każda dzierżawa ma własny identyfikator dzierżawy. Możesz również wyszukać identyfikator dzierżawy organizacji przy użyciu [https://www.whatismytenantid.com](https://www.whatismytenantid.com)adresu URL nazwy domeny pod adresem: .

Podczas gdy oferty SaaS używają identyfikatorów dzierżawy do definiowania odbiorców prywatnych, inne typy ofert mogą używać identyfikatorów subskrypcji platformy Azure (które są również reprezentowane jako identyfikatory GUID).

> [!NOTE]
> Grupa odbiorców prywatnych (lub odbiorców z ograniczeniami) różni się od grupy odbiorców w wersji zapoznawczej. Na karcie **[Podgląd](#preview)** można zdefiniować grupę podglądu. Grupa odbiorców w wersji zapoznawczej ma dostęp do twojej oferty *przed* opublikowaniem oferty na żywo na rynku. Podczas gdy oznaczenie odbiorców prywatnych dotyczy tylko określonego planu, odbiorcy wersji zapoznawczej mogą wyświetlać wszystkie plany (prywatne lub nie), ale tylko w ograniczonym okresie podglądu, gdy plan jest testowany i zatwierdzany.

## <a name="example-list-of-plans-within-a-marketplace-offer"></a>Przykładowa lista planów w ramach oferty rynkowej

![Przykładowa lista planu marketplace z notatkami](./media/marketplace-plan.svg)

## <a name="cloud-solution-provider-csp-reseller-audience"></a>Odbiorcy odsprzedawcy dostawcy rozwiązań w chmurze (CSP)

Wybór udostępnienia oferty w programie CSP umożliwia dostawcom rozwiązań w chmurze sprzedawanie produktu w ramach rozwiązania dołączonego do klientów. Aby uzyskać więcej informacji, zobacz [Dostawcy rozwiązań w chmurze](https://go.microsoft.com/fwlink/?linkid=2111109).

## <a name="publish"></a>Publikowanie

Po wypełnieniu wszystkich wymaganych sekcji oferty wybierz **pozycję publikuj** w prawym górnym rogu portalu. Zostaniesz przekierowany do strony **Recenzja i publikowanie.**

### <a name="submit-offer-to-preview"></a>Prześlij ofertę do wersji zapoznawczej

Jeśli po raz pierwszy publikujesz tę ofertę, możesz:

- Zobacz stan ukończenia każdej sekcji oferty.
    - *Nie rozpoczęto* - oznacza, że sekcja nie została dotknięta i musi zostać ukończona.
    - *Niekompletne* - oznacza, że sekcja zawiera błędy, które muszą zostać naprawione lub wymaga więcej informacji, które mają być dostarczone. Musisz wrócić do sekcji i zaktualizować ją.
    - *Complete* - oznacza, że sekcja jest kompletna, wszystkie wymagane dane zostały dostarczone i nie ma żadnych błędów. Wszystkie sekcje oferty muszą być w pełnym stanie, zanim będzie można złożyć ofertę.
- Przekaż zespołowi certyfikacyjne instrukcje testowania, aby upewnić się, że aplikacja jest poprawnie testowana, oprócz wszelkich dodatkowych notatek przydatnych do zrozumienia aplikacji.
- Prześlij ofertę do publikacji, wybierając pozycję **Prześlij**. Wyślemy Ci wiadomość e-mail z powiadomieniem, kiedy wersja zapoznawcza oferty będzie dostępna do przejrzenia i zatwierdzenia. Musisz wrócić do Centrum partnerskiego i wybrać **opcję Przejdź na żywo,** aby oferta opublikowała ofertę publicznie (lub jeśli jest to oferta prywatna, dla odbiorców prywatnych).

## <a name="next-steps"></a>Następne kroki

- [Aktualizowanie istniejącej oferty w witrynie Marketplace dla zastosowań komercyjnych](./update-existing-offer.md)
