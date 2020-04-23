---
title: Instalowanie istniejących aplikacji z helmem w AKS
description: Dowiedz się, jak używać narzędzia do pakowania helm do wdrażania kontenerów w klastrze usługi Azure Kubernetes (AKS)
services: container-service
author: zr-msft
ms.topic: article
ms.date: 11/22/2019
ms.author: zarhoads
ms.openlocfilehash: e46bed5fc9fd83a907f8c9e716317a54548c58cc
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81870247"
---
# <a name="install-existing-applications-with-helm-in-azure-kubernetes-service-aks"></a>Instalowanie istniejących aplikacji z usługą Helm w usłudze Azure Kubernetes Service (AKS)

[Helm][helm] to narzędzie do pakowania typu open source, które pomaga zainstalować cykl życia aplikacji Kubernetes i zarządzać nimi. Podobnie jak w przypadku menedżerów pakietów Linuksa, takich jak *APT* i *Yum,* Helm służy do zarządzania wykresami Kubernetes, które są pakietami wstępnie skonfigurowanych zasobów Kubernetes.

W tym artykule pokazano, jak skonfigurować i używać helm w klastrze Kubernetes w uzywki AKS.

## <a name="before-you-begin"></a>Przed rozpoczęciem

W tym artykule przyjęto założenie, że masz istniejący klaster AKS. Jeśli potrzebujesz klastra AKS, zobacz szybki start usługi AKS [przy użyciu interfejsu wiersza polecenia platformy Azure][aks-quickstart-cli] lub za pomocą portalu [Azure.][aks-quickstart-portal]

Potrzebujesz również helm cli zainstalowany, który jest klientem, który działa w systemie deweloperów. Umożliwia uruchamianie, zatrzymywania i zarządzanie aplikacjami za pomocą aplikacji Helm. Jeśli używasz usługi Azure Cloud Shell, helm cli jest już zainstalowany. Aby uzyskać instrukcje instalacji na lokalnej platformie, zobacz [Instalowanie helma][helm-install].

> [!IMPORTANT]
> Helm jest przeznaczony do uruchamiania w węzłach systemu Linux. Jeśli masz węzły systemu Windows Server w klastrze, należy upewnić się, że zasobniki helm są zaplanowane tylko do uruchomienia w węzłach systemu Linux. Należy również upewnić się, że wszystkie wykresy Helm, które można zainstalować są również zaplanowane do uruchomienia w odpowiednich węzłach. Polecenia w tym artykule używają [selektorów węzłów,][k8s-node-selector] aby upewnić się, że zasobniki są zaplanowane do odpowiednich węzłów, ale nie wszystkie wykresy Helm może uwidaczniać selektor węzła. Można również rozważyć użycie innych opcji w klastrze, takich jak [skazy][taints].

## <a name="verify-your-version-of-helm"></a>Sprawdź swoją wersję Helma

Użyj `helm version` polecenia, aby zweryfikować zainstalowaną wersję helma:

```console
helm version
```

W poniższym przykładzie pokazano zainstalowany program Helm w wersji 3.0.0:

```console
$ helm version

version.BuildInfo{Version:"v3.0.0", GitCommit:"e29ce2a54e96cd02ccfce88bee4f58bb6e2a28b6", GitTreeState:"clean", GoVersion:"go1.13.4"}
```

