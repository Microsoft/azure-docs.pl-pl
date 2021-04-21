---
title: Szybki start — tworzenie Azure Key Vault za pomocą Azure PowerShell
description: Przewodnik Szybki start przedstawiający sposób tworzenia aplikacji Azure Key Vault użyciu Azure PowerShell
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: quickstart
ms.date: 01/27/2021
ms.author: mbaldwin
ms.openlocfilehash: cc6d9ca2621a56242d7472a088e55651f5502c9c
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107814806"
---
# <a name="quickstart-create-a-key-vault-using-powershell"></a>Szybki start: tworzenie magazynu kluczy przy użyciu programu PowerShell

Azure Key Vault to usługa w chmurze, która zapewnia bezpieczny magazyn [kluczy,](../keys/index.yml) [wpisów tajnych](../secrets/index.yml) [i certyfikatów.](../certificates/index.yml) Aby uzyskać więcej informacji na Key Vault, zobacz [Informacje o Azure Key Vault](overview.md); Aby uzyskać więcej informacji na temat tego, co można przechowywać w magazynie kluczy, zobacz About keys, secrets, and certificates (Informacje o [kluczach, wpisach tajnych i certyfikatach).](about-keys-secrets-certificates.md)

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

W tym przewodniku Szybki start utworzysz magazyn kluczy przy [użyciu](/powershell/azure/)Azure PowerShell . Jeśli postanowisz zainstalować program PowerShell i używać go lokalnie, to ten samouczek wymaga modułu Azure PowerShell w wersji 1.0.0 lub nowszej. Wpisz polecenie `$PSVersionTable.PSVersion`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Login-AzAccount`, aby utworzyć połączenie z platformą Azure.

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

W tym przewodniku Szybki start utworzono Key Vault użyciu Azure PowerShell. Aby dowiedzieć się więcej Key Vault o tym, jak zintegrować ją z aplikacjami, przejdź do poniższych artykułów.

- Przeczytaj omówienie [Azure Key Vault](overview.md)
- Zapoznaj się z odwołaniem do [Azure PowerShell Key Vault cmdlet](/powershell/module/az.keyvault/)
- Przejrzyj omówienie [Azure Key Vault zabezpieczeń](security-features.md)

