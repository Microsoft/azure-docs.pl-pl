---
title: Tworzenie potoków danych przy użyciu zestawu Azure .NET SDK
description: Dowiedz się, jak programowo tworzyć fabryki danych platformy Azure, monitorować je i zarządzać nimi za pomocą zestawu SDK Data Factory.
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/22/2018
ms.custom: devx-track-csharp
ms.openlocfilehash: 1b7f5d1ad671b6d97bd6597386c714b026c02232
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100376857"
---
# <a name="create-monitor-and-manage-azure-data-factories-using-azure-data-factory-net-sdk"></a>Tworzenie i monitorowanie fabryk danych platformy Azure oraz zarządzanie nimi przy użyciu zestawu Azure Data Factory .NET SDK
> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli korzystasz z bieżącej wersji usługi Data Factory, zobacz [samouczek dotyczący działania kopiowania](../quickstart-create-data-factory-dot-net.md). 

## <a name="overview"></a>Omówienie
Fabryki danych platformy Azure można tworzyć, monitorować programowo i zarządzać nimi za pomocą Data Factory .NET SDK. Ten artykuł zawiera przewodnik, który można wykonać w celu utworzenia przykładowej aplikacji konsolowej .NET, która tworzy i monitoruje fabrykę danych. 

> [!NOTE]
> Ten artykuł nie obejmuje całego interfejsu API .NET usługi Data Factory. Aby uzyskać kompleksową dokumentację interfejsu API platformy .NET dla Data Factory, Data Factory Zobacz Dokumentacja [interfejsu API platformy .NET](/dotnet/api/overview/azure/data-factory) . 

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

