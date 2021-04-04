---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/19/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 4e958026b1167d65f47bddbe5e89ec4d6fed7ee3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92217964"
---
Możesz sprawdzić, czy połączenie powiodło się, używając polecenia [az network vpn-connection show](/cli/azure/network/vpn-connection). W podanym przykładzie opcja „--name” odnosi się do nazwy połączenia, które ma zostać przetestowane. Gdy proces nawiązywania połączenia trwa, jego stan połączenia to „Łączenie”. Gdy połączenie zostanie nawiązane, jego stan zmienia się na „Połączone”.

```azurecli-interactive
az network vpn-connection show --name VNet1toSite2 --resource-group TestRG1
```
