---
title: 'Szybki start: tworzenie puli platformy Spark (wersja zapoznawcza) w usłudze Azure Synapse Analytics'
description: Ten przewodnik Szybki start pokazuje, jak używać narzędzi sieci Web do tworzenia puli platformy Spark (wersja zapoznawcza) w usłudze Azure Synapse Analytics i uruchamiania kwerendy SQL platformy Spark.
services: synapse-analytics
author: euangMS
ms.author: euang
ms.reviewer: jrasnick, carlrab
ms.service: synapse-analytics
ms.subservice: ''
ms.topic: quickstart
ms.date: 04/15/2020
ms.openlocfilehash: 5762c074b825c1282959c509c2c72d232f0a238c
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424300"
---
# <a name="quickstart-create-an-apache-spark-pool-preview-in-synapse-analytics-using-web-tools"></a>Szybki start: tworzenie puli platformy Spark Apache (wersja zapoznawcza) w usłudze Synapse Analytics przy użyciu narzędzi internetowych

W tym przewodniku Szybki start dowiesz się, jak utworzyć pulę platformy Spark (wersja zapoznawcza) w usłudze Azure Synapse Analytics przy użyciu narzędzi sieci Web. Następnie nauczysz się łączyć z pulą Platformy Spark Apache i uruchamiać zapytania programu Spark SQL względem plików i tabel. Platforma Apache Spark umożliwia szybką analizę danych i używanie klastrów obliczeniowych korzystających z funkcji przetwarzania w pamięci. Aby uzyskać informacje na temat platformy Spark on Synapse Analytics, zobacz [Omówienie: Platforma Apache Spark w usłudze Azure Synapse Analytics](apache-spark-overview.md).

> [!IMPORTANT]
> Rozliczenia za wystąpienia platformy Spark są naliczane proporcjonalnie do liczby minut, niezależnie od tego, czy są używane, czy nie. Pamiętaj, aby zamknąć wystąpienie platformy Spark po zakończeniu korzystania z niego lub ustawić krótki limit czasu. Aby uzyskać więcej informacji, zobacz sekcję **Czyszczenie zasobów** w tym artykule.

Jeśli nie masz subskrypcji platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.

## <a name="create-an-apache-spark-pool"></a>Tworzenie puli platformy Spark Apache

W tym artykule pokazano, jak utworzyć nową pulę platformy Spark apache przy użyciu narzędzi sieci web.

## <a name="create-a-notebook"></a>Tworzenie notesu

Notes jest interaktywnym środowiskiem, które obsługuje różne języki programowania. Notes umożliwia interakcję z danymi, łączenie kodu z markdown, tekst i wykonywanie prostych wizualizacji.

1. W widoku portalu Azure dla obszaru roboczego Usługi Synapse Analytics wybierz pozycję **Uruchom studio Synapse Studio**.
2. Po uruchomieniu Studia Synapse Analytics, wybierz opcję **Rozwiń**. Następnie umieść wskaźnik myszy na wpisie **Notesy.** Wybierz wielokropek (**...**).
3. W tym miejscu wybierz pozycję **Nowy notatnik**. Nowy notes jest tworzony i otwierany z automatycznie generowaną nazwą.
  ![Nowy notatnik](./media/apache-spark-notebook-create-spark-use-sql/spark-get-started-new-notebook.png "Nowy notatnik")

4. W oknie **Właściwości** podaj nazwę notesu.
5. Na pasku narzędzi kliknij pozycję **Publikuj**.
6. Jeśli w obszarze roboczym znajduje się tylko jedna pula programu Apache Spark, jest ono domyślnie zaznaczone. Użyj listy rozwijanej, aby wybrać właściwą pulę Platformy Spark Apache, jeśli nie jest zaznaczona żadna.
7. Kliknij **pozycję Dodaj kod**. Domyślnym językiem jest `Pyspark`. Masz zamiar użyć kombinacji Pyspark i Spark SQL, więc domyślny wybór jest w porządku.
8. Następnie należy utworzyć prosty obiekt Spark DataFrame do manipulowania. W takim przypadku można go utworzyć na podstawie kodu. Istnieją trzy wiersze i trzy kolumny:

   ```python
   new_rows = [('CA',22, 45000),("WA",35,65000) ,("WA",50,85000)]
   demo_df = spark.createDataFrame(new_rows, ['state', 'age', 'salary'])
   demo_df.show()
   ```

