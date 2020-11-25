---
title: Tworzenie ruchu przychodzącego z automatycznym protokołem TLS
titleSuffix: Azure Kubernetes Service
description: Dowiedz się, jak zainstalować i skonfigurować kontroler protokołu przychodzącego NGINX, który używa szyfrowania do automatycznej generacji certyfikatów TLS w klastrze usługi Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 08/17/2020
ms.openlocfilehash: 5032880ddc5d23f824adec28aee85c652bad29d2
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95993165"
---
# <a name="create-an-https-ingress-controller-on-azure-kubernetes-service-aks"></a>Tworzenie kontrolera protokołu HTTPS w usłudze Azure Kubernetes Service (AKS)

Kontroler ruchu przychodzącego to element oprogramowania dostarczający odwrotny serwer proxy, konfigurowalne trasowanie ruchu oraz zakończenie protokołu TLS dla usług Kubernetes. Zasoby ruchu przychodzącego usług Kubernetes są używane do skonfigurowania zasad ruchu przychodzącego oraz tras dla poszczególnych usług Kubernetes. Dzięki korzystaniu z kontrolera ruchu przychodzącego oraz zasad ruchu przychodzącego można użyć jednego adresu IP do trasowania ruchu w wielu usługach w klastrze Kubernetes.

W tym artykule pokazano, jak wdrożyć [kontroler Nginx Ingress][nginx-ingress] w klastrze usługi Azure Kubernetes Service (AKS). Projekt [Menedżera certyfikatów][cert-manager] służy do automatycznego generowania i konfigurowania certyfikatów [szyfrowania][lets-encrypt] . Na koniec dwie aplikacje są uruchamiane w klastrze AKS, z których każdy jest dostępny za pośrednictwem jednego adresu IP.

Możesz również wykonać następujące czynności:

- [Tworzenie podstawowego kontrolera danych wejściowych z łącznością sieci zewnętrznej][aks-ingress-basic]
- [Włącz dodatek routingu aplikacji protokołu HTTP][aks-http-app-routing]
- [Utwórz kontroler transferu danych przychodzących, który używa wewnętrznej, prywatnej sieci i adresu IP][aks-ingress-internal]
- [Tworzenie kontrolera transferu danych przychodzących korzystającego z własnych certyfikatów TLS][aks-ingress-own-tls]
- [Utwórz kontroler transferu danych przychodzących, który używa szyfrowania, aby automatycznie generować certyfikaty TLS ze statycznym publicznym adresem IP][aks-ingress-static-tls]

## <a name="before-you-begin"></a>Zanim rozpoczniesz

W tym artykule przyjęto założenie, że masz istniejący klaster AKS. Jeśli potrzebujesz klastra AKS, zapoznaj się z przewodnikiem Szybki Start AKS [przy użyciu interfejsu wiersza polecenia platformy Azure][aks-quickstart-cli] lub [przy użyciu Azure Portal][aks-quickstart-portal].

W tym artykule założono również, że masz [domenę niestandardową][custom-domain] ze [strefą DNS][dns-zone] w tej samej grupie zasobów co klaster AKS.

W tym artykule zastosowano [Helm 3][helm] do zainstalowania kontrolera transferu danych przychodzących Nginx i Menedżera certyfikatów. Upewnij się, że używasz najnowszej wersji programu Helm i masz dostęp do repozytoriów *Nginx* i *jetstack* Helm. Instrukcje dotyczące uaktualniania można znaleźć w dokumentacji [Helm Install][helm-install]. Aby uzyskać więcej informacji na temat konfigurowania i używania Helm, zobacz [Install Applications with Helm in Azure Kubernetes Service (AKS)][use-helm].

Ten artykuł wymaga również uruchomienia interfejsu wiersza polecenia platformy Azure w wersji 2.0.64 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli-install].

## <a name="create-an-ingress-controller"></a>Tworzenie kontrolera transferu danych przychodzących

Aby utworzyć kontroler transferu danych przychodzących, użyj polecenia, `helm` Aby zainstalować *Nginx — ruch przychodzący*. W celu zwiększenia nadmiarowości za pomocą parametru `--set controller.replicaCount` wdrażane są dwie repliki kontrolerów wejściowych NGINX. Aby w pełni korzystać z uruchamiania replik kontrolera transferu danych przychodzących, upewnij się, że w klastrze AKS znajduje się więcej niż jeden węzeł.

Kontroler wejściowy należy również zaplanować w węźle z systemem Linux. Nie należy go uruchamiać w węzłach z systemem Windows Server. Za pomocą parametru `--set nodeSelector` podaje się selektor węzła, który nakazuje harmonogramowi usługi Kubernetes uruchomienie kontrolera wejściowego NGINX w węźle opartym na systemie Linux.

