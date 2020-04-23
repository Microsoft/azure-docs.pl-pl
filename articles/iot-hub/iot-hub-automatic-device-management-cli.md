---
title: Automatyczne zarządzanie urządzeniami na dużą skalę za pomocą usługi Azure IoT Hub (CLI) | Dokumenty firmy Microsoft
description: Używanie konfiguracji automatycznych usługi Azure IoT Hub do zarządzania wieloma urządzeniami lub modułami IoT
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: robinsh
ms.openlocfilehash: 60d0ef30a1c7d948a9e837a8bc37c76ace415545
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "82024969"
---
# <a name="automatic-iot-device-and-module-management-using-the-azure-cli"></a>Automatyczne zarządzanie urządzeniami i modułami IoT przy użyciu interfejsu wiersza polecenia platformy Azure

[!INCLUDE [iot-edge-how-to-deploy-monitor-selector](../../includes/iot-hub-auto-device-config-selector.md)]

Automatyczne zarządzanie urządzeniami w usłudze Azure IoT Hub automatyzuje wiele powtarzających się i złożonych zadań związanych z zarządzaniem flotami dużych urządzeń. Dzięki automatycznemu zarządzaniu urządzeniami można kierować zestaw urządzeń na podstawie ich właściwości, definiować żądaną konfigurację, a następnie pozwolić Centrum IoT na aktualizowanie urządzeń, gdy wchodzą w zakres. Ta aktualizacja jest wykonywana przy użyciu _automatycznej konfiguracji urządzenia_ lub _automatycznej konfiguracji modułu,_ która pozwala podsumować zakończenie i zgodność, obsługiwać scalanie i konflikty oraz wdrażać konfiguracje w podejściu etapowym.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Automatyczne zarządzanie urządzeniami działa przez aktualizowanie zestawu bliźniaczych reprezentacji urządzeń lub bliźniaczych reprezentacji modułów z żądanymi właściwościami i raportowanie podsumowania opartego na bliźniaczych zgłoszonych właściwościach.  Wprowadza nową klasę i dokument JSON o nazwie *Configuration,* który składa się z trzech części:

* **Warunek docelowy** definiuje zakres bliźniacze urządzenia lub bliźniacze moduły bliźniacze do aktualizacji. Warunek docelowy jest określony jako kwerenda na bliźniaczej reprezentacji urządzenia tagów i/lub zgłoszonych właściwości.

* **Zawartość docelowa** definiuje żądane właściwości, które mają zostać dodane lub zaktualizowane w bliźniacze urządzenia docelowe lub bliźniacze moduły bliźniacze. Zawartość zawiera ścieżkę do sekcji żądanych właściwości, które mają zostać zmienione.

* **Metryki definiują** sumę podsumowań różnych stanów konfiguracji, takich jak **Sukces**, **W toku**i **Błąd**. Metryki niestandardowe są określone jako zapytania dotyczące właściwości zgłoszonych bliźniaczej reprezentacji.  Metryki systemu są domyślne metryki, które mierzą stan podwójnej aktualizacji, takich jak liczba bliźniaczych reprezentacji, które są kierowane i liczba bliźniaczych reprezentacji, które zostały pomyślnie zaktualizowane.

Konfiguracje automatyczne są uruchamiane po raz pierwszy krótko po utworzeniu konfiguracji, a następnie w odstępach pięciu minut. Zapytania metryk są uruchamiane przy każdym uruchomieniu konfiguracji automatycznej.

## <a name="cli-prerequisites"></a>Wymagania wstępne interfejsu wiersza polecenia

* [Centrum IoT w](../iot-hub/iot-hub-create-using-cli.md) subskrypcji platformy Azure. 

* [Narzędzie CLI platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) w twoim środowisku. Co najmniej wersja interfejsu wiersza polecenia platformy Azure musi mieć wartość 2.0.70 lub nowszy. Użyj polecenia `az –-version` w celu przeprowadzenia weryfikacji. Ta wersja obsługuje polecenia rozszerzenia az i wprowadza platformę poleceń Knack. 

