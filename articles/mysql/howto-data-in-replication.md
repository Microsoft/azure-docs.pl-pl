---
title: Konfigurowanie replikacji danych — Azure Database for MySQL
description: W tym artykule opisano sposób konfigurowania replikacja typu data-in Azure Database for MySQL.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 01/13/2021
ms.openlocfilehash: 22974a47a6b1e9d49e5055a85f46286497cfe149
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/16/2021
ms.locfileid: "98250536"
---
# <a name="how-to-configure-azure-database-for-mysql-data-in-replication"></a>Jak skonfigurować replikacja typu data-in Azure Database for MySQL

W tym artykule opisano sposób konfigurowania [replikacja typu Data-in](concepts-data-in-replication.md) w Azure Database for MySQL przez skonfigurowanie serwerów źródłowych i replik. W tym artykule założono, że masz pewne doświadczenie w korzystaniu z serwerów i baz danych MySQL.

> [!NOTE]
> Ten artykuł zawiera odwołania do warunku _podrzędnego_, termin, który nie jest już wykorzystywany przez firmę Microsoft. Gdy termin zostanie usunięty z oprogramowania, usuniemy go z tego artykułu.
>

Aby utworzyć replikę w usłudze Azure Database for MySQL, [replikacja typu Data-in](concepts-data-in-replication.md)  synchronizuje dane ze źródłowego serwera MySQL lokalnie, na maszynach wirtualnych lub w usługach bazy danych w chmurze. Replikacja typu data-in jest wykonywana za pomocą technologii replikacji opartej na pozycji w pliku dziennika binarnego (binlog) natywnej dla programu MySQL. Aby dowiedzieć się więcej na temat replikacji binlog, zobacz [Omówienie replikacji MySQL binlog](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html).

