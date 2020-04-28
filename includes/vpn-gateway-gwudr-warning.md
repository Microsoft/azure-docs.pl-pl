---
title: dołączanie pliku
description: dołączanie pliku
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 09/28/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 97fde67c3ac7649418ed0239a2c7aa4f1a4b3f96
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81275334"
---
Trasy zdefiniowane przez użytkownika z lokalizacją docelową 0.0.0.0/0 i sieciowych grup zabezpieczeń na GatewaySubnet **nie są obsługiwane**. Bramy utworzone przy użyciu tej konfiguracji zostaną zablokowane w celu utworzenia. Bramy wymagają dostępu do kontrolerów zarządzania w celu poprawnego działania. [Propagacja trasy BGP](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#border-gateway-protocol) powinna być ustawiona na wartość "Enabled" w GatewaySubnet, aby zapewnić dostępność bramy. Jeśli ta wartość jest wyłączona, Brama nie będzie działać.
