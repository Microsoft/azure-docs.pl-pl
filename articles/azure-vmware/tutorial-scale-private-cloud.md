---
title: 'Samouczek: skalowanie chmury prywatnej'
description: W tym samouczku użyjesz Azure Portal do skalowania prywatnej chmury rozwiązania Azure VMware w wersji zapoznawczej.
ms.topic: tutorial
ms.date: 08/21/2020
ms.openlocfilehash: dddfbddd57f3ad6b541d11c360aeadea4383044a
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/22/2020
ms.locfileid: "88750432"
---
# <a name="tutorial-scale-an-azure-vmware-solution-preview-private-cloud"></a>Samouczek: skalowanie chmury prywatnej w wersji zapoznawczej rozwiązania Azure VMware

Aby maksymalnie wykorzystać możliwości środowiska chmury prywatnej w wersji zapoznawczej platformy Azure VMware, Skaluj klastry i hosty w celu odzwierciedlenia potrzebnych obciążeń. Ponieważ rozwiązanie Azure VMware nie obsługuje lokalnego programu vCenter w trakcie korzystania z wersji zapoznawczej, należy użyć tego, co zostało już utworzone za pomocą Azure Portal.

Liczbę klastrów i liczbę hostów w chmurze prywatnej można skalować zgodnie z wymaganiami obciążeń aplikacji. Ograniczenia dotyczące wydajności i dostępności dla określonych usług muszą być rozpatrywane w przypadku w przypadku, gdy chodzi o wielkość liter w środowisku chmury programu Azure VMware w wersji zapoznawczej. Limity klastrów i hostów w chmurze prywatnej są dostępne w artykule dotyczącym [koncepcji chmury prywatnej](concepts-private-clouds-clusters.md).

W tym samouczku użyjesz Azure Portal, aby:

> [!div class="checklist"]
> * Dodawanie klastra do istniejącej chmury prywatnej
> * Dodawanie hostów do istniejącego klastra

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka potrzebna jest chmura prywatna. Jeśli jeszcze nie utworzono chmury prywatnej, Skorzystaj z [samouczka Tworzenie chmury prywatnej](tutorial-create-private-cloud.md) , aby utworzyć chmurę prywatną i skonfigurować sieć dla swojej chmury prywatnej VMware na platformie Azure, tak aby skonfigurować wymaganą sieć wirtualną.

## <a name="add-a-new-cluster"></a>Dodawanie nowego klastra

1. Na stronie Przegląd istniejącej chmury prywatnej w obszarze **Zarządzaj**wybierz pozycję **Skaluj chmurę prywatną**. Następnie wybierz pozycję **+ Dodaj klaster**.

   :::image type="content" source="./media/tutorial-scale-private-cloud/ss2-select-add-cluster.png" alt-text="Wybierz pozycję Dodaj klaster" border="true":::

1. Na stronie **Dodawanie klastra** Użyj suwaka, aby wybrać liczbę hostów. Wybierz pozycję **Zapisz**.

   :::image type="content" source="./media/tutorial-scale-private-cloud/ss3-configure-new-cluster.png" alt-text="Na stronie Dodawanie klastra użyj suwaka, aby wybrać liczbę hostów. Wybierz pozycję Zapisz." border="true":::

   Rozpocznie się wdrażanie nowego klastra.

## <a name="scale-a-cluster"></a>Skalowanie klastra 

1. Na stronie Przegląd istniejącej chmury prywatnej wybierz pozycję **Skaluj chmurę prywatną** i wybierz ikonę ołówka, aby edytować klaster.

   :::image type="content" source="./media/tutorial-scale-private-cloud/ss4-select-scale-private-cloud-2.png" alt-text="Wybierz pozycję Skaluj chmurę prywatną w temacie Omówienie" border="true":::

1. Na stronie **Edytowanie klastra** Użyj suwaka, aby wybrać liczbę hostów. Wybierz pozycję **Zapisz**.

   :::image type="content" source="./media/tutorial-scale-private-cloud/ss5-scale-cluster.png" alt-text="Na stronie edytowanie klastra użyj suwaka, aby wybrać liczbę hostów. Wybierz pozycję Zapisz." border="true":::

   Rozpocznie się Dodawanie hostów do klastra.

## <a name="next-steps"></a>Następne kroki

Jeśli potrzebujesz innej chmury prywatnej rozwiązania VMware platformy Azure, [Utwórz kolejną chmurę prywatną](tutorial-create-private-cloud.md), postępując zgodnie z tymi samymi wymaganiami dotyczącymi wymagań wstępnych dotyczących sieci, klastrów i hostów.

<!-- LINKS - external-->

<!-- LINKS - internal -->
