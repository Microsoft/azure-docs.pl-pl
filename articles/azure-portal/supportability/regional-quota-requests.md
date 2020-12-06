---
title: Poproś o zwiększenie limitów przydziału regionalnych vCPU platformy Azure
description: Jak zażądać zwiększenia limitu przydziału vCPU dla regionu w Azure Portal.
author: sowmyavenkat86
ms.author: svenkat
ms.date: 01/27/2020
ms.topic: how-to
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: eadf740c6b5caccbf678a1238f993d4ec0b34095
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/06/2020
ms.locfileid: "96745421"
---
# <a name="standard-quota-increase-limits-by-region"></a>Przydział standardowy: Zwiększ limity według regionu

Azure Resource Manager obsługuje dwa typy przydziałów vCPU dla maszyn wirtualnych:

* *Maszyny wirtualne z opcją płatność zgodnie z rzeczywistym* użyciem i *zarezerwowane wystąpienia maszyn wirtualnych* podlegają *standardowym limitowi przydziału vCPU*.
* *Dodatkowe maszyny wirtualne* podlegają *limitowi przydziału vCPU*.

Standardowy przydział vCPU dla wystąpień z opcją płatność zgodnie z rzeczywistym użyciem i zarezerwowanych maszyn wirtualnych jest wymuszany w dwóch warstwach dla każdej subskrypcji w każdym regionie:

* Pierwsza warstwa to *całkowity regionalny limit procesorów wirtualnych vCPU* na wszystkich seriach maszyn wirtualnych.
* Druga warstwa to *Limit procesorów wirtualnych vCPU serii dla maszyn wirtualnych*, taki jak procesorów wirtualnych vCPU serii D.

Za każdym razem, gdy wdrażasz nową maszynę wirtualną, całkowite i istniejące użycie vCPU dla tej serii maszyn wirtualnych nie mogą przekroczyć zatwierdzonego limitu przydziału vCPU dla danej serii maszyn wirtualnych. Ponadto całkowita liczba nowych i istniejących procesorów wirtualnych vCPU, które są wdrożone na wszystkich seriach maszyn wirtualnych, nie powinna przekraczać całkowitego zatwierdzonego przydziału regionalnego vCPU dla subskrypcji. Jeśli jeden z tych limitów przydziału zostanie przekroczony, wdrożenie maszyny wirtualnej jest niedozwolone.

Można zażądać zwiększenia limitu przydziału vCPU dla serii maszyn wirtualnych przy użyciu Azure Portal. Zwiększenie limitu przydziału serii maszyn wirtualnych powoduje automatyczne zwiększenie łącznej granicy vCPU w regionie o tej samej wartości.

Gdy tworzysz nową subskrypcję, domyślną łączną liczbą procesorów wirtualnych vCPU regionalnych może być nie równa całkowity limit przydziału vCPU dla wszystkich poszczególnych serii maszyn wirtualnych. Ta niezgodność może skutkować subskrypcją z wystarczającym limitem przydziału dla poszczególnych serii maszyn wirtualnych, które mają zostać wdrożone. Jednak może nie być wystarczający limit przydziału, aby pomieścić łączną procesorów wirtualnych vCPU regionalną dla wszystkich wdrożeń. W takim przypadku należy przesłać żądanie, aby jawnie zwiększyć limit łącznej liczby procesorów wirtualnych vCPU regionalnych. Łączny limit vCPU regionalnych nie może przekroczyć całkowitego zatwierdzonego przydziału dla całej serii maszyn wirtualnych dla regionu.

Aby dowiedzieć się więcej na temat standardowych przydziałów usługi vCPU, zobacz limity przydziału i usługi [Virtual Machine vCPU](../../virtual-machines/windows/quotas.md) oraz [limitów subskrypcji i usług Azure, przydziałów i ograniczeń](../../azure-resource-manager/management/azure-subscription-service-limits.md).

Aby dowiedzieć się więcej o zwiększaniu limitów vCPU maszyn wirtualnych, zobacz limit [przydziału miejsca na miejscu: zwiększenie limitów dla całej serii maszyn wirtualnych](low-priority-quota.md).

Możesz poprosić o zwiększenie limitu przydziału vCPU standardowego według regionu na dwa sposoby.

