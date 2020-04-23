---
title: Używanie certyfikatów TLS na potrzeby ruchu przychodzącego
titleSuffix: Azure Kubernetes Service
description: Dowiedz się, jak zainstalować i skonfigurować kontroler NGINX Ingres, który używa własnych certyfikatów w klastrze usługi Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 05/24/2019
ms.openlocfilehash: 4c3edb40c6d0c9a64ce3cb01f665e8e9cf60d12e
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82100978"
---
# <a name="create-an-https-ingress-controller-and-use-your-own-tls-certificates-on-azure-kubernetes-service-aks"></a>Tworzenie kontrolera ruchu przychodzącego HTTPS i używanie własnych certyfikatów protokołu TLS w usłudze Azure Kubernetes Service (AKS)

Kontroler ruchu przychodzącego to element oprogramowania dostarczający odwrotny serwer proxy, konfigurowalne trasowanie ruchu oraz zakończenie protokołu TLS dla usług Kubernetes. Zasoby ruchu przychodzącego usług Kubernetes są używane do skonfigurowania zasad ruchu przychodzącego oraz tras dla poszczególnych usług Kubernetes. Dzięki korzystaniu z kontrolera ruchu przychodzącego oraz zasad ruchu przychodzącego można użyć jednego adresu IP do trasowania ruchu w wielu usługach w klastrze Kubernetes.

W tym artykule pokazano, jak wdrożyć [kontroler Nginx Ingress][nginx-ingress] w klastrze usługi Azure Kubernetes Service (AKS). Generowanie własnych certyfikatów i tworzenie wpisu tajnego Kubernetes do użycia z trasą transferu danych przychodzących. Na koniec dwie aplikacje są uruchamiane w klastrze AKS, z których każdy jest dostępny za pośrednictwem jednego adresu IP.

Możesz również wykonać następujące czynności:

- [Tworzenie podstawowego kontrolera danych wejściowych z łącznością sieci zewnętrznej][aks-ingress-basic]
- [Włącz dodatek routingu aplikacji protokołu HTTP][aks-http-app-routing]
- [Utwórz kontroler transferu danych przychodzących, który używa wewnętrznej, prywatnej sieci i adresu IP][aks-ingress-internal]
- Utwórz kontroler transferu danych przychodzących, który używa szyfrowania, aby automatycznie generować certyfikaty TLS [z dynamicznym publicznym adresem IP][aks-ingress-tls] lub [statycznym publicznym adresem IP][aks-ingress-static-tls]

## <a name="before-you-begin"></a>Przed rozpoczęciem

W tym artykule zastosowano [Helm 3][helm] do zainstalowania kontrolera transferu Nginx i przykładowej aplikacji sieci Web. Konieczne jest zainicjowanie Helm w ramach klastra AKS i użycie konta usługi dla tego elementu. Upewnij się, że korzystasz z najnowszej wersji programu Helm. Instrukcje dotyczące uaktualniania można znaleźć w dokumentacji [Helm Install][helm-install]. Aby uzyskać więcej informacji na temat konfigurowania i używania Helm, zobacz [Install Applications with Helm in Azure Kubernetes Service (AKS)][use-helm].

Ten artykuł wymaga również uruchomienia interfejsu wiersza polecenia platformy Azure w wersji 2.0.64 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli-install].

## <a name="create-an-ingress-controller"></a>Tworzenie kontrolera transferu danych przychodzących

Aby utworzyć kontroler transferu danych przychodzących, użyj `Helm` programu w celu zainstalowania *Nginx — ruch przychodzący*. W celu zwiększenia nadmiarowości za pomocą parametru `--set controller.replicaCount` wdrażane są dwie repliki kontrolerów wejściowych NGINX. Aby w pełni korzystać z uruchamiania replik kontrolera transferu danych przychodzących, upewnij się, że w klastrze AKS znajduje się więcej niż jeden węzeł.

Kontroler wejściowy należy również zaplanować w węźle z systemem Linux. W węzłach systemu Windows Server (obecnie w wersji zapoznawczej w AKS) nie należy uruchamiać kontrolera transferu danych przychodzących. Za pomocą parametru `--set nodeSelector` podaje się selektor węzła, który nakazuje harmonogramowi usługi Kubernetes uruchomienie kontrolera wejściowego NGINX w węźle opartym na systemie Linux.

> [!TIP]
> Poniższy przykład tworzy przestrzeń nazw Kubernetes dla zasobów przychodzących o nazwie transfery *-Basic*. W razie potrzeby określ przestrzeń nazw dla własnego środowiska. Jeśli klaster AKS nie jest włączony RBAC, Dodaj `--set rbac.create=false` do poleceń Helm.

