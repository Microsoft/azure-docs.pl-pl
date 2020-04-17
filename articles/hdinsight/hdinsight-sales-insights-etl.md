---
title: 'Samouczek: Tworzenie kompleksowego potoku ETL w celu uzyskania szczegółowych informacji o sprzedaży w usłudze Azure HDInsight'
description: Dowiedz się, jak używać tworzenia potoków ETL za pomocą usługi Azure HDInsight w celu uzyskiwania szczegółowych informacji z danych sprzedaży przy użyciu klastrów platformy Spark na żądanie i usługi Power BI.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: tutorial
ms.custom: hdinsightactive
ms.date: 04/15/2020
ms.openlocfilehash: c213b0089af0af295d44afd38bbc5c17b6db159d
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535234"
---
# <a name="tutorial-create-an-end-to-end-data-pipeline-to-derive-sales-insights-in-azure-hdinsight"></a>Samouczek: Tworzenie kompleksowego potoku danych w celu uzyskania szczegółowych informacji o sprzedaży w usłudze Azure HDInsight

W tym samouczku skompilujesz kompleksowy potok danych, który wykonuje operacje wyodrębniania, przekształcania i ładowania (ETL). Potok [użyje apache Spark](./spark/apache-spark-overview.md) i Apache hive klastrów uruchomionych w usłudze Azure HDInsight do wykonywania zapytań i manipulowania danymi. Będziesz również używać technologii, takich jak Usługa Azure Data Lake Storage Gen2 do przechowywania danych i usługa Power BI do wizualizacji.

Ten potok danych łączy dane z różnych magazynów, usuwa niechciane dane, dołącza nowe dane i ładuje to wszystko z powrotem do magazynu, aby wizualizować szczegółowe informacje biznesowe. Dowiedz się więcej o potokach ETL w [zakresie wyodrębniania, przekształcania i ładowania (ETL) na dużą skalę](./hadoop/apache-hadoop-etl-at-scale.md).

![Architektura ETL](./media/hdinsight-sales-insights-etl/architecture.png)

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

* Interfejsu wiersza polecenia platformy Azure — co najmniej w wersji 2.2.0. Zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

* jq, procesor JSON wiersza polecenia.  Zobacz [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/).

* Członek [wbudowanej roli platformy Azure — właściciel](../role-based-access-control/built-in-roles.md).

