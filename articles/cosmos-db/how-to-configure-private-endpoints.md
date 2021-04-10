---
title: Konfigurowanie prywatnego linku platformy Azure dla konta usługi Azure Cosmos
description: Dowiedz się, jak skonfigurować link prywatny platformy Azure, aby uzyskać dostęp do konta usługi Azure Cosmos przy użyciu prywatnego adresu IP w sieci wirtualnej.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: how-to
ms.date: 03/26/2021
ms.author: thweiss
ms.custom: devx-track-azurecli
ms.openlocfilehash: 034eb35eeef975be23cc318aa797282008d71728
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105936907"
---
# <a name="configure-azure-private-link-for-an-azure-cosmos-account"></a>Konfigurowanie prywatnego linku platformy Azure dla konta usługi Azure Cosmos
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Za pomocą prywatnego linku platformy Azure można nawiązać połączenie z kontem usługi Azure Cosmos za pośrednictwem prywatnego punktu końcowego. Prywatny punkt końcowy to zestaw prywatnych adresów IP w podsieci w sieci wirtualnej. Następnie możesz ograniczyć dostęp do konta usługi Azure Cosmos za pośrednictwem prywatnych adresów IP. Jeśli łącze prywatne jest połączone z ograniczonymi zasadami sieciowej grupy zabezpieczeń, pomaga zmniejszyć ryzyko związane z eksfiltracji danych. Aby dowiedzieć się więcej o prywatnych punktach końcowych, zobacz artykuł [prywatny link do platformy Azure](../private-link/private-link-overview.md) .

> [!NOTE]
> Link prywatny nie uniemożliwia rozwiązywania punktów końcowych usługi Azure Cosmos przez publiczny serwer DNS. Filtrowanie żądań przychodzących odbywa się na poziomie aplikacji, a nie na poziomie transportu ani sieci.

Link prywatny umożliwia użytkownikom dostęp do konta usługi Azure Cosmos z poziomu sieci wirtualnej lub z dowolnej równorzędnej sieci wirtualnej. Zasoby mapowane do linku prywatnego są również dostępne lokalnie przez prywatną komunikację równorzędną za pośrednictwem sieci VPN lub Azure ExpressRoute.

