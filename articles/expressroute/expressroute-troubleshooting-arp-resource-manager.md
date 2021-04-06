---
title: 'Azure ExpressRoute: tabele ARP — Rozwiązywanie problemów'
description: Ta strona zawiera instrukcje dotyczące pobierania tabel protokołu ARP (Address Resolution Protocol) dla obwodu usługi ExpressRoute
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: troubleshooting
ms.date: 12/15/2020
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: 7d8ae2c58979c66ebbbab366d172179bdeee4253
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97561583"
---
# <a name="getting-arp-tables-in-the-resource-manager-deployment-model"></a>Pobieranie tabel ARP w modelu wdrażania Menedżer zasobów
> [!div class="op_single_selector"]
> * [Program PowerShell — model usługi Resource Manager](expressroute-troubleshooting-arp-resource-manager.md)
> * [PowerShell — model klasyczny](expressroute-troubleshooting-arp-classic.md)
> 
> 

W tym artykule przedstawiono kroki, które należy wykonać, aby poznać tabelę ARP dla obwodu usługi ExpressRoute.

> [!IMPORTANT]
> Ten dokument ma pomóc w diagnozowaniu i rozwiązywaniu prostych problemów. Nie jest to zamiennik dla pomocy technicznej firmy Microsoft. Musisz otworzyć bilet pomocy technicznej z pomocą [techniczną firmy Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) , jeśli nie możesz rozwiązać problemu przy użyciu wskazówek opisanych poniżej.
> 
> 

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

