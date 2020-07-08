---
title: Tworzenie projektów usługi Azure Migrate i zarządzanie nimi
description: Znajdź, twórz, Zarządzaj i usuwaj projekty w Azure Migrate.
ms.topic: how-to
ms.date: 04/19/2020
ms.openlocfilehash: f5079ed979d98f2c6f0c654c860c6f176f366497
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "81676388"
---
# <a name="create-and-manage-azure-migrate-projects"></a>Tworzenie projektów usługi Azure Migrate i zarządzanie nimi

W tym artykule opisano sposób tworzenia i usuwania [Azure Migrate](migrate-services-overview.md) projektów oraz zarządzania nimi.


## <a name="create-a-project-for-the-first-time"></a>Utwórz projekt po raz pierwszy

Podczas pierwszego konfigurowania Azure Migrate należy utworzyć projekt i dodać narzędzie do oceny lub migracji. [Postępuj zgodnie z tymi instrukcjami](how-to-add-tool-first-time.md) , aby skonfigurować po raz pierwszy.

## <a name="create-additional-projects"></a>Tworzenie dodatkowych projektów

Jeśli masz już projekt Azure Migrate i chcesz utworzyć dodatkowy projekt, wykonaj następujące czynności:  

1. W [portalu publicznym platformy Azure](https://portal.azure.com) lub [Azure Government](https://portal.azure.us)Wyszukaj pozycję **Azure Migrate**.
2. Na **Azure Migrate > pulpitu**nawigacyjnego wybierz pozycję **Zmień** w prawym górnym rogu.

   ![Zmień projekt Azure Migrate](./media/create-manage-projects/switch-project.png)

3. Aby utworzyć nowy projekt, wybierz **pozycję kliknij tutaj**.

   ![Tworzenie drugiego projektu Azure Migrate](./media/create-manage-projects/create-new-project.png)


## <a name="find-a-project"></a>Znajdź projekt

Znajdź projekt w następujący sposób:

1. W [Azure Portal](https://portal.azure.com)wyszukaj ciąg **Azure Migrate**.
2. Na **serwerach**> pulpitu nawigacyjnego Azure Migrate wybierz pozycję **Zmień** w prawym górnym rogu.

    ![Przełącz do istniejącego projektu Azure Migrate](./media/create-manage-projects/switch-project.png)

3. Wybierz odpowiednią subskrypcję i projekt Azure Migrate.


Jeśli projekt został utworzony w [poprzedniej wersji](migrate-services-overview.md#azure-migrate-versions) Azure Migrate, Znajdź go w następujący sposób:

1. W [Azure Portal](https://portal.azure.com)wyszukaj ciąg **Azure Migrate**.
2. Na pulpicie nawigacyjnym Azure Migrate, jeśli projekt został utworzony w poprzedniej wersji, pojawi się transparent odwołujący się do starszych projektów. Wybierz transparent.

    ![Dostęp do istniejących projektów](./media/create-manage-projects/access-existing-projects.png)

3. Zapoznaj się z listą starych projektów.


## <a name="delete-a-project"></a>Usuwanie projektu

Usuń w następujący sposób:

1. Otwórz grupę zasobów platformy Azure, w której projekt został utworzony.
2. Na stronie Grupa zasobów wybierz pozycję **Pokaż ukryte typy**.
3. Wybierz projekt migracji, który chcesz usunąć, i powiązane z nim zasoby.
    - Typ zasobu to **Microsoft. zmigrować/migrateprojects**.
    - Jeśli grupa zasobów jest używana wyłącznie przez projekt Azure Migrate, można usunąć całą grupę zasobów.


Należy pamiętać, że:

- Po usunięciu programu zarówno projekt, jak i metadane dotyczące odnalezionych maszyn są usuwane.
- Jeśli używasz starszej wersji programu Azure Migrate, otwórz grupę zasobów platformy Azure, w której został utworzony projekt. Wybierz projekt migracji, który chcesz usunąć (typ zasobu to **projekt migracji**).
- Jeśli używasz analizy zależności z obszarem roboczym usługi Azure Log Analytics:
    - Jeśli dołączono obszar roboczy Log Analytics do narzędzia do oceny serwera, obszar roboczy nie zostanie automatycznie usunięty. Ten sam obszar roboczy Log Analytics może być używany w wielu scenariuszach.
    - Jeśli chcesz usunąć obszar roboczy Log Analytics, zrób to ręcznie.

### <a name="delete-a-workspace-manually"></a>Ręczne usuwanie obszaru roboczego

1. Przejdź do obszaru roboczego Log Analytics dołączonego do projektu.

    - Jeśli projekt Azure Migrate nie został usunięty, można znaleźć link do obszaru roboczego w **Essentials**  >  **ocenie serwera**Essentials.
       ![LA — obszar roboczy ](./media/create-manage-projects/loganalytics-workspace.png) .
       
    - Jeśli projekt Azure Migrate został już usunięty, wybierz pozycję **grupy zasobów** w lewym okienku Azure Portal i Znajdź obszar roboczy.
       
2. [Postępuj zgodnie z instrukcjami,](https://docs.microsoft.com/azure/azure-monitor/platform/delete-workspace) aby usunąć obszar roboczy.

## <a name="next-steps"></a>Następne kroki

Dodaj narzędzia do [oceny](how-to-assess.md) lub [migracji](how-to-migrate.md) do Azure Migrate projektów.
