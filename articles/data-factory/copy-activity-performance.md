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
ms.date: 03/11/2020
ms.openlocfilehash: aedb3df69821d1436b03b2eb1f12873b624d426e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81414177"
---
# <a name="copy-activity-performance-and-scalability-guide"></a>Przewodnik dotyczący wydajności i skalowalności działania kopiowania

> [!div class="op_single_selector" title1="Wybierz używaną wersję Azure Data Factory:"]
> * [Wersja 1](v1/data-factory-copy-activity-performance.md)
> * [Bieżąca wersja](copy-activity-performance.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Bez względu na to, czy chcesz przeprowadzić migrację danych na dużą skalę z usługi Data Lake, Enterprise Data Warehouse (rozszerzenia) na platformę Azure, czy chcesz pozyskiwanie danych z różnych źródeł na platformie Azure na potrzeby analizy danych Big Data, ma kluczowe znaczenie dla osiągnięcia optymalnej wydajności i skalowalności.  Azure Data Factory zapewnia wydajny, odporny na i ekonomiczny mechanizm pozyskiwania danych w odpowiedniej skali, dzięki czemu inżynierowie danych chcą tworzyć wysoce wydajne i skalowalne potoki pozyskiwania danych.

Po przeczytaniu tego artykułu będzie można odpowiedzieć na następujące pytania:

- Jaki poziom wydajności i skalowalności można osiągnąć za pomocą działania kopiowania APD na potrzeby migracji danych i scenariuszy pozyskiwania danych?

- Jakie kroki należy wykonać w celu dostosowania wydajności działania kopiowania ADF?
- Jakie pokrętła optymalizacji wydajności ADF można wykorzystać w celu optymalizacji wydajności dla pojedynczego uruchomienia działania kopiowania?
- Jakie inne czynniki mają być brane pod uwagę w przypadku optymalizacji wydajności kopiowania?

> [!NOTE]
> Jeśli nie znasz ogólnie działania kopiowania, przed przeczytaniem tego artykułu zapoznaj się z [omówieniem działania kopiowania](copy-activity-overview.md) .

## <a name="copy-performance-and-scalability-achievable-using-adf"></a>Kopiowanie wydajności i skalowalności w osiągalnej postaci ADF

System ADF oferuje architekturę bezserwerową, która umożliwia równoległość na różnych poziomach, dzięki czemu deweloperzy mogą tworzyć potoki w celu pełnego wykorzystania przepustowości sieci, a także liczby operacji we/wy magazynu i przepustowości w celu zmaksymalizowania przepływności przenoszenia danych w środowisku.  Oznacza to, że przepływność, którą można osiągnąć, można oszacować przez zmierzenie minimalnej przepływności oferowanej przez źródłowy magazyn danych, docelowy magazyn danych i przepustowość sieci między źródłem a miejscem docelowym.  Poniższa tabela oblicza czas trwania kopiowania na podstawie rozmiaru danych i limitu przepustowości dla danego środowiska. 

| Rozmiar danych/ <br/> zużywa | 50 Mb/s    | 100 Mb/s  | 500 Mb/s  | 1 Gb/s   | 5 Gb/s   | 10 Gb/s  | 50 GB/s   |
| --------------------------- | ---------- | --------- | --------- | -------- | -------- | -------- | --------- |
| **1 GB**                    | 2,7 min    | 1,4 min   | 0,3 min   | 0,1 min  | 0,03 min | 0,01 min | 0,0 min   |
| **10 GB**                   | 27,3 min   | 13,7 min  | 2,7 min   | 1,3 min  | 0,3 min  | 0,1 min  | 0,03 min  |
| **100 GB**                  | 4,6 godz.    | 2,3 godz.   | 0,5 godz.   | 0,2 godz.  | 0,05 godz. | 0,02 godz. | 0,0 godz.   |
| **1 TB**                    | 46,6 godz.   | 23,3 godz.  | 4,7 godz.   | 2,3 godz.  | 0,5 godz.  | 0,2 godz.  | 0,05 godz.  |
| **10 TB**                   | 19,4 dni  | 9,7 dni  | 1,9 dni  | 0,9 dni | 0,2 dni | 0,1 dni | 0,02 dni |
| **100 TB**                  | 194,2 dni | 97,1 dni | 19,4 dni | 9,7 dni | 1,9 dni | 1 dzień    | 0,2 dni  |
| **1 PB**                    | 64,7 mo    | 32,4 mo   | 6,5 mo    | 3,2 mo   | 0,6 mo   | 0,3 mo   | 0,06 mo   |
| **10 PB**                   | 647,3 mo   | 323,6 mo  | 64,7 mo   | 31,6 mo  | 6,5 mo   | 3,2 mo   | 0,6 mo    |

Kopiowanie na podajniku APD jest skalowalne na różnych poziomach:

![jak są skalowane kopie APD](media/copy-activity-performance/adf-copy-scalability.png)

- Przepływ sterowania ADF można uruchomić równolegle wiele działań kopiowania, na przykład przy użyciu [dla każdej pętli](control-flow-for-each-activity.md).
- Pojedyncze działanie kopiowania może korzystać z skalowalnych zasobów obliczeniowych: w przypadku korzystania z Azure Integration Runtime można określić [maksymalnie 256 DIUs](#data-integration-units) dla każdego działania kopiowania w sposób bezserwerowy. w przypadku korzystania z Integration Runtime samoobsługowego można ręcznie skalować maszynę w górę lub w poziomie na wielu maszynach ([maksymalnie 4 węzły](create-self-hosted-integration-runtime.md#high-availability-and-scalability)), a działanie pojedynczej kopii spowoduje partycjonowanie zestawu plików we wszystkich węzłach.
- Pojedyncze działanie kopiowania odczytuje i zapisuje dane w magazynie danych przy użyciu [równolegle](#parallel-copy)wielu wątków.

## <a name="performance-tuning-steps"></a>Kroki dostrajania wydajności

Wykonaj następujące kroki, aby dostroić wydajność usługi Azure Data Factory za pomocą działania kopiowania.

1. **Pobierz zestaw danych testowych i Ustanów linię bazową.** Podczas fazy tworzenia Przetestuj potok za pomocą działania kopiowania względem reprezentatywnej próbki danych. Wybrany zestaw danych powinien reprezentować typowe wzorce danych (strukturę folderów, wzorzec pliku, schemat danych itd.) i jest wystarczająco duży, aby można było oszacować wydajność kopiowania, na przykład do ukończenia działania kopiowania trwa 10 minut lub więcej. Zbierz szczegóły wykonania i charakterystyki wydajności następujące po [monitorowaniu działania kopiowania](copy-activity-monitoring.md).

2. **Jak zmaksymalizować wydajność pojedynczego działania kopiowania**:

   Aby rozpocząć pracę z programem, zalecamy pierwsze zmaksymalizowanie wydajności przy użyciu jednego działania kopiowania.

   - **Jeśli działanie kopiowania jest wykonywane na Azure Integration Runtime:** Rozpocznij od domyślnych wartości dla [jednostek integracji danych (DIU)](#data-integration-units) i ustawień [kopiowania równoległego](#parallel-copy) . 

   - **Jeśli działanie kopiowania jest wykonywane na Integration Runtime** samodzielnym: zalecamy użycie dedykowanej maszyny niezależnej od serwera hostujący magazyn danych w celu hostowania środowiska Integration Runtime. Zacznij od domyślnych wartości ustawienia [kopiowania równoległego](#parallel-copy) i przy użyciu jednego węzła dla samodzielnego środowiska IR.  

   Wykonaj przebieg testu wydajnościowego i zanotuj osiągniętą wydajność, a także wartości rzeczywiste używane, takie jak DIUs i kopie równoległe. Zapoznaj się z sekcją [monitorowanie działania kopiowania](copy-activity-monitoring.md) , jak zbierać wyniki przebiegu i ustawienia wydajności oraz jak [rozwiązywać](copy-activity-performance-troubleshooting.md) wąskie gardła, aby zidentyfikować i rozwiązać problemy z wydajnością. 

   Wykonaj iterację w celu przeprowadzenia dodatkowych przebiegów testów wydajności po wskazówkach dotyczących rozwiązywania problemów i dostrajania. Po uruchomieniu pojedynczego działania kopiowania nie można osiągnąć większej przepływności, rozważ zmaksymalizowanie zagregowanej przepływności, uruchamiając wiele kopii współbieżnie odnoszących się do kroku 3.


3. **Jak zmaksymalizować zagregowaną przepływność, uruchamiając wiele kopii jednocześnie:**

   Teraz, gdy masz zmaksymalizowaną wydajność pojedynczego działania kopiowania, jeśli nie osiągnięto jeszcze górnych limitów przepływności dla środowiska — sieci, magazynu danych źródłowych i docelowego magazynu danych — można uruchomić wiele działań kopiowania równolegle przy użyciu konstrukcji przepływu sterowania ADF, takich jak [dla każdej pętli](control-flow-for-each-activity.md). Zapoznaj się z [kopiowaniem plików z wielu kontenerów](solution-template-copy-files-multiple-containers.md), [Migrowanie danych z usługi Amazon S3 do ADLS Gen2](solution-template-migration-s3-azure.md)lub [zbiorcze kopiowanie za pomocą szablonów rozwiązań tabeli formantów](solution-template-bulk-copy-with-control-table.md) .

5. **Rozwiń konfigurację do całego zestawu danych.** Gdy wyniki wykonywania i wydajność są zadowalające, można rozwinąć definicję i potok, aby pokryć cały zestaw danych.

## <a name="troubleshoot-copy-activity-performance"></a>Rozwiązywanie problemów z wydajnością działania kopiowania

Postępuj zgodnie z [krokami dostrajania wydajności](#performance-tuning-steps) , aby planować i przeprowadzać testy wydajnościowe w danym scenariuszu. I Dowiedz się, jak rozwiązywać problemy z wydajnością poszczególnych przebiegów działania kopiowania w Azure Data Factory od [rozwiązywania problemów z wydajnością działania kopiowania](copy-activity-performance-troubleshooting.md).

## <a name="copy-performance-optimization-features"></a>Kopiowanie funkcji optymalizacji wydajności

Azure Data Factory zapewnia następujące funkcje optymalizacji wydajności:

- [Jednostki integracji danych](#data-integration-units)
- [Samoobsługowa skalowalność środowiska Integration Runtime](#self-hosted-integration-runtime-scalability)
- [Kopiowanie równoległe](#parallel-copy)
- [Kopia przygotowana](#staged-copy)

### <a name="data-integration-units"></a>Jednostki integracji danych

Jednostka integracji danych to miara, która reprezentuje moc (kombinację procesora CPU, pamięci i alokacji zasobów sieciowych) pojedynczej jednostki w Azure Data Factory. Jednostka integracji danych ma zastosowanie tylko do [środowiska Azure Integration Runtime](concepts-integration-runtime.md#azure-integration-runtime), ale nie do [własnego środowiska Integration Runtime](concepts-integration-runtime.md#self-hosted-integration-runtime). [Dowiedz się więcej](copy-activity-performance-features.md#data-integration-units).

### <a name="self-hosted-integration-runtime-scalability"></a>Samoobsługowa skalowalność środowiska Integration Runtime

Aby hostować zwiększanie współbieżnych obciążeń lub osiąganie większej wydajności, możesz skalować w górę lub w poziomie Integration Runtime samodzielnie hostowanej. [Dowiedz się więcej](copy-activity-performance-features.md#self-hosted-integration-runtime-scalability).

### <a name="parallel-copy"></a>Kopiowanie równoległe

Można ustawić kopię równoległą, aby wskazać równoległość, która ma być używana przez działanie kopiowania. Tę właściwość można traktować jako maksymalną liczbę wątków w działaniu kopiowania odczytywanych ze źródła lub zapisywać do magazynów danych ujścia równolegle. [Dowiedz się więcej](copy-activity-performance-features.md#parallel-copy).

### <a name="staged-copy"></a>Kopia przygotowana

W przypadku kopiowania danych ze źródłowego magazynu danych do magazynu danych ujścia można użyć usługi BLOB Storage jako tymczasowego magazynu przemieszczania. [Dowiedz się więcej](copy-activity-performance-features.md#staged-copy).

## <a name="next-steps"></a>Następne kroki
Zapoznaj się z innymi artykułami dotyczącymi działania kopiowania:

- [Przegląd działania kopiowania](copy-activity-overview.md)
- [Rozwiązywanie problemów z wydajnością działania kopiowania](copy-activity-performance-troubleshooting.md)
- [Funkcje optymalizacji wydajności działania kopiowania](copy-activity-performance-features.md)
- [Używanie Azure Data Factory do migrowania danych z usługi Data Lake lub magazynu danych na platformę Azure](data-migration-guidance-overview.md)
- [Migrowanie danych z usługi Amazon S3 do usługi Azure Storage](data-migration-guidance-s3-azure-storage.md)