Można nawiązać połączenie z kontem usługi Azure Cosmos skonfigurowanym za pomocą linku prywatnego przy użyciu metody zatwierdzania automatyczne lub ręczne. Aby dowiedzieć się więcej, zapoznaj się z sekcją [przepływ pracy zatwierdzania](../private-link/private-endpoint-overview.md#access-to-a-private-link-resource-using-approval-workflow) w dokumentacji łącza prywatnego.

W tym artykule opisano sposób konfigurowania prywatnych punktów końcowych Azure Cosmos DB magazynów transakcyjnych. Przyjęto założenie, że używasz metody automatycznego zatwierdzania. Jeśli używasz magazynu analitycznego, zobacz [prywatne punkty końcowe dla artykułu magazynu analitycznego](analytical-store-private-endpoints.md) .

## <a name="create-a-private-endpoint-by-using-the-azure-portal"></a>Tworzenie prywatnego punktu końcowego za pomocą Azure Portal

Wykonaj następujące kroki, aby utworzyć prywatny punkt końcowy dla istniejącego konta usługi Azure Cosmos przy użyciu Azure Portal:

1. W okienku **wszystkie zasoby** wybierz konto usługi Azure Cosmos.

1. Wybierz pozycję **połączenia prywatne punktów końcowych** z listy ustawień, a następnie wybierz pozycję **prywatny punkt końcowy**:

   :::image type="content" source="./media/how-to-configure-private-endpoints/create-private-endpoint-portal.png" alt-text="Wybory dla tworzenia prywatnego punktu końcowego w Azure Portal":::

1. W okienku **Utwórz prywatny punkt końcowy — podstawowe** wprowadź lub wybierz następujące szczegóły:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | **Szczegóły projektu** | |
    | Subskrypcja | Wybierz subskrypcję. |
    | Grupa zasobów | Wybierz grupę zasobów.|
    | **Szczegóły wystąpienia** |  |
    | Nazwa | Wprowadź dowolną nazwę prywatnego punktu końcowego. Jeśli ta nazwa jest wykonywana, utwórz unikatową. |
    |Region (Region)| Wybierz region, w którym chcesz wdrożyć link prywatny. Utwórz prywatny punkt końcowy w tej samej lokalizacji, w której znajduje się Twoja sieć wirtualna.|
    |||
1. Wybierz pozycję **Dalej: zasób**.
1. W obszarze **Utwórz prywatny punkt końcowy zasobu** wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    |Metoda połączenia  | Wybierz pozycję **Połącz z zasobem platformy Azure w moim katalogu**. <br/><br/> Następnie możesz wybrać jeden z zasobów, aby skonfigurować link prywatny. Możesz też nawiązać połączenie z zasobem innej osoby, używając identyfikatora zasobu lub aliasu, który zostały Ci udostępnione.|
    | Subskrypcja| Wybierz subskrypcję. |
    | Typ zasobu | Wybierz pozycję **Microsoft. AzureCosmosDB/databaseAccounts**. |
    | Zasób |Wybierz swoje konto usługi Azure Cosmos. |
    |Docelowy zasób podrzędny |Wybierz typ interfejsu API Azure Cosmos DB, który chcesz zmapować. Ta wartość domyślna to tylko jeden wybór dla interfejsów API SQL, MongoDB i Cassandra. W przypadku interfejsów API Gremlin i tabel można również wybrać **SQL** , ponieważ te interfejsy API współdziałają z interfejsem API SQL. |
    |||

1. Wybierz pozycję **Dalej: Konfiguracja**.
1. W obszarze **Tworzenie prywatnego punktu końcowego — konfiguracja** wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    |**Sieć**| |
    | Sieć wirtualna| Wybierz sieć wirtualną. |
    | Podsieć | Wybierz podsieć. |
    |**Integracja Prywatna strefa DNS**||
    |Integruj z prywatną strefą DNS |Wybierz pozycję **Tak**. <br><br/> Aby połączyć się prywatnie z prywatnym punktem końcowym, potrzebny jest rekord DNS. Zalecamy integrację prywatnego punktu końcowego z prywatną strefą DNS. Możesz również użyć własnych serwerów DNS lub utworzyć rekordy DNS przy użyciu plików hosta na maszynach wirtualnych. <br><br/> Wybranie opcji Tak spowoduje utworzenie grupy stref prywatnych DNS. Grupa strefy DNS jest łączem między prywatną strefą DNS i prywatnym punktem końcowym. Ten link ułatwia aktualizowanie prywatnej strefy DNS w przypadku aktualizowania prywatnego punktu końcowego. Na przykład po dodaniu lub usunięciu regionów prywatna strefa DNS jest automatycznie aktualizowana. |
    |Prywatna strefa DNS |Wybierz pozycję **privatelink.documents.Azure.com**. <br><br/> Prywatna strefa DNS jest określana automatycznie. Nie można go zmienić za pomocą Azure Portal.|
    |||

1. Wybierz pozycję **Przejrzyj i utwórz**. Na stronie **Recenzja i tworzenie** zweryfikuje swoją konfigurację.
1. Po wyświetleniu komunikatu **Sprawdzanie poprawności zakończone powodzeniem** kliknij przycisk **Utwórz**.

Po zaakceptowaniu prywatnego linku dla konta usługi Azure Cosmos w obszarze Azure Portal opcja **wszystkie sieci** w okienku **Zapora i sieci wirtualne** jest niedostępna.

## <a name="api-types-and-private-zone-names"></a><a id="private-zone-name-mapping"></a>Typy interfejsów API i nazwy stref prywatnych

W poniższej tabeli przedstawiono mapowanie między różnymi typami interfejsów API konta usługi Azure Cosmos, obsługiwanymi podrzędnymi zasobami i odpowiednimi nazwami stref prywatnych. Dostęp do kont Gremlin i interfejs API tabel można także uzyskać za pomocą interfejsu API SQL, dlatego dla tych interfejsów API są dostępne dwa wpisy.

|Typ interfejsu API konta usługi Azure Cosmos  |Obsługiwane zasoby podrzędne (lub identyfikatory grup) |Nazwa strefy prywatnej  |
|---------|---------|---------|
|Sql    |   Sql      | privatelink.documents.azure.com   |
|Cassandra    | Cassandra        |  privatelink.cassandra.cosmos.azure.com    |
|Mongo   |  MongoDB       |  privatelink.mongo.cosmos.azure.com    |
|Gremlin     | Gremlin        |  privatelink.gremlin.cosmos.azure.com   |
|Gremlin     |  Sql       |  privatelink.documents.azure.com    |
|Tabela    |    Tabela     |   privatelink.table.cosmos.azure.com    |
|Tabela     |   Sql      |  privatelink.documents.azure.com    |

### <a name="fetch-the-private-ip-addresses"></a>Pobierz prywatne adresy IP

Po aprowizacji prywatnego punktu końcowego możesz wysyłać zapytania dotyczące adresów IP. Aby wyświetlić adresy IP z Azure Portal:

1. Wybierz pozycję **Wszystkie zasoby**.
1. Wyszukaj utworzony wcześniej prywatny punkt końcowy. W tym przypadku jest to **cdbPrivateEndpoint3**.
1. Wybierz kartę **Przegląd** , aby wyświetlić ustawienia DNS i adresy IP.

:::image type="content" source="./media/how-to-configure-private-endpoints/private-ip-addresses-portal.png" alt-text="Prywatne adresy IP w Azure Portal":::

Utworzono wiele adresów IP dla prywatnego punktu końcowego:

* Jeden dla globalnego punktu końcowego (region-niezależny od) konta usługi Azure Cosmos
* Jeden dla każdego regionu, w którym wdrożono konto usługi Azure Cosmos

## <a name="create-a-private-endpoint-by-using-azure-powershell"></a>Tworzenie prywatnego punktu końcowego za pomocą Azure PowerShell

Uruchom następujący skrypt programu PowerShell, aby utworzyć prywatny punkt końcowy o nazwie "MyPrivateEndpoint" dla istniejącego konta usługi Azure Cosmos. Zastąp wartości zmiennej szczegółowymi informacjami dla danego środowiska.

```azurepowershell-interactive
$SubscriptionId = "<your Azure subscription ID>"
# Resource group where the Azure Cosmos account and virtual network resources are located
$ResourceGroupName = "myResourceGroup"
# Name of the Azure Cosmos account
$CosmosDbAccountName = "mycosmosaccount"

# API type of the Azure Cosmos account: Sql, MongoDB, Cassandra, Gremlin, or Table
$CosmosDbApiType = "Sql"
# Name of the existing virtual network
$VNetName = "myVnet"
# Name of the target subnet in the virtual network
$SubnetName = "mySubnet"
# Name of the private endpoint to create
$PrivateEndpointName = "MyPrivateEndpoint"
# Location where the private endpoint can be created. The private endpoint should be created in the same location where your subnet or the virtual network exists
$Location = "westcentralus"

$cosmosDbResourceId = "/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.DocumentDB/databaseAccounts/$($CosmosDbAccountName)"

$privateEndpointConnection = New-AzPrivateLinkServiceConnection -Name "myConnectionPS" -PrivateLinkServiceId $cosmosDbResourceId -GroupId $CosmosDbApiType
 
$virtualNetwork = Get-AzVirtualNetwork -ResourceGroupName  $ResourceGroupName -Name $VNetName  
 
$subnet = $virtualNetwork | Select -ExpandProperty subnets | Where-Object  {$_.Name -eq $SubnetName}  
 
$privateEndpoint = New-AzPrivateEndpoint -ResourceGroupName $ResourceGroupName -Name $PrivateEndpointName -Location "westcentralus" -Subnet  $subnet -PrivateLinkServiceConnection $privateEndpointConnection
```

### <a name="integrate-the-private-endpoint-with-a-private-dns-zone"></a>Integrowanie prywatnego punktu końcowego z prywatną strefą DNS

Po utworzeniu prywatnego punktu końcowego można zintegrować go z prywatną strefą DNS przy użyciu następującego skryptu programu PowerShell:

```azurepowershell-interactive
Import-Module Az.PrivateDns

# Zone name differs based on the API type and group ID you are using. 
$zoneName = "privatelink.documents.azure.com"
$zone = New-AzPrivateDnsZone -ResourceGroupName $ResourceGroupName `
  -Name $zoneName

$link  = New-AzPrivateDnsVirtualNetworkLink -ResourceGroupName $ResourceGroupName `
  -ZoneName $zoneName `
  -Name "myzonelink" `
  -VirtualNetworkId $virtualNetwork.Id  
 
$pe = Get-AzPrivateEndpoint -Name $PrivateEndpointName `
  -ResourceGroupName $ResourceGroupName

$networkInterface = Get-AzResource -ResourceId $pe.NetworkInterfaces[0].Id `
  -ApiVersion "2019-04-01"

# Create DNS configuration

$PrivateDnsZoneId = $zone.ResourceId

$config = New-AzPrivateDnsZoneConfig -Name $zoneName`
 -PrivateDnsZoneId $PrivateDnsZoneId

## Create a DNS zone group
New-AzPrivateDnsZoneGroup -ResourceGroupName $ResourceGroupName`
 -PrivateEndpointName $PrivateEndpointName`
 -Name "MyPrivateZoneGroup"`
 -PrivateDnsZoneConfig $config
```

### <a name="fetch-the-private-ip-addresses"></a>Pobierz prywatne adresy IP

Po aprowizacji prywatnego punktu końcowego można wysyłać zapytania dotyczące adresów IP i mapowania nazw FQDN przy użyciu następującego skryptu programu PowerShell:

```azurepowershell-interactive
$pe = Get-AzPrivateEndpoint -Name MyPrivateEndpoint -ResourceGroupName myResourceGroup
$networkInterface = Get-AzNetworkInterface -ResourceId $pe.NetworkInterfaces[0].Id
foreach ($IPConfiguration in $networkInterface.IpConfigurations)
{
    Write-Host $IPConfiguration.PrivateIpAddress ":" $IPConfiguration.PrivateLinkConnectionProperties.Fqdns
}
```

## <a name="create-a-private-endpoint-by-using-azure-cli"></a>Tworzenie prywatnego punktu końcowego za pomocą interfejsu wiersza polecenia platformy Azure

Uruchom następujący skrypt interfejsu wiersza polecenia platformy Azure, aby utworzyć prywatny punkt końcowy o nazwie "myPrivateEndpoint" dla istniejącego konta usługi Azure Cosmos. Zastąp wartości zmiennej szczegółowymi informacjami dla danego środowiska.

```azurecli-interactive
# Resource group where the Azure Cosmos account and virtual network resources are located
ResourceGroupName="myResourceGroup"

# Subscription ID where the Azure Cosmos account and virtual network resources are located
SubscriptionId="<your Azure subscription ID>"

# Name of the existing Azure Cosmos account
CosmosDbAccountName="mycosmosaccount"

# API type of your Azure Cosmos account: Sql, MongoDB, Cassandra, Gremlin, or Table
CosmosDbApiType="Sql"

# Name of the virtual network to create
VNetName="myVnet"

# Name of the subnet to create
SubnetName="mySubnet"

# Name of the private endpoint to create
PrivateEndpointName="myPrivateEndpoint"

# Name of the private endpoint connection to create
PrivateConnectionName="myConnection"

az network vnet create \
 --name $VNetName \
 --resource-group $ResourceGroupName \
 --subnet-name $SubnetName

az network vnet subnet update \
 --name $SubnetName \
 --resource-group $ResourceGroupName \
 --vnet-name $VNetName \
 --disable-private-endpoint-network-policies true

az network private-endpoint create \
    --name $PrivateEndpointName \
    --resource-group $ResourceGroupName \
    --vnet-name $VNetName  \
    --subnet $SubnetName \
    --private-connection-resource-id "/subscriptions/$SubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.DocumentDB/databaseAccounts/$CosmosDbAccountName" \
    --group-ids $CosmosDbApiType \
    --connection-name $PrivateConnectionName
```

### <a name="integrate-the-private-endpoint-with-a-private-dns-zone"></a>Integrowanie prywatnego punktu końcowego z prywatną strefą DNS

Po utworzeniu prywatnego punktu końcowego można zintegrować go z prywatną strefą DNS przy użyciu następującego skryptu interfejsu wiersza polecenia platformy Azure:

```azurecli-interactive
#Zone name differs based on the API type and group ID you are using. 
zoneName="privatelink.documents.azure.com"

az network private-dns zone create --resource-group $ResourceGroupName \
   --name  $zoneName

az network private-dns link vnet create --resource-group $ResourceGroupName \
   --zone-name  $zoneName\
   --name myzonelink \
   --virtual-network $VNetName \
   --registration-enabled false 

#Create a DNS zone group
az network private-endpoint dns-zone-group create \
   --resource-group $ResourceGroupName \
   --endpoint-name $PrivateEndpointName \
   --name "MyPrivateZoneGroup" \
   --private-dns-zone $zoneName \
   --zone-name "myzone"
```

## <a name="create-a-private-endpoint-by-using-a-resource-manager-template"></a>Tworzenie prywatnego punktu końcowego przy użyciu szablonu Menedżer zasobów

Połączenie prywatne można skonfigurować przez utworzenie prywatnego punktu końcowego w podsieci sieci wirtualnej. Można to osiągnąć przy użyciu szablonu Azure Resource Manager.

Użyj poniższego kodu, aby utworzyć szablon Menedżer zasobów o nazwie "PrivateEndpoint_template.json". Ten szablon służy do tworzenia prywatnego punktu końcowego dla istniejącego konta interfejsu API usługi Azure Cosmos w istniejącej sieci wirtualnej.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
          "type": "string",
          "defaultValue": "[resourceGroup().location]",
          "metadata": {
            "description": "Location for all resources."
          }
        },
        "privateEndpointName": {
            "type": "string"
        },
        "resourceId": {
            "type": "string"
        },
        "groupId": {
            "type": "string"
        },
        "subnetId": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[parameters('privateEndpointName')]",
            "type": "Microsoft.Network/privateEndpoints",
            "apiVersion": "2019-04-01",
            "location": "[parameters('location')]",
            "properties": {
                "subnet": {
                    "id": "[parameters('subnetId')]"
                },
                "privateLinkServiceConnections": [
                    {
                        "name": "MyConnection",
                        "properties": {
                            "privateLinkServiceId": "[parameters('resourceId')]",
                            "groupIds": [ "[parameters('groupId')]" ],
                            "requestMessage": ""
                        }
                    }
                ]
            }
        }
    ],
    "outputs": {
        "privateEndpointNetworkInterface": {
          "type": "string",
          "value": "[reference(concat('Microsoft.Network/privateEndpoints/', parameters('privateEndpointName'))).networkInterfaces[0].id]"
        }
    }
}
```

**Zdefiniuj plik parametrów dla szablonu**

Utwórz plik parametrów dla szablonu i nadaj mu nazwę "PrivateEndpoint_parameters.json". Dodaj następujący kod do pliku parametrów:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "privateEndpointName": {
            "value": ""
        },
        "resourceId": {
            "value": ""
        },
        "groupId": {
            "value": ""
        },
        "subnetId": {
            "value": ""
        }
    }
}
```

