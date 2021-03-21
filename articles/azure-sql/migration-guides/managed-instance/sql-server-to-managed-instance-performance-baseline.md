---
title: 'SQL Server do wystąpienia zarządzanego SQL: analiza wydajności'
description: Dowiedz się, jak utworzyć i porównać linię bazową wydajności podczas migrowania baz danych SQL Server do wystąpienia zarządzanego Azure SQL.
ms.service: sql-managed-instance
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: mokabiru
ms.date: 11/06/2020
ms.openlocfilehash: 28eedd3a22a0a5adc6e9a37c9e0f0fa0214a4e47
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103564443"
---
# <a name="migration-performance-sql-server-to-sql-managed-instance-performance-analysis"></a>Wydajność migracji: SQL Server do analizy wydajności wystąpienia zarządzanego SQL
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqlmi.md)]

Utwórz linię bazową wydajności, aby porównać wydajność obciążeń z wystąpieniem zarządzanym SQL z pierwotnym obciążeniem działającym na SQL Server. 

## <a name="create-a-baseline"></a>Utwórz linię bazową

W idealnym przypadku wydajność jest podobna lub lepsza po migracji, dlatego ważne jest, aby mierzyć i rejestrować wartości wydajności linii bazowej na źródłowym, a następnie porównywać je ze środowiskiem docelowym. Linia bazowa wydajności to zestaw parametrów, które definiują średnie obciążenie w źródle. 

Wybierz zestaw zapytań, które są ważne dla i reprezentatywne dla obciążenia biznesowego. Mierz i dokumentuj minimalny/średni/maksymalny czas trwania i użycie procesora CPU dla tych zapytań, a także metryki wydajności na serwerze źródłowym, takie jak średnie/maksymalne użycie procesora CPU, średnie/maksymalne opóźnienie operacji we/wy dysku, przepływność, liczba operacji wejścia/wyjścia na sekundę oraz średnia maksymalna wielkość bazy danych tempdb. 

