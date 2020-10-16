---
title: 'Utwórz połączenie między sieci wirtualnych: klasyczne: Azure Portal'
description: Łączenie sieci wirtualnych platformy Azure przy użyciu programu PowerShell i Azure Portal.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/15/2020
ms.author: cherylmc
ms.openlocfilehash: 0d81e0474d898ffee7f128c0bcea61f077c3d758
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2020
ms.locfileid: "92103224"
---
# <a name="configure-a-vnet-to-vnet-connection-classic"></a>Konfigurowanie połączenia Sieć wirtualna-sieć wirtualna (klasyczna)

Ten artykuł pomaga utworzyć połączenie bramy sieci VPN między sieciami wirtualnymi. Sieci wirtualne mogą być zlokalizowane w tych samych lub różnych regionach i mogą funkcjonować w ramach tej samej lub różnych subskrypcji.

:::image type="content" source="./media/vpn-gateway-howto-vnet-vnet-portal-classic/v2vclassic.png" alt-text="Diagram przedstawiający architekturę klasycznej sieci wirtualnej do sieci wirtualnej":::

[!INCLUDE [deployment models](../../includes/vpn-gateway-classic-deployment-model-include.md)]

Kroki opisane w tym artykule mają zastosowanie do klasycznego modelu wdrażania i Azure Portal. Tę konfigurację możesz również utworzyć przy użyciu innego narzędzia wdrażania lub modelu wdrażania, wybierając inną opcję z następującej listy:

> [!div class="op_single_selector"]
> * [Klasyczny](vpn-gateway-howto-vnet-vnet-portal-classic.md)
> * [Resource Manager](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [Łączenie sieci wirtualnych z różnymi modelami wdrażania](vpn-gateway-connect-different-deployment-models-portal.md)
>
>

## <a name="about-vnet-to-vnet-connections"></a>Informacje o połączeniach między sieciami wirtualnymi

Połączenie sieci wirtualnej z inną siecią wirtualną (VNet-to-VNet) w klasycznym modelu wdrażania przy użyciu bramy sieci VPN jest podobne do łączenia sieci wirtualnej z lokacją lokalną. Oba typy połączeń wykorzystują bramę sieci VPN, aby zapewnić bezpieczny tunel z użyciem protokołu IPsec/IKE.

Połączenie sieci wirtualnych może znajdować się w różnych subskrypcjach i w różnych regionach. Można połączyć sieć wirtualną z siecią wirtualną z konfiguracjami wielolokacjowymi. Pozwala to tworzyć topologie sieci, które łączą wdrożenia obejmujące wiele lokalizacji z połączeniami między sieciami wirtualnymi.

:::image type="content" source="./media/vpn-gateway-howto-vnet-vnet-portal-classic/aboutconnections.png" alt-text="Diagram przedstawiający architekturę klasycznej sieci wirtualnej do sieci wirtualnej":::

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

W przypadku większości kroków używamy portalu, ale należy użyć programu PowerShell do utworzenia połączeń między sieci wirtualnych. Nie można utworzyć połączeń przy użyciu Azure Portal, ponieważ nie ma możliwości określenia klucza współużytkowanego w portalu. [!INCLUDE [vpn-gateway-classic-powershell](../../includes/vpn-gateway-powershell-classic-locally.md)]

## <a name="planning"></a><a name="planning"></a>Planowanie

Ważne jest, aby określić zakresy, które będą używane do konfigurowania sieci wirtualnych. W przypadku tej konfiguracji należy upewnić się, że żaden z zakresów sieci wirtualnej nie nakłada się na siebie lub z żadnym z sieci lokalnych, z którymi się łączą.

### <a name="vnets"></a><a name="vnet"></a>Sieci wirtualnych

W tym ćwiczeniu używane są następujące przykładowe wartości:

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
Zakres adresów podsieci: 10.41.0.0/24<br>
Grupa zasobów: ClassicRG<br>
Lokalizacja: West US<br>
GatewaySubnet: 10.41.1.0/27

### <a name="connections"></a><a name="plan"></a>Połączenia

W poniższej tabeli przedstawiono przykład sposobu nawiązywania połączenia z usługą sieci wirtualnych. Używaj zakresów jako tylko wskazówki. Zapisz zakresy dla sieci wirtualnych. Te informacje są potrzebne do wykonania kolejnych kroków.

W tym przykładzie sieci testvnet1 nawiązuje połączenie z lokalną lokacją sieciową utworzoną przy użyciu nazwy "VNet4Local". Ustawienia VNet4Local zawierają prefiksy adresów dla sieci testvnet4.
Lokacja lokalna dla każdej sieci wirtualnej to inna Sieć wirtualna. W naszej konfiguracji są używane następujące przykładowe wartości:

**Przykład**

| Virtual Network | Przestrzeń adresowa | Lokalizacja | Nawiązuje połączenie z lokacją sieci lokalnej |
|:--- |:--- |:--- |:--- |
| TestVNet1 |TestVNet1<br>(10.11.0.0/16)<br>(10.12.0.0/16) |East US |SiteVNet4<br>(10.41.0.0/16)<br>(10.42.0.0/16) |
| TestVNet4 |TestVNet4<br>(10.41.0.0/16)<br>(10.42.0.0/16) |Zachodnie stany USA |SiteVNet1<br>(10.11.0.0/16)<br>(10.12.0.0/16) |

## <a name="create-virtual-networks"></a><a name="vnetvalues"></a>Tworzenie sieci wirtualnych

W tym kroku utworzysz dwie klasyczne sieci wirtualne, sieci testvnet1 i sieci testvnet4. Jeśli używasz tego artykułu jako ćwiczenia, użyj [przykładowych wartości](#vnet).

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

## <a name="configure-sites-and-gateways"></a><a name="localsite"></a>Konfigurowanie lokacji i bram

Platforma Azure używa ustawień określonych w każdej lokacji sieci lokalnej w celu określenia sposobu kierowania ruchu między sieci wirtualnych. Poszczególne sieci wirtualne muszą wskazywać odpowiednią sieć lokalną, do której ma być kierowany ruch. Należy określić nazwę, która ma być używana do odwoływania się do każdej lokacji sieci lokalnej. Najlepiej używać czegoś opisowego.

Na przykład program sieci testvnet1 nawiązuje połączenie z lokalną lokacją sieciową utworzoną przy użyciu nazwy "VNet4Local". Ustawienia VNet4Local zawierają prefiksy adresów dla sieci testvnet4.

Należy pamiętać, że lokacja lokalna dla każdej sieci wirtualnej to inna Sieć wirtualna.

| Virtual Network | Przestrzeń adresowa | Lokalizacja | Nawiązuje połączenie z lokacją sieci lokalnej |
|:--- |:--- |:--- |:--- |
| TestVNet1 |TestVNet1<br>(10.11.0.0/16)<br>(10.12.0.0/16) |East US |SiteVNet4<br>(10.41.0.0/16)<br>(10.42.0.0/16) |
| TestVNet4 |TestVNet4<br>(10.41.0.0/16)<br>(10.42.0.0/16) |Zachodnie stany USA |SiteVNet1<br>(10.11.0.0/16)<br>(10.12.0.0/16) |

### <a name="to-configure-a-site"></a><a name="site"></a>Aby skonfigurować lokację

Lokacja lokalna zazwyczaj oznacza lokalizację lokalną. Zawiera ona adres IP urządzenia sieci VPN, z którym będzie tworzone połączenie, oraz zakresy adresów IP, które będą kierowane za pośrednictwem bramy sieci VPN do tego urządzenia sieci VPN.

1. Na stronie sieci wirtualnej w obszarze **Ustawienia**wybierz pozycję **połączenia lokacja-lokacja**.
1. Na stronie połączenia między lokacjami wybierz pozycję **+ Dodaj**.
1. Na stronie **Konfigurowanie połączenia sieci VPN i bramy** dla **typu połączenia**pozostaw wybraną opcję **lokacja-lokacja** .

   * **Adres IP bramy sieci VPN:** Publiczny adres IP urządzenia sieci VPN w sieci lokalnej. W tym ćwiczeniu można umieścić fikcyjny adres, ponieważ nie ma jeszcze adresu IP bramy sieci VPN dla innej lokacji. Na przykład 5.4.3.2. Później, po skonfigurowaniu bramy dla innej sieci wirtualnej, możesz dostosować tę wartość.

   * **Przestrzeń adresowa klienta:** Utwórz listę zakresów adresów IP, które mają być kierowane do innej sieci wirtualnej za pomocą tej bramy. Można dodać wiele zakresów przestrzeni adresów. Upewnij się, że określone w tym miejscu zakresy nie pokrywają się z zakresami innych sieci, z którymi łączy się Twoja sieć wirtualna, ani z zakresami adresów samej sieci wirtualnej.
1. W dolnej części strony nie wybieraj opcji Recenzja + Utwórz. Zamiast tego wybierz pozycję **Dalej: brama>**.

### <a name="to-configure-a-virtual-network-gateway"></a><a name="sku"></a>Aby skonfigurować bramę sieci wirtualnej

1. Na stronie **brama** wybierz następujące wartości:

   * **Rozmiar:** Jest to jednostka SKU bramy używana do tworzenia bramy sieci wirtualnej. Klasyczne bramy sieci VPN używają starych (starszych) jednostek SKU bramy. Aby uzyskać więcej informacji o starszych jednostkach SKU bramy, zobacz [Working with virtual network gateway SKUs (old SKUs) (Praca z jednostkami SKU [starymi jednostkami SKU] bramy sieci wirtualnej)](vpn-gateway-about-skus-legacy.md). Możesz wybrać opcję **standardowa** dla tego ćwiczenia.

   * **Typ routingu:** Wybierz typ routingu dla bramy. Jest on również nazywany typem sieci VPN. Ważne jest, aby wybrać właściwy typ, ponieważ nie można skonwertować bramy z jednego typu na inny. Urządzenie sieci VPN musi być zgodne z wybranym typem routingu. Aby uzyskać więcej informacji na temat typu routingu, zobacz [Informacje o ustawieniach VPN Gateway](vpn-gateway-about-vpn-gateway-settings.md#vpntype). W niektórych artykułach mogą znajdować się odwołania do typów sieci VPN „RouteBased” i „PolicyBased”. Typ „Dynamiczny” odpowiada typowi „RouteBased”, a „Statyczny” — typowi „PolicyBased”. W tej konfiguracji wybierz pozycję **dynamiczne**.

   * **Podsieć bramy:** Określony rozmiar podsieci bramy zależy od konfiguracji bramy sieci VPN, która ma zostać utworzona. Jest możliwe utworzenie małej podsieci bramy (/29), jednak zalecamy użycie rozmiaru /27 lub /28. Spowoduje to utworzenie większej podsieci obejmującej więcej adresów. Zastosowanie większej podsieci bramy daje wystarczającą liczbę adresów IP, aby uwzględnić możliwe przyszłe konfiguracje.

1. Wybierz pozycję **Przegląd + Utwórz** u dołu strony, aby sprawdzić poprawność ustawień. Wybierz pozycję **Utwórz** do wdrożenia. Utworzenie bramy sieci wirtualnej może potrwać do 45 minut, w zależności od wybranej jednostki SKU bramy.
1. Możesz rozpocząć przejdź do następnego kroku podczas tworzenia tej bramy.

### <a name="configure-testvnet4-settings"></a>Konfigurowanie ustawień sieci testvnet4

Powtórz kroki dla opcji [Utwórz lokację i bramę](#localsite) , aby skonfigurować sieci testvnet4, podstawiając wartości w razie potrzeby. Jeśli wykonujesz tę opcję jako ćwiczenie, użyj [przykładowych wartości](#planning).

## <a name="update-local-sites"></a><a name="updatelocal"></a>Aktualizuj Lokacje lokalne

Po utworzeniu bram sieci wirtualnej dla obu sieci wirtualnych należy dostosować właściwości lokacji lokalnej dla **adresu IP bramy sieci VPN**.

|Nazwa sieci wirtualnej|Połączona lokacja|Adres IP bramy|
|:--- |:--- |:--- |
|TestVNet1|VNet4Local|Adres IP bramy sieci VPN dla sieci testvnet4|
|TestVNet4|VNet1Local|Adres IP bramy sieci VPN dla sieci testvnet1|

### <a name="part-1---get-the-virtual-network-gateway-public-ip-address"></a>Część 1 — pobieranie publicznego adresu IP bramy sieci wirtualnej

1. Przejdź do swojej sieci wirtualnej, przechodząc do **grupy zasobów** i wybierając sieć wirtualną.
1. Na stronie sieci wirtualnej w okienku **podstawowe** po prawej stronie Znajdź **adres IP bramy** i skopiuj go do Schowka.

### <a name="part-2---modify-the-local-site-properties"></a>Część 2 — Modyfikowanie właściwości lokacji lokalnej

1. W obszarze połączenia lokacja-lokacja wybierz połączenie. Na przykład SiteVNet4.
1. Na stronie **Właściwości** połączenia lokacja-lokacja wybierz pozycję **Edytuj lokację lokalną**.
1. W polu **adres IP bramy sieci VPN** Wklej adres IP bramy sieci VPN skopiowany w poprzedniej sekcji.
1. Wybierz przycisk **OK**.
1. Pole jest aktualizowane w systemie. Możesz również użyć tej metody, aby dodać dodatkowy adres IP, który ma zostać rozesłany do tej lokacji.

### <a name="part-3---repeat-steps-for-the-other-vnet"></a>Część 3 — Powtórz kroki dla drugiej sieci wirtualnej

Powtórz kroki dla sieci testvnet4.

## <a name="retrieve-configuration-values"></a><a name="getvalues"></a>Pobierz wartości konfiguracyjne

[!INCLUDE [retrieve values](../../includes/vpn-gateway-values-classic.md)]

## <a name="create-connections"></a><a name="createconnections"></a>Tworzenie połączeń

Po ukończeniu wszystkich poprzednich kroków można ustawić wstępnie udostępnione klucze IPsec/IKE i utworzyć połączenie. Ten zestaw kroków używa programu PowerShell. W Azure Portal nie można skonfigurować połączeń między sieciami wirtualnymi dla klasycznego modelu wdrażania, ponieważ nie można określić klucza współużytkowanego w portalu.

W przykładach należy zauważyć, że klucz współużytkowany jest dokładnie taki sam. Klucz współużytkowany musi być zawsze zgodny. Pamiętaj, aby zastąpić wartości w tych przykładach dokładnymi nazwami witryn sieci sieci wirtualnych i lokalnych.

1. Utwórz połączenie z sieci wirtualnej TestVNet1 do sieci wirtualnej TestVNet4. Upewnij się, że wartości zostały zmienione.

   ```powershell
   Set-AzureVNetGatewayKey -VNetName 'Group ClassicRG TestVNet1' `
   -LocalNetworkSiteName 'value for _VNet4Local' -SharedKey A1b2C3D4
   ```
2. Utwórz połączenie z sieci wirtualnej TestVNet4 do sieci wirtualnej TestVNet1.

   ```powershell
   Set-AzureVNetGatewayKey -VNetName 'Group ClassicRG TestVNet4' `
   -LocalNetworkSiteName 'value for _VNet1Local' -SharedKey A1b2C3D4
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

## <a name="faq-and-considerations"></a><a name="faq"></a>Często zadawane pytania i zagadnienia

Te zagadnienia dotyczą klasycznych sieci wirtualnych i klasycznych bram sieci wirtualnej.

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
