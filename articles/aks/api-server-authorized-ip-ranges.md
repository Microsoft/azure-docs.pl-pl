---
title: Zakresy autoryzowanych adresów IP serwera interfejsu API Azure Kubernetes Service (AKS)
description: Dowiedz się, jak zabezpieczyć klaster przy użyciu zakresu adresów IP w celu uzyskania dostępu do serwera interfejsu API w Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 09/21/2020
ms.openlocfilehash: 8fca3fe61e26a031e6ea09692c9ba0781bfca21f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769647"
---
# <a name="secure-access-to-the-api-server-using-authorized-ip-address-ranges-in-azure-kubernetes-service-aks"></a>Bezpieczny dostęp do serwera interfejsu API przy użyciu autoryzowanych zakresów adresów IP w Azure Kubernetes Service (AKS)

Na serwerze Kubernetes serwer interfejsu API odbiera żądania wykonania akcji w klastrze, takich jak utworzenie zasobów lub skalowanie liczby węzłów. Serwer interfejsu API to centralny sposób interakcji z klastrem i zarządzania nim. Aby zwiększyć bezpieczeństwo klastra i zminimalizować ataki, serwer interfejsu API powinien być dostępny tylko z ograniczonego zestawu zakresów adresów IP.

W tym artykule pokazano, jak używać zakresów autoryzowanych adresów IP serwera interfejsu API w celu ograniczenia adresów IP i raportów CIDR, które mogą uzyskać dostęp do płaszczyzny kontroli.

## <a name="before-you-begin"></a>Zanim rozpoczniesz

W tym artykule pokazano, jak utworzyć klaster usługi AKS przy użyciu interfejsu wiersza polecenia platformy Azure.

Musisz mieć zainstalowany i skonfigurowany interfejs wiersza polecenia platformy Azure w wersji 2.0.76 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][install-azure-cli].

### <a name="limitations"></a>Ograniczenia