**Wdrażanie szablonu przy użyciu skryptu programu PowerShell**

Utwórz skrypt programu PowerShell przy użyciu następującego kodu. Przed uruchomieniem skryptu Zastąp Identyfikator subskrypcji, nazwę grupy zasobów i inne wartości zmiennych informacjami o danym środowisku.

```azurepowershell-interactive
### This script creates a private endpoint for an existing Azure Cosmos account in an existing virtual network

## Step 1: Fill in these details. Replace the variable values with the details for your environment.
$SubscriptionId = "<your Azure subscription ID>"
# Resource group where the Azure Cosmos account and virtual network resources are located
$ResourceGroupName = "myResourceGroup"
# Name of the Azure Cosmos account
$CosmosDbAccountName = "mycosmosaccount"
# API type of the Azure Cosmos account. It can be one of the following: "Sql", "MongoDB", "Cassandra", "Gremlin", "Table"
$CosmosDbApiType = "Sql"
# Name of the existing virtual network
$VNetName = "myVnet"
# Name of the target subnet in the virtual network
$SubnetName = "mySubnet"
# Name of the private endpoint to create
$PrivateEndpointName = "myPrivateEndpoint"

$cosmosDbResourceId = "/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.DocumentDB/databaseAccounts/$($CosmosDbAccountName)"
$VNetResourceId = "/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.Network/virtualNetworks/$($VNetName)"
$SubnetResourceId = "$($VNetResourceId)/subnets/$($SubnetName)"
$PrivateEndpointTemplateFilePath = "PrivateEndpoint_template.json"
$PrivateEndpointParametersFilePath = "PrivateEndpoint_parameters.json"

## Step 2: Sign in to your Azure account and select the target subscription.
Login-AzAccount
Select-AzSubscription -SubscriptionId $subscriptionId

## Step 3: Make sure private endpoint network policies are disabled in the subnet.
$VirtualNetwork= Get-AzVirtualNetwork -Name "$VNetName" -ResourceGroupName "$ResourceGroupName"
($virtualNetwork | Select -ExpandProperty subnets | Where-Object  {$_.Name -eq "$SubnetName"} ).PrivateEndpointNetworkPolicies = "Disabled"
$virtualNetwork | Set-AzVirtualNetwork

## Step 4: Create the private endpoint.
Write-Output "Deploying private endpoint on $($resourceGroupName)"
$deploymentOutput = New-AzResourceGroupDeployment -Name "PrivateCosmosDbEndpointDeployment" `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $PrivateEndpointTemplateFilePath `
    -TemplateParameterFile $PrivateEndpointParametersFilePath `
    -SubnetId $SubnetResourceId `
    -ResourceId $CosmosDbResourceId `
    -GroupId $CosmosDbApiType `
    -PrivateEndpointName $PrivateEndpointName

$deploymentOutput
```

