---
title: Samouczek — Tworzenie sieci wirtualnych platformy Azure dla maszyn wirtualnych z systemem Windows i zarządzanie nimi
description: Z tego samouczka dowiesz się, jak tworzyć sieci wirtualne platformy Azure i zarządzać nimi za pomocą programu Azure PowerShell na maszynach wirtualnych z systemem Windows
author: cynthn
ms.service: virtual-machines
ms.subservice: networking
ms.collection: windows
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/04/2020
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 4e7f08e3c9869eae76f6c2e0127cfdab8f770d04
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102562184"
---
# <a name="tutorial-create-and-manage-azure-virtual-networks-for-windows-virtual-machines-with-azure-powershell"></a>Samouczek: tworzenie sieci wirtualnych platformy Azure i zarządzanie nimi za pomocą programu Azure PowerShell na maszynach wirtualnych z systemem Windows

Maszyny wirtualne platformy Azure korzystają z sieci platformy Azure do wewnętrznej i zewnętrznej komunikacji sieciowej. Ten samouczek przedstawia proces wdrażania dwóch maszyn wirtualnych i konfigurowania dla nich sieci platformy Azure. W przykładach w tym samouczku założono, że maszyny wirtualne hostują aplikację internetową z zapleczem bazy danych, jednak wdrożenie aplikacji nie zostało omówione w samouczku. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie sieci wirtualnej i podsieci
> * Tworzenie publicznego adresu IP
> * Tworzenie maszyny wirtualnej frontonu
> * Zabezpieczanie ruchu sieciowego
> * Tworzenie maszyny wirtualnej zaplecza


## <a name="vm-networking-overview"></a>Omówienie sieci maszyn wirtualnych

Sieci wirtualne platformy Azure umożliwiają bezpieczne połączenia sieciowe między maszynami wirtualnymi, Internetem i innymi usługami platformy Azure, takimi jak Azure SQL Database. Sieci wirtualne są podzielone na logiczne segmenty nazywane podsieciami. Podsieci są używane do sterowania przepływem sieciowym oraz pełnią funkcję granicy zabezpieczeń. Wdrażana maszyna wirtualna zwykle zawiera wirtualny interfejs sieciowy dołączony do podsieci.

Podczas pracy z tym samouczkiem zostaną utworzone następujące zasoby:

![Sieć wirtualna z dwiema podsieciami](./media/tutorial-virtual-network/networktutorial.png)

- *myVNet* — sieć wirtualna, której maszyny wirtualne używają do komunikacji między sobą i z Internetem.
- *myFrontendSubnet* — podsieć w sieci *myVNet* używana przez zasoby frontonu.
- *myPublicIPAddress* — publiczny adres IP używany do uzyskania dostępu do maszyny *myFrontendVM* za pośrednictwem Internetu.
- *myFrontendNic* — interfejs sieciowy używany przez maszynę wirtualną *myFrontendVM* do komunikacji z maszyną *myBackendVM*.
- *myFrontendVM* — maszyna wirtualna używana do komunikacji pomiędzy Internetem a maszyną *myBackendVM*.
- *myBackendNSG* — sieciowa grupa zabezpieczeń, która kontroluje komunikację między maszyną *myFrontendVM* a maszyną *myBackendVM*.
- *myBackendSubnet* — podsieć skojarzona z grupą *myBackendNSG* i używana przez zasoby zaplecza.
- *myBackendNic* — interfejs sieciowy używany przez maszynę wirtualną *myBackendVM* do komunikacji z maszyną *myFrontendVM*.
- *myBackendVM* — maszyna wirtualna, która korzysta z portu 1433 do komunikacji z maszyną *myFrontendVM*.


## <a name="launch-azure-cloud-shell"></a>Uruchamianie usługi Azure Cloud Shell

Usługa Azure Cloud Shell to bezpłatna interaktywna powłoka, której możesz używać do wykonywania kroków opisanych w tym artykule. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie. 

