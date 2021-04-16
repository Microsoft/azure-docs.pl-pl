---
title: 'SQL Server do Azure SQL Managed Instance: Punkt odniesienia wydajności'
description: Dowiedz się, jak utworzyć i porównać punkt odniesienia wydajności podczas migrowania baz danych SQL Server do Azure SQL Managed Instance.
ms.service: sql-managed-instance
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: mokabiru
ms.date: 11/06/2020
ms.openlocfilehash: a47684bf29f1f34b8c9c59c04b7d33d234505cc2
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389710"
---
# <a name="migration-performance-sql-server-to--azure-sql-managed-instance-performance-baseline"></a>Wydajność migracji: SQL Server do Azure SQL Managed Instance wydajności
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqlmi.md)]

Utwórz punkt odniesienia wydajności, aby porównać wydajność obciążenia na SQL Managed Instance z oryginalnym obciążeniem uruchomionym na SQL Server. 

## <a name="create-a-baseline"></a>Tworzenie linii bazowej

W idealnym przypadku wydajność jest podobna lub lepsza po migracji, dlatego ważne jest, aby zmierzyć i zarejestrować podstawowe wartości wydajności w źródle, a następnie porównać je ze środowiskiem docelowym. Punkt odniesienia wydajności to zestaw parametrów, które definiują średnie obciążenie w źródle. 

Wybierz zestaw zapytań, które są ważne dla, i reprezentatywny dla obciążenia biznesowego. Zmierz i udokumentuj minimalny/średni/maksymalny czas trwania i użycie procesora CPU dla tych zapytań, a także metryki wydajności na serwerze źródłowym, takie jak średnie/maksymalne użycie procesora CPU, średnie/maksymalne opóźnienie we/wy dysku, przepływność, liczba operacji we/wy na minutę, średnia/maksymalna oczekiwana długość strony i średni maksymalny rozmiar bazy danych tempdb. 

