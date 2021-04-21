---
title: Używanie statycznego adresu IP z równoważeniem obciążenia
titleSuffix: Azure Kubernetes Service
description: Dowiedz się, jak utworzyć i używać statycznego adresu IP za pomocą Azure Kubernetes Service równoważenia obciążenia usługi (AKS).
services: container-service
ms.topic: article
ms.date: 11/14/2020
ms.openlocfilehash: bb1e5691027a4bd86b57390e12259ac165ca9ed8
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769521"
---
# <a name="use-a-static-public-ip-address-and-dns-label-with-the-azure-kubernetes-service-aks-load-balancer"></a>Używanie statycznego publicznego adresu IP i etykiety DNS z Azure Kubernetes Service równoważenia obciążenia usługi (AKS)

Domyślnie publiczny adres IP przypisany do zasobu usługi równoważenia obciążenia utworzonego przez klaster usługi AKS jest ważny tylko przez okres istnienia tego zasobu. Usunięcie usługi Kubernetes spowoduje również usunięcie skojarzonego usługi równoważenia obciążenia i adresu IP. Jeśli chcesz przypisać określony adres IP lub zachować adres IP dla ponownie wdpoletowanych usług Kubernetes, możesz utworzyć statyczny publiczny adres IP i użyć go.

W tym artykule pokazano, jak utworzyć statyczny publiczny adres IP i przypisać go do usługi Kubernetes.

## <a name="before-you-begin"></a>Zanim rozpoczniesz

W tym artykule przyjęto założenie, że masz istniejący klaster usługi AKS. Jeśli potrzebujesz klastra usługi AKS, zobacz przewodnik Szybki start usługi AKS przy użyciu interfejsu wiersza polecenia platformy [Azure][aks-quickstart-cli] lub Azure Portal [.][aks-quickstart-portal]

Musisz również zainstalować i skonfigurować interfejs wiersza polecenia platformy Azure w wersji 2.0.59 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][install-azure-cli].

W tym artykule o mowa o *używaniu* adresu IP standardowej wersji SKU z *usługą* równoważenia obciążenia w standardowych sku. Aby uzyskać więcej informacji, zobacz [Typy adresów IP i metody alokacji na platformie Azure.][ip-sku]

## <a name="create-a-static-ip-address"></a>Tworzenie statycznego adresu IP

Utwórz statyczny publiczny adres IP za pomocą [polecenia az network public ip create.][az-network-public-ip-create] Poniżej przedstawiono sposób tworzenia zasobu statycznego adresu IP o *nazwie myAKSPublicIP* w grupie zasobów *myResourceGroup:*

```azurecli-interactive
az network public-ip create \
    --resource-group myResourceGroup \
    --name myAKSPublicIP \
    --sku Standard \
    --allocation-method static
```

> [!NOTE]
> Jeśli używasz podstawowego usługi *równoważenia* obciążenia SKU w klastrze usługi AKS, użyj wartości *Podstawowa* dla *parametru SKU* podczas definiowania publicznego adresu IP. Tylko *podstawowe ip* SKU działają z usługą równoważenia obciążenia podstawowej wersji SKU, a tylko standardowe ipy SKU działają z usługami równoważenia obciążenia w standardowych sku.    

Zostanie wyświetlony adres IP, jak pokazano w poniższych skróconych przykładowych danych wyjściowych:

```json
{
  "publicIp": {
    ...
    "ipAddress": "40.121.183.52",
    ...
  }
}
```

Publiczny adres IP można później uzyskać za pomocą [polecenia az network public-ip list.][az-network-public-ip-list] Określ nazwę utworzonej grupy zasobów węzła i publiczny adres IP oraz zapytanie o *adres ipAddress,* jak pokazano w poniższym przykładzie:

```azurecli-interactive
$ az network public-ip show --resource-group myResourceGroup --name myAKSPublicIP --query ipAddress --output tsv

40.121.183.52
```

## <a name="create-a-service-using-the-static-ip-address"></a>Tworzenie usługi przy użyciu statycznego adresu IP

Przed utworzeniem usługi upewnij się, że tożsamość klastra używana przez klaster usługi AKS ma delegowane uprawnienia do innej grupy zasobów. Na przykład:

```azurecli-interactive
az role assignment create \
    --assignee <Client ID> \
    --role "Network Contributor" \
    --scope /subscriptions/<subscription id>/resourceGroups/<resource group name>
```

