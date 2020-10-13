---
title: Obsługa protokołu WebSocket na platformie Azure Application Gateway
description: Usługa Application Gateway zapewnia natywną obsługę protokołu WebSocket w bramach każdego rozmiaru. Nie ma ustawień konfigurowalnych przez użytkownika.
author: vhorne
ms.author: amsriva
ms.service: application-gateway
services: application-gateway
ms.topic: conceptual
ms.date: 11/16/2019
ms.openlocfilehash: 59ca7708b24d2e75381290b80adeb671e2b49822
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91362699"
---
# <a name="overview-of-websocket-support-in-application-gateway"></a>Omówienie obsługi protokołu WebSocket w Application Gateway

Usługa Application Gateway zapewnia natywną obsługę protokołu WebSocket w bramach każdego rozmiaru. Nie ma żadnych ustawień konfigurowanych przez użytkownika umożliwiających selektywne włączenie lub wyłączenie obsługi protokołu WebSocket. 

Protokół WebSocket w standardzie [RFC6455](https://tools.ietf.org/html/rfc6455) umożliwia pełną komunikację dwukierunkową między serwerem a klientem przez długotrwałe połączenie TCP. Ta funkcja pozwala na bardziej interaktywną komunikację między serwerem sieci Web i klientem, który może być dwukierunkowy bez potrzeby sondowania w przypadku implementacji opartych na protokole HTTP. Protokół WebSocket ma niskie obciążenie, w przeciwieństwie do protokołu HTTP i może ponownie użyć tego samego połączenia TCP dla wielu żądań/odpowiedzi, co zwiększa efektywność użycia zasobów. Protokoły WebSocket są przeznaczone do pracy nad tradycyjnymi portami HTTP z 80 i 443.

Aby odbierać ruch z użyciem protokołu WebSocket, można nadal używać standardowego odbiornika HTTP na porcie 80 lub 443. Ruch z użyciem protokołu WebSocket jest następnie kierowany do serwera zaplecza z włączoną obsługą protokołu WebSocket przy użyciu odpowiedniej puli zaplecza, jak określono w regułach usługi Application Gateway. Serwer wewnętrznej bazy danych musi odpowiadać sondom bramy aplikacji, które są opisane w sekcji [Przegląd sondy kondycji](application-gateway-probe-overview.md) . Sondy kondycji bramy aplikacji są tylko HTTP/HTTPS. Każdy serwer zaplecza musi odpowiadać na sondy protokołu HTTP dla usługi Application Gateway, aby kierować ruch protokołu WebSocket do serwera.

Jest on używany w aplikacjach, które korzystają z szybkiej komunikacji w czasie rzeczywistym, takiej jak czat, pulpit nawigacyjny i aplikacje do gier.

## <a name="how-does-websocket-work"></a>Jak działa protokół WebSocket

Do nawiązania połączenia z protokołem WebSocket jest wymieniana określona uzgadnianie oparte na protokole HTTP między klientem a serwerem. Jeśli to się powiedzie, protokół warstwy aplikacji jest "uaktualniony" z protokołu HTTP do usługi WebSockets przy użyciu wcześniej ustanowionego połączenia TCP. Gdy to nastąpi, protokół HTTP zostanie całkowicie wyprowadzony z obrazu; dane można wysyłać lub odbierać przy użyciu protokołu WebSocket przez oba punkty końcowe, aż do zamknięcia połączenia z protokołem WebSocket. 

![Diagram porównuje klienta interakcji z serwerem sieci Web, łącząc dwa razy, aby uzyskać dwie odpowiedzi z interakcją z użyciem protokołu WebSocket, gdzie klient łączy się z serwerem jednokrotnie, aby uzyskać wiele odpowiedzi.](./media/application-gateway-websocket/websocket.png)

### <a name="listener-configuration-element"></a>Element konfiguracji odbiornika

Istniejący odbiornik HTTP może służyć do obsługi ruchu protokołu WebSocket. Poniżej znajduje się fragment elementu httpListeners z przykładowego pliku szablonu. Do obsługi protokołu WebSocket i bezpiecznego ruchu WebSocket wymagane są odbiorniki HTTP i HTTPS. Podobnie można użyć portalu lub Azure PowerShell, aby utworzyć bramę aplikacji z odbiornikami na porcie 80/443 do obsługi ruchu protokołu WebSocket.

```json
"httpListeners": [
        {
            "name": "appGatewayHttpsListener",
            "properties": {
                "FrontendIPConfiguration": {
                    "Id": "/subscriptions/{subscriptionId/resourceGroups/{resourceGroupName/providers/Microsoft.Network/applicationGateways/{applicationGatewayName/frontendIPConfigurations/DefaultFrontendPublicIP"
                },
                "FrontendPort": {
                    "Id": "/subscriptions/{subscriptionId/resourceGroups/{resourceGroupName/providers/Microsoft.Network/applicationGateways/{applicationGatewayName/frontendPorts/appGatewayFrontendPort443'"
                },
                "Protocol": "Https",
                "SslCertificate": {
                    "Id": "/subscriptions/{subscriptionId/resourceGroups/{resourceGroupName/providers/Microsoft.Network/applicationGateways/{applicationGatewayName/sslCertificates/appGatewaySslCert1'"
                },
            }
        },
        {
            "name": "appGatewayHttpListener",
            "properties": {
                "FrontendIPConfiguration": {
                    "Id": "/subscriptions/{subscriptionId/resourceGroups/{resourceGroupName/providers/Microsoft.Network/applicationGateways/{applicationGatewayName/frontendIPConfigurations/appGatewayFrontendIP'"
                },
                "FrontendPort": {
                    "Id": "/subscriptions/{subscriptionId/resourceGroups/{resourceGroupName/providers/Microsoft.Network/applicationGateways/{applicationGatewayName/frontendPorts/appGatewayFrontendPort80'"
                },
                "Protocol": "Http",
            }
        }
    ],
```

## <a name="backendaddresspool-backendhttpsetting-and-routing-rule-configuration"></a>Konfiguracja reguły BackendAddressPool, BackendHttpSetting i routingu

BackendAddressPool jest używany do definiowania puli zaplecza z serwerami z obsługą protokołu WebSocket. BackendHttpSetting jest definiowana z portem zaplecza 80 i 443. Wartość limitu czasu żądania w ustawieniach protokołu HTTP dotyczy również sesji protokołu WebSocket. W regule routingu nie jest wymagana żadna zmiana, która jest używana do powiązania odpowiedniego odbiornika z odpowiednią pulą adresów zaplecza. 

```json
"requestRoutingRules": [{
    "name": "<ruleName1>",
    "properties": {
        "RuleType": "Basic",
        "httpListener": {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/httpListeners/appGatewayHttpsListener')]"
        },
        "backendAddressPool": {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendAddressPools/ContosoServerPool')]"
        },
        "backendHttpSettings": {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendHttpSettingsCollection/appGatewayBackendHttpSettings')]"
        }
    }

}, {
    "name": "<ruleName2>",
    "properties": {
        "RuleType": "Basic",
        "httpListener": {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/httpListeners/appGatewayHttpListener')]"
        },
        "backendAddressPool": {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendAddressPools/ContosoServerPool')]"
        },
        "backendHttpSettings": {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendHttpSettingsCollection/appGatewayBackendHttpSettings')]"
        }

    }
}]
```

## <a name="websocket-enabled-backend"></a>Zaplecze z obsługą protokołu WebSocket

Zaplecze musi mieć serwer sieci Web HTTP/HTTPS uruchomiony na skonfigurowanym porcie (zwykle 80/443), aby protokół WebSocket działał. Ten wymóg jest spowodowany tym, że protokół WebSocket wymaga, aby uzgadnianie początkowe było HTTP z uaktualnieniem do protokołu WebSocket jako pola nagłówka. Poniżej znajduje się przykład nagłówka:

```
    GET /chat HTTP/1.1
    Host: server.example.com
    Upgrade: websocket
    Connection: Upgrade
    Sec-WebSocket-Key: dGhlIHNhbXBsZSBub25jZQ==
    Origin: https://example.com
    Sec-WebSocket-Protocol: chat, superchat
    Sec-WebSocket-Version: 13
```

Kolejną przyczyną jest to, że sonda kondycji zaplecza usługi Application Gateway obsługuje tylko protokoły HTTP i HTTPS. Jeśli serwer wewnętrznej bazy danych nie odpowiada na sondy HTTP lub HTTPS, jest wyłączany z puli zaplecza.

## <a name="next-steps"></a>Następne kroki

Po zapoznaniu się z obsługą protokołu WebSocket przejdź do [pozycji tworzenie bramy aplikacji](quick-create-powershell.md) , aby rozpocząć pracę z aplikacją sieci Web z obsługą protokołu WebSocket.