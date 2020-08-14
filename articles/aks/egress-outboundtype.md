---
title: Dostosowywanie tras zdefiniowanych przez użytkownika (UDR) w usłudze Azure Kubernetes Service (AKS)
description: Informacje o definiowaniu niestandardowej trasy ruchu wychodzącego w usłudze Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.author: juluk
ms.date: 06/29/2020
author: jluk
ms.openlocfilehash: 2ffe9d525e92fa2154889cea43f681a0f31a18ab
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/14/2020
ms.locfileid: "88214219"
---
# <a name="customize-cluster-egress-with-a-user-defined-route"></a>Dostosowywanie ruchu wychodzącego klastra przy użyciu trasy zdefiniowanej przez użytkownika

Ruch wychodzący z klastra AKS można dostosować do określonych scenariuszy. Domyślnie AKS będzie obsługiwać standardową jednostkę Load Balancer SKU, która będzie używana w celu skonfigurowania i użycia dla ruchu wychodzącego. Jednak konfiguracja domyślna może nie spełniać wymagań wszystkich scenariuszy, jeśli publiczne adresy IP są niedozwolone lub dodatkowe przeskoki są wymagane dla ruchu wychodzącego.

W tym artykule opisano sposób dostosowywania trasy ruchu wychodzącego klastra w celu zapewnienia obsługi niestandardowych scenariuszy sieciowych, takich jak te, które uniemożliwiają publiczne adresy IP i wymagają, aby klaster znajduje się za sieciowym urządzeniem wirtualnym (urządzenie WUS).

## <a name="prerequisites"></a>Wymagania wstępne
* Interfejs wiersza polecenia platformy Azure w wersji 2.0.81 lub nowszej
* Wersja interfejsu API `2020-01-01` lub nowsza


## <a name="limitations"></a>Ograniczenia
* Wartości inboundtype można definiować tylko w czasie tworzenia klastra i nie można ich później zaktualizować.
* Ustawienie `outboundType` wymaga klastrów AKS z `vm-set-type` `VirtualMachineScaleSets` i `load-balancer-sku` z `Standard` .
* Ustawienie `outboundType` wartości `UDR` wymaga trasy zdefiniowanej przez użytkownika z prawidłową łącznością wychodzącą dla klastra.
* Ustawienie `outboundType` wartości powoduje `UDR` , że adres IP źródła danych przychodzących kierowany do modułu równoważenia obciążenia może być **niezgodny** z wychodzącym docelowym ruchem wyjściowym klastra.

## <a name="overview-of-outbound-types-in-aks"></a>Przegląd typów wychodzących w AKS

Klaster AKS można dostosować przy użyciu unikatowego `outboundType` typu modułu równoważenia obciążenia lub routingu zdefiniowanego przez użytkownika.

> [!IMPORTANT]
> Typ wychodzący ma wpływ tylko na ruch wyjściowy klastra. Aby uzyskać więcej informacji, zobacz [Konfigurowanie kontrolerów danych wejściowych](ingress-basic.md).

> [!NOTE]
> Możesz użyć własnej [tabeli tras][byo-route-table] z obsługą sieci UDR i korzystającą wtyczki kubenet. Upewnij się, że tożsamość klastra (główna nazwa usługi lub tożsamość zarządzana) ma uprawnienia współautora do niestandardowej tabeli tras.

### <a name="outbound-type-of-loadbalancer"></a>Typ wychodzącego modułu równoważenia obciążenia

Jeśli `loadBalancer` jest ustawiona, AKS automatycznie wykonuje następującą konfigurację. Moduł równoważenia obciążenia jest używany do ruchu wychodzącego przez AKS przypisany publiczny adres IP. Typ wychodzący `loadBalancer` obsługuje usługi Kubernetes Services typu `loadBalancer` , które oczekują wyjście z modułu równoważenia obciążenia utworzonego przez dostawcę zasobów AKS.

