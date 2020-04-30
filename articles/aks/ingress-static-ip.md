---
title: Używanie kontrolera transferu danych przychodzących ze statycznym adresem IP
titleSuffix: Azure Kubernetes Service
description: Dowiedz się, jak zainstalować i skonfigurować kontroler transferu danych przychodzących NGINX ze statycznym publicznym adresem IP w klastrze usługi Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 05/24/2019
ms.openlocfilehash: f0a8f1f1e1b724745e69aef30e2e6404ff6a5484
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82207364"
---
# <a name="create-an-ingress-controller-with-a-static-public-ip-address-in-azure-kubernetes-service-aks"></a>Utwórz kontroler transferu danych przychodzących ze statycznym publicznym adresem IP w usłudze Azure Kubernetes Service (AKS)

Kontroler ruchu przychodzącego to element oprogramowania dostarczający odwrotny serwer proxy, konfigurowalne trasowanie ruchu oraz zakończenie protokołu TLS dla usług Kubernetes. Zasoby ruchu przychodzącego usług Kubernetes są używane do skonfigurowania zasad ruchu przychodzącego oraz tras dla poszczególnych usług Kubernetes. Dzięki korzystaniu z kontrolera ruchu przychodzącego oraz zasad ruchu przychodzącego można użyć jednego adresu IP do trasowania ruchu w wielu usługach w klastrze Kubernetes.

W tym artykule pokazano, jak wdrożyć [kontroler Nginx Ingress][nginx-ingress] w klastrze usługi Azure Kubernetes Service (AKS). Kontroler transferu danych przychodzących jest skonfigurowany ze statycznym publicznym adresem IP. Projekt [Menedżera certyfikatów][cert-manager] służy do automatycznego generowania i konfigurowania certyfikatów [szyfrowania][lets-encrypt] . Na koniec dwie aplikacje są uruchamiane w klastrze AKS, z których każdy jest dostępny za pośrednictwem jednego adresu IP.

Możesz również wykonać następujące czynności:

- [Tworzenie podstawowego kontrolera danych wejściowych z łącznością sieci zewnętrznej][aks-ingress-basic]
- [Włącz dodatek routingu aplikacji protokołu HTTP][aks-http-app-routing]
- [Tworzenie kontrolera transferu danych przychodzących korzystającego z własnych certyfikatów TLS][aks-ingress-own-tls]
- [Utwórz kontroler transferu danych przychodzących, który używa szyfrowania, aby automatycznie generować certyfikaty TLS z dynamicznym publicznym adresem IP][aks-ingress-tls]

## <a name="before-you-begin"></a>Przed rozpoczęciem

W tym artykule przyjęto założenie, że masz istniejący klaster AKS. Jeśli potrzebujesz klastra AKS, zapoznaj się z przewodnikiem Szybki Start AKS [przy użyciu interfejsu wiersza polecenia platformy Azure][aks-quickstart-cli] lub [przy użyciu Azure Portal][aks-quickstart-portal].

W tym artykule zastosowano [Helm 3][helm] do zainstalowania kontrolera transferu danych przychodzących Nginx, Menedżera certyfikatów i przykładowej aplikacji sieci Web. Upewnij się, że korzystasz z najnowszej wersji programu Helm. Instrukcje dotyczące uaktualniania można znaleźć w dokumentacji [Helm Install][helm-install]. Aby uzyskać więcej informacji na temat konfigurowania i używania Helm, zobacz [Install Applications with Helm in Azure Kubernetes Service (AKS)][use-helm].

Ten artykuł wymaga również uruchomienia interfejsu wiersza polecenia platformy Azure w wersji 2.0.64 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli-install].

## <a name="create-an-ingress-controller"></a>Tworzenie kontrolera transferu danych przychodzących

