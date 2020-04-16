---
title: Przewodnik dotyczący mapowania przepływu danych i dostrajania
description: Dowiedz się więcej o kluczowych czynnikach, które wpływają na wydajność mapowania przepływów danych w usłudze Azure Data Factory.
author: kromerm
ms.topic: conceptual
ms.author: makromer
ms.service: data-factory
ms.custom: seo-lt-2019
ms.date: 04/14/2020
ms.openlocfilehash: c09a035c8994118b0fb116f357485766e05883ac
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418443"
---
# <a name="mapping-data-flows-performance-and-tuning-guide"></a>Przewodnik dotyczący mapowania przepływów danych i dostrajania

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Mapowanie przepływów danych w usłudze Azure Data Factory zapewnia interfejs bez kodu do projektowania, wdrażania i organizowania przekształceń danych na dużą skalę. Jeśli nie znasz przepływów danych mapowania, zobacz [Omówienie przepływu danych mapowania](concepts-data-flow-overview.md).

Podczas projektowania i testowania przepływów danych z środowiska użytkownika ADF UX należy włączyć tryb debugowania, aby wykonać przepływy danych w czasie rzeczywistym bez oczekiwania na rozgrzanie klastra. Aby uzyskać więcej informacji, zobacz [Tryb debugowania](concepts-data-flow-debug-mode.md).

W tym klipie wideo przedstawiono przykładowe czasy przekształcające dane za pomocą przepływów danych:
> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4rNxM]

## <a name="monitoring-data-flow-performance"></a>Monitorowanie wydajności przepływu danych

Podczas projektowania przepływów danych mapowania można przetestować każdą transformację, klikając kartę podglądu danych w panelu konfiguracji. Po zweryfikowaniu logiki, przetestować przepływ danych end-to-end jako działanie w potoku. Dodaj działanie Wykonaj przepływ danych i użyj przycisku Debugowania, aby przetestować wydajność przepływu danych. Aby otworzyć plan wykonania i profil wydajności przepływu danych, kliknij ikonę okularów w obszarze "działania" w zakładce danych wyjściowych potoku.

![Monitor przepływu danych](media/data-flow/mon002.png "Monitor przepływu danych 2")

 Te informacje można użyć do oszacowania wydajności przepływu danych względem źródeł danych o różnych rozmiarach. Aby uzyskać więcej informacji, zobacz [Monitorowanie przepływów danych mapowania](concepts-data-flow-monitoring.md).

![Monitorowanie przepływu danych](media/data-flow/mon003.png "Monitor przepływu danych 3")

 W przypadku przebiegów debugowania potoku około jednej minuty czasu konfiguracji klastra w obliczeniach ogólnej wydajności jest wymagane dla klastra ogrzanego. Jeśli inicjujesz domyślny środowiska uruchomieniowego integracji platformy Azure, czas rozkręcania może potrwać około 5 minut.

## <a name="increasing-compute-size-in-azure-integration-runtime"></a>Zwiększanie rozmiaru obliczeń w czasie wykonywania integracji platformy Azure

Środowisko wykonawcze integracji z większą liczbą rdzeni zwiększa liczbę węzłów w środowiskach obliczeniowych Platformy Spark i zapewnia większą moc obliczeniową do odczytu, zapisu i przekształcania danych. Przepływy danych usługi ADF wykorzystują platformę Spark dla aparatu obliczeniowego. Środowisko Platformy Spark działa bardzo dobrze na zasoby zoptymalizowane pod kątem pamięci.
* Wypróbuj klaster **zoptymalizowany pod kątem obliczeń,** jeśli chcesz, aby szybkość przetwarzania była wyższa niż szybkość wprowadzania.
* Wypróbuj klaster **zoptymalizowany pod kątem pamięci,** jeśli chcesz buforować więcej danych w pamięci. Zoptymalizowana pamięć ma wyższy punkt cenowy za rdzeń niż zoptymalizowana pod kątem obliczeń, ale prawdopodobnie spowoduje szybsze przekształcenie.

![Nowe podczerwem](media/data-flow/ir-new.png "Nowe podczerwem")

