---
title: 'Skrypt programu PowerShell: Lista zaproszeń udziału danych platformy Azure wysyłanych do konsumenta | Microsoft Docs'
description: Dowiedz się, jak ten skrypt programu PowerShell otrzymuje zaproszenia wysyłane do konsumenta i zobacz przykład skryptu, którego można użyć.
services: data-share
author: joannapea
ms.service: data-share
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: 3f0374ba5b98c145359d18143584e23fc543c552
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90985618"
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
| [Get-AzDataShareInvitation](/powershell/module/az.datashare/get-azdatashareinvitation?view=azps-2.6.0) | Uzyskaj i wyświetlaj wysłane zaproszenia do udziału danych. |
|||

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat programu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](https://docs.microsoft.com/powershell/).

Dodatkowe przykłady skryptów programu PowerShell dotyczące udziałów danych platformy Azure można znaleźć w [przykładach programu PowerShell w udziale danych platformy Azure](../../samples-powershell.md).
