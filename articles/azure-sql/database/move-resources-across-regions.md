---
title: Przenoszenie zasobów do nowego regionu
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: Dowiedz się, jak przenieść bazę danych lub wystąpienie zarządzane do innego regionu.
services: sql-database
ms.service: sql-db-mi
ms.subservice: data-movement
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 06/25/2019
ms.openlocfilehash: ae6c87c9eabea837ba9c43676d4ca712caa385cb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94594168"
---
# <a name="move-resources-to-new-region---azure-sql-database--azure-sql-managed-instance"></a>Przenoszenie zasobów do nowego regionu — Azure SQL Database & wystąpienie zarządzane Azure SQL
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

W tym artykule przedstawiono ogólny przepływ pracy służący do przenoszenia bazy danych lub wystąpienia zarządzanego do nowego regionu.

## <a name="overview"></a>Omówienie

Istnieją różne scenariusze, w których należy przenieść istniejącą bazę danych lub wystąpienie zarządzane z jednego regionu do innego. Na przykład rozszerzasz swoją firmę do nowego regionu i chcesz ją zoptymalizować pod kątem nowej bazy klientów. Lub należy przenieść operacje do innego regionu ze względu na zgodność. Lub platforma Azure wygenerowała nowy region, który zapewnia lepszą bliskość i usprawnia obsługę klientów.  

Ten artykuł zawiera ogólny przepływ pracy służący do przeniesienia zasobów do innego regionu. Przepływ pracy składa się z następujących kroków:

1. Sprawdź wymagania wstępne dotyczące przenoszenia.
1. Przygotowanie do przenoszenia zasobów w zakresie.
1. Monitoruj proces przygotowywania.
1. Przetestuj proces przenoszenia.
1. Zainicjuj rzeczywiste przeniesienie.
1. Usuń zasoby z regionu źródłowego.

> [!NOTE]
> Ten artykuł dotyczy migracji w chmurze publicznej platformy Azure lub w ramach tej samej suwerennej chmury.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="move-a-database"></a>Przenoszenie bazy danych

### <a name="verify-prerequisites"></a>Weryfikacja wymagań wstępnych

1. Utwórz serwer docelowy dla każdego serwera źródłowego.
1. Skonfiguruj zaporę z właściwymi wyjątkami za pomocą [programu PowerShell](scripts/create-and-configure-database-powershell.md).  
1. Skonfiguruj serwery przy użyciu poprawnych nazw logowania. Jeśli nie jesteś administratorem subskrypcji lub administratorem programu SQL Server, skontaktuj się z administratorem w celu przypisania potrzebnych uprawnień. Aby uzyskać więcej informacji, zobacz [jak zarządzać zabezpieczeniami Azure SQL Database po odzyskiwaniu po awarii](active-geo-replication-security-configure.md).
1. Jeśli bazy danych są szyfrowane przy użyciu przezroczystego szyfrowania danych i używasz własnego klucza szyfrowania w Azure Key Vault, upewnij się, że zainicjowano poprawny materiał szyfrowania w regionach docelowych. Aby uzyskać więcej informacji, zobacz [usługa Azure SQL transparent Data Encryption z kluczami zarządzanymi przez klienta w Azure Key Vault](transparent-data-encryption-byok-overview.md).
1. Jeśli włączono inspekcję na poziomie bazy danych, wyłącz ją i Włącz inspekcję na poziomie serwera. Po przejściu w tryb failover Inspekcja na poziomie bazy danych będzie wymagała ruchu między regionami, co nie jest wymagane ani możliwe po przeniesieniu.
1. W przypadku inspekcji na poziomie serwera upewnij się, że:
   - Kontener magazynu, Log Analytics lub centrum zdarzeń z istniejącymi dziennikami inspekcji jest przenoszony do regionu docelowego.
   - Inspekcja jest konfigurowana na serwerze docelowym. Aby uzyskać więcej informacji, zobacz Rozpoczynanie [pracy z inspekcją SQL Database](../../azure-sql/database/auditing-overview.md).
