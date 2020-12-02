---
title: Pobieranie dzienników IoT Edge — Azure IoT Edge
description: IoT Edge pobieranie dzienników modułu i przekazywanie ich do usługi Azure Blob Storage.
author: v-tcassi
manager: philmea
ms.author: v-tcassi
ms.date: 11/12/2020
ms.topic: conceptual
ms.reviewer: veyalla
ms.service: iot-edge
ms.custom: devx-track-azurecli
services: iot-edge
ms.openlocfilehash: dccb734ef4eaa9f22b70488918f14ad94f723453
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96437137"
---
# <a name="retrieve-logs-from-iot-edge-deployments"></a>Pobieranie dzienników z wdrożeń IoT Edge

Pobierz dzienniki z wdrożeń IoT Edge bez konieczności dostępu fizycznego ani SSH do urządzenia za pomocą metod bezpośrednich zawartych w module agenta IoT Edge. Metody bezpośrednie są implementowane na urządzeniu, a następnie mogą być wywoływane z chmury. Agent IoT Edge obejmuje metody bezpośrednie, które ułatwiają zdalne monitorowanie urządzeń IoT Edge i zarządzanie nimi. Metody bezpośrednie omówione w tym artykule są ogólnie dostępne w wersji 1.0.10.

Aby uzyskać więcej informacji na temat metod bezpośrednich, sposobu ich używania i sposobu ich implementacji w własnych modułach, zobacz [Omówienie i wywoływanie metod bezpośrednich z IoT Hub](../iot-hub/iot-hub-devguide-direct-methods.md).

Nazwy tych metod bezpośrednich są obsługiwane z uwzględnieniem wielkości liter.

## <a name="recommended-logging-format"></a>Zalecany Format rejestrowania

Chociaż nie jest to wymagane, w celu uzyskania najlepszej zgodności z tą funkcją zalecanym formatem rejestrowania jest:

```
<{Log Level}> {Timestamp} {Message Text}
```

