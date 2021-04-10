---
title: Zarządzanie klastrami Apache Hadoop w usłudze HDInsight przy użyciu zestawu .NET SDK — Azure
description: Dowiedz się, jak wykonywać zadania administracyjne dotyczące klastrów Apache Hadoop w usłudze HDInsight przy użyciu zestawu .NET SDK usługi HDInsight.
ms.service: hdinsight
ms.custom: hdinsightactive, devx-track-csharp
ms.topic: conceptual
ms.date: 05/14/2018
ms.openlocfilehash: e5f328d6be52732c30478eb49360efb13919a23e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104872221"
---
# <a name="manage-apache-hadoop-clusters-in-hdinsight-by-using-net-sdk"></a>Zarządzanie klastrami Apache Hadoop w usłudze HDInsight przy użyciu zestawu SDK platformy .NET

[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Dowiedz się, jak zarządzać klastrami usługi HDInsight przy użyciu [zestawu SDK HDInsight.NET](/dotnet/api/overview/azure/hdinsight).

**Wymagania wstępne**

Przed rozpoczęciem korzystania z informacji zawartych w tym artykule należy dysponować następującymi elementami:

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

## <a name="connect-to-azure-hdinsight"></a>Łączenie z usługą Azure HDInsight

Potrzebne są następujące pakiety NuGet:

```powershell
Install-Package Microsoft.Rest.ClientRuntime.Azure.Authentication -Pre
Install-Package Microsoft.Azure.Management.ResourceManager -Pre
Install-Package Microsoft.Azure.Management.HDInsight
```

Poniższy przykład kodu pokazuje, jak nawiązać połączenie z platformą Azure, zanim będzie można administrować klastrami usługi HDInsight w ramach subskrypcji platformy Azure.

```csharp
using System;
using Microsoft.Azure;
using Microsoft.Azure.Management.HDInsight;
using Microsoft.Azure.Management.HDInsight.Models;
using Microsoft.Azure.Management.ResourceManager;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using Microsoft.Rest;
using Microsoft.Rest.Azure.Authentication;

namespace HDInsightManagement
{
    class Program
    {
        private static HDInsightManagementClient _hdiManagementClient;
        // Replace with your AAD tenant ID if necessary
        private const string TenantId = UserTokenProvider.CommonTenantId; 
        private const string SubscriptionId = "<Your Azure Subscription ID>";
        // This is the GUID for the PowerShell client. Used for interactive logins in this example.
        private const string ClientId = "1950a258-227b-4e31-a9cf-717495945fc2";

        static void Main(string[] args)
        {
            // Authenticate and get a token
            var authToken = Authenticate(TenantId, ClientId, SubscriptionId);
            // Flag subscription for HDInsight, if it isn't already.
            EnableHDInsight(authToken);
            // Get an HDInsight management client
            _hdiManagementClient = new HDInsightManagementClient(authToken);

            // insert code here

            System.Console.WriteLine("Press ENTER to continue");
            System.Console.ReadLine();
        }

        /// <summary>
        /// Authenticate to an Azure subscription and retrieve an authentication token
        /// </summary>
        static TokenCloudCredentials Authenticate(string TenantId, string ClientId, string SubscriptionId)
        {
            var authContext = new AuthenticationContext("https://login.microsoftonline.com/" + TenantId);
            var tokenAuthResult = authContext.AcquireToken("https://management.core.windows.net/", 
                ClientId, 
                new Uri("urn:ietf:wg:oauth:2.0:oob"), 
                PromptBehavior.Always, 
                UserIdentifier.AnyUser);
            return new TokenCloudCredentials(SubscriptionId, tokenAuthResult.AccessToken);
        }
        /// <summary>
        /// Marks your subscription as one that can use HDInsight, if it has not already been marked as such.
        /// </summary>
        /// <remarks>This is essentially a one-time action; if you have already done something with HDInsight
        /// on your subscription, then this isn't needed at all and will do nothing.</remarks>
        /// <param name="authToken">An authentication token for your Azure subscription</param>
        static void EnableHDInsight(TokenCloudCredentials authToken)
        {
            // Create a client for the Resource manager and set the subscription ID
            var resourceManagementClient = new ResourceManagementClient(new TokenCredentials(authToken.Token));
            resourceManagementClient.SubscriptionId = SubscriptionId;
            // Register the HDInsight provider
            var rpResult = resourceManagementClient.Providers.Register("Microsoft.HDInsight");
        }
    }
}
```

Po uruchomieniu tego programu zostanie wyświetlony monit.  Jeśli nie chcesz zobaczyć tego monitu, zobacz [Tworzenie aplikacji usługi HDInsight programu .NET w trybie nieinterakcyjnym](hdinsight-create-non-interactive-authentication-dotnet-applications.md).


## <a name="list-clusters"></a>Wyświetl listę klastrów

Poniższy fragment kodu zawiera listę klastrów i niektóre właściwości:

```csharp
var results = _hdiManagementClient.Clusters.List();
foreach (var name in results.Clusters) {
    Console.WriteLine("Cluster Name: " + name.Name);
    Console.WriteLine("\t Cluster type: " + name.Properties.ClusterDefinition.ClusterType);
    Console.WriteLine("\t Cluster location: " + name.Location);
    Console.WriteLine("\t Cluster version: " + name.Properties.ClusterVersion);
}
```

## <a name="delete-clusters"></a>Usuwanie klastrów

Użyj poniższego fragmentu kodu, aby usunąć klaster synchronicznie lub asynchronicznie: 

```csharp
_hdiManagementClient.Clusters.Delete("<Resource Group Name>", "<Cluster Name>");
_hdiManagementClient.Clusters.DeleteAsync("<Resource Group Name>", "<Cluster Name>");
```

## <a name="scale-clusters"></a>Skalowanie klastrów

Funkcja skalowania klastra pozwala zmienić liczbę węzłów procesu roboczego używanych przez klaster, który działa w usłudze Azure HDInsight bez konieczności ponownego tworzenia klastra.

> [!NOTE]  
> Obsługiwane są tylko klastry z usługą HDInsight w wersji 3.1.3 lub nowszej. Jeśli nie masz pewności, jaka jest wersja klastra, możesz sprawdzić stronę właściwości.  Zobacz [listę i wyświetlanie klastrów](hdinsight-administer-use-portal-linux.md#showClusters).

Wpływ zmiany liczby węzłów danych dla każdego typu klastra obsługiwanego przez usługi HDInsight:

* Apache Hadoop
  
    Można bezproblemowo zwiększyć liczbę węzłów procesu roboczego w klastrze Hadoop uruchomionym bez wpływu na zadania oczekujące lub uruchomione. Nowe zadania mogą być również przesyłane, gdy operacja jest w toku. Błędy w operacji skalowania są bezpiecznie obsługiwane, aby klaster zawsze pozostawał w stanie funkcjonalności.
  
    Po skalowaniu klastra Hadoop przez zmniejszenie liczby węzłów danych, niektóre usługi w klastrze są ponownie uruchamiane. Powoduje to, że wszystkie uruchomione i oczekujące zadania kończą się niepowodzeniem po zakończeniu operacji skalowania. Można jednak ponownie przesłać zadania po zakończeniu operacji.
* Apache HBase
  
    Można bezproblemowo dodawać lub usuwać węzły do klastra HBase, gdy jest on uruchomiony. Serwery regionalne są automatycznie równoważone w ciągu kilku minut od zakończenia operacji skalowania. Można również ręcznie zrównoważyć serwery regionalne, logując się do węzła głównego klastra i uruchamiając następujące polecenia z poziomu okna wiersza polecenia:
  

    ```bash
    >pushd %HBASE_HOME%\bin
    >hbase shell
    >balancer
    ```

* Apache Storm
  
    Można bezproblemowo dodawać lub usuwać węzły danych do klastra burzy, gdy jest on uruchomiony. Ale po pomyślnym zakończeniu operacji skalowania należy ponownie zrównoważyć topologię.
  
    Ponowne równoważenie można wykonać na dwa sposoby:
  
  * Interfejs użytkownika sieci Web burzy
  * Narzędzie interfejsu wiersza polecenia (CLI)
    
    Aby uzyskać więcej informacji, zapoznaj się z [dokumentacją Apache Storm](https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html) .
    
    Interfejs użytkownika sieci Web burzy jest dostępny w klastrze usługi HDInsight:
    
    :::image type="content" source="./media/hdinsight-administer-use-powershell/hdinsight-portal-scale-cluster-storm-rebalance.png" alt-text="Ponowne równoważenie skali burzy usługi HDInsight":::
    
    Oto przykład sposobu użycia interfejsu wiersza polecenia w celu ponownego zrównoważenia topologii burzy:
    

    ```console
    ## Reconfigure the topology "mytopology" to use 5 worker processes,
    ## the spout "blue-spout" to use 3 executors, and
    ## the bolt "yellow-bolt" to use 10 executors
    $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10
    ```

Poniższy fragment kodu pokazuje, jak zmienić rozmiar klastra synchronicznie lub asynchronicznie:

```csharp
_hdiManagementClient.Clusters.Resize("<Resource Group Name>", "<Cluster Name>", <New Size>);   
_hdiManagementClient.Clusters.ResizeAsync("<Resource Group Name>", "<Cluster Name>", <New Size>);   
```

## <a name="grantrevoke-access"></a>Udzielanie/odwoływanie dostępu

Klastry HDInsight mają następujące usługi sieci Web HTTP (wszystkie te usługi mają RESTful punkty końcowe):

* ODBC
* JDBC
* Apache Ambari
* Apache Oozie
* Apache Templeton

Domyślnie te usługi są przyznawane na potrzeby dostępu. Możesz odwołać/udzielić dostępu. Aby odwołać:

```csharp
var httpParams = new HttpSettingsParameters
{
    HttpUserEnabled = false,
    HttpUsername = "admin",
    HttpPassword = "*******",
};
_hdiManagementClient.Clusters.ConfigureHttpSettings("<Resource Group Name>, <Cluster Name>, httpParams);
```

Aby udzielić:

```csharp
var httpParams = new HttpSettingsParameters
{
    HttpUserEnabled = enable,
    HttpUsername = "admin",
    HttpPassword = "*******",
};
_hdiManagementClient.Clusters.ConfigureHttpSettings("<Resource Group Name>, <Cluster Name>, httpParams);
```

> [!NOTE]  
> Przez przyznanie/cofnięcie dostępu, należy zresetować nazwę użytkownika i hasło klastra.

Można to również zrobić za pośrednictwem portalu. Zobacz [Zarządzanie klastrami Apache Hadoop w usłudze HDInsight przy użyciu Azure Portal](hdinsight-administer-use-portal-linux.md).

## <a name="update-http-user-credentials"></a>Aktualizowanie poświadczeń użytkownika HTTP

Ta sama procedura jest taka sama jak w przypadku dostępu do protokołu HTTP Grant/Revoke.  Jeśli do klastra udzielono dostępu przy użyciu protokołu HTTP, należy najpierw go odwołać.  A następnie Udziel dostępu przy użyciu nowych poświadczeń użytkownika HTTP.

## <a name="find-the-default-storage-account"></a>Znajdowanie domyślnego konta magazynu

Poniższy fragment kodu przedstawia sposób pobierania domyślnej nazwy konta magazynu i domyślnego klucza konta magazynu dla klastra.

```csharp
var results = _hdiManagementClient.Clusters.GetClusterConfigurations(<Resource Group Name>, <Cluster Name>, "core-site");
foreach (var key in results.Configuration.Keys)
{
    Console.WriteLine(String.Format("{0} => {1}", key, results.Configuration[key]));
}
```

## <a name="submit-jobs"></a>Prześlij zadania

**Aby przesłać zadania MapReduce**

Zobacz [Uruchamianie przykładów MapReduce w usłudze HDInsight](hadoop/apache-hadoop-run-samples-linux.md).

**Aby przesłać Apache Hive zadania** 

Zobacz [uruchamianie Apache Hive zapytań przy użyciu zestawu .NET SDK](hadoop/apache-hadoop-use-hive-dotnet-sdk.md).

**Aby przesłać zadania Apache Sqoop**

Zobacz [Korzystanie z platformy Apache Sqoop z usługą HDInsight](hadoop/apache-hadoop-use-sqoop-dotnet-sdk.md).

**Aby przesłać zadania Apache Oozie**

[Aby zdefiniować i uruchomić przepływ pracy w usłudze HDInsight, zobacz temat Korzystanie z platformy Apache Oozie z usługą Hadoop](hdinsight-use-oozie-linux-mac.md).

## <a name="upload-data-to-azure-blob-storage"></a>Przekazywanie danych do usługi Azure Blob Storage

Zobacz [Przekazywanie danych do usługi HDInsight][hdinsight-upload-data].

## <a name="see-also"></a>Zobacz też

* [Dokumentacja referencyjna zestawu SDK platformy .NET dla usługi HDInsight](/dotnet/api/overview/azure/hdinsight)
* [Zarządzanie klastrami Apache Hadoop w usłudze HDInsight przy użyciu Azure Portal](hdinsight-administer-use-portal-linux.md)
* [Administrowanie usługą HDInsight przy użyciu interfejsu wiersza polecenia][hdinsight-admin-cli]
* [Tworzenie klastrów usługi HDInsight][hdinsight-provision]
* [Przekazywanie danych do usługi HDInsight][hdinsight-upload-data]
* [Wprowadzenie do usługi Azure HDInsight][hdinsight-get-started]

[azure-purchase-options]: https://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: https://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: https://azure.microsoft.com/pricing/free-trial/

[hdinsight-get-started]:hadoop/apache-hadoop-linux-tutorial-get-started.md
[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-provision-custom-options]: hdinsight-hadoop-provision-linux-clusters.md#configuration
[hdinsight-submit-jobs]:hadoop/submit-apache-hadoop-jobs-programmatically.md

[hdinsight-admin-cli]: hdinsight-administer-use-command-line.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-use-hive]:hadoop/hdinsight-use-hive.md
[hdinsight-use-mapreduce]:hadoop/hdinsight-use-mapreduce.md
[hdinsight-upload-data]: hdinsight-upload-data.md
