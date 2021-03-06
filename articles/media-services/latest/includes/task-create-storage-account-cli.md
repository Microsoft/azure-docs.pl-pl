---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/17/2020
ms.author: inhenkel
ms.custom: CLI
ms.openlocfilehash: a6349188a2c6b4da68009df93fbea5fa6eabacf1
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102244746"
---
<!-- ### Create a storage account -->

Podczas tworzenia konta usługi Media Services musisz podać nazwę zasobu konta usługi Azure Storage. Podane konto magazynu jest dołączane do konta usługi Media Services. Aby uzyskać więcej informacji na temat używania kont magazynu w usłudze Media Services, zobacz [Konta magazynu](../storage-account-concept.md).

Musisz mieć jedno **główne** konto magazynu i możesz mieć dowolną liczbę **dodatkowych** kont magazynu skojarzonych z Twoim kontem usługi Media Services. Usługa Media Services obsługuje konta **Ogólnego przeznaczenia, wersja 2** (GPv2) i **Ogólnego przeznaczenia, wersja 1** (GPv1). Konta tylko obiektów blob nie są dozwolone jako **główne**. Jeśli chcesz dowiedzieć się więcej o kontach magazynu, zobacz [Opcje konta usługi Azure Storage](../../../storage/common/storage-account-overview.md). 

W tym przykładzie utworzymy konto ogólnego przeznaczenia w wersji 2 magazynu LRS w warstwie Standardowa. Jeśli chcesz poeksperymentować z kontami magazynu, użyj parametru `--sku Standard_LRS`. Jednak podczas wybierania jednostki SKU dla środowiska produkcyjnego weź pod uwagę użycie parametru `--sku Standard_RAGRS`, co zapewnia replikację geograficzną na potrzeby zapewnienia ciągłości działania. Aby uzyskać więcej informacji, zobacz temat [Konta magazynu](/cli/azure/storage/account).

Poniższe polecenie tworzy konto usługi Storage, które ma zostać skojarzone z kontem usługi Media Services. W poniższym skrypcie możesz zastąpić wartość `storageaccountforams` swoją wartością. `amsResourceGroup` musi być zgodna z wartością podaną dla grupy zasobów w poprzednim kroku. Nazwa konta magazynu musi mieć długość mniejszą niż 24.

```azurecli
az storage account create --name storageaccountforams --kind StorageV2 --sku Standard_LRS -l westus2 -g amsResourceGroup
```
