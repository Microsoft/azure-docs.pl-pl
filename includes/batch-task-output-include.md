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
ms.openlocfilehash: eaa76153fe96b5fd41166b20770e0a969aa9260d
ms.sourcegitcommit: a9784a3fd208f19c8814fe22da9e70fcf1da9c93
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/22/2020
ms.locfileid: "83797138"
---
Zadanie uruchomione w Azure Batch może generować dane wyjściowe po uruchomieniu. Dane wyjściowe zadania często muszą być przechowywane do pobrania przez inne zadania w zadaniu, aplikacja kliencka, która wykonała zadanie. Zadania zapisują dane wyjściowe w systemie plików w węźle obliczeniowym wsadowym, ale wszystkie dane w węźle są tracone podczas jego tworzenia z obrazu lub gdy węzeł opuszcza pulę. Zadania mogą także mieć okres przechowywania plików, po którym pliki utworzone przez zadanie są usuwane. Z tego względu ważne jest, aby zachować dane wyjściowe zadania, które będą potrzebne później do magazynu danych, takiego jak [usługa Azure Storage](https://docs.microsoft.com/azure/storage/).

Aby uzyskać opcje konta magazynu w usłudze Batch, zobacz [konta usługi Batch i konta magazynu Azure](../articles/batch/accounts.md#azure-storage-accounts).
