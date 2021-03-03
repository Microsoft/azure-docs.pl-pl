---
title: Zresetuj bramę sieci VPN lub połączenie, aby ponownie ustanowić tunel IPsec
titleSuffix: Azure VPN Gateway
description: Zresetuj połączenie lub bramę sieci VPN, aby ponownie ustanowić tunele IPsec.
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 02/22/2021
ms.author: cherylmc
ms.openlocfilehash: adc2ffd63d73baaddce00324787df61061ea69dc
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101726657"
---
# <a name="reset-a-vpn-gateway-or-a-connection"></a>Resetowanie bramy sieci VPN lub połączenia

Resetowanie bramy sieci VPN platformy Azure lub połączenia bramy jest przydatne w przypadku utraty połączenia sieci VPN obejmującego wiele lokalizacji w jednym lub wielu tunelach VPN typu lokacja-lokacja. W takiej sytuacji urządzenia lokalnej sieci VPN działają prawidłowo, ale nie mogą nawiązać połączenia w ramach tuneli używających protokołu IPsec z bramami sieci VPN Azure. Ten artykuł pomaga zresetować bramę sieci VPN lub połączenie bramy.

## <a name="what-happens-during-a-reset"></a>Co się stanie w trakcie resetowania

### <a name="gateway-reset"></a>Resetowanie bramy

Brama sieci VPN składa się z dwóch wystąpień maszyn wirtualnych działających w konfiguracji aktywnego stanu gotowości. Zresetowanie bramy powoduje ponowne uruchomienie bramy, a następnie ponowne zastosowanie do niej konfiguracji obejmującej wiele lokalizacji. Brama zachowa publiczny adres IP, który został z nią wcześniej powiązany. Oznacza to, że nie będzie konieczne aktualizowanie konfiguracji routera sieci VPN pod kątem nowego publicznego adresu IP bramy sieci VPN Azure.

Po wydaniu polecenia resetowania bramy bieżące aktywne wystąpienie usługi Azure VPN Gateway zostanie uruchomione ponownie. W trakcie pracy w trybie failover z aktywnego wystąpienia (trwa ponowne uruchomienie) w wystąpieniu gotowości zostanie wystąpiła krótka przerwa w działaniu. Przerwa powinna trwać niż dłużej niż minutę.

Jeśli połączenie nie zostanie przywrócone po pierwszym ponownym rozruchu komputera, należy wydać to polecenie ponownie, aby uruchomić ponownie drugie wystąpienie maszyny wirtualnej (nową aktywną bramę). Jeśli wymagane jest przeprowadzenie dwóch rozruchów jednocześnie, to ponowne uruchomienie obu wystąpień maszyn wirtualnych (aktywnego i w gotowości) będzie trwać nieco dłużej. Spowoduje to dłuższą przerwę w łączności sieci VPN, do 30 do 45 minut w przypadku maszyn wirtualnych do ukończenia ponownych uruchomień.

Jeśli po dwóch ponownych uruchomieniach nadal występują problemy z łącznością między różnymi lokalizacjami, Otwórz żądanie pomocy technicznej z Azure Portal.

### <a name="connection-reset"></a>Resetowanie połączenia

W przypadku wybrania opcji resetowania połączenia Brama nie zostanie ponownie rozruchowa. Tylko wybrane połączenie jest resetowane i przywrócone.

## <a name="reset-a-connection"></a>Zresetuj połączenie

Połączenie można łatwo zresetować przy użyciu Azure Portal.

1. Przejdź do **połączenia** , które chcesz zresetować. Zasób połączenia można znaleźć, umieszczając go we **wszystkich zasobach** lub przechodząc do lokalizacji **"Nazwa bramy"-> połączenia — > "nazwa połączenia** "
1. Na stronie **połączenie** wybierz pozycję **Zresetuj** z menu po lewej stronie.
1. Na stronie **Reset** kliknij pozycję **Zresetuj** , aby zresetować połączenie.

   :::image type="content" source="./media/reset-gateway/reset-connection.png" alt-text="Zrzut ekranu przedstawiający Resetowanie.":::

## <a name="reset-a-vpn-gateway"></a>Resetowanie bramy VPN Gateway

Przed zresetowaniem bramy dla każdego tunelu połączenia sieci VPN typu lokacja-lokacja (site-to site, S2S) korzystającego z protokołu IPsec należy sprawdzić kluczowe elementy wymienione poniżej. Brak zgodności którychkolwiek elementów spowoduje przerwanie połączenia tuneli sieci VPN S2S. Weryfikowanie i poprawianie konfiguracji usług lokalnych i bram sieci VPN platformy Azure powoduje zaoszczędzenie niepotrzebnych ponownych uruchomień i zakłóceń dla innych połączeń roboczych na bramach.

Przed zresetowaniem bramy zweryfikuj następujące elementy:

* Wirtualne adresy IP (VIP) bramy sieci VPN Azure i bramy lokalnej sieci VPN powinny być prawidłowo skonfigurowane zarówno w zasadach platformy Azure, jak i zasadach lokalnej sieci VPN.
* Klucz wstępny musi być taki sam w przypadku bramy sieci VPN Azure oraz bramy lokalnej sieci VPN.
* W przypadku zastosowania konkretnej konfiguracji uwzględniającej protokół IPsec/IKE, takiej jak szyfrowanie, algorytmy wyznaczania wartości skrótu i doskonałe utajnienie przekazywania (Perfect Forward Secrecy, PFS), należy się upewnić, że zarówno brama sieci VPN Azure, jak i brama lokalnej sieci VPN mają takie same konfiguracje.

### <a name="azure-portal"></a><a name="portal"></a>Azure Portal

Bramę sieci VPN Menedżer zasobów można zresetować przy użyciu Azure Portal. Jeśli chcesz zresetować bramę klasyczną, zobacz kroki programu PowerShell dla [klasycznego modelu wdrażania](#resetclassic).

[!INCLUDE [portal steps](../../includes/vpn-gateway-reset-gw-portal-include.md)]

### <a name="powershell"></a><a name="ps"></a>PowerShell

#### <a name="resource-manager-deployment-model"></a>Model wdrażania usługi Resource Manager

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Polecenie cmdlet służące do **resetowania bramy jest resetowane-AzVirtualNetworkGateway**. Przed przeprowadzeniem resetowania upewnij się, że masz najnowszą wersję programu [PowerShell AZ cmdlets](/powershell/module/az.network). Poniższy przykład resetuje bramę sieci wirtualnej o nazwie VNet1GW w grupie zasobów TestRG1:

```powershell
$gw = Get-AzVirtualNetworkGateway -Name VNet1GW -ResourceGroupName TestRG1
Reset-AzVirtualNetworkGateway -VirtualNetworkGateway $gw
```

Wynik:

Po otrzymaniu zwracanego wyniku możesz założyć, że Resetowanie bramy zakończyło się pomyślnie. Jednak nie ma nic w wyniku powrotu, który wskazuje jawnie, że Resetowanie zakończyło się pomyślnie. Jeśli chcesz uważnie sprawdzić historię, aby dokładnie zobaczyć, kiedy wystąpiło zresetowanie bramy, możesz wyświetlić te informacje w [Azure Portal](https://portal.azure.com). W portalu przejdź do **"gatewayname"-> Resource Health**.

#### <a name="classic-deployment-model"></a><a name="resetclassic"></a>Klasyczny model wdrażania

Polecenie cmdlet służące do **resetowania bramy jest resetowane-azurevnetgateway nastąpi**. Polecenia cmdlet Azure PowerShell do zarządzania usługami muszą być zainstalowane lokalnie na pulpicie. Nie można użyć Azure Cloud Shell. Przed przeprowadzeniem resetowania upewnij się, że masz najnowszą wersję [poleceń cmdlet programu PowerShell do zarządzania usługami (SM)](/powershell/azure/servicemanagement/install-azure-ps#azure-service-management-cmdlets). Korzystając z tego polecenia, upewnij się, że używasz pełnej nazwy sieci wirtualnej. Klasyczne sieci wirtualnych, które zostały utworzone przy użyciu portalu, mają długą nazwę, która jest wymagana dla programu PowerShell. Możesz wyświetlić długą nazwę za pomocą polecenia "Get-AzureVNetConfig-ExportToFile C:\Myfoldername\NetworkConfig.xml".

Poniższy przykład resetuje bramę dla sieci wirtualnej o nazwie "Group TestRG1 sieci testvnet1" (która jest wyświetlana jako "sieci testvnet1" w portalu):

```powershell
Reset-AzureVNetGateway –VnetName 'Group TestRG1 TestVNet1'
```

Wynik:

```powershell
Error          :
HttpStatusCode : OK
Id             : f1600632-c819-4b2f-ac0e-f4126bec1ff8
Status         : Successful
RequestId      : 9ca273de2c4d01e986480ce1ffa4d6d9
StatusCode     : OK
```

### <a name="azure-cli"></a><a name="cli"></a>Interfejs wiersza polecenia platformy Azure

Aby zresetować bramę, użyj polecenia [AZ Network VNET-Gateway Reset](/cli/azure/network/vnet-gateway) . Poniższy przykład resetuje bramę sieci wirtualnej o nazwie VNet5GW w grupie zasobów TestRG5:

```azurecli
az network vnet-gateway reset -n VNet5GW -g TestRG5
```

Wynik:

Po otrzymaniu zwracanego wyniku możesz założyć, że Resetowanie bramy zakończyło się pomyślnie. Jednak nie ma nic w wyniku powrotu, który wskazuje jawnie, że Resetowanie zakończyło się pomyślnie. Jeśli chcesz uważnie sprawdzić historię, aby dokładnie zobaczyć, kiedy wystąpiło zresetowanie bramy, możesz wyświetlić te informacje w [Azure Portal](https://portal.azure.com). W portalu przejdź do **"gatewayname"-> Resource Health**.
