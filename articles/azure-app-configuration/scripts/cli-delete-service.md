---
title: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure — Usuwanie magazynu konfiguracji aplikacji platformy Azure
titleSuffix: Azure App Configuration
description: Usuń magazyn konfiguracji aplikacji platformy Azure przy użyciu przykładowego skryptu interfejsu wiersza polecenia platformy Azure. Zobacz linki do artykułów referencyjnych do poleceń używanych w skrypcie.
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.devlang: azurecli
ms.topic: sample
ms.date: 02/19/2020
ms.author: lcozzens
ms.custom: devx-track-azurecli
ms.openlocfilehash: e107ba9628eca384f518f7a241be5c00c278ef0a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88588327"
---
# <a name="delete-an-azure-app-configuration-store"></a>Usuwanie magazynu usługi Azure App Configuration

Ten przykładowy skrypt usuwa wystąpienie usługi Azure App Configuration.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten artykuł będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Przykładowy skrypt

```azurecli-interactive
#/bin/bash

# Delete an App Configuration store named myTestAppConfigStore from the Resource Group myResourceGroup
az appconfig delete --name myTestAppConfigStore --resource-group myResourceGroup
```

[!INCLUDE [cli-script-cleanup](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Objaśnienia dla skryptu

Ten skrypt zawiera następujące polecenia służące do usuwania magazynu konfiguracji aplikacji. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [az appconfig delete](/cli/azure/appconfig#az-appconfig-delete) | Usuwa zasób magazynu konfiguracji aplikacji. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](/cli/azure).

Dodatkowe przykłady skryptów interfejsu wiersza polecenia konfiguracji aplikacji można znaleźć w [przykładach interfejsu wiersza polecenia konfiguracji aplikacji platformy Azure](../cli-samples.md).
