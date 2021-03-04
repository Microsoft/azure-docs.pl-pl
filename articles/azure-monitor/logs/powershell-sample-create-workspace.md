---
title: Utwórz obszar roboczy Log Analytics — Azure PowerShell
description: Przykładowy skrypt programu Azure PowerShell — tworzenie obszaru roboczego usługi Log Analytics
ms.topic: sample
author: bwren
ms.author: bwren
ms.date: 09/07/2017
ms.openlocfilehash: c04eb177eb8c52464be2ab7702a365d7a7b54e07
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102040944"
---
# <a name="create-a-log-analytics-workspace-with-powershell"></a>Tworzenie obszaru roboczego usługi Log Analytics przy użyciu programu PowerShell

Ten skrypt szybko tworzy i konfiguruje obszar roboczy usługi Azure Log Analytics. Jest on wymagany, jeśli chcesz rozpocząć zbieranie i analizowanie danych oraz wykonywanie na nich działań.  

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Przykładowy skrypt

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../powershell_scripts/log-analytics/log-analytics-create-new-resource/log-analytics-create-new-resource.ps1 "Create new Log Analytics workspace")]

## <a name="script-explanation"></a>Objaśnienia dla skryptu

Ten skrypt korzysta z poniższych poleceń w celu utworzenia nowego obszaru roboczego usługi Log Analytics w ramach subskrypcji. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [Get-AzOperationalInsightsWorkspace](/powershell/module/az.operationalinsights/get-azoperationalinsightsworkspace) | Pobiera informacje o istniejącym obszarze roboczym. |
| [New-AzOperationalInsightsWorkspace](/powershell/module/az.operationalinsights/new-azoperationalinsightsworkspace) | Tworzy obszar roboczy w określonej grupie zasobów i lokalizacji. |


## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat modułu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](/powershell/azure/).

