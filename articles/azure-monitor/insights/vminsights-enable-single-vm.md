---
title: Włącz Azure Monitor dla maszyn wirtualnych w Azure Portal
description: Dowiedz się, jak oszacować Azure Monitor dla maszyn wirtualnych na jednej maszynie wirtualnej platformy Azure lub w zestawie skalowania maszyn wirtualnych.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2020
ms.openlocfilehash: 45bc8f16a547d4a95820f9dcd02132844b3be83c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79480712"
---
# <a name="enable-azure-monitor-for-vms-in-the-azure-portal"></a>Włącz Azure Monitor dla maszyn wirtualnych w Azure Portal

W tym artykule opisano sposób włączania Azure Monitor dla maszyn wirtualnych na niewielkiej liczbie maszyn wirtualnych platformy Azure przy użyciu Azure Portal. Twoje cele polegają na monitorowaniu maszyn wirtualnych i znalezieniu problemów z wydajnością lub dostępnością. 

Przed rozpoczęciem Sprawdź [wymagania wstępne](vminsights-enable-overview.md) i upewnij się, że Twoja subskrypcja i zasoby spełniają wymagania.  

## <a name="enable-monitoring-for-a-single-azure-vm"></a>Włączanie monitorowania pojedynczej maszyny wirtualnej platformy Azure
Aby włączyć monitorowanie maszyny wirtualnej platformy Azure:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. Wybierz **Virtual Machines**.

1. Z listy wybierz maszynę wirtualną.

1. Na stronie maszyna wirtualna w sekcji **monitorowanie** wybierz pozycję **szczegółowe informacje** , a następnie **Włącz**.

    ![Włączanie Azure Monitor dla maszyn wirtualnych dla maszyny wirtualnej](media/vminsights-enable-single-vm/enable-vminsights-vm-portal.png)

1. Na stronie Dołączanie do usługi **Azure monitor Insights** , jeśli masz istniejący obszar roboczy log Analytics w tej samej subskrypcji, wybierz go na liście rozwijanej.  

    Na tej liście jest wybierany domyślny obszar roboczy i lokalizacja, w której wdrożono maszynę wirtualną w ramach subskrypcji. 

    >[!NOTE]
    >Aby utworzyć nowy obszar roboczy Log Analytics do przechowywania danych monitorowania z maszyny wirtualnej, zobacz [tworzenie log Analytics obszaru roboczego](../../azure-monitor/learn/quick-create-workspace.md). Obszar roboczy Log Analytics musi należeć do jednego z [obsługiwanych regionów](vminsights-enable-overview.md#log-analytics).

6. Komunikaty o stanie zostaną odebrane w miarę przeprowadzania konfiguracji.

    ![Włącz przetwarzanie wdrożenia monitorowania Azure Monitor dla maszyn wirtualnych](media/vminsights-enable-single-vm/onboard-vminsights-vm-portal-status.png)

## <a name="enable-monitoring-for-a-single-virtual-machine-scale-set"></a>Włącz monitorowanie dla pojedynczego zestawu skalowania maszyn wirtualnych

Aby włączyć monitorowanie zestawu skalowania maszyn wirtualnych platformy Azure:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. Wybierz **Virtual Machine Scale Sets**.

3. Z listy wybierz zestaw skalowania maszyn wirtualnych.

4. Na stronie zestaw skalowania maszyn wirtualnych w sekcji **monitorowanie** wybierz pozycję **szczegółowe informacje** , a następnie **Włącz**.

5. Na stronie **szczegółowe informacje** Jeśli chcesz użyć istniejącego obszaru roboczego log Analytics, wybierz go na liście rozwijanej.

    Na tej liście jest wybierany domyślny obszar roboczy i lokalizacja, w ramach której wdrożono maszynę wirtualną. 

    ![Włączanie Azure Monitor dla maszyn wirtualnych na potrzeby zestawu skalowania maszyn wirtualnych](media/vminsights-enable-single-vm/enable-vminsights-vmss-portal.png)

    >[!NOTE]
    >Aby utworzyć nowy obszar roboczy Log Analytics do przechowywania danych monitorowania z zestawu skalowania maszyn wirtualnych, zobacz [tworzenie log Analytics obszaru roboczego](../learn/quick-create-workspace.md). Obszar roboczy Log Analytics musi należeć do jednego z [obsługiwanych regionów](vminsights-enable-overview.md#log-analytics).

6. Komunikaty o stanie zostaną odebrane w miarę przeprowadzania konfiguracji.

    >[!NOTE]
    >W przypadku korzystania z modelu skalowania ręcznego należy uaktualnić wystąpienia, aby zakończyć instalację. Uaktualnienia można uruchomić z poziomu strony **wystąpienia** , w sekcji **Ustawienia** .
    
    ![Włącz przetwarzanie wdrożenia monitorowania Azure Monitor dla maszyn wirtualnych](media/vminsights-enable-single-vm/onboard-vminsights-vmss-portal-status.png)

Po włączeniu monitorowania dla maszyny wirtualnej lub zestawu skalowania maszyn wirtualnych informacje o monitorowaniu są dostępne do analizy w Azure Monitor dla maszyn wirtualnych. 

## <a name="next-steps"></a>Następne kroki

* Aby wyświetlić odnalezione zależności aplikacji, zobacz [Use Azure monitor dla maszyn wirtualnych map](vminsights-maps.md). 
* Aby identyfikować wąskie gardła, ogólne wykorzystanie i wydajność maszyny wirtualnej, zobacz [Wyświetlanie wydajności maszyny wirtualnej platformy Azure](vminsights-performance.md).
