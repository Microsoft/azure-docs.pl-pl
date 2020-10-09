---
title: Kopiowanie danych z SQL Server do magazynu obiektów BLOB przy użyciu programu PowerShell
description: Dowiedz się, jak skopiować dane z lokalnego magazynu danych do chmury platformy Azure przy użyciu własnego środowiska Integration Runtime w usłudze Azure Data Factory.
services: data-factory
author: nabhishek
ms.author: abnarain
manager: anandsub
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.custom: seo-lt-2019; seo-dt-2019, devx-track-azurepowershell
ms.date: 01/22/2018
ms.openlocfilehash: d148bc81e81c4afa32682fa1462ecfbf5427f164
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89079236"
---
# <a name="tutorial-copy-data-from-a-sql-server-database-to-azure-blob-storage"></a>Samouczek: kopiowanie danych z bazy danych SQL Server do usługi Azure Blob Storage

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

W tym samouczku użyto Azure PowerShell do utworzenia potoku danych, który kopiuje dane z bazy danych SQL Server do usługi Azure Blob Storage. Utworzysz własne środowisko Integration Runtime (Self-hosted), służące do przenoszenia danych między lokalnym magazynem danych i magazynem danych w chmurze.

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
Aby można było tworzyć wystąpienia usługi Data Factory, konto użytkownika używane do logowania się na platformie Azure musi mieć przypisaną rolę *współautora* lub *właściciela* albo być *administratorem* subskrypcji platformy Azure.

W witrynie Azure Portal wybierz swoją nazwę użytkownika w prawym górnym rogu i wybierz pozycję **Uprawnienia**, aby wyświetlić uprawnienia, które masz w subskrypcji. Jeśli masz dostęp do wielu subskrypcji, wybierz odpowiednią subskrypcję. Przykładowe instrukcje dotyczące sposobu dodawania użytkownika do roli można znaleźć w artykule [Manage access using RBAC and the Azure portal](../role-based-access-control/role-assignments-portal.md) (Zarządzanie dostępem przy użyciu kontroli dostępu na podstawie ról i witryny Azure Portal).

### <a name="sql-server-2014-2016-and-2017"></a>Program SQL Server 2014, 2016 oraz 2017
W tym samouczku użyjesz SQL Server bazy danych jako *źródłowego* magazynu danych. Potok w fabryce danych tworzony w tym samouczku kopiuje dane z tej bazy danych SQL Server (Źródło) do usługi Azure Blob Storage (ujścia). Następnie utworzysz tabelę o nazwie **emp** w bazie danych programu SQL Server i wstawisz kilka przykładowych wpisów w tabeli.

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


### <a name="azure-storage-account"></a>Konto usługi Azure Storage
W tym samouczku używasz konta usługi Azure Storage ogólnego przeznaczenia (a dokładniej usługi Azure Blob Storage) jako docelowego magazynu danych (ujścia). Jeśli nie masz konta usługi Azure Storage ogólnego przeznaczenia, zobacz [Tworzenie konta magazynu](../storage/common/storage-account-create.md). Potok w fabryce danych tworzony w tym samouczku kopiuje dane z bazy danych SQL Server (Źródło) do tej usługi Azure Blob Storage (ujścia). 

#### <a name="get-storage-account-name-and-account-key"></a>Pobieranie nazwy konta i klucza konta magazynu
W tym samouczku używasz nazwy i klucza swojego konta usługi Azure Storage. Pobierz nazwę i klucz konta usługi Storage, wykonując następujące czynności:

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) przy użyciu nazwy użytkownika i hasła do konta platformy Azure.

1. W okienku po lewej stronie wybierz pozycję **Więcej usług**, zastosuj filtrowanie według słowa kluczowego **magazyn**, a następnie wybierz pozycję **Konta magazynu**.

    ![Wyszukiwanie konta magazynu](media/doc-common-process/search-storage-account.png)

1. Na liście kont magazynu znajdź swoje konto magazynu, w razie potrzeby używając filtrowania, a następnie wybierz to konto.

1. W oknie **Konto magazynu** wybierz pozycję **Klucze dostępu**.

1. Skopiuj wartości z pól **Nazwa konta magazynu** i **klucz1** i wklej je do Notatnika lub innego edytora do późniejszego użycia z tym samouczkiem.

