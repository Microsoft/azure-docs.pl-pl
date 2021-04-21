---
title: Używanie certyfikatów TLS dla danych przychodzących
titleSuffix: Azure Kubernetes Service
description: Dowiedz się, jak zainstalować i skonfigurować kontroler ruchu przychodzących NGINX, który używa własnych certyfikatów w klastrze Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 08/17/2020
ms.openlocfilehash: d3d554fbbbbb16910b76e7eb56afd13c436f8cf5
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107779943"
---
# <a name="create-an-https-ingress-controller-and-use-your-own-tls-certificates-on-azure-kubernetes-service-aks"></a>Tworzenie kontrolera ruchu przychodzącego HTTPS i używanie własnych certyfikatów protokołu TLS w usłudze Azure Kubernetes Service (AKS)

Kontroler ruchu przychodzącego to element oprogramowania dostarczający odwrotny serwer proxy, konfigurowalne trasowanie ruchu oraz zakończenie protokołu TLS dla usług Kubernetes. Zasoby ruchu przychodzącego usług Kubernetes są używane do skonfigurowania zasad ruchu przychodzącego oraz tras dla poszczególnych usług Kubernetes. Dzięki korzystaniu z kontrolera ruchu przychodzącego oraz zasad ruchu przychodzącego można użyć jednego adresu IP do trasowania ruchu w wielu usługach w klastrze Kubernetes.

W tym artykule pokazano, jak wdrożyć kontroler ruchu przychodzących [NGINX][nginx-ingress] w klastrze Azure Kubernetes Service (AKS). Wygeneruj własne certyfikaty i utwórz klucz tajny Kubernetes do użycia z trasą ruchu wychodzącego. Na koniec dwie aplikacje są uruchamiane w klastrze usługi AKS, z których każda jest dostępna za pośrednictwem jednego adresu IP.

Możesz również wykonać następujące czynności:

- [Tworzenie podstawowego kontrolera ruchu wychodzącego z zewnętrzną łącznością sieciową][aks-ingress-basic]
- [Włączanie dodatku routingu aplikacji HTTP][aks-http-app-routing]
- [Tworzenie kontrolera ruchu wychodzącego, który używa wewnętrznej, prywatnej sieci i adresu IP][aks-ingress-internal]
- Tworzenie kontrolera ruchu wychodzącego, który używa let's encrypt do automatycznego generowania certyfikatów TLS z dynamicznym publicznym adresem [IP][aks-ingress-tls] lub [statycznym publicznym adresem IP][aks-ingress-static-tls]

## <a name="before-you-begin"></a>Zanim rozpoczniesz

W tym artykule do zainstalowania kontrolera danych przychodzących NGINX jest używany program [Helm 3.][helm] Upewnij się, że używasz najnowszej wersji programu Helm i masz dostęp do repozytorium programu Helm *ingress-nginx.* Aby uzyskać instrukcje dotyczące uaktualniania, zobacz dokumenty [dotyczące instalacji programu Helm.][helm-install] Aby uzyskać więcej informacji na temat konfigurowania i używania programu Helm, zobacz Instalowanie aplikacji za pomocą programu Helm w [Azure Kubernetes Service (AKS).][use-helm]

Ten artykuł wymaga również interfejsu wiersza polecenia platformy Azure w wersji 2.0.64 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli-install].

## <a name="create-an-ingress-controller"></a>Tworzenie kontrolera ruchu wychodzącego

Aby utworzyć kontroler ruchu przychodzących, użyj programu `Helm` , aby zainstalować pakiet *nginx-ingress.* W celu zwiększenia nadmiarowości za pomocą parametru `--set controller.replicaCount` wdrażane są dwie repliki kontrolerów wejściowych NGINX. Aby w pełni korzystać z uruchomionych replik kontrolera ruchu wychodzącego, upewnij się, że w klastrze usługi AKS znajduje się więcej niż jeden węzeł.

