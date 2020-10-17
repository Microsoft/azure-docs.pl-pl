---
title: Kopiowanie danych lokalnych za pomocą narzędzia Kopiowanie danych platformy Azure
description: Utwórz fabrykę danych platformy Azure, a następnie użyj narzędzia Kopiowanie danych, aby skopiować dane z bazy danych SQL Server do usługi Azure Blob Storage.
services: data-factory
ms.author: abnarain
author: nabhishek
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.custom: seo-lt-2019
ms.date: 06/09/2020
ms.openlocfilehash: b9bf03110fcb4f0c034c70d5e77e558b949a7825
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2020
ms.locfileid: "92148006"
---
# <a name="copy-data-from-a-sql-server-database-to-azure-blob-storage-by-using-the-copy-data-tool"></a>Kopiowanie danych z bazy danych SQL Server do usługi Azure Blob Storage za pomocą narzędzia Kopiowanie danych
> [!div class="op_single_selector" title1="Wybierz używaną wersję usługi Data Factory:"]
> * [Wersja 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Bieżąca wersja](tutorial-hybrid-copy-data-tool.md)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

W tym samouczku utworzysz fabrykę danych za pomocą witryny Azure Portal. Następnie użyj narzędzia Kopiowanie danych, aby utworzyć potok, który kopiuje dane z bazy danych SQL Server do usługi Azure Blob Storage.

> [!NOTE]
> - Jeśli jesteś nowym użytkownikiem usługi Azure Data Factory, zobacz [Wprowadzenie do usługi Data Factory](introduction.md).

Ten samouczek obejmuje wykonanie następujących kroków:

> [!div class="checklist"]
> * Tworzenie fabryki danych.
> * Tworzenie potoku za pomocą narzędzia do kopiowania danych.
> * Monitorowanie uruchomień potoku i działań.

## <a name="prerequisites"></a>Wymagania wstępne
### <a name="azure-subscription"></a>Subskrypcja platformy Azure
Jeśli nie masz jeszcze subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/).

### <a name="azure-roles"></a>Role platformy Azure
Aby utworzyć wystąpienia usługi Data Factory, konto użytkownika używane do logowania się na platformie Azure musi mieć przypisaną rolę *współautora* lub *właściciela* albo być *administratorem* subskrypcji platformy Azure.

Aby wyświetlić swoje uprawnienia do subskrypcji, przejdź do witryny Azure Portal. W prawym górnym rogu wybierz swoją nazwę użytkownika, a następnie wybierz pozycję **Uprawnienia**. Jeśli masz dostęp do wielu subskrypcji, wybierz odpowiednią subskrypcję. Aby uzyskać przykładowe instrukcje dotyczące dodawania użytkownika do roli, zobacz [Dodawanie lub usuwanie przypisań ról platformy Azure przy użyciu Azure Portal](../role-based-access-control/role-assignments-portal.md).

### <a name="sql-server-2014-2016-and-2017"></a>Program SQL Server 2014, 2016 oraz 2017
W tym samouczku użyjesz SQL Server bazy danych jako *źródłowego* magazynu danych. Potok w fabryce danych tworzony w tym samouczku kopiuje dane z tej bazy danych SQL Server (Źródło) do usługi BLOB Storage (ujścia). Następnie utworzysz tabelę o nazwie **EMP** w bazie danych SQL Server i wstawisz kilka przykładowych wpisów do tabeli.

1. Uruchom program SQL Server Management Studio. Jeśli program nie jest jeszcze zainstalowany na używanej maszynie, przejdź do strony [pobierania programu SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms).

1. Połącz się z wystąpieniem programu SQL Server przy użyciu swoich poświadczeń.

1. Utwórz przykładową bazę danych. W widoku drzewa kliknij prawym przyciskiem myszy pozycję **Bazy danych**, a następnie wybierz pozycję **Nowa baza danych**.

1. W oknie **Nowa baza danych** wprowadź nazwę bazy danych, a następnie wybierz przycisk **OK**.

1. Aby utworzyć tabelę **emp** i wstawić do niej przykładowe dane, uruchom następujący skrypt zapytania w bazie danych. W widoku drzewa kliknij prawym przyciskiem myszy utworzoną bazę danych, a następnie wybierz pozycję **Nowe zapytanie**.

    ```sql
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50)
    )
    GO

    INSERT INTO emp (FirstName, LastName) VALUES ('John', 'Doe')
    INSERT INTO emp (FirstName, LastName) VALUES ('Jane', 'Doe')
    GO
    ```

