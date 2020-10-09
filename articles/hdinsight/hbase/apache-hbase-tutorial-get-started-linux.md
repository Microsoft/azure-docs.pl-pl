---
title: Samouczek — korzystanie z platformy Apache HBase w usłudze Azure HDInsight
description: Postępuj zgodnie z tym samouczkiem Apache HBase, aby rozpocząć korzystanie z usługi Hadoop w usłudze HDInsight. Utwórz tabele z poziomu powłoki HBase i wykonuj zapytania przy użyciu aplikacji Hive.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: tutorial
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 04/14/2020
ms.openlocfilehash: a19e2c6647f1ff072c61044e8e5777d5d3f8d2db
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85958365"
---
# <a name="tutorial-use-apache-hbase-in-azure-hdinsight"></a>Samouczek: korzystanie z platformy Apache HBase w usłudze Azure HDInsight

W tym samouczku pokazano, jak utworzyć klaster Apache HBase w usłudze Azure HDInsight, utworzyć tabele HBase i tabele zapytań przy użyciu Apache Hive.  Aby uzyskać ogólne informacje o bazie danych HBase, zobacz [Omówienie bazy danych HBase w usłudze HDInsight](./apache-hbase-overview.md).

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie klastra Apache HBase
> * Tworzenie tabel HBase i wstawianie danych
> * Używanie Apache Hive do wysyłania zapytań do HBase Apache
> * Korzystanie z interfejsów API REST HBase przy użyciu programu Curl
> * Sprawdzanie stanu klastra

## <a name="prerequisites"></a>Wymagania wstępne

