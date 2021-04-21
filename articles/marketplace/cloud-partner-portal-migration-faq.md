---
title: Często zadawane pytania dotyczące przechodzenia z witryny Cloud Partner Portal do Partner Center — komercyjna platforma handlowa firmy Microsoft
description: Odpowiedzi na często zadawane pytania dotyczące przechodzenia ofert z Cloud Partner Portal do Partner Center.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: mingshen-ms
ms.author: mingshen
ms.date: 07/14/2020
ms.openlocfilehash: 0a45e7c0479cf490ad8688230897ae89ce4d1020
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107819128"
---
# <a name="frequently-asked-questions-about-transitioning-from-the-cloud-partner-portal-to-partner-center"></a>Często zadawane pytania dotyczące przechodzenia z Cloud Partner Portal do Partner Center

Proces Cloud Partner Portal został Partner Center. Partner Center oferuje usprawnione, zintegrowane środowisko publikowania nowych ofert w usługach [Microsoft AppSource](https://appsource.microsoft.com/) [lub Azure Marketplace oraz](https://azuremarketplace.microsoft.com/) zarządzania istniejącymi ofertami, które zostały zmigrowane z Cloud Partner Portal. Wszystkie funkcje tego Cloud Partner Portal są dostępne w Partner Center. Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące tej kwestii.

## <a name="what-does-the-transition-to-partner-center-mean-for-me"></a>Co oznacza przejście do Partner Center dla mnie?

Możesz kontynuować działalność w Partner Center:

| Warstwowy | Zmiany |
| --- | --- |
| Konto | Nie trzeba tworzyć nowego Partner Center konta; Możesz użyć istniejących poświadczeń Cloud Partner Portal, aby zalogować się do Partner Center, w którym będziesz teraz zarządzać kontem, użytkownikami, uprawnieniami i rozliczeniami. Umowa publikowania i informacje o profilu firmy są migrowane na nowe konto Partner Center, wraz z informacjami o profilu wypłat, kontami użytkowników i uprawnieniami oraz aktywnymi ofertami. Aby dowiedzieć się [więcej, zobacz Zarządzanie kontem komercyjnej platformy handlowej w Partner Center](manage-account.md). |
| Publikowanie ofert i zarządzanie ofertami | Dane Twojej oferty zostały przeniesione z Cloud Partner Portal do Partner Center. Teraz będziesz mieć dostęp do swoich ofert w Partner Center, co zapewnia ulepszone środowisko użytkownika i intuicyjny interfejs. Dowiedz się, jak [zaktualizować istniejącą ofertę na platformie handlowej.](partner-center-portal/update-existing-offer.md) |
| Dostępność ofert na platformie handlowej | Brak zmian. Jeśli Twoja oferta jest komercyjna na platformie handlowej, będzie nadal komercyjna. |
| Nowe zakupy i wdrożenia | Brak zmian. Klienci mogą nadal kupować i wdrażać oferty bez przerw. |
| Wypłaty | Wszelkie zakupy i wdrożenia będą nadal opłacane w zwykły sposób. Dowiedz się więcej o [uzyskiwaniu zapłaty na platformie handlowej.](/partner-center/marketplace-get-paid?context=/azure/marketplace/context/context) |
| Integracje interfejsów API z [istniejącymi Cloud Partner Portal API](cloud-partner-portal-api-overview.md) | Istniejące Cloud Partner Portal API są nadal obsługiwane, a istniejące integracje nadal działają. Dowiedz się więcej [z tematu Czy interfejsy API REST Cloud Partner Portal będą obsługiwane?](#are-the-cloud-partner-portal-rest-apis-still-supported) |
| Analiza | Możesz nadal monitorować sprzedaż, oceniać wydajność i optymalizować oferty na platformie handlowej, wyświetlając analizy w Partner Center. Istnieją różnice między wyświetlaniem raportów analitycznych w programie CPP i Partner Center. Na przykład usługa **Analiza dla sprzedawcy** w programie CPP ma kartę Orders & Usage (Zamówienia **& Użycia),** która wyświetla dane dotyczące ofert opartych na użyciu i ofert innych niż oparte na użyciu, natomiast na stronie Partner Center **Orders** (Zamówienia) znajduje się oddzielna karta ofert SaaS. Aby dowiedzieć się [więcej, zobacz Access analytic reports for the commercial marketplace in Partner Center](partner-center-portal/analytics.md)(Dostęp do raportów analitycznych dla platformy handlowej w Partner Center). |
|||

## <a name="do-i-need-to-create-a-new-account-to-manage-my-offers-in-partner-center"></a>Czy muszę utworzyć nowe konto, aby zarządzać ofertami w Partner Center?

Nie, Twoje konto zostanie zachowane. Oznacza to, że jeśli jesteś istniejącym partnerem, możesz użyć istniejących poświadczeń konta Cloud Partner Portal, aby zalogować się do Partner Center. Nie twórz nowego konta ani żadnych ofert utworzonych w Cloud Partner Portal i przeniesionych do Partner Center nie zostaną z nim skojarzone.

## <a name="what-pages-in-partner-center-correspond-to-pages-i-used-in-the-cloud-partner-portal"></a>Które strony w Partner Center odpowiadają stronom użytym w Cloud Partner Portal?

Poniżej przedstawiono Partner Center dla stron często używanych w Cloud Partner Portal. Jeśli zapisano linki Cloud Partner Portal jako zakładki, należy je zaktualizować.

| Cloud Partner Portal strony | Cloud Partner Portal strony | Partner Center strony |
| --- | --- | --- |
| Strona ze wszystkimi ofertami | [https://cloudpartner.azure.com/#alloffers](https://cloudpartner.azure.com/#alloffers) | [https://partner.microsoft.com/dashboard/commercial-marketplace/overview](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) |
| Strona wszystkich wydawców | [https://cloudpartner.azure.com/#publishers](https://cloudpartner.azure.com/#publishers) | [https://partner.microsoft.com/dashboard/account/v3/publishers/list](https://partner.microsoft.com/dashboard/account/v3/publishers/list) |
| Profil wydawcy | [https://cloudpartner.azure.com/#profile](https://cloudpartner.azure.com/#profile) | [https://partner.microsoft.com/dashboard/account/management](https://partner.microsoft.com/dashboard/account/management) |
| Strona użytkowników | [https://cloudpartner.azure.com/#users](https://cloudpartner.azure.com/#users) | [https://partner.microsoft.com/dashboard/account/usermanagement](https://partner.microsoft.com/dashboard/account/usermanagement) |
| Strona Historia | [https://cloudpartner.azure.com/#history](https://cloudpartner.azure.com/#history) | Funkcja Historia nie jest jeszcze obsługiwana w Partner Center. |
| Pulpit nawigacyjny szczegółowych informacji | [https://cloudpartner.azure.com/#insights](https://cloudpartner.azure.com/#insights) | [https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary) |
| Raport wypłat | [https://cloudpartner.azure.com/#insights/payout](https://cloudpartner.azure.com/#insights/payout) | [https://partner.microsoft.com/dashboard/payouts/reports/incentivepayments](https://partner.microsoft.com/dashboard/payouts/reports/incentivepayments) |
|||

## <a name="payout-report-differences"></a>Różnice w raporcie wypłat

Są to różnice w raporcie wypłaty między wycofaną Cloud Partner Portal a bieżącą Partner Center:

| Portal Cloud Partner | Centrum partnerskie |
| --- | --- |
| **Link:**https://cloudpartner.azure.com/ | **Link:** https://partner.microsoft.com/dashboard/payouts/reports/transactionhistory i https://partner.microsoft.com/dashboard/payouts/reports/incentivepayments |
| **Nawigacja:** Raportowanie wypłat udostępniane w analizie wypłaty | **Nawigacja:** Raportowanie wypłat udostępniane w Partner Center — ikona wypłat |
| **Zakres:**<ul><li>Transakcja na element wiersza jest widoczna dla kolekcji w toku, zebranych i płatnych.</li><li>Raportowanie — pokazuje wszystkie pozycje po utworzeniu zamówienia zakupu, w tym kolekcję w toku i rozliczenia w toku oraz stan kolekcji i pozycje, które nie kwalifikują się jeszcze do płatności.</li></ul> | **Zakres:**<ul><li>Pokazuje pozycje po ich uznaniu za kwalifikujące się zarobki.</li><li>Klienci płacą najpierw firmie Microsoft, a następnie isvs mogą zobaczyć raport o wypłatach.</li><li>Raport wypłat nie będzie pokazywać kolekcji w toku i rozliczeń w toku.</li></ul> |
| **Transakcja nie jest gotowa do wypłaty:** rozliczenia w toku | **Transakcja nie jest gotowa do wypłaty:** Następna szacowana płatność: stan wypłaty jest nieprzetworzone. |
| **Stan wypłaty:** n/a | **Stan wypłaty:**<ul><li>Nieprzetworzone: zarobki kwalifikują się do płatności.</li><li>Nadchodzące: zarobki zostaną wysłane do wydawcy w ciągu następnej miesięcznej wypłaty.</li><li>Wysłane: Płatność została wysłana do Banku.</li></ul> |
|||

## <a name="what-about-offers-i-published-in-the-cloud-partner-portal"></a>Co z ofertami opublikowanymi w Cloud Partner Portal?

Oferty zostały przeniesione do usługi Partner Center i będą dostępne po zalogowaniu się do usługi Partner Center, z wyjątkiem ofert usług Dynamics Nav Managed Service i Cortana Intelligence. Jeśli Twoja oferta była komercyjna na platformie handlowej, będzie nadal komercyjna, a klienci nadal będą mogli ją kupować i wdrażać bez zakłóceń. Zobacz następne pytanie What **offers were moved to Partner Center? ( Jakie oferty** zostały przeniesione do usługi Partner Center? , aby uzyskać więcej szczegółów.

## <a name="what-offers-were-moved-to-partner-center"></a>Jakie oferty zostały przeniesione do Partner Center?

Wszystkie typy ofert obsługiwane wcześniej w Cloud Partner Portal są obsługiwane w usłudze Partner Center, z wyjątkiem ofert Dynamics Nav Managed Service i Cortana Intelligence.

W przypadku typów ofert obsługiwanych w Partner Center wszystkie oferty zostały przeniesione niezależnie od ich stanu. Przeniesiono również oferty wersji roboczej, oferty z listy i oferty tylko w wersji zapoznawczej.

| Typ oferty | Przeniesiono do Partner Center? | Następne kroki |
| --- | --- | --- |
| SaaS | Tak | Zaloguj się do Partner Center, aby tworzyć nowe oferty i zarządzać ofertami utworzonymi w Cloud Partner Portal. Aby dowiedzieć się [więcej, zobacz Planowanie oferty SaaS dla platformy handlowej.](plan-saas-offer.md) |
| Maszyna wirtualna | Tak | Zaloguj się do Partner Center, aby tworzyć nowe oferty i zarządzać ofertami utworzonymi w Cloud Partner Portal. Aby dowiedzieć się [więcej, zobacz Planowanie oferty maszyny wirtualnej.](marketplace-virtual-machines.md) |
| Aplikacja platformy Azure | Tak | Zaloguj się do Partner Center, aby tworzyć nowe oferty i zarządzać ofertami utworzonymi w Cloud Partner Portal. Aby dowiedzieć się [więcej, zobacz Tworzenie oferty aplikacji platformy Azure.](create-new-azure-apps-offer.md) |
| Dynamics 365 Business Central | Tak | Zaloguj się do Partner Center, aby tworzyć nowe oferty i zarządzać ofertami utworzonymi w Cloud Partner Portal. Dowiedz się więcej [na stronie Create a Dynamics 365 Business Central offer (Tworzenie oferty Dynamics 365 Business Central).](partner-center-portal/create-new-business-central-offer.md) |
| Dynamics 365 for Customer Engagement & PowerApps | Tak | Zaloguj się do Partner Center, aby tworzyć nowe oferty i zarządzać ofertami utworzonymi w Cloud Partner Portal. Aby dowiedzieć się [więcej, zobacz Tworzenie oferty usługi Dynamics 365 for Customer Engagement & Usługi PowerApps.](dynamics-365-customer-engage-offer-setup.md) |
| Dynamics 365 for Operations | Tak | Zaloguj się do Partner Center, aby tworzyć nowe oferty i zarządzać ofertami utworzonymi w Cloud Partner Portal. Aby dowiedzieć się [więcej, zobacz Create a Dynamics 365 for Operations offer](partner-center-portal/create-new-operations-offer.md)(Tworzenie oferty Dynamics 365 for Operations). |
| Aplikacja usługi Power BI | Tak | Zaloguj się do Partner Center, aby tworzyć nowe oferty i zarządzać ofertami utworzonymi w Cloud Partner Portal. Dowiedz się więcej [z tematu Create a Power BI app for AppSource (Tworzenie aplikacji Power BI dla usługi AppSource).](partner-center-portal/create-power-bi-app-offer.md) |
| IoT Edge modułu | Tak | Zaloguj się do Partner Center, aby tworzyć nowe oferty i zarządzać ofertami utworzonymi w Cloud Partner Portal. Aby dowiedzieć się [więcej, zobacz Create, configure, and publish an IoT Edge module offer in Azure Marketplace (Tworzenie,](partner-center-portal/azure-iot-edge-module-creation.md)konfigurowanie i publikowanie oferty modułu Azure Marketplace ). |
| Kontener | Tak | Zaloguj się do Partner Center, aby tworzyć nowe oferty i zarządzać ofertami utworzonymi w Cloud Partner Portal. Aby dowiedzieć się [więcej, zobacz Create an Azure container offer (Tworzenie oferty kontenera platformy Azure).](./create-azure-container-offer.md) |
| Usługa konsultingowa | Tak | Zaloguj się do Partner Center, aby tworzyć nowe oferty i zarządzać ofertami utworzonymi w Cloud Partner Portal. Dowiedz się więcej [na stronie Create a consulting service offer (Tworzenie oferty usługi konsultingowej).](./create-consulting-service-offer.md) |
| Usługa zarządzana | Tak | Zaloguj się do Partner Center, aby tworzyć nowe oferty i zarządzać ofertami utworzonymi w Cloud Partner Portal. Aby dowiedzieć się [więcej, zobacz Create a Managed Service offer (Tworzenie oferty usługi zarządzanej).](./plan-managed-service-offer.md) |
| Dynamics Nav Managed Service | Nie | Firma Microsoft przekształciła usługę zarządzaną systemu Dynamics NAV w usługę [Dynamics 365 Business Central,](/dynamics365/business-central/)dlatego oferty na żywo usługi zarządzanej dynamics NAV z usługi AppSource zostały wymienione. Te oferty nie są już odnajdywalne przez klientów i nie zostały przeniesione do Partner Center. Aby udostępnić oferty w usłudze AppSource, dostosuj je do ofert usługi Dynamics 365 Business Central i prześlij je w [Partner Center.](https://partner.microsoft.com/) Aby dowiedzieć się [więcej, zobacz Create a Dynamics 365 Business Central offer (Tworzenie oferty dynamics 365 Business Central).](partner-center-portal/create-new-business-central-offer.md) |
| Cortana Intelligence | Nie | Firma Microsoft zmieniła plan rozwoju produktu Dla Cortany Intelligence, dlatego wyewoluowali oferty Cortana Intelligence na żywo z usługi AppSource. Te oferty nie są już odnajdywalne przez klientów i nie zostały przeniesione do Partner Center. Aby udostępnić oferty na platformie handlowej, dostosuj oferty do ofert oprogramowania jako usługi (SaaS) i prześlij je w [Partner Center.](https://partner.microsoft.com/) Aby dowiedzieć się [więcej, zobacz Lista kontrolna tworzenia oferty SaaS w Partner Center](./plan-saas-offer.md). |

## <a name="i-cant-find-my-cloud-partner-portal-offers-in-partner-center"></a>Nie mogę znaleźć moich ofert Cloud Partner Portal w Partner Center

To, co widzisz Partner Center zależy od programów, w których użytkownik jest zarejestrowany, kont, do których należysz, oraz ról i uprawnień użytkownika, które zostały przypisane. Istnieje wiele Partner Center dostępnych programów i użytkownik może być zarejestrowany w wielu programach. Możesz również mieć dostęp do wielu kont z tym samymi poświadczeniami użytkownika.

Oferty utworzone w programie Cloud Partner Portal są dostępne w witrynie Partner Center w ramach programu Komercyjnej platformy handlowej i w ramach konta użytego do tworzenia ofert.  Aby upewnić się, że przeglądasz właściwy program i właściwe konto, wykonaj poniższe kroki. Aby uzyskać inne porady dotyczące rozwiązywania problemów, zobacz Manage your Partner Center account (Zarządzanie [Partner Center kontem).](/partner-center/partner-center-account-setup)

### <a name="access-the-right-program-in-partner-center"></a>Uzyskiwanie dostępu do odpowiedniego programu w Partner Center

1. Zaloguj się do [Partner Center](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) przy użyciu tych samych poświadczeń, które są używane do logowania się do Cloud Partner Portal. W okienku nawigacji po lewej stronie są wyświetlane opcje skojarzone z programami, w których użytkownik jest zarejestrowany.

    Przykład: załóżmy, że masz dostęp do trzech programów: programu MPN, programu poleceń i programu komercyjnej platformy handlowej. Po zalogowaniu się Partner Center w okienku nawigacji zobaczysz te trzy programy.

2. Wybierz **pozycję Omówienie**  >  **komercyjnej platformy handlowej,** aby uzyskać dostęp do ofert.

    Jeśli nie widzisz programu komercyjnej platformy handlowej w okienku nawigacji po lewej stronie, być może masz niewłaściwe konto. Wykonaj kroki opisane w następnej sekcji, aby uzyskać dostęp do odpowiedniego konta.

    [![Zrzut ekranu przedstawiający menu Partner Center Przegląd](media/cpp-pc-faq/overview-menu.png "Wyświetla menu Partner Center Przegląd")](media/cpp-pc-faq/overview-menu.png#lightbox)

### <a name="access-the-right-account-in-partner-center"></a>Uzyskiwanie dostępu do odpowiedniego konta w Partner Center

Jeśli jesteś częścią wielu kont, w Centrum partnerskim zobaczysz przycisk s wyboru konta oznaczony dwiema strzałkami w menu nawigacji po lewej stronie. Wybierz przycisk selektora konta, aby wyświetlić listę wszystkich kont, do których należysz. Wybierz dowolne konto na liście, aby przełączyć się na nie i wyświetlić wszystkie programy i informacje dotyczące tego konta. Jeśli nie widzisz przycisku s wyboru konta w menu nawigacji, jesteś członkiem pojedynczego konta.

[![Zrzut ekranu przedstawia Partner Center s wyboru konta.](media/cpp-pc-faq/picker-button.png "Wyświetla przycisk s Partner Center s wyboru konta użytkownika")](media/cpp-pc-faq/picker-button.png#lightbox)

## <a name="how-do-i-create-new-offers"></a>Jak mogę utworzyć nowe oferty?

Uzyskaj dostęp do programu komercyjnej platformy [handlowej w Partner Center,](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) aby tworzyć nowe oferty. Na stronie Przegląd wybierz pozycję **+ Nowa oferta.**

[![Zrzut ekranu przedstawia Partner Center Przegląd.](media/cpp-pc-faq/new-offer.png "Wyświetla menu Partner Center Przegląd")](media/cpp-pc-faq/new-offer.png#lightbox)

## <a name="i-cant-sign-in-and-need-to-open-a-support-ticket"></a>Nie mogę się zalogować i muszę otworzyć bilet pomocy technicznej

Jeśli nie możesz zalogować się do swojego konta, możesz otworzyć bilet pomocy [technicznej tutaj.](https://partner.microsoft.com/support/v2/?stage=1)

## <a name="where-are-instructions-for-using-partner-center"></a>Gdzie znajdują się instrukcje korzystania z Partner Center?

Przejdź do dokumentacji [platformy handlowej.](index.yml), a następnie rozwiń pozycję **Commercial Marketplace Portal w Partner Center**. Aby wyświetlić artykuły pomocy dotyczące tworzenia ofert w Partner Center, rozwiń **temat Tworzenie nowej oferty.**

## <a name="what-are-the-publishing-and-offer-management-differences"></a>Jakie są różnice w zakresie publikowania i zarządzania ofertami?

Oto kilka różnic między Cloud Partner Portal i Partner Center.

### <a name="modular-publishing-capabilities"></a>Możliwości publikowania modułowego

Partner Center udostępnia modularną opcję publikowania, która pozwala wybrać zmiany, które chcesz opublikować, zamiast zawsze publikować wszystkie aktualizacje jednocześnie. Na przykład poniższy ekran pokazuje, że jedynymi zmianami wybranymi do opublikowania są zmiany właściwości **i**  **oferty**. Zmiany wprowadzone na stronie Podgląd nie zostaną opublikowane.

[![Zrzut ekranu przedstawia Partner Center przeglądanie i publikowanie.](media/cpp-pc-faq/review-page.png "Wyświetla stronę Partner Center przeglądanie i publikowanie")](media/cpp-pc-faq/review-page.png#lightbox)

Aktualizacje, które nie są publikowane, są zapisywane jako wersje robocze. Kontynuuj korzystanie z wersji zapoznawczej oferty, aby zweryfikować ofertę przed jej wprowadzeniem do publicznej wersji.

### <a name="enhanced-preview-options"></a>Rozszerzone opcje wersji zapoznawczej

Partner Center zawiera funkcję [porównania z ulepszonymi](partner-center-portal/update-existing-offer.md#compare-changes-to-your-offer) opcjami filtrowania. Dzięki temu można porównać wersję zapoznawczą i wersję transmisję na żywo oferty.

[![Zrzut ekranu przedstawia Partner Center porównanie.](media/cpp-pc-faq/compare.png "Pokazuje funkcję Partner Center porównanie")](media/cpp-pc-faq/compare.png#lightbox)

### <a name="branding-and-navigation-changes"></a>Zmiany brandowania i nawigacji

Zauważysz pewne zmiany brandingu. Na przykład *jednostki SKU* są znakowane jako *Plany* w Partner Center:

[![Zrzut ekranu przedstawia Partner Center planów.](media/cpp-pc-faq/plans.png "Wyświetla stronę Partner Center planów")](media/cpp-pc-faq/plans.png#lightbox)

Ponadto informacje podane wcześniej w witrynie **Marketplace** lub **storefront Details** (usługa konsultingowa, aplikacja Power BI) w  witrynie Cloud Partner Portal są teraz zbierane na stronie oferty w Partner Center:

[! [Zrzut ekranu przedstawia Partner Center oferty.] (media/cpp-pc-faq/offer-listing.png](media/cpp-pc-faq/offer-listing.png#lightbox)

Informacje podane wcześniej dla jednostki SKU na jednej stronie w Cloud Partner Portal mogą być teraz zbierane na kilku stronach w Partner Center:

- Strona Planowania konfiguracji
- Strona listy planów
- Strona Planowanie dostępności
- Strona planowania konfiguracji technicznej, jak pokazano poniżej:

[![Wyświetla Partner Center konfiguracji technicznej.](media/cpp-pc-faq/technical-configuration.png)](media/cpp-pc-faq/technical-configuration.png#lightbox)

Identyfikator oferty jest teraz wyświetlany na pasku nawigacyjnym po lewej stronie oferty:

![Pokazuje lokalizację Partner Center oferty](media/cpp-pc-faq/offer-id.png)

### <a name="stop-selling-an-offer"></a>Zatrzymywanie sprzedaży oferty

Możesz poprosić o [zatrzymanie sprzedaży oferty na](partner-center-portal/update-existing-offer.md#stop-selling-an-offer-or-plan) platformie handlowej bezpośrednio z Partner Center Portal. Ta opcja jest dostępna na **stronie Omówienie oferty** dla Twojej oferty.

[![Zrzut ekranu przedstawia Partner Center, aby zatrzymać sprzedaż oferty.](media/cpp-pc-faq/stop-sell.png "Wyświetla stronę Partner Center, aby zatrzymać sprzedaż oferty")](media/cpp-pc-faq/stop-sell.png#lightbox)
<br><br>

## <a name="are-the-cloud-partner-portal-rest-apis-still-supported"></a>Czy interfejsy API REST Cloud Partner Portal są nadal obsługiwane?

Interfejsy API Cloud Partner Portal są zintegrowane z Partner Center i będą nadal działać. Przejście do Partner Center wprowadza niewielkie zmiany. Przejrzyj tabelę poniżej, aby upewnić się, że kod nadal działa w Partner Center.

| Interfejs API | Zmień opis | Wpływ |
| --- | --- | --- |
| POST Publish, GoLive, Cancel | W przypadku zmigrowanych ofert nagłówek odpowiedzi będzie miał inny format, ale będzie nadal działać w ten sam sposób, oznaczając ścieżkę względną w celu pobrania stanu operacji. | Podczas wysyłania dowolnego z odpowiednich żądań POST dla oferty nagłówek Location będzie miał jeden z dwóch formatów w zależności od stanu migracji oferty: <ul><li>Oferty niezmigrowane: `/api/operations/{PublisherId}${offerId}$2$preview?api-version=2017-10-31`</li><li>Migrowane oferty: `/api/publishers/{PublisherId}/offers/{offereId}/operations/408a4835-0000-1000-0000-000000000000?api-version=2017-10-31`</li></ul>|
| Operacja GET | W przypadku ofert, które wcześniej obsługujeły pole "notification-email" w odpowiedzi, to pole będzie przestarzałe i nie będzie zwracane dla migrowanych ofert. | W przypadku zmigrowanych ofert nie będziemy już wysyłać powiadomień do listy wiadomości e-mail określonej w żądaniach. Zamiast tego usługa interfejsu API zostanie dopasowana do procesu wysyłania wiadomości e-mail z Partner Center do wysyłania wiadomości e-mail. Powiadomienia o postępie operacji będą wysyłane na adres e-mail ustawiony w sekcji Informacje kontaktowe sprzedawcy ustawień Twojego konta w Partner Center.<br><br>Upewnij się, że adres e-mail [](https://partner.microsoft.com/dashboard/account/management) ustawiony w sekcji Informacje kontaktowe sprzedawcy w ustawieniach konta w Partner Center jest prawidłowy do odbierania powiadomień. |
|||