---
title: Dostosowywanie tras zdefiniowanych przez użytkownika (UDR) w usłudze Azure Kubernetes Service (AKS)
description: Informacje o definiowaniu niestandardowej trasy ruchu wychodzącego w usłudze Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 03/16/2020
ms.openlocfilehash: e7dbde4095fb635180bb1ba663734f8dbfd602f7
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/03/2020
ms.locfileid: "82733502"
---
# <a name="customize-cluster-egress-with-a-user-defined-route-preview"></a>Dostosowywanie ruchu wychodzącego klastra przy użyciu trasy zdefiniowanej przez użytkownika (wersja zapoznawcza)

Ruch wychodzący z klastra AKS można dostosować do określonych scenariuszy. Domyślnie AKS będzie obsługiwać standardową jednostkę SKU, Load Balancer być skonfigurowana i używana do wychodzącego. Jednak konfiguracja domyślna może nie spełniać wymagań wszystkich scenariuszy, jeśli publiczne adresy IP są niedozwolone lub dodatkowe przeskoki są wymagane dla ruchu wychodzącego.

W tym artykule opisano sposób dostosowywania trasy ruchu wychodzącego klastra w celu zapewnienia obsługi niestandardowych scenariuszy sieciowych, takich jak te, które uniemożliwiają publiczne adresy IP i wymagają, aby klaster znajduje się za sieciowym urządzeniem wirtualnym (urządzenie WUS).

> [!IMPORTANT]
> Funkcje w wersji zapoznawczej AKS są samoobsługowe i są oferowane na zasadzie zgody. Wersje zapoznawcze są udostępniane *w postaci* , w jakiej są *dostępne* i są wyłączone z umowy dotyczącej poziomu usług (SLA) i ograniczonej rękojmi. Wersje zapoznawcze AKS są częściowo objęte wsparciem klienta w oparciu o *najlepszą* pracę. W związku z tym funkcje te nie są przeznaczone do użytku produkcyjnego. Aby uzyskać więcej informacji, zobacz następujące artykuły pomocy technicznej:
>
> * [Zasady pomocy technicznej AKS](support-policies.md)
> * [Pomoc techniczna platformy Azure — często zadawane pytania](faq.md)

## <a name="prerequisites"></a>Wymagania wstępne
* Interfejs wiersza polecenia platformy Azure w wersji 2.0.81 lub nowszej
* Rozszerzenie interfejsu wiersza polecenia platformy Azure w wersji zapoznawczej 0.4.28 lub nowszej
* Wersja interfejsu API `2020-01-01` lub nowsza

## <a name="install-the-latest-azure-cli-aks-preview-extension"></a>Zainstaluj najnowsze rozszerzenie AKS w wersji zapoznawczej interfejsu wiersza polecenia platformy Azure
Aby można było ustawić typ wychodzący klastra, wymagany jest interfejs wiersza polecenia platformy Azure AKS w wersji zapoznawczej 0.4.18 lub nowszej. Zainstaluj rozszerzenie AKS interfejsu wiersza polecenia platformy Azure w wersji zapoznawczej za pomocą poleceń AZ Extension Add, a następnie sprawdź, czy są dostępne aktualizacje przy użyciu następującego polecenia AZ Extension Update:

```azure-cli
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

## <a name="limitations"></a>Ograniczenia
* W trakcie okresu `outboundType` zapoznawczego można zdefiniować tylko w czasie tworzenia klastra i nie można go później zaktualizować.
* W trakcie okresu `outboundType` zapoznawczego klastry AKS powinny korzystać z usługi Azure CNI. Korzystającą wtyczki kubenet można skonfigurować, użycie wymaga ręcznego skojarzenia tabeli tras z podsiecią AKS.
* Ustawienie `outboundType` wymaga `vm-set-type` klastrów AKS z `VirtualMachineScaleSets` i `load-balancer-sku` z. `Standard`
* Ustawienie `outboundType` wartości `UDR` wymaga trasy zdefiniowanej przez użytkownika z prawidłową łącznością wychodzącą dla klastra.
* Ustawienie `outboundType` wartości `UDR` powoduje, że adres IP źródła danych przychodzących kierowany do modułu równoważenia obciążenia może być **niezgodny** z wychodzącym docelowym ruchem wyjściowym klastra.

## <a name="overview-of-outbound-types-in-aks"></a>Przegląd typów wychodzących w AKS

Klaster AKS można dostosować przy użyciu unikatowego `outboundType` typu modułu równoważenia obciążenia lub routingu zdefiniowanego przez użytkownika.

> [!IMPORTANT]
> Typ wychodzący ma wpływ tylko na ruch wyjściowy klastra. Aby uzyskać więcej informacji, zobacz [Konfigurowanie kontrolerów](ingress-basic.md) przychodzących.

### <a name="outbound-type-of-loadbalancer"></a>Typ wychodzącego modułu równoważenia obciążenia

Jeśli `loadBalancer` jest ustawiona, AKS automatycznie wykonuje następujące czynności konfiguracyjne. Moduł równoważenia obciążenia jest używany do ruchu wychodzącego przez AKS przypisany publiczny adres IP. Typ wychodzący `loadBalancer` obsługuje usługi Kubernetes Services typu `loadBalancer`, które oczekują wyjście z modułu równoważenia obciążenia utworzonego przez dostawcę zasobów AKS.

Następująca konfiguracja jest wykonywana przez AKS.
   * Publiczny adres IP jest inicjowany dla ruchu wychodzącego klastra.
   * Publiczny adres IP jest przypisywany do zasobu modułu równoważenia obciążenia.
   * Pule zaplecza dla modułu równoważenia obciążenia są skonfigurowane dla węzłów agenta w klastrze.

Poniżej znajduje się topologia sieci wdrożona domyślnie w klastrach AKS, która `outboundType` korzysta `loadBalancer`z programu.

![niepowiązanytype — lb](media/egress-outboundtype/outboundtype-lb.png)

### <a name="outbound-type-of-userdefinedrouting"></a>Typ wychodzący userDefinedRouting

> [!NOTE]
> Użycie typu wychodzącego jest zaawansowanym scenariuszem sieci i wymaga odpowiedniej konfiguracji sieci.

Jeśli `userDefinedRouting` jest ustawiona, AKS nie skonfiguruje automatycznie ścieżek ruchu wychodzącego. **Użytkownik**powinien wykonać następujące czynności.

Klaster AKS musi zostać wdrożony w istniejącej sieci wirtualnej z skonfigurowaną podsiecią. W przypadku korzystania ze standardowej architektury modułu równoważenia obciążenia należy ustanowić jawne dane wyjściowe. Wymaga to wysyłania żądań wychodzących do urządzenia, takiego jak zapora, Brama, Premium lub aby zezwolić na ruch wychodzący przez publiczny adres IP przypisany do standardowej usługi równoważenia obciążenia lub danego węzła.

Dostawca zasobów AKS będzie wdrażać usługę równoważenia obciążenia w warstwie Standardowa. Moduł równoważenia obciążenia nie jest skonfigurowany z żadną regułą i nie [ponosi opłaty do momentu, gdy reguła zostanie umieszczona](https://azure.microsoft.com/pricing/details/load-balancer/). AKS **nie** będzie automatycznie inicjować publicznego adresu IP dla frontonu modułu równoważenia obciążenia. Usługa AKS **nie** będzie automatycznie konfigurować puli zaplecza modułu równoważenia obciążenia.

## <a name="deploy-a-cluster-with-outbound-type-of-udr-and-azure-firewall"></a>Wdrażanie klastra z typem wychodzącym UDR i zaporą platformy Azure

Aby zilustrować aplikację klastra z typem wychodzącym przy użyciu trasy zdefiniowanej przez użytkownika, klaster można skonfigurować w sieci wirtualnej za pomocą komunikacji równorzędnej z zaporą platformy Azure.

![Topologia blokady](media/egress-outboundtype/outboundtype-udr.png)

* Ruch przychodzący jest zmuszony do przepływu przez filtry zapory
   * Izolowana podsieć przechowuje wewnętrzny moduł równoważenia obciążenia do routingu w węzłach agenta
   * Węzły agentów są izolowane w dedykowanej podsieci
* Żądania wychodzące rozpoczynają się od węzłów agenta do wewnętrznego adresu IP zapory platformy Azure przy użyciu trasy zdefiniowanej przez użytkownika
   * Żądania z węzłów agenta AKS są zgodne z UDRem umieszczonym w podsieci, do której został wdrożony klaster AKS.
   * Zapora platformy Azure egresses z sieci wirtualnej z publicznego frontonu IP
   * Dostęp do płaszczyzny kontroli AKS jest chroniony przez sieciowej grupy zabezpieczeń, który włączył adres IP frontonu zapory
   * Dostęp do publicznej sieci Internet lub innych usług platformy Azure przepływy do i z adresu IP frontonu zapory

### <a name="set-configuration-via-environment-variables"></a>Ustawianie konfiguracji za pomocą zmiennych środowiskowych

Zdefiniuj zestaw zmiennych środowiskowych, które mają być używane podczas tworzenia zasobów.

```bash
PREFIX="contosofin"
RG="${PREFIX}-rg"
LOC="eastus"
NAME="${PREFIX}outboundudr"
AKS_NAME="${PREFIX}aks"
VNET_NAME="${PREFIX}vnet"
AKSSUBNET_NAME="${PREFIX}akssubnet"
SVCSUBNET_NAME="${PREFIX}svcsubnet"
# DO NOT CHANGE FWSUBNET_NAME - This is currently a requirement for Azure Firewall.
FWSUBNET_NAME="AzureFirewallSubnet"
FWNAME="${PREFIX}fw"
FWPUBLICIP_NAME="${PREFIX}fwpublicip"
FWIPCONFIG_NAME="${PREFIX}fwconfig"
FWROUTE_TABLE_NAME="${PREFIX}fwrt"
FWROUTE_NAME="${PREFIX}fwrn"
FWROUTE_NAME_INTERNET="${PREFIX}fwinternet"
DEVSUBNET_NAME="${PREFIX}dev"
```

Następnie ustaw identyfikatory subskrypcji.

```azure-cli
# Get ARM Access Token and Subscription ID - This will be used for AuthN later.