* Klient SSH. Aby uzyskać więcej informacji, zobacz [Łączenie się z usługą HDInsight (Apache Hadoop) przy użyciu protokołu SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

* Bash. W przykładach w tym artykule użyto powłoki bash w systemie Windows 10 dla poleceń zwinięcie. Aby uzyskać instrukcje dotyczące instalacji, zobacz artykuł [podsystem instalacji systemu Windows dla systemu Linux w systemie Windows 10](https://docs.microsoft.com/windows/wsl/install-win10) .  Inne [powłoki systemu UNIX](https://www.gnu.org/software/bash/) również będą działały.  Przykłady Zastąp, z niewielkimi modyfikacjami, mogą współpracować z wierszem polecenia systemu Windows.  Można też użyć polecenia cmdlet programu Windows PowerShell [Invoke-RestMethod](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/invoke-restmethod).

## <a name="create-apache-hbase-cluster"></a>Tworzenie klastra Apache HBase

Poniższa procedura używa szablonu Azure Resource Manager do utworzenia klastra HBase. Szablon tworzy również zależne domyślne konto usługi Azure Storage. Aby zapoznać się z parametrami używanymi w tej procedurze oraz innymi metodami tworzenia klastra, zobacz temat [Tworzenie opartych na systemie Linux klastrów Hadoop w usłudze HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

1. Wybierz Poniższy obraz, aby otworzyć szablon w Azure Portal. Szablon znajduje się w szablonach [szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/).

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-linux%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hbase-tutorial-get-started-linux/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

2. W oknie dialogowym **wdrożenie niestandardowe** wprowadź następujące wartości:

    |Właściwość |Opis |
    |---|---|
    |Subskrypcja|Wybierz subskrypcję platformy Azure, która jest używana do tworzenia klastra.|
    |Grupa zasobów|Utwórz grupę usługi Azure Resource Management lub Użyj istniejącej grupy.|
    |Lokalizacja|Określ lokalizację grupy zasobów. |
    |NazwaKlastra|Wprowadź nazwę dla klastra HBase.|
    |Nazwa użytkownika i hasło logowania do klastra|Domyślna nazwa logowania to **admin**.|
    |Nazwa użytkownika i hasło protokołu SSH|Domyślna nazwa użytkownika to **sshuser**.|

    Inne parametry są opcjonalne.  

    Każdy klaster zależy od konta usługi Azure Storage. Po usunięciu klastra dane pozostają na koncie magazynu. Domyślna nazwa konta magazynu klastra to nazwa klastra z dołączonym ciągiem „store”. Jest to stałe w sekcji zmienne szablonu.

3. Zaznacz pozycję **Wyrażam zgodę na powyższe warunki i postanowienia**, a następnie kliknij przycisk **Kup**. Utworzenie klastra trwa około 20 minut.

Po usunięciu klastra HBase można utworzyć inny klaster HBase za pomocą tego samego domyślnego kontenera obiektów blob. Nowy klaster przejmuje tabele bazy danych HBase utworzone w oryginalnym klastrze. Aby uniknąć niespójności, zaleca się wyłączenie tabel HBase przed usunięciem klastra.

## <a name="create-tables-and-insert-data"></a>Tworzenie tabel i wstawianie danych

Protokół SSH umożliwia połączenie z klastrami HBase, a następnie korzystanie z [powłoki Apache HBase](https://hbase.apache.org/0.94/book/shell.html) w celu tworzenia tabel bazy danych HBase, wstawiania danych i wykonywania zapytań o dane.

Dla większości użytkowników dane są wyświetlane w formacie tabelarycznym:

![Dane tabelaryczne usługi HDInsight Apache HBase](./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-contacts-tabular.png)

W HBase (implementacja BigTable w [chmurze](https://cloud.google.com/bigtable/)) te same dane wyglądają następująco:

![Dane usługi HDInsight Apache HBase BigTable](./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-contacts-bigtable.png)

**Korzystanie z powłoki HBase**

1. Użyj `ssh` polecenia, aby nawiązać połączenie z klastrem HBase. Edytuj poniższe polecenie, zastępując je `CLUSTERNAME` nazwą klastra, a następnie wprowadź polecenie:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Użyj `hbase shell` polecenia, aby uruchomić powłokę interaktywną HBase. Wprowadź następujące polecenie w połączeniu SSH:

    ```bash
    hbase shell
    ```

1. Użyj `create` polecenia, aby utworzyć tabelę HBase z rodziną dwóch kolumn. W nazwach tabel i kolumn jest rozróżniana wielkość liter. Wprowadź następujące polecenie:

    ```hbaseshell
    create 'Contacts', 'Personal', 'Office'
    ```

1. Użyj `list` polecenia, aby wyświetlić listę wszystkich tabel w HBase. Wprowadź następujące polecenie:

    ```hbase
    list
    ```

1. Użyj `put` polecenia, aby wstawić wartości w określonej kolumnie w określonym wierszu w określonej tabeli. Wprowadź następujące polecenia:

    ```hbaseshell
    put 'Contacts', '1000', 'Personal:Name', 'John Dole'
    put 'Contacts', '1000', 'Personal:Phone', '1-425-000-0001'
    put 'Contacts', '1000', 'Office:Phone', '1-425-000-0002'
    put 'Contacts', '1000', 'Office:Address', '1111 San Gabriel Dr.'
    ```

1. Użyj `scan` polecenia, aby przeskanować i zwrócić `Contacts` dane tabeli. Wprowadź następujące polecenie:

    ```hbase
    scan 'Contacts'
    ```

    ![Usługa HDInsight Apache Hadoop HBase Shell](./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-shell.png)

1. Użyj `get` polecenia, aby pobrać zawartość wiersza. Wprowadź następujące polecenie:

    ```hbaseshell
    get 'Contacts', '1000'
    ```

    Podobne wyniki można zobaczyć, używając `scan` polecenia, ponieważ istnieje tylko jeden wiersz.

    Aby uzyskać więcej informacji na temat schematu tabeli HBase, zobacz [wprowadzenie do projektu schematu Apache HBase](http://0b4af6cdc2f0c5998459-c0245c5c937c5dedcca3f1764ecc9b2f.r43.cf2.rackcdn.com/9353-login1210_khurana.pdf). Więcej poleceń bazy danych HBase można znaleźć w [Podręczniku bazy danych Apache HBase](https://hbase.apache.org/book.html#quickstart).

1. Użyj `exit` polecenia, aby zatrzymać powłokę interaktywną HBase. Wprowadź następujące polecenie:

    ```hbaseshell
    exit
    ```

**Aby zbiorczo załadować dane do tabeli kontaktów HBase**

Baza danych HBase obsługuje kilka metod ładowania danych do tabel.  Aby uzyskać więcej informacji, zobacz temat [Ładowanie zbiorcze](https://hbase.apache.org/book.html#arch.bulk.load).

Przykładowy plik danych znajduje się w publicznym kontenerze obiektów blob, `wasb://hbasecontacts\@hditutorialdata.blob.core.windows.net/contacts.txt`.  Plik danych ma następującą zawartość:

`8396    Calvin Raji      230-555-0191    230-555-0191    5415 San Gabriel Dr.`

`16600   Karen Wu         646-555-0113    230-555-0192    9265 La Paz`

`4324    Karl Xie         508-555-0163    230-555-0193    4912 La Vuelta`

`16891   Jonn Jackson     674-555-0110    230-555-0194    40 Ellis St.`

`3273    Miguel Miller    397-555-0155    230-555-0195    6696 Anchor Drive`

`3588    Osa Agbonile     592-555-0152    230-555-0196    1873 Lion Circle`

`10272   Julia Lee        870-555-0110    230-555-0197    3148 Rose Street`

`4868    Jose Hayes       599-555-0171    230-555-0198    793 Crawford Street`

`4761    Caleb Alexander  670-555-0141    230-555-0199    4775 Kentucky Dr.`

`16443   Terry Chander    998-555-0171    230-555-0200    771 Northridge Drive`

Opcjonalnie możesz utworzyć plik tekstowy i przesłać go na swoje konto magazynu. Aby uzyskać instrukcje, zobacz [przekazywanie danych dla zadań Apache Hadoop w usłudze HDInsight](../hdinsight-upload-data.md).

Ta procedura korzysta z `Contacts` tabeli HBase utworzonej w ostatniej procedurze.

1. Z otwartego połączenia SSH Uruchom następujące polecenie, aby przekształcić plik danych do postaci storefiles i przechowywać ją w ścieżce względnej określonej przez `Dimporttsv.bulk.output` .

    ```bash
    hbase org.apache.hadoop.hbase.mapreduce.ImportTsv -Dimporttsv.columns="HBASE_ROW_KEY,Personal:Name,Personal:Phone,Office:Phone,Office:Address" -Dimporttsv.bulk.output="/example/data/storeDataFileOutput" Contacts wasb://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt
    ```

2. Uruchom następujące polecenie, aby przekazać dane z `/example/data/storeDataFileOutput` do tabeli HBase:

    ```bash
    hbase org.apache.hadoop.hbase.mapreduce.LoadIncrementalHFiles /example/data/storeDataFileOutput Contacts
    ```

3. Możesz otworzyć powłokę HBase i użyć `scan` polecenia, aby wyświetlić listę zawartości tabeli.

## <a name="use-apache-hive-to-query-apache-hbase"></a>Używanie Apache Hive do wysyłania zapytań do HBase Apache

Możesz wykonywać zapytania dotyczące danych w tabelach HBase przy użyciu [Apache Hive](https://hive.apache.org/). W tej sekcji zostanie utworzona tabela programu Hive odwzorowująca dane w tabeli HBase, która będzie używana do wykonywania zapytań o dane w tabeli HBase.

1. Z otwartego połączenia SSH Użyj następującego polecenia, aby rozpocząć Z usługi Beeline:

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -n admin
    ```

    Aby uzyskać więcej informacji o usłudze Beeline, zobacz [Używanie technologii Hive z usługą Hadoop w usłudze HDInsight z usługą Beeline](../hadoop/apache-hadoop-use-hive-beeline.md).

1. Uruchom następujący skrypt [HiveQL](https://cwiki.apache.org/confluence/display/Hive/LanguageManual) , aby utworzyć tabelę programu Hive, która jest mapowana na tabelę HBase. Przed uruchomieniem tej instrukcji upewnij się, że utworzono przykładową tabelę, do której odwołuje się wcześniej w tym artykule, przy użyciu powłoki HBase.

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

1. Aby wyjść z Z usługi Beeline, użyj polecenia `!exit` .

1. Aby wyjść z połączenia SSH, użyj polecenia `exit` .

## <a name="use-hbase-rest-apis-using-curl"></a>Korzystanie z interfejsów API REST HBase przy użyciu programu Curl

Interfejs API REST jest zabezpieczony za pomocą [uwierzytelniania podstawowego](https://en.wikipedia.org/wiki/Basic_access_authentication). Należy zawsze tworzyć żądania przy użyciu protokołu HTTPS (HTTP Secure), aby mieć pewność, że poświadczenia są bezpiecznie wysyłane do serwera.

1. Ustaw zmienną środowiskową, aby ułatwić korzystanie z niej. Edytuj poniższe polecenia, zastępując je `MYPASSWORD` hasłem logowania klastra. Zamień `MYCLUSTERNAME` na nazwę klastra HBase. Następnie wprowadź polecenia.

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

    Zakodowane w formacie base64 wartości określone w przełączniku-d. W przykładzie:

   * MTAwMA==: 1000
   * UGVyc29uYWw6TmFtZQ = =: Personal: Name
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
> `curl -u <UserName>:<Password> \`
>
> `-G https://<ClusterName>.azurehdinsight.net/templeton/v1/status`
>
> Powinna zostać zwrócona odpowiedź podobna do następującej:
>
> `{"status":"ok","version":"v1"}`

## <a name="check-cluster-status"></a>Sprawdzanie stanu klastra

Baza danych HBase w usłudze HDInsight jest dostarczana z interfejsem użytkownika sieci Web służącym do monitorowania klastrów. Za pośrednictwem interfejsu użytkownika sieci Web możesz przesyłać żądania dotyczące statystyk lub informacji o regionach.

**Aby uzyskać dostęp do głównego interfejsu użytkownika HBase**

1. Zaloguj się do interfejsu użytkownika sieci Web Ambari, `https://CLUSTERNAME.azurehdinsight.net` gdzie `CLUSTERNAME` jest nazwą klastra HBase.

1. Z menu po lewej stronie wybierz pozycję **HBase** .

1. W górnej części strony wybierz pozycję **szybkie linki** , wskaż pozycję Active Node dozorcy link, a następnie wybierz pozycję **interfejs użytkownika HBase Master**.  Interfejs użytkownika zostanie otwarty w innej karcie przeglądarki:

   ![Interfejs użytkownika usługi HDInsight Apache HBase serwera hmaster](./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-hmaster-ui.png)

   Główny interfejs użytkownika HBase zawiera następujące sekcje:

   - serwery regionów
   - wzorce kopii zapasowej
   - tabele
   - zadania
   - atrybuty oprogramowania

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Aby uniknąć niespójności, zaleca się wyłączenie tabel HBase przed usunięciem klastra. Można użyć polecenia HBase `disable 'Contacts'` . Jeśli nie zamierzasz nadal korzystać z tej aplikacji, usuń utworzony klaster bazy danych HBase, wykonując następujące czynności:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
1. W polu **Wyszukaj** w górnej części wpisz **HDInsight**.
1. Wybierz pozycję **Klastry usługi HDInsight** w obszarze **Usługi**.
1. Na wyświetlonej liście klastrów usługi HDInsight kliknij symbol **...** obok klastra utworzonego na potrzeby tego samouczka.
1. Kliknij polecenie **Usuń**. Kliknij przycisk **Yes** (Tak).

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób tworzenia klastra Apache HBase. I jak tworzyć tabele i wyświetlać dane w tych tabelach z poziomu powłoki HBase. Wiesz również, jak używać zapytania Hive dla danych w tabelach HBase. I jak używać interfejsów API REST HBase języka C# do tworzenia tabeli HBase i pobierania danych z tabeli. Aby dowiedzieć się więcej, zobacz:

> [!div class="nextstepaction"]
> [HBase usługi HDInsight — Omówienie](./apache-hbase-overview.md)