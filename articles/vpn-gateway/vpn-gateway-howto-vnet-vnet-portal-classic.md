---
title: 'Utwórz połączenie między sieci wirtualnych: klasyczne: Azure Portal'
description: Łączenie sieci wirtualnych platformy Azure przy użyciu programu PowerShell i Azure Portal.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/08/2020
ms.author: cherylmc
ms.openlocfilehash: 4b1007fe89cf455b6af8ebba00f24e8019ad8013
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2020
ms.locfileid: "92078293"
---
# <a name="configure-a-vnet-to-vnet-connection-classic"></a>Konfigurowanie połączenia Sieć wirtualna-sieć wirtualna (klasyczna)

[!INCLUDE [deployment models](../../includes/vpn-gateway-classic-deployment-model-include.md)]

Ten artykuł pomaga utworzyć połączenie bramy sieci VPN między sieciami wirtualnymi. Sieci wirtualne mogą być zlokalizowane w tych samych lub różnych regionach i mogą funkcjonować w ramach tej samej lub różnych subskrypcji. Kroki opisane w tym artykule mają zastosowanie do klasycznego modelu wdrażania i Azure Portal. Tę konfigurację możesz również utworzyć przy użyciu innego narzędzia wdrażania lub modelu wdrażania, wybierając inną opcję z następującej listy:

> [!div class="op_single_selector"]
> * [Witryna Azure Portal](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [Program PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
> * [Interfejs wiersza polecenia platformy Azure](vpn-gateway-howto-vnet-vnet-cli.md)
> * [Portal Azure (klasyczny)](vpn-gateway-howto-vnet-vnet-portal-classic.md)
> * [Łączenie różnych modeli wdrażania — witryna Azure Portal](vpn-gateway-connect-different-deployment-models-portal.md)
> * [Łączenie różnych modeli wdrażania — program PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
>
>

![Diagram łączności między sieciami wirtualnymi](./media/vpn-gateway-howto-vnet-vnet-portal-classic/v2vclassic.png)

## <a name="about-vnet-to-vnet-connections"></a>Informacje o połączeniach między sieciami wirtualnymi

Połączenie sieci wirtualnej z inną siecią wirtualną (VNet-to-VNet) w klasycznym modelu wdrażania przy użyciu bramy sieci VPN jest podobne do łączenia sieci wirtualnej z lokacją lokalną. Oba typy połączeń wykorzystują bramę sieci VPN, aby zapewnić bezpieczny tunel z użyciem protokołu IPsec/IKE.

Połączenie sieci wirtualnych może znajdować się w różnych subskrypcjach i w różnych regionach. Można połączyć sieć wirtualną z siecią wirtualną z konfiguracjami wielolokacjowymi. Pozwala to tworzyć topologie sieci, które łączą wdrożenia obejmujące wiele lokalizacji z połączeniami między sieciami wirtualnymi.

![Połączenia sieci wirtualnej z siecią wirtualną](./media/vpn-gateway-howto-vnet-vnet-portal-classic/aboutconnections.png)

### <a name="why-connect-virtual-networks"></a><a name="why"></a>Dlaczego łączy się sieci wirtualne?

Sieci wirtualne można łączyć z następujących powodów:

* **Niezależna od regionu nadmiarowość i obecność geograficzna**

  * Można tworzyć własne replikacje geograficzne lub przeprowadzać synchronizację z bezpiecznym połączeniem bez przechodzenia przez punkty końcowe dostępne z Internetu.
  * Dzięki Azure Load Balancer i technologii klastrowania firmy Microsoft lub innych firm można skonfigurować obciążenie o wysokiej dostępności z nadmiarowością geograficzną w wielu regionach świadczenia usługi Azure. Istotnym przykładem jest konfiguracja ustawienia SQL Zawsze włączone, w przypadku którego grupy dostępności rozciągają się na wiele regionów świadczenia usługi Azure.
* **Regionalne aplikacje wielowarstwowe ze ścisłą izolacją**

  * W tym samym regionie można skonfigurować wielowarstwowe aplikacje z wieloma sieci wirtualnych połączonymi ze sobą za pomocą silnej izolacji i bezpiecznej komunikacji między warstwami.
* **Subskrypcja komunikacji między organizacjami na platformie Azure**

  * Jeśli masz wiele subskrypcji platformy Azure, możesz bezpiecznie łączyć obciążenia z różnych subskrypcji między sieciami wirtualnymi.
  * W przypadku przedsiębiorstw lub dostawców usług można włączyć komunikację między organizacjami przy użyciu bezpiecznej technologii sieci VPN na platformie Azure.

Więcej informacji na temat połączeń między sieciami wirtualnymi znajduje się w sekcji [Zagadnienia dotyczące połączeń między sieciami wirtualnymi](#faq) na końcu tego artykułu.

## <a name="prerequisites"></a>Wymagania wstępne

W przypadku większości kroków używamy portalu, ale należy użyć programu PowerShell do utworzenia połączeń między sieci wirtualnych. Nie można utworzyć połączeń przy użyciu Azure Portal. [!INCLUDE [vpn-gateway-classic-powershell](../../includes/vpn-gateway-powershell-classic-locally.md)]

## <a name="step-1---plan-your-ip-address-ranges"></a><a name="plan"></a>Krok 1 — planowanie zakresów adresów IP

Ważne jest, aby określić zakresy, które będą używane do konfigurowania sieci wirtualnych. W przypadku tej konfiguracji należy upewnić się, że żaden z zakresów sieci wirtualnej nie nakłada się na siebie lub z żadnym z sieci lokalnych, z którymi się łączą.

W poniższej tabeli przedstawiono przykład sposobu definiowania sieci wirtualnych. Używaj zakresów jako tylko wskazówki. Zapisz zakresy dla sieci wirtualnych. Te informacje są potrzebne do wykonania kolejnych kroków.

**Przykład**

| Virtual Network | Przestrzeń adresowa | Region | Nawiązuje połączenie z lokacją sieci lokalnej |
|:--- |:--- |:--- |:--- |
| TestVNet1 |TestVNet1<br>(10.11.0.0/16)<br>(10.12.0.0/16) |East US |VNet4Local<br>(10.41.0.0/16)<br>(10.42.0.0/16) |
| TestVNet4 |TestVNet4<br>(10.41.0.0/16)<br>(10.42.0.0/16) |Zachodnie stany USA |VNet1Local<br>(10.11.0.0/16)<br>(10.12.0.0/16) |

## <a name="step-2---create-the-virtual-networks"></a><a name="vnetvalues"></a>Krok 2. Tworzenie sieci wirtualnych

W tym kroku utworzysz dwie klasyczne sieci wirtualne. Jeśli używasz tego artykułu jako ćwiczenia, możesz użyć następujących przykładowych wartości:

**Wartości dla sieci testvnet1**

Nazwa: sieci testvnet1<br>
Przestrzeń adresowa: 10.11.0.0/16, 10.12.0.0/16 (opcjonalnie)<br>
Nazwa podsieci: domyślna<br>
Zakres adresów podsieci: 10.11.0.0/24<br>
Grupa zasobów: ClassicRG<br>
Lokalizacja: Wschodnie stany USA<br>
GatewaySubnet: 10.11.1.0/27

**Wartości dla sieci testvnet4**

Nazwa: sieci testvnet4<br>
Przestrzeń adresowa: 10.41.0.0/16, 10.42.0.0/16 (opcjonalnie)<br>
Nazwa podsieci: domyślna<br>
Zakres adresów podsieci: 10.41.0.1/24<br>
Grupa zasobów: ClassicRG<br>
Lokalizacja: West US<br>
GatewaySubnet: 10.41.1.0/27

**Podczas tworzenia sieci wirtualnych należy pamiętać o następujących ustawieniach:**

* **Virtual Network przestrzenie adresowe** — na stronie Virtual Network przestrzeni adresowej Określ zakres adresów, który ma być używany dla sieci wirtualnej. Są to dynamiczne adresy IP, które zostaną przypisane do maszyn wirtualnych i innych wystąpień roli, które są wdrażane w tej sieci wirtualnej.<br>Wybrane przestrzenie adresowe nie mogą nakładać się na przestrzenie adresowe dla żadnej z innych sieci wirtualnych lub lokalizacji lokalnych, z którymi zostanie nawiązane połączenie sieci wirtualnej.

* **Lokalizacja** — podczas tworzenia sieci wirtualnej należy skojarzyć ją z lokalizacją platformy Azure (region). Na przykład jeśli chcesz, aby maszyny wirtualne wdrożone w sieci wirtualnej były fizycznie zlokalizowane w regionie zachodnie stany USA, wybierz tę lokalizację. Nie można zmienić lokalizacji skojarzonej z siecią wirtualną po jej utworzeniu.

**Po utworzeniu sieci wirtualnych można dodać następujące ustawienia:**

* **Przestrzeń adresowa** — dodatkowa przestrzeń adresowa nie jest wymagana dla tej konfiguracji, ale można dodać do niej dodatkową przestrzeń adresową po utworzeniu sieci wirtualnej.

* **Podsieci** — dodatkowe podsieci nie są wymagane dla tej konfiguracji, ale możesz chcieć, aby maszyny wirtualne były w podsieci, która jest oddzielona od innych wystąpień roli.

* **Serwery DNS** — wprowadź nazwę i adres IP serwera DNS. To ustawienie nie powoduje jednak utworzenia serwera DNS. Umożliwia natomiast określenie serwerów DNS, które mają być używane do rozpoznawania nazw dla tej sieci wirtualnej.

### <a name="to-create-a-classic-virtual-network"></a>Aby utworzyć klasyczną sieć wirtualną

[!INCLUDE [basic classic vnet](../../includes/vpn-gateway-vnet-classic.md)]

[!INCLUDE [basic classic DNS](../../includes/vpn-gateway-dns-classic.md)]

## <a name="step-3---configure-the-local-site"></a><a name="localsite"></a>Krok 3 — Konfigurowanie lokacji lokalnej

Platforma Azure używa ustawień określonych w każdej lokacji sieci lokalnej w celu określenia sposobu kierowania ruchu między sieci wirtualnych. Poszczególne sieci wirtualne muszą wskazywać odpowiednią sieć lokalną, do której ma być kierowany ruch. Należy określić nazwę, która ma być używana do odwoływania się do każdej lokacji sieci lokalnej. Najlepiej używać czegoś opisowego.

Na przykład program sieci testvnet1 nawiązuje połączenie z lokalną lokacją sieciową utworzoną przy użyciu nazwy "VNet4Local". Ustawienia VNet4Local zawierają prefiksy adresów dla sieci testvnet4.

Lokacja lokalna dla każdej sieci wirtualnej to inna Sieć wirtualna. W naszej konfiguracji są używane następujące przykładowe wartości:

| Virtual Network | Przestrzeń adresowa | Region | Nawiązuje połączenie z lokacją sieci lokalnej |
|:--- |:--- |:--- |:--- |
| TestVNet1 |TestVNet1<br>(10.11.0.0/16)<br>(10.12.0.0/16) |East US |VNet4Local<br>(10.41.0.0/16)<br>(10.42.0.0/16) |
| TestVNet4 |TestVNet4<br>(10.41.0.0/16)<br>(10.42.0.0/16) |Zachodnie stany USA |VNet1Local<br>(10.11.0.0/16)<br>(10.12.0.0/16) |

1. Znajdź sieci testvnet1 w Azure Portal. W sekcji **połączenia sieci VPN** na stronie kliknij pozycję **brama**.

    ![Brak bramy](./media/vpn-gateway-howto-vnet-vnet-portal-classic/nogateway.png)
2. Na stronie **nowe połączenie VPN** wybierz pozycję **lokacja-lokacja**.
3. Kliknij pozycję **lokacja lokalna** , aby otworzyć stronę lokacja lokalna i skonfigurować ustawienia.
4. Na stronie **lokacja lokalna** Nadaj nazwę witrynie lokalnej. W naszym przykładzie nazwamy witrynę lokalną "VNet4Local".
5. W przypadku **adresu IP bramy sieci VPN**można użyć dowolnego żądanego adresu IP, o ile jest w prawidłowym formacie. Zwykle używany jest rzeczywisty zewnętrzny adres IP dla urządzenia sieci VPN. Jednak w przypadku klasycznej konfiguracji sieci wirtualnej między sieciami wirtualnymi używany jest publiczny adres IP przypisany do bramy dla wirtualnej. Ponieważ nie utworzono jeszcze bramy sieci wirtualnej, należy określić dowolny prawidłowy publiczny adres IP jako symbol zastępczy.<br>Nie opuszczaj tej wartości pustej — ta konfiguracja nie jest opcjonalna. W późniejszym kroku powrócisz do tych ustawień i skonfigurujesz je przy użyciu odpowiednich adresów IP bramy sieci wirtualnej po jej wygenerowaniu przez platformę Azure.
6. W polu **przestrzeń adresowa klienta**Użyj przestrzeni adresowej innej sieci wirtualnej. Zapoznaj się z przykładem planowania. Kliknij przycisk **OK** , aby zapisać ustawienia i wrócić do **nowej strony połączenia sieci VPN** .

    ![lokacja lokalna](./media/vpn-gateway-howto-vnet-vnet-portal-classic/localsite.png)

## <a name="step-4---create-the-virtual-network-gateway"></a><a name="gw"></a>Krok 4 — Tworzenie bramy sieci wirtualnej

Każda sieć wirtualna musi mieć bramę sieci wirtualnej. Brama sieci wirtualnej kieruje i szyfruje ruch.

1. Na stronie **Nowe połączenie VPN** zaznacz pole wyboru **Utwórz bramę natychmiast**.
2. Kliknij pozycję **podsieć, rozmiar i typ routingu**. Na stronie **Konfiguracja bramy** kliknij pozycję **podsieć**.
3. Nazwa podsieci bramy jest wypełniana automatycznie przy użyciu wymaganej nazwy "GatewaySubnet". **Zakres adresów** zawiera adresy IP, które są przyłączone do usług bramy sieci VPN. Niektóre konfiguracje umożliwiają podsieć bramy/29, ale najlepiej użyć wartości/28 lub/27 w celu uwzględnienia w przyszłości konfiguracji, które mogą wymagać większej liczby adresów IP dla usług bramy. W naszych przykładowych ustawieniach korzystamy z 10.11.1.0/27. Dostosuj przestrzeń adresową, a następnie kliknij przycisk **OK**.
4. Skonfiguruj **rozmiar bramy**. To ustawienie odwołuje się do [jednostki SKU bramy](vpn-gateway-about-vpn-gateway-settings.md#gwsku).
5. Skonfiguruj **Typ routingu**. Typ routingu dla tej konfiguracji musi być **dynamiczny**. Nie można zmienić typu routingu później, chyba że zostanie wyłączona brama i zostanie utworzona nowa.
6. Kliknij przycisk **OK**.
7. Na stronie **nowe połączenie VPN** kliknij przycisk **OK** , aby rozpocząć tworzenie bramy sieci wirtualnej. Tworzenie bramy często może trwać 45 minut lub dłużej, w zależności od wybranej jednostki SKU bramy.

## <a name="step-5---configure-testvnet4-settings"></a><a name="vnet4settings"></a>Krok 5 — Konfigurowanie ustawień sieci testvnet4

Powtórz kroki, aby [utworzyć lokację lokalną](#localsite) i [utworzyć bramę sieci wirtualnej](#gw) w celu skonfigurowania sieci testvnet4, podstawiając wartości w razie potrzeby. Jeśli wykonujesz tę opcję jako ćwiczenie, użyj [przykładowych wartości](#vnetvalues).

## <a name="step-6---update-the-local-sites"></a><a name="updatelocal"></a>Krok 6. Aktualizowanie lokacji lokalnych

Po utworzeniu bram sieci wirtualnej dla obu sieci wirtualnych należy dostosować wartości **adresów IP bramy sieci VPN** w lokacjach lokalnych.

|Nazwa sieci wirtualnej|Połączona lokacja|Adres IP bramy|
|:--- |:--- |:--- |
|TestVNet1|VNet4Local|Adres IP bramy sieci VPN dla sieci testvnet4|
|TestVNet4|VNet1Local|Adres IP bramy sieci VPN dla sieci testvnet1|

### <a name="part-1---get-the-virtual-network-gateway-public-ip-address"></a>Część 1 — pobieranie publicznego adresu IP bramy sieci wirtualnej

1. Znajdź sieć wirtualną w Azure Portal.
2. Kliknij, aby otworzyć stronę **omówienia** sieci wirtualnej. Na stronie **połączenia sieci VPN**można wyświetlić adres IP bramy sieci wirtualnej.

   ![Publiczny adres IP](./media/vpn-gateway-howto-vnet-vnet-portal-classic/publicIP.png)
3. Skopiuj adres IP. Zostanie ona użyta w następnej sekcji.
4. Powtórz te kroki dla sieci testvnet4

### <a name="part-2---modify-the-local-sites"></a>Część 2 — Modyfikowanie lokacji lokalnych

1. Znajdź sieć wirtualną w Azure Portal.
2. Na stronie **Omówienie** sieci wirtualnej kliknij lokację lokalną.

   ![Utworzono lokację lokalną](./media/vpn-gateway-howto-vnet-vnet-portal-classic/local.png)
3. Na stronie **połączenia sieci VPN typu lokacja-lokacja** kliknij nazwę lokacji lokalnej, którą chcesz zmodyfikować.

   ![Otwórz lokację lokalną](./media/vpn-gateway-howto-vnet-vnet-portal-classic/openlocal.png)
4. Kliknij **lokację lokalną** , którą chcesz zmodyfikować.

   ![Modyfikuj witrynę](./media/vpn-gateway-howto-vnet-vnet-portal-classic/connections.png)
5. Zaktualizuj **adres IP bramy sieci VPN** i kliknij przycisk **OK** , aby zapisać ustawienia.

   ![adres IP bramy](./media/vpn-gateway-howto-vnet-vnet-portal-classic/gwupdate.png)
6. Zamknij inne strony.
7. Powtórz te kroki dla sieci testvnet4.

## <a name="step-7---retrieve-values-from-the-network-configuration-file"></a><a name="getvalues"></a>Krok 7. Pobieranie wartości z pliku konfiguracji sieci

[!INCLUDE [retrieve values](../../includes/vpn-gateway-values-classic.md)]

## <a name="step-8---create-the-vpn-gateway-connections"></a><a name="createconnections"></a>Krok 8. Tworzenie połączeń bramy sieci VPN

Po ukończeniu wszystkich poprzednich kroków można ustawić wstępnie udostępnione klucze IPsec/IKE i utworzyć połączenie. Ten zestaw kroków używa programu PowerShell. Połączeń między sieciami wirtualnymi dla klasycznego modelu wdrażania nie można skonfigurować w Azure Portal.

W przykładach należy zauważyć, że klucz współużytkowany jest dokładnie taki sam. Klucz współużytkowany musi być zawsze zgodny. Pamiętaj, aby zastąpić wartości w tych przykładach dokładnymi nazwami witryn sieci sieci wirtualnych i lokalnych.

1. Utwórz połączenie z sieci wirtualnej TestVNet1 do sieci wirtualnej TestVNet4.

   ```powershell
   Set-AzureVNetGatewayKey -VNetName 'Group ClassicRG TestVNet1' `
   -LocalNetworkSiteName '17BE5E2C_VNet4Local' -SharedKey A1b2C3D4
   ```
2. Utwórz połączenie z sieci wirtualnej TestVNet4 do sieci wirtualnej TestVNet1.

   ```powershell
   Set-AzureVNetGatewayKey -VNetName 'Group ClassicRG TestVNet4' `
   -LocalNetworkSiteName 'F7F7BFC7_VNet1Local' -SharedKey A1b2C3D4
   ```
3. Zaczekaj na zainicjowanie połączeń. Po zainicjowaniu bramy stan ma wartość "powodzenie".

   ```
   Error          :
   HttpStatusCode : OK
   Id             :
   Status         : Successful
   RequestId      :
   StatusCode     : OK
   ```

## <a name="vnet-to-vnet-considerations-for-classic-vnets"></a><a name="faq"></a>Zagadnienia dotyczące połączeń między sieciami wirtualnymi dotyczące klasycznej sieci wirtualnych
* Sieci wirtualne mogą znajdować się w tych samych lub różnych subskrypcjach.
* Sieci wirtualne mogą znajdować się w tym samym regionie lub w różnych regionach (lokalizacjach) świadczenia usługi Azure.
* Usługa w chmurze lub punkt końcowy równoważenia obciążenia nie może obejmować między sieciami wirtualnymi, nawet jeśli są połączone ze sobą.
* Łączenie wielu sieci wirtualnych nie wymaga żadnych urządzeń sieci VPN.
* Sieć wirtualna-sieć wirtualna obsługuje łączenie sieci wirtualnych platformy Azure. Nie obsługuje on łączenia maszyn wirtualnych ani usług w chmurze, które nie zostały wdrożone w sieci wirtualnej.
* Sieć wirtualna-sieć wirtualna wymaga dynamicznych bram routingu. Bramy routingu statycznego platformy Azure nie są obsługiwane.
* Połączenie sieci wirtualnej może być używane równocześnie z sieciami VPN obejmującymi wiele lokacji. Istnieje maksymalnie 10 tuneli VPN dla wirtualnej sieci VPN bramy nawiązującej połączenie z innymi sieciami wirtualnymi lub lokacjami lokalnymi.
* Przestrzenie adresów sieci wirtualnych i lokalnych witryn sieci obejmujących lokalizacje w obrębie organizacji nie mogą się nakładać. Nakładające się przestrzenie adresowe spowodują utworzenie sieci wirtualnych lub przekazanie plików konfiguracji netcfg do niepowodzenia.
* Nadmiarowe tunele między dwiema sieciami wirtualnymi nie są obsługiwane.
* Wszystkie tunele VPN dla sieci wirtualnej, w tym sieci VPN P2S, udostępniają dostępną przepustowość dla bramy sieci VPN oraz tę samą umowę SLA dotyczącą czasu działania bramy sieci VPN na platformie Azure.
* Ruch między sieciami wirtualnymi odbywa się przez sieć szkieletową platformy Azure.

## <a name="next-steps"></a>Następne kroki
Sprawdź połączenia. Zobacz [Weryfikowanie połączenia VPN Gateway](vpn-gateway-verify-connection-resource-manager.md).
