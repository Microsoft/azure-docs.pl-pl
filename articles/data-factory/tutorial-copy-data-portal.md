---
title: Tworzenie potoku fabryki danych za pomocą portalu Azure
description: Ten samouczek zawiera instrukcje krok po kroku dotyczące tworzenia fabryki danych z potokiem za pomocą witryny Azure Portal. Potok używa działania kopiowania do kopiowania danych z magazynu obiektów Blob platformy Azure do bazy danych SQL platformy Azure.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.custom: seo-lt-2019
ms.date: 04/13/2020
ms.author: jingwang
ms.openlocfilehash: 655a98ef1b6b8b2d4086b472ee7ce4d67346e5ca
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418715"
---
# <a name="copy-data-from-azure-blob-storage-to-a-sql-database-by-using-azure-data-factory"></a>Kopiowanie danych z usługi Azure Blob Storage do bazy danych SQL za pomocą usługi Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

W tym samouczku utworzysz fabrykę danych przy użyciu interfejsu użytkownika usługi Azure Data Factory. Potok w tej fabryce danych kopiuje dane z magazynu obiektów Blob platformy Azure do bazy danych SQL platformy Azure. Wzorzec konfiguracji w tym samouczku ma zastosowanie do kopiowania danych z magazynu opartego na plikach do relacyjnego magazynu danych. Aby zapoznać się z listą magazynów danych obsługiwanych jako źródła i ujścia, zobacz tabelę zawierającą [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).

> [!NOTE]
> - Jeśli jesteś nowym użytkownikiem usługi Data Factory, zobacz [Wprowadzenie do usługi Azure Data Factory](introduction.md).

Ten samouczek obejmuje wykonanie następujących kroków:

> [!div class="checklist"]
> * Tworzenie fabryki danych.
> * Tworzenie potoku z działaniem kopiowania.
> * Testowe uruchamianie potoku.
> * Ręczne wyzwalanie potoku.
> * Wyzwalanie potoku zgodnie z harmonogramem.
> * Monitorowanie uruchomień potoku i działań.

## <a name="prerequisites"></a>Wymagania wstępne
* **Subskrypcja platformy Azure**. Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/) przed rozpoczęciem.
* **Konto magazynu platformy Azure**. Magazyn obiektów blob jest używany jako magazyn danych będący *źródłem*. Jeśli nie masz konta magazynu, utwórz je, wykonując czynności przedstawione w artykule [Tworzenie konta magazynu platformy Azure](../storage/common/storage-account-create.md).
* **Baza danych SQL platformy Azure**. Baza danych jest używana jako magazyn danych będący *ujściem*. Jeśli nie masz bazy danych SQL platformy Azure, zobacz [Tworzenie bazy danych SQL,](../sql-database/sql-database-get-started-portal.md) aby uzyskać kroki, aby ją utworzyć.

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

1. Utwórz tabelę **dbo.emp** w bazie danych SQL przy użyciu poniższego skryptu SQL:

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

1. Zezwól usługom platformy Azure na dostęp do programu SQL Server. Upewnij się, że ustawienie **Zezwalaj na dostęp do usług platformy Azure** jest włączone i ma wartość **WŁĄCZ** dla programu SQL Server, aby usługa Data Factory mogła zapisywać dane w programie SQL Server. Aby zweryfikować i włączyć to ustawienie, przejdź do usługi Azure SQL server > Overview > Set server firewall> ustaw opcję **Zezwalaj na dostęp do usług platformy Azure** na **ON**.

## <a name="create-a-data-factory"></a>Tworzenie fabryki danych
W tym kroku utworzysz fabrykę danych i uruchomisz interfejs użytkownika usługi Data Factory, aby utworzyć potok w fabryce danych.