> [!IMPORTANT]
> Jeśli adres IP ruchu wychodzącego został dostosowany, upewnij się, że tożsamość klastra ma uprawnienia zarówno do publicznego adresu IP ruchu wychodzącego, jak i do tego przychodzącego publicznego adresu IP.

Aby utworzyć *usługę LoadBalancer* ze statycznym publicznym adresem IP, dodaj właściwość i wartość statycznego publicznego adresu IP do `loadBalancerIP` manifestu YAML. Utwórz plik o nazwie `load-balancer-service.yaml` i skopiuj go w poniższym pliku YAML. Podaj własny publiczny adres IP utworzony w poprzednim kroku. W poniższym przykładzie adnotacja jest również ustawiana na grupę zasobów *o nazwie myResourceGroup*. Podaj własną nazwę grupy zasobów.

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

Utwórz usługę i wdrożenie za pomocą `kubectl apply` polecenia .

```console
kubectl apply -f load-balancer-service.yaml
```

## <a name="apply-a-dns-label-to-the-service"></a>Stosowanie etykiety DNS do usługi

Jeśli usługa używa dynamicznego lub statycznego publicznego adresu IP, możesz użyć adnotacji usługi, aby ustawić publiczną `service.beta.kubernetes.io/azure-dns-label-name` etykietę DNS. To narzędzie publikuje w pełni kwalifikowaną nazwę domeny dla usługi przy użyciu publicznych serwerów DNS platformy Azure i domeny najwyższego poziomu. Wartość adnotacji musi być unikatowa w obrębie lokalizacji platformy Azure, dlatego zaleca się użycie odpowiednio kwalifikowanej etykiety.   

Platforma Azure automatycznie dołączy domyślną podsieć, taką jak (gdzie lokalizacja to wybrany region), do podanej nazwy, aby utworzyć w pełni `<location>.cloudapp.azure.com` kwalifikowaną nazwę DNS. Na przykład:

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
> Aby opublikować usługę we własnej domenie, zobacz [Azure DNS][azure-dns-zone] i [projekt external-dns.][external-dns]

## <a name="troubleshoot"></a>Rozwiązywanie problemów

Jeśli statyczny adres IP zdefiniowany we właściwości *loadBalancerIP* manifestu usługi Kubernetes nie istnieje lub nie został utworzony w grupie zasobów węzła i nie skonfigurowano żadnych dodatkowych delegowania, tworzenie usługi równoważenia obciążenia kończy się niepowodzeniem. Aby rozwiązać problemy, przejrzyj zdarzenia tworzenia usługi za pomocą [polecenia kubectl describe.][kubectl-describe] Podaj nazwę usługi określoną w manifeście YAML, jak pokazano w poniższym przykładzie:

```console
kubectl describe service azure-load-balancer
```

Zostaną wyświetlone informacje o zasobie usługi Kubernetes. Zdarzenia *na* końcu następujących przykładowych danych wyjściowych wskazują, że nie znaleziono adresu *IP podanego przez użytkownika.* W tych scenariuszach sprawdź, czy w grupie zasobów węzła utworzono statyczny publiczny adres IP oraz czy adres IP określony w manifeście usługi Kubernetes jest poprawny.

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

Aby uzyskać dodatkową kontrolę nad ruchem sieciowym do aplikacji, można zamiast tego utworzyć [kontroler ruchu wychodzącego][aks-ingress-basic]. Można również utworzyć [kontroler ruchu wychodzącego ze statycznym publicznym adresem IP][aks-static-ingress].

<!-- LINKS - External -->
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[azure-dns-zone]: https://azure.microsoft.com/services/dns/
[external-dns]: https://github.com/kubernetes-sigs/external-dns

<!-- LINKS - Internal -->
[aks-faq-resource-group]: faq.md#why-are-two-resource-groups-created-with-aks
[az-network-public-ip-create]: /cli/azure/network/public-ip#az_network_public_ip_create
[az-network-public-ip-list]: /cli/azure/network/public-ip#az_network_public_ip_list
[az-aks-show]: /cli/azure/aks#az_aks_show
[aks-ingress-basic]: ingress-basic.md
[aks-static-ingress]: ingress-static-ip.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[ip-sku]: ../virtual-network/public-ip-addresses.md#sku
