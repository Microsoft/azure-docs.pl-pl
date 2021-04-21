---
title: 'Samouczek: tworzenie end-to-end potoku ETL w celu uzyskania szczegółowych informacji o sprzedaży w Azure HDInsight'
description: Dowiedz się, jak tworzyć potoki ETL za pomocą Azure HDInsight, aby uzyskać szczegółowe informacje na podstawie danych sprzedaży przy użyciu klastrów platformy Spark na żądanie i Power BI.
ms.service: hdinsight
ms.topic: tutorial
ms.custom: hdinsightactive, devx-track-azurecli
ms.date: 04/15/2020
ms.openlocfilehash: 140709a0ddb548c126ceca208fdeef2db77616bf
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107761803"
---
# <a name="tutorial-create-an-end-to-end-data-pipeline-to-derive-sales-insights-in-azure-hdinsight"></a>Samouczek: tworzenie potoku danych typu end-to-end w celu uzyskania szczegółowych informacji o sprzedaży w Azure HDInsight

W tym samouczku zostanie skompilowany potok danych typu end-to-end, który wykonuje operacje wyodrębniania, przekształcania i ładowania (ETL). Potok będzie używać [klastrów Apache Spark](./spark/apache-spark-overview.md) i Apache Hive uruchomionych na Azure HDInsight do wykonywania zapytań o dane i manipulowania nimi. Użyjesz również technologii takich jak Azure Data Lake Storage Gen2 magazynu danych i Power BI wizualizacji.

Ten potok danych łączy dane z różnych magazynów, usuwa wszelkie niepożądane dane, dołącza nowe dane i ładuje je z powrotem do magazynu w celu wizualizacji szczegółowych informacji biznesowych. Przeczytaj więcej na temat potoków ETL w te [tematach Extract, transform, and load (ETL) at scale (Wyodrębnianie, przekształcanie i ładowanie) na dużą skalę.](./hadoop/apache-hadoop-etl-at-scale.md)

:::image type="content" source="./media/hdinsight-sales-insights-etl/architecture.png" alt-text="Architektura ETL" border="false":::

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

* Interfejs wiersza polecenia platformy Azure — co najmniej wersja 2.2.0. Zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure.](/cli/azure/install-azure-cli)

* jq, procesor JSON wiersza polecenia.  Zobacz [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/) .

* Członek wbudowanej [roli platformy Azure — właściciel](../role-based-access-control/built-in-roles.md).

* Jeśli używasz programu PowerShell do Data Factory potoku, będziesz potrzebować modułu [Az](/powershell/azure/).

* [Power BI Desktop](https://aka.ms/pbiSingleInstaller) w celu wizualizacji szczegółowych informacji biznesowych na końcu tego samouczka.

## <a name="create-resources"></a>Tworzenie zasobów

### <a name="clone-the-repository-with-scripts-and-data"></a>Klonowanie repozytorium za pomocą skryptów i danych

1. Zaloguj się do subskrypcji platformy Azure. Jeśli planujesz używać Azure Cloud Shell, wybierz pozycję **Wypróbuj** w prawym górnym rogu bloku kodu. W innym przypadku wprowadź poniższe polecenie:

    ```azurecli-interactive
    az login

    # If you have multiple subscriptions, set the one to use
    # az account set --subscription "SUBSCRIPTIONID"
    ```

1. Upewnij się, że jesteś członkiem roli [właściciela](../role-based-access-control/built-in-roles.md)platformy Azure. Zastąp `user@contoso.com` wartość swoim kontem, a następnie wprowadź polecenie:

    ```azurecli
    az role assignment list \
    --assignee "user@contoso.com" \
    --role "Owner"
    ```

    Jeśli żaden rekord nie zostanie zwrócony, nie jesteś członkiem i nie będziesz w stanie ukończyć tego samouczka.

1. Pobierz dane i skrypty dla tego samouczka z repozytorium [ETL usługi HDInsight Sales Insights.](https://github.com/Azure-Samples/hdinsight-sales-insights-etl) Wprowadź następujące polecenie:

    ```bash
    git clone https://github.com/Azure-Samples/hdinsight-sales-insights-etl.git
    cd hdinsight-sales-insights-etl
    ```

1. Upewnij `salesdata scripts templates` się, że utworzono. Sprawdź za pomocą następującego polecenia:

   ```bash
   ls
   ```

### <a name="deploy-azure-resources-needed-for-the-pipeline"></a>Wdrażanie zasobów platformy Azure wymaganych dla potoku

1. Dodaj uprawnienia do wykonywania dla wszystkich skryptów, wprowadzając:

    ```bash
    chmod +x scripts/*.sh
    ````

1. Ustaw zmienną dla grupy zasobów. Zastąp `RESOURCE_GROUP_NAME` element nazwą istniejącej lub nowej grupy zasobów, a następnie wprowadź polecenie:

    ```bash
    resourceGroup="RESOURCE_GROUP_NAME"
    ```

1. Uruchom skrypt. Zastąp `LOCATION` wartość żądaną wartością, a następnie wprowadź polecenie:

    ```bash
    ./scripts/resources.sh $resourceGroup LOCATION
    ```

    Jeśli nie masz pewności, który region określić, możesz pobrać listę obsługiwanych regionów dla subskrypcji za pomocą polecenia [az account list-locations.](/cli/azure/account#az_account_list_locations)

    Polecenie spowoduje wdrożenie następujących zasobów:

    * Konto usługi Azure Blob Storage. To konto będzie zawierać firmowe dane sprzedaży.
    * Konto Azure Data Lake Storage Gen2 użytkownika. To konto będzie służyć jako konto magazynu dla obu klastrów usługi HDInsight. Przeczytaj więcej na temat integracji usług HDInsight Data Lake Storage Gen2 Azure HDInsight [z Data Lake Storage Gen2](https://azure.microsoft.com/blog/azure-hdinsight-integration-with-data-lake-storage-gen-2-preview-acl-and-security-update/).
    * Tożsamość zarządzana przypisana przez użytkownika. To konto zapewnia klastrom usługi HDInsight dostęp do Data Lake Storage Gen2 usługi.
    * Klaster Apache Spark klastra. Ten klaster będzie używany do czyszczenia i przekształcania danych pierwotnych.
    * Klaster Apache Hive [Interactive Query](./interactive-query/apache-interactive-query-get-started.md) klastra. Ten klaster umożliwia wykonywanie zapytań dotyczących danych sprzedaży i wizualizowanie ich za pomocą Power BI.
    * Sieć wirtualna platformy Azure obsługiwana przez reguły sieciowej grupy zabezpieczeń. Ta sieć wirtualna umożliwia klastrom komunikowanie się i zabezpieczanie komunikacji.

Tworzenie klastra może potrwać około 20 minut.

Domyślne hasło dostępu SSH do klastrów to `Thisisapassword1` . Jeśli chcesz zmienić hasło, przejdź do pliku i zmień hasło `./templates/resourcesparameters_remainder.json` dla parametrów , , i `sparksshPassword` `sparkClusterLoginPassword` `llapClusterLoginPassword` `llapsshPassword` .

### <a name="verify-deployment-and-collect-resource-information"></a>Weryfikowanie wdrożenia i zbieranie informacji o zasobach

1. Jeśli chcesz sprawdzić stan wdrożenia, przejdź do grupy zasobów na stronie Azure Portal. W **obszarze** Ustawienia wybierz **pozycję Wdrożenia**, a następnie wdrożenie. W tym miejscu możesz zobaczyć zasoby, które zostały pomyślnie wdrożone, oraz zasoby, które są nadal w toku.

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

1. Aby wyświetlić Data Lake Storage Gen2 i klucz dostępu, wprowadź następujące polecenie:

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

Azure Data Factory to narzędzie, które pomaga zautomatyzować Azure Pipelines. Nie jest to jedyny sposób wykonywania tych zadań, ale jest to doskonały sposób automatyzacji procesów. Aby uzyskać więcej informacji na Azure Data Factory, zobacz [dokumentację Azure Data Factory .](https://azure.microsoft.com/services/data-factory/)

Ta fabryka danych będzie mieć jeden potok z dwoma działaniami:

* Pierwsze działanie spowoduje skopiowanie danych z usługi Azure Blob Storage na konto magazynu Data Lake Storage Gen 2 w celu naśladowania pozyskiwania danych.
* Drugie działanie spowoduje przekształcenie danych w klastrze Spark. Skrypt przekształca dane, usuwając niepożądane kolumny. Dołącza również nową kolumnę, która oblicza przychód generowany przez pojedynczą transakcję.

Aby skonfigurować potok Azure Data Factory, wykonaj poniższe polecenie.  Nadal powinien być w `hdinsight-sales-insights-etl` katalogu.

```bash
blobStorageName=$(cat resourcesoutputs_storage.json | jq -r '.properties.outputs.blobStorageName.value')
ADLSGen2StorageName=$(cat resourcesoutputs_storage.json | jq -r '.properties.outputs.adlsGen2StorageName.value')

./scripts/adf.sh $resourceGroup $ADLSGen2StorageName $blobStorageName
```

Ten skrypt powoduje wykonywanie następujących czynności:

1. Tworzy jednostkę usługi z `Storage Blob Data Contributor` uprawnieniami na Data Lake Storage Gen2 magazynu.
1. Uzyskuje token uwierzytelniania w celu autoryzowania żądań POST do [interfejsu API REST Data Lake Storage Gen2 plików.](https://docs.microsoft.com/rest/api/storageservices/datalakestoragegen2/filesystem/create)
1. Wypełnia rzeczywistą nazwę konta Data Lake Storage Gen2 magazynu w `sparktransform.py` plikach `query.hql` i .
1. Uzyskuje klucze magazynu dla kont Data Lake Storage Gen2 i usługi Blob Storage.
1. Tworzy kolejne wdrożenie zasobów w celu utworzenia Azure Data Factory potoku wraz ze skojarzonymi z nim połączonymi usługami i działaniami. Przekazuje klucze magazynu jako parametry do pliku szablonu, dzięki czemu połączone usługi mogą prawidłowo uzyskać dostęp do kont magazynu.

## <a name="run-the-data-pipeline"></a>Uruchamianie potoku danych

### <a name="trigger-the-data-factory-activities"></a>Wyzwalanie Data Factory działań

Pierwsze działanie w utworzonym potoku Data Factory przenosi dane z usługi Blob Storage do Data Lake Storage Gen2. Drugie działanie stosuje przekształcenia platformy Spark do danych i zapisuje przekształcone pliki CSV w nowej lokalizacji. Ukończenie całego potoku może potrwać kilka minut.

Aby pobrać nazwę Data Factory, wprowadź następujące polecenie:

```azurecli
cat resourcesoutputs_adf.json | jq -r '.properties.outputs.factoryName.value'
```

Aby wyzwolić potok, możesz:

* Wyzwalanie potoku Data Factory w programie PowerShell. Zastąp `RESOURCEGROUP` elementy i odpowiednimi `DataFactoryName` wartościami, a następnie uruchom następujące polecenia:

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

    Wykonaj ponownie w `Get-AzDataFactoryV2PipelineRun` razie potrzeby, aby monitorować postęp.

    Lub

* Otwórz fabrykę danych i wybierz **pozycję & Monitor.** `IngestAndTransform`Wyzwolić potok z portalu. Aby uzyskać informacje na temat wyzwalania potoków za pośrednictwem portalu, zobacz Create [on-demand Apache Hadoop clusters in HDInsight using Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md#trigger-a-pipeline)(Tworzenie klastrów Apache Hadoop na żądanie w umacie HDInsight przy użyciu Azure Data Factory).

Aby sprawdzić, czy potok został uruchomiony, możesz wykonać jedną z następujących czynności:

* Przejdź do sekcji **Monitorowanie** w fabryce danych za pośrednictwem portalu.
* W Eksplorator usługi Azure Storage przejdź do konta magazynu Data Lake Storage Gen2. Przejdź do systemu plików, a następnie przejdź do folderu i sprawdź jego zawartość, aby `files` `transformed` sprawdzić, czy potok zakończył się pomyślnie.

Inne sposoby przekształcania danych przy użyciu usługi HDInsight można znaleźć w [tym artykule na](/azure/hdinsight/spark/apache-spark-load-data-run-query)temat korzystania z Jupyter Notebook .

### <a name="create-a-table-on-the-interactive-query-cluster-to-view-data-on-power-bi"></a>Utwórz tabelę w klastrze Interactive Query, aby wyświetlić dane na Power BI

1. Skopiuj plik `query.hql` do klastra LLAP przy użyciu SCP. Wprowadź polecenie:

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

    Ten skrypt utworzy tabelę zarządzaną w klastrze Interactive Query, do których można uzyskać dostęp z Power BI.

### <a name="create-a-power-bi-dashboard-from-sales-data"></a>Tworzenie pulpitu nawigacyjnego Power BI danych sprzedaży

1. Otwórz program Power BI Desktop.

1. Z menu przejdź do pozycji **Pobierz dane**  >  **Więcej...**  >  **Azure**  >  **HdInsight Interactive Query**.

1. Wybierz pozycję **Połącz**.

1. W **oknie dialogowym Interactive Query HDInsight:**
    1. W polu **tekstowym** Serwer wprowadź nazwę klastra LLAP w formacie `https://LLAPCLUSTERNAME.azurehdinsight.net` .
    1. W polu **tekstowym** bazy danych wprowadź `default` .
    1. Wybierz przycisk **OK**.

1. W **oknie dialogowym AzureHive:**
    1. W polu **tekstowym** Nazwa użytkownika wprowadź `admin` .
    1. W polu **tekstowym** Hasło wprowadź `Thisisapassword1` .
    1. Wybierz pozycję **Połącz**.

1. W **nawigatorze** `sales` wybierz pozycję i/lub, aby wyświetlić podgląd `sales_raw` danych. Po załadowaniu danych możesz poeksperymentować z pulpitem nawigacyjnym, który chcesz utworzyć. Zobacz następujące linki, aby rozpocząć pracę z Power BI nawigacyjnych:

* [Wprowadzenie do pulpitów nawigacyjnych dla projektantów usługi Power BI](https://docs.microsoft.com/power-bi/service-dashboards)
* [Samouczek: wprowadzenie do usługi Power BI](https://docs.microsoft.com/power-bi/service-get-started)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli nie zamierzasz nadal korzystać z tej aplikacji, usuń wszystkie zasoby za pomocą następującego polecenia, aby nie ponosić za nie opłat.

1. Aby usunąć grupę zasobów, wprowadź polecenie:

    ```azurecli
    az group delete -n $resourceGroup
    ```

1. Aby usunąć jednostkę usługi, wprowadź polecenia:

    ```azurecli
    servicePrincipal=$(cat serviceprincipal.json | jq -r '.name')
    az ad sp delete --id $servicePrincipal
    ```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Wyodrębnianie, przekształcanie i ładowanie (ETL) na dużą skalę](./hadoop/apache-hadoop-etl-at-scale.md)
