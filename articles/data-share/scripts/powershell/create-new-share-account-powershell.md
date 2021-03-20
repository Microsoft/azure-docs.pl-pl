---
title: 'Skrypt programu PowerShell: Tworzenie nowego konta udziału danych platformy Azure'
description: Ten skrypt programu PowerShell tworzy nowe konto udostępniania danych.
author: joannapea
ms.service: data-share
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: 4608afc16398a4cd1fa47ee1ae2cd4a4489a3f96
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92221370"
---
# <a name="use-powershell-to-create-a-data-share-account-in-azure"></a>Tworzenie konta udziału danych na platformie Azure przy użyciu programu PowerShell

Ten skrypt programu PowerShell tworzy nowe konto udostępniania danych. 

## <a name="sample-script"></a>Przykładowy skrypt

```powershell
# Set variables with your own values
$resourceGroupName = "<Resource group name"
$location = "<Location>"
$dataShareAccountName = "<Data share account name>"

# Create a new Azure Data Share account
New-AzDataShareAccount -ResourceGroupName $resourceGroupName -Name $dataShareAccountName -Location $location
```

## <a name="script-explanation"></a>Objaśnienia dla skryptu

W tym skrypcie użyto następujących poleceń: 

| Polecenie | Uwagi |
|---|---|
| [New-AzDataShareAccount](/powershell/module/az.datashare/new-azdatashareaccount) | Tworzy konto udostępniania danych. |
|||

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat programu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](/powershell/).

Dodatkowe przykłady skryptów programu PowerShell dotyczące udziałów danych platformy Azure można znaleźć w [przykładach programu PowerShell w udziale danych platformy Azure](../../samples-powershell.md).
