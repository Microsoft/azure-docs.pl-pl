---
title: Użyj skalowania automatycznego klastra w usłudze Azure Kubernetes Service (AKS)
description: Dowiedz się, jak używać skalowania automatycznego klastra do automatycznego skalowania klastra w celu spełnienia wymagań aplikacji w klastrze usługi Azure Kubernetes (AKS).
services: container-service
ms.topic: article
ms.date: 07/18/2019
ms.openlocfilehash: 2baa64779713d0bac063e1d2c06107ba2ab291fb
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617535"
---
# <a name="automatically-scale-a-cluster-to-meet-application-demands-on-azure-kubernetes-service-aks"></a>Automatyczne skalowanie klastra w celu spełnienia wymagań aplikacji w usłudze Azure Kubernetes Service (AKS)

Aby nadążyć za wymaganiami aplikacji w usłudze Azure Kubernetes Service (AKS), może być konieczne dostosowanie liczby węzłów uruchamianych obciążeń. Składnik skalowania automatycznego klastra może obserwować zasobników w klastrze, których nie można zaplanować z powodu ograniczeń zasobów. Po wykryciu problemów liczba węzłów w puli węzłów jest zwiększana w celu zaspokojenia zapotrzebowania aplikacji. Węzły są również regularnie sprawdzane pod kątem braku uruchomionych zasobników, a liczba węzłów następnie zmniejsza się w razie potrzeby. Ta możliwość automatycznego skalowania w górę lub w dół liczby węzłów w klastrze AKS umożliwia uruchamianie wydajnego, ekonomicznego klastra.

W tym artykule pokazano, jak włączyć skalowanie automatyczne klastra i zarządzać nim w klastrze AKS.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Ten artykuł wymaga, aby użytkownik był uruchomiony w wersji interfejsu wiersza polecenia platformy Azure w wersji 2.0.76 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli-install].

## <a name="limitations"></a>Ograniczenia

Podczas tworzenia klastrów AKS korzystających z skalowania automatycznego klastra i zarządzania nimi obowiązują następujące ograniczenia:

* Nie można użyć dodatku routingu aplikacji HTTP.

## <a name="about-the-cluster-autoscaler"></a>Skalowanie automatyczne klastra – informacje

Aby dostosować się do zmieniających się wymagań aplikacji, takich jak między dzieńem roboczym a wieczorem lub w weekend, klastry często potrzebują sposobu automatycznego skalowania. Klastry AKS można skalować na jeden z dwóch sposobów:

* Skalowanie **automatyczne klastra** jest monitorowane dla zasobników, których nie można zaplanować w węzłach z powodu ograniczeń zasobów. Następnie klaster automatycznie zwiększa liczbę węzłów.
* Skalowanie **zasobu poziomego** używa serwera metryk w klastrze Kubernetes do monitorowania zapotrzebowania na zasoby zasobów zasobników. Jeśli aplikacja potrzebuje więcej zasobów, liczba zasobników jest automatycznie zwiększana w celu zaspokojenia popytu.

![Automatyczny skalowanier klastra i skalowanie automatyczne zasobników poziomych często współpracują ze sobą w celu obsługi wymaganych wymagań aplikacji](media/autoscaler/cluster-autoscaler.png)

Zarówno skalowanie automatyczne zasobników w poziomie i skalowanie automatyczne klastra można również zmniejszyć liczbę zasobników i węzłów w razie potrzeby. Skalowanie automatyczne klastra zmniejsza liczbę węzłów, gdy nie było niewykorzystanej pojemności przez pewien czas. Zasobniki w węźle do usunięcia przez skalowanie automatyczne klastra są bezpiecznie zaplanowane w innym miejscu w klastrze. Skalowanie automatyczne klastra może nie być możliwe do skalowania w dół, jeśli zasobników nie można przenieść, na przykład w następujących sytuacjach:

* Zasobnik jest tworzony bezpośrednio i nie jest wspierany przez obiekt kontrolera, taki jak zestaw wdrożenia lub repliki.
* Budżet na zakłócenia zasobnika (PDB) jest zbyt restrykcyjny i nie pozwala na spadek liczby zasobników poniżej pewnego progu.
* Zasobnik używa selektorów węzłów lub anty-koligacji, które nie mogą być honorowane, jeśli zaplanowano w innym węźle.

