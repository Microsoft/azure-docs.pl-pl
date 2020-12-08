---
title: Instalowanie istniejących aplikacji za pomocą programu Helm w AKS
description: Dowiedz się, jak wdrażać kontenery w klastrze usługi Azure Kubernetes Service (AKS) za pomocą narzędzia do pakowania Helm
services: container-service
author: zr-msft
ms.topic: article
ms.date: 12/07/2020
ms.author: zarhoads
ms.openlocfilehash: f12dffe0b538738a8f6dd00cd3d87d44da828f21
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/07/2020
ms.locfileid: "96779171"
---
# <a name="install-existing-applications-with-helm-in-azure-kubernetes-service-aks"></a>Instalowanie istniejących aplikacji przy użyciu usługi Helm w usłudze Azure Kubernetes Service (AKS)

[Helm][helm] to narzędzie do tworzenia pakietów typu "open source", które ułatwia Instalowanie i zarządzanie cyklem życia aplikacji Kubernetes. Podobnie jak w przypadku menedżerów pakietów systemu Linux, takich jak *apt* i *yum*, Helm służy do zarządzania wykresami Kubernetes, które są pakietami wstępnie skonfigurowanych zasobów Kubernetes.

W tym artykule opisano sposób konfigurowania i używania Helm w klastrze Kubernetes w systemie AKS.

## <a name="before-you-begin"></a>Przed rozpoczęciem

W tym artykule przyjęto założenie, że masz istniejący klaster AKS. Jeśli potrzebujesz klastra AKS, zapoznaj się z przewodnikiem Szybki Start AKS [przy użyciu interfejsu wiersza polecenia platformy Azure][aks-quickstart-cli] lub [przy użyciu Azure Portal][aks-quickstart-portal].

Wymagany jest również interfejs wiersza polecenia Helm, który jest klientem uruchomionym w systemie deweloperskim. Pozwala ona uruchamiać, zatrzymywać i zarządzać aplikacjami za pomocą Helm. W przypadku korzystania z Azure Cloud Shell interfejs wiersza polecenia Helm jest już zainstalowany. Aby uzyskać instrukcje dotyczące instalacji na lokalnej platformie, zobacz [Installing Helm][helm-install].

> [!IMPORTANT]
> Helm jest przeznaczony do uruchamiania w węzłach systemu Linux. Jeśli w klastrze znajdują się węzły systemu Windows Server, musisz upewnić się, że Helm są zaplanowane do uruchomienia tylko na węzłach z systemem Linux. Należy również upewnić się, że wszystkie zainstalowane wykresy Helm są również zaplanowane do uruchomienia w prawidłowych węzłach. Polecenia w tym artykule używają [selektorów węzłów] [k8s-Node-Selector], aby upewnić się, że zasobniki są zaplanowane do poprawnych węzłów, ale nie wszystkie wykresy Helm mogą uwidocznić selektor węzła. Możesz również rozważyć użycie innych opcji w klastrze, [takich jak][taints]przypisania.

## <a name="verify-your-version-of-helm"></a>Weryfikowanie wersji programu Helm

Użyj `helm version` polecenia, aby sprawdzić, czy zainstalowano Helm 3:

```console
helm version
```

W poniższym przykładzie przedstawiono zainstalowaną Helm wersję 3.0.0:

```console
$ helm version

version.BuildInfo{Version:"v3.0.0", GitCommit:"e29ce2a54e96cd02ccfce88bee4f58bb6e2a28b6", GitTreeState:"clean", GoVersion:"go1.13.4"}
```

## <a name="install-an-application-with-helm-v3"></a>Instalowanie aplikacji z Helm v3

### <a name="add-helm-repositories"></a>Dodawanie repozytoriów Helm

Użyj polecenia [Helm repozytorium][helm-repo-add] , aby dodać repozytorium transferów przychodzących *-Nginx* .

```console
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
```

### <a name="find-helm-charts"></a>Znajdź wykresy Helm

Wykresy Helm są używane do wdrażania aplikacji w klastrze Kubernetes. Aby wyszukać wstępnie utworzone wykresy Helm, użyj polecenia [wyszukiwania Helm][helm-search] :

```console
helm search repo ingress-nginx
```

Następujące wąskie przykładowe dane wyjściowe pokazują niektóre wykresy Helm dostępne do użycia:

```console
$ helm search repo ingress-nginx

NAME                            CHART VERSION   APP VERSION     DESCRIPTION                                       
ingress-nginx/ingress-nginx     2.12.0          0.34.1          Ingress controller for Kubernetes using NGINX a...
```

Aby zaktualizować listę wykresów, użyj polecenia [Helm repozytorium aktualizacji][helm-repo-update] .

