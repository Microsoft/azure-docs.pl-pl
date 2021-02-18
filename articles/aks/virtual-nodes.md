---
title: Korzystanie z węzłów wirtualnych
titleSuffix: Azure Kubernetes Service
description: Omówienie korzystania z węzła wirtualnego z usługami Azure Kubernetes Services (AKS)
services: container-service
ms.topic: conceptual
ms.date: 02/17/2021
ms.custom: references_regions
ms.openlocfilehash: 3bba1155ec57db67968aec95d1d3386fc6cda006
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100634451"
---
# <a name="create-and-configure-an-azure-kubernetes-services-aks-cluster-to-use-virtual-nodes"></a>Utwórz i skonfiguruj klaster usługi Azure Kubernetes Services (AKS) do korzystania z węzłów wirtualnych

Aby szybko skalować obciążenia aplikacji w klastrze AKS, można użyć węzłów wirtualnych. Dzięki węzłom wirtualnym można szybko zainicjowania obsługi pojemności i płacisz na sekundę w czasie wykonywania. Nie musisz czekać, aż klaster Kubernetes ma wdrażać węzły obliczeniowe maszyny wirtualnej w celu uruchamiania dodatkowych zasobników. Węzły wirtualne są obsługiwane tylko w przypadku systemów i węzłów systemu Linux.

Dodatek Virtual nodes dla AKS jest oparty na [wirtualnej Kubelet][virtual-kubelet-repo]projektu Open Source.

Ten artykuł zawiera omówienie wymagań dotyczących dostępności regionów i sieci dla węzłów wirtualnych, a także znane ograniczenia.

## <a name="regional-availability"></a>Dostępność regionalna

Wszystkie regiony, gdzie ACI obsługuje jednostki SKU sieci wirtualnej, są obsługiwane dla wdrożeń węzłów wirtualnych.

W przypadku dostępnych jednostek SKU procesora i pamięci w każdym regionie Sprawdź [dostępność Azure Container Instances zasobów Azure Container Instances w regionach platformy Azure — grupy kontenerów systemu Linux](../container-instances/container-instances-region-availability.md#linux-container-groups)

## <a name="network-requirements"></a>Wymagania dotyczące sieci

Węzły wirtualne umożliwiają komunikację sieciową między jednostkami, które działają w Azure Container Instances (ACI) i klastrze AKS. W celu zapewnienia tej komunikacji zostanie utworzona podsieć sieci wirtualnej i przypisane uprawnienia delegowane. Węzły wirtualne działają tylko w przypadku klastrów AKS utworzonych przy użyciu *zaawansowanej* sieci (Azure CNI). Domyślnie klastry AKS są tworzone przy użyciu sieci *podstawowej* (korzystającą wtyczki kubenet).

W celu skonfigurowania sieci w programie Azure Container Instances (ACI) musi być wymagany dostęp do punktu końcowego serwera interfejsu API AKS.

## <a name="known-limitations"></a>Znane ograniczenia

Funkcjonalność węzłów wirtualnych jest w dużym stopniu zależna od zestawu funkcji ACI. Poza [przydziałami i limitami dla Azure Container Instances](../container-instances/container-instances-quotas.md)następujące scenariusze nie są jeszcze obsługiwane w przypadku węzłów wirtualnych:

* Używanie nazwy głównej usługi do ściągania obrazów ACR. [Obejście](https://github.com/virtual-kubelet/azure-aci/blob/master/README.md#private-registry) polega na użyciu wpisów [tajnych Kubernetes](https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/#create-a-secret-by-providing-credentials-on-the-command-line)
* [Virtual Network ograniczenia](../container-instances/container-instances-vnet.md) , w tym wirtualne sieci równorzędne, zasady sieci Kubernetes i ruch wychodzący do Internetu za pomocą sieciowych grup zabezpieczeń.
* Inicjuj kontenery
* [Aliasy hostów](https://kubernetes.io/docs/concepts/services-networking/add-entries-to-pod-etc-hosts-with-host-aliases/)
* [Argumenty](../container-instances/container-instances-exec.md#restrictions) dla elementu exec w ACI
* [DaemonSets](concepts-clusters-workloads.md#statefulsets-and-daemonsets) nie będzie wdrażać zasobników w węzłach wirtualnych
* Węzły Wirtualne obsługują planowanie zasobników systemu Linux. Możesz ręcznie zainstalować dostawcę Open Source [Virtual KUBELET ACI](https://github.com/virtual-kubelet/azure-aci) Provider, aby zaplanować kontenery systemu Windows Server na ACI.
* Węzły wirtualne wymagają klastrów AKS z obsługą sieci CNI Azure.
* Użycie zakresów adresów IP autoryzowanych przez serwer API dla AKS.
* Instalowanie woluminu Azure Files udostępnienie [ogólnego przeznaczenia w wersji 1](../storage/common/storage-account-overview.md#types-of-storage-accounts). Postępuj zgodnie z instrukcjami dotyczącymi instalowania [woluminu z udziałem Azure Files](azure-files-volume.md)
* Używanie protokołu IPv6 nie jest obsługiwane.

## <a name="next-steps"></a>Następne kroki

Skonfiguruj węzły wirtualne dla klastrów:

- [Tworzenie węzłów wirtualnych przy użyciu interfejsu wiersza polecenia platformy Azure](virtual-nodes-cli.md)
- [Tworzenie węzłów wirtualnych przy użyciu portalu w usłudze Azure Kubernetes Services (AKS)](virtual-nodes-portal.md)

Węzły wirtualne są często jednym składnikiem rozwiązania do skalowania w AKS. Aby uzyskać więcej informacji na temat skalowania rozwiązań, zobacz następujące artykuły:

- [Korzystanie z funkcji automatycznego skalowania w Kubernetes w poziomie][aks-hpa]
- [Korzystanie z automatycznego skalowania klastra Kubernetes][aks-cluster-autoscaler]
- [Zapoznaj się z przykładem skalowania automatycznego dla węzłów wirtualnych][virtual-node-autoscale]
- [Przeczytaj więcej na temat biblioteki Open Source Kubelet wirtualnej][virtual-kubelet-repo]

<!-- LINKS - external -->
[aks-hpa]: tutorial-kubernetes-scale.md
[aks-cluster-autoscaler]: ./cluster-autoscaler.md
[virtual-node-autoscale]: https://github.com/Azure-Samples/virtual-node-autoscale
[virtual-kubelet-repo]: https://github.com/virtual-kubelet/virtual-kubelet
