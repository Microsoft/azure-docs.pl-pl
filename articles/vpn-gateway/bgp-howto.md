---
title: 'Konfigurowanie protokołu BGP dla VPN Gateway: Portal'
titleSuffix: Azure VPN Gateway
description: W tym artykule omówiono procedurę konfigurowania protokołu BGP za pomocą usługi Azure VPN Gateway przy użyciu programu PowerShell.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/18/2020
ms.author: yushwang
ms.openlocfilehash: f52d684d1e6ef63fdf4287c610608061f30395f8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90996870"
---
# <a name="how-to-configure-bgp-on-azure-vpn-gateways"></a>Jak skonfigurować protokół BGP na bramach sieci VPN platformy Azure

W tym artykule przedstawiono kroki umożliwiające włączenie protokołu BGP w ramach połączenia sieci VPN między lokacjami (S2S) i połączenia między sieciami wirtualnymi przy użyciu Azure Portal.

## <a name="about-bgp"></a><a name="about"></a>BGP — informacje

BGP to standardowy protokół routingu używany często w Internecie do wymiany informacji o routingu i osiągalności między dwiema lub wieloma sieciami. Protokół BGP włącza bramy sieci VPN platformy Azure i lokalne urządzenia sieci VPN, nazywane równorzędnymi elementami BGP lub sąsiadami, do wymiany "tras", które będą informować bramę o dostępności i możliwości uzyskania dostępu do tych prefiksów, aby przejść przez bramę lub routery. Protokół BGP umożliwia również włączenie routingu tranzytowego między wieloma sieciami poprzez propagowanie tras, których brama BGP uczy się od jednego elementu równorzędnego BGP, we wszystkich innych elementach równorzędnych BGP.

Aby uzyskać więcej informacji o zaletach protokołu BGP oraz poznać wymagania techniczne i zagadnienia dotyczące korzystania z protokołu BGP, zobacz [Omówienie protokołu BGP z bramami sieci VPN platformy Azure](vpn-gateway-bgp-overview.md).

## <a name="getting-started"></a>Wprowadzenie

Każda część tego artykułu pomaga utworzyć podstawowy blok konstrukcyjny do włączania protokołu BGP w łączności sieciowej. Jeśli wszystkie trzy części zostały wykonane, można utworzyć topologię, jak pokazano na diagramie 1.

**Diagram 1**

:::image type="content" source="./media/bgp-howto/bgp-crosspremises-v2v.png" alt-text="Diagram przedstawiający architekturę i ustawienia sieci" border="false":::

Możesz połączyć części wspólnie, aby utworzyć bardziej złożoną sieć tranzytową z wieloma przeskokami, która spełnia Twoje potrzeby.

### <a name="prerequisites"></a>Wymagania wstępne

Sprawdź, czy masz subskrypcję platformy Azure. Jeśli nie masz jeszcze subskrypcji platformy Azure, możesz aktywować [korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) lub utworzyć [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/).

## <a name="part-1-configure-bgp-on-the-virtual-network-gateway"></a><a name ="config"></a>Część 1: Konfigurowanie protokołu BGP na bramie sieci wirtualnej

W tej sekcji utworzysz i skonfigurujesz sieć wirtualną, tworzysz i konfigurujesz bramę sieci wirtualnej z parametrami protokołu BGP i uzyskujesz adres IP elementu równorzędnego BGP platformy Azure. Diagram 2 przedstawia ustawienia konfiguracji, które mają być używane podczas pracy z krokami opisanymi w tej sekcji.

**Diagram 2**

:::image type="content" source="./media/bgp-howto/bgp-gateway.png" alt-text="Diagram przedstawiający architekturę i ustawienia sieci" border="false":::

### <a name="1-create-and-configure-testvnet1"></a>1. Utwórz i skonfiguruj sieci testvnet1

W tym kroku utworzysz i skonfigurujesz sieci testvnet1. Wykonaj kroki opisane w [samouczku tworzenie bramy](vpn-gateway-tutorial-create-gateway-powershell.md) , aby utworzyć i skonfigurować usługę Azure Virtual Network i bramę sieci VPN. Użyj ustawień referencyjnych na zrzutach ekranu poniżej.

