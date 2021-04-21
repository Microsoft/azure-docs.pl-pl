---
title: Dostosowywanie tras zdefiniowanych przez użytkownika w Azure Kubernetes Service (AKS)
description: Dowiedz się, jak zdefiniować niestandardową trasę ruchu wychodzącego w Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 06/29/2020
ms.openlocfilehash: e9433978c8ee855ec66901c7692e4d2b59261fd3
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773049"
---
# <a name="customize-cluster-egress-with-a-user-defined-route"></a>Dostosowywanie ruchu wychodzącego klastra za pomocą User-Defined trasy

Ruch wychodzący z klastra usługi AKS można dostosować do określonych scenariuszy. Domyślnie usługę AKS aprowizuje standardową Load Balancer SKU do skonfigurowania i obsługi dla danych wychodzących. Jednak konfiguracja domyślna może nie spełniać wymagań wszystkich scenariuszy, jeśli publiczne ip są niedozwolone lub dla danych wychodzących są wymagane dodatkowe przeskoki.

W tym artykule opisano sposób dostosowywania trasy ruchu wychodzącego klastra do obsługi niestandardowych scenariuszy sieciowych, takich jak te, które nie zezwalają na publiczne ip i wymagają, aby klaster znajduje się za wirtualnym urządzeniem sieciowym (WUS).

## <a name="prerequisites"></a>Wymagania wstępne
* Interfejs wiersza polecenia platformy Azure w wersji 2.0.81 lub nowszej
* Interfejs API w wersji `2020-01-01` lub większej


## <a name="limitations"></a>Ograniczenia
* Typ OutboundType można zdefiniować tylko w czasie tworzenia klastra i nie można go później zaktualizować.
* Ustawienie `outboundType` wymaga klastrów AKS z `vm-set-type` wartościami i `VirtualMachineScaleSets` `load-balancer-sku` `Standard` .
* Ustawienie wartości na wartość wymaga trasy zdefiniowanej `outboundType` `UDR` przez użytkownika z prawidłową łącznością wychodzącą dla klastra.
* Ustawienie wartości oznacza, że źródłowy adres IP ruchu przychodzącego kierowanego do usługi równoważenia obciążenia może nie być zgodne z wychodzącym adresem docelowym `outboundType` `UDR` ruchu wychodzącego klastra. 

## <a name="overview-of-outbound-types-in-aks"></a>Omówienie typów ruchu wychodzącego w ucieku AKS

Klaster usługi AKS można dostosować za pomocą `outboundType` unikatowego typu `loadBalancer` lub `userDefinedRouting` .

> [!IMPORTANT]
> Typ ruchu wychodzącego ma wpływ tylko na ruch wychodzący klastra. Aby uzyskać więcej informacji, zobacz [konfigurowanie kontrolerów ruchu wychodzącego.](ingress-basic.md)

> [!NOTE]
> Możesz użyć własnej tabeli tras [z trasą][byo-route-table] UDR i siecią kubenet. Upewnij się, że tożsamość klastra (jednostki usługi lub tożsamość zarządzana) ma uprawnienia współautora do niestandardowej tabeli tras.

### <a name="outbound-type-of-loadbalancer"></a>Typ ruchu wychodzącego loadBalancer

Jeśli `loadBalancer` jest ustawiona, aKS automatycznie ukończy następującą konfigurację. Równoważenie obciążenia jest używane do wychodzącego za pośrednictwem publicznego adresu IP przypisanego do usługi AKS. Typ ruchu wychodzącego obsługuje usługi typu Kubernetes, które oczekują ruchu wychodzącego z usługi równoważenia obciążenia utworzonej `loadBalancer` przez dostawcę zasobów usługi `loadBalancer` AKS.

Następująca konfiguracja jest wykonywana przez usługę AKS.
   * Publiczny adres IP jest aprowowany dla wychodzącego klastra.
   * Publiczny adres IP jest przypisywany do zasobu usługi równoważenia obciążenia.
   * Pule zaplecza dla usługi równoważenia obciążenia są ustawiane dla węzłów agentów w klastrze.

Poniżej przedstawiono topologię sieci wdrożoną domyślnie w klastrach usługi AKS, które używają `outboundType` elementu `loadBalancer` .

![Diagram przedstawiający ruch przychodzący I P i wychodzący I P, gdzie ruch przychodzący I P kieruje ruch do usługi równoważenia obciążenia, która kieruje ruch do i z klastra wewnętrznego i innego ruchu do ruchu wychodzącego I P, który kieruje ruch do Internetu, M C R, wymaganych usług platformy Azure i płaszczyzny sterowania A K S.](media/egress-outboundtype/outboundtype-lb.png)

