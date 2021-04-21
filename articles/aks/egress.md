---
title: Używanie statycznego adresu IP dla ruchu wychodzącego
titleSuffix: Azure Kubernetes Service
description: Dowiedz się, jak utworzyć statyczny publiczny adres IP dla ruchu wychodzącego w klastrze usługi Azure Kubernetes Service (AKS) i używać go
services: container-service
ms.topic: article
ms.date: 03/16/2021
ms.openlocfilehash: 7cff3f5d66bb9872a0c949c6237150f8b69c9fa7
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773013"
---
# <a name="use-a-static-public-ip-address-for-egress-traffic-with-a-basic-sku-load-balancer-in-azure-kubernetes-service-aks"></a>Używanie statycznego publicznego adresu IP dla ruchu wychodzącego z podstawowym usługą *równoważenia* obciążenia SKU Azure Kubernetes Service (AKS)

Domyślnie adres IP wychodzący z klastra Azure Kubernetes Service (AKS) jest przypisywany losowo. Ta konfiguracja nie jest idealna, gdy konieczne jest na przykład zidentyfikowanie adresu IP w celu uzyskania dostępu do usług zewnętrznych. Zamiast tego może być konieczne przypisanie statycznego adresu IP, który ma zostać dodany do listy zezwalania na dostęp do usługi.

W tym artykule pokazano, jak utworzyć statyczny publiczny adres IP i używać go z ruchem wychodzącym w klastrze usługi AKS.

## <a name="before-you-begin"></a>Zanim rozpoczniesz

W tym artykule przyjęto założenie, że używasz usługi Azure Basic Load Balancer.  Zalecamy używanie usługi [Azure usługa Load Balancer w warstwie Standardowa](../load-balancer/load-balancer-overview.md)i bardziej zaawansowanych funkcji do kontrolowania ruchu [wychodzącego usługi AKS.](./limit-egress-traffic.md)

W tym artykule przyjęto założenie, że masz istniejący klaster usługi AKS. Jeśli potrzebujesz klastra usługi AKS, zobacz przewodnik Szybki start usługi AKS przy użyciu interfejsu wiersza polecenia platformy [Azure][aks-quickstart-cli] lub Azure Portal [.][aks-quickstart-portal]

Musisz również zainstalować i skonfigurować interfejs wiersza polecenia platformy Azure w wersji 2.0.59 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][install-azure-cli].

> [!IMPORTANT]
> W tym artykule jest używany *podstawowy sku* równoważenia obciążenia z jedną pulą węzłów. Ta konfiguracja nie jest dostępna dla wielu pul węzłów, ponieważ *podstawowy* równoważenie obciążenia SKU nie jest obsługiwane w przypadku wielu pul węzłów. Aby uzyskać więcej informacji na temat korzystania z usługi load balancer  w standardowych usługach SKU, zobacz Używanie publicznej usługi usługa Load Balancer w warstwie Standardowa [in Azure Kubernetes Service (AKS).][slb]

## <a name="egress-traffic-overview"></a>Omówienie ruchu wychodzącego

Ruch wychodzący z klastra usługi AKS jest [zgodny Azure Load Balancer konwencjami][outbound-connections]. Przed utworzeniem pierwszej usługi Kubernetes typu węzły agenta w klastrze usługi AKS nie są częścią `LoadBalancer` Azure Load Balancer puli. W tej konfiguracji węzły nie mają publicznego adresu IP na poziomie wystąpienia. Platforma Azure tłumaczy przepływ wychodzący na publiczny źródłowy adres IP, który nie jest konfigurowalny ani deterministyczny.

Po utworzeniu usługi Kubernetes typu węzły agenta są dodawane do `LoadBalancer` Azure Load Balancer puli. Load Balancer Basic wybiera jeden frontend do korzystania z przepływów wychodzących, gdy wiele (publicznych) frontorów IP jest kandydatami do przepływów wychodzących. Tego wyboru nie można skonfigurować i należy wziąć pod uwagę, że algorytm wyboru jest losowy. Ten publiczny adres IP jest ważny tylko przez okres istnienia tego zasobu. Jeśli usuniesz usługę Kubernetes LoadBalancer, skojarzony z nią równoważenie obciążenia i adres IP również zostaną usunięte. Jeśli chcesz przypisać określony adres IP lub zachować adres IP dla ponownie wdychanych usług Kubernetes, możesz utworzyć statyczny publiczny adres IP i używać go.

## <a name="create-a-static-public-ip"></a>Tworzenie statycznego publicznego adresu IP

Pobierz nazwę grupy zasobów za pomocą polecenia [az aks show][az-aks-show] i dodaj `--query nodeResourceGroup` parametr zapytania. Poniższy przykład pobiera grupę zasobów węzła dla nazwy klastra usługi AKS *myAKSCluster* w nazwie grupy zasobów *myResourceGroup:*

```azurecli-interactive
$ az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_eastus
```

