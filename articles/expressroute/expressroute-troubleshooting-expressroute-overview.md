---
title: 'Azure ExpressRoute: weryfikowanie łączności — Przewodnik rozwiązywania problemów'
description: Ta strona zawiera instrukcje dotyczące rozwiązywania problemów i weryfikowania kompleksowej łączności obwodu usługi ExpressRoute.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: troubleshooting
ms.date: 10/31/2019
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: af4ef156cccded6afe2db09628446a6ffe1ad53a
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/01/2020
ms.locfileid: "92204643"
---
# <a name="verifying-expressroute-connectivity"></a>Weryfikowanie połączenia usługi ExpressRoute
Ten artykuł ułatwia sprawdzanie i rozwiązywanie problemów z łącznością ExpressRoute. ExpressRoute rozszerza sieć lokalną do chmury firmy Microsoft przez połączenie prywatne, które jest powszechnie obsługiwane przez dostawcę połączenia. ExpressRoute łączności tradycyjnie obejmuje trzy oddzielne strefy sieciowe w następujący sposób:

-   Sieć klienta
-   Sieć dostawcy
-   Centrum danych firmy Microsoft

> [!NOTE]
> W modelu łączności bezpośredniej ExpressRoute (oferowany przy przepustowości 10/100 GB/s) klienci mogą bezpośrednio łączyć się z portem routerów Microsoft Enterprise Edge (MSEE). W związku z tym w modelu łączności bezpośredniej istnieją tylko strefy klienta i sieci firmy Microsoft.
>


Celem tego dokumentu jest ułatwienie użytkownikowi zidentyfikowania, czy występuje problem z łącznością. W związku z tym, aby pomóc w uzyskaniu pomocy technicznej od odpowiedniego zespołu w celu rozwiązania problemu. Jeśli pomoc techniczna firmy Microsoft jest wymagana do rozwiązania problemu, należy otworzyć bilet pomocy technicznej w [Pomoc techniczna firmy Microsoft][Support].

> [!IMPORTANT]
> Ten dokument ma pomóc w diagnozowaniu i rozwiązywaniu prostych problemów. Nie jest to zamiennik dla pomocy technicznej firmy Microsoft. Otwórz bilet pomocy technicznej z [Pomoc techniczna firmy Microsoft][Support] , jeśli nie możesz rozwiązać problemu przy użyciu podanych wskazówek.
>
>

## <a name="overview"></a>Omówienie
Na poniższym diagramie przedstawiono logiczne połączenie sieci klienta z siecią firmy Microsoft przy użyciu ExpressRoute.
[![jedno]][1]

Na powyższym diagramie liczba wskazuje kluczowe punkty sieciowe. Te punkty sieciowe są przywoływane w tym artykule o ile ich skojarzona liczba. W zależności od modelu łączności ExpressRoute — połączenie z programem Exchange w chmurze, punkt-punkt połączenia Ethernet lub dowolne z nich (IPVPN) — punkty sieciowe 3 i 4 mogą być przełącznikami (urządzeniami warstwy 2) lub routerami (urządzeniami warstwy 3). W modelu łączności bezpośredniej nie ma punktów sieciowych 3 i 4; Zamiast tego (2) są bezpośrednio połączone z MSEE za pośrednictwem ciemnego włókna. Najważniejsze przedstawiane punkty sieci są następujące:

1.  Urządzenie obliczeniowe klienta (na przykład serwer lub komputer)
2.  CE: routery brzegowe klienta 
3.  PEs (CE): dostawca/przełączniki brzegowe dostawcy, które są skierowane do routerów brzegowych klienta. Określany jako PE-CEs w tym dokumencie.
4.  PEs (MSEE): dostawcy krawędzi/przełączniki, które są MSEE. Określany jako PE-MSEEs w tym dokumencie.
5.  MSEE: routery ExpressRoute firmy Microsoft Enterprise Edge (MSEE)
6.  Brama Virtual Network (VNet)
7.  Urządzenie obliczeniowe w sieci wirtualnej platformy Azure

