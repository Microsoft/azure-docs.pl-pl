---
title: Łączenie bramy sieci wirtualnej z wirtualną siecią WAN platformy Azure
description: Ten artykuł pomaga połączyć bramę sieci wirtualnej platformy Azure z bramą sieci VPN platformy Azure Virtual WAN
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: eb31bb09c1e09e4799715fc5a1cbca4b216b28a2
ms.sourcegitcommit: fec60094b829270387c104cc6c21257826fccc54
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/09/2020
ms.locfileid: "96922965"
---
# <a name="connect-a-vpn-gateway-virtual-network-gateway-to-virtual-wan"></a>Łączenie VPN Gateway (Brama sieci wirtualnej) z wirtualną siecią WAN

Ten artykuł ułatwia skonfigurowanie łączności z usługą Azure VPN Gateway (Brama sieci wirtualnej) z wirtualną siecią WAN platformy Azure (VPN Gateway). Tworzenie połączenia z VPN Gateway (Brama sieci wirtualnej) do wirtualnej sieci WAN (bramy sieci VPN) jest podobne do konfigurowania łączności z witryną sieci VPN gałęzi.

Aby zminimalizować możliwe pomyłki między dwoma funkcjami, firma Microsoft ponosi bramę o nazwie funkcji, do której się odwołuje. Na przykład VPN Gateway bramy sieci wirtualnej i bramy sieci VPN wirtualnej sieci WAN.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Przed rozpoczęciem Utwórz następujące zasoby:

Azure Virtual WAN

