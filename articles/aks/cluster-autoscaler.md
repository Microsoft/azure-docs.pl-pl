---
title: Używanie funkcji automatycznego skalowania klastra w Azure Kubernetes Service (AKS)
description: Dowiedz się, jak za pomocą funkcji automatycznego skalowania klastra automatycznie skalować klaster w celu spełnienia wymagań aplikacji w klastrze Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 07/18/2019
ms.openlocfilehash: f02b91f73320786716e356639d8134280325dc19
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776001"
---
# <a name="automatically-scale-a-cluster-to-meet-application-demands-on-azure-kubernetes-service-aks"></a>Automatyczne skalowanie klastra w celu spełnienia wymagań aplikacji w usłudze Azure Kubernetes Service (AKS)

Aby być na bieżąco z wymaganiami aplikacji Azure Kubernetes Service (AKS), może być konieczne dostosowanie liczby węzłów, na których są uruchamiane obciążenia. Składnik automatycznego skalowania klastra może obserwować zasobniki w klastrze, których nie można zaplanować z powodu ograniczeń zasobów. W przypadku wykrycia problemów liczba węzłów w puli węzłów jest zwiększana w celu spełnienia wymagań aplikacji. Węzły są również regularnie sprawdzane pod kątem braku uruchomionych zasobników, a następnie liczba węzłów jest zmniejszana zgodnie z potrzebami. Ta możliwość automatycznego skalowania w górę lub w dół liczby węzłów w klastrze usługi AKS umożliwia uruchomienie wydajnego, ekonomicznego klastra.

W tym artykule pokazano, jak włączyć funkcję automatycznego skalowania klastra i zarządzać nim w klastrze usługi AKS.

## <a name="before-you-begin"></a>Zanim rozpoczniesz

Ten artykuł wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.76 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli-install].

## <a name="about-the-cluster-autoscaler"></a>Informacje o funkcji automatycznego skalowania klastra

Aby dostosować się do zmieniających się wymagań aplikacji, takich jak między dniem pracy a wieczorami lub w weekendy, klastry często potrzebują sposobu automatycznego skalowania. Klastry usługi AKS można skalować na jeden z dwóch sposobów:

* Funkcja **automatycznego skalowania klastra** obserwuje zasobniki, których nie można zaplanować w węzłach z powodu ograniczeń zasobów. Następnie klaster automatycznie zwiększa liczbę węzłów.
* Funkcja **horizontal pod autoscaler** używa serwera Metrics Server w klastrze Kubernetes do monitorowania zapotrzebowania na zasoby zasobników. Jeśli aplikacja wymaga większej ilości zasobów, liczba zasobników jest automatycznie zwiększana w celu spełnienia wymagań.

![Funkcja automatycznego skalowania klastra i funkcja horizontal pod autoscaler często współpracują ze sobą w celu obsługi wymaganych wymagań aplikacji](media/autoscaler/cluster-autoscaler.png)

Zarówno funkcja horizontal pod autoscaler, jak i funkcja automatycznego skalowania klastra mogą również zmniejszyć liczbę zasobników i węzłów zgodnie z potrzebami. Funkcja automatycznego skalowania klastra zmniejsza liczbę węzłów w przypadku nieużywanej pojemności przez określony czas. Zasobniki w węźle, które mają zostać usunięte przez funkcję automatycznego skalowania klastra, są bezpiecznie zaplanowane w innym miejscu w klastrze. Skalowanie automatyczne klastra może nie być w stanie skalować w dół, jeśli nie można przenieść zasobników, na przykład w następujących sytuacjach:

* Zasobnik jest tworzony bezpośrednio i nie jest tworzony przez obiekt kontrolera, taki jak zestaw wdrożeń lub replik.
* Budżet na przerwy w działaniu zasobników (PDB) jest zbyt restrykcyjny i nie zezwala na spadek liczby zasobników poniżej określonego progu.
* Zasobnik używa selektorów węzłów lub koligacji, których nie można honorować, jeśli są zaplanowane w innym węźle.

Aby uzyskać więcej informacji na temat tego, w jaki sposób skalowanie automatyczne klastra może nie być w stanie skalować w dół, zobacz Jakiego typu zasobniki mogą uniemożliwić automatyczne skalowanie klastra [usunięcie węzła?][autoscaler-scaledown]