Następujące zasoby mogą pomóc w zdefiniowaniu linii bazowej wydajności: 

   - [Monitorowanie użycia procesora CPU ](https://techcommunity.microsoft.com/t5/azure-sql-database/monitor-cpu-usage-on-sql-server-and-azure-sql/ba-p/680777#M131)
   - [Monitorowanie użycia pamięci](/sql/relational-databases/performance-monitor/monitor-memory-usage)   i określają ilość pamięci używanej przez różne składniki, takie jak pula buforów, pamięć podręczna planu, pula magazynów kolumn, przetwarzania [OLTP w pamięci](/sql/relational-databases/in-memory-oltp/monitor-and-troubleshoot-memory-usage)itp. Ponadto należy znaleźć średnie i szczytowe wartości licznika wydajności pamięci oczekiwana długość życia strony. 
   - Monitoruj użycie we/wy dysku w wystąpieniu SQL Server przy [użyciu widoku sys.dm_io_virtual_file_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql)lub    [liczników wydajności.](/sql/relational-databases/performance-monitor/monitor-disk-usage) 
   - Monitoruj wydajność obciążeń i zapytań, sprawdzając dynamiczne widoki zarządzania (lub magazyn zapytań w przypadku migracji z programu SQL Server 2016 i nowszych). Zidentyfikuj średni czas trwania i użycie procesora CPU dla najważniejszych zapytań w obciążeniu. 

Wszelkie problemy z wydajnością na SQL Server należy rozwiązać przed migracją. Migrowanie znanych problemów do dowolnego nowego systemu może spowodować nieoczekiwane wyniki i unieważnić dowolne porównanie wydajności. 


## <a name="compare-performance"></a>Porównanie wydajności 

Po zdefiniowanym punkcie odniesienia porównaj podobną wydajność obciążenia w docelowym SQL Managed Instance. Dla dokładności ważne jest, aby środowisko SQL Managed Instance było porównywalne z SQL Server jak to możliwe. 

Istnieją pewne SQL Managed Instance infrastruktury, które sprawiają, że dopasowanie wydajności jest bardzo mało prawdopodobne. Niektóre zapytania mogą działać szybciej niż oczekiwano, podczas gdy inne mogą być wolniejsze. Celem tego porównania jest sprawdzenie, czy wydajność obciążenia w wystąpieniu zarządzanym jest dopasowana do wydajności na SQL Server (średnio) i zidentyfikowanie wszelkich krytycznych zapytań o wydajności, które nie są zgodne z oryginalną wydajnością. 

Porównanie wydajności prawdopodobnie spowoduje następujące wyniki: 

- Wydajność obciążeń w wystąpieniu zarządzanym jest wyrównana lub lepsza niż wydajność obciążenia w źródle SQL Server. W takim przypadku pomyślnie potwierdzono, że migracja zakończyła się pomyślnie. 

- Większość parametrów wydajności i zapytań w obciążeniu jest działać zgodnie z oczekiwaniami, z pewnymi wyjątkami, co spowoduje obniżenie wydajności. W tym przypadku zidentyfikuj różnice i ich znaczenie. Jeśli istnieją ważne zapytania o obniżonej wydajności, sprawdź, czy bazowe plany SQL uległy zmianie lub czy zapytania osiągają limity zasobów. Można temu pomóc, stosując wskazówki dotyczące zapytań krytycznych (na przykład zmiany poziomu zgodności lub narzędzia do szacowania starszej kardynalności) bezpośrednio lub za pomocą przewodników po planach. Upewnij się, że statystyki i indeksy są aktualne i równoważne w obu środowiskach. 

- Większość zapytań w wystąpieniu zarządzanym jest wolniejsza niż w przypadku wystąpienia SQL Server źródłowego. W takim przypadku spróbuj zidentyfikować główne przyczyny różnicy, takie jak osiągnięcie pewnego limitu zasobów, takiego jak limit liczby dzienników we/wy, pamięci lub wystąpienia. [](../../managed-instance/resource-limits.md#service-tier-characteristics) Jeśli nie ma limitów zasobów powodujących różnicę, spróbuj zmienić poziom zgodności bazy danych lub zmienić ustawienia bazy danych, takie jak szacowanie starszej kardynalności, i ponownie uruchomić test. Zapoznaj się z zaleceniami dostarczonymi przez widoki wystąpienia zarządzanego lub magazynu zapytań, aby zidentyfikować zapytania o wydajności, których wydajność się pogoniła. 

SQL Managed Instance ma wbudowaną funkcję automatycznej korekty planu, która jest domyślnie włączona. Dzięki tej funkcji zapytania, które działały prawidłowo w przeszłości, nie będą w przyszłości ulegać pogorszeniu. Jeśli ta funkcja nie jest włączona, uruchom obciążenie ze starymi ustawieniami, aby SQL Managed Instance poznać punkt odniesienia wydajności. Następnie włącz funkcję i ponownie uruchom obciążenie z nowymi ustawieniami. 

Wprowadzaj zmiany parametrów testu lub uaktualnij do wyższych warstw usług, aby osiągnąć optymalną konfigurację dla wydajności obciążenia, która odpowiada Twoim potrzebom. 

## <a name="monitor-performance"></a>Monitorowanie wydajności 

SQL Managed Instance zaawansowane narzędzia do monitorowania i rozwiązywania problemów. Należy ich używać do monitorowania wydajności wystąpienia. Niektóre z kluczowych metryk do monitorowania to: 

- Użycie procesora CPU w wystąpieniu w celu określenia, czy aprowizowana liczba rdzeni wirtualnych jest odpowiednim dopasowaniem dla obciążenia. 
- Oczekiwana długość życia strony w wystąpieniu zarządzanym w celu określenia, czy potrzebujesz [dodatkowej pamięci.](https://techcommunity.microsoft.com/t5/azure-sql-database/do-you-need-more-memory-on-azure-sql-managed-instance/ba-p/563444)
-  Statystyki, takie jak INSTANCE_LOG_GOVERNOR lub PAGEIOLATCH, identyfikujące problemy z we/wy magazynu, szczególnie w warstwie Ogólnego przeznaczenia, gdzie może być konieczne wstępne przydzielenie plików w celu uzyskania lepszej wydajności we/wy. 


## <a name="considerations"></a>Zagadnienia do rozważenia  

Porównując wydajność, należy wziąć pod uwagę następujące kwestie: 

- Ustawienia są zgodne między źródłem i obiektem docelowym. Sprawdź, czy różne ustawienia wystąpienia, bazy danych i bazy danych tempdb są równoważne w obu środowiskach. Różnice w konfiguracji, poziomach zgodności, ustawieniach szyfrowania, flagach śledzenia itp., mogą zapewnić niesymetryczność wydajności. 

- Magazyn jest konfigurowany zgodnie [z najlepszymi rozwiązaniami.](https://techcommunity.microsoft.com/t5/datacat/storage-performance-best-practices-and-considerations-for-azure/ba-p/305525) Na przykład w Ogólnego przeznaczenia może być konieczne wstępne przydzielenie rozmiaru plików w celu zwiększenia wydajności. 

- Istnieją kluczowe [różnice w środowisku,](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/) które mogą powodować różnice wydajności między wystąpieniem zarządzanym a SQL Server. Identyfikowanie czynników ryzyka związanych ze środowiskiem, które mogą przyczynić się do problemu z wydajnością. 

- Magazyn zapytań i automatyczne dostrajanie powinny być włączone na komputerze SQL Managed Instance ponieważ ułatwiają one mierzenie wydajności obciążeń i automatyczne zmniejszanie potencjalnych problemów z wydajnością. 



## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat optymalizacji nowego Azure SQL Managed Instance, zobacz następujące zasoby: 

- [Jak określić, dlaczego wydajność obciążeń na Azure SQL Managed Instance różni się od SQL Server?](https://medium.com/azure-sqldb-managed-instance/what-to-do-when-azure-sql-managed-instance-is-slower-than-sql-server-dd39942aaadd)
- [Kluczowe przyczyny różnic w wydajności między SQL Managed Instance a SQL Server](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/)
- [Najlepsze rozwiązania i zagadnienia dotyczące wydajności magazynu Azure SQL Managed Instance (Ogólnego przeznaczenia)](https://techcommunity.microsoft.com/t5/datacat/storage-performance-best-practices-and-considerations-for-azure/ba-p/305525)
- [Monitorowanie wydajności w czasie rzeczywistym dla Azure SQL Managed Instance (jest to zarchiwizowane, czy jest to zamierzony cel?)](/archive/blogs/sqlcat/real-time-performance-monitoring-for-azure-sql-database-managed-instance)