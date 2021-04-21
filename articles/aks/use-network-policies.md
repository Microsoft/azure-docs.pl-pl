---
title: Zabezpieczanie ruchu zasobników przy użyciu zasad sieciowych
titleSuffix: Azure Kubernetes Service
description: Dowiedz się, jak zabezpieczyć ruch, który przepływa do zasobników i z nich, przy użyciu zasad sieci kubernetes w Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 03/16/2021
ms.openlocfilehash: b05c4add0a62f07b187376d670f23179ba97f3a8
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107767443"
---
# <a name="secure-traffic-between-pods-using-network-policies-in-azure-kubernetes-service-aks"></a>Zabezpieczanie ruchu między zasobnikami przy użyciu zasad sieciowych Azure Kubernetes Service (AKS)

Po uruchomieniu nowoczesnych aplikacji opartych na mikrousługach na kubernetes często chcesz kontrolować, które składniki mogą komunikować się ze sobą. Zasada najmniejszych uprawnień powinna być stosowana do sposobu przepływu ruchu między zasobnikami w klastrze Azure Kubernetes Service (AKS). Załóżmy, że prawdopodobnie chcesz zablokować ruch bezpośrednio do aplikacji za back-end. Funkcja *zasad sieciowych* na platformie Kubernetes umożliwia definiowanie reguł ruchu przychodzących i wychodzącego między zasobnikami w klastrze.

W tym artykule przedstawiono sposób instalowania aparatu zasad sieciowych i tworzenia zasad sieciowych kubernetes w celu kontrolowania przepływu ruchu między zasobnikami w u usługi AKS. Zasady sieciowe powinny być używane tylko w przypadku węzłów i zasobników opartych na systemie Linux w u usługach AKS.

## <a name="before-you-begin"></a>Zanim rozpoczniesz

Musisz zainstalować i skonfigurować interfejs wiersza polecenia platformy Azure w wersji 2.0.61 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][install-azure-cli].

