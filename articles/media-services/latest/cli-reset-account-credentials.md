---
title: Przykład skryptu interfejsu wiersza polecenia platformy Azure — Resetowanie poświadczeń konta
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
ms.openlocfilehash: 2b9b95af79b8aac11f56fe576f860d719b5fb50e
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98897683"
---
# <a name="azure-cli-example-reset-the-account-credentials"></a>Przykład interfejsu wiersza polecenia platformy Azure: Resetowanie poświadczeń konta

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Skrypt interfejsu wiersza polecenia platformy Azure pokazuje sposób resetowania poświadczeń konta i przywracania ustawień pliku app.config.

## <a name="prerequisites"></a>Wymagania wstępne

[Utwórz konto Media Services](./create-account-howto.md).

[!INCLUDE [media-services-cli-instructions.md](../../../includes/media-services-cli-instructions.md)]

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

* [AZ AMS](/cli/azure/ams)
* [Resetowanie poświadczeń](/cli/azure/ams/account/sp#az-ams-account-sp-reset-credentials)
