---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: msmbaldwin
ms.service: virtual-machines
ms.topic: include
ms.date: 10/06/2019
ms.author: mbaldwin
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: be6de283ed230a6e6a6b4986abb0a36386e36925
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102211995"
---
Azure Disk Encryption można włączać i zarządzać nimi za pomocą [interfejsu wiersza polecenia platformy Azure](/cli/azure) i [Azure PowerShell](/powershell/azure/new-azureps-module-az). Aby to zrobić, należy zainstalować narzędzia lokalnie i połączyć się z subskrypcją platformy Azure.

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Interfejs wiersza polecenia [platformy azure 2,0](/cli/azure) to narzędzie z wierszem poleceń do zarządzania zasobami platformy Azure. Interfejs wiersza polecenia jest przeznaczony do elastycznego wykonywania zapytań dotyczących danych, obsługuje długotrwałe operacje jako procesy nieblokujące i ułatwiają wykonywanie skryptów. Możesz zainstalować ją lokalnie, wykonując kroki opisane w temacie [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

Aby [zalogować się do konta platformy Azure za pomocą interfejsu wiersza polecenia platformy Azure](/cli/azure/authenticate-azure-cli), użyj polecenia [AZ login](/cli/azure/reference-index#az-login) .

```azurecli
az login
```

Jeśli chcesz wybrać dzierżawcę do logowania, użyj:
    
```azurecli
az login --tenant <tenant>
```

Jeśli masz wiele subskrypcji i chcesz określić konkretną, Pobierz listę subskrypcji za pomocą opcji [AZ Account List](/cli/azure/account#az-account-list) i określ polecenie [AZ Account Set](/cli/azure/account#az-account-set).
     
```azurecli
az account list
az account set --subscription "<subscription name or ID>"
```

Aby uzyskać więcej informacji, zobacz Rozpoczynanie [pracy z interfejsem wiersza polecenia platformy Azure 2,0](/cli/azure/get-started-with-azure-cli). 

### <a name="azure-powershell"></a>Azure PowerShell
[Azure PowerShell AZ module](/powershell/azure/new-azureps-module-az) udostępnia zestaw poleceń cmdlet, które używają modelu [Azure Resource Manager](../articles/azure-resource-manager/management/overview.md) do zarządzania zasobami platformy Azure. Możesz użyć jej w przeglądarce z [Azure Cloud Shell](../articles/cloud-shell/overview.md)lub zainstalować ją na komputerze lokalnym, korzystając z instrukcji w temacie [Install the Azure PowerShell module](/powershell/azure/install-az-ps). 

Jeśli masz już zainstalowany lokalnie, upewnij się, że korzystasz z najnowszej wersji zestawu SDK Azure PowerShell, aby skonfigurować Azure Disk Encryption. Pobierz najnowszą wersję [Azure PowerShell wydania](https://github.com/Azure/azure-powershell/releases).

Aby [zalogować się do konta platformy Azure przy użyciu Azure PowerShell](/powershell/azure/authenticate-azureps?view=azps-2.5.0), użyj polecenia cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) .

```powershell
Connect-AzAccount
```

Jeśli masz wiele subskrypcji i chcesz określić ją, użyj polecenia cmdlet [Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription) , aby je wyświetlić, a następnie polecenie cmdlet [Set-AzContext](/powershell/module/az.accounts/set-azcontext?view=azps-2.5.0) :

```powershell
Set-AzContext -Subscription -Subscription <SubscriptionId>
```

Uruchomienie polecenia cmdlet [Get-AzContext](/powershell/module/Az.Accounts/Get-AzContext) sprawdzi, czy wybrano poprawną subskrypcję.

Aby potwierdzić, że Azure Disk Encryption polecenia cmdlet są zainstalowane, użyj polecenia cmdlet [Get-Command](/powershell/module/microsoft.powershell.core/get-command?view=powershell-6) :
     
```powershell
Get-command *diskencryption*
```
Aby uzyskać więcej informacji, zobacz [wprowadzenie do Azure PowerShell](/powershell/azure/get-started-azureps).