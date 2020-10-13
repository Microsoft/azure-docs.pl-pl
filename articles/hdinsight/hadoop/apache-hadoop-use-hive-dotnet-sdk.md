---
title: Uruchamianie zapytań Apache Hive przy użyciu zestawu .NET SDK usługi HDInsight — Azure
description: Dowiedz się, jak przesyłać zadania Apache Hadoop do usługi Azure HDInsight Apache Hadoop przy użyciu zestawu .NET SDK HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive, devx-track-csharp
ms.date: 12/24/2019
ms.openlocfilehash: cdb2207ab834ab84cedd2ad23e5e26186ac44039
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89020477"
---
# <a name="run-apache-hive-queries-using-hdinsight-net-sdk"></a>Uruchamianie zapytań Apache Hive przy użyciu zestawu .NET SDK usługi HDInsight

[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Dowiedz się, jak przesyłać zapytania Apache Hive przy użyciu zestawu .NET SDK usługi HDInsight. Napiszesz program w języku C# do przesyłania zapytania Hive na potrzeby wyświetlania listy tabel programu Hive i wyświetlania wyników.

> [!NOTE]  
> Kroki opisane w tym artykule należy wykonać z poziomu klienta systemu Windows. Aby uzyskać informacje na temat używania klienta z systemem Linux, OS X lub UNIX do pracy z usługą Hive, użyj selektora kart wyświetlanego w górnej części artykułu.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem pracy z tym artykułem należy dysponować następującymi elementami:

* Klaster Apache Hadoop w usłudze HDInsight. Zobacz [wprowadzenie do korzystania z platformy Hadoop opartej na systemie Linux w usłudze HDInsight](apache-hadoop-linux-tutorial-get-started.md).

    > [!IMPORTANT]  
    > Od 15 września 2017, zestaw SDK usługi HDInsight .NET obsługuje tylko zwracanie wyników zapytania Hive z kont usługi Azure Storage. W przypadku użycia tego przykładu z klastrem usługi HDInsight, który używa Azure Data Lake Storage jako magazynu podstawowego, nie można pobrać wyników wyszukiwania przy użyciu zestawu .NET SDK.

* [Program Visual Studio](https://visualstudio.microsoft.com/vs/community/) 2013 lub więcej. Należy zainstalować co najmniej obciążenie **aplikacji klasycznej platformy .NET** .

## <a name="run-a-hive-query"></a>Uruchamianie zapytania programu Hive

Zestaw SDK platformy .NET dla usługi HDInsight zawiera biblioteki klienckie platformy .NET, które ułatwiają pracę z klastrami usługi HDInsight z poziomu platformy .NET.

1. Utwórz aplikację konsolową w języku C# w programie Visual Studio.

1. W konsoli Menedżera pakietów NuGet Uruchom następujące polecenie:

    ```console
    Install-Package Microsoft.Azure.Management.HDInsight.Job
    ```

1. Edytuj Poniższy kod, aby zainicjować wartości zmiennych: `ExistingClusterName, ExistingClusterUsername, ExistingClusterPassword,DefaultStorageAccountName,DefaultStorageAccountKey,DefaultStorageContainerName` . Następnie użyj poprawionego kodu jako całej zawartości **program.cs** w programie Visual Studio.

    ```csharp
    using System.Collections.Generic;
    using System.IO;
    using System.Text;
    using System.Threading;
    using Microsoft.Azure.Management.HDInsight.Job;
    using Microsoft.Azure.Management.HDInsight.Job.Models;
    using Hyak.Common;

    namespace SubmitHDInsightJobDotNet
    {
        class Program
        {
            private static HDInsightJobManagementClient _hdiJobManagementClient;

            private const string ExistingClusterName = "<Your HDInsight Cluster Name>";
            private const string ExistingClusterUsername = "<Cluster Username>";
            private const string ExistingClusterPassword = "<Cluster User Password>";

            // Only Azure Storage accounts are supported by the SDK
            private const string DefaultStorageAccountName = "<Default Storage Account Name>";
            private const string DefaultStorageAccountKey = "<Default Storage Account Key>";
            private const string DefaultStorageContainerName = "<Default Blob Container Name>";

            private const string ExistingClusterUri = ExistingClusterName + ".azurehdinsight.net";

            static void Main(string[] args)
            {
                System.Console.WriteLine("The application is running ...");

                var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = ExistingClusterUsername, Password = ExistingClusterPassword };
                _hdiJobManagementClient = new HDInsightJobManagementClient(ExistingClusterUri, clusterCredentials);

                SubmitHiveJob();

                System.Console.WriteLine("Press ENTER to continue ...");
                System.Console.ReadLine();
            }

            private static void SubmitHiveJob()
            {
                Dictionary<string, string> defines = new Dictionary<string, string> { { "hive.execution.engine", "tez" }, { "hive.exec.reducers.max", "1" } };
                List<string> args = new List<string> { { "argA" }, { "argB" } };
                var parameters = new HiveJobSubmissionParameters
                {
                    Query = "SHOW TABLES",
                    Defines = defines,
                    Arguments = args
                };

                System.Console.WriteLine("Submitting the Hive job to the cluster...");
                var jobResponse = _hdiJobManagementClient.JobManagement.SubmitHiveJob(parameters);
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
                var storageAccess = new AzureStorageAccess(DefaultStorageAccountName, DefaultStorageAccountKey,
                    DefaultStorageContainerName);
                var output = (jobDetail.ExitValue == 0)
                    ? _hdiJobManagementClient.JobManagement.GetJobOutput(jobId, storageAccess) // fetch stdout output in case of success
                    : _hdiJobManagementClient.JobManagement.GetJobErrorLogs(jobId, storageAccess); // fetch stderr output in case of failure

                System.Console.WriteLine("Job output is: ");

                using (var reader = new StreamReader(output, Encoding.UTF8))
                {
                    string value = reader.ReadToEnd();
                    System.Console.WriteLine(value);
                }
            }
        }
    }
    ```

1. Naciśnij klawisz **F5** , aby uruchomić aplikację.

Dane wyjściowe aplikacji powinny wyglądać podobnie do:

![Dane wyjściowe zadania Hive usługi HDInsight Hadoop](./media/apache-hadoop-use-hive-dotnet-sdk/hdinsight-hadoop-use-hive-net-sdk-output.png)

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób przesyłania zapytań Apache Hive przy użyciu zestawu .NET SDK usługi HDInsight. Więcej informacji można znaleźć w następujących artykułach:

* [Wprowadzenie do usługi Azure HDInsight](apache-hadoop-linux-tutorial-get-started.md)
* [Tworzenie klastrów Apache Hadoop w usłudze HDInsight](../hdinsight-hadoop-provision-linux-clusters.md)
* [Dokumentacja zestawu SDK platformy .NET dla usługi HDInsight](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight)
* [Korzystanie z programu Apache Sqoop z usługą HDInsight](apache-hadoop-use-sqoop-mac-linux.md)
* [Tworzenie aplikacji usługi HDInsight platformy .NET z uwierzytelnianiem nieinterakcyjnym](../hdinsight-create-non-interactive-authentication-dotnet-applications.md)
