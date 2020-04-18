---
title: Nieautoryzowane zakresy adresów IP serwera interfejsu API w usłudze Azure Kubernetes (AKS)
description: Dowiedz się, jak zabezpieczyć klaster przy użyciu zakresu adresów IP w celu uzyskania dostępu do serwera interfejsu API w usłudze Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 11/05/2019
ms.openlocfilehash: 570d842409fc019d24446e091f83402f4c288d7c
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2020
ms.locfileid: "81640051"
---
# <a name="secure-access-to-the-api-server-using-authorized-ip-address-ranges-in-azure-kubernetes-service-aks"></a>Bezpieczny dostęp do serwera interfejsu API przy użyciu autoryzowanych zakresów adresów IP w usłudze Azure Kubernetes Service (AKS)

W usłudze Kubernetes serwer interfejsu API odbiera żądania wykonywania akcji w klastrze, takich jak tworzenie zasobów lub skalowanie liczby węzłów. Serwer interfejsu API to centralny sposób interakcji z klastrem i zarządzania nim. Aby zwiększyć bezpieczeństwo klastra i zminimalizować ataki, serwer interfejsu API powinien być dostępny tylko z ograniczonego zestawu zakresów adresów IP.

W tym artykule pokazano, jak używać zakresów autoryzowanych adresów IP serwera interfejsu API w celu ograniczenia adresów IP i cidr, które mogą uzyskać dostęp do płaszczyzny kontroli dostępu.

> [!IMPORTANT]
> W przypadku nowych klastrów zakresy adresów IP autoryzowanych serwerów interfejsu API są obsługiwane tylko w modułu równoważenia obciążenia *standardowej* jednostki SKU. Istniejące klastry z skonfigurowanym podstawowym *modułem* równoważenia obciążenia jednostki SKU i autoryzowanym zakresem adresów IP serwera INTERFEJSU API będą nadal działać tak, jak jest, ale nie można ich migrować do modułu równoważenia obciążenia *standardowej* jednostki SKU. Te istniejące klastry będą również nadal działać, jeśli ich wersja Kubernetes lub płaszczyzna sterowania zostaną uaktualnione.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Utworzone przez serwer interfejsu API zakresy adresów IP działają tylko w przypadku nowych klastrów AKS. W tym artykule pokazano, jak utworzyć klaster AKS przy użyciu interfejsu wiersza polecenia platformy Azure.

