---
title: 'Resetowanie obwodu zakończonego niepowodzeniem — ExpressRoute: PowerShell: Azure | Microsoft Docs'
description: Ten artykuł pomaga zresetować obwód ExpressRoute, który jest w stanie niepowodzenia.
services: expressroute
author: anzaman
ms.service: expressroute
ms.topic: how-to
ms.date: 11/28/2018
ms.author: anzaman
ms.custom: seodec18
ms.openlocfilehash: f75d9ae2fc788bbeb769aaf412c77dfacec9d04c
ms.sourcegitcommit: c4ad4ba9c9aaed81dfab9ca2cc744930abd91298
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/12/2020
ms.locfileid: "84738128"
---
# <a name="reset-a-failed-expressroute-circuit"></a>Resetowanie błędnego obwodu usługi ExpressRoute

Gdy operacja w obwodzie obwodu ExpressRoute nie zostanie zakończona pomyślnie, obwód może przejść do stanu "Niepowodzenie". Ten artykuł pomaga zresetować niepowodzenie obwodu usługi Azure ExpressRoute.

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
