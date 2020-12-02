---
title: Azure Data Factory — przykłady
description: Zawiera szczegółowe informacje na temat przykładów dostarczanych z usługą Azure Data Factory.
services: data-factory
documentationcenter: ''
author: dcstwh
ms.author: weetok
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: b373b5c440bf2ef2f50bf323baa2488981cdc775
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96495195"
---
# <a name="azure-data-factory---samples"></a>Azure Data Factory — przykłady
> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącej wersji usługi Data Factory, zobacz [przykłady programu PowerShell w Data Factory](../samples-powershell.md) i [przykłady kodu w galerii przykładów kodu platformy Azure](https://azure.microsoft.com/resources/samples/?service=data-factory).


## <a name="samples-on-github"></a>Przykłady w serwisie GitHub
[Repozytorium Azure-DataFactory GitHub](https://github.com/azure/azure-datafactory) zawiera kilka przykładów, które pomagają szybko uzyskać dostęp do Azure Data Factory usługi (lub) zmodyfikować skrypty i używać ich w aplikacji. Folder Samples\JSON zawiera fragmenty kodu JSON dla typowych scenariuszy.

| Sample | Opis |
|:--- |:--- |
| [Przewodnik po podajniku APD](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/ADFWalkthrough) |Ten przykład zawiera kompleksowy przewodnik dotyczący przetwarzania plików dzienników przy użyciu Azure Data Factory do przekształcania danych z plików dziennika w szczegółowe informacje. <br/><br/>W tym instruktażu potok Data Factory zbiera przykładowe dzienniki, przetwarza i wzbogaca dane z dzienników o dane referencyjne i przekształca je w celu ocenienia skuteczności kampanii marketingowej, która była ostatnio uruchomiona. |
| [Przykłady JSON](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/JSON) |Ten przykład zawiera przykłady JSON dla typowych scenariuszy. |
| [Przykład pobierania danych http](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/HttpDataDownloaderSample) |Ten przykład przedstawia pobieranie danych z punktu końcowego HTTP do usługi Azure Blob Storage przy użyciu niestandardowego działania programu .NET. |
| [Przykład działania z kropką NET w wielu domenach](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/CrossAppDomainDotNetActivitySample) |Ten przykład umożliwia utworzenie niestandardowego działania platformy .NET, które nie jest ograniczone do wersji zestawu używanych przez program do uruchamiania ADF (na przykład WindowsAzure. Storage v 4.3.0, Newtonsoft.Jsw wersji v 6.0. x itd.). |
| [Uruchamianie skryptu języka R](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample) |Ten przykład zawiera Data Factory niestandardowe działanie, które może służyć do wywoływania RScript.exe. Ten przykład działa tylko z własnym klastrem usługi HDInsight (nie na żądanie), na którym jest już zainstalowany język R. |
| [Wywoływanie zadań platformy Spark w klastrze usługi HDInsight Hadoop](../tutorial-transform-data-spark-portal.md) |Ten przykład pokazuje, jak używać działania MapReduce do wywołania programu Spark. Program Spark kopiuje dane z jednego kontenera obiektów blob platformy Azure do innego. |
| [Analiza usługi Twitter przy użyciu Azure Machine Learning Studio (klasycznej) działania oceniania partii](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/TwitterAnalysisSample-AzureMLBatchScoringActivity) |Ten przykład pokazuje, jak używać elementu AzureMLBatchScoringActivity do wywołania modelu Azure Machine Learning, który wykonuje analizę tonacji, ocenianie, prognozowanie itp. |
| [Analiza usługi Twitter przy użyciu działania niestandardowego](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/TwitterAnalysisSample-CustomC%23Activity) |Ten przykład pokazuje, jak używać niestandardowego działania platformy .NET do wywołania modelu Azure Machine Learning, który wykonuje analizę tonacji, ocenianie, prognozowanie itp. |
| [Potoki sparametryzowane dla Azure Machine Learning](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/ParameterizedPipelinesForAzureML) |Przykład zawiera kompletny kod w języku C#, który umożliwia wdrożenie N potoków do oceniania i przeszkolenie każdego z parametrów regionu, w którym lista regionów pochodzi z pliku parameters.txt, który jest dołączony do tego przykładu. |
| [Odświeżanie danych referencyjnych dla zadań Azure Stream Analytics](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/ReferenceDataRefreshForASAJobs) |Ten przykład pokazuje, jak używać Azure Data Factory i Azure Stream Analytics razem do uruchamiania zapytań z danymi referencyjnymi i konfigurowania odświeżania dla danych referencyjnych zgodnie z harmonogramem. |
| [Potok hybrydowy z lokalną usługą Hortonworks Hadoop](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/HybridPipelineWithOnPremisesHortonworksHadoop) |Przykład używa lokalnego klastra usługi Hadoop jako obiektu docelowego obliczeń do uruchamiania zadań w Data Factory tak samo jak w przypadku klastra Hadoop opartego na usłudze HDInsight w chmurze. |
| [Narzędzie konwersji JSON](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/JSONConversionTool) |To narzędzie umożliwia konwertowanie notacji JSON z wersji wcześniejszej niż 2015-07-01 — wersja zapoznawcza do najnowszej lub 2015-07-01 — wersja zapoznawcza (wartość domyślna). |
| [Przykładowy plik wejściowy języka U-SQL](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/U-SQL%20Sample%20Input%20File) |Ten plik jest przykładowym plikiem używanym przez działanie języka U-SQL. |
| [Usuń plik BLOB](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/DeleteBlobFileFolderCustomActivity) | Ten przykład przedstawia plik C#, który może być używany jako część niestandardowego działania programu .NET ADF do usuwania plików z lokalizacji źródłowej obiektu blob platformy Azure po skopiowaniu plików.|

## <a name="azure-resource-manager-templates"></a>Szablony usługi Azure Resource Manager
Poniższe szablony Azure Resource Manager dla Data Factory można znaleźć w witrynie GitHub.

| Template | Opis |
| --- | --- |
| [Kopiuj z usługi Azure Blob Storage do Azure SQL Database](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-blob-to-sql-copy) |Wdrożenie tego szablonu powoduje utworzenie usługi Azure Data Factory z potokiem, który kopiuje dane z określonego magazynu obiektów blob platformy Azure do Azure SQL Database |
| [Kopiuj z witryny Salesforce do usługi Azure Blob Storage](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-salesforce-to-blob-copy) |Wdrożenie tego szablonu powoduje utworzenie usługi Azure Data Factory z potokiem, który kopiuje dane z określonego konta usług Salesforce do magazynu obiektów blob platformy Azure. |
| [Przekształcanie danych przez uruchomienie skryptu programu Hive w klastrze usługi Azure HDInsight](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-hive-transformation) |Wdrożenie tego szablonu powoduje utworzenie usługi Azure Data Factory z potokiem, który przekształca dane przez uruchomienie przykładowego skryptu Hive w klastrze Azure HDInsight Hadoop. |

## <a name="samples-in-azure-portal"></a>Przykłady w Azure Portal
Możesz użyć kafelka **przykładowe potoki** na stronie głównej fabryki danych, aby wdrożyć przykładowe potoki i powiązane z nimi jednostki (zestawy danych i połączone usługi) w usłudze Data Factory.

1. Utwórz fabrykę danych lub Otwórz istniejącą fabrykę danych. Aby utworzyć fabrykę danych, zobacz [Kopiowanie danych z BLOB Storage do SQL Database przy użyciu Data Factory](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) .
2. W bloku **Fabryka danych** dla fabryki danych kliknij kafelek **przykładowe potoki** .

    ![Kafelek przykładowe potoki](./media/data-factory-samples/SamplePipelinesTile.png)
3. W bloku **przykładowe potoki** kliknij **próbkę** , którą chcesz wdrożyć.

    ![Blok przykładowych potoków](./media/data-factory-samples/SampleTile.png)
4. Określ ustawienia konfiguracji dla przykładu. Na przykład nazwa konta usługi Azure Storage i klucz konta, logiczna Nazwa programu SQL Server, baza danych, identyfikator użytkownika i hasło itp.

    ![Przykładowy blok](./media/data-factory-samples/SampleBlade.png)
5. Po określeniu ustawień konfiguracji kliknij pozycję **Utwórz** , aby utworzyć/wdrożyć przykładowe potoki i połączone usługi/tabele używane przez potoki.
6. Zobaczysz stan wdrożenia na kafelku przykładowym, który został kliknięty wcześniej w bloku **przykładowe potoki** .

    ![Stan wdrożenia](./media/data-factory-samples/DeploymentStatus.png)
7. Gdy na kafelku przykładu zostanie wyświetlony komunikat **wdrażanie zakończyło się pomyślnie** , Zamknij blok **przykładowe potoki** .  
8. W bloku **Fabryka danych** zobaczysz, że połączone usługi, zestawy danych i potoki są dodawane do fabryki danych.  

    ![Blok Fabryka danych](./media/data-factory-samples/DataFactoryBladeAfter.png)

## <a name="samples-in-visual-studio"></a>Przykłady w programie Visual Studio
### <a name="prerequisites"></a>Wymagania wstępne
Na komputerze muszą być zainstalowane następujące elementy:

* Visual Studio 2013 lub Visual Studio 2015
* Pobierz zestaw Azure SDK dla programu Visual Studio 2013 lub Visual Studio 2015. Przejdź do [strony plików do pobrania Azure](https://azure.microsoft.com/downloads/) i kliknij pozycję **VS 2013** lub **VS 2015** w sekcji **.NET**.
* Pobierz najnowszą wtyczkę usługi Azure Data Factory dla programu Visual Studio: [VS 2013](https://visualstudiogallery.msdn.microsoft.com/754d998c-8f92-4aa7-835b-e89c8c954aa5) lub [VS 2015](https://visualstudiogallery.msdn.microsoft.com/371a4cf9-0093-40fa-b7dd-be3c74f49005). Jeśli używasz Visual Studio 2013, możesz także zaktualizować wtyczkę, wykonując następujące czynności: w menu kliknij kolejno opcje **Narzędzia**  ->  **rozszerzenia i aktualizacje**  ->  **online**  ->  **Galeria Visual Studio**  ->  **Microsoft Azure Data Factory Tools for Visual Studio**  ->  **Update**.

### <a name="use-data-factory-templates"></a>Korzystanie z szablonów Data Factory
1. Kliknij menu **plik** , wskaż polecenie **Nowy**, a następnie kliknij pozycję **projekt**.
2. W oknie dialogowym **Nowy projekt** wykonaj następujące kroki:

   1. Wybierz pozycję **DataFactory** w obszarze **Szablony**.
   2. W okienku po prawej stronie wybierz pozycję **szablony Data Factory** .
   3. Wprowadź **nazwę** dla projektu.
   4. Wybierz **lokalizację** dla projektu.
   5. Kliknij przycisk **OK**.

      ![Okno dialogowe Nowy projekt](./media/data-factory-samples/vs-new-project-adf-templates.png)
3. W oknie dialogowym **Data Factory szablonów** wybierz przykładowy szablon z sekcji **Używanie szablonów przypadków użycia** , a następnie kliknij przycisk **dalej**. Poniższe kroki przeprowadzą Cię przez proces korzystania z szablonu **profilowania klienta** . Kroki są podobne do innych przykładów.

    ![Okno dialogowe szablony Data Factory](./media/data-factory-samples/vs-data-factory-templates-dialog.png)
4. W oknie dialogowym **konfiguracja Data Factory** kliknij przycisk **dalej** na stronie **podstawowe informacje dotyczące Data Factory** .
5. Na stronie **Konfigurowanie fabryki danych** wykonaj następujące czynności:
   1. Wybierz pozycję **Utwórz nowe Data Factory**. Możesz również wybrać opcję **Użyj istniejącej fabryki danych**.
   2. Wprowadź **nazwę** fabryki danych.
   3. Wybierz **subskrypcję platformy Azure** , w której chcesz utworzyć fabrykę danych.
   4. Wybierz **grupę zasobów** dla fabryki danych.
   5. Wybierz region **zachodnie stany USA**, **Wschodnie stany USA** i **Europa Północna** dla **regionu**.
   6. Kliknij przycisk **Dalej**.
6. Na stronie **Konfigurowanie magazynów danych** Określ istniejącą **bazę danych w Azure SQL Database** i **konto usługi Azure Storage** (lub) Utwórz bazę danych/magazyn, a następnie kliknij przycisk Dalej.
7. Na stronie **Konfigurowanie obliczeń** wybierz pozycję domyślne, a następnie kliknij przycisk **dalej**.
8. Na stronie **Podsumowanie** Przejrzyj wszystkie ustawienia, a następnie kliknij przycisk **dalej**.
9. Na stronie **stan wdrożenia** poczekaj na zakończenie wdrażania, a następnie kliknij przycisk **Zakończ**.
10. Kliknij prawym przyciskiem myszy projekt w Eksploratorze rozwiązań, a następnie kliknij polecenie **Publikuj**.
11. Jeśli zostanie wyświetlone okno dialogowe **Logowanie na koncie Microsoft** wprowadź poświadczenia dla konta z subskrypcją Azure i kliknij przycisk **Zaloguj**.
12. Powinno zostać wyświetlone następujące okno dialogowe:

    ![Okno dialogowe publikowania](./media/data-factory-build-your-first-pipeline-using-vs/publish.png)
13. Na stronie **Konfigurowanie fabryki danych** wykonaj następujące czynności:

    1. Upewnij się, że opcja **Użyj istniejącej fabryki danych** .
    2. Wybierz **fabrykę danych** wybraną podczas korzystania z szablonu.
    3. Kliknij przycisk **Dalej**, aby przejść na stronę **Publikowanie elementów**. (Naciśnij przycisk **TAB**, aby wyjść z pola nazwy, jeśli przycisk **Dalej** jest wyłączony).
14. Na stronie **Publikowanie elementów** upewnij się, że wszystkie jednostki usługi Fabryka danych zostały wybrane, i kliknij przycisk **Dalej**, aby przejść na stronę **Podsumowanie**.     
15. Przejrzyj podsumowanie i kliknij przycisk **Dalej**, aby rozpocząć proces wdrożenia oraz wyświetlić stronę **Stan wdrożenia**.
16. Na stronie **Stan wdrożenia** powinien zostać wyświetlony stan procesu wdrożenia. Po zakończeniu wdrożenia kliknij przycisk Zakończ.

Zobacz [Tworzenie pierwszej fabryki danych (Visual Studio)](data-factory-build-your-first-pipeline-using-vs.md) , aby uzyskać szczegółowe informacje o używaniu programu Visual Studio do tworzenia jednostek Data Factory i publikowania ich na platformie Azure.