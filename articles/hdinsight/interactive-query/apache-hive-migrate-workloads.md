---
title: Migrowanie obciążeń platformy Azure HDInsight 3,6 do usługi HDInsight 4,0
description: Dowiedz się, jak migrować obciążenia Apache Hive w usłudze HDInsight 3,6 do usługi HDInsight 4,0.
author: msft-tacox
ms.author: tacox
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 11/13/2019
ms.openlocfilehash: ec96189185a06c1fcbd95eed6216ade47f3089c3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79214646"
---
# <a name="migrate-azure-hdinsight-36-hive-workloads-to-hdinsight-40"></a>Migrowanie obciążeń platformy Azure HDInsight 3,6 do usługi HDInsight 4,0

W tym dokumencie przedstawiono sposób migrowania obciążeń Apache Hive i LLAP w usłudze HDInsight 3,6 do usługi HDInsight 4,0. Usługa HDInsight 4,0 udostępnia nowsze funkcje Hive i LLAP, takie jak widoki z materiałami i buforowanie wyników zapytania. Podczas migrowania obciążeń do usługi HDInsight 4,0 można korzystać z wielu nowszych funkcji programu Hive 3, które nie są dostępne w usłudze HDInsight 3,6.

W tym artykule opisano następujące zagadnienia:

* Migracja metadanych programu Hive do usługi HDInsight 4,0
* Bezpieczna migracja tabel KWASowych i niekwaśnych
* Zachowywanie zasad zabezpieczeń programu Hive w wersjach usługi HDInsight
* Wykonywanie zapytań i debugowanie z usługi HDInsight 3,6 do usługi HDInsight 4,0

Jedną z zalet programu Hive jest możliwość eksportowania metadanych do zewnętrznej bazy danych (określanej jako magazyn metadanych Hive). **Magazyn metadanych Hive** jest odpowiedzialny za przechowywanie statystyk tabeli, w tym lokalizacji magazynu tabel, nazw kolumn i informacji o indeksie tabeli. Schemat bazy danych magazynu metadanych różni się między wersjami programu Hive. Zalecanym sposobem uaktualnienia magazyn metadanych Hive jest utworzenie kopii i uaktualnienie kopii zamiast bieżącego środowiska produkcyjnego.

## <a name="copy-metastore"></a>Kopiuj magazyn metadanych

Usługi HDInsight 3,6 i HDInsight 4,0 wymagają różnych schematów magazynu metadanych i nie mogą współdzielić pojedynczego magazynu.

### <a name="external-metastore"></a>Zewnętrzny magazyn metadanych

