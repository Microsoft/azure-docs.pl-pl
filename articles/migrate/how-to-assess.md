---
title: Dodawanie narzędzi do oceny w Azure Migrate
description: Dowiedz się, jak dodać narzędzia do oceny w Azure Migrate.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 11/23/2020
ms.openlocfilehash: 37f3748b4f0f3db47bbd6fbe9bc06a307781c2f8
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2021
ms.locfileid: "104786808"
---
# <a name="add-assessment-tools"></a>Dodawanie narzędzi oceny

W tym artykule opisano sposób dodawania narzędzi do oceny w [Azure Migrate](./migrate-services-overview.md). 

- Jeśli chcesz dodać narzędzie do oceny i nie masz jeszcze projektu Azure Migrate, postępuj zgodnie z tym [artykułem](create-manage-projects.md).
- Po dodaniu narzędzia niezależnego dostawcy oprogramowania lub przeniesieniu do oceny [postępuj zgodnie z](prepare-isv-movere.md)instrukcjami, aby przygotować się do pracy z narzędziem.

## <a name="select-an-assessment-scenario"></a>Wybierz scenariusz oceny

1. W projekcie Azure Migrate kliknij pozycję **Przegląd**.
2. Wybierz scenariusz oceny:

    - Aby odnajdywać, oceniać i migrować serwery (fizyczne lub wirtualne) z centrum danych lub innych chmur na platformę Azure, wybierz pozycję **Odkryj, Oceń i Migruj**. Teraz można również odnajdywać i oceniać SQL Server ze środowiska VMware przy użyciu tego celu migracji.
    - Aby ocenić lokalne bazy danych SQL Server, wybierz pozycję **Oceń i Przeprowadź migrację baz danych**.
    - Aby ocenić lub zmigrować lokalne aplikacje sieci Web, wybierz pozycję **Eksploruj więcej**  >  **Web Apps**.
    - Aby ocenić infrastrukturę pulpitu wirtualnego, wybierz pozycję **Eksploruj więcej**  >  **infrastruktury pulpitów wirtualnych**.

    ![Opcje wybierania scenariusza oceny](./media/how-to-assess/assess-scenario.png)

## <a name="select-a-discovery-and-assessment-tool"></a>Wybierz narzędzie do odnajdywania i oceny 


1. Dodaj narzędzie:

    - Jeśli utworzono projekt Azure Migrate przy użyciu opcji **Oceń i Migruj serwery** w portalu, narzędzie do odnajdywania Azure Migrate i oceny jest automatycznie dodawane do projektu. Aby dodać dodatkowe narzędzia do oceny, w **systemach Windows, Linux i SQL Server**, obok opcji **Narzędzia do oceny** wybierz pozycję **Dodaj więcej narzędzi**.

         ![Przycisk dodawania dodatkowych narzędzi do oceny](./media/how-to-assess/add-assessment-tool.png)

    - Jeśli utworzono projekt przy użyciu innej opcji i nie masz jeszcze żadnych narzędzi do oceny w narzędziach do **oceny systemu Windows, Linux i SQL Server**  >  , wybierz **pozycję kliknij tutaj**.

        ![Przycisk umożliwiający dodanie pierwszego narzędzia do oceny](./media/how-to-assess/no-assessment-tool.png)

2. W **Azure Migrate**  >  **Dodaj narzędzia** wybierz narzędzia, które chcesz dodać. Następnie wybierz pozycję **Dodaj narzędzie**.

    ![Wybierz narzędzia do oceny z listy](./media/how-to-assess/select-assessment-tool.png)



## <a name="select-a-database-assessment-tool"></a>Wybierz narzędzie do oceny bazy danych

1. Dodaj narzędzie:

    - Jeśli utworzono projekt Azure Migrate przy użyciu opcji **Oceń i Przeprowadź migrację bazy danych** w portalu, narzędzie do oceny bazy danych jest automatycznie dodawane do projektu. Aby dodać dodatkowe narzędzia do oceny, w obszarze **bazy danych** obok opcji **Narzędzia do oceny** wybierz pozycję **Dodaj więcej narzędzi**.

    - Jeśli utworzono projekt przy użyciu innej opcji i nie masz jeszcze żadnych narzędzi do oceny bazy danych, w   >  **narzędziu do oceny** baz danych wybierz **pozycję kliknij tutaj**.

2. W **Azure Migrate**  >  **Dodaj narzędzia** wybierz narzędzia, które chcesz dodać. Następnie wybierz pozycję **Dodaj narzędzie**.

    ![Wybierz narzędzia do oceny bazy danych z listy](./media/how-to-assess/select-database-assessment-tool.png)


## <a name="select-a-web-app-assessment-tool"></a>Wybierz narzędzie do oceny aplikacji sieci Web

Jeśli utworzono projekt Azure Migrate przy użyciu opcji **Eksploruj więcej**  >  **aplikacji** internetowych w portalu, narzędzie do oceny aplikacji sieci Web zostanie automatycznie dodane do projektu. 


1. Jeśli narzędzie do oceny aplikacji sieci Web nie znajduje się w projekcie , w  >  **narzędziu do oceny** Web Apps wybierz **pozycję kliknij tutaj**.
    
    ![Dodawanie narzędzi do oceny aplikacji sieci Web](./media/how-to-assess/no-web-app-assessment-tool.png)


2. W **Azure Migrate**  >  **Dodaj narzędzia** wybierz narzędzie do oceny aplikacji sieci Web. Następnie wybierz pozycję **Dodaj narzędzie**.

    ![Wybierz narzędzie do migracji bazy danych z listy](./media/how-to-assess/select-web-app-assessment-tool.png)

 


## <a name="next-steps"></a>Następne kroki

Odnajdywanie serwerów lokalnych do oceny za pomocą narzędzia do odnajdywania i oceny Azure Migrate w przypadku oprogramowania [VMware](./tutorial-discover-vmware.md), [funkcji Hyper-V](./tutorial-discover-hyper-v.md)lub [serwerów fizycznych](./tutorial-discover-physical.md)