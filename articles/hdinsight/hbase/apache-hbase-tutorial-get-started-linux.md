---
title: Samouczek — używanie bazy danych Apache HBase w usłudze Azure HDInsight
description: Postępuj zgodnie z tym apache HBase samouczek, aby rozpocząć korzystanie hadoop na HDInsight. Utwórz tabele z poziomu powłoki HBase i wykonuj zapytania przy użyciu aplikacji Hive.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: tutorial
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 04/14/2020
ms.openlocfilehash: a601d54ebda074a25a988ac2a115f6418dd5c7ee
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2020
ms.locfileid: "81390260"
---
# <a name="tutorial-use-apache-hbase-in-azure-hdinsight"></a>Samouczek: Korzystanie z bazy danych Apache HBase w usłudze Azure HDInsight

W tym samouczku pokazano, jak utworzyć klaster Apache HBase w usłudze Azure HDInsight, utworzyć tabele HBase i tabele zapytań przy użyciu bazy danych Apache.  Aby uzyskać ogólne informacje o bazie danych HBase, zobacz [Omówienie bazy danych HBase w usłudze HDInsight](./apache-hbase-overview.md).

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie klastra Apache HBase
> * Tworzenie tabel HBase i wstawianie danych
> * Użyj gałęzi Apache, aby zbadać Apache HBase
> * Korzystanie z interfejsów API REST HBase przy użyciu programu Curl
> * Sprawdzanie stanu klastra

## <a name="prerequisites"></a>Wymagania wstępne

