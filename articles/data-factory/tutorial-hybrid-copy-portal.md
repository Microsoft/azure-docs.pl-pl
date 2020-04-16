---
title: Kopiowanie danych z programu SQL Server do magazynu obiektów Blob przy użyciu witryny Azure portal
description: Dowiedz się, jak skopiować dane z lokalnego magazynu danych do chmury przy użyciu własnego środowiska Integration Runtime w usłudze Azure Data Factory.
services: data-factory
ms.author: abnarain
author: nabhishek
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 03/12/2020
ms.openlocfilehash: 7e222762c8bfa7218fd27e15b97de7b1166d749d
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418579"
---
# <a name="copy-data-from-an-on-premises-sql-server-database-to-azure-blob-storage"></a>Kopiowanie danych z lokalnej bazy danych programu SQL Server do usługi Azure Blob Storage

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

W tym samouczku użyjesz interfejsu użytkownika usługi Azure Data Factory, aby utworzyć potok usługi Data Factory, który kopiuje dane z lokalnej bazy danych programu SQL Server do usługi Azure Blob Storage. Utworzysz własne środowisko Integration Runtime (Self-hosted), służące do przenoszenia danych między lokalnym magazynem danych i magazynem danych w chmurze.

> [!NOTE]
> Ten artykuł nie zawiera szczegółowego wprowadzenia do usługi Data Factory. Aby uzyskać więcej informacji, zobacz [Wprowadzenie do usługi Azure Data Factory](introduction.md).

Ten samouczek obejmuje wykonanie następujących kroków:

> [!div class="checklist"]
> * Tworzenie fabryki danych.
> * Utwórz własne środowisko Integration Runtime.
> * Tworzenie połączonych zestawów programu SQL Server i usługi Azure Storage.
> * Tworzenie zestawów danych programu SQL Server i usługi Azure Blob.
> * Tworzenie potoku z działaniem kopiowania do przenoszenia danych.
> * Uruchom potok.
> * Monitorowanie uruchomienia potoku.

## <a name="prerequisites"></a>Wymagania wstępne
### <a name="azure-subscription"></a>Subskrypcja platformy Azure
Jeśli nie masz jeszcze subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/).

### <a name="azure-roles"></a>Role platformy Azure
Aby utworzyć wystąpienia fabryki danych, konto użytkownika używane do logowania się na platformie Azure musi mieć przypisaną rolę *współautora* lub *właściciela* lub musi być *administratorem* subskrypcji platformy Azure.

Aby wyświetlić swoje uprawnienia do subskrypcji, przejdź do witryny Azure Portal. W prawym górnym rogu wybierz swoją nazwę użytkownika, a następnie wybierz pozycję **Uprawnienia**. Jeśli masz dostęp do wielu subskrypcji, wybierz odpowiednią subskrypcję. Przykładowe instrukcje dotyczące sposobu dodawania użytkownika do roli można znaleźć w temacie [Manage access using RBAC and the Azure portal](../role-based-access-control/role-assignments-portal.md) (Zarządzanie dostępem przy użyciu kontroli dostępu na podstawie ról i witryny Azure Portal).

### <a name="sql-server-2014-2016-and-2017"></a>Program SQL Server 2014, 2016 oraz 2017
Podczas pracy z tym samouczkiem użyjesz lokalnej bazy danych programu SQL Server jako *źródłowego* magazynu danych. Potok usługi Data Factory tworzony w tym samouczku kopiuje dane z tej lokalnej bazy danych programu SQL Server (źródła) do usługi Blob Storage (ujścia). Następnie należy utworzyć tabelę o nazwie **emp** w bazie danych programu SQL Server i wstawić kilka przykładowych wpisów do tabeli.

1. Uruchom program SQL Server Management Studio. Jeśli program nie jest jeszcze zainstalowany na używanej maszynie, przejdź do strony [pobierania programu SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms).

1. Połącz się z wystąpieniem programu SQL Server przy użyciu swoich poświadczeń.

1. Utwórz przykładową bazę danych. W widoku drzewa kliknij prawym przyciskiem myszy pozycję **Bazy danych**, a następnie wybierz pozycję **Nowa baza danych**.
1. W oknie **Nowa baza danych** wprowadź nazwę bazy danych, a następnie wybierz przycisk **OK**.