```console
helm repo update
```

Poniższy przykład przedstawia pomyślne aktualizowanie repozytorium:

```console
$ helm repo update

Hang tight while we grab the latest from your chart repositories...
...Successfully got an update from the "ingress-nginx" chart repository
Update Complete. ⎈ Happy Helming!⎈
```

### <a name="run-helm-charts"></a>Uruchom wykresy Helm

Aby zainstalować wykresy z programem Helm, użyj polecenia [Install Helm][helm-install-command] i określ nazwę wydania oraz nazwę wykresu do zainstalowania. Aby zapoznać się z tematem Instalowanie wykresu Helm w działaniu, Zainstalujmy podstawowe wdrożenie Nginx przy użyciu wykresu Helm.

```console
helm install my-nginx-ingress ingress-nginx/ingress-nginx \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux
```

Następujące wąskie przykładowe dane wyjściowe pokazują stan wdrożenia zasobów Kubernetes utworzonych przez wykres Helm:

```console
$ helm install my-nginx-ingress ingress-nginx/ingress-nginx \
>     --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
>     --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux

NAME: my-nginx-ingress
LAST DEPLOYED: Fri Nov 22 10:08:06 2019
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
The nginx-ingress controller has been installed.
It may take a few minutes for the LoadBalancer IP to be available.
You can watch the status by running 'kubectl --namespace default get services -o wide -w my-nginx-ingress-ingress-nginx-controller'
...
```

Użyj `kubectl get services` polecenia, aby uzyskać *zewnętrzny adres IP* usługi.

```console
kubectl --namespace default get services -o wide -w my-nginx-ingress-ingress-nginx-controller
```

Na przykład poniższe polecenie pokazuje *zewnętrzny adres IP* dla usługi *My-Nginx-* transferal-Nginx-Controller:

```console
$ kubectl --namespace default get services -o wide -w my-nginx-ingress-ingress-nginx-controller

NAME                                        TYPE           CLUSTER-IP   EXTERNAL-IP      PORT(S)                      AGE   SELECTOR
my-nginx-ingress-ingress-nginx-controller   LoadBalancer   10.0.2.237   <EXTERNAL-IP>    80:31380/TCP,443:32239/TCP   72s   app.kubernetes.io/component=controller,app.kubernetes.io/instance=my-nginx-ingress,app.kubernetes.io/name=ingress-nginx
```

### <a name="list-releases"></a>Listy wydań

Aby wyświetlić listę wersji zainstalowanych w klastrze, użyj `helm list` polecenia.

```console
helm list
```

W poniższym przykładzie przedstawiono wydanie *My-Nginx-Ingress* wdrożone w poprzednim kroku:

```console
$ helm list

NAME                NAMESPACE   REVISION    UPDATED                                 STATUS      CHART                   APP VERSION
my-nginx-ingress    default     1           2019-11-22 10:08:06.048477 -0600 CST    deployed    nginx-ingress-1.25.0    0.26.1 
```

### <a name="clean-up-resources"></a>Czyszczenie zasobów

Po wdrożeniu wykresu Helm są tworzone różne zasoby Kubernetes. Do tych zasobów należą między innymi: zasoby, wdrożenia i usługi. Aby wyczyścić te zasoby, użyj polecenia [Helm Uninstall][helm-cleanup] i określ nazwę wydania, jak to zostało znalezione w poprzednim `helm list` poleceniu.

```console
helm uninstall my-nginx-ingress
```

W poniższym przykładzie pokazano wydanie o nazwie *My-Nginx-* Transfered zostało odinstalowane:

```console
$ helm uninstall my-nginx-ingress

release "my-nginx-ingress" uninstalled
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat zarządzania wdrożeniami aplikacji Kubernetes z Helm, zobacz dokumentację Helm.

> [!div class="nextstepaction"]
> [Dokumentacja programu Helm][helm-documentation]

<!-- LINKS - external -->
[helm]: https://github.com/kubernetes/helm/
[helm-cleanup]: https://helm.sh/docs/intro/using_helm/#helm-uninstall-uninstalling-a-release
[helm-documentation]: https://helm.sh/docs/
[helm-install]: https://helm.sh/docs/intro/install/
[helm-install-command]: https://helm.sh/docs/intro/using_helm/#helm-install-installing-a-package
[helm-repo-add]: https://helm.sh/docs/intro/quickstart/#initialize-a-helm-chart-repository
[helm-search]: https://helm.sh/docs/intro/using_helm/#helm-search-finding-charts
[helm-repo-update]: https://helm.sh/docs/intro/using_helm/#helm-repo-working-with-repositories
            
<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[taints]: operator-best-practices-advanced-scheduler.md