W skrypcie programu PowerShell `GroupId` zmienna może zawierać tylko jedną wartość. Ta wartość jest typem interfejsu API konta. Dozwolone wartości to: `Sql` , `MongoDB` , `Cassandra` , `Gremlin` i `Table` . Niektóre typy kont usługi Azure Cosmos są dostępne za pomocą wielu interfejsów API. Na przykład:

* Do konta interfejsu API Gremlin można uzyskać dostęp zarówno z kont usługi Gremlin, jak i interfejsu API SQL.
* Do konta interfejs API tabel można uzyskać dostęp z poziomu kont interfejsu API zarówno w tabeli, jak i SQL.

Dla tych kont należy utworzyć jeden prywatny punkt końcowy dla każdego typu interfejsu API. Odpowiedni typ interfejsu API jest określony w `GroupId` tablicy.

Po pomyślnym wdrożeniu szablonu można zobaczyć dane wyjściowe podobne do poniższej ilustracji. `provisioningState`Wartość to `Succeeded` czy prywatne punkty końcowe są poprawnie skonfigurowane.

:::image type="content" source="./media/how-to-configure-private-endpoints/resource-manager-template-deployment-output.png" alt-text="Dane wyjściowe wdrożenia dla szablonu Menedżer zasobów":::

Po wdrożeniu szablonu prywatne adresy IP są zastrzeżone w podsieci. Reguła zapory dla konta usługi Azure Cosmos jest skonfigurowana do akceptowania połączeń tylko z prywatnego punktu końcowego.

