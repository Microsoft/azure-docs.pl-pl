---
title: Ograniczanie eksfiltracji danych do usługi Azure Storage — Azure PowerShell
description: W tym artykule dowiesz się, jak ograniczyć i ograniczyć eksfiltracji danych sieci wirtualnej do zasobów usługi Azure Storage za pomocą zasad punktu końcowego usługi sieci wirtualnej przy użyciu Azure PowerShell.
services: virtual-network
documentationcenter: virtual-network
author: RDhillon
manager: narayan
editor: ''
tags: azure-resource-manager
Customer intent: I want only resources in a virtual network subnet to access an Azure PaaS resource, such as an Azure Storage account.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: rdhillon
ms.custom: ''
ms.openlocfilehash: d8e300c9be8f3e59dc9443bf1f1806e4228992ad
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98790397"
---
# <a name="manage-data-exfiltration-to-azure-storage-accounts-with-virtual-network-service-endpoint-policies-using-azure-powershell"></a>Zarządzanie eksfiltracjiami danych na kontach usługi Azure Storage za pomocą zasad punktu końcowego usług sieci wirtualnej przy użyciu Azure PowerShell

Zasady punktu końcowego usługi sieci wirtualnej umożliwiają stosowanie kontroli dostępu do kont usługi Azure Storage z poziomu sieci wirtualnej za pośrednictwem punktów końcowych usługi. Jest to klucz służący do zabezpieczania obciążeń, zarządzania tym, jakie konta magazynu są dozwolone i gdzie eksfiltracji danych jest dozwolony.
W tym artykule omówiono sposób wykonywania następujących zadań:

* Utwórz sieć wirtualną.
* Dodaj podsieć i Włącz punkt końcowy usługi dla usługi Azure Storage.
* Utwórz dwa konta usługi Azure Storage i zezwól na dostęp sieciowy do niego z podsieci utworzonej powyżej.
* Utwórz zasady punktu końcowego usługi, aby zezwolić na dostęp tylko do jednego z kont magazynu.
* Wdróż maszynę wirtualną w podsieci.
* Potwierdź dostęp do dozwolonego konta magazynu z podsieci.
* Upewnij się, że dostęp do konta magazynu niedozwolonego z podsieci zostanie odrzucony.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować program PowerShell i używać go lokalnie, ten artykuł będzie wymagał modułu Azure PowerShell w wersji 1.0.0 lub nowszej. Uruchom polecenie `Get-Module -ListAvailable Az`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzAccount`, aby utworzyć połączenie z platformą Azure.

## <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej

Przed utworzeniem sieci wirtualnej należy utworzyć grupę zasobów dla sieci wirtualnej i wszystkie inne zasoby utworzone w tym artykule. Utwórz grupę zasobów za pomocą polecenia [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Poniższy przykład tworzy grupę zasobów o nazwie Moja *resourceName*: 

```azurepowershell-interactive
New-AzResourceGroup `
  -ResourceGroupName myResourceGroup `
  -Location EastUS
```

Utwórz sieć wirtualną przy użyciu polecenia [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). Poniższy przykład tworzy sieć wirtualną o nazwie *myVirtualNetwork* z prefiksem adresu *10.0.0.0/16*.

```azurepowershell-interactive
$virtualNetwork = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

## <a name="enable-a-service-endpoint"></a>Włączanie punktu końcowego usługi

Utwórz podsieć w sieci wirtualnej. W tym przykładzie podsieć o nazwie *Private* została utworzona z punktem końcowym usługi *Microsoft. Storage*: 

```azurepowershell-interactive
$subnetConfigPrivate = Add-AzVirtualNetworkSubnetConfig `
  -Name Private `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork `
  -ServiceEndpoint Microsoft.Storage

$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="restrict-network-access-for-the-subnet"></a>Ogranicz dostęp do sieci dla podsieci

Utwórz reguły zabezpieczeń sieciowej grupy zabezpieczeń w programie [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig). Poniższa reguła umożliwia dostęp wychodzący do publicznych adresów IP przypisanych do usługi Azure Storage: 

