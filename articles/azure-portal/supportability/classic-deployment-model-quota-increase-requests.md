---
title: Klasyczny model wdrażania platformy Azure
description: Klasyczny model wdrażania, który został zastąpiony przez model Menedżer zasobów, wymusza globalne limity przydziału vCPU dla maszyn wirtualnych i zestawów skalowania maszyn wirtualnych.
author: sowmyavenkat86
ms.author: svenkat
ms.date: 01/27/2020
ms.topic: how-to
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: b5b5e8c11bfe164aaa3539742dac8c4d267c69e1
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/06/2020
ms.locfileid: "96745608"
---
# <a name="classic-deployment-model"></a>Klasyczny model wdrażania

Klasyczny model wdrażania jest starszym modelem wdrażania platformy Azure. Wymusza globalny limit przydziału vCPU dla maszyn wirtualnych i zestawów skalowania maszyn wirtualnych. Klasyczny model wdrażania nie jest już zalecany i jest teraz zastępowany przez model Menedżer zasobów.

Aby dowiedzieć się więcej na temat tych dwóch modeli wdrażania i korzyści płynących z używania Menedżer zasobów, zobacz [wdrażanie Menedżer zasobów i klasycznych](../../azure-resource-manager/management/deployment-models.md).

Po utworzeniu nowej subskrypcji zostanie do niej przypisany domyślny przydział procesorów wirtualnych vCPU. Gdy nowa maszyna wirtualna ma zostać wdrożona przy użyciu klasycznego modelu wdrażania, suma nowych i istniejących vCPU użycie we wszystkich regionach nie może przekraczać limitu przydziału vCPU zatwierdzonego dla klasycznego modelu wdrażania.

Aby dowiedzieć się więcej na temat przydziałów, zobacz [limity subskrypcji i usług platformy Azure, limity przydziału i ograniczenia](../../azure-resource-manager/management/azure-subscription-service-limits.md).

Można zażądać zwiększenia limitu przydziału vCPU dla klasycznego modelu wdrażania. Użyj opcji **Pomoc i obsługa** lub **użycie i limity przydziału** w Azure Portal.

## <a name="request-per-vm-series-vcpu-quota-increase-at-subscription-level-using-help--support"></a>Żądanie zwiększania limitu przydziału vCPU na poziomie subskrypcji przy użyciu pomocy i obsługi technicznej

Postępuj zgodnie z poniższymi instrukcjami, aby utworzyć żądanie obsługi przy użyciu **pomocy i obsługi technicznej** w Azure Portal.

