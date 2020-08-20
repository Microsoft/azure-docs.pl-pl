---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/18/2020
ms.author: inhenkel
ms.custom: dotnet
ms.openlocfilehash: 820353ffbb5e23e27c3039ad27a8f6507b9d2167
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2020
ms.locfileid: "88608895"
---
<!--Create a media services asset REST-->

Następujące polecenie platformy Azure .NET tworzy nowy element zawartości Media Services. Zastąp wartości `subscriptionID` , `resourceGroup` i wartościami, dla `amsAccountName` których obecnie pracujesz. Nadaj nazwę elementowi zawartości, ustawiając `assetName` tutaj.

```csharp
 Asset asset = await client.Assets.CreateOrUpdateAsync(resourceGroupName, accountName, assetName, new Asset());
```
