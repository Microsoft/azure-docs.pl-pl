---
title: 'Samouczek: Migrowanie bazy danych Oracle online do Azure Database for PostgreSQL'
titleSuffix: Azure Database Migration Service
description: Dowiedz się, jak przeprowadzić migrację online z programu Oracle lokalnie lub na maszynach wirtualnych do Azure Database for PostgreSQL przy użyciu Azure Database Migration Service.
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
ms.openlocfilehash: ab280bbab71926a6ffb70605fdbf463164469623
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/18/2021
ms.locfileid: "101096353"
---
# <a name="tutorial-migrate-oracle-to-azure-database-for-postgresql-online-using-dms-preview"></a>Samouczek: Migrowanie bazy danych Oracle do Azure Database for PostgreSQL online przy użyciu usługi DMS (wersja zapoznawcza)

Za pomocą Azure Database Migration Service można migrować bazy danych z baz danych Oracle hostowanych lokalnie lub na maszynach wirtualnych w celu [Azure Database for PostgreSQL](../postgresql/index.yml) z minimalnym przestojem. Innymi słowy, można przeprowadzić migrację z minimalnym przestojem aplikacji. W tym samouczku przeprowadzisz migrację przykładowej bazy danych **HR** z wystąpienia programu Oracle 11g w środowisku lokalnym lub maszynie wirtualnej do Azure Database for PostgreSQL przy użyciu działania migracji w trybie online w programie Azure Database Migration Service.

Z tego samouczka dowiesz się, jak wykonywać następujące czynności:
> [!div class="checklist"]
>
> * Oceń wysiłki migracji przy użyciu narzędzia ora2pg.
> * Migruj Przykładowy schemat przy użyciu narzędzia ora2pg.
> * Utwórz wystąpienie usługi Azure Database Migration Service.
> * Utwórz projekt migracji przy użyciu Azure Database Migration Service.
> * Uruchamianie migracji.
> * Monitoruj migrację.

> [!NOTE]
> Użycie Azure Database Migration Service do przeprowadzenia migracji w trybie online wymaga utworzenia wystąpienia na podstawie warstwy cenowej Premium.

> [!IMPORTANT]
> Aby zapewnić optymalne środowisko migracji, firma Microsoft zaleca Tworzenie wystąpienia Azure Database Migration Service w tym samym regionie świadczenia usługi Azure jako docelowej bazie danych. Przenoszenie danych między regionami lub lokalizacjami geograficznymi może spowalniać proces migracji i powodować błędy.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

W tym artykule opisano sposób przeprowadzania migracji w trybie online z programu Oracle do Azure Database for PostgreSQL.

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka niezbędne są następujące elementy:

* Pobierz i zainstaluj [oprogramowanie Oracle 11g wersja 2 (Standard Edition, Standard Edition w wersji 1 lub Enterprise Edition)](https://www.oracle.com/technetwork/database/enterprise-edition/downloads/index.html).
* Pobierz przykładową bazę danych **kadr** z tego [miejsca](https://docs.oracle.com/database/121/COMSC/installation.htm#COMSC00002).
* Pobierz i [Zainstaluj program ora2pg w systemie Windows lub Linux](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Steps%20to%20Install%20ora2pg%20on%20Windows%20and%20Linux.pdf).
* [Utwórz wystąpienie usługi Azure Database for PostgreSQL](../postgresql/quickstart-create-server-database-portal.md).
* Połącz się z wystąpieniem i Utwórz bazę danych za pomocą instrukcji w tym [dokumencie](../postgresql/tutorial-design-database-using-azure-portal.md).
* Utwórz Microsoft Azure Virtual Network dla Azure Database Migration Service przy użyciu modelu wdrażania Azure Resource Manager, który zapewnia łączność między lokacjami z lokalnymi serwerami źródłowymi przy użyciu usługi [ExpressRoute](../expressroute/expressroute-introduction.md) lub [sieci VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md). Aby uzyskać więcej informacji na temat tworzenia sieci wirtualnej, zapoznaj się z [dokumentacją Virtual Network](../virtual-network/index.yml), a w szczególności artykuły szybkiego startu z szczegółowymi szczegółami.

  > [!NOTE]
  > Jeśli podczas konfigurowania sieci wirtualnej używasz usługi ExpressRoute z usługą Komunikacja równorzędna z firmą Microsoft, Dodaj następujące [punkty końcowe](../virtual-network/virtual-network-service-endpoints-overview.md) do podsieci, w której zostanie zainicjowana usługa:
  >
  > * Docelowy punkt końcowy bazy danych (na przykład punkt końcowy SQL, Cosmos DB punkt końcowy itd.)
  > * Punkt końcowy magazynu
  > * Punkt końcowy usługi Service Bus
  >
  > Ta konfiguracja jest konieczna, ponieważ Azure Database Migration Service brak łączności z Internetem.

* Upewnij się, że reguły grupy zabezpieczeń sieci wirtualnej (sieciowej grupy zabezpieczeń) nie blokują portu wychodzącego 443 z ServiceTag dla ServiceBus, Storage i AzureMonitor. Aby uzyskać więcej szczegółów na temat filtrowania ruchu sieciowej grupy zabezpieczeń w sieci wirtualnej, zobacz artykuł [Filtrowanie ruchu sieciowego przy użyciu sieciowych grup zabezpieczeń](../virtual-network/virtual-network-vnet-plan-design-arm.md).
* [Zapora sytemu Windows skonfigurowana pod kątem dostępu do aparatu bazy danych](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Otwórz Zaporę systemu Windows, aby umożliwić Azure Database Migration Service dostęp do źródłowego serwera Oracle, który domyślnie jest portem TCP 1521.
* W przypadku korzystania z urządzenia zapory przed źródłowymi bazami danych może zajść potrzeba dodania reguł zapory, aby umożliwić Azure Database Migration Service dostęp do źródłowych baz danych na potrzeby migracji.
* Utwórz [regułę zapory](../azure-sql/database/firewall-configure.md) na poziomie serwera dla Azure Database for PostgreSQL, aby umożliwić Azure Database Migration Service dostęp do docelowych baz danych. Podaj zakres podsieci sieci wirtualnej używanej na potrzeby Azure Database Migration Service.
* Włącz dostęp do źródłowych baz danych programu Oracle.

  > [!NOTE]
  > Rola DBA jest wymagana, aby użytkownik mógł nawiązać połączenie ze źródłem Oracle.

  * Archiwizowanie dzienników ponownego wykonywania jest wymagane do synchronizacji przyrostowej w Azure Database Migration Service do przechwytywania zmian danych. Wykonaj następujące kroki, aby skonfigurować źródło Oracle:
    * Zaloguj się przy użyciu uprawnienia SYSDBA, uruchamiając następujące polecenie:

      ```
      sqlplus (user)/(password) as sysdba
      ```

    * Zamknij wystąpienie bazy danych, uruchamiając następujące polecenie.

      ```
      SHUTDOWN IMMEDIATE;
      ```

      Poczekaj na potwierdzenie `'ORACLE instance shut down'` .

    * Uruchom nowe wystąpienie i zainstaluj (ale nie Otwórz) bazy danych, aby włączyć lub wyłączyć archiwizowanie komp uruchomione następujące polecenie:

      ```
      STARTUP MOUNT;
      ```

      Baza danych musi być zainstalowana; Zaczekaj na potwierdzenie "uruchomiono wystąpienie Oracle".

    * Zmień tryb archiwizowania bazy danych, uruchamiając następujące polecenie:

      ```
      ALTER DATABASE ARCHIVELOG;
      ```

    * Otwórz bazę danych w celu wykonywania normalnych operacji, uruchamiając następujące polecenie:

      ```
      ALTER DATABASE OPEN;
      ```

      Może być konieczne ponowne uruchomienie pliku ARC w celu wyświetlenia.

    * Aby sprawdzić, uruchom następujące polecenie:

      ```
      SELECT log_mode FROM v$database;
      ```

      Należy odebrać odpowiedź `'ARCHIVELOG'` . Jeśli odpowiedź to `'NOARCHIVELOG'` , to wymagania nie jest spełnione.

  * Włącz dodatkowe rejestrowanie dla replikacji przy użyciu jednej z następujących opcji.

    * **Opcja 1**.
      Zmień dodatkowe rejestrowanie na poziomie bazy danych, aby uwzględnić wszystkie tabele z klucz podstawowy i unikatowym indeksem. Zapytanie wykrywania zwróci wartość `'IMPLICIT'` .

      ```
      ALTER DATABASE ADD SUPPLEMENTAL LOG DATA (PRIMARY KEY, UNIQUE) COLUMNS;
      ```

      Zmień rejestrowanie uzupełniające na poziomie tabeli. Działa tylko w przypadku tabel, które mają manipulowanie danymi i nie mają PKs lub unikatowych indeksów.

      ```
      ALTER TABLE [TABLENAME] ADD SUPPLEMENTAL LOG DATA (ALL) COLUMNS;
      ```

    * **Opcja 2**.
      Zmień dodatkowe rejestrowanie na poziomie bazy danych, aby objęło wszystkie tabele, i zapytanie wykrywania zwraca `'YES'` .

      ```
      ALTER DATABASE ADD SUPPLEMENTAL LOG DATA;
      ```

      Zmień rejestrowanie uzupełniające na poziomie tabeli. Postępuj zgodnie z logiką poniżej, aby uruchomić tylko jedną instrukcję dla każdej tabeli.

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

    Aby sprawdzić, uruchom następujące polecenie:

      ```
      SELECT supplemental_log_data_min FROM v$database;
      ```

    Należy odebrać odpowiedź `'YES'` .

## <a name="assess-the-effort-for-an-oracle-to-azure-database-for-postgresql-migration"></a>Oceń wysiłki dla programu Oracle do Azure Database for PostgreSQL migracji

Zalecamy użycie ora2pg do oceny nakładu pracy wymaganego do migracji z programu Oracle do Azure Database for PostgreSQL. Za pomocą `ora2pg -t SHOW_REPORT` dyrektywy można utworzyć raport zawierający listę wszystkich obiektów programu Oracle, szacowany koszt migracji (w dniach deweloperskich) oraz niektóre obiekty bazy danych, które mogą wymagać specjalnej uwagi w ramach konwersji.

Większość klientów poświęca dużo czasu na zapoznanie się z raportem oceny i uwzględnieniem automatycznego i ręcznego nakładu pracy.

Aby skonfigurować i uruchomić program ora2pg w celu utworzenia raportu z oceny, zobacz sekcję **premigration: Assessment** w programie [Oracle do Azure Database for PostgreSQL Cookbook](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20PostgreSQL%20Migration%20Cookbook.pdf). Przykładowy raport oceny ora2pg jest dostępny w [tym miejscu](https://ora2pg.darold.net/report.html).

## <a name="export-the-oracle-schema"></a>Eksportowanie schematu programu Oracle

Zalecamy użycie ora2pg do konwersji schematu Oracle i innych obiektów Oracle (typy, procedury, funkcje itp.) do schematu, który jest zgodny z Azure Database for PostgreSQL. ora2pg zawiera wiele dyrektyw, które ułatwiają wstępne Definiowanie niektórych typów danych. Na przykład można użyć `DATA_TYPE` dyrektywy, aby zastąpić wszystkie cyfry (*, 0) wartością bigint, a nie wartością numeryczną (38).

Aby wyeksportować wszystkie obiekty bazy danych w plikach. SQL, można uruchomić ora2pg. Następnie można przejrzeć pliki. SQL przed zaimportowaniem ich do Azure Database for PostgreSQL przy użyciu PSQL lub można wykonać. Skrypt SQL w PgAdmin.

```
psql -f [FILENAME] -h [AzurePostgreConnection] -p 5432 -U [AzurePostgreUser] -d database 
```

Na przykład:

```
psql -f %namespace%\schema\sequences\sequence.sql -h server1-server.postgres.database.azure.com -p 5432 -U username@server1-server -d database
```

Aby skonfigurować i uruchomić ora2pg na potrzeby konwersji schematu, zobacz sekcję **migracja: schemat i dane** w programie [Oracle do Azure Database for PostgreSQL Cookbook](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20PostgreSQL%20Migration%20Cookbook.pdf).

## <a name="set-up-the-schema-in-azure-database-for-postgresql"></a>Skonfiguruj schemat w Azure Database for PostgreSQL

Można przekonwertować schematy tabel programu Oracle, procedury składowane, pakiety i inne obiekty bazy danych, aby Postgres je z użyciem ora2pg przed rozpoczęciem potoku migracji w Azure Database Migration Service. Zobacz poniższe linki, aby dowiedzieć się, jak korzystać z usługi ora2pg:

* [Zainstaluj program ora2pg w systemie Windows](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Steps%20to%20Install%20ora2pg%20on%20Windows%20and%20Linux.pdf)
* [PostgreSQL migracji oprogramowania Oracle na platformę Azure Cookbook](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20PostgreSQL%20Migration%20Cookbook.pdf)

Azure Database Migration Service można również utworzyć schemat tabeli PostgreSQL. Usługa uzyskuje dostęp do schematu tabeli w połączonym źródle Oracle i tworzy zgodny schemat tabeli w Azure Database for PostgreSQL. Pamiętaj, aby sprawdzić poprawność i sprawdzić format schematu w Azure Database for PostgreSQL po Azure Database Migration Service zakończeniu tworzenia schematu i przeniesieniu danych.

> [!IMPORTANT]
> Azure Database Migration Service tworzy tylko schemat tabeli; inne obiekty bazy danych, takie jak procedury składowane, pakiety, indeksy itp., nie są tworzone.

Pamiętaj również, aby porzucić klucz obcy w docelowej bazie danych, aby pełne ładowanie zostało uruchomione. Zapoznaj się z sekcją **Migrowanie przykładowego schematu** w [tym](./tutorial-postgresql-azure-postgresql-online.md) artykule, aby zapoznać się z skryptem, którego można użyć do porzucenia klucza obcego. Użyj Azure Database Migration Service, aby przeprowadzić pełne ładowanie i synchronizację.

### <a name="when-the-postgresql-table-schema-already-exists"></a>Gdy schemat tabeli PostgreSQL już istnieje

Jeśli utworzysz schemat PostgreSQL przy użyciu narzędzi takich jak ora2pg przed rozpoczęciem przenoszenia danych przy użyciu Azure Database Migration Service, zamapuj tabele źródłowe na tabele docelowe w Azure Database Migration Service.

1. W przypadku tworzenia nowej bazy danych Oracle do Azure Database for PostgreSQL projekcie migracji zostanie wyświetlony monit o wybranie docelowego schematu i docelowego środowiska w kroku wybierz schematy. Wypełnij docelową bazę danych i schemat docelowy.

   ![Zrzut ekranu przedstawia mapowanie do docelowych baz danych.](media/tutorial-oracle-azure-postgresql-online/dms-map-to-target-databases.png)

2. Ekran **Ustawienia migracji** przedstawia listę tabel w źródle danych Oracle. Azure Database Migration Service próbuje dopasować tabele w źródle i tabelach docelowych na podstawie nazwy tabeli. Jeśli istnieje wiele pasujących tabel docelowych z inną wielkością liter, można wybrać tabelę docelową do mapowania.

    ![Zrzut ekranu przedstawia ustawienia migracji.](media/tutorial-oracle-azure-postgresql-online/dms-migration-settings.png)

> [!NOTE]
> Jeśli zachodzi potrzeba mapowania nazw tabel źródłowych do tabel o różnych nazwach, wiadomości e-mail [dmsfeedback@microsoft.com](mailto:dmsfeedbac@microsoft.com) i możemy udostępnić skrypt służący do automatyzowania procesu.

### <a name="when-the-postgresql-table-schema-doesnt-exist"></a>Gdy schemat tabeli PostgreSQL nie istnieje

Jeśli docelowa baza danych PostgreSQL nie zawiera żadnych informacji o schemacie tabeli, Azure Database Migration Service konwertuje schemat źródłowy i tworzy go ponownie w docelowej bazie danych. Należy pamiętać, Azure Database Migration Service tworzy tylko schemat tabeli, a nie inne obiekty bazy danych, takie jak procedury składowane, pakiety i indeksy.
Aby Azure Database Migration Service utworzyć schemat dla Ciebie, upewnij się, że środowisko docelowe zawiera schemat bez istniejących tabel. Jeśli Azure Database Migration Service odnajduje dowolną tabelę, usługa zakłada, że schemat został utworzony za pomocą zewnętrznego narzędzia, takiego jak ora2pg.

> [!IMPORTANT]
> Azure Database Migration Service wymaga, aby wszystkie tabele zostały utworzone w taki sam sposób, przy użyciu Azure Database Migration Service lub narzędzia, takiego jak ora2pg, ale nie obu.

Aby rozpocząć:

1. Utwórz schemat w docelowej bazie danych zgodnie z wymaganiami dotyczącymi aplikacji. Domyślnie nazwy kolumn i schematu tabeli PostgreSQL są mniejsze. Z drugiej strony, schemat i kolumny tabeli programu Oracle są domyślnie pisane wielkimi literami.
2. W kroku wybierz schematy Określ docelową bazę danych i schemat docelowy.
3. W oparciu o schemat utworzony w Azure Database for PostgreSQL, Azure Database Migration Service używa następujących reguł przekształcania:

    Jeśli nazwa schematu w źródle Oracle i pasuje do tego w Azure Database for PostgreSQL, wówczas Azure Database Migration Service *tworzy schemat tabeli przy użyciu tego samego przypadku jak w elemencie docelowym*.

    Na przykład:

    | Źródłowy schemat programu Oracle | Docelowa baza danych PostgreSQL. schemat | Usługa DMS utworzyła schemat. tabela. kolumna |
    | ------------- | ------------- | ------------- |
    | HR | targetHR. Public | public.countries.country_id |
    | HR | targetHR.trgthr | trgthr.countries.country_id |
    | HR | targetHR.TARGETHR | "TARGETHR"." KRAJE "." COUNTRY_ID " |
    | HR | targetHR.HR | "HR". " KRAJE "." COUNTRY_ID " |
    | HR | targetHR.Hr | * Nie można zmapować przypadków mieszanych |

    * Aby utworzyć mieszany schemat przypadków i nazwy tabel w docelowym PostgreSQL, skontaktuj się z firmą [dmsfeedback@microsoft.com](mailto:dmsfeedback@microsoft.com) . Możemy udostępnić skrypt pozwalający skonfigurować mieszany schemat tabeli przypadków w docelowej bazie danych PostgreSQL.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Rejestrowanie dostawcy zasobów Microsoft.DataMigration

1. Zaloguj się w witrynie Azure Portal, wybierz pozycję **Wszystkie usługi**, a następnie wybierz pozycję **Subskrypcje**.

   ![Wyświetlanie subskrypcji w portalu](media/tutorial-oracle-azure-postgresql-online/portal-select-subscriptions.png)

2. Wybierz subskrypcję, w której chcesz utworzyć wystąpienie usługi Azure Database Migration Service, a następnie wybierz pozycję **Dostawcy zasobów**.

    ![Wyświetlanie dostawców zasobów](media/tutorial-oracle-azure-postgresql-online/portal-select-resource-provider.png)

3. Wyszukaj pozycję migracja, a następnie po prawej stronie **programu Microsoft. datamigration** wybierz pozycję **zarejestruj**.

    ![Rejestrowanie dostawcy zasobów](media/tutorial-oracle-azure-postgresql-online/portal-register-resource-provider.png)

## <a name="create-a-dms-instance"></a>Tworzenie wystąpienia usługi DMS

1. W witrynie Azure Portal wybierz pozycję + **Utwórz zasób**, wyszukaj usługę Azure Database Migration Service, a następnie wybierz usługę **Azure Database Migration Service** na liście rozwijanej.

    ![Azure Marketplace](media/tutorial-oracle-azure-postgresql-online/portal-marketplace.png)

2. Na ekranie **Azure Database Migration Service** wybierz polecenie **Utwórz**.

    ![Tworzenie wystąpienia usługi Azure Database Migration Service](media/tutorial-oracle-azure-postgresql-online/dms-create2.png)
  
3. Na ekranie **Tworzenie usługi migracji** określ nazwę usługi, subskrypcję oraz nową lub istniejącą grupę zasobów.

4. Wybierz istniejącą sieć wirtualną lub Utwórz nową.

    Sieć wirtualna zapewnia Azure Database Migration Service z dostępem do źródłowej bazy danych Oracle i docelowego wystąpienia Azure Database for PostgreSQL.

    Aby uzyskać więcej informacji na temat sposobu tworzenia sieci wirtualnej w Azure Portal, zobacz artykuł [Tworzenie sieci wirtualnej przy użyciu Azure Portal](../virtual-network/quick-create-portal.md).

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
4. Na ekranie **Nowy projekt migracji** Określ nazwę projektu, w polu tekstowym **Typ serwera źródłowego** wybierz pozycję **Oracle**, w polu tekstowym **Typ serwera docelowego** wybierz pozycję **Azure Database for PostgreSQL**.
5. W sekcji **Wybierz typ działania** wybierz pozycję **migracja danych w trybie online**.

   ![Tworzenie projektu usługi Database Migration Service](media/tutorial-oracle-azure-postgresql-online/dms-create-project5.png)

   > [!NOTE]
   > Alternatywnie możesz wybrać opcję **Utwórz projekt tylko** w celu utworzenia projektu migracji, a następnie wykonać migrację później.

6. Wybierz pozycję **Zapisz**. Zanotuj wymagania, aby pomyślnie użyć Azure Database Migration Service do przeprowadzenia migracji w trybie online, a następnie wybierz pozycję **Utwórz i uruchom działanie**.

## <a name="specify-source-details"></a>Określanie szczegółów źródła

* Na ekranie **Dodaj szczegóły źródła** Określ szczegóły połączenia dla wystąpienia źródłowego programu Oracle.

  ![Ekran Dodawanie szczegółów źródła](media/tutorial-oracle-azure-postgresql-online/dms-add-source-details.png)

## <a name="upload-oracle-oci-driver"></a>Przekazywanie sterownika OCI firmy Oracle

1. Wybierz pozycję **Zapisz**, a następnie na ekranie **Zainstaluj sterownik OCI** Zaloguj się do konta Oracle i pobierz **instantclient-basiclite-windows.x64-12.2.0.1.0.zip** sterownika (37 128 586 bajtów) (suma kontrolna SHA1:865082268) z tego [miejsca](https://www.oracle.com/technetwork/topics/winx64soft-089540.html#ic_winx64_inst).
2. Pobierz sterownik do folderu udostępnionego.

   Upewnij się, że folder jest współużytkowany przy użyciu podanej nazwy użytkownika z minimalnym dostępem tylko do odczytu. Azure Database Migration Service dostępu do udziału i odczytuje z niego dane w celu przekazania sterownika OCI do platformy Azure przez personifikowanie określonej nazwy użytkownika.

   Określona nazwa użytkownika musi być kontem użytkownika systemu Windows.

   ![Instalacja sterownika OCI](media/tutorial-oracle-azure-postgresql-online/dms-oci-driver-install.png)

## <a name="specify-target-details"></a>Określanie szczegółów elementu docelowego

1. Wybierz pozycję **Zapisz**, a następnie na ekranie **szczegóły elementu docelowego** Określ szczegóły połączenia dla docelowego serwera Azure Database for PostgreSQL, który jest wstępnie zainicjowanym wystąpieniem Azure Database for PostgreSQL, do którego wdrożono schemat **HR** .

    ![Ekran Szczegóły elementu docelowego](media/tutorial-oracle-azure-postgresql-online/dms-add-target-details1.png)

2. Wybierz polecenie **Zapisz**, a następnie na ekranie **Mapuj do docelowych baz danych**, określ mapowanie źródłowej i docelowej bazy danych na potrzeby migracji.

    Jeśli docelowa baza danych zawiera tę samą nazwę bazy danych co źródłowa baza danych, Azure Database Migration Service domyślnie wybiera docelową bazę danych.

    ![Mapowanie do docelowych baz danych](media/tutorial-oracle-azure-postgresql-online/dms-map-target-details.png)

3. Wybierz pozycję **Zapisz**. Na ekranie **Podsumowanie migracji** w polu tekstowym **Nazwa działania** określ nazwę działania migracji, a następnie sprawdź podsumowanie, aby upewnić się, że szczegółowe informacje źródłowe i docelowe pasują do wcześniej podanych.

    ![Podsumowanie migracji](media/tutorial-oracle-azure-postgresql-online/dms-migration-summary.png)

## <a name="run-the-migration"></a>Uruchamianie migracji

* Wybierz polecenie **Uruchom migrację**.

  Zostanie wyświetlone okno działania migracji oraz **stan** **inicjowania** działania.

## <a name="monitor-the-migration"></a>Monitorowanie migracji

1. Na ekranie działania migracji wybieraj polecenie **Odśwież**, aby zaktualizować ekran, aż do momentu, gdy **Stan** migracji zmieni się na **Uruchomiono**.

     ![Stan działania — uruchomiony](media/tutorial-oracle-azure-postgresql-online/dms-activity-running.png)

2. W obszarze **Nazwa bazy danych** wybierz określoną bazę danych, aby przejść do stanu migracji **pełnych operacji ładowania danych** i **przyrostowej synchronizacji danych** .

    Operacja Pełne ładowanie danych wyświetli stan migracji ładowania początkowego, natomiast operacja Przyrostowa synchronizacja danych wyświetli stan przechwytywania zmian danych (CDC).

     ![Stan działania — ukończono pełne ładowanie](media/tutorial-oracle-azure-postgresql-online/dms-activity-full-load-completed.png)

     ![Stan działania — przyrostowa synchronizacja danych](media/tutorial-oracle-azure-postgresql-online/dms-activity-incremental-data-sync.png)

## <a name="perform-migration-cutover"></a>Przeprowadzanie migracji jednorazowej

Po zakończeniu początkowego pełnego ładowania bazy danych są oznaczone jako **Gotowe do migracji jednorazowej**.

1. Gdy wszystko jest gotowe do zakończenia migracji bazy danych, wybierz pozycję **Rozpocznij migrację jednorazową**.

2. Upewnij się, że zatrzymano wszystkie transakcje przychodzące do źródłowej bazy danych. Poczekaj, aż licznik **Zmiany oczekujące** będzie pokazywać wartość **0**.

   ![Rozpoczynanie migracji jednorazowej](media/tutorial-oracle-azure-postgresql-online/dms-start-cutover.png)

3. Wybierz polecenie **Potwierdź**, a następnie wybierz pozycję **Zastosuj**.
4. Gdy stan migracji bazy danych zostanie **zakończony**, Połącz swoje aplikacje z nowym docelowym wystąpieniem Azure Database for PostgreSQL.

 > [!NOTE]
 > Ponieważ domyślnie PostgreSQL ma wartość Schema. Table. Column w przypadku małych liter, można przywrócić z wielkich liter do małych liter, używając skryptu z sekcji **Konfigurowanie schematu w Azure Database for PostgreSQL** wcześniej w tym artykule.

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać informacje o znanych problemach i ograniczeniach związanych z wykonywaniem migracji w trybie online do usługi Azure Database for PostgreSQL, zobacz artykuł [Znane problemy i obejścia problemów związanych z migracjami online usługi Azure Database for PostgreSQL](known-issues-azure-postgresql-online.md).
* Aby uzyskać informacje na temat Azure Database Migration Service, zobacz artykuł [co to jest Azure Database Migration Service?](./dms-overview.md).
* Aby uzyskać informacje na temat Azure Database for PostgreSQL, zobacz artykuł [co to jest Azure Database for PostgreSQL?](../postgresql/overview.md).