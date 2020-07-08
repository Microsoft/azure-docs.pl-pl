---
title: sys. external_streaming_jobs (Transact-SQL) — Azure SQL Edge (wersja zapoznawcza)
description: Dowiedz się więcej o używaniu wykazu sys. external_streaming_jobs w usłudze Azure SQL Edge (wersja zapoznawcza)
keywords: sys. external_streaming_jobs, SQL Edge
services: sql-edge
ms.service: sql-edge
ms.topic: reference
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2019
ms.openlocfilehash: c4da73e3197df894a0726556b4e92141818a520e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84233070"
---
# <a name="sysexternal_streaming_jobs-transact-sql"></a>sys. external_streaming_jobs (Transact-SQL)

Zwraca wiersz dla każdego zadania przesyłania strumieniowego zewnętrznego utworzonego w ramach zakresu bazy danych.

|Nazwa kolumny|Typ danych|Opis|  
|-----------------|---------------|-----------------|
|**Nazwij**|**bazy**|Nazwa strumienia. Jest unikatowa w ramach bazy danych.|
|**object_id**|**int**|Numer identyfikacyjny obiektu strumienia. Jest unikatowa w ramach bazy danych.|
|**principal_id**|**int**|Identyfikator podmiotu zabezpieczeń, który jest właścicielem tego zestawu|
|**schema_id**|**int**| Identyfikator schematu, który zawiera obiekt.|
|**parent_object_id**|**#c1**| Numer identyfikacyjny obiektu dla obiektu nadrzędnego dla tego strumienia. W bieżącej implementacji ta wartość jest zawsze równa null.|
|**Wprowadź**|**char (2)**|Typ obiektu. Dla obiektów strumienia typem jest zawsze "EJ"|
|**type_desc**|**nvarchar (60)**| Opis typu obiektu. W przypadku obiektów strumienia typ jest zawsze "EXTERNAL_STREAMING_JOB"|
|**create_date**|**datetime**| Data utworzenia obiektu.|
|**modify_date**|**datetime**| W bieżącej implementacji ta wartość jest taka sama jak create_date obiektu Stream |
|**is_ms_shipped**|**bit**| Obiekt utworzony przez składnik wewnętrzny.|  
|**is_published**|**bit**| Obiekt został opublikowany.|  
|**is_schema_published**|**bit**|Tylko schemat obiektu jest publikowany.|
|**uses_ansi_nulls**|**bit**| Obiekt Stream został utworzony przy użyciu opcji Ustaw bazę danych ANSI_NULLS|
|**Merge**|**varchar(max)**| Tekst zapytania usługi Stream Analytics dla zadania przesyłania strumieniowego. Aby uzyskać więcej informacji, zobacz [sp_create_streaming_job](overview.md) |
|**Stany**|**int**| Bieżący stan zadania przesyłania strumieniowego. Możliwe wartości to <br /><br /> **Utworzono** = 0. Zadanie przesyłania strumieniowego zostało utworzone, ale jeszcze nie zostało uruchomione. <br /><br /> **Rozpoczęcie** = 1. Zadanie przesyłania strumieniowego jest w fazie początkowej. <br /><br /> **Niepowodzenie** = 6. Zadanie przesyłania strumieniowego nie powiodło się. Jest to zazwyczaj wskaźnik błędu krytycznego podczas przetwarzania. <br /><br /> **Zatrzymano** = 4. Zadanie przesyłania strumieniowego zostało zatrzymane. <br /><br /> **Bezczynne** = 7. Zadanie przesyłania strumieniowego jest uruchomione, ale nie ma danych wejściowych do przetworzenia. <br /><br /> **Przetwarzanie** = 8. Zadanie przesyłania strumieniowego jest uruchomione i przetwarza dane wejściowe. Ten stan wskazuje na prawidłowy stan zadania przesyłania strumieniowego. <br /><br /> **Obniżona wydajność** = 9. Zadanie przesyłania strumieniowego jest uruchomione, ale wystąpiły Niekrytyczne błędy serializacji/deserializacji operacji wejścia/wyjścia podczas przetwarzania danych wejściowych. Zadanie wejściowe będzie nadal uruchamiane, ale spowoduje porzucenie danych wejściowych, które napotkały błędy.|

## <a name="permissions"></a>Uprawnienia

Widoczność metadanych w widokach wykazu jest ograniczona do zabezpieczania, do których użytkownik należy lub z którym użytkownik przyznał pewne uprawnienia. Aby uzyskać więcej informacji, zobacz [Konfiguracja widoczności metadanych](/sql/relational-databases/security/metadata-visibility-configuration/).

## <a name="see-also"></a>Zobacz także

- [Widoki wykazu przesyłania strumieniowego języka T-SQL](overview.md)
- [Widoki wykazu (Transact-SQL)](/sql/relational-databases/system-catalog-views/catalog-views-transact-sql/)
- [Widoki systemowe (Transact-SQL)](/sql/t-sql/language-reference/)

