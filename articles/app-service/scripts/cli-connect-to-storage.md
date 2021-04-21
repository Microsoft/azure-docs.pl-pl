---
title: 'Interfejs wiersza polecenia: łączenie aplikacji z kontem magazynu'
description: Dowiedz się, jak używać interfejsu wiersza polecenia platformy Azure do automatyzowania wdrażania aplikacji App Service zarządzania nimi. W tym przykładzie pokazano, jak połączyć aplikację z kontem magazynu.
author: msangapu-msft
tags: azure-service-management
ms.assetid: bc8345b2-8487-40c6-a91f-77414e8688e6
ms.devlang: azurecli
ms.topic: sample
ms.date: 12/11/2017
ms.author: msangapu
ms.custom: mvc, seodec18, devx-track-azurecli
ms.openlocfilehash: 8f1eba39f8487df6dd62364574426315aed4c20d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107782341"
---
# <a name="connect-an-app-service-app-to-a-storage-account-using-cli"></a>Łączenie aplikacji usługi App Service z kontem magazynu przy użyciu interfejsu wiersza polecenia

Ten przykładowy skrypt tworzy konto usługi Azure Storage i aplikację usługi App Service. Następnie łączy on konto magazynu z aplikacją przy użyciu ustawień aplikacji.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, musisz mieć interfejs wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Aby dowiedzieć się, jaka wersja jest używana, uruchom polecenie `az --version`. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli).


## <a name="sample-script"></a>Przykładowy skrypt

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/connect-to-storage/connect-to-storage.sh "Azure Storage")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Objaśnienia dla skryptu

Ten skrypt zawiera następujące polecenia, służące do utworzenia grupy zasobów, aplikacji usługi App Service, konta magazynu i wszystkich powiązanych zasobów. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [`az group create`](/cli/azure/group#az_group_create) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [`az appservice plan create`](/cli/azure/appservice/plan#az_appservice_plan_create) | Tworzy plan usługi App Service. |
| [`az webapp create`](/cli/azure/webapp#az_webapp_create) | Tworzy aplikację usługi App Service. |
| [`az storage account create`](/cli/azure/storage/account#az_storage_account_create) | Tworzy konto magazynu. |
| [`az storage account show-connection-string`](/cli/azure/storage/account#az_storage_account_show_connection_string) | Pobiera parametry połączenia konta magazynu. |
| [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_set) | Tworzy lub aktualizuje ustawienia aplikacji dla aplikacji usługi App Service. Ustawienia aplikacji są dostępne dla aplikacji jako zmienne środowiskowe. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](/cli/azure).

Dodatkowe przykłady skryptów interfejsu wiersza polecenia usługi App Service można znaleźć w [dokumentacji usługi Azure App Service](../samples-cli.md).
