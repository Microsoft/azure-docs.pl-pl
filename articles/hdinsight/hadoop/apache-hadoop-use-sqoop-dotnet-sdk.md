---
title: Uruchamianie zadań Apache Sqoop przy użyciu platformy .NET i usługi HDInsight — Azure
description: Dowiedz się, jak używać zestawu .NET SDK usługi HDInsight do uruchamiania importowania i eksportowania usługi Apache Sqoop między klastrem Apache Hadoop i Azure SQL Database.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive, hdiseo17may2017, devx-track-csharp
ms.date: 01/14/2020
ms.openlocfilehash: bc4b3019cb8308ea19d86eea7bf886063ea28563
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89008288"
---
# <a name="run-apache-sqoop-jobs-by-using-net-sdk-for-apache-hadoop-in-hdinsight"></a>Uruchamianie zadań Apache Sqoop przy użyciu zestawu SDK platformy .NET dla Apache Hadoop w usłudze HDInsight

[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Dowiedz się, jak używać zestawu .NET SDK usługi Azure HDInsight do uruchamiania zadań Apache Sqoop w usłudze HDInsight w celu importowania i eksportowania między klastrem usługi HDInsight i bazą danych Azure SQL Database lub SQL Server.

## <a name="prerequisites"></a>Wymagania wstępne

* Zakończenie [konfigurowania środowiska testowego](./hdinsight-use-sqoop.md#create-cluster-and-sql-database) z [używania platformy Apache Sqoop z usługą Hadoop w usłudze HDInsight](./hdinsight-use-sqoop.md).

* [Program Visual Studio](https://visualstudio.microsoft.com/vs/community/).

* Znajomość Sqoop. Aby uzyskać więcej informacji, zobacz [Podręcznik użytkownika Sqoop](https://sqoop.apache.org/docs/1.4.7/SqoopUserGuide.html).

## <a name="use-sqoop-on-hdinsight-clusters-with-the-net-sdk"></a>Korzystanie z Sqoop w klastrach usługi HDInsight z zestawem SDK dla platformy .NET

Zestaw SDK platformy .NET dla usługi HDInsight udostępnia biblioteki klienckie platformy .NET, co ułatwia pracę z klastrami usługi HDInsight z poziomu platformy .NET. W tej sekcji utworzysz aplikację konsolową w języku C# w celu wyeksportowania `hivesampletable` do tabeli Azure SQL Database utworzonej na podstawie wymagań wstępnych.

## <a name="set-up"></a>Konfiguruj

1. Uruchom program Visual Studio i Utwórz aplikację konsolową w języku C#.

1. Przejdź do **menu Narzędzia**Menedżer  >  **pakietów NuGet**  >  **konsola Menedżera pakietów** i uruchom następujące polecenie:

    ```
    Install-Package Microsoft.Azure.Management.HDInsight.Job
    ```

## <a name="sqoop-export"></a>Eksport Sqoop

Z programu Hive do SQL Server.  Ten przykład eksportuje dane z tabeli programu Hive `hivesampletable` do `mobiledata` tabeli w SQL Database.

1. Użyj poniższego kodu w pliku Program.cs. Edytuj kod, aby ustawić wartości dla `ExistingClusterName` i `ExistingClusterPassword` .

    ```csharp
    using Microsoft.Azure.Management.HDInsight.Job;
    using Microsoft.Azure.Management.HDInsight.Job.Models;
    using Hyak.Common;
    
    namespace SubmitHDInsightJobDotNet
    {
        class Program
        {
            private static HDInsightJobManagementClient _hdiJobManagementClient;
    
            private const string ExistingClusterName = "<Your HDInsight Cluster Name>";
            private const string ExistingClusterPassword = "<Cluster User Password>";
            private const string ExistingClusterUri = ExistingClusterName + ".azurehdinsight.net";
            private const string ExistingClusterUsername = "admin";
    
            static void Main(string[] args)
            {
                System.Console.WriteLine("The application is running ...");
    
                var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = ExistingClusterUsername, Password = ExistingClusterPassword };
                _hdiJobManagementClient = new HDInsightJobManagementClient(ExistingClusterUri, clusterCredentials);
    
                SubmitSqoopJob();
    
                System.Console.WriteLine("Press ENTER to continue ...");
                System.Console.ReadLine();
            }
    
            private static void SubmitSqoopJob()
            {
                var sqlDatabaseServerName = ExistingClusterName + "dbserver";
                var sqlDatabaseLogin = "sqluser";
                var sqlDatabaseLoginPassword = ExistingClusterPassword;
                var sqlDatabaseDatabaseName = ExistingClusterName + "db";
    
                // Connection string for using Azure SQL Database; Comment if using SQL Server
                var connectionString = "jdbc:sqlserver://" + sqlDatabaseServerName + ".database.windows.net;user=" + sqlDatabaseLogin + "@" + sqlDatabaseServerName + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName;
    
                // Connection string for using SQL Server; Uncomment if using SQL Server
                // var connectionString = "jdbc:sqlserver://" + sqlDatabaseServerName + ";user=" + sqlDatabaseLogin + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName;
    
                //sqoop start
                var tableName = "mobiledata";
    
                var parameters = new SqoopJobSubmissionParameters
                {
                     Command = "export --connect " + connectionString + " --table " + tableName + " --hcatalog-table hivesampletable"
                };
                //sqoop end
    
                System.Console.WriteLine("Submitting the Sqoop job to the cluster...");
                var response = _hdiJobManagementClient.JobManagement.SubmitSqoopJob(parameters);
                System.Console.WriteLine("Validating that the response is as expected...");
                System.Console.WriteLine("Response status code is " + response.StatusCode);
                System.Console.WriteLine("Validating the response object...");
                System.Console.WriteLine("JobId is " + response.JobSubmissionJsonResponse.Id);
            }
        }
    }
    ```

1. Aby uruchomić program, wybierz klawisz **F5** .

## <a name="sqoop-import"></a>Sqoop import

Z SQL Server do usługi Azure Storage. Ten przykład zależy od powyższego eksportu.  Ten przykład importuje dane z `mobiledata` tabeli w SQL Database do `wasb:///tutorials/usesqoop/importeddata` katalogu na domyślnym koncie magazynu klastra.

1. Zastąp kod powyżej `//sqoop start //sqoop end` bloku następującym kodem:

    ```csharp
    var tableName = "mobiledata";
    var exportDir = "/tutorials/usesqoop/importeddata";
    
    var parameters = new SqoopJobSubmissionParameters
    {
        Command = "import --connect " + connectionString + " --table " + tableName + " --target-dir " +  exportDir + " --fields-terminated-by \\t --lines-terminated-by \\n -m 1"
    };
    ```

1. Aby uruchomić program, wybierz klawisz **F5** .

## <a name="limitations"></a>Ograniczenia

Usługa HDInsight oparta na systemie Linux oferuje następujące ograniczenia:

* Eksport zbiorczy: Łącznik Sqoop używany do eksportowania danych do Microsoft SQL Server lub Azure SQL Database obecnie nie obsługuje operacji wstawiania zbiorczego.

* Przetwarzanie wsadowe: za pomocą `-batch` przełącznika, Sqoop wykonuje wiele operacji INSERT zamiast wsadowych operacje wstawiania.

## <a name="next-steps"></a>Następne kroki

Teraz wiesz już, jak używać programu Sqoop. Aby dowiedzieć się więcej, zobacz:

* [Korzystanie z platformy Apache Oozie z usługą HDInsight](../hdinsight-use-oozie-linux-mac.md): Użyj akcji Sqoop w przepływie pracy Oozie.
* [Przekazywanie danych do usługi HDInsight](../hdinsight-upload-data.md): Znajdź inne metody przekazywania danych do usług HDInsight lub Azure Blob Storage.
