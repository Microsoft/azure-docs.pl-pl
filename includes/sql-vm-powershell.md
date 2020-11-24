---
author: MikeRayMSFT
ms.service: virtual-machines-sql
ms.topic: include
ms.date: 11/25/2018
ms.author: mikeray
ms.openlocfilehash: 27da28073b01c6bc43868172d6c989d9518d8f53
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95563938"
---
## <a name="start-your-powershell-session"></a>Uruchamianie sesji programu PowerShell
 

Uruchom polecenie cmdlet [**Connect-AZ Account**](/powershell/module/Az.Accounts/Connect-AzAccount) i zostanie wyświetlony ekran logowania służący do wprowadzania poświadczeń. Użyj tych samych poświadczeń, których używasz do logowania w witrynie Azure Portal.

```powershell
Connect-AzAccount
```

Jeśli masz wiele subskrypcji, użyj polecenia cmdlet [**Set-AzContext**](/powershell/module/az.accounts/set-azcontext) , aby wybrać subskrypcję, która ma być używana przez sesję programu PowerShell. Aby sprawdzić subskrypcję używaną przez bieżącą sesję programu PowerShell, uruchom polecenie [**Get-AzContext**](/powershell/module/az.accounts/get-azcontext). Aby wyświetlić wszystkie subskrypcje, uruchom polecenie [**Get-AzSubscription**](/powershell/module/az.accounts/get-azsubscription).

```powershell
Set-AzContext -SubscriptionId '4cac86b0-1e56-bbbb-aaaa-000000000000'
```