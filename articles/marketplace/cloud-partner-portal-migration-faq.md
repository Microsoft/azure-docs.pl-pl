---
title: Często zadawane pytania dotyczące przechodzenia z portal Cloud Partner do Centrum partnerskiego — Microsoft Commercial Marketplace
description: Odpowiedzi na często zadawane pytania dotyczące przechodzenia ofert z portal Cloud Partner do Centrum partnerskiego.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: mingshen-ms
ms.author: mingshen
ms.date: 07/14/2020
ms.openlocfilehash: 8346edd0f6016f1751a392213e5d6321f875baf2
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96455664"
---
# <a name="frequently-asked-questions-about-transitioning-from-the-cloud-partner-portal-to-partner-center"></a>Często zadawane pytania dotyczące przechodzenia z portal Cloud Partner do Centrum partnerskiego

Portal Cloud Partner przeszedł do Centrum partnerskiego. Centrum partnerskie oferuje usprawnione, zintegrowane środowisko publikowania nowych ofert w witrynie [Microsoft AppSource](https://appsource.microsoft.com/) lub [witrynie Azure Marketplace](https://azuremarketplace.microsoft.com/) oraz do zarządzania istniejącymi ofertami, które zostały zmigrowane z Portal Cloud partner. Wszystkie funkcje portal Cloud Partner są dostępne w centrum partnerskim. Ten artykuł dotyczy często zadawanych pytań.

## <a name="what-does-the-transition-to-partner-center-mean-for-me"></a>Co oznacza przejście do Centrum partnerskiego?

Możesz kontynuować działalność w centrum partnerskim:

| Obszar<img src="" width=200px> | Zmiany |
| --- | --- |
| Konto | Nie ma potrzeby tworzenia nowego konta Centrum partnerskiego; Możesz użyć istniejących poświadczeń portal Cloud Partner, aby zalogować się do Centrum partnerskiego, w którym będziesz teraz zarządzać kontem, użytkownikami, uprawnieniami i rozliczeniami. Informacje o umowie dotyczące publikowania i profilu firmy są migrowane do nowego konta Centrum partnerskiego wraz z informacjami o profilu wypłaty, kontami użytkowników i uprawnieniami oraz aktywnymi ofertami. Dowiedz się więcej na temat [zarządzania kontem komercyjnej witryny Marketplace w centrum partnerskim](partner-center-portal/manage-account.md). |
| Oferowanie funkcji publikowania i zarządzania ofertami | Twoje dane oferty zostały przeniesione z portal Cloud Partner do Centrum partnerskiego. Teraz będziesz uzyskiwać dostęp do ofert w centrum partnerskim, które oferują Ulepszone środowisko użytkownika i intuicyjny interfejs. Dowiedz się [, jak zaktualizować istniejącą ofertę w komercyjnej witrynie Marketplace](partner-center-portal/update-existing-offer.md). |
| Dostępność ofert w portalu komercyjnym | Brak zmian. Jeśli Twoja oferta znajduje się na żywo na rynku komercyjnym, będzie ona nadal aktywna. |
| Nowe zakupy i wdrożenia | Brak zmian. Klienci mogą nadal kupić i wdrażać oferty bez przerw. |
| Wypłaty | Wszystkie zakupy i wdrożenia będą nadal wynagradzane jako normalne. Dowiedz się więcej [na temat uzyskiwania płatności na rynku komercyjnym](/partner-center/marketplace-get-paid?context=/azure/marketplace/context/context). |
| Integracje interfejsów API z istniejącymi [interfejsami api Portal Cloud partner](cloud-partner-portal-api-overview.md) | Istniejące interfejsy API portal Cloud Partner są nadal obsługiwane i Twoje istniejące integracji nadal działają. Dowiedz się więcej na temat [obsługiwanych Portal Cloud partner interfejsów API REST?](#are-the-cloud-partner-portal-rest-apis-still-supported) |
| Analiza | Możesz kontynuować monitorowanie sprzedaży i ocenę wydajności oraz zoptymalizować oferty w komercyjnej witrynie Marketplace, wyświetlając analizę w centrum partnerskim. Istnieją różnice między sposobem wyświetlania raportów analizy w programie CPP i centrum partnerskim. Na przykład **Informacje o sprzedawcy** w programie CPP mają kartę **użycie & zamówienia** , która wyświetla dane dla ofert opartych na użyciu i nieopartych na użyciu, a w centrum partnerskim strona **Orders** ma osobną kartę dla ofert SaaS. Dowiedz się więcej o [uzyskiwaniu dostępu do raportów analitycznych dla komercyjnego portalu Marketplace w centrum partnerskim](partner-center-portal/analytics.md). |
|||

## <a name="do-i-need-to-create-a-new-account-to-manage-my-offers-in-partner-center"></a>Czy muszę utworzyć nowe konto, aby zarządzać moimi ofertami w centrum partnerskim?

Nie, Twoje konto zostanie zachowane. Oznacza to, że jeśli jesteś istniejącym partnerem, możesz zalogować się do Centrum partnerskiego za pomocą poświadczeń istniejącego konta portal Cloud Partner. Nie twórz nowego konta ani nie są z nim skojarzone żadne oferty utworzone w portal Cloud Partner i przenoszone do Centrum partnerskiego.

## <a name="what-pages-in-partner-center-correspond-to-pages-i-used-in-the-cloud-partner-portal"></a>Jakie strony w centrum partnerskim odpowiadają stronom używanym w portal Cloud Partner?

Poniżej znajdują się linki Centrum partnerskiego dla stron, które są często używane w portal Cloud Partner. Jeśli Zapisano portal Cloud Partner linki jako zakładki, należy je zaktualizować.

| Strona portal Cloud Partner <img src="" width=100px>| portal Cloud Partner łącze strony | Link do strony Centrum partnerskiego |
| --- | --- | --- |
| Strona ze wszystkimi ofertami | [https://cloudpartner.azure.com/#alloffers](https://cloudpartner.azure.com/#alloffers) | [https://partner.microsoft.com/dashboard/commercial-marketplace/overview](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) |
| Strona wszystkich wydawców | [https://cloudpartner.azure.com/#publishers](https://cloudpartner.azure.com/#publishers) | [https://partner.microsoft.com/dashboard/account/v3/publishers/list](https://partner.microsoft.com/dashboard/account/v3/publishers/list) |
| Profil wydawcy | [https://cloudpartner.azure.com/#profile](https://cloudpartner.azure.com/#profile) | [https://partner.microsoft.com/dashboard/account/management](https://partner.microsoft.com/dashboard/account/management) |
| Strona użytkowników | [https://cloudpartner.azure.com/#users](https://cloudpartner.azure.com/#users) | [https://partner.microsoft.com/dashboard/account/usermanagement](https://partner.microsoft.com/dashboard/account/usermanagement) |
| Strona historia | [https://cloudpartner.azure.com/#history](https://cloudpartner.azure.com/#history) | Funkcja History nie jest jeszcze obsługiwana w centrum partnerskim. |
| Pulpit nawigacyjny usługi Insights | [https://cloudpartner.azure.com/#insights](https://cloudpartner.azure.com/#insights) | [https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary) |
| Raport wypłaty | [https://cloudpartner.azure.com/#insights/payout](https://cloudpartner.azure.com/#insights/payout) | [https://partner.microsoft.com/dashboard/payouts/reports/incentivepayments](https://partner.microsoft.com/dashboard/payouts/reports/incentivepayments) |
|||

## <a name="payout-report-differences"></a>Różnice w raportach wypłaty

Poniżej przedstawiono różnice w raporcie wypłaty między wycofanymi portal Cloud Partner i bieżącym centrum partnerskim:

| Portal Cloud Partner | Centrum partnerskie |
| --- | --- |
| **Link**: https://cloudpartner.azure.com/ | **Link**: https://partner.microsoft.com/dashboard/payouts/reports/transactionhistory i https://partner.microsoft.com/dashboard/payouts/reports/incentivepayments |
| **Nawigacja**: raportowanie wypłaty w usłudze Insights | **Nawigacja**: raportowanie wypłaty w centrum partnerskim — ikona wypłaty |
| **Zakres**:<ul><li>Element Transaction na wiersz jest widoczny dla kolekcji w toku, zebranych i płatnych.</li><li>Raportowanie — pokazuje wszystkie elementy wiersza po utworzeniu zamówienia zakupu, w tym zbieranie w toku i rozliczanie w toku oraz stan kolekcji i elementy wierszy, które nie są jeszcze kwalifikujące się do płatności.</li></ul> | **Zakres**:<ul><li>Pokazuje elementy wiersza, gdy są one uznawane za kwalifikujące się zyski.</li><li>Klienci zwracają się najpierw do firmy Microsoft, a następnie dostawcy ISV mogą zobaczyć raport wypłaty.</li><li>Raport wypłaty nie będzie wyświetlał kolekcji w toku i rozliczeń w toku.</li></ul> |
| **Transakcja nie jest gotowa do wypłaty**: rozliczanie w toku | **Transakcja nie jest gotowa do wypłaty**: Następna Szacowana płatność: stan wypłaty jest w stanie nieprzetworzonym. |
| **Stan wypłaty**: brak | **Stan wypłaty**:<ul><li>Nieprzetworzone: pokrycie jest uprawnione do płatności.</li><li>Nadchodzące: zdobywanie zostanie wysłane do wydawcy w następnej miesięcznej wypłatie.</li><li>Wysłano: płatność została wysłana do Twojego banku.</li></ul> |
|||

## <a name="what-about-offers-i-published-in-the-cloud-partner-portal"></a>Co o ofertach opublikowanych w portal Cloud Partner?

Oferty zostały przeniesione do Centrum partnerskiego i będą dostępne po zalogowaniu się do Centrum partnerskiego, z wyjątkiem usług zarządzanych przez program Dynamics NAV i ofert Cortana Intelligence. Jeśli Twoja oferta była aktywna na rynku komercyjnym, będzie ona nadal dostępna, a klienci będą nadal mogli kupować i wdrażać je bez przeszkód. Zapoznaj się z następnym pytaniem, **jakie oferty zostały przeniesione do Centrum partnerskiego?**, aby uzyskać więcej szczegółów.

## <a name="what-offers-were-moved-to-partner-center"></a>Jakie oferty zostały przeniesione do Centrum partnerskiego?

Wszystkie typy ofert, które wcześniej były obsługiwane w portal Cloud Partner są obsługiwane w centrum partnerskim, z wyjątkiem usług zarządzanych i Cortana Intelligence oferowanych przez program Dynamics NAV.

W przypadku typów ofert obsługiwanych w centrum partnerskim wszystkie oferty zostały przeniesione niezależnie od ich stanu. wersje robocza, de-figurowe i Preview są również przenoszone.

| Typ oferty <img src="" width=150px>| Przenosisz do Centrum partnerskiego? <img src="" width=100px>| Następne kroki |
| --- | --- | --- |
| SaaS | Tak | Zaloguj się do Centrum partnerskiego, aby utworzyć nowe oferty i zarządzać ofertami, które zostały utworzone w portal Cloud Partner. Dowiedz się więcej na temat [planowania oferty SaaS dla komercyjnej witryny Marketplace](plan-saas-offer.md). |
| Maszyna wirtualna | Tak | Zaloguj się do Centrum partnerskiego, aby utworzyć nowe oferty i zarządzać ofertami, które zostały utworzone w portal Cloud Partner. Dowiedz się więcej na temat [planowania oferty maszyny wirtualnej](marketplace-virtual-machines.md). |
| Aplikacja platformy Azure | Tak | Zaloguj się do Centrum partnerskiego, aby utworzyć nowe oferty i zarządzać ofertami, które zostały utworzone w portal Cloud Partner. Dowiedz się więcej na temat [tworzenia oferty aplikacji platformy Azure](create-new-azure-apps-offer.md). |
| Dynamics 365 Business Central | Tak | Zaloguj się do Centrum partnerskiego, aby utworzyć nowe oferty i zarządzać ofertami, które zostały utworzone w portal Cloud Partner. Dowiedz się więcej na temat [tworzenia oferty Dynamics 365 Business Central](partner-center-portal/create-new-business-central-offer.md). |
| Dynamics 365 dla zaangażowania klienta & PowerApps | Tak | Zaloguj się do Centrum partnerskiego, aby utworzyć nowe oferty i zarządzać ofertami, które zostały utworzone w portal Cloud Partner. Dowiedz się więcej na temat [tworzenia oferty usługi Dynamics 365 dla klientów & usługi powerapps](partner-center-portal/create-new-customer-engagement-offer.md). |
| Dynamics 365 for Operations | Tak | Zaloguj się do Centrum partnerskiego, aby utworzyć nowe oferty i zarządzać ofertami, które zostały utworzone w portal Cloud Partner. Dowiedz się więcej na temat [tworzenia oferty usługi Dynamics 365 for Operations](partner-center-portal/create-new-operations-offer.md). |
| Aplikacja usługi Power BI | Tak | Zaloguj się do Centrum partnerskiego, aby utworzyć nowe oferty i zarządzać ofertami, które zostały utworzone w portal Cloud Partner. Dowiedz się więcej na temat [tworzenia aplikacji Power BI dla AppSource](partner-center-portal/create-power-bi-app-offer.md). |
| Moduł IoT Edge | Tak | Zaloguj się do Centrum partnerskiego, aby utworzyć nowe oferty i zarządzać ofertami, które zostały utworzone w portal Cloud Partner. Dowiedz się więcej o [tworzeniu, konfigurowaniu i publikowaniu oferty modułu IoT Edge w portalu Azure Marketplace](partner-center-portal/azure-iot-edge-module-creation.md). |
| Kontener | Tak | Zaloguj się do Centrum partnerskiego, aby utworzyć nowe oferty i zarządzać ofertami, które zostały utworzone w portal Cloud Partner. Dowiedz się więcej na temat [tworzenia oferty kontenera platformy Azure](./create-azure-container-offer.md). |
| Usługa konsultingowa | Tak | Zaloguj się do Centrum partnerskiego, aby utworzyć nowe oferty i zarządzać ofertami, które zostały utworzone w portal Cloud Partner. Dowiedz się więcej na temat [tworzenia oferty usługi konsultingowej](./create-consulting-service-offer.md). |
| Usługa zarządzana | Tak | Zaloguj się do Centrum partnerskiego, aby utworzyć nowe oferty i zarządzać ofertami, które zostały utworzone w portal Cloud Partner. Dowiedz się więcej na temat [tworzenia oferty usługi zarządzanej](partner-center-portal/create-new-managed-service-offer.md). |
| Usługa zarządzana w programie Dynamics NAV | Nie | Firma Microsoft przyłączyła usługę zarządzaną przez program Dynamics NAV do usługi [dynamics 365 Business Central](/dynamics365/business-central/) Oferty te nie są już odnajdywane przez klientów i nie zostały przeniesione do Centrum partnerskiego. Aby udostępnić oferty w usłudze AppSource, dostosuj je do oferty Dynamics 365 Business Central i prześlij je do [Centrum partnerskiego](https://partner.microsoft.com/). Dowiedz się więcej na temat [tworzenia oferty Dynamics 365 Business Central](partner-center-portal/create-new-business-central-offer.md). |
| Cortana Intelligence | Nie | Firma Microsoft wyłączyła mapę drogową dotyczącą produktu, aby uzyskać Cortana Intelligence, więc nie mamy na żywo Cortana Intelligence z AppSource. Oferty te nie są już odnajdywane przez klientów i nie zostały przeniesione do Centrum partnerskiego. Aby udostępnić oferty w komercyjnej witrynie Marketplace, Dostosuj oferty do oprogramowania jako usługi (SaaS) oferty i prześlij je w [centrum partnerskim](https://partner.microsoft.com/). Dowiedz się więcej na temat [listy kontrolnej tworzenia oferty SaaS w centrum partnerskim](./plan-saas-offer.md). |

## <a name="i-cant-find-my-cloud-partner-portal-offers-in-partner-center"></a>Nie mogę znaleźć ofert portal Cloud Partner w centrum partnerskim

Elementy wyświetlane w centrum partnerskim są zależne od programów, które są rejestrowane w programie, konta, do których należysz, oraz przypisane role i uprawnienia użytkownika. Dostępnych jest wiele programów Centrum partnerskiego, które mogą być rejestrowane w wielu programach. Możesz również mieć dostęp do wielu kont z tymi samymi poświadczeniami użytkownika.

Oferty utworzone w portal Cloud Partner są dostępne w centrum partnerskim w ramach **komercyjnego programu Marketplace** i w ramach konta używanego do tworzenia ofert. Aby upewnić się, że oglądasz właściwy program i odpowiednie konto, wykonaj poniższe kroki. Aby uzyskać inne wskazówki dotyczące rozwiązywania problemów, zobacz [Zarządzanie kontem w centrum partnerskim](/partner-center/partner-center-account-setup).

### <a name="access-the-right-program-in-partner-center"></a>Dostęp do odpowiedniego programu w centrum partnerskim

1. Zaloguj się do [Centrum partnerskiego](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) przy użyciu tych samych poświadczeń, które są używane do logowania się do Portal Cloud partner. W okienku nawigacji po lewej stronie są wyświetlane opcje skojarzone z programami, które są zarejestrowane w usłudze.

    Przykład: Załóżmy, że masz dostęp do trzech programów: MPN programu, programu referencyjnego i komercyjnego programu Marketplace. Po zalogowaniu się do Centrum partnerskiego zobaczysz te trzy programy w okienku nawigacji.

2. Wybierz pozycję **komercyjne Omówienie witryny Marketplace**  >  **Overview** , aby uzyskać dostęp do ofert.

    Jeśli nie widzisz komercyjnego programu Marketplace w okienku nawigacji po lewej stronie, może to być na niewłaściwym koncie. Postępuj zgodnie z instrukcjami w następnej sekcji, aby uzyskać dostęp do odpowiedniego konta.

    [![Zrzut ekranu pokazujący menu omówienia Centrum partnerskiego](media/cpp-pc-faq/overview-menu.png "Pokazuje menu omówienia Centrum partnerskiego")](media/cpp-pc-faq/overview-menu.png#lightbox)

### <a name="access-the-right-account-in-partner-center"></a>Dostęp do odpowiedniego konta w centrum partnerskim

Jeśli jesteś częścią wielu kont, w centrum partnerskim zostanie wyświetlony przycisk selektora konta oznaczony przez dwie strzałki w menu nawigacji po lewej stronie. Wybierz przycisk wyboru konta, aby wyświetlić listę wszystkich kont, do których należysz. Wybierz dowolne konto na liście, aby przejść do niego i zobaczyć wszystkie programy i informacje dotyczące tego konta. Jeśli nie widzisz przycisku selektora kont w menu nawigacji, jesteś członkiem jednego konta.

[![Zrzut ekranu przedstawia przycisk selektora konta Centrum partnerskiego.](media/cpp-pc-faq/picker-button.png "Pokazuje przycisk selektora konta Centrum partnerskiego")](media/cpp-pc-faq/picker-button.png#lightbox)

## <a name="how-do-i-create-new-offers"></a>Jak mogę utworzyć nowe oferty?

Uzyskaj dostęp do komercyjnego programu Marketplace w [centrum partnerskim](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) , aby utworzyć nowe oferty. Na stronie Przegląd wybierz pozycję **+ Nowa oferta**.

[![Zrzut ekranu przedstawia menu przegląd Centrum partnerskiego.](media/cpp-pc-faq/new-offer.png "Pokazuje menu omówienia Centrum partnerskiego")](media/cpp-pc-faq/new-offer.png#lightbox)

## <a name="i-cant-sign-in-and-need-to-open-a-support-ticket"></a>Nie mogę się zalogować i musisz otworzyć bilet pomocy technicznej

Jeśli nie możesz zalogować się do konta, możesz otworzyć [bilet pomocy technicznej](https://partner.microsoft.com/support/v2/?stage=1) tutaj.

## <a name="where-are-instructions-for-using-partner-center"></a>Gdzie znajdują się instrukcje dotyczące korzystania z Centrum partnerskiego?

Przejdź do [dokumentacji komercyjnej](index.yml)portalu Marketplace, a następnie rozwiń pozycję **komercyjny Portal w witrynie Marketing w centrum partnerskim**. Aby zapoznać się z artykułami dotyczącymi tworzenia ofert w centrum partnerskim, rozwiń pozycję **Utwórz nową ofertę**.

## <a name="what-are-the-publishing-and-offer-management-differences"></a>Jakie są różnice w publikowaniu i ofercie dotyczące zarządzania?

Poniżej przedstawiono kilka różnic między portal Cloud Partner a centrum partnerskim.

### <a name="modular-publishing-capabilities"></a>Modularne możliwości publikowania

Centrum partnerskie udostępnia opcję publikowania modularnego, która pozwala wybrać zmiany, które mają zostać opublikowane, zamiast zawsze publikować wszystkie aktualizacje jednocześnie. Na przykład na poniższym ekranie widać, że jedyne zmiany wybrane do opublikowania to zmiany **Właściwości** i  **oferty**. Zmiany wprowadzone na stronie podglądu nie zostaną opublikowane.

[![Zrzut ekranu przedstawia stronę przegląd i publikowanie Centrum partnerskiego.](media/cpp-pc-faq/review-page.png "Pokazuje stronę przeglądu i publikowania Centrum partnerskiego")](media/cpp-pc-faq/review-page.png#lightbox)

Aktualizacje, które nie są publikowane, są zapisywane jako wersje robocze. Kontynuuj korzystanie z wersji zapoznawczej oferty w celu zweryfikowania oferty przed jej udostępnieniem publicznie.

### <a name="enhanced-preview-options"></a>Rozszerzone opcje podglądu

Centrum partnerskie zawiera [funkcję Compare](partner-center-portal/update-existing-offer.md#compare-changes-to-your-offer) z ulepszonymi opcjami filtrowania. Dzięki temu można porównać z wersjami zapoznawczymi i na żywo oferty.

[![Zrzut ekranu przedstawia funkcję porównania Centrum partnerskiego.](media/cpp-pc-faq/compare.png "Pokazuje funkcję porównania Centrum partnerskiego")](media/cpp-pc-faq/compare.png#lightbox)

### <a name="branding-and-navigation-changes"></a>Zmiany znakowania i nawigacji

Zauważysz pewne zmiany znakowania. Na przykład *jednostki SKU* są oznaczone jako *plany* w centrum partnerskim:

[![Zrzut ekranu przedstawia stronę plany Centrum partnerskiego.](media/cpp-pc-faq/plans.png "Pokazuje stronę plany Centrum partnerskiego")](media/cpp-pc-faq/plans.png#lightbox)

Ponadto informacje, które wcześniej podano w **witrynie Marketplace** lub **szczegółowej**  (usługa doradcza, aplikacja Power BI) w Portal Cloud partner, są teraz zbierane na stronie z **listą ofert** w centrum partnerskim:

[! [Zrzut ekranu przedstawia stronę z listą ofert Centrum partnerskiego.] (Media/CPP-PC — często zadawane pytania/offer-listing.png](media/cpp-pc-faq/offer-listing.png#lightbox)

Informacje podane wcześniej dla jednostek SKU na jednej stronie portal Cloud Partner mogą być teraz zbierane na kilku stronach w centrum partnerskim:

- Strona konfigurowania planu
- Strona aukcji planu
- Zaplanuj stronę dostępności
- Zaplanuj stronę konfiguracji technicznej, jak pokazano poniżej:

[![Pokazuje stronę konfiguracji technicznej Centrum partnerskiego.](media/cpp-pc-faq/technical-configuration.png)](media/cpp-pc-faq/technical-configuration.png#lightbox)

Identyfikator oferty jest teraz pokazywany na pasku nawigacyjnym po lewej stronie oferty:

![Pokazuje lokalizację identyfikatora oferty Centrum partnerskiego](media/cpp-pc-faq/offer-id.png)

### <a name="stop-selling-an-offer"></a>Zatrzymaj sprzedawanie oferty

Możesz poprosić o [zaprzestanie sprzedaży oferty](partner-center-portal/update-existing-offer.md#stop-selling-an-offer-or-plan) w witrynie Marketplace bezpośrednio z poziomu portalu Centrum partnerskiego. Ta opcja jest dostępna na stronie **Przegląd oferty** oferty.

[![Zrzut ekranu przedstawia stronę Centrum partnerskiego, aby zrezygnować z sprzedaży oferty.](media/cpp-pc-faq/stop-sell.png "Pokazuje stronę Centrum partnerskiego, aby zrezygnować z sprzedaży oferty")](media/cpp-pc-faq/stop-sell.png#lightbox)
<br><br>

## <a name="are-the-cloud-partner-portal-rest-apis-still-supported"></a>Czy interfejsy API REST portal Cloud Partner nadal są obsługiwane?

Interfejsy API portal Cloud Partner są zintegrowane z centrum partnerskim i nadal będą działały. Przejście do Centrum partnerskiego wprowadza niewielkie zmiany. Zapoznaj się z tabelą poniżej, aby upewnić się, że kod będzie nadal działał w centrum partnerskim.

| Interfejs API <img src="" width=100px>| Zmień opis | Wpływ |
| --- | --- | --- |
| Publikuj publikowanie, GoLive, Anuluj | W przypadku zmigrowanych ofert nagłówek odpowiedzi będzie miał inny format, ale będzie nadal działał w taki sam sposób, co oznacza ścieżkę względną do pobrania stanu operacji. | Podczas wysyłania dowolnego z odpowiednich żądań POST dla oferty nagłówek lokalizacji będzie miał jeden z dwóch formatów w zależności od stanu migracji oferty: <ul><li>Oferty bez migracji: `/api/operations/{PublisherId}${offerId}$2$preview?api-version=2017-10-31`</li><li>Zmigrowane oferty: `/api/publishers/{PublisherId}/offers/{offereId}/operations/408a4835-0000-1000-0000-000000000000?api-version=2017-10-31`</li></ul>|
| Pobierz operację | W przypadku ofert, które wcześniej obsługiwały w odpowiedzi pole "notification-email", to pole będzie przestarzałe i nie będzie już zwracane dla zmigrowanych ofert. | W przypadku zmigrowanych ofert nie będą już wysyłane powiadomienia do listy wiadomości e-mail określonych w żądaniach. Zamiast tego usługa API zostanie wyrównuje z procesem powiadomień e-mail w centrum partnerskim w celu wysyłania wiadomości e-mail. W każdym przypadku powiadomienia o postępie operacji będą wysyłane na adres e-mail ustawiony w sekcji informacje kontaktowe sprzedawcy ustawień konta w centrum partnerskim.<br><br>Upewnij się, że adres e-mail jest ustawiony w sekcji informacje kontaktowe sprzedawcy w obszarze [Ustawienia konta](https://partner.microsoft.com/dashboard/account/management) w centrum partnerskim, aby otrzymywać powiadomienia. |
|||