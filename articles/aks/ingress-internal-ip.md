---
title: Kontroler transferu danych przychodzących w sieci wewnętrznej
titleSuffix: Azure Kubernetes Service
description: Dowiedz się, jak zainstalować i skonfigurować międzyNGINXowy kontroler dla wewnętrznej, prywatnej sieci w klastrze usługi Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 03/16/2021
ms.openlocfilehash: 3201f510db9970b7db548ee6a3348fa68d278248
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104601469"
---
# <a name="create-an-ingress-controller-to-an-internal-virtual-network-in-azure-kubernetes-service-aks"></a>Tworzenie kontrolera transferu danych przychodzących w wewnętrznej sieci wirtualnej w usłudze Azure Kubernetes Service (AKS)

Kontroler ruchu przychodzącego to element oprogramowania dostarczający odwrotny serwer proxy, konfigurowalne trasowanie ruchu oraz zakończenie protokołu TLS dla usług Kubernetes. Zasoby ruchu przychodzącego usług Kubernetes są używane do skonfigurowania zasad ruchu przychodzącego oraz tras dla poszczególnych usług Kubernetes. Dzięki korzystaniu z kontrolera ruchu przychodzącego oraz zasad ruchu przychodzącego można użyć jednego adresu IP do trasowania ruchu w wielu usługach w klastrze Kubernetes.

W tym artykule pokazano, jak wdrożyć [kontroler Nginx Ingress][nginx-ingress] w klastrze usługi Azure Kubernetes Service (AKS). Kontroler transferu danych przychodzących jest konfigurowany w wewnętrznej, prywatnej sieci wirtualnej i adresie IP. Nie jest dozwolony dostęp zewnętrzny. W klastrze AKS są uruchamiane dwie aplikacje, z których każdy jest dostępny za pośrednictwem pojedynczego adresu IP.

Możesz również wykonać następujące czynności:

- [Tworzenie podstawowego kontrolera danych wejściowych z łącznością sieci zewnętrznej][aks-ingress-basic]
- [Włącz dodatek routingu aplikacji protokołu HTTP][aks-http-app-routing]
- [Tworzenie kontrolera transferu danych przychodzących korzystającego z własnych certyfikatów TLS][aks-ingress-own-tls]
- Utwórz kontroler transferu danych przychodzących, który używa szyfrowania, aby automatycznie generować certyfikaty TLS [z dynamicznym publicznym adresem IP][aks-ingress-tls] lub [statycznym publicznym adresem IP][aks-ingress-static-tls]

## <a name="before-you-begin"></a>Zanim rozpoczniesz

W tym artykule jest instalowany [Helm 3][helm] w celu zainstalowania kontrolera transferu Nginx. Upewnij się, że korzystasz z najnowszej wersji programu Helm i masz dostęp do repozytorium danych przychodzących *-Nginx* Helm. Aby uzyskać więcej informacji na temat konfigurowania i używania Helm, zobacz [Install Applications with Helm in Azure Kubernetes Service (AKS)][use-helm].

Ten artykuł wymaga również uruchomienia interfejsu wiersza polecenia platformy Azure w wersji 2.0.64 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli-install].

## <a name="create-an-ingress-controller"></a>Tworzenie kontrolera transferu danych przychodzących

Domyślnie kontroler transferu danych przychodzących NGINX jest tworzony z dynamicznym przypisaniem publicznego adresu IP. Typowym wymaganiem konfiguracji jest użycie wewnętrznej, prywatnej sieci i adresu IP. Takie podejście umożliwia ograniczenie dostępu do usług użytkownikom wewnętrznym bez dostępu zewnętrznego.

Utwórz plik o nazwie *Internal-Ingres. YAML* przy użyciu następującego przykładowego pliku manifestu. Ten przykład przypisuje *10.240.0.42* do zasobu *loadBalancerIP* . Podaj własny wewnętrzny adres IP do użycia z kontrolerem transferu danych przychodzących. Upewnij się, że ten adres IP nie jest już używany w sieci wirtualnej. Ponadto, jeśli używasz istniejącej sieci wirtualnej i podsieci, musisz skonfigurować klaster AKS z odpowiednimi uprawnieniami do zarządzania siecią wirtualną i podsiecią. Aby uzyskać więcej informacji, zobacz [Korzystanie z sieci korzystającą wtyczki kubenet z własnymi zakresami adresów IP w usłudze Azure Kubernetes Service (AKS)][aks-configure-kubenet-networking] lub [Konfigurowanie sieci CNI platformy Azure w usłudze Azure KUBERNETES Service (AKS)][aks-configure-advanced-networking] .