Kontroler wejściowy należy również zaplanować w węźle z systemem Linux. Nie należy go uruchamiać w węzłach z systemem Windows Server. Za pomocą parametru `--set nodeSelector` podaje się selektor węzła, który nakazuje harmonogramowi usługi Kubernetes uruchomienie kontrolera wejściowego NGINX w węźle opartym na systemie Linux.

> [!TIP]
> Poniższy przykład tworzy przestrzeń nazw Kubernetes dla zasobów przychodzących o *nazwie ingress-basic*. Określ przestrzeń nazw dla własnego środowiska zgodnie z potrzebami. Jeśli klaster usługi AKS nie ma włączonej kontroli RBAC na platformie Kubernetes, `--set rbac.create=false` dodaj polecenie do poleceń programu Helm.

> [!TIP]
> Jeśli chcesz włączyć zachowywanie źródłowego [adresu IP klienta dla][client-source-ip] żądań do kontenerów w klastrze, dodaj polecenie do polecenia instalacji programu `--set controller.service.externalTrafficPolicy=Local` Helm. Źródłowy adres IP klienta jest przechowywany w nagłówku żądania w obszarze *X-Forwarded-For*. W przypadku korzystania z kontrolera ruchu wychodzącego z włączonym zachowaniem źródłowego adresu IP klienta, pass-through protokołu TLS nie będzie działać.

```console
# Create a namespace for your ingress resources
kubectl create namespace ingress-basic

# Add the ingress-nginx repository
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx

# Use Helm to deploy an NGINX ingress controller
helm install nginx-ingress ingress-nginx/ingress-nginx \
    --namespace ingress-basic \
    --set controller.replicaCount=2 \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set controller.admissionWebhooks.patch.nodeSelector."beta\.kubernetes\.io/os"=linux
```

Podczas instalacji dla kontrolera ruchu wychodzącego jest tworzony publiczny adres IP platformy Azure. Ten publiczny adres IP jest statyczny dla okresu życia kontrolera ruchu wychodzącego. Jeśli usuniesz kontroler ruchu wychodzącego, przypisanie publicznego adresu IP zostanie utracone. Jeśli następnie utworzysz dodatkowy kontroler ruchu wychodzącego, zostanie przypisany nowy publiczny adres IP. Jeśli chcesz zachować użycie publicznego adresu IP, możesz zamiast tego utworzyć kontroler ruchu wychodzącego ze statycznym [publicznym adresem IP][aks-ingress-static-tls].

Aby uzyskać publiczny adres IP, użyj `kubectl get service` polecenia .

```console
kubectl --namespace ingress-basic get services -o wide -w nginx-ingress-ingress-nginx-controller
```

Przypisanie adresu IP do usługi może potrwać kilka minut.

```
$ kubectl --namespace ingress-basic get services -o wide -w nginx-ingress-ingress-nginx-controller

NAME                                     TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)                      AGE   SELECTOR
nginx-ingress-ingress-nginx-controller   LoadBalancer   10.0.74.133   EXTERNAL_IP     80:32486/TCP,443:30953/TCP   44s   app.kubernetes.io/component=controller,app.kubernetes.io/instance=nginx-ingress,app.kubernetes.io/name=ingress-nginx
```

Zanotuj ten publiczny adres IP, ponieważ jest on używany w ostatnim kroku do testowania wdrożenia.

Nie utworzono jeszcze żadnych reguł ruchu wychodzącego. W przypadku przeglądania do publicznego adresu IP zostanie wyświetlona domyślna strona 404 kontrolera ruchu przychodzących NGINX.

## <a name="generate-tls-certificates"></a>Generowanie certyfikatów TLS

W tym artykule wygenerujmy certyfikat z podpisem własnym za `openssl` pomocą . Do użytku produkcyjnego należy zażądać zaufanego certyfikatu podpisanego za pośrednictwem dostawcy lub własnego urzędu certyfikacji. W następnym kroku wygenerujesz klucz tajny Kubernetes *przy* użyciu certyfikatu TLS i klucza prywatnego wygenerowanego przez program OpenSSL.

