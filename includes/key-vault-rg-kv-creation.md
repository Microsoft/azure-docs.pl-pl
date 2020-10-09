---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 07/20/2020
ms.author: msmbaldwin
ms.openlocfilehash: 2917854da679816796a7f7748c3877c062995168
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87512814"
---
Ten przewodnik Szybki Start używa wstępnie utworzonego magazynu kluczy platformy Azure. Magazyn kluczy można utworzyć, wykonując czynności opisane w [przewodniku szybki start dotyczącego interfejsu wiersza polecenia platformy Azure](/azure/key-vault/general/quick-create-cli), [Azure PowerShell szybki start](/azure/key-vault/general/quick-create-powershell)lub [Azure Portal przewodniku szybki start](/azure/key-vault/general/quick-create-portal). 

Alternatywnie możesz po prostu uruchomić polecenie interfejsu wiersza polecenia platformy Azure lub Azure PowerShell poniżej.

> [!Important]
> Każdy Magazyn kluczy musi mieć unikatową nazwę. Zastąp <unikatowym identyfikatorem magazynu kluczy> nazwą magazynu klucza w poniższych przykładach.

```azurecli
az group create --name "myResourceGroup" -l "EastUS"

az keyvault create --name "<your-unique-keyvault-name>" -g "myResourceGroup"
```

```azurepowershell
New-AzResourceGroup -Name myResourceGroup -Location EastUS

New-AzKeyVault -Name "<your-unique-keyvault-name>" -ResourceGroupName "myResourceGroup" -Location "EastUS"
```