```azurepowershell-interactive
$rule1 = New-AzNetworkSecurityRuleConfig `
  -Name Allow-Storage-All `
  -Access Allow `
  -DestinationAddressPrefix Storage `
  -DestinationPortRange * `
  -Direction Outbound `
  -Priority 100 -Protocol * `
  -SourceAddressPrefix VirtualNetwork `
  -SourcePortRange *
```

Następująca reguła uniemożliwia dostęp do wszystkich publicznych adresów IP. Poprzednia reguła zastępuje tę regułę z powodu wyższego priorytetu, co umożliwia dostęp do publicznych adresów IP usługi Azure Storage.

```azurepowershell-interactive
$rule2 = New-AzNetworkSecurityRuleConfig `
  -Name Deny-Internet-All `
  -Access Deny `
  -DestinationAddressPrefix Internet `
  -DestinationPortRange * `
  -Direction Outbound `
  -Priority 110 -Protocol * `
  -SourceAddressPrefix VirtualNetwork `
  -SourcePortRange *
```

Poniższa reguła umożliwia ruch Remote Desktop Protocol (RDP) przychodzący do podsieci z dowolnego miejsca. Połączenia pulpitu zdalnego są dozwolone dla podsieci, dzięki czemu można potwierdzić dostęp sieciowy do zasobu w późniejszym kroku.

```azurepowershell-interactive
$rule3 = New-AzNetworkSecurityRuleConfig `
  -Name Allow-RDP-All `
  -Access Allow `
  -DestinationAddressPrefix VirtualNetwork `
  -DestinationPortRange 3389 `
  -Direction Inbound `
  -Priority 120 `
  -Protocol * `
  -SourceAddressPrefix * `
  -SourcePortRange *
```

Utwórz sieciową grupę zabezpieczeń przy użyciu polecenia [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup). Poniższy przykład tworzy sieciową grupę zabezpieczeń o nazwie *myNsgPrivate*.

```azurepowershell-interactive
$nsg = New-AzNetworkSecurityGroup `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myNsgPrivate `
  -SecurityRules $rule1,$rule2,$rule3
```

Skojarz sieciową grupę zabezpieczeń z *podsiecią* [AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) , a następnie Zapisz konfigurację podsieci w sieci wirtualnej. Poniższy przykład kojarzy sieciową grupę zabezpieczeń *myNsgPrivate* z podsiecią *prywatną* :

```azurepowershell-interactive
Set-AzVirtualNetworkSubnetConfig `
  -VirtualNetwork $VirtualNetwork `
  -Name Private `
  -AddressPrefix 10.0.0.0/24 `
  -ServiceEndpoint Microsoft.Storage `
  -NetworkSecurityGroup $nsg

$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="restrict-network-access-to-azure-storage-accounts"></a>Ograniczanie dostępu sieciowego do kont usługi Azure Storage

Kroki niezbędne do ograniczenia dostępu sieciowego do zasobów utworzonych za pomocą usług platformy Azure obsługujących punkty końcowe usługi różnią się w zależności od usługi. Zobacz dokumentację poszczególnych usług, aby poznać konkretne kroki dla każdej usługi. Pozostała część tego artykułu zawiera procedurę ograniczenia dostępu do sieci dla konta usługi Azure Storage.

### <a name="create-two-storage-accounts"></a>Tworzenie dwóch kont magazynu

Utwórz konto usługi Azure Storage za pomocą elementu [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount).

```azurepowershell-interactive
$storageAcctName1 = 'allowedaccount'

New-AzStorageAccount `
  -Location EastUS `
  -Name $storageAcctName1 `
  -ResourceGroupName myResourceGroup `
  -SkuName Standard_LRS `
  -Kind StorageV2
```

Po utworzeniu konta magazynu Pobierz klucz dla konta magazynu do zmiennej za pomocą [Get-AzStorageAccountKey](/powershell/module/az.storage/get-azstorageaccountkey):

