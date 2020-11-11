---
title: Tworzenie tunelu IPSec w rozwiązaniu VMware platformy Azure
description: Dowiedz się, jak utworzyć wirtualny koncentrator sieci WAN, aby nawiązać tunel IPSec z rozwiązaniami VMware platformy Azure.
ms.topic: how-to
ms.date: 10/02/2020
ms.openlocfilehash: 9f869f04bf165f4791f13c626b63257ea98a7ca9
ms.sourcegitcommit: 4bee52a3601b226cfc4e6eac71c1cb3b4b0eafe2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/11/2020
ms.locfileid: "94506451"
---
# <a name="create-an-ipsec-tunnel-into-azure-vmware-solution"></a>Tworzenie tunelu IPSec w rozwiązaniu VMware platformy Azure

W tym artykule opisano kroki umożliwiające nawiązanie przerwania tunelowania między lokacjami (IPsec IKEv1 i IKEv2) w Microsoft Azure wirtualnym koncentratorze sieci WAN. Utworzymy Wirtualne Centrum sieci WAN platformy Azure i bramę sieci VPN z dołączonym publicznym adresem IP. Następnie utworzymy bramę usługi Azure ExpressRoute i nawiążesz punkt końcowy rozwiązania VMware platformy Azure. Zawarto także szczegółowe informacje dotyczące włączania konfiguracji lokalnej sieci VPN opartej na zasadach. 

## <a name="topology"></a>Topologia

![Diagram przedstawiający architekturę tunelowania między lokacjami sieci VPN.](media/create-ipsec-tunnel/vpn-s2s-tunnel-architecture.png)

Usługa Azure Virtual Hub zawiera rozwiązanie Azure VMware ExpressRoute Gateway i bramę sieci VPN typu lokacja-lokacja. Nawiązuje połączenie z lokalnym urządzeniem sieci VPN z punktem końcowym rozwiązania VMware platformy Azure.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Aby utworzyć tunel VPN typu lokacja-lokacja, należy utworzyć publiczny adres IP, który zakończy działanie na lokalnym urządzeniu sieci VPN.

## <a name="create-a-virtual-wan-hub"></a>Tworzenie wirtualnego centrum sieci WAN

1. W Azure Portal Wyszukaj **wirtualne sieci WAN**. Wybierz pozycję **+Dodaj**. Zostanie otwarta strona tworzenie sieci WAN.  

2. Wprowadź wymagane pola na stronie **Tworzenie sieci WAN** , a następnie wybierz pozycję **Przegląd + Utwórz**.
   
   | Pole | Wartość |
   | --- | --- |
   | **Subskrypcja** | Wartość jest wstępnie wypełniona dla subskrypcji należącej do grupy zasobów. |
   | **Grupa zasobów** | Wirtualna sieć WAN jest zasobem globalnym i nie jest ograniczona do określonego regionu.  |
   | **Lokalizacja grupy zasobów** | Aby utworzyć koncentratora wirtualnego sieci WAN, należy ustawić lokalizację dla grupy zasobów.  |
   | **Nazwa** |   |
   | **Typ** | Wybierz opcję **Standardowy** , co pozwoli na więcej niż tylko ruch bramy sieci VPN.  |


    :::image type="content" source="media/create-ipsec-tunnel/create-wan.png" alt-text="Zrzut ekranu przedstawiający stronę tworzenie sieci WAN w Azure Portal.":::

3. W Azure Portal wybierz wirtualną sieć WAN utworzoną w poprzednim kroku, wybierz pozycję **Utwórz koncentrator wirtualny** , wprowadź wymagane pola, a następnie wybierz pozycję **Dalej: lokacja**. 

   | Pole | Wartość |
   | --- | --- |
   | **Region** | Wybieranie regionu jest wymagane z perspektywy zarządzania.  |
   | **Nazwa** |    |
   | **Prywatna przestrzeń adresowa koncentratora** | Wprowadź podsieć przy użyciu `/24` (minimum).  |

    :::image type="content" source="media/create-ipsec-tunnel/create-virtual-hub.png" alt-text="Zrzut ekranu przedstawiający stronę tworzenie wirtualnego centrum.":::

4. Na karcie **lokacja-lokacja** Zdefiniuj bramę lokacja-lokacja, ustawiając zagregowaną przepływność z listy rozwijanej **jednostki skalowania bramy** . 

   >[!TIP]
   >Jedna jednostka skalowania = 500 MB/s. Jednostki skalowania są w parach dla nadmiarowości, z których każda obsługuje 500 MB/s.
  
