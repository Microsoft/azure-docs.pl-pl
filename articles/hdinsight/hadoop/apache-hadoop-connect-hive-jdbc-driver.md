---
title: Zapytanie Apache Hive za pomocą sterownika JDBC — Azure HDInsight
description: Użyj sterownika JDBC z aplikacji Java, aby przesłać Apache Hive zapytania do usługi Hadoop w usłudze HDInsight. Połącz programowo i z klienta SQL SQuirrel.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017,seoapr2020
ms.date: 04/20/2020
ms.openlocfilehash: 6073000f2f14f835e2bfbd91b41619101c36b10f
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104866849"
---
# <a name="query-apache-hive-through-the-jdbc-driver-in-hdinsight"></a>Query Apache Hive through the JDBC driver in HDInsight (Wysyłanie zapytań do usługi Apache Hive za pośrednictwem sterownika JDBC w usłudze HDInsight)

[!INCLUDE [ODBC-JDBC-selector](../../../includes/hdinsight-selector-odbc-jdbc.md)]

Dowiedz się, jak używać sterownika JDBC z poziomu aplikacji Java. Aby przesłać Apache Hive zapytania do Apache Hadoop w usłudze Azure HDInsight. Informacje przedstawione w tym dokumencie przedstawiają sposób nawiązywania połączenia programowo i z klienta SQL SQuirreL.