```yaml
controller:
  service:
    loadBalancerIP: 10.240.0.42
    annotations:
      service.beta.kubernetes.io/azure-load-balancer-internal: "true"
```

Teraz Wdróż wykres *Nginx-* transferal z Helm. Aby użyć pliku manifestu utworzonego w poprzednim kroku, Dodaj `-f internal-ingress.yaml` parametr. W celu zwiększenia nadmiarowości za pomocą parametru `--set controller.replicaCount` wdrażane są dwie repliki kontrolerów wejściowych NGINX. Aby w pełni korzystać z uruchamiania replik kontrolera transferu danych przychodzących, upewnij się, że w klastrze AKS znajduje się więcej niż jeden węzeł.

Kontroler wejściowy należy również zaplanować w węźle z systemem Linux. Nie należy go uruchamiać w węzłach z systemem Windows Server. Za pomocą parametru `--set nodeSelector` podaje się selektor węzła, który nakazuje harmonogramowi usługi Kubernetes uruchomienie kontrolera wejściowego NGINX w węźle opartym na systemie Linux.

> [!TIP]
> Poniższy przykład tworzy przestrzeń nazw Kubernetes dla zasobów przychodzących o nazwie transfery *-Basic*. W razie potrzeby określ przestrzeń nazw dla własnego środowiska. Jeśli w klastrze AKS nie włączono kontroli RBAC, Dodaj `--set rbac.create=false` do poleceń Helm.

> [!TIP]
> Jeśli chcesz włączyć [zachowywanie źródłowych adresów IP klienta][client-source-ip] dla żądań do kontenerów w klastrze, Dodaj `--set controller.service.externalTrafficPolicy=Local` do polecenia instalacji Helm. Adres IP źródła klienta jest przechowywany w nagłówku żądania w obszarze *X-forwardd-for*. W przypadku korzystania z kontrolera transferu danych przychodzących z włączonym zachowywaniem źródłowych adresów IP klienta protokół TLS nie będzie działał.

```console
# Create a namespace for your ingress resources
kubectl create namespace ingress-basic

# Add the ingress-nginx repository
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx

# Use Helm to deploy an NGINX ingress controller
helm install nginx-ingress ingress-nginx/ingress-nginx \
    --namespace ingress-basic \
    -f internal-ingress.yaml \
    --set controller.replicaCount=2 \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set controller.admissionWebhooks.patch.nodeSelector."beta\.kubernetes\.io/os"=linux
```

Gdy usługa równoważenia obciążenia Kubernetes jest tworzona dla kontrolera NGINX, wewnętrzny adres IP jest przypisywany. Aby uzyskać publiczny adres IP, użyj `kubectl get service` polecenia.

```console
kubectl --namespace ingress-basic get services -o wide -w nginx-ingress-ingress-nginx-controller
```

Przypisanie adresu IP do usługi może potrwać kilka minut, jak pokazano w następujących przykładowych danych wyjściowych:

```
$ kubectl --namespace ingress-basic get services -o wide -w nginx-ingress-ingress-nginx-controller

NAME                                     TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)                      AGE   SELECTOR
nginx-ingress-ingress-nginx-controller   LoadBalancer   10.0.74.133   EXTERNAL_IP     80:32486/TCP,443:30953/TCP   44s   app.kubernetes.io/component=controller,app.kubernetes.io/instance=nginx-ingress,app.kubernetes.io/name=ingress-nginx
```

Nie utworzono jeszcze żadnych reguł dotyczących ruchu przychodzącego, więc w przypadku przechodzenia do wewnętrznego adresu IP zostanie wyświetlona domyślna strona 404 kontrolera NGINX. Reguły dotyczące transferu danych przychodzących są konfigurowane w poniższych krokach.

## <a name="run-demo-applications"></a>Uruchom aplikacje demonstracyjne

Aby wyświetlić kontroler transferu danych przychodzących w działaniu, uruchom dwie aplikacje demonstracyjne w klastrze AKS. W tym przykładzie użyto `kubectl apply` do wdrożenia dwóch wystąpień prostej aplikacji *Hello World* .

Utwórz plik *AKS-HelloWorld. YAML* i skopiuj go do poniższego przykładu YAML:

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: aks-helloworld
spec:
  replicas: 1
  selector:
    matchLabels:
      app: aks-helloworld
  template:
    metadata:
      labels:
        app: aks-helloworld
    spec:
      containers:
      - name: aks-helloworld
        image: mcr.microsoft.com/azuredocs/aks-helloworld:v1
        ports:
        - containerPort: 80
        env:
        - name: TITLE
          value: "Welcome to Azure Kubernetes Service (AKS)"
