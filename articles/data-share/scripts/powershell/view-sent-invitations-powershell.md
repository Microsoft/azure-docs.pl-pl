---
title: 'Skrypt programu PowerShell: Wyświetlanie listy zaproszeń udziału danych platformy Azure wysyłanych do konsumenta'
description: Dowiedz się, jak ten skrypt programu PowerShell otrzymuje zaproszenia wysyłane do konsumenta i zobacz przykład skryptu, którego można użyć.
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: article
ms.date: 07/07/2019
ms.openlocfilehash: 5f75894094fa2a15dbc9e2809ed6c3631df96c3f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92221217"
---
# <a name="use-powershell-to-get-a-data-share-invitation"></a>Pobieranie zaproszenia do udziału danych przy użyciu programu PowerShell

Ten skrypt programu PowerShell otrzymuje zaproszenia wysyłane do konsumenta.

## <a name="sample-script"></a>Przykładowy skrypt
```powershell
# Set variables with your own values
$invitationId = "<invitationId>"
$location = "<location>"

#List invitations sent to a consumer
Get-AzDataShareInvitation

#Get a specific invitation sent to a consumer
Get-AzDataShareInvitation -location -invitationId 

```

## <a name="script-explanation"></a>Objaśnienia dla skryptu

W tym skrypcie użyto następujących poleceń: 

| Polecenie | Uwagi |
|---|---|
| [Get-AzDataShareInvitation](/powershell/module/az.datashare/get-azdatashareinvitation) | Uzyskaj i wyświetlaj wysłane zaproszenia do udziału danych. |
|||

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat programu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](/powershell/).

Dodatkowe przykłady skryptów programu PowerShell dotyczące udziałów danych platformy Azure można znaleźć w [przykładach programu PowerShell w udziale danych platformy Azure](../../samples-powershell.md).
