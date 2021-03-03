---
title: 'Szybki Start: Tworzenie puli Apache Spark bezserwerowej przy użyciu narzędzi sieci Web'
description: W tym przewodniku szybki start pokazano, jak za pomocą narzędzi sieci Web utworzyć pulę Apache Spark bezserwerowych w usłudze Azure Synapse Analytics oraz jak uruchomić zapytanie Spark SQL.
services: synapse-analytics
author: euangMS
ms.author: euang
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: spark
ms.topic: quickstart
ms.date: 10/16/2020
ms.openlocfilehash: 060c78621b82f4698d4596383cd155d85d483d8e
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101669484"
---
# <a name="quickstart-create-a-serverless-apache-spark-pool-in-azure-synapse-analytics-using-web-tools"></a>Szybki Start: Tworzenie puli Apache Spark bezserwerowej w usłudze Azure Synapse Analytics przy użyciu narzędzi sieci Web

W tym przewodniku szybki start dowiesz się, jak utworzyć bezserwerową pulę Apache Spark w usłudze Azure Synapse przy użyciu narzędzi sieci Web. Następnie dowiesz się, jak nawiązać połączenie z pulą Apache Spark i uruchamiać zapytania Spark SQL dotyczące plików i tabel. Platforma Apache Spark umożliwia szybką analizę danych i używanie klastrów obliczeniowych korzystających z funkcji przetwarzania w pamięci. Aby uzyskać informacje na temat platformy Spark w usłudze Azure Synapse, zobacz [Omówienie: Apache Spark na platformie Azure Synapse](./spark/apache-spark-overview.md).

> [!IMPORTANT]
> Opłaty za wystąpienia platformy Spark są naliczane proporcjonalnie za minutę, niezależnie od tego, czy są one używane. Upewnij się, że wystąpienie platformy Spark zostało zamknięte po zakończeniu korzystania z niego, lub ustaw krótki limit czasu. Aby uzyskać więcej informacji, zobacz sekcję **Czyszczenie zasobów** w tym artykule.

Jeśli nie masz subskrypcji platformy Azure, [przed rozpoczęciem utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/)
- [Obszar roboczy analizy Synapse](quickstart-create-workspace.md)
- [Bezserwerowa Pula Apache Spark](quickstart-create-apache-spark-pool-studio.md)

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="create-a-notebook"></a>Tworzenie notesu

Notes to interaktywny środowisko obsługujące różne języki programowania. Notes pozwala na posługiwanie się danymi, łączenie kodu z opcją promocji, tekstu i wykonywanie prostych wizualizacji.

1. W widoku Azure Portal dla obszaru roboczego usługi Azure Synapse, którego chcesz użyć, wybierz pozycję **Uruchom Synapse Studio**.
2. Po uruchomieniu programu Synapse Studio wybierz pozycję **Utwórz**. Następnie wybierz **+** ikonę "", aby dodać nowy zasób.
3. Z tego miejsca wybierz pozycję **Notes**. Nowy Notes zostanie utworzony i otwarty z automatycznie wygenerowaną nazwą.
 
     ![Nowy Notes](./media/quickstart-apache-spark-notebook/spark-get-started-new-notebook.png "Nowy Notes")

4. W oknie **Właściwości** Podaj nazwę notesu.
5. Na pasku narzędzi kliknij przycisk **Publikuj**.
6. Jeśli w obszarze roboczym istnieje tylko jedna pula Apache Spark, zostanie ona domyślnie wybrana. Użyj listy rozwijanej, aby wybrać poprawną pulę Apache Spark, jeśli żadna nie została wybrana.
7. Kliknij przycisk **Dodaj kod**. Język domyślny to `Pyspark` . Zamierzasz używać kombinacji Pyspark i Spark SQL, więc wybór domyślny jest prawidłowy. Inne obsługiwane języki to Scala i .NET for Spark.
8. Następnie utworzysz prosty obiekt Dataframe platformy Spark do manipulowania. W takim przypadku należy utworzyć go z poziomu kodu. Istnieją trzy wiersze i trzy kolumny:

   ```python
   new_rows = [('CA',22, 45000),("WA",35,65000) ,("WA",50,85000)]
   demo_df = spark.createDataFrame(new_rows, ['state', 'age', 'salary'])
   demo_df.show()
   ```