* Sieć wirtualna:

   :::image type="content" source="./media/bgp-howto/testvnet-1.png" alt-text="Diagram przedstawiający architekturę i ustawienia sieci":::

* Podsieci:

   :::image type="content" source="./media/bgp-howto/testvnet-1-subnets.png" alt-text="Diagram przedstawiający architekturę i ustawienia sieci":::

### <a name="2-create-the-vpn-gateway-for-testvnet1-with-bgp-parameters"></a>2. Utwórz bramę sieci VPN dla usługi sieci testvnet1 przy użyciu parametrów protokołu BGP

W tym kroku utworzysz bramę sieci VPN z odpowiednimi parametrami protokołu BGP.

1. W Azure Portal przejdź do zasobu **bramy Virtual Network** z portalu Marketplace, a następnie wybierz pozycję **Utwórz**.

1. Wypełnij parametry, jak pokazano poniżej:

   :::image type="content" source="./media/bgp-howto/create-gateway-1.png" alt-text="Diagram przedstawiający architekturę i ustawienia sieci":::

1. W wyróżnionej sekcji **Skonfiguruj protokół BGP** skonfiguruj następujące ustawienia:

   :::image type="content" source="./media/bgp-howto/create-gateway-1-bgp.png" alt-text="Diagram przedstawiający architekturę i ustawienia sieci":::

   * Wybierz pozycję **Konfiguruj protokół BGP**  -  **włączony** , aby wyświetlić sekcję Konfiguracja protokołu BGP.

   * Wypełnij pole ASN (numer systemu autonomicznego).

   * Pole **adres IP protokołu BGP funkcji APIPA platformy Azure** jest opcjonalne. Jeśli lokalne urządzenia sieci VPN używają adresu APIPA dla protokołu BGP, należy wybrać adres z zakresu adresów APIPA zarezerwowanych na platformie Azure dla sieci VPN, który jest z **169.254.21.0** do **169.254.22.255**. W tym przykładzie używa 169.254.21.11.

   * Jeśli tworzysz bramę sieci VPN w usłudze Active-Active, w sekcji BGP zostanie wyświetlony dodatkowy **adres IP protokołu BGP usługi Azure APIPA**. Określ inny adres z dozwolonego zakresu APIPA (**169.254.21.0** do **169.254.22.255**).

   > [!IMPORTANT]
   >
   > * Domyślnie platforma Azure przypisuje prywatny adres IP z zakresu prefiksów GatewaySubnet automatycznie jako adres IP protokołu BGP platformy Azure w bramie sieci VPN platformy Azure. Niestandardowy adres BGP platformy Azure jest wymagany, gdy lokalne urządzenia sieci VPN używają adresu APIPA (od 169.254.0.1 do 169.254.255.254) jako adresu IP protokołu BGP. Usługa Azure VPN Gateway wybierze niestandardowy adres APIPA, jeśli odpowiedni zasób bramy sieci lokalnej (sieć lokalna) ma adres APIPA jako element równorzędny protokołu BGP. Jeśli Brama sieci lokalnej używa zwykłego adresu IP (nie APIPA), usługa Azure VPN Gateway powróci do prywatnego adresu IP z zakresu GatewaySubnet.
   >
   > * Adresy APIPA BGP nie mogą nakładać się na lokalne urządzenia sieci VPN i wszystkie połączone bramy sieci VPN platformy Azure.
   >

1. Wybierz pozycję **Przegląd + Utwórz** , aby uruchomić walidację. Po zakończeniu walidacji wybierz pozycję **Utwórz** , aby wdrożyć bramę sieci VPN. Aby w pełni utworzyć i wdrożyć bramę, może upłynąć do 45 minut. Stan wdrożenia można sprawdzić na stronie Przegląd dla bramy.

### <a name="3-obtain-the-azure-bgp-peer-ip-addresses"></a>3. Uzyskaj adresy IP elementów równorzędnych BGP platformy Azure

