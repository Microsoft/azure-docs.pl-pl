---
title: Integracja Azure Data Explorer na celu długoterminowe przechowywanie dzienników | Microsoft Docs
description: Wysyłaj Azure Sentinel do Azure Data Explorer w celu długoterminowego przechowywania, aby zmniejszyć koszty magazynowania danych.
services: sentinel
documentationcenter: na
author: batamig
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/21/2021
ms.author: bagol
ms.openlocfilehash: c7d9ef58d4bb15afe59c6734ce887c2cc3c07c26
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107836228"
---
# <a name="integrate-azure-data-explorer-for-long-term-log-retention"></a>Integracja Azure Data Explorer na celu długoterminowe przechowywanie dzienników

<!--Info not included:>

Script - can't xref out to a private github repo from docs
-->

Domyślnie dzienniki pozyskane do usługi Azure Sentinel są przechowywane w Azure Monitor Log Analytics. W tym artykule wyjaśniono, jak zmniejszyć koszty przechowywania w Azure Sentinel, wysyłając je do Azure Data Explorer (ADX) w celu długoterminowego przechowywania.

Przechowywanie dzienników w adx zmniejsza koszty przy zachowaniu możliwości wykonywania zapytań o dane i jest szczególnie przydatne w miarę wzrostu ilości danych. Na przykład dane zabezpieczeń mogą z czasem tracić wartość, jednak może być konieczne zachowanie dzienników dla wymagań prawnych lub okresowe badanie starszych danych.

## <a name="about-azure-data-explorer"></a>Informacje o usłudze Azure Data Explorer

ADX to platforma do analizy danych big data, która jest wysoce zoptymalizowana pod kątem analizy dzienników i danych. Ponieważ adX używa języka Kusto Query Language (KQL) jako języka zapytań, jest to dobra alternatywa dla magazynu Azure Sentinel danych. Użycie usługi ADX do przechowywania danych umożliwia uruchamianie zapytań międzyplatformowych i wizualizowanie danych zarówno w usługach ADX, jak i Azure Sentinel.

Aby uzyskać więcej informacji, zobacz dokumentację usługi [ADX](/azure/data-explorer/) i [blog](https://azure.microsoft.com/en-us/blog/tag/azure-data-explorer/).

### <a name="when-to-integrate-with-adx"></a>Kiedy należy zintegrować z usługą ADX

Azure Sentinel oferuje pełne funkcje SIEM i SOAR, szybkie wdrażanie i konfigurację, a także zaawansowane, wbudowane funkcje zabezpieczeń dla zespołów SOC. Jednak wartość przechowywania danych zabezpieczeń w usługach Azure Sentinel może spaść po kilku miesiącach, gdy użytkownicy SOC nie będą musieli uzyskać do nich dostępu tak często, jak mają dostęp do nowszej danych.

Jeśli dostęp do określonych tabel jest potrzebny od czasu do czasu, na przykład w przypadku okresowych badań lub inspekcji, możesz wziąć pod uwagę, że zachowywanie danych w programie Azure Sentinel nie jest już opłacalne. W tym momencie zalecamy przechowywanie danych w adx, co jest tańszą usługą, ale nadal umożliwia eksplorowanie przy użyciu tych samych zapytań KQL, które są uruchamiane w Azure Sentinel.

Dostęp do danych w usługach ADX można uzyskać bezpośrednio z witryny Azure Sentinel użyciu funkcji serwera proxy ADX usługi [Log Analytics.](//azure/azure-monitor/logs/azure-monitor-data-explorer-proxy) W tym celu użyj zapytań między klastrami w wyszukiwaniu w dzienniku lub skoroszytach. 

> [!IMPORTANT]
> Podstawowe możliwości rozwiązania SIEM, w tym reguły analityczne, analiza UEBA i wykres badania, nie obsługują danych przechowywanych w u usługach ADX.
>

> [!NOTE]
> Integracja z usługą ADX umożliwia również kontrolę i szczegółowość danych. Aby uzyskać więcej informacji, zobacz [Zagadnienia dotyczące projektowania.](#design-considerations)
> 
## <a name="send-data-directly-to-azure-sentinel-and-adx-in-parallel"></a>Równoległe wysyłanie danych bezpośrednio do Azure Sentinel i ADX

Możesz zachować wszystkie dane  z wartością zabezpieczeń w programie Azure Sentinel do użycia w wykrywaniu, badaniach zdarzeń, wykrywaniu zagrożeń, ueba itp. Przechowywanie tych danych w Azure Sentinel przynosi korzyści głównie użytkownikom usługi Security Operations Center (SOC), gdzie zwykle wystarczy 3–12 miesięcy przechowywania.

Można również skonfigurować wszystkie dane, niezależnie od ich wartości *zabezpieczeń,* do wysłania do usługi ADX w tym samym czasie, gdzie można przechowywać je przez dłuższy czas. Podczas wysyłania danych do Azure Sentinel i ADX w tym samym czasie powoduje pewne duplikowanie, oszczędności kosztów mogą być znaczące, ponieważ zmniejsza się koszty przechowywania w Azure Sentinel.

> [!TIP]
> Ta opcja umożliwia również skorelowanie danych rozrzuconych między magazynami danych, na przykład w celu wzbogacenia danych zabezpieczeń przechowywanych w programie Azure Sentinel z danymi operacyjnymi lub długoterminowymi przechowywanymi w usługach ADX. Aby uzyskać więcej informacji, zobacz Wykonywanie zapytań [między zasobami Azure Data Explorer użyciu Azure Monitor](/azure/azure-monitor/logs/azure-monitor-data-explorer-proxy).
>

Na poniższej ilustracji przedstawiono, jak można zachować wszystkie dane w adx, wysyłając tylko dane zabezpieczeń do usługi Azure Sentinel do codziennego użytku.

:::image type="content" source="media/store-logs-in-adx/store-data-in-sentinel-and-adx-in-parallel.png" alt-text="Przechowuj dane w usługach ADX i Azure Sentinel równolegle.":::

Aby uzyskać więcej informacji na temat implementowania tej opcji architektury, zobacz [Azure Data Explorer monitorowania](/azure/architecture/solution-ideas/articles/monitor-azure-data-explorer).

## <a name="export-data-from-log-analytics-into-adx"></a>Eksportowanie danych z usługi Log Analytics do usługi ADX

Zamiast wysyłać dane bezpośrednio do usługi ADX, możesz wyeksportować dane z usługi Log Analytics do usługi ADX za pośrednictwem centrum zdarzeń ADX lub Azure Data Factory.

### <a name="data-export-architecture"></a>Architektura eksportu danych

Na poniższej ilustracji przedstawiono przykładowy przepływ wyeksportowanych danych za pośrednictwem Azure Monitor pozyskiwania danych. Dane są domyślnie kierowane do usługi Log Analytics, ale można je również skonfigurować do eksportowania na konto usługi Azure Storage lub do centrum zdarzeń.

:::image type="content" source="media/store-logs-in-adx/export-data-from-azure-monitor.png" alt-text="Eksportowanie danych z Azure Monitor — architektura.":::

Podczas konfigurowania reguł eksportu danych wybierz typy dzienników, które chcesz wyeksportować. Po skonfigurowaniu nowe dane docierające do punktu końcowego pozyskiwania usługi Log Analytics i kierowane do obszaru roboczego dla wybranych tabel są eksportowane do konta magazynu lub centrum zdarzeń.

Podczas konfigurowania danych do eksportu należy wziąć pod uwagę następujące kwestie:

|Kwestie do rozważenia  | Szczegóły |
|---------|---------|
|**Zakres wyeksportowanych danych**     |  Po skonfigurowaniu eksportu dla określonej tabeli wszystkie dane wysyłane do tej tabeli są eksportowane bez żadnych wyjątków. Wyeksportowany odfiltrowany podzbiór danych lub ograniczenie eksportu do określonych zdarzeń nie jest obsługiwane.       |
|**Wymagania dotyczące lokalizacji**     |   Zarówno obszar roboczy Azure Monitor/Azure Sentinel, jak i lokalizacja docelowa (konto usługi Azure Storage lub centrum zdarzeń) muszą znajdować się w tym samym regionie geograficznym.      |
|**Obsługiwane tabele**     | Nie wszystkie tabele są obsługiwane w przypadku eksportowania, na przykład niestandardowe tabele dzienników, które nie są obsługiwane. <br><br>Aby uzyskać więcej informacji, zobacz Eksportowanie danych [obszaru roboczego usługi Log Analytics Azure Monitor](/azure/azure-monitor/logs/logs-data-export) i listę [obsługiwanych tabel.](/azure/azure-monitor/logs/logs-data-export#supported-tables)         |
|     |         |

### <a name="data-export-methods-and-procedures"></a>Metody i procedury eksportowania danych

Użyj jednej z następujących procedur, aby wyeksportować dane z usługi Azure Sentinel adx:

- **Za pośrednictwem centrum zdarzeń ADX**. Wyeksportuj dane z usługi Log Analytics do centrum zdarzeń, w którym można je pozysać do usługi ADX. Ta metoda przechowuje niektóre dane (pierwsze X miesięcy) w obu Azure Sentinel i ADX.

- **Za pomocą usługi Azure Storage i Azure Data Factory**. Wyeksportuj dane z usługi Log Analytics Azure Blob Storage do usługi Azure Data Factory, aby uruchomić okresowe zadanie kopiowania w celu dalszego wyeksportowania danych do usługi ADX. Ta metoda umożliwia kopiowanie danych z usługi Azure Data Factory tylko wtedy, gdy zbliża się ona do limitu przechowywania w usługach Azure Sentinel/Log Analytics, unikając duplikacji.

### <a name="adx-event-hub"></a>[Centrum zdarzeń ADX](#tab/adx-event-hub)

W tej sekcji opisano sposób eksportowania Azure Sentinel z usługi Log Analytics do centrum zdarzeń, w którym można je pozysować do usługi ADX. Podobnie jak w przypadku równoległego wysyłania danych bezpośrednio do usług [Azure Sentinel i ADX,](#send-data-directly-to-azure-sentinel-and-adx-in-parallel)ta metoda obejmuje pewne duplikowanie danych, ponieważ dane są przesyłane strumieniowo do usługi ADX, gdy docierają do usługi Log Analytics.

Na poniższej ilustracji przedstawiono przykładowy przepływ wyeksportowanych danych do centrum zdarzeń, z którego są pozysane do usługi ADX.

:::image type="content" source="media/store-logs-in-adx/ingest-data-to-adx-via-event-hub.png" alt-text="Eksportowanie danych do usługi ADX za pośrednictwem centrum zdarzeń ADX.":::

Architektura pokazana na poprzedniej ilustracji zapewnia pełne środowisko rozwiązania Azure Sentinel SIEM, w tym zarządzanie zdarzeniami, badania wizualne, wyszukiwanie zagrożeń, zaawansowane wizualizacje, ueba i inne, dla danych, do których należy często uzyskiwać dostęp, co *X* miesięcy. W tym samym czasie ta architektura umożliwia również wykonywanie zapytań dotyczących danych długoterminowych przez uzyskiwanie do nich dostępu bezpośrednio w u usługach ADX lub za pośrednictwem usługi Azure Sentinel dzięki funkcji serwera proxy ADX. Zapytania dotyczące długoterminowego magazynu danych w usługach ADX można portować bez żadnych zmian Azure Sentinel do ADX.

> [!TIP]
> Aby uzyskać więcej informacji na temat tej procedury, zobacz [Samouczek:](/azure/data-explorer/ingest-data-no-code)pozysowanie i wykonywanie zapytań dotyczących danych monitorowania w Azure Data Explorer .
>

**Aby wyeksportować dane do usługi ADX za pośrednictwem centrum zdarzeń:**

1. **Skonfiguruj eksport danych usługi Log Analytics do centrum zdarzeń**. Aby uzyskać więcej informacji, zobacz [Eksportowanie danych obszaru roboczego usługi Log Analytics w Azure Monitor](/azure/azure-monitor/platform/logs-data-export).

1. **Utwórz klaster ADX i bazę danych**. Aby uzyskać więcej informacji, zobacz:

    - [Tworzenie klastra Azure Data Explorer bazy danych](/azure/data-explorer/create-cluster-database-portal)
    - [Wybierz poprawną jednostkę SKU obliczeniową dla Azure Data Explorer klastra](/azure/data-explorer/manage-cluster-choose-sku)

1. **Utwórz tabele docelowe.** Dane pierwotne są najpierw pozytywowane do tabeli pośredniej, w której dane pierwotne są przechowywane, manipulowane i rozszerzane.

    Zasady aktualizacji, które są podobne do funkcji zastosowanej do wszystkich nowych danych, są używane do pozysowania rozszerzonych danych do tabeli końcowej, która ma taki sam schemat jak oryginalna tabela w Azure Sentinel.

    Ustaw okres przechowywania w pierwotnej tabeli **na 0** dni. Dane są przechowywane tylko w prawidłowo sformatowanych tabelach i usuwane w nieprzetworzonych tabelach zaraz po ich przekształcone.

    Aby uzyskać więcej informacji, zobacz [Ingest and query monitoring data in Azure Data Explorer (Pozysuj dane monitorowania i odpytuj je w Azure Data Explorer](/azure/data-explorer/ingest-data-no-code?tabs=diagnostic-metrics)).

1. <a name="mapping"></a>**Utwórz mapowanie tabeli**. Zamapuj tabele JSON, aby zdefiniować sposób, w jaki rekordy trafiają do tabeli nieprzetworzonych zdarzeń, gdy pochodzą z centrum zdarzeń. Aby uzyskać więcej informacji, zobacz Create the update policy for metric and log data (Tworzenie zasad [aktualizacji dla danych metryk i dzienników).](/azure/data-explorer/ingest-data-no-code?tabs=diagnostic-metrics)

1. **Utwórz zasady aktualizacji i dołącz je do tabeli rekordów pierwotnych**. W tym kroku utworzysz funkcję o nazwie zasady aktualizacji i dołączysz ją do tabeli docelowej, aby dane są przekształcane w czasie pozyskiwania.

    > [!NOTE]
    > Ten krok jest wymagany tylko wtedy, gdy chcesz mieć tabele danych w adx o tym samym schemacie i formacie co w Azure Sentinel.
    >

    Aby uzyskać więcej informacji, zobacz [Łączenie centrum zdarzeń z Azure Data Explorer](/azure/data-explorer/ingest-data-no-code?tabs=activity-logs).

1. **Utwórz połączenie danych między centrum zdarzeń i tabelą danych pierwotnych w adx**. Skonfiguruj usługę ADX ze szczegółami eksportowania danych do centrum zdarzeń.

    Skorzystaj z instrukcji podanych [w Azure Data Explorer i](/azure/data-explorer/ingest-data-no-code?tabs=activity-logs) określ następujące szczegóły:

    - **Element docelowy**. Określ określoną tabelę przy użyciu danych pierwotnych.
    - **Formatuj**. Określ `.json` jako format tabeli.
    - **Mapowanie do zastosowania.** Określ tabelę mapowania utworzoną w [kroku 4](#mapping) powyżej.


1. **Zmodyfikuj przechowywanie dla tabeli docelowej**. Domyślne [zasady Azure Data Explorer przechowywania](/azure/data-explorer/kusto/management/retentionpolicy) danych mogą być znacznie dłuższe niż jest to konieczne.

    Użyj następującego polecenia, aby zaktualizować zasady przechowywania do jednego roku:

    ```kusto
    .alter-merge table <tableName> policy retention softdelete = 365d recoverability = disabled
    ```
### <a name="azure-storage--azure-data-factory"></a>[Azure Storage / Azure Data Factory](#tab/azure-storage-azure-data-factory)

W tej sekcji opisano sposób eksportowania Azure Sentinel z usługi Log Analytics do usługi Azure Storage, w Azure Data Factory można uruchamiać zwykłe zadanie eksportowania danych do usługi ADX.

Korzystanie z usługi Azure Storage Azure Data Factory umożliwia kopiowanie danych z usługi Azure Storage tylko wtedy, gdy jest ona bliska limitu przechowywania w usłudze Azure Sentinel/Log Analytics. Nie ma duplikowania danych,  a adx jest używany tylko do uzyskiwania dostępu do danych, które są starsze niż limit przechowywania w Azure Sentinel.

> [!TIP]
> Chociaż architektura korzystania z usługi Azure Storage i Azure Data Factory dla starszych danych jest bardziej złożona, ta metoda może ogólnie oferować większe oszczędności.
>
Na poniższej ilustracji przedstawiono przykładowy przepływ wyeksportowanych danych do usługi Azure Storage, z Azure Data Factory uruchamia regularne zadanie dalszego eksportowania ich do usługi ADX.

:::image type="content" source="media/store-logs-in-adx/ingest-data-to-adx-via-azure-storage-azure-data-factory.png" alt-text="Eksportowanie danych do usługi ADX za pośrednictwem usługi Azure Storage i Azure Data Factory.":::

**Aby wyeksportować dane do usługi ADX za pośrednictwem usługi Azure Storage i Azure Data Factory:**

1. **Skonfiguruj eksport danych usługi Log Analytics do centrum zdarzeń**. Aby uzyskać więcej informacji, zobacz [Eksportowanie danych obszaru roboczego usługi Log Analytics w Azure Monitor](/azure/azure-monitor/logs/logs-data-export?tabs=portal#enable-data-export).

1. **Utwórz klaster ADX i bazę danych**. Aby uzyskać więcej informacji, zobacz:

    - [Tworzenie klastra Azure Data Explorer bazy danych](/azure/data-explorer/create-cluster-database-portal)
    - [Wybierz poprawną jednostkę SKU obliczeniową dla Azure Data Explorer klastra](/azure/data-explorer/manage-cluster-choose-sku)

1. **Utwórz tabele docelowe.** Dane pierwotne są najpierw pozytywowane do tabeli pośredniej, w której dane pierwotne są przechowywane, manipulowane i rozszerzane.

    Zasady aktualizacji, które są podobne do funkcji zastosowanej do wszystkich nowych danych, są używane do pozysowania rozszerzonych danych do tabeli końcowej, która ma taki sam schemat jak oryginalna tabela w Azure Sentinel.

    Ustaw okres przechowywania w pierwotnej tabeli **na 0** dni. Dane są przechowywane tylko w prawidłowo sformatowanych tabelach i usuwane w nieprzetworzonych tabelach zaraz po przekształcone.

    Aby uzyskać więcej informacji, zobacz [Ingest and query monitoring data in Azure Data Explorer (Pozysuj dane monitorowania i odpytuj je w Azure Data Explorer](/azure/data-explorer/ingest-data-no-code?tabs=diagnostic-metrics)).

1. <a name="mapping"></a>**Utwórz mapowanie tabeli**. Zamapuj tabele JSON, aby zdefiniować sposób, w jaki rekordy trafiają do tabeli nieprzetworzonych zdarzeń, gdy pochodzą z centrum zdarzeń. Aby uzyskać więcej informacji, zobacz Create the update policy for metric and log data (Tworzenie zasad [aktualizacji dla danych metryk i dzienników).](/azure/data-explorer/ingest-data-no-code?tabs=diagnostic-metrics)

1. **Utwórz zasady aktualizacji i dołącz je do tabeli rekordów pierwotnych**. W tym kroku utworzysz funkcję o nazwie zasady aktualizacji i dołączysz ją do tabeli docelowej, aby dane są przekształcane w czasie pozyskiwania.

    > [!NOTE]
    > Ten krok jest wymagany tylko wtedy, gdy chcesz mieć tabele danych w adx o tym samym schemacie i formacie co w Azure Sentinel.
    >

    Aby uzyskać więcej informacji, zobacz [Łączenie centrum zdarzeń z Azure Data Explorer](/azure/data-explorer/ingest-data-no-code?tabs=activity-logs).

1. **Utwórz połączenie danych między centrum zdarzeń i tabelą danych pierwotnych w adx**. Skonfiguruj usługę ADX ze szczegółami eksportowania danych do centrum zdarzeń.

    Skorzystaj z instrukcji podanych [w Azure Data Explorer i](/azure/data-explorer/ingest-data-no-code?tabs=activity-logs) określ następujące szczegóły:

    - **Element docelowy**. Określ określoną tabelę przy użyciu danych pierwotnych.
    - **Formatuj**. Określ `.json` jako format tabeli.
    - **Mapowanie do zastosowania.** Określ tabelę mapowania utworzoną w [kroku 4](#mapping) powyżej.

1. **Skonfiguruj potok Azure Data Factory potoku:**

    - Utwórz połączone usługi dla usług Azure Storage i Azure Data Explorer. Aby uzyskać więcej informacji, zobacz:

        - [Kopiowanie i przekształcanie danych w usłudze Azure Blob Storage przy użyciu Azure Data Factory](/azure/data-factory/connector-azure-blob-storage)
        - [Kopiowanie danych do lub z Azure Data Explorer przy użyciu Azure Data Factory](/azure/data-factory/connector-azure-data-explorer).

    - Tworzenie zestawu danych z usługi Azure Storage. Aby uzyskać więcej informacji, zobacz [Zestawy danych w Azure Data Factory](/azure/data-factory/concepts-datasets-linked-services).

    - Utwórz potok danych z operacją kopiowania na podstawie właściwości **LastModifiedDate.**

        Aby uzyskać więcej informacji, zobacz [Copy new and changed files by **LastModifiedDate** with Azure Data Factory](/azure/data-factory/solution-template-copy-new-files-lastmodifieddate).

---

## <a name="design-considerations"></a>Zagadnienia dotyczące projektowania

Podczas przechowywania danych Azure Sentinel adx należy wziąć pod uwagę następujące elementy:

|Kwestie do rozważenia  |Opis  |
|---------|---------|
|**Rozmiar klastra i jego sku**     | Starannie zaplanuj liczbę węzłów i liczbę węzłów maszyny wirtualnej w klastrze. Te czynniki określają ilość mocy obliczeniowej i rozmiar gorącej pamięci podręcznej (SSD i pamięć). Im większa pamięć podręczna, tym więcej danych będzie można odpytować przy większej wydajności. <br><br>Zachęcamy do odwiedzenia kalkulatora rozmiarów [adx,](https://dataexplorer.azure.com/AzureDataExplorerCostEstimator.html)w którym można korzystać z różnych konfiguracji i zobaczyć wynikowy koszt. <br><br>Usługa ADX ma również funkcję automatycznego skalowania, która podejmuje inteligentne decyzje dotyczące dodawania/usuwania węzłów w zależności od obciążenia klastra. Aby uzyskać więcej informacji, zobacz [Manage cluster horizontal scaling (scale out) in Azure Data Explorer to accommodate changing demand (Zarządzanie](/azure/data-explorer/manage-cluster-horizontal-scaling)skalowaniem klastra w poziomie (skalowaniem w poziomie) w Azure Data Explorer w celu dostosowania do zmieniających się potrzeb.      |
|**Gorąca/zimna pamięć podręczna**     | AdX zapewnia kontrolę nad tabelami danych, które znajdują się w gorącej pamięci podręcznej, i szybciej zwraca wyniki. Jeśli w klastrze ADX masz duże ilości danych, możesz chcieć rozbić tabele według miesięcy, aby uzyskać większy poziom szczegółowości danych, które są obecne w gorącej pamięci podręcznej. <br><br>Aby uzyskać więcej informacji, zobacz [Zasady pamięci podręcznej (gorąca i zimna pamięć podręczna)](/azure/data-explorer/kusto/management/cachepolicy)       |
|**Przechowywanie**     |   W adx można skonfigurować, kiedy dane są usuwane z bazy danych lub poszczególnych tabel, co jest również ważną częścią ograniczania kosztów magazynowania. <br><br> Aby uzyskać więcej informacji, zobacz [Zasady przechowywania](/azure/data-explorer/kusto/management/retentionpolicy).       |
|**Bezpieczeństwo**     |  Kilka ustawień adx może pomóc w ochronie danych, takich jak zarządzanie tożsamościami, szyfrowanie i tak dalej. W szczególności w przypadku kontroli dostępu opartej na rolach (RBAC) można skonfigurować usługę ADX w celu ograniczenia dostępu do baz danych, tabel, a nawet wierszy w tabeli. Aby uzyskać więcej informacji, zobacz [Zabezpieczenia w Azure Data Explorer](/azure/data-explorer/security) i Zabezpieczenia na poziomie [wiersza.](/azure/data-explorer/kusto/management/rowlevelsecuritypolicy)|
|**Udostępnianie danych**     |   AdX umożliwia użytkownikom zapewnianie dostępu do danych innym podmiotom, takim jak partnerzy lub dostawcy, a nawet kupowanie danych od innych podmiotów. Aby uzyskać więcej informacji, zobacz [Używanie Azure Data Share do udostępniania danych Azure Data Explorer](/azure/data-explorer/data-share).      |
| **Inne składniki kosztów** | Weź pod uwagę inne składniki kosztów dla następujących metod: <br><br>**Eksportowanie danych za pośrednictwem centrum zdarzeń ADX:** <br>— Koszty eksportu danych usługi Log Analytics z opłatami za wyeksportowane kb/s. <br>— Koszty centrum zdarzeń, naliczane według jednostki przepływności.  <br><br>**Eksportowanie danych za pośrednictwem usługi Azure Storage i Azure Data Factory:** <br>— Eksportowanie danych usługi Log Analytics z opłatami za wyeksportowane kb/s. <br>— Usługa Azure Storage, naliczana według przechowywanych GB. <br>— Azure Data Factory, opłata naliczana za kopię uruchomionych działań.
|     |         |

## <a name="next-steps"></a>Następne kroki

Bez względu na to, gdzie przechowujesz dane, kontynuuj badania i odszukaj je przy użyciu Azure Sentinel.

Aby uzyskać więcej informacji, zobacz:

- [Samouczek: badanie zdarzeń za pomocą Azure Sentinel](tutorial-investigate-cases.md)
- [Poluj na zagrożenia za pomocą Azure Sentinel](hunting.md)
