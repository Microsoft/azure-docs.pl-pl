---
title: Uzyskiwanie zmian zasobów
description: Dowiedz się, jak znaleźć, kiedy zasób został zmieniony, uzyskać listę właściwości, które uległy zmianie, i Oceń różnice.
ms.date: 01/27/2021
ms.topic: how-to
ms.openlocfilehash: 5ad86ec2598cd7f24b8e0cd2208889bb7a088568
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100594643"
---
# <a name="get-resource-changes"></a>Uzyskiwanie zmian zasobów

Zasoby są zmieniane w trakcie codziennego użytkowania, ponownej konfiguracji i nawet ponownego wdrożenia.
Zmiany mogą pochodzić od osoby lub przez proces zautomatyzowany. Większość zmian jest zaprojektowana, ale czasami nie jest. W przypadku historii zmian z ostatnich 14 dni usługa Azure Resource Graph umożliwia:

- Znajdź, kiedy zostały wykryte zmiany we właściwości Azure Resource Manager
- Dla każdej zmiany zasobów Zobacz szczegóły zmiany właściwości
- Zobacz pełne porównanie zasobów przed i po wykrytej zmianie

Wykrywanie zmian i szczegółowe informacje są przydatne dla następujących przykładowych scenariuszy:

- Podczas zarządzania zdarzeniami w celu zrozumienia _potencjalnie_ powiązanych zmian. Wykonaj zapytanie o zdarzenia zmiany w określonym oknie czasu i Oceń szczegóły zmiany.
- Przechowywanie bazy danych zarządzania konfiguracją, znanej jako CMDB, na bieżąco. Zamiast odświeżania wszystkich zasobów i ich pełnych zestawów właściwości zgodnie z zaplanowaną częstotliwością, należy uzyskać tylko te zmiany.
- Informacje o innych właściwościach, które mogły zostać zmienione w przypadku zmiany stanu zgodności zasobu. Ocena tych dodatkowych właściwości może zapewnić wgląd w inne właściwości, które mogą być potrzebne do zarządzania za pośrednictwem definicji Azure Policy.

