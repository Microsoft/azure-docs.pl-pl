---
title: 'Samouczek — Apache Spark usługi Azure Synapse Analytics: Apache Spark definicję zadania dla Synapse'
description: Samouczek — Użyj usługi Azure Synapse Analytics, aby utworzyć definicje zadań platformy Spark i przesłać je do Apache Spark dla puli analiz Synapse Azure.
author: hrasheed-msft
ms.author: jejiang
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 04/15/2020
ms.openlocfilehash: 5fc9dffaa73d195c842381b6682a00e9834c0fe7
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83587939"
---
# <a name="tutorial-use-azure-synapse-analytics-to-create-apache-spark-job-definitions-for-synapse-spark-pools"></a>Samouczek: korzystanie z usługi Azure Synapse Analytics do tworzenia definicji zadań Apache Spark dla pul Synapse Spark

W tym samouczku pokazano, jak utworzyć definicje zadań platformy Spark za pomocą usługi Azure Synapse Analytics, a następnie przesłać je do puli Synapse Spark. Możesz użyć wtyczki na kilka sposobów:

* Utwórz i prześlij definicję zadania platformy Spark w puli Synapse Spark.
* Wyświetl szczegóły zadania po przeprowadzeniu.

Z tego samouczka dowiesz się, jak wykonywać następujące czynności:

> [!div class="checklist"]
>
> * Utwórz i prześlij definicję zadania platformy Spark w puli Synapse Spark.
> * Wyświetl szczegóły zadania po przeprowadzeniu.

## <a name="prerequisites"></a>Wymagania wstępne

