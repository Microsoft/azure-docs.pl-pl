---
title: 'Łączenie sieci wirtualnej z siecią wirtualną przy użyciu połączenia Sieć wirtualna-sieć wirtualna: interfejs wiersza polecenia platformy Azure'
description: Łączenie sieci wirtualnych przy użyciu połączenia sieć wirtualna-sieć wirtualna oraz interfejsu wiersza polecenia platformy Azure.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/02/2020
ms.author: cherylmc
ms.custom: devx-track-azurecli
ms.openlocfilehash: b9502f3fbd50aad756e15daa4db1badda2abf9ab
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94660070"
---
# <a name="configure-a-vnet-to-vnet-vpn-gateway-connection-using-azure-cli"></a>Konfigurowanie połączenia bramy sieci VPN między sieciami wirtualnymi przy użyciu interfejsu wiersza polecenia platformy Azure

Ten artykuł pomoże Ci połączyć sieci wirtualne przy użyciu typu połączenia sieć wirtualna-sieć wirtualna. Sieci wirtualne mogą być zlokalizowane w tych samych lub różnych regionach i mogą funkcjonować w ramach tej samej lub różnych subskrypcji. W przypadku łączenia sieci wirtualnych z różnych subskrypcji subskrypcje nie muszą być skojarzone z tą samą dzierżawą usługi Active Directory.

Kroki podane w tym artykule mają zastosowanie do modelu wdrażania przy użyciu usługi Resource Manager i użyto w nich interfejsu wiersza polecenia platformy Azure. Tę konfigurację możesz również utworzyć przy użyciu innego narzędzia wdrażania lub modelu wdrażania, wybierając inną opcję z następującej listy:

> [!div class="op_single_selector"]
> * [Witryna Azure Portal](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [Program PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
> * [Interfejs wiersza polecenia platformy Azure](vpn-gateway-howto-vnet-vnet-cli.md)
> * [Portal Azure (klasyczny)](vpn-gateway-howto-vnet-vnet-portal-classic.md)
> * [Łączenie różnych modeli wdrażania — witryna Azure Portal](vpn-gateway-connect-different-deployment-models-portal.md)
> * [Łączenie różnych modeli wdrażania — program PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
>
>

## <a name="about-connecting-vnets"></a><a name="about"></a>Łączenie sieci wirtualnych — informacje

Z sieciami wirtualnymi można łączyć się na wiele sposobów. W poniższych sekcjach opisano różne metody łączenia się z sieciami wirtualnymi.

### <a name="vnet-to-vnet"></a>Połączenia między sieciami wirtualnymi

Skonfigurowanie połączenia sieci wirtualnej z siecią wirtualną jest dobrym sposobem łatwego połączenia sieci wirtualnych. Proces łączenia dwóch sieci wirtualnych przy użyciu typu połączenia sieć wirtualna-sieć wirtualna przebiega podobnie do procesu tworzenia połączenia IPsec typu lokacja-lokacja z lokacją lokalną. Oba typy połączeń używają bramy sieci VPN, aby zapewnić bezpieczny tunel korzystający z protokołu IPsec/IKE, oraz działają tak samo pod względem komunikacji. Różnica między nimi dotyczy konfiguracji bramy sieci lokalnej. Podczas tworzenia połączenia sieć wirtualna-sieć wirtualna przestrzeń adresowa bramy sieci lokalnej nie jest widoczna. Jest ona tworzona i wypełniana automatycznie. Po zaktualizowaniu przestrzeni adresowej dla jednej sieci wirtualnej druga sieć wirtualna automatycznie kieruje ruch do zaktualizowanej przestrzeni adresowej. Utworzenie połączenia sieć wirtualna-sieć wirtualna jest zwykle szybsze i łatwiejsze niż utworzenie połączenia lokacja-lokacja między sieciami wirtualnymi.

### <a name="connecting-vnets-using-site-to-site-ipsec-steps"></a>Łączenie sieci wirtualnych za pomocą procedury połączenia lokacja-lokacja (IPsec)

W przypadku pracy ze złożoną konfiguracją sieci lepszym rozwiązaniem może być połączenie sieci wirtualnych za pomocą procedury tworzenia połączenia [lokacja-lokacja](vpn-gateway-howto-site-to-site-resource-manager-cli.md) zamiast sieć wirtualna-sieć wirtualna. W przypadku korzystania z procedury tworzenia połączenia lokacja-lokacja bramy sieci lokalnej są tworzone i konfigurowane ręcznie. Każda z bram sieci lokalnej sieci wirtualnej traktuje drugą sieć wirtualną jako lokację lokalną. Dzięki temu można określić dodatkową przestrzeń adresową dla bramy sieci lokalnej w celu kierowania ruchu. W przypadku zmiany przestrzeni adresowej sieci wirtualnej należy ręcznie zaktualizować odpowiednią bramę sieci lokalnej w celu odzwierciedlenia zmiany. Nie jest to aktualizowane automatycznie.

### <a name="vnet-peering"></a>Komunikacja równorzędna sieci wirtualnych

Warto rozważyć połączenie sieci wirtualnych za pomocą komunikacji równorzędnej sieci wirtualnych. W przypadku komunikacji równorzędnej sieci wirtualnych nie jest używana brama sieci VPN i są z nią związane inne ograniczenia. Ponadto [ceny dotyczące komunikacji równorzędnej sieci wirtualnych](https://azure.microsoft.com/pricing/details/virtual-network) są obliczane inaczej niż [ceny dotyczące usługi VPN Gateway połączenia sieć wirtualna-sieć wirtualna](https://azure.microsoft.com/pricing/details/vpn-gateway). Aby uzyskać więcej informacji, zobacz temat [Komunikacja równorzędna sieci wirtualnych](../virtual-network/virtual-network-peering-overview.md).

## <a name="why-create-a-vnet-to-vnet-connection"></a><a name="why"></a>Dlaczego warto utworzyć połączenie sieć wirtualna-sieć wirtualna?

Sieci wirtualne można łączyć za pomocą połączenia sieć wirtualna-sieć wirtualna z następujących powodów:

* **Niezależna od regionu nadmiarowość i obecność geograficzna**

  * Można tworzyć własne replikacje geograficzne lub przeprowadzać synchronizację z bezpiecznym połączeniem bez przechodzenia przez punkty końcowe dostępne z Internetu.
  * Usługi Azure Traffic Manager i Load Balancer pozwalają skonfigurować obciążenie o wysokiej dostępności z nadmiarowością geograficzną w wielu regionach świadczenia usługi Azure. Istotnym przykładem jest konfiguracja ustawienia SQL Zawsze włączone, w przypadku którego grupy dostępności rozciągają się na wiele regionów świadczenia usługi Azure.
* **Regionalne aplikacje wielowarstwowe z izolacją lub granicami administracyjnymi**

  * W ramach jednego regionu można skonfigurować aplikacje wielowarstwowe z wielu połączonych ze sobą sieci wirtualnych, korzystając z izolacji lub wymagań administracyjnych.

Komunikację między sieciami wirtualnymi można łączyć z konfiguracjami obejmującymi wiele lokacji. Pozwala to tworzyć topologie sieci, które łączą wdrożenia obejmujące wiele lokalizacji z połączeniami między sieciami wirtualnymi.

## <a name="which-vnet-to-vnet-steps-should-i-use"></a><a name="steps"></a>Która procedura połączenia sieć wirtualna-sieć wirtualna ma zastosowanie w moim przypadku?

W tym artykule przedstawiono dwie różne procedury tworzenia połączenia sieć wirtualna-sieć wirtualna. Jeden zestaw dotyczy [sieci wirtualnych znajdujących się w tej samej subskrypcji](#samesub), a drugi ma zastosowanie w przypadku [sieci wirtualnych znajdujących się w różnych subskrypcjach](#difsub). 

W tym ćwiczeniu możesz łączyć konfiguracje lub po prostu wybrać tę, której chcesz używać. Wszystkie konfiguracje używają typu połączenia sieć wirtualna-sieć wirtualna. Ruch sieciowy przepływa między bezpośrednio połączonymi sieciami wirtualnymi. W tym ćwiczeniu ruch z sieci TestVNet4 nie jest kierowany do sieci TestVNet5.

* [Sieci wirtualnych, które znajdują się w tej samej subskrypcji:](#samesub) W przypadku tej konfiguracji należy użyć sieci testvnet1 i sieci testvnet4.

  ![Diagram przedstawiający kroki usługi net-to-V netto dotyczące sieci V, które znajdują się w tej samej subskrypcji.](./media/vpn-gateway-howto-vnet-vnet-cli/v2vrmps.png)

* [Sieci wirtualne znajdujące się w różnych subskrypcjach:](#difsub) w ramach tej konfiguracji są używane sieci TestVNet1 i TestVNet5.

  ![Diagram połączenia między sieciami wirtualnymi (v2v)](./media/vpn-gateway-howto-vnet-vnet-cli/v2vdiffsub.png)


## <a name="connect-vnets-that-are-in-the-same-subscription"></a><a name="samesub"></a>Łączenie sieci wirtualnych, które należą do tej samej subskrypcji

### <a name="before-you-begin"></a>Zanim rozpoczniesz

Przed rozpoczęciem zainstaluj najnowszą wersję poleceń interfejsu wiersza polecenia (wersję 2.0 lub nowszą). Aby uzyskać informacje o instalowaniu poleceń interfejsu wiersza polecenia, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

### <a name="plan-your-ip-address-ranges"></a><a name="Plan"></a>Planowanie zakresów adresów IP

W poniższych krokach utworzymy dwie sieci wirtualne wraz z odpowiednimi konfiguracjami oraz podsieciami bram. Następnie utworzymy połączenie sieci VPN między dwiema sieciami wirtualnymi. Ważne, aby zaplanować zakresy adresów IP dla konfiguracji sieci. Niezbędne jest upewnienie się, że zakresy sieci wirtualnej ani sieci lokalnej nie zachodzą na siebie w jakikolwiek sposób. W tych przykładach nie uwzględniamy serwera DNS. Jeśli chcesz, aby w sieciach wirtualnych działało rozpoznawanie nazw, zobacz artykuł o [rozpoznawaniu nazw](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

W przykładach stosujemy następujące wartości:

**Wartości dla sieci TestVNet1:**

* Nazwa sieci wirtualnej: TestVNet1
* Grupa zasobów: TestRG1
* Lokalizacja: Wschodnie stany USA
* TestVNet1: 10.11.0.0/16 & 10.12.0.0/16
* FrontEnd: 10.11.0.0/24
* BackEnd: 10.12.0.0/24
* GatewaySubnet: 10.12.255.0/27
* GatewayName: VNet1GW
* Publiczny adres IP: VNet1GWIP
* VPNType: RouteBased
* Connection(1to4): VNet1toVNet4
* Connection(1to5): VNet1toVNet5 (dla sieci wirtualnych w różnych subskrypcjach)

**Wartości dla sieci TestVNet4:**

* Nazwa sieci wirtualnej: TestVNet4
* TestVNet2: 10.41.0.0/16 & 10.42.0.0/16
* FrontEnd: 10.41.0.0/24
* BackEnd: 10.42.0.0/24
* GatewaySubnet: 10.42.255.0/27
* Grupa zasobów: TestRG4
* Lokalizacja: West US
* GatewayName: VNet4GW
* Publiczny adres IP: VNet4GWIP
* VPNType: RouteBased
* Połączenie: VNet4toVNet1

### <a name="step-1---connect-to-your-subscription"></a><a name="Connect"></a>Krok 1 — Nawiązanie połączenia z subskrypcją

[!INCLUDE [CLI login](../../includes/vpn-gateway-cli-login-numbers-include.md)]

### <a name="step-2---create-and-configure-testvnet1"></a><a name="TestVNet1"></a>Krok 2 — tworzenie i konfigurowanie sieci TestVNet1

1. Utwórz grupę zasobów.

   ```azurecli
   az group create -n TestRG1  -l eastus
   ```
2. Utwórz sieć TestVNet1 i jej podsieci. W tym przykładzie jest tworzona sieć wirtualna TestVNet1 i podsieć o nazwie FrontEnd.

   ```azurecli
   az network vnet create -n TestVNet1 -g TestRG1 --address-prefix 10.11.0.0/16 -l eastus --subnet-name FrontEnd --subnet-prefix 10.11.0.0/24
   ```
3. Utwórz dodatkową przestrzeń adresową dla podsieci zaplecza. Zwróć uwagę, że w tym kroku określana jest zarówno przestrzeń adresowa utworzona wcześniej, jak i dodatkowa przestrzeń adresowa, którą chcemy dodać. Wynika to z tego, że wykonanie polecenia [az network vnet update](/cli/azure/network/vnet) zastąpi wcześniejsze ustawienia. Pamiętaj, aby w przypadku korzystania z tego polecenia określić wszystkie prefiksy adresu.

   ```azurecli
   az network vnet update -n TestVNet1 --address-prefixes 10.11.0.0/16 10.12.0.0/16 -g TestRG1
   ```
4. Utwórz podsieć zaplecza.
  
   ```azurecli
   az network vnet subnet create --vnet-name TestVNet1 -n BackEnd -g TestRG1 --address-prefix 10.12.0.0/24 
   ```
5. Utwórz podsieć bramy. Zwróć uwagę, że podsieć bramy ma nazwę „GatewaySubnet”. Nazwa jest wymagana. W tym przykładzie użyto podsieci bramy /27. Chociaż możliwe jest utworzenie małej podsieci bramy /29, zaleca się wybranie podsieci przynajmniej /28 lub /27, aby zawierała więcej adresów. Zapewni to wystarczająco dużo adresów, aby możliwe były dodatkowe konfiguracje, które mogą być potrzebne w przyszłości.

   ```azurecli 
   az network vnet subnet create --vnet-name TestVNet1 -n GatewaySubnet -g TestRG1 --address-prefix 10.12.255.0/27
   ```
6. Następnie zostaje przesłane żądanie przydzielenia publicznego adresu IP do bramy, która zostanie utworzona dla sieci wirtualnej. Należy zauważyć, że metoda AllocationMethod jest dynamiczna. Nie możesz określić adresu IP, którego chcesz użyć. Jest on przydzielany dynamicznie do bramy.

   ```azurecli
   az network public-ip create -n VNet1GWIP -g TestRG1 --allocation-method Dynamic
   ```
7. Utwórz bramę sieci wirtualnej dla sieci TestVNet1. Konfiguracje połączeń między sieciami wirtualnymi wymagają zastosowania wartości RouteBased obiektu VpnType. Jeśli to polecenie zostanie uruchomione z parametrem „--no-wait”, nie będą widoczne żadne informacje zwrotne ani dane wyjściowe. Parametr „--no-wait” umożliwia utworzenie bramy w tle. Nie oznacza to, że brama sieci VPN zostanie utworzona natychmiast. Proces ten może trwać nawet 45 minut lub dłużej — w zależności od używanej jednostki SKU bramy.

   ```azurecli
   az network vnet-gateway create -n VNet1GW -l eastus --public-ip-address VNet1GWIP -g TestRG1 --vnet TestVNet1 --gateway-type Vpn --sku VpnGw1 --vpn-type RouteBased --no-wait
   ```

### <a name="step-3---create-and-configure-testvnet4"></a><a name="TestVNet4"></a>Krok 3 — Tworzenie i konfigurowanie sieci TestVNet4

1. Utwórz grupę zasobów.

   ```azurecli
   az group create -n TestRG4  -l westus
   ```
2. Utwórz sieć TestVNet4.

   ```azurecli
   az network vnet create -n TestVNet4 -g TestRG4 --address-prefix 10.41.0.0/16 -l westus --subnet-name FrontEnd --subnet-prefix 10.41.0.0/24
   ```

3. Utwórz dodatkowe podsieci dla sieci TestVNet4.

   ```azurecli
   az network vnet update -n TestVNet4 --address-prefixes 10.41.0.0/16 10.42.0.0/16 -g TestRG4 
   az network vnet subnet create --vnet-name TestVNet4 -n BackEnd -g TestRG4 --address-prefix 10.42.0.0/24 
   ```
4. Utwórz podsieć bramy.

   ```azurecli
   az network vnet subnet create --vnet-name TestVNet4 -n GatewaySubnet -g TestRG4 --address-prefix 10.42.255.0/27
   ```
5. Prześlij żądanie dotyczące publicznego adresu IP.

   ```azurecli
   az network public-ip create -n VNet4GWIP -g TestRG4 --allocation-method Dynamic
   ```
6. Utwórz bramę sieci wirtualnej dla sieci TestVNet4.

   ```azurecli
   az network vnet-gateway create -n VNet4GW -l westus --public-ip-address VNet4GWIP -g TestRG4 --vnet TestVNet4 --gateway-type Vpn --sku VpnGw1 --vpn-type RouteBased --no-wait
   ```

### <a name="step-4---create-the-connections"></a><a name="createconnect"></a>Krok 4 — Tworzenie połączeń

Mamy teraz dwie sieci wirtualne z bramami sieci VPN. Następny krok polega na utworzeniu połączeń VPN między bramami sieci wirtualnych. Jeśli użyto powyższych przykładów, bramy sieci wirtualnych znajdują się w różnych grupach zasobów. Gdy bramy znajdują się w różnych grupach zasobów, podczas nawiązywania połączenia należy zidentyfikować i określić identyfikatory zasobów dla każdej bramy. Jeśli sieci wirtualne znajdują się w tej samej grupie zasobów, można użyć [drugiego zestawu instrukcji](#samerg) , ponieważ nie trzeba wtedy określać identyfikatorów zasobów.

### <a name="to-connect-vnets-that-reside-in-different-resource-groups"></a><a name="diffrg"></a>Łączenie sieci wirtualnych, które znajdują się w różnych grupach zasobów

1. Pobierz identyfikator zasobu sieci VNet1GW z danych wyjściowych następującego polecenia:

   ```azurecli
   az network vnet-gateway show -n VNet1GW -g TestRG1
   ```

   W danych wyjściowych znajdź wiersz "id:". Wartości w cudzysłowie są potrzebne do utworzenia połączenia w następnej sekcji. Skopiuj te wartości do edytora tekstów (np. Notatnika), aby można je było łatwo wkleić podczas tworzenia połączenia.

   Przykładowe dane wyjściowe:

   ```
   "activeActive": false, 
   "bgpSettings": { 
    "asn": 65515, 
    "bgpPeeringAddress": "10.12.255.30", 
    "peerWeight": 0 
   }, 
   "enableBgp": false, 
   "etag": "W/\"ecb42bc5-c176-44e1-802f-b0ce2962ac04\"", 
   "gatewayDefaultSite": null, 
   "gatewayType": "Vpn", 
   "id": "/subscriptions/d6ff83d6-713d-41f6-a025-5eb76334fda9/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW", 
   "ipConfigurations":
   ```

   Skopiuj ujęte w cudzysłów wartości po elemencie **"id":**.

   ```
   "id": "/subscriptions/d6ff83d6-713d-41f6-a025-5eb76334fda9/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW"
   ```

2. Pobierz identyfikator zasobu sieci VNet4GW i skopiuj wartości do edytora tekstów.

   ```azurecli
   az network vnet-gateway show -n VNet4GW -g TestRG4
   ```

3. Utwórz połączenie z sieci wirtualnej TestVNet1 do sieci wirtualnej TestVNet4. W tym kroku zostanie utworzone połączenie z sieci wirtualnej TestVNet1 do sieci wirtualnej TestVNet4. W przykładach zastosowano odwołania do klucza współużytkowanego. Możesz wybrać własne wartości dla klucza współużytkowanego. Ważne jest, aby klucz współużytkowany był zgodny z obydwoma połączeniami. Tworzenie połączenia może trochę potrwać.

   ```azurecli
   az network vpn-connection create -n VNet1ToVNet4 -g TestRG1 --vnet-gateway1 /subscriptions/d6ff83d6-713d-41f6-a025-5eb76334fda9/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW -l eastus --shared-key "aabbcc" --vnet-gateway2 /subscriptions/d6ff83d6-713d-41f6-a025-5eb76334fda9/resourceGroups/TestRG4/providers/Microsoft.Network/virtualNetworkGateways/VNet4GW 
   ```
4. Utwórz połączenie z sieci wirtualnej TestVNet4 do sieci wirtualnej TestVNet1. Ten krok jest podobny do powyższego, jednak w jego przypadku tworzy się połączenie od sieci TestVNet4 do sieci TestVNet1. Upewnij się, że klucze współużytkowane są zgodne. Nawiązanie połączenia trwa kilka minut.

   ```azurecli
   az network vpn-connection create -n VNet4ToVNet1 -g TestRG4 --vnet-gateway1 /subscriptions/d6ff83d6-713d-41f6-a025-5eb76334fda9/resourceGroups/TestRG4/providers/Microsoft.Network/virtualNetworkGateways/VNet4GW -l westus --shared-key "aabbcc" --vnet-gateway2 /subscriptions/d6ff83d6-713d-41f6-a025-5eb76334fda9/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1G
   ```
5. Sprawdź połączenia. Zobacz sekcję [Weryfikowanie połączenia](#verify).

### <a name="to-connect-vnets-that-reside-in-the-same-resource-group"></a><a name="samerg"></a>Łączenie sieci wirtualnych, które znajdują się w tej samej grupie zasobów

1. Utwórz połączenie z sieci wirtualnej TestVNet1 do sieci wirtualnej TestVNet4. W tym kroku zostanie utworzone połączenie z sieci wirtualnej TestVNet1 do sieci wirtualnej TestVNet4. Grupy zasobów w przykładach są takie same. W przykładach zastosowano również odwołania do klucza współużytkowanego. Dla klucza współużytkowanego można użyć własnych wartości, ale klucz ten musi być zgodny w przypadku obu połączeń. Tworzenie połączenia może trochę potrwać.

   ```azurecli
   az network vpn-connection create -n VNet1ToVNet4 -g TestRG1 --vnet-gateway1 VNet1GW -l eastus --shared-key "eeffgg" --vnet-gateway2 VNet4GW
   ```
2. Utwórz połączenie z sieci wirtualnej TestVNet4 do sieci wirtualnej TestVNet1. Ten krok jest podobny do powyższego, jednak w jego przypadku tworzy się połączenie od sieci TestVNet4 do sieci TestVNet1. Upewnij się, że klucze współużytkowane są zgodne. Nawiązanie połączenia trwa kilka minut.

   ```azurecli
   az network vpn-connection create -n VNet4ToVNet1 -g TestRG1 --vnet-gateway1 VNet4GW -l eastus --shared-key "eeffgg" --vnet-gateway2 VNet1GW
   ```
3. Sprawdź połączenia. Zobacz sekcję [Weryfikowanie połączenia](#verify).

## <a name="connect-vnets-that-are-in-different-subscriptions"></a><a name="difsub"></a>Łączenie sieci wirtualnych, które należą do różnych subskrypcji

W tym scenariuszu nawiązywane jest połączenie między sieciami wirtualnymi TestVNet1 i TestVNet5. Sieci wirtualne znajdują się w różnych subskrypcjach. Subskrypcje nie muszą być skojarzone z tą samą dzierżawą usługi Active Directory. Czynności opisane dla tej konfiguracji dodają dodatkowe połączenie między sieciami wirtualnymi, aby można było nawiązać połączenie między sieciami TestVNet1 i TestVNet5.

### <a name="step-5---create-and-configure-testvnet1"></a><a name="TestVNet1diff"></a>Krok 5 — Tworzenie i konfigurowanie sieci TestVNet1

Te instrukcje są kontynuacją kroków z poprzednich sekcji. Należy wykonać [krok 1](#Connect) i [krok 2](#TestVNet1) , aby utworzyć i skonfigurować sieci testvnet1 oraz VPN Gateway dla sieci testvnet1. W przypadku tej konfiguracji nie trzeba tworzyć sieci TestVNet4 z poprzedniej sekcji, chociaż jeśli zostanie ona utworzona, nie będzie powodować żadnych konfliktów z tymi krokami. Po ukończeniu kroków 1 i 2 przejdź do kroku 6 (poniżej).

### <a name="step-6---verify-the-ip-address-ranges"></a><a name="verifyranges"></a>Krok 6 — Weryfikowanie zakresów adresów IP

Podczas tworzenia dodatkowych połączeń, ważne jest, by sprawdzić, czy przestrzeń adresów IP nowej sieci wirtualnej nie nakłada się na żaden z pozostałych zakresów sieci wirtualnych ani na żaden z zakresów bramy sieci lokalnej. Na potrzeby tego ćwiczenia można zastosować następujące wartości dla sieci wirtualnej TestVNet5:

**Wartości dla sieci TestVNet5:**

* Nazwa sieci wirtualnej: TestVNet5
* Grupa zasobów: TestRG5
* Lokalizacja: Japan East
* TestVNet5: 10.51.0.0/16 & 10.52.0.0/16
* FrontEnd: 10.51.0.0/24
* BackEnd: 10.52.0.0/24
* GatewaySubnet: 10.52.255.0.0/27
* GatewayName: VNet5GW
* Publiczny adres IP: VNet5GWIP
* VPNType: RouteBased
* Połączenie: VNet5toVNet1
* ConnectionType: VNet2VNet

### <a name="step-7---create-and-configure-testvnet5"></a><a name="TestVNet5"></a>Krok 7 — Tworzenie i konfigurowanie sieci TestVNet5

Ten krok należy wykonać w kontekście nowej subskrypcji (Subskrypcja 5). Tę część procedury może wykonać administrator w innej organizacji, która jest właścicielem subskrypcji. Aby przełączać się między subskrypcjami `az account list --all` , użyj, aby wyświetlić listę subskrypcji dostępnych dla Twojego konta, a następnie użyj opcji, `az account set --subscription <subscriptionID>` Aby przełączyć się na subskrypcję, której chcesz użyć.

1. Upewnij się, czy nastąpiło połączenie z Subskrypcją 5, a następnie utwórz grupę zasobów.

   ```azurecli
   az group create -n TestRG5  -l japaneast
   ```
2. Utwórz sieć wirtualną TestVNet5.

   ```azurecli
   az network vnet create -n TestVNet5 -g TestRG5 --address-prefix 10.51.0.0/16 -l japaneast --subnet-name FrontEnd --subnet-prefix 10.51.0.0/24
   ```

3. Dodaj podsieci.

   ```azurecli
   az network vnet update -n TestVNet5 --address-prefixes 10.51.0.0/16 10.52.0.0/16 -g TestRG5
   az network vnet subnet create --vnet-name TestVNet5 -n BackEnd -g TestRG5 --address-prefix 10.52.0.0/24
   ```

4. Dodaj podsieć bramy.

   ```azurecli
   az network vnet subnet create --vnet-name TestVNet5 -n GatewaySubnet -g TestRG5 --address-prefix 10.52.255.0/27
   ```

5. Prześlij żądanie dotyczące publicznego adresu IP.

   ```azurecli
   az network public-ip create -n VNet5GWIP -g TestRG5 --allocation-method Dynamic
   ```
6. Utworzenie bramy sieci TestVNet5

   ```azurecli
   az network vnet-gateway create -n VNet5GW -l japaneast --public-ip-address VNet5GWIP -g TestRG5 --vnet TestVNet5 --gateway-type Vpn --sku VpnGw1 --vpn-type RouteBased --no-wait
   ```

### <a name="step-8---create-the-connections"></a><a name="connections5"></a>Krok 8 — Tworzenie połączeń

Ze względu na to, że bramy należą do różnych subskrypcji, zastosowano rozbicie na dwie sesje interfejsu wiersza polecenia oznaczone jako **[Subskrypcja 1]** i **[Subskrypcja 5]**. Aby przełączać się między subskrypcjami `az account list --all` , użyj, aby wyświetlić listę subskrypcji dostępnych dla Twojego konta, a następnie użyj opcji, `az account set --subscription <subscriptionID>` Aby przełączyć się na subskrypcję, której chcesz użyć.

1. **[Subskrypcja 1]** Zaloguj się i połącz z Subskrypcją 1. Uruchom następujące polecenie, aby uzyskać nazwę i identyfikator bramy z danych wyjściowych:

   ```azurecli
   az network vnet-gateway show -n VNet1GW -g TestRG1
   ```

   Skopiuj dane wyjściowe z wiersza "id:". Wyślij identyfikator i nazwę bramy sieci wirtualnej (VNet1GW) do administratora Subskrypcji 5 za pomocą poczty e-mail lub innej metody.

   Przykładowe dane wyjściowe:

   ```
   "id": "/subscriptions/d6ff83d6-713d-41f6-a025-5eb76334fda9/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW"
   ```

2. **[Subskrypcja 5]** Zaloguj się i połącz z Subskrypcją 5. Uruchom następujące polecenie, aby uzyskać nazwę i identyfikator bramy z danych wyjściowych:

   ```azurecli
   az network vnet-gateway show -n VNet5GW -g TestRG5
   ```

   Skopiuj dane wyjściowe z wiersza "id:". Wyślij identyfikator i nazwę bramy sieci wirtualnej (VNet5GW) do administratora Subskrypcji 1 za pomocą poczty e-mail lub innej metody.

3. **[Subskrypcja 1]** W tym kroku zostanie utworzone połączenie z sieci wirtualnej TestVNet1 do sieci wirtualnej TestVNet5. Dla klucza współużytkowanego można użyć własnych wartości, ale klucz ten musi być zgodny w przypadku obu połączeń. Tworzenie połączenia może nieco potrwać. Połącz się z Subskrypcją 1.

   ```azurecli
   az network vpn-connection create -n VNet1ToVNet5 -g TestRG1 --vnet-gateway1 /subscriptions/d6ff83d6-713d-41f6-a025-5eb76334fda9/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW -l eastus --shared-key "eeffgg" --vnet-gateway2 /subscriptions/e7e33b39-fe28-4822-b65c-a4db8bbff7cb/resourceGroups/TestRG5/providers/Microsoft.Network/virtualNetworkGateways/VNet5GW
   ```

4. **[Subskrypcja 5]** Ten krok jest podobny do powyższego, jednak w jego przypadku tworzy się połączenie z sieci TestVNet5 do sieci TestVNet1. Upewnij się, że klucze współużytkowane są zgodne i że łączysz się z Subskrypcją 5.

   ```azurecli
   az network vpn-connection create -n VNet5ToVNet1 -g TestRG5 --vnet-gateway1 /subscriptions/e7e33b39-fe28-4822-b65c-a4db8bbff7cb/resourceGroups/TestRG5/providers/Microsoft.Network/virtualNetworkGateways/VNet5GW -l japaneast --shared-key "eeffgg" --vnet-gateway2 /subscriptions/d6ff83d6-713d-41f6-a025-5eb76334fda9/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW
   ```

## <a name="verify-the-connections"></a><a name="verify"></a>Weryfikowanie połączeń
[!INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)]

[!INCLUDE [verify connections](../../includes/vpn-gateway-verify-connection-cli-rm-include.md)]

## <a name="vnet-to-vnet-faq"></a><a name="faq"></a>Często zadawane pytania dotyczące połączeń między sieciami wirtualnymi
[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]

## <a name="next-steps"></a>Następne kroki

* Po zakończeniu procesu nawiązywania połączenia można dodać do sieci wirtualnych maszyny wirtualne. Aby uzyskać więcej informacji, zobacz [dokumentację dotyczącą maszyn wirtualnych](../index.yml).
* Informacje na temat protokołu BGP można znaleźć w artykułach [BGP Overview](vpn-gateway-bgp-overview.md) (Omówienie protokołu BGP) i [How to configure BGP](vpn-gateway-bgp-resource-manager-ps.md) (Konfigurowanie protokołu BGP).