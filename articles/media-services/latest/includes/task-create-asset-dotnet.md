---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/18/2020
ms.author: inhenkel
ms.custom: dotnet
ms.openlocfilehash: dcd2cda3bad2a13a83c5f3f6700e5a57471e2065
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88653894"
---
<!--Create a media services asset REST-->

Następujące polecenie platformy Azure .NET tworzy nowy element zawartości Media Services. Zastąp wartości `subscriptionID` , `resourceGroup` i wartościami, dla `amsAccountName` których obecnie pracujesz. Nadaj nazwę elementowi zawartości, ustawiając `assetName` tutaj.

[!code-csharp[Main](../../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateInputAsset)]