### <a name="azure-storage-account"></a>Konto magazynu Azure
W tym samouczku używasz konta usługi Azure Storage ogólnego przeznaczenia (konkretnie usługi Blob Storage) jako magazynu danych: docelowego/ujścia. Jeśli nie masz konta magazynu ogólnego przeznaczenia, zobacz [Tworzenie konta magazynu](../storage/common/storage-account-create.md), aby dowiedzieć się, jak je utworzyć. Potok w fabryce danych tworzony w tym samouczku kopiuje dane z bazy danych SQL Server (Źródło) do tego magazynu obiektów BLOB (ujścia). 

#### <a name="get-the-storage-account-name-and-account-key"></a>Pobieranie nazwy konta i klucza konta magazynu
W tym samouczku używasz nazwy i klucza swojego konta magazynu. Pobierz nazwę i klucz konta magazynu, wykonując następujące kroki:

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) przy użyciu nazwy użytkownika i hasła do konta platformy Azure.

1. W lewym okienku wybierz pozycję **Wszystkie usługi**. Zastosuj filtrowanie według słowa kluczowego **Magazyn**, a następnie wybierz pozycję **Konta magazynu**.

    ![Wyszukiwanie kont magazynu](media/doc-common-process/search-storage-account.png)

1. Na liście kont magazynu odfiltruj swoje konto magazynu, jeśli to konieczne. Następnie wybierz swoje konto magazynu.

1. W oknie **Konto magazynu** wybierz pozycję **Klucze dostępu**.


1. Skopiuj wartości z pól **Nazwa konta magazynu** i **klucz1** i wklej je do Notatnika lub innego edytora do późniejszego użycia z tym samouczkiem.

#### <a name="create-the-adftutorial-container"></a>Tworzenie kontenera adftutorial
W tej sekcji utworzysz kontener obiektów blob o nazwie **adftutorial** w usłudze Blob Storage.

1. W oknie **Konto magazynu** przełącz się do widoku **Przegląd**, a następnie wybierz pozycję **Obiekty blob**.

1. W oknie **obiekty blob** wybierz pozycję **+ kontener**.

1. W oknie **nowy kontener** w obszarze **Nazwa**wpisz **adftutorial**, a następnie wybierz przycisk **OK**.

1. Kliknij pozycję **adftutorial** na liście kontenerów.


1. Pozostaw otwarte okno **kontenera** dla **adftutorial**. Służy do zweryfikowania danych wyjściowych na końcu samouczka. Usługa Data Factory automatycznie tworzy folder wyjściowy w tym kontenerze, więc Ty nie musisz go tworzyć.


## <a name="create-a-data-factory"></a>Tworzenie fabryki danych

1. W menu po lewej stronie wybierz pozycję **+ Utwórz**  >  **Analytics**  >  **Data Factory**analizy zasobów.

   ![Tworzenie nowej fabryki danych](./media/doc-common-process/new-azure-data-factory-menu.png)

1. Na stronie **Nowa fabryka danych** w polu **Nazwa** wprowadź wartość **ADFTutorialDataFactory**.

   Nazwa fabryki danych musi być *globalnie unikatowa*. Jeśli dla pola nazwy zobaczysz poniższy komunikat o błędzie, zmień nazwę fabryki danych (np. twojanazwaADFTutorialDataFactory). Reguły nazewnictwa dla artefaktów usługi Data Factory można znaleźć w artykule [Data Factory — reguły nazewnictwa](naming-rules.md).

   ![Nazwa nowej fabryki danych](./media/doc-common-process/name-not-available-error.png)
1. Wybierz **subskrypcję** platformy Azure, w której chcesz utworzyć fabrykę danych.
1. W obszarze **Grupa zasobów** wykonaj jedną z następujących czynności:

   - Wybierz pozycję **Użyj istniejącej**, a następnie wybierz istniejącą grupę zasobów z listy rozwijanej.

   - Wybierz pozycję **Utwórz nową**, a następnie wprowadź nazwę grupy zasobów. 
        
     Informacje na temat grup zasobów znajdują się w artykule [Using resource groups to manage your Azure resources (Używanie grup zasobów do zarządzania zasobami platformy Azure)](../azure-resource-manager/management/overview.md).
1. W obszarze **Wersja** wybierz pozycję **V2**.
1. W obszarze **Lokalizacja** wybierz lokalizację fabryki danych. Na liście rozwijanej są wyświetlane tylko obsługiwane lokalizacje. Magazyny danych (np. usługi Azure Storage i SQL Database) oraz jednostki obliczeniowe (np. usługa Azure HDInsight) używane przez usługę Data Factory mogą mieścić się w innych lokalizacjach/regionach.
1. Wybierz pozycję **Utwórz**.

