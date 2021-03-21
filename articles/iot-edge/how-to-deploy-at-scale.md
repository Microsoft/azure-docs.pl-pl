---
title: Wdrażanie modułów na dużą skalę Azure Portal — Azure IoT Edge
description: Użyj Azure Portal, aby utworzyć automatyczne wdrożenia dla grup urządzeń IoT Edge
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/13/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: db27a466ca5f1370e8b43ceb472f5deeaba509f1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103200333"
---
# <a name="deploy-iot-edge-modules-at-scale-using-the-azure-portal"></a>Wdrażanie modułów IoT Edge na dużą skalę przy użyciu Azure Portal

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Utwórz **IoT Edge Automatyczne wdrażanie** w Azure Portal, aby zarządzać trwającymi wdrożeniami dla wielu urządzeń jednocześnie. Automatyczne wdrożenia dla IoT Edge są częścią funkcji [automatycznej zarządzania urządzeniami](../iot-hub/iot-hub-automatic-device-management.md) w programie IoT Hub. Wdrożenia to procesy dynamiczne, które umożliwiają wdrożenie wielu modułów na wielu urządzeniach, śledzenie stanu i kondycji modułów oraz wprowadzanie zmian w razie potrzeby.

Aby uzyskać więcej informacji, zobacz [opis IoT Edge wdrożenia automatyczne dla pojedynczych urządzeń lub w odpowiedniej skali](module-deployment-monitoring.md).

## <a name="identify-devices-using-tags"></a>Identyfikowanie urządzeń przy użyciu tagów

Przed utworzeniem wdrożenia należy mieć możliwość określania, które urządzenia mają mieć wpływ. Azure IoT Edge identyfikuje urządzenia przy użyciu **tagów** z sznurka urządzenia. Każde urządzenie może mieć wiele tagów zdefiniowanych w dowolny sposób, który ma sens dla danego rozwiązania.

Na przykład, Jeśli zarządzasz kampusami z inteligentnymi budynkami, możesz dodać do urządzenia następujące znaczniki lokalizacji, typu pokoju i środowiska:

```json
"tags":{
  "location":{
    "building": "20",
    "floor": "2"
  },
  "roomtype": "conference",
  "environment": "prod"
}
```

Aby uzyskać więcej informacji na temat bliźniaczych reprezentacji i tagów urządzeń, zobacz [Omówienie i używanie urządzenia bliźniaczych reprezentacji w IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md).

## <a name="create-a-deployment"></a>Tworzenie wdrożenia

IoT Edge oferuje dwa różne typy wdrożeń automatycznych, których można użyć do dostosowania scenariusza. Można utworzyć standardowe *wdrożenie*, które obejmuje moduły środowiska uruchomieniowego systemu oraz dodatkowe moduły i trasy. Każde urządzenie może zastosować tylko jedno wdrożenie. Można też utworzyć *wdrożenie warstwowe*, które obejmuje tylko niestandardowe moduły i trasy, a nie środowisko uruchomieniowe systemu. Wiele wdrożeń warstwowych można łączyć na urządzeniu na podstawie standardowego wdrożenia. Aby uzyskać więcej informacji o tym, jak dwa typy wdrożeń automatycznych współpracują ze sobą, zobacz [opis IoT Edge wdrożenia automatyczne dla pojedynczych urządzeń lub w odpowiedniej skali](module-deployment-monitoring.md).

Kroki związane z tworzeniem wdrożenia i wdrożeniem warstwowym są bardzo podobne. Wszelkie różnice są wywoływane w poniższych krokach.