Domyślnie kontroler transferu danych przychodzących NGINX jest tworzony z nowym przypisaniem publicznego adresu IP. Ten publiczny adres IP jest tylko statyczny dla okresu życia kontrolera transferu danych przychodzących i zostaje utracony, Jeśli kontroler został usunięty i utworzony. Typowym wymaganiem dotyczącym konfiguracji jest zapewnienie, że kontroler transferu danych przychodzących NGINX istniejący statyczny publiczny adres IP. Statyczny publiczny adres IP pozostaje w przypadku usunięcia kontrolera transferu danych przychodzących. Takie podejście umożliwia używanie istniejących rekordów DNS i konfiguracji sieci w spójny sposób w całym cyklu życia aplikacji.

Jeśli musisz utworzyć statyczny publiczny adres IP, najpierw Pobierz nazwę grupy zasobów klastra AKS za pomocą polecenia [AZ AKS show][az-aks-show] :

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv
```

Następnie utwórz publiczny adres IP za pomocą metody alokacji *statycznej* przy użyciu polecenia [AZ Network Public-IP Create][az-network-public-ip-create] . Poniższy przykład tworzy publiczny adres IP o nazwie *myAKSPublicIP* w grupie zasobów klastra AKS uzyskanym w poprzednim kroku:

```azurecli-interactive
az network public-ip create --resource-group MC_myResourceGroup_myAKSCluster_eastus --name myAKSPublicIP --sku Standard --allocation-method static --query publicIp.ipAddress -o tsv
```

Teraz Wdróż wykres *Nginx-* transferal z Helm. W celu zwiększenia nadmiarowości za pomocą parametru `--set controller.replicaCount` wdrażane są dwie repliki kontrolerów wejściowych NGINX. Aby w pełni korzystać z uruchamiania replik kontrolera transferu danych przychodzących, upewnij się, że w klastrze AKS znajduje się więcej niż jeden węzeł.

Do wydania Helm należy przekazać dwa dodatkowe parametry, aby kontroler transferu danych przychodzących był świadomy zarówno statycznego adresu IP modułu równoważenia obciążenia, który ma zostać przydzielony do usługi kontrolera transferu danych przychodzących, jak i etykiety nazwy DNS stosowanej do zasobu publicznego adresu IP. Aby certyfikaty HTTPS działały prawidłowo, etykieta nazwy DNS jest używana do konfigurowania nazwy FQDN dla adresu IP kontrolera transferu danych przychodzących.

1. Dodaj `--set controller.service.loadBalancerIP` parametr. Określ własny publiczny adres IP, który został utworzony w poprzednim kroku.
1. Dodaj `--set controller.service.annotations."service\.beta\.kubernetes\.io/azure-dns-label-name"` parametr. Określ etykietę nazwy DNS, która ma zostać zastosowana do publicznego adresu IP, który został utworzony w poprzednim kroku.

Kontroler wejściowy należy również zaplanować w węźle z systemem Linux. Nie należy go uruchamiać w węzłach z systemem Windows Server. Za pomocą parametru `--set nodeSelector` podaje się selektor węzła, który nakazuje harmonogramowi usługi Kubernetes uruchomienie kontrolera wejściowego NGINX w węźle opartym na systemie Linux.

> [!TIP]
> Poniższy przykład tworzy przestrzeń nazw Kubernetes dla zasobów przychodzących o nazwie transfery *-Basic*. W razie potrzeby określ przestrzeń nazw dla własnego środowiska. Jeśli klaster AKS nie jest włączony RBAC, Dodaj `--set rbac.create=false` do poleceń Helm.

> [!TIP]
> Jeśli chcesz włączyć [zachowywanie źródłowych adresów IP klienta][client-source-ip] dla żądań do kontenerów w klastrze, Dodaj `--set controller.service.externalTrafficPolicy=Local` do polecenia instalacji Helm. Adres IP źródła klienta jest przechowywany w nagłówku żądania w obszarze *X-forwardd-for*. W przypadku korzystania z kontrolera transferu danych przychodzących z włączonym zachowywaniem źródłowych adresów IP klienta protokół TLS nie będzie działał.

Zaktualizuj następujący skrypt przy użyciu **adresu IP** kontrolera transferu danych przychodzących i **unikatowej nazwy** , która ma być używana dla prefiksu nazwy FQDN:

```console
# Create a namespace for your ingress resources
kubectl create namespace ingress-basic

