---
title: 'Samouczek: tworzenie klastra z obsługą Apache Kafka REST w usłudze HDInsight przy użyciu interfejsu wiersza polecenia platformy Azure'
description: Dowiedz się, jak wykonywać operacje Apache Kafka przy użyciu serwera proxy REST platformy Kafka na Azure HDInsight.
ms.service: hdinsight
ms.topic: tutorial
ms.date: 02/27/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: cfb9ff9e6b107c9da84b164a055453994fc29229
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107786643"
---
# <a name="tutorial-create-an-apache-kafka-rest-proxy-enabled-cluster-in-hdinsight-using-azure-cli"></a>Samouczek: tworzenie klastra z obsługą Apache Kafka REST w usłudze HDInsight przy użyciu interfejsu wiersza polecenia platformy Azure

Z tego samouczka dowiesz się, jak utworzyć klaster serwera [proxy REST](./rest-proxy.md) Apache Kafka w usłudze Azure HDInsight przy użyciu interfejsu wiersza polecenia (CLI) platformy Azure. Azure HDInsight jest zarządzaną usługą analityczną typu „open source” o szerokim zakresie, z przeznaczeniem dla przedsiębiorstw. Apache Kafka to rozproszona platforma przesyłania strumieniowego typu open source. Jest ona często używana jako broker komunikatów, ponieważ oferuje funkcje podobne do kolejki komunikatów dotyczących publikowania i subskrybowania. Serwer proxy REST platformy Kafka umożliwia interakcję z klastrem Kafka za pośrednictwem interfejsu [API REST za](/rest/api/hdinsight-kafka-rest-proxy/) pośrednictwem protokołu HTTP. Interfejs wiersza polecenia platformy Azure to wieloplatformowe środowisko wiersza polecenia do zarządzania zasobami platformy Azure.

Dostęp do interfejsu API platformy Apache Kafka mogą uzyskać tylko zasoby będące w tej samej sieci wirtualnej. Dostęp do klastra można uzyskać bezpośrednio przy użyciu SSH. Aby do platformy Apache Kafka podłączyć inne usługi, sieci lub maszyny wirtualne, należy najpierw utworzyć sieć wirtualną, a następnie utworzyć zasoby w obrębie tej sieci. Aby uzyskać więcej informacji, zobacz Connect to Apache Kafka using a virtual network (Nawiązywanie połączenia [z siecią wirtualną przy użyciu sieci wirtualnej).](./apache-kafka-connect-vpn-gateway.md)

Z tego samouczka dowiesz się:

> [!div class="checklist"]
> * Wymagania wstępne dotyczące serwera proxy REST platformy Kafka
> * Tworzenie klastra Apache Kafka przy użyciu interfejsu wiersza polecenia platformy Azure

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

* Aplikacja zarejestrowana w usłudze Azure AD. Aplikacje klienckie, które zapisujesz do interakcji z serwerem proxy REST platformy Kafka, będą używać identyfikatora i identyfikatora tajnego tej aplikacji do uwierzytelniania na platformie Azure. Aby uzyskać więcej informacji, zobacz Register an application with the Microsoft identity platform (Rejestrowanie aplikacji [za pomocą platformy tożsamości firmy Microsoft).](../../active-directory/develop/quickstart-register-app.md)

* Grupa zabezpieczeń usługi Azure AD z zarejestrowaną aplikacją jako członkiem. Ta grupa zabezpieczeń będzie służyć do kontrolowania, które aplikacje mogą wchodzić w interakcje z serwerem proxy REST. Aby uzyskać więcej informacji na temat tworzenia grup usługi Azure AD, zobacz Tworzenie grupy podstawowej i dodawanie członków przy [użyciu Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

* Interfejs wiersza polecenia platformy Azure. Upewnij się, że masz co najmniej wersję 2.0.79. Zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure.](/cli/azure/install-azure-cli)

## <a name="create-an-apache-kafka-cluster"></a>Tworzenie klastra platformy Apache Kafka

1. Zaloguj się do subskrypcji platformy Azure.

    ```azurecli
    az login
    
    # If you have multiple subscriptions, set the one to use
    # az account set --subscription "SUBSCRIPTIONID"
    ```

