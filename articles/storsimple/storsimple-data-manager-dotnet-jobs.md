---
title: Korzystanie z zestawu SDK platformy .NET na potrzeby zadań Data Manager Microsoft Azure StorSimple
description: Dowiedz się, jak przekształcać dane urządzeń StorSimple za pomocą zestawu .NET SDK w ramach usługi StorSimple Data Manager.
author: alkohli
ms.service: storsimple
ms.topic: how-to
ms.date: 01/16/2018
ms.author: alkohli
ms.openlocfilehash: b18627d2806662d6d966af95d51873d5623b2393
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96015540"
---
# <a name="use-the-net-sdk-to-initiate-data-transformation"></a>Inicjowanie transformacji danych przy użyciu zestawu SDK platformy .NET

## <a name="overview"></a>Omówienie

W tym artykule wyjaśniono, jak można użyć funkcji przekształcania danych w ramach usługi StorSimple Data Manager do przekształcania danych urządzenia StorSimple. Przekształcone dane są następnie zużywane przez inne usługi platformy Azure w chmurze.

Zadanie przekształcania danych można uruchomić na dwa sposoby:

- Korzystanie z zestawu SDK dla platformy .NET
- Użyj Azure Automation elementu Runbook
 
  W tym artykule szczegółowo opisano sposób tworzenia przykładowej aplikacji konsolowej platformy .NET w celu zainicjowania zadania transformacji danych, a następnie śledzenia jej pod kątem ukończenia. Aby dowiedzieć się więcej o tym, jak zainicjować transformację danych za pośrednictwem automatyzacji, przejdź do pozycji [użyj Azure Automation elementu Runbook, aby wyzwolić zadania przekształcania danych](storsimple-data-manager-job-using-automation.md).

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że masz:
*   Komputer z systemem:

    - Program Visual Studio 2012, 2013, 2015 lub 2017.

    - Program Azure PowerShell. [Pobierz program Azure PowerShell](/powershell/azure/).
*   Prawidłowo skonfigurowana Definicja zadania w StorSimple Data Manager w grupie zasobów.
*   Wszystkie wymagane biblioteki DLL. Pobierz te biblioteki DLL z [repozytorium GitHub](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/tree/master/Data_Manager_Job_Run/dlls).
*   [`Get-ConfigurationParams.ps1`](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/blob/master/Data_Manager_Job_Run/Get-ConfigurationParams.ps1) skrypt z repozytorium GitHub.

## <a name="step-by-step-procedure"></a>Procedura krok po kroku

Wykonaj następujące kroki, aby użyć programu .NET do uruchomienia zadania transformacji danych.

1. Aby pobrać parametry konfiguracji, wykonaj następujące czynności:
    1. Pobierz go `Get-ConfigurationParams.ps1` ze skryptu repozytorium GitHub w `C:\DataTransformation` lokalizacji.
    1. Uruchom `Get-ConfigurationParams.ps1` skrypt z repozytorium GitHub. Wpisz następujące polecenie:

        ```
        C:\DataTransformation\Get-ConfigurationParams.ps1 -SubscriptionName "AzureSubscriptionName" -ActiveDirectoryKey "AnyRandomPassword" -AppName "ApplicationName"
         ```
        Można przekazać wszystkie wartości dla ActiveDirectoryKey i nazwa_aplikacji.

2. Ten skrypt wyprowadza następujące wartości:
    * Identyfikator klienta
    * Identyfikator dzierżawy
    * Klucz Active Directory (taki sam jak podany powyżej)
    * Identyfikator subskrypcji

        ![Parametry konfiguracji dane wyjściowe skryptu](media/storsimple-data-manager-dotnet-jobs/get-config-parameters.png)

3. Korzystając z programu Visual Studio 2012, 2013 lub 2015, Utwórz aplikację konsolową .NET C#.

    1. Uruchom **program Visual Studio 2012/2013/2015**.
    1. Wybierz pozycję **Plik > Nowy > Projekt**.

        ![Tworzenie projektu 1](media/storsimple-data-manager-dotnet-jobs/create-new-project-7.png)        
    2. Wybierz pozycję **zainstalowane > szablony > aplikacji konsolowej > Visual C#**.
    3. Wprowadź **DataTransformationApp** dla **nazwy**.
    4. Dla **lokalizacji** wybierz pozycję **C:\DataTransformation** .
    6. Kliknij przycisk **OK**, aby utworzyć projekt.

        ![Tworzenie projektu 2](media/storsimple-data-manager-dotnet-jobs/create-new-project-1.png)

4. Teraz Dodaj wszystkie biblioteki DLL znajdujące się w [folderze dll](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/tree/master/Data_Manager_Job_Run/dlls) jako **odwołania** w projekcie, który został utworzony. Aby dodać pliki dll, wykonaj następujące czynności:

   1. W programie Visual Studio przejdź do **widoku > Eksplorator rozwiązań**.
   2. Kliknij strzałkę po lewej stronie projektu przekształcania danych aplikacji. Kliknij pozycję **odwołania** , a następnie kliknij prawym przyciskiem myszy, aby **dodać odwołanie**.
    
       ![Dodawanie bibliotek DLL 1](media/storsimple-data-manager-dotnet-jobs/create-new-project-4.png)

   3. Przejdź do lokalizacji folderu Packages, zaznacz wszystkie biblioteki DLL i kliknij przycisk **Dodaj**, a następnie kliknij przycisk **OK**.

       ![Dodawanie bibliotek DLL 2](media/storsimple-data-manager-dotnet-jobs/create-new-project-6.png)

