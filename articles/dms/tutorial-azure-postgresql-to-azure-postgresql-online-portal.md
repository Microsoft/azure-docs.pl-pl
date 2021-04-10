---
title: 'Samouczek: Migrowanie usługi Azure DB for PostgreSQL do usługi Azure DB for PostgreSQL online za pośrednictwem Azure Portal'
titleSuffix: Azure Database Migration Service
description: Dowiedz się, jak przeprowadzić migrację online z jednej bazy danych platformy Azure do PostgreSQL do innej Azure Database for PostgreSQL przy użyciu Azure Database Migration Service za pośrednictwem Azure Portal.
services: dms
author: arunkumarthiags
ms.author: arthiaga
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 07/21/2020
ms.openlocfilehash: 996523d507f0a4f2850e936df39a38769bc47cde
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101091304"
---
# <a name="tutorial-migrateupgrade-azure-db-for-postgresql---single-server-to-azure-db-for-postgresql---single-server--online-using-dms-via-the-azure-portal"></a>Samouczek: Migrowanie/uaktualnianie usługi Azure DB dla PostgreSQL — jeden serwer do usługi Azure DB dla PostgreSQL — pojedynczy serwer online przy użyciu systemu DMS za pośrednictwem Azure Portal

Za pomocą Azure Database Migration Service można migrować bazy danych z wystąpienia [jednego serwera Azure Database for PostgreSQLgo](../postgresql/overview.md#azure-database-for-postgresql---single-server) do tej samej lub innej wersji Azure Database for PostgreSQL-pojedynczego wystąpienia serwera lub Azure Database for PostgreSQL-elastycznego serwera o minimalnym przestoju. W tym samouczku przeprowadzisz migrację przykładowej bazy danych **najmu dysku DVD** z Azure Database for PostgreSQL v10 do Azure Database for PostgreSQL-pojedynczego serwera przy użyciu działania migracji online w Azure Database Migration Service.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
>
> * Migruj Przykładowy schemat przy użyciu narzędzia pg_dump.
> * Utwórz wystąpienie usługi Azure Database Migration Service.
> * Utwórz projekt migracji w Azure Database Migration Service.
> * Uruchamianie migracji.
> * Monitoruj migrację.
> * Przeprowadź migrację uruchomienie produkcyjne.

> [!NOTE]
> Użycie Azure Database Migration Service do przeprowadzenia migracji w trybie online wymaga utworzenia wystąpienia na podstawie warstwy cenowej Premium. Szyfrujemy dysk, aby zapobiec kradzieży danych podczas procesu migracji

> [!IMPORTANT]
> Aby zapewnić optymalne środowisko migracji, firma Microsoft zaleca Tworzenie wystąpienia Azure Database Migration Service w tym samym regionie świadczenia usługi Azure jako docelowej bazie danych. Przenoszenie danych między regionami lub lokalizacjami geograficznymi może spowalniać proces migracji i powodować błędy.

> [!IMPORTANT]
> Migracja z Azure Database for PostgreSQL jest obsługiwana dla PostgreSQL w wersji 10 i nowszych. Za pomocą tego samouczka można także przeprowadzić migrację z jednego wystąpienia Azure Database for PostgreSQL do innego wystąpienia Azure Database for PostgreSQL lub funkcji Citus).

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka niezbędne są następujące elementy:

* Sprawdź [stan scenariuszy migracji obsługiwanych przez Azure Database Migration Service](./resource-scenario-status.md) dla obsługiwanych migracji i kombinacji wersji. 
* Istniejące [Azure Database for PostgreSQL](../postgresql/index.yml) w wersji 10 i nowszych przy użyciu bazy danych **wynajmu DVD** . 

    Należy również pamiętać, że wersja Azure Database for PostgreSQL docelowej musi być równa lub późniejsza niż lokalna wersja PostgreSQL. Na przykład PostgreSQL 10 można migrować do Azure Database for PostgreSQL 10 lub 11, ale nie do Azure Database for PostgreSQL 9,6.

