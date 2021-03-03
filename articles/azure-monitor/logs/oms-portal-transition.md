---
title: Portal pakietu OMS przeniesiony do platformy Azure | Microsoft Docs
description: Dostęp do portalu pakietu OMS jest wykonywany przy użyciu wszystkich funkcji przenoszonych do Azure Portal. Ten artykuł zawiera szczegółowe informacje na temat tego przejścia.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/22/2019
ms.openlocfilehash: ab7d4bf0b8d8ca32dafe6f19b46047eca89a7734
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101733979"
---
# <a name="oms-portal-moving-to-azure"></a>Portal pakietu OMS przeniesiony do platformy Azure

> [!NOTE]
> Ten artykuł dotyczy zarówno chmury publicznej platformy Azure, jak i chmury dla instytucji rządowych, z wyjątkiem sytuacji, w której zanotowano inaczej

**Portal pakietu OMS dla chmury publicznej platformy Azure został oficjalnie wycofany. Portal usługi OMS dla chmury dla instytucji rządowych w Stanach Zjednoczonych został oficjalnie wycofany 15 maja 2019.** Przyjemnością się do Azure Portal i oczekujemy, że przejście będzie łatwe. Jednak rozumiemy, że zmiany są trudne i mogą być kłopotliwe. Pozostała część tego artykułu obejmuje najważniejsze scenariusze i plan dla tego przejścia.

Azure Portal to centrum dla wszystkich usług platformy Azure i oferuje rozbudowane środowisko zarządzania z możliwościami, takimi jak pulpity nawigacyjne służące do przypinania zasobów, inteligentne wyszukiwanie do znajdowania zasobów i tagowanie w celu zarządzania zasobami. Aby skonsolidować i usprawnić przepływ pracy monitorowania i zarządzania, rozpocząłmy Dodawanie możliwości portalu OMS do Azure Portal. Wszystkie funkcje portalu pakietu OMS są teraz częścią Azure Portal. W rzeczywistości niektóre nowe funkcje, takie jak Analiza ruchu, są dostępne tylko w Azure Portal. Będzie można wykonać wszystkie czynności wykonywane w portalu pakietu OMS z Azure Portal i nie tylko. Jeśli jeszcze tego nie zrobiono, należy zacząć korzystać z Azure Portal dzisiaj!

## <a name="what-is-changing"></a>Co się zmieni? 
Następujące zmiany są ogłaszane za pomocą przestarzałego portalu pakietu OMS. Każda z tych zmian została szczegółowo opisana w poniższych sekcjach.

