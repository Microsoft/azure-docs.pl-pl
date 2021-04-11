---
title: Planowanie raportów & monitorowanie wdrożenia — Azure AD
description: Opisuje sposób planowania i wykonywania implementacji raportowania i monitorowania.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: baselden
ms.reviewer: plenzke
ms.collection: M365-identity-device-management
ms.openlocfilehash: c37c672f8784052424e058837d31bc1d22fcfc57
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2021
ms.locfileid: "106552618"
---
# <a name="plan-an-azure-active-directory-reporting-and-monitoring-deployment"></a>Planowanie wdrożenia Azure Active Directory raportowanie i monitorowanie

Rozwiązanie do raportowania i monitorowania Azure Active Directory (Azure AD) zależy od wymagań prawnych, bezpieczeństwa i działania oraz istniejącego środowiska i procesów. W tym artykule przedstawiono różne opcje projektowania i przeprowadzimy Cię do odpowiedniej strategii wdrażania.

### <a name="benefits-of-azure-ad-reporting-and-monitoring"></a>Zalety raportowania i monitorowania usługi Azure AD

Usługa Azure AD Reporting oferuje kompleksowy widok i dzienniki działania usługi Azure AD w Twoim środowisku, w tym zdarzenia logowania, zdarzenia inspekcji i zmiany w katalogu.

Na podstawie udostępnionych danych można:

* Określ, w jaki sposób są używane aplikacje i usługi.

* Wykrywaj potencjalne zagrożenia wpływające na kondycję środowiska.

* Rozwiązywanie problemów uniemożliwiających użytkownikom wykonywanie pracy.

* uzyskaj wgląd w szczegółowe dane, sprawdzając zdarzenia inspekcji zmian w katalogu usługi Azure AD.

> [!IMPORTANT]
> Usługa Azure AD monitoring umożliwia kierowanie dzienników generowanych przez usługę Azure AD Reporting do różnych systemów docelowych. Możesz następnie przechowywać je na potrzeby długoterminowego użytkowania lub zintegrować je z narzędziami do zarządzania informacjami i zdarzeniami zabezpieczeń (SIEM) innych firm, aby uzyskać wgląd w środowisko.

Usługa Azure AD monitoring umożliwia kierowanie dzienników do:

* konto usługi Azure Storage na potrzeby archiwizowania.
* Dzienniki Azure Monitor, znane wcześniej jako obszar roboczy usługi Azure Log Analytics, gdzie można analizować dane, tworzyć pulpity nawigacyjne i alerty dotyczące określonych zdarzeń.
* centrum zdarzeń platformy Azure, w którym można przeprowadzić integrację z istniejącymi narzędziami SIEM, takimi jak Splunk, SumoLogic lub QRadar.

> [!NOTE]
> Ostatnio rozpoczęto korzystanie z dzienników Azure Monitor, a nie Log Analytics. Dane dziennika nadal są przechowywane w obszarze roboczym Log Analytics i są nadal zbierane i analizowane przez tę samą usługę Log Analytics. Aktualizujemy terminologię, aby lepiej odzwierciedlić rolę [dzienników w Azure monitor](../../azure-monitor/data-platform.md). Aby uzyskać szczegółowe informacje, zobacz [Azure monitor terminologia](../../azure-monitor/terminology.md) .

[Dowiedz się więcej na temat zasad przechowywania raportów](./reference-reports-data-retention.md).

### <a name="licensing-and-prerequisites-for-azure-ad-reporting-and-monitoring"></a>Licencjonowanie i wymagania wstępne związane z raportowaniem i monitorowaniem usługi Azure AD

Potrzebujesz licencji usługi Azure AD Premium, aby uzyskać dostęp do dzienników logowania usługi Azure AD.

