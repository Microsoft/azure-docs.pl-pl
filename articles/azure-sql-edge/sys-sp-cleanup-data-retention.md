---
title: sys.sp_cleanup_data_retention (Transact-SQL) — Azure SQL Edge
description: Dowiedz się więcej o używaniu sys.sp_cleanup_data_retention (Transact-SQL) w usłudze Azure SQL Edge
keywords: sys.sp_cleanup_data_retention (Transact-SQL), SQL Edge
services: sql-edge
ms.service: sql-edge
ms.topic: reference
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: 9c0a6700a476d4f7f875af5373e3c99bc4e25af2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "90938636"
---
# <a name="syssp_cleanup_data_retention-transact-sql"></a>sys.sp_cleanup_data_retention (Transact-SQL)

**Dotyczy:** Usługa Azure SQL Edge

Wykonuje czyszczenie przestarzałych rekordów z tabel z włączonymi zasadami przechowywania danych. Aby uzyskać więcej informacji, zobacz [przechowywanie danych](data-retention-overview.md). 

## <a name="syntax"></a>Składnia 

```sql
sys.sp_cleanup_data_retention   
    { [@schema_name = ] 'schema_name' },  
    { [@table_name = ] 'table_name' },   
    [ [@rowcount =] rowcount OUTPUT ]    

```

## <a name="arguments"></a>Argumenty  
`[ @schema_name = ] schema_name`    
 Jest nazwą schematu będącego właścicielem dla tabeli, w której należy wykonać oczyszczanie. *schema_name* jest wymaganym parametrem typu **bazy**.
  
`[ @table_name = ] 'table_name'`    
 Jest nazwą tabeli, w której należy wykonać operację czyszczenia. *table_name* jest wymaganym parametrem typu **bazy**.

## <a name="output-parameter"></a>Parametr wyjściowy  

`[ @rowcount = ] rowcount OUTPUT`   
 rowcount jest opcjonalnym parametrem WYJŚCIOWYm, który reprezentuje liczbę rekordów oczyszczania z tabeli. liczba *wierszy* to int.
  
## <a name="permissions"></a>Uprawnienia    
 Wymaga uprawnień db_owner.

## <a name="next-steps"></a>Następne kroki
- [Przechowywanie danych i automatyczne czyszczenie danych](data-retention-overview.md)
- [Zarządzanie danymi historycznymi przy użyciu zasad przechowywania](data-retention-cleanup.md) 
- [Włączanie i wyłączanie przechowywania danych](data-retention-enable-disable.md)
