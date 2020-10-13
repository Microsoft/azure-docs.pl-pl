---
title: Tworzenie i pobieranie atrybutów klucza w Azure Key Vault — Azure PowerShell
description: Przewodnik Szybki Start przedstawiający sposób ustawiania i pobierania klucza z Azure Key Vault przy użyciu Azure PowerShell
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.date: 03/30/2020
ms.author: mbaldwin
ms.openlocfilehash: 3cea28fa278bb699a7fb5aaa0c79bf1e71f59ac1
ms.sourcegitcommit: 541bb46e38ce21829a056da880c1619954678586
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2020
ms.locfileid: "91940501"
---
# <a name="quickstart-set-and-retrieve-a-key-from-azure-key-vault-using-azure-powershell"></a>Szybki Start: Ustawianie i pobieranie klucza z Azure Key Vault przy użyciu Azure PowerShell

W tym przewodniku szybki start utworzysz Magazyn kluczy w Azure Key Vault z Azure PowerShell. Azure Key Vault to usługa w chmurze, która działa jako bezpieczny magazyn wpisów tajnych. Możesz bezpiecznie przechowywać klucze, hasła, certyfikaty oraz inne wpisy tajne. Aby uzyskać więcej informacji na Key Vault można zapoznać się z [omówieniem](../general/overview.md). Azure PowerShell służy do tworzenia zasobów platformy Azure i zarządzania nimi przy użyciu poleceń lub skryptów. Po zakończeniu tej operacji będziesz przechowywać klucz.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Jeśli postanowisz zainstalować program PowerShell i używać go lokalnie, to ten samouczek wymaga modułu Azure PowerShell w wersji 1.0.0 lub nowszej. Wpisz polecenie `$PSVersionTable.PSVersion`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Login-AzAccount`, aby utworzyć połączenie z platformą Azure.

```azurepowershell-interactive
Login-AzAccount
```

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów platformy Azure za pomocą polecenia [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Grupa zasobów to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. 

```azurepowershell-interactive
New-AzResourceGroup -Name ContosoResourceGroup -Location EastUS
```

## <a name="create-a-key-vault"></a>Tworzenie magazynu kluczy

Następnie utworzysz magazyn kluczy. Podczas wykonywania tego kroku potrzebne będą pewne informacje:

Chociaż w tym przewodniku szybki start używamy Key Vault nazwy "contoso KeyVault2", musisz użyć unikatowej nazwy.

- **Nazwa magazynu**: Contoso Vault2.
- **Nazwa grupy zasobów** ContosoResourceGroup.
- **Lokalizacja** Wschodnie stany USA.

```azurepowershell-interactive
New-AzKeyVault -Name 'Contoso-Vault2' -ResourceGroupName 'ContosoResourceGroup' -Location 'East US'
```

Dane wyjściowe tego polecenia cmdlet pokazują właściwości nowo utworzonej usługi Key Vault. Zanotuj dwie poniższe właściwości:

* **Nazwa magazynu**: w tym przykładzie jest to **Contoso-Vault2**. Ta nazwa będzie używana do innych poleceń cmdlet usługi Key Vault.
* **Identyfikator URI magazynu**: w tym przykładzie jest to https://Contoso-Vault2.vault.azure.net/. Aplikacje korzystające z magazynu za pomocą jego interfejsu API REST muszą używać tego identyfikatora URI.

Po utworzeniu magazynu Twoje konto platformy Azure będzie jedynym kontem z uprawnieniami do wykonywania jakichkolwiek operacji na tym nowym magazynie.

## <a name="add-a-key-to-key-vault"></a>Dodaj klucz do Key Vault

Aby dodać klucz do magazynu, wystarczy wykonać kilka dodatkowych kroków. Ten klucz może być używany przez aplikację. 

Wpisz poniższe polecenia, aby utworzyć nazwę o nazwie **ExampleKey** :

```azurepowershell-interactive
Add-AzKeyVaultKey -VaultName 'Contoso-Vault2' -Name 'ExampleKey' -Destination 'Software'
```

Teraz można odwołać się do tego klucza, który został dodany do Azure Key Vault przy użyciu identyfikatora URI. Użyj **" https://Contoso-Vault2.vault.azure.net/keys/ExampleKey "** , aby pobrać bieżącą wersję. 

Aby wyświetlić poprzednio przechowywany klucz:

```azurepowershell-interactive
Get-AzKeyVaultKey -VaultName 'Contoso-Vault2' -KeyName 'ExampleKey'
```

Teraz utworzono Key Vault, Zapisano klucz i pobieramy go.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Inne przewodniki szybkiego startu i samouczki w tej kolekcji bazują na tym przewodniku. Jeśli planujesz korzystać z kolejnych przewodników Szybki start i samouczków, pozostaw te zasoby na swoim miejscu.
Gdy grupa zasobów i wszystkie pokrewne zasoby nie będą już potrzebne, można je usunąć za pomocą polecenia [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) . Możesz usunąć zasoby w następujący sposób:

```azurepowershell-interactive
Remove-AzResourceGroup -Name ContosoResourceGroup
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start utworzono Key Vault i Zapisano w nim certyfikat. Aby dowiedzieć się więcej na temat Key Vault i sposobu integrowania go z aplikacjami, przejdź do artykułu poniżej.

- Zapoznaj się [z omówieniem Azure Key Vault](../general/overview.md)
- Zobacz informacje dotyczące [Azure PowerShell poleceń cmdlet Key Vault](/powershell/module/az.keyvault/)
- Przegląd [Azure Key Vault najlepszych](../general/best-practices.md) rozwiązań
