---
title: Konfigurowanie PostgreSQL na maszynie wirtualnej z systemem Linux
description: Dowiedz się, jak zainstalować i skonfigurować PostgreSQL na maszynie wirtualnej z systemem Linux na platformie Azure
author: cynthn
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: how-to
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/01/2016
ms.author: cynthn
ms.openlocfilehash: fdde7613627c9fec0694f3985f78cf10e52f59c2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91397100"
---
# <a name="install-and-configure-postgresql-on-azure"></a>Instalowanie i Konfigurowanie PostgreSQL na platformie Azure
PostgreSQL to zaawansowana baza danych open source podobna do Oracle i DB2. Obejmuje ona funkcje gotowe dla przedsiębiorstwa, takie jak Pełna zgodność ze standardami KWASowymi, niezawodne przetwarzanie transakcyjne i wielostronicowa kontrola współbieżności. Obsługuje ona również standardy, takie jak ANSI SQL i SQL/MED (w tym obce otoki danych dla systemów Oracle, MySQL, MongoDB i wielu innych). Jest wysoce rozszerzalny z obsługą ponad 12 języków proceduralnych, indeksów ĄTEK i dzienników, obsługi danych przestrzennych i wielu funkcji podobnej do NoSQL dla aplikacji opartych na kluczach JSON lub wartościowych.

Ten artykuł zawiera informacje na temat instalowania i konfigurowania PostgreSQL na maszynie wirtualnej platformy Azure z systemem Linux.