1. Po zakończeniu tworzenia zostanie wyświetlona strona **Data Factory** , jak pokazano na ilustracji.

     ![Strona główna fabryki danych](./media/doc-common-process/data-factory-home-page.png)
1. Wybierz pozycję **Tworzenie i monitorowanie**, aby na osobnej karcie uruchomić interfejs użytkownika usługi Data Factory.

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Tworzenie potoku za pomocą narzędzia do kopiowania danych

1. Na stronie **Wprowadzenie** wybierz pozycję **Kopiowanie danych**, aby uruchomić narzędzie do kopiowania danych.

   ![Strona Wprowadzenie](./media/doc-common-process/get-started-page.png)

1. Na stronie **Właściwości** narzędzia do kopiowania danych wprowadź w polu **Nazwa zadania** wartość **CopyFromOnPremSqlToAzureBlobPipeline**. Następnie wybierz przycisk **Dalej**. Narzędzie do kopiowania danych tworzy potok o nazwie określonej w tym polu.
  ![Nazwa zadania](./media/tutorial-hybrid-copy-data-tool/properties-page.png)

1. Na stronie **Źródłowy magazyn danych** kliknij pozycję **Utwórz nowe połączenie**.

1. W obszarze **Nowa połączona usługa**wyszukaj pozycję **SQL Server**, a następnie wybierz pozycję **Kontynuuj**.

1. W oknie dialogowym **Nowa połączona usługa (SQL Server)** w polu **Nazwa**wprowadź **SqlServerLinkedService**. Wybierz pozycję **+Nowy** w polu **Połącz za pośrednictwem środowiska Integration Runtime**. Należy utworzyć środowisko Integration Runtime (Self-hosted), pobrać je na komputer i zarejestrować w usłudze Data Factory. Środowisko Integration Runtime (Self-hosted) kopiuje dane między środowiskiem lokalnym a chmurą.

1. W oknie dialogowym **konfiguracja Integration Runtime** wybierz pozycję **samodzielny**. Następnie wybierz pozycję **Kontynuuj**.

   ![Tworzenie środowiska Integration Runtime](./media/tutorial-hybrid-copy-data-tool/create-self-hosted-integration-runtime.png)

1. W oknie dialogowym **konfiguracja Integration Runtime** w polu **Nazwa**wprowadź **TutorialIntegrationRuntime**. Następnie wybierz przycisk **Utwórz**.

1. W oknie dialogowym **konfiguracja Integration Runtime** wybierz **pozycję kliknij tutaj, aby uruchomić instalację ekspresową dla tego komputera**. Ta akcja instaluje na komputerze środowisko Integration Runtime i rejestruje je w usłudze Data Factory. Ewentualnie można użyć opcji instalacji ręcznej w celu pobrania pliku instalacyjnego, uruchomienia go i zarejestrowania środowiska Integration Runtime za pomocą klucza.

1. Uruchom pobraną aplikację. W oknie zostanie wyświetlony stan instalacji ekspresowej.

    ![Stan instalacji ekspresowej](./media/tutorial-hybrid-copy-data-tool/express-setup-status.png)

1. W oknie dialogowym **Nowa połączona usługa (SQL Server)** upewnij się, że dla pola Integration Runtime została wybrana opcja **TutorialIntegrationRuntime** . Następnie wykonaj następujące czynności:

    a. W polu **Nazwa** wprowadź wartość **SqlServerLinkedService**.

    b. W polu **Nazwa serwera** wprowadź nazwę lokalnego wystąpienia programu SQL Server.

    c. Wprowadź nazwę swojej lokalnej bazy danych w polu **Nazwa bazy danych**.

    d. Wybierz odpowiedni typ uwierzytelniania w polu **Typ uwierzytelniania**.

    e. W obszarze **Nazwa użytkownika**wprowadź nazwę użytkownika mającego dostęp do SQL Server.

    f. Wprowadź **hasło** dla użytkownika.

    przykład Test connection i wybierz pozycję **Zakończ**.

      ![Wybrane środowisko Integration Runtime](./media/tutorial-hybrid-copy-data-tool/integration-runtime-selected.png)

1. Na stronie **Magazyn danych źródłowych** wybierz pozycję **dalej**.

1. Na stronie **Wybieranie tabel, z których mają być kopiowane dane, lub używanie zapytania niestandardowego** wybierz z listy tabelę **[dbo].[emp]**, a następnie wybierz pozycję **Dalej**. Możesz wybrać dowolną inną tabelę, odpowiednio do bazy danych.

