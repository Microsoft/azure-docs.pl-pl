---
title: Migrowanie obciążeń platformy Azure HDInsight 3,6 do usługi HDInsight 4,0
description: Dowiedz się, jak migrować obciążenia Apache Hive w usłudze HDInsight 3,6 do usługi HDInsight 4,0.
author: msft-tacox
ms.author: tacox
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.date: 11/13/2019
ms.openlocfilehash: bcc0faa8fdbd61ab3e3e0886256f7c796e5a98e2
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96011510"
---
# <a name="migrate-azure-hdinsight-36-hive-workloads-to-hdinsight-40"></a>Migrowanie obciążeń platformy Azure HDInsight 3,6 do usługi HDInsight 4,0

W tym dokumencie przedstawiono sposób migrowania obciążeń Apache Hive i LLAP w usłudze HDInsight 3,6 do usługi HDInsight 4,0. Usługa HDInsight 4,0 udostępnia nowsze funkcje Hive i LLAP, takie jak widoki z materiałami i buforowanie wyników zapytania. Podczas migrowania obciążeń do usługi HDInsight 4,0 można korzystać z wielu nowszych funkcji programu Hive 3, które nie są dostępne w usłudze HDInsight 3,6.

W tym artykule opisano następujące zagadnienia:

* Migracja metadanych programu Hive do usługi HDInsight 4,0
* Bezpieczna migracja tabel KWASowych i niekwaśnych
* Zachowywanie zasad zabezpieczeń programu Hive w wersjach usługi HDInsight
* Wykonywanie zapytań i debugowanie z usługi HDInsight 3,6 do usługi HDInsight 4,0

Jedną z zalet programu Hive jest możliwość eksportowania metadanych do zewnętrznej bazy danych (określanej jako magazyn metadanych Hive). **Magazyn metadanych Hive** jest odpowiedzialny za przechowywanie statystyk tabeli, w tym lokalizacji magazynu tabel, nazw kolumn i informacji o indeksie tabeli. Usługi HDInsight 3,6 i HDInsight 4,0 wymagają różnych schematów magazynu metadanych i nie mogą współdzielić pojedynczego magazynu. Zalecanym sposobem na uaktualnienie magazyn metadanych Hive jest uaktualnienie kopii zamiast oryginału w bieżącym środowisku produkcyjnym. Ten dokument wymaga, aby oryginalne i nowe klastry miały dostęp do tego samego konta magazynu. W związku z tym nie obejmuje migracji danych do innego regionu.

## <a name="migrate-from-external-metastore"></a>Migrowanie z zewnętrznego magazynu metadanych

### <a name="1-run-major-compaction-on-acid-tables-in-hdinsight-36"></a>1. Uruchom kompaktowanie główne w tabelach KWASowych w usłudze HDInsight 3,6

