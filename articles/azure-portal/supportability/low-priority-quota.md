---
title: Limit przydziału maszyny wirtualnej
description: Zwiększenie limitów przydziału dla maszyn wirtualnych na miejscu, które zapewniają model użycia platformy Azure, który umożliwia założenie obniżenia kosztów w programie Exchange w celu umożliwienia platformie Azure usuwania maszyn wirtualnych zgodnie z wymaganiami.
author: sowmyavenkat86
ms.author: svenkat
ms.date: 01/27/2020
ms.topic: how-to
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: c8b9a2251d7923fe1919b7b934f6c97877cd5b37
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96745489"
---
# <a name="spot-quota-increase-limits-for-all-vm-series"></a>Przydział miejsca na miejscu: Zwiększ limity dla całej serii maszyn wirtualnych

Maszyny wirtualne w miejscu zapewniają inny model użycia platformy Azure. Umożliwiają one założenie obniżenia kosztów w programie Exchange w celu umożliwienia systemowi Azure usuwania maszyn wirtualnych zgodnie z wymaganiami dla wdrożeń z opcją płatność zgodnie z rzeczywistym użyciem lub zarezerwowanych wystąpień maszyn wirtualnych. Aby uzyskać więcej informacji na temat maszyn wirtualnych, zobacz maszyny wirtualne [platformy Azure dla zestawów skalowania maszyn wirtualnych](../../virtual-machine-scale-sets/use-spot.md).

Azure Resource Manager obsługuje dwa typy przydziałów vCPU dla maszyn wirtualnych:

* *Maszyny wirtualne z opcją płatność zgodnie z rzeczywistym* użyciem i *zarezerwowane wystąpienia maszyn wirtualnych* podlegają *standardowym limitowi przydziału vCPU*.
* *Dodatkowe maszyny wirtualne* podlegają *limitowi przydziału vCPU*.

W przypadku typu limitu przydziału usługi vCPU Menedżer zasobów przydziały vCPU są wymuszane we wszystkich dostępnych seriach maszyn wirtualnych w ramach pojedynczego limitu regionalnego.

Za każdym razem, gdy wdrażasz nową maszynę wirtualną, całkowite nowe i istniejące użycie vCPU dla wszystkich wystąpień maszyn wirtualnych nie mogą przekraczać zatwierdzonego limitu przydziału miejsca vCPU. W przypadku przekroczenia limitu przydziału miejsca do miejsca wdrożenia maszyny wirtualnej nie są dozwolone.

W tym artykule omówiono sposób żądania zwiększenia limitu przydziału vCPU w miejscu przy użyciu Azure Portal.

Aby dowiedzieć się więcej na temat standardowych przydziałów usługi vCPU, zobacz limity przydziału i usługi [Virtual Machine vCPU](../../virtual-machines/windows/quotas.md) oraz [limitów subskrypcji i usług Azure, przydziałów i ograniczeń](../../azure-resource-manager/management/azure-subscription-service-limits.md).

Aby dowiedzieć się więcej o zwiększaniu limitu vCPU według regionu, zobacz [przydział standardowy: Zwiększ limity według regionów](regional-quota-requests.md).

## <a name="request-a-quota-limit-increase-from-help--support"></a>Poproś o zwiększenie limitu przydziału z pomocy i obsługi technicznej

Aby zażądać zwiększenia limitu przydziału na miejscu dla wszystkich serii maszyn wirtualnych korzystających z **pomocy i obsługi technicznej**:

> [!NOTE]
> Możesz również zażądać zwiększenia limitu przydziału dla wielu regionów za pomocą jednego przypadku pomocy technicznej. Aby uzyskać szczegółowe informacje, zobacz krok 8.