* [Utwórz serwer Azure Database for PostgreSQL](../postgresql/quickstart-create-server-database-portal.md) lub [Utwórz serwer Azure Database for PostgreSQL-Citus](../postgresql/quickstart-create-hyperscale-portal.md) jako docelowy serwer baz danych, aby zmigrować dane do programu.
* Utwórz Microsoft Azure Virtual Network dla Azure Database Migration Service przy użyciu modelu wdrażania Azure Resource Manager. Aby uzyskać więcej informacji na temat tworzenia sieci wirtualnej, zapoznaj się z [dokumentacją Virtual Network](../virtual-network/index.yml), a w szczególności artykuły szybkiego startu z szczegółowymi szczegółami.

* Upewnij się, że reguły sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń) dla sieci wirtualnej nie blokują portu wychodzącego 443 z ServiceTag dla ServiceBus, Storage i AzureMonitor. Aby uzyskać więcej szczegółów na temat filtrowania ruchu sieciowej grupy zabezpieczeń w sieci wirtualnej, zobacz artykuł [Filtrowanie ruchu sieciowego przy użyciu sieciowych grup zabezpieczeń](../virtual-network/virtual-network-vnet-plan-design-arm.md).
* Utwórz [regułę zapory](../azure-sql/database/firewall-configure.md) na poziomie serwera dla źródła Azure Database for PostgreSQL, aby umożliwić Azure Database Migration Service dostęp do źródłowych baz danych. Podaj zakres podsieci sieci wirtualnej używanej na potrzeby Azure Database Migration Service.
* Utwórz [regułę zapory](../azure-sql/database/firewall-configure.md) na poziomie serwera dla elementu docelowego Azure Database for PostgreSQL, aby umożliwić Azure Database Migration Service dostęp do docelowych baz danych. Podaj zakres podsieci sieci wirtualnej używanej na potrzeby Azure Database Migration Service.
* [Włącz replikację logiczną](../postgresql/concepts-logical.md) w źródle usługi Azure DB for PostgreSQL. 
* Ustaw następujące parametry serwera w wystąpieniu Azure Database for PostgreSQL używanym jako Źródło:

  * max_replication_slots = [liczba gniazd], zalecamy ustawienie na **dziesięć gniazd**
  * max_wal_senders = [liczba współbieżnych zadań] — parametr max_wal_senders ustawia liczbę współbieżnych zadań, które można uruchomić, przy czym zalecane ustawienie to **10 zadań**

> [!NOTE]
> Powyższe parametry serwera są statyczne i wymagają ponownego uruchomienia wystąpienia Azure Database for PostgreSQL, aby zaczęły obowiązywać. Aby uzyskać więcej informacji na temat przełączania parametrów serwera, zobacz [konfigurowanie Azure Database for PostgreSQL Server Parameters](../postgresql/howto-configure-server-parameters-using-portal.md).

> [!IMPORTANT]
> Wszystkie tabele w istniejącej bazie danych muszą mieć klucz podstawowy, aby upewnić się, że zmiany można synchronizować z docelową bazą danych.

## <a name="migrate-the-sample-schema"></a>Migrowanie przykładowego schematu

Aby utworzyć wszystkie obiekty bazy danych, takie jak schematy tabel, indeksy oraz procedury składowane, należy wyodrębnić schemat ze źródłowej bazy danych i zastosować go w bazie danych.

1. Użyj polecenia -s pg_dump, aby utworzyć plik zrzutu schematu dla bazy danych.

    ```
    pg_dump -o -h hostname -U db_username -d db_name -s > your_schema.sql
    ```

    Na przykład, aby utworzyć plik zrzutu schematu dla bazy danych **dvdrental** :

    ```
    pg_dump -o -h mypgserver-source.postgres.database.azure.com -U pguser@mypgserver-source -d dvdrental -s -O -x > dvdrentalSchema.sql
    ```

    Aby uzyskać więcej informacji na temat korzystania z narzędzia pg_dump, zobacz przykłady w samouczku narzędzia [pg-dump](https://www.postgresql.org/docs/9.6/static/app-pgdump.html#PG-DUMP-EXAMPLES).

2. Utwórz pustą bazę danych w swoim środowisku docelowym, czyli w usłudze Azure Database for PostgreSQL.

    Aby uzyskać szczegółowe informacje na temat nawiązywania połączenia i tworzenia bazy danych, zobacz artykuł [Tworzenie serwera Azure Database for PostgreSQL w Azure Portal](../postgresql/quickstart-create-server-database-portal.md) lub [Tworzenie serwera Azure Database for PostgreSQL-Citus w Azure Portal](../postgresql/quickstart-create-hyperscale-portal.md).

    > [!NOTE]
    > Wystąpienie Azure Database for PostgreSQL-Citus) ma tylko jedną bazę danych: **Citus**.

