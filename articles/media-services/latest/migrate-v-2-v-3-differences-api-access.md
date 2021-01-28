---
title: Różnice dostępu interfejsu API Azure Media Services V2 do wersji v3
description: W tym artykule opisano różnice dostępu do interfejsu API między Azure Media Services V2 a v3.
services: media-services
documentationcenter: na
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.devlang: multiple
ms.topic: conceptual
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 1/14/2020
ms.author: inhenkel
ms.openlocfilehash: 59347d2ff797cbbd2f91410c3ec713b544d7fd08
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98928202"
---
# <a name="api-access-differences-between-azure-media-services-v2-to-v3-api"></a>Różnice dostępu interfejsu API między Azure Media Services V2 a v3 API

![logo przewodnika migracji](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![kroki migracji 2](./media/migration-guide/steps-2.svg)

W tym artykule opisano różnice dostępu do interfejsu API między Azure Media Services V2 a v3.

## <a name="api-access"></a>Dostęp do interfejsu API

Wszystkie konta Media Services będą miały dostęp do interfejsu API v3. Jednak zdecydowanie zalecamy tworzenie migracji na nowym koncie przed zastosowaniem zaktualizowanego kodu do istniejącego konta w wersji 2. Wynika to z faktu, że jednostki V3 nie są wstecznie zgodne z v2. Niektóre jednostki w wersji 2, takie jak zasoby, są zgodne z wersjami v3.
Można nadal korzystać z istniejących kont, jeśli nie Mieszasz interfejsów API v2 i v3, a następnie spróbujemy wrócić do wersji 2, ale nie jest to zalecane.

Dostęp do interfejsu API v2 będzie dostępny do momentu wycofania go w 2024.

Podczas migracji można utworzyć konto v3, które nadal ma dostęp do wersji 2.  Tworzenie konta można wykonać przy użyciu:

- Interfejs API REST i Starsza wersja
- Zaznaczenie pola wyboru w portalu.

> [!div class="mx-imgBorder"]
> [![Tworzenie konta w portalu ](./media/migration-guide/v-3-v-2-access-account-creation-small.png)](./media/migration-guide/v-3-v-2-access-account-creation.png#lightbox)

Wszystkie zestawy SDK dla platformy .NET, interfejsu wiersza polecenia i inne są zgodne z najnowszym interfejsem API 2020-05-01, dlatego można znaleźć lub skonfigurować starsze wersje interfejsu API.

> [!NOTE]
> Nowe konta utworzone za pomocą interfejsu API 2020-05-01 nie mogą korzystać z interfejsów API v2.

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [migration guide next steps](./includes/migration-guide-next-steps.md)]