`{Log Level}` powinien być zgodny z [formatem poziomu ważności dziennika](https://wikipedia.org/wiki/Syslog#Severity_level) systemu i `{Timestamp}` powinien być sformatowany jako `yyyy-mm-dd hh:mm:ss.fff zzz` .

[Klasa rejestratora w IoT Edge](https://github.com/Azure/iotedge/blob/master/edge-util/src/Microsoft.Azure.Devices.Edge.Util/Logger.cs) służy jako implementacja kanoniczna.

## <a name="retrieve-module-logs"></a>Pobieranie dzienników modułu

Użyj metody **GetModuleLogs** Direct, aby pobrać dzienniki modułu IoT Edge.

Ta metoda akceptuje ładunek JSON z następującym schematem:

```json
    {
       "schemaVersion": "1.0",
       "items": [
          {
             "id": "regex string",
             "filter": {
                "tail": "int",
                "since": "int",
                "until": "int",
                "loglevel": "int",
                "regex": "regex string"
             }
          }
       ],
       "encoding": "gzip/none",
       "contentType": "json/text" 
    }
```

| Nazwa | Typ | Opis |
|-|-|-|
| schemaVersion | ciąg | Ustaw wartość `1.0` |
| produktów | Tablica JSON | Tablica z `id` `filter` kolekcjami i. |
| ID | ciąg | Wyrażenie regularne, które dostarcza nazwę modułu. Może on być zgodny z wieloma modułami na urządzeniu brzegowym. Oczekiwano formatu [wyrażeń regularnych programu .NET](/dotnet/standard/base-types/regular-expressions) . |
| filter | Sekcja JSON | Filtry dzienników do zastosowania do modułów pasujących do `id` wyrażenia regularnego w spójnej kolekcji. |
| drugorzędn | liczba całkowita | Liczba wierszy dziennika w przeszłości do pobrania od najnowszych. Obowiązkowe. |
| Fire | liczba całkowita | Zwracaj dzienniki tylko od tego czasu, jako czas trwania (1 d, 90 m, 2 dni 3 godziny), sygnatury czasowej rfc3339 lub sygnatury czasowej systemu UNIX.  Jeśli oba `tail` i `since` są określone, dzienniki są pobierane przy użyciu `since` wartości pierwszej. Następnie `tail` wartość zostanie zastosowana do wyniku, a końcowy wynik jest zwracany. Obowiązkowe. |
| Zanim | liczba całkowita | Zwróć tylko dzienniki przed określonym czasem, jako sygnatura czasowa rfc3339, sygnatura czasowa systemu UNIX lub czas trwania (1 d, 90 m, 2 dni 3 godz.). Obowiązkowe. |
| poziom dziennika | liczba całkowita | Filtruj wiersze dziennika mniejsze niż lub równe określonemu poziomowi dziennika. Wiersze dziennika powinny być zgodne z zalecanym formatem rejestrowania i korzystać ze standardowego [poziomu ważności dziennika](https://en.wikipedia.org/wiki/Syslog#Severity_level) systemowego. Obowiązkowe. |
| wyrażeń | ciąg | Filtruj wiersze dziennika, które mają zawartość zgodną z określonym wyrażeniem regularnym przy użyciu formatu [wyrażeń regularnych programu .NET](/dotnet/standard/base-types/regular-expressions) . Obowiązkowe. |
| encoding | ciąg | Wartość `gzip` lub `none`. Wartość domyślna to `none`. |
| contentType | ciąg | Wartość `json` lub `text`. Wartość domyślna to `text`. |

> [!NOTE]
> Jeśli zawartość dzienników przekracza limit rozmiaru odpowiedzi metod bezpośrednich, która jest obecnie 128 KB, odpowiedź zwróci błąd.

Pomyślne pobranie dzienników zwraca wartość **"status": 200** , a następnie ładunek zawierający dzienniki pobrane z modułu, filtrowane według ustawień określonych w żądaniu.

Przykład:

```azurecli
az iot hub invoke-module-method --method-name 'GetModuleLogs' -n <hub name> -d <device id> -m '$edgeAgent' --method-payload \
'
    {
       "schemaVersion": "1.0",
       "items": [
          {
             "id": "edgeAgent",
             "filter": {
                "tail": 10
             }
          }
       ],
       "encoding": "none",
       "contentType": "text"
    }
'
```

W Azure Portal Wywołaj metodę z nazwą metody `GetModuleLogs` i następującym ŁADUNKIEM JSON:

```json
    {
       "schemaVersion": "1.0",
       "items": [
          {
             "id": "edgeAgent",
             "filter": {
                "tail": 10
             }
          }
       ],
       "encoding": "none",
       "contentType": "text"
    }
```

![Wywołaj metodę bezpośrednią "GetModuleLogs" w Azure Portal](./media/how-to-retrieve-iot-edge-logs/invoke-get-module-logs.png)

Możesz również przekazać dane wyjściowe interfejsu wiersza polecenia do narzędzi systemu Linux, takich jak [gzip](https://en.wikipedia.org/wiki/Gzip), do przetwarzania skompresowanej odpowiedzi. Przykład:

```azurecli
az iot hub invoke-module-method \
  --method-name 'GetModuleLogs' \
  -n <hub name> \
  -d <device id> \
  -m '$edgeAgent' \
  --method-payload '{"contentType": "text","schemaVersion": "1.0","encoding": "gzip","items": [{"id": "edgeHub","filter": {"since": "2d","tail": 1000}}],}' \
  -o tsv --query 'payload[0].payloadBytes' \
  | base64 --decode \
  | gzip -d
```

## <a name="upload-module-logs"></a>Przekazywanie dzienników modułu

Użyj metody **UploadModuleLogs** Direct do wysłania żądanych dzienników do określonego kontenera BLOB Storage platformy Azure.

<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"

> [!NOTE]
> Jeśli chcesz przekazać dzienniki z urządzenia za urządzeniem bramy, musisz mieć skonfigurowane [moduły proxy interfejsu API i usługi BLOB Storage](how-to-configure-api-proxy-module.md) na urządzeniu warstwy najwyższego poziomu. Te moduły kierują dzienniki z urządzenia warstwy niższej za pośrednictwem urządzenia bramy do magazynu w chmurze.

::: moniker-end

Ta metoda akceptuje ładunek JSON podobny do **GetModuleLogs**, z dodaniem klucza "adresie sasurl":

```json
    {
       "schemaVersion": "1.0",
       "sasUrl": "Full path to SAS URL",
       "items": [
          {
             "id": "regex string",
             "filter": {
                "tail": "int",
                "since": "int",
                "until": "int",
                "loglevel": "int",
                "regex": "regex string"
             }
          }
       ],
       "encoding": "gzip/none",
       "contentType": "json/text" 
    }
```

| Nazwa | Typ | Opis |
|-|-|-|
| Adresie sasurl | ciąg (URI) | [Adres URL sygnatury dostępu współdzielonego z dostępem do zapisu do kontenera usługi Azure Blob Storage](/archive/blogs/jpsanders/easily-create-a-sas-to-download-a-file-from-azure-storage-using-azure-storage-explorer). |

Pomyślne żądanie przekazania dzienników zwraca **"status": 200** , po którym następuje ładunek z następującym schematem:

```json
    {
        "status": "string",
        "message": "string",
        "correlationId": "GUID"
    }
```

| Nazwa | Typ | Opis |
|-|-|-|
| status | ciąg | Jeden z `NotStarted` , `Running` , `Completed` , `Failed` lub `Unknown` . |
| message | ciąg | Komunikat, jeśli wystąpi błąd, pusty ciąg w przeciwnym razie. |
| correlationId | ciąg   | Identyfikator do wysłania zapytania do stanu żądania przekazywania. |

Przykład:

Następujące wywołanie umożliwia przekazanie ostatnich wierszy dziennika 100 ze wszystkich modułów w formacie skompresowanym JSON:

```azurecli
az iot hub invoke-module-method --method-name UploadModuleLogs -n <hub name> -d <device id> -m \$edgeAgent --method-payload \
'
    {
        "schemaVersion": "1.0",
        "sasUrl": "<sasUrl>",
        "items": [
            {
                "id": ".*",
                "filter": {
                    "tail": 100
                }
            }
        ],
        "encoding": "gzip",
        "contentType": "json"
    }
'
```

Następujące wywołanie przekazanie ostatnich wierszy dziennika 100 z edgeAgent i edgeHub z ostatnimi wierszami dziennika 1000 z modułu tempSensor w nieskompresowanym formacie tekstowym:

```azurecli
az iot hub invoke-module-method --method-name UploadModuleLogs -n <hub name> -d <device id> -m \$edgeAgent --method-payload \
'
    {
        "schemaVersion": "1.0",
        "sasUrl": "<sasUrl>",
        "items": [
            {
                "id": "edge",
                "filter": {
                    "tail": 100
                }
            },
            {
                "id": "tempSensor",
                "filter": {
                    "tail": 1000
                }
            }
        ],
        "encoding": "none",
        "contentType": "text"
    }
'
```

W Azure Portal Wywołaj metodę z nazwą metody `UploadModuleLogs` i następującym ŁADUNKIEM JSON po zapełnieniu adresie sasurl informacjami:

```json
    {
       "schemaVersion": "1.0",
       "sasUrl": "<sasUrl>",
       "items": [
          {
             "id": "edgeAgent",
             "filter": {
                "tail": 10
             }
          }
       ],
       "encoding": "none",
       "contentType": "text"
    }
```

![Wywołaj metodę bezpośrednią "UploadModuleLogs" w Azure Portal](./media/how-to-retrieve-iot-edge-logs/invoke-upload-module-logs.png)

## <a name="upload-support-bundle-diagnostics"></a>Diagnostyka pakietu obsługi przekazywania

Użyj metody **UploadSupportBundle** Direct, aby powiązać i przekazać plik zip dzienników modułów IoT Edge do dostępnego kontenera BLOB Storage platformy Azure. Ta metoda bezpośrednia uruchamia [`iotedge support-bundle`](./troubleshoot.md#gather-debug-information-with-support-bundle-command) polecenie na urządzeniu IoT Edge, aby uzyskać dzienniki.

<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"

> [!NOTE]
> Jeśli chcesz przekazać dzienniki z urządzenia za urządzeniem bramy, musisz mieć skonfigurowane [moduły proxy interfejsu API i usługi BLOB Storage](how-to-configure-api-proxy-module.md) na urządzeniu warstwy najwyższego poziomu. Te moduły kierują dzienniki z urządzenia warstwy niższej za pośrednictwem urządzenia bramy do magazynu w chmurze.

::: moniker-end

Ta metoda akceptuje ładunek JSON z następującym schematem:

```json
    {
        "schemaVersion": "1.0",
        "sasUrl": "Full path to SAS url",
        "since": "2d",
        "until": "1d",
        "edgeRuntimeOnly": false
    }
```

| Nazwa | Typ | Opis |
|-|-|-|
| schemaVersion | ciąg | Ustaw wartość `1.0` |
| Adresie sasurl | ciąg (URI) | [Adres URL sygnatury dostępu współdzielonego z dostępem do zapisu do kontenera usługi Azure Blob Storage](/archive/blogs/jpsanders/easily-create-a-sas-to-download-a-file-from-azure-storage-using-azure-storage-explorer) |
| Fire | liczba całkowita | Zwracaj dzienniki tylko od tego czasu, jako czas trwania (1 d, 90 m, 2 dni 3 godziny), sygnatury czasowej rfc3339 lub sygnatury czasowej systemu UNIX. Obowiązkowe. |
| Zanim | liczba całkowita | Zwróć tylko dzienniki przed określonym czasem, jako sygnatura czasowa rfc3339, sygnatura czasowa systemu UNIX lub czas trwania (1 d, 90 m, 2 dni 3 godz.). Obowiązkowe. |
| edgeRuntimeOnly | boolean | Jeśli wartość jest równa true, tylko te dzienniki są zwracane z agenta krawędzi, koncentratora brzegowego i demona zabezpieczeń brzegowych. Wartość domyślna: false.  Obowiązkowe. |

> [!IMPORTANT]
> Pakiet pomocy technicznej IoT Edge może zawierać identyfikowalne dane osobowe.

Pomyślne żądanie przekazania dzienników zwraca **"status": 200** , po którym następuje ładunek z tym samym schematem co odpowiedź **UploadModuleLogs** :

```json
    {
        "status": "string",
        "message": "string",
        "correlationId": "GUID"
    }
```

| Nazwa | Typ | Opis |
|-|-|-|
| status | ciąg | Jeden z `NotStarted` , `Running` , `Completed` , `Failed` lub `Unknown` . |
| message | ciąg | Komunikat, jeśli wystąpi błąd, pusty ciąg w przeciwnym razie. |
| correlationId | ciąg   | Identyfikator do wysłania zapytania do stanu żądania przekazywania. |

Przykład:

```azurecli
az iot hub invoke-module-method --method-name 'UploadSupportBundle' -n <hub name> -d <device id> -m '$edgeAgent' --method-payload \
'
    {
        "schemaVersion": "1.0",
        "sasUrl": "Full path to SAS url",
        "since": "2d",
        "until": "1d",
        "edgeRuntimeOnly": false
    }
'
```

W Azure Portal Wywołaj metodę z nazwą metody `UploadSupportBundle` i następującym ŁADUNKIEM JSON po zapełnieniu adresie sasurl informacjami:

```json
    {
        "schemaVersion": "1.0",
        "sasUrl": "Full path to SAS url",
        "since": "2d",
        "until": "1d",
        "edgeRuntimeOnly": false
    }
```

![Wywołaj metodę bezpośrednią "UploadSupportBundle" w Azure Portal](./media/how-to-retrieve-iot-edge-logs/invoke-upload-support-bundle.png)

## <a name="get-upload-request-status"></a>Pobierz stan żądania przekazania

Użyj metody **GetTaskStatus** Direct, aby zbadać stan żądania dzienników przekazywania. Ładunek żądania **GetTaskStatus** używa `correlationId` żądania przekazywania dzienników do pobrania stanu zadania. `correlationId`Jest zwracany w odpowiedzi na wywołanie metody **UploadModuleLogs** Direct.

Ta metoda akceptuje ładunek JSON z następującym schematem:

```json
    {
      "schemaVersion": "1.0",
      "correlationId": "<GUID>"
    }
```

Pomyślne żądanie przekazania dzienników zwraca **"status": 200** , po którym następuje ładunek z tym samym schematem co odpowiedź **UploadModuleLogs** :

```json
    {
        "status": "string",
        "message": "string",
        "correlationId": "GUID"
    }
```

| Nazwa | Typ | Opis |
|-|-|-|
| status | ciąg | Jeden z `NotStarted` , `Running` , `Completed` , `Failed` lub `Unknown` . |
| message | ciąg | Komunikat, jeśli wystąpi błąd, pusty ciąg w przeciwnym razie. |
| correlationId | ciąg   | Identyfikator do wysłania zapytania do stanu żądania przekazywania. |

Przykład:

```azurecli
az iot hub invoke-module-method --method-name 'GetTaskStatus' -n <hub name> -d <device id> -m '$edgeAgent' --method-payload \
'
    {
      "schemaVersion": "1.0",
      "correlationId": "<GUID>"
    }
'
```

W Azure Portal Wywołaj metodę z nazwą metody `GetTaskStatus` i następującym ŁADUNKIEM JSON po zapełnieniu identyfikatora GUID informacjami:

```json
    {
      "schemaVersion": "1.0",
      "correlationId": "<GUID>"
    }
```

![Wywołaj metodę bezpośrednią "GetTaskStatus" w Azure Portal](./media/how-to-retrieve-iot-edge-logs/invoke-get-task-status.png)

## <a name="next-steps"></a>Następne kroki

[Właściwości agenta IoT Edge i modułu IoT Edge Hub bliźniaczych reprezentacji](module-edgeagent-edgehub.md)