1. Na stronie **Docelowy magazyn danych** wybierz pozycję **Utwórz nowe połączenie**.


1. W obszarze **Nowa połączona usługa**Wyszukaj i wybierz pozycję **obiekt blob platformy Azure**, a następnie wybierz pozycję **Kontynuuj**.

   ![Wybór usługi Blob Storage](./media/tutorial-hybrid-copy-data-tool/select-destination-data-store.png)

1. W oknie dialogowym **Nowa połączona usługa (Azure Blob Storage)** wykonaj następujące kroki:

   a. W polu **Nazwa**wprowadź **AzureStorageLinkedService**.

   b. W obszarze **Połącz za pośrednictwem środowiska Integration Runtime** wybierz pozycję **TutorialIntegrationRuntime**.

   c. W polu **Nazwa konta magazynu** wybierz z listy rozwijanej swoje konto magazynu.

   d. Wybierz pozycję **Zakończ**.

1. Upewnij się, że w oknie dialogowym **docelowy magazyn danych** wybrano opcję **Azure Blob Storage** . Następnie wybierz przycisk **Dalej**.

1. W oknie dialogowym **Wybieranie wyjściowego pliku lub folderu** w obszarze **Ścieżka folderu** wprowadź wartość **adftutorial/fromonprem**. Jako jedno z wymagań wstępnych został utworzony kontener **adftutorial**. Jeśli folder wyjściowy (w tym przykładzie **fromonprem**) nie istnieje, usługa Data Factory utworzy go automatycznie. Możesz również użyć przycisku **Przeglądaj** , aby przeglądać magazyn obiektów blob i jego kontenerów/folderów. Jeśli nie wprowadzisz żadnej wartości w polu **Nazwa pliku**, domyślnie zostanie użyta nazwa źródła (w tym przykładzie **dbo.emp**).

   ![Wybieranie pliku lub folderu wyjściowego](./media/tutorial-hybrid-copy-data-tool/choose-output-file-folder.png)

1. W oknie dialogowym **Ustawienia formatu pliku** wybierz przycisk **Dalej**.

1. W oknie dialogowym **Ustawienia** wybierz przycisk **Dalej**.

1. W oknie dialogowym **Podsumowanie** sprawdź wartości wszystkich ustawień, a następnie wybierz pozycję **Dalej**.

1. Na **stronie Wdrażanie** wybierz pozycję **Monitorowanie**, aby monitorować potok (zadanie). 

1. Po zakończeniu przebiegu potoku można wyświetlić stan utworzonego potoku. 

1. Na stronie uruchomienia potoku wybierz pozycję **Odśwież** , aby odświeżyć listę. Kliknij link pod **nazwą potoku** , aby wyświetlić szczegóły uruchomienia działania lub ponownie uruchomić potok. 

1. Na stronie uruchomienia działania wybierz link **szczegóły** (ikona okularów) w kolumnie **Nazwa działania** , aby uzyskać więcej informacji na temat operacji kopiowania. Aby wrócić do widoku uruchomień potoków, wybierz link **wszystkie uruchomienia potoków** w menu stron nadrzędnych. Aby odświeżyć widok, wybierz pozycję **Odśwież**.

1. Upewnij się, że w folderze **fromonprem** kontenera **adftutorial** znajduje się plik wyjściowy.

1. Wybierz kartę **Edycja** po lewej stronie, aby przełączyć się w tryb edytora. Za pomocą edytora można zaktualizować usługi połączone, zestawy danych i potoki utworzone przez narzędzie. Wybierz pozycję **Kod**, aby wyświetlić kod JSON skojarzony z jednostką otwartą w edytorze. Aby uzyskać szczegółowe informacje dotyczące sposobu edytowania tych jednostek w interfejsie użytkownika usługi Data Factory, zobacz [wersję witryny Azure Portal używaną w tym samouczku](tutorial-copy-data-portal.md).


## <a name="next-steps"></a>Następne kroki
Potok w tym przykładzie kopiuje dane z bazy danych SQL Server do magazynu obiektów BLOB. W tym samouczku omówiono:

> [!div class="checklist"]
> * Tworzenie fabryki danych.
> * Tworzenie potoku za pomocą narzędzia do kopiowania danych.
> * Monitorowanie uruchomień potoku i działań.

Lista magazynów danych obsługiwanych przez usługę Data Factory znajduje się w artykule [Obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).

Aby dowiedzieć się, jak zbiorczo kopiować dane z lokalizacji źródłowej do docelowej, przejdź do następującego samouczka:

> [!div class="nextstepaction"]
>[Zbiorcze kopiowanie danych](tutorial-bulk-copy-portal.md)
