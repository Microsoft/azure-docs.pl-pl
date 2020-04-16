---
title: Zabezpiecz ruch zasobników za pomocą zasad sieciowych
titleSuffix: Azure Kubernetes Service
description: Dowiedz się, jak zabezpieczyć ruch, który przepływa do i z zasobników przy użyciu zasad sieciowych Kubernetes w usłudze Azure Kubernetes (AKS)
services: container-service
ms.topic: article
ms.date: 05/06/2019
ms.openlocfilehash: a2794f53407be3ce3d7e69caa8039c13217a0356
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2020
ms.locfileid: "81392608"
---
# <a name="secure-traffic-between-pods-using-network-policies-in-azure-kubernetes-service-aks"></a>Bezpieczny ruch między zasobnikami przy użyciu zasad sieciowych w usłudze Azure Kubernetes (AKS)

Po uruchomieniu nowoczesnych aplikacji opartych na mikrousługach w umięśniach często chcesz kontrolować, które składniki mogą komunikować się ze sobą. Zasada najmniejszych uprawnień powinny być stosowane do jak ruch może przepływać między zasobnikami w klastrze usługi Azure Kubernetes (AKS). Załóżmy, że prawdopodobnie chcesz zablokować ruch bezpośrednio do aplikacji zaplecza. Funkcja *zasad sieciowych* w uliczce Kubernetes umożliwia definiowanie reguł ruchu przychodzącego i wychodzącego między zasobnikami w klastrze.

W tym artykule pokazano, jak zainstalować aparat zasad sieciowych i utworzyć zasady sieciowe kubernetes do kontrolowania przepływu ruchu między zasobnikami w ucho. Zasady sieciowe powinny być używane tylko dla węzłów i zasobników opartych na systemie Linux w udręki AKS.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Potrzebujesz zainstalowanego i skonfigurowany interfejsu wiersza polecenia platformy Azure w wersji 2.0.61 lub nowszej. Uruchom polecenie  `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie przeprowadzenie instalacji lub uaktualnienia, zobacz  [Instalowanie interfejsu wiersza polecenia platformy Azure][install-azure-cli].

> [!TIP]
> Jeśli funkcja zasad sieciowych była używana podczas podglądu, zaleca się [utworzenie nowego klastra](#create-an-aks-cluster-and-enable-network-policy).
> 
> Jeśli chcesz nadal używać istniejących klastrów testowych, które używały zasad sieciowych podczas wersji zapoznawczej, uaktualnij klaster do nowych wersji kubernetes dla najnowszej wersji ga, a następnie wdrożyć następujący manifest YAML, aby naprawić serwer metryk ulegających awarii i pulpit nawigacyjny kubernetes. Ta poprawka jest wymagana tylko dla klastrów, które korzystały z aparatu zasad sieciowych Calico.
>
> Ze względów bezpieczeństwa [przejrzyj zawartość tego manifestu YAML,][calico-aks-cleanup] aby zrozumieć, co jest wdrażane w klastrze AKS.
>
> `kubectl delete -f https://raw.githubusercontent.com/Azure/aks-engine/master/docs/topics/calico-3.3.1-cleanup-after-upgrade.yaml`

## <a name="overview-of-network-policy"></a>Omówienie zasad sieciowych

Wszystkie zasobniki w klastrze AKS mogą domyślnie wysyłać i odbierać ruch bez ograniczeń. Aby zwiększyć bezpieczeństwo, można zdefiniować reguły, które kontrolują przepływ ruchu. Aplikacje zaplecza są często tylko narażone na wymagane usługi frontonu, na przykład. Lub składniki bazy danych są dostępne tylko dla warstw aplikacji, które łączą się z nimi.

Zasady sieciowe to specyfikacja kubernetes, która definiuje zasady dostępu do komunikacji między zasobnikami. Za pomocą zasad sieciowych można zdefiniować uporządkowany zestaw reguł do wysyłania i odbierania ruchu i zastosować je do kolekcji zasobników, które pasują do jednego lub więcej selektorów etykiet.

Te reguły zasad sieciowych są zdefiniowane jako manifesty YAML. Zasady sieciowe mogą być uwzględnione jako część szerszego manifestu, który tworzy również wdrożenie lub usługę.

