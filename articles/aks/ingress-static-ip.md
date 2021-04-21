---
title: Używanie kontrolera ruchu przychodzących ze statycznym adresem IP
titleSuffix: Azure Kubernetes Service
description: Dowiedz się, jak zainstalować i skonfigurować kontroler ruchu przychodzących NGINX przy użyciu statycznego publicznego adresu IP w klastrze usługi Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 08/17/2020
ms.openlocfilehash: 8de0d25bc30d53f11ddaed5ab7b53ff992a5c88c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107779835"
---
# <a name="create-an-ingress-controller-with-a-static-public-ip-address-in-azure-kubernetes-service-aks"></a>Tworzenie kontrolera ruchu przychodzącego za pomocą statycznego publicznego adresu IP w usłudze Azure Kubernetes Service (AKS)

Kontroler ruchu przychodzącego to element oprogramowania dostarczający odwrotny serwer proxy, konfigurowalne trasowanie ruchu oraz zakończenie protokołu TLS dla usług Kubernetes. Zasoby ruchu przychodzącego usług Kubernetes są używane do skonfigurowania zasad ruchu przychodzącego oraz tras dla poszczególnych usług Kubernetes. Dzięki korzystaniu z kontrolera ruchu przychodzącego oraz zasad ruchu przychodzącego można użyć jednego adresu IP do trasowania ruchu w wielu usługach w klastrze Kubernetes.