1. Otwórz przeglądarkę **Microsoft Edge** lub **Google Chrome**. Obecnie interfejs użytkownika usługi Data Factory jest obsługiwany tylko przez przeglądarki internetowe Microsoft Edge i Google Chrome.
2. W menu po lewej stronie wybierz pozycję **Utwórz fabrykę** > **danych****analizy** > zasobów .
3. Na stronie **Nowa fabryka danych** w polu **Nazwa** wprowadź wartość **ADFTutorialDataFactory**.

   Nazwa fabryki danych platformy Azure musi być *unikatowa globalnie.* Jeśli zostanie wyświetlony komunikat o błędzie dotyczącym wartości nazwy, wprowadź inną nazwę dla fabryki danych. (na przykład yournameADFTutorialDataFactory). Reguły nazewnictwa dla artefaktów usługi Data Factory można znaleźć w artykule [Data Factory — reguły nazewnictwa](naming-rules.md).

     ![Nowa fabryka danych](./media/doc-common-process/name-not-available-error.png)
4. Wybierz **subskrypcję** platformy Azure, w której chcesz utworzyć fabrykę danych.
5. W obszarze **Grupa zasobów** wykonaj jedną z następujących czynności:

    a. Wybierz pozycję **Użyj istniejącej**, a następnie wybierz istniejącą grupę zasobów z listy rozwijanej.

    b. Wybierz **pozycję Utwórz nowy**i wprowadź nazwę grupy zasobów. 
         
    Informacje na temat grup zasobów znajdują się w artykule [Using resource groups to manage your Azure resources (Używanie grup zasobów do zarządzania zasobami platformy Azure)](../azure-resource-manager/management/overview.md). 
6. W obszarze **Wersja** wybierz pozycję **V2**.
7. W obszarze **Lokalizacja** wybierz lokalizację fabryki danych. Na liście rozwijanej są wyświetlane tylko obsługiwane lokalizacje. Magazyny danych (np. usługi Azure Storage i SQL Database) oraz jednostki obliczeniowe (np. usługa Azure HDInsight) używane przez fabrykę danych mogą znajdować się w innych regionach.
8. Wybierz pozycję **Utwórz**.
9. Po zakończeniu tworzenia zostanie wyświetlone powiadomienie w Centrum powiadomień. Wybierz **pozycję Przejdź do zasobu,** aby przejść do strony Fabryka danych.
10. Wybierz pozycję **Tworzenie i monitorowanie**, aby uruchomić interfejs użytkownika usługi Data Factory na osobnej karcie.


## <a name="create-a-pipeline"></a>Tworzenie potoku
W tym kroku utworzysz potok z działaniem kopiowania w fabryce danych. Działanie kopiowania kopiuje dane z magazynu obiektów blob do usługi SQL Database. W [samouczku szybkiego startu](quickstart-create-data-factory-portal.md) utworzono potok, wykonując następujące czynności:

1. Utworzenie połączonej usługi.
1. Utworzenie wejściowych i wyjściowych zestawów danych.
1. Tworzenie potoku.

W tym samouczku zaczniesz od utworzenia potoku. Następnie utworzysz usługi połączone i zestawy danych, gdy będą potrzebne do skonfigurowania potoku.

1. Na stronie **Zaczynajmy** wybierz pozycję **Utwórz potok**.

   ![Tworzenie potoku](./media/doc-common-process/get-started-page.png)
1. Na karcie **Ogólne** dla potoku w polu **Nazwa** wprowadź wartość **CopyPipeline**.

1. W polu **narzędziowym Działania** rozwiń kategorię **Przenieś i Przekształć,** a następnie przeciągnij i upuść działanie **Kopiuj dane** z pola narzędzi na powierzchnię projektanta potoku. Wprowadź wartość **CopyFromBlobToSql** w polu **Nazwa**.

    ![Działanie kopiowania](./media/tutorial-copy-data-portal/drag-drop-copy-activity.png)

### <a name="configure-source"></a>Konfigurowanie źródła