9. Teraz uruchom komórkę przy użyciu jednej z następujących metod:

   - Naciśnij **klawisz SHIFT + ENTER**.
   - Wybierz niebieską ikonę odtwarzania po lewej stronie komórki.
   - Wybierz przycisk **Uruchom wszystko** na pasku narzędzi.

   ![Tworzenie obiektu ramki danych](./media/apache-spark-notebook-create-spark-use-sql/spark-get-started-create-data-frame-object.png "Dane wyjściowe z zadania Spark")

10. Jeśli wystąpienie puli platformy Apache Spark nie jest jeszcze uruchomione, zostanie automatycznie uruchomione. Stan wystąpienia puli Apache Spark można zobaczyć pod uruchomiona komórka, a także na panelu stanu u dołu notesu. W zależności od wielkości basenu, rozpoczęcie powinno potrwać 2-5 minut. Po zakończeniu działania kodu wyświetlane są informacje poniżej komórki pokazujące, jak długo trwało uruchamianie i jego wykonanie. W komórce wyjściowej są widoczne dane wyjściowe.

    ![Dane wyjściowe z wykonania komórki](./media/apache-spark-notebook-create-spark-use-sql/run-cell-with-output.png "Dane wyjściowe z zadania Spark")

11. Dane istnieją teraz w DataFrame stamtąd można użyć danych na wiele różnych sposobów. Będziesz go potrzebować w różnych formatach do końca tego szybkiego startu.
12. Wprowadź poniższy kod w innej komórce i uruchom go, spowoduje to utworzenie tabeli Spark, pliku CSV i pliku Parkietu z kopiami danych:

    ```python
     demo_df.createOrReplaceTempView('demo_df')
     demo_df.write.csv('demo_df', mode='overwrite')
     demo_df.write.parquet('abfss://<<TheNameOfAStorageAccountFileSystem>>@<<TheNameOfAStorageAccount>>.dfs.core.windows.net/demodata/demo_df', mode='overwrite')
    ```

    Jeśli używasz eksploratora magazynu, jest możliwe, aby zobaczyć wpływ dwóch różnych sposobów pisania pliku używanego powyżej. Jeśli nie określono systemu plików, w tym `default>user>trusted-service-user>demo_df`przypadku jest używany domyślny system . Dane są zapisywane w lokalizacji określonego systemu plików.

    Uwaga zarówno w formatach "csv" i "parkiet", operacje zapisu katalog jest tworzony z wielu plików podzielonych na partycje.

    ![Widok eksploratora magazynu danych wyjściowych](./media/apache-spark-notebook-create-spark-use-sql/spark-get-started-default-storage.png "Widok eksploratora magazynu danych wyjściowych")

    ![Widok eksploratora magazynu danych wyjściowych](./media/apache-spark-notebook-create-spark-use-sql/spark-get-started-default-storage2.png "Widok eksploratora magazynu danych wyjściowych")

## <a name="run-spark-sql-statements"></a>Uruchamianie instrukcji Spark SQL

SQL (Structured Query Language) to najczęściej używany język służący do definiowania danych i wykonywania zapytań na tych danych. Rozwiązanie Spark SQL stanowi rozszerzenie platformy Apache Spark służące do przetwarzania danych strukturalnych za pomocą dobrze znanej składni języka SQL.

