---
title: Utwórz wewnętrzny moduł równoważenia obciążenia.
titleSuffix: Azure Kubernetes Service
description: Dowiedz się, jak utworzyć i użyć wewnętrznego modułu równoważenia obciążenia, aby udostępnić swoje usługi za pomocą usługi Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 03/04/2019
ms.openlocfilehash: 4c2c0866aa9a721a73e1eb8fa230f0022cf6b8ca
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102505634"
---
# <a name="use-an-internal-load-balancer-with-azure-kubernetes-service-aks"></a>Korzystanie z wewnętrznego modułu równoważenia obciążenia z usługą Azure Kubernetes Service (AKS)

Aby ograniczyć dostęp do aplikacji w usłudze Azure Kubernetes Service (AKS), można utworzyć i użyć wewnętrznego modułu równoważenia obciążenia. Wewnętrzny moduł równoważenia obciążenia sprawia, że usługa Kubernetes jest dostępna tylko dla aplikacji działających w tej samej sieci wirtualnej co klaster Kubernetes. W tym artykule opisano sposób tworzenia i używania wewnętrznego modułu równoważenia obciążenia z usługą Azure Kubernetes Service (AKS).

> [!NOTE]
> Azure Load Balancer jest dostępny w dwóch jednostkach SKU — *podstawowa* i *standardowa*. Domyślnie standardowa jednostka SKU jest używana podczas tworzenia klastra AKS.  Podczas tworzenia usługi przy użyciu typu jako modułu równoważenia obciążenia otrzymasz ten sam typ LB jak podczas aprowizacji klastra. Aby uzyskać więcej informacji, zobacz [porównanie jednostki SKU modułu równoważenia obciążenia platformy Azure][azure-lb-comparison].

## <a name="before-you-begin"></a>Zanim rozpoczniesz

W tym artykule przyjęto założenie, że masz istniejący klaster AKS. Jeśli potrzebujesz klastra AKS, zapoznaj się z przewodnikiem Szybki Start AKS [przy użyciu interfejsu wiersza polecenia platformy Azure][aks-quickstart-cli] lub [przy użyciu Azure Portal][aks-quickstart-portal].

Konieczne jest również zainstalowanie i skonfigurowanie interfejsu wiersza polecenia platformy Azure w wersji 2.0.59 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][install-azure-cli].

Tożsamość klastra klastrów AKS wymaga uprawnień do zarządzania zasobami sieci, jeśli używana jest istniejąca podsieć lub Grupa zasobów. Aby uzyskać więcej informacji, zobacz [Korzystanie z sieci korzystającą wtyczki kubenet z własnymi zakresami adresów IP w usłudze Azure Kubernetes Service (AKS)][use-kubenet] lub [Konfigurowanie sieci CNI platformy Azure w usłudze Azure KUBERNETES Service (AKS)][advanced-networking]. W przypadku konfigurowania modułu równoważenia obciążenia do używania [adresu IP w innej podsieci][different-subnet]upewnij się, że tożsamość klastra AKS ma również dostęp do odczytu do tej podsieci.

Aby uzyskać więcej informacji o uprawnieniach, zobacz [delegowanie dostępu AKS do innych zasobów platformy Azure][aks-sp].

## <a name="create-an-internal-load-balancer"></a>Utwórz wewnętrzny moduł równoważenia obciążenia.

Aby utworzyć wewnętrzny moduł równoważenia obciążenia, należy utworzyć manifest usługi o nazwie `internal-lb.yaml` przy użyciu *modułu równoważenia* obciążenia i *Azure-load-module —* adnotacja wewnętrzna, jak pokazano w następującym przykładzie:

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

Wdróż wewnętrzny moduł równoważenia obciążenia przy użyciu [polecenia kubectl Zastosuj][kubectl-apply] i określ nazwę manifestu YAML:

```console
kubectl apply -f internal-lb.yaml
```

Moduł równoważenia obciążenia platformy Azure jest tworzony w grupie zasobów węzła i połączony z tą samą siecią wirtualną co klaster AKS.

Po wyświetleniu szczegółów usługi adres IP wewnętrznego modułu równoważenia obciążenia jest wyświetlany w kolumnie *zewnętrzny adres IP* . W tym kontekście *zewnętrzny* jest w odniesieniu do zewnętrznego interfejsu modułu równoważenia obciążenia, a nie otrzymuje publicznego zewnętrznego adresu IP. Zmiana adresu IP na faktyczny wewnętrzny adres IP może potrwać minutę lub dwa *\<pending\>* , jak pokazano w następującym przykładzie:

```
$ kubectl get service internal-app

NAME           TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
internal-app   LoadBalancer   10.0.248.59   10.240.0.7    80:30555/TCP   2m
```

## <a name="specify-an-ip-address"></a>Określ adres IP

Jeśli chcesz użyć określonego adresu IP z wewnętrznym modułem równoważenia obciążenia, Dodaj właściwość *loadBalancerIP* do manifestu YAML modułu równoważenia obciążenia. W tym scenariuszu określony adres IP musi znajdować się w tej samej podsieci co klaster AKS i nie może być już przypisany do zasobu. Na przykład nie należy używać adresu IP z zakresu wyznaczono dla podsieci Kubernetes.

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

Po wdrożeniu i wyświetleniu szczegółów usługi adres IP w kolumnie *External-IP* odzwierciedla określony adres IP:

```
$ kubectl get service internal-app

NAME           TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE
internal-app   LoadBalancer   10.0.184.168   10.240.0.25   80:30225/TCP   4m
```

Aby uzyskać więcej informacji na temat konfigurowania modułu równoważenia obciążenia w innej podsieci, zobacz [Określanie innej podsieci][different-subnet]

## <a name="use-private-networks"></a>Korzystanie z sieci prywatnych

Podczas tworzenia klastra AKS można określić zaawansowane ustawienia sieci. Takie podejście umożliwia wdrożenie klastra w istniejącej sieci wirtualnej platformy Azure i podsieci. Jednym z scenariuszy jest wdrożenie klastra AKS w sieci prywatnej połączonej ze środowiskiem lokalnym i uruchamianie usług dostępnych wewnętrznie. Aby uzyskać więcej informacji, zobacz Konfigurowanie własnych podsieci sieci wirtualnej za pomocą usługi [korzystającą wtyczki kubenet][use-kubenet] lub [Azure CNI][advanced-networking].

Nie są wymagane żadne zmiany w poprzednich krokach w celu wdrożenia wewnętrznego modułu równoważenia obciążenia w klastrze AKS, który korzysta z sieci prywatnej. Moduł równoważenia obciążenia jest tworzony w tej samej grupie zasobów co klaster AKS, ale połączony z prywatną siecią wirtualną i podsiecią, jak pokazano w następującym przykładzie:

```
$ kubectl get service internal-app

NAME           TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
internal-app   LoadBalancer   10.1.15.188   10.0.0.35     80:31669/TCP   1m
```

> [!NOTE]
> Może być konieczne przyznanie tożsamości klastra dla klastra AKS do grupy zasobów *, w* której są wdrożone zasoby sieci wirtualnej platformy Azure. Wyświetl tożsamość klastra za pomocą [AZ AKS show][az-aks-show], na przykład `az aks show --resource-group myResourceGroup --name myAKSCluster --query "identity"` . Aby utworzyć przypisanie roli, użyj polecenia [AZ role przypisanie Create][az-role-assignment-create] .

## <a name="specify-a-different-subnet"></a>Określ inną podsieć

Aby określić podsieć dla modułu równoważenia obciążenia, Dodaj do usługi adnotację *Azure-load-module-Internal-Subnet* . Określona podsieć musi znajdować się w tej samej sieci wirtualnej co klaster AKS. Po wdrożeniu *zewnętrzny adres IP* modułu równoważenia obciążenia jest częścią określonej podsieci.

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

## <a name="delete-the-load-balancer"></a>Usuwanie usługi równoważenia obciążenia

Po usunięciu wszystkich usług, które korzystają z wewnętrznego modułu równoważenia obciążenia, sam moduł równoważenia obciążenia również zostaje usunięty.

Można również bezpośrednio usunąć usługę tak, jak w przypadku dowolnego zasobu Kubernetes, na przykład `kubectl delete service internal-app` , która spowoduje również usunięcie bazowego modułu równoważenia obciążenia platformy Azure.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o usługach Kubernetes Services w [dokumentacji usług Kubernetes Services][kubernetes-services].

<!-- LINKS - External -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubernetes-services]: https://kubernetes.io/docs/concepts/services-networking/service/
[aks-engine]: https://github.com/Azure/aks-engine

<!-- LINKS - Internal -->
[advanced-networking]: configure-azure-cni.md
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[azure-lb-comparison]: ../load-balancer/skus.md
[use-kubenet]: configure-kubenet.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[aks-sp]: kubernetes-service-principal.md#delegate-access-to-other-azure-resources
[different-subnet]: #specify-a-different-subnet