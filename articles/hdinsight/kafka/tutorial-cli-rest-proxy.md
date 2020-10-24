---
title: 'Samouczek: Tworzenie klastra Apache Kafka z włączonym serwerem proxy REST w usłudze HDInsight przy użyciu interfejsu wiersza polecenia platformy Azure'
description: Dowiedz się, jak wykonywać Apache Kafka operacje przy użyciu serwera proxy REST Kafka w usłudze Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: tutorial
ms.date: 02/27/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 49419853f193336e39ff8f729472342bb137fd39
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2020
ms.locfileid: "92490224"
---
# <a name="tutorial-create-an-apache-kafka-rest-proxy-enabled-cluster-in-hdinsight-using-azure-cli"></a>Samouczek: Tworzenie klastra Apache Kafka z włączonym serwerem proxy REST w usłudze HDInsight przy użyciu interfejsu wiersza polecenia platformy Azure

W tym samouczku dowiesz się, jak Apache Kafka utworzyć klaster z [włączonym serwerem proxy REST](./rest-proxy.md) w usłudze Azure HDInsight przy użyciu interfejsu wiersza polecenia platformy Azure (CLI). Azure HDInsight jest zarządzaną usługą analityczną typu „open source” o szerokim zakresie, z przeznaczeniem dla przedsiębiorstw. Apache Kafka to rozproszona platforma przesyłania strumieniowego typu open source. Jest ona często używana jako broker komunikatów, ponieważ oferuje funkcje podobne do kolejki komunikatów dotyczących publikowania i subskrybowania. Serwer proxy REST Kafka umożliwia współdziałanie z klastrem Kafka za pośrednictwem [interfejsu API REST](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy/) za pośrednictwem protokołu HTTP. Interfejs wiersza polecenia platformy Azure to wieloplatformowe środowisko wiersza polecenia do zarządzania zasobami platformy Azure.

Dostęp do interfejsu API platformy Apache Kafka mogą uzyskać tylko zasoby będące w tej samej sieci wirtualnej. Dostęp do klastra można uzyskać bezpośrednio przy użyciu protokołu SSH. Aby do platformy Apache Kafka podłączyć inne usługi, sieci lub maszyny wirtualne, należy najpierw utworzyć sieć wirtualną, a następnie utworzyć zasoby w obrębie tej sieci. Aby uzyskać więcej informacji, zobacz [łączenie się z Apache Kafka przy użyciu sieci wirtualnej](./apache-kafka-connect-vpn-gateway.md).

W ramach tego samouczka nauczysz się:

> [!div class="checklist"]
> * Wymagania wstępne dotyczące serwera proxy REST Kafka
> * Tworzenie klastra Apache Kafka przy użyciu interfejsu wiersza polecenia platformy Azure

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

* Aplikacja zarejestrowana w usłudze Azure AD. Aplikacje klienckie zapisywane w celu współdziałania z serwerem proxy REST Kafka będą używać tego identyfikatora aplikacji i klucza tajnego do uwierzytelniania na platformie Azure. Aby uzyskać więcej informacji, zobacz [Rejestrowanie aplikacji na platformie tożsamości firmy Microsoft](../../active-directory/develop/quickstart-register-app.md).

* Grupa zabezpieczeń usługi Azure AD z zarejestrowaną aplikacją jako członek. Ta grupa zabezpieczeń zostanie użyta do kontrolowania, które aplikacje mogą korzystać z serwera proxy REST. Aby uzyskać więcej informacji na temat tworzenia grup usługi Azure AD, zobacz [Tworzenie grupy podstawowej i Dodawanie członków przy użyciu Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

* Interfejs wiersza polecenia platformy Azure. Upewnij się, że masz co najmniej wersję 2.0.79. Zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

## <a name="create-an-apache-kafka-cluster"></a>Tworzenie klastra platformy Apache Kafka

1. Zaloguj się do subskrypcji platformy Azure.

    ```azurecli
    az login
    
    # If you have multiple subscriptions, set the one to use
    # az account set --subscription "SUBSCRIPTIONID"
    ```

1. Ustaw zmienne środowiskowe. Użycie zmiennych w tym samouczku jest oparte na bash. Inne środowiska będą wymagały niewielkich zmian.

    |Zmienna | Opis |
    |---|---|
    |resourceGroupName|Zastąp RESOURCEGROUPNAME nazwą nowej grupy zasobów.|
    |location|Zamień lokalizację na region, w którym zostanie utworzony klaster. Aby uzyskać listę prawidłowych lokalizacji, użyj `az account list-locations` polecenia|
    |clusterName|Zastąp element CLUSTERname globalnie unikatową nazwą nowego klastra.|
    |storageAccount|Zastąp STORAGEACCOUNTNAME nazwą nowego konta magazynu.|
    |httpPassword|Zastąp hasło hasłem logowania klastra, **administrator**.|
    |sshPassword|Zastąp hasło hasłem dla nazwy użytkownika Secure Shell **sshuser**.|
    |securityGroupName|Zastąp SECURITYGROUPNAME nazwą grupy zabezpieczeń klienta usługi AAD dla Kafka proxy Rest. Zmienna zostanie przeniesiona do `--kafka-client-group-name` parametru dla `az-hdinsight-create` .|
    |securityGroupID|Zastąp SECURITYGROUPID IDENTYFIKATORem grupy zabezpieczeń usługi AAD klienta dla Kafka serwera proxy Rest. Zmienna zostanie przeniesiona do `--kafka-client-group-id` parametru dla `az-hdinsight-create` .|
    |storageContainer|Kontener magazynu, który będzie używany przez klaster, dla tego samouczka należy pozostawić wartość ". Ta zmienna zostanie ustawiona z nazwą klastra.|
    |workernodeCount|Liczba węzłów procesu roboczego w klastrze, w tym samouczku należy pozostawić jako-. Aby zapewnić wysoką dostępność, Kafka wymaga co najmniej 3 węzłów procesu roboczego|
    |clustertype|Typ klastra usługi HDInsight, w tym samouczku pozostaw polecenie "As".|
    |clusterVersion|W ramach tego samouczka w wersji klastra usługi HDInsight należy pozostawić ten krok. Serwer proxy REST Kafka wymaga minimalnej wersji klastra 4,0.|
    |componentVersion|W tym samouczku należy pozostawić wersję Kafka. Serwer proxy REST Kafka wymaga minimalnej wersji składnika 2,1.|

    Zaktualizuj zmienne przy użyciu żądanych wartości. Następnie wprowadź polecenie interfejsu wiersza polecenia, aby ustawić zmienne środowiskowe.

    ```azurecli
    export resourceGroupName=RESOURCEGROUPNAME
    export location=LOCATION
    export clusterName=CLUSTERNAME
    export storageAccount=STORAGEACCOUNTNAME
    export httpPassword='PASSWORD'
    export sshPassword='PASSWORD'
    export securityGroupName=SECURITYGROUPNAME
    export securityGroupID=SECURITYGROUPID

    export storageContainer=$(echo $clusterName | tr "[:upper:]" "[:lower:]")
    export workernodeCount=3
    export clusterType=kafka
    export clusterVersion=4.0
    export componentVersion=kafka=2.1
    ```

1. [Utwórz grupę zasobów](/cli/azure/group#az-group-create) , wprowadzając następujące polecenie:

    ```azurecli
     az group create \
        --location $location \
        --name $resourceGroupName
    ```

1. [Utwórz konto usługi Azure Storage](/cli/azure/storage/account#az-storage-account-create) , wprowadzając następujące polecenie:

    ```azurecli
    # Note: kind BlobStorage is not available as the default storage account.
    az storage account create \
        --name $storageAccount \
        --resource-group $resourceGroupName \
        --https-only true \
        --kind StorageV2 \
        --location $location \
        --sku Standard_LRS
    ```

1. [Wyodrębnij klucz podstawowy](/cli/azure/storage/account/keys#az-storage-account-keys-list) z konta usługi Azure Storage i Zapisz go w zmiennej, wprowadzając następujące polecenie:

    ```azurecli
    export storageAccountKey=$(az storage account keys list \
        --account-name $storageAccount \
        --resource-group $resourceGroupName \
        --query [0].value -o tsv)
    ```

1. [Utwórz kontener usługi Azure Storage](/cli/azure/storage/container#az-storage-container-create) , wprowadzając następujące polecenie:

    ```azurecli
    az storage container create \
        --name $storageContainer \
        --account-key $storageAccountKey \
        --account-name $storageAccount
    ```

1. [Utwórz klaster usługi HDInsight](/cli/azure/hdinsight#az-hdinsight-create). Przed wprowadzeniem polecenia należy zwrócić uwagę na następujące parametry:

    1. Parametry wymagane dla klastrów Kafka:

        |Parametr | Opis|
        |---|---|
        |--Wpisz|Wartość musi być **Kafka**.|
        |--workernode-dane-disks-Node|Liczba dysków danych do użycia na węzeł procesu roboczego. Usługa HDInsight Kafka jest obsługiwana tylko w przypadku dysków z danymi. Ten samouczek używa wartości **2**.|

    1. Parametry wymagane dla serwera proxy REST Kafka:

        |Parametr | Opis|
        |---|---|
        |--Kafka-Management-Node-size|Rozmiar węzła. W tym samouczku zostanie użyta wartość **Standard_D4_v2**.|
        |--Kafka-Client-Group-ID|Identyfikator grupy zabezpieczeń usługi AAD klienta dla serwera proxy REST Kafka. Wartość jest przenoszona ze zmiennej **$securityGroupID**.|
        |--Kafka-Client-Group-Name|Nazwa grupy zabezpieczeń usługi AAD klienta dla serwera proxy REST Kafka. Wartość jest przenoszona ze zmiennej **$securityGroupName**.|
        |--Version|Wersja klastra usługi HDInsight musi być równa co najmniej 4,0. Wartość jest przenoszona ze zmiennej **$clusterVersion**.|
        |--wersja składnika|Wersja Kafka musi wynosić co najmniej 2,1. Wartość jest przenoszona ze zmiennej **$componentVersion**.|
    
        Jeśli chcesz utworzyć klaster bez serwera proxy REST, Usuń `--kafka-management-node-size` , `--kafka-client-group-id` i `--kafka-client-group-name` z `az hdinsight create` polecenia.

    1. Jeśli masz istniejącą sieć wirtualną, Dodaj parametry `--vnet-name` i `--subnet` i ich wartości.

    Wprowadź następujące polecenie, aby utworzyć klaster:

    ```azurecli
    az hdinsight create \
        --name $clusterName \
        --resource-group $resourceGroupName \
        --type $clusterType \
        --component-version $componentVersion \
        --http-password $httpPassword \
        --http-user admin \
        --location $location \
        --ssh-password $sshPassword \
        --ssh-user sshuser \
        --storage-account $storageAccount \
        --storage-account-key $storageAccountKey \
        --storage-container $storageContainer \
        --version $clusterVersion \
        --workernode-count $workernodeCount \
        --workernode-data-disks-per-node 2 \
        --kafka-management-node-size "Standard_D4_v2" \
        --kafka-client-group-id $securityGroupID \
        --kafka-client-group-name "$securityGroupName"
    ```

    Ukończenie procesu tworzenia klastra może potrwać kilka minut. Zwykle około 15.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Po ukończeniu korzystania z artykułu warto usunąć klaster. Dzięki usłudze HDInsight dane są przechowywane w usłudze Azure Storage, dzięki czemu można bezpiecznie usunąć klaster, gdy nie jest używany. Opłaty są naliczone również za klaster usługi HDInsight, nawet wtedy, gdy nie jest używany. Ze względu na to, że opłaty za klaster są dużo razy większe niż opłaty za magazyn, sprawia to, że należy usunąć klastry, gdy nie są używane.

Wprowadź wszystkie lub niektóre z następujących poleceń, aby usunąć zasoby:

```azurecli
# Remove cluster
az hdinsight delete \
    --name $clusterName \
    --resource-group $resourceGroupName

# Remove storage container
az storage container delete \
    --account-name $storageAccount  \
    --name $storageContainer

# Remove storage account
az storage account delete \
    --name $storageAccount  \
    --resource-group $resourceGroupName

# Remove resource group
az group delete \
    --name $resourceGroupName
```

## <a name="next-steps"></a>Następne kroki

Teraz, po pomyślnym Apache Kafka utworzeniu klastra z włączonym serwerem proxy REST w usłudze Azure HDInsight przy użyciu interfejsu wiersza polecenia platformy Azure, użyj kodu Python w celu współdziałania z serwerem proxy REST:

> [!div class="nextstepaction"]
> [Tworzenie przykładowej aplikacji](./rest-proxy.md#client-application-sample)
