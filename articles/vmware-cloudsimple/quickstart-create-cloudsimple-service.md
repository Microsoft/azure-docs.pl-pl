---
title: 'Szybki Start: Tworzenie usługi VMware CloudSimple'
titleSuffix: Azure VMware Solution by CloudSimple
description: Dowiedz się, jak utworzyć usługę CloudSimple, węzły zakupu i rezerwowe węzły
author: sharaths-cs
ms.author: dikamath
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 14df0f131aaef8a4c24e2d1eb242a9b440e7c7b0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "86507594"
---
# <a name="quickstart---create-azure-vmware-solution-by-cloudsimple-service"></a>Szybki Start — Tworzenie rozwiązania VMware platformy Azure według usługi CloudSimple

Aby rozpocząć, Utwórz rozwiązanie Azure VMware przez CloudSimple w Azure Portal.

## <a name="vmware-solution-by-cloudsimple---service-overview"></a>Rozwiązanie VMware według CloudSimple — Omówienie usługi

Usługa CloudSimple umożliwia korzystanie z rozwiązań VMware platformy Azure przez CloudSimple.  Tworzenie usługi pozwala na inicjowanie obsługi węzłów, rezerwowanie węzłów i tworzenie chmur prywatnych.  Należy dodać usługę CloudSimple w każdym regionie świadczenia usługi Azure, w którym jest dostępna usługa CloudSimple.  Usługa definiuje sieć brzegową rozwiązania Azure VMware przez CloudSimple.  Ta sieć brzegowa jest używana w przypadku usług, które obejmują sieci VPN, ExpressRoute i łączność z Internetem z chmurami prywatnymi.

Aby dodać usługę CloudSimple, należy utworzyć podsieć bramy. Podsieć bramy jest używana podczas tworzenia sieci brzegowej i wymaga bloku/28 CIDR. Przestrzeń adresowa podsieci bramy musi być unikatowa. Nie może pokrywać się z żadnym z lokalnych przestrzeni adresowych sieci ani przestrzenią adresową sieci wirtualnej platformy Azure.

## <a name="before-you-begin"></a>Zanim rozpoczniesz

Przydziel blok CIDR/28 dla podsieci bramy.  Podsieć bramy jest wymagana dla usługi CloudSimple i jest unikatowa dla regionu, w którym została utworzona. Podsieć bramy jest używana dla rozwiązań VMware platformy Azure przez usługi sieciowe CloudSimple Edge i wymaga bloków CIDR/28. Przestrzeń adresowa podsieci bramy musi być unikatowa. Nie może pokrywać się z żadną siecią, która komunikuje się ze środowiskiem CloudSimple.  Sieci, które komunikują się z usługą CloudSimple, obejmują sieci lokalne i sieci wirtualne platformy Azure.

Przejrzyj [wymagania wstępne dotyczące sieci](cloudsimple-network-checklist.md). 

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).

## <a name="create-the-service"></a>Tworzenie usługi

1. Wybierz pozycję **Wszystkie usługi**.
2. Wyszukaj **usługę CloudSimple**.

    ![Wyszukaj usługę CloudSimple](media/create-cloudsimple-service-search.png)

3. Wybierz pozycję **CloudSimple Services**.
4. Kliknij przycisk **Dodaj** , aby utworzyć nową usługę.

    ![Dodaj usługę CloudSimple](media/create-cloudsimple-service-add.png)

5. Wybierz subskrypcję, w której chcesz utworzyć usługę CloudSimple.
6. Wybierz grupę zasobów dla usługi. Aby dodać nową grupę zasobów, kliknij pozycję **Utwórz nową**.
7. Wprowadź nazwę identyfikującą usługę.
8. Wprowadź wartość CIDR dla bramy usługi. Określ podsieć/28, która nie nakłada się na żadną z podsieci lokalnych, podsieci platformy Azure lub podsieci planowanych CloudSimple. Po utworzeniu usługi nie można zmienić CIDR.

    ![Tworzenie usługi CloudSimple](media/create-cloudsimple-service.png)

9. Kliknij przycisk **OK**.

Usługa zostanie utworzona i dodana do listy usług.

## <a name="provision-nodes"></a>Aprowizowanie węzłów

Aby skonfigurować funkcję płatność zgodnie z rzeczywistym użyciem dla środowiska chmury prywatnej CloudSimple, należy najpierw udostępnić węzły w Azure Portal.

1. Wybierz pozycję **Wszystkie usługi**.
2. Wyszukaj **węzły CloudSimple**.

    ![Wyszukaj węzły CloudSimple](media/create-cloudsimple-node-search.png)

3. Wybierz **węzły CloudSimple**.
4. Kliknij przycisk **Dodaj** , aby utworzyć węzły.

    ![Dodaj węzły CloudSimple](media/create-cloudsimple-node-add.png)

5. Wybierz subskrypcję, w której chcesz udostępnić węzły CloudSimple.
6. Wybierz grupę zasobów dla węzłów. Aby dodać nową grupę zasobów, kliknij pozycję **Utwórz nową**.
7. Wprowadź prefiks, aby zidentyfikować węzły.
8. Wybierz lokalizację dla zasobów węzła.
9. Wybierz dedykowaną lokalizację do hostowania zasobów węzła.
10. Wybierz [Typ węzła](cloudsimple-node.md).
11. Wybierz liczbę węzłów do aprowizacji.
12. Wybierz pozycję **Przejrzyj i utwórz**.
13. Przejrzyj ustawienia. Aby zmodyfikować wszystkie ustawienia, kliknij przycisk **Wstecz**.
14. Wybierz przycisk **Utwórz**.

## <a name="next-steps"></a>Następne kroki

* [Utwórz chmurę prywatną i skonfiguruj środowisko](quickstart-create-private-cloud.md)
* Dowiedz się więcej o [usłudze CloudSimple](./cloudsimple-service.md)
