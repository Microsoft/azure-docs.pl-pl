---
title: Automatyczne zarządzanie urządzeniami na dużą skalę za pomocą usługi Azure IoT Hub (CLI) | Microsoft Docs
description: Korzystanie z usługi Azure IoT Hub konfiguracjami automatycznymi do zarządzania wieloma urządzeniami lub modułami IoT
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: robinsh
ms.openlocfilehash: 0b8b499613f8234f449e6d72f6ed6ec1f2f21287
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92545416"
---
# <a name="automatic-iot-device-and-module-management-using-the-azure-cli"></a>Automatyczne zarządzanie urządzeniami i modułami IoT przy użyciu interfejsu wiersza polecenia platformy Azure

[!INCLUDE [iot-edge-how-to-deploy-monitor-selector](../../includes/iot-hub-auto-device-config-selector.md)]

Automatyczne zarządzanie urządzeniami w systemie Azure IoT Hub automatyzuje wiele powtarzających się i złożonych zadań związanych z zarządzaniem dużymi flotami urządzeń. Za pomocą funkcji automatycznego zarządzania urządzeniami można kierować zestawem urządzeń na podstawie ich właściwości, definiować żądaną konfigurację, a następnie zezwalać IoT Hub na aktualizowanie urządzeń w zakresie. Ta aktualizacja jest wykonywana przy użyciu _automatycznej konfiguracji urządzenia_ lub _automatycznej konfiguracji modułu_, która umożliwia podsumowywanie i zgodność, obsługę scalania i konfliktów oraz wdrażanie konfiguracji w ramach podejścia etapowego.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Automatyczne zarządzanie urządzeniami działa przez aktualizację zestawu bliźniaczych reprezentacji urządzeń lub bliźniaczych reprezentacji modułu z żądanymi właściwościami oraz raportowanie podsumowania opartego na raportowanych właściwościach.  Wprowadza nową klasę i dokument JSON o nazwie *Konfiguracja* , która ma trzy części:

* **Warunek docelowy** definiuje zakres bliźniaczych reprezentacji urządzeń lub bliźniaczych reprezentacji modułu do zaktualizowania. Warunek docelowy jest określony jako zapytanie dotyczące tagów sznurka urządzenia i/lub zgłoszonych właściwości.

* **Zawartość docelowa** definiuje żądane właściwości, które mają zostać dodane lub zaktualizowane w docelowym urządzeniu bliźniaczych reprezentacji lub bliźniaczych reprezentacji modułu. Zawartość zawiera ścieżkę do sekcji żądanych właściwości, które mają zostać zmienione.

* **Metryki** definiują podsumowania różnych stanów konfiguracji, takich jak **sukces**, **w toku** i **błąd**. Metryki niestandardowe są określane jako zapytania dotyczące właściwości zgłaszanych przez sznurek.  Metryki systemu są domyślnymi metrykami, które mierzą stan aktualizacji bliźniaczych, takich jak liczba bliźniaczych reprezentacji, które są przeznaczone do użycia i liczba pomyślnie zaktualizowanych bliźniaczych reprezentacji.

Automatyczne konfiguracje są uruchamiane po raz pierwszy wkrótce po utworzeniu konfiguracji, a następnie co pięć minut. Zapytania metryk są uruchamiane przy każdym uruchomieniu automatycznej konfiguracji.

## <a name="cli-prerequisites"></a>Wymagania wstępne interfejsu wiersza polecenia

* [Centrum IoT](../iot-hub/iot-hub-create-using-cli.md) w ramach subskrypcji platformy Azure. 

* [Interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) w Twoim środowisku. Minimalna wersja interfejsu wiersza polecenia platformy Azure musi być 2.0.70 lub nowsza. Użyj polecenia `az –-version` w celu przeprowadzenia weryfikacji. Ta wersja obsługuje polecenia rozszerzenia az i wprowadza platformę poleceń Knack. 

