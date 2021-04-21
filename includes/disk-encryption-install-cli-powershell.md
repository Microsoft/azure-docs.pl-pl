---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: msmbaldwin
ms.service: virtual-machines
ms.topic: include
ms.date: 10/06/2019
ms.author: mbaldwin
ms.custom: include file
ms.openlocfilehash: a550087f552238820ecff079ff1dc2523c4172d3
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776806"
---
Azure Disk Encryption można włączyć i zarządzać nimi za pomocą interfejsu wiersza [polecenia platformy Azure](/cli/azure) [Azure PowerShell](/powershell/azure/new-azureps-module-az). W tym celu należy zainstalować narzędzia lokalnie i połączyć się z subskrypcją platformy Azure.

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Interfejs [wiersza polecenia platformy Azure 2.0](/cli/azure) to narzędzie wiersza polecenia do zarządzania zasobami platformy Azure. Interfejs wiersza polecenia został zaprojektowany w celu elastycznego wykonywania zapytań o dane, obsługi długotrwałych operacji jako procesów nieblokowania i ułatwiania wykonywania skryptów. Możesz zainstalować go lokalnie, korzystając z kroków instalowania [interfejsu wiersza polecenia platformy Azure.](/cli/azure/install-azure-cli)

Aby [zalogować się do konta platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure,](/cli/azure/authenticate-azure-cli)użyj [polecenia az login.](/cli/azure/reference-index#az_login)

```azurecli
az login
```

Jeśli chcesz wybrać dzierżawę, w ramach których chcesz się zalogować, użyj:
    
```azurecli
az login --tenant <tenant>
```

Jeśli masz wiele subskrypcji i chcesz określić określoną subskrypcję, pobierz listę subskrypcji za pomocą az [account list](/cli/azure/account#az_account_list) i określ za pomocą az [account set](/cli/azure/account#az_account_set).
     
```azurecli
az account list
az account set --subscription "<subscription name or ID>"
```

Aby uzyskać więcej informacji, zobacz [Wprowadzenie do interfejsu wiersza polecenia platformy Azure w wersji 2.0.](/cli/azure/get-started-with-azure-cli) 

### <a name="azure-powershell"></a>Azure PowerShell
Moduł [Azure PowerShell az](/powershell/azure/new-azureps-module-az) zawiera zestaw polecenia cmdlet, które Azure Resource Manager modelu zarządzania zasobami platformy Azure. [](../articles/azure-resource-manager/management/overview.md) Można go użyć w przeglądarce z programem [Azure Cloud Shell](../articles/cloud-shell/overview.md)lub zainstalować go na komputerze lokalnym, korzystając z instrukcji w temacie Instalowanie Azure PowerShell [modułu](/powershell/azure/install-az-ps). 

Jeśli masz już zainstalowane lokalnie, upewnij się, że używasz najnowszej wersji zestawu SDK Azure PowerShell, aby skonfigurować Azure Disk Encryption. Pobierz najnowszą wersję [Azure PowerShell wersji](https://github.com/Azure/azure-powershell/releases).

Aby [zalogować się do konta platformy Azure przy użyciu Azure PowerShell](/powershell/azure/authenticate-azureps), użyj polecenia cmdlet [Connect-AzAccount.](/powershell/module/az.accounts/connect-azaccount)

```powershell
Connect-AzAccount
```

Jeśli masz wiele subskrypcji i chcesz określić jedną z nich, użyj polecenia cmdlet [Get-AzSubscription,](/powershell/module/Az.Accounts/Get-AzSubscription) aby je wyświetlić, a następnie polecenia cmdlet [Set-AzContext:](/powershell/module/az.accounts/set-azcontext)

```powershell
Set-AzContext -Subscription -Subscription <SubscriptionId>
```

Uruchomienie polecenia cmdlet [Get-AzContext](/powershell/module/Az.Accounts/Get-AzContext) spowoduje sprawdzenie, czy wybrano poprawną subskrypcję.

Aby potwierdzić, Azure Disk Encryption polecenia cmdlet są zainstalowane, użyj polecenia cmdlet [Get-command:](/powershell/module/microsoft.powershell.core/get-command)
     
```powershell
Get-command *diskencryption*
```
Aby uzyskać więcej informacji, zobacz [Wprowadzenie do Azure PowerShell](/powershell/azure/get-started-azureps).