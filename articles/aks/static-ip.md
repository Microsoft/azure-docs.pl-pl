---
title: Używanie statycznego adresu IP z usługą równoważenia obciążenia
titleSuffix: Azure Kubernetes Service
description: Dowiedz się, jak utworzyć statyczny adres IP i używać go w usłudze równoważenia obciążenia usługi Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 11/14/2020
ms.openlocfilehash: 22fd099633556fa9ddce575c2ac238b4950667cb
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94651893"
---
# <a name="use-a-static-public-ip-address-and-dns-label-with-the-azure-kubernetes-service-aks-load-balancer"></a>Używanie statycznego publicznego adresu IP i etykiety DNS w usłudze Azure Kubernetes Service (AKS)

Domyślnie publiczny adres IP przypisany do zasobu modułu równoważenia obciążenia utworzonego przez klaster AKS jest prawidłowy tylko dla cykl życia tego zasobu. Usunięcie usługi Kubernetes spowoduje również usunięcie powiązanego modułu równoważenia obciążenia i adresu IP. Jeśli chcesz przypisać określony adres IP lub zachować adres IP dla ponownie wdrożonych usług Kubernetes, możesz utworzyć statyczny publiczny adres IP i używać go.

W tym artykule opisano sposób tworzenia statycznego publicznego adresu IP i przypisywania go do usługi Kubernetes.

## <a name="before-you-begin"></a>Zanim rozpoczniesz

W tym artykule przyjęto założenie, że masz istniejący klaster AKS. Jeśli potrzebujesz klastra AKS, zapoznaj się z przewodnikiem Szybki Start AKS [przy użyciu interfejsu wiersza polecenia platformy Azure][aks-quickstart-cli] lub [przy użyciu Azure Portal][aks-quickstart-portal].

Konieczne jest również zainstalowanie i skonfigurowanie interfejsu wiersza polecenia platformy Azure w wersji 2.0.59 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][install-azure-cli].

W tym artykule opisano użycie *standardowego* adresu IP jednostki SKU przy użyciu modułu równoważenia obciążenia ze *standardową* jednostką SKU. Aby uzyskać więcej informacji, zobacz [typy adresów IP i metody alokacji na platformie Azure][ip-sku].

## <a name="create-a-static-ip-address"></a>Tworzenie statycznego adresu IP

Utwórz statyczny publiczny adres IP za pomocą polecenia [AZ Network Public IP Create][az-network-public-ip-create] . Poniżej przedstawiono tworzenie zasobu statyczny adres IP o nazwie *myAKSPublicIP* w grupie zasobów zasobu *webresources* :

```azurecli-interactive
az network public-ip create \
    --resource-group myResourceGroup \
    --name myAKSPublicIP \
    --sku Standard \
    --allocation-method static
```

> [!NOTE]
> Jeśli używasz *podstawowego* modułu równoważenia obciążenia SKU w klastrze AKS, użyj opcji *podstawowa* dla parametru *SKU* podczas definiowania publicznego adresu IP. Tylko *podstawowe* adresy IP jednostki SKU współpracują z usługą równoważenia obciążenia *podstawowej* jednostki SKU i tylko *standardowe* adresy IP jednostki SKU współpracują ze *standardowymi* modułami równoważenia obciążenia jednostki SKU. 

Wyświetlany jest adres IP, jak pokazano w następujących wąskich przykładowych danych wyjściowych:

```json
{
  "publicIp": {
    ...
    "ipAddress": "40.121.183.52",
    ...
  }
}
```

Możesz później uzyskać publiczny adres IP za pomocą polecenia [AZ Network Public-IP list][az-network-public-ip-list] . Określ nazwę grupy zasobów węzła i utworzonego publicznego adresu IP, a następnie zapytaj o *adres* IP, jak pokazano w następującym przykładzie:

```azurecli-interactive
$ az network public-ip show --resource-group myResourceGroup --name myAKSPublicIP --query ipAddress --output tsv

40.121.183.52
```

## <a name="create-a-service-using-the-static-ip-address"></a>Tworzenie usługi przy użyciu statycznego adresu IP

Przed utworzeniem usługi upewnij się, że nazwa główna usługi używana przez klaster AKS ma delegowane uprawnienia do innej grupy zasobów. Na przykład:

```azurecli-interactive
az role assignment create \
    --assignee <SP Client ID> \
    --role "Network Contributor" \
    --scope /subscriptions/<subscription id>/resourceGroups/<resource group name>
```

Alternatywnie można użyć tożsamości zarządzanej przypisanej przez system do uprawnień zamiast nazwy głównej usługi. Aby uzyskać więcej informacji, zobacz [Korzystanie z tożsamości zarządzanych](use-managed-identity.md).

> [!IMPORTANT]
> W przypadku dostosowania wychodzącego adresu IP upewnij się, że tożsamość klastra ma uprawnienia zarówno do wychodzącego publicznego adresu IP, jak i tego przychodzącego publicznego adresu IP.