Poniższy przykład generuje 2048-bitowy certyfikat RSA X509 ważny przez 365 dni o nazwie *aks-ingress-tls.crt.* Plik klucza prywatnego ma nazwę *aks-ingress-tls.key.* Klucz tajny TLS kubernetes wymaga obu tych plików.

Ten artykuł współpracuje z *demo.azure.com* pospolitą podmiotu i nie trzeba go zmieniać. W przypadku użycia w środowisku produkcyjnym określ własne wartości organizacyjne dla `-subj` parametru :

```console
openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
    -out aks-ingress-tls.crt \
    -keyout aks-ingress-tls.key \
    -subj "/CN=demo.azure.com/O=aks-ingress-tls"
```

## <a name="create-kubernetes-secret-for-the-tls-certificate"></a>Tworzenie wpisu tajnego platformy Kubernetes dla certyfikatu protokołu TLS

Aby umożliwić platformie Kubernetes używanie certyfikatu TLS i klucza prywatnego dla kontrolera ruchu wychodzącego, należy utworzyć klucz tajny i użyć go. Klucz tajny jest definiowany raz i używa pliku certyfikatu i klucza utworzonego w poprzednim kroku. Następnie przy definiowaniu tras ruchu wychodzącego należy odwołać się do tego tajnego.

Poniższy przykład tworzy nazwę *tajnego aks-ingress-tls:*

```console
kubectl create secret tls aks-ingress-tls \
    --namespace ingress-basic \
    --key aks-ingress-tls.key \
    --cert aks-ingress-tls.crt
```

## <a name="run-demo-applications"></a>Uruchamianie aplikacji demonstracyjnych

Skonfigurowano kontroler ruchu wychodzącego i klucz tajny z certyfikatem. Teraz uruchommy dwie demonstracyjne aplikacje w klastrze usługi AKS. W tym przykładzie program Helm służy do wdrażania dwóch wystąpień prostej aplikacji "Hello world".

Aby zobaczyć działanie kontrolera ruchu wychodzącego, uruchom dwie aplikacje demonstracyjne w klastrze usługi AKS. W tym przykładzie użyjemy `kubectl apply` funkcji , aby wdrożyć dwa wystąpienia prostej aplikacji *Hello* world.

Utwórz plik *aks-helloworld.yaml* i skopiuj go do następującego przykładowego pliku YAML:

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

Utwórz plik *ingress-demo.yaml* i skopiuj go do następującego przykładowego pliku YAML:

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

Uruchom dwie aplikacje demonstracyjne przy użyciu narzędzia `kubectl apply` :

```console
kubectl apply -f aks-helloworld.yaml --namespace ingress-basic
kubectl apply -f ingress-demo.yaml --namespace ingress-basic
```

## <a name="create-an-ingress-route"></a>Tworzenie trasy ruchu wychodzącego

Obie aplikacje są teraz uruchomione w klastrze Kubernetes, jednak są one skonfigurowane przy użyciu usługi typu `ClusterIP` . W związku z tym aplikacje nie są dostępne z Internetu. Aby udostępnić je publicznie, utwórz zasób danych przychodzących Kubernetes. Zasób ruchu przychodzących konfiguruje reguły, które przekierowywuje ruch do jednej z dwóch aplikacji.

W poniższym przykładzie ruch do adresu `https://demo.azure.com/` jest przekierowyny do usługi o nazwie `aks-helloworld` . Ruch do adresu `https://demo.azure.com/hello-world-two` jest przekierowyny do `ingress-demo` usługi. W tym artykule nie trzeba zmieniać nazw hostów pokazów. Do użytku produkcyjnego podaj nazwy określone w ramach procesu generowania i żądania certyfikatu.

> [!TIP]
> Jeśli nazwa hosta określona podczas procesu żądania certyfikatu, nazwa nazwy JN, nie jest dopasowana do hosta zdefiniowanego w trasie ruchu wychodzącego, kontroler ruchu wychodzącego wyświetla ostrzeżenie Fałszywy certyfikat kontrolera ruchu wychodzącego *Kubernetes.* Upewnij się, że nazwy hostów tras ruchu wychodzącego i certyfikatu są zgodne.

