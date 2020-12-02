---
title: Klasyfikacja obciążeń dla dedykowanej puli SQL
description: Wskazówki dotyczące używania klasyfikacji do zarządzania współbieżnością zapytań, ważnością i zasobami obliczeniowymi dla dedykowanej puli SQL w usłudze Azure Synapse Analytics.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: bf19e2d1674d0a0c2102280b28b5549505c1dfab
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96447776"
---
# <a name="workload-classification-for-dedicated-sql-pool-in-azure-synapse-analytics"></a>Klasyfikacja obciążenia dedykowanej puli SQL w usłudze Azure Synapse Analytics

W tym artykule opisano proces klasyfikacji obciążenia przypisujący grupę obciążeń i znaczenie żądań przychodzących z dedykowanymi pulami SQL w usłudze Azure Synapse.

## <a name="classification"></a>Klasyfikacja

> [!Video https://www.youtube.com/embed/QcCRBAhoXpM]

Klasyfikacja zarządzania obciążeniami umożliwia stosowanie zasad obciążeń do żądań przez przypisanie [klas zasobów](resource-classes-for-workload-management.md#what-are-resource-classes) i [ważności](sql-data-warehouse-workload-importance.md).

Istnieje wiele sposobów klasyfikowania obciążeń związanych z magazynem danych, tym najprostszą i najbardziej typową klasyfikacją jest obciążenie i wykonywanie zapytań. Ładowanie danych za pomocą instrukcji INSERT, Update i DELETE.  Wykonywanie zapytań dotyczących danych przy użyciu polecenia SELECT. Rozwiązanie do magazynowania danych często ma zasady obciążenia dla aktywności obciążenia, takie jak przypisanie wyższej klasy zasobów większej ilości zasobów. Różne zasady obciążenia mogą dotyczyć zapytań, takich jak mniejsza ważność porównana z działaniami ładowania.

Można również podzielić obciążenia na obciążenia i zapytania. Podklasyfikacja zapewnia większą kontrolę nad obciążeniami. Na przykład obciążenia zapytań mogą składać się z odświeżeń modułów, zapytań pulpitu nawigacyjnego lub zapytań ad hoc. Można sklasyfikować każde z tych obciążeń zapytań o różne klasy zasobów lub ustawienia ważności. Obciążenie może również korzystać z podklasyfikacji. Duże przekształcenia można przypisać do większych klas zasobów. Wyższe znaczenie może służyć do zapewnienia, że kluczowe dane sprzedaży są modułem ładującym przed danymi pogody lub strumieniowym źródłem danych społecznościowych.

Nie wszystkie instrukcje są klasyfikowane, ponieważ nie wymagają zasobów lub muszą mieć istotny wpływ na wykonanie.  Instrukcje poleceń DBCC, BEGIN, COMMIT i ROLLBACK TRANSACTION nie są klasyfikowane.

## <a name="classification-process"></a>Proces klasyfikacji

Klasyfikację dedykowanej puli SQL można już dzisiaj, przypisując użytkownikom do roli, która ma odpowiadającą jej klasę zasobów przypisaną do niej przy użyciu [sp_addrolemember](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest). Możliwość scharakteryzowania żądań poza logowaniem do klasy zasobów jest ograniczona tą możliwością. Bardziej zaawansowana Metoda klasyfikacji jest teraz dostępna ze składnią [klasyfikatora tworzenia obciążenia](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) .  Korzystając z tej składni, użytkownicy dedykowanej puli SQL mogą przypisywać ważność i ilość zasobów systemowych przypisanych do żądania za pośrednictwem `workload_group` parametru.

> [!NOTE]
> Klasyfikacja jest oceniana na podstawie żądania. Wiele żądań w jednej sesji może być klasyfikowanych inaczej.

## <a name="classification-weighting"></a>Klasyfikowanie klasyfikacji

W ramach procesu klasyfikacji jest stosowane ustalanie wagi, aby określić, która grupa obciążeń jest przypisana.  Waga odbywa się w następujący sposób:

|Parametr klasyfikatora |Waga   |
|---------------------|---------|
|CZŁONEKNAME: UŻYTKOWNIK      |64       |
|MEMBERNAME: ROLA      |32       |
|WLM_LABEL            |16       |
|WLM_CONTEXT          |8        |
|START_TIME/END_TIME  |4        |

`membername`Parametr jest obowiązkowy.  Jeśli jednak określona wartość elementu członkowskiego jest użytkownikiem bazy danych, a nie rolą bazy danych, oznacza to, że wagi dla użytkownika są wyższe i w ten sposób jest wybierany klasyfikator.

Jeśli użytkownik jest członkiem wielu ról z różnymi klasami zasobów przypisanymi lub dopasowanymi w wielu klasyfikatorach, użytkownik otrzymuje największe przypisanie klasy zasobów.  To zachowanie jest spójne z istniejącym zachowaniem przypisywania klasy zasobów.

## <a name="system-classifiers"></a>Klasyfikatory systemu

Klasyfikacja obciążenia ma klasyfikatory obciążenia systemu. Klasyfikatory systemu mapują istniejące członkostwa ról klasy zasobów na alokacje zasobów klasy zasobów o normalnym znaczeniu. Nie można porzucić klasyfikatorów systemowych. Aby wyświetlić klasyfikatory systemu, można uruchomić następujące zapytanie:

```sql
SELECT * FROM sys.workload_management_workload_classifiers where classifier_id <= 12
```

## <a name="mixing-resource-class-assignments-with-classifiers"></a>Mieszanie przypisań klas zasobów z klasyfikatorami

Klasyfikatory systemu utworzone w Twoim imieniu zapewniają łatwą ścieżkę do migracji do klasyfikacji obciążeń. Użycie mapowań roli klasy zasobów z pierwszeństwem klasyfikacji może prowadzić do błędnej klasyfikacji podczas tworzenia nowych klasyfikatorów o ważności.

Poniżej przedstawiono przykładowy scenariusz:

- Istniejący magazyn danych ma DBAUser użytkownika bazy danych przypisany do roli klasy zasobów largerc. Przypisanie klasy zasobów zostało wykonane z sp_addrolemember.
- Magazyn danych jest teraz aktualizowany za pomocą zarządzania obciążeniami.
- Aby przetestować nową składnię klasyfikacji, rola bazy danych DBARole (która DBAUser jest członkiem), ma klasyfikator utworzony dla ich mapowania na mediumrc i wysoką ważność.
- Gdy DBAUser loguje się i uruchamia zapytanie, zapytanie zostanie przypisane do largerc. Ponieważ użytkownik ma pierwszeństwo przed członkostwem w roli.

Aby uprościć Rozwiązywanie problemów z błędną klasyfikacją, zaleca się usunięcie mapowań ról klasy zasobów podczas tworzenia klasyfikatorów obciążeń.  Poniższy kod zwraca istniejące członkostwa ról klasy zasobów.  Uruchom [sp_droprolemember](/sql/relational-databases/system-stored-procedures/sp-droprolemember-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) dla każdej nazwy elementu członkowskiego zwróconej z odpowiedniej klasy zasobów.

```sql
SELECT  r.name AS [Resource Class]
,       m.name AS membername
FROM    sys.database_role_members rm
JOIN    sys.database_principals AS r ON rm.role_principal_id = r.principal_id
JOIN    sys.database_principals AS m ON rm.member_principal_id = m.principal_id
WHERE   r.name IN ('mediumrc','largerc','xlargerc','staticrc10','staticrc20','staticrc30','staticrc40','staticrc50','staticrc60','staticrc70','staticrc80');

--for each row returned run
sp_droprolemember '[Resource Class]', membername
```

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji na temat tworzenia klasyfikatora, zobacz [Tworzenie KLASYFIKATORA obciążenia (Transact-SQL)](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).  
- Zobacz Przewodnik Szybki Start dotyczący tworzenia klasyfikatora obciążeń [Tworzenie klasyfikatora obciążeń](quickstart-create-a-workload-classifier-tsql.md).
- Zapoznaj się z artykułami z artykułu jak, aby [skonfigurować ważność obciążenia](sql-data-warehouse-how-to-configure-workload-importance.md) oraz jak [zarządzać i monitorować zarządzanie obciążeniami](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md).
- Zobacz [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) , aby wyświetlać zapytania i przypisane znaczenie.