* Obszar roboczy analizy usługi Azure Synapse. Aby uzyskać instrukcje, zobacz [Tworzenie obszaru roboczego usługi Azure Synapse Analytics](../../machine-learning/how-to-manage-workspace.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#create-a-workspace).

## <a name="get-started"></a>Wprowadzenie

Przed przesłaniem definicji zadania platformy Spark musisz być właścicielem danych obiektów blob magazynu dla systemu plików ADLS Gen2, z którym chcesz współpracować. Jeśli nie, musisz dodać uprawnienie ręcznie.

### <a name="scenario-1-add-permission"></a>Scenariusz 1: Dodawanie uprawnienia

1. Otwórz [Microsoft Azure](https://ms.portal.azure.com), a następnie otwórz konto magazynu.

2. Kliknij pozycję **kontenery**, a następnie Utwórz **system plików**. W tym samouczku użyto regionu `sparkjob`.

    ![Kliknij przycisk Prześlij, aby przesłać definicję zadania platformy Spark](./media/apache-spark-job-definitions/open-azure-container.png)

    ![Okno dialogowe przesyłanie danych platformy Spark](./media/apache-spark-job-definitions/create-new-filesystem.png)

3. Otwórz `sparkjob` program, kliknij pozycję **Access Control (IAM)**, a następnie kliknij pozycję **Dodaj** i wybierz pozycję **Dodaj przypisanie roli**.

    ![Kliknij przycisk Prześlij, aby przesłać definicję zadania platformy Spark](./media/apache-spark-job-definitions/add-role-assignment-01.png)

    ![Kliknij przycisk Prześlij, aby przesłać definicję zadania platformy Spark](./media/apache-spark-job-definitions/add-role-assignment-02.png)

4. Kliknij pozycję **przypisania ról**, wprowadź nazwę użytkownika, a następnie sprawdź pozycję rola użytkownika.

    ![Kliknij przycisk Prześlij, aby przesłać definicję zadania platformy Spark](./media/apache-spark-job-definitions/verify-user-role.png)

### <a name="scenario-2-prepare-folder-structure"></a>Scenariusz 2: Przygotowanie struktury folderów

Przed przesłaniem definicji zadania platformy Spark, jedno zadanie, które należy wykonać, przekazuje pliki do ADLS Gen2 i przygotowywania struktury folderów. W programie Synapse Studio używamy węzła Storage do przechowywania plików.

1. Otwórz [usługę Azure Synapse Analytics](https://web.azuresynapse.net/).

2. Kliknij pozycję **dane**, wybierz pozycję **konta magazynu**i przekaż odpowiednie pliki do systemu plików ADLS Gen2. Obsługujemy Scala, Java, .NET i Python. Ten samouczek używa przykładu na rysunku jako demonstracji, można zmienić strukturę projektu w miarę potrzeb.

    ![Ustaw wartość definicji zadania platformy Spark](./media/apache-spark-job-definitions/prepare-project-structure.png)

## <a name="create-a-spark-job-definition"></a>Tworzenie definicji zadania platformy Spark

1. Otwórz [usługę Azure Synapse Analytics](https://web.azuresynapse.net/)i wybierz pozycję **Utwórz**.

2. W okienku po lewej stronie wybierz pozycję **definicje zadań platformy Spark** .

3. Kliknij węzeł **Akcje** po prawej stronie "Definicje zadań platformy Spark".

     ![Utwórz nową definicję zadania platformy Spark](./media/apache-spark-job-definitions/create-new-definition-01.png)

4. Z listy rozwijanej **Akcje** wybierz pozycję **Nowa definicja zadania platformy Spark**

     ![Utwórz nową definicję zadania platformy Spark](./media/apache-spark-job-definitions/create-new-definition-02.png)

5. W oknie Nowa definicja zadania platformy Spark wybierz pozycję język, a następnie podaj następujące informacje:  

   * Wybierz **Język** jako **Spark (Scala)**.

    |  Właściwość   | Opis   |  
    | ----- | ----- |  
    |Nazwa definicji zadania| Wprowadź nazwę definicji zadania platformy Spark.  W tym samouczku użyto regionu `job definition sample`. Tę nazwę można aktualizować w dowolnym momencie, dopóki nie zostanie opublikowana.|  
    |Plik definicji głównej| Główny plik używany do zadania. Wybierz plik JAR z magazynu. Możesz wybrać opcję **Przekaż plik** , aby przekazać plik do konta magazynu. |
    |Nazwa klasy głównej| W pełni kwalifikowany identyfikator lub główna Klasa, która znajduje się w głównym pliku definicji.|
    |Argumenty wiersza polecenia| Opcjonalne argumenty do zadania.|
    |Pliki referencyjne| Dodatkowe pliki używane do celów referencyjnych w pliku definicji głównej. Możesz wybrać opcję **Przekaż plik** , aby przekazać plik do konta magazynu.|
    |Pula platformy Spark| Zadanie zostanie przesłane do wybranej puli platformy Spark.|
    |Wersja platformy Spark| Wersja platformy Spark, w której działa Pula platformy Spark.|
    |Wykonawców| Liczba modułów wykonujących, które mają być podane w określonej puli platformy Spark dla zadania.|
    |Rozmiar wykonawcy| Liczba rdzeni i pamięci, które mają być używane dla wykonawców podanych w określonej puli Spark dla tego zadania.|  
    |Rozmiar sterownika| Liczba rdzeni i pamięci, które mają być używane dla sterownika określonego w określonej puli Spark dla tego zadania.|

    ![Ustaw wartość definicji zadania platformy Spark](./media/apache-spark-job-definitions/create-scala-definition.png)

   * Wybierz **Język** jako **PySpark (Python)**.

    |  Właściwość   | Opis   |  
    | ----- | ----- |  
    |Nazwa definicji zadania| Wprowadź nazwę definicji zadania platformy Spark.  W tym samouczku użyto regionu `job definition sample`. Tę nazwę można aktualizować w dowolnym momencie, dopóki nie zostanie opublikowana.|  
    |Plik definicji głównej| Główny plik używany do zadania. Wybierz plik PR z magazynu. Możesz wybrać opcję **Przekaż plik** , aby przekazać plik do konta magazynu.|
    |Argumenty wiersza polecenia| Opcjonalne argumenty do zadania.|
    |Pliki referencyjne| Dodatkowe pliki używane do celów referencyjnych w pliku definicji głównej. Możesz wybrać opcję **Przekaż plik** , aby przekazać plik do konta magazynu.|
    |Pula platformy Spark| Zadanie zostanie przesłane do wybranej puli platformy Spark.|
    |Wersja platformy Spark| Wersja platformy Spark, w której działa Pula platformy Spark.|
    |Wykonawców| Liczba modułów wykonujących, które mają być podane w określonej puli platformy Spark dla zadania.|
    |Rozmiar wykonawcy| Liczba rdzeni i pamięci, które mają być używane dla wykonawców podanych w określonej puli Spark dla tego zadania.|  
    |Rozmiar sterownika| Liczba rdzeni i pamięci, które mają być używane dla sterownika określonego w określonej puli Spark dla tego zadania.|

    ![Ustaw wartość definicji zadania platformy Spark](./media/apache-spark-job-definitions/create-py-definition.png)

   * Wybierz **Język** jako **.NET Spark (C#/f #)**.

    |  Właściwość   | Opis   |  
    | ----- | ----- |  
    |Nazwa definicji zadania| Wprowadź nazwę definicji zadania platformy Spark.  W tym samouczku użyto regionu `job definition sample`. Tę nazwę można aktualizować w dowolnym momencie, dopóki nie zostanie opublikowana.|  
    |Plik definicji głównej| Główny plik używany do zadania. Wybierz plik ZIP, który zawiera aplikację platformy .NET dla platformy Spark (czyli główny plik wykonywalny, biblioteki DLL zawierające funkcje zdefiniowane przez użytkownika i inne wymagane pliki) z magazynu. Możesz wybrać opcję **Przekaż plik** , aby przekazać plik do konta magazynu.|
    |Główny plik wykonywalny| Główny plik wykonywalny w pliku ZIP definicji głównej.|
    |Argumenty wiersza polecenia| Opcjonalne argumenty do zadania.|
    |Pliki referencyjne| Dodatkowe pliki, które są używane przez węzły procesu roboczego do wykonywania aplikacji platformy .NET dla platformy Spark, które nie znajdują się w pliku ZIP definicji głównej (to jest, zależne Jars, dodatkowe biblioteki DLL funkcji zdefiniowane przez użytkownika i inne pliki konfiguracyjne). Możesz wybrać opcję **Przekaż plik** , aby przekazać plik do konta magazynu.|
    |Pula platformy Spark| Zadanie zostanie przesłane do wybranej puli platformy Spark.|
    |Wersja platformy Spark| Wersja platformy Spark, w której działa Pula platformy Spark.|
    |Wykonawców| Liczba modułów wykonujących, które mają być podane w określonej puli platformy Spark dla zadania.|
    |Rozmiar wykonawcy| Liczba rdzeni i pamięci, które mają być używane dla wykonawców podanych w określonej puli Spark dla tego zadania.|  
    |Rozmiar sterownika| Liczba rdzeni i pamięci, które mają być używane dla sterownika określonego w określonej puli Spark dla tego zadania.|

    ![Ustaw wartość definicji zadania platformy Spark](./media/apache-spark-job-definitions/create-net-definition.png)

6. Wybierz pozycję **Publikuj** , aby zapisać definicję zadania platformy Spark.

    ![Publikuj definicję zadania platformy Spark](./media/apache-spark-job-definitions/publish-net-definition.png)

## <a name="submit-a-spark-job-definition"></a>Prześlij definicję zadania platformy Spark

Po utworzeniu definicji zadania platformy Spark można przesłać ją do puli Synapse Spark. Przed podjęciem próby pobrania próbek w tej części upewnij się, że wykonano czynności opisane w temacie **Get-Started** .

### <a name="scenario-1-submit-spark-job-definition"></a>Scenariusz 1: przesyłanie definicji zadania platformy Spark

1. Aby otworzyć okno definicji zadania platformy Spark, kliknij je.

      ![Otwórz definicję zadania platformy Spark do przesłania ](./media/apache-spark-job-definitions/open-spark-definition.png)

2. Kliknij przycisk **Prześlij** ikonę, aby przesłać projekt do wybranej puli platformy Spark. Możesz kliknąć kartę **URL monitorowania platformy Spark** , aby zobaczyć LogQuery aplikacji platformy Spark.

    ![Kliknij przycisk Prześlij, aby przesłać definicję zadania platformy Spark](./media/apache-spark-job-definitions/submit-spark-definition.png)

    ![Okno dialogowe przesyłanie danych platformy Spark](./media/apache-spark-job-definitions/submit-definition-result.png)

### <a name="scenario-2-view-spark-job-running-progress"></a>Scenariusz 2: wyświetlenie postępu wykonywania zadania platformy Spark

1. Kliknij pozycję **monitor**, a następnie wybierz opcję **aplikacje Spark** . Przesłaną aplikację platformy Spark można znaleźć.

    ![Wyświetl aplikację platformy Spark](./media/apache-spark-job-definitions/view-spark-application.png)

2. Następnie kliknij pozycję Aplikacja platformy Spark, **LogQuery** okno. Postęp wykonywania zadania można wyświetlić z **LogQuery**.

    ![Wyświetl LogQuery aplikacji platformy Spark](./media/apache-spark-job-definitions/view-job-log-query.png)

### <a name="scenario-3-check-output-file"></a>Scenariusz 3: Sprawdzanie pliku wyjściowego

 1. Kliknij pozycję **dane**, a następnie wybierz pozycję **konta magazynu**. Po pomyślnym uruchomieniu można przejść do obszaru magazyn ADLS Gen2 i sprawdź, czy są generowane dane wyjściowe.

    ![Wyświetl plik wyjściowy](./media/apache-spark-job-definitions/view-output-file.png)

## <a name="next-steps"></a>Następne kroki

W tym samouczku pokazano, jak utworzyć definicje zadań platformy Spark za pomocą usługi Azure Synapse Analytics, a następnie przesłać je do puli Synapse Spark. Następnie możesz użyć usługi Azure Synapse Analytics, aby utworzyć Power BI zestawy danych i zarządzać Power BI danymi. 

- [Łączenie się z danymi w programie Power BI Desktop](https://docs.microsoft.com/power-bi/desktop-quickstart-connect-to-data)
- [Wizualizowanie przy użyciu usługi Power BI](../sql-data-warehouse/sql-data-warehouse-get-started-visualize-with-power-bi.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
