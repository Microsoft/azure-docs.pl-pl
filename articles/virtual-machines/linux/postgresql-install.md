---
title: Konfigurowanie postgresql na maszynie wirtualnej z systemem Linux
description: Dowiedz się, jak zainstalować i skonfigurować postgresql na maszynie wirtualnej systemu Linux na platformie Azure
author: cynthn
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/01/2016
ms.author: cynthn
ms.openlocfilehash: d86e42dcc16d108cc82c9d245c7919145cef365f
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759335"
---
# <a name="install-and-configure-postgresql-on-azure"></a>Instalowanie i konfigurowanie usługi PostgreSQL na platformie Azure
PostgreSQL to zaawansowana baza danych typu open source podobna do Oracle i DB2. Zawiera funkcje gotowe do pracy w przedsiębiorstwie, takie jak pełna zgodność acid, niezawodne przetwarzanie transakcyjne i kontrola współbieżności wielu wersji. Obsługuje również standardy, takie jak ANSI SQL i SQL/MED (w tym zagraniczne otoki danych dla Oracle, MySQL, MongoDB i wielu innych). Jest wysoce rozszerzalny dzięki obsłudze ponad 12 języków proceduralnych, indeksów GIN i GiST, obsługi danych przestrzennych i wielu funkcji podobnych do NoSQL dla aplikacji JSON lub aplikacji opartych na wartości kluczach.

W tym artykule dowiesz się, jak zainstalować i skonfigurować PostgreSQL na maszynie wirtualnej platformy Azure z systemem Linux.


