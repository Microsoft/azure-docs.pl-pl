---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/17/2020
ms.author: inhenkel
ms.custom: CLI
ms.openlocfilehash: 5c0341087cdd348e973da5faaa1f90081780c9c8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88602531"
---
<!--Create a media services account -->

Poniższe polecenie interfejsu wiersza polecenia platformy Azure tworzy nowe konto usługi Media Services. Można zastąpić następujące wartości: `amsaccount` `storageaccountforams` (musi być zgodna z wartością podaną dla konta magazynu) i `amsResourceGroup` (musi być zgodna z wartością podaną dla grupy zasobów).  

```azurecli
az ams account create --name amsaccount -g amsResourceGroup --storage-account storageaccountforams -l westus2
```