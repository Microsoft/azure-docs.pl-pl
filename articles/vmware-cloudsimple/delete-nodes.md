---
title: Usuwanie węzłów dla rozwiązania VMware przez CloudSimple — Azure
description: Dowiedz się, jak usuwać węzły z programu VMWare przy użyciu wdrożenia CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 08/05/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 122e0636f54e066ae86ed2d19cefe5863b026293
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "77024742"
---
# <a name="delete-nodes-from-azure-vmware-solution-by-cloudsimple"></a>Usuwanie węzłów z rozwiązań VMware platformy Azure według CloudSimple

CloudSimple węzły są mierzone po ich utworzeniu.  Aby zatrzymać pomiar węzłów, należy usunąć węzły.  Węzły, które nie są używane w programie Azure Portal, zostaną usunięte.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Węzeł można usunąć tylko w następujących warunkach:

* Chmura prywatna utworzona z węzłami zostanie usunięta.  Aby usunąć chmurę prywatną, zobacz [usuwanie rozwiązania Azure VMware według CloudSimple prywatnej chmury](delete-private-cloud.md).
* Węzeł został usunięty z chmury prywatnej, zmniejszając chmurę prywatną.  Aby zmniejszyć chmurę prywatną, zobacz artykuł [zmniejszanie rozwiązań VMware platformy Azure według chmury prywatnej CloudSimple](shrink-private-cloud.md).

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).

## <a name="delete-cloudsimple-node"></a>Usuń węzeł CloudSimple

1. Wybierz pozycję **Wszystkie usługi**.

2. Wyszukaj **węzły CloudSimple**.

   ![Wyszukaj węzły CloudSimple](media/create-cloudsimple-node-search.png)

3. Wybierz **węzły CloudSimple**.

4. Wybierz węzły, które nie należą do chmury prywatnej, aby usunąć.  Kolumna **Nazwa chmury prywatnej** zawiera nazwę chmury prywatnej, do której należy węzeł.  Jeśli węzeł nie jest używany przez chmurę prywatną, wartość będzie pusta. 

    ![Wybierz węzły CloudSimple](media/select-delete-cloudsimple-node.png)

> [!NOTE]
> Można usunąć tylko węzły, które nie są częścią chmury prywatnej.

## <a name="next-steps"></a>Następne kroki

* Informacje o [chmurze prywatnej](cloudsimple-private-cloud.md)