Aby uzyskać więcej informacji na temat tworzenia środowiska wykonawczego integracji, zobacz [Środowisko wykonawcze integracji w usłudze Azure Data Factory](concepts-integration-runtime.md).

### <a name="increase-the-size-of-your-debug-cluster"></a>Zwiększanie rozmiaru klastra debugowania

Domyślnie włączenie debugowania spowoduje użycie domyślnego środowiska uruchomieniowego integracji platformy Azure, który jest tworzony automatycznie dla każdej fabryki danych. Ta domyślna usługa Azure IR jest ustawiona dla ośmiu rdzeni, czterech dla węzła sterownika i czterech dla węzła procesu roboczego przy użyciu właściwości ogólnych obliczeń. Podczas testowania z większymi danymi można zwiększyć rozmiar klastra debugowania, tworząc usługę Azure IR z większymi konfiguracjami i wybrać tę nową usługę Azure IR po włączeniu debugowania. Spowoduje to poinstruowanie usługi ADF do używania tej usługi Azure IR do podglądu danych i debugowania potoku z przepływami danych.

### <a name="decrease-cluster-compute-start-up-time-with-ttl"></a>Skrócenie czasu uruchamiania obliczeń klastra dzięki czasowi wygaśnięcia

Istnieje właściwość w usłudze Azure IR w obszarze Właściwości przepływu danych, która pozwoli na stand-up puli zasobów obliczeniowych klastra dla fabryki. Z tej puli, można sekwencyjnie przesłać działania przepływu danych do wykonania. Po ustanowieniu puli każde kolejne zadanie zajmie 1-2 minuty, aby klaster platformy Spark na żądanie wykonał zadanie. Początkowa konfiguracja puli zasobów zajmie około 6 minut. Określ czas, przez jaki chcesz zachować pulę zasobów w ustawieniu czas wygaśnięcia (TTL).

## <a name="optimizing-for-azure-sql-database-and-azure-sql-data-warehouse-synapse"></a>Optymalizacja pod kątem bazy danych SQL Azure i synapse usługi Azure SQL Data Warehouse

### <a name="partitioning-on-source"></a>Partycjonowanie w źródle

1. Przejdź do karty **Optymalizuj** i wybierz pozycję **Ustaw partycjonowanie**
1. Wybierz **źródło**.
1. W **obszarze Liczba partycji**ustaw maksymalną liczbę połączeń z bazą danych SQL platformy Azure. Można spróbować wyższe ustawienie, aby uzyskać połączenia równoległe do bazy danych. Jednak niektóre przypadki mogą spowodować szybszą wydajność przy ograniczonej liczbie połączeń.
1. Wybierz, czy mają być partycjonowanie według określonej kolumny tabeli, czy kwerendy.
1. Jeśli wybrano **opcję Kolumna,** wybierz kolumnę partycji.
1. Jeśli wybrano **opcję Kwerenda**, wprowadź kwerendę odpowiadającą schematowi partycjonowania tabeli bazy danych. Ta kwerenda umożliwia aparat źródłowej bazy danych do wykorzystania eliminacji partycji. Tabele źródłowej bazy danych nie muszą być podzielone na partycje. Jeśli źródło nie jest jeszcze podzielony na partycje, ADF będzie nadal używać partycjonowania danych w środowisku transformacji platformy Spark na podstawie klucza wybranego w transformacji źródła.

![Część źródłna](media/data-flow/sourcepart3.png "Część źródłna")

> [!NOTE]
> Dobry przewodnik, który pomoże Ci wybrać liczbę partycji dla źródła, jest oparty na liczbie rdzeni ustawionych dla środowiska wykonawczego integracji platformy Azure i pomnożyć tę liczbę przez pięć. Tak, na przykład, jeśli przekształcasz serię plików w folderach ADLS i zamierzasz korzystać z 32-rdzeniowej usługi Azure IR, liczba partycji, które mają być kierowane jest 32 x 5 = 160 partycji.

### <a name="source-batch-size-input-and-isolation-level"></a>Rozmiar partii źródłowej, dane wejściowe i poziom izolacji