* Program Visual Studio w wersji 2012, 2013 lub 2015.
* Pobierz i zainstaluj zestaw [Azure .NET SDK](https://azure.microsoft.com/downloads/).
* Azure PowerShell. Postępuj zgodnie z instrukcjami w artykule [How to install and configure Azure PowerShell](/powershell/azure/) (Instalowanie i konfigurowanie programu Azure PowerShell), aby zainstalować program Azure PowerShell na komputerze. Program Azure PowerShell służy do tworzenia aplikacji Azure Active Directory.

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
    $azureAdApplication = New-AzADApplication -DisplayName "ADFDotNetWalkthroughApp" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.adfdotnetwalkthroughapp.org/example" -Password "Pass@word1"
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
W tym przewodniku utworzysz fabrykę danych z potokiem zawierającym działanie kopiowania. Działanie kopiowania kopiuje dane z folderu w usłudze Azure Blob Storage do innego folderu w tym samym magazynie obiektów BLOB. 

Działanie kopiowania wykonuje operację przenoszenia danych w usłudze Azure Data Factory. Działanie jest obsługiwane przez globalnie dostępną usługę, która może kopiować dane między różnymi magazynami danych w sposób bezpieczny, niezawodny i skalowalny. Szczegółowe informacje dotyczące działania kopiowania znajdują się w artykule [Data Movement Activities](data-factory-data-movement-activities.md) (Działania przenoszenia danych).

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
4. Zastąp zawartość pliku **App.config** w projekcie następującą zawartością: 
    
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
5. W pliku App.Config zaktualizuj wartości dla **&lt; identyfikatora &gt; aplikacji**, **&lt; hasła &gt;**, **&lt; identyfikatora &gt; subskrypcji** i **&lt; identyfikatora &gt; dzierżawy** przy użyciu własnych wartości.
6. Dodaj następujące instrukcje **using** do pliku **program. cs** w projekcie.

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

    //IMPORTANT: specify the name of Azure resource group here
    string resourceGroupName = "ADFTutorialResourceGroup";

    //IMPORTANT: the name of the data factory must be globally unique.
    // Therefore, update this value. For example:APITutorialFactory05122017
    string dataFactoryName = "APITutorialFactory";

    TokenCloudCredentials aadTokenCredentials = new TokenCloudCredentials(
            ConfigurationManager.AppSettings["SubscriptionId"],
            GetAuthorizationHeader().Result);

    Uri resourceManagerUri = new Uri(ConfigurationManager.AppSettings["ResourceManagerEndpoint"]);

    DataFactoryManagementClient client = new DataFactoryManagementClient(aadTokenCredentials, resourceManagerUri);
    ```

   > [!IMPORTANT]
   > Zastąp wartość **resourceGroupName** nazwą grupy zasobów platformy Azure. Grupę zasobów można utworzyć za pomocą polecenia cmdlet [New-AzureResourceGroup](/powershell/module/az.resources/new-azresourcegroup) .
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
9. Dodaj następujący kod, który tworzy **wejściowe i wyjściowe zestawy danych**, do metody **Main**.

    **FolderPath** dla wejściowego obiektu BLOB jest ustawiony na **adftutorial/** Where **adftutorial** jest nazwą kontenera w magazynie obiektów BLOB. Jeśli ten kontener nie istnieje w magazynie obiektów blob platformy Azure, Utwórz kontener o tej nazwie: **adftutorial** i Przekaż plik tekstowy do kontenera.

    FolderPath dla wyjściowego obiektu BLOB jest ustawiony na: **adftutorial/apifactoryoutput/{Slice}** , gdzie **wycinek** jest dynamicznie obliczany na podstawie wartości **parametru slicestart** (Data rozpoczęcia-czas każdego wycinka).

    ```csharp
    // create input and output datasets
    Console.WriteLine("Creating input and output datasets");
    string Dataset_Source = "DatasetBlobSource";
    string Dataset_Destination = "DatasetBlobDestination";
    
    client.Datasets.CreateOrUpdate(resourceGroupName, dataFactoryName,
    new DatasetCreateOrUpdateParameters()
    {
        Dataset = new Dataset()
        {
            Name = Dataset_Source,
            Properties = new DatasetProperties()
            {
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
    
    client.Datasets.CreateOrUpdate(resourceGroupName, dataFactoryName,
    new DatasetCreateOrUpdateParameters()
    {
        Dataset = new Dataset()
        {
            Name = Dataset_Destination,
            Properties = new DatasetProperties()
            {
    
                LinkedServiceName = "AzureStorageLinkedService",
                TypeProperties = new AzureBlobDataset()
                {
                    FolderPath = "adftutorial/apifactoryoutput/{Slice}",
                    PartitionedBy = new Collection<Partition>()
                    {
                        new Partition()
                        {
                            Name = "Slice",
                            Value = new DateTimePartitionValue()
                            {
                                Date = "SliceStart",
                                Format = "yyyyMMdd-HH"
                            }
                        }
                    }
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
10. Dodaj następujący kod, który **tworzy i aktywuje potok**, do metody **Main**. Ten potok zawiera działanie **CopyActivity**, które pobiera element **BlobSource** jako źródło i **BlobSink** jako ujście.

    Działanie kopiowania wykonuje operację przenoszenia danych w usłudze Azure Data Factory. Działanie jest obsługiwane przez globalnie dostępną usługę, która może kopiować dane między różnymi magazynami danych w sposób bezpieczny, niezawodny i skalowalny. Szczegółowe informacje dotyczące działania kopiowania znajdują się w artykule [Data Movement Activities](data-factory-data-movement-activities.md) (Działania przenoszenia danych).

    ```csharp
    // create a pipeline
    Console.WriteLine("Creating a pipeline");
    DateTime PipelineActivePeriodStartTime = new DateTime(2014, 8, 9, 0, 0, 0, 0, DateTimeKind.Utc);
    DateTime PipelineActivePeriodEndTime = PipelineActivePeriodStartTime.AddMinutes(60);
    string PipelineName = "PipelineBlobSample";
    
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
                        Name = "BlobToBlob",
                        Inputs = new List<ActivityInput>()
                        {
                            new ActivityInput()
                {
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
    
                },
            }
        }
    });
    ```
12. Dodaj następujący kod do metody **Main**, aby pobrać stan wycinka danych zestawu danych wyjściowych. W tym przykładzie jest oczekiwany tylko jeden wycinek.

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
13. **(opcjonalnie)** Dodaj następujący kod, aby uzyskać szczegóły uruchomienia wycinka danych do metody **Main** .

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
        });
    
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
14. Dodaj następującą metodę pomocnika używaną przez metodę **Main** do klasy **Program**. Ta metoda oznacza okno dialogowe, w którym można podać **nazwę użytkownika** i **hasło** , których używasz do logowania się do Azure Portal.

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

15. W Eksplorator rozwiązań rozwiń projekt: **DataFactoryAPITestApp**, kliknij prawym przyciskiem myszy pozycję **odwołania** i kliknij polecenie **Dodaj odwołanie**. Zaznacz pole wyboru dla `System.Configuration` zestawu i kliknij przycisk **OK**.
15. Skompiluj aplikację konsolową. Kliknij przycisk **Kompiluj** w menu i kliknij opcję **Kompiluj rozwiązanie**.
16. Potwierdź, że istnieje co najmniej jeden plik w kontenerze adftutorial w magazynie obiektów blob platformy Azure. W przeciwnym razie utwórz w Notatniku plik Emp.txt o następującej treści i przekaż go do kontenera adftutorial.

    ```
    John, Doe
    Jane, Doe
    ```
17. Uruchom przykład, klikając polecenie **Debuguj**  ->  **Rozpocznij debugowanie** w menu. Po wyświetleniu komunikatu **Pobieranie szczegółów uruchomienia wycinka danych** zaczekaj kilka minut, a następnie naciśnij klawisz **ENTER**.
18. Użyj witryny Azure Portal, aby upewnić się, że fabryka danych **APITutorialFactory** została utworzona z następującymi artefaktami:
    * Połączona usługa: **AzureStorageLinkedService**
    * Zestaw danych: **DatasetBlobSource** i **DatasetBlobDestination**.
    * Potok: **PipelineBlobSample**
19. Sprawdź, czy plik wyjściowy jest tworzony w folderze **apifactoryoutput** w kontenerze **adftutorial** .

## <a name="get-a-list-of-failed-data-slices"></a>Pobierz listę wycinków danych zakończonych niepowodzeniem 

```csharp
// Parse the resource path
var ResourceGroupName = "ADFTutorialResourceGroup";
var DataFactoryName = "DataFactoryAPITestApp";

var parameters = new ActivityWindowsByDataFactoryListParameters(ResourceGroupName, DataFactoryName);
parameters.WindowState = "Failed";
var response = dataFactoryManagementClient.ActivityWindows.List(parameters);
do
{
    foreach (var activityWindow in response.ActivityWindowListResponseValue.ActivityWindows)
    {
        var row = string.Join(
            "\t",
            activityWindow.WindowStart.ToString(),
            activityWindow.WindowEnd.ToString(),
            activityWindow.RunStart.ToString(),
            activityWindow.RunEnd.ToString(),
            activityWindow.DataFactoryName,
            activityWindow.PipelineName,
            activityWindow.ActivityName,
            string.Join(",", activityWindow.OutputDatasets));
        Console.WriteLine(row);
    }

    if (response.NextLink != null)
    {
        response = dataFactoryManagementClient.ActivityWindows.ListNext(response.NextLink, parameters);
    }
    else
    {
        response = null;
    }
}
while (response != null);
```

## <a name="next-steps"></a>Następne kroki
Zapoznaj się z poniższym przykładem tworzenia potoku przy użyciu zestawu .NET SDK, który kopiuje dane z usługi Azure Blob Storage do Azure SQL Database: 

- [Tworzenie potoku w celu skopiowania danych z Blob Storage do SQL Database](data-factory-copy-activity-tutorial-using-dotnet-api.md)
