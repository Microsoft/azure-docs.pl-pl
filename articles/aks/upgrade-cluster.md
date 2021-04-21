---
title: Uaktualnianie klastra usługi Azure Kubernetes Service (AKS)
description: Dowiedz się, jak uaktualnić klaster usługi Azure Kubernetes Service (AKS) w celu uzyskania najnowszych funkcji i aktualizacji zabezpieczeń.
services: container-service
ms.topic: article
ms.date: 12/17/2020
ms.openlocfilehash: d6a5ed468541090d433dba732707a59841e6ff41
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107779619"
---
# <a name="upgrade-an-azure-kubernetes-service-aks-cluster"></a>Uaktualnianie klastra usługi Azure Kubernetes Service (AKS)

Część cyklu życia klastra usługi AKS obejmuje przeprowadzanie okresowych uaktualnień do najnowszej wersji rozwiązania Kubernetes. Ważne jest zastosowanie najnowszych wersji zabezpieczeń lub uaktualnienie w celu uzyskania najnowszych funkcji. W tym artykule pokazano, jak uaktualnić składniki główne lub pojedynczą domyślną pulę węzłów w klastrze usługi AKS.

W przypadku klastrów usługi AKS, które korzystają z wielu pul węzłów lub węzłów systemu Windows Server, zobacz [Uaktualnianie puli węzłów w u usługi AKS][nodepool-upgrade].

## <a name="before-you-begin"></a>Zanim rozpoczniesz

Ten artykuł wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.65 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli-install].

> [!WARNING]
> Uaktualnienie klastra usługi AKS wyzwala cordon i opróżnianie węzłów. Jeśli masz dostępny niski limit przydziału zasobów obliczeniowych, uaktualnienie może się nie powieść. Aby uzyskać więcej informacji, zobacz [Zwiększanie limitów przydziału](../azure-portal/supportability/resource-manager-core-quotas-request.md)

## <a name="check-for-available-aks-cluster-upgrades"></a>Sprawdzanie dostępnych uaktualnień klastra usługi AKS

