---
title: plik dołączany
description: plik dołączany
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 61082333afc88abef0a8d8a57d1f1b1d893b6148
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "67183089"
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
