---
title: Mapowanie wydajności przepływu danych i przewodnik dostrajania
description: Poznaj kluczowe czynniki wpływające na wydajność mapowania przepływów danych w Azure Data Factory.
author: kromerm
ms.topic: conceptual
ms.author: makromer
ms.service: data-factory
ms.custom: seo-lt-2019
ms.date: 05/21/2020
ms.openlocfilehash: 327fffd807d93fda67ff650954ece65e5db58e63
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/22/2020
ms.locfileid: "83798110"
---
# <a name="mapping-data-flows-performance-and-tuning-guide"></a>Przewodnik dotyczący wydajności i dostrajania przepływu danych

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Mapowanie przepływów danych w Azure Data Factory zapewnia interfejs bez kodu do projektowania, wdrażania i organizowania transformacji danych na dużą skalę. Jeśli nie masz doświadczenia w mapowaniu przepływów danych, zobacz [Omówienie przepływu danych mapowania](concepts-data-flow-overview.md).

Podczas projektowania i testowania przepływów danych z poziomu środowiska ADF APD upewnij się, że jest włączona opcja tryb debugowania, aby wykonać przepływy danych w czasie rzeczywistym bez oczekiwania na rozgrzewanie się klastra. Aby uzyskać więcej informacji, zobacz [tryb debugowania](concepts-data-flow-debug-mode.md).

Ten film wideo pokazuje przykładowy chronometraż przekształcania danych za pomocą przepływów danych:
> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4rNxM]

## <a name="monitoring-data-flow-performance"></a>Monitorowanie wydajności przepływu danych

Podczas projektowania mapowania przepływów danych można testować poszczególne przekształcenia, klikając kartę Podgląd danych w panelu konfiguracja. Po zweryfikowaniu logiki Przetestuj przepływ danych jako działanie w potoku. Dodaj działanie wykonaj przepływ danych i użyj przycisku Debuguj, aby przetestować wydajność przepływu danych. Aby otworzyć plan wykonania i profil wydajności przepływu danych, kliknij ikonę okularów w obszarze "akcje" na karcie dane wyjściowe potoku.

![Monitor przepływu danych](media/data-flow/mon002.png "Monitor przepływu danych 2")

 Korzystając z tych informacji, można oszacować wydajność przepływu danych w oparciu o różne źródła danych o różnych rozmiarach. Aby uzyskać więcej informacji, zobacz [monitorowanie przepływów danych mapowania](concepts-data-flow-monitoring.md).

![Monitorowanie przepływu danych](media/data-flow/mon003.png "Monitor przepływu danych 3")

 W przypadku uruchomienia debugowania potoku około jednej minuty czasu konfiguracji klastra jest wymagana w przypadku klastra ciepłego. W przypadku inicjowania domyślnego Azure Integration Runtime czas pracy może potrwać około 5 minut.

## <a name="increasing-compute-size-in-azure-integration-runtime"></a>Zwiększanie rozmiaru obliczeń w Azure Integration Runtime

Integration Runtime o większej liczbie rdzeni zwiększa liczbę węzłów w środowiskach obliczeniowych platformy Spark i zapewnia większą moc obliczeniową do odczytywania, zapisywania i przekształcania danych. Przepływy danych ADF wykorzystują platformę Spark dla aparatu obliczeniowego. Środowisko Spark działa bardzo dobrze w przypadku zasobów zoptymalizowanych pod kątem pamięci.
* Wypróbuj klaster **zoptymalizowany pod kątem obliczeń** , jeśli szybkość przetwarzania ma być wyższa niż stawka wejściowa.
* Wypróbuj klaster **zoptymalizowany pod kątem pamięci** , jeśli chcesz buforować więcej danych w pamięci. Zoptymalizowane pod kątem pamięci ma wyższy poziom cen na rdzeń niż zoptymalizowany od obliczeń, ale prawdopodobnie spowoduje to szybsze przyspieszenie transformacji. Jeśli podczas wykonywania przepływów danych wystąpią błędy związane z pamięcią, przełącz się do konfiguracji Azure IR zoptymalizowanej pod kątem pamięci.

![Nowy IR](media/data-flow/ir-new.png "Nowy IR")

Aby uzyskać więcej informacji na temat tworzenia Integration Runtime, zobacz [Integration Runtime w Azure Data Factory](concepts-integration-runtime.md).

### <a name="increase-the-size-of-your-debug-cluster"></a>Zwiększ rozmiar klastra debugowania