Aby utworzyć usługę *równoważenia obciążenia* przy użyciu statycznego publicznego adresu IP, Dodaj `loadBalancerIP` Właściwość i wartość statycznego publicznego adresu IP do manifestu YAML. Utwórz plik o nazwie `load-balancer-service.yaml` i skopiuj w następującym YAML. Podaj własny publiczny adres IP utworzony w poprzednim kroku. Poniższy przykład ustawia również adnotację z grupą zasobów o nazwie Moja *zasobów*. Podaj własną nazwę grupy zasobów.

```yaml
apiVersion: v1
kind: Service
metadata:
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-resource-group: myResourceGroup
  name: azure-load-balancer
spec:
  loadBalancerIP: 40.121.183.52
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-load-balancer
```

Utwórz usługę i wdrożenie za pomocą `kubectl apply` polecenia.

```console
kubectl apply -f load-balancer-service.yaml
```

## <a name="apply-a-dns-label-to-the-service"></a>Stosowanie etykiety DNS do usługi

Jeśli w usłudze jest używany dynamiczny lub statyczny publiczny adres IP, możesz użyć adnotacji usługi, `service.beta.kubernetes.io/azure-dns-label-name` Aby ustawić publiczną etykietę DNS. Spowoduje to opublikowanie w pełni kwalifikowanej nazwy domeny dla usługi przy użyciu publicznych serwerów DNS i domen najwyższego poziomu platformy Azure. Wartość adnotacji musi być unikatowa w obrębie lokalizacji platformy Azure, więc zaleca się użycie wystarczająco kwalifikowanej etykiety.   

Na platformie Azure zostanie automatycznie dołączona domyślna podsieć, na przykład `<location>.cloudapp.azure.com` (gdzie lokalizacja jest wybranym regionem) do podania nazwy, aby utworzyć w pełni kwalifikowaną nazwę DNS. Na przykład:

```yaml
apiVersion: v1
kind: Service
metadata:
  annotations:
    service.beta.kubernetes.io/azure-dns-label-name: myserviceuniquelabel
  name: azure-load-balancer
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-load-balancer
```

> [!NOTE] 
> Aby opublikować usługę we własnej domenie, zobacz [Azure DNS][azure-dns-zone] i projekt [zewnętrzny DNS][external-dns] .

## <a name="troubleshoot"></a>Rozwiązywanie problemów

Jeśli statyczny adres IP zdefiniowany we właściwości *loadBalancerIP* manifestu usługi Kubernetes nie istnieje lub nie został utworzony w grupie zasobów węzła i nie skonfigurowano żadnych dodatkowych delegowania, tworzenie usługi równoważenia obciążenia zakończy się niepowodzeniem. Aby rozwiązać problem, Przejrzyj zdarzenia tworzenia usługi za pomocą polecenia [polecenia kubectl opisywanie][kubectl-describe] . Podaj nazwę usługi określoną w manifeście YAML, jak pokazano w następującym przykładzie:

```console
kubectl describe service azure-load-balancer
```

Zostanie wyświetlone informacje o zasobie usługi Kubernetes. *Zdarzenia* na końcu następujących przykładowych danych wyjściowych wskazują, że *nie znaleziono adresu IP dostarczonego przez użytkownika*. W tych scenariuszach upewnij się, że w grupie zasobów węzła został utworzony statyczny publiczny adres IP i że adres IP określony w manifeście usługi Kubernetes jest poprawny.

```
Name:                     azure-load-balancer
Namespace:                default
Labels:                   <none>
Annotations:              <none>
Selector:                 app=azure-load-balancer
Type:                     LoadBalancer
IP:                       10.0.18.125
IP:                       40.121.183.52
Port:                     <unset>  80/TCP
TargetPort:               80/TCP
NodePort:                 <unset>  32582/TCP
Endpoints:                <none>
Session Affinity:         None
External Traffic Policy:  Cluster
Events:
  Type     Reason                      Age               From                Message
  ----     ------                      ----              ----                -------
  Normal   CreatingLoadBalancer        7s (x2 over 22s)  service-controller  Creating load balancer
  Warning  CreatingLoadBalancerFailed  6s (x2 over 12s)  service-controller  Error creating load balancer (will retry): Failed to create load balancer for service default/azure-load-balancer: user supplied IP Address 40.121.183.52 was not found
```

## <a name="next-steps"></a>Następne kroki

W celu zapewnienia dodatkowej kontroli nad ruchem sieciowym w aplikacjach można [utworzyć kontroler][aks-ingress-basic]transferu danych przychodzących. Można również utworzyć kontroler transferu danych przychodzących [ze statycznym publicznym adresem IP][aks-static-ingress].

<!-- LINKS - External -->
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[azure-dns-zone]: https://azure.microsoft.com/services/dns/
[external-dns]: https://github.com/kubernetes-sigs/external-dns

<!-- LINKS - Internal -->
[aks-faq-resource-group]: faq.md#why-are-two-resource-groups-created-with-aks
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[az-network-public-ip-list]: /cli/azure/network/public-ip#az-network-public-ip-list
[az-aks-show]: /cli/azure/aks#az-aks-show
[aks-ingress-basic]: ingress-basic.md
[aks-static-ingress]: ingress-static-ip.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[ip-sku]: ../virtual-network/public-ip-addresses.md#sku