## <a name="install-postgresql"></a>Zainstaluj PostgreSQL
> [!NOTE]
> Aby ukończyć ten samouczek, musisz mieć już maszynę wirtualną platformy Azure z systemem Linux. Aby utworzyć i skonfigurować maszynę wirtualną z systemem Linux przed kontynuowaniem, zobacz [Samouczek dotyczący maszyny wirtualnej z systemem Linux](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
> 
> 

W takim przypadku należy użyć portu 1999 jako portu PostgreSQL.  

Nawiąż połączenie z maszyną wirtualną z systemem Linux utworzoną za pomocą wykorzystanych funkcji. Jeśli korzystasz z maszyny wirtualnej z systemem Linux na platformie Azure po raz pierwszy, zobacz [jak używać protokołu SSH z systemem Linux na platformie Azure](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) , aby dowiedzieć się, jak korzystać z funkcji tworzenia połączenia z maszyną wirtualną z systemem Linux.

1. Uruchom następujące polecenie, aby przełączyć się do katalogu głównego (administratora):

    ```console
    # sudo su -
    ```

2. Niektóre dystrybucje mają zależności, które należy zainstalować przed zainstalowaniem PostgreSQL. Sprawdź dystrybucji na tej liście i uruchom odpowiednie polecenie:
   
   * Red Hat Base Linux:

        ```console
        # yum install readline-devel gcc make zlib-devel openssl openssl-devel libxml2-devel pam-devel pam  libxslt-devel tcl-devel python-devel -y
        ```

   * Debian podstawowy system Linux:

        ```console
        # apt-get install readline-devel gcc make zlib-devel openssl openssl-devel libxml2-devel pam-devel pam libxslt-devel tcl-devel python-devel -y
        ```

   * SUSE Linux:

        ```console
        # zypper install readline-devel gcc make zlib-devel openssl openssl-devel libxml2-devel pam-devel pam  libxslt-devel tcl-devel python-devel -y
        ```

3. Pobierz PostgreSQL do katalogu głównego, a następnie Rozpakuj pakiet:

    ```console
    # wget https://ftp.postgresql.org/pub/source/v9.3.5/postgresql-9.3.5.tar.bz2 -P /root/

    # tar jxvf  postgresql-9.3.5.tar.bz2
    ```

    Powyższy kod jest przykładem. Bardziej szczegółowy adres do pobrania można znaleźć w [indeksie/pub/Source/](https://ftp.postgresql.org/pub/source/).

4. Aby rozpocząć kompilację, uruchom następujące polecenia:

    ```console
    # cd postgresql-9.3.5

    # ./configure --prefix=/opt/postgresql-9.3.5
    ```

5. Jeśli chcesz skompilować wszystko, co można skompilować, łącznie z dokumentacją (strony HTML i Man) i dodatkowymi modułami ( `contrib` ), zamiast tego Uruchom następujące polecenie:

    ```console
    # gmake install-world
    ```

    Powinien zostać wyświetlony następujący komunikat potwierdzający:

    ```output
    PostgreSQL, contrib, and documentation successfully made. Ready to install.
    ```

## <a name="configure-postgresql"></a>Konfigurowanie PostgreSQL
1. Obowiązkowe Utwórz link symboliczny, aby skrócić odwołanie PostgreSQL do nieuwzględnienia numeru wersji:

    ```console
    # ln -s /opt/postgresql-9.3.5 /opt/pgsql
    ```

2. Utwórz katalog dla bazy danych:

    ```console
    # mkdir -p /opt/pgsql_data
    ```

3. Utwórz użytkownika innego niż root i zmodyfikuj profil tego użytkownika. Następnie przejdź do nowego użytkownika (o nazwie *Postgres* w naszym przykładzie):

    ```console
    # useradd postgres
   
    # chown -R postgres.postgres /opt/pgsql_data
   
    # su - postgres
    ```
   
   > [!NOTE]
   > Ze względów bezpieczeństwa PostgreSQL używa użytkownika niebędącego elementem głównym do inicjowania, uruchamiania lub wyłączania bazy danych.
   > 
   > 
4. Edytuj plik *bash_profile* , wprowadzając poniższe polecenia. Te wiersze zostaną dodane na końcu pliku *bash_profile* :

    ```config
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
    ```

5. Wykonaj plik *bash_profile* :

    ```console
    $ source .bash_profile
    ```

6. Sprawdź poprawność instalacji przy użyciu następującego polecenia:

    ```console
    $ which psql
    ```

    Jeśli instalacja zakończy się pomyślnie, zostanie wyświetlona następująca odpowiedź:

    ```output
    /opt/pgsql/bin/psql
    ```

7. Możesz również sprawdzić wersję PostgreSQL:

    ```sql
    $ psql -V
    ```

8. Zainicjuj bazę danych:

    ```console
    $ initdb -D $PGDATA -E UTF8 --locale=C -U postgres -W
    ```

    Powinny pojawić się następujące dane wyjściowe:

![Zrzut ekranu pokazujący dane wyjściowe po zainicjowaniu bazy danych.](./media/postgresql-install/no1.png)

## <a name="set-up-postgresql"></a>Konfigurowanie PostgreSQL
<!--    [postgres@ test ~]$ exit -->

Uruchom następujące polecenia:

```console
# cd /root/postgresql-9.3.5/contrib/start-scripts

# cp linux /etc/init.d/postgresql
```

Zmodyfikuj dwie zmienne w pliku/etc/init.d/PostgreSQL. Prefiks jest ustawiany na ścieżkę instalacji PostgreSQL: **/opt/pgsql**. PGDATA jest ustawiona na ścieżkę magazynu danych PostgreSQL: **/opt/pgsql_data**.

```config
# sed -i '32s#usr/local#opt#' /etc/init.d/postgresql

# sed -i '35s#usr/local/pgsql/data#opt/pgsql_data#' /etc/init.d/postgresql
```

![Zrzut ekranu pokazujący prefiks instalacji i katalog danych.](./media/postgresql-install/no2.png)

Zmień plik w taki sposób, aby był on wykonywalny:

```console
# chmod +x /etc/init.d/postgresql
```

Rozpocznij PostgreSQL:

```console
# /etc/init.d/postgresql start
```

Sprawdź, czy punkt końcowy elementu PostgreSQL jest włączony:

```console
# netstat -tunlp|grep 1999
```

Powinny zostać wyświetlone następujące dane wyjściowe:

![Zrzut ekranu pokazujący punkt końcowy PostgreSQL jest włączony.](./media/postgresql-install/no3.png)

## <a name="connect-to-the-postgres-database"></a>Łączenie z bazą danych Postgres
Ponownie Przełącz do Postgres użytkownika:

```console
# su - postgres
```

Utwórz bazę danych Postgres:

```console
$ createdb events
```

Połącz się z utworzoną bazą danych zdarzeń:

```console
$ psql -d events
```

## <a name="create-and-delete-a-postgres-table"></a>Tworzenie i usuwanie tabeli Postgres
Teraz, gdy masz połączenie z bazą danych, możesz utworzyć w niej tabele.

Na przykład utwórz nową przykładową tabelę Postgres za pomocą następującego polecenia:

```sql
CREATE TABLE potluck (name VARCHAR(20),    food VARCHAR(30),    confirmed CHAR(1), signup_date DATE);
```

Teraz można skonfigurować tabelę z czterema kolumnami z następującymi nazwami kolumn i ograniczeniami:

1. Wartość kolumny "name" została ograniczona przez polecenie VARCHAR, która nie może mieć więcej niż 20 znaków.
2. Kolumna "żywność" wskazuje element żywności, który zostanie przeniesieny przez każdą osobę. VARCHAR ogranicza ten tekst do mniej niż 30 znaków.
3. Kolumna "potwierdzone" rejestruje, czy osoba ma RSVP do zatwierdzenia. Dopuszczalne wartości to "Y" i "N".
4. Kolumna "date" jest wyświetlana po zarejestrowaniu się na potrzeby zdarzenia. Postgres wymaga, aby daty były zapisywane jako rrrr-mm-dd.

Jeśli tabela została pomyślnie utworzona, powinny zostać wyświetlone następujące elementy:

![Zrzut ekranu pokazujący komunikat wyświetlany po pomyślnym utworzeniu tabeli.](./media/postgresql-install/no4.png)

Możesz również sprawdzić strukturę tabeli przy użyciu następującego polecenia:

![Zrzut ekranu pokazujący polecenie sprawdzania struktury tabeli.](./media/postgresql-install/no5.png)

### <a name="add-data-to-a-table"></a>Dodawanie danych do tabeli
Najpierw Wstaw informacje do wiersza:

```sql
INSERT INTO potluck (name, food, confirmed, signup_date) VALUES('John', 'Casserole', 'Y', '2012-04-11');
```

Powinny być widoczne następujące dane wyjściowe:

![Zrzut ekranu pokazujący dodane informacje o wierszu.](./media/postgresql-install/no6.png)

Możesz również dodać kilka więcej osób do tabeli. Poniżej przedstawiono niektóre opcje lub można utworzyć własne:

```sql
INSERT INTO potluck (name, food, confirmed, signup_date) VALUES('Sandy', 'Key Lime Tarts', 'N', '2012-04-14');

INSERT INTO potluck (name, food, confirmed, signup_date) VALUES ('Tom', 'BBQ','Y', '2012-04-18');

INSERT INTO potluck (name, food, confirmed, signup_date) VALUES('Tina', 'Salad', 'Y', '2012-04-18');
```

### <a name="show-tables"></a>Pokaż tabele
Użyj następującego polecenia, aby wyświetlić tabelę:

```sql
select * from potluck;
```

Dane wyjściowe wyglądają następująco:

![Zrzut ekranu pokazujący dane wyjściowe polecenia służącego do wyświetlania tabeli.](./media/postgresql-install/no7.png)

### <a name="delete-data-in-a-table"></a>Usuwanie danych w tabeli
Użyj następującego polecenia, aby usunąć dane w tabeli:

```sql
delete from potluck where name=’John’;
```

Spowoduje to usunięcie wszystkich informacji w wierszu "Jan". Dane wyjściowe wyglądają następująco:

![image (obraz)](./media/postgresql-install/no8.png)

### <a name="update-data-in-a-table"></a>Aktualizowanie danych w tabeli
Użyj następującego polecenia, aby zaktualizować dane w tabeli. W takim przypadku Piaskowobrązowy potwierdził, że uczestniczą, więc zmienimy wartość RSVP z "N" na "Y":

```sql
UPDATE potluck set confirmed = 'Y' WHERE name = 'Sandy';
```

## <a name="get-more-information-about-postgresql"></a>Uzyskaj więcej informacji na temat PostgreSQL
Po zakończeniu instalacji programu PostgreSQL na maszynie wirtualnej z systemem Linux na platformie Azure możesz korzystać z niego na platformie Azure. Aby dowiedzieć się więcej na temat PostgreSQL, odwiedź [witrynę sieci Web PostgreSQL](https://www.postgresql.org/).
