---
title: Kopiowanie danych z usługi Azure Blob Storage do programu SQL przy użyciu narzędzia Kopiowanie danych
description: Utwórz Azure Data Factory a następnie użyj narzędzia Kopiowanie danych, aby skopiować dane z usługi Azure Blob Storage do SQL Database.
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.topic: tutorial
ms.custom: seo-lt-2019
ms.date: 11/09/2020
ms.openlocfilehash: 585a325c2a5650510b2138c0d708cebdfd17d034
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100375735"
---
# <a name="copy-data-from-azure-blob-storage-to-a-sql-database-by-using-the-copy-data-tool"></a>Kopiowanie danych z usługi Azure Blob Storage do SQL Database przy użyciu narzędzia Kopiowanie danych

> [!div class="op_single_selector" title1="Wybierz używaną wersję usługi Data Factory:"]
> * [Wersja 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Bieżąca wersja](tutorial-copy-data-tool.md)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

W tym samouczku utworzysz fabrykę danych za pomocą witryny Azure Portal. Następnie użyj narzędzia Kopiowanie danych, aby utworzyć potok, który kopiuje dane z usługi Azure Blob Storage do SQL Database.

> [!NOTE]
> Jeśli jesteś nowym użytkownikiem usługi Azure Data Factory, zobacz [Wprowadzenie do usługi Azure Data Factory](introduction.md).

Ten samouczek obejmuje wykonanie następujących kroków:
> [!div class="checklist"]
> * Tworzenie fabryki danych.
> * Tworzenie potoku za pomocą narzędzia do kopiowania danych.
> * Monitorowanie uruchomień potoku i działań.

## <a name="prerequisites"></a>Wymagania wstępne

* **Subskrypcja platformy Azure**: jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/).
* **Konto usługi Azure Storage**: Użyj magazynu obiektów BLOB jako _źródłowego_ magazynu danych. Jeśli nie masz konta usługi Azure Storage, zapoznaj się z instrukcjami w temacie [Tworzenie konta magazynu](../storage/common/storage-account-create.md).
* **Azure SQL Database**: Użyj SQL Database jako magazynu danych _ujścia_ . Jeśli nie masz SQL Database, zapoznaj się z instrukcjami w artykule [tworzenie SQL Database](../azure-sql/database/single-database-create-quickstart.md).

### <a name="create-a-blob-and-a-sql-table"></a>Tworzenie obiektu blob i tabeli SQL

Wykonaj następujące kroki, aby przygotować magazyn obiektów blob i SQL Database dla tego samouczka.

#### <a name="create-a-source-blob"></a>Tworzenie źródłowego obiektu Blob

1. Uruchom **Notatnik**. Skopiuj poniższy tekst i zapisz go na dysku w pliku o nazwie **inputEmp.txt**:

    ```
    FirstName|LastName
    John|Doe
    Jane|Doe
    ```