### <a name="outbound-type-of-userdefinedrouting"></a>Typ ruchu wychodzącego userDefinedRouting

> [!NOTE]
> Korzystanie z typu ruchu wychodzącego jest zaawansowanym scenariuszem sieciowym i wymaga odpowiedniej konfiguracji sieci.

Jeśli `userDefinedRouting` ta wartość jest ustawiona, usługi AKS nie skonfiguruje automatycznie ścieżek ruchu wychodzącego. Konfiguracja dla danych wychodzących musi zostać wykonana przez Ciebie.

Klaster usługi AKS należy wdrożyć w istniejącej sieci wirtualnej z wcześniej skonfigurowaną podsiecią, ponieważ w przypadku nieuzyskania architektury standardowego równoważenia obciążenia (SLB) należy ustanowić jawny ruch wychodzący. W związku z tym ta architektura wymaga jawnego wysyłania ruchu wychodzącego do urządzenia, takiego jak zapora, brama lub serwer proxy, aby umożliwić tłumaczenie adresów sieciowych (NAT) za pomocą publicznego adresu IP przypisanego do standardowego urządzenia lub usługi równoważenia obciążenia.

#### <a name="load-balancer-creation-with-userdefinedrouting"></a>Tworzenie modułów równoważenia obciążenia za pomocą userDefinedRouting

Klastry usługi AKS z typem ruchu wychodzącego UDR otrzymują standardowy usługę równoważenia obciążenia tylko wtedy, gdy jest wdrożona pierwsza usługa Kubernetes typu "loadBalancer". Równoważenie obciążenia jest konfigurowane przy użyciu publicznego adresu IP dla żądań *przychodzących* i puli zaplecza dla *żądań przychodzących.* Reguły ruchu przychodzącego są konfigurowane przez dostawcę chmury platformy Azure, ale żaden publiczny adres **IP** ruchu wychodzącego ani reguły ruchu wychodzącego nie są konfigurowane ze względu na typ ruchu wychodzącego UDR. Twoja UDR będzie nadal jedynym źródłem ruchu wychodzącego.

Za usługi równoważenia obciążenia platformy Azure nie są naliczane [opłaty, dopóki reguła nie zostanie umieszczona.](https://azure.microsoft.com/pricing/details/load-balancer/)

## <a name="deploy-a-cluster-with-outbound-type-of-udr-and-azure-firewall"></a>Wdrażanie klastra z typem ruchu wychodzącego UDR i Azure Firewall

Aby zilustrować zastosowanie klastra z typem ruchu wychodzącego przy użyciu trasy zdefiniowanej przez użytkownika, klaster można skonfigurować w sieci wirtualnej z Azure Firewall we własnej podsieci. Zobacz ten przykład w przykładzie [ograniczania ruchu wychodzącego za pomocą usługi Azure Firewall.](limit-egress-traffic.md#restrict-egress-traffic-using-azure-firewall)

> [!IMPORTANT]
> Wychodzący typ trasy UDR wymaga trasy dla adresu 0.0.0.0/0 i miejsca docelowego następnego przeskoku urządzenia WUS (wirtualnego urządzenia sieciowego) w tabeli tras.
> Tabela tras ma już domyślną wartość 0.0.0.0/0 do Internetu, bez publicznego adresu IP do SNAT po prostu dodanie tej trasy nie zapewni ruchu wychodzącego. AKS zweryfikuje, czy nie tworzysz trasy 0.0.0.0/0 kierowanej do Internetu, ale zamiast tego do urządzenia WUS lub bramy itp. W przypadku korzystania z typu ruchu wychodzącego UDR  publiczny adres IP usługi równoważenia obciążenia dla żądań przychodzących nie jest tworzony, chyba że skonfigurowano usługę typu *loadbalancer.* Publiczny adres IP dla żądań **wychodzących nigdy** nie jest tworzony przez usługę AKS, jeśli ustawiono typ ruchu wychodzącego UDR.

## <a name="next-steps"></a>Następne kroki

Zobacz [Azure networking UDR overview (Omówienie funkcji UDR dla sieci platformy Azure).](../virtual-network/virtual-networks-udr-overview.md)

Zobacz, [jak utworzyć, zmienić lub usunąć tabelę tras.](../virtual-network/manage-route-table.md)

<!-- LINKS - internal -->
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[byo-route-table]: configure-kubenet.md#bring-your-own-subnet-and-route-table-with-kubenet
