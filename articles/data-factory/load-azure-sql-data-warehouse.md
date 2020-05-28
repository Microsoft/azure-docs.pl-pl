---
title: Ładowanie danych do usługi Azure SQL Data Warehouse
description: Używanie Azure Data Factory do kopiowania danych do Azure SQL Data Warehouse
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/16/2020
ms.openlocfilehash: e0a9a00aa6abd35ad723f02a30869e8f7734b1f3
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2020
ms.locfileid: "84020561"
---
# <a name="load-data-into-azure-sql-data-warehouse-by-using-azure-data-factory"></a>Load data into Azure SQL Data Warehouse by using Azure Data Factory (Ładowanie danych do usługi Azure SQL Data Warehouse przy użyciu usługi Azure Data Factory)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

[Azure SQL Data Warehouse](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) to oparta na chmurze baza danych skalowalna w poziomie, która umożliwia przetwarzanie dużych ilości danych, zarówno relacyjnych, jak i nierelacyjnych. SQL Data Warehouse jest oparty na architekturze wysoce Parallel Processing (MPP) zoptymalizowanej pod kątem obciążeń magazynu danych w przedsiębiorstwie. Oferuje elastyczność chmury i umożliwia niezależne skalowanie magazynu i obliczanie.

Wprowadzenie do Azure SQL Data Warehouse jest teraz łatwiejsze niż kiedykolwiek wcześniej podczas korzystania z Azure Data Factory. Azure Data Factory to w pełni zarządzana usługa integracji danych oparta na chmurze. Za pomocą usługi można wypełnić SQL Data Warehouse danymi z istniejącego systemu i zaoszczędzić czas podczas kompilowania rozwiązań analitycznych.

Azure Data Factory oferuje następujące korzyści związane z ładowaniem danych do Azure SQL Data Warehouse:

* **Łatwa konfiguracja**: Intuicyjny kreator 5-etapowy bez konieczności wykonywania skryptów.
* **Obsługa rozbudowanych magazynów danych**: Wbudowana obsługa bogatego zestawu lokalnych i opartych na chmurze magazynów danych. Aby uzyskać szczegółową listę, zobacz tabelę [obsługiwanych magazynów danych](copy-activity-overview.md#supported-data-stores-and-formats).
* **Bezpieczeństwo i zgodność**: dane są przesyłane za pośrednictwem protokołu HTTPS lub ExpressRoute. Globalna obecność usługi gwarantuje, że dane nigdy nie opuszczą granicy geograficznej.
* **Niezrównana wydajność przy użyciu bazy danych bazowych**: baza jest najbardziej wydajnym sposobem, aby przenieść dane do Azure SQL Data Warehouse. Funkcja tymczasowych obiektów BLOB umożliwia uzyskanie dużej szybkości ładowania ze wszystkich typów magazynów danych, w tym usługi Azure Blob Storage i Data Lake Store. (Baza danych jest domyślnie obsługiwana w usłudze Azure Blob Storage i Azure Data Lake Store). Aby uzyskać szczegółowe informacje, zobacz [wydajność działania kopiowania](copy-activity-performance.md).

W tym artykule pokazano, jak za pomocą narzędzia Kopiowanie danych Data Factory _załadować dane z Azure SQL Database do Azure SQL Data Warehouse_. Możesz wykonać podobne kroki, aby skopiować dane z innych typów magazynów danych.

> [!NOTE]
> Aby uzyskać więcej informacji, zobacz [Kopiowanie danych do lub z Azure SQL Data Warehouse przy użyciu Azure Data Factory](connector-azure-sql-data-warehouse.md).

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure: Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/) .
* Azure SQL Data Warehouse: Magazyn danych przechowuje dane skopiowane z bazy danych SQL. Jeśli nie masz Azure SQL Data Warehouse, zapoznaj się z instrukcjami w artykule [tworzenie SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-get-started-tutorial.md).
* Azure SQL Database: w tym samouczku kopiuje dane z bazy danych Azure SQL Database za pomocą przykładowych danych firmy Adventure Works LT. Bazę danych SQL można utworzyć, postępując zgodnie z instrukcjami zawartymi w temacie [Tworzenie bazy danych Azure SQL Database](../azure-sql/database/single-database-create-quickstart.md).
* Konto usługi Azure Storage: usługa Azure Storage jest używana jako _tymczasowy_ obiekt BLOB w operacji kopiowania zbiorczego. Jeśli nie masz konta usługi Azure Storage, zobacz instrukcje podane w temacie [Tworzenie konta magazynu](../storage/common/storage-account-create.md).