1. W [Azure Portal](https://portal.azure.com)przejdź do IoT Hub.
1. W menu w lewym okienku wybierz pozycję **IoT Edge** w obszarze **Automatyczne zarządzanie urządzeniami**.
1. Na górnym pasku wybierz pozycję **Utwórz wdrożenie** lub **Utwórz wdrożenie warstwowe**.

Istnieje pięć kroków, które należy wykonać, aby utworzyć wdrożenie. W poniższych sekcjach omówiono każdy z nich.

>[!NOTE]
>Kroki opisane w tym artykule odzwierciedlają najnowszą wersję schematu IoT Edge agenta i centrum. Wersja schematu 1,1 została wydana wraz z IoT Edge wersja 1.0.10 i włącza funkcje kolejności uruchamiania modułu i określania priorytetów trasy.
>
>W przypadku wdrażania na urządzeniu z uruchomioną wersją 1.0.9 lub wcześniejszą należy zmodyfikować **Ustawienia środowiska uruchomieniowego** **w kroku w kreatorze, aby** użyć wersji schematu 1,0.

### <a name="step-1-name-and-label"></a>Krok 1. nazwa i etykieta

1. Nadaj wdrożenie unikatową nazwę, która jest maksymalnie 128 małymi literami. Unikaj spacji i następujących nieprawidłowych znaków: `& ^ [ ] { } \ | " < > /` .
1. Etykiety można dodawać jako pary klucz-wartość, aby ułatwić śledzenie wdrożeń. Na przykład **HostPlatform** i **Linux**, lub **wersja** i **3.0.1**.
1. Wybierz kolejno pozycje **Następny: moduły** , aby przejść do kroku 2.

### <a name="step-2-modules"></a>Krok 2. moduły

Do wdrożenia można dodać do 50 modułów. Jeśli utworzysz wdrożenie bez modułów, spowoduje to usunięcie wszystkich bieżących modułów z urządzeń docelowych.

W obszarze wdrożenia można zarządzać ustawieniami agenta IoT Edge i IoT Edge modułów centrów. Wybierz pozycję **Ustawienia środowiska uruchomieniowego** , aby skonfigurować dwa moduły środowiska uruchomieniowego. W przypadku wdrażania warstwowego moduły środowiska uruchomieniowego nie są uwzględniane, więc nie można ich skonfigurować.

Aby dodać niestandardowy kod jako moduł lub ręcznie dodać moduł usługi platformy Azure, wykonaj następujące kroki:

1. W sekcji **ustawienia Container Registry** na stronie podaj poświadczenia, aby uzyskać dostęp do wszelkich prywatnych rejestrów kontenerów zawierających obrazy modułu.
1. W sekcji **IoT Edge modułów** na stronie wybierz pozycję **Dodaj**.
1. Wybierz jeden z trzech typów modułów z menu rozwijanego:

   * **Moduł IoT Edge** — Podaj nazwę modułu i identyfikator URI obrazu kontenera. Na przykład identyfikator URI obrazu dla przykładowego modułu SimulatedTemperatureSensor to `mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0` . Jeśli obraz modułu jest przechowywany w rejestrze kontenera prywatnego, Dodaj poświadczenia na tej stronie, aby uzyskać dostęp do tego obrazu.
   * Moduły **portalu Marketplace** hostowane w witrynie Azure Marketplace. Niektóre moduły portalu Marketplace wymagają dodatkowej konfiguracji, dlatego Przejrzyj szczegóły modułu na liście [modułów IoT Edge portalu Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules) .
   * Moduły modułów **Azure Stream Analytics** wygenerowane na podstawie obciążenia Azure Stream Analyticsowego.

1. W razie potrzeby powtórz kroki 2 i 3, aby dodać kolejne moduły do wdrożenia.

Po dodaniu modułu do wdrożenia można wybrać jego nazwę, aby otworzyć stronę **modułu aktualizacji IoT Edge** . Na tej stronie można edytować ustawienia modułu, zmienne środowiskowe, opcje tworzenia, kolejność uruchamiania i sznurki modułowe. Jeśli dodano moduł z portalu Marketplace, może on mieć już wypełnione niektóre parametry. Aby uzyskać więcej informacji na temat dostępnych ustawień modułu, zobacz [Konfiguracja modułu i zarządzanie nimi](module-composition.md#module-configuration-and-management).

W przypadku tworzenia wdrożenia warstwowego można skonfigurować moduł, który istnieje w innych wdrożeniach przeznaczonych dla tych samych urządzeń. Aby zaktualizować splot modułu bez zastępowania innych wersji, Otwórz kartę **Ustawienia dla sznurka modułu** . Utwórz nową **Właściwość sznurka modułu** o unikatowej nazwie podsekcji w odpowiednich właściwościach sznurka modułu, na przykład `properties.desired.settings` . Jeśli zdefiniujesz właściwości w tylko `properties.desired` polu, spowoduje to zastąpienie żądanych właściwości modułu zdefiniowanego we wdrożeniach o niższym priorytecie.

![Ustaw właściwość sznurka modułu dla wdrożenia warstwowego](./media/how-to-deploy-monitor/module-twin-property.png)

Aby uzyskać więcej informacji na temat konfiguracji sznurka modułu w przypadku wdrożeń warstwowych, zobacz [wdrażanie warstwowe](module-deployment-monitoring.md#layered-deployment).

Po skonfigurowaniu wszystkich modułów dla wdrożenia wybierz pozycję **Dalej: trasy** , które mają zostać przeniesione do kroku 3.

### <a name="step-3-routes"></a>Krok 3. trasy

Na karcie **trasy** należy określić sposób przekazywania komunikatów między modułami i IoT Hub. Komunikaty są konstruowane przy użyciu par nazwa/wartość.

Na przykład trasa o nazwie **trasa** i wartość **z/messages/ \* do $upstream** zajmie wszystkie komunikaty wyjściowe przez dowolne moduły i wyśle je do centrum IoT Hub.  

**Priorytet** i **czas wygaśnięcia** parametrów są opcjonalnymi parametrami, które można uwzględnić w definicji trasy. Parametr Priority umożliwia wybranie tras, które powinny być przetwarzane jako pierwsze, lub które trasy powinny być przetwarzane jako ostatnie. Priorytet jest określany przez ustawienie liczby 0-9, gdzie 0 oznacza najwyższy priorytet. Parametr Time to Live umożliwia zadeklarować, jak długo komunikaty w tej trasie powinny być przechowywane, dopóki nie zostaną przetworzone lub usunięte z kolejki.

Aby uzyskać więcej informacji o sposobach tworzenia tras, zobacz [deklarowanie tras](module-composition.md#declare-routes).

Wybierz pozycję **Dalej: metryki**.

### <a name="step-4-metrics"></a>Krok 4. metryki

Metryki zawierają podsumowanie liczb różnych stanów, które urządzenie może zgłosić z powrotem w wyniku zastosowania zawartości konfiguracji.

1. Wprowadź nazwę dla **nazwy metryki**.

1. Wprowadź zapytanie dla **kryteriów metryki**. Zapytanie jest oparte na IoT Edge [raportowanych właściwościach](module-edgeagent-edgehub.md#edgehub-reported-properties)modułu centrum. Metryka reprezentuje liczbę wierszy zwracanych przez zapytanie.

   Na przykład:

   ```sql
   SELECT deviceId FROM devices
     WHERE properties.reported.lastDesiredStatus.code = 200
   ```

Wybierz pozycję **Dalej: urządzenia docelowe**.

### <a name="step-5-target-devices"></a>Krok 5. urządzenia docelowe

Użyj właściwości Tags z urządzeń, aby określić urządzenia docelowe, które powinny otrzymać to wdrożenie.

Ponieważ wiele wdrożeń może wskazywać na to samo urządzenie, należy nadać każdemu wdrożeniu numer priorytetu. Jeśli kiedykolwiek wystąpi konflikt, wdrożenie o najwyższym priorytecie (większe wartości wskazują wyższy priorytet) WINS. Jeśli dwa wdrożenia mają taki sam numer priorytetu, ten, który został utworzony ostatnio jako usługa WINS.

Jeśli wiele wdrożeń jest przeznaczonych dla tego samego urządzenia, stosowany jest tylko ten z wyższym priorytetem. Jeśli wiele wdrożeń warstwowych jest przeznaczonych dla tego samego urządzenia, zostaną one zastosowane. Jednakże jeśli wszystkie właściwości są zduplikowane, na przykład jeśli istnieją dwie trasy o tej samej nazwie, to jeden z wyższych priorytetów wdrożenia warstwowego zastępuje resztę.

Każde wdrożenie warstwowe ukierunkowane na urządzenie musi mieć wyższy priorytet niż wdrożenie podstawowe, aby można je było zastosować.

1. Wprowadź dodatnią liczbę całkowitą dla **priorytetu** wdrożenia.
1. Wprowadź **warunek docelowy** , aby określić, które urządzenia będą ukierunkowane na to wdrożenie. Warunek jest oparty na tagach bliźniaczych urządzeń lub w raportowanych właściwościach urządzenia i powinien być zgodny z formatem wyrażenia. Na przykład: `tags.environment='test'` lub `properties.reported.devicemodel='4000x'`.

Wybierz pozycję **Dalej: Przejrzyj i Utwórz** , aby przejść do ostatniego kroku.

### <a name="step-6-review-and-create"></a>Krok 6. przegląd i tworzenie

Przejrzyj informacje o wdrożeniu, a następnie wybierz pozycję **Utwórz**.

Aby monitorować wdrożenie, zobacz [monitorowanie wdrożeń IoT Edge](how-to-monitor-iot-edge-deployments.md).

## <a name="modify-a-deployment"></a>Modyfikowanie wdrożenia

Po zmodyfikowaniu wdrożenia zmiany są natychmiast replikowane na wszystkie urządzenia objęte usługą. Istnieje możliwość zmodyfikowania następujących ustawień i funkcji dla istniejącego wdrożenia:

* Warunki docelowe
* Metryki niestandardowe
* Etykiety
* Tagi
* Żądane właściwości

### <a name="modify-target-conditions-custom-metrics-and-labels"></a>Modyfikowanie warunków docelowych, metryk niestandardowych i etykiet

1. W centrum IoT wybierz pozycję **IoT Edge** w menu po lewej stronie.
1. Wybierz kartę **wdrożenia IoT Edge** a następnie wybierz wdrożenie, które chcesz skonfigurować.
1. Wybierz kartę **warunek docelowy** . Zmień **warunek docelowy** , aby określić docelowe urządzenia. Możesz również dostosować **priorytet**.  Wybierz pozycję **Zapisz**.

    W przypadku zaktualizowania warunku docelowego następujące aktualizacje są wykonywane:

    * Jeśli urządzenie nie spełnia starego warunku docelowego, ale spełnia warunki nowego elementu docelowego, a to wdrożenie ma najwyższy priorytet dla tego urządzenia, to wdrożenie jest stosowane na urządzeniu.
    * Jeśli urządzenie, na którym jest uruchomione to wdrożenie, już nie spełnia warunku docelowego, Odinstalowuje to wdrożenie i podejmuje kolejne wdrożenie o najwyższym priorytecie.
    * Jeśli urządzenie, na którym jest uruchomione to wdrożenie, już nie spełnia warunku docelowego i nie spełnia warunku docelowego innych wdrożeń, nie nastąpi żadne zmiany na urządzeniu. Urządzenie kontynuuje uruchamianie bieżących modułów w bieżącym stanie, ale nie jest już zarządzane w ramach tego wdrożenia. Po spełnieniu warunku docelowego dowolnego innego wdrożenia Odinstalowuje to wdrożenie i przyjmuje nowe.

1. Wybierz kartę **metryki** i kliknij przycisk **Edytuj metryki** . Dodawanie lub modyfikowanie metryk niestandardowych przy użyciu składni przykładowej jako przewodnika. Wybierz pozycję **Zapisz**.

    ![Edytowanie metryk niestandardowych w ramach wdrożenia](./media/how-to-deploy-monitor/metric-list.png)

1. Wybierz kartę **etykiety** i wprowadź żądane zmiany i wybierz pozycję **Zapisz**.

## <a name="delete-a-deployment"></a>Usuwanie wdrożenia

Po usunięciu wdrożenia wszystkie wdrożone urządzenia przyjmą kolejne wdrożenie o najwyższym priorytecie. Jeśli urządzenia nie spełniają warunku docelowego innego wdrożenia, moduły nie zostaną usunięte po usunięciu wdrożenia.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) i przejdź do IoT Hub.
1. Wybierz **IoT Edge**.
1. Wybierz kartę **wdrożenia IoT Edge** .

   ![Wyświetl IoT Edge wdrożenia](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. Użyj pola wyboru, aby wybrać wdrożenie, które chcesz usunąć.
1. Wybierz pozycję **Usuń**.
1. Zostanie wyświetlony monit z informacją o tym, że ta akcja spowoduje usunięcie tego wdrożenia i przywrócenie poprzedniego stanu dla wszystkich urządzeń. Zostanie zastosowane wdrożenie z niższym priorytetem. Jeśli żadne inne wdrożenie nie jest wskazywane, moduły nie zostaną usunięte. Jeśli chcesz usunąć wszystkie moduły z urządzenia, Utwórz wdrożenie z zerowymi modułami i wdróż je na tych samych urządzeniach. Wybierz przycisk **Tak**, aby kontynuować.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [wdrażaniu modułów na IoT Edge urządzeniach](module-deployment-monitoring.md).