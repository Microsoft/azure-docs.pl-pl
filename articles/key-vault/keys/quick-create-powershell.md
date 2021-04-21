---
title: Tworzenie i pobieranie atrybutów klucza w Azure Key Vault — Azure PowerShell
description: Przewodnik Szybki start pokazujący, jak ustawić i pobrać klucz z Azure Key Vault przy użyciu Azure PowerShell
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.date: 01/27/2021
ms.author: mbaldwin
ms.openlocfilehash: ede01de815a65aede410fe539b6205f1f4e4fd68
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107815454"
---
# <a name="quickstart-set-and-retrieve-a-key-from-azure-key-vault-using-azure-powershell"></a>Szybki start: konfigurowanie i pobieranie klucza z usługi Azure Key Vault pomocą Azure PowerShell

W tym przewodniku Szybki start utworzysz magazyn kluczy w u Azure Key Vault za pomocą Azure PowerShell. Azure Key Vault to usługa w chmurze, która działa jako bezpieczny magazyn wpisów tajnych. Możesz bezpiecznie przechowywać klucze, hasła, certyfikaty oraz inne wpisy tajne. Aby uzyskać więcej informacji na Key Vault, zapoznaj się z tematem [Omówienie.](../general/overview.md) Azure PowerShell służy do tworzenia zasobów platformy Azure i zarządzania nimi za pomocą poleceń lub skryptów. Po zakończeniu tego procesu będziesz przechowywać klucz.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Jeśli postanowisz zainstalować program PowerShell i używać go lokalnie, to ten samouczek wymaga modułu Azure PowerShell w wersji 1.0.0 lub nowszej. Wpisz polecenie `$PSVersionTable.PSVersion`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Login-AzAccount`, aby utworzyć połączenie z platformą Azure.

```azurepowershell-interactive
Login-AzAccount
```

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

[!INCLUDE [Create a resource group](../../../includes/key-vault-powershell-rg-creation.md)]

## <a name="create-a-key-vault"></a>Tworzenie magazynu kluczy

[!INCLUDE [Create a key vault](../../../includes/key-vault-powershell-kv-creation.md)]

## <a name="add-a-key-to-key-vault"></a>Dodawanie klucza do Key Vault

Aby dodać klucz do magazynu, wystarczy wykonać kilka dodatkowych kroków. Ten klucz może być używany przez aplikację. 

Wpisz poniższe polecenia, aby utworzyć klucz **o nazwie ExampleKey:**

```azurepowershell-interactive
Add-AzKeyVaultKey -VaultName "<your-unique-keyvault-name>" -Name "ExampleKey" -Destination "Software"
```

Teraz możesz odwoływać się do tego klucza, który został dodany do Azure Key Vault przy użyciu jego wartości URI. Użyj **adresu "https://<your-unique-keyvault-name>.vault.azure.net/keys/ExampleKey",** aby uzyskać bieżącą wersję. 

Aby wyświetlić wcześniej zapisany klucz:

```azurepowershell-interactive
Get-AzKeyVaultKey -VaultName "<your-unique-keyvault-name>" -KeyName "ExampleKey"
```

Teraz utworzono nową Key Vault, przechowywano klucz i pobrano go.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

[!INCLUDE [Create a key vault](../../../includes/key-vault-powershell-delete-resources.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzono Key Vault przechowywany w nim certyfikat. Aby dowiedzieć się więcej Key Vault o tym, jak zintegrować ją z aplikacjami, przejdź do poniższych artykułów.

- Przeczytaj omówienie [Azure Key Vault](../general/overview.md)
- Zobacz informacje dotyczące Azure PowerShell Key Vault [cmdlet](/powershell/module/az.keyvault/)
- Przejrzyj omówienie [Key Vault zabezpieczeń](../general/security-features.md)
