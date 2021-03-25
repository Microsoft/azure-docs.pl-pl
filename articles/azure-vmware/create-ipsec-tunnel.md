---
title: Tworzenie tunelu IPSec w rozwiązaniu VMware platformy Azure
description: Dowiedz się, jak ustanowić tunel VPN typu lokacja-lokacja (IPsec IKEv1 i IKEv2) do rozwiązań VMware platformy Azure.
ms.topic: how-to
ms.date: 03/23/2021
ms.openlocfilehash: 280ffdd3fec77208d5b49c8e624b7b22bca1daaf
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2021
ms.locfileid: "105027045"
---
# <a name="create-an-ipsec-tunnel-into-azure-vmware-solution"></a>Tworzenie tunelu IPSec w rozwiązaniu VMware platformy Azure

W tym artykule opisano kroki umożliwiające nawiązanie przerwania tunelowania między lokacjami (IPsec IKEv1 i IKEv2) w Microsoft Azure wirtualnym koncentratorze sieci WAN. Centrum zawiera rozwiązanie Azure VMware ExpressRoute Gateway i bramę sieci VPN typu lokacja-lokacja. Nawiązuje połączenie z lokalnym urządzeniem sieci VPN z punktem końcowym rozwiązania VMware platformy Azure.

:::image type="content" source="media/create-ipsec-tunnel/vpn-s2s-tunnel-architecture.png" alt-text="Diagram przedstawiający architekturę tunelowania między lokacjami sieci VPN." border="false":::

W tym celu:
- Utwórz Centrum sieci wirtualnej platformy Azure i bramę sieci VPN z dołączonym publicznym adresem IP. 
- Utwórz bramę usługi Azure ExpressRoute i Ustanów punkt końcowy rozwiązania VMware platformy Azure. 
- Włącz lokalną konfigurację sieci VPN opartej na zasadach. 

## <a name="prerequisites"></a>Wymagania wstępne
Na lokalnym urządzeniu sieci VPN musi być zakończony publiczny adres IP.

## <a name="step-1-create-an-azure-virtual-wan"></a>Krok 1. Tworzenie wirtualnej sieci WAN platformy Azure

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-create-vwan-include.md)]

## <a name="step-2-create-a-virtual-wan-hub-and-gateway"></a>Krok 2. Tworzenie wirtualnego centrum sieci WAN i bramy

