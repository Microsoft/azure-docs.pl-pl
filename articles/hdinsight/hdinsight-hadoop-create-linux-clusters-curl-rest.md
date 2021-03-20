---
title: Tworzenie klastrów Apache Hadoop przy użyciu interfejsu API REST platformy Azure — Azure
description: Dowiedz się, jak tworzyć klastry usługi HDInsight, przesyłając szablony Azure Resource Manager do interfejsu API REST platformy Azure.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive, devx-track-azurecli
ms.date: 12/10/2019
ms.openlocfilehash: fa5675104d9614e1bd917585ea537c92dddd88cc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98945790"
---
# <a name="create-apache-hadoop-clusters-using-the-azure-rest-api"></a>Tworzenie klastrów Apache Hadoop przy użyciu interfejsu API REST platformy Azure

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Dowiedz się, jak utworzyć klaster usługi HDInsight przy użyciu szablonu Azure Resource Manager i interfejsu API REST platformy Azure.

Interfejs API REST platformy Azure umożliwia wykonywanie operacji zarządzania usługami hostowanymi na platformie Azure, w tym tworzeniem nowych zasobów, takich jak klastry usługi HDInsight.

> [!NOTE]  
> Kroki opisane w tym dokumencie wykorzystują [zwinięcie ( https://curl.haxx.se/) ](https://curl.haxx.se/) narzędzie do komunikacji z interfejsem API REST platformy Azure.

## <a name="create-a-template"></a>Tworzenie szablonu

Szablony Azure Resource Manager są dokumentami JSON opisującymi **grupę zasobów** i wszystkie znajdujące się w niej zasoby (takie jak HDInsight). Takie podejście oparte na szablonach umożliwia definiowanie zasobów potrzebnych dla usługi HDInsight w jednym szablonie.

Następujący dokument JSON jest połączeniem z plikami szablonu i parametrów z programu [https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-ssh-password](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-ssh-password) , który tworzy klaster oparty na systemie Linux przy użyciu hasła do zabezpieczenia konta użytkownika ssh.

   ```json
   {
       "properties": {
           "template": {
               "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
               "contentVersion": "1.0.0.0",
               "parameters": {
                   "clusterType": {
                       "type": "string",
                       "allowedValues": ["hadoop",
                       "hbase",
                       "storm",
                       "spark"],
                       "metadata": {
                           "description": "The type of the HDInsight cluster to create."
                       }
                   },
                   "clusterName": {
                       "type": "string",
                       "metadata": {
                           "description": "The name of the HDInsight cluster to create."
                       }
                   },
                   "clusterLoginUserName": {
                       "type": "string",
                       "metadata": {
                           "description": "These credentials can be used to submit jobs to the cluster and to log into cluster dashboards."
                       }
                   },
                   "clusterLoginPassword": {
                       "type": "securestring",
                       "metadata": {
                           "description": "The password must be at least 10 characters in length and must contain at least one digit, one non-alphanumeric character, and one upper or lower case letter."
                       }
                   },
                   "sshUserName": {
                       "type": "string",
                       "metadata": {
                           "description": "These credentials can be used to remotely access the cluster."
                       }
                   },
                   "sshPassword": {
                       "type": "securestring",
                       "metadata": {
                           "description": "The password must be at least 10 characters in length and must contain at least one digit, one non-alphanumeric character, and one upper or lower case letter."
                       }
                   },
                   "clusterStorageAccountName": {
                       "type": "string",
                       "metadata": {
                           "description": "The name of the storage account to be created and be used as the cluster's storage."
                       }
                   },
                   "clusterWorkerNodeCount": {
                       "type": "int",
                       "defaultValue": 4,
                       "metadata": {
                           "description": "The number of nodes in the HDInsight cluster."
                       }
                   }
               },
               "variables": {
                   "defaultApiVersion": "2015-05-01-preview",
                   "clusterApiVersion": "2015-03-01-preview"
               },
               "resources": [{
                   "name": "[parameters('clusterStorageAccountName')]",
                   "type": "Microsoft.Storage/storageAccounts",
                   "location": "[resourceGroup().location]",
                   "apiVersion": "[variables('defaultApiVersion')]",
                   "dependsOn": [],
                   "tags": {

                   },
                   "properties": {
                       "accountType": "Standard_LRS"
                   }
               },
               {
                   "name": "[parameters('clusterName')]",
                   "type": "Microsoft.HDInsight/clusters",
                   "location": "[resourceGroup().location]",
                   "apiVersion": "[variables('clusterApiVersion')]",
                   "dependsOn": ["[concat('Microsoft.Storage/storageAccounts/',parameters('clusterStorageAccountName'))]"],
                   "tags": {

                   },
                   "properties": {
                       "clusterVersion": "3.6",
                       "osType": "Linux",
                       "clusterDefinition": {
                           "kind": "[parameters('clusterType')]",
                           "configurations": {
                               "gateway": {
                                   "restAuthCredential.isEnabled": true,
                                   "restAuthCredential.username": "[parameters('clusterLoginUserName')]",
                                   "restAuthCredential.password": "[parameters('clusterLoginPassword')]"
                               }
                           }
                       },
                       "storageProfile": {
                           "storageaccounts": [{
                               "name": "[concat(parameters('clusterStorageAccountName'),'.blob.core.windows.net')]",
                               "isDefault": true,
                               "container": "[parameters('clusterName')]",
                               "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('clusterStorageAccountName')), variables('defaultApiVersion')).key1]"
                           }]
                       },
                       "computeProfile": {
                           "roles": [{
                               "name": "headnode",
                               "targetInstanceCount": "2",
                               "hardwareProfile": {
                                   "vmSize": "{}" 
                               },
                               "osProfile": {
                                   "linuxOperatingSystemProfile": {
                                       "username": "[parameters('sshUserName')]",
                                       "password": "[parameters('sshPassword')]"
                                   }
                               }
                           },
                           {
                               "name": "workernode",
                               "targetInstanceCount": "[parameters('clusterWorkerNodeCount')]",
                               "hardwareProfile": {
                                   "vmSize": "{}"
                               },
                               "osProfile": {
                                   "linuxOperatingSystemProfile": {
                                       "username": "[parameters('sshUserName')]",
                                       "password": "[parameters('sshPassword')]"
                                   }
                               }
                           }]
                       }
                   }
               }],
               "outputs": {
                   "cluster": {
                       "type": "object",
                       "value": "[reference(resourceId('Microsoft.HDInsight/clusters',parameters('clusterName')))]"
                   }
               }
           },
           "mode": "incremental",
           "Parameters": {
               "clusterName": {
                   "value": "newclustername"
               },
               "clusterType": {
                   "value": "hadoop"
               },
               "clusterStorageAccountName": {
                   "value": "newstoragename"
               },
               "clusterLoginUserName": {
                   "value": "admin"
               },
               "clusterLoginPassword": {
                   "value": "changeme"
               },
               "sshUserName": {
                   "value": "sshuser"
               },
               "sshPassword": {
                   "value": "changeme"
               }
           }
       }
   }
   ```

Ten przykład jest używany w procedurach przedstawionych w tym dokumencie. Zastąp przykładowe *wartości* w sekcji **Parameters** wartościami dla danego klastra.

> [!IMPORTANT]  
> Szablon używa domyślnej liczby węzłów procesu roboczego (4) dla klastra usługi HDInsight. Jeśli planujesz więcej niż 32 węzłów procesu roboczego, musisz wybrać rozmiar węzła głównego z co najmniej 8 rdzeniami i 14 GB pamięci RAM.
>
> Aby uzyskać więcej informacji o rozmiarach węzła i powiązanych kosztach, zobacz [Cennik usługi HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="sign-in-to-your-azure-subscription"></a>Zaloguj się do subskrypcji platformy Azure

Wykonaj kroki opisane w temacie [Rozpoczynanie pracy z interfejsem wiersza polecenia platformy Azure](/cli/azure/get-started-with-az-cli2) i nawiąż połączenie z subskrypcją za pomocą `az login` poleceniu.

## <a name="create-a-service-principal"></a>Tworzenie nazwy głównej usługi

> [!NOTE]  
> Te kroki są skróconą wersją sekcji *Tworzenie nazwy głównej usługi z hasłem* w [interfejsie wiersza polecenia Użyj platformy Azure, aby utworzyć nazwę główną usługi do uzyskiwania dostępu do zasobów](/cli/azure/create-an-azure-service-principal-azure-cli) . W tych krokach opisano tworzenie jednostki usługi, która jest używana do uwierzytelniania w interfejsie API REST platformy Azure.

1. W wierszu polecenia Użyj następującego polecenia, aby wyświetlić listę Twoich subskrypcji platformy Azure.

   ```azurecli
   az account list --query '[].{Subscription_ID:id,Tenant_ID:tenantId,Name:name}'  --output table
   ```

    Z listy wybierz subskrypcję, której chcesz użyć, a następnie zanotuj **Subscription_ID** i __Tenant_ID__ kolumny. Zapisz te wartości.

2. Użyj następującego polecenia, aby utworzyć aplikację w Azure Active Directory.

   ```azurecli
   az ad app create --display-name "exampleapp" --homepage "https://www.contoso.org" --identifier-uris "https://www.contoso.org/example" --password <Your password> --query 'appId'
   ```

    Zastąp wartości dla `--display-name` , `--homepage` i `--identifier-uris` własnymi wartościami. Podaj hasło dla nowego wpisu Active Directory.

   > [!NOTE]  
   > `--home-page`Wartości i `--identifier-uris` nie muszą odwoływać się do rzeczywistej strony sieci Web hostowanej w Internecie. Muszą to być unikatowe identyfikatory URI.

   Wartością zwracaną z tego polecenia jest __Identyfikator aplikacji__ dla nowej aplikacji. Zapisz tę wartość.

3. Użyj następującego polecenia, aby utworzyć nazwę główną usługi przy użyciu **identyfikatora aplikacji**.

   ```azurecli
   az ad sp create --id <App ID> --query 'objectId'
   ```

     Wartością zwracaną z tego polecenia jest __Identyfikator obiektu__. Zapisz tę wartość.

4. Przypisz rolę **właściciela** do jednostki usługi przy użyciu wartości **identyfikatora obiektu** . Użyj pozyskanego wcześniej **identyfikatora subskrypcji** .

   ```azurecli
   az role assignment create --assignee <Object ID> --role Owner --scope /subscriptions/<Subscription ID>/
   ```

## <a name="get-an-authentication-token"></a>Pobierz token uwierzytelniania

Użyj następującego polecenia, aby pobrać token uwierzytelniania:

```bash
curl -X "POST" "https://login.microsoftonline.com/$TENANTID/oauth2/token" \
-H "Cookie: flight-uxoptin=true; stsservicecookie=ests; x-ms-gateway-slice=productionb; stsservicecookie=ests" \
-H "Content-Type: application/x-www-form-urlencoded" \
--data-urlencode "client_id=$APPID" \
--data-urlencode "grant_type=client_credentials" \
--data-urlencode "client_secret=$PASSWORD" \
--data-urlencode "resource=https://management.azure.com/"
```

Ustaw `$TENANTID` , `$APPID` i `$PASSWORD` do wartości uzyskanych lub użytych wcześniej.

Jeśli to żądanie powiedzie się, otrzymasz odpowiedź serii 200, a treść odpowiedzi zawiera dokument JSON.

Dokument JSON zwrócony przez to żądanie zawiera element o nazwie **access_token**. Wartość **access_token** jest używana do uwierzytelniania żądań do interfejsu API REST.

```json
{
    "token_type":"Bearer",
    "expires_in":"3599",
    "expires_on":"1463409994",
    "not_before":"1463406094",
    "resource":"https://management.azure.com/","access_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWoNBVGZNNXBPWWlKSE1iYTlnb0VLWSIsImtpZCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSJ9.eyJhdWQiOiJodHRwczovL21hbmFnZW1lbnQuYXp1cmUuY29tLyIsImlzcyI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI2Ny8iLCJpYXQiOjE0NjM0MDYwOTQsIm5iZiI6MTQ2MzQwNjA5NCwiZXhwIjoxNDYzNDA5OTk5LCJhcHBpZCI6IjBlYzcyMzM0LTZkMDMtNDhmYi04OWU1LTU2NTJiODBiZDliYiIsImFwcGlkYWNyIjoiMSIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0Ny8iLCJvaWQiOiJlNjgxZTZiMi1mZThkLTRkZGUtYjZiMS0xNjAyZDQyNWQzOWYiLCJzdWIiOiJlNjgxZTZiMi1mZThkLTRkZGUtYjZiMS0xNjAyZDQyNWQzOWYiLCJ0aWQiOiI3MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDciLCJ2ZXIiOiIxLjAifQ.nJVERbeDHLGHn7ZsbVGBJyHOu2PYhG5dji6F63gu8XN2Cvol3J1HO1uB4H3nCSt9DTu_jMHqAur_NNyobgNM21GojbEZAvd0I9NY0UDumBEvDZfMKneqp7a_cgAU7IYRcTPneSxbD6wo-8gIgfN9KDql98b0uEzixIVIWra2Q1bUUYETYqyaJNdS4RUmlJKNNpENllAyHQLv7hXnap1IuzP-f5CNIbbj9UgXxLiOtW5JhUAwWLZ3-WMhNRpUO2SIB7W7tQ0AbjXw3aUYr7el066J51z5tC1AK9UC-mD_fO_HUP6ZmPzu5gLA6DxkIIYP3grPnRVoUDltHQvwgONDOw"
}
```

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Aby utworzyć grupę zasobów, należy wykonać następujące czynności.

* Ustaw `$SUBSCRIPTIONID` Identyfikator subskrypcji otrzymany podczas tworzenia nazwy głównej usługi.
* Ustaw `$ACCESSTOKEN` na token dostępu otrzymany w poprzednim kroku.
* Zamień na `DATACENTERLOCATION` centrum danych, w którym chcesz utworzyć grupę zasobów i zasoby. Na przykład "Południowo-środkowe stany USA".
* Ustaw `$RESOURCEGROUPNAME` na nazwę, która ma być używana dla tej grupy:

```bash
curl -X "PUT" "https://management.azure.com/subscriptions/$SUBSCRIPTIONID/resourcegroups/$RESOURCEGROUPNAME?api-version=2015-01-01" \
    -H "Authorization: Bearer $ACCESSTOKEN" \
    -H "Content-Type: application/json" \
    -d $'{
"location": "DATACENTERLOCATION"
}'
```

Jeśli to żądanie powiedzie się, otrzymasz odpowiedź serii 200, a treść odpowiedzi zawiera dokument JSON zawierający informacje o grupie. `"provisioningState"`Element zawiera wartość `"Succeeded"` .

## <a name="create-a-deployment"></a>Tworzenie wdrożenia

Użyj następującego polecenia, aby wdrożyć szablon w grupie zasobów.

* Ustaw `$DEPLOYMENTNAME` nazwę, która ma być używana dla tego wdrożenia.

```bash
curl -X "PUT" "https://management.azure.com/subscriptions/$SUBSCRIPTIONID/resourcegroups/$RESOURCEGROUPNAME/providers/microsoft.resources/deployments/$DEPLOYMENTNAME?api-version=2015-01-01" \
-H "Authorization: Bearer $ACCESSTOKEN" \
-H "Content-Type: application/json" \
-d "{set your body string to the template and parameters}"
```

> [!NOTE]  
> Jeśli szablon został zapisany w pliku, można użyć następującego polecenia zamiast `-d "{ template and parameters}"` :
>
> `--data-binary "@/path/to/file.json"`

Jeśli to żądanie powiedzie się, otrzymasz odpowiedź serii 200, a treść odpowiedzi zawiera dokument JSON zawierający informacje o operacji wdrażania.

> [!IMPORTANT]  
> Wdrożenie zostało przesłane, ale nie zostało ukończone. Ukończenie wdrożenia może potrwać kilka minut, zwykle około 15.

## <a name="check-the-status-of-a-deployment"></a>Sprawdź stan wdrożenia

Aby sprawdzić stan wdrożenia, użyj następującego polecenia:

```bash
curl -X "GET" "https://management.azure.com/subscriptions/$SUBSCRIPTIONID/resourcegroups/$RESOURCEGROUPNAME/providers/microsoft.resources/deployments/$DEPLOYMENTNAME?api-version=2015-01-01" \
-H "Authorization: Bearer $ACCESSTOKEN" \
-H "Content-Type: application/json"
```

To polecenie zwraca dokument JSON zawierający informacje o operacji wdrażania. `"provisioningState"`Element zawiera stan wdrożenia. Jeśli ten element zawiera wartość `"Succeeded"` , wdrożenie zakończyło się pomyślnie.

## <a name="troubleshoot"></a>Rozwiązywanie problemów

W razie problemów podczas tworzenia klastrów usługi HDInsight zapoznaj się z [wymaganiami dotyczącymi kontroli dostępu](./hdinsight-hadoop-customize-cluster-linux.md#access-control).

## <a name="next-steps"></a>Następne kroki

Teraz, gdy klaster usługi HDInsight został pomyślnie utworzony, Skorzystaj z poniższych informacji, aby dowiedzieć się, jak korzystać z klastra.

### <a name="apache-hadoop-clusters"></a>Klastry Apache Hadoop

* [Korzystanie z programu Apache Hive z usługą HDInsight](hadoop/hdinsight-use-hive.md)
* [Korzystanie z MapReduce z usługą HDInsight](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Klastry Apache HBase

* [Wprowadzenie do platformy Apache HBase w usłudze HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Tworzenie aplikacji Java dla platformy Apache HBase w usłudze HDInsight](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="apache-storm-clusters"></a>Klastry Apache Storm

* [Tworzenie topologii języka Java dla Apache Storm w usłudze HDInsight](storm/apache-storm-develop-java-topology.md)
* [Używanie składników języka Python w Apache Storm w usłudze HDInsight](storm/apache-storm-develop-python-topology.md)
* [Wdrażanie i monitorowanie topologii za pomocą Apache Storm w usłudze HDInsight](storm/apache-storm-deploy-monitor-topology-linux.md)
