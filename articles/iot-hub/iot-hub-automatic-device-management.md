---
title: Automatyczne zarządzanie urządzeniami na dużą skalę dzięki usłudze Azure IoT Hub | Dokumenty firmy Microsoft
description: Używanie konfiguracji automatycznych usługi Azure IoT Hub do zarządzania wieloma urządzeniami i modułami IoT
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: robinsh
ms.openlocfilehash: 276f115f579fbd1ab077722b220a4a0c6c571850
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "82025071"
---
# <a name="automatic-iot-device-and-module-management-using-the-azure-portal"></a>Automatyczne zarządzanie urządzeniami i modułami IoT za pomocą portalu Azure

[!INCLUDE [iot-edge-how-to-deploy-monitor-selector](../../includes/iot-hub-auto-device-config-selector.md)]

Automatyczne zarządzanie urządzeniami w usłudze Azure IoT Hub automatyzuje wiele powtarzających się i złożonych zadań związanych z zarządzaniem flotami dużych urządzeń. Dzięki automatycznemu zarządzaniu urządzeniami można kierować zestaw urządzeń na podstawie ich właściwości, definiować żądaną konfigurację, a następnie pozwolić Centrum IoT na aktualizowanie urządzeń, gdy wchodzą w zakres. Ta aktualizacja jest wykonywana przy użyciu _automatycznej konfiguracji urządzenia_ lub _automatycznej konfiguracji modułu,_ która pozwala podsumować zakończenie i zgodność, obsługiwać scalanie i konflikty oraz wdrażać konfiguracje w podejściu etapowym.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Automatyczne zarządzanie urządzeniami działa przez aktualizowanie zestawu bliźniaczych reprezentacji urządzeń lub bliźniaczych reprezentacji modułów z żądanymi właściwościami i raportowanie podsumowania opartego na bliźniaczych zgłoszonych właściwościach.  Wprowadza nową klasę i dokument JSON o nazwie *Configuration,* który składa się z trzech części:

* **Warunek docelowy** definiuje zakres bliźniacze urządzenia lub bliźniacze moduły bliźniacze do aktualizacji. Warunek docelowy jest określony jako kwerenda na tagi bliźniaczej reprezentacji i/lub zgłaszane właściwości.

* **Zawartość docelowa** definiuje żądane właściwości, które mają zostać dodane lub zaktualizowane w bliźniacze urządzenia docelowe lub bliźniacze moduły bliźniacze. Zawartość zawiera ścieżkę do sekcji żądanych właściwości, które mają zostać zmienione.

* **Metryki definiują** sumę podsumowań różnych stanów konfiguracji, takich jak **Sukces**, **W toku**i **Błąd**. Metryki niestandardowe są określone jako zapytania dotyczące właściwości zgłoszonych bliźniaczej reprezentacji.  Metryki systemu są domyślne metryki, które mierzą stan podwójnej aktualizacji, takich jak liczba bliźniaczych reprezentacji, które są kierowane i liczba bliźniaczych reprezentacji, które zostały pomyślnie zaktualizowane.

Konfiguracje automatyczne są uruchamiane po raz pierwszy krótko po utworzeniu konfiguracji, a następnie w odstępach pięciu minut. Zapytania metryk są uruchamiane przy każdym uruchomieniu konfiguracji automatycznej.

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

## <a name="create-a-configuration"></a>Tworzenie konfiguracji

