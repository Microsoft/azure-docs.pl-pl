---
title: Tworzenie projektów i zarządzanie nimi
description: Znajdowanie, tworzenie i usuwanie projektów oraz zarządzanie nimi w Azure Migrate.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: how-to
ms.date: 11/23/2020
ms.openlocfilehash: 0c9102f8ca724e431bb478945c5f4ba0369643d6
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107714846"
---
# <a name="create-and-manage-projects"></a>Tworzenie projektów i zarządzanie nimi

W tym artykule opisano sposób tworzenia i usuwania projektów oraz [zarządzania nimi.](migrate-services-overview.md) 

Wersja Azure Migrate zostanie wycofana w lutym 2024 r. Po lutym 2024 r. klasyczna wersja usługi Azure Migrate nie będzie już obsługiwana, a metadane spisu w projekcie klasycznym zostaną usunięte. Jeśli używasz projektów klasycznych, usuń te projekty i postępuj zgodnie z instrukcjami, aby utworzyć nowy projekt. Nie można uaktualnić klasycznych projektów lub składników do Azure Migrate. Wyświetl [często zadawane](./resources-faq.md#i-have-a-project-with-the-previous-classic-experience-of-azure-migrate-how-do-i-start-using-the-new-version) pytania przed rozpoczęciem procesu tworzenia.

Projekt służy do przechowywania metadanych odnajdywania, oceny i migracji zebranych ze środowiska, które oceniasz lub migruje. W projekcie można śledzić odnalezione zasoby, tworzyć oceny i orkiestrować migracje na platformę Azure.  

## <a name="verify-permissions"></a>Weryfikowanie uprawnień

Sprawdź, czy masz odpowiednie uprawnienia do tworzenia projektu:

1. W Azure Portal otwórz odpowiednią subskrypcję, a następnie wybierz **pozycję Kontrola dostępu (IAM).**
2. Na **stronie Sprawdź dostęp** znajdź odpowiednie konto i wybierz je, aby wyświetlić uprawnienia. Musisz mieć uprawnienia *Współautor* *lub* Właściciel. 


## <a name="create-a-project-for-the-first-time"></a>Tworzenie projektu po raz pierwszy

Skonfiguruj nowy projekt w subskrypcji platformy Azure.

1. W Azure Portal wyszukaj *Azure Migrate*.
2. W **chmurze** Usługi **wybierz pozycję Azure Migrate**.
3. W obszarze **Przegląd** wybierz pozycję **Ocena i migracja serwerów**.

    :::image type="content" source="./media/create-manage-projects/assess-migrate-servers.png" alt-text="Opcja oceny i migracji serwerów w przeglądzie":::

4. Na **stronie Serwery** wybierz pozycję Utwórz **projekt.**

    :::image type="content" source="./media/create-manage-projects/create-project.png" alt-text="Przycisk , aby rozpocząć tworzenie projektu":::

5. W **witrynie Create project**(Tworzenie projektu) wybierz subskrypcję platformy Azure i grupę zasobów. Utwórz grupę zasobów, jeśli jej nie masz.
6. W **szczegółach** projektu określ nazwę projektu i lokalizację geograficzną, w której chcesz utworzyć projekt.
    - Lokalizacja geograficzna jest używana tylko do przechowywania metadanych zebranych z serwerów lokalnych. Do migracji można wybrać dowolny region docelowy. 
    - Przejrzyj obsługiwane lokalizacje geograficzne chmur [publicznych](migrate-support-matrix.md#supported-geographies-public-cloud) [i rządowych.](migrate-support-matrix.md#supported-geographies-azure-government) 


    > [!Note]
    > Sekcja **Konfiguracja zaawansowana** umożliwia utworzenie projektu Azure Migrate z łącznością z prywatnym punktem końcowym. [Dowiedz się więcej](how-to-use-azure-migrate-with-private-endpoints.md#create-a-project-with-private-endpoint-connectivity) 

7. Wybierz przycisk **Utwórz**.

     :::image type="content" source="./media/create-manage-projects/project-details.png" alt-text="Strona ustawień projektu wejściowego":::


Poczekaj kilka minut na wdrożenie projektu.

## <a name="create-a-project-in-a-specific-region"></a>Tworzenie projektu w określonym regionie

W portalu możesz wybrać obszar geograficzny, w którym chcesz utworzyć projekt. Jeśli chcesz utworzyć projekt w określonym regionie platformy Azure, utwórz projekt za pomocą następującego polecenia interfejsu API.

```rest
PUT /subscriptions/<subid>/resourceGroups/<rg>/providers/Microsoft.Migrate/MigrateProjects/<mymigrateprojectname>?api-version=2018-09-01-preview "{location: 'centralus', properties: {}}"
```

## <a name="create-additional-projects"></a>Tworzenie dodatkowych projektów

Jeśli masz już projekt i chcesz utworzyć dodatkowy projekt, wykonaj następujące czynności:  

1. W [witrynie Azure Public Portal](https://portal.azure.com) [lub Azure Government](https://portal.azure.us)wyszukaj **Azure Migrate**.
2. Na Azure Migrate nawigacyjnym > **Serwery** wybierz pozycję **Zmień** w prawym górnym rogu.

    :::image type="content" source="./media/create-manage-projects/switch-project.png" alt-text="Zmienianie projektu":::

3. Aby utworzyć nowy projekt, wybierz **pozycję kliknij tutaj**.


## <a name="find-a-project"></a>Znajdowanie projektu

Znajdź projekt w następujący sposób:

1. W polu [Azure Portal](https://portal.azure.com)wyszukaj *Azure Migrate*.
2. Na Azure Migrate nawigacyjnym > **serwery** wybierz **pozycję Zmień** w prawym górnym rogu.

    :::image type="content" source="./media/create-manage-projects/switch-project.png" alt-text="Przełączanie do istniejącego projektu":::

3. Wybierz odpowiednią subskrypcję i projekt.


### <a name="find-a-classic-project"></a>Znajdowanie projektu klasycznego

Jeśli projekt został utworzony w [poprzedniej wersji programu](migrate-services-overview.md#azure-migrate-versions) Azure Migrate, znajdź go w następujący sposób:

1. W polu [Azure Portal](https://portal.azure.com)wyszukaj *Azure Migrate*.
2. Jeśli na Azure Migrate nawigacyjnym utworzono projekt w poprzedniej wersji, zostanie wyświetlony baner odwołujący się do starszych projektów. Wybierz transparent.

    :::image type="content" source="./media/create-manage-projects/access-existing-projects.png" alt-text="Uzyskiwanie dostępu do istniejących projektów":::

3. Przejrzyj listę starych projektów.


## <a name="delete-a-project"></a>Usuwanie projektu

Usuń w następujący sposób:

1. Otwórz grupę zasobów platformy Azure, w której został utworzony projekt.
2. Na stronie grupy zasobów wybierz pozycję **Pokaż ukryte typy**.
3. Wybierz projekt, który chcesz usunąć, i skojarzone z nim zasoby.
    - Typ zasobu to **Microsoft.Migrate/migrateprojects.**
    - Jeśli grupa zasobów jest używana wyłącznie przez projekt, możesz usunąć całą grupę zasobów.

Należy pamiętać, że:

- Usunięcie powoduje usunięcie zarówno projektu, jak i metadanych dotyczących odnalezionych serwerów.
- Jeśli używasz starszej wersji aplikacji Azure Migrate, otwórz grupę zasobów platformy Azure, w której został utworzony projekt. Wybierz projekt, który chcesz usunąć (typ zasobu to **Projekt migracji).**
- Jeśli używasz analizy zależności w obszarze roboczym usługi Azure Log Analytics:
    - Jeśli obszar roboczy usługi Log Analytics został dołączony do narzędzia Server Assessment, obszar roboczy nie jest automatycznie usuwany. Ten sam obszar roboczy usługi Log Analytics może być używany w wielu scenariuszach.
    - Jeśli chcesz usunąć obszar roboczy usługi Log Analytics, zrób to ręcznie.
- Usunięcie projektu jest nieodwracalne. Usuniętych obiektów nie można odzyskać.

### <a name="delete-a-workspace-manually"></a>Ręczne usuwanie obszaru roboczego

1. Przejdź do obszaru roboczego usługi Log Analytics dołączonego do projektu.

    - Jeśli projekt nie został usunięty, możesz znaleźć link do obszaru roboczego w tece **Essentials**  >  **Server Assessment**.
    :::image type="content" source="./media/create-manage-projects/loganalytics-workspace.png" alt-text="Obszar roboczy la":::
       
    - Jeśli projekt został już usunięty, wybierz pozycję **Grupy** zasobów w lewym okienku okna Azure Portal i znajdź obszar roboczy.
       
2. [Postępuj zgodnie z instrukcjami,](../azure-monitor/logs/delete-workspace.md) aby usunąć obszar roboczy.

## <a name="next-steps"></a>Następne kroki

Dodawanie [narzędzi do](how-to-assess.md) oceny lub [migracji](how-to-migrate.md) do projektów.