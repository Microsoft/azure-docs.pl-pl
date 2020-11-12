---
title: Ograniczenia — Azure Database for MariaDB
description: W tym artykule opisano ograniczenia w Azure Database for MariaDB, takie jak liczba opcji połączenia i aparatu magazynu.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: conceptual
ms.date: 10/2/2020
ms.openlocfilehash: d4546732f067988c9d7dd2d11c718a15fbe32d23
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/12/2020
ms.locfileid: "94540658"
---
# <a name="limitations-in-azure-database-for-mariadb"></a>Ograniczenia w Azure Database for MariaDB
W poniższych sekcjach opisano pojemność, obsługę aparatu magazynu, obsługę uprawnień, obsługę instrukcji manipulowania danymi oraz limity funkcjonalne w usłudze bazy danych.

## <a name="server-parameters"></a>Parametry serwera

> [!NOTE]
> Jeśli szukasz minimalnej/maksymalnej wartości parametrów serwera, takich jak `max_connections` i `innodb_buffer_pool_size` , te informacje zostały przeniesione do artykułu **[parametry serwera](./concepts-server-parameters.md)** .

Azure Database for MariaDB obsługuje dostrajanie wartości parametrów serwera. Wartość minimalna i maksymalna niektórych parametrów (np. `max_connections`, `join_buffer_size` , `query_cache_size` ) jest określana na podstawie warstwy cenowej i rdzeni wirtualnych serwera. Aby uzyskać więcej informacji na temat tych limitów, zapoznaj się z [parametrami serwera](./concepts-server-parameters.md) .

Po wstępnym wdrożeniu serwer platformy Azure dla MariaDB zawiera tabele systemów dla informacji o strefie czasowej, ale te tabele nie są wypełnione. Tabele strefy czasowej mogą być wypełniane przez wywołanie `mysql.az_load_timezone` procedury składowanej z narzędzia, takiego jak wiersz polecenia MySQL lub MySQL Workbench. Zapoznaj się z artykułami [Azure Portal](howto-server-parameters.md#working-with-the-time-zone-parameter) lub [interfejsem wiersza polecenia platformy Azure](howto-configure-server-parameters-cli.md#working-with-the-time-zone-parameter) dotyczącymi sposobu wywoływania procedury składowanej i ustawiania stref czasowych na poziomie globalnym lub w sesji.

Wtyczki hasła, takie jak "validate_password" i "caching_sha2_password", nie są obsługiwane przez usługę.

## <a name="storage-engine-support"></a>Obsługa aparatu magazynu

### <a name="supported"></a>Obsługiwane
- [InnoDB](https://mariadb.com/kb/en/library/xtradb-and-innodb/)
- [ROZMIAR](https://mariadb.com/kb/en/library/memory-storage-engine/)

### <a name="unsupported"></a>Nieobsługiwane
- [MyISAM](https://mariadb.com/kb/en/library/myisam-storage-engine/)
- [BLACKHOLE](https://mariadb.com/kb/en/library/blackhole/)
- [FOLDERU](https://mariadb.com/kb/en/library/archive/)

## <a name="privileges--data-manipulation-support"></a>Uprawnienia & obsługa manipulowania danymi

Wiele parametrów i ustawień serwera może przypadkowo obniżyć wydajność serwera lub właściwości negateego kwasu serwera MariaDB. Aby zachować integralność usługi i umowę SLA na poziomie produktu, ta usługa nie ujawnia wielu ról. 

Usługa MariaDB nie zezwala na bezpośredni dostęp do bazowego systemu plików. Niektóre polecenia manipulowania danymi nie są obsługiwane. 

## <a name="privilege-support"></a>Obsługa uprawnień

### <a name="unsupported"></a>Nieobsługiwane

Następujące elementy nie są obsługiwane:
- Rola DBA: z ograniczeniami. Alternatywnie można użyć administratora (utworzonego podczas tworzenia nowego serwera), który umożliwia wykonywanie większości instrukcji DDL i DML. 
- Najwyższe uprawnienia: analogicznie, najwyższe [uprawnienia](https://mariadb.com/kb/en/library/grant/#global-privileges) również są ograniczone.
- Zdefiniuj: wymagane są uprawnienia administratora do tworzenia i jest ograniczone. W przypadku importowania danych przy użyciu kopii zapasowej Usuń `CREATE DEFINER` polecenia ręcznie lub przy użyciu `--skip-definer` polecenia podczas wykonywania mysqldump.
- Systemowe bazy danych: [baza danych systemu MySQL](https://mariadb.com/kb/en/the-mysql-database-tables/) jest tylko do odczytu i używana do obsługi różnych funkcji PaaS. Nie można wprowadzać zmian w `mysql` systemowej bazie danych.
- `SELECT ... INTO OUTFILE`: Nieobsługiwane w usłudze.

### <a name="supported"></a>Obsługiwane
- `LOAD DATA INFILE` jest obsługiwane, ale `[LOCAL]` parametr musi być określony i skierowany do ścieżki UNC (magazyn platformy Azure zainstalowany za pomocą protokołu SMB).

## <a name="functional-limitations"></a>Ograniczenia funkcjonalne

### <a name="scale-operations"></a>Operacje skalowania
- Dynamiczne skalowanie do i z podstawowych warstw cenowych nie jest obecnie obsługiwane.
- Zmniejszenie rozmiaru magazynu serwera nie jest obsługiwane.

### <a name="server-version-upgrades"></a>Uaktualnienia wersji serwera
- Automatyczna Migracja między wersjami aparatu głównej bazy danych nie jest obecnie obsługiwana.

### <a name="point-in-time-restore"></a>Przywracanie do punktu w czasie
- W przypadku korzystania z funkcji kopie nowy serwer jest tworzony z tymi samymi konfiguracjami co serwer, na którym jest oparta.
- Przywracanie usuniętego serwera nie jest obsługiwane.

### <a name="subscription-management"></a>Zarządzanie subskrypcjami
- Dynamiczne przeniesienie wstępnie utworzonych serwerów między subskrypcją i grupą zasobów nie jest obecnie obsługiwane.

### <a name="vnet-service-endpoints"></a>Punkty końcowe usługi sieci wirtualnej
- Obsługa punktów końcowych usługi sieci wirtualnej jest obsługiwana tylko w przypadku serwerów Ogólnego przeznaczenia i zoptymalizowanych pod kątem pamięci.

### <a name="storage-size"></a>Rozmiar magazynu
- Zapoznaj się z [warstwami cenowymi](concepts-pricing-tiers.md) dla limitów rozmiaru magazynu dla warstwy cenowej.

## <a name="current-known-issues"></a>Bieżące znane problemy
- Wystąpienie serwera MariaDB wyświetla nieprawidłową wersję serwera po nawiązaniu połączenia. Aby uzyskać poprawną wersję aparatu wystąpienia serwera, użyj `select version();` polecenia.

## <a name="next-steps"></a>Następne kroki
- [Co jest dostępne w poszczególnych warstwach usług](concepts-pricing-tiers.md)
- [Obsługiwane wersje bazy danych MariaDB](concepts-supported-versions.md)
