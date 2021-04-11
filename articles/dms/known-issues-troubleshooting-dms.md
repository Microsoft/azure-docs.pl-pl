---
title: Typowe problemy — Azure Database Migration Service
description: Dowiedz się, jak rozwiązywać typowe znane problemy i błędy związane z używaniem Azure Database Migration Service.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: troubleshooting
ms.date: 02/20/2020
ms.openlocfilehash: ce53e8a77186f96801879e5c9d8f8c65809470d0
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105639802"
---
# <a name="troubleshoot-common-azure-database-migration-service-issues-and-errors"></a>Rozwiązywanie typowych problemów i błędów Azure Database Migration Service

W tym artykule opisano niektóre typowe problemy i błędy, które mogą występować Azure Database Migration Service użytkownicy. Artykuł zawiera również informacje dotyczące sposobu rozwiązywania tych problemów i błędów.

> [!NOTE]
> Komunikacja bezpłatna bez opłat
>
> Firma Microsoft obsługuje różnorodne i dołączane środowiska. Ten artykuł zawiera odwołania do programu Word _podrzędny_. Przewodnik po [stylu firmy Microsoft dla komunikacji bezpłatnej](https://github.com/MicrosoftDocs/microsoft-style-guide/blob/master/styleguide/bias-free-communication.md) jest rozpoznawany jako wykluczony wyraz. Słowo jest używane w tym artykule w celu zapewnienia spójności, ponieważ jest to obecnie słowo, które jest wyświetlane w oprogramowaniu. W przypadku zaktualizowania oprogramowania w celu usunięcia wyrazu ten artykuł zostanie zaktualizowany w celu wyrównania.
>

## <a name="migration-activity-in-queued-state"></a>Działanie migracji w stanie w kolejce

W przypadku tworzenia nowych działań w projekcie Azure Database Migration Service działania pozostają w stanie w kolejce.

| Przyczyna         | Rozwiązanie |
| ------------- | ------------- |
| Ten problem występuje, gdy wystąpienie Azure Database Migration Service osiągnie maksymalną pojemność dla bieżących zadań, które jednocześnie są uruchamiane. Wszelkie nowe działania są umieszczane w kolejce do momentu udostępnienia pojemności. | Sprawdź, czy wystąpienie usługi migracji danych ma uruchomione działania między projektami. Można nadal tworzyć nowe działania, które zostaną automatycznie dodane do kolejki w celu wykonania. Gdy tylko wszystkie istniejące działania są zakończone, następne działanie w kolejce zacznie działać i stan zmieni się na uruchomiony automatycznie. Nie musisz podejmować żadnych dodatkowych czynności, aby rozpocząć migrację działania z kolejki.<br><br> |

## <a name="max-number-of-databases-selected-for-migration"></a>Maksymalna liczba baz danych wybranych do migracji

Następujący błąd występuje podczas tworzenia działania dla projektu migracji bazy danych na potrzeby przeniesienia do Azure SQL Database lub wystąpienia zarządzanego Azure SQL:

* **Błąd**: Wystąpił błąd walidacji ustawień migracji "," errorDetail ":" więcej niż maksymalna liczba obiektów "Databases" dla "Database" została wybrana do migracji ".

| Przyczyna         | Rozwiązanie |
| ------------- | ------------- |
| Ten błąd jest wyświetlany, gdy wybrano więcej niż cztery bazy danych dla jednego działania migracji. W danej chwili każde działanie migracji jest ograniczone do czterech baz danych. | Wybierz cztery lub mniej baz danych na działanie migracji. Jeśli musisz migrować więcej niż cztery bazy danych równolegle, zainicjuj inne wystąpienie Azure Database Migration Service. Obecnie Każda subskrypcja obsługuje do dwóch wystąpień Azure Database Migration Service.<br><br> |

## <a name="errors-for-mysql-migration-to-azure-mysql-with-recovery-failures"></a>Błędy migracji MySQL do usługi Azure MySQL z błędami odzyskiwania

W przypadku migrowania z programu MySQL do Azure Database for MySQL przy użyciu Azure Database Migration Service działanie migracji kończy się niepowodzeniem z powodu następującego błędu:

* **Błąd**: błąd migracji bazy danych — zadanie "TaskID" zostało wstrzymane z powodu [n] kolejnych błędów odzyskiwania.

| Przyczyna         | Rozwiązanie |
| ------------- | ------------- |
| Ten błąd może wystąpić, gdy użytkownik wykonujący migrację nie ma ReplicationAdmin roli i/lub uprawnień klienta replikacji, repliki replikacji i SUPER (wersje wcześniejsze niż MySQL 5.6.6).<br><br><br><br><br><br><br><br><br><br><br><br><br> | Upewnij się, że [wstępnie wymagane uprawnienia](./tutorial-mysql-azure-mysql-online.md#prerequisites) dla konta użytkownika są skonfigurowane prawidłowo w wystąpieniu Azure Database for MySQL. Na przykład następujące kroki można wykonać, aby utworzyć użytkownika o nazwie "MigrateUser" z wymaganymi uprawnieniami:<br>1. Utwórz użytkownika migrateuser@ "%" IDENTYFIKOWANego przez element "Secret"; <br>2. Udziel wszystkim uprawnień na db_name. * do "MigrateUser" @ "%" identyfikowanego przez "Secret"; Powtórz ten krok, aby przyznać dostęp do większej liczby baz danych <br>3. Udziel dostępu do replikacji podrzędnej *.* na element "MigrateUser" @ "%" identyfikowany przez element "Secret";<br>4. Udziel klientowi replikacji na *.* na element "MigrateUser" @ "%" identyfikowany przez element "Secret";<br>5. uprawnienia opróżniania; |

## <a name="error-when-attempting-to-stop-azure-database-migration-service"></a>Wystąpił błąd podczas próby zatrzymania Azure Database Migration Service

Podczas zatrzymywania wystąpienia Azure Database Migration Service pojawia się następujący błąd:

* **Błąd**: nie można zatrzymać usługi. Error: {'error':{'code':'InvalidRequest','message':'Co najmniej jedno działanie jest aktualnie uruchomione. Aby zatrzymać usługę, poczekaj na zakończenie działania lub Zatrzymaj te działania ręcznie i spróbuj ponownie. "}}

| Przyczyna         | Rozwiązanie |
| ------------- | ------------- |
| Ten błąd jest wyświetlany, gdy wystąpienie usługi, które ma zostać zatrzymane, obejmuje działania, które są nadal uruchomione lub obecne w projektach migracji. <br><br><br><br><br><br> | Upewnij się, że nie ma żadnych działań uruchomionych w wystąpieniu Azure Database Migration Service, które próbujesz zatrzymać. Przed podjęciem próby zatrzymania usługi można również usunąć działania lub projekty. Poniższe kroki ilustrują, jak usunąć projekty w celu oczyszczenia wystąpienia usługi migracji przez usunięcie wszystkich uruchomionych zadań:<br>1. Install-Module-Name AzureRM. datamigration <br>2. Login-AzureRmAccount <br>3. Select-AzureRmSubscription-Subscriptionname " \<subName> " <br> 4. Remove-AzureRmDataMigrationProject-Name \<projectName> -ResourceGroupName \<rgName> -ServiceName- \<serviceName> DeleteRunningTask |

## <a name="error-when-attempting-to-start-azure-database-migration-service"></a>Wystąpił błąd podczas próby uruchomienia Azure Database Migration Service

Podczas uruchamiania wystąpienia Azure Database Migration Service pojawia się następujący błąd:

* **Błąd**: uruchamianie usługi nie powiodło się. Błąd: {"errorDetail": "nie można uruchomić usługi, skontaktuj się z pomocą techniczną firmy Microsoft".

| Przyczyna         | Rozwiązanie |
| ------------- | ------------- |
| Ten błąd jest wyświetlany, gdy poprzednie wystąpienie nie powiodło się wewnętrznie. Ten błąd występuje rzadko, a zespół inżynieryjny zna go. <br> | Usuń wystąpienie usługi, której nie można uruchomić, a następnie Udostępnij nowe, aby je zastąpić. |

## <a name="error-restoring-database-while-migrating-sql-to-azure-sql-db-managed-instance"></a>Wystąpił błąd podczas przywracania bazy danych programu SQL Server do wystąpienia zarządzanego usługi Azure SQL DB

Podczas przeprowadzania migracji w trybie online z SQL Server do wystąpienia zarządzanego usługi Azure SQL uruchomienie produkcyjne kończy się niepowodzeniem z powodu następującego błędu:

* **Błąd**: operacja przywracania nie powiodła się dla identyfikatora operacji "operationId". Kod "AuthorizationFailed", komunikat "klient" clientId "o identyfikatorze obiektu" objectId ", nie ma autoryzacji do wykonania akcji" Microsoft. SQL/Locations/managedDatabaseRestoreAzureAsyncOperation/Read "w zakresie"/subscriptions/subscriptionId ".".

| Przyczyna         | Rozwiązanie    |
| ------------- | ------------- |
| Ten błąd wskazuje, że podmiot zabezpieczeń aplikacji używany do migracji w trybie online z SQL Server do wystąpienia zarządzanego SQL nie ma uprawnienia współautora w ramach subskrypcji. Niektóre wywołania interfejsu API z wystąpieniem zarządzanym w obecnym momencie wymagają tego uprawnienia do subskrypcji dla operacji przywracania. <br><br><br><br><br><br><br><br><br><br><br><br><br><br> | Użyj `Get-AzureADServicePrincipal` polecenia cmdlet programu PowerShell z opcją `-ObjectId` dostępne w komunikacie o błędzie, aby wyświetlić nazwę wyświetlaną używanego identyfikatora aplikacji.<br><br> Sprawdź poprawność uprawnień do tej aplikacji i upewnij się, że ma ona [rolę współautor](../role-based-access-control/built-in-roles.md#contributor) na poziomie subskrypcji. <br><br> Zespół inżynierów Azure Database Migration Service pracuje nad ograniczeniem dostępu wymaganego przez bieżącą rolę programu w ramach subskrypcji. Jeśli masz wymóg biznesowy, który nie zezwala na korzystanie z roli programu, skontaktuj się z pomocą techniczną platformy Azure w celu uzyskania dodatkowej pomocy. |

## <a name="error-when-deleting-nic-associated-with-azure-database-migration-service"></a>Wystąpił błąd podczas usuwania karty sieciowej skojarzonej z Azure Database Migration Service

Podczas próby usunięcia karty interfejsu sieciowego skojarzonej z Azure Database Migration Service próba usunięcia nie powiedzie się z powodu tego błędu:

* **Błąd**: nie można usunąć karty sieciowej skojarzonej z Azure Database Migration Service z powodu usługi DMS korzystającej z karty sieciowej

| Przyczyna         | Rozwiązanie    |
| ------------- | ------------- |
| Ten problem występuje, gdy wystąpienie Azure Database Migration Service nadal może być obecne i zużywać kartę sieciową. <br><br><br><br><br><br><br><br> | Aby usunąć tę kartę sieciową, Usuń wystąpienie usługi DMS, które automatycznie usunie kartę sieciową używaną przez usługę.<br><br> **Ważne**: Upewnij się, że usuwane wystąpienie Azure Database Migration Service nie ma uruchomionych działań.<br><br> Po usunięciu wszystkich projektów i działań skojarzonych z wystąpieniem Azure Database Migration Service można usunąć wystąpienie usługi. Karta sieciowa używana przez wystąpienie usługi jest automatycznie czyszczona w ramach usunięcia usługi. |

## <a name="connection-error-when-using-expressroute"></a>Błąd połączenia podczas korzystania z usługi ExpressRoute

Podczas próby nawiązania połączenia ze źródłem w kreatorze projektu usługi Azure Database Migration Service, połączenie nie powiedzie się po dłuższym czasie oczekiwania, jeśli źródło używa usługi ExpressRoute dla połączeń.

| Przyczyna         | Rozwiązanie    |
| ------------- | ------------- |
| W przypadku korzystania z [ExpressRoute](https://azure.microsoft.com/services/expressroute/), Azure Database Migration Service [wymaga](./tutorial-sql-server-to-azure-sql.md) obsługi trzech punktów końcowych usługi w podsieci Virtual Network skojarzonej z usługą:<br> --Service Bus punkt końcowy<br> --Punkt końcowy magazynu<br> --Docelowy punkt końcowy bazy danych (np. punkt końcowy SQL, Cosmos DB punkt końcowy)<br><br><br><br><br> | [Włącz](./tutorial-sql-server-to-azure-sql.md) wymagane punkty końcowe usługi dla łączności ExpressRoute między źródłem a Azure Database Migration Service. <br><br><br><br><br><br><br><br> |

## <a name="lock-wait-timeout-error-when-migrating-a-mysql-database-to-azure-db-for-mysql"></a>Błąd oczekiwania na zablokowanie podczas migrowania bazy danych MySQL do usługi Azure DB for MySQL

Podczas migrowania bazy danych MySQL do wystąpienia Azure Database for MySQL za pośrednictwem Azure Database Migration Service migracja kończy się niepowodzeniem z powodu następującego błędu limitu czasu oczekiwania na blokadę:

* **Błąd**: błąd migracji bazy danych — załadowanie pliku nie powiodło się — nie można uruchomić procesu ładowania pliku "n" RetCode: SQL_ERROR SQLSTATE: HY000 NativeError: 1205 komunikat: [MySQL] [ODBC Driver] [MySQL] Przekroczono limit czasu oczekiwania na blokadę; Spróbuj ponownie uruchomić transakcję

| Przyczyna         | Rozwiązanie    |
| ------------- | ------------- |
| Ten błąd występuje, gdy migracja nie powiedzie się z powodu przekroczenia limitu czasu oczekiwania blokady podczas migracji. | Rozważ zwiększenie wartości parametru serwera **"innodb_lock_wait_timeout"**. Najwyższa dozwolona wartość to 1073741824. |

## <a name="error-connecting-to-source-sql-server-when-using-dynamic-port-or-named-instance"></a>Błąd podczas nawiązywania połączenia ze źródłem SQL Server przy użyciu portu dynamicznego lub nazwanego wystąpienia

Podczas próby nawiązania połączenia Azure Database Migration Service ze źródłem SQL Server, które jest uruchamiane w nazwanym wystąpieniu lub na porcie dynamicznym, połączenie kończy się niepowodzeniem z powodu tego błędu:

* **Błąd**: nie można nawiązać połączenia z programem SQL Server. Podczas nawiązywania połączenia z serwerem SQL wystąpił błąd dotyczący sieci lub wystąpienia. Serwer nie został znaleziony lub był niedostępny. Sprawdź, czy nazwa wystąpienia jest poprawna i czy SQL Server jest skonfigurowany do zezwalania na połączenia zdalne. (Dostawca: interfejsy sieciowe SQL, błąd: 26 — błąd lokalizowania określonego serwera/wystąpienia)

| Przyczyna         | Rozwiązanie    |
| ------------- | ------------- |
| Ten problem występuje, gdy wystąpienie źródła SQL Server, które Azure Database Migration Service próbuje nawiązać połączenie z portem dynamicznym lub używa nazwanego wystąpienia. Usługa SQL Server Browser nasłuchuje na porcie UDP 1434 dla połączeń przychodzących z nazwanym wystąpieniem lub przy użyciu portu dynamicznego. Port dynamiczny może ulec zmianie przy każdym ponownym uruchomieniu usługi SQL Server. Możesz sprawdzić port dynamiczny przypisany do wystąpienia za pośrednictwem konfiguracji sieci w SQL Server Configuration Manager.<br><br><br> |Sprawdź, czy Azure Database Migration Service może nawiązać połączenie z usługą SQL Server Browser źródłowej na porcie UDP 1434 i wystąpieniu SQL Server za pośrednictwem dynamicznie przypisanego portu TCP. |

## <a name="additional-known-issues"></a>Dodatkowe znane problemy

* [Znane problemy/ograniczenia migracji z migracją online do Azure SQL Database](./index.yml)
* [Znane problemy/ograniczenia migracji z migracją online do Azure Database for MySQL](./known-issues-azure-mysql-online.md)
* [Znane problemy/ograniczenia migracji z migracją online do Azure Database for PostgreSQL](./known-issues-azure-postgresql-online.md)

## <a name="next-steps"></a>Następne kroki

* Zapoznaj się z artykułem [Azure Database Migration Service PowerShell](/powershell/module/azurerm.datamigration#data_migration).
* Zapoznaj się z artykułem [jak skonfigurować parametry serwera w Azure Database for MySQL przy użyciu Azure Portal](../mysql/howto-server-parameters.md).
* Zapoznaj się z artykułem [Przegląd wymagań wstępnych dotyczących korzystania z Azure Database Migration Service](./pre-reqs.md).
* Zobacz [często zadawane pytania dotyczące korzystania z Azure Database Migration Service](./faq.md).