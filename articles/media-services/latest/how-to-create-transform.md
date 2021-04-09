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
ms.openlocfilehash: 4623610960d8f21a2dab3293c7499a2112416254
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101718917"
---
# <a name="create-a-transform"></a>Tworzenie przekształcenia

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Skrypt interfejsu wiersza polecenia platformy Azure w tym artykule pokazuje sposób tworzenia przekształcenia. Przekształcenia opisują prosty przepływ pracy zadań podczas przetwarzania plików wideo lub audio (często określany jako „przepis”). Zawsze należy sprawdzić, czy przekształcenie z wybraną nazwą i „przepisem” już istnieje. Jeśli tak, należy użyć go ponownie.

## <a name="prerequisites"></a>Wymagania wstępne

[Utwórz konto Media Services](./create-account-howto.md).

## <a name="cli"></a>[Interfejs wiersza polecenia](#tab/cli/)

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

[!INCLUDE [transforms next steps](./includes/transforms-next-steps.md)]