ACCESS_TOKEN=$(az account get-access-token -o tsv --query 'accessToken')

# NOTE: Update Subscription Name
# Set Default Azure Subscription to be Used via Subscription ID

az account set -s <SUBSCRIPTION_ID_GOES_HERE>

# NOTE: Update Subscription Name for setting SUBID

SUBID=$(az account show -s '<SUBSCRIPTION_NAME_GOES_HERE>' -o tsv --query 'id')
```

## <a name="create-a-virtual-network-with-multiple-subnets"></a>Tworzenie sieci wirtualnej z wieloma podsieciami

Zainicjuj obsługę sieci wirtualnej z trzema oddzielnymi podsieciami, jedną dla klastra, jedną dla zapory i jedną dla usługi transferu danych przychodzących.

![Pusta topologia sieci](media/egress-outboundtype/empty-network.png)

Utwórz grupę zasobów, w której mają być przechowywane wszystkie zasoby.

```azure-cli
# Create Resource Group

az group create --name $RG --location $LOC
```

Utwórz dwie sieci wirtualne do hostowania klastra AKS i zapory platformy Azure. Każda z nich będzie miała własną podsieć. Zacznijmy od sieci AKS.

```
# Dedicated virtual network with AKS subnet

az network vnet create \
    --resource-group $RG \
    --name $VNET_NAME \
    --address-prefixes 100.64.0.0/16 \
    --subnet-name $AKSSUBNET_NAME \
    --subnet-prefix 100.64.1.0/24