## <a name="create-a-data-factory"></a>Tworzenie fabryki danych

1. W menu po lewej stronie wybierz pozycję **Utwórz zasób**  >  **dane + analiza**  >  **Data Factory**:

2. Na stronie **Nowa fabryka danych** podaj wartości następujących elementów:

    * **Nazwa**: wprowadź *LoadSQLDWDemo* dla nazwy. Nazwa fabryki danych musi być * globalnie unikatowa. Jeśli zostanie wyświetlony komunikat o błędzie "Nazwa fabryki danych" LoadSQLDWDemo "jest niedostępna, wprowadź inną nazwę fabryki danych. Można na przykład _**użyć nazwy namename**_**ADFTutorialDataFactory**. Spróbuj ponownie utworzyć fabrykę danych. Artykuł [Data Factory naming rules (Zasady nazewnictwa fabryki danych)](naming-rules.md) zawiera zasady nazewnictwa artefaktów usługi Data Factory.
    * **Subskrypcja**: wybierz subskrypcję platformy Azure, w której chcesz utworzyć fabrykę danych. 
    * **Grupa zasobów**: wybierz istniejącą grupę zasobów z listy rozwijanej lub wybierz opcję **Utwórz nową** , a następnie wprowadź nazwę grupy zasobów. Informacje na temat grup zasobów znajdują się w artykule [Using resource groups to manage your Azure resources](../azure-resource-manager/management/overview.md) (Używanie grup zasobów do zarządzania zasobami platformy Azure).  
    * **Wersja**: wybierz pozycję **v2**.
    * **Lokalizacja**: Wybierz lokalizację fabryki danych. Na liście rozwijanej są wyświetlane tylko obsługiwane lokalizacje. Magazyny danych używane przez fabrykę danych mogą znajdować się w innych lokalizacjach i regionach. Te magazyny danych obejmują Azure Data Lake Store, Azure Storage, Azure SQL Database i tak dalej.

3. Wybierz przycisk **Utwórz**.
4. Po zakończeniu tworzenia przejdź do fabryki danych. Zostanie wyświetlona strona główna **Data Factory** , jak pokazano na poniższej ilustracji:

   ![Strona główna fabryki danych](./media/doc-common-process/data-factory-home-page.png)

   Wybierz kafelek **Tworzenie i monitorowanie**, aby w osobnej karcie uruchomić aplikację Integracja danych.

## <a name="load-data-into-azure-sql-data-warehouse"></a>Ładowanie danych do usługi Azure SQL Data Warehouse

1. Na stronie **Wprowadzenie** wybierz kafelek **Kopiowanie danych**, aby uruchomić narzędzie do kopiowania danych.

1. Na stronie **Właściwości** Określ **CopyFromSQLToSQLDW** dla pola **Nazwa zadania** , a następnie wybierz przycisk **dalej**.

    ![Strona właściwości](./media/load-azure-sql-data-warehouse/copy-data-tool-properties-page.png)

