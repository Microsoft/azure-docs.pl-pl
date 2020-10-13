---
title: Źródła danych w Azure Monitor | Microsoft Docs
description: Opisuje dane dostępne do monitorowania kondycji i wydajności zasobów platformy Azure oraz działających na nich aplikacji.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/06/2020
ms.openlocfilehash: 8ef498a51f25a6b084a0d048661f3d18a5881644
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91802071"
---
# <a name="sources-of-monitoring-data-for-azure-monitor"></a>Źródła danych monitorowania dla Azure Monitor
Azure Monitor jest oparta na [wspólnej platformie danych monitorowania](data-platform.md) , która obejmuje [dzienniki](data-platform-logs.md) i [metryki](data-platform-metrics.md). Zbieranie danych do tej platformy pozwala analizować dane z wielu zasobów wspólnie przy użyciu wspólnego zestawu narzędzi w Azure Monitor. Dane monitorowania mogą być również wysyłane do innych lokalizacji w celu obsługi niektórych scenariuszy, a niektóre zasoby mogą zapisywać w innych lokalizacjach, zanim będą mogły być zbierane w dziennikach lub metrykach.

W tym artykule opisano różne źródła danych monitorowania zbieranych przez Azure Monitor oprócz danych monitorowania utworzonych przez zasoby platformy Azure. Linki są udostępniane ze szczegółowymi informacjami na temat konfiguracji wymaganej do zebrania tych danych w różnych lokalizacjach.

## <a name="application-tiers"></a>Warstwy aplikacji

Źródła danych monitorowania z aplikacji platformy Azure można organizować w warstwy, a najwyższe warstwy są używane przez samą aplikację, a niższe warstwy są składnikiem platformy platformy Azure. Metoda uzyskiwania dostępu do danych z poszczególnych warstw jest różna. W poniższej tabeli zestawiono warstwy aplikacji, a źródła danych monitorowania w poszczególnych warstwach zostały przedstawione w poniższych sekcjach. Zobacz sekcję [monitorowanie lokalizacji danych na platformie Azure](data-locations.md) , aby uzyskać opis każdej lokalizacji danych i uzyskać dostęp do jej danych.


![Warstwy monitorowania](../media/overview/overview.png)


### <a name="azure"></a>Azure
W poniższej tabeli krótko opisano warstwy aplikacji, które są specyficzne dla platformy Azure. Poniżej znajduje się łącze do dalszych szczegółowych informacji na temat każdego z poniższych sekcji.