Funkcja autoryzowanych zakresów adresów IP serwera interfejsu API ma następujące ograniczenia:
- W klastrach utworzonych po tym, jak autoryzowane zakresy adresów IP serwera interfejsu API zostały przeniesione z  wersji zapoznawczej w październiku 2019 r., autoryzowane zakresy adresów IP serwera interfejsu API są obsługiwane tylko przez usługę równoważenia obciążenia w standardowych sku. Istniejące klastry  ze skonfigurowanymi zakresami autoryzowanych adresów IP serwera interfejsu API i usługi load balancer podstawowej wersji SKU będą nadal działać bez względu na to, jak są, ale nie można ich migrować do usługi równoważenia obciążenia standardowej *wersji* SKU. Te istniejące klastry będą nadal działać po uaktualnieniu ich wersji lub płaszczyzny sterowania kubernetes. Zakresy autoryzowanych adresów IP serwera interfejsu API nie są obsługiwane w przypadku klastrów prywatnych.
- Ta funkcja nie jest zgodna z klastrami, które używają [publicznego adresu IP na węzeł](use-multiple-node-pools.md#assign-a-public-ip-per-node-for-your-node-pools).

## <a name="overview-of-api-server-authorized-ip-ranges"></a>Omówienie zakresów autoryzowanych adresów IP serwera interfejsu API

Serwer interfejsu API platformy Kubernetes to sposób, w jaki są widoczne bazowe interfejsy API platformy Kubernetes. Ten składnik umożliwia interakcję z narzędziami do zarządzania, takimi jak `kubectl` pulpit nawigacyjny kubernetes. Usługa AKS udostępnia płaszczyznę sterowania klastra z jedną dzierżawą z dedykowanym serwerem interfejsu API. Domyślnie serwer interfejsu API ma przypisany publiczny adres IP i należy kontrolować dostęp przy użyciu kontroli dostępu opartej na rolach (RBAC) kubernetes lub kontroli dostępu opartej na rolach platformy Azure.

Aby zabezpieczyć dostęp do publicznie dostępnej płaszczyzny sterowania/serwera interfejsu API usługi AKS, można włączyć i użyć autoryzowanych zakresów adresów IP. Te autoryzowane zakresy adresów IP zezwalają tylko zdefiniowanym zakresom adresów IP na komunikację z serwerem interfejsu API. Żądanie skierowane do serwera interfejsu API z adresu IP, który nie jest częścią tych autoryzowanych zakresów adresów IP, jest blokowane. Kontynuuj używanie kontroli RBAC platformy Kubernetes lub kontroli RBAC platformy Azure do autoryzowania użytkowników i akcji, których żądają.

Aby uzyskać więcej informacji na temat serwera interfejsu API i innych składników klastra, zobacz [Kubernetes core concepts for AKS (Podstawowe pojęcia dotyczące usługi Kubernetes dla usługi AKS).][concepts-clusters-workloads]

## <a name="create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled"></a>Tworzenie klastra usługi AKS z włączonymi autoryzowanymi zakresami adresów IP serwera interfejsu API

Utwórz klaster przy użyciu [narzędzia az aks create][az-aks-create] i określ parametr , aby podać listę *`--api-server-authorized-ip-ranges`* autoryzowanych zakresów adresów IP. Te zakresy adresów IP są zazwyczaj zakresami adresów używanymi przez sieci lokalne lub publiczne adresy IP. Po określeniu zakresu CIDR zacznij od pierwszego adresu IP z zakresu. Na przykład *137.117.106.90/29* jest prawidłowym zakresem, ale upewnij się, że określono pierwszy adres IP z zakresu, taki jak *137.117.106.88/29.*

> [!IMPORTANT]
> Domyślnie klaster używa standardowego równoważenia obciążenia [SKU,][standard-sku-lb] którego można użyć do skonfigurowania bramy ruchu wychodzącego. Po włączeniu autoryzowanych zakresów adresów IP serwera interfejsu API podczas tworzenia klastra publiczny adres IP klastra jest domyślnie dozwolony oprócz zakresów, które określisz. Jeśli określisz *wartość ""* lub nie określisz żadnej wartości dla , autoryzowane zakresy adresów IP serwera *`--api-server-authorized-ip-ranges`* interfejsu API zostaną wyłączone. Pamiętaj, że jeśli używasz programu PowerShell, użyj polecenia (ze znakiem równości), aby uniknąć problemów *`--api-server-authorized-ip-ranges=""`* z analizą.

Poniższy przykład tworzy klaster z jednym węzłem o nazwie *myAKSCluster* w grupie zasobów o nazwie *myResourceGroup* z włączonymi autoryzowanymi zakresami adresów IP serwera interfejsu API. Dozwolone zakresy adresów IP to *73.140.245.0/24:*

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
> Należy dodać te zakresy do listy zezwalań:
> - Publiczny adres IP zapory
> - Dowolny zakres reprezentujący sieci, z których będziesz administrować klastrem
> - Jeśli używasz usługi Azure Dev Spaces w klastrze usługi AKS, musisz zezwolić na dodatkowe [zakresy w zależności od regionu.][dev-spaces-ranges]
>
> Górny limit liczby zakresów adresów IP, które można określić, to 200.
>
> Propagacja reguł może potrwać do 2 minut. Do tego czasu należy od czasu do czasu przetestować połączenie.

### <a name="specify-the-outbound-ips-for-the-standard-sku-load-balancer"></a>Określanie wychodzących ip dla standardowego równoważenia obciążenia SKU

Jeśli podczas tworzenia klastra usługi AKS określisz adresy IP lub prefiksy ruchu wychodzącego dla klastra, te adresy lub prefiksy również będą dozwolone. Na przykład:

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

W powyższym przykładzie wszystkie ip podane w parametrze są dozwolone wraz *`--load-balancer-outbound-ip-prefixes`* z ip w *`--api-server-authorized-ip-ranges`* parametrze.

Zamiast tego można określić parametr , aby zezwolić na prefiksy *`--load-balancer-outbound-ip-prefixes`* IP wychodzącego równoważenia obciążenia.

### <a name="allow-only-the-outbound-public-ip-of-the-standard-sku-load-balancer"></a>Zezwalanie tylko na publiczny adres IP ruchu wychodzącego usługi load balancer w standardowych sku

Po włączeniu autoryzowanych zakresów adresów IP serwera interfejsu API podczas tworzenia klastra publiczny adres IP ruchu wychodzącego dla usługi równoważenia obciążenia standardowej wersji SKU dla klastra jest również domyślnie dozwolony oprócz zakresów, które określisz. Aby zezwolić tylko na wychodzący publiczny adres IP usługi równoważenia obciążenia standardowej wersji SKU, podczas określania parametru użyj adresu *0.0.0.0/32.* *`--api-server-authorized-ip-ranges`*

W poniższym przykładzie dozwolony jest tylko publiczny adres IP ruchu wychodzącego standardowego usługi równoważenia obciążenia SKU, a dostęp do serwera interfejsu API można uzyskać tylko z węzłów w klastrze.

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

Aby zaktualizować autoryzowane zakresy adresów IP serwera interfejsu API w istniejącym klastrze, użyj polecenia [az aks update][az-aks-update] i użyj parametrów *`--api-server-authorized-ip-ranges`* ,--load-balancer-outbound-ip-prefixes *, lub *`--load-balancer-outbound-ips`* --load-balancer-outbound-ip-prefixes.*

Poniższy przykład aktualizuje autoryzowane zakresy adresów IP serwera interfejsu API w klastrze o nazwie *myAKSCluster* w grupie zasobów *o nazwie myResourceGroup.* Zakres adresów IP do autoryzowania to *73.140.245.0/24:*

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges  73.140.245.0/24
```

Można również użyć *0.0.0.0/32* podczas określania parametru, aby zezwolić tylko na publiczny adres IP standardowego *`--api-server-authorized-ip-ranges`* usługi równoważenia obciążenia SKU.

## <a name="disable-authorized-ip-ranges"></a>Wyłączanie autoryzowanych zakresów adresów IP

Aby wyłączyć autoryzowane zakresy adresów IP, użyj [opcji az aks update][az-aks-update] i określ pusty zakres, aby wyłączyć autoryzowane zakresy adresów IP serwera interfejsu API. Na przykład:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges ""
```

## <a name="find-existing-authorized-ip-ranges"></a>Znajdowanie istniejących autoryzowanych zakresów adresów IP

Aby znaleźć autoryzowane zakresy adresów IP, użyj [az aks show][az-aks-show] i określ nazwę klastra oraz grupę zasobów. Na przykład:

```azurecli-interactive
az aks show \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --query apiServerAccessProfile.authorizedIpRanges'
```

## <a name="update-disable-and-find-authorized-ip-ranges-using-azure-portal"></a>Aktualizowanie, wyłączanie i znajdowanie autoryzowanych zakresów adresów IP przy użyciu Azure Portal

Powyższe operacje dodawania, aktualizowania, znajdowania i wyłączania autoryzowanych zakresów adresów IP można również wykonywać w Azure Portal. Aby uzyskać dostęp, przejdź do pozycji **Sieć** **w** obszarze Ustawienia w bloku menu zasobu klastra.

:::image type="content" source="media/api-server-authorized-ip-ranges/ip-ranges-specified.PNG" alt-text="W przeglądarce zostanie pokazana strona ustawień sieciowych zasobu Azure Portal klastra. Opcje &quot;ustaw określony zakres adresów IP&quot; i &quot;Określone zakresy adresów IP&quot; są wyróżnione.":::

## <a name="how-to-find-my-ip-to-include-in---api-server-authorized-ip-ranges"></a>Jak znaleźć mój adres IP do dołączyć do `--api-server-authorized-ip-ranges` ?

Aby uzyskać dostęp do serwera interfejsu API z tego serwera, należy dodać maszyny dewelopera, narzędzia lub adresy IP automatyzacji do listy zatwierdzonych zakresów adresów IP klastra usługi AKS. 

Inną opcją jest skonfigurowanie serwera przeskoku z wymaganymi narzędziami w oddzielnej podsieci w sieci wirtualnej zapory. Przyjęto założenie, że środowisko ma zaporę z odpowiednią siecią i że dodano do nich autoryzowane zakresy adresów IP zapory. Analogicznie, jeśli wymuszono tunelowanie z podsieci usługi AKS do podsieci Zapory, to nie ma również serwera przeskoku w podsieci klastra.

Dodaj kolejny adres IP do zatwierdzonych zakresów za pomocą następującego polecenia.

```bash
# Retrieve your IP address
CURRENT_IP=$(dig @resolver1.opendns.com ANY myip.opendns.com +short)
# Add to AKS approved list
az aks update -g $RG -n $AKSNAME --api-server-authorized-ip-ranges $CURRENT_IP/32
```

>> [!NOTE]
> Powyższy przykład dołącza autoryzowane zakresy adresów IP serwera interfejsu API w klastrze. Aby wyłączyć autoryzowane zakresy adresów IP, użyj az aks update i określ pusty zakres "". 

Inną opcją jest użycie poniższego polecenia w systemach Windows w celu uzyskania publicznego adresu IPv4. Możesz też wykonać kroki opisane w artykule [Znajdowanie adresu IP.](https://support.microsoft.com/en-gb/help/4026518/windows-10-find-your-ip-address)

```azurepowershell-interactive
Invoke-RestMethod http://ipinfo.io/json | Select -exp ip
```

Możesz również znaleźć ten adres, wyszukując "jaki jest mój adres IP" w przeglądarce internetowej.

## <a name="next-steps"></a>Następne kroki

W tym artykule włączono autoryzowane zakresy adresów IP serwera interfejsu API. To podejście jest jedną z części sposobu uruchamiania bezpiecznego klastra usługi AKS.

Aby uzyskać więcej informacji, zobacz [Security concepts for applications and clusters in AKS][concepts-security] (Pojęcia związane z zabezpieczeniami aplikacji i klastrów w UKS) i Best [practices for cluster security and upgrades in AKS][operator-best-practices-cluster-security](Najlepsze rozwiązania dotyczące zabezpieczeń i uaktualnień klastra w UKS).

<!-- LINKS - external -->
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[dev-spaces-ranges]: ../dev-spaces/configure-networking.md#aks-cluster-network-requirements
[kubenet]: https://kubernetes.io/docs/concepts/extend-kubernetes/compute-storage-net/network-plugins/#kubenet

<!-- LINKS - internal -->
[az-aks-update]: /cli/azure/ext/aks-preview/aks#ext-aks-preview-az-aks-update
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-show]: /cli/azure/aks#az_aks_show
[az-network-public-ip-list]: /cli/azure/network/public-ip#az_network_public_ip_list
[concepts-clusters-workloads]: concepts-clusters-workloads.md
[concepts-security]: concepts-security.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-cluster-security]: operator-best-practices-cluster-security.md
[route-tables]: ../virtual-network/manage-route-table.md
[standard-sku-lb]: load-balancer-standard.md