9. Teraz uruchom komórkę przy użyciu jednej z następujących metod:

   - Naciśnij klawisze **SHIFT + ENTER**.
   - Wybierz niebieską ikonę odtwarzania z lewej strony komórki.
   - Wybierz przycisk **Uruchom wszystko** na pasku narzędzi.

       ![Utwórz obiekt ramki danych](./media/quickstart-apache-spark-notebook/spark-get-started-create-data-frame-object.png)

10. Jeśli wystąpienie puli Apache Spark nie jest już uruchomione, zostanie automatycznie uruchomione. Stan wystąpienia puli Apache Spark można zobaczyć poniżej używanej komórki, a także w panelu stanu w dolnej części notesu. W zależności od rozmiaru puli rozpoczęcie powinno trwać 2-5 minut. Po zakończeniu działania kodu informacje znajdujące się poniżej komórki pokazują, jak długo wymagało uruchomienia i jego wykonania. W komórce danych wyjściowych są wyświetlane dane wyjściowe.

    ![Wyjście z wykonywania komórki](./media/quickstart-apache-spark-notebook/run-cell-with-output.png)

11. Dane teraz istnieją w ramce danych, z której można korzystać na wiele różnych sposobów. Będzie ona potrzebna w różnych formatach dla pozostałej części tego przewodnika Szybki Start.
12. Wprowadź Poniższy kod w innej komórce i uruchom go. spowoduje to utworzenie tabeli Spark, CSV i pliku Parquet z kopiami danych:

    ```python
     demo_df.createOrReplaceTempView('demo_df')
     demo_df.write.csv('demo_df', mode='overwrite')
     demo_df.write.parquet('abfss://<<TheNameOfAStorageAccountFileSystem>>@<<TheNameOfAStorageAccount>>.dfs.core.windows.net/demodata/demo_df', mode='overwrite')
    ```

    W przypadku korzystania z Eksploratora magazynu można zobaczyć wpływ dwóch różnych sposobów pisania plików użytych powyżej. Gdy nie określono żadnego systemu plików, w tym przypadku zostanie użyta wartość domyślna `default>user>trusted-service-user>demo_df` . Dane są zapisywane w lokalizacji określonego systemu plików.

    Zwróć uwagę na formaty "CSV" i "Parquet", operacje zapisu, które są tworzone przez katalog z wieloma plikami partycjonowanymi.

    ![Widok Eksploratora magazynu danych wyjściowych](./media/quickstart-apache-spark-notebook/spark-get-started-default-storage.png "Widok Eksploratora magazynu danych wyjściowych")

    ![Zrzut ekranu, który podświetla domyślną > demodata > demo_df Path.](./media/quickstart-apache-spark-notebook/spark-get-started-default-storage2.png "Widok Eksploratora magazynu danych wyjściowych")

## <a name="run-spark-sql-statements"></a>Uruchamianie instrukcji Spark SQL

Structured Query Language (SQL) to najbardziej typowy i szeroko używany język służący do wykonywania zapytań i definiowania danych. Rozwiązanie Spark SQL stanowi rozszerzenie platformy Apache Spark służące do przetwarzania danych strukturalnych za pomocą dobrze znanej składni języka SQL.