# Dedicated subnet for K8s services

az network vnet subnet create \
    --resource-group $RG \
    --vnet-name $VNET_NAME \
    --name $SVCSUBNET_NAME \
    --address-prefix 100.64.2.0/24

# Dedicated subnet for Azure Firewall (Firewall name cannot be changed)

az network vnet subnet create \
    --resource-group $RG \
    --vnet-name $VNET_NAME \
    --name $FWSUBNET_NAME \
    --address-prefix 100.64.3.0/24
```

## <a name="create-and-setup-an-azure-firewall-with-a-udr"></a>Tworzenie i Konfigurowanie zapory platformy Azure za pomocą UDR

Reguły ruchu przychodzącego i wychodzącego zapory platformy Azure muszą być skonfigurowane. Głównym celem zapory jest umożliwienie organizacjom konfigurowania szczegółowych zasad ruchu przychodzącego i wychodzącego w klastrze AKS.

![Zapora i UDR](media/egress-outboundtype/firewall-udr.png)

Utwórz zasób publicznego adresu IP jednostki SKU, który będzie używany jako adres frontonu zapory platformy Azure.

```azure-cli
az network public-ip create -g $RG -n $FWPUBLICIP_NAME -l $LOC --sku "Standard"
```

Zarejestruj interfejs wiersza polecenia w wersji zapoznawczej, aby utworzyć zaporę platformy Azure.
```azure-cli
# Install Azure Firewall preview CLI extension

az extension add --name azure-firewall

# Deploy Azure Firewall

az network firewall create -g $RG -n $FWNAME -l $LOC
```

Utworzony wcześniej adres IP można teraz przypisać do frontonu zapory.
> [!NOTE]
> Konfiguracja publicznego adresu IP w zaporze platformy Azure może potrwać kilka minut.
> 
> Jeśli błędy są często odbierane przy użyciu poniższego polecenia, należy usunąć istniejącą zaporę i publiczny adres IP oraz udostępnić publiczny adres IP i zaporę platformy Azure w tym samym czasie.

```azure-cli
# Configure Firewall IP Config

az network firewall ip-config create -g $RG -f $FWNAME -n $FWIPCONFIG_NAME --public-ip-address $FWPUBLICIP_NAME --vnet-name $VNET_NAME
```

Gdy poprzednie polecenie zakończyło się pomyślnie, Zapisz adres IP frontonu zapory, aby skonfigurować go później.

```bash
# Capture Firewall IP Address for Later Use

FWPUBLIC_IP=$(az network public-ip show -g $RG -n $FWPUBLICIP_NAME --query "ipAddress" -o tsv)
FWPRIVATE_IP=$(az network firewall show -g $RG -n $FWNAME --query "ipConfigurations[0].privateIpAddress" -o tsv)
```

### <a name="create-a-udr-with-a-hop-to-azure-firewall"></a>Tworzenie UDR z przeskokiem do zapory platformy Azure

Platforma Azure automatycznie kieruje ruchem między podsieciami platformy Azure, sieciami wirtualnymi i sieciami lokalnymi. Jeśli chcesz zmienić domyślny Routing systemu Azure, możesz to zrobić, tworząc tabelę tras.

Utwórz pustą tabelę tras, która ma zostać skojarzona z daną podsiecią. W tabeli tras zostanie zdefiniowany następny przeskok, który został utworzony powyżej przez zaporę platformy Azure. Każda podsieć może mieć skojarzoną ze sobą żadną lub jedną tabelę tras.

```azure-cli
# Create UDR and add a route for Azure Firewall

