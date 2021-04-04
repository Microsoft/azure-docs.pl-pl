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
ms.openlocfilehash: f222d4a7f4724506112a47eff61ccc48354dd622
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96027223"
---
### <a name="to-modify-local-network-gateway-ip-address-prefixes---no-gateway-connection"></a><a name="noconnection"></a>Aby zmodyfikować prefiksy adresów IP bramy sieci lokalnej — brak połączenia bramy

Jeśli nie masz połączenia bramy i chcesz dodać lub usunąć prefiksy adresów IP, możesz użyć tego samego polecenia, za pomocą którego utworzono bramę sieci lokalnej: [az network local-gateway create](/cli/azure/network/local-gateway). To polecenie umożliwia również zaktualizowanie adresu IP bramy dla urządzenia sieci VPN. W celu zastąpienia bieżących ustawień użyj istniejącej nazwy bramy sieci lokalnej. Jeśli użyjesz innej nazwy, utworzysz nową bramę sieci lokalnej, a nie zastąpisz istniejącej.

Po wprowadzeniu każdej zmiany należy określić całą listę prefiksów, a nie tylko prefiksy, które chcesz zmienić. Określ tylko prefiksy, które chcesz zachować. W tym przypadku są to prefiksy 10.0.0.0/24 i 20.0.0.0/24

```azurecli
az network local-gateway create --gateway-ip-address 23.99.221.164 --name Site2 -g TestRG1 --local-address-prefixes 10.0.0.0/24 20.0.0.0/24
```

### <a name="to-modify-local-network-gateway-ip-address-prefixes---existing-gateway-connection"></a><a name="withconnection"></a>Aby zmodyfikować prefiksy adresów IP bramy sieci lokalnej — istniejące połączenie bramy

Jeśli masz połączenie bramy i chcesz dodać lub usunąć prefiksy adresów IP, możesz zaktualizować prefiksy przy użyciu polecenia [az network local-gateway update](/cli/azure/network/local-gateway). Spowoduje to pewien przestój połączenia sieci VPN. W przypadku modyfikowania prefiksów adresów IP nie musisz usuwać bramy sieci VPN.

Po wprowadzeniu każdej zmiany należy określić całą listę prefiksów, a nie tylko prefiksy, które chcesz zmienić. W tym przykładzie już istnieją prefiksy 10.0.0.0/24 i 20.0.0.0/24. Dodano prefiksy 30.0.0.0/24 i 40.0.0.0/24 oraz określono wszystkie 4 prefiksy podczas aktualizowania.

```azurecli
az network local-gateway update --local-address-prefixes 10.0.0.0/24 20.0.0.0/24 30.0.0.0/24 40.0.0.0/24 --name VNet1toSite2 -g TestRG1
```