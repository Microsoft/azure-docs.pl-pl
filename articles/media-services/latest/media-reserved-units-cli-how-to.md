---
title: Interfejs wiersza polecenia skalowania jednostek zarezerwowanych multimediów (MRUs)
description: W tym temacie przedstawiono sposób skalowania przetwarzania multimediów przy użyciu interfejsu wiersza polecenia Azure Media Services.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 09/30/2020
ms.author: inhenkel
ms.openlocfilehash: 98e87cf9d1f46ddb8ee1d433bd0b0ba8806fac89
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/18/2021
ms.locfileid: "101091925"
---
# <a name="how-to-scale-media-reserved-units"></a>Jak skalować jednostki zarezerwowane multimediów

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

W tym artykule przedstawiono sposób skalowania jednostek zarezerwowanych multimediów (MRSs) w celu przyspieszenia kodowania.

## <a name="prerequisites"></a>Wymagania wstępne

[Utwórz konto Media Services](./create-account-howto.md).

Zrozumienie [jednostek zarezerwowanych multimediów](concept-media-reserved-units.md).

## <a name="scale-media-reserved-units-with-cli"></a>Skalowanie jednostek zarezerwowanych multimediów przy użyciu interfejsu wiersza polecenia

Uruchom polecenie `mru`.

Następujące polecenie [AZ AMS Account MRU](/cli/azure/ams/account/mru?view=azure-cli-latest) ustawia jednostki zarezerwowane multimediów na koncie "amsaccount" przy użyciu parametrów **Count** i **Type** .

```azurecli
az ams account mru set -n amsaccount -g amsResourceGroup --count 10 --type S3
```

## <a name="billing"></a>Rozliczenia

Opłata jest naliczana na podstawie liczby minut, w ciągu których zainicjowano alokację jednostek zarezerwowanych multimediów na Twoim koncie. Zdarza się to niezależnie od tego, czy na Twoim koncie są uruchomione jakieś zadania. Szczegółowe wyjaśnienie można znaleźć w sekcji często zadawane pytania na stronie [cennika Media Services](https://azure.microsoft.com/pricing/details/media-services/) .   

## <a name="next-step"></a>Następny krok

[Analizowanie wideo](analyze-videos-tutorial-with-api.md)

## <a name="see-also"></a>Zobacz też

* [Limity przydziału i ograniczenia](limits-quotas-constraints.md)