W przypadku sieci Helm v3 wykonaj czynności opisane w [sekcji Helm v3](#install-an-application-with-helm-v3). W przypadku helm v2 wykonaj czynności opisane w [sekcji Helm v2](#install-an-application-with-helm-v2)

## <a name="install-an-application-with-helm-v3"></a>Instalowanie aplikacji z aplikacją Helm v3

### <a name="add-the-official-helm-stable-charts-repository"></a>Dodaj oficjalne repozytorium wykresów stabilnych Helm

Użyj polecenia [repozytorium helm,][helm-repo-add] aby dodać oficjalne repozytorium wykresów stabilnych Helm.

```console
helm repo add stable https://kubernetes-charts.storage.googleapis.com/
```

### <a name="find-helm-charts"></a>Znajdź wykresy Helm

Wykresy helm są używane do wdrażania aplikacji w klastrze Kubernetes. Aby wyszukać wstępnie utworzone wykresy Helm, użyj polecenia [wyszukiwania helm:][helm-search]

```console
helm search repo stable
```

Następujące skrócone przykładowe dane wyjściowe przedstawiają niektóre wykresy Helm dostępne do użycia:


```console
$ helm search repo stable

NAME                                    CHART VERSION   APP VERSION                     DESCRIPTION                                       
stable/acs-engine-autoscaler            2.2.2           2.1.1                           DEPRECATED Scales worker nodes within agent pools 
stable/aerospike                        0.3.1           v4.5.0.5                        A Helm chart for Aerospike in Kubernetes          
stable/airflow                          4.10.0          1.10.4                          Airflow is a platform to programmatically autho...
stable/ambassador                       4.4.7           0.85.0                          A Helm chart for Datawire Ambassador              
stable/anchore-engine                   1.3.7           0.5.2                           Anchore container analysis and policy evaluatio...
stable/apm-server                       2.1.5           7.0.0                           The server receives data from the Elastic APM a...
stable/ark                              4.2.2           0.10.2                          DEPRECATED A Helm chart for ark                   
stable/artifactory                      7.3.1           6.1.0                           DEPRECATED Universal Repository Manager support...
stable/artifactory-ha                   0.4.1           6.2.0                           DEPRECATED Universal Repository Manager support...
stable/atlantis                         3.8.4           v0.8.2                          A Helm chart for Atlantis https://www.runatlant...
stable/auditbeat                        1.1.0           6.7.0                           A lightweight shipper to audit the activities o...
stable/aws-cluster-autoscaler           0.3.3                                           Scales worker nodes within autoscaling groups.    
stable/aws-iam-authenticator            0.1.1           1.0                             A Helm chart for aws-iam-authenticator            
stable/bitcoind                         0.2.2           0.17.1                          Bitcoin is an innovative payment network and a ...
stable/bookstack                        1.1.2           0.27.4-1                        BookStack is a simple, self-hosted, easy-to-use...
stable/buildkite                        0.2.4           3                               DEPRECATED Agent for Buildkite                    
stable/burrow                           1.5.2           0.29.0                          Burrow is a permissionable smart contract machine 
stable/centrifugo                       3.1.0           2.1.0                           Centrifugo is a real-time messaging server.       
stable/cerebro                          1.3.1           0.8.5                           A Helm chart for Cerebro - a web admin tool tha...
stable/cert-manager                     v0.6.7          v0.6.2                          A Helm chart for cert-manager                     
stable/chaoskube                        3.1.2           0.14.0                          Chaoskube periodically kills random pods in you...
stable/chartmuseum                      2.4.0           0.8.2                           Host your own Helm Chart Repository               
stable/chronograf                       1.1.0           1.7.12                          Open-source web application written in Go and R...
stable/clamav                           1.0.4           1.6                             An Open-Source antivirus engine for detecting t...
stable/cloudserver                      1.0.3           8.1.5                           An open-source Node.js implementation of the Am...
stable/cluster-autoscaler               6.2.0           1.14.6                          Scales worker nodes within autoscaling groups.    
stable/cluster-overprovisioner          0.2.6           1.0                             Installs the a deployment that overprovisions t...
stable/cockroachdb                      2.1.16          19.1.5                          CockroachDB is a scalable, survivable, strongly...
stable/collabora-code                   1.0.5           4.0.3.1                         A Helm chart for Collabora Office - CODE-Edition  
stable/concourse                        8.2.7           5.6.0                           Concourse is a simple and scalable CI system.     
stable/consul                           3.9.2           1.5.3                           Highly available and distributed service discov...
stable/contour                          0.1.0           v0.15.0                         Contour Ingress controller for Kubernetes         
stable/coredns                          1.7.4           1.6.4                           CoreDNS is a DNS server that chains plugins and...
stable/cosbench                         1.0.1           0.0.6                           A benchmark tool for cloud object storage services
stable/coscale                          1.0.0           3.16.0                          CoScale Agent                                     
stable/couchbase-operator               1.0.1           1.2.1                           A Helm chart to deploy the Couchbase Autonomous...
stable/couchdb                          2.3.0           2.3.1                           DEPRECATED A database featuring seamless multi-...
stable/dask                             3.1.0           1.1.5                           Distributed computation in Python with task sch...
stable/dask-distributed                 2.0.2                                           DEPRECATED: Distributed computation in Python     
stable/datadog                          1.38.3          6.14.0                          DataDog Agent 
...
```

Aby zaktualizować listę wykresów, użyj polecenia [aktualizacji repozytorium helm.][helm-repo-update] W poniższym przykładzie przedstawiono pomyślną aktualizację repozytorium:

```console
$ helm repo update

Hang tight while we grab the latest from your chart repositories...
...Successfully got an update from the "stable" chart repository
Update Complete. ⎈ Happy Helming!⎈
```

### <a name="run-helm-charts"></a>Uruchamianie wykresów Helm

Aby zainstalować wykresy za pomocą polecenia Helm install, należy użyć polecenia [instalacji helm][helm-install-command] i określić nazwę wydania oraz nazwę wykresu do zainstalowania. Aby wyświetlić instalowanie wykresu Helm w akcji, zainstalujmy podstawowe wdrożenie nginx przy użyciu wykresu Helm.

```console
helm install my-nginx-ingress stable/nginx-ingress \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux
```

Następujące skrócone przykładowe dane wyjściowe przedstawiają stan wdrożenia zasobów kubernetes utworzonych przez wykres Helm:

```console
$ helm install my-nginx-ingress stable/nginx-ingress \
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
You can watch the status by running 'kubectl --namespace default get services -o wide -w my-nginx-ingress-controller'
...
```

Użyj `kubectl get services` polecenia, aby uzyskać *ADRES IP usługi.* Na przykład poniższe polecenie pokazuje *ADRES IP dla* usługi *my-nginx-ingress-controller:*

```console
$ kubectl --namespace default get services -o wide -w my-nginx-ingress-controller

NAME                          TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)                      AGE   SELECTOR
my-nginx-ingress-controller   LoadBalancer   10.0.123.1     <EXTERNAL-IP>   80:31301/TCP,443:31623/TCP   96s   app=nginx-ingress,component=controller,release=my-nginx-ingress
```

### <a name="list-releases"></a>Lista wydań

Aby wyświetlić listę wersji zainstalowanych w `helm list` klastrze, użyj polecenia.

```console
helm list
```

W poniższym przykładzie przedstawiono wydanie *my-nginx-ingress wdrożone* w poprzednim kroku:

```console
$ helm list

NAME                NAMESPACE   REVISION    UPDATED                                 STATUS      CHART                   APP VERSION
my-nginx-ingress    default     1           2019-11-22 10:08:06.048477 -0600 CST    deployed    nginx-ingress-1.25.0    0.26.1 
```

### <a name="clean-up-resources"></a>Oczyszczanie zasobów

Podczas wdrażania wykresu Helm tworzone są wiele zasobów Kubernetes. Zasoby te obejmują zasoby, wdrożenia i usługi. Aby wyczyścić te zasoby, użyj polecenia [odinstaluj helm][helm-cleanup] i `helm list` określ nazwę wydania, jak w poprzednim poleceniu.

```console
helm uninstall my-nginx-ingress
```

Poniższy przykład pokazuje, że wersja o nazwie *my-nginx-ingress* została odinstalowana:

```console
$ helm uninstall my-nginx-ingress

release "my-nginx-ingress" uninstalled
```

## <a name="install-an-application-with-helm-v2"></a>Instalowanie aplikacji z aplikacją Helm v2

### <a name="create-a-service-account"></a>Tworzenie konta usługi

Przed wdrożeniem helm w klastrze AKS z włączoną funkcją RBAC, należy konto usługi i powiązanie roli dla usługi Tiller. Aby uzyskać więcej informacji na temat zabezpieczania helm / tiller w klastrze z włączoną funkcją RBAC, zobacz [Tiller, Przestrzenie nazw i RBAC][tiller-rbac]. Jeśli klaster AKS nie jest włączony RBAC, pomiń ten krok.

Utwórz plik `helm-rbac.yaml` o nazwie i skopiuj w następującym pliku YAML:

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: tiller
  namespace: kube-system
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: tiller
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
  - kind: ServiceAccount
    name: tiller
    namespace: kube-system
```

Utwórz powiązanie konta usługi `kubectl apply` i roli za pomocą polecenia:

```console
kubectl apply -f helm-rbac.yaml
```

### <a name="secure-tiller-and-helm"></a>Bezpieczny kultywator i ster

Klient Helm i usługa Tiller uwierzytelniają się i komunikują się ze sobą przy użyciu protokołu TLS/SSL. Ta metoda uwierzytelniania pomaga zabezpieczyć klaster Kubernetes i jakie usługi można wdrożyć. Aby zwiększyć bezpieczeństwo, można wygenerować własne podpisane certyfikaty. Każdy użytkownik helmu otrzyma własny certyfikat klienta, a program Tiller zostanie zainicjowany w klastrze Kubernetes z zastosowanymi certyfikatami. Aby uzyskać więcej informacji, zobacz [Korzystanie z protokołu TLS/SSL między helmem a kultywatorem][helm2-ssl].

Za pomocą klastra Kubernetes z włączoną funkcją RBAC można kontrolować poziom dostępu, jaki firma Tiller ma do klastra. Można zdefiniować obszar nazw Kubernetes, w której jest wdrożony program Tiller, i ograniczyć, w jakich przestrzeniach nazw firma Tiller może następnie wdrażać zasoby. Takie podejście umożliwia tworzenie wystąpień Tiller w różnych przestrzeniach nazw i ograniczyć granice wdrażania i zakres użytkowników klienta Helm do niektórych obszarów nazw. Aby uzyskać więcej informacji, zobacz [Sterowanie dostępem oparte na rolach sterów .][helm2-rbac]

### <a name="configure-helm"></a>Konfigurowanie helma

Aby wdrożyć podstawowy kultywator w klastrze AKS, użyj polecenia [init helm.][helm2-init] Jeśli klaster nie jest włączony RBAC, usuń `--service-account` argument i wartość. Poniższe przykłady również ustawić [history-max][helm2-history-max] do 200.

Jeśli skonfigurowano TLS/SSL dla Tiller i Helm, pomiń ten podstawowy `--tiller-tls-` krok inicjowania i zamiast tego podaj wymagane, jak pokazano w następnym przykładzie.

```console
helm init --history-max 200 --service-account tiller --node-selectors "beta.kubernetes.io/os=linux"
```

Jeśli skonfigurowano TLS/SSL między Helm i `--tiller-tls-*` Tiller podaj parametry i nazwy własnych certyfikatów, jak pokazano w poniższym przykładzie:

```console
helm init \
    --tiller-tls \
    --tiller-tls-cert tiller.cert.pem \
    --tiller-tls-key tiller.key.pem \
    --tiller-tls-verify \
    --tls-ca-cert ca.cert.pem \
    --history-max 200 \
    --service-account tiller \
    --node-selectors "beta.kubernetes.io/os=linux"
```

### <a name="find-helm-charts"></a>Znajdź wykresy Helm

Wykresy helm są używane do wdrażania aplikacji w klastrze Kubernetes. Aby wyszukać wstępnie utworzone wykresy Helm, użyj polecenia [wyszukiwania helm:][helm2-search]

```console
helm search
```

Następujące skrócone przykładowe dane wyjściowe przedstawiają niektóre wykresy Helm dostępne do użycia:

```
$ helm search

NAME                           CHART VERSION    APP VERSION  DESCRIPTION
stable/aerospike               0.1.7            v3.14.1.2    A Helm chart for Aerospike in Kubernetes
stable/anchore-engine          0.1.7            0.1.10       Anchore container analysis and policy evaluatio...
stable/apm-server              0.1.0            6.2.4        The server receives data from the Elastic APM a...
stable/ark                     1.0.1            0.8.2        A Helm chart for ark
stable/artifactory             7.2.1            6.0.0        Universal Repository Manager supporting all maj...
stable/artifactory-ha          0.2.1            6.0.0        Universal Repository Manager supporting all maj...
stable/auditbeat               0.1.0            6.2.4        A lightweight shipper to audit the activities o...
stable/aws-cluster-autoscaler  0.3.3                         Scales worker nodes within autoscaling groups.
stable/bitcoind                0.1.3            0.15.1       Bitcoin is an innovative payment network and a ...
stable/buildkite               0.2.3            3            Agent for Buildkite
stable/burrow                  0.4.4            0.17.1       Burrow is a permissionable smart contract machine
stable/centrifugo              2.0.1            1.7.3        Centrifugo is a real-time messaging server.
stable/cerebro                 0.1.0            0.7.3        A Helm chart for Cerebro - a web admin tool tha...
stable/cert-manager            v0.3.3           v0.3.1       A Helm chart for cert-manager
stable/chaoskube               0.7.0            0.8.0        Chaoskube periodically kills random pods in you...
stable/chartmuseum             1.5.0            0.7.0        Helm Chart Repository with support for Amazon S...
stable/chronograf              0.4.5            1.3          Open-source web application written in Go and R...
stable/cluster-autoscaler      0.6.4            1.2.2        Scales worker nodes within autoscaling groups.
stable/cockroachdb             1.1.1            2.0.0        CockroachDB is a scalable, survivable, strongly...
stable/concourse               1.10.1           3.14.1       Concourse is a simple and scalable CI system.
stable/consul                  3.2.0            1.0.0        Highly available and distributed service discov...
stable/coredns                 0.9.0            1.0.6        CoreDNS is a DNS server that chains plugins and...
stable/coscale                 0.2.1            3.9.1        CoScale Agent
stable/dask                    1.0.4            0.17.4       Distributed computation in Python with task sch...
stable/dask-distributed        2.0.2                         DEPRECATED: Distributed computation in Python
stable/datadog                 0.18.0           6.3.0        DataDog Agent
...
```

Aby zaktualizować listę wykresów, użyj polecenia [aktualizacji repozytorium helm.][helm2-repo-update] W poniższym przykładzie przedstawiono pomyślną aktualizację repozytorium:

```console
$ helm repo update

Hold tight while we grab the latest from your chart repositories...
...Skip local chart repository
...Successfully got an update from the "stable" chart repository
Update Complete.
```

### <a name="run-helm-charts"></a>Uruchamianie wykresów Helm

Aby zainstalować wykresy za pomocą polecenia Helm install, należy użyć polecenia [instalacji steru][helm2-install-command] i określić nazwę wykresu do zainstalowania. Aby wyświetlić instalowanie wykresu Helm w akcji, zainstalujmy podstawowe wdrożenie nginx przy użyciu wykresu Helm. Jeśli skonfigurowano TLS/SSL, `--tls` dodaj parametr, aby użyć certyfikatu klienta Helm.

```console
helm install stable/nginx-ingress \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux
```

Następujące skrócone przykładowe dane wyjściowe przedstawiają stan wdrożenia zasobów kubernetes utworzonych przez wykres Helm:

```
$ helm install stable/nginx-ingress --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux

NAME:   flailing-alpaca
LAST DEPLOYED: Thu May 23 12:55:21 2019
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/ConfigMap
NAME                                      DATA  AGE
flailing-alpaca-nginx-ingress-controller  1     0s

==> v1/Pod(related)
NAME                                                            READY  STATUS             RESTARTS  AGE
flailing-alpaca-nginx-ingress-controller-56666dfd9f-bq4cl       0/1    ContainerCreating  0         0s
flailing-alpaca-nginx-ingress-default-backend-66bc89dc44-m87bp  0/1    ContainerCreating  0         0s

==> v1/Service
NAME                                           TYPE          CLUSTER-IP  EXTERNAL-IP  PORT(S)                     AGE
flailing-alpaca-nginx-ingress-controller       LoadBalancer  10.0.109.7  <pending>    80:31219/TCP,443:32421/TCP  0s
flailing-alpaca-nginx-ingress-default-backend  ClusterIP     10.0.44.97  <none>       80/TCP                      0s
...
```

Wypełnianie adresu *IP usługi* nginx-ingress-controller zajmuje minutę lub dwie i umożliwia dostęp do niego za pomocą przeglądarki internetowej.

### <a name="list-helm-releases"></a>Lista wydań Helm

Aby wyświetlić listę wersji zainstalowanych w klastrze, użyj polecenia [listy sterów.][helm2-list] W poniższym przykładzie przedstawiono zwolnienie nginx-ingress wdrożone w poprzednim kroku. Jeśli skonfigurowano TLS/SSL, `--tls` dodaj parametr, aby użyć certyfikatu klienta Helm.

```console
$ helm list

NAME                REVISION    UPDATED                     STATUS      CHART                 APP VERSION   NAMESPACE
flailing-alpaca   1         Thu May 23 12:55:21 2019    DEPLOYED    nginx-ingress-1.6.13    0.24.1      default
```

### <a name="clean-up-resources"></a>Oczyszczanie zasobów

Podczas wdrażania wykresu Helm tworzone są wiele zasobów Kubernetes. Zasoby te obejmują zasoby, wdrożenia i usługi. Aby wyczyścić te zasoby, użyj `helm delete` polecenia i określ nazwę `helm list` wydania, jak w poprzednim poleceniu. Poniższy przykład usuwa wydanie o nazwie *flailing-alpaca:*

```console
$ helm delete flailing-alpaca

release "flailing-alpaca" deleted
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat zarządzania wdrożeniami aplikacji Kubernetes za pomocą helm, zobacz dokumentację helm.

> [!div class="nextstepaction"]
> [Dokumentacja steru][helm-documentation]

<!-- LINKS - external -->
[helm]: https://github.com/kubernetes/helm/
[helm-cleanup]: https://helm.sh/docs/intro/using_helm/#helm-uninstall-uninstalling-a-release
[helm-documentation]: https://helm.sh/docs/
[helm-install]: https://helm.sh/docs/intro/install/
[helm-install-command]: https://helm.sh/docs/intro/using_helm/#helm-install-installing-a-package
[helm-repo-add]: https://helm.sh/docs/intro/quickstart/#initialize-a-helm-chart-repository
[helm-search]: https://helm.sh/docs/intro/using_helm/#helm-search-finding-charts
[helm-repo-update]: https://helm.sh/docs/intro/using_helm/#helm-repo-working-with-repositories
[helm2-init]: https://v2.helm.sh/docs/helm/#helm-init
[helm2-install-command]: https://v2.helm.sh/docs/helm/#helm-install
[helm2-install-options]: https://github.com/kubernetes/helm/blob/master/docs/install.md
[helm2-list]: https://v2.helm.sh/docs/helm/#helm-list
[helm2-history-max]: https://v2.helm.sh/docs/using_helm/#initialize-helm-and-install-tiller
[helm2-rbac]: https://v2.helm.sh/docs/using_helm/#role-based-access-control
[helm2-repo-update]: https://v2.helm.sh/docs/helm/#helm-repo-update
[helm2-search]: https://v2.helm.sh/docs/helm/#helm-search
[tiller-rbac]: https://v2.helm.sh/docs/using_helm/#tiller-namespaces-and-rbac
[helm2-ssl]: https://v2.helm.sh/docs/using_helm/#using-ssl-between-helm-and-tiller
            
<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[k8s-node-selector]: concepts-clusters-workloads.md#node-selectors
[taints]: operator-best-practices-advanced-scheduler.md
