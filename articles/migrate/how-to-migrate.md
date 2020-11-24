---
title: Dodaj narzędzia migracji w Azure Migrate
description: Dowiedz się, jak dodać narzędzia migracji w Azure Migrate.
ms.topic: article
ms.date: 11/23/2020
ms.openlocfilehash: 5ff5fb54e077896fb6169ad53ce29483cd2c2f89
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95545338"
---
# <a name="add-migration-tools"></a>Dodawanie narzędzi migracji

W tym artykule opisano sposób dodawania narzędzi migracji w programie [Azure Migrate](./migrate-services-overview.md).

- Jeśli chcesz dodać narzędzie do migracji i nie został jeszcze skonfigurowany projekt Azure Migrate, postępuj zgodnie z tym [artykułem](create-manage-projects.md).
- Jeśli dodano narzędzie niezależnego dostawcy oprogramowania do migracji, [wykonaj te kroki](prepare-isv-movere.md), aby przygotować się do pracy z narzędziem.

## <a name="select-a-migration-scenario"></a>Wybierz scenariusz migracji

1. W projekcie Azure Migrate kliknij pozycję **Przegląd**.
2. Wybierz scenariusz migracji, którego chcesz użyć:

    - Aby przeprowadzić migrację maszyn i obciążeń na platformę Azure, wybierz pozycję **Oceń i Przeprowadź migrację serwerów**.
    - Aby przeprowadzić migrację lokalnych baz danych, wybierz pozycję **Oceń i Przeprowadź migrację baz danych**.
    - Aby przeprowadzić migrację lokalnych aplikacji sieci Web, wybierz opcję **Eksploruj więcej**  >  **Web Apps**.
    - Aby przeprowadzić migrację danych do platformy Azure przy użyciu pola danych, wybierz opcję **Eksploruj więcej**  >  **danych**.

    ![Opcje wybierania scenariusza migracji](./media/how-to-migrate/migrate-scenario.png)


## <a name="select-a-server-migration-tool"></a>Wybierz narzędzie do migracji serwera

1. Dodaj narzędzie:

    - Jeśli utworzono projekt Azure Migrate przy użyciu opcji **Oceń i Migruj serwery** w portalu, narzędzie do migracji Azure Migrate Server zostanie automatycznie dodane do projektu. Aby dodać dodatkowe narzędzia migracji, w obszarze **serwery** obok opcji **Narzędzia migracji** wybierz pozycję **Dodaj więcej narzędzi**.
    
         ![Przycisk dodawania dodatkowych narzędzi do migracji](./media/how-to-migrate/add-migration-tools.png)

    - Jeśli utworzono projekt przy użyciu innej opcji i nie masz jeszcze żadnych narzędzi do migracji, w obszarze **Servers**  >  **Narzędzia migracji** serwerów wybierz **pozycję kliknij tutaj**.

    ![Przycisk dodawania narzędzi do pierwszego migracji](./media/how-to-migrate/no-migration-tool.png)

2. W **Azure Migrate**  >  **Dodaj narzędzia** wybierz narzędzia, które chcesz dodać. Następnie wybierz pozycję **Dodaj narzędzie**.

    ![Wybierz narzędzia do oceny z listy](./media/how-to-migrate/select-migration-tool.png)


## <a name="select-a-database-migration-tool"></a>Wybierz narzędzie do migracji bazy danych

Jeśli utworzono projekt Azure Migrate przy użyciu opcji **Oceń i Przeprowadź migrację bazy danych** w portalu, narzędzie do migracji bazy danych jest automatycznie dodawane do projektu. 

1. Jeśli narzędzie do migracji bazy danych nie znajduje się w projekcie **Databases**, w  >  **narzędziu do oceny** baz danych wybierz **pozycję kliknij tutaj**.
    
    ![Dodaj narzędzia migracji bazy danych](./media/how-to-migrate/no-database-migration-tool.png)


2. W **Azure Migrate**  >  **Dodaj narzędzia**, wybierz narzędzie do migracji bazy danych. Następnie wybierz pozycję **Dodaj narzędzie**.

    ![Wybierz narzędzie do migracji bazy danych z listy](./media/how-to-migrate/select-database-migration-tool.png)

    

## <a name="select-a-web-app-migration-tool"></a>Wybierz narzędzie do migracji aplikacji sieci Web

Jeśli utworzono projekt Azure Migrate przy użyciu opcji **Eksploruj więcej**  >  **aplikacji** sieci Web w portalu, narzędzie do migracji aplikacji internetowych zostanie automatycznie dodane do projektu. 

1. Jeśli narzędzie do migracji aplikacji sieci Web nie znajduje się w projekcie, w narzędziu do oceny **aplikacji sieci Web**  >  **Assessment tools** wybierz **pozycję kliknij tutaj**.

    ![Dodaj narzędzia migracji aplikacji sieci Web](./media/how-to-migrate/no-web-app-migration-tool.png)
 

2. W **Azure Migrate**  >  **dodawania narzędzi** wybierz narzędzie do migracji aplikacji sieci Web. Następnie wybierz pozycję **Dodaj narzędzie**.

    ![Wybierz narzędzia do oceny webapp z listy](./media/how-to-migrate/select-web-app-migration-tool.png)


## <a name="order-an-azure-data-box"></a>Zamów Azure Data Box

Aby migrować duże ilości danych na platformę Azure, można zamówić Azure Data Box do transferu danych w trybie offline.

1. W obszarze **Przegląd** wybierz pozycję **Eksploruj więcej**.
2. W obszarze **Eksploruj więcej** wybierz pozycję **pole danych**.
3. W temacie Wprowadzenie **do urządzenie Data Box** wybierz subskrypcję i grupę zasobów, której chcesz użyć podczas porządkowania urządzenie Data Box.
4. **Typ transferu** to import do platformy Azure. Określ kraj, w którym znajdują się dane, oraz region świadczenia usługi Azure, do którego chcesz przenieść dane. 
5. Kliknij przycisk **Zastosuj** , aby zapisać ustawienia.

## <a name="next-steps"></a>Następne kroki

Wypróbuj migrację za pomocą narzędzia do migracji Azure Migrate Server dla maszyn wirtualnych [funkcji Hyper-V](tutorial-migrate-hyper-v.md) lub programu [VMware](tutorial-migrate-vmware.md) .
