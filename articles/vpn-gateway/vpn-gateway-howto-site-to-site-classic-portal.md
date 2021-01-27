---
title: 'Łączenie sieci lokalnej z siecią wirtualną platformy Azure: sieć VPN typu lokacja-lokacja (model klasyczny): portal | Microsoft Docs'
description: Tworzenie połączenia IPsec z sieci lokalnej do sieci wirtualnej klasycznej platformy Azure za pośrednictwem publicznego Internetu.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/08/2020
ms.author: cherylmc
ms.openlocfilehash: 4ad05281f13885327c855a261a3101388f38af83
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98878057"
---
# <a name="create-a-site-to-site-connection-using-the-azure-portal-classic"></a>Tworzenie sieci wirtualnej za pomocą połączenia typu lokacja-lokacja przy użyciu witryny Azure Portal (model klasyczny)

Ten artykuł pokazuje, jak używać witryny Azure Portal do tworzenia połączenia bramy sieci VPN lokacja-lokacja z sieci lokalnej do sieci wirtualnej. Kroki opisane w tym artykule mają zastosowanie do klasycznego modelu wdrażania i nie mają zastosowania do bieżącego modelu wdrażania Menedżer zasobów. Tę konfigurację możesz również utworzyć przy użyciu innego narzędzia wdrażania lub modelu wdrażania, wybierając inną opcję z następującej listy:

> [!div class="op_single_selector"]
> * [Witryna Azure Portal](./tutorial-site-to-site-portal.md)
> * [Program PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
> * [Interfejs wiersza polecenia](vpn-gateway-howto-site-to-site-resource-manager-cli.md)
> * [Portal Azure (klasyczny)](vpn-gateway-howto-site-to-site-classic-portal.md)
> 

Połączenie bramy sieci VPN typu lokacja-lokacja umożliwia łączenie sieci lokalnej z siecią wirtualną platformy Azure za pośrednictwem tunelu sieci VPN IPsec/IKE (IKEv1 lub IKEv2). Ten typ połączenia wymaga lokalnego urządzenia sieci VPN z przypisanym publicznym adresem IP dostępnym z zewnątrz. Więcej informacji o bramach sieci VPN można znaleźć w artykule [Informacje dotyczące bram sieci VPN](vpn-gateway-about-vpngateways.md).

![Diagram połączenia bramy VPN Gateway typu lokacja-lokacja obejmującego wiele lokalizacji](./media/vpn-gateway-howto-site-to-site-classic-portal/site-to-site-diagram.png)

## <a name="before-you-begin"></a><a name="before"></a>Zanim rozpoczniesz

Przed rozpoczęciem konfiguracji sprawdź, czy są spełnione następujące kryteria:

* Upewnij się, że chcesz pracować w klasycznym modelu wdrażania. Jeśli chcesz pracować w modelu wdrażania usługi Resource Manager, zobacz [Tworzenie połączenia typu lokacja-lokacja (Resource Manager)](./tutorial-site-to-site-portal.md). Zalecamy użycie modelu wdrażania Menedżer zasobów, ponieważ model klasyczny jest starszy.
* Upewnij się, że masz zgodne urządzenie sieci VPN i dostępna jest osoba, która umie je skonfigurować. Aby uzyskać więcej informacji o zgodnych urządzeniach sieci VPN i konfiguracji urządzeń, zobacz artykuł [Informacje o urządzeniach sieci VPN](vpn-gateway-about-vpn-devices.md).
* Sprawdź, czy masz dostępny zewnętrznie publiczny adres IPv4 urządzenia sieci VPN.
* Jeśli nie znasz zakresów adresów IP w konfiguracji swojej sieci lokalnej, skontaktuj się z osobą, która może podać Ci te dane. Tworząc tę konfigurację, musisz określić prefiksy zakresu adresów IP, które platforma Azure będzie kierować do Twojej lokalizacji lokalnej. Żadna z podsieci sieci lokalnej nie może się nakładać na podsieci sieci wirtualnej, z którymi chcesz nawiązać połączenie.
* Program PowerShell jest wymagany, aby określić klucz współużytkowany i utworzyć połączenie bramy sieci VPN. [!INCLUDE [vpn-gateway-classic-powershell](../../includes/vpn-gateway-powershell-classic-locally.md)]

### <a name="sample-configuration-values-for-this-exercise"></a><a name="values"></a>Przykładowe wartości konfiguracji dla tego ćwiczenia

W przykładach w tym artykule są stosowane następujące wartości. Tych wartości możesz użyć do tworzenia środowiska testowego lub odwoływać się do nich, aby lepiej zrozumieć przykłady w niniejszym artykule. Zwykle podczas pracy z wartościami adresów IP dla przestrzeni adresowej, chcesz skoordynować się z administratorem sieci, aby uniknąć nakładających się przestrzeni adresowych, co może wpłynąć na Routing. W takim przypadku należy zastąpić wartości adresów IP własnymi, jeśli chcesz utworzyć połączenie robocze.

* **Grupa zasobów:** TestRG1
* **Nazwa sieci wirtualnej:** Sieci testvnet1
* **Przestrzeń adresowa:** 10.11.0.0/16
* **Nazwa podsieci:** Frontonu
* **Zakres adresów podsieci:** 10.11.0.0/24
* **GatewaySubnet:** 10.11.255.0/27
* **Region:** (US) Wschodnie stany USA
* **Nazwa lokacji lokalnej:** Lokacja2
* **Przestrzeń adresowa klienta:** przestrzeń adresowa znajdująca się w lokacji lokalnej.

## <a name="create-a-virtual-network"></a><a name="CreatVNet"></a>Tworzenie sieci wirtualnej

Podczas tworzenia sieci wirtualnej na potrzeby połączenia typu lokacja-lokacja upewnij się, że określone przestrzenie adresowe nie nakładają się na żadne przestrzenie adresowe klientów dla lokacji lokalnych, z którymi chcesz nawiązywać połączenia. Obecność nakładających się podsieci spowoduje, że połączenie nie będzie działać prawidłowo.

* Jeśli masz już sieć wirtualną, sprawdź, czy ustawienia są zgodne z projektem bramy sieci VPN. Zwróć szczególną uwagę na wszelkie podsieci, które mogą pokrywać się z innymi sieciami.

* Jeśli nie masz jeszcze sieci wirtualnej, utwórz ją. Zamieszczone zrzuty ekranu są przykładowe. Przedstawione na nich wartości należy zastąpić własnymi.

### <a name="to-create-a-virtual-network"></a>Aby utworzyć sieć wirtualną

[!INCLUDE [basic classic vnet](../../includes/vpn-gateway-vnet-classic.md)]

[!INCLUDE [basic classic DNS](../../includes/vpn-gateway-dns-classic.md)]

## <a name="configure-the-site-and-gateway"></a><a name="localsite"></a>Skonfiguruj lokację i bramę

### <a name="to-configure-the-site"></a>Aby skonfigurować lokację

Lokacja lokalna zazwyczaj oznacza lokalizację lokalną. Zawiera ona adres IP urządzenia sieci VPN, z którym będzie tworzone połączenie, oraz zakresy adresów IP, które będą kierowane za pośrednictwem bramy sieci VPN do tego urządzenia sieci VPN.

1. Na stronie sieci wirtualnej w obszarze **Ustawienia** wybierz pozycję **połączenia lokacja-lokacja**.
1. Na stronie połączenia między lokacjami wybierz pozycję **+ Dodaj**.
1. Na stronie **Konfigurowanie połączenia sieci VPN i bramy** dla **typu połączenia** pozostaw wybraną opcję **lokacja-lokacja** . W tym ćwiczeniu konieczne będzie użycie kombinacji [przykładowych wartości](#values) i własnych wartości.

   * **Adres IP bramy sieci VPN:** Publiczny adres IP urządzenia sieci VPN w sieci lokalnej. Urządzenie sieci VPN wymaga publicznego adresu IPv4. Określ prawidłowy publiczny adres IP dla urządzenia sieci VPN, z którym chcesz się połączyć. Musi być dostępna dla systemu Azure. Jeśli nie znasz adresu IP urządzenia sieci VPN, zawsze możesz podać wartość zastępczą (o ile jest w formacie prawidłowego publicznego adresu IP) i zmienić ją później.

   * **Przestrzeń adresowa klienta:** Podaj listę zakresów adresów IP, które mają być kierowane do sieci lokalnej za pośrednictwem tej bramy. Można dodać wiele zakresów przestrzeni adresów. Upewnij się, że określone w tym miejscu zakresy nie pokrywają się z zakresami innych sieci, z którymi łączy się Twoja sieć wirtualna, ani z zakresami adresów samej sieci wirtualnej.
1. W dolnej części strony nie wybieraj opcji Recenzja + Utwórz. Zamiast tego wybierz pozycję **Dalej: brama>**.

### <a name="to-configure-the-virtual-network-gateway"></a><a name="sku"></a>Aby skonfigurować bramę sieci wirtualnej

1. Na stronie **brama** wybierz następujące wartości:

   * **Rozmiar:** Jest to jednostka SKU bramy używana do tworzenia bramy sieci wirtualnej. Klasyczne bramy sieci VPN używają starych (starszych) jednostek SKU bramy. Aby uzyskać więcej informacji o starszych jednostkach SKU bramy, zobacz [Working with virtual network gateway SKUs (old SKUs) (Praca z jednostkami SKU [starymi jednostkami SKU] bramy sieci wirtualnej)](vpn-gateway-about-skus-legacy.md). Możesz wybrać opcję **standardowa** dla tego ćwiczenia.

   * **Typ routingu:** Wybierz typ routingu dla bramy. Jest on również nazywany typem sieci VPN. Ważne jest, aby wybrać właściwy typ, ponieważ nie można skonwertować bramy z jednego typu na inny. Urządzenie sieci VPN musi być zgodne z wybranym typem routingu. Aby uzyskać więcej informacji na temat typu routingu, zobacz [Informacje o ustawieniach VPN Gateway](vpn-gateway-about-vpn-gateway-settings.md#vpntype). W niektórych artykułach mogą znajdować się odwołania do typów sieci VPN „RouteBased” i „PolicyBased”. Typ „Dynamiczny” odpowiada typowi „RouteBased”, a „Statyczny” — typowi „PolicyBased”. Zazwyczaj chcesz mieć dynamiczny Routing.

   * **Podsieć bramy:** Określony rozmiar podsieci bramy zależy od konfiguracji bramy sieci VPN, która ma zostać utworzona. Jest możliwe utworzenie małej podsieci bramy (/29), jednak zalecamy użycie rozmiaru /27 lub /28. Spowoduje to utworzenie większej podsieci obejmującej więcej adresów. Zastosowanie większej podsieci bramy daje wystarczającą liczbę adresów IP, aby uwzględnić możliwe przyszłe konfiguracje.

1. Wybierz pozycję **Przegląd + Utwórz** u dołu strony, aby sprawdzić poprawność ustawień. Wybierz pozycję **Utwórz** do wdrożenia. Utworzenie bramy sieci wirtualnej może potrwać do 45 minut, w zależności od wybranej jednostki SKU bramy.

## <a name="configure-your-vpn-device"></a><a name="vpndevice"></a>Konfiguracja urządzenia sieci VPN

Połączenia typu lokacja-lokacja z siecią lokalną wymagają urządzenia sieci VPN. W tym kroku konfigurowane jest urządzenie sieci VPN. Podczas konfigurowania urządzenia sieci VPN potrzebne są następujące wartości:

* Klucz współużytkowany. To ten sam klucz współużytkowany, który jest określany podczas tworzenia połączenia sieci VPN typu lokacja-lokacja. W naszych przykładach używamy podstawowego klucza współużytkowanego. Zalecamy, aby do użycia wygenerować bardziej złożony klucz.
* Publiczny adres IP bramy sieci wirtualnej. Publiczny adres IP można wyświetlić za pomocą witryny Azure Portal, programu PowerShell lub interfejsu wiersza polecenia.

[!INCLUDE [vpn-gateway-configure-vpn-device-rm](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]

## <a name="retrieve-values"></a><a name="getvalues"></a>Pobierz wartości

[!INCLUDE [retrieve values](../../includes/vpn-gateway-values-classic.md)]

## <a name="create-the-connection"></a><a name="CreateConnection"></a>Tworzenie połączenia

> [!NOTE]
> W przypadku klasycznego modelu wdrażania ten krok nie jest dostępny w Azure Portal lub za pośrednictwem Azure Cloud Shell. Musisz użyć wersji usługi Service Management (SM) Azure PowerShell poleceń cmdlet lokalnie na pulpicie.
>

W tym kroku, korzystając z wartości z poprzednich kroków, należy ustawić klucz współużytkowany i utworzyć połączenie. Ustawiony klucz musi być tym samym kluczem, którego użyto do skonfigurowania urządzenia sieci VPN.

1. Ustaw klucz współużytkowany i utwórz połączenie.

   * Zmień wartość parametru-VNetName i-LocalNetworkSiteName. W przypadku podawania nazwy, która zawiera spacje, umieść wartość w apostrofach.
   * Wartość "-SharedKey" jest generowana, a następnie określ. W przykładzie użyto elementu "abc123", ale można wygenerować coś bardziej złożonego (i powinien). Pamiętaj o tym, że wartość podana w tym miejscu musi być taka sama, jak wartość podana podczas konfigurowania urządzenia sieci VPN.

   ```powershell
   Set-AzureVNetGatewayKey -VNetName 'Group TestRG1 TestVNet1' `
   -LocalNetworkSiteName '6C74F6E6_Site2' -SharedKey abc123
   ```

1. Wynik po utworzeniu połączenia: **Stan: Powodzenie**.

## <a name="verify-your-connection"></a><a name="verify"></a>Weryfikowanie połączenia

[!INCLUDE [vpn-gateway-verify-connection-azureportal-classic](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]

Jeśli występują problemy z połączeniem, zobacz sekcję **Rozwiązywanie problemów** spisu treści w okienku po lewej stronie.

## <a name="how-to-reset-a-vpn-gateway"></a><a name="reset"></a>Jak zresetować bramę VPN Gateway

Resetowanie bramy Azure VPN Gateway przydaje się w przypadku utraty połączenia sieci VPN obejmującego wiele lokalizacji w jednym lub wielu tunelach VPN typu lokacja-lokacja. W takiej sytuacji urządzenia lokalnej sieci VPN działają prawidłowo, ale nie mogą nawiązać połączenia w ramach tuneli używających protokołu IPsec z bramami sieci VPN Azure. Aby uzyskać instrukcje, zobacz [Resetowanie bramy VPN Gateway](./reset-gateway.md#resetclassic).

## <a name="how-to-change-a-gateway-sku"></a><a name="changesku"></a>Jak zmienić jednostkę SKU bramy

Aby uzyskać instrukcje dotyczące zmiany jednostki SKU bramy, zobacz [zmiana rozmiaru jednostki SKU bramy](vpn-gateway-about-SKUS-legacy.md#classicresize).

## <a name="next-steps"></a>Następne kroki

* Po zakończeniu procesu nawiązywania połączenia można dodać do sieci wirtualnych maszyny wirtualne. Aby uzyskać więcej informacji, zobacz [Virtual Machines](../index.yml) (Maszyny wirtualne).
* Aby uzyskać informacje o wymuszonym tunelowaniu, zobacz [Informacje o wymuszonym tunelowaniu](vpn-gateway-about-forced-tunneling.md).