---
title: 'Szybki start: tworzenie usługi VMware CloudSimple'
titleSuffix: Azure VMware Solution by CloudSimple
description: Dowiedz się, jak utworzyć usługę CloudSimple, węzły zakupu i węzły rezerwy
author: sharaths-cs
ms.author: dikamath
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d7c137a75c0a021aa8bca3aec23da6c4d1ada300
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81868030"
---
# <a name="quickstart---create-azure-vmware-solution-by-cloudsimple-service"></a>Szybki start — tworzenie rozwiązania Azure VMware przez usługę CloudSimple

Aby rozpocząć, utwórz rozwiązanie Azure VMware przez CloudSimple w witrynie Azure portal.

## <a name="vmware-solution-by-cloudsimple---service-overview"></a>VMware Solution by CloudSimple — omówienie usługi

Usługa CloudSimple umożliwia korzystanie z rozwiązania Azure VMware przez CloudSimple.  Tworzenie usługi umożliwia aprowizowanie węzłów, zarezerwowanie węzłów i tworzenie chmur prywatnych.  Dodaj usługę CloudSimple w każdym regionie platformy Azure, w którym jest dostępna usługa CloudSimple.  Usługa definiuje sieć brzegową rozwiązania Azure VMware przez CloudSimple.  Ta sieć brzegowa jest używana w usługach obejmujących sieć VPN, usługę ExpressRoute i łączność z Internetem z chmurami prywatnymi.

Aby dodać usługę CloudSimple, należy utworzyć podsieć bramy. Podsieć bramy jest używana podczas tworzenia sieci brzegowej i wymaga bloku CIDR /28. Przestrzeń adresowa podsieci bramy musi być unikatowa. Nie może nakładać się na żadne lokalne przestrzenie adresowe sieciowe ani przestrzeń adresową sieci wirtualnej platformy Azure.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Przydziel blok CIDR /28 dla podsieci bramy.  Podsieć bramy jest wymagana dla usługi CloudSimple i jest unikatowa dla regionu, w którym została utworzona. Podsieć bramy jest używana dla usługi Azure VMware Solution by CloudSimple usługi sieciowe usługi brzegowe i wymaga /28 bloku CIDR. Przestrzeń adresowa podsieci bramy musi być unikatowa. Nie może pokrywać się z żadną siecią, która komunikuje się ze środowiskiem CloudSimple.  Sieci, które komunikują się z CloudSimple obejmują sieci lokalne i sieci wirtualne platformy Azure.

Przejrzyj [wymagania wstępne sieci .](cloudsimple-network-checklist.md) 

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).

## <a name="create-the-service"></a>Tworzenie usługi

1. Wybierz pozycję **Wszystkie usługi**.
2. Wyszukaj **usługę CloudSimple**.

    ![Usługa Search CloudSimple](media/create-cloudsimple-service-search.png)

3. Wybierz **pozycję CloudSimple Services**.
4. Kliknij **przycisk Dodaj,** aby utworzyć nową usługę.

    ![Dodaj usługę CloudSimple](media/create-cloudsimple-service-add.png)

5. Wybierz subskrypcję, w której chcesz utworzyć usługę CloudSimple.
6. Wybierz grupę zasobów dla usługi. Aby dodać nową grupę zasobów, kliknij przycisk **Utwórz nowy**.
7. Wprowadź nazwę, aby zidentyfikować usługę.
8. Wprowadź cidr bramy usługi. Określ podsieć /28, która nie pokrywa się z żadnymi podsieciami lokalnymi, podsieciami platformy Azure lub planowanymi podsieciami CloudSimple. Nie można zmienić CIDR po utworzeniu usługi.

    ![Tworzenie usługi CloudSimple](media/create-cloudsimple-service.png)

9. Kliknij przycisk **OK**.

Usługa jest tworzona i dodawana do listy usług.

## <a name="provision-nodes"></a>Aprowizowanie węzłów

Aby skonfigurować pojemność płatności zgodnie z rzeczywistym idziemy dla środowiska cloudsimple private cloud, najpierw inicjuj węzełe w witrynie Azure portal.

1. Wybierz pozycję **Wszystkie usługi**.
2. Wyszukaj **węzły CloudSimple**.

    ![Szukaj węzłów CloudSimple](media/create-cloudsimple-node-search.png)

3. Wybierz **opcję Węzły CloudSimple**.
4. Kliknij **przycisk Dodaj,** aby utworzyć węzły.

    ![Dodawanie węzłów CloudSimple](media/create-cloudsimple-node-add.png)

5. Wybierz subskrypcję, w której chcesz aprowizować węzły CloudSimple.
6. Wybierz grupę zasobów dla węzłów. Aby dodać nową grupę zasobów, kliknij przycisk **Utwórz nowy**.
7. Wprowadź prefiks, aby zidentyfikować węzły.
8. Wybierz lokalizację dla zasobów węzła.
9. Wybierz dedykowaną lokalizację, aby obsługiwać zasoby węzła.
10. Wybierz [typ węzła](cloudsimple-node.md).
11. Wybierz liczbę węzłów do zainicjowania.
12. Wybierz **pozycję Recenzja + Utwórz**.
13. Przejrzyj ustawienia. Aby zmodyfikować dowolne ustawienia, kliknij przycisk **Poprzedni**.
14. Wybierz pozycję **Utwórz**.

## <a name="next-steps"></a>Następne kroki

* [Tworzenie chmury prywatnej i konfigurowanie środowiska](quickstart-create-private-cloud.md)
* Dowiedz się więcej o [usłudze CloudSimple](https://docs.microsoft.com/azure/vmware-cloudsimple/cloudsimple-service)