> [!TIP]
> Jeśli chcesz włączyć [zachowywanie źródłowych adresów IP klienta][client-source-ip] dla żądań do kontenerów w klastrze, Dodaj `--set controller.service.externalTrafficPolicy=Local` do polecenia instalacji Helm. Adres IP źródła klienta jest przechowywany w nagłówku żądania w obszarze *X-forwardd-for*. W przypadku korzystania z kontrolera transferu danych przychodzących z włączonym zachowywaniem źródłowych adresów IP klienta przekazywanie protokołu SSL nie będzie działało.

```console
# Create a namespace for your ingress resources
kubectl create namespace ingress-basic

# Use Helm to deploy an NGINX ingress controller
helm install stable/nginx-ingress \
    --namespace ingress-basic \
    --set controller.replicaCount=2 \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux
```

Podczas instalacji jest tworzony publiczny adres IP platformy Azure dla kontrolera transferu danych przychodzących. Ten publiczny adres IP jest statyczny dla okresu istnienia kontrolera transferu danych przychodzących. Jeśli usuniesz kontroler transferu danych przychodzących, przypisanie publicznego adresu IP zostanie utracone. Jeśli następnie utworzysz dodatkowy kontroler transferu danych przychodzących, zostanie przypisany nowy publiczny adres IP. Jeśli chcesz zachować publiczny adres IP, możesz utworzyć kontroler transferu danych przychodzących [ze statycznym publicznym adresem IP][aks-ingress-static-tls].

Aby uzyskać publiczny adres IP, użyj `kubectl get service` polecenia. Przypisanie adresu IP do usługi może potrwać kilka minut.

```
$ kubectl get service -l app=nginx-ingress --namespace ingress-basic

NAME                                          TYPE           CLUSTER-IP    EXTERNAL-IP    PORT(S)                      AGE
virulent-seal-nginx-ingress-controller        LoadBalancer   10.0.48.240   40.87.46.190   80:31159/TCP,443:30657/TCP   7m
virulent-seal-nginx-ingress-default-backend   ClusterIP      10.0.50.5     <none>         80/TCP                       7m
```

Zanotuj ten publiczny adres IP, ponieważ jest on używany w ostatnim kroku do testowania wdrożenia.

Nie utworzono jeszcze żadnych reguł dotyczących transferu danych przychodzących. Jeśli przejdziesz do publicznego adresu IP, zostanie wyświetlona domyślna strona 404 kontrolera NGINX transferu danych przychodzących.

## <a name="generate-tls-certificates"></a>Generuj certyfikaty TLS

W tym artykule wygenerujemy certyfikat z podpisem własnym za pomocą `openssl`usługi. Do użycia w środowisku produkcyjnym należy zażądać certyfikatu zaufanego, podpisanego za pomocą dostawcy lub własnego urzędu certyfikacji. W następnym kroku zostanie wygenerowany *wpis tajny* Kubernetes przy użyciu certyfikatu TLS i klucza prywatnego wygenerowanego przez OpenSSL.

Poniższy przykład generuje 2048-bitowy certyfikat x509 certyfikatu RSA ważny przez 365 dni o nazwie *AKS-Ingress-TLS. CRT*. Plik klucza prywatnego ma nazwę *AKS-Ingress-TLS. Key*. Wpis tajny Kubernetes TLS wymaga obu tych plików.

Ten artykuł działa z wspólną nazwą podmiotu *demo.Azure.com* i nie trzeba go zmieniać. Do użycia w środowisku produkcyjnym Określ własne wartości organizacyjne `-subj` dla parametru:

```console
openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
    -out aks-ingress-tls.crt \
    -keyout aks-ingress-tls.key \
    -subj "/CN=demo.azure.com/O=aks-ingress-tls"
```

## <a name="create-kubernetes-secret-for-the-tls-certificate"></a>Tworzenie wpisu tajnego platformy Kubernetes dla certyfikatu protokołu TLS

Aby zezwolić Kubernetes na korzystanie z certyfikatu TLS i klucza prywatnego dla kontrolera transferu danych przychodzących, utworzysz i używasz klucza tajnego. Wpis tajny jest definiowany jednokrotnie i używa certyfikatu i pliku klucza utworzonego w poprzednim kroku. Następnie należy odwołać się do tego wpisu tajnego podczas definiowania tras przychodzących.

Poniższy przykład tworzy tajną nazwę *AKS-Ingres-TLS*:

```console
kubectl create secret tls aks-ingress-tls \
    --namespace ingress-basic \
    --key aks-ingress-tls.key \
    --cert aks-ingress-tls.crt
```

