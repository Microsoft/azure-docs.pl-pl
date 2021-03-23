---
title: Dostrajanie wydajności i pamięci wydawcy programu Microsoft OPC
description: W tym samouczku dowiesz się, jak dostroić wydajność i pamięć wydawcy OPC.
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: tutorial
ms.date: 3/22/2021
ms.openlocfilehash: 89e288d1186efd405019d6474dcbd332e7925d67
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2021
ms.locfileid: "104787841"
---
# <a name="tutorial-tune-the-opc-publisher-performance-and-memory"></a>Samouczek: dostrajanie wydajności i pamięci wydawcy OPC

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Dostosowanie wydajności
> * Dopasowywanie przepływu komunikatów do zasobów pamięci

W przypadku uruchamiania programu OPC Publisher w konfiguracjach produkcyjnych należy rozważyć wymagania dotyczące wydajności sieci (przepływności i opóźnienia) oraz zasoby pamięci. Wydawca OPC udostępnia następujące parametry wiersza polecenia, aby pomóc spełnić te wymagania:

* Pojemność kolejki komunikatów ( `mq` w przypadku wersji 2,5 i poniżej, niedostępna w wersji 2,6 `om` dla wersji 2,7)
* Interwał wysyłania IoT Hub ( `si` )
* Rozmiar komunikatu IoT Hub ( `ms` )

## <a name="adjusting-iot-hub-send-interval-and-iot-hub-message-size"></a>Dopasowywanie interwału wysyłania IoT Hub i rozmiaru komunikatu IoT Hub

`mq/om`Parametr steruje górnym limitem pojemności wewnętrznej kolejki komunikatów. Ta Kolejka buforuje wszystkie komunikaty przed ich wysłaniem do IoT Hub. Domyślny rozmiar kolejki wynosi do 2 MB dla wydawcy OPC w wersji 2,5 i poniżej oraz 4000 IoT Hub komunikatów dla wersji 2,7 (to oznacza, że jeśli ustawienie rozmiaru komunikatu IoT Hub ma wartość 256 KB, rozmiar kolejki będzie wynosić do 1 GB). Jeśli program OPC Publisher nie może wysyłać komunikatów do IoT Hub wystarczająco szybko, liczba elementów w tej kolejce rośnie. W takim przypadku podczas przebiegów testów można wykonać jedną lub obie następujące czynności w celu ograniczenia:

* Zmniejsz interwał wysyłania IoT Hub ( `si` )

* Zwiększ rozmiar komunikatu IoT Hub ( `ms` Maksymalna wartość tego ustawienia to 256 KB)

Jeśli kolejka ciągle rośnie, mimo że `si` Parametry i zostały `ms` dostosowane, ostatecznie zostanie osiągnięta maksymalna pojemność kolejki, a komunikaty zostaną utracone. Wynika to z faktu, że oba `si` Parametry i `ms` mają limity fizyczne, a połączenie internetowe między wydawcą OPC a IoT Hub nie jest wystarczająco szybkie dla liczby komunikatów, które muszą być wysyłane w danym scenariuszu. W takim przypadku można skonfigurować tylko kilka równoległych wydawców OPC. `mq/om`Parametr ma również największy wpływ na użycie pamięci przez wydawcę OPC. 

`si`Parametr wymusza OPC wydawcy do wysyłania komunikatów do IoT Hub w określonym interwale. Wysyłany jest komunikat, gdy maksymalny rozmiar komunikatu IoT Hub o 256 KB danych jest dostępny (wyzwalanie interwału wysyłania do zresetowania) lub po upływie określonego czasu interwału.

`ms`Parametr umożliwia tworzenie wsadowe komunikatów wysyłanych do IoT Hub. W większości ustawień sieci czas oczekiwania na wysłanie pojedynczego komunikatu do IoT Hub jest wysoki, w porównaniu do czasu potrzebnego do przesłania ładunku. Jest to spowodowane głównie wymaganiami dotyczącymi Quality of Service (QoS), ponieważ komunikaty są potwierdzane tylko wtedy, gdy zostały przetworzone przez IoT Hub). W związku z tym, jeśli opóźnienie dla danych, które zostaną dostarczone w IoT Hub jest akceptowalne, należy skonfigurować wydawcę, aby używać maksymalnego rozmiaru komunikatu 256 KB przez ustawienie `ms` parametru na 0. Jest to również najtańsza Metoda używania wydawcy OPC.

Konfiguracja domyślna wysyła dane do IoT Hub co 10 sekund ( `si=10` ) lub w przypadku dostępności 256 KB IoT Hub danych komunikatów ( `ms=0` ). Powoduje to dodanie maksymalnego opóźnienia wynoszącego 10 sekund, ale ma niskie prawdopodobieństwo utraty danych z powodu dużego rozmiaru wiadomości. Metryka `monitored item notifications enqueue failure`  w wydawcy OPC w wersji 2,5 i starszej oraz `messages lost` w programie OPC Publisher w wersji 2,7 pokazuje, ile komunikatów zostało utraconych.

Gdy oba `si` `ms` Parametry i są ustawione na wartość 0, OPC Publisher wysyła komunikat do IoT Hub, gdy tylko dane będą dostępne. Powoduje to średni rozmiar komunikatu IoT Hub przez ponad 200 bajtów. Jednak zaletą tej konfiguracji jest to, że wydawca programu OPC wysyła dane z połączonego zasobu bez opóźnień. Liczba utraconych wiadomości będzie wysoka dla przypadków użycia, w których musi być publikowana duża ilość danych, dlatego nie jest to zalecane w przypadku tych scenariuszy.

Aby zmierzyć wydajność wydawcy programu OPC, `di` można użyć parametru do drukowania metryk wydajności do dziennika śledzenia w określonym interwale (w sekundach).

## <a name="next-steps"></a>Następne kroki
Teraz, gdy wiesz już, jak dostosować wydajność i pamięć wydawcy OPC, możesz zapoznać się z repozytorium OPC Publisher w witrynie GitHub, aby uzyskać więcej zasobów:

> [!div class="nextstepaction"]
> [Repozytorium GitHub OPC Publisher](https://github.com/Azure/Industrial-IoT)