### <a name="network-policy-options-in-aks"></a>Opcje zasad sieciowych w AKS

Platforma Azure udostępnia dwa sposoby implementowania zasad sieciowych. Podczas tworzenia klastra AKS należy wybrać opcję zasad sieciowych. Opcji zasad nie można zmienić po utworzeniu klastra:

* Implementacja platformy Azure o nazwie *Zasady sieci Azure*.
* *Calico Network Policies*, sieci open-source i rozwiązanie bezpieczeństwa sieci założona przez [Tigera][tigera].

Obie implementacje używają *linux iptables* do wymuszania określonych zasad. Zasady są tłumaczone na zestawy dozwolonych i niedozwolonych par ADRESÓW IP. Pary te są następnie programowane jako reguły filtru IPTable.

### <a name="differences-between-azure-and-calico-policies-and-their-capabilities"></a>Różnice między zasadami platformy Azure i Calico a ich możliwościami

| Możliwości                               | Azure                      | Calico                      |
|------------------------------------------|----------------------------|-----------------------------|
| Obsługiwane platformy                      | Linux                      | Linux                       |
| Obsługiwane opcje sieciowe             | Azure CNI                  | Azure CNI i kubenet       |
| Zgodność ze specyfikacją Kubernetes | Obsługiwane wszystkie typy zasad |  Obsługiwane wszystkie typy zasad |
| Dodatkowe funkcje                      | Brak                       | Rozszerzony model zasad składający się z globalnych zasad sieciowych, globalnego zestawu sieci i punktu końcowego hosta. Aby uzyskać więcej `calicoctl` informacji na temat używania interfejsu wiersza polecenia do zarządzania tymi rozszerzonymi funkcjami, zobacz [odwołanie do użytkownika calicoctl][calicoctl]. |
| Pomoc techniczna                                  | Obsługiwane przez zespół pomocy technicznej i inżynierii platformy Azure | Wsparcie społeczności Calico. Aby uzyskać więcej informacji na temat dodatkowej płatnej pomocy technicznej, zobacz [Opcje pomocy technicznej projektu Calico][calico-support]. |
| Rejestrowanie                                  | Reguły dodawane / usuwane w tabelach IPTables są rejestrowane na każdym hoście pod */var/log/azure-npm.log* | Aby uzyskać więcej informacji, zobacz [Dzienniki komponentów Calico][calico-logs] |

## <a name="create-an-aks-cluster-and-enable-network-policy"></a>Tworzenie klastra usługi AKS i włączanie zasad sieciowych

Aby wyświetlić zasady sieci w działaniu, utwórzmy, a następnie rozwiń na zasadach definiujących przepływ ruchu:

* Odmów wszelkiego ruchu do zasobnika.
* Zezwalaj na ruch oparty na etykietach zasobników.
* Zezwalaj na ruch na podstawie obszaru nazw.

Najpierw utwórzmy klaster AKS, który obsługuje zasady sieciowe. 

> [!IMPORTANT]
>
> Funkcję zasad sieciowych można włączyć tylko podczas tworzenia klastra. Nie można włączyć zasad sieciowych w istniejącym klastrze AKS.

Aby korzystać z zasad sieciowych platformy Azure, należy użyć [wtyczki CNI platformy Azure][azure-cni] i zdefiniować własną sieć wirtualną i podsieci. Aby uzyskać bardziej szczegółowe informacje na temat planowania wymaganych zakresów podsieci, zobacz [konfigurowanie sieci zaawansowanych][use-advanced-networking]. Zasady sieci calico mogą być używane z tą samą wtyczką CNI platformy Azure lub z wtyczką Kubenet CNI.

Następujący przykładowy skrypt:

* Tworzy sieć wirtualną i podsieć.
* Tworzy jednostkę usługi Azure Active Directory (Azure AD) do użytku z klastrem AKS.
* Przypisuje uprawnienia *współautora* dla jednostki usługi klastrowania AKS w sieci wirtualnej.
* Tworzy klaster AKS w zdefiniowanej sieci wirtualnej i włącza zasady sieciowe.
    * Używana jest opcja zasad sieci *azure.* Aby użyć calico jako opcji zasad sieciowych, należy użyć parametru. `--network-policy calico` Uwaga: Calico może być `--network-plugin azure` używany `--network-plugin kubenet`z jednym lub .

