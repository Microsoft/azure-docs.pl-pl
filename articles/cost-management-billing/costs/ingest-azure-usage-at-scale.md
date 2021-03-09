---
title: Powtarzaj pobieranie dużych zestawów danych kosztów za pomocą eksportu
description: Ten artykuł pomaga regularnie eksportować duże ilości danych z eksportami z Azure Cost Management.
author: bandersmsft
ms.author: banders
ms.date: 03/08/2021
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: adwise
ms.openlocfilehash: 465225341bdffc984ac6cbc82ba94eb656ad60df
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102509700"
---
# <a name="retrieve-large-cost-datasets-recurringly-with-exports"></a>Powtarzaj pobieranie dużych zestawów danych kosztów za pomocą eksportu

Ten artykuł pomaga regularnie eksportować duże ilości danych z eksportami z Azure Cost Management. Eksportowanie jest zalecanym sposobem pobierania niezagregowanych danych dotyczących kosztów. Szczególnie gdy pliki użycia są zbyt duże, aby można było w niezawodny sposób wywoływać je i pobierać przy użyciu interfejsu API szczegółów użycia. Wyeksportowane dane są umieszczane na wybranym koncie usługi Azure Storage. Stamtąd możesz ładować je do własnych systemów i analizować zgodnie z potrzebami. Aby skonfigurować eksporty w witrynie Azure Portal, zobacz [Eksportowanie danych](tutorial-export-acm-data.md).

Jeśli chcesz zautomatyzować eksporty w różnych zakresach, dobrym punktem wyjścia jest przykładowe żądanie interfejsu API w następnej sekcji. Za pomocą interfejsu API eksportów możesz tworzyć automatyczne eksporty w ramach ogólnej konfiguracji środowiska. Automatyczne eksporty pomagają upewnić się, że masz potrzebne dane. W miarę rozszerzania zakresu zastosowań platformy Azure możesz używać ich w systemach swojej organizacji.

## <a name="common-export-configurations"></a>Typowe konfiguracje eksportu

Zanim utworzysz pierwszy eksport, rozważ scenariusz i opcje konfiguracji niezbędne do jego realizacji. Rozważ następujące opcje eksportu:

- **Cykl** — określa częstotliwość uruchamiania zadania eksportu oraz moment umieszczenia pliku na koncie usługi Azure Storage. Do wyboru są opcje Codziennie, Co tydzień i Co miesiąc. Spróbuj skonfigurować cykl w taki sposób, aby pasował do zadań importu danych używanych przez wewnętrzny system w organizacji.
- **Okres cyklu** — określa, jak długo eksport pozostaje ważny. Pliki są eksportowane tylko w okresie cyklu.
- **Horyzont czasowy** — określa ilość danych wygenerowanych przez eksport w danym przebiegu. Typowe opcje to Bieżący miesiąc i Bieżący tydzień.
- **Data rozpoczęcia** — konfiguruje czas rozpoczęcia harmonogramu eksportu. Eksport jest tworzony w dniu rozpoczęcia i później, w zależności od ustawień cyklu.
- **Typ** — istnieją trzy typy eksportu:
  - Koszt rzeczywisty — pokazuje łączne użycie i koszty dla określonego okresu, gdy są naliczane i widoczne na rachunku.
  - Koszt amortyzowany — pokazuje łączne użycie i koszty dla określonego okresu z zastosowaną amortyzacją obowiązujących kosztów zakupu rezerwacji.
  - Użycie — wszystkie eksporty utworzone przed 20 lipca 2020 r. mają typ Użycie. Zaktualizuj wszystkie zaplanowane eksporty jako Koszt rzeczywisty lub Koszt amortyzowany.
- **Kolumny** — definiuje pola danych, które mają być uwzględnione w pliku eksportu. Odpowiadają one polom dostępnym w interfejsie API szczegółów użycia. Aby uzyskać więcej informacji, zobacz [Interfejs API szczegółów użycia](/rest/api/consumption/usagedetails/list).

## <a name="create-a-daily-month-to-date-export-for-a-subscription"></a>Tworzenie codziennego eksportu z bieżącego miesiąca na potrzeby subskrypcji

Adres URL żądania: `PUT https://management.azure.com/{scope}/providers/Microsoft.CostManagement/exports/{exportName}?api-version=2020-06-01`

