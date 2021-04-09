---
title: Przenoszenie danych — Brama Zarządzanie danymi
description: Skonfiguruj bramę danych do przenoszenia danych między środowiskiem lokalnym i chmurą. Użyj Zarządzanie danymi Gateway w Azure Data Factory, aby przenieść dane.
author: nabhishek
ms.author: abnarain
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
robots: noindex
ms.openlocfilehash: 53fce1744ccbf4289b2415e926e084c90d708a13
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100380291"
---
# <a name="move-data-between-on-premises-sources-and-the-cloud-with-data-management-gateway"></a>Przenoszenie danych między źródłami lokalnymi i chmurą przy użyciu bramy Zarządzanie danymi
> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącej wersji usługi Data Factory, zobacz [Kopiowanie danych między środowiskiem lokalnym i chmurą przy użyciu Data Factory](../tutorial-hybrid-copy-powershell.md).

Ten artykuł zawiera Omówienie integracji danych między lokalnymi magazynami danych i magazynami danych w chmurze przy użyciu Data Factory. Jest ona oparta na artykule o [działaniach związanych z przenoszeniem danych](data-factory-data-movement-activities.md) i innych artykułach podstawowych pojęć związanych z usługą Data Factory: [zestawy](data-factory-create-datasets.md) danych i [potoki](data-factory-create-pipelines.md).

## <a name="data-management-gateway"></a>Brama zarządzania danymi
Aby umożliwić przeniesienie danych do/z lokalnego magazynu danych, należy zainstalować bramę Zarządzanie danymi na maszynie lokalnej. Bramę można zainstalować na tym samym komputerze, na którym znajduje się magazyn danych lub na innym komputerze, o ile Brama może połączyć się z magazynem danych.

> [!IMPORTANT]
> Szczegółowe informacje o bramie Zarządzanie danymi można znaleźć w artykule [Zarządzanie danymi Gateway](data-factory-data-management-gateway.md) .

W poniższym przewodniku pokazano, jak utworzyć fabrykę danych z potokiem, który przenosi dane z lokalnej bazy danych **SQL Server** do magazynu obiektów blob platformy Azure. W ramach tego przewodnika zainstalujesz i skonfigurujesz bramę zarządzania danymi na tym komputerze.

## <a name="walkthrough-copy-on-premises-data-to-cloud"></a>Przewodnik: kopiowanie danych lokalnych do chmury
W tym instruktażu wykonasz następujące czynności:

1. Tworzenie fabryki danych.
2. Utwórz bramę zarządzania danymi.
3. Tworzenie połączonych usług dla magazynów danych źródłowych i ujścia.
4. Utwórz zestawy danych, aby reprezentować dane wejściowe i wyjściowe.
5. Tworzenie potoku z działaniem kopiowania do przenoszenia danych.

## <a name="prerequisites-for-the-tutorial"></a>Wymagania wstępne dotyczące samouczka
Przed rozpoczęciem tego instruktażu należy spełnić następujące wymagania wstępne:

* **Subskrypcja platformy Azure**.  Jeśli nie masz subskrypcji, możesz utworzyć konto bezpłatnej wersji próbnej w zaledwie kilka minut. Szczegółowe informacje można znaleźć w artykule dotyczącym [bezpłatnej wersji próbnej](https://azure.microsoft.com/pricing/free-trial/) .
* **Konto usługi Azure Storage**. Magazyn obiektów BLOB jest używany jako magazyn danych **docelowych/ujścia** w tym samouczku. Jeśli nie masz konta usługi Azure Storage, zapoznaj się z artykułem [Tworzenie konta magazynu](../../storage/common/storage-account-create.md) , aby dowiedzieć się, jak go utworzyć.
* **SQL Server**. W tym samouczku używasz bazy danych SQL Server jako **źródła** magazynu danych.

## <a name="create-data-factory"></a>Tworzenie fabryki danych
W tym kroku użyjesz Azure Portal, aby utworzyć wystąpienie Azure Data Factory o nazwie **ADFTutorialOnPremDF**.

1. Zaloguj się do [Azure Portal](https://portal.azure.com).
2. Kliknij pozycję **Utwórz zasób**, kliknij pozycję **Analiza i analiza**, a następnie kliknij pozycję **Data Factory**.

   ![Nowy->Fabryka danych](./media/data-factory-move-data-between-onprem-and-cloud/NewDataFactoryMenu.png)  
3. Na stronie **Nowa fabryka danych** wprowadź **ADFTutorialOnPremDF** w polu Nazwa.

    ![Dodaj do tablicy startowej](./media/data-factory-move-data-between-onprem-and-cloud/OnPremNewDataFactoryAddToStartboard.png)

   > [!IMPORTANT]
   > Nazwa fabryki danych Azure musi być globalnie unikatowa. Jeśli wystąpi błąd: **Nazwa fabryki danych "ADFTutorialOnPremDF" jest niedostępna**, Zmień nazwę fabryki danych (np. yournameADFTutorialOnPremDF) i spróbuj utworzyć ją ponownie. Użyj tej nazwy zamiast ADFTutorialOnPremDF podczas wykonywania pozostałych kroków w tym samouczku.
   >
   > W przyszłości nazwa fabryki danych może zostać zarejestrowana jako nazwa **DNS** , a zatem stanie się widoczna publicznie.
   >
   >
4. Wybierz **subskrypcję Azure**, w ramach której chcesz utworzyć fabrykę danych.
5. Wybierz istniejącą **grupę zasobów** lub utwórz grupę zasobów. Aby zapoznać się z samouczkiem, Utwórz grupę zasobów o nazwie: **ADFTutorialResourceGroup**.
6. Kliknij przycisk **Utwórz** na stronie **Nowa fabryka danych** .

   > [!IMPORTANT]
   > Aby utworzyć wystąpienia usługi Data Factory, użytkownik musi być członkiem roli [współautora usługi Data Factory](../../role-based-access-control/built-in-roles.md#data-factory-contributor) na poziomie subskrypcji/grupy zasobów.
   >
   >
7. Po zakończeniu tworzenia zostanie wyświetlona strona **Data Factory** , jak pokazano na poniższej ilustracji:

   ![Strona główna Data Factory](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDataFactoryHomePage.png)

## <a name="create-gateway"></a>Utwórz bramę
1. Na stronie **Data Factory** kliknij kafelek **Utwórz i Wdróż** , aby uruchomić **Edytor** dla fabryki danych.

    ![Kafelek Utwórz i wdróż](./media/data-factory-move-data-between-onprem-and-cloud/author-deploy-tile.png)
2. W edytorze Data Factory kliknij pozycję **... Więcej** na pasku narzędzi, a następnie kliknij pozycję **Nowa brama danych**. Alternatywnie możesz kliknąć prawym przyciskiem myszy pozycję **bramy danych** w widoku drzewa, a następnie kliknąć pozycję **Nowa brama danych**.

   ![Nowa brama danych na pasku narzędzi](./media/data-factory-move-data-between-onprem-and-cloud/NewDataGateway.png)
3. Na stronie **Tworzenie** wprowadź **adftutorialgateway** jako **nazwę**, a następnie kliknij przycisk **OK**.     

    ![Utwórz stronę bramy](./media/data-factory-move-data-between-onprem-and-cloud/OnPremCreateGatewayBlade.png)

    > [!NOTE]
    > W tym instruktażu utworzysz bramę logiczną z tylko jednym węzłem (lokalna maszyna z systemem Windows). Bramę zarządzania danymi można skalować w poziomie, kojarząc wiele maszyn lokalnych z bramą. Możesz skalować w górę, zwiększając liczbę zadań przenoszenia danych, które mogą być uruchamiane współbieżnie w węźle. Ta funkcja jest również dostępna dla bramy logicznej z pojedynczym węzłem. Aby uzyskać szczegółowe informacje, zobacz temat [skalowanie bramy zarządzania danymi w artykule Azure Data Factory](data-factory-data-management-gateway-high-availability-scalability.md) .  
4. Na stronie **Konfiguracja** kliknij pozycję **Instaluj bezpośrednio na tym komputerze**. Ta akcja powoduje pobranie pakietu instalacyjnego dla bramy, zainstalowanie, skonfigurowanie i zarejestrowanie bramy na komputerze.  

   > [!NOTE]
   > Użyj programu Internet Explorer lub przeglądarki internetowej zgodnej z programem Microsoft ClickOnce.
   >
   > Jeśli używasz programu Chrome, przejdź do [sklepu sieci Web programu Chrome](https://chrome.google.com/webstore/), wyszukaj słowo kluczowe "ClickOnce", wybierz jedno z rozszerzeń ClickOnce i zainstaluj je.
   >
   > Wykonaj te same czynności dla przeglądarki Firefox (Zainstaluj dodatek). Kliknij przycisk **Otwórz menu** na pasku narzędzi (**trzy poziome linie** w prawym górnym rogu), kliknij pozycję **Dodatki**, wyszukaj słowo kluczowe "ClickOnce", wybierz jedno z rozszerzeń ClickOnce i zainstaluj je.    
   >
   >

    ![Brama — Konfigurowanie strony](./media/data-factory-move-data-between-onprem-and-cloud/OnPremGatewayConfigureBlade.png)

    W ten sposób najłatwiejszym sposobem (jednym kliknięciem) jest pobranie, zainstalowanie, skonfigurowanie i zarejestrowanie bramy w jednym kroku. Na komputerze jest zainstalowana aplikacja **Configuration Manager Microsoft zarządzanie danymi Gateway** . **ConfigManager.exe** pliku wykonywalnego można również znaleźć w folderze: **C:\Program Files\Microsoft zarządzanie danymi Gateway\2.0\Shared**.

    Możesz również ręcznie pobrać i zainstalować bramę, korzystając z linków na tej stronie i rejestrując je przy użyciu klucza podanego w polu tekstowym **nowy klucz** .

    Aby uzyskać szczegółowe informacje o bramie, zobacz artykuł dotyczący [bramy zarządzanie danymi](data-factory-data-management-gateway.md) .

   > [!NOTE]
   > Aby pomyślnie zainstalować i skonfigurować bramę Zarządzanie danymi, musisz mieć uprawnienia administratora na komputerze lokalnym. Możesz dodać dodatkowych użytkowników do lokalnej grupy systemu Windows dla **użytkowników bramy zarządzanie danymi** . Aby skonfigurować bramę, członkowie tej grupy mogą używać narzędzia Configuration Manager Zarządzanie danymi Gateway.
   >
   >
5. Poczekaj kilka minut lub zaczekaj, aż zobaczysz następujący komunikat powiadomienia:

    ![Pomyślnie zainstalowano bramę](./media/data-factory-move-data-between-onprem-and-cloud/gateway-install-success.png)
6. Uruchom aplikację **Zarządzanie danymi Gateway Configuration Manager** na swoim komputerze. W oknie **wyszukiwania** wpisz **Zarządzanie danymi Gateway** , aby uzyskać dostęp do tego narzędzia. **ConfigManager.exe** pliku wykonywalnego można również znaleźć w folderze: **C:\Program Files\Microsoft zarządzanie danymi Gateway\2.0\Shared**

    ![Configuration Manager bramy](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDMGConfigurationManager.png)
7. Potwierdź, że zobaczysz `adftutorialgateway is connected to the cloud service` komunikat. Na dolnej liście wyświetlany jest pasek stanu **połączony z usługą w chmurze** z **zielonym znacznikiem wyboru**.

    Na karcie **Narzędzia główne** można także wykonać następujące operacje:

   * **Zarejestrowanie** bramy przy użyciu klucza z Azure Portal przy użyciu przycisku Zarejestruj.
   * **Zatrzymaj** usługę hosta bramy zarządzanie danymi uruchomioną na maszynie bramy.
   * **Zaplanuj instalację aktualizacji** w określonym czasie dnia.
   * Wyświetl **datę ostatniej aktualizacji** bramy.
   * Określ godzinę, o której można zainstalować aktualizację bramy.
8. Przejdź do karty **Ustawienia** . Certyfikat określony w sekcji **certyfikat** służy do szyfrowania/odszyfrowywania poświadczeń dla lokalnego magazynu danych określonego w portalu. obowiązkowe Kliknij przycisk **Zmień** , aby zamiast tego użyć własnego certyfikatu. Domyślnie brama używa certyfikatu, który jest automatycznie generowany przez usługę Data Factory.

    ![Konfiguracja certyfikatu bramy](./media/data-factory-move-data-between-onprem-and-cloud/gateway-certificate.png)

    Na karcie **Ustawienia** można również wykonać następujące czynności:

   * Wyświetl lub wyeksportuj certyfikat używany przez bramę.
   * Zmień punkt końcowy HTTPS używany przez bramę.    
   * Ustaw serwer proxy HTTP, który ma być używany przez bramę.     
9. obowiązkowe Przejdź do karty **Diagnostyka** , zaznacz opcję **Włącz pełne rejestrowanie** , jeśli chcesz włączyć pełne rejestrowanie, którego można użyć do rozwiązywania problemów z bramą. Informacje o rejestrowaniu można znaleźć w **Podgląd zdarzeń** w obszarze **Dzienniki aplikacji i usług**  ->  **Zarządzanie danymi węźle bramy** .

    ![Karta Diagnostyka](./media/data-factory-move-data-between-onprem-and-cloud/diagnostics-tab.png)

    Na karcie **Diagnostyka** można także wykonać następujące czynności:

   * Użyj sekcji **Testuj połączenie** do lokalnego źródła danych przy użyciu bramy.
   * Kliknij przycisk **Wyświetl dzienniki** , aby wyświetlić dziennik zarządzanie danymi bramy w oknie Podgląd zdarzeń.
   * Kliknij pozycję **Wyślij dzienniki** , aby przesłać plik zip z dziennikami z ostatnich siedmiu dni do firmy Microsoft w celu ułatwienia rozwiązywania problemów.
10. Na karcie **Diagnostyka** w sekcji **Testuj połączenie** wybierz pozycję **SqlServer** dla typu magazynu danych, wprowadź nazwę serwera bazy danych, nazwę bazy danych, określ typ uwierzytelniania, wprowadź nazwę użytkownika i hasło, a następnie kliknij przycisk **Testuj** , aby sprawdzić, czy brama może nawiązać połączenie z bazą danych.
11. Przejdź do przeglądarki sieci Web, a w **Azure Portal** kliknij przycisk **OK** na stronie **Konfigurowanie** , a następnie na stronie **Nowa brama danych** .
12. W widoku drzewa po lewej stronie powinien być widoczny **adftutorialgateway** w obszarze **bramy danych** .  Jeśli klikniesz ją, zobaczysz skojarzony kod JSON.

## <a name="create-linked-services"></a>Tworzenie połączonych usług
W tym kroku utworzysz dwie połączone usługi: **AzureStorageLinkedService** i **SqlServerLinkedService**. **SqlServerLinkedService** łączy SQL Server bazą danych, a połączona usługa **AzureStorageLinkedService** łączy magazyn obiektów blob platformy Azure z fabryką danych. W tym instruktażu utworzysz potok, który kopiuje dane z bazy danych SQL Server do magazynu obiektów blob platformy Azure.

#### <a name="add-a-linked-service-to-a-sql-server-database"></a>Dodawanie połączonej usługi do bazy danych SQL Server
1. W **edytorze Data Factory** kliknij pozycję **nowy magazyn danych** na pasku narzędzi i wybierz pozycję **SQL Server**.

   ![Nowa SQL Server połączona usługa](./media/data-factory-move-data-between-onprem-and-cloud/NewSQLServer.png)
2. W **Edytorze JSON** po prawej stronie wykonaj następujące czynności:

   1. Dla **bramy** Określ wartość **adftutorialgateway**.    
   2. W **elemencie ConnectionString** wykonaj następujące czynności:    

      1. W polu Nazwa **serwera** wprowadź nazwę serwera, który obsługuje bazę danych SQL Server.
      2. W polu **DatabaseName** wprowadź nazwę bazy danych.
      3. Kliknij przycisk **Szyfruj** na pasku narzędzi. Zostanie wyświetlona aplikacja Menedżer poświadczeń.

         ![Aplikacja Menedżera poświadczeń](./media/data-factory-move-data-between-onprem-and-cloud/credentials-manager-application.png)
      4. W oknie dialogowym **Ustawianie poświadczeń** Określ typ uwierzytelniania, nazwę użytkownika i hasło, a następnie kliknij przycisk **OK**. W przypadku pomyślnego nawiązania połączenia zaszyfrowane poświadczenia są przechowywane w formacie JSON, a okno dialogowe zostanie zamknięte.
      5. Zamknij pustą kartę przeglądarki, która uruchomiła okno dialogowe, jeśli nie jest automatycznie zamknięte i wróć do karty przy użyciu Azure Portal.

         Na maszynie bramy te poświadczenia są **szyfrowane** przy użyciu certyfikatu, którego właścicielem jest usługa Data Factory. Jeśli zamiast tego chcesz użyć certyfikatu skojarzonego z bramą Zarządzanie danymi, zobacz Ustawianie poświadczeń bezpiecznie.    
   3. Kliknij przycisk **Wdróż** na pasku poleceń, aby wdrożyć połączoną usługę SQL Server. Połączona usługa powinna zostać wyświetlona w widoku drzewa.

      ![SQL Server połączoną usługę w widoku drzewa](./media/data-factory-move-data-between-onprem-and-cloud/sql-linked-service-in-tree-view.png)    

#### <a name="add-a-linked-service-for-an-azure-storage-account"></a>Dodawanie połączonej usługi dla konta usługi Azure Storage
1. W **edytorze Data Factory** kliknij pozycję **nowy magazyn danych** na pasku poleceń, a następnie kliknij pozycję **Azure Storage**.
2. Wprowadź nazwę konta usługi Azure Storage w polu **nazwa konta**.
3. Wprowadź klucz konta usługi Azure Storage dla **klucza konta**.
4. Kliknij przycisk **Wdróż** , aby wdrożyć **AzureStorageLinkedService**.

## <a name="create-datasets"></a>Tworzenie zestawów danych
W tym kroku utworzysz zestawy danych wejściowych i wyjściowych, które reprezentują dane wejściowe i wyjściowe operacji kopiowania (lokalna baza danych programu SQL Server => usługa Azure Blob Storage). Przed utworzeniem zestawów danych wykonaj następujące czynności (szczegółowy opis kroków znajduje się po liście):

* Utwórz tabelę o nazwie **emp** w bazie danych programu SQL Server dodanej jako połączona usługa do fabryki danych i wstaw kilka przykładowych wpisów do tabeli.
* Utwórz kontener obiektów blob o nazwie **adftutorial** na koncie usługi Azure Blob Storage dodanym jako połączona usługa do fabryki danych.

### <a name="prepare-on-premises-sql-server-for-the-tutorial"></a>Przygotowywanie SQL Server lokalnych dla samouczka
1. W bazie danych określonej dla SQL Server połączonej usługi (**SqlServerLinkedService**) Użyj poniższego skryptu SQL, aby utworzyć tabelę **EMP** w bazie danych.

    ```SQL   
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50),
        CONSTRAINT PK_emp PRIMARY KEY (ID)
    )
    GO
    ```
2. Wstaw do tabeli kilka przykładów:

    ```SQL
    INSERT INTO emp VALUES ('John', 'Doe')
    INSERT INTO emp VALUES ('Jane', 'Doe')
    ```

### <a name="create-input-dataset"></a>Tworzenie wejściowego zestawu danych

1. W **edytorze Data Factory** kliknij pozycję **... Więcej**, kliknij przycisk **Nowy zestaw danych** na pasku poleceń, a następnie kliknij **SQL Server tabelę**.
2. Zastąp kod JSON w prawym okienku następującym tekstem:

    ```JSON   
    {        
        "name": "EmpOnPremSQLTable",
        "properties": {
            "type": "SqlServerTable",
            "linkedServiceName": "SqlServerLinkedService",
            "typeProperties": {
                "tableName": "emp"
            },
            "external": true,
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "externalData": {
                    "retryInterval": "00:01:00",
                    "retryTimeout": "00:10:00",
                    "maximumRetry": 3
                }
            }
        }
    }     
    ```       
   Pamiętaj o następujących kwestiach:

   * **Typ** jest ustawiony na **SqlServerName**.
   * wartość **TableName** jest ustawiona na **EMP**.
   * **linkedServiceName** jest ustawiona na **SqlServerLinkedService** (ta połączona usługa została utworzona wcześniej w tym instruktażu).
   * Dla wejściowego zestawu danych, który nie jest generowany przez inny potok w Azure Data Factory, należy ustawić wartość **zewnętrzny** na **true**. Oznacza to, że dane wejściowe są tworzone poza usługą Azure Data Factory. Opcjonalnie można określić dowolne zasady danych zewnętrznych przy użyciu elementu **externalData** w sekcji **Policy** .    

   Aby uzyskać szczegółowe informacje na temat właściwości JSON [, zobacz Przenoszenie danych do/z SQL Server](data-factory-sqlserver-connector.md) .
3. Kliknij przycisk **Wdróż** na pasku poleceń, aby wdrożyć zestaw danych.  

### <a name="create-output-dataset"></a>Tworzenie wyjściowego zestawu danych

1. W **edytorze Data Factory** kliknij pozycję **Nowy zestaw danych** na pasku poleceń, a następnie kliknij pozycję **Azure Blob Storage**.
2. Zastąp kod JSON w prawym okienku następującym tekstem:

    ```JSON   
    {
        "name": "OutputBlobTable",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "folderPath": "adftutorial/outfromonpremdf",
                "format": {
                    "type": "TextFormat",
                    "columnDelimiter": ","
                }
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
     }
    ```   
   Pamiętaj o następujących kwestiach:

   * **Typ** jest ustawiony na **AzureBlob**.
   * **linkedServiceName** jest ustawiona na **AzureStorageLinkedService** (ta połączona usługa została utworzona w kroku 2).
   * **folderPath** jest ustawiona na **adftutorial/outfromonpremdf** , gdzie outfromonpremdf jest folderem w kontenerze adftutorial. Utwórz kontener **adftutorial** , jeśli jeszcze nie istnieje.
   * Parametr **availability** (dostępność) został ustawiony na wartość **hourly** (co godzinę) (parametr **frequency** [częstotliwość] został ustawiony na **hour** [godzinę], a **interval** [interwał] został ustawiony na wartość **1**).  Usługa Data Factory generuje wycinek danych wyjściowych co godzinę w tabeli **EMP** w Azure SQL Database.

   Jeśli nie określisz **nazwy pliku** dla **tabeli wyjściowej**, wygenerowane pliki w **folderPath** są nazywane w następującym formacie: `Data.<Guid>.txt` (na przykład:: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.).

   Aby ustawić parametry **folderPath** i **fileName** dynamicznie w oparciu o czas **SliceStart**, użyj właściwości partitionedBy. W poniższym przykładzie parametr folderPath używa elementów Year, Month i Day z parametru SliceStart (czas rozpoczęcia przetwarzania wycinka), a parametr fileName używa elementu Hour z parametru SliceStart. Na przykład jeśli wycinek jest generowany dla czasu 2014-10-20T08:00:00, parametr folderName zostaje ustawiony na wikidatagateway/wikisampledataout/2014/10/20, a parametr fileName zostaje ustawiony na wartość 08.csv.

    ```JSON
    "folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
    "fileName": "{Hour}.csv",
    "partitionedBy":
    [

        { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
        { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
        { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
        { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } }
    ],
    ```

    Aby uzyskać szczegółowe informacje na temat właściwości JSON [, zobacz Przenoszenie danych do/z usługi Azure Blob Storage](data-factory-azure-blob-connector.md) .
3. Kliknij przycisk **Wdróż** na pasku poleceń, aby wdrożyć zestaw danych. Upewnij się, że oba zestawy danych są widoczne w widoku drzewa.  

## <a name="create-pipeline"></a>Tworzenie potoku
W tym kroku utworzysz **potok** z jednym **działaniem kopiowania** , który używa **EmpOnPremSQLTable** jako danych wejściowych i **OutputBlobTable** jako danych wyjściowych.

1. W edytorze Data Factory kliknij pozycję **... Więcej**, a następnie kliknij pozycję **Nowy potok**.
2. Zastąp kod JSON w prawym okienku następującym tekstem:    

    ```JSON   
     {
         "name": "ADFTutorialPipelineOnPrem",
         "properties": {
         "description": "This pipeline has one Copy activity that copies data from an on premises SQL to Azure blob",
         "activities": [
           {
             "name": "CopyFromSQLtoBlob",
             "description": "Copy data from on premises SQL server to blob",
             "type": "Copy",
             "inputs": [
               {
                 "name": "EmpOnPremSQLTable"
               }
             ],
             "outputs": [
               {
                 "name": "OutputBlobTable"
               }
             ],
             "typeProperties": {
               "source": {
                 "type": "SqlSource",
                 "sqlReaderQuery": "select * from emp"
               },
               "sink": {
                 "type": "BlobSink"
               }
             },
             "Policy": {
               "concurrency": 1,
               "executionPriorityOrder": "NewestFirst",
               "style": "StartOfInterval",
               "retry": 0,
               "timeout": "01:00:00"
             }
           }
         ],
         "start": "2016-07-05T00:00:00Z",
         "end": "2016-07-06T00:00:00Z",
         "isPaused": false
       }
     }
    ```   
   > [!IMPORTANT]
   > Zastąp wartość właściwości **start** datą bieżącą, a wartość **end** datą jutrzejszą.
   >
   >

   Pamiętaj o następujących kwestiach:

   * W sekcji działania jest tylko działanie, którego **Typ** jest ustawiony na **Kopiuj**.
   * **Dane wejściowe** dla działania są ustawione na wartość **EmpOnPremSQLTable** , a **dane wyjściowe** dla działania są ustawione na **OutputBlobTable**.
   * W sekcji **typeProperties** , **sqlsource** jest określony jako **Typ źródła** , a **wartość blobsink** jest określony jako **Typ ujścia**.
   * Zapytanie SQL `select * from emp` jest określone dla właściwości **sqlReaderQuery** elementu **sqlsource**.

   Zarówno data/godzina rozpoczęcia, jak i data/godzina zakończenia muszą być w [formacie ISO](https://en.wikipedia.org/wiki/ISO_8601). Na przykład: 2014-10-14T16:32:41Z. Czas **end** jest opcjonalny, ale w tym samouczku zostanie użyty.

   Jeśli nie określisz wartości właściwości **End** , zostanie ona obliczona jako "**Start + 48 godzin**". Aby uruchomić potok bezterminowo, określ **9/9/9999** jako wartość dla właściwości **end**.

   Definiujesz czas, w którym wycinki danych są przetwarzane na podstawie właściwości **dostępności** , które zostały zdefiniowane dla każdego Azure Data Factory zestawu danych.

   W tym przykładzie występują 24 wycinki danych, ponieważ poszczególne wycinki są generowane co godzinę.        
3. Kliknij przycisk **Wdróż** na pasku poleceń, aby wdrożyć zestaw danych (tabela jest prostokątnym zestawem danych). Upewnij się, że potok jest wyświetlany w widoku drzewa w węźle **potoki** .  
4. Teraz kliknij dwukrotnie przycisk **X** , aby zamknąć stronę, aby wrócić do strony **Data Factory** **ADFTutorialOnPremDF**.

**Gratulacje!** Pomyślnie utworzono usługę Azure Data Factory, połączone usługi, zestawy danych i potok oraz zaplanowano potok.

#### <a name="view-the-data-factory-in-a-diagram-view"></a>Wyświetlanie fabryki danych w widoku diagramu
1. W **Azure Portal** kliknij kafelek **Diagram** na stronie głównej fabryki danych **ADFTutorialOnPremDF** . :

    ![Link diagramu](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDiagramLink.png)
2. Powinien zostać wyświetlony diagram podobny do tego na poniższej ilustracji:

    ![Widok diagramu](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDiagramView.png)

    Możesz powiększyć, pomniejszyć, powiększyć do 100%, powiększyć do dopasowania, automatycznie pozycjonować potoki i zestawy danych, a także pokazać informacje o położeniu podrzędnym (Podświetl elementy nadrzędne i podrzędne wybranych elementów).  Możesz kliknąć dwukrotnie obiekt (zestaw danych wejściowych/wyjściowych lub potok), aby wyświetlić jego właściwości.

## <a name="monitor-pipeline"></a>Monitorowanie potoku
W tym kroku użyjesz Azure Portal do monitorowania tego, co dzieje się w usłudze Azure Data Factory. Do monitorowania zestawów danych i potoków można również używać poleceń cmdlet programu PowerShell. Aby uzyskać szczegółowe informacje o monitorowaniu, zobacz [monitorowanie potoków i zarządzanie nimi](data-factory-monitor-manage-pipelines.md).

1. Na diagramie kliknij dwukrotnie pozycję **EmpOnPremSQLTable**.  

    ![Wycinki EmpOnPremSQLTable](./media/data-factory-move-data-between-onprem-and-cloud/OnPremSQLTableSlicesBlade.png)
2. Zwróć uwagę, że wszystkie wycinki danych są w stanie **gotowe** , ponieważ czas trwania potoku (czas rozpoczęcia do czasu zakończenia) jest w przeszłości. Istnieje również możliwość, że dane zostały wstawione do bazy danych SQL Server i cały czas. Upewnij się, że żadne wycinki nie są wyświetlane w sekcji **wycinki problemów** u dołu. Aby wyświetlić wszystkie wycinki, kliknij pozycję **Zobacz więcej** u dołu listy wycinków.
3. Teraz na stronie **zestawy danych** kliknij pozycję **OutputBlobTable**.

    ![Wycinki OputputBlobTable](./media/data-factory-move-data-between-onprem-and-cloud/OutputBlobTableSlicesBlade.png)
4. Kliknięcie dowolnego wycinka danych na liście powinno spowodować wyświetlenie strony **wycinka danych** . Zobaczysz uruchomienia działania dla wycinka. Zobaczysz tylko jedno uruchomienie działania.  

    ![Blok wycinka danych](./media/data-factory-move-data-between-onprem-and-cloud/DataSlice.png)

    Jeśli wycinek nie znajduje się w stanie **Gotowe**, możesz wyświetlić wycinki strumienia wychodzącego, które nie są w stanie Gotowe i blokują wykonanie bieżącego wycinka na liście **Niegotowe wycinki strumienia wychodzącego**.
5. Kliknij **przebieg działania** z listy na dole, aby wyświetlić **szczegóły uruchomienia działania**.

   ![Strona szczegółów uruchomienia działania](./media/data-factory-move-data-between-onprem-and-cloud/ActivityRunDetailsBlade.png)

   Zobaczysz informacje takie jak przepływność, czas trwania i Brama służąca do transferu danych.
6. Kliknij przycisk **X** , aby zamknąć wszystkie strony, dopóki nie zostanie
7. Wróć do strony głównej **ADFTutorialOnPremDF**.
8. obowiązkowe Kliknij pozycję **potoki**, kliknij pozycję **ADFTutorialOnPremDF**, a następnie przejdź do szczegółów w tabeli wejściowej (**zużyty**) lub wyjściowe zestawy danych (**utworzone**).
9. Użyj narzędzi, takich jak [Microsoft Eksplorator usługi Storage](https://storageexplorer.com/) , aby sprawdzić, czy obiekt BLOB/plik jest tworzony przez każdą godzinę.

   ![Eksplorator usługi Azure Storage](./media/data-factory-move-data-between-onprem-and-cloud/OnPremAzureStorageExplorer.png)

## <a name="next-steps"></a>Następne kroki
* Aby uzyskać szczegółowe informacje o bramie Zarządzanie danymi, zobacz artykuł dotyczący [bramy usługi Zarządzanie danymi](data-factory-data-management-gateway.md) .
* Zobacz [Kopiowanie danych z usługi Azure BLOB do usługi Azure SQL](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) , aby dowiedzieć się, jak za pomocą działania kopiowania przenieść dane z magazynu danych źródłowych do magazynu danych ujścia.
