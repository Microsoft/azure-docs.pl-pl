---
title: 'Samouczek: tworzenie potoku za pomocą działania kopiowania przy użyciu interfejsu API .NET '
description: Ten samouczek zawiera instrukcje tworzenia potoku usługi Azure Data Factory za pomocą działania kopiowania przy użyciu interfejsu API .NET.
author: linda33wj
ms.service: data-factory
ms.topic: tutorial
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 8c2baf4da4e7893b650e1f885fb66be835c4b736
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100377078"
---
# <a name="tutorial-create-a-pipeline-with-copy-activity-using-net-api"></a>Samouczek: tworzenie potoku za pomocą działania kopiowania przy użyciu interfejsu API .NET
> [!div class="op_single_selector"]
> * [Przegląd i wymagania wstępne](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Kreator kopiowania](data-factory-copy-data-wizard-tutorial.md)
> * [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
> * [Program PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
> * [Szablon usługi Azure Resource Manager](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
> * [Interfejs API REST](data-factory-copy-activity-tutorial-using-rest-api.md)
> * [Interfejs API .NET](data-factory-copy-activity-tutorial-using-dotnet-api.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli korzystasz z bieżącej wersji usługi Data Factory, zobacz [samouczek dotyczący działania kopiowania](../quickstart-create-data-factory-dot-net.md). 

W tym artykule dowiesz się, jak używać [interfejsu API platformy .NET](https://portal.azure.com) do tworzenia fabryki danych z potokiem, który kopiuje dane z usługi Azure Blob storage do Azure SQL Database. Jeśli jesteś nowym użytkownikiem usługi Azure Data Factory, przed wykonaniem instrukcji z tego samouczka zapoznaj się z artykułem [Wprowadzenie do usługi Azure Data Factory](data-factory-introduction.md).   

W tym samouczku opisano tworzenie potoku z jednym działaniem (Działanie kopiowania). Działanie kopiowania kopiuje dane z obsługiwanego magazynu danych do obsługiwanego magazynu danych ujścia. Aby zapoznać się z listą magazynów danych obsługiwanych jako źródła i ujścia, zobacz [obsługiwane magazyny danych](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Działanie jest obsługiwane przez globalnie dostępną usługę, która może kopiować dane między różnymi magazynami danych w sposób bezpieczny, niezawodny i skalowalny. Więcej informacji o działaniu kopiowania znajduje się w temacie [działania dotyczące przenoszenia danych](data-factory-data-movement-activities.md).

Potok może obejmować więcej niż jedno działanie. Dwa działania można połączyć w łańcuch (uruchomić jedno działanie po drugim), ustawiając wyjściowy zestaw danych jednego działania jako zestaw wejściowy drugiego. Aby uzyskać więcej informacji, zobacz sekcję dotyczącą [wielu działań w potoku](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline). 

> [!NOTE] 
> Aby zapoznać się z pełną dokumentacją dotyczącą interfejsu API .NET usługi Data Factory, zobacz [dokumentację interfejsu API .NET usługi Data Factory](/dotnet/api/overview/azure/data-factory).
> 
> Potok danych przedstawiony w tym samouczku kopiuje dane ze źródłowego do docelowego magazynu danych. Aby zapoznać się z samouczkiem dotyczącym przekształcania danych za pomocą usługi Azure Data Factory, zobacz [Tutorial: Build a pipeline to transform data using Hadoop cluster](data-factory-build-your-first-pipeline.md) (Samouczek: Tworzenie potoku przekształcającego dane przy użyciu klastra Hadoop).

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

* Przejrzyj [omówienie samouczka i wymagania wstępne](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md), aby uzyskać omówienie samouczka i pełne kroki **wymagań wstępnych**.
* Program Visual Studio w wersji 2012, 2013 lub 2015.
* Pobierz i zainstaluj [zestaw Azure .NET SDK](https://azure.microsoft.com/downloads/)
* Azure PowerShell. Postępuj zgodnie z instrukcjami w artykule [How to install and configure Azure PowerShell](/powershell/azure/install-Az-ps) (Instalowanie i konfigurowanie programu Azure PowerShell), aby zainstalować program Azure PowerShell na komputerze. Program Azure PowerShell służy do tworzenia aplikacji Azure Active Directory.

### <a name="create-an-application-in-azure-active-directory"></a>Utworzenie aplikacji w usłudze Azure Active Directory
Utwórz aplikację usługi Azure Active Directory, utwórz nazwę główną usługi aplikacji i przypisz ją do roli **Współautor Data Factory**.

1. Uruchom program **PowerShell**.
2. Uruchom poniższe polecenie i wprowadź nazwę użytkownika oraz hasło, których używasz do logowania się w witrynie Azure Portal.

    ```powershell
    Connect-AzAccount
    ```
3. Uruchom poniższe polecenie, aby wyświetlić wszystkie subskrypcje dla tego konta.

    ```powershell
    Get-AzSubscription
    ```
4. Uruchom poniższe polecenie, aby wybrać subskrypcję, z którą chcesz pracować. Zastąp **&lt; ciąg nameofazuresubscription nazwą** &gt; nazwą swojej subskrypcji platformy Azure.

    ```powershell
    Get-AzSubscription -SubscriptionName <NameOfAzureSubscription> | Set-AzContext
    ```

   > [!IMPORTANT]
   > Zanotuj dane **SubscriptionId** i **TenantId** z danych wyjściowych tego polecenia.

5. Utwórz grupę zasobów platformy Azure o nazwie **ADFTutorialResourceGroup** przez uruchomienie następującego polecenia w programie PowerShell.

    ```powershell
    New-AzResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"
    ```

    Jeśli istnieje już grupa zasobów, można określić, czy należy ją zaktualizować (Y), czy zachować (N).

    Jeśli używasz innej grupy zasobów, podczas wykonywania instrukcji w tym samouczku trzeba będzie wstawić jej nazwę zamiast nazwy ADFTutorialResourceGroup.
6. Utwórz aplikację usługi Azure Active Directory.

    ```powershell
    $azureAdApplication = New-AzADApplication -DisplayName "ADFCopyTutotiralApp" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.adfcopytutorialapp.org/example" -Password "Pass@word1"
    ```

    Jeśli zostanie wyświetlony następujący błąd, określ inny adres URL i uruchom polecenie ponownie.
    
    ```powershell
    Another object with the same value for property identifierUris already exists.
    ```
7. Utwórz nazwę główną usługi AD.

    ```powershell
    New-AzADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId
    ```
8. Dodaj nazwę główną usługi do roli **Współautor Data Factory**.

    ```powershell
    New-AzRoleAssignment -RoleDefinitionName "Data Factory Contributor" -ServicePrincipalName $azureAdApplication.ApplicationId.Guid
    ```
9. Uzyskaj identyfikator aplikacji.

    ```powershell
    $azureAdApplication
    ```
    Zanotuj identyfikator aplikacji (applicationID) z danych wyjściowych.

Po wykonaniu tych kroków powinny być dostępne cztery następujące wartości:

* Identyfikator dzierżawy
* Identyfikator subskrypcji
* Identyfikator aplikacji
* Hasło (określone w pierwszym poleceniu)

## <a name="walkthrough"></a>Przewodnik
1. Za pomocą programu Visual Studio 2012/2013/2015 utwórz aplikację konsolową .NET C#.
   1. Uruchom program **Visual Studio** 2012/2013/2015.
   2. Kliknij pozycję **Plik**, wskaż polecenie **Nowy** i kliknij pozycję **Projekt**.
   3. Rozwiń węzeł **Szablony** i wybierz opcję **Visual C#**. W tym przewodniku stosowany jest język C#, ale można użyć dowolnego języka platformy .NET.
   4. Wybierz opcję **Aplikacja konsolowa** z listy typów projektów po prawej stronie.
   5. Wprowadź wartość **DataFactoryAPITestApp** jako nazwę.
   6. Wybierz ścieżkę **C:\ADFGetStarted** jako lokalizację.
   7. Kliknij przycisk **OK**, aby utworzyć projekt.
2. Kliknij pozycję **Narzędzia**, wskaż pozycję **Menedżer pakietów NuGet**, a następnie kliknij pozycję **Konsola menedżera pakietów**.
3. W **konsoli menedżera pakietów** wykonaj następujące czynności:
   1. Uruchom następujące polecenie, aby zainstalować pakiet Fabryka danych: `Install-Package Microsoft.Azure.Management.DataFactories`
   2. Uruchom następujące polecenie, aby zainstalować pakiet Azure Active Directory (użyjesz interfejsu API usługi Active Directory w kodzie): `Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.19.208020213`
4. Dodaj następującą sekcję **appSetttings** do pliku **App.config**. Te ustawienia są używane przez metodę pomocy **GetAuthorizationHeader**.

    Zastąp wartości dla **&lt; identyfikatora &gt; aplikacji**, **&lt; hasła &gt;**, **&lt; identyfikatora &gt; subskrypcji** i **&lt; identyfikatora &gt; dzierżawy** własnymi wartościami.

    ```xml
    <?xml version="1.0" encoding="utf-8" ?>
    <configuration>
        <appSettings>
            <add key="ActiveDirectoryEndpoint" value="https://login.microsoftonline.com/" />
            <add key="ResourceManagerEndpoint" value="https://management.azure.com/" />
            <add key="WindowsManagementUri" value="https://management.core.windows.net/" />

            <add key="ApplicationId" value="your application ID" />
            <add key="Password" value="Password you used while creating the AAD application" />
            <add key="SubscriptionId" value= "Subscription ID" />
            <add key="ActiveDirectoryTenantId" value="Tenant ID" />
        </appSettings>
    </configuration>
    ```

5. Dodaj następujące instrukcje **using** do pliku źródłowego (Program.cs) w projekcie.

    ```csharp
    using System.Configuration;
    using System.Collections.ObjectModel;
    using System.Threading;
    using System.Threading.Tasks;

    using Microsoft.Azure;
    using Microsoft.Azure.Management.DataFactories;
    using Microsoft.Azure.Management.DataFactories.Models;
    using Microsoft.Azure.Management.DataFactories.Common.Models;

    using Microsoft.IdentityModel.Clients.ActiveDirectory;

    ```

6. Dodaj następujący kod, który tworzy wystąpienie klasy **DataPipelineManagementClient** w metodzie **Main**. Ten obiekt jest używany do tworzenia fabryki danych, połączonej usługi, zestawów danych wejściowych i wyjściowych oraz potoku. Tego obiektu można również używać do monitorowania wycinków zestawu danych w czasie wykonywania.

    ```csharp
    // create data factory management client
    string resourceGroupName = "ADFTutorialResourceGroup";
    string dataFactoryName = "APITutorialFactory";

    TokenCloudCredentials aadTokenCredentials = new TokenCloudCredentials(
            ConfigurationManager.AppSettings["SubscriptionId"],
            GetAuthorizationHeader().Result);

    Uri resourceManagerUri = new Uri(ConfigurationManager.AppSettings["ResourceManagerEndpoint"]);

    DataFactoryManagementClient client = new DataFactoryManagementClient(aadTokenCredentials, resourceManagerUri);
    ```

   > [!IMPORTANT]
   > Zastąp wartość **resourceGroupName** nazwą grupy zasobów platformy Azure.
   >
   > Zaktualizuj nazwę fabryki danych (dataFactoryName), aby była unikatowa. Nazwa fabryki danych musi być globalnie unikatowa. Artykuł [Data Factory — Naming Rules](data-factory-naming-rules.md) (Fabryka danych — zasady nazewnictwa) zawiera zasady nazewnictwa artefaktów usługi Fabryka danych.

7. Dodaj następujący kod, który tworzy **fabrykę danych**, do metody **Main**.

    ```csharp
    // create a data factory
    Console.WriteLine("Creating a data factory");
    client.DataFactories.CreateOrUpdate(resourceGroupName,
        new DataFactoryCreateOrUpdateParameters()
        {
            DataFactory = new DataFactory()
            {
                Name = dataFactoryName,
                Location = "westus",
                Properties = new DataFactoryProperties()
            }
        }
    );
    ```

    Fabryka danych może obejmować jeden lub wiele potoków. Potok może obejmować jedno lub wiele działań. Na przykład działanie kopiowania może służyć do skopiowania danych ze źródła do docelowego magazynu danych, a działanie programu Hive w usłudze HDInsight do uruchomienia skryptu programu Hive, który przekształci dane wejściowe w dane wyjściowe produktu. Zacznijmy tworzenie fabryki danych w tym kroku.
8. Dodaj następujący kod, który tworzy **usługę połączoną usługi Azure Storage**, do metody **Main**.

   > [!IMPORTANT]
   > Zastąp wartości **storageaccountname** i **accountkey** nazwą konta usługi Azure Storage oraz jego kluczem.

    ```csharp
    // create a linked service for input data store: Azure Storage
    Console.WriteLine("Creating Azure Storage linked service");
    client.LinkedServices.CreateOrUpdate(resourceGroupName, dataFactoryName,
        new LinkedServiceCreateOrUpdateParameters()
        {
            LinkedService = new LinkedService()
            {
                Name = "AzureStorageLinkedService",
                Properties = new LinkedServiceProperties
                (
                    new AzureStorageLinkedService("DefaultEndpointsProtocol=https;AccountName=<storageaccountname>;AccountKey=<accountkey>")
                )
            }
        }
    );
    ```

    Połączone usługi tworzy się w fabryce danych w celu połączenia magazynów danych i usług obliczeniowych z fabryką danych. W tym samouczku nie przedstawiono korzystania z żadnych usług obliczeniowych, takich jak Azure HDInsight czy Azure Data Lake Analytics. Zostają użyte dwa magazyny danych typu Azure Storage (źródło) i Azure SQL Database (lokalizacja docelowa). 

    W związku z tym tworzy się dwie połączone usługi o nazwie AzureStorageLinkedService i AzureSqlLinkedService typu: AzureStorage i AzureSqlDatabase.  

    Polecenie AzureStorageLinkedService łączy konto usługi Azure Storage z fabryką danych. Na tym koncie magazynu utworzono kontener i przekazano na nie dane w ramach [wymagań wstępnych](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
9. Dodaj następujący kod, który tworzy **usługę połączoną Azure SQL**, do metody **Main**.

   > [!IMPORTANT]
   > Zastąp wartości **servername**, **DatabaseName**, **username** i **Password** nazwami serwera, bazy danych, użytkownika i hasła.

    ```csharp
    // create a linked service for output data store: Azure SQL Database
    Console.WriteLine("Creating Azure SQL Database linked service");
    client.LinkedServices.CreateOrUpdate(resourceGroupName, dataFactoryName,
        new LinkedServiceCreateOrUpdateParameters()
        {
            LinkedService = new LinkedService()
            {
                Name = "AzureSqlLinkedService",
                Properties = new LinkedServiceProperties
                (
                    new AzureSqlDatabaseLinkedService("Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;User ID=<username>;Password=<password>;Integrated Security=False;Encrypt=True;Connect Timeout=30")
                )
            }
        }
    );
    ```

    Linki AzureSqlLinkedService Azure SQL Database do fabryki danych. W tej bazie danych są przechowywane dane skopiowane z magazynu obiektów blob. Tabelę emp w tej bazie danych utworzono w ramach [wymagań wstępnych](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
10. Dodaj następujący kod, który tworzy **wejściowe i wyjściowe zestawy danych**, do metody **Main**.

    ```csharp
    // create input and output datasets
    Console.WriteLine("Creating input and output datasets");
    string Dataset_Source = "InputDataset";
    string Dataset_Destination = "OutputDataset";

    Console.WriteLine("Creating input dataset of type: Azure Blob");
    client.Datasets.CreateOrUpdate(resourceGroupName, dataFactoryName,

    new DatasetCreateOrUpdateParameters()
    {
        Dataset = new Dataset()
        {
            Name = Dataset_Source,
            Properties = new DatasetProperties()
            {
                Structure = new List<DataElement>()
                {
                    new DataElement() { Name = "FirstName", Type = "String" },
                    new DataElement() { Name = "LastName", Type = "String" }
                },
                LinkedServiceName = "AzureStorageLinkedService",
                TypeProperties = new AzureBlobDataset()
                {
                    FolderPath = "adftutorial/",
                    FileName = "emp.txt"
                },
                External = true,
                Availability = new Availability()
                {
                    Frequency = SchedulePeriod.Hour,
                    Interval = 1,
                },

                Policy = new Policy()
                {
                    Validation = new ValidationPolicy()
                    {
                        MinimumRows = 1
                    }
                }
            }
        }
    });

    Console.WriteLine("Creating output dataset of type: Azure SQL");
    client.Datasets.CreateOrUpdate(resourceGroupName, dataFactoryName,
        new DatasetCreateOrUpdateParameters()
        {
            Dataset = new Dataset()
            {
                Name = Dataset_Destination,
                Properties = new DatasetProperties()
                {
                    Structure = new List<DataElement>()
                    {
                        new DataElement() { Name = "FirstName", Type = "String" },
                        new DataElement() { Name = "LastName", Type = "String" }
                    },
                    LinkedServiceName = "AzureSqlLinkedService",
                    TypeProperties = new AzureSqlTableDataset()
                    {
                        TableName = "emp"
                    },
                    Availability = new Availability()
                    {
                        Frequency = SchedulePeriod.Hour,
                        Interval = 1,
                    },
                }
            }
        });
    ```
    
    W poprzednim kroku zostały utworzone połączone usługi, aby połączyć konto usługi Azure Storage i Azure SQL Database z fabryką danych. W tym kroku zostaną zdefiniowane dwa zestawy danych o nazwach InputDataset i OutputDataset zawierające dane wejściowe i wyjściowe przechowywane w magazynach danych, do których odwołują się usługi AzureStorageLinkedService i AzureSqlLinkedService.

    Połączona usługa magazynu Azure określa parametry połączenia, z których korzysta usługa Data Factory w czasie wykonywania, aby połączyć się z kontem magazynu Azure. Natomiast wejściowy zestaw danych obiektów blob (InputDataset) określa kontener oraz folder, który zawiera dane wejściowe.  

    Analogicznie, Azure SQL Database połączona usługa określa parametry połączenia, których usługa Data Factory używa w czasie wykonywania w celu nawiązania połączenia z bazą danych. Wyjściowy zestaw danych tabeli SQL (OutputDataset) określa tabelę w bazie danych, do której są kopiowane dane z magazynu obiektów blob.

    W tym kroku opisano tworzenie zestawu danych o nazwie InputDataset wskazującego na plik obiektów blob (emp.txt) w katalogu głównym kontenera obiektów blob (adftutorial) w usłudze Azure Storage reprezentowany przez połączoną usługę AzureStorageLinkedService. Jeśli nie określisz wartości obiektu fileName lub ją pominiesz, dane ze wszystkich obiektów blob w folderze wejściowym zostaną skopiowane do lokalizacji docelowej. W tym samouczku wartość obiektu fileName jest określona.    

    W tym kroku tworzony jest wyjściowy zestaw danych o nazwie **OutputDataset**. Ten zestaw danych wskazuje tabelę SQL w bazie danych reprezentowaną przez **AzureSqlLinkedService**.
11. Dodaj następujący kod, który **tworzy i aktywuje potok**, do metody **Main**. W tym kroku opisano tworzenie potoku za pomocą **działania kopiowania**, w którym parametr **InputDataset** jest używany jako dane wejściowe, a parametr **OutputDataset** jako dane wyjściowe.

    ```csharp
    // create a pipeline
    Console.WriteLine("Creating a pipeline");
    DateTime PipelineActivePeriodStartTime = new DateTime(2017, 5, 11, 0, 0, 0, 0, DateTimeKind.Utc);
    DateTime PipelineActivePeriodEndTime = new DateTime(2017, 5, 12, 0, 0, 0, 0, DateTimeKind.Utc);
    string PipelineName = "ADFTutorialPipeline";

    client.Pipelines.CreateOrUpdate(resourceGroupName, dataFactoryName,
        new PipelineCreateOrUpdateParameters()
        {
            Pipeline = new Pipeline()
            {
                Name = PipelineName,
                Properties = new PipelineProperties()
                {
                    Description = "Demo Pipeline for data transfer between blobs",

                    // Initial value for pipeline's active period. With this, you won't need to set slice status
                    Start = PipelineActivePeriodStartTime,
                    End = PipelineActivePeriodEndTime,

                    Activities = new List<Activity>()
                    {
                        new Activity()
                        {
                            Name = "BlobToAzureSql",
                            Inputs = new List<ActivityInput>()
                            {
                                new ActivityInput() {
                                    Name = Dataset_Source
                                }
                            },
                            Outputs = new List<ActivityOutput>()
                            {
                                new ActivityOutput()
                                {
                                    Name = Dataset_Destination
                                }
                            },
                            TypeProperties = new CopyActivity()
                            {
                                Source = new BlobSource(),
                                Sink = new BlobSink()
                                {
                                    WriteBatchSize = 10000,
                                    WriteBatchTimeout = TimeSpan.FromMinutes(10)
                                }
                            }
                        }
                    }
                }
            }
        });
    ```

    Pamiętaj o następujących kwestiach:
   
    - W sekcji działań jest tylko jedno działanie, którego parametr **type** (typ) został ustawiony na wartość **Copy**. Więcej informacji o działaniu kopiowania znajduje się w artykule dotyczącym [działań związanych z przenoszeniem danych](data-factory-data-movement-activities.md). W rozwiązaniach usługi Data Factory można również użyć [działań dotyczących przekształcania danych](data-factory-data-transformation-activities.md).
    - Dane wejściowe dla działania mają ustawienie **InputDataset**, a dane wyjściowe — **OutputDataset**. 
    - W sekcji **typeProperties** parametr **BlobSource** został określony jako typ źródłowy, a parametr **SqlSink** został określony jako typ ujścia. Aby uzyskać pełną listę magazynów danych obsługiwanych przez działanie kopiowania jako źródła i ujścia, zobacz informacje dotyczące [obsługiwanych magazynów danych](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Aby dowiedzieć się, jak używać określonego obsługiwanego magazynu danych jako źródła/ujścia, kliknij link w tabeli.  
   
    Obecnie harmonogram jest prowadzony przy użyciu wyjściowego zestawu danych. W tym samouczku wyjściowy zestaw danych jest konfigurowany do tworzenia wycinka co godzinę. Potok ma godzinę rozpoczęcia i zakończenia, między którymi następuje jeden dzień różnicy (dokładnie 24 godziny). Potok tworzy więc 24 wycinki wyjściowego zestawu danych.
12. Dodaj następujący kod do metody **Main**, aby pobrać stan wycinka danych zestawu danych wyjściowych. W tym przykładzie oczekiwany jest tylko wycinek.

    ```csharp
    // Pulling status within a timeout threshold
    DateTime start = DateTime.Now;
    bool done = false;

    while (DateTime.Now - start < TimeSpan.FromMinutes(5) && !done)
    {
        Console.WriteLine("Pulling the slice status");        
        // wait before the next status check
        Thread.Sleep(1000 * 12);

        var datalistResponse = client.DataSlices.List(resourceGroupName, dataFactoryName, Dataset_Destination,
            new DataSliceListParameters()
            {
                DataSliceRangeStartTime = PipelineActivePeriodStartTime.ConvertToISO8601DateTimeString(),
                DataSliceRangeEndTime = PipelineActivePeriodEndTime.ConvertToISO8601DateTimeString()
            });

        foreach (DataSlice slice in datalistResponse.DataSlices)
        {
            if (slice.State == DataSliceState.Failed || slice.State == DataSliceState.Ready)
            {
                Console.WriteLine("Slice execution is done with status: {0}", slice.State);
                done = true;
                break;
            }
            else
            {
                Console.WriteLine("Slice status is: {0}", slice.State);
            }
        }
    }
    ```

13. Dodaj następujący kod do metody **Main**, aby uzyskać szczegóły uruchomienia dla wycinka danych.

    ```csharp
    Console.WriteLine("Getting run details of a data slice");

    // give it a few minutes for the output slice to be ready
    Console.WriteLine("\nGive it a few minutes for the output slice to be ready and press any key.");
    Console.ReadKey();

    var datasliceRunListResponse = client.DataSliceRuns.List(
            resourceGroupName,
            dataFactoryName,
            Dataset_Destination,
            new DataSliceRunListParameters()
            {
                DataSliceStartTime = PipelineActivePeriodStartTime.ConvertToISO8601DateTimeString()
            }
        );

    foreach (DataSliceRun run in datasliceRunListResponse.DataSliceRuns)
    {
        Console.WriteLine("Status: \t\t{0}", run.Status);
        Console.WriteLine("DataSliceStart: \t{0}", run.DataSliceStart);
        Console.WriteLine("DataSliceEnd: \t\t{0}", run.DataSliceEnd);
        Console.WriteLine("ActivityId: \t\t{0}", run.ActivityName);
        Console.WriteLine("ProcessingStartTime: \t{0}", run.ProcessingStartTime);
        Console.WriteLine("ProcessingEndTime: \t{0}", run.ProcessingEndTime);
        Console.WriteLine("ErrorMessage: \t{0}", run.ErrorMessage);
    }

    Console.WriteLine("\nPress any key to exit.");
    Console.ReadKey();
    ```

14. Dodaj następującą metodę pomocnika używaną przez metodę **Main** do klasy **Program**.

    > [!NOTE] 
    > Podczas kopiowania i wklejania następującego kodu, upewnij się, że skopiowany kod jest na tym samym poziomie co metoda główna.

    ```csharp
    public static async Task<string> GetAuthorizationHeader()
    {
        AuthenticationContext context = new AuthenticationContext(ConfigurationManager.AppSettings["ActiveDirectoryEndpoint"] + ConfigurationManager.AppSettings["ActiveDirectoryTenantId"]);
        ClientCredential credential = new ClientCredential(
            ConfigurationManager.AppSettings["ApplicationId"],
            ConfigurationManager.AppSettings["Password"]);
        AuthenticationResult result = await context.AcquireTokenAsync(
            resource: ConfigurationManager.AppSettings["WindowsManagementUri"],
            clientCredential: credential);

        if (result != null)
            return result.AccessToken;

        throw new InvalidOperationException("Failed to acquire token");
    }
    ```

15. W Eksploratorze rozwiązań rozwiń projekt (DataFactoryAPITestApp), kliknij prawym przyciskiem myszy pozycję **Odwołania** i kliknij polecenie **Dodaj odwołanie**. Zaznacz pole wyboru zestawu **System.Configuration**. i kliknij przycisk **OK**.
16. Skompiluj aplikację konsolową. Kliknij przycisk **Kompiluj** w menu i kliknij opcję **Kompiluj rozwiązanie**.
17. Potwierdź, że istnieje co najmniej jeden plik w kontenerze **adftutorial** w magazynie obiektów blob platformy Azure. W przeciwnym razie utwórz w Notatniku plik **Emp.txt** o następującej treści i przekaż go do kontenera adftutorial.

    ```
    John, Doe
    Jane, Doe
    ```
18. Uruchom przykład, klikając polecenie **Debuguj**  ->  **Rozpocznij debugowanie** w menu. Po wyświetleniu komunikatu **Pobieranie szczegółów uruchomienia wycinka danych** zaczekaj kilka minut, a następnie naciśnij klawisz **ENTER**.
19. Użyj witryny Azure Portal, aby upewnić się, że fabryka danych **APITutorialFactory** została utworzona z następującymi artefaktami:
    * Połączona usługa: **LinkedService_AzureStorage**
    * Zestaw danych: **InputDataset** i **OutputDataset**.
    * Potok: **PipelineBlobSample**
20. Sprawdź, czy w tabeli **EMP** w określonej bazie danych są tworzone dwa rekordy pracowników.

## <a name="next-steps"></a>Następne kroki
Aby zapoznać się z pełną dokumentacją dotyczącą interfejsu API .NET usługi Data Factory, zobacz [dokumentację interfejsu API .NET usługi Data Factory](/dotnet/api/overview/azure/data-factory).

W tym samouczku użyto magazynu obiektów blob platformy Azure jako źródłowego magazynu danych i Azure SQL Database jako docelowy magazyn danych w operacji kopiowania. Poniższa tabela zawiera listę magazynów danych obsługiwanych przez działanie kopiowania jako źródła i lokalizacje docelowe: 

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

Aby uzyskać informacje dotyczące kopiowania danych do/z magazynu danych, kliknij link do magazynu danych w tabeli.

