---
title: Zakresy adresów IP autoryzowanych serwerów interfejsu API w usłudze Azure Kubernetes Service (AKS)
description: Dowiedz się, jak zabezpieczyć klaster przy użyciu zakresu adresów IP na potrzeby dostępu do serwera interfejsu API w usłudze Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 09/21/2020
ms.openlocfilehash: 99c6b173d96bbd54f12a0edc501d49e8c65caf01
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91613734"
---
# <a name="secure-access-to-the-api-server-using-authorized-ip-address-ranges-in-azure-kubernetes-service-aks"></a>Bezpieczny dostęp do serwera interfejsu API za pomocą zakresów autoryzowanych adresów IP w usłudze Azure Kubernetes Service (AKS)

W programie Kubernetes serwer interfejsu API odbiera żądania wykonania akcji w klastrze, takie jak tworzenie zasobów lub skalowanie liczby węzłów. Serwer interfejsu API jest centralną metodą współdziałania z klastrem i zarządzania nim. Aby zwiększyć bezpieczeństwo klastra i zminimalizować ataki, serwer interfejsu API powinien być dostępny tylko z ograniczonym zestawem zakresów adresów IP.

W tym artykule pokazano, jak używać zakresów adresów IP autoryzowanych przez serwer interfejsu API do ograniczania, które adresy IP i CIDR mogą uzyskać dostęp do płaszczyzny kontroli.

## <a name="before-you-begin"></a>Zanim rozpoczniesz

W tym artykule opisano sposób tworzenia klastra AKS przy użyciu interfejsu wiersza polecenia platformy Azure.