```json
{
  "properties": {
    "schedule": {
      "status": "Active",
      "recurrence": "Daily",
      "recurrencePeriod": {
        "from": "2020-06-01T00:00:00Z",
        "to": "2020-10-31T00:00:00Z"
      }
    },
    "format": "Csv",
    "deliveryInfo": {
      "destination": {
        "resourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MYDEVTESTRG/providers/Microsoft.Storage/storageAccounts/{yourStorageAccount} ",
        "container": "{yourContainer}",
        "rootFolderPath": "{yourDirectory}"
      }
    },
    "definition": {
      "type": "ActualCost",
      "timeframe": "MonthToDate",
      "dataSet": {
        "granularity": "Daily",
        "configuration": {
          "columns": [
            "Date",
            "MeterId",
            "ResourceId",
            "ResourceLocation",
            "Quantity"
          ]
        }
      }
    }
}
```

## <a name="copy-large-azure-storage-blobs"></a>Kopiowanie dużych obiektów BLOB usługi Azure Storage

Za pomocą Cost Management można zaplanować eksporty szczegółów użycia platformy Azure na konta usługi Azure Storage jako obiekty blob. Rozmiary obiektów BLOB mogą być powyżej gigabajtów. Zespół Azure Cost Management pracował z zespołem usługi Azure Storage w celu testowania kopiowania dużych obiektów BLOB usługi Azure Storage. Wyniki są udokumentowane w poniższych sekcjach. Można oczekiwać, że wyniki są podobne podczas kopiowania obiektów blob magazynu z jednego regionu platformy Azure do innego.

W celu przetestowania wydajności zespół przeniósł obiekty blob z kont magazynu w regionie zachodnie stany USA do tego samego i innych regionów. Zespół przestawia szybkości, które przepadają w przedziale od 2 GB na sekundę w tym samym regionie do 150 MB na sekundę do kont magazynu w regionie Azja Wschodnia Południowej.

### <a name="test-configuration"></a>Konfiguracja testu

Aby zmierzyć szybkości transferu obiektów blob, zespół utworzył prostą aplikację konsolową platformy .NET odwołującą się do najnowszej wersji (v 2.0.1) biblioteki przenoszenia danych (DLM) platformy Azure za pośrednictwem narzędzia NuGet. DLM to zestaw SDK dostarczany przez zespół usługi Azure Storage, który ułatwia dostęp programistyczny do usług transferu. Następnie utworzyli konta magazynu w wersji 2 w wielu regionach i użyj zachodnich stanów USA jako regionu źródłowego. Wypełnią one konta magazynu z kontenerami, w których każdy z nich ma 10 2 GB bloków obiektów BLOB. Te kontenery zostały skopiowane do innych kont magazynu za pomocą metody DLM _. CopyDirectoryAsync ()_ z opcją _CopyMethod. ServiceSideSyncCopy_ . Testy zostały przeprowadzone na komputerze z systemem Windows 10 z 12 rdzeniami i siecią 1-GbE.

Użyte ustawienia aplikacji:

- _TransferManager.Configurations. ParallelOperations_  =  _środowiska. ProcessorCount \* 32_. Zespół znalazł ustawienie mające największy wpływ na ogólną przepływność. Wartość 32 pomnożona przez liczbę rdzeni, które zapewniają najlepszą przepływność dla klienta testowego.
- _ServicePointManager. DefaultConnectionLimit = int. MaxValue_. Ustawienie na wartość maksymalną powoduje skuteczne przekazanie pełnej kontroli równoległości transferu do powyższego ustawienia _ParallelOperations_ .
- _TransferManager.Configurations. Rozmiar bloku = 4 194 304_. Miało to wpływ na szybkości transferu z 4 MB, co jest najlepszym rozwiązaniem do testowania.