1. W [witrynie Azure portal](https://portal.azure.com)przejdź do centrum IoT Hub. 

2. Wybierz **konfigurację urządzenia IoT**.

3. Wybierz **pozycję Dodaj konfigurację urządzenia** lub Dodaj **konfigurację modułu**.

   ![Dodawanie konfiguracji urządzenia lub konfiguracji modułu](./media/iot-hub-automatic-device-management/create-automatic-configuration.png)

Istnieje pięć kroków, aby utworzyć konfigurację. W poniższych sekcjach przechodzi każdy z nich. 

### <a name="name-and-label"></a>Nazwa i etykieta

1. Nadaj konfiguracji unikatową nazwę, która wynosi do 128 małych liter. Unikaj spacji i następujących `& ^ [ ] { } \ | " < > /`nieprawidłowych znaków: .

2. Dodaj etykiety, aby ułatwić śledzenie konfiguracji. Etykiety to **Nazwa**, pary **wartości** opisujące konfigurację. Na przykład: `HostPlatform, Linux` lub `Version, 3.0.1`.

3. Wybierz **przycisk Dalej,** aby przejść do następnego kroku. 

### <a name="specify-settings"></a>Określ ustawienia

W tej sekcji zdefiniowano zawartość, która ma być ustawiona w urządzeniu docelowym lub bliźniacze modułu. Istnieją dwa wejścia dla każdego zestawu ustawień. Pierwsza to ścieżka bliźniaczej reprezentacji, która jest ścieżką do sekcji JSON w obrębie bliźniaczej reprezentacji żądane właściwości, które zostaną ustawione.  Drugi to zawartość JSON, która ma zostać wstawiona w tej sekcji. 

Na przykład można ustawić ścieżkę `properties.desired.chiller-water` bliźniaczej reprezentacji, a następnie podać następującą zawartość JSON: 

```json
{
  "temperature": 66,
  "pressure": 28
}
```

![Ustawianie ścieżki bliźniaczej i zawartości](./media/iot-hub-automatic-device-management/module-config-twin-settings.png)


Można również ustawić poszczególne ustawienia, określając całą ścieżkę bliźniaczej reprezentacji i udostępniając wartość bez nawiasów. Na przykład ze ścieżką bliźniacząką `properties.desired.chiller-water.temperature`ustaw zawartość na `66`. Następnie utwórz nowe ustawienie bliźniaczej dla właściwości ciśnienia. 

Jeśli dwie lub więcej konfiguracji jest przeznaczone dla tej samej ścieżki bliźniaczej reprezentacji, zostanie zastosowana zawartość z konfiguracji o najwyższym priorytecie (priorytet jest zdefiniowany w kroku 4).

Jeśli chcesz usunąć istniejącą właściwość, określ wartość właściwości na `null`.

Możesz dodać dodatkowe ustawienia, wybierając **pozycję Dodaj ustawienie bliźniaczej reprezentacji urządzenia** lub Dodaj ustawienie **bliźniaczej reprezentacji modułu**.

### <a name="specify-metrics-optional"></a>Określanie metryk (opcjonalnie)

Metryki zawierają podsumowanie liczby różnych stanów, które urządzenie lub moduł może raportować po zastosowaniu zawartości konfiguracji. Na przykład można utworzyć metrykę dla oczekujących zmian ustawień, metrykę błędów i metrykę dla pomyślnych zmian ustawień.

Każda konfiguracja może mieć maksymalnie pięć niestandardowych metryk. 

1. Wprowadź nazwę **nazwy metryki**.

2. Wprowadź kwerendę dla **kryteriów metryk**.  Kwerenda jest oparta na bliźniaczej reprezentacji urządzenia zgłoszonych właściwości.  Metryka reprezentuje liczbę wierszy zwracanych przez kwerendę.

Przykład:

```sql
SELECT deviceId FROM devices 
  WHERE properties.reported.chillerWaterSettings.status='pending'
```

Można dołączyć klauzulę, że konfiguracja została zastosowana, na przykład: 

```sql
/* Include the double brackets. */
SELECT deviceId FROM devices 
  WHERE configurations.[[yourconfigname]].status='Applied'
```

Jeśli budujesz metrykę do raportowania skonfigurowanych `moduleId` modułów, wybierz opcję `devices.modules`. Przykład:

```sql
SELECT deviceId, moduleId FROM devices.modules
  WHERE properties.reported.lastDesiredStatus.code = 200
```

### <a name="target-devices"></a>Urządzenia docelowe

Użyj właściwości tags z bliźniąt, aby kierować określone urządzenia lub moduły, które powinny otrzymać tę konfigurację. Można również kierować bliźniaczych zgłoszonych właściwości.

Automatyczne konfiguracje urządzeń mogą kierować tylko bliźniacze tagi urządzeń, a automatyczne konfiguracje modułów mogą kierować tylko bliźniacze tagi modułu docelowego. 

Ponieważ wiele konfiguracji może być przeznaczone dla tego samego urządzenia lub modułu, każda konfiguracja wymaga numeru priorytetu. Jeśli kiedykolwiek wystąpi konflikt, wygrywa konfiguracja o najwyższym priorytecie. 

1. Wprowadź dodatnią ćdźwę całkowitą dla **priorytetu**konfiguracji . Najwyższa wartość liczbowa jest uważana za najwyższy priorytet. Jeśli dwie konfiguracje mają ten sam numer priorytetu, wygrywa ta, która została utworzona ostatnio. 

2. Wprowadź **warunek docelowy,** aby określić, które urządzenia lub moduły będą kierowane z tą konfiguracją. Warunek jest oparty na tagach bliźniaczych lub bliźniaczych zgłoszonych właściwości i powinien odpowiadać formatowi wyrażenia. 

   W przypadku konfiguracji urządzenia automatycznego można określić tylko tag lub zgłoszoną właściwość do docelowej. Na przykład: `tags.environment='test'` lub `properties.reported.chillerProperties.model='4000x'`. Można określić, aby kierować `*` reklamy na wszystkie urządzenia. 
   
   W przypadku konfiguracji modułu automatycznego należy użyć kwerendy, aby określić znaczniki lub zgłoszone właściwości z modułów zarejestrowanych w centrum IoT hub. Na przykład: `from devices.modules where tags.environment='test'` lub `from devices.modules where properties.reported.chillerProperties.model='4000x'`. Symbol wieloznaczny nie może być używany do kierowania wszystkich modułów. 

3. Wybierz **przycisk Dalej,** aby przejść do ostatniego kroku.

### <a name="review-configuration"></a>Konfiguracja recenzji

Przejrzyj informacje o konfiguracji, a następnie wybierz pozycję **Prześlij**.

## <a name="monitor-a-configuration"></a>Monitorowanie konfiguracji

Aby wyświetlić szczegóły konfiguracji i monitorować uruchomione jej urządzenia, należy wykonać następujące czynności:

1. W [witrynie Azure portal](https://portal.azure.com)przejdź do centrum IoT Hub. 

2. Wybierz **konfigurację urządzenia IoT**.

3. Sprawdź listę konfiguracji. Dla każdej konfiguracji można wyświetlić następujące szczegóły:

   * **Identyfikator** - nazwa konfiguracji.

   * **Warunek docelowy** — kwerenda używana do definiowania urządzeń lub modułów docelowych.

   * **Priorytet** — numer priorytetu przypisany do konfiguracji.

   * **Czas tworzenia** — sygnatura czasowa z momentu utworzenia konfiguracji. Ten sygnatura czasowa jest używana do przerywania powiązań, gdy dwie konfiguracje mają ten sam priorytet. 

   * **Metryki systemu** — metryki, które są obliczane przez Centrum IoT i nie mogą być dostosowywane przez deweloperów. Targeted określa liczbę bliźniacze urządzenia, które pasują do warunku docelowego. Stosuje określoną liczbę bliźniacze urządzenia, które zostały zmodyfikowane przez konfigurację, która może zawierać częściowe modyfikacje w przypadku, gdy konfiguracja o innym priorytecie również wprowadzać zmiany. 

   * **Metryki niestandardowe** — metryki, które zostały określone przez dewelopera jako zapytania względem bliźniaczych właściwości zgłoszonych.  Na konfigurację można zdefiniować maksymalnie pięć niestandardowych metryk. 
   
4. Wybierz konfigurację, którą chcesz monitorować.  

5. Sprawdź szczegóły konfiguracji. Za pomocą kart można wyświetlić szczegółowe informacje o urządzeniach, które otrzymały konfigurację.

   * **Warunek docelowy** — urządzenia lub moduły, które pasują do warunku docelowego. 

   * **Metryki** — lista metryk systemowych i niestandardowych metryk.  Można wyświetlić listę urządzeń lub modułów, które są liczone dla każdej metryki, wybierając metrykę z listy rozwijanej, a następnie wybierając **pozycję Wyświetl urządzenia** lub Wyświetl **moduły**.

   * **Ustawienia bliźniaczej reprezentacji urządzenia** lub **ustawienia bliźniaczej reprezentacji modułu** — ustawienia bliźniaczej reprezentacji, które są ustawiane przez konfigurację. 

   * **Etykiety konfiguracyjne** — pary klucz-wartość używane do opisania konfiguracji.  Etykiety nie mają wpływu na funkcjonalność. 

## <a name="modify-a-configuration"></a>Modyfikowanie konfiguracji

Podczas modyfikowania konfiguracji zmiany natychmiast replikują się na wszystkie urządzenia docelowe lub moduły. 

W przypadku aktualizacji warunku docelowego wystąpią następujące aktualizacje:

* Jeśli bliźniacza reprezentacja nie spełnia starego warunku docelowego, ale spełnia nowy warunek docelowy i ta konfiguracja jest najwyższy priorytet dla tego bliźniaczego, a następnie ta konfiguracja jest stosowana. 

* Jeśli bliźniacza aktualnie uruchomiona ta konfiguracja nie spełnia już warunku docelowego, ustawienia z konfiguracji zostaną usunięte, a bliźniacza reprezentacja zostanie zmodyfikowana przez następną konfigurację o najwyższym priorytecie. 

* Jeśli bliźniacza aktualnie uruchomiona ta konfiguracja nie spełnia już warunku docelowego i nie spełnia warunku docelowego innych konfiguracji, ustawienia z konfiguracji zostaną usunięte i nie zostaną wprowadzone żadne inne zmiany w bliźniaczej reprezentacji. 

Aby zmodyfikować konfigurację, należy wykonać następujące czynności: 

1. W [witrynie Azure portal](https://portal.azure.com)przejdź do centrum IoT Hub. 

2. Wybierz **konfigurację urządzenia IoT**. 

3. Wybierz konfigurację, którą chcesz zmodyfikować. 

4. Aktualizuj następujące pola: 

   * Warunek docelowy 
   * Etykiety 
   * Priorytet 
   * Metryki

4. Wybierz pozycję **Zapisz**.

5. Wykonaj kroki opisane w [monitorze konfiguracji,](#monitor-a-configuration) aby obserwować wdrażanie zmian. 

## <a name="delete-a-configuration"></a>Usuwanie konfiguracji

Po usunięciu konfiguracji wszystkie bliźniacze urządzenia przyjmują następną konfigurację o najwyższym priorytecie. Jeśli bliźniacze urządzenia nie spełniają warunku docelowego innej konfiguracji, nie są stosowane żadne inne ustawienia. 

1. W [witrynie Azure portal](https://portal.azure.com)przejdź do centrum IoT Hub. 

2. Wybierz **konfigurację urządzenia IoT**. 

3. Użyj pola wyboru, aby wybrać konfigurację, którą chcesz usunąć. 

4. Wybierz pozycję **Usuń**.

5. Zostanie wyświetlony monit o potwierdzenie.

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