## <a name="run-demo-applications"></a>Uruchom aplikacje demonstracyjne

Skonfigurowano kontroler transferu danych przychodzących i klucz tajny z certyfikatem. Teraz Uruchommy dwie aplikacje demonstracyjne w klastrze AKS. W tym przykładzie Helm jest używany do wdrażania dwóch wystąpień prostej aplikacji "Hello World".

Przed zainstalowaniem przykładowych wykresów Helm należy dodać repozytorium przykładów platformy Azure do środowiska Helm w następujący sposób:

```console
helm repo add azure-samples https://azure-samples.github.io/helm-charts/
```

Utwórz pierwszą aplikację demonstracyjną z wykresu Helm za pomocą następującego polecenia:

```console
helm install azure-samples/aks-helloworld --namespace ingress-basic
```

Teraz Zainstaluj drugie wystąpienie aplikacji demonstracyjnej. Dla drugiego wystąpienia należy określić nowy tytuł, tak aby dwie aplikacje były wizualnie różne. Należy również określić unikatową nazwę usługi:

```console
helm install azure-samples/aks-helloworld \
    --namespace ingress-basic \
    --set title="AKS Ingress Demo" \
    --set serviceName="ingress-demo"
```

## <a name="create-an-ingress-route"></a>Tworzenie trasy transferu danych przychodzących

Obie aplikacje są teraz uruchomione w klastrze Kubernetes, ale są skonfigurowane za pomocą usługi typu `ClusterIP`. W związku z tym aplikacje nie są dostępne z Internetu. Aby udostępnić je publicznie, utwórz zasób Kubernetes. Zasób danych przychodzących konfiguruje reguły, które kierują ruch do jednej z dwóch aplikacji.

W poniższym przykładzie ruch do adresu `https://demo.azure.com/` jest kierowany do usługi o nazwie. `aks-helloworld` Ruch do adresu `https://demo.azure.com/hello-world-two` jest kierowany do `ingress-demo` usługi. W tym artykule nie trzeba zmieniać tych nazw demonstracyjnych hostów. W celu użycia w środowisku produkcyjnym Podaj nazwy określone jako część procesu żądania certyfikatu i generacji.

> [!TIP]
> Jeśli nazwa hosta określona w procesie żądania certyfikatu, Nazwa nazwy POSPOLITej nie jest zgodna z hostem zdefiniowanym w marszrucie transferu danych przychodzących, kontroler usług przychodzących wyświetla komunikat o *fałszywej certyfikacji kontrolera Kubernetes* . Upewnij się, że certyfikat i nazwy hosta trasy wejściowej są zgodne.

Sekcja *TLS* informuje trasę transferu danych przychodzących o użyciu wpisu tajnego o nazwie *AKS-* indemo.Azure.comd-TLS dla hosta *demo.azure.com*. W celu użycia w środowisku produkcyjnym Określ własny adres hosta.

Utwórz plik o nazwie `hello-world-ingress.yaml` i skopiuj w poniższym przykładzie YAML.

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  namespace: ingress-basic
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/rewrite-target: /$1
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
        path: /(.*)
      - backend:
          serviceName: ingress-demo
          servicePort: 80
        path: /hello-world-two(/|$)(.*)
```

Utwórz zasób transferu danych przychodzących przy użyciu `kubectl apply -f hello-world-ingress.yaml` polecenia.

```
$ kubectl apply -f hello-world-ingress.yaml

ingress.extensions/hello-world-ingress created
```

## <a name="test-the-ingress-configuration"></a>Testowanie konfiguracji transferu danych przychodzących

Aby przetestować certyfikaty przy użyciu naszego *demo.Azure.comego* hosta, użyj `curl` i określ parametr *--Rozwiązuj* . Ten parametr umożliwia zamapowanie nazwy *demo.Azure.com* na publiczny adres IP kontrolera transferu danych przychodzących. Określ publiczny adres IP własnego kontrolera transferu danych przychodzących, jak pokazano w następującym przykładzie:

```
curl -v -k --resolve demo.azure.com:443:40.87.46.190 https://demo.azure.com
```

Nie dostarczono żadnej dodatkowej ścieżki z adresem, dlatego kontroler transferu danych przychodzących jest domyślnie */* kierowany do trasy. Zostanie zwrócona pierwsza aplikacja demonstracyjna, jak pokazano w następujących wąskich przykładowych danych wyjściowych:

```
$ curl -v -k --resolve demo.azure.com:443:40.87.46.190 https://demo.azure.com

[...]
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <link rel="stylesheet" type="text/css" href="/static/default.css">
    <title>Welcome to Azure Kubernetes Service (AKS)</title>