Potrzebujesz zainstalowanego i skonfigurowany interfejsu wiersza polecenia platformy Azure w wersji 2.0.76 lub nowszej. Uruchom polecenie  `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie przeprowadzenie instalacji lub uaktualnienia, zobacz  [Instalowanie interfejsu wiersza polecenia platformy Azure][install-azure-cli].

## <a name="overview-of-api-server-authorized-ip-ranges"></a>Omówienie zakresów autoryzowanych adresów IP serwera INTERFEJSU API

Serwer interfejsu API usługi Kubernetes jest jak podstawowe interfejsy API kubernetes są udostępniane. Ten składnik zapewnia interakcję dla narzędzi `kubectl` do zarządzania, takich jak lub pulpit nawigacyjny Kubernetes. Usługa AKS udostępnia wzorzec klastra z jedną dzierżawą z dedykowanym serwerem interfejsu API. Domyślnie serwerowi INTERFEJSU API jest przypisywany publiczny adres IP i należy kontrolować dostęp za pomocą kontroli dostępu opartego na rolach (RBAC).

Aby zabezpieczyć dostęp do publicznie dostępnego samolotu sterującego AKS / serwera API, można włączyć i używać autoryzowanych zakresów adresów IP. Te autoryzowane zakresy adresów IP umożliwiają tylko zdefiniowane zakresy adresów IP do komunikowania się z serwerem interfejsu API. Żądanie skierowane do serwera interfejsu API z adresu IP, który nie jest częścią tych autoryzowanych zakresów adresów IP, jest blokowane. Kontynuuj używanie funkcji RBAC do autoryzowania użytkowników i żądanych przez nie akcji.

Aby uzyskać więcej informacji na temat serwera interfejsu API i innych składników klastra, zobacz [Podstawowe pojęcia kubernetes dla usługi AKS][concepts-clusters-workloads].

## <a name="create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled"></a>Tworzenie klastra AKS z włączonymi zakresami autoryzowanych adresów IP serwera API

Nieautoryzowane zakresy adresów IP serwera INTERFEJSU API działają tylko w przypadku nowych klastrów AKS. Utwórz klaster przy użyciu [az aks utworzyć][az-aks-create] i określić *parametr --api-server-authorized-ip-ranges,* aby zapewnić listę autoryzowanych zakresów adresów IP. Te zakresy adresów IP są zazwyczaj zakresami adresów używanymi przez sieci lokalne lub publiczne adresy IP. Po określeniu zakresu CIDR należy rozpocząć od pierwszego adresu IP w zakresie. Na przykład *137.117.106.90/29* jest prawidłowym zakresem, ale upewnij się, że określono pierwszy adres IP w zakresie, na przykład *137.117.106.88/29*.

> [!IMPORTANT]
> Domyślnie klaster używa [standardowego modułu równoważenia obciążenia jednostki SKU,][standard-sku-lb] którego można użyć do skonfigurowania bramy wychodzącej. Po włączeniu zakresów autoryzowanych adresów IP serwera interfejsu API podczas tworzenia klastra publiczny adres IP dla klastra jest również domyślnie dozwolony oprócz określanych zakresów. Jeśli określisz *""* lub brak wartości dla *--api-server-authorized-ip-ranges,* nieautoryzowane zakresy adresów IP serwera INTERFEJSU API zostaną wyłączone. Należy zauważyć, że jeśli używasz programu PowerShell, użyj *--api-server-authorized-ip-ranges=""* (ze znakiem równości), aby uniknąć problemów z analizowaniem.

Poniższy przykład tworzy klaster z jednym węzłem o nazwie *myAKSCluster* w grupie zasobów o nazwie *myResourceGroup* z włączonymi zakresami adresów IP autoryzowanych serwera INTERFEJSU API. Dopuszczalne zakresy adresów IP to *73.140.245.0/24:*

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --vm-set-type VirtualMachineScaleSets \
    --load-balancer-sku standard \
    --api-server-authorized-ip-ranges 73.140.245.0/24 \
    --generate-ssh-keys
```

> [!NOTE]
> Należy dodać te zakresy do listy dozwolonych:
> - Publiczny adres IP zapory
> - Dowolny zakres reprezentujący sieci, z których będzie administrowany klaster
> - Jeśli używasz usługi Azure Dev Spaces w klastrze AKS, musisz zezwolić [na dodatkowe zakresy na podstawie regionu][dev-spaces-ranges].

> Górna granica liczby zakresów adresów IP, którą można określić, wynosi 3500. 

### <a name="specify-the-outbound-ips-for-the-standard-sku-load-balancer"></a>Określanie wychodzących usług IP dla standardowego modułu równoważenia obciążenia jednostki SKU

Podczas tworzenia klastra AKS, jeśli określisz wychodzące adresy IP lub prefiksy dla klastra, te adresy lub prefiksy są również dozwolone. Przykład:

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --vm-set-type VirtualMachineScaleSets \
    --load-balancer-sku standard \
    --api-server-authorized-ip-ranges 73.140.245.0/24 \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2> \
    --generate-ssh-keys
