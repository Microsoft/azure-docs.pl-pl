---
title: Utwórz wewnętrzny moduł równoważenia obciążenia.
titleSuffix: Azure Kubernetes Service
description: Dowiedz się, jak utworzyć wewnętrzny usługę równoważenia obciążenia i używać jej w celu uwidocznić usługi Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 03/04/2019
ms.openlocfilehash: cbb898d05ecc1f0796f3609adb1368c3d77de2c5
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107779745"
---
# <a name="use-an-internal-load-balancer-with-azure-kubernetes-service-aks"></a>Używanie wewnętrznego równoważenia obciążenia z Azure Kubernetes Service (AKS)

Aby ograniczyć dostęp do aplikacji w Azure Kubernetes Service (AKS), można utworzyć wewnętrzny usługę równoważenia obciążenia i korzystać z niego. Wewnętrzny równoważenie obciążenia sprawia, że usługa Kubernetes jest dostępna tylko dla aplikacji uruchomionych w tej samej sieci wirtualnej co klaster Kubernetes. W tym artykule przedstawiono sposób tworzenia i używania wewnętrznego równoważenia obciążenia za pomocą Azure Kubernetes Service (AKS).

> [!NOTE]
> Azure Load Balancer jest dostępna w dwóch jednostkach SKU — *Podstawowa* i *Standardowa.* Domyślnie podczas tworzenia klastra usługi AKS jest używana standardowa wartość SKU.  Podczas tworzenia usługi o typie LoadBalancer otrzymasz ten sam typ równoważenia obciążenia co podczas aprowizowania klastra. Aby uzyskać więcej informacji, zobacz [Porównanie wersji SKU usługi Azure Load Balancer.][azure-lb-comparison]

## <a name="before-you-begin"></a>Zanim rozpoczniesz

W tym artykule przyjęto założenie, że masz istniejący klaster usługi AKS. Jeśli potrzebujesz klastra usługi AKS, zobacz przewodnik Szybki start usługi AKS przy użyciu interfejsu wiersza polecenia platformy [Azure][aks-quickstart-cli] lub Azure Portal [.][aks-quickstart-portal]

Musisz również zainstalować i skonfigurować interfejs wiersza polecenia platformy Azure w wersji 2.0.59 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][install-azure-cli].

Tożsamość klastra usługi AKS wymaga uprawnień do zarządzania zasobami sieci, jeśli używasz istniejącej podsieci lub grupy zasobów. Aby uzyskać informacje, zobacz Use [kubenet networking with your own IP address ranges in Azure Kubernetes Service (AKS) (Korzystanie][use-kubenet] z sieci kubenet z własnymi zakresami adresów IP w u usługach Azure Kubernetes Service)) lub [Configure Azure CNI networking in Azure Kubernetes Service (AKS) (Konfigurowanie][advanced-networking]sieci Azure CNI sieci w Azure Kubernetes Service (AKS) ). Jeśli konfigurujesz usługę równoważenia obciążenia do używania adresu [IP][different-subnet]w innej podsieci, upewnij się, że tożsamość klastra usługi AKS ma również dostęp do odczytu do tej podsieci.

Aby uzyskać więcej informacji na temat uprawnień, zobacz [Delegowanie dostępu usługi AKS do innych zasobów platformy Azure.][aks-sp]

## <a name="create-an-internal-load-balancer"></a>Utwórz wewnętrzny moduł równoważenia obciążenia.

Aby utworzyć wewnętrzny usługę równoważenia obciążenia, utwórz manifest usługi o nazwie z typem usługi `internal-lb.yaml` *LoadBalancer* i adnotacją *wewnętrzną azure-load-balancer-internal,* jak pokazano w poniższym przykładzie:

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

Wdróż wewnętrzny usługę równoważenia obciążenia przy użyciu narzędzia [kubectl apply][kubectl-apply] i określ nazwę manifestu YAML:

```console
kubectl apply -f internal-lb.yaml
```

Usługa Azure Load Balancer jest tworzona w grupie zasobów węzła i podłączona do tej samej sieci wirtualnej co klaster usługi AKS.

Podczas wyświetlania szczegółów usługi adres IP wewnętrznego równoważenia obciążenia jest wyświetlany w *kolumnie EXTERNAL-IP.* W tym kontekście *zewnętrzne* jest w odniesieniu do interfejsu zewnętrznego usługi równoważenia obciążenia, a nie odbiera publiczny, zewnętrzny adres IP. Zmiana adresu IP z na rzeczywisty wewnętrzny adres IP może potrwać minutę lub dwie, jak pokazano *\<pending\>* w poniższym przykładzie:

```
$ kubectl get service internal-app

NAME           TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
internal-app   LoadBalancer   10.0.248.59   10.240.0.7    80:30555/TCP   2m
```

## <a name="specify-an-ip-address"></a>Określanie adresu IP

Jeśli chcesz użyć określonego adresu IP z wewnętrznym równoważeniem obciążenia, dodaj właściwość *loadBalancerIP* do manifestu YAML usługi równoważenia obciążenia. W tym scenariuszu określony adres IP musi znajdować się w tej samej podsieci co klaster usługi AKS i nie może być jeszcze przypisany do zasobu. Na przykład nie należy używać adresu IP w zakresie wyznaczonym dla podsieci Kubernetes.

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

Podczas wdrażania i wyświetlania szczegółów usługi adres IP w kolumnie *EXTERNAL-IP* odzwierciedla określony adres IP:

```
$ kubectl get service internal-app

NAME           TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE
internal-app   LoadBalancer   10.0.184.168   10.240.0.25   80:30225/TCP   4m
```

Aby uzyskać więcej informacji na temat konfigurowania usługi równoważenia obciążenia w innej podsieci, zobacz [Określanie innej podsieci][different-subnet]

## <a name="use-private-networks"></a>Korzystanie z sieci prywatnych

Podczas tworzenia klastra usługi AKS można określić zaawansowane ustawienia sieci. Takie podejście umożliwia wdrożenie klastra w istniejącej sieci wirtualnej i podsieciach platformy Azure. Jednym ze scenariuszy jest wdrożenie klastra usługi AKS w sieci prywatnej połączonej ze środowiskiem lokalnym i uruchamianie usług dostępnych tylko wewnętrznie. Aby uzyskać więcej informacji, zobacz konfigurowanie własnych podsieci sieci wirtualnej przy użyciu platformy [Kubenet][use-kubenet] [lub Azure CNI][advanced-networking].

Żadne zmiany w poprzednich krokach nie są wymagane do wdrożenia wewnętrznego równoważenia obciążenia w klastrze usługi AKS, który używa sieci prywatnej. Usługa równoważenia obciążenia jest tworzona w tej samej grupie zasobów co klaster usługi AKS, ale jest połączona z prywatną siecią wirtualną i podsiecią, jak pokazano w poniższym przykładzie:

```
$ kubectl get service internal-app

NAME           TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
internal-app   LoadBalancer   10.1.15.188   10.0.0.35     80:31669/TCP   1m
```

> [!NOTE]
> Może być konieczne przyznanie tożsamości klastra usługi  AKS roli Współautor sieci do grupy zasobów, w której są wdrażane zasoby sieci wirtualnej platformy Azure. Wyświetl tożsamość klastra za pomocą [az aks show][az-aks-show], na przykład `az aks show --resource-group myResourceGroup --name myAKSCluster --query "identity"` . Aby utworzyć przypisanie roli, użyj [polecenia az role assignment create.][az-role-assignment-create]

## <a name="specify-a-different-subnet"></a>Określanie innej podsieci

Aby określić podsieć dla usługi równoważenia obciążenia, dodaj do usługi adnotację *azure-load-balancer-internal-subnet.* Określona podsieć musi znajdować się w tej samej sieci wirtualnej co klaster usługi AKS. Po wdrożeniu adres *EXTERNAL-IP* usługi równoważenia obciążenia jest częścią określonej podsieci.

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

## <a name="delete-the-load-balancer"></a>Usuwanie równoważenia obciążenia

Usunięcie wszystkich usług, które korzystają z wewnętrznego równoważenia obciążenia, również zostanie usunięte.

Możesz również bezpośrednio usunąć usługę tak jak w przypadku dowolnego zasobu platformy Kubernetes, takiego jak , co spowoduje również usunięcie podstawowego `kubectl delete service internal-app` usługi Azure Load Balancer.

## <a name="next-steps"></a>Następne kroki

Więcej informacji na temat usług Kubernetes można znaleźć w [dokumentacji usług Kubernetes.][kubernetes-services]

<!-- LINKS - External -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubernetes-services]: https://kubernetes.io/docs/concepts/services-networking/service/
[aks-engine]: https://github.com/Azure/aks-engine

<!-- LINKS - Internal -->
[advanced-networking]: configure-azure-cni.md
[az-aks-show]: /cli/azure/aks#az_aks_show
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
[azure-lb-comparison]: ../load-balancer/skus.md
[use-kubenet]: configure-kubenet.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[aks-sp]: kubernetes-service-principal.md#delegate-access-to-other-azure-resources
[different-subnet]: #specify-a-different-subnet