> [!TIP]
> Poniższy przykład tworzy przestrzeń nazw Kubernetes dla zasobów przychodzących o nazwie transfery *-Basic*. W razie potrzeby określ przestrzeń nazw dla własnego środowiska.

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
    --set controller.replicaCount=2 \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux
```

Podczas instalacji jest tworzony publiczny adres IP platformy Azure dla kontrolera transferu danych przychodzących. Ten publiczny adres IP jest statyczny dla okresu istnienia kontrolera transferu danych przychodzących. Jeśli usuniesz kontroler transferu danych przychodzących, przypisanie publicznego adresu IP zostanie utracone. Jeśli następnie utworzysz dodatkowy kontroler transferu danych przychodzących, zostanie przypisany nowy publiczny adres IP. Jeśli chcesz zachować publiczny adres IP, możesz utworzyć kontroler transferu danych przychodzących [ze statycznym publicznym adresem IP][aks-ingress-static-tls].

Aby uzyskać publiczny adres IP, użyj `kubectl get service` polecenia. Przypisanie adresu IP do usługi może potrwać kilka minut.

```
$ kubectl --namespace ingress-basic get services -o wide -w nginx-ingress-ingress-nginx-controller

NAME                                     TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)                      AGE   SELECTOR
nginx-ingress-ingress-nginx-controller   LoadBalancer   10.0.74.133   EXTERNAL_IP     80:32486/TCP,443:30953/TCP   44s   app.kubernetes.io/component=controller,app.kubernetes.io/instance=nginx-ingress,app.kubernetes.io/name=ingress-nginx
```

Nie utworzono jeszcze żadnych reguł dotyczących transferu danych przychodzących. Jeśli przejdziesz do publicznego adresu IP, zostanie wyświetlona domyślna strona 404 kontrolera NGINX transferu danych przychodzących.

## <a name="add-an-a-record-to-your-dns-zone"></a>Dodawanie rekordu A do strefy DNS

Dodawanie rekordu *A* do strefy DNS z zewnętrznym adresem IP usługi Nginx przy użyciu polecenia [AZ Network DNS record-Set A Add-Record][az-network-dns-record-set-a-add-record].

```console
az network dns record-set a add-record \
    --resource-group myResourceGroup \
    --zone-name MY_CUSTOM_DOMAIN \
    --record-set-name * \
    --ipv4-address MY_EXTERNAL_IP
```

> [!NOTE]
> Opcjonalnie można skonfigurować nazwę FQDN dla adresu IP kontrolera transferu danych przychodzących zamiast domeny niestandardowej. Należy zauważyć, że ten przykład dotyczy powłoki bash.
> 
> ```azurecli-interactive
> # Public IP address of your ingress controller
> IP="MY_EXTERNAL_IP"
> 
> # Name to associate with public IP address
> DNSNAME="demo-aks-ingress"
> 
> # Get the resource-id of the public ip
> PUBLICIPID=$(az network public-ip list --query "[?ipAddress!=null]|[?contains(ipAddress, '$IP')].[id]" --output tsv)
> 
> # Update public ip address with DNS name
> az network public-ip update --ids $PUBLICIPID --dns-name $DNSNAME
> 
> # Display the FQDN
> az network public-ip show --ids $PUBLICIPID --query "[dnsSettings.fqdn]" --output tsv
> ```

## <a name="install-cert-manager"></a>Instalowanie narzędzia cert-manager

Kontroler ruchu przychodzącego NGINX obsługuje kończenie żądań protokołu TLS. Istnieje kilka sposobów pobierania i konfigurowania certyfikatów protokołu HTTPS. W tym artykule pokazano, jak korzystać z [Menedżera certyfikatów][cert-manager], który umożliwia automatyczne [szyfrowanie][lets-encrypt] funkcji generowania certyfikatów i zarządzania nimi.

Aby zainstalować kontroler Menedżera certyfikatów:

```console
# Label the ingress-basic namespace to disable resource validation
kubectl label namespace ingress-basic cert-manager.io/disable-validation=true

# Add the Jetstack Helm repository
helm repo add jetstack https://charts.jetstack.io

# Update your local Helm chart repository cache
helm repo update

# Install the cert-manager Helm chart
helm install \
  cert-manager \
  --namespace ingress-basic \
  --version v0.16.1 \
  --set installCRDs=true \
  --set nodeSelector."beta\.kubernetes\.io/os"=linux \
  jetstack/cert-manager
