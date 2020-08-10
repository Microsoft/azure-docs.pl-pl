---
title: Ograniczenia — Azure Database for MySQL
description: W tym artykule opisano ograniczenia w Azure Database for MySQL, takie jak liczba opcji połączenia i aparatu magazynu.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 6/25/2020
ms.openlocfilehash: 24a214d63fd01fc4353be6563d18f9e28b820c6f
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/10/2020
ms.locfileid: "88036525"
---
# <a name="limitations-in-azure-database-for-mysql"></a>Ograniczenia w Azure Database for MySQL
W poniższych sekcjach opisano pojemność, obsługę aparatu magazynu, obsługę uprawnień, obsługę instrukcji manipulowania danymi oraz limity funkcjonalne w usłudze bazy danych. Zapoznaj się również z [ogólnymi ograniczeniami](https://dev.mysql.com/doc/mysql-reslimits-excerpt/5.6/en/limits.html) dotyczącymi aparatu bazy danych MySQL.

## <a name="server-parameters"></a>Parametry serwera

> [!NOTE]
> Jeśli szukasz minimalnej/maksymalnej wartości parametrów serwera, takich jak `max_connections` i `innodb_buffer_pool_size` , te informacje zostały przeniesione do artykułu **[parametry serwera](./concepts-server-parameters.md)** .

Azure Database for MySQL obsługuje dostrajanie wartości parametrów serwera. Wartość minimalna i maksymalna niektórych parametrów (np. `max_connections`, `join_buffer_size` , `query_cache_size` ) jest określana na podstawie warstwy cenowej i rdzeni wirtualnych serwera. Aby uzyskać więcej informacji na temat tych limitów, zapoznaj się z [parametrami serwera](./concepts-server-parameters.md) .

Po wstępnym wdrożeniu serwer platformy Azure dla programu MySQL zawiera tabele systemów dla informacji o strefie czasowej, ale te tabele nie są wypełnione. Tabele strefy czasowej mogą być wypełniane przez wywołanie `mysql.az_load_timezone` procedury składowanej z narzędzia, takiego jak wiersz polecenia MySQL lub MySQL Workbench. Zapoznaj się z artykułami [Azure Portal](howto-server-parameters.md#working-with-the-time-zone-parameter) lub [interfejsem wiersza polecenia platformy Azure](howto-configure-server-parameters-using-cli.md#working-with-the-time-zone-parameter) dotyczącymi sposobu wywoływania procedury składowanej i ustawiania stref czasowych na poziomie globalnym lub w sesji.

## <a name="storage-engine-support"></a>Obsługa aparatu magazynu

### <a name="supported"></a>Obsługiwane
- [InnoDB](https://dev.mysql.com/doc/refman/5.7/en/innodb-introduction.html)
- [ROZMIAR](https://dev.mysql.com/doc/refman/5.7/en/memory-storage-engine.html)

### <a name="unsupported"></a>Nieobsługiwane
- [MyISAM](https://dev.mysql.com/doc/refman/5.7/en/myisam-storage-engine.html)
- [BLACKHOLE](https://dev.mysql.com/doc/refman/5.7/en/blackhole-storage-engine.html)
- [FOLDERU](https://dev.mysql.com/doc/refman/5.7/en/archive-storage-engine.html)
- [FEDERACYJNY](https://dev.mysql.com/doc/refman/5.7/en/federated-storage-engine.html)

## <a name="privilege-support"></a>Obsługa uprawnień

### <a name="unsupported"></a>Nieobsługiwane
- Rola DBA: wiele parametrów serwera i ustawień może przypadkowo obniżyć wydajność serwera lub Negate właściwości KWASów systemu DBMS. W związku z tym, aby zachować integralność usługi i umowę SLA na poziomie produktu, ta usługa nie ujawnia roli DBA. Domyślne konto użytkownika, które jest konstruowane podczas tworzenia nowego wystąpienia bazy danych, umożliwia temu użytkownikowi wykonywanie większości instrukcji języka DDL i DML w zarządzanym wystąpieniu bazy danych. 
- Uprawnienie "noprivileged": podobne [nieuprzywilejowane](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html#priv_super) również jest ograniczone.
- Zdefiniuj: wymagane są uprawnienia administratora do tworzenia i jest ograniczone. W przypadku importowania danych przy użyciu kopii zapasowej Usuń `CREATE DEFINER` polecenia ręcznie lub przy użyciu `--skip-definer` polecenia podczas wykonywania mysqldump.
- Systemowe bazy danych: w Azure Database for MySQL [baza danych systemu MySQL](https://dev.mysql.com/doc/refman/8.0/en/system-schema.html) jest tylko do odczytu, ponieważ jest używana do obsługi różnych funkcji usługi PaaS. Należy pamiętać, że nie można nic zmienić w `mysql` systemowej bazie danych.

## <a name="data-manipulation-statement-support"></a>Obsługa instrukcji manipulowania danymi

### <a name="supported"></a>Obsługiwane
- `LOAD DATA INFILE`jest obsługiwane, ale `[LOCAL]` parametr musi być określony i skierowany do ścieżki UNC (magazyn platformy Azure zainstalowany za pomocą protokołu SMB).

### <a name="unsupported"></a>Nieobsługiwane
- `SELECT ... INTO OUTFILE`

## <a name="functional-limitations"></a>Ograniczenia funkcjonalne

### <a name="scale-operations"></a>Operacje skalowania
- Dynamiczne skalowanie do i z podstawowych warstw cenowych nie jest obecnie obsługiwane.
- Zmniejszenie rozmiaru magazynu serwera nie jest obsługiwane.

### <a name="server-version-upgrades"></a>Uaktualnienia wersji serwera
- Automatyczna Migracja między wersjami aparatu głównej bazy danych nie jest obecnie obsługiwana. Jeśli chcesz uaktualnić do następnej wersji głównej, zrób [zrzut i Przywróć](./concepts-migrate-dump-restore.md) go na serwerze, który został utworzony przy użyciu nowej wersji aparatu.

### <a name="point-in-time-restore"></a>Przywracanie do punktu w czasie
- W przypadku korzystania z funkcji kopie nowy serwer jest tworzony z tymi samymi konfiguracjami co serwer, na którym jest oparta.
- Przywracanie usuniętego serwera nie jest obsługiwane.

### <a name="vnet-service-endpoints"></a>Punkty końcowe usługi sieci wirtualnej
- Obsługa punktów końcowych usługi sieci wirtualnej jest obsługiwana tylko w przypadku serwerów Ogólnego przeznaczenia i zoptymalizowanych pod kątem pamięci.

### <a name="storage-size"></a>Rozmiar magazynu
- Zapoznaj się z [warstwami cenowymi](concepts-pricing-tiers.md) dla limitów rozmiaru magazynu dla warstwy cenowej.

## <a name="current-known-issues"></a>Bieżące znane problemy
- Wystąpienie serwera MySQL wyświetla nieprawidłową wersję serwera po nawiązaniu połączenia. Aby uzyskać poprawną wersję aparatu wystąpienia serwera, użyj `select version();` polecenia.

## <a name="next-steps"></a>Następne kroki
- [Co jest dostępne w poszczególnych warstwach usług](concepts-pricing-tiers.md)
- [Obsługiwane wersje bazy danych MySQL](concepts-supported-versions.md)
