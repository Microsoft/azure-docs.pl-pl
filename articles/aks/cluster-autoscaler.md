---
title: Korzystanie z automatycznego skalowania klastra w usłudze Azure Kubernetes Service (AKS)
description: Dowiedz się, jak za pomocą automatycznego skalowania klastra automatycznie skalować klaster w celu spełnienia wymagań aplikacji w klastrze usługi Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 07/18/2019
ms.openlocfilehash: 9f1dcc64569e9822e3703312740450e2528479dc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88257518"
---
# <a name="automatically-scale-a-cluster-to-meet-application-demands-on-azure-kubernetes-service-aks"></a>Automatyczne skalowanie klastra w celu spełnienia wymagań aplikacji w usłudze Azure Kubernetes Service (AKS)

Aby zachować wymagania dotyczące aplikacji w usłudze Azure Kubernetes Service (AKS), może być konieczne dostosowanie liczby węzłów, na których działają obciążenia. Składnik automatycznego skalowania klastra można obejrzeć w przypadku zasobników w klastrze, których nie można zaplanować ze względu na ograniczenia zasobów. W przypadku wykrycia problemów liczba węzłów w puli węzłów zostanie zwiększona w celu spełnienia wymagań aplikacji. Węzły są również regularnie sprawdzane pod kątem braku uruchomionych zasobników, a liczba węzłów zmniejszyła się w miarę potrzeby. Możliwość automatycznego skalowania w górę lub w dół liczby węzłów w klastrze AKS umożliwia uruchamianie wydajnego, ekonomicznego klastra.

W tym artykule opisano sposób włączania automatycznego skalowania klastra i zarządzania nim w klastrze AKS.

## <a name="before-you-begin"></a>Zanim rozpoczniesz

Ten artykuł wymaga uruchomienia interfejsu wiersza polecenia platformy Azure w wersji 2.0.76 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli-install].

## <a name="about-the-cluster-autoscaler"></a>Informacje o automatycznym skalowaniu klastra

Aby dostosować się do zmieniających się wymagań aplikacji, takich jak między dniem i wieczór lub w weekendie, klastry często potrzebują sposobu na automatyczne skalowanie. Klastry AKS można skalować na jeden z dwóch sposobów:

* **Automatyczne skalowanie klastra** jest obserwujące dla zasobników, których nie można zaplanować w węzłach ze względu na ograniczenia zasobów. Klaster automatycznie zwiększa liczbę węzłów.
* **Skalowanie w poziomie** poniżej używa serwera metryk w klastrze Kubernetes do monitorowania zapotrzebowania na zasoby. Jeśli aplikacja wymaga większej liczby zasobów, liczba numerów jest automatycznie zwiększana, aby sprostać zapotrzebowaniu.

![Automatyczne skalowanie klastra i skalowanie w poziomie, które często współpracują ze sobą, obsługują wymagane wymagania aplikacji](media/autoscaler/cluster-autoscaler.png)

Zarówno Skalowanie automatyczne, jak i automatyczne skalowanie klastra mogą również zmniejszać liczbę i węzły w razie konieczności. Automatyczne skalowanie klastra zmniejsza liczbę węzłów, gdy w danym okresie czasu wykorzystano nieużywaną pojemność. W węźle, który ma zostać usunięty przez automatyczne skalowanie klastra, są bezpiecznie zaplanowane w innym miejscu w klastrze. Automatyczne skalowanie klastra może nie być w stanie skalować w dół, jeśli nie można przenieść zasobników, na przykład w następujących sytuacjach:

* Element pod jest bezpośrednio tworzony i nie jest obsługiwany przez obiekt kontrolera, taki jak wdrożenie lub zestaw replik.
* Budżet (PDB) jest zbyt restrykcyjny i nie zezwala na spadek liczby numerów w wysokości poniżej określonego progu.
* Przy użyciu selektorów węzłów lub zapobiegania, których nie można uznać, jeśli zaplanowano w innym węźle.

