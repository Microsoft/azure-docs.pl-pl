---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/17/2020
ms.author: inhenkel
ms.custom: CLI
ms.openlocfilehash: d567a4f7d9b9429887d6396cb2b03dfc34c6ac93
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88602571"
---
<!-- Create a resource group -->

Użyj następującego polecenia, aby utworzyć grupę zasobów. Wybierz region geograficzny, który będzie używany do przechowywania multimediów i rekordów metadanych dla konta usługi Media Services. Ten region będzie używany do przetwarzania i przesyłania strumieniowego multimediów.

```azurecli
az group create --name amsResourceGroup --location westus2
```
