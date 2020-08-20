---
title: Przekazywanie zawartości do Azure Media Services zasobu przy użyciu interfejsu wiersza polecenia platformy Azure
description: Skrypt interfejsu wiersza polecenia platformy Azure w tym temacie pokazuje sposób tworzenia elementu zawartości usługi Media Services, do którego będzie przekazywana zawartość.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: ''
ms.service: media-services
ms.devlang: azurecli
ms.topic: how-to
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 08/18/2020
ms.author: inhenkel
ms.custom: devx-track-azurecli
ms.openlocfilehash: b40c936006fa47964ef67ffda37c80eb5732bdf0
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/20/2020
ms.locfileid: "88653896"
---
# <a name="create-an-asset"></a>Tworzenie zasobu

W tym artykule pokazano, jak utworzyć Media Services element zawartości.  Będziesz używać zasobu do przechowywania zawartości multimedialnej w celu kodowania i przesyłania strumieniowego.  Aby dowiedzieć się więcej o zasobach Media Services, Odczytaj [elementy zawartości w Azure Media Services v3](assets-concept.md)

## <a name="prerequisites"></a>Wymagania wstępne

Postępuj zgodnie z instrukcjami w temacie [Tworzenie konta Media Services](./create-account-howto.md) , aby utworzyć konto Media Services i grupę zasobów do utworzenia elementu zawartości.

## <a name="methods"></a>Metody

## <a name="cli"></a>[Interfejs wiersza polecenia](#tab/cli/)

[!INCLUDE [Create an asset with CLI](./includes/task-create-asset-cli.md)]

## <a name="cli-shell"></a>[Powłoka interfejsu wiersza polecenia](#tab/clishell/)

[!INCLUDE [media-services-cli-instructions.md](../../../includes/media-services-cli-instructions.md)]

## <a name="example-script"></a>Przykładowy skrypt

[!code-azurecli-interactive[main](../../../cli_scripts/media-services/create-asset/Create-Asset.sh "Create an asset")]

## <a name="rest"></a>[REST](#tab/rest/)

### <a name="using-rest"></a>Korzystanie z interfejsu REST

[!INCLUDE [media-services-cli-instructions.md](./includes/task-create-asset-rest.md)]

### <a name="using-curl"></a>Używanie programu cURL

[!INCLUDE [media-services-cli-instructions.md](./includes/task-create-asset-curl.md)]

## <a name="using-postman"></a>Używanie narzędzia Postman

[!INCLUDE[Create an asset with Postman](./includes/task-create-asset-postman.md)]

## <a name="net"></a>[.NET](#tab/net/)

[!INCLUDE [media-services-cli-instructions.md](./includes/task-create-asset-dotnet.md)]

---

## <a name="next-steps"></a>Następne kroki

[Przegląd Media Services](media-services-overview.md)
