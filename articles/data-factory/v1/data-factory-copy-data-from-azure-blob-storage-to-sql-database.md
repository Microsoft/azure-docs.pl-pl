---
title: Kopiowanie danych z Blob Storage do SQL Database — Azure
description: W tym samouczku pokazano, jak za pomocą działania kopiowania w potoku Azure Data Factory skopiować dane z usługi BLOB Storage do usługi SQL Database.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: ''
editor: ''
ms.assetid: e4035060-93bf-4e8d-bf35-35e2d15c51e0
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 6c8c93c8721527d506847e394a02fc4eb5a98c47
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85248364"
---
# <a name="tutorial-copy-data-from-blob-storage-to-sql-database-using-data-factory"></a>Samouczek: kopiowanie danych z Blob Storage do SQL Database przy użyciu Data Factory
> [!div class="op_single_selector"]
> * [Przegląd i wymagania wstępne](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Kreator kopiowania](data-factory-copy-data-wizard-tutorial.md)
> * [Program Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
> * [Program PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
> * [Szablon usługi Azure Resource Manager](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
> * [Interfejs API REST](data-factory-copy-activity-tutorial-using-rest-api.md)
> * [Interfejs API .NET](data-factory-copy-activity-tutorial-using-dotnet-api.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli korzystasz z bieżącej wersji usługi Data Factory, zobacz [samouczek dotyczący działania kopiowania](../quickstart-create-data-factory-dot-net.md).

W tym samouczku utworzysz fabrykę danych z potokiem służącym do kopiowania danych z magazynu obiektów BLOB do SQL Database.

Działanie kopiowania wykonuje operację przenoszenia danych w usłudze Azure Data Factory. Jest obsługiwane przez globalnie dostępną usługę, która może kopiować dane między różnymi magazynami danych w sposób bezpieczny, niezawodny i skalowalny. Szczegółowe informacje dotyczące działania kopiowania znajdują się w artykule [Data Movement Activities](data-factory-data-movement-activities.md) (Działania przenoszenia danych).  

> [!NOTE]
> Aby uzyskać szczegółowe omówienie usługi Data Factory, zobacz artykuł [wprowadzenie do Azure Data Factory](data-factory-introduction.md) .
>
>

## <a name="prerequisites-for-the-tutorial"></a>Wymagania wstępne dotyczące samouczka
Przed rozpoczęciem pracy z tym samouczkiem należy spełnić następujące wymagania wstępne:

* **Subskrypcja platformy Azure**.  Jeśli nie masz subskrypcji, możesz utworzyć konto bezpłatnej wersji próbnej w zaledwie kilka minut. Szczegółowe informacje można znaleźć w artykule dotyczącym [bezpłatnej wersji próbnej](https://azure.microsoft.com/pricing/free-trial/) .
* **Konto usługi Azure Storage**. Magazyn obiektów BLOB jest używany jako magazyn danych **źródłowych** w tym samouczku. Jeśli nie masz konta usługi Azure Storage, zapoznaj się z artykułem [Tworzenie konta magazynu](../../storage/common/storage-account-create.md) , aby dowiedzieć się, jak go utworzyć.
* **Azure SQL Database**. W tym samouczku używasz Azure SQL Database jako **docelowego** magazynu danych. Jeśli nie masz bazy danych w Azure SQL Database, której można użyć w samouczku, zobacz [jak utworzyć i skonfigurować bazę danych w Azure SQL Database](../../sql-database/sql-database-get-started.md) , aby ją utworzyć.
* **SQL Server 2012/2014 lub Visual Studio 2013**. Za pomocą SQL Server Management Studio lub Visual Studio można utworzyć przykładową bazę danych i wyświetlić dane wynikowe w bazie danych.  

## <a name="collect-blob-storage-account-name-and-key"></a>Zbierz nazwę i klucz konta magazynu obiektów BLOB
Do wykonania tego samouczka potrzebna jest nazwa konta i klucz konta usługi Azure Storage. Zanotuj **nazwę konta** i **klucz konta** dla konta usługi Azure Storage.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com/).
2. W menu po lewej stronie kliknij pozycję **wszystkie usługi** , a następnie wybierz pozycję **konta magazynu**.

    ![Przeglądanie kont magazynu](media/data-factory-copy-data-from-azure-blob-storage-to-sql-database/browse-storage-accounts.png)
3. W bloku **konta magazynu** wybierz **konto usługi Azure Storage** , którego chcesz użyć w tym samouczku.
4. W obszarze **Ustawienia**wybierz pozycję **klucze dostępu** .
5. Kliknij przycisk **Kopiuj** (obraz) obok pola tekstowego **nazwa konta magazynu** i Zapisz/wklej go w dowolnym miejscu (na przykład w pliku tekstowym).
6. Powtórz poprzedni krok, aby skopiować lub zanotować **Klucz1**.

    ![Klucz dostępu do magazynu](media/data-factory-copy-data-from-azure-blob-storage-to-sql-database/storage-access-key.png)
7. Zamknij wszystkie bloki, klikając przycisk **X**.

## <a name="collect-sql-server-database-user-names"></a>Zbierz dane programu SQL Server, bazy danych, nazwy użytkowników
Do wykonania tego samouczka potrzebne są nazwy logicznego serwera SQL, bazy danych i użytkownika. Zanotuj nazwy **serwerów**, **baz danych**i **użytkownika** Azure SQL Database.

1. W **Azure Portal**kliknij pozycję **wszystkie usługi** po lewej stronie, a następnie wybierz pozycję **bazy danych SQL**.
2. W **bloku bazy danych SQL**wybierz **bazę danych** , która ma być używana w tym samouczku. Zanotuj **nazwę bazy danych**.  
3. W bloku **bazy danych SQL** kliknij pozycję **Właściwości** w obszarze **Ustawienia**.
4. Zanotuj wartości w polu **Nazwa serwera** i **Identyfikator logowania administratora serwera**.
5. Zamknij wszystkie bloki, klikając przycisk **X**.

## <a name="allow-azure-services-to-access-sql-server"></a>Zezwalaj usługom platformy Azure na dostęp do programu SQL Server
Upewnij się, że ustawienie **Zezwalaj na dostęp do usług platformy Azure** **jest włączone dla** serwera, aby usługa Data Factory mogła uzyskać dostęp do serwera. W celu sprawdzenia i włączenia tego ustawienia wykonaj następujące kroki:

1. Kliknij pozycję **wszystkie centrum usług** po lewej stronie, a następnie kliknij pozycję **serwery SQL**.
2. Wybierz swój serwer, a następnie kliknij przycisk **Zapora** w obszarze **USTAWIENIA**.
3. W bloku **Ustawienia zapory** kliknij pozycję **WŁĄCZ** dla ustawienia **Zezwalaj na dostęp do usług Azure**.
4. Zamknij wszystkie bloki, klikając przycisk **X**.

## <a name="prepare-blob-storage-and-sql-database"></a>Przygotuj Blob Storage i SQL Database
Teraz przygotuj magazyn obiektów blob platformy Azure i Azure SQL Database dla tego samouczka, wykonując następujące czynności:  

1. Uruchom program Notatnik. Skopiuj poniższy tekst i Zapisz go jako **emp.txt** do folderu **C:\ADFGetStarted** na dysku twardym.

    ```
    John, Doe
    Jane, Doe
    ```
2. Użyj narzędzi takich jak [Eksplorator magazynu Azure](https://storageexplorer.com/) do utworzenia kontenera **adftutorial** i przekazania pliku **emp.txt** do kontenera.

3. Poniższy skrypt SQL umożliwia utworzenie tabeli **emp** w usłudze Azure SQL Database.  

    ```SQL
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50),
    )
    GO

    CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID);
    ```

    **Jeśli na komputerze zainstalowano SQL Server 2012/2014:** postępuj zgodnie z instrukcjami [zarządzania Azure SQL Database przy użyciu SQL Server Management Studio](../../sql-database/sql-database-manage-azure-ssms.md) , aby nawiązać połączenie z serwerem i uruchomić skrypt SQL.

    Jeśli klient nie może uzyskać dostępu do logicznego serwera SQL, należy skonfigurować zaporę dla serwera, aby zezwalać na dostęp z komputera (adres IP). Zapoznaj się z [tym artykułem](../../sql-database/sql-database-configure-firewall-settings.md) , aby uzyskać instrukcje dotyczące konfigurowania zapory dla serwera programu.

## <a name="create-a-data-factory"></a>Tworzenie fabryki danych
Wymagania wstępne zostały spełnione. Fabrykę danych można utworzyć, korzystając z jednego z następujących sposobów. Kliknij jedną z opcji na liście rozwijanej u góry lub poniższe linki, aby wykonać samouczek.     

* [Kreator kopiowania](data-factory-copy-data-wizard-tutorial.md)
* [Program Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
* [Program PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
* [Szablon usługi Azure Resource Manager](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
* [Interfejs API REST](data-factory-copy-activity-tutorial-using-rest-api.md)
* [Interfejs API .NET](data-factory-copy-activity-tutorial-using-dotnet-api.md)

> [!NOTE]
> Potok danych przedstawiony w tym samouczku kopiuje dane ze źródłowego do docelowego magazynu danych. Nie przekształca on danych wejściowych w celu wygenerowania danych wyjściowych. Aby zapoznać się z samouczkiem dotyczącym przekształcania danych za pomocą usługi Azure Data Factory, zobacz [Tutorial: Build your first pipeline to transform data using Hadoop cluster](data-factory-build-your-first-pipeline.md) (Samouczek: Tworzenie pierwszego potoku przekształcającego dane przy użyciu klastra Hadoop).
>
> Dwa działania można połączyć w łańcuch (uruchomić jedno działanie po drugim), ustawiając wyjściowy zestaw danych jednego działania jako zestaw wejściowy drugiego. Szczegółowe informacje znajdują się w artykule [Scheduling and execution in Data Factory](data-factory-scheduling-and-execution.md) (Planowanie i wykonywanie w usłudze Data Factory).
