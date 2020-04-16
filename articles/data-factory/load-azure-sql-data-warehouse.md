---
title: Ładowanie danych do usługi Azure SQL Data Warehouse
description: Kopiowanie danych do magazynu SQL Azure data warehouse za pomocą usługi Azure Data Factory
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 06/22/2018
ms.openlocfilehash: 1943c16c678d0783b99a0f16c25fa72137defc8a
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415891"
---
# <a name="load-data-into-azure-sql-data-warehouse-by-using-azure-data-factory"></a>Load data into Azure SQL Data Warehouse by using Azure Data Factory (Ładowanie danych do usługi Azure SQL Data Warehouse przy użyciu usługi Azure Data Factory)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

[Usługa Azure SQL Data Warehouse](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) to oparta na chmurze, skalowawska baza danych, która może przetwarzać ogromne ilości danych, zarówno relacyjnych, jak i nierelacyjnych. Usługa SQL Data Warehouse jest oparta na architekturze przetwarzania równoległego (MPP), która jest zoptymalizowana pod kątem obciążeń magazynu danych przedsiębiorstwa. Oferuje elastyczność chmury z elastycznością skalowania pamięci masowej i obliczania niezależnie.

Wprowadzenie do usługi Azure SQL Data Warehouse jest teraz łatwiejsze niż kiedykolwiek podczas korzystania z usługi Azure Data Factory. Usługa Azure Data Factory to w pełni zarządzana usługa integracji danych oparta na chmurze. Za pomocą tej usługi można wypełnić magazyn danych SQL danymi z istniejącego systemu i zaoszczędzić czas podczas tworzenia rozwiązań analitycznych.

Usługa Azure Data Factory oferuje następujące korzyści związane z ładowaniem danych do usługi Azure SQL Data Warehouse:

* **Łatwy w konfiguracji:** intuicyjny 5-stopniowy kreator bez konieczności tworzenia skryptów.
* **Obsługa magazynu danych:** wbudowana obsługa bogatego zestawu lokalnych i chmurowych magazynów danych. Aby uzyskać szczegółową listę, zobacz tabelę [obsługiwanych magazynów danych](copy-activity-overview.md#supported-data-stores-and-formats).
* **Bezpieczne i zgodne**: Dane są przesyłane za pośrednictwem protokołu HTTPS lub usługi ExpressRoute. Globalna obecność usługi gwarantuje, że dane nigdy nie opuszczają granicy geograficznej.
* **Niezrównana wydajność przy użyciu PolyBase:** Polybase jest najbardziej efektywnym sposobem przenoszenia danych do usługi Azure SQL Data Warehouse. Funkcja obiektu blob przemieszczania, aby osiągnąć wysokie prędkości ładowania ze wszystkich typów magazynów danych, w tym magazynu obiektów Blob platformy Azure i magazynu usługi Data Lake. (Polybase domyślnie obsługuje magazyn obiektów Blob platformy Azure i magazyn usługi Azure Data Lake Store). Aby uzyskać szczegółowe informacje, zobacz [Kopiowanie wydajności działania](copy-activity-performance.md).

W tym artykule pokazano, jak korzystać z narzędzia Data kopiowania danych z fabryki danych w celu _załadowania danych z bazy danych SQL Azure do usługi Azure SQL Data Warehouse._ Możesz wykonać podobne kroki, aby skopiować dane z innych typów magazynów danych.

> [!NOTE]
> Aby uzyskać więcej informacji, zobacz [Kopiowanie danych do lub z usługi Azure SQL Data Warehouse przy użyciu usługi Azure Data Factory](connector-azure-sql-data-warehouse.md).

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure: jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.
* Usługa Azure SQL Data Warehouse: Magazyn danych przechowuje dane, które są kopiowane z bazy danych SQL. Jeśli nie masz usługi Azure SQL Data Warehouse, zobacz instrukcje w [tworzenie magazynu danych SQL](../sql-data-warehouse/sql-data-warehouse-get-started-tutorial.md).
* Usługa Azure SQL Database: Ten samouczek kopiuje dane z bazy danych SQL platformy Azure za pomocą przykładowych danych programu Adventure Works LT. Bazę danych SQL można utworzyć, postępując zgodnie z instrukcjami w [obszarze Tworzenie bazy danych SQL platformy Azure](../sql-database/sql-database-get-started-portal.md).
* Konto usługi Azure storage: usługa Azure Storage jest używana jako obiekt blob _przemieszczania_ w operacji kopiowania zbiorczego. Jeśli nie masz konta usługi Azure Storage, zobacz instrukcje podane w temacie [Tworzenie konta magazynu](../storage/common/storage-account-create.md).

## <a name="create-a-data-factory"></a>Tworzenie fabryki danych

1. W menu po lewej stronie wybierz pozycję **Utwórz źródło** > **Data + Analytics** > **Data Factory**:

   ![Wybór usługi Data Factory w okienku „Nowy”](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. Na stronie **Nowa fabryka danych** podaj wartości pól wyświetlanych na poniższym obrazie:

   ![Strona Nowa fabryka danych](./media/load-azure-sql-data-warehouse/new-azure-data-factory.png)

    * **Nazwa:** Wprowadź globalnie unikatową nazwę dla fabryki danych platformy Azure. Jeśli zostanie wyświetlony błąd "Nazwa \"fabryki danych LoadSQLDWDemo\" nie jest dostępna", wprowadź inną nazwę dla fabryki danych. Na przykład można użyć nazwy _**yourname**_**ADFTutorialDataFactory**. Spróbuj ponownie utworzyć fabrykę danych. Artykuł [Data Factory naming rules (Zasady nazewnictwa fabryki danych)](naming-rules.md) zawiera zasady nazewnictwa artefaktów usługi Data Factory.
    * **Subskrypcja**: Wybierz subskrypcję platformy Azure, w której chcesz utworzyć fabrykę danych. 
    * **Grupa zasobów:** Wybierz istniejącą grupę zasobów z listy rozwijanej lub wybierz opcję **Utwórz nową** i wprowadź nazwę grupy zasobów. Informacje na temat grup zasobów znajdują się w artykule [Using resource groups to manage your Azure resources](../azure-resource-manager/management/overview.md) (Używanie grup zasobów do zarządzania zasobami platformy Azure).  
    * **Wersja**: Wybierz **V2**.
    * **Lokalizacja**: Wybierz lokalizację fabryki danych. Na liście rozwijanej są wyświetlane tylko obsługiwane lokalizacje. Magazyny danych, które są używane przez fabrykę danych może znajdować się w innych lokalizacjach i regionach. Te magazyny danych obejmują usługi Azure Data Lake Store, Usługa Azure Storage, usługa Azure SQL Database i tak dalej.

3. Wybierz pozycję **Utwórz**.
4. Po zakończeniu tworzenia przejdź do fabryki danych. Zostanie wyświetlona strona główna **usługi Data Factory,** jak pokazano na poniższej ilustracji:

   ![Strona główna fabryki danych](./media/load-azure-sql-data-warehouse/data-factory-home-page.png)

   Wybierz kafelek **Tworzenie i monitorowanie**, aby w osobnej karcie uruchomić aplikację Integracja danych.

## <a name="load-data-into-azure-sql-data-warehouse"></a>Ładowanie danych do usługi Azure SQL Data Warehouse

1. Na stronie **Wprowadzenie** wybierz kafelek **Kopiuj dane,** aby uruchomić narzędzie Kopiuj dane:

   ![Kafelek narzędzia do kopiowania danych](./media/load-azure-sql-data-warehouse/copy-data-tool-tile.png)
1. Na stronie **Właściwości** określ **polecenie CopyFromSQLToSQLDW** dla pola **Nazwa zadania** i wybierz pozycję **Dalej:**

    ![Strona właściwości](./media/load-azure-sql-data-warehouse/copy-data-tool-properties-page.png)

1. Na stronie **Magazyn danych źródłowych** wykonaj następujące czynności:

    a. kliknij **+ Utwórz nowe połączenie:**

    ![Strona Źródłowy magazyn danych](./media/load-azure-sql-data-warehouse/new-source-linked-service.png)

    b. Wybierz z galerii **pozycję Azure SQL Database** i wybierz pozycję **Kontynuuj**. Możesz wpisać "SQL" w polu wyszukiwania, aby filtrować łączniki.

    ![Wybieranie bazy danych Azure SQL](./media/load-azure-sql-data-warehouse/select-azure-sql-db-source.png)

    d. Na stronie **Nowa usługa połączona** wybierz nazwę serwera i nazwę bazy danych z listy rozwijanej i określ nazwę użytkownika i hasło. Kliknij przycisk **Testuj połączenie**, aby zweryfikować ustawienia, a następnie wybierz pozycję **Zakończ**.

    ![Konfigurowanie bazy danych Azure SQL](./media/load-azure-sql-data-warehouse/configure-azure-sql-db.png)

    d. Wybierz nowo utworzoną połączoną usługę jako źródło, a następnie kliknij pozycję **Dalej**.

    ![Wybieranie połączonej usługi źródłowej](./media/load-azure-sql-data-warehouse/select-source-linked-service.png)

1. W **tabelach Wybierz, z których mają być kopiowane dane lub używana strona kwerendy niestandardowej,** wprowadź **saleslt,** aby filtrować tabele. Wybierz pole **(Zaznacz wszystko),** aby użyć wszystkich tabel do kopii, a następnie wybierz przycisk **Dalej:**

    ![Wybieranie tabel źródłowych](./media/load-azure-sql-data-warehouse/select-source-tables.png)

1. Na stronie **Magazyn danych miejsce docelowe** wykonaj następujące czynności:

    a. Kliknij pozycję **+Utwórz nowe połączenie**, aby dodać połączenie.

    ![Strona magazynu danych ujścia](./media/load-azure-sql-data-warehouse/new-sink-linked-service.png)

    b. Wybierz **pozycję Usługa Azure SQL Data Warehouse** z galerii i wybierz pozycję **Dalej**.

    ![Wybierz usługę Azure SQL DW](./media/load-azure-sql-data-warehouse/select-azure-sql-dw-sink.png)

    d. Na stronie **Nowa usługa połączona** wybierz nazwę serwera i nazwę bazy danych z listy rozwijanej i określ nazwę użytkownika i hasło. Kliknij przycisk **Testuj połączenie**, aby zweryfikować ustawienia, a następnie wybierz pozycję **Zakończ**.

    ![Konfigurowanie usługi Azure SQL DW](./media/load-azure-sql-data-warehouse/configure-azure-sql-dw.png)

    d. Wybierz nowo utworzoną połączoną usługę jako ujście, a następnie kliknij pozycję **Dalej**.

    ![Wybieranie połączonej usługi ujścia](./media/load-azure-sql-data-warehouse/select-sink-linked-service.png)

1. Na stronie **Mapowanie tabel** przejrzyj zawartość i wybierz pozycję **Dalej**. Wyświetla się inteligentne mapowanie tabeli. Tabele źródłowe są mapowane do tabel docelowych na podstawie nazw tabel. Jeśli tabela źródłowa nie istnieje w miejscu docelowym, usługa Azure Data Factory domyślnie tworzy tabelę docelową o tej samej nazwie. Można również mapować tabelę źródłową do istniejącej tabeli docelowej.

   > [!NOTE]
   > Automatyczne tworzenie tabel dla ujścia magazynu danych SQL ma zastosowanie, gdy źródłem jest program SQL Server lub Azure SQL Database. Jeśli kopiujesz dane z innego magazynu danych źródłowych, musisz wstępnie utworzyć schemat w ujściu usługi Azure SQL Data Warehouse przed wykonaniem kopii danych.

   ![Strona Mapowanie tabeli](./media/load-azure-sql-data-warehouse/table-mapping.png)

1. Na stronie **Mapowanie schematu** przejrzyj zawartość i wybierz pozycję **Dalej**. Inteligentne mapowanie tabeli jest oparte na nazwie kolumny. Jeśli usługa Data Factory zostanie automatycznie utworzona, konwersja typu danych może wystąpić w przypadku niezgodności między magazynami źródłowymi i docelowymi. Jeśli między kolumną źródłową a docelową jest nieobsługicona konwersja typu danych, obok odpowiedniej tabeli zostanie wyświetlony komunikat o błędzie.

    ![Strona Mapowanie schematu](./media/load-azure-sql-data-warehouse/schema-mapping.png)

1. Na stronie **Ustawienia** wykonaj następujące czynności:

    a. W sekcji **Ustawienia przemieszczania** kliknij przycisk **+ Nowy,** aby nowy magazyn przemieszczania. Magazyn jest używany do przemieszczania danych, zanim zostanie załadowany do magazynu danych SQL przy użyciu PolyBase. Po zakończeniu kopiowania tymczasowe dane w usłudze Azure Storage są automatycznie czyszczone.

    ![Konfigurowanie przemieszczania](./media/load-azure-sql-data-warehouse/configure-staging.png)

    b. Na stronie **Nowa usługa połączona** wybierz konto magazynu i wybierz pozycję **Zakończ**.

    ![Konfigurowanie usługi Azure Storage](./media/load-azure-sql-data-warehouse/configure-blob-storage.png)

    d. W sekcji **Ustawienia zaawansowane** usuń zaznaczenie opcji **Użyj domyślnego typu,** a następnie wybierz pozycję **Dalej**.

    ![Konfigurowanie bazy polybase](./media/load-azure-sql-data-warehouse/configure-polybase.png)

1. Na stronie **Podsumowanie** przejrzyj ustawienia i wybierz pozycję **Dalej:**

    ![Strona podsumowania](./media/load-azure-sql-data-warehouse/summary-page.png)
1. Na **stronie Wdrażanie**wybierz **pozycję Monitor,** aby monitorować potok (zadanie):

    ![Strona Wdrażanie](./media/load-azure-sql-data-warehouse/deployment-page.png)
1. Zwróć uwagę, że karta **Monitor** po lewej stronie jest automatycznie wybrana. Kolumna **Akcje** zawiera łącza do wyświetlania szczegółów uruchomienia działania i ponownego uruchamiania potoku:

    ![Monitorowanie uruchomień potoku](./media/load-azure-sql-data-warehouse/pipeline-monitoring.png)
1. Aby wyświetlić przebiegi aktywności skojarzone z uruchomieniem potoku, wybierz łącze **Wyświetl działanie uruchamiane** w kolumnie **Akcje.** Aby przełączyć się z powrotem do widoku przebiegów potoku, wybierz **łącze Potoki** u góry. Wybierz pozycję **Odśwież**, aby odświeżyć listę.

    ![Monitorowanie uruchomień działania](./media/load-azure-sql-data-warehouse/activity-monitoring.png)

1. Aby monitorować szczegóły wykonania dla każdego działania kopiowania, wybierz **łącze Szczegóły** w obszarze **Akcje** w widoku monitorowania działań. Można monitorować szczegóły, takie jak ilość danych skopiowanych ze źródła do ujścia, przepływność danych, kroki wykonywania o odpowiednim czasie trwania i używane konfiguracje:

    ![Monitorowanie szczegółów uruchomienia aktywności](./media/load-azure-sql-data-warehouse/monitor-activity-run-details.png)

## <a name="next-steps"></a>Następne kroki

Przejdź do następującego artykułu, aby dowiedzieć się więcej o pomocy technicznej usługi Azure SQL Data Warehouse:

> [!div class="nextstepaction"]
>[Łącznik usługi Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md)
