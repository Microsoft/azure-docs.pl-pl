---
title: 'Skrypt programu PowerShell: Akceptowanie zaproszenia z udziału danych platformy Azure'
description: Ten skrypt programu PowerShell akceptuje zaproszenia z istniejącego udziału danych.
services: data-share
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: article
ms.date: 07/07/2019
ms.openlocfilehash: 24091bafac7ad5c558b975d52064f12715b3d622
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92221404"
---
# <a name="use-powershell-to-accept-a-data-share-invitation"></a>Akceptowanie zaproszenia udziału danych przy użyciu programu PowerShell

Ten skrypt programu PowerShell akceptuje zaproszenia wysyłane do konsumenta.

## <a name="sample-script"></a>Przykładowy skrypt
```powershell
#List invitations sent to a consumer
Get-AzDataShareInvitation

# Set variables with your own values
$resourceGroupName = "<Resource group name>"
$dataShareAccountName = "<Data share account name>"
$dataShareName = "<Data share name>"
$invitationId = "<Invitation id>"

#Accept a specific invitation by creating a share subscription
New-AzDataShareSubscription -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName -Name $dataShareName -InvitationId $invitationId

```

## <a name="script-explanation"></a>Objaśnienia dla skryptu

W tym skrypcie użyto następujących poleceń: 

| Polecenie | Uwagi |
|---|---|
| [Get-AzDataShareInvitation](/powershell/module/az.datashare/get-azdatashareinvitation) | Uzyskaj i wyświetlaj wysłane zaproszenia do udziału danych. |
| [New-AzDataShareSubscription](/powershell/module/az.datashare/get-azdatasharesubscription) | Utwórz subskrypcję udziału danych. |
|||

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat programu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](/powershell/).

Dodatkowe przykłady skryptów programu PowerShell dotyczące udziałów danych platformy Azure można znaleźć w [przykładach programu PowerShell w udziale danych platformy Azure](../../samples-powershell.md).
