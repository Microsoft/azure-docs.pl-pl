---
title: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure — tworzenie przekształcenia | Microsoft Docs
description: Przekształcenia opisują prosty przepływ pracy zadań podczas przetwarzania plików wideo lub audio (często określany jako „przepis”). Skrypt interfejsu wiersza polecenia platformy Azure w tym artykule pokazuje sposób tworzenia przekształcenia.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: ''
ms.service: media-services
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-azurecli
ms.openlocfilehash: dd2efc820bd2f35dee8e27737e7de919e6af2f30
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/01/2020
ms.locfileid: "89269781"
---
# <a name="cli-example-create-a-transform"></a>Przykład interfejsu wiersza polecenia: tworzenie przekształcenia

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Skrypt interfejsu wiersza polecenia platformy Azure w tym artykule pokazuje sposób tworzenia przekształcenia. Przekształcenia opisują prosty przepływ pracy zadań podczas przetwarzania plików wideo lub audio (często określany jako „przepis”). Zawsze należy sprawdzić, czy przekształcenie z wybraną nazwą i „przepisem” już istnieje. Jeśli tak, należy użyć go ponownie.

## <a name="prerequisites"></a>Wymagania wstępne 

[Utwórz konto Media Services](./create-account-howto.md).

[!INCLUDE [media-services-cli-instructions.md](../../../includes/media-services-cli-instructions.md)]

> [!NOTE]
> Można określić tylko ścieżkę do niestandardowego pliku JSON domyślnego kodera dla [StandardEncoderPreset](/rest/api/media/transforms/createorupdate#standardencoderpreset), zobacz przykład [kodowania z niestandardowym przekształceniem](custom-preset-cli-howto.md) .
>
> Nie można przekazać nazwy pliku podczas korzystania z [BuiltInStandardEncoderPreset](/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset).

## <a name="example-script"></a>Przykładowy skrypt

[!code-azurecli-interactive[main](../../../cli_scripts/media-services/create-transform/Create-Transform.sh "Create a transform")]

## <a name="next-steps"></a>Następne kroki

[AZ AMS Transform (CLI)](/cli/azure/ams/transform?view=azure-cli-latest)
