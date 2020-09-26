---
title: 'Konfigurowanie szyfrowania ExpressRoute: IPsec przez ExpressRoute dla wirtualnej sieci WAN platformy Azure'
description: W tym samouczku dowiesz się, jak za pomocą usługi Azure Virtual WAN utworzyć połączenie sieci VPN typu lokacja-lokacja za pośrednictwem prywatnej komunikacji równorzędnej ExpressRoute.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: fe8cf0da6cb6542646f3107980b49fb6fef9cb45
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91317637"
---
# <a name="expressroute-encryption-ipsec-over-expressroute-for-virtual-wan"></a>Szyfrowanie ExpressRoute: IPsec przez ExpressRoute dla wirtualnej sieci WAN

W tym artykule opisano sposób korzystania z usługi Azure Virtual Network do nawiązywania połączenia sieci VPN IPsec/IKE z sieci lokalnej na platformę Azure za pośrednictwem prywatnej komunikacji równorzędnej obwodu usługi Azure ExpressRoute. Ta technika może zapewnić zaszyfrowane tranzyt między sieciami lokalnymi i sieciami wirtualnymi platformy Azure za pośrednictwem usługi ExpressRoute, bez przechodzenia przez publiczny Internet ani publicznych adresów IP.

## <a name="topology-and-routing"></a>Topologia i Routing

Na poniższym diagramie przedstawiono przykład łączności sieci VPN za pośrednictwem prywatnej komunikacji równorzędnej ExpressRoute:

![Sieć VPN przez ExpressRoute](./media/vpn-over-expressroute/vwan-vpn-over-er.png)

Diagram przedstawia sieć w sieci lokalnej połączonej z bramą sieci VPN usługi Azure Hub przez prywatną komunikację równorzędną ExpressRoute. Przedsiębiorstwo łączności jest proste:

1. Ustanów łączność ExpressRoute z obwodem usługi ExpressRoute i prywatną komunikację równorzędną.
2. Ustanów połączenie sieci VPN zgodnie z opisem w tym artykule.

Ważnym aspektem tej konfiguracji jest Routing między sieciami lokalnymi i platformą Azure przez ścieżki ExpressRoute i sieci VPN.

### <a name="traffic-from-on-premises-networks-to-azure"></a>Ruch z sieci lokalnych do platformy Azure

W przypadku ruchu z sieci lokalnych na platformę Azure, prefiksy platformy Azure (w tym koncentrator wirtualny i wszystkie sieci wirtualne szprych podłączonych do centrum) są anonsowane za pośrednictwem protokołu BGP Private peer równorzędnego ExpressRoute oraz protokołu BGP dla sieci VPN. Powoduje to dwie trasy sieciowe (ścieżki) do platformy Azure z sieci lokalnych:

- Jeden za pośrednictwem ścieżki chronionej przez protokół IPsec
- Bezpośrednio za pośrednictwem ExpressRoute *bez* ochrony IPsec 

Aby zastosować szyfrowanie do komunikacji, należy się upewnić, że w przypadku sieci połączonej z siecią VPN na diagramie są preferowane trasy platformy Azure za pośrednictwem lokalnej bramy sieci VPN za pośrednictwem bezpośredniej ścieżki ExpressRoute.

### <a name="traffic-from-azure-to-on-premises-networks"></a>Ruch z platformy Azure do sieci lokalnych

To samo wymaganie dotyczy ruchu z platformy Azure do sieci lokalnych. Aby upewnić się, że ścieżka IPsec jest preferowana w ścieżce Direct ExpressRoute (bez protokołu IPsec), dostępne są dwie opcje:

- Anonsowanie bardziej szczegółowych prefiksów w sesji protokołu BGP sieci VPN dla sieci połączonej z siecią VPN. Można anonsować większy zakres obejmujący sieć podłączoną przez sieć VPN za pośrednictwem prywatnej komunikacji równorzędnej ExpressRoute, a następnie bardziej konkretne zakresy w sesji protokołu BGP sieci VPN. Na przykład Anonsuj 10.0.0.0/16 w ExpressRoute i 10.0.1.0/24 za pośrednictwem sieci VPN.

