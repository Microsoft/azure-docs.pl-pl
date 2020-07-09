---
title: Włącz Azure Monitor dla maszyn wirtualnych w Azure Portal
description: Dowiedz się, jak oszacować Azure Monitor dla maszyn wirtualnych na jednej maszynie wirtualnej platformy Azure lub w zestawie skalowania maszyn wirtualnych.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/25/2020
ms.openlocfilehash: 4cdb9390b3146df74f2cbe8eba7b170a5d11fb2c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85507062"
---
# <a name="enable-azure-monitor-for-single-vm-or-vmss-in-the-azure-portal"></a>Włącz Azure Monitor dla pojedynczej maszyny wirtualnej lub VMSS w Azure Portal
W tym artykule opisano sposób włączania Azure Monitor dla maszyn wirtualnych dla jednej maszyny wirtualnej lub zestawu skalowania maszyn wirtualnych przy użyciu Azure Portal. Ta procedura może być używana w następujących kwestiach:

- Maszyna wirtualna platformy Azure
- Zestaw skalowania maszyn wirtualnych platformy Azure
- Maszyna Azure Arc

Przed rozpoczęciem Sprawdź [wymagania wstępne](vminsights-enable-overview.md) i upewnij się, że Twoja subskrypcja i zasoby spełniają wymagania.  

## <a name="enable-azure-monitor-for-vms"></a>Włącz Azure Monitor dla maszyn wirtualnych

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. Wybierz kolejno pozycje **maszyny wirtualne**, **zestawy skalowania maszyn wirtualnych**lub **maszyny — Azure Arc**.

1. Wybierz zasób z listy.

1. W sekcji **monitorowanie** w menu wybierz pozycję **szczegółowe informacje** , a następnie **Włącz**. W poniższym przykładzie przedstawiono maszynę wirtualną platformy Azure, ale menu jest podobne do usługi Azure VMSS lub Azure Arc.

    ![Włączanie Azure Monitor dla maszyn wirtualnych dla maszyny wirtualnej](media/vminsights-enable-single-vm/enable-vminsights-vm-portal.png)

1. Jeśli maszyna wirtualna nie jest już połączona z obszarem roboczym Log Analytics, zostanie wyświetlony monit o wybranie jednej z nich. Jeśli nie utworzono wcześniej [obszaru roboczego](../../azure-monitor/learn/quick-create-workspace.md), możesz wybrać wartość domyślną dla lokalizacji, w której WDROŻONo maszynę wirtualną lub VMSS. Ten obszar roboczy zostanie utworzony i skonfigurowany, jeśli jeszcze nie istnieje.

2. Komunikaty o stanie zostaną odebrane w miarę przeprowadzania konfiguracji.

    >[!NOTE]
    >W przypadku korzystania z modelu skalowania ręcznego należy uaktualnić wystąpienia, aby zakończyć instalację. Uaktualnienia można uruchomić z poziomu strony **wystąpienia** , w sekcji **Ustawienia** .

    ![Włącz przetwarzanie wdrożenia monitorowania Azure Monitor dla maszyn wirtualnych](media/vminsights-enable-single-vm/onboard-vminsights-vm-portal-status.png)



## <a name="next-steps"></a>Następne kroki

* Aby wyświetlić odnalezione zależności aplikacji, zobacz [Use Azure monitor dla maszyn wirtualnych map](vminsights-maps.md). 
* Aby identyfikować wąskie gardła, ogólne wykorzystanie i wydajność maszyny wirtualnej, zobacz [Wyświetlanie wydajności maszyny wirtualnej platformy Azure](vminsights-performance.md).
