---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: batch
author: laurenhughes
ms.service: batch
ms.topic: include
ms.date: 04/06/2018
ms.author: lahugh
ms.custom: include file
ms.openlocfilehash: 7ba4c90811bd8051ed9c307d9f9fa33e08e69dc7
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/26/2020
ms.locfileid: "67183584"
---
Zadanie uruchomione w Azure Batch może generować dane wyjściowe po uruchomieniu. Dane wyjściowe zadania często muszą być przechowywane do pobrania przez inne zadania w zadaniu, aplikacja kliencka, która wykonała zadanie. Zadania zapisują dane wyjściowe w systemie plików w węźle obliczeniowym wsadowym, ale wszystkie dane w węźle są tracone podczas jego tworzenia z obrazu lub gdy węzeł opuszcza pulę. Zadania mogą także mieć okres przechowywania plików, po którym pliki utworzone przez zadanie są usuwane. Z tego względu ważne jest, aby zachować dane wyjściowe zadania, które będą potrzebne później do magazynu danych, takiego jak [usługa Azure Storage](https://docs.microsoft.com/azure/storage/).

Opis opcji konta magazynu w usłudze Batch można znaleźć w temacie [Omówienie funkcji usługi Batch](../articles/batch/batch-api-basics.md#azure-storage-account).