3. Zaimportuj schemat do docelowej bazy danych utworzonej przez przywrócenie pliku zrzutu schematu.

    ```
    psql -h hostname -U db_username -d db_name < your_schema.sql
    ```

    Na przykład:

    ```
    psql -h mypgserver-source.postgres.database.azure.com  -U pguser@mypgserver-source -d dvdrental citus < dvdrentalSchema.sql
    ```

4. Aby wyodrębnić skrypt klucza obcego i dodać go do miejsca docelowego (Azure Database for PostgreSQL), w PgAdmin lub PSQL, uruchom następujący skrypt.

   > [!IMPORTANT]
   > Klucze obce w schemacie spowodują, że wstępne ładowanie i ciągła synchronizacja migracji zakończą się niepowodzeniem.

    ```
    SELECT Q.table_name
        ,CONCAT('ALTER TABLE ','"', table_schema,'"', '.','"', table_name ,'"', STRING_AGG(DISTINCT CONCAT(' DROP CONSTRAINT ','"', foreignkey,'"'), ','), ';') as DropQuery
            ,CONCAT('ALTER TABLE ','"', table_schema,'"', '.','"', table_name,'"', STRING_AGG(DISTINCT CONCAT(' ADD CONSTRAINT ','"', foreignkey,'"', ' FOREIGN KEY (','"', column_name,'"', ')', ' REFERENCES ','"', foreign_table_schema,'"', '.','"', foreign_table_name,'"', '(','"', foreign_column_name,'"', ')',' ON UPDATE ',update_rule,' ON DELETE ',delete_rule), ','), ';') as AddQuery
    FROM
        (SELECT
        S.table_schema,
        S.foreignkey,
        S.table_name,
        STRING_AGG(DISTINCT S.column_name, ',') AS column_name,
        S.foreign_table_schema,
        S.foreign_table_name,
        STRING_AGG(DISTINCT S.foreign_column_name, ',') AS foreign_column_name,
        S.update_rule,
        S.delete_rule
    FROM
        (SELECT DISTINCT
        tc.table_schema,
        tc.constraint_name AS foreignkey,
        tc.table_name,
        kcu.column_name,
        ccu.table_schema AS foreign_table_schema,
        ccu.table_name AS foreign_table_name,
        ccu.column_name AS foreign_column_name,
        rc.update_rule AS update_rule,
        rc.delete_rule AS delete_rule
        FROM information_schema.table_constraints AS tc
        JOIN information_schema.key_column_usage AS kcu ON tc.constraint_name = kcu.constraint_name AND tc.table_schema = kcu.table_schema
        JOIN information_schema.constraint_column_usage AS ccu ON ccu.constraint_name = tc.constraint_name AND ccu.table_schema = tc.table_schema
        JOIN information_schema.referential_constraints as rc ON rc.constraint_name = tc.constraint_name AND rc.constraint_schema = tc.table_schema
    WHERE constraint_type = 'FOREIGN KEY'
        ) S
        GROUP BY S.table_schema, S.foreignkey, S.table_name, S.foreign_table_schema, S.foreign_table_name,S.update_rule,S.delete_rule
        ) Q
        GROUP BY Q.table_schema, Q.table_name;
    ```

5. Uruchom docelowy klucz obcy (znajduje się w drugiej kolumnie) w wyniku zapytania.

