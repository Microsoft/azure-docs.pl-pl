---
title: Automatyczne zarządzanie urządzeniami na dużą skalę przy użyciu usługi Azure IoT Hub | Microsoft Docs
description: Korzystanie z usługi Azure IoT Hub konfiguracjami automatycznymi do zarządzania wieloma urządzeniami i modułami IoT
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: robinsh
ms.custom:
- 'Role: Cloud Development'
- 'Role: IoT Device'
ms.openlocfilehash: 0e017f4df413d6db528bb99756646859d9a74aea
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92545399"
---
# <a name="automatic-iot-device-and-module-management-using-the-azure-portal"></a>Automatyczne zarządzanie urządzeniami i modułami IoT przy użyciu witryny Azure Portal

[!INCLUDE [iot-edge-how-to-deploy-monitor-selector](../../includes/iot-hub-auto-device-config-selector.md)]

Automatyczne zarządzanie urządzeniami w systemie Azure IoT Hub automatyzuje wiele powtarzających się i złożonych zadań związanych z zarządzaniem dużymi flotami urządzeń. Za pomocą funkcji automatycznego zarządzania urządzeniami można kierować zestawem urządzeń na podstawie ich właściwości, definiować żądaną konfigurację, a następnie zezwalać IoT Hub na aktualizowanie urządzeń w zakresie. Ta aktualizacja jest wykonywana przy użyciu _automatycznej konfiguracji urządzenia_ lub _automatycznej konfiguracji modułu_, która umożliwia podsumowywanie i zgodność, obsługę scalania i konfliktów oraz wdrażanie konfiguracji w ramach podejścia etapowego.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Automatyczne zarządzanie urządzeniami działa przez aktualizację zestawu bliźniaczych reprezentacji urządzeń lub bliźniaczych reprezentacji modułu z żądanymi właściwościami oraz raportowanie podsumowania opartego na raportowanych właściwościach.  Wprowadza nową klasę i dokument JSON o nazwie *Konfiguracja* , która ma trzy części:

* **Warunek docelowy** definiuje zakres bliźniaczych reprezentacji urządzeń lub bliźniaczych reprezentacji modułu do zaktualizowania. Warunek docelowy jest określony jako zapytanie dla tagów bliźniaczych i/lub zgłoszonych właściwości.

* **Zawartość docelowa** definiuje żądane właściwości, które mają zostać dodane lub zaktualizowane w docelowym urządzeniu bliźniaczych reprezentacji lub bliźniaczych reprezentacji modułu. Zawartość zawiera ścieżkę do sekcji żądanych właściwości, które mają zostać zmienione.

* **Metryki** definiują podsumowania różnych stanów konfiguracji, takich jak **sukces**, **w toku** i **błąd**. Metryki niestandardowe są określane jako zapytania dotyczące właściwości zgłaszanych przez sznurek.  Metryki systemu są domyślnymi metrykami, które mierzą stan aktualizacji bliźniaczych, takich jak liczba bliźniaczych reprezentacji, które są przeznaczone do użycia i liczba pomyślnie zaktualizowanych bliźniaczych reprezentacji.

Automatyczne konfiguracje są uruchamiane po raz pierwszy wkrótce po utworzeniu konfiguracji, a następnie co pięć minut. Zapytania metryk są uruchamiane przy każdym uruchomieniu automatycznej konfiguracji.

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

## <a name="create-a-configuration"></a>Utwórz konfigurację