- Anonsowanie rozłączonych prefiksów dla sieci VPN i ExpressRoute. Jeśli zakresy sieci połączone z siecią VPN są rozłączone z innymi połączonymi sieciami ExpressRoute, można anonsować prefiksy odpowiednio w sesjach protokołu BGP sieci VPN i ExpressRoute. Na przykład Anonsuj 10.0.0.0/24 przez ExpressRoute i 10.0.1.0/24 za pośrednictwem sieci VPN.

W obu tych przykładach usługa Azure wyśle ruch do 10.0.1.0/24 przez połączenie sieci VPN, a nie bezpośrednio za pośrednictwem ExpressRoute bez ochrony sieci VPN.

> [!WARNING]
> Jeśli anonsuje te *same* prefiksy dla połączeń EXPRESSROUTE i VPN, platforma Azure będzie używać ścieżki ExpressRoute bezpośrednio bez ochrony sieci VPN.
>

## <a name="before-you-begin"></a>Przed rozpoczęciem

[!INCLUDE [Before you begin](../../includes/virtual-wan-tutorial-vwan-before-include.md)]

## <a name="1-create-a-virtual-wan-and-hub-with-gateways"></a><a name="openvwan"></a>1. Utwórz wirtualną sieć WAN i koncentrator przy użyciu bram

Przed kontynuowaniem należy wykonać następujące zasoby platformy Azure i odpowiednie konfiguracje lokalne:

- Wirtualna sieć WAN platformy Azure
- Wirtualne Centrum sieci WAN z [bramą ExpressRoute](virtual-wan-expressroute-portal.md) i [bramą sieci VPN](virtual-wan-site-to-site-portal.md)

Aby uzyskać instrukcje dotyczące tworzenia wirtualnej sieci WAN platformy Azure i centrum za pomocą skojarzenia ExpressRoute, zobacz [Tworzenie skojarzenia ExpressRoute przy użyciu wirtualnej sieci WAN platformy Azure](virtual-wan-expressroute-portal.md). Aby uzyskać instrukcje dotyczące tworzenia bramy sieci VPN w wirtualnej sieci WAN, zobacz [Tworzenie połączenia typu lokacja-lokacja przy użyciu wirtualnej sieci WAN platformy Azure](virtual-wan-site-to-site-portal.md).

## <a name="2-create-a-site-for-the-on-premises-network"></a><a name="site"></a>2. Utwórz witrynę dla sieci lokalnej

Zasób lokacji jest taki sam jak Lokacje sieci VPN inne niż ExpressRoute dla wirtualnej sieci WAN. Adres IP lokalnego urządzenia sieci VPN może teraz być prywatnym adresem IP lub publicznym adresem IP w sieci lokalnej dostępnej za pośrednictwem prywatnej komunikacji równorzędnej ExpressRoute utworzonej w kroku 1.

> [!NOTE]
> Adres IP lokalnego urządzenia sieci VPN *musi* być częścią prefiksów adresów anonsowanego wirtualnemu KONCENTRATOROWI sieci WAN za pośrednictwem prywatnej komunikacji równorzędnej Azure ExpressRoute.
>

1. Przejdź do Azure Portal w przeglądarce. 
1. Wybierz utworzoną sieć WAN. Na stronie sieć WAN w obszarze **łączność**wybierz pozycję **Lokacje sieci VPN**.
1. Na stronie **witryny sieci VPN** wybierz pozycję **+ Utwórz lokację**.
1. Na stronie **Tworzenie lokacji** wypełnij następujące pola:
   * **Subskrypcja**: Sprawdź subskrypcję.
   * **Grupa zasobów**: wybierz lub Utwórz grupę zasobów, której chcesz użyć.
   * **Region**: wprowadź region platformy Azure dla zasobu witryny sieci VPN.
   * **Nazwa**: Wprowadź nazwę, w której chcesz odwołać się do lokacji lokalnej.
   * **Dostawca urządzenia**: Wprowadź dostawcę lokalnego urządzenia sieci VPN.
   * **Border Gateway Protocol**: wybierz opcję "Włącz", jeśli sieć lokalna używa protokołu BGP.
   * **Prywatna przestrzeń adresowa**: wprowadź przestrzeń adresową IP, która znajduje się w lokacji lokalnej. Ruch przeznaczony dla tej przestrzeni adresowej jest kierowany do sieci lokalnej za pośrednictwem bramy sieci VPN.
   * **Centra**: Wybierz co najmniej jeden koncentrator, aby połączyć tę LOKACJĘ sieci VPN. Wybrane centra muszą mieć już utworzone bramy sieci VPN.