6. Aby wyłączyć Wyzwalacze w docelowej bazie danych, uruchom poniższy skrypt.

   > [!IMPORTANT]
   > Wyzwalacze (INSERT lub Update) w danych wymuszają integralność danych w miejscu docelowym przed danymi replikowanymi ze źródła. W związku z tym zaleca się wyłączenie wyzwalaczy we wszystkich tabelach w **miejscu docelowym** podczas migracji, a następnie ponowne włączenie wyzwalaczy po zakończeniu migracji.

    ```
    SELECT DISTINCT CONCAT('ALTER TABLE ', event_object_schema, '.', event_object_table, ' DISABLE TRIGGER ', trigger_name, ';')
    FROM information_schema.triggers
    ```

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Rejestrowanie dostawcy zasobów Microsoft.DataMigration

1. Zaloguj się w witrynie Azure Portal, wybierz pozycję **Wszystkie usługi**, a następnie wybierz pozycję **Subskrypcje**.

   ![Wyświetlanie subskrypcji w portalu](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/portal-select-subscriptions.png)

2. Wybierz subskrypcję, w której chcesz utworzyć wystąpienie Azure Database Migration Service, a następnie wybierz pozycję **dostawcy zasobów**.

    ![Wyświetlanie dostawców zasobów](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/portal-select-resource-provider.png)

3. Wyszukaj pozycję migracja, a następnie po prawej stronie **programu Microsoft. datamigration** wybierz pozycję **zarejestruj**.

    ![Rejestrowanie dostawcy zasobów](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/portal-register-resource-provider.png)

## <a name="create-a-dms-instance"></a>Tworzenie wystąpienia usługi DMS

1. W witrynie Azure Portal wybierz pozycję + **Utwórz zasób**, wyszukaj usługę Azure Database Migration Service, a następnie wybierz usługę **Azure Database Migration Service** na liście rozwijanej.

    ![Azure Marketplace](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/portal-marketplace.png)

2. Na ekranie **Azure Database Migration Service** wybierz polecenie **Utwórz**.

    ![Tworzenie wystąpienia usługi Azure Database Migration Service](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-create1.png)
  
3. Na ekranie **Tworzenie usługi migracji** Określ nazwę, subskrypcję, nową lub istniejącą grupę zasobów oraz lokalizację usługi.

4. Wybierz istniejącą sieć wirtualną lub Utwórz nową.

    Sieć wirtualna zapewnia Azure Database Migration Service z dostępem do źródłowego serwera PostgreSQL i docelowego wystąpienia Azure Database for PostgreSQL.

    Aby uzyskać więcej informacji na temat sposobu tworzenia sieci wirtualnej w Azure Portal, zobacz artykuł [Tworzenie sieci wirtualnej przy użyciu Azure Portal](../virtual-network/quick-create-portal.md).