Domyślnie włączenie debugowania będzie używać domyślnego środowiska Azure Integration Runtime, które jest tworzone automatycznie dla każdej fabryki danych. Ten domyślny Azure IR jest ustawiany dla ośmiu rdzeni, czterech dla węzła sterownika i czterech dla węzła procesu roboczego, przy użyciu ogólnych właściwości obliczeń. Podczas testowania większej ilości danych można zwiększyć rozmiar klastra debugowania, tworząc Azure IR z większą liczbą konfiguracji i wybierając tę nową Azure IR po włączeniu debugowania. Spowoduje to, że funkcja ADF użyje tego Azure IR do wyświetlania podglądu danych i debugowania potoku przy użyciu przepływów danych.

### <a name="decrease-cluster-compute-start-up-time-with-ttl"></a>Zmniejsz czas uruchamiania obliczeń klastra przy użyciu czasu wygaśnięcia

W Azure IR w obszarze właściwości przepływu danych istnieje właściwość, która umożliwi rozłożenia puli zasobów obliczeniowych klastra dla fabryki. Za pomocą tej puli można sekwencyjnie przesyłać działania przepływu danych do wykonania. Po ustanowieniu puli każde kolejne zadanie zajmie 1-2 minut, aby klaster Spark na żądanie wykonywał zadanie. Początkowa konfiguracja puli zasobów zajmie około 6 minut. Określ ilość czasu, przez jaki chcesz zachować pulę zasobów w ustawieniu czasu wygaśnięcia (TTL).

## <a name="optimizing-for-azure-sql-database-and-azure-sql-data-warehouse-synapse"></a>Optymalizacja pod kątem Azure SQL Database i Azure SQL Data Warehouse Synapse

### <a name="partitioning-on-source"></a>Partycjonowanie na serwerze źródłowym

1. Przejdź do karty **Optymalizacja** i wybierz pozycję **Ustaw partycjonowanie**
1. Wybierz pozycję **Źródło**.
1. W obszarze **liczba partycji**Ustaw maksymalną liczbę połączeń z bazą danych SQL Azure. Możesz wypróbować wyższe ustawienie, aby uzyskać równoległe połączenia z bazą danych. Jednak niektóre przypadki mogą spowodować szybszą wydajność dzięki ograniczonej liczbie połączeń.
1. Wybierz, czy chcesz podzielić na partycje według określonej kolumny tabeli czy zapytania.
1. W przypadku wybrania **kolumny**wybierz kolumnę partycji.
1. W przypadku wybrania **zapytania**wprowadź zapytanie zgodne ze schematem partycjonowania tabeli bazy danych. To zapytanie pozwala aparatowi źródłowej bazy danych korzystać z eliminacji partycji. Tabele źródłowej bazy danych nie muszą być partycjonowane. Jeśli źródło nie jest już podzielone na partycje, moduł ADF nadal będzie używać partycjonowania danych w środowisku transformacji platformy Spark na podstawie klucza wybieranego w transformacji źródłowej.

![Część źródłowa](media/data-flow/sourcepart3.png "Część źródłowa")

> [!NOTE]
> Dobry przewodnik ułatwiający wybranie liczby partycji dla źródła jest oparty na liczbie rdzeni ustawionych dla Azure Integration Runtime i pomnożenie liczby przez pięć. Tak więc, na przykład, jeśli przekształcasz serię plików w folderach ADLS i zamierzasz korzystać z 32-rdzeniowego Azure IR, liczba partycji, które będą docelowe, to 32 x 5 = 160 partitions.

### <a name="source-batch-size-input-and-isolation-level"></a>Rozmiar partii źródłowej, dane wejściowe i poziom izolacji

W obszarze **Opcje źródła** w transformacji źródłowej następujące ustawienia mogą mieć wpływ na wydajność:

* Rozmiar wsadu powoduje, że moduł ADF zapisuje dane w zestawach w pamięci platformy Spark, a nie wiersz po wierszu. Rozmiar wsadu jest ustawieniem opcjonalnym, a w węzłach obliczeniowych można wypróbować zasoby, jeśli ich rozmiar nie jest prawidłowy. Ustawienie tej właściwości spowoduje użycie domyślnych ustawień wsadowych buforowania platformy Spark.
* Ustawienie zapytania może umożliwić filtrowanie wierszy w źródle przed ich nadejściem do przepływu danych w celu przetworzenia. Może to spowodować szybsze pozyskiwanie danych. Jeśli używasz zapytania, możesz dodać opcjonalne wskazówki dotyczące zapytań dla bazy danych Azure SQL, takie jak Odczyt niezatwierdzony.
* Odczytanie niezatwierdzone zapewni szybsze wyniki zapytania dotyczące transformacji źródłowej

