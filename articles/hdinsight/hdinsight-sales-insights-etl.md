---
title: 'Samouczek: Tworzenie kompleksowego potoku ETL w celu uzyskania szczegółowych informacji o sprzedaży w usłudze Azure HDInsight'
description: Dowiedz się, jak tworzyć potoki ETL za pomocą usługi Azure HDInsight, aby uzyskiwać wgląd w dane sprzedaży przy użyciu klastrów na żądanie i Power BI.
ms.service: hdinsight
ms.topic: tutorial
ms.custom: hdinsightactive, devx-track-azurecli
ms.date: 04/15/2020
ms.openlocfilehash: 32c331b10fcc73b5449588595370f8f69ef9893c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104872051"
---
# <a name="tutorial-create-an-end-to-end-data-pipeline-to-derive-sales-insights-in-azure-hdinsight"></a>Samouczek: Tworzenie kompleksowego potoku danych w celu uzyskania szczegółowych informacji o sprzedaży w usłudze Azure HDInsight

W tym samouczku utworzysz kompleksowy Potok danych, który wykonuje operacje wyodrębniania, przekształcania i ładowania (ETL). Potok będzie używać klastrów [Apache Spark](./spark/apache-spark-overview.md) i Apache Hive działających w usłudze Azure HDInsight na potrzeby wykonywania zapytań i manipulowania danymi. Będziesz również używać takich technologii jak Azure Data Lake Storage Gen2 do przechowywania danych i Power BI do wizualizacji.

Ten potok danych łączy dane z różnych magazynów, usuwa wszelkie niechciane dane, dołącza nowe dane i ładuje je z powrotem do magazynu w celu wizualizacji szczegółowych informacji o firmie. Więcej informacji na temat potoków ETL można znaleźć w części [wyodrębnianie, przekształcanie i ładowanie (ETL) na dużą skalę](./hadoop/apache-hadoop-etl-at-scale.md).

:::image type="content" source="./media/hdinsight-sales-insights-etl/architecture.png" alt-text="Architektura ETL" border="false":::

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

* Interfejs wiersza polecenia platformy Azure — co najmniej wersja 2.2.0. Zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

* JQ, procesor JSON w wierszu polecenia.  Zobacz [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/) .

* Członek [wbudowanego właściciela roli platformy Azure](../role-based-access-control/built-in-roles.md).

* Jeśli używasz programu PowerShell do wyzwalania potoku Data Factory, będziesz potrzebować polecenia [AZ module](/powershell/azure/).