Aby uzyskać więcej informacji o tym, jak skalowanie automatyczne klastra może nie być możliwe skalowania w dół, zobacz [Jakie typy zasobników mogą uniemożliwić automatycznemu skalowaniu klastra usunięcie węzła?][autoscaler-scaledown]

Skalowanie automatyczne klastra używa parametrów uruchamiania dla rzeczy, takich jak interwały czasowe między zdarzeniami skali i progami zasobów. Te parametry są zdefiniowane przez platformę Azure i nie są obecnie udostępniane, aby dostosować. Aby uzyskać więcej informacji na temat parametrów używanych przez program cluster autoscaler, zobacz [Jakie są parametry skalowania automatycznego klastra?][autoscaler-parameters].

Skalowanie automatyczne klastra i zasobników poziomych mogą współpracować ze sobą i często są wdrażane w klastrze. Po połączeniu skalowanie zasobników poziomych koncentruje się na uruchamianiu liczby zasobników wymaganych do zaspokojenia zapotrzebowania aplikacji. Skalowanie automatyczne klastra koncentruje się na uruchamianiu liczby węzłów wymaganych do obsługi zaplanowanych zasobników.

> [!NOTE]
> Ręczne skalowanie jest wyłączone podczas korzystania z skalowania automatycznego klastra. Pozwól skalowaniu automatycznemu klastra określić wymaganą liczbę węzłów. Jeśli chcesz ręcznie skalować klaster, [wyłącz skalowanie automatyczne klastra](#disable-the-cluster-autoscaler).

## <a name="create-an-aks-cluster-and-enable-the-cluster-autoscaler"></a>Tworzenie klastra AKS i włączanie skalowania automatycznego klastra

Jeśli chcesz utworzyć klaster AKS, użyj polecenia [az aks create.][az-aks-create] Aby włączyć i skonfigurować skalowanie automatyczne klastra w puli węzłów dla klastra, użyj parametru *--enable-cluster-autoscaler* i określ węzeł *--min-count* i *--max-count*.

> [!IMPORTANT]
> Skalowanie automatyczne klastra jest składnikiem Kubernetes. Mimo że klaster AKS używa zestawu skalowania maszyny wirtualnej dla węzłów, nie należy ręcznie włączać ani edytować ustawień skalowania ustawionego automatycznie w witrynie Azure portal lub przy użyciu interfejsu wiersza polecenia platformy Azure. Pozwól skalowaniu automatycznemu klastra kubernetes zarządzać wymaganymi ustawieniami skali. Aby uzyskać więcej informacji, zobacz [Czy mogę zmodyfikować zasoby usługi AKS w grupie zasobów węzła?](faq.md#can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-node-resource-group)

Poniższy przykład tworzy klaster AKS z puli jednego węzła wspierane przez zestaw skalowania maszyny wirtualnej. Umożliwia również skalowanie automatyczne klastra w puli węzłów dla klastra i ustawia co najmniej *1* i maksymalnie *3* węzły:

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

Utworzenie klastra i skonfigurowanie ustawień skalowania automatycznego klastra zajmuje kilka minut.

## <a name="change-the-cluster-autoscaler-settings"></a>Zmienianie ustawień skalowania automatycznego klastra

> [!IMPORTANT]
> Jeśli w klastrze usługi AKS jest wiele pul węzłów, przejdź do [sekcji skalowanie automatyczne z wieloma pulami agentów](#use-the-cluster-autoscaler-with-multiple-node-pools-enabled). Klastry z wieloma pulami `az aks nodepool` agentów wymagają użycia zestawu poleceń, `az aks`aby zmienić właściwości określonej puli węzłów zamiast .

W poprzednim kroku, aby utworzyć klaster AKS lub zaktualizować istniejącą pulę węzłów, minimalna liczba węzłów skalowania automatycznego klastra została ustawiona na *1,* a maksymalna liczba węzłów została ustawiona na *3*. W miarę jak aplikacja wymaga zmiany, może być konieczne dostosowanie liczby węzłów skalowania automatycznego klastra.

Aby zmienić liczbę węzłów, użyj polecenia [az aks update.][az-aks-update]

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --update-cluster-autoscaler \
  --min-count 1 \
  --max-count 5
```

Powyższy przykład aktualizuje skalowanie automatyczne klastra w puli pojedynczego węzła w *myAKSCluster* do co najmniej *1* i maksymalnie *5* węzłów.

> [!NOTE]
> Nie można ustawić wyższej minimalnej liczby węzłów niż jest obecnie ustawiona dla puli węzłów. Na przykład, jeśli masz obecnie licznik minut ustawiony na *1,* nie można zaktualizować licznika minut do *3*.

Monitoruj wydajność aplikacji i usług i dostosowuj liczbę węzłów skalowania automatycznego klastra, aby dopasować się do wymaganej wydajności.

## <a name="using-the-autoscaler-profile"></a>Korzystanie z profilu skalowania automatycznego

Można również skonfigurować bardziej szczegółowe szczegóły skalowania automatycznego klastra, zmieniając wartości domyślne w profilu skalowania automatycznego w całym klastrze. Na przykład zdarzenie skalowania w dół dzieje się po węzły są niedostateczone po 10 minutach. Jeśli masz obciążeń, które były uruchomiony co 15 minut, można zmienić profil skalowania automatycznego do skalowania w dół w obszarze wykorzystanych węzłów po 15 lub 20 minut. Po włączeniu skalowania automatycznego klastra używany jest profil domyślny, chyba że zostaną określone inne ustawienia. Profil skalowania automatycznego klastra ma następujące ustawienia, które można zaktualizować:

| Ustawienie                          | Opis                                                                              | Wartość domyślna |
|----------------------------------|------------------------------------------------------------------------------------------|---------------|
| interwał skanowania                    | Jak często klaster jest ponownie wyceny dla skalowania w górę lub w dół                                    | 10 sekund    |
| skalowanie w dół-opóźnienie po dodaniu       | Jak długo po skalowaniu w górę, że skalowanie w dół oceny wznawia                               | 10 minut    |
| skalowanie w dół-opóźnienie po usunięciu    | Jak długo po wznowieniu oceny skalowania w dół,                          | interwał skanowania |
| scale-down-delay-after-failure   | Jak długo po awarii skalowania w dół, że skalowanie w dół oceny wznawia                     | 3 minuty     |
| skalowanie w dół-niepotrzebny czas         | Jak długo węzeł powinien być niepotrzebny, zanim kwalifikuje się do skalowania w dół                  | 10 minut    |
| skalowanie w dół-niezrealiza czas          | Jak długo nieuleczony węzeł powinien być niepotrzebny, zanim kwalifikuje się do skalowania w dół         | 20 minut    |
| próg wykorzystania w dół | Poziom wykorzystania węzła, zdefiniowany jako suma żądanych zasobów podzielonych przez pojemność, poniżej którego węzeł można uwzględnić w skalowaniu w dół | 0,5 |
| max-wdzięku-zakończenia-s     | Maksymalna liczba sekund automatycznego skalowania klastra czeka na zakończenie zasobnika podczas próby skalowania w dół węzła. | 600 sekund   |
| grupy węzłów równowagi | Wykrywanie podobnych pul węzłów i równoważenie liczby węzłów między nimi | false |

> [!IMPORTANT]
> Profil skalowania automatycznego klastra dotyczy wszystkich pul węzłów korzystających z skalowania automatycznego klastra. Nie można ustawić profilu skalowania automatycznego dla puli węzłów.

### <a name="install-aks-preview-cli-extension"></a>Instalowanie rozszerzenia interfejsu wiersza polecenia aks-preview

Aby ustawić profil ustawień skalowania automatycznego klastra, potrzebujesz rozszerzenia interfejsu wiersza polecenia *aks-preview* w wersji 0.4.30 lub nowszej. Zainstaluj rozszerzenie interfejsu wiersza polecenia interfejsu wiersza polecenia platformy Azure *aks-preview* przy użyciu polecenia [dodawania rozszerzenia az,][az-extension-add] a następnie sprawdź dostępność dostępnych aktualizacji za pomocą polecenia [aktualizacji rozszerzenia az:][az-extension-update]

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="set-the-cluster-autoscaler-profile-on-an-existing-aks-cluster"></a>Ustawianie profilu skalowania automatycznego klastra w istniejącym klastrze AKS

Użyj polecenia [az aks update][az-aks-update] z parametrem *profilu skalowania automatycznego skalowania klastra,* aby ustawić profil skalowania automatycznego klastra w klastrze. W poniższym przykładzie skonfigurowano ustawienie interwału skanowania jako 30s w profilu.

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --cluster-autoscaler-profile scan-interval=30s
```

Po włączeniu skalowania automatycznego klastra w pulach węzłów w klastrze klastry te będą również używać profilu skalowania automatycznego klastra. Przykład:

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
> Po ustawieniu profilu skalowania automatycznego klastra wszystkie istniejące pule węzłów z włączonym skalowaniem automatycznym klastra natychmiast zaczną używać profilu.

### <a name="set-the-cluster-autoscaler-profile-when-creating-an-aks-cluster"></a>Ustawianie profilu skalowania automatycznego klastra podczas tworzenia klastra AKS

Podczas tworzenia klastra można również użyć *parametru profilu automatycznego skalowania* klastra. Przykład:

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

Powyższe polecenie tworzy klaster AKS i definiuje interwał skanowania jako 30 sekund dla profilu skalowania automatycznego w całym klastrze. Polecenie włącza również skalowanie automatyczne klastra w puli węzłów początkowych, ustawia minimalną liczbę węzłów na 1, a maksymalną liczbę węzłów na 3.

### <a name="reset-cluster-autoscaler-profile-to-default-values"></a>Resetowanie profilu skalowania automatycznego klastra do wartości domyślnych

Użyj polecenia [az aks update,][az-aks-update] aby zresetować profil skalowania automatycznego klastra w klastrze.

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --cluster-autoscaler-profile ""
```

## <a name="disable-the-cluster-autoscaler"></a>Wyłączanie skalowania automatycznego klastra

Jeśli nie chcesz już używać skalowania automatycznego klastra, możesz go wyłączyć za pomocą polecenia [az aks update,][az-aks-update] określając parametr *--disable-cluster-autoscaler.* Węzły nie są usuwane, gdy skalowanie automatyczne klastra jest wyłączone.

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --disable-cluster-autoscaler
```

Po wyłączeniu skalowania automatycznego klastra można ręcznie skalować klaster za pomocą polecenia [az aks scale.][az-aks-scale] Jeśli używasz skalowania automatycznego zasobnika poziomego, ta funkcja będzie nadal działać z wyłączonym skalowaniem automatycznym klastra, ale zasobniki mogą nie być zaplanowane, jeśli wszystkie zasoby węzła są używane.

## <a name="re-enable-a-disabled-cluster-autoscaler"></a>Ponowne włączanie automatycznego skalowania klastra

Jeśli chcesz ponownie włączyć skalowanie automatyczne klastra w istniejącym klastrze, możesz ponownie włączyć go za pomocą polecenia [az aks update,][az-aks-update] określając parametry *--enable-cluster-autoscaler*, *--min-count*i *--max-count.*

## <a name="retrieve-cluster-autoscaler-logs-and-status"></a>Pobieranie dzienników i stanu skalowania automatycznego klastra

Aby zdiagnozować i debugować zdarzenia skalowania automatycznego, dzienniki i stan można pobrać z dodatku skalowania automatycznego.

Usługa AKS zarządza skalowaniem automatycznym klastra w Twoim imieniu i uruchamia go na płaszczyźnie zarządzanej. W rezultacie należy skonfigurować dzienniki węzłów wzorcowych, aby były wyświetlane.

Aby skonfigurować dzienniki, które mają być wypychane z skalowania automatycznego klastra do usługi Log Analytics, wykonaj następujące kroki.

1. Skonfiguruj regułę dla dzienników diagnostycznych do wypychania dzienników skalowania automatycznego klastra do usługi Log Analytics. [Instrukcje są szczegółowe tutaj,](https://docs.microsoft.com/azure/aks/view-master-logs#enable-diagnostics-logs)upewnij `cluster-autoscaler` się, że zaznaczysz pole wyboru opcji "Dzienniki".
1. Kliknij sekcję "Dzienniki" w klastrze za pośrednictwem witryny Azure portal.
1. Wprowadź następującą przykładowe zapytanie do usługi Log Analytics:

```
AzureDiagnostics
| where Category == "cluster-autoscaler"
```

Powinny być widoczne dzienniki podobne do poniższego przykładu, tak długo, jak istnieją dzienniki do pobrania.

![Dzienniki usługi Log Analytics](media/autoscaler/autoscaler-logs.png)

Skalowanie automatyczne klastra będzie również zapisywać stan `cluster-autoscaler-status`kondycji do configmap o nazwie . Aby pobrać te dzienniki, `kubectl` wykonaj następujące polecenie. Stan kondycji zostanie zgłoszony dla każdej puli węzłów skonfigurowanych za pomocą skalowania automatycznego klastra.

```
kubectl get configmap -n kube-system cluster-autoscaler-status -o yaml
```

Aby dowiedzieć się więcej o tym, co jest rejestrowane z skalowania automatycznego, przeczytaj często zadawane pytania w [projekcie GitHub Kubernetes/autoscaler](https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#ca-doesnt-work-but-it-used-to-work-yesterday-why).

## <a name="use-the-cluster-autoscaler-with-multiple-node-pools-enabled"></a>Używanie skalowania automatycznego klastra z włączoną pulą wielu węzłów

Skalowania automatycznego klastra mogą być używane razem z [wielu pul węzłów](use-multiple-node-pools.md) włączone. Postępuj zgodnie z tym dokumentem, aby dowiedzieć się, jak włączyć wiele pul węzłów i dodać dodatkowe pule węzłów do istniejącego klastra. Korzystając z obu funkcji razem, można włączyć skalowanie automatyczne klastra na każdej puli poszczególnych węzłów w klastrze i można przekazać unikatowe reguły skalowania automatycznego do każdego.

Poniższe polecenie zakłada, że postępowałeś zgodnie z [początkowymi instrukcjami](#create-an-aks-cluster-and-enable-the-cluster-autoscaler) wcześniej w tym dokumencie i chcesz zaktualizować maksymalną liczbę istniejącej puli węzłów z *3* do *5*. Użyj polecenia [aktualizacji az aks nodepool,][az-aks-nodepool-update] aby zaktualizować ustawienia istniejącej puli węzłów.

```azurecli-interactive
az aks nodepool update \
  --resource-group myResourceGroup \
  --cluster-name myAKSCluster \
  --name nodepool1 \
  --update-cluster-autoscaler \
  --min-count 1 \
  --max-count 5
```

Skalowanie automatyczne klastra można wyłączyć za pomocą [aktualizacji az aks nodepool][az-aks-nodepool-update] i przekazania parametru. `--disable-cluster-autoscaler`

```azurecli-interactive
az aks nodepool update \
  --resource-group myResourceGroup \
  --cluster-name myAKSCluster \
  --name nodepool1 \
  --disable-cluster-autoscaler
```

Aby ponownie włączyć skalowanie automatyczne klastra w istniejącym klastrze, można go ponownie włączyć za pomocą polecenia [az aks nodepool update,][az-aks-nodepool-update] określając parametry *--enable-cluster-autoscaler*, *--min-count*i *--max-count.*

## <a name="next-steps"></a>Następne kroki

W tym artykule pokazano, jak automatycznie skalować liczbę węzłów AKS. Można również użyć skalowania automatycznego zasobnika poziomego, aby automatycznie dostosować liczbę zasobników uruchamianych aplikacji. Aby uzyskać instrukcje dotyczące używania skalowania automatycznego zasobnika poziomego, zobacz [Skalowanie aplikacji w programie AKS][aks-scale-apps].

<!-- LINKS - internal -->
[aks-faq]: faq.md
[aks-scale-apps]: tutorial-kubernetes-scale.md
[aks-support-policies]: support-policies.md
[aks-upgrade]: upgrade-cluster.md
[autoscaler-profile-properties]: #using-the-autoscaler-profile
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-scale]: /cli/azure/aks#az-aks-scale
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register

<!-- LINKS - external -->
[az-aks-update]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview
[az-aks-nodepool-update]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview#enable-cluster-auto-scaler-for-a-node-pool
[autoscaler-scaledown]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-types-of-pods-can-prevent-ca-from-removing-a-node
[autoscaler-parameters]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-are-the-parameters-to-ca
