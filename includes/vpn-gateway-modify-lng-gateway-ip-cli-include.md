---
title: dołączanie pliku
description: dołączanie pliku
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: e368b1590f263618969423d57cdf0531fc2bb54d
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/27/2020
ms.locfileid: "67183070"
---
### <a name="to-modify-the-local-network-gateway-gatewayipaddress"></a>Aby zmodyfikować element „gatewayIpAddress” bramy sieci lokalnej

W przypadku zmiany publicznego adresu IP urządzenia sieci VPN, z którym chcesz nawiązać połączenie, musisz zmodyfikować bramę sieci lokalnej w celu odzwierciedlenia tej zmiany. Adres IP bramy można zmienić bez usuwania istniejącego połączenia bramy sieci VPN (jeśli istnieje). Aby zmodyfikować adres IP bramy, zamień wartości „Site2” i „TestRG1” na własne wartości za pomocą polecenia [az network local-gateway update](https://docs.microsoft.com/cli/azure/network/local-gateway).

```azurecli
az network local-gateway update --gateway-ip-address 23.99.222.170 --name Site2 --resource-group TestRG1
```

Sprawdź, czy adres IP w danych wyjściowych jest poprawny:

```
"gatewayIpAddress": "23.99.222.170",
```
