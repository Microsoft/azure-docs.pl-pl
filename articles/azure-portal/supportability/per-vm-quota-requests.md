---
title: Poproś o zwiększenie limitów przydziału vCPU na serię maszyn wirtualnych platformy Azure
description: Jak zażądać zwiększenia limitu przydziału vCPU dla serii maszyn wirtualnych w Azure Portal, co zwiększa łączny limit vCPU dla tej samej kwoty.
author: sowmyavenkat86
ms.author: svenkat
ms.date: 01/27/2020
ms.topic: how-to
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 26fc916a2bacc0f6ae9791a53b642e93bb031466
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2020
ms.locfileid: "92077987"
---
# <a name="standard-quota-increase-limits-by-vm-series"></a>Standardowy limit przydziału: Zwiększanie limitów według serii maszyn wirtualnych

Azure Resource Manager obsługuje dwa typy przydziałów vCPU dla maszyn wirtualnych:

* *Maszyny wirtualne z opcją płatność zgodnie z rzeczywistym* użyciem i *zarezerwowane wystąpienia maszyn wirtualnych* podlegają *standardowym limitowi przydziału vCPU*.
* *Dodatkowe maszyny wirtualne* podlegają *limitowi przydziału vCPU*.

Standardowy przydział vCPU dla wystąpień z opcją płatność zgodnie z rzeczywistym użyciem i zarezerwowanych maszyn wirtualnych jest wymuszany w dwóch warstwach dla każdej subskrypcji w każdym regionie:

* Pierwsza warstwa to *całkowity regionalny limit procesorów wirtualnych vCPU*na wszystkich seriach maszyn wirtualnych.
* Druga warstwa to *Limit procesorów wirtualnych vCPU serii dla maszyn wirtualnych*, taki jak Dv3 serii procesorów wirtualnych vCPU.

Za każdym razem, gdy wdrażasz nową maszynę wirtualną, całkowite nowe i istniejące użycie vCPU dla wszystkich wystąpień maszyn wirtualnych nie mogą przekraczać zatwierdzonego limitu przydziału miejsca vCPU. W przypadku przekroczenia limitu przydziału miejsca do miejsca wdrożenia maszyny wirtualnej nie są dozwolone.

Można zażądać zwiększenia limitu przydziału vCPU dla serii maszyn wirtualnych przy użyciu Azure Portal. Zwiększenie limitu przydziału serii maszyn wirtualnych powoduje automatyczne zwiększenie łącznej granicy vCPU w regionie o tej samej wartości.

Aby dowiedzieć się więcej na temat standardowych przydziałów usługi vCPU, zobacz limity [przydziału vCPU maszyny wirtualnej](../../virtual-machines/windows/quotas.md) i [usługi Azure Subscription and Service limit](./classic-deployment-model-quota-increase-requests.md).

Aby dowiedzieć się więcej na temat zwiększania limitu przydziału vCPU według regionu dla standardowego limitu, zobacz [przydział standardowy: Zwiększ limity według regionów](regional-quota-requests.md).

Aby dowiedzieć się więcej o zwiększaniu limitów vCPU maszyn wirtualnych, zobacz limit [przydziału miejsca na miejscu: zwiększenie limitów dla całej serii maszyn wirtualnych](low-priority-quota.md).

Możesz poprosić o zwiększenie limitów przydziału standardowych vCPU na serię maszyn wirtualnych na jeden z dwóch sposobów, zgodnie z opisem w poniższych sekcjach.

## <a name="request-a-standard-quota-increase-from-help--support"></a>Zażądaj standardowego limitu przydziału od pomocy i obsługi technicznej

Aby zażądać standardowego wzrostu przydziału vCPU na serię maszyn wirtualnych z **pomocy i obsługi technicznej**:

> [!NOTE]
> Możesz również zażądać zwiększenia limitu przydziału dla wielu regionów za pomocą jednego przypadku pomocy technicznej. Aby uzyskać szczegółowe informacje, zobacz krok 8.

