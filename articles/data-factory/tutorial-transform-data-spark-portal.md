---
title: 'Przekształcanie danych przy użyciu platformy Spark w Azure Data Factory '
description: Ten samouczek zawiera instrukcje krok po kroku dotyczące przekształcania danych za pomocą działania platformy Spark w usłudze Azure Data Factory.
ms.service: data-factory
ms.topic: tutorial
author: nabhishek
ms.author: abnarain
ms.date: 01/10/2018
ms.openlocfilehash: 2e2a50a96402f01fe914c79d5257fc5bb4dc57a0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100377792"
---
# <a name="transform-data-in-the-cloud-by-using-a-spark-activity-in-azure-data-factory"></a>Przekształcanie danych w chmurze za pomocą działania platformy Spark w usłudze Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

W tym samouczku utworzysz potok usługi Azure Data Factory za pomocą witryny Azure Portal. Ten potok przekształca dane przy użyciu działania Spark i połączonej usługi Azure HDInsight dostępnej na żądanie. 

Ten samouczek obejmuje następujące procedury:

> [!div class="checklist"]
> * Tworzenie fabryki danych. 
> * Tworzenie potoku używającego działania platformy Spark.
> * Wyzwalanie uruchomienia potoku
> * Monitorowanie uruchomienia potoku.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* **Konto usługi Azure Storage**. Utworzysz skrypt w języku Python i plik wejściowy, a następnie przekażesz je do usługi Azure Storage. Dane wyjściowe programu platformy Spark są przechowywane na tym koncie magazynu. Klaster platformy Spark na żądanie używa tego samego konta magazynu, jako swojego podstawowego magazynu.  

> [!NOTE]
> Usługa HdInsight obsługuje tylko konta magazynu ogólnego przeznaczenia w warstwie Standardowa. Upewnij się, że konto nie jest kontem magazynu w warstwie Premium ani kontem magazynu obsługującym tylko obiekty blob.

* Zainstalowanie programu **Azure PowerShell**. Wykonaj instrukcje podane w temacie [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/install-Az-ps).


### <a name="upload-the-python-script-to-your-blob-storage-account"></a>Przekazywanie skryptu w języku Python do konta usługi Blob Storage
1. Utwórz plik w języku Python o nazwie **WordCount_Spark.py** i następującej zawartości: 

    ```python
    import sys
    from operator import add
    
    from pyspark.sql import SparkSession
    
    def main():
        spark = SparkSession\
            .builder\
            .appName("PythonWordCount")\
            .getOrCreate()
            
        lines = spark.read.text("wasbs://adftutorial@<storageaccountname>.blob.core.windows.net/spark/inputfiles/minecraftstory.txt").rdd.map(lambda r: r[0])
        counts = lines.flatMap(lambda x: x.split(' ')) \
            .map(lambda x: (x, 1)) \
            .reduceByKey(add)
        counts.saveAsTextFile("wasbs://adftutorial@<storageaccountname>.blob.core.windows.net/spark/outputfiles/wordcount")
        
        spark.stop()
    
    if __name__ == "__main__":
        main()
    ```
1. Zastąp *&lt; storageAccountName &gt;* nazwą konta usługi Azure Storage. Następnie zapisz plik. 
1. W usłudze Azure Blob Storage utwórz kontener o nazwie **adftutorial**, jeśli nie istnieje. 
1. Utwórz folder o nazwie **spark**.
1. Utwórz podfolder o nazwie **script** w folderze **spark**. 
1. Przekaż plik **WordCount_Spark.py** do podfolderu **script**. 


### <a name="upload-the-input-file"></a>Przekazywanie pliku wejściowego
1. Utwórz plik o nazwie **minecraftstory.txt** zawierający tekst. Program platformy Spark zlicza liczbę słów w tym tekście. 
1. Utwórz podfolder o nazwie **inputfiles** w folderze **spark**. 
1. Przekaż plik **minecraftstory.txt** do podfolderu **inputfiles**. 

## <a name="create-a-data-factory"></a>Tworzenie fabryki danych

1. Uruchom przeglądarkę internetową **Microsoft Edge** lub **Google Chrome**. Obecnie interfejs użytkownika usługi Data Factory jest obsługiwany tylko przez przeglądarki internetowe Microsoft Edge i Google Chrome.
1. Wybierz pozycję **Nowy** w menu po lewej stronie, wybierz pozycję **Dane + analiza**, a następnie wybierz pozycję **Data Factory**. 
   
   ![Wybór usługi Data Factory w okienku „Nowy”](./media/tutorial-transform-data-spark-portal/new-azure-data-factory-menu.png)
