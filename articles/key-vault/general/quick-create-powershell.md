---
title: Szybki Start — tworzenie Azure Key Vault przy użyciu Azure PowerShell
description: Przewodnik Szybki Start przedstawiający sposób tworzenia Azure Key Vault przy użyciu Azure PowerShell
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: quickstart
ms.date: 01/27/2021
ms.author: mbaldwin
ms.openlocfilehash: e77493bc73bc2d6f590d9bdcf891171fbd71f74e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99070230"
---
# <a name="quickstart-create-a-key-vault-using-powershell"></a>Szybki Start: Tworzenie magazynu kluczy przy użyciu programu PowerShell

Azure Key Vault to usługa w chmurze, która zapewnia bezpieczny magazyn [kluczy](../keys/index.yml), wpisów [tajnych](../secrets/index.yml)i [certyfikatów](../certificates/index.yml). Aby uzyskać więcej informacji na temat Key Vault, zobacz [Informacje o Azure Key Vault](overview.md); Aby uzyskać więcej informacji na temat tego, co może być przechowywane w magazynie kluczy, zobacz [Informacje o kluczach, wpisach tajnych i certyfikatach](about-keys-secrets-certificates.md).

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

W tym przewodniku szybki start utworzysz Magazyn kluczy z [Azure PowerShell](/powershell/azure/). Jeśli postanowisz zainstalować program PowerShell i używać go lokalnie, to ten samouczek wymaga modułu Azure PowerShell w wersji 1.0.0 lub nowszej. Wpisz polecenie `$PSVersionTable.PSVersion`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Login-AzAccount`, aby utworzyć połączenie z platformą Azure.

```azurepowershell-interactive
Login-AzAccount
```

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

[!INCLUDE [Create a resource group](../../../includes/key-vault-powershell-rg-creation.md)]

## <a name="create-a-key-vault"></a>Tworzenie magazynu kluczy

[!INCLUDE [Create a key vault](../../../includes/key-vault-powershell-kv-creation.md)]

## <a name="clean-up-resources"></a>Czyszczenie zasobów

[!INCLUDE [Create a key vault](../../../includes/key-vault-powershell-delete-resources.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start utworzono Key Vault przy użyciu Azure PowerShell. Aby dowiedzieć się więcej na temat Key Vault i sposobu integrowania go z aplikacjami, przejdź do artykułu poniżej.

- Zapoznaj się [z omówieniem Azure Key Vault](overview.md)
- Zobacz informacje dotyczące [Azure PowerShell poleceń cmdlet Key Vault](/powershell/module/az.keyvault/)
- Zapoznaj się z [omówieniem zabezpieczeń Azure Key Vault](security-overview.md)