Przed wykonaniem kroków opisanych w tym artykule Przejrzyj [ograniczenia i wymagania](concepts-data-in-replication.md#limitations-and-considerations) replikacji danych.

## <a name="create-a-mysql-server-to-be-used-as-replica"></a>Utwórz serwer MySQL, który ma być używany jako replika

1. Utwórz nowy serwer Azure Database for MySQL

   Utwórz nowy serwer MySQL (np. "replica.mysql.database.azure.com"). Zapoznaj się z tematem [Tworzenie serwera Azure Database for MySQL przy użyciu Azure Portal](quickstart-create-mysql-server-database-using-azure-portal.md) do tworzenia serwera. Ten serwer jest serwerem "Replica" w replikacja typu data-in.

   > [!IMPORTANT]
   > Serwer Azure Database for MySQL należy utworzyć w warstwach cenowych Ogólnego przeznaczenia lub zoptymalizowanych pod kątem pamięci.
   >

2. Tworzenie tych samych kont użytkowników i odpowiednich uprawnień

   Konta użytkowników nie są replikowane z serwera źródłowego do serwera repliki. Jeśli planujesz udostępnienie użytkownikom dostępu do serwera repliki, musisz ręcznie utworzyć wszystkie konta i odpowiednie uprawnienia na tym nowo utworzonym Azure Database for MySQL serwerze.

3. Dodaj adres IP serwera źródłowego do reguł zapory repliki.

   Zaktualizuj reguły zapory za pomocą [witryny Azure Portal](howto-manage-firewall-using-portal.md) lub [interfejsu wiersza polecenia platformy Azure](howto-manage-firewall-using-cli.md).

## <a name="configure-the-source-server"></a>Skonfiguruj serwer źródłowy

Poniższe kroki dotyczą przygotowania i skonfigurowania serwera MySQL hostowanego lokalnie, na maszynie wirtualnej lub usługi bazy danych hostowanej przez innych dostawców chmury dla replikacja typu data-in. Ten serwer jest "źródłem" w replikacji danych.

1. Przed kontynuowaniem Przejrzyj [wymagania dotyczące serwera źródłowego](concepts-data-in-replication.md#requirements) .

2. Upewnij się, że serwer źródłowy zezwala na ruch przychodzący i wychodzący na porcie 3306 oraz że ma **publiczny adres IP**, usługa DNS jest publicznie dostępna lub ma w pełni kwalifikowaną nazwę domeny (FQDN).

   Przetestuj łączność z serwerem źródłowym, próbując nawiązać połączenie za pomocą narzędzia, takiego jak wiersz polecenia MySQL hostowany na innym komputerze lub z [Azure Cloud Shell](../cloud-shell/overview.md) dostępnego w Azure Portal.

   Jeśli organizacja ma ścisłe zasady zabezpieczeń i nie zezwoli na dostęp do wszystkich adresów IP na serwerze źródłowym w celu włączenia komunikacji z platformy Azure do serwera źródłowego, możesz użyć poniższego polecenia, aby określić adres IP serwera MySQL.

   1. Zaloguj się do Azure Database for MySQL przy użyciu narzędzia, takiego jak wiersz polecenia MySQL.

   2. Wykonaj poniższe zapytanie.

      ```bash
      mysql> SELECT @@global.redirect_server_host;
      ```

      Poniżej przedstawiono przykładowe dane wyjściowe:

      ```bash
      +-----------------------------------------------------------+
      | @@global.redirect_server_host                             |
      +-----------------------------------------------------------+
      | e299ae56f000.tr1830.westus1-a.worker.database.windows.net |
       +-----------------------------------------------------------+
      ```

   3. Wyjdź z wiersza polecenia MySQL.
   4. Wykonaj następujące polecenie w narzędziu ping, aby uzyskać adres IP.

      ```bash
      ping <output of step 2b>
      ```

      Na przykład:

      ```bash
      C:\Users\testuser> ping e299ae56f000.tr1830.westus1-a.worker.database.windows.net
      Pinging tr1830.westus1-a.worker.database.windows.net (**11.11.111.111**) 56(84) bytes of data.
      ```

   5. Skonfiguruj reguły zapory serwera źródłowego w taki sposób, aby obejmowały adres IP przedniego poprzedniego kroku w porcie 3306.

   > [!NOTE]
   > Ten adres IP może ulec zmianie z powodu operacji konserwacji/wdrażania. Ta metoda łączności jest tylko dla klientów, którzy nie mogą udzielić zezwolenia na wszystkie adresy IP na porcie 3306.
  
3. Włącz rejestrowanie binarne

   Sprawdź, czy w źródle jest włączone rejestrowanie binarne, uruchamiając następujące polecenie: 

   ```sql
   SHOW VARIABLES LIKE 'log_bin';
   ```

   Jeśli zmienna [`log_bin`](https://dev.mysql.com/doc/refman/8.0/en/replication-options-binary-log.html#sysvar_log_bin) jest zwracana z wartością "on", rejestrowanie binarne jest włączone na serwerze.

   Jeśli `log_bin` zwracana jest wartość "off", należy włączyć rejestrowanie binarne, edytując plik my. cnf, aby `log_bin=ON` zmiany zaczęły obowiązywać.

4. Ustawienia serwera źródłowego

   Replikacja typu data-in wymaga `lower_case_table_names` , aby parametr był spójny między serwerami źródłowym i repliki. Domyślnie ten parametr jest 1 w Azure Database for MySQL.

   ```sql
   SET GLOBAL lower_case_table_names = 1;
   ```

5. Utwórz nową rolę replikacji i skonfiguruj uprawnienie

   Utwórz konto użytkownika na serwerze źródłowym, który jest skonfigurowany z uprawnieniami replikacji. Można to zrobić za pomocą poleceń SQL lub narzędzi, takich jak MySQL Workbench. Należy rozważyć, czy ma być przeprowadzana replikacja przy użyciu protokołu SSL, ponieważ należy ją określić podczas tworzenia użytkownika. Zapoznaj się z dokumentacją programu MySQL, aby dowiedzieć się, jak [dodać konta użytkowników](https://dev.mysql.com/doc/refman/5.7/en/user-names.html) na serwerze źródłowym.

   W poniższych poleceniach Nowa rola replikacji ma dostęp do źródła z dowolnego komputera, a nie tylko z komputera, który hostuje źródło. W tym celu należy określić "syncuser@"% "w poleceniu Create User. Zapoznaj się z dokumentacją programu MySQL, aby dowiedzieć się więcej o [określaniu nazw kont](https://dev.mysql.com/doc/refman/5.7/en/account-names.html).

   **Polecenie SQL**

   *Replikacja przy użyciu protokołu SSL*

   Aby wymagać protokołu SSL dla wszystkich połączeń użytkowników, użyj następującego polecenia, aby utworzyć użytkownika:

   ```sql
   CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
   GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%' REQUIRE SSL;
   ```

   *Replikacja bez protokołu SSL*

   Jeśli protokół SSL nie jest wymagany dla wszystkich połączeń, użyj następującego polecenia, aby utworzyć użytkownika:

   ```sql
   CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
   GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%';
   ```

   **MySQL Workbench**

   Aby utworzyć rolę replikacji w programie MySQL Workbench, Otwórz panel **Użytkownicy i uprawnienia** w panelu **zarządzania** , a następnie wybierz pozycję **Dodaj konto**.

   :::image type="content" source="./media/howto-data-in-replication/users_privileges.png" alt-text="Użytkownicy i uprawnienia":::

   Wpisz nazwę użytkownika w polu **Nazwa logowania** .

   :::image type="content" source="./media/howto-data-in-replication/syncuser.png" alt-text="Użytkownik synchronizacji":::

   Wybierz panel **role administracyjne** , a następnie wybierz pozycję **replikacja podrzędna** z listy **uprawnień globalnych**. Następnie wybierz pozycję **Zastosuj** , aby utworzyć rolę replikacji.

   :::image type="content" source="./media/howto-data-in-replication/replicationslave.png" alt-text="Replikacja podrzędna":::

6. Ustaw serwer źródłowy do trybu tylko do odczytu

   Przed rozpoczęciem zrzutu bazy danych należy umieścić serwer w trybie tylko do odczytu. W trybie tylko do odczytu źródło nie będzie w stanie przetwarzać żadnych transakcji zapisu. Oceń wpływ na swoją firmę i w razie potrzeby Zaplanuj okno tylko do odczytu w czasie poza godzinami szczytu.

   ```sql
   FLUSH TABLES WITH READ LOCK;
   SET GLOBAL read_only = ON;
   ```

7. Pobieranie binarnej nazwy pliku dziennika i przesunięcia

   Uruchom [`show master status`](https://dev.mysql.com/doc/refman/5.7/en/show-master-status.html) polecenie, aby określić bieżącą nazwę pliku dziennika binarnego i przesunięcia.

   ```sql
    show master status;
   ```

   Wyniki powinny wyglądać podobnie do poniższego. Pamiętaj, aby zanotować nazwę pliku binarnego, ponieważ zostanie ona użyta w kolejnych krokach.

   :::image type="content" source="./media/howto-data-in-replication/masterstatus.png" alt-text="Wyniki stanu głównego":::

## <a name="dump-and-restore-source-server"></a>Zrzuć i przywróć serwer źródłowy

1. Ustal, które bazy danych i tabele, które mają być replikowane, do Azure Database for MySQL i wykonaj Zrzut z serwera źródłowego.

    Możesz użyć mysqldump, aby zrzucić bazy danych z serwera głównego. Aby uzyskać szczegółowe informacje, zobacz [zrzut & przywracanie](concepts-migrate-dump-restore.md). Nie jest konieczne zatopienie biblioteki MySQL i biblioteki testowej.

2. Ustaw serwer źródłowy na tryb odczytu/zapisu.

   Po zrzucie bazy danych Zmień źródłowy serwer MySQL z powrotem na tryb odczytu/zapisu.

   ```sql
   SET GLOBAL read_only = OFF;
   UNLOCK TABLES;
   ```

3. Przywróć plik zrzutu na nowy serwer.

   Przywróć plik zrzutu do serwera utworzonego w usłudze Azure Database for MySQL. Zapoznaj się z artykułem [zrzut & Przywróć](concepts-migrate-dump-restore.md) , aby przywrócić plik zrzutu do serwera MySQL. Jeśli plik zrzutu jest duży, przekaż go do maszyny wirtualnej na platformie Azure w tym samym regionie, w którym znajduje się serwer repliki. Przywróć go do serwera Azure Database for MySQL z maszyny wirtualnej.

## <a name="link-source-and-replica-servers-to-start-data-in-replication"></a>Połącz serwery źródłowe i repliki, aby uruchomić replikacja typu data-in

1. Ustaw serwer źródłowy.

   Wszystkie funkcje replikacja typu data-in są wykonywane przez procedury składowane. Wszystkie procedury można znaleźć w [replikacja typu Data-in procedurach składowanych](./reference-stored-procedures.md). Procedury składowane można uruchamiać w programie MySQL Shell lub MySQL Workbench.

   Aby połączyć dwa serwery i rozpocząć replikację, zaloguj się do docelowego serwera repliki w usłudze Azure DB dla MySQL i ustaw wystąpienie zewnętrzne jako serwer źródłowy. W tym celu należy użyć `mysql.az_replication_change_master` procedury składowanej na serwerze usługi Azure DB dla programu MySQL.

   ```sql
   CALL mysql.az_replication_change_master('<master_host>', '<master_user>', '<master_password>', 3306, '<master_log_file>', <master_log_pos>, '<master_ssl_ca>');
   ```

   - master_host: Nazwa hosta serwera źródłowego
   - master_user: Nazwa użytkownika serwera źródłowego
   - master_password: hasło dla serwera źródłowego
   - master_log_file: nie uruchomiono binarnej nazwy pliku dziennika `show master status`
   - master_log_pos: uruchamianie binarnej lokalizacji dziennika `show master status`
   - master_ssl_ca: kontekst certyfikatu urzędu certyfikacji. Jeśli nie korzystasz z protokołu SSL, Przekaż pusty ciąg.

     Zaleca się, aby przekazać ten parametr jako zmienną. Więcej informacji można znaleźć w poniższych przykładach.

   > [!NOTE]
   > Jeśli serwer źródłowy jest hostowany na maszynie wirtualnej platformy Azure, ustaw opcję "Zezwalaj na dostęp do usług platformy Azure" na "włączone", aby umożliwić serwerom źródłowym i Resource wzajemne komunikowanie się ze sobą. To ustawienie można zmienić przy użyciu opcji **zabezpieczeń połączenia** . Aby uzyskać więcej informacji, zobacz [Zarządzanie regułami zapory za pomocą portalu](howto-manage-firewall-using-portal.md) .

   **Przykłady**

   *Replikacja przy użyciu protokołu SSL*

   Zmienna `@cert` jest tworzona przez uruchomienie następujących poleceń MySQL:

      ```sql
      SET @cert = '-----BEGIN CERTIFICATE-----
      PLACE YOUR PUBLIC KEY CERTIFICATE'`S CONTEXT HERE
      -----END CERTIFICATE-----'
      ```

   Replikacja przy użyciu protokołu SSL jest skonfigurowana między serwerem źródłowym hostowanym w domenie "companya.com" i serwerem repliki hostowanym w Azure Database for MySQL. Ta procedura składowana jest uruchamiana w replice.

      ```sql
      CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mysql-bin.000002', 120, @cert);
      ```

   *Replikacja bez protokołu SSL*

   Replikacja bez protokołu SSL jest skonfigurowana między serwerem źródłowym hostowanym w domenie "companya.com" i serwerem repliki hostowanym w Azure Database for MySQL. Ta procedura składowana jest uruchamiana w replice.

      ```sql
      CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mysql-bin.000002', 120, '');
      ```

2. Filtrowanie.

   Jeśli chcesz pominąć replikację niektórych tabel z poziomu głównego, zaktualizuj `replicate_wild_ignore_table` parametr serwera na serwerze repliki. Można podać więcej niż jeden wzorzec tabeli przy użyciu listy rozdzielanej przecinkami.

   Zapoznaj się z [dokumentacją programu MySQL](https://dev.mysql.com/doc/refman/8.0/en/replication-options-replica.html#option_mysqld_replicate-wild-ignore-table) , aby dowiedzieć się więcej o tym parametrze.

   Aby zaktualizować parametr, można użyć [Azure Portal](howto-server-parameters.md) lub [interfejsu wiersza polecenia platformy Azure](howto-configure-server-parameters-using-cli.md).

3. Rozpocznij replikację.

   Wywołaj `mysql.az_replication_start` procedurę składowaną, aby rozpocząć replikację.

   ```sql
   CALL mysql.az_replication_start;
   ```

4. Sprawdź stan replikacji.

   Wywołaj [`show slave status`](https://dev.mysql.com/doc/refman/5.7/en/show-slave-status.html) polecenie na serwerze repliki, aby wyświetlić stan replikacji.

   ```sql
   show slave status;
   ```

   Jeśli stan i ma `Slave_IO_Running` `Slave_SQL_Running` wartość "yes", a wartością `Seconds_Behind_Master` jest "0", replikacja działa prawidłowo. `Seconds_Behind_Master` wskazuje, jak późna jest replika. Jeśli wartość nie jest równa "0", oznacza to, że replika przetwarza aktualizacje.

## <a name="other-stored-procedures"></a>Inne procedury składowane

### <a name="stop-replication"></a>Zatrzymywanie replikacji

Aby zatrzymać replikację między serwerem źródłowym i repliką programu, należy użyć następującej procedury składowanej:

```sql
CALL mysql.az_replication_stop;
```

### <a name="remove-replication-relationship"></a>Usuń relację replikacji

Aby usunąć relację między serwerem źródłowym i repliką programu, należy użyć następującej procedury składowanej:

```sql
CALL mysql.az_replication_remove_master;
```

### <a name="skip-replication-error"></a>Pomiń błąd replikacji

Aby pominąć błąd replikacji i umożliwić kontynuowanie replikacji, należy użyć następującej procedury składowanej:

```sql
CALL mysql.az_replication_skip_counter;
```

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej na temat [replikacja typu data-in](concepts-data-in-replication.md) Azure Database for MySQL.