az network route-table create -g $RG --name $FWROUTE_TABLE_NAME
az network route-table route create -g $RG --name $FWROUTE_NAME --route-table-name $FWROUTE_TABLE_NAME --address-prefix 0.0.0.0/0 --next-hop-type VirtualAppliance --next-hop-ip-address $FWPRIVATE_IP --subscription $SUBID
az network route-table route create -g $RG --name $FWROUTE_NAME_INTERNET --route-table-name $FWROUTE_TABLE_NAME --address-prefix $FWPUBLIC_IP/32 --next-hop-type Internet
```

Zapoznaj się z [dokumentacją dotyczącą trasy sieci wirtualnej](../virtual-network/virtual-networks-udr-overview.md#user-defined) , aby dowiedzieć się, jak zastąpić domyślne trasy systemu platformy Azure lub dodać dodatkowe trasy do tabeli tras podsieci.

## <a name="adding-network-firewall-rules"></a>Dodawanie reguł zapory sieciowej

> [!WARNING]
> Poniżej przedstawiono przykład dodawania reguły zapory. Wszystkie punkty końcowe wyjściowe zdefiniowane w [wymaganych punktach końcowych ruchu](egress.md) wychodzącego muszą być włączone przez reguły zapory aplikacji dla klastrów AKS do działania. Bez włączonych punktów końcowych klaster nie może działać.

Poniżej znajduje się przykład reguły sieci i aplikacji. Dodawana jest reguła sieci, która zezwala na dowolny protokół, adres źródłowy, adres docelowy i port docelowy. Dodajemy również regułę aplikacji dla **niektórych** punktów końcowych wymaganych przez AKS.

W scenariuszu produkcyjnym należy włączyć tylko dostęp do wymaganych punktów końcowych dla aplikacji oraz tych zdefiniowanych w [AKS](egress.md).

```
# Add Network FW Rules

az network firewall network-rule create -g $RG -f $FWNAME --collection-name 'aksfwnr' -n 'netrules' --protocols 'Any' --source-addresses '*' --destination-addresses '*' --destination-ports '*' --action allow --priority 100

# Add Application FW Rules
# IMPORTANT: Add AKS required egress endpoints

az network firewall application-rule create -g $RG -f $FWNAME \
    --collection-name 'AKS_Global_Required' \
    --action allow \
    --priority 100 \
    -n 'required' \
    --source-addresses '*' \
    --protocols 'http=80' 'https=443' \
    --target-fqdns \
        'aksrepos.azurecr.io' \
        '*blob.core.windows.net' \
        'mcr.microsoft.com' \
        '*cdn.mscr.io' \
        '*.data.mcr.microsoft.com' \
        'management.azure.com' \
        'login.microsoftonline.com' \
        'ntp.ubuntu.com' \
        'packages.microsoft.com' \
        'acs-mirror.azureedge.net'
```

Zobacz [dokumentację zapory platformy Azure](https://docs.microsoft.com/azure/firewall/overview) , aby dowiedzieć się więcej na temat usługi Zapora systemu Azure.

## <a name="associate-the-route-table-to-aks"></a>Skojarz tabelę tras z AKS

Aby można było skojarzyć klaster z zaporą, dedykowana podsieć klastra musi odwoływać się do utworzonej powyżej tabeli tras. Skojarzenie można wykonać, wydając polecenie do sieci wirtualnej, w której klaster i Zapora mają aktualizować tabelę tras w podsieci klastra.

```azure-cli
# Associate route table with next hop to Firewall to the AKS subnet