5. Na karcie **ExpressRoute** Utwórz bramę ExpressRoute. 

   >[!TIP]
   >Wartość jednostki skalowania to 2 GB/s. 

    Utworzenie poszczególnych centrów zajmie około 30 minut. 

## <a name="create-a-vpn-site"></a>Tworzenie witryny sieci VPN 

1. W obszarze **ostatnie zasoby** w Azure Portal wybierz wirtualną sieć WAN utworzoną w poprzedniej sekcji.

2. W oknie **Omówienie** centrum wirtualnego wybierz pozycję **łączność**  >  **VPN (lokacja-lokacja)** , a następnie wybierz pozycję **Utwórz nową witrynę sieci VPN**.


    :::image type="content" source="media/create-ipsec-tunnel/create-vpn-site-basics.png" alt-text="Zrzut ekranu przedstawiający stronę omówienia koncentratora wirtualnego z siecią VPN (lokacja-lokacja) i wybraną nową lokację sieci VPN.":::  
 
3. Na karcie **podstawowe** wprowadź wymagane pola, a następnie wybierz pozycję **Dalej: linki**. 

   | Pole | Wartość |
   | --- | --- |
   | **Region** | Ten sam region, który został określony w poprzedniej sekcji.  |
   | **Nazwa** |  |
   | **Dostawca urządzenia** |  |
   | **Protokół Border Gateway Protocol** | Ustaw opcję na **Włącz** , aby zapewnić, że zarówno rozwiązanie VMware platformy Azure, jak i serwery lokalne anonsują trasy w tunelu. Jeśli ta wartość jest wyłączona, podsieci, które muszą być anonsowane, muszą być przechowywane ręcznie. Jeśli zostaną pominięte podsieci, HCX nie będzie mógł utworzyć siatki usługi. Aby uzyskać więcej informacji, zobacz  [Informacje o protokole BGP z platformą Azure VPN Gateway](../vpn-gateway/vpn-gateway-bgp-overview.md). |
   | **Prywatna przestrzeń adresowa**  | Wprowadź lokalny blok CIDR.  Jest on używany do kierowania całego ruchu związanego z lokalnymi przez tunel.  Blok CIDR jest wymagany tylko wtedy, gdy nie zostanie włączony protokół BGP. |
   | **Połącz z** |   |

4. Na karcie **łącza** Wypełnij pola wymagane i wybierz pozycję **Przegląd + Utwórz**. Określanie nazw łączy i dostawców pozwala odróżnić dowolną liczbę bram, które mogą zostać utworzone w ramach centrum. Protokół BGP i numer systemu autonomicznego muszą być unikatowe w organizacji.
 
## <a name="optional-defining-a-vpn-site-for-policy-based-vpn-site-to-site-tunnels"></a>Obowiązkowe Definiowanie witryny sieci VPN dla tuneli między lokacjami sieci VPN opartych na zasadach

Ta sekcja dotyczy tylko sieci VPN opartych na zasadach. Konfiguracje sieci VPN oparte na zasadach (lub statycznej, opartej na trasach) są oparte na możliwościach lokalnych urządzeń sieci VPN w większości przypadków. Wymagają one określenia lokalnych i sieci rozwiązań VMware platformy Azure. W przypadku rozwiązania VMware platformy Azure z wirtualnym Centrum sieci WAN platformy Azure nie można wybrać *żadnej* sieci. Zamiast tego należy określić wszystkie odpowiednie lokalne i wirtualne rozwiązanie VMware platformy Azure. Te zakresy centrów służą do określania domeny szyfrowania podstawowego tunelu sieci VPN w lokalnym punkcie końcowym. Po stronie rozwiązania VMware dla platformy Azure wymagane jest włączenie wskaźnika selektora ruchu opartego na zasadach. 

1. W Azure Portal przejdź do witryny wirtualnego centrum sieci WAN; w obszarze **łączność** wybierz pozycję **VPN (lokacja-lokacja)**.

2. Wybierz nazwę witryny sieci VPN, a następnie wielokropek (...) po prawej stronie; następnie wybierz pozycję **Edytuj połączenie sieci VPN z tym centrum**.
 
    :::image type="content" source="media/create-ipsec-tunnel/edit-vpn-section-to-this-hub.png" alt-text="Zrzut ekranu strony na platformie Azure dla witryny wirtualnego centrum sieci WAN z zaznaczoną wielokropkiem, aby uzyskać dostęp do edytowania połączenia sieci VPN z tym centrum." lightbox="media/create-ipsec-tunnel/edit-vpn-section-to-this-hub.png":::

