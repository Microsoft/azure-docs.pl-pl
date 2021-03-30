---
title: Obsługa ponownych uruchomień węzłów systemu Linux za pomocą kured
titleSuffix: Azure Kubernetes Service
description: Dowiedz się, jak aktualizować węzły systemu Linux i automatycznie ponownie uruchamiać je za pomocą kured w usłudze Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 02/28/2019
ms.openlocfilehash: 35c9e76c234e4b09fbb090eda363506ee3e11130
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88164244"
---
# <a name="apply-security-and-kernel-updates-to-linux-nodes-in-azure-kubernetes-service-aks"></a>Stosowanie aktualizacji zabezpieczeń i jądra do węzłów systemu Linux w usłudze Azure Kubernetes Service (AKS)

W celu ochrony klastrów aktualizacje zabezpieczeń są automatycznie stosowane do węzłów systemu Linux w programie AKS. Te aktualizacje obejmują poprawki zabezpieczeń systemu operacyjnego lub aktualizacje jądra. Niektóre z tych aktualizacji wymagają ponownego uruchomienia węzła w celu ukończenia procesu. AKS nie uruchamia automatycznie ponownie tych węzłów systemu Linux w celu ukończenia procesu aktualizacji.

Proces zachowywania Aktualności węzłów systemu Windows Server jest nieco inny. Węzły systemu Windows Server nie otrzymują codziennych aktualizacji. Zamiast tego wykonywana jest aktualizacja AKS, która wdraża nowe węzły z najnowszym obrazem i poprawkami podstawowego serwera systemu Windows. W przypadku klastrów AKS korzystających z węzłów systemu Windows Server zobacz [uaktualnianie puli węzłów w AKS][nodepool-upgrade].

W tym artykule opisano sposób użycia [demona ponownego rozruchu kured (KUbernetes)][kured] w celu obejrzenia węzłów systemu Linux, które wymagają ponownego uruchomienia, a następnie automatycznie obsłużyć ponowne planowanie procesów uruchamiania i ponownego uruchamiania węzła.

> [!NOTE]
> `Kured` jest projektem "open source" przez Weaveworks. Pomoc techniczna dla tego projektu w programie AKS jest dostarczana na podstawie najlepszego wysiłku. Dodatkową pomoc techniczną można znaleźć w kanale czasu bez#weaveowego Wspólnoty.

## <a name="before-you-begin"></a>Zanim rozpoczniesz

W tym artykule przyjęto założenie, że masz istniejący klaster AKS. Jeśli potrzebujesz klastra AKS, zapoznaj się z przewodnikiem Szybki Start AKS [przy użyciu interfejsu wiersza polecenia platformy Azure][aks-quickstart-cli] lub [przy użyciu Azure Portal][aks-quickstart-portal].

Konieczne jest również zainstalowanie i skonfigurowanie interfejsu wiersza polecenia platformy Azure w wersji 2.0.59 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][install-azure-cli].

## <a name="understand-the-aks-node-update-experience"></a>Opis środowiska aktualizacji węzła AKS

W klastrze AKS węzły Kubernetes są uruchamiane jako maszyny wirtualne platformy Azure. Te maszyny wirtualne z systemem Linux używają obrazu Ubuntu z systemem operacyjnym skonfigurowanym do automatycznego sprawdzania dostępności aktualizacji w każdej nocy. Jeśli aktualizacje zabezpieczeń lub jądra są dostępne, zostaną automatycznie pobrane i zainstalowane.

![Proces aktualizacji i ponownego uruchamiania węzła AKS przy użyciu kured](media/node-updates-kured/node-reboot-process.png)

Niektóre aktualizacje zabezpieczeń, takie jak aktualizacje jądra, wymagają ponownego uruchomienia węzła w celu sfinalizowania procesu. Węzeł systemu Linux wymagający ponownego uruchomienia tworzy plik o nazwie */var/run/reboot-Required*. Ten proces ponownego uruchamiania nie odbywa się automatycznie.

Możesz użyć własnych przepływów pracy i procesów do obsługi ponownych uruchomień węzłów lub użyć, `kured` Aby zorganizować proces. W programie `kured` wdrożono [elementu daemonset][DaemonSet] , który działa pod względem każdego węzła systemu Linux w klastrze. Te zasobniki w elementu daemonset zaobserwują obecność pliku */var/run/reboot-Required* , a następnie inicjują proces ponownego uruchamiania węzłów.

### <a name="node-upgrades"></a>Uaktualnienia węzłów

Istnieje dodatkowy proces w AKS, który umożliwia *uaktualnienie* klastra. Uaktualnienie zazwyczaj jest przenoszone do nowszej wersji programu Kubernetes, a nie tylko w przypadku aktualizacji zabezpieczeń węzła. Uaktualnienie AKS wykonuje następujące akcje:

* Nowy węzeł zostanie wdrożony z najnowszymi aktualizacjami zabezpieczeń i zastosowanymi wersjami Kubernetes.
* Stary węzeł jest odizolowywane i opróżniany.
* Na nowym węźle są planowane nowe zasobniki.
* Stary węzeł zostanie usunięty.

Nie można pozostawać w tej samej wersji Kubernetes podczas zdarzenia uaktualniania. Należy określić nowszą wersję programu Kubernetes. Aby uaktualnić do najnowszej wersji programu Kubernetes, można [uaktualnić klaster AKS][aks-upgrade].

