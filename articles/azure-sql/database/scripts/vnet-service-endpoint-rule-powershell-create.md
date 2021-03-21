---
title: Program PowerShell dla punktów końcowych sieci wirtualnych i reguł dla baz danych z pojedynczym i pulą
description: Udostępnia skrypty programu PowerShell do tworzenia punktów końcowych usługi wirtualnych dla Azure SQL Database i usługi Azure Synapse oraz zarządzania nimi.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.devlang: PowerShell
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto
ms.date: 04/17/2019
ms.custom: sqldbrb=1
tags: azure-synapse
ms.openlocfilehash: 76a1d3aaadcbd1b15966a84f5dd2fe876f82c43a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102177627"
---
# <a name="powershell-create-a-virtual-service-endpoint-and-vnet-rule-for-azure-sql-database"></a>PowerShell: Tworzenie punktu końcowego usługi wirtualnej i reguły sieci wirtualnej dla Azure SQL Database
[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqldb.md)]

*Reguły sieci wirtualnej* to jedna funkcja zabezpieczeń zapory, która kontroluje, czy [serwer logiczny SQL](../logical-servers.md) dla baz danych [Azure SQL Database](../sql-database-paas-overview.md) , pul elastycznych lub baz danych w [usłudze Azure Synapse](../../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) akceptuje komunikację, która jest wysyłana z określonych podsieci w sieciach wirtualnych.

> [!IMPORTANT]
> Ten artykuł ma zastosowanie do Azure SQL Database, w tym Azure Synapse (dawniej SQL DW). Dla uproszczenia termin Azure SQL Database w tym artykule ma zastosowanie do baz danych należących do Azure SQL Database lub Azure Synapse. Ten artykuł *nie* dotyczy wystąpienia zarządzanego Azure SQL, ponieważ nie ma skojarzonego z nim punktu końcowego usługi.

W tym artykule przedstawiono skrypt programu PowerShell, który wykonuje następujące czynności:

1. Tworzy *punkt końcowy usługi wirtualnej* Microsoft Azure w podsieci.
2. Dodaje punkt końcowy do zapory serwera, aby utworzyć *regułę sieci wirtualnej*.

Aby uzyskać więcej informacji, zobacz [punkty końcowe usługi wirtualnej dla Azure SQL Database][sql-db-vnet-service-endpoint-rule-overview-735r].