```

Aby uzyskać więcej informacji na temat konfiguracji Menedżera certyfikatów, zobacz [projekt Menedżera certyfikatów][cert-manager].

## <a name="create-a-ca-cluster-issuer"></a>Tworzenie wystawcy klastra urzędu certyfikacji

Aby można było wystawiać certyfikaty, Menedżer certyfikatów wymaga zasobu [wystawcy][cert-manager-issuer] lub [ClusterIssuer][cert-manager-cluster-issuer] . Te zasoby Kubernetes są identyczne w ramach funkcjonalności, jednak `Issuer` działają w pojedynczej przestrzeni nazw i `ClusterIssuer` działają we wszystkich przestrzeniach nazw. Aby uzyskać więcej informacji, zobacz dokumentację [wystawcy Menedżera certyfikatów][cert-manager-issuer] .

Utwórz wystawcę klastra, na przykład `cluster-issuer.yaml` korzystając z następującego przykładowego manifestu. Zaktualizuj adres e-mail przy użyciu prawidłowego adresu z Twojej organizacji:

```yaml
apiVersion: cert-manager.io/v1alpha2
kind: ClusterIssuer
metadata:
  name: letsencrypt
spec:
  acme:
    server: https://acme-v02.api.letsencrypt.org/directory
    email: MY_EMAIL_ADDRESS
    privateKeySecretRef:
      name: letsencrypt
    solvers:
    - http01:
        ingress:
          class: nginx
          podTemplate:
            spec:
              nodeSelector:
                "kubernetes.io/os": linux
```

Aby utworzyć wystawcę, użyj `kubectl apply` polecenia.

```console
kubectl apply -f cluster-issuer.yaml
```

## <a name="run-demo-applications"></a>Uruchom aplikacje demonstracyjne

Skonfigurowano kontroler transferu danych przychodzących i rozwiązanie do zarządzania certyfikatami. Teraz Uruchommy dwie aplikacje demonstracyjne w klastrze AKS. W tym przykładzie Helm jest używany do wdrażania dwóch wystąpień prostej aplikacji *Hello World* .

Aby wyświetlić kontroler transferu danych przychodzących w działaniu, uruchom dwie aplikacje demonstracyjne w klastrze AKS. W tym przykładzie użyto `kubectl apply` do wdrożenia dwóch wystąpień prostej aplikacji *Hello World* .

Utwórz plik *AKS-HelloWorld-one. YAML* i skopiuj go w poniższym przykładzie YAML:

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: aks-helloworld-one
spec:
  replicas: 1
  selector:
    matchLabels:
      app: aks-helloworld-one
  template:
    metadata:
      labels:
        app: aks-helloworld-one
    spec:
      containers:
      - name: aks-helloworld-one
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
  name: aks-helloworld-one
spec:
  type: ClusterIP
  ports:
  - port: 80
  selector:
    app: aks-helloworld-one
```

Utwórz plik *AKS-HelloWorld-dwa. YAML* i skopiuj go w poniższym przykładzie YAML:

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: aks-helloworld-two
spec:
  replicas: 1
  selector:
    matchLabels:
      app: aks-helloworld-two
  template:
    metadata:
      labels:
        app: aks-helloworld-two
    spec:
      containers:
      - name: aks-helloworld-two
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
  name: aks-helloworld-two
spec:
  type: ClusterIP
  ports:
  - port: 80
  selector:
    app: aks-helloworld-two
```

Uruchom dwie aplikacje demonstracyjne przy użyciu `kubectl apply` :

```console
kubectl apply -f aks-helloworld-one.yaml --namespace ingress-basic
kubectl apply -f aks-helloworld-two.yaml --namespace ingress-basic
```

## <a name="create-an-ingress-route"></a>Tworzenie trasy transferu danych przychodzących

Obie aplikacje działają teraz w klastrze Kubernetes. Jednak są skonfigurowane za pomocą usługi typu `ClusterIP` i nie są dostępne z Internetu. Aby udostępnić je publicznie, utwórz zasób Kubernetes. Zasób danych przychodzących konfiguruje reguły, które kierują ruch do jednej z dwóch aplikacji.

W poniższym przykładzie ruch do adresu *Hello-World-Ingress. MY_CUSTOM_DOMAIN* jest kierowany do usługi *AKS-HelloWorld* . Ruch do adresu *Hello-World-Ingres. MY_CUSTOM_DOMAIN/Hello-World-Two* jest kierowany do usługi *AKS-HelloWorld-2* . Ruch do *Hello-World — dane wejściowe. MY_CUSTOM_DOMAIN/static* jest kierowany do usługi o nazwie *AKS-HelloWorld* dla zasobów statycznych.

> [!NOTE]
> Jeśli skonfigurowano nazwę FQDN dla adresu IP kontrolera transferu danych przychodzących zamiast domeny niestandardowej, użyj nazwy FQDN zamiast *Hello-World-Ingres. MY_CUSTOM_DOMAIN*. Jeśli na przykład nazwa FQDN to *demo-AKS-Ingress.eastus.cloudapp.Azure.com*, zastąp opcję *Hello-World-Transfered. MY_CUSTOM_DOMAIN* z *demo-AKS-Ingress.eastus.cloudapp.Azure.com* w `hello-world-ingress.yaml` .

Utwórz plik o nazwie `hello-world-ingress.yaml` using poniżej przykład YAML. Zaktualizuj *hosty* i *host* na nazwę DNS utworzoną w poprzednim kroku.

```yaml
apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/rewrite-target: /$1
    nginx.ingress.kubernetes.io/use-regex: "true"
    cert-manager.io/cluster-issuer: letsencrypt
