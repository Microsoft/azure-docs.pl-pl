---
title: Zmniejszanie rozwiązań VMware platformy Azure według chmury prywatnej CloudSimple
description: Dowiedz się, jak dynamicznie zmniejszać chmurę prywatną w programie CloudSimple, usuwając węzeł z istniejącego klastra vSphere lub usuwając cały klaster.
author: sharaths-cs
ms.author: b-shsury
ms.date: 07/01/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 2759b35c9ba521a63eaf54c4c696598d545120b4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88141741"
---
# <a name="shrink-a-cloudsimple-private-cloud"></a>Zmniejsz chmurę prywatną CloudSimple

CloudSimple zapewnia elastyczność dynamicznego zmniejszania liczby chmur prywatnych.  Chmura prywatna składa się z co najmniej jednego klastra vSphere. Każdy klaster może zawierać od 3 do 16 węzłów. Podczas zmniejszania chmury prywatnej można usunąć węzeł z istniejącego klastra lub usunąć cały klaster. 

## <a name="before-you-begin"></a>Zanim rozpoczniesz

Aby zmniejszyć chmurę prywatną, należy spełnić następujące warunki.  Klaster zarządzania (pierwszy klaster) utworzony podczas tworzenia chmury prywatnej nie może zostać usunięty.

* Klaster vSphere musi mieć trzy węzły.  Nie można zmniejszyć klastra z tylko trzema węzłami.
* Łączny zużyty magazyn nie powinien przekraczać całkowitej pojemności po zmniejszeniu poziomu klastra.
* Sprawdź, czy jakiekolwiek reguły usługi Distributed Resource Scheduler (DRS) uniemożliwiają vMotion maszyny wirtualnej.  Jeśli istnieją reguły, wyłącz lub Usuń reguły.  Reguły DRS obejmują reguły koligacji maszyny wirtualnej z hostem.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).

## <a name="shrink-a-private-cloud"></a>Zmniejszanie chmury prywatnej

1. [Dostęp do portalu CloudSimple](access-cloudsimple-portal.md).

2. Otwórz stronę **zasoby** .

3. Kliknij chmurę prywatną, którą chcesz zmniejszyć

4. Na stronie Podsumowanie kliknij przycisk **Zmniejsz**.

    ![Zmniejsz chmurę prywatną](media/shrink-private-cloud.png)

5. Wybierz klaster, który chcesz zmniejszyć lub usunąć. 

    ![Zmniejsz chmurę prywatną — wybierz klaster](media/shrink-private-cloud-select-cluster.png)

6. Wybierz pozycję **Usuń jeden węzeł** lub **Usuń cały klaster**. 

7. Weryfikowanie pojemności klastra

8. Kliknij pozycję **Prześlij** , aby zmniejszyć chmurę prywatną.

Rozpocznie się zmniejszanie chmury prywatnej.  Postęp można monitorować w zadaniach.  Proces zmniejszania może potrwać kilka godzin w zależności od danych, które muszą zostać ponownie zsynchronizowane w sieci vSAN.

> [!NOTE]
> 1. Jeśli zmniejszasz chmurę prywatną przez usunięcie ostatniego lub jedynego klastra w centrum danych, centrum danych nie zostanie usunięte.
> 2. W przypadku wystąpienia naruszenia reguły DRS węzeł nie zostanie usunięty z klastra, a w opisie zadania zostanie wyświetlona reguła DRS w klastrze.    


## <a name="next-steps"></a>Następne kroki

* [Używanie maszyn wirtualnych VMware na platformie Azure](quickstart-create-vmware-virtual-machine.md)
* Dowiedz się więcej o [chmurach prywatnych](cloudsimple-private-cloud.md)