Aby sprawdzić, które wydania kubernetes są dostępne dla klastra, użyj [polecenia az aks get-upgrades.][az-aks-get-upgrades] Poniższy przykład sprawdza dostępne uaktualnienia do *myAKSCluster* w *grupie myResourceGroup:*

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster --output table
```

> [!NOTE]
> Podczas uaktualniania obsługiwanego klastra usługi AKS nie można pominąć wersji pomocniczej kubernetes. Wszystkie uaktualnienia muszą być wykonywane sekwencyjnie według numeru wersji głównych. Na przykład uaktualnienia z wersji *1.14.x*  ->  *1.15.x* lub *1.15.x*  ->  *1.16.x* są dozwolone, jednak *1.14.x*  ->  *1.16.x* jest niedozwolone. 
> > Pomijanie wielu wersji można wykonać tylko podczas uaktualniania z nieobsługiwanej _wersji_ z powrotem do _obsługiwanej wersji._ Na przykład można ukończyć uaktualnienie z nieobsługiwanej wersji *1.10.x* > obsługiwanej wersji *1.15.x.*

Następujące przykładowe dane wyjściowe pokazują, że klaster można uaktualnić do wersji *1.19.1* i *1.19.3:*

```console
Name     ResourceGroup    MasterVersion    Upgrades
-------  ---------------  ---------------  --------------
default  myResourceGroup  1.18.10          1.19.1, 1.19.3
```

Jeśli uaktualnienie nie jest dostępne, zostanie wyświetlony komunikat:

```console
ERROR: Table output unavailable. Use the --query option to specify an appropriate query. Use --debug for more info.
```

## <a name="customize-node-surge-upgrade"></a>Dostosowywanie uaktualnienia skoków węzłów

> [!Important]
> Skoki liczby węzłów wymagają limitu przydziału subskrypcji dla żądanej maksymalnej liczby skoków dla każdej operacji uaktualniania. Na przykład klaster, który ma 5 pul węzłów, z których każda ma liczbę 4 węzłów, ma łącznie 20 węzłów. Jeśli każda pula węzłów ma maksymalną wartość skoków na poziomie 50%, do ukończenia uaktualnienia jest wymagany dodatkowy limit przydziału zasobów obliczeniowych i adresów IP 10 węzłów (2 węzły * 5 pul).
>
> Jeśli używasz Azure CNI, sprawdź, czy w podsieci są dostępne adresy [IP,](configure-azure-cni.md)aby spełnić wymagania dotyczące adresów IP Azure CNI .

Domyślnie usługę AKS konfiguruje uaktualnienia do operacji skoków z jednym dodatkowym węzłem. Wartość domyślna jednej dla maksymalnych ustawień skoków umożliwi u usługi AKS zminimalizowanie zakłóceń obciążenia przez utworzenie dodatkowego węzła przed odiszczeniami/opróżnieniami istniejących aplikacji w celu zastąpienia starszego węzła wersji. Maksymalną wartość skoków można dostosować dla puli węzłów, aby umożliwić różnicę między szybkością uaktualnienia a przerwami w uaktualnieniu. Zwiększając maksymalną wartość skoków, proces uaktualniania jest ukończony szybciej, ale ustawienie dużej wartości maksymalnego skoku może spowodować zakłócenia podczas procesu uaktualniania. 

Na przykład maksymalna wartość skoków na poziomie 100% zapewnia najszybszy możliwy proces uaktualniania (podwojenie liczby węzłów), ale również powoduje jednoczesne opróżnienie wszystkich węzłów w puli węzłów. Możesz użyć wyższej wartości, takiej jak ta, do środowisk testowych. W przypadku pul węzłów produkcyjnych zalecamy ustawienie max_surge na 33%.

AKS akceptuje zarówno wartości całkowite, jak i wartość procentową dla maksymalnych skoków. Liczba całkowita, taka jak "5", wskazuje pięć dodatkowych węzłów do skoków. Wartość "50%" wskazuje wartość skoków o połowę bieżącą liczbę węzłów w puli. Maksymalna wartość procentowa skoków może być co najmniej 1% i maksymalna 100%. Wartość procentowa jest zaokrąglana w górę do najbliższej liczby węzłów. Jeśli maksymalna wartość skoków jest niższa niż bieżąca liczba węzłów w czasie uaktualniania, bieżąca liczba węzłów jest używana dla maksymalnej wartości skoków.

Podczas uaktualniania maksymalna wartość skoków może być równa co najmniej 1, a maksymalna wartość jest równa liczbie węzłów w puli węzłów. Można ustawić większe wartości, ale maksymalna liczba węzłów używanych do maksymalnego skoku nie będzie większa niż liczba węzłów w puli w czasie uaktualniania.

> [!Important]
> Ustawienie maksymalnego skoku w puli węzłów jest trwałe.  Kolejne uaktualnienia kubernetes lub uaktualnienia wersji węzła będą używać tego ustawienia. W dowolnym momencie możesz zmienić maksymalną wartość skoków dla pul węzłów. W przypadku pul węzłów produkcyjnych zalecamy ustawienie maksymalnego skoku na 33%.

Użyj następujących poleceń, aby ustawić maksymalne wartości skoków dla nowych lub istniejących pul węzłów.

```azurecli-interactive
# Set max surge for a new node pool
az aks nodepool add -n mynodepool -g MyResourceGroup --cluster-name MyManagedCluster --max-surge 33%
```

```azurecli-interactive
# Update max surge for an existing node pool 
az aks nodepool update -n mynodepool -g MyResourceGroup --cluster-name MyManagedCluster --max-surge 5
```

## <a name="upgrade-an-aks-cluster"></a>Uaktualnianie klastra AKS

Aby uzyskać listę dostępnych wersji klastra usługi AKS, użyj polecenia [az aks upgrade,][az-aks-upgrade] aby uaktualnić klaster. Podczas procesu uaktualniania zestaw AKS: 
- dodaj nowy węzeł bufora (lub tyle węzłów, ile skonfigurowano przy maksymalnym skoku [)](#customize-node-surge-upgrade)do klastra, w których jest uruchomiona określona wersja usługi Kubernetes. 
- [cordon i opróżnienie][kubernetes-drain] jednego ze starych węzłów w celu zminimalizowania [][kubernetes-drain] zakłóceń w działaniu aplikacji (jeśli używasz maksymalnego skoku, spowoduje to odień i opróżnienie tylu węzłów w tym samym czasie, co określona liczba węzłów buforu). 
- Gdy stary węzeł zostanie w pełni opróżniony, zostanie ponownie przeprojektowany w celu otrzymania nowej wersji i stanie się węzłem buforu dla następującego węzła do uaktualnienia. 
- Ten proces jest powtarzany do momentu uaktualnienia wszystkich węzłów w klastrze. 
- Po zakończeniu procesu ostatni węzeł bufora zostanie usunięty, zachowując istniejącą liczbę węzłów agenta i równoważenie strefy.

```azurecli-interactive
az aks upgrade \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --kubernetes-version KUBERNETES_VERSION
```

Uaktualnienie klastra może potrwać kilka minut, w zależności od tego, ile węzłów masz.

> [!IMPORTANT]
> Upewnij się, że wszystkie pliki (PDB) zezwalają na co najmniej 1 replikę zasobnika na czas, w przeciwnym razie operacja `PodDisruptionBudgets` opróżniania/eksmisji nie powiedzie się.
> Jeśli operacja opróżniania zakończy się niepowodzeniem, operacja uaktualniania nie powiedzie się z projektem, aby zapewnić, że aplikacje nie zostaną zakłócone. Popraw, co spowodowało zatrzymanie operacji (nieprawidłowe pliki PDB, brak przydziału itp.) i spróbuj ponownie wykonać operację.

Aby potwierdzić, że uaktualnienie powiodło się, użyj [polecenia az aks show:][az-aks-show]

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --output table
```

Następujące przykładowe dane wyjściowe pokazują, że w klastrze działa *teraz 1.18.10:*

