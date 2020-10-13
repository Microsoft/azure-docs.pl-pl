---
title: Połączenie skonfigurowane z sieci wirtualnej do SAP HANA na platformie Azure (duże wystąpienia) | Microsoft Docs
description: Połączenie skonfigurowane z sieci wirtualnej do używania SAP HANA na platformie Azure (duże wystąpienia).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/25/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8c7e8d4875a8bf3f53ac536ae95ac7499a74d45c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87082158"
---
# <a name="connect-a-virtual-network-to-hana-large-instances"></a>Łączenie sieci wirtualnej z dużymi wystąpieniami platformy HANA

Po utworzeniu sieci wirtualnej platformy Azure możesz połączyć tę sieć, aby SAP HANA w dużych wystąpieniach platformy Azure. Utwórz bramę usługi Azure ExpressRoute w sieci wirtualnej. Ta brama umożliwia połączenie sieci wirtualnej z obwodem ExpressRoute, który łączy się z dzierżawcą klienta w sygnaturze dużej instancji HANA.

> [!NOTE] 
> Wykonanie tego kroku może potrwać do 30 minut. Nowa brama zostanie utworzona w wyznaczonym subskrypcji platformy Azure, a następnie podłączona do określonej sieci wirtualnej platformy Azure.

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

Jeśli Brama już istnieje, sprawdź, czy jest to brama ExpressRoute. Jeśli nie jest to brama ExpressRoute, Usuń bramę i utwórz ją ponownie jako bramę ExpressRoute. Jeśli Brama ExpressRoute jest już ustanowiona, zapoznaj się z sekcją w tym artykule "łączenie sieci wirtualnych". 

