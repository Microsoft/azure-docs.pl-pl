---
title: Usuwanie węzłów dla rozwiązania VMware przez CloudSimple — Azure
description: Dowiedz się, jak usuwać węzły z programu VMWare przy użyciu wdrożenia CloudSimple. CloudSimple węzły są taryfowe. Usuń węzły, które nie są używane w Azure Portal.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/05/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 569bc6350b1bfa01228d49d28a1d12e2ab62f6f0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88142268"
---
# <a name="delete-nodes-from-azure-vmware-solution-by-cloudsimple"></a>Usuwanie węzłów z rozwiązań VMware platformy Azure według CloudSimple

CloudSimple węzły są mierzone po ich utworzeniu.  Aby zatrzymać pomiar węzłów, należy usunąć węzły.  Węzły, które nie są używane w programie Azure Portal, zostaną usunięte.

## <a name="before-you-begin"></a>Zanim rozpoczniesz

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