1. Wklej następujący kod do pustej komórki, a następnie uruchom kod. Polecenie wyświetla listę tabel w puli.

   ```sql
   %%sql
   SHOW TABLES
   ```

   W przypadku korzystania z notesu z pulą Apache Spark usługi Azure Synapse uzyskasz ustawienie wstępne, za pomocą którego `sqlContext` można uruchamiać zapytania przy użyciu platformy Spark SQL. `%%sql` informuje Notes o konieczności użycia ustawienia wstępnego `sqlContext` do uruchomienia zapytania. Zapytanie pobiera 10 najważniejszych wierszy z tabeli systemowej, która jest domyślnie dostarczana z wszystkimi pulami Apache Spark Azure Synapse.

2. Uruchom inne zapytanie, aby wyświetlić dane z tabeli `demo_df`.

    ```sql
    %%sql
    SELECT * FROM demo_df
    ```

    Kod tworzy dwie komórki wyjściowe, jeden, który zawiera dane, jest inny, który pokazuje widok zadania.

    Domyślnie w widoku wyniki jest wyświetlana siatka. Ale istnieje przełącznik widoku pod siatką umożliwiającą przełączenie widoku między widokami siatki i wykresów.

    ![Zapytanie wyjściowe zapytania w usłudze Azure Synapse Spark](./media/quickstart-apache-spark-notebook/spark-get-started-query.png "Zapytanie wyjściowe zapytania w usłudze Azure Synapse Spark")

3. W przełączniku **widoku** wybierz pozycję **Wykres**.
4. Wybierz ikonę **Wyświetl opcje** z prawej strony.
5. W polu **Typ wykresu** wybierz pozycję "wykres słupkowy".
6. W polu kolumna osi X wybierz pozycję stan.
7. W polu kolumna osi Y wybierz pozycję "wynagrodzenie".
8. W polu **agregacja** wybierz wartość "Średnia".
9. Wybierz przycisk **Zastosuj**.

   ![Dane wyjściowe wykresu na platformie Azure Synapse Spark](./media/quickstart-apache-spark-notebook/spark-get-started-query-chart-output.png "Dane wyjściowe wykresu na platformie Azure Synapse Spark")

10. Można korzystać z tego samego środowiska programu SQL, ale bez konieczności przełączania języków. Można to zrobić, zastępując komórkę SQL powyżej tą komórką PySpark, ale środowisko danych wyjściowych jest takie samo, ponieważ jest używane polecenie **Display** :

    ```python
    display(spark.sql('SELECT * FROM demo_df'))
    ```

11. Każda z wcześniej wykonanych komórek miała opcję przejścia do **serwera historii** i **monitorowania**. Kliknięcie linków prowadzi do różnych części środowiska użytkownika.

> [!NOTE]
> Niektóre [Apache Spark oficjalnej dokumentacji](https://spark.apache.org/docs/latest/) opierają się na użyciu konsoli Spark, która nie jest dostępna w Synapse Spark. Zamiast tego należy użyć [notesu](quickstart-apache-spark-notebook.md) lub środowiska [IntelliJ](./spark/intellij-tool-synapse.md) .

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Usługa Azure Synapse zapisuje dane w Azure Data Lake Storage. Można bezpiecznie zezwolić na wyłączenie wystąpienia platformy Spark, gdy nie jest ono używane. Opłata jest naliczana za bezserwerową pulę Apache Spark, o ile jest uruchomiona, nawet jeśli nie jest używana. 

Ze względu na to, że opłaty za pulę są znacznie większe niż opłaty za magazyn, sprawia, że wystąpienia platformy Spark są zamykane, gdy nie są używane.

Aby upewnić się, że wystąpienie platformy Spark zostanie zamknięte, Zakończ wszystkie połączone sesje (notesy). Pula jest zamykana, gdy zostanie osiągnięty **czas bezczynności** określony w puli Apache Spark. Możesz również wybrać opcję **sesja końcowa** na pasku stanu w dolnej części notesu.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start przedstawiono sposób tworzenia puli Apache Spark bezserwerowej i uruchamiania podstawowego zapytania Spark SQL.

- [Azure Synapse Analytics](overview-what-is.md)
- [Dokumentacja platformy .NET dla Apache Spark](/dotnet/spark)



