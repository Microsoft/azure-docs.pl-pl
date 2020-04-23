---
title: Tworzenie grupy umieszczania w sąsiedztwie przy użyciu portalu
description: Dowiedz się, jak utworzyć grupę umieszczania w sąsiedztwie przy użyciu Azure Portal.
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 10/30/2019
ms.author: cynthn
ms.openlocfilehash: aaecfbd14289840e795c6323737877e267586e16
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82098649"
---
# <a name="create-a-proximity-placement-group-using-the-portal"></a>Tworzenie grupy umieszczania w sąsiedztwie przy użyciu portalu

Aby zapewnić, że maszyny wirtualne będą możliwie jak najbliżej, osiągając najniższe możliwe opóźnienie, należy wdrożyć je w obrębie [grupy umieszczania sąsiedztwa](co-location.md#proximity-placement-groups).

Grupa umieszczania bliskości jest grupą logiczną używaną w celu upewnienia się, że zasoby obliczeniowe platformy Azure znajdują się fizycznie blisko siebie. Grupy umieszczania zbliżeniowe są przydatne w przypadku obciążeń, w których jest wymagane małe opóźnienia.


## <a name="create-the-proximity-placement-group"></a>Tworzenie grupy umieszczania zbliżeniowe

1. W polu wyszukiwania wpisz **bliską grupę umieszczania** .
1. W obszarze **usługi** w wynikach wyszukiwania wybierz pozycję **grupy położenia zbliżeniowe**.
1. Na stronie **grupy umieszczania sąsiedztwa** wybierz pozycję **Dodaj**.
1. Na karcie **podstawy** w obszarze **szczegóły projektu**upewnij się, że wybrano poprawną subskrypcję.
1. W obszarze **Grupa zasobów** wybierz pozycję **Utwórz nową** , aby utworzyć nową grupę, lub wybierz istniejącą grupę zasobów z listy rozwijanej.
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




## <a name="next-steps"></a>Następne kroki

Można również użyć [Azure PowerShell](proximity-placement-groups.md) do tworzenia grup umieszczania sąsiedztwa.

