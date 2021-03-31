---
title: 'Azure ExpressRoute: tabele ARP — Rozwiązywanie problemów: klasyczny'
description: Ta strona zawiera instrukcje dotyczące pobierania tabel ARP dla modelu wdrożenia ExpressRoute — klasyczny.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: troubleshooting
ms.date: 01/30/2017
ms.author: duau
ms.openlocfilehash: 7c83b6d25936e184c70d4bf0a51636a3986bf4b2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "89394843"
---
# <a name="getting-arp-tables-in-the-classic-deployment-model"></a>Pobieranie tabel ARP w klasycznym modelu wdrażania
> [!div class="op_single_selector"]
> * [Program PowerShell — model usługi Resource Manager](expressroute-troubleshooting-arp-resource-manager.md)
> * [PowerShell — model klasyczny](expressroute-troubleshooting-arp-classic.md)
> 
> 

W tym artykule omówiono procedurę pobierania tabel protokołu ARP (Address Resolution Protocol) dla obwodu usługi Azure ExpressRoute.

> [!IMPORTANT]
> Ten dokument ma pomóc w diagnozowaniu i rozwiązywaniu prostych problemów. Nie jest to zamiennik dla pomocy technicznej firmy Microsoft. Jeśli nie możesz rozwiązać problemu, korzystając z poniższych wskazówek, Otwórz żądanie pomocy technicznej w [Microsoft Azure pomocy i obsługi technicznej](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
> 
> 

## <a name="address-resolution-protocol-arp-and-arp-tables"></a>Protokół ARP (Address Resolution Protocol) i tabele ARP
ARP to protokół warstwy 2, który jest zdefiniowany w [dokumencie RFC 826](https://tools.ietf.org/html/rfc826). Protokół ARP jest używany do mapowania adresu IP (adres MAC) na adres.

Tabela ARP zawiera mapowanie adresów IPv4 i adresów MAC dla określonej komunikacji równorzędnej. Tabela ARP dla komunikacji równorzędnej obwodu usługi ExpressRoute zawiera następujące informacje dotyczące poszczególnych interfejsów (podstawowych i pomocniczych):

1. Mapowanie adresu IP lokalnego interfejsu routera na adres MAC
2. Mapowanie adresu IP interfejsu ExpressRoute router na adres MAC
3. Wiek mapowania

Tabele ARP mogą pomóc w weryfikacji konfiguracji warstwy 2 i rozwiązywaniu problemów z podstawowymi problemami z łącznością warstwy 2.

Poniżej znajduje się przykład tabeli ARP:

```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------    
 10 On-Prem           10.0.0.1   ffff.eeee.dddd
  0 Microsoft         10.0.0.2   aaaa.bbbb.cccc
```


W poniższej sekcji znajdują się informacje o sposobie wyświetlania tabel ARP, które są widoczne dla routerów brzegowych ExpressRoute.

## <a name="prerequisites-for-using-arp-tables"></a>Wymagania wstępne dotyczące korzystania z tabel ARP
Przed kontynuowaniem upewnij się, że masz następujące elementy:

* Prawidłowy obwód ExpressRoute skonfigurowany przy użyciu co najmniej jednej komunikacji równorzędnej. Obwód musi być w pełni skonfigurowany przez dostawcę połączenia. Użytkownik (lub dostawca połączenia) musi skonfigurować co najmniej jedną komunikację równorzędną (prywatną, publiczną platformy Azure lub firmę Microsoft) w tym obwodzie.
* Zakresy adresów IP, które są używane do konfigurowania komunikacji równorzędnej (prywatne platformy Azure, publiczne platformy Azure i Microsoft). Zapoznaj się z przykładami przypisywania adresów IP na [stronie Wymagania dotyczące routingu ExpressRoute](expressroute-routing.md) , aby uzyskać informacje na temat sposobu mapowania adresów IP na interfejsy po stronie i po stronie ExpressRoute. Informacje o konfiguracji komunikacji równorzędnej można uzyskać, przeglądając [stronę konfiguracji komunikacji równorzędnej ExpressRoute](expressroute-howto-routing-classic.md).
* Informacje od zespołu sieciowego lub dostawcy łączności dotyczące adresów MAC interfejsów, które są używane z tymi adresami IP.
* Najnowszy moduł programu Windows PowerShell dla platformy Azure (wersja 1,50 lub nowsza).

## <a name="arp-tables-for-your-expressroute-circuit"></a>Tabele ARP dla obwodu usługi ExpressRoute
Ta sekcja zawiera instrukcje dotyczące wyświetlania tabel ARP dla każdego typu komunikacji równorzędnej przy użyciu programu PowerShell. Przed kontynuowaniem użytkownik lub dostawca łączności musi skonfigurować komunikację równorzędną. Każdy obwód ma dwie ścieżki (podstawowe i pomocnicze). Tabelę ARP można sprawdzić niezależnie od ścieżki.

### <a name="arp-tables-for-azure-private-peering"></a>Tabele ARP dla prywatnej komunikacji równorzędnej Azure
Następujące polecenie cmdlet udostępnia tabele ARP dla prywatnej komunikacji równorzędnej Azure:

```azurepowershell
# Required variables
$ckt = "<your Service Key here>

# ARP table for Azure private peering--primary path
Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Private -Path Primary

# ARP table for Azure private peering--secondary path
Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Private -Path Secondary
```

Poniżej przedstawiono przykładowe dane wyjściowe dla jednej ze ścieżek:

```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------    
 10 On-Prem           10.0.0.1   ffff.eeee.dddd
  0 Microsoft         10.0.0.2   aaaa.bbbb.cccc
```


### <a name="arp-tables-for-azure-public-peering"></a>Tabele ARP dla publicznej komunikacji równorzędnej Azure:
Następujące polecenie cmdlet udostępnia tabele ARP dla publicznej komunikacji równorzędnej Azure:

```azurepowershell
# Required variables
$ckt = "<your Service Key here>

# ARP table for Azure public peering--primary path
Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Public -Path Primary

# ARP table for Azure public peering--secondary path
Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Public -Path Secondary
```

Poniżej przedstawiono przykładowe dane wyjściowe dla jednej ze ścieżek:

```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------    
 10 On-Prem           10.0.0.1   ffff.eeee.dddd
  0 Microsoft         10.0.0.2   aaaa.bbbb.cccc
```


Poniżej przedstawiono przykładowe dane wyjściowe dla jednej ze ścieżek:

```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------    
 10 On-Prem           64.0.0.1   ffff.eeee.dddd
  0 Microsoft         64.0.0.2   aaaa.bbbb.cccc
```


### <a name="arp-tables-for-microsoft-peering"></a>Tabele ARP dla komunikacji równorzędnej firmy Microsoft
Następujące polecenie cmdlet udostępnia tabele ARP dla komunikacji równorzędnej firmy Microsoft:

```azurepowershell
# ARP table for Microsoft peering--primary path
Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Microsoft -Path Primary

# ARP table for Microsoft peering--secondary path
Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Microsoft -Path Secondary
```


Poniżej przedstawiono przykładowe dane wyjściowe dla jednej ze ścieżek:

```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------    
 10 On-Prem           65.0.0.1   ffff.eeee.dddd
  0 Microsoft         65.0.0.2   aaaa.bbbb.cccc
```


## <a name="how-to-use-this-information"></a>Jak korzystać z tych informacji
Tabela ARP komunikacji równorzędnej może służyć do weryfikowania konfiguracji i łączności warstwy 2. Ta sekcja zawiera omówienie sposobu wyszukiwania tabel ARP w różnych scenariuszach.

### <a name="arp-table-when-a-circuit-is-in-an-operational-expected-state"></a>Tabela ARP, gdy obwód jest w stanie operacyjnym (oczekiwanym)
* Tabela ARP zawiera wpis dla strony lokalnej z prawidłowym adresem IP i MAC oraz podobny wpis dla strony firmy Microsoft.
* Ostatni oktet lokalnego adresu IP jest zawsze liczbą nieparzystą.
* Ostatni oktet adresu IP firmy Microsoft jest zawsze liczbą parzystą.
* Ten sam adres MAC pojawia się po stronie firmy Microsoft dla wszystkich trzech komunikacji równorzędnych (podstawowy/pomocniczy).

```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------    
 10 On-Prem           65.0.0.1   ffff.eeee.dddd
  0 Microsoft         65.0.0.2   aaaa.bbbb.cccc
```

### <a name="arp-table-when-its-on-premises-or-when-the-connectivity-provider-side-has-problems"></a>Tabela ARP, gdy jest ona lokalna lub gdy po stronie dostawcy połączeń występują problemy
 W tabeli ARP pojawia się tylko jeden wpis. Pokazuje mapowanie między adresem MAC a adresem IP, który jest używany po stronie firmy Microsoft.

```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------    
   0 Microsoft         65.0.0.2  aaaa.bbbb.cccc
```

> [!NOTE]
> Jeśli wystąpi problem podobny do tego, Otwórz żądanie pomocy technicznej z dostawcą połączenia, aby je rozwiązać.
> 
> 

### <a name="arp-table-when-the-microsoft-side-has-problems"></a>Tabela ARP, gdy strona firmy Microsoft ma problemy
* Nie zostanie wyświetlona tabela ARP wyświetlana dla komunikacji równorzędnej, jeśli występują problemy po stronie firmy Microsoft.
* Otwórz żądanie pomocy technicznej, korzystając z [Microsoft Azure pomocy i obsługi technicznej](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Określ, że masz problem z łącznością z warstwą 2.

## <a name="next-steps"></a>Następne kroki
* Weryfikowanie konfiguracji warstwy 3 dla obwodu usługi ExpressRoute:
  * Pobierz Podsumowanie trasy, aby określić stan sesji protokołu BGP.
  * Pobierz tabelę tras, aby określić, które prefiksy są anonsowane w ExpressRoute.
* Sprawdź poprawność transferu danych, przeglądając i wychodząc w bajtach.
* Otwórz żądanie pomocy technicznej, korzystając z [Microsoft Azure pomocy i obsługi technicznej](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) , jeśli nadal występują problemy.