1. Na stronie **Magazyn danych źródłowych** wykonaj następujące czynności:
    >[!TIP]
    >W tym samouczku jako typ uwierzytelniania dla źródłowego magazynu danych używasz *uwierzytelniania SQL* , ale możesz wybrać inne obsługiwane metody uwierzytelniania:*nazwę główną usługi* i *tożsamość zarządzaną* w razie potrzeby. Aby uzyskać szczegółowe informacje, zapoznaj się z odpowiednimi sekcjami w [tym artykule](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-database#linked-service-properties) .
    >Aby bezpiecznie przechowywać wpisy tajne dla magazynów danych, zaleca się również korzystanie z Azure Key Vault. Szczegółowe ilustracje znajdują się w [tym artykule](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault) .

    a. Kliknij pozycję **+ Utwórz nowe połączenie**.

    b. Wybierz **Azure SQL Database** z galerii, a następnie wybierz pozycję **Kontynuuj**. Możesz wpisać ciąg "SQL" w polu wyszukiwania, aby filtrować łączniki.

    ![Wybieranie bazy danych Azure SQL](./media/load-azure-sql-data-warehouse/select-azure-sql-db-source.png)

    c. Na stronie **Nowa połączona usługa** wybierz nazwę serwera i nazwę bazy danych z listy rozwijanej, a następnie określ nazwę użytkownika i hasło. Kliknij przycisk **Test connection** , aby sprawdzić poprawność ustawień, a następnie wybierz pozycję **Utwórz**.

    ![Konfigurowanie bazy danych Azure SQL](./media/load-azure-sql-data-warehouse/configure-azure-sql-db.png)

    d. Wybierz nowo utworzoną połączoną usługę jako źródło, a następnie kliknij pozycję **Dalej**.

1. W **wybranych tabelach, z których mają zostać skopiowane dane lub Użyj niestandardowej strony zapytania** , wprowadź **tabeli SalesLT** , aby odfiltrować tabele. Wybierz pole **(Zaznacz wszystko)** , aby użyć wszystkich tabel do skopiowania, a następnie wybierz przycisk **dalej**.

    ![Wybieranie tabel źródłowych](./media/load-azure-sql-data-warehouse/select-source-tables.png)

1. Na stronie **Zastosuj filtr** Określ ustawienia lub wybierz przycisk **dalej**.

1. Na stronie **docelowy magazyn danych** wykonaj następujące czynności:
    >[!TIP]
    >W tym samouczku użyjesz *uwierzytelniania SQL* jako typu uwierzytelniania dla docelowego magazynu danych, ale możesz wybrać inne obsługiwane metody uwierzytelniania:*nazwę główną usługi* i *tożsamość zarządzaną* w razie potrzeby. Aby uzyskać szczegółowe informacje, zapoznaj się z odpowiednimi sekcjami w [tym artykule](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse#linked-service-properties) .
    >Aby bezpiecznie przechowywać wpisy tajne dla magazynów danych, zaleca się również korzystanie z Azure Key Vault. Szczegółowe ilustracje znajdują się w [tym artykule](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault) .

    a. Kliknij pozycję **+Utwórz nowe połączenie**, aby dodać połączenie.

    b. Wybierz pozycję **Azure Synapse Analytics (wcześniej SQL DW)** z galerii, a następnie wybierz pozycję **Kontynuuj**. Możesz wpisać ciąg "SQL" w polu wyszukiwania, aby filtrować łączniki.

    ![Wybieranie usługi Azure SQL DW](./media/load-azure-sql-data-warehouse/select-azure-sql-dw-sink.png)

    c. Na stronie **Nowa połączona usługa** wybierz nazwę serwera i nazwę bazy danych z listy rozwijanej, a następnie określ nazwę użytkownika i hasło. Kliknij przycisk **Test connection** , aby sprawdzić poprawność ustawień, a następnie wybierz pozycję **Utwórz**.

    ![Konfigurowanie usługi Azure SQL DW](./media/load-azure-sql-data-warehouse/configure-azure-sql-dw.png)

    d. Wybierz nowo utworzoną połączoną usługę jako ujście, a następnie kliknij pozycję **Dalej**.

1. Na stronie **Mapowanie tabeli** przejrzyj zawartość, a następnie wybierz przycisk **dalej**. Zostanie wyświetlone inteligentne Mapowanie tabeli. Tabele źródłowe są mapowane do tabel docelowych na podstawie nazw tabel. Jeśli tabela źródłowa nie istnieje w miejscu docelowym, Azure Data Factory domyślnie utworzy tabelę docelową o tej samej nazwie. Możesz również zmapować tabelę źródłową do istniejącej tabeli docelowej.

   > [!NOTE]
   > Automatyczne tworzenie tabeli dla ujścia SQL Data Warehouse ma zastosowanie, gdy SQL Server lub Azure SQL Database jest źródłem. W przypadku kopiowania danych z innego źródłowego magazynu danych przed wykonaniem kopii danych należy wstępnie utworzyć schemat w Azure SQL Data Warehouse ujścia.

   ![Strona Mapowanie tabeli](./media/load-azure-sql-data-warehouse/table-mapping.png)

1. Na stronie **Mapowanie kolumn** przejrzyj zawartość, a następnie wybierz przycisk **dalej**. Mapowanie inteligentnej tabeli opiera się na nazwie kolumny. Jeśli zezwolisz Data Factory na automatyczne tworzenie tabel, konwersja typu danych może wystąpić w przypadku braku zgodności między magazynem źródłowym a docelowym. Jeśli istnieje nieobsługiwana konwersja typu danych między kolumną źródłową i docelową, zobaczysz komunikat o błędzie obok odpowiedniej tabeli.

    ![Strona mapowania kolumn](./media/load-azure-sql-data-warehouse/schema-mapping.png)

1. Na stronie **Ustawienia** wykonaj następujące czynności:

    a. W sekcji **Ustawienia przemieszczania** kliknij pozycję **+ Nowy** , aby nowe miejsce w magazynie tymczasowym. Magazyn jest używany do przemieszczania danych przed załadowaniem ich do SQL Data Warehouse przy użyciu bazy. Po zakończeniu kopiowania zostaną automatycznie oczyszczone dane tymczasowe Blob Storage platformy Azure.

    b. Na stronie **Nowa połączona usługa** wybierz konto magazynu, a następnie wybierz pozycję **Utwórz** , aby wdrożyć połączoną usługę.

    c. W sekcji **Ustawienia zaawansowane** Usuń zaznaczenie opcji **Użyj domyślnego typu** , a następnie wybierz przycisk **dalej**.

    ![Skonfiguruj bazę](./media/load-azure-sql-data-warehouse/configure-polybase.png)

1. Na stronie **Podsumowanie** przejrzyj ustawienia, a następnie wybierz przycisk **dalej**.

    ![Strona podsumowania](./media/load-azure-sql-data-warehouse/summary-page.png)
1. Na **stronie wdrażanie**wybierz pozycję **Monitoruj** , aby monitorować potok (zadanie).

1. Zwróć uwagę, że karta **Monitor** po lewej stronie jest automatycznie wybrana. Po pomyślnym zakończeniu przebiegu potoku wybierz łącze **CopyFromSQLToSQLDW** w kolumnie **Nazwa potoku** , aby wyświetlić szczegóły uruchomienia działania i ponownie uruchomić potok.

    [![Monitorowanie uruchomień potoku](./media/load-azure-sql-data-warehouse/pipeline-monitoring.png)](./media/load-azure-sql-data-warehouse/pipeline-monitoring.png#lightbox)
1. Aby przełączyć się z powrotem do widoku uruchomienia potoków, wybierz link **wszystkie uruchomienia potoku** u góry. Wybierz pozycję **Odśwież**, aby odświeżyć listę.

    ![Monitorowanie uruchomień działania](./media/load-azure-sql-data-warehouse/activity-monitoring.png)

1. Aby monitorować szczegóły wykonania dla każdego działania kopiowania, wybierz link **szczegóły** (ikona okularów) w obszarze **Nazwa działania** w widoku uruchomienia działania. Możesz monitorować szczegóły, takie jak ilość danych skopiowanych ze źródła do ujścia, przepływność danych, etapy wykonywania z odpowiednim czasem trwania i używane konfiguracje.
    ![Monitoruj szczegóły uruchomienia działania](./media/load-azure-sql-data-warehouse/monitor-activity-run-details-1.png)

    ![Monitoruj szczegóły uruchomienia działania](./media/load-azure-sql-data-warehouse/monitor-activity-run-details-2.png)

## <a name="next-steps"></a>Następne kroki

Przejdź do poniższego artykułu, aby dowiedzieć się więcej o pomocy technicznej Azure SQL Data Warehouse:

> [!div class="nextstepaction"]
>[Łącznik Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md)
