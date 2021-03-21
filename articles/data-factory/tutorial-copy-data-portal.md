---
title: Użyj Azure Portal, aby utworzyć potok fabryki danych
description: Ten samouczek zawiera instrukcje krok po kroku dotyczące tworzenia fabryki danych z potokiem za pomocą witryny Azure Portal. Potok używa działania kopiowania do kopiowania danych z usługi Azure Blob Storage do Azure SQL Database.
author: linda33wj
ms.service: data-factory
ms.topic: tutorial
ms.custom: seo-lt-2019
ms.date: 02/18/2021
ms.author: jingwang
ms.openlocfilehash: 08ab3c9181f4fdcfc9b55bf80c53f88ff26f90ce
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104584415"
---
# <a name="copy-data-from-azure-blob-storage-to-a-database-in-azure-sql-database-by-using-azure-data-factory"></a>Kopiowanie danych z usługi Azure Blob Storage do bazy danych w Azure SQL Database przy użyciu Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym samouczku utworzysz fabrykę danych przy użyciu interfejsu użytkownika usługi Azure Data Factory. Potok w tej fabryce danych kopiuje dane z usługi Azure Blob Storage do bazy danych w Azure SQL Database. Wzorzec konfiguracji w tym samouczku ma zastosowanie do kopiowania danych z magazynu opartego na plikach do relacyjnego magazynu danych. Aby zapoznać się z listą magazynów danych obsługiwanych jako źródła i ujścia, zobacz tabelę zawierającą [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).

> [!NOTE]
> Jeśli jesteś nowym użytkownikiem usługi Data Factory, zobacz [Wprowadzenie do usługi Azure Data Factory](introduction.md).

Ten samouczek obejmuje wykonanie następujących kroków:

> [!div class="checklist"]
> * Tworzenie fabryki danych.
> * Tworzenie potoku z działaniem kopiowania.
> * Testowe uruchamianie potoku.
> * Ręczne wyzwalanie potoku.
> * Wyzwalanie potoku zgodnie z harmonogramem.
> * Monitorowanie uruchomień potoku i działań.

## <a name="prerequisites"></a>Wymagania wstępne
* **Subskrypcja platformy Azure**. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/) .
* **Konto usługi Azure Storage**. Magazyn obiektów blob jest używany jako magazyn danych będący *źródłem*. Jeśli nie masz konta magazynu, utwórz je, wykonując czynności przedstawione w artykule [Tworzenie konta magazynu platformy Azure](../storage/common/storage-account-create.md).
* **Azure SQL Database**. Baza danych jest używana jako magazyn danych będący *ujściem*. Jeśli nie masz bazy danych w Azure SQL Database, zapoznaj się z instrukcjami [tworzenia bazy danych w Azure SQL Database](../azure-sql/database/single-database-create-quickstart.md) .

### <a name="create-a-blob-and-a-sql-table"></a>Tworzenie obiektu blob i tabeli SQL

Teraz przygotuj swój magazyn obiektów blob i bazę danych SQL na potrzeby tego samouczka, wykonując następujące kroki.

#### <a name="create-a-source-blob"></a>Tworzenie źródłowego obiektu Blob

1. Uruchom program Notatnik. Skopiuj poniższy tekst i zapisz go na dysku jako plik **emp.txt**:

    ```
    FirstName,LastName
    John,Doe
    Jane,Doe
    ```

