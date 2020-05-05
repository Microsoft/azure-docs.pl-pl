---
title: 'Samouczek: skalowanie chmury prywatnej'
description: W tym samouczku użyjesz Azure Portal do skalowania chmury prywatnej (automatyczna wersja zapoznawcza) usługi Azure VMware.
ms.topic: tutorial
ms.date: 05/04/2020
ms.openlocfilehash: 70d8c1c555badd6102f4b2547492bdea54e55783
ms.sourcegitcommit: d9cd51c3a7ac46f256db575c1dfe1303b6460d04
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82740290"
---
# <a name="tutorial-scale-an-azure-vmware-solution-avs-preview-private-cloud"></a>Samouczek: skalowanie w wersji zapoznawczej rozwiązania Azure VMware (wersja zapoznawcza)

Aby maksymalnie wykorzystać możliwości wersji zapoznawczej chmury prywatnej, należy skalować klastry i hosty w celu odzwierciedlenia potrzebnych obciążeń. Ponieważ wersja zapoznawcza nie obsługuje lokalnego programu vCenter w okresie zapoznawczym, musisz użyć tego, co zostało już utworzone za pomocą Azure Portal.

Liczbę klastrów i liczbę hostów w chmurze prywatnej można skalować zgodnie z wymaganiami obciążeń aplikacji. Ograniczenia dotyczące wydajności i dostępności dla określonych usług muszą być rozpatrywane w przypadku, gdy jest to możliwe, w środowisku chmury w wersji zapoznawczej. Limity klastrów i hostów w chmurze prywatnej są dostępne w artykule dotyczącym [koncepcji chmury prywatnej](concepts-private-clouds-clusters.md).

W tym samouczku użyjesz Azure Portal, aby:

> [!div class="checklist"]
> * Dodawanie klastra do istniejącej chmury prywatnej
> * Dodawanie hostów do istniejącego klastra

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka potrzebna jest chmura prywatna. Jeśli jeszcze nie utworzono chmury prywatnej, Skorzystaj z [samouczka Tworzenie chmury prywatnej](tutorial-create-private-cloud.md) , aby utworzyć chmurę prywatną i skonfigurować sieć dla swojej chmury prywatnej VMware na platformie Azure, tak aby skonfigurować wymaganą sieć wirtualną.

## <a name="add-a-new-cluster"></a>Dodawanie nowego klastra

Na stronie Przegląd istniejącej chmury prywatnej w obszarze **Zarządzaj**wybierz pozycję **Skaluj chmurę prywatną**. Następnie wybierz pozycję **+ Dodaj klaster**.

:::image type="content" source="./media/tutorial-scale-private-cloud/ss2-select-add-cluster.png" alt-text="Wybierz pozycję Dodaj klaster" border="true":::

Na stronie **Dodawanie klastra** Użyj suwaka, aby wybrać liczbę hostów. Wybierz pozycję **Zapisz**.

:::image type="content" source="./media/tutorial-scale-private-cloud/ss3-configure-new-cluster.png" alt-text="Konfigurowanie nowego klastra chmury prywatnej" border="true":::

Rozpocznie się wdrażanie nowego klastra.

## <a name="scale-a-cluster"></a>Skalowanie klastra 

Na stronie Przegląd istniejącej chmury prywatnej wybierz pozycję **Skaluj chmurę prywatną** i wybierz ikonę ołówka, aby edytować klaster.

:::image type="content" source="./media/tutorial-scale-private-cloud/ss4-select-scale-private-cloud-2.png" alt-text="Wybierz pozycję Skaluj chmurę prywatną w temacie Omówienie" border="true":::

Na stronie **Edytowanie klastra** Użyj suwaka, aby wybrać liczbę hostów. Wybierz pozycję **Zapisz**.

:::image type="content" source="./media/tutorial-scale-private-cloud/ss5-scale-cluster.png" alt-text="Konfigurowanie nowego klastra chmury prywatnej" border="true":::

Rozpocznie się Dodawanie hostów do klastra.

## <a name="next-steps"></a>Następne kroki

Jeśli potrzebujesz innej chmury prywatnej automatycznej synchronizacji, [Utwórz kolejną chmurę prywatną](tutorial-create-private-cloud.md), wykonując te same wymagania wstępne dotyczące sieci, klaster i limity hostów...

<!-- LINKS - external-->

<!-- LINKS - internal -->
