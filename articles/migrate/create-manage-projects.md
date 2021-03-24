---
title: Tworzenie projektów i zarządzanie nimi
description: Znajdź, twórz, Zarządzaj i usuwaj projekty w Azure Migrate.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: how-to
ms.date: 11/23/2020
ms.openlocfilehash: cb0ac41d469ad9a7670ce4b1bae23b315a17dc38
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104871107"
---
# <a name="create-and-manage-projects"></a>Tworzenie projektów i zarządzanie nimi

W tym artykule opisano sposób tworzenia i usuwania [projektów](migrate-services-overview.md)oraz zarządzania nimi. 

Azure Migratee klasyczne jest wycofywane w lutym 2024. Po lutym 2024 wersja klasyczna Azure Migrate nie będzie już obsługiwana i metadane spisu w projekcie klasycznym zostaną usunięte. Jeśli używasz projektów klasycznych, usuń te projekty i postępuj zgodnie z instrukcjami, aby utworzyć nowy projekt. Nie można uaktualnić klasycznych projektów lub składników do Azure Migrate. Wyświetlanie [często zadawanych pytań](./resources-faq.md#i-have-a-project-with-the-previous-classic-experience-of-azure-migrate-how-do-i-start-using-the-new-version) przed rozpoczęciem procesu tworzenia.

Projekt jest używany do przechowywania metadanych odnajdowania, oceny i migracji zebranych ze środowiska, które oceniasz lub migrujesz. W projekcie można śledzić odnalezione zasoby, tworzyć oceny i organizować migracje na platformie Azure.  

## <a name="verify-permissions"></a>Weryfikowanie uprawnień

Sprawdź, czy masz odpowiednie uprawnienia do tworzenia projektu:

1. W Azure Portal Otwórz odpowiednią subskrypcję i wybierz pozycję **Kontrola dostępu (IAM)**.
2. W obszarze **Sprawdź dostęp** Znajdź odpowiednie konto i wybierz pozycję Wyświetl uprawnienia. Należy mieć uprawnienia *współautora* lub *właściciela* . 


## <a name="create-a-project-for-the-first-time"></a>Utwórz projekt po raz pierwszy

Skonfiguruj nowy projekt w ramach subskrypcji platformy Azure.

1. W Azure Portal Wyszukaj ciąg *Azure Migrate*.
2. W obszarze **usługi** wybierz pozycję **Azure Migrate**.
3. W obszarze **Przegląd** wybierz pozycję **Ocena i migracja serwerów**.

    :::image type="content" source="./media/create-manage-projects/assess-migrate-servers.png" alt-text="Opcja w przeglądzie do oceny i migrowania serwerów":::

4. W obszarze **serwery** wybierz pozycję **Utwórz projekt**.

    :::image type="content" source="./media/create-manage-projects/create-project.png" alt-text="Przycisk, aby rozpocząć tworzenie projektu":::

5. W obszarze **Utwórz projekt** wybierz subskrypcję platformy Azure i grupę zasobów. Utwórz grupę zasobów, jeśli jej nie masz.
6. W obszarze **szczegóły projektu** Określ nazwę projektu i geografię, w której chcesz utworzyć projekt.
    - Lokalizacja geograficzna służy tylko do przechowywania metadanych zebranych z serwerów lokalnych. Można wybrać dowolny region docelowy do migracji. 
    - Przejrzyj obsługiwane lokalizacje geograficzne dla chmur [publicznych](migrate-support-matrix.md#supported-geographies-public-cloud) i [instytucji rządowych](migrate-support-matrix.md#supported-geographies-azure-government).

8. Wybierz przycisk **Utwórz**.

     :::image type="content" source="./media/create-manage-projects/project-details.png" alt-text="Strona do ustawień projektu wejściowego":::


Zaczekaj kilka minut, aż projekt zostanie wdrożony.

## <a name="create-a-project-in-a-specific-region"></a>Tworzenie projektu w określonym regionie

W portalu możesz wybrać geografię, w której chcesz utworzyć projekt. Jeśli chcesz utworzyć projekt w ramach określonego regionu świadczenia usługi Azure, użyj następującego polecenia API, aby utworzyć projekt.

```rest
PUT /subscriptions/<subid>/resourceGroups/<rg>/providers/Microsoft.Migrate/MigrateProjects/<mymigrateprojectname>?api-version=2018-09-01-preview "{location: 'centralus', properties: {}}"
```

## <a name="create-additional-projects"></a>Tworzenie dodatkowych projektów

Jeśli masz już projekt i chcesz utworzyć dodatkowy projekt, wykonaj następujące czynności:  

1. W [portalu publicznym platformy Azure](https://portal.azure.com) lub [Azure Government](https://portal.azure.us)Wyszukaj pozycję **Azure Migrate**.
2. Na **Azure Migrate > pulpitu** nawigacyjnego wybierz pozycję **Zmień** w prawym górnym rogu.

    :::image type="content" source="./media/create-manage-projects/switch-project.png" alt-text="Zmień projekt":::

3. Aby utworzyć nowy projekt, wybierz **pozycję kliknij tutaj**.


## <a name="find-a-project"></a>Znajdź projekt

Znajdź projekt w następujący sposób:

1. W [Azure Portal](https://portal.azure.com)wyszukaj ciąg *Azure Migrate*.
2. Na **serwerach**> pulpitu nawigacyjnego Azure Migrate wybierz pozycję **Zmień** w prawym górnym rogu.

    :::image type="content" source="./media/create-manage-projects/switch-project.png" alt-text="Przełącz do istniejącego projektu":::

3. Wybierz odpowiednią subskrypcję i projekt.


### <a name="find-a-classic-project"></a>Znajdź projekt klasyczny

Jeśli projekt został utworzony w [poprzedniej wersji](migrate-services-overview.md#azure-migrate-versions) Azure Migrate, Znajdź go w następujący sposób:

1. W [Azure Portal](https://portal.azure.com)wyszukaj ciąg *Azure Migrate*.
2. Na pulpicie nawigacyjnym Azure Migrate, jeśli projekt został utworzony w poprzedniej wersji, pojawi się transparent odwołujący się do starszych projektów. Wybierz transparent.

    :::image type="content" source="./media/create-manage-projects/access-existing-projects.png" alt-text="Dostęp do istniejących projektów":::

3. Zapoznaj się z listą starych projektów.


## <a name="delete-a-project"></a>Usuwanie projektu

Usuń w następujący sposób:

1. Otwórz grupę zasobów platformy Azure, w której projekt został utworzony.
2. Na stronie Grupa zasobów wybierz pozycję **Pokaż ukryte typy**.
3. Wybierz projekt, który chcesz usunąć, i powiązane z nim zasoby.
    - Typ zasobu to **Microsoft. zmigrować/migrateprojects**.
    - Jeśli grupa zasobów jest używana wyłącznie przez projekt, można usunąć całą grupę zasobów.

Należy pamiętać, że:

- Po usunięciu programu zarówno projekt, jak i metadane dotyczące odnalezionych serwerów zostaną usunięte.
- Jeśli używasz starszej wersji programu Azure Migrate, otwórz grupę zasobów platformy Azure, w której został utworzony projekt. Wybierz projekt, który chcesz usunąć (typ zasobu to **projekt migracji**).
- Jeśli używasz analizy zależności z obszarem roboczym usługi Azure Log Analytics:
    - Jeśli dołączono obszar roboczy Log Analytics do narzędzia do oceny serwera, obszar roboczy nie zostanie automatycznie usunięty. Ten sam obszar roboczy Log Analytics może być używany w wielu scenariuszach.
    - Jeśli chcesz usunąć obszar roboczy Log Analytics, zrób to ręcznie.
- Usuwanie projektu jest nieodwracalne. Nie można odzyskać usuniętych obiektów.

### <a name="delete-a-workspace-manually"></a>Ręczne usuwanie obszaru roboczego

1. Przejdź do obszaru roboczego Log Analytics dołączonego do projektu.

    - Jeśli projekt nie został usunięty, można znaleźć link do obszaru roboczego w   >  **ocenie serwera** Essentials.
    :::image type="content" source="./media/create-manage-projects/loganalytics-workspace.png" alt-text="LA — obszar roboczy":::
       
    - Jeśli projekt został już usunięty, wybierz pozycję **grupy zasobów** w lewym okienku Azure Portal i Znajdź obszar roboczy.
       
2. [Postępuj zgodnie z instrukcjami,](../azure-monitor/logs/delete-workspace.md) aby usunąć obszar roboczy.

## <a name="next-steps"></a>Następne kroki

Dodaj narzędzia do [oceny](how-to-assess.md) lub [migracji](how-to-migrate.md) do projektów.