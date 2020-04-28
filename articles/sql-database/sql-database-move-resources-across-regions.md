---
title: Jak przenieść zasoby do innego regionu
description: Dowiedz się, jak przenieść Azure SQL Database, elastyczną pulę usługi Azure SQL lub wystąpienie zarządzane Azure SQL do innego regionu.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 06/25/2019
ms.openlocfilehash: 851ef49a5c066f12a95baa54daf5e267cb4278c5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/27/2020
ms.locfileid: "73821431"
---
# <a name="how-to-move-azure-sql-resources-to-another-region"></a>Jak przenieść zasoby SQL platformy Azure do innego regionu

W tym artykule przedstawiono ogólny przepływ pracy służący do przenoszenia Azure SQL Database pojedynczej bazy danych, elastycznej puli i wystąpienia zarządzanego do nowego regionu. 

## <a name="overview"></a>Omówienie

Istnieją różne scenariusze, w których należy przenieść istniejące zasoby Azure SQL z jednego regionu do innego. Na przykład możesz rozszerzyć swoją firmę do nowego regionu i chcieć ją zoptymalizować dla nowej bazy klientów. Lub należy przenieść operacje do innego regionu ze względu na zgodność. Lub platforma Azure wygenerowała nowy region, który zapewnia lepszą bliskość i usprawnia obsługę klientów.  

Ten artykuł zawiera ogólny przepływ pracy służący do przeniesienia zasobów do innego regionu. Przepływ pracy składa się z następujących kroków: 

- Sprawdź wymagania wstępne dotyczące przenoszenia 
- Przygotowanie do przeniesienia zasobów w zakresie
- Monitorowanie procesu przygotowania
- Testowanie procesu przenoszenia
- Zainicjuj rzeczywiste przeniesienie 
- Usuń zasoby z regionu źródłowego 


> [!NOTE]
> Ten artykuł dotyczy migracji w chmurze publicznej platformy Azure lub w ramach tej samej suwerennej chmury. 


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="move-single-database"></a>Przenoszenie pojedynczej bazy danych

### <a name="verify-prerequisites"></a>Weryfikowanie wymagań wstępnych 

1. Utwórz docelowy serwer logiczny dla każdego serwera źródłowego. 
1. Skonfiguruj zaporę z właściwymi wyjątkami przy użyciu [programu PowerShell](scripts/sql-database-create-and-configure-database-powershell.md).  
1. Skonfiguruj serwery logiczne przy użyciu poprawnych nazw logowania. Jeśli nie jesteś administratorem subskrypcji lub administratorem programu SQL Server, skontaktuj się z administratorem w celu przypisania potrzebnych uprawnień. Aby uzyskać więcej informacji, zobacz [jak zarządzać zabezpieczeniami usługi Azure SQL Database po awarii](sql-database-geo-replication-security-config.md). 
1. Jeśli bazy danych są zaszyfrowane za pomocą TDE i używasz własnego klucza szyfrowania w usłudze Azure Key, upewnij się, że zainicjowano prawidłowy materiał szyfrowania w regionach docelowych. Aby uzyskać więcej informacji, zobacz [Azure SQL transparent Data Encryption z kluczami zarządzanymi przez klienta w programie Azure Key Vault](transparent-data-encryption-byok-azure-sql.md)
1. Jeśli włączono inspekcję na poziomie bazy danych, wyłącz ją i Włącz inspekcję na poziomie serwera. Po przejściu w tryb failover Inspekcja na poziomie bazy danych będzie wymagała ruchu między regionami, co nie jest wymagane ani możliwe po przeniesieniu. 
1. W przypadku inspekcji na poziomie serwera upewnij się, że:
   - Kontener magazynu, Log Analytics lub centrum zdarzeń z istniejącymi dziennikami inspekcji jest przenoszony do regionu docelowego. 
   - Inspekcja jest konfigurowana na serwerze docelowym. Aby uzyskać więcej informacji, zobacz Rozpoczynanie [pracy z inspekcją bazy danych SQL](sql-database-auditing.md). 