Wymagany jest interfejs wiersza polecenia platformy Azure w wersji 2.0.76 lub nowszej. Uruchom polecenie  `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie przeprowadzenie instalacji lub uaktualnienia, zobacz  [Instalowanie interfejsu wiersza polecenia platformy Azure][install-azure-cli].

### <a name="limitations"></a>Ograniczenia

Funkcja zakresów autoryzowanych adresów IP serwera interfejsu API ma następujące ograniczenia:
- W przypadku klastrów utworzonych po przeniesieniu zakresów adresów IP autoryzowanych przez serwer API z wersji zapoznawczej w październiku 2019 zakresy adresów IP autoryzowanych przez serwer API są obsługiwane tylko w przypadku *standardowego* modułu równoważenia obciążenia jednostki SKU. Istniejące klastry z modułem równoważenia obciążenia *podstawowej* jednostki SKU oraz skonfigurowanymi dozwolonymi zakresami adresów IP serwera interfejsu API będą nadal działać w takiej postaci, w jakiej nie można migrować do modułu równoważenia obciążenia w *warstwie Standardowa* . Te istniejące klastry będą również nadal działały, jeśli ich wersja Kubernetes lub płaszczyzna kontroli zostaną uaktualnione. Zakresy adresów IP autoryzowanych serwerów interfejsu API nie są obsługiwane w przypadku klastrów prywatnych.
- Ta funkcja jest niezgodna z klastrami korzystającymi z [funkcji publicznej wersji zapoznawczej pul węzłów węzła](use-multiple-node-pools.md#assign-a-public-ip-per-node-for-your-node-pools-preview).

## <a name="overview-of-api-server-authorized-ip-ranges"></a>Przegląd zakresów adresów IP autoryzowanych serwerów interfejsu API

Serwer interfejsu API Kubernetes to sposób ujawniania podstawowych interfejsów API Kubernetes. Ten składnik zapewnia interakcję z narzędziami do zarządzania, takimi jak `kubectl` lub pulpitem nawigacyjnym Kubernetes. AKS zapewnia jednodostępną płaszczyznę kontroli klastra z dedykowanym serwerem interfejsu API. Domyślnie serwer interfejsu API ma przypisany publiczny adres IP i należy kontrolować dostęp przy użyciu kontroli dostępu opartej na rolach (RBAC).

Aby zabezpieczyć dostęp do dostępnego publicznie AKS kontroli/serwera interfejsu API, można włączyć i używać autoryzowanych zakresów adresów IP. Te autoryzowane zakresy adresów IP zezwalają na komunikowanie się z serwerem interfejsu API tylko zdefiniowanym zakresom IP. Żądanie wysłane do serwera interfejsu API z adresu IP, który nie należy do tych autoryzowanych zakresów adresów IP, jest blokowane. Kontynuuj używanie RBAC, aby autoryzować użytkowników i akcje, które żądają.

Aby uzyskać więcej informacji na temat serwera interfejsu API i innych składników klastra, zobacz [Kubernetes podstawowe pojęcia dotyczące AKS][concepts-clusters-workloads].

## <a name="create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled"></a>Tworzenie klastra AKS z włączonymi zakresami adresów IP autoryzowanych przez serwer interfejsu API

Utwórz klaster przy użyciu polecenia [AZ AKS Create][az-aks-create] i określ *`--api-server-authorized-ip-ranges`* parametr, aby podać listę autoryzowanych zakresów adresów IP. Te zakresy adresów IP są zwykle zakresami adresów używanymi przez sieci lokalne lub publiczne adresy IP. Po określeniu zakresu CIDR, należy zacząć od pierwszego adresu IP z zakresu. Na przykład *137.117.106.90/29* jest prawidłowym zakresem, ale należy określić pierwszy adres IP z zakresu, na przykład *137.117.106.88/29*.

> [!IMPORTANT]
> Domyślnie klaster używa usługi [równoważenia obciążenia standardowej jednostki SKU][standard-sku-lb] , której można użyć do skonfigurowania bramy wychodzącej. W przypadku włączenia zakresów adresów IP autoryzowanych przez serwer interfejsu API podczas tworzenia klastra publiczny adres IP dla klastra jest również Domyślnie dozwolony jako dodatek do określonych zakresów. Jeśli określisz *""* lub nie wartość dla *`--api-server-authorized-ip-ranges`* , dozwolone zakresy adresów IP serwera interfejsu API zostaną wyłączone. Należy pamiętać, że jeśli używasz programu PowerShell, użyj polecenia *`--api-server-authorized-ip-ranges=""`* (z znakiem równości), aby uniknąć problemów z analizowaniem.

W poniższym przykładzie jest tworzony klaster z jednym węzłem o nazwie *myAKSCluster* w grupie zasobów o nazwie Moje *zasoby* z włączonymi zakresami adresów IP autoryzowanego serwera interfejsu API. Dozwolone zakresy adresów IP to *73.140.245.0/24*:

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
> - Każdy zakres reprezentujący sieci, z których będziesz administrować klastrem
> - Jeśli używasz Azure Dev Spaces w klastrze AKS, musisz zezwolić na [dodatkowe zakresy w zależności od regionu][dev-spaces-ranges].
>
> Górny limit liczby zakresów adresów IP, które można określić, to 200.
>
> Odpropagowanie reguł może potrwać do 2min. Zaczekaj na ten czas podczas testowania połączenia.

### <a name="specify-the-outbound-ips-for-the-standard-sku-load-balancer"></a>Określ wychodzące adresy IP dla usługi równoważenia obciążenia standardowej jednostki SKU

W przypadku tworzenia klastra AKS, jeśli określono wychodzące adresy IP lub prefiksy dla klastra, dozwolone są również te adresy lub prefiksy. Na przykład:

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

W powyższym przykładzie wszystkie adresy IP podane w parametrze *`--load-balancer-outbound-ip-prefixes`* są dozwolone wraz z adresami IP w *`--api-server-authorized-ip-ranges`* parametrze.

Zamiast tego można określić parametr, *`--load-balancer-outbound-ip-prefixes`* Aby zezwolić na wychodzące prefiksy IP modułu równoważenia obciążenia.

### <a name="allow-only-the-outbound-public-ip-of-the-standard-sku-load-balancer"></a>Zezwalaj tylko na wychodzący publiczny adres IP usługi równoważenia obciążenia standardowej jednostki SKU

Po włączeniu zakresów adresów IP autoryzowanych przez serwer interfejsu API podczas tworzenia klastra, publiczny adres IP dla usługi równoważenia obciążenia w warstwie Standardowa jest również Domyślnie dozwolony jako dodatek do określonych zakresów. Aby zezwolić na wychodzący publiczny adres IP standardowego modułu równoważenia obciążenia jednostki SKU, użyj wartości *0.0.0.0/32* podczas określania *`--api-server-authorized-ip-ranges`* parametru.

W poniższym przykładzie dozwolony jest tylko wychodzący publiczny adres IP usługi równoważenia obciążenia standardowej jednostki SKU i dostęp do serwera interfejsu API można uzyskać tylko z węzłów w klastrze.

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

## <a name="update-a-clusters-api-server-authorized-ip-ranges"></a>Aktualizowanie zakresów adresów IP autoryzowanych serwerów interfejsu API klastra

Aby zaktualizować zakres adresów IP autoryzowanych przez serwer interfejsu API w istniejącym klastrze, użyj polecenia [AZ AKS Update][az-aks-update] i wykorzystaj *`--api-server-authorized-ip-ranges`* Parametry,--równoważenia obciążenia-wychodzące-IP-Red-* *`--load-balancer-outbound-ips`* -Load-wychodzącego-IP* .

Poniższy przykład aktualizuje zakresy dozwolonych adresów IP serwera interfejsu API w klastrze o nazwie *myAKSCluster* w grupie zasobów o nazwie Moja *resourceName*. Zakres adresów IP do autoryzacji to *73.140.245.0/24*:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges  73.140.245.0/24
```

