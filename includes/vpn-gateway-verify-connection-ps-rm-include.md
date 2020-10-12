---
title: plik dołączany
description: plik dołączany
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/17/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 49f48c2d0bf865cf8c8fde831e7a597f8701d213
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "67183059"
---
Możesz sprawdzić, czy połączenie powiodło się, używając polecenia cmdlet "Get-AzVirtualNetworkGatewayConnection" z opcją "-debug" lub bez niej. 

1. Można skorzystać z następującego przykładu użycia polecenia cmdlet, dopasowując wartości do własnych potrzeb. Po wyświetleniu monitu wybierz „A”, aby uruchomić wszystko. W podanym przykładzie opcja „-Name” odnosi się do nazwy połączenia, które ma zostać przetestowane.

   ```azurepowershell-interactive
   Get-AzVirtualNetworkGatewayConnection -Name VNet1toSite1 -ResourceGroupName TestRG1
   ```
2. Po zakończeniu działania polecenia cmdlet sprawdź wartości. W poniższym przykładzie stan połączenia jest wyświetlany jako „Połączone” i można zobaczyć bajty przychodzące i wychodzące.
   
   ```
   "connectionStatus": "Connected",
   "ingressBytesTransferred": 33509044,
   "egressBytesTransferred": 4142431
   ```