1. Wybierz pozycję **Dalej: linki >** ustawień łącza sieci VPN:
   * **Nazwa łącza**: nazwa, za pomocą której chcesz odwołać się do tego połączenia.
   * **Nazwa dostawcy**: Nazwa usługodawcy internetowego dla tej lokacji. W przypadku sieci lokalnej ExpressRoute jest to nazwa dostawcy usług ExpressRoute.
   * **Szybkość**: szybkość łącza usługi internetowej lub obwodu ExpressRoute.
   * **Adres IP**: publiczny adres IP urządzenia sieci VPN znajdującego się w lokacji lokalnej. Lub, w przypadku lokalnego ExpressRoute, jest to prywatny adres IP urządzenia sieci VPN za pośrednictwem usługi ExpressRoute.

   Jeśli protokół BGP jest włączony, będzie miał zastosowanie do wszystkich połączeń utworzonych dla tej witryny na platformie Azure. Konfigurowanie protokołu BGP w wirtualnej sieci WAN jest równoznaczne z skonfigurowaniem protokołu BGP na bramie sieci VPN platformy Azure. 
   
   Adres lokalnego elementu równorzędnego protokołu BGP *nie może* być taki sam jak adres IP sieci VPN do urządzenia lub przestrzeń adresów sieci wirtualnej witryny sieci VPN. Użyj innego adresu IP na urządzeniu sieci VPN dla adresu IP elementu równorzędnego protokołu BGP. Może to być adres przypisany do interfejsu sprzężenia zwrotnego na urządzeniu. Jednak *nie może* to być adresowanie APIPA (169,254.* x*. *x*). Określ ten adres w odpowiedniej bramie sieci lokalnej reprezentującej lokalizację. Wymagania wstępne dotyczące protokołu BGP zawiera temat [Informacje o protokole BGP z platformą Azure VPN Gateway](../vpn-gateway/vpn-gateway-bgp-overview.md).

1. Wybierz kolejno pozycje **Dalej: przegląd + utwórz >** , aby sprawdzić wartości ustawień i utworzyć witrynę sieci VPN. W przypadku wybrania **centrów** do połączenia zostanie nawiązane połączenie między siecią lokalną a centralną bramą sieci VPN.

## <a name="3-update-the-vpn-connection-setting-to-use-expressroute"></a><a name="hub"></a>3. Zaktualizuj ustawienie połączenia sieci VPN, aby używać ExpressRoute

Po utworzeniu witryny sieci VPN i nawiązaniu połączenia z centrum wykonaj następujące kroki, aby skonfigurować połączenie do korzystania z prywatnej komunikacji równorzędnej ExpressRoute:

1. Wróć do strony zasobów wirtualnej sieci WAN, a następnie wybierz zasób centrum. Lub przejdź między lokacją sieci VPN a podłączonym koncentratorem.
1. W obszarze **łączność**wybierz pozycję **Sieć VPN (lokacja-lokacja)**.
1. Wybierz wielokropek (**...**) w witrynie sieci VPN za pośrednictwem ExpressRoute, a następnie wybierz pozycję **Edytuj połączenie sieci VPN z tym centrum**.
1. W obszarze **Użyj prywatnego adresu IP platformy Azure**wybierz pozycję **tak**. Ustawienie konfiguruje bramę sieci VPN Hub do używania prywatnych adresów IP w ramach zakresu adresów koncentratora na bramie dla tego połączenia zamiast publicznych adresów IP. Zapewni to, że ruch z sieci lokalnej przechodzi przez prywatne ścieżki komunikacji równorzędnej ExpressRoute zamiast używać publicznego Internetu dla tego połączenia sieci VPN. Poniższy zrzut ekranu przedstawia ustawienie.

   ![Ustawienie dla korzystania z prywatnego adresu IP dla połączenia sieci VPN](./media/vpn-over-expressroute/vpn-link-configuration.png)
   
