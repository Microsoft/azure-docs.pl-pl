---
title: Metryki Azure Storage Analytics (klasyczne)
description: Dowiedz się, jak używać metryk analityka magazynu w usłudze Azure Storage. Dowiedz się więcej o metrykach transakcji i pojemności, sposobie przechowywania metryk, włączaniu metryk i nie tylko.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 01/29/2021
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.custom: monitoring, devx-track-csharp
ms.openlocfilehash: d900ffa4481ba2b6deb21a8325f3f8def8084f84
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101714735"
---
# <a name="azure-storage-analytics-metrics-classic"></a>Metryki Azure Storage Analytics (klasyczne)

W dniu **31 sierpnia 2023** metryki analityka magazynu, nazywane również *metrykami klasycznymi* , zostaną wycofane. Więcej informacji znajdziesz w [oficjalnym ogłoszeniu](https://azure.microsoft.com/updates/azure-storage-classic-metrics-will-be-retired-on-31-august-2023/). Jeśli używasz metryk klasycznych, pamiętaj, aby przejść na metryki w usłudze Azure Monitor przed tą datą. Ten artykuł ułatwi Ci to przejście. 

Usługa Azure Storage korzysta z rozwiązania analityka magazynu do przechowywania metryk, które obejmują zagregowane statystyki transakcji oraz dane pojemności dotyczące żądań do usługi magazynu. Transakcje są raportowane na poziomie operacji interfejsu API i na poziomie usługi magazynu. Wydajność jest raportowana na poziomie usługi magazynu. Dane metryk mogą służyć do:
- Analizuj użycie usługi magazynu.
- Diagnozuj problemy z żądaniami skierowanymi do usługi Storage.
- Zwiększenie wydajności aplikacji korzystających z usługi.

 Metryki analityka magazynu są domyślnie włączone dla nowych kont magazynu. Metryki można skonfigurować w [Azure Portal](https://portal.azure.com/)przy użyciu programu PowerShell lub interfejsu wiersza polecenia platformy Azure. Aby uzyskać wskazówki krok po kroku, zobacz temat [Włączanie metryk analitycznych usługi Azure Storage i zarządzanie nimi (klasyczne)](./manage-storage-analytics-logs.md). Możesz również włączyć analityka magazynu programowo za pośrednictwem interfejsu API REST lub biblioteki klienta. Użyj operacji ustawiania właściwości usługi, aby włączyć analityka magazynu dla każdej usługi.  

> [!NOTE]
> Metryki analityka magazynu są dostępne dla usługi Azure Blob Storage, Azure queue storage, Azure Table Storage i Azure Files.
> Metryki analityka magazynu są teraz metrykami klasycznymi. Zalecamy używanie [metryk magazynu w Azure monitor](../blobs/monitor-blob-storage.md) zamiast metryk analityka magazynu.

## <a name="transaction-metrics"></a>Metryki transakcji  
 Niezawodny zestaw danych jest rejestrowany w co godzinę lub w minutę interwałów dla każdej usługi magazynu i żądana operacja interfejsu API, która obejmuje wartości procentowe i wychodzące, dostępność, błędy i przydzielone żądania według kategorii. Aby uzyskać pełną listę szczegółów transakcji, zobacz [analityka magazynu metryk tabeli schematu](/rest/api/storageservices/storage-analytics-metrics-table-schema).  

 Dane transakcji są rejestrowane na poziomie usługi i na poziomie operacji interfejsu API. Na poziomie usługi statystyka, która podsumowuje wszystkie żądane operacje interfejsu API, jest zapisywana w jednostce tabeli co godzinę, nawet jeśli żadne żądania nie zostały wysłane do usługi. Na poziomie operacji interfejsu API statystyki są zapisywane tylko w jednostce, jeśli zażądano tej operacji w danej godzinie.  

 Na przykład w przypadku wykonywania operacji **GetBlob** na usłudze blob usługa analityka magazynu metryki rejestruje żądanie i dołącza je do zagregowanych danych dla usługi BLOB Service i operacji **GetBlob** . Jeśli w ciągu godziny nie zażądano żadnej operacji **GetBlob** , jednostka nie jest zapisywana do *$MetricsTransactionsBlob* dla tej operacji.  

 Metryki transakcji są rejestrowane dla żądań użytkowników i żądań wykonywanych przez analityka magazynu samego siebie. Na przykład zarejestrowano żądania analityka magazynu do zapisu dzienników i jednostek tabel.

## <a name="capacity-metrics"></a>Metryki pojemności  

> [!NOTE]
>  Obecnie metryki pojemności są dostępne tylko dla usługi BLOB Service.

 Dane pojemności są rejestrowane codziennie dla usługi BLOB konta magazynu, a dwie jednostki tabeli są zapisywane. Jedna jednostka zawiera statystyki dotyczące danych użytkownika, a drugi zawiera dane statystyczne dotyczące `$logs` kontenera obiektów BLOB używanych przez analityka magazynu. Tabela *$MetricsCapacityBlob* zawiera następujące statystyki:  

- **Pojemność**: ilość miejsca do magazynowania używanego przez usługę BLOB konta magazynu w bajtach.  
- **ContainerCount**: liczba kontenerów obiektów BLOB w usłudze obiektów BLOB konta magazynu.  
- **ObjectCount**: Liczba przekazanych i nieprzydzielonych bloków lub stronicowych obiektów BLOB w usłudze obiektów BLOB konta magazynu.  

  Aby uzyskać więcej informacji na temat metryk pojemności, zobacz [analityka magazynu metryk tabeli schematu](/rest/api/storageservices/storage-analytics-metrics-table-schema).  

## <a name="how-metrics-are-stored"></a>Jak są przechowywane metryki  

 Wszystkie dane metryk dla każdej usługi magazynu są przechowywane w trzech tabelach zarezerwowanych dla tej usługi. Jedna tabela jest dla informacji o transakcji. jedna tabela jest dla informacji o jednej z minut, a druga — informacje o pojemności. Informacje o transakcji i minucie zawierają dane dotyczące żądań i odpowiedzi. Informacje o pojemności składają się z danych użycia magazynu. Metryki godzinowe, metryki minut i pojemności dla usługi BLOB konta magazynu są dostępne w tabelach, które są opisane w poniższej tabeli.  

|Poziom metryk|Nazwy tabel|Obsługiwane w wersjach|  
|-------------------|-----------------|----------------------------|  
|Metryki godzinowe, podstawowa lokalizacja|-$MetricsTransactionsBlob<br />-$MetricsTransactionsTable<br />-$MetricsTransactionsQueue|Wersje wcześniejsze niż 15 sierpnia 2013. Mimo że te nazwy są nadal obsługiwane, zalecamy przełączenie się do korzystania z tabel, które obserwują.|  
|Metryki godzinowe, podstawowa lokalizacja|-$MetricsHourPrimaryTransactionsBlob<br />-$MetricsHourPrimaryTransactionsTable<br />-$MetricsHourPrimaryTransactionsQueue<br />-$MetricsHourPrimaryTransactionsFile|Wszystkie wersje. Obsługa metryk usługi plików jest dostępna tylko w wersji 5 kwietnia 2015 i nowszych.|  
|Metryki minut, lokalizacja podstawowa|-$MetricsMinutePrimaryTransactionsBlob<br />-$MetricsMinutePrimaryTransactionsTable<br />-$MetricsMinutePrimaryTransactionsQueue<br />-$MetricsMinutePrimaryTransactionsFile|Wszystkie wersje. Obsługa metryk usługi plików jest dostępna tylko w wersji 5 kwietnia 2015 i nowszych.|  
|Metryki godzinowe, lokacja dodatkowa|-$MetricsHourSecondaryTransactionsBlob<br />-$MetricsHourSecondaryTransactionsTable<br />-$MetricsHourSecondaryTransactionsQueue|Wszystkie wersje. Replikacja geograficznie nadmiarowa do odczytu musi być włączona.|  
|Metryki minutowe, lokalizacja dodatkowa|-$MetricsMinuteSecondaryTransactionsBlob<br />-$MetricsMinuteSecondaryTransactionsTable<br />-$MetricsMinuteSecondaryTransactionsQueue|Wszystkie wersje. Replikacja geograficznie nadmiarowa do odczytu musi być włączona.|  
|Pojemność (tylko usługa BLOB)|$MetricsCapacityBlob|Wszystkie wersje.|  

 Te tabele są tworzone automatycznie, gdy analityka magazynu jest włączona dla punktu końcowego usługi magazynu. Są one dostępne za pośrednictwem przestrzeni nazw konta magazynu, na przykład `https://<accountname>.table.core.windows.net/Tables("$MetricsTransactionsBlob")` . Tabele metryk nie są wyświetlane w operacji tworzenia listy i należy uzyskać do nich dostęp bezpośrednio za pośrednictwem nazwy tabeli.

## <a name="metrics-alerts"></a>Alerty metryk
Rozważ skonfigurowanie alertów w [Azure Portal](https://portal.azure.com) , aby otrzymywać powiadomienia o ważnych zmianach w zachowaniu usług magazynu. Instrukcje krok po kroku znajdują się w temacie [Create Metric Alerts](./manage-storage-analytics-logs.md).

Jeśli użyjesz narzędzia Eksplorator usługi Storage, aby pobrać te dane metryk w formacie rozdzielanym, możesz użyć programu Microsoft Excel do przeanalizowania danych. Aby uzyskać listę dostępnych narzędzi Eksplorator usługi Storage, zobacz [narzędzia klienckie usługi Azure Storage](./storage-explorers.md).

> [!IMPORTANT]
> Może wystąpić opóźnienie między zdarzeniem magazynu i w przypadku rejestrowania odpowiednich danych metryk godzinowych lub minutowych. W przypadku metryk minut, kilka minut danych może być jednocześnie zapisywana. Przyczyną tego problemu może być transakcja z wcześniejszych minut agregowania do transakcji przez bieżącą minutę. Gdy ten problem wystąpi, usługa alertów może nie mieć wszystkich dostępnych danych metryk dla skonfigurowanego interwału alertów, co może prowadzić do nieoczekiwanego uruchamiania alertów.
>

## <a name="billing-on-storage-metrics"></a>Rozliczanie metryk magazynu
Żądania zapisu do tworzenia jednostek tabeli dla metryk są naliczone według standardowych stawek związanych ze wszystkimi operacjami usługi Azure Storage.  

Żądania odczytu i usuwania danych metryk przez klienta są również obciążane według stawek standardowych. W przypadku skonfigurowania zasad przechowywania danych opłaty nie są naliczone, gdy usługa Azure Storage usunie stare dane metryk. W przypadku usunięcia danych analitycznych Twoje konto jest obciążane za operacje usuwania.  

Opłaty za użycie w tabelach metryk są również naliczane. Poniższe informacje służą do oszacowania ilości pojemności używanej do przechowywania danych metryk:  

-   Jeśli każda usługa korzysta z każdego interfejsu API w każdej usłudze, około 148 KB danych jest przechowywana co godzinę w tabelach transakcji metryk w przypadku włączenia podsumowania poziomu usług i interfejsu API.  
-   Jeśli w każdej godzinie usługa korzysta z każdego interfejsu API w usłudze, około 12 KB danych jest przechowywany co godzinę w tabelach transakcji metryk w przypadku włączenia tylko podsumowania poziomu usługi.  
-   Tabela pojemności dla obiektów BLOB zawiera dwa wiersze, które zostały dodane każdego dnia, pod warunkiem w przypadku dzienników. W tym scenariuszu zakłada się, że każdy dzień tej tabeli zwiększa się o maksymalnie 300 bajtów.

## <a name="next-steps"></a>Następne kroki
* [Monitorowanie konta magazynu](https://www.windowsazure.com/manage/services/storage/how-to-monitor-a-storage-account/)   
* [Schemat tabeli metryk analityka magazynu](/rest/api/storageservices/storage-analytics-metrics-table-schema)   
* [analityka magazynu zarejestrowane operacje i komunikaty o stanie](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)   
* [Rejestrowanie analityka magazynu](storage-analytics-logging.md)
