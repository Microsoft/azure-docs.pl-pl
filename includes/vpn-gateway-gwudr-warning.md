---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 09/28/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: cf9d4c3fd96df83361e7d9aa89ba702d37265ec6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "95564311"
---
Trasy zdefiniowane przez użytkownika z lokalizacją docelową 0.0.0.0/0 i sieciowych grup zabezpieczeń na GatewaySubnet **nie są obsługiwane**. Bramy utworzone przy użyciu tej konfiguracji zostaną zablokowane w celu utworzenia. Bramy wymagają dostępu do kontrolerów zarządzania w celu poprawnego działania. [Propagacja trasy BGP](../articles/virtual-network/virtual-networks-udr-overview.md#border-gateway-protocol) powinna być ustawiona na wartość "Enabled" w GatewaySubnet, aby zapewnić dostępność bramy. Jeśli ta wartość jest ustawiona na disabled, brama nie będzie działać.