---
title: Uaktualnianie przy użyciu zrzutów i przywracania-Azure Database for PostgreSQL — pojedynczy serwer
description: Opisuje metody uaktualniania w trybie offline za pomocą zrzutów i przywracania baz danych do migracji do nowszej wersji Azure Database for PostgreSQL-jednego serwera.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 11/10/2020
ms.openlocfilehash: e756e033c8e5b2508dca9bde76ad16be26a940fa
ms.sourcegitcommit: 4bee52a3601b226cfc4e6eac71c1cb3b4b0eafe2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/11/2020
ms.locfileid: "94505788"
---
# <a name="upgrade-your-postgresql-database-using-dump-and-restore"></a>Uaktualnianie bazy danych PostgreSQL przy użyciu funkcji zrzutów i przywracania

Serwer PostgreSQL wdrożony w ramach jednego serwera Azure Database for PostgreSQL programu można uaktualnić, przechodząc do serwera o wyższej wersji głównej, korzystając z następujących metod.
* Metoda **offline** korzystająca z PostgreSQL [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) i [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html) , która powoduje przestoje w przypadku migrowania danych. Ten dokument dotyczy tej metody uaktualniania/migracji.
* Metoda **online** korzystająca z [Database Migration Service](https://docs.microsoft.com/azure/dms/tutorial-azure-postgresql-to-azure-postgresql-online-portal) (DMS). Ta metoda zapewnia ograniczoną migrację przestojów i utrzymuje docelową bazę danych w synchronizacji ze źródłem i można wybrać opcję wycinania. Istnieje jednak kilka wymagań wstępnych i ograniczeń związanych z korzystaniem z usługi DMS. Aby uzyskać szczegółowe informacje, zobacz [dokumentację usługi DMS](https://docs.microsoft.com/azure/dms/tutorial-azure-postgresql-to-azure-postgresql-online-portal). 

 W poniższej tabeli przedstawiono niektóre zalecenia na podstawie rozmiarów i scenariuszy baz danych.

| **Baza danych/scenariusz** | **Zrzuć/Przywróć (offline)** | **DMS (online)** |
| ------ | :------: | :-----: |
| Istnieje mała baza danych i może to pozwolić na przestoje w celu uaktualnienia  | X | |
| Małe bazy danych (< 10 GB)  | X | X | 
| Mały średni baz danych (10 GB – 100 GB) | X | X |
| Duże bazy danych (> 100 GB) |  | X |
| Może zapewnić przestoje do uaktualnienia (niezależnie od rozmiaru bazy danych) | X |  |
| Może rozwiązać [wymagania wstępne systemu](https://docs.microsoft.com/azure/dms/tutorial-azure-postgresql-to-azure-postgresql-online-portal#prerequisites)DMS, w tym ponowny rozruch? |  | X |
| Czy w trakcie procesu uaktualniania można uniknąć DDLs i niezarejestrowanych tabel? | |  X |

Ten przewodnik zawiera kilka metod migracji w trybie offline i przykłady przedstawiające sposób migracji z serwera źródłowego na serwer docelowy, na którym działa nowsza wersja PostgreSQL.

> [!NOTE]
> Zrzuty i przywracanie PostgreSQL można wykonywać na wiele sposobów. Możesz zdecydować się na migrację przy użyciu jednej z metod opisanych w tym przewodniku lub wybrać alternatywne sposoby odpowiadające Twoim potrzebom. Aby uzyskać szczegółowy zrzut i składnię przywracania z dodatkowymi parametrami, zobacz artykuły [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) i [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html). 


## <a name="prerequisites-for-using-dump-and-restore-with-azure-database-for-postgresql"></a>Wymagania wstępne dotyczące używania zrzutów i przywracania za pomocą Azure Database for PostgreSQL
 
Aby krokowo uzyskać instrukcje, musisz:

- **Źródłowa** baza danych PostgreSQL z systemem 9,5, 9,6 lub 10, która ma zostać uaktualniona
- **Docelowy** serwer bazy danych PostgreSQL z żądaną wersją główną [Azure Database for PostgreSQL Server](quickstart-create-server-database-portal.md). 
- System klienta PostgreSQL do uruchamiania poleceń zrzutu i przywracania.
  - Może to być klient z systemem Linux lub Windows z zainstalowanym programem PostgreSQL i ma [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) i [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html) zainstalowanych narzędzi wiersza polecenia. 
  - Alternatywnie możesz użyć [Azure Cloud Shell](https://shell.azure.com) lub klikając Azure Cloud Shell na pasku menu w prawym górnym rogu [Azure Portal](https://portal.azure.com). `az login`Przed uruchomieniem poleceń zrzutu i przywracania należy zalogować się na swoje konto.
- Klient PostgreSQL najlepiej działa w tym samym regionie co serwery źródłowe i docelowe. 


## <a name="additional-details-and-considerations"></a>Dodatkowe szczegóły i zagadnienia
- Parametry połączenia można znaleźć w źródłowej i docelowej bazie danych, klikając "parametry połączenia" w portalu. 
- Na serwerze może być uruchomiona więcej niż jedna baza danych. Listę baz danych można znaleźć, łącząc się z serwerem źródłowym i uruchamiając `\l` .
- Utwórz odpowiednie bazy danych na docelowym serwerze bazy danych.
- Możesz pominąć uaktualnianie `azure_maintenance` lub bazy danych szablonów.
- Zapoznaj się z powyższymi tabelami, aby określić, że baza danych jest odpowiednia dla tego trybu migracji.
- Jeśli chcesz użyć Azure Cloud Shell, pamiętaj, że sesja przekroczy limit czasu po 20 minutach. Jeśli rozmiar bazy danych wynosi < 10 GB, może być możliwe przeprowadzenie uaktualnienia bez limitu czasu sesji. W przeciwnym razie może być konieczne pozostawienie otwartej sesji przy użyciu innych metod, takich jak naciśnięcie <Enter> klawisza jeden raz w 10-15 minut. 


## <a name="example-database-used-in-this-guide"></a>Przykładowa baza danych używana w tym przewodniku

W tym przewodniku następujące serwery źródłowe i docelowe oraz nazwy baz danych są używane do zilustrowania przykładów.

 | **Opis** | **Wartość** |
 | ------- | ------- |
 | Serwer źródłowy (v 9.5) | pg-95.postgres.database.azure.com |
 | Źródłowa baza danych | bench5gb |
 | Rozmiar źródłowej bazy danych | 5 GB |
 | Nazwa użytkownika źródłowego | pg@pg-95 |
 | Serwer docelowy (v11) | pg-11.postgres.database.azure.com |
 | Docelowa baza danych | bench5gb |
 | Nazwa użytkownika docelowego | pg@pg-11 |

## <a name="upgrade-your-databases-using-offline-migration-methods"></a>Uaktualnianie baz danych przy użyciu metod migracji w trybie offline
Możesz użyć jednej z metod opisanych w tej sekcji w przypadku uaktualnień. Podczas wykonywania zadań można użyć poniższych porad.

- Jeśli używasz tego samego hasła dla źródłowej i docelowej bazy danych, możesz ustawić `PGPASSWORD=yourPassword` zmienną środowiskową.  Następnie nie musisz podawać hasła za każdym razem, gdy uruchamiasz polecenia takie jak PSQL, pg_dump i pg_restore.  Podobnie można skonfigurować dodatkowe zmienne, takie `PGUSER` jak `PGSSLMODE` itp., zobacz [PostgreSQL zmienne środowiskowe](https://www.postgresql.org/docs/11/libpq-envars.html).
  
- Jeśli serwer PostgreSQL wymaga połączeń TLS/SSL (domyślnie na serwerach Azure Database for PostgreSQL), Ustaw zmienną środowiskową `PGSSLMODE=require` tak, aby narzędzie pg_restore łączyło się z protokołem TLS. Bez protokołu TLS błąd może zostać odczytany  `FATAL:  SSL connection is required. Please specify SSL options and retry.`

- W wierszu polecenia systemu Windows uruchom polecenie `SET PGSSLMODE=require` przed uruchomieniem pg_restore polecenie. W systemie Linux lub bash Uruchom polecenie `export PGSSLMODE=require` przed uruchomieniem polecenia pg_restore.

### <a name="method-1-migrate-using-dump-file"></a>Metoda 1. Migrowanie przy użyciu pliku zrzutu

Ta metoda obejmuje dwa kroki. Najpierw należy utworzyć zrzut z serwera źródłowego. Drugim krokiem jest przywrócenie pliku zrzutu na serwer docelowy. Więcej szczegółów można znaleźć w dokumentacji [Migrowanie przy użyciu zrzutów i przywracania](howto-migrate-using-dump-and-restore.md) . Jest to zalecana metoda w przypadku dużych baz danych, a system klienta ma wystarczającą ilość miejsca do przechowywania pliku zrzutu.

### <a name="method-2-migrate-using-streaming-the-dump-data-to-the-target-database"></a>Metoda 2. Migrowanie przy użyciu przesyłania strumieniowego danych zrzutu do docelowej bazy danych

Jeśli nie masz klienta PostgreSQL lub chcesz użyć Azure Cloud Shell, możesz użyć tej metody. Zrzut bazy danych jest przesyłany strumieniowo bezpośrednio do docelowego serwera bazy danych i nie przechowuje zrzutu na kliencie. W związku z tym może być używany z klientem z ograniczoną ilością pamięci masowej, a nawet można uruchamiać z poziomu Azure Cloud Shell. 

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

 Przykładowo w poniższej tabeli przedstawiono czas trwania migracji przy użyciu metody zrzutu strumieniowego. Przykładowe dane są wypełniane przy użyciu [pgbench](https://www.postgresql.org/docs/10/pgbench.html). Ponieważ baza danych może mieć różną liczbę obiektów o różnych rozmiarach niż pgbench wygenerowane tabele i indeksy, zdecydowanie zaleca się przetestowanie zrzutu i przywrócenie bazy danych w celu zrozumienia rzeczywistego czasu potrzebnego na uaktualnienie bazy danych. 

| **Rozmiar bazy danych** | **Przybliżony czas trwania** | 
| ----- | ------ |
| 1 GB  | 1-2 minut |
| 5 GB | 8-10 minut |
| 10 GB | 15-20 minut |
| 50 GB | 1 – 1,5 godz. |
| 100 GB | 2,5-3 godziny|
   
### <a name="method-3-migrate-using-parallel-dump-and-restore"></a>Metoda 3. Migrowanie przy użyciu zrzutu równoległego i przywracanie 

Tę metodę można wziąć pod uwagę, jeśli w bazie danych masz kilka większych tabel i chcesz zrównoleglanie proces zrzutu i przywracania dla tej bazy danych. Potrzebujesz również wystarczającej ilości miejsca w systemie klienta, aby uwzględnić zrzuty kopii zapasowych. Ten równoległy proces zrzutu i przywracania skraca czas wykonywania całej migracji. Na przykład baza danych 50 GB pgbench, która wykonała 1-1,5 godziny do migracji, została ukończona przy użyciu metody 1 i 2 trwało mniej niż 30 minut przy użyciu tej metody.

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

- Po spełnieniu funkcji docelowej bazy danych można usunąć stary serwer bazy danych. 
- Jeśli chcesz użyć tego samego punktu końcowego bazy danych co serwer źródłowy, po usunięciu starego źródłowego serwera bazy danych można utworzyć replikę odczytu ze starą nazwą serwera bazy danych. Po ustanowieniu stałego stanu można zatrzymać replikę, która będzie wspierać serwer repliki jako niezależny serwer. Aby uzyskać więcej informacji, zobacz [replikacja](./concepts-read-replicas.md) .
- Pamiętaj, aby testować i sprawdzać poprawność tych poleceń w środowisku testowym przed użyciem ich w produkcji.
