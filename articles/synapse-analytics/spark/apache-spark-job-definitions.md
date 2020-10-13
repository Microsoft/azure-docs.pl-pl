---
title: 'Samouczek: Tworzenie definicji zadania Apache Spark w programie Synapse Studio'
description: Samouczek — Użyj usługi Azure Synapse Analytics, aby utworzyć definicje zadań platformy Spark i przesłać je do Apache Spark puli analiz usługi Azure Synapse.
author: Jejiang
ms.author: jejiang
ms.reviewer: jasonh
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: spark
ms.date: 04/15/2020
ms.openlocfilehash: 143713649afd7f9e42f517713b6b2365ee1605a3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91260278"
---
# <a name="tutorial-create-apache-spark-job-definition-in-synapse-studio"></a>Samouczek: Tworzenie definicji zadania Apache Spark w programie Synapse Studio

W tym samouczku pokazano, jak utworzyć definicje zadań Apache Spark za pomocą usługi Azure Synapse Studio, a następnie przesłać je do puli Apache Spark.

Ten samouczek obejmuje następujące zadania:
> [!div class="checklist"]
>
> - Tworzenie definicji zadania Apache Spark dla PySpark (Python)
> - Utwórz definicję zadania Apache Spark dla platformy Spark (Scala)
> - Tworzenie definicji zadania Apache Spark dla platformy .NET Spark (C#/F #)
> - Prześlij definicję zadania Apache Spark jako zadanie wsadowe
> - Dodawanie definicji zadania Apache Spark do potoku

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem pracy z tym samouczkiem upewnij się, że zostały spełnione następujące wymagania:

* Obszar roboczy analizy usługi Azure Synapse. Aby uzyskać instrukcje, zobacz [Tworzenie obszaru roboczego usługi Azure Synapse Analytics](../../machine-learning/how-to-manage-workspace.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#create-a-workspace).
* Pula Apache Spark.
* Konto magazynu ADLS Gen2. Musisz być **właścicielem danych obiektów blob magazynu** dla systemu plików ADLS Gen2, z którym chcesz współpracować. Jeśli nie, musisz dodać uprawnienie ręcznie.
* Jeśli nie chcesz używać magazynu domyślnego obszaru roboczego, Połącz wymagane konto magazynu ADLS Gen2 w programie Synapse Studio. 

## <a name="create-an-apache-spark-job-definition-for-pyspark-python"></a>Tworzenie definicji zadania Apache Spark dla PySpark (Python)

W tej sekcji utworzysz definicję zadania Apache Spark dla PySpark (Python).

1. Otwórz [usługę Azure Synapse Studio](https://web.azuresynapse.net/).

2. Możesz przejść do [plików przykładowych, aby utworzyć Apache Spark definicje zadań](https://github.com/Azure-Samples/Synapse/tree/master/Spark/Python) , aby pobrać **przykładowe pliki dla python.zip**, rozpakować skompresowany pakiet i wyodrębnić pliki **WORDCOUNT.py** i **shakespeare.txt** . 

     ![pliki przykładowe](./media/apache-spark-job-definitions/sample-files.png)

3. Wybierz pozycję **dane**   ->  **połączone**   ->  **Azure Data Lake Storage Gen2**i przekaż **WORDCOUNT.py**   i **shakespeare.txt** do systemu plików ADLS Gen2. 

     ![Przekaż plik Python](./media/apache-spark-job-definitions/upload-python-file.png)

4. Wybierz pozycję **opracowywanie** centrum, wybierz ikonę "+", a następnie wybierz pozycję **Definicja zadania platformy Spark** , aby utworzyć nową definicję zadania platformy Spark. 

     ![Utwórz nową definicję dla języka Python](./media/apache-spark-job-definitions/create-new-definition.png)

5. Wybierz pozycję **PySpark (Python)** z listy rozwijanej Język w oknie głównym definicji zadania Apache Spark.

     ![Wybierz język Python](./media/apache-spark-job-definitions/select-python.png)

6. Uzupełnij informacje dotyczące definicji zadania Apache Spark. 

     |  Właściwość   | Opis   |  
     | ----- | ----- |  
     |Nazwa definicji zadania| Wprowadź nazwę dla definicji zadania Apache Spark. Tę nazwę można aktualizować w dowolnym momencie, dopóki nie zostanie opublikowana. <br> Northwind `job definition sample`|
     |Plik definicji głównej| Główny plik używany do zadania. Wybierz plik PR z magazynu. Możesz wybrać opcję **Przekaż plik** , aby przekazać plik do konta magazynu. <br> Northwind `abfss://…/path/to/wordcount.py`|
     |Argumenty wiersza polecenia| Opcjonalne argumenty do zadania. <br> Przykład: `abfss://…/path/to/shakespeare.txt``abfss://…/path/to/result` <br> *Uwaga: dwa argumenty dla przykładowej definicji zadania są oddzielone spacją.*|
     |Pliki referencyjne| Dodatkowe pliki używane do celów referencyjnych w pliku definicji głównej. Możesz wybrać opcję **Przekaż plik** , aby przekazać plik do konta magazynu. |
     |Pula platformy Spark| Zadanie zostanie przesłane do wybranej puli Apache Spark.|
     |Wersja platformy Spark| Wersja Apache Spark, w której jest uruchomiona Pula Apache Spark.|
     |Wykonawców| Liczba modułów wykonujących, które mają być podane w określonej puli Apache Spark dla zadania.|
     |Rozmiar wykonawcy| Liczba rdzeni i pamięci, które mają być używane dla wykonawców podanych w określonej puli Apache Spark dla tego zadania.|  
     |Rozmiar sterownika| Liczba rdzeni i pamięci, które mają być używane dla sterownika określonego w określonej puli Apache Spark dla tego zadania.|

     ![Ustawianie wartości definicji zadania platformy Spark dla języka Python](./media/apache-spark-job-definitions/create-py-definition.png)

7. Wybierz pozycję **Publikuj** , aby zapisać definicję zadania Apache Spark.

     ![Publikowanie definicji z pr](./media/apache-spark-job-definitions/publish-py-definition.png)

## <a name="create-an-apache-spark-job-definition-for-apache-sparkscala"></a>Utwórz definicję zadania Apache Spark dla Apache Spark (Scala)

W tej sekcji utworzysz definicję zadania Apache Spark dla Apache Spark (Scala).

 1. Otwórz [usługę Azure Synapse Studio](https://web.azuresynapse.net/).

 2. Możesz przejść do [plików przykładowych, aby utworzyć Apache Spark definicje zadań](https://github.com/Azure-Samples/Synapse/tree/master/Spark/Scala) , aby pobrać **przykładowe pliki dla scala.zip**, rozpakować skompresowany pakiet i wyodrębnić pliki **WORDCOUNT. jar** i **shakespeare.txt** . 
 
     ![pliki przykładowe Scala](./media/apache-spark-job-definitions/sample-files-scala.png)

 3. Wybierz pozycję **dane**   ->  **połączone**   ->  **Azure Data Lake Storage Gen2**i przekaż **WORDCOUNT. jar**   i **shakespeare.txt** do systemu plików ADLS Gen2.
 
     ![Przygotuj strukturę Scala](./media/apache-spark-job-definitions/prepare-scala-structure.png)

 4. Wybierz pozycję **opracowywanie** centrum, wybierz ikonę "+", a następnie wybierz pozycję **Definicja zadania platformy Spark** , aby utworzyć nową definicję zadania platformy Spark. (Przykładowy obraz jest taki sam jak krok 4 **tworzenia definicji zadania Apache Spark (Python) dla PySpark**).

 5. Z listy rozwijanej Język wybierz pozycję **Spark (Scala)** , a następnie w oknie głównym definicji zadania Apache Spark.

     ![Wybierz Scala](./media/apache-spark-job-definitions/select-scala.png)

 6. Uzupełnij informacje dotyczące definicji zadania Apache Spark. Możesz skopiować przykładowe informacje.

     |  Właściwość   | Opis   |  
     | ----- | ----- |  
     |Nazwa definicji zadania| Wprowadź nazwę dla definicji zadania Apache Spark. Tę nazwę można aktualizować w dowolnym momencie, dopóki nie zostanie opublikowana. <br> Northwind `scala`|
     |Plik definicji głównej| Główny plik używany do zadania. Wybierz plik JAR z magazynu. Możesz wybrać opcję **Przekaż plik** , aby przekazać plik do konta magazynu. <br> Northwind `abfss://…/path/to/wordcount.jar`|
     |Nazwa klasy głównej| W pełni kwalifikowany identyfikator lub główna Klasa, która znajduje się w głównym pliku definicji. <br> Northwind `WordCount`|
     |Argumenty wiersza polecenia| Opcjonalne argumenty do zadania. <br> Przykład: `abfss://…/path/to/shakespeare.txt``abfss://…/path/to/result` <br> *Uwaga: dwa argumenty dla przykładowej definicji zadania są oddzielone spacją.* |
     |Pliki referencyjne| Dodatkowe pliki używane do celów referencyjnych w pliku definicji głównej. Możesz wybrać opcję **Przekaż plik** , aby przekazać plik do konta magazynu.|
     |Pula platformy Spark| Zadanie zostanie przesłane do wybranej puli Apache Spark.|
     |Wersja platformy Spark| Wersja Apache Spark, w której jest uruchomiona Pula Apache Spark.|
     |Wykonawców| Liczba modułów wykonujących, które mają być podane w określonej puli Apache Spark dla zadania.|  
     |Rozmiar wykonawcy| Liczba rdzeni i pamięci, które mają być używane dla wykonawców podanych w określonej puli Apache Spark dla tego zadania.|
     |Rozmiar sterownika| Liczba rdzeni i pamięci, które mają być używane dla sterownika określonego w określonej puli Apache Spark dla tego zadania.|

     ![Ustaw wartość definicji zadania platformy Spark dla Scala](./media/apache-spark-job-definitions/create-scala-definition.png)

 7. Wybierz pozycję **Publikuj** , aby zapisać definicję zadania Apache Spark.

      ![Publikuj definicję Scala](./media/apache-spark-job-definitions/publish-scala-definition.png)

## <a name="create-an-apache-spark-job-definition-for-net-sparkcf"></a>Tworzenie definicji zadania Apache Spark dla platformy .NET Spark (C#/F #)

W tej sekcji utworzysz definicję zadania Apache Spark dla platformy .NET Spark (C#/F #).
 1. Otwórz [usługę Azure Synapse Studio](https://web.azuresynapse.net/).

 2. Możesz przejść do [plików przykładowych, aby utworzyć Apache Spark definicje zadań](https://github.com/Azure-Samples/Synapse/tree/master/Spark/DotNET) , aby pobrać **przykładowe pliki dla dotnet.zip**, rozpakować skompresowany pakiet i wyodrębnić pliki **wordcount.zip** i **shakespeare.txt** . 

     ![Przykładowy dotnet](./media/apache-spark-job-definitions/sample-dotnet.png)

 3. Wybierz pozycję **dane**   ->  **połączone**   ->  **Azure Data Lake Storage Gen2**i przekaż **wordcount.zip**   i **shakespeare.txt** do systemu plików ADLS Gen2.
 
     ![Przygotuj strukturę dotnet](./media/apache-spark-job-definitions/prepare-dotnet-structure.png)

 4. Wybierz pozycję **opracowywanie** centrum, wybierz ikonę "+", a następnie wybierz pozycję **Definicja zadania platformy Spark** , aby utworzyć nową definicję zadania platformy Spark. (Przykładowy obraz jest taki sam jak krok 4 **tworzenia definicji zadania Apache Spark (Python) dla PySpark**).

 5. Wybierz pozycję **.NET Spark (C#/f #)** z listy rozwijanej Język w oknie głównym definicji zadania Apache Spark.

     ![Wybierz dotnet](./media/apache-spark-job-definitions/select-dotnet.png)

 6. Uzupełnij informacje dotyczące definicji zadania Apache Spark. Możesz skopiować przykładowe informacje.
    
     |  Właściwość   | Opis   |  
     | ----- | ----- |  
     |Nazwa definicji zadania| Wprowadź nazwę dla definicji zadania Apache Spark. Tę nazwę można aktualizować w dowolnym momencie, dopóki nie zostanie opublikowana. <br> Northwind `dotnet`|
     |Plik definicji głównej| Główny plik używany do zadania. Wybierz plik ZIP, który zawiera aplikację platformy .NET dla Apache Spark (czyli główny plik wykonywalny, biblioteki DLL zawierające funkcje zdefiniowane przez użytkownika i inne wymagane pliki) z magazynu. Możesz wybrać opcję **Przekaż plik** , aby przekazać plik do konta magazynu. <br> Northwind `abfss://…/path/to/wordcount.zip`|
     |Główny plik wykonywalny| Główny plik wykonywalny w pliku ZIP definicji głównej. <br> Northwind `WordCount`|
     |Argumenty wiersza polecenia| Opcjonalne argumenty do zadania. <br> Przykład: `abfss://…/path/to/shakespeare.txt``abfss://…/path/to/result` <br> *Uwaga: dwa argumenty dla przykładowej definicji zadania są oddzielone spacją.* |
     |Pliki referencyjne| Dodatkowe pliki, które są używane przez węzły procesu roboczego do wykonywania programu .NET for Apache Spark, które nie znajdują się w pliku ZIP definicji głównej (to jest, zależne od Jars, dodatkowe biblioteki DLL funkcji zdefiniowanych przez użytkownika i inne pliki konfiguracyjne). Możesz wybrać opcję **Przekaż plik** , aby przekazać plik do konta magazynu.|
     |Pula platformy Spark| Zadanie zostanie przesłane do wybranej puli Apache Spark.|
     |Wersja platformy Spark| Wersja Apache Spark, w której jest uruchomiona Pula Apache Spark.|
     |Wykonawców| Liczba modułów wykonujących, które mają być podane w określonej puli Apache Spark dla zadania.|  
     |Rozmiar wykonawcy| Liczba rdzeni i pamięci, które mają być używane dla wykonawców podanych w określonej puli Apache Spark dla tego zadania.|
     |Rozmiar sterownika| Liczba rdzeni i pamięci, które mają być używane dla sterownika określonego w określonej puli Apache Spark dla tego zadania.|

     ![Ustawianie wartości definicji zadania platformy Spark dla programu dotnet](./media/apache-spark-job-definitions/create-dotnet-definition.png)

 7. Wybierz pozycję **Publikuj** , aby zapisać definicję zadania Apache Spark.

      ![Publikuj definicję dotnet](./media/apache-spark-job-definitions/publish-dotnet-definition.png)

## <a name="submit-an-apache-spark-job-definition-as-a-batch-job"></a>Prześlij definicję zadania Apache Spark jako zadanie wsadowe

Po utworzeniu definicji zadania Apache Spark można przesłać ją do puli Apache Spark. Upewnij się, że jesteś **właścicielem danych obiektów blob magazynu** ADLS Gen2 systemie plików, z którym chcesz współpracować. Jeśli nie, musisz dodać uprawnienie ręcznie.

### <a name="scenario-1-submit-apache-spark-job-definition"></a>Scenariusz 1. przesyłanie Apache Spark definicji zadania
 1. Otwórz okno definicji zadania platformy Apache Spark, wybierając je.

      ![Otwórz definicję zadania platformy Spark do przesłania ](./media/apache-spark-job-definitions/open-spark-definition.png)

 2. Wybierz przycisk **Prześlij** , aby przesłać projekt do wybranej puli Apache Spark. Możesz wybrać kartę **URL monitorowania platformy Spark** , aby zobaczyć LogQuery aplikacji Apache Spark.

    ![Wybierz przycisk Prześlij, aby przesłać definicję zadania platformy Spark](./media/apache-spark-job-definitions/submit-spark-definition.png)

    ![Okno dialogowe przesyłanie danych platformy Spark](./media/apache-spark-job-definitions/submit-definition-result.png)

### <a name="scenario-2-view-apache-spark-job-running-progress"></a>Scenariusz 2. Wyświetlanie Apache Spark zadania w toku

 1. Wybierz pozycję **monitor**, a następnie wybierz opcję **Apache Spark aplikacje** . Możesz znaleźć przesłaną aplikację Apache Spark.

     ![Wyświetl aplikację platformy Spark](./media/apache-spark-job-definitions/view-spark-application.png)

 2. Następnie wybierz aplikację Apache Spark, zostanie wyświetlone okno zadania **SparkJobDefinition** . Postęp wykonywania zadania można wyświetlić w tym miejscu.
     
     ![Wyświetl LogQuery aplikacji platformy Spark](./media/apache-spark-job-definitions/view-job-log-query.png)

### <a name="scenario-3-check-output-file"></a>Scenariusz 3: Sprawdzanie pliku wyjściowego

 1. Wybierz pozycję **dane**  ->  **połączone**  ->  **Azure Data Lake Storage Gen2** (hozhaobdbj), Otwórz utworzony wcześniej folder **wyników** , a następnie przejdź do folderu wynik i sprawdź, czy dane wyjściowe są generowane.

     ![Wyświetl plik wyjściowy](./media/apache-spark-job-definitions/view-output-file.png)

## <a name="add-an-apache-spark-job-definition-into-pipeline"></a>Dodawanie definicji zadania Apache Spark do potoku

W tej sekcji dodasz definicję zadania Apache Spark do potoku.

 1. Otwórz istniejącą definicję zadania Apache Spark.

 2. Wybierz ikonę w prawym górnym rogu Apache Spark definicji zadania, wybierz pozycję **istniejący potok**lub **Nowy potok**. Więcej informacji można znaleźć na stronie potoku.

     ![Dodaj do pipeline1](./media/apache-spark-job-definitions/add-to-pipeline01.png)

     ![Dodaj do pipeline2](./media/apache-spark-job-definitions/add-to-pipeline02.png)

## <a name="next-steps"></a>Następne kroki

Następnie możesz użyć usługi Azure Synapse Studio do tworzenia Power BI zestawów danych i zarządzania Power BI danymi. Aby dowiedzieć się więcej Power BI, przejdź do artykułu obszaru [roboczego Synapse](../quickstart-power-bi.md) , aby uzyskać więcej informacji. 

