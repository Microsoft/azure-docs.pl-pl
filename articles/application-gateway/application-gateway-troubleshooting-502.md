---
title: Rozwiązywanie problemów z błędami bramy — Application Gateway platformy Azure
description: 'Dowiedz się, jak rozwiązywać problemy z błędem serwera Application Gateway: 502 — serwer sieci Web odebrał nieprawidłową odpowiedź, działając jako brama lub serwer proxy.'
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: troubleshooting
ms.date: 11/16/2019
ms.author: amsriva
ms.openlocfilehash: d44f9109540c3899ab50bd5c4c02afa19045bafb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96182941"
---
# <a name="troubleshooting-bad-gateway-errors-in-application-gateway"></a>Rozwiązywanie problemów z nieprawidłową bramą w usłudze Application Gateway

Dowiedz się, jak rozwiązywać błędy nieprawidłowej bramy (502) otrzymane podczas korzystania z usługi Azure Application Gateway.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Omówienie

Po skonfigurowaniu bramy aplikacji może być wyświetlany komunikat o błędzie "błąd serwera: 502 — serwer sieci Web odebrał nieprawidłową odpowiedź, działając jako brama lub serwer proxy". Ten błąd może wystąpić z następujących powodów:

* SIECIOWEJ grupy zabezpieczeń, UDR lub niestandardowy serwer DNS blokuje dostęp do elementów członkowskich puli zaplecza.
* Maszyny wirtualne zaplecza lub wystąpienia zestawu skalowania maszyn wirtualnych nie odpowiadają domyślnej sondy kondycji.
* Nieprawidłowa lub niewłaściwa konfiguracja niestandardowych sond kondycji.
* [Pula zaplecza Application Gateway platformy Azure nie jest skonfigurowana ani pusta](#empty-backendaddresspool).
* Żadna z maszyn wirtualnych lub wystąpień w [zestawie skalowania maszyn wirtualnych nie jest w dobrej kondycji](#unhealthy-instances-in-backendaddresspool).
* [Żądaj problemów z limitem czasu lub połączeniem](#request-time-out) z żądaniami użytkowników.

## <a name="network-security-group-user-defined-route-or-custom-dns-issue"></a>Sieciowa Grupa zabezpieczeń, trasa zdefiniowana przez użytkownika lub problem z niestandardowym systemem DNS

### <a name="cause"></a>Przyczyna

Jeśli dostęp do zaplecza jest zablokowany z powodu sieciowej grupy zabezpieczeń, UDR lub niestandardowej usługi DNS, wystąpienia bramy aplikacji nie mogą nawiązać połączenia z pulą zaplecza. Powoduje to awarie sondy, co spowodowało błędy 502.

SIECIOWEJ grupy zabezpieczeń/UDR może być obecny w podsieci bramy aplikacji lub podsieci, w której wdrożono maszyny wirtualne aplikacji.

Podobnie obecność niestandardowego serwera DNS w sieci wirtualnej może również powodować problemy. Nazwa FQDN używana przez członków puli zaplecza może nie zostać poprawnie rozwiązana przez skonfigurowany przez użytkownika serwer DNS dla sieci wirtualnej.

### <a name="solution"></a>Rozwiązanie

Sprawdź poprawność konfiguracji sieciowej grupy zabezpieczeń, UDR i DNS, wykonując następujące czynności:

* Sprawdź sieciowych grup zabezpieczeń skojarzone z podsiecią bramy aplikacji. Upewnij się, że komunikacja z zapleczem nie jest zablokowana.
* Sprawdź UDR skojarzone z podsiecią bramy aplikacji. Upewnij się, że UDR nie przekierowuje ruchu z podsieci zaplecza. Na przykład sprawdź, czy są kierowane wirtualne urządzenia sieciowe lub trasy domyślne anonsowane do podsieci bramy aplikacji za pośrednictwem usługi ExpressRoute/VPN.

```azurepowershell
$vnet = Get-AzVirtualNetwork -Name vnetName -ResourceGroupName rgName
Get-AzVirtualNetworkSubnetConfig -Name appGwSubnet -VirtualNetwork $vnet
```

* Sprawdź efektywne sieciowej grupy zabezpieczeń i Roześlij z maszyną wirtualną zaplecza

```azurepowershell
Get-AzEffectiveNetworkSecurityGroup -NetworkInterfaceName nic1 -ResourceGroupName testrg
Get-AzEffectiveRouteTable -NetworkInterfaceName nic1 -ResourceGroupName testrg
```

* Sprawdź obecność niestandardowego serwera DNS w sieci wirtualnej. System DNS można sprawdzić, przeglądając szczegóły właściwości sieci wirtualnej w danych wyjściowych.

```json
Get-AzVirtualNetwork -Name vnetName -ResourceGroupName rgName 
DhcpOptions            : {
                           "DnsServers": [
                             "x.x.x.x"
                           ]
                         }
```
Jeśli jest obecny, upewnij się, że serwer DNS może prawidłowo rozpoznać nazwę FQDN składowej puli zaplecza.

## <a name="problems-with-default-health-probe"></a>Problemy z domyślną sondą kondycji

### <a name="cause"></a>Przyczyna

502 błędy mogą być również częstymi wskaźnikami, że domyślna sonda kondycji nie może nawiązać połączenia z maszynami wirtualnymi zaplecza.

Po aprowizacji wystąpienia bramy aplikacji automatycznie konfiguruje domyślne sondy kondycji dla każdego BackendAddressPoolu przy użyciu właściwości BackendHttpSetting. Do ustawienia tej sondy nie jest wymagane wprowadzanie danych przez użytkownika. W przypadku skonfigurowania reguły równoważenia obciążenia następuje skojarzenie między BackendHttpSetting i BackendAddressPool. Domyślna sonda jest konfigurowana dla każdego z tych skojarzeń, a Brama aplikacji uruchamia okresowe połączenie sprawdzania kondycji z każdym wystąpieniem w BackendAddressPool na porcie określonym w elemencie BackendHttpSetting. 

Poniższa tabela zawiera listę wartości skojarzonych z domyślną sondą kondycji:

| Właściwość sondy | Wartość | Opis |
| --- | --- | --- |
| Adres URL sondy |`http://127.0.0.1/` |Ścieżka adresu URL |
| Interwał |30 |Interwał sondy w sekundach |
| Limit czasu |30 |Limit czasu sondy w sekundach |
| Próg złej kondycji |3 |Liczba ponownych prób sondowania. Serwer zaplecza jest oznaczony jako wyłączony po kolejnej liczbie błędów sondy osiągnie próg złej kondycji. |

### <a name="solution"></a>Rozwiązanie

* Upewnij się, że domyślna lokacja jest skonfigurowana i nasłuchuje na adresie 127.0.0.1.
* Jeśli BackendHttpSetting określa port inny niż 80, należy skonfigurować domyślną lokację do nasłuchiwania na tym porcie.
* Wywołanie `http://127.0.0.1:port` powinno zwrócić kod wyniku HTTP 200. Ta wartość powinna zostać zwrócona w ciągu 30-sekundowego okresu limitu czasu.
* Upewnij się, że skonfigurowany port jest otwarty i że nie ma żadnych reguł zapory lub sieciowych grup zabezpieczeń platformy Azure, które blokują ruch przychodzący lub wychodzący na porcie skonfigurowanym.
* Jeśli klasyczne maszyny wirtualne platformy Azure lub usługa w chmurze jest używana z nazwą FQDN lub publicznym adresem IP, należy się upewnić, że odpowiadający mu [punkt końcowy](/previous-versions/azure/virtual-machines/windows/classic/setup-endpoints?toc=%2fazure%2fapplication-gateway%2ftoc.json) jest otwarty.
* Jeśli maszyna wirtualna jest skonfigurowana za pośrednictwem Azure Resource Manager i znajduje się poza siecią wirtualną, w której wdrożono bramę aplikacji, należy skonfigurować [sieciową grupę zabezpieczeń](../virtual-network/network-security-groups-overview.md) tak, aby zezwalała na dostęp na żądanym porcie.

## <a name="problems-with-custom-health-probe"></a>Problemy z niestandardową sondą kondycji

### <a name="cause"></a>Przyczyna

Niestandardowe sondy kondycji umożliwiają dodatkową elastyczność podczas domyślnego działania sondowania. W przypadku korzystania z sond niestandardowych można skonfigurować interwał sondowania, adres URL, ścieżkę do testu oraz liczbę niepowodzeń odpowiedzi, które mają zostać zaakceptowane przed oznaczeniem wystąpienia puli zaplecza jako złej kondycji.

Dodawane są następujące dodatkowe właściwości:

| Właściwość sondy | Opis |
| --- | --- |
| Nazwa |Nazwa sondy. Ta nazwa służy do odwoływania się do sondy w ustawieniach protokołu HTTP zaplecza. |
| Protokół |Protokół używany do wysyłania sondy. Sonda używa protokołu zdefiniowanego w ustawieniach protokołu HTTP zaplecza |
| Host |Nazwa hosta do wysłania sondy. Dotyczy tylko sytuacji, gdy w bramie aplikacji skonfigurowano wiele witryn. Różni się to od nazwy hosta maszyny wirtualnej. |
| Ścieżka |Ścieżka względna sondy. Prawidłowa ścieżka zaczyna się od znaku "/". Sonda jest wysyłana do \<protocol\> :// \<host\> :\<port\>\<path\> |
| Interwał |Interwał sondy (w sekundach). Jest to przedział czasu między dwoma kolejnymi sondami. |
| Limit czasu |Limit czasu sondy w sekundach. Jeśli prawidłowa odpowiedź nie zostanie odebrana w tym okresie, sonda zostanie oznaczona jako niepowodzenie. |
| Próg złej kondycji |Liczba ponownych prób sondowania. Serwer zaplecza jest oznaczony jako wyłączony po kolejnej liczbie błędów sondy osiągnie próg złej kondycji. |

### <a name="solution"></a>Rozwiązanie

Sprawdź, czy niestandardowa sonda kondycji została prawidłowo skonfigurowana jako poprzednia tabela. Oprócz powyższych kroków rozwiązywania problemów należy również upewnić się, że:

* Upewnij się, że sonda została prawidłowo określona zgodnie z [przewodnikiem](application-gateway-create-probe-ps.md).
* Jeśli Brama aplikacji jest skonfigurowana dla jednej lokacji, domyślnie nazwa hosta powinna być określona jako `127.0.0.1` , chyba że jest skonfigurowana w sondie niestandardowej.
* Upewnij się, że wywołanie http:// \<host\> : \<port\> \<path\> zwraca kod wyniku http 200.
* Upewnij się, że interwał, limit czasu i UnhealtyThreshold znajdują się w akceptowalnych zakresach.
* W przypadku korzystania z sondy HTTPS upewnij się, że serwer zaplecza nie wymaga SNI, konfigurując certyfikat rezerwowy na serwerze zaplecza.

## <a name="request-time-out"></a>Limit czasu żądania

### <a name="cause"></a>Przyczyna

Po odebraniu żądania użytkownika Brama aplikacji stosuje skonfigurowane reguły do żądania i kieruje je do wystąpienia puli zaplecza. Czeka na konfigurację interwału czasu dla odpowiedzi z wystąpienia zaplecza. Domyślnie ten interwał wynosi **20** sekund. Jeśli Brama aplikacji nie odbiera odpowiedzi z aplikacji zaplecza w tym interwale, żądanie użytkownika pobiera błąd 502.

### <a name="solution"></a>Rozwiązanie

Application Gateway umożliwia skonfigurowanie tego ustawienia za pośrednictwem BackendHttpSetting, które można następnie zastosować do różnych pul. Różne pule zaplecza mogą mieć różne BackendHttpSetting i mieć skonfigurowany limit czasu żądania.

```azurepowershell
    New-AzApplicationGatewayBackendHttpSettings -Name 'Setting01' -Port 80 -Protocol Http -CookieBasedAffinity Enabled -RequestTimeout 60
```

## <a name="empty-backendaddresspool"></a>Puste BackendAddressPool

### <a name="cause"></a>Przyczyna

Jeśli Brama aplikacji nie ma skonfigurowanych maszyn wirtualnych lub zestawu skalowania maszyn wirtualnych w puli adresów zaplecza, nie może kierować żadnych żądań klienta i wysyła komunikat o błędzie nieprawidłowej bramy.

### <a name="solution"></a>Rozwiązanie

Upewnij się, że pula adresów zaplecza nie jest pusta. Można to zrobić za pomocą programu PowerShell, interfejsu wiersza polecenia lub portalu.

```azurepowershell
Get-AzApplicationGateway -Name "SampleGateway" -ResourceGroupName "ExampleResourceGroup"
```

Dane wyjściowe z powyższego polecenia cmdlet powinny zawierać niepustą pulę adresów zaplecza. Poniższy przykład przedstawia dwie zwrócone pule, które są skonfigurowane przy użyciu nazwy FQDN lub adresów IP dla maszyn wirtualnych zaplecza. Stan aprowizacji elementu BackendAddressPool musi mieć wartość "powodzenie".

BackendAddressPoolsText:

```json
[{
    "BackendAddresses": [{
        "ipAddress": "10.0.0.10",
        "ipAddress": "10.0.0.11"
    }],
    "BackendIpConfigurations": [],
    "ProvisioningState": "Succeeded",
    "Name": "Pool01",
    "Etag": "W/\"00000000-0000-0000-0000-000000000000\"",
    "Id": "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name>/backendAddressPools/pool01"
}, {
    "BackendAddresses": [{
        "Fqdn": "xyx.cloudapp.net",
        "Fqdn": "abc.cloudapp.net"
    }],
    "BackendIpConfigurations": [],
    "ProvisioningState": "Succeeded",
    "Name": "Pool02",
    "Etag": "W/\"00000000-0000-0000-0000-000000000000\"",
    "Id": "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name>/backendAddressPools/pool02"
}]
```

## <a name="unhealthy-instances-in-backendaddresspool"></a>Wystąpienia w złej kondycji w BackendAddressPool

### <a name="cause"></a>Przyczyna

Jeśli wszystkie wystąpienia programu BackendAddressPool są w złej kondycji, Brama aplikacji nie ma żadnego zaplecza, aby skierować żądanie użytkownika do programu. Może to również być przypadek, gdy wystąpienia zaplecza są w dobrej kondycji, ale nie mają wdrożonej wymaganej aplikacji.

### <a name="solution"></a>Rozwiązanie

Upewnij się, że wystąpienia są w dobrej kondycji i że aplikacja została prawidłowo skonfigurowana. Sprawdź, czy wystąpienia zaplecza mogą reagować na polecenie ping z innej maszyny wirtualnej w tej samej sieci wirtualnej. W przypadku skonfigurowania z publicznym punktem końcowym upewnij się, że żądanie przeglądarki do aplikacji sieci Web jest z obsługą.

## <a name="next-steps"></a>Następne kroki

Jeśli powyższe kroki nie rozwiążą problemu, Otwórz [bilet pomocy technicznej](https://azure.microsoft.com/support/options/).