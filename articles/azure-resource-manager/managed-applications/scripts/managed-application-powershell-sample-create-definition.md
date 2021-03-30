---
title: Utwórz definicję aplikacji zarządzanej — Azure PowerShell
description: Zawiera przykładowy skrypt Azure PowerShell, który tworzy definicję aplikacji zarządzanej w ramach subskrypcji platformy Azure.
author: tfitzmac
ms.devlang: powershell
ms.topic: sample
ms.date: 10/27/2017
ms.author: tomfitz
ms.openlocfilehash: b605d63db09b81bfe73eca8f4abd7a38d2325004
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "86056025"
---
# <a name="create-a-managed-application-definition-with-powershell"></a>Tworzenie definicji aplikacji zarządzanej za pomocą programu PowerShell

Ten skrypt publikuje definicję aplikacji zarządzanej w katalogu usług.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Przykładowy skrypt

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../../powershell_scripts/managed-applications/create-definition/create-definition.ps1 "Create definition")]


## <a name="script-explanation"></a>Objaśnienia dla skryptu

Ten skrypt używa następującego polecenia do utworzenia definicji aplikacji zarządzanej. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [New-AzManagedApplicationDefinition](/powershell/module/az.resources/new-azmanagedapplicationdefinition) | Tworzy definicję aplikacji zarządzanej. Należy podać pakiet, który zawiera wymagane pliki. |


## <a name="next-steps"></a>Następne kroki

* Aby zapoznać się z wprowadzeniem do aplikacji zarządzanych, zobacz [Azure Managed Application overview](../overview.md) (Omówienie aplikacji zarządzanych platformy Azure).
* Aby uzyskać więcej informacji na temat programu PowerShell, zobacz [dokumentację programu Azure PowerShell](/powershell/azure/get-started-azureps).
