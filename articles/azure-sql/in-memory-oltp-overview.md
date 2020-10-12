---
title: Technologie w pamięci
description: Technologie w pamięci znacznie zwiększają wydajność obciążeń transakcyjnych i analitycznych w Azure SQL Database i wystąpieniu zarządzanym usługi Azure SQL.
services: sql-database
ms.service: sql-db-mi
ms.subservice: ''
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/19/2019
ms.openlocfilehash: 43527e8e5860e0bbfc50643210156be943d2f174
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85985194"
---
# <a name="optimize-performance-by-using-in-memory-technologies-in-azure-sql-database-and-azure-sql-managed-instance"></a>Optymalizowanie wydajności przy użyciu technologii znajdujących się w pamięci w Azure SQL Database i wystąpieniu zarządzanym usługi Azure SQL
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

Technologie w pamięci umożliwiają zwiększenie wydajności aplikacji i potencjalnie obniżenie kosztu bazy danych.

## <a name="when-to-use-in-memory-technologies"></a>Kiedy używać technologii w pamięci

Korzystając z technologii w pamięci, można osiągnąć ulepszenia wydajności przy użyciu różnych obciążeń:

- **Transakcyjna** (przetwarzanie transakcyjne online (OLTP)), w których większość żądań odczyta lub aktualizuje mniejszy zestaw danych (na przykład CRUD operacji).
- Analiza **analityczna** (Online Analytical Processing (OLAP)), w której większość zapytań ma złożone obliczenia dla celów raportowania, z określoną liczbą zapytań, które ładują i dołączają dane do istniejących tabel (nazywanych zbiorczo) lub usuwają dane z tabel.
- **Mieszany** (transakcja hybrydowa/analityczna przetwarzania (HTAP)), w którym zapytania OLTP i OLAP są wykonywane w tym samym zestawie danych.

Technologie w pamięci mogą zwiększyć wydajność tych obciążeń, zachowując dane, które powinny być przetwarzane w pamięci, za pomocą natywnej kompilacji zapytań lub przetwarzania zaawansowanego, takiego jak przetwarzanie wsadowe i instrukcje SIMD, które są dostępne na podstawowym sprzęcie.

## <a name="overview"></a>Omówienie

Azure SQL Database i wystąpienie zarządzane SQL platformy Azure mają następujące technologie w pamięci:

- Przetwarzanie wsadowe *[w pamięci](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization)* zwiększa liczbę transakcji na sekundę i zmniejsza opóźnienia w przetwarzaniu transakcji. Scenariusze korzystające z In-Memory OLTP to: przetwarzanie transakcji o wysokiej przepływności, takie jak handel i granie, pozyskiwanie danych z zdarzeń lub urządzeń IoT, buforowanie, ładowanie danych i tymczasowe tabele i zmienne tabeli.
- *Klastrowane indeksy magazynu kolumn* zmniejszają rozmiar magazynu (do 10 razy) i zwiększają wydajność raportów i kwerend analitycznych. Można jej używać z tabelami faktów w składnic danych, aby zmieścić więcej danych w bazie danych i zwiększyć wydajność. Ponadto można użyć jej z danymi historycznymi w operacyjnej bazie danych, aby archiwizować i mieć możliwość wykonywania zapytań do 10 razy więcej danych.
- *Nieklastrowane indeksy magazynu kolumn* dla HTAP ułatwiają uzyskiwanie wglądu w dane biznesowe w czasie rzeczywistym za pomocą zapytań bezpośrednio do operacyjnej bazy danych bez konieczności uruchamiania kosztownych procesów wyodrębniania, przekształcania i ładowania (ETL) i poczekaj na wypełnienie hurtowni danych. Nieklastrowane indeksy magazynu kolumn umożliwiają szybkie wykonywanie zapytań analitycznych w bazie danych OLTP, jednocześnie zmniejszając wpływ na obciążenie operacyjne.
- *Klastrowane indeksy magazynu kolumn* dla usługi HTAP umożliwiają szybkie przetwarzanie transakcji i *współbieżne* uruchamianie zapytań analitycznych na tych samych danych.

Zarówno indeksy magazynu kolumn, jak i In-Memory OLTP były częścią SQL Server produktu od 2012 do 2014. Azure SQL Database, wystąpienie zarządzane usługi Azure SQL i SQL Server mają tę samą implementację technologii w pamięci.

