---
title: Włącz Azure Monitor dla jednej maszyny wirtualnej lub zestawu skalowania maszyn wirtualnych w Azure Portal
description: Dowiedz się, jak włączyć Azure Monitor dla maszyn wirtualnych na jednej maszynie wirtualnej platformy Azure lub w zestawie skalowania maszyn wirtualnych przy użyciu Azure Portal.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/27/2020
ms.openlocfilehash: ba6ae9262fb0e55c53e1b1421c075e555fae8d98
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87328365"
---
# <a name="enable-azure-monitor-for-single-virtual-machine-or-virtual-machine-scale-set-in-the-azure-portal"></a>Włącz Azure Monitor dla jednej maszyny wirtualnej lub zestawu skalowania maszyn wirtualnych w Azure Portal
W tym artykule opisano sposób włączania Azure Monitor dla maszyn wirtualnych dla maszyny wirtualnej lub zestawu skalowania maszyn wirtualnych przy użyciu Azure Portal. Ta procedura może być używana w następujących kwestiach:

- Maszyna wirtualna platformy Azure
- Zestaw skalowania maszyn wirtualnych platformy Azure
- Hybrydowa maszyna wirtualna połączona z usługą Azure Arc

## <a name="prerequisites"></a>Wymagania wstępne

- [Utwórz i skonfiguruj obszar roboczy log Analytics](vminsights-configure-workspace.md). Alternatywnie można utworzyć nowy obszar roboczy w trakcie tego procesu.
- Zobacz [obsługiwane systemy operacyjne](vminsights-enable-overview.md#supported-operating-systems) , aby upewnić się, że jest obsługiwany system operacyjny maszyny wirtualnej lub zestawu skalowania maszyn wirtualnych. 

## <a name="enable-azure-monitor-for-vms"></a>Włącz Azure Monitor dla maszyn wirtualnych

Z Azure Portal wybierz pozycję **maszyny wirtualne**, **zestawy skalowania maszyn wirtualnych**lub **maszyny — Azure Arc** i wybierz zasób z listy. W sekcji **monitorowanie** w menu wybierz pozycję **szczegółowe informacje** , a następnie **Włącz**. W poniższym przykładzie przedstawiono maszynę wirtualną platformy Azure, ale menu jest podobne do zestawu skalowania maszyn wirtualnych platformy Azure lub usługi Azure Arc.

![Włączanie Azure Monitor dla maszyn wirtualnych dla maszyny wirtualnej](media/vminsights-enable-single-vm/enable-vminsights-vm-portal.png)

Jeśli maszyna wirtualna nie jest już połączona z obszarem roboczym Log Analytics, zostanie wyświetlony monit o wybranie jednej z nich. Jeśli nie utworzono wcześniej [obszaru roboczego](../../azure-monitor/learn/quick-create-workspace.md), możesz wybrać wartość domyślną dla lokalizacji, w której wdrożono maszynę wirtualną lub zestaw skalowania maszyn wirtualnych w ramach subskrypcji. Ten obszar roboczy zostanie utworzony i skonfigurowany, jeśli jeszcze nie istnieje. W przypadku wybrania istniejącego obszaru roboczego zostanie on skonfigurowany dla Azure Monitor dla maszyn wirtualnych, jeśli nie był jeszcze.

> [!NOTE]
> W przypadku wybrania obszaru roboczego, który nie został wcześniej skonfigurowany do Azure Monitor dla maszyn wirtualnych, do tego obszaru roboczego zostanie dodany pakiet administracyjny *VMInsights* . Ta wartość zostanie zastosowana do każdego agenta, który jest już połączony z obszarem roboczym, niezależnie od tego, czy jest on włączony dla Azure Monitor dla maszyn wirtualnych. Dane dotyczące wydajności będą zbierane z tych maszyn wirtualnych i przechowywane w tabeli *InsightsMetrics* .

![Wybór obszaru roboczego](media/vminsights-configure-workspace/select-workspace.png)

Komunikaty o stanie zostaną odebrane w miarę przeprowadzania konfiguracji.

>[!NOTE]
>Jeśli używasz ręcznego modelu do skalowania maszyn wirtualnych, Uaktualnij wystąpienia, aby zakończyć instalację. Uaktualnienia można uruchomić z poziomu strony **wystąpienia** , w sekcji **Ustawienia** .

![Włącz przetwarzanie wdrożenia monitorowania Azure Monitor dla maszyn wirtualnych](media/vminsights-enable-single-vm/onboard-vminsights-vm-portal-status.png)



## <a name="next-steps"></a>Następne kroki

* Zobacz [Użyj mapy Azure monitor dla maszyn wirtualnych](vminsights-maps.md) , aby wyświetlić odnalezione zależności aplikacji. 
* Zobacz [Wyświetlanie wydajności maszyny wirtualnej platformy Azure](vminsights-performance.md) , aby identyfikować wąskie gardła, ogólne wykorzystanie i wydajność maszyny wirtualnej.