#### <a name="create-the-adftutorial-container"></a>Tworzenie kontenera adftutorial
W tej sekcji utworzysz kontener obiektów BLOB o nazwie **adftutorial** w usłudze Azure Blob Storage.

1. W oknie **Konto magazynu** przełącz się do widoku **Przegląd**, a następnie wybierz pozycję **Obiekty blob**.

    ![Wybieranie opcji Obiekty blob](media/tutorial-hybrid-copy-powershell/select-blobs.png)

1. W oknie **Blob Service** wybierz pozycję **Kontener**.

1. W oknie **Nowy kontener** w polu **Nazwa** wpisz nazwę **adftutorial**, a następnie wybierz pozycję **OK**.

    ![Wprowadzanie nazwy kontenera](media/tutorial-hybrid-copy-powershell/new-container-dialog.png)

1. Kliknij pozycję **adftutorial** na liście kontenerów.  

1. Pozostaw otwarte okno **kontenera** dla **adftutorial** . Będzie ona używana do weryfikacji danych wyjściowych na końcu tego samouczka. Usługa Data Factory automatycznie tworzy folder wyjściowy w tym kontenerze, więc Ty nie musisz go tworzyć.


### <a name="windows-powershell"></a>Windows PowerShell

#### <a name="install-azure-powershell"></a>Instalowanie programu Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Zainstaluj najnowszą wersję programu Azure PowerShell, jeśli nie masz jej jeszcze na swojej maszynie. Aby uzyskać szczegółowe informacje, zobacz [How to install and configure Azure PowerShell (Jak zainstalować i skonfigurować program Azure PowerShell)](/powershell/azure/install-Az-ps).

#### <a name="log-in-to-powershell"></a>Logowanie do programu PowerShell

1. Uruchom program PowerShell na maszynie i pozostaw ten program otwarty aż do zakończenia pracy z tym samouczkiem Szybki start. Jeśli go zamkniesz i otworzysz ponownie, musisz uruchomić te polecenia jeszcze raz.

1. Uruchom poniższe polecenie, a następnie wprowadź nazwę użytkownika platformy Azure oraz hasło, których używasz do logowania się w witrynie Azure Portal:

    ```powershell
    Connect-AzAccount
    ```        

1. Jeśli masz wiele subskrypcji platformy Azure, uruchom poniższe polecenie, aby wybrać subskrypcję, z którą chcesz pracować. Zastąp **Identyfikator subskrypcji ID** subskrypcji platformy Azure:

    ```powershell
    Select-AzSubscription -SubscriptionId "<SubscriptionId>"    
    ```

## <a name="create-a-data-factory"></a>Tworzenie fabryki danych

1. Zdefiniuj zmienną nazwy grupy zasobów, której użyjesz później w poleceniach programu PowerShell. Skopiuj poniższe polecenie do programu PowerShell, podaj nazwę [grupy zasobów platformy Azure](../azure-resource-manager/management/overview.md) w podwójnych cudzysłowach (na przykład `"adfrg"`), a następnie uruchom polecenie. 
   
    ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup"
    ```

1. Aby utworzyć grupę zasobów platformy Azure, uruchom następujące polecenie:

    ```powershell
    New-AzResourceGroup $resourceGroupName -location 'East US'
    ```

    Jeśli grupa zasobów już istnieje, możesz zrezygnować z jej zastąpienia. Przypisz inną wartość do zmiennej `$resourceGroupName` i ponownie uruchom polecenie.

1. Zdefiniuj zmienną nazwy fabryki danych, której możesz użyć później w poleceniach programu PowerShell. Nazwa musi zaczynać się od litery lub cyfry i może zawierać tylko litery, cyfry i znak łącznika (-).

    > [!IMPORTANT]
    >  Zaktualizuj nazwę fabryki danych, nadając jej globalnie unikatową nazwę. Na przykład: ADFTutorialFactorySP1127.

    ```powershell
    $dataFactoryName = "ADFTutorialFactory"
    ```

1. Zdefiniuj zmienną lokalizacji fabryki danych:

    ```powershell
    $location = "East US"
    ```  

1. Aby utworzyć fabrykę danych, uruchom następujące polecenie cmdlet `Set-AzDataFactoryV2`:

    ```powershell       
    Set-AzDataFactoryV2 -ResourceGroupName $resourceGroupName -Location $location -Name $dataFactoryName
    ```

> [!NOTE]
>
> * Nazwa fabryki danych musi być globalnie unikatowa. Jeśli zostanie wyświetlony następujący błąd, zmień nazwę i spróbuj ponownie.
>    ```
>    The specified data factory name 'ADFv2TutorialDataFactory' is already in use. Data factory names must be globally unique.
>    ```
> * Aby utworzyć wystąpienia usługi Data Factory, konto użytkownika używane do logowania się na platformie Azure musi mieć przypisaną rolę *współautora* lub *właściciela* albo być *administratorem* subskrypcji platformy Azure.
> * Aby uzyskać listę regionów platformy Azure, w których obecnie jest dostępna usługa Data Factory, wybierz dane regiony na poniższej stronie, a następnie rozwiń węzeł **Analiza**, aby zlokalizować pozycję **Data Factory**: [Produkty dostępne według regionu](https://azure.microsoft.com/global-infrastructure/services/). Magazyny danych (Azure Storage, Azure SQL Database itd.) i jednostki obliczeniowe (Azure HDInsight itd.) używane przez fabrykę danych mogą mieścić się w innych regionach.
>
>

## <a name="create-a-self-hosted-integration-runtime"></a>Tworzenie własnego środowiska Integration Runtime

W tej sekcji utworzysz własne środowisko Integration Runtime i skojarzysz je z maszyną lokalną za pomocą bazy danych programu SQL Server. Własne środowisko Integration Runtime jest składnikiem, który kopiuje dane z bazy danych programu SQL Server na Twojej maszynie do usługi Azure Blob Storage.

1. Utwórz zmienną dla nazwy środowiska Integration Runtime. Użyj unikatowej nazwy i zanotuj ją. Będziesz jej używać w dalszej części tego samouczka.

    ```powershell
   $integrationRuntimeName = "ADFTutorialIR"
    ```

1. Utwórz własne środowisko Integration Runtime.

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $integrationRuntimeName -Type SelfHosted -Description "selfhosted IR description"
    ```

    Oto przykładowe dane wyjściowe:

    ```json
    Name              : ADFTutorialIR
    Type              : SelfHosted
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Description       : selfhosted IR description
    Id                : /subscriptions/<subscription ID>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<dataFactoryName>/integrationruntimes/<integrationRuntimeName>
    ```

1. Uruchom następujące polecenie, aby pobrać stan utworzonego środowiska Integration Runtime:

    ```powershell
   Get-AzDataFactoryV2IntegrationRuntime -name $integrationRuntimeName -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Status
    ```

    Oto przykładowe dane wyjściowe:

    ```json
    State                     : NeedRegistration
    Version                   :
    CreateTime                : 9/10/2019 3:24:09 AM
    AutoUpdate                : On
    ScheduledUpdateDate       :
    UpdateDelayOffset         :
    LocalTimeZoneOffset       :
    InternalChannelEncryption :
    Capabilities              : {}
    ServiceUrls               : {eu.frontend.clouddatahub.net}
    Nodes                     : {}
    Links                     : {}
    Name                      : <Integration Runtime name>
    Type                      : SelfHosted
    ResourceGroupName         : <resourceGroup name>
    DataFactoryName           : <dataFactory name>
    Description               : selfhosted IR description
    Id                        : /subscriptions/<subscription ID>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<dataFactoryName>/integrationruntimes/<integrationRuntimeName>
    ```

1. Uruchom następujące polecenie, aby pobrać *klucze uwierzytelniania* w celu zarejestrowania własnego środowiska Integration Runtime za pomocą usługi Data Factory w chmurze. Skopiuj jeden z kluczy (pomijając cudzysłowy) na potrzeby rejestracji własnego środowiska Integration Runtime, które zainstalujesz na swojej maszynie w następnym kroku.

    ```powershell
    Get-AzDataFactoryV2IntegrationRuntimeKey -Name $integrationRuntimeName -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName | ConvertTo-Json
    ```

    Oto przykładowe dane wyjściowe:

    ```json
    {
        "AuthKey1":  "IR@0000000000-0000-0000-0000-000000000000@xy0@xy@xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx=",
        "AuthKey2":  "IR@0000000000-0000-0000-0000-000000000000@xy0@xy@yyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyy="
    }
    ```

## <a name="install-the-integration-runtime"></a>Instalowanie środowiska Integration Runtime
1. Pobierz [środowisko Integration Runtime usługi Azure Data Factory](https://www.microsoft.com/download/details.aspx?id=39717) na lokalną maszynę z systemem Windows, a następnie uruchom instalację.

1. Na **stronie powitalnej Kreatora instalacji środowiska Microsoft Integration Runtime** wybierz przycisk **Dalej**.  

1. W oknie **Umowa Licencyjna Użytkownika Oprogramowania** zaakceptuj warunki i umowę licencyjną, a następnie wybierz przycisk **Dalej**.

1. W oknie **Folder docelowy** wybierz przycisk **Dalej**.

1. W oknie **Gotowe do zainstalowania środowiska Microsoft Integration Runtime** wybierz pozycję **Zainstaluj**.

1. W oknie **zakończenia pracy z Kreatorem instalacji środowiska Microsoft Integration Runtime** wybierz pozycję **Zakończ**.

1. W oknie **Rejestrowanie środowiska Integration Runtime (Self-hosted)** wklej klucz zapisany w poprzedniej sekcji i wybierz pozycję **Zarejestruj**.

    ![Rejestrowanie środowiska Integration Runtime](media/tutorial-hybrid-copy-powershell/register-integration-runtime.png)

1. W oknie **nowy węzeł Integration Runtime (Self-Hosted)** wybierz pozycję **Zakończ**.

    ![Okno Nowy węzeł Integration Runtime (Self-hosted)](media/tutorial-hybrid-copy-powershell/new-integration-runtime-node-page.png)

 1. Gdy własne środowisko Integration Runtime zostanie pomyślnie zarejestrowane, zostanie wyświetlony następujący komunikat:

    ![Pomyślnie zarejestrowano](media/tutorial-hybrid-copy-powershell/registered-successfully.png)

1. W oknie **Rejestrowanie produktu Integration Runtime (Self-hosted)** kliknij pozycję **Uruchom program Configuration Manager**.

1. Gdy węzeł zostanie połączony z usługą w chmurze, zostanie wyświetlony następujący komunikat:

    ![Węzeł jest połączony](media/tutorial-hybrid-copy-powershell/node-is-connected.png)

1. Przetestuj łączność z bazą danych programu SQL Server, wykonując następujące kroki:

    a. W oknie **Configuration Manager** przejdź na kartę **Diagnostyka** .

    b. W polu **Typ źródła danych** wybierz pozycję **SqlServer**.

    c. Wprowadź nazwę serwera.

    d. Wprowadź nazwę bazy danych.

    e. Wybierz tryb uwierzytelniania.

    f. Wprowadź nazwę użytkownika.

    przykład Wprowadź hasło powiązane z tą nazwą użytkownika.

    h. Wybierz przycisk **Testuj**, aby upewnić się, że środowisko Integration Runtime może połączyć się z wystąpieniem programu SQL Server.  
    ![Łączność powiodła się](media/tutorial-hybrid-copy-powershell/config-manager-diagnostics-tab.png)

    Jeśli połączenie zostanie pomyślnie nawiązane, zostanie wyświetlona ikona zielonego znacznika wyboru. W przeciwnym razie zostanie wyświetlony komunikat o błędzie skojarzony z określonym błędem. Rozwiąż wszelkie problemy i upewnij się, że środowisko Integration Runtime może połączyć się z wystąpieniem programu SQL Server.

    Zanotuj wszystkie powyższe wartości do późniejszego użycia z tym samouczkiem.

## <a name="create-linked-services"></a>Tworzenie połączonych usług
Utwórz połączone usługi w fabryce danych w celu połączenia swoich magazynów danych i usług obliczeniowych z fabryką danych. W tym samouczku połączysz swoje konto usługi Azure Storage i wystąpienie SQL Server z magazynem danych. Połączone usługi mają informacje o połączeniu, których usługa Data Factory używa w środowisku uruchomieniowym do łączenia się z nimi.

### <a name="create-an-azure-storage-linked-service-destinationsink"></a>Tworzenie połączonej usługi Azure Storage (obiektu docelowego/ujścia)
W tym kroku opisano łączenie konta usługi Azure Storage z fabryką danych.

1. W folderze *C:\ADFv2Tutorial* utwórz plik JSON o nazwie *AzureStorageLinkedService.json*, używając poniższego kodu. Jeśli folder *ADFv2Tutorial* jeszcze nie istnieje, utwórz go.  

    > [!IMPORTANT]
    > Przed zapisaniem pliku Zastąp wartości \<accountName> i \<accountKey> nazwą i kluczem konta usługi Azure Storage. Te wartości zapisano podczas wykonywania czynności opisanych w sekcji [Wymagania wstępne](#get-storage-account-name-and-account-key).

   ```json
    {
        "name": "AzureStorageLinkedService",
        "properties": {
            "annotations": [],
            "type": "AzureBlobStorage",
            "typeProperties": {
                "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountName>;AccountKey=<accountKey>;EndpointSuffix=core.windows.net"
            }
        }
    }
   ```

1. W programie PowerShell przejdź do folderu *C:\ADFv2Tutorial*.
   ```powershell
   Set-Location 'C:\ADFv2Tutorial'    
   ```

1. Aby utworzyć połączoną usługę o nazwie AzureStorageLinkedService, uruchom następujące polecenie cmdlet `Set-AzDataFactoryV2LinkedService`:

   ```powershell
   Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "AzureStorageLinkedService" -File ".\AzureStorageLinkedService.json"
   ```

   Oto przykładowe dane wyjściowe:

    ```json
    LinkedServiceName : AzureStorageLinkedService
    ResourceGroupName : <resourceGroup name>
    DataFactoryName   : <dataFactory name>
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureBlobStorageLinkedService
    ```

    Jeśli zostanie zwrócony błąd „nie znaleziono pliku”, sprawdź, czy plik istnieje, uruchamiając polecenie `dir`. Jeśli nazwa pliku ma rozszerzenie *txt* (na przykład AzureStorageLinkedService.json.txt), usuń je, a następnie ponownie uruchom polecenie programu PowerShell.

### <a name="create-and-encrypt-a-sql-server-linked-service-source"></a>Tworzenie i szyfrowanie połączonej usługi SQL Server (źródło)
W tym kroku połączysz wystąpienie SQL Server z fabryką danych.

1. W folderze *C:\ADFv2Tutorial* utwórz plik JSON o nazwie *SqlServerLinkedService.json*, używając następującego kodu:

    > [!IMPORTANT]
    > Wybierz właściwą sekcję na podstawie metody uwierzytelniania używanej do nawiązywania połączenia z programem SQL Server.

    **Nawiązywanie połączenia przy użyciu uwierzytelniania SQL (sa):**

    ```json
    {  
        "name":"SqlServerLinkedService",
        "type":"Microsoft.DataFactory/factories/linkedservices",
        "properties":{  
            "annotations":[  

            ],
            "type":"SqlServer",
            "typeProperties":{  
                "connectionString":"integrated security=False;data source=<serverName>;initial catalog=<databaseName>;user id=<userName>;password=<password>"
            },
            "connectVia":{  
                "referenceName":"<integration runtime name> ",
                "type":"IntegrationRuntimeReference"
            }
        }
    }
   ```    

    **Nawiązywanie połączenia przy użyciu uwierzytelniania systemu Windows:**

    ```json
    {  
        "name":"SqlServerLinkedService",
        "type":"Microsoft.DataFactory/factories/linkedservices",
        "properties":{  
            "annotations":[  

            ],
            "type":"SqlServer",
            "typeProperties":{  
                "connectionString":"integrated security=True;data source=<serverName>;initial catalog=<databaseName>",
                "userName":"<username> or <domain>\\<username>",
                "password":{  
                    "type":"SecureString",
                    "value":"<password>"
                }
            },
            "connectVia":{  
                "referenceName":"<integration runtime name>",
                "type":"IntegrationRuntimeReference"
            }
        }
    }
    ```

    > [!IMPORTANT]
    > - Wybierz właściwą sekcję na podstawie metody uwierzytelniania używanej do nawiązywania połączenia z wystąpieniem programu SQL Server.
    > - Zamień na  **\<integration runtime name>** nazwę Twojego środowiska Integration Runtime.
    > - Przed zapisaniem pliku Zastąp **\<servername>** wartości,, **\<databasename>** **\<username>** i **\<password>** wartościami wystąpienia SQL Server.
    > - Jeśli musisz użyć znaku ukośnika (\\) w nazwie konta użytkownika lub nazwie serwera, poprzedź go znakiem ucieczki (\\). Na przykład użyj elementu *webdomain \\ \\ *.

1. Aby zaszyfrować dane poufne (nazwę użytkownika, hasło itp.), uruchom polecenie cmdlet `New-AzDataFactoryV2LinkedServiceEncryptedCredential`.  
    To zapewnia szyfrowanie poświadczeń za pomocą interfejsu API ochrony danych (DPAPI). Zaszyfrowane poświadczenia są przechowywane lokalnie w węźle środowiska Integration Runtime (Self-hosted) (maszyna lokalna). Ładunek danych wyjściowych może zostać przekierowany do innego pliku JSON (w tym przypadku *encryptedLinkedService.json*), który zawiera zaszyfrowane poświadczenia.

   ```powershell
   New-AzDataFactoryV2LinkedServiceEncryptedCredential -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -IntegrationRuntimeName $integrationRuntimeName -File ".\SQLServerLinkedService.json" > encryptedSQLServerLinkedService.json
   ```

1. Uruchom następujące polecenie, aby utworzyć element EncryptedSqlServerLinkedService:

   ```powershell
   Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "EncryptedSqlServerLinkedService" -File ".\encryptedSqlServerLinkedService.json"
   ```


## <a name="create-datasets"></a>Tworzenie zestawów danych
W tym kroku utworzysz zestawy danych wejściowych i wyjściowych. Reprezentują dane wejściowe i wyjściowe dla operacji kopiowania, które kopiuje dane z bazy danych SQL Server do usługi Azure Blob Storage.

### <a name="create-a-dataset-for-the-source-sql-server-database"></a>Tworzenie zestawu danych źródłowej bazy danych programu SQL Server
W tym kroku zdefiniujesz zestaw danych, który reprezentuje dane w wystąpieniu bazy danych programu SQL Server. Typ zestawu danych to SqlServerTable. Odwołuje się on do połączonej usługi programu SQL Server utworzonej w poprzednim kroku. Połączona usługa ma informacje o połączeniu, których usługa Data Factory używa do nawiązywania połączenia z wystąpieniem programu SQL Server w środowisku uruchomieniowym. Ten zestaw danych określa tabelę SQL w bazie danych, która zawiera dane. W tym samouczku dane źródłowe zawiera tabela **emp**.

1. Utwórz plik JSON o nazwie *SqlServerDataset.json* w folderze *C:\ADFv2Tutorial*, używając następującego kodu:  
    ```json
    {  
        "name":"SqlServerDataset",
        "properties":{  
            "linkedServiceName":{  
                "referenceName":"EncryptedSqlServerLinkedService",
                "type":"LinkedServiceReference"
            },
            "annotations":[  

            ],
            "type":"SqlServerTable",
            "schema":[  

            ],
            "typeProperties":{  
                "schema":"dbo",
                "table":"emp"
            }
        }
    }
    ```

1. Aby utworzyć zestaw danych SqlServerDataset, uruchom polecenie cmdlet `Set-AzDataFactoryV2Dataset`.

    ```powershell
    Set-AzDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SqlServerDataset" -File ".\SqlServerDataset.json"
    ```

    Oto przykładowe dane wyjściowe:

    ```json
    DatasetName       : SqlServerDataset
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.SqlServerTableDataset
    ```

### <a name="create-a-dataset-for-azure-blob-storage-sink"></a>Tworzenie zestawu danych usługi Azure Blob Storage (ujścia)
W tym kroku zdefiniujesz zestaw danych reprezentujący dane, które mają zostać skopiowane do usługi Azure Blob Storage. Typ tego zestawu danych to AzureBlob. Odwołuje się on do połączonej usługi Azure Storage utworzonej wcześniej w tym samouczku.

Połączona usługa ma informacje o połączeniu, których usługa Data Factory używa w środowisku uruchomieniowym do nawiązania połączenia z kontem usługi Azure Storage. Ten zestaw danych określa folder w usłudze Azure Storage, do którego kopiowane są dane z bazy danych programu SQL Server. W tym samouczku jest to folder *adftutorial/fromonprem*, gdzie `adftutorial` to kontener obiektów blob, a `fromonprem` to folder.

1. Utwórz plik JSON o nazwie *AzureBlobDataset.json* w folderze *C:\ADFv2Tutorial*, używając następującego kodu:

    ```json
    {  
        "name":"AzureBlobDataset",
        "properties":{  
            "linkedServiceName":{  
                "referenceName":"AzureStorageLinkedService",
                "type":"LinkedServiceReference"
            },
            "annotations":[  

            ],
            "type":"DelimitedText",
            "typeProperties":{  
                "location":{  
                    "type":"AzureBlobStorageLocation",
                    "folderPath":"fromonprem",
                    "container":"adftutorial"
                },
                "columnDelimiter":",",
                "escapeChar":"\\",
                "quoteChar":"\""
            },
            "schema":[  

            ]
        },
        "type":"Microsoft.DataFactory/factories/datasets"
    }
    ```

1. Aby utworzyć zestaw danych AzureBlobDataset, uruchom polecenie cmdlet `Set-AzDataFactoryV2Dataset`.

    ```powershell
    Set-AzDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureBlobDataset" -File ".\AzureBlobDataset.json"
    ```

    Oto przykładowe dane wyjściowe:

    ```json
    DatasetName       : AzureBlobDataset
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.DelimitedTextDataset
    ```

## <a name="create-a-pipeline"></a>Tworzenie potoku
W tym samouczku jest tworzony potok za pomocą działania kopiowania. Działanie kopiowania używa zestawu danych SqlServerDataset jako wejściowego zestawu danych oraz zestawu danych AzureBlobDataset jako wyjściowego zestawu danych. Typ źródła jest ustawiony na wartość *SqlSource*, a typ ujścia — na wartość *BlobSink*.

1. Utwórz plik JSON o nazwie *SqlServerToBlobPipeline.json* w folderze *C:\ADFv2Tutorial*, używając następującego kodu:

    ```json
    {  
        "name":"SqlServerToBlobPipeline",
        "properties":{  
            "activities":[  
                {  
                    "name":"CopySqlServerToAzureBlobActivity",
                    "type":"Copy",
                    "dependsOn":[  

                    ],
                    "policy":{  
                        "timeout":"7.00:00:00",
                        "retry":0,
                        "retryIntervalInSeconds":30,
                        "secureOutput":false,
                        "secureInput":false
                    },
                    "userProperties":[  

                    ],
                    "typeProperties":{  
                        "source":{  
                            "type":"SqlServerSource"
                        },
                        "sink":{  
                            "type":"DelimitedTextSink",
                            "storeSettings":{  
                                "type":"AzureBlobStorageWriteSettings"
                            },
                            "formatSettings":{  
                                "type":"DelimitedTextWriteSettings",
                                "quoteAllText":true,
                                "fileExtension":".txt"
                            }
                        },
                        "enableStaging":false
                    },
                    "inputs":[  
                        {  
                            "referenceName":"SqlServerDataset",
                            "type":"DatasetReference"
                        }
                    ],
                    "outputs":[  
                        {  
                            "referenceName":"AzureBlobDataset",
                            "type":"DatasetReference"
                        }
                    ]
                }
            ],
            "annotations":[  

            ]
        }
    }
    ```

1. Aby utworzyć potok SQLServerToBlobPipeline, uruchom polecenie cmdlet `Set-AzDataFactoryV2Pipeline`.

    ```powershell
    Set-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SQLServerToBlobPipeline" -File ".\SQLServerToBlobPipeline.json"
    ```

    Oto przykładowe dane wyjściowe:

    ```json
    PipelineName      : SQLServerToBlobPipeline
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Activities        : {CopySqlServerToAzureBlobActivity}
    Parameters        :  
    ```

## <a name="create-a-pipeline-run"></a>Tworzenie uruchomienia potoku
Uruchom działanie potoku dla potoku SQLServerToBlobPipeline i zapisz identyfikator działania potoku do monitorowania w przyszłości.

```powershell
$runId = Invoke-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName 'SQLServerToBlobPipeline'
```

## <a name="monitor-the-pipeline-run"></a>Monitorowanie działania potoku

1. Aby stale sprawdzać stan działania potoku SQLServerToBlobPipeline, uruchom następujący skrypt w programie PowerShell, a następnie wydrukuj wynik końcowy:

    ```powershell
    while ($True) {
        $result = Get-AzDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)

        if (($result | Where-Object { $_.Status -eq "InProgress" } | Measure-Object).count -ne 0) {
            Write-Host "Pipeline run status: In Progress" -foregroundcolor "Yellow"
            Start-Sleep -Seconds 30
        }
        else {
            Write-Host "Pipeline 'SQLServerToBlobPipeline' run finished. Result:" -foregroundcolor "Yellow"
            $result
            break
        }
    }
    ```

    Oto dane wyjściowe przykładowego uruchomienia:

    ```JSON
    ResourceGroupName    : <resourceGroupName>
    DataFactoryName      : <dataFactoryName>
    ActivityRunId        : 24af7cf6-efca-4a95-931d-067c5c921c25
    ActivityName         : CopySqlServerToAzureBlobActivity
    ActivityType         : Copy
    PipelineRunId        : 7b538846-fd4e-409c-99ef-2475329f5729
    PipelineName         : SQLServerToBlobPipeline
    Input                : {source, sink, enableStaging}
    Output               : {dataRead, dataWritten, filesWritten, sourcePeakConnections...}
    LinkedServiceName    :
    ActivityRunStart     : 9/11/2019 7:10:37 AM
    ActivityRunEnd       : 9/11/2019 7:10:58 AM
    DurationInMs         : 21094
    Status               : Succeeded
    Error                : {errorCode, message, failureType, target}
    AdditionalProperties : {[retryAttempt, ], [iterationHash, ], [userProperties, {}], [recoveryStatus, None]...}
    ```

1. Aby uzyskać identyfikator działania potoku SQLServerToBlobPipeline i sprawdzić szczegółowe wyniki uruchomienia działania, uruchom następujące polecenie:

    ```powershell
    Write-Host "Pipeline 'SQLServerToBlobPipeline' run result:" -foregroundcolor "Yellow"
    ($result | Where-Object {$_.ActivityName -eq "CopySqlServerToAzureBlobActivity"}).Output.ToString()
    ```

    Oto dane wyjściowe przykładowego uruchomienia:

    ```json
    {  
        "dataRead":36,
        "dataWritten":32,
        "filesWritten":1,
        "sourcePeakConnections":1,
        "sinkPeakConnections":1,
        "rowsRead":2,
        "rowsCopied":2,
        "copyDuration":18,
        "throughput":0.01,
        "errors":[  

        ],
        "effectiveIntegrationRuntime":"ADFTutorialIR",
        "usedParallelCopies":1,
        "executionDetails":[  
            {  
                "source":{  
                    "type":"SqlServer"
                },
                "sink":{  
                    "type":"AzureBlobStorage",
                    "region":"CentralUS"
                },
                "status":"Succeeded",
                "start":"2019-09-11T07:10:38.2342905Z",
                "duration":18,
                "usedParallelCopies":1,
                "detailedDurations":{  
                    "queuingDuration":6,
                    "timeToFirstByte":0,
                    "transferDuration":5
                }
            }
        ]
    }
    ```

## <a name="verify-the-output"></a>Sprawdzanie danych wyjściowych
Potok automatycznie tworzy folder wyjściowy o nazwie *fromonprem* w kontenerze obiektów blob `adftutorial`. Upewnij się, że plik *dbo.emp.txt* jest widoczny w folderze wyjściowym.

1. W witrynie Azure Portal w oknie kontenera **adftutorial** kliknij przycisk **Odśwież**, aby wyświetlić folder wyjściowy.
1. Wybierz pozycję `fromonprem` na liście folderów.
1. Upewnij się, że jest wyświetlany plik o nazwie `dbo.emp.txt`.

    ![Plik wyjściowy](media/tutorial-hybrid-copy-powershell/fromonprem-file.png)


## <a name="next-steps"></a>Następne kroki
Potok w tym przykładzie kopiuje dane z jednej lokalizacji do innej lokalizacji w usłudze Azure Blob Storage. W tym samouczku omówiono:

> [!div class="checklist"]
> * Tworzenie fabryki danych.
> * Utwórz własne środowisko Integration Runtime.
> * Tworzenie połączonych zestawów programu SQL Server i usługi Azure Storage.
> * Tworzenie zestawów danych programu SQL Server i usługi Azure Blob.
> * Tworzenie potoku z działaniem kopiowania do przenoszenia danych.
> * Uruchom potok.
> * Monitorowanie uruchomienia potoku.

Listę magazynów danych obsługiwanych przez Data Factory można znaleźć w temacie [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).

Przejdź do poniższego samouczka, aby dowiedzieć się o zbiorczym kopiowaniu danych z lokalizacji źródłowej do docelowej:

> [!div class="nextstepaction"]
>[Zbiorcze kopiowanie danych](tutorial-bulk-copy.md)
