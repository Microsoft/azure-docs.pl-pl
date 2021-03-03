---
title: Konfigurowanie skalowania dla usługi Azure Cloud Services (obsługa rozszerzona)
description: Jak włączyć opcje skalowania dla usługi Azure Cloud Services (obsługa rozszerzona)
ms.topic: how-to
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: cfa5be01a0d36764086c6c9adf97e6cb166d2bb6
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101728165"
---
# <a name="configure-scaling-options-with-azure-cloud-services-extended-support"></a>Konfigurowanie opcji skalowania przy użyciu usługi Azure Cloud Services (obsługa rozszerzona) 

Warunki można skonfigurować w taki sposób, aby umożliwiały skalowanie i wychodzące wdrożenia w Cloud Services (obsługa rozszerzona). Warunki te mogą opierać się na użyciu procesora CPU, obciążenia dysku i obciążenia sieci. 

Podczas konfigurowania skalowania wdrożeń usług w chmurze należy wziąć pod uwagę następujące informacje:
- Skalowanie wpływa na podstawowe użycie. Większe wystąpienia roli zużywają więcej rdzeni i można skalować tylko w ramach podstawowego limitu subskrypcji. Aby uzyskać więcej informacji, zobacz [Azure subscription and service limits, quotas, and constraints (Limity, przydziały i ograniczenia usług i subskrypcji platformy Azure)](../azure-resource-manager/management/azure-subscription-service-limits.md).
- Obsługiwane jest skalowanie oparte na wartości progowej komunikatów w kolejce. Aby uzyskać więcej informacji, zobacz Rozpoczynanie [pracy z usługą Azure queue storage](../storage/queues/storage-dotnet-how-to-use-queues.md).
- Aby zapewnić wysoką dostępność aplikacji w chmurze (rozszerzonej pomocy technicznej), należy wdrożyć program z co najmniej dwoma wystąpieniami roli.
- Niestandardowe Skalowanie automatyczne może wystąpić tylko wtedy, gdy wszystkie role są w stanie **gotowe** .

## <a name="configure-and-manage-scaling"></a>Konfigurowanie skalowania i zarządzanie nim

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). 
2. Wybierz wdrożenie usługi w chmurze (obsługa rozszerzona), dla którego chcesz skonfigurować skalowanie. 
3. Wybierz blok **Skala** . 

    :::image type="content" source="media/enable-scaling-1.png" alt-text="Obraz pokazuje wybranie opcji Pulpit zdalny w Azure Portal":::

4. Na stronie zostanie wyświetlona lista wszystkich ról, w których można skonfigurować skalowanie. Wybierz rolę, którą chcesz skonfigurować. 
5. Wybierz typ skali, którą chcesz skonfigurować
    - **Skalowanie ręczne** ustawi bezwzględną liczbę wystąpień.
        1. Wybierz pozycję **skalowanie ręczne**.
        2. Wprowadź liczbę wystąpień do skalowania w górę lub w dół do.
        3. Wybierz pozycję **Zapisz**.

        :::image type="content" source="media/enable-scaling-2.png" alt-text="Obraz przedstawiający Konfigurowanie ręcznego skalowania w Azure Portal":::

        4. Operacja skalowania rozpocznie się natychmiast. 
        
    - **Niestandardowe Skalowanie automatyczne** umożliwi określenie reguł, które określają, jak dużo lub jak mała do skalowania. 
        1. Wybierz **niestandardowe automatyczne skalowanie**
        2. Wybierz skalowanie na podstawie metryki lub liczby wystąpień.

        :::image type="content" source="media/enable-scaling-3.png" alt-text="Obraz przedstawiający Konfigurowanie niestandardowego automatycznego skalowania w Azure Portal":::

        3. W przypadku skalowania na podstawie metryki Dodaj reguły w miarę potrzeb, aby osiągnąć wymagane wyniki skalowania.

        :::image type="content" source="media/enable-scaling-4.png" alt-text="Obraz przedstawiający Konfigurowanie niestandardowych reguł automatycznego skalowania w Azure Portal":::

        4. Wybierz pozycję **Zapisz**.
        5. Operacje skalowania rozpoczną się zaraz po wyzwoleniu reguły.
        
6. Możesz wyświetlić lub dostosować istniejące reguły skalowania stosowane do wdrożeń, wybierając kartę **Skala** .

    :::image type="content" source="media/enable-scaling-5.png" alt-text="Obraz przedstawiający dostosowanie istniejącej reguły skalowania w Azure Portal":::

## <a name="next-steps"></a>Następne kroki 
- Zapoznaj się z [wymaganiami wstępnymi](deploy-prerequisite.md) dotyczącymi wdrażania Cloud Services (obsługa rozszerzona).
- Zapoznaj się z [często zadawanymi pytaniami](faq.md) dotyczącymi Cloud Services (obsługa rozszerzona).
- Wdróż usługę w chmurze (obsługę rozszerzoną) przy użyciu [Azure Portal](deploy-portal.md), [programu PowerShell](deploy-powershell.md), [szablonu](deploy-template.md) lub [programu Visual Studio](deploy-visual-studio.md).