W obszarze **Opcje źródła** w transformacji źródła następujące ustawienia mogą mieć wpływ na wydajność:

* Rozmiar partii nakazuje podajnikowi ADF przechowywanie danych w zestawach w pamięci platformy Spark zamiast wiersza po wierszu. Rozmiar partii jest ustawieniem opcjonalnym i może zabraknąć zasobów w węzłach obliczeniowych, jeśli nie są one odpowiednio dobrane. Nie ustawienie tej właściwości będzie korzystać z domyślnych partii buforowania platformy Spark.
* Ustawienie kwerendy może umożliwić filtrowanie wierszy u źródła, zanim dotrą one do przepływu danych w celu przetworzenia. Może to przyspieszyć początkowe pozyskiwanie danych. Jeśli używasz kwerendy, można dodać opcjonalne wskazówki kwerendy dla usługi Azure SQL DB, takie jak ODCZYT BEZ ZGODY.
* Odczyt niezatwierdzonych zapewni szybsze wyniki kwerendy w transformacji źródła

![Element źródłowy](media/data-flow/source4.png "Element źródłowy")

### <a name="sink-batch-size"></a>Rozmiar partii zlewu

Aby uniknąć przetwarzania przepływów danych wiersz po wierszu, ustaw **rozmiar usługi Batch** na karcie Ustawienia dla witryn Usługi Azure SQL DB i ujmek sql dw platformy Azure. Jeśli rozmiar partii jest ustawiona, ADF przetwarza zapis bazy danych w partiach na podstawie podanego rozmiaru. Nie ustawienie tej właściwości będzie korzystać z domyślnych partii buforowania platformy Spark.

![Ujście](media/data-flow/sink4.png "Ujście")

### <a name="partitioning-on-sink"></a>Partycjonowanie na zlewie

Nawet jeśli nie masz danych podzielonych na partycje w tabelach docelowych, zaleca się, aby dane na partycje w transformacji ujścia. Dane podzielone na partycje często powoduje znacznie szybsze ładowanie przez zmuszając wszystkie połączenia do korzystania z jednego węzła/partycji. Przejdź do zakładki Optymalizuj zlew i wybierz *partycjonowanie okrężne,* aby wybrać idealną liczbę partycji do zapisania w zlewie.

### <a name="disable-indexes-on-write"></a>Wyłączanie indeksów podczas zapisu

W potoku dodaj [działanie procedury składowanej](transform-data-using-stored-procedure.md) przed działaniem przepływu danych, które wyłącza indeksy w tabelach docelowych zapisanych z zlewu. Po zakończeniu działania przepływu danych dodaj inne działanie procedury składowanej, które włącza te indeksy. Lub wykorzystać skrypty przetwarzania wstępnego i przetwarzania końcowego w ujściu bazy danych.

### <a name="increase-the-size-of-your-azure-sql-db-and-dw"></a>Zwiększ rozmiar bazy danych i dw usługi Azure SQL

Zaplanuj zmiany rozmiaru źródła i ujścia usługi Azure SQL DB i DW przed uruchomieniem potoku, aby zwiększyć przepływność i zminimalizować ograniczanie przepustowości platformy Azure po osiągnięciu limitów jednostek DTU. Po zakończeniu wykonywania potoku, zmienić rozmiar baz danych z powrotem do ich normalnej szybkości uruchamiania.

* Tabela źródłowych bazy danych SQL z wierszami 887k i 74 kolumnami do tabeli bazy danych SQL z pojedynczą transformacją kolumn pochodnych trwa około 3 minut od końca do końca przy użyciu zoptymalizowanych pod kątem pamięci 80-rdzeniowych podstaw debugowania usługi Azure.

### <a name="azure-synapse-sql-dw-only-use-staging-to-load-data-in-bulk-via-polybase"></a>[Tylko usługa Azure Synapse SQL DW] Użyj przemieszczania, aby ładować dane zbiorczo za pośrednictwem Polybase