az network vnet subnet update -g $RG --vnet-name $VNET_NAME --name $AKSSUBNET_NAME --route-table $FWROUTE_TABLE_NAME
```

## <a name="deploy-aks-with-outbound-type-of-udr-to-the-existing-network"></a>Wdróż AKS z typem wychodzącym UDR do istniejącej sieci

Teraz klaster AKS można wdrożyć w istniejącej konfiguracji sieci wirtualnej. Aby można było ustawić typ ruchu wychodzącego klastra do routingu zdefiniowanego przez użytkownika, należy podać istniejącą podsieć do AKS.

![AKS — Wdróż](media/egress-outboundtype/outboundtype-udr.png)

### <a name="create-a-service-principal-with-access-to-provision-inside-the-existing-virtual-network"></a>Tworzenie jednostki usługi z dostępem do inicjowania obsługi administracyjnej w istniejącej sieci wirtualnej

Nazwa główna usługi jest używana przez AKS do tworzenia zasobów klastra. Nazwa główna usługi przenoszona podczas tworzenia czasu służy do tworzenia podstawowych zasobów AKS, takich jak maszyny wirtualne, magazyn i moduły równoważenia obciążenia używane przez AKS. W przypadku przyznania zbyt małej liczby uprawnień nie będzie możliwe Inicjowanie obsługi administracyjnej klastra AKS.

```azure-cli
# Create SP and Assign Permission to Virtual Network

az ad sp create-for-rbac -n "${PREFIX}sp" --skip-assignment
```

Teraz Zastąp `APPID` poniższe `PASSWORD` i poniżej z identyfikatorem jednostki usługi i hasłem głównym usługi generowanym automatycznie przez poprzednie dane wyjściowe polecenia. Odwołujemy się do identyfikatora zasobu sieci wirtualnej, aby przyznać uprawnienia do nazwy głównej usługi, dzięki czemu AKS może wdrożyć w niej zasoby.

```azure-cli
APPID="<SERVICE_PRINCIPAL_APPID_GOES_HERE>"
PASSWORD="<SERVICEPRINCIPAL_PASSWORD_GOES_HERE>"
VNETID=$(az network vnet show -g $RG --name $VNET_NAME --query id -o tsv)

# Assign SP Permission to VNET

az role assignment create --assignee $APPID --scope $VNETID --role Contributor

# View Role Assignment
az role assignment list --assignee $APPID --all -o table
```

### <a name="deploy-aks"></a>Wdróż AKS

Na koniec klaster AKS można wdrożyć w istniejącej podsieci, która jest dedykowana dla klastra. Podsieć docelowa do wdrożenia jest zdefiniowana za pomocą zmiennej środowiskowej `$SUBNETID`. Nie zdefiniowano `$SUBNETID` zmiennej w poprzednich krokach. Aby ustawić wartość identyfikatora podsieci, można użyć następującego polecenia:

```azurecli
SUBNETID="/subscriptions/$SUBID/resourceGroups/$RG/providers/Microsoft.Network/virtualNetworks/$VNET_NAME/subnets/$AKSSUBNET_NAME"
```

Zdefiniujemy typ wychodzący, który będzie podążał za UDR, który istnieje w podsieci, umożliwiając AKS w celu pominięcia instalacji i inicjowania obsługi protokołu IP dla modułu równoważenia obciążenia, który może być teraz wyłącznie wewnętrzny.

Można dodać funkcję AKS dla [dozwolonych zakresów adresów IP serwera interfejsu API](api-server-authorized-ip-ranges.md) , aby ograniczyć dostęp serwera API tylko do publicznego punktu końcowego zapory. Funkcja zakresów autoryzowanych adresów IP jest określana na diagramie jako sieciowej grupy zabezpieczeń, który musi zostać przesłany w celu uzyskania dostępu do płaszczyzny kontroli. W przypadku włączenia funkcji autoryzowanego zakresu adresów IP w celu ograniczenia dostępu do serwera interfejsu API narzędzia deweloperskie muszą używać serwera przesiadkowego z sieci wirtualnej zapory lub należy dodać wszystkie punkty końcowe dewelopera do autoryzowanego zakresu adresów IP.

> [!TIP]
> Do wdrożenia klastra można dodać dodatkowe funkcje, takie jak (klaster prywatny) []. W przypadku korzystania z autoryzowanych zakresów adresów IP serwera przesiadkowego w sieci klastra musi być wymagana w celu uzyskania dostępu do serwera interfejsu API.

```azure-cli
az aks create -g $RG -n $AKS_NAME -l $LOC \
  --node-count 3 \
  --network-plugin azure --generate-ssh-keys \
  --service-cidr 192.168.0.0/16 \
  --dns-service-ip 192.168.0.10 \
  --docker-bridge-address 172.22.0.1/29 \
  --vnet-subnet-id $SUBNETID \
  --service-principal $APPID \
  --client-secret $PASSWORD \
  --load-balancer-sku standard \
  --outbound-type userDefinedRouting \
  --api-server-authorized-ip-ranges $FWPUBLIC_IP
  ```

### <a name="enable-developer-access-to-the-api-server"></a>Włącz dostęp dewelopera do serwera interfejsu API

Ze względu na konfigurację zakresów autoryzowanych adresów IP dla klastra należy dodać adresy IP narzędzi deweloperskich do listy klastrów AKS zatwierdzonych zakresów adresów IP w celu uzyskania dostępu do serwera interfejsu API. Innym rozwiązaniem jest skonfigurowanie serwera przesiadkowego z wymaganymi narzędziami w ramach oddzielnej podsieci w sieci wirtualnej zapory.

Dodaj inny adres IP do zatwierdzonych zakresów przy użyciu następującego polecenia

```bash
# Retrieve your IP address
CURRENT_IP=$(dig @resolver1.opendns.com ANY myip.opendns.com +short)

