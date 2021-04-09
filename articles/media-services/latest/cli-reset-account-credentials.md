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
ms.openlocfilehash: cc605a08147da1d076b302e515a4ebe8d411a782
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101091846"
---
# <a name="azure-cli-example-reset-the-account-credentials"></a>Przykład interfejsu wiersza polecenia platformy Azure: Resetowanie poświadczeń konta

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Skrypt interfejsu wiersza polecenia platformy Azure pokazuje sposób resetowania poświadczeń konta i przywracania ustawień pliku app.config.

## <a name="prerequisites"></a>Wymagania wstępne

[Utwórz konto Media Services](./create-account-howto.md).

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
