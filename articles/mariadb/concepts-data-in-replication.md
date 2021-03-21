---
title: Replikacja danych — Azure Database for MariaDB
description: Dowiedz się więcej o korzystaniu z replikacji danych w celu synchronizacji z serwerem zewnętrznym z usługą Azure Database for MariaDB.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 331e064bcf11af31a778cb8dd06c463712421b7c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98662631"
---
# <a name="replicate-data-into-azure-database-for-mariadb"></a>Replikowanie danych do Azure Database for MariaDB

Replikacja typu data-in umożliwia synchronizowanie danych z serwera MariaDB uruchomionego lokalnie, na maszynach wirtualnych lub w usługach bazy danych hostowanych przez innych dostawców chmury do usługi Azure Database for MariaDB. Replikacja typu data-in jest wykonywana za pomocą technologii replikacji opartej na pozycji w pliku dziennika binarnego (binlog) natywnej dla programu MariaDB. Aby dowiedzieć się więcej na temat replikacji binlog, zobacz [Omówienie replikacji binlog](https://mariadb.com/kb/en/library/replication-overview/).

## <a name="when-to-use-data-in-replication"></a>Kiedy używać replikacja typu data-in
Główne scenariusze, które należy wziąć pod uwagę przy użyciu replikacja typu data-in są następujące:

- **Synchronizacja danych hybrydowych:** Za pomocą replikacja typu data-in można zachować synchronizację danych między serwerami lokalnymi i Azure Database for MariaDB. Ta synchronizacja jest przydatna do tworzenia aplikacji hybrydowych. Ta metoda jest atrakcyjna, gdy masz istniejący lokalny serwer baz danych, ale chcesz przenieść dane do regionu bliżej użytkowników końcowych.
- **Synchronizacja z obsługą kilku chmur:** W przypadku złożonych rozwiązań w chmurze Użyj replikacja typu data-in do synchronizowania danych między Azure Database for MariaDB i różnymi dostawcami chmury, w tym maszyn wirtualnych i usług baz danych hostowanych w tych chmurach.

## <a name="limitations-and-considerations"></a>Ograniczenia i istotne zagadnienia

### <a name="data-not-replicated"></a>Dane nie zostały zreplikowane
[*Baza danych systemu MySQL*](https://mariadb.com/kb/en/library/the-mysql-database-tables/) na serwerze źródłowym nie jest replikowana. Zmiany kont i uprawnień na serwerze źródłowym nie są replikowane. Jeśli utworzysz konto na serwerze źródłowym, a to konto musi uzyskać dostęp do serwera repliki, utwórz je ręcznie na stronie serwera repliki. Aby zrozumieć, jakie tabele są zawarte w systemowej bazie danych, zapoznaj się z [dokumentacją MariaDB](https://mariadb.com/kb/en/library/the-mysql-database-tables/).

### <a name="requirements"></a>Wymagania
- Wersja serwera źródłowego musi mieć co najmniej MariaDB w wersji 10,2.
- Wersja serwera źródłowego i repliki musi być taka sama. Na przykład obydwie muszą być MariaDB w wersji 10,2.
- Każda tabela musi mieć klucz podstawowy.
- Serwer źródłowy powinien używać aparatu InnoDB.
- Użytkownik musi mieć uprawnienia do konfigurowania rejestrowania plików binarnych i tworzenia nowych użytkowników na serwerze źródłowym.
- Jeśli na serwerze źródłowym jest włączony protokół SSL, upewnij się, że certyfikat urzędu certyfikacji SSL podany dla domeny został uwzględniony w `mariadb.az_replication_change_master` procedurze składowanej. Zapoznaj się z poniższymi [przykładami](howto-data-in-replication.md#link-the-source-and-replica-servers-to-start-data-in-replication) i `master_ssl_ca` parametrem.
- Upewnij się, że adres IP serwera źródłowego został dodany do reguł zapory serwera repliki Azure Database for MariaDB. Zaktualizuj reguły zapory za pomocą [witryny Azure Portal](howto-manage-firewall-portal.md) lub [interfejsu wiersza polecenia platformy Azure](howto-manage-firewall-cli.md).
- Upewnij się, że komputer obsługujący serwer źródłowy zezwala na ruch przychodzący i wychodzący na porcie 3306.
- Upewnij się, że serwer źródłowy ma **publiczny adres IP**, usługa DNS jest publicznie dostępna lub ma w pełni kwalifikowaną nazwę domeny (FQDN).

### <a name="other"></a>Inne
- Replikacja danych jest obsługiwana tylko w warstwach cenowych Ogólnego przeznaczenia i zoptymalizowanych pod kątem pamięci.

## <a name="next-steps"></a>Następne kroki
- Dowiedz się, jak [skonfigurować replikację danych](howto-data-in-replication.md).
