---
title: Programowe monitorowanie zadań Azure Stream Analytics i zarządzanie nimi
description: W tym artykule opisano sposób programowanego monitorowania Stream Analytics zadań utworzonych za pomocą interfejsów API REST, zestawu Azure SDK lub programu PowerShell.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 04/20/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: 9c746e409964b4625a4c9921d9f02ca0c9730a35
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93123450"
---
# <a name="programmatically-create-a-stream-analytics-job-monitor"></a>Programowe tworzenie Stream Analytics monitor zadań

W tym artykule przedstawiono sposób włączania monitorowania dla zadania Stream Analytics. Zadania Stream Analytics tworzone za pośrednictwem interfejsów API REST, zestawu Azure SDK lub programu PowerShell nie mają domyślnie włączonego monitorowania. Możesz włączyć ją ręcznie w Azure Portal, przechodząc do strony monitorowanie zadania i klikając przycisk Włącz. można też zautomatyzować ten proces, wykonując kroki opisane w tym artykule. Dane monitorowania zostaną wyświetlone w obszarze metryki Azure Portal dla zadania Stream Analytics.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tego procesu należy spełnić następujące wymagania wstępne:

* Visual Studio 2019 lub 2015
* [Zestaw Azure .NET SDK](https://azure.microsoft.com/downloads/) został pobrany i zainstalowany
* Istniejące zadanie Stream Analytics, które musi mieć włączone monitorowanie

## <a name="create-a-project"></a>Tworzenie projektu

1. Utwórz aplikację konsolową .NET programu Visual Studio w języku C#.
2. W konsoli Menedżera pakietów Uruchom następujące polecenia, aby zainstalować pakiety NuGet. Pierwszym z nich jest zestaw SDK platformy .NET do zarządzania Azure Stream Analytics. Drugim z nich jest zestaw Azure Monitor SDK, który zostanie użyty do włączenia monitorowania. Ostatnim z nich jest klient Azure Active Directory, który będzie używany do uwierzytelniania.
   
   ```powershell
   Install-Package Microsoft.Azure.Management.StreamAnalytics
   Install-Package Microsoft.Azure.Insights -Pre
   Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
   ```
3. Dodaj następującą sekcję appSettings do pliku App.config.
   
   ```csharp
   <appSettings>
     <!--CSM Prod related values-->
     <add key="ResourceGroupName" value="RESOURCE GROUP NAME" />
     <add key="JobName" value="YOUR JOB NAME" />
     <add key="StorageAccountName" value="YOUR STORAGE ACCOUNT"/>
     <add key="ActiveDirectoryEndpoint" value="https://login.microsoftonline.com/" />
     <add key="ResourceManagerEndpoint" value="https://management.azure.com/" />
     <add key="WindowsManagementUri" value="https://management.core.windows.net/" />
     <add key="AsaClientId" value="1950a258-227b-4e31-a9cf-717495945fc2" />
     <add key="RedirectUri" value="urn:ietf:wg:oauth:2.0:oob" />
     <add key="SubscriptionId" value="YOUR AZURE SUBSCRIPTION ID" />
     <add key="ActiveDirectoryTenantId" value="YOUR TENANT ID" />
   </appSettings>
   ```
   Zastąp wartości identyfikatorów *subskrypcji i* *ActiveDirectoryTenantId* identyfikatorem dzierżawy platformy Azure. Te wartości można uzyskać, uruchamiając następujące polecenie cmdlet programu PowerShell:
   
   ```powershell
   Get-AzureAccount
   ```
4. Dodaj następujące instrukcje using do pliku źródłowego (Program.cs) w projekcie.
   
   ```csharp
     using System;
     using System.Configuration;
     using System.Threading;
     using Microsoft.Azure;
     using Microsoft.Azure.Management.Insights;
     using Microsoft.Azure.Management.Insights.Models;
     using Microsoft.Azure.Management.StreamAnalytics;
     using Microsoft.Azure.Management.StreamAnalytics.Models;
     using Microsoft.IdentityModel.Clients.ActiveDirectory;
   ```
5. Dodaj metodę pomocnika uwierzytelniania.

   ```csharp   
   public static string GetAuthorizationHeader()
   {
      AuthenticationResult result = null;
      var thread = new Thread(() =>
      {
         try
         {
             var context = new AuthenticationContext(
                ConfigurationManager.AppSettings["ActiveDirectoryEndpoint"] +
                ConfigurationManager.AppSettings["ActiveDirectoryTenantId"]);
             result = context.AcquireToken(
                 resource: ConfigurationManager.AppSettings["WindowsManagementUri"],
                 clientId: ConfigurationManager.AppSettings["AsaClientId"],
                 redirectUri: new Uri(ConfigurationManager.AppSettings["RedirectUri"]),
                 promptBehavior: PromptBehavior.Always);
         }
         catch (Exception threadEx)
         {
             Console.WriteLine(threadEx.Message);
         }
     });

     thread.SetApartmentState(ApartmentState.STA);
     thread.Name = "AcquireTokenThread";
     thread.Start();
     thread.Join();
   
     if (result != null)
     {
         return result.AccessToken;
     }
         throw new InvalidOperationException("Failed to acquire token");
   }
   ```

## <a name="create-management-clients"></a>Tworzenie klientów zarządzania

Poniższy kod spowoduje skonfigurowanie niezbędnych zmiennych i klientów zarządzania.

   ```csharp
    string resourceGroupName = "<YOUR AZURE RESOURCE GROUP NAME>";
    string streamAnalyticsJobName = "<YOUR STREAM ANALYTICS JOB NAME>";

    // Get authentication token
    TokenCloudCredentials aadTokenCredentials =
        new TokenCloudCredentials(
            ConfigurationManager.AppSettings["SubscriptionId"],
            GetAuthorizationHeader());

    Uri resourceManagerUri = new
    Uri(ConfigurationManager.AppSettings["ResourceManagerEndpoint"]);

    // Create Stream Analytics and Insights management client
    StreamAnalyticsManagementClient streamAnalyticsClient = new
    StreamAnalyticsManagementClient(aadTokenCredentials, resourceManagerUri);
    InsightsManagementClient insightsClient = new
    InsightsManagementClient(aadTokenCredentials, resourceManagerUri);
   ```

## <a name="enable-monitoring-for-an-existing-stream-analytics-job"></a>Włącz monitorowanie istniejącego zadania Stream Analytics

Poniższy kod umożliwia monitorowanie **istniejącego** zadania Stream Analytics. Pierwsza część kodu wykonuje żądanie GET względem usługi Stream Analytics w celu pobrania informacji o konkretnym Stream Analytics zadania. Używa właściwości *ID* (pobranej z żądania GET) jako parametru metody Put w drugiej połowie kodu, co spowoduje wysłanie żądania Put do usługi Insights w celu włączenia monitorowania dla zadania Stream Analytics.

> [!WARNING]
> Jeśli wcześniej włączono monitorowanie dla innego zadania Stream Analytics za pośrednictwem Azure Portal lub programowo za pośrednictwem poniższego kodu, **zalecamy podanie tej samej nazwy konta magazynu, która została użyta w przypadku wcześniejszego włączenia monitorowania.**
> 
> Konto magazynu jest połączone z regionem, w którym utworzono zadanie Stream Analytics w programie, a nie w odróżnieniu od samego zadania.
> 
> Wszystkie zadania Stream Analytics (i wszystkie inne zasoby platformy Azure) w tym samym regionie współużytkują to konto magazynu do przechowywania danych monitorowania. Jeśli postanowisz inne konto magazynu, może to spowodować nieprzewidziane skutki uboczne monitorowania innych zadań Stream Analytics lub innych zasobów platformy Azure.
> 
> Nazwa konta magazynu używana do zamiany `<YOUR STORAGE ACCOUNT NAME>` w poniższym kodzie powinna być kontem magazynu, które znajduje się w tej samej subskrypcji, co zadanie Stream Analytics, na którym jest włączane monitorowanie.
> 
> 
>    ```csharp
>    // Get an existing Stream Analytics job
>     JobGetParameters jobGetParameters = new JobGetParameters()
>     {
>         PropertiesToExpand = "inputs,transformation,outputs"
>     };
>     JobGetResponse jobGetResponse = streamAnalyticsClient.StreamingJobs.Get(resourceGroupName, streamAnalyticsJobName, jobGetParameters);
>
>    // Enable monitoring
>    ServiceDiagnosticSettingsPutParameters insightPutParameters = new ServiceDiagnosticSettingsPutParameters()
>    {
>            Properties = new ServiceDiagnosticSettings()
>           {
>               StorageAccountName = "<YOUR STORAGE ACCOUNT NAME>"
>           }
>    };
>   insightsClient.ServiceDiagnosticSettingsOperations.Put(jobGetResponse.Job.Id, insightPutParameters);
>   ```


## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

Aby uzyskać dalszą pomoc, Wypróbuj naszą [stronę pytań firmy&Microsoft dotyczącą Azure Stream Analytics](/answers/topics/azure-stream-analytics.html).

## <a name="next-steps"></a>Następne kroki

* [Wprowadzenie do Azure Stream Analytics](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics (Rozpoczynanie pracy z usługą Azure Stream Analytics)](stream-analytics-real-time-fraud-detection.md)
* [Scale Azure Stream Analytics jobs (Skalowanie zadań usługi Azure Stream Analytics)](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Query Language Reference (Dokumentacja dotycząca języka zapytań usługi Azure Stream Analytics)](/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure Stream Analytics Management REST API Reference (Dokumentacja interfejsu API REST zarządzania usługą Azure Stream Analytics)](/rest/api/streamanalytics/)