---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: 354015930170ca6466b3555c78d211c080232c82
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/27/2020
ms.locfileid: "92755393"
---
### <a name="to-view-local-network-gateways"></a>Aby wyświetlić bramy sieci lokalnej

Aby wyświetlić listę bram sieci lokalnej, użyj polecenia [az network local-gateway list](https://docs.microsoft.com/cli/azure/network/local-gateway).

```azurecli
az network local-gateway list --resource-group TestRG1
```

[!INCLUDE [modify-prefix](vpn-gateway-modify-ip-prefix-cli-include.md)]

[!INCLUDE [modify-gateway-IP](vpn-gateway-modify-lng-gateway-ip-cli-include.md)]

### <a name="to-verify-the-shared-key-values"></a>Aby sprawdzić wartości klucza współużytkowanego

Sprawdź, czy wartość klucza współużytkowanego jest taka sama jak wartość użyta do konfiguracji urządzenia sieci VPN. Jeśli nie, ponownie uruchom połączenie przy użyciu wartości z urządzenia lub zaktualizuj urządzenie wartością ze zwracanych danych. Wartości muszą być zgodne. Aby wyświetlić klucz współużytkowany, użyj polecenia [az network vpn-connection-list](https://docs.microsoft.com/cli/azure/network/vpn-connection).

```azurecli
az network vpn-connection shared-key show --connection-name VNet1toSite2 --resource-group TestRG1
```
### <a name="to-view-the-vpn-gateway-public-ip-address"></a>Aby wyświetlić publiczny adres IP bramy sieci VPN

Aby znaleźć publiczny adres IP swojej bramy sieci wirtualnej, użyj polecenia [az network public-ip list](https://docs.microsoft.com/cli/azure/network/public-ip). W celu poprawienia czytelności dane wyjściowe tego polecenia zostały sformatowane do wyświetlania listy publicznych adresów IP w formacie tabeli.

```azurecli
az network public-ip list --resource-group TestRG1 --output table
```
