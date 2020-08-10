---
title: Replikacja danych — Azure Database for MySQL
description: Dowiedz się więcej o korzystaniu z replikacji danych w celu synchronizacji z serwerem zewnętrznym z usługą Azure Database for MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 8/7/2020
ms.openlocfilehash: a9d6c1b2438f20a06062842b96b147e094760238
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/10/2020
ms.locfileid: "88031221"
---
# <a name="replicate-data-into-azure-database-for-mysql"></a>Replikowanie danych do Azure Database for MySQL

Replikacja typu data-in pozwala synchronizować dane z zewnętrznego serwera MySQL do usługi Azure Database for MySQL. Serwer zewnętrzny może być lokalny, w maszynach wirtualnych lub w usłudze bazy danych hostowanej przez innych dostawców chmury. Replikacja typu data-in jest wykonywana za pomocą technologii replikacji opartej na pozycji w pliku dziennika binarnego (binlog) natywnej dla programu MySQL. Aby dowiedzieć się więcej na temat replikacji binlog, zobacz [Omówienie replikacji MySQL binlog](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html). 

## <a name="when-to-use-data-in-replication"></a>Kiedy używać replikacja typu data-in
Główne scenariusze, które należy wziąć pod uwagę przy użyciu replikacja typu data-in są następujące:

- **Synchronizacja danych hybrydowych:** Za pomocą replikacja typu data-in można zachować synchronizację danych między serwerami lokalnymi i Azure Database for MySQL. Ta synchronizacja jest przydatna do tworzenia aplikacji hybrydowych. Ta metoda jest atrakcyjna, gdy masz istniejący lokalny serwer baz danych, ale chcesz przenieść dane do regionu bliżej użytkowników końcowych.
- **Synchronizacja z obsługą kilku chmur:** W przypadku złożonych rozwiązań w chmurze Użyj replikacja typu data-in do synchronizowania danych między Azure Database for MySQL i różnymi dostawcami chmury, w tym maszyn wirtualnych i usług baz danych hostowanych w tych chmurach.
 
W przypadku scenariuszy migracji Użyj [Azure Database Migration Service](https://azure.microsoft.com/services/database-migration/)(DMS).

## <a name="limitations-and-considerations"></a>Ograniczenia i istotne zagadnienia

### <a name="data-not-replicated"></a>Dane nie zostały zreplikowane
[*Baza danych systemu MySQL*](https://dev.mysql.com/doc/refman/5.7/en/system-schema.html) na serwerze głównym nie jest replikowana. Zmiany kont i uprawnień na serwerze głównym nie są replikowane. Jeśli utworzysz konto na serwerze głównym, a to konto musi uzyskać dostęp do serwera repliki, ręcznie Utwórz to samo konto na stronie serwera repliki. Aby zrozumieć, jakie tabele są zawarte w systemowej bazie danych, zobacz [Podręcznik programu MySQL](https://dev.mysql.com/doc/refman/5.7/en/system-schema.html).

### <a name="filtering"></a>Filtrowanie
Aby pominąć replikację tabel z serwera głównego (hostowanego lokalnie, w maszynach wirtualnych lub w usłudze bazy danych hostowanej przez innych dostawców chmury), `replicate_wild_ignore_table` parametr jest obsługiwany. Opcjonalnie należy zaktualizować ten parametr na serwerze repliki hostowanym na platformie Azure przy użyciu [Azure Portal](howto-server-parameters.md) lub [interfejsu wiersza polecenia platformy Azure](howto-configure-server-parameters-using-cli.md).

Zapoznaj się z [dokumentacją programu MySQL](https://dev.mysql.com/doc/refman/8.0/en/replication-options-replica.html#option_mysqld_replicate-wild-ignore-table) , aby dowiedzieć się więcej o tym parametrze.

### <a name="requirements"></a>Wymagania
- Wersja serwera głównego musi być nowsza niż wersja 5,6. 
- Wersje serwerów głównych i replik muszą być takie same. Na przykład oba muszą być w wersji 5,6 lub muszą mieć wersję MySQL w wersji 5,7.
- Każda tabela musi mieć klucz podstawowy.
- Serwer główny powinien korzystać z aparatu programu MySQL InnoDB.
- Użytkownik musi mieć uprawnienia do konfigurowania rejestrowania plików binarnych i tworzenia nowych użytkowników na serwerze głównym.
- Jeśli na serwerze głównym jest włączony protokół SSL, upewnij się, że certyfikat urzędu certyfikacji SSL podany dla domeny został uwzględniony w `mysql.az_replication_change_master` procedurze składowanej. Zapoznaj się z poniższymi [przykładami](https://docs.microsoft.com/azure/mysql/howto-data-in-replication#link-master-and-replica-servers-to-start-data-in-replication) i `master_ssl_ca` parametrem.
- Upewnij się, że adres IP serwera głównego został dodany do reguł zapory serwera repliki usługi Azure Database for MySQL. Zaktualizuj reguły zapory za pomocą [witryny Azure Portal](https://docs.microsoft.com/azure/mysql/howto-manage-firewall-using-portal) lub [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/mysql/howto-manage-firewall-using-cli).
- Upewnij się, że maszyna hostująca serwer główny zezwala na ruch przychodzący i wychodzący na porcie 3306.
- Upewnij się, że serwer główny ma **publiczny adres IP**, usługa DNS jest publicznie dostępna lub ma w pełni kwalifikowaną nazwę domeny (FQDN).

### <a name="other"></a>Inne
- Replikacja danych jest obsługiwana tylko w warstwach cenowych Ogólnego przeznaczenia i zoptymalizowanych pod kątem pamięci.
- Globalne identyfikatory transakcji (GTID) nie są obsługiwane.

## <a name="next-steps"></a>Następne kroki
- Dowiedz się, jak [skonfigurować replikację danych](howto-data-in-replication.md)
- Więcej informacji [na temat replikowania na platformie Azure przy użyciu replik odczytu](concepts-read-replicas.md)
- Dowiedz się więcej o tym, jak [migrować dane przy minimalnym przestoju przy użyciu usługi DMS](howto-migrate-online.md)