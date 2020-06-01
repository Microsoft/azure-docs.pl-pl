---
title: ALTER EXTERNAL STREAM (Transact-SQL) — Azure SQL Edge (wersja zapoznawcza)
description: Dowiedz się więcej o instrukcji ALTER EXTERNAL STREAM w usłudze Azure SQL Edge (wersja zapoznawcza)
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 2559c4b4b875403b7c70671e27cb6222a3f1103a
ms.sourcegitcommit: f1132db5c8ad5a0f2193d751e341e1cd31989854
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/31/2020
ms.locfileid: "84235193"
---
# <a name="alter-external-stream-transact-sql"></a>ALTER EXTERNAL STREAM (Transact-SQL)

Modyfikuje definicję strumienia zewnętrznego. Modyfikowanie zewnętrznego strumienia, który jest używany przez zadanie przesyłania strumieniowego w stanie *uruchomionym* , jest niedozwolone. 



## <a name="syntax"></a>Składnia

```sql
  ALTER EXTERNAL STREAM external_stream_name 
  SET 
    [DATA_SOURCE] = <data_source_name> 
    , LOCATION = <location_name> 
    , EXTERNAL_FILE_FORMAT = <external_file_format_name> 
    , INPUT_OPTIONS = <input_options> 
    , OUTPUT_OPTIONS = <output_options> 
```

## <a name="arguments"></a>Argumenty

Aby uzyskać szczegółowe informacje na temat argumentów polecenia ALTER External Stream, zobacz [Create External Stream (Transact-SQL)](create-external-stream-transact-sql.md).

## <a name="return-code-values"></a>Zwróć wartości kodu

Instrukcja ALTER EXTERNAL STREAM zwraca wartość 0, jeśli powodzenie. Wartość zwracana różna od zera wskazuje niepowodzenie.


## <a name="see-also"></a>Zobacz także

- [Utwórz strumień zewnętrzny (Transact-SQL)](create-external-stream-transact-sql.md) 
- [Usuń strumień zewnętrzny (Transact-SQL)](drop-external-stream-transact-sql.md) 