Po utworzeniu bramy można uzyskać adresy IP elementów równorzędnych BGP w bramie sieci VPN platformy Azure. Te adresy są konieczne do konfigurowania lokalnych urządzeń sieci VPN w celu ustanowienia sesji protokołu BGP z bramą sieci VPN platformy Azure.

1. Przejdź do zasobu bramy sieci wirtualnej i wybierz stronę **Konfiguracja** , aby wyświetlić informacje o konfiguracji protokołu BGP, jak pokazano na poniższym zrzucie ekranu. Na tej stronie można wyświetlić wszystkie informacje o konfiguracji protokołu BGP bramy sieci VPN platformy Azure: ASN, publiczny adres IP i odpowiadające im adresy IP elementów równorzędnych BGP po stronie platformy Azure (domyślne i APIPA).

   :::image type="content" source="./media/bgp-howto/vnet-1-gw-bgp.png" alt-text="Diagram przedstawiający architekturę i ustawienia sieci":::

1. Na stronie **Konfiguracja** można wprowadzić następujące zmiany w konfiguracji:

   * Jeśli to konieczne, można zaktualizować numer ASN lub adres IP protokołu BGP (APIPA).
   * Jeśli masz aktywną aktywną bramę sieci VPN, na tej stronie zostanie wyświetlony publiczny adres IP, domyślne i adresy IP protokołu BGP dla drugiego wystąpienia bramy sieci VPN platformy Azure.

1. Jeśli wprowadzono jakiekolwiek zmiany, wybierz pozycję **Zapisz** , aby zatwierdzić zmiany w bramie sieci VPN platformy Azure.

## <a name="part-2-configure-bgp-on-cross-premises-s2s-connections"></a><a name ="crosspremises"></a>Część 2: Konfigurowanie protokołu BGP dla wielu lokalizacji połączeń S2S

Aby nawiązać połączenie między różnymi lokalizacjami, należy utworzyć *bramę sieci lokalnej* do reprezentowania lokalnego urządzenia sieci VPN oraz *połączenie* z bramą sieci VPN z bramą sieci lokalnej zgodnie z opisem w temacie [Tworzenie połączenia lokacja-lokacja](vpn-gateway-howto-site-to-site-resource-manager-portal.md). Ten artykuł zawiera dodatkowe właściwości wymagane do określenia parametrów konfiguracji protokołu BGP.

**Diagram 3**

:::image type="content" source="./media/bgp-howto/bgp-crosspremises.png" alt-text="Diagram przedstawiający architekturę i ustawienia sieci" border="false":::

### <a name="1-configure-bgp-on-the-local-network-gateway"></a>1. Skonfiguruj protokół BGP na bramie sieci lokalnej

W tym kroku skonfigurujesz protokół BGP na bramie sieci lokalnej. Na przykład użyj poniższego zrzutu ekranu. Zrzut ekranu przedstawia bramę sieci lokalnej (site5) z parametrami określonymi w diagramie 3.

:::image type="content" source="./media/bgp-howto/create-local-bgp.png" alt-text="Diagram przedstawiający architekturę i ustawienia sieci":::

#### <a name="important-configuration-considerations"></a>Ważne zagadnienia dotyczące konfiguracji

* Numer ASN i adres IP elementu równorzędnego BGP muszą być zgodne z konfiguracją lokalnego routera sieci VPN.
* **Przestrzeń adresową** można pozostawić pustą, tylko jeśli używasz protokołu BGP do łączenia się z tą siecią. Usługa Azure VPN Gateway będzie wewnętrznie dodawać trasę adresu IP elementu równorzędnego BGP do odpowiedniego tunelu IPsec. Jeśli **nie** korzystasz z protokołu BGP między bramą sieci VPN platformy Azure i tą konkretną siecią, **musisz** podać listę prawidłowych prefiksów adresów dla **przestrzeni adresowej**.
* Opcjonalnie można użyć **adresu IP APIPA** (169.254. x. x) jako lokalnego adresu IP elementu równorzędnego protokołu BGP, jeśli jest to konieczne. Ale należy również określić adres IP APIPA, jak opisano wcześniej w tym artykule dla bramy sieci VPN platformy Azure, w przeciwnym razie nie można ustanowić sesji BGP dla tego połączenia.
* Informacje o konfiguracji protokołu BGP można wprowadzić podczas tworzenia bramy sieci lokalnej lub dodać lub zmienić konfigurację protokołu BGP na stronie **Konfiguracja** zasobu bramy sieci lokalnej.