## <a name="benefits-of-in-memory-technology"></a>Zalety technologii w pamięci

Ze względu na wydajniejsze przetwarzanie zapytań i transakcji, technologie w pamięci pomagają również obniżyć koszty. Zazwyczaj nie trzeba aktualizować warstwy cenowej bazy danych, aby uzyskać zyski z wydajności. W niektórych przypadkach może nawet być możliwe zmniejszenie warstwy cenowej, ale wciąż można zwiększyć wydajność przy użyciu technologii w pamięci.

Poniżej przedstawiono dwa przykłady sposobu, w jaki In-Memory OLTP pomogła znacząco poprawić wydajność:

- Dzięki użyciu In-Memory OLTP [rozwiązania biznesowe kworum mogły zwiększyć obciążenie przy jednoczesnym zwiększeniu DTU o 70%](https://resources.quorumsoftware.com/case-studies/quorum-doubles-key-database-s-workload-while-lowering-dtu).
- Poniższy film wideo ilustruje znaczne zwiększenie zużycia zasobów przy użyciu przykładowego obciążenia: [wideo OLTP w pamięci](https://channel9.msdn.com/Shows/Data-Exposed/In-Memory-OTLP-in-Azure-SQL-DB). Aby uzyskać więcej informacji, zobacz wpis w blogu: przetwarzanie [OLTP w pamięci](https://azure.microsoft.com/blog/in-memory-oltp-in-azure-sql-database/)

> [!NOTE]  
> Technologie w pamięci są dostępne w warstwach Premium i Krytyczne dla działania firmy.

Poniższy film wideo wyjaśnia potencjalne korzyści z wydajności dzięki technologiom pamięci. Należy pamiętać, że uzyskany wzrost wydajności zawsze zależy od wielu czynników, takich jak charakter obciążenia i danych, wzorzec dostępu do bazy danych itd.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-In-Memory-Technologies/player]
>

W tym artykule opisano zagadnienia dotyczące In-Memory OLTP i indeksów magazynu kolumn, które są specyficzne dla Azure SQL Database i wystąpienia zarządzanego usługi Azure SQL, a także zawierają przykłady:

- Zobaczysz wpływ tych technologii na limity rozmiaru magazynu i danych.
- Zobaczysz, jak zarządzać przenoszeniem baz danych korzystających z tych technologii między różnymi warstwami cenowymi.
- Zobaczysz dwa przykłady ilustrujące zastosowanie In-Memory OLTP, a także indeksów magazynu kolumn.

Aby uzyskać więcej informacji na temat pamięci w SQL Server, zobacz:

- [Omówienie OLTP i scenariusze użycia w pamięci (w](/sql/relational-databases/in-memory-oltp/overview-and-usage-scenarios) tym informacje na temat analiz przypadków klientów i informacji na temat rozpoczynania pracy)
- [Dokumentacja usługi In-Memory OLTP](/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization)
- [Przewodnik po indeksach magazynu kolumn](/sql/relational-databases/indexes/columnstore-indexes-overview)
- Hybrydowe przetwarzanie transakcyjne/analityczne (HTAP), znane także jako [Analiza operacyjna w czasie rzeczywistym](/sql/relational-databases/indexes/get-started-with-columnstore-for-real-time-operational-analytics)

## <a name="in-memory-oltp"></a>Przetwarzanie OLTP w pamięci

In-Memory technologia OLTP zapewnia bardzo szybkie operacje dostępu do danych, zachowując wszystkie dane w pamięci. Używa ona również wyspecjalizowanych indeksów, natywnej kompilacji zapytań i danych z niewolnego zamknięcia, aby zwiększyć wydajność obciążenia OLTP. Istnieją dwa sposoby organizowania danych OLTP In-Memory:

- **Zoptymalizowane pod kątem pamięci format magazynu wierszy** , w którym każdy wiersz jest oddzielnym obiektem pamięci. Jest to klasyczny In-Memory w formacie OLTP zoptymalizowany pod kątem obciążeń OLTP o wysokiej wydajności. Istnieją dwa typy tabel zoptymalizowanych pod kątem pamięci, które mogą być używane w formacie magazynu wierszy zoptymalizowanym pod kątem pamięci:

  - *Tabele trwałe* (SCHEMA_AND_DATA), w których wiersze znajdujące się w pamięci są zachowywane po ponownym uruchomieniu serwera. Ten typ tabel zachowuje się jak tradycyjna tabela magazynu wierszy z dodatkowymi zaletami optymalizacji w pamięci.
  - *Tabele nietrwałe* (SCHEMA_ONLY), w których wiersze nie są zachowywane po ponownym uruchomieniu. Ten typ tabeli jest przeznaczony dla danych tymczasowych (na przykład zastępowanie tabel tymczasowych) lub tabel, w których należy szybko ładować dane przed przeniesieniem ich do pewnej utrwalonej tabeli (nazywanej tabelami przemieszczania).

- **Zoptymalizowane pod kątem pamięci format magazynu kolumn** , w którym dane są zorganizowane w formacie kolumnowym. Ta struktura jest przeznaczona dla scenariuszy HTAP, w których należy uruchamiać zapytania analityczne w tej samej strukturze danych, w której działa obciążenie OLTP.

> [!Note]
> Technologia In-Memory OLTP została zaprojektowana dla struktur danych, które mogą w pełni znajdować się w pamięci. Ponieważ dane znajdujące się w pamięci nie mogą zostać Odciążone na dysku, upewnij się, że korzystasz z bazy danych, która ma wystarczającą ilość pamięci. Aby uzyskać więcej informacji [, zobacz rozmiar danych i limit magazynu dla In-Memory OLTP](#data-size-and-storage-cap-for-in-memory-oltp) .

Krótki przewodnik po In-Memory OLTP: szybki [Start 1: In-Memory technologii OLTP w celu uzyskania szybszej wydajności T-SQL](/sql/relational-databases/in-memory-oltp/survey-of-initial-areas-in-in-memory-oltp) (inny artykuł, który pomoże Ci rozpocząć pracę)

Szczegółowe wideo dotyczące technologii:

- [OLTP w pamięci](https://channel9.msdn.com/Shows/Data-Exposed/In-Memory-OTLP-in-Azure-SQL-DB) (który zawiera demonstrację korzyści z wydajności i kroków, aby odtworzyć te wyniki samodzielnie)
- [Wideo OLTP w pamięci: co to jest i kiedy go używać](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../in-memory-oltp-video-what-it-is-and-whenhow-to-use-it/)

Istnieje programistyczny sposób, aby zrozumieć, czy dana baza danych obsługuje In-Memory OLTP. Można wykonać następujące zapytanie w języku Transact-SQL:

```sql
SELECT DatabasePropertyEx(DB_NAME(), 'IsXTPSupported');
```

Jeśli zapytanie zwróci wartość **1**, In-Memory OLTP jest obsługiwane w tej bazie danych. Następujące zapytania identyfikują wszystkie obiekty, które należy usunąć przed obniżeniem poziomu bazy danych do Ogólnego przeznaczenia, Standard lub Basic:

```sql
SELECT * FROM sys.tables WHERE is_memory_optimized=1
SELECT * FROM sys.table_types WHERE is_memory_optimized=1
SELECT * FROM sys.sql_modules WHERE uses_native_compilation=1
```

### <a name="data-size-and-storage-cap-for-in-memory-oltp"></a>Rozmiar danych i limit pamięci dla In-Memory OLTP

In-Memory OLTP obejmuje tabele zoptymalizowane pod kątem pamięci, które są używane do przechowywania danych użytkownika. Te tabele są wymagane do dopasowania do pamięci. Ze względu na to, że zarządzasz pamięcią bezpośrednio w SQL Database, mamy koncepcji przydziału danych użytkownika. Ten pomysł jest określany mianem *magazynu OLTP w pamięci*.

Każda obsługiwana warstwa cenowa pojedynczej bazy danych i każda warstwa cenowa puli elastycznej zawiera pewną ilość In-Memory magazynu OLTP.

- [Limity zasobów na podstawie jednostek DTU — pojedyncza baza danych](database/resource-limits-dtu-single-databases.md)
- [Limity zasobów oparte na jednostkach DTU — pule elastyczne](database/resource-limits-dtu-elastic-pools.md)
- [Limity zasobów opartych na rdzeń wirtualny — pojedyncze bazy danych](database/resource-limits-vcore-single-databases.md)
- [Limity zasobów opartych na rdzeń wirtualny — pule elastyczne](database/resource-limits-vcore-elastic-pools.md)
- [Limity zasobów na podstawie rdzeń wirtualny — wystąpienie zarządzane](managed-instance/resource-limits.md)

Następujące elementy są wliczane do limitu magazynu In-Memory OLTP:

- Aktywne wiersze danych użytkownika w tabelach zoptymalizowanych pod kątem pamięci i zmiennych tabeli. Należy zauważyć, że stare wersje wierszy nie są wliczane do limitu.
- Indeksy w tabelach zoptymalizowanych pod kątem pamięci.
- Narzuty eksploatacyjne operacji ALTER TABLE.

Po osiągnięciu limitu wystąpi błąd braku przydziału i nie można już wstawiać ani aktualizować danych. Aby wyeliminować ten błąd, Usuń dane lub Zwiększ warstwę cenową bazy danych lub puli.

Aby uzyskać szczegółowe informacje na temat monitorowania In-Memory wykorzystania magazynu OLTP i konfigurowania alertów po niemal osiągnięciu limitu, zobacz [monitorowanie magazynu w pamięci](in-memory-oltp-monitor-space.md).

#### <a name="about-elastic-pools"></a>Pule elastyczne — informacje

W przypadku pul elastycznych In-Memory magazyn OLTP jest współużytkowany przez wszystkie bazy danych w puli. W związku z tym użycie w jednej bazie danych może mieć wpływ na inne bazy danych. Dwa środki zaradcze są następujące:

- Skonfiguruj `Max-eDTU` lub `MaxvCore` dla baz danych, które są mniejsze niż liczba jednostek EDTU lub rdzeń wirtualny dla puli jako całości. Pozwala to na In-Memory maksymalne wykorzystanie magazynu OLTP w każdej bazie danych w puli do rozmiaru odpowiadającego liczbie jednostek eDTU.
- Skonfiguruj `Min-eDTU` lub `MinvCore` , który jest większy niż 0. Ta minimalna gwarancja, że każda baza danych w puli ma ilość dostępnego In-Memory magazyn OLTP, który odnosi się do skonfigurowanego `Min-eDTU` lub `vCore` .

### <a name="changing-service-tiers-of-databases-that-use-in-memory-oltp-technologies"></a>Zmiana warstw usług baz danych korzystających z technologii OLTP In-Memory

Zawsze można uaktualnić bazę danych lub wystąpienie do wyższego poziomu, na przykład z Ogólnego przeznaczenia do Krytyczne dla działania firmy (lub standard do warstwy Premium). Tylko dostępne funkcje i zasoby zwiększają się.

Jednak obniżenie warstwy może mieć negatywny wpływ na bazę danych. Ten wpływ jest szczególnie oczywisty podczas obniżania poziomu Krytyczne dla działania firmy do Ogólnego przeznaczenia (lub Premium do wersji Standard lub Basic), gdy baza danych zawiera In-Memory obiektów OLTP. Tabele zoptymalizowane pod kątem pamięci są niedostępne po obniżeniu poziomu (nawet jeśli są widoczne). Te same kwestie mają zastosowanie w przypadku obniżenia poziomu cenowego puli elastycznej lub przeniesienia bazy danych z użyciem technologii w pamięci do Ogólnego przeznaczenia, standardowej lub podstawowej puli elastycznej.

> [!Important]
> In-Memory OLTP nie jest obsługiwane w warstwie Ogólnego przeznaczenia, standardowa lub podstawowa. W związku z tym nie można przenieść bazy danych, która ma In-Memory obiekty OLTP do jednej z tych warstw.

Przed obniżeniem poziomu bazy danych do Ogólnego przeznaczenia, Standard lub Basic, Usuń wszystkie tabele zoptymalizowane pod kątem pamięci i typy tabel, a także wszystkie natywnie skompilowane moduły T-SQL.

*Skalowanie zasobów w warstwie krytyczne dla działania firmy*: dane w tabelach zoptymalizowanych pod kątem pamięci muszą mieścić się w In-Memory magazynie OLTP, który jest skojarzony z warstwą bazy danych lub wystąpienia zarządzanego, lub jest dostępny w puli elastycznej. Próba skalowania w dół warstwy lub przeniesienie bazy danych do puli, która nie ma wystarczającej ilości dostępnego In-Memory magazynie OLTP, operacja kończy się niepowodzeniem.

## <a name="in-memory-columnstore"></a>Magazyn kolumn w pamięci

Technologia magazynu kolumn w pamięci umożliwia przechowywanie dużych ilości danych w tabelach i wykonywanie na nich zapytań. Technologia magazynu kolumn korzysta z opartych na kolumnach formacie magazynu danych i przetwarzania zapytań wsadowych, aby uzyskać dostęp do 10 razy wydajności zapytań w obciążeniach OLAP w ramach tradycyjnego magazynu zorientowanego na wiersze. Możesz również uzyskać zyski do 10 razy kompresji danych przez rozmiar nieskompresowanych danych.
Istnieją dwa typy modeli magazynu kolumn, za pomocą których można organizować dane:

- **Klastrowana magazyn kolumn** , w której wszystkie dane w tabeli są zorganizowane w formacie kolumnowym. W tym modelu wszystkie wiersze w tabeli są umieszczane w formacie kolumnowy, który kompresuje dane i umożliwia wykonywanie szybkich zapytań analitycznych i raportów w tabeli. W zależności od charakteru danych rozmiar danych może ulec zmniejszeniu 10X-100x. Klastrowany model magazynu kolumn umożliwia również szybkie pozyskiwanie dużych ilości danych (ładowanie masowe), ponieważ duże partie danych przekraczają 100 000 wierszy, zanim są przechowywane na dysku. Ten model jest dobrym rozwiązaniem dla scenariuszy klasycznego magazynu danych.
- **Nieklastrowana magazyn kolumn** , w której dane są przechowywane w tradycyjnych magazynu wierszy tabeli i istnieje indeks w formacie magazynu kolumn, który jest używany dla zapytań analitycznych. Ten model umożliwia przetwarzanie Transactional-Analytic hybrydowych (HTAP): możliwość uruchamiania wykonywania analizy w czasie rzeczywistym w ramach obciążeń transakcyjnych. Zapytania OLTP są wykonywane w tabeli magazynu wierszy, która jest zoptymalizowana pod kątem dostępu do małego zestawu wierszy, podczas gdy zapytania OLAP są wykonywane w indeksie magazynu kolumn, który jest lepszym wyborem w przypadku skanowania i analizy. Optymalizator zapytań dynamicznie wybiera format magazynu wierszy lub magazynu kolumn na podstawie zapytania. Nieklastrowane indeksy magazynu kolumn nie zmniejszają rozmiaru danych, ponieważ oryginalny zestaw danych jest przechowywany w oryginalnej tabeli magazynu wierszy bez żadnej zmiany. Jednak rozmiar dodatkowego indeksu magazynu kolumn powinien mieć wielkość mniejszą niż odpowiedni indeks B-drzewa.

> [!Note]
> Technologia magazynu kolumn w pamięci przechowuje tylko te dane, które są zbędne do przetwarzania w pamięci, podczas gdy dane, które nie mieszczą się w pamięci, są przechowywane na dysku. W związku z tym ilość danych w strukturach magazynu kolumn w pamięci może przekroczyć ilość dostępnej pamięci.

Szczegółowy film dotyczący technologii:

- [Indeks magazynu kolumn: wideo analizy w pamięci z zapłonem 2016](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../columnstore-index-in-memory-analytics-i-e-columnstore-index-videos-from-ignite-2016/)

### <a name="data-size-and-storage-for-columnstore-indexes"></a>Rozmiar danych i magazyn dla indeksów magazynu kolumn

Indeksy magazynu kolumn nie są wymagane do dopasowania do pamięci. W związku z tym jedynym limitem rozmiaru indeksów jest maksymalny całkowity rozmiar bazy danych, który jest udokumentowany w [modelu zakupu opartego na](database/service-tiers-dtu.md) jednostkach DTU oraz w artykułach [modelu zakupu opartego na rdzeń wirtualny](database/service-tiers-vcore.md) .

W przypadku korzystania z klastrowanych indeksów magazynu kolumn, kompresja kolumnowa jest używana dla magazynu tabeli podstawowej. Ta kompresja może znacznie zmniejszyć rozmiar przestrzeni dyskowej danych użytkownika, co oznacza, że można zmieścić więcej danych w bazie danych. Kompresję można również zwiększyć przy użyciu [kompresji archiwalnej](https://msdn.microsoft.com/library/cc280449.aspx#using-columnstore-and-columnstore-archive-compression). Ilość kompresji, którą można osiągnąć, zależy od rodzaju danych, ale 10 razy kompresja nie jest nietypowa.

Na przykład jeśli masz bazę danych o maksymalnym rozmiarze wynoszącym 1 terabajt (TB), a następnie przeniesiesz 10 razy kompresję przy użyciu indeksów magazynu kolumn, możesz dopasować łącznie 10 TB danych użytkownika do bazy danych.

W przypadku korzystania z nieklastrowanych indeksów magazynu kolumn tabela podstawowa jest nadal przechowywana w tradycyjnym formacie magazynu wierszy. W związku z tym oszczędności magazynu nie są tak znaczące jak w przypadku klastrowanych indeksów magazynu kolumn. Jednak w przypadku wymiany wielu tradycyjnych indeksów nieklastrowanych z pojedynczym indeksem magazynu kolumn nadal można uzyskać ogólne oszczędności w odniesieniu do tabeli.

### <a name="changing-service-tiers-of-databases-containing-columnstore-indexes"></a>Zmiana warstw usługi baz danych zawierających indeksy magazynu kolumn

*W przypadku obniżenia poziomu jednej bazy danych do warstwy Podstawowa lub standardowa* może nie być możliwe, jeśli warstwa docelowa jest niższa niż S3. Indeksy magazynu kolumn są obsługiwane tylko w warstwie cenowej Krytyczne dla działania firmy/Premium i w warstwie Standardowa, S3 i wyższych, a nie w warstwie Podstawowa. W przypadku obniżenia poziomu bazy danych do nieobsługiwanej warstwy, indeks magazynu kolumn będzie niedostępny. System przechowuje indeks magazynu kolumn, ale nigdy nie korzysta z indeksu. Jeśli później przejdziesz uaktualnienie do obsługiwanej warstwy lub poziomu, indeks magazynu kolumn będzie natychmiast gotowy do ponownego wykorzystania.

Jeśli masz **klastrowany** indeks magazynu kolumn, cała tabela staną się niedostępna po obniżeniu poziomu. Dlatego zalecamy porzucenie wszystkich *klastrowanych* indeksów magazynu kolumn przed obniżeniem poziomu bazy danych na nieobsługiwaną warstwę lub poziom.

> [!Note]
> Wystąpienie zarządzane SQL obsługuje indeksy magazynu kolumn we wszystkich warstwach.

<a id="install_oltp_manuallink" name="install_oltp_manuallink"></a>

## <a name="next-steps"></a>Następne kroki

- [Szybki Start 1: In-Memory technologii OLTP w celu uzyskania szybszej wydajności T-SQL](https://msdn.microsoft.com/library/mt694156.aspx)
- [Używanie In-Memory OLTP w istniejącej aplikacji Azure SQL](in-memory-oltp-configure.md)
- [Monitoruj In-Memory magazyn OLTP](in-memory-oltp-monitor-space.md) dla In-Memory OLTP
- [Próbowanie funkcji w pamięci](in-memory-sample.md)

## <a name="additional-resources"></a>Zasoby dodatkowe

### <a name="deeper-information"></a>Dokładniejsze informacje

- [Dowiedz się, jak kworum podwaja obciążenie bazy danych przy jednoczesnym obniżeniu liczby jednostek DTU o 70% dzięki In-Memory OLTP w SQL Database](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database)
- [Wpis w blogu OLTP w pamięci](https://azure.microsoft.com/blog/in-memory-oltp-in-azure-sql-database/)
- [Dowiedz się więcej o In-Memory OLTP](https://msdn.microsoft.com/library/dn133186.aspx)
- [Informacje o indeksach magazynu kolumn](https://msdn.microsoft.com/library/gg492088.aspx)
- [Informacje o analizie operacyjnej w czasie rzeczywistym](https://msdn.microsoft.com/library/dn817827.aspx)
- Zapoznaj się [z typowymi wzorcami obciążeń i zagadnieniami](https://msdn.microsoft.com/library/dn673538.aspx) dotyczącymi migracji (które opisują wzorce obciążeń, w których In-Memory OLTP często zapewnia znaczny wzrost wydajności)

### <a name="application-design"></a>Projekt aplikacji

- [Przetwarzanie OLTP w pamięci (Optymalizacja w pamięci)](https://msdn.microsoft.com/library/dn133186.aspx)
- [Używanie In-Memory OLTP w istniejącej aplikacji Azure SQL](in-memory-oltp-configure.md)

### <a name="tools"></a>Narzędzia

- [Azure Portal](https://portal.azure.com/)
- [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx)
- [SQL Server Data Tools (SSDT)](https://msdn.microsoft.com/library/mt204009.aspx)
