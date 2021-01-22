---
title: Konfigurowanie ważności obciążenia dla dedykowanej puli SQL
description: Dowiedz się, jak ustawić ważność poziomu żądania w usłudze Azure Synapse Analytics.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.subservice: sql-dw
ms.topic: conceptual
ms.date: 05/15/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 38fb842cf90c110266f53b79a9ab2ef6157025b4
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98681289"
---
# <a name="configure-workload-importance-in-dedicated-sql-pool-for-azure-synapse-analytics"></a>Skonfiguruj ważność obciążenia w dedykowanej puli SQL dla usługi Azure Synapse Analytics

Ustawienie znaczenia w dedykowanej puli SQL dla usługi Azure Synapse umożliwia planowanie zapytań. Zapytania o wyższym znaczeniu zostaną zaplanowane do uruchomienia przed zapytaniami o niższej ważności. Aby przypisać ważność do zapytań, należy utworzyć klasyfikator obciążeń.

## <a name="create-a-workload-classifier-with-importance"></a>Tworzenie klasyfikatora obciążeń o ważności

Często w scenariuszu hurtowni danych masz użytkowników, którzy korzystają z systemu, który musi szybko uruchamiać swoje zapytania.  Użytkownik może być dyrektorem firmy, którzy muszą uruchamiać raporty lub użytkownik może być analitykiem, na którym działa zapytanie ad hoc. Aby przypisać ważność, należy utworzyć klasyfikator obciążeń i znaczenie jest przypisane do zapytania.  W poniższych przykładach użyto składni  [klasyfikatora tworzenia obciążenia](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) , aby utworzyć dwa klasyfikatory. `Membername` może być pojedynczym użytkownikiem lub grupą.  Aby znaleźć istniejących dedykowanych użytkowników puli SQL, uruchom polecenie:

```sql
Select name from sys.sysusers
```

Aby utworzyć klasyfikator obciążeń dla użytkownika o wyższej ważności:

```sql
CREATE WORKLOAD CLASSIFIER ExecReportsClassifier
    WITH (WORKLOAD_GROUP = 'xlargerc'
         ,MEMBERNAME     = 'name' 
         ,IMPORTANCE     = above_normal);
```

Aby utworzyć klasyfikator obciążeń dla użytkownika, na którym są wykonywane zapytania ad hoc z niższym przebiegiem ważności:  

```sql
CREATE WORKLOAD CLASSIFIER AdhocClassifier
    WITH (WORKLOAD_GROUP = 'xlargerc'
         ,MEMBERNAME     = 'name' 
         ,IMPORTANCE     = below_normal);
```

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji na temat zarządzania obciążeniem, zobacz [Klasyfikacja obciążeń](sql-data-warehouse-workload-classification.md)
- Aby uzyskać więcej informacji na temat ważności, zobacz [ważność obciążenia](sql-data-warehouse-workload-importance.md)

> [!div class="nextstepaction"]
> [Przejdź do pozycji Zarządzaj i monitoruj ważność obciążenia](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md)
