---
title: Dodaj narzędzia migracji w Azure Migrate
description: Dowiedz się, jak dodać narzędzia migracji w Azure Migrate.
ms.topic: article
ms.date: 04/26/2020
ms.openlocfilehash: b6ac8ed64d3f12783cc16f0428874a19d027adf9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86109811"
---
# <a name="add-migration-tools"></a>Dodawanie narzędzi migracji

W tym artykule opisano sposób dodawania narzędzi migracji w programie [Azure Migrate](./migrate-services-overview.md).

- Jeśli chcesz dodać narzędzie do migracji i nie został jeszcze skonfigurowany projekt Azure Migrate, postępuj zgodnie z tym [artykułem](how-to-add-tool-first-time.md).
- Jeśli dodano narzędzie niezależnego dostawcy oprogramowania do migracji, [wykonaj te kroki](prepare-isv-movere.md), aby przygotować się do pracy z narzędziem.

## <a name="select-a-migration-scenario"></a>Wybierz scenariusz migracji

1. W projekcie Azure Migrate kliknij pozycję **Przegląd**.
2. Wybierz scenariusz migracji, którego chcesz użyć:

    - Aby przeprowadzić migrację maszyn i obciążeń na platformę Azure, wybierz pozycję **Oceń i Przeprowadź migrację serwerów**.
    - Aby przeprowadzić migrację lokalnych baz danych SQL Server, wybierz pozycję **Oceń i Przeprowadź migrację baz danych**.
    - Aby przeprowadzić migrację lokalnych aplikacji sieci Web, wybierz pozycję **Oceń i Migruj aplikacje sieci Web**.
    - Aby migrować duże ilości danych lokalnych na platformę Azure w trybie offline, wybierz pozycję **zamów urządzenie Data Box**.

    ![Scenariusz oceny](./media/how-to-migrate/assess-scenario.png)

## <a name="select-a-server-migration-tool"></a>Wybierz narzędzie do migracji serwera

1. Kliknij pozycję **Oceń i Przeprowadź migrację serwerów**.
2. Jeśli jeszcze nie dodano narzędzi do migracji w obszarze **Azure Migrate-Server**, w obszarze **Narzędzia migracji**wybierz **pozycję kliknij tutaj, aby dodać narzędzie do migracji**. Jeśli dodano już narzędzia migracji, w obszarze **Dodaj więcej narzędzi migracji**wybierz pozycję **Zmień**.

    > [!NOTE]
    > Jeśli chcesz przejść do innego projektu, w obszarze **serwery Azure Migrate**, kliknij **przycisk kliknij tutaj**, aby **wyświetlić szczegóły dotyczące innego projektu migracji**.

3. W **Azure Migrate**wybierz narzędzie do migracji, którego chcesz użyć.
    - W przypadku korzystania z migracji serwera Azure Migrate można skonfigurować i uruchomić migracje bezpośrednio w projekcie Azure Migrate.
    - Jeśli używasz narzędzia do oceny innej firmy, przejdź do linku podanego dla niezależnego dostawcy oprogramowania i uruchom migrację zgodnie z instrukcjami, które zapewnia.

## <a name="select-a-database-migration-tool"></a>Wybierz narzędzie do migracji bazy danych

1. Kliknij pozycję **Oceń i Przeprowadź migrację baz danych**
2. W obszarze **bazy danych**kliknij pozycję **Dodaj narzędzia**.
3. W obszarze Dodaj narzędzie > **Wybierz narzędzie migracji**wybierz narzędzie, którego chcesz użyć do migrowania bazy danych.

## <a name="select-a-web-app-migration-tool"></a>Wybierz narzędzie do migracji aplikacji sieci Web

1. Kliknij pozycję **Oceń i Migruj aplikacje sieci Web**.
2. Użyj linku do narzędzia migracji dla Azure App Service. Użyj narzędzia migracji, aby:

    - **Oceń aplikacje online**: możesz ocenić i zmigrować aplikacje za pomocą publicznego adresu URL w trybie online przy użyciu Asystent migracji Azure App Service.
    - **.NET/php**: w przypadku wewnętrznych aplikacji platformy .NET i php można pobrać i uruchomić Asystent migracji.

## <a name="order-an-azure-data-box"></a>Zamów Azure Data Box

Aby migrować duże ilości danych na platformę Azure, możesz zamówić pole danych platformy Azure na potrzeby transferu danych w trybie offline.

1. Kliknij pozycję **zamów urządzenie Data Box**.
2. W obszarze **wybierz Azure Data Box**określ swoją subskrypcję. 
3. Transfer będzie zaimportowany do platformy Azure. Określ źródło danych oraz miejsce docelowe dla danych w regionie platformy Azure.

## <a name="next-steps"></a>Następne kroki

Wypróbowanie migracji przy użyciu Azure Migrate migracji serwera dla maszyn wirtualnych [funkcji Hyper-V](tutorial-migrate-hyper-v.md) lub programu [VMware](tutorial-migrate-vmware.md) .