```json
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ----------------------------------------------
myAKSCluster  eastus      myResourceGroup  1.18.10              Succeeded            myakscluster-dns-379cbbb9.hcp.eastus.azmk8s.io
```

## <a name="set-auto-upgrade-channel"></a>Ustawianie kanału automatycznego uaktualniania

Oprócz ręcznego uaktualniania klastra można ustawić w klastrze kanał automatycznego uaktualniania. Dostępne są następujące kanały uaktualniania:

|Kanał| Akcja | Przykład
|---|---|---|
| `none`| wyłącza automatyczne uaktualnienia i utrzymuje klaster w bieżącej wersji usługi Kubernetes| Ustawienie domyślne, jeśli pozostawione bez zmian|
| `patch`| automatycznie uaktualnia klaster do najnowszej obsługiwanej wersji poprawek, gdy stanie się dostępny, przy zachowaniu tej samej wersji pomocniczej.| Jeśli na przykład klaster ma wersję *1.17.7* i *1.17.9,* *1.18.4,* *1.18.6* i *1.19.1,* klaster zostanie uaktualniony do *wersji 1.17.9*|
| `stable`| automatycznie uaktualnij klaster do najnowszej obsługiwanej wersji poprawek w wersji pomocniczej *N-1,* gdzie *N* to najnowsza obsługiwana wersja pomocnicza.| Jeśli na przykład klaster ma wersję *1.17.7* i *1.17.9,* *1.18.4,* *1.18.6* i *1.19.1,* klaster zostanie uaktualniony do *wersji 1.18.6.*
| `rapid`| automatycznie uaktualniać klaster do najnowszej obsługiwanej wersji poprawek w najnowszej obsługiwanej wersji pomocniczej.| W przypadkach, gdy klaster jest w wersji rozwiązania Kubernetes w wersji *pomocniczej N-2,* gdzie *N* jest najnowszą obsługiwaną wersją pomocniczą, klaster najpierw uaktualnia do najnowszej obsługiwanej wersji poprawki *N-1* pomocniczej. Jeśli na przykład klaster ma wersję *1.17.7* i *1.17.9,* *1.18.4,* *1.18.6* i *1.19.1,* klaster najpierw zostanie uaktualniony do *wersji 1.18.6,* a następnie uaktualniony do *wersji 1.19.1.*

> [!NOTE]
> Automatyczne uaktualnianie klastra jest aktualizowane tylko do wersji GA usługi Kubernetes i nie będzie aktualizowane do wersji zapoznawczych.

Automatyczne uaktualnianie klastra jest wykonywane w taki sam sposób, jak w przypadku ręcznego uaktualniania klastra. Aby uzyskać więcej informacji, zobacz [Uaktualnianie klastra usługi AKS.][upgrade-cluster]

Funkcja automatycznego uaktualniania klastra dla klastrów usługi AKS jest funkcją w wersji zapoznawczej.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

Zarejestruj `AutoUpgradePreview` flagę funkcji za pomocą [polecenia az feature register,][az-feature-register] jak pokazano w poniższym przykładzie:

```azurecli-interactive
az feature register --namespace Microsoft.ContainerService -n AutoUpgradePreview
```

Wyświetlanie stanu Zarejestrowane trwa kilka *minut.* Sprawdź stan rejestracji za pomocą [polecenia az feature list:][az-feature-list]

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AutoUpgradePreview')].{Name:name,State:properties.state}"
```

Gdy wszystko będzie gotowe, odśwież rejestrację dostawcy *zasobów Microsoft.ContainerService* za pomocą [polecenia az provider register:][az-provider-register]

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

Aby ustawić kanał automatycznego uaktualniania podczas tworzenia klastra, użyj parametru *auto-upgrade-channel,* podobnie jak w poniższym przykładzie.

```azurecli-interactive
az aks create --resource-group myResourceGroup --name myAKSCluster --auto-upgrade-channel stable --generate-ssh-keys
```

Aby ustawić kanał automatycznego uaktualniania w istniejącym klastrze, zaktualizuj parametr *auto-upgrade-channel,* podobnie jak w poniższym przykładzie.

```azurecli-interactive
az aks update --resource-group myResourceGroup --name myAKSCluster --auto-upgrade-channel stable
```

## <a name="next-steps"></a>Następne kroki

W tym artykule popisano, jak uaktualnić istniejący klaster usługi AKS. Aby dowiedzieć się więcej na temat wdrażania klastrów usługi AKS i zarządzania nimi, zobacz zestaw samouczków.

> [!div class="nextstepaction"]
> [Samouczki dotyczące AKS][aks-tutorial-prepare-app]

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-upgrades]: /cli/azure/aks#az_aks_get_upgrades
[az-aks-upgrade]: /cli/azure/aks#az_aks_upgrade
[az-aks-show]: /cli/azure/aks#az_aks_show
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-provider-register]: /cli/azure/provider#az_provider_register
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[upgrade-cluster]:  #upgrade-an-aks-cluster