```azurepowershell-interactive
$storageAcctKey1 = (Get-AzStorageAccountKey -ResourceGroupName myResourceGroup -AccountName $storageAcctName1).Value[0]
```

Ten klucz jest używany do tworzenia udziału plików w późniejszym kroku. Wprowadź `$storageAcctKey` i zanotuj wartość, ponieważ należy również ręcznie wprowadzić ją w późniejszym kroku podczas mapowania udziału plików na dysk na maszynie wirtualnej.

Teraz Powtórz powyższe kroki, aby utworzyć drugie konto magazynu.

```azurepowershell-interactive
$storageAcctName2 = 'notallowedaccount'

New-AzStorageAccount `
  -Location EastUS `
  -Name $storageAcctName2 `
  -ResourceGroupName myResourceGroup `
  -SkuName Standard_LRS `
  -Kind StorageV2
```

Pobierz również klucz konta magazynu z tego konta, aby użyć go później do utworzenia udziału plików.

```azurepowershell-interactive
$storageAcctKey2 = (Get-AzStorageAccountKey -ResourceGroupName myResourceGroup -AccountName $storageAcctName2).Value[0]
```

### <a name="create-a-file-share-in-each-of-the-storage-account"></a>Utwórz udział plików na każdym koncie magazynu

Utwórz kontekst konta magazynu i klucz przy użyciu funkcji [New-AzStorageContext](/powershell/module/az.storage/new-AzStoragecontext). Kontekst hermetyzuje nazwę konta magazynu i klucz konta:

```azurepowershell-interactive
$storageContext1 = New-AzStorageContext $storageAcctName1 $storageAcctKey1

$storageContext2 = New-AzStorageContext $storageAcctName2 $storageAcctKey2
```

Utwórz udział plików przy użyciu elementu [New-AzStorageShare](/powershell/module/az.storage/new-azstorageshare):

```azurepowershell-interactive
$share1 = New-AzStorageShare my-file-share -Context $storageContext1

$share2 = New-AzStorageShare my-file-share -Context $storageContext2
```

### <a name="deny-all-network-access-to-a-storage-accounts"></a>Odmowa dostępu przez sieć do kont magazynu

Domyślnie konta magazynu akceptują połączenia sieciowe od klientów w dowolnej sieci. Aby ograniczyć dostęp do wybranych sieci, Zmień domyślną akcję na *Odmów* z poleceniem [Update-AzStorageAccountNetworkRuleSet](/powershell/module/az.storage/update-azstorageaccountnetworkruleset). Kiedy dostęp sieciowy jest blokowany, konto magazynu nie jest dostępne z żadnej sieci.

```azurepowershell-interactive
Update-AzStorageAccountNetworkRuleSet `
  -ResourceGroupName myresourcegroup `
  -Name $storageAcctName1 `
  -DefaultAction Deny

Update-AzStorageAccountNetworkRuleSet  `
  -ResourceGroupName myresourcegroup `
  -Name $storageAcctName2 `
  -DefaultAction Deny
```

### <a name="enable-network-access-only-from-the-vnet-subnet"></a>Włącz dostęp do sieci tylko z podsieci sieci wirtualnej

Pobierz utworzoną sieć wirtualną za pomocą [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) , a następnie Pobierz obiekt podsieci prywatnej do zmiennej z poleceniem [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig):

```azurepowershell-interactive
$privateSubnet = Get-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Name myVirtualNetwork `
  | Get-AzVirtualNetworkSubnetConfig -Name Private
```

Zezwalaj na dostęp sieciowy do konta magazynu z podsieci *prywatnej* z [dodatkiem Add-AzStorageAccountNetworkRule](/powershell/module/az.network/add-aznetworksecurityruleconfig).

```azurepowershell-interactive
Add-AzStorageAccountNetworkRule `
  -ResourceGroupName myresourcegroup `
  -Name $storageAcctName1 `
  -VirtualNetworkResourceId $privateSubnet.Id

Add-AzStorageAccountNetworkRule `
  -ResourceGroupName myresourcegroup `
  -Name $storageAcctName2 `
  -VirtualNetworkResourceId $privateSubnet.Id
```

