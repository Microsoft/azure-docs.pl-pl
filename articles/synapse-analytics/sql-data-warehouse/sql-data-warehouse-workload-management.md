---
title: Zarządzanie obciążeniem
description: Wskazówki dotyczące implementowania zarządzania obciążeniami w usłudze Azure Synapse Analytics.
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
ms.openlocfilehash: 5640c1e254c4738ab53881544a09808b4894a462
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98676973"
---
# <a name="what-is-workload-management"></a>Co to jest zarządzanie obciążeniami?

Uruchamianie obciążeń mieszanych może stanowić wyzwanie dla zasobów w systemach zajętych.  Architektzy rozwiązań poszukują sposobów rozdzielania klasycznych działań związanych z magazynem danych (takich jak ładowanie, przekształcanie i wykonywanie zapytań dotyczących danych), aby upewnić się, że istnieje wystarczająca ilość zasobów do trafienia umowy SLA.  

Izolacja serwera fizycznego może prowadzić do kieszeni na infrastrukturę, która jest niedostatecznie wykorzystywana, zastrzeżona lub w stanie, w którym bufory są stale uruchamiane z sprzętem, który jest uruchamiany i zatrzymywany.  Pomyślny schemat zarządzania obciążeniami skutecznie zarządza zasobami, zapewnia wysoce wydajne wykorzystanie zasobów i maksymalizuje zwrot z inwestycji.

Obciążenie magazynu danych odnosi się do wszystkich operacji, które wykonywanych w odniesieniu do hurtowni danych. Głębokość i szerokość tych składników zależą od poziomu dojrzałości hurtowni danych.  Obciążenia magazynu danych obejmują:

- Cały proces ładowania danych do magazynu
- Wykonywanie analizy i raportowania hurtowni danych
- Zarządzanie danymi w magazynie danych
- Eksportowanie danych z magazynu danych

Wydajność hurtowni danych jest określana przez [jednostki magazynu danych](what-is-a-data-warehouse-unit-dwu-cdwu.md).

- Aby wyświetlić zasoby przydzieloną dla wszystkich profilów wydajności, zobacz [limity pamięci i współbieżności](memory-concurrency-limits.md).
- W celu dostosowania pojemności można [skalować w górę lub w dół](quickstart-scale-compute-portal.md).

## <a name="workload-management-concepts"></a>Pojęcia związane z zarządzaniem obciążeniem

W przeszłości Synapse SQL na platformie Azure Synapse zarządzanie wydajnością zapytań za pomocą [klas zasobów](resource-classes-for-workload-management.md).  Klasy zasobów dozwolone do przypisywania pamięci do zapytania na podstawie przynależności do roli.  Podstawowym wyzwaniem z klasami zasobów jest to, że po skonfigurowaniu nie było żadnego ładu ani kontrolować obciążenia.  

Załóżmy na przykład, że przyznanie roli użytkownika ad hoc do smallrc zezwala temu użytkownikowi na korzystanie z 100% pamięci w systemie.  W przypadku klas zasobów nie ma możliwości zarezerwowania i zapewnienia dostępności zasobów dla obciążeń krytycznych.

Dedykowane zarządzanie obciążeniem puli SQL w usłudze Azure Synapse składa się z trzech koncepcji wysokiego poziomu: [klasyfikacji obciążeń](sql-data-warehouse-workload-classification.md), [ważności obciążeń](sql-data-warehouse-workload-importance.md)i [izolacji obciążeń](sql-data-warehouse-workload-isolation.md).  Te funkcje zapewniają większą kontrolę nad sposobem, w jaki obciążenie wykorzystuje zasoby systemowe.

Klasyfikacja obciążenia to koncepcja przypisywania żądania do grupy obciążeń i ustawiania poziomów ważności.  W przeszłości to przypisanie zostało wykonane za pośrednictwem przynależności do roli przy użyciu [sp_addrolemember](resource-classes-for-workload-management.md#change-a-users-resource-class).  Tę akcję można teraz wykonać za pośrednictwem [tworzenia obciążenia CLASSIFER](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).  Funkcja klasyfikacji oferuje bogatszy zestaw opcji, takich jak etykieta, sesja i czas, aby sklasyfikować żądania.

Znaczenie obciążeń ma wpływ na kolejność, w jakiej żądanie uzyskuje dostęp do zasobów.  W przypadku zajętego systemu żądanie o wyższym znaczeniu ma pierwszy dostęp do zasobów.  Ważność może także zapewnić uporządkowany dostęp do blokad.

Izolacja obciążenia rezerwuje zasoby dla grupy obciążenia.  Zasoby zarezerwowane w grupie obciążenia są przechowywane wyłącznie dla tej grupy obciążenia, aby zapewnić wykonanie.  Grupy obciążeń umożliwiają również zdefiniowanie ilości zasobów, które są przypisane na żądanie, podobnie jak klasy zasobów.  Grupy obciążeń zapewniają możliwość zarezerwowania lub zakończenia ilości zasobów, które mogą być używane przez zestaw żądań.  Na koniec grupy obciążeń to mechanizm do zastosowania reguł, takich jak przekroczenie limitu czasu zapytania, do żądań.  

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji na temat klasyfikacji obciążeń, zobacz [Klasyfikacja obciążeń](sql-data-warehouse-workload-classification.md).  
- Aby uzyskać więcej informacji o izolacji obciążenia, zobacz [izolacja obciążenia](sql-data-warehouse-workload-isolation.md).  
- Aby uzyskać więcej informacji o ważności obciążenia, zobacz [ważność obciążenia](sql-data-warehouse-workload-importance.md).  
- Aby uzyskać więcej informacji na temat monitorowania zarządzania obciążeniem, zobacz [monitorowanie Portal zarządzania obciążenia](sql-data-warehouse-workload-management-portal-monitor.md).  
