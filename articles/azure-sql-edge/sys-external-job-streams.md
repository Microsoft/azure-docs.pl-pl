---
title: sys. external_job_streams (Transact-SQL) — Azure SQL Edge (wersja zapoznawcza)
description: Dowiedz się więcej o używaniu wykazu sys. external_job_streams w usłudze Azure SQL Edge (wersja zapoznawcza)
keywords: sys. external_job_streams, SQL Edge
services: sql-edge
ms.service: sql-edge
ms.topic: reference
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2019
ms.openlocfilehash: 2634128f4d431e4283f59032c6474a71f2af364d
ms.sourcegitcommit: f1132db5c8ad5a0f2193d751e341e1cd31989854
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/31/2020
ms.locfileid: "84233097"
---
# <a name="sysexternal_job_streams-transact-sql"></a>sys.external_job_streams (Transact-SQL)

Zwraca wiersz każdy dla obiektu strumienia zewnętrznego danych wejściowych lub wyjściowych mapowanych na zewnętrzne zadanie przesyłania strumieniowego.

|Nazwa kolumny|Typ danych|Opis|  
|-----------------|---------------|-----------------|
|**job_id**|**int**| Numer identyfikacyjny obiektu dla obiektu zadania przesyłania strumieniowego. Ta kolumna jest mapowana do kolumny object_id wykazu sys. external_streaming_jobs.|
|**stream_id**|**int**| Numer identyfikacyjny obiektu strumienia. Ta kolumna jest mapowana do kolumny object_id wykazu sys. external_streams. |
|**is_input**|**bit**| 1 Jeśli obiekt Stream jest używany jako dane wejściowe dla zadania przesyłania strumieniowego, w przeciwnym razie 0.|
|**is_output**|**bit**| 1, jeśli obiekt Stream jest używany w danych wyjściowych dla zadania przesyłania strumieniowego, w przeciwnym razie 0.|

## <a name="example"></a>Przykład

Ten widok wykazu jest używany razem z `sys.external_streams` `sys.external_streaming_jobs` widokami wykazu i. Poniżej przedstawiono przykładowe zapytanie

```sql
Select
    sj.Name as Job_Name,
    sj.Create_date as Job_Create_date,
    sj.modify_date as Job_Modify_date,
    sj.statement as Stream_Job_Query,
    Input_Stream_Name =
        Case js.is_input
            when 1 then s.Name
            else null
        END,
    output_Stream_Name =
        case js.is_output
            when 1 then s.Name
            else null
        END,
    s.location as Stream_Location
from sys.external_job_streams js
inner join sys.external_streams s on s.object_id = js.stream_id
inner join sys.external_streaming_jobs sj on sj.object_id = js.job_id
```

## <a name="permissions"></a>Uprawnienia

Widoczność metadanych w widokach wykazu jest ograniczona do zabezpieczania, do których użytkownik należy lub z którym użytkownik przyznał pewne uprawnienia. Aby uzyskać więcej informacji, zobacz [Konfiguracja widoczności metadanych](/sql/relational-databases/security/metadata-visibility-configuration/).

## <a name="see-also"></a>Zobacz także

- [Widoki wykazu (Transact-SQL)](/sql/relational-databases/system-catalog-views/catalog-views-transact-sql/)
- [Widoki systemowe (Transact-SQL)](/sql/t-sql/language-reference/)
