---
title: Wdrażanie usługi Azure Monitor
description: W tym artykule opisano różne kroki wymagane do wykonania pełnej implementacji Azure Monitor do monitorowania wszystkich zasobów w ramach subskrypcji platformy Azure.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/27/2020
ms.openlocfilehash: f5855d7ab1f7ba8e11334f1373fb10166f47003a
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101708258"
---
# <a name="deploy-azure-monitor"></a>Wdrażanie usługi Azure Monitor
Włączenie Azure Monitor monitorowania wszystkich zasobów platformy Azure jest kombinacją konfigurowania składników Azure Monitor i konfigurowania zasobów platformy Azure w celu wygenerowania danych monitorowania dla Azure Monitor do zebrania. W tym artykule opisano różne kroki wymagane do wykonania pełnej implementacji Azure Monitor przy użyciu wspólnej konfiguracji do monitorowania wszystkich zasobów w ramach subskrypcji platformy Azure. Podstawowe opisy poszczególnych kroków są dostępne z linkami do innej dokumentacji dotyczącej szczegółowych wymagań dotyczących konfiguracji.

> [!IMPORTANT]
> Funkcje Azure Monitor i ich konfiguracja będą się różnić w zależności od wymagań firmy objętych kosztem włączonych funkcji. Każdy krok poniżej wskaże, czy jest to potencjalny koszt, i należy ocenić te koszty przed kontynuowaniem tego kroku. Zobacz [Cennik usługi Azure monitor](https://azure.microsoft.com/pricing/details/monitor/) , aby uzyskać szczegółowe informacje o cenach.

## <a name="configuration-goals"></a>Cele konfiguracji
Celem kompletnej implementacji Azure Monitor jest zebranie wszystkich dostępnych danych ze wszystkich zasobów i aplikacji w chmurze oraz umożliwienie jak największej liczby funkcji w Azure Monitor na podstawie tych danych.

Dane zbierane przez Azure Monitor są wysyłane do [Azure monitor metryk](essentials/data-platform-metrics.md) lub [dzienników Azure monitor](logs/data-platform-logs.md). Każdy z nich przechowuje różne rodzaje danych i włącza różne rodzaje analiz i alertów. Zapoznaj się z tematem [porównanie Azure monitor metryk i dzienników](data-platform.md) w celu porównania dwóch i [omówienia alertów w Microsoft Azure](alerts/alerts-overview.md) , aby uzyskać opis różnych typów alertów. 

Niektóre dane mogą być wysyłane do metryk i dzienników, aby można było korzystać z nich przy użyciu różnych funkcji. W takich przypadkach może być konieczne skonfigurowanie każdego z nich osobno. Na przykład dane metryk są automatycznie wysyłane przez zasoby platformy Azure do metryk, które obsługują Eksploratora metryk i alerty metryk. Należy utworzyć ustawienie diagnostyczne dla każdego zasobu, aby wysłać te same dane metryki do dzienników, co pozwala analizować trendy wydajności z innymi danymi dzienników przy użyciu Log Analytics. W poniższych sekcjach opisano, gdzie są wysyłane dane, oraz każdy krok wymagany do wysłania danych do wszystkich możliwych lokalizacji.

Mogą istnieć dodatkowe wymagania, takie jak monitorowanie zasobów poza platformą Azure i wysyłanie danych poza Azure Monitor. Wymagania takie jak te można osiągnąć przy użyciu dodatkowej konfiguracji funkcji opisanych w tym artykule. Aby uzyskać dodatkowe opcje konfiguracji, Skorzystaj z linków do dokumentacji w każdym kroku.

## <a name="collect-data-from-azure-resources"></a>Zbieranie danych z zasobów platformy Azure

> [!NOTE]
> Zobacz [monitorowanie zasobów platformy Azure za pomocą Azure monitor](essentials/monitor-azure-resource.md) , aby uzyskać kompletny przewodnik dotyczący monitorowania maszyn wirtualnych z Azure monitor.

Niektóre monitorowanie zasobów platformy Azure jest dostępne automatycznie bez konieczności konfigurowania, podczas gdy konieczne jest wykonanie kroków konfiguracyjnych w celu zebrania dodatkowych danych monitorowania. W poniższej tabeli przedstawiono kroki konfiguracji wymagane do zebrania wszystkich dostępnych danych z zasobów platformy Azure, w tym dane kroku, które są wysyłane do Azure Monitor metryki i dzienniki Azure Monitor. W poniższych sekcjach szczegółowo opisano każdy krok.

[![Wdrażanie monitorowania ](media/deploy/deploy-azure-resources.png) zasobów platformy Azure](media/deploy/deploy-azure-resources.png#lightbox)

### <a name="no-configuration"></a>Brak konfiguracji
Następujące funkcje Azure Monitor są włączone bez konieczności konfigurowania podczas tworzenia subskrypcji platformy Azure. Brak kosztów skojarzonych z tym monitorowaniem.

[Dzienniki Azure Active Directory](../active-directory/reports-monitoring/overview-reports.md) — zawiera historię na poziomie dzierżawy działania związanego z logowaniem i dziennika inspekcji zmian wprowadzonych w Azure Active Directory. Aby uzyskać szczegółowe informacje o dziennikach Azure Active Directory i sposobie wyświetlania ich w Azure Portal, zobacz [raporty dotyczące inspekcji w portalu Azure Active Directory](../active-directory/reports-monitoring/concept-audit-logs.md) i w [raportach aktywności logowania w portalu Azure Active Directory](../active-directory/reports-monitoring/concept-sign-ins.md) .

[Dziennik aktywności](essentials/platform-logs-overview.md) — zawiera szczegółowe informacje na temat grup zarządzania i zdarzeń na poziomie subskrypcji, które wystąpiły na platformie Azure. Zdarzenia są automatycznie zapisywane w dzienniku aktywności podczas tworzenia nowego zasobu platformy Azure, modyfikowania zasobu lub wykonywania znaczących działań. Po utworzeniu określonych zdarzeń można wyświetlić zdarzenia w Azure Portal i utworzyć alerty dziennika aktywności. Zobacz [Dziennik aktywności platformy Azure](essentials/activity-log.md) , aby uzyskać szczegółowe informacje o dzienniku aktywności i sposobie wyświetlania go w Azure Portal.

[Metryki platformy](essentials/metrics-supported.md) — zbierane automatycznie z usług platformy Azure do [metryk Azure monitor](essentials/data-platform-metrics.md). Te dane są często przedstawiane na stronie **Przegląd** w Azure Portal dla różnych usług. Zobacz Rozpoczynanie [pracy z usługą Azure Eksplorator metryk](essentials/metrics-getting-started.md) , aby uzyskać szczegółowe informacje na temat analizowania metryk platformy w Azure Portal. 


### <a name="create-log-analytics-workspace"></a>Tworzenie obszaru roboczego usługi Log Analytics
Do włączenia [dzienników Azure monitor](logs/data-platform-logs.md)wymagane jest co najmniej jeden obszar roboczy log Analytics, który jest wymagany do zbierania takich danych jak dzienniki z zasobów platformy Azure, zbieranie danych z systemu operacyjnego gościa maszyn wirtualnych platformy Azure oraz większość Azure monitor szczegółowych informacji. Inne usługi, takie jak Azure, i Azure Security Center korzystają również z Log Analytics obszaru roboczego i mogą współużytkować ten sam Azure Monitor. Aby obsłużyć to monitorowanie, możesz rozpocząć pracę z jednym obszarem roboczym, ale zapoznaj się z tematem  [projektowanie wdrożenia dzienników Azure monitor](logs/design-logs-deployment.md) , aby uzyskać wskazówki dotyczące używania wielu obszarów roboczych.

Nie ma kosztów tworzenia obszaru roboczego Log Analytics, ale istnieje potencjalna opłata za skonfigurowanie danych do zebrania. Aby uzyskać szczegółowe informacje [, zobacz Zarządzanie użyciem i kosztami za pomocą dzienników Azure monitor](logs/manage-cost-storage.md) .  

Aby utworzyć początkowy obszar roboczy Log Analytics [, zobacz Tworzenie obszaru roboczego log Analytics w Azure Portal](logs/quick-create-workspace.md) . Aby skonfigurować dostęp [, zobacz Zarządzanie dostępem do danych dziennika i obszarów roboczych w Azure monitor](logs/manage-access.md) . 

### <a name="create-diagnostic-setting-to-collect-tenant-and-subscription-logs"></a>Tworzenie ustawień diagnostycznych w celu zbierania dzienników dzierżawy i subskrypcji
Podczas gdy [dzienniki Azure Active Directory](../active-directory/reports-monitoring/overview-reports.md) dla Twojej dzierżawy i [Dziennik aktywności](essentials/platform-logs-overview.md) dla subskrypcji są zbierane automatycznie, wysłanie ich do log Analytics obszaru roboczego umożliwia przeanalizowanie tych zdarzeń z innymi danymi dzienników przy użyciu zapytań dzienników w log Analytics. Pozwala to również na tworzenie alertów kwerendy dziennika, które są jedynym sposobem na wygenerowanie alertów dotyczących dzienników Azure Active Directory i dostarczenie bardziej złożonej logiki niż alerty dzienników aktywności.

Nie ma kosztu wysłania dziennika aktywności do obszaru roboczego, ale istnieje opłata za pozyskiwanie i przechowywanie danych Azure Active Directory dzienników. 

Zobacz [integrowanie dzienników usługi Azure AD z dziennikami Azure monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) i [Tworzenie ustawień diagnostycznych w celu wysyłania dzienników platformy i metryk do różnych miejsc docelowych](essentials/diagnostic-settings.md) w celu utworzenia ustawień diagnostycznych dla dzierżawy i subskrypcji w celu wysyłania wpisów dziennika do obszaru roboczego log Analytics. 

### <a name="create-diagnostic-setting-to-collect-resource-logs-and-platform-metrics"></a>Tworzenie ustawień diagnostycznych w celu zbierania dzienników zasobów i metryk platformy
Zasoby na platformie Azure automatycznie generują [dzienniki zasobów](essentials/platform-logs-overview.md) , które zawierają szczegółowe informacje o operacjach wykonywanych w ramach zasobu. W przeciwieństwie do metryk platformy chociaż należy skonfigurować dzienniki zasobów do zebrania. Utwórz ustawienie diagnostyczne, aby wysłać je do obszaru roboczego Log Analytics, aby połączyć je z innymi danymi używanymi z dziennikami Azure Monitor. To samo ustawienie diagnostyczne może służyć do wysyłania metryk platformy dla większości zasobów do tego samego obszaru roboczego, co umożliwia analizowanie danych metryk przy użyciu zapytań dzienników z innymi zebranymi danymi.

Ta kolekcja jest kosztem, dlatego należy zapoznać się z [cennikiem Azure monitor](https://azure.microsoft.com/pricing/details/monitor/) przed wdrożeniem dużej liczby zasobów. Zobacz też temat [Zarządzanie użyciem i kosztami za pomocą dzienników Azure monitor,](logs/manage-cost-storage.md) Aby uzyskać szczegółowe informacje na temat optymalizowania kosztów zbierania dzienników.

Zobacz [Tworzenie ustawień diagnostycznych, aby zbierać dzienniki zasobów i metryki na platformie Azure w](essentials/diagnostic-settings.md#create-in-azure-portal) celu utworzenia ustawienia diagnostycznego dla zasobu platformy Azure. Ponieważ należy utworzyć ustawienie diagnostyczne dla każdego zasobu platformy Azure, zobacz temat [wdrażanie Azure monitor w odpowiedniej skali](deploy-scale.md) , aby uzyskać szczegółowe informacje na temat używania [usługi Azure Policy](../governance/policy/overview.md) do automatycznego tworzenia ustawień przy każdym utworzeniu zasobu platformy Azure.

### <a name="enable-insights-and-solutions"></a>Włączanie szczegółowych informacji i rozwiązań
Szczegółowe informacje i rozwiązania zapewniają wyspecjalizowane monitorowanie dla konkretnej usługi lub rozwiązania. Usługa Insights korzysta z najnowszych funkcji Azure Monitor, takich jak skoroszyty, dlatego należy używać szczegółowych informacji, jeśli są one dostępne dla usługi. Są one automatycznie dostępne w każdej subskrypcji platformy Azure, ale mogą wymagać pewnej konfiguracji w celu zapewnienia pełnej funkcjonalności. Zwykle korzystają z dzienników metryk i zasobów platformy, które zostały wcześniej skonfigurowane i mogą zbierać dodatkowe dane.

Rozwiązania należy dodać do każdej subskrypcji i korzystać wyłącznie z danych z dzienników Azure Monitor i mogą zbierać dodatkowe dane dziennika.

Nie ma kosztu dla rozwiązań i szczegółowych informacji, ale opłaty mogą być naliczane za zebrane dane.

Zobacz, [co jest monitorowane przez Azure monitor?](monitor-reference.md) , aby uzyskać listę dostępnych szczegółowych informacji i rozwiązań w Azure monitor. Zapoznaj się z dokumentacją dla każdej unikatowej konfiguracji lub informacji o cenach. 

## <a name="collect-data-from-virtual-machines"></a>Zbieranie danych z maszyn wirtualnych

> [!NOTE]
> Zobacz [monitorowanie maszyn wirtualnych platformy Azure z Azure monitor](vm/monitor-vm-azure.md) , aby uzyskać kompletny przewodnik dotyczący monitorowania maszyn wirtualnych z Azure monitor. 

Maszyny wirtualne generują podobne dane jak inne zasoby platformy Azure, ale potrzebujesz agenta do zbierania danych z systemu operacyjnego gościa. Zapoznaj się [z omówieniem Azure Monitor agentów](agents/agents-overview.md) , aby porównać agentów używanych przez Azure monitor. 

Usługi [VM Insights](vm/vminsights-overview.md) używają agenta log Analytics i agenta zależności do zbierania danych z systemu operacyjnego gościa maszyn wirtualnych, dzięki czemu można wdrożyć tych agentów w ramach implementacji tego wglądu. Dzięki temu Agent Log Analytics może korzystać z innych usług, takich jak Azure Security Center.


[![Wdróż maszynę wirtualną ](media/deploy/deploy-azure-vm.png) platformy Azure](media/deploy/deploy-azure-vm.png#lightbox)


### <a name="configure-workspace-for-vm-insights"></a>Konfigurowanie obszaru roboczego dla szczegółowych informacji o maszynie wirtualnej
Usługa VM Insights wymaga Log Analyticsego obszaru roboczego, który jest zwykle taki sam jak utworzony w celu zbierania danych z innych zasobów platformy Azure. Przed włączeniem jakichkolwiek maszyn wirtualnych należy dodać rozwiązanie wymagane dla usługi VM Insights do obszaru roboczego.

Aby uzyskać szczegółowe informacje na temat konfigurowania obszaru roboczego Log Analytics na potrzeby usługi VM Insights, zobacz [Konfigurowanie obszaru roboczego log Analytics dla usługi VM Insights](vm/vminsights-configure-workspace.md) .

### <a name="enable-vm-insights-on-each-virtual-machine"></a>Włącz szczegółowe informacje o MASZYNach wirtualnych na każdej maszynie wirtualnej
Po skonfigurowaniu obszaru roboczego można włączyć każdą maszynę wirtualną, instalując agenta Log Analytics i agenta zależności. Istnieje wiele metod instalacji tych agentów, w tym Azure Policy, które umożliwiają automatyczne skonfigurowanie każdej maszyny wirtualnej w miarę jej tworzenia. Dane dotyczące wydajności i informacje o procesie zbierane przez usługi VM Insights są przechowywane w dziennikach Azure Monitor.

Zobacz temat [Włączanie usługi VM Insights — Omówienie](vm/vminsights-enable-overview.md) opcji wdrażania agentów na maszynach wirtualnych i włączania ich do monitorowania.

### <a name="configure-workspace-to-collect-events"></a>Konfigurowanie obszaru roboczego do zbierania zdarzeń
Usługi VM Insights będą zbierać dane dotyczące wydajności oraz szczegóły i zależności procesów z systemu operacyjnego gościa każdej maszyny wirtualnej. Agent Log Analytics może również zbierać dzienniki z gościa, w tym dziennik zdarzeń z systemu Windows i dziennika systemowego z systemem Linux. Pobiera konfigurację dla tych dzienników z obszaru roboczego Log Analytics, do którego jest podłączona. Wystarczy skonfigurować obszar roboczy tylko raz i za każdym razem, gdy Agent nawiąże połączenie, pobierze wszelkie zmiany konfiguracji. 

Zobacz [źródła danych agentów w Azure monitor](agents/agent-data-sources.md) , aby uzyskać szczegółowe informacje na temat konfigurowania obszaru roboczego log Analytics w celu zbierania dodatkowych danych z maszyn wirtualnych agenta.

> [!NOTE]
> Istnieje również możliwość skonfigurowania obszaru roboczego do zbierania liczników wydajności, ale najprawdopodobniej będzie to możliwe za pomocą danych wydajności zbieranych przez usługi VM Insights. Dane wydajności zbierane przez obszar roboczy będą przechowywane w tabeli *wydajności* , podczas gdy dane wydajności zbierane przez usługi VM Insights są przechowywane w tabeli *InsightsMetrics* . Skonfiguruj zbieranie danych o wydajności w obszarze roboczym tylko wtedy, gdy wymagane są liczniki, które nie zostały jeszcze zebrane przez usługi VM Insights.

### <a name="diagnostic-extension-and-telegraf-agent"></a>Rozszerzenie diagnostyczne i Agent telegraf
W usłudze VM Insights jest używany Agent Log Analytics, który wysyła dane wydajności do Log Analytics obszaru roboczego, ale nie do Azure Monitor metryk. Wysłanie tych danych do metryk umożliwia przeanalizowanie ich przy użyciu Eksplorator metryk i użycie z alertami metryk. Wymaga to rozszerzenia diagnostycznego w systemie Windows i agencie telegraf w systemie Linux.

Zobacz [Instalowanie i Konfigurowanie rozszerzenia diagnostyki systemu Windows Azure (funkcji wad)](agents/diagnostics-extension-windows-install.md) oraz [zbieranie niestandardowych metryk dla maszyny wirtualnej z systemem Linux za pomocą agenta InfluxData telegraf,](essentials/collect-custom-metrics-linux-telegraf.md) Aby uzyskać szczegółowe informacje na temat instalowania i konfigurowania tych agentów.


## <a name="monitor-applications"></a>Monitorowanie aplikacji
Azure Monitor monitoruje aplikacje niestandardowe przy użyciu [Application Insights](app/app-insights-overview.md), które należy skonfigurować dla każdej aplikacji, która ma być monitorowana. Proces konfiguracji będzie się różnić w zależności od typu monitorowanej aplikacji i typu monitorowania, które chcesz wykonać. Dane zbierane przez Application Insights są przechowywane w Azure Monitor metrykach, dziennikach Azure Monitor i magazynie obiektów blob platformy Azure, w zależności od funkcji. Dane wydajności są przechowywane zarówno w Azure Monitor metryki, jak i w dziennikach Azure Monitor bez konieczności wykonywania dodatkowych czynności konfiguracyjnych.

### <a name="create-an-application-resource"></a>Tworzenie zasobu aplikacji
Należy utworzyć zasób w Application Insights dla każdej aplikacji, która ma być monitorowana. Dane dziennika zbierane przez Application Insights są przechowywane w dziennikach Azure Monitor dla aplikacji opartej na obszarze roboczym. Dane dziennika dla klasycznych aplikacji są przechowywane niezależnie od obszaru roboczego Log Analytics, zgodnie z opisem w temacie [Struktura danych](logs/data-platform-logs.md#data-structure).

 Podczas tworzenia aplikacji należy wybrać, czy używać klasycznego, czy opartego na obszarze roboczym. Zobacz [Tworzenie zasobu Application Insights](app/create-new-resource.md) , aby utworzyć klasyczną aplikację. Zapoznaj się z [zasobami Application Insights opartymi na obszarze roboczym (wersja zapoznawcza)](app/create-workspace-resource.md) w celu utworzenia aplikacji na podstawie obszaru roboczego.

### <a name="configure-codeless-or-code-based-monitoring"></a>Konfigurowanie monitorowania na podstawie kodu lub kodu
Aby włączyć monitorowanie dla aplikacji, należy zdecydować, czy będzie używane monitorowanie oparte na kodzie, czy też na podstawie kodu. Proces konfiguracji będzie się różnić w zależności od tej decyzji i typu aplikacji, która ma być monitorowana.

**Monitorowanie bezkodowe** jest najłatwiej zaimplementowane i można je skonfigurować po zakończeniu tworzenia kodu. Nie wymaga żadnych aktualizacji kodu. Zapoznaj się z poniższymi zasobami, aby uzyskać szczegółowe informacje na temat włączania monitorowania w zależności od aplikacji.

- [Aplikacje hostowane w usłudze Azure Web Apps](app/azure-web-apps.md)
- [Aplikacje Java](app/java-in-process-agent.md)
- [ASP.NET aplikacje hostowane w usługach IIS na maszynie wirtualnej platformy Azure lub w zestawie skalowania maszyn wirtualnych platformy Azure](app/azure-vm-vmss-apps.md)
- [ASP.NET aplikacje hostowane w lokalnych maszynach wirtualnych usług IIS](app/monitor-performance-live-website-now.md)


**Monitorowanie oparte na kodzie** jest bardziej dostosowywalne i gromadzi dodatkową telemetrię, ale wymaga dodania zależności do kodu w pakietach NuGet zestawu Application Insights SDK. Zapoznaj się z poniższymi zasobami, aby uzyskać szczegółowe informacje na temat włączania monitorowania w zależności od aplikacji.

- [Aplikacje ASP.NET](app/asp-net.md)
- [ASP.NET Core aplikacji](app/asp-net-core.md)
- [Aplikacje konsolowe platformy .NET](app/console.md)
- [Java](app/java-get-started.md)
- [Node.js](app/nodejs.md)
- [Python](app/opencensus-python.md)
- [Inne platformy](app/platforms.md)

### <a name="configure-availability-testing"></a>Konfigurowanie testowania dostępności
Testy dostępności w Application Insights to testy cykliczne, które monitorują dostępność i czas odpowiedzi aplikacji w regularnych odstępach czasu na całym świecie. Możesz utworzyć prosty test polecenia ping bezpłatnie lub utworzyć sekwencję żądań sieci Web, aby symulować transakcje użytkowników, które mają skojarzony koszt. 

Zobacz [monitorowanie dostępności dowolnej witryny sieci Web](app/monitor-web-app-availability.md) w celu podsumowania różnych rodzajów testów i szczegółów dotyczących ich tworzenia.

### <a name="configure-profiler"></a>Konfigurowanie profilera
Program Profiler w Application Insights udostępnia dane śledzenia wydajności aplikacji .NET. Ułatwia on zidentyfikowanie ścieżki kodu "gorąca", która zajmuje najdłuższy czas, gdy obsługuje określone żądanie sieci Web. Proces konfigurowania profilera różni się w zależności od typu aplikacji. 

Zobacz [Profilowanie aplikacji produkcyjnych na platformie Azure z Application Insights](app/profiler-overview.md) , aby uzyskać szczegółowe informacje na temat konfigurowania profilera.

### <a name="configure-snapshot-debugger"></a>Konfigurowanie Snapshot Debugger
Snapshot Debugger w Application Insights monitoruje dane telemetryczne wyjątków z aplikacji .NET i zbiera migawki dotyczące najważniejszych, zgłaszanych wyjątków, aby uzyskać informacje potrzebne do diagnozowania problemów w środowisku produkcyjnym. Proces konfigurowania Snapshot Debugger różni się w zależności od typu aplikacji. 

Aby uzyskać szczegółowe informacje na temat konfigurowania Snapshot Debugger, zobacz [debugowanie migawek na wyjątkach w aplikacjach .NET](app/snapshot-debugger.md) .


## <a name="visualize-data"></a>Wizualizowanie danych
Szczegółowe informacje i rozwiązania będą obejmować własne skoroszyty i widoki umożliwiające analizowanie ich danych. Oprócz tych można utworzyć własne [wizualizacje](visualizations.md) , w tym skoroszyty dla danych Azure monitor i pulpitów nawigacyjnych, aby połączyć dane Azure monitor z danymi z innych usług na platformie Azure.


### <a name="create-workbooks"></a>Tworzenie skoroszytów
[Skoroszyty](visualize/workbooks-overview.md) w Azure Monitor umożliwiają tworzenie rozbudowanych raportów wizualnych w Azure Portal. Można połączyć różne zestawy danych z Azure Monitor metryk i dzienników Azure Monitor, aby utworzyć ujednolicone środowiska interaktywne. Możesz uzyskać dostęp do galerii skoroszytów na karcie **skoroszyty** menu Azure monitor. 

Aby uzyskać szczegółowe informacje na temat tworzenia skoroszytów niestandardowych, zobacz [Azure monitor skoroszytów](visualize/workbooks-overview.md) .

### <a name="create-dashboards"></a>Tworzenie pulpitów nawigacyjnych
[Pulpity nawigacyjne platformy Azure](../azure-portal/azure-portal-dashboards.md) są podstawową technologią nawigacyjną dla systemu Azure i umożliwiają łączenie Azure monitor danych z danymi z innych usług, aby zapewnić pojedyncze szyby w infrastrukturze platformy Azure. Aby uzyskać szczegółowe informacje na temat tworzenia pulpitu nawigacyjnego zawierającego dane z dzienników Azure Monitor, zobacz [Tworzenie i udostępnianie pulpitów nawigacyjnych danych log Analytics](visualize/tutorial-logs-dashboards.md) . 

Zobacz [Tworzenie niestandardowych pulpitów nawigacyjnych wskaźników KPI przy użyciu usługi Azure Application Insights](app/tutorial-app-dashboards.md) , aby uzyskać szczegółowe informacje na temat tworzenia pulpitu nawigacyjnego zawierającego dane z Application Insights. 

## <a name="alerts"></a>Alerty
Alerty w usłudze Azure Monitor aktywnie powiadamiać użytkownika o ważnych danych lub wzorcach zidentyfikowanych w danych monitorowania. Niektóre szczegółowe dane będą generować alerty bez konfiguracji. W przypadku innych scenariuszy należy utworzyć [reguły alertów](alerts/alerts-overview.md) , które obejmują dane do analizy, oraz kryteria dla momentu wygenerowania alertu, a także grupy akcji, które definiują akcję podejmowaną po wygenerowaniu alertu. 


### <a name="create-action-groups"></a>Tworzenie grup akcji
[Grupy akcji](alerts/action-groups.md) to zbiór preferencji powiadomień używanych przez reguły alertów w celu określenia akcji do wykonania w przypadku wyzwolenia alertu. Przykłady akcji obejmują wysyłanie wiadomości e-mail lub tekstu, wywoływanie elementu webhook lub wysyłanie danych do narzędzia Narzędzia ITSM. Każda reguła alertu wymaga co najmniej jednej grupy akcji, a jedna z nich może być używana przez wiele reguł alertów.

Zobacz [Tworzenie grup akcji i zarządzanie nimi w Azure Portal,](alerts/action-groups.md) Aby uzyskać szczegółowe informacje na temat tworzenia grupy akcji oraz opis różnych akcji, które może uwzględnić.


### <a name="create-alert-rules"></a>Tworzenie reguł alertów
Istnieje wiele typów reguł alertów zdefiniowanych przez typ danych, których używają. Każdy z nich ma inne możliwości i inny koszt. Podstawową strategią, którą należy wykonać, jest użycie typu reguły alertu z najniższym kosztem, który zapewnia wymaganą logikę.

- [Reguły dziennika aktywności](alerts/activity-log-alerts.md). Tworzy alert w odpowiedzi na nowe zdarzenie dziennika aktywności pasujące do określonych warunków. Te alerty nie są kosztami, więc powinny być wybrane jako pierwsze. Aby uzyskać szczegółowe informacje na temat tworzenia alertu dziennika aktywności, zobacz temat [Tworzenie i wyświetlanie alertów dziennika aktywności oraz zarządzanie nimi za pomocą Azure monitor](alerts/alerts-activity-log.md) .
- [Reguły alertów dotyczących metryk](alerts/alerts-metric-overview.md). Tworzy alert w odpowiedzi na co najmniej jedną wartość metryki przekroczenia wartości progowej. Alerty metryk są stanowe oznacza, że alert zostanie automatycznie zamknięty, gdy wartość spadnie poniżej wartości progowej i będzie wysyłać powiadomienia tylko wtedy, gdy stan zmieni się. Istnieją koszty związane z alertami, które są znacznie mniejsze niż alerty dzienników. Aby uzyskać szczegółowe informacje na temat tworzenia alertu dotyczącego metryki [, zobacz Tworzenie i wyświetlanie alertów metryk oraz zarządzanie nimi za pomocą Azure monitor](alerts/alerts-metric.md) .
- [Reguły alertów dzienników](alerts/alerts-unified-log.md). Tworzy alert, gdy wyniki zapytania dotyczącego harmonogramu są zgodne z określonymi kryteriami. Są one najtańsze regułami alertów, ale zezwalają na najbardziej złożone kryteria. Aby uzyskać szczegółowe informacje na temat tworzenia alertu zapytania dziennika [, zobacz Tworzenie, wyświetlanie i zarządzanie alertami dzienników przy użyciu Azure monitor](alerts/alerts-log.md) .
- [Alerty aplikacji](app/monitor-web-app-availability.md) umożliwiają przeprowadzanie aktywnych testów wydajności i dostępności aplikacji sieci Web. Możesz wykonywać proste testy ping bez ponoszenia kosztów, ale istnieje koszt na bardziej złożone testy. Zobacz [monitorowanie dostępności dowolnej witryny sieci Web](app/monitor-web-app-availability.md) , aby zapoznać się z opisem różnych testów i szczegółowych informacji dotyczących ich tworzenia.


## <a name="next-steps"></a>Następne kroki

- Zobacz [wdrażanie Azure monitor w skali przy użyciu Azure Policy](deploy-scale.md).