1. W okienku **Nowa fabryka danych** wprowadź wartość **ADFTutorialDataFactory** w polu **Nazwa**. 
      
   ![Okienko „Nowa fabryka danych”](./media/tutorial-transform-data-spark-portal/new-azure-data-factory.png)
 
   Nazwa fabryki danych Azure musi być *globalnie unikatowa*. Jeśli zostanie wyświetlony poniższy błąd, zmień nazwę fabryki danych. (Na przykład użyj elementuname **&lt; &gt; ADFTutorialDataFactory**). Artykuł [Usługa Data Factory — reguły nazewnictwa](naming-rules.md) zawiera reguły nazewnictwa artefaktów usługi Data Factory.
  
   ![Komunikat o błędzie występujący, jeśli nazwa jest niedostępna](./media/tutorial-transform-data-spark-portal/name-not-available-error.png)
1. W obszarze **Subskrypcja** wybierz subskrypcję platformy Azure, w której chcesz utworzyć fabrykę danych. 
1. W obszarze **Grupa zasobów** wykonaj jedną z następujących czynności:
     
   - Wybierz pozycję **Użyj istniejącej**, a następnie wybierz istniejącą grupę zasobów z listy rozwijanej. 
   - Wybierz pozycję **Utwórz nową**, a następnie wprowadź nazwę grupy zasobów.   
         
   W niektórych krokach w tym przewodniku szybki start przyjęto założenie, że dla grupy zasobów jest używana nazwa **ADFTutorialResourceGroup** . Informacje na temat grup zasobów znajdują się w artykule [Using resource groups to manage your Azure resources](../azure-resource-manager/management/overview.md) (Używanie grup zasobów do zarządzania zasobami platformy Azure).  