# Use Helm to deploy an NGINX ingress controller
helm install nginx-ingress stable/nginx-ingress \
    --namespace ingress-basic \
    --set controller.replicaCount=2 \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set controller.service.loadBalancerIP="40.121.63.72"
    --set controller.service.annotations."service\.beta\.kubernetes\.io/azure-dns-label-name"="demo-aks-ingress"
```

Gdy usługa równoważenia obciążenia Kubernetes jest tworzona dla kontrolera NGINX, zostanie przypisany statyczny adres IP, jak pokazano w następujących przykładowych danych wyjściowych:

```
$ kubectl get service -l app=nginx-ingress --namespace ingress-basic

NAME                                        TYPE           CLUSTER-IP    EXTERNAL-IP    PORT(S)                      AGE
nginx-ingress-controller                    LoadBalancer   10.0.232.56   40.121.63.72   80:31978/TCP,443:32037/TCP   3m
nginx-ingress-default-backend               ClusterIP      10.0.95.248   <none>         80/TCP                       3m
```

Nie utworzono jeszcze żadnych reguł dotyczących ruchu przychodzącego, dlatego po przejściu do publicznego adresu IP zostanie wyświetlona domyślna strona 404 kontrolera NGINX. Reguły dotyczące transferu danych przychodzących są konfigurowane w poniższych krokach.

Aby sprawdzić, czy etykieta nazwy DNS została zastosowana, zbadaj nazwę FQDN na publicznym adresie IP w następujący sposób:

```azurecli-interactive
#!/bin/bash
az network public-ip list --resource-group MC_myResourceGroup_myAKSCluster_eastus --query $("[?name=='myAKSPublicIP'].[dnsSettings.fqdn]") -o tsv
```

Kontroler transferu danych przychodzących jest teraz dostępny za pomocą adresu IP lub nazwy FQDN.

## <a name="install-cert-manager"></a>Instalowanie narzędzia cert-manager

Kontroler ruchu przychodzącego NGINX obsługuje kończenie żądań protokołu TLS. Istnieje kilka sposobów pobierania i konfigurowania certyfikatów protokołu HTTPS. W tym artykule pokazano, jak korzystać z [Menedżera certyfikatów][cert-manager], który umożliwia automatyczne [szyfrowanie][lets-encrypt] funkcji generowania certyfikatów i zarządzania nimi.

> [!NOTE]
> W tym artykule jest `staging` stosowane środowisko do szyfrowania. W przypadku wdrożeń produkcyjnych należy `letsencrypt-prod` używać `https://acme-v02.api.letsencrypt.org/directory` i w definicjach zasobów oraz podczas instalowania wykresu Helm.

Aby zainstalować kontroler Menedżera certyfikatów w klastrze z obsługą RBAC, użyj następującego `helm install` polecenia:

```console
# Install the CustomResourceDefinition resources separately
kubectl apply --validate=false -f https://raw.githubusercontent.com/jetstack/cert-manager/release-0.13/deploy/manifests/00-crds.yaml

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
  --version v0.13.0 \
  jetstack/cert-manager
```

Aby uzyskać więcej informacji na temat konfiguracji Menedżera certyfikatów, zobacz [projekt Menedżera certyfikatów][cert-manager].

## <a name="create-a-ca-cluster-issuer"></a>Tworzenie wystawcy klastra urzędu certyfikacji

Aby można było wystawiać certyfikaty, Menedżer certyfikatów wymaga zasobu [wystawcy][cert-manager-issuer] lub [ClusterIssuer][cert-manager-cluster-issuer] . Te zasoby Kubernetes są identyczne w ramach funkcjonalności, `Issuer` jednak działają w pojedynczej przestrzeni nazw `ClusterIssuer` i działają we wszystkich przestrzeniach nazw. Aby uzyskać więcej informacji, zobacz dokumentację [wystawcy Menedżera certyfikatów][cert-manager-issuer] .