Należy zauważyć, że zamiast używać jednostki usługi, można użyć tożsamości zarządzanej dla uprawnień. Aby uzyskać więcej informacji, zobacz [Używanie tożsamości zarządzanych](use-managed-identity.md).

Zapewnij własne bezpieczne *SP_PASSWORD*. Można zastąpić zmienne *RESOURCE_GROUP_NAME* i *CLUSTER_NAME:*

```azurecli-interactive
RESOURCE_GROUP_NAME=myResourceGroup-NP
CLUSTER_NAME=myAKSCluster
LOCATION=canadaeast

# Create a resource group
az group create --name $RESOURCE_GROUP_NAME --location $LOCATION

# Create a virtual network and subnet
az network vnet create \
    --resource-group $RESOURCE_GROUP_NAME \
    --name myVnet \
    --address-prefixes 10.0.0.0/8 \
    --subnet-name myAKSSubnet \
    --subnet-prefix 10.240.0.0/16

# Create a service principal and read in the application ID
SP=$(az ad sp create-for-rbac --output json)
SP_ID=$(echo $SP | jq -r .appId)
SP_PASSWORD=$(echo $SP | jq -r .password)

# Wait 15 seconds to make sure that service principal has propagated
echo "Waiting for service principal to propagate..."
sleep 15

# Get the virtual network resource ID
VNET_ID=$(az network vnet show --resource-group $RESOURCE_GROUP_NAME --name myVnet --query id -o tsv)

# Assign the service principal Contributor permissions to the virtual network resource
az role assignment create --assignee $SP_ID --scope $VNET_ID --role Contributor

# Get the virtual network subnet resource ID
SUBNET_ID=$(az network vnet subnet show --resource-group $RESOURCE_GROUP_NAME --vnet-name myVnet --name myAKSSubnet --query id -o tsv)

# Create the AKS cluster and specify the virtual network and service principal information
# Enable network policy by using the `--network-policy` parameter
az aks create \
    --resource-group $RESOURCE_GROUP_NAME \
    --name $CLUSTER_NAME \
    --node-count 1 \
    --generate-ssh-keys \
    --network-plugin azure \
    --service-cidr 10.0.0.0/16 \
    --dns-service-ip 10.0.0.10 \
    --docker-bridge-address 172.17.0.1/16 \
    --vnet-subnet-id $SUBNET_ID \
    --service-principal $SP_ID \
    --client-secret $SP_PASSWORD \
    --network-policy azure
```

Utworzenie klastra trwa kilka minut. Gdy klaster jest gotowy, skonfiguruj `kubectl` połączenie z klastrem Kubernetes za pomocą polecenia [az aks get-credentials.][az-aks-get-credentials] To polecenie pobiera poświadczenia i konfiguruje interfejsu wiersza polecenia Kubernetes, aby ich używać:

```azurecli-interactive
az aks get-credentials --resource-group $RESOURCE_GROUP_NAME --name $CLUSTER_NAME
```

## <a name="deny-all-inbound-traffic-to-a-pod"></a>Odmów wszelkiego ruchu przychodzącego do zasobnika

Przed zdefiniowaniem reguł zezwalania na określony ruch sieciowy należy najpierw utworzyć zasadę sieciową, która ma uniemożliwić cały ruch. Ta zasada daje punkt wyjścia, aby rozpocząć do białej listy tylko żądany ruch. Można również wyraźnie zobaczyć, że ruch jest usuwany po zastosowaniu zasad sieciowych.

W przypadku przykładowego środowiska aplikacji i reguł ruchu najpierw utwórzmy obszar nazw o nazwie *development,* aby uruchomić przykładowe zasobniki:

```console
kubectl create namespace development
kubectl label namespace/development purpose=development
```

