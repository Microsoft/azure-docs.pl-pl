---
title: sys.external_streams (Transact-SQL) — Azure SQL Edge
description: Dowiedz się więcej o używaniu sys.external_streams w usłudze Azure SQL Edge
keywords: sys.external_streams, SQL Edge
services: sql-edge
ms.service: sql-edge
ms.topic: reference
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2019
ms.openlocfilehash: 04950f01c06bc3c8ed3bb11a790310c2319a0579
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "90900301"
---
# <a name="sysexternal_streams-transact-sql"></a>sys.external_streams (Transact-SQL)

Zwraca wiersz dla każdego zewnętrznego obiektu strumienia utworzonego w zakresie bazy danych.

|Nazwa kolumny|Typ danych|Opis|  
|-----------------|---------------|-----------------|
|**Nazwij**|**bazy**|Nazwa strumienia. Jest unikatowa w ramach bazy danych.|
|**object_id**|**int**|Numer identyfikacyjny obiektu strumienia. Jest unikatowa w ramach bazy danych.|
|**principal_id**|**int**|Identyfikator podmiotu zabezpieczeń, który jest właścicielem tego zestawu|
|**schema_id**|**int**| Identyfikator schematu, który zawiera obiekt.|
|**parent_object_id**|**id**| Numer identyfikacyjny obiektu dla obiektu nadrzędnego dla tego strumienia. W bieżącej implementacji ta wartość jest zawsze równa null.|
|**Wprowadź**|**char (2)**|Typ obiektu. W przypadku obiektów strumienia typ ma zawsze wartość "ES"|
|**type_desc**|**nvarchar (60)**| Opis typu obiektu. W przypadku obiektów strumienia typ jest zawsze "EXTERNAL_STREAM"|
|**create_date**|**datetime**| Data utworzenia obiektu.|
|**modify_date**|**datetime**| Data ostatniej modyfikacji obiektu przy użyciu instrukcji ALTER.|
|**is_ms_shipped**|**bit**| Obiekt utworzony przez składnik wewnętrzny.|  
|**is_published**|**bit**|Obiekt został opublikowany.|  
|**is_schema_published**|**bit**|Tylko schemat obiektu jest publikowany.|
|**max_column_id_used**|**bit**| Ta kolumna jest używana do celów wewnętrznych i zostanie usunięta w przyszłości|  
|**uses_ansi_nulls**|**bit**| Obiekt Stream został utworzony przy użyciu opcji Ustaw bazę danych ANSI_NULLS|
|**data_source_id**|**int**| Identyfikator obiektu zewnętrznego źródła danych reprezentowanego przez obiekt Stream |  
|**file_format_id**|**int**| Identyfikator obiektu dla formatu pliku zewnętrznego używany przez obiekt strumienia. Format pliku zewnętrznego jest wymagany do określenia rzeczywistego układu danych, do których odwołuje się strumień zewnętrzny.| 
|**przeniesienie**|**varchar(max)**| Obiekt docelowy obiektu strumienia zewnętrznego. Aby uzyskać więcej informacji, zobacz [Tworzenie strumienia zewnętrznego](overview.md) |
|**input_option**|**varchar(max)**| Opcje wejściowe używane podczas tworzenia strumienia zewnętrznego. Aby uzyskać więcej informacji, zobacz [Tworzenie strumienia zewnętrznego](overview.md) |
|**output_option**|**varchar(max)**| Opcje wyjściowe używane podczas tworzenia strumienia zewnętrznego. Aby uzyskać więcej informacji, zobacz [Tworzenie strumienia zewnętrznego](overview.md) | 

## <a name="permissions"></a>Uprawnienia

Widoczność metadanych w widokach wykazu jest ograniczona do zabezpieczania, do których użytkownik należy lub z którym użytkownik przyznał pewne uprawnienia. Aby uzyskać więcej informacji, zobacz [Konfiguracja widoczności metadanych](/sql/relational-databases/security/metadata-visibility-configuration/).

## <a name="see-also"></a>Zobacz też

- [Widoki wykazu (Transact-SQL)](/sql/relational-databases/system-catalog-views/catalog-views-transact-sql/)
- [Widoki systemowe (Transact-SQL)](/sql/t-sql/language-reference/)
