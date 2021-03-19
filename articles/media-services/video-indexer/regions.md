---
title: Regiony, w których Video Indexer jest dostępna — Azure
titleSuffix: Azure Media Services
description: Ten artykuł zawiera informacje na temat regionów świadczenia usługi Azure, w których Video Indexer Azure Media Services jest dostępna.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 09/14/2020
ms.author: juliako
ms.openlocfilehash: 6ebdb22f50efbefc695f9752c6e6fc333571828c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "90530945"
---
# <a name="azure-regions-in-which-video-indexer-exists"></a>Regiony platformy Azure, w których istnieje Video Indexer

Interfejsy API Video Indexer zawierają parametr **lokalizacji** , który należy ustawić dla regionu platformy Azure, do którego ma zostać przekierowane wywołanie. Musi to być [region platformy Azure, w którym Video Indexer jest dostępna](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services&regions=all).

## <a name="locations"></a>Lokalizacje

`location`Jako wartość parametru musi być określona nazwa kodu regionu platformy Azure. Jeśli używasz Video Indexer w trybie wersji zapoznawczej, należy ustawić `"trial"` jako wartość. `trial` jest wartością domyślną dla `location` parametru. W przeciwnym razie, aby uzyskać nazwę kodową regionu platformy Azure, w którym znajduje się konto, a połączenie powinno być kierowane do, można użyć Azure Portal lub uruchomić polecenie [interfejsu wiersza polecenia platformy Azure](/cli/azure) .

### <a name="azure-portal"></a>Azure Portal

1. Zaloguj się w witrynie internetowej usługi [Video Indexer](https://www.videoindexer.ai/).
1. Wybierz pozycję **konta użytkowników** w prawym górnym rogu strony.
1. Znajdź lokalizację swojego konta w prawym górnym rogu.  

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/location/location1.png" alt-text="Lokalizacja":::
    
###  <a name="cli-command"></a>Interfejs wiersza polecenia

```azurecli-interactive
az account list-locations
```

Po uruchomieniu pokazanego powyżej wiersza otrzymujesz listę wszystkich regionów świadczenia usługi Azure. Przejdź do regionu platformy Azure, dla którego szukasz *DisplayName* , i użyj jego wartości *nazwy* dla parametru **Location** .

Na przykład dla regionu Azure zachodnie stany USA 2 (wyświetlone poniżej) dla parametru **Location** zostanie użyta wartość "westus2".

```json
   {
      "displayName": "West US 2",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/locations/westus2",
      "latitude": "47.233",
      "longitude": "-119.852",
      "name": "westus2",
      "subscriptionId": null
    }
```

## <a name="next-steps"></a>Następne kroki

- [Dostosowywanie modelu języka przy użyciu interfejsów API](customize-language-model-with-api.md)
- [Dostosowywanie modelu marek przy użyciu interfejsów API](customize-brands-model-with-api.md)
- [Dostosuj model osoby przy użyciu interfejsów API](customize-person-model-with-api.md)