![Element źródłowy](media/data-flow/source4.png "Element źródłowy")

### <a name="sink-batch-size"></a>Rozmiar wsadu ujścia

Aby uniknąć przetwarzania wierszy na wierszach przepływów danych, ustaw **rozmiar wsadu** na karcie Ustawienia dla usługi Azure SQL DB i ujścia usługi Azure SQL DW. W przypadku ustawienia rozmiaru wsadu ADF przetwarza operacje zapisu bazy danych w partiach na podstawie podanego rozmiaru. Ustawienie tej właściwości spowoduje użycie domyślnych ustawień wsadowych buforowania platformy Spark.

![Ujście](media/data-flow/sink4.png "Ujście")

### <a name="partitioning-on-sink"></a>Partycjonowanie na ujścia

Nawet jeśli nie masz partycjonowanych danych w tabelach docelowych, zaleca się, aby dane były partycjonowane w transformację ujścia. Partycjonowane dane często są znacznie szybsze niż w przypadku wymuszania wszystkich połączeń przy użyciu jednego węzła/partycji. Przejdź do karty Optymalizacja ujścia i wybierz *opcję partycjonowanie okrężne* , aby wybrać idealną liczbę partycji do zapisu w ujściach.

### <a name="disable-indexes-on-write"></a>Wyłącz indeksy przy zapisie

W potoku Dodaj [działanie procedury składowanej](transform-data-using-stored-procedure.md) przed działaniem przepływu danych, które powoduje wyłączenie indeksów w tabelach docelowych zapisanych w ujściach. Po działaniu przepływu danych Dodaj kolejne działanie procedury składowanej, które włącza te indeksy. Lub użyj skryptów przed przetwarzaniem i po przetworzeniu w ujścia bazy danych.

### <a name="increase-the-size-of-your-azure-sql-db-and-dw"></a>Zwiększanie rozmiaru bazy danych Azure SQL i serwera DW

Zaplanuj zmianę rozmiarów źródła i ujścia usługi Azure SQL DB oraz DW przed uruchomieniem potoku, aby zwiększyć przepływność i zminimalizować ograniczenie przepustowości platformy Azure po osiągnięciu limitów jednostek DTU. Po zakończeniu wykonywania potoku Zmień rozmiar baz danych z powrotem na ich normalną szybkość uruchamiania.

* Tabela źródłowa usługi SQL DB z 887k wierszami i kolumnami 74 w tabeli bazy danych SQL z pojedynczą transformację kolumn pochodnych zajmują około 3 minuty na zakończenie korzystania z zoptymalizowanej pod kątem pamięci 80-rdzeniowego debugowania platformy Azure.

### <a name="azure-synapse-sql-dw-only-use-staging-to-load-data-in-bulk-via-polybase"></a>[Tylko Azure Synapse SQL DW] Ładowanie danych luzem przy użyciu funkcji przemieszczania za pośrednictwem bazy

Aby uniknąć wstawiania wierszy w wierszu do magazynu danych, zaznacz pole wyboru **Włącz przemieszczanie** w ustawieniach ujścia, aby można było użyć [bazy](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide)danych ADF. Baza danych wielobase pozwala na ładowanie zbiorczych modułów ADF.
* Gdy wykonujesz działanie przepływu danych z potoku, musisz wybrać obiekt BLOB lub ADLS Gen2 lokalizację przechowywania danych podczas ładowania zbiorczego.

* Źródło pliku 421Mb z kolumnami 74 w tabeli Synapse i pojedyncza transformacja kolumn pochodnych zajmie około 4 minut, korzystając z zoptymalizowanej pod kątem pamięci 80-rdzeniowego debugowania platformy Azure.

## <a name="optimizing-for-files"></a>Optymalizacja dla plików

W każdym przekształceniu można ustawić schemat partycjonowania, który ma być używany przez fabrykę danych na karcie Optymalizacja. Dobrym sposobem jest pierwsze przetestowanie sink opartych na plikach, zachowując domyślne partycjonowanie i optymalizacje.