Teraz utwórz statyczny publiczny adres IP za pomocą [polecenia az network public ip create.][az-network-public-ip-create] Określ nazwę grupy zasobów węzła uzyskaną w poprzednim poleceniu, a następnie nazwę zasobu adresu IP, taką jak *myAKSPublicIP:*

```azurecli-interactive
az network public-ip create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --name myAKSPublicIP \
    --allocation-method static
```

Zostanie pokazany adres IP, jak pokazano w poniższych skróconych przykładowych danych wyjściowych:

```json
{
  "publicIp": {
    "dnsSettings": null,
    "etag": "W/\"6b6fb15c-5281-4f64-b332-8f68f46e1358\"",
    "id": "/subscriptions/<SubscriptionID>/resourceGroups/MC_myResourceGroup_myAKSCluster_eastus/providers/Microsoft.Network/publicIPAddresses/myAKSPublicIP",
    "idleTimeoutInMinutes": 4,
    "ipAddress": "40.121.183.52",
    [..]
  }
```

Publiczny adres IP można później uzyskać za pomocą [polecenia az network public-ip list.][az-network-public-ip-list] Określ nazwę grupy zasobów węzła, a następnie zapytanie o *adres ipAddress,* jak pokazano w poniższym przykładzie:

```azurecli-interactive
$ az network public-ip list --resource-group MC_myResourceGroup_myAKSCluster_eastus --query [0].ipAddress --output tsv

40.121.183.52
```

## <a name="create-a-service-with-the-static-ip"></a>Tworzenie usługi przy użyciu statycznego adresu IP

Aby utworzyć usługę ze statycznym publicznym adresem IP, dodaj właściwość i wartość statycznego publicznego adresu `loadBalancerIP` IP do manifestu YAML. Utwórz plik o nazwie `egress-service.yaml` i skopiuj go w poniższym pliku YAML. Podaj własny publiczny adres IP utworzony w poprzednim kroku.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-egress
spec:
  loadBalancerIP: 40.121.183.52
  type: LoadBalancer
  ports:
  - port: 80
```

Utwórz usługę i wdrożenie za pomocą `kubectl apply` polecenia .

```console
kubectl apply -f egress-service.yaml
```

Ta usługa konfiguruje nowy adres IP frontendu na Azure Load Balancer. Jeśli nie masz skonfigurowanych żadnych innych adresów IP, cały **ruch** wychodzący powinien teraz używać tego adresu. Jeśli skonfigurowano wiele adresów na Azure Load Balancer, dowolny z tych publicznych adresów IP jest kandydatem do przepływów wychodzących, a jeden jest wybierany losowo.

## <a name="verify-egress-address"></a>Weryfikowanie adresu wychodzącego

Aby sprawdzić, czy statyczny publiczny adres IP jest używany, można użyć usługi wyszukiwania DNS, takiej jak `checkip.dyndns.org` .

Uruchom podstawowy zasobnik *Debian* i dołącz go:

```console
kubectl run -it --rm aks-ip --image=mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11
```

Aby uzyskać dostęp do witryny internetowej z poziomu kontenera, użyj programu `apt-get` , aby zainstalować w `curl` kontenerze.

```console
apt-get update && apt-get install curl -y
```

Teraz użyj programu curl, aby uzyskać *dostęp do checkip.dyndns.org* witryny. Zostanie wyświetlony adres IP dla danych wychodzących, jak pokazano w poniższych przykładowych danych wyjściowych. Ten adres IP jest taki, jak statyczny publiczny adres IP utworzony i zdefiniowany dla usługi loadBalancer:

```console
$ curl -s checkip.dyndns.org

<html><head><title>Current IP Check</title></head><body>Current IP Address: 40.121.183.52</body></html>
```

## <a name="next-steps"></a>Następne kroki

Aby uniknąć obsługi wielu publicznych adresów IP na Azure Load Balancer, można zamiast tego użyć kontrolera ruchu wychodzącego. Kontrolery ruchu wychodzącego zapewniają dodatkowe korzyści, takie jak zakończenie protokołu SSL/TLS, obsługa ponownego nadawania adresów URI i nadrzędne szyfrowanie SSL/TLS. Aby uzyskać więcej informacji, zobacz Create a basic ingress controller in AKS (Tworzenie podstawowego kontrolera [ruchu wychodzącego w UKS).][ingress-aks-cluster]

<!-- LINKS - internal -->
[az-network-public-ip-create]: /cli/azure/network/public-ip#az_network_public_ip_create
[az-network-public-ip-list]: /cli/azure/network/public-ip#az_network_public_ip_list
[az-aks-show]: /cli/azure/aks#az_aks_show
[azure-cli-install]: /cli/azure/install-azure-cli
[ingress-aks-cluster]: ./ingress-basic.md
[outbound-connections]: ../load-balancer/load-balancer-outbound-connections.md#scenarios
[public-ip-create]: /cli/azure/network/public-ip#az_network_public_ip_create
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[slb]: load-balancer-standard.md