>[!TIP]
>W tym samouczku używasz *klucza konta* jako typu uwierzytelniania dla magazynu danych źródłowych, ale w razie potrzeby można wybrać inne obsługiwane metody *uwierzytelniania: SAS URI*,*Service Principal* i *Managed Identity.* Szczegółowe informacje można znaleźć w odpowiednich sekcjach w [tym artykule.](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#linked-service-properties)
>Aby bezpiecznie przechowywać wpisy tajne dla magazynów danych, zaleca się również korzystanie z usługi Azure Key Vault. Szczegółowe ilustracje można znaleźć w [tym artykule.](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault)

1. Przejdź do karty **Źródło.** Wybierz **+ Nowy,** aby utworzyć źródłowy zestaw danych.

1. W oknie dialogowym **Nowy zestaw danych** wybierz pozycję Azure **Blob Storage**, a następnie wybierz pozycję **Kontynuuj**. Dane źródłowe znajdują się w magazynie obiektów blob, musisz więc wybrać usługę **Azure Blob Storage** dla źródłowego zestawu danych.

1. W oknie dialogowym **Wybieranie formatu** wybierz typ formatu danych, a następnie wybierz pozycję **Kontynuuj**.

1. W oknie dialogowym **Ustawianie właściwości** wprowadź **zestaw SourceBlobDataset** dla name. Zaznacz pole wyboru **Pierwszy wiersz jako nagłówek**. W polu **tekstowym Połączone usługi** wybierz pozycję **+ Nowy**.

1. W oknie dialogowym **Nowa usługa łączona (usługa Azure Blob Storage)** wprowadź jako nazwę **usługę AzureStorageLinkedService,** wybierz konto magazynu z listy **Nazwa konta magazynu.** Przetestuj połączenie, wybierz **pozycję Utwórz,** aby wdrożyć usługę połączeniową.

1. Po utworzeniu połączonej usługi jest ona nawigowana z powrotem do strony **Ustaw właściwości.** Wybierz przycisk **Przeglądaj** obok pozycji **Ścieżka pliku**.

1. Przejdź do folderu **adftutorial/input,** wybierz plik **emp.txt,** a następnie wybierz **przycisk OK**.

1. Kliknij przycisk **OK**. Automatycznie przechodzi do strony potoku. Na karcie **Źródło** upewnij się, że wybrano **zestaw SourceBlobDataset.** Aby wyświetlić podgląd danych na tej stronie, wybierz pozycję **Podgląd danych**.

    ![Zestaw danych źródłowych](./media/tutorial-copy-data-portal/source-dataset-selected.png)

### <a name="configure-sink"></a>Konfigurowanie ujścia
>[!TIP]
>W tym samouczku używasz *uwierzytelniania SQL* jako typu uwierzytelniania dla magazynu danych ujścia, ale można wybrać inne obsługiwane metody uwierzytelniania: *podmiotu zabezpieczeń usługi* i tożsamości *zarządzanej* w razie potrzeby. Szczegółowe informacje można znaleźć w odpowiednich sekcjach w [tym artykule.](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-database#linked-service-properties)
>Aby bezpiecznie przechowywać wpisy tajne dla magazynów danych, zaleca się również korzystanie z usługi Azure Key Vault. Szczegółowe ilustracje można znaleźć w [tym artykule.](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault)

1. Przejdź do karty **Ujście**, a następnie wybierz pozycję **+ Nowy**, aby utworzyć zestaw danych będący ujściem.

1. W oknie dialogowym **Nowy zestaw danych** wprowadź "SQL" w polu wyszukiwania w celu filtrowania łączników, wybierz pozycję Azure SQL **Database**, a następnie wybierz pozycję **Kontynuuj**. W tym samouczku skopiujesz dane do bazy danych SQL.

1. W oknie dialogowym **Ustawianie właściwości** wprowadź polecenie OutputSqlDataset for Name (Nazwa) **wprowadź zestaw OutputSqlDataset.** Z listy rozwijanej **Usługi połączone** wybierz + **Nowy**. Zestaw danych musi być skojarzony z połączoną usługą. Połączona usługa ma parametry połączenia, których usługa Data Factory używa do nawiązywania połączenia z usługą SQL Database w środowisku uruchomieniowym. Zestaw danych określa kontener, folder i plik (opcjonalnie), do którego dane są kopiowane.

1. W oknie dialogowym **Nowa usługa połączona (usługa Azure SQL Database)** należy wykonać następujące kroki:

    a. W obszarze **Nazwa** wprowadź wartość **AzureSqlDatabaseLinkedService**.

    b. W polu **Nazwa serwera** wybierz swoje wystąpienie programu SQL Server.

    d. W polu **Nazwa bazy danych** wybierz swoją usługę SQL Database.

    d. W polu **Nazwa użytkownika** wprowadź nazwę użytkownika.

    e. W polu **Hasło** wprowadź hasło użytkownika.

    f. Wybierz pozycję **Testuj połączenie**, aby przetestować połączenie.

    g. Wybierz **pozycję Utwórz,** aby wdrożyć usługę połączeniową.

    ![Zapisywanie nowej połączonej usługi](./media/tutorial-copy-data-portal/new-azure-sql-linked-service-window.png)

1. Automatycznie przechodzi do okna dialogowego **Ustawianie właściwości.** W obszarze **Tabela** wybierz pozycję **[dbo].[emp]**. Następnie wybierz przycisk **OK**.

1. Przejdź do karty z potokiem i upewnij się, że w obszarze **Zestaw danych będący ujściem** wybrano pozycję **OutputSqlDataset**.

    ![Karta potoku](./media/tutorial-copy-data-portal/pipeline-tab-2.png)       

Opcjonalnie można mapować schemat źródła na odpowiedni schemat docelowy, wykonując [mapowanie schematu w działaniu kopiowania](copy-activity-schema-and-type-mapping.md).

## <a name="validate-the-pipeline"></a>Weryfikowanie potoku
Wybierz na pasku narzędzi pozycję **Weryfikuj**, aby zweryfikować potok.

Możesz zobaczyć kod JSON skojarzone z potoku, klikając **kod** w prawym górnym rogu.

## <a name="debug-and-publish-the-pipeline"></a>Debugowanie i publikowanie potoku
Przed opublikowaniem artefaktów (połączone usługi, zestawy danych i potok) w usłudze Data Factory lub własnym repozytorium Git usługi Azure Repos możesz debugować potok.

1. Aby debugować potok, wybierz na pasku narzędzi pozycję **Debuguj**. Na karcie **Dane wyjściowe** w dolnej części okna wyświetlany jest stan uruchomienia potoku.

1. Gdy potok może działać pomyślnie, na górnym pasku narzędzi wybierz pozycję **Publikuj wszystkie**. Ta akcja powoduje opublikowanie utworzonych jednostek (zestawy danych i potok) w usłudze Data Factory.

1. Poczekaj na wyświetlenie komunikatu **Pomyślnie opublikowano**. Aby wyświetlić komunikaty powiadomień, kliknij pozycję **Pokaż powiadomienia** w prawym górnym rogu (przycisk dzwonka).

## <a name="trigger-the-pipeline-manually"></a>Ręczne wyzwalanie potoku
W tym kroku ręcznie wyzwolisz potok, który został opublikowany w poprzednim kroku.

1. Wybierz pozycję **Wyzwól** na pasku narzędzi, a następnie wybierz pozycję **Wyzwól teraz**. Na stronie **Przebieg potoku** wybierz pozycję **OK**.  

1. Przejdź do karty **Monitorowanie** po lewej stronie. Widoczne jest uruchomienie potoku, które zostało wyzwolone za pomocą wyzwalacza ręcznego. Za pomocą łączy w kolumnie **NAZWA POTOKU** można wyświetlić szczegóły działania i ponownie uruchomić potok.

    [![Monitorowanie uruchomień potoku](./media/tutorial-copy-data-portal/monitor-pipeline-inline-and-expended.png)](./media/tutorial-copy-data-portal/monitor-pipeline-inline-and-expended.png#lightbox)

1. Aby wyświetlić uruchomienia działania skojarzone z uruchomieniem potoku, wybierz łącze **CopyPipeline** w kolumnie **NAZWA POTOKU.** W tym przykładzie istnieje tylko jedno działanie, więc widzisz tylko jeden wpis na liście. Aby uzyskać szczegółowe informacje na temat operacji kopiowania, wybierz **łącze Szczegóły** (ikona okularów) w kolumnie **NAZWA DZIAŁANIA.** Wybierz **wszystkie uruchomień potoku** u góry, aby wrócić do widoku Przebiegi potoku. Aby odświeżyć widok, wybierz pozycję **Odśwież**.

    [![Monitorowanie uruchomień działania](./media/tutorial-copy-data-portal/view-activity-runs-inline-and-expended.png)](./media/tutorial-copy-data-portal/view-activity-runs-inline-and-expended.png#lightbox)

1. Sprawdź, czy dodano jeszcze dwa wiersze do tabeli **emp** w bazie danych SQL.

## <a name="trigger-the-pipeline-on-a-schedule"></a>Wyzwalanie potoku zgodnie z harmonogramem
W tym kroku utworzysz wyzwalacz harmonogramu potoku. Wyzwalacz uruchamia potok zgodnie z określonym harmonogramem, na przykład co godzinę lub codziennie. W tym miejscu można ustawić wyzwalacz, aby uruchamiał co minutę do określonego daty zakończenia.

1. Przejdź do karty **Tworzenie** po lewej stronie karty Monitorowanie.

1. Przejdź do potoku, kliknij pozycję **Wyzwalacz** na pasku narzędzi, a następnie wybierz pozycję **Nowy/Edytuj**.

1. W oknie dialogowym **Dodawanie wyzwalaczy** wybierz pozycję **+ Nowy** dla obszaru **wyzwalacza Wybierz.**

1. W oknie **Nowy wyzwalacz** wykonaj następujące czynności:

    a. W obszarze **Nazwa** wprowadź wartość **RunEveryMinute**.

    b. W obszarze **Koniec** wybierz pozycję **W dniu**.

    d. W obszarze **Dzień zakończenia** wybierz listę rozwijaną.

    d. Wybierz opcję **Bieżący dzień**. Domyślnie dzień zakończenia jest ustawiony na następny dzień.

    e. Zaktualizuj część **Czas zakończenia,** aby była o kilka minut po bieżącej datetime. Wyzwalacz zostanie aktywowany tylko w przypadku, gdy opublikujesz zmiany. Jeśli ustawisz go tylko na kilka minut od siebie, a nie opublikujesz go do tego czasu, nie zobaczysz uruchomienia wyzwalacza.

    f. Kliknij przycisk **OK**.

    g. W obszarze **Opcja Aktywowana** wybierz pozycję **Tak**.

    h. Kliknij przycisk **OK**.

    > [!IMPORTANT]
    > Za poszczególne uruchomienia potoku są naliczane opłaty, zatem określ stosowną datę zakończenia.

1. Na stronie **Wyzwalacz edytowania** przejrzyj ostrzeżenie, a następnie wybierz pozycję **Zapisz**. Potok w tym przykładzie nie przyjmuje żadnych parametrów.

1. Kliknij **pozycję Opublikuj wszystko,** aby opublikować zmianę.

1. Przejdź do karty **Monitorowanie** po lewej stronie, aby zobaczyć wyzwolone uruchomienia potoku.

    [![Wyzwolone uruchomienia potoku](./media/tutorial-copy-data-portal/triggered-pipeline-runs-inline-and-expended.png)](./media/tutorial-copy-data-portal/triggered-pipeline-runs-inline-and-expended.png#lightbox)

1. Aby przełączyć się z widoku **Przebiegi potoku** do widoku **Przebiegi wyzwalacza,** wybierz **wyzwalacz uruchamia** po lewej stronie okna.

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