Utwórz przykład zasobnika zaplecza, który uruchamia NGINX. Ten zasobnik zaplecza może służyć do symulacji przykładowej aplikacji sieci web zaplecza. Utwórz ten zasobnik w obszarze nazw *rozwoju* i otwórz port *80,* aby obsługiwać ruch internetowy. Etykieta zasobnika z *app =webapp,role=backend,* dzięki czemu możemy kierować go z zasad sieci w następnej sekcji:

```console
kubectl run backend --image=nginx --labels app=webapp,role=backend --namespace development --expose --port 80 --generator=run-pod/v1
```

Utwórz kolejną zasobnik i dołącz sesję terminala, aby sprawdzić, czy można pomyślnie dotrzeć do domyślnej strony sieci Web NGINX:

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

W wierszu polecenia `wget` powłoki użyj, aby potwierdzić, że można uzyskać dostęp do domyślnej strony sieci Web NGINX:

```console
wget -qO- http://backend
```

Poniższe przykładowe dane wyjściowe pokazują, że zwrócono domyślną stronę sieci Web NGINX:

```output
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Wyjdź z załączonej sesji terminalu. Zasobnik testowy zostanie automatycznie usunięty.

```console
exit
```

### <a name="create-and-apply-a-network-policy"></a>Tworzenie i stosowanie zasad sieciowych

Teraz, po potwierdzeniu, że możesz użyć podstawowej strony sieci Web NGINX w przykładowym zasobniku zaplecza, utwórz zasady sieciowe, aby odmówić całego ruchu. Utwórz plik `backend-policy.yaml` o nazwie i wklej następujący manifest YAML. Ten manifest używa *podSelector* do dołączania zasad do zasobników, które mają *app:webapp,role:etykieta wewnętrznej bazy danych,* takich jak przykładowy zasobnik NGINX. Żadne reguły nie są zdefiniowane w obszarze *ruch przychodzący,* więc cały ruch przychodzący do zasobnika jest odrzucany:

```yaml
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: backend-policy
  namespace: development
spec:
  podSelector:
    matchLabels:
      app: webapp
      role: backend
  ingress: []
```

Przejdź [https://shell.azure.com](https://shell.azure.com) do otwierania usługi Azure Cloud Shell w przeglądarce.

Zastosuj zasady sieciowe za pomocą polecenia [kubectl apply][kubectl-apply] i określ nazwę manifestu YAML:

```console
kubectl apply -f backend-policy.yaml
```

### <a name="test-the-network-policy"></a>Testowanie zasad sieciowych

Zobaczmy, czy możesz ponownie użyć strony internetowej NGINX na zasobniku zaplecza. Utwórz kolejną kapsułę testową i dołącz sesję terminala:

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

W wierszu powłoki użyj, `wget` aby sprawdzić, czy można uzyskać dostęp do domyślnej strony sieci Web NGINX. Tym razem ustaw wartość limitu czasu na *2* sekundy. Zasady sieciowe blokują teraz cały ruch przychodzący, więc nie można załadować strony, jak pokazano w poniższym przykładzie:

```console
wget -qO- --timeout=2 http://backend
```

```output
wget: download timed out
```

Wyjdź z załączonej sesji terminalu. Zasobnik testowy zostanie automatycznie usunięty.

```console
exit
```

## <a name="allow-inbound-traffic-based-on-a-pod-label"></a>Zezwalaj na ruch przychodzący na podstawie etykiety zasobnika

W poprzedniej sekcji zaplanowano zaplecza NGINX zasobnika i utworzono zasady sieciowe, aby odmówić całego ruchu. Utwórzmy zasobnik frontonu i zaktualizujmy zasady sieci, aby umożliwić ruch z zasobników frontonu.

Zaktualizuj zasady sieciowe, aby zezwolić na ruch z zasobników za pomocą *aplikacji etykiety: webapp,role:frontend* i w dowolnym obszarze nazw. Edytuj poprzedni plik *backend-policy.yaml* i dodaj reguły ruchu przychodzącego *matchLabels,* tak aby manifest wyglądał następująco:

```yaml
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: backend-policy
  namespace: development
spec:
  podSelector:
    matchLabels:
      app: webapp
      role: backend
  ingress:
  - from:
    - namespaceSelector: {}
      podSelector:
        matchLabels:
          app: webapp
          role: frontend
