---
title: Samouczek — skalowanie chmury prywatnej
description: W tym samouczku użyjesz Azure Portal, aby skalować chmurę prywatną rozwiązania Azure VMware.
ms.topic: tutorial
ms.date: 09/21/2020
ms.openlocfilehash: d49d973cc6d97280dc0c7ea6681f2602b871e1ba
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/28/2020
ms.locfileid: "92791243"
---
# <a name="tutorial-scale-an-azure-vmware-solution-private-cloud"></a>Samouczek: skalowanie chmury prywatnej rozwiązania Azure VMware

Aby maksymalnie wykorzystać możliwości chmury prywatnej rozwiązań VMware platformy Azure, Skaluj klastry i hosty w celu odzwierciedlenia potrzebnych obciążeń. Klastry i hosty można skalować w chmurze prywatnej zgodnie z wymaganiami obciążeń aplikacji. Ograniczenia dotyczące wydajności i dostępności dla określonych usług powinny być rozkierowane na wielkość liter. Limity klastrów i hostów znajdują się w artykule dotyczącym [koncepcji chmury prywatnej](concepts-private-clouds-clusters.md) .

W tym samouczku użyjesz Azure Portal, aby:

> [!div class="checklist"]
> * Dodawanie klastra do istniejącej chmury prywatnej
> * Dodawanie hostów do istniejącego klastra

## <a name="prerequisites"></a>Wymagania wstępne

Chmura prywatna do wykonania tego samouczka. Jeśli nie utworzono chmury prywatnej, użyj [samouczka Utwórz chmurę prywatną](tutorial-create-private-cloud.md) , aby ją utworzyć. Skonfiguruj sieć dla swojej chmury prywatnej VMware na platformie Azure, aby skonfigurować wymaganą sieć wirtualną.

## <a name="add-a-new-cluster"></a>Dodawanie nowego klastra

1. Na stronie Przegląd istniejącej chmury prywatnej w obszarze **Zarządzaj** wybierz pozycję **Skaluj chmurę prywatną** . Następnie wybierz pozycję **+ Dodaj klaster** .

   :::image type="content" source="./media/tutorial-scale-private-cloud/ss2-select-add-cluster.png" alt-text="Wybierz pozycję Dodaj klaster" border="true":::

1. Na stronie **Dodawanie klastra** Użyj suwaka, aby wybrać liczbę hostów. Wybierz pozycję **Zapisz** .

   :::image type="content" source="./media/tutorial-scale-private-cloud/ss3-configure-new-cluster.png" alt-text="Wybierz pozycję Dodaj klaster" border="true":::

   Rozpocznie się wdrażanie nowego klastra.

## <a name="scale-a-cluster"></a>Skalowanie klastra 

1. Na stronie Przegląd istniejącej chmury prywatnej wybierz pozycję **Skaluj chmurę prywatną** i wybierz ikonę ołówka, aby edytować klaster.

   :::image type="content" source="./media/tutorial-scale-private-cloud/ss4-select-scale-private-cloud-2.png" alt-text="Wybierz pozycję Dodaj klaster" border="true":::

1. Na stronie **Edytowanie klastra** Użyj suwaka, aby wybrać liczbę hostów. Wybierz pozycję **Zapisz** .

   :::image type="content" source="./media/tutorial-scale-private-cloud/ss5-scale-cluster.png" alt-text="Wybierz pozycję Dodaj klaster" border="true":::

   Rozpocznie się Dodawanie hostów do klastra.

## <a name="next-steps"></a>Następne kroki

Jeśli potrzebujesz innej chmury prywatnej rozwiązania VMware platformy Azure, [Utwórz kolejną chmurę prywatną](tutorial-create-private-cloud.md), wykonując te same wymagania wstępne dotyczące sieci, klastra i limitów hosta.

<!-- LINKS - external-->

<!-- LINKS - internal -->
