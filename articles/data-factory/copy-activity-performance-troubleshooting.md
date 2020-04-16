---
title: Rozwiązywanie problemów z wydajnością działania kopiowania
description: Dowiedz się, jak rozwiązywać problemy z wydajnością działania kopiowania w usłudze Azure Data Factory.
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
ms.openlocfilehash: 6df1903e828c0c4cafa6589d4a85f4016bed893e
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414139"
---
# <a name="troubleshoot-copy-activity-performance"></a>Rozwiązywanie problemów z wydajnością działania kopiowania

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule opisano, jak rozwiązać problem z wydajnością działania kopiowania w usłudze Azure Data Factory. 

Po uruchomieniu działania kopiowania, można zebrać statystyki wyników uruchomienia i wydajności w widoku [monitorowania aktywności kopiowania.](copy-activity-monitoring.md) Poniżej przedstawiono przykład.

![Szczegóły uruchomienia działania kopiowania monitoruj](./media/copy-activity-overview/monitor-copy-activity-run-details.png)

## <a name="performance-tuning-tips"></a>Porady dotyczące dostrajania wydajności

W niektórych scenariuszach po uruchomieniu działania kopiowania w fabryce danych u góry zobaczysz **"Porady dotyczące dostrajania wydajności",** jak pokazano w powyższym przykładzie. Porady informują o wąskim gardle zidentyfikowanym przez podajnikU ADF dla tego konkretnego uruchomienia kopii, wraz z sugestią, jak zwiększyć przepływność kopiowania. Spróbuj ponownie zamienić polecenie, a następnie uruchom kopię ponownie.

Jako punkt odniesienia obecnie wskazówki dotyczące dostrajania wydajności zawierają sugestie dotyczące następujących przypadków:

| Kategoria              | Porady dotyczące dostrajania wydajności                                      |
| --------------------- | ------------------------------------------------------------ |
| Specyficzny magazyn danych   | Ładowanie danych do **usługi Azure Synpase Analytics (dawniej SQL DW)**: sugeruj użycie instrukcji PolyBase lub COPY, jeśli nie jest używana. |
| &nbsp;                | Kopiowanie danych z/do **usługi Azure SQL Database:** gdy obiekt DTU jest w trakcie wysokiego wykorzystania, zaproponuj uaktualnienie do wyższej warstwy. |
| &nbsp;                | Kopiowanie danych z/do **usługi Azure Cosmos DB:** gdy ru jest w trakcie wysokiego wykorzystania, zaproponuj uaktualnienie do większych RU. |
| &nbsp;                | Połknięcia danych z **Amazon Redshift**: zasugeruj użycie UNLOAD, jeśli nie jest używany. |
| Ograniczanie przepustowości magazynu danych | Jeśli liczba operacji odczytu/zapisu są ograniczane przez magazyn danych podczas kopiowania, zaproponuj sprawdzanie i zwiększyć dozwoloną szybkość żądania dla magazynu danych lub zmniejszyć równoczesne obciążenie. |
| Środowisko uruchomieniowe integracji  | Jeśli używasz **self-hosted Integration Runtime (IR)** i działania kopiowania czeka długo w kolejce, aż IR ma dostępny zasób do wykonania, sugeruj skalowanie w poziomie/w górę podczerwieni. |
| &nbsp;                | Jeśli używasz **środowiska uruchomieniowego integracji platformy Azure,** który znajduje się w nieoptymalnym regionie, co powoduje powolny odczyt/zapis, zaproponuj skonfigurowanie do używania środowiska IR w innym regionie. |
| Odporność na uszkodzenia       | Jeśli skonfigurujesz odporność na uszkodzenia i pomijanie niezgodnych wierszy powoduje powolną wydajność, sugeruj zapewnienie zgodności danych źródłowych i ujścia. |
| Kopia etapowa           | Jeśli kopia etapowa jest skonfigurowana, ale nie jest przydatna dla pary źródła ujścia, zaproponuj jej usunięcie. |
| Resume                | Po wznowieniu aktywności kopiowania z ostatniego punktu awarii, ale zdarzy się, aby zmienić ustawienie DIU po uruchomieniu oryginalnym, należy pamiętać, że nowe ustawienie DIU nie działa. |