3. Edytuj połączenie między lokacją sieci VPN a centrum, a następnie wybierz pozycję **Zapisz**.
   - Zabezpieczenia protokołu internetowego (IPSec), wybierz opcję **niestandardowy**.
   - Użyj selektora ruchu opartego na zasadach, wybierz pozycję **Włącz**
   - Określ szczegóły dla **usługi IKE phase 1** i **IKE Phase 2 (IPSec)**. 
 
    :::image type="content" source="media/create-ipsec-tunnel/edit-vpn-connection.png" alt-text="Zrzut ekranu przedstawiający stronę Edytowanie połączenia sieci VPN."::: 
 
    Selektory ruchu lub podsieci, które są częścią domeny szyfrowania opartej na zasadach, powinny mieć następujące wartości:
    
    - Koncentrator wirtualnych sieci WAN/24
    - Chmura prywatna rozwiązania Azure VMware/22
    - Połączona sieć wirtualna platformy Azure (jeśli istnieje)

## <a name="connect-your-vpn-site-to-the-hub"></a>Łączenie witryny sieci VPN z centrum

1. Zaznacz pole wyboru obok nazwy witryny sieci VPN (zobacz poprzedni zrzut ekranu **witryny sieci VPN do lokacji** ), a następnie wybierz pozycję **Połącz witryny sieci VPN**. W polu **klucz wstępny** wprowadź klucz wcześniej zdefiniowany dla lokalnego punktu końcowego. Jeśli nie masz wcześniej zdefiniowanego klucza, możesz pozostawić to pole puste, a klucz zostanie wygenerowany automatycznie. 
 
    Po wdrożeniu zapory w centrum i następnym przeskoku do połączeń za pomocą tego tunelu należy włączyć opcję **Propagacja trasy domyślnej** .

    Wybierz pozycję **Połącz**. Na ekranie stan połączenia zostanie wyświetlony stan tworzenia tunelu.

2. Przejdź do omówienia wirtualnej sieci WAN. Otwórz stronę witryny sieci VPN i Pobierz plik konfiguracji sieci VPN, aby zastosować go do lokalnego punktu końcowego.  

3. Teraz będziemy poprawiać rozwiązanie Azure VMware ExpressRoute w wirtualnym koncentratorze sieci WAN. (Ten krok wymaga, aby najpierw utworzyć chmurę prywatną).

    Przejdź do sekcji **łączność** w chmurze prywatnej rozwiązania Azure VMware. Na karcie **ExpressRoute** wybierz pozycję **+ Zażądaj klucza autoryzacji**. Nadaj jej nazwę i wybierz pozycję **Utwórz**. (Utworzenie klucza może potrwać około 30 sekund). Skopiuj identyfikator ExpressRoute i klucz autoryzacji. 

    :::image type="content" source="media/create-ipsec-tunnel/express-route-connectivity.png" alt-text="Zrzut ekranu strony łączność z chmurą prywatną z żądaniem wybranego klucza autoryzacji na karcie ExpressRoute.":::

    > [!NOTE]
    > Klucz autoryzacji będzie znikał po pewnym czasie, więc skopiuj go tak szybko, jak zostanie wyświetlony.

4. Następnie połączymy rozwiązanie VMware platformy Azure i bramę sieci VPN ze sobą w wirtualnym koncentratorze sieci WAN. W Azure Portal Otwórz utworzoną wcześniej wirtualną sieć WAN. Wybierz utworzony wirtualny koncentrator sieci WAN, a następnie wybierz pozycję **ExpressRoute** w lewym okienku. Wybierz pozycję **+ Zrealizuj klucz autoryzacji**.

    :::image type="content" source="media/create-ipsec-tunnel/redeem-authorization-key.png" alt-text="Zrzut ekranu strony ExpressRoute dla chmury prywatnej z wybranym kluczem autoryzacji Zrealizuj.":::

    Wklej klucz autoryzacji do pola klucz autoryzacji i identyfikator ExpressRoute w polu Identyfikator **URI obwodu równorzędnego** . Upewnij się, że wybrano opcję **automatycznie skojarz ten obwód ExpressRoute z centrum.** Wybierz pozycję **Dodaj** , aby nawiązać połączenie. 

5. Aby przetestować połączenie, [Utwórz segment NSX-T](./tutorial-nsx-t-network-segment.md) i Udostępnij maszynę wirtualną w sieci. Przetestuj przez pingowanie zarówno punktów końcowych rozwiązania lokalnego, jak i platformy Azure VMware.
