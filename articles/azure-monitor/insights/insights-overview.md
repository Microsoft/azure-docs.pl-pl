---
title: Przegląd szczegółowych informacji w Azure Monitor | Microsoft Docs
description: Szczegółowe informacje zapewniają dostosowane środowisko monitorowania w Azure Monitor dla określonych aplikacji i usług. Ten artykuł zawiera krótki opis wszystkich dostępnych obecnie informacji.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/22/2019
ms.openlocfilehash: e3eefb97c85b3865abbf116b9912dc45f6db8ce8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87326872"
---
# <a name="overview-of-insights-in-azure-monitor"></a>Przegląd szczegółowych informacji w Azure Monitor
Szczegółowe informacje zapewniają dostosowane środowisko monitorowania dla określonych aplikacji i usług. Przechowują one dane na [platformie danych Azure monitor](../platform/data-platform.md) i korzystają z innych funkcji Azure monitor do analizy i tworzenia alertów, ale mogą zbierać dodatkowe dane i zapewniać unikatowe środowisko użytkownika w Azure Portal. Uzyskaj dostęp do szczegółowych informacji z sekcji **Insights** w menu Azure Monitor w Azure Portal.


Poniższe sekcje zawierają Krótki opis szczegółowych informacji, które są obecnie dostępne w Azure Monitor. Aby uzyskać szczegółowe informacje na temat każdej z nich, zobacz szczegółową dokumentację.

## <a name="application-insights"></a>Application Insights
Usługa Application Insights jest rozszerzalną usługą zarządzania wydajnością aplikacji (APM) dla deweloperów sieci Web na wielu platformach. Użyj tej usługi do monitorowania aplikacji internetowej na żywo. Działa w przypadku aplikacji na różnych platformach, w tym .NET, Node.js i Java EE, hostowanych lokalnie, hybrydowych lub dowolnej chmurze publicznej. Integruje się ona również z procesem DevOps i ma punkty połączenia z różnymi narzędziami programistycznymi.

Zobacz [co to jest Application Insights?](../app/app-insights-overview.md).

![Application Insights](media/insights-overview/app-insights.png)

## <a name="azure-monitor-for-containers"></a>Usługa Azure Monitor dla kontenerów
Azure Monitor dla kontenerów monitoruje wydajność obciążeń kontenera wdrożonych w ramach Azure Container Instances lub zarządzanych klastrów Kubernetes hostowanych w usłudze Azure Kubernetes Service (AKS). Monitorowanie kontenerów ma krytyczne znaczenie, szczególnie w przypadku korzystania z klastra produkcyjnego w odpowiedniej skali z wieloma aplikacjami.

Zapoznaj się z tematem [Azure monitor for Containers — Omówienie](./container-insights-overview.md).

![Usługa Azure Monitor dla kontenerów](media/insights-overview/container-insights.png)

## <a name="azure-monitor-for-resource-groups-preview"></a>Azure Monitor dla grup zasobów (wersja zapoznawcza)
Azure Monitor dla grup zasobów ułatwia Klasyfikacja i diagnozowanie problemów występujących w poszczególnych zasobach, a także w kontekście kondycji i wydajności grupy zasobów jako całości.

Zobacz [monitorowanie grup zasobów przy użyciu Azure monitor (wersja zapoznawcza)](./resource-group-insights.md).

![Azure Monitor dla grup zasobów](media/insights-overview/resource-group-insights.png)

## <a name="azure-monitor-for-vms-preview"></a>Azure Monitor dla maszyn wirtualnych (wersja zapoznawcza)
Usługa Azure Monitor dla maszyn wirtualnych monitoruje Twoje maszyny wirtualne platformy Azure oraz zestawy skalowania maszyn wirtualnych na dużą skalę. Analizuje ona wydajność i kondycję Twoich maszyn wirtualnych z systemami Windows i Linux oraz monitoruje ich procesy i zależności od innych zasobów oraz procesów zewnętrznych.

Zobacz [co to jest Azure monitor dla maszyn wirtualnych?](vminsights-overview.md)

![Usługa Azure Monitor dla maszyn wirtualnych](media/insights-overview/vm-insights.png)

## <a name="azure-monitor-for-networks-preview"></a>Azure Monitor dla sieci (wersja zapoznawcza)
[Azure monitor dla sieci](network-insights-overview.md) zapewnia kompleksowy wgląd w kondycję i metryki dla wszystkich zasobów sieciowych. Funkcja wyszukiwania zaawansowanego pomaga identyfikować zależności zasobów i umożliwiać scenariusze, takie jak identyfikowanie zasobów, które obsługują witrynę sieci Web, przez zwykłe wyszukiwanie nazwy witryny sieci Web.

![Usługa Azure Monitor dla sieci](media/insights-overview/network-insights.png)

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o [platformie danych Azure monitor](../platform/data-platform.md) wykorzystywanej przez usługi Insights.
* Poznaj różne [źródła danych używane przez Azure monitor](../platform/data-sources.md) i różne rodzaje danych zbieranych przez poszczególne szczegółowe dane.

