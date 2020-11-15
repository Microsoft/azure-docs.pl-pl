---
title: Konfigurowanie dostępu opartego na sieci wirtualnej dla konta usługi Azure Cosmos
description: W tym dokumencie opisano kroki wymagane do skonfigurowania punktu końcowego usługi sieci wirtualnej dla Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.date: 10/13/2020
ms.author: mjbrown
ms.custom: devx-track-azurecli
ms.openlocfilehash: 1d63d21f4c49e3c7aef035208477ac9fc79f2e51
ms.sourcegitcommit: 295db318df10f20ae4aa71b5b03f7fb6cba15fc3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/15/2020
ms.locfileid: "94637187"
---
# <a name="configure-access-to-azure-cosmos-db-from-virtual-networks-vnet"></a>Konfigurowanie dostępu do Azure Cosmos DB z sieci wirtualnych (VNet)
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Konto usługi Azure Cosmos można skonfigurować tak, aby zezwalało na dostęp tylko z określonej podsieci sieci wirtualnej (VNet). Przez włączenie [punktu końcowego usługi](../virtual-network/virtual-network-service-endpoints-overview.md) w celu uzyskania dostępu do Azure Cosmos DB w podsieci w sieci wirtualnej, ruch z tej podsieci jest wysyłany do Azure Cosmos dB przy użyciu tożsamości podsieci i Virtual Network. Po włączeniu punktu końcowego usługi Azure Cosmos DB można ograniczyć dostęp do podsieci, dodając ją do konta usługi Azure Cosmos.

Domyślnie konto usługi Azure Cosmos jest dostępne z dowolnego źródła, jeśli do żądania dołączono prawidłowy token autoryzacji. Po dodaniu co najmniej jednej podsieci w programie sieci wirtualnych tylko żądania pochodzące z tych podsieci będą mieć prawidłową odpowiedź. Żądania pochodzące z dowolnego innego źródła otrzymają odpowiedź 403 (dostęp zabroniony). 

Konta usługi Azure Cosmos DB można skonfigurować tak, aby zezwalać na dostęp tylko z określonej podsieci sieci wirtualnej platformy Azure. Aby ograniczyć dostęp do konta Azure Cosmos DB z połączeniami z podsieci w sieci wirtualnej:

1. Włącz podsieć do wysyłania do Azure Cosmos DB podsieci i tożsamości sieci wirtualnej. Można to osiągnąć przez włączenie punktu końcowego usługi dla Azure Cosmos DB w określonej podsieci.

1. Dodaj regułę na koncie Azure Cosmos DB, aby określić podsieć jako źródło, z którego można uzyskać dostęp do konta.