W tym artykule pokazano, jak zbierać te informacje za poorednictwem zestawu SDK grafu zasobów. Aby wyświetlić te informacje w Azure Portal, zobacz [historię zmian](../../policy/how-to/determine-non-compliance.md#change-history) Azure Policy lub [historię zmian](../../../azure-monitor/essentials/activity-log.md#view-the-activity-log)dziennika aktywności platformy Azure. Aby uzyskać szczegółowe informacje o zmianach w aplikacjach z warstwy infrastruktury we wszystkich sposobach wdrażania aplikacji, zobacz [Korzystanie z analizy zmian aplikacji (wersja zapoznawcza)](../../../azure-monitor/app/change-analysis.md) w Azure monitor.

> [!NOTE]
> Szczegóły zmiany w grafie zasobów są przeznaczone dla Menedżer zasobów właściwości. Aby śledzić zmiany wewnątrz maszyny wirtualnej, zobacz Azure Automation [śledzenie zmian](../../../automation/change-tracking/overview.md) lub [konfiguracja gościa Azure Policy dla maszyn wirtualnych](../../policy/concepts/guest-configuration.md).

> [!IMPORTANT]
> Historia zmian w usłudze Azure Resource Graph jest w publicznej wersji zapoznawczej.

## <a name="find-detected-change-events-and-view-change-details"></a>Znajdź wykryte zdarzenia zmiany i Wyświetl szczegóły zmiany

Pierwszym krokiem w przypadku wyświetlenia zmiany w zasobie jest znalezienie zdarzeń zmiany związanych z tym zasobem w oknie czasu. Każde zdarzenie zmiany zawiera również szczegółowe informacje o zmianach w zasobie. Ten krok jest realizowany za pomocą punktu końcowego REST **resourceChanges** .

Punkt końcowy **resourceChanges** akceptuje następujące parametry w treści żądania:

- Identyfikator **zasobu** \[ wymagane \] : zasób platformy Azure, w którym mają zostać wyszukane zmiany.
- **Interwał** \[ wymagane \] : Właściwość z datą _początkową_ i _końcową_ , kiedy należy wyszukać zdarzenie zmiany przy użyciu **strefy czasowej Zulu (Z)**.
- **fetchPropertyChanges** (opcjonalnie): właściwość logiczna, która ustawia, czy obiekt odpowiedzi zawiera zmiany właściwości.

Przykład treści żądania:

```json
{
    "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
    "interval": {
        "start": "2019-09-28T00:00:00.000Z",
        "end": "2019-09-29T00:00:00.000Z"
    },
    "fetchPropertyChanges": true
}
```

W przypadku powyższej treści żądania identyfikator URI interfejsu API REST dla **resourceChanges** jest:

```http
POST https://management.azure.com/providers/Microsoft.ResourceGraph/resourceChanges?api-version=2018-09-01-preview
```

Odpowiedź wygląda podobnie do tego przykładu:

```json
{
    "changes": [
        {
            "changeId": "{\"beforeId\":\"3262e382-9f73-4866-a2e9-9d9dbee6a796\",\"beforeTime\":\"2019-09-28T00:45:35.012Z\",\"afterId\":\"6178968e-981e-4dac-ac37-340ee73eb577\",\"afterTime\":\"2019-09-28T00:52:53.371Z\"}",
            "beforeSnapshot": {
                "snapshotId": "3262e382-9f73-4866-a2e9-9d9dbee6a796",
                "timestamp": "2019-09-28T00:45:35.012Z"
            },
            "afterSnapshot": {
                "snapshotId": "6178968e-981e-4dac-ac37-340ee73eb577",
                "timestamp": "2019-09-28T00:52:53.371Z"
            },
            "changeType": "Create"
        },
        {
            "changeId": "{\"beforeId\":\"a00f5dac-86a1-4d86-a1c5-a9f7c8147b7c\",\"beforeTime\":\"2019-09-28T00:43:38.366Z\",\"afterId\":\"3262e382-9f73-4866-a2e9-9d9dbee6a796\",\"afterTime\":\"2019-09-28T00:45:35.012Z\"}",
            "beforeSnapshot": {
                "snapshotId": "a00f5dac-86a1-4d86-a1c5-a9f7c8147b7c",
                "timestamp": "2019-09-28T00:43:38.366Z"
            },
            "afterSnapshot": {
                "snapshotId": "3262e382-9f73-4866-a2e9-9d9dbee6a796",
                "timestamp": "2019-09-28T00:45:35.012Z"
            },
            "changeType": "Delete"
        },
        {
            "changeId": "{\"beforeId\":\"b37a90d1-7ebf-41cd-8766-eb95e7ee4f1c\",\"beforeTime\":\"2019-09-28T00:43:15.518Z\",\"afterId\":\"a00f5dac-86a1-4d86-a1c5-a9f7c8147b7c\",\"afterTime\":\"2019-09-28T00:43:38.366Z\"}",
            "beforeSnapshot": {
                "snapshotId": "b37a90d1-7ebf-41cd-8766-eb95e7ee4f1c",
                "timestamp": "2019-09-28T00:43:15.518Z"
            },
            "afterSnapshot": {
                "snapshotId": "a00f5dac-86a1-4d86-a1c5-a9f7c8147b7c",
                "timestamp": "2019-09-28T00:43:38.366Z"
            },
            "propertyChanges": [
                {
                    "propertyName": "tags.org",
                    "afterValue": "compute",
                    "changeCategory": "User",
                    "changeType": "Insert"
                },
                {
                    "propertyName": "tags.team",
                    "afterValue": "ARG",
                    "changeCategory": "User",
                    "changeType": "Insert"
                }
            ],
            "changeType": "Update"
        },
        {
            "changeId": "{\"beforeId\":\"19d12ab1-6ac6-4cd7-a2fe-d453a8e5b268\",\"beforeTime\":\"2019-09-28T00:42:46.839Z\",\"afterId\":\"b37a90d1-7ebf-41cd-8766-eb95e7ee4f1c\",\"afterTime\":\"2019-09-28T00:43:15.518Z\"}",
            "beforeSnapshot": {
                "snapshotId": "19d12ab1-6ac6-4cd7-a2fe-d453a8e5b268",
                "timestamp": "2019-09-28T00:42:46.839Z"
            },
            "afterSnapshot": {
                "snapshotId": "b37a90d1-7ebf-41cd-8766-eb95e7ee4f1c",
                "timestamp": "2019-09-28T00:43:15.518Z"
            },
            "propertyChanges": [{
                "propertyName": "tags.cgtest",
                "afterValue": "hello",
                "changeCategory": "User",
                "changeType": "Insert"
            }],
            "changeType": "Update"
        }
    ]
}
```

Każde wykryte zdarzenie zmiany dla **ResourceID** ma następujące właściwości:

- **changeId** — ta wartość jest unikatowa dla tego zasobu. Ciąg **changeId** może czasami zawierać inne właściwości, dlatego jest tylko unikatowy.
- **beforeSnapshot** — zawiera **snapshotId** i **sygnaturę czasową** migawki zasobu, która została wykonana przed wykryciem zmiany.
- **afterSnapshot** — zawiera **snapshotId** i **sygnaturę czasową** migawki zasobu, która została wykonana po wykryciu zmiany.
- **ChangeType** — opisuje typ zmiany wykryty dla całego rekordu zmiany między **beforeSnapshot** i **afterSnapshot**. Wartości to: _Tworzenie_, _Aktualizowanie_ i _usuwanie_. Tablica właściwości **propertyChanges** jest uwzględniana tylko wtedy, gdy **ChangeType** jest _aktualizacją_.
- **propertyChanges** — ta tablica właściwości zawiera szczegóły wszystkich właściwości zasobów, które zostały zaktualizowane między **beforeSnapshot** i **afterSnapshot**:
  - **PropertyName** — nazwa właściwości zasobu, która została zmieniona.
  - **changeCategory** — opisuje, jak została wprowadzona zmiana. Wartości to: _system_ i _użytkownik_.
  - **ChangeType** — opisuje typ zmiany wykryty dla poszczególnych właściwości zasobu.
    Wartości to: _INSERT_, _Update_, _Remove_.
  - **beforeValue** — wartość właściwości zasobu w **beforeSnapshot**. Nie jest wyświetlany, gdy **ChangeType** jest _Wstawianie_.
  - **afterValue** — wartość właściwości zasobu w **afterSnapshot**. Nie jest wyświetlany, gdy **ChangeType** jest _usuwany_.

## <a name="compare-resource-changes"></a>Porównaj zmiany zasobów

Przy **changeId** z punktu końcowego **resourceChanges** , punkt końcowy REST **resourceChangeDetails** jest następnie używany do pobierania migawek przed i po zmianie zasobu, który został zmieniony.

Punkt końcowy **resourceChangeDetails** wymaga dwóch parametrów w treści żądania:

- **ResourceID**: zasób platformy Azure, w którym mają zostać porównane zmiany.
- **changeId**: unikatowe zdarzenie zmiany dla elementu **ResourceID** zebrane z **resourceChanges**.

Przykład treści żądania:

```json
{
    "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
    "changeId": "{\"beforeId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-09T00:00:00.000Z\",\"afterId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"afterTime\":'2019-05-10T00:00:00.000Z\"}"
}
```

W przypadku powyższej treści żądania identyfikator URI interfejsu API REST dla **resourceChangeDetails** jest:

```http
POST https://management.azure.com/providers/Microsoft.ResourceGraph/resourceChangeDetails?api-version=2018-09-01-preview
```

Odpowiedź wygląda podobnie do tego przykładu:

```json
{
    "changeId": "{\"beforeId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-09T00:00:00.000Z\",\"afterId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-10T00:00:00.000Z\"}",
    "beforeSnapshot": {
        "timestamp": "2019-03-29T01:32:05.993Z",
        "content": {
            "sku": {
                "name": "Standard_LRS",
                "tier": "Standard"
            },
            "kind": "Storage",
            "id": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
            "name": "mystorageaccount",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "westus",
            "tags": {},
            "properties": {
                "networkAcls": {
                    "bypass": "AzureServices",
                    "virtualNetworkRules": [],
                    "ipRules": [],
                    "defaultAction": "Allow"
                },
                "supportsHttpsTrafficOnly": false,
                "encryption": {
                    "services": {
                        "file": {
                            "enabled": true,
                            "lastEnabledTime": "2018-07-27T18:37:21.8333895Z"
                        },
                        "blob": {
                            "enabled": true,
                            "lastEnabledTime": "2018-07-27T18:37:21.8333895Z"
                        }
                    },
                    "keySource": "Microsoft.Storage"
                },
                "provisioningState": "Succeeded",
                "creationTime": "2018-07-27T18:37:21.7708872Z",
                "primaryEndpoints": {
                    "blob": "https://mystorageaccount.blob.core.windows.net/",
                    "queue": "https://mystorageaccount.queue.core.windows.net/",
                    "table": "https://mystorageaccount.table.core.windows.net/",
                    "file": "https://mystorageaccount.file.core.windows.net/"
                },
                "primaryLocation": "westus",
                "statusOfPrimary": "available"
            }
        }
    },
    "afterSnapshot": {
        "timestamp": "2019-03-29T01:54:24.42Z",
        "content": {
            "sku": {
                "name": "Standard_LRS",
                "tier": "Standard"
            },
            "kind": "Storage",
            "id": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
            "name": "mystorageaccount",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "westus",
            "tags": {},
            "properties": {
                "networkAcls": {
                    "bypass": "AzureServices",
                    "virtualNetworkRules": [],
                    "ipRules": [],
                    "defaultAction": "Allow"
                },
                "supportsHttpsTrafficOnly": true,
                "encryption": {
                    "services": {
                        "file": {
                            "enabled": true,
                            "lastEnabledTime": "2018-07-27T18:37:21.8333895Z"
                        },
                        "blob": {
                            "enabled": true,
                            "lastEnabledTime": "2018-07-27T18:37:21.8333895Z"
                        }
                    },
                    "keySource": "Microsoft.Storage"
                },
                "provisioningState": "Succeeded",
                "creationTime": "2018-07-27T18:37:21.7708872Z",
                "primaryEndpoints": {
                    "blob": "https://mystorageaccount.blob.core.windows.net/",
                    "queue": "https://mystorageaccount.queue.core.windows.net/",
                    "table": "https://mystorageaccount.table.core.windows.net/",
                    "file": "https://mystorageaccount.file.core.windows.net/"
                },
                "primaryLocation": "westus",
                "statusOfPrimary": "available"
            }
        }
    }
}
```

**beforeSnapshot** i **afterSnapshot** każdy podaje czas trwania migawki i właściwości w tym czasie. Zmiana miała miejsce w pewnym momencie między tymi migawkami. Patrząc na poprzedni przykład, możemy zobaczyć, że właściwość, która została zmieniona, została **supportsHttpsTrafficOnly**.

Aby porównać wyniki, należy użyć właściwości **zmiany** w **resourceChanges** lub oszacować część **zawartości** każdej migawki w **resourceChangeDetails** , aby określić różnicę. W przypadku porównania migawek **sygnatura czasowa** zawsze będzie widoczna jako różnica, mimo że jest to oczekiwane.

## <a name="next-steps"></a>Następne kroki

- Zobacz język używany w [zapytaniach początkowych](../samples/starter.md).
- Zobacz zaawansowane zastosowania w [zaawansowanych zapytaniach](../samples/advanced.md).
- Dowiedz się więcej o sposobach [eksplorowania zasobów](../concepts/explore-resources.md).
- Aby uzyskać wskazówki dotyczące pracy z kwerendami z dużą częstotliwością, zobacz [wskazówki dotyczące żądań ograniczających](../concepts/guidance-for-throttled-requests.md).