## <a name="install-postgresql"></a>Instalowanie postgresql
> [!NOTE]
> Aby ukończyć ten samouczek, musisz już mieć maszynę wirtualną platformy Azure z systemem Linux. Aby utworzyć i skonfigurować maszynę wirtualną z systemem Linux przed kontynuowaniem, zobacz [samouczek maszyny wirtualnej z systemem Azure Linux](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
> 
> 

W takim przypadku należy użyć portu 1999 jako portu PostgreSQL.  

Połącz się z maszyną wirtualną z systemem Linux utworzoną przez PuTTY. Jeśli jest to pierwszy raz, gdy używasz maszyny Wirtualnej systemu Azure z systemem Linux, zobacz [Jak używać SSH z systemem Linux na platformie Azure,](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) aby dowiedzieć się, jak używać PuTTY do łączenia się z maszyną wirtualną z systemem Linux.

1. Uruchom następujące polecenie, aby przełączyć się do katalogu głównego (administrator):
   
        # sudo su -
2. Niektóre dystrybucje mają zależności, które należy zainstalować przed zainstalowaniem PostgreSQL. Sprawdź swoją dystrybucję na tej liście i uruchom odpowiednie polecenie:
   
   * Red Hat podstawy Linux:
     
           # yum install readline-devel gcc make zlib-devel openssl openssl-devel libxml2-devel pam-devel pam  libxslt-devel tcl-devel python-devel -y  
   * Baza Debiana Linux:
     
            # apt-get install readline-devel gcc make zlib-devel openssl openssl-devel libxml2-devel pam-devel pam libxslt-devel tcl-devel python-devel -y  
   * SUSE Linux:
     
           # zypper install readline-devel gcc make zlib-devel openssl openssl-devel libxml2-devel pam-devel pam  libxslt-devel tcl-devel python-devel -y  
3. Pobierz PostgreSQL do katalogu głównego, a następnie rozpaj pakiet:
   
        # wget https://ftp.postgresql.org/pub/source/v9.3.5/postgresql-9.3.5.tar.bz2 -P /root/
   
        # tar jxvf  postgresql-9.3.5.tar.bz2
   
    Powyższy jest przykładem. Bardziej szczegółowy adres do pobrania można znaleźć w [indeksie /pub/source/](https://ftp.postgresql.org/pub/source/).
4. Aby rozpocząć kompilację, uruchom następujące polecenia:
   
        # cd postgresql-9.3.5
   
        # ./configure --prefix=/opt/postgresql-9.3.5
5. Jeśli chcesz zbudować wszystko, co można zbudować, w tym dokumentację`contrib`(strony HTML i man) i dodatkowe moduły ( ), zamiast tego uruchom następujące polecenie:
   
        # gmake install-world
   
    Powinieneś otrzymać następujący komunikat potwierdzający:
   
        PostgreSQL, contrib, and documentation successfully made. Ready to install.

## <a name="configure-postgresql"></a>Konfigurowanie postgresql
1. (Opcjonalnie) Utwórz dowiązanie symboliczne, aby skrócić odwołanie PostgreSQL, aby nie zawierać numeru wersji:
   
        # ln -s /opt/postgresql-9.3.5 /opt/pgsql
2. Tworzenie katalogu bazy danych:
   
        # mkdir -p /opt/pgsql_data
3. Utwórz użytkownika niebędącego użytkownikiem głównym i zmodyfikuj jego profil. Następnie przełącz się do tego nowego użytkownika (o nazwie *postgres* w naszym przykładzie):
   
        # useradd postgres
   
        # chown -R postgres.postgres /opt/pgsql_data
   
        # su - postgres
   
   > [!NOTE]
   > Ze względów bezpieczeństwa PostgreSQL używa użytkownika niebędącego rootem do inicjowania, uruchamiania lub zamykania bazy danych.
   > 
   > 
4. Edytuj plik *bash_profile,* wprowadzając poniższe polecenia. Te wiersze zostaną dodane na końcu pliku *bash_profile:*
   
        cat >> ~/.bash_profile <<EOF
        export PGPORT=1999
        export PGDATA=/opt/pgsql_data
        export LANG=en_US.utf8
        export PGHOME=/opt/pgsql
        export PATH=\$PATH:\$PGHOME/bin
        export MANPATH=\$MANPATH:\$PGHOME/share/man
        export DATA=`date +"%Y%m%d%H%M"`
        export PGUSER=postgres
        alias rm='rm -i'
        alias ll='ls -lh'
        EOF
5. Wykonaj plik *bash_profile:*
   
        $ source .bash_profile
6. Sprawdź poprawność instalacji za pomocą następującego polecenia:
   
        $ which psql
   
    Jeśli instalacja zakończy się pomyślnie, zostanie wyświetlna następująca odpowiedź:
   
        /opt/pgsql/bin/psql
7. Można również sprawdzić wersję PostgreSQL:
   
        $ psql -V

8. Inicjowanie bazy danych:
   
        $ initdb -D $PGDATA -E UTF8 --locale=C -U postgres -W
   
    Należy otrzymać następujące dane wyjściowe:

![image](./media/postgresql-install/no1.png)

## <a name="set-up-postgresql"></a>Konfigurowanie postgreSql
<!--    [postgres@ test ~]$ exit -->

Uruchom następujące polecenia:

    # cd /root/postgresql-9.3.5/contrib/start-scripts

    # cp linux /etc/init.d/postgresql

Zmodyfikuj dwie zmienne w pliku /etc/init.d/postgresql. Prefiks jest ustawiony na ścieżkę instalacji PostgreSQL: **/opt/pgsql**. PGDATA jest ustawiona na ścieżkę przechowywania danych PostgreSQL: **/opt/pgsql_data**.

    # sed -i '32s#usr/local#opt#' /etc/init.d/postgresql

    # sed -i '35s#usr/local/pgsql/data#opt/pgsql_data#' /etc/init.d/postgresql

![image](./media/postgresql-install/no2.png)

Zmień plik, aby był wykonywalny:

    # chmod +x /etc/init.d/postgresql

Rozpocznij postgresql:

    # /etc/init.d/postgresql start

Sprawdź, czy punkt końcowy PostgreSQL jest włączony:

    # netstat -tunlp|grep 1999

Powinny zostać wyświetlone następujące dane wyjściowe:

![image](./media/postgresql-install/no3.png)

## <a name="connect-to-the-postgres-database"></a>Łączenie się z bazą danych Postgres
Przełącz się ponownie do użytkownika postgres:

    # su - postgres

Tworzenie bazy danych postgres:

    $ createdb events

Połącz się z właśnie utworzoną bazą danych zdarzeń:

    $ psql -d events

## <a name="create-and-delete-a-postgres-table"></a>Tworzenie i usuwanie tabeli postgres
Teraz, po połączeniu z bazą danych, można utworzyć tabele w nim.

Na przykład utwórz nową przykładową tabelę Postgres za pomocą następującego polecenia:

    CREATE TABLE potluck (name VARCHAR(20),    food VARCHAR(30),    confirmed CHAR(1), signup_date DATE);

Teraz skonfigurowana jest tabela czterokolumnowa z następującymi nazwami kolumn i ograniczeniami:

1. Kolumna "nazwa" została ograniczona przez polecenie VARCHAR do długości poniżej 20 znaków.
2. Kolumna "jedzenie" wskazuje produkt spożywczy, który każda osoba przyniesie. Varchar ogranicza ten tekst do poniżej 30 znaków.
3. Kolumna "potwierdzone" rejestruje, czy dana osoba ma RSVP'd do potluck. Dopuszczalne wartości to "Y" i "N".
4. Kolumna "data" pokazuje, kiedy zarejestrowali się na wydarzenie. Postgres wymaga, aby daty były zapisywane jako yyyy-mm-dd.

Jeśli tabela została pomyślnie utworzona, powinny zostać wyświetlone następujące czynności:

![image](./media/postgresql-install/no4.png)

Strukturę tabeli można również sprawdzić za pomocą następującego polecenia:

![image](./media/postgresql-install/no5.png)

### <a name="add-data-to-a-table"></a>Dodawanie danych do tabeli
Najpierw wstaw informacje do wiersza:

    INSERT INTO potluck (name, food, confirmed, signup_date) VALUES('John', 'Casserole', 'Y', '2012-04-11');

Powinny być widoczne następujące dane wyjściowe:

![image](./media/postgresql-install/no6.png)

Możesz dodać jeszcze kilka osób do stołu. Oto kilka opcji lub możesz utworzyć własne:

    INSERT INTO potluck (name, food, confirmed, signup_date) VALUES('Sandy', 'Key Lime Tarts', 'N', '2012-04-14');

    INSERT INTO potluck (name, food, confirmed, signup_date) VALUES ('Tom', 'BBQ','Y', '2012-04-18');

    INSERT INTO potluck (name, food, confirmed, signup_date) VALUES('Tina', 'Salad', 'Y', '2012-04-18');

### <a name="show-tables"></a>Pokaż tabele
Użyj następującego polecenia, aby wyświetlić tabelę:

    select * from potluck;

Dane wyjściowe wyglądają następująco:

![image](./media/postgresql-install/no7.png)

### <a name="delete-data-in-a-table"></a>Usuwanie danych w tabeli
Użyj następującego polecenia, aby usunąć dane z tabeli:

    delete from potluck where name=’John’;

Spowoduje to usunięcie wszystkich informacji w wierszu "Jan". Dane wyjściowe wyglądają następująco:

![image](./media/postgresql-install/no8.png)

### <a name="update-data-in-a-table"></a>Aktualizowanie danych w tabeli
Użyj następującego polecenia, aby zaktualizować dane w tabeli. Dla tego, Sandy potwierdził, że uczestniczą, więc zmienimy RSVP z "N" na "Y":

     UPDATE potluck set confirmed = 'Y' WHERE name = 'Sandy';


## <a name="get-more-information-about-postgresql"></a>Uzyskaj więcej informacji o PostgreSQL
Po zakończeniu instalacji postgreSQL na maszynie wirtualnej systemu Azure z systemem Linux możesz cieszyć się używaniem jej na platformie Azure. Aby dowiedzieć się więcej o PostgreSQL, odwiedź [witrynę PostgreSQL](https://www.postgresql.org/).