1. W menu [Azure Portal](https://portal.azure.com) wybierz pozycję **Pomoc i obsługa techniczna**.

   ![Łącze Pomoc i obsługa techniczna](./media/resource-manager-core-quotas-request/help-plus-support.png)

1. W oknie **Pomoc i obsługa techniczna**wybierz pozycję **nowe żądanie obsługi**.

    ![Utwórz nowe żądanie obsługi](./media/resource-manager-core-quotas-request/new-support-request.png)

1. W obszarze **typ problemu**wybierz pozycję **usługi i limity subskrypcji (przydziały)**.

   ![Wybierz typ problemu](./media/resource-manager-core-quotas-request/select-quota-issue-type.png)

1. W obszarze **subskrypcja**wybierz subskrypcję, której przydział chcesz zwiększyć.

   ![Wybierz subskrypcję dla zwiększonych limitów przydziału](./media/resource-manager-core-quotas-request/select-subscription-support-request.png)

1. W obszarze **Typ limitu przydziału**wybierz pozycję **COMPUTE-VM (rdzenie-procesorów wirtualnych vCPU)**.

   ![Wybierz typ limitu przydziału](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. Wybierz pozycję **Dalej: rozwiązania** , aby otworzyć **Szczegóły problemu**. Wybierz pozycję **Podaj szczegóły** , aby wprowadzić dodatkowe informacje.

   ![Link "Podaj szczegóły"](./media/resource-manager-core-quotas-request/provide-details-link.png)

1. W obszarze **Szczegóły przydziału**wykonaj następujące czynności:

   ![Dodatkowe szczegóły przydziału TProvide](./media/resource-manager-core-quotas-request/quota-details-deployment-rm-locations.png)

   1. W obszarze **model wdrażania**wybierz odpowiedni model.

   1. W obszarze **lokalizacje**wybierz lokalizację. Dla wybranej lokalizacji w obszarze **typy** **Wybierz typ, a**następnie wybierz pozycję **Standardowy**.

      ![Szczegóły przydziału — typy przydziałów](./media/resource-manager-core-quotas-request/quota-details-select-standard-type.png)

      W obszarze **typy**można zażądać zarówno typów limitu przydziału standardowego, jak i dodatkowego z jednego przypadku pomocy technicznej w ramach obsługi wieloselekcji.

      Aby uzyskać więcej informacji na temat zwiększania limitów przydziału punktów, zobacz [maszyny wirtualne Azure spot dla zestawów skalowania maszyn wirtualnych](../../virtual-machine-scale-sets/use-spot.md).

   1. W obszarze **Standard**wybierz serię SKU dla zwiększonych przydziałów.

      ![Szczegóły przydziału — seria SKU](./media/resource-manager-core-quotas-request/quota-details-standard-select-series.png)

   1. Wprowadź nowe limity przydziałów dla tej subskrypcji. Aby usunąć jednostkę SKU z listy, wyczyść pole wyboru obok jednostki SKU lub wybierz ikonę Odrzuć "X".

      ![Wybierz nowy limit vCPU](./media/resource-manager-core-quotas-request/quota-details-standard-set-vcpu-limit.png)

1. Aby zażądać zwiększenia limitu przydziału dla więcej niż jednej lokalizacji, wybierz dodatkową lokalizację w **lokalizacjach**, a następnie wybierz odpowiedni typ maszyny wirtualnej. Następnie można wprowadzić limit dotyczący dodatkowej lokalizacji.

   ![Określ dodatkowe lokalizacje w szczegółach przydziału](./media/resource-manager-core-quotas-request/quota-details-multiple-locations.png)

1. Wybierz pozycję **Zapisz i Kontynuuj** , aby kontynuować tworzenie żądania obsługi.

## <a name="request-a-standard-quota-increase-from-subscriptions"></a>Poproś o zwiększenie limitu przydziału w warstwie Standardowa od subskrypcji

Aby zażądać standardowego wzrostu przydziału vCPU na serię maszyn wirtualnych z **subskrypcji**:

> [!NOTE]
> Możesz również zażądać zwiększenia limitu przydziału dla wielu regionów za pomocą jednego przypadku pomocy technicznej. Aby uzyskać szczegółowe informacje, zobacz krok 7.

1. W [Azure Portal](https://portal.azure.com)Wyszukaj i wybierz pozycję **subskrypcje**.

   ![Subskrypcje w wyszukiwaniu Azure Portal](./media/resource-manager-core-quotas-request/search-for-subscriptions.png)

1. Wybierz subskrypcję, której przydział chcesz zwiększyć.

   ![Subskrypcje do wyboru dla zmian](./media/resource-manager-core-quotas-request/select-subscription-change-quota.png)

1. W lewym okienku wybierz pozycję **użycie i przydziały**.

   ![Link "użycie i przydziały"](./media/resource-manager-core-quotas-request/select-usage-plus-quotas.png)

1. W prawym górnym rogu wybierz pozycję **Zwiększ liczbę żądań**.

   ![Wybierz, aby zwiększyć limit przydziału](./media/resource-manager-core-quotas-request/request-increase-from-subscription.png)

1. W obszarze **Typ limitu przydziału**wybierz pozycję **COMPUTE-VM (rdzenie-procesorów wirtualnych vCPU)**.

   ![Wybierz typ limitu przydziału](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. W obszarze **Szczegóły przydziału**wykonaj następujące czynności:

   1. W obszarze **model wdrażania**wybierz odpowiedni model i dla pozycji **lokalizacje**wybierz lokalizację.

      ![Podaj szczegóły przydziału](./media/resource-manager-core-quotas-request/quota-details-deployment-rm-locations.png)

   1. Dla wybranej lokalizacji w obszarze **typy**wybierz pozycję **Wybierz typ**, a następnie wybierz pozycję **Standardowy**.

      ![Wybierz typ standardowy](./media/resource-manager-core-quotas-request/quota-details-select-standard-type.png)

      W obszarze **typy**można zażądać zarówno typów limitu przydziału standardowego, jak i dodatkowego z jednego przypadku pomocy technicznej w ramach obsługi wieloselekcji.

      Aby uzyskać więcej informacji na temat zwiększania limitów przydziału punktów, zobacz [maszyny wirtualne Azure spot dla zestawów skalowania maszyn wirtualnych](../../virtual-machine-scale-sets/use-spot.md).

   1. W polu **standardowa**wybierz serię SKU, której przydziały chcesz zwiększyć.

      ![Szczegóły przydziału — seria SKU](./media/resource-manager-core-quotas-request/quota-details-standard-select-series.png)

   1. Wprowadź nowe limity przydziałów dla tej subskrypcji. Aby usunąć jednostkę SKU z listy, usuń zaznaczenie pola wyboru obok jednostki SKU lub wybierz pozycję Odrzuć ikonę "X".

      ![Wybierz nowy limit vCPU](./media/resource-manager-core-quotas-request/quota-details-standard-set-vcpu-limit.png)

1. Aby zażądać zwiększenia limitu przydziału dla więcej niż jednej lokalizacji, wybierz dodatkową lokalizację w **lokalizacjach**, a następnie wybierz odpowiedni typ maszyny wirtualnej.

   Ten krok umożliwia wstępne załadowanie serii SKU wybranej dla wcześniejszych lokalizacji. Wprowadź limity przydziałów, które mają być stosowane do dodatkowej serii.

   ![Wybierz dodatkowe lokalizacje w szczegółach limitu przydziału](./media/resource-manager-core-quotas-request/quota-details-multiple-locations.png)

1. Wybierz pozycję **Zapisz i Kontynuuj** , aby kontynuować tworzenie żądania obsługi.