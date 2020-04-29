---
title: Konfigurowanie znaczenia obciążenia
description: Dowiedz się, jak ustawić ważność poziomu żądania w usłudze Azure Synapse Analytics.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.subservice: ''
ms.topic: conceptual
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 0ab7b8be8780f7edb2734d99587bc7709ced9436
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80633365"
---
# <a name="configure-workload-importance-in-azure-synapse-analytics"></a>Konfigurowanie ważności obciążenia w usłudze Azure Synapse Analytics

Ustawienie znaczenia w Synapse SQL dla usługi Azure Synapse pozwala na wpływ na planowanie zapytań. Zapytania o wyższym znaczeniu zostaną zaplanowane do uruchomienia przed zapytaniami o niższej ważności. Aby przypisać ważność do zapytań, należy utworzyć klasyfikator obciążeń.

## <a name="create-a-workload-classifier-with-importance"></a>Tworzenie klasyfikatora obciążeń o ważności

Często w scenariuszu hurtowni danych masz użytkowników, którzy potrzebują swoich zapytań do szybkiego uruchomienia.  Użytkownik może być dyrektorem firmy, którzy muszą uruchamiać raporty lub użytkownik może być analitykiem, na którym działa zapytanie ad hoc. Tworzysz klasyfikator obciążeń, aby przypisać ważność do zapytania.  W poniższych przykładach użyto nowej składni [klasyfikatora tworzenia obciążenia](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) w celu utworzenia dwóch klasyfikatorów. `Membername`może być pojedynczym użytkownikiem lub grupą. Klasyfikacje poszczególnych użytkowników mają pierwszeństwo przed klasyfikacjami ról. Aby znaleźć istniejących użytkowników magazynu danych, uruchom polecenie:

```sql
Select name from sys.sysusers
```

Aby utworzyć klasyfikator obciążeń dla użytkownika o wyższej ważności:

```sql
CREATE WORKLOAD CLASSIFIER ExecReportsClassifier  
    WITH (WORKLOAD_GROUP = 'xlargerc'
         ,MEMBERNAME     = 'name'  
         ,IMPORTANCE     =  above_normal);  

```

Aby utworzyć klasyfikator obciążeń dla użytkownika, na którym są wykonywane zapytania ad hoc z niższym przebiegiem ważności:  

```sql
CREATE WORKLOAD CLASSIFIER AdhocClassifier  
    WITH (WORKLOAD_GROUP = 'xlargerc'
         ,MEMBERNAME     = 'name'  
         ,IMPORTANCE     =  below_normal);  
```

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji na temat zarządzania obciążeniem, zobacz [Klasyfikacja obciążeń](sql-data-warehouse-workload-classification.md)
- Aby uzyskać więcej informacji na temat ważności, zobacz [ważność obciążenia](sql-data-warehouse-workload-importance.md)

> [!div class="nextstepaction"]
> [Przejdź do pozycji Zarządzaj i monitoruj ważność obciążenia](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md)
