---
title: Tworzenie grupy umieszczania w pobliżu przy użyciu portalu
description: Dowiedz się, jak utworzyć grupę umieszczania w sąsiedztwie przy użyciu Azure Portal.
author: cynthn
ms.service: virtual-machines
ms.subservice: proximity-placement-groups
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 3/8/2021
ms.author: cynthn
ms.openlocfilehash: daf844870670c14db5208f45fbd9c9adf46be985
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102504630"
---
# <a name="create-a-proximity-placement-group-using-the-azure-portal"></a>Tworzenie grupy położenia zbliżeniowe przy użyciu Azure Portal

Aby zapewnić, że maszyny wirtualne będą możliwie jak najbliżej, osiągając najniższe możliwe opóźnienie, należy wdrożyć je w obrębie [grupy umieszczania sąsiedztwa](../co-location.md#proximity-placement-groups).

Grupa umieszczania bliskości jest grupą logiczną używaną w celu upewnienia się, że zasoby obliczeniowe platformy Azure znajdują się fizycznie blisko siebie. Grupy umieszczania zbliżeniowe są przydatne w przypadku obciążeń, w których jest wymagane małe opóźnienia.

> [!NOTE]
> Grupy umieszczania zbliżeniowe nie mogą być używane z dedykowanymi hostami.
>
> Jeśli chcesz używać stref dostępności razem z grupami umieszczania, musisz upewnić się, że maszyny wirtualne w grupie umieszczania są również w tej samej strefie dostępności.
>

## <a name="create-the-proximity-placement-group"></a>Tworzenie grupy umieszczania zbliżeniowe

1. W polu wyszukiwania wpisz **bliską grupę umieszczania** .
1. W obszarze **usługi** w wynikach wyszukiwania wybierz pozycję **grupy położenia zbliżeniowe**.
1. Na stronie **grupy umieszczania sąsiedztwa** wybierz pozycję **Dodaj**.
1. Na karcie **podstawy** w obszarze **szczegóły projektu** upewnij się, że wybrano poprawną subskrypcję.
1. W obszarze **Grupa zasobów** wybierz pozycję **Utwórz nową** , aby utworzyć nową grupę, lub Wybierz pustą grupę zasobów, która już istnieje, z listy rozwijanej. 
1. W **obszarze region** wybierz lokalizację, w której ma zostać utworzona grupa umieszczania sąsiedztwa.
1. W polu **Nazwa grupy położenia zbliżeniowe** wpisz nazwę, a następnie wybierz pozycję **Przegląd + Utwórz**.
1. Po zakończeniu walidacji wybierz pozycję **Utwórz** , aby utworzyć grupę umieszczania zbliżeniowe.

    ![Zrzut ekranu przedstawiający tworzenie grupy umieszczania zbliżeniowe](./media/ppg/ppg.png)


## <a name="create-a-vm"></a>Tworzenie maszyny wirtualnej

1. Podczas tworzenia maszyny wirtualnej w portalu przejdź do karty **Zaawansowane** . 
1. W wybranej **grupie umieszczania sąsiedztwa** Wybierz poprawną grupę umieszczania. 

    ![Zrzut ekranu przedstawiający sekcję Grupa umieszczania sąsiedztwa podczas tworzenia nowej maszyny wirtualnej w portalu](./media/ppg/vm-ppg.png)

1. Po zakończeniu wprowadzania wszystkich innych wymaganych opcji wybierz pozycję **Przegląd + Utwórz**.
1. Po przeprowadzeniu walidacji wybierz pozycję **Utwórz** , aby wdrożyć maszynę wirtualną w grupie umieszczania.


## <a name="add-vms-in-an-availability-set-to-a-proximity-placement-group"></a>Dodaj maszyny wirtualne w zestawie dostępności do grupy umieszczania zbliżeniowego

Jeśli maszyna wirtualna jest częścią zestawu dostępności, przed dodaniem maszyn wirtualnych należy dodać zestaw dostępności do grupy umieszczania.

1. W [portalu](https://portal.azure.com) Wyszukaj *zestawy dostępności* i wybierz swój zestaw dostępności na podstawie wyników.
1. Stop\deallocate każdą maszynę wirtualną w zestawie dostępności, wybierając maszynę wirtualną, a następnie wybierając pozycję **Zatrzymaj** na stronie dla maszyny wirtualnej, a następnie wybierz **przycisk OK** , aby zatrzymać maszynę wirtualną.
1. Na stronie zestawu dostępności upewnij się, że wszystkie maszyny wirtualne mają **stan** " **zatrzymane" (cofnięto przydział)**.
1. W menu po lewej stronie wybierz pozycję **Konfiguracja**.
1. W obszarze **Grupa położenia zbliżeniowe** wybierz grupę umieszczania z listy rozwijanej, a następnie wybierz pozycję **Zapisz**.
1. Wybierz pozycję **Przegląd** z menu po lewej stronie, aby ponownie wyświetlić listę maszyn wirtualnych. 
1. Zaznacz każdą maszynę wirtualną w zestawie dostępności, a następnie wybierz pozycję **Uruchom** na stronie dla każdej maszyny wirtualnej. 


## <a name="add-existing-vm-to-placement-group"></a>Dodaj istniejącą maszynę wirtualną do grupy umieszczania 


1. Na stronie maszyny wirtualnej wybierz pozycję **Zatrzymaj**.
1. Gdy stan maszyny wirtualnej zostanie wyświetlony jako **zatrzymana (bez przydziału)**, wybierz pozycję **Konfiguracja** w menu po lewej stronie.
1. W obszarze **Grupa położenia zbliżeniowe** wybierz grupę umieszczania z listy rozwijanej, a następnie wybierz pozycję **Zapisz**.
1. Z menu po lewej stronie wybierz pozycję **Przegląd** , a następnie wybierz pozycję **Uruchom** , aby ponownie uruchomić maszynę wirtualną.

 

## <a name="next-steps"></a>Następne kroki

Można również użyć [Azure PowerShell](proximity-placement-groups.md) do tworzenia grup umieszczania sąsiedztwa.