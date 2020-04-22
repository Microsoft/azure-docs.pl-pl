---
title: Tworzenie projektów usługi Azure Migrate i zarządzanie nimi
description: Znajdowanie projektów, tworzenie, zarządzanie nimi i usuwanie ich podczas migracji na platformie Azure.
ms.topic: how-to
ms.date: 04/19/2020
ms.openlocfilehash: f5079ed979d98f2c6f0c654c860c6f176f366497
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81676388"
---
# <a name="create-and-manage-azure-migrate-projects"></a>Tworzenie projektów usługi Azure Migrate i zarządzanie nimi

W tym artykule opisano sposób tworzenia projektów migracji platformy Azure, zarządzania nimi i ich [usuwania.](migrate-services-overview.md)


## <a name="create-a-project-for-the-first-time"></a>Tworzenie projektu po raz pierwszy

Przy pierwszej konfiguracji usługi Azure Migrate należy utworzyć projekt i dodać narzędzie do oceny lub migracji. [Postępuj zgodnie z tymi instrukcjami,](how-to-add-tool-first-time.md) aby skonfigurować po raz pierwszy.

## <a name="create-additional-projects"></a>Tworzenie dodatkowych projektów

Jeśli masz już projekt migracji platformy Azure i chcesz utworzyć dodatkowy projekt, wykonaj następujące czynności:  

1. W [portalu publicznym platformy Azure](https://portal.azure.com) lub [usłudze Azure Government](https://portal.azure.us)wyszukaj usługę Azure **Migrate**.
2. Na pulpicie nawigacyjnym usługi Azure Migrate > **serwery**wybierz **pozycję Zmień** w prawym górnym rogu.

   ![Zmienianie projektu migracji platformy Azure](./media/create-manage-projects/switch-project.png)

3. Aby utworzyć nowy projekt, wybierz **pozycję kliknij tutaj**.

   ![Tworzenie drugiego projektu migracji platformy Azure](./media/create-manage-projects/create-new-project.png)


## <a name="find-a-project"></a>Znajdowanie projektu

Znajdź projekt w następujący sposób:

1. W [witrynie Azure portal](https://portal.azure.com)wyszukaj **usługę Azure Migrate**.
2. Na pulpicie nawigacyjnym usługi Azure Migrate > **Serwery**wybierz **pozycję Zmień** w prawym górnym rogu.

    ![Przełączanie do istniejącego projektu migracji platformy Azure](./media/create-manage-projects/switch-project.png)

3. Wybierz odpowiednią subskrypcję i projekt migracji platformy Azure.


Jeśli projekt został utworzony w [poprzedniej wersji](migrate-services-overview.md#azure-migrate-versions) usługi Azure Migrate, znajdź go w następujący sposób:

1. W [witrynie Azure portal](https://portal.azure.com)wyszukaj **usługę Azure Migrate**.
2. Na pulpicie nawigacyjnym usługi Azure Migrate, jeśli utworzono projekt w poprzedniej wersji, pojawi się baner odwołujący się do starszych projektów. Wybierz baner.

    ![Dostęp do istniejących projektów](./media/create-manage-projects/access-existing-projects.png)

3. Przejrzyj listę starych projektów.


## <a name="delete-a-project"></a>Usuwanie projektu

Skreślić w następujący sposób:

1. Otwórz grupę zasobów platformy Azure, w której utworzono projekt.
2. Na stronie grupy zasobów wybierz pozycję **Pokaż ukryte typy**.
3. Wybierz projekt migracji, który chcesz usunąć, i skojarzone z nim zasoby.
    - Typem zasobu jest **microsoft.migrate/migrateprojects**.
    - Jeśli grupa zasobów jest używana wyłącznie przez projekt migracji platformy Azure, można usunąć całą grupę zasobów.


Należy pamiętać, że:

- Po usunięciu, zarówno projektu i metadane o odnalezionych maszyn są usuwane.
- Jeśli używasz starszej wersji usługi Azure Migrate, otwórz grupę zasobów platformy Azure, w której utworzono projekt. Wybierz projekt migracji, który chcesz usunąć (typem zasobu jest **projekt migracji**).
- Jeśli używasz analizy zależności z obszarem roboczym usługi Azure Log Analytics:
    - Jeśli do narzędzia Ocena serwera dołączono obszar roboczy usługi Log Analytics, obszar roboczy nie zostanie automatycznie usunięty. Ten sam obszar roboczy usługi Log Analytics może służyć do wielu scenariuszy.
    - Jeśli chcesz usunąć obszar roboczy usługi Log Analytics, zrób to ręcznie.

### <a name="delete-a-workspace-manually"></a>Ręczne usuwanie obszaru roboczego

1. Przejdź do obszaru roboczego usługi Log Analytics dołączonego do projektu.

    - Jeśli projekt migracji platformy Azure nie został usunięty, łącze do obszaru roboczego można znaleźć w **programie Essentials** > **Server Assessment**.
       ![LA Workspace](./media/create-manage-projects/loganalytics-workspace.png).
       
    - Jeśli projekt migracji platformy Azure został już usunięty, wybierz pozycję **Grupy zasobów** w lewym okienku witryny Azure portal i znajdź obszar roboczy.
       
2. [Postępuj zgodnie z instrukcjami,](https://docs.microsoft.com/azure/azure-monitor/platform/delete-workspace) aby usunąć obszar roboczy.

## <a name="next-steps"></a>Następne kroki

Dodaj narzędzia [oceny](how-to-assess.md) lub [migracji](how-to-migrate.md) do projektów migracji platformy Azure.