- Użyj [Azure Portal](https://portal.azure.com/) lub programu PowerShell, aby utworzyć bramę sieci VPN ExpressRoute połączonej z siecią wirtualną.
  - Jeśli używasz Azure Portal, Dodaj nową **bramę Virtual Network**, a następnie wybierz pozycję **ExpressRoute** jako typ bramy.
  - Jeśli używasz programu PowerShell, najpierw Pobierz najnowszy [zestaw SDK Azure PowerShell](https://azure.microsoft.com/downloads/)i użyj go. 
 
Następujące polecenia tworzą bramę ExpressRoute. Teksty poprzedzone przez zdefiniowane przez _$_ użytkownika zmienne, które powinny zostać zaktualizowane przy użyciu określonych informacji.

```powershell
# These Values should already exist, update to match your environment
$myAzureRegion = "eastus"
$myGroupName = "SAP-East-Coast"
$myVNetName = "VNet01"

# These values are used to create the gateway, update for how you wish the GW components to be named
$myGWName = "VNet01GW"
$myGWConfig = "VNet01GWConfig"
$myGWPIPName = "VNet01GWPIP"
$myGWSku = "HighPerformance" # Supported values for HANA large instances are: HighPerformance or UltraPerformance

# These Commands create the Public IP and ExpressRoute Gateway
$vnet = Get-AzVirtualNetwork -Name $myVNetName -ResourceGroupName $myGroupName
$subnet = Get-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
New-AzPublicIpAddress -Name $myGWPIPName -ResourceGroupName $myGroupName `
-Location $myAzureRegion -AllocationMethod Dynamic
$gwpip = Get-AzPublicIpAddress -Name $myGWPIPName -ResourceGroupName $myGroupName
$gwipconfig = New-AzVirtualNetworkGatewayIpConfig -Name $myGWConfig -SubnetId $subnet.Id `
-PublicIpAddressId $gwpip.Id

New-AzVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName -Location $myAzureRegion `
-IpConfigurations $gwipconfig -GatewayType ExpressRoute `
-GatewaySku $myGWSku -VpnType PolicyBased -EnableBgp $true
```

W tym przykładzie użyto jednostki SKU bramy HighPerformance. Dostępne opcje to HighPerformance lub UltraPerformance jako jedyne jednostki SKU bramy, które są obsługiwane dla SAP HANA na platformie Azure (duże wystąpienia).

> [!IMPORTANT]
> W przypadku dużych wystąpień jednostek SKU klasy typu II należy użyć jednostki SKU bramy UltraPerformance.

## <a name="link-virtual-networks"></a>Łączenie sieci wirtualnych

Usługa Azure Virtual Network ma teraz bramę ExpressRoute. Użyj informacji o autoryzacji dostarczonych przez firmę Microsoft w celu połączenia bramy ExpressRoute z obwodem ExpressRoute SAP HANA — duże wystąpienia. Można nawiązać połączenie za pomocą Azure Portal lub programu PowerShell. Instrukcje programu PowerShell są następujące. 

Uruchom następujące polecenia dla każdej bramy ExpressRoute, używając innego AuthGUID dla każdego połączenia. Pierwsze dwa wpisy, które przedstawiono w poniższym skrypcie pochodzą z informacji dostarczonych przez firmę Microsoft. Ponadto AuthGUID jest specyficzny dla każdej sieci wirtualnej i jej bramy. Jeśli chcesz dodać kolejną sieć wirtualną platformy Azure, musisz uzyskać kolejną AuthID dla obwodu usługi ExpressRoute, który łączy duże wystąpienia platformy HANA z platformą Azure od firmy Microsoft. 

```powershell
# Populate with information provided by Microsoft Onboarding team
$PeerID = "/subscriptions/9cb43037-9195-4420-a798-f87681a0e380/resourceGroups/Customer-USE-Circuits/providers/Microsoft.Network/expressRouteCircuits/Customer-USE01"
$AuthGUID = "76d40466-c458-4d14-adcf-3d1b56d1cd61"

# Your ExpressRoute Gateway information
$myGroupName = "SAP-East-Coast"
$myGWName = "VNet01GW"
$myGWLocation = "East US"

# Define the name for your connection
$myConnectionName = "VNet01GWConnection"

# Create a new connection between the ER Circuit and your Gateway using the Authorization
$gw = Get-AzVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName
    
New-AzVirtualNetworkGatewayConnection -Name $myConnectionName `
-ResourceGroupName $myGroupName -Location $myGWLocation -VirtualNetworkGateway1 $gw `
-PeerId $PeerID -ConnectionType ExpressRoute -AuthorizationKey $AuthGUID -ExpressRouteGatewayBypass
```

> [!NOTE]
> Ostatnim parametrem w poleceniu New-AzVirtualNetworkGatewayConnection, **ExpressRouteGatewayBypass** jest nowy parametr, który włącza funkcję ExpressRoute Fast Path. Funkcja, która zmniejsza opóźnienie sieci między jednostkami dużych wystąpień usługi HANA i maszynami wirtualnymi platformy Azure. Funkcjonalność została dodana w maju 2019. Aby uzyskać więcej informacji, zobacz artykuł [SAP HANA (duże wystąpienia) Architektura sieci](./hana-network-architecture.md). Przed uruchomieniem poleceń upewnij się, że korzystasz z najnowszej wersji poleceń cmdlet programu PowerShell.

Aby połączyć bramę z więcej niż jednym obwodem usługi ExpressRoute skojarzonym z subskrypcją, może być konieczne wykonanie tego kroku więcej niż raz. Można na przykład połączyć tę samą bramę sieci wirtualnej z obwodem usługi ExpressRoute, który łączy sieć wirtualną z siecią lokalną.

## <a name="applying-expressroute-fast-path-to-existing-hana-large-instance-expressroute-circuits"></a>Stosowanie szybkiej ścieżki ExpressRoute do istniejących obwodów usługi ExpressRoute duże wystąpienie platformy HANA
W tej części dokumentacji wyjaśniono, jak połączyć nowy obwód usługi ExpressRoute, który został utworzony przy użyciu wdrożenia dużego wystąpienia HANA w usłudze Azure ExpressRoute Gateway jednej z sieci wirtualnych platformy Azure. Wielu klientów ma już konfigurację obwodów usługi ExpressRoute, a ich sieci wirtualne są już połączone z dużymi wystąpieniami platformy HANA. Ponieważ nowa szybka ścieżka ExpressRoute zmniejsza opóźnienie sieci, zaleca się zastosowanie zmiany w celu korzystania z tej funkcji. Polecenia służące do łączenia nowego obwodu ExpreesRoute i zmiany istniejącego obwodu ExpressRoute są takie same. W związku z tym należy uruchomić tę sekwencję poleceń programu PowerShell, aby zmienić istniejący obwód do użycia 

```powershell
# Populate with information provided by Microsoft Onboarding team
$PeerID = "/subscriptions/9cb43037-9195-4420-a798-f87681a0e380/resourceGroups/Customer-USE-Circuits/providers/Microsoft.Network/expressRouteCircuits/Customer-USE01"
$AuthGUID = "76d40466-c458-4d14-adcf-3d1b56d1cd61"

# Your ExpressRoute Gateway information
$myGroupName = "SAP-East-Coast"
$myGWName = "VNet01GW"
$myGWLocation = "East US"

# Define the name for your connection
$myConnectionName = "VNet01GWConnection"

# Create a new connection between the ER Circuit and your Gateway using the Authorization
$gw = Get-AzVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName
    
New-AzVirtualNetworkGatewayConnection -Name $myConnectionName `
-ResourceGroupName $myGroupName -Location $myGWLocation -VirtualNetworkGateway1 $gw `
-PeerId $PeerID -ConnectionType ExpressRoute -AuthorizationKey $AuthGUID -ExpressRouteGatewayBypass
```

Ważne jest, aby dodać ostatni parametr, jak Wyświetlono powyżej, aby włączyć funkcję ExpressRoute Fast Path


## <a name="expressroute-global-reach"></a>ExpressRoute Global Reach
Aby włączyć Global Reach dla jednego lub obu dwóch scenariuszy:

 - Replikacja systemu HANA bez dodatkowych serwerów proxy lub zapór
 - Kopiowanie kopii zapasowych między jednostkami dużych wystąpień programu HANA w dwóch różnych regionach w celu wykonywania kopii systemu lub odświeżeń systemu

należy wziąć pod uwagę, że:

- Należy podać zakres przestrzeni adresowej z/29 przestrzeni adresowej. Zakres adresów nie może nakładać się na wszystkie inne zakresy przestrzeni adresowej używane do łączenia z platformą HANA duże wystąpienia w systemie Azure i mogą nie nakładać się na żadne z zakresów adresów IP używanych w innym miejscu na platformie Azure lub lokalnie.
- Istnieje ograniczenie ASN (numer systemu autonomicznego), którego można użyć do anonsowania tras lokalnych w dużych wystąpieniach platformy HANA. Lokalne nie mogą anonsować żadnych tras z prywatnym WPW w zakresie 65000 – 65020 lub 65515. 
- Aby zapoznać się z scenariuszem łączenia lokalnego dostępu do dużych wystąpień platformy HANA, należy obliczyć opłatę za obwód, który łączy się z platformą Azure. W przypadku cen Sprawdź ceny [dodatku Global REACH](https://azure.microsoft.com/pricing/details/expressroute/).

Aby uzyskać jedno lub oba scenariusze zastosowane do wdrożenia, Otwórz komunikat pomocy technicznej na platformie Azure zgodnie z opisem w artykule [otwieranie żądania obsługi dla dużych wystąpień usługi Hana](./hana-li-portal.md#open-a-support-request-for-hana-large-instances)

Wymagane dane i słowa kluczowe, które są potrzebne do użycia przez firmę Microsoft, aby można było kierować i wykonywać żądanie, wygląda następująco:

- Usługa: SAP HANA duże wystąpienie
- Typ problemu: Konfiguracja i konfiguracja
- Podtyp problemu: mój problem nie jest wymieniony powyżej
- Podmiot "Modyfikuj moją sieć — Dodaj Global Reach"
- Szczegóły: "Dodaj Global Reach do wystąpienia HANA duże wystąpienie do dzierżawy dużej instancji HANA lub" Dodaj Global Reach do lokalnego dzierżawy dużych wystąpień w usłudze Hana.
- Dodatkowe szczegóły dotyczące wystąpienia usługi HANA duże wystąpienie dzierżawy dużego wystąpienia: należy zdefiniować **dwa regiony platformy Azure** , w których znajdują się dwie dzierżawcy do połączenia **, i** należy przesłać **zakres adresów IP/29**
- Dodatkowe szczegóły dotyczące przypadku dzierżawy z dużym wystąpieniem w środowisku lokalnym i HANA: należy zdefiniować **region platformy Azure** , w którym wdrożono dzierżawcę dużej instancji Hana, z którym chcesz nawiązać bezpośrednie połączenie. Ponadto należy podać identyfikator **GUID uwierzytelniania** i **element równorzędny obwodu** , który został odebrany podczas ustanawiania obwodu usługi ExpressRoute między środowiskiem lokalnym i platformą Azure. Ponadto należy nazwać numer **ASN**. Ostatni element dostarczany jest **zakresem adresów IP/29** dla ExpressRoute Global REACH.

> [!NOTE]
> Jeśli chcesz, aby oba przypadki były obsługiwane, należy podać dwa różne/29 zakresów adresów IP, które nie nakładają się na żadne inne adresy IP używane do tej pory. 




## <a name="next-steps"></a>Następne kroki

- [Dodatkowe wymagania dotyczące sieci dla elementu HLI](hana-additional-network-requirements.md)