## <a name="address-resolution-protocol-arp-and-arp-tables"></a>Protokół ARP (Address Resolution Protocol) i tabele ARP
Protokół ARP (Address Resolution Protocol) to protokół warstwy 2 zdefiniowany w [dokumencie RFC 826](https://tools.ietf.org/html/rfc826). Protokół ARP jest używany do mapowania adresu IP (adres MAC) przy użyciu adresu.

Tabela ARP zawiera następujące informacje dotyczące zarówno podstawowego, jak i pomocniczego interfejsu dla każdego typu komunikacji równorzędnej:

1. Mapowanie adresu IP lokalnego interfejsu routera na adres MAC
2. Mapowanie adresu IP interfejsu ExpressRoute router na adres MAC
3. Wiek mapowania

Tabele ARP mogą pomóc w sprawdzeniu konfiguracji warstwy 2 i rozwiązywaniu problemów z podstawowymi problemami z łącznością warstwy 2. 

Przykładowa tabela ARP: 

```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------    
 10 On-Prem           10.0.0.1   ffff.eeee.dddd
  0 Microsoft         10.0.0.2   aaaa.bbbb.cccc
```


Poniższa sekcja zawiera informacje na temat sposobu wyświetlania tabel ARP widocznych dla routerów brzegowych ExpressRoute. 

## <a name="prerequisites-for-learning-arp-tables"></a>Wymagania wstępne dotyczące tabel uczenia ARP
Przed kontynuowaniem upewnij się, że poniższe informacje są prawdziwe:

* Prawidłowy obwód ExpressRoute skonfigurowany przy użyciu co najmniej jednej komunikacji równorzędnej. Obwód musi być w pełni skonfigurowany przez dostawcę połączenia. Ty lub Twój dostawca połączenia musi mieć skonfigurowaną co najmniej platformę Azure prywatną, publiczną platformy Azure lub komunikację równorzędną firmy Microsoft w tym obwodzie.
* Zakresy adresów IP używane do konfigurowania komunikacji równorzędnej. Zapoznaj się z przykładami przypisywania adresów IP na [stronie Wymagania dotyczące routingu ExpressRoute](expressroute-routing.md) , aby zrozumieć, jak adresy IP są zamapowane na interfejsy. Informacje na temat konfiguracji komunikacji równorzędnej można uzyskać, przeglądając [stronę konfiguracji komunikacji równorzędnej ExpressRoute](expressroute-howto-routing-arm.md).
* Informacje od zespołu sieciowego/dostawcy łączności na adresy MAC interfejsów używanych z tymi adresami IP.
* Wymagany jest najnowszy moduł programu PowerShell dla platformy Azure (wersja 1,50 lub nowsza).

> [!NOTE]
> Jeśli dostawca usług udostępnia warstwę 3, a tabele ARP są puste w portalu/danych wyjściowych, Odśwież konfigurację obwodu za pomocą przycisku Odśwież w portalu. Ta operacja spowoduje zastosowanie odpowiedniej konfiguracji routingu w obwodzie. 
>
>

## <a name="getting-the-arp-tables-for-your-expressroute-circuit"></a>Pobieranie tabel ARP dla obwodu usługi ExpressRoute
Ta sekcja zawiera instrukcje dotyczące wyświetlania tabel ARP dla komunikacji równorzędnej za pomocą programu PowerShell. Ty lub Twój dostawca połączenia musi skonfigurować komunikację równorzędną przed dalszej dalszej realizacji. Każdy obwód ma dwie ścieżki (podstawowe i pomocnicze). Tabelę ARP można sprawdzić niezależnie od ścieżki.

### <a name="arp-tables-for-azure-private-peering"></a>Tabele ARP dla prywatnej komunikacji równorzędnej Azure
Następujące polecenie cmdlet udostępnia tabele ARP dla prywatnej komunikacji równorzędnej Azure

```azurepowershell
# Required Variables
$RG = "<Your Resource Group Name Here>"
$Name = "<Your ExpressRoute Circuit Name Here>"

# ARP table for Azure private peering - Primary path
Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePrivatePeering -DevicePath Primary

# ARP table for Azure private peering - Secondary path
Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePrivatePeering -DevicePath Secondary 
```

Przykładowe dane wyjściowe pokazano poniżej dla jednej ze ścieżek

```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------    
 10 On-Prem           10.0.0.1   ffff.eeee.dddd
  0 Microsoft         10.0.0.2   aaaa.bbbb.cccc
```


### <a name="arp-tables-for-azure-public-peering"></a>Tabele ARP dla publicznej komunikacji równorzędnej Azure
Następujące polecenie cmdlet udostępnia tabele ARP dla publicznej komunikacji równorzędnej Azure

```azurepowershell
# Required Variables
$RG = "<Your Resource Group Name Here>"
$Name = "<Your ExpressRoute Circuit Name Here>"

# ARP table for Azure public peering - Primary path
Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePublicPeering -DevicePath Primary

# ARP table for Azure public peering - Secondary path
Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePublicPeering -DevicePath Secondary 
```


Przykładowe dane wyjściowe pokazano poniżej dla jednej ze ścieżek

```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------    
 10 On-Prem           64.0.0.1   ffff.eeee.dddd
  0 Microsoft         64.0.0.2   aaaa.bbbb.cccc
```


### <a name="arp-tables-for-microsoft-peering"></a>Tabele ARP dla komunikacji równorzędnej firmy Microsoft
Następujące polecenie cmdlet udostępnia tabele ARP dla komunikacji równorzędnej firmy Microsoft

```azurepowershell
# Required Variables
$RG = "<Your Resource Group Name Here>"
$Name = "<Your ExpressRoute Circuit Name Here>"

# ARP table for Microsoft peering - Primary path
Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType MicrosoftPeering -DevicePath Primary

# ARP table for Microsoft peering - Secondary path
Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType MicrosoftPeering -DevicePath Secondary 
```


Przykładowe dane wyjściowe pokazano poniżej dla jednej ze ścieżek

```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------    
 10 On-Prem           65.0.0.1   ffff.eeee.dddd
  0 Microsoft         65.0.0.2   aaaa.bbbb.cccc
```


## <a name="how-to-use-this-information"></a>Jak korzystać z tych informacji
Tabela ARP komunikacji równorzędnej może służyć do określenia, czy ma zostać zweryfikowana konfiguracja warstwy 2 i łączność. Ta sekcja zawiera omówienie sposobu, w jaki tabele ARP będą wyglądały w różnych scenariuszach.

### <a name="arp-table-when-a-circuit-is-in-operational-state-expected-state"></a>Tabela ARP, gdy obwód jest w stanie operacyjnym (oczekiwany stan)
* Tabela ARP będzie zawierać wpis dla lokalnej strony z prawidłowym adresem IP i adresem MAC. Ta sama wartość może być widoczna dla strony firmy Microsoft. 
* Ostatni oktet lokalnego adresu IP zawsze będzie liczbą nieparzystą.
* Ostatni oktet adresu IP firmy Microsoft zawsze będzie liczbą parzystą.
* Ten sam adres MAC zostanie wyświetlony po stronie firmy Microsoft dla wszystkich trzech komunikacji równorzędnych (podstawowy/pomocniczy). 

```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------    
 10 On-Prem           65.0.0.1   ffff.eeee.dddd
  0 Microsoft         65.0.0.2   aaaa.bbbb.cccc
```

### <a name="arp-table-when-on-premises--connectivity-provider-side-has-problems"></a>Tabela ARP, gdy występuje problem po stronie lokalnej/dostawcy łączności
Jeśli wystąpi problem z dostawcą lokalnym lub połączeniem, w tabeli ARP zostanie wyświetlona jedna z dwóch rzeczy. Zobaczysz, że lokalny adres MAC Pokaż niekompletne lub widzisz tylko wpis Microsoft w tabeli ARP.
  
```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------   
  0 On-Prem           65.0.0.1   Incomplete
  0 Microsoft         65.0.0.2   aaaa.bbbb.cccc
```
lub
   
```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------    
  0 Microsoft         65.0.0.2   aaaa.bbbb.cccc
```  

> [!NOTE]
> Otwórz żądanie pomocy technicznej z dostawcą połączenia, aby debugować takie problemy. Jeśli tabela ARP nie zawiera adresów IP interfejsów mapowanych na adresy MAC, zapoznaj się z następującymi informacjami:
> 
> 1. Jeśli pierwszy adres IP podsieci/30 przypisany dla linku między MSEE-PR i MSEE jest używany w interfejsie MSEE-PR. Platforma Azure zawsze używa drugiego adresu IP dla MSEE.
> 2. Sprawdź, czy Tagi sieci VLAN klienta (C-tag) i Service (S-tag) pasują zarówno do pary MSEE-PR i MSEE.
> 

### <a name="arp-table-when-microsoft-side-has-problems"></a>Tabela ARP, gdy firma Microsoft ma problemy
* Nie zostanie wyświetlona tabela ARP wyświetlana dla komunikacji równorzędnej, jeśli występują problemy po stronie firmy Microsoft. 
* Otwórz bilet pomocy technicznej w [pomocy technicznej firmy Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Określ, że masz problem z łącznością z warstwą 2. 

## <a name="next-steps"></a>Następne kroki
* Sprawdź poprawność konfiguracji warstwy 3 dla obwodu usługi ExpressRoute.
  * Pobierz Podsumowanie trasy, aby określić stan sesji protokołu BGP.
  * Pobierz tabelę tras, aby określić, które prefiksy są anonsowane w ExpressRoute.
* Sprawdź poprawność transferu danych, przeglądając bajty we/wychodzącym.
* Otwórz bilet pomocy technicznej w [pomocy technicznej firmy Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) , jeśli nadal występują problemy.