Utwórz nową kopię zewnętrznego magazynu metadanych. Jeśli używasz zewnętrznego magazynu metadanych, jeden z bezpiecznych i łatwych sposobów tworzenia kopii magazynu metadanych polega na [przywróceniu bazy danych](../../sql-database/sql-database-recovery-using-backups.md#point-in-time-restore) z inną nazwą przy użyciu funkcji przywracania SQL Database.  Zobacz [Używanie zewnętrznych magazynów metadanych w usłudze Azure HDInsight](../hdinsight-use-external-metadata-stores.md) , aby dowiedzieć się więcej na temat dołączania zewnętrznego magazynu Metadata do klastra usługi HDInsight.

### <a name="internal-metastore"></a>Wewnętrzny magazyn metadanych

Jeśli używasz wewnętrznego magazynu metadanych, możesz użyć zapytań do eksportowania definicji obiektów w magazyn metadanych Hive i zaimportować je do nowej bazy danych.

Po zakończeniu tego skryptu zakłada się, że stary klaster nie będzie już używany do uzyskiwania dostępu do dowolnych tabel lub baz danych, do których odwołuje się w skrypcie.

> [!NOTE]
> W przypadku tabel z KWASem zostanie utworzona nowa kopia danych znajdujących się pod tabelą.

1. Połącz się z klastrem usługi HDInsight przy użyciu [klienta Secure Shell (SSH)](../hdinsight-hadoop-linux-use-ssh-unix.md).

1. Aby nawiązać połączenie z usługą serwera hiveserver2 z Twoim [klientem z usługi Beeline](../hadoop/apache-hadoop-use-hive-beeline.md) , wprowadź następujące polecenie:

    ```hiveql
    for d in `beeline -u "jdbc:hive2://localhost:10001/;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "show databases;"`; 
    do
        echo "Scanning Database: $d"
        echo "create database if not exists $d; use $d;" >> alltables.hql; 
        for t in `beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "show tables;"`;
        do
            echo "Copying Table: $t"
            ddl=`beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "show create table $t;"`;

            echo "$ddl;" >> alltables.hql;
            lowerddl=$(echo $ddl | awk '{print tolower($0)}')
            if [[ $lowerddl == *"'transactional'='true'"* ]]; then
                if [[ $lowerddl == *"partitioned by"* ]]; then
                    # partitioned
                    raw_cols=$(beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "show create table $t;" | tr '\n' ' ' | grep -io "CREATE TABLE .*" | cut -d"(" -f2- | cut -f1 -d")" | sed 's/`//g');
                    ptn_cols=$(beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "show create table $t;" | tr '\n' ' ' | grep -io "PARTITIONED BY .*" | cut -f1 -d")" | cut -d"(" -f2- | sed 's/`//g');
                    final_cols=$(echo "(" $raw_cols "," $ptn_cols ")")

                    beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "create external table ext_$t $final_cols TBLPROPERTIES ('transactional'='false');";
                    beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "insert into ext_$t select * from $t;";
                    staging_ddl=`beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "show create table ext_$t;"`;
                    dir=$(echo $staging_ddl | grep -io " LOCATION .*" | grep -m1 -o "'.*" | sed "s/'[^-]*//2g" | cut -c2-);

                    parsed_ptn_cols=$(echo $ptn_cols| sed 's/ [a-z]*,/,/g' | sed '$s/\w*$//g');
                    echo "create table flattened_$t $final_cols;" >> alltables.hql;
                    echo "load data inpath '$dir' into table flattened_$t;" >> alltables.hql;
                    echo "insert into $t partition($parsed_ptn_cols) select * from flattened_$t;" >> alltables.hql;
                    echo "drop table flattened_$t;" >> alltables.hql;
                    beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "drop table ext_$t";
                else
                    # not partitioned
                    beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "create external table ext_$t like $t TBLPROPERTIES ('transactional'='false');";
                    staging_ddl=`beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "show create table ext_$t;"`;
                    dir=$(echo $staging_ddl | grep -io " LOCATION .*" | grep -m1 -o "'.*" | sed "s/'[^-]*//2g" | cut -c2-);

                    beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "insert into ext_$t select * from $t;";
                    echo "load data inpath '$dir' into table $t;" >> alltables.hql;
                    beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "drop table ext_$t";
                fi
            fi
            echo "$ddl" | grep -q "PARTITIONED\s*BY" && echo "MSCK REPAIR TABLE $t;" >> alltables.hql;
        done;
    done
    ```

    To polecenie generuje plik o nazwie **alltables. HQL**.

1. Zamknij sesję SSH. Następnie wprowadź polecenie SCP, aby pobrać **alltables. HQL** lokalnie.

    ```bash
    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:alltables.hql c:/hdi
    ```

1. Przekaż **alltables. HQL** do *nowego* klastra usługi HDInsight.

    ```bash
    scp c:/hdi/alltables.hql sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/home/sshuser/
    ```

1. Następnie użyj protokołu SSH, aby nawiązać połączenie z *nowym* klastrem usługi HDInsight. Uruchom następujący kod z sesji SSH:

    ```bash
    beeline -u "jdbc:hive2://localhost:10001/;transportMode=http" -i alltables.hql
    ```


## <a name="upgrade-metastore"></a>Uaktualnij magazyn metadanych

Po zakończeniu **kopiowania** magazynu metadanych Uruchom skrypt uaktualnienia schematu w [akcji skryptu](../hdinsight-hadoop-customize-cluster-linux.md) w istniejącym klastrze usługi HDInsight 3,6, aby uaktualnić nowy magazyn metadanych do schematu Hive 3. Umożliwia to dołączenie bazy danych jako magazynu metadanych usługi HDInsight 4,0.

Skorzystaj z wartości w tabeli poniżej. Zamień `SQLSERVERNAME DATABASENAME USERNAME PASSWORD` na odpowiednie wartości dla **skopiowanego** magazyn metadanych Hive, rozdzielone spacjami. Podczas określania nazwy serwera SQL nie dodawaj ". database.windows.net".

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

## <a name="migrate-hive-tables-to-hdinsight-40"></a>Migrowanie tabel programu Hive do usługi HDInsight 4,0

Po ukończeniu poprzedniego zestawu kroków w celu migrowania magazynu metadanych Hive do usługi HDInsight 4,0 tabele i bazy danych zarejestrowane w magazynie metadanych będą widoczne w klastrze usługi HDInsight 4,0 przez wykonanie `show tables` lub `show databases` z poziomu klastra. Aby uzyskać informacje na temat wykonywania zapytań w klastrach usługi HDInsight 4,0, zobacz [wykonywanie zapytań w różnych wersjach usługi HDInsight](#query-execution-across-hdinsight-versions) .

Jednak rzeczywiste dane z tabel nie są dostępne, dopóki klaster nie będzie miał dostępu do niezbędnych kont magazynu. Aby upewnić się, że klaster usługi HDInsight 4,0 ma dostęp do tych samych danych co stary klaster usługi HDInsight 3,6, wykonaj następujące czynności:

1. Określ konto usługi Azure Storage w tabeli lub bazie danych.

1. Jeśli klaster HDInsight 4,0 jest już uruchomiony, Dołącz konto usługi Azure Storage do klastra za pośrednictwem Ambari. Jeśli klaster HDInsight 4,0 nie został jeszcze utworzony, upewnij się, że konto usługi Azure Storage jest określone jako konto podstawowego lub pomocniczego magazynu klastra. Aby uzyskać więcej informacji na temat dodawania kont magazynu do klastrów usługi HDInsight, zobacz [Dodawanie dodatkowych kont magazynu do usługi HDInsight](../hdinsight-hadoop-add-storage.md).

## <a name="deploy-new-hdinsight-40-and-connect-to-the-new-metastore"></a>Wdrażanie nowej usługi HDInsight 4,0 i nawiązywanie połączenia z nowym magazynem metadanych

Po zakończeniu uaktualniania schematu należy wdrożyć nowy klaster usługi HDInsight 4,0 i połączyć uaktualniony magazyn. Jeśli wdrożono już 4,0, ustaw ją tak, aby można było nawiązać połączenie z magazynem metadanych z Ambari.

## <a name="run-schema-migration-script-from-hdinsight-40"></a>Uruchom skrypt migracji schematu z usługi HDInsight 4,0

Tabele są traktowane inaczej w usłudze HDInsight 3,6 i HDInsight 4,0. Z tego powodu nie można współużytkować tych samych tabel dla klastrów różnych wersji. Jeśli chcesz używać usługi HDInsight 3,6 w tym samym czasie co Usługa HDInsight 4,0, musisz mieć osobne kopie danych dla każdej wersji.

Obciążenie programu Hive może zawierać mieszaninę tabel KWASowych i niekwasowych. Jedną z kluczowych różnic między usługą Hive w usłudze HDInsight 3,6 (Hive 2) i Hive w usłudze HDInsight 4,0 (Hive 3) jest zgodność z KWASem dla tabel. W usłudze HDInsight 3,6 włączenie zgodności z KWASem Hive wymaga dodatkowej konfiguracji, ale w tabelach usługi HDInsight 4,0 są domyślnie zgodne ze standardami. Jedyną akcją wymaganą przed migracją jest uruchomienie poważniejszej kompaktowania w tabeli KWASowej w klastrze 3,6. W widoku programu Hive lub z Z usługi Beeline Uruchom następujące zapytanie:

```sql
alter table myacidtable compact 'major';
```

Ta kompaktowanie jest wymagane, ponieważ tabele KWASów HDInsight 3,6 i HDInsight 4,0 są w inny sposób rozpoznawane pod kątem różnic w środowisku. Kompaktowanie wymusza czystą podstawę, która gwarantuje spójność. Sekcja 4 [dokumentacji migracji programu Hive](https://docs.hortonworks.com/HDPDocuments/Ambari-2.7.3.0/bk_ambari-upgrade-major/content/prepare_hive_for_upgrade.html) zawiera wskazówki dotyczące masowej kompaktowania tabel z kwasem HDInsight 3,6.

Po ukończeniu kroków migracji i kompaktowania magazynu metadanych można migrować rzeczywisty magazyn. Po zakończeniu migracji magazynu Hive magazyn usługi HDInsight 4,0 będzie miał następujące właściwości:

|3,6 |4.0 |
|---|---|
|Tabele zewnętrzne|Tabele zewnętrzne|
|Nietransakcyjne tabele zarządzane|Tabele zewnętrzne|
|Zarządzane tabele transakcyjne|Zarządzane tabele|

Przed wykonaniem migracji może być konieczne dostosowanie właściwości hurtowni. Na przykład, jeśli oczekujesz, że niektóre tabele będą dostępne dla innej firmy (takiej jak klaster usługi HDInsight 3,6), ta tabela musi być zewnętrzna po zakończeniu migracji. W usłudze HDInsight 4,0 wszystkie zarządzane tabele są transakcyjne. W związku z tym tabele zarządzane w usłudze HDInsight 4,0 powinny być dostępne tylko w klastrach usługi HDInsight 4,0.

Po poprawnym ustawieniu właściwości tabeli należy wykonać narzędzie migracji magazynu Hive z jednego z węzłów głównych klastra przy użyciu powłoki SSH:

1. Nawiąż połączenie z klastrem węzła głównego przy użyciu protokołu SSH. Aby uzyskać instrukcje, zobacz [nawiązywanie połączenia z usługą HDInsight przy użyciu protokołu SSH](../hdinsight-hadoop-linux-use-ssh-unix.md)
1. Otwórz powłokę logowania jako użytkownika programu Hive, uruchamiając`sudo su - hive`
1. Określ wersję stosu platformy danych przez wykonanie `ls /usr/hdp`. Spowoduje to wyświetlenie ciągu wersji, którego należy użyć w następnym poleceniu.
1. Wykonaj następujące polecenie w powłoce. Zamień `STACK_VERSION` na ciąg wersji z poprzedniego kroku:

```bash
/usr/hdp/STACK_VERSION/hive/bin/hive --config /etc/hive/conf --service  strictmanagedmigration --hiveconf hive.strict.managed.tables=true -m automatic --modifyManagedTables
```

Po zakończeniu działania narzędzia migracji magazyn Hive będzie gotowy do usługi HDInsight 4,0.

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

Istnieją dwa sposoby wykonywania i debugowania zapytań Hive/LLAP w klastrze usługi HDInsight 3,6. HiveCLI udostępnia środowisko wiersza polecenia, a widok tez/Hive zawiera przepływ pracy oparty na graficznym interfejsie użytkownika.

W usłudze HDInsight 4,0 HiveCLI został zastąpiony Z usługi Beeline. HiveCLI to Thrift Client for Hiveserver 1, a Z usługi Beeline to klient JDBC, który zapewnia dostęp do Hiveserver 2. Z usługi Beeline może również służyć do nawiązywania połączenia z dowolnym innym punktem końcowym bazy danych zgodnym z JDBC. Usługa z usługi Beeline jest dostępna w przypadku usługi HDInsight 4,0 bez konieczności instalacji.

W usłudze HDInsight 3,6 klient z graficznym interfejsem użytkownika służący do współdziałania z serwerem Hive jest widokiem Ambari Hive. Usługa HDInsight 4,0 nie jest dostarczana z widokiem Ambari. Firma Microsoft udostępniła klientom możliwość korzystania z usługi Data Analytics Studio (DAS), która nie jest podstawową usługą HDInsight. DAS nie jest dostarczana z klastrami usługi HDInsight, które są wbudowane i nie jest oficjalnie obsługiwanym pakietem. Program DAS można jednak zainstalować w klastrze za pomocą [akcji skryptu](../hdinsight-hadoop-customize-cluster-linux.md) w następujący sposób:

|Właściwość | Wartość |
|---|---|
|Typ skryptu|-Niestandardowe|
|Nazwa|URZĄDZEŃ|
|Identyfikator URI skryptu bash|`https://hdiconfigactions.blob.core.windows.net/dasinstaller/LaunchDASInstaller.sh`|
|Typy węzłów|Head|

Odczekaj od 10 do 15 minut, a następnie uruchom program Data Analytics Studio `https://CLUSTERNAME.azurehdinsight.net/das/`przy użyciu tego adresu URL:.

Przed uzyskaniem dostępu do obiektu DAS może być wymagane odświeżenie interfejsu użytkownika Ambari i/lub ponowne uruchomienie wszystkich składników programu Ambari.

Jeśli nie widzisz zapytań, które zostały uruchomione w podglądzie zapytań, należy wykonać następujące czynności:

1. Ustaw konfiguracje dla programu Hive, tez i DAS, jak opisano w [tym przewodniku dotyczące rozwiązywania problemów z instalacją Das](https://docs.hortonworks.com/HDPDocuments/DAS/DAS-1.2.0/troubleshooting/content/das_queries_not_appearing.html).
2. Upewnij się, że następujące konfiguracje katalogów usługi Azure Storage są stronicowymi obiektami BLOB i są wyświetlane w obszarze `fs.azure.page.blob.dirs`:
    * `hive.hook.proto.base-directory`
    * `tez.history.logging.proto-base-dir`
3. Uruchom ponownie System HDFS, Hive, tez i DAS na obu węzłów głównychach.

## <a name="next-steps"></a>Następne kroki

* [Anons usługi HDInsight 4,0](../hdinsight-version-release.md)
* [HDInsight 4,0 głębokie szczegółowe](https://azure.microsoft.com/blog/deep-dive-into-azure-hdinsight-4-0/)
* [Tabele KWASów Hive 3](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.1.0/using-hiveql/content/hive_3_internals.html)