Sekcja *TLS informuje* trasę ruchu wychodzącego o użyciu tajnego kluczu o nazwie *aks-ingress-tls* dla hosta *demo.azure.com*. Ponownie, w przypadku użycia w środowisku produkcyjnym, określ własny adres hosta.

Utwórz plik o nazwie `hello-world-ingress.yaml` i skopiuj go w poniższym przykładzie YAML.

```yaml
apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  namespace: ingress-basic
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/use-regex: "true"
    nginx.ingress.kubernetes.io/rewrite-target: /$2
spec:
  tls:
  - hosts:
    - demo.azure.com
    secretName: aks-ingress-tls
  rules:
  - host: demo.azure.com
    http:
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

Utwórz zasób danych przychodzących przy użyciu `kubectl apply -f hello-world-ingress.yaml` polecenia .

```console
kubectl apply -f hello-world-ingress.yaml
```

Przykładowe dane wyjściowe pokazują, że zasób przychodzący został utworzony.

```
$ kubectl apply -f hello-world-ingress.yaml

ingress.extensions/hello-world-ingress created
```

## <a name="test-the-ingress-configuration"></a>Testowanie konfiguracji danych przychodzących

Aby przetestować certyfikaty za pomocą naszego fałszywego *demo.azure.com* hosta, użyj i `curl` określ parametr *--resolve.* Ten parametr umożliwia mapowanie *demo.azure.com* na publiczny adres IP kontrolera ruchu wychodzącego. Określ publiczny adres IP własnego kontrolera ruchu przychodzących, jak pokazano w poniższym przykładzie:

```
curl -v -k --resolve demo.azure.com:443:EXTERNAL_IP https://demo.azure.com
```

Nie podano dodatkowej ścieżki z adresem , więc kontroler ruchu wychodzącego domyślnie ma wartość */* trasę. Zwracana jest pierwsza aplikacja demonstracyjna, jak pokazano w poniższych skróconych przykładowych danych wyjściowych:

```
$ curl -v -k --resolve demo.azure.com:443:EXTERNAL_IP https://demo.azure.com

[...]
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <link rel="stylesheet" type="text/css" href="/static/default.css">
    <title>Welcome to Azure Kubernetes Service (AKS)</title>
[...]
```

Parametr *-v* w naszym `curl` poleceniu zawiera pełne informacje, w tym otrzymany certyfikat TLS. W połowie danych wyjściowych curl możesz sprawdzić, czy twój certyfikat TLS został użyty. Parametr *-k* kontynuuje ładowanie strony, mimo że używamy certyfikatu z podpisem własnym. W poniższym przykładzie pokazano, że *wystawca: CN=demo.azure.com; Udano certyfikat O=aks-ingress-tls:*

```
[...]
* Server certificate:
*  subject: CN=demo.azure.com; O=aks-ingress-tls
*  start date: Oct 22 22:13:54 2018 GMT
*  expire date: Oct 22 22:13:54 2019 GMT
*  issuer: CN=demo.azure.com; O=aks-ingress-tls
*  SSL certificate verify result: self signed certificate (18), continuing anyway.
[...]
```

Teraz dodaj *do adresu ścieżkę /hello-world-two,* taką jak `https://demo.azure.com/hello-world-two` . Zostanie zwrócona druga aplikacja demonstracyjna z niestandardowym tytułem, jak pokazano w poniższych skróconych przykładowych danych wyjściowych:

```
$ curl -v -k --resolve demo.azure.com:443:EXTERNAL_IP https://demo.azure.com/hello-world-two

[...]
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <link rel="stylesheet" type="text/css" href="/static/default.css">
    <title>AKS Ingress Demo</title>
[...]
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

W tym artykule program Helm został użyty do zainstalowania składników przychodzących i przykładowych aplikacji. Podczas wdrażania planu helm jest tworzona liczba zasobów kubernetes. Te zasoby obejmują zasobniki, wdrożenia i usługi. Aby wyczyścić te zasoby, możesz usunąć całą przykładową przestrzeń nazw lub poszczególne zasoby.

### <a name="delete-the-sample-namespace-and-all-resources"></a>Usuwanie przykładowej przestrzeni nazw i wszystkich zasobów

Aby usunąć całą przykładową przestrzeń nazw, użyj `kubectl delete` polecenia i określ nazwę przestrzeni nazw. Wszystkie zasoby w przestrzeni nazw zostaną usunięte.

```console
kubectl delete namespace ingress-basic
```

### <a name="delete-resources-individually"></a>Pojedyncze usuwanie zasobów

Alternatywnie bardziej szczegółowym podejściem jest usunięcie poszczególnych utworzonych zasobów. Lista wydań helm za pomocą `helm list` polecenia . 

```console
helm list --namespace ingress-basic
```

Poszukaj wykresu o *nazwie nginx-ingress,* jak pokazano w następujących przykładowych danych wyjściowych:

```
$ helm list --namespace ingress-basic

NAME                    NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                   APP VERSION
nginx-ingress           ingress-basic   1               2020-01-06 19:55:46.358275 -0600 CST    deployed        nginx-ingress-1.27.1    0.26.1 
```

Odinstaluj wydania za pomocą `helm uninstall` polecenia . 

```console
helm uninstall nginx-ingress --namespace ingress-basic
```

Poniższy przykład odinstalowuje wdrożenie danych przychodzących NGINX.

```
$ helm uninstall nginx-ingress --namespace ingress-basic

release "nginx-ingress" uninstalled
```

Następnie usuń dwie przykładowe aplikacje:

```console
kubectl delete -f aks-helloworld.yaml --namespace ingress-basic
kubectl delete -f ingress-demo.yaml --namespace ingress-basic
```

Usuń trasę ruchu wychodzącego, która kierowała ruch do przykładowych aplikacji:

```console
kubectl delete -f hello-world-ingress.yaml
```

Usuń klucz tajny certyfikatu:

```console
kubectl delete secret aks-ingress-tls --namespace ingress-basic
```

Na koniec możesz usunąć samą przestrzeń nazw. Użyj polecenia `kubectl delete` i określ nazwę przestrzeni nazw:

```console
kubectl delete namespace ingress-basic
```

## <a name="next-steps"></a>Następne kroki

W tym artykule uwzględniono niektóre składniki zewnętrzne usługi AKS. Aby dowiedzieć się więcej na temat tych składników, zobacz następujące strony projektu:

- [Interfejs wiersza polecenia helm][helm-cli]
- [Kontroler ruchu przychodzących NGINX][nginx-ingress]

Możesz również wykonać następujące czynności:

- [Tworzenie podstawowego kontrolera ruchu wychodzącego z zewnętrzną łącznością sieciową][aks-ingress-basic]
- [Włączanie dodatku routingu aplikacji HTTP][aks-http-app-routing]
- [Tworzenie kontrolera ruchu wychodzącego, który używa wewnętrznej, prywatnej sieci i adresu IP][aks-ingress-internal]
- Tworzenie kontrolera ruchu wychodzącego, który używa let's encrypt do automatycznego generowania certyfikatów TLS z dynamicznym publicznym adresem [IP][aks-ingress-tls] lub [statycznym publicznym adresem IP][aks-ingress-static-tls]

<!-- LINKS - external -->
[helm-cli]: ./kubernetes-helm.md
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx
[helm]: https://helm.sh/
[helm-install]: https://docs.helm.sh/using_helm/#installing-helm

<!-- LINKS - internal -->
[use-helm]: kubernetes-helm.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az_aks_show
[az-network-public-ip-create]: /cli/azure/network/public-ip#az_network_public_ip_create
[aks-ingress-internal]: ingress-internal-ip.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-ingress-basic]: ingress-basic.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-tls]: ingress-tls.md
[client-source-ip]: concepts-network.md#ingress-controllers