| Warstwa | Opis | Metoda kolekcjonowania |
|:---|:---|:---|
| [Dzierżawa platformy Azure](#azure-tenant) | Dane dotyczące działania usług na poziomie dzierżawy platformy Azure, takich jak Azure Active Directory. | Wyświetlanie danych usługi AAD w portalu lub Konfigurowanie kolekcji w celu Azure Monitor przy użyciu ustawienia diagnostyki dzierżawcy. |
| [Subskrypcja platformy Azure](#azure-subscription) | Dane związane z kondycją i zarządzaniem usługami wielu zasobów w ramach subskrypcji platformy Azure, takich jak Menedżer zasobów i Service Health. | Wyświetl w portalu lub skonfiguruj kolekcję do Azure Monitor przy użyciu profilu dziennika. |
| [Zasoby platformy Azure](#azure-resources) |  Dane dotyczące operacji i wydajności poszczególnych zasobów platformy Azure. | Zebrane metryki są automatycznie wyświetlane w Eksplorator metryk.<br>Skonfiguruj ustawienia diagnostyczne w celu zbierania dzienników w Azure Monitor.<br>Rozwiązania do monitorowania i szczegółowe informacje umożliwiające monitorowanie określonych typów zasobów. |

### <a name="azure-other-cloud-or-on-premises"></a>Platforma Azure, inna chmura lub lokalna 
W poniższej tabeli krótko opisano warstwy aplikacji, które mogą znajdować się na platformie Azure, w innej chmurze lub lokalnie. Poniżej znajduje się łącze do dalszych szczegółowych informacji na temat każdego z poniższych sekcji.

| Warstwa | Opis | Metoda kolekcjonowania |
|:---|:---|:---|
| [System operacyjny (gościa)](#operating-system-guest) | Dane dotyczące systemu operacyjnego w zasobach obliczeniowych. | Zainstaluj agenta Log Analytics, aby zebrać źródła danych klienta do Azure Monitor i agenta zależności w celu zebrania zależności obsługujących Azure Monitor dla maszyn wirtualnych.<br>W przypadku maszyn wirtualnych platformy Azure Zainstaluj rozszerzenie Diagnostyka platformy Azure, aby zebrać dzienniki i metryki do Azure Monitor. |
| [Kod aplikacji](#application-code) | Dane dotyczące wydajności i funkcjonalności rzeczywistej aplikacji i kodu, w tym śledzenia wydajności, dzienników aplikacji i danych telemetrycznych użytkownika. | Instrumentacja kodu do zbierania danych w Application Insights. |
| [Źródła niestandardowe](#custom-sources) | Dane z usług zewnętrznych lub innych składników lub urządzeń. | Zbieraj dane dziennika lub metryki do Azure Monitor z dowolnego klienta REST. |

## <a name="azure-tenant"></a>Dzierżawa platformy Azure
Dane telemetryczne związane z dzierżawą platformy Azure są zbierane z usług obejmujących dzierżawę, takich jak Azure Active Directory.

![Kolekcja dzierżawców platformy Azure](media/data-sources/tenant.png)

### <a name="azure-active-directory-audit-logs"></a>Azure Active Directory dzienników inspekcji
[Raportowanie Azure Active Directory](../../active-directory/reports-monitoring/overview-reports.md) zawiera historię działań związanych z logowaniem i rejestrowaniem śladu zmian wprowadzonych w ramach określonej dzierżawy. 

| Element docelowy | Opis | Tematy pomocy |
|:---|:---|:---|
| Dzienniki usługi Azure Monitor | Skonfiguruj dzienniki usługi Azure AD, które mają być zbierane w Azure Monitor, aby przeanalizować je z innymi danymi monitorowania. | [Integrowanie dzienników usługi Azure AD z dziennikami Azure Monitor (wersja zapoznawcza)](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) |
| Azure Storage | Eksportowanie dzienników usługi Azure AD do usługi Azure Storage w celu archiwizacji. | [Samouczek: archiwizowanie dzienników usługi Azure AD na koncie usługi Azure Storage (wersja zapoznawcza)](../../active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md) |
| Centrum zdarzeń | Przesyłanie strumieniowe dzienników usługi Azure AD do innych lokalizacji przy użyciu Event Hubs. | [Samouczek: przesyłanie strumieniowe dzienników Azure Active Directory do centrum zdarzeń platformy Azure (wersja zapoznawcza)](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md). |



## <a name="azure-subscription"></a>Subskrypcja platformy Azure
Dane telemetryczne związane z kondycją i działaniem subskrypcji platformy Azure.

![Subskrypcja platformy Azure](media/data-sources/azure-subscription.png)

### <a name="azure-activity-log"></a>Dziennik aktywności platformy Azure 
[Dziennik aktywności platformy Azure](platform-logs-overview.md) zawiera rekordy kondycji usługi wraz z rekordami dotyczącymi wszelkich zmian konfiguracji wprowadzonych w zasobach w ramach subskrypcji platformy Azure. Dziennik aktywności jest dostępny dla wszystkich zasobów platformy Azure i reprezentuje widok _zewnętrzny_ .

| Element docelowy | Opis | Tematy pomocy |
|:---|:---|
| Dziennik aktywności | Dziennik aktywności jest zbierany do własnego magazynu danych, który można wyświetlić z menu Azure Monitor lub użyć do tworzenia alertów dziennika aktywności. | [Wykonaj zapytanie dotyczące dziennika aktywności w Azure Portal](./activity-log.md#view-the-activity-log) |
| Dzienniki usługi Azure Monitor | Skonfiguruj dzienniki Azure Monitor w celu zebrania dziennika aktywności w celu przeanalizowania go przy użyciu innych danych monitorowania. | [Zbieranie i analizowanie dzienników aktywności platformy Azure w obszarze roboczym Log Analytics w Azure Monitor](./activity-log.md) |
| Azure Storage | Wyeksportuj dziennik aktywności do usługi Azure Storage w celu archiwizacji. | [Archiwizowanie dziennika aktywności](./resource-logs.md#send-to-azure-storage)  |
| Event Hubs | Strumieniowe przesyłanie dziennika aktywności do innych lokalizacji przy użyciu Event Hubs | [Przesyłaj strumieniowo dziennik aktywności do centrum zdarzeń](./resource-logs.md#send-to-azure-event-hubs). |

### <a name="azure-service-health"></a>Azure Service Health
[Azure Service Health](../../service-health/service-health-overview.md) zawiera informacje o kondycji usług platformy Azure w ramach subskrypcji, na których zależą Twoje aplikacje i zasoby.

| Element docelowy | Opis | Tematy pomocy |
|:---|:---|:---|
| Dziennik aktywności<br>Dzienniki usługi Azure Monitor | Rekordy Service Health są przechowywane w dzienniku aktywności platformy Azure, dzięki czemu można je przeglądać w Azure Portal lub wykonywać inne czynności, które można wykonać przy użyciu dziennika aktywności. | [Wyświetlanie powiadomień dotyczących kondycji usługi za pomocą witryny Azure Portal](../../service-health/service-notifications.md) |


## <a name="azure-resources"></a>Zasoby platformy Azure
Metryki i dzienniki zasobów zawierają informacje o _wewnętrznej_ operacji zasobów platformy Azure. Są one dostępne dla większości usług platformy Azure, a monitorowanie rozwiązań i szczegółowych informacji zbiera dodatkowe dane dotyczące określonych usług.

![Zbieranie zasobów platformy Azure](media/data-sources/data-source-azure-resources.svg)


### <a name="platform-metrics"></a>Metryki platformy 
Większość usług platformy Azure wyśle [metryki platformy](data-platform-metrics.md) , które odzwierciedlają ich wydajność i działanie bezpośrednio do bazy danych metryk. Określone [metryki różnią się w zależności od typu zasobu](metrics-supported.md). 

| Element docelowy | Opis | Tematy pomocy |
|:---|:---|:---|
| Metryki Azure Monitor | Metryki platformy będą zapisywać w bazie danych metryk Azure Monitor bez konfiguracji. Uzyskaj dostęp do metryk platformy z Eksplorator metryk.  | [Wprowadzenie do Eksploratora metryk platformy Azure](metrics-getting-started.md)<br>[Obsługiwane metryki z Azure Monitor](metrics-supported.md) |
| Dzienniki usługi Azure Monitor | Kopiuj metryki platformy do dzienników dla trendów i innych analiz przy użyciu Log Analytics. | [Diagnostyka Azure bezpośrednio do Log Analytics](./resource-logs.md#send-to-log-analytics-workspace) |
| Event Hubs | Metryki strumienia do innych lokalizacji przy użyciu Event Hubs. |[Przesyłanie strumieniowe danych monitorowania platformy Azure do centrum zdarzeń w celu użycia przez narzędzie zewnętrzne](stream-monitoring-data-event-hubs.md) |

### <a name="resource-logs"></a>Dzienniki zasobów
[Dzienniki zasobów](platform-logs-overview.md) zapewniają wgląd w _wewnętrzną_ operację zasobów platformy Azure.  Dzienniki zasobów są tworzone automatycznie, ale należy utworzyć ustawienie diagnostyczne, aby określić miejsce docelowe do zebrania dla każdego zasobu.

Wymagania dotyczące konfiguracji i zawartość dzienników zasobów różnią się w zależności od typu zasobu, a nie wszystkie usługi jeszcze nie zostały utworzone. Zobacz [obsługiwane usługi, schematy i kategorie dla dzienników zasobów platformy Azure,](./resource-logs-schema.md) Aby uzyskać szczegółowe informacje na temat każdej usługi i linki do szczegółowych procedur konfiguracyjnych. Jeśli usługa nie jest wymieniona w tym artykule, ta usługa nie tworzy obecnie dzienników zasobów.

| Element docelowy | Opis | Tematy pomocy |
|:---|:---|:---|
| Dzienniki usługi Azure Monitor | Wyślij dzienniki zasobów, aby Azure Monitor dzienniki analizy z innymi zebranymi danymi dzienników. | [Zbieranie dzienników zasobów platformy Azure w obszarze roboczym Log Analytics w Azure Monitor](./resource-logs.md#send-to-azure-storage) |
| Magazyn | Wyślij dzienniki zasobów do usługi Azure Storage w celu archiwizacji. | [Archiwizowanie dzienników zasobów platformy Azure](./resource-logs.md#send-to-log-analytics-workspace) |
| Event Hubs | Przesyłanie strumieniowe dzienników zasobów do innych lokalizacji przy użyciu Event Hubs. |[Przesyłanie strumieniowe dzienników zasobów platformy Azure do centrum zdarzeń](./resource-logs.md#send-to-azure-event-hubs) |

## <a name="operating-system-guest"></a>System operacyjny (gościa)
Zasoby obliczeniowe na platformie Azure, w innych chmurach i lokalnie, mają system operacyjny gościa do monitorowania. W przypadku instalacji jednego lub większej liczby agentów można zbierać dane telemetryczne od gościa do Azure Monitor, aby przeanalizować je za pomocą tych samych narzędzi monitorujących co same usługi platformy Azure.

![Kolekcja zasobów obliczeniowych platformy Azure](media/data-sources/compute-resources.png)

### <a name="azure-diagnostic-extension"></a>Rozszerzenie diagnostyki platformy Azure
Włączenie rozszerzenia Diagnostyka Azure dla usługi Azure Virtual Machines umożliwia zbieranie dzienników i metryk z systemu operacyjnego gościa zasobów obliczeniowych platformy Azure, w tym ról sieci Web i procesów roboczych usługi Azure Cloud Service (klasycznych), Virtual Machines, zestawów skalowania maszyn wirtualnych i Service Fabric.

| Element docelowy | Opis | Tematy pomocy |
|:---|:---|:---|
| Magazyn | Rozszerzenie Diagnostyka Azure zawsze zapisuje dane na koncie usługi Azure Storage. | [Instalowanie i Konfigurowanie rozszerzenia diagnostyki systemu Windows Azure (funkcji wad)](diagnostics-extension-windows-install.md)<br>[Używanie rozszerzenia diagnostycznego systemu Linux do monitorowania metryk i dzienników](../../virtual-machines/extensions/diagnostics-linux.md) |
| Metryki Azure Monitor | Podczas konfigurowania rozszerzenia diagnostyki do zbierania liczników wydajności są one zapisywane w bazie danych metryk Azure Monitor. | [Wysyłanie metryk systemu operacyjnego gościa do Azure Monitor magazynu metryk przy użyciu szablonu Menedżer zasobów dla maszyny wirtualnej z systemem Windows](collect-custom-metrics-guestos-resource-manager-vm.md) |
| Event Hubs | Skonfiguruj rozszerzenie diagnostyki, aby przesyłać strumieniowo dane do innych lokalizacji przy użyciu Event Hubs.  | [Przesyłanie strumieniowe danych Diagnostyka Azure przy użyciu Event Hubs](diagnostics-extension-stream-event-hubs.md)<br>[Używanie rozszerzenia diagnostycznego systemu Linux do monitorowania metryk i dzienników](../../virtual-machines/extensions/diagnostics-linux.md) |
| Dzienniki Application Insights | Zbieranie dzienników i liczników wydajności z zasobów obliczeniowych obsługujących aplikację do analizy z innymi danymi aplikacji. | [Wyślij usługę w chmurze, maszynę wirtualną lub Service Fabric dane diagnostyczne do Application Insights](diagnostics-extension-to-application-insights.md) |


### <a name="log-analytics-agent"></a>Agent usługi Log Analytics 
Zainstaluj agenta Log Analytics, aby uzyskać kompleksowe monitorowanie i zarządzanie maszynami wirtualnymi z systemem Windows lub Linux. Maszyna wirtualna może działać na platformie Azure, w innej chmurze lub lokalnie.

| Element docelowy | Opis | Tematy pomocy |
|:---|:---|:---|
| Dzienniki usługi Azure Monitor | Agent Log Analytics nawiązuje połączenie Azure Monitor bezpośrednio lub za pośrednictwem System Center Operations Manager i umożliwia zbieranie danych ze źródeł danych skonfigurowanych lub z rozwiązań monitorowania, które udostępniają dodatkowe informacje o aplikacjach działających na maszynie wirtualnej. | [Źródła danych agentów w Azure Monitor](agent-data-sources.md)<br>[Połącz Operations Manager z Azure Monitor](om-agents.md) |
| Magazyn maszyny wirtualnej | Azure Monitor dla maszyn wirtualnych używa agenta Log Analytics do przechowywania informacji o stanie kondycji w niestandardowej lokalizacji. Aby uzyskać więcej informacji, zobacz następną sekcję.  |


### <a name="azure-monitor-for-vms"></a>Usługa Azure Monitor dla maszyn wirtualnych 
[Azure monitor dla maszyn wirtualnych](../insights/vminsights-overview.md) zapewnia dostosowane środowisko monitorowania dla maszyn wirtualnych, które udostępniają funkcje wykraczające poza podstawowe funkcje Azure monitor. Wymaga Agent zależności na maszynach wirtualnych z systemem Windows i Linux, które integrują się z agentem Log Analytics w celu zbierania odnalezionych danych dotyczących procesów uruchomionych na maszynie wirtualnej i zewnętrznych zależności procesów.

| Element docelowy | Opis | Tematy pomocy |
|:---|:---|:---|
| Dzienniki usługi Azure Monitor | Przechowuje dane dotyczące procesów i zależności w agencie. | [Korzystanie z mapy Azure Monitor dla maszyn wirtualnych (wersja zapoznawcza) w celu zrozumienia składników aplikacji](../insights/vminsights-maps.md) |



## <a name="application-code"></a>Kod aplikacji
Szczegółowe monitorowanie aplikacji w Azure Monitor jest realizowane przy użyciu [Application Insights](/azure/application-insights/) , w którym zbierane są dane z aplikacji działających na różnych platformach. Aplikacja może działać na platformie Azure, w innej chmurze lub lokalnie.

![Zbieranie danych aplikacji](media/data-sources/applications.png)


### <a name="application-data"></a>Dane aplikacji
Po włączeniu Application Insights aplikacji przez zainstalowanie pakietu Instrumentacji program zbiera metryki i dzienniki związane z wydajnością i działaniem aplikacji. Application Insights przechowuje dane zbierane w tej samej Azure Monitorj platformie danych używanej przez inne źródła danych. Zawiera ona rozbudowane narzędzia do analizowania tych danych, ale można je również analizować przy użyciu danych z innych źródeł za pomocą narzędzi takich jak Eksplorator metryk i Log Analytics.

| Element docelowy | Opis | Tematy pomocy |
|:---|:---|:---|
| Dzienniki usługi Azure Monitor | Dane operacyjne dotyczące aplikacji, w tym widoki stron, żądania aplikacji, wyjątki i ślady. | [Analizowanie danych dziennika w Azure Monitor](../log-query/log-query-overview.md) |
|                    | Informacje o zależnościach między składnikami aplikacji w celu obsługi mapy aplikacji i korelacji telemetrii. | [Korelacja telemetrii w Application Insights](../app/correlation.md) <br> [Mapa aplikacji](../app/app-map.md) |
|            | Wyniki testów dostępności, które testują dostępność i czas odpowiedzi aplikacji z różnych lokalizacji w publicznym Internecie. | [Monitorowanie dostępności i czasu odpowiedzi dowolnej witryny sieci Web](../app/monitor-web-app-availability.md) |
| Metryki Azure Monitor | Application Insights zbiera metryki opisujące wydajność i działanie aplikacji, a także metryki niestandardowe zdefiniowane w aplikacji do bazy danych metryk Azure Monitorowych. | [Metryki oparte na dzienniku i metryki wstępnie zagregowane w usłudze Application Insights](../app/pre-aggregated-metrics-log-metrics.md)<br>[Interfejs API usługi Application Insights dla niestandardowych zdarzeń i metryk](../app/api-custom-events-metrics.md) |
| Azure Storage | Wyślij dane aplikacji do usługi Azure Storage w celu archiwizacji. | [Eksportowanie telemetrii z usługi Application Insights](../app/export-telemetry.md) |
|            | Szczegóły testów dostępności są przechowywane w usłudze Azure Storage. Użyj Application Insights w Azure Portal, aby pobrać na potrzeby analizy lokalnej. Wyniki testów dostępności są przechowywane w dziennikach Azure Monitor. | [Monitorowanie dostępności i czasu odpowiedzi dowolnej witryny sieci Web](../app/monitor-web-app-availability.md) |
|            | Dane śledzenia profilera są przechowywane w usłudze Azure Storage. Użyj Application Insights w Azure Portal, aby pobrać na potrzeby analizy lokalnej.  | [Profilowanie aplikacji produkcyjnych na platformie Azure za pomocą Application Insights](../app/profiler-overview.md) 
|            | Debugowanie danych migawek, które są przechwytywane dla podzbioru wyjątków, jest przechowywane w usłudze Azure Storage. Użyj Application Insights w Azure Portal, aby pobrać na potrzeby analizy lokalnej.  | [Jak działają migawki](../app/snapshot-debugger.md#how-snapshots-work) |

## <a name="monitoring-solutions-and-insights"></a>Monitorowanie rozwiązań i szczegółowych informacji
[Monitorowanie rozwiązań](../insights/solutions.md) i [szczegółowych](../insights/insights-overview.md) danych w celu zapewnienia dodatkowego wglądu w działanie konkretnej usługi lub aplikacji. Mogą one dotyczyć zasobów w różnych warstwach aplikacji, a nawet wielu warstw.

### <a name="monitoring-solutions"></a>Rozwiązania do monitorowania

| Element docelowy | Opis | Tematy pomocy
|:---|:---|:---|
| Dzienniki usługi Azure Monitor | Monitorowanie rozwiązań zbiera dane do dzienników Azure Monitor, które mogą być analizowane przy użyciu języka zapytań lub [widoków](view-designer.md) , które są zwykle dołączone do rozwiązania. | [Szczegóły zbierania danych na potrzeby monitorowania rozwiązań na platformie Azure](../monitor-reference.md) |


### <a name="azure-monitor-for-containers"></a>Usługa Azure Monitor dla kontenerów
[Azure monitor dla kontenerów](../insights/container-insights-overview.md) zapewnia dostosowane środowisko monitorowania dla [usługi Azure KUBERNETES Service (AKS)](../../aks/index.yml). Zbiera ona dodatkowe dane dotyczące tych zasobów opisanych w poniższej tabeli.

| Element docelowy | Opis | Tematy pomocy |
|:---|:---|:---|
| Dzienniki usługi Azure Monitor | Przechowuje dane monitorowania dla AKS, w tym spis, dzienniki i zdarzenia. Dane metryk są również przechowywane w dziennikach, aby można było korzystać z funkcji analizy w portalu. | [Sprawdzanie wydajności klastra usługi AKS w usłudze Azure Monitor dla kontenerów](../insights/container-insights-analyze.md) |
| Metryki Azure Monitor | Dane metryk są przechowywane w bazie danych metryk w celu utworzenia wizualizacji i alertów. | [Wyświetlanie metryk kontenera w Eksploratorze metryk](../insights/container-insights-analyze.md#view-container-metrics-in-metrics-explorer) |
| Azure Kubernetes Service | Zapewnia bezpośredni dostęp do dzienników kontenerów usługi Azure Kubernetes Service (AKS) (stdout/stderr), zdarzeń i metryk pod w portalu. | [Jak wyświetlać dzienniki Kubernetes, zdarzenia i metryki pod względem czasu rzeczywistego ](../insights/container-insights-livedata-overview.md) |

### <a name="azure-monitor-for-vms"></a>Usługa Azure Monitor dla maszyn wirtualnych
[Azure monitor dla maszyn wirtualnych](../insights/vminsights-overview.md) zapewnia dostosowane środowisko monitorowania maszyn wirtualnych. Opis danych zbieranych przez Azure Monitor dla maszyn wirtualnych znajduje się w powyższej sekcji [systemu operacyjnego (gościa)](#operating-system-guest) .

## <a name="custom-sources"></a>Źródła niestandardowe
Oprócz standardowych warstw aplikacji może być konieczne monitorowanie innych zasobów, których dane telemetryczne nie mogą być zbierane z innymi źródłami danych. W przypadku tych zasobów Zapisz te dane do metryk lub dzienników przy użyciu interfejsu API Azure Monitor.

![Kolekcja niestandardowa](media/data-sources/custom.png)

| Element docelowy | Metoda | Opis | Tematy pomocy |
|:---|:---|:---|:---|
| Dzienniki usługi Azure Monitor | Interfejs API modułu zbierającego dane | Zbieranie danych dziennika z dowolnego klienta REST i przechowywanie w Log Analytics obszarze roboczym. | [Wysyłanie danych dziennika do Azure Monitor za pomocą interfejsu API modułu zbierającego dane HTTP (publiczna wersja zapoznawcza)](data-collector-api.md) |
| Metryki Azure Monitor | Interfejs API metryk niestandardowych | Zbieraj dane metryk z dowolnego klienta REST i przechowuj je w bazie danych metryk Azure Monitor. | [Wysyłanie metryk niestandardowych dla zasobu platformy Azure do magazynu metryk Azure Monitor przy użyciu interfejsu API REST](metrics-store-custom-rest-api.md) |


## <a name="other-services"></a>Inne usługi
Inne usługi na platformie Azure zapisują dane na platformie danych Azure Monitor. Dzięki temu można analizować dane zbierane przez te usługi przy użyciu danych zbieranych przez Azure Monitor i korzystać z tych samych narzędzi do analizy i wizualizacji.

| Usługa | Element docelowy | Opis | Tematy pomocy |
|:---|:---|:---|:---|
| [Azure Security Center](../../security-center/index.yml) | Dzienniki usługi Azure Monitor | Azure Security Center przechowuje dane dotyczące zabezpieczeń zbierane w Log Analytics obszarze roboczym, co umożliwia przeanalizowanie ich przy użyciu innych danych dziennika zebranych przez Azure Monitor.  | [Zbieranie danych w usłudze Azure Security Center](../../security-center/security-center-enable-data-collection.md) |
| [Azure Sentinel](../../sentinel/index.yml) | Dzienniki usługi Azure Monitor | Na platformie Azure wskaźnikowe dane są przechowywane z różnych źródeł danych w obszarze roboczym Log Analytics, co umożliwia ich analizowanie z innymi danymi dziennika zbieranymi przez Azure Monitor.  | [Łączenie ze źródłami danych](../../sentinel/quickstart-onboard.md) |


## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej na temat [typów danych monitorowania zbieranych przez Azure monitor](data-platform.md) oraz sposobu wyświetlania i analizowania tych danych.
- Wykorzystaj [różne lokalizacje, w których zasoby platformy Azure przechowują dane](data-locations.md) i jak możesz uzyskać do nich dostęp. 