* [Power BI Desktop](https://aka.ms/pbiSingleInstaller) wizualizacji szczegółowych informacji o firmie na końcu tego samouczka.

## <a name="create-resources"></a>Tworzenie zasobów

### <a name="clone-the-repository-with-scripts-and-data"></a>Klonowanie repozytorium za pomocą skryptów i danych

1. Zaloguj się do subskrypcji platformy Azure. Jeśli planujesz używać Azure Cloud Shell, wybierz opcję **Wypróbuj** w prawym górnym rogu bloku kodu. W przeciwnym razie wprowadź poniższe polecenie:

    ```azurecli-interactive
    az login

    # If you have multiple subscriptions, set the one to use
    # az account set --subscription "SUBSCRIPTIONID"
    ```

1. Upewnij się, że jesteś członkiem roli [właściciela](../role-based-access-control/built-in-roles.md)systemu Azure. Zamień `user@contoso.com` na swoje konto, a następnie wprowadź polecenie:

    ```azurecli
    az role assignment list \
    --assignee "user@contoso.com" \
    --role "Owner"
    ```

    Jeśli żaden rekord nie zostanie zwrócony, nie jest członkiem i nie będzie można ukończyć pracy z tym samouczkiem.

1. Pobierz dane i skrypty dla tego samouczka z [REPOZYTORIUM ETL usługi HDInsight Sales Insights](https://github.com/Azure-Samples/hdinsight-sales-insights-etl). Wprowadź następujące polecenie:

    ```bash
    git clone https://github.com/Azure-Samples/hdinsight-sales-insights-etl.git
    cd hdinsight-sales-insights-etl
    ```

1. Upewnij `salesdata scripts templates` się, że zostały utworzone. Sprawdź przy użyciu następującego polecenia:

   ```bash
   ls
   ```

### <a name="deploy-azure-resources-needed-for-the-pipeline"></a>Wdrażanie zasobów platformy Azure wymaganych dla potoku

1. Dodaj uprawnienia wykonywania dla wszystkich skryptów, wprowadzając następujące polecenie:

    ```bash
    chmod +x scripts/*.sh
    ````

1. Ustaw zmienną dla grupy zasobów. Zamień `RESOURCE_GROUP_NAME` na nazwę istniejącej lub nowej grupy zasobów, a następnie wprowadź polecenie:

    ```bash
    resourceGroup="RESOURCE_GROUP_NAME"
    ```

1. Uruchom skrypt. Zamień `LOCATION` na żądaną wartość, a następnie wprowadź polecenie:

    ```bash
    ./scripts/resources.sh $resourceGroup LOCATION
    ```

    Jeśli nie masz pewności, który region należy określić, możesz pobrać listę obsługiwanych regionów dla subskrypcji za pomocą polecenia [AZ Account List-Locations](/cli/azure/account#az-account-list-locations) .

    Polecenie spowoduje wdrożenie następujących zasobów:

    * Konto usługi Azure Blob Storage. To konto będzie zawierać dane sprzedaży firmowej.
    * Konto Azure Data Lake Storage Gen2. To konto będzie działać jako konto magazynu dla obu klastrów usługi HDInsight. Przeczytaj więcej na temat usługi HDInsight i Data Lake Storage Gen2 w [usłudze Azure HDInsight Integration z Data Lake Storage Gen2](https://azure.microsoft.com/blog/azure-hdinsight-integration-with-data-lake-storage-gen-2-preview-acl-and-security-update/).
    * Tożsamość zarządzana przypisana przez użytkownika. To konto umożliwia Klastrom usługi HDInsight dostęp do konta Data Lake Storage Gen2.
    * Klaster Apache Spark. Ten klaster zostanie użyty do oczyszczenia i przekształcenia danych pierwotnych.
    * Apache Hive [interaktywny klaster zapytań](./interactive-query/apache-interactive-query-get-started.md) . Ten klaster umożliwi wykonywanie zapytań dotyczących danych sprzedaży i wizualizację go przy użyciu Power BI.
    * Sieć wirtualna platformy Azure obsługiwana przez reguły sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń). Ta sieć wirtualna umożliwia Klastrom komunikowanie się i zabezpieczenie komunikacji.

Tworzenie klastra może trwać około 20 minut.

Domyślne hasło dostępu SSH do klastrów to `Thisisapassword1` . Jeśli chcesz zmienić hasło, przejdź do `./templates/resourcesparameters_remainder.json` pliku i Zmień hasło dla `sparksshPassword` parametrów,, `sparkClusterLoginPassword` `llapClusterLoginPassword` i `llapsshPassword` .

### <a name="verify-deployment-and-collect-resource-information"></a>Weryfikowanie wdrożenia i zbieranie informacji o zasobach

1. Jeśli chcesz sprawdzić stan wdrożenia, przejdź do grupy zasobów na Azure Portal. W obszarze **Ustawienia** wybierz pozycję **wdrożenia**, a następnie wdrożenie. Tutaj można zobaczyć zasoby, które zostały pomyślnie wdrożone, oraz zasoby, które są nadal w toku.

1. Aby wyświetlić nazwy klastrów, wprowadź następujące polecenie:

    ```bash
    sparkClusterName=$(cat resourcesoutputs_remainder.json | jq -r '.properties.outputs.sparkClusterName.value')
    llapClusterName=$(cat resourcesoutputs_remainder.json | jq -r '.properties.outputs.llapClusterName.value')

    echo "Spark Cluster" $sparkClusterName
    echo "LLAP cluster" $llapClusterName
    ```

1. Aby wyświetlić konto usługi Azure Storage i klucz dostępu, wprowadź następujące polecenie:

    ```azurecli
    blobStorageName=$(cat resourcesoutputs_storage.json | jq -r '.properties.outputs.blobStorageName.value')

    blobKey=$(az storage account keys list \
        --account-name $blobStorageName \
        --resource-group $resourceGroup \
        --query [0].value -o tsv)

    echo $blobStorageName
    echo $blobKey
    ```

1. Aby wyświetlić konto Data Lake Storage Gen2 i klucz dostępu, wprowadź następujące polecenie:

    ```azurecli
    ADLSGen2StorageName=$(cat resourcesoutputs_storage.json | jq -r '.properties.outputs.adlsGen2StorageName.value')

    adlsKey=$(az storage account keys list \
        --account-name $ADLSGen2StorageName \
        --resource-group $resourceGroup \
        --query [0].value -o tsv)

    echo $ADLSGen2StorageName
    echo $adlsKey
    ```

### <a name="create-a-data-factory"></a>Tworzenie fabryki danych

Azure Data Factory jest narzędziem ułatwiającym automatyzację Azure Pipelines. Nie jest to jedyna metoda wykonywania tych zadań, ale jest to świetny sposób automatyzacji procesów. Aby uzyskać więcej informacji na temat Azure Data Factory, zobacz [dokumentację Azure Data Factory](https://azure.microsoft.com/services/data-factory/).

Ta Fabryka danych będzie mieć jeden potok z dwoma działaniami:

* Pierwsze działanie spowoduje skopiowanie danych z usługi Azure Blob Storage do konta magazynu Data Lake Storage generacji 2, aby naśladować pozyskiwanie danych.
* Drugie działanie spowoduje przekształcenie danych w klastrze Spark. Skrypt przekształca dane przez usunięcie niechcianych kolumn. Dodaje również nową kolumnę, która oblicza przychód generowany przez pojedynczą transakcję.

Aby skonfigurować potok Azure Data Factory, wykonaj poniższe polecenie.  Nadal powinien znajdować się w `hdinsight-sales-insights-etl` katalogu.

```bash
blobStorageName=$(cat resourcesoutputs_storage.json | jq -r '.properties.outputs.blobStorageName.value')
ADLSGen2StorageName=$(cat resourcesoutputs_storage.json | jq -r '.properties.outputs.adlsGen2StorageName.value')

./scripts/adf.sh $resourceGroup $ADLSGen2StorageName $blobStorageName
```

Ten skrypt wykonuje następujące czynności:

1. Tworzy jednostkę usługi z `Storage Blob Data Contributor` uprawnieniami na koncie magazynu Data Lake Storage Gen2.
1. Uzyskuje token uwierzytelniania w celu autoryzowania żądań POST do [interfejsu API REST systemu plików Data Lake Storage Gen2](https://docs.microsoft.com/rest/api/storageservices/datalakestoragegen2/filesystem/create).
1. Wypełnia rzeczywistą nazwę konta magazynu Data Lake Storage Gen2 w `sparktransform.py` `query.hql` plikach i.
1. Uzyskuje klucze magazynu dla Data Lake Storage Gen2 i kont usługi BLOB Storage.
1. Tworzy inne wdrożenie zasobów w celu utworzenia potoku Azure Data Factory przy użyciu skojarzonych z nim usług i działań. Przekazuje klucze magazynu jako parametry do pliku szablonu, dzięki czemu połączone usługi mogą prawidłowo uzyskiwać dostęp do kont magazynu.

## <a name="run-the-data-pipeline"></a>Uruchamianie potoku danych

### <a name="trigger-the-data-factory-activities"></a>Wyzwól działania Data Factory

Pierwsze działanie w utworzonym potoku Data Factory przenosi dane z magazynu obiektów BLOB do Data Lake Storage Gen2. Drugie działanie stosuje przekształcenia Spark na danych i zapisuje przekształcone pliki CSV do nowej lokalizacji. Ukończenie całego potoku może potrwać kilka minut.

Aby pobrać nazwę Data Factory, wprowadź następujące polecenie:

```azurecli
cat resourcesoutputs_adf.json | jq -r '.properties.outputs.factoryName.value'
```

Aby wyzwolić potok, można wykonać jedną z:

* Wyzwalanie potoku Data Factory w programie PowerShell. Zamień `RESOURCEGROUP` i `DataFactoryName` z odpowiednimi wartościami, a następnie uruchom następujące polecenia:

    ```powershell
    # If you have multiple subscriptions, set the one to use
    # Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"

    $resourceGroup="RESOURCEGROUP"
    $dataFactory="DataFactoryName"

    $pipeline =Invoke-AzDataFactoryV2Pipeline `
        -ResourceGroupName $resourceGroup `
        -DataFactory $dataFactory `
        -PipelineName "IngestAndTransform"

    Get-AzDataFactoryV2PipelineRun `
        -ResourceGroupName $resourceGroup  `
        -DataFactoryName $dataFactory `
        -PipelineRunId $pipeline
    ```

    Wykonaj ponowne wykonanie `Get-AzDataFactoryV2PipelineRun` w razie potrzeby, aby monitorować postęp.

    Lub

* Otwórz fabrykę danych i wybierz pozycję **utwórz & monitor**. Wyzwalanie `IngestAndTransform` potoku z poziomu portalu. Aby uzyskać informacje dotyczące wyzwalania potoków za pośrednictwem portalu, zobacz [Tworzenie klastrów Apache Hadoop na żądanie w usłudze HDInsight przy użyciu Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md#trigger-a-pipeline).

Aby sprawdzić, czy potok został uruchomiony, można wykonać jedną z następujących czynności:

* Przejdź do sekcji **monitorowanie** w fabryce danych za pomocą portalu.
* W Eksplorator usługi Azure Storage przejdź do konta magazynu Data Lake Storage generacji 2. Przejdź do `files` systemu plików, a następnie przejdź do `transformed` folderu i sprawdź jego zawartość, aby sprawdzić, czy potok zakończył się pomyślnie.

Aby uzyskać inne metody przekształcania danych za pomocą usługi HDInsight, zobacz [ten artykuł na temat korzystania z Jupyter Notebook](/azure/hdinsight/spark/apache-spark-load-data-run-query).

### <a name="create-a-table-on-the-interactive-query-cluster-to-view-data-on-power-bi"></a>Utwórz tabelę w klastrze zapytań interaktywnych, aby wyświetlić dane na Power BI

1. Skopiuj `query.hql` plik do klastra LLAP przy użyciu punktu połączenia usługi. Wprowadź polecenie:

    ```bash
    llapClusterName=$(cat resourcesoutputs_remainder.json | jq -r '.properties.outputs.llapClusterName.value')
    scp scripts/query.hql sshuser@$llapClusterName-ssh.azurehdinsight.net:/home/sshuser/
    ```

    Przypomnienie: domyślne hasło to `Thisisapassword1` .

1. Użyj protokołu SSH, aby uzyskać dostęp do klastra LLAP. Wprowadź polecenie:

    ```bash
    ssh sshuser@$llapClusterName-ssh.azurehdinsight.net
    ```

1. Użyj następującego polecenia, aby uruchomić skrypt:

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -f query.hql
    ```

    Ten skrypt spowoduje utworzenie tabeli zarządzanej w klastrze interakcyjnych zapytań, do której można uzyskać dostęp z Power BI.

### <a name="create-a-power-bi-dashboard-from-sales-data"></a>Tworzenie pulpitu nawigacyjnego Power BI na podstawie danych sprzedaży

1. Otwórz program Power BI Desktop.

1. Z menu Przejdź, aby **uzyskać**  >  **więcej danych...**  >  **Platforma Azure**  >  **Zapytanie interaktywne usługi HDInsight**.

1. Wybierz pozycję **Połącz**.

1. W oknie dialogowym **interakcyjnego zapytania usługi HDInsight** :
    1. W polu tekstowym **serwer** wprowadź nazwę klastra LLAP w formacie `https://LLAPCLUSTERNAME.azurehdinsight.net` .
    1. W polu tekstowym **baza danych** wprowadź `default` .
    1. Wybierz przycisk **OK**.

1. W oknie dialogowym **AzureHive** :
    1. W polu tekstowym **Nazwa użytkownika** wprowadź `admin` .
    1. W polu tekstowym **hasło** wprowadź `Thisisapassword1` .
    1. Wybierz pozycję **Połącz**.

1. Z poziomu **nawigatora** wybierz `sales` pozycję i/lub, `sales_raw` Aby wyświetlić podgląd danych. Po załadowaniu danych można eksperymentować z pulpitem nawigacyjnym, który ma zostać utworzony. Aby rozpocząć pracę z pulpitami nawigacyjnymi Power BI, zobacz następujące linki:

* [Wprowadzenie do pulpitów nawigacyjnych dla projektantów usługi Power BI](https://docs.microsoft.com/power-bi/service-dashboards)
* [Samouczek: wprowadzenie do usługi Power BI](https://docs.microsoft.com/power-bi/service-get-started)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli nie chcesz nadal korzystać z tej aplikacji, Usuń wszystkie zasoby przy użyciu następującego polecenia, aby nie były naliczone opłaty.

1. Aby usunąć grupę zasobów, wprowadź polecenie:

    ```azurecli
    az group delete -n $resourceGroup
    ```

1. Aby usunąć jednostkę usługi, wprowadź następujące polecenia:

    ```azurecli
    servicePrincipal=$(cat serviceprincipal.json | jq -r '.name')
    az ad sp delete --id $servicePrincipal
    ```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Wyodrębnianie, przekształcanie i ładowanie (ETL) na dużą skalę](./hadoop/apache-hadoop-etl-at-scale.md)