Tabele KWASów HDInsight 3,6 i HDInsight 4,0 są w inny sposób zrozumiałe dla różnic KWASowych. Jedyną akcją wymaganą przed migracją jest uruchomienie "Wielkiej" kompaktowania dla każdej tabeli KWASowej w klastrze 3,6. Szczegółowe informacje na temat kompaktowania można znaleźć w [podręczniku języka Hive](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL#LanguageManualDDL-AlterTable/Partition/Compact) .

### <a name="2-copy-sql-database"></a>2. Kopiowanie bazy danych SQL
Utwórz nową kopię zewnętrznego magazynu metadanych. Jeśli używasz zewnętrznego magazynu metadanych, jeden z bezpiecznych i łatwych sposobów tworzenia kopii magazynu metadanych polega na [przywróceniu bazy danych](../../azure-sql/database/recovery-using-backups.md#point-in-time-restore) z inną nazwą przy użyciu `RESTORE` funkcji.  Zobacz [Używanie zewnętrznych magazynów metadanych w usłudze Azure HDInsight](../hdinsight-use-external-metadata-stores.md) , aby dowiedzieć się więcej na temat dołączania zewnętrznego magazynu Metadata do klastra usługi HDInsight.

### <a name="3-upgrade-metastore-schema"></a>3. Uaktualnij schemat magazynu metadanych
Po zakończeniu **kopiowania** magazynu metadanych Uruchom skrypt uaktualnienia schematu w [akcji skryptu](../hdinsight-hadoop-customize-cluster-linux.md) w istniejącym klastrze usługi HDInsight 3,6, aby uaktualnić nowy magazyn metadanych do schematu Hive 3. (Ten krok nie wymaga połączenia nowego magazynu metadanych z klastrem). Umożliwia to dołączenie bazy danych jako magazynu metadanych usługi HDInsight 4,0.

Skorzystaj z wartości w tabeli poniżej. Zamień na `SQLSERVERNAME DATABASENAME USERNAME PASSWORD` odpowiednie wartości magazyn metadanych Hive **kopii**, rozdzielone spacjami. Podczas określania nazwy serwera SQL nie dodawaj ". database.windows.net".

|Właściwość | Wartość |
|---|---|
|Typ skryptu|-Niestandardowe|
|Nazwa|Uaktualnienie programu Hive|
|Identyfikator URI skryptu bash|`https://hdiconfigactions.blob.core.windows.net/hivemetastoreschemaupgrade/launch-schema-upgrade.sh`|
|Typy węzłów|Head|
|Parametry|SQLSERVERNAME DATABASENAME — HASŁO UŻYTKOWNIKA|

> [!Warning]  
> Uaktualnienie, które konwertuje schemat metadanych usługi HDInsight 3,6 do schematu usługi HDInsight 4,0, nie może zostać cofnięte.

Uaktualnienie można sprawdzić, uruchamiając następujące zapytanie SQL względem bazy danych:

```sql
select * from dbo.version
```

### <a name="4-deploy-a-new-hdinsight-40-cluster"></a>4. Wdróż nowy klaster usługi HDInsight 4,0

1. Określ uaktualniony magazyn jako magazyn metadanych Hive nowego klastra.

1. Jednak rzeczywiste dane z tabel nie są dostępne, dopóki klaster nie będzie miał dostępu do niezbędnych kont magazynu.
Upewnij się, że konta magazynu tabel programu Hive w klastrze usługi HDInsight 3,6 są określone jako podstawowe lub pomocnicze konta magazynu dla nowego klastra usługi HDInsight 4,0.
Aby uzyskać więcej informacji na temat dodawania kont magazynu do klastrów usługi HDInsight, zobacz [Dodawanie dodatkowych kont magazynu do usługi HDInsight](../hdinsight-hadoop-add-storage.md).

### <a name="5-complete-migration-with-a-post-upgrade-tool-in-hdinsight-40"></a>5. Ukończ migrację za pomocą narzędzia po uaktualnieniu w usłudze HDInsight 4,0

Tabele zarządzane muszą być domyślnie zgodne z KWASem w usłudze HDInsight 4,0. Po zakończeniu migracji magazynu metadanych Uruchom narzędzie po uaktualnieniu, aby zapewnić, że tabele zarządzane w systemie innym niż kwas nie są zgodne z klastrem usługi HDInsight 4,0. To narzędzie zastosuje następującą konwersję:

|3,6 |4,0 |
|---|---|
|Tabele zewnętrzne|Tabele zewnętrzne|
|Zarządzane tabele niebędące KWASami|Tabele zewnętrzne z właściwością "External. Table. przeczyszczanie" = "true"|
|Tabele zarządzane przez kwas|Tabele zarządzane przez kwas|

Uruchom narzędzie Hive po uaktualnieniu z klastra usługi HDInsight 4,0 przy użyciu powłoki SSH:

1. Nawiąż połączenie z klastrem węzła głównego przy użyciu protokołu SSH. Aby uzyskać instrukcje, zobacz [nawiązywanie połączenia z usługą HDInsight przy użyciu protokołu SSH](../hdinsight-hadoop-linux-use-ssh-unix.md)
1. Otwórz powłokę logowania jako użytkownika programu Hive, uruchamiając `sudo su - hive`
1. Wykonaj następujące polecenie w powłoce.

    ```bash
    STACK_VERSION=$(hdp-select status hive-server2 | awk '{ print $3; }')
    /usr/hdp/$STACK_VERSION/hive/bin/hive --config /etc/hive/conf --service  strictmanagedmigration --hiveconf hive.strict.managed.tables=true -m automatic --modifyManagedTables
    ```

Po zakończeniu działania narzędzia magazyn Hive będzie gotowy do usługi HDInsight 4,0.

## <a name="migrate-from-internal-metastore"></a>Migrowanie z wewnętrznego magazynu metadanych

Jeśli klaster usługi HDInsight 3,6 używa wewnętrznego magazyn metadanych Hive, wykonaj poniższe kroki, aby uruchomić skrypt, który generuje zapytania Hive do eksportowania definicji obiektów z magazynu metadanych.

Klastry HDInsight 3,6 i 4,0 muszą korzystać z tego samego konta magazynu.

> [!NOTE]
>
> * W przypadku tabel z KWASem zostanie utworzona nowa kopia danych znajdujących się pod tabelą.
>
> * Ten skrypt obsługuje tylko migrację baz danych, tabel i partycji programu Hive. Należy ręcznie skopiować inne obiekty metadanych, takie jak widoki, UDF i ograniczenia tabeli.
>
> * Po zakończeniu tego skryptu zakłada się, że stary klaster nie będzie już używany do uzyskiwania dostępu do dowolnych tabel lub baz danych, do których odwołuje się w skrypcie.
>
> * Wszystkie tabele zarządzane staną się transakcyjne w usłudze HDInsight 4,0. Opcjonalnie należy zachować tabelę nietransakcyjną przez wyeksportowanie danych do tabeli zewnętrznej z właściwością "External. Table. przeczyszczanie" = "true". Na przykład
>
>    ```SQL
>    create table tablename_backup like tablename;
>    insert overwrite table tablename_backup select * from tablename;
>    create external table tablename_tmp like tablename;
>    insert overwrite table tablename_tmp select * from tablename;
>    alter table tablename_tmp set tblproperties('external.table.purge'='true');
>    drop table tablename;
>    alter table tablename_tmp rename to tablename;
>    ```

1. Połącz się z klastrem usługi HDInsight 3,6 przy użyciu [klienta Secure Shell (SSH)](../hdinsight-hadoop-linux-use-ssh-unix.md).

1. Z otwartej sesji SSH Pobierz następujący plik skryptu w celu wygenerowania pliku o nazwie **alltables. HQL**.

    ```bash
    wget https://hdiconfigactions.blob.core.windows.net/hivemetastoreschemaupgrade/exporthive_hdi_3_6.sh
    chmod 755 exporthive_hdi_3_6.sh
    ```

    * W przypadku zwykłego klastra usługi HDInsight bez ESP po prostu wykonaj polecenie `exporthive_hdi_3_6.sh` .

    * W przypadku klastra z partycją ESP narzędzie kinit i zmodyfikuj argumenty z usługi Beeline: Uruchom następujące polecenie, określając użytkownika i domenę dla użytkownika usługi Azure AD z pełnymi uprawnieniami Hive.

        ```bash
        USER="USER"  # replace USER
        DOMAIN="DOMAIN"  # replace DOMAIN
        DOMAIN_UPPER=$(printf "%s" "$DOMAIN" | awk '{ print toupper($0) }')
        kinit "$USER@$DOMAIN_UPPER"
        ```

        ```bash
        hn0=$(grep hn0- /etc/hosts | xargs | cut -d' ' -f4)
        BEE_CMD="beeline -u 'jdbc:hive2://$hn0:10001/default;principal=hive/_HOST@$DOMAIN_UPPER;auth-kerberos;transportMode=http' -n "$USER@$DOMAIN" --showHeader=false --silent=true --outputformat=tsv2 -e"
        ./exporthive_hdi_3_6.sh "$BEE_CMD"
        ```

1. Zamknij sesję SSH. Następnie wprowadź polecenie SCP, aby pobrać **alltables. HQL** lokalnie.

    ```bash
    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:alltables.hql c:/hdi
    ```

1. Przekaż **alltables. HQL** do *nowego* klastra usługi HDInsight.

    ```bash
    scp c:/hdi/alltables.hql sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/home/sshuser/
    ```

1. Następnie użyj protokołu SSH, aby nawiązać połączenie z *nowym* klastrem usługi HDInsight 4,0. Uruchom następujący kod z sesji SSH do tego klastra:

    Bez ESP:

    ```bash
    beeline -u "jdbc:hive2://localhost:10001/;transportMode=http" -f alltables.hql
    ```

    Za pomocą ESP:

    ```bash
    USER="USER"  # replace USER
    DOMAIN="DOMAIN"  # replace DOMAIN
    DOMAIN_UPPER=$(printf "%s" "$DOMAIN" | awk '{ print toupper($0) }')
    kinit "$USER@$DOMAIN_UPPER"
    ```

    ```bash
    hn0=$(grep hn0- /etc/hosts | xargs | cut -d' ' -f4)
    beeline -u "jdbc:hive2://$hn0:10001/default;principal=hive/_HOST@$DOMAIN_UPPER;auth-kerberos;transportMode=http" -n "$USER@$DOMAIN" -f alltables.hql
    ```

Narzędzie po uaktualnieniu dla migracji zewnętrznej magazynu metadanych nie ma zastosowania w tym miejscu, ponieważ tabele zarządzane niekwaśne z usługi HDInsight 3,6 konwertują na tabele zarządzane w środowisku HDInsight 4,0.

> [!Important]  
> Tabele zarządzane w usłudze HDInsight 4,0 (w tym tabele migrowane z 3,6) nie powinny mieć dostępu do innych usług ani aplikacji, w tym klastrów usługi HDInsight 3,6.

## <a name="secure-hive-across-hdinsight-versions"></a>Zabezpieczanie usługi Hive w wersjach usługi HDInsight

Ponieważ Usługa HDInsight 3,6, Usługa HDInsight integruje się z Azure Active Directory przy użyciu pakiet Enterprise Security usługi HDInsight (ESP). ESP używa protokołów Kerberos i Apache Ranger do zarządzania uprawnieniami określonych zasobów w klastrze. Zasady Ranger wdrożone w usłudze Hive w usłudze HDInsight 3,6 można migrować do usługi HDInsight 4,0, wykonując następujące czynności:

1. Przejdź do panelu Ranger Service Manager w klastrze usługi HDInsight 3,6.
2. Przejdź do zasad o nazwie **Hive** i wyeksportuj zasady do pliku JSON.
3. Upewnij się, że wszyscy użytkownicy określeni w wyeksportowanym pliku JSON zasad istnieją w nowym klastrze. Jeśli użytkownik jest określony w pliku JSON zasad, ale nie istnieje w nowym klastrze, należy dodać użytkownika do nowego klastra lub usunąć odwołanie z zasad.
4. Przejdź do panelu **Ranger Service Manager** w klastrze usługi HDInsight 4,0.
5. Przejdź do zasad o nazwie **Hive** i zaimportuj kod JSON zasad Ranger z kroku 2.

## <a name="check-compatibility-and-modify-codes-as-needed-in-test-app"></a>Sprawdź zgodność i modyfikowanie kodów zgodnie z wymaganiami w aplikacji testowej

Podczas migrowania obciążeń, takich jak istniejące programy i zapytania, należy zapoznać się z informacjami o wersji i dokumentacją dotyczącymi zmian i zastosować zmiany w razie potrzeby. Jeśli klaster usługi HDInsight 3,6 używa udostępnionej platformy Spark i magazyn metadanych Hive, wymagana jest [dodatkowa konfiguracja łącznika magazynu Hive](./apache-hive-warehouse-connector.md) .

## <a name="deploy-new-app-for-production"></a>Wdróż nową aplikację dla środowiska produkcyjnego

Aby przełączyć się do nowego klastra, na przykład, można zainstalować nową aplikację kliencką i użyć jej jako nowego środowiska produkcyjnego. można też uaktualnić istniejącą aplikację kliencką i przełączyć się do usługi HDInsight 4,0.

## <a name="switch-hdinsight-40-to-the-production"></a>Przełączenie usługi HDInsight 4,0 do środowiska produkcyjnego

Jeśli w magazynie metadanych zostały utworzone różnice podczas testowania, należy zaktualizować zmiany tuż przed przełączeniem. W takim przypadku można wyeksportować & zaimportować magazyn metadanych, a następnie ponownie go uaktualnić.

## <a name="remove-the-old-production"></a>Usuń stare środowisko produkcyjne

Po potwierdzeniu, że wydanie jest kompletne i w pełni funkcjonalne, można usunąć wersję 3,6 i poprzedni magazyn metadanych. Przed usunięciem środowiska upewnij się, że wszystkie elementy zostały zmigrowane.

## <a name="query-execution-across-hdinsight-versions"></a>Wykonywanie zapytania w wersjach usługi HDInsight

Istnieją dwa sposoby wykonywania i debugowania zapytań Hive/LLAP w klastrze usługi HDInsight 3,6. HiveCLI udostępnia środowisko wiersza polecenia, a widok [tez/Hive](../hadoop/apache-hadoop-use-hive-ambari-view.md) zawiera przepływ pracy oparty na graficznym interfejsie użytkownika.

W usłudze HDInsight 4,0 HiveCLI został zastąpiony Z usługi Beeline. Widok tez/Hive zawiera przepływ pracy oparty na graficznym interfejsie użytkownika. HiveCLI to Thrift Client for Hiveserver 1, a Z usługi Beeline to klient JDBC, który zapewnia dostęp do Hiveserver 2. Z usługi Beeline również może służyć do nawiązywania połączenia z dowolnym innym punktem końcowym bazy danych zgodnym z JDBC. Usługa z usługi Beeline jest dostępna w przypadku usługi HDInsight 4,0 bez konieczności instalacji.

## <a name="next-steps"></a>Następne kroki

* [Anons usługi HDInsight 4,0](../hdinsight-version-release.md)
* [HDInsight 4,0 głębokie szczegółowe](https://azure.microsoft.com/blog/deep-dive-into-azure-hdinsight-4-0/)
* [Tabele KWASów Hive 3](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.1.0/using-hiveql/content/hive_3_internals.html)