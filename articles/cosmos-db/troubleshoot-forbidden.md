---
title: Rozwiązywanie problemów z niedozwolonymi wyjątkami Azure Cosmos DB
description: Dowiedz się, jak zdiagnozować i naprawić niedozwolone wyjątki.
author: ealsur
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 02/05/2021
ms.author: maquaran
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 0854e5d2c323df695460908c45714673756328c2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99971912"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-forbidden-exceptions"></a>Diagnozowanie i rozwiązywanie problemów Azure Cosmos DB zabronionych wyjątków
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Kod stanu HTTP 403 oznacza, że żądanie jest zabronione.

## <a name="firewall-blocking-requests"></a>Żądania blokowania zapory
W tym scenariuszu często wyświetlane są błędy podobne do poniższych:

```
Request originated from client IP {...} through public internet. This is blocked by your Cosmos DB account firewall settings.
```

```
Request is blocked. Please check your authorization token and Cosmos DB account firewall settings
```

### <a name="solution"></a>Rozwiązanie
Sprawdź, czy bieżące [Ustawienia zapory](how-to-configure-firewall.md) są poprawne i Dołącz adresy IP lub sieci, z których próbujesz nawiązać połączenie.
Jeśli zostały one niedawno zaktualizowane, należy pamiętać, że zastosowanie zmian może potrwać **do 15 minut**.

## <a name="next-steps"></a>Następne kroki
* Skonfiguruj [zaporę IP](how-to-configure-firewall.md).
* Skonfiguruj dostęp z [sieci wirtualnych](how-to-configure-vnet-service-endpoint.md).
* Skonfiguruj dostęp z [prywatnych punktów końcowych](how-to-configure-private-endpoints.md).
