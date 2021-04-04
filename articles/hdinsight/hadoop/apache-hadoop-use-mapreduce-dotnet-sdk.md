---
title: Przesyłanie zadań MapReduce przy użyciu zestawu .NET SDK usługi HDInsight — Azure
description: Dowiedz się, jak przesyłać zadania MapReduce do usługi Azure HDInsight Apache Hadoop przy użyciu zestawu .NET SDK w usłudze HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive, devx-track-csharp
ms.date: 01/15/2020
ms.openlocfilehash: 8fbcb66be11c7c77a9bfaf0e6ec790622dcbbda7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98932175"
---
# <a name="run-mapreduce-jobs-using-hdinsight-net-sdk"></a>Uruchamianie zadania MapReduce przy użyciu zestawu SDK dla platformy .NET usługi HDInsight

[!INCLUDE [mapreduce-selector](../../../includes/hdinsight-selector-use-mapreduce.md)]

Dowiedz się, jak przesyłać zadania MapReduce przy użyciu zestawu .NET SDK usługi HDInsight. Klastry usługi HDInsight są dostarczane z plikiem jar z niektórymi przykładami MapReduce. Plik JAR ma wartość `/example/jars/hadoop-mapreduce-examples.jar` .  Jednym z przykładów jest **WORDCOUNT**. Tworzysz aplikację konsolową w języku C# w celu przesłania zadania WORDCOUNT.  Zadanie odczytuje `/example/data/gutenberg/davinci.txt` plik i wyprowadza wyniki do `/example/data/davinciwordcount` .  Jeśli chcesz ponownie uruchomić aplikację, musisz wyczyścić folder wyjściowy.

> [!NOTE]  
> Kroki opisane w tym artykule należy wykonać z poziomu klienta systemu Windows. Aby uzyskać informacje na temat używania klienta z systemem Linux, OS X lub UNIX do pracy z usługą Hive, użyj selektora kart wyświetlanego w górnej części artykułu.

## <a name="prerequisites"></a>Wymagania wstępne

* Klaster Apache Hadoop w usłudze HDInsight. Zobacz [Tworzenie klastrów Apache Hadoop przy użyciu Azure Portal](../hdinsight-hadoop-create-linux-clusters-portal.md).