Aby uniknąć wstawień wiersza po wierszu do dw, zaznacz opcję **Włącz przemieszczanie** w ustawieniach ujścia, aby ADF mógł używać [polybase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide). PolyBase umożliwia ADF do ładowania danych zbiorczo.
* Podczas wykonywania działania przepływu danych z potoku, należy wybrać lokalizację magazynu obiektu Blob lub ADLS Gen2 do etapu danych podczas ładowania zbiorczego.

* Źródło pliku 421Mb z 74 kolumnami do tabeli Synapse i pojedynczą transformacją kolumny pochodnej trwa około 4 minut od końca do końca przy użyciu zoptymalizowanej pamięci 80-rdzeniowej debugowania za pomocą identyfikatorów Azure.

## <a name="optimizing-for-files"></a>Optymalizacja plików

Przy każdej transformacji można ustawić schemat partycjonowania, który ma być używany fabrycznie danych na karcie Optymalizuj. Jest dobrą praktyką, aby najpierw przetestować pochłaniacze oparte na plikach, zachowując domyślne partycjonowanie i optymalizacje.

* W przypadku mniejszych plików może się okazać, że wybranie mniejszej liczby partycji może czasami działać lepiej i szybciej niż proszenie platformy Spark o partycjonowanie małych plików.
* Jeśli nie masz wystarczającej ilości informacji o danych źródłowych, wybierz opcję Partycjonowanie *okrężne* i ustaw liczbę partycji.
* Jeśli dane mają kolumny, które mogą być dobrymi klawiszami skrótu, wybierz opcję *Partycjonowanie skrótów*.

* Źródło pliku z pochłanianiem plików pliku 421Mb z 74 kolumnami i pojedynczą transformacją kolumny pochodnej trwa około 2 minut od końca do końca przy użyciu zoptymalizowanej pamięci 80-rdzeniowej debugowania za pomocą identyfikatorów Azure.

Podczas debugowania w podglądzie danych i debugowaniu potoku rozmiary limitów i próbkowania dla źródłowych zestawów danych opartych na plikach dotyczą tylko liczby zwróconych wierszy, a nie liczby odczytanych wierszy. Może to mieć wpływ na wydajność wykonania debugowania i ewentualnie spowodować niepowodzenie przepływu.
* Klastry debugowania są domyślnie małymi klastrami z pojedynczymi węzłami i zaleca się używanie przykładowych małych plików do debugowania. Przejdź do ustawień debugowania i wskaż mały podzbiór danych przy użyciu pliku tymczasowego.

    ![Ustawienia debugowania](media/data-flow/debugsettings3.png "Ustawienia debugowania")

### <a name="file-naming-options"></a>Opcje nazewnictwa plików

Najczęstszym sposobem zapisywania przekształconych danych w przepływach danych mapowania magazynu plików obiektów Blob lub ADLS. W zlewie należy wybrać zestaw danych, który wskazuje kontener lub folder, a nie nazwany plik. Ponieważ przepływ danych mapowania używa platformy Spark do wykonania, dane wyjściowe są dzielone na wiele plików na podstawie schematu partycjonowania.

Typowy schemat partycjonowania jest, aby wybrać _wyjście do jednego pliku,_ który scala wszystkie wyjściowe pliki PART w jednym pliku w zlewie. Ta operacja wymaga, aby dane wyjściowe zmniejsza się do jednej partycji w jednym węźle klastra. Jeśli łączysz wiele dużych plików źródłowych w jeden plik wyjściowy, możesz zabraknąć zasobów węzła klastra.

Aby uniknąć wyczerpania zasobów węzła obliczeniowego, zachowaj domyślny, zoptymalizowany schemat w przepływie danych i dodaj działanie kopiowania w potoku, które scala wszystkie pliki PART z folderu wyjściowego do nowego pojedynczego pliku. Ta technika oddziela akcję transformacji od scalania plików i osiąga taki sam wynik jak ustawienie _Output na pojedynczy plik_.

### <a name="looping-through-file-lists"></a>Zapętlanie list plików