1. W obszarze **Wersja** wybierz pozycję **V2**.
1. W obszarze **Lokalizacja** wybierz lokalizację fabryki danych. 

   Aby uzyskać listę regionów platformy Azure, w których obecnie jest dostępna usługa Data Factory, wybierz dane regiony na poniższej stronie, a następnie rozwiń węzeł **Analiza**, aby zlokalizować pozycję **Data Factory**: [Produkty dostępne według regionu](https://azure.microsoft.com/global-infrastructure/services/). Magazyny danych (na przykład Azure Storage lub Azure SQL Database) i jednostki obliczeniowe (takie jak HDInsight) używane przez usługę Data Factory mogą znajdować się w innych regionach.

1. Wybierz przycisk **Utwórz**.

1. Po zakończeniu tworzenia zostanie wyświetlona strona **Fabryka danych**. Wybierz kafelek **Tworzenie i monitorowanie**, aby na osobnej karcie uruchomić aplikację interfejsu użytkownika usługi Data Factory.

    ![Strona główna fabryki danych z kafelkiem „Tworzenie i monitorowanie”](./media/tutorial-transform-data-spark-portal/data-factory-home-page.png)

## <a name="create-linked-services"></a>Tworzenie połączonych usług
W tej sekcji zredagujesz dwie połączone usługi: 
    
- **Połączona usługa Azure Storage** , która łączy konto usługi Azure Storage z fabryką danych. Ten magazyn jest używany przez klaster usługi HDInsight na żądanie. Zawiera on także skrypt platformy Spark do uruchomienia. 
- **Połączona Usługa HDInsight na żądanie**. Usługa Azure Data Factory automatycznie tworzy klaster usługi HDInsight i uruchamia program platformy Spark. Następnie usuwa klaster usługi HDInsight, gdy jest on bezczynny przez wstępnie skonfigurowany czas. 

### <a name="create-an-azure-storage-linked-service"></a>Tworzenie połączonej usługi Azure Storage

1. Na stronie **Wprowadzenie** przejdź do karty **Edycja** na lewym panelu. 

   ![Strona „Wprowadzenie”](./media/tutorial-transform-data-spark-portal/get-started-page.png)

1. Wybierz pozycję **Połączenia** w dolnej części okna, a następnie wybierz pozycję **+ Nowy**. 

   ![Przyciski do tworzenia nowego połączenia](./media/tutorial-transform-data-spark-portal/new-connection.png)
1. W oknie **Nowa połączona usługa** wybierz pozycje **Magazyn danych** > **Azure Blob Storage**, a następnie wybierz pozycję **Kontynuuj**. 

   ![Wybieranie kafelka „Azure Blob Storage”](./media/tutorial-transform-data-spark-portal/select-azure-storage.png)
1. W obszarze **Nazwa konta magazynu** wybierz nazwę z listy, a następnie wybierz pozycję **Zapisz**. 

   ![Pole do określenia nazwy konta magazynu](./media/tutorial-transform-data-spark-portal/new-azure-storage-linked-service.png)


### <a name="create-an-on-demand-hdinsight-linked-service"></a>Tworzenie połączonej usługi HDInsight na żądanie

1. Wybierz ponownie przycisk **+ Nowy**, aby utworzyć kolejną połączoną usługę. 
1. W oknie **Nowa połączona usługa** wybierz kolejno pozycje **Compute** > **Azure HDInsight**, a następnie kliknij pozycję **Kontynuuj**. 

   ![Wybieranie kafelka „Azure HDInsight”](./media/tutorial-transform-data-spark-portal/select-azure-hdinsight.png)
1. W oknie **Nowa połączona usługa** wykonaj następujące czynności: 

   a. W polu **Nazwa** wprowadź ciąg **AzureHDInsightLinkedService**.
   
   b. Upewnij się, że w polu **Typ** wybrano wartość **HDInsight na żądanie**.
   
   c. W przypadku **połączonej usługi Azure Storage** wybierz pozycję **AzureBlobStorage1**. Ta połączona usługa została utworzona wcześniej. Jeśli użyto innej nazwy, podaj w tym miejscu prawidłową nazwę. 
   
   d. W polu **Typ klastra** wybierz wartość **spark**.
   
   e. W polu **Identyfikator jednostki usługi** wprowadź identyfikator, który ma uprawnienia do tworzenia klastra usługi HDInsight. 
   
      Jednostka usługi musi być członkiem roli współautora subskrypcji lub grupy zasobów, gdzie został utworzony klaster. Aby uzyskać więcej informacji, zobacz [Create Azure Active Directory application and service principal (Tworzenie jednostki usługi i aplikacji usługi Azure Active Directory)](../active-directory/develop/howto-create-service-principal-portal.md). **Identyfikator jednostki usługi** jest odpowiednikiem *identyfikatora aplikacji*, a **klucz jednostki usługi** jest równoważny z wartością klucza *tajnego klienta*.
   
   f. W polu **Klucz jednostki usługi** wprowadź klucz. 
   
   przykład W polu **Grupa zasobów** wybierz tę samą grupę zasobów, która została użyta podczas tworzenia fabryki danych. W tej grupie zasobów jest tworzony klaster Spark. 
   
   h. Rozwiń węzeł **Typ systemu operacyjnego**.
   
   i. Podaj nazwę w polu **Nazwa użytkownika klastra**. 
   
   j. Określ wartość pola **Hasło klastra** dla użytkownika. 
   
   k. Wybierz pozycję **Zakończ**. 

   ![Ustawienia połączonej usługi HDInsight](./media/tutorial-transform-data-spark-portal/azure-hdinsight-linked-service-settings.png)

> [!NOTE]
> Usługa Azure HDInsight ogranicza całkowitą liczbę rdzeni, których możesz użyć w każdym obsługiwanym przez nią regionie platformy Azure. Dla połączonej usługi HDInsight dostępnej na żądanie zostanie utworzony klaster usługi HDInsight w tej samej lokalizacji usługi Azure Storage użytej jako jej podstawowy magazyn. Upewnij się, że masz wystarczająco duże limity przydziału dla klastra, aby można go było pomyślnie utworzyć. Aby uzyskać więcej informacji, zobacz [Set up clusters in HDInsight with Hadoop, Spark, Kafka and more (Konfigurowanie klastrów w usłudze HDInsight za pomocą platform Hadoop, Spark, Kafka i innych)](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md). 

## <a name="create-a-pipeline"></a>Tworzenie potoku

1. Wybierz **+** przycisk (znak plus), a następnie wybierz pozycję **potok** w menu.

   ![Przyciski do tworzenia nowego potoku](./media/tutorial-transform-data-spark-portal/new-pipeline-menu.png)
1. W przyborniku **Działania** rozwiń pozycję **HDInsight**. Przeciągnij działanie **Spark** z przybornika **Działania** na powierzchnię projektanta potoku. 

   ![Przeciąganie działania Spark](./media/tutorial-transform-data-spark-portal/drag-drop-spark-activity.png)
1. We właściwościach okna działania **Spark** u dołu wykonaj następujące czynności: 

   a. Przejdź do karty **Klaster usługi HDI**.
   
   b. Wybierz usługę **AzureHDInsightLinkedService** (utworzoną w ramach poprzedniej procedury). 
        
   ![Określanie połączonej usługi HDInsight](./media/tutorial-transform-data-spark-portal/select-hdinsight-linked-service.png)
1. Przejdź do karty **Skrypt/Jar** i wykonaj następujące czynności: 

   a. W obszarze **połączona usługa** wybierz pozycję **AzureBlobStorage1**.
   
   b. Wybierz pozycję **Przeglądaj magazyn**.

   ![Określenie skryptu platformy Spark na karcie „Skrypt/Jar”](./media/tutorial-transform-data-spark-portal/specify-spark-script.png)
   
   c. Przejdź do folderu **adftutorial/spark/script**, wybierz plik **WordCount_Spark.py** i wybierz pozycję **Zakończ**.      

1. Aby zweryfikować potok, wybierz przycisk **Weryfikuj** na pasku narzędzi. Wybierz **>>** przycisk (Strzałka w prawo), aby zamknąć okno walidacji. 
    
   ![Przycisk „Weryfikuj”](./media/tutorial-transform-data-spark-portal/validate-button.png)
1. Wybierz pozycję **Opublikuj wszystkie**. Interfejs użytkownika usługi Data Factory publikuje jednostki (połączone usług i potok) do usługi Azure Data Factory. 
    
   ![Przycisk „Opublikuj wszystkie”](./media/tutorial-transform-data-spark-portal/publish-button.png)


## <a name="trigger-a-pipeline-run"></a>Wyzwalanie uruchomienia potoku
Na pasku narzędzi wybierz pozycję **Dodaj wyzwalacz** , a następnie wybierz pozycję **Wyzwól teraz**. 

![Przyciski „Wyzwól” i „Wyzwól teraz”](./media/tutorial-transform-data-spark-portal/trigger-now-menu.png)

## <a name="monitor-the-pipeline-run"></a>Monitorowanie działania potoku

1. Przejdź do karty **monitorowanie** . Upewnij się, że zobaczysz uruchomienie potoku. Utworzenie klastra Spark trwa około 20 minut. 
   
1. Okresowo wybieraj pozycję **Odśwież**, aby sprawdzić stan uruchomienia potoku. 

   ![Karta monitorowania przebiegów potoków z przyciskiem „Odśwież”](./media/tutorial-transform-data-spark-portal/monitor-tab.png)

1. Aby wyświetlić uruchomienia działań skojarzone z uruchomieniem potoku, wybierz pozycję **Wyświetl uruchomienia działań** w kolumnie **Akcje**.

   ![Stan uruchomienia potoku](./media/tutorial-transform-data-spark-portal/pipeline-run-succeeded.png) 

   Możesz wrócić do widoku uruchomienia potoków, wybierając łącze **wszystkie uruchomienia potoku** w górnej części ekranu.

   ![Widok „Uruchomienia działania”](./media/tutorial-transform-data-spark-portal/activity-runs.png)

## <a name="verify-the-output"></a>Sprawdzanie danych wyjściowych
Sprawdź, czy plik wyjściowy został utworzony w folderze spark/otuputfiles/wordcount kontenera adftutorial. 

![Lokalizacja pliku wyjściowego](./media/tutorial-transform-data-spark-portal/verity-output.png)

Ten plik powinien zawierać każdy wyraz z wejściowego pliku tekstowego oraz liczbę powtórzeń wyrazu w pliku. Na przykład: 

```
(u'This', 1)
(u'a', 1)
(u'is', 1)
(u'test', 1)
(u'file', 1)
```

## <a name="next-steps"></a>Następne kroki
Potok w tym przykładzie przekształca dane przy użyciu działania Spark i połączonej usługi HDInsight dostępnej na żądanie. W tym samouczku omówiono: 

> [!div class="checklist"]
> * Tworzenie fabryki danych. 
> * Tworzenie potoku używającego działania platformy Spark.
> * Wyzwalanie uruchomienia potoku
> * Monitorowanie uruchomienia potoku.

Aby dowiedzieć się, jak przekształcać dane, uruchamiając skrypt programu Hive w klastrze usługi Azure HDInsight, który znajduje się w sieci wirtualnej, przejdź do następnego samouczka: 

> [!div class="nextstepaction"]
> [Samouczek: przekształcanie danych za pomocą programu Hive w usłudze Azure Virtual Network](tutorial-transform-data-hive-virtual-network-portal.md).