## <a name="deploy-kured-in-an-aks-cluster"></a>Wdrażanie kured w klastrze AKS

Aby wdrożyć `kured` elementu daemonset, zainstaluj Poniższy oficjalny wykres Kured Helm. Spowoduje to utworzenie roli i roli klastra, powiązań i konta usługi, a następnie wdrożenie elementu daemonset przy użyciu programu `kured` .

```console
# Add the Kured Helm repository
helm repo add kured https://weaveworks.github.io/kured

# Update your local Helm chart repository cache
helm repo update

# Create a dedicated namespace where you would like to deploy kured into
kubectl create namespace kured

# Install kured in that namespace with Helm 3 (only on Linux nodes, kured is not working on Windows nodes)
helm install kured kured/kured --namespace kured --set nodeSelector."beta\.kubernetes\.io/os"=linux
```

Możesz również skonfigurować dodatkowe parametry dla `kured` , takie jak integracja z Prometheus lub zapasem czasu. Aby uzyskać więcej informacji na temat dodatkowych parametrów konfiguracyjnych, zobacz [Wykres Kured Helm][kured-install].

## <a name="update-cluster-nodes"></a>Aktualizowanie węzłów klastra

Domyślnie węzły systemu Linux w programie AKS sprawdzają dostępność aktualizacji co wieczór. Jeśli nie chcesz czekać, możesz ręcznie wykonać aktualizację, aby sprawdzić, czy `kured` działa poprawnie. Najpierw postępuj zgodnie z instrukcjami protokołu [SSH do jednego z węzłów AKS][aks-ssh]. Po nawiązaniu połączenia SSH z węzłem systemu Linux Sprawdź, czy są dostępne aktualizacje i zastosuj je w następujący sposób:

```console
sudo apt-get update && sudo apt-get upgrade -y
```

Jeśli zastosowano aktualizacje, które wymagają ponownego uruchomienia węzła, plik jest zapisywana w */var/run/reboot-Required*. `Kured` sprawdza węzły, które domyślnie wymagają ponownego uruchomienia co 60 minut.

## <a name="monitor-and-review-reboot-process"></a>Monitorowanie i przeglądanie procesu ponownego uruchamiania

Gdy jedna z replik w elementu daemonset wykryła, że wymagane jest ponowne uruchomienie węzła, blokada jest umieszczana w węźle za pomocą interfejsu API Kubernetes. Ta blokada uniemożliwia zaplanowanie dodatkowych magazynów w węźle. Blokada wskazuje również, że tylko jeden węzeł należy uruchomić ponownie w danym momencie. Gdy węzeł odizolowywane off, uruchomione zasobniki są opróżniane z węzła, a węzeł zostanie ponownie uruchomiony.

Stan węzłów można monitorować za pomocą polecenia [polecenia kubectl Get nodes][kubectl-get-nodes] . Poniższe przykładowe dane wyjściowe przedstawiają węzeł o stanie *SchedulingDisabled* jako węzeł przygotowuje się do ponownego uruchomienia procesu:

```
NAME                       STATUS                     ROLES     AGE       VERSION
aks-nodepool1-28993262-0   Ready,SchedulingDisabled   agent     1h        v1.11.7
```

Po zakończeniu procesu aktualizacji można wyświetlić stan węzłów za pomocą polecenia [polecenia kubectl Pobierz węzły][kubectl-get-nodes] z `--output wide` parametrem. To dodatkowe wyjście pozwala zobaczyć różnicę w *wersji jądra* węzłów źródłowych, jak pokazano w poniższych przykładowych danych wyjściowych. Element *AKS-nodepool1-28993262-0* został zaktualizowany w poprzednim kroku i zawiera wersję jądra *4.15.0-1039-Azure*. Węzeł *AKS-nodepool1-28993262-1* , który nie został zaktualizowany, zawiera wersję jądra *4.15.0-1037-Azure*.

```
NAME                       STATUS    ROLES     AGE       VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE             KERNEL-VERSION      CONTAINER-RUNTIME
aks-nodepool1-28993262-0   Ready     agent     1h        v1.11.7   10.240.0.4    <none>        Ubuntu 16.04.6 LTS   4.15.0-1039-azure   docker://3.0.4
aks-nodepool1-28993262-1   Ready     agent     1h        v1.11.7   10.240.0.5    <none>        Ubuntu 16.04.6 LTS   4.15.0-1037-azure   docker://3.0.4
```

## <a name="next-steps"></a>Następne kroki

W tym artykule szczegółowo opisano sposób `kured` automatycznego ponownego uruchamiania węzłów systemu Linux w ramach procesu aktualizacji zabezpieczeń. Aby uaktualnić do najnowszej wersji programu Kubernetes, można [uaktualnić klaster AKS][aks-upgrade].

W przypadku klastrów AKS korzystających z węzłów systemu Windows Server zobacz [uaktualnianie puli węzłów w AKS][nodepool-upgrade].

<!-- LINKS - external -->
[kured]: https://github.com/weaveworks/kured
[kured-install]: https://github.com/weaveworks/kured/tree/master/charts/kured
[kubectl-get-nodes]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[DaemonSet]: concepts-clusters-workloads.md#statefulsets-and-daemonsets
[aks-ssh]: ssh.md
[aks-upgrade]: upgrade-cluster.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
