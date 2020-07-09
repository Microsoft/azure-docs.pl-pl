---
title: Usuwanie rozwiązania Azure VMware według chmury prywatnej CloudSimple
description: Opisuje, jak usunąć chmurę prywatną CloudSimple.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/06/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 6bc3e7030c500ea2d6072a1cce0f0b3d9fc62801
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "77024759"
---
# <a name="delete-a-cloudsimple-private-cloud"></a>Usuń chmurę prywatną CloudSimple

CloudSimple zapewnia elastyczność usuwania chmury prywatnej.  Chmura prywatna składa się z co najmniej jednego klastra vSphere. Każdy klaster może zawierać od 3 do 16 węzłów. Po usunięciu chmury prywatnej wszystkie klastry zostaną usunięte.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Usunięcie chmury prywatnej spowoduje usunięcie całej chmury prywatnej.  Wszystkie składniki chmury prywatnej zostaną usunięte.  Aby zachować jakiekolwiek dane, należy wykonać kopię zapasową danych w magazynie lokalnym lub w usłudze Azure Storage.

Składniki chmury prywatnej obejmują:

* CloudSimple węzły
* Maszyny wirtualne
* Sieci VLAN/podsieci
* Wszystkie dane użytkownika przechowywane w chmurze prywatnej
* Wszystkie załączniki reguły zapory do sieci VLAN/podsieci

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).

## <a name="delete-a-private-cloud"></a>Usuwanie chmury prywatnej

1. [Dostęp do portalu CloudSimple](access-cloudsimple-portal.md).

2. Otwórz stronę **zasoby** .

3. Kliknij chmurę prywatną, którą chcesz usunąć.

4. Na stronie Podsumowanie kliknij przycisk **Usuń**.

    ![Usuń chmurę prywatną](media/delete-private-cloud.png)

5. Na stronie Potwierdzenie wprowadź nazwę chmury prywatnej, a następnie kliknij przycisk **Usuń**. 

    ![Usuń chmurę prywatną — Potwierdź](media/delete-private-cloud-confirm.png)

Chmura prywatna jest oznaczona do usunięcia.  Proces usuwania rozpocznie się po trzech godzinach i usuwa chmurę prywatną.

> [!CAUTION]
> Węzły należy usunąć po usunięciu chmury prywatnej.  Pomiary węzłów będą kontynuowane do momentu usunięcia ich z subskrypcji.

## <a name="next-steps"></a>Następne kroki

* [Usuwanie węzłów](delete-nodes.md)