# Add to AKS approved list
az aks update -g $RG -n $AKS_NAME --api-server-authorized-ip-ranges $CURRENT_IP/32

```

 Użyj polecenia [AZ AKS Get-Credentials][az-aks-get-credentials] , aby skonfigurować `kubectl` program do nawiązywania połączenia z nowo utworzonym klastrem Kubernetes. 

 ```azure-cli
 az aks get-credentials -g $RG -n $AKS_NAME
 ```

### <a name="setup-the-internal-load-balancer"></a>Konfigurowanie wewnętrznego modułu równoważenia obciążenia

Usługa AKS wdrożyła moduł równoważenia obciążenia z klastrem, który może być skonfigurowany jako [wewnętrzny moduł równoważenia obciążenia](internal-lb.md).

Aby utworzyć wewnętrzny moduł równoważenia obciążenia, należy utworzyć manifest usługi o nazwie Internal-LB. YAML z typem usługi równoważenia obciążenia i funkcją Azure-load-module — wewnętrzna Adnotacja, jak pokazano w następującym przykładzie:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: internal-app
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
    service.beta.kubernetes.io/azure-load-balancer-internal-subnet: "contosofinsvcsubnet"
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: internal-app
```

Wdróż wewnętrzny moduł równoważenia obciążenia przy użyciu polecenia kubectl Zastosuj i określ nazwę manifestu YAML:

```bash
kubectl apply -f internal-lb.yaml
```

## <a name="deploy-a-kubernetes-service"></a>Wdrażanie usługi Kubernetes

Ponieważ typ wychodzący klastra jest ustawiony jako UDR, skojarzenie węzłów agenta jako puli zaplecza dla modułu równoważenia obciążenia nie jest wykonywane automatycznie przez AKS podczas tworzenia klastra. Jednak skojarzenie puli zaplecza jest obsługiwane przez dostawcę chmury platformy Azure Kubernetes podczas wdrażania usługi Kubernetes.

Wdróż aplikację do głosowania na platformie Azure, kopiując YAML poniżej do pliku o nazwie `example.yaml`.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: azure-vote-back
spec:
  replicas: 1
  selector:
    matchLabels:
      app: azure-vote-back
  template:
    metadata:
      labels:
        app: azure-vote-back
    spec:
      nodeSelector:
        "beta.kubernetes.io/os": linux
      containers:
      - name: azure-vote-back
        image: redis
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 250m
            memory: 256Mi
        ports:
        - containerPort: 6379
          name: redis