Aby uzyskać szczegółowe informacje dotyczące funkcji i licencjonowania w [przewodniku po cenach Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

Aby wdrożyć monitorowanie i raportowanie usługi Azure AD, będziesz potrzebować użytkownika, który jest administratorem globalnym lub administratorem zabezpieczeń dla dzierżawy usługi Azure AD.

W zależności od ostatecznego miejsca docelowego danych dziennika potrzebne są następujące elementy:

* Konto usługi Azure Storage, do którego masz uprawnienia ListKeys. Zalecamy używanie konta magazynu ogólnego, a nie konta magazynu obiektów blob. Informacje o cenach magazynu można znaleźć na stronie [kalkulatora cen usługi Azure Storage](https://azure.microsoft.com/pricing/calculator/?service=storage).

* Przestrzeń nazw Event Hubs platformy Azure do integracji z rozwiązaniami SIEM innych firm.

* Obszar roboczy usługi Azure Log Analytics do wysyłania dzienników do Azure Monitor dzienników.

## <a name="plan-an-azure-reporting-and-monitoring-deployment-project"></a>Planowanie projektu wdrożenia raportowania i monitorowania platformy Azure

W tym projekcie zdefiniujemy odbiorców, którzy będą korzystać z raportów i monitorować je oraz definiować architekturę monitorowania usługi Azure AD.

### <a name="engage-the-right-stakeholders"></a>Zaangażuj odpowiednich uczestników projektu

Gdy projekty technologii kończą się niepowodzeniem, zazwyczaj są to spowodowane niezgodnością oczekiwań, rezultatów i obowiązków. Aby uniknąć tych pułapek, [upewnij się, że interesują](../fundamentals/active-directory-deployment-plans.md)Cię odpowiednie osoby zainteresowane. Należy również upewnić się, że role udziałowców w projekcie są zrozumiałe poprzez dokumentowanie uczestników projektu i ich danych wejściowych i accountabilities.

### <a name="plan-communications"></a>Planowanie komunikacji

Komunikacja jest niezwykle ważna dla sukcesu każdej nowej usługi. Proaktywnie Komunikuj się z użytkownikami, w jaki sposób ich środowisko zostanie zmienione, gdy ulegnie zmianie, i jak uzyskać pomoc techniczną, jeśli wystąpią problemy.

### <a name="document-your-current-infrastructure-and-policies"></a>Udokumentowanie bieżącej infrastruktury i zasad

Twoja bieżąca infrastruktura i zasady będą mieć na celu tworzenie raportów i projektów monitorowania. Upewnij się, że wiesz

* Jakie narzędzia SIEM są używane.

* Infrastruktura platformy Azure, w tym istniejące konta magazynu i używane monitorowanie.

* Zasady przechowywania w organizacji dla dzienników, w tym wszelkie wymagane wymagania dotyczące struktur zgodności. 

## <a name="plan-an-azure-ad-reporting-and-monitoring-deployment"></a>Planowanie wdrażania i monitorowania usługi Azure AD

Raportowanie i monitorowanie są używane do zaspokajania wymagań firmy, uzyskiwania szczegółowych informacji o wzorcach użycia i zwiększania bezpieczeństwa stan organizacji.

### <a name="business-use-cases"></a>Przypadki użycia biznesowego

* Wymagane w celu spełnienia wymagań firmy
* Całkiem trzeba spełnić potrzeby biznesowe
* Nie dotyczy

|Obszar |Opis |
|-|-|
|Przechowywanie| **Przechowywanie dziennika przez ponad 30 dni**. Ze względu na wymagania prawne lub biznesowe wymagane jest przechowywanie dzienników inspekcji i logowanie dzienników usługi Azure AD dłużej niż 30 dni. |
|Analiza| **Dzienniki muszą być przeszukiwane**. Przechowywane dzienniki muszą być przeszukiwane przy użyciu narzędzi analitycznych. |
| Operational Insights| **Szczegółowe informacje dla różnych zespołów**. Konieczność udzielenia dostępu różnym użytkownikom w celu uzyskania wglądu w dane operacyjne, takich jak użycie aplikacji, błędy logowania, samoobsługowe użycie, trendy itp. |
| Szczegółowe informacje o zabezpieczeniach| **Szczegółowe informacje dla różnych zespołów**. Konieczność udzielenia dostępu różnym użytkownikom w celu uzyskania wglądu w dane operacyjne, takich jak użycie aplikacji, błędy logowania, użycie samoobsługowe, trendy itp. |
| Integracja w systemach SIEM      | **Integracja Siem**. Konieczność integracji i przesyłania strumieniowego dzienników logowania usługi Azure AD oraz rejestrowania inspekcji do istniejących systemów SIEM. |

### <a name="choose-a-monitoring-solution-architecture"></a>Wybierz architekturę rozwiązania do monitorowania

Usługa Azure AD monitoring umożliwia kierowanie dzienników aktywności usługi Azure AD do systemu, który najlepiej spełnia Twoje potrzeby biznesowe. Następnie można zachować je do długoterminowego raportowania i analizy, aby uzyskać wgląd w środowisko i zintegrować go z narzędziami SIEM.

#### <a name="decision-flow-chartan-image-showing-what-is-described-in-subsequent-sections"></a>Wykres przepływu decyzji![Obraz pokazujący, co zostało opisane w kolejnych sekcjach](media/reporting-deployment-plan/deploy-reporting-flow-diagram.png)

#### <a name="archive-logs-in-a-storage-account"></a>Archiwizowanie dzienników na koncie magazynu

Dzięki routingu dzienników do konta usługi Azure Storage można przechowywać je dłużej niż domyślny okres przechowywania przedstawiony w [zasadach przechowywania](./reference-reports-data-retention.md). Tej metody należy użyć, jeśli trzeba zarchiwizować dzienniki, ale nie musimy zintegrować ich z systemem SIEM i nie potrzebują bieżących zapytań i analizy. Można nadal wykonywać wyszukiwania na żądanie.

Dowiedz się, jak [kierować dane do konta magazynu](./quickstart-azure-monitor-route-logs-to-storage-account.md).

#### <a name="send-logs-to-azure-monitor-logs"></a>Wysyłanie dzienników do dzienników usługi Azure Monitor

[Dzienniki Azure monitor](../../azure-monitor/logs/log-query-overview.md) konsolidują dane monitorowania z różnych źródeł. Udostępnia także język zapytań i aparat analityczny, który zapewnia wgląd w działanie aplikacji i korzystanie z zasobów. Wysyłanie dzienników aktywności usługi Azure AD do dzienników Azure Monitor umożliwia szybkie pobieranie, monitorowanie i zgłaszanie alertów dotyczących zebranych danych. Użyj tej metody, jeśli nie masz istniejącego rozwiązania SIEM, do którego chcesz wysłać dane bezpośrednio, ale Zażądaj zapytań i analizy. Gdy dane znajdują się w Azure Monitor dziennikach, można wysłać je do centrum zdarzeń i z tego miejsca do SIEM, jeśli chcesz.

Dowiedz się, jak [wysyłać dane do dzienników usługi Azure Monitor](./howto-integrate-activity-logs-with-log-analytics.md).

Możesz również zainstalować wstępnie skompilowane widoki dzienników aktywności usługi Azure AD, aby monitorować typowe scenariusze związane z logowaniem i inspekcją zdarzeń.

Dowiedz się, jak [zainstalować widoki analizy dzienników dla dzienników aktywności usługi Azure AD i używać ich](./howto-install-use-log-analytics-views.md).

#### <a name="stream-logs-to-your-azure-event-hub"></a>Przesyłanie strumieniowe dzienników do centrum zdarzeń platformy Azure

Dzienniki routingu do centrum zdarzeń platformy Azure umożliwiają integrację z narzędziami SIEM innych firm. Ta integracja pozwala połączyć dane dzienników aktywności usługi Azure AD z innymi danymi zarządzanymi przez rozwiązania SIEM, co zapewnia lepszy wgląd w środowisko. 

Dowiedz się, jak [wysyłać strumieniowo dzienniki do centrum zdarzeń](./tutorial-azure-monitor-stream-logs-to-event-hub.md).

## <a name="plan-operations-and-security-for-azure-ad-reporting-and-monitoring"></a>Planowanie operacji i zabezpieczeń na potrzeby raportowania i monitorowania usługi Azure AD

Zainteresowane strony muszą uzyskiwać dostęp do dzienników usługi Azure AD w celu uzyskania wglądu w dane operacyjne. Możliwi Użytkownicy obejmują członków zespołu zabezpieczeń, wewnętrznych lub zewnętrznych audytorów oraz zespół operacji zarządzania tożsamościami i dostępem.

Role usługi Azure AD umożliwiają Delegowanie możliwości konfigurowania i wyświetlania raportów usługi Azure AD na podstawie Twojej roli. Określ, kto w organizacji musi mieć uprawnienia do odczytu raportów usługi Azure AD i jakie role byłyby dla nich odpowiednie. 

Następujące role mogą odczytywać raporty usługi Azure AD:

* Administrator globalny

* Administrator zabezpieczeń

* Czytelnik zabezpieczeń

* Czytelnik raportów

Dowiedz się więcej na temat [ról administracyjnych usługi Azure AD](../roles/permissions-reference.md).

*Zawsze stosuj koncepcję najniższych uprawnień, aby zmniejszyć ryzyko złamania zabezpieczeń konta*. Rozważ zaimplementowanie [Privileged Identity Management](../privileged-identity-management/pim-configure.md) , aby lepiej zabezpieczyć organizację.

##  

## <a name="deploy-azure-ad-reporting-and-monitoring"></a>Wdróż raportowanie i monitorowanie usługi Azure AD

W zależności od decyzji podjętych wcześniej przy użyciu powyższych wytycznych dotyczących projektowania ta sekcja przeprowadzi Cię do dokumentacji dotyczącej różnych opcji wdrażania.

### <a name="consume-and-archive-azure-ad-logs"></a>Korzystanie z dzienników usługi Azure AD i archiwizowanie ich

[Znajdowanie raportów dotyczących aktywności w witrynie Azure Portal](./howto-find-activity-reports.md)

[Archiwizowanie dzienników usługi Azure AD na koncie usługi Azure Storage](./quickstart-azure-monitor-route-logs-to-storage-account.md)

### <a name="implement-monitoring-and-analytics"></a>Implementowanie monitorowania i analizy

[Wyślij dzienniki do Azure Monitor](./howto-integrate-activity-logs-with-log-analytics.md)

[Instalowanie i Używanie widoków usługi log Analytics na potrzeby Azure Active Directory](./howto-install-use-log-analytics-views.md)

[Analizowanie dzienników aktywności usługi Azure AD za pomocą dzienników Azure Monitor](./howto-analyze-activity-logs-log-analytics.md)

* [Interpretuj schemat dzienników inspekcji w Azure Monitor](./reference-azure-monitor-audit-log-schema.md)

* [Interpretuj schemat logowania dzienników w Azure Monitor](./reference-azure-monitor-sign-ins-log-schema.md)

 * [Przesyłanie strumieniowe dzienników usługi Azure AD do centrum zdarzeń platformy Azure](./tutorial-azure-monitor-stream-logs-to-event-hub.md)

* [Integrate Azure AD logs with Splunk by using Azure Monitor (Integrowanie dzienników usługi Azure AD z narzędziem Splunk przy użyciu usługi Azure Monitor)](./howto-integrate-activity-logs-with-splunk.md)

* [Integrate Azure AD logs with SumoLogic by using Azure Monitor (Integrowanie dzienników usługi Azure AD z narzędziem SumoLogic przy użyciu usługi Azure Monitor)](./howto-integrate-activity-logs-with-sumologic.md)

 

 

## <a name="next-steps"></a>Następne kroki

Rozważ zaimplementowanie [Privileged Identity Management](../privileged-identity-management/pim-configure.md) 

Rozważ zaimplementowanie [kontroli dostępu opartej na rolach (Azure RBAC)](../../role-based-access-control/overview.md)