1. Wybierz pozycję **Zapisz**.

Po zapisaniu zmian Brama sieci VPN centrum będzie używać prywatnych adresów IP w bramie sieci VPN do nawiązywania połączeń protokołu IPsec/IKE z lokalnym urządzeniem sieci VPN za pośrednictwem usługi ExpressRoute.

## <a name="4-get-the-private-ip-addresses-for-the-hub-vpn-gateway"></a><a name="associate"></a>4. Uzyskaj prywatne adresy IP dla bramy sieci VPN Hub

Pobierz konfigurację urządzeń sieci VPN, aby uzyskać prywatne adresy IP bramy sieci VPN centrum. Te adresy są potrzebne do skonfigurowania lokalnego urządzenia sieci VPN.

1. Na stronie centrum wybierz pozycję **Sieć VPN (lokacja-lokacja)** w obszarze **łączność**.
1. W górnej części strony **Przegląd** wybierz pozycję **Pobierz konfigurację sieci VPN**. 

   Platforma Azure tworzy konto magazynu w grupie zasobów "Microsoft-Network-[Location]", gdzie *Lokalizacja* jest lokalizacją sieci WAN. Po zastosowaniu konfiguracji do urządzeń sieci VPN można usunąć to konto magazynu.
1. Po utworzeniu pliku wybierz łącze, aby je pobrać.
1. Zastosuj konfigurację na urządzeniu sieci VPN.

### <a name="vpn-device-configuration-file"></a>Plik konfiguracji urządzenia sieci VPN

Plik konfiguracji urządzenia zawiera ustawienia, które są używane podczas konfigurowania lokalnego urządzenia sieci VPN. Podczas przeglądania tego pliku należy zwrócić uwagę na następujące informacje:

* **vpnSiteConfiguration**: w tej sekcji znajdują się informacje o urządzeniu skonfigurowanym jako lokacja, która nawiązuje połączenie z wirtualną siecią WAN. Zawiera nazwę i publiczny adres IP urządzenia rozgałęzienia.
* **vpnSiteConnections**: Ta sekcja zawiera informacje o następujących ustawieniach:

    * Przestrzeń adresowa sieci wirtualnej koncentratora wirtualnego.<br/>Przykład:
           ```
           "AddressSpace":"10.51.230.0/24"
           ```
    * Przestrzeń adresowa sieci wirtualnych podłączonych do centrum.<br>Przykład:
           ```
           "ConnectedSubnets":["10.51.231.0/24"]
            ```
    * Adresy IP bramy sieci VPN koncentratora wirtualnego. Ponieważ każde połączenie bramy sieci VPN składa się z dwóch tuneli w konfiguracji Active-Active, zobaczysz oba adresy IP wymienione w tym pliku. W tym przykładzie zobaczysz `Instance0` i `Instance1` dla każdej witryny i są one prywatnymi adresami IP, a nie publicznymi adresami IP.<br>Przykład:
           ``` 
           "Instance0":"10.51.230.4"
           "Instance1":"10.51.230.5"
           ```
    * Szczegóły konfiguracji połączenia z bramą sieci VPN, takie jak protokół BGP i klucz wstępny. Klucz wstępny jest generowany automatycznie. Możesz zawsze edytować połączenie na stronie **Przegląd** dla niestandardowego klucza wstępnego.
  
### <a name="example-device-configuration-file"></a>Przykładowy plik konfiguracji urządzenia

