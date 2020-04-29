---
title: Instalowanie istniejących aplikacji za pomocą programu Helm w AKS
description: Dowiedz się, jak wdrażać kontenery w klastrze usługi Azure Kubernetes Service (AKS) za pomocą narzędzia do pakowania Helm
services: container-service
author: zr-msft
ms.topic: article
ms.date: 11/22/2019
ms.author: zarhoads
ms.openlocfilehash: e46bed5fc9fd83a907f8c9e716317a54548c58cc
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81870247"
---
# <a name="install-existing-applications-with-helm-in-azure-kubernetes-service-aks"></a>Instalowanie istniejących aplikacji przy użyciu usługi Helm w usłudze Azure Kubernetes Service (AKS)

[Helm][helm] to narzędzie do tworzenia pakietów typu "open source", które ułatwia Instalowanie i zarządzanie cyklem życia aplikacji Kubernetes. Podobnie jak w przypadku menedżerów pakietów systemu Linux, takich jak *apt* i *yum*, Helm służy do zarządzania wykresami Kubernetes, które są pakietami wstępnie skonfigurowanych zasobów Kubernetes.

W tym artykule opisano sposób konfigurowania i używania Helm w klastrze Kubernetes w systemie AKS.

## <a name="before-you-begin"></a>Przed rozpoczęciem

W tym artykule przyjęto założenie, że masz istniejący klaster AKS. Jeśli potrzebujesz klastra AKS, zapoznaj się z przewodnikiem Szybki Start AKS [przy użyciu interfejsu wiersza polecenia platformy Azure][aks-quickstart-cli] lub [przy użyciu Azure Portal][aks-quickstart-portal].

Wymagany jest również interfejs wiersza polecenia Helm, który jest klientem uruchomionym w systemie deweloperskim. Pozwala ona uruchamiać, zatrzymywać i zarządzać aplikacjami za pomocą Helm. W przypadku korzystania z Azure Cloud Shell interfejs wiersza polecenia Helm jest już zainstalowany. Aby uzyskać instrukcje dotyczące instalacji na lokalnej platformie, zobacz [Installing Helm][helm-install].

> [!IMPORTANT]
> Helm jest przeznaczony do uruchamiania w węzłach systemu Linux. Jeśli w klastrze znajdują się węzły systemu Windows Server, musisz upewnić się, że Helm są zaplanowane do uruchomienia tylko na węzłach z systemem Linux. Należy również upewnić się, że wszystkie zainstalowane wykresy Helm są również zaplanowane do uruchomienia w prawidłowych węzłach. Polecenia w tym artykule używają selektorów [węzłów][k8s-node-selector] , aby upewnić się, że zasobniki są zaplanowane do poprawnych węzłów, ale nie wszystkie wykresy Helm mogą uwidocznić wybór węzła. Możesz również rozważyć użycie innych opcji w klastrze, [takich jak][taints]przypisania.

## <a name="verify-your-version-of-helm"></a>Weryfikowanie wersji programu Helm

Użyj polecenia `helm version` , aby sprawdzić zainstalowaną wersję programu Helm:

```console
helm version
```

W poniższym przykładzie przedstawiono zainstalowaną Helm wersję 3.0.0:

```console
$ helm version

version.BuildInfo{Version:"v3.0.0", GitCommit:"e29ce2a54e96cd02ccfce88bee4f58bb6e2a28b6", GitTreeState:"clean", GoVersion:"go1.13.4"}
```