* [Utwórz wirtualną sieć WAN](virtual-wan-site-to-site-portal.md#openvwan).
* [Utwórz centrum](virtual-wan-site-to-site-portal.md#hub). Koncentrator wirtualny zawiera wirtualną bramę sieci VPN w sieci WAN.

Azure Virtual Network

* Utwórz sieć wirtualną bez bram sieci wirtualnej. Sprawdź, czy żadna z podsieci sieci lokalnych nie nakłada się na sieci wirtualne, z którymi chcesz nawiązać połączenie. Aby utworzyć sieć wirtualną w Azure Portal, zobacz [Przewodnik Szybki Start](../virtual-network/quick-create-portal.md).

## <a name="1-create-a-vpn-gateway-virtual-network-gateway"></a><a name="vnetgw"></a>1. Utwórz bramę sieci wirtualnej VPN Gateway

Utwórz bramę sieci wirtualnej **VPN Gateway** w trybie aktywny-aktywny dla sieci wirtualnej. Podczas tworzenia bramy można użyć istniejących publicznych adresów IP dla dwóch wystąpień bramy lub można utworzyć nowe publiczne IP. Te publiczne adresy IP będą używane podczas konfigurowania wirtualnych witryn sieci WAN. Aby uzyskać więcej informacji na temat bram sieci VPN i kroków konfiguracji usługi Active-Active, zobacz [Konfigurowanie aktywnych i aktywnych bram sieci VPN](../vpn-gateway/vpn-gateway-activeactive-rm-powershell.md#aagateway).

### <a name="active-active-mode-setting"></a><a name="active-active"></a>Ustawienie trybu aktywne-aktywne

Na stronie **Konfiguracja** bramy sieci wirtualnej Włącz tryb aktywny-aktywny.

![aktywne-aktywne](./media/connect-virtual-network-gateway-vwan/active.png "aktywne-aktywne")

### <a name="bgp-setting"></a><a name="BGP"></a>Ustawienie protokołu BGP

Na stronie **Konfiguracja** bramy sieci wirtualnej można skonfigurować protokół **BGP**. Zmień wartość ASN protokołu BGP. Wartość ASN protokołu BGP nie może być 65515. 65515 będzie używana przez wirtualną sieć WAN platformy Azure.

![Zrzut ekranu przedstawia stronę konfiguracji bramy sieci wirtualnej z wybraną pozycją Konfiguruj protokół BGP ASN.](./media/connect-virtual-network-gateway-vwan/bgp.png "Protokół")

### <a name="public-ip-addresses"></a><a name="pip"></a>Publiczne adresy IP

Po utworzeniu bramy przejdź do strony **Właściwości** . Właściwości i ustawienia konfiguracji będą podobne do poniższego przykładu. Zwróć uwagę na dwa publiczne adresy IP, które są używane przez bramę.

![aœciwoœci](./media/connect-virtual-network-gateway-vwan/publicip.png "properties")

## <a name="2-create-virtual-wan-vpn-sites"></a><a name="vwansite"></a>2. Utwórz witryny sieci VPN wirtualnej sieci WAN

Aby utworzyć witryny sieci VPN wirtualnej sieci WAN, przejdź do swojej wirtualnej sieci WAN, a następnie w obszarze **łączność** wybierz pozycję **Lokacje sieci VPN**. W tej sekcji utworzysz dwie lokacje wirtualnej sieci WAN, które odpowiadają bramom sieci wirtualnej utworzonym w poprzedniej sekcji.

1. Wybierz pozycję **+ Utwórz lokację**.
2. Na stronie **Tworzenie witryn sieci VPN** wpisz następujące wartości:

   * **Region** — ten sam region, w którym znajduje się Brama sieci wirtualnej platformy Azure VPN Gateway.
   * **Dostawca urządzenia** — wprowadź nazwę dostawcy urządzenia (dowolna nazwa).
   * **Prywatna przestrzeń adresowa** — wprowadź wartość lub pozostaw puste, gdy jest włączony protokół BGP.
   * **Border Gateway Protocol** -ustaw, aby **włączyć** , jeśli Brama sieci wirtualnej platformy Azure VPN Gateway ma włączony protokół BGP.
   * **Łączenie z centrami** — wybierz centrum utworzone w sekcji wymagania wstępne z listy rozwijanej. Jeśli nie widzisz centrum, sprawdź, czy utworzono bramę sieci VPN typu lokacja-lokacja dla swojego centrum.
3. W obszarze **linki** wprowadź następujące wartości:

   * **Nazwa dostawcy** — wprowadź nazwę łącza i nazwę dostawcy (dowolną nazwę).
   * **Szybkość i szybkość** (dowolna liczba).
   * **Adres IP** — wprowadź adres IP (taki sam jak pierwszy publiczny adres IP widoczny w obszarze właściwości bramy sieci wirtualnej (VPN Gateway)).
   * **Adres BGP** i numer **ASN** -BGP oraz numer ASN. Muszą one być takie same jak jeden z adresów IP elementów równorzędnych BGP i ASN z bramy sieci wirtualnej VPN Gateway skonfigurowanej w [kroku 1](#vnetgw).
4. Przejrzyj i wybierz pozycję **Potwierdź** , aby utworzyć lokację.
5. Powtórz poprzednie kroki, aby utworzyć drugą lokację zgodną z drugim wystąpieniem VPN Gateway bramy sieci wirtualnej. Będziesz mieć te same ustawienia, z wyjątkiem używania drugiego publicznego adresu IP i drugiego adresu IP elementu równorzędnego BGP z konfiguracji VPN Gateway.
6. Teraz zostały pomyślnie wdrożone dwie lokacje i można przejoć do następnej sekcji, aby pobrać pliki konfiguracji.

## <a name="3-download-the-vpn-configuration-files"></a><a name="downloadconfig"></a>3. Pobierz pliki konfiguracji sieci VPN

W tej sekcji pobrano plik konfiguracji sieci VPN dla każdej lokacji utworzonej w poprzedniej sekcji.

1. W górnej części strony wirtualne **sieci VPN** sieci WAN wybierz **lokację**, a następnie wybierz pozycję **Pobierz konfigurację sieci VPN typu lokacja-lokacja**. Na platformie Azure zostanie utworzony plik konfiguracji z ustawieniami.

   ![Zrzut ekranu przedstawiający stronę "witryny sieci VPN" z wybraną akcją Pobierz konfigurację sieci VPN typu lokacja-lokacja.](./media/connect-virtual-network-gateway-vwan/download.png "pobieranie")
2. Pobierz i Otwórz plik konfiguracji.
3. Powtórz te kroki dla drugiej lokacji. Po otwarciu obu plików konfiguracji można przejoć do następnej sekcji.

## <a name="4-create-the-local-network-gateways"></a><a name="createlocalgateways"></a>4. Utwórz bramy sieci lokalnej

W tej sekcji utworzysz dwie bramy sieci lokalnej na platformie Azure VPN Gateway. Pliki konfiguracji z poprzedniego kroku zawierają ustawienia konfiguracji bramy. Te ustawienia służą do tworzenia i konfigurowania bram sieci lokalnej platformy Azure VPN Gateway.

1. Utwórz bramę sieci lokalnej przy użyciu tych ustawień. Aby uzyskać informacje dotyczące sposobu tworzenia VPN Gateway bramy sieci lokalnej, zobacz artykuł VPN Gateway [Tworzenie bramy sieci lokalnej](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md#LocalNetworkGateway).

   * **Adres IP** — Użyj adresu IP Instance0 podanego dla *gatewayconfiguration* z pliku konfiguracji.
   * **BGP** — Jeśli połączenie jest nawiązywane za pośrednictwem protokołu BGP, wybierz pozycję **Konfiguruj ustawienia protokołu BGP** i wprowadź numer ASN "65515". Wprowadź adres IP elementu równorzędnego BGP. Użyj "Instance0 BgpPeeringAddresses" dla *gatewayconfiguration* z pliku konfiguracyjnego.
   * **Subskrypcja, Grupa zasobów i lokalizacja** są takie same jak dla wirtualnego KONCENTRATORA sieci WAN.
2. Przejrzyj i Utwórz bramę sieci lokalnej. Brama sieci lokalnej powinna wyglądać podobnie do tego przykładu.

   ![Zrzut ekranu przedstawiający stronę "Konfiguracja" z wyróżnionym adresem IP i wybraną opcją "Konfiguruj ustawienia protokołu BGP".](./media/connect-virtual-network-gateway-vwan/lng1.png "instance0")
3. Powtórz te kroki, aby utworzyć kolejną bramę sieci lokalnej, ale tym razem użyj wartości "Wystąpienia1" zamiast wartości "Instance0" z pliku konfiguracji.

   ![Pobierz plik konfiguracji](./media/connect-virtual-network-gateway-vwan/lng2.png "wystąpienia1")

## <a name="5-create-connections"></a><a name="createlocalgateways"></a>5. Utwórz połączenia

W tej sekcji utworzysz połączenie między bramami sieci lokalnej VPN Gateway i bramą sieci wirtualnej. Aby uzyskać instrukcje dotyczące sposobu tworzenia połączenia VPN Gateway, zobacz [Configure a Connection](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md#CreateConnection).

1. W portalu przejdź do bramy sieci wirtualnej, a następnie kliknij pozycję **połączenia**. W górnej części strony Połączenia kliknij pozycję **+Dodaj**, aby otworzyć stronę **Dodawanie połączenia**.
2. Na stronie **Dodawanie połączenia** skonfiguruj następujące wartości dla połączenia:

   * **Nazwa:** nazwij połączenie.
   * **Typ połączenia:** Wybierz pozycję **lokacja-lokacja (IPSec)**
   * **Brama sieci wirtualnej:** wartość jest stała, ponieważ połączenie jest nawiązywane z tej bramy.
   * **Brama sieci lokalnej:** To połączenie spowoduje połączenie bramy sieci wirtualnej z bramą sieci lokalnej. Wybierz jedną z utworzonych wcześniej bram sieci lokalnej.
   * **Klucz współużytkowany:** Wprowadź klucz współużytkowany.
   * **Protokół IKE:** Wybierz protokół IKE.
3. Kliknij przycisk **OK**, aby utworzyć połączenie.
4. Połączenie będzie widoczne na stronie **Połączenia** bramy sieci wirtualnej.

   ![Połączenie](./media/connect-virtual-network-gateway-vwan/connect.png "połączenie")
5. Powtórz powyższe kroki, aby utworzyć drugie połączenie. W przypadku drugiego połączenia wybierz inną utworzoną bramę sieci lokalnej.
6. Jeśli połączenia są nawiązywane za pośrednictwem protokołu BGP, po utworzeniu połączeń przejdź do połączenia i wybierz pozycję **Konfiguracja**. Na stronie **Konfiguracja** dla protokołu **BGP** wybierz opcję **włączone**. Następnie kliknij przycisk **Zapisz**. Powtórz dla drugiego połączenia.

## <a name="6-test-connections"></a><a name="test"></a>6. Testowanie połączeń

Połączenie można przetestować przez utworzenie dwóch maszyn wirtualnych, jednej po stronie VPN Gateway bramy sieci wirtualnej i jednej w sieci wirtualnej dla wirtualnej sieci WAN, a następnie wysłanie polecenia ping do dwóch maszyn wirtualnych.

1. Utwórz maszynę wirtualną w sieci wirtualnej (TEST1-VNet) dla platformy Azure VPN Gateway (TEST1-VNG). Nie należy tworzyć maszyny wirtualnej w GatewaySubnet.
2. Utwórz kolejną sieć wirtualną, aby nawiązać połączenie z wirtualną siecią WAN. Utwórz maszynę wirtualną w podsieci tej sieci wirtualnej. Ta sieć wirtualna nie może zawierać żadnych bram sieci wirtualnej. Możesz szybko utworzyć sieć wirtualną, korzystając z kroków programu PowerShell w artykule [połączenie lokacja-lokacja](virtual-wan-site-to-site-portal.md#vnet) . Przed uruchomieniem poleceń cmdlet należy zmienić wartości.
3. Podłącz sieć wirtualną do koncentratora sieci WAN. Na stronie wirtualnej sieci WAN wybierz opcję **połączenia sieci wirtualnej**, a następnie pozycję **Dodaj połączenie**. Na stronie **Dodaj połączenie** wypełnij następujące pola:

    * **Nazwa połączenia** — nazwij połączenie.
    * **Koncentratory** — wybierz koncentrator, z którym chcesz skojarzyć to połączenie.
    * **Subskrypcja** — sprawdź, czy wybrano właściwą subskrypcję.
    * **Sieć wirtualna** — wybierz sieć wirtualną, którą chcesz połączyć z tym koncentratorem. Sieć wirtualna nie może mieć istniejącej bramy sieci wirtualnej.
4. Kliknij przycisk **OK** , aby utworzyć połączenie sieci wirtualnej.
5. Połączenie jest teraz ustawione między maszynami wirtualnymi. Należy mieć możliwość wysyłania polecenia ping do jednej maszyny wirtualnej z drugiej, chyba że istnieją zapory lub inne zasady blokujące komunikację.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać instrukcje dotyczące konfigurowania niestandardowych zasad protokołu IPsec, zobacz [Konfigurowanie niestandardowych zasad protokołu IPSec dla wirtualnej sieci WAN](virtual-wan-custom-ipsec-portal.md).
Aby uzyskać więcej informacji na temat wirtualnej sieci WAN, zobacz [temat informacje o wirtualnej sieci WAN platformy Azure](virtual-wan-about.md) i [usłudze Azure Virtual WAN — często zadawane pytania](virtual-wan-faq.md).