W tym artykule pokazano, jak wdrożyć kontroler ruchu przychodzących [NGINX][nginx-ingress] w klastrze Azure Kubernetes Service (AKS). Kontroler ruchu wychodzącego jest skonfigurowany przy użyciu statycznego publicznego adresu IP. Projekt [cert-manager][cert-manager] służy do automatycznego generowania i konfigurowania certyfikatów [Let's Encrypt.][lets-encrypt] Na koniec dwie aplikacje są uruchamiane w klastrze usługi AKS, z których każda jest dostępna za pośrednictwem jednego adresu IP.

Możesz również wykonać następujące czynności:

- [Tworzenie podstawowego kontrolera ruchu wychodzącego z zewnętrzną łącznością sieciową][aks-ingress-basic]
- [Włączanie dodatku routingu aplikacji HTTP][aks-http-app-routing]
- [Tworzenie kontrolera ruchu wychodzącego, który używa własnych certyfikatów TLS][aks-ingress-own-tls]
- [Tworzenie kontrolera ruchu wychodzącego, który używa szyfrowania Let's Encrypt do automatycznego generowania certyfikatów TLS z dynamicznym publicznym adresem IP][aks-ingress-tls]

## <a name="before-you-begin"></a>Zanim rozpoczniesz

W tym artykule przyjęto założenie, że masz istniejący klaster usługi AKS. Jeśli potrzebujesz klastra usługi AKS, zobacz przewodnik Szybki start usługi AKS przy użyciu interfejsu wiersza polecenia platformy [Azure][aks-quickstart-cli] lub [interfejsu Azure Portal][aks-quickstart-portal].

W tym artykule użyto [programu Helm 3][helm] do zainstalowania kontrolera ruchu przychodzących NGINX i menedżera certyfikatów. Upewnij się, że używasz najnowszej wersji programu Helm i masz dostęp do repozytoriów *ingress-nginx* i *jetstack* Helm. Aby uzyskać instrukcje dotyczące uaktualniania, zobacz [dokumenty dotyczące instalacji programu Helm.][helm-install] Aby uzyskać więcej informacji na temat konfigurowania i używania programu Helm, zobacz Instalowanie aplikacji za pomocą [programu Helm w Azure Kubernetes Service (AKS).][use-helm]

Ten artykuł wymaga również interfejsu wiersza polecenia platformy Azure w wersji 2.0.64 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli-install].

## <a name="create-an-ingress-controller"></a>Tworzenie kontrolera ruchu wychodzącego

Domyślnie kontroler ruchu przychodzących NGINX jest tworzony z nowym przypisaniem publicznego adresu IP. Ten publiczny adres IP jest statyczny tylko dla okresu życia kontrolera ruchu przychodzących i zostanie utracony w przypadku usunięcia i ponownego utworzenia kontrolera. Powszechnym wymaganiem konfiguracji jest zapewnienie kontrolerowi ruchu przychodzącym NGINX istniejącego statycznego publicznego adresu IP. Statyczny publiczny adres IP pozostaje w przypadku usunięcia kontrolera ruchu wychodzącego. Takie podejście umożliwia spójne korzystanie z istniejących rekordów DNS i konfiguracji sieci w całym cyklu życia aplikacji.

Jeśli musisz utworzyć statyczny publiczny adres IP, najpierw pobierz nazwę grupy zasobów klastra usługi AKS za pomocą [polecenia az aks show:][az-aks-show]

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv
```

Następnie utwórz publiczny adres IP przy użyciu metody alokacji *statycznej* przy użyciu [polecenia az network public-ip create.][az-network-public-ip-create] Poniższy przykład tworzy publiczny adres IP o nazwie *myAKSPublicIP* w grupie zasobów klastra AKS uzyskanej w poprzednim kroku:

```azurecli-interactive
az network public-ip create --resource-group MC_myResourceGroup_myAKSCluster_eastus --name myAKSPublicIP --sku Standard --allocation-method static --query publicIp.ipAddress -o tsv
```

> [!NOTE]
> Powyższe polecenia tworzą adres IP, który zostanie usunięty w przypadku usunięcia klastra usługi AKS. Alternatywnie możesz utworzyć adres IP w innej grupie zasobów, którą można zarządzać oddzielnie od klastra usługi AKS. Jeśli utworzysz adres IP w innej grupie zasobów, upewnij się, że tożsamość klastra używana przez klaster usługi AKS ma delegowane uprawnienia do innej grupy zasobów, takiej jak *Współautor sieci.* Aby uzyskać więcej informacji, zobacz Use a static public IP address and DNS label with the AKS load balancer (Używanie statycznego publicznego adresu IP i [etykiety DNS z usługą równoważenia obciążenia usługi AKS).][aks-static-ip]

Teraz wd *wdrażają pakiet nginx-ingress* za pomocą narzędzia Helm. W celu zwiększenia nadmiarowości za pomocą parametru `--set controller.replicaCount` wdrażane są dwie repliki kontrolerów wejściowych NGINX. Aby w pełni korzystać z uruchomionych replik kontrolera ruchu wychodzącego, upewnij się, że w klastrze usługi AKS znajduje się więcej niż jeden węzeł.

Należy przekazać dwa dodatkowe parametry do wydania usługi Helm, aby kontroler ruchu wejściowego wiedział o statycznym adresie IP usługi równoważenia obciążenia, który ma zostać przydzielony do usługi kontrolera ruchu wychodzącego, oraz o etykiecie nazwy DNS stosowanej do zasobu publicznego adresu IP. Aby certyfikaty HTTPS działały prawidłowo, do skonfigurowania nazwy FQDN dla adresu IP kontrolera ruchu wychodzącego jest używana etykieta nazwy DNS.

1. Dodaj `--set controller.service.loadBalancerIP` parametr . Określ własny publiczny adres IP, który został utworzony w poprzednim kroku.
1. Dodaj `--set controller.service.annotations."service\.beta\.kubernetes\.io/azure-dns-label-name"` parametr . Określ etykietę nazwy DNS, która ma zostać zastosowana do publicznego adresu IP utworzonego w poprzednim kroku.

Kontroler wejściowy należy również zaplanować w węźle z systemem Linux. Nie należy go uruchamiać w węzłach z systemem Windows Server. Za pomocą parametru `--set nodeSelector` podaje się selektor węzła, który nakazuje harmonogramowi usługi Kubernetes uruchomienie kontrolera wejściowego NGINX w węźle opartym na systemie Linux.

> [!TIP]
> Poniższy przykład tworzy przestrzeń nazw Kubernetes dla zasobów przychodzących o *nazwie ingress-basic*. Określ przestrzeń nazw dla własnego środowiska zgodnie z potrzebami. Jeśli klaster usługi AKS nie ma włączonej kontroli RBAC na platformie Kubernetes, dodaj `--set rbac.create=false` ją do poleceń programu Helm.

> [!TIP]
> Jeśli chcesz włączyć zachowywanie [źródłowych adresów IP klienta dla][client-source-ip] żądań do kontenerów w klastrze, dodaj polecenie do polecenia instalacji programu `--set controller.service.externalTrafficPolicy=Local` Helm. Źródłowy adres IP klienta jest przechowywany w nagłówku żądania w obszarze *X-Forwarded-For*. W przypadku korzystania z kontrolera ruchu wychodzącego z włączonym zachowywaniem źródłowych adresów IP klienta, pass-through protokołu TLS nie będzie działać.

Zaktualizuj następujący skrypt przy użyciu **adresu IP** kontrolera  ruchu danych przychodzących i unikatowej nazwy, która ma być dostępna dla prefiksu nazwy FQDN.

> [!IMPORTANT]
> Należy zaktualizować zastąp *STATIC_IP* i *DNS_LABEL* własnym adresem IP i unikatową nazwą podczas uruchamiania polecenia.

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
    --set controller.admissionWebhooks.patch.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set controller.service.loadBalancerIP="STATIC_IP" \
    --set controller.service.annotations."service\.beta\.kubernetes\.io/azure-dns-label-name"="DNS_LABEL"
```

Po utworzeniu usługi równoważenia obciążenia Kubernetes dla kontrolera ruchu przychodzących NGINX zostanie przypisany statyczny adres IP, jak pokazano w następujących przykładowych danych wyjściowych:

```
$ kubectl --namespace ingress-basic get services -o wide -w nginx-ingress-ingress-nginx-controller

NAME                                     TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)                      AGE   SELECTOR
nginx-ingress-ingress-nginx-controller   LoadBalancer   10.0.74.133   EXTERNAL_IP     80:32486/TCP,443:30953/TCP   44s   app.kubernetes.io/component=controller,app.kubernetes.io/instance=nginx-ingress,app.kubernetes.io/name=ingress-nginx
```

Nie utworzono jeszcze żadnych reguł ruchu przychodzących, dlatego podczas przeglądania do publicznego adresu IP zostanie wyświetlona domyślna strona 404 kontrolera ruchu przychodzących NGINX. Reguły ruchu wychodzącego są konfigurowane w poniższych krokach.

Możesz sprawdzić, czy etykieta nazwy DNS została zastosowana, odpytując nazwę FQDN o publiczny adres IP w następujący sposób:

```azurecli-interactive
az network public-ip list --resource-group MC_myResourceGroup_myAKSCluster_eastus --query "[?ipAddress=='myAKSPublicIP'].[dnsSettings.fqdn]" -o tsv
```

Kontroler ruchu wychodzącego jest teraz dostępny za pośrednictwem adresu IP lub FQDN.

## <a name="install-cert-manager"></a>Instalowanie narzędzia cert-manager

Kontroler ruchu przychodzącego NGINX obsługuje kończenie żądań protokołu TLS. Istnieje kilka sposobów pobierania i konfigurowania certyfikatów protokołu HTTPS. W tym artykule przedstawiono użycie [narzędzia cert-manager,][cert-manager]które zapewnia automatyczne [szyfrowanie][lets-encrypt] generowania certyfikatów i zarządzania nimi.

> [!NOTE]
> W tym artykule jest `staging` używane środowisko let's encrypt. We wdrożeniach produkcyjnych użyj wartości `letsencrypt-prod` i `https://acme-v02.api.letsencrypt.org/directory` w definicjach zasobów oraz podczas instalowania pakietu Helm.

Aby zainstalować kontroler cert-manager w klastrze z włączoną obsługą kontroli RBAC kubernetes, użyj następującego `helm install` polecenia:

```console
# Label the cert-manager namespace to disable resource validation
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

Aby uzyskać więcej informacji na temat konfiguracji cert-manager, zobacz [projekt cert-manager][cert-manager].

## <a name="create-a-ca-cluster-issuer"></a>Tworzenie wystawcy klastra urzędu certyfikacji

Aby można było wystawiać certyfikaty, menedżer certyfikatów wymaga zasobu [Wystawca][cert-manager-issuer] lub [Wystawca][cert-manager-cluster-issuer] klastra. Te zasoby kubernetes są identyczne w funkcjonalności, jednak działa w jednej przestrzeni `Issuer` nazw i działa we wszystkich `ClusterIssuer` przestrzeniach nazw. Aby uzyskać więcej informacji, zobacz dokumentację [wystawcy cert-manager.][cert-manager-issuer]

Utwórz wystawcę klastra, takiego jak `cluster-issuer.yaml` , przy użyciu następującego przykładowego manifestu. Zaktualizuj adres e-mail prawidłowym adresem organizacji:

```yaml
apiVersion: cert-manager.io/v1alpha2
kind: ClusterIssuer
metadata:
  name: letsencrypt-staging
spec:
  acme:
    server: https://acme-staging-v02.api.letsencrypt.org/directory
    email: user@contoso.com
    privateKeySecretRef:
      name: letsencrypt-staging
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

```
kubectl apply -f cluster-issuer.yaml --namespace ingress-basic
```

Dane wyjściowe powinny być podobne do tego przykładu:

```
clusterissuer.cert-manager.io/letsencrypt-staging created
```

## <a name="run-demo-applications"></a>Uruchamianie aplikacji demonstracyjnych

Skonfigurowano kontroler ruchu wychodzącego i rozwiązanie do zarządzania certyfikatami. Teraz uruchommy dwie demonstracyjne aplikacje w klastrze usługi AKS. W tym przykładzie program Helm służy do wdrażania dwóch wystąpień prostej aplikacji "Hello world".

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

Obie aplikacje są teraz uruchomione w klastrze Kubernetes, jednak są one skonfigurowane przy użyciu usługi typu `ClusterIP` . W związku z tym aplikacje nie są dostępne z Internetu. Aby udostępnić je publicznie, utwórz zasób danych przychodzących Kubernetes. Zasób ruchu przychodzących konfiguruje reguły, które rozsyłają ruch do jednej z dwóch aplikacji.

W poniższym przykładzie ruch do adresu `https://demo-aks-ingress.eastus.cloudapp.azure.com/` jest kierowane do usługi o nazwie `aks-helloworld` . Ruch do adresu `https://demo-aks-ingress.eastus.cloudapp.azure.com/hello-world-two` jest przekierowyny do `ingress-demo` usługi. Zaktualizuj *hosty* *i hosty* do nazwy DNS utworzonej w poprzednim kroku.

Utwórz plik o nazwie `hello-world-ingress.yaml` i skopiuj go w poniższym przykładzie YAML.

```yaml
apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  annotations:
    kubernetes.io/ingress.class: nginx
    cert-manager.io/cluster-issuer: letsencrypt-staging
    nginx.ingress.kubernetes.io/rewrite-target: /$1
    nginx.ingress.kubernetes.io/use-regex: "true"
spec:
  tls:
  - hosts:
    - demo-aks-ingress.eastus.cloudapp.azure.com
    secretName: tls-secret
  rules:
  - host: demo-aks-ingress.eastus.cloudapp.azure.com
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

Utwórz zasób danych przychodzących przy użyciu `kubectl apply` polecenia .

```
kubectl apply -f hello-world-ingress.yaml --namespace ingress-basic
```

Dane wyjściowe powinny być podobne do tego przykładu:

```
ingress.extensions/hello-world-ingress created
```

## <a name="create-a-certificate-object"></a>Tworzenie obiektu certyfikatu

Następnie należy utworzyć zasób certyfikatu. Zasób certyfikatu definiuje żądany certyfikat X.509. Aby uzyskać więcej informacji, zobacz [certyfikaty cert-manager][cert-manager-certificates].

Program Cert-manager prawdopodobnie automatycznie utworzył obiekt certyfikatu przy użyciu podkładki ingress-shim, która jest automatycznie wdrażana za pomocą narzędzia cert-manager od wersji 0.2.2. Aby uzyskać więcej informacji, zobacz dokumentację dotyczącą podkładki [dla danych przychodzących.][ingress-shim]

Aby sprawdzić, czy certyfikat został utworzony pomyślnie, użyj `kubectl describe certificate tls-secret --namespace ingress-basic` polecenia .

Jeśli certyfikat został wystawiony, zobaczysz dane wyjściowe podobne do następujących:
```
Type    Reason          Age   From          Message
----    ------          ----  ----          -------
  Normal  CreateOrder     11m   cert-manager  Created new ACME order, attempting validation...
  Normal  DomainVerified  10m   cert-manager  Domain "demo-aks-ingress.eastus.cloudapp.azure.com" verified with "http-01" validation
  Normal  IssueCert       10m   cert-manager  Issuing certificate...
  Normal  CertObtained    10m   cert-manager  Obtained certificate from ACME server
  Normal  CertIssued      10m   cert-manager  Certificate issued successfully
```

Jeśli musisz utworzyć dodatkowy zasób certyfikatu, możesz to zrobić za pomocą następującego przykładowego manifestu. Zaktualizuj *nazwy dnsName i* *domeny* na nazwę DNS utworzoną w poprzednim kroku. Jeśli używasz kontrolera ruchu wychodzącego tylko do użytku wewnętrznego, określ wewnętrzną nazwę DNS dla usługi.

```yaml
apiVersion: cert-manager.io/v1alpha2
kind: Certificate
metadata:
  name: tls-secret
  namespace: ingress-basic
spec:
  secretName: tls-secret
  dnsNames:
  - demo-aks-ingress.eastus.cloudapp.azure.com
  acme:
    config:
    - http01:
        ingressClass: nginx
      domains:
      - demo-aks-ingress.eastus.cloudapp.azure.com
  issuerRef:
    name: letsencrypt-staging
    kind: ClusterIssuer
```

Aby utworzyć zasób certyfikatu, użyj `kubectl apply` polecenia .

```
$ kubectl apply -f certificates.yaml

certificate.cert-manager.io/tls-secret created
```

## <a name="test-the-ingress-configuration"></a>Testowanie konfiguracji danych przychodzących

Otwórz w przeglądarce internetowej w domenie FQDN kontrolera ruchu przychodzących Kubernetes, taką jak *`https://demo-aks-ingress.eastus.cloudapp.azure.com`* .

W tych przykładach używa się protokołu , dlatego wystawiony `letsencrypt-staging` certyfikat protokołu TLS/SSL nie jest zaufany przez przeglądarkę. Zaakceptuj monit ostrzegawczy, aby kontynuować pracę z aplikacją. Informacje o certyfikacie pokazują, że *ten fałszywy certyfikat X1 pośredniej* wersji LE jest wystawiany przez let's encrypt. Ten fałszywy certyfikat wskazuje, że żądanie zostało prawidłowo `cert-manager` przetworzone i otrzymał certyfikat od dostawcy:

![Zaszyfruj certyfikat przejściowy](media/ingress/staging-certificate.png)

W przypadku zmiany ustawienia Let's Encrypt na używanie zamiast , używany jest zaufany certyfikat wystawiony przez let's encrypt, jak pokazano w `prod` `staging` poniższym przykładzie:

![Let's Encrypt certificate](media/ingress/certificate.png)

Aplikacja demonstracyjna jest wyświetlana w przeglądarce internetowej:

![Przykład aplikacji ( przykładowy)](media/ingress/app-one.png)

Teraz dodaj *ścieżkę /hello-world-two* do sieci FQDN, taką jak *`https://demo-aks-ingress.eastus.cloudapp.azure.com/hello-world-two`* . Zostanie pokazana druga aplikacja demonstracyjna z tytułem niestandardowym:

![Przykład aplikacji 2](media/ingress/app-two.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

W tym artykule program Helm został użyty do zainstalowania składników, certyfikatów i przykładowych aplikacji dla danych przychodzących. Podczas wdrażania planu helm jest tworzona liczba zasobów kubernetes. Te zasoby obejmują zasobniki, wdrożenia i usługi. Aby wyczyścić te zasoby, możesz usunąć całą przykładową przestrzeń nazw lub poszczególne zasoby.

### <a name="delete-the-sample-namespace-and-all-resources"></a>Usuwanie przykładowej przestrzeni nazw i wszystkich zasobów

Aby usunąć całą przykładową przestrzeń nazw, użyj `kubectl delete` polecenia i określ nazwę przestrzeni nazw. Wszystkie zasoby w przestrzeni nazw zostaną usunięte.

```console
kubectl delete namespace ingress-basic
```

### <a name="delete-resources-individually"></a>Pojedyncze usuwanie zasobów

Alternatywnie bardziej szczegółowym podejściem jest usunięcie poszczególnych utworzonych zasobów. Najpierw usuń zasoby certyfikatu:

```console
kubectl delete -f certificates.yaml
kubectl delete -f cluster-issuer.yaml
```

Teraz możesz wyświetlić listę wydań helm za pomocą `helm list` polecenia . Poszukaj wykresów o *nazwach nginx-ingress* i *cert-manager,* jak pokazano w następujących przykładowych danych wyjściowych:

```
$ helm list --all-namespaces

NAME                    NAMESPACE       REVISION        UPDATED                        STATUS          CHART                   APP VERSION
nginx-ingress           ingress-basic   1               2020-01-11 14:51:03.454165006  deployed        nginx-ingress-1.28.2    0.26.2
cert-manager            ingress-basic   1               2020-01-06 21:19:03.866212286  deployed        cert-manager-v0.13.0    v0.13.0
```

Odinstaluj wydania za pomocą `helm uninstall` polecenia . Poniższy przykład odinstalowuje wdrożenia danych przychodzących NGINX i wdrożeń menedżera certyfikatów.

```
$ helm uninstall nginx-ingress cert-manager -n ingress-basic

release "nginx-ingress" deleted
release "cert-manager" deleted
```

Następnie usuń dwie przykładowe aplikacje:

```console
kubectl delete -f aks-helloworld.yaml --namespace ingress-basic
kubectl delete -f ingress-demo.yaml --namespace ingress-basic
```

Usuń samą przestrzeń nazw. Użyj polecenia `kubectl delete` i określ nazwę przestrzeni nazw:

```console
kubectl delete namespace ingress-basic
```

Na koniec usuń statyczny publiczny adres IP utworzony dla kontrolera ruchu wychodzącego. Podaj *nazwę MC_* grupy zasobów klastra uzyskaną w pierwszym kroku tego artykułu, na przykład *MC_myResourceGroup_myAKSCluster_eastus*:

```azurecli-interactive
az network public-ip delete --resource-group MC_myResourceGroup_myAKSCluster_eastus --name myAKSPublicIP
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
- [Tworzenie kontrolera ruchu wychodzącego z dynamicznym publicznym adresem IP i konfigurowanie szyfrowania Let's Encrypt w celu automatycznego generowania certyfikatów TLS][aks-ingress-tls]

<!-- LINKS - external -->
[helm-cli]: ./kubernetes-helm.md
[cert-manager]: https://github.com/jetstack/cert-manager
[cert-manager-certificates]: https://cert-manager.readthedocs.io/en/latest/reference/certificates.html
[cert-manager-cluster-issuer]: https://cert-manager.readthedocs.io/en/latest/reference/clusterissuers.html
[cert-manager-issuer]: https://cert-manager.readthedocs.io/en/latest/reference/issuers.html
[lets-encrypt]: https://letsencrypt.org/
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx
[helm]: https://helm.sh/
[helm-install]: https://docs.helm.sh/using_helm/#installing-helm
[ingress-shim]: https://docs.cert-manager.io/en/latest/tasks/issuing-certificates/ingress-shim.html

<!-- LINKS - internal -->
[use-helm]: kubernetes-helm.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az_aks_show
[az-network-public-ip-create]: /cli/azure/network/public-ip#az_network_public_ip_create
[aks-ingress-internal]: ingress-internal-ip.md
[aks-ingress-basic]: ingress-basic.md
[aks-ingress-tls]: ingress-tls.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-own-tls]: ingress-own-tls.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[client-source-ip]: concepts-network.md#ingress-controllers
[install-azure-cli]: /cli/azure/install-azure-cli
[aks-static-ip]: static-ip.md