1. Z menu [Azure Portal](https://portal.azure.com) wybierz pozycję **Pomoc i obsługa techniczna**.

   ![Wybierz pozycję Pomoc i obsługa techniczna w Azure Portal](./media/resource-manager-core-quotas-request/help-plus-support.png)

1. Wybierz pozycję **Nowe żądanie obsługi**.

   ![Utwórz nowe żądanie obsługi w Azure Portal](./media/resource-manager-core-quotas-request/new-support-request.png)

1. W polu **typ problemu** wybierz pozycję **usługi i limity subskrypcji (przydziały)**.

   ![Wybierz przydziały jako typ problemu](./media/resource-manager-core-quotas-request/select-quota-issue-type.png)

1. Wybierz subskrypcję, której przydział chcesz zwiększyć.

   ![Wybierz subskrypcję, dla której chcesz zwiększyć przydział](./media/resource-manager-core-quotas-request/select-subscription-support-request.png)

1. W obszarze **Typ limitu przydziału** wybierz pozycję **COMPUTE-VM (rdzenie-procesorów wirtualnych vCPU)**.

   ![Wybierz typ limitu przydziału do zwiększenia](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. Wybierz pozycję **Dalej: rozwiązania** , aby otworzyć **Szczegóły problemu**. Wybierz pozycję **Podaj szczegóły** , aby podać dodatkowe informacje.

   ![Podaj szczegółowe informacje ułatwiające Twoje żądanie](./media/resource-manager-core-quotas-request/provide-details-link.png)

1. W obszarze **Szczegóły przydziału** wybierz pozycję **klasyczne** i wybierz **lokalizację**.

   ![Dodaj szczegóły, w tym model wdrożenia i lokalizację](./media/resource-manager-core-quotas-request/quota-details-classic.png)

1. W przypadku **rodziny SKU** wybierz co najmniej jedną rodzinę SKU do zwiększenia.

   ![Określ rodzinę SKU do zwiększenia](./media/resource-manager-core-quotas-request/sku-family-classic.png)

1. Wprowadź nowe limity, które chcesz wykonać w ramach subskrypcji. Aby usunąć wiersz, usuń zaznaczenie jednostki SKU z **rodziny SKU** lub wybierz pozycję Odrzuć ikonę "X". Po wprowadzeniu przydziału dla każdej rodziny SKU wybierz pozycję **Zapisz i Kontynuuj** w obszarze **szczegóły limitu przydziału** , aby kontynuować żądanie pomocy technicznej.

   ![Żądaj nowych limitów](./media/resource-manager-core-quotas-request/new-limits-classic.png)

## <a name="request-per-vm-series-vcpu-quota-increase-at-subscription-level-using-usage--quotas"></a>Żądanie zwiększania limitu przydziału vCPU na poziomie subskrypcji przy użyciu i przydziałów

Postępuj zgodnie z poniższymi instrukcjami, aby utworzyć żądanie obsługi przy użyciu przystawki **użycie + przydziały** w Azure Portal.

1. W [Azure Portal](https://portal.azure.com)Wyszukaj i wybierz pozycję **subskrypcje**.

   ![Przejdź do pozycji subskrypcje w Azure Portal](./media/resource-manager-core-quotas-request/search-for-subscriptions.png)

1. Wybierz subskrypcję, której przydział chcesz zwiększyć.

   ![Wybierz subskrypcję do zmodyfikowania](./media/resource-manager-core-quotas-request/select-subscription-change-quota.png)

1. Wybierz pozycję **użycie i przydziały**.

   ![Wybierz użycie i przydziały dla subskrypcji](./media/resource-manager-core-quotas-request/select-usage-plus-quotas.png)

1. W prawym górnym rogu wybierz pozycję **Zwiększ liczbę żądań**.

   ![Wybierz, aby zwiększyć limit przydziału](./media/resource-manager-core-quotas-request/request-increase-from-subscription.png)

1. Wybierz pozycję **COMPUTE-VM (rdzenie-procesorów wirtualnych vCPU) limit subskrypcji jest zwiększany** jako **typ przydziału**.

   ![Wybierz typ limitu przydziału](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. Wybierz pozycję **Dalej: rozwiązania** , aby otworzyć **Szczegóły problemu**. Wybierz pozycję **Podaj szczegóły** , aby podać dodatkowe informacje.

   ![Podaj szczegóły żądania](./media/resource-manager-core-quotas-request/provide-details-link.png)

1. W obszarze **Szczegóły przydziału** wybierz pozycję **klasyczne** i **lokalizację**.

   ![Wybierz Szczegóły przydziału, w tym model wdrożenia i lokalizację](./media/resource-manager-core-quotas-request/quota-details-classic.png)

1. Wybierz co najmniej jedną rodzinę SKU, aby zwiększyć.

   ![Wybierz rodzinę jednostek SKU do zwiększenia](./media/resource-manager-core-quotas-request/sku-family-classic.png)

1. Wprowadź nowe limity, które chcesz wykonać w ramach subskrypcji. Aby usunąć wiersz, usuń zaznaczenie jednostki SKU z **rodziny SKU** lub wybierz pozycję Odrzuć ikonę "X". Po wprowadzeniu przydziału dla każdej rodziny SKU wybierz pozycję **Zapisz i Kontynuuj** w obszarze **szczegóły limitu przydziału** , aby kontynuować żądanie pomocy technicznej.

   ![Wprowadź nowy przydział](./media/resource-manager-core-quotas-request/new-limits-classic.png)