## <a name="apply-policy-to-allow-access-to-valid-storage-account"></a>Zastosuj zasady, aby zezwolić na dostęp do prawidłowego konta magazynu

Aby upewnić się, że użytkownicy w sieci wirtualnej mogą uzyskiwać dostęp tylko do kont usługi Azure Storage, które są bezpieczne i dozwolone, można utworzyć zasady punktu końcowego usługi z listą dozwolonych kont magazynu w definicji. Te zasady są następnie stosowane do podsieci sieci wirtualnej, która jest połączona z magazynem za pośrednictwem punktów końcowych usługi.

### <a name="create-a-service-endpoint-policy"></a>Tworzenie zasad punktu końcowego usługi

Ta sekcja służy do tworzenia definicji zasad z listą dozwolonych zasobów na potrzeby dostępu przez punkt końcowy usługi

Pobierz identyfikator zasobu dla pierwszego (dozwolonego) konta magazynu 

```azurepowershell-interactive
$resourceId = (Get-AzStorageAccount -ResourceGroupName myresourcegroup -Name $storageAcctName1).id
```

Utwórz definicję zasad, aby zezwolić na powyższy zasób

```azurepowershell-interactive
$policyDefinition = New-AzServiceEndpointPolicyDefinition -Name mypolicydefinition `
  -Description "Service Endpoint Policy Definition" `
  -Service "Microsoft.Storage" `
  -ServiceResource $resourceId
```

Tworzenie zasad punktu końcowego usługi przy użyciu definicji zasad utworzonej powyżej

```azurepowershell-interactive
$sepolicy = New-AzServiceEndpointPolicy -ResourceGroupName myresourcegroup `
  -Name mysepolicy -Location EastUS
  -ServiceEndpointPolicyDefinition $policyDefinition
```

### <a name="associate-the-service-endpoint-policy-to-the-virtual-network-subnet"></a>Skojarz zasady punktu końcowego usługi z podsiecią sieci wirtualnej

Po utworzeniu zasad punktu końcowego usługi należy skojarzyć ją z podsiecią docelową z konfiguracją punktu końcowego usługi Azure Storage.

```azurepowershell-interactive
Set-AzVirtualNetworkSubnetConfig -VirtualNetwork $VirtualNetwork `
  -Name Private `
  -AddressPrefix 10.0.0.0/24 `
  -NetworkSecurityGroup $nsg `
  -ServiceEndpoint Microsoft.Storage `
  -ServiceEndpointPolicy $sepolicy

$virtualNetwork | Set-AzVirtualNetwork
```
## <a name="validate-access-restriction-to-azure-storage-accounts"></a>Sprawdzanie poprawności ograniczenia dostępu do kont usługi Azure Storage

### <a name="deploy-the-virtual-machine"></a>Wdrażanie maszyny wirtualnej

Aby przetestować dostęp sieciowy do konta magazynu, Wdróż maszynę wirtualną w podsieci.

Utwórz maszynę wirtualną w podsieci *prywatnej* przy użyciu elementu [New-AzVM](/powershell/module/az.compute/new-azvm). Podczas wykonywania poniższego polecenia jest wyświetlany monit o poświadczenia. Wprowadzane wartości są konfigurowane jako nazwa użytkownika i hasło dla maszyny wirtualnej. Opcja `-AsJob` tworzy maszynę wirtualną w tle, dzięki czemu można przejść do następnego kroku.

```azurepowershell-interactive
New-AzVm -ResourceGroupName myresourcegroup `
  -Location "East US" `
  -VirtualNetworkName myVirtualNetwork `
  -SubnetName Private `
  -Name "myVMPrivate" -AsJob
```

Zwracane są dane wyjściowe podobne do następujących przykładowych danych wyjściowych:

```powershell
Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
--     ----            -------------   -----         -----------     --------             -------
1      Long Running... AzureLongRun... Running       True            localhost            New-AzVM
```

### <a name="confirm-access-to-the-allowed-storage-account"></a>Potwierdź dostęp do *dozwolonego* konta magazynu

Użyj polecenia [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress), aby uzyskać publiczny adres IP maszyny wirtualnej. Poniższy przykład zwraca publiczny adres IP maszyny wirtualnej *myVmPrivate* :

```azurepowershell-interactive
Get-AzPublicIpAddress `
  -Name myVmPrivate `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

