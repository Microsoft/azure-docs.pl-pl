---
title: Tworzenie danych przychodzących przy użyciu automatycznego TLS
titleSuffix: Azure Kubernetes Service
description: Dowiedz się, jak zainstalować i skonfigurować kontroler ruchu przychodzących NGINX, który używa let's encrypt do automatycznego generowania certyfikatów TLS w klastrze usługi Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 08/17/2020
ms.openlocfilehash: a3f78c0777d1377c8afdc5b43b457873de395cd6
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107779799"
---
# <a name="create-an-https-ingress-controller-on-azure-kubernetes-service-aks"></a>Tworzenie kontrolera ruchu przychodzących HTTPS na Azure Kubernetes Service (AKS)

Kontroler ruchu przychodzącego to element oprogramowania dostarczający odwrotny serwer proxy, konfigurowalne trasowanie ruchu oraz zakończenie protokołu TLS dla usług Kubernetes. Zasoby ruchu przychodzącego usług Kubernetes są używane do skonfigurowania zasad ruchu przychodzącego oraz tras dla poszczególnych usług Kubernetes. Dzięki korzystaniu z kontrolera ruchu przychodzącego oraz zasad ruchu przychodzącego można użyć jednego adresu IP do trasowania ruchu w wielu usługach w klastrze Kubernetes.