```
[{
      "configurationVersion":{
        "LastUpdatedTime":"2019-10-11T05:57:35.1803187Z",
        "Version":"5b096293-edc3-42f1-8f73-68c14a7c4db3"
      },
      "vpnSiteConfiguration":{
        "Name":"VPN-over-ER-site",
        "IPAddress":"172.24.127.211",
        "LinkName":"VPN-over-ER"
      },
      "vpnSiteConnections":[{
        "hubConfiguration":{
          "AddressSpace":"10.51.230.0/24",
          "Region":"West US 2",
          "ConnectedSubnets":["10.51.231.0/24"]
        },
        "gatewayConfiguration":{
          "IpAddresses":{
            "Instance0":"10.51.230.4",
            "Instance1":"10.51.230.5"
          }
        },
        "connectionConfiguration":{
          "IsBgpEnabled":false,
          "PSK":"abc123",
          "IPsecParameters":{"SADataSizeInKilobytes":102400000,"SALifeTimeInSeconds":3600}
        }
      }]
    },
    {
      "configurationVersion":{
        "LastUpdatedTime":"2019-10-11T05:57:35.1803187Z",
        "Version":"fbdb34ea-45f8-425b-9bc2-4751c2c4fee0"
      },
      "vpnSiteConfiguration":{
        "Name":"VPN-over-INet-site",
        "IPAddress":"13.75.195.234",
        "LinkName":"VPN-over-INet"
      },
      "vpnSiteConnections":[{
        "hubConfiguration":{
          "AddressSpace":"10.51.230.0/24",
          "Region":"West US 2",
          "ConnectedSubnets":["10.51.231.0/24"]
        },
        "gatewayConfiguration":{
          "IpAddresses":{
            "Instance0":"51.143.63.104",
            "Instance1":"52.137.90.89"
          }
        },
        "connectionConfiguration":{
          "IsBgpEnabled":false,
          "PSK":"abc123",
          "IPsecParameters":{"SADataSizeInKilobytes":102400000,"SALifeTimeInSeconds":3600}
        }
      }]
}]
```

### <a name="configuring-your-vpn-device"></a>Konfigurowanie urządzenia sieci VPN

Jeśli chcesz wiedzieć, jak skonfigurować urządzenie, możesz skorzystać z instrukcji na [stronie ze skryptami konfigurowania urządzenia sieci VPN](~/articles/vpn-gateway/vpn-gateway-about-vpn-devices.md#configscripts), z następującymi zastrzeżeniami:

* Instrukcje na stronie urządzenia sieci VPN nie są napisywane dla wirtualnej sieci WAN. Można jednak użyć wirtualnych wartości sieci WAN z pliku konfiguracji, aby ręcznie skonfigurować urządzenie sieci VPN. 
* Skrypty konfiguracji urządzenia do pobrania przeznaczone dla bramy sieci VPN nie działają dla wirtualnej sieci WAN, ponieważ konfiguracja różni się.
* Nowa wirtualna sieć WAN może obsługiwać zarówno protokół IKEv1, jak i IKEv2.
* Wirtualna sieć WAN może używać tylko urządzeń sieci VPN opartych na trasach i instrukcji dotyczących urządzeń.

## <a name="5-view-your-virtual-wan"></a><a name="viewwan"></a>5. Wyświetl wirtualną sieć WAN

1. Przejdź do wirtualnej sieci WAN.
1. Na stronie **Przegląd** każdy punkt na mapie reprezentuje centrum.
1. W sekcji **centra i połączenia** można wyświetlić stan połączenia centrum, witryny, regionu i sieci VPN. Możesz również wyświetlić bajty i wychodzące.

## <a name="7-monitor-a-connection"></a><a name="connectmon"></a>7. monitorowanie połączenia

Utwórz połączenie, aby monitorować komunikację między maszyną wirtualną platformy Azure a lokacją zdalną. Aby uzyskać informacje dotyczące konfigurowania monitora połączeń, zobacz [Monitorowanie komunikacji sieciowej](~/articles/network-watcher/connection-monitor.md). Pole Źródło jest adresem IP maszyny wirtualnej na platformie Azure, a docelowy adres IP to adres IP lokacji.

## <a name="8-clean-up-resources"></a><a name="cleanup"></a>8. Wyczyść zasoby

Jeśli te zasoby nie są już potrzebne, możesz usunąć grupę zasobów i wszystkie znajdujące się w niej zasoby, używając polecenie [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) . Uruchom następujące polecenie programu PowerShell i Zastąp ciąg `myResourceGroup` nazwą grupy zasobów:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Następne kroki

Ten artykuł pomaga utworzyć połączenie sieci VPN przez prywatną komunikację równorzędną ExpressRoute przy użyciu wirtualnej sieci WAN. Aby dowiedzieć się więcej o wirtualnej sieci WAN i powiązanych funkcjach, zobacz [Omówienie wirtualnej sieci WAN](virtual-wan-about.md).