* Jeśli do wyzwolenia potoku usługi Data Factory za pomocą programu PowerShell potrzebny jest [moduł Az](https://docs.microsoft.com/powershell/azure/overview).

* [Program Power BI Desktop](https://aka.ms/pbiSingleInstaller) do wizualizacji szczegółowych informacji biznesowych na końcu tego samouczka.

## <a name="create-resources"></a>Tworzenie zasobów

### <a name="clone-the-repository-with-scripts-and-data"></a>Klonowanie repozytorium za pomocą skryptów i danych

1. Zaloguj się do subskrypcji platformy Azure. Jeśli planujesz używać usługi Azure Cloud Shell, a następnie wybierz spróbuj **go** w prawym górnym rogu bloku kodu. W przeciwnym razie wprowadź poniższe polecenie:

    ```azurecli-interactive
    az login

    # If you have multiple subscriptions, set the one to use
    # az account set --subscription "SUBSCRIPTIONID"
    ```

1. Upewnij się, że jesteś członkiem [właściciela](../role-based-access-control/built-in-roles.md)roli platformy Azure. Zamień `user@contoso.com` na swoje konto, a następnie wprowadź polecenie:

    ```azurecli
    az role assignment list \
    --assignee "user@contoso.com" \
    --role "Owner"
    ```

    Jeśli nie zostanie zwrócony żaden rekord, nie jesteś członkiem i nie będzie można ukończyć tego samouczka.

1. Pobierz dane i skrypty dla tego samouczka z [repozytorium ETL analiz sprzedaży HDInsight](https://github.com/Azure-Samples/hdinsight-sales-insights-etl). Wprowadź następujące polecenie:

    ```bash
    git clone https://github.com/Azure-Samples/hdinsight-sales-insights-etl.git
    cd hdinsight-sales-insights-etl
    ```

1. Upewnij `salesdata scripts templates` się, że zostały utworzone. Sprawdź za pomocą następującego polecenia:

   ```bash
   ls
   ```

### <a name="deploy-azure-resources-needed-for-the-pipeline"></a>Wdrażanie zasobów platformy Azure potrzebnych do potoku

1. Dodaj uprawnienia wykonania dla wszystkich skryptów, wprowadzając:

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

    Jeśli nie masz pewności, który region określić, możesz pobrać listę obsługiwanych regionów dla subskrypcji za pomocą polecenia [AZ account list-locations.](https://docs.microsoft.com/cli/azure/account?view=azure-cli-latest#az-account-list-locations)

    Polecenie wdroży następujące zasoby:

    * Konto magazynu obiektów Blob platformy Azure. To konto będzie zawierać dane sprzedaży firmy.
    * Konto usługi Azure Data Lake Storage Gen2. To konto będzie służyć jako konto magazynu dla obu klastrów HDInsight. Dowiedz się więcej o rozwiązaniach HDInsight i data lake storage gen2 w [integracji usługi Azure HDInsight z usługą Data Lake Storage Gen2.](https://azure.microsoft.com/blog/azure-hdinsight-integration-with-data-lake-storage-gen-2-preview-acl-and-security-update/)
    * Tożsamość zarządzana przypisana przez użytkownika. To konto daje klastrom HDInsight dostęp do konta Data Lake Storage Gen2.
    * Klaster Platformy Spark Apache. Ten klaster będzie używany do czyszczenia i przekształcania nieprzetworzonych danych.
    * Klaster [interaktywnych zapytań](./interactive-query/apache-interactive-query-get-started.md) gałęzi Apache. Ten klaster umożliwi wykonywanie zapytań dotyczących danych sprzedaży i ich wizualizację za pomocą usługi Power BI.
    * Sieć wirtualna platformy Azure obsługiwana przez reguły sieciowej grupy zabezpieczeń (NSG). Ta sieć wirtualna umożliwia klastrom komunikowanie się i zabezpiecza ich komunikację.

Tworzenie klastra może potrwać około 20 minut.

Domyślnym hasłem dostępu SSH do `Thisisapassword1`klastrów jest . Jeśli chcesz zmienić hasło, przejdź `./templates/resourcesparameters_remainder.json` do pliku i `sparksshPassword`zmień `sparkClusterLoginPassword` `llapClusterLoginPassword`hasło `llapsshPassword` dla , , i parametrów.

### <a name="verify-deployment-and-collect-resource-information"></a>Weryfikowanie wdrażania i zbieranie informacji o zasobach

1. Jeśli chcesz sprawdzić stan wdrożenia, przejdź do grupy zasobów w witrynie Azure portal. W obszarze **Ustawienia**wybierz pozycję **Wdrożenia**, a następnie wdrożenie. W tym miejscu można zobaczyć zasoby, które zostały pomyślnie wdrożone i zasoby, które są nadal w toku.

1. Aby wyświetlić nazwy klastrów, wprowadź następujące polecenie:

    ```bash
    sparkClusterName=$(cat resourcesoutputs_remainder.json | jq -r '.properties.outputs.sparkClusterName.value')
    llapClusterName=$(cat resourcesoutputs_remainder.json | jq -r '.properties.outputs.llapClusterName.value')

    echo "Spark Cluster" $sparkClusterName
    echo "LLAP cluster" $llapClusterName
    ```

1. Aby wyświetlić konto magazynu platformy Azure i klucz dostępu, wprowadź następujące polecenie:

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

Usługa Azure Data Factory to narzędzie ułatwiace automatyzację potoków platformy Azure. Nie jest to jedyny sposób na wykonanie tych zadań, ale jest to świetny sposób na automatyzację procesów. Aby uzyskać więcej informacji na temat usługi Azure Data Factory, zobacz [dokumentację usługi Azure Data Factory](https://azure.microsoft.com/services/data-factory/).

Ta fabryka danych będzie miała jeden potok z dwoma działaniami:

* Pierwsze działanie skopiuje dane z magazynu obiektów Blob platformy Azure do konta magazynu usługi Data Lake Storage gen 2, aby naśladować pozyskiwania danych.
* Drugie działanie spowoduje przekształcenie danych w klastrze platformy Spark. Skrypt przekształca dane, usuwając niechciane kolumny. Dołącza również nową kolumnę, która oblicza przychody generowane przez pojedynczą transakcję.

Aby skonfigurować potok usługi Azure Data Factory, wykonaj poniższe polecenie.  Nadal powinieneś znajdować `hdinsight-sales-insights-etl` się w katalogu.

```bash
blobStorageName=$(cat resourcesoutputs_storage.json | jq -r '.properties.outputs.blobStorageName.value')
ADLSGen2StorageName=$(cat resourcesoutputs_storage.json | jq -r '.properties.outputs.adlsGen2StorageName.value')

./scripts/adf.sh $resourceGroup $ADLSGen2StorageName $blobStorageName
```

Ten skrypt wykonuje następujące czynności:

1. Tworzy jednostkę `Storage Blob Data Contributor` usługi z uprawnieniami na koncie magazynu Usługi Data Lake Storage Gen2.
1. Uzyskuje token uwierzytelniania w celu autoryzowania żądań POST do [interfejsu API REST systemu danych usługi Data Lake Storage Gen2.](https://docs.microsoft.com/rest/api/storageservices/datalakestoragegen2/filesystem/create)
1. Wypełnia rzeczywistą nazwę konta magazynu Data Lake Storage `sparktransform.py` Gen2 w plikach i. `query.hql`
1. Uzyskuje klucze magazynu dla kont magazynu usługi Data Lake Gen2 i blob magazynu.
1. Tworzy inne wdrożenie zasobów w celu utworzenia potoku usługi Azure Data Factory z skojarzonymi połączonymi usługami i działaniami. Przekazuje klucze magazynu jako parametry do pliku szablonu, dzięki czemu połączone usługi mogą uzyskać dostęp do kont magazynu poprawnie.

## <a name="run-the-data-pipeline"></a>Uruchamianie potoku danych

### <a name="trigger-the-data-factory-activities"></a>Wyzwalanie działań usługi Data Factory

Pierwsze działanie w potoku fabryki danych, który został utworzony przenosi dane z magazynu obiektów Blob do usługi Data Lake Storage Gen2. Drugie działanie stosuje transformacje platformy Spark na danych i zapisuje przekształcone pliki csv w nowej lokalizacji. Zakończenie całego potoku może potrwać kilka minut.

Aby pobrać nazwę fabryki danych, wprowadź następujące polecenie:

```azurecli
cat resourcesoutputs_adf.json | jq -r '.properties.outputs.factoryName.value'
```

Aby wyzwolić potok, można:

* Wyzwalanie potoku fabryki danych w programie PowerShell. `RESOURCEGROUP`Zamień `DataFactoryName` i odpowiednie wartości, a następnie uruchom następujące polecenia:

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

    W razie `Get-AzDataFactoryV2PipelineRun` potrzeby ponownie wykonać, aby monitorować postęp.

    Lub

* Otwórz fabrykę danych i wybierz **pozycję Autor & Monitor**. Wyzwalanie `IngestAndTransform` potoku z portalu. Aby uzyskać informacje na temat wyzwalania potoków za pośrednictwem portalu, zobacz [Tworzenie klastrów Apache Hadoop na żądanie w usłudze HDInsight przy użyciu usługi Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md#trigger-a-pipeline).

Aby sprawdzić, czy potok został uruchomiony, można wykonać jedną z następujących czynności:

* Przejdź do sekcji **Monitor** w fabryce danych za pośrednictwem portalu.
* W Eksploratorze usługi Azure Storage przejdź do konta magazynu usługi Data Lake Storage gen 2. Przejdź do `files` systemu plików, a `transformed` następnie przejdź do folderu i sprawdź jego zawartość, aby sprawdzić, czy potok zakończył się pomyślnie.

Aby uzyskać inne sposoby przekształcania danych za pomocą programu HDInsight, zobacz [ten artykuł dotyczący korzystania z notebooka Jupyter](/azure/hdinsight/spark/apache-spark-load-data-run-query).

### <a name="create-a-table-on-the-interactive-query-cluster-to-view-data-on-power-bi"></a>Tworzenie tabeli w klastrze zapytań interaktywnych w celu wyświetlania danych w usłudze Power BI

1. Skopiuj `query.hql` plik do klastra LLAP przy użyciu protokołu SCP. Wprowadź polecenie:

    ```bash
    llapClusterName=$(cat resourcesoutputs_remainder.json | jq -r '.properties.outputs.llapClusterName.value')
    scp scripts/query.hql sshuser@$llapClusterName-ssh.azurehdinsight.net:/home/sshuser/
    ```

    Przypomnienie: Domyślnym `Thisisapassword1`hasłem jest .

1. Użyj protokołu SSH, aby uzyskać dostęp do klastra LLAP. Wprowadź polecenie:

    ```bash
    ssh sshuser@$llapClusterName-ssh.azurehdinsight.net
    ```

1. Użyj następującego polecenia, aby uruchomić skrypt:

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -f query.hql
    ```

    Ten skrypt utworzy tabelę zarządzaną w klastrze zapytań interaktywnych, do której można uzyskać dostęp z usługi Power BI.

### <a name="create-a-power-bi-dashboard-from-sales-data"></a>Tworzenie pulpitu nawigacyjnego usługi Power BI na podstawie danych sprzedaży

1. Otwórz program Power BI Desktop.

1. W menu przejdź do pozycji **Pobierz dane** > **więcej...**  > Zapytanie interaktywne **usługi Azure** > **HDInsight**.

1. Wybierz przycisk **Połącz**.

1. W oknie dialogowym **zapytanie interaktywne usługi HDInsight:**
    1. W polu tekstowym **Serwer** wprowadź nazwę klastra LLAP `https://LLAPCLUSTERNAME.azurehdinsight.net`w formacie .
    1. W polu tekstowym `default`bazy **danych** wprowadź klawisz .
    1. Kliknij przycisk **OK**.

1. Z okna dialogowego **AzureHive:**
    1. W polu tekstowym Nazwa `admin` **użytkownika** wprowadź .
    1. W polu **tekstowym** `Thisisapassword1`Hasło wprowadź .
    1. Wybierz przycisk **Połącz**.

1. W **aplikacji Navigator**wybierz `sales` `sales_raw` i/lub wyświetli podgląd danych. Po załadowaniu danych można eksperymentować z pulpitem nawigacyjnym, który chcesz utworzyć. Aby rozpocząć korzystanie z pulpitów nawigacyjnych usługi Power BI, zobacz następujące łącza:

* [Wprowadzenie do pulpitów nawigacyjnych dla projektantów usługi Power BI](https://docs.microsoft.com/power-bi/service-dashboards)
* [Samouczek: Wprowadzenie do usługi Power BI](https://docs.microsoft.com/power-bi/service-get-started)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie zamierzasz nadal używać tej aplikacji, usuń wszystkie zasoby za pomocą następującego polecenia, aby nie były za nie obciążane.

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