## <a name="understand-copy-activity-execution-details"></a>Opis szczegółów wykonywania działania kopiowania

Szczegóły wykonania i czas trwania w dolnej części widoku monitorowania działania kopiowania opisuje kluczowych etapów działania kopiowania przechodzi (zobacz przykład na początku tego artykułu), co jest szczególnie przydatne do rozwiązywania problemów z wydajnością kopiowania. Wąskie gardło przebiegu kopii jest tym, który ma najdłuższy czas trwania. Zapoznaj się z poniższą tabelą dotyczącą definicji każdego etapu i dowiedz się, jak [rozwiązywać problemy z aktywnością kopiowania na platformie Azure IR](#troubleshoot-copy-activity-on-azure-ir) i rozwiązywać problemy z [kopiowaniem na samodzielnym podczerwieni](#troubleshoot-copy-activity-on-self-hosted-ir) z takimi informacjami.

| Etap           | Opis                                                  |
| --------------- | ------------------------------------------------------------ |
| Kolejka           | Czas, jaki upłynął, dopóki działanie kopiowania faktycznie rozpocznie się w czasie wykonywania integracji. |
| Skrypt przed kopiowaniem | Czas, jaki upłynął między aktywnością kopiowania, począwszy od podczerwania, a zakończeniem wykonywania skryptu przed kopiowaniem w magazynie danych ujścia. Zastosuj podczas konfigurowania skryptu przed kopiowaniem dla ujścia bazy danych, na przykład podczas zapisywania danych w usłudze Azure SQL Database należy oczyścić przed skopiowaniem nowych danych. |
| Transfer        | Czas, jaki upłynął między końcem poprzedniego kroku a podczerwienią przesyłania wszystkich danych ze źródła do ujścia. Podkroki w obszarze "Transfer" przebiegają równolegle.<br><br>- **Czas do pierwszego bajtu:** Czas, jaki upłynął między końcem poprzedniego kroku a czasem, kiedy iR odbiera pierwszy bajt z magazynu danych źródłowych. Dotyczy źródeł nienapartych na plikach.<br>- **Źródło aukcji:** Ilość czasu spędzonego na wyliczanie plików źródłowych lub partycji danych. Ten ostatni ma zastosowanie podczas konfigurowania opcji partycji dla źródeł baz danych, na przykład podczas kopiowania danych z baz danych, takich jak Oracle / SAP HANA / Teradata / Netezza / etc.<br/>-**Czytanie ze źródła:** Czas poświęcony na pobieranie danych z magazynu danych źródłowych.<br/>- **Pisanie do zlewu:** Ilość czasu poświęconego na zapisywanie danych do magazynu danych ujścia. |

## <a name="troubleshoot-copy-activity-on-azure-ir"></a>Rozwiązywanie problemów z aktywnośćą kopiowania na podstawie usługi Azure IR

Wykonaj [kroki dostrajania wydajności,](copy-activity-performance.md#performance-tuning-steps) aby zaplanować i przeprowadzić test wydajności dla twojego scenariusza. 

Gdy wydajność działania kopiowania nie spełnia twoich oczekiwań, aby rozwiązać problem z działaniem pojedynczej kopii uruchomionym w czasie wykonywania integracji platformy Azure, jeśli widzisz [porady dotyczące dostrajania wydajności](#performance-tuning-tips) wyświetlane w widoku monitorowania kopiowania, zastosuj sugestię i spróbuj ponownie. W przeciwnym razie [należy zrozumieć szczegóły wykonania działania kopiowania,](#understand-copy-activity-execution-details)sprawdzić, który etap ma **najdłuższy** czas trwania i zastosować poniższe wskazówki, aby zwiększyć wydajność kopiowania:

- **"Skrypt przed kopiowaniem" doświadczony długi czas trwania:** oznacza to, że skrypt przed kopiowaniem uruchomiony w bazie danych zlewu trwa długo, aby zakończyć. Dostroić określoną logikę skryptu przed kopiowaniem, aby zwiększyć wydajność. Jeśli potrzebujesz dalszej pomocy w ulepszaniu skryptu, skontaktuj się z zespołem bazy danych.

- **"Transfer - Time to first byte" doświadczony długi czas pracy:** oznacza to, że zapytanie źródłowe trwa długo, aby zwrócić wszelkie dane. Sprawdź i zoptymalizuj kwerendę lub serwer. Jeśli potrzebujesz dalszej pomocy, skontaktuj się z zespołem magazynu danych.

- **"Transfer - Źródło aukcji" doświadczył długiego czasu pracy:** oznacza to wyliczanie plików źródłowych lub partycji danych źródłowej bazy danych jest powolny.

  - Podczas kopiowania danych ze źródła opartego na plikach, jeśli używasz `wildcardFileName` **filtru wieloznacznego** w ścieżce folderu lub nazwie pliku (`wildcardFolderPath` lub ), lub **użyjesz filtru czasu ostatniej modyfikacji pliku** (`modifiedDatetimeStart` lub`modifiedDatetimeEnd`), uwaga, taki filtr spowoduje, że działanie kopiowania spowoduje wystawienie wszystkich plików w określonym folderze po stronie klienta, a następnie zastosuje filtr. Takie wyliczenie plików może stać się wąskim gardłem, zwłaszcza gdy tylko niewielki zestaw plików spełnia regułę filtru.

    - Sprawdź, czy można [kopiować pliki na podstawie ścieżki pliku podzielonego na partycje datetime lub nazwy](tutorial-incremental-copy-partitioned-file-name-copy-data-tool.md)pliku . Taki sposób nie obciąża strony źródła aukcji.

    - Sprawdź, czy zamiast tego możesz użyć filtru macierzystego magazynu danych, w szczególności **"prefiks**" dla Amazon S3 i Azure Blob. Filtr prefiksów jest filtrem po stronie serwera magazynu danych i miałby znacznie lepszą wydajność.

    - Należy rozważyć podzielenie pojedynczego dużego zestawu danych na kilka mniejszych zestawów danych i niech te zadania kopiowania są uruchamiane jednocześnie, każda rozwiązuje część danych. Możesz to zrobić za pomocą funkcji Odnośnik/GetMetadata + ForEach + Copy. Na przykład [można znaleźć pozycję Kopiuj pliki z wielu kontenerów](solution-template-copy-files-multiple-containers.md) lub Migruj dane z szablonów rozwiązań [Amazon S3 do ADLS Gen2.](solution-template-migration-s3-azure.md)

  - Sprawdź, czy usługa ADF zgłasza błąd ograniczania przepustowości w źródle lub jeśli magazyn danych jest w stanie wysokiego wykorzystania. Jeśli tak, zmniejsz obciążenia w magazynie danych lub spróbuj skontaktować się z administratorem magazynu danych, aby zwiększyć limit ograniczania przepustowości lub dostępny zasób.

  - Użyj usługi Azure IR w tym samym lub blisko regionu magazynu danych źródłowych.

- **"Transfer - czytanie ze źródła" doświadczonych długi czas pracy:** 

  - Zastosuj najlepsze rozwiązanie dotyczące ładowania danych specyficznych dla łącznika, jeśli ma zastosowanie. Na przykład podczas kopiowania danych z [Amazon Redshift,](connector-amazon-redshift.md)skonfigurować do korzystania z Redshift UNLOAD.

  - Sprawdź, czy usługa ADF zgłasza błąd ograniczania przepustowości w źródle lub jeśli magazyn danych jest obciążony wysokim wykorzystaniem. Jeśli tak, zmniejsz obciążenia w magazynie danych lub spróbuj skontaktować się z administratorem magazynu danych, aby zwiększyć limit ograniczania przepustowości lub dostępny zasób.

  - Sprawdź źródło kopii i wzór ujścia: 

    - Jeśli wzorzec kopiowania obsługuje większe niż 4 jednostki integracji danych (DIU) — zapoznaj się z [tą sekcją](copy-activity-performance-features.md#data-integration-units) dotyczącą szczegółów, zazwyczaj możesz spróbować zwiększyć dius, aby uzyskać lepszą wydajność. 

    - W przeciwnym razie należy rozważyć podzielenie pojedynczego dużego zestawu danych na kilka mniejszych zestawów danych i pozwolić tym zadaniam kopiowania uruchamiać się jednocześnie, z których każda rozwiązuje część danych. Możesz to zrobić za pomocą funkcji Odnośnik/GetMetadata + ForEach + Copy. Należy zapoznać się [z poleceniem Kopiuj pliki z wielu kontenerów](solution-template-copy-files-multiple-containers.md), [Migruj dane z Amazon S3 do ADLS Gen2](solution-template-migration-s3-azure.md)lub [Kopiuj zbiorczo z](solution-template-bulk-copy-with-control-table.md) szablonami rozwiązań tabeli sterowania jako ogólny przykład.

  - Użyj usługi Azure IR w tym samym lub blisko regionu magazynu danych źródłowych.

- **"Transfer - pisanie do zlewu" doświadczonych długi czas pracy:**

  - Zastosuj najlepsze rozwiązanie dotyczące ładowania danych specyficznych dla łącznika, jeśli ma zastosowanie. Na przykład podczas kopiowania danych do [usługi Azure Synapse Analytics](connector-azure-sql-data-warehouse.md) (dawniej SQL DW), należy użyć PolyBase lub COPY instrukcji. 

  - Sprawdź, czy usługa ADF zgłasza błąd ograniczania przepustowości w zlewie lub jeśli magazyn danych jest obciążony wysokim wykorzystaniem. Jeśli tak, zmniejsz obciążenia w magazynie danych lub spróbuj skontaktować się z administratorem magazynu danych, aby zwiększyć limit ograniczania przepustowości lub dostępny zasób.

  - Sprawdź źródło kopii i wzór ujścia: 

    - Jeśli wzorzec kopiowania obsługuje większe niż 4 jednostki integracji danych (DIU) — zapoznaj się z [tą sekcją](copy-activity-performance-features.md#data-integration-units) dotyczącą szczegółów, zazwyczaj możesz spróbować zwiększyć dius, aby uzyskać lepszą wydajność. 

    - W przeciwnym razie stopniowo dostrajaj [równoległe kopie,](copy-activity-performance-features.md)zwróć uwagę, że zbyt wiele kopii równoległych może nawet zaszkodzić wydajności.

  - Użyj usługi Azure IR w tym samym lub blisko regionu magazynu danych ujścia.

## <a name="troubleshoot-copy-activity-on-self-hosted-ir"></a>Rozwiązywanie problemów z aktywność kopiowania na samodzielnym podczerwonym podczerwone

Wykonaj [kroki dostrajania wydajności,](copy-activity-performance.md#performance-tuning-steps) aby zaplanować i przeprowadzić test wydajności dla twojego scenariusza. 

Gdy wydajność kopiowania nie spełnia twoich oczekiwań, aby rozwiązać problem z działaniem pojedynczej kopii uruchomionym w czasie wykonywania integracji platformy Azure, jeśli widzisz [wskazówki dotyczące dostrajania wydajności](#performance-tuning-tips) wyświetlane w widoku monitorowania kopiowania, zastosuj sugestię i spróbuj ponownie. W przeciwnym razie [należy zrozumieć szczegóły wykonania działania kopiowania,](#understand-copy-activity-execution-details)sprawdzić, który etap ma **najdłuższy** czas trwania i zastosować poniższe wskazówki, aby zwiększyć wydajność kopiowania:

- **"Kolejka" doświadczonych długi czas trwania:** oznacza to, że działanie kopiowania czeka długo w kolejce, aż samodzielnie hostowane IR ma zasobów do wykonania. Sprawdź pojemność i użycie podczerwień i [skaluj w górę lub w poziomie](create-self-hosted-integration-runtime.md#high-availability-and-scalability) w zależności od obciążenia.

- **"Transfer - Time to first byte" doświadczony długi czas pracy:** oznacza to, że zapytanie źródłowe trwa długo, aby zwrócić wszelkie dane. Sprawdź i zoptymalizuj kwerendę lub serwer. Jeśli potrzebujesz dalszej pomocy, skontaktuj się z zespołem magazynu danych.

- **"Transfer - Źródło aukcji" doświadczył długiego czasu pracy:** oznacza to wyliczanie plików źródłowych lub partycji danych źródłowej bazy danych jest powolny.

  - Sprawdź, czy komputer z własnym hostem podczerwony ma małe opóźnienia łączące się ze źródłowym magazynem danych. Jeśli źródło jest na platformie Azure, można użyć [tego narzędzia,](http://www.azurespeed.com/Azure/Latency) aby sprawdzić opóźnienie z komputera podczerwone hostowane samodzielnie do regionu platformy Azure, tym mniej lepiej.

  - Podczas kopiowania danych ze źródła opartego na plikach, jeśli używasz `wildcardFileName` **filtru wieloznacznego** w ścieżce folderu lub nazwie pliku (`wildcardFolderPath` lub ), lub **użyjesz filtru czasu ostatniej modyfikacji pliku** (`modifiedDatetimeStart` lub`modifiedDatetimeEnd`), uwaga, taki filtr spowoduje, że działanie kopiowania spowoduje wystawienie wszystkich plików w określonym folderze po stronie klienta, a następnie zastosuje filtr. Takie wyliczenie plików może stać się wąskim gardłem, zwłaszcza gdy tylko niewielki zestaw plików spełnia regułę filtru.

    - Sprawdź, czy można [kopiować pliki na podstawie ścieżki pliku podzielonego na partycje datetime lub nazwy](tutorial-incremental-copy-partitioned-file-name-copy-data-tool.md)pliku . Taki sposób nie obciąża strony źródła aukcji.

    - Sprawdź, czy zamiast tego możesz użyć filtru macierzystego magazynu danych, w szczególności **"prefiks**" dla Amazon S3 i Azure Blob. Filtr prefiksów jest filtrem po stronie serwera magazynu danych i miałby znacznie lepszą wydajność.

    - Należy rozważyć podzielenie pojedynczego dużego zestawu danych na kilka mniejszych zestawów danych i niech te zadania kopiowania są uruchamiane jednocześnie, każda rozwiązuje część danych. Możesz to zrobić za pomocą funkcji Odnośnik/GetMetadata + ForEach + Copy. Na przykład [można znaleźć pozycję Kopiuj pliki z wielu kontenerów](solution-template-copy-files-multiple-containers.md) lub Migruj dane z szablonów rozwiązań [Amazon S3 do ADLS Gen2.](solution-template-migration-s3-azure.md)

  - Sprawdź, czy usługa ADF zgłasza błąd ograniczania przepustowości w źródle lub jeśli magazyn danych jest w stanie wysokiego wykorzystania. Jeśli tak, zmniejsz obciążenia w magazynie danych lub spróbuj skontaktować się z administratorem magazynu danych, aby zwiększyć limit ograniczania przepustowości lub dostępny zasób.

- **"Transfer - czytanie ze źródła" doświadczonych długi czas pracy:** 

  - Sprawdź, czy komputer z własnym hostem podczerwony ma małe opóźnienia łączące się ze źródłowym magazynem danych. Jeśli źródło jest na platformie Azure, można użyć [tego narzędzia,](http://www.azurespeed.com/Azure/Latency) aby sprawdzić opóźnienie z komputera podczerwone hostowane samodzielnie do regionów platformy Azure, tym mniej lepiej.

  - Sprawdź, czy komputer z własnym hostem podczerwony ma wystarczającą przepustowość przychodzącą do odczytu i transferu danych. Jeśli źródłowy magazyn danych znajduje się na platformie Azure, można użyć [tego narzędzia,](https://www.azurespeed.com/Azure/Download) aby sprawdzić szybkość pobierania.

  - Sprawdź samodzielnie hostowany procesor IR użycie procesora i pamięci trend w witrynie Azure portal -> na stronie przeglądu fabryki danych >. Należy rozważyć [skalowanie w górę/w poziomie IR,](create-self-hosted-integration-runtime.md#high-availability-and-scalability) jeśli użycie procesora CPU jest wysokie lub dostępna pamięć jest niska.

  - Zastosuj najlepsze rozwiązanie dotyczące ładowania danych specyficznych dla łącznika, jeśli ma zastosowanie. Przykład:

    - Podczas kopiowania danych z [Oracle](connector-oracle.md#oracle-as-source), [Netezza](connector-netezza.md#netezza-as-source), [Teradata](connector-teradata.md#teradata-as-source), [SAP HANA](connector-sap-hana.md#sap-hana-as-source), [SAP Table](connector-sap-table.md#sap-table-as-source)i SAP [Open Hub](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source)), włącz opcje partycji danych, aby kopiować dane równolegle.

    - Podczas kopiowania danych z [usługi HDFS](connector-hdfs.md)należy skonfigurować do używania protokołu DistCp.

    - Podczas kopiowania danych z [Amazon Redshift,](connector-amazon-redshift.md)skonfigurować do korzystania z Redshift UNLOAD.

  - Sprawdź, czy usługa ADF zgłasza błąd ograniczania przepustowości w źródle lub jeśli magazyn danych jest objęty wysokim wykorzystaniem. Jeśli tak, zmniejsz obciążenia w magazynie danych lub spróbuj skontaktować się z administratorem magazynu danych, aby zwiększyć limit ograniczania przepustowości lub dostępny zasób.

  - Sprawdź źródło kopii i wzór ujścia: 

    - Jeśli kopiujesz dane z magazynów danych z opcją partycji, należy rozważyć stopniowe dostrajanie [kopii równoległych,](copy-activity-performance-features.md)należy pamiętać, że zbyt wiele kopii równoległych może nawet zaszkodzić wydajności.

    - W przeciwnym razie należy rozważyć podzielenie pojedynczego dużego zestawu danych na kilka mniejszych zestawów danych i pozwolić tym zadaniam kopiowania uruchamiać się jednocześnie, z których każda rozwiązuje część danych. Możesz to zrobić za pomocą funkcji Odnośnik/GetMetadata + ForEach + Copy. Należy zapoznać się [z poleceniem Kopiuj pliki z wielu kontenerów](solution-template-copy-files-multiple-containers.md), [Migruj dane z Amazon S3 do ADLS Gen2](solution-template-migration-s3-azure.md)lub [Kopiuj zbiorczo z](solution-template-bulk-copy-with-control-table.md) szablonami rozwiązań tabeli sterowania jako ogólny przykład.

- **"Transfer - pisanie do zlewu" doświadczonych długi czas pracy:**

  - Zastosuj najlepsze rozwiązanie dotyczące ładowania danych specyficznych dla łącznika, jeśli ma zastosowanie. Na przykład podczas kopiowania danych do [usługi Azure Synapse Analytics](connector-azure-sql-data-warehouse.md) (dawniej SQL DW), należy użyć PolyBase lub COPY instrukcji. 

  - Sprawdź, czy samodzielnie hostowany na komputerze podczerwony ma małe opóźnienia łączące się z magazynem danych ujścia. Jeśli ujście jest na platformie Azure, można użyć [tego narzędzia,](http://www.azurespeed.com/Azure/Latency) aby sprawdzić opóźnienie z komputera podczerwone hostowane samodzielnie do regionu platformy Azure, tym mniej lepiej.

  - Sprawdź, czy komputer z własnym hostem podczerwieni ma wystarczającą przepustowość wychodzącą, aby efektywnie przesyłać i zapisywać dane. Jeśli magazyn danych ujścia znajduje się na platformie Azure, można użyć [tego narzędzia,](https://www.azurespeed.com/Azure/UploadLargeFile) aby sprawdzić szybkość przekazywania.

  - Sprawdź, czy samodzielnie hostowany procesor IR i trend użycia pamięci w witrynie Azure portal -> strony przeglądu fabryki danych >. Należy rozważyć [skalowanie w górę/w poziomie IR,](create-self-hosted-integration-runtime.md#high-availability-and-scalability) jeśli użycie procesora CPU jest wysokie lub dostępna pamięć jest niska.

  - Sprawdź, czy usługa ADF zgłasza błąd ograniczania przepustowości w zlewie lub jeśli magazyn danych jest obciążony wysokim wykorzystaniem. Jeśli tak, zmniejsz obciążenia w magazynie danych lub spróbuj skontaktować się z administratorem magazynu danych, aby zwiększyć limit ograniczania przepustowości lub dostępny zasób.

  - Należy wziąć pod uwagę, aby stopniowo dostroić [równoległe kopie,](copy-activity-performance-features.md)należy pamiętać, że zbyt wiele kopii równoległych może nawet zaszkodzić wydajności.

## <a name="other-references"></a>Inne referencje

Oto monitorowanie wydajności i dostrajanie odwołań dla niektórych obsługiwanych magazynów danych:

* Magazyn obiektów Blob platformy Azure: [cele dotyczące skalowalności i wydajności magazynu obiektów Blob](../storage/blobs/scalability-targets.md) oraz [listy kontrolnej wydajności i skalowalności magazynu obiektów Blob.](../storage/blobs/storage-performance-checklist.md)
* Magazyn tabel platformy Azure: [cele dotyczące skalowalności i wydajności dla magazynu tabel](../storage/tables/scalability-targets.md) oraz listy [kontrolnej wydajności i skalowalności dla magazynu tabel](../storage/tables/storage-performance-checklist.md).
* Usługa Azure SQL Database: można [monitorować wydajność](../sql-database/sql-database-single-database-monitor.md) i sprawdzić procent jednostki transakcji bazy danych (DTU).
* Usługa Azure SQL Data Warehouse: jego możliwości są mierzone w jednostkach hurtowni danych (DWU). Zobacz [Zarządzanie mocą obliczeniową w usłudze Azure SQL Data Warehouse (Omówienie)](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md).
* Usługa Azure Cosmos DB: [poziomy wydajności w usłudze Azure Cosmos DB](../cosmos-db/performance-levels.md).
* Lokalny program SQL Server: [Monitoruj i dostrajaj wydajność](https://msdn.microsoft.com/library/ms189081.aspx).
* Lokalny serwer plików: [dostrajanie wydajności serwerów plików](https://msdn.microsoft.com/library/dn567661.aspx).

## <a name="next-steps"></a>Następne kroki
Zobacz inne artykuły dotyczące aktywności kopiowania:

- [Omówienie działania kopiowania](copy-activity-overview.md)
- [Przewodnik po wydajności i skalowalności działania kopiowania](copy-activity-performance.md)
- [Kopiowanie funkcji optymalizacji wydajności działania](copy-activity-performance-features.md)
- [Użyj usługi Azure Data Factory do migracji danych z usługi Data Lake lub magazynu danych na platformę Azure](data-migration-guidance-overview.md)
- [Migrowanie danych z usługi Amazon S3 do usługi Azure Storage](data-migration-guidance-s3-azure-storage.md)
