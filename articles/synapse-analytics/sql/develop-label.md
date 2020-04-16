---
title: Używanie etykiet kwerend w języku Synapse SQL
description: Zawarte w tym artykule są podstawowe wskazówki dotyczące korzystania z etykiet zapytań w Synapse SQL.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.custom: ''
ms.openlocfilehash: 47b476cbc6997ca5ec63968bdc269e2273662100
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430034"
---
# <a name="use-query-labels-in-synapse-sql"></a>Używanie etykiet kwerend w języku Synapse SQL
Zawarte w tym artykule są podstawowe wskazówki dotyczące korzystania z etykiet zapytań w Synapse SQL.

> [!NOTE]
> SQL na żądanie (wersja zapoznawcza) nie obsługuje etykietowania zapytań.

## <a name="what-are-query-labels"></a>Co to są etykiety zapytań
Pula SQL obsługuje koncepcję o nazwie etykiety zapytań. Przed wejściem w jakąkolwiek głębię przyjrzyjmy się przykładowi:

```sql
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query Label')
;
```

Ostatni wiersz oznacza ciąg "Moja etykieta zapytania" do kwerendy. Ten tag jest szczególnie przydatny, ponieważ etykieta jest w stanie zapytania za pośrednictwem dmvs. Wykonywanie zapytań o etykiety zapewnia mechanizm lokalizowania zapytań o problemy i pomaga zidentyfikować postęp za pośrednictwem uruchomienia ELT.

Dobra konwencja nazewnictwa są najbardziej pomocne. Na przykład rozpoczęcie etykiety z PROJEKTU, PROCEDURY, INSTRUKCJI lub KOMENTARZ jednoznacznie identyfikuje kwerendę wśród całego kodu w formancie źródłowym.

Następująca kwerenda używa dynamicznego widoku zarządzania do wyszukiwania według etykiety:

```sql
SELECT  *
FROM    sys.dm_pdw_exec_requests r
WHERE   r.[label] = 'My Query Label'
;
```

> [!NOTE]
> Istotne jest, aby umieścić nawiasy kwadratowe lub cudzysłowy wokół etykiety słowa podczas wykonywania zapytań. Etykieta jest słowem zastrzeżonym i powoduje błąd, gdy nie jest rozdzielany. 
> 
> 

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej wskazówek dotyczących rozwoju, zobacz [omówienie rozwoju](develop-overview.md).