Aby uzyskać więcej informacji na temat interfejsu JDBC programu Hive, zobacz [HiveJDBCInterface](https://cwiki.apache.org/confluence/display/Hive/HiveJDBCInterface).

## <a name="prerequisites"></a>Wymagania wstępne

* HDInsight An klastra Hadoop. Aby go utworzyć, zobacz [Rozpoczynanie pracy z usługą Azure HDInsight](apache-hadoop-linux-tutorial-get-started.md). Upewnij się, że usługa serwera hiveserver2 jest uruchomiona.
* [Zestaw Java developer Kit (JDK) w wersji 11](https://www.oracle.com/technetwork/java/javase/downloads/jdk11-downloads-5066655.html) lub nowszej.
* [Squirrel SQL](http://squirrel-sql.sourceforge.net/). SQuirreL jest aplikacją kliencką JDBC.

## <a name="jdbc-connection-string"></a>Parametry połączenia sterownika JDBC

Połączenia JDBC z klastrem usługi HDInsight na platformie Azure są realizowane za pośrednictwem portu 443. Ruch jest zabezpieczony przy użyciu protokołu TLS/SSL. Brama publiczna, w której znajdują się klastry, przekierowuje ruch do portu, na którym nasłuchuje serwera hiveserver2. Poniższe parametry połączenia przedstawiają format do użycia w usłudze HDInsight:

```http
    jdbc:hive2://CLUSTERNAME.azurehdinsight.net:443/default;transportMode=http;ssl=true;httpPath=/hive2
```

Element `CLUSTERNAME` należy zastąpić nazwą klastra usługi HDInsight.

Można też uzyskać połączenie za pomocą **interfejsu użytkownika Ambari > Hive > konfiguracjami > Advanced**.

:::image type="content" source="./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-get-connection-string-through-ambari.png" alt-text="Pobierz parametry połączenia usługi JDBC za poorednictwem Ambari" border="true":::

### <a name="host-name-in-connection-string"></a>Nazwa hosta w parametrach połączenia

Nazwa hosta "CLUSTERNAME.azurehdinsight.net" w parametrach połączenia jest taka sama jak w przypadku adresu URL klastra. Możesz to zrobić za poorednictwem Azure Portal.

### <a name="port-in-connection-string"></a>Port w parametrach połączenia

**Portu 443** można używać tylko do nawiązywania połączenia z klastrem z niektórych miejsc poza siecią wirtualną platformy Azure. HDInsight to usługa zarządzana, która oznacza, że wszystkie połączenia z klastrem są zarządzane za pośrednictwem bezpiecznej bramy. Nie można nawiązać połączenia z HiveServer 2 bezpośrednio na portach 10001 lub 10000. Porty te nie są widoczne dla zewnątrz.

## <a name="authentication"></a>Authentication

Podczas ustanawiania połączenia użyj nazwy administratora klastra usługi HDInsight i hasła do uwierzytelniania. Od klientów JDBC, takich jak SQuirreL SQL, wprowadź nazwę i hasło administratora w ustawieniach klienta.

W aplikacji Java należy użyć nazwy i hasła podczas ustanawiania połączenia. Na przykład poniższy kod Java otwiera nowe połączenie:

```java
DriverManager.getConnection(connectionString,clusterAdmin,clusterPassword);
```

## <a name="connect-with-squirrel-sql-client"></a>Łączenie z klientem SQL SQuirreL

SQuirreL SQL to klient JDBC, za pomocą którego można zdalnie uruchamiać zapytania Hive w klastrze usługi HDInsight. W poniższych krokach przyjęto założenie, że już zainstalowano program SQuirreL SQL.

1. Utwórz katalog zawierający pliki do skopiowania z klastra.

2. W poniższym skrypcie Zastąp ciąg `sshuser` nazwą konta użytkownika SSH dla klastra.  Zamień na `CLUSTERNAME` nazwę klastra usługi HDInsight.  W wierszu polecenia Zmień katalog roboczy na taki, który został utworzony w poprzednim kroku, a następnie wprowadź następujące polecenie, aby skopiować pliki z klastra usługi HDInsight:

    ```cmd
    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/usr/hdp/current/hadoop-client/{hadoop-auth.jar,hadoop-common.jar,lib/log4j-*.jar,lib/slf4j-*.jar,lib/curator-*.jar} .

    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/usr/hdp/current/hive-client/lib/{commons-codec*.jar,commons-logging-*.jar,hive-*-*.jar,httpclient-*.jar,httpcore-*.jar,libfb*.jar,libthrift-*.jar} .
    ```

3. Uruchom aplikację SQL SQuirreL. Z lewej strony okna wybierz pozycję **sterowniki**.

    :::image type="content" source="./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-squirreldrivers.png" alt-text="Karta Sterowniki po lewej stronie okna" border="true":::

4. Z ikon w górnej części okna dialogowego **sterowniki** wybierz **+** ikonę, aby utworzyć sterownik.

    :::image type="content" source="./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-driversicons.png" alt-text="Ikona sterowników aplikacji SQL SQuirreL" border="true":::

5. W oknie dialogowym Dodawanie sterownika Dodaj następujące informacje:

    |Właściwość | Wartość |
    |---|---|
    |Nazwa|Hive|
    |Przykładowy adres URL|`jdbc:hive2://localhost:443/default;transportMode=http;ssl=true;httpPath=/hive2`|
    |Dodatkowa ścieżka klasy|Użyj przycisku **Dodaj** , aby dodać wszystkie pobrane wcześniej pliki jar.|
    |Nazwa klasy|org. Apache. Hive. JDBC. HiveDriver|

   :::image type="content" source="./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-add-driver.png" alt-text="okno dialogowe Dodawanie sterownika z parametrami" border="true":::

   Wybierz **przycisk OK** , aby zapisać te ustawienia.

6. Po lewej stronie okna SQuirreL SQL Wybierz pozycję **aliasy**. Następnie wybierz **+** ikonę, aby utworzyć alias połączenia.

    :::image type="content" source="./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-new-aliases.png" alt-text="Okno dialogowe &quot;SQuirreL Dodawanie nowego aliasu SQL&quot;" border="true":::

7. W oknie dialogowym **Dodawanie aliasu** należy użyć następujących wartości:

    |Właściwość |Wartość |
    |---|---|
    |Nazwa|Usługa Hive w usłudze HDInsight|
    |Sterownik|Użyj listy rozwijanej, aby wybrać sterownik **Hive** .|
    |Adres URL|`jdbc:hive2://CLUSTERNAME.azurehdinsight.net:443/default;transportMode=http;ssl=true;httpPath=/hive2`. Zastąp **CLUSTERNAME** nazwą klastra usługi HDInsight:|
    |Nazwa użytkownika|Nazwa konta logowania klastra dla klastra usługi HDInsight. Wartość domyślna to **admin**.|
    |Hasło|Hasło do konta logowania do klastra.|

    :::image type="content" source="./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-addalias-dialog.png" alt-text="Dodaj okno dialogowe aliasu z parametrami" border="true":::

    > [!IMPORTANT]
    > Użyj przycisku **Testuj** , aby sprawdzić, czy połączenie działa. Po **nawiązaniu połączenia z: zostanie wyświetlone okno dialogowe Hive w usłudze HDInsight** , a następnie wybierz pozycję **Połącz** , aby wykonać test. Jeśli test zakończy się pomyślnie, zostanie wyświetlone okno dialogowe **pomyślne połączenie** . Jeśli wystąpi błąd, zobacz [Rozwiązywanie problemów](#troubleshooting).

    Aby zapisać alias połączenia, użyj przycisku **OK** u dołu okna dialogowego **Dodawanie aliasu** .

8. Z listy rozwijanej **Połącz** z na początku SQuirreL SQL Wybierz pozycję **Hive w usłudze HDInsight**. Po wyświetleniu monitu wybierz pozycję **Połącz**.

    :::image type="content" source="./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-connect-dialog.png" alt-text="okno dialogowe połączenia z parametrami" border="true":::

9. Po nawiązaniu połączenia wprowadź następujące zapytanie do okna dialogowego zapytania SQL, a następnie wybierz ikonę **Run** (uruchomiona osoba). Obszar wyników powinien zawierać wyniki zapytania.

    ```hiveql
    select * from hivesampletable limit 10;
    ```

    :::image type="content" source="./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-sqlquery-dialog.png" alt-text="okno dialogowe kwerendy SQL, w tym wyniki" border="true":::

## <a name="connect-from-an-example-java-application"></a>Nawiązywanie połączenia z przykładową aplikacją Java

Przykład użycia klienta Java do wykonywania zapytań w usłudze HDInsight jest dostępny pod adresem [https://github.com/Azure-Samples/hdinsight-java-hive-jdbc](https://github.com/Azure-Samples/hdinsight-java-hive-jdbc) . Postępuj zgodnie z instrukcjami w repozytorium, aby skompilować i uruchomić przykład.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

### <a name="unexpected-error-occurred-attempting-to-open-an-sql-connection"></a>Wystąpił nieoczekiwany błąd podczas próby otwarcia połączenia SQL

**Objawy**: podczas nawiązywania połączenia z klastrem usługi HDInsight w wersji 3,3 lub nowszej może pojawić się komunikat o błędzie, który wystąpił nieoczekiwany błąd. Ślad stosu dla tego błędu rozpoczyna się od następujących wierszy:

```java
java.util.concurrent.ExecutionException: java.lang.RuntimeException: java.lang.NoSuchMethodError: org.apache.commons.codec.binary.Base64.<init>(I)V
at java.util.concurrent.FutureTas...(FutureTask.java:122)
at java.util.concurrent.FutureTask.get(FutureTask.java:206)
```

**Przyczyna**: ten błąd jest spowodowany przez starszą wersję pliku Commons-Codec. jar dołączoną do SQuirreL.

**Rozwiązanie**: Aby rozwiązać ten problem, wykonaj następujące czynności:

1. Zamknij SQuirreL, a następnie przejdź do katalogu, w którym SQuirreL jest zainstalowany w systemie, na przykład `C:\Program Files\squirrel-sql-4.0.0\lib` . W katalogu SquirreL w `lib` katalogu Zastąp istniejący plik Commons-Codec. jar, który został pobrany z klastra usługi HDInsight.

1. Uruchom ponownie SQuirreL. Błąd nie powinien już występować podczas nawiązywania połączenia z usługą Hive w usłudze HDInsight.

### <a name="connection-disconnected-by-hdinsight"></a>Połączenie zostało rozłączone przez HDInsight

**Objawy**: podczas próby pobrania ogromnych ilości danych (np. kilku gigabajtów) za pośrednictwem JDBC/ODBC połączenie jest rozłączone przez HDInsight nieoczekiwanie podczas pobierania.

**Przyczyna**: ten błąd jest spowodowany przez ograniczenie w węzłach bramy. Podczas pobierania danych z JDBC/ODBC wszystkie dane muszą przechodzić przez węzeł bramy. Jednak Brama nie jest przeznaczona do pobierania ogromnych ilości danych, dlatego Brama może zamknąć połączenie, jeśli nie może obsłużyć ruchu.

**Rozwiązanie**: Unikaj używania sterownika JDBC/ODBC do pobierania ogromnych ilości danych. Skopiuj dane bezpośrednio z magazynu obiektów BLOB.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już, jak używać JDBC do pracy z usługą Hive, Skorzystaj z poniższych linków, aby poznać inne sposoby pracy z usługą Azure HDInsight.

* [Wizualizuj Apache Hive dane za pomocą usługi Microsoft Power BI w usłudze Azure HDInsight](apache-hadoop-connect-hive-power-bi.md).
* [Wizualizowanie interakcyjnych zapytań danych Hive za pomocą Power BI w usłudze Azure HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).
* [Łączenie programu Excel z usługą HDInsight przy użyciu Sterownik Microsoft Hive ODBC](apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Łączenie programu Excel z Apache Hadoop przy użyciu Power Query](apache-hadoop-connect-excel-power-query.md).
* [Korzystanie z programu Apache Hive z usługą HDInsight](hdinsight-use-hive.md)
* [Korzystanie z programu Apache Pig z usługą HDInsight](../use-pig.md)
* [Korzystanie z zadań MapReduce z usługą HDInsight](hdinsight-use-mapreduce.md)