1. Aby utworzyć tabelę **emp** i wstawić do niej przykładowe dane, uruchom następujący skrypt zapytania w bazie danych. W widoku drzewa kliknij prawym przyciskiem myszy utworzoną bazę danych, a następnie wybierz pozycję **Nowe zapytanie**.

   ```
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
W tym samouczku używasz konta usługi Azure Storage ogólnego przeznaczenia (konkretnie usługi Blob Storage) jako magazynu danych: docelowego/ujścia. Jeśli nie masz konta usługi Azure Storage ogólnego przeznaczenia, zobacz [Tworzenie konta magazynu](../storage/common/storage-account-create.md). Potok w fabryce danych tworzony w tym samouczku kopiuje dane z lokalnej bazy danych programu SQL Server (źródła) do tej usługi Blob Storage (ujścia). 

#### <a name="get-the-storage-account-name-and-account-key"></a>Pobieranie nazwy konta i klucza konta magazynu
W tym samouczku używasz nazwy i klucza swojego konta magazynu. Pobierz nazwę i klucz konta magazynu, wykonując następujące kroki:

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) przy użyciu nazwy użytkownika i hasła do konta platformy Azure.

1. W lewym okienku wybierz pozycję **Wszystkie usługi**. Zastosuj filtrowanie według słowa kluczowego **Magazyn**, a następnie wybierz pozycję **Konta magazynu**.

    ![Wyszukiwanie kont magazynu](media/doc-common-process/search-storage-account.png)

1. W razie potrzeby na liście kont magazynu przefiltruj konto magazynu. Następnie wybierz swoje konto magazynu.

1. W oknie **Konto magazynu** wybierz pozycję **Klucze dostępu**.

1. Skopiuj wartości z pól **Nazwa konta magazynu** i **klucz1** i wklej je do Notatnika lub innego edytora do późniejszego użycia z tym samouczkiem.

#### <a name="create-the-adftutorial-container"></a>Tworzenie kontenera adftutorial
W tej sekcji utworzysz kontener obiektów blob o nazwie **adftutorial** w usłudze Blob Storage.

1. W oknie **Konto magazynu** przejdź do **strony Przegląd**, a następnie wybierz pozycję **Kontenery**.

    ![Wybieranie opcji Obiekty blob](media/tutorial-hybrid-copy-powershell/select-blobs.png)

1. W oknie **Kontenery** wybierz **+ Kontener,** aby utworzyć nowy.

1. W oknie **Nowy kontener** w polu **Nazwa** wprowadź wartość **adftutorial**. Następnie wybierz pozycję **Utwórz**.

1. Na liście kontenerów wybierz **adftutorial,** który właśnie utworzyłeś.

1. Okno **kontenera** **należy** otworzyć. Można go użyć do sprawdzenia danych wyjściowych na końcu samouczka. Usługa Data Factory automatycznie tworzy folder wyjściowy w tym kontenerze, więc Ty nie musisz go tworzyć.

## <a name="create-a-data-factory"></a>Tworzenie fabryki danych
W tym kroku utworzysz fabrykę danych i uruchomisz interfejs użytkownika usługi Data Factory, aby utworzyć potok w fabryce danych.

1. Otwórz przeglądarkę internetową **Microsoft Edge** lub **Google Chrome**. Obecnie interfejs użytkownika usługi Data Factory jest obsługiwany tylko przez przeglądarki internetowe Microsoft Edge i Google Chrome.
1. W menu po lewej stronie wybierz pozycję **Utwórz fabrykę** > **danych****analizy** > zasobów:

   ![Wybór usługi Data Factory w okienku „Nowy”](./media/doc-common-process/new-azure-data-factory-menu.png)

1. Na stronie **Nowa fabryka danych** w polu **Nazwa** wprowadź wartość **ADFTutorialDataFactory**.

   Nazwa fabryki danych musi być *globalnie unikatowa*. Jeśli dla pola nazwy zobaczysz poniższy komunikat o błędzie, zmień nazwę fabryki danych (np. twojanazwaADFTutorialDataFactory). Reguły nazewnictwa dla artefaktów usługi Data Factory można znaleźć w artykule [Data Factory — reguły nazewnictwa](naming-rules.md).

   ![Nazwa nowej fabryki danych](./media/doc-common-process/name-not-available-error.png)

1. Wybierz **subskrypcję** platformy Azure, w której chcesz utworzyć fabrykę danych.
1. W obszarze **Grupa zasobów** wykonaj jedną z następujących czynności:

   - Wybierz pozycję **Użyj istniejącej**, a następnie wybierz istniejącą grupę zasobów z listy rozwijanej.

   - Wybierz **pozycję Utwórz nowy**i wprowadź nazwę grupy zasobów.
        
     Informacje na temat grup zasobów znajdują się w artykule [Using resource groups to manage your Azure resources (Używanie grup zasobów do zarządzania zasobami platformy Azure)](../azure-resource-manager/management/overview.md).
1. W obszarze **Wersja** wybierz pozycję **V2**.
1. W obszarze **Lokalizacja** wybierz lokalizację fabryki danych. Na liście rozwijanej są wyświetlane tylko obsługiwane lokalizacje. Magazyny danych (np. usługi Storage i SQL Database) oraz jednostki obliczeniowe (np. usługa Azure HDInsight) używane przez usługę Data Factory mogą mieścić się w innych regionach.
1. Wybierz pozycję **Utwórz**.

1. Po zakończeniu tworzenia zostanie wyświetlona strona **Fabryka danych,** jak pokazano na obrazie:

    ![Strona główna fabryki danych](./media/doc-common-process/data-factory-home-page.png)
1. Wybierz kafelek **Tworzenie i monitorowanie**, aby w osobnej karcie uruchomić interfejs użytkownika usługi Azure Data Factory.


## <a name="create-a-pipeline"></a>Tworzenie potoku

1. Na stronie **Zaczynajmy** wybierz pozycję **Utwórz potok**. Potok zostanie utworzony automatycznie. Potok będzie wyświetlony w widoku drzewa i zostanie otwarty jego edytor.

   ![Strona Zaczynajmy](./media/doc-common-process/get-started-page.png)

1. Na karcie **Ogólne** u dołu okna **Właściwości** w obszarze **Nazwa**wprowadź pozycję **SQLServerToBlobPipeline**.

1. W polu **narzędziowym Działania** rozwiń pozycję **Przenieś & przekształć**. Przeciągnij i upuść działanie **Kopiowanie** na powierzchnię projektowania potoku. Ustaw nazwę działania na wartość **CopySqlServerToAzureBlobActivity**.

1. W oknie **Właściwości** przejdź do karty **Źródło** i wybierz pozycję **+ Nowy**.

1. W oknie dialogowym **Nowy zestaw danych** wyszukaj program SQL **Server**. Wybierz **pozycję SQL Server**, a następnie wybierz pozycję **Kontynuuj**.
    ![Nowy zestaw danych programu SqlServer](./media/tutorial-hybrid-copy-portal/create-sqlserver-dataset.png)

1. W oknie dialogowym **Ustawianie właściwości** w obszarze **Nazwa**wprowadź pozycję **SqlServerDataset**. W obszarze **Połączona usługa**wybierz **+ Nowy**. W tym kroku tworzone jest połączenie z magazynem danych źródłowych (bazą danych programu SQL Server).

1. W oknie dialogowym **Nowa usługa połączona** dodaj **nazwę** jako **SqlServerLinkedService**. W obszarze **Połącz za pośrednictwem środowiska uruchomieniowego integracji**wybierz **+Nowy**.  W tej sekcji utworzysz własne środowisko Integration Runtime i skojarzysz je z maszyną lokalną za pomocą bazy danych programu SQL Server. Własne środowisko Integration Runtime jest składnikiem, który kopiuje dane z bazy danych programu SQL Server na Twojej maszynie do usługi Blob Storage.

1. W oknie dialogowym **Ustawienia środowiska wykonawczego integracji** wybierz pozycję **Hostowany samodzielnie,** a następnie wybierz pozycję **Kontynuuj**.

1. Pod nazwą wprowadź **TutorialIntegrationRuntime**. Następnie wybierz pozycję **Utwórz**.

1. W obszarze Ustawienia wybierz pozycję **Kliknij tutaj, aby uruchomić konfigurację ekspresową dla tego komputera**. Ta akcja instaluje na komputerze środowisko Integration Runtime i rejestruje je w usłudze Data Factory. Ewentualnie można użyć opcji instalacji ręcznej w celu pobrania pliku instalacyjnego, uruchomienia go i zarejestrowania środowiska Integration Runtime za pomocą klucza.
    ![Instalacja środowiska Integration Runtime](./media/tutorial-hybrid-copy-portal/intergration-runtime-setup.png)

1. W oknie **Ustawienia programu Integration Runtime (Self-hosted) Express (Self-hosted)** wybierz pozycję **Zamknij** po zakończeniu procesu.

    ![Instalacja ekspresowa środowiska Integration Runtime (Self-hosted)](./media/tutorial-hybrid-copy-portal/integration-runtime-setup-successful.png)

1. W oknie dialogowym **Nowa usługa połączona (SQL Server)** upewnij się, że w obszarze **Połącz za pośrednictwem środowiska wykonawczego integracji**wybrano **opcję TutorialIntegrationRuntime** . Następnie należy wykonać następujące czynności:

    a. W polu **Nazwa** wprowadź wartość **SqlServerLinkedService**.

    b. W polu **Nazwa serwera** wprowadź nazwę lokalnego wystąpienia programu SQL Server.

    d. W polu **Nazwa bazy danych** wprowadź nazwę bazy danych zawierającej tabelę **emp**.

    d. W polu **Typ uwierzytelniania** wybierz odpowiedni typ uwierzytelniania, którego usługa Data Factory powinna używać do nawiązania połączenia z bazą danych SQL Server.

    e. W polach **Nazwa użytkownika** i **Hasło** wprowadź nazwę użytkownika i hasło. Jeśli musisz użyć znaku ukośnika (\\) w nazwie konta użytkownika lub nazwie serwera, poprzedź go znakiem ucieczki (\\). Na przykład użyj *mydomain\\\\myuser*.

    f. Wybierz pozycję **Testuj połączenie**. Ten krok ma na celu potwierdzenie, że usługa Data Factory może łączyć się z bazą danych programu SQL Server przy użyciu utworzonego środowiska wykonawczego integracji hostowanego przez użytkownika.

    g. Aby zapisać usługę połączony, wybierz pozycję **Utwórz**.
 
    ![Nowa usługa połączona (SQL Server)](./media/tutorial-hybrid-copy-portal/new-sqlserver-linked-service.png)

1. Po utworzeniu połączonej usługi powrócisz do strony **Ustaw właściwości** zestawu SqlServerDataset. Wykonaj następujące kroki:

    a. Upewnij się, że dla ustawienia **Połączona usługa** jest wyświetlana wartość **SqlServerLinkedService**.

    b. Pod **nazwą tabeli**wybierz **[dbo].[ emp]**.
    
    d. Kliknij przycisk **OK**.

1. Przejdź do karty z elementem **SQLServerToBlobPipeline** lub wybierz pozycję **SQLServerToBlobPipeline** w widoku drzewa.

1. Przejdź do karty **Ujście** u dołu okna **Właściwości**, a następnie wybierz pozycję **+ Nowy**.

1. W oknie dialogowym **Nowy zestaw danych** wybierz pozycję Azure **Blob Storage**. Następnie wybierz pozycję **Kontynuuj**.

1. W oknie dialogowym **Wybieranie formatu** wybierz typ formatu danych. Następnie wybierz pozycję **Kontynuuj**.

    ![Wybór formatu danych](./media/doc-common-process/select-data-format.png)

1. W oknie dialogowym **Ustawianie właściwości** wprowadź pozycję AzureBlobDataset for Name.In the Set Properties dialog, enter **AzureBlobDataset** for Name. Kliknij pozycję **+ Nowy** obok pola tekstowego **Połączona usługa**.

1. W oknie dialogowym **Nowa usługa łączona (usługa Azure Blob Storage)** wprowadź jako nazwę **usługę AzureStorageLinkedService,** wybierz konto magazynu z listy Nazwa **konta magazynu.** Przetestuj połączenie, a następnie wybierz pozycję **Utwórz,** aby wdrożyć połączony serwis.

1. Po utworzeniu połączonej usługi powrócisz do strony **Ustaw właściwości.** Kliknij przycisk **OK**.

1. Otwórz zestaw danych ujścia. Na karcie **Połączenie** wykonaj następujące kroki:

    a. Upewnij się, że w polu **Połączona usługa** wybrano wartość **AzureStorageLinkedService**.

    b. W **polu Ścieżka pliku**wprowadź **adftutorial/fromonprem** dla części **Kontener/Katalog.** Jeśli folder wyjściowy nie istnieje w kontenerze adftutorial, usługa Data Factory automatycznie utworzy folder wyjściowy.

    d. W części **Plik** wybierz pozycję **Dodaj zawartość dynamiczną**.
    ![wyrażenie dynamiczne do rozpoznawania nazwy pliku](./media/tutorial-hybrid-copy-portal/file-name.png)

    d. Dodaj `@CONCAT(pipeline().RunId, '.txt')`, a następnie wybierz pozycję **Zakończ**. Ta akcja zmieni nazwę pliku za pomocą pliku PipelineRunID.txt.

1. Przejdź na kartę z otwartym potokiem lub wybierz potok w widoku drzewa. Upewnij się, że w polu **Zestaw danych będący ujściem** wybrano wartość **AzureBlobDataset**.

1. Aby zweryfikować poprawność ustawień potoku, wybierz pozycję **Weryfikuj** na pasku narzędzi dla potoku. Aby **zamknąć dane wyjściowe sprawdzania poprawności potoku,** wybierz ikonę. **>>**
    ![sprawdzanie poprawności potoku](./media/tutorial-hybrid-copy-portal/validate-pipeline.png)
    

1. Aby opublikować jednostki utworzone w układzie danych, wybierz pozycję **Publikuj wszystkie**.

1. Poczekaj, aż zostanie **wyświetlona okno podręczne Publishing zakończone.** Aby sprawdzić stan publikowania, wybierz **łącze Pokaż powiadomienia** w górnej części okna. Aby zamknąć okno powiadomienia, wybierz przycisk **X**.


## <a name="trigger-a-pipeline-run"></a>Wyzwalanie uruchomienia potoku
Wybierz **pozycję Dodaj wyzwalacz** na pasku narzędzi dla potoku, a następnie wybierz pozycję **Wyzwalaj teraz**.

## <a name="monitor-the-pipeline-run"></a>Monitorowanie działania potoku

1. Przejdź do karty **Monitor.** Zobaczysz potok, który został ręcznie wyzwolony w poprzednim kroku.

1. Aby wyświetlić przebiegi aktywności skojarzone z uruchomieniem potoku, wybierz łącze **SQLServerToBlobPipeline** w obszarze *NAZWA POTOKU*. 
    ![Monitorowanie uruchomień potoku](./media/tutorial-hybrid-copy-portal/pipeline-runs.png)

1. Na stronie **Przebiegi działania** wybierz łącze Szczegóły (obraz okularów), aby wyświetlić szczegółowe informacje o operacji kopiowania. Aby wrócić do widoku Przebiegi potoku, wybierz **opcję Wszystkie uruchomień potoku** u góry.

## <a name="verify-the-output"></a>Sprawdzanie danych wyjściowych
Potok automatycznie tworzy folder wyjściowy o nazwie *fromonprem* w kontenerze obiektów blob `adftutorial`. Upewnij się, że plik *[pipeline().RunId].txt* jest widoczny w folderze wyjściowym.


## <a name="next-steps"></a>Następne kroki
Potok w tym przykładzie kopiuje dane z jednej lokalizacji do innej lokalizacji w usłudze Blob Storage. W tym samouczku omówiono:

> [!div class="checklist"]
> * Tworzenie fabryki danych.
> * Utwórz własne środowisko Integration Runtime.
> * Tworzenie połączonych usług programu SQL Server i usługi Storage.
> * Tworzenie zestawów danych programu SQL Server i usługi Blob Storage.
> * Tworzenie potoku z działaniem kopiowania do przenoszenia danych.
> * Uruchom potok.
> * Monitorowanie uruchomienia potoku.

Lista magazynów danych obsługiwanych przez usługę Data Factory znajduje się w artykule [Obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).

Aby dowiedzieć się, jak zbiorczo kopiować dane z lokalizacji źródłowej do docelowej, przejdź do następującego samouczka:

> [!div class="nextstepaction"]
>[Zbiorcze kopiowanie danych](tutorial-bulk-copy-portal.md)
