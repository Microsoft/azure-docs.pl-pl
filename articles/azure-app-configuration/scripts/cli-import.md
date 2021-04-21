---
title: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure — importowanie do App Configuration magazynu
titleSuffix: Azure App Configuration
description: Używanie skryptu interfejsu wiersza polecenia platformy Azure — importowanie konfiguracji do Azure App Configuration
services: azure-app-configuration
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.devlang: azurecli
ms.topic: sample
ms.date: 02/19/2020
ms.author: alkemper
ms.custom: devx-track-azurecli
ms.openlocfilehash: 5a80acd1261211fe3efaefa6a4b6025a321e2d65
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107774183"
---
# <a name="import-to-an-azure-app-configuration-store"></a>Importowanie do magazynu usługi Azure App Configuration

Ten przykładowy skrypt importuje ustawienia klucz-wartość do Azure App Configuration magazynu.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Ten samouczek wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Jeśli używasz Azure Cloud Shell, najnowsza wersja jest już zainstalowana.

## <a name="sample-script"></a>Przykładowy skrypt

```azurecli-interactive
#!/bin/bash

# Import key-values from a file
az appconfig kv import --name myTestAppConfigStore --source file --format json --path ~/Import.json
```

[!INCLUDE [cli-script-cleanup](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Objaśnienia dla skryptu

Ten skrypt używa następujących poleceń do importowania do App Configuration magazynu. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [az appconfig kv import](/cli/azure/appconfig/kv#az_appconfig_kv_import) | Importuje do App Configuration magazynu. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację](/cli/azure)interfejsu wiersza polecenia platformy Azure .

Więcej App Configuration przykładowych skryptów interfejsu wiersza polecenia można znaleźć w przykładach Azure App Configuration [interfejsu wiersza polecenia.](../cli-samples.md)
