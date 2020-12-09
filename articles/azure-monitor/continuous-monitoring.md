---
title: Ciągłe monitorowanie za pomocą Azure Monitor | Microsoft Docs
description: W tym artykule opisano poszczególne kroki umożliwiające używanie Azure Monitor w celu zapewnienia ciągłego monitorowania w całym przepływie pracy.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/12/2018
ms.openlocfilehash: 1892afde42ccbcf1c28b7d6a757875772ee3ac5d
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/09/2020
ms.locfileid: "96905608"
---
# <a name="continuous-monitoring-with-azure-monitor"></a>Ciągłe monitorowanie za pomocą Azure Monitor

Ciągłe monitorowanie dotyczy procesu i technologii wymaganych do włączenia monitorowania w każdej fazie cyklu życia DevOps i operacji IT. Pomaga w ciągłym zapewnieniu kondycji, wydajności i niezawodności aplikacji i infrastruktury w miarę przemieszczania się z opracowywania do produkcji. Ciągłe monitorowanie kompiluje koncepcje ciągłej integracji i ciągłego wdrażania (CI/CD), które ułatwiają tworzenie i dostarczanie oprogramowania szybciej i bardziej niezawodnie w celu zapewnienia ciągłej wartości użytkownikom.

[Azure monitor](overview.md) to ujednolicone rozwiązanie do monitorowania na platformie Azure, które zapewnia zauważalność całego stosu między aplikacjami i infrastrukturą w chmurze i lokalnie. Bezproblemowo współpracuje z programem [Visual Studio i Visual Studio Code](https://visualstudio.microsoft.com/) podczas tworzenia i testowania oraz integruje się z [usługą Azure DevOps](/azure/devops/user-guide/index) na potrzeby zarządzania zleceniami i zarządzania elementami roboczymi podczas wdrażania i operacji. Nawet integruje się w narzędziach narzędzia ITSM i SIEM, aby ułatwić śledzenie problemów i zdarzeń w ramach istniejących procesów IT.

W tym artykule opisano poszczególne kroki dotyczące korzystania z Azure Monitor w celu zapewnienia ciągłego monitorowania w ramach przepływów pracy. Zawiera łącza do innej dokumentacji, która zawiera szczegółowe informacje dotyczące implementowania różnych funkcji.


## <a name="enable-monitoring-for-all-your-applications"></a>Włącz monitorowanie dla wszystkich aplikacji
Aby uzyskać wgląd w całe środowisko, należy włączyć monitorowanie wszystkich aplikacji sieci Web i usług. Pozwoli to łatwo wizualizować kompleksowe transakcje i połączenia we wszystkich składnikach.

- [Azure DevOps projects](../devops-project/overview.md) zapewnić uproszczone środowisko pracy z istniejącym kodem i repozytorium Git lub wybrać jedną z przykładowych aplikacji, aby utworzyć potok ciągłej integracji i ciągłego dostarczania na platformie Azure.
- [Ciągłe monitorowanie w potoku wydania DevOps](./app/continuous-monitoring.md) umożliwia bramowanie lub wycofywanie wdrożenia na podstawie danych monitorowania.
- [Monitor stanu](./app/monitor-performance-live-website-now.md)  umożliwia instrumentację na żywo aplikacji .NET w systemie Windows przy użyciu usługi Azure Application Insights, bez konieczności modyfikowania ani ponownego wdrażania kodu.
- Jeśli masz dostęp do kodu aplikacji, Włącz pełne monitorowanie za pomocą [Application Insights](./app/app-insights-overview.md) , instalując zestaw SDK Azure Monitor Application Insights dla [platformy .NET](./app/asp-net.md), [.net core](./app/asp-net-core.md), [Java](./app/java-get-started.md), [Node.js](./learn/nodejs-quick-start.md)lub [innych języków programowania](./app/platforms.md). Pozwala to określić niestandardowe zdarzenia, metryki lub widoki stron, które są istotne dla Twojej aplikacji i Twojej firmy.



## <a name="enable-monitoring-for-your-entire-infrastructure"></a>Włącz monitorowanie całej infrastruktury
Aplikacje są tak niezawodne, jak ich podstawowa infrastruktura. Monitorowanie włączone w całej infrastrukturze pomoże Ci w osiągnięciu pełnego zauważalności i ułatwi odnajdywanie potencjalnej głównej przyczyny, gdy coś się nie powiedzie. Azure Monitor pomaga śledzić kondycję i wydajność całej infrastruktury hybrydowej, w tym takie zasoby, jak maszyny wirtualne, kontenery, magazyn i sieć.

- Na podstawie większości zasobów platformy Azure nie ma konfiguracji, czy są automatycznie uzyskiwane [metryki platformy, dzienniki aktywności i dzienniki diagnostyczne](platform/data-sources.md) .
- Włącz bardziej szczegółowe monitorowanie dla maszyn wirtualnych przy użyciu [Azure monitor dla maszyn wirtualnych](insights/vminsights-overview.md).
-  Włącz bardziej szczegółowe monitorowanie klastrów AKS za pomocą [Azure monitor dla kontenerów](insights/container-insights-overview.md).
- Dodawanie [rozwiązań monitorowania](./monitor-reference.md) dla różnych aplikacji i usług w danym środowisku.


[Infrastruktura jako kod](/azure/devops/learn/what-is-infrastructure-as-code) to zarządzanie infrastrukturą w modelu opisowym przy użyciu tej samej wersji, której zespoły DevOps używają do kodu źródłowego. Zwiększa niezawodność i skalowalność środowiska oraz pozwala korzystać z podobnych procesów, które są używane do zarządzania aplikacjami.

-  Użyj [szablonów Menedżer zasobów](./samples/resource-manager-workspace.md) , aby włączyć monitorowanie i skonfigurować alerty w ramach dużego zestawu zasobów.
- Użyj [Azure Policy](../governance/policy/overview.md) , aby wymusić różne reguły dla zasobów. Gwarantuje to, że te zasoby pozostają zgodne ze standardami firmy i umowami dotyczącymi poziomu usług. 


##  <a name="combine-resources-in-azure-resource-groups"></a>Łączenie zasobów w grupach zasobów platformy Azure
Typowa aplikacja na platformie Azure obejmuje wiele zasobów, takich jak maszyny wirtualne i App Services lub mikrousługi hostowane w Cloud Services, AKS klastrów lub Service Fabric. Aplikacje te często wykorzystują zależności, takie jak Event Hubs, Storage, SQL i Service Bus.

- Połącz zasoby w grupach zasobów platformy Azure, aby uzyskać pełną widoczność wszystkich zasobów tworzących różne aplikacje. [Azure monitor dla grup zasobów](./insights/resource-group-insights.md) to prosty sposób na śledzenie kondycji i wydajności całej aplikacji pełnego stosu oraz umożliwia przechodzenie do szczegółów odpowiednich składników dla dowolnych badań lub debugowania.

## <a name="ensure-quality-through-continuous-deployment"></a>Zapewnianie jakości poprzez ciągłe wdrażanie
Ciągła integracja/ciągłe wdrażanie pozwala na automatyczną integrację i wdrażanie zmian kodu w aplikacji na podstawie wyników zautomatyzowanego testowania. Usprawnia proces wdrażania i zapewnia jakość wszelkich zmian przed ich przejściem do środowiska produkcyjnego.


- Użyj [Azure Pipelines](/azure/devops/pipelines) , aby wdrożyć ciągłe wdrażanie i zautomatyzować cały proces od przekazania kodu do środowiska produkcyjnego na podstawie testów ciągłej integracji/ciągłego dostarczania.
- Używaj [bram jakości](/azure/devops/pipelines/release/approvals/gates) do integrowania monitorowania z wdrożeniem wstępnym lub po wdrożeniu. Gwarantuje to, że spełniasz kluczowe metryki kondycji/wydajności (KPI), ponieważ aplikacje przechodzą z deweloperów do środowiska produkcyjnego, a różnice w środowisku infrastruktury lub skali nie wpływają negatywnie na wskaźniki KPI.
- [Przechowuj oddzielne wystąpienia monitorowania](./app/separate-resources.md) między różnymi środowiskami wdrażania, takimi jak dev, test, Kanaryjskie i prod. Gwarantuje to, że zebrane dane są istotne dla skojarzonych aplikacji i infrastruktury. Aby skorelować dane między środowiskami, można użyć [wykresów wielowymiarowych w Eksplorator metryk](./platform/metrics-charts.md) lub utworzyć [zapytania krzyżowe w Azure monitor](log-query/cross-workspace-query.md).


## <a name="create-actionable-alerts-with-actions"></a>Tworzenie alertów z możliwością działania z akcjami
Krytyczny aspekt monitorowania jest aktywnie powiadamiany administratorów o wszelkich bieżących i przewidywalnych problemach. 

- Tworzenie [alertów w Azure monitor](./platform/alerts-overview.md) na podstawie dzienników i metryk w celu identyfikowania przewidywalnych Stanów niepowodzeń. Należy mieć na celu wykonywanie wszystkich alertów, co oznacza, że reprezentują rzeczywiste krytyczne warunki i starają się zmniejszyć liczbę fałszywie dodatnich. Użyj [progów dynamicznych](platform/alerts-dynamic-thresholds.md) , aby automatycznie obliczać linie bazowe w danych metryk zamiast definiować własne progi statyczne. 
- Zdefiniuj akcje dla alertów, aby używać najbardziej efektywnego sposobu powiadamiania administratorów. Dostępne [akcje dotyczące powiadomień](platform/action-groups.md#create-an-action-group-by-using-the-azure-portal) to wiadomości SMS, wiadomości e-mail, powiadomienia wypychane lub połączenia głosowe.
- Użyj bardziej zaawansowanych akcji, aby [nawiązać połączenie z NARZĘDZIEM narzędzia ITSM](platform/itsmc-overview.md) lub innymi systemami zarządzania alertami za pomocą elementów [webhook](platform/activity-log-alerts-webhook.md).
- Koryguj sytuacje określone w alertach, a także [Azure Automation elementy Runbook](../automation/automation-webhooks.md) lub [Logic Apps](/connectors/custom-connectors/create-webhook-trigger) , które mogą być uruchamiane z poziomu alertu za pomocą elementów webhook. 
- Funkcja automatycznego [skalowania](./learn/tutorial-autoscale-performance-schedule.md) umożliwia dynamiczne zwiększanie i zmniejszanie zasobów obliczeniowych na podstawie zebranych metryk.

## <a name="prepare-dashboards-and-workbooks"></a>Przygotowywanie pulpitów nawigacyjnych i skoroszytów
Upewnienie się, że Twoje programowanie i operacje mają dostęp do tych samych danych telemetrii i narzędzi, pozwala im wyświetlać wzorce w całym środowisku i zminimalizować średni czas wykrywania (MTTD) oraz średni czas przywracania (MTTR).

- Przygotuj [niestandardowe pulpity nawigacyjne](./learn/tutorial-app-dashboards.md) oparte na typowych metrykach i dziennikach dla różnych ról w organizacji. Pulpity nawigacyjne mogą łączyć dane ze wszystkich zasobów platformy Azure.
- Przygotuj [skoroszyty](./platform/workbooks-overview.md) , aby zapewnić udostępnianie wiedzy między programowaniem i operacjami. Mogą one być przygotowane jako dynamiczne raporty z wykresami metryk i dziennikami zapytań, a nawet jako przewodniki dotyczące rozwiązywania problemów przygotowane przez deweloperów pomagają pracownikom działu pomocy technicznej lub operacji w celu obsługi podstawowych problemów.

## <a name="continuously-optimize"></a>Ciągłe Optymalizowanie
 Monitorowanie to jeden z podstawowych aspektów popularnej funkcji budowania-miary, która zaleca ciągłe śledzenie kluczowych wskaźników wydajności i metryk zachowania użytkowników, a następnie striving je w celu optymalizacji za pomocą iteracji planowania. Azure Monitor ułatwia zbieranie metryk i dzienników związanych z Twoją firmą oraz dodawanie nowych punktów danych w następnym wdrożeniu zgodnie z potrzebami.

- Użyj narzędzi w Application Insights, aby [śledzić zachowanie i zaangażowanie użytkowników końcowych](./learn/tutorial-users.md).
- Użyj [analizy wpływu](./app/usage-impact.md) , aby określić priorytety obszarów, na których należy skupić się na dysku, na ważne wskaźniki KPI.


## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o składnikach różnic [Azure monitor](overview.md).
- [Dodawanie ciągłego monitorowania](./app/continuous-monitoring.md) do potoku wydania.