---
apiVersion: v1
kind: Service
metadata:
  name: aks-helloworld
spec:
  type: ClusterIP
  ports:
  - port: 80
  selector:
    app: aks-helloworld
```

Utwórz plik *YAML-demonstracyjny* i skopiuj w poniższym przykładzie YAML:

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: ingress-demo
spec:
  replicas: 1
  selector:
    matchLabels:
      app: ingress-demo
  template:
    metadata:
      labels:
        app: ingress-demo
    spec:
      containers:
      - name: ingress-demo
        image: mcr.microsoft.com/azuredocs/aks-helloworld:v1
        ports:
        - containerPort: 80
        env:
        - name: TITLE
          value: "AKS Ingress Demo"
---
apiVersion: v1
kind: Service
metadata:
  name: ingress-demo
spec:
  type: ClusterIP
  ports:
  - port: 80
  selector:
    app: ingress-demo
```

Uruchom dwie aplikacje demonstracyjne przy użyciu `kubectl apply` :

```console
kubectl apply -f aks-helloworld.yaml --namespace ingress-basic
kubectl apply -f ingress-demo.yaml --namespace ingress-basic
```

## <a name="create-an-ingress-route"></a>Tworzenie trasy transferu danych przychodzących

Obie aplikacje działają teraz w klastrze Kubernetes. Aby skierować ruch do poszczególnych aplikacji, utwórz zasób Kubernetes. Zasób danych przychodzących konfiguruje reguły, które kierują ruch do jednej z dwóch aplikacji.

W poniższym przykładzie ruch do adresu `http://10.240.0.42/` jest kierowany do usługi o nazwie `aks-helloworld` . Ruch do adresu `http://10.240.0.42/hello-world-two` jest kierowany do `ingress-demo` usługi.

Utwórz plik o nazwie `hello-world-ingress.yaml` i skopiuj w poniższym przykładzie YAML.

```yaml
apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  namespace: ingress-basic
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/ssl-redirect: "false"
    nginx.ingress.kubernetes.io/use-regex: "true"
    nginx.ingress.kubernetes.io/rewrite-target: /$1
spec:
  rules:
  - http:
      paths:
      - backend:
          serviceName: aks-helloworld
          servicePort: 80
        path: /hello-world-one(/|$)(.*)
      - backend:
          serviceName: ingress-demo
          servicePort: 80
        path: /hello-world-two(/|$)(.*)
      - backend:
          serviceName: aks-helloworld
          servicePort: 80
        path: /(.*)
```

Utwórz zasób transferu danych przychodzących przy użyciu `kubectl apply -f hello-world-ingress.yaml` polecenia.

```console
kubectl apply -f hello-world-ingress.yaml
```

Następujące przykładowe dane wyjściowe pokazują, że tworzony jest zasób transferu danych przychodzących.

```
$ kubectl apply -f hello-world-ingress.yaml

ingress.extensions/hello-world-ingress created
```

## <a name="test-the-ingress-controller"></a>Testowanie kontrolera transferu danych przychodzących

Aby przetestować trasy dla kontrolera transferu danych przychodzących, przejdź do dwóch aplikacji za pomocą klienta sieci Web. W razie konieczności można szybko przetestować te funkcje wyłącznie wewnętrznie z poziomu klastra AKS. Utwórz test pod i Dołącz do niego sesję terminalu:

```console
kubectl run -it --rm aks-ingress-test --image=mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11 --namespace ingress-basic
```

Zainstaluj `curl` w temacie using `apt-get` :

```console
apt-get update && apt-get install -y curl
```

Uzyskaj teraz dostęp do adresu kontrolera usługi Kubernetes Ingress przy użyciu `curl` , takiego jak *http://10.240.0.42* . Podaj własny wewnętrzny adres IP określony podczas wdrażania kontrolera transferu danych przychodzących w pierwszym kroku tego artykułu.

```console
curl -L http://10.240.0.42
```

Nie dostarczono żadnej dodatkowej ścieżki z adresem, dlatego kontroler transferu danych przychodzących jest domyślnie kierowany do */* trasy. Zostanie zwrócona pierwsza aplikacja demonstracyjna, jak pokazano w następujących wąskich przykładowych danych wyjściowych:

```
$ curl -L http://10.240.0.42

<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <link rel="stylesheet" type="text/css" href="/static/default.css">
    <title>Welcome to Azure Kubernetes Service (AKS)</title>
[...]
```

Teraz dodaj ścieżkę */Hello-World-Two* do adresu, na przykład *http://10.240.0.42/hello-world-two* . Zostanie zwrócona druga aplikacja demonstracyjna z tytułem niestandardowym, jak pokazano w następujących wąskich przykładowych danych wyjściowych:

