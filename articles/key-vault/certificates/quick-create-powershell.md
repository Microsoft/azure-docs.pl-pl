---
title: Szybki start — ustawianie & wyświetlania Azure Key Vault certyfikatów przy użyciu Azure PowerShell
description: Przewodnik Szybki start przedstawiający sposób ustawienia i pobrania certyfikatu z usługi Azure Key Vault użyciu Azure PowerShell
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: quickstart
ms.custom: mvc, seo-javascript-september2019, seo-javascript-october2019, devx-track-azurepowershell
ms.date: 01/27/2021
ms.author: mbaldwin
ms.openlocfilehash: a641ca1206cb41ded0513db72daa278dc3753c85
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107750396"
---
# <a name="quickstart-set-and-retrieve-a-certificate-from-azure-key-vault-using-azure-powershell"></a>Szybki start: konfigurowanie i pobieranie certyfikatu z usługi Azure Key Vault użyciu Azure PowerShell

W tym przewodniku Szybki start utworzysz magazyn kluczy w Azure Key Vault przy użyciu Azure PowerShell. Azure Key Vault to usługa w chmurze, która działa jako bezpieczny magazyn wpisów tajnych. Możesz bezpiecznie przechowywać klucze, hasła, certyfikaty oraz inne wpisy tajne. Aby uzyskać więcej informacji na Key Vault, zapoznaj się z tematem [Omówienie.](../general/overview.md) Azure PowerShell służy do tworzenia zasobów platformy Azure i zarządzania nimi za pomocą poleceń lub skryptów. Po zakończeniu tego procesu certyfikat zostanie przechowynyny.

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

## <a name="add-a-certificate-to-key-vault"></a>Dodawanie certyfikatu do Key Vault

Aby dodać certyfikat do magazynu, wystarczy wykonać kilka dodatkowych czynności. Ten certyfikat może być używany przez aplikację. 

Wpisz poniższe polecenia, aby utworzyć certyfikat z podpisem własnym z zasadami o **nazwie ExampleCertificate** :

```azurepowershell-interactive
$Policy = New-AzKeyVaultCertificatePolicy -SecretContentType "application/x-pkcs12" -SubjectName "CN=contoso.com" -IssuerName "Self" -ValidityInMonths 6 -ReuseKeyOnRenewal

Add-AzKeyVaultCertificate -VaultName "<your-unique-keyvault-name>" -Name "ExampleCertificate" -CertificatePolicy $Policy
```

Teraz możesz odwoływać się do tego certyfikatu, który został dodany do Azure Key Vault przy użyciu jego wartości URI. Użyj **adresu "https://<your-unique-keyvault-name>.vault.azure.net/certificates/ExampleCertificate",** aby uzyskać bieżącą wersję. 

Aby wyświetlić wcześniej zapisany certyfikat:

```azurepowershell-interactive
Get-AzKeyVaultCertificate -VaultName "<your-unique-keyvault-name>" -Name "ExampleCertificate"
```

Teraz utworzono nową Key Vault, przechowywano certyfikat i pobrano go.

**Rozwiązywanie problemów:**

Operacja zwróciła nieprawidłowy kod stanu "Zabronione"

Jeśli wystąpi ten błąd, konto, do Azure Key Vault nie ma odpowiednich uprawnień do tworzenia certyfikatów.

Uruchom następujące polecenie Azure PowerShell, aby przypisać odpowiednie uprawnienia:

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy -VaultName <KeyVaultName> -ObjectId <AzureObjectID> -PermissionsToCertificates get,list,update,create
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

[!INCLUDE [Create a key vault](../../../includes/key-vault-powershell-delete-resources.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzono Key Vault przechowywany w nim certyfikat. Aby dowiedzieć się więcej Key Vault o tym, jak zintegrować ją z aplikacjami, przejdź do poniższych artykułów.

- Przeczytaj omówienie [Azure Key Vault](../general/overview.md)
- Zobacz informacje dotyczące Azure PowerShell Key Vault [cmdlet](/powershell/module/az.keyvault/)
- Przejrzyj omówienie [Key Vault zabezpieczeń](../general/security-overview.md)