- Nowe [obszary robocze](#new-workspaces) można tworzyć tylko w Azure Portal.
- Nowe środowisko zarządzania alertami [zastępuje alert Management rozwiązanie](#changes-to-alerts).
- [Zarządzanie dostępem użytkowników](#user-access-and-role-migration) odbywa się teraz w Azure Portal przy użyciu kontroli dostępu opartej na rolach platformy Azure.
- [Application Insights Connector nie jest już wymagana](#application-insights-connector-and-solution) , ponieważ te same funkcje są włączane za pomocą zapytań między obszarami roboczymi.
- [Aplikacja mobilna pakietu OMS](#oms-mobile-app) jest przestarzała. 
- [Rozwiązanie sieciowej grupy zabezpieczeń jest zastępowane](#azure-network-security-group-analytics) ulepszonymi funkcjami dostępnymi za pośrednictwem rozwiązania Analiza ruchu.
- Nowe połączenia z System Center Operations Manager do Log Analytics wymagają [zaktualizowanych pakietów administracyjnych](#system-center-operations-manager).
- Aby uzyskać szczegółowe informacje na temat zmian [Update Management](../../automation/update-management/overview.md), zobacz [Migrowanie wdrożeń aktualizacji pakietu OMS na platformę Azure](../../automation/migrate-oms-update-deployments.md) .


## <a name="what-should-i-do-now"></a>Co mam zrobić teraz?
Chociaż większość funkcji będzie nadal działać bez konieczności przeprowadzania migracji, należy wykonać następujące zadania:

- Należy [przeprowadzić migrację uprawnień użytkowników](#user-access-and-role-migration) do Azure Portal.
- Aby uzyskać szczegółowe informacje na temat przechodzenia do rozwiązania Update Management, zobacz [Migrowanie wdrożeń aktualizacji pakietu OMS na platformę Azure](../../automation/migrate-oms-update-deployments.md) .

Zapoznaj się z [typowymi pytaniami dotyczącymi przejścia z portalu pakietu OMS w celu Azure Portal log Analytics użytkowników,](../overview.md) Aby uzyskać informacje o sposobach przejścia do Azure Portal. 

## <a name="user-access-and-role-migration"></a>Migracja dostępu użytkowników i ról
Azure Portal zarządzanie dostępem jest bogatsze i wydajniejsze niż zarządzanie dostępem w portalu pakietu OMS. Aby uzyskać szczegółowe informacje na temat zarządzania dostępem w programie Log Analytics, zobacz temat [projektowanie obszaru roboczego dzienniki Azure monitor](../logs/design-logs-deployment.md) .

> [!NOTE]
> W poprzednich wersjach tego artykułu określono, że uprawnienia zostaną automatycznie skonwertowane z portalu pakietu OMS do Azure Portal. Ta Automatyczna konwersja nie jest już planowana i należy wykonać konwersję samodzielnie.

Być może masz już odpowiedni dostęp w Azure Portal w tym przypadku nie musisz wprowadzać żadnych zmian. Istnieje kilka przypadków, w których użytkownik może nie mieć odpowiedniego dostępu, w takim przypadku administrator musi przypisać uprawnienia.

- W portalu pakietu OMS masz uprawnienia użytkownika tylko do odczytu, ale nie masz uprawnień w Azure Portal. 
- Masz uprawnienia współautora w portalu pakietu OMS, ale tylko dostęp z czytnika w Azure Portal.
 
W obu tych przypadkach administrator musi ręcznie przypisać odpowiednią rolę z poniższej tabeli. Zalecamy przypisanie tej roli na poziomie grupy zasobów lub subskrypcji.  W obu przypadkach będzie dostępna więcej wskazówek dotyczących skryptów.

| Uprawnienie do portalu pakietu OMS | Rola platformy Azure |
|:---|:---|
| ReadOnly | Czytelnik usługi Log Analytics |
| Współautor | Współautor usługi Log Analytics |
| Administrator | Właściciel | 
 

## <a name="new-workspaces"></a>Nowe obszary robocze
Nie można już tworzyć nowych obszarów roboczych przy użyciu portalu pakietu OMS. Postępuj zgodnie ze wskazówkami w temacie [tworzenie log Analytics obszaru roboczego w Azure Portal,](./quick-create-workspace.md) aby utworzyć nowy obszar roboczy w Azure Portal.

## <a name="changes-to-alerts"></a>Zmiany alertów

### <a name="alert-extension"></a>Rozszerzenie alertu  

Alerty zostały [rozszerzone do Azure Portal](../alerts/alerts-unified-log.md) istniejące alerty będą nadal wyświetlane w portalu pakietu OMS, ale można nimi zarządzać tylko w Azure Portal. Jeśli uzyskujesz dostęp do alertów programowo przy użyciu szablonu zasobu interfejsu API REST Log Analytics lub alertu usługi Log Analytics, musisz użyć grup akcji zamiast akcji w wywołaniach interfejsu API, szablonach Azure Resource Manager i poleceniach programu PowerShell.

### <a name="alert-management-solution"></a>Rozwiązanie do zarządzania alertami
W przypadku zmiany z poprzedniego anonsu rozwiązanie do [zarządzania alertami](../insights/alert-management-solution.md) będzie nadal dostępne i w pełni obsługiwane w Azure Portal. Możesz nadal instalować rozwiązanie z witryny Azure Marketplace.

Mimo że rozwiązanie do zarządzania alertami jest nadal dostępne, zachęcamy do korzystania z [ujednoliconego interfejsu alertów Azure monitor](../alerts/alerts-overview.md) do wizualizacji i zarządzania wszystkimi alertami na platformie Azure. To nowe środowisko umożliwia natywne agregowanie alertów z wielu źródeł w ramach platformy Azure, w tym alertów dzienników z Log Analytics. Jeśli używasz ujednoliconego interfejsu alertów Azure Monitor, rozwiązanie do zarządzania alertami jest wymagane tylko w celu umożliwienia integracji alertów z programu System Center Operations Manager z platformą Azure. W ramach ujednoliconego interfejsu alertów w Azure Monitor można zobaczyć dystrybucje alertów, skorzystać z zautomatyzowanego grupowania powiązanych alertów za pośrednictwem grup inteligentnych i wyświetlać alerty w wielu subskrypcjach przy zastosowaniu bogatych filtrów. Przyszłe postępy związane z zarządzaniem alertami będą głównie dostępne w ramach tego nowego środowiska. 

Dane zbierane przez rozwiązanie do zarządzania alertami (rekordy z typem alertu) nadal będą znajdować się w Log Analytics, o ile rozwiązanie zostanie zainstalowane dla obszaru roboczego. 

## <a name="oms-mobile-app"></a>Aplikacja mobilna OMS
Aplikacja mobilna pakietu OMS zostanie również przemieszczona przy użyciu portalu pakietu OMS. Zamiast aplikacji mobilnej OMS, aby uzyskać dostęp do informacji o infrastrukturze IT, pulpitach nawigacyjnych i zapisanych zapytaniach, możesz uzyskać dostęp do Azure Portal bezpośrednio z przeglądarki na urządzeniu przenośnym. Aby otrzymywać alerty, należy skonfigurować [grupy akcji platformy Azure](../alerts/action-groups.md) w celu otrzymywania powiadomień w formie wiadomości SMS lub połączenia głosowego

## <a name="application-insights-connector-and-solution"></a>Application Insights Connector i rozwiązanie
[Application Insights Connector](../logs/app-insights-connector.md) zapewnia sposób dołączania Application Insights danych do obszaru roboczego log Analytics. Ta duplikacja danych była wymagana w celu umożliwienia wglądu w dane infrastruktury i aplikacji. Dzięki obsłudze Application Insights rozszerzonego przechowywania danych w marcu 2019 i możliwości wykonywania [zapytań między zasobami](../logs/cross-workspace-query.md) oprócz możliwości [wyświetlania wielu Azure Monitor Application Insights zasobów](./unify-app-resource-data.md)nie ma potrzeby duplikowania danych z zasobów Application Insights i wysyłania ich do log Analytics. Ponadto łącznik wysyła podzestaw właściwości aplikacji do Log Analytics, podczas gdy zapytania między zasobami zapewniają większą elastyczność.  

W związku z tym Application Insights Connector była przestarzała i usuwana z portalu Azure Marketplace wraz z zaniechaniem korzystania z Portal OMS w dniu 30 marca 2019. Istniejące połączenia będą nadal działały do 30 czerwca 2019. W przypadku wycofania portalu pakietu OMS nie istnieje sposób konfigurowania i usuwania istniejących połączeń z portalu. Ta wartość będzie obsługiwana przy użyciu interfejsu API REST, który zostanie udostępniony w styczniu 2019, a powiadomienie zostanie ogłoszone w [usłudze Azure Updates](https://azure.microsoft.com/updates/). 

## <a name="azure-network-security-group-analytics"></a>Azure Network Security Group Analytics
[Azure Network Security Group Analytics rozwiązanie](../insights/azure-networking-analytics.md#azure-network-security-group-analytics-solution-in-azure-monitor) zostanie zastąpione ostatnio uruchomionymi [Analiza ruchu](https://azure.microsoft.com/blog/traffic-analytics-in-preview/) , które zapewniają wgląd w aktywność użytkowników i aplikacji w sieciach w chmurze. Analiza ruchu pomaga w inspekcji aktywności sieciowej organizacji, zabezpieczaniu aplikacji i danych, optymalizowaniu wydajności obciążeń i utrzymania zgodności. 

To rozwiązanie analizuje dzienniki przepływu sieciowej grupy zabezpieczeń i udostępnia szczegółowe informacje w następujących tematach.

- Przepływy ruchu między sieciami między platformą Azure i Internetem, regionami chmury publicznej, sieci wirtualnych i podsieciami.
- Aplikacje i protokoły w sieci bez potrzeby wykrywania lub dedykowanych urządzeń do zbierania przepływów.
- Najpopularniejsze rozmowy, aplikacje rozmawiające, konwersacje maszyn wirtualnych w chmurze, punkty połączenia z ruchem.
- Źródła i miejsca docelowe ruchu między sieci wirtualnychami, relacje między krytycznymi usługami i aplikacjami biznesowymi.
- Zabezpieczenia, w tym złośliwy ruch, porty otwarte dla Internetu, aplikacje lub maszyny wirtualne próbujące uzyskać dostęp do Internetu.
- Wykorzystanie pojemności, które pomaga wyeliminować problemy dotyczące nadmiernej aprowizacji lub niedostatecznego wykorzystania.

Można nadal korzystać z ustawień diagnostycznych w celu wysyłania dzienników sieciowej grupy zabezpieczeń do Log Analytics tak, aby istniejące zapisane wyszukiwania, alerty i pulpity nawigacyjne nadal działały. Klienci, którzy już zainstalowali rozwiązanie, mogą nadal z niego korzystać do momentu dalszej uwagi. Od 5 września rozwiązanie do analizy grup zabezpieczeń sieci zostanie usunięte z portalu Marketplace i udostępnione przez społeczność jako [szablon szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Operationalinsights).

## <a name="system-center-operations-manager"></a>System Center Operations Manager
Jeśli [Grupa zarządzania Operations Manager została podłączona do log Analytics](../agents/om-agents.md), będzie ona nadal działała bez zmian. W przypadku nowych połączeń należy postępować zgodnie ze wskazówkami w artykule [Microsoft System Center Operations Manager Management Pack, aby skonfigurować pakiet Operations Management Suite](https://techcommunity.microsoft.com/t5/system-center-blog/bg-p/SystemCenterBlog).

## <a name="next-steps"></a>Następne kroki
- Zobacz [często zadawane pytania dotyczące przejścia z portalu pakietu OMS w celu Azure Portal log Analytics użytkowników](../overview.md) w celu uzyskania wskazówek dotyczących przenoszenia z portalu pakietu oms do Azure Portal.