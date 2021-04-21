---
title: Resetowanie poświadczeń konta — interfejs wiersza polecenia
description: Resetowanie poświadczeń konta i przywracanie ustawień pliku app.config przy użyciu skryptu interfejsu wiersza polecenia.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: ''
ms.service: media-services
ms.devlang: azurecli
ms.topic: troubleshooting
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-azurecli
ms.openlocfilehash: d5604f177484d33255d2923d72b00fae124c0f9a
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783597"
---
# <a name="azure-cli-example-reset-the-account-credentials"></a>Przykład interfejsu wiersza polecenia platformy Azure: resetowanie poświadczeń konta

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Skrypt interfejsu wiersza polecenia platformy Azure pokazuje sposób resetowania poświadczeń konta i przywracania ustawień pliku app.config.

## <a name="prerequisites"></a>Wymagania wstępne

[Utwórz Media Services konto.](./create-account-howto.md)

## <a name="example-script"></a>Przykładowy skrypt

```azurecli-interactive
# Update the following variables for your own settings:
resourceGroup=amsResourceGroup
amsAccountName=amsmediaaccountname

az ams account sp reset-credentials \
  --account-name $amsAccountName \
  --resource-group $resourceGroup
 ```

## <a name="next-steps"></a>Następne kroki

* [az ams](/cli/azure/ams)
* [Resetowanie poświadczeń](/cli/azure/ams/account/sp#az_ams_account_sp_reset_credentials)
