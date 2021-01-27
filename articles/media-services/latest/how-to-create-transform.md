---
title: Przykład skryptu interfejsu wiersza polecenia platformy Azure — tworzenie przekształcenia
description: Przekształcenia opisują prosty przepływ pracy zadań podczas przetwarzania plików wideo lub audio (często określany jako „przepis”). Skrypt interfejsu wiersza polecenia platformy Azure w tym artykule pokazuje sposób tworzenia przekształcenia.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: ''
ms.service: media-services
ms.devlang: multiple
ms.topic: how-to
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/18/2020
ms.author: inhenkel
ms.custom: devx-track-azurecli
ms.openlocfilehash: adbd7deccf32312f67cff7b92ff7813036e9b1b3
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98898532"
---
# <a name="create-a-transform"></a>Tworzenie przekształcenia

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Skrypt interfejsu wiersza polecenia platformy Azure w tym artykule pokazuje sposób tworzenia przekształcenia. Przekształcenia opisują prosty przepływ pracy zadań podczas przetwarzania plików wideo lub audio (często określany jako „przepis”). Zawsze należy sprawdzić, czy przekształcenie z wybraną nazwą i „przepisem” już istnieje. Jeśli tak, należy użyć go ponownie.

## <a name="prerequisites"></a>Wymagania wstępne

[Utwórz konto Media Services](./create-account-howto.md).

## <a name="cli"></a>[Interfejs wiersza polecenia](#tab/cli/)

[!INCLUDE [media-services-cli-instructions.md](../../../includes/media-services-cli-instructions.md)]

> [!NOTE]
> Można określić tylko ścieżkę do niestandardowego pliku JSON domyślnego kodera dla [StandardEncoderPreset](/rest/api/media/transforms/createorupdate#standardencoderpreset), zobacz przykład [kodowania z niestandardowym przekształceniem](custom-preset-cli-howto.md) .
>
> Nie można przekazać nazwy pliku podczas korzystania z [BuiltInStandardEncoderPreset](/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset).

## <a name="example-script"></a>Przykładowy skrypt

[!code-azurecli-interactive[main](../../../cli_scripts/media-services/create-transform/Create-Transform.sh "Create a transform")]

## <a name="rest"></a>[REST](#tab/rest/)

[!INCLUDE [task general transform creation](./includes/task-create-basic-audio-rest.md)]

---

## <a name="next-steps"></a>Następne kroki

[Więcej informacji o transformacjech i zadaniach](transforms-jobs-concept.md)
