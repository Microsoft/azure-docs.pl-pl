---
title: Konfigurowanie sieci Azure CNI w usłudze Azure Kubernetes Service (AKS)
description: Dowiedz się, jak skonfigurować usługę Azure CNI (Advanced) Networking w usłudze Azure Kubernetes Service (AKS), w tym wdrażanie klastra AKS do istniejącej sieci wirtualnej i podsieci.
services: container-service
ms.topic: article
ms.date: 06/03/2019
ms.openlocfilehash: afb98acf903f90ead137c9b372d33ce82b89f7b5
ms.sourcegitcommit: 1a98b3f91663484920a747d75500f6d70a6cb2ba
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2021
ms.locfileid: "99062221"
---
# <a name="configure-azure-cni-networking-in-azure-kubernetes-service-aks"></a>Konfigurowanie sieci Azure CNI w usłudze Azure Kubernetes Service (AKS)

Domyślnie klastry AKS korzystają z [korzystającą wtyczki kubenet][kubenet], a sieć wirtualna i podsieć są tworzone dla Ciebie. W przypadku *korzystającą wtyczki kubenet* węzły uzyskują adres IP z podsieci sieci wirtualnej. Translacja adresów sieciowych (NAT) jest następnie konfigurowana w węzłach, a w jednostkach. Takie podejście zmniejsza liczbę adresów IP, które należy zarezerwować w przestrzeni sieciowej, aby można było korzystać z nich.

[Usługa Azure Container Network Interface (CNI)][cni-networking]w każdym przypadku Pobiera adres IP z podsieci i jest dostępny bezpośrednio. Te adresy IP muszą być unikatowe w przestrzeni sieci i muszą być planowane z wyprzedzeniem. Każdy węzeł ma parametr konfiguracji dla maksymalnej liczby obsługiwanych przez nią zasobników. Równoważna liczba adresów IP na węzeł jest następnie rezerwowana na początku dla tego węzła. Takie podejście wymaga większego planowania i często prowadzi do wyczerpania adresów IP lub trzeba ponownie skompilować klastry w większej podsieci, w miarę wzrostu wymagań aplikacji.

W tym artykule pokazano, jak za pomocą *usługi Azure CNI* Networking utworzyć i używać podsieci sieci wirtualnej dla klastra AKS. Aby uzyskać więcej informacji o opcjach sieciowych i zagadnieniach, zobacz [pojęcia sieci dotyczące Kubernetes i AKS][aks-network-concepts].

## <a name="prerequisites"></a>Wymagania wstępne