**Przykład**

W tym przykładzie używa adresu APIPA (169.254.100.1) jako lokalnego adresu IP elementu równorzędnego BGP:

:::image type="content" source="./media/bgp-howto/local-apipa.png" alt-text="Diagram przedstawiający architekturę i ustawienia sieci":::

### <a name="2-configure-a-s2s-connection-with-bgp-enabled"></a>2. Skonfiguruj połączenie S2S z włączonym protokołem BGP

W tym kroku utworzysz nowe połączenie z włączonym protokołem BGP. Jeśli masz już połączenie i chcesz włączyć dla niego protokół BGP, możesz [zaktualizować istniejące połączenie](#update).

#### <a name="to-create-a-connection-with-bgp-enabled"></a>Aby utworzyć połączenie z włączonym protokołem BGP

Aby utworzyć nowe połączenie z włączonym protokołem BGP, na stronie **Dodawanie połączenia** Wypełnij wartości, a następnie zaznacz pole wyboru **Włącz protokół BGP** , aby włączyć protokół BGP dla tego połączenia. Kliknij przycisk **OK**, aby utworzyć połączenie.

:::image type="content" source="./media/bgp-howto/ipsec-connection-bgp.png" alt-text="Diagram przedstawiający architekturę i ustawienia sieci":::

#### <a name="to-update-an-existing-connection"></a><a name ="update"></a>Aby zaktualizować istniejące połączenie

Jeśli chcesz zmienić opcję protokołu BGP dla połączenia, przejdź do strony **Konfiguracja** zasobu połączenia, a następnie Przełącz opcję protokołu **BGP** jako wyróżnioną w poniższym przykładzie. Wybierz pozycję **Zapisz** , aby zapisać zmiany.

:::image type="content" source="./media/bgp-howto/update-bgp.png" alt-text="Diagram przedstawiający architekturę i ustawienia sieci":::

## <a name="part-3-configure-bgp-on-vnet-to-vnet-connections"></a><a name ="v2v"></a>Część 3: Konfigurowanie połączeń BGP w połączeniach między sieciami wirtualnymi

Instrukcje dotyczące włączania lub wyłączania protokołu BGP na potrzeby połączenia między sieciami wirtualnymi są takie same jak w przypadku kroków S2S w [części 2](#crosspremises). Można włączyć protokół BGP podczas tworzenia połączenia lub zaktualizować konfigurację w istniejącym połączeniu między sieciami wirtualnymi.

>[!NOTE] 
>Połączenie między sieciami wirtualnymi bez protokołu BGP ograniczy komunikację tylko do dwóch połączonych sieci wirtualnych. Włącz protokół BGP, aby zezwalać na Routing tranzytowy z innymi połączeniami S2S lub VNet-to-VNet tych dwóch sieci wirtualnych.
>

W przypadku kontekstu odnoszącego się do **diagramu 4**, jeśli protokół BGP miał zostać wyłączony między TestVNet2 i sieci testvnet1, TestVNet2 nie nauczy się tras dla sieci lokalnej, site5 i dlatego nie może komunikować się z lokacją 5. Po włączeniu protokołu BGP, jak pokazano na diagramie 4, wszystkie trzy sieci będą mogły komunikować się z połączeniami IPsec i sieciami wirtualnymi.

**Diagram 4**

:::image type="content" source="./media/bgp-howto/bgp-crosspremises-v2v.png" alt-text="Diagram przedstawiający architekturę i ustawienia sieci" border="false":::

## <a name="next-steps"></a>Następne kroki

Po zakończeniu procesu nawiązywania połączenia można dodać do sieci wirtualnych maszyny wirtualne. Kroki opisano w sekcji [Tworzenie maszyny wirtualnej](../virtual-machines/windows/quick-create-portal.md).