1. Wklej następujący kod w pustej komórce, a następnie uruchom kod. Polecenie wyświetla listę tabel w puli.

   ```sql
   %%sql
   SHOW TABLES
   ```

   Korzystając z notesu z puli Programu Synapse Analytics Apache `sqlContext` Spark, otrzymasz predefiniowane ustawienia, których można użyć do uruchamiania zapytań przy użyciu programu Spark SQL. `%%sql`informuje notes, aby używał `sqlContext` ustawienia wstępnego do uruchomienia kwerendy. Kwerenda pobiera 10 pierwszych wierszy z tabeli systemowej, która jest domyślnie dostępna we wszystkich pulach Platformy Spark usługi Synapse Analytics Apache.

2. Uruchom inne zapytanie, aby wyświetlić dane z tabeli `demo_df`.

    ```sql
    %%sql
    SELECT * FROM demo_df
    ```

    Kod tworzy dwie komórki wyjściowe, jeden, który zawiera wyniki danych, drugi, który pokazuje widok zadania.

    Domyślnie widok wyników pokazuje siatkę, ale pod siatką znajduje się przełącznik widoku, który umożliwia przełączanie widoku między widokami siatki i wykresu.

    ![Dane wyjściowe kwerendy w programie Synapse Analytics Spark](./media/apache-spark-notebook-create-spark-use-sql/spark-get-started-query.png "Dane wyjściowe kwerendy w programie Synapse Analytics Spark")

3. W przełączniku **Widok** wybierz **pozycję Wykres**
4. Wybierz ikonę **Opcje widoku** od prawej strony.
5. W polu **Typ wykresu** wybierz "wykres słupkowy".
6. W polu kolumny osi X wybierz "stan".
7. W polu kolumny osi Y wybierz "wynagrodzenie".
8. W polu **Agregacja** wybierz wartość "AVG".
9. Wybierz przycisk **Zastosuj**.

   ![Dane wyjściowe wykresu w platformie Synapse Analytics Spark](./media/apache-spark-notebook-create-spark-use-sql/spark-get-started-query-chart-output.png "Dane wyjściowe wykresu w platformie Synapse Analytics Spark")

10. Jest możliwe, aby uzyskać takie samo doświadczenie uruchamiania języka SQL, ale bez konieczności przełączania języków. Można to zrobić, zastępując komórkę SQL powyżej tą komórką PySpark, środowisko wyjściowe jest takie samo, ponieważ używane jest polecenie **display:**

    ```python
    display(spark.sql('SELECT * FROM demo_df'))
    ```

11. Każda z komórek, które zostały wcześniej wykonane, miała możliwość przejścia do **serwera historii** i **monitorowania**. Kliknięcie linków prowadzi do różnych części środowiska użytkownika.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Usługa Synapse Analytics zapisuje dane w usłudze Azure Data Lake Storage. Można bezpiecznie pozwolić Spark wystąpienie zamknąć, gdy nie jest używany. Opłata jest naliczana za pulę Synapse Analytics Apache Spark tak długo, jak jest uruchomiona, nawet jeśli nie jest używana. Ponieważ opłaty za pulę są wielokrotnie większe niż opłaty za przechowywanie, ma ekonomiczny sens, aby umożliwić spark wystąpień zamkniętych, gdy nie są one używane.

Aby upewnić się, że wystąpienie platformy Spark jest zamknięte, zakończ wszystkie połączone sesje (notesy). Pula zostanie zamknięta po osiągnięciu **czasu bezczynnego** określonego w puli platformy Apache Spark. Można również wybrać **sesję końcową** z paska stanu u dołu notesu.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start dowiesz się, jak utworzyć pulę programu Synapse Analytics Apache Spark i uruchomić podstawową kwerendę SQL platformy Spark.

- [Azure Synapse Analytics](../overview-what-is.md)
- [.NET dla apache Spark dokumentacji](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Oficjalna dokumentacja Apache Spark](https://spark.apache.org/docs/latest/)

>[!NOTE]
> Niektóre oficjalne apache spark dokumentacji opiera się na użyciu konsoli Platformy Spark, który nie jest dostępny na platformie Azure Synapse Spark. Zamiast tego użyj [notebooka](../spark/apache-spark-notebook-create-spark-use-sql.md) lub [intellij.](../spark/intellij-tool-synapse.md)