Następująca konfiguracja jest wykonywana przez AKS.
   * Publiczny adres IP jest inicjowany dla ruchu wychodzącego klastra.
   * Publiczny adres IP jest przypisywany do zasobu modułu równoważenia obciążenia.
   * Pule zaplecza dla modułu równoważenia obciążenia są konfigurowane dla węzłów agenta w klastrze.

Poniżej znajduje się topologia sieci wdrożona domyślnie w klastrach AKS, która korzysta `outboundType` z programu `loadBalancer` .

![niepowiązanytype — lb](media/egress-outboundtype/outboundtype-lb.png)

### <a name="outbound-type-of-userdefinedrouting"></a>Typ wychodzący userDefinedRouting

> [!NOTE]
> Użycie typu wychodzącego jest zaawansowanym scenariuszem sieci i wymaga odpowiedniej konfiguracji sieci.

Jeśli `userDefinedRouting` jest ustawiona, AKS nie konfiguruje automatycznie ścieżek ruchu wychodzącego. Konfigurację ruchu wychodzącego należy wykonać przez użytkownika.

Klaster AKS musi zostać wdrożony w istniejącej sieci wirtualnej z podsiecią, która została wcześniej skonfigurowana, ponieważ w przypadku braku korzystania ze standardowej architektury modułu równoważenia obciążenia należy ustanowić jawne dane wyjściowe. W związku z tym ta architektura wymaga jawnego wysłania ruchu wychodzącego do urządzenia, takiego jak zapora, Brama lub serwer proxy lub umożliwienie translacji adresów sieciowych (NAT) przez publiczny adres IP przypisany do standardowego modułu równoważenia obciążenia lub urządzenia.

Dostawca zasobów AKS będzie wdrażać usługę równoważenia obciążenia w warstwie Standardowa. Moduł równoważenia obciążenia nie jest skonfigurowany z żadną regułą i [nie powoduje naliczania opłat do momentu, gdy reguła zostanie umieszczona](https://azure.microsoft.com/pricing/details/load-balancer/). Usługa AKS **nie będzie** automatycznie inicjować publicznego adresu IP dla FRONTONU modułu równoważenia obciążenia ani automatycznie konfigurować puli zaplecza modułu równoważenia obciążeń.

## <a name="deploy-a-cluster-with-outbound-type-of-udr-and-azure-firewall"></a>Wdrażanie klastra z typem wychodzącym UDR i zaporą platformy Azure

Aby zilustrować aplikację klastra z typem wychodzącym przy użyciu trasy zdefiniowanej przez użytkownika, klaster można skonfigurować w sieci wirtualnej za pomocą zapory platformy Azure we własnej podsieci. Zapoznaj się z tym przykładem w [przykładzie Ogranicz ruch wychodzący przy użyciu zapory platformy Azure](limit-egress-traffic.md#restrict-egress-traffic-using-azure-firewall).

> [!IMPORTANT]
> Typ wychodzący UDR wymaga trasy dla 0.0.0.0/0 i lokalizacji docelowej następnego przeskoku urządzenie WUS (sieciowe urządzenie wirtualne) w tabeli tras.
> Tabela tras ma już domyślne wartości 0.0.0.0/0 do Internetu, bez publicznego adresu IP do podłączania, po prostu dodanie tej trasy nie spowoduje wypróbowania ruchu wychodzącego. AKS sprawdzi, czy nie utworzysz trasy 0.0.0.0/0 wskazującej Internet, ale zamiast urządzenie WUS lub bramy itd.
> 
> Gdy jest używany typ wychodzący UDR, publiczny adres IP modułu równoważenia obciążenia nie jest tworzony, chyba że jest skonfigurowana usługa typu *równoważenia* obciążenia.

## <a name="next-steps"></a>Następne kroki

Zobacz [Omówienie usługi Azure Network UDR](../virtual-network/virtual-networks-udr-overview.md).

Zobacz [jak utworzyć, zmienić lub usunąć tabelę tras](../virtual-network/manage-route-table.md).

<!-- LINKS - internal -->
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[byo-route-table]: configure-kubenet.md#bring-your-own-subnet-and-route-table-with-kubenet