* Sieć wirtualna klastra AKS musi zezwalać na wychodzącą łączność z Internetem.
* Klastry AKS nie mogą korzystać z zakresów adresów usługi Kubernetes,,, ani ich zakresu, z zakresu adresów `169.254.0.0/16` `172.30.0.0/16` `172.31.0.0/16` `192.0.2.0/24` lub zakresu adresów sieci wirtualnej klastra. 
* Nazwa główna usługi używana przez klaster AKS musi mieć co najmniej uprawnienia [współautora sieci](../role-based-access-control/built-in-roles.md#network-contributor) w podsieci w sieci wirtualnej. Jeśli chcesz zdefiniować [rolę niestandardową](../role-based-access-control/custom-roles.md) , zamiast korzystać z wbudowanej roli współautor sieci, wymagane są następujące uprawnienia:
  * `Microsoft.Network/virtualNetworks/subnets/join/action`
  * `Microsoft.Network/virtualNetworks/subnets/read`
* Zamiast nazwy głównej usługi można użyć przypisanej tożsamości zarządzanej przez system w celu uzyskania uprawnień. Aby uzyskać więcej informacji, zobacz [Korzystanie z tożsamości zarządzanych](use-managed-identity.md).
* Podsieć przypisana do puli węzłów AKS nie może być [delegowaną podsiecią](../virtual-network/subnet-delegation-overview.md).

## <a name="plan-ip-addressing-for-your-cluster"></a>Planowanie adresowania IP dla klastra

Klastry skonfigurowane przy użyciu sieci Azure CNI wymagają dodatkowego planowania. Rozmiar sieci wirtualnej i jej podsieci musi uwzględniać liczbę planów, które należy uruchomić, oraz liczbę węzłów klastra.

Adresy IP dla i węzły klastra są przypisywane z określonej podsieci w sieci wirtualnej. Każdy węzeł jest skonfigurowany przy użyciu podstawowego adresu IP. Domyślnie 30 dodatkowych adresów IP jest wstępnie skonfigurowanych przez usługę Azure CNI, które są przypisane do każdego z węzłów w węźle. W przypadku skalowania w poziomie klastra każdy węzeł jest podobnie skonfigurowany przy użyciu adresów IP z podsieci. Możesz również wyświetlić [maksymalną liczbę zasobników na węzeł](#maximum-pods-per-node).

> [!IMPORTANT]
> Wymagana liczba adresów IP powinna obejmować zagadnienia dotyczące operacji uaktualniania i skalowania. Jeśli zakres adresów IP zostanie ustawiony tak, aby obsługiwał stałą liczbę węzłów, nie można uaktualnić ani skalować klastra.
>
> - Po **uaktualnieniu** klastra AKS do klastra jest wdrażany nowy węzeł. Usługi i obciążenia zaczynają działać w nowym węźle, a z klastra jest usuwany starszy węzeł. Ten proces uaktualniania stopniowego wymaga, aby był dostępny co najmniej jeden dodatkowy blok adresów IP. Liczba węzłów jest następnie `n + 1` .
>   - Jest to szczególnie ważne w przypadku używania pul węzłów systemu Windows Server. W węzłach systemu Windows Server w programie AKS nie są automatycznie stosowane aktualizacje systemu Windows, zamiast tego należy przeprowadzić uaktualnienie w puli węzłów. To uaktualnienie wdraża nowe węzły z najnowszym obrazem podstawowego węzła systemu Windows Server 2019 i poprawkami zabezpieczeń. Aby uzyskać więcej informacji na temat uaktualniania puli węzłów systemu Windows Server, zobacz [uaktualnianie puli węzłów w AKS][nodepool-upgrade].
>
> - Podczas **skalowania** klastra AKS nowy węzeł jest wdrażany w klastrze. Usługi i obciążenia zaczynają działać w nowym węźle. Zakres adresów IP należy wziąć pod uwagę, w jaki sposób można skalować liczbę węzłów i określić, jak może być obsługiwany klaster. Należy również uwzględnić jeden dodatkowy węzeł operacji uaktualniania. Liczba węzłów jest następnie `n + number-of-additional-scaled-nodes-you-anticipate + 1` .

Jeśli oczekujesz, że węzły mają uruchamiać maksymalną liczbę zasobników i regularnie zniszczą i wdrażają stawki, należy również wziąć pod uwagę pewne dodatkowe adresy IP na węzeł. Te dodatkowe adresy IP należy wziąć pod uwagę, ponieważ usunięcie usługi może potrwać kilka sekund, a adres IP dla nowej usługi zostanie wdrożony i uzyskać adres.

Plan adresów IP dla klastra AKS składa się z sieci wirtualnej, co najmniej jednej podsieci dla węzłów i zasobników oraz zakresu adresów usługi Kubernetes.

| Zakres adresów/zasób platformy Azure | Limity i rozmiary |
| --------- | ------------- |
| Sieć wirtualna | Sieć wirtualna platformy Azure może mieć wartość większą niż 8, ale jest ograniczona do 65 536 skonfigurowanych adresów IP. Przed skonfigurowaniem przestrzeni adresowej należy wziąć pod uwagę wszystkie wymagania dotyczące sieci, w tym komunikację z usługami w innych sieciach wirtualnych. Na przykład w przypadku konfigurowania zbyt dużej ilości przestrzeni adresowej mogą wystąpić problemy z nakładaniem się innych przestrzeni adresowych w sieci.|
| Podsieć | Musi być wystarczająco duży, aby pomieścić węzły, zasobniki i wszystkie zasoby Kubernetes i platformy Azure, które mogą być obsługiwane w klastrze. Jeśli na przykład zostanie wdrożony wewnętrzny Azure Load Balancer, jego adresy IP frontonu są przydzieleni z podsieci klastra, a nie do publicznych adresów IP. Rozmiar podsieci powinien również uwzględniać operacje uaktualniania konta lub przyszłe potrzeby skalowania.<p />Aby obliczyć *minimalny* rozmiar podsieci obejmujący dodatkowy węzeł operacji uaktualniania: `(number of nodes + 1) + ((number of nodes + 1) * maximum pods per node that you configure)`<p/>Przykład klastra węzłów 50: `(51) + (51  * 30 (default)) = 1,581` (/21 lub większych)<p/>Przykład klastra węzłów 50, który obejmuje również obsługę skalowania w górę do 10 dodatkowych węzłów: `(61) + (61 * 30 (default)) = 1,891` (/21 lub większych)<p>Jeśli podczas tworzenia klastra nie zostanie określona maksymalna liczba numerów zasobników na węzeł, Maksymalna liczba zasobników na węzeł jest ustawiona na wartość *30*. Minimalna liczba wymaganych adresów IP jest określana na podstawie tej wartości. W przypadku obliczenia minimalnych wymagań dotyczących adresów IP dla innej wartości maksymalnej należy zapoznać się z tematem [jak skonfigurować maksymalną liczbę zasobników na węzeł](#configure-maximum---new-clusters) , aby ustawić tę wartość podczas wdrażania klastra. |
| Zakres adresów usługi Kubernetes | Ten zakres nie powinien być używany przez żaden element sieci w lub podłączony do tej sieci wirtualnej. Adres usługi CIDR musi być mniejszy niż/12. Można ponownie użyć tego zakresu w różnych klastrach AKS. |
| Adres IP usługi DNS platformy Kubernetes | Adres IP w zakresie adresów usługi Kubernetes, który będzie używany przez odnajdywanie usługi klastrowania. Nie używaj pierwszego adresu IP z zakresu adresów, takiego jak. 1. Pierwszy adres w zakresie podsieci jest używany dla adresu *Kubernetes. default. svc. Cluster. Local* . |
| Adres mostka platformy Docker | Adres mostka platformy Docker to domyślny adres sieciowy mostka *docker0*, który jest obecny we wszystkich instalacjach platformy Docker. Chociaż mostek *docker0* nie jest używany przez klastry AKS ani same same z nich, należy ustawić ten adres, aby nadal obsługiwał scenariusze, takie jak *kompilacja Docker* w klastrze AKS. Jest wymagane wybranie CIDR dla adresu sieciowego mostka platformy Docker, ponieważ w przeciwnym razie platforma Docker automatycznie wybierze podsieć, która może powodować konflikt z innymi CIDR. Należy wybrać przestrzeń adresową, która nie koliduje z pozostałą częścią CIDR w sieci, w tym CIDR usług w klastrze i CIDR. Wartość domyślna 172.17.0.1/16. Można ponownie użyć tego zakresu w różnych klastrach AKS. |

## <a name="maximum-pods-per-node"></a>Maksymalna liczba zasobników na węzeł

Maksymalna liczba zasobników na węzeł w klastrze AKS to 250. *Domyślna* Maksymalna liczba zasobników na węzeł jest różna w zależności od *korzystającą wtyczki KUBENET* i sieci *CNI* i metody wdrażania klastra.

| Metoda wdrażania | Korzystającą wtyczki kubenet domyślne | Domyślna usługa Azure CNI | Konfigurowalne podczas wdrażania |
| -- | :--: | :--: | -- |
| Interfejs wiersza polecenia platformy Azure | 110 | 30 | Tak (do 250) |
| Szablon usługi Resource Manager | 110 | 30 | Tak (do 250) |
| Portal | 110 | 110 (skonfigurowany na karcie pule węzłów) | Nie |

### <a name="configure-maximum---new-clusters"></a>Konfigurowanie nowych klastrów

Można skonfigurować maksymalną liczbę zasobników na węzeł w czasie wdrażania klastra lub dodać nowe pule węzłów. W przypadku wdrażania przy użyciu interfejsu wiersza polecenia platformy Azure lub szablonu Menedżer zasobów można ustawić wartość maksymalna liczba zasobników na węzeł jako 250.

Jeśli nie określisz maxPods podczas tworzenia nowych pul węzłów, otrzymasz wartość domyślną 30 dla usługi Azure CNI.

Minimalna wartość dla maksymalnej liczby zasobników na węzeł jest wymuszana w celu zagwarantowania ilości miejsca dla systemu w przypadku kondycji klastra o krytycznym znaczeniu. Minimalną wartością, którą można ustawić dla maksymalnej liczby zasobników na węzeł, jest 10, jeśli i tylko wtedy, gdy konfiguracja każdej puli węzłów ma miejsce na co najmniej 30 zasobników. Na przykład ustawienie maksymalnej liczby zasobników na węzeł na wartość minimalną 10 wymaga, aby każda pula węzłów była mieć co najmniej 3 węzły. To wymaganie jest stosowane dla każdej nowej puli węzłów, tak więc jeśli 10 jest zdefiniowana jako maksymalna liczba zasobników na węzeł, każda kolejna dodana Pula węzłów musi mieć co najmniej 3 węzły.

| Sieć | Minimum | Maksimum |
| -- | :--: | :--: |
| Azure CNI | 10 | 250 |
| Korzystającą wtyczki kubenet | 10 | 110 |

> [!NOTE]
> Minimalna wartość w powyższej tabeli jest wymuszana przez usługę AKS. Nie można ustawić wartości maxPods mniejszej niż wartość minimalna wyświetlana jako taka może uniemożliwić uruchomienie klastra.

* **Interfejs wiersza polecenia platformy Azure**: Określ `--max-pods` argument podczas wdrażania klastra za pomocą polecenia [AZ AKS Create][az-aks-create] . Wartość maksymalna to 250.
* **Szablon Menedżer zasobów**: Określ `maxPods` Właściwość w obiekcie [ManagedClusterAgentPoolProfile] podczas wdrażania klastra z szablonem Menedżer zasobów. Wartość maksymalna to 250.
* **Azure Portal**: nie można zmienić maksymalnej liczby zasobników na węzeł podczas wdrażania klastra z Azure Portal. W przypadku wdrażania przy użyciu Azure Portal klastry sieci usługi Azure CNI są ograniczone do 30 zasobników na węzeł.

### <a name="configure-maximum---existing-clusters"></a>Skonfiguruj maksymalną liczbę istniejących klastrów

Ustawienie maxPod na węzeł można zdefiniować podczas tworzenia nowej puli węzłów. Jeśli musisz zwiększyć ustawienia maxPod na węzeł istniejącego klastra, Dodaj nową pulę węzłów z nową żądaną liczbą maxPod. Po przeprowadzeniu migracji do nowej puli, Usuń starszą pulę. Aby usunąć wszystkie starsze pule w klastrze, należy się upewnić, że są ustawiane tryby puli węzłów, zgodnie z definicją w [dokumencie pule węzłów systemowych][system-node-pools].

## <a name="deployment-parameters"></a>Parametry wdrożenia

Podczas tworzenia klastra AKS następujące parametry można skonfigurować dla sieci Azure CNI:

**Sieć wirtualna**: Sieć wirtualna, w której ma zostać wdrożony klaster Kubernetes. Jeśli chcesz utworzyć nową sieć wirtualną dla klastra, wybierz pozycję *Utwórz nową* i postępuj zgodnie z instrukcjami w sekcji *Tworzenie sieci wirtualnej* . Aby uzyskać informacje o limitach i przydziałach dla sieci wirtualnej platformy Azure, zobacz [limity subskrypcji i usług platformy Azure, limity przydziału i ograniczenia](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits).

**Podsieć**: podsieć w sieci wirtualnej, w której ma zostać wdrożony klaster. Jeśli chcesz utworzyć nową podsieć w sieci wirtualnej klastra, wybierz pozycję *Utwórz nową* i postępuj zgodnie z instrukcjami w sekcji *Tworzenie podsieci* . W przypadku łączności hybrydowej zakres adresów nie powinien pokrywać się z innymi sieciami wirtualnymi w danym środowisku.

**Wtyczka sieciowa platformy Azure**: w przypadku korzystania z wtyczki sieciowej platformy Azure nie można uzyskać dostępu do wewnętrznej usługi równoważenia obciążenia z użyciem "ExternalTrafficPolicy = local" z maszyn wirtualnych z adresem IP w clusterCIDR, który nie należy do klastra AKS.

**Zakres adresów usługi Kubernetes**: jest to zestaw wirtualnych adresów IP, które Kubernetes przypisuje do [usług][services] wewnętrznych w klastrze. Możesz użyć dowolnego prywatnego zakresu adresów, który spełnia następujące wymagania:

* Nie może znajdować się w zakresie adresów IP sieci wirtualnej klastra
* Nie może pokrywać się z innymi sieciami wirtualnymi, z którymi są równorzędne sieci wirtualne klastra
* Nie może nakładać się na lokalne adresy IP
* Nie może należeć do zakresu `169.254.0.0/16` , `172.30.0.0/16` , `172.31.0.0/16` lub `192.0.2.0/24`

Chociaż jest to technicznie możliwe określenie zakresu adresów usługi w ramach tej samej sieci wirtualnej co klaster, nie jest to zalecane. W przypadku używania nakładających się zakresów adresów IP może wystąpić nieprzewidywalne zachowanie. Aby uzyskać więcej informacji, zapoznaj się z sekcją [często zadawanych pytań](#frequently-asked-questions) w tym artykule. Aby uzyskać więcej informacji na temat usług Kubernetes Services, zobacz [usługi][services] w dokumentacji Kubernetes.

**Kubernetes adres IP usługi DNS**: adres IP usługi DNS klastra. Ten adres musi znajdować się w *zakresie adresów usługi Kubernetes*. Nie używaj pierwszego adresu IP z zakresu adresów, takiego jak. 1. Pierwszy adres w zakresie podsieci jest używany dla adresu *Kubernetes. default. svc. Cluster. Local* .

**Adres mostka platformy Docker**: adres sieciowy mostka platformy Docker reprezentuje domyślny adres sieciowy mostka *docker0* występujący we wszystkich instalacjach platformy Docker. Chociaż mostek *docker0* nie jest używany przez klastry AKS ani same same z nich, należy ustawić ten adres, aby nadal obsługiwał scenariusze, takie jak *kompilacja Docker* w klastrze AKS. Jest wymagane wybranie CIDR dla adresu sieciowego mostka platformy Docker, ponieważ w przeciwnym razie platforma Docker automatycznie wybierze podsieć, która może powodować konflikt z innymi CIDR. Należy wybrać przestrzeń adresową, która nie koliduje z pozostałą częścią CIDR w sieci, w tym CIDR usług w klastrze i CIDR.

## <a name="configure-networking---cli"></a>Konfigurowanie sieci — interfejs wiersza polecenia

Podczas tworzenia klastra AKS przy użyciu interfejsu wiersza polecenia platformy Azure można również skonfigurować usługę Azure CNI Networking. Użyj następujących poleceń, aby utworzyć nowy klaster AKS z włączoną obsługą sieci Azure CNI.

Najpierw Pobierz identyfikator zasobu podsieci dla istniejącej podsieci, do której zostanie przyłączony klaster AKS:

```azurecli-interactive
$ az network vnet subnet list \
    --resource-group myVnet \
    --vnet-name myVnet \
    --query "[0].id" --output tsv

/subscriptions/<guid>/resourceGroups/myVnet/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/default
```

Użyj polecenia [AZ AKS Create][az-aks-create] z `--network-plugin azure` argumentem, aby utworzyć klaster z zaawansowaną siecią. Zaktualizuj `--vnet-subnet-id` wartość przy użyciu identyfikatora podsieci zebranego w poprzednim kroku:

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --network-plugin azure \
    --vnet-subnet-id <subnet-id> \
    --docker-bridge-address 172.17.0.1/16 \
    --dns-service-ip 10.2.0.10 \
    --service-cidr 10.2.0.0/24 \
    --generate-ssh-keys
```

## <a name="configure-networking---portal"></a>Konfigurowanie sieci — Portal

Poniższy zrzut ekranu z Azure Portal zawiera przykład konfigurowania tych ustawień podczas tworzenia klastra AKS:

![Zaawansowana konfiguracja sieci w Azure Portal][portal-01-networking-advanced]

## <a name="frequently-asked-questions"></a>Często zadawane pytania

Poniższe pytania i odpowiedzi dotyczą konfiguracji sieci **usługi Azure CNI** .

* *Czy mogę wdrożyć maszyny wirtualne w podsieci klastra?*

  Tak.

* *Jakie źródłowe adresy IP są dostępne dla ruchu pochodzącego z usługi Azure CNI pod kątem?*

  Systemy w tej samej sieci wirtualnej, co klaster AKS, zobacz adres IP pod adresem źródłowym dla dowolnego ruchu z usługi. Systemy spoza sieci wirtualnej klastra AKS Zobacz adres IP węzła w przypadku każdego ruchu z usługi pod. 

* *Czy można skonfigurować zasady dotyczące sieci dla poszczególnych firm?*

  Tak, zasady sieciowe Kubernetes są dostępne w AKS. Aby rozpocząć, zobacz [bezpieczny ruch między elementami z użyciem zasad sieciowych w AKS][network-policy].

* *Czy maksymalna liczba identyfikatorów, które można wdrożyć w węźle konfigurowalne?*

  Tak, podczas wdrażania klastra przy użyciu interfejsu wiersza polecenia platformy Azure lub szablonu Menedżer zasobów. Zobacz [maksymalną liczbę zasobników na węzeł](#maximum-pods-per-node).

  Nie można zmienić maksymalnej liczby zasobników na węzeł w istniejącym klastrze.

* *Jak mogę skonfigurować dodatkowe właściwości podsieci, która została utworzona podczas tworzenia klastra AKS? Na przykład punkty końcowe usługi.*

  Pełna lista właściwości sieci wirtualnej i podsieci tworzonych podczas tworzenia klastra AKS można skonfigurować na stronie Konfiguracja standardowego sieci wirtualnej w Azure Portal.

* *Czy można użyć innej podsieci w sieci wirtualnej klastra dla* **zakresu adresów usługi Kubernetes**?

  Nie jest to zalecane, ale ta konfiguracja jest możliwa. Zakres adresów usługi to zestaw wirtualnych adresów IP (VIP), które Kubernetes przypisuje do usług wewnętrznych w klastrze. Sieć platformy Azure nie ma wglądu w zakres adresów IP usługi klastra Kubernetes. Ze względu na brak widoczności w zakresie adresów usługi klastra można później utworzyć nową podsieć w sieci wirtualnej klastra, która pokrywa się z zakresem adresów usługi. W takim przypadku Kubernetes może przypisać usługę, która jest już używana przez inny zasób w podsieci, powodując nieprzewidywalne zachowanie lub błędy. Dzięki zapewnieniu użycia zakresu adresów poza siecią wirtualną klastra można uniknąć nakładania się ryzyka.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o sieci w AKS w następujących artykułach:

- [Używanie statycznego adresu IP w usłudze Azure Kubernetes Service (AKS)](static-ip.md)
- [Korzystanie z wewnętrznego modułu równoważenia obciążenia z Azure Container Service (AKS)](internal-lb.md)

- [Tworzenie podstawowego kontrolera danych wejściowych z łącznością sieci zewnętrznej][aks-ingress-basic]
- [Włącz dodatek routingu aplikacji protokołu HTTP][aks-http-app-routing]
- [Utwórz kontroler transferu danych przychodzących, który używa wewnętrznej, prywatnej sieci i adresu IP][aks-ingress-internal]
- [Utwórz kontroler transferu danych przychodzących z dynamicznym publicznym adresem IP, a następnie skonfiguruj szyfrowanie, aby automatycznie generować certyfikaty TLS][aks-ingress-tls]
- [Utwórz kontroler transferu danych przychodzących ze statycznym publicznym adresem IP i skonfiguruj szyfrowanie, aby automatycznie generować certyfikaty TLS][aks-ingress-static-tls]

### <a name="aks-engine"></a>Aparat usługi AKS

[Aparat usługi Kubernetes platformy Azure (aparat AKS)][aks-engine] to projekt open-source, który generuje szablony Azure Resource Manager, których można użyć do wdrażania klastrów Kubernetes na platformie Azure.

Klastry Kubernetes utworzone za pomocą aparatu AKS obsługują zarówno wtyczki [korzystającą wtyczki kubenet][kubenet] , jak i [platformy Azure CNI][cni-networking] . W związku z tym oba scenariusze sieci są obsługiwane przez aparat AKS.

<!-- IMAGES -->
[advanced-networking-diagram-01]: ./media/networking-overview/advanced-networking-diagram-01.png
[portal-01-networking-advanced]: ./media/networking-overview/portal-01-networking-advanced.png

<!-- LINKS - External -->
[aks-engine]: https://github.com/Azure/aks-engine
[services]: https://kubernetes.io/docs/concepts/services-networking/service/
[portal]: https://portal.azure.com
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/cluster-administration/network-plugins/#kubenet

<!-- LINKS - Internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[aks-ssh]: ssh.md
[ManagedClusterAgentPoolProfile]: /azure/templates/microsoft.containerservice/managedclusters#managedclusteragentpoolprofile-object
[aks-network-concepts]: concepts-network.md
[aks-ingress-basic]: ingress-basic.md
[aks-ingress-tls]: ingress-tls.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-internal]: ingress-internal-ip.md
[network-policy]: use-network-policies.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[network-comparisons]: concepts-network.md#compare-network-models
[system-node-pools]: use-system-pools.md