```

W powyższym przykładzie wszystkie adresy IP podane w parametrze *--load-balancer-outbound-ip-prefiksy* są dozwolone wraz z adresami IP w *parametrze --api-server-authorized-ip-ranges.*

Alternatywnie można określić parametr *--load-balancer-outbound-ip-prefixes,* aby umożliwić prefiksy IP modułu równoważenia obciążenia wychodzącego.

### <a name="allow-only-the-outbound-public-ip-of-the-standard-sku-load-balancer"></a>Zezwalaj tylko na wychodzący publiczny adres IP standardowego modułu równoważenia obciążenia jednostki SKU

Po włączeniu zakresów adresów IP autoryzowanych serwera interfejsu API podczas tworzenia klastra wychodzący publiczny adres IP dla standardowego modułu równoważenia obciążenia jednostki SKU dla klastra jest również domyślnie dozwolony oprócz określanych zakresów. Aby zezwolić tylko na wychodzący publiczny adres IP standardowego modułu równoważenia obciążenia jednostki SKU, należy użyć *0.0.0.0/32* przy określaniu parametru *--api-server-authorized-ip-ranges.*

W poniższym przykładzie dozwolony jest tylko wychodzący publiczny adres IP modułu równoważenia obciążenia standardowej jednostki SKU i można uzyskać dostęp do serwera interfejsu API tylko z węzłów w klastrze.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --vm-set-type VirtualMachineScaleSets \
    --load-balancer-sku standard \
    --api-server-authorized-ip-ranges 0.0.0.0/32 \
    --generate-ssh-keys
```

## <a name="update-a-clusters-api-server-authorized-ip-ranges"></a>Aktualizowanie autoryzowanych zakresów adresów IP serwera interfejsu API klastra

Aby zaktualizować nieautoryzowane zakresy adresów IP serwera interfejsu API w istniejącym klastrze, użyj polecenia [az aks update][az-aks-update] i użyj *parametrów --api-server-authorized-ip-ranges*, *--load-balancer-outbound-ip-prefixes*, *--load-balancer-outbound-ips*, lub *--load-balancer-outbound-ip-prefixes.*

Poniższy przykład aktualizuje nieautoryzowane zakresy adresów IP serwera interfejsu API w klastrze o nazwie *myAKSCluster* w grupie zasobów o nazwie *myResourceGroup*. Zakres adresów IP do autoryzacji to *73.140.245.0/24:*

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges  73.140.245.0/24
```

Można również użyć *0.0.0.0/32* podczas określania parametru *--api-server-authorized-ip-ranges,* aby zezwolić tylko na publiczny adres IP modułu równoważenia obciążenia standardowej jednostki SKU.

## <a name="disable-authorized-ip-ranges"></a>Wyłączanie autoryzowanych zakresów adresów IP

Aby wyłączyć autoryzowane zakresy adresów IP, użyj [aktualizacji az aks][az-aks-update] i określ pusty zakres, aby wyłączyć nieautoryzowane zakresy adresów IP serwera INTERFEJSU API. Przykład:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges ""
```

## <a name="next-steps"></a>Następne kroki

W tym artykule włączono nieautoryzowane zakresy adresów IP serwera interfejsu API. Takie podejście jest jedną z części sposobu uruchamiania bezpiecznego klastra AKS.

Aby uzyskać więcej informacji, zobacz [Pojęcia dotyczące zabezpieczeń dla aplikacji i klastrów w uzywki AKS][concepts-security] oraz Najważniejsze [wskazówki dotyczące zabezpieczeń klastra i uaktualnień w u. AKS][operator-best-practices-cluster-security].

<!-- LINKS - external -->
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[dev-spaces-ranges]: https://github.com/Azure/dev-spaces/tree/master/public-ips
[kubenet]: https://kubernetes.io/docs/concepts/extend-kubernetes/compute-storage-net/network-plugins/#kubenet

<!-- LINKS - internal -->
[az-aks-update]: /cli/azure/ext/aks-preview/aks#ext-aks-preview-az-aks-update
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-network-public-ip-list]: /cli/azure/network/public-ip#az-network-public-ip-list
[concepts-clusters-workloads]: concepts-clusters-workloads.md
[concepts-security]: concepts-security.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-cluster-security]: operator-best-practices-cluster-security.md
[route-tables]: ../virtual-network/manage-route-table.md
[standard-sku-lb]: load-balancer-standard.md