### <a name="integrate-the-private-endpoint-with-a-private-dns-zone"></a>Integrowanie prywatnego punktu końcowego ze strefą Prywatna strefa DNS

Użyj poniższego kodu, aby utworzyć szablon Menedżer zasobów o nazwie "PrivateZone_template.json". Ten szablon tworzy prywatną strefę DNS dla istniejącego konta interfejsu API SQL Cosmos platformy Azure w istniejącej sieci wirtualnej.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "privateZoneName": {
            "type": "string"
        },
        "VNetId": {
            "type": "string"
        }        
    },
    "resources": [
        {
            "name": "[parameters('privateZoneName')]",
            "type": "Microsoft.Network/privateDnsZones",
            "apiVersion": "2018-09-01",
            "location": "global",
            "properties": {                
            }
        },
        {
            "type": "Microsoft.Network/privateDnsZones/virtualNetworkLinks",
            "apiVersion": "2018-09-01",
            "name": "[concat(parameters('privateZoneName'), '/myvnetlink')]",
            "location": "global",
            "dependsOn": [
                "[resourceId('Microsoft.Network/privateDnsZones', parameters('privateZoneName'))]"
            ],
            "properties": {
                "registrationEnabled": false,
                "virtualNetwork": {
                    "id": "[parameters('VNetId')]"
                }
            }
        }        
    ]
}
```

**Zdefiniuj plik parametrów dla szablonu**

Utwórz następujący dwa pliki parametrów dla szablonu. Utwórz "PrivateZone_parameters.json". przy użyciu następującego kodu:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "privateZoneName": {
            "value": ""
        },
        "VNetId": {
            "value": ""
        }
    }
}
```