* [Rozszerzenie IoT dla interfejsu wiersza polecenia platformy Azure](https://github.com/Azure/azure-cli).

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="implement-twins"></a>Wdrażanie bliźniąt

Automatyczne konfiguracje urządzeń wymagają użycia bliźniąt bliźniąt urządzeń do synchronizacji stanu między chmurą a urządzeniami.  Aby uzyskać więcej informacji, zobacz [Opis bliźniaczej reprezentacji urządzenia w usłudze IoT Hub oraz sposoby jej używania](iot-hub-devguide-device-twins.md).

Automatyczne konfiguracje modułów wymagają użycia bliźniacze modułów do synchronizacji stanu między chmurą a modułami. Aby uzyskać więcej informacji, zobacz [Opis i używanie bliźniaczych modułów w Centrum IoT Hub](iot-hub-devguide-module-twins.md).

## <a name="use-tags-to-target-twins"></a>Używanie znaczników do kierowania na bliźniacze reprezentacje

Przed utworzeniem konfiguracji należy określić, na które urządzenia lub moduły mają mieć wpływ. Usługa Azure IoT Hub identyfikuje urządzenia i przy użyciu tagów w bliźniaczej reprezentacji urządzenia i identyfikuje moduły przy użyciu tagów w bliźniaczej reprezentacji modułu. Każde urządzenie lub moduły mogą mieć wiele tagów i można je zdefiniować w dowolny sposób, który ma sens dla rozwiązania. Jeśli na przykład zarządzasz urządzeniami w różnych lokalizacjach, dodaj następujące znaczniki do bliźniaczej reprezentacji urządzenia:

```json
"tags": {
    "location": {
        "state": "Washington",
        "city": "Tacoma"
    }
},
```

## <a name="define-the-target-content-and-metrics"></a>Definiowanie docelowej zawartości i danych

Docelowa zawartość i zapytania metryk są określone jako dokumenty JSON, które opisują bliźniaczej reprezentacji urządzenia lub bliźniaczej reprezentacji modułu żądane właściwości, aby ustawić i zgłosił właściwości do pomiaru.  Aby utworzyć konfigurację automatyczną przy użyciu interfejsu wiersza polecenia platformy Azure, zapisz zawartość docelową i metryki lokalnie jako pliki txt. Ścieżki plików w dalszej sekcji są używane po uruchomieniu polecenia w celu zastosowania konfiguracji do urządzenia.

Oto podstawowy przykład zawartości docelowej dla automatycznej konfiguracji urządzenia:

```json
{
  "content": {
    "deviceContent": {
      "properties.desired.chillerWaterSettings": {
        "temperature": 38,
        "pressure": 78
      }
    }
}
```

Automatyczne konfiguracje modułów zachowują się `moduleContent` bardzo podobnie, ale cel zamiast `deviceContent`.

```json
{
  "content": {
    "moduleContent": {
      "properties.desired.chillerWaterSettings": {
        "temperature": 38,
        "pressure": 78
      }
    }
}
```

Oto przykłady zapytań metrycznych:

```json
{
  "queries": {
    "Compliant": "select deviceId from devices where configurations.[[chillerdevicesettingswashington]].status = 'Applied' AND properties.reported.chillerWaterSettings.status='current'",
    "Error": "select deviceId from devices where configurations.[[chillerdevicesettingswashington]].status = 'Applied' AND properties.reported.chillerWaterSettings.status='error'",
    "Pending": "select deviceId from devices where configurations.[[chillerdevicesettingswashington]].status = 'Applied' AND properties.reported.chillerWaterSettings.status='pending'"
  }
}
```

Zapytania metryczne dla modułów są również podobne do zapytań `devices.modules`dla urządzeń, ale można wybrać z `moduleId` . Przykład: 

```json
{
  "queries": {
    "Compliant": "select deviceId, moduleId from devices.module where configurations.[[chillermodulesettingswashington]].status = 'Applied' AND properties.reported.chillerWaterSettings.status='current'"
  }
}
```

## <a name="create-a-configuration"></a>Tworzenie konfiguracji

Konfigurowanie urządzeń docelowych przez utworzenie konfiguracji, która składa się z zawartości docelowej i metryki. 

Aby utworzyć konfigurację, użyj następującego polecenia:

```azurecli
   az iot hub configuration create --config-id [configuration id] \
     --labels [labels] --content [file path] --hub-name [hub name] \
     --target-condition [target query] --priority [int] \
     --metrics [metric queries]
```

* --**config-id** — nazwa konfiguracji, która zostanie utworzona w centrum IoT Hub. Nadaj konfiguracji unikatową nazwę, która wynosi do 128 małych liter. Unikaj spacji i następujących `& ^ [ ] { } \ | " < > /`nieprawidłowych znaków: .

* --**etykiety** — dodaj etykiety, aby ułatwić śledzenie konfiguracji. Etykiety to Name, Value par, które opisują wdrożenia. Na przykład `HostPlatform, Linux` lub `Version, 3.0.1`

* --**zawartość** — wbudowana ścieżka JSON lub ścieżka pliku do zawartości docelowej, która ma być ustawiona jako dwie j.in. 

* --**nazwa koncentratora** — nazwa centrum IoT hub, w którym zostanie utworzona konfiguracja. Centrum musi znajdować się w bieżącej subskrypcji. Przełączanie do żądanej subskrypcji za pomocą polecenia`az account set -s [subscription name]`

* --**warunek docelowy** — wprowadź warunek docelowy, aby określić, które urządzenia lub moduły będą kierowane z tą konfiguracją.W przypadku automatycznej konfiguracji urządzenia warunek jest oparty na podwójnych tagach urządzenia lub bliźniaczej reprezentacji urządzenia żądanych właściwości i powinien być zgodny z formatem wyrażenia.Na przykład: `tags.environment='test'` lub `properties.desired.devicemodel='4000x'`.W przypadku automatycznej konfiguracji modułu warunek jest oparty na podwójnych znacznikach modułu lub bliźniaczej reprezentacji modułu żądanych właściwości.. Na przykład: `from devices.modules where tags.environment='test'` lub `from devices.modules where properties.reported.chillerProperties.model='4000x'`.

* --**priorytet** — dodatnia wartość całkowita. W przypadku, gdy dwie lub więcej konfiguracji są przeznaczone dla tego samego urządzenia lub modułu, zostanie zastosowana konfiguracja o najwyższej wartości liczbowej dla priorytetu.

* --**metryki** — ścieżka pliku do zapytań metrycznych. Metryki zawierają podsumowanie liczby różnych stanów, które urządzenie lub moduł może raportować po zastosowaniu zawartości konfiguracji. Na przykład można utworzyć metrykę dla oczekujących zmian ustawień, metrykę błędów i metrykę dla pomyślnych zmian ustawień. 

## <a name="monitor-a-configuration"></a>Monitorowanie konfiguracji

Użyj następującego polecenia, aby wyświetlić zawartość konfiguracji:

```azurecli
az iot hub configuration show --config-id [configuration id] \
  --hub-name [hub name]
```

* --**config-id** — nazwa konfiguracji, która istnieje w centrum IoT Hub.

* --**nazwa koncentratora** — nazwa centrum IoT hub, w którym istnieje konfiguracja. Centrum musi znajdować się w bieżącej subskrypcji. Przełączanie do żądanej subskrypcji za pomocą polecenia`az account set -s [subscription name]`

Sprawdź konfigurację w oknie polecenia.Właściwość **metryki** zawiera listę licznika dla każdej metryki, która jest oceniana przez każdego koncentratora:

* **targetedCount** - metryka systemu określająca liczbę bliźniaczych urządzeń lub bliźniaczych modułów bliźniąt w centrum IoT Hub, które odpowiadają warunkowi kierowania.

* **appliedCount** — metryka systemu określa liczbę urządzeń lub modułów, które miały zawartość docelową stosowane.

* **Metryka niestandardowa** — wszystkie zdefiniowane dane są danymi użytkownika.

Można wyświetlić listę identyfikatorów urządzeń, identyfikatorów modułów lub obiektów dla każdej z metryk, używając następującego polecenia:

```azurecli
az iot hub configuration show-metric --config-id [configuration id] \
   --metric-id [metric id] --hub-name [hub name] --metric-type [type] 
```

* --**config-id** — nazwa wdrożenia, które istnieje w centrum IoT Hub.

* --**identyfikator metryki** — nazwa metryki, dla której ma zostać wyświetlona lista identyfikatorów urządzeń `appliedCount`lub identyfikatorów modułów, na przykład .

* --**nazwa koncentratora** — nazwa centrum IoT hub, w którym istnieje wdrożenie. Centrum musi znajdować się w bieżącej subskrypcji. Przełącz się do żądanej subskrypcji za pomocą polecenia `az account set -s [subscription name]`.

* --**typ metryki** - Typ `system` metryki może być lub `user`.  Metryki systemowe są `targetedCount` i `appliedCount`. Wszystkie inne metryki są metryki użytkownika.

## <a name="modify-a-configuration"></a>Modyfikowanie konfiguracji

Podczas modyfikowania konfiguracji zmiany natychmiast replikują się na wszystkie urządzenia docelowe. 

W przypadku aktualizacji warunku docelowego wystąpią następujące aktualizacje:

* Jeśli bliźniacza reprezentacja nie spełnia starego warunku docelowego, ale spełnia nowy warunek docelowy i ta konfiguracja jest najwyższy priorytet dla tego bliźniaczego, a następnie ta konfiguracja jest stosowana. 

* Jeśli bliźniacza aktualnie uruchomiona ta konfiguracja nie spełnia już warunku docelowego, ustawienia z konfiguracji zostaną usunięte, a bliźniacza reprezentacja zostanie zmodyfikowana przez następną konfigurację o najwyższym priorytecie. 

* Jeśli bliźniacza aktualnie uruchomiona ta konfiguracja nie spełnia już warunku docelowego i nie spełnia warunku docelowego innych konfiguracji, ustawienia z konfiguracji zostaną usunięte i nie zostaną wprowadzone żadne inne zmiany w bliźniaczej reprezentacji. 

Aby zaktualizować konfigurację, użyj następującego polecenia:

```azurecli
az iot hub configuration update --config-id [configuration id] \
   --hub-name [hub name] --set [property1.property2='value']
```

* --**config-id** — nazwa konfiguracji, która istnieje w centrum IoT Hub.

* --**nazwa koncentratora** — nazwa centrum IoT hub, w którym istnieje konfiguracja. Centrum musi znajdować się w bieżącej subskrypcji. Przełącz się do żądanej subskrypcji za pomocą polecenia `az account set -s [subscription name]`.

* --**set** - Zaktualizuj właściwość w konfiguracji. Można zaktualizować następujące właściwości:

    * targetCondition - na przykład`targetCondition=tags.location.state='Oregon'`

    * Etykiety 

    * priority

## <a name="delete-a-configuration"></a>Usuwanie konfiguracji

Po usunięciu konfiguracji wszystkie bliźniacze dane bliźniacze lub bliźniacze modułów przyjmują następną konfigurację o najwyższym priorytecie. Jeśli bliźniacze nie spełniają warunku docelowego innej konfiguracji, nie są stosowane żadne inne ustawienia. 

Użyj następującego polecenia, aby usunąć konfigurację:

```azurecli
az iot hub configuration delete --config-id [configuration id] \
   --hub-name [hub name] 
```

* --**config-id** — nazwa konfiguracji, która istnieje w centrum IoT Hub.

* --**nazwa koncentratora** — nazwa centrum IoT hub, w którym istnieje konfiguracja. Centrum musi znajdować się w bieżącej subskrypcji. Przełącz się do żądanej subskrypcji za pomocą polecenia `az account set -s [subscription name]`.

## <a name="next-steps"></a>Następne kroki

W tym artykule dowiesz się, jak skonfigurować i monitorować urządzenia IoT na dużą skalę. Skorzystaj z tych łączy, aby dowiedzieć się więcej o zarządzaniu usługą Azure IoT Hub:

* [Zbiorcze zarządzanie tożsamościami urządzeń usługi IoT Hub](iot-hub-bulk-identity-mgmt.md)
* [Metryki usługi IoT Hub](iot-hub-metrics.md)
* [Monitorowanie operacji](iot-hub-operations-monitoring.md)

Aby dokładniej zbadać możliwości usługi IoT Hub, zobacz:

* [Przewodnik dla deweloperów usługi IoT Hub](iot-hub-devguide.md)
* [Wdrażanie rozwiązań SI na urządzeniach brzegowych przy użyciu usługi Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)

Aby eksplorować korzystanie z usługi inicjowania obsługi administracyjnej urządzeń usługi IoT Hub, aby włączyć obsługę obsługi administracyjnej typu "tylko w czasie" typu "zero-touch", zobacz: 

* [Usługa Azure IoT Hub Device Provisioning](/azure/iot-dps)