Przepływ danych mapowania będzie lepiej wykonywać, gdy transformacja źródła iteruje za pośrednictwem wielu plików zamiast pętli za pośrednictwem for each działania. Zalecamy używanie symboli wieloznacznych lub list plików w transformacji źródła. Proces przepływu danych będzie wykonywane szybciej, umożliwiając pętli występuje wewnątrz klastra Platformy Spark. Aby uzyskać więcej informacji, zobacz [Wildcarding w transformacji źródła](connector-azure-data-lake-storage.md#mapping-data-flow-properties).

Na przykład jeśli masz listę plików danych z lipca 2019, które chcesz przetworzyć w folderze w magazynie obiektów Blob, poniżej znajduje się symbol wieloznaczny, którego możesz użyć w transformacji źródła.

```DateFiles/*_201907*.txt```

Za pomocą symboli wieloznacznych, potok będzie zawierać tylko jedno działanie przepływu danych. Będzie to działać lepiej niż wyszukiwanie względem magazynu obiektów Blob, który następnie iteruje we wszystkich dopasowanych plików przy użyciu ForEach z execute data flow działania wewnątrz.

### <a name="optimizing-for-cosmosdb"></a>Optymalizacja dla usługi CosmosDB

Ustawianie właściwości przepływności i wsadu na ujście usługi CosmosDB tylko wziąć pod życie podczas wykonywania tego przepływu danych z działania przepływu danych potoku. Oryginalne ustawienia kolekcji będą honorowane przez cosmosdb po wykonaniu przepływu danych.

* Rozmiar partii: Oblicz przybliżony rozmiar wiersza danych i upewnij się, że rozmiar partii rowSize * jest mniejszy niż dwa miliony. Jeśli tak, zwiększ rozmiar partii, aby uzyskać lepszą przepustowość
* Przepływność: Ustaw wyższe ustawienie przepływności w tym miejscu, aby umożliwić dokumenty do szybszego zapisu w usłudze CosmosDB. Należy pamiętać o wyższych kosztach RU w oparciu o wysokie ustawienie przepustowości.
*   Budżet przepływności zapisu: Użyj wartości, która jest mniejsza niż całkowita liczba procesorów rus na minutę. Jeśli masz przepływ danych z dużą liczbą partycji Spark, ustawienie przepływności budżetu pozwoli na większą równowagę między tymi partycjami.

## <a name="join-performance"></a>Dołącz do wydajności

Zarządzanie wydajnością sprzężeń w przepływie danych jest bardzo powszechną operacją, którą można wykonać w całym cyklu życia przekształceń danych. W ujrzeniu ADF przepływy danych nie wymagają sortowania danych przed sprzężeniami, ponieważ te operacje są wykonywane jako sprzężenia mieszania w programie Spark. Jednak można korzystać z lepszej wydajności z "Broadcast" Join optymalizacji. Pozwoli to uniknąć przetasowań, naciskając w dół zawartość obu stron relacji sprzężenia do węzła Spark. Działa to dobrze w przypadku mniejszych tabel, które są używane do wyszukiwania odwołań. Większe tabele, które mogą nie pasować do pamięci węzła, nie są dobrymi kandydatami do optymalizacji emisji.

Innym Join optymalizacji jest tworzenie sprzężeń w taki sposób, że pozwala uniknąć skłonności platformy Spark do implementowania sprzężeń krzyżowych. Na przykład po dołączeniu wartości literału w warunkach sprzężenia, Spark może zobaczyć, że jako wymóg, aby najpierw wykonać pełny produkt kartezjański, a następnie odfiltrować połączone wartości. Ale jeśli upewnij się, że masz wartości kolumn po obu stronach warunku sprzężenia, można uniknąć tego produktu kartesian wywołane przez platformę spark i poprawić wydajność sprzężenia i przepływów danych.

## <a name="next-steps"></a>Następne kroki

Zobacz inne artykuły o przepływie danych związane z wydajnością:

- [Karta Optymalizacja przepływu danych](concepts-data-flow-overview.md#optimize)
- [Działanie przepływu danych](control-flow-execute-data-flow-activity.md)
- [Monitorowanie wydajności przepływu danych](concepts-data-flow-monitoring.md)
