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
ms.openlocfilehash: c5fa3aa8397ea6e13500717f035c414af8de8e3d
ms.sourcegitcommit: 9f4510cb67e566d8dad9a7908fd8b58ade9da3b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2021
ms.locfileid: "106121680"
---
# <a name="how-to-scale-media-reserved-units"></a>Jak skalować jednostki zarezerwowane multimediów

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

W tym artykule przedstawiono sposób skalowania jednostek zarezerwowanych multimediów (MRSs) w celu przyspieszenia kodowania.

> [!WARNING]
> To polecenie przestanie działać w przypadku kont Media Services utworzonych za pomocą wersji 2020-05-01 interfejsu API lub nowszego. Jednostki zarezerwowane multimediów na te konta nie są już potrzebne, ponieważ system automatycznie przeskaluje się w górę i w dół na podstawie obciążenia. Jeśli nie widzisz opcji zarządzania MRUs w Azure Portal, używasz konta, które zostało utworzone za pomocą interfejsu API 2020-05-01 lub nowszego.

## <a name="prerequisites"></a>Wymagania wstępne

[Utwórz konto Media Services](./account-create-how-to.md).

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

[Analizowanie wideo](analyze-videos-tutorial.md)

## <a name="see-also"></a>Zobacz też

* [Limity przydziału i ograniczenia](limits-quotas-constraints-reference.md)
