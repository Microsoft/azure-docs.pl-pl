---
title: Azure Monitor istniejących Operations Manager klientów
description: Wskazówki dla istniejących użytkowników Operations Manager przejścia do monitorowania niektórych obciążeń do Azure Monitor w ramach przejścia do chmury.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/11/2021
ms.openlocfilehash: 85172e2430a3e65edb0c5ec119c920e2c7d20217
ms.sourcegitcommit: c7153bb48ce003a158e83a1174e1ee7e4b1a5461
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/15/2021
ms.locfileid: "98234857"
---
# <a name="azure-monitor-for-existing-operations-manager-customers"></a>Azure Monitor istniejących Operations Manager klientów
Ten artykuł zawiera wskazówki dla klientów, którzy obecnie używają [System Center Operations Manager](https://docs.microsoft.com/system-center/scom/welcome) i planowania przejścia do [Azure monitor](overview.md) podczas migrowania aplikacji firmowych i innych zasobów na platformę Azure. Przyjęto założenie, że ostatecznym celem jest pełne przejście do chmury, zastępowanie możliwie jak największej Operations Manager funkcji w Azure Monitor, bez naruszania wymagań firmy i operacyjnych IT. 

Szczegółowe zalecenia zawarte w tym artykule zmienią się w miarę Azure Monitor i Operations Manager dodawania funkcji. Jednak podstawowa strategia będzie niespójna.

> [!IMPORTANT]
> Istnieje koszt implementacji kilku funkcji Azure Monitor opisanych tutaj, dlatego należy oszacować ich wartości przed wdrożeniem w całym środowisku.

## <a name="prerequisites"></a>Wymagania wstępne
W tym artykule przyjęto założenie, że już używasz [Operations Manager](https://docs.microsoft.com/system-center/scom) i co najmniej ma podstawową wiedzę na temat [Azure monitor](overview.md). Aby zapoznać się z pełnym porównaniem między nimi, zobacz [Przewodnik monitorowania w chmurze: przegląd platform monitorowania](/azure/cloud-adoption-framework/manage/monitor/platform-overview). Ten artykuł zawiera szczegółowe informacje o różnych funkcjach między dwoma, które ułatwią zrozumienie niektórych zaleceń opisanych w tym miejscu. 


## <a name="general-strategy"></a>Ogólna strategia
Nie ma narzędzi migracji do konwersji zasobów z Operations Manager Azure Monitor, ponieważ platformy różnią się w zależności od siebie. Migracja będzie zamiast tego stanowić [standardową implementację Azure monitor](deploy.md) podczas dalszego korzystania z Operations Manager. W miarę dostosowywania Azure Monitor, aby spełniały wymagania dotyczące różnych aplikacji i składników, a także w miarę zwiększania funkcjonalności, można rozpocząć wycofywanie różnych pakietów administracyjnych i agentów w programie Operations Manager.

Ogólna strategia zalecana w tym artykule jest taka sama jak w [przewodniku monitorowania chmury](https://docs.microsoft.com/azure/cloud-adoption-framework/manage/monitor/), który zaleca strategię [monitorowania chmury hybrydowej](/azure/cloud-adoption-framework/manage/monitor/cloud-models-monitor-overview#hybrid-cloud-monitoring) , która umożliwia stopniowe przejście do chmury. Mimo że niektóre funkcje mogą się nakładać, ta strategia umożliwi zachowanie istniejących procesów firmy w taki sposób, aby były one bardziej znane z nową platformą. Można poruszać się tylko przed Operations Manager funkcją, ponieważ możesz ją zamienić na Azure Monitor. Użycie wielu narzędzi do monitorowania powoduje dodanie złożoności, ale umożliwia korzystanie z Azure Monitor możliwości monitorowania obciążeń chmurowych następnej generacji przy zachowaniu Operations Manager zdolności do monitorowania oprogramowania serwera i składników infrastruktury, które mogą być lokalne lub w innych chmurach. 


## <a name="components-to-monitor"></a>Składniki do monitorowania
Ułatwia ona kategoryzowanie różnych typów obciążeń, które należy monitorować, aby określić odrębną strategię monitorowania dla każdej z nich. [Przewodnik po monitorowaniu w chmurze: formułowanie strategii monitorowania](/azure/cloud-adoption-framework/strategy/monitoring-strategy#high-level-modeling) zapewnia szczegółowy podział różnych warstw w środowisku, które wymagają monitorowania w miarę postępów od starszych aplikacji dla przedsiębiorstw do nowoczesnych aplikacji w chmurze.

Przed chmurą użyto Operations Manager do monitorowania wszystkich warstw. Po rozpoczęciu przejścia z infrastrukturą jako usługą (IaaS) można nadal używać Operations Manager dla maszyn wirtualnych, ale rozpocząć korzystanie Azure Monitor z zasobów w chmurze. Dalsze przejście do nowoczesnych aplikacji przy użyciu platformy jako usługi (PaaS) umożliwia skoncentrowanie się na Azure Monitor i rozpoczęcie wycofywania funkcji Operations Manager.


![Modele chmury](https://docs.microsoft.com/azure/cloud-adoption-framework/strategy/media/monitoring-strategy/cloud-models.png)

Te warstwy można uprościć do następujących kategorii, które są dokładniej opisane w dalszej części tego artykułu. Chociaż każde obciążenie monitorowania w środowisku może nie pasować do jednej z tych kategorii, każdy z nich powinien być blisko, aby uzyskać ogólne zalecenia, które mają zostać zastosowane.

**Aplikacje biznesowe.** Aplikacje, które udostępniają funkcje specyficzne dla Twojej firmy. Mogą być wewnętrzne lub zewnętrzne i często opracowywane wewnętrznie przy użyciu kodu niestandardowego. Starsze aplikacje są zwykle hostowane na maszynach wirtualnych lub fizycznych z systemem Windows lub Linux, a nowsze aplikacje są oparte na usługach aplikacji na platformie Azure, takich jak Azure Web Apps i Azure Functions.

**Usługi platformy Azure.** Zasoby platformy Azure, które obsługują Twoje aplikacje biznesowe, które zostały zmigrowane do chmury. Dotyczy to m.in. usług, takich jak Azure Storage, Azure SQL i Azure IoT. Obejmuje to również maszyny wirtualne platformy Azure, ponieważ są monitorowane jak inne usługi platformy Azure, ale aplikacje i oprogramowanie działające w systemie operacyjnym gościa tych maszyn wirtualnych wymagają więcej monitorowania poza hostem.

**Oprogramowanie serwera.** Oprogramowanie działające na maszynach wirtualnych i fizycznych, które obsługują aplikacje biznesowe lub spakowane aplikacje, które zapewniają ogólną funkcjonalność Twojej firmy. Przykłady obejmują Internet Information Server (IIS), SQL Server, Exchange i SharePoint. Obejmuje to również system operacyjny Windows lub Linux na maszynach wirtualnych i fizycznych.

**Infrastruktura lokalna.** Składniki charakterystyczne dla środowiska lokalnego, które wymagają monitorowania. Obejmuje to takie zasoby jak serwery fizyczne, magazyn i składniki sieciowe. Są to składniki, które są zwirtualizowane po przejściu do chmury.

## <a name="sample-walkthrough"></a>Przewodnik po przykładzie
Poniżej przedstawiono hipotetyczny Przewodnik migracji z Operations Manager do Azure Monitor. Nie jest to przeznaczone do reprezentowania pełnej złożoności rzeczywistej migracji, ale co najmniej udostępnia podstawowe kroki i sekwencję. W poniższych sekcjach szczegółowo opisano każdy z tych kroków.

Twoje środowisko przed przeniesieniem składników na platformę Azure jest oparte na maszynach wirtualnych i fizycznych znajdujących się lokalnie lub u dostawcy usług zarządzanych. Jest on oparty na Operations Manager do monitorowania aplikacji, oprogramowania serwera i innych składników infrastruktury w środowisku, takich jak serwery fizyczne i sieci. W przypadku oprogramowania serwerowego, takiego jak usługi IIS, SQL Server i różne oprogramowanie dostawcy, można używać standardowych pakietów administracyjnych, a te pakiety administracyjne są dopasowywane do określonych wymagań. Tworzysz niestandardowe pakiety administracyjne dla aplikacji firmowych i innych składników, których nie można monitorować przy użyciu istniejących pakietów administracyjnych, i skonfiguruj Operations Manager, aby obsługiwać procesy biznesowe.

Migracja na platformę Azure rozpoczyna się od IaaS, co umożliwia przenoszenie maszyn wirtualnych obsługujących aplikacje biznesowe na platformę Azure. Wymagania dotyczące monitorowania tych aplikacji i oprogramowania serwerowego, od których zależą, nie zmieniają się, i nadal używasz Operations Manager na tych serwerach z istniejącymi pakietami administracyjnymi. 

Azure Monitor jest włączona dla usług platformy Azure zaraz po utworzeniu subskrypcji platformy Azure. Automatycznie zbiera metryki platformy i dziennik aktywności, a następnie konfiguruje dzienniki zasobów do zebrania, aby można było interaktywnie analizować wszystkie dostępne dane telemetryczne przy użyciu zapytań dzienników. Włączenie Azure Monitor dla maszyn wirtualnych na maszynach wirtualnych umożliwia analizowanie danych monitorowania w całym środowisku oraz odnajdywanie relacji między maszynami i procesami. Możesz zwiększyć użycie Azure Monitor do lokalnych maszyn fizycznych i wirtualnych, włączając na nich serwery z włączonym funkcją Azure Arc. 

Włączasz Application Insights dla każdej aplikacji biznesowej. Identyfikuje różne składniki każdej aplikacji, zaczyna zbierać dane dotyczące użycia i wydajności, a także identyfikuje wszelkie błędy występujące w kodzie. Możesz tworzyć testy dostępności, aby aktywnie testować aplikacje zewnętrzne i wysyłać alerty do problemów z wydajnością lub dostępnością. Chociaż Application Insights zapewnia zaawansowane funkcje, które nie są dostępne w Operations Manager, można nadal polegać na niestandardowych pakietach administracyjnych opracowanych dla aplikacji firmowych, ponieważ obejmują one scenariusze monitorowania, które nie są jeszcze objęte Azure Monitor. 

W celu uzyskania wiedzy Azure Monitor, należy zacząć tworzyć reguły alertów, które mogą zastąpić niektóre funkcje pakietu administracyjnego i rozpocząć rozwijanie procesów firmy w celu korzystania z nowej platformy monitorowania. Pozwala to rozpocząć usuwanie maszyn i pakietów administracyjnych z Operations Manager grupy zarządzania. Nadal używasz pakietów administracyjnych dla krytycznego oprogramowania serwera i infrastruktury lokalnej, ale nadal Obserwuj nowe funkcje w Azure Monitor, które umożliwią wycofanie dodatkowych funkcji.

## <a name="monitor-azure-services"></a>Monitorowanie usług platformy Azure
Usługi platformy Azure rzeczywiście wymagają, aby Azure Monitor zbierać dane telemetryczne i w momencie tworzenia subskrypcji platformy Azure została włączona. [Dziennik aktywności](platform/activity-log.md) jest automatycznie zbierany dla subskrypcji, a [metryki platformy](platform/data-platform-metrics.md) są automatycznie zbierane z dowolnych utworzonych zasobów platformy Azure. Możesz od razu zacząć korzystać z [Eksploratora metryk](platform/metrics-getting-started.md), który jest podobny do widoków wydajności w konsoli operacje, ale zapewnia interaktywną analizę i [Zaawansowane agregacje](platform/metrics-charts.md) danych. [Utwórz alert metryki](platform/alerts-metric.md) , aby otrzymywać powiadomienia, gdy wartość przekracza próg lub [Dodaj wykres do pulpitu nawigacyjnego platformy Azure](platform/metrics-charts.md#pin-charts-to-dashboards) w celu zapewnienia widoczności.

[![Eksplorator metryk](media/azure-monitor-operations-manager/metrics-explorer.png)](media/azure-monitor-operations-manager/metrics-explorer.png#lightbox)

[Utwórz ustawienie diagnostyczne](platform/diagnostic-settings.md) dla każdego zasobu platformy Azure w celu wysyłania metryk i [dzienników zasobów](platform/resource-logs.md), które zawierają szczegółowe informacje o operacji wewnętrznej poszczególnych zasobów w obszarze roboczym log Analytics. Zapewnia to wszystkie dostępne dane telemetryczne dla zasobów i umożliwia używanie [log Analytics](log-query/log-analytics-overview.md) do interaktywnego analizowania danych dzienników i wydajności przy użyciu zaawansowanego języka zapytań, który nie jest odpowiednikiem Operations Manager. Możesz również utworzyć [alerty kwerendy dziennika](platform/alerts-log-query.md), które mogą używać złożonej logiki do określania warunków alertów i skorelowania danych między wieloma zasobami.

[![Dzienniki analizy](media/azure-monitor-operations-manager/log-analytics.png)](media/azure-monitor-operations-manager/log-analytics.png#lightbox)

[Szczegółowe informacje](monitor-reference.md) w Azure monitor są podobne do pakietów administracyjnych w celu zapewnienia unikatowego monitorowania określonej usługi platformy Azure. Szczegółowe informacje są obecnie dostępne dla kilku usług, w tym sieci, magazynu i kontenerów, a inne są ciągle dodawane.

[![Przykład wglądu w szczegółowe dane](media/azure-monitor-operations-manager/insight.png)](media/azure-monitor-operations-manager/insight.png#lightbox)


Szczegółowe informacje są oparte na [skoroszytach](platform/workbooks-overview.md) w Azure monitor, które łączą metryki i zapytania dzienników w rozbudowanych raportach interaktywnych. Utwórz własne skoroszyty, aby połączyć dane z wielu usług, podobnie jak w przypadku tworzenia widoków niestandardowych i raportów w konsoli operacje.

### <a name="azure-management-pack"></a>Pakiet administracyjny platformy Azure
[Pakiet administracyjny platformy Azure](https://www.microsoft.com/download/details.aspx?id=50013) umożliwia Operations Manager odnajdywania zasobów platformy Azure i monitorowania ich kondycji na podstawie określonego zestawu scenariuszy monitorowania. Ten pakiet administracyjny wymaga wykonania dodatkowej konfiguracji dla każdego zasobu na platformie Azure, ale może być przydatne, aby zapewnić widoczność zasobów platformy Azure w konsoli operacje do momentu, gdy procesy biznesowe zostaną rozpakowane na Azure Monitor.

[![Pakiet administracyjny platformy Azure](media/azure-monitor-operations-manager/operations-console.png)](media/azure-monitor-operations-manager/operations-console.png#lightbox)

 Możesz użyć pakietu administracyjnego platformy Azure, jeśli chcesz uwidocznić niektóre zasoby platformy Azure w konsoli operacje i zintegrować niektóre podstawowe alerty z istniejącymi procesami. W rzeczywistości używa danych zbieranych przez Azure Monitor. Aby uzyskać długoterminowe monitorowanie zasobów platformy Azure, należy poszukać Azure Monitor. 


## <a name="monitor-server-software-and-local-infrastructure"></a>Monitorowanie oprogramowania serwera i infrastruktury lokalnej
W przypadku przenoszenia maszyn do chmury wymagania dotyczące monitorowania nie są zmieniane. Nie jest już konieczne monitorowanie ich składników fizycznych, ponieważ są one zwirtualizowane, ale system operacyjny gościa i jego obciążenia mają takie same wymagania, niezależnie od ich środowiska.

[Azure monitor dla maszyn wirtualnych](insights/vminsights-overview.md) jest podstawową funkcją w Azure monitor do monitorowania maszyn wirtualnych i ich systemu operacyjnego gościa oraz obciążeń. Podobnie jak w przypadku Operations Manager, Azure Monitor dla maszyn wirtualnych używa agenta do zbierania danych z systemu operacyjnego gościa maszyn wirtualnych. Jest to te same dane dotyczące wydajności i zdarzeń, zwykle używane przez pakiety administracyjne do analizy i generowania alertów. Nie ma już istniejących reguł, które identyfikują i zgłaszają problemy związane z aplikacjami biznesowymi i oprogramowaniem serwerowym działającym na tych komputerach. Należy utworzyć własne reguły alertów, aby otrzymywać proaktywnie powiadomienia o wykrytych problemach.

[![Azure Monitor dla maszyn wirtualnych wydajność](media/azure-monitor-operations-manager/vm-insights-performance.png)](media/azure-monitor-operations-manager/vm-insights-performance.png#lightbox)

Azure Monitor również nie mierzy kondycji różnych aplikacji i usług działających na maszynie wirtualnej. Alerty metryk mogą być automatycznie rozwiązywane, gdy wartość spadnie poniżej wartości progowej, ale Azure Monitor nie ma obecnie możliwości zdefiniowania kryteriów kondycji dla aplikacji i usług działających na maszynie, ani nie zapewnia zestawienia kondycji do grupowania kondycji powiązanych składników.

> [!NOTE]
> Nowa [Funkcja kondycji gościa dla Azure monitor dla maszyn wirtualnych](insights/vminsights-health-overview.md) jest teraz dostępna w publicznej wersji zapoznawczej i generuje alert na podstawie stanu kondycji zestawu metryk wydajności. Jest to początkowo ograniczone do określonego zestawu liczników wydajności związanych z systemem operacyjnym gościa, a nie aplikacji lub innych obciążeń uruchomionych na maszynie wirtualnej.
> 
> [![Azure Monitor dla maszyn wirtualnych kondycji gościa](media/azure-monitor-operations-manager/vm-insights-guest-health.png)](media/azure-monitor-operations-manager/vm-insights-guest-health.png#lightbox)

Monitorowanie oprogramowania na maszynach w środowisku hybrydowym zwykle używa kombinacji Azure Monitor dla maszyn wirtualnych i Operations Manager, w zależności od wymagań poszczególnych maszyn i w procesie zapadalności procesów operacyjnych wokół Azure Monitor. Program Microsoft Management Agent (nazywany agentem Log Analytics w programie Azure Monitor) jest używany przez obie platformy, aby umożliwić jednoczesne monitorowanie jednej maszyny.

> [!NOTE]
> W przyszłości Azure Monitor dla maszyn wirtualnych przejdzie do [agenta Azure monitor](platform/azure-monitor-agent-overview.md), który jest obecnie w publicznej wersji zapoznawczej. Będzie ona zgodna z Microsoft Monitoring Agent, dzięki czemu ta sama maszyna wirtualna będzie w dalszym ciągu monitorowana przez obie platformy.

W dalszym ciągu używaj Operations Manager do funkcji, które nie mogą być dostarczone przez Azure Monitor. Obejmuje to pakiety administracyjne dla krytycznego oprogramowania serwera, takie jak IIS, SQL Server lub Exchange. Możesz również mieć niestandardowe pakiety administracyjne opracowane dla infrastruktury lokalnej, których nie można osiągnąć przy użyciu Azure Monitor. Kontynuuj również korzystanie Operations Manager, jeśli jest ściśle zintegrowany z procesami operacyjnymi do momentu przejścia do modernizacji operacji usługi, w których Azure Monitor i innych usług platformy Azure można rozszerzyć lub zamienić. 

Użyj Azure Monitor fo maszyn wirtualnych, aby ulepszyć bieżące monitorowanie nawet wtedy, gdy nie zastąpi on natychmiast Operations Manager. Przykłady funkcji unikatowych dla Azure Monitor są następujące:

- Wykrywaj i monitoruj relacje między maszynami wirtualnymi i ich zewnętrznymi zależnościami.
- Wyświetlanie zagregowanych danych wydajności na wielu maszynach wirtualnych w interaktywnych wykresach i skoroszytach.
- Użyj [zapytań dzienników](log-query/log-query-overview.md) , aby interaktywnie analizować dane telemetryczne z maszyn wirtualnych przy użyciu danych z innych zasobów platformy Azure.
- Tworzenie [reguł alertów dziennika](platform/alerts-log-query.md) opartych na złożonej logice na wielu maszynach wirtualnych.

[![Mapa Azure Monitor dla maszyn wirtualnych](media/azure-monitor-operations-manager/vm-insights-map.png)](media/azure-monitor-operations-manager/vm-insights-map.png#lightbox)

Oprócz maszyn wirtualnych platformy Azure Azure Monitor dla maszyn wirtualnych mogą monitorować maszyny lokalnie i w innych chmurach przy użyciu serwerów z [obsługą usługi Azure Arc](../azure-arc/servers/overview.md). Serwery z włączonymi środkami ARC umożliwiają zarządzanie maszynami z systemami Windows i Linux hostowanymi poza platformą Azure, w sieci firmowej lub innym dostawcą chmury, które są zgodne z zarządzaniem natywnymi maszynami wirtualnymi platformy Azure.



## <a name="monitor-business-applications"></a>Monitoruj aplikacje biznesowe
Zwykle wymagane są niestandardowe pakiety administracyjne do monitorowania aplikacji firmowych za pomocą Operations Manager, wykorzystując agentów zainstalowanych na poszczególnych maszynach wirtualnych. Application Insights w Azure Monitor monitoruje aplikacje oparte na sieci Web, niezależnie od tego, czy znajdują się na platformie Azure, w innych chmurach czy lokalnie, dlatego mogą być używane we wszystkich aplikacjach niezależnie od tego, czy zostały zmigrowane do platformy Azure.

Jeśli monitorowanie aplikacji biznesowej jest ograniczone do funkcjonalności zapewnianej przez [szablon wydajności aplikacji .NET]() w Operations Manager, można najprawdopodobniej przeprowadzić migrację do Application Insights bez utraty funkcjonalności. W rzeczywistości Application Insights będzie zawierać znaczną liczbę dodatkowych funkcji, w tym następujące:

- Automatycznie Odnajduj i monitoruj składniki aplikacji.
- Zbieraj szczegółowe dane dotyczące użycia i wydajności aplikacji, takie jak czas odpowiedzi, wskaźniki niepowodzeń i stawki żądania.
- Zbieraj dane przeglądarki, takie jak widoki stron i wydajność ładowania.
- Wykrywaj wyjątki i przechodzenie do szczegółów w śladach stosu i powiązanych żądaniach.
- Przeprowadzanie zaawansowanej analizy przy użyciu funkcji, takich jak [śledzenie rozproszone](app/distributed-tracing.md) i [wykrywanie inteligentne](app/proactive-diagnostics.md).
- Użyj [Eksploratora metryk](platform/metrics-getting-started.md) do interaktywnej analizy danych wydajności.
- Użyj [zapytań dzienników](log-query/log-query-overview.md) , aby interaktywnie analizować zebrane dane telemetryczne wraz z danymi zebranymi dla usług platformy Azure i Azure monitor dla maszyn wirtualnych.

[![Application Insights](media/azure-monitor-operations-manager/application-insights.png)](media/azure-monitor-operations-manager/application-insights.png#lightbox)

Istnieją pewne scenariusze, w których może być konieczne kontynuowanie korzystania z Operations Manager oprócz Application Insights do momentu, aż będzie możliwe uzyskanie wymaganych funkcji. Przykłady, w których może być konieczne kontynuowanie pracy z Operations Manager:

-  [Testy dostępności](app/monitor-web-app-availability.md), które umożliwiają monitorowanie i zgłaszanie alertów dotyczących dostępności i czasu odpowiedzi aplikacji, wymagają żądań przychodzących z adresów IP agentów testów sieci Web. Jeśli zasady nie zezwalają na taki dostęp, może być konieczne pozostawienie [monitorów dostępności aplikacji sieci Web](/system-center/scom/web-application-availability-monitoring-template) w Operations Manager.
- W Operations Manager można ustawić dowolny interwał sondowania dla testów dostępności, wielu klientów sprawdza co 60-120 sekund. Application Insights ma minimalny interwał sondowania wynoszący 5 minut, który może być zbyt długi dla niektórych klientów.
- Znaczna ilość monitorowania w Operations Manager jest wykonywana przez zbieranie zdarzeń generowanych przez aplikacje i uruchamianie skryptów na lokalnym agencie. Nie są to standardowe opcje w Application Insights, więc można wymagać niestandardowej pracy, aby osiągnąć wymagania biznesowe. Może to obejmować niestandardowe reguły alertów korzystających z danych zdarzeń przechowywanych w obszarze roboczym Log Analytics i skryptów uruchomionych w gościu maszyn wirtualnych przy użyciu [hybrydowego procesu roboczego elementu Runbook](../automation/automation-hybrid-runbook-worker.md).
- W zależności od języka, w którym napisano aplikację, może być ograniczona w [Instrumentacji, z której można korzystać z Application Insights](app/platforms.md).

Postępując zgodnie z podstawową strategią w pozostałych sekcjach tego przewodnika, nadal używaj Operations Manager aplikacji dla firm, ale Skorzystaj z dodatkowych funkcji udostępnianych przez Application Insights. W miarę możliwości zastępowania funkcji krytycznych Azure Monitor można rozpocząć wycofywanie niestandardowych pakietów administracyjnych.


## <a name="next-steps"></a>Następne kroki

- Szczegółowe porównanie Azure Monitor i System Center Operations Manager oraz więcej informacji na temat projektowania i implementowania hybrydowego środowiska monitorowania można znaleźć w [przewodniku monitorowania chmury](/azure/cloud-adoption-framework/manage/monitor/) .
- Przeczytaj więcej [na temat monitorowania zasobów platformy Azure w Azure monitor](insights/monitor-azure-resource.md).
- Przeczytaj więcej [na temat monitorowania maszyn wirtualnych platformy Azure w Azure monitor](insights/monitor-vm-azure.md).
- Przeczytaj więcej na temat [Azure monitor dla maszyn wirtualnych](insights/vminsights-overview.md).
- Przeczytaj więcej na temat [Application Insights](app/app-insights-overview.md).
