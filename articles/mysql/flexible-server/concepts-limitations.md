---
title: Ograniczenia — Azure Database for MySQL — elastyczny serwer
description: W tym artykule opisano ograniczenia w Azure Database for MySQL-elastycznym serwerze, takie jak liczba opcji połączenia i aparatu magazynu.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 10/1/2020
ms.openlocfilehash: a064b2b2d0bad5fc8ded9a59b66d84a361facec9
ms.sourcegitcommit: 4bee52a3601b226cfc4e6eac71c1cb3b4b0eafe2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/11/2020
ms.locfileid: "94504343"
---
# <a name="limitations-in-azure-database-for-mysql---flexible-server-preview"></a>Ograniczenia na serwerze elastycznym Azure Database for MySQL (wersja zapoznawcza)

> [!IMPORTANT] 
> Serwer elastyczny Azure Database for MySQL jest obecnie w publicznej wersji zapoznawczej.

W tym artykule opisano ograniczenia w Azure Database for MySQL elastycznej usłudze serwerowej. Stosowane są również [ogólne ograniczenia](https://dev.mysql.com/doc/mysql-reslimits-excerpt/5.7/en/limits.html) aparatu bazy danych MySQL. Jeśli chcesz dowiedzieć się więcej o warstwach zasobów (obliczeniowych, pamięci, magazynu), zobacz artykuł dotyczący [obliczeń i magazynu](concepts-compute-storage.md) .

## <a name="server-parameters"></a>Parametry serwera

> [!NOTE]
> Jeśli szukasz minimalnej/maksymalnej wartości parametrów serwera, takich jak `max_connections` i `innodb_buffer_pool_size` , te informacje zostały przeniesione do artykułu [Parametry](./concepts-server-parameters.md) serwera pojęcia dotyczące parametrów serwera.

Azure Database for MySQL obsługuje dostrajanie wartości parametrów serwera. Wartość minimalna i maksymalna niektórych parametrów (np. `max_connections`, `join_buffer_size` , `query_cache_size` ) zależy od warstwy obliczeniowej i rozmiaru obliczeniowego serwera. Aby uzyskać więcej informacji na temat tych limitów, zapoznaj się z [parametrami serwera](./concepts-server-parameters.md) .

Wtyczki hasła, takie jak "validate_password" i "caching_sha2_password", nie są obsługiwane przez usługę.

## <a name="storage-engines"></a>Aparaty pamięci masowej

Program MySQL obsługuje wiele aparatów pamięci masowej. Na Azure Database for MySQL elastycznym serwerze obsługiwane są następujące aparaty magazynu:

### <a name="supported"></a>Obsługiwane
- [InnoDB](https://dev.mysql.com/doc/refman/5.7/en/innodb-introduction.html)
- [ROZMIAR](https://dev.mysql.com/doc/refman/5.7/en/memory-storage-engine.html)

### <a name="unsupported"></a>Nieobsługiwane
- [MyISAM](https://dev.mysql.com/doc/refman/5.7/en/myisam-storage-engine.html)
- [BLACKHOLE](https://dev.mysql.com/doc/refman/5.7/en/blackhole-storage-engine.html)
- [FOLDERU](https://dev.mysql.com/doc/refman/5.7/en/archive-storage-engine.html)
- [FEDERACYJNY](https://dev.mysql.com/doc/refman/5.7/en/federated-storage-engine.html)

## <a name="privileges--data-manipulation-support"></a>Uprawnienia & obsługa manipulowania danymi

Wiele parametrów serwera i ustawień może przypadkowo obniżyć poziom wydajności serwera lub właściwości KWASów Negate serwera MySQL. Aby zachować integralność usługi i umowę SLA na poziomie produktu, ta usługa nie ujawnia wielu ról. 

Usługa MySQL nie zezwala na bezpośredni dostęp do bazowego systemu plików. Niektóre polecenia manipulowania danymi nie są obsługiwane. 

### <a name="unsupported"></a>Nieobsługiwane

Następujące elementy nie są obsługiwane:
- Rola DBA: z ograniczeniami. Alternatywnie można użyć administratora (utworzonego podczas tworzenia nowego serwera), który umożliwia wykonywanie większości instrukcji DDL i DML. 
- Najwyższe uprawnienia: analogicznie, [uprawnienie](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html#priv_super) jest ograniczone.
- Zdefiniuj: wymagane są uprawnienia administratora do tworzenia i jest ograniczone. W przypadku importowania danych przy użyciu kopii zapasowej Usuń `CREATE DEFINER` polecenia ręcznie lub przy użyciu `--skip-definer` polecenia podczas wykonywania mysqldump.
- Systemowe bazy danych: [baza danych systemu MySQL](https://dev.mysql.com/doc/refman/5.7/en/system-schema.html) jest tylko do odczytu i używana do obsługi różnych funkcji PaaS. Nie można wprowadzać zmian w `mysql` systemowej bazie danych.
- `SELECT ... INTO OUTFILE`: Nieobsługiwane w usłudze.

### <a name="supported"></a>Obsługiwane
- `LOAD DATA INFILE` jest obsługiwane, ale `[LOCAL]` parametr musi być określony i skierowany do ścieżki UNC (magazyn platformy Azure zainstalowany za pomocą protokołu SMB).

## <a name="functional-limitations"></a>Ograniczenia funkcjonalne

### <a name="zone-redundant-ha"></a>Strefa nadmiarowa o wysokiej dostępności
- Tę konfigurację można ustawić tylko podczas tworzenia serwera.
- Nieobsługiwane w warstwie obliczeniowej z możliwością naliczania.

### <a name="networking"></a>Sieć
- Nie można zmienić metody łączności po utworzeniu serwera. Jeśli serwer został utworzony z *dostępem prywatnym (Integracja z siecią wirtualną)* , nie można go zmienić na *publiczny dostęp (dozwolone adresy IP)* po utworzeniu i odwrotnie
- Protokół TLS/SSL jest domyślnie włączony i nie można go wyłączyć.
- Minimalna wersja protokołu TLS obsługiwana na serwerze to TLS 1.2. Aby dowiedzieć się więcej, zobacz [nawiązywanie połączenia za pomocą protokołu TLS/SSL](./how-to-connect-tls-ssl.md) .

### <a name="stopstart-operation"></a>Operacja zatrzymywania/uruchamiania
- Nieobsługiwane w przypadku strefowo nadmiarowych konfiguracji HA (zarówno podstawowych, jak i w stanie wstrzymania).
- Nieobsługiwane w przypadku konfiguracji repliki odczytu (źródła i repliki).

### <a name="scale-operations"></a>Operacje skalowania
- Zmniejszenie ilości miejsca w magazynie serwera nie jest obsługiwane.

### <a name="read-replicas"></a>Repliki do odczytu
- Nieobsługiwane w przypadku strefowo nadmiarowych konfiguracji HA (zarówno podstawowych, jak i w stanie wstrzymania).

### <a name="server-version-upgrades"></a>Uaktualnienia wersji serwera
- Automatyczna Migracja między wersjami aparatu głównej bazy danych nie jest obsługiwana. Jeśli chcesz uaktualnić wersję główną, weź [zrzut i Przywróć](../concepts-migrate-dump-restore.md) ją na serwerze, który został utworzony przy użyciu nowej wersji aparatu.

### <a name="restoring-a-server"></a>Przywracanie serwera
- W przypadku przywracania do punktu w czasie nowe serwery są tworzone przy użyciu tych samych konfiguracji obliczeniowych i magazynu, co na serwerze źródłowym, na którym bazuje. Nowo przywrócone obliczenia serwera można skalować w dół po utworzeniu serwera.
- Przywracanie usuniętego serwera nie jest obsługiwane.

## <a name="next-steps"></a>Następne kroki

- [Informacje o dostępnych opcjach obliczeniowych i pamięci masowej](concepts-compute-storage.md)
- Informacje o [obsługiwanych wersjach programu MySQL](concepts-supported-versions.md)
- Zapoznaj [się z tematem jak utworzyć kopię zapasową i przywrócić serwer przy użyciu Azure Portal](how-to-restore-server-portal.md)