spec:
  tls:
  - hosts:
    - hello-world-ingress.MY_CUSTOM_DOMAIN
    secretName: tls-secret
  rules:
  - host: hello-world-ingress.MY_CUSTOM_DOMAIN
    http:
      paths:
      - backend:
          serviceName: aks-helloworld-one
          servicePort: 80
        path: /hello-world-one(/|$)(.*)
      - backend:
          serviceName: aks-helloworld-two
          servicePort: 80
        path: /hello-world-two(/|$)(.*)
      - backend:
          serviceName: aks-helloworld-one
          servicePort: 80
        path: /(.*)
---
apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress-static
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/rewrite-target: /static/$2
    nginx.ingress.kubernetes.io/use-regex: "true"
    cert-manager.io/cluster-issuer: letsencrypt
spec:
  tls:
  - hosts:
    - hello-world-ingress.MY_CUSTOM_DOMAIN
    secretName: tls-secret
  rules:
  - host: hello-world-ingress.MY_CUSTOM_DOMAIN
    http:
      paths:
      - backend:
          serviceName: aks-helloworld-one
          servicePort: 80
        path: /static(/|$)(.*)
```

Utwórz zasób transferu danych przychodzących przy użyciu `kubectl apply` polecenia.

```console
kubectl apply -f hello-world-ingress.yaml --namespace ingress-basic
```

## <a name="verify-a-certificate-object-has-been-created"></a>Weryfikowanie utworzenia obiektu certyfikatu

Następnie należy utworzyć zasób certyfikatu. Zasób certyfikatu definiuje żądany certyfikat X. 509. Aby uzyskać więcej informacji, zobacz [Certyfikaty Menedżera][cert-manager-certificates]certyfikatów. Menedżer certyfikatów automatycznie utworzył obiekt certyfikatu przy użyciu funkcji transferu danych przychodzących-podkładki, która jest automatycznie wdrażana z menedżerem certyfikatów od 0.2.2. Aby uzyskać więcej informacji, zapoznaj się z [dokumentacją dotyczącą podkładki][ingress-shim].

Aby sprawdzić, czy certyfikat został utworzony pomyślnie, użyj `kubectl get certificate --namespace ingress-basic` polecenia i sprawdź, *READY* czy jest to *prawdziwe*, co może potrwać kilka minut.

```
$ kubectl get certificate --namespace ingress-basic

NAME         READY   SECRET       AGE
tls-secret   True    tls-secret   11m
```

## <a name="test-the-ingress-configuration"></a>Testowanie konfiguracji transferu danych przychodzących

Otwórz przeglądarkę internetową, aby *powitać w Internecie. MY_CUSTOM_DOMAIN* kontrolera transferu danych przychodzących. Zwróć uwagę, że przekierujesz do korzystania z protokołu HTTPS, a certyfikat jest zaufany, a aplikacja demonstracyjna zostanie wyświetlona w przeglądarce sieci Web. Dodaj ścieżkę */Hello-World-Two* i zwróć uwagę na drugą aplikację demonstracyjną z tytułem niestandardowym.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

W tym artykule użyto Helm do zainstalowania składników przychodzących, certyfikatów i przykładowych aplikacji. Po wdrożeniu wykresu Helm są tworzone różne zasoby Kubernetes. Te zasoby obejmują między innymi te, wdrożenia i usługi. Aby wyczyścić te zasoby, można usunąć całą przykładową przestrzeń nazw lub poszczególne zasoby.

### <a name="delete-the-sample-namespace-and-all-resources"></a>Usuń przykładową przestrzeń nazw i wszystkie zasoby

Aby usunąć całą przykładową przestrzeń nazw, użyj `kubectl delete` polecenia i określ nazwę przestrzeni nazw. Wszystkie zasoby w przestrzeni nazw są usuwane.

```console
kubectl delete namespace ingress-basic
```

### <a name="delete-resources-individually"></a>Usuń zasoby pojedynczo

Alternatywnie, bardziej szczegółowe podejście polega na usunięciu utworzonych poszczególnych zasobów. Najpierw usuń zasoby wystawcy klastra:

```console
kubectl delete -f cluster-issuer.yaml --namespace ingress-basic
```

Utwórz listę wersji Helm za pomocą `helm list` polecenia. Wyszukaj wykresy o nazwach *Nginx* i *Menedżer certyfikatów*, jak pokazano w następujących przykładowych danych wyjściowych:

```
$ helm list --namespace ingress-basic