Następujące zasoby mogą pomóc zdefiniować linię bazową wydajności: 

   - [Monitoruj użycie procesora CPU ](https://techcommunity.microsoft.com/t5/azure-sql-database/monitor-cpu-usage-on-sql-server-and-azure-sql/ba-p/680777#M131)
   - [Monitoruj użycie pamięci](/sql/relational-databases/performance-monitor/monitor-memory-usage)   Określ ilość pamięci używanej przez różne składniki, takie jak pula buforów, pamięć podręczna planu, Pula magazynów kolumn, przetwarzanie [OLTP w pamięci](/sql/relational-databases/in-memory-oltp/monitor-and-troubleshoot-memory-usage)itp. Ponadto należy znaleźć wartości średnie i szczytowe licznika wydajności stron pamięci na stronie. 
   - Monitoruj użycie operacji we/wy dysku na źródłowym wystąpieniu SQL Server przy użyciu widoku [sys.dm_io_virtual_file_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql)   lub [liczników wydajności](/sql/relational-databases/performance-monitor/monitor-disk-usage). 
   - Monitoruj obciążenia i wydajność zapytań, badając dynamiczne widoki zarządzania (lub magazyn zapytań w przypadku migrowania z SQL Server 2016 i nowszych). Identyfikuj średni czas trwania i użycie procesora dla najważniejszych zapytań w obciążeniu. 

Przed migracją należy rozwiązać wszelkie problemy z wydajnością SQL Server źródłowe. Migrowanie znanych problemów do dowolnego nowego systemu może spowodować nieoczekiwane wyniki i unieważnić wszystkie porównania wydajności. 


## <a name="compare-performance"></a>Porównanie wydajności 

Po zdefiniowaniu linii bazowej należy porównać podobną wydajność obciążeń z docelowym wystąpieniem zarządzanym SQL. W celu uzyskania dokładności ważne jest, aby środowisko wystąpienia zarządzanego SQL było porównywalne do środowiska SQL Server, jak to możliwe. 

Istnieją różnice w zakresie infrastruktury wystąpienia zarządzanego SQL, które sprawiają, że dopasowanie wydajności jest dokładnie mało prawdopodobne. Niektóre zapytania mogą działać szybciej niż oczekiwano, a inne mogą być wolniejsze. Celem tego porównania jest upewnienie się, że wydajność obciążeń w wystąpieniu zarządzanym jest zgodna z wydajnością SQL Server (średnio) i identyfikować krytyczne zapytania o wydajności, które nie są zgodne z oryginalną wydajnością. 

Porównanie wydajności prawdopodobnie spowoduje powstanie następujących wyników: 

- Wydajność obciążeń w wystąpieniu zarządzanym jest wyrównana lub lepsza od wydajności obciążeń SQL Server źródłowej. W takim przypadku pomyślnie potwierdzono, że migracja się powiodła. 

- Większość parametrów wydajności i zapytań w obciążeniu działa zgodnie z oczekiwaniami, z pewnymi wyjątkami powodującymi spadek wydajności. W takim przypadku należy zidentyfikować różnice i ich znaczenie. Jeśli istnieją ważne zapytania o obniżonej wydajności, sprawdź, czy źródłowe plany SQL uległy zmianie, czy też czy zapytania mają wpływ na limity zasobów. Można to ograniczyć, stosując pewne wskazówki dotyczące krytycznych zapytań (na przykład zmiany poziomu zgodności, starszej kardynalności szacowania) albo bezpośrednio lub korzystając z przewodników planu. Upewnij się, że statystyki i indeksy są aktualne i są równoważne w obu środowiskach. 

- Większość zapytań jest wolniejsza w wystąpieniu zarządzanym w porównaniu do wystąpienia źródłowego SQL Server. W takim przypadku spróbuj zidentyfikować główne przyczyny różnicy, takie jak [osiągnięcie pewnego limitu zasobów](../../managed-instance/resource-limits.md#service-tier-characteristics) , np. limitów szybkości operacji we/wy, pamięci lub dziennika wystąpienia. Jeśli nie ma żadnych limitów zasobów powodujących różnicę, spróbuj zmienić poziom zgodności bazy danych lub zmienić ustawienia bazy danych, takie jak starsze oszacowanie kardynalności i ponownie uruchom test. Zapoznaj się z zaleceniami podanymi w widokach wystąpienia zarządzanego lub magazynu zapytań, aby zidentyfikować zapytania z wydajnością uległa pogorszeniu. 

Wystąpienie zarządzane SQL ma wbudowaną funkcję automatycznego korygowania planu, która jest domyślnie włączona. Ta funkcja zapewnia, że zapytania, które działały w przeszłości, nie obniżą się w przyszłości. Jeśli ta funkcja nie jest włączona, należy uruchomić obciążenie przy użyciu starych ustawień, aby wystąpienie zarządzane SQL mogły poznać linię bazową wydajności. Następnie Włącz funkcję i ponownie uruchom obciążenie przy użyciu nowych ustawień. 

Wprowadź zmiany w parametrach testu lub Uaktualnij je do wyższych warstw usług, aby osiągnąć optymalną konfigurację wydajności obciążeń, która odpowiada Twoim potrzebom. 

## <a name="monitor-performance"></a>Monitorowanie wydajności 

Wystąpienie zarządzane SQL udostępnia zaawansowane narzędzia do monitorowania i rozwiązywania problemów, a następnie należy ich używać do monitorowania wydajności w wystąpieniu. Oto niektóre kluczowe metryki do monitorowania: 

- Użycie procesora CPU w wystąpieniu, aby określić, czy liczba zainicjowanych rdzeni wirtualnych jest właściwym dopasowaniem dla obciążenia. 
- Stron życia strony na wystąpieniu zarządzanym, aby określić, czy potrzebna jest [dodatkowa pamięć](https://techcommunity.microsoft.com/t5/azure-sql-database/do-you-need-more-memory-on-azure-sql-managed-instance/ba-p/563444).
-  Dane statystyczne, takie jak INSTANCE_LOG_GOVERNOR lub PAGEIOLATCH, które identyfikują problemy we/wy magazynu, szczególnie w warstwie Ogólnego przeznaczenia, które mogą wymagać wstępnego przydzielenia plików w celu uzyskania lepszej wydajności operacji we/wy. 


## <a name="considerations"></a>Zagadnienia do rozważenia  

Podczas porównywania wydajności należy wziąć pod uwagę następujące kwestie: 

- Ustawienia pasują do lokalizacji źródłowej i docelowej. Sprawdź, czy różne ustawienia wystąpień, baz danych i tempdb są równoważne między tymi dwoma środowiskami. Różnice w konfiguracji, poziomach zgodności, ustawieniach szyfrowania, flagi śledzenia itp., mogą spowodować pochylenie wydajności. 

- Magazyn jest konfigurowany zgodnie z [najlepszymi rozwiązaniami](https://techcommunity.microsoft.com/t5/datacat/storage-performance-best-practices-and-considerations-for-azure/ba-p/305525). Na przykład w przypadku Ogólnego przeznaczenia może zajść potrzeba wstępnego przydzielenia rozmiaru plików w celu zwiększenia wydajności. 

- Istnieją [kluczowe różnice w środowisku](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/) , które mogą spowodować różnice w wydajności między wystąpieniem zarządzanym a SQL Server. Identyfikuj ryzyko związane ze środowiskiem, które mogą przyczynić się do problemów z wydajnością. 

- Magazyn zapytań i dostrajanie automatyczne należy włączyć w wystąpieniu zarządzanym SQL, ponieważ ułatwiają one pomiar wydajności obciążeń i automatycznie zmniejszają potencjalne problemy z wydajnością. 



## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat optymalizowania nowego środowiska wystąpienia zarządzanego usługi Azure SQL, zobacz następujące zasoby: 

- [Jak ustalić, dlaczego wydajność obciążeń w wystąpieniu zarządzanym Azure SQL jest inna niż SQL Server?](https://medium.com/azure-sqldb-managed-instance/what-to-do-when-azure-sql-managed-instance-is-slower-than-sql-server-dd39942aaadd)
- [Kluczowe przyczyny różnic wydajności między wystąpieniem zarządzanym SQL i SQL Server](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/)
- [Najlepsze rozwiązania w zakresie wydajności magazynu i zagadnienia dotyczące wystąpienia zarządzanego Azure SQL (Ogólnego przeznaczenia)](https://techcommunity.microsoft.com/t5/datacat/storage-performance-best-practices-and-considerations-for-azure/ba-p/305525)
- [Monitorowanie wydajności w czasie rzeczywistym dla wystąpienia zarządzanego Azure SQL (to jest zarchiwizowane, jest to zamierzony cel?)](/archive/blogs/sqlcat/real-time-performance-monitoring-for-azure-sql-database-managed-instance)