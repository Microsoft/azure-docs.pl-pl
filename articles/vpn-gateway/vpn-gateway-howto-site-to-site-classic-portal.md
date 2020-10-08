---
title: 'Łączenie sieci lokalnej z siecią wirtualną platformy Azure: sieć VPN typu lokacja-lokacja (model klasyczny): portal | Microsoft Docs'
description: Tworzenie połączenia IPsec z sieci lokalnej do sieci wirtualnej klasycznej platformy Azure za pośrednictwem publicznego Internetu.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 02/11/2020
ms.author: cherylmc
ms.openlocfilehash: 002aa9da465d86392aaaa5d404f67959b341ecf9
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/07/2020
ms.locfileid: "91818986"
---
# <a name="create-a-site-to-site-connection-using-the-azure-portal-classic"></a>Tworzenie sieci wirtualnej za pomocą połączenia typu lokacja-lokacja przy użyciu witryny Azure Portal (model klasyczny)


Ten artykuł pokazuje, jak używać witryny Azure Portal do tworzenia połączenia bramy sieci VPN lokacja-lokacja z sieci lokalnej do sieci wirtualnej. Kroki opisane w tym artykule mają zastosowanie do klasycznego modelu wdrażania i nie mają zastosowania do bieżącego modelu wdrażania Menedżer zasobów. Tę konfigurację możesz również utworzyć przy użyciu innego narzędzia wdrażania lub modelu wdrażania, wybierając inną opcję z następującej listy:

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
> * [Program PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
> * [Interfejs wiersza polecenia](vpn-gateway-howto-site-to-site-resource-manager-cli.md)
> * [Portal Azure (klasyczny)](vpn-gateway-howto-site-to-site-classic-portal.md)
> 
>

Połączenie bramy sieci VPN typu lokacja-lokacja umożliwia łączenie sieci lokalnej z siecią wirtualną platformy Azure za pośrednictwem tunelu sieci VPN IPsec/IKE (IKEv1 lub IKEv2). Ten typ połączenia wymaga lokalnego urządzenia sieci VPN z przypisanym publicznym adresem IP dostępnym z zewnątrz. Więcej informacji o bramach sieci VPN można znaleźć w artykule [Informacje dotyczące bram sieci VPN](vpn-gateway-about-vpngateways.md).

![Diagram połączenia bramy VPN Gateway typu lokacja-lokacja obejmującego wiele lokalizacji](./media/vpn-gateway-howto-site-to-site-classic-portal/site-to-site-diagram.png)

## <a name="before-you-begin"></a><a name="before"></a>Przed rozpoczęciem

Przed rozpoczęciem konfiguracji sprawdź, czy są spełnione następujące kryteria:

* Upewnij się, że chcesz pracować w klasycznym modelu wdrażania. Jeśli chcesz pracować w modelu wdrażania usługi Resource Manager, zobacz [Tworzenie połączenia typu lokacja-lokacja (Resource Manager)](vpn-gateway-howto-site-to-site-resource-manager-portal.md). Zawsze, jeśli jest to możliwe, zalecamy użycie modelu wdrażania przy użyciu usługi Resource Manager.
* Upewnij się, że masz zgodne urządzenie sieci VPN i dostępna jest osoba, która umie je skonfigurować. Aby uzyskać więcej informacji o zgodnych urządzeniach sieci VPN i konfiguracji urządzeń, zobacz artykuł [Informacje o urządzeniach sieci VPN](vpn-gateway-about-vpn-devices.md).
* Sprawdź, czy masz dostępny zewnętrznie publiczny adres IPv4 urządzenia sieci VPN.
* Jeśli nie znasz zakresów adresów IP w konfiguracji swojej sieci lokalnej, skontaktuj się z osobą, która może podać Ci te dane. Tworząc tę konfigurację, musisz określić prefiksy zakresu adresów IP, które platforma Azure będzie kierować do Twojej lokalizacji lokalnej. Żadna z podsieci sieci lokalnej nie może się nakładać na podsieci sieci wirtualnej, z którymi chcesz nawiązać połączenie.
* Program PowerShell jest wymagany, aby określić klucz współużytkowany i utworzyć połączenie bramy sieci VPN. [!INCLUDE [vpn-gateway-classic-powershell](../../includes/vpn-gateway-powershell-classic-locally.md)]

### <a name="sample-configuration-values-for-this-exercise"></a><a name="values"></a>Przykładowe wartości konfiguracji dla tego ćwiczenia

W przykładach w tym artykule są stosowane następujące wartości. Tych wartości możesz użyć do tworzenia środowiska testowego lub odwoływać się do nich, aby lepiej zrozumieć przykłady w niniejszym artykule.

* **Nazwa sieci wirtualnej:** Sieci testvnet1
* **Przestrzeń adresowa:** 
  * 10.11.0.0/16
  * 10.12.0.0/16 (opcjonalnie na potrzeby tego ćwiczenia)
* **Podsieci**
  * FrontEnd: 10.11.0.0/24
  * BackEnd: 10.12.0.0/24 (opcjonalnie na potrzeby tego ćwiczenia)
* **GatewaySubnet:** 10.11.255.0/27
* **Grupa zasobów:** TestRG1
* **Lokalizacja:** Wschodnie stany USA
* **Serwer DNS:** 10.11.0.3 (opcjonalnie na potrzeby tego ćwiczenia)
* **Nazwa lokacji lokalnej:** Lokacja2
* **Przestrzeń adresowa klienta:** przestrzeń adresowa znajdująca się w lokacji lokalnej.

## <a name="1-create-a-virtual-network"></a><a name="CreatVNet"></a>1. Tworzenie sieci wirtualnej

Podczas tworzenia sieci wirtualnej na potrzeby połączenia typu lokacja-lokacja upewnij się, że określone przestrzenie adresowe nie nakładają się na żadne przestrzenie adresowe klientów dla lokacji lokalnych, z którymi chcesz nawiązywać połączenia. Obecność nakładających się podsieci spowoduje, że połączenie nie będzie działać prawidłowo.

* Jeśli masz już sieć wirtualną, sprawdź, czy ustawienia są zgodne z projektem bramy sieci VPN. Zwróć szczególną uwagę na wszelkie podsieci, które mogą pokrywać się z innymi sieciami. 

* Jeśli nie masz jeszcze sieci wirtualnej, utwórz ją. Zamieszczone zrzuty ekranu są przykładowe. Przedstawione na nich wartości należy zastąpić własnymi.

### <a name="to-create-a-virtual-network"></a>Aby utworzyć sieć wirtualną

1. W przeglądarce przejdź do witryny [Azure Portal](https://portal.azure.com) i, jeśli to konieczne, zaloguj się przy użyciu konta platformy Azure.
2. Kliknij pozycję **+ Utwórz zasób*. W polu **Wyszukaj w witrynie Marketplace** wpisz "Virtual Network". Znajdź pozycję **Sieć wirtualna** na liście wyników i kliknij, aby otworzyć stronę **Sieć wirtualna**.
3. Kliknij przycisk **(Zmień na klasyczny)**, a następnie kliknij przycisk **Utwórz**.
4. Na stronie **Utwórz sieć wirtualną (klasyczną)** skonfiguruj ustawienia sieci wirtualnej. Na tej stronie dodaj pierwszą przestrzeń adresową i zakres adresów pojedynczej podsieci. Po utworzeniu sieci wirtualnej można wrócić i dodać dodatkowe podsieci oraz przestrzenie adresowe.

   ![Strona Tworzenie sieci wirtualnej](./media/vpn-gateway-howto-site-to-site-classic-portal/createvnet.png "Strona Tworzenie sieci wirtualnej")
5. Sprawdź, czy pole **Subskrypcja** zawiera prawidłową wartość. Subskrypcje można zmieniać, korzystając z listy rozwijanej.
6. Kliknij przycisk **Grupa zasobów** i wybierz istniejącą grupę zasobów lub utwórz nową, wpisując nazwę. Aby uzyskać więcej informacji na temat grup zasobów, zobacz [Omówienie usługi Azure Resource Manager](../azure-resource-manager/management/overview.md#resource-groups).
7. Następnie w polu **Lokalizacja** wybierz ustawienia sieci wirtualnej. Lokalizacja określa miejsce, w którym zostaną umieszczone zasoby wdrażane w tej sieci wirtualnej.
8. Kliknij przycisk **Utwórz**, aby utworzyć sieć wirtualną.
9. Po kliknięciu przycisku „Utwórz” na pulpicie nawigacyjnym zostanie umieszczony kafelek, który będzie odzwierciedlać postęp Twojej sieci wirtualnej. Wygląd kafelka zmienia się w trakcie tworzenia sieci wirtualnej.

## <a name="2-add-additional-address-space"></a><a name="additionaladdress"></a>2. Dodaj dodatkową przestrzeń adresową

Po utworzeniu sieci wirtualnej możesz dodać kolejną przestrzeń adresową. Dodanie kolejnej przestrzeni adresowej nie jest wymagane w przypadku konfiguracji typu lokacja-lokacja, ale jeśli potrzebujesz wielu przestrzeni adresowych, wykonaj następujące kroki:

1. Zlokalizuj sieć wirtualną w portalu.
2. Na stronie Twojej sieci wirtualnej w sekcji **Ustawienia** kliknij pozycję **Przestrzeń adresowa**.
3. Na stronie Przestrzeń adresowa kliknij pozycję **+Dodaj** i wprowadź dodatkową przestrzeń adresową.

## <a name="3-specify-a-dns-server"></a><a name="dns"></a>3. Określ serwer DNS.

Ustawienia DNS nie są wymagane w przypadku konfiguracji typu lokacja-lokacja, ale serwer DNS jest konieczny, aby korzystać z rozpoznawania nazw. Określenie wartości nie powoduje utworzenia nowego serwera DNS. Określony adres IP serwera DNS powinien być adresem serwera będącego w stanie rozpoznawać nazwy zasobów, z którymi nawiązywane jest połączenie. W ustawieniach przykładowych użyto prywatnego adresu IP. Użyty przez nas adres IP prawdopodobnie nie jest adresem IP Twojego serwera DNS. Pamiętaj, aby użyć własnych wartości.

Po utworzeniu sieci wirtualnej możesz dodać adres IP serwera DNS, aby umożliwić obsługę rozpoznawania nazw. Otwórz ustawienia dla sieci wirtualnej, kliknij pozycję Serwery DNS i dodaj adres IP serwera DNS, który ma być używany do rozpoznawania nazw.

1. Zlokalizuj sieć wirtualną w portalu.
2. Na stronie Twojej sieci wirtualnej w sekcji **Ustawienia** kliknij pozycję **Serwery DNS**.
3. Dodaj serwer DNS.
4. Aby zapisać ustawienia, kliknij przycisk **Zapisz** znajdujący się u góry strony.

## <a name="4-configure-the-local-site"></a><a name="localsite"></a>4. Skonfiguruj lokację lokalną

Lokacja lokalna zazwyczaj oznacza lokalizację lokalną. Zawiera ona adres IP urządzenia sieci VPN, z którym będzie tworzone połączenie, oraz zakresy adresów IP, które będą kierowane za pośrednictwem bramy sieci VPN do tego urządzenia sieci VPN.

1. Na stronie sieci wirtualnej w obszarze **monitorowanie**kliknij pozycję **Diagram**.
1. Na stronie **połączenia sieci VPN** kliknij pozycję **nie masz żadnych istniejących połączeń sieci VPN. Kliknij tutaj, aby**rozpocząć.
1. W obszarze **Typ połączenia**pozostaw wybraną opcję **lokacja-lokacja** .
4. Kliknij pozycję **Lokacja lokalna — Skonfiguruj wymagane ustawienia**, aby otworzyć stronę **Lokacja lokalna**. Skonfiguruj ustawienia, a następnie kliknij przycisk **OK**, aby je zapisać.
   - **Nazwa:** Utwórz nazwę lokacji lokalnej, aby ułatwić jej identyfikację.
   - **Adres IP bramy sieci VPN:** Publiczny adres IP urządzenia sieci VPN w sieci lokalnej. Urządzenie sieci VPN wymaga publicznego adresu IPv4. Określ prawidłowy publiczny adres IP dla urządzenia sieci VPN, z którym chcesz się połączyć. Musi być dostępna dla systemu Azure. Jeśli nie znasz adresu IP urządzenia sieci VPN, zawsze możesz podać wartość zastępczą (o ile jest w formacie prawidłowego publicznego adresu IP) i zmienić ją później.
   - **Przestrzeń adresowa klienta:** Podaj listę zakresów adresów IP, które mają być kierowane do sieci lokalnej za pośrednictwem tej bramy. Można dodać wiele zakresów przestrzeni adresów. Upewnij się, że określone w tym miejscu zakresy nie pokrywają się z zakresami innych sieci, z którymi łączy się Twoja sieć wirtualna, ani z zakresami adresów samej sieci wirtualnej.

   ![Zrzut ekranu przedstawiający okna "nowe połączenie sieci VPN i" lokacja lokalna ".](./media/vpn-gateway-howto-site-to-site-classic-portal/localnetworksite.png)

Kliknij przycisk **OK** , aby zamknąć stronę lokacja lokalna. **Nie klikaj przycisku OK, aby zamknąć nową stronę połączenia sieci VPN**.

## <a name="5-configure-the-gateway-subnet"></a><a name="gatewaysubnet"></a>5. Skonfiguruj podsieć bramy

Musisz utworzyć podsieć dla bramy sieci VPN. Podsieć bramy zawiera adresy IP, z których korzystają usługi bramy sieci VPN.


1. Na stronie **Nowe połączenie VPN** zaznacz pole wyboru **Utwórz bramę natychmiast**. Zostanie wyświetlona strona „Opcjonalna konfiguracja bramy”. Jeśli nie zaznaczysz pola wyboru, strona umożliwiająca skonfigurowanie podsieci bramy nie zostanie wyświetlona.

   ![Konfiguracja bramy — podsieć, rozmiar, typ routingu](./media/vpn-gateway-howto-site-to-site-classic-portal/optional.png "Konfiguracja bramy — podsieć, rozmiar, typ routingu")
2. Aby otworzyć stronę **Konfiguracja bramy**, kliknij pozycję **Opcjonalna konfiguracja bramy — Podsieć, rozmiar i typ routingu**.
3. Na stronie **Konfiguracja bramy** kliknij pozycję **Podsieć — Skonfiguruj wymagane ustawienia**, aby otworzyć stronę **Dodawanie podsieci**. Po zakończeniu konfigurowania tych ustawień kliknij przycisk **OK**.

   ![Konfiguracja bramy — podsieć bramy](./media/vpn-gateway-howto-site-to-site-classic-portal/subnetrequired.png "Konfiguracja bramy — podsieć bramy")
4. Na stronie **Dodawanie podsieci** dodaj podsieć bramy. Rozmiar określanej podsieci bramy zależy od konfiguracji bramy sieci VPN, którą chcesz utworzyć. Jest możliwe utworzenie małej podsieci bramy (/29), jednak zalecamy użycie rozmiaru /27 lub /28. Spowoduje to utworzenie większej podsieci obejmującej więcej adresów. Zastosowanie większej podsieci bramy daje wystarczającą liczbę adresów IP, aby uwzględnić możliwe przyszłe konfiguracje.

   ![Dodaj podsieć bramy](./media/vpn-gateway-howto-site-to-site-classic-portal/addgwsubnet.png "Dodaj podsieć bramy")

## <a name="6-specify-the-sku-and-vpn-type"></a><a name="sku"></a>6. Określ typ jednostki SKU i sieci VPN.

1. Wybierz **rozmiar** bramy. To jest jednostka SKU bramy używana do tworzenia bramy sieci wirtualnej. Klasyczne bramy sieci VPN używają starych (starszych) jednostek SKU bramy. Aby uzyskać więcej informacji o starszych jednostkach SKU bramy, zobacz [Working with virtual network gateway SKUs (old SKUs) (Praca z jednostkami SKU [starymi jednostkami SKU] bramy sieci wirtualnej)](vpn-gateway-about-skus-legacy.md).

   ![Wybierz Wybieranie i typ sieci VPN](./media/vpn-gateway-howto-site-to-site-classic-portal/sku.png "Wybierz typ jednostki SKU i sieci VPN")
2. Wybierz **Typ routingu** dla bramy. Jest on również nazywany typem sieci VPN. Ważne jest, aby wybrać właściwy typ, ponieważ nie można skonwertować bramy z jednego typu na inny. Urządzenie sieci VPN musi być zgodne z wybranym typem routingu. Aby uzyskać więcej informacji na temat typu routingu, zobacz [Informacje o ustawieniach VPN Gateway](vpn-gateway-about-vpn-gateway-settings.md#vpntype). W niektórych artykułach mogą znajdować się odwołania do typów sieci VPN „RouteBased” i „PolicyBased”. Typ „Dynamiczny” odpowiada typowi „RouteBased”, a „Statyczny” — typowi „PolicyBased”.
3. Kliknij pozycję **OK**, aby zapisać ustawienia.
4. Na stronie **nowe połączenie VPN** kliknij przycisk **OK** u dołu strony, aby rozpocząć wdrażanie bramy sieci wirtualnej. W zależności od wybranej jednostki SKU tworzenie bramy sieci wirtualnej może potrwać do 45 minut.

## <a name="7-configure-your-vpn-device"></a><a name="vpndevice"></a>7. Skonfiguruj urządzenie sieci VPN

Połączenia typu lokacja-lokacja z siecią lokalną wymagają urządzenia sieci VPN. W tym kroku konfigurowane jest urządzenie sieci VPN. Podczas konfigurowania urządzenia sieci VPN potrzebne będą:

- Klucz współużytkowany. To ten sam klucz współużytkowany, który jest określany podczas tworzenia połączenia sieci VPN typu lokacja-lokacja. W naszych przykładach używamy podstawowego klucza współużytkowanego. Zalecamy, aby do użycia wygenerować bardziej złożony klucz.
- Publiczny adres IP bramy sieci wirtualnej. Publiczny adres IP można wyświetlić za pomocą witryny Azure Portal, programu PowerShell lub interfejsu wiersza polecenia.

[!INCLUDE [vpn-gateway-configure-vpn-device-rm](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]

## <a name="8-create-the-connection"></a><a name="CreateConnection"></a>8. Utwórz połączenie
W tym kroku należy ustawić klucz współużytkowany i utworzyć połączenie. Ustawiony klucz musi być tym samym kluczem, którego użyto do skonfigurowania urządzenia sieci VPN.

> [!NOTE]
> Obecnie tego kroku nie można wykonać w witrynie Azure Portal. Musisz użyć wersji poleceń cmdlet programu Azure PowerShell pochodzącej z usługi Service Management (SM). Aby uzyskać informacje na temat instalowania tych poleceń cmdlet [, zobacz przed rozpoczęciem](#before) .
>

### <a name="step-1-connect-to-your-azure-account"></a>Krok 1. Nawiąż połączenie z kontem platformy Azure

Te polecenia należy uruchomić lokalnie przy użyciu modułu zarządzania usługą programu PowerShell. 

1. Otwórz konsolę programu PowerShell z podwyższonym poziomem uprawnień. Aby przełączyć się do zarządzania usługami, użyj tego polecenia:

   ```powershell
   azure config mode asm
   ```
2. Połącz się ze swoim kontem. Użyj poniższego przykładu w celu łatwiejszego nawiązania połączenia:

   ```powershell
   Add-AzureAccount
   ```
3. Sprawdź subskrypcje dostępne na koncie.

   ```powershell
   Get-AzureSubscription
   ```
4. Jeśli masz więcej niż jedną subskrypcję, wybierz tę, której chcesz użyć.

   ```powershell
   Select-AzureSubscription -SubscriptionId "Replace_with_your_subscription_ID"
   ```

### <a name="step-2-set-the-shared-key-and-create-the-connection"></a>Krok 2. Ustaw klucz współużytkowany i utwórz połączenie

W przypadku tworzenia klasycznej sieci wirtualnej w portalu (nie przy użyciu programu PowerShell) platforma Azure dodaje nazwę grupy zasobów do krótkiej nazwy. Na przykład, zgodnie z platformą Azure, nazwa sieci wirtualnej utworzonej dla tego ćwiczenia to "Group TestRG1 sieci testvnet1", a nie "sieci testvnet1". Program PowerShell wymaga pełnej nazwy sieci wirtualnej, a nie krótkiej nazwy, która pojawia się w portalu. Długa nazwa nie jest widoczna w portalu. Poniższe kroki ułatwiają wyeksportowanie pliku konfiguracji sieci w celu uzyskania dokładnych wartości nazwy sieci wirtualnej. 

1. Utwórz katalog na komputerze, a następnie wyeksportuj plik konfiguracji sieci do tego katalogu. W tym przykładzie plik konfiguracji sieci zostanie wyeksportowany do katalogu C:\AzureNet.

   ```powershell
   Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
   ```
2. Otwórz plik konfiguracji sieci przy użyciu edytora xml, a następnie sprawdź wartości właściwości „LocalNetworkSite name” i „VirtualNetworkSite name”. Zmodyfikuj przykład dla tego ćwiczenia, aby odzwierciedlał wartości w kodzie XML. W przypadku podawania nazwy, która zawiera spacje, umieść wartość w apostrofach.

3. Ustaw klucz współużytkowany i utwórz połączenie. Wartość „-SharedKey” jest generowana i określana przez Ciebie. W tym przykładzie użyliśmy wartości „abc123”, ale można (i należy) wygenerować bardziej złożoną wartość. Pamiętaj o tym, że wartość podana w tym miejscu musi być taka sama, jak wartość podana podczas konfigurowania urządzenia sieci VPN.

   ```powershell
   Set-AzureVNetGatewayKey -VNetName 'Group TestRG1 TestVNet1' `
   -LocalNetworkSiteName 'D1BFC9CB_Site2' -SharedKey abc123
   ```
   Wynik po utworzeniu połączenia: **Stan: Powodzenie**.

## <a name="9-verify-your-connection"></a><a name="verify"></a>9. Sprawdź połączenie

[!INCLUDE [vpn-gateway-verify-connection-azureportal-classic](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]

Jeśli występują problemy z połączeniem, zobacz sekcję **Rozwiązywanie problemów** spisu treści w okienku po lewej stronie.

## <a name="how-to-reset-a-vpn-gateway"></a><a name="reset"></a>Jak zresetować bramę VPN Gateway

Resetowanie bramy Azure VPN Gateway przydaje się w przypadku utraty połączenia sieci VPN obejmującego wiele lokalizacji w jednym lub wielu tunelach VPN typu lokacja-lokacja. W takiej sytuacji urządzenia lokalnej sieci VPN działają prawidłowo, ale nie mogą nawiązać połączenia w ramach tuneli używających protokołu IPsec z bramami sieci VPN Azure. Aby uzyskać instrukcje, zobacz [Resetowanie bramy VPN Gateway](vpn-gateway-resetgw-classic.md#resetclassic).

## <a name="how-to-change-a-gateway-sku"></a><a name="changesku"></a>Jak zmienić jednostkę SKU bramy

Aby uzyskać instrukcje zmiany jednostki SKU bramy, zobacz [Resize a gateway SKU (Zmiana rozmiaru jednostki SKU bramy)](vpn-gateway-about-SKUS-legacy.md#classicresize).

## <a name="next-steps"></a>Następne kroki

* Po zakończeniu procesu nawiązywania połączenia można dodać do sieci wirtualnych maszyny wirtualne. Aby uzyskać więcej informacji, zobacz [Virtual Machines](https://docs.microsoft.com/azure/) (Maszyny wirtualne).
* Aby uzyskać informacje o wymuszonym tunelowaniu, zobacz [Informacje o wymuszonym tunelowaniu](vpn-gateway-about-forced-tunneling.md).
