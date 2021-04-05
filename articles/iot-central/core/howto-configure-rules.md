---
title: Konfigurowanie reguł i akcji w usłudze Azure IoT Central | Microsoft Docs
description: Ten artykuł zawiera opis sposobu konfigurowania reguł i akcji opartych na danych telemetrycznych w aplikacji IoT Central platformy Azure.
author: vavilla
ms.author: vavilla
ms.date: 12/23/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 378a1dbcfbc89cdf9b24dc2490db583f1135b9a2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97796792"
---
# <a name="configure-rules"></a>Konfigurowanie reguł

*Ten artykuł dotyczy operatorów, konstruktorów i administratorów.*

Reguły w IoT Central pełnią rolę narzędzia do dostosowywania odpowiedzi, które wyzwalają aktywne monitorowane zdarzenia z połączonych urządzeń. W poniższych sekcjach opisano, jak są oceniane reguły.

## <a name="select-target-devices"></a>Wybierz urządzenia docelowe

Sekcja urządzenia docelowe służy do wybierania rodzaju urządzeń, na których zostanie zastosowana ta reguła. Filtry umożliwiają dokładniejsze zawężenie urządzeń, które powinny być uwzględniane. Filtry używają właściwości w szablonie urządzenia do filtrowania zestawu urządzeń. Same filtry nie wyzwalają akcji. Na poniższym zrzucie ekranu urządzenia, które są wskazywane, są typu szablonu urządzenia **chłodziarkd**. Filtr wskazuje, że reguła powinna zawierać tylko **chłodziarki** , w których właściwość **stan wyprodukowana** jest równa **Waszyngton**.

![Warunki](media/howto-configure-rules/filters.png)

## <a name="use-multiple-conditions"></a>Użycie wielu warunków

Warunki są wyzwalane przez reguły. Obecnie po dodaniu wielu warunków do reguły są one logicznie i razem. Innymi słowy, aby reguła mogła oszacować jako prawda, muszą zostać spełnione wszystkie warunki.  

Na poniższym zrzucie ekranu warunki sprawdzają, kiedy temperatura jest większa niż 70 &deg; F, a wilgotność jest mniejsza niż 10. Gdy obie te instrukcje są spełnione, reguła zwraca wartość true i wyzwala akcję.

![Zrzut ekranu przedstawia monitor chłodziark z warunkami określonymi dla temperatury i wilgotności.](media/howto-configure-rules/conditions.png)

### <a name="use-a-cloud-property-in-a-value-field"></a>Używanie właściwości chmury w polu wartości

Można odwołać się do właściwości chmury z szablonu urządzenia w polu **wartość** dla warunku. Właściwość chmury i wartość telemetrii muszą mieć podobne typy. Na przykład jeśli **temperatura** jest podwójna, wówczas tylko właściwości chmury typu Double Pokaż jako opcje na liście rozwijanej **wartość** .

Jeśli wybierzesz wartość telemetrii typu zdarzenia, lista rozwijana **wartość** zawiera opcję **dowolny**. **Każda** opcja oznacza, że reguła jest wyzwalana, gdy aplikacja otrzymuje zdarzenie tego typu, niezależnie od ładunku.

## <a name="use-aggregate-windowing"></a>Użyj okna agregacji

Reguły obliczają łączny czas systemu Windows jako wirowania Windows. Na poniższym zrzucie ekranu czas przedziału wynosi pięć minut. Co pięć minut reguła jest szacowana w ciągu ostatnich pięciu minut danych. Dane są oceniane tylko raz w oknie, do którego się odnosi.

![Wirowania systemu Windows](media/howto-configure-rules/tumbling-window.png)

## <a name="use-rules-with-iot-edge-modules"></a>Używanie reguł z modułami IoT Edge

Ograniczenie ma zastosowanie do reguł, które są stosowane do modułów IoT Edge. Reguły dotyczące danych telemetrycznych z różnych modułów nie są oceniane jako prawidłowe reguły. Zapoznaj się z poniższym przykładem. Pierwszy warunek reguły znajduje się na danych telemetrycznych temperatury z modułu A. Drugi warunek reguły znajduje się na telemetrcie wilgotności w module B. Ponieważ dwa warunki pochodzą z różnych modułów, jest to nieprawidłowy zestaw warunków. Reguła jest nieprawidłowa i zgłosi błąd podczas próby zapisania reguły.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy już wiesz, jak skonfigurować regułę w aplikacji IoT Central platformy Azure, możesz dowiedzieć się, jak [skonfigurować zaawansowane reguły](howto-configure-rules-advanced.md) przy użyciu automatyzacji lub Azure Logic Apps.