1. Jeśli wystąpienie ma zasady przechowywania długoterminowego (LTR), istniejące kopie zapasowe w tym miejscu pozostaną skojarzone z bieżącym serwerem. Ponieważ serwer docelowy jest inny, będzie można uzyskać dostęp do starszych kopii zapasowych LTR w regionie źródłowym przy użyciu serwera źródłowego, nawet jeśli serwer został usunięty. 

  > [!NOTE]
  > Ta wartość będzie niewystarczająca do przeniesienia między niesuwerenną chmurą a regionem publicznym. Taka migracja wymaga przeniesienia kopii zapasowych LTR na serwer docelowy, co nie jest obecnie obsługiwane. 

### <a name="prepare-resources"></a>Przygotowywanie zasobów

1. Utwórz [grupę trybu failover](sql-database-single-database-failover-group-tutorial.md#2---create-the-failover-group) między serwerem logicznym źródła a serwerem logicznym obiektu docelowego.  
1. Dodaj bazy danych, które chcesz przenieść do grupy trybu failover. 
    - Replikacja wszystkich dodanych baz danych zostanie zainicjowana automatycznie. Aby uzyskać więcej informacji, zobacz [najlepsze rozwiązania dotyczące korzystania z grup trybu failover z pojedynczymi bazami danych](sql-database-auto-failover-group.md#best-practices-of-using-failover-groups-with-single-databases-and-elastic-pools). 
 
### <a name="monitor-the-preparation-process"></a>Monitorowanie procesu przygotowania

Można okresowo wywołać [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) , aby monitorować replikację baz danych z lokalizacji źródłowej do docelowej. Obiekt danych wyjściowych `Get-AzSqlDatabaseFailoverGroup` zawiera właściwość dla **ReplicationState**: 
   - **ReplicationState = 2** (CATCH_UP) wskazuje, że baza danych jest zsynchronizowana i może być bezpiecznie przełączona w tryb failover. 
   - **ReplicationState = 0** (inicjator) wskazuje, że baza danych nie została jeszcze zainicjowana, a próba przełączenia w tryb failover zakończy się niepowodzeniem. 

### <a name="test-synchronization"></a>Synchronizacja testowa

Gdy **ReplicationState** to `2`, Połącz się z każdą bazą danych lub podzbiorem baz danych przy `<fog-name>.secondary.database.windows.net` użyciu pomocniczego punktu końcowego i wykonaj dowolne zapytanie dotyczące baz danych, aby zapewnić łączność, właściwą konfigurację zabezpieczeń i replikację danych. 

### <a name="initiate-the-move"></a>Inicjowanie przenoszenia

1. Połącz się z serwerem docelowym przy użyciu pomocniczego `<fog-name>.secondary.database.windows.net`punktu końcowego.
1. Aby przełączyć pomocnicze wystąpienie zarządzane z pełną synchronizacją, należy użyć [przełącznika-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup) . Ta operacja zakończy się powodzeniem lub zostanie wycofana. 
1. Sprawdź, czy polecenie zostało pomyślnie zakończone przy użyciu `nslook up <fog-name>.secondary.database.windows.net` , aby upewnić się, że wpis CNAME DNS wskazuje na adres IP regionu docelowego. Jeśli polecenie Switch zakończy się niepowodzeniem, rekord CNAME nie zostanie zaktualizowany. 

### <a name="remove-the-source-databases"></a>Usuń źródłowe bazy danych

Po zakończeniu przenoszenia Usuń zasoby w regionie źródłowym, aby uniknąć niepotrzebnych opłat. 

1. Usuń grupę trybu failover za pomocą polecenia [Remove-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup). 
1. Usuń każdą źródłową bazę danych za pomocą polecenia [Remove-AzSqlDatabase](/powershell/module/az.sql/remove-azsqldatabase) dla każdej bazy danych na serwerze źródłowym. Spowoduje to automatyczne zakończenie linków replikacji geograficznej. 
1. Usuń serwer źródłowy za pomocą polecenia [Remove-AzSqlServer](/powershell/module/az.sql/remove-azsqlserver). 
1. Usuń Magazyn kluczy, Przeprowadź inspekcję kontenerów magazynu, centrum zdarzeń, wystąpienie usługi AAD i inne zależne zasoby, aby zatrzymać ich rozliczanie. 

## <a name="move-elastic-pools"></a>Przenoszenie pul elastycznych

### <a name="verify-prerequisites"></a>Weryfikowanie wymagań wstępnych 

1. Utwórz docelowy serwer logiczny dla każdego serwera źródłowego. 
1. Skonfiguruj zaporę z właściwymi wyjątkami przy użyciu [programu PowerShell](scripts/sql-database-create-and-configure-database-powershell.md). 
1. Skonfiguruj serwery logiczne przy użyciu poprawnych nazw logowania. Jeśli nie jesteś administratorem subskrypcji lub administratorem programu SQL Server, skontaktuj się z administratorem w celu przypisania potrzebnych uprawnień. Aby uzyskać więcej informacji, zobacz [jak zarządzać zabezpieczeniami usługi Azure SQL Database po awarii](sql-database-geo-replication-security-config.md). 
1. Jeśli bazy danych są szyfrowane za pomocą TDE i używasz własnego klucza szyfrowania w usłudze Azure Key, upewnij się, że zainicjowano prawidłowy materiał szyfrowania w regionie docelowym.
1. Utwórz docelową pulę elastyczną dla każdej źródłowej puli elastycznej, aby upewnić się, że pula została utworzona w tej samej warstwie usług o tej samej nazwie i o takim samym rozmiarze. 
1. Jeśli inspekcja na poziomie bazy danych jest włączona, należy ją wyłączyć i włączyć inspekcję na poziomie serwera. Po przejściu w tryb failover Inspekcja na poziomie bazy danych będzie wymagała ruchu między regionami, co nie jest wymagane ani możliwe po przeniesieniu. 
1. W przypadku inspekcji na poziomie serwera upewnij się, że:
    - Kontener magazynu, Log Analytics lub centrum zdarzeń z istniejącymi dziennikami inspekcji jest przenoszony do regionu docelowego.
    - Konfiguracja inspekcji została skonfigurowana na serwerze docelowym. Aby uzyskać więcej informacji, zobacz [Inspekcja usługi SQL Database](sql-database-auditing.md).
1. Jeśli wystąpienie ma zasady przechowywania długoterminowego (LTR), istniejące kopie zapasowe w tym miejscu pozostaną skojarzone z bieżącym serwerem. Ponieważ serwer docelowy jest inny, będzie można uzyskać dostęp do starszych kopii zapasowych LTR w regionie źródłowym przy użyciu serwera źródłowego, nawet jeśli serwer został usunięty. 

  > [!NOTE]
  > Ta wartość będzie niewystarczająca do przeniesienia między niesuwerenną chmurą a regionem publicznym. Taka migracja wymaga przeniesienia kopii zapasowych LTR na serwer docelowy, co nie jest obecnie obsługiwane. 

### <a name="prepare-to-move"></a>Przygotuj do przeniesienia
 
1.  Utwórz oddzielną [grupę trybu failover](sql-database-elastic-pool-failover-group-tutorial.md#3---create-the-failover-group) między każdą elastyczną pulą na źródłowym serwerze logicznym i odpowiadającą jej pulę elastyczną na serwerze docelowym. 
1.  Dodaj wszystkie bazy danych w puli do grupy trybu failover. 
    - Replikacja dodanych baz danych zostanie zainicjowana automatycznie. Aby uzyskać więcej informacji, zobacz [najlepsze rozwiązania dotyczące grup trybu failover z elastycznymi pulami](sql-database-auto-failover-group.md#best-practices-of-using-failover-groups-with-single-databases-and-elastic-pools). 

  > [!NOTE]
  > Chociaż istnieje możliwość utworzenia grupy trybu failover, która obejmuje wiele pul elastycznych, zdecydowanie zalecamy utworzenie osobnej grupy trybu failover dla każdej puli. Jeśli masz dużą liczbę baz danych między wieloma pulami elastycznymi, które należy przenieść, możesz uruchomić kroki przygotowania równolegle, a następnie zainicjować równolegle etap przenoszenia. Ten proces będzie lepszy do skalowania i trwa krótszy czas porównywany z wieloma pulami elastycznymi w tej samej grupie trybu failover. 

### <a name="monitor-the-preparation-process"></a>Monitorowanie procesu przygotowania

Można okresowo wywołać [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) , aby monitorować replikację baz danych z lokalizacji źródłowej do docelowej. Obiekt danych wyjściowych `Get-AzSqlDatabaseFailoverGroup` zawiera właściwość dla **ReplicationState**: 
   - **ReplicationState = 2** (CATCH_UP) wskazuje, że baza danych jest zsynchronizowana i może być bezpiecznie przełączona w tryb failover. 
   - **ReplicationState = 0** (inicjator) wskazuje, że baza danych nie została jeszcze zainicjowana, a próba przełączenia w tryb failover zakończy się niepowodzeniem. 

### <a name="test-synchronization"></a>Synchronizacja testowa
 
Gdy **ReplicationState** to `2`, Połącz się z każdą bazą danych lub podzbiorem baz danych przy `<fog-name>.secondary.database.windows.net` użyciu pomocniczego punktu końcowego i wykonaj dowolne zapytanie dotyczące baz danych, aby zapewnić łączność, właściwą konfigurację zabezpieczeń i replikację danych. 

### <a name="initiate-the-move"></a>Inicjowanie przenoszenia
 
1. Połącz się z serwerem docelowym przy użyciu pomocniczego `<fog-name>.secondary.database.windows.net`punktu końcowego.
1. Aby przełączyć pomocnicze wystąpienie zarządzane z pełną synchronizacją, należy użyć [przełącznika-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup) . Ta operacja zakończy się powodzeniem lub zostanie wycofana. 
1. Sprawdź, czy polecenie zostało pomyślnie zakończone przy użyciu `nslook up <fog-name>.secondary.database.windows.net` , aby upewnić się, że wpis CNAME DNS wskazuje na adres IP regionu docelowego. Jeśli polecenie Switch zakończy się niepowodzeniem, rekord CNAME nie zostanie zaktualizowany. 

### <a name="remove-the-source-elastic-pools"></a>Usuń źródłowe pule elastyczne
 
Po zakończeniu przenoszenia Usuń zasoby w regionie źródłowym, aby uniknąć niepotrzebnych opłat. 

1. Usuń grupę trybu failover za pomocą polecenia [Remove-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup).
1. Usuń każdą źródłową pulę elastyczną na serwerze źródłowym za pomocą polecenia [Remove-AzSqlElasticPool](/powershell/module/az.sql/remove-azsqlelasticpool). 
1. Usuń serwer źródłowy za pomocą polecenia [Remove-AzSqlServer](/powershell/module/az.sql/remove-azsqlserver). 
1. Usuń Magazyn kluczy, Przeprowadź inspekcję kontenerów magazynu, centrum zdarzeń, wystąpienie usługi AAD i inne zależne zasoby, aby zatrzymać ich rozliczanie. 

## <a name="move-managed-instance"></a>Przenieś wystąpienie zarządzane

### <a name="verify-prerequisites"></a>Weryfikowanie wymagań wstępnych
 
1. Dla każdego źródłowego wystąpienia zarządzanego Utwórz docelowe wystąpienie zarządzane o takim samym rozmiarze w regionie docelowym.  
1. Skonfiguruj sieć dla wystąpienia zarządzanego. Aby uzyskać więcej informacji, zobacz [Konfiguracja sieci](sql-database-howto-managed-instance.md#network-configuration).
1. Skonfiguruj docelową bazę danych Master przy użyciu poprawnych nazw logowania. Jeśli nie jesteś administratorem subskrypcji lub administratorem programu SQL Server, skontaktuj się z administratorem w celu przypisania potrzebnych uprawnień. 
1. Jeśli bazy danych są szyfrowane za pomocą TDE i używasz własnego klucza szyfrowania w usłudze Azure Key, upewnij się, że AKV z identycznymi kluczami szyfrowania istnieją w regionach źródłowym i docelowym. Aby uzyskać więcej informacji, zobacz [TDE z kluczami zarządzanymi przez klienta w Azure Key Vault](transparent-data-encryption-byok-azure-sql.md).
1. Jeśli inspekcja jest włączona dla tego wystąpienia, upewnij się, że:
    - Kontener magazynu lub centrum zdarzeń z istniejącymi dziennikami jest przenoszony do regionu docelowego. 
    - Inspekcja jest konfigurowana w wystąpieniu docelowym. Aby uzyskać więcej informacji, zobacz [inspekcja z wystąpieniem zarządzanym](sql-database-managed-instance-auditing.md).
1. Jeśli wystąpienie ma zasady przechowywania długoterminowego (LTR), istniejące kopie zapasowe w tym miejscu pozostaną skojarzone z bieżącym serwerem. Ponieważ serwer docelowy jest inny, będzie można uzyskać dostęp do starszych kopii zapasowych LTR w regionie źródłowym przy użyciu serwera źródłowego, nawet jeśli serwer został usunięty. 

  > [!NOTE]
  > Ta wartość będzie niewystarczająca do przeniesienia między niesuwerenną chmurą a regionem publicznym. Taka migracja wymaga przeniesienia kopii zapasowych LTR na serwer docelowy, co nie jest obecnie obsługiwane. 

### <a name="prepare-resources"></a>Przygotowywanie zasobów

Utwórz grupę trybu failover między każdym wystąpieniem źródłowym i odpowiednim wystąpieniem docelowym.
    - Replikacja wszystkich baz danych w każdym wystąpieniu zostanie zainicjowana automatycznie. Aby uzyskać więcej informacji, zobacz [grupy z obsługą trybu failover](sql-database-auto-failover-group.md) .

 
### <a name="monitor-the-preparation-process"></a>Monitorowanie procesu przygotowania

Można okresowo wywołać [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup?view=azps-2.3.2) , aby monitorować replikację baz danych z lokalizacji źródłowej do docelowej. Obiekt danych wyjściowych `Get-AzSqlDatabaseFailoverGroup` zawiera właściwość dla **ReplicationState**: 
   - **ReplicationState = 2** (CATCH_UP) wskazuje, że baza danych jest zsynchronizowana i może być bezpiecznie przełączona w tryb failover. 
   - **ReplicationState = 0** (inicjator) wskazuje, że baza danych nie została jeszcze zainicjowana, a próba przełączenia w tryb failover zakończy się niepowodzeniem. 

### <a name="test-synchronization"></a>Synchronizacja testowa

Gdy **ReplicationState** to `2`, Połącz się z każdą bazą danych lub podzbiorem baz danych przy `<fog-name>.secondary.database.windows.net` użyciu pomocniczego punktu końcowego i wykonaj dowolne zapytanie dotyczące baz danych, aby zapewnić łączność, właściwą konfigurację zabezpieczeń i replikację danych. 

### <a name="initiate-the-move"></a>Inicjowanie przenoszenia 

1. Połącz się z serwerem docelowym przy użyciu pomocniczego `<fog-name>.secondary.database.windows.net`punktu końcowego.
1. Aby przełączyć pomocnicze wystąpienie zarządzane z pełną synchronizacją, należy użyć [przełącznika-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup?view=azps-2.3.2) . Ta operacja zakończy się powodzeniem lub zostanie wycofana. 
1. Sprawdź, czy polecenie zostało pomyślnie zakończone przy użyciu `nslook up <fog-name>.secondary.database.windows.net` , aby upewnić się, że wpis CNAME DNS wskazuje na adres IP regionu docelowego. Jeśli polecenie Switch zakończy się niepowodzeniem, rekord CNAME nie zostanie zaktualizowany. 

### <a name="remove-the-source-managed-instances"></a>Usuń źródłowe wystąpienia zarządzane
Po zakończeniu przenoszenia Usuń zasoby w regionie źródłowym, aby uniknąć niepotrzebnych opłat. 

1. Usuń grupę trybu failover za pomocą polecenia [Remove-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup). Spowoduje to usunięcie konfiguracji grupy trybu failover i zakończenie linków replikacji geograficznej między tymi dwoma wystąpieniami. 
1. Usuń źródłowe wystąpienie zarządzane za pomocą polecenia [Remove-AzSqlInstance](/powershell/module/az.sql/remove-azsqlinstance). 
1. Usuń dodatkowe zasoby z grupy zasobów, takie jak klaster wirtualny, Sieć wirtualna i Grupa zabezpieczeń. 

## <a name="next-steps"></a>Następne kroki 

[Zarządzaj](sql-database-manage-after-migration.md) Azure SQL Database po migracji. 