1. Ustaw zmienne środowiskowe. Używanie zmiennych w tym samouczku jest oparte na powłoce Bash. W innych środowiskach będą potrzebne niewielkie zmiany.

    |Zmienna | Opis |
    |---|---|
    |resourceGroupName|Zastąp parametr RESOURCEGROUPNAME nazwą nowej grupy zasobów.|
    |location|Zastąp lokalizację regionem, w którym zostanie utworzony klaster. Aby uzyskać listę prawidłowych lokalizacji, użyj `az account list-locations` polecenia|
    |clusterName|Zastąp parametr CLUSTERNAME globalnie unikatową nazwą nowego klastra.|
    |storageAccount|Zastąp parametr STORAGEACCOUNTNAME nazwą nowego konta magazynu.|
    |httpPassword|Zastąp hasło hasłem logowania do klastra, **administrator**.|
    |sshPassword|Zastąp hasło hasłem nazwy użytkownika bezpiecznej powłoki **sshuser.**|
    |securityGroupName|Zastąp parametr SECURITYGROUPNAME nazwą grupy zabezpieczeń usługi AAD klienta dla serwera proxy rest platformy Kafka. Zmienna zostanie przekazana do `--kafka-client-group-name` parametru dla `az-hdinsight-create` .|
    |securityGroupID|Zastąp identyfikator SECURITYGROUPID identyfikatorem grupy zabezpieczeń usługi AAD klienta dla serwera proxy rest platformy Kafka. Zmienna zostanie przekazana do `--kafka-client-group-id` parametru dla `az-hdinsight-create` .|
    |storageContainer|Na użytek tego samouczka pozostaw kontener magazynu, który będzie przez klaster. Pozostaw go bez tego samouczka. Ta zmienna zostanie ustawiona z nazwą klastra.|
    |workernodeCount|Liczba węzłów procesu roboczego w klastrze— pozostaw w tym samouczku taką, jaka jest. Aby zagwarantować wysoką dostępność, platforma Kafka wymaga co najmniej 3 węzłów procesu roboczego|
    |typ klastra|Typ klastra usługi HDInsight pozostaw w tym samouczku bez względu na to, jak jest.|
    |clusterVersion|Wersja klastra usługi HDInsight pozostaw w tym samouczku w takiej konfiguracji, jaka jest. Serwer proxy REST platformy Kafka wymaga minimalnej wersji klastra 4.0.|
    |componentVersion|Wersja platformy Kafka pozostaw bez tego samouczka. Serwer proxy REST platformy Kafka wymaga minimalnej wersji składnika 2.1.|

    Zaktualizuj zmienne za pomocą żądanych wartości. Następnie wprowadź polecenia interfejsu wiersza polecenia, aby ustawić zmienne środowiskowe.

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

1. [Utwórz grupę zasobów,](/cli/azure/group#az_group_create) wprowadzając poniższe polecenie:

    ```azurecli
     az group create \
        --location $location \
        --name $resourceGroupName
    ```

1. [Utwórz konto usługi Azure Storage,](/cli/azure/storage/account#az_storage_account_create) wprowadzając poniższe polecenie:

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

1. [Wyodrębnij klucz podstawowy](/cli/azure/storage/account/keys#az_storage_account_keys_list) z konta usługi Azure Storage i przechowuj go w zmiennej, wprowadzając poniższe polecenie:

    ```azurecli
    export storageAccountKey=$(az storage account keys list \
        --account-name $storageAccount \
        --resource-group $resourceGroupName \
        --query [0].value -o tsv)
    ```

1. [Utwórz kontener usługi Azure Storage,](/cli/azure/storage/container#az_storage_container_create) wprowadzając poniższe polecenie:

    ```azurecli
    az storage container create \
        --name $storageContainer \
        --account-key $storageAccountKey \
        --account-name $storageAccount
    ```

1. [Utwórz klaster usługi HDInsight.](/cli/azure/hdinsight#az_hdinsight_create) Przed wprowadzeniem polecenia zanotuj następujące parametry:

    1. Wymagane parametry klastrów platformy Kafka:

        |Parametr | Opis|
        |---|---|
        |--type|Wartość musi mieć wartość **Kafka**.|
        |--workernode-data-disks-per-node|Liczba dysków danych do użycia w węźle procesu roboczego. Platforma Kafka usługi HDInsight jest obsługiwana tylko w przypadku dysków danych. W tym samouczku jest używana wartość **2**.|

    1. Wymagane parametry serwera proxy REST platformy Kafka:

        |Parametr | Opis|
        |---|---|
        |--kafka-management-node-size|Rozmiar węzła. W tym samouczku jest używana **wartość Standard_D4_v2**.|
        |--kafka-client-group-id|Identyfikator grupy zabezpieczeń usługi AAD klienta dla serwera proxy rest platformy Kafka. Wartość jest przekazywana ze zmiennej **$securityGroupID**.|
        |--kafka-client-group-name|Nazwa grupy zabezpieczeń usługi AAD klienta dla serwera proxy rest platformy Kafka. Wartość jest przekazywana ze zmiennej **$securityGroupName**.|
        |--version|Wersja klastra usługi HDInsight musi być co najmniej 4.0. Wartość jest przekazywana ze zmiennej **$clusterVersion**.|
        |--component-version|Wersja platformy Kafka musi być co najmniej 2.1. Wartość jest przekazywana ze zmiennej **$componentVersion**.|
    
        Jeśli chcesz utworzyć klaster bez serwera proxy REST, wyeliminuj `--kafka-management-node-size` , i z polecenia `--kafka-client-group-id` `--kafka-client-group-name` `az hdinsight create` .

    1. Jeśli masz istniejącą sieć wirtualną, dodaj parametry `--vnet-name` i `--subnet` oraz ich wartości.

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

Po ukończeniu korzystania z artykułu warto usunąć klaster. W usłudze HDInsight dane są przechowywane w usłudze Azure Storage, dzięki czemu można bezpiecznie usunąć klaster, gdy nie jest on w użyciu. Opłaty są również naliczane za klaster usługi HDInsight, nawet jeśli nie jest on w użyciu. Ponieważ opłaty za klaster są wielokrotnie wyższe niż opłaty za magazyn, ekonomicznie warto usunąć klastry, gdy nie są one w użyciu.

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

Teraz, po pomyślnym utworzeniu klastra serwera proxy REST Apache Kafka przy użyciu interfejsu wiersza polecenia platformy Azure Azure HDInsight użyj kodu języka Python do interakcji z serwerem proxy REST:

> [!div class="nextstepaction"]
> [Tworzenie przykładowej aplikacji](./rest-proxy.md#client-application-sample)