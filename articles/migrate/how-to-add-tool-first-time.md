---
title: Dodaj narzędzie do oceny/migracji w Azure Migrate
description: Opisuje sposób tworzenia projektu Azure Migrate i dodawania narzędzia do oceny/migracji.
ms.topic: how-to
ms.date: 04/16/2020
ms.openlocfilehash: a94e3cc18f46c457d6ed54ef88c62adefb07c5b9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86102535"
---
# <a name="add-an-assessmentmigration-tool-for-the-first-time"></a>Dodawanie narzędzia oceny/migracji po raz pierwszy

W tym artykule opisano sposób dodawania narzędzia do oceny lub migracji do projektu [Azure Migrate](./migrate-services-overview.md) po raz pierwszy.  
Azure Migrate udostępnia centralne centrum do śledzenia odnajdywania, oceny i migracji lokalnych aplikacji i obciążeń oraz maszyn wirtualnych chmur prywatnych/publicznych na platformie Azure. Centrum udostępnia Azure Migrate narzędzia do oceny i migracji, a także inne narzędzia i [oferty](migrate-services-overview.md#isv-integration) niezależnych dostawców oprogramowania (ISV). 

## <a name="check-permissions-to-create-project"></a>Sprawdź uprawnienia do tworzenia projektu

Jeśli projekt Azure Migrate nie został jeszcze utworzony, sprawdź, czy masz odpowiednie uprawnienia.

1. W witrynie Azure Portal otwórz daną subskrypcję i wybierz pozycję **Kontrola dostępu (IAM)** .
2. W obszarze Sprawdź dostęp Znajdź odpowiednie konto i kliknij je, aby wyświetlić uprawnienia. Należy mieć uprawnienia współautora lub właściciela.
    - Jeśli bezpłatne konto platformy Azure zostało właśnie utworzone, jesteś właścicielem subskrypcji.
    - Jeśli nie jesteś właścicielem subskrypcji, poproś właściciela o przypisanie roli.

## <a name="create-a-project-and-add-a-tool"></a>Tworzenie projektu i Dodawanie narzędzia

Skonfiguruj nowy projekt Azure Migrate w ramach subskrypcji platformy Azure i Dodaj narzędzie.

- Projekt Azure Migrate jest używany do przechowywania metadanych odnajdywania, oceny i migracji zebranych ze środowiska, które oceniasz lub migrujesz. 
- W projekcie można śledzić odnalezione zasoby oraz organizować oceny i przeprowadzić migrację.

1. W witrynie Azure Portal > **Wszystkie usługi** znajdź pozycję **Azure Migrate**.
2. W obszarze **Usługi** wybierz pozycję **Azure Migrate**.

    ![Skonfiguruj Azure Migrate](./media/how-to-add-tool-first-time/azure-migrate-search.png)

3. W obszarze **Przegląd** kliknij pozycję **Ocena i migracja serwerów**.
4. W obszarze **odnajdywanie, ocenianie i Migrowanie serwerów**kliknij pozycję **Oceń i Przeprowadź migrację serwerów**.

    ![Odnajdywanie i ocenianie serwerów](./media/how-to-add-tool-first-time/assess-migrate.png)

1. W obszarze **Odnajdywanie, ocena i migracja serwerów** kliknij pozycję **Dodaj narzędzia**.
2. W obszarze **Projekt migracji**wybierz subskrypcję platformy Azure i utwórz grupę zasobów, jeśli jej nie masz.
3. W obszarze **szczegóły projektu**Określ nazwę projektu i lokalizację geograficzną, w której chcesz utworzyć projekt.  Przejrzyj obsługiwane lokalizacje geograficzne dla chmur [publicznych](migrate-support-matrix.md#supported-geographies-public-cloud) i [instytucji rządowych](migrate-support-matrix.md#supported-geographies-azure-government).

    ![Tworzenie projektu usługi Azure Migrate](./media/how-to-add-tool-first-time/migrate-project.png)

    - Geografia projektu jest używana wyłącznie do przechowywania metadanych zebranych z lokalnych maszyn wirtualnych. Można wybrać dowolny region docelowy dla rzeczywistej migracji.
    - Jeśli musisz wdrożyć projekt w określonym regionie w lokalizacji geograficznej, użyj następującego interfejsu API, aby utworzyć projekt. Określ Identyfikator subskrypcji, nazwę grupy zasobów i nazwę projektu wraz z lokalizacją. Przejrzyj lokalizacje geograficzne/regiony dla chmur [publicznych](migrate-support-matrix.md#supported-geographies-public-cloud) i [rządowych](migrate-support-matrix.md#supported-geographies-azure-government).

        `PUT /subscriptions/<subid>/resourceGroups/<rg>/providers/Microsoft.Migrate/MigrateProjects/<mymigrateprojectname>?api-version=2018-09-01-preview "{location: 'centralus', properties: {}}"`   


4. Kliknij przycisk **dalej**i Dodaj narzędzie do oceny lub migracji.

    > [!NOTE]
    > Podczas tworzenia projektu należy dodać co najmniej jedno narzędzie do oceny lub migracji.

5. W **narzędziu Wybierz ocenę**Dodaj narzędzie do oceny. Jeśli nie potrzebujesz narzędzia do oceny, wybierz pozycję **Pomiń Dodawanie narzędzia do oceny teraz**  >  **Next**. 
2. W obszarze **Wybieranie narzędzia migracji**Dodaj narzędzie do migracji zgodnie z wymaganiami. Jeśli nie potrzebujesz teraz narzędzia migracji, wybierz pozycję **Pomiń Dodawanie narzędzia do migracji teraz**  >  **Next**.
3. W oknie **Recenzja + Dodawanie narzędzi**przejrzyj ustawienia, a następnie kliknij pozycję **Dodaj narzędzia**.

Po utworzeniu projektu można wybrać dodatkowe narzędzia do oceny i migracji serwerów oraz obciążeń, baz danych i aplikacji sieci Web.

## <a name="create-additional-projects"></a>Tworzenie dodatkowych projektów

W pewnych okolicznościach może być konieczne utworzenie dodatkowych projektów Azure Migrate. Na przykład jeśli masz centra danych w różnych lokalizacje geograficzneach lub musisz przechowywać metadane w innej lokalizacji geograficznej. Utwórz dodatkowy projekt w następujący sposób:

1. W bieżącym projekcie Azure Migrate kliknij pozycję **serwery** lub **bazy danych**.
2. W prawym górnym rogu kliknij pozycję **Zmień** obok bieżącej nazwy projektu.
3. W obszarze **Ustawienia**wybierz **pozycję kliknij tutaj, aby utworzyć nowy projekt**.
4. Utwórz nowy projekt i Dodaj nowe narzędzie zgodnie z opisem w poprzedniej procedurze.

## <a name="next-steps"></a>Następne kroki

- Wprowadzenie do [Azure Migrate: Ocena serwera](migrate-services-overview.md#azure-migrate-server-assessment-tool)lub [Azure Migrate: Migracja serwera](migrate-services-overview.md#azure-migrate-server-migration-tool).
- Po dodaniu narzędzia niezależnego dostawcy oprogramowania lub przeniesieniu, [Przejrzyj kroki](prepare-isv-movere.md) , aby przygotować się do łączenia narzędzia do Azure Migrate.
- Dowiedz się, jak dodać dodatkowe narzędzia do [oceny](how-to-assess.md) i [migracji](how-to-migrate.md) . 
