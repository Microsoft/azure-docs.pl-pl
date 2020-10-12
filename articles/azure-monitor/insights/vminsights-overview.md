---
title: Co to jest Azure Monitor dla maszyn wirtualnych?
description: Przegląd Azure Monitor dla maszyn wirtualnych, który służy do monitorowania kondycji i wydajności maszyn wirtualnych platformy Azure oraz automatycznego odnajdywania i mapowania składników aplikacji oraz ich zależności.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/22/2020
ms.openlocfilehash: 5c3cb13d0b2da9370f402083d82397679f2c9343
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89022500"
---
# <a name="what-is-azure-monitor-for-vms"></a>Co to jest Azure Monitor dla maszyn wirtualnych?

Azure Monitor dla maszyn wirtualnych monitoruje wydajność i kondycję maszyn wirtualnych i zestawów skalowania maszyn wirtualnych, w tym ich uruchomione procesy i zależności od innych zasobów. Może ona pomóc zapewnić przewidywalną wydajność i dostępność najważniejszych aplikacji, identyfikując wąskie gardła wydajności i problemy z siecią, a także pomaga zrozumieć, czy problem jest związany z innymi zależnościami.

Azure Monitor dla maszyn wirtualnych obsługuje systemy operacyjne Windows i Linux w następujący sposób:

- Maszyny wirtualne platformy Azure
- Zestawy skalowania maszyn wirtualnych platformy Azure
- Hybrydowe maszyny wirtualne połączone z usługą Azure Arc
- Lokalne maszyny wirtualne
- Maszyny wirtualne hostowane w innym środowisku chmury
  



Azure Monitor dla maszyn wirtualnych przechowuje dane w dziennikach Azure Monitor, co pozwala na dostarczanie zaawansowanej agregacji i filtrowania oraz analizowanie trendów danych w miarę upływu czasu. Możesz wyświetlić te dane bezpośrednio z maszyny wirtualnej lub użyć Azure Monitor, aby dostarczyć Zagregowany widok wielu maszyn wirtualnych.

![Perspektywa usługi Virtual Machine Insights w Azure Portal](media/vminsights-overview/vminsights-azmon-directvm.png)


## <a name="pricing"></a>Cennik
Nie ma bezpośredniego kosztu Azure Monitor dla maszyn wirtualnych, ale opłaty są naliczane za aktywność w obszarze roboczym Log Analytics. W oparciu o Cennik opublikowany na [stronie cennika Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/)Azure monitor dla maszyn wirtualnych opłaty są naliczane za:

- Dane pozyskane z agentów i przechowywane w obszarze roboczym.
- Reguły alertów na podstawie danych dziennika i kondycji.
- Powiadomienia wysyłane z reguł alertów.

Rozmiar dziennika zmienia się w zależności od długości ciągu liczników wydajności i może zwiększyć liczbę dysków logicznych i kart sieciowych przypisanych do maszyny wirtualnej. Jeśli już używasz Service Map, jedyną zmianą, która zobaczysz, jest dodatkowe dane wydajności wysyłane do Azure Monitor `InsightsMetrics` typu danych.


## <a name="configuring-azure-monitor-for-vms"></a>Konfigurowanie Azure Monitor dla maszyn wirtualnych
Poniżej przedstawiono procedurę konfigurowania Azure Monitor dla maszyn wirtualnych. Aby zapoznać się ze szczegółowymi wskazówkami dotyczącymi poszczególnych kroków, należy wykonać następujące czynności:

- [Utwórz obszar roboczy Log Analytics.](vminsights-configure-workspace.md#create-log-analytics-workspace)
- [Dodaj rozwiązanie VMInsights do obszaru roboczego.](vminsights-configure-workspace.md#add-vminsights-solution-to-workspace)
- [Zainstaluj agentów na maszynie wirtualnej i zestawie skalowania maszyn wirtualnych do monitorowania.](vminsights-enable-overview.md)



## <a name="next-steps"></a>Następne kroki

- Aby włączyć monitorowanie dla maszyn wirtualnych, zobacz temat [wdrażanie Azure monitor dla maszyn wirtualnych](vminsights-enable-overview.md) .