Użyj poniższego kodu, aby utworzyć szablon Menedżer zasobów o nazwie "PrivateZoneGroup_template.json". Ten szablon tworzy prywatną grupę stref DNS dla istniejącego konta interfejsu API usługi Azure Cosmos w istniejącej sieci wirtualnej.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "privateZoneName": {
            "type": "string"
        },
        "PrivateEndpointDnsGroupName": {
            "value": "string"
        },
        "privateEndpointName":{
            "value": "string"
        }        
    },
    "resources": [
        {
            "type": "Microsoft.Network/privateEndpoints/privateDnsZoneGroups",
            "apiVersion": "2020-06-01",
            "name": "[parameters('PrivateEndpointDnsGroupName')]",
            "location": "global",
            "dependsOn": [
                "[resourceId('Microsoft.Network/privateDnsZones', parameters('privateZoneName'))]",
                "[variables('privateEndpointName')]"
            ],
          "properties": {
            "privateDnsZoneConfigs": [
              {
                "name": "config1",
                "properties": {
                  "privateDnsZoneId": "[resourceId('Microsoft.Network/privateDnsZones', parameters('privateZoneName'))]"
                }
              }
            ]
          }
        }
    ]
}
```

**Zdefiniuj plik parametrów dla szablonu**

Utwórz następujący dwa pliki parametrów dla szablonu. Utwórz "PrivateZoneGroup_parameters.json". przy użyciu następującego kodu:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "privateZoneName": {
            "value": ""
        },
        "PrivateEndpointDnsGroupName": {
            "value": ""
        },
        "privateEndpointName":{
            "value": ""
        }
    }
}
```

**Wdrażanie szablonu przy użyciu skryptu programu PowerShell**

Utwórz skrypt programu PowerShell przy użyciu następującego kodu. Przed uruchomieniem skryptu Zastąp Identyfikator subskrypcji, nazwę grupy zasobów i inne wartości zmiennych informacjami o danym środowisku.

```azurepowershell-interactive
### This script:
### - creates a private zone
### - creates a private endpoint for an existing Cosmos DB account in an existing VNet
### - maps the private endpoint to the private zone

## Step 1: Fill in these details. Replace the variable values with the details for your environment.
$SubscriptionId = "<your Azure subscription ID>"
# Resource group where the Azure Cosmos account and virtual network resources are located
$ResourceGroupName = "myResourceGroup"
# Name of the Azure Cosmos account
$CosmosDbAccountName = "mycosmosaccount"
# API type of the Azure Cosmos account. It can be one of the following: "Sql", "MongoDB", "Cassandra", "Gremlin", "Table"
$CosmosDbApiType = "Sql"
# Name of the existing virtual network
$VNetName = "myVnet"
# Name of the target subnet in the virtual network
$SubnetName = "mySubnet"
# Name of the private zone to create
$PrivateZoneName = "myPrivateZone.documents.azure.com"
# Name of the private endpoint to create
$PrivateEndpointName = "myPrivateEndpoint"

# Name of the DNS zone group to create
$PrivateEndpointDnsGroupName = "myPrivateDNSZoneGroup"

$cosmosDbResourceId = "/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.DocumentDB/databaseAccounts/$($CosmosDbAccountName)"
$VNetResourceId = "/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.Network/virtualNetworks/$($VNetName)"
$SubnetResourceId = "$($VNetResourceId)/subnets/$($SubnetName)"
$PrivateZoneTemplateFilePath = "PrivateZone_template.json"
$PrivateZoneParametersFilePath = "PrivateZone_parameters.json"
$PrivateEndpointTemplateFilePath = "PrivateEndpoint_template.json"
$PrivateEndpointParametersFilePath = "PrivateEndpoint_parameters.json"
$PrivateZoneGroupTemplateFilePath = "PrivateZoneGroup_template.json"
$PrivateZoneGroupParametersFilePath = "PrivateZoneGroup_parameters.json"

## Step 2: Login your Azure account and select the target subscription
Login-AzAccount 
Select-AzSubscription -SubscriptionId $subscriptionId

## Step 3: Make sure private endpoint network policies are disabled in the subnet
$VirtualNetwork= Get-AzVirtualNetwork -Name "$VNetName" -ResourceGroupName "$ResourceGroupName"
($virtualNetwork | Select -ExpandProperty subnets | Where-Object  {$_.Name -eq "$SubnetName"} ).PrivateEndpointNetworkPolicies = "Disabled"
$virtualNetwork | Set-AzVirtualNetwork

## Step 4: Create the private zone
New-AzResourceGroupDeployment -Name "PrivateZoneDeployment" `
    -ResourceGroupName $ResourceGroupName `
    -TemplateFile $PrivateZoneTemplateFilePath `
    -TemplateParameterFile $PrivateZoneParametersFilePath `
    -PrivateZoneName $PrivateZoneName `
    -VNetId $VNetResourceId

## Step 5: Create the private endpoint
Write-Output "Deploying private endpoint on $($resourceGroupName)"
$deploymentOutput = New-AzResourceGroupDeployment -Name "PrivateCosmosDbEndpointDeployment" `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $PrivateEndpointTemplateFilePath `
    -TemplateParameterFile $PrivateEndpointParametersFilePath `
    -SubnetId $SubnetResourceId `
    -ResourceId $CosmosDbResourceId `
    -GroupId $CosmosDbApiType `
    -PrivateEndpointName $PrivateEndpointName
$deploymentOutput

## Step 6: Create the private zone
New-AzResourceGroupDeployment -Name "PrivateZoneGroupDeployment" `
    -ResourceGroupName $ResourceGroupName `
    -TemplateFile $PrivateZoneGroupTemplateFilePath `
    -TemplateParameterFile $PrivateZoneGroupParametersFilePath `
    -PrivateZoneName $PrivateZoneName `
    -PrivateEndpointName $PrivateEndpointName`
    -PrivateEndpointDnsGroupName $PrivateEndpointDnsGroupName

