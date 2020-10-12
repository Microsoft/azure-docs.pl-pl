---
title: 'Skrypt programu PowerShell: Tworzenie zaproszenia do udziału danych platformy Azure | Microsoft Docs'
description: Ten skrypt programu PowerShell wysyła zaproszenie do udziału danych.
services: data-share
author: joannapea
ms.service: data-share
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: 220677df3bd0c221f61efd28a80b05cfd76e2734
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87086646"
---
# <a name="use-a-powershell-script-to-monitor-the-usage-of-a-sent-data-share"></a>Używanie skryptu programu PowerShell do monitorowania użycia wysłanego udziału danych

Ten skrypt programu PowerShell tworzy zaproszenie do udziału danych.

## <a name="sample-script"></a>Przykładowy skrypt


```powershell
# Set variables with your own values
$resourceGroupName = "<Resource group name>"
$dataShareAccountName = "<Data share account name>"
$dataShareName = "<Data share name>"
$targetEmail = "<Target email>"

# Send a data share invitation
New-AzDataShareInvitation -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName -ShareName $dataShareName -Name $dataShareName -TargetEmail $targetEmail

```


## <a name="script-explanation"></a>Objaśnienia dla skryptu

W tym skrypcie użyto następujących poleceń: 

| Polecenie | Uwagi |
|---|---|
| [New-AzDataShareInvitation](/powershell/module/az.datashare/new-azdatashareinvitation?view=azps-2.6.0) | Utwórz zaproszenie do udziału danych. |
|||

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat programu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](https://docs.microsoft.com/powershell/).

Dodatkowe przykłady skryptów programu PowerShell dotyczące udziałów danych platformy Azure można znaleźć w [przykładach programu PowerShell w udziale danych platformy Azure](../../samples-powershell.md).
