---
title: Pobierz zarządzaną grupę zasobów & zmienić rozmiaru maszyn wirtualnych — Azure PowerShell
description: Zawiera Azure PowerShell przykładowy skrypt, który pobiera zarządzaną grupę zasobów dla aplikacji zarządzanej platformy Azure. Skrypt zmienia rozmiar maszyn wirtualnych.
author: tfitzmac
ms.devlang: powershell
ms.topic: sample
ms.date: 10/27/2017
ms.author: tomfitz
ms.openlocfilehash: 695023f4700370cbe5e9b345f513e38d1cf1fc0c
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "75650184"
---
# <a name="get-resources-in-a-managed-resource-group-and-resize-vms-with-powershell"></a>Pobieranie zasobów z zarządzanej grupy zasobów i zmienianie rozmiaru maszyn wirtualnych za pomocą programu PowerShell

Ten skrypt pobiera zasoby z zarządzanej grupy zasobów i zmienia rozmiar maszyn wirtualnych w tej grupie zasobów.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Przykładowy skrypt

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../../powershell_scripts/managed-applications/get-application/get-application.ps1 "Get application")]


## <a name="script-explanation"></a>Objaśnienia dla skryptu

Ten skrypt używa następujących poleceń do wdrożenia aplikacji zarządzanej. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [Get-AzManagedApplication](https://docs.microsoft.com/powershell/module/az.resources/get-azmanagedapplication) | Wyświetla listę aplikacji zarządzanych. Należy podać nazwę grupy zasobów, aby zawęzić wyniki. |
| [Get-AzResource](https://docs.microsoft.com/powershell/module/az.resources/get-azresource) | Wyświetla listę zasobów. Należy podać grupę zasobów i typ zasobu, aby zawęzić wyniki. |
| [Update-AzVM](https://docs.microsoft.com/powershell/module/az.compute/update-azvm) | Aktualizuje rozmiar maszyny wirtualnej. |


## <a name="next-steps"></a>Następne kroki

* Aby zapoznać się z wprowadzeniem do aplikacji zarządzanych, zobacz [Azure Managed Application overview](../overview.md) (Omówienie aplikacji zarządzanych platformy Azure).
* Aby uzyskać więcej informacji na temat programu PowerShell, zobacz [dokumentację programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).