* W przypadku mniejszych plików można znaleźć mniejszą liczbę partycji, które czasami mogą pracować lepiej i szybciej niż w przypadku, gdy platforma Spark ma dzielić małe pliki.
* Jeśli nie masz wystarczającej ilości informacji na temat danych źródłowych, wybierz partycjonowanie działania *okrężne* i ustaw liczbę partycji.
* Jeśli dane zawierają kolumny, które mogą być dobrymi kluczami skrótów, wybierz opcję *partycjonowanie skrótów*.

* Źródło pliku z datasinkem pliku 421Mb z kolumnami 74 i pojedyncza transformacja kolumn pochodnych trwa około 2 minuty, używając zoptymalizowanej pod kątem pamięci 80-rdzeniowego debugowania platformy Azure.

W przypadku debugowania w podglądzie danych i debugowaniu potoku, rozmiary i próbkowanie dla źródłowych elementów DataSet opartych na plikach są stosowane tylko do liczby zwracanych wierszy, a nie liczby odczytanych wierszy. Może to mieć wpływ na wydajność wykonywania debugowania i może spowodować niepowodzenie przepływu.
* Klastry debugowania są domyślnie małymi klastrami z jednym węzłem, a firma Microsoft zaleca korzystanie z przykładowych małych plików na potrzeby debugowania. Przejdź do pozycji Ustawienia debugowania i wskaż mały podzestaw danych przy użyciu pliku tymczasowego.

    ![Ustawienia debugowania](media/data-flow/debugsettings3.png "Ustawienia debugowania")

### <a name="file-naming-options"></a>Opcje nazewnictwa plików

Najbardziej typowym sposobem zapisu przekształconych danych w mapowaniu przepływów danych, pisząc obiekty blob lub ADLS. W ujściach musisz wybrać zestaw danych, który wskazuje na kontener lub folder, a nie plik o nazwie. Ponieważ przepływ danych mapowania używa platformy Spark do wykonania, dane wyjściowe są podzielone na wiele plików na podstawie schematu partycjonowania.

Typowy schemat partycjonowania polega na wybraniu _danych wyjściowych do pojedynczego pliku_, który scala wszystkie pliki składników wyjściowych w jeden plik w ujścia. Ta operacja wymaga, aby dane wyjściowe były ograniczone do pojedynczej partycji w jednym węźle klastra. Możesz wylogować się z zasobów węzłów klastra, jeśli łączysz wiele dużych plików źródłowych w jeden plik wyjściowy.

Aby uniknąć wyczerpania zasobów węzłów obliczeniowych, Zachowaj domyślny, zoptymalizowany schemat w przepływie danych i Dodaj działanie kopiowania w potoku, które scala wszystkie pliki części z folderu wyjściowego z nowym pojedynczym plikiem. Ta technika oddziela akcję transformacji od scalania plików i osiąga ten sam wynik, co ustawienie _danych wyjściowych do pojedynczego pliku_.

### <a name="looping-through-file-lists"></a>Zapętlenie za poorednictwem list plików