1. Jeśli wystąpienie ma zasady przechowywania długoterminowego (LTR), istniejące kopie zapasowe w tym miejscu pozostaną skojarzone z bieżącym serwerem. Ponieważ serwer docelowy jest inny, można uzyskać dostęp do starszych kopii zapasowych LTR w regionie źródłowym przy użyciu serwera źródłowego, nawet jeśli serwer został usunięty.

      > [!NOTE]
      > Ta wartość będzie niewystarczająca do przeniesienia między niesuwerenną chmurą a regionem publicznym. Taka migracja wymaga przeniesienia kopii zapasowych LTR na serwer docelowy, co nie jest obecnie obsługiwane.

### <a name="prepare-resources"></a>Przygotowywanie zasobów

1. Utwórz [grupę trybu failover](failover-group-add-single-database-tutorial.md#2---create-the-failover-group) między serwerem źródłowym a serwerem docelowym.  
1. Dodaj bazy danych, które chcesz przenieść do grupy trybu failover.
  
    Replikacja wszystkich dodanych baz danych zostanie zainicjowana automatycznie. Aby uzyskać więcej informacji, zobacz [najlepsze rozwiązania dotyczące korzystania z grup trybu failover z pojedynczymi bazami danych](auto-failover-group-overview.md#best-practices-for-sql-database).

### <a name="monitor-the-preparation-process"></a>Monitorowanie procesu przygotowania

Można okresowo wywołać [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) , aby monitorować replikację baz danych z lokalizacji źródłowej do docelowej. Obiekt danych wyjściowych `Get-AzSqlDatabaseFailoverGroup` zawiera właściwość dla **ReplicationState**:

- **ReplicationState = 2** (CATCH_UP) wskazuje, że baza danych jest zsynchronizowana i może być bezpiecznie przełączona w tryb failover.
- **ReplicationState = 0** (inicjator) wskazuje, że baza danych nie została jeszcze zainicjowana, a próba przełączenia w tryb failover zakończy się niepowodzeniem.

### <a name="test-synchronization"></a>Synchronizacja testowa

Po **ReplicationState** `2` , Połącz się z każdą bazą danych lub podzbiorem baz danych przy użyciu pomocniczego punktu końcowego `<fog-name>.secondary.database.windows.net` i wykonaj wszelkie zapytania dotyczące baz danych, aby zapewnić łączność, właściwą konfigurację zabezpieczeń i replikację danych.

### <a name="initiate-the-move"></a>Inicjowanie przenoszenia

1. Połącz się z serwerem docelowym przy użyciu pomocniczego punktu końcowego `<fog-name>.secondary.database.windows.net` .
1. Aby przełączyć pomocnicze wystąpienie zarządzane z pełną synchronizacją, należy użyć [przełącznika-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup) . Ta operacja zakończy się pomyślnie lub zostanie wycofana.
1. Sprawdź, czy polecenie zostało pomyślnie zakończone przy użyciu, `nslook up <fog-name>.secondary.database.windows.net` Aby upewnić się, że wpis CNAME DNS wskazuje na adres IP regionu docelowego. Jeśli polecenie Switch zakończy się niepowodzeniem, rekord CNAME nie zostanie zaktualizowany.

### <a name="remove-the-source-databases"></a>Usuń źródłowe bazy danych

Po zakończeniu przenoszenia Usuń zasoby w regionie źródłowym, aby uniknąć niepotrzebnych opłat.

1. Usuń grupę trybu failover za pomocą polecenia [Remove-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup).
1. Usuń każdą źródłową bazę danych za pomocą polecenia [Remove-AzSqlDatabase](/powershell/module/az.sql/remove-azsqldatabase) dla każdej bazy danych na serwerze źródłowym. Spowoduje to automatyczne zakończenie linków replikacji geograficznej.
1. Usuń serwer źródłowy za pomocą polecenia [Remove-AzSqlServer](/powershell/module/az.sql/remove-azsqlserver).
1. Usuń Magazyn kluczy, Przeprowadź inspekcję kontenerów magazynu, centrum zdarzeń, wystąpienie Azure Active Directory (Azure AD) i inne zależne zasoby, aby zatrzymać ich rozliczanie.

## <a name="move-elastic-pools"></a>Przenoszenie pul elastycznych

### <a name="verify-prerequisites"></a>Weryfikacja wymagań wstępnych

1. Utwórz serwer docelowy dla każdego serwera źródłowego.
1. Skonfiguruj zaporę z właściwymi wyjątkami przy użyciu [programu PowerShell](scripts/create-and-configure-database-powershell.md).
1. Skonfiguruj serwery przy użyciu poprawnych nazw logowania. Jeśli nie jesteś administratorem subskrypcji lub administratorem serwera, skontaktuj się z administratorem w celu przypisania potrzebnych uprawnień. Aby uzyskać więcej informacji, zobacz [jak zarządzać zabezpieczeniami Azure SQL Database po odzyskiwaniu po awarii](active-geo-replication-security-configure.md).
1. Jeśli bazy danych są szyfrowane przy użyciu przezroczystego szyfrowania danych i używasz własnego klucza szyfrowania w Azure Key Vault, upewnij się, że zainicjowano poprawny materiał szyfrowania w regionie docelowym.
1. Utwórz docelową pulę elastyczną dla każdej źródłowej puli elastycznej, aby upewnić się, że pula została utworzona w tej samej warstwie usług o tej samej nazwie i o takim samym rozmiarze.
1. Jeśli inspekcja na poziomie bazy danych jest włączona, należy ją wyłączyć i włączyć inspekcję na poziomie serwera. Po przejściu w tryb failover Inspekcja na poziomie bazy danych będzie wymagała ruchu między regionami, co nie jest wymagane ani możliwe po przeniesieniu.
1. W przypadku inspekcji na poziomie serwera upewnij się, że:
    - Kontener magazynu, Log Analytics lub centrum zdarzeń z istniejącymi dziennikami inspekcji jest przenoszony do regionu docelowego.
    - Konfiguracja inspekcji została skonfigurowana na serwerze docelowym. Aby uzyskać więcej informacji, zobacz [inspekcja SQL Database](../../azure-sql/database/auditing-overview.md).
1. Jeśli wystąpienie ma zasady przechowywania długoterminowego (LTR), istniejące kopie zapasowe w tym miejscu pozostaną skojarzone z bieżącym serwerem. Ponieważ serwer docelowy jest inny, można uzyskać dostęp do starszych kopii zapasowych LTR w regionie źródłowym przy użyciu serwera źródłowego, nawet jeśli serwer został usunięty.

      > [!NOTE]
      > Ta wartość będzie niewystarczająca do przeniesienia między niesuwerenną chmurą a regionem publicznym. Taka migracja wymaga przeniesienia kopii zapasowych LTR na serwer docelowy, co nie jest obecnie obsługiwane.

### <a name="prepare-to-move"></a>Przygotuj do przeniesienia

1. Utwórz oddzielną [grupę trybu failover](failover-group-add-elastic-pool-tutorial.md#3---create-the-failover-group) między każdą elastyczną pulą na serwerze źródłowym i odpowiadającą jej pulę elastyczną na serwerze docelowym.
1. Dodaj wszystkie bazy danych w puli do grupy trybu failover.

    Replikacja dodanych baz danych zostanie zainicjowana automatycznie. Aby uzyskać więcej informacji, zobacz [najlepsze rozwiązania dotyczące grup trybu failover z elastycznymi pulami](auto-failover-group-overview.md#best-practices-for-sql-database).

      > [!NOTE]
      > Chociaż istnieje możliwość utworzenia grupy trybu failover, która obejmuje wiele pul elastycznych, zdecydowanie zalecamy utworzenie osobnej grupy trybu failover dla każdej puli. Jeśli masz dużą liczbę baz danych między wieloma pulami elastycznymi, które należy przenieść, możesz uruchomić kroki przygotowania równolegle, a następnie zainicjować równolegle etap przenoszenia. Ten proces będzie lepszy do skalowania i trwa krótszy czas porównywany z wieloma pulami elastycznymi w tej samej grupie trybu failover.

### <a name="monitor-the-preparation-process"></a>Monitorowanie procesu przygotowania

Można okresowo wywołać [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) , aby monitorować replikację baz danych z lokalizacji źródłowej do docelowej. Obiekt danych wyjściowych `Get-AzSqlDatabaseFailoverGroup` zawiera właściwość dla **ReplicationState**:

- **ReplicationState = 2** (CATCH_UP) wskazuje, że baza danych jest zsynchronizowana i może być bezpiecznie przełączona w tryb failover.
- **ReplicationState = 0** (inicjator) wskazuje, że baza danych nie została jeszcze zainicjowana, a próba przełączenia w tryb failover zakończy się niepowodzeniem.

### <a name="test-synchronization"></a>Synchronizacja testowa

Gdy **ReplicationState** jest `2` , Połącz się z każdą bazą danych lub podzbiorem baz danych przy użyciu pomocniczego punktu końcowego `<fog-name>.secondary.database.windows.net` i wykonaj dowolne zapytanie dotyczące baz danych, aby zapewnić łączność, właściwą konfigurację zabezpieczeń i replikację danych.

### <a name="initiate-the-move"></a>Inicjowanie przenoszenia

1. Połącz się z serwerem docelowym przy użyciu pomocniczego punktu końcowego `<fog-name>.secondary.database.windows.net` .
1. Aby przełączyć pomocnicze wystąpienie zarządzane z pełną synchronizacją, należy użyć [przełącznika-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup) . Ta operacja zakończy się powodzeniem lub zostanie wycofana.
1. Sprawdź, czy polecenie zostało pomyślnie zakończone przy użyciu, `nslook up <fog-name>.secondary.database.windows.net` Aby upewnić się, że wpis CNAME DNS wskazuje na adres IP regionu docelowego. Jeśli polecenie Switch zakończy się niepowodzeniem, rekord CNAME nie zostanie zaktualizowany.

### <a name="remove-the-source-elastic-pools"></a>Usuń źródłowe pule elastyczne

Po zakończeniu przenoszenia Usuń zasoby w regionie źródłowym, aby uniknąć niepotrzebnych opłat.

1. Usuń grupę trybu failover za pomocą polecenia [Remove-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup).
1. Usuń każdą źródłową pulę elastyczną na serwerze źródłowym za pomocą polecenia [Remove-AzSqlElasticPool](/powershell/module/az.sql/remove-azsqlelasticpool).
1. Usuń serwer źródłowy za pomocą polecenia [Remove-AzSqlServer](/powershell/module/az.sql/remove-azsqlserver).
1. Usuń Magazyn kluczy, Przeprowadź inspekcję kontenerów magazynu, centrum zdarzeń, wystąpienie usługi Azure AD i inne zależne zasoby, aby zatrzymać ich naliczanie.

## <a name="move-a-managed-instance"></a>Przenoszenie wystąpienia zarządzanego

### <a name="verify-prerequisites"></a>Weryfikacja wymagań wstępnych

1. Dla każdego wystąpienia zarządzanego źródła Utwórz wystąpienie docelowe wystąpienia zarządzanego SQL o takim samym rozmiarze w regionie docelowym.  
1. Skonfiguruj sieć dla wystąpienia zarządzanego. Aby uzyskać więcej informacji, zobacz [Konfiguracja sieci](../managed-instance/how-to-content-reference-guide.md#network-configuration).
1. Skonfiguruj docelową bazę danych Master przy użyciu poprawnych nazw logowania. W przypadku braku subskrypcji lub administratora wystąpienia zarządzanego SQL należy skontaktować się z administratorem w celu przypisania potrzebnych uprawnień.
1. Jeśli bazy danych są szyfrowane za pomocą funkcji przezroczystego szyfrowania danych i używasz własnego klucza szyfrowania w Azure Key Vault, upewnij się, że Azure Key Vault z identycznymi kluczami szyfrowania istnieją w regionach źródłowym i docelowym. Aby uzyskać więcej informacji, zobacz [przezroczyste szyfrowanie danych z kluczami zarządzanymi przez klienta w programie Azure Key Vault](transparent-data-encryption-byok-overview.md).
1. Jeśli inspekcja jest włączona dla wystąpienia zarządzanego, należy się upewnić, że:
    - Kontener magazynu lub centrum zdarzeń z istniejącymi dziennikami jest przenoszony do regionu docelowego.
    - Inspekcja jest konfigurowana w wystąpieniu docelowym. Aby uzyskać więcej informacji, zobacz [Inspekcja przy użyciu wystąpienia zarządzanego SQL](../managed-instance/auditing-configure.md).
1. Jeśli wystąpienie ma zasady przechowywania długoterminowego (LTR), istniejące kopie zapasowe w tym miejscu pozostaną skojarzone z bieżącym wystąpieniem. Ponieważ wystąpienie docelowe jest inne, można uzyskać dostęp do starszych kopii zapasowych LTR w regionie źródłowym przy użyciu wystąpienia źródłowego, nawet jeśli wystąpienie zostanie usunięte.

  > [!NOTE]
  > Ta wartość będzie niewystarczająca do przeniesienia między niesuwerenną chmurą a regionem publicznym. Taka migracja wymaga przeniesienia kopii zapasowych LTR do wystąpienia docelowego, co nie jest obecnie obsługiwane.

### <a name="prepare-resources"></a>Przygotowywanie zasobów

Utwórz grupę trybu failover między poszczególnymi wystąpieniami zarządzanymi i odpowiednim docelowym wystąpieniem wystąpienia zarządzanego SQL.

Replikacja wszystkich baz danych w każdym wystąpieniu zostanie zainicjowana automatycznie. Aby uzyskać więcej informacji, zobacz [grupy autopraca awaryjna](auto-failover-group-overview.md).

### <a name="monitor-the-preparation-process"></a>Monitorowanie procesu przygotowania

Można okresowo wywołać [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) , aby monitorować replikację baz danych z lokalizacji źródłowej do docelowej. Obiekt danych wyjściowych `Get-AzSqlDatabaseFailoverGroup` zawiera właściwość dla **ReplicationState**:

- **ReplicationState = 2** (CATCH_UP) wskazuje, że baza danych jest zsynchronizowana i może być bezpiecznie przełączona w tryb failover.
- **ReplicationState = 0** (inicjator) wskazuje, że baza danych nie została jeszcze zainicjowana, a próba przełączenia w tryb failover zakończy się niepowodzeniem.

### <a name="test-synchronization"></a>Synchronizacja testowa

Gdy **ReplicationState** to `2` , Połącz się z każdą bazą danych lub podzbiorem baz danych przy użyciu pomocniczego punktu końcowego `<fog-name>.secondary.database.windows.net` i wykonaj dowolne zapytanie dotyczące baz danych, aby zapewnić łączność, właściwą konfigurację zabezpieczeń i replikację danych.

### <a name="initiate-the-move"></a>Inicjowanie przenoszenia

1. Nawiąż połączenie z docelowym wystąpieniem zarządzanym przy użyciu pomocniczego punktu końcowego `<fog-name>.secondary.database.windows.net` .
1. Aby przełączyć pomocnicze wystąpienie zarządzane z pełną synchronizacją, należy użyć [przełącznika-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup) . Ta operacja zakończy się powodzeniem lub zostanie wycofana.
1. Sprawdź, czy polecenie zostało pomyślnie zakończone przy użyciu, `nslook up <fog-name>.secondary.database.windows.net` Aby upewnić się, że wpis CNAME DNS wskazuje na adres IP regionu docelowego. Jeśli polecenie Switch zakończy się niepowodzeniem, rekord CNAME nie zostanie zaktualizowany.

### <a name="remove-the-source-managed-instances"></a>Usuń źródłowe wystąpienia zarządzane

Po zakończeniu przenoszenia Usuń zasoby w regionie źródłowym, aby uniknąć niepotrzebnych opłat.

1. Usuń grupę trybu failover za pomocą polecenia [Remove-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup). Spowoduje to usunięcie konfiguracji grupy trybu failover i zakończenie linków replikacji geograficznej między tymi dwoma wystąpieniami.
1. Usuń źródłowe wystąpienie zarządzane za pomocą polecenia [Remove-AzSqlInstance](/powershell/module/az.sql/remove-azsqlinstance).
1. Usuń dodatkowe zasoby z grupy zasobów, takie jak klaster wirtualny, Sieć wirtualna i Grupa zabezpieczeń.

## <a name="next-steps"></a>Następne kroki

[Zarządzanie](manage-data-after-migrating-to-database.md) bazą danych po jej migracji.