> [!TIP]
> Jeśli wystarczy ocenić lub dodać *nazwę typu* punktu końcowego usługi wirtualnej dla Azure SQL Database do podsieci, możesz przejść do naszego bardziej [bezpośredniego skryptu programu PowerShell](#a-verify-subnet-is-endpoint-ps-100).

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

> [!IMPORTANT]
> Moduł Azure Resource Manager programu PowerShell jest nadal obsługiwany przez Azure SQL Database, ale wszystkie przyszłe Programowanie dla [ `Az.Sql` poleceń cmdlet](/powershell/module/az.sql). W przypadku starszego modułu zobacz [AzureRM. SQL](/powershell/module/AzureRM.Sql/). Argumenty poleceń polecenia AZ module i w modułach AzureRm są zasadniczo identyczne.

## <a name="major-cmdlets"></a>Główne polecenia cmdlet

W tym artykule wyróżnia się [polecenie cmdlet **New-AzSqlServerVirtualNetworkRule**](/powershell/module/az.sql/new-azsqlservervirtualnetworkrule) , które dodaje punkt końcowy podsieci do listy kontroli dostępu (ACL) serwera, tworząc regułę.

Na poniższej liście przedstawiono sekwencję innych *najważniejszych* poleceń cmdlet, które należy wykonać, aby przygotować się do wywołania polecenia **New-AzSqlServerVirtualNetworkRule**. W tym artykule te wywołania występują w [skrypcie 3 "reguła sieci wirtualnej"](#a-script-30):

1. [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig): tworzy obiekt podsieci.
2. Polecenie [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork): tworzy sieć wirtualną, podając ją w podsieci.
3. [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/Set-azVirtualNetworkSubnetConfig): przypisuje wirtualny punkt końcowy usługi do podsieci.
4. [Set-AzVirtualNetwork](/powershell/module/az.network/Set-azVirtualNetwork): utrzymuje aktualizacje dokonane w sieci wirtualnej.
5. Polecenie [New-AzSqlServerVirtualNetworkRule](/powershell/module/az.sql/new-azsqlservervirtualnetworkrule): gdy podsieć jest punktem końcowym, program dodaje podsieć jako regułę sieci wirtualnej do listy kontroli dostępu serwera programu.
   - To polecenie cmdlet oferuje parametr **-IgnoreMissingVNetServiceEndpoint**, zaczynając od modułu Azure RM PowerShell w wersji 5.1.1.

## <a name="prerequisites-for-running-powershell"></a>Wymagania wstępne dotyczące uruchamiania programu PowerShell

- Możesz już zalogować się do platformy Azure, na przykład za pomocą [Azure Portal][http-azure-portal-link-ref-477t].
- Można już uruchamiać skrypty programu PowerShell.

> [!NOTE]
> Upewnij się, że punkty końcowe usługi są włączone dla sieci wirtualnej/podsieci, która ma zostać dodana do serwera, w przeciwnym razie utworzenie reguły zapory sieci wirtualnej nie powiedzie się.

## <a name="one-script-divided-into-four-chunks"></a>Jeden skrypt podzielony na cztery fragmenty

Nasz Demonstracja skrypt programu PowerShell jest podzielony na sekwencję mniejszych skryptów. Dział ułatwia uczenie się i zapewnia elastyczność. Skrypty muszą być uruchamiane w ich wskazanej kolejności. Jeśli nie masz teraz czasu na uruchomienie skryptów, nasze rzeczywiste dane wyjściowe testu są wyświetlane po skrypcie 4.

<a name="a-script-10"></a>

### <a name="script-1-variables"></a>Skrypt 1: zmienne

Ten pierwszy skrypt programu PowerShell przypisuje wartości do zmiennych. Kolejne skrypty zależą od tych zmiennych.

> [!IMPORTANT]
> Przed uruchomieniem tego skryptu można edytować wartości, jeśli chcesz. Na przykład jeśli masz już grupę zasobów, możesz chcieć edytować nazwę grupy zasobów jako przypisaną wartość.
>
> Nazwa subskrypcji powinna być edytowana do skryptu.

### <a name="powershell-script-1-source-code"></a>Kod źródłowy skryptu programu PowerShell 1

```powershell
######### Script 1 ########################################
##   LOG into to your Azure account.                     ##
##   (Needed only one time per powershell.exe session.)  ##
###########################################################

$yesno = Read-Host 'Do you need to log into Azure (only one time per powershell.exe session)?  [yes/no]'
if ('yes' -eq $yesno) { Connect-AzAccount }

###########################################################
##  Assignments to variables used by the later scripts.  ##
###########################################################

# You can edit these values, if necessary.
$SubscriptionName = 'yourSubscriptionName'
Select-AzSubscription -SubscriptionName $SubscriptionName

$ResourceGroupName = 'RG-YourNameHere'
$Region = 'westcentralus'

$VNetName = 'myVNet'
$SubnetName = 'mySubnet'
$VNetAddressPrefix = '10.1.0.0/16'
$SubnetAddressPrefix = '10.1.1.0/24'
$VNetRuleName = 'myFirstVNetRule-ForAcl'

$SqlDbServerName = 'mysqldbserver-forvnet'
$SqlDbAdminLoginName = 'ServerAdmin'
$SqlDbAdminLoginPassword = 'ChangeYourAdminPassword1'

$ServiceEndpointTypeName_SqlDb = 'Microsoft.Sql'  # Official type name.

Write-Host 'Completed script 1, the "Variables".'
```

<a name="a-script-20"></a>

### <a name="script-2-prerequisites"></a>Skrypt 2: wymagania wstępne

Ten skrypt przygotowuje się do następnego skryptu, gdzie akcja punktu końcowego to. Ten skrypt tworzy następujące elementy wymienione na liście, ale tylko wtedy, gdy jeszcze nie istnieją. Możesz pominąć skrypt 2, jeśli masz pewność, że te elementy już istnieją:

- Grupa zasobów platformy Azure
- Logiczny serwer SQL

### <a name="powershell-script-2-source-code"></a>Kod źródłowy skryptu programu PowerShell 2

```powershell
######### Script 2 ########################################
##   Ensure your Resource Group already exists.          ##
###########################################################

Write-Host "Check whether your Resource Group already exists."

$gottenResourceGroup = $null
$gottenResourceGroup = Get-AzResourceGroup -Name $ResourceGroupName -ErrorAction SilentlyContinue

if ($null -eq $gottenResourceGroup) {
    Write-Host "Creating your missing Resource Group - $ResourceGroupName."
    New-AzResourceGroup -Name $ResourceGroupName -Location $Region
} else {
    Write-Host "Good, your Resource Group already exists - $ResourceGroupName."
}

$gottenResourceGroup = $null

###########################################################
## Ensure your server already exists. ##
###########################################################

Write-Host "Check whether your server already exists."

$sqlDbServer = $null
$azSqlParams = @{
    ResourceGroupName = $ResourceGroupName
    ServerName        = $SqlDbServerName
    ErrorAction       = 'SilentlyContinue'
}
$sqlDbServer = Get-AzSqlServer @azSqlParams

if ($null -eq $sqlDbServer) {
    Write-Host "Creating the missing server - $SqlDbServerName."
    Write-Host "Gather the credentials necessary to next create a server."

    $sqlAdministratorCredentials = [pscredential]::new($SqlDbAdminLoginName,(ConvertTo-SecureString -String $SqlDbAdminLoginPassword -AsPlainText -Force))

    if ($null -eq $sqlAdministratorCredentials) {
        Write-Host "ERROR, unable to create SQL administrator credentials.  Now ending."
        return
    }

    Write-Host "Create your server."

    $sqlSrvParams = @{
        ResourceGroupName           = $ResourceGroupName
        ServerName                  = $SqlDbServerName
        Location                    = $Region
        SqlAdministratorCredentials = $sqlAdministratorCredentials
    }
    New-AzSqlServer @sqlSrvParams
} else {
    Write-Host "Good, your server already exists - $SqlDbServerName."
}

$sqlAdministratorCredentials = $null
$sqlDbServer = $null

Write-Host 'Completed script 2, the "Prerequisites".'
```

<a name="a-script-30"></a>

## <a name="script-3-create-an-endpoint-and-a-rule"></a>Skrypt 3: Tworzenie punktu końcowego i reguły

Ten skrypt tworzy sieć wirtualną z podsiecią. Następnie skrypt przypisuje typ punktu końcowego **Microsoft. SQL** do podsieci. Na koniec skrypt dodaje do listy kontroli dostępu (ACL) podsieć, a tym samym tworzy regułę.

### <a name="powershell-script-3-source-code"></a>Kod źródłowy skryptu programu PowerShell 3

```powershell
######### Script 3 ########################################
##   Create your virtual network, and give it a subnet.  ##
###########################################################

Write-Host "Define a subnet '$SubnetName', to be given soon to a virtual network."

$subnetParams = @{
    Name            = $SubnetName
    AddressPrefix   = $SubnetAddressPrefix
    ServiceEndpoint = $ServiceEndpointTypeName_SqlDb
}
$subnet = New-AzVirtualNetworkSubnetConfig @subnetParams

Write-Host "Create a virtual network '$VNetName'.`nGive the subnet to the virtual network that we created."

$vnetParams = @{
    Name              = $VNetName
    AddressPrefix     = $VNetAddressPrefix
    Subnet            = $subnet
    ResourceGroupName = $ResourceGroupName
    Location          = $Region
}
$vnet = New-AzVirtualNetwork @vnetParams

###########################################################
##   Create a Virtual Service endpoint on the subnet.    ##
###########################################################

Write-Host "Assign a Virtual Service endpoint 'Microsoft.Sql' to the subnet."

$vnetSubParams = @{
    Name            = $SubnetName
    AddressPrefix   = $SubnetAddressPrefix
    VirtualNetwork  = $vnet
    ServiceEndpoint = $ServiceEndpointTypeName_SqlDb
}
$vnet = Set-AzVirtualNetworkSubnetConfig @vnetSubParams

Write-Host "Persist the updates made to the virtual network > subnet."

$vnet = Set-AzVirtualNetwork -VirtualNetwork $vnet

$vnet.Subnets[0].ServiceEndpoints  # Display the first endpoint.

###########################################################
##   Add the Virtual Service endpoint Id as a rule,      ##
##   into SQL Database ACLs.                             ##
###########################################################

Write-Host "Get the subnet object."

$vnet = Get-AzVirtualNetwork -ResourceGroupName $ResourceGroupName -Name $VNetName

$subnet = Get-AzVirtualNetworkSubnetConfig -Name $SubnetName -VirtualNetwork $vnet

Write-Host "Add the subnet .Id as a rule, into the ACLs for your server."

$ruleParams = @{
    ResourceGroupName      = $ResourceGroupName
    ServerName             = $SqlDbServerName
    VirtualNetworkRuleName = $VNetRuleName
    VirtualNetworkSubnetId = $subnet.Id
}
New-AzSqlServerVirtualNetworkRule @ruleParams 

Write-Host "Verify that the rule is in the SQL Database ACL."

$rule2Params = @{
    ResourceGroupName      = $ResourceGroupName
    ServerName             = $SqlDbServerName
    VirtualNetworkRuleName = $VNetRuleName
}
Get-AzSqlServerVirtualNetworkRule @rule2Params

Write-Host 'Completed script 3, the "Virtual-Network-Rule".'
```

<a name="a-script-40"></a>

## <a name="script-4-clean-up"></a>Skrypt 4: oczyszczanie

Ten końcowy skrypt usuwa zasoby, które zostały utworzone przez poprzednie skrypty dla demonstracji. Jednak skrypt prosi o potwierdzenie przed usunięciem następujących danych:

- Logiczny serwer SQL
- Grupa zasobów platformy Azure

Skrypt 4 można uruchomić w dowolnym momencie po zakończeniu działania skryptu 1.

### <a name="powershell-script-4-source-code"></a>Kod źródłowy skryptu programu PowerShell 4

```powershell
######### Script 4 ########################################
##   Clean-up phase A:  Unconditional deletes.           ##
##                                                       ##
##   1. The test rule is deleted from SQL Database ACL.        ##
##   2. The test endpoint is deleted from the subnet.    ##
##   3. The test virtual network is deleted.             ##
###########################################################

Write-Host "Delete the rule from the SQL Database ACL."

$removeParams = @{
    ResourceGroupName      = $ResourceGroupName
    ServerName             = $SqlDbServerName
    VirtualNetworkRuleName = $VNetRuleName
    ErrorAction            = 'SilentlyContinue'
}
Remove-AzSqlServerVirtualNetworkRule @removeParams

Write-Host "Delete the endpoint from the subnet."

$vnet = Get-AzVirtualNetwork -ResourceGroupName $ResourceGroupName -Name $VNetName

Remove-AzVirtualNetworkSubnetConfig -Name $SubnetName -VirtualNetwork $vnet

Write-Host "Delete the virtual network (thus also deletes the subnet)."

$removeParams = @{
    Name              = $VNetName
    ResourceGroupName = $ResourceGroupName
    ErrorAction       = 'SilentlyContinue'
}
Remove-AzVirtualNetwork @removeParams

###########################################################
##   Clean-up phase B:  Conditional deletes.             ##
##                                                       ##
##   These might have already existed, so user might     ##
##   want to keep.                                       ##
##                                                       ##
##   1. Logical SQL server                        ##
##   2. Azure resource group                             ##
###########################################################

$yesno = Read-Host 'CAUTION !: Do you want to DELETE your server AND your resource group?  [yes/no]'
if ('yes' -eq $yesno) {
    Write-Host "Remove the server."

    $removeParams = @{
        ServerName        = $SqlDbServerName
        ResourceGroupName = $ResourceGroupName
        ErrorAction       = 'SilentlyContinue'
    }
    Remove-AzSqlServer @removeParams

    Write-Host "Remove the Azure Resource Group."
    
    Remove-AzResourceGroup -Name $ResourceGroupName -ErrorAction SilentlyContinue
} else {
    Write-Host "Skipped over the DELETE of SQL Database and resource group."
}

Write-Host 'Completed script 4, the "Clean-Up".'
```

<a name="a-actual-output"></a>

<a name="a-verify-subnet-is-endpoint-ps-100"></a>

## <a name="verify-your-subnet-is-an-endpoint"></a>Sprawdź, czy podsieć jest punktem końcowym

Być może istnieje podsieć, która ma już przypisaną nazwę typu **Microsoft. SQL** , co oznacza, że jest już punktem końcowym usługi wirtualnej. [Azure Portal][http-azure-portal-link-ref-477t] można użyć do utworzenia reguły sieci wirtualnej z punktu końcowego.

Możesz też nie upewnić się, czy podsieć ma nazwę typu **Microsoft. SQL** . Aby wykonać następujące działania, można uruchomić następujący skrypt programu PowerShell:

1. Sprawdź, czy podsieć ma nazwę typu **Microsoft. SQL** .
2. Opcjonalnie można przypisać nazwę typu, jeśli jest nieobecny.
    - Skrypt prosi o *potwierdzenie* przed zastosowaniem nieobecnej nazwy typu.

### <a name="phases-of-the-script"></a>Fazy skryptu

Poniżej przedstawiono etapy skryptu programu PowerShell:

1. Zaloguj się do konta platformy Azure, które jest konieczne tylko raz na sesję PS.  Przypisz zmienne.
2. Wyszukaj sieć wirtualną, a następnie dla podsieci.
3. Czy podsieć jest oznaczona jako typ **Microsoft. SQL** Endpoint Server?
4. Dodaj punkt końcowy usługi wirtualnej o nazwie **Microsoft. SQL** w podsieci.

> [!IMPORTANT]
> Przed uruchomieniem tego skryptu należy edytować wartości przypisane do zmiennych $, w górnej części skryptu.

### <a name="direct-powershell-source-code"></a>Bezpośredni kod źródłowy programu PowerShell

Ten skrypt programu PowerShell nie aktualizuje niczego, chyba że odpowie tak, jeśli zostanie wyświetlony monit o potwierdzenie. Skrypt może dodać nazwę typu **Microsoft. SQL** do podsieci. Jednak skrypt próbuje dodać tylko wtedy, gdy podsieć nie ma nazwy typu.

```powershell
### 1. LOG into to your Azure account, needed only once per PS session.  Assign variables.
$yesno = Read-Host 'Do you need to log into Azure (only one time per powershell.exe session)?  [yes/no]'
if ('yes' -eq $yesno) { Connect-AzAccount }

# Assignments to variables used by the later scripts.
# You can EDIT these values, if necessary.

$SubscriptionName = 'yourSubscriptionName'
Select-AzSubscription -SubscriptionName "$SubscriptionName"

$ResourceGroupName = 'yourRGName'
$VNetName = 'yourVNetName'
$SubnetName = 'yourSubnetName'
$SubnetAddressPrefix = 'Obtain this value from the Azure portal.' # Looks roughly like: '10.0.0.0/24'

$ServiceEndpointTypeName_SqlDb = 'Microsoft.Sql'  # Do NOT edit. Is official value.

### 2. Search for your virtual network, and then for your subnet.
# Search for the virtual network.
$vnet = $null
$vnet = Get-AzVirtualNetwork -ResourceGroupName $ResourceGroupName -Name $VNetName

if ($vnet -eq $null) {
    Write-Host "Caution: No virtual network found by the name '$VNetName'."
    return
}

$subnet = $null
for ($nn = 0; $nn -lt $vnet.Subnets.Count; $nn++) {
    $subnet = $vnet.Subnets[$nn]
    if ($subnet.Name -eq $SubnetName) { break }
    $subnet = $null
}

if ($null -eq $subnet) {
    Write-Host "Caution: No subnet found by the name '$SubnetName'"
    Return
}

### 3. Is your subnet tagged as 'Microsoft.Sql' endpoint server type?
$endpointMsSql = $null
for ($nn = 0; $nn -lt $subnet.ServiceEndpoints.Count; $nn++) {
    $endpointMsSql = $subnet.ServiceEndpoints[$nn]
    if ($endpointMsSql.Service -eq $ServiceEndpointTypeName_SqlDb) {
        $endpointMsSql
        break
    }
    $endpointMsSql = $null
}

if ($null -eq $endpointMsSql) {
    Write-Host "Good: Subnet found, and is already tagged as an endpoint of type '$ServiceEndpointTypeName_SqlDb'."
    return
} else {
    Write-Host "Caution: Subnet found, but not yet tagged as an endpoint of type '$ServiceEndpointTypeName_SqlDb'."

    # Ask the user for confirmation.
    $yesno = Read-Host 'Do you want the PS script to apply the endpoint type name to your subnet?  [yes/no]'
    if ('no' -eq $yesno) { return }
}

### 4. Add a Virtual Service endpoint of type name 'Microsoft.Sql', on your subnet.
$setParams = @{
    Name            = $SubnetName
    AddressPrefix   = $SubnetAddressPrefix
    VirtualNetwork  = $vnet
    ServiceEndpoint = $ServiceEndpointTypeName_SqlDb
}
$vnet = Set-AzVirtualNetworkSubnetConfig @setParams

# Persist the subnet update.
$vnet = Set-AzVirtualNetwork -VirtualNetwork $vnet

for ($nn = 0; $nn -lt $vnet.Subnets.Count; $nn++) {
    $vnet.Subnets[0].ServiceEndpoints # Display.
}
```

<!-- Link references: -->
[sql-db-vnet-service-endpoint-rule-overview-735r]:../vnet-service-endpoint-rule-overview.md
[http-azure-portal-link-ref-477t]: https://portal.azure.com/
