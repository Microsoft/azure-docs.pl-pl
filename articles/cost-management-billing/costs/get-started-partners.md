---
title: Wprowadzenie do usługi Azure Cost Management dla partnerów
description: W tym artykule wyjaśniono, w jaki sposób partnerzy używają funkcji usługi Azure Cost Management i jak umożliwiają dostęp do usługi Cost Management swoim klientom.
author: bandersmsft
ms.author: banders
ms.date: 01/27/2021
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: aparnag
ms.custom: secdec18
ms.openlocfilehash: 40c683d98a13bf4810ec4575af3fba5ae64961f0
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98946981"
---
# <a name="get-started-with-azure-cost-management-for-partners"></a>Wprowadzenie do usługi Azure Cost Management dla partnerów

Usługa Azure Cost Management jest natywnie dostępna dla bezpośrednich partnerów, którzy dołączyli swoich klientów do umowy klienta firmy Microsoft i [zakupili plan platformy Azure](/partner-center/purchase-azure-plan). W tym artykule wyjaśniono, w jaki sposób partnerzy używają funkcji usługi [Azure Cost Management](../index.yml) do wyświetlania kosztów subskrypcji w planie platformy Azure. Opisano w nim również, jak partnerzy zapewniają swoim klientom dostęp do usługi Cost Management według stawek detalicznych.

W przypadku bezpośrednich partnerów i dostawców pośrednich administrator globalny oraz agenci administratora mogą uzyskiwać dostęp do usługi Cost Management w dzierżawie partnera i zarządzać kosztami według zafakturowanych cen.

Odsprzedawcy i klienci mogą uzyskać dostęp do Cost Management w dzierżawie klienta i wyświetlić koszty zużycia dla subskrypcji, w przypadku których koszty są obliczane i przedstawiane według stawek detalicznych. Jednak muszą oni mieć dostęp RBAC platformy Azure do subskrypcji w dzierżawie klienta w celu wyświetlania kosztów. Zasady widoczności kosztów muszą zostać włączone przez dostawcę dla dzierżawy klienta.

Klienci mogą korzystać z funkcji usługi Cost Management, jeśli są one włączone przez ich partnera będącego dostawcą usług w chmurze.

Partnerzy będący dostawcami usług w chmurze używają usługi Cost Management do:

