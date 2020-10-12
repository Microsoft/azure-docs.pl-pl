---
author: mgoedtel
ms.service: log-analytics
ms.topic: include
ms.date: 11/09/2018
ms.author: magoedte
ms.openlocfilehash: 6890c71ac7c265d46cc77751786fea4d0b228588
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "67183624"
---
### <a name="troubleshoot-azure-diagnostics"></a>Rozwiązywanie problemów z funkcją Diagnostyka Azure

Jeśli zostanie wyświetlony następujący komunikat o błędzie, dostawca zasobów Microsoft.insights nie jest zarejestrowany:

`Failed to update diagnostics for 'resource'. {"code":"Forbidden","message":"Please register the subscription 'subscription id' with Microsoft.Insights."}`

Aby zarejestrować tego dostawcę zasobów, wykonaj następujące czynności w witrynie Azure Portal:

1.  W okienku nawigacji po lewej stronie kliknij pozycję *Subskrypcje*
2.  Wybierz subskrypcję określoną w komunikacie o błędzie
3.  Kliknij pozycję *Dostawcy zasobów*
4.  Odszukaj dostawcę *Microsoft.insights*
5.  Kliknij link *Zarejestruj*

![Rejestrowanie dostawcy zasobów microsoft.insights](./media/log-analytics-troubleshoot-azure-diagnostics/log-analytics-register-microsoft-diagnostics-resource-provider.png)

Po zarejestrowaniu dostawcy zasobów *Microsoft.insights* spróbuj ponownie skonfigurować diagnostykę.


Jeśli zostanie wyświetlony następujący komunikat o błędzie w programie PowerShell, musisz zaktualizować swoją wersję programu PowerShell:

`Set-AzDiagnosticSetting : A parameter cannot be found that matches parameter name 'WorkspaceId'.`

Zaktualizuj swoją wersję Azure PowerShell, postępuj zgodnie z instrukcjami w artykule [instalowanie Azure PowerShell](/powershell/azure/install-az-ps) .