>[!TIP]
>[Bramę można również utworzyć w istniejącym centrum](../virtual-wan/virtual-wan-expressroute-portal.md#existinghub).

1. Wybierz wirtualną sieć WAN utworzoną w poprzednim kroku.

1. Wybierz pozycję **Utwórz koncentrator wirtualny**, wprowadź wymagane pola, a następnie wybierz pozycję **Dalej: lokacja-lokacja**. 

   Wprowadź podsieć przy użyciu `/24` (minimum).

   :::image type="content" source="media/create-ipsec-tunnel/create-virtual-hub.png" alt-text="Zrzut ekranu przedstawiający stronę tworzenie wirtualnego centrum.":::

4. Wybierz kartę **lokacja-lokacja** , zdefiniuj bramę lokacja-lokacja, ustawiając zagregowaną przepływność z listy rozwijanej **jednostki skalowania bramy** . 

   >[!TIP]
   >Jednostki skalowania są w parach dla nadmiarowości, z których każda obsługuje 500 MB/s (jedna jednostka skalowania = 500 MB/s). 
  
   :::image type="content" source="../../includes/media/virtual-wan-tutorial-hub-include/site-to-site.png" alt-text="Zrzut ekranu przedstawiający szczegóły między lokacjami.":::

5. Wybierz kartę **ExpressRoute** , a następnie utwórz bramę ExpressRoute. 

   :::image type="content" source="../../includes/media/virtual-wan-tutorial-er-hub-include/hub2.png" alt-text="Zrzut ekranu przedstawiający ustawienia ExpressRoute.":::

   >[!TIP]
   >Wartość jednostki skalowania to 2 GB/s. 

    Utworzenie poszczególnych centrów zajmie około 30 minut. 

## <a name="step-3-create-a-site-to-site-vpn"></a>Krok 3. Utworzenie międzylokacyjnej sieci VPN

1. W Azure Portal Wybierz utworzoną wcześniej wirtualną sieć WAN.

2. W oknie **Omówienie** koncentratora wirtualnego wybierz pozycję **łączność**  >  **VPN (lokacja-lokacja)**  >  **Utwórz nową witrynę sieci VPN**.

   :::image type="content" source="media/create-ipsec-tunnel/create-vpn-site-basics.png" alt-text="Zrzut ekranu przedstawiający stronę omówienia koncentratora wirtualnego z siecią VPN (lokacja-lokacja) i wybraną nową lokację sieci VPN.":::  
 
3. Na karcie **podstawowe** wprowadź wymagane pola. 

   :::image type="content" source="media/create-ipsec-tunnel/create-vpn-site-basics2.png" alt-text="Zrzut ekranu przedstawiający kartę podstawowe informacje o nowej witrynie sieci VPN.":::  

   1. Ustaw **Border Gateway Protocol** do **włączenia**.  Gdy ta opcja jest włączona, zapewnia, że zarówno rozwiązanie VMware platformy Azure, jak i serwery lokalne anonsują trasy w tunelu. Jeśli ta wartość jest wyłączona, podsieci, które muszą być anonsowane, muszą być przechowywane ręcznie. Jeśli zostaną pominięte podsieci, HCX nie będzie mógł utworzyć siatki usługi. Aby uzyskać więcej informacji, zobacz  [Informacje o protokole BGP z platformą Azure VPN Gateway](../vpn-gateway/vpn-gateway-bgp-overview.md).
   
   1. W polu **prywatna przestrzeń adresowa** wprowadź lokalny blok CIDR. Jest on używany do kierowania całego ruchu związanego z lokalnymi przez tunel. Blok CIDR jest wymagany tylko wtedy, gdy nie zostanie włączony protokół BGP.

1. Wybierz pozycję **Dalej: linki** i Wypełnij wymagane pola. Określanie nazw łączy i dostawców pozwala odróżnić dowolną liczbę bram, które mogą zostać utworzone w ramach centrum. Protokół BGP i numer systemu autonomicznego muszą być unikatowe w organizacji.

   :::image type="content" source="media/create-ipsec-tunnel/create-vpn-site-links.png" alt-text="Zrzut ekranu pokazujący szczegóły łącza.":::

1. Wybierz pozycję **Przejrzyj i utwórz**. 

1. Przejdź do żądanego koncentratora wirtualnego i usuń zaznaczenie opcji **skojarzenie piasty** , aby połączyć swoją LOKACJĘ sieci VPN z centrum.
 
   :::image type="content" source="../../includes/media/virtual-wan-tutorial-site-include/connect.png" alt-text="Zrzut ekranu przedstawiający okienko połączone Lokacje dla KONCENTRATORa wirtualnego gotowego do Klucz wstępny i skojarzonych ustawień.":::   

## <a name="step-4-optional-create-policy-based-vpn-site-to-site-tunnels"></a>Krok 4. Obowiązkowe Tworzenie opartych na zasadach tuneli sieci VPN typu lokacja-lokacja

>[!IMPORTANT]
>Jest to opcjonalny krok i ma zastosowanie tylko do sieci VPN opartych na zasadach. 

Konfiguracje sieci VPN oparte na zasadach wymagają określenia sieci opartych na lokalnym i rozwiązań VMware platformy Azure, w tym zakresów centrów.  Te zakresy centrów określają domenę szyfrowania w lokalnym punkcie końcowym tunelu sieci VPN opartego na zasadach.  Po stronie rozwiązania VMware dla platformy Azure wymagane jest włączenie wskaźnika selektora ruchu opartego na zasadach. 

1. W Azure Portal przejdź do witryny wirtualnego centrum sieci WAN. W obszarze **łączność** wybierz pozycję **VPN (lokacja-lokacja)**.

2. Wybierz nazwę witryny sieci VPN, wielokropek (...) po prawej stronie, a następnie **Edytuj połączenie sieci VPN z tym centrum**.
 
   :::image type="content" source="media/create-ipsec-tunnel/edit-vpn-section-to-this-hub.png" alt-text="Zrzut ekranu strony na platformie Azure dla witryny wirtualnego centrum sieci WAN z zaznaczoną wielokropkiem, aby uzyskać dostęp do edytowania połączenia sieci VPN z tym centrum." lightbox="media/create-ipsec-tunnel/edit-vpn-section-to-this-hub.png":::

3. Edytuj połączenie między lokacją sieci VPN a centrum, a następnie wybierz pozycję **Zapisz**.
   - Zabezpieczenia protokołu internetowego (IPSec), wybierz opcję **niestandardowy**.
   - Użyj selektora ruchu opartego na zasadach, wybierz pozycję **Włącz**
   - Określ szczegóły dla **usługi IKE phase 1** i **IKE Phase 2 (IPSec)**. 
 
   :::image type="content" source="media/create-ipsec-tunnel/edit-vpn-connection.png" alt-text="Zrzut ekranu przedstawiający stronę Edytowanie połączenia sieci VPN."::: 
 
   Selektory ruchu lub podsieci, które są częścią domeny szyfrowania opartej na zasadach, powinny mieć następujące wartości:
    
   - Koncentrator wirtualnych sieci WAN `/24`
   - Chmura prywatna rozwiązania Azure VMware `/22`
   - Połączona sieć wirtualna platformy Azure (jeśli istnieje)

## <a name="step-5-connect-your-vpn-site-to-the-hub"></a>Krok 5. Łączenie witryny sieci VPN z centrum

1. Wybierz nazwę witryny sieci VPN, a następnie wybierz pozycję **Połącz witryny sieci VPN**. 

1. W polu **klucz wstępny** wprowadź klucz wcześniej zdefiniowany dla lokalnego punktu końcowego. 

   >[!TIP]
   >Jeśli nie masz wcześniej zdefiniowanego klucza, możesz pozostawić to pole puste. Klucz jest generowany automatycznie. 

   :::image type="content" source="../../includes/media/virtual-wan-tutorial-connect-vpn-site-include/connect.png" alt-text="Zrzut ekranu przedstawiający okienko połączone Lokacje dla KONCENTRATORa wirtualnego gotowego do Klucz wstępny i skojarzonych ustawień. "::: 

1. Jeśli instalujesz zaporę w centrum i jest ona następnym przeskokiem, ustaw opcję **Propaguj trasę domyślną** , aby **włączyć**. 

   Gdy ta funkcja jest włączona, koncentrator wirtualnego sieci WAN propaguje do połączenia tylko wtedy, gdy koncentrator już wybrał domyślną trasę podczas wdrażania zapory w centrum lub jeśli włączono tunelowanie wymuszone. Trasa domyślna nie pochodzi z wirtualnego koncentratora sieci WAN.  

1. Wybierz pozycję **Połącz**. Po kilku minutach lokacja pokazuje stan połączenia i łączności.

   :::image type="content" source="../../includes/media/virtual-wan-tutorial-connect-vpn-site-include/status.png" alt-text="Zrzut ekranu pokazujący stan połączenia między lokacjami i łączności." lightbox="../../includes/media/virtual-wan-tutorial-connect-vpn-site-include/status.png":::

1. [Pobierz plik konfiguracji sieci VPN](../virtual-wan/virtual-wan-site-to-site-portal.md#device) dla lokalnego punktu końcowego.  

3. Należy zastosować poprawki do usługi Azure VMware ExpressRoute w wirtualnym koncentratorze sieci WAN. 

   >[!IMPORTANT]
   >Przed zainstalowaniem platformy należy najpierw utworzyć chmurę prywatną. 

   [!INCLUDE [request-authorization-key](includes/request-authorization-key.md)]

4. Połącz rozwiązanie VMware platformy Azure i bramę sieci VPN ze sobą w wirtualnym koncentratorze sieci WAN. Będziesz używać klucza autoryzacji i identyfikatora ExpressRoute (URI obwodu równorzędnego) z poprzedniego kroku.

   1. Wybierz bramę ExpressRoute, a następnie wybierz pozycję **Zrealizuj klucz autoryzacji**.

      :::image type="content" source="media/create-ipsec-tunnel/redeem-authorization-key.png" alt-text="Zrzut ekranu strony ExpressRoute dla chmury prywatnej z wybranym kluczem autoryzacji Zrealizuj.":::

   1. Wklej klucz autoryzacji w polu **klucz autoryzacji** .
   1. Wklej identyfikator ExpressRoute do pola **identyfikatora URI obwodu równorzędnego** . 
   1. Zaznacz pole wyboru **automatycznie skojarz ten obwód ExpressRoute z centrum** . 
   1. Wybierz pozycję **Dodaj** , aby nawiązać połączenie. 

5. Przetestuj połączenie, [tworząc segment NSX-T](./tutorial-nsx-t-network-segment.md) i udostępniając maszynę wirtualną w sieci. Wyślij polecenie ping do punktów końcowych rozwiązań lokalnych i programu VMware platformy Azure.
