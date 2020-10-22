---
title: Wywoływanie programów platformy Spark z Azure Data Factory
description: Dowiedz się, jak wywoływać programy Spark z fabryki danych platformy Azure przy użyciu działania MapReduce.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 97e2be64818888040b7e6ac3bc8861da24ebdbbd
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/22/2020
ms.locfileid: "92359955"
---
# <a name="invoke-spark-programs-from-azure-data-factory-pipelines"></a>Wywoływanie programów platformy Spark z potoków Azure Data Factory

> [!div class="op_single_selector" title1="Działania transformacji"]
> * [Działanie Hive](data-factory-hive-activity.md)
> * [Aktywność trzody chlewnej](data-factory-pig-activity.md)
> * [Działanie MapReduce](data-factory-map-reduce.md)
> * [Działanie przesyłania strumieniowego Hadoop](data-factory-hadoop-streaming-activity.md)
> * [Działanie platformy Spark](data-factory-spark.md)
> * [Działanie wykonywania wsadowego w Azure Machine Learning Studio (klasycznej)](data-factory-azure-ml-batch-execution-activity.md)
> * [Działanie aktualizacji zasobów Azure Machine Learning Studio (klasycznej)](data-factory-azure-ml-update-resource-activity.md)
> * [Działanie procedury składowanej](data-factory-stored-proc-activity.md)
> * [Data Lake Analytics działanie U-SQL](data-factory-usql-activity.md)
> * [Niestandardowe działanie platformy .NET](data-factory-use-custom-activities.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Azure Data Factory, która jest ogólnie dostępna. Jeśli używasz bieżącej wersji usługi Data Factory, zobacz [Przekształć dane przy użyciu działania Apache Spark w Data Factory](../transform-data-using-spark.md).

## <a name="introduction"></a>Wprowadzenie
Działanie platformy Spark jest jedną z [działań przekształcania danych](data-factory-data-transformation-activities.md) obsługiwanych przez Data Factory. To działanie uruchamia określony program platformy Spark w klastrze Spark w usłudze Azure HDInsight.

> [!IMPORTANT]
> - Działanie platformy Spark nie obsługuje klastrów usługi HDInsight Spark, które używają Azure Data Lake Store jako magazynu podstawowego.
> - Działanie platformy Spark obsługuje tylko istniejące (własne) klastry usługi HDInsight Spark. Nie obsługuje połączonej usługi HDInsight na żądanie.

## <a name="walkthrough-create-a-pipeline-with-a-spark-activity"></a>Przewodnik: Tworzenie potoku za pomocą działania platformy Spark
Poniżej przedstawiono typowe kroki tworzenia potoku fabryki danych przy użyciu działania platformy Spark:

* Tworzenie fabryki danych.
* Utwórz połączoną usługę Azure Storage, aby połączyć magazyn, który jest skojarzony z klastrem usługi HDInsight Spark z fabryką danych.
* Utwórz połączoną usługę HDInsight, aby połączyć klaster Spark w usłudze HDInsight z fabryką danych.
* Utwórz zestaw danych, który odwołuje się do połączonej usługi Storage. Obecnie należy określić wyjściowy zestaw danych dla działania, nawet jeśli nie są generowane żadne dane wyjściowe.
* Utwórz potok z działaniem platformy Spark, który odwołuje się do utworzonej połączonej usługi HDInsight. Działanie jest skonfigurowane z zestawem danych utworzonym w poprzednim kroku jako wyjściowy zestaw danych. Wyjściowy zestaw danych to dysk, na którym jest planowany harmonogram (co godzinę, codziennie). W związku z tym należy określić wyjściowy zestaw danych, mimo że działanie nie produkuje danych wyjściowych.

### <a name="prerequisites"></a>Wymagania wstępne
1. Utwórz konto magazynu ogólnego przeznaczenia, postępując zgodnie z instrukcjami podanymi w temacie [Tworzenie konta magazynu](../../storage/common/storage-account-create.md).

1. Utwórz klaster Spark w usłudze HDInsight, postępując zgodnie z instrukcjami podanymi w samouczku [Tworzenie klastra Spark w usłudze HDInsight](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md). Skojarz konto magazynu utworzone w kroku 1 z tym klastrem.

1. Pobierz i przejrzyj plik skryptu języka Python **test.py** znajdujący się w lokalizacji [https://adftutorialfiles.blob.core.windows.net/sparktutorial/test.py](https://adftutorialfiles.blob.core.windows.net/sparktutorial/test.py) .

1. Przekaż **test.py** do folderu **pyFiles** w kontenerze **adfspark** w magazynie obiektów BLOB. Utwórz kontener i folder, jeśli nie istnieją.

### <a name="create-a-data-factory"></a>Tworzenie fabryki danych
Aby utworzyć fabrykę danych, wykonaj następujące kroki:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

1. Wybierz pozycję **nowe**  >  **dane + analiza**  >  **Data Factory**.

1. W bloku **Nowa fabryka danych** w obszarze **Nazwa**wprowadź **SparkDF**.

   > [!IMPORTANT]
   > Nazwa fabryki danych Azure musi być globalnie unikatowa. Jeśli zobaczysz błąd "Nazwa fabryki danych SparkDF jest niedostępna", Zmień nazwę fabryki danych. Na przykład użyj yournameSparkDFdate i ponownie utwórz fabrykę danych. Aby uzyskać więcej informacji na temat reguł nazewnictwa, zobacz [Data Factory: Naming rules (Fabryka danych: reguły nazewnictwa)](data-factory-naming-rules.md).

1. W obszarze **Subskrypcja** wybierz subskrypcję platformy Azure, w ramach której chcesz utworzyć fabrykę danych.

1. Wybierz istniejącą grupę zasobów lub Utwórz grupę zasobów platformy Azure.

1. Zaznacz pole wyboru **Przypnij do pulpitu nawigacyjnego**.

1. Wybierz pozycję **Utwórz**.

   > [!IMPORTANT]
   > Aby utworzyć wystąpienia Data Factory, musisz być członkiem roli [współautor Data Factory](../../role-based-access-control/built-in-roles.md#data-factory-contributor) na poziomie subskrypcji/grupy zasobów.

1. Fabryka danych zostanie wyświetlona w trakcie tworzenia na pulpicie nawigacyjnym Azure Portal.

1. Po utworzeniu fabryki danych zostanie wyświetlona strona **Fabryka danych** z zawartością fabryki danych. Jeśli nie widzisz strony **Fabryka danych** , wybierz kafelek dla fabryki danych na pulpicie nawigacyjnym.

    ![Blok Fabryka danych](./media/data-factory-spark/data-factory-blade.png)

### <a name="create-linked-services"></a>Tworzenie połączonych usług
W tym kroku utworzysz dwie połączone usługi. Jedna usługa łączy klaster platformy Spark z fabryką danych, a druga usługa łączy magazyn z fabryką danych.

#### <a name="create-a-storage-linked-service"></a>Tworzenie połączonej usługi Storage
W tym kroku opisano łączenie konta magazynu z fabryką danych. Zestaw danych tworzony w kroku w dalszej części tego instruktażu odwołuje się do tej połączonej usługi. Połączona Usługa HDInsight zdefiniowana w następnym kroku odwołuje się do tej połączonej usługi.

1. W bloku **Fabryka danych** wybierz pozycję **Utwórz i Wdróż**. Zostanie wyświetlony Edytor Data Factory.

1. Wybierz pozycję **Nowy magazyn danych**, a następnie opcję **Azure Storage**.

   ![Nowy magazyn danych](./media/data-factory-spark/new-data-store-azure-storage-menu.png)

1. Skrypt JSON używany do tworzenia połączonej usługi magazynu zostanie wyświetlony w edytorze.

   ![AzureStorageLinkedService](./media/data-factory-build-your-first-pipeline-using-editor/azure-storage-linked-service.png)

1. Zastąp **nazwę konta** i **klucz konta** nazwą i kluczem dostępu do konta magazynu. Aby dowiedzieć się, jak uzyskać klucz dostępu do magazynu, zobacz [Zarządzanie kluczami dostępu do konta magazynu](../../storage/common/storage-account-keys-manage.md).

1. Aby wdrożyć połączoną usługę, wybierz pozycję **Wdróż** na pasku poleceń. Po pomyślnym wdrożeniu połączonej usługi okno Wersja robocza-1 zniknie. W widoku drzewa po lewej stronie pojawi się wartość **AzureStorageLinkedService**.

#### <a name="create-an-hdinsight-linked-service"></a>Tworzenie połączonej usługi HDInsight
W tym kroku utworzysz połączoną usługę HDInsight, aby połączyć klaster usługi HDInsight Spark z fabryką danych. Klaster usługi HDInsight służy do uruchamiania programu Spark określonego w działaniu Spark potoku w tym przykładzie.

1. W edytorze Data Factory wybierz pozycję **więcej**  >  **Nowy**  >  **klaster usługi HDInsight**COMPUTE.

    ![Tworzenie połączonej usługi HDInsight](media/data-factory-spark/new-hdinsight-linked-service.png)

1. Skopiuj i wklej poniższy fragment kodu do okna Wersja robocza-1. W edytorze JSON wykonaj następujące czynności:

    a. Określ identyfikator URI klastra usługi HDInsight Spark. Na przykład: `https://<sparkclustername>.azurehdinsight.net/`.

    b. Określ nazwę użytkownika, który ma dostęp do klastra Spark.

    c. Podaj hasło użytkownika.

    d. Określ połączoną usługę Storage skojarzoną z klastrem usługi HDInsight Spark. W tym przykładzie jest to AzureStorageLinkedService.

    ```json
    {
        "name": "HDInsightLinkedService",
        "properties": {
            "type": "HDInsight",
            "typeProperties": {
                "clusterUri": "https://<sparkclustername>.azurehdinsight.net/",
                "userName": "admin",
                "password": "**********",
                "linkedServiceName": "AzureStorageLinkedService"
            }
        }
    }
    ```

    > [!IMPORTANT]
    > - Działanie platformy Spark nie obsługuje klastrów usługi HDInsight Spark, które używają Azure Data Lake Store jako magazynu podstawowego.
    > - Działanie platformy Spark obsługuje tylko istniejące (własne) klastry usługi HDInsight Spark. Nie obsługuje połączonej usługi HDInsight na żądanie.

    Aby uzyskać więcej informacji na temat połączonej usługi HDInsight, zobacz [połączona Usługa HDInsight](data-factory-compute-linked-services.md#azure-hdinsight-linked-service).

1. Aby wdrożyć połączoną usługę, wybierz pozycję **Wdróż** na pasku poleceń.

### <a name="create-the-output-dataset"></a>Tworzenie wyjściowego zestawu danych
Wyjściowy zestaw danych to dysk, na którym jest planowany harmonogram (co godzinę, codziennie). W związku z tym należy określić wyjściowy zestaw danych dla działania platformy Spark w potoku, nawet wtedy, gdy działanie nie produkuje żadnych danych wyjściowych. Określanie wejściowego zestawu danych dla działania jest opcjonalne.

1. W edytorze Data Factory wybierz pozycję **więcej**  >  **Nowy zestaw danych**  >  **Azure Blob Storage**.

1. Skopiuj i wklej poniższy fragment kodu do okna Wersja robocza-1. Fragment kodu JSON definiuje zestaw danych o nazwie **OutputDataset**. Ponadto należy określić, że wyniki są przechowywane w kontenerze obiektów BLOB o nazwie **adfspark** oraz folderze o nazwie **pyFiles/Output**. Jak wspomniano wcześniej, ten zestaw danych jest fikcyjnym zestawem danych. Program Spark w tym przykładzie nie tworzy żadnych danych wyjściowych. Sekcja **dostępności** określa, że wyjściowy zestaw danych jest tworzony codziennie.

    ```json
    {
        "name": "OutputDataset",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "fileName": "sparkoutput.txt",
                "folderPath": "adfspark/pyFiles/output",
                "format": {
                    "type": "TextFormat",
                    "columnDelimiter": "\t"
                }
            },
            "availability": {
                "frequency": "Day",
                "interval": 1
            }
        }
    }
    ```
1. Aby wdrożyć zestaw danych, wybierz pozycję **Wdróż** na pasku poleceń.


### <a name="create-a-pipeline"></a>Tworzenie potoku
W tym kroku utworzysz potok z działaniem HDInsightSpark. W tym przypadku wyjściowy zestaw danych jest elementem wpływającym na ustawienia harmonogramu, więc musisz utworzyć wyjściowy zestaw danych nawet wtedy, gdy działanie nie generuje żadnych danych wyjściowych. Jeśli w działaniu nie są używane żadne dane wejściowe, możesz pominąć tworzenie zestawu danych wejściowych. W związku z tym w tym przykładzie nie określono wejściowego zestawu danych.

1. W edytorze Data Factory wybierz pozycję **więcej**  >  **nowych potoków**.

1. Zastąp skrypt w oknie wersja robocza-1 następującym skryptem:

    ```json
    {
        "name": "SparkPipeline",
        "properties": {
            "activities": [
                {
                    "type": "HDInsightSpark",
                    "typeProperties": {
                        "rootPath": "adfspark\\pyFiles",
                        "entryFilePath": "test.py",
                        "getDebugInfo": "Always"
                    },
                    "outputs": [
                        {
                            "name": "OutputDataset"
                        }
                    ],
                    "name": "MySparkActivity",
                    "linkedServiceName": "HDInsightLinkedService"
                }
            ],
            "start": "2017-02-05T00:00:00Z",
            "end": "2017-02-06T00:00:00Z"
        }
    }
    ```
    Pamiętaj o następujących kwestiach:

    a. Właściwość **Type** jest ustawiona na wartość **HDInsightSpark**.

    b. Właściwość **Właściwość RootPath** jest ustawiona na **adfspark \\ pyFiles** , gdzie adfspark jest kontenerem obiektów blob, a pyFiles jest folderem plików w tym kontenerze. W tym przykładzie magazyn obiektów BLOB jest skojarzony z klastrem Spark. Plik można przekazać do innego konta magazynu. W takim przypadku należy utworzyć połączoną usługę Storage w celu połączenia tego konta magazynu z fabryką danych. Następnie określ nazwę połączonej usługi jako wartość właściwości **sparkJobLinkedService** . Aby uzyskać więcej informacji na temat tej właściwości i innych właściwości obsługiwanych przez działanie platformy Spark, zobacz [właściwości działania platformy Spark](#spark-activity-properties).

    c. Właściwość **entryFilePath** jest ustawiona na **test.py**, która jest plikiem języka Python.

    d. Właściwość **GetDebugInfo —** jest ustawiona na **zawsze**, co oznacza, że pliki dziennika są zawsze generowane (sukces lub niepowodzenie).

    > [!IMPORTANT]
    > Zaleca się, aby nie ustawiać tej właściwości `Always` w środowisku produkcyjnym, chyba że Rozwiązywanie problemów nie jest możliwe.

    e. Sekcja **outputs** Outputs ma jeden wyjściowy zestaw danych. Musisz określić wyjściowy zestaw danych, nawet jeśli program Spark nie wygenerował żadnych danych wyjściowych. Wyjściowy zestaw danych steruje harmonogramem potoku (co godzinę, codziennie).

    Aby uzyskać więcej informacji na temat właściwości obsługiwanych przez działanie platformy Spark, zobacz sekcję [właściwości działania platformy Spark](#spark-activity-properties).

1. Aby wdrożyć potok, wybierz pozycję **Wdróż** na pasku poleceń.

### <a name="monitor-a-pipeline"></a>Monitorowanie potoku
1. W bloku **Fabryka danych** wybierz pozycję **monitorowanie & zarządzanie** , aby uruchomić aplikację monitorowania na innej karcie.

    ![Kafelek Monitorowanie i zarządzanie](media/data-factory-spark/monitor-and-manage-tile.png)

1. Zmień filtr **godzina rozpoczęcia** na wartość **2/1/2017**i wybierz pozycję **Zastosuj**.

1. Wyświetlane jest tylko jedno okno działania, ponieważ między początkiem (2017-02-01) i godzinami zakończenia (2017-02-02) potoku występuje tylko jeden dzień. Upewnij się, że wycinek danych jest w stanie **gotowe** .

    ![Monitorowanie potoku](media/data-factory-spark/monitor-and-manage-app.png)

1. Z listy **okna działania** wybierz uruchomienie działania, aby wyświetlić szczegółowe informacje o nim. Jeśli wystąpi błąd, zobaczysz szczegółowe informacje o nim w okienku po prawej stronie.

### <a name="verify-the-results"></a>Weryfikowanie wyników

1. Uruchom Jupyter Notebook dla klastra usługi HDInsight Spark, przechodząc do programu `https://CLUSTERNAME.azurehdinsight.net/jupyter` . Możesz również otworzyć pulpit nawigacyjny klastra dla klastra usługi HDInsight Spark, a następnie uruchomić Jupyter Notebook.

1. Wybierz pozycję **New**  >  **PySpark** , aby uruchomić nowy Notes.

    ![Jupyter nowy Notes](media/data-factory-spark/jupyter-new-book.png)

1. Uruchom następujące polecenie, kopiując i wklejając tekst, a następnie naciskając klawisze Shift + Enter na końcu drugiej instrukcji:

    ```sql
    %%sql

    SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"
    ```
1. Upewnij się, że dane z tabeli HVAC są widoczne.

    ![Wyniki zapytania Jupyter](media/data-factory-spark/jupyter-notebook-results.png)

<!-- Removed bookmark #run-a-hive-query-using-spark-sql since it doesn't exist in the target article -->
Aby uzyskać szczegółowe instrukcje, zobacz sekcję [Uruchamianie zapytania Spark SQL](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md).

### <a name="troubleshooting"></a>Rozwiązywanie problemów
Ponieważ GetDebugInfo — jest ustawiony na **zawsze**, w folderze pyFiles w kontenerze obiektów BLOB zostanie wyświetlony podfolder log. Plik dziennika w folderze dziennika zawiera dodatkowe informacje. Ten plik dziennika jest szczególnie przydatny w przypadku wystąpienia błędu. W środowisku produkcyjnym można ustawić go na **Niepowodzenie**.

Aby uzyskać dalsze informacje dotyczące rozwiązywania problemów, wykonaj następujące czynności:


1. Przejdź do adresu `https://<CLUSTERNAME>.azurehdinsight.net/yarnui/hn/cluster`.

    ![Aplikacja interfejsu użytkownika PRZĘDZy](media/data-factory-spark/yarnui-application.png)

1. Wybierz pozycję **dzienniki** dla jednego z prób uruchomienia.

    ![Strona aplikacji](media/data-factory-spark/yarn-applications.png)

1. Na stronie dziennika są widoczne następujące dodatkowe informacje o błędzie:

    ![Błąd dziennika](media/data-factory-spark/yarnui-application-error.png)

Poniższe sekcje zawierają informacje o jednostkach fabryki danych służące do korzystania z klastra Spark i działania platformy Spark w fabryce danych.

## <a name="spark-activity-properties"></a>Właściwości działania platformy Spark
Poniżej znajduje się przykładowa definicja JSON potoku z działaniem Spark:

```json
{
    "name": "SparkPipeline",
    "properties": {
        "activities": [
            {
                "type": "HDInsightSpark",
                "typeProperties": {
                    "rootPath": "adfspark\\pyFiles",
                    "entryFilePath": "test.py",
                    "arguments": [ "arg1", "arg2" ],
                    "sparkConfig": {
                        "spark.python.worker.memory": "512m"
                    },
                    "getDebugInfo": "Always"
                },
                "outputs": [
                    {
                        "name": "OutputDataset"
                    }
                ],
                "name": "MySparkActivity",
                "description": "This activity invokes the Spark program",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2017-02-01T00:00:00Z",
        "end": "2017-02-02T00:00:00Z"
    }
}
```

W poniższej tabeli opisano właściwości JSON używane w definicji JSON.

| Właściwość | Opis | Wymagane |
| -------- | ----------- | -------- |
| name | Nazwa działania w potoku. | Tak |
| description | Tekst opisujący działanie działania. | Nie |
| typ | Ta właściwość musi być ustawiona na HDInsightSpark. | Tak |
| linkedServiceName | Nazwa połączonej usługi HDInsight, w której jest uruchamiany program Spark. | Tak |
| Właściwość RootPath | Kontener obiektów blob i folder, który zawiera plik Spark. W nazwie pliku rozróżniana jest wielkość liter. | Tak |
| entryFilePath | Ścieżka względna do folderu głównego kodu/pakietu platformy Spark. | Tak |
| Nazwą | Główna Klasa środowiska Java/Spark aplikacji. | Nie |
| argumentu | Lista argumentów wiersza polecenia do programu Spark. | Nie |
| proxyUser | Konto użytkownika służące do personifikacji w celu wykonania programu Spark. | Nie |
| sparkConfig | Określ wartości dla właściwości konfiguracji platformy Spark wymienionych w temacie [Konfiguracja platformy Spark: właściwości aplikacji](https://spark.apache.org/docs/latest/configuration.html#available-properties). | Nie |
| GetDebugInfo — | Określa, kiedy pliki dziennika platformy Spark są kopiowane do magazynu używanego przez klaster usługi HDInsight (lub) określonego przez sparkJobLinkedService. Dozwolone wartości to None, Always lub Failure. Wartość domyślna to Brak. | Nie |
| sparkJobLinkedService | Połączona usługa Storage, która przechowuje plik zadania platformy Spark, zależności i dzienniki. Jeśli nie określisz wartości tej właściwości, zostanie użyty magazyn skojarzony z klastrem usługi HDInsight. | Nie |

## <a name="folder-structure"></a>Struktura folderów
Działanie platformy Spark nie obsługuje skryptu wbudowanego jako działań związanych z świnią i Hive. Zadania platformy Spark są również bardziej rozszerzalne niż zadania w postaci trzody chlewnej/Hive. W przypadku zadań platformy Spark można podać wiele zależności, takich jak pakiety jar (umieszczone w ścieżce klas Java), pliki Python (umieszczone na PYTHONPATH) i inne pliki.

Utwórz następującą strukturę folderów w magazynie obiektów blob, do której odwołuje się połączona Usługa HDInsight. Następnie Przekaż pliki zależne do odpowiednich podfolderów w folderze głównym przedstawionym przez **entryFilePath**. Na przykład przekazanie plików Python do podfolderu pyFiles i plików jar do podfolderu Jars folderu głównego. W czasie wykonywania Usługa Data Factory oczekuje następującej struktury folderów w magazynie obiektów blob:

| Ścieżka | Opis | Wymagane | Typ |
| ---- | ----------- | -------- | ---- |
| . | Ścieżka katalogu głównego zadania platformy Spark w połączonej usłudze Storage. | Tak | Folder |
| &lt;zdefiniowane przez użytkownika &gt; | Ścieżka wskazująca plik wpisu zadania Spark. | Tak | Plik |
| ./jars | Wszystkie pliki w tym folderze są przekazywane i umieszczane na ścieżce klas Java klastra. | Nie | Folder |
| ./pyFiles | Wszystkie pliki w tym folderze są przekazywane i umieszczane w PYTHONPATH klastra. | Nie | Folder |
| ./files | Wszystkie pliki w tym folderze są przekazywane i umieszczane w katalogu roboczym programu wykonującego. | Nie | Folder |
| ./archives | Wszystkie pliki w tym folderze są nieskompresowane. | Nie | Folder |
| ./logs | Folder, w którym są przechowywane dzienniki z klastra Spark.| Nie | Folder |

Oto przykład dla magazynu zawierającego dwa pliki zadań platformy Spark w magazynie obiektów blob, do których odwołuje się połączona Usługa HDInsight:

```
SparkJob1
    main.jar
    files
        input1.txt
        input2.txt
    jars
        package1.jar
        package2.jar
    logs

SparkJob2
    main.py
    pyFiles
        scrip1.py
        script2.py
    logs
```