```

> [!NOTE]
> Ta zasada sieciowa używa *namespaceSelector* i *podSelector* elementu dla reguły transferu ruchu przychodzącego. Składnia YAML jest ważne dla reguł przychodzących, które mają być addytywne. W tym przykładzie oba elementy muszą być zgodne dla reguły transferu ruchu przychodzącego, która ma zostać zastosowana. Wersje kubernetes przed *1.12* może nie interpretować tych elementów poprawnie i ograniczyć ruch sieciowy, zgodnie z oczekiwaniami. Aby uzyskać więcej informacji na temat tego zachowania, zobacz [Zachowanie selektorów do i od][policy-rules].

Zastosuj zaktualizowane zasady sieciowe za pomocą polecenia [zastosuj kubectl][kubectl-apply] i określ nazwę manifestu YAML:

```console
kubectl apply -f backend-policy.yaml
```

Zaplanuj zasobnik, który jest oznaczony jako *app=webapp,role=frontend* i dołącz sesję terminala:

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace development --generator=run-pod/v1
```

W wierszu polecenia `wget` powłoki użyj, aby sprawdzić, czy można uzyskać dostęp do domyślnej strony sieci Web NGINX:

```console
wget -qO- http://backend
```

Ponieważ reguła transferu danych przychodzących zezwala na ruch z zasobnikami, które mają *aplikacje etykiety: webapp,role: frontend*, ruch z zasobnika frontonu jest dozwolony. Poniższy przykładowy wynik przedstawia domyślną zwróconą stronę sieci Web NGINX:

```output
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Wyjdź z załączonej sesji terminalu. Zasobnik zostanie automatycznie usunięty.

```console
exit
```

### <a name="test-a-pod-without-a-matching-label"></a>Testowanie zasobnika bez pasującej etykiety

Zasady sieciowe zezwalają na ruch z zasobników oznaczonych *jako aplikacja: webapp,role: frontend*, ale powinien odmówić wszelkiego innego ruchu. Przetestujmy, aby zobaczyć, czy inny zasobnik bez tych etykiet może uzyskać dostęp do zaplecza NGINX pod. Utwórz kolejną kapsułę testową i dołącz sesję terminala:

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

W wierszu powłoki użyj, `wget` aby sprawdzić, czy można uzyskać dostęp do domyślnej strony sieci Web NGINX. Zasady sieciowe blokują ruch przychodzący, więc nie można załadować strony, jak pokazano w poniższym przykładzie:

```console
wget -qO- --timeout=2 http://backend
```

```output
wget: download timed out
```

Wyjdź z załączonej sesji terminalu. Zasobnik testowy zostanie automatycznie usunięty.

```console
exit
```

## <a name="allow-traffic-only-from-within-a-defined-namespace"></a>Zezwalaj na ruch tylko z obszaru zdefiniowanego obszaru nazw

W poprzednich przykładach utworzono zasadę sieciową, która zabroniła całego ruchu, a następnie zaktualizowano zasady, aby zezwolić na ruch z zasobników z określoną etykietą. Inną częstą potrzebą jest ograniczenie ruchu tylko w obrębie danego obszaru nazw. Jeśli poprzednie przykłady były dla ruchu w przestrzeni nazw *rozwoju,* należy utworzyć zasady sieci, która uniemożliwia ruch z innego obszaru nazw, takich jak *produkcja,* z dotarciem do zasobników.

Najpierw utwórz nową przestrzeń nazw, aby symulować produkcyjną przestrzeń nazw:

```console
kubectl create namespace production
kubectl label namespace/production purpose=production
```

Zaplanuj zasobnik testowy w *produkcyjnym* obszarze nazw, który jest oznaczony jako *app=webapp,role=frontend*. Załącz sesję terminalu:

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace production --generator=run-pod/v1
```

W wierszu polecenia `wget` powłoki użyj, aby potwierdzić, że można uzyskać dostęp do domyślnej strony sieci Web NGINX:

```console
wget -qO- http://backend.development
```

Ponieważ etykiety zasobnika są zgodne z tym, co jest obecnie dozwolone w zasadach sieciowych, ruch jest dozwolony. Zasady sieciowe nie patrzą na obszary nazw, tylko na etykiety zasobników. Poniższy przykładowy wynik przedstawia domyślną zwróconą stronę sieci Web NGINX:

```output
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Wyjdź z załączonej sesji terminalu. Zasobnik testowy zostanie automatycznie usunięty.

```console
exit
```

### <a name="update-the-network-policy"></a>Aktualizowanie zasad sieciowych

Zaktualizujmy sekcję obszaru nazw reguły transferu danych *przychodzącychPrzekroj,* aby zezwalać tylko na ruch z obszaru nazw *deweloperów.* Edytuj plik manifestu *backend-policy.yaml,* jak pokazano w poniższym przykładzie:

```yaml
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: backend-policy
  namespace: development
spec:
  podSelector:
    matchLabels:
      app: webapp
      role: backend
  ingress:
  - from:
    - namespaceSelector:
        matchLabels:
          purpose: development
      podSelector:
        matchLabels:
          app: webapp
          role: frontend
```

W bardziej złożonych przykładach można zdefiniować wiele reguł przychodzących, takich jak *namespaceSelector,* a następnie *podSelector*.

Zastosuj zaktualizowane zasady sieciowe za pomocą polecenia [zastosuj kubectl][kubectl-apply] i określ nazwę manifestu YAML:

```console
kubectl apply -f backend-policy.yaml
```

### <a name="test-the-updated-network-policy"></a>Testowanie zaktualizowanych zasad sieciowych

Zaplanuj inną zasobnik w *produkcyjnym* obszarze nazw i dołącz sesję terminala:

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace production --generator=run-pod/v1
```

W wierszu polecenia `wget` powłoki użyj, aby zobaczyć, że zasady sieciowe teraz odmawia ruchu:

```console
wget -qO- --timeout=2 http://backend.development
```

```output
wget: download timed out
```

Wyjdź z kapsuły testowej:

```console
exit
```

W celu uniemożliwienia ruchu z obszaru nazw *produkcji* zaplanuj zasobnik testowy z powrotem w obszarze nazw *rozwoju* i dołącz sesję terminala:

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace development --generator=run-pod/v1
```

W wierszu polecenia `wget` powłoki użyj, aby zobaczyć, że zasady sieciowe zezwalają na ruch:

```console
wget -qO- http://backend
```

Ruch jest dozwolony, ponieważ zasobnik jest zaplanowany w obszarze nazw, który pasuje do tego, co jest dozwolone w zasadach sieciowych. Następujące przykładowe dane wyjściowe pokazują zwróconą domyślną stronę sieci Web NGINX:

```output
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Wyjdź z załączonej sesji terminalu. Zasobnik testowy zostanie automatycznie usunięty.

```console
exit
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

W tym artykule utworzyliśmy dwie przestrzenie nazw i zastosowaliśmy zasady sieciowe. Aby wyczyścić te zasoby, użyj polecenia [kubectl delete][kubectl-delete] i określ nazwy zasobów:

```console
kubectl delete namespace production
kubectl delete namespace development
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji o [zasobach sieciowych, zobacz Pojęcia dotyczące sieci dla aplikacji w usłudze Azure Kubernetes Service (AKS)][concepts-network].

Aby dowiedzieć się więcej o zasadach, zobacz [Zasady sieci kubernetes][kubernetes-network-policies].

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubernetes-network-policies]: https://kubernetes.io/docs/concepts/services-networking/network-policies/
[azure-cni]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[policy-rules]: https://kubernetes.io/docs/concepts/services-networking/network-policies/#behavior-of-to-and-from-selectors
[aks-github]: https://github.com/azure/aks/issues
[tigera]: https://www.tigera.io/
[calicoctl]: https://docs.projectcalico.org/v3.9/reference/calicoctl/
[calico-support]: https://www.tigera.io/tigera-products/calico/
[calico-logs]: https://docs.projectcalico.org/v3.9/maintenance/component-logs
[calico-aks-cleanup]: https://github.com/Azure/aks-engine/blob/master/docs/topics/calico-3.3.1-cleanup-after-upgrade.yaml

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[use-advanced-networking]: configure-advanced-networking.md
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[concepts-network]: concepts-network.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