Aby uzyskać więcej informacji i przykładowy kod, zobacz linki w sekcji [następne kroki](#next-steps) .

### <a name="test-results"></a>Wyniki testu

| **Numer testu** | **Do regionu** | **Obiekty blob** | **Czas (s)** | **MB/s** | **Komentarze** |
| --- | --- | --- | --- | --- | --- |
| 1 | WestUS | 2 GB x 10 | 10 | 2000 |   |
| 2 | WestUS2 | 2 GB x 10 | 33 | 600 |   |
| 3 | EastUS | 2 GB x 10 | 67 | 300 |   |
| 4 | EastUS | 2 GB x 10 x 4 | 99 | 200 | 4 transfery równoległe przy użyciu 8 kont magazynu: średnia od 1 do 4 wschód na transfer |
| 6 | EastUS | 2 GB x 10 x 4 | 92 | 870 | 4 transfery równoległe z jednego konta magazynu do innego |
| 5 | EastUS | 2G x 10 x 8 | 148 | 135 | 8 transferów równoległych przy użyciu 8 kont magazynu: Średnia 4x2 Wschodnia na transfer |
| 7 | Azja Wschodnia | 2 GB x 10 | 133 | 150 |   |
| 8 | Azja Wschodnia | 2 GB x 10 x 4 | 444 | 180 | 4 transfery równoległe z jednego konta magazynu do innego |

### <a name="sync-transfer-characteristics"></a>Charakterystyki transferu synchronizacji

Poniżej przedstawiono niektóre cechy transferu synchronizacji po stronie usług używane z DML, które są istotne dla użycia:

- DML można przenieść pojedynczego obiektu BLOB lub katalogu. W przypadku transferu katalogów można użyć wzorca wyszukiwania, aby dopasować go do prefiksu obiektu BLOB.
- Blokowanie transferów obiektów BLOB odbywa się równolegle. Wszystko kończy się w kierunku końca procesu transferu. Poszczególne bloki obiektów BLOB są przesyłane równolegle.
- Transfer jest wykonywany asynchronicznie na kliencie. Stan transferu jest okresowo dostępny za pośrednictwem wywołania zwrotnego do metody, która może być zdefiniowana w obiekcie _TransferContext_ .
- Transfer tworzy punkty kontrolne w toku i uwidacznia obiekt _TransferCheckpoint_ . Obiekt reprezentuje ostatni punkt kontrolny za pośrednictwem obiektu _TransferContext_ . Jeśli _TransferCheckpoint_ jest zapisywany przed anulowaniem transferu/przerwaniem, transfer może zostać wznowiony od punktu kontrolnego przez maksymalnie siedem dni. Transfer można wznowić z dowolnego punktu kontrolnego, a nie tylko najnowszego.
- Jeśli proces klienta transferu zostanie zamknięty i uruchomiony ponownie bez implementacji funkcji Checkpoint.
  - Przed ukończeniem jakichkolwiek transferów obiektów BLOB transfer zostanie uruchomiony ponownie.
  - Po zakończeniu niektórych obiektów BLOB transfer zostanie uruchomiony ponownie tylko dla nieukończonych obiektów BLOB.
- Wstrzymywanie wykonywania przez klienta wstrzymuje transfery.
- Funkcja transferu obiektów BLOB wyodrębnia klienta z błędów przejściowych. Na przykład ograniczanie konta magazynu nie zwykle spowoduje niepowodzenie transferu, ale spowoduje spowolnienie transferu.
- Transfery po stronie usług mają niskie użycie zasobów klienta dotyczące procesora i pamięci, pewnej przepustowości sieci i połączeń.

### <a name="async-transfer-characteristics"></a>Właściwości transferu asynchronicznego

Metodę _transfermanager. CopyDirectoryAsync ()_ można wywołać za pomocą opcji _CopyMethod. ServiceSideAsyncCopy_ . Działa podobnie jak mechanizm transferu synchronizacji z perspektywy klienta, ale z następującymi różnicami w działaniu:

- Stawki za transfer są znacznie wolniejsze niż odpowiedni transfer synchronizacji (zwykle 10 MB/s lub mniej).
- Transfer kontynuuje działanie nawet wtedy, gdy proces klienta zostanie przerwany.
- Mimo że punkty kontrolne są obsługiwane, wznowienie transferu przy użyciu _TransferCheckpoint_ nie zostanie wznowione w czasie punktu kontrolnego, ale w bieżącym stanie transferu.

### <a name="test-summary"></a>Podsumowanie testu

Usługa Azure Blob Storage obsługuje wysokie globalne szybkości transferu przy użyciu funkcji transferu synchronizacji po stronie usługi. Korzystanie z funkcji w aplikacjach .NET jest proste przy użyciu biblioteki przenoszenia danych. Możliwe jest Cost Management eksportu, aby niezawodnie kopiować setki gigabajtów danych na konto magazynu w dowolnym miejscu w czasie krótszym niż godzina.

## <a name="next-steps"></a>Następne kroki

- Zobacz źródło [biblioteki przenoszenia danych Microsoft Azure Storage](https://github.com/Azure/azure-storage-net-data-movement) .
- [Przenoszenie danych za pomocą biblioteki przenoszenia danych](../../storage/common/storage-use-data-movement-library.md).
- Zobacz przykład przykładowego źródła [aplikacji AzureDmlBackup](https://github.com/markjbrown/AzureDmlBackup) .
- Zapoznaj [się z wysoką przepływność przy użyciu usługi Azure Blob Storage](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage).