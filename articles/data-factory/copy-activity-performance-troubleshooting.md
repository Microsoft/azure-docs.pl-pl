---
title: Rozwiązywanie problemów z wydajnością działania kopiowania
description: Dowiedz się więcej na temat rozwiązywania problemów z wydajnością działania kopiowania w Azure Data Factory.
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
ms.date: 12/09/2020
ms.openlocfilehash: d22d040b0001ee30e29c551e686a7cb6bc47c2af
ms.sourcegitcommit: fec60094b829270387c104cc6c21257826fccc54
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/09/2020
ms.locfileid: "96921926"
---
# <a name="troubleshoot-copy-activity-performance"></a>Rozwiązywanie problemów z wydajnością działania kopiowania

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule opisano sposób rozwiązywania problemów z wydajnością działania kopiowania w Azure Data Factory. 

Po uruchomieniu działania kopiowania można zebrać wyniki uruchamiania i statystyki wydajności w widoku [monitorowanie działania kopiowania](copy-activity-monitoring.md) . Poniżej przedstawiono przykład.

![Monitoruj szczegóły uruchomienia działania kopiowania](./media/copy-activity-overview/monitor-copy-activity-run-details.png)

## <a name="performance-tuning-tips"></a>Porady dotyczące dostrajania wydajności

W niektórych scenariuszach, gdy uruchomisz działanie kopiowania w Data Factory, zobaczysz **"porady dotyczące dostrajania wydajności"** u góry, jak pokazano w powyższym przykładzie. Porady przedstawiają wąskie gardła identyfikowane przez ADF dla danego przebiegu kopii, a także sugestie dotyczące sposobu zwiększenia przepływności kopiowania. Spróbuj ponownie wykonać zmiany, a następnie uruchom kopię.

Obecnie wskazówki dotyczące dostrajania wydajności udostępniają sugestie dotyczące następujących przypadków:

| Kategoria              | Porady dotyczące dostrajania wydajności                                      |
| --------------------- | ------------------------------------------------------------ |
| Specyficzne dla magazynu danych   | Ładowanie danych do **usługi Azure Synapse Analytics**: Sugeruj przy użyciu instrukcji Base lub Copy, jeśli nie jest ona używana. |
| &nbsp;                | Kopiowanie danych z/do **Azure SQL Database**: gdy jednostka DTU jest w wysokim obciążeniu, Sugeruj uaktualnienie do wyższej warstwy. |
| &nbsp;                | Kopiowanie danych z/do **Azure Cosmos DB**: gdy ru jest w wysokim wykorzystaniu, Sugeruj uaktualnienie do większej wartości ru. |
|                       | Kopiowanie danych z **tabeli SAP**: podczas kopiowania dużej ilości danych, Sugeruj opcję partycji łącznika SAP, aby włączyć równoległe ładowanie i zwiększyć maksymalną liczbę partycji. |
| &nbsp;                | Pozyskiwanie danych z **Amazon RedShift**: SUGERUJ użycie Unload, jeśli nie jest używany. |
| Ograniczanie magazynu danych | Jeśli liczba operacji odczytu/zapisu jest ograniczona przez magazyn danych podczas kopiowania, Sugeruj sprawdzanie i Zwiększ liczbę dozwolonych żądań dla magazynu danych lub Zmniejsz obciążenie współbieżne. |
| Środowisko Integration Runtime  | W przypadku korzystania z samodzielnej obsługi **Integration Runtime (IR)** i działania kopiowania czekają na ukończenie kolejki do momentu, w którym środowisko IR będzie dostępne, Sugeruj skalowanie w górę/w górę dla środowiska IR. |
| &nbsp;                | Jeśli używasz **Azure Integration Runtime** , który znajduje się w nieoptymalnym regionie, co powoduje powolne odczyt/zapis, Sugeruj konfigurację, aby używać środowiska IR w innym regionie. |
| Odporność na uszkodzenia       | W przypadku skonfigurowania odporności na uszkodzenia i pomijania niezgodnych wierszy wyniki są powolne, Sugeruj, że dane źródłowe i ujścia są zgodne. |
| Kopia przygotowana           | Jeśli kopia przygotowana jest skonfigurowana, ale nie jest pomocna dla pary Source-sink, Sugeruj jej usunięcie. |
| Wznów                | Gdy działanie kopiowania jest wznawiane od ostatniego punktu awarii, ale zmiana ustawienia DIU po zakończeniu oryginalnego przebiegu jest konieczna, należy pamiętać, że nowe ustawienie DIU nie zacznie obowiązywać. |

## <a name="understand-copy-activity-execution-details"></a>Informacje o wykonywaniu działania kopiowania

Szczegóły wykonania i czasy trwania w dolnej części widoku monitorowanie działania kopiowania opisują kluczowe etapy działania kopiowania (Zobacz przykład na początku tego artykułu), które jest szczególnie przydatne w przypadku rozwiązywania problemów z wydajnością kopiowania. Wąskie gardła przebiegu kopii są takie same, jak najdłuższy czas trwania. Zapoznaj się z poniższą tabelą w każdej definicji etapu i Dowiedz się, jak [rozwiązywać problemy z działaniem kopiowania na Azure IR](#troubleshoot-copy-activity-on-azure-ir) i [rozwiązywać problemy z działaniem kopiowania na samoobsługowym środowisku IR](#troubleshoot-copy-activity-on-self-hosted-ir) z takimi informacjami.

| Etap           | Opis                                                  |
| --------------- | ------------------------------------------------------------ |
| Kolejka           | Czas, który upłynął do momentu rzeczywistego uruchomienia działania kopiowania w środowisku Integration Runtime. |
| Skrypt poprzedzający kopiowanie | Upłynęło czasu między działaniem kopiowania, rozpoczynającym się od działania funkcji IR i kopiowania, kończący wykonywanie skryptu przed kopiowaniem w magazynie danych ujścia. Zastosuj podczas konfigurowania skryptu przed kopiowaniem dla obiektów ujścia bazy danych, na przykład podczas zapisywania danych do Azure SQL Database Wyczyść przed skopiowaniem nowych danych. |
| Transfer        | Czas, który upłynął między końcem poprzedniego kroku a środowiskiem IR transferu wszystkie dane ze źródła do ujścia. <br/>Należy zwrócić uwagę na podetapy w obszarze transfer równoległy, a niektóre operacje nie są teraz wyświetlane, np. analizowanie/generowanie formatu pliku.<br><br/>- **Czas do pierwszego bajtu:** Czas, który upłynął między końcem poprzedniego kroku a czasem, gdy środowisko IR odbierze pierwszy bajt z magazynu danych źródłowych. Dotyczy źródeł nieopartych na plikach.<br>- **Źródło listy:** Ilość czasu poświęcanego na Wyliczenie plików źródłowych lub partycji danych. Ten ostatni ma zastosowanie w przypadku konfigurowania opcji partycji dla źródeł baz danych, na przykład podczas kopiowania danych z baz danych, takich jak Oracle/SAP HANA/Teradata/Netezza/itd.<br/>-**Odczytywanie ze źródła:** Czas spędzony na pobieraniu danych ze źródłowego magazynu danych.<br/>- **Zapisywanie do ujścia:** Ilość czasu poświęcanego na zapisanie danych do magazynu danych ujścia. Zwróć uwagę na to, że w tym momencie niektóre łączniki nie mają tej metryki, w tym Azure Wyszukiwanie poznawcze, Azure Eksplorator danych, Azure Table Storage, Oracle, SQL Server, Common Data Service, Dynamics 365, Dynamics CRM, Salesforce/Cloud Service chmura. |

## <a name="troubleshoot-copy-activity-on-azure-ir"></a>Rozwiązywanie problemów z działaniem kopiowania na Azure IR

Postępuj zgodnie z [krokami dostrajania wydajności](copy-activity-performance.md#performance-tuning-steps) , aby planować i przeprowadzać testy wydajnościowe w danym scenariuszu. 

Gdy wydajność działania kopiowania nie spełnia oczekiwań, aby rozwiązywać problemy z działaniem pojedynczej kopii uruchomionej w Azure Integration Runtime, jeśli w widoku monitorowanie kopiowania widoczne są [porady dotyczące dostrajania wydajności](#performance-tuning-tips) , Zastosuj sugestię i spróbuj ponownie. W przeciwnym razie należy zapoznać się ze [szczegółami wykonywania działania kopiowania](#understand-copy-activity-execution-details), sprawdzić, który etap ma **najdłuższy** czas trwania, i zastosować poniższe wskazówki, aby zwiększyć wydajność kopiowania:

- **"Skrypt przed kopiowaniem" miał długi czas trwania:** oznacza to, że skrypt poprzedzający kopiowanie działający w bazie danych ujścia kończy się długo. Dostosuj określoną logikę skryptu przed kopiowaniem, aby zwiększyć wydajność. Jeśli potrzebujesz dalszej pomocy na temat ulepszania skryptu, skontaktuj się z zespołem bazy danych.

- **"Czas przesyłania do pierwszego bajtu" był długi czas pracy**: oznacza to, że zapytanie źródłowe zajmuje dużo czasu na zwrócenie jakichkolwiek danych. Sprawdź i zoptymalizuj zapytanie lub serwer. Jeśli potrzebujesz dalszej pomocy, skontaktuj się z zespołem ds. magazynu danych.

- **"Źródło listy transferu" ma długi czas pracy**: oznacza to, że wyliczane pliki źródłowe lub źródłowe partycje danych bazy danych są wolne.
  - W przypadku kopiowania danych z źródła plików, jeśli używasz **filtru symboli wieloznacznych** dla ścieżki folderu lub nazwy pliku ( `wildcardFolderPath` lub `wildcardFileName` ) lub Użyj **filtru czasu ostatniej modyfikacji pliku** ( `modifiedDatetimeStart` lub `modifiedDatetimeEnd` ), zwróć uwagę na to, że ten filtr spowoduje wyświetlenie wszystkich plików w określonym folderze po stronie klienta, a następnie Zastosuj filtr. Takie Wyliczenie plików może stać się wąskim gardłem, szczególnie w przypadku spełnienia przez regułę filtru tylko małego zestawu plików.

    - Sprawdź, czy można [kopiować pliki na podstawie ścieżki lub nazwy pliku podzielonego na partycje DateTime](tutorial-incremental-copy-partitioned-file-name-copy-data-tool.md). Taki sposób nie powoduje obciążenia po stronie źródłowej.

    - Sprawdź, czy możesz zamiast tego użyć filtru natywnego magazynu danych, w tym "**prefiks**" dla usługi Amazon S3/Azure Blob/Azure File Storage i "**listAfter/listBefore**" ADLS Gen1. Te filtry to filtr po stronie serwera magazynu danych i ma znacznie lepszą wydajność.

    - Rozważ podzielenie pojedynczych dużych ilości danych na kilka mniejszych zestawów danych i umożliwienie wykonywania tych zadań kopiowania współbieżnie każdej części danych. Można to zrobić za pomocą metody Lookup/GetMetadata + ForEach + Copy. Zapoznaj się z tematem [Kopiowanie plików z wielu kontenerów](solution-template-copy-files-multiple-containers.md) lub [Migrowanie danych z usługi Amazon S3 do ADLS Gen2](solution-template-migration-s3-azure.md) szablonów rozwiązań jako ogólnego przykładu.

  - Sprawdź, czy funkcja ADF zgłasza jakikolwiek błąd ograniczania w źródle lub czy magazyn danych jest w stanie wyższym niż użycie. Jeśli tak, Zmniejsz obciążenie magazynu danych lub spróbuj skontaktować się z administratorem magazynu danych w celu zwiększenia limitu ograniczania przepustowości lub dostępnego zasobu.

  - Użyj Azure IR w tym samym lub blisko regionu źródłowego magazynu danych.

- **"Transfer-odczyt ze źródła" długotrwały czas pracy**: 

  - Należy zastosować najlepsze rozwiązanie do ładowania danych specyficznych dla łącznika, jeśli ma zastosowanie. Na przykład podczas kopiowania danych z usługi [Amazon RedShift](connector-amazon-redshift.md)należy skonfigurować do korzystania z usługi RedShift Unload.

  - Sprawdź, czy funkcja ADF zgłasza jakikolwiek błąd ograniczania w źródle lub czy magazyn danych jest w wysokim obciążeniu. Jeśli tak, Zmniejsz obciążenie magazynu danych lub spróbuj skontaktować się z administratorem magazynu danych w celu zwiększenia limitu ograniczania przepustowości lub dostępnego zasobu.

  - Sprawdź Źródło kopii i wzorzec ujścia: 

    - Jeśli wzorzec kopiowania obsługuje więcej niż 4 jednostki integracji danych (DIUs) — zapoznaj się z [tą sekcją](copy-activity-performance-features.md#data-integration-units) , aby uzyskać lepszą wydajność. 

    - W przeciwnym razie należy rozważyć rozdzielenie pojedynczych dużych ilości danych na kilka mniejszych zestawów danych i umożliwienie wykonywania tych zadań kopiowania współbieżnie każdej części danych. Można to zrobić za pomocą metody Lookup/GetMetadata + ForEach + Copy. Zapoznaj się z [kopiowaniem plików z wielu kontenerów](solution-template-copy-files-multiple-containers.md), [Migrowanie danych z usługi Amazon S3 do ADLS Gen2](solution-template-migration-s3-azure.md)lub [zbiorcze kopiowanie za pomocą szablonów rozwiązań tabeli formantów](solution-template-bulk-copy-with-control-table.md) .

  - Użyj Azure IR w tym samym lub blisko regionu źródłowego magazynu danych.

- **"Przesyłanie zapisu do ujścia" ma długi czas pracy**:

  - Należy zastosować najlepsze rozwiązanie do ładowania danych specyficznych dla łącznika, jeśli ma zastosowanie. Na przykład podczas kopiowania danych do [usługi Azure Synapse Analytics](connector-azure-sql-data-warehouse.md)Użyj instrukcji Base lub Copy. 

  - Sprawdź, czy funkcja ADF zgłasza jakikolwiek błąd ograniczania przepustowości w ujściach lub czy magazyn danych jest w wysokim obciążeniu. Jeśli tak, Zmniejsz obciążenie magazynu danych lub spróbuj skontaktować się z administratorem magazynu danych w celu zwiększenia limitu ograniczania przepustowości lub dostępnego zasobu.

  - Sprawdź Źródło kopii i wzorzec ujścia: 

    - Jeśli wzorzec kopiowania obsługuje więcej niż 4 jednostki integracji danych (DIUs) — zapoznaj się z [tą sekcją](copy-activity-performance-features.md#data-integration-units) , aby uzyskać lepszą wydajność. 

    - W przeciwnym razie należy stopniowo dostrajać [kopie równoległe](copy-activity-performance-features.md), pamiętaj, że zbyt wiele kopii równoległych może nawet obniżyć wydajność.

  - Użyj Azure IR w tym samym lub blisko regionu magazynu danych ujścia.

## <a name="troubleshoot-copy-activity-on-self-hosted-ir"></a>Rozwiązywanie problemów z kopiowaniem do samodzielnego środowiska IR

Postępuj zgodnie z [krokami dostrajania wydajności](copy-activity-performance.md#performance-tuning-steps) , aby planować i przeprowadzać testy wydajnościowe w danym scenariuszu. 

Gdy wydajność kopiowania nie spełnia oczekiwań, aby rozwiązywać problemy z pojedynczym kopiowaniem uruchomionym na Azure Integration Runtime, jeśli w widoku monitorowanie kopiowania widoczne są [porady dotyczące dostrajania wydajności](#performance-tuning-tips) , Zastosuj sugestię i spróbuj ponownie. W przeciwnym razie należy zapoznać się ze [szczegółami wykonywania działania kopiowania](#understand-copy-activity-execution-details), sprawdzić, który etap ma **najdłuższy** czas trwania, i zastosować poniższe wskazówki, aby zwiększyć wydajność kopiowania:

- **"Kolejka" napotkała długi czas trwania:** oznacza to, że działanie kopiowania czeka długo w kolejce, dopóki nie zostanie uruchomione własne środowisko IR. Sprawdź pojemność i użycie IR oraz Skaluj w [górę lub](create-self-hosted-integration-runtime.md#high-availability-and-scalability) w dół zgodnie z obciążeniem.

- **"Czas przesyłania do pierwszego bajtu" był długi czas pracy**: oznacza to, że zapytanie źródłowe zajmuje dużo czasu na zwrócenie jakichkolwiek danych. Sprawdź i zoptymalizuj zapytanie lub serwer. Jeśli potrzebujesz dalszej pomocy, skontaktuj się z zespołem ds. magazynu danych.

- **"Źródło listy transferu" ma długi czas pracy**: oznacza to, że wyliczane pliki źródłowe lub źródłowe partycje danych bazy danych są wolne.

  - Sprawdź, czy własna maszyna IR ma małe opóźnienia łączące się z magazynem danych źródłowych. Jeśli Twoje źródło znajduje się na platformie Azure, możesz użyć [tego narzędzia](http://www.azurespeed.com/Azure/Latency) , aby sprawdzić opóźnienia z samodzielnej maszyny podczerwieni z regionem świadczenia usługi Azure, im mniej lepiej.

  - W przypadku kopiowania danych z źródła plików, jeśli używasz **filtru symboli wieloznacznych** dla ścieżki folderu lub nazwy pliku ( `wildcardFolderPath` lub `wildcardFileName` ) lub Użyj **filtru czasu ostatniej modyfikacji pliku** ( `modifiedDatetimeStart` lub `modifiedDatetimeEnd` ), zwróć uwagę na to, że ten filtr spowoduje wyświetlenie wszystkich plików w określonym folderze po stronie klienta, a następnie Zastosuj filtr. Takie Wyliczenie plików może stać się wąskim gardłem, szczególnie w przypadku spełnienia przez regułę filtru tylko małego zestawu plików.

    - Sprawdź, czy można [kopiować pliki na podstawie ścieżki lub nazwy pliku podzielonego na partycje DateTime](tutorial-incremental-copy-partitioned-file-name-copy-data-tool.md). Taki sposób nie powoduje obciążenia po stronie źródłowej.

    - Sprawdź, czy możesz zamiast tego użyć filtru natywnego magazynu danych, w tym "**prefiks**" dla usługi Amazon S3/Azure Blob/Azure File Storage i "**listAfter/listBefore**" ADLS Gen1. Te filtry to filtr po stronie serwera magazynu danych i ma znacznie lepszą wydajność.

    - Rozważ podzielenie pojedynczych dużych ilości danych na kilka mniejszych zestawów danych i umożliwienie wykonywania tych zadań kopiowania współbieżnie każdej części danych. Można to zrobić za pomocą metody Lookup/GetMetadata + ForEach + Copy. Zapoznaj się z tematem [Kopiowanie plików z wielu kontenerów](solution-template-copy-files-multiple-containers.md) lub [Migrowanie danych z usługi Amazon S3 do ADLS Gen2](solution-template-migration-s3-azure.md) szablonów rozwiązań jako ogólnego przykładu.

  - Sprawdź, czy funkcja ADF zgłasza jakikolwiek błąd ograniczania w źródle lub czy magazyn danych jest w stanie wyższym niż użycie. Jeśli tak, Zmniejsz obciążenie magazynu danych lub spróbuj skontaktować się z administratorem magazynu danych w celu zwiększenia limitu ograniczania przepustowości lub dostępnego zasobu.

- **"Transfer-odczyt ze źródła" długotrwały czas pracy**: 

  - Sprawdź, czy własna maszyna IR ma małe opóźnienia łączące się z magazynem danych źródłowych. Jeśli Twoje źródło znajduje się na platformie Azure, możesz użyć [tego narzędzia](http://www.azurespeed.com/Azure/Latency) , aby sprawdzić opóźnienia z samodzielnej maszyny podczerwieni z regionami świadczenia usługi Azure, im mniej lepiej.

  - Sprawdź, czy własna maszyna IR ma wystarczającą przepustowość przychodzącą, aby efektywnie odczytywać i przesyłać dane. Jeśli źródłowy magazyn danych znajduje się na platformie Azure, możesz użyć [tego narzędzia](https://www.azurespeed.com/Azure/Download) , aby sprawdzić szybkość pobierania.

  - Zapoznaj się z trendem użycia procesora CPU i pamięci przez środowisko IR w Azure Portal-> stronie Przegląd > usługi Data Factory. Należy rozważyć [skalowanie w górę/](create-self-hosted-integration-runtime.md#high-availability-and-scalability) w poziomie środowiska IR, jeśli użycie procesora CPU jest wysokie lub dostępna pamięć jest niska.

  - Należy zastosować najlepsze rozwiązanie do ładowania danych specyficznych dla łącznika, jeśli ma zastosowanie. Na przykład:

    - Podczas kopiowania danych z programu [Oracle](connector-oracle.md#oracle-as-source), [Netezza](connector-netezza.md#netezza-as-source), [Teradata](connector-teradata.md#teradata-as-source), [SAP HANA](connector-sap-hana.md#sap-hana-as-source), [tabeli SAP](connector-sap-table.md#sap-table-as-source)i [oprogramowania SAP Open Hub](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source), Włącz opcje partycji danych, aby równolegle kopiować dane.

    - Podczas kopiowania danych z systemu plików [HDFS](connector-hdfs.md)Skonfiguruj do korzystania z pomocą distcp.

    - Podczas kopiowania danych z usługi [Amazon RedShift](connector-amazon-redshift.md)Skonfiguruj do korzystania z usługi RedShift Unload.

  - Sprawdź, czy funkcja ADF zgłasza jakikolwiek błąd ograniczania w źródle lub czy magazyn danych jest w wysokim obciążeniu. Jeśli tak, Zmniejsz obciążenie magazynu danych lub spróbuj skontaktować się z administratorem magazynu danych w celu zwiększenia limitu ograniczania przepustowości lub dostępnego zasobu.

  - Sprawdź Źródło kopii i wzorzec ujścia: 

    - W przypadku kopiowania danych z magazynów danych z włączoną opcją partycji należy rozważyć stopniowe Dostosowywanie [kopii równoległych](copy-activity-performance-features.md), co oznacza, że zbyt wiele kopii równoległych może nawet obniżyć wydajność.

    - W przeciwnym razie należy rozważyć rozdzielenie pojedynczych dużych ilości danych na kilka mniejszych zestawów danych i umożliwienie wykonywania tych zadań kopiowania współbieżnie każdej części danych. Można to zrobić za pomocą metody Lookup/GetMetadata + ForEach + Copy. Zapoznaj się z [kopiowaniem plików z wielu kontenerów](solution-template-copy-files-multiple-containers.md), [Migrowanie danych z usługi Amazon S3 do ADLS Gen2](solution-template-migration-s3-azure.md)lub [zbiorcze kopiowanie za pomocą szablonów rozwiązań tabeli formantów](solution-template-bulk-copy-with-control-table.md) .

- **"Przesyłanie zapisu do ujścia" ma długi czas pracy**:

  - Należy zastosować najlepsze rozwiązanie do ładowania danych specyficznych dla łącznika, jeśli ma zastosowanie. Na przykład podczas kopiowania danych do [usługi Azure Synapse Analytics](connector-azure-sql-data-warehouse.md)Użyj instrukcji Base lub Copy. 

  - Sprawdź, czy własna maszyna IR ma małe opóźnienia łączące się z magazynem danych ujścia. Jeśli ujścia znajduje się na platformie Azure, możesz użyć [tego narzędzia](http://www.azurespeed.com/Azure/Latency) , aby sprawdzić opóźnienia z samodzielnej maszyny IR w regionie świadczenia usługi Azure, im mniej lepiej.

  - Sprawdź, czy maszyna z własnymi środowiskami IR ma wystarczającą przepustowość wychodzącą, aby efektywnie przesyłać i zapisywać dane. Jeśli magazyn danych ujścia znajduje się na platformie Azure, możesz użyć [tego narzędzia](https://www.azurespeed.com/Azure/UploadLargeFile) , aby sprawdzić szybkość przekazywania.

  - Sprawdź, czy w Azure Portal na stronie Omówienie usługi Fabryka danych > — >, czy jest dostępna wartość trend użycia procesora i pamięci przez środowisko IR. Należy rozważyć [skalowanie w górę/](create-self-hosted-integration-runtime.md#high-availability-and-scalability) w poziomie środowiska IR, jeśli użycie procesora CPU jest wysokie lub dostępna pamięć jest niska.

  - Sprawdź, czy funkcja ADF zgłasza jakikolwiek błąd ograniczania przepustowości w ujściach lub czy magazyn danych jest w wysokim obciążeniu. Jeśli tak, Zmniejsz obciążenie magazynu danych lub spróbuj skontaktować się z administratorem magazynu danych w celu zwiększenia limitu ograniczania przepustowości lub dostępnego zasobu.

  - Rozważ stopniowe dopasowanie [kopii równoległych](copy-activity-performance-features.md), co oznacza, że zbyt wiele kopii równoległych może nawet obniżyć wydajność.

## <a name="other-references"></a>Inne odwołania

Poniżej przedstawiono informacje dotyczące monitorowania wydajności i dostrajania dla niektórych obsługiwanych magazynów danych:

* Azure Blob Storage: [elementy docelowe skalowalności i wydajności dla magazynu obiektów BLOB](../storage/blobs/scalability-targets.md) i [wydajności i skalowalności dla usługi BLOB Storage](../storage/blobs/storage-performance-checklist.md).
* Azure Table Storage: [elementy docelowe skalowalności i wydajności dla magazynu tabel](../storage/tables/scalability-targets.md) oraz [listę kontrolną wydajności i skalowalności w magazynie tabel](../storage/tables/storage-performance-checklist.md).
* Azure SQL Database: można [monitorować wydajność](../azure-sql/database/monitor-tune-overview.md) i sprawdzać wartość procentową jednostki transakcji bazy danych (DTU).
* Azure Synapse Analytics: jej możliwości są mierzone w jednostkach magazynu danych (jednostek dwu). Zobacz [zarządzanie mocą obliczeniową w usłudze Azure Synapse Analytics (omówienie)](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md).
* Azure Cosmos DB: [poziomy wydajności w Azure Cosmos DB](../cosmos-db/performance-levels.md).
* SQL Server: [monitorowanie i dostrajanie wydajności](/sql/relational-databases/performance/monitor-and-tune-for-performance).
* Lokalny serwer plików: [dostrajanie wydajności dla serwerów plików](/previous-versions//dn567661(v=vs.85)).

## <a name="next-steps"></a>Następne kroki
Zapoznaj się z innymi artykułami dotyczącymi działania kopiowania:

- [Przegląd działania kopiowania](copy-activity-overview.md)
- [Przewodnik dotyczący wydajności i skalowalności działania kopiowania](copy-activity-performance.md)
- [Funkcje optymalizacji wydajności działania kopiowania](copy-activity-performance-features.md)
- [Używanie Azure Data Factory do migrowania danych z usługi Data Lake lub magazynu danych na platformę Azure](data-migration-guidance-overview.md)
- [Migrowanie danych z usługi Amazon S3 do usługi Azure Storage](data-migration-guidance-s3-azure-storage.md)