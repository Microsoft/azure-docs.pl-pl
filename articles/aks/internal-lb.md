---
title: Utwórz wewnętrzny moduł równoważenia obciążenia.
titleSuffix: Azure Kubernetes Service
description: Dowiedz się, jak utworzyć i używać wewnętrznego modułu równoważenia obciążenia, aby udostępnić swoje usługi za pomocą usługi Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 03/04/2019
ms.openlocfilehash: 9c2966215d07c4ddf052d30a5757a2deee2e0b5c
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2020
ms.locfileid: "81392788"
---
# <a name="use-an-internal-load-balancer-with-azure-kubernetes-service-aks"></a>Użyj wewnętrznego modułu równoważenia obciążenia z usługą Azure Kubernetes Service (AKS)

Aby ograniczyć dostęp do aplikacji w usłudze Azure Kubernetes Service (AKS), można utworzyć i używać wewnętrznego modułu równoważenia obciążenia. Wewnętrzny moduł równoważenia obciążenia sprawia, że usługa Kubernetes jest dostępna tylko dla aplikacji działających w tej samej sieci wirtualnej co klaster Kubernetes. W tym artykule pokazano, jak utworzyć i używać wewnętrznego modułu równoważenia obciążenia za pomocą usługi Azure Kubernetes Service (AKS).

> [!NOTE]
> Moduł równoważenia obciążenia platformy Azure jest dostępny w dwóch jednostkach SKU — *Basic* i *Standard*. Domyślnie standardowa jednostka SKU jest używana podczas tworzenia klastra AKS.  Podczas tworzenia usługi z typem jako LoadBalancer, otrzymasz ten sam typ LB, jak podczas aprowizowania klastra. Aby uzyskać więcej informacji, zobacz [porównanie jednostki SKU modułu równoważenia obciążenia platformy Azure][azure-lb-comparison].

## <a name="before-you-begin"></a>Przed rozpoczęciem

W tym artykule przyjęto założenie, że masz istniejący klaster AKS. Jeśli potrzebujesz klastra AKS, zobacz szybki start usługi AKS [przy użyciu interfejsu wiersza polecenia platformy Azure][aks-quickstart-cli] lub za pomocą portalu [Azure.][aks-quickstart-portal]

Potrzebne są również zainstalowane i skonfigurowane i skonfigurowane narzędzia Azure CLI w wersji 2.0.59 lub nowszej. Uruchom polecenie  `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie przeprowadzenie instalacji lub uaktualnienia, zobacz  [Instalowanie interfejsu wiersza polecenia platformy Azure][install-azure-cli].

Podmiot zabezpieczeń usługi klastrowania usługi AKS wymaga uprawnień do zarządzania zasobami sieciowymi, jeśli używasz istniejącej podsieci lub grupy zasobów. Ogólnie rzecz biorąc przypisz rolę *współautora sieci* do jednostki usługi na delegowanych zasobów. Zamiast jednostki usługi można użyć systemu przypisanego tożsamości zarządzanej dla uprawnień. Aby uzyskać więcej informacji, zobacz [Używanie tożsamości zarządzanych](use-managed-identity.md). Aby uzyskać więcej informacji na temat uprawnień, zobacz [Delegowanie dostępu AKS do innych zasobów platformy Azure][aks-sp].

## <a name="create-an-internal-load-balancer"></a>Utwórz wewnętrzny moduł równoważenia obciążenia.

Aby utworzyć wewnętrzny moduł równoważenia `internal-lb.yaml` obciążenia, utwórz manifest usługi o nazwie o typie usługi *LoadBalancer* i adnotację *azure-load-balance-internal,* jak pokazano w poniższym przykładzie:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: internal-app
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: internal-app
```

Wdrażanie wewnętrznego modułu równoważenia obciążenia przy użyciu [aplikacji kubectl][kubectl-apply] i określ nazwę manifestu YAML:

```console
kubectl apply -f internal-lb.yaml
```

Moduł równoważenia obciążenia platformy Azure jest tworzony w grupie zasobów węzła i połączony z tą samą siecią wirtualną co klaster AKS.

Podczas wyświetlania szczegółów usługi adres IP wewnętrznego modułu równoważenia obciążenia jest wyświetlany w kolumnie *EXTERNAL-IP.* W tym kontekście *External* jest w odniesieniu do interfejsu zewnętrznego modułu równoważenia obciążenia, a nie, że odbiera publiczny, zewnętrzny adres IP. Zmiana adresu IP z * \<oczekującego\> * na rzeczywisty wewnętrzny adres IP może potrwać minutę lub dwie, jak pokazano w poniższym przykładzie:

```
$ kubectl get service internal-app

NAME           TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
internal-app   LoadBalancer   10.0.248.59   10.240.0.7    80:30555/TCP   2m
```

## <a name="specify-an-ip-address"></a>Określanie adresu IP

Jeśli chcesz użyć określonego adresu IP z wewnętrznym modułem równoważenia obciążenia, dodaj właściwość *loadBalancerIP* do manifestu modułu równoważenia obciążenia YAML. Określony adres IP musi znajdować się w tej samej podsieci co klaster AKS i nie może być już przypisany do zasobu.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: internal-app
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
spec:
  type: LoadBalancer
  loadBalancerIP: 10.240.0.25
  ports:
  - port: 80
  selector:
    app: internal-app
```

Po wdrożeniu i wyświetleniu szczegółów usługi adres IP w kolumnie *EXTERNAL-IP* odzwierciedla określony adres IP:

```
$ kubectl get service internal-app

NAME           TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE
internal-app   LoadBalancer   10.0.184.168   10.240.0.25   80:30225/TCP   4m
```

## <a name="use-private-networks"></a>Korzystanie z sieci prywatnych

Podczas tworzenia klastra AKS można określić zaawansowane ustawienia sieciowe. Takie podejście umożliwia wdrożenie klastra w istniejącej sieci wirtualnej platformy Azure i podsieci. Jednym ze scenariuszy jest wdrożenie klastra AKS w sieci prywatnej podłączonej do środowiska lokalnego i uruchomienie usług dostępnych tylko wewnętrznie. Aby uzyskać więcej informacji, zobacz konfigurowanie własnych podsieci sieci wirtualnej za pomocą [aplikacji Kubenet][use-kubenet] lub [Azure CNI][advanced-networking].

Nie są potrzebne żadne zmiany w poprzednich krokach, aby wdrożyć wewnętrzny moduł równoważenia obciążenia w klastrze AKS korzystającym z sieci prywatnej. Moduł równoważenia obciążenia jest tworzony w tej samej grupie zasobów co klaster AKS, ale połączony z prywatną siecią wirtualną i podsiecią, jak pokazano w poniższym przykładzie:

```
$ kubectl get service internal-app

NAME           TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
internal-app   LoadBalancer   10.1.15.188   10.0.0.35     80:31669/TCP   1m
```

> [!NOTE]
> Może być konieczne przyznanie jednostki usługi dla klastra AKS roli *współautora sieci* do grupy zasobów, w której są wdrażane zasoby sieci wirtualnej platformy Azure. Zobacz jednostkę usługi z [az aks show][az-aks-show], takich jak `az aks show --resource-group myResourceGroup --name myAKSCluster --query "servicePrincipalProfile.clientId"`. Aby utworzyć przypisanie roli, użyj polecenia [utwórz przypisanie roli az.][az-role-assignment-create]

## <a name="specify-a-different-subnet"></a>Określanie innej podsieci

Aby określić podsieć dla modułu równoważenia obciążenia, dodaj adnotację *azure-load-balancer-internal-subnet* do usługi. Określona podsieć musi znajdować się w tej samej sieci wirtualnej co klaster AKS. Po wdrożeniu adres *EXTERNAL-IP* modułu równoważenia obciążenia jest częścią określonej podsieci.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: internal-app
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
    service.beta.kubernetes.io/azure-load-balancer-internal-subnet: "apps-subnet"
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: internal-app
```

## <a name="delete-the-load-balancer"></a>Usuwanie modułu równoważenia obciążenia

Po usunięciu wszystkich usług korzystających z wewnętrznego modułu równoważenia obciążenia, sam moduł równoważenia obciążenia jest również usuwany.

Można również bezpośrednio usunąć usługę, jak w przypadku dowolnego `kubectl delete service internal-app`zasobu Kubernetes, takich jak , który również następnie usuwa podstawowy moduł równoważenia obciążenia platformy Azure.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o usługach Kubernetes w [dokumentacji usług Kubernetes][kubernetes-services].

<!-- LINKS - External -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubernetes-services]: https://kubernetes.io/docs/concepts/services-networking/service/
[aks-engine]: https://github.com/Azure/aks-engine

<!-- LINKS - Internal -->
[advanced-networking]: configure-azure-cni.md
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[azure-lb-comparison]: ../load-balancer/concepts-limitations.md#skus
[use-kubenet]: configure-kubenet.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[aks-sp]: kubernetes-service-principal.md#delegate-access-to-other-azure-resources