W poniższym poleceniu zastąp ciąg `<publicIpAddress>` publicznym adresem IP zwróconym w poprzednim poleceniu, a następnie wprowadź następujące polecenie:

```powershell
mstsc /v:<publicIpAddress>
```

Zostanie utworzony i pobrany na komputer plik Remote Desktop Protocol (rdp). Otwórz pobrany plik rdp. Po wyświetleniu monitu wybierz pozycję **Połącz**. Wprowadź nazwę użytkownika i hasło określone podczas tworzenia maszyny wirtualnej. Może okazać się konieczne wybranie pozycji **Więcej opcji**, a następnie pozycji **Użyj innego konta**, aby określić poświadczenia wprowadzone podczas tworzenia maszyny wirtualnej. Wybierz pozycję **OK**. Podczas procesu logowania może pojawić się ostrzeżenie o certyfikacie. Jeśli zostanie wyświetlone ostrzeżenie, wybierz pozycję **Tak** lub **Kontynuuj**, aby nawiązać połączenie.

Na maszynie wirtualnej *myVmPrivate* zmapuj udział plików platformy Azure z dozwolonego konta magazynu na dysk z przy użyciu programu PowerShell. 

```powershell
$acctKey = ConvertTo-SecureString -String $storageAcctKey1 -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential -ArgumentList ("Azure\allowedaccount"), $acctKey
New-PSDrive -Name Z -PSProvider FileSystem -Root "\\allowedaccount.file.core.windows.net\my-file-share" -Credential $credential
```

Program PowerShell zwraca dane wyjściowe podobne do następujących przykładowych danych wyjściowych:

```powershell
Name           Used (GB)     Free (GB) Provider      Root
----           ---------     --------- --------      ----
Z                                      FileSystem    \\allowedaccount.file.core.windows.net\my-f...
```

Udział plików platformy Azure został pomyślnie mapowany na dysk Z.

Zamknij sesję pulpitu zdalnego dla maszyny wirtualnej *myVmPrivate*.

### <a name="confirm-access-is-denied-to-non-allowed-storage-account"></a>Potwierdzanie dostępu do *niedozwolonego* konta magazynu

Na tej samej maszynie wirtualnej *myVmPrivate* spróbuj zmapować udział plików platformy Azure na dysk X. 

```powershell
$acctKey = ConvertTo-SecureString -String $storageAcctKey1 -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential -ArgumentList "Azure\notallowedaccount", $acctKey
New-PSDrive -Name X -PSProvider FileSystem -Root "\\notallowedaccount.file.core.windows.net\my-file-share" -Credential $credential
```

Odmowa dostępu do udziału i pojawi się `New-PSDrive : Access is denied` komunikat o błędzie. Odmowa dostępu, ponieważ konto magazynu *notallowedaccount* nie znajduje się na liście dozwolonych zasobów w zasadach punktu końcowego usługi. 

Zamknij sesję pulpitu zdalnego dla maszyny wirtualnej *myVmPublic*.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy grupa zasobów i wszystkie zawarte w niej zasoby nie będą już potrzebne, można je usunąć za pomocą [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) :

```azurepowershell-interactive 
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Następne kroki

W tym artykule zastosowano zasady punktu końcowego usługi za pośrednictwem punktu końcowego usługi sieci wirtualnej platformy Azure do usługi Azure Storage. Konta usługi Azure Storage zostały utworzone i ograniczono dostęp do sieci tylko do określonych kont magazynu (i w ten sposób odmówiono innym) z podsieci sieci wirtualnej. Aby dowiedzieć się więcej na temat zasad punktu końcowego usługi, zobacz [Omówienie zasad punktów końcowych usługi](virtual-network-service-endpoint-policies-overview.md).
