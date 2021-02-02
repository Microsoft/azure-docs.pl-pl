---
title: Używanie spisu usługi Azure Storage do zarządzania danymi obiektów BLOB (wersja zapoznawcza)
description: Spis usługi Azure Storage to narzędzie ułatwiające zapoznaj się z omówieniem wszystkich danych obiektów BLOB w ramach konta magazynu.
services: storage
author: mhopkins-msft
ms.service: storage
ms.date: 12/03/2020
ms.topic: conceptual
ms.author: mhopkins
ms.reviewer: yzheng
ms.subservice: blobs
ms.custom: references_regions
ms.openlocfilehash: a41966c2b3ba73d7b68399b1b99d14313e220833
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/02/2021
ms.locfileid: "99257815"
---
# <a name="use-azure-storage-blob-inventory-to-manage-blob-data-preview"></a>Używanie spisu obiektów BLOB usługi Azure Storage do zarządzania danymi obiektów BLOB (wersja zapoznawcza)

Funkcja spisywania obiektów BLOB usługi Azure Storage zawiera przegląd danych obiektów BLOB w ramach konta magazynu. Za pomocą raportu spisu można zrozumieć łączny rozmiar danych, wiek, stan szyfrowania itd. Ten raport zawiera przegląd danych na potrzeby biznesowe i wymagania dotyczące zgodności. Po włączeniu tego raportu spis zostanie automatycznie utworzony codziennie.

## <a name="availability"></a>Dostępność

Spis obiektów BLOB jest obsługiwany w przypadku kont usługi Storage ogólnego przeznaczenia w wersji 2 (GPv2) i blokowych bloków BLOB w warstwie Premium. Ta funkcja jest obsługiwana z włączoną funkcją [hierarchicznej przestrzeni nazw](data-lake-storage-namespace.md) lub bez niej.

### <a name="preview-regions"></a>Podgląd regionów

Wersja zapoznawcza magazynu obiektów BLOB jest dostępna na kontach magazynu w następujących regionach:

- Francja Środkowa
- Kanada Środkowa
- Kanada Wschodnia
- East US
- Wschodnie stany USA 2

### <a name="pricing-and-billing"></a>Cennik i rozliczenia

Opłata za raporty dotyczące spisu nie jest naliczana w okresie zapoznawczym. Cennik zostanie ustalony, gdy ta funkcja jest ogólnie dostępna.

## <a name="enable-inventory-reports"></a>Włącz raporty spisu