## <a name="request-a-quota-increase-by-region-from-help--support"></a>Poproś o zwiększenie limitu przydziału w regionie od pomocy i obsługi technicznej

Aby zażądać zwiększenia przydziału vCPU przez region z **pomocy i obsługi technicznej**:

1. Z menu [Azure Portal](https://portal.azure.com) wybierz pozycję **Pomoc i obsługa techniczna**.

   ![Link "pomoc + obsługa techniczna"](./media/resource-manager-core-quotas-request/help-plus-support.png)

1. W oknie **Pomoc i obsługa techniczna** wybierz pozycję **nowe żądanie obsługi**.

    ![Nowy wniosek o pomoc techniczną](./media/resource-manager-core-quotas-request/new-support-request.png)

1. W obszarze **typ problemu** wybierz pozycję **usługi i limity subskrypcji (przydziały)**.

   ![Wybierz typ problemu](./media/resource-manager-core-quotas-request/select-quota-issue-type.png)

1. W obszarze **subskrypcja** wybierz subskrypcję, której przydział chcesz zwiększyć.

   ![Wybieranie subskrypcji](./media/resource-manager-core-quotas-request/select-subscription-support-request.png)

1. W polu **Typ limitu przydziału** wybierz pozycję **inne żądania**.

   ![Wybierz typ limitu przydziału](./media/resource-manager-core-quotas-request/regional-quotatype.png)

1. Wybierz pozycję **Dalej: rozwiązania** , aby otworzyć **Szczegóły problemu**. W polu **Opis** podaj następujące informacje:

    1. W obszarze **model wdrażania** Określ **Menedżer zasobów**.  
    1. W **obszarze region** określ wymagany region, na przykład **Wschodnie stany USA 2**.  
    1. W polu **nowy limit** określ nowy limit vCPU dla regionu. Ta wartość nie powinna przekraczać sumy zatwierdzonych przydziałów dla poszczególnych serii SKU dla tej subskrypcji.

    ![Wprowadź szczegóły żądania limitu przydziału](./media/resource-manager-core-quotas-request/regional-details.png)

1. Wybierz pozycję **Recenzja + Utwórz** , aby kontynuować tworzenie żądania obsługi.

## <a name="request-a-quota-increase-by-region-from-subscriptions"></a>Poproś o zwiększenie limitu przydziału według regionu z subskrypcji

Aby zażądać zwiększenia przydziału vCPU przez region z **subskrypcji**:

1. W [Azure Portal](https://portal.azure.com)Wyszukaj i wybierz pozycję **subskrypcje**.

   ![Przejdź do pozycji subskrypcje w Azure Portal](./media/resource-manager-core-quotas-request/search-for-subscriptions.png)

1. Wybierz subskrypcję, której przydział chcesz zwiększyć.

   ![Wybierz subskrypcję do zmodyfikowania](./media/resource-manager-core-quotas-request/select-subscription-change-quota.png)

1. W lewym okienku wybierz pozycję **użycie i przydziały**.

   ![Śledź linki użycia i przydziałów](./media/resource-manager-core-quotas-request/select-usage-plus-quotas.png)

1. W prawym górnym rogu wybierz pozycję **Zwiększ liczbę żądań**.

   ![Wybierz, aby zwiększyć limit przydziału](./media/resource-manager-core-quotas-request/request-increase-from-subscription.png)

1. W obszarze **typ przydziału** wybierz pozycję **inne żądania**.

   ![Wybierz typ limitu przydziału](./media/resource-manager-core-quotas-request/regional-quotatype.png)

1. Wybierz pozycję **Dalej: rozwiązania** , aby otworzyć **Szczegóły problemu**. W polu **Opis** podaj następujące informacje dodatkowe:

    1. W obszarze **model wdrażania** Określ **Menedżer zasobów**.  
    1. W **obszarze region** określ wymagany region, na przykład **Wschodnie stany USA 2**.  
    1. W polu **nowy limit** określ nowy limit vCPU dla regionu. Ta wartość nie powinna przekraczać sumy zatwierdzonych przydziałów dla poszczególnych serii SKU dla tej subskrypcji.

    ![Wprowadź informacje w obszarze Szczegóły](./media/resource-manager-core-quotas-request/regional-details.png)

1. Wybierz pozycję **Recenzja + Utwórz** , aby kontynuować tworzenie żądania obsługi.
