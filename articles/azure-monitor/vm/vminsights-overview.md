---
title: Co to jest maszyna wirtualna Insights?
description: Omówienie usługi VM Insights, która monitoruje kondycję i wydajność maszyn wirtualnych platformy Azure oraz automatycznie odnajduje i mapuje składniki aplikacji oraz ich zależności.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/22/2020
ms.openlocfilehash: 18e1fdcdee347a057c452f6170f36ec7f1f43244
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102046418"
---
# <a name="overview-of-vm-insights"></a>Przegląd szczegółowych informacji o maszynie wirtualnej

Program VM Insights monitoruje wydajność i kondycję maszyn wirtualnych i zestawów skalowania maszyn wirtualnych, w tym ich uruchomione procesy i zależności od innych zasobów. Może ona pomóc zapewnić przewidywalną wydajność i dostępność najważniejszych aplikacji, identyfikując wąskie gardła wydajności i problemy z siecią, a także pomaga zrozumieć, czy problem jest związany z innymi zależnościami.

Usługi VM Insights obsługują systemy operacyjne Windows i Linux na następujących komputerach:

- Maszyny wirtualne platformy Azure
- Zestawy skalowania maszyn wirtualnych platformy Azure
- Hybrydowe maszyny wirtualne połączone z usługą Azure Arc
- Lokalne maszyny wirtualne
- Maszyny wirtualne hostowane w innym środowisku chmury
  

Usługi VM Insights przechowują swoje dane w dziennikach Azure Monitor, co pozwala na dostarczanie zaawansowanej agregacji i filtrowania oraz analizowanie trendów danych w miarę upływu czasu. Możesz wyświetlić te dane bezpośrednio z maszyny wirtualnej lub użyć Azure Monitor, aby dostarczyć Zagregowany widok wielu maszyn wirtualnych.

![Perspektywa usługi Virtual Machine Insights w Azure Portal](media/vminsights-overview/vminsights-azmon-directvm.png)


## <a name="pricing"></a>Ceny
Nie ma bezpośredniego kosztu do wglądu w dane maszyn wirtualnych, ale opłaty są naliczane za działanie w obszarze roboczym Log Analytics. W oparciu o Cennik opublikowany na [stronie cennika Azure monitor](https://azure.microsoft.com/pricing/details/monitor/), usługi VM Insights są rozliczane za:

- Dane pozyskane z agentów i przechowywane w obszarze roboczym.
- Dane stanu kondycji zbierane z kondycji gościa (wersja zapoznawcza)
- Reguły alertów na podstawie danych dziennika i kondycji.
- Powiadomienia wysyłane z reguł alertów.

Rozmiar dziennika zmienia się w zależności od długości ciągu liczników wydajności i może zwiększyć liczbę dysków logicznych i kart sieciowych przypisanych do maszyny wirtualnej. Jeśli już używasz Service Map, jedyną zmianą, która zobaczysz, jest dodatkowe dane wydajności, które są wysyłane do Azure Monitor `InsightsMetrics` typu danych.


## <a name="configuring-vm-insights"></a>Konfigurowanie usługi VM Insights
Poniżej przedstawiono procedurę konfigurowania szczegółowych informacji o maszynach wirtualnych. Aby zapoznać się ze szczegółowymi wskazówkami dotyczącymi poszczególnych kroków, należy wykonać następujące czynności:

- [Utwórz obszar roboczy Log Analytics.](./vminsights-configure-workspace.md#create-log-analytics-workspace)
- [Dodaj rozwiązanie VMInsights do obszaru roboczego.](./vminsights-configure-workspace.md#add-vminsights-solution-to-workspace)
- [Zainstaluj agentów na maszynie wirtualnej i zestawie skalowania maszyn wirtualnych do monitorowania.](./vminsights-enable-overview.md)



## <a name="next-steps"></a>Następne kroki

- Zobacz [wdrażanie usługi VM Insights](./vminsights-enable-overview.md) , aby poznać wymagania i metody umożliwiające monitorowanie maszyn wirtualnych.
