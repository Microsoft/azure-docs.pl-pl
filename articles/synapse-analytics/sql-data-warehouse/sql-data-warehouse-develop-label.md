---
title: Używanie etykiet do Instrumentacji zapytań
description: Porady dotyczące używania etykiet do Instrumentacji zapytań dla dedykowanych pul SQL w usłudze Azure Synapse Analytics.
services: synapse-analytics
author: MSTehrani
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: emtehran
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 7958caf71658dcdcbf31bac84697931e9049452f
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96462742"
---
# <a name="using-labels-to-instrument-queries-for-dedicated-sql-pools-in-azure-synapse-analytics"></a>Używanie etykiet do Instrumentacji zapytań dla dedykowanych pul SQL w usłudze Azure Synapse Analytics

W tym artykule przedstawiono wskazówki dotyczące opracowywania rozwiązań przy użyciu etykiet do Instrumentacji zapytań w dedykowanych pulach SQL.

## <a name="what-are-labels"></a>Co to są etykiety?

Dedykowana Pula SQL obsługuje koncepcję o nazwie etykiety zapytań. Przed przejściem do dowolnej głębokości Przyjrzyjmy się przykładowi:

```sql
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query Label')
;
```

Ostatni wiersz określa ciąg "My Query Label" w zapytaniu. Ten tag jest przydatny, ponieważ etykieta jest z możliwością zapytania przez widoków DMV.

Wykonywanie zapytań dotyczących etykiet zapewnia mechanizm lokalizowania zapytań o problemy oraz ułatwiający identyfikację postępu przez uruchomienie ELT.

Dobrą konwencją nazewnictwa są naprawdę pomocne. Na przykład uruchomienie etykiety z projektem, procedurą, INSTRUKCJą lub KOMENTARZEm jednoznacznie identyfikuje zapytanie między całym kodem w kontroli źródła.

Następujące zapytanie używa dynamicznego widoku zarządzania do wyszukiwania według etykiety:

```sql
SELECT  *
FROM    sys.dm_pdw_exec_requests r
WHERE   r.[label] = 'My Query Label'
;
```

> [!NOTE]
> Podczas wykonywania zapytań należy umieścić nawiasy kwadratowe lub podwójne cudzysłowy wokół etykiety wyrazu. Etykieta jest słowem zastrzeżonym i powoduje błąd, gdy nie zostanie on rozdzielony.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej porad programistycznych, zobacz [Omówienie projektowania](sql-data-warehouse-overview-develop.md).
