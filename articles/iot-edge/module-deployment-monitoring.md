---
title: Automatyczne wdrażanie dla grup urządzeń — Azure IoT Edge | Microsoft Docs
description: Używanie wdrożeń automatycznych w programie Azure IoT Edge do zarządzania grupami urządzeń w oparciu o udostępnione Tagi
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/30/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 3989ec4ca2b5c9d7385841604678791b20c1d102
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103489986"
---
# <a name="understand-iot-edge-automatic-deployments-for-single-devices-or-at-scale"></a>Informacje na temat IoT Edge wdrożeń automatycznych dla pojedynczych urządzeń lub na dużą skalę

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Wdrożenia automatyczne i wdrożenie warstwowe ułatwiają zarządzanie i Konfigurowanie modułów na dużej liczbie IoT Edge urządzeń.

Azure IoT Edge oferuje dwa sposoby konfigurowania modułów do uruchamiania na urządzeniach IoT Edge. Pierwsza metoda polega na wdrożeniu modułów na poszczególnych urządzeniach. Należy utworzyć manifest wdrożenia, a następnie zastosować go do określonego urządzenia według nazwy. Druga metoda polega na automatycznym wdrożeniu modułów na dowolnym zarejestrowanym urządzeniu, które spełnia określone warunki. Należy utworzyć manifest wdrożenia, a następnie określić, które urządzenia odnoszą się do nich w oparciu o [Tagi](../iot-edge/how-to-deploy-at-scale.md#identify-devices-using-tags) w bliźniaczych urządzeniach.

Ten artykuł koncentruje się na konfigurowaniu i monitorowaniu flot urządzeń, zbiorczo nazywanymi *IoT Edge wdrożeniami automatycznymi*. Podstawowe kroki wdrażania są następujące:

1. Operator definiuje wdrożenie opisujące zestaw modułów i urządzeń docelowych. Każde wdrożenie zawiera manifest wdrożenia, który odzwierciedla te informacje.
2. Usługa IoT Hub komunikuje się ze wszystkimi urządzeniami przeznaczonymi do konfigurowania za pomocą zadeklarowanych modułów.
3. Usługa IoT Hub Pobiera stan z urządzeń IoT Edge i udostępnia je operatorowi.  Na przykład operator może zobaczyć, kiedy urządzenie brzegowe nie zostało prawidłowo skonfigurowane lub jeśli wystąpi błąd modułu w czasie wykonywania.
4. W każdej chwili do wdrożenia są konfigurowane nowe IoT Edge urządzenia spełniające warunki określania wartości docelowej.

W tym artykule opisano każdy składnik związany z konfigurowaniem i monitorowaniem wdrożenia. Aby zapoznać się z przewodnikiem dotyczącym tworzenia i aktualizowania wdrożenia, zobacz [wdrażanie i monitorowanie modułów IoT Edge w odpowiedniej skali](how-to-deploy-at-scale.md).

## <a name="deployment"></a>Wdrożenie

IoT Edge wdrożenie automatyczne przypisuje IoT Edge obrazy modułów do uruchamiania jako wystąpienia w określonym zestawie IoT Edge urządzeń. Działa przez skonfigurowanie manifestu wdrażania IoT Edge w celu uwzględnienia listy modułów z odpowiednimi parametrami inicjalizacji. Wdrożenie może być przypisane do jednego urządzenia (w oparciu o identyfikator urządzenia) lub do grupy urządzeń (w oparciu o Tagi). Gdy urządzenie IoT Edge odbiera manifest wdrożenia, pobiera i instaluje obrazy kontenerów z odpowiednich repozytoriów kontenerów i odpowiednio konfiguruje je. Po utworzeniu wdrożenia operator może monitorować stan wdrożenia, aby sprawdzić, czy urządzenia są poprawnie skonfigurowane.

Tylko urządzenia IoT Edge można skonfigurować przy użyciu wdrożenia. Poniższe wymagania wstępne muszą znajdować się na urządzeniu, aby można było odebrać wdrożenie:

* Podstawowy system operacyjny
* System zarządzania kontenerami, taki jak Moby lub Docker
* Inicjowanie obsługi środowiska uruchomieniowego IoT Edge

### <a name="deployment-manifest"></a>Manifest wdrożenia

Manifest wdrożenia jest dokumentem JSON opisującym moduły, które mają być skonfigurowane na urządzeniach IoT Edge. Zawiera metadane konfiguracji dla wszystkich modułów, w tym wymagane moduły systemowe (w szczególności Agent IoT Edge i centrum IoT Edge).  

Metadane konfiguracji dla każdego modułu obejmują:

* Wersja
* Typ
* Stan (na przykład uruchomiona lub zatrzymana)
* Zasady ponownego uruchamiania
* Obraz i rejestr kontenerów
* Trasy dla danych wejściowych i wyjściowych

Jeśli obraz modułu jest przechowywany w rejestrze kontenera prywatnego, Agent IoT Edge przechowuje poświadczenia rejestru.

### <a name="target-condition"></a>Warunek docelowy

Warunek docelowy jest stale oceniany przez cały okres istnienia wdrożenia. Wszystkie nowe urządzenia spełniające wymagania są uwzględniane, a wszystkie istniejące urządzenia, które nie są już usuwane. Wdrożenie jest ponownie uaktywniane, jeśli usługa wykryje jakąkolwiek zmianę warunku docelowego.

Na przykład istnieje wdrożenie z tagami warunku docelowego. Environment = "prod". Po rozmieszczeniu wdrożenia programu dostępne są 10 urządzeń produkcyjnych. Moduły zostały pomyślnie zainstalowane na tych 10 urządzeniach. Stan agenta IoT Edge przedstawia 10 wszystkich urządzeń, 10 udanych odpowiedzi, 0 odpowiedzi na błędy i 0 oczekujących odpowiedzi. Teraz dodasz pięć więcej urządzeń ze znacznikami. Environment = "prod". Usługa wykrywa zmianę i stan agenta IoT Edge stanie się 15 łączna liczba urządzeń, 10 udanych odpowiedzi, 0 odpowiedzi na błędy i 5 odpowiedzi oczekujących podczas wdrażania na pięć nowych urządzeń.

Aby wybrać urządzenia docelowe, użyj dowolnego warunku logicznego dla tagów sznurka urządzenia, zgłaszanych właściwości lub urządzenia deviceId. Jeśli chcesz użyć warunku ze znacznikami, musisz dodać "Tagi": {} sekcję w sznurze urządzenia pod tym samym poziomem co właściwości. [Dowiedz się więcej o tagach w bliźniaczych urządzeniach](../iot-hub/iot-hub-devguide-device-twins.md)

Przykłady warunków docelowych:

* deviceId = "linuxprod1"
* Tagi. Environment = "prod"
* Tagi. Environment = "prod" i Tags. Location = "zachodnie"
* Tagi. Environment = "prod" lub Tags. Location = "zachodnie"
* Tags. operator = "Jan" i Tagi. Environment = "prod" i NOT deviceId = "linuxprod1"
* Properties. devicemodel = "4000x"

Te ograniczenia należy wziąć pod uwagę podczas konstruowania warunku docelowego:

* W przypadku sznurka urządzenia można utworzyć warunek docelowy tylko przy użyciu tagów, raportowanych właściwości lub deviceId.
* Podwójne cudzysłowy nie są dozwolone w żadnej części warunku docelowego. Użyj apostrofów.
* Pojedyncze cudzysłowy reprezentują wartości warunku docelowego. W związku z tym należy wymusić pojedyncze cudzysłowy z innym pojedynczym cudzysłowem, jeśli jest ono częścią nazwy urządzenia. Na przykład, aby określić urządzenie jako docelowe `operator'sDevice` , Zapisz `deviceId='operator''sDevice'` .
* W wartościach warunku docelowego są dozwolone liczby, litery i następujące znaki: `-:.+%_#*?!(),=@;$` .

### <a name="priority"></a>Priorytet

Priorytet definiuje, czy wdrożenie ma być stosowane do urządzenia przeznaczonego względem innych wdrożeń. Priorytet wdrożenia jest dodatnią liczbą całkowitą o większej liczbie oznaczającej wyższy priorytet. Jeśli urządzenie IoT Edge jest objęte więcej niż jednym wdrożeniem, obowiązuje wdrożenie z najwyższym priorytetem.  Wdrożenia z niższymi priorytetami nie są stosowane ani nie są scalane.  Jeśli urządzenie jest przeznaczone dla co najmniej dwóch wdrożeń o równym priorytecie, stosuje się ostatnio utworzone wdrożenie (określone przez sygnaturę czasową tworzenia).

### <a name="labels"></a>Etykiety

Etykiety to pary klucz/wartość, których można użyć do filtrowania i grupowania wdrożeń. Wdrożenie może mieć wiele etykiet. Etykiety są opcjonalne i nie mają wpływu na rzeczywistą konfigurację IoT Edge urządzeń.

### <a name="metrics"></a>Metryki

Domyślnie wszystkie wdrożenia są raportowane według czterech metryk:

* Element **docelowy** przedstawia IoT Edge urządzeń, które pasują do warunku określania wartości docelowej wdrożenia.
* **Dotyczy** IoT Edge to urządzeń, które nie są objęte innymi wdrożeniami o wyższym priorytecie.
* **Raportowanie sukcesu** zawiera IoT Edge urządzeń, które zgłosiły, że moduły zostały pomyślnie wdrożone.
* **Raportowanie błędów** przedstawia IoT Edge urządzeń, które zgłosiły, że co najmniej jeden moduł nie został pomyślnie wdrożony. Aby dokładniej zbadać błąd, Połącz się zdalnie z tymi urządzeniami i Przejrzyj pliki dziennika.

Ponadto można definiować własne metryki niestandardowe w celu ułatwienia monitorowania wdrożenia i zarządzania nim.

Metryki zawierają podsumowanie liczb różnych stanów, które urządzenia mogą raportować z powrotem w wyniku zastosowania konfiguracji wdrożenia. Metryki mogą wykonywać zapytania dotyczące [właściwości w module edgeHub](module-edgeagent-edgehub.md#edgehub-reported-properties), takich jak *lastDesiredStatus* lub *lastConnectTime*. Na przykład:

```sql
SELECT deviceId FROM devices
  WHERE properties.reported.lastDesiredStatus.code = 200
```

Dodawanie własnych metryk jest opcjonalne i nie ma wpływu na rzeczywistą konfigurację IoT Edge urządzeń.

## <a name="layered-deployment"></a>Wdrożenie warstwowe

Wdrożenia warstwowe to automatyczne wdrożenia, które można łączyć ze sobą, aby zmniejszyć liczbę unikatowych wdrożeń, które należy utworzyć. Wdrożenia warstwowe są przydatne w scenariuszach, w których te same moduły są ponownie używane w różnych kombinacjach w wielu wdrożeniach automatycznych.

Wdrożenia warstwowe mają takie same składniki podstawowe jak dowolne wdrożenie automatyczne. Są one urządzeniami docelowymi w oparciu o Tagi w urządzeniu bliźniaczych reprezentacji i oferują te same funkcje wokół etykiet, metryk i raportów o stanie. Wdrożenia warstwowe mają przypisane priorytety, ale zamiast korzystać z priorytetu w celu ustalenia, które wdrożenie jest stosowane do urządzenia, priorytet określa, jak wiele wdrożeń jest uporządkowanych na urządzeniu. Na przykład jeśli dwa wdrożenia warstwowe mają moduł lub trasę o tej samej nazwie, wdrożenie warstwowe o wyższym priorytecie zostanie zastosowane, gdy dolny priorytet zostanie zastąpiony.

Moduły uruchomieniowe systemu, edgeAgent i edgeHub nie są skonfigurowane jako część wdrożenia warstwowego. Każde urządzenie IoT Edge objęte wdrożeniem warstwowym wymaga najpierw standardowego wdrożenia automatycznego. Wdrożenie automatyczne zapewnia podstawę, w której można dodawać wdrożenia warstwowe.

Urządzenie IoT Edge może zastosować jedno i tylko jedno standardowe wdrożenie automatyczne, ale może stosować wiele wdrożeń automatycznych z warstwami. Wszystkie wdrożenia warstwowe ukierunkowane na urządzenie muszą mieć wyższy priorytet niż wdrożenie automatyczne dla tego urządzenia.

Rozważmy na przykład następujący scenariusz firmy, która zarządza budynkami. Opracowano IoT Edge modułów do zbierania danych z kamer zabezpieczeń, czujników ruchu i wind. Jednak nie wszystkie budynki mogą korzystać z wszystkich trzech modułów. W przypadku standardowych wdrożeń automatycznych firma musi utworzyć poszczególne wdrożenia dla wszystkich kombinacji modułów, których potrzebują.

![Standardowe wdrożenia automatyczne muszą obsługiwać każdą kombinację modułów](./media/module-deployment-monitoring/standard-deployment.png)

Jednak po przełączeniu do warstwowych wdrożeń automatycznych można utworzyć te same kombinacje modułów dla budynków z mniejszą liczbą wdrożeń do zarządzania. Każdy moduł ma własne wdrożenie warstwowe, a Tagi urządzeń określają, które moduły zostaną dodane do każdego budynku.

![Automatyczne wdrożenia warstwowe upraszczają scenariusze, w których te same moduły są łączone na różne sposoby](./media/module-deployment-monitoring/layered-deployment.png)

### <a name="module-twin-configuration"></a>Konfiguracja sznurka modułu

Podczas pracy z wdrożeniami warstwowymi można celowo lub w inny sposób mieć dwa wdrożenia z tym samym modułem przeznaczonym dla urządzenia. W takich przypadkach można zdecydować, czy wdrożenie o wyższym priorytecie powinno zastąpić sznurek modułu, czy dołączyć do niego. Na przykład może istnieć wdrożenie, które stosuje ten sam moduł do 100 różnych urządzeń. Jednak 10 z tych urządzeń jest w bezpiecznym zakresie i potrzebna jest dodatkowa konfiguracja w celu komunikowania się przez serwery proxy. Przy użyciu wdrożenia warstwowego można dodać właściwości wieloosiowe modułu, które umożliwią tym 10 urządzeniom bezpieczne komunikowanie się bez zastępowania istniejących informacji o postawce modułu z podstawowego wdrożenia.

W manifeście wdrożenia można dołączyć odpowiednie właściwości sznurka modułu. W przypadku wdrożenia standardowego należy dodać właściwości w sekcji **właściwości. wymagana** sekcja sznurka modułu, w przypadku wdrożenia warstwowego można zadeklarować nowy podzbiór żądanych właściwości.

Na przykład w standardowym wdrożeniu można dodać moduł symulowanego czujnika temperatury z następującymi pożądanymi właściwościami, które informują go o konieczności wysyłania danych w 5-sekundowych odstępach czasu:

```json
"SimulatedTemperatureSensor": {
  "properties.desired": {
    "SendData": true,
    "SendInterval": 5
  }
}
```

W przypadku wdrożenia warstwowego, które jest przeznaczone dla niektórych lub wszystkich urządzeń, można dodać właściwość informującą, że czujnik symulowany wysyła komunikaty 1000, a następnie zatrzymuje. Nie chcesz zastąpić istniejących właściwości, więc Utwórz nową sekcję w ramach żądanych właściwości `layeredProperties` , które zawierają nową właściwość:

```json
"SimulatedTemperatureSensor": {
  "properties.desired.layeredProperties": {
    "StopAfterCount": 1000
  }
}
```

Urządzenie, które ma zastosowane oba wdrożenia, będzie odzwierciedlać następujące właściwości w postaci sznurka modułowego dla czujnika symulowanej temperatury:

```json
"properties": {
  "desired": {
    "SendData": true,
    "SendInterval": 5,
    "layeredProperties": {
      "StopAfterCount": 1000
    }
  }
}
```

Jeśli ustawisz `properties.desired` pole sznurka modułu w ramach wdrożenia warstwowego, zastąpią one odpowiednie właściwości tego modułu we wdrożeniach o niższym priorytecie.

## <a name="phased-rollout"></a>Etapowe wdrażanie

Etapowe wdrażanie to ogólny proces, w którym operator wdraża zmiany w rozszerzonym zestawie IoT Edge urządzeń. Celem jest stopniowe wprowadzanie zmian w celu zmniejszenia ryzyka związanego z wprowadzaniem zmian w skali szerokiej. Automatyczne wdrożenia pomagają zarządzać stopniowanymi wdrożeniami w ramach floty IoT Edge urządzeń.

Etapowe wdrażanie jest wykonywane w następujących fazach i krokach:

1. Ustanów środowisko testowe IoT Edge urządzeń, udostępniając je i ustawiając tag sznurka urządzenia, taki jak `tag.environment='test'` . Środowisko testowe powinno dublować środowisko produkcyjne, w którym wdrożenie będzie ostatecznie docelowe.
2. Utwórz wdrożenie, w tym wymagane moduły i konfiguracje. Warunek określania wartości docelowej powinien dotyczyć środowiska testowego IoT Edge urządzenia.
3. Sprawdź poprawność konfiguracji nowego modułu w środowisku testowym.
4. Zaktualizuj wdrożenie, aby uwzględnić podzestaw IoT Edge produkcyjnych urządzeń przez dodanie nowego tagu do warunku określania wartości docelowej. Upewnij się również, że priorytet wdrożenia jest wyższy niż inne wdrożenia, które są obecnie przeznaczone dla tych urządzeń.
5. Sprawdź, czy wdrożenie zakończyło się pomyślnie na kierowanych urządzeniach IoT, wyświetlając stan wdrożenia.
6. Zaktualizuj wdrożenie na wszystkie pozostałe urządzenia IoT Edge produkcyjnego.

## <a name="rollback"></a>Wycofywanie

Wdrożenia mogą być wycofywane w przypadku otrzymania błędów lub błędnej konfiguracji. Ze względu na to, że wdrożenie definiuje bezwzględną konfigurację modułu dla urządzenia IoT Edge, dodatkowe wdrożenie musi być również celem tego samego urządzenia o niższym priorytecie, nawet jeśli celem jest usunięcie wszystkich modułów.  

Usunięcie wdrożenia nie powoduje usunięcia modułów z urządzeń kierowanych. Musi istnieć inne wdrożenie, które definiuje nową konfigurację dla urządzeń, nawet jeśli jest to puste wdrożenie.

Wykonaj wycofywanie w następującej kolejności:

1. Upewnij się, że drugie wdrożenie jest również przeznaczone dla tego samego zestawu urządzeń. Jeśli celem wycofywania jest usunięcie wszystkich modułów, drugie wdrożenie nie powinno obejmować żadnych modułów.
2. Zmodyfikuj lub Usuń wyrażenie warunku docelowego wdrożenia, które chcesz przywrócić, aby urządzenia nie spełniały warunku określania wartości docelowej.
3. Sprawdź, czy wycofywanie zakończyło się pomyślnie, wyświetlając stan wdrożenia.
   * Wdrożenie wycofane nie powinno już wyświetlać stanu dla urządzeń, które zostały wycofane.
   * Drugie wdrożenie powinno teraz zawierać stan wdrożenia dla urządzeń, które zostały wycofane.

## <a name="next-steps"></a>Następne kroki

* Zapoznaj się z instrukcjami tworzenia, aktualizowania lub usuwania wdrożenia w sekcji [wdrażanie i monitorowanie modułów IoT Edge](how-to-deploy-at-scale.md)w odpowiedniej skali.
* Dowiedz się więcej na temat innych IoT Edge pojęć, takich jak [środowisko uruchomieniowe IoT Edge](iot-edge-runtime.md) i [moduły IoT Edge](iot-edge-modules.md).
