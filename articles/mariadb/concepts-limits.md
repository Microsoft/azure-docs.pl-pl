---
title: Ograniczenia — Azure Database for MariaDB
description: W tym artykule opisano ograniczenia w Azure Database for MariaDB, takie jak liczba opcji połączenia i aparatu magazynu.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 6/8/2020
ms.openlocfilehash: 11b28acfbda8b2760f19aa130373ba0f24f94db2
ms.sourcegitcommit: ce44069e729fce0cf67c8f3c0c932342c350d890
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/09/2020
ms.locfileid: "84636601"
---
# <a name="limitations-in-azure-database-for-mariadb"></a>Ograniczenia w Azure Database for MariaDB
W poniższych sekcjach opisano pojemność, obsługę aparatu magazynu, obsługę uprawnień, obsługę instrukcji manipulowania danymi oraz limity funkcjonalne w usłudze bazy danych.

## <a name="storage-engine-support"></a>Obsługa aparatu magazynu

### <a name="supported"></a>Obsługiwane
- [InnoDB](https://mariadb.com/kb/en/library/xtradb-and-innodb/)
- [ROZMIAR](https://mariadb.com/kb/en/library/memory-storage-engine/)

### <a name="unsupported"></a>Nieobsługiwane
- [MyISAM](https://mariadb.com/kb/en/library/myisam-storage-engine/)
- [BLACKHOLE](https://mariadb.com/kb/en/library/blackhole/)
- [FOLDERU](https://mariadb.com/kb/en/library/archive/)

## <a name="privilege-support"></a>Obsługa uprawnień

### <a name="unsupported"></a>Nieobsługiwane
- Rola DBA: wiele parametrów serwera i ustawień może przypadkowo obniżyć wydajność serwera lub Negate właściwości KWASów systemu DBMS. W związku z tym, aby zachować integralność usługi i umowę SLA na poziomie produktu, ta usługa nie ujawnia roli DBA. Domyślne konto użytkownika, które jest konstruowane podczas tworzenia nowego wystąpienia bazy danych, umożliwia temu użytkownikowi wykonywanie większości instrukcji języka DDL i DML w zarządzanym wystąpieniu bazy danych.
- Uprawnienie "noprivileged": podobne [nieuprzywilejowane](https://mariadb.com/kb/en/library/grant/#global-privileges) również jest ograniczone.
- Zdefiniuj: wymagane są uprawnienia administratora do tworzenia i jest ograniczone. W przypadku importowania danych przy użyciu kopii zapasowej Usuń `CREATE DEFINER` polecenia ręcznie lub przy użyciu `--skip-definer` polecenia podczas wykonywania mysqldump.

## <a name="server-parameters"></a>Parametry serwera
Azure Database for MariaDB obsługuje dostrajanie wartości parametrów serwera. Minimalna i maksymalna wartość niektórych parametrów jest określana na podstawie warstwy cenowej i rdzeni wirtualnych serwera. Aby uzyskać więcej informacji na temat tych limitów, zapoznaj się z [parametrami serwera](./concepts-server-parameters.md) . 

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
