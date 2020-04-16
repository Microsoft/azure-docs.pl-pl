---
title: Przewodnik po wydajności i skalowalności działania kopiowania
description: Dowiedz się więcej o kluczowych czynnikach, które wpływają na wydajność przenoszenia danych w usłudze Azure Data Factory podczas korzystania z działania kopiowania.
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
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414177"
---
# <a name="copy-activity-performance-and-scalability-guide"></a>Przewodnik po wydajności i skalowalności działania kopiowania

> [!div class="op_single_selector" title1="Wybierz wersję usługi Azure Data Factory, której używasz:"]
> * [Wersja 1](v1/data-factory-copy-activity-performance.md)
> * [Bieżąca wersja](copy-activity-performance.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Niezależnie od tego, czy chcesz przeprowadzić migrację danych na dużą skalę z usługi Data Lake lub enterprise data warehouse (EDW) na platformę Azure, czy chcesz pozyskiwać dane na dużą skalę z różnych źródeł na platformie Azure w celu analizy dużych zbiorów danych, ważne jest osiągnięcie optymalnej wydajności i skalowalności.  Usługa Azure Data Factory zapewnia wydajny, odporny i ekonomiczny mechanizm pozyskiwania danych na dużą skalę, dzięki czemu doskonale nadaje się dla inżynierów danych, którzy chcą tworzyć wysoce wydajne i skalowalne potoki pozyskiwania danych.

Po przeczytaniu tego artykułu, będziesz w stanie odpowiedzieć na następujące pytania:

- Jaki poziom wydajności i skalowalności można osiągnąć przy użyciu aktywność kopiowania ADF dla scenariuszy migracji danych i pozyskiwania danych?

- Jakie kroki należy podjąć, aby dostroić wydajność działania kopiowania podajnika ADF?
- Jakie pokrętła optymalizacji adf perf można wykorzystać do optymalizacji wydajności dla pojedynczego uruchomienia działania kopiowania?
- Jakie inne czynniki poza podajnikem ADF należy wziąć pod uwagę przy optymalizowaniu wydajności kopiowania?

> [!NOTE]
> Jeśli nie znasz działania kopiowania w ogóle, zobacz [omówienie działania kopiowania](copy-activity-overview.md) przed przeczytaniem tego artykułu.

## <a name="copy-performance-and-scalability-achievable-using-adf"></a>Wydajność kopiowania i skalowalności osiągalne przy użyciu podajnika ADF

ADF oferuje architekturę bezserwerową, która umożliwia równoległość na różnych poziomach, co umożliwia deweloperom tworzenie potoków w celu pełnego wykorzystania przepustowości sieci, a także operacje we/wy magazynu i przepustowość w celu zmaksymalizowania przepływności przepływu danych w danym środowisku.  Oznacza to, że przepływność, którą można osiągnąć, można oszacować, mierząc minimalną przepustowość oferowaną przez magazyn danych źródłowych, docelowy magazyn danych i przepustowość sieci między źródłem a miejscem docelowym.  Poniższa tabela oblicza czas trwania kopiowania na podstawie rozmiaru danych i limitu przepustowości dla środowiska. 

| Rozmiar danych / <br/> Przepustowości | 50 Mb/s    | 100 Mb/s  | 500 Mb/s  | 1 Gb/s   | 5 Gb/s   | 10 Gb/s  | 50 Gb/s   |
| --------------------------- | ---------- | --------- | --------- | -------- | -------- | -------- | --------- |
| **1 GB**                    | 2,7 min.    | 1,4 min.   | 0,3 min.   | 0,1 min.  | 0,03 min. | 0,01 min. | 0,0 min.   |
| **10 GB**                   | 27,3 min.   | 13,7 min.  | 2,7 min.   | 1,3 min.  | 0,3 min.  | 0,1 min.  | 0,03 min.  |
| **100 GB**                  | 4,6 godz.    | 2,3 godz.   | 0,5 godz.   | 0,2 godz.  | 0,05 godz. | 0,02 godz. | 0,0 godz.   |
| **1 TB**                    | 46,6 godz.   | 23,3 godz.  | 4,7 godz.   | 2,3 godz.  | 0,5 godz.  | 0,2 godz.  | 0,05 godz.  |
| **10 TB**                   | 19,4 dnia  | 9,7 dnia  | 1,9 dnia  | 0,9 dnia | 0,2 dnia | 0,1 dnia | 0,02 dnia |
| **100 TB**                  | 194,2 dni | 97,1 dnia | 19,4 dnia | 9,7 dnia | 1,9 dnia | 1 dzień    | 0,2 dnia  |
| **1 pb**                    | 64,7 mln zł.    | 32,4 mln zł.   | 6,5 mln zł.    | 3,2 mln zł.   | 0,6 tys.   | 0,3 tys.   | 0,06 tys.   |
| **10 pb**                   | 647,3 mln zł.   | 323,6 mln zł.  | 64,7 mln zł.   | 31,6 mln zł.  | 6,5 mln zł.   | 3,2 mln zł.   | 0,6 tys.    |

Kopia ADF jest skalowalna na różnych poziomach:

![jak skaluje się kopiowanie podnajem ADF](media/copy-activity-performance/adf-copy-scalability.png)

- Przepływ sterowania ADF można uruchomić wiele działań kopiowania równolegle, na przykład za pomocą [dla każdej pętli](control-flow-for-each-activity.md).
- Działanie pojedynczej kopii można skorzystać ze skalowalnych zasobów obliczeniowych: podczas korzystania z środowiska uruchomieniowego integracji platformy Azure, można określić [maksymalnie 256 jednostek DIU](#data-integration-units) dla każdego działania kopiowania w sposób bezserwerowy; podczas korzystania z samodzielnego środowiska wykonawczego integracji można ręcznie skalować komputer lub skalować w poziomie do wielu[komputerów (do 4 węzłów),](create-self-hosted-integration-runtime.md#high-availability-and-scalability)a pojedyncze działanie kopiowania będzie partycjonować jego zestaw plików we wszystkich węzłach.
- Działanie pojedynczej kopii odczytuje i zapisuje w magazynie danych przy użyciu wielu wątków [równolegle](#parallel-copy).

## <a name="performance-tuning-steps"></a>Kroki dostrajania wydajności

Wykonaj następujące kroki, aby dostosować wydajność usługi Azure Data Factory z działaniem kopiowania.

1. **Podnieś testowy zestaw danych i ustal linię bazową.** Podczas fazy rozwoju przetestuj potoku przy użyciu działania kopiowania względem próbki danych reprezentatywnych. Wybranego zestawu danych powinny reprezentować typowe wzorce danych (struktura folderów, wzorzec pliku, schemat danych itd.) i jest wystarczająco duży, aby ocenić wydajność kopiowania, na przykład trwa 10 minut lub dłużej, aby wykonać działanie kopiowania. Zbieranie szczegółów wykonania i charakterystyki wydajności po [monitorowaniu działania kopiowania](copy-activity-monitoring.md).

2. **Jak zmaksymalizować wydajność pojedynczego działania kopiowania:**

   Na początek zalecamy, aby najpierw zmaksymalizować wydajność przy użyciu działania pojedynczej kopii.

   - **Jeśli działanie kopiowania jest wykonywane w środowisku runtime integracji platformy Azure:** należy rozpocząć od wartości domyślnych dla [jednostek integracji danych (DIU)](#data-integration-units) i ustawień [kopiowania równoległego.](#parallel-copy) 

   - **Jeśli działanie kopiowania jest wykonywane w własnym czasie wykonywania integracji:** zaleca się użycie dedykowanego komputera oddzielnie od serwera obsługującego magazyn danych do hostowania środowiska wykonawczego integracji. Zacznij od wartości domyślnych dla ustawienia [kopiowania równoległego](#parallel-copy) i przy użyciu pojedynczego węzła dla samodzielnego hostowania podczerwony.  

   Wykonaj przebieg testu wydajności i zanotuj osiągniętą wydajność, a także rzeczywiste wartości używane, takie jak diu i kopie równoległe. Zapoznaj się [z monitorowaniem działań kopiowania,](copy-activity-monitoring.md) jak zbierać używane wyniki uruchamiania i ustawienia wydajności, a także dowiedzieć się, jak [rozwiązywać problemy z wydajnością działania kopiowania,](copy-activity-performance-troubleshooting.md) aby zidentyfikować i rozwiązać wąskie gardło. 

   Iteracji do przeprowadzenia dodatkowych przebiegów testów wydajności po rozwiązywaniu problemów i dostrajania wskazówki. Po uruchomieniu działania pojedynczej kopii nie można osiągnąć lepszą przepływność, należy rozważyć, aby zmaksymalizować przepływność agregacji, uruchamiając wiele kopii jednocześnie odnosząc się do kroku 3.


3. **Jak zmaksymalizować agregującą przepustowość, uruchamiając jednocześnie wiele kopii:**

   Teraz, gdy zmaksymalizowano wydajność działania pojedynczej kopii, jeśli nie osiągnięto jeszcze górnych limitów przepływności środowiska — sieci, magazynu danych źródłowych i docelowego magazynu danych — można uruchamiać wiele działań kopiowania równolegle przy użyciu konstrukcji przepływu sterowania ADF, takich jak [dla każdej pętli.](control-flow-for-each-activity.md) Należy zapoznać się [z poleceniem Kopiuj pliki z wielu kontenerów](solution-template-copy-files-multiple-containers.md), [Migruj dane z Amazon S3 do ADLS Gen2](solution-template-migration-s3-azure.md)lub [Kopiuj zbiorczo z](solution-template-bulk-copy-with-control-table.md) szablonami rozwiązań tabeli sterowania jako ogólny przykład.

5. **Rozwiń konfigurację do całego zestawu danych.** Po spełnieniu wyników wykonania i wydajności, można rozwinąć definicję i potoku, aby objąć cały zestaw danych.

## <a name="troubleshoot-copy-activity-performance"></a>Rozwiązywanie problemów z wydajnością działania kopiowania

Wykonaj [kroki dostrajania wydajności,](#performance-tuning-steps) aby zaplanować i przeprowadzić test wydajności dla twojego scenariusza. Dowiedz się, jak rozwiązać problem z wydajnością każdego uruchomienia działania kopiowania w usłudze Azure Data Factory na podstawie funkcji Rozwiązywanie problemów z [wydajnością działania kopiowania.](copy-activity-performance-troubleshooting.md)

## <a name="copy-performance-optimization-features"></a>Kopiowanie funkcji optymalizacji wydajności

Usługa Azure Data Factory oferuje następujące funkcje optymalizacji wydajności:

- [Jednostki integracji danych](#data-integration-units)
- [Skalowalność środowiska uruchomieniowego integracji hostowanego samodzielnie](#self-hosted-integration-runtime-scalability)
- [Kopia równoległa](#parallel-copy)
- [Kopia etapowa](#staged-copy)

### <a name="data-integration-units"></a>Jednostki integracji danych

Jednostka integracji danych to miara reprezentująca moc (kombinację procesora CPU, pamięci i alokacji zasobów sieciowych) pojedynczej jednostki w usłudze Azure Data Factory. Jednostka integracji danych ma zastosowanie tylko do [środowiska wykonawczego integracji platformy Azure,](concepts-integration-runtime.md#azure-integration-runtime)ale nie [do samodzielnego hostowanego środowiska wykonawczego integracji.](concepts-integration-runtime.md#self-hosted-integration-runtime) [Dowiedz się więcej](copy-activity-performance-features.md#data-integration-units).

### <a name="self-hosted-integration-runtime-scalability"></a>Skalowalność środowiska uruchomieniowego integracji hostowanego samodzielnie

Aby hostować rosnące równoczesne obciążenie lub osiągnąć wyższą wydajność, można skalować w górę lub skalować w poziomie środowisko uruchomieniowe integracji hostowanego samodzielnie. [Dowiedz się więcej](copy-activity-performance-features.md#self-hosted-integration-runtime-scalability).

### <a name="parallel-copy"></a>Kopia równoległa

Można ustawić kopię równoległą, aby wskazać równoległość, której ma używać działanie kopiowania. Tę właściwość można interpretować jako maksymalną liczbę wątków w ramach działania kopiowania, które odczytują ze źródła lub zapisują do magazynów danych ujścia równolegle. [Dowiedz się więcej](copy-activity-performance-features.md#parallel-copy).

### <a name="staged-copy"></a>Kopia etapowa

Podczas kopiowania danych z magazynu danych źródłowych do magazynu danych ujścia, można użyć magazynu obiektów Blob jako tymczasowego magazynu przemieszczania. [Dowiedz się więcej](copy-activity-performance-features.md#staged-copy).

## <a name="next-steps"></a>Następne kroki
Zobacz inne artykuły dotyczące aktywności kopiowania:

- [Omówienie działania kopiowania](copy-activity-overview.md)
- [Rozwiązywanie problemów z wydajnością działania kopiowania](copy-activity-performance-troubleshooting.md)
- [Kopiowanie funkcji optymalizacji wydajności działania](copy-activity-performance-features.md)
- [Użyj usługi Azure Data Factory do migracji danych z usługi Data Lake lub magazynu danych na platformę Azure](data-migration-guidance-overview.md)
- [Migrowanie danych z usługi Amazon S3 do usługi Azure Storage](data-migration-guidance-s3-azure-storage.md)
