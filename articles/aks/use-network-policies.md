---
title: Zabezpieczanie ruchu pod względem zasad sieciowych
titleSuffix: Azure Kubernetes Service
description: Dowiedz się, jak zabezpieczyć ruch przepływający do i z zasobników przy użyciu zasad sieciowych Kubernetes w usłudze Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 05/06/2019
ms.openlocfilehash: 1d3aa49a749890783fdae589edab3d1910b2ac73
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101729423"
---
# <a name="secure-traffic-between-pods-using-network-policies-in-azure-kubernetes-service-aks"></a>Zabezpieczanie ruchu między różnymi sieciami przy użyciu zasad sieciowych w usłudze Azure Kubernetes Service (AKS)

W przypadku uruchamiania nowoczesnych aplikacji opartych na mikrousługach w programie Kubernetes często trzeba kontrolować, które składniki mogą komunikować się ze sobą. Zasada najniższych uprawnień powinna być stosowana do sposobu przepływu ruchu między jednostkami w klastrze usługi Azure Kubernetes Service (AKS). Załóżmy, że wiesz, że chcesz blokować ruch bezpośrednio do aplikacji zaplecza. Funkcja *zasad sieciowych* w programie Kubernetes umożliwia definiowanie reguł ruchu przychodzącego i wychodzącego między zasobnikami w klastrze.

W tym artykule opisano sposób instalowania aparatu zasad sieciowych i tworzenia zasad sieciowych Kubernetes w celu kontrolowania przepływu ruchu między nimi w AKS. Zasady sieciowe powinny być używane tylko dla węzłów i zasobników opartych na systemie Linux w AKS.

## <a name="before-you-begin"></a>Zanim rozpoczniesz

Wymagany jest interfejs wiersza polecenia platformy Azure w wersji 2.0.61 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][install-azure-cli].

