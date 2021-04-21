---
title: Szybki start — tworzenie aplikacji Azure Key Vault interfejsem wiersza polecenia platformy Azure
description: Przewodnik Szybki start przedstawiający sposób tworzenia aplikacji Azure Key Vault interfejsie wiersza polecenia platformy Azure
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: quickstart
ms.date: 01/27/2021
ms.author: mbaldwin
ms.openlocfilehash: 588bee2922ee44f3f89b5d252b5b4a6991d26b82
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107815130"
---
# <a name="quickstart-create-a-key-vault-using-the-azure-cli"></a>Szybki start: tworzenie magazynu kluczy przy użyciu interfejsu wiersza polecenia platformy Azure

Azure Key Vault to usługa w chmurze, która zapewnia bezpieczny magazyn [kluczy,](../keys/index.yml) [wpisów tajnych](../secrets/index.yml) [i certyfikatów.](../certificates/index.yml) Aby uzyskać więcej informacji na Key Vault, zobacz [Informacje o Azure Key Vault](overview.md); Aby uzyskać więcej informacji na temat tego, co można przechowywać w magazynie kluczy, zobacz About keys, secrets, and certificates (Informacje o [kluczach, wpisach tajnych i certyfikatach).](about-keys-secrets-certificates.md)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Ten przewodnik Szybki start wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.4 lub nowszej. Jeśli używasz Azure Cloud Shell, najnowsza wersja jest już zainstalowana.

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

[!INCLUDE [Create a resource group](../../../includes/key-vault-cli-rg-creation.md)]

## <a name="create-a-key-vault"></a>Tworzenie magazynu kluczy

[!INCLUDE [Create a key vault](../../../includes/key-vault-cli-kv-creation.md)]

## <a name="clean-up-resources"></a>Czyszczenie zasobów

[!INCLUDE [Create a key vault](../../../includes/key-vault-cli-delete-resources.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzono Key Vault i usunięto go. Aby dowiedzieć się więcej Key Vault o tym, jak zintegrować ją z aplikacjami, przejdź do poniższych artykułów.

- Przeczytaj omówienie [Azure Key Vault](overview.md)
- Przejrzyj omówienie [Azure Key Vault zabezpieczeń](security-features.md)
- Zobacz informacje dotyczące poleceń [az keyvault interfejsu wiersza polecenia platformy Azure](/cli/azure/keyvault)

