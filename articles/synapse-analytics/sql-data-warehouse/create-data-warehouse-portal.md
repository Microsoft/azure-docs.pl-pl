---
title: 'Szybki Start: Tworzenie i wykonywanie zapytań względem dedykowanej puli SQL (dawniej SQL DW) (Azure Portal)'
description: Tworzenie i wykonywanie zapytań do dedykowanej puli SQL (dawniej SQL DW) przy użyciu Azure Portal
services: synapse-analytics
author: pimorano
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql-dw
ms.date: 05/28/2019
ms.author: pimorano
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: dd9d66683d5f2349807915651c50bf9eedd285c1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104602234"
---
# <a name="quickstart-create-and-query-a-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics-using-the-azure-portal"></a>Szybki Start: Tworzenie i wykonywanie zapytań do dedykowanej puli SQL (dawniej SQL DW) w usłudze Azure Synapse Analytics przy użyciu Azure Portal

Szybkie tworzenie i wysyłanie zapytań do dedykowanej puli SQL (dawniej SQL DW) w usłudze Azure Synapse Analytics przy użyciu Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

1. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

   > [!NOTE]
   > Tworzenie dedykowanej puli SQL (dawniej SQL DW) w usłudze Azure Synapse może spowodować powstanie nowej usługi do obciążania. Aby uzyskać więcej informacji, zobacz [Cennik usługi Azure Synapse Analytics](https://azure.microsoft.com/pricing/details/synapse-analytics/).

2. Pobierz i zainstaluj najnowszą wersję programu [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) (SSMS). Uwaga: program SSMS jest dostępny tylko na platformach opartych na systemie Windows, zapoznaj się z [pełną listą obsługiwanych platform](/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-ver15&preserve-view=true#supported-operating-systems-ssms-185t).

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

## <a name="create-a-sql-pool"></a>Tworzenie puli SQL

Magazyny danych są tworzone przy użyciu dedykowanej puli SQL (dawniej SQL DW) w usłudze Azure Synapse Analytics. Dedykowana Pula SQL (wcześniej SQL DW) jest tworzona ze zdefiniowanym zestawem [zasobów obliczeniowych](memory-concurrency-limits.md). Baza danych jest tworzona w [grupie zasobów platformy Azure](../../azure-resource-manager/management/overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) i w [logicznym serwerze SQL](../../azure-sql/database/logical-servers.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

Wykonaj następujące kroki, aby utworzyć dedykowaną pulę SQL (dawniej SQL DW), która zawiera przykładowe dane **AdventureWorksDW** .

1. W lewym górnym rogu witryny Azure Portal wybierz pozycję **Utwórz zasób**.

   ![Tworzenie zasobu w Azure Portal](./media/create-data-warehouse-portal/create-a-resource.png)

2. Na pasku wyszukiwania typu "dedykowana Pula SQL" Wybierz dedykowaną pulę SQL (dawniej SQL DW). Wybierz pozycję **Utwórz** na stronie, która zostanie otwarta.

   ![tworzenie pustego magazynu danych](./media/create-data-warehouse-portal/create-a-data-warehouse.png)

3. W obszarze **podstawowe** Podaj swoją subskrypcję, grupę zasobów, dedykowaną pulę SQL (dawniej SQL DW) i nazwę serwera:

   | Ustawienie | Sugerowana wartość | Opis |
   | :------ | :-------------- | :---------- |
   | **Subskrypcja** | Twoja subskrypcja | Aby uzyskać szczegółowe informacje o subskrypcjach, zobacz [Subskrypcje](https://account.windowsazure.com/Subscriptions). |
   | **Grupa zasobów** | myResourceGroup | Prawidłowe nazwy grup zasobów opisano w artykule [Naming rules and restrictions](/azure/architecture/best-practices/resource-naming?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) (Reguły i ograniczenia nazewnictwa). |
   | **Nazwa puli SQL** | Dowolna globalnie unikatowa nazwa (przykład to *mySampleDataWarehouse*) | Prawidłowe nazwy baz danych znajdują się w temacie [identyfikatory baz danych](/sql/relational-databases/databases/database-identifiers?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).  |
   | **Server** (Serwer) | Dowolna nazwa unikatowa w skali globalnej | Wybierz pozycję istniejący serwer lub Utwórz nową nazwę serwera, a następnie wybierz pozycję **Utwórz nowy**. Prawidłowe nazwy serwera opisano w artykule [Naming rules and restrictions](/azure/architecture/best-practices/resource-naming?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) (Reguły i ograniczenia nazewnictwa). |

   ![Tworzenie podstawowych szczegółów magazynu danych](./media/create-data-warehouse-portal/create-sql-pool-basics.png)

4. W obszarze **poziom wydajności** wybierz pozycję **Wybierz poziom wydajności** , aby opcjonalnie zmienić konfigurację za pomocą suwaka.

   ![Zmień poziom wydajności magazynu danych](./media/create-data-warehouse-portal/create-sql-pool-performance-level.png)  

   Aby uzyskać więcej informacji na temat poziomów wydajności, zobacz [Zarządzanie obliczeniami w usłudze Azure Synapse Analytics](sql-data-warehouse-manage-compute-overview.md).

5. Wybierz pozycję **dodatkowe ustawienia**, w obszarze **Użyj istniejących danych** wybierz pozycję **przykład** , aby AdventureWorksDW zostanie utworzony jako Przykładowa baza danych.

    ![Wybierz pozycję Użyj istniejących danych](./media/create-data-warehouse-portal/create-sql-pool-additional-1.png)

6. Po zakończeniu karty podstawowe w formularzu analizy usługi Azure Synapse wybierz pozycję **Przegląd + Utwórz** , a następnie **Utwórz** , aby utworzyć pulę SQL. Aprowizacja zajmuje kilka minut.

   ![Wybieranie opcji Recenzja + tworzenie](./media/create-data-warehouse-portal/create-sql-pool-review-create.png)

   ![wybieranie pozycji Utwórz](./media/create-data-warehouse-portal/create-sql-pool-create.png)

7. Na pasku narzędzi wybierz pozycję **Powiadomienia**, aby monitorować proces wdrażania.

   ![Zrzut ekranu przedstawia powiadomienia z wdrożeniem w toku.](./media/create-data-warehouse-portal/notification.png)

## <a name="create-a-server-level-firewall-rule"></a>Tworzenie reguły zapory na poziomie serwera

Usługa Azure Synapse tworzy zaporę na poziomie serwera. Ta zapora uniemożliwia zewnętrznym aplikacjom i narzędziom łączenie się z serwerem lub dowolnymi bazami danych na serwerze. Aby umożliwić łączność, możesz dodać reguły zezwalające na połączenia dla konkretnych adresów IP. Wykonaj następujące kroki, aby utworzyć [regułę zapory na poziomie serwera](../../azure-sql/database/firewall-configure.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) dla Twojego adresu IP klienta.

> [!NOTE]
> Usługa Azure Synapse komunikuje się przez port 1433. Jeśli próbujesz nawiązać połączenie z sieci firmowej, ruch wychodzący na porcie 1433 może być blokowany przez zaporę sieciową. W takim przypadku nie można nawiązać połączenia z serwerem, chyba że dział IT otworzy port 1433.

1. Po zakończeniu wdrożenia wybierz pozycję **wszystkie usługi** w menu po lewej stronie. Wybierz pozycję **bazy danych**, wybierz gwiazdkę obok pozycji **Azure Synapse Analytics** , aby dodać usługę Azure Synapse Analytics do ulubionych.

2. Wybierz pozycję **Azure Synapse Analytics** z menu po lewej stronie, a następnie wybierz pozycję **MySampleDataWarehouse** ( **usługa Azure Synapse Analytics** ). Zostanie otwarta strona przeglądu bazy danych zawierająca w pełni kwalifikowaną nazwę serwera (na przykład **sqlpoolservername.Database.Windows.NET**) i opcje dalszej konfiguracji.

3. Skopiuj tę w pełni kwalifikowaną nazwę serwera do użycia w celu nawiązania połączenia z serwerem i jego bazami danych w tym i innym przewodniku Szybki Start. Aby otworzyć ustawienia serwera, wybierz nazwę serwera.

   ![znajdowanie nazwy serwera](./media/create-data-warehouse-portal/find-server-name.png)

4. Wybierz pozycję **Pokaż ustawienia zapory**.

   ![ustawienia serwera](./media/create-data-warehouse-portal/server-settings.png)

5. Zostanie otwarta strona **Ustawienia zapory** dla serwera.

   ![reguła zapory serwera](./media/create-data-warehouse-portal/server-firewall-rule.png)

6. Aby dodać bieżący adres IP do nowej reguły zapory, wybierz pozycję **Dodaj adres IP klienta** na pasku narzędzi. Reguła zapory może otworzyć port 1433 dla pojedynczego adresu IP lub zakresu adresów IP.

7. Wybierz pozycję **Zapisz**. Reguła zapory na poziomie serwera jest tworzona dla bieżącego adresu IP otwierającego port 1433 na serwerze.

8. Wybierz przycisk **OK** , a następnie zamknij stronę **Ustawienia zapory** .

Teraz można nawiązać połączenie z serwerem i jego pulami SQL przy użyciu tego adresu IP. Połączenie działa z programu SQL Server Management Studio lub dowolnego innego narzędzia. Przy łączeniu się używaj wcześniej utworzonego konta administratora serwera.

> [!IMPORTANT]
> Domyślnie dostęp za pośrednictwem zapory usługi SQL Database jest włączony dla wszystkich usług platformy Azure. Wybierz pozycję **wyłączone** na tej stronie, a następnie wybierz pozycję **Zapisz** , aby wyłączyć zaporę dla wszystkich usług platformy Azure.

## <a name="get-the-fully-qualified-server-name"></a>Uzyskiwanie w pełni kwalifikowanej nazwy serwera

Uzyskaj w pełni kwalifikowaną nazwę serwera dla serwera w Azure Portal. Nazwa ta będzie używana później przy nawiązywaniu połączenia z serwerem.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

2. Wybierz pozycję **Azure Synapse Analytics** z menu po lewej stronie, a następnie wybierz pozycję ze strony **usługi Azure Synapse Analytics** .

3. W okienku **Essentials** na stronie bazy danych w witrynie Azure Portal zlokalizuj i skopiuj **nazwę serwera**. W tym przykładzie w pełni kwalifikowana nazwa to sqlpoolservername.database.windows.net.

    ![informacje o połączeniu](./media/create-data-warehouse-portal/find-server-name.png)

## <a name="connect-to-the-server-as-server-admin"></a>Nawiąż połączenie z serwerem jako administrator serwera

Ta sekcja używa [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) (SSMS) do nawiązywania połączenia z serwerem.

1. Otwórz program SQL Server Management Studio.

2. W oknie dialogowym **Połącz z serwerem** wprowadź następujące informacje:

   | Ustawienie | Sugerowana wartość | Opis |
   | :------ | :-------------- | :---------- |
   | Typ serwera | Aparat bazy danych | Ta wartość jest wymagana |
   | Nazwa serwera | W pełni kwalifikowana nazwa serwera | Oto przykład: **sqlpoolservername.Database.Windows.NET**. |
   | Uwierzytelnianie | Uwierzytelnianie programu SQL Server | Uwierzytelnianie SQL to jedyny typ uwierzytelniania skonfigurowany w tym samouczku. |
   | Zaloguj się | Konto administratora serwera | Konto określone podczas tworzenia serwera. |
   | Hasło | Hasło konta administratora serwera | Hasło określone podczas tworzenia serwera. |
   ||||

   ![łączenie z serwerem](./media/create-data-warehouse-portal/connect-to-server-ssms.png)

3. Wybierz pozycję **Połącz**. W programie SSMS zostanie otwarte okno Eksplorator obiektów.

4. W Eksploratorze obiektów rozwiń pozycję **Bazy danych**. Następnie rozwiń pozycję **mySampleDatabase**, aby wyświetlić obiekty w nowej bazy danych.

   ![obiekty bazy danych](./media/create-data-warehouse-portal/connected-ssms.png)

## <a name="run-some-queries"></a>Uruchamianie zapytań

Nie zaleca się uruchamiania dużych zapytań podczas rejestrowania jako administrator serwera, ponieważ używa ona [ograniczonej klasy zasobów](resource-classes-for-workload-management.md). Zamiast tego należy skonfigurować [izolację obciążenia](./quickstart-configure-workload-isolation-tsql.md) , jak [pokazano w samouczkach](./load-data-wideworldimportersdw.md#create-a-user-for-loading-data).

Usługa Azure Synapse Analytics jako język zapytań używa języka T-SQL. Aby otworzyć okno zapytania i uruchomić kilka zapytań T-SQL, użyj następujących kroków:

1. Kliknij prawym przyciskiem myszy pozycję **mySampleDataWarehouse** i wybierz pozycję **nowe zapytanie**. Otworzy się okno nowego zapytania.

2. W oknie zapytania wprowadź następujące polecenie, aby wyświetlić listę baz danych.

    ```sql
    SELECT * FROM sys.databases
    ```

3. Wybierz pozycję **Wykonaj**. W wynikach zapytania są widoczne dwie bazy danych: **master** i **mySampleDataWarehouse**.

   ![Wykonywanie zapytań kierowanych do baz danych](./media/create-data-warehouse-portal/query-databases.png)

4. Aby zobaczyć trochę danych, wpisz następujące polecenie w celu wyświetlenia liczby klientów o nazwisku Adams, którzy mają trójkę dzieci. Wynikiem jest lista z sześcioma klientami.

    ```sql
    SELECT LastName, FirstName FROM dbo.dimCustomer
    WHERE LastName = 'Adams' AND NumberChildrenAtHome = 3;
    ```

   ![Zapytanie dotyczące tabeli dbo.dimCustomer](./media/create-data-warehouse-portal/query-customer.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Opłaty są naliczane za jednostki magazynu danych i dane przechowywane w dedykowanej puli SQL (dawniej SQL DW). Opłaty za te zasoby obliczeniowe i magazynowe są naliczane osobno.

- Jeśli chcesz przechowywać dane w magazynie, możesz wstrzymać obliczenia, gdy nie używasz dedykowanej puli SQL (dawniej SQL DW). Przez wstrzymywanie obliczeń opłata jest naliczana tylko za magazyn danych. Obliczenia można wznowić za każdym razem, gdy wszystko będzie gotowe do pracy z danymi.

- Jeśli chcesz usunąć przyszłe opłaty, możesz usunąć dedykowaną pulę SQL (dawniej SQL DW).

Wykonaj następujące kroki, aby wyczyścić zasoby, które nie są już potrzebne.

1. Zaloguj się do [Azure Portal](https://portal.azure.com), wybierz dedykowaną pulę SQL (dawniej SQL DW).

   ![Czyszczenie zasobów](./media/create-data-warehouse-portal/clean-up-resources.png)

2. Aby wstrzymać obliczenia, wybierz przycisk **Wstrzymaj** . Po wstrzymaniu dedykowanej puli SQL (dawniej SQL DW) zobaczysz przycisk **Wznów** . Aby wznowić obliczanie, wybierz pozycję **Wznów**.

3. Aby usunąć dedykowaną pulę SQL (dawniej SQL DW), aby nie naliczać opłat za zasoby obliczeniowe ani magazynowe, wybierz pozycję **Usuń**.

4. Aby usunąć utworzony serwer, wybierz pozycję **sqlpoolservername.Database.Windows.NET** na poprzednim obrazie, a następnie wybierz pozycję **Usuń**. Należy zachować ostrożność podczas usuwania, ponieważ usunięcie serwera spowoduje również usunięcie wszystkich baz danych przypisanych do tego serwera.

5. Aby usunąć grupę zasobów, wybierz pozycję Moja **zasobów**, a następnie wybierz pozycję **Usuń grupę zasobów**.

Chcesz zoptymalizować i zapisać wydatki na chmurę?

[!INCLUDE [cost-management-horizontal](../../../includes/cost-management-horizontal.md)]

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat ładowania danych do dedykowanej puli SQL (dawniej SQL DW), przejdź do artykułu [ładowanie danych do dedykowanej puli SQL](load-data-from-azure-blob-storage-using-copy.md) .