---
title: Wycofanie funkcji Security Center (lipiec 2019) | Microsoft Docs
description: W tym artykule opisano funkcje w Security Center, które zostały wycofane 31 lipca 2019.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: article
ms.date: 09/10/2019
ms.author: memildin
ms.openlocfilehash: 4a760fe1e5df7cf614a68f8fa8a05926326d3edf
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "80583218"
---
# <a name="retirement-of-security-center-features-july-2019"></a>Wycofanie funkcji Security Center (lipiec 2019)

> [!NOTE]
> W tym dokumencie szczegółowo przedstawiono listę funkcji, które zostały wycofane z Azure Security Center 31 lipca 2019.
>
>

Wprowadziliśmy kilka [ulepszeń](https://azure.microsoft.com/updates/?product=security-center) Azure Security Center w ciągu sześciu miesięcy, które zaczynają się do lipca 2019.
Dzięki tym ulepszonym funkcjom firma Microsoft usunęła pewne nadmiarowe funkcje i powiązane interfejsy API z programu Security Center 31 lipca 2019.

Większość z tych wycofanych funkcji można zastąpić innymi funkcjami w Azure Security Center lub Log Analytics platformy Azure. Inne funkcje można zaimplementować przy użyciu [systemu Azure (wersja zapoznawcza)](https://azure.microsoft.com/services/azure-sentinel/).

Wycofane funkcje Security Center obejmują:

- [Pulpit nawigacyjny zdarzeń](#menu_events)
- [Wpis menu wyszukiwania](#menu_search)
- [Wyświetlanie linku dostępu & klasycznej tożsamości i dostępu (wersja zapoznawcza)](#menu_classicidentity)
- [Przycisk mapy zdarzeń zabezpieczeń na mapie alertów zabezpieczeń (wersja zapoznawcza)](#menu_securityeventsmap)
- [Niestandardowe reguły alertów (wersja zapoznawcza)](#menu_customalerts)
- [Przycisk Zbadaj w alertach zabezpieczeń ochrony przed zagrożeniami](#menu_investigate)
- [Podzbiór rozwiązań zabezpieczeń](#menu_solutions)
- [Edytuj konfiguracje zabezpieczeń dla zasad zabezpieczeń](#menu_securityconfigurations)
- [Pulpit nawigacyjny zabezpieczenia i inspekcja (pierwotnie używany w portalu OMS) dla Log Analytics obszarów roboczych](#menu_securityomsdashboard)

Ten artykuł zawiera szczegółowe informacje dotyczące każdej wycofanej funkcji oraz czynności, które można wykonać w celu zaimplementowania funkcji wymiany.

## <a name="events-dashboard"></a>Pulpit nawigacyjny zdarzeń<a name="menu_events"></a>

Security Center używa agenta Log Analytics do zbierania różnych konfiguracji związanych z zabezpieczeniami i zdarzeń z maszyn. Te zdarzenia są przechowywane w obszarach roboczych. [Pulpit nawigacyjny zdarzeń](https://docs.microsoft.com/azure/security-center/security-center-events-dashboard) umożliwia wyświetlenie danych i zapewnia punkt wejścia do log Analytics.

Wycofano pulpit nawigacyjny zdarzeń, który pojawił się po wybraniu obszaru roboczego:

![Pulpit nawigacyjny zdarzeń][2]

### <a name="events-dashboard---the-new-experience"></a>Pulpit nawigacyjny zdarzeń — nowe środowisko

Zachęcamy do korzystania z natywnych możliwości platformy Azure Log Analytics do wyświetlania istotnych zdarzeń w obszarach roboczych.

Jeśli utworzono niestandardowe zdarzenia w Security Center, będą one dostępne. W obszarze log Analytics przejdź do pozycji **Wybierz obszary robocze**  >  **zapisane wyszukiwania**. Twoje dane nie zostaną utracone ani zmodyfikowane. Natywne istotne zdarzenia są również dostępne z tego samego ekranu w Log Analytics.

![Wyszukiwania zapisane w obszarze roboczym][3]

## <a name="search-menu-entry"></a>Wpis menu wyszukiwania<a name="menu_search"></a>

Azure Security Center obecnie używa przeszukiwania dzienników Azure Monitor do pobierania i analizowania danych zabezpieczeń. Ten ekran służy jako okno do Log Analytics strony wyszukiwania i umożliwia użytkownikom uruchamianie zapytań wyszukiwania w wybranym obszarze roboczym. Aby uzyskać więcej informacji, zobacz [Azure Security Center Search](https://docs.microsoft.com/azure/security-center/security-center-search). Wycofano to okno wyszukiwania:

![Strona wyszukiwania][4]

### <a name="search-menu-entry---the-new-experience"></a>Wpis menu wyszukiwania — nowe środowisko

Zachęcamy do korzystania z funkcji macierzystych platformy Azure Log Analytics w celu wykonywania zapytań wyszukiwania w obszarach roboczych. Przejdź do usługi Azure Log Analytics i wybierz pozycję **dzienniki**.

![Strona dzienników Log Analytics][5]

## <a name="classic-identity--access-preview"></a>Dostęp do & tożsamości klasycznej (wersja zapoznawcza)<a name="menu_classicidentity"></a>

Środowisko dostępu do klasycznej tożsamości & w Security Center obecnie zawiera pulpit nawigacyjny informacji o tożsamości i dostępie w Log Analytics. Aby wyświetlić ten pulpit nawigacyjny:

1. Wybierz pozycję **Wyświetl klasyczną tożsamość & dostęp**.

   ![Strona tożsamości][6]

1. Wyświetl **pulpit nawigacyjny dostępu & tożsamości**.

    ![Strona tożsamości — wybór obszaru roboczego][7]

1. Wybierz obszar roboczy, aby otworzyć pulpit nawigacyjny **dostępu & tożsamości** w log Analytics, aby wyświetlić informacje o tożsamości i dostępie w obszarze roboczym.

   ![Strona tożsamości — pulpit nawigacyjny][8]

Wycofano wszystkie trzy ekrany przedstawione w powyższych krokach. Twoje dane pozostają dostępne w rozwiązaniu zabezpieczeń Log Analytics i nie zostały zmodyfikowane ani usunięte.

### <a name="classic-identity--access-preview---the-new-experience"></a>Klasyczna tożsamość & dostęp (wersja zapoznawcza) — nowe środowisko

Pulpit nawigacyjny Log Analytics ma wgląd w szczegółowe dane w jednym obszarze roboczym. Jednak natywne możliwości Security Center zapewniają wgląd we wszystkie subskrypcje i wszystkie powiązane z nimi obszary robocze. Możesz uzyskać dostęp do łatwego w użyciu widoku, który pozwala skupić się na ważnych kwestiach związanych z zaleceniami uporządkowanymi zgodnie z ich bezpiecznymi wynikami.

Wszystkie funkcje pulpitu nawigacyjnego **dostępu & tożsamości** w log Analytics można uzyskać, wybierając pozycję **tożsamość & dostęp (wersja zapoznawcza)** w ramach Security Center.

![Strona tożsamości — wycofanie środowiska klasycznego][9]

## <a name="security-events-map"></a>Mapa zdarzeń zabezpieczeń<a name="menu_securityeventsmap"></a>

Security Center udostępnia mapę alerty zabezpieczeń, która pomaga identyfikować zagrożenia bezpieczeństwa. Przycisk **Przejdź do mapy zdarzeń zabezpieczeń** w tym mapowaniu spowoduje otwarcie pulpitu nawigacyjnego, który umożliwia wyświetlanie pierwotnych zdarzeń zabezpieczeń w wybranym obszarze roboczym.

Usunęliśmy przycisk **Przejdź do mapy zdarzeń zabezpieczeń** i pulpit nawigacyjny dla poszczególnych obszarów roboczych.

![Mapa alertów zabezpieczeń — przycisk][10]

Po wybraniu przycisku **Przejdź do mapy zdarzeń zabezpieczeń** zostanie otwarty pulpit nawigacyjny analizy zagrożeń (teraz wycofany).

![Pulpit nawigacyjny analizy zagrożeń][11]

Po wybraniu obszaru roboczego, aby wyświetlić pulpit nawigacyjny analizy zagrożeń, należy otworzyć ekran mapy alertów zabezpieczeń (wersja zapoznawcza) na stronie Log Analytics.

![Mapa alertów zabezpieczeń w Log Analytics][12]

Istniejące dane pozostają dostępne w rozwiązaniu zabezpieczeń Log Analytics i nie zostały zmodyfikowane ani usunięte.

### <a name="security-events-map---the-new-experience"></a>Mapa zdarzeń zabezpieczeń — nowe środowisko

Zachęcamy do korzystania z funkcji mapy alertów wbudowanej w Security Center: **Mapa alertów zabezpieczeń (wersja zapoznawcza)**. Ta funkcja zapewnia zoptymalizowane środowisko i działa we wszystkich subskrypcjach i skojarzonych obszarach roboczych. Zapewnia on wysoki poziom widoku w środowisku i nie skupia się na jednym obszarze roboczym.

## <a name="custom-alert-rules-preview"></a>Niestandardowe reguły alertów (wersja zapoznawcza)<a name="menu_customalerts"></a>

[Wycofano środowisko alertów niestandardowych](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) 30 czerwca 2019, ponieważ jego podstawowa infrastruktura została wycofana. Po dacie wycofania nie są już generowane niestandardowe alerty zabezpieczeń.
Zalecamy włączenie [platformy Azure](https://azure.microsoft.com/services/azure-sentinel/) , a następnie ponowne utworzenie alertów niestandardowych. Możesz również utworzyć alerty za pomocą alertów dziennika Azure Monitor.

Aby utworzyć niestandardowe alerty za pomocą platformy Azure — wskaźnik:

1. [Otwórz wskaźnik platformy Azure](https://portal.azure.com/#create/Microsoft.ASI/preview) , a następnie wybierz obszar roboczy, w którym są przechowywane alerty niestandardowe
1. Wybierz pozycję **Analiza** z menu
1. Postępuj zgodnie z instrukcjami przedstawionymi w poniższym [samouczku](https://docs.microsoft.com/azure/sentinel/tutorial-detect-threats) dotyczącym tworzenia niestandardowych alertów na platformie Azure — wskaźnik

Jeśli nie interesuje Cię korzystanie z platformy Azure, możesz utworzyć alerty za pomocą alertów dziennika Azure Monitor. Aby uzyskać instrukcje, zobacz [Tworzenie i wyświetlanie alertów dzienników oraz zarządzanie nimi przy użyciu Azure monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log) i [rejestrowania alertów w Azure monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log).

![Alerty niestandardowe][13]

Aby uzyskać więcej informacji na temat wycofywania alertów niestandardowych, zobacz [niestandardowe reguły alertów w Azure Security Center (wersja zapoznawcza)](https://docs.microsoft.com/azure/security-center/security-center-custom-alert).

## <a name="security-alerts-investigation"></a>Badanie alertów zabezpieczeń<a name="menu_investigate"></a>

Funkcja badania w Security Center pomaga Klasyfikacja potencjalne zdarzenie związane z bezpieczeństwem. Funkcja umożliwia zrozumienie zakresu incydentu i śledzenie jego głównej przyczyny. Ta funkcja została usunięta z Security Center, ponieważ została zamieniona na udoskonalone środowisko [platformy Azure — wskaźnik](https://azure.microsoft.com/services/azure-sentinel/)wydajności.

![Zdarzenie naruszenia zabezpieczeń][14]

Po wybraniu przycisku **Zbadaj** na ekranie **incydentu zabezpieczeń** zostanie otwarty pulpit nawigacyjny badania (wersja zapoznawcza) w log Analytics. Wycofano pulpit nawigacyjny badania.

Istniejące dane pozostają dostępne w rozwiązaniu zabezpieczeń Log Analytics i nie zostały zmodyfikowane ani usunięte.

![Pulpit nawigacyjny badania w Log Analytics][15]

### <a name="investigation---the-new-experience"></a>Badanie — nowe środowisko

Zachęcamy do przejścia na [platformę Azure](https://azure.microsoft.com/services/azure-sentinel/) , aby uzyskać bogate środowisko badania. Platforma Azure — wskaźnik przedstawia zaawansowane narzędzia do wyszukiwania i zapytań, które umożliwiają wyszukiwanie zagrożeń związanych z bezpieczeństwem w źródłach danych w organizacji.

## <a name="subset-of-security-solutions"></a>Podzbiór rozwiązań zabezpieczeń<a name="menu_solutions"></a>

Security Center można włączyć [zintegrowane rozwiązania zabezpieczeń na platformie Azure](https://docs.microsoft.com/azure/security-center/security-center-partner-integration). Firma Microsoft wycofała następujące rozwiązania partnerskie od Security Center. Te rozwiązania są włączane na [platformie Azure wskaźnikowej](https://azure.microsoft.com/services/azure-sentinel/) wraz z szeregiem dodatkowych źródeł danych.

- [Rozwiązania zapory i zapory aplikacji sieci Web dla nowej generacji](https://docs.microsoft.com/azure/sentinel/connect-data-sources)
- [Integracja rozwiązań z zakresu zabezpieczeń, które obsługują format typowego zdarzenia (CEF)](https://docs.microsoft.com/azure/sentinel/connect-common-event-format)
- [Microsoft Advanced Threat Analytics](https://docs.microsoft.com/azure/sentinel/connect-azure-atp)
- [Usługa Azure AD Identity Protection](https://docs.microsoft.com/azure/sentinel/connect-azure-ad-identity-protection)

Po wycofaniu nie można dodawać ani modyfikować żadnych typów rozwiązań wymienionych na poprzedniej liście z poziomu interfejsu użytkownika lub interfejsu API. Azure Security Center nie będzie już wykrywał żadnych nowych wystąpień tych rozwiązań partnerskich.

Jeśli masz istniejące połączone rozwiązania, zachęcamy do przejścia na platformę Azure.

![Rozwiązania Security Center][16]

## <a name="edit-security-configurations-for-security-policies"></a>Edytuj konfiguracje zabezpieczeń dla zasad zabezpieczeń<a name="menu_securityconfigurations"></a>

Usługa Azure Security Center monitoruje konfiguracje zabezpieczeń, stosując zestaw [ponad 150 zalecanych reguł](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335) dotyczących wzmacniania ochrony systemu operacyjnego. Te reguły dotyczą zapór, inspekcji, zasad haseł i nie tylko. Jeśli okaże się, że maszyna ma konfigurację podatną na zagrożenia, usługa Security Center wygeneruje rekomendację dotyczącą zabezpieczeń. [Ekran edytowanie konfiguracji zabezpieczeń](https://docs.microsoft.com/azure/security-center/security-center-customize-os-security-config) umożliwia klientom dostosowanie domyślnej konfiguracji zabezpieczeń systemu operacyjnego w programie Security Center.

Ta funkcja w wersji zapoznawczej została wycofana. Aby resetować konfiguracje zabezpieczeń z powrotem do wartości domyślnych po dacie wycofania, zrób to za pośrednictwem interfejsu API lub programu PowerShell, wykonując [poniższe instrukcje](https://github.com/Azure/Azure-Security-Center/tree/master/Powershell%20scripts/Reset%20security%20configurations%20customization).

![Edytuj konfiguracje zabezpieczeń][17]

### <a name="edit-security-configurations---the-new-experience"></a>Edytowanie konfiguracji zabezpieczeń — nowe środowisko

Zamierzamy włączyć Security Center do obsługi [agenta konfiguracji gościa](https://docs.microsoft.com/azure/governance/policy/concepts/guest-configuration). Taka aktualizacja umożliwi znacznie bogatszy zestaw funkcji, w tym obsługę większej liczby systemów operacyjnych i integrację zasad systemu Azure w przypadku konfiguracji Gości. Po włączeniu tych zmian można także kontrolować konfiguracje w odpowiedniej skali i automatycznie stosować je do nowych zasobów.

## <a name="security-and-audit-dashboard-for-log-analytics-workspaces"></a>Pulpit nawigacyjny zabezpieczenia i inspekcja dla obszarów roboczych Log Analytics<a name="menu_securityomsdashboard"></a>

Pulpit nawigacyjny zabezpieczenia i inspekcja został pierwotnie użyty w portalu pakietu OMS. W Log Analytics pulpit nawigacyjny zawiera przegląd dla poszczególnych obszarów roboczych dotyczących istotnych zdarzeń zabezpieczeń i zagrożeń, mapę analizy zagrożeń oraz ocenę tożsamości i dostępu do zdarzeń zabezpieczeń zapisanych w obszarze roboczym. Ten pulpit nawigacyjny został usunięty. Ponieważ zalecamy już używanie interfejsu użytkownika pulpitu nawigacyjnego, zalecamy przejście do Azure Security Center.

![Pulpit nawigacyjny zabezpieczeń Log Analytics][18]

### <a name="security-and-audit-dashboard---the-new-experience"></a>Pulpit nawigacyjny zabezpieczenia i inspekcja — nowe środowisko

Firma Microsoft zaleca, aby przełączyć się do Azure Security Center. Zapewnia to ten sam Przegląd zabezpieczeń w wielu subskrypcjach oraz obszary robocze skojarzone z nimi oraz bogatszy zestaw funkcji.

Możesz uzyskać oryginalne zapytania Log Analytics, które wypełniają pulpit nawigacyjny zabezpieczenia i inspekcja w [repozytorium GitHub](https://github.com/Azure/Azure-Security-Center/tree/master/Legacy%20Log%20Analytics%20dashboards) dla Security Center.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [Azure Security Center](https://docs.microsoft.com/azure/security-center/).
- Dowiedz się więcej na temat [platformy Azure — wskaźnik](https://docs.microsoft.com/azure/sentinel).

<!--Image references - events-->
[1]: ./media/security-center-features-retirement-july2019/asc_events_dashboard.png
[2]: ./media/security-center-features-retirement-july2019/asc_events_dashboard_inner.png
[3]: ./media/security-center-features-retirement-july2019/workspace_saved_searches.png
<!--Image references - search-->
[4]: ./media/security-center-features-retirement-july2019/asc_search.png
[5]: ./media/security-center-features-retirement-july2019/workspace_logs.png
<!--Image references - classic identity and access-->
[6]: ./media/security-center-features-retirement-july2019/asc_identity.png
[7]: ./media/security-center-features-retirement-july2019/asc_identity_workspace_selection.png
[8]: ./media/security-center-features-retirement-july2019/loganalytics_dashboard_identity.png
[9]: ./media/security-center-features-retirement-july2019/asc_identity_nobuttonhighlight.png
<!--Image references - alerts map-->
[10]: ./media/security-center-features-retirement-july2019/asc_security_alerts_map.png
[11]: ./media/security-center-features-retirement-july2019/asc_threat_intellignece_dashboard.png
[12]: ./media/security-center-features-retirement-july2019/loganalytics_security_alerts_map.png
<!--Image references - custom alerts-->
[13]: ./media/security-center-features-retirement-july2019/asc_custom_alerts.png
<!--Image references - Investigation-->
[14]: ./media/security-center-features-retirement-july2019/asc-security-incident.png
[15]: ./media/security-center-features-retirement-july2019/loganalytics_investigation_dashboard.png
<!--Image references - Solutions-->
[16]: ./media/security-center-features-retirement-july2019/asc_security_solutions.png
<!--Image references - Edit security configurations-->
[17]: ./media/security-center-features-retirement-july2019/asc_edit_security_configurations.png
<!--Image references - Security dashboard in log analytics-->
[18]: ./media/security-center-features-retirement-july2019/loganalytics_security_dashboard.png
