---
title: Konfigurowanie dostępu opartego na sieci wirtualnej dla konta usługi Azure Cosmos
description: W tym dokumencie opisano kroki wymagane do skonfigurowania punktu końcowego usługi sieci wirtualnej dla Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.date: 06/04/2020
ms.author: mjbrown
ms.custom: devx-track-azurecli
ms.openlocfilehash: 995e5a1a87ee332c48641f42c4134e3e58f11cfa
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87495424"
---
# <a name="configure-access-from-virtual-networks-vnet"></a>Konfigurowanie dostępu z sieci wirtualnych (VNet)

Konta usługi Azure Cosmos DB można skonfigurować tak, aby zezwalać na dostęp tylko z określonej podsieci sieci wirtualnej platformy Azure. Aby ograniczyć dostęp do konta Azure Cosmos DB z połączeniami z podsieci w sieci wirtualnej:

1. Włącz podsieć do wysyłania do Azure Cosmos DB podsieci i tożsamości sieci wirtualnej. Można to osiągnąć przez włączenie punktu końcowego usługi dla Azure Cosmos DB w określonej podsieci.

1. Dodaj regułę na koncie Azure Cosmos DB, aby określić podsieć jako źródło, z którego można uzyskać dostęp do konta.

> [!NOTE]
> Gdy punkt końcowy usługi dla konta Azure Cosmos DB jest włączony w podsieci, źródło ruchu, który dociera do Azure Cosmos DB przełącza z publicznego adresu IP do sieci wirtualnej i podsieci. Przełączenie ruchu ma zastosowanie do każdego konta Azure Cosmos DB, do którego dostęp jest uzyskiwany z tej podsieci. Jeśli na kontach Azure Cosmos DB istnieje zapora oparta na protokole IP zezwalająca na tę podsieć, żądania z podsieci z włączoną obsługą usług nie są już zgodne z regułami zapory IP i są odrzucane.
>
> Aby dowiedzieć się więcej, zobacz czynności opisane w sekcji [Migrowanie z reguły zapory IP do listy kontroli dostępu do sieci wirtualnej](#migrate-from-firewall-to-vnet) w tym artykule.

W poniższych sekcjach opisano sposób konfigurowania punktu końcowego usługi sieci wirtualnej dla konta Azure Cosmos DB.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="configure-a-service-endpoint-by-using-the-azure-portal"></a><a id="configure-using-portal"></a>Konfigurowanie punktu końcowego usługi przy użyciu Azure Portal

### <a name="configure-a-service-endpoint-for-an-existing-azure-virtual-network-and-subnet"></a>Konfigurowanie punktu końcowego usługi dla istniejącej sieci wirtualnej platformy Azure i podsieci

1. W bloku **wszystkie zasoby** znajdź konto Azure Cosmos DB, które chcesz zabezpieczyć.

1. Wybierz pozycję **zapory i sieci wirtualne** z menu Ustawienia, a następnie wybierz opcję zezwolenia na dostęp z **wybranych sieci**.

1. Aby udzielić dostępu do podsieci istniejącej sieci wirtualnej, w obszarze **sieci wirtualne**wybierz pozycję **Dodaj istniejącą sieć wirtualną platformy Azure**.

1. Wybierz **subskrypcję** , z której chcesz dodać sieć wirtualną platformy Azure. Wybierz **sieci wirtualne** i **podsieci** platformy Azure, dla których chcesz zapewnić dostęp do konta Azure Cosmos DB. Następnie wybierz pozycję **Włącz** , aby włączyć wybrane sieci z punktami końcowymi usługi dla elementu "Microsoft. AzureCosmosDB". Po zakończeniu wybierz pozycję **Dodaj**.

   :::image type="content" source="./media/how-to-configure-vnet-service-endpoint/choose-subnet-and-vnet.png" alt-text="Wybierz sieć wirtualną i podsieć":::

1. Po włączeniu konta Azure Cosmos DB dostępu z sieci wirtualnej zezwoli on na ruch tylko z tej wybranej podsieci. Dodana Sieć wirtualna i podsieć powinna zostać wyświetlona, jak pokazano na poniższym zrzucie ekranu:

   :::image type="content" source="./media/how-to-configure-vnet-service-endpoint/vnet-and-subnet-configured-successfully.png" alt-text="Wybierz sieć wirtualną i podsieć":::

> [!NOTE]
> Aby włączyć punkty końcowe usługi sieci wirtualnej, wymagane są następujące uprawnienia do subskrypcji:
>   * Subskrypcja z siecią wirtualną: współautor sieci
>   * Subskrypcja przy użyciu konta Azure Cosmos DB: współautor konta DocumentDB
>   * Jeśli Twoja sieć wirtualna i konto Azure Cosmos DB znajdują się w różnych subskrypcjach, upewnij się, że subskrypcja z siecią wirtualną również ma `Microsoft.DocumentDB` zarejestrowany dostawca zasobów. Aby zarejestrować dostawcę zasobów, zobacz artykuł [dostawcy zasobów platformy Azure i typy](../azure-resource-manager/management/resource-providers-and-types.md) artykułów.

Poniżej przedstawiono wskazówki dotyczące rejestrowania subskrypcji przy użyciu dostawcy zasobów.

### <a name="configure-a-service-endpoint-for-a-new-azure-virtual-network-and-subnet"></a>Konfigurowanie punktu końcowego usługi dla nowej sieci wirtualnej platformy Azure i podsieci

1. W bloku **wszystkie zasoby** znajdź konto Azure Cosmos DB, które chcesz zabezpieczyć.  

1. Wybierz pozycję **zapory i sieci wirtualne platformy Azure** z menu Ustawienia, a następnie wybierz opcję zezwolenia na dostęp z **wybranych sieci**.  

1. Aby udzielić dostępu do nowej sieci wirtualnej platformy Azure, w obszarze **sieci wirtualne**wybierz pozycję **Dodaj nową sieć wirtualną**.  

1. Podaj szczegóły wymagane do utworzenia nowej sieci wirtualnej, a następnie wybierz pozycję **Utwórz**. Podsieć zostanie utworzona z punktem końcowym usługi dla "Microsoft. AzureCosmosDB".

   :::image type="content" source="./media/how-to-configure-vnet-service-endpoint/choose-subnet-and-vnet-new-vnet.png" alt-text="Wybierz sieć wirtualną i podsieć":::

Jeśli konto Azure Cosmos DB jest używane przez inne usługi platformy Azure, takie jak Azure Wyszukiwanie poznawcze lub dostęp za pośrednictwem usług Stream Analytics lub Power BI, zezwolisz na dostęp poprzez wybranie opcji **Akceptuj połączenia z poziomu globalnych centrów danych platformy Azure**.

Aby mieć pewność, że masz dostęp do metryk Azure Cosmos DB z poziomu portalu, musisz włączyć opcję **Zezwalaj na dostęp z poziomu Azure Portal** . Aby dowiedzieć się więcej na temat tych opcji, zobacz artykuł [Konfigurowanie zapory IP](how-to-configure-firewall.md) . Po włączeniu dostępu wybierz pozycję **Zapisz** , aby zapisać ustawienia.

## <a name="remove-a-virtual-network-or-subnet"></a><a id="remove-vnet-or-subnet"></a>Usuwanie sieci wirtualnej lub podsieci

1. W bloku **wszystkie zasoby** znajdź konto Azure Cosmos DB, dla którego przypisano punkty końcowe usługi.  

1. Wybierz pozycję **zapory i sieci wirtualne** z menu Ustawienia.  

1. Aby usunąć regułę sieci wirtualnej lub podsieci, wybierz pozycję **...** obok sieci wirtualnej lub podsieci, a następnie wybierz pozycję **Usuń**.

   :::image type="content" source="./media/how-to-configure-vnet-service-endpoint/remove-a-vnet.png" alt-text="Wybierz sieć wirtualną i podsieć":::

1. Aby zastosować zmiany, wybierz pozycję **Zapisz**.

## <a name="configure-a-service-endpoint-by-using-azure-powershell"></a><a id="configure-using-powershell"></a>Konfigurowanie punktu końcowego usługi przy użyciu Azure PowerShell

> [!NOTE]
> Gdy korzystasz z programu PowerShell lub interfejsu wiersza polecenia platformy Azure, pamiętaj, aby określić kompletną listę list ACL filtrów i sieci wirtualnych w parametrach, a nie tylko te, które należy dodać.

Wykonaj następujące kroki, aby skonfigurować punkt końcowy usługi na koncie Azure Cosmos DB przy użyciu Azure PowerShell:  

1. Zainstaluj [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps) i [Zaloguj się](https://docs.microsoft.com/powershell/azure/authenticate-azureps).  

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

## <a name="next-steps"></a>Następne kroki

* Aby skonfigurować zaporę dla Azure Cosmos DB, zapoznaj się z artykułem [dotyczącym obsługi zapory](firewall-support.md) .
