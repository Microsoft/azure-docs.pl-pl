---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/17/2020
ms.author: inhenkel
ms.custom: CLI
ms.openlocfilehash: d567a4f7d9b9429887d6396cb2b03dfc34c6ac93
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88602571"
---
<!-- Create a resource group -->

Użyj następującego polecenia, aby utworzyć grupę zasobów. Wybierz region geograficzny, który będzie używany do przechowywania multimediów i rekordów metadanych dla konta usługi Media Services. Ten region będzie używany do przetwarzania i przesyłania strumieniowego multimediów.

```azurecli
az group create --name amsResourceGroup --location westus2
```
