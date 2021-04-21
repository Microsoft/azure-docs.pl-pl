---
title: Szybki start — ustawianie & pobierania tajnego z Key Vault przy użyciu programu PowerShell"
description: W tym przewodniku Szybki start dowiesz się, jak tworzyć, pobierać i usuwać wpisy tajne z Azure Key Vault przy użyciu Azure PowerShell.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: mvc, devx-track-azurepowershell
ms.date: 01/27/2021
ms.author: mbaldwin
ms.openlocfilehash: 8a0ebfc90fb57a6e67d7c9e41b78d9db502b2720
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107814644"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-powershell"></a>Szybki start: konfigurowanie i pobieranie wpisów tajnych z usługi Azure Key Vault przy użyciu programu PowerShell

Azure Key Vault to usługa w chmurze, która działa jako bezpieczny magazyn wpisów tajnych. Możesz bezpiecznie przechowywać klucze, hasła, certyfikaty oraz inne wpisy tajne. Aby uzyskać więcej informacji na temat usługi Key Vault, możesz zapoznać się z [omówieniem](../general/overview.md). W tym przewodniku Szybki start opisano tworzenie magazynu kluczy przy użyciu programu PowerShell, a następnie umieszczanie wpisu tajnego w nowo utworzonym magazynie.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować program PowerShell i używać go lokalnie, ten samouczek wymaga modułu Azure PowerShell wersji 5.0.0 lub nowszej. Wpisz polecenie `$PSVersionTable.PSVersion`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzAccount`, aby utworzyć połączenie z platformą Azure.

```azurepowershell-interactive
Connect-AzAccount
```

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

[!INCLUDE [Create a resource group](../../../includes/key-vault-powershell-rg-creation.md)]

## <a name="create-a-key-vault"></a>Tworzenie magazynu kluczy

[!INCLUDE [Create a key vault](../../../includes/key-vault-powershell-kv-creation.md)]

## <a name="give-your-user-account-permissions-to-manage-secrets-in-key-vault"></a>Nadaj kontu użytkownika uprawnienia do zarządzania wpisami tajnymi w Key Vault

Użyj polecenia cmdlet Azure PowerShell Set-AzKeyVaultAccessPolicy , aby zaktualizować zasady Key Vault dostępu i udzielić tajnych uprawnień do konta użytkownika.

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy -VaultName "<your-unique-keyvault-name>" -UserPrincipalName "user@domain.com" -PermissionsToSecrets get,set,delete
```

## <a name="adding-a-secret-to-key-vault"></a>Dodawanie wpisu tajnego do usługi Key Vault

Aby dodać wpis tajny do magazynu, wystarczy tylko wykonać kilka czynności. W tym przypadku dodaj hasło, którego będzie mogła używać aplikacja. Hasło ma nazwę **ExamplePassword** i przechowywana jest w nim wartość **hVFkk965BuUv**.

Najpierw przekonwertuj wartość **hVFkk965BuUv** na bezpieczny ciąg, wpisując polecenie:

```azurepowershell-interactive
$secretvalue = ConvertTo-SecureString "hVFkk965BuUv" -AsPlainText -Force
```

Następnie użyj Azure PowerShell cmdlet [Set-AzKeyVaultSecret,](/powershell/module/az.keyvault/set-azkeyvaultsecret) aby utworzyć w programie Key Vault klucz tajny o nazwie **ExamplePassword** o wartości **hVFkk965BuUv:**


```azurepowershell-interactive
$secret = Set-AzKeyVaultSecret -VaultName "<your-unique-keyvault-name>" -Name "ExamplePassword" -SecretValue $secretvalue
```

## <a name="retrieve-a-secret-from-key-vault"></a>Pobieranie tajnego z Key Vault

Aby wyświetlić wartość zawartą we wpisie tajnym jako zwykły tekst:

```azurepowershell-interactive
$secret = Get-AzKeyVaultSecret -VaultName "<your-unique-keyvault-name>" -Name "ExamplePassword"
$ssPtr = [System.Runtime.InteropServices.Marshal]::SecureStringToBSTR($secret.SecretValue)
try {
   $secretValueText = [System.Runtime.InteropServices.Marshal]::PtrToStringBSTR($ssPtr)
} finally {
   [System.Runtime.InteropServices.Marshal]::ZeroFreeBSTR($ssPtr)
}
Write-Output $secretValueText
```

Utworzono usługę Key Vault, umieszczono w niej wpis tajny i pobrano go.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

 Inne przewodniki szybkiego startu i samouczki w tej kolekcji bazują na tym przewodniku. Jeśli planujesz korzystać z innych przewodników Szybki start i samouczków, pozostaw te zasoby na swoim miejscu.

Gdy grupa zasobów, usługa Key Vault i wszystkie pokrewne zasoby nie będą już potrzebne, można je usunąć za pomocą polecenia [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup).

```azurepowershell-interactive
Remove-AzResourceGroup -Name ContosoResourceGroup
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzono Key Vault i przechowywano w nim klucz tajny. Aby dowiedzieć się więcej Key Vault o tym, jak zintegrować ją z aplikacjami, przejdź do poniższych artykułów.

- Przeczytaj omówienie [Azure Key Vault](../general/overview.md)
- Dowiedz się, jak [przechowywać wieloliniowe wpisy tajne w Key Vault](multiline-secrets.md)
- Zapoznaj się z odwołaniem do [Azure PowerShell Key Vault cmdlet](/powershell/module/az.keyvault/#key_vault)
- Przejrzyj omówienie [Key Vault zabezpieczeń](../general/security-features.md)