* Klient SSH. Aby uzyskać więcej informacji, zobacz [Łączenie się z usługą HDInsight (Apache Hadoop) przy użyciu protokołu SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

* Bash. Przykłady w tym artykule używają powłoki Bash w systemie Windows 10 dla poleceń curl. Aby uzyskać instrukcje instalacji, zobacz [Podręcznik instalacji systemu Windows dla systemu Linux dla systemu Windows 10.](https://docs.microsoft.com/windows/wsl/install-win10)  Inne [powłoki Unix](https://www.gnu.org/software/bash/) będzie działać, jak również.  Przykłady zwijania, z niewielkimi modyfikacjami, mogą działać w wierszu polecenia systemu Windows.  Można też użyć polecenia cmdlet [Invoke-RestMethod](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/invoke-restmethod)systemu Windows PowerShell .

## <a name="create-apache-hbase-cluster"></a>Tworzenie klastra Apache HBase

Poniższa procedura używa szablonu usługi Azure Resource Manager do utworzenia klastra HBase. Szablon tworzy również zależne domyślne konto usługi Azure Storage. Aby zapoznać się z parametrami używanymi w tej procedurze oraz innymi metodami tworzenia klastra, zobacz temat [Tworzenie opartych na systemie Linux klastrów Hadoop w usłudze HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

1. Wybierz następujący obraz, aby otworzyć szablon w witrynie Azure portal. Szablon znajduje się w [szablonach szybki start platformy Azure](https://azure.microsoft.com/resources/templates/).

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-linux%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hbase-tutorial-get-started-linux/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

2. W oknie dialogowym **wdrażania niestandardowego** wprowadź następujące wartości:

    |Właściwość |Opis |
    |---|---|
    |Subskrypcja|Wybierz subskrypcję platformy Azure, która jest używana do tworzenia klastra.|
    |Grupa zasobów|Utwórz grupę zarządzania zasobami platformy Azure lub użyj istniejącej.|
    |Lokalizacja|Określ lokalizację grupy zasobów. |
    |NazwaKlastra|Wprowadź nazwę klastra HBase.|
    |Nazwa użytkownika i hasło logowania do klastra|Domyślną nazwą logowania jest **admin**.|
    |Nazwa użytkownika i hasło protokołu SSH|Domyślna nazwa użytkownika to **sshuser**.|

    Inne parametry są opcjonalne.  

    Każdy klaster zależy od konta usługi Azure Storage. Po usunięciu klastra dane pozostają na koncie magazynu. Domyślna nazwa konta magazynu klastra to nazwa klastra z dołączonym ciągiem „store”. Jest hardcoded w sekcji zmiennych szablonu.

3. Zaznacz pozycję **Wyrażam zgodę na powyższe warunki i postanowienia**, a następnie kliknij przycisk **Kup**. Utworzenie klastra trwa około 20 minut.

Po usunięciu klastra HBase można utworzyć inny klaster HBase za pomocą tego samego domyślnego kontenera obiektów blob. Nowy klaster przejmuje tabele bazy danych HBase utworzone w oryginalnym klastrze. Aby uniknąć niespójności, zaleca się wyłączenie tabel HBase przed usunięciem klastra.

## <a name="create-tables-and-insert-data"></a>Tworzenie tabel i wstawianie danych

Protokół SSH umożliwia połączenie z klastrami HBase, a następnie korzystanie z [powłoki Apache HBase](https://hbase.apache.org/0.94/book/shell.html) w celu tworzenia tabel bazy danych HBase, wstawiania danych i wykonywania zapytań o dane.

Dla większości użytkowników dane są wyświetlane w formacie tabelarycznym:

![Dane tabelaryczne HDInsight Apache HBase](./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-contacts-tabular.png)

W UBase (implementacja [Cloud BigTable)](https://cloud.google.com/bigtable/)te same dane wyglądają następująco:

![HdInsight Apache HBase BigTable danych](./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-contacts-bigtable.png)

**Korzystanie z powłoki HBase**

1. Użyj `ssh` polecenia, aby połączyć się z klastrem HBase. Edytuj poniższe polecenie, zastępując `CLUSTERNAME` nazwą klastra, a następnie wprowadź polecenie:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Użyj `hbase shell` polecenia, aby uruchomić powłokę interaktywną HBase. Wprowadź następujące polecenie w połączeniu SSH:

    ```bash
    hbase shell
    ```

1. Polecenie `create` służy do tworzenia tabeli bazy danych HBase z rodzinami dwukolumnowym. W nazwach tabel i kolumn rozróżniana jest wielkość liter. Wprowadź następujące polecenie:

    ```hbaseshell
    create 'Contacts', 'Personal', 'Office'
    ```

1. Użyj `list` polecenia, aby wyświetlić listę wszystkich tabel w bazie danych HBase. Wprowadź następujące polecenie:

    ```hbase
    list
    ```

1. Polecenie `put` służy do wstawiania wartości w określonej kolumnie w określonym wierszu w określonej tabeli. Wprowadź następujące polecenia:

    ```hbaseshell
    put 'Contacts', '1000', 'Personal:Name', 'John Dole'
    put 'Contacts', '1000', 'Personal:Phone', '1-425-000-0001'
    put 'Contacts', '1000', 'Office:Phone', '1-425-000-0002'
    put 'Contacts', '1000', 'Office:Address', '1111 San Gabriel Dr.'
    ```

1. Użyj `scan` polecenia, aby `Contacts` zeskanować i zwrócić dane tabeli. Wprowadź następujące polecenie:

    ```hbase
    scan 'Contacts'
    ```

    ![HDInsight Apache Hadoop Powłoka HBase](./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-shell.png)

1. Polecenie `get` służy do pobierania zawartości wiersza. Wprowadź następujące polecenie:

    ```hbaseshell
    get 'Contacts', '1000'
    ```

    Widzisz podobne wyniki jako `scan` przy użyciu polecenia, ponieważ istnieje tylko jeden wiersz.

    Aby uzyskać więcej informacji na temat schematu tabeli HBase, zobacz [Wprowadzenie do projektu schematu Bazy danych Apache HBase](http://0b4af6cdc2f0c5998459-c0245c5c937c5dedcca3f1764ecc9b2f.r43.cf2.rackcdn.com/9353-login1210_khurana.pdf). Więcej poleceń bazy danych HBase można znaleźć w [Podręczniku bazy danych Apache HBase](https://hbase.apache.org/book.html#quickstart).

1. Użyj `exit` polecenia, aby zatrzymać powłokę interaktywną HBase. Wprowadź następujące polecenie:

    ```hbaseshell
    exit
    ```

**Aby zbiorczo załadować dane do tabeli kontaktów HBase**

Baza danych HBase obsługuje kilka metod ładowania danych do tabel.  Aby uzyskać więcej informacji, zobacz temat [Ładowanie zbiorcze](https://hbase.apache.org/book.html#arch.bulk.load).

Przykładowy plik danych znajduje się w publicznym kontenerze obiektów blob, `wasb://hbasecontacts\@hditutorialdata.blob.core.windows.net/contacts.txt`.  Plik danych ma następującą zawartość:

    8396    Calvin Raji      230-555-0191    230-555-0191    5415 San Gabriel Dr.
    16600   Karen Wu         646-555-0113    230-555-0192    9265 La Paz
    4324    Karl Xie         508-555-0163    230-555-0193    4912 La Vuelta
    16891   Jonn Jackson     674-555-0110    230-555-0194    40 Ellis St.
    3273    Miguel Miller    397-555-0155    230-555-0195    6696 Anchor Drive
    3588    Osa Agbonile     592-555-0152    230-555-0196    1873 Lion Circle
    10272   Julia Lee        870-555-0110    230-555-0197    3148 Rose Street
    4868    Jose Hayes       599-555-0171    230-555-0198    793 Crawford Street
    4761    Caleb Alexander  670-555-0141    230-555-0199    4775 Kentucky Dr.
    16443   Terry Chander    998-555-0171    230-555-0200    771 Northridge Drive

Opcjonalnie możesz utworzyć plik tekstowy i przesłać go na swoje konto magazynu. Aby uzyskać instrukcje, zobacz [Przekazywanie danych dla zadań Apache Hadoop w HDInsight](../hdinsight-upload-data.md).

Ta procedura używa `Contacts` tabeli HBase utworzonej w ostatniej procedurze.

1. Z otwartego połączenia ssh uruchom następujące polecenie, aby przekształcić plik danych w StoreFiles i przechowywać w ścieżce względnej określonej przez `Dimporttsv.bulk.output`program .

    ```bash
    hbase org.apache.hadoop.hbase.mapreduce.ImportTsv -Dimporttsv.columns="HBASE_ROW_KEY,Personal:Name,Personal:Phone,Office:Phone,Office:Address" -Dimporttsv.bulk.output="/example/data/storeDataFileOutput" Contacts wasb://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt
    ```

2. Uruchom następujące polecenie, aby `/example/data/storeDataFileOutput` przekazać dane z tabeli HBase:

    ```bash
    hbase org.apache.hadoop.hbase.mapreduce.LoadIncrementalHFiles /example/data/storeDataFileOutput Contacts
    ```

3. Można otworzyć powłokę HBase i `scan` użyć polecenia do wyświetlenia listy zawartości tabeli.

## <a name="use-apache-hive-to-query-apache-hbase"></a>Użyj gałęzi Apache, aby zbadać Apache HBase

Dane można wysyłać do danych w tabelach bazy danych HBase za pomocą [funkcji Apache Hive](https://hive.apache.org/). W tej sekcji zostanie utworzona tabela programu Hive odwzorowująca dane w tabeli HBase, która będzie używana do wykonywania zapytań o dane w tabeli HBase.

1. Aby uruchomić beeline, z otwartego połączenia ssh użyj następującego polecenia:

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -n admin
    ```

    Aby uzyskać więcej informacji o usłudze Beeline, zobacz [Używanie technologii Hive z usługą Hadoop w usłudze HDInsight z usługą Beeline](../hadoop/apache-hadoop-use-hive-beeline.md).

1. Uruchom następujący skrypt [HiveQL,](https://cwiki.apache.org/confluence/display/Hive/LanguageManual) aby utworzyć tabelę hive, która jest mapowana do tabeli HBase. Przed uruchomieniem tej instrukcji upewnij się, że utworzono przykładową tabelę, do której odwołuje się wcześniej w tym artykule, przy użyciu powłoki HBase.

    ```hiveql
    CREATE EXTERNAL TABLE hbasecontacts(rowkey STRING, name STRING, homephone STRING, officephone STRING, officeaddress STRING)
    STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
    WITH SERDEPROPERTIES ('hbase.columns.mapping' = ':key,Personal:Name,Personal:Phone,Office:Phone,Office:Address')
    TBLPROPERTIES ('hbase.table.name' = 'Contacts');
    ```

1. Uruchom poniższy skrypt HiveQL, aby wykonać zapytanie o dane w tabeli HBase:

    ```hiveql
    SELECT count(rowkey) AS rk_count FROM hbasecontacts;
    ```

1. Aby wyjść z Beeline, użyj . `!exit`

1. Aby wyjść z połączenia `exit`ssh, użyj .

## <a name="use-hbase-rest-apis-using-curl"></a>Korzystanie z interfejsów API REST HBase przy użyciu programu Curl

Interfejs API REST jest zabezpieczony za pomocą [uwierzytelniania podstawowego](https://en.wikipedia.org/wiki/Basic_access_authentication). Należy zawsze tworzyć żądania przy użyciu protokołu HTTPS (HTTP Secure), aby mieć pewność, że poświadczenia są bezpiecznie wysyłane do serwera.

1. Ustaw zmienną środowiskową dla ułatwienia użytkowania. Edytuj poniższe polecenia, `MYPASSWORD` zastępując hasłem logowania klastra. Zamień `MYCLUSTERNAME` na nazwę klastra HBase. Następnie wprowadź polecenia.

    ```bash
    export password='MYPASSWORD'
    export clustername=MYCLUSTERNAME
    ```

1. Użyj następującego polecenia, aby wyświetlić listę istniejących tabel HBase:

    ```bash
    curl -u admin:$password \
    -G https://$clustername.azurehdinsight.net/hbaserest/
    ```

1. Użyj następującego polecenia, aby utworzyć nową tabelę HBase z dwiema rodzinami kolumn:

    ```bash
    curl -u admin:$password \
    -X PUT "https://$clustername.azurehdinsight.net/hbaserest/Contacts1/schema" \
    -H "Accept: application/json" \
    -H "Content-Type: application/json" \
    -d "{\"@name\":\"Contact1\",\"ColumnSchema\":[{\"name\":\"Personal\"},{\"name\":\"Office\"}]}" \
    -v
    ```

    Schemat jest podany w formacie JSon.
1. Użyj następującego polecenia, aby wstawić dane:

    ```bash
    curl -u admin:$password \
    -X PUT "https://$clustername.azurehdinsight.net/hbaserest/Contacts1/false-row-key" \
    -H "Accept: application/json" \
    -H "Content-Type: application/json" \
    -d "{\"Row\":[{\"key\":\"MTAwMA==\",\"Cell\": [{\"column\":\"UGVyc29uYWw6TmFtZQ==\", \"$\":\"Sm9obiBEb2xl\"}]}]}" \
    -v
    ```

    Base64 zakoduje wartości określone w przełączniku -d. W przykładzie:

   * MTAwMA==: 1000
   * UGVyc29uYWw6TmFtZQ==: Personal: Imię i nazwisko
   * Sm9obiBEb2xl: John Dole

     [false-row-key](https://hbase.apache.org/apidocs/org/apache/hadoop/hbase/rest/package-summary.html#operation_cell_store_single) umożliwia wstawianie wielu wartości (w partiach).

1. Użyj następującego polecenia, aby pobrać wiersz:

    ```bash
    curl -u admin:$password \
    GET "https://$clustername.azurehdinsight.net/hbaserest/Contacts1/1000" \
    -H "Accept: application/json" \
    -v
    ```

Aby uzyskać więcej informacji o interfejsie Rest HBase, zobacz [Apache HBase Reference Guide](https://hbase.apache.org/book.html#_rest) (Podręcznik referencyjny Apache HBase).

> [!NOTE]  
> Platforma Thrift nie jest obsługiwana przez bazę danych HBase w usłudze HDInsight.
>
> Używając programu Curl lub innego połączenia REST z usługą WebHCat, należy uwierzytelnić żądania, podając nazwę użytkownika i hasło administratora klastra usługi HDInsight. Należy również użyć nazwy klastra jako części identyfikatora URI stosowanego przy wysyłaniu żądań do serwera:
> 
>   
>        curl -u <UserName>:<Password> \
>        -G https://<ClusterName>.azurehdinsight.net/templeton/v1/status
>   
>    Powinna zostać zwrócona odpowiedź podobna do następującej:
>   
>        {"status":"ok","version":"v1"}

## <a name="check-cluster-status"></a>Sprawdzanie stanu klastra

Baza danych HBase w usłudze HDInsight jest dostarczana z interfejsem użytkownika sieci Web służącym do monitorowania klastrów. Za pośrednictwem interfejsu użytkownika sieci Web możesz przesyłać żądania dotyczące statystyk lub informacji o regionach.

**Aby uzyskać dostęp do głównego interfejsu użytkownika HBase**

1. Zaloguj się do interfejsu użytkownika `https://CLUSTERNAME.azurehdinsight.net` sieci `CLUSTERNAME` Web Ambari, gdzie jest nazwa klastra HBase.

1. Wybierz **HBase** z lewego menu.

1. Wybierz **szybkie łącza** u góry strony, wskaż aktywne łącze węzła Zookeeper, a następnie wybierz pozycję Interfejs użytkownika **wzorca bazy danych HBase**.  Interfejs użytkownika zostanie otwarty w innej karcie przeglądarki:

   ![Interfejs użytkownika HDInsight Apache HBase HMaster](./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-hmaster-ui.png)

   Główny interfejs użytkownika HBase zawiera następujące sekcje:

   - serwery regionów
   - wzorce kopii zapasowej
   - tabele
   - zadania
   - atrybuty oprogramowania

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Aby uniknąć niespójności, zaleca się wyłączenie tabel HBase przed usunięciem klastra. Można użyć polecenia `disable 'Contacts'`HBase . Jeśli nie zamierzasz nadal korzystać z tej aplikacji, usuń utworzony klaster bazy danych HBase, wykonując następujące czynności:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
1. W polu **Wyszukaj** w górnej części wpisz **HDInsight**.
1. Wybierz pozycję **Klastry usługi HDInsight** w obszarze **Usługi**.
1. Na wyświetlonej liście klastrów usługi HDInsight kliknij symbol **...** obok klastra utworzonego na potrzeby tego samouczka.
1. Kliknij **pozycję Usuń**. Kliknij **przycisk Tak**.

## <a name="next-steps"></a>Następne kroki

W tym samouczku dowiesz się, jak utworzyć klaster Apache HBase. I jak tworzyć tabele i wyświetlać dane w tych tabelach z powłoki HBase. Dowiesz się również, jak używać kwerendy hive na danych w tabelach HBase. I jak używać interfejsów API odzyskiwania danych HBase C# REST do tworzenia tabeli HBase i pobierania danych z tabeli. Aby dowiedzieć się więcej, zobacz:

> [!div class="nextstepaction"]
> [Omówienie bazy danych HDInsight HBase](./apache-hbase-overview.md)