1. W magazynie obiektów blob utwórz kontener o nazwie **adftutorial**. W tym kontenerze utwórz folder o nazwie **input**. Następnie przekaż plik **emp.txt** do folderu **input**. Do wykonania tych zadań użyj witryny Azure Portal lub narzędzi takich jak [Eksplorator usługi Azure Storage](https://storageexplorer.com/).

#### <a name="create-a-sink-sql-table"></a>Tworzenie tabeli SQL ujścia

1. Użyj poniższego skryptu SQL, aby utworzyć tabelę **dbo. EMP** w bazie danych:

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

1. Zezwól usługom platformy Azure na dostęp do programu SQL Server. Upewnij się, że ustawienie **Zezwalaj na dostęp do usług platformy Azure** jest włączone i ma wartość **WŁĄCZ** dla programu SQL Server, aby usługa Data Factory mogła zapisywać dane w programie SQL Server. Aby sprawdzić i włączyć to ustawienie, przejdź do logicznego serwera SQL > Omówienie > Ustaw zaporę serwera> ustaw opcję **Zezwalaj na dostęp do usług platformy Azure** na wartość **włączone**.

## <a name="create-a-data-factory"></a>Tworzenie fabryki danych
W tym kroku utworzysz fabrykę danych i uruchomisz interfejs użytkownika usługi Data Factory, aby utworzyć potok w fabryce danych.

1. Otwórz przeglądarkę **Microsoft Edge** lub **Google Chrome**. Obecnie interfejs użytkownika usługi Data Factory jest obsługiwany tylko przez przeglądarki internetowe Microsoft Edge i Google Chrome.
2. W menu po lewej stronie wybierz pozycję **Utwórz zasób**  >  **integracja**  >  **Data Factory**.
3. Na stronie **tworzenie Data Factory** w obszarze **podstawowe** karty wybierz **subskrypcję** platformy Azure, w której chcesz utworzyć fabrykę danych.
4. W obszarze **Grupa zasobów** wykonaj jedną z następujących czynności:

    a. Wybierz istniejącą grupę zasobów z listy rozwijanej.

    b. Wybierz pozycję **Utwórz nową**, a następnie wprowadź nazwę nowej grupy zasobów.
    
    Informacje na temat grup zasobów znajdują się w artykule [Using resource groups to manage your Azure resources (Używanie grup zasobów do zarządzania zasobami platformy Azure)](../azure-resource-manager/management/overview.md). 
5. W obszarze **region** wybierz lokalizację fabryki danych. Na liście rozwijanej są wyświetlane tylko obsługiwane lokalizacje. Magazyny danych (np. usługi Azure Storage i SQL Database) oraz jednostki obliczeniowe (np. usługa Azure HDInsight) używane przez fabrykę danych mogą znajdować się w innych regionach.
6. W polu **Nazwa** wprowadź **ADFTutorialDataFactory**.

   Nazwa fabryki danych Azure musi być *globalnie unikatowa*. Jeśli zostanie wyświetlony komunikat o błędzie dotyczącym wartości nazwy, wprowadź inną nazwę dla fabryki danych. (na przykład Twojanazwaadftutorialdatafactory). Reguły nazewnictwa dla artefaktów usługi Data Factory można znaleźć w artykule [Data Factory — reguły nazewnictwa](naming-rules.md).

    :::image type="content" source="./media/doc-common-process/name-not-available-error.png" alt-text="Nowy komunikat o błędzie usługi Fabryka danych dla zduplikowanej nazwy.":::

7. W obszarze **Wersja** wybierz pozycję **V2**.
8. Wybierz kartę **Konfiguracja usługi git** u góry, a następnie zaznacz pole wyboru **Konfiguruj git później** .
9. Wybierz pozycję **Recenzja + Utwórz**, a następnie wybierz pozycję **Utwórz** po pomyślnym przekazaniu walidacji.
10. Po zakończeniu tworzenia zobaczysz powiadomienie w centrum powiadomień. Wybierz pozycję **Przejdź do zasobu** , aby przejść do strony Fabryka danych.
11. Wybierz pozycję **utwórz & monitor** , aby uruchomić interfejs użytkownika Azure Data Factory na oddzielnej karcie.


## <a name="create-a-pipeline"></a>Tworzenie potoku
W tym kroku utworzysz potok z działaniem kopiowania w fabryce danych. Działanie kopiowania kopiuje dane z magazynu obiektów blob do usługi SQL Database. W [samouczku szybkiego startu](quickstart-create-data-factory-portal.md) utworzono potok, wykonując następujące czynności:

1. Utworzenie połączonej usługi.
1. Utworzenie wejściowych i wyjściowych zestawów danych.
1. Tworzenie potoku.

W tym samouczku zaczniesz od utworzenia potoku. Następnie utworzysz usługi połączone i zestawy danych, gdy będą potrzebne do skonfigurowania potoku.

1. Na stronie **Zaczynajmy** wybierz pozycję **Utwórz potok**.

   ![Tworzenie potoku](./media/doc-common-process/get-started-page.png)

1. W panelu Ogólne w obszarze **Właściwości** Określ **CopyPipeline** dla **nazwy**. Następnie Zwiń panel, klikając ikonę właściwości w prawym górnym rogu.

1. W oknie Narzędzie **działania** rozwiń kategorię **przenoszenie i przekształcanie** , a następnie przeciągnij i upuść działanie **Kopiowanie danych** z okna narzędzia do powierzchni projektanta potoku. Wprowadź wartość **CopyFromBlobToSql** w polu **Nazwa**.

    ![Działanie kopiowania](./media/tutorial-copy-data-portal/drag-drop-copy-activity.png)

### <a name="configure-source"></a>Konfigurowanie źródła

>[!TIP]
>W tym samouczku użyto *klucza konta* jako typu uwierzytelniania dla źródłowego magazynu danych, ale można wybrać inne obsługiwane metody uwierzytelniania: *Identyfikator URI SAS*,*nazwę główną usługi* i *tożsamość zarządzaną* , jeśli jest to konieczne. Aby uzyskać szczegółowe informacje, zapoznaj się z odpowiednimi sekcjami w [tym artykule](./connector-azure-blob-storage.md#linked-service-properties) .
>Aby bezpiecznie przechowywać wpisy tajne dla magazynów danych, zaleca się również korzystanie z Azure Key Vault. Szczegółowe ilustracje znajdują się w [tym artykule](./store-credentials-in-key-vault.md) .

1. Przejdź do karty **Źródło** . Wybierz pozycję **+ Nowy** , aby utworzyć źródłowy zestaw danych.

1. W oknie dialogowym **Nowy zestaw danych** wybierz pozycję **Azure Blob Storage**, a następnie wybierz pozycję **Kontynuuj**. Dane źródłowe znajdują się w magazynie obiektów blob, musisz więc wybrać usługę **Azure Blob Storage** dla źródłowego zestawu danych.

1. W oknie dialogowym **Wybieranie formatu** wybierz typ formatu danych, a następnie wybierz pozycję **Kontynuuj**.

1. W oknie dialogowym **Ustawianie właściwości** wpisz **SourceBlobDataset** w polu Nazwa. Zaznacz pole wyboru **pierwszy wiersz jako nagłówek**. W polu tekstowym **połączona usługa** wybierz pozycję **+ Nowy**.

1. W oknie dialogowym **Nowa połączona usługa (Azure Blob Storage)** wprowadź **AzureStorageLinkedService** jako nazwę, a następnie wybierz konto magazynu z listy **nazwa konta magazynu** . Test connection wybierz pozycję **Utwórz** , aby wdrożyć połączoną usługę.

1. Po utworzeniu połączonej usługi zostanie ona wyłączona z powrotem do strony **Ustawianie właściwości** . Wybierz przycisk **Przeglądaj** obok pozycji **Ścieżka pliku**.

1. Przejdź do folderu **adftutorial/Input** , wybierz plik **emp.txt** , a następnie wybierz przycisk **OK**.

1. Wybierz przycisk **OK**. Automatycznie przechodzi do strony potoku. Na karcie **Źródło** upewnij się, że wybrano opcję **SourceBlobDataset** . Aby wyświetlić podgląd danych na tej stronie, wybierz pozycję **Podgląd danych**.

    ![Zestaw danych źródłowych](./media/tutorial-copy-data-portal/source-dataset-selected.png)

### <a name="configure-sink"></a>Konfigurowanie ujścia
>[!TIP]
>W tym samouczku jako typ uwierzytelniania dla magazynu danych ujścia używasz *uwierzytelniania SQL* , ale możesz wybrać inne obsługiwane metody uwierzytelniania: *nazwę główną usługi* i *tożsamość zarządzaną* , jeśli jest to konieczne. Aby uzyskać szczegółowe informacje, zapoznaj się z odpowiednimi sekcjami w [tym artykule](./connector-azure-sql-database.md#linked-service-properties) .
>Aby bezpiecznie przechowywać wpisy tajne dla magazynów danych, zaleca się również korzystanie z Azure Key Vault. Szczegółowe ilustracje znajdują się w [tym artykule](./store-credentials-in-key-vault.md) .

1. Przejdź do karty **Ujście**, a następnie wybierz pozycję **+ Nowy**, aby utworzyć zestaw danych będący ujściem.

1. W oknie dialogowym **Nowy zestaw danych** wprowadź wartość "SQL" w polu wyszukiwania, aby odfiltrować łączniki, wybierz pozycję **Azure SQL Database**, a następnie wybierz pozycję **Kontynuuj**. W tym samouczku skopiujesz dane do bazy danych SQL.

1. W oknie dialogowym **Ustawianie właściwości** wpisz **OutputSqlDataset** w polu Nazwa. Z listy rozwijanej **połączona usługa** wybierz pozycję **+ Nowy**. Zestaw danych musi być skojarzony z połączoną usługą. Połączona usługa ma parametry połączenia, których Data Factory używa do nawiązywania połączenia z SQL Database w czasie wykonywania. Zestaw danych określa kontener, folder i plik (opcjonalnie), do którego dane są kopiowane.

1. W oknie dialogowym **Nowa połączona usługa (Azure SQL Database)** wykonaj następujące czynności:

    a. W obszarze **Nazwa** wprowadź wartość **AzureSqlDatabaseLinkedService**.

    b. W polu **Nazwa serwera** wybierz swoje wystąpienie programu SQL Server.

    c. W obszarze **Nazwa bazy danych** wybierz swoją bazę danych.

    d. W polu **Nazwa użytkownika** wprowadź nazwę użytkownika.

    e. W polu **Hasło** wprowadź hasło użytkownika.

    f. Wybierz pozycję **Testuj połączenie**, aby przetestować połączenie.

    przykład Wybierz pozycję **Utwórz** , aby wdrożyć połączoną usługę.

    ![Zapisywanie nowej połączonej usługi](./media/tutorial-copy-data-portal/new-azure-sql-linked-service-window.png)

1. Powoduje automatyczne przejście do okna dialogowego **Ustawianie właściwości** . W obszarze **Tabela** wybierz pozycję **[dbo].[emp]**. Następnie wybierz przycisk **OK**.

1. Przejdź do karty z potokiem i upewnij się, że w obszarze **Zestaw danych będący ujściem** wybrano pozycję **OutputSqlDataset**.

    ![Karta potoku](./media/tutorial-copy-data-portal/pipeline-tab-2.png)       

Opcjonalnie można zmapować schemat źródła do odpowiedniego schematu docelowego przez następujące [Mapowanie schematu w działaniu kopiowania](copy-activity-schema-and-type-mapping.md).

## <a name="validate-the-pipeline"></a>Weryfikowanie potoku
Wybierz na pasku narzędzi pozycję **Weryfikuj**, aby zweryfikować potok.

Kod JSON skojarzony z potokiem można zobaczyć, klikając **kod** w prawym górnym rogu.

## <a name="debug-and-publish-the-pipeline"></a>Debugowanie i publikowanie potoku
Przed opublikowaniem artefaktów (połączone usługi, zestawy danych i potok) w usłudze Data Factory lub własnym repozytorium Git usługi Azure Repos możesz debugować potok.

1. Aby debugować potok, wybierz na pasku narzędzi pozycję **Debuguj**. Na karcie **Dane wyjściowe** w dolnej części okna wyświetlany jest stan uruchomienia potoku.

1. Po pomyślnym uruchomieniu potoku na górnym pasku narzędzi wybierz pozycję **Opublikuj wszystko**. Ta akcja powoduje opublikowanie utworzonych jednostek (zestawy danych i potok) w usłudze Data Factory.

1. Poczekaj na wyświetlenie komunikatu **Pomyślnie opublikowano**. Aby wyświetlić komunikaty powiadomień, kliknij pozycję **Pokaż powiadomienia** w prawym górnym rogu (przycisk dzwonka).

## <a name="trigger-the-pipeline-manually"></a>Ręczne wyzwalanie potoku
W tym kroku ręcznie wyzwolisz potok, który został opublikowany w poprzednim kroku.

1. Wybierz pozycję **Wyzwól** na pasku narzędzi, a następnie wybierz pozycję **Wyzwól teraz**. Na stronie **uruchomienie potoku** wybierz pozycję **OK**.  

1. Przejdź do karty **Monitorowanie** po lewej stronie. Widoczne jest uruchomienie potoku, które zostało wyzwolone za pomocą wyzwalacza ręcznego. Możesz użyć linków w kolumnie **Nazwa potoku** , aby wyświetlić szczegóły działania i ponownie uruchomić potok.

    [![Monitorowanie uruchomień potoku](./media/tutorial-copy-data-portal/monitor-pipeline-inline-and-expended.png)](./media/tutorial-copy-data-portal/monitor-pipeline-inline-and-expended.png#lightbox)

1. Aby wyświetlić uruchomienia działań skojarzone z uruchomieniem potoku, wybierz link **CopyPipeline** w kolumnie **Nazwa potoku** . W tym przykładzie istnieje tylko jedno działanie, dlatego na liście jest widoczny tylko jeden wpis. Aby uzyskać szczegółowe informacje na temat operacji kopiowania, wybierz link **szczegóły** (ikona okularów) w kolumnie **Nazwa działania** . Zaznacz opcję **wszystkie uruchomienia potoków** u góry, aby wrócić do widoku uruchomienia potoków. Aby odświeżyć widok, wybierz pozycję **Odśwież**.

    [![Monitorowanie uruchomień działania](./media/tutorial-copy-data-portal/view-activity-runs-inline-and-expended.png)](./media/tutorial-copy-data-portal/view-activity-runs-inline-and-expended.png#lightbox)

1. Sprawdź, czy dodano dwa więcej wierszy do tabeli **EMP** w bazie danych.

## <a name="trigger-the-pipeline-on-a-schedule"></a>Wyzwalanie potoku zgodnie z harmonogramem
W tym kroku utworzysz wyzwalacz harmonogramu potoku. Wyzwalacz uruchamia potok zgodnie z określonym harmonogramem, na przykład co godzinę lub codziennie. W tym miejscu ustawisz wyzwalacz do uruchomienia co minutę, aż do określonej daty/godziny zakończenia.

1. Przejdź do karty **Tworzenie** po lewej stronie karty Monitorowanie.

1. Przejdź do potoku, kliknij pozycję **Wyzwalacz** na pasku narzędzi, a następnie wybierz pozycję **Nowy/Edytuj**.

1. W oknie dialogowym **Dodawanie wyzwalaczy** wybierz pozycję **+ Nowy** dla **opcji wybierz obszar wyzwalacza** .

1. W oknie **Nowy wyzwalacz** wykonaj następujące czynności:

    a. W obszarze **Nazwa** wprowadź wartość **RunEveryMinute**.

    b. Zaktualizuj **datę początkową** dla wyzwalacza. Jeśli data przypada przed bieżącą datą i godziną, wyzwalacz zacznie obowiązywać po opublikowaniu zmiany. 

    c. W obszarze **strefa czasowa** wybierz listę rozwijaną.

    d. Ustaw **cykl** na **co 1 minutę**.

    e. Zaznacz pole wyboru **Określ datę końcową** i zaktualizuj **koniec** części na kilka minut przed bieżącą datą i godziną. Wyzwalacz zostanie aktywowany tylko w przypadku, gdy opublikujesz zmiany. Jeśli ustawisz ją na kilka minut od siebie i nie opublikujesz jej w tym czasie, nie zobaczysz uruchomienia wyzwalacza.

    f. Dla opcji **aktywowana** wybierz pozycję **tak**.

    przykład Wybierz przycisk **OK**.

    > [!IMPORTANT]
    > Za poszczególne uruchomienia potoku są naliczane opłaty, zatem określ stosowną datę zakończenia.

1. Na stronie **Edytowanie wyzwalacza** Sprawdź ostrzeżenie, a następnie wybierz pozycję **Zapisz**. Potok w tym przykładzie nie przyjmuje żadnych parametrów.

1. Kliknij przycisk **Opublikuj wszystko** , aby opublikować zmianę.

1. Przejdź do karty **Monitorowanie** po lewej stronie, aby zobaczyć wyzwolone uruchomienia potoku.

    [![Wyzwolone uruchomienia potoku](./media/tutorial-copy-data-portal/triggered-pipeline-runs-inline-and-expended.png)](./media/tutorial-copy-data-portal/triggered-pipeline-runs-inline-and-expended.png#lightbox)

1. Aby przełączyć się z widoku **uruchomienia potoku** do widoku **uruchomienia wyzwalacza** , wybierz pozycję **wyzwalacz uruchomienia** po lewej stronie okna.

1. Uruchomienia wyzwalacza znajdują się na liście.

1. Sprawdź, czy do tabeli **emp** są wstawiane dwa wiersze na minutę (dla każdego uruchomienia potoku), aż do określonego czasu zakończenia.

## <a name="next-steps"></a>Następne kroki
Potok w tym przykładzie kopiuje dane z jednej lokalizacji do innej lokalizacji w magazynie obiektów blob. W tym samouczku omówiono:

> [!div class="checklist"]
> * Tworzenie fabryki danych.
> * Tworzenie potoku z działaniem kopiowania.
> * Testowe uruchamianie potoku.
> * Ręczne wyzwalanie potoku.
> * Wyzwalanie potoku zgodnie z harmonogramem.
> * Monitorowanie uruchomień potoku i działań.


Aby dowiedzieć się, jak kopiować dane ze środowiska lokalnego do chmury, przejdź do następującego samouczka:

> [!div class="nextstepaction"]
>[Kopiowanie danych ze środowiska lokalnego do chmury](tutorial-hybrid-copy-portal.md)