---
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-back
spec:
  ports:
  - port: 6379
  selector:
    app: azure-vote-back
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: azure-vote-front
spec:
  replicas: 1
  selector:
    matchLabels:
      app: azure-vote-front
  template:
    metadata:
      labels:
        app: azure-vote-front
    spec:
      nodeSelector:
        "beta.kubernetes.io/os": linux
      containers:
      - name: azure-vote-front
        image: microsoft/azure-vote-front:v1
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 250m
            memory: 256Mi
        ports:
        - containerPort: 80
        env:
        - name: REDIS
          value: "azure-vote-back"
---
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
    service.beta.kubernetes.io/azure-load-balancer-internal-subnet: "contosofinsvcsubnet"
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-vote-front
```

Wdróż usługę, uruchamiając:

```bash
kubectl apply -f example.yaml
```

## <a name="add-a-dnat-rule-to-azure-firewall"></a>Dodawanie reguły DNAT do zapory platformy Azure

Aby można było skonfigurować łączność z ruchem przychodzącym, reguła DNAT musi być zapisywana w zaporze platformy Azure. Aby przetestować łączność z naszym klastrem, reguła jest definiowana dla publicznego adresu IP frontonu w celu kierowania do wewnętrznego adresu IP uwidocznionego przez usługę wewnętrzną.

Adres docelowy można dostosować, ponieważ jest to port w zaporze, do którego ma być uzyskiwany dostęp. Przetłumaczony adres musi być adresem IP wewnętrznego modułu równoważenia obciążenia. Przetłumaczony port musi być widocznym portem dla usługi Kubernetes.

Należy określić wewnętrzny adres IP przypisany do modułu równoważenia obciążenia utworzonego przez usługę Kubernetes. Pobierz adres, uruchamiając:

```bash
kubectl get services
```

Wymagany adres IP zostanie wyświetlony w kolumnie zewnętrzny adres IP, podobnie do poniższego.

```bash
NAME               TYPE           CLUSTER-IP       EXTERNAL-IP   PORT(S)        AGE
azure-vote-back    ClusterIP      192.168.92.209   <none>        6379/TCP       23m
azure-vote-front   LoadBalancer   192.168.19.183   100.64.2.5    80:32106/TCP   23m
kubernetes         ClusterIP      192.168.0.1      <none>        443/TCP        4d3h
```

```azure-cli
az network firewall nat-rule create --collection-name exampleset --destination-addresses $FWPUBLIC_IP --destination-ports 80 --firewall-name $FWNAME --name inboundrule --protocols Any --resource-group $RG --source-addresses '*' --translated-port 80 --action Dnat --priority 100 --translated-address <INSERT IP OF K8s SERVICE>
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

> [!NOTE]
> W przypadku usuwania usługi wewnętrznej Kubernetes, jeśli wewnętrzny moduł równoważenia obciążenia nie jest już używany przez żadną usługę, dostawca chmury platformy Azure usunie wewnętrzny moduł równoważenia obciążenia. W następnym wdrożeniu usługi moduł równoważenia obciążenia zostanie wdrożony, jeśli nie można znaleźć żadnej z żądanych konfiguracji.

Aby wyczyścić zasoby platformy Azure, Usuń grupę zasobów AKS.

```azure-cli
az group delete -g $RG
```

## <a name="validate-connectivity"></a>Sprawdź poprawność łączności

Przejdź do adresu IP frontonu zapory platformy Azure w przeglądarce, aby sprawdzić poprawność łączności.

Powinien zostać wyświetlony obraz aplikacji do głosowania platformy Azure.

## <a name="next-steps"></a>Następne kroki

Zobacz [Omówienie usługi Azure Network UDR](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview).

Zobacz [jak utworzyć, zmienić lub usunąć tabelę tras](https://docs.microsoft.com/azure/virtual-network/manage-route-table).

<!-- LINKS - internal -->
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