```
$ curl -L -k http://10.240.0.42/hello-world-two

<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <link rel="stylesheet" type="text/css" href="/static/default.css">
    <title>AKS Ingress Demo</title>
[...]
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

W tym artykule użyto Helm do zainstalowania składników przychodzących. Po wdrożeniu wykresu Helm są tworzone różne zasoby Kubernetes. Te zasoby obejmują między innymi te, wdrożenia i usługi. Aby wyczyścić te zasoby, można usunąć całą przykładową przestrzeń nazw lub poszczególne zasoby.

### <a name="delete-the-sample-namespace-and-all-resources"></a>Usuń przykładową przestrzeń nazw i wszystkie zasoby

Aby usunąć całą przykładową przestrzeń nazw, użyj `kubectl delete` polecenia i określ nazwę przestrzeni nazw. Wszystkie zasoby w przestrzeni nazw są usuwane.

```console
kubectl delete namespace ingress-basic
```

### <a name="delete-resources-individually"></a>Usuń zasoby pojedynczo

Alternatywnie, bardziej szczegółowe podejście polega na usunięciu utworzonych poszczególnych zasobów. Utwórz listę wersji Helm za pomocą `helm list` polecenia. 

```console
helm list --namespace ingress-basic
```

Poszukaj wykresów o nazwie *Nginx-Ingress* i *AKS-HelloWorld*, jak pokazano w następujących przykładowych danych wyjściowych:

```
$ helm list --namespace ingress-basic

NAME                    NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                   APP VERSION
nginx-ingress           ingress-basic   1               2020-01-06 19:55:46.358275 -0600 CST    deployed        nginx-ingress-1.27.1    0.26.1  
```

Odinstaluj wydania za pomocą `helm uninstall` polecenia.

```console
helm uninstall nginx-ingress --namespace ingress-basic
```

Poniższy przykład Odinstalowuje wdrożenie NGINX.

```
$ helm uninstall nginx-ingress --namespace ingress-basic

release "nginx-ingress" uninstalled
```

Następnie usuń dwie przykładowe aplikacje:

```console
kubectl delete -f aks-helloworld.yaml --namespace ingress-basic
kubectl delete -f ingress-demo.yaml --namespace ingress-basic
```

Usuń trasę transferu danych przychodzących, która kieruje ruch do aplikacji przykładowych:

```console
kubectl delete -f hello-world-ingress.yaml
```

Na koniec można usunąć samą przestrzeń nazw. Użyj `kubectl delete` polecenia i określ nazwę przestrzeni nazw:

```console
kubectl delete namespace ingress-basic
```

## <a name="next-steps"></a>Następne kroki

W tym artykule uwzględniono niektóre składniki zewnętrzne do AKS. Aby dowiedzieć się więcej o tych składnikach, zobacz następujące strony projektu:

- [Interfejs wiersza polecenia Helm][helm-cli]
- [Kontroler transferu danych przychodzących NGINX][nginx-ingress]

Możesz również wykonać następujące czynności:

- [Tworzenie podstawowego kontrolera danych wejściowych z łącznością sieci zewnętrznej][aks-ingress-basic]
- [Włącz dodatek routingu aplikacji protokołu HTTP][aks-http-app-routing]
- [Utwórz kontroler transferu danych przychodzących z dynamicznym publicznym adresem IP, a następnie skonfiguruj szyfrowanie, aby automatycznie generować certyfikaty TLS][aks-ingress-tls]
- [Utwórz kontroler transferu danych przychodzących ze statycznym publicznym adresem IP i skonfiguruj szyfrowanie, aby automatycznie generować certyfikaty TLS][aks-ingress-static-tls]

<!-- LINKS - external -->
[helm]: https://helm.sh/
[helm-cli]: ./kubernetes-helm.md
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx

<!-- LINKS - internal -->
[use-helm]: kubernetes-helm.md
[azure-cli-install]: /cli/azure/install-azure-cli
[aks-ingress-basic]: ingress-basic.md
[aks-ingress-tls]: ingress-tls.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-own-tls]: ingress-own-tls.md
[client-source-ip]: concepts-network.md#ingress-controllers
[aks-configure-kubenet-networking]: configure-kubenet.md
[aks-configure-advanced-networking]: configure-azure-cni.md