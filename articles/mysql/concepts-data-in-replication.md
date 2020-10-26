---
title: Replikacja danych — Azure Database for MySQL
description: Dowiedz się więcej o korzystaniu z replikacji danych w celu synchronizacji z serwerem zewnętrznym z usługą Azure Database for MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 8/7/2020
ms.openlocfilehash: e84f0c9beaee8a755499467925d28a83ba3139fc
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/26/2020
ms.locfileid: "92544056"
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
[*Baza danych systemu MySQL*](https://dev.mysql.com/doc/refman/5.7/en/system-schema.html) na serwerze źródłowym nie jest replikowana. Nie są replikowane zmiany kont i uprawnień na serwerze źródłowym. Jeśli utworzysz konto na serwerze źródłowym, a to konto musi uzyskać dostęp do serwera repliki, ręcznie Utwórz to samo konto na stronie serwera repliki. Aby zrozumieć, jakie tabele są zawarte w systemowej bazie danych, zobacz [Podręcznik programu MySQL](https://dev.mysql.com/doc/refman/5.7/en/system-schema.html).

### <a name="filtering"></a>Filtrowanie
Aby pominąć replikację tabel z serwera źródłowego (hostowanego lokalnie, w maszynach wirtualnych lub w usłudze bazy danych hostowanej przez innych dostawców chmury), `replicate_wild_ignore_table` parametr jest obsługiwany. Opcjonalnie należy zaktualizować ten parametr na serwerze repliki hostowanym na platformie Azure przy użyciu [Azure Portal](howto-server-parameters.md) lub [interfejsu wiersza polecenia platformy Azure](howto-configure-server-parameters-using-cli.md).

Zapoznaj się z [dokumentacją programu MySQL](https://dev.mysql.com/doc/refman/8.0/en/replication-options-replica.html#option_mysqld_replicate-wild-ignore-table) , aby dowiedzieć się więcej o tym parametrze.

### <a name="requirements"></a>Wymagania
- Wersja serwera źródłowego musi być nowsza niż wersja 5,6. 
- Wersja serwera źródłowego i repliki musi być taka sama. Na przykład oba muszą być w wersji 5,6 lub muszą mieć wersję MySQL w wersji 5,7.
- Każda tabela musi mieć klucz podstawowy.
- Serwer źródłowy powinien używać aparatu programu MySQL InnoDB.
- Użytkownik musi mieć uprawnienia do konfigurowania rejestrowania plików binarnych i tworzenia nowych użytkowników na serwerze źródłowym.
- Jeśli na serwerze źródłowym jest włączony protokół SSL, upewnij się, że certyfikat urzędu certyfikacji SSL podany dla domeny został uwzględniony w `mysql.az_replication_change_master` procedurze składowanej. Zapoznaj się z poniższymi [przykładami](./howto-data-in-replication.md#link-source-and-replica-servers-to-start-data-in-replication) i `master_ssl_ca` parametrem.
- Upewnij się, że adres IP serwera źródłowego został dodany do reguł zapory serwera repliki Azure Database for MySQL. Zaktualizuj reguły zapory za pomocą [witryny Azure Portal](./howto-manage-firewall-using-portal.md) lub [interfejsu wiersza polecenia platformy Azure](./howto-manage-firewall-using-cli.md).
- Upewnij się, że komputer obsługujący serwer źródłowy zezwala na ruch przychodzący i wychodzący na porcie 3306.
- Upewnij się, że serwer źródłowy ma **publiczny adres IP** , usługa DNS jest publicznie dostępna lub ma w pełni kwalifikowaną nazwę domeny (FQDN).

### <a name="other"></a>Inne
- Replikacja danych jest obsługiwana tylko w warstwach cenowych Ogólnego przeznaczenia i zoptymalizowanych pod kątem pamięci.
- Globalne identyfikatory transakcji (GTID) nie są obsługiwane.

## <a name="next-steps"></a>Następne kroki
- Dowiedz się, jak [skonfigurować replikację danych](howto-data-in-replication.md)
- Więcej informacji [na temat replikowania na platformie Azure przy użyciu replik odczytu](concepts-read-replicas.md)
- Dowiedz się więcej o tym, jak [migrować dane przy minimalnym przestoju przy użyciu usługi DMS](howto-migrate-online.md)