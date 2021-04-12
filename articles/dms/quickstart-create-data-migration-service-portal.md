---
title: 'Szybki Start: Tworzenie wystąpienia przy użyciu Azure Portal'
titleSuffix: Azure Database Migration Service
description: Użyj Azure Portal, aby utworzyć wystąpienie Azure Database Migration Service.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: quickstart
ms.date: 01/29/2021
ms.openlocfilehash: 8a500104a0273b9e131815c4dc832bd33729cd51
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105566595"
---
# <a name="quickstart-create-an-instance-of-the-azure-database-migration-service-by-using-the-azure-portal"></a>Szybki start: tworzenie wystąpienia usługi Azure Database Migration Service przy użyciu witryny Azure Portal

W tym przewodniku szybki start utworzysz wystąpienie Azure Database Migration Service za pomocą Azure Portal. Po utworzeniu wystąpienia można użyć go do migracji danych z wielu źródeł baz danych do platformy danych platformy Azure, takich jak z SQL Server do Azure SQL Database lub z SQL Server do wystąpienia zarządzanego Azure SQL.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Otwórz przeglądarkę internetową, przejdź do witryny [Microsoft Azure Portal](https://portal.azure.com/), a następnie wprowadź swoje poświadczenia, aby zalogować się w portalu. Widok domyślny to pulpit nawigacyjny usług.

> [!NOTE]
> Można utworzyć maksymalnie 10 wystąpień usługi DMS na subskrypcję na region. Jeśli potrzebujesz większej liczby wystąpień, Utwórz bilet pomocy technicznej.

## <a name="register-the-resource-provider"></a>Rejestrowanie dostawcy zasobów

Przed utworzeniem pierwszego wystąpienia usługi Database Migration Service zarejestruj dostawcę zasobów Microsoft.DataMigration.

1. W Azure Portal Wyszukaj i wybierz pozycję **subskrypcje**.

   ![Wyświetlanie subskrypcji w portalu](media/quickstart-create-data-migration-service-portal/portal-select-subscription.png)

2. Wybierz subskrypcję, w której chcesz utworzyć wystąpienie Azure Database Migration Service, a następnie wybierz pozycję **dostawcy zasobów**.

    ![Wyświetlanie dostawców zasobów](media/quickstart-create-data-migration-service-portal/portal-select-resource-provider.png)

3. Wyszukaj pozycję migracja, a następnie wybierz pozycję **zarejestruj** dla elementu **Microsoft. datamigration**.

    ![Rejestrowanie dostawcy zasobów](media/quickstart-create-data-migration-service-portal/dms-register-provider.png)

## <a name="create-an-instance-of-the-service"></a>Tworzenie wystąpienia usługi

1. W menu Azure Portal lub na stronie **głównej** wybierz pozycję **Utwórz zasób**. Wyszukaj i wybierz **Azure Database Migration Service**.

    ![Azure Marketplace](media/quickstart-create-data-migration-service-portal/portal-marketplace.png)

2. Na ekranie **Azure Database Migration Service** wybierz polecenie **Utwórz**.

    ![Tworzenie wystąpienia usługi Azure Database Migration Service](media/quickstart-create-data-migration-service-portal/dms-create.png)

3. Na ekranie **Tworzenie podstawy usługi migracji** :

     - Wybierz subskrypcję.
     - Utwórz nową grupę zasobów lub wybierz istniejącą.
     - Określ nazwę wystąpienia Azure Database Migration Service.
     - Wybierz lokalizację, w której chcesz utworzyć wystąpienie Azure Database Migration Service.
     - Wybierz **platformę Azure** jako tryb usługi.
     - Wybierz warstwę cenową. Aby uzyskać więcej informacji na temat kosztów i warstw cenowych, zobacz [stronę z cennikiem](https://aka.ms/dms-pricing).
     
    ![Skonfiguruj ustawienia podstawowe dla Azure Database Migration Service wystąpienia](media/quickstart-create-data-migration-service-portal/dms-create-basics.png)

     - Wybierz pozycję Dalej: Sieć.

4. Na ekranie **Tworzenie sieci usługi migracji** :

    - Wybierz istniejącą sieć wirtualną lub Utwórz nową. Sieć wirtualna zapewnia Azure Database Migration Service z dostępem do źródłowej bazy danych i środowiska docelowego. Aby uzyskać więcej informacji na temat sposobu tworzenia sieci wirtualnej w Azure Portal, zobacz artykuł [Tworzenie sieci wirtualnej przy użyciu Azure Portal](../virtual-network/quick-create-portal.md).

    ![Konfigurowanie ustawień sieciowych wystąpienia Azure Database Migration Service](media/quickstart-create-data-migration-service-portal/dms-network-settings.png)

    - Wybierz pozycję **Przegląd + Utwórz** , aby utworzyć usługę. 
    
    - Po kilku chwilach wystąpienie usługi Azure Database Migration Service jest tworzone i gotowe do użycia:

    ![Utworzono usługę migracji](media/quickstart-create-data-migration-service-portal/dms-service-created.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Możesz wyczyścić zasoby utworzone w tym przewodniku Szybki Start, usuwając [grupę zasobów platformy Azure](../azure-resource-manager/management/overview.md). Aby usunąć grupę zasobów, przejdź do utworzonego wystąpienia usługi Azure Database Migration Service. Wybierz nazwę w obszarze **Grupa zasobów**, a następnie wybierz pozycję **Usuń grupę zasobów**. Ta akcja spowoduje usunięcie wszystkich zasobów w grupie zasobów, a także usunięcie samej grupy.

## <a name="next-steps"></a>Następne kroki

* [Migracja z programu SQL Server do usługi Azure SQL Database w trybie offline](tutorial-sql-server-to-azure-sql.md)
* [Migracja z programu SQL Server do usługi Azure SQL Database w trybie online](./tutorial-sql-server-to-azure-sql.md)
* [Migrowanie SQL Server do wystąpienia zarządzanego usługi Azure SQL w trybie offline](tutorial-sql-server-to-managed-instance.md)
* [Migrowanie SQL Server do wystąpienia zarządzanego usługi Azure SQL w trybie online](tutorial-sql-server-managed-instance-online.md)