```

## <a name="configure-custom-dns"></a>Konfigurowanie niestandardowego systemu DNS

Należy używać prywatnej strefy DNS w podsieci, w której utworzono prywatny punkt końcowy. Skonfiguruj punkty końcowe tak, aby każdy prywatny adres IP był mapowany na wpis DNS. (Zobacz `fqdns` Właściwość w odpowiedzi pokazanej wcześniej).

Podczas tworzenia prywatnego punktu końcowego można zintegrować go z prywatną strefą DNS na platformie Azure. Jeśli zdecydujesz się użyć niestandardową strefę DNS, musisz ją skonfigurować, aby dodać rekordy DNS dla wszystkich prywatnych adresów IP zarezerwowanych dla prywatnego punktu końcowego.

> [!IMPORTANT]
> Jest rozpoznawana przez system DNS żądania, które określają, czy te żądania przechodzą przez prywatne punkty końcowe, czy też korzystają ze standardowej trasy publicznej. Upewnij się, że lokalna usługa DNS poprawnie odwołuje się do prywatnego adresu IP zamapowanego przez prywatny punkt końcowy.

## <a name="private-link-combined-with-firewall-rules"></a>Link prywatny połączony z regułami zapory

W przypadku korzystania z prywatnego linku w połączeniu z regułami zapory są możliwe następujące sytuacje i wyniki:

* Jeśli nie skonfigurujesz żadnych reguł zapory, domyślnie cały ruch będzie miał dostęp do konta usługi Azure Cosmos.

* W przypadku skonfigurowania ruchu publicznego lub punktu końcowego usługi i utworzenia prywatnych punktów końcowych różne typy ruchu przychodzącego są autoryzowane przez odpowiedni typ reguły zapory. Jeśli prywatny punkt końcowy jest skonfigurowany w podsieci, w której skonfigurowano również punkt końcowy usługi:
  * ruch do konta bazy danych mapowanego przez prywatny punkt końcowy jest kierowany za pośrednictwem prywatnego punktu końcowego.
  * ruch do innych kont bazy danych z podsieci jest kierowany przez punkt końcowy usługi.

* Jeśli nie skonfigurujesz ruchu publicznego lub punktu końcowego usługi i utworzysz prywatne punkty końcowe, konto usługi Azure Cosmos będzie dostępne tylko za pomocą prywatnych punktów końcowych. Jeśli nie skonfigurujesz ruchu publicznego lub punktu końcowego usługi, po odrzuceniu lub usunięciu wszystkich zatwierdzonych prywatnych punktów końcowych konto jest otwarte dla całej sieci, chyba że PublicNetworkAccess ma wartość wyłączone (zobacz sekcję poniżej).

## <a name="blocking-public-network-access-during-account-creation"></a>Blokowanie dostępu do sieci publicznej podczas tworzenia konta

Zgodnie z opisem w poprzedniej sekcji i o ile nie zostały ustawione określone reguły zapory, dodanie prywatnego punktu końcowego sprawia, że konto platformy Azure Cosmos jest dostępne tylko za pomocą prywatnych punktów końcowych. Oznacza to, że można nawiązać połączenie z kontem usługi Azure Cosmos z ruchu publicznego po jego utworzeniu i przed dodaniem prywatnego punktu końcowego. Aby upewnić się, że dostęp do sieci publicznej jest wyłączony nawet przed utworzeniem prywatnych punktów końcowych, można ustawić `publicNetworkAccess` flagę na `Disabled` podczas tworzenia konta. Należy pamiętać, że ta flaga ma pierwszeństwo przed wszystkimi regułami adresów IP lub sieci wirtualnych; cały ruch w sieci publicznej i wirtualnej jest blokowany, gdy flaga jest ustawiona na `Disabled` , nawet jeśli źródłowy adres IP lub Sieć wirtualna jest dozwolona w konfiguracji zapory.

[Ten Azure Resource Manager szablon](https://azure.microsoft.com/resources/templates/101-cosmosdb-private-endpoint/) zawiera przykład przedstawiający sposób użycia tej flagi.

## <a name="adding-private-endpoints-to-an-existing-cosmos-account-with-no-downtime"></a>Dodawanie prywatnych punktów końcowych do istniejącego konta Cosmos bez przestojów

Domyślnie dodanie prywatnego punktu końcowego do istniejącego konta skutkuje krótkim przestojem wynoszącym około 5 minut. Postępuj zgodnie z poniższymi instrukcjami, aby uniknąć tego przestoju:

1. Dodaj reguły adresu IP lub sieci wirtualnej do konfiguracji zapory, aby jawnie zezwolić na połączenia klientów.
1. Poczekaj 10 minut, aby upewnić się, że aktualizacja konfiguracji została zastosowana.
1. Skonfiguruj nowy prywatny punkt końcowy.
1. Usuń reguły zapory ustawione w kroku 1.

## <a name="port-range-when-using-direct-mode"></a>Zakres portów w trybie bezpośrednim

Jeśli używasz prywatnego linku z kontem usługi Azure Cosmos za pośrednictwem połączenia trybu bezpośredniego, musisz upewnić się, że jest otwarty pełny zakres portów TCP (0-65535).

## <a name="update-a-private-endpoint-when-you-add-or-remove-a-region"></a>Aktualizowanie prywatnego punktu końcowego po dodaniu lub usunięciu regionu

Na przykład w przypadku wdrażania konta usługi Azure Cosmos w trzech regionach: "zachodnie stany USA", "środkowe stany USA" i "Europa Zachodnia". W przypadku tworzenia prywatnego punktu końcowego dla konta cztery prywatne adresy IP są zastrzeżone w podsieci. Istnieje jeden adres IP dla każdego z trzech regionów i istnieje jeden adres IP dla punktu końcowego Global/region-niezależny od. Później możesz dodać nowy region (na przykład "Wschodnie stany USA") do konta usługi Azure Cosmos. Prywatna strefa DNS jest aktualizowana w następujący sposób:

* **Jeśli jest używana grupa prywatnych stref DNS:**

  Jeśli używasz prywatnej grupy stref DNS, prywatna strefa DNS jest automatycznie aktualizowana podczas aktualizowania prywatnego punktu końcowego. W poprzednim przykładzie, po dodaniu nowego regionu, prywatna strefa DNS jest automatycznie aktualizowana.

* **Jeśli nie jest używana grupa prywatnych stref DNS:**

  Jeśli nie korzystasz z grupy prywatnych stref DNS, Dodawanie lub usuwanie regionów do konta usługi Azure Cosmos wymaga dodania lub usunięcia wpisów DNS dla tego konta. Po dodaniu lub usunięciu regionów można zaktualizować prywatną strefę DNS podsieci w celu odzwierciedlenia dodanych lub usuniętych wpisów DNS oraz odpowiednich prywatnych adresów IP.

  W poprzednim przykładzie po dodaniu nowego regionu należy dodać odpowiedni rekord DNS do prywatnej strefy DNS lub do niestandardowego serwera DNS. Po usunięciu regionu można użyć tych samych kroków. Po usunięciu regionu należy usunąć odpowiedni rekord DNS z prywatnej strefy DNS lub niestandardowego serwera DNS.

## <a name="current-limitations"></a>Bieżące ograniczenia

W przypadku korzystania z prywatnego linku do konta usługi Azure Cosmos stosowane są następujące ograniczenia:

* Na jednym koncie usługi Azure Cosmos nie można mieć więcej niż 200 prywatnych punktów końcowych.

* Jeśli używasz prywatnego linku z kontem usługi Azure Cosmos za pośrednictwem połączenia trybu bezpośredniego, możesz użyć tylko protokołu TCP. Protokół HTTP nie jest obecnie obsługiwany.

* W przypadku korzystania z interfejsu API Azure Cosmos DB dla kont MongoDB prywatny punkt końcowy jest obsługiwany tylko dla kont na serwerze w wersji 3,6 (to oznacza, że konta korzystają z punktu końcowego w formacie `*.mongo.cosmos.azure.com` ). Link prywatny nie jest obsługiwany dla kont na serwerze w wersji 3,2 (oznacza to, że konta korzystają z punktu końcowego w formacie `*.documents.azure.com` ). Aby użyć linku prywatnego, należy przeprowadzić migrację starych kont do nowej wersji.

* Jeśli używasz interfejsu API Azure Cosmos DB dla konta MongoDB, które ma link prywatny, narzędzia/biblioteki muszą obsługiwać identyfikatory nazw usług (SNI) lub przekazać `appName` parametr z parametrów połączenia, aby prawidłowo nawiązać połączenie. Niektóre starsze narzędzia/biblioteki mogą nie być zgodne, aby można było korzystać z funkcji Link prywatny.

* Administrator sieci powinien mieć co najmniej `Microsoft.DocumentDB/databaseAccounts/PrivateEndpointConnectionsApproval/action` uprawnienia w zakresie konta usługi Azure Cosmos w celu utworzenia automatycznie zatwierdzonych prywatnych punktów końcowych.

### <a name="limitations-to-private-dns-zone-integration"></a>Ograniczenia związane z integracją prywatnej strefy DNS

Jeśli nie korzystasz z grupy prywatnych stref DNS, rekordy DNS w prywatnej strefie DNS nie są usuwane automatycznie po usunięciu prywatnego punktu końcowego lub usunięciu regionu z konta usługi Azure Cosmos. Rekordy DNS należy usunąć ręcznie przed:

* Dodawanie nowego prywatnego punktu końcowego połączonego z tą prywatną strefą DNS.
* Dodawanie nowego regionu do dowolnego konta bazy danych z prywatnymi punktami końcowymi połączonymi z tą prywatną strefą DNS.

Jeśli rekordy DNS nie są czyszczone, mogą wystąpić nieoczekiwane problemy z płaszczyzną danych. Te problemy obejmują awarię danych do regionów dodanych po usunięciu prywatnego punktu końcowego lub usunięciu regionu.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat funkcji zabezpieczeń Azure Cosmos DB, zobacz następujące artykuły:

* Aby skonfigurować zaporę dla Azure Cosmos DB, zobacz [Obsługa zapory](how-to-configure-firewall.md).

* Aby dowiedzieć się, jak skonfigurować punkt końcowy usługi sieci wirtualnej dla konta usługi Azure Cosmos, zobacz [Konfigurowanie dostępu z sieci wirtualnych](how-to-configure-vnet-service-endpoint.md).

* Aby dowiedzieć się więcej na temat linku prywatnego, zapoznaj się z dokumentacją [prywatnego linku do platformy Azure](../private-link/private-link-overview.md) .