Przepływ danych mapowania będzie wykonywany lepiej, gdy transformacja źródłowa wykonuje iterację wielu plików zamiast pętli za pośrednictwem dla każdego działania. Zalecamy używanie symboli wieloznacznych lub list plików w transformacji źródłowej. Proces przepływu danych będzie wykonywany szybciej, umożliwiając zapętlenie w klastrze Spark. Aby uzyskać więcej informacji, zobacz [symbol wieloznaczny w transformacji źródłowej](connector-azure-data-lake-storage.md#mapping-data-flow-properties).

Na przykład jeśli masz listę plików danych z lipca 2019, które chcesz przetworzyć w folderze w Blob Storage, poniżej jest symbolem wieloznacznym, którego można użyć w transformacji źródłowej.

```DateFiles/*_201907*.txt```

Używając symboli wieloznacznych, potok będzie zawierać tylko jedno działanie przepływu danych. Będzie to wykonywane lepiej niż wyszukiwanie w magazynie obiektów blob, który następnie iteruje we wszystkich dopasowanych plikach za pomocą działania ForEach z działaniem przepływu danych wewnątrz.

Potok dla każdego w trybie równoległym spowoduje duplikowanie wielu klastrów przez odwirowanie klastrów zadań dla każdego wykonanego działania przepływu danych. Może to spowodować ograniczenie usługi platformy Azure o dużą liczbę współbieżnych wykonań. Jednak użycie przepływu danych Execute wewnątrz dla każdego z sekwencyjnym zestawem w potoku spowoduje uniknięcie ograniczenia przepustowości i wyczerpania zasobów. Spowoduje to wymuszenie Data Factory wykonywania każdego z plików względem przepływu danych sekwencyjnie.

Zaleca się, aby w przypadku użycia dla każdego z przepływem danych w sekwencji używać ustawienia czasu wygaśnięcia w Azure Integration Runtime. Wynika to z faktu, że każdy plik będzie miał pełny 5-minutowy czas uruchomienia klastra w iteratoru.

### <a name="optimizing-for-cosmosdb"></a>Optymalizacja pod kątem CosmosDB

Ustawianie przepływności i właściwości partii w ujściach CosmosDB zacznie obowiązywać tylko podczas wykonywania tego przepływu danych z działania przepływu danych potoku. Oryginalne ustawienia kolekcji zostaną uznane przez CosmosDB po wykonaniu przepływu danych.

* Rozmiar wsadu: Oblicz przybliżony rozmiar wiersza danych i upewnij się, że rozmiar wsadu rowSize * jest mniejszy niż 2 000 000. Jeśli tak, Zwiększ rozmiar partii, aby uzyskać lepszą przepływność
* Przepływność: w tym miejscu ustaw wyższą wartość ustawienia przepływności, aby zezwolić na szybsze pisanie dokumentów w programie CosmosDB. Pamiętaj o wyższych kosztach RU na podstawie ustawień o wysokiej przepływności.
*   Zapisz budżet przepływności: Użyj wartości, która jest mniejsza niż łączna liczba jednostek ru na minutę. Jeśli istnieje przepływ danych o dużej liczbie partycji platformy Spark, ustawienie przepływności budżetu pozwoli na zwiększenie równowagi między tymi partycjami.

## <a name="join-performance"></a>Przyłączanie wydajności

Zarządzanie wydajnością sprzężeń w przepływie danych jest bardzo powszechną operacją wykonywaną w całym cyklu życia przekształceń danych. W podajniku ADF przepływy danych nie wymagają sortowania danych przed sprzężeniem, ponieważ te operacje są wykonywane jako sprzężenia skrótu w platformie Spark. Można jednak skorzystać z ulepszonej wydajności z optymalizacją sprzężenia "broadcast", która dotyczy transformacji, EXISTS i przeszukiwania.

Spowoduje to uniknięcie losowego odtwarzania przez wypchnięcie zawartości obu stron relacji sprzężenia do węzła Spark. Jest to dobre rozwiązanie w przypadku mniejszych tabel, które są używane do wyszukiwania odwołań. Większe tabele, które mogą nie pasować do pamięci węzła, nie są dobrymi kandydatami do optymalizacji emisji.

Zalecaną konfiguracją przepływów danych z wieloma operacjami łączenia jest zachowanie ustawienia optymalizacji "automatycznej" dla "emisji" i użycie zoptymalizowanej pod kątem pamięci Azure Integration Runtime konfiguracji. Jeśli występują błędy związane z pamięcią lub emisja przekroczenia limitu czasu podczas wykonywania przepływu danych, można wyłączyć optymalizację emisji. Spowoduje to jednak wolniejsze wykonywanie przepływów danych. Opcjonalnie można nakazać przepływ danych do przekazywania tylko lewej lub prawej strony sprzężenia lub obu tych elementów.

![Ustawienia emisji](media/data-flow/newbroad.png "Ustawienia emisji")

Inną optymalizacją sprzężenia jest skompilowanie sprzężeń w taki sposób, aby uniknąć tendencji platformy Spark w celu zaimplementowania sprzężenia krzyżowego. Na przykład po dołączeniu wartości literału w warunkach sprzężenia platforma Spark może zobaczyć, że jako wymaganie najpierw wykonać pełny kartezjańskiego produkt, a następnie odfiltrować połączone wartości. Ale jeśli masz pewność, że masz wartości kolumn po obu stronach warunku sprzężenia, możesz uniknąć tego kartezjańskiegoego produktu platformy Spark i zwiększyć wydajność sprzężeń i przepływów danych.

## <a name="next-steps"></a>Następne kroki

Zobacz inne artykuły dotyczące przepływu danych związane z wydajnością:

- [Karta Optymalizacja przepływu danych](concepts-data-flow-overview.md#optimize)
- [Działanie przepływu danych](control-flow-execute-data-flow-activity.md)
- [Monitorowanie wydajności przepływu danych](concepts-data-flow-monitoring.md)