1. Utwórz kontener o nazwie **adfv2tutorial** i przekaż plik inputEmp.txt do kontenera. Aby wykonać te zadania, można użyć Azure Portal lub różnych narzędzi, takich jak [Eksplorator usługi Azure Storage](https://storageexplorer.com/) .

#### <a name="create-a-sink-sql-table"></a>Tworzenie tabeli SQL ujścia

1. Użyj poniższego skryptu SQL, aby utworzyć tabelę o nazwie **dbo. EMP** w SQL Database:

    ```sql
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50)
    )
    GO

    CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID);
    ```

2. Zezwól usługom platformy Azure na dostęp do programu SQL Server. Sprawdź, czy ustawienie **Zezwalaj usługom i zasobom platformy Azure na dostęp do tego serwera** jest włączone dla serwera, na którym działa SQL Database. To ustawienie umożliwia usłudze Data Factory zapisywanie danych w danym wystąpieniu bazy danych. Aby sprawdzić i włączyć to ustawienie, przejdź do pozycji logiczne SQL Server > zabezpieczenia > zapory i sieci wirtualne > ustaw opcję **Zezwól usługom i zasobom platformy Azure na dostęp do tego serwera** **w systemie**.

## <a name="create-a-data-factory"></a>Tworzenie fabryki danych

1. W menu po lewej stronie wybierz pozycję **Utwórz zasób**  >  **integracja**  >  **Data Factory**:

    ![Tworzenie nowej fabryki danych](./media/doc-common-process/new-azure-data-factory-menu.png)
1. Na stronie **Nowa fabryka danych** w polu **Nazwa** wprowadź wartość **ADFTutorialDataFactory**.

    Nazwa Twojej fabryki danych musi być _globalnie unikatowa_. Może zostać wyświetlony następujący komunikat o błędzie:

    ![Komunikat o błędzie dotyczący nowej fabryki danych](./media/doc-common-process/name-not-available-error.png)

    Jeśli zostanie wyświetlony komunikat o błędzie dotyczącym wartości nazwy, wprowadź inną nazwę dla fabryki danych. Na przykład użyj nazwy _**twojanazwa**_**ADFTutorialDataFactory**. Artykuł [Data Factory naming rules (Zasady nazewnictwa fabryki danych)](naming-rules.md) zawiera zasady nazewnictwa artefaktów usługi Data Factory.
1. Wybierz **subskrypcję** platformy Azure, w której utworzysz nową fabrykę danych.
1. W obszarze **Grupa zasobów** wykonaj jedną z następujących czynności:

    a. Wybierz pozycję **Użyj istniejącej**, a następnie wybierz istniejącą grupę zasobów z listy rozwijanej.

    b. Wybierz pozycję **Utwórz nową**, a następnie wprowadź nazwę grupy zasobów.
    
    Informacje na temat grup zasobów znajdują się w artykule [Using resource groups to manage your Azure resources (Używanie grup zasobów do zarządzania zasobami platformy Azure)](../azure-resource-manager/management/overview.md).

1. W obszarze **Wersja** wybierz wersję **V2**.
1. W obszarze **Lokalizacja** wybierz lokalizację fabryki danych. Na liście rozwijanej są wyświetlane tylko obsługiwane lokalizacje. Magazyny danych (np. usługi Azure Storage i SQL Database) oraz jednostki obliczeniowe (np. usługa Azure HDInsight) używane przez Twoją fabrykę danych mogą mieścić się w innych lokalizacjach i regionach.
1. Wybierz przycisk **Utwórz**.

1. Po zakończeniu tworzenia zostanie wyświetlona strona główna usługi **Data Factory**.

    ![Strona główna fabryki danych](./media/doc-common-process/data-factory-home-page.png)
1. Aby w osobnej karcie uruchomić interfejs użytkownika usługi Azure Data Factory, kliknij kafelek **Tworzenie i monitorowanie**.

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Tworzenie potoku za pomocą narzędzia do kopiowania danych

1. Na stronie **Wprowadzenie** wybierz kafelek **Kopiowanie danych**, aby uruchomić narzędzie do kopiowania danych.

    ![Kafelek narzędzia do kopiowania danych](./media/doc-common-process/get-started-page.png)
1. Na stronie **Właściwości** w obszarze **Nazwa zadania**, wprowadź wartość **CopyFromBlobToSqlPipeline**. Następnie wybierz przycisk **Dalej**. Interfejs użytkownika usługi Data Factory tworzy potok o określonej nazwie zadania.
    ![Tworzenie potoku](./media/tutorial-copy-data-tool/create-pipeline.png)

1. Na stronie **Źródłowy magazyn danych** wykonaj następujące czynności:

    a. Kliknij pozycję **+Utwórz nowe połączenie**, aby dodać połączenie.

    b. Wybierz pozycję **Azure Blob Storage** z galerii, a następnie wybierz pozycję **Kontynuuj**.

    c. Na stronie **Nowa połączona usługa** wybierz subskrypcję platformy Azure, a następnie wybierz konto magazynu z listy **nazwa konta magazynu** . Test connection a następnie wybierz pozycję **Utwórz**.

    d. Wybierz nowo utworzoną połączoną usługę jako źródło, a następnie kliknij pozycję **Dalej**.

    ![Wybieranie połączonej usługi źródłowej](./media/tutorial-copy-data-tool/select-source-linked-service.png)

1. Na stronie **Wybieranie pliku lub folderu wejściowego** wykonaj następujące czynności:

    a. Kliknij pozycję **Przeglądaj**, aby przejść do folderu **adfv2tutorial/input**, wybierz plik **inputEmp.txt**, a następnie kliknij przycisk **Wybierz**.

    b. Kliknij przycisk **Dalej**, aby przejść do następnego kroku.

1. Na stronie **Ustawienia formatu pliku** zaznacz pole wyboru *pierwszy wiersz jako nagłówek*. Należy zauważyć, że narzędzie automatycznie wykrywa ograniczniki kolumn i wierszy. Wybierz opcję **Dalej**. Możesz także wyświetlić podgląd danych i wyświetlić schemat danych wejściowych na tej stronie.

    ![Ustawienia formatu pliku](./media/tutorial-copy-data-tool/file-format-settings-page.png)
1. Na stronie **Docelowy magazyn danych** wykonaj następujące czynności:

    a. Kliknij pozycję **+Utwórz nowe połączenie**, aby dodać połączenie.

    b. Wybierz **Azure SQL Database** z galerii, a następnie wybierz pozycję **Kontynuuj**.

    c. Na stronie **Nowa połączona usługa** wybierz nazwę serwera i nazwę bazy danych z listy rozwijanej, a następnie określ nazwę użytkownika i hasło, a następnie wybierz pozycję **Utwórz**.

    ![Konfigurowanie bazy danych Azure SQL](./media/tutorial-copy-data-tool/config-azure-sql-db.png)

    d. Wybierz nowo utworzoną połączoną usługę jako ujście, a następnie kliknij pozycję **Dalej**.

1. Na stronie **Mapowanie tabeli** wybierz tabelę **[dbo].[emp]**, a następnie kliknij przycisk **Dalej**.

1. Na stronie **Mapowanie kolumny** Zwróć uwagę, że druga i trzecia kolumna w pliku wejściowym są mapowane na kolumny **FirstName** i **LastName** tabeli **EMP** . Dostosuj mapowanie, aby upewnić się, że nie ma błędów, a następnie wybierz przycisk **dalej**.

    ![Strona mapowania kolumn](./media/tutorial-copy-data-tool/column-mapping.png)

1. Na stronie **Ustawienia** wybierz przycisk **Dalej**.

1. Na stronie **Podsumowanie** sprawdź ustawienia, a następnie kliknij przycisk **Dalej**.

1. Na **stronie Wdrażanie** wybierz pozycję **Monitorowanie**, aby monitorować potok (zadanie).

    ![Monitorowanie potoku](./media/tutorial-copy-data-tool/monitor-pipeline.png)
    
1. Na stronie uruchomienia potoku wybierz pozycję **Odśwież** , aby odświeżyć listę. Kliknij link pod **nazwą potoku** , aby wyświetlić szczegóły uruchomienia działania lub ponownie uruchomić potok. 
    ![Uruchomienie potoku](./media/tutorial-copy-data-tool/pipeline-run.png)

1. Na stronie uruchomienia działania wybierz link **szczegóły** (ikona okularów) w kolumnie **Nazwa działania** , aby uzyskać więcej informacji na temat operacji kopiowania. Aby wrócić do widoku uruchomień potoków, wybierz link **wszystkie uruchomienia potoków** w menu stron nadrzędnych. Aby odświeżyć widok, wybierz pozycję **Odśwież**.

    ![Monitorowanie uruchomień działania](./media/tutorial-copy-data-tool/activity-monitoring.png)

1. Sprawdź, czy dane są wstawiane do tabeli **dbo. EMP** w SQL Database.

1. Wybierz kartę **Autor** po lewej stronie, aby przełączyć się w tryb edytora. Za pomocą edytora można zaktualizować usługi połączone, zestawy danych i potoki utworzone przez narzędzie. Aby uzyskać szczegółowe informacje dotyczące edytowania tych jednostek w interfejsie użytkownika usługi Data Factory, zobacz [wersję witryny Azure Portal używaną w tym samouczku](tutorial-copy-data-portal.md).

    ![Wybieranie karty autora](./media/tutorial-copy-data-tool/author-tab.png)

## <a name="next-steps"></a>Następne kroki
Potok w tym przykładzie kopiuje dane z magazynu obiektów BLOB do SQL Database. W tym samouczku omówiono:

> [!div class="checklist"]
> * Tworzenie fabryki danych.
> * Tworzenie potoku za pomocą narzędzia do kopiowania danych.
> * Monitorowanie uruchomień potoku i działań.

Aby dowiedzieć się, jak kopiować dane ze środowiska lokalnego do chmury, przejdź do następującego samouczka:

>[!div class="nextstepaction"]
>[Kopiowanie danych ze środowiska lokalnego do chmury](tutorial-hybrid-copy-data-tool.md)