Można również użyć *0.0.0.0/32* podczas określania parametru, *`--api-server-authorized-ip-ranges`* aby zezwalać tylko na publiczny adres IP standardowego modułu równoważenia obciążenia jednostki SKU.

## <a name="disable-authorized-ip-ranges"></a>Wyłącz autoryzowane zakresy adresów IP

Aby wyłączyć autoryzowane zakresy adresów IP, użyj [AZ AKS Update][az-aks-update] i określ pusty zakres, aby wyłączyć autoryzowane zakresy adresów IP serwera interfejsu API. Na przykład:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges ""
```

## <a name="how-to-find-my-ip-to-include-in---api-server-authorized-ip-ranges"></a>Jak znaleźć mój adres IP do uwzględnienia `--api-server-authorized-ip-ranges` ?

Należy dodać maszyny deweloperskie, narzędzia lub adresy IP usługi Automation do listy klastrów AKS zatwierdzonych zakresów adresów IP w celu uzyskania dostępu do serwera interfejsu API. 

Innym rozwiązaniem jest skonfigurowanie serwera przesiadkowego z wymaganymi narzędziami w ramach oddzielnej podsieci w sieci wirtualnej zapory. Przyjęto założenie, że środowisko ma zaporę z odpowiednią siecią i dodano adresy IP zapory do autoryzowanych zakresów. Podobnie, jeśli wymuszono tunelowanie z podsieci AKS do podsieci zapory, niż w przypadku braku serwera przesiadkowego w podsieci klastra.

Dodaj inny adres IP do zatwierdzonych zakresów przy użyciu poniższego polecenia.

```bash
# Retrieve your IP address
CURRENT_IP=$(dig @resolver1.opendns.com ANY myip.opendns.com +short)
# Add to AKS approved list
az aks update -g $RG -n $AKSNAME --api-server-authorized-ip-ranges $CURRENT_IP/32
```

>> [!NOTE]
> Powyższy przykład dołącza zakresy adresów IP autoryzowanych serwerów interfejsu API w klastrze. Aby wyłączyć autoryzowane zakresy adresów IP, użyj AZ AKS Update i określ pusty zakres "". 

Innym rozwiązaniem jest użycie poniższego polecenia w systemach Windows w celu uzyskania publicznego adresu IPv4 lub wykonanie kroków opisanych w sekcji [Znajdowanie adresu IP](https://support.microsoft.com/en-gb/help/4026518/windows-10-find-your-ip-address).

```azurepowershell-interactive
Invoke-RestMethod http://ipinfo.io/json | Select -exp ip
```

Możesz również znaleźć ten adres, wyszukując pozycję "co to jest mój adres IP" w przeglądarce internetowej.

## <a name="next-steps"></a>Następne kroki

W tym artykule włączono zakres adresów IP autoryzowanych przez serwer interfejsu API. To podejście jest jedną z metod uruchomienia bezpiecznego klastra AKS.

Aby uzyskać więcej informacji, zobacz [pojęcia dotyczące zabezpieczeń aplikacji i klastrów w AKS][concepts-security] i [najlepszych rozwiązaniach dotyczących zabezpieczeń klastrów i uaktualnień w AKS][operator-best-practices-cluster-security].

<!-- LINKS - external -->
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[dev-spaces-ranges]: ../dev-spaces/configure-networking.md#aks-cluster-network-requirements
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
