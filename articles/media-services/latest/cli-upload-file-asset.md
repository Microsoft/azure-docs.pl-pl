---
title: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure — przekazywanie pliku do kontenera | Microsoft Docs
description: W tym artykule pokazano, jak za pomocą skryptu interfejsu wiersza polecenia platformy Azure przekazać plik lokalny do kontenera magazynu.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: ''
ms.service: media-services
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/01/2019
ms.author: juliako
ms.openlocfilehash: 68dbeba62f5b59e2c047c7f403e7c50e7325e8ad
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87092120"
---
# <a name="azure-cli-example-upload-a-local-file-to-a-container"></a>Przykład interfejsu wiersza polecenia platformy Azure: przekazywanie lokalnego pliku do kontenera

Skrypt interfejsu wiersza polecenia platformy Azure w tym artykule pokazuje, jak można przekazać plik lokalny do kontenera magazynu.

## <a name="prerequisites"></a>Wymagania wstępne

* [Utwórz konto Media Services](./create-account-howto.md).
* Zapoznaj się z tematem [Zarządzanie zasobami](manage-asset-concept.md).

[!INCLUDE [media-services-cli-instructions.md](../../../includes/media-services-cli-instructions.md)]

## <a name="example-script"></a>Przykładowy skrypt

```azurecli-interactive
#!/bin/bash
# Update the following variables for your own settings:
storageAccountName=build2018storage
assetContainer="asset-4c834446-7e55-4760-9a25-f2d4fb1f4657"
localFile="..\Media\ignite-short.mp4"
blobName="ignite-short.mp4"
sasToken="?sv=2015-07-08&sr=c&sig=u1uy9OIeXnZUEN62hE0bDgg%2FPXYgRDNGnQxE%2BSi51dM%3D&se=2018-04-29T18:42:02Z&sp=rwl"
# Use the az storage modules to upload a local file to the container using the SAS URL from previous step.
# If you are logged in already to the subscription with access to the storage account, you do not need to use the --sas-token at all. Just eliminate it below.
# The container name is in the SAS URL path, and should be set with the -c option.
# Use the -f option to point to a local file on your machine.
# Use the -n option to name the blob in storage.
# Use the --account-name option to point to the storage account name to use 
# Use the --sas-token option to place the SAS token after the query string from previous step. 
# NOTE that the SAS Token is only good for up to 24 hours max. 
#
az storage blob upload \
    -c $assetContainer \
    -f $localFile \
    -n $blobName \
    --account-name $storageAccountName \
    --sas-token $sasToken \
echo "press  [ENTER]  to continue."
read continue
```

## <a name="next-steps"></a>Następne kroki

[Przegląd Media Services](media-services-overview.md)