Aby otworzyć usługę Cloud Shell, wybierz pozycję **Wypróbuj** w prawym górnym rogu bloku kodu. Cloud Shell można również uruchomić na osobnej karcie przeglądarki, przechodząc do [https://shell.azure.com/powershell](https://shell.azure.com/powershell) . Wybierz przycisk **Kopiuj**, aby skopiować bloki kodu, wklej je do usługi Cloud Shell, a następnie naciśnij klawisz Enter, aby je uruchomić.


## <a name="create-subnet"></a>Tworzenie podsieci 

W tym samouczku zostanie utworzona jedna sieć wirtualna z dwoma podsieciami. Zostanie utworzona podsieć frontonu do hostowania aplikacji internetowej oraz podsieć zaplecza do hostowania serwera bazy danych.

Przed utworzeniem sieci wirtualnej należy utworzyć grupę zasobów za pomocą polecenia [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie *myRGNetwork* w lokalizacji *EastUS*:

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName myRGNetwork -Location EastUS
```

Utwórz konfigurację podsieci o nazwie *myFrontendSubnet* za pomocą polecenia [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig):

```azurepowershell-interactive
$frontendSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name myFrontendSubnet `
  -AddressPrefix 10.0.0.0/24
```

Następnie utwórz konfigurację podsieci o nazwie *myBackendSubnet*:

```azurepowershell-interactive
$backendSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name myBackendSubnet `
  -AddressPrefix 10.0.1.0/24
```

## <a name="create-virtual-network"></a>Tworzenie sieci wirtualnej

Utwórz sieć wirtualną o nazwie *myVNet* na podstawie konfiguracji *myFrontendSubnet* i *myBackendSubnet* za pomocą polecenia [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork):

```azurepowershell-interactive
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myVNet `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $frontendSubnet, $backendSubnet
```

W ten sposób utworzono sieć i podzielono ją na dwie podsieci — jedną dla usług frontonu i jedną dla usług zaplecza. W kolejnej sekcji zostaną utworzone maszyny wirtualne połączone z tymi podsieciami.

## <a name="create-a-public-ip-address"></a>Tworzenie publicznego adresu IP

Publiczny adres IP umożliwia dostęp do zasobów platformy Azure w Internecie. Można skonfigurować dynamiczną lub statyczną alokację publicznego adresu IP. Domyślnie publiczny adres IP jest przydzielany dynamicznie. Dynamiczne adresy IP są zwalniane, gdy przydział maszyny wirtualnej zostaje cofnięty. Dlatego adres IP zmienia się podczas każdej operacji, która obejmuje cofnięcie przydziału maszyny wirtualnej.

Można też ustawić statyczną metodę alokacji, co gwarantuje, że adres IP pozostaje przydzielony do maszyny wirtualnej nawet wówczas, gdy jej przydział zostanie cofnięty. Jeśli używasz statycznego adresu IP, nie możesz określić samego adresu IP. Zamiast tego jest on przydzielany z puli dostępnych adresów.

Na koniec utwórz publiczny adres IP o nazwie *myPublicIPAddress*, korzystając z polecenia [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress):

```azurepowershell-interactive
$pip = New-AzPublicIpAddress `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -AllocationMethod Dynamic `
  -Name myPublicIPAddress
```

Możesz zmienić parametr -AllocationMethod na `Static`, aby przypisać statyczny publiczny adres IP.

## <a name="create-a-front-end-vm"></a>Tworzenie maszyny wirtualnej frontonu

Aby maszyna wirtualna mogła komunikować się w sieci wirtualnej, potrzebuje wirtualnego interfejsu sieciowego. Utwórz interfejs sieciowy za pomocą polecenia [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface):

```azurepowershell-interactive
$frontendNic = New-AzNetworkInterface `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myFrontend `
  -SubnetId $vnet.Subnets[0].Id `
  -PublicIpAddressId $pip.Id
```

Ustaw nazwę użytkownika i hasło potrzebne dla konta administratora na maszynie wirtualnej przy użyciu polecenia [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential). Te poświadczenia służą do nawiązywania połączenia z maszyną wirtualną w dodatkowych krokach:

```azurepowershell-interactive
$cred = Get-Credential
```

Utwórz maszyny wirtualne za pomocą polecenia [New-AzVM](/powershell/module/az.compute/new-azvm).

```azurepowershell-interactive
New-AzVM `
   -Credential $cred `
   -Name myFrontend `
   -PublicIpAddressName myPublicIPAddress `
   -ResourceGroupName myRGNetwork `
   -Location "EastUS" `
   -Size Standard_D1 `
   -SubnetName myFrontendSubnet `
   -VirtualNetworkName myVNet
```

## <a name="secure-network-traffic"></a>Zabezpieczanie ruchu sieciowego

Sieciowa grupa zabezpieczeń (NSG, network security group) zawiera listę reguł zabezpieczeń, które blokują lub zezwalają na ruch sieciowy do zasobów połączonych z usługami Azure Virtual Network (VNet). Sieciowe grupy zabezpieczeń można skojarzyć z podsieciami lub pojedynczymi interfejsami sieciowymi. Sieciowa grupa zabezpieczeń skojarzona z interfejsem sieciowym jest stosowana wyłącznie do skojarzonej maszyny wirtualnej. Jeśli sieciowa grupa zabezpieczeń jest skojarzona z podsiecią, te reguły są stosowane do wszystkich zasobów połączonych z tą podsiecią.

### <a name="network-security-group-rules"></a>Reguły sieciowych grup zabezpieczeń

Reguły sieciowych grup zabezpieczeń określają porty sieciowe, dla których ruch jest dozwolony lub niedozwolony. Reguły mogą zawierać zakresy źródłowych lub docelowych adresów IP, umożliwiając kontrolę ruchu pomiędzy określonymi systemami lub podsieciami. Reguły sieciowych grup zabezpieczeń mają również priorytet (od 1 do 4096). Priorytet określa kolejność oceny reguł. Reguła z priorytetem 100 jest oceniana przed regułą z priorytetem 200.

Wszystkie sieciowe grupy zabezpieczeń zawierają zestaw reguł domyślnych. Reguł domyślnych nie można usunąć, ale ponieważ mają przypisany najniższy priorytet, mogą być zastąpione przez tworzone zasady.

- **Sieć wirtualna** — ruch pochodzący z sieci wirtualnej i kończący się w niej jest dozwolony zarówno w kierunku przychodzącym, jak i wychodzącym.
- **Internet** — ruch wychodzący jest dozwolony, ale ruch przychodzący jest blokowany.
- **Moduł równoważenia obciążenia** — umożliwia modułowi równoważenia obciążenia platformy Azure badanie kondycji maszyn wirtualnych i wystąpień ról. Jeśli nie używasz zestawu z równoważeniem obciążenia, możesz przesłonić tę regułę.

### <a name="create-network-security-groups"></a>Tworzenie sieciowych grup zabezpieczeń

Utwórz regułę ruchu przychodzącego o nazwie *myFrontendNSGRule*, aby zezwolić na przychodzący ruch internetowy na maszynie *myFrontendVM* za pomocą polecenia [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig):

```azurepowershell-interactive
$nsgFrontendRule = New-AzNetworkSecurityRuleConfig `
  -Name myFrontendNSGRule `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 200 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 80 `
  -Access Allow
```

Możesz zezwalać na ruch wewnętrzny do maszyny *myBackendVM* pochodzący jedynie z maszyny *myFrontendVM*, tworząc dla podsieci zaplecza sieciową grupę zabezpieczeń. W poniższym przykładzie pokazano tworzenie sieciowej grupy zabezpieczeń o nazwie *myBackendNSGRule*:

```azurepowershell-interactive
$nsgBackendRule = New-AzNetworkSecurityRuleConfig `
  -Name myBackendNSGRule `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 100 `
  -SourceAddressPrefix 10.0.0.0/24 `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 1433 `
  -Access Allow
```

Dodaj sieciową grupę zabezpieczeń o nazwie *myFrontendNSG* przy użyciu polecenia [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup):

```azurepowershell-interactive
$nsgFrontend = New-AzNetworkSecurityGroup `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myFrontendNSG `
  -SecurityRules $nsgFrontendRule
```

Teraz dodaj sieciową grupę zabezpieczeń o nazwie *myBackendNSG* przy użyciu polecenia New-AzNetworkSecurityGroup:

```azurepowershell-interactive
$nsgBackend = New-AzNetworkSecurityGroup `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myBackendNSG `
  -SecurityRules $nsgBackendRule
```

Dodaj sieciowe grupy zabezpieczeń do podsieci:

```azurepowershell-interactive
$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName myRGNetwork `
  -Name myVNet
$frontendSubnet = $vnet.Subnets[0]
$backendSubnet = $vnet.Subnets[1]
$frontendSubnetConfig = Set-AzVirtualNetworkSubnetConfig `
  -VirtualNetwork $vnet `
  -Name myFrontendSubnet `
  -AddressPrefix $frontendSubnet.AddressPrefix `
  -NetworkSecurityGroup $nsgFrontend
$backendSubnetConfig = Set-AzVirtualNetworkSubnetConfig `
  -VirtualNetwork $vnet `
  -Name myBackendSubnet `
  -AddressPrefix $backendSubnet.AddressPrefix `
  -NetworkSecurityGroup $nsgBackend
Set-AzVirtualNetwork -VirtualNetwork $vnet
```

## <a name="create-a-back-end-vm"></a>Tworzenie maszyny wirtualnej zaplecza

Na potrzeby tego samouczka najprostszym sposobem tworzenia maszyny wirtualnej zaplecza jest wykorzystanie obrazu programu SQL Server. Samouczek obejmuje wyłącznie tworzenie maszyny wirtualnej z serwerem baz danych. Nie podaje żadnych informacji na temat uzyskiwania dostępu do bazy danych.

Utwórz interfejs *myBackendNic*:

```azurepowershell-interactive
$backendNic = New-AzNetworkInterface `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myBackend `
  -SubnetId $vnet.Subnets[1].Id
```

Ustaw nazwę użytkownika i hasło potrzebne dla konta administratora na maszynie wirtualnej przy użyciu polecenia Get-Credential:

```azurepowershell-interactive
$cred = Get-Credential
```

Utwórz maszynę *myBackendVM*.

```azurepowershell-interactive
New-AzVM `
   -Credential $cred `
   -Name myBackend `
   -ImageName "MicrosoftSQLServer:SQL2016SP1-WS2016:Enterprise:latest" `
   -ResourceGroupName myRGNetwork `
   -Location "EastUS" `
   -SubnetName MyBackendSubnet `
   -VirtualNetworkName myVNet
```

Obraz w tym przykładzie ma zainstalowany program SQL Server, ale nie jest używany w ramach tego samouczka. Został on uwzględniony, aby zademonstrować sposób konfigurowania maszyny wirtualnej do obsługiwania ruchu internetowego albo do zarządzania bazami danych.

## <a name="next-steps"></a>Następne kroki

W tym samouczku utworzono i zabezpieczono sieci platformy Azure na potrzeby maszyn wirtualnych. 

> [!div class="checklist"]
> * Tworzenie sieci wirtualnej i podsieci
> * Tworzenie publicznego adresu IP
> * Tworzenie maszyny wirtualnej frontonu
> * Zabezpieczanie ruchu sieciowego
> * Tworzenie maszyny wirtualnej zaplecza

Aby dowiedzieć się więcej o ochronie dysków maszyn wirtualnych, zobacz [kopia zapasowa i odzyskiwanie po awarii dla dysków](../backup-and-disaster-recovery-for-azure-iaas-disks.md).