Utwórz wystawcę klastra, na `cluster-issuer.yaml`przykład korzystając z następującego przykładowego manifestu. Zaktualizuj adres e-mail przy użyciu prawidłowego adresu z Twojej organizacji:

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
```

Aby utworzyć wystawcę, użyj `kubectl apply -f cluster-issuer.yaml` polecenia.

```
$ kubectl apply -f cluster-issuer.yaml --namespace ingress-basic

clusterissuer.cert-manager.io/letsencrypt-staging created
```

## <a name="run-demo-applications"></a>Uruchom aplikacje demonstracyjne

Skonfigurowano kontroler transferu danych przychodzących i rozwiązanie do zarządzania certyfikatami. Teraz Uruchommy dwie aplikacje demonstracyjne w klastrze AKS. W tym przykładzie Helm jest używany do wdrażania dwóch wystąpień prostej aplikacji "Hello World".

Przed zainstalowaniem przykładowych wykresów Helm należy dodać repozytorium przykładów platformy Azure do środowiska Helm w następujący sposób:

```console
helm repo add azure-samples https://azure-samples.github.io/helm-charts/
```

Utwórz pierwszą aplikację demonstracyjną z wykresu Helm za pomocą następującego polecenia:

```console
helm install aks-helloworld azure-samples/aks-helloworld --namespace ingress-basic
```

Teraz Zainstaluj drugie wystąpienie aplikacji demonstracyjnej. Dla drugiego wystąpienia należy określić nowy tytuł, tak aby dwie aplikacje były wizualnie różne. Należy również określić unikatową nazwę usługi:

```console
helm install aks-helloworld-2 azure-samples/aks-helloworld \
    --namespace ingress-basic \
    --set title="AKS Ingress Demo" \
    --set serviceName="ingress-demo"
```

## <a name="create-an-ingress-route"></a>Tworzenie trasy transferu danych przychodzących

Obie aplikacje są teraz uruchomione w klastrze Kubernetes, ale są skonfigurowane za pomocą usługi typu `ClusterIP`. W związku z tym aplikacje nie są dostępne z Internetu. Aby udostępnić je publicznie, utwórz zasób Kubernetes. Zasób danych przychodzących konfiguruje reguły, które kierują ruch do jednej z dwóch aplikacji.

W poniższym przykładzie ruch do adresu `https://demo-aks-ingress.eastus.cloudapp.azure.com/` jest kierowany do usługi o nazwie. `aks-helloworld` Ruch do adresu `https://demo-aks-ingress.eastus.cloudapp.azure.com/hello-world-two` jest kierowany do `ingress-demo` usługi. Zaktualizuj *hosty* i *host* na nazwę DNS utworzoną w poprzednim kroku.

Utwórz plik o nazwie `hello-world-ingress.yaml` i skopiuj w poniższym przykładzie YAML.

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  annotations:
    kubernetes.io/ingress.class: nginx
    cert-manager.io/cluster-issuer: letsencrypt-staging
    nginx.ingress.kubernetes.io/rewrite-target: /$1
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
        path: /(.*)
      - backend:
          serviceName: ingress-demo
          servicePort: 80
        path: /hello-world-two(/|$)(.*)
```

Utwórz zasób transferu danych przychodzących przy użyciu `kubectl apply -f hello-world-ingress.yaml --namespace ingress-basic` polecenia.

```
$ kubectl apply -f hello-world-ingress.yaml --namespace ingress-basic

