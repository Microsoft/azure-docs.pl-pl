---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 069d7af9cee0bee673c8f0b32659ce362fe4dd70
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95555214"
---
### <a name="to-modify-the-local-network-gateway-gatewayipaddress"></a>Aby zmodyfikować element „gatewayIpAddress” bramy sieci lokalnej

W przypadku zmiany publicznego adresu IP urządzenia sieci VPN, z którym chcesz nawiązać połączenie, musisz zmodyfikować bramę sieci lokalnej w celu odzwierciedlenia tej zmiany. Adres IP bramy można zmienić bez usuwania istniejącego połączenia bramy sieci VPN (jeśli istnieje). Aby zmodyfikować adres IP bramy, zamień wartości „Site2” i „TestRG1” na własne wartości za pomocą polecenia [az network local-gateway update](/cli/azure/network/local-gateway).

```azurecli
az network local-gateway update --gateway-ip-address 23.99.222.170 --name Site2 --resource-group TestRG1
```

Sprawdź, czy adres IP w danych wyjściowych jest poprawny:

```
"gatewayIpAddress": "23.99.222.170",
```