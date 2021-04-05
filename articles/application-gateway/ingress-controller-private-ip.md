---
title: Używanie prywatnego adresu IP do routingu wewnętrznego dla punktu końcowego transferu danych przychodzących
description: Ten artykuł zawiera informacje na temat używania prywatnych adresów IP na potrzeby routingu wewnętrznego, a tym samym udostępniania punktu końcowego transferu danych przychodzących w ramach klastra do pozostałej części sieci wirtualnej.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 8be37ed1da0da4da3db43ef4c1cd01ed962f24ed
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93397312"
---
# <a name="use-private-ip-for-internal-routing-for-an-ingress-endpoint"></a>Używanie prywatnego adresu IP do routingu wewnętrznego dla punktu końcowego transferu danych przychodzących 

Ta funkcja pozwala uwidocznić punkt końcowy transferu danych przychodzących w ramach `Virtual Network` prywatnego adresu IP.

## <a name="pre-requisites"></a>Wymagania wstępne  
Application Gateway z [konfiguracją prywatnego adresu IP](./configure-application-gateway-with-private-frontend-ip.md)

Istnieją dwa sposoby konfigurowania kontrolera do korzystania z prywatnego adresu IP na potrzeby ruchu przychodzącego.

## <a name="assign-to-a-particular-ingress"></a>Przypisywanie do określonego ruchu przychodzącego
Aby uwidocznić określony ruch przychodzący przez prywatny adres IP, należy użyć adnotacji [`appgw.ingress.kubernetes.io/use-private-ip`](./ingress-controller-annotations.md#use-private-ip) w danych wejściowych.

### <a name="usage"></a>Użycie
```yaml
appgw.ingress.kubernetes.io/use-private-ip: "true"
```

W przypadku bram aplikacji bez prywatnego adresu IP Ingresses z adnotacją `appgw.ingress.kubernetes.io/use-private-ip: "true"` zostanie zignorowane. Zostanie to wskazane w dzienniku zdarzeń przychodzących i AGIC pod.

* Błąd, jak wskazano w zdarzeniu transferu danych przychodzących

    ```bash
    Events:
    Type     Reason       Age               From                                                                     Message
    ----     ------       ----              ----                                                                     -------
    Warning  NoPrivateIP  2m (x17 over 2m)  azure/application-gateway, prod-ingress-azure-5c9b6fcd4-bctcb  Ingress default/hello-world-ingress requires Application Gateway 
    applicationgateway3026 has a private IP address
    ```

* Błąd, jak wskazano w dziennikach AGIC

    ```bash
    E0730 18:57:37.914749       1 prune.go:65] Ingress default/hello-world-ingress requires Application Gateway applicationgateway3026 has a private IP address
    ```


## <a name="assign-globally"></a>Przypisz globalnie
W przypadku wymogu jest ograniczenie, że wszystkie Ingresses mają być udostępniane za pośrednictwem prywatnego adresu IP. w tym celu należy użyć `appgw.usePrivateIP: true` `helm` konfiguracji.

### <a name="usage"></a>Użycie
```yaml
appgw:
    subscriptionId: <subscriptionId>
    resourceGroup: <resourceGroupName>
    name: <applicationGatewayName>
    usePrivateIP: true
```

Dzięki temu kontroler transferu danych przychodzących będzie przefiltrować konfiguracje adresów IP dla prywatnego adresu IP podczas konfigurowania odbiorników frontonu na Application Gateway.
AGIC będzie awaryjnego i ulega awarii, jeśli `usePrivateIP: true` nie zostanie przypisany prywatny adres IP.

> [!NOTE]
> Jednostka SKU Application Gateway v2 wymaga publicznego adresu IP. Jeśli potrzebujesz Application Gateway być prywatnym, Dołącz [`Network Security Group`](../virtual-network/network-security-groups-overview.md) do podsieci Application Gateway, aby ograniczyć ruch.