- Poznania kosztów zafakturowanych i kojarzenia kosztów z klientami, subskrypcjami, grupami zasobów i usługami.
- Uzyskiwania intuicyjnego widoku kosztów platformy Azure w [analizie kosztów](quick-acm-cost-analysis.md) z możliwościami analizowania kosztów według klientów, subskrypcji, grup zasobów, zasobów, mierników, usług i wielu innych wymiarów.
- Wyświetlania kosztów zasobów, w przypadku których w analizie kosztów uwzględniono środki uzyskane przez partnerów.
- Skonfigurowania powiadomień i automatyzacji przy użyciu programowych [budżetów](tutorial-acm-create-budgets.md) i alertów, gdy koszty przekraczają budżety.
- Włączania zasad usługi Azure Resource Manager, które zapewniają klientom dostęp do danych usługi Cost Management. Klienci mogą następnie wyświetlać dane dotyczące kosztów zużycia dla swoich subskrypcji, w których stosowane są [stawki płatności zgodnie z rzeczywistym użyciem](https://azure.microsoft.com/pricing/calculator/).
- Wyeksportowania danych kosztów i użycia do obiektu blob magazynu z subskrypcją płatną zgodnie z rzeczywistym użyciem.

Oto przykład przedstawiający koszty dla wszystkich klientów.

[![Przykład przedstawiający koszty dla wszystkich klientów](./media/get-started-partners/customer-costs1.png)](./media/get-started-partners/customer-costs1.png#lightbox)

Oto przykład przedstawiający koszty dla jednego klienta.

[![Przykład przedstawiający koszty dla jednego klienta](./media/get-started-partners/customer-costs2.png)](./media/get-started-partners/customer-costs2.png#lightbox)

Wszystkie funkcje dostępne w usłudze Azure Cost Management są również dostępne w interfejsach API REST. Użyj interfejsów API do automatyzowania zadań zarządzania kosztami.

## <a name="prerequisites"></a>Wymagania wstępne

Jako partner masz usługę Azure Cost Management natywnie dostępną tylko dla subskrypcji, które znajdują się w planie platformy Azure.

Aby włączyć usługę Azure Cost Management w witrynie Azure Portal, musisz potwierdzić zgodę klienta na Umowę z Klientem Microsoft (w imieniu klienta) i przenieść klienta do planu platformy Azure. W usłudze Azure Cost Management są dostępne tylko koszty subskrypcji, które są przenoszone do planu platformy Azure.

Usługa Azure Cost Management wymaga dostępu do odczytu do konta rozliczeniowego lub subskrypcji.

Aby uzyskać więcej informacji na temat włączania i przypisywania dostępu do usługi Azure Cost Management dla konta rozliczeniowego, zobacz [Przypisywanie ról i uprawnień użytkowników](/partner-center/permissions-overview). Kosztami dla konta rozliczeniowego mogą zarządzać role **Administrator globalny** i **Agent administratora**.

Aby uzyskać dostęp do usługi Azure Cost Management w zakresie subskrypcji, każdy użytkownik z dostępem RBAC platformy Azure do subskrypcji może wyświetlać koszty po stawkach detalicznych (płatność zgodnie z rzeczywistym użyciem). Jednak [zasady widoczności kosztów dla dzierżawy klienta](#enable-the-policy-to-view-azure-usage-charges) muszą być włączone. Aby wyświetlić pełną listę obsługiwanych typów kont, zobacz [Omówienie danych usługi Cost Management](understand-cost-mgt-data.md).

W przypadku przenoszenia istniejących umów dotyczących rozliczeń do nowego partnera funkcje zarządzania kosztami są dostępne tylko dla bieżącej relacji rozliczania z partnerem. Koszty historyczne przed przeniesieniem do nowego partnera nie są przenoszone na nowe konto rozliczeniowe. Jednak historia kosztów pozostaje w oryginalnym skojarzonym koncie rozliczania.

## <a name="how-cost-management-uses-scopes"></a>Jak usługa Cost Management używa zakresów

Zakresy to miejsce, w którym zarządzasz danymi dotyczącymi rozliczeń, masz role specyficzne dla płatności, wyświetlasz faktury i ogólnie zarządzasz kontami. Role rozliczeń i kont są zarządzane oddzielnie od zakresów używanych do zarządzania zasobami, które używają systemu RBAC platformy Azure. Aby wyraźnie odróżnić przeznaczenie poszczególnych zakresów, a w tym różnice w kontroli dostępu, są one określane odpowiednio jako zakresy rozliczeń i zakresy RBAC platformy Azure.

Aby zrozumieć zakresy rozliczeń i zakresy RBAC platformy Azure oraz to, jak działa zarządzanie kosztami przy użyciu zakresów, zobacz [Opis zakresów i praca z nimi](understand-work-scopes.md).

## <a name="manage-costs-with-partner-tenant-billing-scopes"></a>Zarządzanie kosztami przy użyciu zakresów rozliczeń dzierżawy partnera

Po dodaniu klientów do Umowy z Klientem Microsoft w Twojej dzierżawie są dostępne następujące _zakresy rozliczeń_. Użyj zakresów, aby zarządzać kosztami w usłudze Cost Management.

### <a name="billing-account-scope"></a>Zakres konta rozliczeniowego

Zakres konta rozliczeniowego służy do wyświetlania kosztów przed opodatkowaniem dla wszystkich klientów i profilów rozliczeniowych. Koszty faktury są pokazywane tylko dla tych produktów klienta bazujących na użyciu, które są zawarte w Umowie z Klientem Microsoft. W przypadku produktów dla klientów bazujących na zakupach koszty faktury są pokazywane dla produktów w Umowie z Klientem Microsoft i w ofercie dostawcy usług w chmurze. Obecnie domyślną walutą do wyświetlania kosztów w zakresie są dolary amerykańskie (USD). Budżety ustawione dla zakresu są również w USD.

Niezależnie od różnych naliczanych walut, partnerzy używają zakresu konta rozliczeniowego do ustalania budżetów i zarządzania kosztami w USD dla swoich klientów, subskrypcji, zasobów i grup zasobów.

Partnerzy filtrują także koszty w określonej walucie rozliczeniowej dla klientów w widoku analizy kosztów. Wybierz listę **Koszty rzeczywiste**, aby wyświetlić koszty w obsługiwanych walutach rozliczeniowych.

![Przykład pokazujący wybór kosztów rzeczywistych dla walut](./media/get-started-partners/actual-cost-selector.png)

Użyj [widoku kosztu amortyzowanego](quick-acm-cost-analysis.md#customize-cost-views) w zakresach rozliczeniowych, aby wyświetlić amortyzowane koszty wystąpienia zarezerwowanego w ramach okresu rezerwacji.

### <a name="billing-profile-scope"></a>Zakres profilu rozliczeniowego

Zakres profilu rozliczeniowego umożliwia wyświetlanie kosztów przed opodatkowaniem w walucie rozliczeń dla wszystkich klientów oraz wszystkich produktów i subskrypcji zawartych na fakturze. Możesz filtrować koszty w profilu rozliczeniowym dla określonej faktury przy użyciu filtru **InvoiceID**. Filtr pokazuje koszty użycia i zakupu produktów dla określonej faktury. Możesz również filtrować koszty dla określonego klienta na fakturze, aby zobaczyć koszty przed opodatkowaniem.

Po dołączeniu klientów do Umowy z Klientem Microsoft otrzymasz fakturę zawierającą wszystkie opłaty za wszystkie produkty (użycie, zakupy i upoważnienia) dla tych klientów w Umowie z Klientem Microsoft. W przypadku rozliczania w tej samej walucie faktury te obejmują również opłaty za upoważnienia i zakupione produkty, takie jak SaaS, Azure Marketplace i rezerwacje dla klientów, którzy nadal są objęci klasyczną ofertą dostawcy usług w chmurze poza planem platformy Azure.

Aby pomóc w uzgadnianiu opłat z fakturą klienta, zakres profilu rozliczeniowego umożliwia wyświetlenie wszystkich kosztów, które naliczane są za faktury dla klientów. Podobnie jak w przypadku faktury, zakres pokazuje koszty każdego klienta w nowej Umowie z Klientem Microsoft. Zakres pokazuje również wszystkie opłaty za produkty związane z upoważnieniami klienta nadal dostępne w bieżącej ofercie dostawcy CSP.

Zakresy profilu rozliczeniowego i konta rozliczeniowego są jedynymi odpowiednimi zakresami, które pokazują opłaty za produkty bazujące na upoważnieniach i zakupach, takie jak Azure Marketplace i zakupy rezerwacji.

Profile rozliczeniowe definiują subskrypcje uwzględnione na fakturze. Profile rozliczeniowe to funkcjonalny odpowiednik rejestracji w ramach umowy Enterprise Agreement. Profil rozliczeniowy jest zakresem, w którym są generowane faktury.

Obecnie waluta rozliczeniowa jest walutą domyślną podczas wyświetlania kosztów w zakresie profilu rozliczeniowego. Budżety ustawione w zakresie profilu rozliczeniowego są wyrażone w walucie rozliczeniowej.

Partnerzy mogą używać zakresu, aby uzgadniać faktury. Wykorzystują oni zakres do ustalania budżetów w walucie rozliczeniowej dla następujących elementów:

- Konkretna przefiltrowana faktura
- Klient
- Subskrypcja
- Grupa zasobów
- Zasób
- Usługa platformy Azure
- Miernik
- ResellerMPNID

### <a name="customer-scope"></a>Zakres klienta

Partnerzy korzystają z zakresu, aby zarządzać kosztami związanymi z klientami dołączonymi do Umowy z Klientem Microsoft. Zakres umożliwia partnerom wyświetlanie kosztów przed opodatkowaniem dla określonego klienta w walucie rozliczeniowej. Możesz również filtrować koszty przed opodatkowaniem dla określonej subskrypcji, grupy zasobów lub zasobu.

Zakres klienta nie obejmuje klientów, którzy są objęci bieżącą ofertą dostawcy usług w chmurze. Zakres zawiera tylko tych klientów, którzy mają Umowę z Klientem Microsoft. Po zastosowaniu filtru klienta dla klientów objętych bieżącą ofertą dostawcy usług w chmurze w zakresach konta rozliczeniowego i profilu rozliczeniowego są dostępne koszty upoważnienia, a nie użycia platformy Azure. Budżety ustalone w tym zakresie są podane w walucie rozliczeniowej.

## <a name="partner-access-to-billing-scopes-in-cost-management"></a>Dostęp partnerów do zakresów rozliczeniowych w usłudze Cost Management

Tylko użytkownicy mający role **Administrator globalny** i **Agent administratora** mogą wyświetlać koszty dla kont rozliczeniowych, profilów rozliczeniowych i klientów bezpośrednio w dzierżawie platformy Azure partnera oraz zarządzać nimi. Aby uzyskać więcej informacji na temat ról Centrum partnerskiego, zobacz [Przypisywanie ról i uprawnień użytkowników](/partner-center/permissions-overview).

## <a name="enable-cost-management-for-customer-tenant-subscriptions"></a>Włączanie zarządzania kosztami dla subskrypcji dzierżawy klienta

Partnerzy mogą włączyć dostęp do usługi Cost Management po dołączeniu klientów do Umowy z Klientem Microsoft. Następnie partnerzy mogą włączyć zasady umożliwiające klientom wyświetlanie ich kosztów usług używanych w ramach platformy Azure obliczonych na podstawie stawek detalicznych za płatność zgodnie z rzeczywistym użyciem. Koszty są pokazane w walucie rozliczeniowej klienta dla ich użycia w ramach subskrypcji systemu RBAC platformy Azure i zakresów grup zasobów.

Po włączeniu przez partnera zasad dotyczących widoczności kosztów każdy użytkownik, który ma dostęp w usłudze Azure Resource Manager do subskrypcji, może zarządzać kosztami i analizować je według stawek płatności zgodnie z rzeczywistym użyciem. Efektywnie odsprzedawcy i klienci, którzy mają odpowiedni dostęp RBAC platformy Azure do subskrypcji platformy Azure, mogą wyświetlać koszt.

Niezależnie od zasad administratorzy globalni i agenci administratora dostawcy mogą wyświetlać koszty subskrypcji, jeśli mają dostęp do subskrypcji i grupy zasobów.

### <a name="enable-the-policy-to-view-azure-usage-charges"></a>Włączanie zasad w celu wyświetlania opłat za użycie platformy Azure

Aby wyświetlać i aktualizować zasady, musisz być członkiem grupy **agent administrator**. Użyj następujących informacji, aby włączyć zasady umożliwiające klientom wyświetlanie opłat za użycie platformy Azure.

W witrynie Azure Portal zaloguj się do *dzierżawy partnera* i wybierz pozycję **Zarządzanie kosztami i rozliczenia**. Wybierz odpowiedni zakres rozliczeniowy w obszarze Zakres rozliczeniowy, a następnie wybierz pozycję **Klienci**. Lista klientów jest skojarzona z kontem rozliczeniowym. *Jeśli omyłkowo zalogujesz się do dzierżawy klienta, nie zobaczysz listy **Klienci**.*

Na liście klientów wybierz klienta, który ma być uprawniony do wyświetlania kosztów.

[![Wybieranie klientów w usłudze Cost Management](./media/get-started-partners/customer-list.png)](./media/get-started-partners/customer-list.png#lightbox)

W obszarze **Ustawienia** wybierz pozycję **Zasady**.

Bieżące zasady widoczności kosztów są wyświetlane dla opłat **za użycie platformy Azure** skojarzonych z subskrypcjami dla wybranego klienta.
![Zasady umożliwiające klientom wyświetlanie opłat z tytułu płatności zgodnie z rzeczywistym użyciem](./media/get-started-partners/cost-management-billing-policies.png)

Jeśli zasady są ustawione na **Nie**, usługa Azure Cost Management jest niedostępna dla użytkowników subskrypcji skojarzonej z klientem. O ile nie zostaną one włączone przez partnera, zasady widoczności kosztów są domyślnie wyłączone dla wszystkich użytkowników subskrypcji.

Gdy zasady kosztów zostaną ustawione na wartość **Tak**, użytkownicy subskrypcji powiązani z dzierżawą klienta mogą zobaczyć opłaty za użycie w ramach stawek płatności zgodnie z rzeczywistym użyciem.

Po włączeniu zasad widoczności kosztów wszystkie usługi mające użycie subskrypcji wykazują koszty według stawek płatności zgodnie z rzeczywistym użyciem. Użycie rezerwacji jest wyświetlane z zerowymi opłatami za rzeczywiste i amortyzowane koszty. Zakupy i upoważnienia nie są skojarzone z konkretną subskrypcją. W związku z tym zakupy nie są wyświetlane w zakresie subskrypcji. Administrator globalny / agent administracyjny bezpośredniego partnera lub dostawcy pośredniego może również użyć funkcji [Zaktualizuj interfejs API klienta](/rest/api/billing/2019-10-01-preview/policies/updatecustomer), aby ustawić zasady widoczności kosztów każdego klienta na dużą skalę.

### <a name="view-subscription-costs-in-the-customer-tenant"></a>Wyświetlanie kosztów subskrypcji w dzierżawie klienta

Aby wyświetlić koszty dla subskrypcji, otwórz panel **Zarządzanie kosztami i rozliczenia** w dzierżawie platformy Azure klienta. Wybierz pozycję **Analiza kosztów** dla wymaganej subskrypcji, aby rozpocząć przeglądanie kosztów. Koszty użycia poszczególnych subskrypcji można wyświetlić indywidualnie w dzierżawie klienta.

[![Wyświetlanie analizy kosztów jako klient](./media/get-started-partners/subscription-costs.png)](./media/get-started-partners/subscription-costs.png#lightbox)

Analiza kosztów, budżety i alerty są dostępne dla zakresów subskrypcji i zakresów RBAC platformy Azure grup zasobów z kosztami bazującymi na płatności zgodnie z rzeczywistym użyciem.

Widoki amortyzowane i rzeczywiste koszty dla wystąpień zarezerwowanych w zakresach RBAC platformy Azure pokazują zero opłat. Koszty zakupu dla uprawnień, takich jak wystąpienia zarezerwowane i opłaty w witrynie Marketplace, są wyświetlane tylko w zakresach rozliczeń w dzierżawie partnera, w których dokonano zakupów.

Stawki detaliczne używane do obliczania kosztów pokazanych w widoku to te same ceny, które są widoczne w kalkulatorze cen platformy Azure dla wszystkich klientów. Pokazane koszty nie obejmują żadnych rabatów ani środków, które mogą być w posiadaniu partnera, takich jak środki uzyskane przez partnerów, rabaty dotyczące warstw i zniżki na usługi globalne.

 

## <a name="analyze-costs-in-cost-analysis"></a>Analizowanie kosztów w obrębie analizy kosztów

Partnerzy z dostępem do zakresów rozliczeniowych w dzierżawie partnera mogą badać i analizować zafakturowane koszty w analizie kosztów u klientów dla określonego klienta lub faktury. W widoku [Analiza kosztów](quick-acm-cost-analysis.md) możesz także [zapisywać widoki](quick-acm-cost-analysis.md#saving-and-sharing-customized-views) i eksportować dane do [plików CSV i PNG](quick-acm-cost-analysis.md#download-usage-data).

Użytkownicy systemu RBAC platformy Azure z dostępem do subskrypcji w dzierżawie klienta mogą również analizować koszty sprzedaży detalicznej dla subskrypcji w dzierżawie klienta, zapisywać widoki oraz eksportować dane do plików CSV i PNG.

W celu przeanalizowania kosztów według wielu pól możesz użyć funkcji filtrowania i grupowania w analizie kosztów. Pola specyficzne dla partnera są wyświetlane w następnej sekcji.

## <a name="data-fields"></a>Pola danych

Następujące pola danych znajdują się w plikach szczegółów użycia i interfejsach API usługi Cost Management. Jeśli są dostępne, wyświetlane są równoważne informacje z Centrum partnerskiego. W przypadku następujących pogrubionych pól partnerzy mogą używać funkcji filtrowania i grupowania w analizie kosztów, aby analizować koszty według wielu pól. Pola pogrubione dotyczą tylko Umów z Klientem Microsoft obsługiwanych przez partnerów.

| **Nazwa pola** | **Opis** | **Odpowiednik z Centrum partnerskiego** |
| --- | --- | --- |
| invoiceId | Identyfikator faktury znajdujący się na fakturze dla określonej transakcji. | Numer faktury, na której jest pokazywana transakcja. |
| previousInvoiceID | Odwołanie do oryginalnej faktury, gdzie jest zwrot (koszt ujemny). Wypełniane tylko, gdy istnieje zwrot. | Nie dotyczy |
| billingAccountName | Nazwa konta rozliczeniowego reprezentującego partnera. Naliczane są wszystkie koszty dla klientów, którzy zostali dołączeni do Umowy z Klientem Microsoft oraz klientów dostawcy usług w chmurze, którzy mieli upoważnienie do zakupów, takich jak SaaS, Azure Marketplace i rezerwacje. | Nie dotyczy |
| billingAccountID | Identyfikator konta rozliczeniowego reprezentującego partnera. | Identyfikator głównego elementu komercyjnego partnera MCAPI. Używane w żądaniu, ale nie zawarte w odpowiedzi.|
| billingProfileID | Identyfikator profilu rozliczeniowego, który grupuje koszty faktur w ramach pojedynczej waluty rozliczeniowej dla klientów, którzy zostali dołączeni do Umowy z Klientem Microsoft, oraz klientów dostawcy usług w chmurze, którzy dokonywali zakupów upoważnień, takich jak SaaS, Azure Marketplace i rezerwacje. | Identyfikator grupy rozliczeniowej partnera MCAPI. Używane w żądaniu, ale nie zawarte w odpowiedzi. |
| billingProfileName | Nazwa profilu rozliczeniowego, który grupuje koszty faktur w ramach pojedynczej waluty rozliczeniowej dla klientów, którzy zostali dołączeni do Umowy z Klientem Microsoft oraz klientów dostawcy usług w chmurze, którzy dokonywali zakupów upoważnień, takich jak SaaS, Azure Marketplace i rezerwacje. | Nie dotyczy |
| invoiceSectionName | Nazwa projektu, za który opłaty są umieszczane na fakturze. Nie dotyczy Umów z Klientem Microsoft dołączonych przez partnerów. | Nie dotyczy |
| invoiceSectionID | Identyfikator projektu, za który opłaty są umieszczane na fakturze. Nie dotyczy Umów z Klientem Microsoft dołączonych przez partnerów. | Nie dotyczy |
| **CustomerTenantID** | Identyfikator dzierżawy usługi Azure Active Directory subskrypcji klienta. | Identyfikator organizacyjny klienta — identyfikator TenantID usługi Azure Active Directory klienta. |
| **CustomerName** | Nazwa dzierżawy usługi Azure Active Directory dla subskrypcji klienta. | Nazwa organizacji klienta w postaci pokazywanej w Centrum partnerskim. Ważne w przypadku uzgadniania faktury z informacjami w systemie. |
| **CustomerTenantDomainName** | Nazwa domeny dla dzierżawy usługi Azure Active Directory dla subskrypcji klienta. | Domena dzierżawy usługi Azure Active Directory klienta. |
| **PartnerTenantID** | Identyfikator dzierżawy usługi Azure Active Directory partnera. | Identyfikator dzierżawy usługi Azure Active Directory partnera, nazywany identyfikatorem partnera, w formacie identyfikatora GUID. |
| **PartnerName** | Nazwa dzierżawy usługi Azure Active Directory partnera. | Nazwa partnera. |
| **ResellerMPNID** | Identyfikator MPNID odsprzedawcy skojarzonego z subskrypcją. | Identyfikator MPN odsprzedawcy zarejestrowanego dla subskrypcji. Niedostępne dla bieżącego działania. |
| costCenter | Centrum kosztów skojarzone z subskrypcją. | Nie dotyczy |
| billingPeriodStartDate | Data rozpoczęcia okresu rozliczeniowego pokazana na fakturze. | Nie dotyczy |
| billingPeriodEndDate | Data zakończenia okresu rozliczeniowego pokazana na fakturze. | Nie dotyczy |
| servicePeriodStartDate | Data rozpoczęcia okresu oceny, podczas którego użycie usługi zostało ocenione pod kątem opłat. Ceny usług platformy Azure są określane dla okresu oceny. | ChargeStartDate w Centrum partnerskim.  Data rozpoczęcia cyklu rozliczeniowego z wyjątkiem przypadków, gdy są przedstawiane daty wcześniej nieodpłatnych ukrytych danych użycia z poprzedniego okresu rozliczeniowego. Godzina to zawsze początek dnia, 0:00. |
| servicePeriodEndDate | Data zakończenia okresu, w którym użycie usługi zostało ocenione pod kątem opłat. Ceny usług platformy Azure są ustalane na podstawie okresu oceny. | Nie dotyczy |
| date | W przypadku danych dotyczących zużycia na platformie Azure pokazuje to datę użycia jako ocenioną. W przypadku wystąpienia zarezerwowanego jest wyświetlana data zakupu. W przypadku naliczania opłat cyklicznych i jednorazowych, takich jak usługa Marketplace i pomoc techniczna, jest wyświetlana data zakupu. | Nie dotyczy |
| productID | Identyfikator produktu, dla którego naliczono opłaty według zużycia lub zakupu. Jest to połączenie klucza identyfikatorów productID i SKuID, jak pokazano w Centrum partnerskim. | Identyfikator produktu. |
| product | Nazwa produktu, który ma naliczone opłaty według zużycia lub zakupu, jak pokazano na fakturze. | Nazwa produktu w katalogu. |
| serviceFamily | Pokazuje rodzinę usług dla zakupionego lub naliczonego produktu. Na przykład Storage lub Compute. | Nie dotyczy |
| productOrderID | Identyfikator zasobu lub nazwa planu platformy Azure, do którego należy subskrypcja. Na przykład Plan platformy Azure. | CommerceSubscriptionID |
| productOrderName | Nazwa planu platformy Azure, do którego należy subskrypcja. Na przykład Plan platformy Azure. | Nie dotyczy|
| consumedService | Użycie usługi (starsza taksonomia) w ramach szczegółowych informacji o użyciu starszych wersji umowy EA. | Usługa wyświetlana w Centrum partnerskim. Na przykład Microsoft.Storage, Microsoft.Compute i microsoft.operationalinsights. |
| meterID | Identyfikator miernika dla mierzonego zużycia. | Identyfikator używanego miernika. |
| meterName | Określa nazwę miernika dla mierzonego zużycia. | Nazwa użytego miernika. |
| meterCategory | Określa usługę najwyższego poziomu do użycia. | Usługa najwyższego poziomu do użycia. |
| meterSubCategory | Definiuje typ lub podkategorię usługi platformy Azure, która może wpływać na stawkę. | Typ usługi platformy Azure, który może wpływać na stawkę.|
| meterRegion | Określa lokalizację centrum danych pewnych usług, które są wyceniane na podstawie lokalizacji centrum danych. | Regionalna lokalizacja centrum danych dla usług, jeśli ma to zastosowanie i jest wypełnione. |
| subscription ID | Unikatowy identyfikator wygenerowany przez firmę Microsoft dla subskrypcji platformy Azure. | EntitlementID |
| subscriptionName | Nazwa subskrypcji platformy Azure. | Nie dotyczy |
| Okres | Przedstawia okres ważności oferty. Na przykład wystąpienia zarezerwowane pokazują 12 miesięcy dla rocznej rezerwacji wystąpienia. W przypadku zakupów jednorazowych lub cyklicznych okres to jeden miesiąc dla usługi SaaS, Azure Marketplace i pomocy technicznej. Nie dotyczy użycia platformy Azure. | Nie dotyczy |
| publisherType (firstParty, thirdPartyReseller, thirdPartyAgency) | Typ wydawcy, który identyfikuje wydawcę jako własną firmę albo odsprzedawcę lub agencję będącą podmiotem trzecim. | Nie dotyczy |
| partNumber | Numer części nieużywanego wystąpienia zarezerwowanego i usług Azure Marketplace. | Nie dotyczy |
| publisherName | Nazwa wydawcy usługi,w tym firmy Microsoft lub innej. | Nazwa wydawcy produktu.|
| reservationId | Identyfikator zakupu wystąpienia zarezerwowanego. | Nie dotyczy |
| reservationName | Nazwa wystąpienia zarezerwowanego. | Nie dotyczy |
| reservationOrderId | Identyfikator OrderID wystąpienia zarezerwowanego. | Nie dotyczy |
| frequency | Częstotliwość płatności dla wystąpienia zarezerwowanego. | Nie dotyczy |
| resourceGroup | Nazwa grupy zasobów platformy Azure używanej do zarządzania zasobami cyklu życia. | Nazwa grupy zasobów. |
| instanceID (lub) ResourceID | Identyfikator wystąpienia zasobu. | Pokazywane jako identyfikator ResourceURI, który obejmuje pełne właściwości zasobu. |
| resourceLocation | Nazwa lokalizacji zasobu. | Lokalizacja zasobu. |
| Lokalizacja | Znormalizowana lokalizacja zasobu. | Nie dotyczy |
| effectivePrice | Obowiązująca cena jednostkowa usługi w walucie podanej w cenniku. Unikatowa dla produktu, rodziny usług, miernika i oferty. Używana z cenami w arkuszu cen dla konta rozliczeniowego. W przypadku cen warstwowych lub uwzględnionej ilości pokazuje połączoną cenę zużycia. | Cena jednostkowa po dokonaniu korekt. |
| Liczba | Zmierzona ilość zakupiona lub zużyta. Ilość wskazana przez miernik użycia w okresie rozliczeniowym. | Liczba jednostek. Upewnij się, że jest ona zgodna z informacjami w systemie rozliczeniowym podczas uzgadniania. |
| unitOfMeasure | Identyfikuje jednostkę, w której rozliczana jest usługa. Na przykład GB i godziny. | Identyfikuje jednostkę, w której rozliczana jest usługa. Na przykład GB, godziny i dziesiątki tysięcy. |
| pricingCurrency | Waluta określająca cenę jednostkową. | Waluta w cenniku.|
| billingCurrency | Waluta definiująca koszt naliczany. | Waluta zdefiniowana jako waluta rozliczania na fakturze. |
| chargeType | Określa typ opłaty reprezentowanej przez koszt w usłudze Azure Cost Management, taki jak zakup i zwrot. | Typ opłaty lub korekty. Niedostępne dla bieżącego działania. |
| costinBillingCurrency | Koszt rozszerzony lub zmieszany przed opodatkowaniem w walucie rozliczeniowej. | Nie dotyczy |
| costinPricingCurrency | Koszt rozszerzony lub zmieszany przed opodatkowaniem w walucie cennika w celu skorelowania z cenami. | Nie dotyczy |
| **costinUSD** | Szacowany koszt rozszerzony lub zmieszany przed opodatkowaniem w USD. | Nie dotyczy |
| **paygCostInBillingCurrency** | Pokazuje koszty, jeśli cennik zawiera ceny detaliczne. Pokazuje ceny płatności zgodnie z rzeczywistym użyciem w walucie rozliczeniowej. Dostępne tylko w zakresach RBAC platformy Azure. | Nie dotyczy |
| **paygCostInUSD** | Pokazuje koszty, jeśli cennik zawiera ceny detaliczne. Pokazuje ceny płatności zgodnie z rzeczywistym użyciem w USD. Dostępne tylko w zakresach RBAC platformy Azure. | Nie dotyczy |
| exchangeRate | Kurs wymiany używany do konwersji z waluty cennika na walutę rozliczeń. | Określany jako PCToBCExchangeRate w Centrum partnerskim. Kurs wymiany waluty cennika na walutę rozliczeń.|
| exchangeRateDate | Data kursu wymiany używanego do konwersji z waluty cennika na walutę rozliczeń. | Określana jako PCToBCExchangeRateDat w Centrum partnerskim. Data kursu wymiany waluty cennika na walutę rozliczeń.|
| isAzureCreditEligible | Wskazuje, czy koszt można opłacić ze środków na korzystanie z platformy Azure. | Nie dotyczy |
| serviceInfo1 | Starsze pole, które zawiera opcjonalne metadane właściwe dla usługi. | Wewnętrzne metadane usługi platformy Azure. |
| serviceInfo2 | Starsze pole, które zawiera opcjonalne metadane właściwe dla usługi. | Informacje o usłudze. Na przykład typ obrazu dla maszyny wirtualnej i nazwa usługodawcy internetowego dla usługi ExpressRoute.|
| additionalInfo | Metadane dotyczące konkretnej usługi. Na przykład typ obrazu dla maszyny wirtualnej. | Wszelkie dodatkowe informacje, które nie są zawarte w innych kolumnach. Metadane specyficzne dla usługi. Na przykład typ obrazu dla maszyny wirtualnej.|
| tags | Tag przypisany do miernika. Użyj tagów, aby zgrupować rekordy rozliczeń. Na przykład możesz użyć tagów do dystrybucji kosztów według działu używającego danego miernika. | Tagi dodane przez klienta.|
| **partnerEarnedCreditRate** | Stawka rabatu stosowana, gdy partner uzyskał środki na podstawie dostępu do linku administratora partnera. | Stawka za środki uzyskane przez partnerów. Na przykład 0% lub 15%. |
| **partnerEarnedCreditApplied** | Wskazuje, czy są stosowane środki uzyskane przez partnerów. | Nie dotyczy |

## <a name="view-partner-earned-credit-pec-resource-costs"></a>Wyświetlanie kosztów zasobów dla środków uzyskanych przez partnerów

W usłudze Azure Cost Management partnerzy mogą korzystać z analizy kosztów, aby wyświetlać koszty, dla których uwzględniono środki uzyskane przez partnerów.

W witrynie Azure Portal zaloguj się do dzierżawy partnera i wybierz pozycję **Zarządzanie kosztami i rozliczenia**. W obszarze **Zarządzanie kosztami** wybierz pozycję **Analiza kosztów**.

W widoku Analiza kosztów są wyświetlane koszty z konta rozliczeniowego dla partnera. W razie potrzeby wybierz **zakres** dla partnera, określonego klienta lub profilu rozliczeniowego, aby uzgodnić faktury.

Na wykresie pierścieniowym wybierz listę rozwijaną, a następnie wybierz właściwość **PartnerEarnedCreditApplied**, aby przejść do szczegółów dotyczących kosztów dla środki uzyskane przez partnerów.

![Przykład przedstawiający sposób wyświetlania środków uzyskanych przez partnerów](./media/get-started-partners/cost-analysis-pec1.png)

Gdy właściwość **PartnerEarnedCreditApplied** ma wartość _True_, skojarzony koszt ma wliczoną korzyść dostępu administratora uzyskanego przez partnera.

Gdy właściwość **PartnerEarnedCreditApplied** ma wartość _False_, skojarzony koszt nie ma wymaganych uprawnień do środków. Możliwe też, że zakupiona usługa nie kwalifikuje się do środków uzyskanych przez partnerów.

Wyświetlenie danych użycia usługi w usłudze Cost Management zwykle zajmuje od 8 do 24 godzin. Aby uzyskać więcej informacji, zobacz [Aktualizacje oraz przechowywanie danych dotyczących kosztów i użycia](understand-cost-mgt-data.md#cost-and-usage-data-updates-and-retention). Środki uzyskane przez partnerów są wyświetlane w ciągu 48 godzin od momentu uzyskania dostępu w usłudze Azure Cost Management.


Możesz również grupować i filtrować według właściwości **PartnerEarnedCreditApplied** przy użyciu opcji **Grupuj według**. Użyj opcji, aby zbadać koszty, które mają i nie mają środków uzyskanych przez partnerów.

![Grupowanie lub filtrowanie według środków uzyskanych przez partnerów](./media/get-started-partners/cost-analysis-pec2.png)

## <a name="export-cost-data-to-azure-storage"></a>Eksportowanie danych kosztów do usługi Azure Storage

Partnerzy z dostępem do zakresów rozliczeniowych w dzierżawie partnera mogą eksportować swoje dane dotyczące kosztów i użycia do obiektu blob usługi Azure Storage. Obiekt blob musi znajdować się w subskrypcji w dzierżawie partnera, która nie jest [subskrypcją usługi udostępnionej](/partner-center/shared-services) ani subskrypcją klienta. Aby włączyć eksportowanie danych kosztów, zalecamy skonfigurowanie niezależnej subskrypcji płatnej zgodnie z rzeczywistym użyciem w dzierżawie partnera w celu hostowania wyeksportowanych danych kosztów. Konto magazynu do eksportowania jest tworzone w obiekcie blob usługi Azure Storage hostowanej w ramach subskrypcji z opcją płatności zgodnie z rzeczywistym użyciem. Na podstawie zakresu, w którym partner tworzy eksport, skojarzone dane są automatycznie cyklicznie eksportowane na konto magazynu.

Użytkownicy z dostępem RBAC platformy Azure do subskrypcji mogą również eksportować dane kosztów do obiektu blob usługi Azure Storage hostowanego w dowolnej subskrypcji w dzierżawie klienta.

### <a name="create-an-export-in-a-partner-tenant-or-customer-tenant"></a>Tworzenie eksportu w dzierżawie partnera lub dzierżawie klienta

W witrynie Azure Portal zaloguj się do dzierżawy partnera lub dzierżawy klienta, a następnie wybierz pozycję **Zarządzanie kosztami i rozliczenia**. Wybierz odpowiedni zakres, na przykład konto rozliczeniowe z umową Microsoft Partner Agreement, a następnie wybierz pozycję **Analiza kosztów**. Po załadowaniu strony wybierz pozycję **Eksportuj**. Wybierz pozycję **Wyświetl wszystkie eksporty** w obszarze Zaplanuj eksport.

![Wybieranie pozycji Eksportuj i Wyświetl wszystkie eksporty](./media/get-started-partners/export01.png)

Następnie wybierz pozycję **Dodaj** oraz wpisz nazwę i wybierz typ eksportu. Wybierz kartę **Magazyn** i wprowadź wymagane informacje.

![Karta Dodawanie nowego eksportu i wybieranie magazynu](./media/get-started-partners/export02.png)

Po utworzeniu eksportu w dzierżawie partnera wybierz subskrypcję z płatnością zgodnie z rzeczywistym użyciem w dzierżawie partnera. Utwórz konto usługi Azure Storage za pomocą tej subskrypcji.

W przypadku użytkowników systemu RBAC platformy Azure w dzierżawie klienta wybierz subskrypcję w dzierżawie klienta. Utwórz konto usługi Azure Storage za pomocą tej subskrypcji.

Przejrzyj zawartość, a następnie wybierz pozycję **Utwórz**, aby zaplanować eksport.

Aby sprawdzić dane na liście eksportu, wybierz nazwę konta magazynu. Na stronie konta magazynu wybierz pozycję **Kontenery**, a następnie wybierz kontener. Przejdź do odpowiedniego folderu i wybierz plik CSV. Wybierz pozycję **Pobierz**, aby pobrać plik CSV i otworzyć go. Wyeksportowane dane przypominają dane kosztów podobne do szczegółów użycia z witryny Azure Portal.

![Przykład wyeksportowanych danych](./media/get-started-partners/example-export-data.png)

## <a name="cost-management-rest-apis"></a>Interfejsy API REST usługi Cost Management

Partnerzy i klienci mogą używać interfejsów API usługi Cost Management opisanych w poniższych sekcjach do typowych zadań.

### <a name="azure-cost-management-apis---direct-and-indirect-providers"></a>Interfejsy API usługi Azure Cost Management — bezpośredni i pośredni dostawcy

Partnerzy z dostępem do zakresów rozliczeniowych w dzierżawie partnera mogą używać następujących interfejsów API do wyświetlania zafakturowanych kosztów.

Interfejsy API w zakresie subskrypcji mogą być wywoływane przez partnera niezależnie od zasad kosztów, jeśli mają dostęp do subskrypcji. Inni użytkownicy z dostępem do subskrypcji, tacy jak klient lub odsprzedawca, mogą wywoływać interfejsy API tylko po włączeniu przez partnera zasad kosztów dla dzierżawy klienta.


#### <a name="to-get-a-list-of-billing-accounts"></a>Aby uzyskać listę kont rozliczeniowych

```
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts?api-version=2019-10-01-preview
```

#### <a name="to-get-a-list-of-customers"></a>Aby uzyskać listę klientów

```
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/customers?api-version=2019-10-01-preview
```

#### <a name="to-get-a-list-of-subscriptions"></a>Aby uzyskać listę subskrypcji

```
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/billingSubscriptions?api-version=2019-10-01-preview
```

#### <a name="to-get-a-list-of-invoices-for-a-period-of-time"></a>Aby uzyskać listę faktur w danym czasie

```
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/invoices?api-version=2019-10-01-preview&periodStartDate={periodStartDate}&periodEndDate={periodEndDate}
```

Wywołanie interfejsu API zwraca tablicę faktur, która ma elementy podobne do poniższego kodu JSON.

```
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/{billingAccountID}/billingProfiles/{BillingProfileID}/invoices/{InvoiceID}",
      "name": "{InvoiceID}",
      "properties": {
        "amountDue": {
          "currency": "USD",
          "value": x.xx
        },
        ...
    }
```

Użyj poprzednio zwróconej wartości pola ID i zastąp ją w poniższym przykładzie zakresem zapytania o szczegóły użycia.

```
GET https://management.azure.com/{id}/providers/Microsoft.Consumption/UsageDetails?api-version=2019-10-01
```

Przykład zwraca rekordy użycia skojarzone z określoną fakturą.


#### <a name="to-get-the-policy-for-customers-to-view-costs"></a>Aby uzyskać zasady dotyczące wyświetlania kosztów przez klientów

```
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/customers/{customerID}/policies/default?api-version=2019-10-01-preview
```

#### <a name="to-set-the-policy-for-customers-to-view-costs"></a>Aby określić zasady dotyczące wyświetlania kosztów przez klientów

```
PUT https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/customers/{customerID}/policies/default?api-version=2019-10-01-preview
```

#### <a name="to-get-azure-service-usage-for-a-billing-account"></a>Aby uzyskać użycie usługi platformy Azure dla konta rozliczeniowego

```
GET https://management.azure.com/providers/Microsoft.Billing/BillingAccounts/{billingAccountName}/providers/Microsoft.Consumption/usageDetails?api-version=2019-10-01
```

#### <a name="to-download-a-customers-azure-service-usage"></a>Aby pobrać użycie usługi platformy Azure przez klienta

Następujące wywołanie funkcji get jest operacją asynchroniczną.

```
GET https://management.azure.com/Microsoft.Billing/billingAccounts/{billingAccountName}/customers/{customerID}/providers/Microsoft.Consumption/usageDetails/download?api-version=2019-10-01 -verbose
```

Wywołaj identyfikator URI `Location` zwrócony w odpowiedzi, aby sprawdzić stan operacji. Gdy stan ma wartość *Ukończone*, właściwość `downloadUrl` zawiera link, którego możesz użyć do pobrania wygenerowanego raportu.


#### <a name="to-get-or-download-the-price-sheet-for-consumed-azure-services"></a>Aby uzyskać lub pobrać arkusz cen dla użytych usług platformy Azure

Najpierw użyj poniższej operacji post.

```
POST https://management.azure.com/providers/Microsoft.Billing/BillingAccounts/{billingAccountName}/billingProfiles/{billingProfileID}/pricesheet/default/download?api-version=2019-10-01-preview&format=csv" -verbose
```

Następnie wywołaj wartość właściwości operacji asynchronicznej. Przykład:

```
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/billingProfiles/{billingProfileID}/pricesheetDownloadOperations/{operation}?sessiontoken=0:11186&api-version=2019-10-01-preview
```
Poprzednie wywołanie get zwraca link pobierania zawierający arkusz cen.


#### <a name="to-get-aggregated-costs"></a>Aby uzyskać zagregowane koszty

```
POST https://management.azure.com/providers/microsoft.billing/billingAccounts/{billingAccountName}/providers/microsoft.costmanagement/query?api-version=2019-10-01
```

#### <a name="create-a-budget-for-a-partner"></a>Tworzenie budżetu dla partnera

```
PUT https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/providers/Microsoft.CostManagement/budgets/partnerworkshopbudget?api-version=2019-10-01
```

#### <a name="create-a-budget-for-a-customer"></a>Tworzenie budżetu dla klienta

```
PUT https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/customers/{customerID}/providers/Microsoft.Consumption/budgets/{budgetName}?api-version=2019-10-01
```

#### <a name="delete-a-budget"></a>Usuwanie budżetu

```
DELETE
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/providers/Microsoft.CostManagement/budgets/{budgetName}?api-version=2019-10-01
```


## <a name="next-steps"></a>Następne kroki
- [Rozpoczynanie analizowania kosztów](quick-acm-cost-analysis.md) w usłudze Cost Management
- [Tworzenie budżetów i zarządzanie nimi](tutorial-acm-create-budgets.md) w usłudze Cost Management
