---
title: Interfejs wiersza polecenia skalowania jednostek zarezerwowanych multimediów (MRUs)
description: W tym temacie przedstawiono sposób skalowania przetwarzania multimediów przy użyciu interfejsu wiersza polecenia Azure Media Services.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: how-to
ms.date: 03/22/2021
ms.author: inhenkel
ms.openlocfilehash: 06c0c6333b84697415ef598d4c5e853d5c006f08
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104870164"
---
# <a name="how-to-scale-media-reserved-units"></a>Jak skalować jednostki zarezerwowane multimediów

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

W tym artykule przedstawiono sposób skalowania jednostek zarezerwowanych multimediów (MRSs) w celu przyspieszenia kodowania.

> [!WARNING]
> To polecenie przestanie działać w przypadku kont Media Services utworzonych za pomocą wersji 2020-05-01 interfejsu API lub nowszego. Jednostki zarezerwowane multimediów na te konta nie są już potrzebne, ponieważ system automatycznie przeskaluje się w górę i w dół na podstawie obciążenia. Jeśli nie widzisz opcji zarządzania MRUs w Azure Portal, używasz konta, które zostało utworzone za pomocą interfejsu API 2020-05-01 lub nowszego.

## <a name="prerequisites"></a>Wymagania wstępne

[Utwórz konto Media Services](./create-account-howto.md).

Zrozumienie [jednostek zarezerwowanych multimediów](concept-media-reserved-units.md).

## <a name="scale-media-reserved-units-with-cli"></a>Skalowanie jednostek zarezerwowanych multimediów przy użyciu interfejsu wiersza polecenia

Uruchom polecenie `mru`.

Następujące polecenie [AZ AMS Account MRU](/cli/azure/ams/account/mru) ustawia jednostki zarezerwowane multimediów na koncie "amsaccount" przy użyciu parametrów **Count** i **Type** .

```azurecli
az ams account mru set -n amsaccount -g amsResourceGroup --count 10 --type S3
```

## <a name="billing"></a>Rozliczenia

Opłata jest naliczana na podstawie liczby minut, w ciągu których zainicjowano alokację jednostek zarezerwowanych multimediów na Twoim koncie. Zdarza się to niezależnie od tego, czy na Twoim koncie są uruchomione jakieś zadania. Szczegółowe wyjaśnienie można znaleźć w sekcji często zadawane pytania na stronie [cennika Media Services](https://azure.microsoft.com/pricing/details/media-services/) .   

## <a name="next-step"></a>Następny krok

[Analizowanie wideo](analyze-videos-tutorial-with-api.md)

## <a name="see-also"></a>Zobacz też

* [Limity przydziału i ograniczenia](limits-quotas-constraints.md)
