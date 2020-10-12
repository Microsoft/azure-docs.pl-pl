---
title: 'Azure ExpressRoute: tabele ARP — Rozwiązywanie problemów'
description: Ta strona zawiera instrukcje dotyczące pobierania tabel ARP dla obwodu usługi ExpressRoute
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: troubleshooting
ms.date: 01/30/2017
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: 9272bb8bac2054d7a02a7eac8c214395a86ceebf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89394860"
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

Tabela ARP zawiera mapowanie adresów IPv4 i adresów MAC dla określonej komunikacji równorzędnej. Tabela ARP dla komunikacji równorzędnej obwodu usługi ExpressRoute zawiera następujące informacje dotyczące poszczególnych interfejsów (podstawowych i pomocniczych)

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
Przed kontynuowaniem upewnij się, że masz następujące czynności

* Prawidłowy obwód ExpressRoute skonfigurowany przy użyciu co najmniej jednej komunikacji równorzędnej. Obwód musi być w pełni skonfigurowany przez dostawcę połączenia. Użytkownik (lub dostawca połączenia) musi skonfigurować co najmniej jedną komunikację równorzędną (prywatną platformy Azure, publiczną i Microsoft) w tym obwodzie.
* Zakresy adresów IP używane do konfigurowania komunikacji równorzędnej (prywatny Azure, Azure Public i Microsoft). Zapoznaj się z przykładami przypisywania adresów IP na [stronie Wymagania dotyczące routingu ExpressRoute](expressroute-routing.md) , aby uzyskać informacje na temat sposobu mapowania adresów IP na interfejsy po stronie i po stronie ExpressRoute. Informacje na temat konfiguracji komunikacji równorzędnej można uzyskać, przeglądając [stronę konfiguracji komunikacji równorzędnej ExpressRoute](expressroute-howto-routing-arm.md).
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
* Tabela ARP będzie zawierać wpis dla lokalnej strony z prawidłowym adresem IP i adresem MAC oraz podobnym wpisem dla strony firmy Microsoft. 
* Ostatni oktet lokalnego adresu IP zawsze będzie liczbą nieparzystą.
* Ostatni oktet adresu IP firmy Microsoft zawsze będzie liczbą parzystą.
* Ten sam adres MAC zostanie wyświetlony po stronie firmy Microsoft dla wszystkich 3 komunikacji równorzędnych (podstawowy/pomocniczy). 

```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------    
 10 On-Prem           65.0.0.1   ffff.eeee.dddd
  0 Microsoft         65.0.0.2   aaaa.bbbb.cccc
```

### <a name="arp-table-when-on-premises--connectivity-provider-side-has-problems"></a>Tabela ARP, gdy po stronie dostawcy usług lokalnych/łączności występuje problem
W przypadku problemów związanych z dostawcą lokalnym lub połączeniem można zobaczyć, że w tabeli ARP zostanie wyświetlony tylko jeden wpis lub na lokalnym adresie MAC będzie wyświetlana wartość niepełna. Spowoduje to wyświetlenie mapowania między adresem MAC i adresem IP używanym po stronie firmy Microsoft. 
  
```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------    
  0 Microsoft         65.0.0.2   aaaa.bbbb.cccc
```

lub
       
```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------   
  0 On-Prem           65.0.0.1   Incomplete
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
* Weryfikowanie konfiguracji warstwy 3 dla obwodu usługi ExpressRoute
  * Pobierz Podsumowanie trasy, aby określić stan sesji protokołu BGP 
  * Pobierz tabelę tras, aby określić, które prefiksy są anonsowane w ExpressRoute
* Sprawdź poprawność transferu danych, przeglądając/wychodzące bajty
* Otwórz bilet pomocy technicznej w [pomocy technicznej firmy Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) , jeśli nadal występują problemy.

