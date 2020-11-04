---
title: Chmury i regiony, w których jest dostępny Azure Media Services wersja 3
description: W tym artykule omówiono adresy URL używane dla punktów końcowych i kod dla regionów.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: reference
ms.date: 10/28/2020
ms.author: inhenkel
ms.openlocfilehash: c17eeda91af46c4159f7c4b1747416298f24c703
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93319641"
---
# <a name="regional-code-names-and-endpoints"></a>Regionalne nazwy kodu i punkty końcowe

### <a name="region-code-name"></a>Nazwa kodu regionu

Gdy parametr **Location** jest używany w poleceniu lub żądaniu, należy podać nazwę kodu regionu jako wartość **lokalizacji** . Aby uzyskać nazwę kodu regionu, w którym znajduje się konto, a połączenie powinno być kierowane, możesz uruchomić następujący wiersz w interfejsie wiersza polecenia platformy Azure.

```azurecli-interactive
az account list-locations
```

Po uruchomieniu pokazanego powyżej wiersza otrzymujesz listę wszystkich regionów świadczenia usługi Azure. Przejdź do regionu platformy Azure, dla którego szukasz *DisplayName* , i użyj jego wartości *nazwy* dla parametru **Location** .

Na przykład dla regionu Azure zachodnie stany USA 2 (wyświetlone poniżej) dla parametru **Location** zostanie użyta wartość "westus2".

```json
   {
      "displayName": "West US 2",
      "id": "/subscriptions/00000000-23da-4fce-b59c-f6fb9513eeeb/locations/westus2",
      "latitude": "47.233",
      "longitude": "-119.852",
      "name": "westus2",
      "subscriptionId": null
    }
```

## <a name="endpoints"></a>Punkty końcowe  

Następujące punkty końcowe są ważne, aby wiedzieć, jak nawiązać połączenie z kontami Media Services z różnych krajowych chmur platformy Azure.

### <a name="global-azure"></a>Globalny platformę Azure

| Usługa | Punkt końcowy |
| ------- | -------- |
| Azure Resource Manager |  `https://management.azure.com/` |
| Authentication | `https://login.microsoftonline.com/` |
| Odbiorcy tokenu | `https://management.core.windows.net/` |

### <a name="azure-government"></a>Azure Government

| Usługa | Punkt końcowy |
| ------- | -------- |
| Azure Resource Manager |  `https://management.usgovcloudapi.net/` |
| Authentication | `https://login.microsoftonline.us/` |
| Odbiorcy tokenu | `https://management.core.usgovcloudapi.net/` |

[!INCLUDE [Widevine is not available in the GovCloud region.](./includes/widevine-not-available-govcloud.md)]

### <a name="azure-germany"></a>Azure (Niemcy)

> [!NOTE]
> Punkty końcowe platformy Azure (Niemcy) stosują się tylko do suwerennych chmur w Niemczech.

| Usługa | Punkt końcowy |
| ------- | -------- |
| Azure Resource Manager | `https://management.cloudapi.de/` |
| Authentication | `https://login.microsoftonline.de/` |
| Odbiorcy tokenu | `https://management.core.cloudapi.de/`|

### <a name="azure-china-21vianet"></a>Azure w Chinach — 21Vianet

| Usługa | Punkt końcowy |
| ------- | -------- |
| Azure Resource Manager | `https://management.chinacloudapi.cn/` |
| Authentication | `https://login.chinacloudapi.cn/` |
| Odbiorcy tokenu |  `https://management.core.chinacloudapi.cn/` |

## <a name="see-also"></a>Zobacz także

* [Regiony platformy Azure](https://azure.microsoft.com/global-infrastructure/regions/)
* [Regionalne nazwy kodu i punkty końcowe](azure-regions-code-names.md)
* [Obszary geograficzne platformy Azure](https://azure.microsoft.com/global-infrastructure/geographies/)
* [Lokalizacje platformy Azure](https://azure.microsoft.com/global-infrastructure/locations/)

## <a name="next-steps"></a>Następne kroki

[Media Services wersja 3 — Omówienie](media-services-overview.md)