5. Wybierz warstwę cenową.

    Aby uzyskać więcej informacji na temat kosztów i warstw cenowych, zobacz [stronę z cennikiem](https://aka.ms/dms-pricing).

    ![Konfigurowanie ustawień wystąpienia usługi Azure Database Migration Service](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-settings4.png)

6. Wybierz pozycję **Przegląd + Utwórz** , aby utworzyć usługę.

   Tworzenie usługi zakończy się w ciągu około 10 do 15 minut.

## <a name="create-a-migration-project"></a>Tworzenie projektu migracji

Po utworzeniu usługi znajdź ją w witrynie Azure Portal, otwórz ją, a następnie utwórz nowy projekt migracji.

1. W witrynie Azure Portal wybierz pozycję **Wszystkie usługi**, wyszukaj usługę Azure Database Migration Service, a następnie wybierz pozycję **Azure Database Migration Services**.

      ![Znajdź wszystkie wystąpienia Azure Database Migration Service](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-search.png)

2. Na ekranie **usługi Azure Database Migration Services** Wyszukaj nazwę utworzonego wystąpienia Azure Database Migration Service, wybierz wystąpienie, a następnie wybierz pozycję + **Nowy projekt migracji**.

3. Na ekranie **Nowy projekt migracji** Określ nazwę projektu, w polu tekstowym **Typ serwera źródłowego** wybierz pozycję **PostgreSQL**, w polu tekstowym **Typ serwera docelowego** wybierz pozycję **Azure Database for PostgreSQL**.
    > [!NOTE]
    > Wybierz pozycję **PostgreSQL** na **serwerze źródłowym** , mimo że serwer źródłowy jest wystąpieniem **Azure Database for PostgreSQL** .  

4. W sekcji **Wybierz typ działania** wybierz pozycję **migracja danych w trybie online**.

    ![Utwórz projekt Azure Database Migration Service](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-create-project.png)

    > [!NOTE]
    > Alternatywnie możesz wybrać opcję **Utwórz projekt tylko** w celu utworzenia projektu migracji, a następnie wykonać migrację później.

5. Wybierz pozycję **Zapisz**, zwróć uwagę na wymagania, aby pomyślnie użyć Azure Database Migration Service do migracji danych, a następnie wybierz pozycję **Utwórz i uruchom działanie**.

## <a name="specify-source-details"></a>Określanie szczegółów źródła

1. Na ekranie **Dodaj szczegóły źródła** Określ szczegóły połączenia dla wystąpienia źródła PostgreSQL.

    ![Ekran Dodawanie szczegółów źródła](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-add-source-details.png)

    > [!NOTE]
    > Szczegółowe informacje, takie jak "Nazwa serwera", "Port serwera", "Nazwa bazy danych" itp., można znaleźć w portalu **Azure Database for PostgreSQL** .

2. Wybierz pozycję **Zapisz**.

## <a name="specify-target-details"></a>Określanie szczegółów elementu docelowego

1. Na ekranie **szczegóły elementu docelowego** Określ szczegóły połączenia dla serwera docelowego Citus, który jest wstępnie zainicjowanym wystąpieniem funkcji Citus, do którego wdrożono schemat **wynajmu dysku DVD** przy użyciu pg_dump.

    ![Ekran Szczegóły elementu docelowego](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-add-target-details.png)

    > [!NOTE]
    > Można przeprowadzić migrację z wystąpienia Azure Database for PostgreSQL do innego wystąpienia Azure Database for PostgreSQL jednego serwera lub do serwera Citus.

2. Wybierz polecenie **Zapisz**, a następnie na ekranie **Mapuj do docelowych baz danych**, określ mapowanie źródłowej i docelowej bazy danych na potrzeby migracji.

    Jeśli docelowa baza danych zawiera tę samą nazwę bazy danych co źródłowa baza danych, Azure Database Migration Service domyślnie wybiera docelową bazę danych.

    ![Ekran mapowania do docelowej bazy danych](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-map-target-databases.png)

3. Wybierz pozycję **Zapisz**, a następnie na ekranie **Ustawienia migracji** zaakceptuj wartości domyślne.

    ![Ekran ustawień migracji](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-migration-settings.png)

4. Wybierz pozycję **Zapisz**. Na ekranie **Podsumowanie migracji** w polu tekstowym **Nazwa działania** określ nazwę działania migracji, a następnie sprawdź podsumowanie, aby upewnić się, że szczegółowe informacje źródłowe i docelowe pasują do wcześniej podanych.

    ![Ekran podsumowania migracji](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-migration-summary.png)

## <a name="run-the-migration"></a>Uruchamianie migracji

* Wybierz polecenie **Uruchom migrację**.

Zostanie wyświetlone okno działanie migracji, a **stan** działania powinna zostać zaktualizowana, aby pokazać **Tworzenie kopii zapasowej w toku**. Podczas uaktualniania z usługi Azure DB dla PostgreSQL 9,5 lub 9,6 może wystąpić następujący błąd:

**Scenariusz zgłosił nieznany błąd. 28000: brak wpisu pg_hba. conf dla połączenia replikacji z hosta "40.121.141.121", użytkownik "SR"**

Wynika to z faktu, że PostgreSQL nie ma odpowiednich uprawnień do tworzenia wymaganych artefaktów replikacji logicznej. Aby włączyć wymagane uprawnienia, można wykonać następujące czynności:

1. Otwórz ustawienia "zabezpieczenia połączeń" dla serwera źródłowego usługi Azure DB for PostgreSQL, z którego próbujesz migrować/uaktualnić.
2. Dodaj nową regułę zapory z nazwą kończącą się ciągiem "_replrule" i Dodaj adres IP z komunikatu o błędzie do pól początkowy adres IP i końcowy adres IP. Dla powyższego przykładu błędu —
> Nazwa reguły zapory = sr_replrule; Początkowy adres IP = 40.121.141.121; Końcowy adres IP = 40.121.141.121

3. Kliknij przycisk Zapisz i pozwól na zakończenie zmiany. 
4. Działanie retry DMS. 

## <a name="monitor-the-migration"></a>Monitorowanie migracji

1. Na ekranie działania migracji wybieraj polecenie **Odśwież**, aby zaktualizować ekran, aż do momentu, gdy **Stan** migracji zmieni się na **Ukończono**.

     ![Monitorowanie procesu migracji](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-monitor-migration.png)

2. Po zakończeniu migracji w obszarze **Nazwa bazy danych** Wybierz konkretną bazę danych, aby przejść do stanu migracji **pełnych danych** i **przyrostowych operacji synchronizacji danych** .

   > [!NOTE]
   > **Pełne ładowanie danych** pokazuje stan początkowej migracji ładowania, podczas gdy **Synchronizacja danych przyrostowych** pokazuje stan funkcji przechwytywania zmian danych.

     ![Pełne szczegóły ładowania danych](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-full-data-load-details.png)

     ![Szczegóły synchronizacji danych przyrostowych](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-incremental-data-sync-details.png)

## <a name="perform-migration-cutover"></a>Przeprowadzanie migracji jednorazowej

Po zakończeniu początkowego pełnego ładowania bazy danych są oznaczone jako **Gotowe do migracji jednorazowej**.

1. Gdy wszystko jest gotowe do zakończenia migracji bazy danych, wybierz pozycję **Rozpocznij migrację jednorazową**.

2. Poczekaj, aż licznik **oczekujące zmiany** wyświetli **wartość 0** , aby upewnić się, że wszystkie przychodzące transakcje do źródłowej bazy danych zostaną zatrzymane, zaznacz pole wyboru **Potwierdź** , a następnie wybierz pozycję **Zastosuj**.

    ![Pełny ekran uruchomienie produkcyjne](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-complete-cutover.png)

3. Gdy stan migracji bazy danych jest **zakończony**, [Utwórz ponownie sekwencje](https://wiki.postgresql.org/wiki/Fixing_Sequences) (jeśli dotyczy) i Połącz swoje aplikacje z nowym docelowym wystąpieniem Azure Database for PostgreSQL.
 
> [!NOTE]
> Azure Database Migration Service można używać do przeprowadzania uaktualnień wersji głównych ze zmniejszonym przestojem na serwerze Azure Database for PostgreSQL-pojedynczym. Najpierw skonfiguruj docelową bazę danych z wymaganą wyższą wersją PostgreSQL, ustawieniami sieci i parametrami. Następnie można zainicjować migrację do docelowych baz danych, korzystając z procedury opisanej powyżej. Po uruchomienie produkcyjne do docelowego serwera bazy danych można zaktualizować parametry połączenia aplikacji, aby wskazywały docelowy serwer bazy danych. 

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać informacje o znanych problemach i ograniczeniach związanych z wykonywaniem migracji w trybie online do usługi Azure Database for PostgreSQL, zobacz artykuł [Znane problemy i obejścia problemów związanych z migracjami online usługi Azure Database for PostgreSQL](known-issues-azure-postgresql-online.md).
* Aby uzyskać informacje o usłudze Azure Database Migration Service, zobacz artykuł [Czym jest usługa Azure Database Migration Service?](./dms-overview.md).
* Aby uzyskać informacje na temat Azure Database for PostgreSQL, zobacz artykuł [co to jest Azure Database for PostgreSQL?](../postgresql/overview.md).
