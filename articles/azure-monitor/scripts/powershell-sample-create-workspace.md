---
title: Utwórz obszar roboczy Log Analytics — Azure PowerShell
description: Przykładowy skrypt programu Azure PowerShell — tworzenie obszaru roboczego usługi Log Analytics
ms.subservice: logs
ms.topic: sample
author: bwren
ms.author: bwren
ms.date: 09/07/2017
ms.openlocfilehash: 62b02de5d1c08f6047052d71e3be420cceb1c5c0
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80054626"
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

Aby uzyskać więcej informacji na temat modułu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](/powershell/azure/overview).

