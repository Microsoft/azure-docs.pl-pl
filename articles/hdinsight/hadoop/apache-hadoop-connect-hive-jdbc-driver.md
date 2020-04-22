---
title: Rozszerzeń apache hive za pośrednictwem sterownika JDBC — Azure HDInsight
description: Użyj sterownika JDBC z aplikacji Java, aby przesłać zapytania Apache Hive do Usługi Hadoop w u hdinsight. Połącz programowo i z klienta SQL SQuirrel.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 04/20/2020
ms.openlocfilehash: 803256ab1c5201534cfbd8210f96040ba75081e5
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687297"
---
# <a name="query-apache-hive-through-the-jdbc-driver-in-hdinsight"></a>Query Apache Hive through the JDBC driver in HDInsight (Wysyłanie zapytań do usługi Apache Hive za pośrednictwem sterownika JDBC w usłudze HDInsight)

[!INCLUDE [ODBC-JDBC-selector](../../../includes/hdinsight-selector-odbc-jdbc.md)]

Dowiedz się, jak używać sterownika JDBC z aplikacji Java. Aby przesłać zapytania gałąź Apache do Apache Hadoop w usłudze Azure HDInsight. Informacje zawarte w tym dokumencie pokazuje, jak połączyć się programowo i z klienta SQL SQuirreL.