ingress.extensions/hello-world-ingress created
```

## <a name="create-a-certificate-object"></a>Tworzenie obiektu certyfikatu

Następnie należy utworzyć zasób certyfikatu. Zasób certyfikatu definiuje żądany certyfikat X. 509. Aby uzyskać więcej informacji, zobacz [Certyfikaty Menedżera][cert-manager-certificates]certyfikatów.

Menedżer certyfikatów prawdopodobnie automatycznie utworzył obiekt certyfikatu przy użyciu funkcji transferu danych przychodzących-podkładki, która jest automatycznie wdrażana z menedżerem certyfikatów od wersji 0.2.2. Aby uzyskać więcej informacji, zapoznaj się z [dokumentacją dotyczącą podkładki][ingress-shim].

Aby sprawdzić, czy certyfikat został utworzony pomyślnie, użyj `kubectl describe certificate tls-secret --namespace ingress-basic` polecenia.

Jeśli certyfikat został wystawiony, zostaną wyświetlone dane wyjściowe podobne do następujących:
```
Type    Reason          Age   From          Message
----    ------          ----  ----          -------
  Normal  CreateOrder     11m   cert-manager  Created new ACME order, attempting validation...
  Normal  DomainVerified  10m   cert-manager  Domain "demo-aks-ingress.eastus.cloudapp.azure.com" verified with "http-01" validation
  Normal  IssueCert       10m   cert-manager  Issuing certificate...
  Normal  CertObtained    10m   cert-manager  Obtained certificate from ACME server
  Normal  CertIssued      10m   cert-manager  Certificate issued successfully
```

Jeśli musisz utworzyć dodatkowy zasób certyfikatu, możesz to zrobić z poniższym przykładowym manifestem. Zaktualizuj *dnsNames* i *domeny* do nazwy DNS utworzonej w poprzednim kroku. W przypadku korzystania z kontrolera danych wejściowych tylko wewnętrznego należy określić wewnętrzną nazwę DNS usługi.

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

Aby utworzyć zasób certyfikatu, użyj `kubectl apply -f certificates.yaml` polecenia.

```
$ kubectl apply -f certificates.yaml

certificate.cert-manager.io/tls-secret created
```

## <a name="test-the-ingress-configuration"></a>Testowanie konfiguracji transferu danych przychodzących

Otwórz przeglądarkę internetową, aby wyświetlić nazwę FQDN kontrolera Kubernetes Ingres, na przykład *`https://demo-aks-ingress.eastus.cloudapp.azure.com`*.

Jak te przykłady używają `letsencrypt-staging`, wystawiony certyfikat TLS/SSL nie jest zaufany przez przeglądarkę. Zaakceptuj monit ostrzegawczy, aby przejść do aplikacji. Informacje o certyfikacie przedstawiają ten sztuczny *pośredni certyfikat x1* wystawiony przezmy szyfrowanie. Ten fałszywy certyfikat `cert-manager` wskazuje na prawidłowe przetworzenie żądania i odebranie certyfikatu od dostawcy:

![Szyfrujmy certyfikat przemieszczania](media/ingress/staging-certificate.png)

W przypadku zmiany szyfrowania do użycia `prod` zamiast `staging`programu zostanie użyty zaufany certyfikat wystawiony przez szyfrowanie, jak pokazano w następującym przykładzie:

![Szyfrujmy certyfikat](media/ingress/certificate.png)

Aplikacja demonstracyjna zostanie wyświetlona w przeglądarce sieci Web:

![Przykład aplikacji jeden](media/ingress/app-one.png)

Teraz dodaj ścieżkę */Hello-World-Two* do nazwy FQDN, takiej jak *`https://demo-aks-ingress.eastus.cloudapp.azure.com/hello-world-two`*. Zostanie wyświetlona druga aplikacja demonstracyjna z tytułem niestandardowym:

![Przykład aplikacji dwa](media/ingress/app-two.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

W tym artykule użyto Helm do zainstalowania składników przychodzących, certyfikatów i przykładowych aplikacji. Po wdrożeniu wykresu Helm są tworzone różne zasoby Kubernetes. Te zasoby obejmują między innymi te, wdrożenia i usługi. Aby wyczyścić te zasoby, można usunąć całą przykładową przestrzeń nazw lub poszczególne zasoby.

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

Alternatywnie, bardziej szczegółowe podejście polega na usunięciu utworzonych poszczególnych zasobów. Najpierw usuń zasoby certyfikatu:

```console
kubectl delete -f certificates.yaml
kubectl delete -f cluster-issuer.yaml
```

Teraz Utwórz listę wersji Helm za pomocą `helm list` polecenia. Poszukaj wykresów o nazwie *Nginx-Ingress*, *CERT-Manager*i *AKS-HelloWorld*, jak pokazano w następujących przykładowych danych wyjściowych:

```
$ helm list --all-namespaces

NAME                    NAMESPACE       REVISION        UPDATED                        STATUS          CHART                   APP VERSION
aks-helloworld          ingress-basic   1               2020-01-11 15:02:21.51172346   deployed        aks-helloworld-0.1.0
aks-helloworld-2        ingress-basic   1               2020-01-11 15:03:10.533465598  deployed        aks-helloworld-0.1.0
nginx-ingress           ingress-basic   1               2020-01-11 14:51:03.454165006  deployed        nginx-ingress-1.28.2    0.26.2
cert-manager            ingress-basic    1               2020-01-06 21:19:03.866212286  deployed        cert-manager-v0.13.0    v0.13.0
```

Usuń wydania za pomocą `helm uninstall` polecenia. Poniższy przykład usuwa wdrożenie NGINX, Menedżer certyfikatów oraz dwie przykładowe aplikacje AKS Hello World.

```
$ helm uninstall aks-helloworld aks-helloworld-2 nginx-ingress cert-manager -n ingress-basic

release "aks-helloworld" deleted
release "aks-helloworld-2" deleted
release "nginx-ingress" deleted
release "cert-manager" deleted
```

Następnie usuń repozytorium Helm dla aplikacji AKS Hello World:

```console
helm repo remove azure-samples
```

Usuń samą przestrzeń nazw. Użyj `kubectl delete` polecenia i określ nazwę przestrzeni nazw:

```console
kubectl delete namespace ingress-basic
```

Na koniec Usuń statyczny publiczny adres IP utworzony dla kontrolera transferu danych przychodzących. Podaj nazwę grupy zasobów klastra *MC_* uzyskaną w pierwszym kroku tego artykułu, na przykład *MC_myResourceGroup_myAKSCluster_eastus*:

```azurecli-interactive
az network public-ip delete --resource-group MC_myResourceGroup_myAKSCluster_eastus --name myAKSPublicIP
```

## <a name="next-steps"></a>Następne kroki

W tym artykule uwzględniono niektóre składniki zewnętrzne do AKS. Aby dowiedzieć się więcej o tych składnikach, zobacz następujące strony projektu:

- [Interfejs wiersza polecenia Helm][helm-cli]
- [Kontroler transferu danych przychodzących NGINX][nginx-ingress]
- [Menedżer certyfikatów][cert-manager]

Możesz również wykonać następujące czynności:

- [Tworzenie podstawowego kontrolera danych wejściowych z łącznością sieci zewnętrznej][aks-ingress-basic]
- [Włącz dodatek routingu aplikacji protokołu HTTP][aks-http-app-routing]
- [Utwórz kontroler transferu danych przychodzących, który używa wewnętrznej, prywatnej sieci i adresu IP][aks-ingress-internal]
- [Tworzenie kontrolera transferu danych przychodzących korzystającego z własnych certyfikatów TLS][aks-ingress-own-tls]
- [Utwórz kontroler transferu danych przychodzących z dynamicznym publicznym adresem IP, a następnie skonfiguruj szyfrowanie, aby automatycznie generować certyfikaty TLS][aks-ingress-tls]

<!-- LINKS - external -->
[helm-cli]: https://docs.microsoft.com/azure/aks/kubernetes-helm
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
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[aks-ingress-internal]: ingress-internal-ip.md
[aks-ingress-basic]: ingress-basic.md
[aks-ingress-tls]: ingress-tls.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-own-tls]: ingress-own-tls.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[client-source-ip]: concepts-network.md#ingress-controllers
[install-azure-cli]: /cli/azure/install-azure-cli