* [Rozszerzenie IoT dla interfejsu wiersza polecenia platformy Azure](https://github.com/Azure/azure-cli).

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="implement-twins"></a>Implementuj bliźniaczych reprezentacji

Automatyczne konfiguracje urządzeń wymagają synchronizowania stanu między chmurą i urządzeniami przy użyciu usługi Device bliźniaczych reprezentacji.  Aby uzyskać więcej informacji, zobacz [Opis bliźniaczej reprezentacji urządzenia w usłudze IoT Hub oraz sposoby jej używania](iot-hub-devguide-device-twins.md).

Automatyczne konfiguracje modułów wymagają użycia modułu bliźniaczych reprezentacji do synchronizowania stanu między chmurą i modułami. Aby uzyskać więcej informacji, zobacz [Omówienie i Używanie modułu bliźniaczych reprezentacji w IoT Hub](iot-hub-devguide-module-twins.md).

## <a name="use-tags-to-target-twins"></a>Użyj tagów, aby docelowa bliźniaczych reprezentacji

Przed utworzeniem konfiguracji należy określić, które urządzenia lub moduły mają mieć wpływ. Usługa Azure IoT Hub identyfikuje urządzenia i używa tagów w bliźniaczych urządzeniach i identyfikuje moduły przy użyciu tagów w bliźniaczych modułach. Każde urządzenie lub moduły może mieć wiele tagów i można je zdefiniować w dowolny sposób, który ma sens dla danego rozwiązania. Na przykład Jeśli zarządzasz urządzeniami w różnych lokalizacjach, Dodaj następujące znaczniki do sznurka urządzenia:

```json
"tags": {
    "location": {
        "state": "Washington",
        "city": "Tacoma"
    }
},
```

## <a name="define-the-target-content-and-metrics"></a>Definiowanie docelowej zawartości i metryk

Docelowe zapytania o zawartość i metryki są określane jako dokumenty JSON opisujące sznurki lub właściwości sznurka urządzenia, aby ustawić i zgłosić właściwości do miary.  Aby utworzyć automatyczną konfigurację przy użyciu interfejsu wiersza polecenia platformy Azure, Zapisz zawartość docelową i metryki lokalnie jako pliki txt. Ścieżki plików są używane w dalszej części, gdy uruchamiasz polecenie, aby zastosować konfigurację na urządzeniu.

Oto podstawowa przykład zawartości docelowej dla automatycznej konfiguracji urządzenia:

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

Automatyczne konfiguracje modułów zachowują się bardzo podobnie, ale `moduleContent` zamiast `deviceContent` .

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

Oto przykłady zapytań metryk:

```json
{
  "queries": {
    "Compliant": "select deviceId from devices where configurations.[[chillerdevicesettingswashington]].status = 'Applied' AND properties.reported.chillerWaterSettings.status='current'",
    "Error": "select deviceId from devices where configurations.[[chillerdevicesettingswashington]].status = 'Applied' AND properties.reported.chillerWaterSettings.status='error'",
    "Pending": "select deviceId from devices where configurations.[[chillerdevicesettingswashington]].status = 'Applied' AND properties.reported.chillerWaterSettings.status='pending'"
  }
}
```

Zapytania dotyczące metryk dla modułów są również podobne do zapytań dotyczących urządzeń, ale wybierasz opcję `moduleId` z `devices.modules` . Na przykład: 

```json
{
  "queries": {
    "Compliant": "select deviceId, moduleId from devices.module where configurations.[[chillermodulesettingswashington]].status = 'Applied' AND properties.reported.chillerWaterSettings.status='current'"
  }
}
```

## <a name="create-a-configuration"></a>Utwórz konfigurację

Aby skonfigurować urządzenia docelowe, należy utworzyć konfigurację, która składa się z docelowej zawartości i metryk. 

Użyj następującego polecenia, aby utworzyć konfigurację:

```azurecli
   az iot hub configuration create --config-id [configuration id] \
     --labels [labels] --content [file path] --hub-name [hub name] \
     --target-condition [target query] --priority [int] \
     --metrics [metric queries]
```

* --**config-ID** — nazwa konfiguracji, która zostanie utworzona w usłudze IoT Hub. Nadaj swojej konfiguracji unikatową nazwę, która jest maksymalnie 128 małymi literami. Unikaj spacji i następujących nieprawidłowych znaków: `& ^ [ ] { } \ | " < > /` .

* --**etykiety** — Dodaj etykiety, aby pomóc w śledzeniu konfiguracji. Etykiety to nazwy i pary wartości opisujące wdrożenie. Na przykład `HostPlatform, Linux` lub `Version, 3.0.1`

* --**zawartość** w formacie JSON lub ścieżka pliku do zawartości docelowej, która ma zostać ustawiona jako wartość właściwości "bliźniaczych". 

* --**Hub-Name** -Name Centrum IoT, w którym zostanie utworzona konfiguracja. Centrum musi znajdować się w bieżącej subskrypcji. Przejdź do żądanej subskrypcji za pomocą polecenia `az account set -s [subscription name]`

* --**warunek docelowy** — wprowadź warunek docelowy, aby określić, które urządzenia lub moduły będą ukierunkowane na tę konfigurację. W przypadku automatycznej konfiguracji urządzenia warunek jest oparty na tagach sznurka urządzenia lub odpowiednich właściwościach urządzenia i powinien być zgodny z formatem wyrażenia. Na przykład: `tags.environment='test'` lub `properties.desired.devicemodel='4000x'`. W przypadku automatycznej konfiguracji modułu warunek jest oparty na tagach wieloosiowych modułu lub odpowiednich właściwościach modułu. Na przykład: `from devices.modules where tags.environment='test'` lub `from devices.modules where properties.reported.chillerProperties.model='4000x'`.

* --**priorytet** — dodatnia liczba całkowita. W przypadku, gdy co najmniej dwie konfiguracje są przeznaczone dla tego samego urządzenia lub modułu, zostanie zastosowana konfiguracja o najwyższej wartości liczbowej dla priorytetu.

* --**metryki** -FilePath do zapytań metryk. Metryki zawierają podsumowanie liczb różnych stanów, które urządzenie lub moduł może zgłosić po zastosowaniu zawartości konfiguracyjnej. Można na przykład utworzyć metrykę dla oczekujących zmian ustawień, metrykę dla błędów oraz metrykę dla pomyślnych zmian ustawień. 

## <a name="monitor-a-configuration"></a>Monitoruj konfigurację

Użyj następującego polecenia, aby wyświetlić zawartość konfiguracji:

```azurecli
az iot hub configuration show --config-id [configuration id] \
  --hub-name [hub name]
```

* --**config-ID** — nazwa konfiguracji, która istnieje w usłudze IoT Hub.

* --**Hub-Name** -Name Centrum IoT, w którym istnieje konfiguracja. Centrum musi znajdować się w bieżącej subskrypcji. Przejdź do żądanej subskrypcji za pomocą polecenia `az account set -s [subscription name]`

Sprawdź konfigurację w oknie poleceń. Właściwość **metryki** zawiera liczbę dla każdej metryki ocenianej przez poszczególne centra:

* **targetedCount** — Metryka systemowa, która określa liczbę bliźniaczych reprezentacji urządzeń lub bliźniaczych reprezentacji modułu w IoT Hub, które pasują do warunku określania wartości docelowej.

* **appliedCount** — Metryka systemowa określa liczbę urządzeń lub modułów, dla których zastosowano zawartość docelową.

* **Metryka niestandardowa** — wszystkie zdefiniowane metryki to metryki użytkowników.

Można wyświetlić listę identyfikatorów urządzeń, identyfikatorów modułów lub obiektów dla każdej z metryk przy użyciu następującego polecenia:

```azurecli
az iot hub configuration show-metric --config-id [configuration id] \
   --metric-id [metric id] --hub-name [hub name] --metric-type [type] 
```

* --**config-ID** — nazwa wdrożenia, która istnieje w usłudze IoT Hub.

* --**Metric-ID** — Nazwa metryki, dla której chcesz zobaczyć listę identyfikatorów urządzeń lub identyfikatorów modułów, na przykład `appliedCount` .

* --**Hub-Name** -Name Centrum IoT, w którym istnieje wdrożenie. Centrum musi znajdować się w bieżącej subskrypcji. Przejdź do żądanej subskrypcji za pomocą polecenia `az account set -s [subscription name]` .

* --**Typ metryki** — typ metryki może być `system` lub `user` .  Metryki systemu są `targetedCount` i `appliedCount` . Wszystkie inne metryki są metrykami użytkownika.

## <a name="modify-a-configuration"></a>Modyfikowanie konfiguracji

Po zmodyfikowaniu konfiguracji zmiany są natychmiast replikowane na wszystkie urządzenia objęte usługą. 

W przypadku zaktualizowania warunku docelowego następujące aktualizacje są wykonywane:

* Jeśli dwuosiowy nie spełnił starego warunku docelowego, ale spełnia nowy warunek docelowy, a ta konfiguracja jest najwyższy priorytetem dla tej dwuosiowej, ta konfiguracja zostanie zastosowana. 

* Jeśli dwuosiowy, na którym uruchomiona jest ta konfiguracja, już nie spełnia warunku docelowego, ustawienia z konfiguracji zostaną usunięte, a dwuosiowy zostanie zmodyfikowany przez następną konfigurację o najwyższym priorytecie. 

* Jeśli jeden z tych konfiguracji nie spełnia już warunku docelowego i nie spełnia warunku docelowego innych konfiguracji, ustawienia z konfiguracji zostaną usunięte i żadne inne zmiany nie zostaną wprowadzone na przędze. 

Aby zaktualizować konfigurację, użyj następującego polecenia:

```azurecli
az iot hub configuration update --config-id [configuration id] \
   --hub-name [hub name] --set [property1.property2='value']
```

* --**config-ID** — nazwa konfiguracji, która istnieje w usłudze IoT Hub.

* --**Hub-Name** -Name Centrum IoT, w którym istnieje konfiguracja. Centrum musi znajdować się w bieżącej subskrypcji. Przejdź do żądanej subskrypcji za pomocą polecenia `az account set -s [subscription name]` .

* --**Ustaw** — zaktualizuj właściwość w konfiguracji. Można zaktualizować następujące właściwości:

    * targetCondition — na przykład `targetCondition=tags.location.state='Oregon'`

    * Etykieta 

    * priority

## <a name="delete-a-configuration"></a>Usuń konfigurację

Usunięcie konfiguracji powoduje, że wszystkie urządzenia bliźniaczych reprezentacji lub bliźniaczych reprezentacji modułu przyjmują następną konfigurację o najwyższym priorytecie. Jeśli bliźniaczych reprezentacji nie spełnia warunku docelowego żadnej innej konfiguracji, nie są stosowane żadne inne ustawienia. 

Aby usunąć konfigurację, użyj następującego polecenia:

```azurecli
az iot hub configuration delete --config-id [configuration id] \
   --hub-name [hub name] 
```

* --**config-ID** — nazwa konfiguracji, która istnieje w usłudze IoT Hub.

* --**Hub-Name** -Name Centrum IoT, w którym istnieje konfiguracja. Centrum musi znajdować się w bieżącej subskrypcji. Przejdź do żądanej subskrypcji za pomocą polecenia `az account set -s [subscription name]` .

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób konfigurowania i monitorowania urządzeń IoT na dużą skalę. Skorzystaj z poniższych linków, aby dowiedzieć się więcej o zarządzaniu usługą Azure IoT Hub:

* [Zbiorcze zarządzanie tożsamościami urządzeń usługi IoT Hub](iot-hub-bulk-identity-mgmt.md)
* [Monitorowanie Centrum IoT Hub](monitor-iot-hub.md)

Aby dowiedzieć się więcej o możliwościach IoT Hub, zobacz:

* [Przewodnik dla deweloperów IoT Hub](iot-hub-devguide.md)
* [Wdrażanie rozwiązań SI na urządzeniach brzegowych przy użyciu usługi Azure IoT Edge](../iot-edge/quickstart-linux.md)

Aby poznać korzystanie z IoT Hub Device Provisioning Service w celu włączenia funkcji bezobsługowego, zainicjowania obsługi just-in-Time, zobacz: 

* [Usługa Azure IoT Hub Device Provisioning](../iot-dps/index.yml)