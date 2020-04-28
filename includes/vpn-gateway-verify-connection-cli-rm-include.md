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
ms.openlocfilehash: 0e009354e66ab13cdb9fbc3cf9e4b37e904bdfd1
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/27/2020
ms.locfileid: "67183061"
---
Możesz sprawdzić, czy połączenie powiodło się, używając polecenia [az network vpn-connection show](/cli/azure/network/vpn-connection). W podanym przykładzie opcja „--name” odnosi się do nazwy połączenia, które ma zostać przetestowane. Gdy proces nawiązywania połączenia trwa, jego stan połączenia to „Łączenie”. Gdy połączenie zostanie nawiązane, jego stan zmienia się na „Połączone”.

```azurecli
az network vpn-connection show --name VNet1toSite2 --resource-group TestRG1
```
