---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/20/2018
ms.author: glenga
ms.openlocfilehash: 82d122ed236dc72ced7ebafe2301ef5f1143897f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "76963663"
---
Aby zmaksymalizować wydajność, należy użyć oddzielnego konta magazynu dla każdej aplikacji funkcji. Jest to szczególnie ważne w przypadku Durable Functions lub funkcji wyzwalanych przez centrum zdarzeń, które generują duże ilości transakcji magazynu. Gdy logika aplikacji współdziała z usługą Azure Storage, bezpośrednio (przy użyciu zestawu SDK magazynu) lub za pośrednictwem jednego z powiązań magazynu, należy użyć dedykowanego konta magazynu. Na przykład jeśli masz funkcję wyzwalaną przez centrum zdarzeń, która zapisuje dane w usłudze BLOB Storage, użyj dwóch kont magazynu &mdash; jednej dla aplikacji funkcji i innej dla obiektów BLOB przechowywanych przez funkcję.