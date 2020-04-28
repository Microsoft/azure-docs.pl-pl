---
title: dołączanie pliku
description: dołączanie pliku
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/17/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: c1b5560e16b68565c37365ac9c2cba217d9b1b90
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/27/2020
ms.locfileid: "67183063"
---
Możesz sprawdzić, czy połączenie powiodło się, używając polecenia cmdlet "Get-AzureVNetConnection".

1. Można skorzystać z następującego przykładu użycia polecenia cmdlet, dopasowując wartości do własnych potrzeb. Nazwa sieci wirtualnej musi znajdować się w cudzysłowie, jeśli zawiera spacje.

   ```azurepowershell
   Get-AzureVNetConnection "Group ClassicRG ClassicVNet"
   ```
2. Po zakończeniu działania polecenia cmdlet sprawdź wartości. W poniższym przykładzie stan połączenia jest wyświetlany jako "połączone" i można zobaczyć bajty przychodzące i wychodzące.

        ConnectivityState         : Connected
        EgressBytesTransferred    : 181664
        IngressBytesTransferred   : 182080
        LastConnectionEstablished : 1/7/2016 12:40:54 AM
        LastEventID               : 24401
        LastEventMessage          : The connectivity state for the local network site 'RMVNetLocal' changed from Connecting to
                                    Connected.
        LastEventTimeStamp        : 1/7/2016 12:40:54 AM
        LocalNetworkSiteName      : RMVNetLocal