Włącz raporty dotyczące spisu obiektów blob, dodając zasady do konta magazynu. Dodawanie, edytowanie lub usuwanie zasad przy użyciu [Azure Portal](https://portal.azure.com/).

1. Przejdź do [Azure Portal](https://portal.azure.com/)
1. Wybierz jedno z kont magazynu
1. W obszarze **BLOB Service** wybierz pozycję **Magazyn obiektów BLOB**
1. Upewnij się, że jest zaznaczona **Funkcja spis obiektów BLOB włączona**
1. Wybierz pozycję **Dodaj regułę**
1. Nazwij nową regułę
1. Wybierz **typy obiektów BLOB** dla raportu ze spisu
1. Dodaj dopasowanie prefiksu, aby odfiltrować raport dotyczący spisu
1. Zdecyduj, czy **dołączać wersje obiektów BLOB** i **dołączyć migawki** do raportu ze spisu. Wersje i migawki muszą być włączone na koncie, aby można było zapisać nową regułę z włączoną odpowiednią opcją.
1. Wybierz pozycję **Zapisz**

:::image type="content" source="./media/blob-inventory/portal-blob-inventory.png" alt-text="Zrzut ekranu przedstawiający sposób dodawania reguły spisu obiektów BLOB przy użyciu Azure Portal":::

Zasady spisu są odczytywane lub zapisywane w całości. Aktualizacje częściowe nie są obsługiwane.

> [!IMPORTANT]
> Jeśli włączysz reguły zapory dla konta magazynu, żądania spisu mogą być blokowane. Można odblokować te żądania, określając wyjątki dla zaufanych usług firmy Microsoft. Aby uzyskać więcej informacji, zobacz sekcję wyjątki w artykule [Konfigurowanie zapór i sieci wirtualnych](../common/storage-network-security.md#exceptions).

Uruchamianie spisu obiektów BLOB jest automatycznie planowane codziennie. Ukończenie działania spisu może potrwać do 24 godzin. Raport spisu jest konfigurowany przez dodanie zasad spisu do co najmniej jednej reguły.

## <a name="inventory-policy"></a>Zasady spisu

Zasada spisu jest kolekcją reguł w dokumencie JSON.

```json
{
    "destination": "destinationContainer",
    "enabled": true,
    "rules": [
    {
        "enabled": true,
        "name": "inventoryrule1",
        "definition": {. . .}
    },
    {
        "enabled": true,
        "name": "inventoryrule2",
        "definition": {. . .}
    }]
}
```

Aby wyświetlić dane JSON dla zasad spisu, wybierz kartę **Widok kodu** w sekcji **Magazyn obiektów BLOB** w Azure Portal.

| Nazwa parametru | Typ parametru        | Uwagi | Wymagane? |
|----------------|-----------------------|-------|-----------|
| destination    | Ciąg                | Kontener docelowy, w którym zostaną wygenerowane wszystkie pliki spisu. Kontener docelowy musi już istnieć. | Tak |
| enabled        | Wartość logiczna               | Służy do wyłączania wszystkich zasad. Po ustawieniu na **wartość true** pole włączony poziom reguły zastępuje ten parametr. Po wyłączeniu spis wszystkich reguł zostanie wyłączony. | Tak |
| rules          | Tablica obiektów reguł | W zasadach jest wymagana co najmniej jedna reguła. Obsługiwane są maksymalnie 10 reguł. | Tak |

## <a name="inventory-rules"></a>Reguły spisu

Reguła przechwytuje warunki filtrowania i parametry wyjściowe do generowania raportu spisu. Każda reguła tworzy raport ze spisu. Reguły mogą nakładać się na prefiksy. Obiekt BLOB może znajdować się w więcej niż jednym spisie w zależności od definicji reguł.

Każda reguła w ramach zasad ma kilka parametrów:

| Nazwa parametru | Typ parametru                 | Uwagi | Wymagane? |
|----------------|--------------------------------|-------|-----------|
| name           | Ciąg                         | Nazwa reguły może zawierać do 256 znaków alfanumerycznych z rozróżnianiem wielkości liter. Nazwa musi być unikatowa w ramach zasad. | Tak |
| enabled        | Wartość logiczna                        | Flaga zezwalająca na włączenie lub wyłączenie reguły. Wartość domyślna to **true**. | Tak |
| definicja     | Definicja reguły spisu JSON | Każda definicja składa się z zestawu filtrów reguł. | Tak |

Flaga z **włączonym globalnym magazynem obiektów BLOB** ma pierwszeństwo przed *włączonym* parametrem w regule.

### <a name="rule-filters"></a>Filtry reguł

Do dostosowywania raportu spisu obiektów BLOB dostępne są kilka filtrów:

| Nazwa filtru         | Typ filtru                     | Uwagi | Wymagane? |
|---------------------|---------------------------------|-------|-----------|
| blobTypes           | Tablica wstępnie zdefiniowanych wartości wyliczeniowych | Prawidłowe wartości to `blockBlob` i `appendBlob` dla kont z włączoną hierarchiczną przestrzeni nazw oraz `blockBlob` , `appendBlob` i `pageBlob` dla innych kont. | Tak |
| prefixMatch         | Tablica do 10 ciągów dla prefiksów, które mają zostać dopasowane. Prefiks musi rozpoczynać się od nazwy kontenera, na przykład "container1/foo" | Jeśli nie zdefiniujesz *prefixMatch* lub nie podajesz pustego prefiksu, reguła zostanie zastosowana do wszystkich obiektów BLOB w ramach konta magazynu. | Nie |
| includeSnapshots    | Wartość logiczna                         | Określa, czy spis powinien zawierać migawki. Wartość domyślna to **false**. | Nie |
| includeBlobVersions | Wartość logiczna                         | Określa, czy spis powinien zawierać wersje obiektów BLOB. Wartość domyślna to **false**. | Nie |

Wyświetl dane JSON dla reguł spisu, wybierając kartę **Widok kodu** w sekcji **Magazyn obiektów BLOB** w Azure Portal. Filtry są określone w definicji reguły.

```json
{
    "destination": "destinationContainer",
    "enabled": true,
    "rules": [
    {
        "enabled": true,
        "name": "inventoryrule1",
        "definition":
        {
            "filters":
            {
                "blobTypes": ["blockBlob", "appendBlob", "pageBlob"],
                "prefixMatch": ["inventorycontainer1", "inventorycontainer2/abcd", "etc"]
            }
        }
    },
    {
        "enabled": true,
        "name": "inventoryrule2",
        "definition":
        {
            "filters":
            {
                "blobTypes": ["pageBlob"],
                "prefixMatch": ["inventorycontainer-disks-", "inventorycontainer4/"],
                "includeSnapshots": true,
                "includeBlobVersions": true
            }
        }
    }]
}
```

## <a name="inventory-output"></a>Dane wyjściowe spisu

Każdy przebieg spisu generuje zestaw plików w formacie CSV w określonym kontenerze docelowym spisu. Dane wyjściowe spisu są generowane w następującej ścieżce: `https://<accountName>.blob.core.windows.net/<inventory-destination-container>/YYYY/MM/DD/HH-MM-SS/` gdzie:

- Nazwa *konta* jest nazwą konta usługi Azure Blob Storage
- *spis — docelowy — kontener* to kontener docelowy określony w zasadach spisu
- *Rrrr/mm/dd/hh-mm-SS* to czas, w którym rozpoczęto wykonywanie spisu

### <a name="inventory-files"></a>Pliki spisu

Każdy przebieg spisu generuje następujące pliki:

- **Plik CSV spisu**: plik wartości rozdzielanych przecinkami (CSV) dla każdej reguły spisu. Każdy plik zawiera dopasowane obiekty i ich metadane. Pierwszy wiersz w każdym pliku CSV jest zawsze wierszem schematu. Na poniższej ilustracji przedstawiono plik CSV ze spisem otwarty w programie Microsoft Excel.

   :::image type="content" source="./media/blob-inventory/csv-file-excel.png" alt-text="Zrzut ekranu przedstawiający plik CSV spisu otwarty w programie Microsoft Excel":::

- **Plik manifestu**: manifest.jspliku zawierającego szczegóły plików spisu generowanych dla każdej reguły w tym przebiegu. Plik manifestu przechwytuje również definicję reguły dostarczoną przez użytkownika i ścieżkę do spisu dla tej reguły.

- **Plik sumy kontrolnej**: plik manifest. Checksum zawierający sumę kontrolną MD5 zawartości manifest.jsw pliku. Generacja pliku manifest. Checksum oznacza zakończenie przebiegu spisu.

## <a name="inventory-completed-event"></a>Zdarzenie ukończenia spisu

Zasubskrybuj zdarzenie ukończenia spisu, aby otrzymywać powiadomienia o zakończeniu działania spisu. To zdarzenie jest generowane podczas tworzenia pliku sum kontrolnych manifestu. Zdarzenie ukończenia spisu występuje również wtedy, gdy uruchomienie spisu nie powiedzie się z powodu błędu użytkownika przed uruchomieniem. Na przykład nieprawidłowe zasady lub nieobecny kontener docelowy spowodują wywołanie zdarzenia. Zdarzenie jest publikowane w temacie magazyn obiektów BLOB.

Przykładowe zdarzenie:

```json
{
  "topic": "/subscriptions/3000151d-7a84-4120-b71c-336feab0b0f0/resourceGroups/BlobInventory/providers/Microsoft.EventGrid/topics/BlobInventoryTopic",
  "subject": "BlobDataManagement/BlobInventory",
  "eventType": "Microsoft.Storage.BlobInventoryPolicyCompleted",
  "id": "c99f7962-ef9d-403e-9522-dbe7443667fe",
  "data": {
    "scheduleDateTime": "2020-10-13T15:37:33Z",
    "accountName": "inventoryaccountname",
    "policyRunStatus": "Succeeded",
    "policyRunStatusMessage": "Inventory run succeeded, refer manifest file for inventory details.",
    "policyRunId": "b5e1d4cc-ee23-4ed5-b039-897376a84f79",
    "manifestBlobUrl": "https://inventoryaccountname.blob.core.windows.net/inventory-destination-container/2020/10/13/15-37-33/manifest.json"
  },
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2020-10-13T15:47:54Z"
}
```

## <a name="next-steps"></a>Następne kroki

[Zarządzanie cyklem życia usługi Azure Blob Storage](storage-lifecycle-management-concepts.md)
