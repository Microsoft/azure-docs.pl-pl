---
title: 'Samouczek — usługa Azure Synapse Analytics: Spark job definition for Synapse'
description: Samouczek — użyj usługi Azure Synapse Analytics, aby utworzyć definicje zadań platformy Spark i przesłać je do puli platformy Spark synapse.
author: hrasheed-msft
ms.author: jejiang
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 04/15/2020
ms.openlocfilehash: d5443a2db6f4fecbd84ef51166f44c3a6e920aee
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81422970"
---
# <a name="tutorial-use-azure-synapse-analytics-to-create-apache-spark-job-definitions-for-synapse-spark-pools"></a>Samouczek: Tworzenie definicji zadań platformy Apache Spark dla pul platformy Spark synapse za pomocą usługi Azure Synapse Analytics

W tym samouczku pokazano, jak używać usługi Azure Synapse Analytics do tworzenia definicji zadań platformy Spark, a następnie przesyłać je do puli platformy Spark synapse. Wtyczkę można używać na kilka sposobów:

* Tworzenie i przesyłanie definicji zadania platformy Spark w puli platformy Spark synapse.
* Wyświetlanie szczegółów zadania po przesłaniu.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
>
> * Tworzenie i przesyłanie definicji zadania platformy Spark w puli platformy Spark synapse.
> * Wyświetlanie szczegółów zadania po przesłaniu.

## <a name="prerequisites"></a>Wymagania wstępne

