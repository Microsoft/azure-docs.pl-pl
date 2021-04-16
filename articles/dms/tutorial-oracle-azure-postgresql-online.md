---
title: 'Samouczek: migrowanie bazy danych Oracle w trybie online do Azure Database for PostgreSQL'
titleSuffix: Azure Database Migration Service
description: Dowiedz się, jak przeprowadzić migrację online z bazy danych Oracle lokalnie lub na maszynach wirtualnych do Azure Database for PostgreSQL przy użyciu Azure Database Migration Service.
services: dms
author: arunkumarthiags
ms.author: arthiaga
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 01/24/2020
ms.openlocfilehash: 76ab2204a81d3cbfb559bfa4591f1f332b1707ee
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107388078"
---
# <a name="tutorial-migrate-oracle-to-azure-database-for-postgresql-online-using-dms-preview"></a>Samouczek: migrowanie bazy danych Oracle do Azure Database for PostgreSQL online przy użyciu usługi DMS (wersja zapoznawcza)

> [!IMPORTANT]
> **Scenariusz migracji Azure Database for PostgreSQL Oracle** (obecnie w wersji zapoznawczej) nie będzie już dostępny po 1 maja 2021 r. Będziemy nadal zapewniać pomoc techniczną za pośrednictwem alternatywnych narzędzi (takich jak Ora2pg) i zapewniać najlepsze środowisko migracji dla migracji Oracle do PostgreSQL. Aby uzyskać najlepsze rozwiązania dotyczące migracji, zobacz Oracle to Azure Database for PostgreSQL migration guide (Przewodnik migracji z bazy [danych Oracle Azure Database for PostgreSQL migracji).](https://aka.ms/OracletoPGguide) 

Za pomocą usługi Azure Database Migration Service można migrować bazy danych z baz danych Oracle hostowanych lokalnie lub na maszynach wirtualnych Azure Database for PostgreSQL [z](../postgresql/index.yml) minimalnym przestojem. Innymi słowy, możesz ukończyć migrację z minimalnym przestojem aplikacji. W tym samouczku zmigrujesz przykładową bazę danych **HR** z lokalnego wystąpienia maszyny wirtualnej oracle 11g do programu Azure Database for PostgreSQL przy użyciu działania migracji online w programie Azure Database Migration Service.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
>
> * Oceń nakład pracy migracji przy użyciu narzędzia ora2pg.
> * Migrowanie przykładowego schematu przy użyciu narzędzia ora2pg.
> * Utwórz wystąpienie usługi Azure Database Migration Service.
> * Utwórz projekt migracji przy użyciu Azure Database Migration Service.
> * Uruchamianie migracji.
> * Monitoruj migrację.

> [!NOTE]
> Przeprowadzenie Azure Database Migration Service online wymaga utworzenia wystąpienia na podstawie warstwy cenowej Premium.

> [!IMPORTANT]
> Aby uzyskać optymalne środowisko migracji, firma Microsoft zaleca utworzenie wystąpienia usługi Azure Database Migration Service w tym samym regionie świadczenia usługi Azure co docelowa baza danych. Przenoszenie danych między regionami lub lokalizacjami geograficznymi może spowalniać proces migracji i powodować błędy.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

W tym artykule opisano sposób przeprowadzania migracji online z bazy danych Oracle do Azure Database for PostgreSQL.

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka niezbędne są następujące elementy:

* Pobierz i zainstaluj [program Oracle 11g w wersji 2 (Standard Edition, Standard Edition One lub Enterprise Edition).](https://www.oracle.com/technetwork/database/enterprise-edition/downloads/index.html)
* Pobierz przykładową **bazę danych HR** stąd. [](https://docs.oracle.com/database/121/COMSC/installation.htm#COMSC00002)
* Pobierz i [zainstaluj pakiet ora2pg w systemie Windows lub Linux.](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Steps%20to%20Install%20ora2pg%20on%20Windows%20and%20Linux.pdf)
* [Utwórz wystąpienie usługi Azure Database for PostgreSQL](../postgresql/quickstart-create-server-database-portal.md).
* Połącz się z wystąpieniem i utwórz bazę danych przy użyciu instrukcji w tym [dokumencie](../postgresql/tutorial-design-database-using-azure-portal.md).
* Utwórz Microsoft Azure Virtual Network dla usługi Azure Database Migration Service przy użyciu modelu wdrażania usługi Azure Resource Manager, który zapewnia łączność lokacja-lokacja z lokalnymi serwerami źródłowymi przy użyciu usługi [ExpressRoute](../expressroute/expressroute-introduction.md) lub sieci [VPN.](../vpn-gateway/vpn-gateway-about-vpngateways.md) Aby uzyskać więcej informacji na temat tworzenia sieci wirtualnej, zobacz dokumentację usługi [Virtual Network, a](../virtual-network/index.yml)zwłaszcza artykuły Szybki start ze szczegółami krok po kroku.

  > [!NOTE]
  > Jeśli podczas konfigurowania sieci wirtualnej używasz usługi ExpressRoute z [](../virtual-network/virtual-network-service-endpoints-overview.md) sieciową komunikacji równorzędnej z firmą Microsoft, dodaj następujące punkty końcowe usługi do podsieci, w której zostanie aprowizowana usługa:
  >
  > * Docelowy punkt końcowy bazy danych (na przykład punkt końcowy SQL, Cosmos DB punkt końcowy i tak dalej)
  > * Punkt końcowy magazynu
  > * Punkt końcowy usługi Service Bus
  >
  > Ta konfiguracja jest konieczna, ponieważ Azure Database Migration Service połączenia z Internetem.

* Upewnij się, że reguły sieciowej grupy zabezpieczeń sieci wirtualnej nie blokują portu wychodzącego 443 tagu usługi ServiceTag dla usług ServiceBus, Storage i AzureMonitor. Aby uzyskać więcej informacji na temat filtrowania ruchu sieciowej grupy zabezpieczeń w sieci wirtualnej, zobacz artykuł Filtrowanie ruchu sieciowego [przy użyciu sieciowych grup zabezpieczeń.](../virtual-network/virtual-network-vnet-plan-design-arm.md)
* [Zapora sytemu Windows skonfigurowana pod kątem dostępu do aparatu bazy danych](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Otwórz zaporę systemu Windows, aby Azure Database Migration Service dostęp do źródłowego serwera Oracle, który domyślnie jest portem TCP 1521.
* W przypadku korzystania z urządzenia zapory przed źródłową bazą danych może być konieczne dodanie reguł zapory w celu umożliwienia Azure Database Migration Service dostępu do źródłowych baz danych na potrzeby migracji.
* Utwórz regułę zapory na [poziomie serwera dla](../azure-sql/database/firewall-configure.md) Azure Database for PostgreSQL, Azure Database Migration Service dostępu do docelowych baz danych. Podaj zakres podsieci sieci wirtualnej używanej do Azure Database Migration Service.
* Zapewnianie dostępu do źródłowych baz danych Oracle.

  > [!NOTE]
  > Rola administratora bazy danych jest wymagana, aby użytkownik łączył się ze źródłem Oracle.

  * Dzienniki ponownego archiwizowania są wymagane do synchronizacji przyrostowej w Azure Database Migration Service do przechwytywania zmian danych. Wykonaj następujące kroki, aby skonfigurować źródło Oracle:
    * Zaloguj się przy użyciu uprawnień SYSDBA, uruchamiając następujące polecenie:

      ```
      sqlplus (user)/(password) as sysdba
      ```

    * Zamknij wystąpienie bazy danych, uruchamiając następujące polecenie.

      ```
      SHUTDOWN IMMEDIATE;
      ```

      Poczekaj na potwierdzenie `'ORACLE instance shut down'` .

    * Uruchom nowe wystąpienie i zainstaluj bazę danych (ale nie otwieraj jej), aby włączyć lub wyłączyć archiwizowanie, uruchamiając następujące polecenie:

      ```
      STARTUP MOUNT;
      ```

      Baza danych musi być zainstalowany; zaczekaj na potwierdzenie "Uruchomiliśmy wystąpienie Oracle".

    * Zmień tryb archiwizacji bazy danych, uruchamiając następujące polecenie:

      ```
      ALTER DATABASE ARCHIVELOG;
      ```

    * Otwórz bazę danych dla normalnych operacji, uruchamiając następujące polecenie:

      ```
      ALTER DATABASE OPEN;
      ```

      Może być konieczne ponowne uruchomienie, aby plik ARC był pokazywany.

    * Aby to sprawdzić, uruchom następujące polecenie:

      ```
      SELECT log_mode FROM v$database;
      ```

      Powinna zostać wyświetlony odpowiedź `'ARCHIVELOG'` . Jeśli odpowiedź to `'NOARCHIVELOG'` , to wymaganie nie jest spełnione.

  * Włącz rejestrowanie uzupełniające replikacji przy użyciu jednej z następujących opcji.

    * **Opcja 1**.
      Zmień rejestrowanie uzupełniające na poziomie bazy danych, aby obejmować wszystkie tabele z pk i indeksem unikatowym. Zapytanie dotyczące wykrywania zwróci `'IMPLICIT'` .

      ```
      ALTER DATABASE ADD SUPPLEMENTAL LOG DATA (PRIMARY KEY, UNIQUE) COLUMNS;
      ```

      Zmień rejestrowanie uzupełniające na poziomie tabeli. Uruchamiany tylko dla tabel, które mają manipulowanie danymi i nie mają indeksów PKs ani unikatowych indeksów.

      ```
      ALTER TABLE [TABLENAME] ADD SUPPLEMENTAL LOG DATA (ALL) COLUMNS;
      ```

    * **Opcja 2**.
      Zmień rejestrowanie uzupełniające na poziomie bazy danych, aby obejmować wszystkie tabele, a zapytanie wykrywania zwróci wartość `'YES'` .

      ```
      ALTER DATABASE ADD SUPPLEMENTAL LOG DATA;
      ```

      Zmień rejestrowanie uzupełniające na poziomie tabeli. Postępuj zgodnie z logiką poniżej, aby uruchomić tylko jedną instrukcje dla każdej tabeli.

      Jeśli tabela zawiera klucz podstawowy:

      ```
      ALTER TABLE xxx ADD SUPPLEMENTAL LOG DATA (PRIMARY KEY) COLUMNS;
      ```

      Jeśli tabela ma unikatowy indeks:

      ```
      ALTER TABLE xxx ADD SUPPLEMENTAL LOG GROUP (first unique index columns) ALWAYS;
      ```

      W przeciwnym razie uruchom następujące polecenie:

      ```
      ALTER TABLE xxx ADD SUPPLEMENTAL LOG DATA (ALL) COLUMNS;
      ```

    Aby to sprawdzić, uruchom następujące polecenie:

      ```
      SELECT supplemental_log_data_min FROM v$database;
      ```

    Powinna zostać wyświetlony odpowiedź `'YES'` .

## <a name="assess-the-effort-for-an-oracle-to-azure-database-for-postgresql-migration"></a>Ocena nakładu pracy firmy Oracle w celu Azure Database for PostgreSQL migracji

Zalecamy użycie narzędzia ora2pg do oceny nakładu pracy wymaganego do przeprowadzenia migracji z bazy danych Oracle do Azure Database for PostgreSQL. Użyj dyrektywy , aby utworzyć raport z listą wszystkich obiektów Oracle, szacowanego kosztu migracji (w dniach dewelopera) i niektórych obiektów bazy danych, które mogą wymagać specjalnej uwagi w ramach `ora2pg -t SHOW_REPORT` konwersji.

Większość klientów poświęca znaczną ilość czasu na przeglądanie raportu z oceny i uwzględnianie nakładu pracy na automatyczną i ręczną konwersję.

Aby skonfigurować i uruchomić program ora2pg w celu utworzenia raportu z oceny, zobacz sekcję **Premigration: Assessment (Premigracja:** ocena) w podręczniku [Oracle Azure Database for PostgreSQL Cookbook.](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20PostgreSQL%20Migration%20Cookbook.pdf) Przykładowy raport oceny ora2pg jest dostępny do odwołania [tutaj.](https://ora2pg.darold.net/report.html)

## <a name="export-the-oracle-schema"></a>Eksportowanie schematu Oracle

Zalecamy użycie polecenia ora2pg w celu przekonwertowania schematu Oracle i innych obiektów Oracle (typów, procedur, funkcji itp.) na schemat zgodny z Azure Database for PostgreSQL. Ora2pg zawiera wiele dyrektyw, które ułatwiają wstępne definiowanie niektórych typów danych. Na przykład można użyć dyrektywy , aby zastąpić wszystkie wartości `DATA_TYPE` NUMBER(*,0) bigint zamiast NUMERIC(38).

Możesz uruchomić program ora2pg, aby wyeksportować każdy obiekt bazy danych w plikach SQL. Następnie możesz przejrzeć pliki sql przed ich zaimportowaniem do programu Azure Database for PostgreSQL za pomocą narzędzia psql lub wykonać polecenie . Skrypt SQL w programie PgAdmin.

```
psql -f [FILENAME] -h [AzurePostgreConnection] -p 5432 -U [AzurePostgreUser] -d database 
```

Na przykład:

```
psql -f %namespace%\schema\sequences\sequence.sql -h server1-server.postgres.database.azure.com -p 5432 -U username@server1-server -d database
```

Aby skonfigurować i uruchomić program ora2pg w celu konwersji schematu, zobacz sekcję **Migration: Schema and data** (Migracja: schemat i dane) bazy danych Oracle do Azure Database for PostgreSQL [Cookbook.](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20PostgreSQL%20Migration%20Cookbook.pdf)

## <a name="set-up-the-schema-in-azure-database-for-postgresql"></a>Konfigurowanie schematu w programie Azure Database for PostgreSQL

Przed uruchomieniem potoku migracji w programie można przekonwertować schematy tabel Oracle, procedury składowane, pakiety i inne obiekty bazy danych w celu ich zgodności z bazą danych Postgres przy użyciu narzędzia ora2p Azure Database Migration Service g. Zapoznaj się z poniższymi linkami, aby dowiedzieć się, jak pracować z usługą ora2pg:

* [Instalowanie ora2pg w systemie Windows](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Steps%20to%20Install%20ora2pg%20on%20Windows%20and%20Linux.pdf)
* [Oracle to Azure PostgreSQL Migration Cookbook](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20PostgreSQL%20Migration%20Cookbook.pdf)

Azure Database Migration Service można również utworzyć schemat tabeli PostgreSQL. Usługa uzyskuje dostęp do schematu tabeli w połączonym źródle Oracle i tworzy zgodny schemat tabeli w Azure Database for PostgreSQL. Pamiętaj, aby zweryfikować i sprawdzić format schematu w Azure Database for PostgreSQL po Azure Database Migration Service zakończeniu tworzenia schematu i przenoszenia danych.

> [!IMPORTANT]
> Azure Database Migration Service tworzy tylko schemat tabeli; inne obiekty bazy danych, takie jak procedury składowane, pakiety, indeksy itp., nie są tworzone.

Pamiętaj również, aby usunąć klucz obcy w docelowej bazie danych, aby pełne obciążenie było uruchamiane. Zapoznaj się z **sekcją Migrowanie** przykładowego schematu w tym artykule, aby uzyskać skrypt, który umożliwia upuszczanie klucza obcego. [](./tutorial-postgresql-azure-postgresql-online.md) Użyj Azure Database Migration Service, aby uruchomić w celu pełnego ładowania i synchronizacji.

### <a name="when-the-postgresql-table-schema-already-exists"></a>Jeśli schemat tabeli PostgreSQL już istnieje

Jeśli tworzysz schemat PostgreSQL przy użyciu narzędzi, takich jak ora2pg, przed rozpoczęciem przemieszczania danych za pomocą programu Azure Database Migration Service, zamapuj tabele źródłowe na tabele docelowe w Azure Database Migration Service.

1. Podczas tworzenia nowej bazy danych Oracle do Azure Database for PostgreSQL migracji zostanie wyświetlony monit o wybranie docelowej bazy danych i schematu docelowego w kroku Wybieranie schematów. Wypełnij docelową bazę danych i schemat docelowy.

   ![Zrzut ekranu przedstawia mapowanie do docelowych baz danych.](media/tutorial-oracle-azure-postgresql-online/dms-map-to-target-databases.png)

2. Na **ekranie** Ustawienia migracji zostanie wyświetlona lista tabel w źródle Oracle. Azure Database Migration Service próbuje dopasować tabele w tabelach źródłowych i docelowych na podstawie nazwy tabeli. Jeśli istnieje wiele pasujących tabel docelowych o różnych wielkościach, możesz wybrać tabelę docelową do mapowania.

    ![Zrzut ekranu przedstawia ustawienia migracji.](media/tutorial-oracle-azure-postgresql-online/dms-migration-settings.png)

> [!NOTE]
> Jeśli musisz zamapować nazwy tabel źródłowych na tabele o różnych nazwach, wyślij wiadomość e-mail i udostępnimy [dmsfeedback@microsoft.com](mailto:dmsfeedbac@microsoft.com) skrypt do automatyzacji procesu.

### <a name="when-the-postgresql-table-schema-doesnt-exist"></a>Gdy schemat tabeli PostgreSQL nie istnieje

Jeśli docelowa baza danych PostgreSQL nie zawiera żadnych informacji o schemacie tabeli, program Azure Database Migration Service skonwertuje schemat źródłowy i odtworzy go w docelowej bazie danych. Pamiętaj, Azure Database Migration Service tylko schemat tabeli, a nie inne obiekty bazy danych, takie jak procedury składowane, pakiety i indeksy.
Aby Azure Database Migration Service schemat, upewnij się, że środowisko docelowe zawiera schemat bez istniejących tabel. Jeśli Azure Database Migration Service dowolną tabelę, usługa zakłada, że schemat został utworzony przez zewnętrzne narzędzie, takie jak ora2pg.

> [!IMPORTANT]
> Azure Database Migration Service wymaga, aby wszystkie tabele były tworzone w taki sam sposób, przy użyciu Azure Database Migration Service lub narzędzia, takiego jak ora2pg, ale nie obu.

Aby rozpocząć:

1. Utwórz schemat w docelowej bazie danych na podstawie wymagań aplikacji. Domyślnie nazwy kolumn i schematu tabeli PostgreSQL są małe. Z drugiej strony kolumny i schemat tabeli Oracle są domyślnie we wszystkich literach.
2. W kroku Wybierz schematy określ docelową bazę danych i schemat docelowy.
3. Na podstawie schematu, który został Azure Database for PostgreSQL w Azure Database Migration Service, są używane następujące reguły przekształcania:

    Jeśli nazwa schematu w źródle Oracle jest taka sama jak Azure Database for PostgreSQL, program Azure Database Migration Service tworzy schemat tabeli przy użyciu tego samego przypadku, co w *przypadku obiektu docelowego*.

    Na przykład:

    | Źródłowy schemat Oracle | Docelowa baza danych PostgreSQL.schema | Utworzono usługę DMS schema.table.column |
    | ------------- | ------------- | ------------- |
    | HR | targetHR.public | public.countries.country_id |
    | HR | targetHR.trgthr | trgthr.countries.country_id |
    | HR | targetHR.TARGETHR | "TARGETHR"." KRAJE"." COUNTRY_ID" |
    | HR | targetHR.HR | "HR"." KRAJE"." COUNTRY_ID" |
    | HR | targetHR.Hr | *Nie można mapować przypadków mieszanych |

    *Aby utworzyć nazwy tabel i schematów przypadków mieszanych w docelowym programie PostgreSQL, skontaktuj się z . [dmsfeedback@microsoft.com](mailto:dmsfeedback@microsoft.com) Możemy udostępnić skrypt do skonfigurowania schematu tabeli przypadków mieszanych w docelowej bazie danych PostgreSQL.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Rejestrowanie dostawcy zasobów Microsoft.DataMigration

1. Zaloguj się w witrynie Azure Portal, wybierz pozycję **Wszystkie usługi**, a następnie wybierz pozycję **Subskrypcje**.

   ![Wyświetlanie subskrypcji w portalu](media/tutorial-oracle-azure-postgresql-online/portal-select-subscriptions.png)

2. Wybierz subskrypcję, w której chcesz utworzyć wystąpienie usługi Azure Database Migration Service, a następnie wybierz pozycję **Dostawcy zasobów**.

    ![Wyświetlanie dostawców zasobów](media/tutorial-oracle-azure-postgresql-online/portal-select-resource-provider.png)

3. Wyszukaj pozycję migration (Migracja), a następnie po prawej stronie pola **Microsoft.DataMigration** wybierz pozycję **Register (Zarejestruj).**

    ![Rejestrowanie dostawcy zasobów](media/tutorial-oracle-azure-postgresql-online/portal-register-resource-provider.png)

## <a name="create-a-dms-instance"></a>Tworzenie wystąpienia usługi DMS

1. W witrynie Azure Portal wybierz pozycję + **Utwórz zasób**, wyszukaj usługę Azure Database Migration Service, a następnie wybierz usługę **Azure Database Migration Service** na liście rozwijanej.

    ![Azure Marketplace](media/tutorial-oracle-azure-postgresql-online/portal-marketplace.png)

2. Na ekranie **Azure Database Migration Service** wybierz polecenie **Utwórz**.

    ![Tworzenie wystąpienia usługi Azure Database Migration Service](media/tutorial-oracle-azure-postgresql-online/dms-create2.png)
  
3. Na ekranie **Tworzenie usługi migracji** określ nazwę usługi, subskrypcję oraz nową lub istniejącą grupę zasobów.

4. Wybierz istniejącą sieć wirtualną lub utwórz nową.

    Sieć wirtualna zapewnia Azure Database Migration Service dostępu do źródłowej bazy danych Oracle i docelowego Azure Database for PostgreSQL danych.

    Aby uzyskać więcej informacji na temat tworzenia sieci wirtualnej w Azure Portal, zobacz artykuł [Create a virtual network using the Azure Portal](../virtual-network/quick-create-portal.md)(Tworzenie sieci wirtualnej przy użyciu Azure Portal).

5. Wybierz warstwę cenową.

    Aby uzyskać więcej informacji na temat kosztów i warstw cenowych, zobacz [stronę z cennikiem](https://aka.ms/dms-pricing).

    ![Konfigurowanie ustawień wystąpienia usługi Azure Database Migration Service](media/tutorial-oracle-azure-postgresql-online/dms-settings5.png)

6. Wybierz pozycję **Utwórz**, aby utworzyć usługę.

## <a name="create-a-migration-project"></a>Tworzenie projektu migracji

Po utworzeniu usługi znajdź ją w witrynie Azure Portal, otwórz ją, a następnie utwórz nowy projekt migracji.

1. W witrynie Azure Portal wybierz pozycję **Wszystkie usługi**, wyszukaj usługę Azure Database Migration Service, a następnie wybierz pozycję **Azure Database Migration Services**.

    ![Znajdowanie wszystkich wystąpień usługi Azure Database Migration Service](media/tutorial-oracle-azure-postgresql-online/dms-search.png)

2. Na ekranie **Azure Database Migration Service** wyszukaj nazwę utworzonego wystąpienia usługi Azure Database Migration Service, a następnie wybierz to wystąpienie.

    ![Znajdowanie wystąpienia usługi Azure Database Migration Service](media/tutorial-oracle-azure-postgresql-online/dms-instance-search.png)

3. Wybierz pozycję + **Nowy projekt migracji**.
4. Na **ekranie Nowy** projekt migracji określ nazwę projektu, w polu tekstowym Typ  serwera źródłowego wybierz pozycję  **Oracle,** w polu tekstowym Typ serwera docelowego wybierz pozycję **Azure Database for PostgreSQL**.
5. W sekcji **Wybierz typ działania** wybierz pozycję **Migracja danych w trybie online.**

   ![Tworzenie projektu usługi Database Migration Service](media/tutorial-oracle-azure-postgresql-online/dms-create-project5.png)

   > [!NOTE]
   > Alternatywnie możesz wybrać pozycję **Utwórz tylko projekt,** aby utworzyć projekt migracji teraz i wykonać migrację później.

6. Wybierz **pozycję** Zapisz, zanotuj wymagania dotyczące pomyślnego użycia Azure Database Migration Service do przeprowadzenia migracji w trybie online, a następnie wybierz **pozycję Utwórz i uruchom działanie**.

## <a name="specify-source-details"></a>Określanie szczegółów źródła

* Na **ekranie Dodawanie szczegółów źródła** określ szczegóły połączenia dla źródłowego wystąpienia Oracle.

  ![Ekran Dodawanie szczegółów źródła](media/tutorial-oracle-azure-postgresql-online/dms-add-source-details.png)

## <a name="upload-oracle-oci-driver"></a>Przekazywanie sterownika Oracle OCI

1. Wybierz pozycję **Zapisz, a** następnie na ekranie Instalowanie sterownika **OCI** zaloguj się do konta Oracle i pobierz sterownikinstantclient-basiclite-windows.x64-12.2.0.1.0.zip(37 128 586 bajtów) (sumy kontrolne SHA1: 865082268) z [tego menu.](https://www.oracle.com/technetwork/topics/winx64soft-089540.html#ic_winx64_inst)
2. Pobierz sterownik do folderu udostępnionego.

   Upewnij się, że folder jest współużytowany z określoną nazwą użytkownika z minimalnym dostępem tylko do odczytu. Azure Database Migration Service uzyskuje dostęp do udziału i odczytuje go w celu przekazania sterownika OCI na platformę Azure, personifikując podaną nazwę użytkownika.

   Określana nazwa użytkownika musi być kontem użytkownika systemu Windows.

   ![Instalacja sterownika OCI](media/tutorial-oracle-azure-postgresql-online/dms-oci-driver-install.png)

## <a name="specify-target-details"></a>Określanie szczegółów elementu docelowego

1. Wybierz pozycję Zapisz,  a następnie na ekranie Szczegóły obiektu docelowego określ szczegóły połączenia dla docelowego serwera Azure Database for PostgreSQL, który  jest wstępnie zaaprowizowym wystąpieniem usługi Azure Database for PostgreSQL, na którym wdrożono schemat hr.

    ![Ekran Szczegóły elementu docelowego](media/tutorial-oracle-azure-postgresql-online/dms-add-target-details1.png)

2. Wybierz polecenie **Zapisz**, a następnie na ekranie **Mapuj do docelowych baz danych**, określ mapowanie źródłowej i docelowej bazy danych na potrzeby migracji.

    Jeśli docelowa baza danych zawiera taką samą nazwę bazy danych jak źródłową bazę danych, Azure Database Migration Service domyślnie wybierze docelową bazę danych.

    ![Mapowanie do docelowych baz danych](media/tutorial-oracle-azure-postgresql-online/dms-map-target-details.png)

3. Wybierz pozycję **Zapisz**. Na ekranie **Podsumowanie migracji** w polu tekstowym **Nazwa działania** określ nazwę działania migracji, a następnie sprawdź podsumowanie, aby upewnić się, że szczegółowe informacje źródłowe i docelowe pasują do wcześniej podanych.

    ![Podsumowanie migracji](media/tutorial-oracle-azure-postgresql-online/dms-migration-summary.png)

## <a name="run-the-migration"></a>Uruchamianie migracji

* Wybierz polecenie **Uruchom migrację**.

  Zostanie wyświetlone okno działania migracji, a **stan** działania to **inicjowanie**.

## <a name="monitor-the-migration"></a>Monitorowanie migracji

1. Na ekranie działania migracji wybieraj polecenie **Odśwież**, aby zaktualizować ekran, aż do momentu, gdy **Stan** migracji zmieni się na **Uruchomiono**.

     ![Stan działania — uruchomione](media/tutorial-oracle-azure-postgresql-online/dms-activity-running.png)

2. W **obszarze Nazwa bazy** danych wybierz określoną bazę danych, aby uzyskać stan migracji dla operacji Pełne **ładowanie** danych i **Przyrostowa synchronizacja** danych.

    Operacja Pełne ładowanie danych wyświetli stan migracji ładowania początkowego, natomiast operacja Przyrostowa synchronizacja danych wyświetli stan przechwytywania zmian danych (CDC).

     ![Stan działania — ukończono pełne ładowanie](media/tutorial-oracle-azure-postgresql-online/dms-activity-full-load-completed.png)

     ![Stan działania — przyrostowa synchronizacja danych](media/tutorial-oracle-azure-postgresql-online/dms-activity-incremental-data-sync.png)

## <a name="perform-migration-cutover"></a>Przeprowadzanie migracji jednorazowej

Po zakończeniu początkowego pełnego ładowania bazy danych są oznaczone jako **Gotowe do migracji jednorazowej**.

1. Gdy wszystko jest gotowe do zakończenia migracji bazy danych, wybierz pozycję **Rozpocznij migrację jednorazową**.

2. Upewnij się, że zatrzymano wszystkie transakcje przychodzące do źródłowej bazy danych. Poczekaj, aż licznik **Zmiany oczekujące** będzie pokazywać wartość **0**.

   ![Rozpoczynanie migracji jednorazowej](media/tutorial-oracle-azure-postgresql-online/dms-start-cutover.png)

3. Wybierz polecenie **Potwierdź**, a następnie wybierz pozycję **Zastosuj**.
4. Gdy stan migracji bazy danych ma **wartość Ukończono,** połącz aplikacje z nowym docelowym Azure Database for PostgreSQL wystąpieniem.

 > [!NOTE]
 > Ponieważ tabela PostgreSQL domyślnie ma małe litery schema.table.column, możesz przywrócić małe litery, używając skryptu z sekcji Konfigurowanie schematu w programie **Azure Database for PostgreSQL** we wcześniejszej części tego artykułu.

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać informacje o znanych problemach i ograniczeniach związanych z wykonywaniem migracji w trybie online do usługi Azure Database for PostgreSQL, zobacz artykuł [Znane problemy i obejścia problemów związanych z migracjami online usługi Azure Database for PostgreSQL](known-issues-azure-postgresql-online.md).
* Aby uzyskać informacje o Azure Database Migration Service, zobacz artykuł [Co to jest Azure Database Migration Service?](./dms-overview.md).
* Aby uzyskać informacje Azure Database for PostgreSQL, zobacz artykuł Co to jest [Azure Database for PostgreSQL?](../postgresql/overview.md).
