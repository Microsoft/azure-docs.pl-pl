---
author: MikeRayMSFT
ms.service: virtual-machines-sql
ms.topic: include
ms.date: 11/25/2018
ms.author: mikeray
ms.openlocfilehash: c6666f4417cde9e0f77cc965ded1d6bdb5dced34
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/26/2020
ms.locfileid: "67183189"
---
## <a name="start-your-powershell-session"></a>Uruchamianie sesji programu PowerShell
 

Uruchom polecenie cmdlet [**Connect-AZ Account**](https://docs.microsoft.com/powershell/module/Az.Accounts/Connect-AzAccount) i zostanie wyświetlony ekran logowania służący do wprowadzania poświadczeń. Użyj tych samych poświadczeń, których używasz do logowania w witrynie Azure Portal.

```powershell
Connect-AzAccount
```

Jeśli masz wiele subskrypcji, użyj polecenia cmdlet [**Set-AzContext**](https://docs.microsoft.com/powershell/module/az.accounts/set-azcontext) , aby wybrać subskrypcję, która ma być używana przez sesję programu PowerShell. Aby sprawdzić subskrypcję używaną przez bieżącą sesję programu PowerShell, uruchom polecenie [**Get-AzContext**](https://docs.microsoft.com/powershell/module/az.accounts/get-azcontext). Aby wyświetlić wszystkie subskrypcje, uruchom polecenie [**Get-AzSubscription**](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription).

```powershell
Set-AzContext -SubscriptionId '4cac86b0-1e56-bbbb-aaaa-000000000000'
```

