---
title: Zestaw Azure HDInsight SDK dla języka go
description: Materiały referencyjne dotyczące korzystania z zestawu SDK usługi Azure HDInsight dla klastrów go i Apache Hadoop
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seodec18, devx-track-azurecli
ms.date: 01/03/2020
ms.openlocfilehash: 09564bd149488d956586c8a0e349cd79eec358a1
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100582763"
---
# <a name="hdinsight-sdk-for-go-preview"></a>HDInsight SDK for go (wersja zapoznawcza)

## <a name="overview"></a>Omówienie
Zestaw SDK usługi HDInsight dla języka go zawiera klasy i funkcje, które umożliwiają zarządzanie klastrami usługi HDInsight. Obejmuje operacje umożliwiające tworzenie, usuwanie, aktualizowanie, wyświetlanie i zmienianie rozmiaru, wykonywanie akcji skryptu, monitorowanie, pobieranie właściwości klastrów usługi HDInsight itd.

> [!NOTE]  
>Materiał referencyjny GoDoc dla tego zestawu SDK jest również [dostępny tutaj](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2015-03-01-preview/hdinsight).

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="prerequisites"></a>Wymagania wstępne

* [ `go get` Narzędzie](https://github.com/golang/go/wiki/GoGetTools).
* [Przejdź](https://golang.org/dl/).

## <a name="sdk-installation"></a>Instalacja zestawu SDK

W lokalizacji ZMIENNĄ GOPATH Uruchom polecenie `go get github.com/Azure/azure-sdk-for-go/tree/master/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight`

## <a name="authentication"></a>Authentication

Najpierw należy uwierzytelnić zestaw SDK w ramach subskrypcji platformy Azure.  Postępuj zgodnie z poniższym przykładem, aby utworzyć nazwę główną usługi i użyć jej do uwierzytelnienia. Po wykonaniu tej czynności będziesz mieć wystąpienie a `ClustersClient` , które zawiera wiele funkcji (opisanych w poniższych sekcjach), które mogą być używane do wykonywania operacji zarządzania.

> [!NOTE]  
> Istnieją inne sposoby uwierzytelniania oprócz poniższego przykładu, które mogą być lepiej dopasowane do Twoich potrzeb. Wszystkie funkcje są opisane tutaj: [funkcje uwierzytelniania w Azure SDK dla języka go](/azure/go/azure-sdk-go-authorization)

### <a name="authentication-example-using-a-service-principal"></a>Przykład uwierzytelniania przy użyciu nazwy głównej usługi

Najpierw Zaloguj się do [Azure Cloud Shell](https://shell.azure.com/bash). Upewnij się, że korzystasz obecnie z subskrypcji, w której ma zostać utworzona jednostka usługi.

```azurecli-interactive
az account show
```

Informacje o subskrypcji są wyświetlane jako dane JSON.

```json
{
  "environmentName": "AzureCloud",
  "id": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
  "isDefault": true,
  "name": "XXXXXXX",
  "state": "Enabled",
  "tenantId": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
  "user": {
    "cloudShellID": true,
    "name": "XXX@XXX.XXX",
    "type": "user"
  }
}
```

Jeśli nie zalogowano się do poprawnej subskrypcji, wybierz odpowiednią opcję, wykonując następujące działania: 
```azurecli-interactive
az account set -s <name or ID of subscription>
```

> [!IMPORTANT]  
> Jeśli dostawca zasobów usługi HDInsight nie został jeszcze zarejestrowany przez inną funkcję (na przykład przez utworzenie klastra usługi HDInsight za pomocą Azure Portal), należy wykonać tę czynność raz, aby umożliwić uwierzytelnianie. Można to zrobić z poziomu [Azure Cloud Shell](https://shell.azure.com/bash) , uruchamiając następujące polecenie:
>```azurecli-interactive
>az provider register --namespace Microsoft.HDInsight
>```

Następnie wybierz nazwę swojej nazwy głównej usługi i utwórz ją za pomocą następującego polecenia:

```azurecli-interactive
az ad sp create-for-rbac --name <Service Principal Name> --sdk-auth
```

Informacje o jednostce usługi są wyświetlane jako dane JSON.

```json
{
  "clientId": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
  "clientSecret": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
  "subscriptionId": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
  "tenantId": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
  "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
  "resourceManagerEndpointUrl": "https://management.azure.com/",
  "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
  "galleryEndpointUrl": "https://gallery.azure.com/",
  "managementEndpointUrl": "https://management.core.windows.net/"
}
```

Skopiuj poniższy fragment kodu i wypełnij `TENANT_ID` pola, `CLIENT_ID` , `CLIENT_SECRET` , i `SUBSCRIPTION_ID` z ciągami z kodu JSON, które zostały zwrócone po uruchomieniu polecenia, aby utworzyć jednostkę usługi.

```golang
package main

import (
    "context"
    "github.com/Azure/go-autorest/autorest/azure/auth"
    hdi "github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight"
    "github.com/Azure/go-autorest/autorest/to"
)

func main() {
    var err error

    // Tenant ID for your Azure Subscription
    var TENANT_ID = ""
    // Your Service Principal App Client ID
    var CLIENT_ID = ""
    // Your Service Principal Client Secret
    var CLIENT_SECRET = ""
    // Azure Subscription ID
    var SUBSCRIPTION_ID = ""

    var credentials = auth.NewClientCredentialsConfig(CLIENT_ID, CLIENT_SECRET, TENANT_ID)
    var client = hdi.NewClustersClient(SUBSCRIPTION_ID)

    client.Authorizer, err = credentials.Authorizer()
    if (err != nil) {
        fmt.Println("Error: ", err)
    }
```

## <a name="cluster-management"></a>Zarządzanie klastrami

> [!NOTE]  
> W tej sekcji założono, że masz już uwierzytelnione i skonstruowane `ClusterClient` wystąpienie i zapisujesz je w zmiennej o nazwie `client` . Instrukcje dotyczące uwierzytelniania i uzyskiwania `ClusterClient` można znaleźć w powyższej sekcji uwierzytelniania.

### <a name="create-a-cluster"></a>Tworzenie klastra

Nowy klaster można utworzyć przez wywołanie `client.Create()` . 

#### <a name="example"></a>Przykład

W tym przykładzie pokazano, jak utworzyć klaster [Apache Spark](https://spark.apache.org/) z dwoma węzłami głównymi i jednym węzłem procesu roboczego.

> [!NOTE]  
> Najpierw musisz utworzyć grupę zasobów i konto magazynu, jak wyjaśniono poniżej. Jeśli zostały już utworzone, możesz pominąć te kroki.

##### <a name="creating-a-resource-group"></a>Tworzenie grupy zasobów

Grupę zasobów można utworzyć przy użyciu [Azure Cloud Shell](https://shell.azure.com/bash) , uruchamiając

```azurecli-interactive
az group create -l <Region Name (i.e. eastus)> --n <Resource Group Name>
```

##### <a name="creating-a-storage-account"></a>Tworzenie konta magazynu

Konto magazynu można utworzyć przy użyciu [Azure Cloud Shell](https://shell.azure.com/bash) , uruchamiając następujące polecenia:

```azurecli-interactive
az storage account create -n <Storage Account Name> -g <Existing Resource Group Name> -l <Region Name (i.e. eastus)> --sku <SKU i.e. Standard_LRS>
```

Teraz uruchom następujące polecenie, aby uzyskać klucz dla konta magazynu (konieczne będzie utworzenie klastra):

```azurecli-interactive
az storage account keys list -n <Storage Account Name>
```

---
Poniższy fragment kodu go tworzy klaster Spark z dwoma węzłami głównymi i jednym węzłem roboczym. Wypełnij puste zmienne, jak wyjaśniono w komentarzach i śmiało, aby zmienić inne parametry zgodnie z konkretnymi potrzebami.

```golang
// The name for the cluster you are creating
var clusterName = "";
// The name of your existing Resource Group
var resourceGroupName = "";
// Choose a username
var username = "";
// Choose a password
var password = "";
// Replace <> with the name of your storage account
var storageAccount = "<>.blob.core.windows.net";
// Storage account key you obtained above
var storageAccountKey = "";
// Choose a region
var location = "";
var container = "default";

var parameters = hdi.ClusterCreateParametersExtended {
    Location: to.StringPtr(location),
    Tags: make(map[string]*string),
    Properties: &hdi.ClusterCreateProperties {
        ClusterVersion: to.StringPtr("3.6"),
        OsType: hdi.Linux,
        ClusterDefinition: &hdi.ClusterDefinition {
            Kind: to.StringPtr("spark"),
            Configurations: map[string]map[string]interface{}{
                "gateway": {
                    "restAuthCredential.isEnabled": "True",
                    "restAuthCredential.username":  username,
                    "restAuthCredential.password":  password,
                },
            },
        },
        Tier: hdi.Standard,
        ComputeProfile: &hdi.ComputeProfile {
            Roles: &[]hdi.Role {
                hdi.Role {
                    Name: to.StringPtr("headnode"),
                    TargetInstanceCount: to.Int32Ptr(2),
                    HardwareProfile: &hdi.HardwareProfile {
                        VMSize: to.StringPtr("Large"),
                    },
                    OsProfile: &hdi.OsProfile {
                        LinuxOperatingSystemProfile: &hdi.LinuxOperatingSystemProfile {
                            Username: to.StringPtr(username),
                            Password: to.StringPtr(password),
                        },
                    },
                },
                hdi.Role {
                    Name: to.StringPtr("workernode"),
                    TargetInstanceCount: to.Int32Ptr(1),
                    HardwareProfile: &hdi.HardwareProfile {
                        VMSize: to.StringPtr("Large"),
                    },
                    OsProfile: &hdi.OsProfile {
                        LinuxOperatingSystemProfile: &hdi.LinuxOperatingSystemProfile {
                            Username: to.StringPtr(username),
                            Password: to.StringPtr(password),
                        },
                    },
                },
            },
        },
        StorageProfile: &hdi.StorageProfile {
            Storageaccounts: &[]hdi.StorageAccount {
                hdi.StorageAccount {
                    Name: to.StringPtr(storageAccount),
                    Key: to.StringPtr(storageAccountKey),
                    Container: to.StringPtr(container),
                    IsDefault: to.BoolPtr(true),
                },
            },
        },
    },
}
client.Create(context.Background(), resourceGroupName, clusterName, parameters)
```

### <a name="get-cluster-details"></a>Pobierz szczegóły klastra

Aby uzyskać właściwości dla danego klastra:

```golang
client.Get(context.Background(), "<Resource Group Name>", "<Cluster Name>")
```

#### <a name="example"></a>Przykład

Za pomocą `get` programu można potwierdzić, że klaster został pomyślnie utworzony.

```golang
cluster, err := client.Get(context.Background(), resourceGroupName, clusterName)
if (err != nil) {
    fmt.Println("Error: ", err)
}
fmt.Println(*cluster.Name)
fmt.Println(*cluster.ID
```

Dane wyjściowe powinny wyglądać następująco:

```
<Cluster Name>
/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<Resource Group Name>/providers/Microsoft.HDInsight/clusters/<Cluster Name>
```

### <a name="list-clusters"></a>Wyświetl listę klastrów

#### <a name="list-clusters-under-the-subscription"></a>Wyświetlanie listy klastrów w ramach subskrypcji

```golang
client.List()
```

#### <a name="list-clusters-by-resource-group"></a>Wyświetl listę klastrów według grupy zasobów

```golang
client.ListByResourceGroup("<Resource Group Name>")
```

> [!NOTE]  
> Obie `List()` i `ListByResourceGroup()` zwracają `ClusterListResultPage` strukturę. Aby uzyskać następną stronę, możesz wywołać metodę `Next()` . Można to powtórzyć do momentu `ClusterListResultPage.NotDone()` `false` pokazywania, jak pokazano w poniższym przykładzie.

#### <a name="example"></a>Przykład

Poniższy przykład drukuje właściwości wszystkich klastrów dla bieżącej subskrypcji:

```golang
page, err := client.List(context.Background())
if (err != nil) {
    fmt.Println("Error: ", err)
}
for (page.NotDone()) {
    for _, cluster := range page.Values() {
        fmt.Println(*cluster.Name)
    }
    err = page.Next();
    if (err != nil) {
        fmt.Println("Error: ", err)
    }
}
```

### <a name="delete-a-cluster"></a>Usuwanie klastra

Aby usunąć klaster:

```golang
client.Delete(context.Background(), "<Resource Group Name>", "<Cluster Name>")
```

### <a name="update-cluster-tags"></a>Aktualizowanie tagów klastra

Możesz zaktualizować Tagi danego klastra, takie jak:

```golang
client.Update(context.Background(), "<Resource Group Name>", "<Cluster Name>", hdi.ClusterPatchParameters{<map[string]*string} of Tags>)
```

#### <a name="example"></a>Przykład

```golang
client.Update(context.Background(), "SDKTestRG", "SDKTest", hdi.ClusterPatchParameters{map[string]*string{"tag1Name" : to.StringPtr("tag1Value"), "tag2Name" : to.StringPtr("tag2Value")}})
```

### <a name="resize-cluster"></a>Zmień rozmiar klastra

Można zmienić rozmiar danego klastra liczba węzłów procesu roboczego przez określenie nowego rozmiaru, takiego jak:

```golang
client.Resize(context.Background(), "<Resource Group Name>", "<Cluster Name>", hdi.ClusterResizeParameters{<Num of Worker Nodes (int)>})
```

## <a name="cluster-monitoring"></a>Monitorowanie klastrów

Zestawu SDK zarządzania usługi HDInsight można także użyć do zarządzania monitorowaniem w klastrach za pomocą pakietu Operations Management Suite (OMS).

Podobnie jak w `ClusterClient` przypadku operacji zarządzania, należy utworzyć element `ExtensionClient` do użycia na potrzeby operacji monitorowania. Po zakończeniu powyższej sekcji uwierzytelniania można utworzyć podobny do tego `ExtensionClient` :

```golang
extClient := hdi.NewExtensionsClient(SUBSCRIPTION_ID)
extClient.Authorizer, _ = credentials.Authorizer()
```

> [!NOTE]  
> W poniższych przykładach monitorowania założono, że został on już zainicjowany `ExtensionClient` `extClient` i ustawił go `Authorizer` tak jak pokazano powyżej.

### <a name="enable-oms-monitoring"></a>Włącz monitorowanie pakietu OMS

> [!NOTE]  
> Aby włączyć monitorowanie pakietu OMS, musisz mieć istniejący obszar roboczy Log Analytics. Jeśli jeszcze tego nie zrobiono, możesz dowiedzieć się, jak to zrobić tutaj: [Utwórz obszar roboczy log Analytics w Azure Portal](../azure-monitor/logs/quick-create-workspace.md).

Aby włączyć monitorowanie pakietu OMS w klastrze:

```golang
extClient.EnableMonitoring(context.Background(), "<Resource Group Name", "Cluster Name", hdi.ClusterMonitoringRequest {WorkspaceID: to.StringPtr("<Workspace Id>")})
```

### <a name="view-status-of-oms-monitoring"></a>Wyświetl stan monitorowania pakietu OMS

Aby uzyskać stan pakietu OMS w klastrze:

```golang
extClient.GetMonitoringStatus(context.Background(), "<Resource Group Name", "Cluster Name")
```

### <a name="disable-oms-monitoring"></a>Wyłącz monitorowanie pakietu OMS

Aby wyłączyć pakiet OMS w klastrze:

```golang
extClient.DisableMonitoring(context.Background(), "<Resource Group Name", "Cluster Name")
```

## <a name="script-actions"></a>Akcje skryptu

Usługa HDInsight udostępnia funkcję konfiguracji o nazwie akcje skryptu, które wywołuje niestandardowe skrypty w celu dostosowania klastra.

> [!NOTE]  
> Więcej informacji na temat sposobu korzystania z akcji skryptu można znaleźć tutaj: [Dostosowywanie klastrów usługi HDInsight opartych na systemie Linux przy użyciu akcji skryptu](./hdinsight-hadoop-customize-cluster-linux.md)

### <a name="execute-script-actions"></a>Akcje wykonywania skryptu

Akcje skryptu można wykonywać w danym klastrze, takie jak:

```golang
var scriptAction1 = hdi.RuntimeScriptAction{Name: to.StringPtr("<Script Name>"), URI: to.StringPtr("<URL To Script>"), Roles: <&[]string of roles>} //valid roles are "headnode", "workernode", "zookeepernode", and "edgenode"
client.ExecuteScriptActions(context.Background(), "<Resource Group Name>", "<Cluster Name>", hdi.ExecuteScriptActionParameters{PersistOnSuccess: to.BoolPtr(true), ScriptActions: &[]hdi.RuntimeScriptAction{scriptAction1}}) //add more RuntimeScriptActions to the list to execute multiple scripts
```

W przypadku operacji "Usuń skrypt akcji" i "Wyświetlaj utrwalone akcje skryptu" należy utworzyć obiekt `ScriptActionsClient` , podobnie jak w przypadku `ClusterClient` operacji zarządzania. Po zakończeniu powyższej sekcji uwierzytelniania można utworzyć podobny do tego `ScriptActionsClient` :

```golang
scriptActionsClient := hdi.NewScriptActionsClient(SUBSCRIPTION_ID)
scriptActionsClient.Authorizer, _ = credentials.Authorizer()
```

> [!NOTE]  
> W poniższych akcjach skryptu założono, że zainicjowano już `ScriptActionsClient` wywołanie metody wywoływanej `scriptActionsClient` i ustawimy ją `Authorizer` jak pokazano powyżej.

### <a name="delete-script-action"></a>Akcja usuwania skryptu

Aby usunąć określoną akcję utrwalonego skryptu w danym klastrze:

```golang
scriptActionsClient.Delete(context.Background(), "<Resource Group Name>", "<Cluster Name>", "<Script Name>")
```

### <a name="list-persisted-script-actions"></a>Wyświetl listę utrwalonych akcji skryptów

> [!NOTE]  
> Oba `ListByCluster()` zwracają `ScriptActionsListPage` strukturę. Aby uzyskać następną stronę, możesz wywołać metodę `Next()` . Można to powtórzyć do momentu `ClusterListResultPage.NotDone()` `false` pokazywania, jak pokazano w poniższym przykładzie.

Aby wyświetlić listę wszystkich akcji utrwalonego skryptu dla określonego klastra:
```golang
scriptActionsClient.ListByCluster(context.Background(), "<Resource Group Name>", "<Cluster Name>")
```

#### <a name="example"></a>Przykład

```golang
page, err := scriptActionsClient.ListByCluster(context.Background(), resourceGroupName, clusterName)
if (err != nil) {
    fmt.Println("Error: ", err)
}
for (page.NotDone()) {
    for _, script := range page.Values() {
        fmt.Println(*script.Name) //There are functions to get other properties of RuntimeScriptActionDetail besides Name, such as Status, Operation, StartTime, EndTime, etc. See reference documentation.
    }
    err = page.Next();
    if (err != nil) {
        fmt.Println("Error: ", err)
    }
}
```

### <a name="list-all-scripts-execution-history"></a>Wyświetl listę wszystkich historii wykonywania skryptów

W przypadku tej operacji należy utworzyć obiekt `ScriptExecutionHistoryClient` , podobnie jak w `ClusterClient` przypadku operacji zarządzania. Po zakończeniu powyższej sekcji uwierzytelniania można utworzyć podobny do tego `ScriptActionsClient` :

```golang
scriptExecutionHistoryClient := hdi.NewScriptExecutionHistoryClient(SUBSCRIPTION_ID)
scriptExecutionHistoryClient.Authorizer, _ = credentials.Authorizer()
```

> [!NOTE]  
> W poniższym przykładzie przyjęto, że został już zainicjowany obiekt `ScriptExecutionHistoryClient` wywoływany `scriptExecutionHistoryClient` i ustawiony `Authorizer` powyżej.

Aby wyświetlić listę wszystkich skryptów wykonywania dla określonego klastra:

```golang
scriptExecutionHistoryClient.ListByCluster(context.Background(), "<Resource Group Name>", "<Cluster Name>")
```

#### <a name="example"></a>Przykład

Ten przykład drukuje wszystkie szczegóły wszystkich wykonanych wykonań skryptu.

```golang
page, err := scriptExecutionHistoryClient.ListByCluster(context.Background(), resourceGroupName, clusterName)
if (err != nil) {
    fmt.Println("Error: ", err)
}
for (page.NotDone()) {
    for _, script := range page.Values() {
        fmt.Println(*script.Name) //There are functions to get other properties of RuntimeScriptActionDetail besides Name, such as Status, Operation, StartTime, EndTime, etc. See reference documentation.
    }
    err = page.Next();
    if (err != nil) {
        fmt.Println("Error: ", err)
    }
}
```

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [materiałem referencyjnym GoDoc](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2015-03-01-preview/hdinsight). GoDocs dostarcza dokumentację referencyjną dla wszystkich funkcji w zestawie SDK.