Aby uzyskać więcej informacji o tym, jak może być niemożliwe skalowanie automatycznego skalowania klastra, zobacz, [jakie typy zasobników mogą uniemożliwić usunięcie węzła przez automatyczne skalowanie klastra?][autoscaler-scaledown]

Automatyczne skalowanie klastra używa parametrów uruchamiania dla elementów, takich jak przedziały czasu między zdarzeniami skalowania i progami zasobów. Aby uzyskać więcej informacji na temat parametrów używanych przez automatyczne skalowanie klastra, zobacz [Korzystanie z profilu skalowania automatycznego](#using-the-autoscaler-profile).

Klaster i automatyczne skalowanie w poziomie mogą współdziałać ze sobą i są często wdrażane w klastrze. Po połączeniu, skalowanie w poziomie na pionie jest ukierunkowane na uruchamianie liczby numerów wymaganych do spełnienia wymagań aplikacji. Automatyczne skalowanie klastra koncentruje się na uruchamianiu liczby węzłów wymaganych do obsługi zaplanowanych zasobników.

> [!NOTE]
> Skalowanie ręczne jest wyłączone w przypadku korzystania z automatycznego skalowania klastra. Pozwól, aby automatyczne skalowanie klastra określiło wymaganą liczbę węzłów. Jeśli chcesz ręcznie skalować klaster, [Wyłącz automatyczne skalowanie klastra](#disable-the-cluster-autoscaler).

## <a name="create-an-aks-cluster-and-enable-the-cluster-autoscaler"></a>Tworzenie klastra AKS i Włączanie automatycznego skalowania klastra

Jeśli musisz utworzyć klaster AKS, użyj polecenia [AZ AKS Create][az-aks-create] . Aby włączyć i skonfigurować automatyczne skalowanie klastra w puli węzłów klastra, należy użyć parametru *--enable-Cluster-autoscaleer* i określić węzeł *--min-Count* i *--Max-Count*.

> [!IMPORTANT]
> Automatyczne skalowanie klastra to składnik Kubernetes. Chociaż klaster AKS korzysta z zestawu skalowania maszyn wirtualnych dla węzłów, nie włączaj ręcznie ani nie edytuj ustawień skalowania automatycznego skalowania w Azure Portal lub przy użyciu interfejsu wiersza polecenia platformy Azure. Zezwól automatycznemu skalowaniu klastra Kubernetes na zarządzanie wymaganymi ustawieniami skalowania. Aby uzyskać więcej informacji, zobacz [czy można modyfikować zasoby AKS w grupie zasobów węzła?][aks-faq-node-resource-group]

W poniższym przykładzie tworzony jest klaster AKS z pulą jednego węzła, które są obsługiwane przez zestaw skalowania maszyn wirtualnych. Włącza również automatyczne skalowanie klastra w puli węzłów klastra i ustawia co najmniej *1* i maksymalnie *3* węzły:

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

Utworzenie klastra i skonfigurowanie ustawień automatycznego skalowania klastra trwa kilka minut.

## <a name="update-an-existing-aks-cluster-to-enable-the-cluster-autoscaler"></a>Aktualizowanie istniejącego klastra AKS w celu włączenia automatycznego skalowania klastra

Użyj polecenia [AZ AKS Update][az-aks-update] , aby włączyć i skonfigurować automatyczne skalowanie klastra w puli węzłów dla istniejącego klastra. Użyj parametru *--enable-Cluster-AutoScale* , a następnie określ węzeł *--min-Count* i *--Max-Count*.

> [!IMPORTANT]
> Automatyczne skalowanie klastra to składnik Kubernetes. Chociaż klaster AKS korzysta z zestawu skalowania maszyn wirtualnych dla węzłów, nie włączaj ręcznie ani nie edytuj ustawień skalowania automatycznego skalowania w Azure Portal lub przy użyciu interfejsu wiersza polecenia platformy Azure. Zezwól automatycznemu skalowaniu klastra Kubernetes na zarządzanie wymaganymi ustawieniami skalowania. Aby uzyskać więcej informacji, zobacz [czy można modyfikować zasoby AKS w grupie zasobów węzła?][aks-faq-node-resource-group]

Poniższy przykład aktualizuje istniejący klaster AKS, aby włączyć automatyczne skalowanie klastra w puli węzłów dla klastra i ustawić co najmniej *1* i maksymalnie *3* węzły:

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3
```

Zaktualizowanie klastra i skonfigurowanie ustawień automatycznego skalowania klastra trwa kilka minut.

## <a name="change-the-cluster-autoscaler-settings"></a>Zmień ustawienia automatycznego skalowania klastra

> [!IMPORTANT]
> Jeśli masz wiele pul węzłów w klastrze AKS, przejdź do [sekcji Automatyczne skalowanie z wieloma pulami agentów](#use-the-cluster-autoscaler-with-multiple-node-pools-enabled). Klastry z wieloma pulami agentów wymagają użycia `az aks nodepool` zestawu poleceń, aby zmienić właściwości specyficzne dla puli węzłów zamiast `az aks` .

W poprzednim kroku, aby utworzyć klaster AKS lub zaktualizować istniejącą pulę węzłów, minimalna liczba węzłów w ramach automatycznego skalowania klastra została ustawiona na *1*, a maksymalna liczba węzłów została ustawiona na wartość *3*. Gdy aplikacja wymaga zmiany, może być konieczne dostosowanie liczby węzłów automatycznego skalowania klastra.

Aby zmienić liczbę węzłów, użyj polecenia [AZ AKS Update][az-aks-update] .

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --update-cluster-autoscaler \
  --min-count 1 \
  --max-count 5
```

Powyższy przykład aktualizuje automatyczne skalowanie klastra w puli jednego węzła w *myAKSCluster* do co najmniej *1* i maksymalnie *5* węzłów.

> [!NOTE]
> Automatyczne skalowanie klastra podejmuje decyzje dotyczące skalowania na podstawie minimalnej i maksymalnej liczby zestawów ustawionych w każdej puli węzłów, ale nie wymusza ich po aktualizacji minimalnej lub maksymalnej liczby. Na przykład ustawienie minimalnej liczby 5, gdy bieżąca liczba węzłów wynosi 3, nie spowoduje natychmiastowego skalowania puli do 5. Jeśli minimalna liczba w puli węzłów ma wartość wyższą niż bieżąca liczba węzłów, nowe ustawienia minimalne lub maksymalne będą przestrzegane, gdy jest dostępnych wystarczającą liczbę unschedulableowych zasobników, które wymagają 2 nowych węzłów i wyzwala zdarzenie skalowania automatycznego. Po zdarzeniu skalowania są przestrzegane nowe limity liczby.

Monitoruj wydajność aplikacji i usług, a następnie dostosuj liczbę węzłów automatycznego skalowania klastra, aby dopasować ją do wymaganej wydajności.

## <a name="using-the-autoscaler-profile"></a>Korzystanie z profilu skalowania automatycznego

Możesz również skonfigurować bardziej szczegółowe szczegóły automatycznego skalowania klastra, zmieniając wartości domyślne w profilu skalowania automatycznego dla całego klastra. Na przykład zdarzenie skalowania w dół występuje po upływie 10 minut użycia węzłów. Jeśli masz obciążenia, które są uruchamiane co 15 minut, możesz zmienić profil skalowania automatycznego, aby skalować w dół w obszarze używanych węzłów po 15 lub 20 minutach. Po włączeniu automatycznego skalowania klastra zostanie użyty profil domyślny, chyba że określisz inne ustawienia. Profil automatycznego skalowania klastra ma następujące ustawienia, które można zaktualizować:

| Ustawienie                          | Opis                                                                              | Wartość domyślna |
|----------------------------------|------------------------------------------------------------------------------------------|---------------|
| Interwał skanowania                    | Częstotliwość, z jaką klaster jest obliczany na potrzeby skalowania w górę lub w dół                                    | 10 sekund    |
| skalowanie w dół — opóźnienie po dodaniu       | Czas, po którym skalowanie w dół w dół powoduje wznowienie oceny                               | 10 minut    |
| skalowanie w dół — opóźnienie po usunięciu    | Jak długo po usunięciu węzła zostanie wznowione skalowanie w dół                          | Interwał skanowania |
| skalowanie w dół — opóźnienie po awarii   | Jak długo po awarii skalowania w dół, skalowanie w dół powoduje wznowienie obliczeń                     | 3 minuty     |
| skalowanie w dół — niepotrzebny czas         | Jak długo węzeł powinien być niepotrzebny, zanim będzie można go przystąpić do skalowania w dół                  | 10 minut    |
| skalowanie w dół-czas nieczytelny          | Jak długo węzeł nieczytelny powinien być niepotrzebny, zanim będzie uprawniony do skalowania w dół         | 20 minut    |
| skalowanie w dół — próg użycia | Poziom użycia węzła zdefiniowany jako suma żądanych zasobów podzielona przez pojemność, poniżej którego węzeł może być brany pod uwagę dla skalowania w dół | 0,5 |
| maks-bezpiecznie-zakończenie-s     | Maksymalna liczba sekund, przez jaką automatyczne skalowanie klastra czeka na zakończenie podczas próby skalowania w dół węzła. | 600 sekund   |
| równowaga — podobne-grupy węzłów | Wykrywaj podobne pule węzłów i Zrównoważ liczbę węzłów między nimi | fałsz |

> [!IMPORTANT]
> Profil automatycznego skalowania klastra ma wpływ na wszystkie pule węzłów używające automatycznego skalowania klastra. Nie można ustawić profilu autoskalowania na pulę węzłów.

### <a name="install-aks-preview-cli-extension"></a>Instalowanie rozszerzenia interfejsu wiersza polecenia aks-preview

Aby ustawić profil ustawień automatycznego skalowania klastra, wymagany jest interfejs wiersza polecenia *AKS-Preview* w wersji 0.4.30 lub nowszej. Zainstaluj rozszerzenie interfejsu wiersza polecenia platformy Azure w *wersji zapoznawczej* przy użyciu poleceń [AZ Extension Add][az-extension-add] , a następnie wyszukaj wszystkie dostępne aktualizacje za pomocą polecenia [AZ Extension Update][az-extension-update] :

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="set-the-cluster-autoscaler-profile-on-an-existing-aks-cluster"></a>Ustawianie profilu automatycznego skalowania klastra w istniejącym klastrze AKS

Użyj polecenia [AZ AKS Update][az-aks-update-preview] z parametrem *cluster-autoscaleing-profile* , aby ustawić profil automatycznego skalowania klastra w klastrze. Poniższy przykład konfiguruje ustawienie Interwał skanowania jako 30 s w profilu.

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --cluster-autoscaler-profile scan-interval=30s
```

Po włączeniu automatycznego skalowania klastra w pulach węzłów w klastrze te klastry będą również używać profilu automatycznego skalowania klastra. Na przykład:

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
> Po ustawieniu profilu automatycznego skalowania klastra wszystkie istniejące pule węzłów z włączonym automatycznym skalowaniem klastra rozpocznie korzystanie z profilu od razu.

### <a name="set-the-cluster-autoscaler-profile-when-creating-an-aks-cluster"></a>Ustawianie profilu automatycznego skalowania klastra podczas tworzenia klastra AKS

Podczas tworzenia klastra można również użyć parametru *klaster-autoscaleer* . Na przykład:

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

Powyższe polecenie tworzy klaster AKS i definiuje Interwał skanowania jako 30 sekund dla profilu automatycznego skalowania w całym klastrze. Polecenie włącza również automatyczne skalowanie klastra w początkowej puli węzłów, ustawia minimalną liczbę węzłów na 1 i maksymalną liczbę węzłów równą 3.

### <a name="reset-cluster-autoscaler-profile-to-default-values"></a>Zresetuj profil automatycznego skalowania klastra do wartości domyślnych

Za pomocą polecenia [AZ AKS Update][az-aks-update-preview] Zresetuj profil automatycznego skalowania klastra w klastrze.

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --cluster-autoscaler-profile ""
```

## <a name="disable-the-cluster-autoscaler"></a>Wyłącz automatyczne skalowanie klastra

Jeśli nie chcesz już używać automatycznego skalowania klastra, możesz je wyłączyć za pomocą polecenia [AZ AKS Update][az-aks-update-preview] , określając parametr *--disable-Cluster-autoscaleer* . Węzły nie są usuwane, gdy automatyczne skalowanie klastra jest wyłączone.

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --disable-cluster-autoscaler
```

Możesz ręcznie skalować klaster po wyłączeniu automatycznego skalowania klastra za pomocą polecenia [AZ AKS Scale][az-aks-scale] . W przypadku korzystania z funkcji automatycznego skalowania w poziomie, ta funkcja będzie nadal uruchamiana z wyłączonym automatycznym skalowaniem klastra, ale w przypadku używania wszystkich zasobów węzłów nie można zaplanować wystąpienia.

## <a name="re-enable-a-disabled-cluster-autoscaler"></a>Ponowne włączenie wyłączonego automatycznego skalowania klastra

Jeśli chcesz ponownie włączyć automatyczne skalowanie klastra w istniejącym klastrze, możesz je ponownie włączyć przy użyciu polecenia [AZ AKS Update][az-aks-update-preview] , określając parametr *--enable-Cluster-AutoScale*, *--min-Count*i *--Max-Count* .

## <a name="retrieve-cluster-autoscaler-logs-and-status"></a>Pobieranie dzienników i stanu automatycznego skalowania klastra

Aby zdiagnozować i debugować zdarzenia automatycznego skalowania, dzienniki i stan można pobrać z dodatku automatycznego skalowania.

AKS zarządza automatycznym skalowaniem klastra w Twoim imieniu i uruchamia go na zarządzanej płaszczyźnie kontroli. Dzienniki węzłów głównych muszą być skonfigurowane tak, aby były wyświetlane w wyniku.

Aby skonfigurować dzienniki do wypychania z automatycznego skalowania klastra do Log Analytics, wykonaj następujące kroki.

1. Skonfiguruj regułę dla dzienników zasobów, aby wypychanie dzienników automatycznego skalowania klastra do Log Analytics. [Instrukcje są tutaj szczegółowe][aks-view-master-logs]. Upewnij się, że pole wyboru jest zaznaczone, aby `cluster-autoscaler` wybrać opcję "dzienniki".
1. Kliknij sekcję "Logs" w klastrze za pomocą Azure Portal.
1. Wprowadź następujące przykładowe zapytanie do Log Analytics:

```
AzureDiagnostics
| where Category == "cluster-autoscaler"
```

Dzienniki powinny wyglądać podobnie do poniższego przykładu, o ile dzienniki mają być pobierane.

![Dzienniki Log Analytics](media/autoscaler/autoscaler-logs.png)

Automatyczne skalowanie klastra również zapisuje stan kondycji do configmap o nazwie `cluster-autoscaler-status` . Aby pobrać te dzienniki, wykonaj następujące `kubectl` polecenie. Dla każdej puli węzłów skonfigurowanej przy użyciu automatycznego skalowania klastra zostanie zgłoszony stan kondycji.

```
kubectl get configmap -n kube-system cluster-autoscaler-status -o yaml
```

Aby dowiedzieć się więcej na temat tego, co jest rejestrowane w ramach automatycznego skalowania, zapoznaj się z często zadawanymi pytaniami w [projekcie GitHub Kubernetes/autoscalenia][kubernetes-faq].

## <a name="use-the-cluster-autoscaler-with-multiple-node-pools-enabled"></a>Używanie automatycznego skalowania klastra z włączonymi wieloma pulami węzłów

Automatyczne skalowanie klastra może być używane razem z włączonymi [wieloma pulami węzłów][aks-multiple-node-pools] . Postępuj zgodnie z tym dokumentem, aby dowiedzieć się, jak włączyć wiele pul węzłów i dodać dodatkowe pule węzłów do istniejącego klastra. W przypadku korzystania z obu funkcji jednocześnie należy włączyć automatyczne skalowanie klastra dla każdej puli poszczególnych węzłów w klastrze i przekazywać unikatowe reguły automatycznego skalowania do każdego z nich.

W poniższym poleceniu założono, że wykonano [instrukcje wstępne](#create-an-aks-cluster-and-enable-the-cluster-autoscaler) we wcześniejszej części tego dokumentu, a użytkownik chce zaktualizować maksymalną liczbę węzłów z zakresu od *3* do *5*. Użyj polecenia [AZ AKS nodepool Update][az-aks-nodepool-update] , aby zaktualizować ustawienia istniejącej puli węzłów.

```azurecli-interactive
az aks nodepool update \
  --resource-group myResourceGroup \
  --cluster-name myAKSCluster \
  --name nodepool1 \
  --update-cluster-autoscaler \
  --min-count 1 \
  --max-count 5
```

Automatyczne skalowanie klastra można wyłączyć za pomocą [AZ AKS nodepool Update][az-aks-nodepool-update] i przekazując `--disable-cluster-autoscaler` parametr.

```azurecli-interactive
az aks nodepool update \
  --resource-group myResourceGroup \
  --cluster-name myAKSCluster \
  --name nodepool1 \
  --disable-cluster-autoscaler
```

Jeśli chcesz ponownie włączyć automatyczne skalowanie klastra w istniejącym klastrze, możesz je ponownie włączyć za pomocą polecenia [AZ AKS nodepool Update][az-aks-nodepool-update] , określając parametr *--enable-Cluster-AutoScale*, *--min-Count*i *--Max-Count* .

## <a name="next-steps"></a>Następne kroki

W tym artykule pokazano, jak automatycznie skalować liczbę węzłów AKS. Możesz również użyć skalowania w poziomie w pionie, aby automatycznie dostosować liczbę zasobników z uruchomioną aplikacją. Aby zapoznać się z instrukcjami dotyczącymi używania automatycznego skalowania w poziomie, zobacz [skalowanie aplikacji w AKS][aks-scale-apps].

<!-- LINKS - internal -->
[aks-faq]: faq.md
[aks-faq-node-resource-group]: faq.md#can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-node-resource-group
[aks-multiple-node-pools]: use-multiple-node-pools.md
[aks-scale-apps]: tutorial-kubernetes-scale.md
[aks-support-policies]: support-policies.md
[aks-upgrade]: upgrade-cluster.md
[aks-view-master-logs]: ./view-master-logs.md#enable-resource-logs
[autoscaler-profile-properties]: #using-the-autoscaler-profile
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-update]: /cli/azure/aks#az-aks-update
[az-aks-scale]: /cli/azure/aks#az-aks-scale
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register

<!-- LINKS - external -->
[az-aks-update-preview]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview
[az-aks-nodepool-update]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview#enable-cluster-auto-scaler-for-a-node-pool
[autoscaler-scaledown]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-types-of-pods-can-prevent-ca-from-removing-a-node
[autoscaler-parameters]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-are-the-parameters-to-ca
[kubernetes-faq]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#ca-doesnt-work-but-it-used-to-work-yesterday-why