[...]
```

Parametr *-v* w naszym `curl` poleceniu zapisuje pełne informacje, w tym otrzymany certyfikat TLS. Dane wyjściowe zakupionego, można sprawdzić, czy został użyty własny certyfikat TLS. Parametr *-k* kontynuuje ładowanie strony, mimo że używany jest certyfikat z podpisem własnym. Poniższy przykład pokazuje, że *wystawca: CN = Demonstracja. Azure. com; O = AKS-ruch przychodzący —* użyto certyfikatu TLS:

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

Teraz dodaj ścieżkę */Hello-World-Two* do adresu, na przykład `https://demo.azure.com/hello-world-two`. Zostanie zwrócona druga aplikacja demonstracyjna z tytułem niestandardowym, jak pokazano w następujących wąskich przykładowych danych wyjściowych:

```
$ curl -v -k --resolve demo.azure.com:443:137.117.36.18 https://demo.azure.com/hello-world-two

[...]
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <link rel="stylesheet" type="text/css" href="/static/default.css">
    <title>AKS Ingress Demo</title>
[...]
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

W tym artykule użyto Helm do zainstalowania składników przychodzących i przykładowych aplikacji. Po wdrożeniu wykresu Helm są tworzone różne zasoby Kubernetes. Do tych zasobów należą między innymi: zasoby, wdrożenia i usługi. Aby wyczyścić te zasoby, można usunąć całą przykładową przestrzeń nazw lub poszczególne zasoby.

### <a name="delete-the-sample-namespace-and-all-resources"></a>Usuń przykładową przestrzeń nazw i wszystkie zasoby

Aby usunąć całą przykładową przestrzeń nazw, użyj `kubectl delete` polecenia i określ nazwę przestrzeni nazw. Wszystkie zasoby w przestrzeni nazw są usuwane.

```console
kubectl delete namespace ingress-basic
```

Następnie usuń repozytorium Helm dla aplikacji AKS Hello World:

```console
helm repo remove azure-samples
```

### <a name="delete-resources-individually"></a>Usuń zasoby pojedynczo

Alternatywnie, bardziej szczegółowe podejście polega na usunięciu utworzonych poszczególnych zasobów. Utwórz listę wersji Helm za pomocą `helm list` polecenia. Poszukaj wykresów o nazwie *Nginx-Ingress* i *AKS-HelloWorld*, jak pokazano w następujących przykładowych danych wyjściowych:

```
$ helm list

NAME            REVISION    UPDATED                     STATUS      CHART                   APP VERSION NAMESPACE
virulent-seal   1           Tue Oct 23 16:37:24 2018    DEPLOYED    nginx-ingress-0.22.1    0.15.0      kube-system
billowing-guppy 1           Tue Oct 23 16:41:38 2018    DEPLOYED    aks-helloworld-0.1.0                default
listless-quokka 1           Tue Oct 23 16:41:30 2018    DEPLOYED    aks-helloworld-0.1.0                default
```

Usuń wydania za pomocą `helm delete` polecenia. Poniższy przykład usuwa wdrożenie NGINX, a dwa przykładowe aplikacje AKS Hello World.

```
$ helm delete virulent-seal billowing-guppy listless-quokka

release "virulent-seal" deleted
release "billowing-guppy" deleted
release "listless-quokka" deleted
```

Następnie usuń repozytorium Helm dla aplikacji AKS Hello World:

```console
helm repo remove azure-samples
```

Usuń trasę transferu danych przychodzących, która kieruje ruch do aplikacji przykładowych:

```console
kubectl delete -f hello-world-ingress.yaml
```

Usuń klucz tajny certyfikatu:

```console
kubectl delete secret aks-ingress-tls
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
- [Utwórz kontroler transferu danych przychodzących, który używa wewnętrznej, prywatnej sieci i adresu IP][aks-ingress-internal]
- Utwórz kontroler transferu danych przychodzących, który używa szyfrowania, aby automatycznie generować certyfikaty TLS [z dynamicznym publicznym adresem IP][aks-ingress-tls] lub [statycznym publicznym adresem IP][aks-ingress-static-tls]

<!-- LINKS - external -->
[helm-cli]: https://docs.microsoft.com/azure/aks/kubernetes-helm
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx
[helm]: https://helm.sh/
[helm-install]: https://docs.helm.sh/using_helm/#installing-helm

<!-- LINKS - internal -->
[use-helm]: kubernetes-helm.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[aks-ingress-internal]: ingress-internal-ip.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-ingress-basic]: ingress-basic.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-tls]: ingress-tls.md
[client-source-ip]: concepts-network.md#ingress-controllers
