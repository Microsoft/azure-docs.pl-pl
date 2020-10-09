---
title: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure — tworzenie magazynu konfiguracji aplikacji platformy Azure
titleSuffix: Azure App Configuration
description: Utwórz magazyn konfiguracji aplikacji platformy Azure przy użyciu przykładowego skryptu interfejsu wiersza polecenia platformy Azure. Zobacz linki do artykułów referencyjnych do poleceń używanych w skrypcie.
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: sample
ms.date: 01/24/2020
ms.author: lcozzens
ms.custom: devx-track-azurecli
ms.openlocfilehash: 7b3221c55cef6207ea38ac1375202acd8b8ab4f1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88588338"
---
# <a name="create-an-azure-app-configuration-store"></a>Tworzenie magazynu usługi Azure App Configuration

Ten przykładowy skrypt tworzy nowe wystąpienie konfiguracji aplikacji platformy Azure w nowej grupie zasobów.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten artykuł będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Przykładowy skrypt

```azurecli-interactive
#!/bin/bash

appConfigName=myTestAppConfigStore
#resource name must be lowercase
myAppConfigStoreName=${appConfigName,,}
myResourceGroupName=$appConfigName"Group"

# Create resource group 
az group create --name $myResourceGroupName --location eastus

# Create the Azure AppConfig Service resource and query the hostName
appConfigHostname=$(az appconfig create \
  --name $myAppConfigStoreName \
  --location eastus \
  --resource-group $myResourceGroupName \
  --query endpoint \
  --sku free \
  -o tsv
  )

# Get the AppConfig connection string 
appConfigConnectionString=$(az appconfig credential list \
--resource-group $myResourceGroupName \
--name $myAppConfigStoreName \
--query "[?name=='Secondary Read Only'] .connectionString" -o tsv)

# Echo the connection string for use in your application
echo "$appConfigConnectionString"
```

Zanotuj rzeczywistą nazwę wygenerowaną dla nowej grupy zasobów. Tej nazwy grupy zasobów użyjesz, gdy zechcesz usunąć wszystkie zasoby w grupie.

[!INCLUDE [cli-script-cleanup](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Objaśnienia dla skryptu

Ten skrypt zawiera następujące polecenia służące do tworzenia nowej grupy zasobów i magazynu konfiguracji aplikacji. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [az appconfig create](/cli/azure/appconfig#az-appconfig-create) | Tworzy zasób magazynu konfiguracji aplikacji. |
| [AZ AppConfig list Credential](/cli/azure/appconfig/credential#az-appconfig-credential-list) | Wyświetl listę kluczy dostępu dla magazynu konfiguracji aplikacji. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](/cli/azure).

Dodatkowe przykłady skryptów interfejsu wiersza polecenia konfiguracji aplikacji można znaleźć w [przykładach interfejsu wiersza polecenia konfiguracji aplikacji platformy Azure](../cli-samples.md).