W tym artykule pokazano, jak wdrożyć kontroler ruchu przychodzących [NGINX][nginx-ingress] w klastrze Azure Kubernetes Service (AKS). Projekt [cert-manager][cert-manager] służy do automatycznego generowania i konfigurowania certyfikatów [Let's Encrypt.][lets-encrypt] Na koniec dwie aplikacje są uruchamiane w klastrze usługi AKS, z których każda jest dostępna za pośrednictwem jednego adresu IP.

Możesz również wykonać następujące czynności:

- [Tworzenie podstawowego kontrolera ruchu wychodzącego z zewnętrzną łącznością sieciową][aks-ingress-basic]
- [Włączanie dodatku routingu aplikacji HTTP][aks-http-app-routing]
- [Tworzenie kontrolera ruchu wychodzącego, który używa wewnętrznej, prywatnej sieci i adresu IP][aks-ingress-internal]
- [Tworzenie kontrolera ruchu wychodzącego, który używa własnych certyfikatów TLS][aks-ingress-own-tls]
- [Tworzenie kontrolera ruchu wychodzącego, który używa szyfrowania Let's Encrypt do automatycznego generowania certyfikatów TLS ze statycznym publicznym adresem IP][aks-ingress-static-tls]

## <a name="before-you-begin"></a>Zanim rozpoczniesz

W tym artykule przyjęto założenie, że masz istniejący klaster usługi AKS. Jeśli potrzebujesz klastra usługi AKS, zobacz przewodnik Szybki start usługi AKS przy użyciu interfejsu wiersza polecenia platformy [Azure][aks-quickstart-cli] lub [interfejsu Azure Portal][aks-quickstart-portal].

W tym artykule założono również, że masz [domenę niestandardową][custom-domain] ze strefą [DNS][dns-zone] w tej samej grupie zasobów co klaster usługi AKS.

W tym artykule użyto [programu Helm 3][helm] do zainstalowania kontrolera ruchu przychodzących NGINX i menedżera certyfikatów. Upewnij się, że używasz najnowszej wersji programu Helm i masz dostęp do repozytoriów *ingress-nginx* i *jetstack* Helm. Aby uzyskać instrukcje dotyczące uaktualniania, zobacz [dokumenty dotyczące instalacji programu Helm.][helm-install] Aby uzyskać więcej informacji na temat konfigurowania i używania programu Helm, zobacz Instalowanie aplikacji za pomocą [programu Helm w Azure Kubernetes Service (AKS).][use-helm]

Ten artykuł wymaga również interfejsu wiersza polecenia platformy Azure w wersji 2.0.64 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli-install].

## <a name="create-an-ingress-controller"></a>Tworzenie kontrolera ruchu wychodzącego

Aby utworzyć kontroler ruchu przychodzących, użyj `helm` polecenia , aby zainstalować polecenie *nginx-ingress.* W celu zwiększenia nadmiarowości za pomocą parametru `--set controller.replicaCount` wdrażane są dwie repliki kontrolerów wejściowych NGINX. Aby w pełni korzystać z uruchomionych replik kontrolera ruchu wychodzącego, upewnij się, że w klastrze usługi AKS znajduje się więcej niż jeden węzeł.

Kontroler wejściowy należy również zaplanować w węźle z systemem Linux. Nie należy go uruchamiać w węzłach z systemem Windows Server. Za pomocą parametru `--set nodeSelector` podaje się selektor węzła, który nakazuje harmonogramowi usługi Kubernetes uruchomienie kontrolera wejściowego NGINX w węźle opartym na systemie Linux.

> [!TIP]
> Poniższy przykład tworzy przestrzeń nazw Kubernetes dla zasobów przychodzących o *nazwie ingress-basic*. Określ przestrzeń nazw dla własnego środowiska zgodnie z potrzebami.

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

Aby uzyskać publiczny adres IP, użyj `kubectl get service` polecenia . Przypisanie adresu IP do usługi może potrwać kilka minut.

```console
$ kubectl --namespace ingress-basic get services -o wide -w nginx-ingress-ingress-nginx-controller

NAME                                     TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)                      AGE   SELECTOR
nginx-ingress-ingress-nginx-controller   LoadBalancer   10.0.74.133   EXTERNAL_IP     80:32486/TCP,443:30953/TCP   44s   app.kubernetes.io/component=controller,app.kubernetes.io/instance=nginx-ingress,app.kubernetes.io/name=ingress-nginx
```

Nie utworzono jeszcze żadnych reguł ruchu wychodzącego. W przypadku przeglądania do publicznego adresu IP zostanie wyświetlona domyślna strona 404 kontrolera ruchu przychodzących NGINX.

## <a name="add-an-a-record-to-your-dns-zone"></a>Dodawanie rekordu A do strefy DNS

Dodaj rekord *A* do strefy DNS przy użyciu zewnętrznego adresu IP usługi NGINX, używając [az network dns record-set add-record.][az-network-dns-record-set-a-add-record]

```console
az network dns record-set a add-record \
    --resource-group myResourceGroup \
    --zone-name MY_CUSTOM_DOMAIN \
    --record-set-name * \
    --ipv4-address MY_EXTERNAL_IP
```

> [!NOTE]
> Opcjonalnie można skonfigurować nazwę FQDN dla adresu IP kontrolera ruchu wychodzącego zamiast domeny niestandardowej. Należy pamiętać, że ten przykład dotyczy powłoki Bash.
> 
> ```bash
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

Kontroler ruchu przychodzącego NGINX obsługuje kończenie żądań protokołu TLS. Istnieje kilka sposobów pobierania i konfigurowania certyfikatów protokołu HTTPS. W tym artykule przedstawiono użycie [narzędzia cert-manager,][cert-manager]które zapewnia automatyczne [szyfrowanie][lets-encrypt] generowania certyfikatów i zarządzania nimi.

Aby zainstalować kontroler cert-manager:

```console
# Label the ingress-basic namespace to disable resource validation
kubectl label namespace ingress-basic cert-manager.io/disable-validation=true

# Add the Jetstack Helm repository
helm repo add jetstack https://charts.jetstack.io

# Update your local Helm chart repository cache
helm repo update

# Install the cert-manager Helm chart
helm install cert-manager jetstack/cert-manager \
  --namespace ingress-basic \
  --set installCRDs=true \
  --set nodeSelector."kubernetes\.io/os"=linux \
  --set webhook.nodeSelector."kubernetes\.io/os"=linux \
  --set cainjector.nodeSelector."kubernetes\.io/os"=linux
```

Aby uzyskać więcej informacji na temat konfiguracji cert-manager, zobacz [projekt cert-manager][cert-manager].

## <a name="create-a-ca-cluster-issuer"></a>Tworzenie wystawcy klastra urzędu certyfikacji

Aby można było wystawiać certyfikaty, menedżer certyfikatów wymaga zasobu [Wystawca][cert-manager-issuer] lub [Wystawca][cert-manager-cluster-issuer] klastra. Te zasoby kubernetes są identyczne w funkcjonalności, jednak działa w jednej przestrzeni `Issuer` nazw i działa we wszystkich `ClusterIssuer` przestrzeniach nazw. Aby uzyskać więcej informacji, zobacz dokumentację [wystawcy cert-manager.][cert-manager-issuer]

Utwórz wystawcę klastra, takiego jak `cluster-issuer.yaml` , przy użyciu następującego przykładowego manifestu. Zaktualizuj adres e-mail przy użyciu prawidłowego adresu organizacji:

```yaml
apiVersion: cert-manager.io/v1
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

Aby utworzyć wystawcę, użyj `kubectl apply` polecenia .

```console
kubectl apply -f cluster-issuer.yaml
```

## <a name="run-demo-applications"></a>Uruchamianie aplikacji demonstracyjnych

Skonfigurowano kontroler ruchu wychodzącego i rozwiązanie do zarządzania certyfikatami. Teraz uruchommy dwie aplikacje demonstracyjne w klastrze usługi AKS. W tym przykładzie program Helm służy do wdrażania dwóch wystąpień prostej aplikacji *Hello* world.

Aby zobaczyć działanie kontrolera ruchu wychodzącego, uruchom dwie aplikacje demonstracyjne w klastrze usługi AKS. W tym przykładzie użyjemy funkcji , aby `kubectl apply` wdrożyć dwa wystąpienia prostej aplikacji *Hello* world.

Utwórz plik *aks-helloworld-one.yaml* i skopiuj go w następującym przykładzie YAML:

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

Utwórz plik *aks-helloworld-two.yaml* i skopiuj go w następującym przykładzie YAML:

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

Uruchom dwie aplikacje demonstracyjne przy użyciu narzędzia `kubectl apply` :

```console
kubectl apply -f aks-helloworld-one.yaml --namespace ingress-basic
kubectl apply -f aks-helloworld-two.yaml --namespace ingress-basic
```

## <a name="create-an-ingress-route"></a>Tworzenie trasy ruchu wychodzącego

Obie aplikacje są teraz uruchomione w klastrze Kubernetes. Jednak są one skonfigurowane za pomocą usługi typu `ClusterIP` i nie są dostępne z Internetu. Aby udostępnić je publicznie, utwórz zasób danych przychodzących Kubernetes. Zasób ruchu przychodzących konfiguruje reguły, które przekierowywuje ruch do jednej z dwóch aplikacji.

W poniższym przykładzie ruch na adres *hello-world-ingress. MY_CUSTOM_DOMAIN* jest kierowane do usługi *aks-helloworld-one.* Ruch do adresu *hello-world-ingress. MY_CUSTOM_DOMAIN/hello-world-two* jest kierowane do *usługi aks-helloworld-two.* Ruch do *ruchu wychodzącego hello-world. MY_CUSTOM_DOMAIN/static jest* kierowane do usługi o nazwie *aks-helloworld-one dla* zasobów statycznych.

> [!NOTE]
> Jeśli skonfigurowano nazwę FQDN dla adresu IP kontrolera ruchu wychodzącego zamiast domeny niestandardowej, użyj nazwy FQDN zamiast *hello-world-ingress. MY_CUSTOM_DOMAIN .* Jeśli na przykład w twojej demo-aks-ingress.eastus.cloudapp.azure.com FQDN *znajduje* się adres , zastąp *hello-world-ingress. MY_CUSTOM_DOMAIN* *z* demo-aks-ingress.eastus.cloudapp.azure.com `hello-world-ingress.yaml` w .

Utwórz plik o nazwie `hello-world-ingress.yaml` przy użyciu poniższego przykładowego pliku YAML. Zaktualizuj *hosty* *i hosty* do nazwy DNS utworzonej w poprzednim kroku.

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

Utwórz zasób danych przychodzących przy użyciu `kubectl apply` polecenia .

```console
kubectl apply -f hello-world-ingress.yaml --namespace ingress-basic
```

## <a name="verify-a-certificate-object-has-been-created"></a>Sprawdzanie, czy obiekt certyfikatu został utworzony

Następnie należy utworzyć zasób certyfikatu. Zasób certyfikatu definiuje żądany certyfikat X.509. Aby uzyskać więcej informacji, zobacz [certyfikaty cert-manager][cert-manager-certificates]. Program Cert-manager automatycznie utworzył obiekt certyfikatu przy użyciu podkładki ingress-shim, która jest automatycznie wdrażana za pomocą narzędzia cert-manager od wersji 0.2.2. Aby uzyskać więcej informacji, zobacz [dokumentację podkładki dla danych przychodzących.][ingress-shim]

Aby sprawdzić, czy certyfikat został utworzony pomyślnie, użyj polecenia i sprawdź, czy wartość READY ma wartość `kubectl get certificate --namespace ingress-basic` *True,*  co może potrwać kilka minut.

```console
$ kubectl get certificate --namespace ingress-basic

NAME         READY   SECRET       AGE
tls-secret   True    tls-secret   11m
```

## <a name="test-the-ingress-configuration"></a>Testowanie konfiguracji danych przychodzących

Otwórz przeglądarkę internetową, aby *otworzyć stronę hello-world-ingress. MY_CUSTOM_DOMAIN* kontrolera ruchu przychodzących Kubernetes. Zwróć uwagę, że przekierowujesz do korzystania z protokołu HTTPS, a certyfikat jest zaufany, a aplikacja demonstracyjna jest wyświetlana w przeglądarce internetowej. Dodaj *ścieżkę /hello-world-two* i zwróć uwagę na to, że jest wyświetlana druga aplikacja demonstracyjna z niestandardowym tytułem.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

W tym artykule program Helm został użyty do zainstalowania składników, certyfikatów i przykładowych aplikacji dla danych przychodzących. Podczas wdrażania wykresu helm jest tworzona liczba zasobów kubernetes. Te zasoby obejmują zasobniki, wdrożenia i usługi. Aby wyczyścić te zasoby, możesz usunąć całą przykładową przestrzeń nazw lub poszczególne zasoby.

### <a name="delete-the-sample-namespace-and-all-resources"></a>Usuwanie przykładowej przestrzeni nazw i wszystkich zasobów

Aby usunąć całą przykładową przestrzeń nazw, użyj `kubectl delete` polecenia i określ nazwę przestrzeni nazw. Wszystkie zasoby w przestrzeni nazw zostaną usunięte.

```console
kubectl delete namespace ingress-basic
```

### <a name="delete-resources-individually"></a>Pojedyncze usuwanie zasobów

Alternatywnie bardziej szczegółowym podejściem jest usunięcie poszczególnych utworzonych zasobów. Najpierw usuń zasoby wystawcy klastra:

```console
kubectl delete -f cluster-issuer.yaml --namespace ingress-basic
```

Lista wydań helm za pomocą `helm list` polecenia . Poszukaj wykresów o *nazwach nginx* i *cert-manager,* jak pokazano w następujących przykładowych danych wyjściowych:

```console
$ helm list --namespace ingress-basic

NAME                    NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                   APP VERSION
cert-manager            ingress-basic   1               2020-01-15 10:23:36.515514 -0600 CST    deployed        cert-manager-v0.13.0    v0.13.0    
nginx                   ingress-basic   1               2020-01-15 10:09:45.982693 -0600 CST    deployed        nginx-ingress-1.29.1    0.27.0  
```

Odinstaluj wydania za pomocą `helm uninstall` polecenia . Poniższy przykład odinstalowuje wdrożenia danych przychodzących i wdrożeń menedżera certyfikatów NGINX.

```console
$ helm uninstall cert-manager nginx --namespace ingress-basic

release "cert-manager" uninstalled
release "nginx" uninstalled
```

Następnie usuń dwie przykładowe aplikacje:

```console
kubectl delete -f aks-helloworld-one.yaml --namespace ingress-basic
kubectl delete -f aks-helloworld-two.yaml --namespace ingress-basic
```

Usuń trasę ruchu wychodzącego, która kierowała ruch do przykładowych aplikacji:

```console
kubectl delete -f hello-world-ingress.yaml --namespace ingress-basic
```

Na koniec możesz usunąć samą przestrzeń nazw. Użyj polecenia `kubectl delete` i określ nazwę przestrzeni nazw:

```console
kubectl delete namespace ingress-basic
```

## <a name="next-steps"></a>Następne kroki

W tym artykule uwzględniono niektóre składniki zewnętrzne usługi AKS. Aby dowiedzieć się więcej na temat tych składników, zobacz następujące strony projektu:

- [Interfejs wiersza polecenia helm][helm-cli]
- [Kontroler ruchu przychodzących NGINX][nginx-ingress]
- [cert-manager][cert-manager]

Możesz również wykonać następujące czynności:

- [Tworzenie podstawowego kontrolera ruchu wychodzącego z zewnętrzną łącznością sieciową][aks-ingress-basic]
- [Włączanie dodatku routingu aplikacji HTTP][aks-http-app-routing]
- [Tworzenie kontrolera ruchu wychodzącego, który używa wewnętrznej, prywatnej sieci i adresu IP][aks-ingress-internal]
- [Tworzenie kontrolera ruchu wychodzącego, który używa własnych certyfikatów TLS][aks-ingress-own-tls]
- [Tworzenie kontrolera ruchu wychodzącego, który używa szyfrowania Let's Encrypt do automatycznego generowania certyfikatów TLS ze statycznym publicznym adresem IP][aks-ingress-static-tls]

<!-- LINKS - external -->
[az-network-dns-record-set-a-add-record]: /cli/azure/network/dns/record-set/#az_network_dns_record_set_a_add_record
[custom-domain]: ../app-service/manage-custom-dns-buy-domain.md#buy-an-app-service-domain
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
[az-aks-show]: /cli/azure/aks#az_aks_show
[az-network-public-ip-create]: /cli/azure/network/public-ip#az_network_public_ip_create
[aks-ingress-internal]: ingress-internal-ip.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-ingress-basic]: ingress-basic.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-own-tls]: ingress-own-tls.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[client-source-ip]: concepts-network.md#ingress-controllers
[install-azure-cli]: /cli/azure/install-azure-cli