> [!TIP]
> W przypadku użycia funkcji zasad sieciowych w trakcie korzystania z wersji zapoznawczej zalecamy [utworzenie nowego klastra](#create-an-aks-cluster-and-enable-network-policy).
> 
> Jeśli chcesz kontynuować korzystanie z istniejących klastrów testowych, które używały zasad sieciowych w trakcie okresu zapoznawczego, Uaktualnij klaster do nowej wersji usługi Kubernetes w najnowszej wersji, a następnie wdróż następujący manifest YAML, aby naprawić serwer metryk z awariami i pulpit nawigacyjny Kubernetes. Ta poprawka jest wymagana tylko w przypadku klastrów, które używały aparatu zasad sieciowych Calico.
>
> Zgodnie z najlepszymi rozwiązaniami w zakresie bezpieczeństwa, [zapoznaj się z zawartością tego manifestu YAML][calico-aks-cleanup] , aby zrozumieć, co jest wdrażane w klastrze AKS.
>
> `kubectl delete -f https://raw.githubusercontent.com/Azure/aks-engine/master/docs/topics/calico-3.3.1-cleanup-after-upgrade.yaml`

## <a name="overview-of-network-policy"></a>Omówienie zasad sieciowych

Wszystkie zasobniki w klastrze AKS mogą domyślnie wysyłać i odbierać ruch bez ograniczeń. Aby zwiększyć bezpieczeństwo, można zdefiniować reguły sterujące przepływem ruchu. Aplikacje zaplecza często są widoczne tylko dla wymaganych usług frontonu, na przykład. Lub składniki bazy danych są dostępne tylko dla warstw aplikacji, które łączą się z nimi.

Zasady sieciowe to specyfikacja Kubernetes, która definiuje zasady dostępu do komunikacji między serwerami. Korzystając z zasad sieciowych, należy zdefiniować uporządkowany zestaw reguł do wysyłania i odbierania ruchu, a następnie zastosować je do kolekcji zestawów, które pasują do jednego lub kilku selektorów etykiet.

Te reguły zasad sieciowych są zdefiniowane jako manifesty YAML. Zasady sieciowe mogą być dołączane jako część szerszego manifestu, który tworzy również wdrożenie lub usługę.

### <a name="network-policy-options-in-aks"></a>Opcje zasad sieciowych w AKS

Platforma Azure zapewnia dwa sposoby implementacji zasad sieciowych. Podczas tworzenia klastra AKS wybierz opcję zasad sieciowych. Nie można zmienić opcji zasad po utworzeniu klastra:

* Własna implementacja platformy Azure, nazywana *zasadami sieci platformy Azure*.
* *Calico zasady sieci*, rozwiązanie zabezpieczeń sieci i sieci "open source" oparte na [Tiger][tigera]a.

Obie implementacje używają systemu Linux *dołączenie iptables* , aby wymusić określone zasady. Zasady są tłumaczone na zestawy dozwolonych i niedozwolonych par adresów IP. Te pary są następnie zaprogramowane jako reguły filtru IPTable.

### <a name="differences-between-azure-and-calico-policies-and-their-capabilities"></a>Różnice między zasadami platformy Azure i Calico oraz ich możliwościami

| Możliwość                               | Azure                      | Calico                      |
|------------------------------------------|----------------------------|-----------------------------|
| Obsługiwane platformy                      | Linux                      | Linux, Windows Server 2019 (wersja zapoznawcza)  |
| Obsługiwane opcje sieci             | Azure CNI                  | Azure CNI (systemy Windows Server 2019 i Linux) i korzystającą wtyczki kubenet (Linux)  |
| Zgodność ze specyfikacją Kubernetes | Wszystkie typy zasad obsługiwane |  Wszystkie typy zasad obsługiwane |
| Dodatkowe funkcje                      | Brak                       | Rozszerzony model zasad składający się z globalnych zasad sieciowych, globalnego zestawu sieci i punktu końcowego hosta. Aby uzyskać więcej informacji na temat korzystania z `calicoctl` interfejsu wiersza polecenia do zarządzania tymi rozszerzonymi funkcjami, zobacz [calicoctl User Reference][calicoctl]. |
| Pomoc techniczna                                  | Obsługiwane przez zespół pomocy technicznej i inżynierów platformy Azure | Wsparcie społeczności Calico. Aby uzyskać więcej informacji na temat dodatkowej płatnej pomocy technicznej, zobacz [Opcje pomocy technicznej dla programu Project Calico][calico-support]. |
| Rejestrowanie                                  | Reguły dodane/usunięte w dołączenie iptables są rejestrowane na każdym hoście w obszarze */var/log/Azure-npm.log* | Aby uzyskać więcej informacji, zobacz [dzienniki składników Calico][calico-logs] |

## <a name="create-an-aks-cluster-and-enable-network-policy"></a>Tworzenie klastra AKS i Włączanie zasad sieciowych

Aby wyświetlić zasady sieciowe w działaniu, Utwórz i rozwiń zasady, które definiują przepływ ruchu:

* Odrzuć cały ruch do pod.
* Zezwalaj na ruch na podstawie etykiet pod.
* Zezwalaj na ruch na podstawie przestrzeni nazw.

Najpierw Utwórzmy klaster AKS, który obsługuje zasady sieciowe.

> [!IMPORTANT]
>
> Funkcja zasad sieciowych można włączyć tylko podczas tworzenia klastra. Nie można włączyć zasad sieciowych w istniejącym klastrze AKS.

Aby korzystać z zasad sieciowych platformy Azure, musisz użyć [wtyczki CNI platformy Azure][azure-cni] i zdefiniować własną sieć wirtualną oraz podsieci. Aby uzyskać szczegółowe informacje na temat planowania wymaganych zakresów podsieci, zobacz [Konfigurowanie zaawansowanej sieci][use-advanced-networking]. Zasad sieciowych Calico można użyć z tą samą wtyczką CNI platformy Azure lub z dodatkiem plug-in korzystającą wtyczki kubenet CNI.

Następujący przykładowy skrypt:

* Tworzy sieć wirtualną i podsieć.
* Tworzy jednostkę usługi Azure Active Directory (Azure AD) do użycia z klastrem AKS.
* Przypisuje uprawnienia *współautora* dla jednostki usługi klastra AKS w sieci wirtualnej.
* Tworzy klaster AKS w zdefiniowanej sieci wirtualnej i włącza zasady sieciowe.
    * Opcja zasad _sieciowych platformy Azure_ jest używana. Aby zamiast tego użyć Calico jako opcji zasad sieciowych, należy użyć `--network-policy calico` parametru. Uwaga: Calico można użyć z `--network-plugin azure` lub `--network-plugin kubenet` .

Należy pamiętać, że zamiast używać nazwy głównej usługi, można użyć tożsamości zarządzanej w celu uzyskania uprawnień. Aby uzyskać więcej informacji, zobacz [Korzystanie z tożsamości zarządzanych](use-managed-identity.md).

Podaj własne bezpieczne *SP_PASSWORD*. Można zastąpić zmienne *RESOURCE_GROUP_NAME* i *CLUSTER_NAME* :

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
```

### <a name="create-an-aks-cluster-for-azure-network-policies"></a>Tworzenie klastra AKS dla zasad sieciowych platformy Azure

Utwórz klaster AKS i określ sieć wirtualną, informacje o głównej usłudze i *platformę Azure* dla wtyczki sieciowej i zasad sieciowych.

```azurecli
az aks create \
    --resource-group $RESOURCE_GROUP_NAME \
    --name $CLUSTER_NAME \
    --node-count 1 \
    --generate-ssh-keys \
    --service-cidr 10.0.0.0/16 \
    --dns-service-ip 10.0.0.10 \
    --docker-bridge-address 172.17.0.1/16 \
    --vnet-subnet-id $SUBNET_ID \
    --service-principal $SP_ID \
    --client-secret $SP_PASSWORD \
    --network-plugin azure \
    --network-policy azure
```

Utworzenie klastra trwa kilka minut. Gdy klaster jest gotowy, skonfiguruj, `kubectl` Aby nawiązać połączenie z klastrem Kubernetes za pomocą polecenia [AZ AKS Get-Credentials][az-aks-get-credentials] . To polecenie umożliwia pobranie poświadczeń i skonfigurowanie interfejsu wiersza polecenia Kubernetes do ich użycia:

```azurecli-interactive
az aks get-credentials --resource-group $RESOURCE_GROUP_NAME --name $CLUSTER_NAME
```

### <a name="create-an-aks-cluster-for-calico-network-policies"></a>Tworzenie klastra AKS dla zasad sieciowych Calico

Utwórz klaster AKS i określ sieć wirtualną, informacje o głównej usłudze, *platformę Azure* dla wtyczki sieciowej oraz *Calico* zasad sieciowych. Użycie *Calico* jako zasad sieciowych umożliwia Calico sieci zarówno w przypadku pul węzłów systemu Linux, jak i Windows.

Jeśli planujesz Dodawanie pul węzłów systemu Windows do klastra, Dołącz `windows-admin-username` Parametry i, `windows-admin-password` które spełniają [wymagania dotyczące hasła systemu Windows Server][windows-server-password]. Aby używać Calico z pulami węzłów systemu Windows, należy również zarejestrować `Microsoft.ContainerService/EnableAKSWindowsCalico` .

Zarejestruj `EnableAKSWindowsCalico` flagę funkcji za pomocą polecenia [AZ Feature Register][az-feature-register] , jak pokazano w następującym przykładzie:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "EnableAKSWindowsCalico"
```

 Stan rejestracji można sprawdzić za pomocą polecenia [AZ Feature list][az-feature-list] :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/EnableAKSWindowsCalico')].{Name:name,State:properties.state}"
```

Gdy wszystko będzie gotowe, Odśwież rejestrację dostawcy zasobów *Microsoft. ContainerService* za pomocą polecenia [AZ Provider Register][az-provider-register] :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

> [!IMPORTANT]
> W tej chwili Używanie zasad sieciowych Calico z węzłami systemu Windows jest dostępne w nowych klastrach przy użyciu Kubernetes w wersji 1,20 lub nowszej z Calico 3.17.2 i wymaga użycia sieci Azure CNI. Węzły systemu Windows w klastrach AKS z włączonym Calicoem również mają domyślnie włączoną funkcję [bezpośredniego powrotu serwera (DSR)][dsr] .
>
> W przypadku klastrów z tylko pulami węzłów systemu Linux z systemem Kubernetes 1,20 ze starszymi wersjami Calico wersja Calico zostanie automatycznie uaktualniona do 3.17.2.

Zasady sieci Calico z węzłami systemu Windows są obecnie dostępne w wersji zapoznawczej.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

```azurecli
PASSWORD_WIN="P@ssw0rd1234"

az aks create \
    --resource-group $RESOURCE_GROUP_NAME \
    --name $CLUSTER_NAME \
    --node-count 1 \
    --generate-ssh-keys \
    --service-cidr 10.0.0.0/16 \
    --dns-service-ip 10.0.0.10 \
    --docker-bridge-address 172.17.0.1/16 \
    --vnet-subnet-id $SUBNET_ID \
    --service-principal $SP_ID \
    --client-secret $SP_PASSWORD \
    --windows-admin-password $PASSWORD_WIN \
    --windows-admin-username azureuser \
    --vm-set-type VirtualMachineScaleSets \
    --kubernetes-version 1.20.2 \
    --network-plugin azure \
    --network-policy calico
```

Utworzenie klastra trwa kilka minut. Domyślnie klaster jest tworzony tylko za pomocą puli węzłów systemu Linux. Jeśli chcesz użyć pul węzłów systemu Windows, możesz dodać jeden z nich. Na przykład:

```azurecli
az aks nodepool add \
    --resource-group $RESOURCE_GROUP_NAME \
    --cluster-name $CLUSTER_NAME \
    --os-type Windows \
    --name npwin \
    --node-count 1
```

Gdy klaster jest gotowy, skonfiguruj, `kubectl` Aby nawiązać połączenie z klastrem Kubernetes za pomocą polecenia [AZ AKS Get-Credentials][az-aks-get-credentials] . To polecenie umożliwia pobranie poświadczeń i skonfigurowanie interfejsu wiersza polecenia Kubernetes do ich użycia:

```azurecli-interactive
az aks get-credentials --resource-group $RESOURCE_GROUP_NAME --name $CLUSTER_NAME
```

## <a name="deny-all-inbound-traffic-to-a-pod"></a>Odrzuć cały ruch przychodzący do pod

Przed zdefiniowaniem reguł zezwalających na określony ruch sieciowy należy najpierw utworzyć zasady sieciowe, aby odmówić całego ruchu. Te zasady zapewniają punkt początkowy, od którego należy zacząć tworzyć listę dozwolonych tylko dla żądanego ruchu. Możesz również jasno zobaczyć, że ruch zostanie porzucony podczas stosowania zasad sieciowych.

W przypadku przykładowych zasad dotyczących środowiska aplikacji i ruchu Utwórzmy najpierw przestrzeń nazw o nazwie *programowanie* , aby uruchomić przykładowe zasobniki:

```console
kubectl create namespace development
kubectl label namespace/development purpose=development
```

Utwórz przykładowy przykład zaplecza z systemem NGINX. Tego zaplecza można użyć, aby symulować przykładową aplikację opartą na sieci Web zaplecza. Utwórz ten obszar w przestrzeni nazw *Development* i otwórz port *80* w celu obsługi ruchu w sieci Web. Oznacz element pod za pomocą *aplikacji App = webapp, role = zaplecza* , tak aby można było określić go przy użyciu zasad sieciowych w następnej sekcji:

```console
kubectl run backend --image=nginx --labels app=webapp,role=backend --namespace development --expose --port 80
```

Utwórz kolejną stronę pod i Dołącz sesję terminalową, aby sprawdzić, czy można pomyślnie uzyskać dostęp do domyślnej strony sieci Web NGINX:

```console
kubectl run --rm -it --image=alpine network-policy --namespace development
```

W wierszu polecenia powłoki Użyj, `wget` Aby potwierdzić, że możesz uzyskać dostęp do domyślnej strony sieci Web Nginx:

```console
wget -qO- http://backend
```

Następujące przykładowe dane wyjściowe pokazują, że zwracana jest domyślna strona sieci Web NGINX:

```output
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Wyjdź z dołączonej sesji terminala. Test pod zostanie automatycznie usunięty.

```console
exit
```

### <a name="create-and-apply-a-network-policy"></a>Tworzenie i stosowanie zasad sieciowych

Po potwierdzeniu, że możesz użyć podstawowej strony sieci Web NGINX na przykładowej zaplecza, Utwórz zasady sieciowe, aby odmówić całego ruchu. Utwórz plik o nazwie `backend-policy.yaml` i wklej następujący manifest YAML. Ten manifest używa *podSelector* do dołączania zasad do zasobników, które mają etykietę *App: webapp, role: zaplecza* , taką jak przykład Nginx. *W ramach ruchu* przychodzącego nie są zdefiniowane żadne reguły, dlatego cały ruch przychodzący do niego jest odrzucany:

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

Przejdź do [https://shell.azure.com](https://shell.azure.com) okna, aby otworzyć Azure Cloud Shell w przeglądarce.

Zastosuj zasady sieci przy użyciu polecenia [polecenia kubectl Apply][kubectl-apply] i określ nazwę manifestu YAML:

```console
kubectl apply -f backend-policy.yaml
```

### <a name="test-the-network-policy"></a>Testowanie zasad sieciowych

Sprawdźmy, czy można ponownie użyć strony sieci Web NGINX na zapleczu. Utwórz inny test pod i Dołącz sesję terminala:

```console
kubectl run --rm -it --image=alpine network-policy --namespace development
```

W wierszu polecenia powłoki Użyj, `wget` Aby zobaczyć, czy można uzyskać dostęp do domyślnej strony sieci Web Nginx. Tym razem ustaw wartość limitu czasu na *2* sekundy. Zasady sieciowe blokują teraz cały ruch przychodzący, więc nie można załadować strony, jak pokazano w następującym przykładzie:

```console
wget -qO- --timeout=2 http://backend
```

```output
wget: download timed out
```

Wyjdź z dołączonej sesji terminala. Test pod zostanie automatycznie usunięty.

```console
exit
```

## <a name="allow-inbound-traffic-based-on-a-pod-label"></a>Zezwalaj na ruch przychodzący na podstawie etykiety pod

W poprzedniej sekcji zaplanowano NGINX zaplecza, a zasady sieciowe zostały utworzone w celu odblokowania całego ruchu. Utwórzmy fronton pod i zaktualizuj zasady sieciowe, aby umożliwić ruch z zasobników frontonu.

Aktualizowanie zasad sieciowych w celu zezwalania na ruch z aplikacji w postaci etykiet *: webapp, role: fronton* i w dowolnej przestrzeni nazw. Edytuj poprzedni plik *zaplecza-Policy. YAML* i Dodaj reguły transferu *matchLabels* , aby manifest wyglądał następująco:

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
> Ta zasada sieciowa używa elementu *namespaceSelector* i *podSelector* dla reguły transferu danych przychodzących. Składnia YAML jest ważna dla reguł transferu danych przychodzących. W tym przykładzie oba elementy muszą być zgodne dla reguły transferu danych przychodzących. Wersje Kubernetes wcześniejsze niż *1,12* mogą nie interpretować tych elementów poprawnie i ograniczać ruch sieciowy zgodnie z oczekiwaniami. Aby uzyskać więcej informacji na temat tego zachowania, zobacz [zachowanie elementów i selektorów][policy-rules].

Zastosuj zaktualizowane zasady sieci przy użyciu polecenia [polecenia kubectl Apply][kubectl-apply] i określ nazwę manifestu YAML:

```console
kubectl apply -f backend-policy.yaml
```

Zaplanuj pod nazwą *App = webapp, role = fronton* i Dołącz sesję terminalową:

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace development
```

W wierszu polecenia powłoki Użyj, `wget` Aby zobaczyć, czy można uzyskać dostęp do domyślnej strony sieci Web Nginx:

```console
wget -qO- http://backend
```

Ponieważ reguła ruchu przychodzącego zezwala na ruch z punktami końcowymi, które mają aplikację Labels *: webapp, role: fronton*, ruch z frontonu poniżej jest dozwolony. Następujące przykładowe dane wyjściowe przedstawiają domyślną stronę sieci Web NGINX:

```output
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Wyjdź z dołączonej sesji terminala. Wartość pod jest automatycznie usuwana.

```console
exit
```

### <a name="test-a-pod-without-a-matching-label"></a>Przetestuj element pod bez pasującej etykiety

Zasady sieciowe zezwalają na ruch z aplikacji z nazwami oznaczonymi etykietą *App: webapp, role: fronton*, ale powinny odrzucać cały ruch. Przetestujmy, aby sprawdzić, czy inny pod bez etykiet nie może uzyskać dostępu do NGINX zaplecza pod. Utwórz inny test pod i Dołącz sesję terminala:

```console
kubectl run --rm -it --image=alpine network-policy --namespace development
```

W wierszu polecenia powłoki Użyj, `wget` Aby zobaczyć, czy można uzyskać dostęp do domyślnej strony sieci Web Nginx. Zasady sieciowe blokują ruch przychodzący, więc nie można załadować strony, jak pokazano w następującym przykładzie:

```console
wget -qO- --timeout=2 http://backend
```

```output
wget: download timed out
```

Wyjdź z dołączonej sesji terminala. Test pod zostanie automatycznie usunięty.

```console
exit
```

## <a name="allow-traffic-only-from-within-a-defined-namespace"></a>Zezwalaj na ruch tylko z wewnątrz zdefiniowanej przestrzeni nazw

W poprzednich przykładach zostały utworzone zasady sieci, które odrzuciły cały ruch, a następnie Zaktualizowano zasady w celu zezwolenia na ruch z programu z określoną etykietą. Innym często spotykanym zapotrzebowaniem jest ograniczanie ruchu tylko w obrębie danego obszaru nazw. Jeśli poprzednie przykłady dotyczyły ruchu w przestrzeni nazw *deweloperskiej* , Utwórz zasady sieci, które uniemożliwiają ruch z innej przestrzeni nazw, takiej jak *produkcja*, od osiągnięcia do nich zasobników.

Najpierw utwórz nową przestrzeń nazw, aby symulować produkcyjną przestrzeń nazw:

```console
kubectl create namespace production
kubectl label namespace/production purpose=production
```

Zaplanuj test pod w przestrzeni nazw *produkcyjny* , który jest oznaczony jako *App = webapp, role = fronton*. Dołącz sesję terminalową:

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace production
```

W wierszu polecenia powłoki Użyj, `wget` Aby potwierdzić, że możesz uzyskać dostęp do domyślnej strony sieci Web Nginx:

```console
wget -qO- http://backend.development
```

Ponieważ etykiety pod pod kątem zgodności są obecnie dozwolone w zasadach sieciowych, ruch jest dozwolony. Zasady sieciowe nie sprawdzają się w przestrzeniach nazw, tylko w etykietach pod. Następujące przykładowe dane wyjściowe przedstawiają domyślną stronę sieci Web NGINX:

```output
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Wyjdź z dołączonej sesji terminala. Test pod zostanie automatycznie usunięty.

```console
exit
```

### <a name="update-the-network-policy"></a>Aktualizowanie zasad sieciowych

Zaktualizujmy sekcję *namespaceSelector* reguły transferu danych przychodzących, aby zezwalać tylko na ruch z przestrzeni nazw *deweloperskich* . Edytuj plik manifestu *zaplecza-Policy. YAML* , jak pokazano w następującym przykładzie:

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

Bardziej skomplikowane przykłady umożliwiają zdefiniowanie wielu reguł dotyczących ruchu przychodzącego, takich jak *namespaceSelector* , a następnie *podSelector*.

Zastosuj zaktualizowane zasady sieci przy użyciu polecenia [polecenia kubectl Apply][kubectl-apply] i określ nazwę manifestu YAML:

```console
kubectl apply -f backend-policy.yaml
```

### <a name="test-the-updated-network-policy"></a>Testowanie zaktualizowanych zasad sieciowych

Zaplanuj inny element pod w przestrzeni nazw *produkcyjnej* i Dołącz sesję terminala:

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace production
```

W wierszu polecenia powłoki Użyj, `wget` Aby zobaczyć, że zasady sieciowe odrzucają teraz ruch:

```console
wget -qO- --timeout=2 http://backend.development
```

```output
wget: download timed out
```

Wyjdź z testu pod:

```console
exit
```

Mając odmowę ruchu z przestrzeni nazw *produkcyjnej* , Zaplanuj test na odwrocie w przestrzeni nazw *deweloperskiej* i Dołącz sesję terminala:

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace development
```

W wierszu polecenia powłoki Użyj, `wget` Aby zobaczyć, że zasady sieciowe zezwalają na ruch:

```console
wget -qO- http://backend
```

Ruch jest dozwolony, ponieważ jest zaplanowany w przestrzeni nazw, który jest zgodny z dozwolonymi zasadami sieciowymi. Następujące przykładowe dane wyjściowe przedstawiają domyślną stronę sieci Web NGINX:

```output
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Wyjdź z dołączonej sesji terminala. Test pod zostanie automatycznie usunięty.

```console
exit
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

W tym artykule utworzyliśmy dwie przestrzenie nazw i zastosowano zasady sieci. Aby wyczyścić te zasoby, użyj polecenia [polecenia kubectl Delete][kubectl-delete] i określ nazwy zasobów:

```console
kubectl delete namespace production
kubectl delete namespace development
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji o zasobach sieciowych, zobacz [pojęcia dotyczące sieci dla aplikacji w usłudze Azure Kubernetes Service (AKS)][concepts-network].

Aby dowiedzieć się więcej na temat zasad, zobacz [Kubernetes Network policies][kubernetes-network-policies].

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubernetes-network-policies]: https://kubernetes.io/docs/concepts/services-networking/network-policies/
[azure-cni]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[policy-rules]: https://kubernetes.io/docs/concepts/services-networking/network-policies/#behavior-of-to-and-from-selectors
[aks-github]: https://github.com/azure/aks/issues
[tigera]: https://www.tigera.io/
[calicoctl]: https://docs.projectcalico.org/reference/calicoctl/
[calico-support]: https://www.tigera.io/tigera-products/calico/
[calico-logs]: https://docs.projectcalico.org/maintenance/troubleshoot/component-logs
[calico-aks-cleanup]: https://github.com/Azure/aks-engine/blob/master/docs/topics/calico-3.3.1-cleanup-after-upgrade.yaml

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[use-advanced-networking]: configure-azure-cni.md
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[concepts-network]: concepts-network.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[windows-server-password]: /windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements#reference
[az-extension-add]: /cli/azure/extension?view=azure-cli-latest#az-extension-add
[az-extension-update]: /cli/azure/extension?view=azure-cli-latest#az-extension-update
[dsr]: ../load-balancer/load-balancer-multivip-overview.md#rule-type-2-backend-port-reuse-by-using-floating-ip