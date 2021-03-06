---
title: 'Interfejs wiersza polecenia: tworzenie zaplanowanej kopii zapasowej'
description: Dowiedz się, jak używać interfejsu wiersza polecenia platformy Azure do automatyzowania wdrażania aplikacji App Service zarządzania nimi. W tym przykładzie pokazano, jak utworzyć zaplanowaną kopię zapasową aplikacji.
author: msangapu-msft
tags: azure-service-management
ms.devlang: azurecli
ms.topic: sample
ms.date: 12/11/2017
ms.author: msangapu
ms.reviewer: cephalin
ms.custom: mvc, seodec18, devx-track-azurecli
ms.openlocfilehash: db1d0558f93b203af1605663533847d32afbcffb
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107782556"
---
# <a name="create-a-scheduled-backup-for-an-app-service-app-using-cli"></a>Tworzenie zaplanowanej kopii zapasowej aplikacji usługi App Service przy użyciu interfejsu wiersza polecenia

Ten przykładowy skrypt tworzy aplikację w usłudze App Service wraz z jej powiązanymi zasobami, a następnie tworzy zaplanowaną kopię zapasową na potrzeby tej aplikacji. 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, musisz mieć interfejs wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Aby dowiedzieć się, jaka wersja jest używana, uruchom polecenie `az --version`. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Przykładowy skrypt

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/backup-scheduled/backup-scheduled.sh?highlight=3-7 "Create a scheduled backup for an app")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Objaśnienia dla skryptu

W tym skrypcie użyto następujących poleceń. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [`az group create`](/cli/azure/group#az_group_create) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [`az storage account create`](/cli/azure/storage/account#az_storage_account_create) | Tworzy konto magazynu. |
| [`az storage container create`](/cli/azure/storage/container#az_storage_container_create) | Tworzy kontener usługi Azure Storage. |
| [`az storage container generate-sas`](/cli/azure/storage/container#az_storage_container_generate_sas) | Generuje token SAS dla kontenera usługi Azure Storage.  |
| [`az appservice plan create`](/cli/azure/appservice/plan#az_appservice_plan_create) | Tworzy plan usługi App Service. |
| [`az webapp create`](/cli/azure/webapp#az_webapp_create) | Tworzy aplikację usługi App Service. |
| [`az webapp config backup update`](/cli/azure/webapp/config/backup#az_webapp_config_backup_update) | Konfiguruje nowy harmonogram tworzenia kopii zapasowej aplikacji usługi App Service. |
| [`az webapp config backup show`](/cli/azure/webapp/config/backup#az_webapp_config_backup_show) | Wyświetla harmonogram tworzenia kopii zapasowej aplikacji usługi App Service. |
| [`az webapp config backup list`](/cli/azure/webapp/config/backup#az_webapp_config_backup_list) | Pobiera listę kopii zapasowych dla aplikacji usługi App Service. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](/cli/azure).

Dodatkowe przykłady skryptów interfejsu wiersza polecenia usługi App Service można znaleźć w [dokumentacji usługi Azure App Service](../samples-cli.md).
