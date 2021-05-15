---
title: Przewodnik dotyczący wydajności i skalowalności działania kopiowania
description: Zapoznaj się z najważniejszymi czynnikami wpływającymi na wydajność przenoszenia danych w Azure Data Factory podczas korzystania z działania kopiowania.
services: data-factory
documentationcenter: ''
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/15/2020
ms.openlocfilehash: 75f9080b43333168802a72e60751eec2a765c6d4
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2021
ms.locfileid: "106580823"
---
# <a name="copy-activity-performance-and-scalability-guide"></a>Przewodnik dotyczący wydajności i skalowalności działania kopiowania

> [!div class="op_single_selector" title1="Wybierz używaną wersję Azure Data Factory:"]
> * [Wersja 1](v1/data-factory-copy-activity-performance.md)
> * [Bieżąca wersja](copy-activity-performance.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Czasami chcesz przeprowadzić migrację danych na dużą skalę z usług Data Lake lub Enterprise Data Warehouse (rozszerzenia) na platformę Azure. Inne przypadki, w których chcesz pozyskać duże ilości danych z różnych źródeł na platformę Azure, aby przeprowadzić analizę danych Big Data. W każdym przypadku ma kluczowe znaczenie dla osiągnięcia optymalnej wydajności i skalowalności.

Azure Data Factory (ADF) zapewnia mechanizm pozyskiwania danych. ADF ma następujące zalety:

* Obsługuje duże ilości danych
* Jest wysoce wydajny
* Jest opłacalne

Te zalety sprawiają, że w przypadku inżynierów danych, którzy chcą tworzyć skalowalne potoki pozyskiwania danych, które są wysoce wydajne.

Po przeczytaniu tego artykułu będzie można odpowiedzieć na następujące pytania:

* Jaki poziom wydajności i skalowalności można osiągnąć za pomocą działania kopiowania APD na potrzeby migracji danych i scenariuszy pozyskiwania danych?
* Jakie kroki należy wykonać w celu dostosowania wydajności działania kopiowania ADF?
* Jakie pokrętła optymalizacji wydajności ADF można wykorzystać w celu optymalizacji wydajności dla pojedynczego uruchomienia działania kopiowania?
* Jakie inne czynniki mają być brane pod uwagę w przypadku optymalizacji wydajności kopiowania?

> [!NOTE]
> Jeśli nie znasz ogólnie działania kopiowania, przed przeczytaniem tego artykułu zapoznaj się z [omówieniem działania kopiowania](copy-activity-overview.md) .

## <a name="copy-performance-and-scalability-achievable-using-adf"></a>Kopiowanie wydajności i skalowalności w osiągalnej postaci ADF

System ADF oferuje architekturę bezserwerową, która umożliwia równoległość na różnych poziomach.

Ta architektura umożliwia tworzenie potoków, które maksymalizują przepływność przenoszenia danych w danym środowisku. Te potoki w pełni wykorzystują następujące zasoby:

* Przepustowość sieci między źródłowym i docelowym magazynem danych
* Źródłowa lub docelowa liczba operacji wejścia/wyjścia magazynu danych na sekundę (IOPS) i przepustowość

W tym pełnym wykorzystaniu można oszacować ogólną przepływność, mierząc minimalną przepływność dostępną dla następujących zasobów:

* Źródłowy magazyn danych
* Docelowy magazyn danych
* Przepustowość sieci między źródłowym i docelowym magazynem danych

W poniższej tabeli przedstawiono Obliczanie czasu trwania przenoszenia danych. Czas trwania każdej komórki jest obliczany na podstawie danej sieci i przepustowości magazynu danych oraz danego rozmiaru ładunku danych.

> [!NOTE]
> Czas podany poniżej ma na celu przedstawienie osiągalnej wydajności w ramach kompleksowego rozwiązania do integracji danych zaimplementowanego przy użyciu usługi ADF przy użyciu co najmniej jednej metody optymalizacji wydajności opisanej w [funkcjach optymalizacji wydajności kopiowania](#copy-performance-optimization-features), w tym przy użyciu instrukcji foreach do partycjonowania i duplikowania wielu współbieżnych działań kopiowania. Zalecamy wykonanie kroków opisanych w [krokach dostrajania wydajności](#performance-tuning-steps) w celu zoptymalizowania wydajności kopiowania dla określonego zestawu danych i konfiguracji systemu. Należy używać numerów uzyskanych w testach dostrajania wydajności na potrzeby planowania wdrożenia produkcyjnego, planowania pojemności i projekcji rozliczeń.

&nbsp;

| Rozmiar danych/ <br/> zużywa | 50 Mb/s    | 100 Mb/s  | 500 Mb/s  | 1 Gb/s   | 5 Gb/s   | 10 Gb/s  | 50 Gb/s   |
| --------------------------- | ---------- | --------- | --------- | -------- | -------- | -------- | --------- |
| **1 GB**                    | 2,7 min    | 1,4 min   | 0,3 min   | 0,1 min  | 0,03 min | 0,01 min | 0,0 min   |
| **10 GB**                   | 27,3 min   | 13,7 min  | 2,7 min   | 1,3 min  | 0,3 min  | 0,1 min  | 0,03 min  |
| **100 GB**                  | 4,6 godz.    | 2,3 godz.   | 0,5 godz.   | 0,2 godz.  | 0,05 godz. | 0,02 godz. | 0,0 godz.   |
| **1 TB**                    | 46,6 godz.   | 23,3 godz.  | 4,7 godz.   | 2,3 godz.  | 0,5 godz.  | 0,2 godz.  | 0,05 godz.  |
| **10 TB**                   | 19,4 dni  | 9,7 dni  | 1,9 dni  | 0,9 dni | 0,2 dni | 0,1 dni | 0,02 dni |
| **100 TB**                  | 194,2 dni | 97,1 dni | 19,4 dni | 9,7 dni | 1,9 dni | 1 dzień    | 0,2 dni  |
| **1 PB**                    | 64,7 mies.    | 32,4 mies.   | 6,5 mies.    | 3,2 mies.   | 0,6 mies.   | 0,3 mies.   | 0,06 mies.   |
| **10 PB**                   | 647,3 mies.   | 323,6 mies.  | 64,7 mies.   | 31,6 mies.  | 6,5 mies.   | 3,2 mies.   | 0,6 mies.    |
| | |  | | |  | | |

Kopiowanie na podajniku APD jest skalowalne na różnych poziomach:

![jak są skalowane kopie APD](media/copy-activity-performance/adf-copy-scalability.png)

* Przepływ sterowania ADF można uruchomić równolegle wiele działań kopiowania, na przykład przy użyciu [dla każdej pętli](control-flow-for-each-activity.md).

* Pojedyncze działanie kopiowania może korzystać z skalowalnych zasobów obliczeniowych.
  * W przypadku korzystania z platformy Azure Integration Runtime (IR) można określić [do 256 jednostek integracji danych (DIUs)](#data-integration-units) dla każdego działania kopiowania w sposób bezserwerowy.
  * Korzystając z własnego środowiska IR, można wykonać jedną z następujących czynności:
    * Ręczne skalowanie maszyny.
    * Skalowanie w poziomie do wielu maszyn ([maksymalnie 4 węzły](create-self-hosted-integration-runtime.md#high-availability-and-scalability)), a działanie pojedynczej kopii spowoduje partycjonowanie zestawu plików we wszystkich węzłach.

* Pojedyncze działanie kopiowania odczytuje i zapisuje dane w magazynie danych przy użyciu [równolegle](#parallel-copy)wielu wątków.

## <a name="performance-tuning-steps"></a>Kroki dostrajania wydajności

Wykonaj następujące kroki, aby dostroić wydajność usługi Azure Data Factory za pomocą działania kopiowania:

1. **Pobierz zestaw danych testowych i Ustanów linię bazową.**

    Podczas programowania Przetestuj potok za pomocą działania kopiowania względem reprezentatywnej próbki danych. Wybrany zestaw danych powinien reprezentować typowe wzorce danych przy użyciu następujących atrybutów:

    * Struktura folderów
    * Wzorzec pliku
    * Schemat danych

    Zestaw danych powinien być wystarczająco duży, aby można było oszacować wydajność kopiowania. Dobry rozmiar wymaga co najmniej 10 minut na ukończenie działania kopiowania. Zbierz szczegóły wykonania i charakterystyki wydajności następujące po [monitorowaniu działania kopiowania](copy-activity-monitoring.md).

2. **Jak zmaksymalizować wydajność pojedynczego działania kopiowania**:

    Zalecamy, aby najpierw zmaksymalizować wydajność przy użyciu jednego działania kopiowania.

    * **Jeśli działanie kopiowania jest wykonywane w środowisku _Azure_ Integration Runtime:**

        Rozpocznij od domyślnych wartości ustawień [jednostki integracji danych (DIU)](#data-integration-units) i [kopiowania równoległego](#parallel-copy) .

    * **Jeśli działanie kopiowania jest wykonywane w ramach _własnego_ środowiska Integration Runtime:**

        Zalecamy używanie dedykowanego komputera do hostowania środowiska IR. Maszyna powinna być oddzielona od serwera hostującym magazyn danych. Zacznij od domyślnych wartości ustawienia [kopiowania równoległego](#parallel-copy) i przy użyciu jednego węzła dla samodzielnego środowiska IR.

    Przeprowadź przebieg testu wydajnościowego. Zanotuj osiągniętą wydajność. Uwzględnij rzeczywiste używane wartości, takie jak DIUs i kopie równoległe. Zapoznaj się z [monitorowaniem działania kopiowania](copy-activity-monitoring.md) , jak zbierać wyniki uruchamiania i używane ustawienia wydajności. Informacje o [rozwiązywaniu problemów z wydajnością działania kopiowania](copy-activity-performance-troubleshooting.md) w celu identyfikowania i rozwiązywania wąskiego gardła.

    Wykonaj iterację w celu przeprowadzenia dodatkowych przebiegów testów wydajności po wskazówkach dotyczących rozwiązywania problemów i dostrajania. Po uruchomieniu pojedynczego działania kopiowania nie można osiągnąć większej przepływności, należy rozważyć, czy maksymalizuje zagregowaną przepływność, uruchamiając wiele kopii jednocześnie. Ta opcja została omówiona w następnym numerowanym punktorze.

3. **Jak zmaksymalizować zagregowaną przepływność, uruchamiając wiele kopii jednocześnie:**

    Teraz masz zmaksymalizowaną wydajność jednego działania kopiowania. Jeśli nie osiągnięto jeszcze górnych limitów przepływności w środowisku, można uruchomić równolegle wiele działań kopiowania. Można uruchamiać równolegle przy użyciu konstrukcji przepływu sterowania ADF. Jedna taka konstrukcja jest [dla każdej pętli](control-flow-for-each-activity.md). Aby uzyskać więcej informacji, zobacz następujące artykuły dotyczące szablonów rozwiązań:

    * [Kopiowanie plików z wielu kontenerów](solution-template-copy-files-multiple-containers.md)
    * [Migrowanie danych z usługi Amazon S3 do ADLS Gen2](solution-template-migration-s3-azure.md)
    * [Kopiowanie masowe z tabelą formantów](solution-template-bulk-copy-with-control-table.md)

4. **Rozwiń konfigurację do całego zestawu danych.**

    Gdy wyniki wykonywania i wydajność są zadowalające, można rozwinąć definicję i potok, aby pokryć cały zestaw danych.

## <a name="troubleshoot-copy-activity-performance"></a>Rozwiązywanie problemów z wydajnością działania kopiowania

Postępuj zgodnie z [krokami dostrajania wydajności](#performance-tuning-steps) , aby planować i przeprowadzać testy wydajnościowe w danym scenariuszu. I Dowiedz się, jak rozwiązywać problemy z wydajnością poszczególnych przebiegów działania kopiowania w Azure Data Factory od [rozwiązywania problemów z wydajnością działania kopiowania](copy-activity-performance-troubleshooting.md).

## <a name="copy-performance-optimization-features"></a>Kopiowanie funkcji optymalizacji wydajności

Azure Data Factory zapewnia następujące funkcje optymalizacji wydajności:

* [Jednostki integracji danych](#data-integration-units)
* [Samoobsługowa skalowalność środowiska Integration Runtime](#self-hosted-integration-runtime-scalability)
* [Kopiowanie równoległe](#parallel-copy)
* [Kopia przygotowana](#staged-copy)

### <a name="data-integration-units"></a>Jednostki integracji danych

Jednostka integracji danych (DIU) to miara, która reprezentuje moc pojedynczej jednostki w Azure Data Factory. Potęga to kombinacja procesora CPU, pamięci i zasobów sieciowych. DIU dotyczy tylko [platformy Azure Integration Runtime](concepts-integration-runtime.md#azure-integration-runtime). DIU nie ma zastosowania do [własnego środowiska Integration Runtime](concepts-integration-runtime.md#self-hosted-integration-runtime). [Więcej informacji można znaleźć tutaj](copy-activity-performance-features.md#data-integration-units).

### <a name="self-hosted-integration-runtime-scalability"></a>Samoobsługowa skalowalność środowiska Integration Runtime

Możesz chcieć obsługiwać rosnące współbieżne obciążenie. Możesz też chcieć osiągnąć wyższą wydajność na obecnym poziomie obciążenia. Możesz zwiększyć skalę przetwarzania, wykonując następujące podejścia:

* Możesz skalować w _górę_ własne środowisko IR, zwiększając liczbę [współbieżnych zadań](create-self-hosted-integration-runtime.md#scale-up) , które mogą być uruchamiane w węźle.  
Skalowanie w górę działa tylko wtedy, gdy procesor i pamięć węzła są mniejsze niż w pełni wykorzystywane.
* Środowisko IR można _skalować w poziomie,_ dodając więcej węzłów (maszyn).

Aby uzyskać więcej informacji, zobacz:

* [Funkcje optymalizacji wydajności działania kopiowania: samoobsługowa skalowalność środowiska Integration Runtime](copy-activity-performance-features.md#self-hosted-integration-runtime-scalability)
* [Tworzenie i Konfigurowanie własnego środowiska Integration Runtime: zagadnienia dotyczące skalowania](create-self-hosted-integration-runtime.md#scale-considerations)

### <a name="parallel-copy"></a>Kopiowanie równoległe

Właściwość można ustawić tak `parallelCopies` , aby wskazywała równoległość, która ma być używana przez działanie kopiowania. Tę właściwość należy traktować jako maksymalną liczbę wątków w ramach działania kopiowania. Wątki działają równolegle. Wątki odczytują się ze źródła lub zapisują w magazynach danych ujścia. [Dowiedz się więcej](copy-activity-performance-features.md#parallel-copy).

### <a name="staged-copy"></a>Kopia przygotowana

Operacja kopiowania danych może wysyłać dane _bezpośrednio_ do magazynu danych ujścia. Alternatywnie można wybrać opcję użycia magazynu obiektów BLOB jako tymczasowego magazynu _przemieszczania_ . [Dowiedz się więcej](copy-activity-performance-features.md#staged-copy).

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z innymi artykułami dotyczącymi działania kopiowania:

* [Przegląd działania kopiowania](copy-activity-overview.md)
* [Rozwiązywanie problemów z wydajnością działania kopiowania](copy-activity-performance-troubleshooting.md)
* [Funkcje optymalizacji wydajności działania kopiowania](copy-activity-performance-features.md)
* [Używanie Azure Data Factory do migrowania danych z usługi Data Lake lub magazynu danych na platformę Azure](data-migration-guidance-overview.md)
* [Migrowanie danych z usługi Amazon S3 do usługi Azure Storage](data-migration-guidance-s3-azure-storage.md)
