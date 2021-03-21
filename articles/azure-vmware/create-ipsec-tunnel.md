---
title: Tworzenie tunelu IPSec w rozwiązaniu VMware platformy Azure
description: Dowiedz się, jak utworzyć wirtualny koncentrator sieci WAN, aby nawiązać tunel IPSec z rozwiązaniami VMware platformy Azure.
ms.topic: how-to
ms.date: 10/02/2020
ms.openlocfilehash: 21df674862b65ef6573a8a3fcfd7538b1053f04e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103491854"
---
# <a name="create-an-ipsec-tunnel-into-azure-vmware-solution"></a>Tworzenie tunelu IPSec w rozwiązaniu VMware platformy Azure

W tym artykule opisano kroki umożliwiające nawiązanie przerwania tunelowania między lokacjami (IPsec IKEv1 i IKEv2) w Microsoft Azure wirtualnym koncentratorze sieci WAN. Utworzymy Wirtualne Centrum sieci WAN platformy Azure i bramę sieci VPN z dołączonym publicznym adresem IP. Następnie utworzymy bramę usługi Azure ExpressRoute i nawiążesz punkt końcowy rozwiązania VMware platformy Azure. Zawarto także szczegółowe informacje dotyczące włączania konfiguracji lokalnej sieci VPN opartej na zasadach. 

## <a name="topology"></a>Topologia

![Diagram przedstawiający architekturę tunelowania między lokacjami sieci VPN.](media/create-ipsec-tunnel/vpn-s2s-tunnel-architecture.png)

Usługa Azure Virtual Hub zawiera rozwiązanie Azure VMware ExpressRoute Gateway i bramę sieci VPN typu lokacja-lokacja. Nawiązuje połączenie z lokalnym urządzeniem sieci VPN z punktem końcowym rozwiązania VMware platformy Azure.

## <a name="before-you-begin"></a>Zanim rozpoczniesz

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
   | **Typ** | Wybierz opcję **Standardowy**, co pozwoli na więcej niż tylko ruch bramy sieci VPN.  |

   :::image type="content" source="media/create-ipsec-tunnel/create-wan.png" alt-text="Zrzut ekranu przedstawiający stronę tworzenie sieci WAN w Azure Portal.":::

3. W Azure Portal wybierz wirtualną sieć WAN utworzoną w poprzednim kroku, wybierz pozycję **Utwórz koncentrator wirtualny**, wprowadź wymagane pola, a następnie wybierz pozycję **Dalej: lokacja**. 

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

2. W oknie **Omówienie** centrum wirtualnego wybierz pozycję **łączność**  >  **VPN (lokacja-lokacja)**, a następnie wybierz pozycję **Utwórz nową witrynę sieci VPN**.

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

1. W Azure Portal przejdź do witryny wirtualnego centrum sieci WAN. W obszarze **łączność** wybierz pozycję **VPN (lokacja-lokacja)**.

2. Wybierz nazwę witryny sieci VPN, wielokropek (...) po prawej stronie, a następnie **Edytuj połączenie sieci VPN z tym centrum**.
 
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

1. Wybierz nazwę witryny sieci VPN, a następnie wybierz pozycję **Połącz witryny sieci VPN**. 
1. W polu **klucz wstępny** wprowadź klucz wcześniej zdefiniowany dla lokalnego punktu końcowego. 

   >[!TIP]
   >Jeśli nie masz wcześniej zdefiniowanego klucza, możesz pozostawić to pole puste. Klucz jest generowany automatycznie. 
 
   >[!IMPORTANT]
   >Po wdrożeniu zapory w centrum i następnym przeskoku do połączeń za pomocą tego tunelu należy włączyć opcję **Propagacja trasy domyślnej** .

1. Wybierz pozycję **Połącz**. Ekran stanu połączenia pokazuje stan tworzenia tunelu.

2. Przejdź do omówienia wirtualnej sieci WAN i Otwórz stronę witryny sieci VPN, aby pobrać plik konfiguracji sieci VPN dla lokalnego punktu końcowego.  

3. Należy zastosować poprawki do usługi Azure VMware ExpressRoute w wirtualnym koncentratorze sieci WAN. Ten krok wymaga wcześniejszego utworzenia chmury prywatnej.

   [!INCLUDE [request-authorization-key](includes/request-authorization-key.md)]

4. Połącz rozwiązanie VMware platformy Azure i bramę sieci VPN ze sobą w wirtualnym koncentratorze sieci WAN. 
   1. W Azure Portal Otwórz utworzoną wcześniej wirtualną sieć WAN. 
   1. Wybierz utworzony wirtualny koncentrator sieci WAN, a następnie wybierz pozycję **ExpressRoute** w lewym okienku. 
   1. Wybierz pozycję **+ Zrealizuj klucz autoryzacji**.

      :::image type="content" source="media/create-ipsec-tunnel/redeem-authorization-key.png" alt-text="Zrzut ekranu strony ExpressRoute dla chmury prywatnej z wybranym kluczem autoryzacji Zrealizuj.":::

   1. Wklej klucz autoryzacji do pola klucz autoryzacji.
   1. W polu Identyfikator **URI obwodu równorzędnego** należy wkleić wartość ExpressRoute. 
   1. Wybierz pozycję **automatycznie skojarz ten obwód ExpressRoute z centrum.** 
   1. Wybierz pozycję **Dodaj** , aby nawiązać połączenie. 

5. Przetestuj połączenie, [tworząc segment NSX-T](./tutorial-nsx-t-network-segment.md) i udostępniając maszynę wirtualną w sieci. Wyślij polecenie ping do punktów końcowych rozwiązań lokalnych i programu VMware platformy Azure.