5. Dodaj następujące instrukcje **using** do pliku źródłowego (Program.cs) w projekcie.

    ```
    using System;
    using System.Collections.Generic;
    using System.Threading;
    using Microsoft.Azure.Management.HybridData.Models;
    using Microsoft.Internal.Dms.DmsWebJob;
    using Microsoft.Internal.Dms.DmsWebJob.Contracts;
    ```
    
6. Poniższy kod Inicjuje wystąpienie zadania transformacji danych. Dodaj tę wartość w **metodzie Main**. Zastąp wartości parametrów konfiguracyjnych jako pobrane wcześniej. Podłącz wartości **Nazwa grupy zasobów** i **resourceName**. **ResourceGroupName** jest skojarzona z StorSimple Data Manager, na którym skonfigurowano definicję zadania. **ResourceName** to nazwa usługi StorSimple Data Manager.

    ```
    // Setup the configuration parameters.
    var configParams = new ConfigurationParams
    {
        ClientId = "client-id",
        TenantId = "tenant-id",
        ActiveDirectoryKey = "active-directory-key",
        SubscriptionId = "subscription-id",
        ResourceGroupName = "resource-group-name",
        ResourceName = "resource-name"
    };

    // Initialize the Data Transformation Job instance.
    DataTransformationJob dataTransformationJob = new DataTransformationJob(configParams);
    ```
   
7. Określ parametry, dla których należy uruchomić definicję zadania

    ```
    string jobDefinitionName = "job-definition-name";

    DataTransformationInput dataTransformationInput = dataTransformationJob.GetJobDefinitionParameters(jobDefinitionName);
    ```

    ORAZ

    Jeśli chcesz zmienić parametry definicji zadania w czasie wykonywania, Dodaj następujący kod:

    ```
    string jobDefinitionName = "job-definition-name";
    // Must start with a '\'
    var rootDirectories = new List<string> {@"\root"};

    // Name of the volume on the StorSimple device.
    var volumeNames = new List<string> {"volume-name"};

    var dataTransformationInput = new DataTransformationInput
    {
        // If you require the latest existing backup to be picked else use TakeNow to trigger a new backup.
        BackupChoice = BackupChoice.UseExistingLatest.ToString(),
        // Name of the StorSimple device.
        DeviceName = "device-name",
        // Name of the container in Azure storage where the files will be placed after execution.
        ContainerName = "container-name",
        // File name filter (search pattern) to be applied on files under the root directory. * - Match all files.
        FileNameFilter = "*",
        // List of root directories.
        RootDirectories = rootDirectories,
        // Name of the volume on StorSimple device on which the relevant data is present. 
        VolumeNames = volumeNames
    };
    ```

8. Po zainicjowaniu Dodaj następujący kod, aby wyzwolić zadanie przekształcenia danych w definicji zadania. Podłącz odpowiednią **nazwę definicji zadania**.

    ```
    // Trigger a job, retrieve the jobId and the retry interval for polling.
    int retryAfter;
    string jobId = dataTransformationJob.RunJobAsync(jobDefinitionName, 
    dataTransformationInput, out retryAfter);
    Console.WriteLine("jobid: ", jobId);
    Console.ReadLine();

    ```
    Po wklejeniu kodu Skompiluj rozwiązanie. Oto zrzut ekranu przedstawiający fragment kodu, w którym można zainicjować wystąpienie zadania transformacji danych.

   ![Fragment kodu dotyczący zadania przekształcania danych](media/storsimple-data-manager-dotnet-jobs/start-dotnet-job-code-snippet-1.png)

9. To zadanie przekształca dane, które pasują do katalogu głównego i filtrów plików w woluminie StorSimple i umieszcza je w określonym kontenerze/udziale plików. Gdy plik zostanie przekształcony, zostanie dodany komunikat do kolejki magazynu (na tym samym koncie magazynu co kontener/udział plików) o takiej samej nazwie jak definicja zadania. Ten komunikat może służyć jako wyzwalacz, który inicjuje dalsze przetwarzanie pliku.

10. Po wyzwoleniu zadania można użyć poniższego kodu do śledzenia zadania do ukończenia. Dodanie tego kodu do uruchomienia zadania nie jest obowiązkowe.

    ```
    Job jobDetails = null;

    // Poll the job.
    do
    {
        jobDetails = dataTransformationJob.GetJob(jobDefinitionName, jobId);

        // Wait before polling for the status again.
        Thread.Sleep(TimeSpan.FromSeconds(retryAfter));

    } while (jobDetails.Status == JobStatus.InProgress);

    // Completion status of the job.
    Console.WriteLine("JobStatus: {0}", jobDetails.Status);
    
    // To hold the console before exiting.
    Console.Read();

    ```
    Oto zrzut ekranu przedstawiający cały przykładowy kod używany do wyzwalania zadania przy użyciu platformy .NET.

    ![Pełny fragment kodu służący do wyzwalania zadania programu .NET](media/storsimple-data-manager-dotnet-jobs/start-dotnet-job-code-snippet.png)

## <a name="next-steps"></a>Następne kroki

[Użyj StorSimple Data Manager interfejsu użytkownika do przekształcania danych](storsimple-data-manager-ui.md).