---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/29/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 24d146da7946176c92902698d0f52ae01baf79ee
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93061641"
---
Jeśli używasz programu PowerShell lokalnie, Otwórz konsolę programu PowerShell z podwyższonym poziomem uprawnień i Połącz się z kontem platformy Azure. Polecenie cmdlet *Connect-AzAccount* poprosi o poświadczenia. Po uwierzytelnieniu program pobiera Twoje ustawienia konta, aby były dostępne do Azure PowerShell.

Jeśli używasz Azure Cloud Shell zamiast lokalnego uruchamiania programu PowerShell, Zauważ, że nie musisz uruchamiać polecenia *Connect-AzAccount*. Azure Cloud Shell nawiązuje połączenie z kontem platformy Azure automatycznie po wybraniu **tej** opcji.

1. Jeśli używasz programu PowerShell lokalnie, zaloguj się.

   ```azurepowershell
   Connect-AzAccount
   ```

1. Jeśli masz więcej niż jedną subskrypcję, uzyskaj listę subskrypcji platformy Azure.

   ```azurepowershell-interactive
   Get-AzSubscription
   ```

1. Wskaż subskrypcję, której chcesz użyć.

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionName "Name of subscription"
   ```
