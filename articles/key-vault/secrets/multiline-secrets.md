---
title: Przechowywanie w Azure Key Vault wielowierszowego klucza tajnego
description: Samouczek przedstawiający sposób ustawiania haseł wielowierszowych z Azure Key Vault przy użyciu interfejsu wiersza polecenia platformy Azure i programu PowerShell
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: mvc, seo-javascript-september2019, seo-javascript-october2019, devx-track-azurecli
ms.date: 03/17/2021
ms.author: mbaldwin
ms.openlocfilehash: e320795d5e8623dea9b97ac6371a0ffc6e6117f1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104610286"
---
# <a name="store-a-multi-line-secret-in-azure-key-vault"></a>Przechowywanie tajemnicy wielowierszowej w Azure Key Vault

[Przewodnik Szybki Start dotyczący interfejsu wiersza polecenia platformy Azure](quick-create-cli.md) i [Azure PowerShell szybki start](quick-create-powershell.md) przedstawia sposób przechowywania klucza tajnego w jednym wierszu.   Key Vault można również użyć do przechowywania klucza tajnego wielowierszowego, takiego jak plik JSON lub klucz prywatny RSA.

Nie można przesłać wpisów tajnych z obsługą wiele wierszy do interfejsu wiersza polecenia platformy Azure [AZ AzKeyVaultSecret Secret set](/cli/azure/keyvault/secret#az_keyvault_secret_set) Azure PowerShell lub polecenia cmdlet [Set-](/powershell/module/az.keyvault/set-azkeyvaultsecret) z wiersza polecenia. Zamiast tego należy najpierw przechowywać wiersz tajny wielowierszowy jako plik tekstowy. 

Na przykład można utworzyć plik tekstowy o nazwie "secretfile.txt" zawierający następujące wiersze:

```bash
This is my
multi-line
secret
```

Następnie można przekazać ten plik do interfejsu wiersza polecenia platformy Azure [AZ klucza tajnego Set](/cli/azure/keyvault/secret#az_keyvault_secret_set) przy użyciu `--file` parametru.

```azurecli-interactive
az keyvault secret set --vault-name "<your-unique-keyvault-name>" --name "MultilineSecret" --file "secretfile.txt"
```

W Azure PowerShell należy najpierw odczytywać w pliku przy użyciu polecenia cmdlet [Get-Content](/powershell/module/microsoft.powershell.management/get-content) , a następnie konwertować je na bezpieczny ciąg za pomocą [ConvertTo-SecureString](/powershell/module/microsoft.powershell.security/convertto-securestring). 

```azurepowershell-interactive
$RawSecret =  Get-Content "secretfile.txt" -Raw
$SecureSecret = ConvertTo-SecureString -String $RawSecret -AsPlainText -Force
```

Na koniec Zapisz wpis tajny za pomocą polecenia cmdlet [Set-AzKeyVaultSecret](/powershell/module/az.keyvault/set-azkeyvaultsecret) .

```azurepowershell-interactive
$secret = Set-AzKeyVaultSecret -VaultName "<your-unique-keyvault-name>" -Name "MultilineSecret" -SecretValue $SecureSecret
```

W obu przypadkach można wyświetlić zapisane tajne hasło przy użyciu interfejsu wiersza polecenia platformy Azure [AZ AzKeyVaultSecret Secret show](/cli/azure/keyvault/secret#az_keyvault_secret_show) , lub Azure PowerShell [Get-](/powershell/module/az.keyvault/get-azkeyvaultsecret) polecenia cmdlet.

```azurecli-interactive
az keyvault secret show --name "MultilineSecret" --vault-name "<your-unique-keyvault-name>" --query "value"
```

Wpis tajny zostanie zwrócony z osadzonym znakiem nowego wiersza:

```bash
"This is\nmy multi-line\nsecret"
```

## <a name="next-steps"></a>Następne kroki

- Zapoznaj się [z omówieniem Azure Key Vault](../general/overview.md)
- Zobacz [Przewodnik Szybki Start dotyczący interfejsu wiersza polecenia platformy Azure](quick-create-cli.md)
- Zobacz [interfejs wiersza polecenia platformy Azure AZ-magazyn Commands](/cli/azure/keyvault)
- Zobacz [Przewodnik Szybki start Azure PowerShell](quick-create-powershell.md)
- Zobacz [Azure PowerShell polecenia cmdlet AZ.](/powershell/module/az.keyvault#key-vault)
