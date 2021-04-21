---
title: Dodatek routingu aplikacji PROTOKOŁU HTTP Azure Kubernetes Service (AKS)
description: Użyj dodatku routingu aplikacji PROTOKOŁU HTTP, aby uzyskać dostęp do aplikacji wdrożonych Azure Kubernetes Service (AKS).
services: container-service
author: lachie83
ms.topic: article
ms.date: 07/20/2020
ms.author: laevenso
ms.openlocfilehash: 95767e94a120b7f4595744beabc72fcd954e6e2f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783111"
---
# <a name="http-application-routing"></a>Routing aplikacji protokołu HTTP

Rozwiązanie routingu aplikacji PROTOKOŁU HTTP ułatwia dostęp do aplikacji wdrożonych w klastrze Azure Kubernetes Service (AKS). Po włączeniu rozwiązania konfiguruje kontroler [ruchu przychodzących w](https://kubernetes.io/docs/concepts/services-networking/ingress-controllers/) klastrze usługi AKS. Podczas wdrażania aplikacji rozwiązanie tworzy również publicznie dostępne nazwy DNS dla punktów końcowych aplikacji.

Po włączeniu dodatku tworzy on strefę DNS w subskrypcji. Aby uzyskać więcej informacji na temat kosztów usługi DNS, zobacz [Cennik usługi DNS.][dns-pricing]

> [!CAUTION]
> Dodatek routingu aplikacji PROTOKOŁU HTTP został zaprojektowany tak, aby umożliwiał szybkie tworzenie kontrolera ruchu wychodzącego i uzyskiwanie dostępu do aplikacji. Ten dodatek nie jest obecnie przeznaczony do użytku w środowisku produkcyjnym i nie jest zalecany do użytku produkcyjnego. W przypadku wdrożeń przychodzących gotowych do użycia w środowisku produkcyjnym, które obejmują wiele replik i obsługę protokołu TLS, zobacz [Tworzenie kontrolera ruchu przychodzących HTTPS.](./ingress-tls.md)

## <a name="http-routing-solution-overview"></a>Omówienie rozwiązania routingu HTTP

Dodatek wdraża dwa składniki: kontroler ruchu przychodzących [Kubernetes][ingress] i [zewnętrzny kontroler DNS.][external-dns]

- **Kontroler ruchu wychodzącego:** kontroler ruchu przychodzących jest narażony na połączenie z Internetem za pomocą usługi Kubernetes typu LoadBalancer. Kontroler ruchu przychodzących obserwuje i implementuje zasoby ruchu przychodzących [Kubernetes][ingress-resource], co tworzy trasy do punktów końcowych aplikacji.
- **Zewnętrzny kontroler DNS:** obserwuje zasoby ruchu przychodzących Kubernetes i tworzy rekordy DNS A w strefie DNS specyficznej dla klastra.

## <a name="deploy-http-routing-cli"></a>Wdrażanie routingu HTTP: interfejs wiersza polecenia

Dodatek routingu aplikacji HTTP można włączyć za pomocą interfejsu wiersza polecenia platformy Azure podczas wdrażania klastra usługi AKS. W tym celu użyj [polecenia az aks create][az-aks-create] z `--enable-addons` argumentem .

```azurecli
az aks create --resource-group myResourceGroup --name myAKSCluster --enable-addons http_application_routing
```

> [!TIP]
> Jeśli chcesz włączyć wiele dodatków, podaj je jako listę rozdzieloną przecinkami. Aby na przykład włączyć routing i monitorowanie aplikacji HTTP, użyj formatu `--enable-addons http_application_routing,monitoring` .

Routing HTTP można również włączyć w istniejącym klastrze usługi AKS za pomocą [polecenia az aks enable-addons.][az-aks-enable-addons] Aby włączyć routing HTTP w istniejącym klastrze, dodaj parametr i określ `--addons` http_application_routing, jak pokazano w poniższym  przykładzie:

```azurecli
az aks enable-addons --resource-group myResourceGroup --name myAKSCluster --addons http_application_routing
```

Po wdrożeniu lub zaktualizowaniu klastra użyj [polecenia az aks show,][az-aks-show] aby pobrać nazwę strefy DNS.

```azurecli
az aks show --resource-group myResourceGroup --name myAKSCluster --query addonProfiles.httpApplicationRouting.config.HTTPApplicationRoutingZoneName -o table
```

Ta nazwa jest potrzebna do wdrożenia aplikacji w klastrze usługi AKS i jest wyświetlana w następujących przykładowych danych wyjściowych:

```console
9f9c1fe7-21a1-416d-99cd-3543bb92e4c3.eastus.aksapp.io
```

## <a name="deploy-http-routing-portal"></a>Wdrażanie routingu HTTP: portal

Dodatek routingu aplikacji HTTP można włączyć za pośrednictwem Azure Portal podczas wdrażania klastra usługi AKS.

![Włączanie funkcji routingu HTTP](media/http-routing/create.png)

Po wdrożeniu klastra przejdź do automatycznie utworzonej grupy zasobów usługi AKS i wybierz strefę DNS. Zanotuj nazwę strefy DNS. Ta nazwa jest potrzebna do wdrożenia aplikacji w klastrze usługi AKS.

![Uzyskiwanie nazwy strefy DNS](media/http-routing/dns.png)

## <a name="connect-to-your-aks-cluster"></a>Nawiązywanie połączenia z klastrem usługi AKS

Aby nawiązać połączenie z klastrem Kubernetes z komputera lokalnego, należy użyć narzędzia [kubectl][kubectl], czyli klienta wiersza polecenia usługi Kubernetes.

Jeśli korzystasz z usługi Azure Cloud Shell, narzędzie `kubectl` jest już zainstalowane. Możesz także zainstalować je lokalnie za pomocą polecenia [az aks install-cli][]:

```azurecli
az aks install-cli
```

Aby skonfigurować narzędzie `kubectl` w celu nawiązania połączenia z klastrem Kubernetes, użyj polecenia [az aks get-credentials][]. Poniższy przykład pobiera poświadczenia dla klastra usługi AKS o nazwie *MyAKSCluster* w *grupie MyResourceGroup:*

```azurecli
az aks get-credentials --resource-group MyResourceGroup --name MyAKSCluster
```

## <a name="use-http-routing"></a>Korzystanie z routingu HTTP

Rozwiązanie routingu aplikacji PROTOKOŁU HTTP może być wyzwalane tylko dla zasobów przychodzących z adnotacjami w następujący sposób:

```yaml
annotations:
  kubernetes.io/ingress.class: addon-http-application-routing
```

Utwórz plik o nazwie **samples-http-application-routing.yaml** i skopiuj go w poniższym pliku YAML. W wierszu 43 zaktualizuj `<CLUSTER_SPECIFIC_DNS_ZONE>` przy użyciu nazwy strefy DNS zebranej w poprzednim kroku tego artykułu.

```yaml
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
---
apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata:
  name: aks-helloworld
  annotations:
    kubernetes.io/ingress.class: addon-http-application-routing
spec:
  rules:
  - host: aks-helloworld.<CLUSTER_SPECIFIC_DNS_ZONE>
    http:
      paths:
      - backend:
          serviceName: aks-helloworld
          servicePort: 80
        path: /
```

Użyj polecenia [kubectl apply,][kubectl-apply] aby utworzyć zasoby.

```bash
kubectl apply -f samples-http-application-routing.yaml
```

W poniższym przykładzie pokazano utworzone zasoby:

```bash
$ kubectl apply -f samples-http-application-routing.yaml

deployment.apps/aks-helloworld created
service/aks-helloworld created
ingress.networking.k8s.io/aks-helloworld created
```

Otwórz w przeglądarce internetowej adres *aks-helloworld. \<CLUSTER_SPECIFIC_DNS_ZONE\>*, na przykład aks-helloworld.9f9c1fe7-21a1-416d-99cd-3543bb92e4c3.eastus.aksapp.io *i* sprawdź, czy widzisz aplikację demonstracyjną. Aplikacja może pojawić się po kilku minutach.

## <a name="remove-http-routing"></a>Usuwanie routingu HTTP

Rozwiązanie routingu HTTP można usunąć przy użyciu interfejsu wiersza polecenia platformy Azure. W tym celu uruchom następujące polecenie, zastępując nazwę klastra i grupy zasobów usługi AKS.

```azurecli
az aks disable-addons --addons http_application_routing --name myAKSCluster --resource-group myResourceGroup --no-wait
```

Gdy dodatek routingu aplikacji HTTP jest wyłączony, niektóre zasoby kubernetes mogą pozostać w klastrze. Te zasoby obejmują *configMaps i* *wpisy tajne* oraz są tworzone w przestrzeni *nazw kube-system.* Aby zachować czysty klaster, możesz usunąć te zasoby.

Poszukaj zasobów *addon-http-application-routing przy* użyciu następujących [poleceń get narzędzia kubectl:][kubectl-get]

```console
kubectl get deployments --namespace kube-system
kubectl get services --namespace kube-system
kubectl get configmaps --namespace kube-system
kubectl get secrets --namespace kube-system
```

Następujące przykładowe dane wyjściowe pokazują configMaps, które powinny zostać usunięte:

```
$ kubectl get configmaps --namespace kube-system

NAMESPACE     NAME                                                       DATA   AGE
kube-system   addon-http-application-routing-nginx-configuration         0      9m7s
kube-system   addon-http-application-routing-tcp-services                0      9m7s
kube-system   addon-http-application-routing-udp-services                0      9m7s
```

Aby usunąć zasoby, użyj [polecenia kubectl delete.][kubectl-delete] Określ typ zasobu, nazwę zasobu i przestrzeń nazw. Poniższy przykład usuwa jedną z poprzednich map konfiguracji:

```console
kubectl delete configmaps addon-http-application-routing-nginx-configuration --namespace kube-system
```

Powtórz poprzedni krok dla wszystkich zasobów `kubectl delete` *addon-http-application-routingu,* które pozostały w klastrze.

## <a name="troubleshoot"></a>Rozwiązywanie problemów

Użyj polecenia [kubectl logs,][kubectl-logs] aby wyświetlić dzienniki aplikacji dla aplikacji External-DNS. Dzienniki powinny potwierdzić, że pomyślnie utworzono rekord DNS A i TXT.

```
$ kubectl logs -f deploy/addon-http-application-routing-external-dns -n kube-system

time="2018-04-26T20:36:19Z" level=info msg="Updating A record named 'aks-helloworld' to '52.242.28.189' for Azure DNS zone '471756a6-e744-4aa0-aa01-89c4d162a7a7.canadaeast.aksapp.io'."
time="2018-04-26T20:36:21Z" level=info msg="Updating TXT record named 'aks-helloworld' to '"heritage=external-dns,external-dns/owner=default"' for Azure DNS zone '471756a6-e744-4aa0-aa01-89c4d162a7a7.canadaeast.aksapp.io'."
```

Te rekordy można również zobaczyć w zasobie strefy DNS w Azure Portal.

![Uzyskiwanie rekordów DNS](media/http-routing/clippy.png)

Użyj polecenia [kubectl logs,][kubectl-logs] aby wyświetlić dzienniki aplikacji dla kontrolera ruchu przychodzących Nginx. Dzienniki powinny `CREATE` potwierdzić, że zasób ruchu przychodzących i ponowne załadowanie kontrolera. Wszystkie działania HTTP są rejestrowane.

```bash
$ kubectl logs -f deploy/addon-http-application-routing-nginx-ingress-controller -n kube-system

-------------------------------------------------------------------------------
NGINX Ingress controller
  Release:    0.13.0
  Build:      git-4bc943a
  Repository: https://github.com/kubernetes/ingress-nginx
-------------------------------------------------------------------------------

I0426 20:30:12.212936       9 flags.go:162] Watching for ingress class: addon-http-application-routing
W0426 20:30:12.213041       9 flags.go:165] only Ingress with class "addon-http-application-routing" will be processed by this ingress controller
W0426 20:30:12.213505       9 client_config.go:533] Neither --kubeconfig nor --master was specified.  Using the inClusterConfig.  This might not work.
I0426 20:30:12.213752       9 main.go:181] Creating API client for https://10.0.0.1:443
I0426 20:30:12.287928       9 main.go:225] Running in Kubernetes Cluster version v1.8 (v1.8.11) - git (clean) commit 1df6a8381669a6c753f79cb31ca2e3d57ee7c8a3 - platform linux/amd64
I0426 20:30:12.290988       9 main.go:84] validated kube-system/addon-http-application-routing-default-http-backend as the default backend
I0426 20:30:12.294314       9 main.go:105] service kube-system/addon-http-application-routing-nginx-ingress validated as source of Ingress status
I0426 20:30:12.426443       9 stat_collector.go:77] starting new nginx stats collector for Ingress controller running in namespace  (class addon-http-application-routing)
I0426 20:30:12.426509       9 stat_collector.go:78] collector extracting information from port 18080
I0426 20:30:12.448779       9 nginx.go:281] starting Ingress controller
I0426 20:30:12.463585       9 event.go:218] Event(v1.ObjectReference{Kind:"ConfigMap", Namespace:"kube-system", Name:"addon-http-application-routing-nginx-configuration", UID:"2588536c-4990-11e8-a5e1-0a58ac1f0ef2", APIVersion:"v1", ResourceVersion:"559", FieldPath:""}): type: 'Normal' reason: 'CREATE' ConfigMap kube-system/addon-http-application-routing-nginx-configuration
I0426 20:30:12.466945       9 event.go:218] Event(v1.ObjectReference{Kind:"ConfigMap", Namespace:"kube-system", Name:"addon-http-application-routing-tcp-services", UID:"258ca065-4990-11e8-a5e1-0a58ac1f0ef2", APIVersion:"v1", ResourceVersion:"561", FieldPath:""}): type: 'Normal' reason: 'CREATE' ConfigMap kube-system/addon-http-application-routing-tcp-services
I0426 20:30:12.467053       9 event.go:218] Event(v1.ObjectReference{Kind:"ConfigMap", Namespace:"kube-system", Name:"addon-http-application-routing-udp-services", UID:"259023bc-4990-11e8-a5e1-0a58ac1f0ef2", APIVersion:"v1", ResourceVersion:"562", FieldPath:""}): type: 'Normal' reason: 'CREATE' ConfigMap kube-system/addon-http-application-routing-udp-services
I0426 20:30:13.649195       9 nginx.go:302] starting NGINX process...
I0426 20:30:13.649347       9 leaderelection.go:175] attempting to acquire leader lease  kube-system/ingress-controller-leader-addon-http-application-routing...
I0426 20:30:13.649776       9 controller.go:170] backend reload required
I0426 20:30:13.649800       9 stat_collector.go:34] changing prometheus collector from  to default
I0426 20:30:13.662191       9 leaderelection.go:184] successfully acquired lease kube-system/ingress-controller-leader-addon-http-application-routing
I0426 20:30:13.662292       9 status.go:196] new leader elected: addon-http-application-routing-nginx-ingress-controller-5cxntd6
I0426 20:30:13.763362       9 controller.go:179] ingress backend successfully reloaded...
I0426 21:51:55.249327       9 event.go:218] Event(v1.ObjectReference{Kind:"Ingress", Namespace:"default", Name:"aks-helloworld", UID:"092c9599-499c-11e8-a5e1-0a58ac1f0ef2", APIVersion:"extensions", ResourceVersion:"7346", FieldPath:""}): type: 'Normal' reason: 'CREATE' Ingress default/aks-helloworld
W0426 21:51:57.908771       9 controller.go:775] service default/aks-helloworld does not have any active endpoints
I0426 21:51:57.908951       9 controller.go:170] backend reload required
I0426 21:51:58.042932       9 controller.go:179] ingress backend successfully reloaded...
167.220.24.46 - [167.220.24.46] - - [26/Apr/2018:21:53:20 +0000] "GET / HTTP/1.1" 200 234 "" "Mozilla/5.0 (compatible; MSIE 9.0; Windows NT 6.1; Trident/5.0)" 197 0.001 [default-aks-helloworld-80] 10.244.0.13:8080 234 0.004 200
```

## <a name="clean-up"></a>Czyszczenie

Usuń skojarzone obiekty Kubernetes utworzone w tym artykule przy `kubectl delete` użyciu .

```bash
kubectl delete -f samples-http-application-routing.yaml
```

Przykładowe dane wyjściowe pokazują, że obiekty kubernetes zostały usunięte.

```bash
$ kubectl delete -f samples-http-application-routing.yaml

deployment "aks-helloworld" deleted
service "aks-helloworld" deleted
ingress "aks-helloworld" deleted
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać informacje na temat sposobu instalowania kontrolera ruchu wychodzącego zabezpieczonego przy użyciu protokołu HTTPS w uwitrynie AKS, zobacz [Https Ingress on Azure Kubernetes Service (AKS) (Ruch przychodzący HTTPS na platformie Azure Kubernetes Service (AKS).][ingress-https]

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-show]: /cli/azure/aks#az_aks_show
[ingress-https]: ./ingress-tls.md
[az-aks-enable-addons]: /cli/azure/aks#az_aks_enable_addons
[az aks install-cli]: /cli/azure/aks#az_aks_install_cli
[az aks get-credentials]: /cli/azure/aks#az_aks_get_credentials

<!-- LINKS - external -->
[dns-pricing]: https://azure.microsoft.com/pricing/details/dns/
[external-dns]: https://github.com/kubernetes-incubator/external-dns
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-logs]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs
[ingress]: https://kubernetes.io/docs/concepts/services-networking/ingress/
[ingress-resource]: https://kubernetes.io/docs/concepts/services-networking/ingress/#the-ingress-resource
