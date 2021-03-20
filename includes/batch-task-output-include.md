---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: batch
author: JnHs
ms.service: batch
ms.topic: include
ms.date: 04/06/2018
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: 019e8db54c1cfd9f436f880b8ddbb9bfa31c50bc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96026614"
---
Zadanie uruchomione w Azure Batch może generować dane wyjściowe po uruchomieniu. Dane wyjściowe zadania często muszą być przechowywane do pobrania przez inne zadania w zadaniu, aplikacja kliencka, która wykonała zadanie. Zadania zapisują dane wyjściowe w systemie plików w węźle obliczeniowym wsadowym, ale wszystkie dane w węźle są tracone podczas jego tworzenia z obrazu lub gdy węzeł opuszcza pulę. Zadania mogą także mieć okres przechowywania plików, po którym pliki utworzone przez zadanie są usuwane. Z tego względu ważne jest, aby zachować dane wyjściowe zadania, które będą potrzebne później do magazynu danych, takiego jak [usługa Azure Storage](../articles/storage/index.yml).

Aby uzyskać opcje konta magazynu w usłudze Batch, zobacz [konta usługi Batch i konta magazynu Azure](../articles/batch/accounts.md#azure-storage-accounts).