W przypadku wersji Helm v3 wykonaj kroki opisane w [sekcji Helm v3](#install-an-application-with-helm-v3). W przypadku Helm v2 postępuj zgodnie z instrukcjami w [sekcji Helm v2](#install-an-application-with-helm-v2)

## <a name="install-an-application-with-helm-v3"></a>Instalowanie aplikacji z Helm v3

### <a name="add-the-official-helm-stable-charts-repository"></a>Dodawanie oficjalnego repozytorium Helm stabilnych wykresów

Użyj polecenia [Helm repozytorium][helm-repo-add] , aby dodać oficjalne repozytorium Helm stabilnych wykresów.

```console
helm repo add stable https://kubernetes-charts.storage.googleapis.com/
```

### <a name="find-helm-charts"></a>Znajdź wykresy Helm

Wykresy Helm są używane do wdrażania aplikacji w klastrze Kubernetes. Aby wyszukać wstępnie utworzone wykresy Helm, użyj polecenia [wyszukiwania Helm][helm-search] :

```console
helm search repo stable
```

Następujące wąskie przykładowe dane wyjściowe pokazują niektóre wykresy Helm dostępne do użycia:


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

Aby zaktualizować listę wykresów, użyj polecenia [Helm repozytorium aktualizacji][helm-repo-update] . Poniższy przykład przedstawia pomyślne aktualizowanie repozytorium:

```console
$ helm repo update

Hang tight while we grab the latest from your chart repositories...
...Successfully got an update from the "stable" chart repository
Update Complete. ⎈ Happy Helming!⎈
```

### <a name="run-helm-charts"></a>Uruchom wykresy Helm

Aby zainstalować wykresy z programem Helm, użyj polecenia [Install Helm][helm-install-command] i określ nazwę wydania oraz nazwę wykresu do zainstalowania. Aby zapoznać się z tematem Instalowanie wykresu Helm w działaniu, Zainstalujmy podstawowe wdrożenie Nginx przy użyciu wykresu Helm.

```console
helm install my-nginx-ingress stable/nginx-ingress \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux
```

Następujące wąskie przykładowe dane wyjściowe pokazują stan wdrożenia zasobów Kubernetes utworzonych przez wykres Helm:

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

Użyj `kubectl get services` polecenia, aby uzyskać *zewnętrzny adres IP* usługi. Na przykład poniższe polecenie pokazuje *zewnętrzny adres IP* dla usługi *My-Nginx-Ingres-Controller* :

```console
$ kubectl --namespace default get services -o wide -w my-nginx-ingress-controller

NAME                          TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)                      AGE   SELECTOR
my-nginx-ingress-controller   LoadBalancer   10.0.123.1     <EXTERNAL-IP>   80:31301/TCP,443:31623/TCP   96s   app=nginx-ingress,component=controller,release=my-nginx-ingress
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

### <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po wdrożeniu wykresu Helm są tworzone różne zasoby Kubernetes. Do tych zasobów należą między innymi: zasoby, wdrożenia i usługi. Aby wyczyścić te zasoby, użyj polecenia [Helm Uninstall][helm-cleanup] i określ nazwę wydania, jak to zostało znalezione w poprzednim `helm list` poleceniu.

```console
helm uninstall my-nginx-ingress
```

W poniższym przykładzie pokazano wydanie o nazwie *My-Nginx-* Transfered zostało odinstalowane:

```console
$ helm uninstall my-nginx-ingress

release "my-nginx-ingress" uninstalled
```

## <a name="install-an-application-with-helm-v2"></a>Instalowanie aplikacji z Helm v2

### <a name="create-a-service-account"></a>Utwórz konto usługi

Przed wdrożeniem Helm w klastrze AKS z włączoną funkcją RBAC należy mieć konto usługi i powiązanie roli dla usługi. Aby uzyskać więcej informacji na temat zabezpieczania Helm/do usługi w klastrze z obsługą RBAC, zobacz odniesień [, przestrzenie nazw i RBAC][tiller-rbac]. Jeśli w klastrze AKS nie włączono kontroli RBAC, Pomiń ten krok.

Utwórz plik o nazwie `helm-rbac.yaml` i skopiuj w następującym YAML:

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

Utwórz konto usługi i powiązanie roli za pomocą `kubectl apply` polecenia:

```console
kubectl apply -f helm-rbac.yaml
```

### <a name="secure-tiller-and-helm"></a>Zabezpieczanie do i Helm

Klient Helm i usługa do odczekania uwierzytelniają się i komunikują się ze sobą przy użyciu protokołu TLS/SSL. Ta metoda uwierzytelniania pomaga zabezpieczyć klaster Kubernetes i jakie usługi można wdrożyć. Aby zwiększyć bezpieczeństwo, można wygenerować własne certyfikaty z podpisem. Każdy użytkownik Helm otrzyma swój własny certyfikat klienta i zostanie on zainicjowany w klastrze Kubernetes z zastosowanymi certyfikatami. Aby uzyskać więcej informacji, zobacz [Korzystanie z protokołu TLS/SSL między Helm i przydziałem][helm2-ssl]IT.

W przypadku klastra Kubernetes z obsługą kontroli RBAC można kontrolować poziom dostępu do klastra. Można zdefiniować przestrzeń nazw Kubernetes, która jest wdrażana w programie, i ograniczyć zakres przestrzeni nazw, które mogą następnie wdrożyć zasoby w programie. Takie podejście umożliwia tworzenie wystąpień programu w różnych przestrzeniach nazw i ograniczanie granic wdrożenia oraz określanie zakresu użytkowników programu Helm Client do określonych przestrzeni nazw. Aby uzyskać więcej informacji, zobacz [Helm kontroli dostępu opartej na rolach][helm2-rbac].

### <a name="configure-helm"></a>Konfigurowanie Helm

Aby wdrożyć podstawową usługę do klastra AKS, użyj polecenia [init Helm][helm2-init] . Jeśli w `--service-account` klastrze nie włączono kontroli RBAC, Usuń argument i wartość. W poniższych przykładach ustawiono również [historię — maks][helm2-history-max] . do 200.

W przypadku skonfigurowania protokołu TLS/SSL dla operacji do odniesień i Helm należy pominąć ten podstawowy krok inicjujący, a następnie podać wymagane `--tiller-tls-` , jak pokazano w następnym przykładzie.

```console
helm init --history-max 200 --service-account tiller --node-selectors "beta.kubernetes.io/os=linux"
```

W przypadku skonfigurowania protokołu TLS/SSL między Helm i do przydzielenia przez `--tiller-tls-*` program do dostarczania parametrów i nazw własnych certyfikatów, jak pokazano w następującym przykładzie:

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

Wykresy Helm są używane do wdrażania aplikacji w klastrze Kubernetes. Aby wyszukać wstępnie utworzone wykresy Helm, użyj polecenia [wyszukiwania Helm][helm2-search] :

```console
helm search
```

Następujące wąskie przykładowe dane wyjściowe pokazują niektóre wykresy Helm dostępne do użycia:

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

Aby zaktualizować listę wykresów, użyj polecenia [Helm repozytorium aktualizacji][helm2-repo-update] . Poniższy przykład przedstawia pomyślne aktualizowanie repozytorium:

```console
$ helm repo update

Hold tight while we grab the latest from your chart repositories...
...Skip local chart repository
...Successfully got an update from the "stable" chart repository
Update Complete.
```

### <a name="run-helm-charts"></a>Uruchom wykresy Helm

Aby zainstalować wykresy z Helm, użyj polecenia [Install Helm][helm2-install-command] i określ nazwę wykresu do zainstalowania. Aby zapoznać się z tematem Instalowanie wykresu Helm w działaniu, Zainstalujmy podstawowe wdrożenie Nginx przy użyciu wykresu Helm. Jeśli skonfigurowano protokół TLS/SSL, Dodaj `--tls` parametr, aby użyć certyfikatu klienta Helm.

```console
helm install stable/nginx-ingress \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux
```

Następujące wąskie przykładowe dane wyjściowe pokazują stan wdrożenia zasobów Kubernetes utworzonych przez wykres Helm:

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

Aby można było wypełniać zewnętrzny adres *IP* usługi Nginx-transfer-Controller i umożliwić dostęp do niej przy użyciu przeglądarki sieci Web, potrwa minutę lub dwa.

### <a name="list-helm-releases"></a>Lista wersji Helm

Aby wyświetlić listę wersji zainstalowanych w klastrze, użyj polecenia [listy Helm][helm2-list] . Poniższy przykład przedstawia wydanie Nginx-Ingress wdrożone w poprzednim kroku. Jeśli skonfigurowano protokół TLS/SSL, Dodaj `--tls` parametr, aby użyć certyfikatu klienta Helm.

```console
$ helm list

NAME                REVISION    UPDATED                     STATUS      CHART                 APP VERSION   NAMESPACE
flailing-alpaca   1         Thu May 23 12:55:21 2019    DEPLOYED    nginx-ingress-1.6.13    0.24.1      default
```

### <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po wdrożeniu wykresu Helm są tworzone różne zasoby Kubernetes. Do tych zasobów należą między innymi: zasoby, wdrożenia i usługi. Aby wyczyścić te zasoby, użyj `helm delete` polecenia i określ nazwę wydania, jak to zostało znalezione w poprzednim `helm list` poleceniu. Poniższy przykład usuwa wydanie o nazwie *Flailing-Alpaca*:

```console
$ helm delete flailing-alpaca

release "flailing-alpaca" deleted
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat zarządzania wdrożeniami aplikacji Kubernetes z Helm, zobacz dokumentację Helm.

> [!div class="nextstepaction"]
> [Dokumentacja Helm][helm-documentation]

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
