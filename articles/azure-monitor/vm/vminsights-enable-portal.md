---
title: Włącz Azure Monitor dla jednej maszyny wirtualnej lub zestawu skalowania maszyn wirtualnych w Azure Portal
description: Dowiedz się, jak włączyć szczegółowe informacje o maszynie wirtualnej na jednej maszynie wirtualnej platformy Azure lub w zestawie skalowania maszyn wirtualnych przy użyciu Azure Portal.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/27/2020
ms.openlocfilehash: 076fcab7b0747a7993407edd65f9d08efc27309f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102035589"
---
# <a name="enable-azure-monitor-for-single-virtual-machine-or-virtual-machine-scale-set-in-the-azure-portal"></a>Włącz Azure Monitor dla jednej maszyny wirtualnej lub zestawu skalowania maszyn wirtualnych w Azure Portal
W tym artykule opisano sposób włączania usługi VM Insights dla maszyny wirtualnej lub zestawu skalowania maszyn wirtualnych przy użyciu Azure Portal. Ta procedura może być używana w następujących kwestiach:

- Maszyna wirtualna platformy Azure
- Zestaw skalowania maszyn wirtualnych platformy Azure
- Hybrydowa maszyna wirtualna połączona z usługą Azure Arc

## <a name="prerequisites"></a>Wymagania wstępne

- [Utwórz i skonfiguruj obszar roboczy log Analytics](./vminsights-configure-workspace.md). Alternatywnie można utworzyć nowy obszar roboczy w trakcie tego procesu.
- Zobacz [obsługiwane systemy operacyjne](./vminsights-enable-overview.md#supported-operating-systems) , aby upewnić się, że jest obsługiwany system operacyjny maszyny wirtualnej lub zestawu skalowania maszyn wirtualnych. 

## <a name="enable-vm-insights"></a>Włącz szczegółowe informacje o maszynie wirtualnej

Z Azure Portal wybierz pozycję **maszyny wirtualne**, **zestawy skalowania maszyn wirtualnych** lub **serwery — Azure Arc** i wybierz zasób z listy. W sekcji **monitorowanie** w menu wybierz pozycję **szczegółowe informacje** , a następnie **Włącz**. W poniższym przykładzie przedstawiono maszynę wirtualną platformy Azure, ale menu jest podobne do zestawu skalowania maszyn wirtualnych platformy Azure lub usługi Azure Arc.

![Włączanie usługi VM Insights dla maszyny wirtualnej](media/vminsights-enable-portal/enable-vminsights-vm-portal.png)

Jeśli maszyna wirtualna nie jest już połączona z obszarem roboczym Log Analytics, zostanie wyświetlony monit o wybranie jednej z nich. Jeśli nie utworzono wcześniej [obszaru roboczego](../logs/quick-create-workspace.md), możesz wybrać wartość domyślną dla lokalizacji, w której wdrożono maszynę wirtualną lub zestaw skalowania maszyn wirtualnych w ramach subskrypcji. Ten obszar roboczy zostanie utworzony i skonfigurowany, jeśli jeszcze nie istnieje. Jeśli wybierzesz istniejący obszar roboczy, zostanie on skonfigurowany do wglądu w informacje o maszynie wirtualnej, jeśli nie jest jeszcze.

> [!NOTE]
> W przypadku wybrania obszaru roboczego, który nie został wcześniej skonfigurowany do wglądu w dane maszyny wirtualnej, do tego obszaru roboczego zostanie dodany pakiet administracyjny *VMInsights* . Ta wartość zostanie zastosowana do każdego agenta, który jest już połączony z obszarem roboczym, niezależnie od tego, czy jest on włączony do wglądu w dane maszyn wirtualnych. Dane dotyczące wydajności będą zbierane z tych maszyn wirtualnych i przechowywane w tabeli *InsightsMetrics* .

![Wybór obszaru roboczego](media/vminsights-enable-portal/select-workspace.png)

Komunikaty o stanie zostaną odebrane w miarę przeprowadzania konfiguracji.

>[!NOTE]
>Jeśli używasz ręcznego modelu do skalowania maszyn wirtualnych, Uaktualnij wystąpienia, aby zakończyć instalację. Uaktualnienia można uruchomić z poziomu strony **wystąpienia** , w sekcji **Ustawienia** .

![Włącz przetwarzanie wdrożenia monitorowania w usłudze VM Insights](media/vminsights-enable-portal/onboard-vminsights-vm-portal-status.png)



## <a name="next-steps"></a>Następne kroki

* Zobacz [Używanie mapy usługi VM Insights](vminsights-maps.md) , aby wyświetlić odnalezione zależności aplikacji. 
* Zobacz [Wyświetlanie wydajności maszyny wirtualnej platformy Azure](vminsights-performance.md) , aby identyfikować wąskie gardła, ogólne wykorzystanie i wydajność maszyny wirtualnej.
