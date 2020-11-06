---
title: Uaktualnianie przy użyciu zrzutów i przywracania-Azure Database for PostgreSQL — pojedynczy serwer
description: Opisuje kilka metod zrzucania i przywracania baz danych w celu przeprowadzenia migracji do nowszej wersji Azure Database for PostgreSQL-jednego serwera.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 11/05/2020
ms.openlocfilehash: 6dfcf0b2ec1d46821007123908a8e7ba8df29744
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2020
ms.locfileid: "93421774"
---
# <a name="upgrade-your-postgresql-database-using-dump-and-restore"></a>Uaktualnianie bazy danych PostgreSQL przy użyciu funkcji zrzutów i przywracania

W Azure Database for PostgreSQL-pojedynczym serwerze zaleca się uaktualnienie aparatu bazy danych PostgreSQL do nowszej wersji głównej przy użyciu jednej z następujących metod:
* Metoda offline korzystająca z PostgreSQL [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) i [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html). W tej metodzie należy najpierw wykonać zrzut z serwera źródłowego, a następnie przywrócić ten zrzut na serwerze docelowym.
* Metoda online korzystająca z [**Database Migration Service**](https://docs.microsoft.com/azure/dms/tutorial-azure-postgresql-to-azure-postgresql-online-portal) (DMS). Ta metoda przechowuje docelową bazę danych w synchronizacji ze źródłem i można wybrać opcję wycinania. Istnieje jednak kilka wymagań wstępnych i ograniczeń, które należy rozwiązać. 

Po wybraniu metod w trybie online i offline w celu przeprowadzania uaktualnień głównych wersji można zastosować poniższe zalecenia.

| **Database** (Baza danych) | **Zrzuć/Przywróć (offline)** | **DMS (online)** |
| ------ | :------: | :-----: |
| Istnieje mała baza danych i może to pozwolić na przestoje w celu uaktualnienia  | X | |
| Małe bazy danych (< 10 GB)  | X | X | 
| Mały średni baz danych (10 GB – 100 GB) | X | X |
| Duże bazy danych (> 100 GB) |  | X |
| Może zapewnić przestoje do uaktualnienia (niezależnie od rozmiaru bazy danych) | X |  |
| Może rozwiązać [wymagania wstępne składnika](https://docs.microsoft.com/azure/dms/tutorial-azure-postgresql-to-azure-postgresql-online-portal#prerequisites) DMS z uwzględnieniem ponownego uruchomienia? |  | X |
| Czy w trakcie procesu uaktualniania można uniknąć DDLs i niezarejestrowanych tabel? | |  X |

Ten przewodnik przedstawia dwie przykładowe metody uaktualniania baz danych przy użyciu pg_dump PostgreSQL i pg_restore poleceń. Proces w tym dokumencie jest nazywany **uaktualnieniem** , chociaż baza danych jest  **migrowana** z serwera źródłowego na serwer docelowy. 

> [!NOTE]
> Zrzuty i przywracanie PostgreSQL można wykonywać na wiele sposobów. Możesz użyć innych metod niż to, co jest określone w tym dokumencie. Na przykład w celu wykonania zrzutu, po którym następuje przywracanie z klienta PostgreSQL, zapoznaj się z [dokumentacją](./howto-migrate-using-dump-and-restore.md) , aby zapoznać się ze szczegółami procedury i najlepszymi rozwiązaniami. Aby uzyskać szczegółowy zrzut i składnię przywracania z dodatkowymi parametrami, zobacz artykuły [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) i [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html). 


## <a name="prerequisites-for-using-dump-and-restore-with-azure-postgresql"></a>Wymagania wstępne dotyczące używania zrzutów i przywracania za pomocą usługi Azure PostgreSQL
 
Aby krokowo uzyskać instrukcje, musisz:
- Źródłowa baza danych z systemem 9,5, 9,6 lub 10 (Azure Database for PostgreSQL — pojedynczy serwer)
- Docelowy serwer baz danych z żądaną wersją główną PostgreSQL [Azure Database for PostgreSQL Server](quickstart-create-server-database-portal.md). 
- System kliencki (Linux) z zainstalowanym programem PostgreSQL i ma zainstalowane [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) i [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html) narzędzia wiersza polecenia. 
- Alternatywnie możesz użyć [Azure Cloud Shell](https://shell.azure.com) lub klikając Azure Cloud Shell na pasku menu w prawym górnym rogu [Azure Portal](https://portal.azure.com). `az login`Przed uruchomieniem poleceń zrzutu i przywracania należy zalogować się na swoje konto.
- Lokalizacja klienta PostgreSQL, taka jak maszyna wirtualna, najlepiej działająca w tym samym regionie co serwery źródłowe i docelowe). 

## <a name="additional-details-and-considerations"></a>Dodatkowe szczegóły i zagadnienia
- Parametry połączenia można znaleźć w źródłowej i docelowej bazie danych, klikając "parametry połączenia" w portalu. 
- Na serwerze może być uruchomiona więcej niż jedna baza danych. Listę baz danych można znaleźć, łącząc się z serwerem źródłowym i uruchamiając `\l` .
- Utwórz odpowiednie bazy danych na docelowym serwerze bazy danych.
- Możesz pominąć uaktualnianie `azure_maintenance` lub bazy danych szablonów.
- Zapoznaj się z powyższymi tabelami, aby określić, że baza danych jest odpowiednia dla tego trybu migracji.
- Jeśli chcesz użyć Azure Cloud Shell, sesja przekroczy limit czasu po 20 minutach. Jeśli rozmiar bazy danych wynosi < 10 GB, można zakończyć uaktualnienie bez przekroczenia limitu czasu. W przeciwnym razie może być konieczne pozostawienie otwartej sesji przy użyciu innych metod, takich jak naciśnięcie <Enter> klawisza jeden raz w 10-15 minut. 

> [!TIP] 
> - Jeśli używasz tego samego hasła dla źródłowej i docelowej bazy danych, możesz ustawić `PGPASSWORD=yourPassword` zmienną środowiskową.  Następnie nie trzeba podawać hasła, za każdym razem polecenia, takie jak PSQL, pg_dump i pg_restore.  Podobnie można skonfigurować dodatkowe zmienne, takie `PGUSER` jak `PGSSLMODE` itp., zobacz [PostgreSQL zmienne środowiskowe](https://www.postgresql.org/docs/11/libpq-envars.html).
>  
> - Jeśli serwer PostgreSQL wymaga połączeń TLS/SSL (domyślnie na serwerach Azure Database for PostgreSQL), Ustaw zmienną środowiskową `PGSSLMODE=require` tak, aby narzędzie pg_restore łączyło się z protokołem TLS. Bez protokołu TLS błąd może zostać odczytany  `FATAL:  SSL connection is required. Please specify SSL options and retry.`
>
> - W wierszu polecenia systemu Windows uruchom polecenie `SET PGSSLMODE=require` przed uruchomieniem pg_restore polecenie. W systemie Linux lub bash Uruchom polecenie `export PGSSLMODE=require` przed uruchomieniem polecenia pg_restore.

## <a name="example-database-used-in-this-guide"></a>Przykładowa baza danych używana w tym przewodniku

 | **Opis** | **Wartość** |
 | ------- | ------- |
 | Serwer źródłowy (v 9.5) | pg-95.postgres.database.azure.com |
 | Źródłowa baza danych | bench5gb |
 | Rozmiar źródłowej bazy danych | 5 GB |
 | Nazwa użytkownika źródłowego | pg@pg-95 |
 | Serwer docelowy (v11) | pg-11.postgres.database.azure.com |
 | Docelowa baza danych | bench5gb |
 | Nazwa użytkownika docelowego | pg@pg-11 |

## <a name="method-1-upgrade-with-streaming-backups-to-the-target"></a>Metoda 1: Uaktualnianie z kopiami zapasowymi przesyłania strumieniowego do elementu docelowego 

W tej metodzie cały zrzut bazy danych jest przesyłany strumieniowo bezpośrednio do docelowego serwera bazy danych i nie przechowuje zrzutu na kliencie. W związku z tym może być używany z klientem z ograniczoną ilością pamięci masowej, a nawet można uruchamiać z poziomu Azure Cloud Shell. 

1. Upewnij się, że baza danych istnieje na serwerze docelowym przy użyciu `\l` polecenia. Jeśli baza danych nie istnieje, utwórz ją.
   ```azurecli-interactive
    psql "host=myTargetServer port=5432 dbname=postgres user=myUser password=###### sslmode=mySSLmode"
    ```
    ```SQL
    postgres> \l   
    postgres> create database myTargetDB;
   ```

2. Uruchom zrzut i Przywróć jako pojedynczy wiersz polecenia przy użyciu potoku. 
    ```azurecli-interactive
    pg_dump -Fc -v --mySourceServer --port=5432 --username=myUser --dbname=mySourceDB | pg_restore -v --no-owner --host=myTargetServer --port=5432 --username=myUser --dbname=myTargetDB
    ```

    Przykład:

    ```azurecli-interactive
    pg_dump -Fc -v --host=pg-95.postgres.database.azure.com --port=5432 --username=pg@pg-95 --dbname=bench5gb | pg_restore -v --no-owner --host=pg-11.postgres.database.azure.com --port=5432 --username=pg@pg-11 --dbname=bench5gb
    ```  
3. Po zakończeniu procesu uaktualniania (migracji) można testować aplikację z serwerem docelowym. 
4. Powtórz ten proces dla wszystkich baz danych na serwerze.

 W poniższej tabeli przedstawiono czas potrzebny na uaktualnienie za pomocą tej metody. Dane są wypełniane przy użyciu [pgbench](https://www.postgresql.org/docs/10/pgbench.html). Ponieważ baza danych może mieć różną liczbę obiektów o różnych rozmiarach niż pgbench wygenerowane tabele i indeksy, zdecydowanie zaleca się przetestowanie zrzutu i przywrócenie bazy danych w celu zrozumienia rzeczywistego czasu potrzebnego na uaktualnienie bazy danych. 

| **Rozmiar bazy danych** | **Przybliżony czas trwania** | 
| ----- | ------ |
| 1 GB  | 1-2 minut |
| 5 GB | 8-10 minut |
| 10 GB | 15-20 minut |
| 50 GB | 1 – 1,5 godz. |
| 100 GB | 2,5-3 godziny|
   
## <a name="method-2-upgrade-with-parallel-dump-and-restore"></a>Metoda 2. Uaktualnianie przy użyciu zrzutu równoległego i przywracanie 

Ta metoda jest przydatna, jeśli masz kilka większych tabel w bazie danych i chcesz zrównoleglanie proces zrzutu i przywracania dla tej bazy danych. Potrzebujesz wystarczającej ilości miejsca na dysku lokalnym, aby obsłużyć zrzuty kopii zapasowych baz danych. Ten równoległy proces zrzutu i przywracania skraca czas wykonywania całej migracji/uaktualnienia. Na przykład baza danych 50 GB pgbench, która wykonała 1-1,5 godziny do migracji, została ukończona w mniej niż 30 minut.

1. Dla każdej bazy danych na serwerze źródłowym Utwórz odpowiednią bazę danych na serwerze docelowym.

   ```bash
    psql "host=myTargetServer port=5432 dbname=postgres user=myuser password=###### sslmode=mySSLmode"
    postgresl> create database myDB;
   ```
   Przykład:
    ```bash
    psql "host=pg-11.postgres.database.azure.com port=5432 dbname=postgres user=pg@pg-11 password=###### sslmode=require"

    postgres> create database bench5gb;
    postgres> \q
    ```

2. Uruchom pg_dump polecenie w formacie katalogu o liczbie zadań = 4 (liczba tabel w bazie danych). W przypadku większych warstw obliczeniowych i z większą liczbą tabel można zwiększyć ją do większej liczby. Ten pg_dump utworzy katalog do przechowywania skompresowanych plików dla każdego zadania.

    ```bash
    pg_dump -Fd -v --host=sourceServer --port=5432 --username=myUser --dbname=mySourceDB -j 4 -f myDumpDirectory
    ```
    Przykład:
    ```bash
    pg_dump -Fd -v --host=pg-95.postgres.database.azure.com --port=5432 --username=pg@pg-95 --dbname=bench5gb -j 4 -f dump.dir
    ```

3. Następnie Przywróć kopię zapasową na serwerze docelowym.
    ```bash
    $ pg_restore -v --no-owner --host=myTargetServer --port=5432 --username=myUser --dbname=myTargetDB -j 4 myDumpDir
    ```
    Przykład:
    ```bash
    $ pg_restore -v --no-owner --host=pg-11.postgres.database.azure.com --port=5432 --username=pg@pg-11 --dbname=bench5gb -j 4 dump.dir
    ```

> [!TIP]
> Proces opisany w tym dokumencie może być również używany do uaktualniania serwera Azure Database for PostgreSQL-elastyczny, który jest w wersji zapoznawczej. Główna różnica polega na tym, że parametry połączenia dla elastycznego obiektu docelowego serwera nie są `@dbName` .  Na przykład, jeśli nazwa użytkownika to `pg` , w ciągu połączenia jest używana nazwa użytkownika pojedynczego serwera, `pg@pg-95` a przy użyciu elastycznego serwera można po prostu użyć `pg` .

## <a name="next-steps"></a>Następne kroki

- Gdy docelowa funkcja bazy danych jest zadowalająca, można usunąć stary serwer bazy danych. 
- Jeśli chcesz użyć tego samego punktu końcowego bazy danych co serwer źródłowy, po usunięciu starego źródłowego serwera bazy danych można utworzyć replikę odczytu ze starą nazwą serwera bazy danych. Po ustanowieniu stałego stanu można zatrzymać replikę, która będzie wspierać serwer repliki jako niezależny serwer. Aby uzyskać więcej informacji, zobacz [replikacja](./concepts-read-replicas.md) .
- Pamiętaj, aby testować i sprawdzać poprawność tych poleceń w środowisku testowym przed użyciem ich w produkcji.