Jeśli używane są wspólne lokalizacje usługi Exchange w chmurze, sieci Ethernet typu punkt-punkt lub bezpośrednie modele połączeń, usługa rejestracji urządzeń (2) ustanawia komunikację równorzędną BGP z MSEE (5). 

Jeśli używany jest model łączności "dowolny do dowolnego" (IPVPN), PE-MSEEs (4) ustanowić komunikację równorzędną BGP z MSEE (5). PE-MSEEs propagować trasy otrzymane od firmy Microsoft z powrotem do sieci klienta za pośrednictwem sieci dostawcy usług IPVPN.

> [!NOTE]
>Aby zapewnić wysoką dostępność, firma Microsoft ustanawia w pełni nadmiarowe połączenie równoległe między MSEE (5) i PE-MSEEs (4) pary. W pełni nadmiarowa równoległa ścieżka sieciowa jest również zachęcana między siecią klienta a parą PE-CEs. Aby uzyskać więcej informacji na temat wysokiej dostępności, zobacz artykuł [projektowanie pod kątem wysokiej dostępności za pomocą ExpressRoute][HA]
>
>

Poniżej przedstawiono kroki logiczne, w których można rozwiązywać problemy z ExpressRoute obwodu:

* [Weryfikacja aprowizacji i stanu obwodu](#verify-circuit-provisioning-and-state)
  
* [Weryfikuj konfigurację komunikacji równorzędnej](#validate-peering-configuration)
  
* [Weryfikowanie protokołu ARP](#validate-arp)
  
* [Weryfikowanie protokołu BGP i tras na MSEE](#validate-bgp-and-routes-on-the-msee)
  
* [Potwierdzenie przepływu ruchu](#confirm-the-traffic-flow)


## <a name="verify-circuit-provisioning-and-state"></a>Weryfikacja aprowizacji i stanu obwodu
Zainicjowanie obwodu usługi ExpressRoute ustanawia nadmiarowe połączenia warstwy 2 między usługami ce/PE-MSEE (2)/(4) i MSEE (5). Aby uzyskać więcej informacji na temat tworzenia, modyfikowania, inicjowania obsługi i weryfikowania obwodu usługi ExpressRoute, zobacz artykuł [Tworzenie i modyfikowanie obwodu usługi ExpressRoute][CreateCircuit].

>[!TIP]
>Klucz usługi jednoznacznie identyfikuje obwód ExpressRoute. Jeśli potrzebujesz pomocy od firmy Microsoft lub od partnera ExpressRoute, aby rozwiązać problem z ExpressRoute, Podaj klucz usługi, aby łatwo zidentyfikować obwód.
>
>

### <a name="verification-via-the-azure-portal"></a>Weryfikacja za pomocą Azure Portal
W Azure Portal Otwórz blok obwodu ExpressRoute. W sekcji ![3][3] w bloku ExpressRoute podstawowe informacje są wyświetlane jak pokazano na poniższym zrzucie ekranu:

![4][4]    

W ExpressRoute Essentials *stan obwodu* wskazuje stan obwodu po stronie firmy Microsoft. *Stan dostawcy* wskazuje, czy *nie zainicjowano* obsługi obwodu na stronie Dostawca usług. 

Aby obwód usługi ExpressRoute był uruchomiony, musi być *włączony* *stan obwodu* , a *stan dostawcy* musi być *zainicjowany* .

> [!NOTE]
> Po skonfigurowaniu obwodu ExpressRoute, jeśli *stan obwodu* wykreśla się w stanie niewłączonym, skontaktuj się z [Pomoc techniczna firmy Microsoft][Support]. Z drugiej strony, jeśli *stan dostawcy* jest wykreślony w stanie nieaprowizacji, skontaktuj się z dostawcą usług.
>
>

### <a name="verification-via-powershell"></a>Weryfikacja za pomocą programu PowerShell
Aby wyświetlić listę wszystkich obwodów usługi ExpressRoute w grupie zasobów, użyj następującego polecenia:

```azurepowershell
Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG"
```

>[!TIP]
>Jeśli szukasz nazwy grupy zasobów, możesz ją uzyskać, wyświetlając listę wszystkich grup zasobów w subskrypcji przy użyciu polecenia *Get-AzResourceGroup*
>


Aby wybrać konkretny obwód ExpressRoute w grupie zasobów, użyj następującego polecenia:

```azurepowershell
Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
```

Przykładowa odpowiedź:

```output
Name                             : Test-ER-Ckt
ResourceGroupName                : Test-ER-RG
Location                         : westus2
Id                               : /subscriptions/***************************/resourceGroups/Test-ER-RG/providers/***********/expressRouteCircuits/Test-ER-Ckt
Etag                             : W/"################################"
ProvisioningState                : Succeeded
Sku                              : {
                                    "Name": "Standard_UnlimitedData",
                                    "Tier": "Standard",
                                    "Family": "UnlimitedData"
                                    }
CircuitProvisioningState         : Enabled
ServiceProviderProvisioningState : Provisioned
ServiceProviderNotes             : 
ServiceProviderProperties        : {
                                    "ServiceProviderName": "****",
                                    "PeeringLocation": "******",
                                    "BandwidthInMbps": 100
                                    }
ServiceKey                       : **************************************
Peerings                         : []
Authorizations                   : []
```

Aby potwierdzić, czy obwód ExpressRoute działa, należy zwrócić szczególną uwagę na następujące pola:

```output
CircuitProvisioningState         : Enabled
ServiceProviderProvisioningState : Provisioned
```

> [!NOTE]
> Po skonfigurowaniu obwodu ExpressRoute, jeśli *stan obwodu* wykreśla się w stanie niewłączonym, skontaktuj się z [Pomoc techniczna firmy Microsoft][Support]. Z drugiej strony, jeśli *stan dostawcy* jest wykreślony w stanie nieaprowizacji, skontaktuj się z dostawcą usług.
>
>

## <a name="validate-peering-configuration"></a>Weryfikuj konfigurację komunikacji równorzędnej
Po zakończeniu aprowizacji obwodu usługi ExpressRoute przez dostawcę usług można utworzyć wiele konfiguracji routingu opartych na eBGP w ramach obwodu ExpressRoute między usługą rejestracji certyfikatów (1)/(4) i MSEE (5). Każdy obwód usługi ExpressRoute może mieć: prywatna Komunikacja równorzędna Azure (ruch do prywatnych sieci wirtualnych na platformie Azure) i/lub Komunikacja równorzędna firmy Microsoft (ruch do publicznych punktów końcowych PaaS i SaaS). Aby uzyskać więcej informacji na temat tworzenia i modyfikowania konfiguracji routingu, zobacz artykuł [Tworzenie i modyfikowanie routingu dla obwodu usługi ExpressRoute][CreatePeering].

### <a name="verification-via-the-azure-portal"></a>Weryfikacja za pomocą Azure Portal

> [!NOTE]
> W modelu łączności IPVPN dostawcy usług obsługują obowiązek konfigurowania komunikacji równorzędnej (usługi warstwy 3). W takim modelu, po skonfigurowaniu komunikacji równorzędnej przez dostawcę usług i jeśli Komunikacja równorzędna jest pusta w portalu, spróbuj odświeżyć konfigurację obwodu za pomocą przycisku Odśwież w portalu. Ta operacja spowoduje ściągnięcie bieżącej konfiguracji routingu z obwodu. 
>

W Azure Portal stan komunikacji równorzędnej obwodu ExpressRoute można sprawdzić pod blokiem obwodu ExpressRoute. W sekcji ![3][3] bloku ExpressRoute Komunikacja równorzędna będzie wyświetlana jak pokazano na poniższym zrzucie ekranu:

![5][5]

W powyższym przykładzie, jako że zanotowano zainicjowanie prywatnej komunikacji równorzędnej platformy Azure, nie zainicjowano obsługi komunikacji równorzędnej usług Azure Public i Microsoft. Pomyślnie zainicjowany kontekst komunikacji równorzędnej również będzie zawierał podstawową i pomocniczą podsieć punkt-punkt. Podsieci/30 są używane dla adresu IP interfejsu MSEE i CE/PE-MSEE. W przypadku obsługi komunikacji równorzędnej ta lista wskazuje również, kto ostatnio zmodyfikował konfigurację. 

> [!NOTE]
> Jeśli Włączanie komunikacji równorzędnej nie powiedzie się, sprawdź, czy przypisane podsieci podstawowej i pomocniczej są zgodne z konfiguracją w powiązanym elemencie CE/PE-MSEE. Sprawdź również, czy poprawne *VlanId* , *AzureASN* i *PeerASN* są używane w MSEE, i czy te wartości są mapowane na te, które są używane w połączonym ce/PE-MSEE. Jeśli zostanie wybrane Mieszanie MD5, klucz współużytkowany powinien być taki sam w parze MSEE i PE-MSEE/CE. Wcześniej skonfigurowany klucz współużytkowany nie zostanie wyświetlony ze względów bezpieczeństwa. Należy zmienić dowolną konfigurację na routerze MSEE, zapoznaj się z tematem [Tworzenie i modyfikowanie routingu dla obwodu usługi ExpressRoute][CreatePeering].  
>

> [!NOTE]
> W przypadku podsieci/30 przypisanej do interfejsu firma Microsoft wybierze drugi używany adres IP podsieci dla interfejsu MSEE. W związku z tym upewnij się, że pierwszy używany adres IP podsieci został przypisany do komunikacji równorzędnej CE/PE-MSEE.
>


### <a name="verification-via-powershell"></a>Weryfikacja za pomocą programu PowerShell
Aby uzyskać szczegółowe informacje o konfiguracji prywatnej komunikacji równorzędnej Azure, użyj następujących poleceń:

```azurepowershell
$ckt = Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt
```

Przykładowa odpowiedź dla pomyślnie skonfigurowanej prywatnej komunikacji równorzędnej:

```output
Name                       : AzurePrivatePeering
Id                         : /subscriptions/***************************/resourceGroups/Test-ER-RG/providers/***********/expressRouteCircuits/Test-ER-Ckt/peerings/AzurePrivatePeering
Etag                       : W/"################################"
PeeringType                : AzurePrivatePeering
AzureASN                   : 12076
PeerASN                    : 123##
PrimaryPeerAddressPrefix   : 172.16.0.0/30
SecondaryPeerAddressPrefix : 172.16.0.4/30
PrimaryAzurePort           : 
SecondaryAzurePort         : 
SharedKey                  : 
VlanId                     : 200
MicrosoftPeeringConfig     : null
ProvisioningState          : Succeeded
```

 Pomyślnie włączono kontekst komunikacji równorzędnej będzie zawierał prefiksy adresów podstawowych i pomocniczych. Podsieci/30 są używane dla adresu IP interfejsu MSEE i CE/PE-MSEE.

Aby uzyskać szczegółowe informacje o konfiguracji publicznej komunikacji równorzędnej platformy Azure, użyj następujących poleceń:

```azurepowershell
$ckt = Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt
```

Aby uzyskać szczegółowe informacje dotyczące konfiguracji komunikacji równorzędnej firmy Microsoft, użyj następujących poleceń:

```azurepowershell
$ckt = Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
Get-AzExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt
```

Jeśli Komunikacja równorzędna nie zostanie skonfigurowana, zostanie wyświetlony komunikat o błędzie. Przykładowa odpowiedź, gdy podana Komunikacja równorzędna (publiczna Komunikacja równorzędna Azure w tym przykładzie) nie jest skonfigurowana w ramach obwodu:

```azurepowershell
Get-AzExpressRouteCircuitPeeringConfig : Sequence contains no matching element
At line:1 char:1
    + Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering ...
    + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
        + CategoryInfo          : CloseError: (:) [Get-AzExpr...itPeeringConfig], InvalidOperationException
        + FullyQualifiedErrorId : Microsoft.Azure.Commands.Network.GetAzureExpressRouteCircuitPeeringConfigCommand
```

> [!NOTE]
> Jeśli Włączanie komunikacji równorzędnej nie powiedzie się, sprawdź, czy przypisane podsieci podstawowej i pomocniczej są zgodne z konfiguracją w powiązanym elemencie CE/PE-MSEE. Sprawdź również, czy poprawne *VlanId* , *AzureASN* i *PeerASN* są używane w MSEE, i czy te wartości są mapowane na te, które są używane w połączonym ce/PE-MSEE. Jeśli zostanie wybrane Mieszanie MD5, klucz współużytkowany powinien być taki sam w parze MSEE i PE-MSEE/CE. Wcześniej skonfigurowany klucz współużytkowany nie zostanie wyświetlony ze względów bezpieczeństwa. Należy zmienić dowolną konfigurację na routerze MSEE, zapoznaj się z tematem [Tworzenie i modyfikowanie routingu dla obwodu usługi ExpressRoute][CreatePeering].  
>
>

> [!NOTE]
> W przypadku podsieci/30 przypisanej do interfejsu firma Microsoft wybierze drugi używany adres IP podsieci dla interfejsu MSEE. W związku z tym upewnij się, że pierwszy używany adres IP podsieci został przypisany do komunikacji równorzędnej CE/PE-MSEE.
>

## <a name="validate-arp"></a>Weryfikowanie protokołu ARP

Tabela ARP zawiera mapowanie adresów IP i adresów MAC dla określonej komunikacji równorzędnej. Tabela ARP dla komunikacji równorzędnej obwodu usługi ExpressRoute zawiera następujące informacje dotyczące poszczególnych interfejsów (podstawowych i pomocniczych):
* Mapowanie adresu IP lokalnego interfejsu routera na adres MAC
* Mapowanie adresu IP interfejsu ExpressRoute router na adres MAC
* Wiek tabel mapowania ARP może pomóc w sprawdzeniu konfiguracji warstwy 2 i rozwiązywaniu problemów z podstawowymi problemami z łącznością warstwy 2.


Zobacz [Pobieranie tabel ARP w dokumencie Menedżer zasobów model wdrażania][ARP] , aby wyświetlić tabelę ARP komunikacji równorzędnej ExpressRoute oraz jak korzystać z informacji w celu rozwiązywania problemów z łącznością warstwy 2.


## <a name="validate-bgp-and-routes-on-the-msee"></a>Weryfikowanie protokołu BGP i tras na MSEE

Aby uzyskać tabelę routingu z MSEE na ścieżce *podstawowej* kontekstu routingu *prywatnego* , użyj następującego polecenia:

```azurepowershell
Get-AzExpressRouteCircuitRouteTable -DevicePath Primary -ExpressRouteCircuitName ******* -PeeringType AzurePrivatePeering -ResourceGroupName ****
```

Przykład odpowiedzi:

```output
Network : 10.1.0.0/16
NextHop : 10.17.17.141
LocPrf  : 
Weight  : 0
Path    : 65515

Network : 10.1.0.0/16
NextHop : 10.17.17.140*
LocPrf  : 
Weight  : 0
Path    : 65515

Network : 10.2.20.0/25
NextHop : 172.16.0.1
LocPrf  : 
Weight  : 0
Path    : 123##
```

> [!NOTE]
> Jeśli stan komunikacji równorzędnej eBGP między MSEE i CE/PE-MSEE jest aktywny lub bezczynny, sprawdź, czy przypisana podstawowa i pomocnicza podsieć równorzędna jest zgodna z konfiguracją w połączonym CE/PE-MSEE. Sprawdź również, czy poprawne *VlanId* , *AzureAsn* i *PeerAsn* są używane w MSEE i czy te wartości są mapowane na te, które są używane w połączonym środowisku PE-MSEE/CE. Jeśli zostanie wybrane Mieszanie MD5, klucz współużytkowany powinien być taki sam w parze MSEE i CE/PE-MSEE. Należy zmienić dowolną konfigurację na routerze MSEE, zapoznaj się z tematem [Tworzenie i modyfikowanie routingu dla obwodu usługi ExpressRoute][CreatePeering].
>


> [!NOTE]
> Jeśli niektóre lokalizacje docelowe nie są dostępne w przypadku komunikacji równorzędnej, sprawdź tabelę tras MSEE dla odpowiedniego kontekstu komunikacji równorzędnej. Jeśli w tabeli routingu znajduje się pasujący prefiks (może być adresem IP "Jand"), sprawdź, czy na ścieżce, która blokuje ruch, znajdują się zapory/sieciowej grupy zabezpieczeń/listy ACL.
>


Poniższy przykład przedstawia odpowiedź polecenia dla komunikacji równorzędnej, która nie istnieje:

```azurepowershell
Get-AzExpressRouteCircuitRouteTable : The BGP Peering AzurePublicPeering with Service Key ********************* is not found.
StatusCode: 400
```

## <a name="confirm-the-traffic-flow"></a>Potwierdzenie przepływu ruchu
Aby uzyskać dane statystyczne dotyczące połączonej ścieżki podstawowej i pomocniczej — liczba bajtów w kontekście komunikacji równorzędnej, użyj następującego polecenia:

```azurepowershell
Get-AzExpressRouteCircuitStats -ResourceGroupName $RG -ExpressRouteCircuitName $CircuitName -PeeringType 'AzurePrivatePeering'
```

Przykładowe dane wyjściowe polecenia to:

```output
PrimaryBytesIn PrimaryBytesOut SecondaryBytesIn SecondaryBytesOut
-------------- --------------- ---------------- -----------------
     240780020       239863857        240565035         239628474
```

Przykładowe dane wyjściowe polecenia dla nieistniejącej komunikacji równorzędnej to:

```azurepowershell
Get-AzExpressRouteCircuitRouteTable : The BGP Peering AzurePublicPeering with Service Key ********************* is not found.
StatusCode: 400
```

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji i uzyskać pomoc, Skorzystaj z następujących linków:

- [pomoc techniczna firmy Microsoft][Support]
- [Tworzenie i modyfikowanie obwodu usługi ExpressRoute][CreateCircuit]
- [Tworzenie i modyfikowanie routingu dla obwodu usługi ExpressRoute][CreatePeering]

<!--Image References-->
[1]: ./media/expressroute-troubleshooting-expressroute-overview/expressroute-logical-diagram.png "łączność między logicznymi trasami ekspresowych"
[2]: ./media/expressroute-troubleshooting-expressroute-overview/portal-all-resources.png "Ikona wszystkie zasoby"
[3]: ./media/expressroute-troubleshooting-expressroute-overview/portal-overview.png "Ikona przeglądu"
[4]: ./media/expressroute-troubleshooting-expressroute-overview/portal-circuit-status.png "Przykładowy zrzut ekranu ExpressRoute Essentials"
[5]: ./media/expressroute-troubleshooting-expressroute-overview/portal-private-peering.png "Przykładowy zrzut ekranu ExpressRoute Essentials"

<!--Link References-->
[Support]: https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[CreateCircuit]: ./expressroute-howto-circuit-portal-resource-manager.md
[CreatePeering]: ./expressroute-howto-routing-portal-resource-manager.md
[ARP]: ./expressroute-troubleshooting-arp-resource-manager.md
[HA]: ./designing-for-high-availability-with-expressroute.md
[DR-Pvt]: ./designing-for-disaster-recovery-with-expressroute-privatepeering.md