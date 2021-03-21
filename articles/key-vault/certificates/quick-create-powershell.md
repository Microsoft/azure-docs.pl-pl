---
title: Szybki Start — Ustawianie & wyświetlania certyfikatów Azure Key Vault za pomocą Azure PowerShell
description: Przewodnik Szybki Start przedstawiający sposób ustawiania i pobierania certyfikatu z Azure Key Vault przy użyciu Azure PowerShell
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: quickstart
ms.custom: mvc, seo-javascript-september2019, seo-javascript-october2019
ms.date: 01/27/2021
ms.author: mbaldwin
ms.openlocfilehash: 587815cf9628df35f1e1efdbc6a7a3c89a27ed55
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99071921"
---
# <a name="quickstart-set-and-retrieve-a-certificate-from-azure-key-vault-using-azure-powershell"></a>Szybki Start: Ustawianie i pobieranie certyfikatu z Azure Key Vault przy użyciu Azure PowerShell

W tym przewodniku szybki start utworzysz Magazyn kluczy w Azure Key Vault z Azure PowerShell. Azure Key Vault to usługa w chmurze, która działa jako bezpieczny magazyn wpisów tajnych. Możesz bezpiecznie przechowywać klucze, hasła, certyfikaty oraz inne wpisy tajne. Aby uzyskać więcej informacji na Key Vault można zapoznać się z [omówieniem](../general/overview.md). Azure PowerShell służy do tworzenia zasobów platformy Azure i zarządzania nimi przy użyciu poleceń lub skryptów. Po zakończeniu tej operacji certyfikat zostanie zapisany.

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

Aby dodać certyfikat do magazynu, wystarczy wykonać kilka dodatkowych kroków. Ten certyfikat może być używany przez aplikację. 

Wpisz poniższe polecenia, aby utworzyć certyfikat z podpisem własnym przy użyciu zasad o nazwie **ExampleCertificate** :

```azurepowershell-interactive
$Policy = New-AzKeyVaultCertificatePolicy -SecretContentType "application/x-pkcs12" -SubjectName "CN=contoso.com" -IssuerName "Self" -ValidityInMonths 6 -ReuseKeyOnRenewal

Add-AzKeyVaultCertificate -VaultName "<your-unique-keyvault-name>" -Name "ExampleCertificate" -CertificatePolicy $Policy
```

Teraz można odwołać się do tego certyfikatu, który został dodany do Azure Key Vault przy użyciu identyfikatora URI. Aby uzyskać aktualną wersję, użyj **nazwy "https://<identyfikator magazynu unikatowych kluczy>. Vault.Azure.NET/Certificates/ExampleCertificate".** 

Aby wyświetlić poprzednio zapisany certyfikat:

```azurepowershell-interactive
Get-AzKeyVaultCertificate -VaultName "<your-unique-keyvault-name>" -Name "ExampleCertificate"
```

Teraz utworzono Key Vault, Zapisano certyfikat i pobieramy go.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

[!INCLUDE [Create a key vault](../../../includes/key-vault-powershell-delete-resources.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start utworzono Key Vault i Zapisano w nim certyfikat. Aby dowiedzieć się więcej na temat Key Vault i sposobu integrowania go z aplikacjami, przejdź do artykułu poniżej.

- Zapoznaj się [z omówieniem Azure Key Vault](../general/overview.md)
- Zobacz informacje dotyczące [Azure PowerShell poleceń cmdlet Key Vault](/powershell/module/az.keyvault/)
- Zapoznaj się z [omówieniem zabezpieczeń Key Vault](../general/security-overview.md)