1. Z menu [Azure Portal](https://portal.azure.com) wybierz pozycję **Pomoc i obsługa techniczna**.

   ![Łącze Pomoc i obsługa techniczna](./media/resource-manager-core-quotas-request/help-plus-support.png)

1. W oknie **Pomoc i obsługa techniczna** wybierz pozycję **nowe żądanie obsługi**.

    ![Utwórz nowe żądanie obsługi](./media/resource-manager-core-quotas-request/new-support-request.png)

1. W obszarze **typ problemu** wybierz pozycję **usługi i limity subskrypcji (przydziały)**.

   ![Wybierz typ problemu](./media/resource-manager-core-quotas-request/select-quota-issue-type.png)

1. W obszarze **subskrypcja** wybierz subskrypcję, której przydział chcesz zwiększyć.

   ![Wybierz subskrypcję dla zwiększonych limitów przydziału](./media/resource-manager-core-quotas-request/select-subscription-support-request.png)

1. W obszarze **Typ limitu przydziału** wybierz pozycję **COMPUTE-VM (rdzenie-procesorów wirtualnych vCPU)**.

   ![Wybierz typ limitu przydziału](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. Wybierz pozycję **Dalej: rozwiązania** , aby otworzyć **Szczegóły problemu**. Wybierz pozycję **Podaj szczegóły** , aby wprowadzić dodatkowe informacje.

   ![Link "Podaj szczegóły"](./media/resource-manager-core-quotas-request/provide-details-link.png)

1. W obszarze **szczegóły limitu przydziału** wykonaj następujące czynności:

   1. W obszarze **model wdrażania** wybierz odpowiedni model i dla pozycji **lokalizacje** wybierz lokalizację.

      ![Podaj dodatkowe szczegóły przydziału](./media/resource-manager-core-quotas-request/quota-details-deployment-locations.png)

   1. Dla wybranej lokalizacji, w obszarze **typy**, w **Wybierz typ**, wybierz opcję **punkt**.

      ![Wybierz typ punktu](./media/resource-manager-core-quotas-request/select-spot-type.png)

       W obszarze **typy** można zażądać zarówno typów limitu przydziału standardowego, jak i dodatkowego z jednego przypadku pomocy technicznej w ramach obsługi wieloselekcji.

       Aby uzyskać więcej informacji, zobacz [przydział standardowy: zwiększanie limitów przez serię maszyn wirtualnych](per-vm-quota-requests.md).

   1. Wprowadź nowy limit przydziału dla tej subskrypcji.

      ![Wybierz nowy przydział dla dostosowanej maszyny wirtualnej](./media/resource-manager-core-quotas-request/spot-set-new-quota.png)

1. Aby zażądać zwiększenia limitu przydziału dla więcej niż jednej lokalizacji, wybierz dodatkową lokalizację w **lokalizacjach**, a następnie wybierz odpowiedni typ maszyny wirtualnej. Następnie można wprowadzić limit dotyczący dodatkowej lokalizacji.

   ![Określ dodatkowe lokalizacje w szczegółach przydziału](./media/resource-manager-core-quotas-request/quota-details-multiple-locations.png)

1. Wybierz pozycję **Zapisz i Kontynuuj** , aby kontynuować tworzenie żądania obsługi.

## <a name="request-a-quota-limit-increase-from-the-subscriptions-pane"></a>Poproś o zwiększenie limitu przydziału w okienku subskrypcje

Aby zażądać zwiększenia limitu przydziału punktów na wszystkich seriach maszyn wirtualnych w okienku **subskrypcje** :

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

1. W obszarze **Typ limitu przydziału** wybierz pozycję **COMPUTE-VM (rdzenie-procesorów wirtualnych vCPU)**.

   ![Wybierz typ limitu przydziału](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. Wybierz pozycję **Dalej: rozwiązania** , aby otworzyć **Szczegóły problemu**. Wybierz pozycję **Podaj szczegóły** , aby wprowadzić dodatkowe informacje. W obszarze **Szczegóły przydziału** wprowadź następujące informacje:

   1. W obszarze **model wdrażania** wybierz odpowiedni model i dla pozycji **lokalizacje** wybierz lokalizację.

      ![Podaj szczegóły przydziału](./media/resource-manager-core-quotas-request/quota-details-deployment-locations.png)

   1. Dla wybranej lokalizacji, w obszarze **typy**, w **Wybierz typ**, wybierz opcję **punkt**.

      ![Wybierz typ punktu](./media/resource-manager-core-quotas-request/select-spot-type.png)

      Aby uzyskać więcej informacji, zobacz [przydział standardowy: zwiększanie limitów przez serię maszyn wirtualnych](per-vm-quota-requests.md).

   1. Wprowadź nowy limit przydziału dla tej subskrypcji.

      ![Wprowadź nową wartość limitu vCPU](./media/resource-manager-core-quotas-request/spot-set-new-quota.png)

1. Aby zażądać zwiększenia limitu przydziału dla więcej niż jednej lokalizacji, wybierz dodatkową lokalizację w **lokalizacjach**, a następnie wybierz odpowiedni typ maszyny wirtualnej. Następnie można wprowadzić limit dotyczący dodatkowej lokalizacji.

   ![Wybierz dodatkowe lokalizacje w szczegółach limitu przydziału](./media/resource-manager-core-quotas-request/quota-details-multiple-locations.png)

1. Wybierz pozycję **Zapisz i Kontynuuj** , aby kontynuować tworzenie żądania obsługi.
