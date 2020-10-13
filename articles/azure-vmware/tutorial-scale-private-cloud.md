---
title: Samouczek — skalowanie chmury prywatnej
description: W tym samouczku użyjesz Azure Portal, aby skalować chmurę prywatną rozwiązania Azure VMware.
ms.topic: tutorial
ms.date: 09/21/2020
ms.openlocfilehash: e1226eb98607a34869bda1f998c7cecea2e50919
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91254418"
---
# <a name="tutorial-scale-an-azure-vmware-solution-private-cloud"></a>Samouczek: skalowanie chmury prywatnej rozwiązania Azure VMware

Aby maksymalnie wykorzystać możliwości chmury prywatnej rozwiązań VMware platformy Azure, Skaluj klastry i hosty w celu odzwierciedlenia potrzebnych obciążeń. Liczbę klastrów i liczbę hostów w chmurze prywatnej można skalować zgodnie z wymaganiami obciążeń aplikacji. Ograniczenia dotyczące wydajności i dostępności dla określonych usług muszą być rozpatrywane w przypadku, gdy chodzi o wielkość liter w środowisku chmury prywatnej rozwiązania VMware platformy Azure. Limity klastrów i hostów znajdują się w artykule dotyczącym [koncepcji chmury prywatnej](concepts-private-clouds-clusters.md) .

W tym samouczku użyjesz Azure Portal, aby:

> [!div class="checklist"]
> * Dodawanie klastra do istniejącej chmury prywatnej
> * Dodawanie hostów do istniejącego klastra

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka potrzebna jest chmura prywatna. Jeśli jeszcze nie utworzono chmury prywatnej, Skorzystaj z [samouczka Tworzenie chmury prywatnej](tutorial-create-private-cloud.md) , aby utworzyć i skonfigurować sieć dla chmury prywatnej VMware na platformie Azure w celu skonfigurowania wymaganej sieci wirtualnej.

## <a name="add-a-new-cluster"></a>Dodawanie nowego klastra

1. Na stronie Przegląd istniejącej chmury prywatnej w obszarze **Zarządzaj**wybierz pozycję **Skaluj chmurę prywatną**. Następnie wybierz pozycję **+ Dodaj klaster**.

   :::image type="content" source="./media/tutorial-scale-private-cloud/ss2-select-add-cluster.png" alt-text="Wybierz pozycję Dodaj klaster" border="true":::

1. Na stronie **Dodawanie klastra** Użyj suwaka, aby wybrać liczbę hostów. Wybierz pozycję **Zapisz**.

   :::image type="content" source="./media/tutorial-scale-private-cloud/ss3-configure-new-cluster.png" alt-text="Wybierz pozycję Dodaj klaster" border="true":::

   Rozpocznie się wdrażanie nowego klastra.

## <a name="scale-a-cluster"></a>Skalowanie klastra 

1. Na stronie Przegląd istniejącej chmury prywatnej wybierz pozycję **Skaluj chmurę prywatną** i wybierz ikonę ołówka, aby edytować klaster.

   :::image type="content" source="./media/tutorial-scale-private-cloud/ss4-select-scale-private-cloud-2.png" alt-text="Wybierz pozycję Dodaj klaster" border="true":::

1. Na stronie **Edytowanie klastra** Użyj suwaka, aby wybrać liczbę hostów. Wybierz pozycję **Zapisz**.

   :::image type="content" source="./media/tutorial-scale-private-cloud/ss5-scale-cluster.png" alt-text="Wybierz pozycję Dodaj klaster" border="true":::

   Rozpocznie się Dodawanie hostów do klastra.

## <a name="next-steps"></a>Następne kroki

Jeśli potrzebujesz innej chmury prywatnej rozwiązania VMware platformy Azure, [Utwórz kolejną chmurę prywatną](tutorial-create-private-cloud.md), postępując zgodnie z tymi samymi wymaganiami dotyczącymi wymagań wstępnych dotyczących sieci, klastrów i hostów.

<!-- LINKS - external-->

<!-- LINKS - internal -->
