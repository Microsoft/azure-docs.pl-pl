---
title: 'Samouczek: klastry na żądanie w usłudze Azure HDInsight z Data Factory'
description: Samouczek — informacje na temat tworzenia klastrów Apache Hadoop na żądanie w usłudze HDInsight przy użyciu Azure Data Factory.
ms.service: hdinsight
ms.topic: tutorial
ms.custom: seoapr2020
ms.date: 04/24/2020
ms.openlocfilehash: 762938ebb4785a54224771e96c5bca274721dc30
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98945964"
---
# <a name="tutorial-create-on-demand-apache-hadoop-clusters-in-hdinsight-using-azure-data-factory"></a>Samouczek: tworzenie klastrów Apache Hadoop na żądanie w usłudze HDInsight przy użyciu Azure Data Factory

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

W ramach tego samouczka nauczysz się tworzyć klaster [Apache Hadoop](../hdinsight/hdinsight-overview.md#cluster-types-in-hdinsight) na żądanie w usłudze Azure HDInsight przy użyciu Azure Data Factory. Następnie można używać potoków danych w Azure Data Factory, aby uruchamiać zadania Hive i usuwać klaster. Po zakończeniu tego samouczka dowiesz się, jak `operationalize` zadanie tworzenia klastrów, uruchamiania zadań i usuwania klastra odbywa się zgodnie z harmonogramem.

Ten samouczek obejmuje następujące zadania:

> [!div class="checklist"]
> * Tworzenie konta usługi Azure Storage
> * Opis działania Azure Data Factory
> * Tworzenie fabryki danych przy użyciu Azure Portal
> * Tworzenie połączonych usług
> * Tworzenie potoku
> * Wyzwalanie potoku
> * Monitorowanie potoku
> * Sprawdzanie danych wyjściowych

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

* Program PowerShell [AZ module](/powershell/azure/) został zainstalowany.

* Nazwa główna usługi Azure Active Directory. Po utworzeniu jednostki usługi Pamiętaj o pobraniu **identyfikatora aplikacji** i **klucza uwierzytelniania** przy użyciu instrukcji w połączonym artykule. Te wartości są potrzebne w dalszej części tego samouczka. Upewnij się również, że jednostka usługi jest członkiem roli *współautora* subskrypcji lub grupy zasobów, w której tworzony jest klaster. Aby uzyskać instrukcje dotyczące pobierania wymaganych wartości i przypisywania odpowiednich ról, zobacz [Tworzenie jednostki usługi Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md).

## <a name="create-preliminary-azure-objects"></a>Tworzenie wstępnych obiektów platformy Azure

W tej sekcji utworzysz różne obiekty, które będą używane dla klastra usługi HDInsight tworzonego na żądanie. Utworzone konto magazynu będzie zawierać przykładowy skrypt HiveQL, `partitionweblogs.hql` który służy do symulowania przykładowego zadania Apache Hive działającego w klastrze.

Ta sekcja używa skryptu Azure PowerShell, aby utworzyć konto magazynu i skopiować je do wymaganych plików na koncie magazynu. Przykładowy skrypt Azure PowerShell w tej sekcji wykonuje następujące zadania:

1. Loguje się do platformy Azure.
2. Tworzy grupę zasobów platformy Azure.
3. Tworzy konto usługi Azure Storage.
4. Tworzy kontener obiektów BLOB na koncie magazynu
5. Kopiuje przykładowy skrypt HiveQL (**partitionweblogs. HQL**) kontenera obiektów BLOB. Skrypt jest dostępny pod adresem [https://hditutorialdata.blob.core.windows.net/adfhiveactivity/script/partitionweblogs.hql](https://hditutorialdata.blob.core.windows.net/adfhiveactivity/script/partitionweblogs.hql) . Przykładowy skrypt jest już dostępny w innym publicznym kontenerze obiektów BLOB. Poniższy skrypt programu PowerShell tworzy kopię tych plików na koncie usługi Azure Storage, które tworzy.

### <a name="create-storage-account-and-copy-files"></a>Tworzenie konta magazynu i kopiowanie plików

> [!IMPORTANT]  
> Określ nazwy dla grupy zasobów platformy Azure i konta usługi Azure Storage, które zostaną utworzone przez skrypt.
> Zapisz **nazwę grupy zasobów**, **nazwę konta magazynu** i **klucz konta magazynu** przedstawiony przez skrypt. Będą one potrzebne w następnej sekcji.

```powershell
$resourceGroupName = "<Azure Resource Group Name>"
$storageAccountName = "<Azure Storage Account Name>"
$location = "East US"

$sourceStorageAccountName = "hditutorialdata"  
$sourceContainerName = "adfv2hiveactivity"

$destStorageAccountName = $storageAccountName
$destContainerName = "adfgetstarted" # don't change this value.

####################################
# Connect to Azure
####################################
#region - Connect to Azure subscription
Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# If you have multiple subscriptions, set the one to use
# Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"

#endregion

####################################
# Create a resource group, storage, and container
####################################

#region - create Azure resources
Write-Host "`nCreating resource group, storage account and blob container ..." -ForegroundColor Green

New-AzResourceGroup `
    -Name $resourceGroupName `
    -Location $location

New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $destStorageAccountName `
    -Kind StorageV2 `
    -Location $location `
    -SkuName Standard_LRS `
    -EnableHttpsTrafficOnly 1

$destStorageAccountKey = (Get-AzStorageAccountKey `
    -ResourceGroupName $resourceGroupName `
    -Name $destStorageAccountName)[0].Value

$sourceContext = New-AzStorageContext `
    -StorageAccountName $sourceStorageAccountName `
    -Anonymous

$destContext = New-AzStorageContext `
    -StorageAccountName $destStorageAccountName `
    -StorageAccountKey $destStorageAccountKey

New-AzStorageContainer `
    -Name $destContainerName `
    -Context $destContext
#endregion

####################################
# Copy files
####################################
#region - copy files
Write-Host "`nCopying files ..." -ForegroundColor Green

$blobs = Get-AzStorageBlob `
    -Context $sourceContext `
    -Container $sourceContainerName `
    -Blob "hivescripts\hivescript.hql"

$blobs|Start-AzStorageBlobCopy `
    -DestContext $destContext `
    -DestContainer $destContainerName `
    -DestBlob "hivescripts\partitionweblogs.hql"

Write-Host "`nCopied files ..." -ForegroundColor Green
Get-AzStorageBlob `
    -Context $destContext `
    -Container $destContainerName
#endregion

Write-host "`nYou will use the following values:" -ForegroundColor Green
write-host "`nResource group name: $resourceGroupName"
Write-host "Storage Account Name: $destStorageAccountName"
write-host "Storage Account Key: $destStorageAccountKey"

Write-host "`nScript completed" -ForegroundColor Green
```

### <a name="verify-storage-account"></a>Weryfikuj konto magazynu

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Po lewej stronie przejdź do **wszystkich usług**  >  **Ogólne**  >  **grupy zasobów**.
1. Wybierz nazwę grupy zasobów utworzoną w skrypcie programu PowerShell. Użyj filtru, jeśli na liście znajduje się zbyt wiele grup zasobów.
1. W widoku **Przegląd** zobaczysz jeden zasób na liście, o ile nie zostanie udostępniona Grupa zasobów z innymi projektami. Ten zasób jest kontem magazynu o podanej wcześniej nazwie. Wybierz nazwę konta magazynu.
1. Wybierz kafelek **kontenery** .
1. Wybierz kontener **adfgetstarted** . Zobaczysz folder o nazwie **`hivescripts`** .
1. Otwórz folder i upewnij się, że zawiera przykładowy plik skryptu, **partitionweblogs. HQL**.

## <a name="understand-the-azure-data-factory-activity"></a>Opis działania Azure Data Factory

[Azure Data Factory](../data-factory/introduction.md) organizuje i automatyzuje przenoszenie i Przekształcanie danych. Azure Data Factory może utworzyć klaster usługi HDInsight Hadoop just-in-Time, aby przetworzyć wycinek danych wejściowych i usunąć klaster po zakończeniu przetwarzania.

W Azure Data Factory, Fabryka danych może mieć co najmniej jeden potok danych. Potok danych zawiera co najmniej jedną czynność. Istnieją dwa typy działań:

* [Działania przenoszenia danych](../data-factory/copy-activity-overview.md). Działanie przenoszenia danych służy do przenoszenia danych ze źródłowego magazynu danych do docelowego magazynu danych.
* [Działania przekształcania danych](../data-factory/transform-data.md). Do przekształcania/przetwarzania danych służą działania przekształcania danych. Działanie programu Hive w usłudze HDInsight jest jedną z działań transformacji obsługiwanych przez Data Factory. W tym samouczku używasz działania transformacji Hive.

W tym artykule opisano konfigurowanie działania programu Hive w celu utworzenia klastra Hadoop usługi HDInsight na żądanie. Gdy działanie jest uruchamiane w celu przetworzenia danych, Oto co dzieje się:

1. HDInsight An klaster Hadoop jest automatycznie tworzony w celu przetworzenia wycinka.

2. Dane wejściowe są przetwarzane przez uruchomienie skryptu HiveQL w klastrze. W tym samouczku skrypt HiveQL skojarzony z działaniem programu Hive wykonuje następujące akcje:

    * Używa istniejącej tabeli (*hivesampletable*) do utworzenia innej tabeli **HiveSampleOut**.
    * Wypełnia tabelę **HiveSampleOut** tylko określonymi kolumnami z oryginalnego *hivesampletable*.

3. Klaster usługi HDInsight Hadoop jest usuwany po zakończeniu przetwarzania i klaster jest bezczynny przez skonfigurowany czas (ustawienie timeToLive). Jeśli następny wycinek danych jest dostępny do przetworzenia w ramach tego timeToLive czasu bezczynności, ten sam klaster jest używany do przetwarzania wycinka.  

## <a name="create-a-data-factory"></a>Tworzenie fabryki danych

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

2. W menu po lewej stronie przejdź do **`+ Create a resource`**  >  **Data Factory analityczne**  >  .

    ![Azure Data Factory w portalu](./media/hdinsight-hadoop-create-linux-clusters-adf/data-factory-azure-portal.png "Azure Data Factory w portalu")

3. Wprowadź lub wybierz następujące wartości dla nowego kafelka usługi **Data Factory** :

    |Właściwość  |Wartość  |
    |---------|---------|
    |Nazwa | Wprowadź nazwę fabryki danych. Ta nazwa musi być unikatowa w skali globalnej.|
    |Wersja | Pozostaw w **wersji 2**. |
    |Subskrypcja | Wybierz swoją subskrypcję platformy Azure. |
    |Grupa zasobów | Wybierz grupę zasobów utworzoną za pomocą skryptu programu PowerShell. |
    |Lokalizacja | Lokalizacja jest automatycznie ustawiana na lokalizację określoną podczas tworzenia grupy zasobów. W tym samouczku lokalizacja jest ustawiona na **Wschodnie stany USA**. |
    |Włączanie usługi GIT|Usuń zaznaczenie tego pola.|

    ![Tworzenie Azure Data Factory przy użyciu Azure Portal](./media/hdinsight-hadoop-create-linux-clusters-adf/azure-portal-create-data-factory.png "Tworzenie Azure Data Factory przy użyciu Azure Portal")

4. Wybierz przycisk **Utwórz**. Tworzenie fabryki danych może potrwać od 2 do 4 minut.

5. Po utworzeniu fabryki danych otrzymasz powiadomienie o **pomyślnym wdrożeniu** za pomocą przycisku **Przejdź do zasobu** .  Wybierz pozycję **Przejdź do zasobu** , aby otworzyć widok domyślny Data Factory.

6. Wybierz pozycję **utwórz & monitor** , aby uruchomić Azure Data Factory Portal tworzenia i monitorowania.

    ![Omówienie portalu Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/data-factory-portal-overview.png "Przegląd Azure Data Factory")

## <a name="create-linked-services"></a>Tworzenie połączonych usług

W tej sekcji utworzysz dwie połączone usługi w fabryce danych.

* **Połączona usługa Azure Storage** , która łączy konto usługi Azure Storage z fabryką danych. Ten magazyn jest używany przez klaster usługi HDInsight na żądanie. Zawiera również skrypt Hive, który jest uruchamiany w klastrze.
* **Połączona Usługa HDInsight na żądanie**. Azure Data Factory automatycznie tworzy klaster usługi HDInsight i uruchamia skrypt Hive. Następnie usuwa klaster usługi HDInsight, gdy jest on bezczynny przez wstępnie skonfigurowany czas.

### <a name="create-an-azure-storage-linked-service"></a>Tworzenie połączonej usługi Azure Storage

1. **W lewym okienku na stronie Wprowadzenie** wybierz ikonę **autora** .

    ![Tworzenie połączonej usługi Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/data-factory-edit-tab.png "Tworzenie połączonej usługi Azure Data Factory")

2. Wybierz pozycję **połączenia** w lewym dolnym rogu okna, a następnie wybierz pozycję **+ Nowy**.

    ![Tworzenie połączeń w Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/data-factory-create-new-connection.png "Tworzenie połączeń w Azure Data Factory")

3. W oknie dialogowym **Nowa połączona usługa** wybierz pozycję **Azure Blob Storage** a następnie wybierz pozycję **Kontynuuj**.

    ![Tworzenie połączonej usługi Azure Storage dla Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-storage-linked-service.png "Tworzenie połączonej usługi Azure Storage dla Data Factory")

4. Podaj następujące wartości dla połączonej usługi Storage:

    |Właściwość |Wartość |
    |---|---|
    |Nazwa |Wprowadź `HDIStorageLinkedService`.|
    |Subskrypcja platformy Azure |Wybierz swoją subskrypcję z listy rozwijanej.|
    |Nazwa konta magazynu |Wybierz konto usługi Azure Storage utworzone jako część skryptu programu PowerShell.|

    Wybierz pozycję **Test connection** i jeśli to się powiedzie, a następnie wybierz pozycję **Utwórz**.

    ![Podaj nazwę połączonej usługi Azure Storage](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-storage-linked-service-details.png "Podaj nazwę połączonej usługi Azure Storage")

### <a name="create-an-on-demand-hdinsight-linked-service"></a>Tworzenie połączonej usługi HDInsight na żądanie

1. Wybierz ponownie przycisk **+ Nowy**, aby utworzyć kolejną połączoną usługę.

2. W oknie **Nowa połączona usługa** wybierz kartę **obliczenia** .

3. Wybierz pozycję **Azure HDInsight**, a następnie wybierz pozycję **Kontynuuj**.

    ![Tworzenie połączonej usługi HDInsight dla Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-linked-service.png "Tworzenie połączonej usługi HDInsight dla Azure Data Factory")

4. W oknie **Nowa połączona usługa** wprowadź następujące wartości i pozostaw resztę jako domyślną:

    | Właściwość | Wartość |
    | --- | --- |
    | Nazwa | Wprowadź `HDInsightLinkedService`.|
    | Typ | Wybierz pozycję **HDInsight na żądanie**. |
    | Połączona usługa Azure Storage | Wybierz pozycję `HDIStorageLinkedService`. |
    | Typ klastra | Wybierz pozycję **Hadoop** |
    | Czas wygaśnięcia | Określ czas, przez jaki klaster usługi HDInsight ma być dostępny przed automatycznym usunięciem.|
    | Identyfikator jednostki usługi | Podaj identyfikator aplikacji nazwy głównej usługi Azure Active Directory utworzonej w ramach wymagań wstępnych. |
    | Klucz jednostki usługi | Podaj klucz uwierzytelniania dla jednostki usługi Azure Active Directory. |
    | Prefiks nazwy klastra | Podaj wartość, która będzie poprzedzona prefiksem wszystkich typów klastrów tworzonych przez fabrykę danych. |
    |Subskrypcja |Wybierz swoją subskrypcję z listy rozwijanej.|
    | Wybieranie grupy zasobów | Wybierz grupę zasobów utworzoną w ramach użytego wcześniej skryptu programu PowerShell.|
    | Typ systemu operacyjnego/nazwa użytkownika SSH klastra | Wprowadź nazwę użytkownika SSH, która jest często `sshuser` . |
    | Typ systemu operacyjnego/hasło SSH dla klastra | Podaj hasło dla użytkownika SSH |
    | Typ systemu operacyjnego/nazwa użytkownika klastra | Wprowadź nazwę użytkownika klastra `admin` . |
    | Typ systemu operacyjnego/hasło klastra | Podaj hasło dla użytkownika klastra. |

    Następnie wybierz pozycję **Utwórz**.

    ![Podaj wartości dla połączonej usługi HDInsight](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-linked-service-details.png "Podaj wartości dla połączonej usługi HDInsight")

## <a name="create-a-pipeline"></a>Tworzenie potoku

1. Wybierz **+** przycisk (znak plus), a następnie wybierz pozycję **potok**.

    ![Tworzenie potoku w Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-create-pipeline.png "Tworzenie potoku w Azure Data Factory")

1. W przyborniku **działania** rozwiń węzeł **HDInsight** i przeciągnij działanie **Hive** do powierzchni projektanta potoku. Na karcie **Ogólne** Podaj nazwę działania.

    ![Dodawanie działań do Data Factory potoku](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-add-hive-pipeline.png "Dodawanie działań do Data Factory potoku")

1. Upewnij się, że wybrano działanie Hive, wybierz kartę **klaster HDI** . Z listy rozwijanej **połączona Usługa HDInsight** wybierz połączoną usługę utworzoną wcześniej, **HDInsightLinkedService** w usłudze HDInsight.

    ![Podaj szczegóły klastra usługi HDInsight dla potoku](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-hive-activity-select-hdinsight-linked-service.png "Podaj szczegóły klastra usługi HDInsight dla potoku")

1. Wybierz kartę **skrypt** i wykonaj następujące czynności:

    1. W polu **połączona usługa skryptu** wybierz pozycję **HDIStorageLinkedService** z listy rozwijanej. Ta wartość to utworzona wcześniej usługa połączonej pamięci masowej.

    1. W polu **ścieżka pliku** wybierz pozycję **Przeglądaj magazyn** i przejdź do lokalizacji, w której jest dostępny przykładowy skrypt Hive. W przypadku wcześniejszego uruchomienia skryptu programu PowerShell powinna to być lokalizacja `adfgetstarted/hivescripts/partitionweblogs.hql` .

        ![Podaj szczegóły skryptu Hive dla potoku](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-provide-script-path.png "Podaj szczegóły skryptu Hive dla potoku")

    1. W obszarze  >  **Parametry** zaawansowane wybierz opcję **`Auto-fill from script`** . Ta opcja szuka parametrów w skrypcie Hive, które wymagają wartości w czasie wykonywania.

    1. W polu tekstowym **wartość** Dodaj istniejący folder w formacie `wasbs://adfgetstarted@<StorageAccount>.blob.core.windows.net/outputfolder/` . W ścieżce jest rozróżniana wielkość liter. Ta ścieżka to miejsce, w którym będą przechowywane dane wyjściowe skryptu. `wasbs`Schemat jest niezbędny, ponieważ konta magazynu mają teraz włączony bezpieczny transfer.

        ![Podaj parametry skryptu Hive](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-provide-script-parameters.png "Podaj parametry skryptu Hive")

1. Wybierz pozycję **Weryfikuj** , aby zweryfikować potoku. Wybierz **>>** przycisk (Strzałka w prawo), aby zamknąć okno walidacji.

    ![Weryfikowanie potoku Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-validate-all.png "Weryfikowanie potoku Azure Data Factory")

1. Na koniec wybierz pozycję **Opublikuj wszystkie** , aby opublikować artefakty do Azure Data Factory.

    ![Publikowanie potoku Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-publish-pipeline.png "Publikowanie potoku Azure Data Factory")

## <a name="trigger-a-pipeline"></a>Wyzwalanie potoku

1. Na pasku narzędzi na powierzchni projektanta wybierz pozycję **Dodaj wyzwalacz wyzwalacza**  >  **teraz**.

    ![Wyzwalanie potoku Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-trigger-pipeline.png "Wyzwalanie potoku Azure Data Factory")

2. Na pasku bocznym okna podręcznego wybierz pozycję **OK** .

## <a name="monitor-a-pipeline"></a>Monitorowanie potoku

1. Przejdź do karty **Monitorowanie** po lewej stronie. Uruchomienie potoku zostanie wyświetlone na liście **Uruchomienia potoku**. Zwróć uwagę na stan uruchomienia w kolumnie **stan** .

    ![Monitorowanie potoku Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-monitor-pipeline.png "Monitorowanie potoku Azure Data Factory")

1. Wybierz pozycję **Odśwież**, aby odświeżyć stan.

1. Możesz również wybrać ikonę **Wyświetl uruchomienia działania** , aby zobaczyć przebieg działania skojarzony z potokiem. Na poniższym zrzucie ekranu zobaczysz tylko jedno uruchomienie działania, ponieważ w utworzonym potoku jest tylko jedno działanie. Aby przełączyć się z powrotem do poprzedniego widoku, wybierz pozycję **potoki** w górnej części strony.

    ![Monitorowanie działania potoku Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-monitor-pipeline-activity.png "Monitorowanie działania potoku Azure Data Factory")

## <a name="verify-the-output"></a>Sprawdzanie danych wyjściowych

1. Aby sprawdzić dane wyjściowe, w Azure Portal przejdź do konta magazynu użytego w tym samouczku. Powinny zostać wyświetlone następujące foldery lub kontenery:

    * Zobaczysz element **adfgerstarted/outputfolder** , który zawiera dane wyjściowe skryptu Hive, który został uruchomiony w ramach potoku.

    * Zobaczysz kontener **adfhdidatafactory \<linked-service-name> - \<timestamp>** . Ten kontener jest domyślną lokalizacją magazynu klastra usługi HDInsight, który został utworzony w ramach uruchomienia potoku.

    * Zobaczysz kontener **adfjobs** z dziennikami zadań Azure Data Factory.  

        ![Sprawdzanie Azure Data Factory danych wyjściowych potoku](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-verify-output.png "Sprawdzanie Azure Data Factory danych wyjściowych potoku")

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Po utworzeniu klastra usługi HDInsight na żądanie nie trzeba jawnie usuwać klastra usługi HDInsight. Klaster jest usuwany w oparciu o konfigurację podaną podczas tworzenia potoku. Nawet po usunięciu klastra konta magazynu skojarzone z klastrem nadal istnieją. Takie zachowanie jest zaprojektowane w taki sposób, aby można było zachować dane bez zmian. Jeśli jednak nie chcesz utrwalać danych, możesz usunąć utworzone konto magazynu.

Możesz również usunąć całą grupę zasobów utworzoną dla tego samouczka. Ten proces powoduje usunięcie konta magazynu i utworzonego Azure Data Factory.

### <a name="delete-the-resource-group"></a>Usuwanie grupy zasobów

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. W okienku po lewej stronie wybierz pozycję **grupy zasobów** .
1. Wybierz nazwę grupy zasobów utworzoną w skrypcie programu PowerShell. Użyj filtru, jeśli na liście znajduje się zbyt wiele grup zasobów. Spowoduje to otwarcie grupy zasobów.
1. Na kafelku **zasoby** masz domyślne konto magazynu i fabrykę danych, o ile nie zostanie udostępniona Grupa zasobów z innymi projektami.
1. Wybierz pozycję **Usuń grupę zasobów**. Spowoduje to usunięcie konta magazynu i danych przechowywanych na koncie magazynu.

    !["Azure Portal usunąć grupy zasobów"](./media/hdinsight-hadoop-create-linux-clusters-adf/delete-resource-group.png "Usuwanie grupy zasobów")

1. Wprowadź nazwę grupy zasobów, aby potwierdzić usunięcie, a następnie wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób użycia Azure Data Factory do tworzenia klastra usługi HDInsight na żądanie i uruchamiania Apache Hive zadań. Przejdź do następnego artykułu, aby dowiedzieć się, jak tworzyć klastry usługi HDInsight z konfiguracją niestandardową.

> [!div class="nextstepaction"]
> [Tworzenie klastrów usługi Azure HDInsight z konfiguracją niestandardową](hdinsight-hadoop-provision-linux-clusters.md)