NAME                    NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                   APP VERSION
cert-manager            ingress-basic   1               2020-01-15 10:23:36.515514 -0600 CST    deployed        cert-manager-v0.13.0    v0.13.0    
nginx                   ingress-basic   1               2020-01-15 10:09:45.982693 -0600 CST    deployed        nginx-ingress-1.29.1    0.27.0  
```

Odinstaluj wydania za pomocą `helm uninstall` polecenia. Poniższy przykład Odinstalowuje wdrożenia NGINX przychodzących i menedżerów certyfikatów.

```
$ helm uninstall cert-manager nginx --namespace ingress-basic

release "cert-manager" uninstalled
release "nginx" uninstalled
```

Następnie usuń dwie przykładowe aplikacje:

```console
kubectl delete -f aks-helloworld-one.yaml --namespace ingress-basic
kubectl delete -f aks-helloworld-two.yaml --namespace ingress-basic
```

Usuń trasę transferu danych przychodzących, która kieruje ruch do aplikacji przykładowych:

```console
kubectl delete -f hello-world-ingress.yaml --namespace ingress-basic
```

Na koniec można usunąć samą przestrzeń nazw. Użyj `kubectl delete` polecenia i określ nazwę przestrzeni nazw:

```console
kubectl delete namespace ingress-basic
```

## <a name="next-steps"></a>Następne kroki

W tym artykule uwzględniono niektóre składniki zewnętrzne do AKS. Aby dowiedzieć się więcej o tych składnikach, zobacz następujące strony projektu:

- [Interfejs wiersza polecenia Helm][helm-cli]
- [Kontroler transferu danych przychodzących NGINX][nginx-ingress]
- [cert-manager][cert-manager]

Możesz również wykonać następujące czynności:

- [Tworzenie podstawowego kontrolera danych wejściowych z łącznością sieci zewnętrznej][aks-ingress-basic]
- [Włącz dodatek routingu aplikacji protokołu HTTP][aks-http-app-routing]
- [Utwórz kontroler transferu danych przychodzących, który używa wewnętrznej, prywatnej sieci i adresu IP][aks-ingress-internal]
- [Tworzenie kontrolera transferu danych przychodzących korzystającego z własnych certyfikatów TLS][aks-ingress-own-tls]
- [Utwórz kontroler transferu danych przychodzących, który używa szyfrowania, aby automatycznie generować certyfikaty TLS ze statycznym publicznym adresem IP][aks-ingress-static-tls]

<!-- LINKS - external -->
[az-network-dns-record-set-a-add-record]: /cli/azure/network/dns/record-set/a?view=azure-cli-latest#az-network-dns-record-set-a-add-record
[custom-domain]: ../app-service/manage-custom-dns-buy-domain.md#buy-the-domain
[dns-zone]: ../dns/dns-getstarted-cli.md
[helm]: https://helm.sh/
[helm-cli]: ./kubernetes-helm.md
[cert-manager]: https://github.com/jetstack/cert-manager
[cert-manager-certificates]: https://cert-manager.readthedocs.io/en/latest/reference/certificates.html
[ingress-shim]: https://docs.cert-manager.io/en/latest/tasks/issuing-certificates/ingress-shim.html
[cert-manager-cluster-issuer]: https://cert-manager.readthedocs.io/en/latest/reference/clusterissuers.html
[cert-manager-issuer]: https://cert-manager.readthedocs.io/en/latest/reference/issuers.html
[lets-encrypt]: https://letsencrypt.org/
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx
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
[aks-ingress-own-tls]: ingress-own-tls.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[client-source-ip]: concepts-network.md#ingress-controllers
[install-azure-cli]: /cli/azure/install-azure-cli
