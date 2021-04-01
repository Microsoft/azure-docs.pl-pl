---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/18/2020
ms.author: inhenkel
ms.custom: CLI
ms.openlocfilehash: 9f079be76d4d75f05b4a8e132ac425255eed8558
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88605972"
---
<!--Create a media services asset CLI-->

Następujące polecenie interfejsu wiersza polecenia platformy Azure tworzy nowy element zawartości Media Services. Zastąp wartości `assetName` `amsAccountName` i `resourceGroup` wartościami, dla których obecnie pracujesz.  

```azurecli
az ams asset create -n assetName -a amsAccountName -g resourceGroup
```