> [!TIP]
> Jeśli funkcja zasad sieciowych została użyta w wersji zapoznawczej, zaleca się utworzenie [nowego klastra](#create-an-aks-cluster-and-enable-network-policy).
> 
> Jeśli chcesz nadal korzystać z istniejących klastrów testowych, które użyły zasad sieciowych w wersji zapoznawczej, uaktualnij klaster do nowej wersji kubernetes dla najnowszej wersji ga,a następnie wdróż następujący manifest YAML, aby naprawić awarię serwera metryk i pulpitu nawigacyjnego Kubernetes. Ta poprawka jest wymagana tylko w przypadku klastrów, które używały aparatu zasad sieciowych Calico.
>
> Najlepszym rozwiązaniem w zakresie zabezpieczeń jest przejrzenie zawartości tego [manifestu YAML,][calico-aks-cleanup] aby zrozumieć, co jest wdrażane w klastrze usługi AKS.
>
> `kubectl delete -f https://raw.githubusercontent.com/Azure/aks-engine/master/docs/topics/calico-3.3.1-cleanup-after-upgrade.yaml`

## <a name="overview-of-network-policy"></a>Omówienie zasad sieciowych

Domyślnie wszystkie zasobniki w klastrze usługi AKS mogą wysyłać i odbierać ruch bez ograniczeń. Aby zwiększyć bezpieczeństwo, można zdefiniować reguły, które kontrolują przepływ ruchu. Aplikacje back-end są często widoczne tylko dla wymaganych usług frontonu, na przykład. Składniki bazy danych są również dostępne tylko dla warstw aplikacji, które się z nimi łączą.

Zasady sieciowe to specyfikacja kubernetes definiująca zasady dostępu do komunikacji między zasobnikami. Za pomocą zasad sieciowych definiuje się uporządkowany zestaw reguł wysyłania i odbierania ruchu oraz stosowania go do kolekcji zasobników, które pasują do co najmniej jednego selektora etykiet.

Te reguły zasad sieciowych są zdefiniowane jako manifesty YAML. Zasady sieciowe mogą być uwzględniane w szerszym manifeście, który tworzy również wdrożenie lub usługę.

### <a name="network-policy-options-in-aks"></a>Opcje zasad sieciowych w u usługi AKS

Platforma Azure oferuje dwa sposoby implementacji zasad sieciowych. Opcję zasad sieciowych należy wybrać podczas tworzenia klastra usługi AKS. Opcji zasad nie można zmienić po utworzeniu klastra:

* Własną implementację platformy Azure o nazwie *Azure Network Policies*.
* *Calico Network Policies*— rozwiązanie typu open source do zabezpieczeń sieci i sieci, które jest oparte na [rozwiązaniu firmy Zaa.][tigera]

Obie implementacje używają tabel *IPTable systemu* Linux do wymuszania określonych zasad. Zasady są tłumaczone na zestawy dozwolonych i niedozwolone par adresów IP. Te pary są następnie programowane jako reguły filtrowania IPTable.

### <a name="differences-between-azure-and-calico-policies-and-their-capabilities"></a>Różnice między zasadami platformy Azure i platformą Calico oraz ich możliwościami

| Możliwość                               | Azure                      | Calico                      |
|------------------------------------------|----------------------------|-----------------------------|
| Obsługiwane platformy                      | Linux                      | Linux, Windows Server 2019 (wersja zapoznawcza)  |
| Obsługiwane opcje sieci             | Azure CNI                  | Azure CNI (Windows Server 2019 i Linux) i kubenet (Linux)  |
| Zgodność ze specyfikacją Kubernetes | Wszystkie obsługiwane typy zasad |  Wszystkie obsługiwane typy zasad |
| Dodatkowe funkcje                      | Brak                       | Rozszerzony model zasad składający się z globalnych zasad sieciowych, globalnego zestawu sieci i punktu końcowego hosta. Aby uzyskać więcej informacji na temat używania interfejsu wiersza polecenia do zarządzania tymi rozszerzonymi `calicoctl` funkcjami, zobacz [calicoctl user reference][calicoctl]. |
| Pomoc techniczna                                  | Obsługiwane przez zespół inżynierów i pomocy technicznej platformy Azure | Pomoc techniczna dla społeczności firmy Calico. Aby uzyskać więcej informacji na temat dodatkowej płatnej pomocy technicznej, zobacz [Project Calico support options (Opcje pomocy technicznej projektu Calico).][calico-support] |
| Rejestrowanie                                  | Reguły dodane/usunięte w tabelach IPTable są rejestrowane na każdym hoście w *obszarze /var/log/azure-npm.log* | Aby uzyskać więcej informacji, zobacz [Dzienniki składników calico][calico-logs] |

## <a name="create-an-aks-cluster-and-enable-network-policy"></a>Tworzenie klastra usługi AKS i włączanie zasad sieciowych

Aby zobaczyć zasady sieciowe w działaniu, utwórzmy, a następnie rozwińmy zasady definiujące przepływ ruchu:

* Odmów całego ruchu do zasobnika.
* Zezwalaj na ruch na podstawie etykiet zasobników.
* Zezwalaj na ruch na podstawie przestrzeni nazw.

Najpierw utwórzmy klaster usługi AKS, który obsługuje zasady sieciowe.

> [!IMPORTANT]
>
> Funkcję zasad sieciowych można włączyć tylko podczas tworzenia klastra. Nie można włączyć zasad sieciowych w istniejącym klastrze usługi AKS.

Aby korzystać z usługi Azure Network Policy, musisz użyć [wtyczki Azure CNI][azure-cni] i zdefiniować własną sieć wirtualną i podsieci. Aby uzyskać bardziej szczegółowe informacje na temat planowania wymaganych zakresów podsieci, zobacz [konfigurowanie zaawansowanej sieci][use-advanced-networking]. Zasady sieci Calico mogą być używane z tą samą Azure CNI wtyczki lub z wtyczką Kubenet CNI.

Poniższy przykładowy skrypt:

* Tworzy sieć wirtualną i podsieć.
* Tworzy jednostkę Azure Active Directory (Azure AD) do użycia z klastrem AKS.
* Przypisuje uprawnienia *współautora* do jednostki usługi klastra AKS w sieci wirtualnej.
* Tworzy klaster usługi AKS w zdefiniowanej sieci wirtualnej i włącza zasady sieciowe.
    * Używana jest opcja Zasady sieci platformy _Azure._ Aby zamiast tego użyć mechanizmu Calico jako opcji zasad sieciowych, użyj `--network-policy calico` parametru . Uwaga: Calico może być używany z albo `--network-plugin azure` lub `--network-plugin kubenet` .

Pamiętaj, że zamiast jednostki usługi możesz użyć tożsamości zarządzanej na użytek uprawnień. Aby uzyskać więcej informacji, zobacz [Use managed identities (Korzystanie z tożsamości zarządzanych).](use-managed-identity.md)

Podaj własne bezpieczne *SP_PASSWORD*. Możesz zastąpić *RESOURCE_GROUP_NAME* i *CLUSTER_NAME* zmienne:

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

Utwórz klaster AKS i określ sieć wirtualną, informacje o nazwie głównej usługi oraz *platformę Azure* dla wtyczki sieci i zasad sieciowych.

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

Utworzenie klastra trwa kilka minut. Gdy klaster będzie gotowy, skonfiguruj go w celu nawiązania połączenia z klastrem `kubectl` Kubernetes za pomocą [polecenia az aks get-credentials.][az-aks-get-credentials] To polecenie pobiera poświadczenia i konfiguruje interfejs wiersza polecenia kubernetes do ich użycia:

```azurecli-interactive
az aks get-credentials --resource-group $RESOURCE_GROUP_NAME --name $CLUSTER_NAME
```

### <a name="create-an-aks-cluster-for-calico-network-policies"></a>Tworzenie klastra AKS dla zasad sieciowych Calico

Utwórz klaster AKS i określ sieć wirtualną, informacje o nazwie głównej usługi, *platformę Azure* dla wtyczki sieci i *calico* dla zasad sieciowych. Korzystanie *z narzędzia calico* jako zasad sieci umożliwia korzystanie z sieci Calico w pulach węzłów systemów Linux i Windows.

Jeśli planujesz dodawanie pul węzłów systemu Windows do klastra, uwzględnij parametry i , które spełniają wymagania dotyczące `windows-admin-username` `windows-admin-password` haseł systemu Windows [Server.][windows-server-password] Aby używać programu Calico z pulami węzłów systemu Windows, należy również zarejestrować `Microsoft.ContainerService/EnableAKSWindowsCalico` .

Zarejestruj `EnableAKSWindowsCalico` flagę funkcji za pomocą [polecenia az feature register,][az-feature-register] jak pokazano w poniższym przykładzie:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "EnableAKSWindowsCalico"
```

 Stan rejestracji możesz sprawdzić za pomocą [polecenia az feature list:][az-feature-list]

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/EnableAKSWindowsCalico')].{Name:name,State:properties.state}"
```

Gdy wszystko będzie gotowe, odśwież rejestrację dostawcy *zasobów Microsoft.ContainerService* za pomocą [polecenia az provider register:][az-provider-register]

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

> [!IMPORTANT]
> Obecnie zasady sieci Calico z węzłami systemu Windows są dostępne w nowych klastrach korzystających z platformy Kubernetes w wersji 1.20 lub nowszej z systemem Calico 3.17.2 i wymagają użycia Azure CNI sieciowych. Węzły systemu Windows w klastrach AKS z włączonym mechanizmem Calico mają również domyślnie włączoną funkcję [Direct Server Return (DSR).][dsr]
>
> W przypadku klastrów z tylko pulami węzłów systemu Linux z systemem Kubernetes 1.20 ze wcześniejszymi wersjami oprogramowania Calico wersja Calico zostanie automatycznie uaktualniona do wersji 3.17.2.

Zasady sieci calico z węzłami systemu Windows są obecnie dostępne w wersji zapoznawczej.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

Utwórz nazwę użytkownika do użycia jako poświadczenia administratora dla kontenerów systemu Windows Server w klastrze. Następujące polecenia monitują o nazwę użytkownika i ustawiają ją WINDOWS_USERNAME do użycia w późniejszym poleceniu (pamiętaj, że polecenia w tym artykule są wprowadzane do powłoki BASH).

```azurecli-interactive
echo "Please enter the username to use as administrator credentials for Windows Server containers on your cluster: " && read WINDOWS_USERNAME
```

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
    --windows-admin-username $WINDOWS_USERNAME \
    --vm-set-type VirtualMachineScaleSets \
    --kubernetes-version 1.20.2 \
    --network-plugin azure \
    --network-policy calico
```

Utworzenie klastra trwa kilka minut. Domyślnie klaster jest tworzony tylko przy użyciu puli węzłów systemu Linux. Jeśli chcesz używać pul węzłów systemu Windows, możesz je dodać. Na przykład:

```azurecli
az aks nodepool add \
    --resource-group $RESOURCE_GROUP_NAME \
    --cluster-name $CLUSTER_NAME \
    --os-type Windows \
    --name npwin \
    --node-count 1
```

Gdy klaster będzie gotowy, skonfiguruj połączenie z klastrem Kubernetes za pomocą polecenia `kubectl` [az aks get-credentials.][az-aks-get-credentials] To polecenie pobiera poświadczenia i konfiguruje interfejs wiersza polecenia kubernetes do ich używania:

```azurecli-interactive
az aks get-credentials --resource-group $RESOURCE_GROUP_NAME --name $CLUSTER_NAME
```

## <a name="deny-all-inbound-traffic-to-a-pod"></a>Odrzucanie całego ruchu przychodzącego do zasobnika

Przed zdefiniowaniem reguł zezwalania na określony ruch sieciowy należy najpierw utworzyć zasady sieci w celu blokowania całego ruchu. Te zasady umożliwiają rozpoczęcie tworzenia listy zezwalania tylko dla żądanego ruchu. Można również wyraźnie zobaczyć, że ruch jest porzucany po zastosowaniu zasad sieciowych.

Dla przykładowego środowiska aplikacji i reguł ruchu najpierw utwórzmy przestrzeń nazw o nazwie *development,* aby uruchomić przykładowe zasobniki:

```console
kubectl create namespace development
kubectl label namespace/development purpose=development
```

Utwórz przykładowy zasobnik back-end z uruchomionym NGINX. Ten zasobnik tego typu może służyć do symulowania przykładowej aplikacji internetowej na zaurze. Utwórz ten zasobnik w przestrzeni *nazw development* i otwórz port *80* w celu obsługi ruchu internetowego. Oznacz zasobnik etykietą *app=webapp,role=backend,* aby w następnej sekcji określić go jako docelowy dla zasad sieciowych:

```console
kubectl run backend --image=mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine --labels app=webapp,role=backend --namespace development --expose --port 80
```

Utwórz kolejny zasobnik i dołącz sesję terminalu, aby sprawdzić, czy można pomyślnie uzyskać dostęp do domyślnej strony internetowej NGINX:

```console
kubectl run --rm -it --image=mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11 network-policy --namespace development
```

W wierszu polecenia powłoki użyj polecenia , `wget` aby potwierdzić, że możesz uzyskać dostęp do domyślnej strony internetowej NGINX:

```console
wget -qO- http://backend
```

Następujące przykładowe dane wyjściowe pokazują, że zwracana jest domyślna strona internetowa NGINX:

```output
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Wyjdź z dołączonej sesji terminalowej. Zasobnik testowy jest automatycznie usuwany.

```console
exit
```

### <a name="create-and-apply-a-network-policy"></a>Tworzenie i stosowanie zasad sieciowych

Teraz, po potwierdzeniu, że możesz użyć podstawowej strony internetowej NGINX w przykładowym zasobniku, utwórz zasady sieciowe w celu blokowania całego ruchu. Utwórz plik o nazwie `backend-policy.yaml` i wklej następujący manifest YAML. Ten manifest używa *podSelector* do dołączania zasad do zasobników z etykietą *app:webapp,role:backend,* taką jak przykładowy zasobnik NGINX. Żadne reguły nie są zdefiniowane w ramach *ruchu* przychodzącego , więc cały ruch przychodzący do zasobnika jest zabroniony:

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

Przejdź do [https://shell.azure.com](https://shell.azure.com) strony , Azure Cloud Shell w przeglądarce.

Zastosuj zasady sieciowe za pomocą polecenia [kubectl apply][kubectl-apply] i określ nazwę manifestu YAML:

```console
kubectl apply -f backend-policy.yaml
```

### <a name="test-the-network-policy"></a>Testowanie zasad sieciowych

Sprawdźmy, czy możesz ponownie użyć strony internetowej NGINX w zasobniku back-end. Utwórz kolejny zasobnik testowy i dołącz sesję terminalu:

```console
kubectl run --rm -it --image=mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11 network-policy --namespace development
```

W wierszu polecenia powłoki użyj polecenia , aby sprawdzić, czy `wget` możesz uzyskać dostęp do domyślnej strony internetowej NGINX. Tym razem ustaw wartość limitu czasu na *2* sekundy. Zasady sieciowe blokują teraz cały ruch przychodzący, więc nie można załadować strony, jak pokazano w poniższym przykładzie:

```console
wget -qO- --timeout=2 http://backend
```

```output
wget: download timed out
```

Wyjdź z dołączonej sesji terminalu. Zasobnik testowy jest automatycznie usuwany.

```console
exit
```

## <a name="allow-inbound-traffic-based-on-a-pod-label"></a>Zezwalaj na ruch przychodzący na podstawie etykiety zasobnika

W poprzedniej sekcji zaplanowano zasobnik NGINX na zabłysku i utworzono zasady sieci w celu blokowania całego ruchu. Utwórzmy zasobnik frontony i zaktualizujmy zasady sieciowe, aby zezwolić na ruch z zasobników frontoni.

Zaktualizuj zasady sieciowe, aby zezwolić na ruch z zasobników za pomocą etykiet *app:webapp,role:frontend* i w dowolnej przestrzeni nazw. Edytuj poprzedni plik *backend-policy.yaml* i dodaj reguły ruchu przychodzących *matchLabels,* aby manifest wyglądał jak w poniższym przykładzie:

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
> Ta zasada sieciowa używa *elementu namespaceSelector* i *podSelector* dla reguły ruchu wychodzącego. Składnia YAML jest ważna, aby reguły ruchu wychodzącego można było addytywne. W tym przykładzie oba elementy muszą być zgodne, aby reguła wejscowa była stosowana. Wersje kubernetes wcześniejsze niż *1.12* mogą nie interpretować tych elementów poprawnie i ograniczać ruch sieciowy zgodnie z oczekiwaniami. Aby uzyskać więcej informacji na temat tego zachowania, zobacz Zachowanie selektorów do [i z .][policy-rules]

Zastosuj zaktualizowane zasady sieciowe za pomocą [polecenia kubectl apply][kubectl-apply] i określ nazwę manifestu YAML:

```console
kubectl apply -f backend-policy.yaml
```

Zaplanuj zasobnik oznaczony jako *app=webapp,role=frontend* i dołącz sesję terminalu:

```console
kubectl run --rm -it frontend --image=mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11 --labels app=webapp,role=frontend --namespace development
```

W wierszu polecenia powłoki użyj polecenia , aby sprawdzić, czy `wget` możesz uzyskać dostęp do domyślnej strony internetowej NGINX:

```console
wget -qO- http://backend
```

Ponieważ reguła ruchu wychodzącego zezwala na ruch z zasobnikami z aplikacją *etykiet: webapp,role: frontend*, ruch z zasobnika frontendu jest dozwolony. Następujące przykładowe dane wyjściowe pokazują zwracaną domyślną stronę internetową NGINX:

```output
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Wyjdź z dołączonej sesji terminalowej. Zasobnik zostanie automatycznie usunięty.

```console
exit
```

### <a name="test-a-pod-without-a-matching-label"></a>Testowanie zasobnika bez pasującej etykiety

Zasady sieci zezwalają na ruch z zasobników z etykietą *aplikacja: webapp,role: frontend*, ale powinny odrzucać cały inny ruch. Przetestujmy, aby sprawdzić, czy inny zasobnik bez tych etykiet może uzyskać dostęp do zasobnika NGINX na zabłocie. Utwórz kolejny zasobnik testowy i dołącz sesję terminalu:

```console
kubectl run --rm -it --image=mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11 network-policy --namespace development
```

W wierszu polecenia powłoki użyj polecenia , aby sprawdzić, czy `wget` możesz uzyskać dostęp do domyślnej strony internetowej NGINX. Zasady sieciowe blokują ruch przychodzący, więc nie można załadować strony, jak pokazano w poniższym przykładzie:

```console
wget -qO- --timeout=2 http://backend
```

```output
wget: download timed out
```

Wyjdź z dołączonej sesji terminalowej. Zasobnik testowy jest automatycznie usuwany.

```console
exit
```

## <a name="allow-traffic-only-from-within-a-defined-namespace"></a>Zezwalanie na ruch tylko ze zdefiniowanej przestrzeni nazw

W poprzednich przykładach utworzono zasady sieci, które odmawiają całego ruchu, a następnie zaktualizowano zasady, aby zezwolić na ruch z zasobników o określonej etykiecie. Inną często spotykaną potrzebą jest ograniczenie ruchu tylko do danej przestrzeni nazw. Jeśli poprzednie przykłady były  dotyczące ruchu w przestrzeni nazw projektowania, utwórz zasady sieciowe, które uniemożliwiają dotarcie ruchu z innej przestrzeni nazw, takiej jak produkcyjna *,* do zasobników.

Najpierw utwórz nową przestrzeń nazw, aby symulować produkcyjną przestrzeń nazw:

```console
kubectl create namespace production
kubectl label namespace/production purpose=production
```

Zaplanuj zasobnik testowy w *produkcyjnej* przestrzeni nazw, która jest oznaczona jako *app=webapp,role=frontend.* Dołączanie sesji terminalowej:

```console
kubectl run --rm -it frontend --image=mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11 --labels app=webapp,role=frontend --namespace production
```

W wierszu polecenia powłoki użyj polecenia , `wget` aby potwierdzić, że możesz uzyskać dostęp do domyślnej strony internetowej NGINX:

```console
wget -qO- http://backend.development
```

Ponieważ etykiety zasobnika są zgodne z tym, co jest obecnie dozwolone w zasadach sieciowych, ruch jest dozwolony. Zasady sieci nie rozpatrzą przestrzeni nazw, a tylko etykiet zasobników. Następujące przykładowe dane wyjściowe pokazują zwracaną domyślną stronę internetową NGINX:

```output
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Wyjdź z dołączonej sesji terminalu. Zasobnik testowy jest automatycznie usuwany.

```console
exit
```

### <a name="update-the-network-policy"></a>Aktualizowanie zasad sieciowych

Zaktualizujmy sekcję *namespaceSelector* reguły ruchu wychodzącego, aby zezwalać tylko na ruch z przestrzeni *nazw* development. Edytuj plik *manifestu backend-policy.yaml,* jak pokazano w poniższym przykładzie:

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

W bardziej złożonych przykładach można zdefiniować wiele reguł ruchu wychodzącego, takich jak *namespaceSelector,* a następnie *podSelector*.

Zastosuj zaktualizowane zasady sieciowe za pomocą [polecenia kubectl apply][kubectl-apply] i określ nazwę manifestu YAML:

```console
kubectl apply -f backend-policy.yaml
```

### <a name="test-the-updated-network-policy"></a>Testowanie zaktualizowanych zasad sieciowych

Zaplanuj kolejny zasobnik w przestrzeni *nazw produkcji* i dołącz sesję terminalu:

```console
kubectl run --rm -it frontend --image=mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11 --labels app=webapp,role=frontend --namespace production
```

W wierszu polecenia powłoki użyj polecenia , aby zobaczyć, `wget` że zasady sieciowe teraz nie zezwalają na ruch:

```console
wget -qO- --timeout=2 http://backend.development
```

```output
wget: download timed out
```

Wyjdź z zasobnika testowego:

```console
exit
```

W przypadku odmowy ruchu z *produkcyjnej przestrzeni* nazw  zaplanuj zasobnik testowy z powrotem w przestrzeni nazw projektowania i dołącz sesję terminalu:

```console
kubectl run --rm -it frontend --image=mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11 --labels app=webapp,role=frontend --namespace development
```

W wierszu polecenia powłoki użyj polecenia , aby sprawdzić, `wget` czy zasady sieci zezwalają na ruch:

```console
wget -qO- http://backend
```

Ruch jest dozwolony, ponieważ zasobnik jest zaplanowany w przestrzeni nazw, która odpowiada dozwolonym zasadom sieciowym. Następujące przykładowe dane wyjściowe pokazują zwracaną domyślną stronę internetową NGINX:

```output
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Wyjdź z dołączonej sesji terminalu. Zasobnik testowy jest automatycznie usuwany.

```console
exit
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

W tym artykule utworzono dwie przestrzenie nazw i zastosowano zasady sieciowe. Aby wyczyścić te zasoby, użyj [polecenia kubectl delete][kubectl-delete] i określ nazwy zasobów:

```console
kubectl delete namespace production
kubectl delete namespace development
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat zasobów sieciowych, zobacz [Pojęcia dotyczące sieci dla aplikacji w Azure Kubernetes Service (AKS).][concepts-network]

Aby dowiedzieć się więcej na temat zasad, zobacz [Kubernetes network policies (Zasady sieciowe usługi Kubernetes).][kubernetes-network-policies]

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
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[concepts-network]: concepts-network.md
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[windows-server-password]: /windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements#reference
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[dsr]: ../load-balancer/load-balancer-multivip-overview.md#rule-type-2-backend-port-reuse-by-using-floating-ip