* Obszar roboczy usługi Azure Synapse Analytics. Aby uzyskać instrukcje, zobacz [Tworzenie obszaru roboczego usługi Azure Synapse Analytics](../../machine-learning/how-to-manage-workspace.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#create-a-workspace).

## <a name="get-started"></a>Rozpoczęcie pracy

Przed przesłaniem definicji zadania platformy Spark należy być właścicielem danych obiektu blob magazynu systemu plików ADLS Gen2, z którymi chcesz pracować. Jeśli nie, musisz dodać uprawnienia ręcznie.

### <a name="scenario-1-add-permission"></a>Scenariusz 1: Dodawanie uprawnień

1. Otwórz [platformę Microsoft Azure](https://ms.portal.azure.com), a następnie otwórz konto usługi Storage.

2. Kliknij pozycję **Kontenery**, a następnie utwórz **system plików**. W tym samouczku użyto regionu `sparkjob`.

    ![Kliknij przycisk Prześlij, aby przesłać definicję zadania iskry](./media/apache-spark-job-definitions/open-azure-container.png)

    ![Okno dialogowe Przesyłanie platformy Spark](./media/apache-spark-job-definitions/create-new-filesystem.png)

3. Otwórz `sparkjob`, kliknij pozycję **Kontrola dostępu (IAM),** a następnie kliknij pozycję **Dodaj** i wybierz pozycję **Dodaj przypisanie roli**.

    ![Kliknij przycisk Prześlij, aby przesłać definicję zadania iskry](./media/apache-spark-job-definitions/add-role-assignment-01.png)

    ![Kliknij przycisk Prześlij, aby przesłać definicję zadania iskry](./media/apache-spark-job-definitions/add-role-assignment-02.png)

4. Kliknij **pozycję Przypisania ról**, wejściowa nazwa użytkownika, a następnie sprawdź rolę użytkownika.

    ![Kliknij przycisk Prześlij, aby przesłać definicję zadania iskry](./media/apache-spark-job-definitions/verify-user-role.png)

### <a name="scenario-2-prepare-folder-structure"></a>Scenariusz 2: Przygotowywanie struktury folderów

Przed przesłaniem definicji zadania platformy Spark jednym z zadań, które musisz wykonać, jest przesyłanie plików do ADLS Gen2 i przygotowywanie tam struktury folderów. Używamy węzła Magazyn w Synapse Studio do przechowywania plików.

1. Otwórz [usługę Azure Synapse Analytics](https://web.azuresynapse.net/).

2. Kliknij **pozycję Dane**, wybierz pozycję Konta **magazynu**i przekaż odpowiednie pliki do systemu plików ADLS Gen2. Obsługujemy Scala, Java, .NET i Python. W tym samouczku użyto przykładu na rysunku jako demonstracji, można zmienić strukturę projektu, jak chcesz.

    ![Ustawianie wartości definicji zadania platformy Spark](./media/apache-spark-job-definitions/prepare-project-structure.png)

## <a name="create-a-spark-job-definition"></a>Tworzenie definicji zadania platformy Spark

1. Otwórz [usługę Azure Synapse Analytics](https://web.azuresynapse.net/)i wybierz pozycję **Rozwiń**.

2. Z lewego okienka wybierz **pozycję Definicje zadań platformy Spark.**

3. Kliknij węzeł **Akcje** po prawej stronie "Definicje zadań iskrzenie".

     ![Tworzenie nowej definicji zadania iskrzącego](./media/apache-spark-job-definitions/create-new-definition-01.png)

4. Z listy rozwijanej **Akcje** wybierz pozycję **Nowa definicja zadania Spark**

     ![Tworzenie nowej definicji zadania iskrzącego](./media/apache-spark-job-definitions/create-new-definition-02.png)

5. W oknie Definicja zadania Nowa platforma Spark wybierz język, a następnie podaj następujące informacje:  

   * Wybierz **język** jako **Iskrę (Scala)**.

    |  Właściwość   | Opis   |  
    | ----- | ----- |  
    |Nazwa definicji zadania| Wprowadź nazwę definicji zadania platformy Spark.  W tym samouczku użyto regionu `job definition sample`. Tę nazwę można zaktualizować w dowolnym momencie, dopóki nie zostanie opublikowana.|  
    |Główny plik definicji| Główny plik używany do zadania. Wybierz plik JAR z magazynu. Możesz wybrać **opcję Przekaż plik,** aby przekazać plik na konto magazynu. |
    |Nazwa głównej klasy| W pełni kwalifikowany identyfikator lub klasa główna znajdująca się w głównym pliku definicji.|
    |Argumenty wiersza polecenia| Opcjonalne argumenty zadania.|
    |Pliki odwołań| Dodatkowe pliki używane do odwołania w głównym pliku definicji. Możesz wybrać **opcję Przekaż plik,** aby przekazać plik na konto magazynu.|
    |Basen iskra| Zadanie zostanie przesłane do wybranej puli platformy Spark.|
    |Wersja Spark| Wersja platformy Spark, która jest uruchomiona w puli platformy Spark.|
    |Executors| Liczba wykonawców, które mają być podane w określonej puli platformy Spark dla zadania.|
    |Rozmiar wykonawcy| Liczba rdzeni i pamięci, które mają być używane dla executors podane w określonej puli Platformy Spark dla zadania.|  
    |Rozmiar sterownika| Liczba rdzeni i pamięci, które mają być używane dla sterownika podane w określonej puli platformy Spark dla zadania.|

    ![Ustawianie wartości definicji zadania platformy Spark](./media/apache-spark-job-definitions/create-scala-definition.png)

   * Wybierz **język** jako **PySpark(Python)**.

    |  Właściwość   | Opis   |  
    | ----- | ----- |  
    |Nazwa definicji zadania| Wprowadź nazwę definicji zadania platformy Spark.  W tym samouczku użyto regionu `job definition sample`. Tę nazwę można zaktualizować w dowolnym momencie, dopóki nie zostanie opublikowana.|  
    |Główny plik definicji| Główny plik używany do zadania. Wybierz plik PY z magazynu. Możesz wybrać **opcję Przekaż plik,** aby przekazać plik na konto magazynu.|
    |Argumenty wiersza polecenia| Opcjonalne argumenty zadania.|
    |Pliki odwołań| Dodatkowe pliki używane do odwołania w głównym pliku definicji. Możesz wybrać **opcję Przekaż plik,** aby przekazać plik na konto magazynu.|
    |Basen iskra| Zadanie zostanie przesłane do wybranej puli platformy Spark.|
    |Wersja Spark| Wersja platformy Spark, która jest uruchomiona w puli platformy Spark.|
    |Executors| Liczba wykonawców, które mają być podane w określonej puli platformy Spark dla zadania.|
    |Rozmiar wykonawcy| Liczba rdzeni i pamięci, które mają być używane dla executors podane w określonej puli Platformy Spark dla zadania.|  
    |Rozmiar sterownika| Liczba rdzeni i pamięci, które mają być używane dla sterownika podane w określonej puli platformy Spark dla zadania.|

    ![Ustawianie wartości definicji zadania platformy Spark](./media/apache-spark-job-definitions/create-py-definition.png)

   * Wybierz **język** jako **.NET Spark(C#/F#)**.

    |  Właściwość   | Opis   |  
    | ----- | ----- |  
    |Nazwa definicji zadania| Wprowadź nazwę definicji zadania platformy Spark.  W tym samouczku użyto regionu `job definition sample`. Tę nazwę można zaktualizować w dowolnym momencie, dopóki nie zostanie opublikowana.|  
    |Główny plik definicji| Główny plik używany do zadania. Wybierz plik ZIP zawierający aplikację .NET for Spark (czyli główny plik wykonywalny, biblioteki DLL zawierające funkcje zdefiniowane przez użytkownika i inne wymagane pliki) z magazynu. Możesz wybrać **opcję Przekaż plik,** aby przekazać plik na konto magazynu.|
    |Główny plik wykonywalny| Główny plik wykonywalny w głównym pliku ZIP definicji.|
    |Argumenty wiersza polecenia| Opcjonalne argumenty zadania.|
    |Pliki odwołań| Dodatkowe pliki potrzebne przez węzły procesu roboczego do wykonywania aplikacji .NET for Spark, która nie jest uwzględniona w głównym pliku ZIP definicji (czyli słoikach zależnych, dodatkowych bibliotekach DLL funkcji zdefiniowanych przez użytkownika i innych plikach konfiguracyjnych). Możesz wybrać **opcję Przekaż plik,** aby przekazać plik na konto magazynu.|
    |Basen iskra| Zadanie zostanie przesłane do wybranej puli platformy Spark.|
    |Wersja Spark| Wersja platformy Spark, która jest uruchomiona w puli platformy Spark.|
    |Executors| Liczba wykonawców, które mają być podane w określonej puli platformy Spark dla zadania.|
    |Rozmiar wykonawcy| Liczba rdzeni i pamięci, które mają być używane dla executors podane w określonej puli Platformy Spark dla zadania.|  
    |Rozmiar sterownika| Liczba rdzeni i pamięci, które mają być używane dla sterownika podane w określonej puli platformy Spark dla zadania.|

    ![Ustawianie wartości definicji zadania platformy Spark](./media/apache-spark-job-definitions/create-net-definition.png)

6. Wybierz **pozycję Publikuj,** aby zapisać definicję zadania platformy Spark.

    ![Publikowanie definicji zadania platformy Spark](./media/apache-spark-job-definitions/publish-net-definition.png)

## <a name="submit-a-spark-job-definition"></a>Prześlij definicję zadania platformy Spark

Po utworzeniu definicji zadania platformy Spark można przesłać je do puli platformy Spark synapse. Przed wypróbowaniem próbek **w** tej części upewnij się, że przeszedłeś przez kroki w tekach.

### <a name="scenario-1-submit-spark-job-definition"></a>Scenariusz 1: Prześlij definicję zadania platformy Spark

1. Otwórz okno definicji zadania iskry, klikając je.

      ![Otwórz definicję zadania iskry do przesłania ](./media/apache-spark-job-definitions/open-spark-definition.png)

2. Kliknij ikonę **prześlij,** aby przesłać projekt do wybranej puli iskier. Możesz kliknąć kartę **adresu URL monitorowania platformy Spark,** aby wyświetlić logquery aplikacji Spark.

    ![Kliknij przycisk Prześlij, aby przesłać definicję zadania iskry](./media/apache-spark-job-definitions/submit-spark-definition.png)

    ![Okno dialogowe Przesyłanie platformy Spark](./media/apache-spark-job-definitions/submit-definition-result.png)

### <a name="scenario-2-view-spark-job-running-progress"></a>Scenariusz 2: Wyświetl postęp uruchamiania zadania spark

1. Kliknij **pozycję Monitor**, a następnie wybierz opcję **Aplikacje Platformy Spark.** Możesz znaleźć przesłany wniosek Spark.

    ![Wyświetl aplikację Spark](./media/apache-spark-job-definitions/view-spark-application.png)

2. Następnie kliknij aplikację Spark, zostanie wyświetlone okno **LogQuery.** Postęp wykonania zadania można wyświetlić w pliku **LogQuery**.

    ![Zobacz aplikację iskrzącą LogQuery](./media/apache-spark-job-definitions/view-job-log-query.png)

### <a name="scenario-3-check-output-file"></a>Scenariusz 3: Sprawdź plik wyjściowy

 1. Kliknij **pozycję Dane**, a następnie wybierz pozycję Konta **magazynu**. Po pomyślnym uruchomieniu można przejść do magazynu ADLS Gen2 i sprawdzić, czy są generowane dane wyjściowe.

    ![Wyświetl plik wyjściowy](./media/apache-spark-job-definitions/view-output-file.png)

## <a name="next-steps"></a>Następne kroki

W tym samouczku pokazano, jak używać usługi Azure Synapse Analytics do tworzenia definicji zadań platformy Spark, a następnie przesyłać je do puli platformy Spark synapse. Następnie możesz użyć usługi Azure Synapse Analytics do tworzenia zestawów danych usługi Power BI i zarządzania danymi usługi Power BI. 

- [Łączenie się z danymi w programie Power BI Desktop](https://docs.microsoft.com/power-bi/desktop-quickstart-connect-to-data)
- [Wizualizowanie przy użyciu usługi Power BI](../sql-data-warehouse/sql-data-warehouse-get-started-visualize-with-power-bi.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
