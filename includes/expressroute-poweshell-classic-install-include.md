---
author: cherylmc
ms.date: 12/06/2019
ms.service: expressroute
ms.topic: include
ms.author: cherylmc
ms.openlocfilehash: f04861d55c9cea3c79f4983f7be2e1f5a3c6d864
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96018175"
---
Zainstaluj najnowsze wersje modułów programu PowerShell dla usługi Azure Service Management (SM) i modułu ExpressRoute. Do uruchamiania modułów SM nie można używać środowiska CloudShell platformy Azure.

1. Aby zainstalować moduł zarządzania usługami platformy Azure, wykonaj instrukcje opisane w artykule [Instalowanie modułu zarządzania usługami](/powershell/azure/servicemanagement/install-azure-ps) . Jeśli masz już zainstalowany moduł AZ lub RM, upewnij się, że używasz elementu "-AllowClobber".
2. Zaimportuj zainstalowane moduły. W poniższym przykładzie Dostosuj ścieżkę, aby odzwierciedlić lokalizację i wersję zainstalowanych modułów programu PowerShell.

   ```powershell
   Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.3.0\Azure.psd1'
   Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.3.0\ExpressRoute\ExpressRoute.psd1'
   ```
3. Aby zalogować się do konta platformy Azure, Otwórz konsolę programu PowerShell z podwyższonym poziomem uprawnień i Połącz się ze swoim kontem. Skorzystaj z następującego przykładu, aby nawiązać połączenie przy użyciu modułu zarządzania usługami:

   ```powershell
   Add-AzureAccount
   ```