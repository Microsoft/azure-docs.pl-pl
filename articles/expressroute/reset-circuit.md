---
title: 'Resetowanie obwodu zakończonego niepowodzeniem — ExpressRoute: PowerShell: Azure | Microsoft Docs'
description: Ten artykuł pomaga zresetować obwód ExpressRoute, który jest w stanie niepowodzenia.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 01/07/2021
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: 84c8275b7a7257530a735e8612047ef42e2f8a7c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98011343"
---
# <a name="reset-a-failed-expressroute-circuit"></a>Resetowanie błędnego obwodu usługi ExpressRoute

Gdy operacja w obwodzie obwodu ExpressRoute nie zakończy się pomyślnie, obwód może przejść do stanu "Niepowodzenie". Ten artykuł pomoże Ci zresetować niepowodzenie obwodu usługi Azure ExpressRoute.

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

## <a name="reset-a-circuit"></a>Zresetuj obwód

1. Zainstaluj najnowszą wersję poleceń cmdlet programu PowerShell usługi Azure Resource Manager. Aby uzyskać więcej informacji, zobacz [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/install-az-ps).

2. Otwórz konsolę programu PowerShell z podwyższonym poziomem uprawnień i połącz się ze swoim kontem. Użyj poniższego przykładu w celu łatwiejszego nawiązania połączenia:

   ```azurepowershell-interactive
   Connect-AzAccount
   ```
3. Jeśli masz wiele subskrypcji platformy Azure, wyświetl subskrypcje dla konta.

   ```azurepowershell-interactive
   Get-AzSubscription
   ```
4. Wskaż subskrypcję, której chcesz użyć.

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionName "Replace_with_your_subscription_name"
   ```
5. Uruchom następujące polecenia, aby zresetować obwód, który jest w stanie niepowodzenia:

   ```azurepowershell-interactive
   $ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

Obwód powinien teraz być w dobrej kondycji. Otwórz bilet pomocy technicznej w ramach [pomocy technicznej firmy Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) , jeśli obwód nadal jest w stanie awarii.

## <a name="next-steps"></a>Następne kroki

Otwórz bilet pomocy technicznej w [pomocy technicznej firmy Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) , jeśli nadal występują problemy.