Narzędzie do automatycznego skalowania klastra używa parametrów uruchamiania na przykład przedziałów czasu między zdarzeniami skalowania i progami zasobów. Aby uzyskać więcej informacji na temat parametrów, których używa funkcja automatycznego skalowania klastra, zobacz [Korzystanie z profilu autoskalowania.](#using-the-autoscaler-profile)

Klastry i rozwiązania do automatycznego skalowania zasobników w poziomie mogą ze sobą współpracować i często są wdrażane w klastrze. W połączeniu funkcja horizontal pod autoscaler koncentruje się na uruchamianiu liczby zasobników wymaganych do spełnienia wymagań aplikacji. Funkcja automatycznego skalowania klastra koncentruje się na uruchomieniu liczby węzłów wymaganych do obsługi zaplanowanych zasobników.

> [!NOTE]
> Skalowanie ręczne jest wyłączone w przypadku korzystania z funkcji automatycznego skalowania klastra. Pozwól, aby funkcja automatycznego skalowania klastra określała wymaganą liczbę węzłów. Jeśli chcesz ręcznie skalować klaster, wyłącz [funkcję automatycznego skalowania klastra.](#disable-the-cluster-autoscaler)

## <a name="create-an-aks-cluster-and-enable-the-cluster-autoscaler"></a>Tworzenie klastra usługi AKS i włączanie automatycznego skalowania klastra

Jeśli musisz utworzyć klaster usługi AKS, użyj [polecenia az aks create.][az-aks-create] Aby włączyć i skonfigurować funkcję automatycznego skalowania klastra w puli węzłów dla klastra, użyj parametru , a następnie `--enable-cluster-autoscaler` określ węzeł `--min-count` i `--max-count` .

> [!IMPORTANT]
> Funkcja automatycznego skalowania klastra jest składnikiem usługi Kubernetes. Mimo że klaster usługi AKS używa zestawu skalowania maszyn wirtualnych dla węzłów, nie należy ręcznie włączać ani edytować ustawień automatycznego skalowania zestawu skalowania w usłudze Azure Portal interfejsie wiersza polecenia platformy Azure. Pozwól, aby funkcja automatycznego skalowania klastra Kubernetes zarządzała wymaganymi ustawieniami skalowania. Aby uzyskać więcej informacji, zobacz [Czy mogę zmodyfikować zasoby AKS w grupie zasobów węzła?][aks-faq-node-resource-group]

Poniższy przykład tworzy klaster usługi AKS z pojedynczą pulą węzłów, która jest zapasowa przez zestaw skalowania maszyn wirtualnych. Umożliwia również automatyczne skalowanie klastra w puli węzłów klastra i ustawia co najmniej *1* lub *maksymalnie 3 węzły:*

```azurecli-interactive
# First create a resource group
az group create --name myResourceGroup --location eastus

# Now create the AKS cluster and enable the cluster autoscaler
az aks create \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --node-count 1 \
  --vm-set-type VirtualMachineScaleSets \
  --load-balancer-sku standard \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3
```

Utworzenie klastra i skonfigurowanie ustawień automatycznego skalowania klastra może potrwać kilka minut.

## <a name="update-an-existing-aks-cluster-to-enable-the-cluster-autoscaler"></a>Aktualizowanie istniejącego klastra usługi AKS w celu włączenia funkcji automatycznego skalowania klastra

Użyj polecenia [az aks update,][az-aks-update] aby włączyć i skonfigurować autoskalowanie klastra w puli węzłów dla istniejącego klastra. Użyj `--enable-cluster-autoscaler` parametru i określ węzeł oraz `--min-count` `--max-count` .

> [!IMPORTANT]
> Funkcja automatycznego skalowania klastra jest składnikiem usługi Kubernetes. Mimo że klaster usługi AKS używa zestawu skalowania maszyn wirtualnych dla węzłów, nie należy ręcznie włączać ani edytować ustawień automatycznego skalowania zestawu skalowania w usłudze Azure Portal ani przy użyciu interfejsu wiersza polecenia platformy Azure. Pozwól, aby funkcja automatycznego skalowania klastra Kubernetes zarządzała wymaganymi ustawieniami skalowania. Aby uzyskać więcej informacji, zobacz [Czy mogę zmodyfikować zasoby AKS w grupie zasobów węzła?][aks-faq-node-resource-group]

Poniższy przykład aktualizuje istniejący klaster usługi AKS, aby umożliwić automatyczne skalowanie klastra w puli węzłów klastra i ustawia co najmniej *1* lub *maksymalnie 3 węzły:*

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3
```

Aktualizacja klastra i skonfigurowanie ustawień automatycznego skalowania klastra może potrwać kilka minut.

## <a name="change-the-cluster-autoscaler-settings"></a>Zmienianie ustawień automatycznego skalowania klastra

> [!IMPORTANT]
> Jeśli klaster usługi AKS zawiera wiele pul węzłów, przejdź do sekcji [autoskalowania przy użyciu wielu pul agentów.](#use-the-cluster-autoscaler-with-multiple-node-pools-enabled) Klastry z wieloma pulami agentów wymagają użycia zestawu poleceń w celu zmiany właściwości specyficznych dla puli `az aks nodepool` węzłów zamiast `az aks` .

W poprzednim kroku tworzenia klastra usługi AKS lub aktualizowania istniejącej puli węzłów minimalna liczba węzłów skalowania automatycznego klastra została ustawiona na *1*, a maksymalna liczba węzłów została ustawiona *na 3*. W przypadku zmiany wymagań aplikacji może być konieczne dostosowanie liczby węzłów funkcji automatycznego skalowania klastra.

Aby zmienić liczbę węzłów, użyj [polecenia az aks update.][az-aks-update]

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --update-cluster-autoscaler \
  --min-count 1 \
  --max-count 5
```

Powyższy przykład aktualizuje funkcję automatycznego skalowania klastra w puli pojedynczych węzłów w klastrze *myAKSCluster* do co najmniej *1* lub *maksymalnie 5 węzłów.*

> [!NOTE]
> Funkcja automatycznego skalowania klastra podejmuje decyzje dotyczące skalowania na podstawie minimalnej i maksymalnej liczby ustawionej w każdej puli węzłów, ale nie wymusza ich po zaktualizowaniu minimalnej lub maksymalnej liczby. Na przykład ustawienie minimalnej liczby 5, gdy bieżąca liczba węzłów wynosi 3, nie spowoduje natychmiastowego skalowania puli do 5. Jeśli minimalna liczba w puli węzłów ma wartość większą niż bieżąca liczba węzłów, nowe minimalne lub maksymalne ustawienia będą przestrzegane, gdy istnieje wystarczająca liczba nieplanowanych zasobników, które wymagają 2 nowych dodatkowych węzłów i wyzwalają zdarzenie autoskalowania. Po zdarzeniu skalowania nowe limity liczby są przestrzegane.

Monitoruj wydajność aplikacji i usług oraz dostosuj liczbę węzłów autoskalowania klastra, aby dopasować je do wymaganej wydajności.

## <a name="using-the-autoscaler-profile"></a>Korzystanie z profilu autoskalowania

Można również skonfigurować bardziej szczegółowe szczegóły autoskalowania klastra, zmieniając wartości domyślne w profilu automatycznego skalowania całego klastra. Na przykład zdarzenie skalowania w dół występuje, gdy węzły nie są w pełni wykorzystywane po 10 minutach. Jeśli masz obciążenia, które były skalowane co 15 minut, możesz zmienić profil skalowania automatycznego w celu skalowania w dół w ramach wykorzystywanych węzłów po upływie 15 lub 20 minut. Po włączeniu funkcji automatycznego skalowania klastra używany jest profil domyślny, chyba że określisz inne ustawienia. Profil automatycznego skalowania klastra ma następujące ustawienia, które można zaktualizować:

| Ustawienie                          | Opis                                                                              | Wartość domyślna |
|----------------------------------|------------------------------------------------------------------------------------------|---------------|
| interwał skanowania                    | Jak często klaster jest ponownievaluowany w celu skalowania w górę lub w dół                                    | 10 sekund    |
| scale-down-delay-after-add       | Jak długo po wznowieniu oceny skalowania w górę w dół                               | 10 minut    |
| scale-down-delay-after-delete    | Jak długo po usunięciu węzła wznawiana jest ocena skalowania w dół                          | interwał skanowania |
| scale-down-delay-after-failure   | Jak długo po niepowodzeniu skalowania w dół wznawia się ocena skalowania w dół                     | 3 minuty     |
| skalowanie w dół — czas niepotrzebny         | Jak długo węzeł powinien być niepotrzebny, zanim kwalifikuje się do skalowania w dół                  | 10 minut    |
| skalowanie w dół — czas nieosiągów          | Jak długo niezarejestrowany węzeł powinien być niepotrzebny, zanim będzie kwalifikować się do skalowania w dół         | 20 minut    |
| scale-down-utilization-threshold | Poziom wykorzystania węzłów zdefiniowany jako suma żądanych zasobów podzielonych przez pojemność, poniżej której można rozważyć skalowanie węzła w dół | 0,5 |
| maksymalna liczba zakończenie-graceful-s     | Maksymalna liczba sekund oczekiwania przez autoskalowanie klastra na zakończenie działania zasobnika podczas próby skalowania węzła w dół | 600 sekund   |
| balance-similar-node-groups      | Wykrywa podobne pule węzłów i równoważy liczbę węzłów między nimi                 | fałsz         |
| Expander                         | Typ eksp [expandera puli węzłów,](https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-are-expanders) który ma być używany w skalowania w górę. Możliwe wartości: `most-pods` , `random` , `least-waste` , `priority` | losowo | 
| skip-nodes-with-local-storage    | Jeśli funkcja automatycznego skalowania klastra true nigdy nie usunie węzłów z zasobnikami z magazynem lokalnym, na przykład EmptyDir lub HostPath | true |
| skip-nodes-with-system-pods      | Jeśli funkcja automatycznego skalowania klastra true nigdy nie usunie węzłów z zasobnikami z zestawu kube-system (z wyjątkiem zestawu DaemonSet lub zasobników dublowania) | true | 
| max-empty-bulk-delete            | Maksymalna liczba pustych węzłów, które można usunąć w tym samym czasie                       | 10 węzłów      |
| new-pod-scale-up-delay           | W scenariuszach takich jak skalowanie wsadowe/burst, w przypadku których nie chcesz, aby urząd certyfikacji działał przed zaplanowaniem wszystkich zasobników przez harmonogram kubernetes, możesz poinformować urząd certyfikacji, aby ignorował nieplanowane zasobniki przed określonym wiekiem.                                                                                                                | 0 sekund    |
| max-total-unready-percentage     | Maksymalna wartość procentowa niezarejestrowania węzłów w klastrze. Po przekroczeniu tej wartości procentowej urząd certyfikacji zatrzymuje operacje | 45% |
| max-node-provision-time          | Maksymalny czas oczekiwania przez autoskalowanie na aprowizowanie węzła                           | 15 minut    |   
| ok-total-unready-count           | Liczba dozwolonych, nieosiąganych węzłów, niezależnie od wartości maksymalnej całkowitej i nierozpoznawczej wartości procentowej            | 3 węzły       |

> [!IMPORTANT]
> Profil automatycznego skalowania klastra ma wpływ na wszystkie pule węzłów, które korzystają z funkcji automatycznego skalowania klastra. Nie można ustawić profilu automatycznego skalowania dla puli węzłów.
>
> Profil automatycznego skalowania klastra wymaga interfejsu wiersza polecenia platformy Azure w wersji *2.11.1* lub większej. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli-install].

### <a name="set-the-cluster-autoscaler-profile-on-an-existing-aks-cluster"></a>Ustawianie profilu automatycznego skalowania klastra w istniejącym klastrze usługi AKS

Użyj polecenia [az aks update][az-aks-update-preview] z parametrem *cluster-autoscaler-profile,* aby ustawić profil automatycznego skalowania klastra w klastrze. W poniższym przykładzie ustawienie interwału skanowania jest konfigurowane jako 30 s w profilu.

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --cluster-autoscaler-profile scan-interval=30s
```

Po włączeniu funkcji automatycznego skalowania klastra w pulach węzłów w klastrze te klastry będą również używać profilu automatycznego skalowania klastra. Na przykład:

```azurecli-interactive
az aks nodepool update \
  --resource-group myResourceGroup \
  --cluster-name myAKSCluster \
  --name mynodepool \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3
```

> [!IMPORTANT]
> Po skonfigurowaniu profilu automatycznego skalowania klastra wszystkie istniejące pule węzłów z włączonym profilem automatycznego skalowania klastra natychmiast zaczną korzystać z profilu.

### <a name="set-the-cluster-autoscaler-profile-when-creating-an-aks-cluster"></a>Ustawianie profilu automatycznego skalowania klastra podczas tworzenia klastra usługi AKS

Podczas tworzenia klastra można również użyć *parametru cluster-autoscaler-profile.* Na przykład:

```azurecli-interactive
az aks create \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --node-count 1 \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3 \
  --cluster-autoscaler-profile scan-interval=30s
```

Powyższe polecenie tworzy klaster usługi AKS i definiuje interwał skanowania jako 30 sekund dla profilu skalowania automatycznego w całym klastrze. Polecenie włącza również funkcję automatycznego skalowania klastra w początkowej puli węzłów, ustawia minimalną liczbę węzłów na 1, a maksymalną liczbę węzłów na 3.

### <a name="reset-cluster-autoscaler-profile-to-default-values"></a>Resetowanie profilu automatycznego skalowania klastra do wartości domyślnych

Użyj polecenia [az aks update,][az-aks-update-preview] aby zresetować profil automatycznego skalowania klastra w klastrze.

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --cluster-autoscaler-profile ""
```

## <a name="disable-the-cluster-autoscaler"></a>Wyłączanie automatycznego skalowania klastra

Jeśli nie chcesz już używać narzędzia do automatycznego skalowania klastra, możesz go wyłączyć za pomocą polecenia [az aks update,][az-aks-update-preview] określając `--disable-cluster-autoscaler` parametr . Węzły nie są usuwane, gdy funkcja automatycznego skalowania klastra jest wyłączona.

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --disable-cluster-autoscaler
```

Po wyłączeniu narzędzia do automatycznego skalowania klastra można ręcznie skalować klaster za pomocą [polecenia az aks scale.][az-aks-scale] Jeśli używasz funkcji horizontal pod autoscaler, ta funkcja będzie nadal działać z wyłączonym skalowania klastra, ale może się okazać, że nie będzie można zaplanować zasobników, jeśli wszystkie zasoby węzła są w użyciu.

## <a name="re-enable-a-disabled-cluster-autoscaler"></a>Ponowne włączanie wyłączonego automatycznego skalowania klastra

Jeśli chcesz ponownie włączyć funkcję automatycznego skalowania klastra w istniejącym klastrze, możesz ją ponownie włączyć przy użyciu polecenia [az aks update,][az-aks-update-preview] określając parametry `--enable-cluster-autoscaler` , i `--min-count` `--max-count` .

## <a name="retrieve-cluster-autoscaler-logs-and-status"></a>Pobieranie dzienników i stanu automatycznego skalowania klastra

Aby diagnozować i debugować zdarzenia autoskalowania, dzienniki i stan można pobrać z dodatku do automatycznego skalowania.

Usługa AKS zarządza usługą autoskalowania klastra w Twoim imieniu i uruchamia ją na zarządzanej płaszczyźnie sterowania. Możesz włączyć węzeł płaszczyzny sterowania, aby wyświetlić dzienniki i operacje z urzędu certyfikacji.

Aby skonfigurować wypychanie dzienników z narzędzia do automatycznego skalowania klastra do usługi Log Analytics, wykonaj następujące kroki.

1. Skonfiguruj regułę dla dzienników zasobów w celu wypychania dzienników narzędzia cluster-autoscaler do usługi Log Analytics. [Instrukcje zostały szczegółowo opisane tutaj][aks-view-master-logs]. Upewnij się, że pole wyboru jest sprawdzane podczas `cluster-autoscaler` wybierania opcji dla opcji "Dzienniki".
1. Wybierz sekcję "Dzienniki" w klastrze za pośrednictwem Azure Portal.
1. Wprowadź następujące przykładowe zapytanie do usługi Log Analytics:

```
AzureDiagnostics
| where Category == "cluster-autoscaler"
```

Dzienniki powinny być podobne do poniższego przykładu, o ile istnieją dzienniki do pobrania.

![Dzienniki usługi Log Analytics](media/autoscaler/autoscaler-logs.png)

Funkcja automatycznego skalowania klastra zapisze również stan kondycji dla o `configmap` nazwie `cluster-autoscaler-status` . Aby pobrać te dzienniki, wykonaj następujące `kubectl` polecenie. Stan kondycji będzie zgłaszany dla każdej puli węzłów skonfigurowanej za pomocą funkcji automatycznego skalowania klastra.

```
kubectl get configmap -n kube-system cluster-autoscaler-status -o yaml
```

Aby dowiedzieć się więcej o tym, co jest rejestrowane z autoskalowania, przeczytaj często zadawane pytania dotyczące projektu [Kubernetes/autoscaler w witrynie GitHub.][kubernetes-faq]

## <a name="use-the-cluster-autoscaler-with-multiple-node-pools-enabled"></a>Używanie automatycznego skalowania klastra z włączonymi wieloma pulami węzłów

Funkcji automatycznego skalowania klastra można używać razem z wieloma [włączonymi pulami węzłów.][aks-multiple-node-pools] Postępuj zgodnie z tym dokumentem, aby dowiedzieć się, jak włączyć wiele pul węzłów i dodać dodatkowe pule węzłów do istniejącego klastra. W przypadku używania obu tych funkcji jednocześnie należy włączyć funkcję automatycznego skalowania klastra w każdej puli węzłów w klastrze i przekazać do każdej z nich unikatowe reguły skalowania automatycznego.

W poniższym poleceniu [](#create-an-aks-cluster-and-enable-the-cluster-autoscaler) przyjęto założenie, że instrukcje początkowe są stosowane wcześniej w tym dokumencie i chcesz zaktualizować maksymalną liczbę węzłów istniejącej puli węzłów z *3* do *5*. Użyj polecenia [az aks nodepool update,][az-aks-nodepool-update] aby zaktualizować ustawienia istniejącej puli węzłów.

```azurecli-interactive
az aks nodepool update \
  --resource-group myResourceGroup \
  --cluster-name myAKSCluster \
  --name nodepool1 \
  --update-cluster-autoscaler \
  --min-count 1 \
  --max-count 5
```

Autoskalowanie klastra można wyłączyć za pomocą [funkcji az aks nodepool update][az-aks-nodepool-update] i przekazując parametr `--disable-cluster-autoscaler` .

```azurecli-interactive
az aks nodepool update \
  --resource-group myResourceGroup \
  --cluster-name myAKSCluster \
  --name nodepool1 \
  --disable-cluster-autoscaler
```

Jeśli chcesz ponownie włączyć funkcję automatycznego skalowania klastra w istniejącym klastrze, możesz ją ponownie włączyć przy użyciu polecenia [az aks nodepool update,][az-aks-nodepool-update] określając parametry `--enable-cluster-autoscaler` , i `--min-count` `--max-count` .

> [!NOTE]
> Jeśli planujesz używanie narzędzia do automatycznego skalowania klastra z buforami węzłów, które obejmują wiele stref i korzystasz z funkcji planowania związanych ze strefami, takich jak planowanie topologiczne woluminów, zaleca się użycie jednej puli węzłów na strefę i włączenie funkcji za pośrednictwem profilu `--balance-similar-node-groups` automatycznego skalowania. Zapewni to, że skalowanie automatyczne zostanie pomyślnie przeskalowane w górę, a rozmiary puli węzłów będą zrównoważone.

## <a name="next-steps"></a>Następne kroki

W tym artykule popisano, jak automatycznie skalować liczbę węzłów usługi AKS. Możesz również użyć funkcji horizontal pod autoscaler, aby automatycznie dostosować liczbę zasobników, na których jest uruchamiana aplikacja. Aby uzyskać instrukcje dotyczące korzystania z narzędzia Horizontal Pod Autoscaler, zobacz [Scale applications in AKS (Skalowanie aplikacji w UKS).][aks-scale-apps]

<!-- LINKS - internal -->
[aks-faq]: faq.md
[aks-faq-node-resource-group]: faq.md#can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-node-resource-group
[aks-multiple-node-pools]: use-multiple-node-pools.md
[aks-scale-apps]: tutorial-kubernetes-scale.md
[aks-support-policies]: support-policies.md
[aks-upgrade]: upgrade-cluster.md
[aks-view-master-logs]: ./view-control-plane-logs.md#enable-resource-logs
[autoscaler-profile-properties]: #using-the-autoscaler-profile
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az_aks_show
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-update]: /cli/azure/aks#az_aks_update
[az-aks-scale]: /cli/azure/aks#az_aks_scale
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register

<!-- LINKS - external -->
[az-aks-update-preview]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview
[az-aks-nodepool-update]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview#enable-cluster-auto-scaler-for-a-node-pool
[autoscaler-scaledown]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-types-of-pods-can-prevent-ca-from-removing-a-node
[autoscaler-parameters]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-are-the-parameters-to-ca
[kubernetes-faq]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#ca-doesnt-work-but-it-used-to-work-yesterday-why