Aby uzyskać więcej informacji na temat interfejsu JDBC hive, zobacz [HiveJDBCInterface](https://cwiki.apache.org/confluence/display/Hive/HiveJDBCInterface).

## <a name="prerequisites"></a>Wymagania wstępne

* Klaster Hadoop HDInsight. Aby go utworzyć, zobacz [Wprowadzenie do usługi Azure HDInsight](apache-hadoop-linux-tutorial-get-started.md). Upewnij się, że usługa HiveServer2 jest uruchomiona.
* [Zestaw Java Developer Kit (JDK) w wersji 11](https://www.oracle.com/technetwork/java/javase/downloads/jdk11-downloads-5066655.html) lub nowszej.
* [SQuirreL SQL](http://squirrel-sql.sourceforge.net/). SQuirreL jest aplikacją kliencką JDBC.

## <a name="jdbc-connection-string"></a>Parametry połączenia sterownika JDBC

Połączenia JDBC z klastrem HDInsight na platformie Azure są nawiązywać za pomocą portu 443. Ruch jest zabezpieczony za pomocą protokołu TLS/SSL. Brama publiczna, za którą znajdują się klastry, przekierowuje ruch do portu, na który faktycznie nasłuchuje HiveServer2. Następujący ciąg połączenia pokazuje format używany dla usługi HDInsight:

    jdbc:hive2://CLUSTERNAME.azurehdinsight.net:443/default;transportMode=http;ssl=true;httpPath=/hive2

Element `CLUSTERNAME` należy zastąpić nazwą klastra usługi HDInsight.

Lub można uzyskać połączenie za pośrednictwem **interfejsu użytkownika Ambari > Hive > Configs > Advanced**.

![Pobierz ciąg połączenia JDBC przez Ambari](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-get-connection-string-through-ambari.png)

### <a name="host-name-in-connection-string"></a>Nazwa hosta w ciągu połączenia

Nazwa hosta "CLUSTERNAME.azurehdinsight.net" w ciągu połączenia jest taka sama jak adres URL klastra. Można go uzyskać za pośrednictwem witryny Azure portal.

### <a name="port-in-connection-string"></a>Port w ciągu połączenia

Portu **443** można używać tylko do łączenia się z klastrem z niektórych miejsc poza siecią wirtualną platformy Azure. HDInsight to usługa zarządzana, co oznacza, że wszystkie połączenia z klastrem są zarządzane za pośrednictwem bezpiecznej bramy. Nie można połączyć się z HiveServer 2 bezpośrednio na portach 10001 lub 10000. Te porty nie są narażone na zewnątrz.

## <a name="authentication"></a>Authentication

Podczas ustanawiania połączenia należy użyć nazwy administratora i hasła klastra HDInsight do uwierzytelniania. Spośród klientów JDBC, takich jak SQuirreL SQL, wprowadź nazwę administratora i hasło w ustawieniach klienta.

Z aplikacji Java należy użyć nazwy i hasła podczas ustanawiania połączenia. Na przykład następujący kod Java otwiera nowe połączenie:

```java
DriverManager.getConnection(connectionString,clusterAdmin,clusterPassword);
```

## <a name="connect-with-squirrel-sql-client"></a>Połącz się z klientem SQuirreL SQL

SQuirreL SQL to klient JDBC, który może służyć do zdalnego uruchamiania zapytań hive z klastrem HDInsight. W poniższych krokach założono, że zainstalowano już SQuirreL SQL.

1. Utwórz katalog zawierający określone pliki do skopiowania z klastra.

2. W poniższym skrypcie zastąp `sshuser` nazwą konta użytkownika SSH dla klastra.  Zamień `CLUSTERNAME` na nazwę klastra HDInsight.  Z wiersza polecenia zmień katalog roboczy na utworzony w poprzednim kroku, a następnie wprowadź następujące polecenie, aby skopiować pliki z klastra HDInsight:

    ```cmd
    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/usr/hdp/current/hadoop-client/{hadoop-auth.jar,hadoop-common.jar,lib/log4j-*.jar,lib/slf4j-*.jar,lib/curator-*.jar} .

    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/usr/hdp/current/hive-client/lib/{commons-codec*.jar,commons-logging-*.jar,hive-*-*.jar,httpclient-*.jar,httpcore-*.jar,libfb*.jar,libthrift-*.jar} .
    ```

3. Uruchom aplikację SQuirreL SQL. Po lewej stronie okna wybierz pozycję **Sterowniki**.

    ![Karta Sterowniki po lewej stronie okna](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-squirreldrivers.png)

4. Z ikon w górnej części okna dialogowego **+** **Sterowniki** wybierz ikonę, aby utworzyć sterownik.

    ![Ikona sterowników aplikacji SQuirreL SQL](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-driversicons.png)

5. W oknie dialogowym Dodawanie sterownika dodaj następujące informacje:

    |Właściwość | Wartość |
    |---|---|
    |Nazwa|Hive|
    |Przykładowy adres URL|`jdbc:hive2://localhost:443/default;transportMode=http;ssl=true;httpPath=/hive2`|
    |Dodatkowa ścieżka zajęć|Użyj przycisku **Dodaj,** aby dodać wszystkie pliki jar pobrane wcześniej.|
    |Nazwa klasy|org.apache.hive.jdbc.HiveDriver|

   ![dodawanie okna dialogowego sterownika z parametrami](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-add-driver.png)

   Wybierz **przycisk OK,** aby zapisać te ustawienia.

6. Po lewej stronie okna SQuirreL SQL wybierz pozycję **Aliases**. Następnie wybierz **+** ikonę, aby utworzyć alias połączenia.

    !['SQuirreL SQL add new alias dialog' 'SQuirreL SQL add new alias dialog' 'SQuirreL SQL add new alias dialog' 'S](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-new-aliases.png)

7. Użyj następujących wartości w oknie dialogowym **Dodawanie aliasu:**

    |Właściwość |Wartość |
    |---|---|
    |Nazwa|Gałąź na HDInsight|
    |Sterownik|Użyj listy rozwijanej, aby wybrać sterownik **Hive.**|
    |Adres URL|`jdbc:hive2://CLUSTERNAME.azurehdinsight.net:443/default;transportMode=http;ssl=true;httpPath=/hive2`. Zastąp **CLUSTERNAME** nazwą klastra usługi HDInsight:|
    |Nazwa użytkownika|Nazwa konta logowania klastra dla klastra HDInsight. Wartość domyślna to **admin**.|
    |Hasło|Hasło do konta logowania klastra.|

    ![dodawanie okna dialogowego aliasu z parametrami](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-addalias-dialog.png)

    > [!IMPORTANT]
    > Użyj przycisku **Testuj,** aby sprawdzić, czy połączenie działa. Po wyświetleniu okna dialogowego **Połącz z: Gałąź w programie HDInsight** wybierz opcję **Połącz,** aby wykonać test. Jeśli test zakończy się pomyślnie, zostanie wyświetlone okno dialogowe **Połączenie pomyślne.** Jeśli wystąpi błąd, zobacz [Rozwiązywanie problemów](#troubleshooting).

    Aby zapisać alias połączenia, użyj przycisku **Ok** u dołu okna dialogowego **Dodaj alias.**

8. Z listy rozwijanej **Połącz do** góry aplikacji SQuirreL SQL wybierz pozycję **Gałąź w programie HDInsight**. Po wyświetleniu monitu wybierz pozycję **Połącz**.

    ![okno dialogowe połączenia z parametrami](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-connect-dialog.png)

9. Po nawiązaniu połączenia wprowadź następującą kwerendę w oknie dialogowym kwerendy SQL, a następnie wybierz ikonę **Uruchom** (osoba z systemem). Obszar wyników powinien wyświetlać wyniki kwerendy.

    ```hql
    select * from hivesampletable limit 10;
    ```

    ![okno dialogowe kwerendy sql, w tym wyniki](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-sqlquery-dialog.png)

## <a name="connect-from-an-example-java-application"></a>Łączenie z przykładową aplikacją Java

Przykład użycia klienta Java do wykonywania zapytań hive [https://github.com/Azure-Samples/hdinsight-java-hive-jdbc](https://github.com/Azure-Samples/hdinsight-java-hive-jdbc)w programie HDInsight jest dostępny pod adresem . Postępuj zgodnie z instrukcjami w repozytorium, aby skompilować i uruchomić przykład.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

### <a name="unexpected-error-occurred-attempting-to-open-an-sql-connection"></a>Wystąpił nieoczekiwany błąd podczas próby otwarcia połączenia SQL

**Symptomy:** Podczas łączenia się z klastrem HDInsight o wersji 3.3 lub większej może pojawić się błąd, który wystąpił nieoczekiwany błąd. Śledzenie stosu dla tego błędu rozpoczyna się od następujących wierszy:

```java
java.util.concurrent.ExecutionException: java.lang.RuntimeException: java.lang.NoSuchMethodError: org.apache.commons.codec.binary.Base64.<init>(I)V
at java.util.concurrent.FutureTas...(FutureTask.java:122)
at java.util.concurrent.FutureTask.get(FutureTask.java:206)
```

**Przyczyna:** Ten błąd jest spowodowany przez starszą wersję pliku commons-codec.jar dołączonego do SQuirreL.

**Rozwiązanie**: Aby naprawić ten błąd, należy wykonać następujące czynności:

1. Wyjdź Z SQuirreL, a następnie przejdź do katalogu, w `C:\Program Files\squirrel-sql-4.0.0\lib`którym SQuirreL jest zainstalowany w systemie, być może . W katalogu SquirreL, w `lib` katalogu, zastąp istniejący commons-codec.jar z tym pobranym z klastra HDInsight.

1. Uruchom ponownie SQuirreL. Błąd nie powinien już występować podczas łączenia się z hive na HDInsight.

### <a name="connection-disconnected-by-hdinsight"></a>Połączenie odłączone przez HDInsight

**Objawy**: Podczas próby pobrania ogromnej ilości danych (powiedzmy kilka GB) przez JDBC / ODBC, połączenie jest nieoczekiwanie odłączane przez HDInsight podczas pobierania.

**Przyczyna:** Ten błąd jest spowodowany ograniczeniem węzłów bramy. Podczas uzyskiwania danych z JDBC/ODBC wszystkie dane muszą przechodzić przez węzeł bramy. Jednak brama nie jest przeznaczony do pobierania ogromnej ilości danych, więc brama może zamknąć połączenie, jeśli nie może obsłużyć ruchu.

**Rozdzielczość**: Unikaj używania sterownika JDBC/ODBC do pobierania ogromnych ilości danych. Zamiast tego skopiuj dane bezpośrednio z magazynu obiektów blob.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy już wiesz, jak używać JDBC do pracy z hive, użyj następujących łączy, aby zbadać inne sposoby pracy z usługą Azure HDInsight.

* [Wizualizuj dane gałęzi Apache za pomocą usługi Microsoft Power BI w usłudze Azure HDInsight](apache-hadoop-connect-hive-power-bi.md).
* [Wizualizuj dane gałęzi zapytań interaktywnych za pomocą usługi Power BI w usłudze Azure HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).
* [Podłącz program Excel do usługi HDInsight za pomocą sterownika ODBC firmy Microsoft Hive](apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Łączenie programu Excel z apache Hadoop przy użyciu dodatku Power Query](apache-hadoop-connect-excel-power-query.md).
* [Korzystanie z programu Apache Hive z usługą HDInsight](hdinsight-use-hive.md)
* [Korzystanie z programu Apache Pig z usługą HDInsight](../use-pig.md)
* [Korzystanie z zadań MapReduce z usługą HDInsight](hdinsight-use-mapreduce.md)