1. W [Azure Portal](https://portal.azure.com)przejdź do centrum IoT Hub. 

2. Wybierz pozycję **Konfiguracja urządzenia IoT**.

3. Wybierz pozycję **Dodaj konfigurację urządzenia** lub **Dodaj konfigurację modułu**.

   ![Dodaj konfigurację urządzenia lub konfigurację modułu](./media/iot-hub-automatic-device-management/create-automatic-configuration.png)

Istnieje pięć kroków, które należy wykonać, aby utworzyć konfigurację. W poniższych sekcjach omówiono każdy z nich. 

### <a name="name-and-label"></a>Nazwa i etykieta

1. Nadaj swojej konfiguracji unikatową nazwę, która jest maksymalnie 128 małymi literami. Unikaj spacji i następujących nieprawidłowych znaków: `& ^ [ ] { } \ | " < > /` .

2. Dodaj etykiety, aby pomóc w śledzeniu konfiguracji. Etykiety to **nazwy** i pary **wartości** opisujące konfigurację. Na przykład: `HostPlatform, Linux` lub `Version, 3.0.1`.

3. Wybierz przycisk **dalej** , aby przejść do następnego kroku. 

### <a name="specify-settings"></a>Określ ustawienia

W tej sekcji zdefiniowano zawartość, która ma zostać ustawiona w bliźniaczych reprezentacji na urządzeniu lub module. Dla każdego zestawu ustawień istnieją dwa dane wejściowe. Pierwsza to ścieżka z dwuosiową, która jest ścieżką do sekcji JSON w obszarze właściwości, które zostaną ustawione.  Drugim jest zawartość JSON, która ma zostać wstawiona w tej sekcji. 

Na przykład można ustawić ścieżkę bliźniaczy na, `properties.desired.chiller-water` a następnie podać następującą zawartość JSON: 

```json
{
  "temperature": 66,
  "pressure": 28
}
```

![Ustawianie ścieżki i zawartości sznurka](./media/iot-hub-automatic-device-management/module-config-twin-settings.png)


Możesz również ustawić poszczególne ustawienia, określając całą ścieżkę bliźniaczy i dostarczając wartość bez nawiasów. Na przykład ze ścieżką ze znakiem bliźniaczym `properties.desired.chiller-water.temperature` Ustaw zawartość na `66` . Następnie utwórz nowe ustawienie sznurka dla właściwości ciśnienie. 

Jeśli co najmniej dwie konfiguracje mają tę samą ścieżkę jednoosiową, zostanie zastosowana zawartość z konfiguracji o najwyższym priorytecie (priorytet jest zdefiniowany w kroku 4).

Jeśli chcesz usunąć istniejącą właściwość, określ wartość właściwości `null` .

Możesz dodać dodatkowe ustawienia, wybierając pozycję **Dodaj ustawienie sznurka urządzenia** lub **Dodaj ustawienie sznurka modułu**.

### <a name="specify-metrics-optional"></a>Określ metryki (opcjonalnie)

Metryki zawierają podsumowanie liczb różnych stanów, które urządzenie lub moduł może zgłosić po zastosowaniu zawartości konfiguracyjnej. Można na przykład utworzyć metrykę dla oczekujących zmian ustawień, metrykę dla błędów oraz metrykę dla pomyślnych zmian ustawień.

Każda konfiguracja może mieć maksymalnie pięć metryk niestandardowych. 

1. Wprowadź nazwę dla **nazwy metryki**.

2. Wprowadź zapytanie dla **kryteriów metryki**.  Zapytanie jest oparte na raportowanych właściwościach urządzenia.  Metryka reprezentuje liczbę wierszy zwracanych przez zapytanie.

Na przykład:

```sql
SELECT deviceId FROM devices 
  WHERE properties.reported.chillerWaterSettings.status='pending'
```

Można uwzględnić klauzulę, która została zastosowana do konfiguracji, na przykład: 

```sql
/* Include the double brackets. */
SELECT deviceId FROM devices 
  WHERE configurations.[[yourconfigname]].status='Applied'
```

Jeśli tworzysz metrykę do raportowania w skonfigurowanych modułach, wybierz pozycję `moduleId` z `devices.modules` . Na przykład:

```sql
SELECT deviceId, moduleId FROM devices.modules
  WHERE properties.reported.lastDesiredStatus.code = 200
```

### <a name="target-devices"></a>Urządzenia docelowe

Użyj właściwości Tags z bliźniaczych reprezentacji, aby określić urządzenia i moduły, które powinny otrzymać tę konfigurację. Możliwe jest również ukierunkowanie na wyraportowane właściwości.

Automatyczne konfiguracje urządzeń mogą dotyczyć tylko znaczników bliźniaczych urządzeń, a automatyczne konfiguracje modułów mogą jedynie wskazywać znaczniki bliźniaczych modułów. 

Ponieważ wiele konfiguracji może wskazywać na to samo urządzenie lub moduł, każda konfiguracja wymaga numeru priorytetu. Jeśli kiedykolwiek wystąpił konflikt, konfiguracja z najwyższym priorytetem usługi WINS. 

1. Wprowadź dodatnią liczbę całkowitą dla **priorytetu** konfiguracji. Największa wartość liczbowa jest uznawana za najwyższy priorytet. Jeśli dwie konfiguracje mają taki sam numer priorytetu, ten, który został utworzony ostatnio w usłudze WINS. 

2. Wprowadź **warunek docelowy** , aby określić, które urządzenia lub moduły mają być ukierunkowane na tę konfigurację. Warunek jest oparty na znacznikach bliźniaczych lub wyraportowanych właściwościach i powinien być zgodny z formatem wyrażenia. 

   Aby skonfigurować automatyczną konfigurację urządzenia, można określić tylko tag lub zgłoszoną właściwość jako element docelowy. Na przykład: `tags.environment='test'` lub `properties.reported.chillerProperties.model='4000x'`. Możesz określić, `*` Aby wskazać wszystkie urządzenia jako docelowe. 
   
   W celu automatycznego konfigurowania modułów należy użyć zapytania, aby określić Tagi lub zgłoszone właściwości z modułów zarejestrowanych w centrum IoT Hub. Na przykład: `from devices.modules where tags.environment='test'` lub `from devices.modules where properties.reported.chillerProperties.model='4000x'`. Symbol wieloznaczny nie może być używany jako element docelowy wszystkich modułów. 

3. Wybierz przycisk **dalej** , aby przejść do ostatniego kroku.

### <a name="review-configuration"></a>Przegląd konfiguracji

Przejrzyj informacje o konfiguracji, a następnie wybierz pozycję **Prześlij**.

## <a name="monitor-a-configuration"></a>Monitoruj konfigurację

Aby wyświetlić szczegóły konfiguracji i monitorować urządzenia, na których działa, wykonaj następujące czynności:

1. W [Azure Portal](https://portal.azure.com)przejdź do centrum IoT Hub. 

2. Wybierz pozycję **Konfiguracja urządzenia IoT**.

3. Sprawdź listę konfiguracji. Dla każdej konfiguracji można wyświetlić następujące informacje:

   * **Identyfikator** — nazwa konfiguracji.

   * **Warunek docelowy** — zapytanie używane do definiowania docelowych urządzeń lub modułów.

   * **Priorytet** — numer priorytetu przypisany do konfiguracji.

   * **Czas utworzenia** — sygnatura czasowa, z której została utworzona konfiguracja. Ta sygnatura czasowa jest używana do przerwania powiązań, gdy dwie konfiguracje mają ten sam priorytet. 

   * **Metryki systemu** — metryki obliczane przez IoT Hub i nie mogą być dostosowywane przez deweloperów. Docelowy określa liczbę bliźniaczych reprezentacji urządzeń, które pasują do warunku docelowego. Stosuje określoną liczbę bliźniaczych reprezentacji urządzeń, które zostały zmodyfikowane przez konfigurację, które mogą uwzględniać częściowe modyfikacje w przypadku, gdy osobna konfiguracja o wyższym priorytecie również wprowadziła zmiany. 

   * **Metryki niestandardowe** — metryki, które zostały określone przez dewelopera jako zapytania względem raportowanych właściwości.  Na konfigurację można zdefiniować maksymalnie pięć metryk niestandardowych. 
   
4. Wybierz konfigurację, która ma być monitorowana.  

5. Sprawdź szczegóły konfiguracji. Za pomocą kart można wyświetlić szczegółowe informacje o urządzeniach, które otrzymały konfigurację.

   * **Warunek docelowy** — urządzenia lub moduły, które pasują do warunku docelowego. 

   * **Metryki** — lista metryk systemowych i metryki niestandardowe.  Można wyświetlić listę urządzeń lub modułów, które są zliczane dla każdej metryki, wybierając metrykę z listy rozwijanej, a następnie wybierając pozycję **Wyświetl urządzenia** lub **Widok moduły**.

   * Ustawienia **sznurka urządzenia** lub **Ustawienia sznurka modułu** — ustawienia dwuosiowe ustawiane przez konfigurację. 

   * **Etykiety konfiguracji** — pary klucz-wartość używane do opisywania konfiguracji.  Etykiety nie mają wpływu na funkcjonalność. 

## <a name="modify-a-configuration"></a>Modyfikowanie konfiguracji

Po zmodyfikowaniu konfiguracji zmiany są natychmiast replikowane do wszystkich urządzeń lub modułów objętych usługą. 

W przypadku zaktualizowania warunku docelowego następujące aktualizacje są wykonywane:

* Jeśli dwuosiowy nie spełnił starego warunku docelowego, ale spełnia nowy warunek docelowy, a ta konfiguracja jest najwyższy priorytetem dla tej dwuosiowej, ta konfiguracja zostanie zastosowana. 

* Jeśli dwuosiowy, na którym uruchomiona jest ta konfiguracja, już nie spełnia warunku docelowego, ustawienia z konfiguracji zostaną usunięte, a dwuosiowy zostanie zmodyfikowany przez następną konfigurację o najwyższym priorytecie. 

* Jeśli jeden z tych konfiguracji nie spełnia już warunku docelowego i nie spełnia warunku docelowego innych konfiguracji, ustawienia z konfiguracji zostaną usunięte i żadne inne zmiany nie zostaną wprowadzone na przędze. 

Aby zmodyfikować konfigurację, wykonaj następujące czynności: 

1. W [Azure Portal](https://portal.azure.com)przejdź do centrum IoT Hub. 

2. Wybierz pozycję **Konfiguracja urządzenia IoT**. 

3. Wybierz konfigurację, którą chcesz zmodyfikować. 

4. Wprowadź aktualizacje następujących pól: 

   * Warunek docelowy 
   * Etykiety 
   * Priorytet 
   * Metryki

4. Wybierz pozycję **Zapisz**.

5. Wykonaj kroki opisane w sekcji [monitorowanie konfiguracji](#monitor-a-configuration) , aby obejrzeć zmiany. 

## <a name="delete-a-configuration"></a>Usuń konfigurację

Po usunięciu konfiguracji wszystkie urządzenia bliźniaczych reprezentacjią swoją konfigurację o najwyższym priorytecie. Jeśli bliźniaczych reprezentacji urządzeń nie spełnia warunku docelowego żadnej innej konfiguracji, nie są stosowane żadne inne ustawienia. 

1. W [Azure Portal](https://portal.azure.com)przejdź do centrum IoT Hub. 

2. Wybierz pozycję **Konfiguracja urządzenia IoT**. 

3. Użyj pola wyboru, aby wybrać konfigurację, która ma zostać usunięta. 

4. Wybierz pozycję **Usuń**.

5. Zostanie wyświetlony monit z prośbą o potwierdzenie.

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób konfigurowania i monitorowania urządzeń IoT na dużą skalę. Skorzystaj z poniższych linków, aby dowiedzieć się więcej o zarządzaniu usługą Azure IoT Hub:

* [Zbiorcze zarządzanie tożsamościami urządzeń usługi IoT Hub](iot-hub-bulk-identity-mgmt.md)
* [Monitorowanie Centrum IoT Hub](monitor-iot-hub.md)

Aby dowiedzieć się więcej o możliwościach IoT Hub, zobacz:

* [Przewodnik dla deweloperów IoT Hub](iot-hub-devguide.md)
* [Wdrażanie rozwiązań SI na urządzeniach brzegowych przy użyciu usługi Azure IoT Edge](../iot-edge/quickstart-linux.md)

Aby poznać korzystanie z IoT Hub Device Provisioning Service w celu włączenia funkcji bezobsługowego, zainicjowania obsługi just-in-Time, zobacz: 

* [Usługa Azure IoT Hub Device Provisioning](../iot-dps/index.yml)