* [Program Visual Studio](https://visualstudio.microsoft.com/vs/community/).

## <a name="submit-mapreduce-jobs-using-hdinsight-net-sdk"></a>Przesyłanie zadań MapReduce przy użyciu zestawu .NET SDK usługi HDInsight

Zestaw SDK platformy .NET dla usługi HDInsight zawiera biblioteki klienckie platformy .NET, które ułatwiają pracę z klastrami usługi HDInsight z poziomu platformy .NET.

1. Uruchom program Visual Studio i Utwórz aplikację konsolową w języku C#.

1. Przejdź do **menu Narzędzia** Menedżer  >  **pakietów NuGet**  >  **konsola Menedżera pakietów** i wprowadź następujące polecenie:

    ```   
    Install-Package Microsoft.Azure.Management.HDInsight.Job
    ```

1. Skopiuj poniższy kod do **programu program. cs**. Następnie Edytuj kod, ustawiając wartości dla: `existingClusterName` ,,, `existingClusterPassword` `defaultStorageAccountName` `defaultStorageAccountKey` i `defaultStorageContainerName` .

    ```csharp
    using System.Collections.Generic;
    using System.IO;
    using System.Text;
    using System.Threading;
    using Microsoft.Azure.Management.HDInsight.Job;
    using Microsoft.Azure.Management.HDInsight.Job.Models;
    using Hyak.Common;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    
    namespace SubmitHDInsightJobDotNet
    {
        class Program
        {
            private static HDInsightJobManagementClient _hdiJobManagementClient;
    
            private const string existingClusterName = "<Your HDInsight Cluster Name>";
            private const string existingClusterPassword = "<Cluster User Password>";
            private const string defaultStorageAccountName = "<Default Storage Account Name>"; 
            private const string defaultStorageAccountKey = "<Default Storage Account Key>";
            private const string defaultStorageContainerName = "<Default Blob Container Name>";
    
            private const string existingClusterUsername = "admin";
            private const string existingClusterUri = existingClusterName + ".azurehdinsight.net";
            private const string sourceFile = "/example/data/gutenberg/davinci.txt";
            private const string outputFolder = "/example/data/davinciwordcount";
    
            static void Main(string[] args)
            {
                System.Console.WriteLine("The application is running ...");
    
                var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = existingClusterUsername, Password = existingClusterPassword };
                _hdiJobManagementClient = new HDInsightJobManagementClient(existingClusterUri, clusterCredentials);
    
                SubmitMRJob();
    
                System.Console.WriteLine("Press ENTER to continue ...");
                System.Console.ReadLine();
            }
    
            private static void SubmitMRJob()
            {
                List<string> args = new List<string> { { "/example/data/gutenberg/davinci.txt" }, { "/example/data/davinciwordcount" } };
    
                var paras = new MapReduceJobSubmissionParameters
                {
                    JarFile = @"/example/jars/hadoop-mapreduce-examples.jar",
                    JarClass = "wordcount",
                    Arguments = args
                };
    
                System.Console.WriteLine("Submitting the MR job to the cluster...");
                var jobResponse = _hdiJobManagementClient.JobManagement.SubmitMapReduceJob(paras);
                var jobId = jobResponse.JobSubmissionJsonResponse.Id;
                System.Console.WriteLine("Response status code is " + jobResponse.StatusCode);
                System.Console.WriteLine("JobId is " + jobId);
    
                System.Console.WriteLine("Waiting for the job completion ...");
    
                // Wait for job completion
                var jobDetail = _hdiJobManagementClient.JobManagement.GetJob(jobId).JobDetail;
                while (!jobDetail.Status.JobComplete)
                {
                    Thread.Sleep(1000);
                    jobDetail = _hdiJobManagementClient.JobManagement.GetJob(jobId).JobDetail;
                }
    
                // Get job output
                System.Console.WriteLine("Job output is: ");
                var storageAccess = new AzureStorageAccess(defaultStorageAccountName, defaultStorageAccountKey,
                    defaultStorageContainerName);
    
                if (jobDetail.ExitValue == 0)
                {
                    // Create the storage account object
                    CloudStorageAccount storageAccount = CloudStorageAccount.Parse("DefaultEndpointsProtocol=https;AccountName=" +
                        defaultStorageAccountName +
                        ";AccountKey=" + defaultStorageAccountKey);
    
                    // Create the blob client.
                    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    
                    // Retrieve reference to a previously created container.
                    CloudBlobContainer container = blobClient.GetContainerReference(defaultStorageContainerName);
    
                    CloudBlockBlob blockBlob = container.GetBlockBlobReference(outputFolder.Substring(1) + "/part-r-00000");
    
                    using (var stream = blockBlob.OpenRead())
                    {
                        using (StreamReader reader = new StreamReader(stream))
                        {
                            while (!reader.EndOfStream)
                            {
                                System.Console.WriteLine(reader.ReadLine());
                            }
                        }
                    }
                }
                else
                {
                    // fetch stderr output in case of failure
                    var output = _hdiJobManagementClient.JobManagement.GetJobErrorLogs(jobId, storageAccess);
    
                    using (var reader = new StreamReader(output, Encoding.UTF8))
                    {
                        string value = reader.ReadToEnd();
                        System.Console.WriteLine(value);
                    }
    
                }
            }
        }
    }

    ```

1. Naciśnij klawisz **F5** , aby uruchomić aplikację.

Aby ponownie uruchomić zadanie, należy zmienić nazwę folderu wyjściowego zadania w próbce `/example/data/davinciwordcount` .

Po pomyślnym zakończeniu zadania aplikacja drukuje zawartość pliku wyjściowego `part-r-00000` .

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono kilka sposobów tworzenia klastra usługi HDInsight. Więcej informacji można znaleźć w następujących artykułach:

* Aby przesłać zadanie programu Hive, zobacz [uruchamianie Apache Hive zapytań przy użyciu zestawu .NET SDK usługi HDInsight](apache-hadoop-use-hive-dotnet-sdk.md).
* Aby utworzyć klastry usługi HDInsight, zobacz [Tworzenie klastrów Apache Hadoop opartych na systemie Linux w usłudze HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).
* Aby zarządzać klastrami usługi HDInsight, zobacz [Zarządzanie klastrami Apache Hadoop w usłudze HDInsight](../hdinsight-administer-use-portal-linux.md).
* Aby uzyskać informacje na temat usługi HDInsight SDK dla platformy .NET, zobacz [Dokumentacja zestawu SDK usługi HDInsight dla platformy .NET](/dotnet/api/overview/azure/hdinsight).
* W przypadku nieinterakcyjnego uwierzytelniania na platformie Azure zobacz [Tworzenie aplikacji nieinterakcyjnego uwierzytelniania .NET HDInsight](../hdinsight-create-non-interactive-authentication-dotnet-applications.md).