> [!NOTE]
> Gdy punkt końcowy usługi dla konta Azure Cosmos DB jest włączony w podsieci, źródło ruchu, który dociera do Azure Cosmos DB przełącza z publicznego adresu IP do sieci wirtualnej i podsieci. Przełączenie ruchu ma zastosowanie do każdego konta Azure Cosmos DB, do którego dostęp jest uzyskiwany z tej podsieci. Jeśli na kontach Azure Cosmos DB istnieje zapora oparta na protokole IP zezwalająca na tę podsieć, żądania z podsieci z włączoną obsługą usług nie są już zgodne z regułami zapory IP i są odrzucane.
>
> Aby dowiedzieć się więcej, zobacz czynności opisane w sekcji [Migrowanie z reguły zapory IP do listy kontroli dostępu do sieci wirtualnej](#migrate-from-firewall-to-vnet) w tym artykule.

W poniższych sekcjach opisano sposób konfigurowania punktu końcowego usługi sieci wirtualnej dla konta Azure Cosmos DB.

## <a name="configure-a-service-endpoint-by-using-the-azure-portal"></a><a id="configure-using-portal"></a>Konfigurowanie punktu końcowego usługi przy użyciu Azure Portal

### <a name="configure-a-service-endpoint-for-an-existing-azure-virtual-network-and-subnet"></a>Konfigurowanie punktu końcowego usługi dla istniejącej sieci wirtualnej platformy Azure i podsieci

1. W bloku **wszystkie zasoby** znajdź konto Azure Cosmos DB, które chcesz zabezpieczyć.

1. Wybierz pozycję **zapory i sieci wirtualne** z menu Ustawienia, a następnie wybierz opcję zezwolenia na dostęp z **wybranych sieci**.

1. Aby udzielić dostępu do podsieci istniejącej sieci wirtualnej, w obszarze **sieci wirtualne** wybierz pozycję **Dodaj istniejącą sieć wirtualną platformy Azure**.

1. Wybierz **subskrypcję** , z której chcesz dodać sieć wirtualną platformy Azure. Wybierz **sieci wirtualne** i **podsieci** platformy Azure, dla których chcesz zapewnić dostęp do konta Azure Cosmos DB. Następnie wybierz pozycję **Włącz** , aby włączyć wybrane sieci z punktami końcowymi usługi dla elementu "Microsoft. AzureCosmosDB". Po zakończeniu wybierz pozycję **Dodaj**.

   :::image type="content" source="./media/how-to-configure-vnet-service-endpoint/choose-subnet-and-vnet.png" alt-text="Wybierz sieć wirtualną i podsieć":::

1. Po włączeniu konta Azure Cosmos DB dostępu z sieci wirtualnej zezwoli on na ruch tylko z tej wybranej podsieci. Dodana Sieć wirtualna i podsieć powinna zostać wyświetlona, jak pokazano na poniższym zrzucie ekranu:

   :::image type="content" source="./media/how-to-configure-vnet-service-endpoint/vnet-and-subnet-configured-successfully.png" alt-text="Pomyślnie skonfigurowano sieć wirtualną i podsieć":::

> [!NOTE]
> Aby włączyć punkty końcowe usługi sieci wirtualnej, wymagane są następujące uprawnienia do subskrypcji:
>   * Subskrypcja z siecią wirtualną: współautor sieci
>   * Subskrypcja przy użyciu konta Azure Cosmos DB: współautor konta DocumentDB
>   * Jeśli Twoja sieć wirtualna i konto Azure Cosmos DB znajdują się w różnych subskrypcjach, upewnij się, że subskrypcja z siecią wirtualną również ma `Microsoft.DocumentDB` zarejestrowany dostawca zasobów. Aby zarejestrować dostawcę zasobów, zobacz artykuł [dostawcy zasobów platformy Azure i typy](../azure-resource-manager/management/resource-providers-and-types.md) artykułów.

Poniżej przedstawiono wskazówki dotyczące rejestrowania subskrypcji przy użyciu dostawcy zasobów.

### <a name="configure-a-service-endpoint-for-a-new-azure-virtual-network-and-subnet"></a>Konfigurowanie punktu końcowego usługi dla nowej sieci wirtualnej platformy Azure i podsieci

1. W bloku **wszystkie zasoby** znajdź konto Azure Cosmos DB, które chcesz zabezpieczyć.  

1. Wybierz pozycję **zapory i sieci wirtualne platformy Azure** z menu Ustawienia, a następnie wybierz opcję zezwolenia na dostęp z **wybranych sieci**.  

1. Aby udzielić dostępu do nowej sieci wirtualnej platformy Azure, w obszarze **sieci wirtualne** wybierz pozycję **Dodaj nową sieć wirtualną**.  

1. Podaj szczegóły wymagane do utworzenia nowej sieci wirtualnej, a następnie wybierz pozycję **Utwórz**. Podsieć zostanie utworzona z punktem końcowym usługi dla "Microsoft. AzureCosmosDB".

   :::image type="content" source="./media/how-to-configure-vnet-service-endpoint/choose-subnet-and-vnet-new-vnet.png" alt-text="Wybierz sieć wirtualną i podsieć dla nowej sieci wirtualnej":::

Jeśli konto Azure Cosmos DB jest używane przez inne usługi platformy Azure, takie jak Azure Wyszukiwanie poznawcze lub dostęp za pośrednictwem usług Stream Analytics lub Power BI, zezwolisz na dostęp poprzez wybranie opcji **Akceptuj połączenia z poziomu globalnych centrów danych platformy Azure**.

Aby mieć pewność, że masz dostęp do metryk Azure Cosmos DB z poziomu portalu, musisz włączyć opcję **Zezwalaj na dostęp z poziomu Azure Portal** . Aby dowiedzieć się więcej na temat tych opcji, zobacz artykuł [Konfigurowanie zapory IP](how-to-configure-firewall.md) . Po włączeniu dostępu wybierz pozycję **Zapisz** , aby zapisać ustawienia.

## <a name="remove-a-virtual-network-or-subnet"></a><a id="remove-vnet-or-subnet"></a>Usuwanie sieci wirtualnej lub podsieci

1. W bloku **wszystkie zasoby** znajdź konto Azure Cosmos DB, dla którego przypisano punkty końcowe usługi.  

1. Wybierz pozycję **zapory i sieci wirtualne** z menu Ustawienia.  

1. Aby usunąć regułę sieci wirtualnej lub podsieci, wybierz pozycję **...** obok sieci wirtualnej lub podsieci, a następnie wybierz pozycję **Usuń**.

   :::image type="content" source="./media/how-to-configure-vnet-service-endpoint/remove-a-vnet.png" alt-text="Usuwanie sieci wirtualnej":::

1. Aby zastosować zmiany, wybierz pozycję **Zapisz**.

## <a name="configure-a-service-endpoint-by-using-azure-powershell"></a><a id="configure-using-powershell"></a>Konfigurowanie punktu końcowego usługi przy użyciu Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Wykonaj następujące kroki, aby skonfigurować punkt końcowy usługi na koncie Azure Cosmos DB przy użyciu Azure PowerShell:  

1. Zainstaluj [Azure PowerShell](/powershell/azure/install-Az-ps) i [Zaloguj się](/powershell/azure/authenticate-azureps).  

1. Włącz punkt końcowy usługi dla istniejącej podsieci sieci wirtualnej.  

   ```powershell
   $resourceGroupName = "<Resource group name>"
   $vnetName = "<Virtual network name>"
   $subnetName = "<Subnet name>"
   $subnetPrefix = "<Subnet address range>"
   $serviceEndpoint = "Microsoft.AzureCosmosDB"

   Get-AzVirtualNetwork `
      -ResourceGroupName $resourceGroupName `
      -Name $vnetName | Set-AzVirtualNetworkSubnetConfig `
      -Name $subnetName `
      -AddressPrefix $subnetPrefix `
      -ServiceEndpoint $serviceEndpoint | Set-AzVirtualNetwork
   ```

   > [!NOTE]
   > Gdy korzystasz z programu PowerShell lub interfejsu wiersza polecenia platformy Azure, pamiętaj, aby określić kompletną listę list ACL filtrów i sieci wirtualnych w parametrach, a nie tylko te, które należy dodać.

1. Pobierz informacje o sieci wirtualnej.

   ```powershell
   $vnet = Get-AzVirtualNetwork `
      -ResourceGroupName $resourceGroupName `
      -Name $vnetName

   $subnetId = $vnet.Id + "/subnets/" + $subnetName
   ```

1. Przygotowywanie reguły Virtual Network Cosmos DB

   ```powershell
   $vnetRule = New-AzCosmosDBVirtualNetworkRule `
      -Id $subnetId
   ```

1. Zaktualizuj właściwości konta Azure Cosmos DB przy użyciu nowej konfiguracji Virtual Network punktu końcowego: 

   ```powershell
   $accountName = "<Cosmos DB account name>"

   Update-AzCosmosDBAccount `
      -ResourceGroupName $resourceGroupName `
      -Name $accountName `
      -EnableVirtualNetwork $true `
      -VirtualNetworkRuleObject @($vnetRule)
   ```

1. Uruchom następujące polecenie, aby sprawdzić, czy konto Azure Cosmos DB zostało zaktualizowane za pomocą punktu końcowego usługi sieci wirtualnej, który został skonfigurowany w poprzednim kroku:

   ```powershell
   $account = Get-AzCosmosDBAccount `
      -ResourceGroupName $resourceGroupName `
      -Name $accountName

   $account.IsVirtualNetworkFilterEnabled
   $account.VirtualNetworkRules
   ```

## <a name="configure-a-service-endpoint-by-using-the-azure-cli"></a><a id="configure-using-cli"></a>Konfigurowanie punktu końcowego usługi przy użyciu interfejsu wiersza polecenia platformy Azure

Konta usługi Azure Cosmos można skonfigurować dla punktów końcowych usług, gdy są one tworzone lub aktualizowane w późniejszym czasie, jeśli podsieć jest już skonfigurowana. Punkty końcowe usługi można również włączyć na koncie Cosmos, w którym podsieć nie została jeszcze skonfigurowana dla nich, a następnie zacznie działać, gdy podsieć zostanie skonfigurowana później. Ta elastyczność pozwala administratorom, którzy nie mają dostępu do zasobów konta Cosmos i sieci wirtualnej, w celu zapewnienia ich konfiguracji niezależnie od siebie.

### <a name="create-a-new-cosmos-account-and-connect-it-to-a-back-end-subnet-for-a-new-virtual-network"></a>Utwórz nowe konto Cosmos i połącz je z podsiecią zaplecza dla nowej sieci wirtualnej

W tym przykładzie Sieć wirtualna i podsieć jest tworzona z włączonymi punktami końcowymi usługi podczas ich tworzenia.

```azurecli-interactive
# Create an Azure Cosmos Account with a service endpoint connected to a backend subnet

# Resource group and Cosmos account variables
resourceGroupName='MyResourceGroup'
location='West US 2'
accountName='mycosmosaccount'

# Variables for a new Virtual Network with two subnets
vnetName='myVnet'
frontEnd='FrontEnd'
backEnd='BackEnd'

# Create a resource group
az group create -n $resourceGroupName -l $location

# Create a virtual network with a front-end subnet
az network vnet create \
   -n $vnetName \
   -g $resourceGroupName \
   --address-prefix 10.0.0.0/16 \
   --subnet-name $frontEnd \
   --subnet-prefix 10.0.1.0/24

# Create a back-end subnet with service endpoints enabled for Cosmos DB
az network vnet subnet create \
   -n $backEnd \
   -g $resourceGroupName \
   --address-prefix 10.0.2.0/24 \
   --vnet-name $vnetName \
   --service-endpoints Microsoft.AzureCosmosDB

svcEndpoint=$(az network vnet subnet show -g $resourceGroupName -n $backEnd --vnet-name $vnetName --query 'id' -o tsv)

# Create a Cosmos DB account with default values and service endpoints
az cosmosdb create \
   -n $accountName \
   -g $resourceGroupName \
   --enable-virtual-network true \
   --virtual-network-rules $svcEndpoint
```

### <a name="connect-and-configure-a-cosmos-account-to-a-back-end-subnet-independently"></a>Łączenie i Konfigurowanie konta Cosmos w podsieci zaplecza niezależnie

Ten przykład ma na celu pokazanie sposobu łączenia konta usługi Azure Cosmos z istniejącą nową siecią wirtualną, w której podsieć nie została jeszcze skonfigurowana dla punktów końcowych usługi. Jest to realizowane za pomocą `--ignore-missing-vnet-service-endpoint` parametru. Pozwala to na ukończenie konfiguracji konta Cosmos bez błędu przed ukończeniem konfiguracji podsieci sieci wirtualnej. Po zakończeniu konfiguracji podsieci konto Cosmos będzie dostępne za pomocą skonfigurowanej podsieci.

```azurecli-interactive
# Create an Azure Cosmos Account with a service endpoint connected to a backend subnet
# that is not yet enabled for service endpoints.

# Resource group and Cosmos account variables
resourceGroupName='MyResourceGroup'
location='West US 2'
accountName='mycosmosaccount'

# Variables for a new Virtual Network with two subnets
vnetName='myVnet'
frontEnd='FrontEnd'
backEnd='BackEnd'

# Create a resource group
az group create -n $resourceGroupName -l $location

# Create a virtual network with a front-end subnet
az network vnet create \
   -n $vnetName \
   -g $resourceGroupName \
   --address-prefix 10.0.0.0/16 \
   --subnet-name $frontEnd \
   --subnet-prefix 10.0.1.0/24

# Create a back-end subnet but without configuring service endpoints (--service-endpoints Microsoft.AzureCosmosDB)
az network vnet subnet create \
   -n $backEnd \
   -g $resourceGroupName \
   --address-prefix 10.0.2.0/24 \
   --vnet-name $vnetName

svcEndpoint=$(az network vnet subnet show -g $resourceGroupName -n $backEnd --vnet-name $vnetName --query 'id' -o tsv)

# Create a Cosmos DB account with default values
az cosmosdb create -n $accountName -g $resourceGroupName

# Add the virtual network rule but ignore the missing service endpoint on the subnet
az cosmosdb network-rule add \
   -n $accountName \
   -g $resourceGroupName \
   --virtual-network $vnetName \
   --subnet svcEndpoint \
   --ignore-missing-vnet-service-endpoint true

read -p'Press any key to now configure the subnet for service endpoints'

az network vnet subnet update \
   -n $backEnd \
   -g $resourceGroupName \
   --vnet-name $vnetName \
   --service-endpoints Microsoft.AzureCosmosDB
```

## <a name="port-range-when-using-direct-mode"></a>Zakres portów w trybie bezpośrednim

W przypadku korzystania z punktów końcowych usługi z kontem usługi Azure Cosmos za pośrednictwem połączenia trybu bezpośredniego należy upewnić się, że zakres portów TCP od 10000 do 20000 jest otwarty.

## <a name="migrating-from-an-ip-firewall-rule-to-a-virtual-network-acl"></a><a id="migrate-from-firewall-to-vnet"></a>Migrowanie z reguły zapory IP do listy ACL sieci wirtualnej

Aby przeprowadzić migrację konta Azure Cosmos DB z używania reguł zapory protokołu IP do korzystania z punktów końcowych usługi sieci wirtualnej, wykonaj następujące czynności.

Po skonfigurowaniu konta Azure Cosmos DB dla punktu końcowego usługi dla podsieci żądania z tej podsieci są wysyłane do Azure Cosmos DB przy użyciu informacji o sieci wirtualnej i źródła podsieci zamiast źródłowego publicznego adresu IP. Te żądania nie będą już zgodne z filtrem IP skonfigurowanym na koncie Azure Cosmos DB, dlatego należy wykonać następujące czynności, aby uniknąć przestoju.

Przed kontynuowaniem Włącz punkt końcowy usługi Azure Cosmos DB w sieci wirtualnej i podsieci, korzystając z kroku pokazanego powyżej w temacie "Włączanie punktu końcowego usługi dla istniejącej podsieci sieci wirtualnej".

1. Pobierz informacje o sieci wirtualnej i podsieci:

   ```powershell
   $resourceGroupName = "myResourceGroup"
   $accountName = "mycosmosaccount"
   $vnetName = "myVnet"
   $subnetName = "mySubnet"

   $vnet = Get-AzVirtualNetwork `
      -ResourceGroupName $resourceGroupName `
      -Name $vnetName

   $subnetId = $vnet.Id + "/subnets/" + $subnetName
   ```

1. Przygotuj nowy obiekt reguły Virtual Network dla konta Azure Cosmos DB:

   ```powershell
   $vnetRule = New-AzCosmosDBVirtualNetworkRule `
      -Id $subnetId
   ```

1. Zaktualizuj konto Azure Cosmos DB, aby umożliwić dostęp do punktu końcowego usługi z podsieci:

   ```powershell
   Update-AzCosmosDBAccount `
      -ResourceGroupName $resourceGroupName `
      -Name $accountName `
      -EnableVirtualNetwork $true `
      -VirtualNetworkRuleObject @($vnetRule)
   ```

1. Powtórz poprzednie kroki dla wszystkich kont Azure Cosmos DB, do których uzyskano dostęp z podsieci.

1. Usuń regułę zapory IP dla podsieci z reguł zapory konta Azure Cosmos DB.

## <a name="frequently-asked-questions"></a>Często zadawane pytania

Poniżej przedstawiono kilka często zadawanych pytań dotyczących konfigurowania dostępu z sieci wirtualnych:

### <a name="are-notebooks-and-mongocassandra-shell-currently-compatible-with-virtual-network-enabled-accounts"></a>Czy powłoki Notesy i Mongo/Cassandra są obecnie zgodne z włączonymi kontami Virtual Network?

W tej chwili integracja [powłoki Mongo](https://devblogs.microsoft.com/cosmosdb/preview-native-mongo-shell/) i [powłoki Cassandra](https://devblogs.microsoft.com/cosmosdb/announcing-native-cassandra-shell-preview/) w Eksplorator danych Cosmos DB i [usługi notesów Jupyter](./cosmosdb-jupyter-notebooks.md)nie są obsługiwane w przypadku dostępu do sieci wirtualnej. Jest to obecnie aktywne programowanie.

### <a name="can-i-specify-both-virtual-network-service-endpoint-and-ip-access-control-policy-on-an-azure-cosmos-account"></a>Czy można określić zarówno punkt końcowy usługi sieci wirtualnej, jak i zasady kontroli dostępu IP na koncie usługi Azure Cosmos? 

Można włączyć zarówno punkt końcowy usługi sieci wirtualnej, jak i zasady kontroli dostępu IP (nazywane także zaporą) na koncie usługi Azure Cosmos. Te dwie funkcje uzupełniają się i wspólnie zapewniają izolację i bezpieczeństwo konta usługi Azure Cosmos. Użycie zapory IP gwarantuje, że statyczne adresy IP będą mogły uzyskiwać dostęp do Twojego konta. 

### <a name="how-do-i-limit-access-to-subnet-within-a-virtual-network"></a>Jak mogę ograniczyć dostęp do podsieci w ramach sieci wirtualnej? 

Istnieją dwa kroki, które należy wykonać, aby ograniczyć dostęp do konta usługi Azure Cosmos z podsieci. Najpierw można zezwolić na ruch z podsieci, aby mógł przenieść swoją podsieć i tożsamość sieci wirtualnej do Azure Cosmos DB. W tym celu należy włączyć punkt końcowy usługi dla Azure Cosmos DB w podsieci. Następnie Dodaj regułę do konta usługi Azure Cosmos, określając tę podsieć jako źródło, z którego można uzyskać dostęp do konta.

### <a name="will-virtual-network-acls-and-ip-firewall-reject-requests-or-connections"></a>Czy listy ACL sieci wirtualnych i zapory IP odrzucają żądania lub połączenia? 

Gdy są dodawane reguły dostępu zapory IP lub sieci wirtualnej, tylko żądania od dozwolonych źródeł uzyskują prawidłowe odpowiedzi. Inne żądania są odrzucane z 403 (niedozwolone). Ważne jest, aby rozróżnić zaporę konta usługi Azure Cosmos na podstawie zapory na poziomie połączenia. Źródło może nadal łączyć się z usługą, a połączenia same nie są odrzucane.

### <a name="my-requests-started-getting-blocked-when-i-enabled-service-endpoint-to-azure-cosmos-db-on-the-subnet-what-happened"></a>Moje żądania rozpoczęły się, gdy włączono punkt końcowy usługi do Azure Cosmos DB w podsieci. Co się stało?

Po włączeniu punktu końcowego usługi dla Azure Cosmos DB w podsieci źródło ruchu docierające do konta jest przełączane z publicznego adresu IP do sieci wirtualnej i podsieci. Jeśli konto usługi Azure Cosmos ma zaporę opartą na protokole IP, ruch z podsieci z obsługą usługi nie będzie już zgodny z regułami zapory IP i dlatego zostanie odrzucony. Wykonaj kroki, aby bezproblemowo migrować z zapory opartej na protokole IP do kontroli dostępu opartej na sieci wirtualnej.

### <a name="are-additional-azure-rbac-permissions-needed-for-azure-cosmos-accounts-with-vnet-service-endpoints"></a>Czy dla kont Azure Cosmos z punktami końcowymi usługi sieci wirtualnej są spełnione dodatkowe uprawnienia kontroli RBAC platformy Azure?

Po dodaniu punktów końcowych usługi sieci wirtualnej do konta usługi Azure Cosmos, aby wprowadzić zmiany w ustawieniach konta, musisz mieć dostęp do `Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action` akcji dla wszystkich sieci wirtualnych skonfigurowanych na koncie Azure Cosmos. To uprawnienie jest wymagane, ponieważ proces autoryzacji sprawdza dostęp do zasobów (takich jak zasoby bazy danych i sieci wirtualnej) przed ocenami wszystkich właściwości.
 
Autoryzacja sprawdza poprawność uprawnienia do akcji zasobu sieci wirtualnej, nawet jeśli użytkownik nie określi list ACL sieci wirtualnej przy użyciu interfejsu wiersza polecenia platformy Azure. Obecnie płaszczyzna kontroli konta usługi Azure Cosmos obsługuje ustawienie pełnego stanu konta usługi Azure Cosmos. Jednym z parametrów wywołania płaszczyzny kontroli jest `virtualNetworkRules` . Jeśli ten parametr nie jest określony, interfejs wiersza polecenia platformy Azure tworzy wywołanie Get Database, aby pobrać `virtualNetworkRules` i używa tej wartości w wywołaniu aktualizacji.

### <a name="do-the-peered-virtual-networks-also-have-access-to-azure-cosmos-account"></a>Czy równorzędne sieci wirtualne mają również dostęp do konta usługi Azure Cosmos? 
Tylko sieć wirtualna i ich podsieci dodane do konta usługi Azure Cosmos mają dostęp. Ich Komunikacja równorzędna sieci wirtualnych nie może uzyskać dostępu do konta do momentu dodania do konta podsieci w ramach równorzędnych sieci wirtualnych.

### <a name="what-is-the-maximum-number-of-subnets-allowed-to-access-a-single-cosmos-account"></a>Jaka jest maksymalna liczba podsieci, które mogą uzyskać dostęp do jednego konta Cosmos? 
Obecnie możesz mieć maksymalnie 256 podsieci dozwolonych dla konta usługi Azure Cosmos.

### <a name="can-i-enable-access-from-vpn-and-express-route"></a>Czy mogę włączyć dostęp z sieci VPN i Express Route? 
Aby uzyskać dostęp do konta Azure Cosmos za pośrednictwem usługi Express Route z lokalnego, należy włączyć komunikację równorzędną firmy Microsoft. Po wprowadzeniu zapory IP lub reguł dostępu do sieci wirtualnej można dodać publiczne adresy IP używane do komunikacji równorzędnej firmy Microsoft na zaporze IP konta usługi Azure Cosmos, aby zezwolić na dostęp usług lokalnych do konta usługi Azure Cosmos. 

### <a name="do-i-need-to-update-the-network-security-groups-nsg-rules"></a>Czy muszę zaktualizować reguły grup zabezpieczeń sieci (sieciowej grupy zabezpieczeń)? 
Reguły sieciowej grupy zabezpieczeń są używane do ograniczania łączności do i z podsieci przy użyciu sieci wirtualnej. Gdy dodasz punkt końcowy usługi dla Azure Cosmos DB do podsieci, nie ma potrzeby otwierania łączności wychodzącej w sieciowej grupy zabezpieczeń dla konta usługi Azure Cosmos. 

### <a name="are-service-endpoints-available-for-all-vnets"></a>Czy punkty końcowe usługi są dostępne dla wszystkich sieci wirtualnych?
Nie, tylko Azure Resource Manager sieci wirtualnych może mieć włączony punkt końcowy usługi. Klasyczne sieci wirtualne nie obsługują punktów końcowych usługi.

### <a name="can-i-accept-connections-from-within-public-azure-datacenters-when-service-endpoint-access-is-enabled-for-azure-cosmos-db"></a>Czy mogę "akceptować połączenia z poziomu publicznych centrów danych platformy Azure", gdy dostęp do punktu końcowego usługi jest włączony dla Azure Cosmos DB?  
Jest to wymagane tylko wtedy, gdy chcesz, aby konto Azure Cosmos DB było dostępne przez inne usługi platformy Azure, takie jak Azure Data Factory, Azure Wyszukiwanie poznawcze lub dowolna usługa wdrożona w danym regionie świadczenia usługi Azure.

## <a name="next-steps"></a>Następne kroki

* Aby skonfigurować zaporę dla Azure Cosmos DB, zapoznaj się z artykułem [dotyczącym obsługi zapory](how-to-configure-firewall.md) .
