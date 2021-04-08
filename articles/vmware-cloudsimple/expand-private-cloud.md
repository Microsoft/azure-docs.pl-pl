---
title: Rozszerzanie rozwiązania Azure VMware według chmury prywatnej CloudSimple
description: Opisuje, jak rozszerzyć istniejącą chmurę prywatną CloudSimple w celu dodania pojemności w istniejącym lub nowym klastrze.
author: Ajayan1008
ms.author: v-hborys
ms.date: 06/06/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: c469ea3f104e71eb80010d4a5cd421eab6184fcf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97895862"
---
# <a name="expand-a-cloudsimple-private-cloud"></a>Rozwiń chmurę prywatną CloudSimple

CloudSimple zapewnia elastyczność dynamicznego rozszerzania chmury prywatnej. Możesz zacząć od mniejszej konfiguracji, a następnie rozwijać ją, gdy potrzebujesz większej pojemności. Można też utworzyć chmurę prywatną na podstawie bieżących potrzeb, a następnie rozwijać ją w miarę wzrostu zużycia.

Chmura prywatna składa się z co najmniej jednego klastra vSphere. Każdy klaster może zawierać od 3 do 16 węzłów.  Podczas rozszerzania chmury prywatnej należy dodać węzły do istniejącego klastra lub utworzyć nowy klaster. Aby rozszerzyć istniejący klaster, dodatkowe węzły muszą być tego samego typu (SKU) jak istniejące węzły. W przypadku tworzenia nowego klastra węzły mogą być innego typu. Aby uzyskać więcej informacji na temat limitów chmur prywatnych, zobacz sekcja limity w artykule [Omówienie chmury prywatnej CloudSimple](cloudsimple-private-cloud.md) .

Chmura prywatna jest tworzona przy użyciu domyślnego **centrum** danych w programie vCenter.  Każde centrum danych służy jako jednostka zarządzania najwyższego poziomu.  W przypadku nowego klastra CloudSimple zapewnia możliwość dodania do istniejącego centrum danych lub utworzenia nowego centrum danych.

W ramach nowej konfiguracji klastra CloudSimple konfiguruje infrastrukturę VMware.  Ustawienia obejmują ustawienia magazynu dla grup dysków sieci vSAN, wysokiej dostępności VMware i Distributed Resource Scheduler (DRS).

Chmurę prywatną można rozszerzyć wiele razy. Rozszerzanie można wykonać tylko wtedy, gdy użytkownik pozostaje w ogólnym limicie węzłów. Za każdym razem, gdy rozszerzasz chmurę prywatną, którą chcesz dodać do istniejącego klastra, lub Utwórz nową.

## <a name="before-you-begin"></a>Zanim rozpoczniesz

Aby można było rozszerzyć chmurę prywatną, węzły muszą być obsługiwane.  Aby uzyskać więcej informacji o węzłach aprowizacji, zobacz temat [Inicjowanie obsługi węzłów dla rozwiązań VMware przez CloudSimple — artykuł platformy Azure](create-nodes.md) .  Do utworzenia nowego klastra potrzebne są co najmniej trzy dostępne węzły tej samej jednostki SKU.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).

## <a name="expand-a-private-cloud"></a>Rozszerzanie chmury prywatnej

1. [Dostęp do portalu CloudSimple](access-cloudsimple-portal.md).

2. Otwórz stronę **zasoby** i wybierz chmurę prywatną, dla której chcesz rozwinąć.

3. W sekcji Podsumowanie kliknij przycisk **Rozwiń**.

    ![Rozwiń chmurę prywatną](media/resources-expand-private-cloud.png)

4. Wybierz, czy chcesz rozszerzyć istniejący klaster, czy utworzyć nowy klaster vSphere. Podczas wprowadzania zmian informacje podsumowujące na stronie zostaną zaktualizowane.

    * Aby rozwinąć istniejący klaster, kliknij przycisk **Rozwiń istniejący klaster**. Wybierz klaster, który chcesz rozwinąć, a następnie wprowadź liczbę węzłów do dodania. Każdy klaster może zawierać maksymalnie 16 węzłów.
    * Aby dodać nowy klaster, kliknij przycisk **Utwórz nowy klaster**. Wprowadź nazwę klastra. Wybierz istniejące centrum danych lub wprowadź nazwę, aby utworzyć nowe centrum danych. Wybierz typ węzła. Możesz wybrać inny typ węzła podczas tworzenia nowego klastra vSphere, ale nie podczas rozszerzania istniejącego klastra vSphere. Wybierz liczbę węzłów. Każdy nowy klaster musi mieć co najmniej trzy węzły.

    ![Rozwiń chmurę prywatną — Dodaj węzły](media/resources-expand-private-cloud-add-nodes.png)

5. Kliknij pozycję **Prześlij** , aby rozwinąć chmurę prywatną.

## <a name="next-steps"></a>Następne kroki

* [Używanie maszyn wirtualnych VMware na platformie Azure](quickstart-create-vmware-virtual-machine.md)
* Dowiedz się więcej o [chmurach prywatnych](cloudsimple-private-cloud.md)