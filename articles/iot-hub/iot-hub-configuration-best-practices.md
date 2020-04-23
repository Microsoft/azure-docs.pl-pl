---
title: Najważniejsze wskazówki dotyczące konfiguracji urządzeń dla usługi Azure IoT Hub | Dokumenty firmy Microsoft
description: Dowiedz się więcej o najlepszych rozwiązaniach dotyczących używania automatycznego zarządzania urządzeniami w celu zminimalizowania powtarzających się i złożonych zadań związanych z zarządzaniem urządzeniami IoT na dużą skalę.
author: robinsh
ms.author: robinsh
ms.date: 06/28/2019
ms.topic: conceptual
ms.service: iot-hub
services: iot-hub
ms.openlocfilehash: 67f0d9eb1fdac603ee82d568644e8ad8550d1c80
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "82024782"
---
# <a name="best-practices-for-device-configuration-within-an-iot-solution"></a>Najważniejsze wskazówki dotyczące konfiguracji urządzenia w ramach rozwiązania IoT

Automatyczne zarządzanie urządzeniami w usłudze Azure IoT Hub automatyzuje wiele powtarzających się i złożonych zadań związanych z zarządzaniem flotami dużych urządzeń w całym cyklu ich życia. W tym artykule zdefiniowano wiele najlepszych rozwiązań dla różnych ról zaangażowanych w opracowywanie i obsługę rozwiązania IoT.

* **Producent/integrator sprzętu IoT:** Producenci sprzętu IoT, integratorzy montujący sprzęt różnych producentów lub dostawcy dostarczający sprzęt do wdrożenia IoT produkowanego lub zintegrowanego przez innych dostawców. Zaangażowany w rozwój i integrację oprogramowania układowego, wbudowanych systemów operacyjnych i wbudowanego oprogramowania.

* **Deweloper rozwiązań IoT:** Rozwój rozwiązania IoT jest zazwyczaj wykonywane przez dewelopera rozwiązania. Ten deweloper może być częścią wazy w domu zespołu lub integratora systemu specjalizującego się w tym działaniu. Deweloper rozwiązania IoT może od podstaw tworzyć różne składniki rozwiązania IoT, integrować różne komponenty standardowe lub typu open source lub dostosowywać [akcelerator rozwiązań IoT.](/azure/iot-accelerators/)

* **Operator rozwiązania IoT:** Po wdrożeniu rozwiązania IoT wymaga ono długoterminowych operacji, monitorowania, uaktualnień i konserwacji. Zadania te mogą być wykonywane przez zespół we w domu, który składa się ze specjalistów technologii informatycznych, zespołów operacyjnych i konserwacji sprzętu oraz specjalistów od domeny, którzy monitorują prawidłowe zachowanie całej infrastruktury IoT.

## <a name="understand-automatic-device-management-for-configuring-iot-devices-at-scale"></a>Poznaj automatyczne zarządzanie urządzeniami do konfigurowania urządzeń IoT na dużą skalę

Automatyczne zarządzanie urządzeniami obejmuje wiele korzyści [bliźniacze urządzenia](iot-hub-devguide-device-twins.md) i [bliźniacze moduły](iot-hub-devguide-module-twins.md) do synchronizacji pożądanych i zgłoszonych stanów między chmurą a urządzeniami. [Automatyczne konfiguracje urządzeń](iot-hub-auto-device-config.md) automatycznie aktualizują duże zestawy bliźniąt bliźniąt i podsumowują postęp i zgodność. W poniższych krokach wysokiego poziomu opisano sposób opracowywania i stosowania automatycznego zarządzania urządzeniami:

* **Producent/integrator sprzętu IoT** implementuje funkcje zarządzania urządzeniami w osadzonej aplikacji przy użyciu [bliźniaczych reprezentacji urządzeń.](iot-hub-devguide-device-twins.md) Funkcje te mogą obejmować aktualizacje oprogramowania układowego, instalację i aktualizację oprogramowania oraz zarządzanie ustawieniami.

* **Deweloper rozwiązania IoT** implementuje warstwę zarządzania operacjami zarządzania urządzeniami przy użyciu [bliźniąt bliźniąt urządzeń](iot-hub-devguide-device-twins.md) i [automatycznych konfiguracji urządzeń.](iot-hub-auto-device-config.md) Rozwiązanie powinno obejmować definiowanie interfejsu operatora do wykonywania zadań związanych z zarządzaniem urządzeniami.

* **Operator rozwiązania IoT** używa rozwiązania IoT do wykonywania zadań związanych z zarządzaniem urządzeniami, w szczególności do grupowania urządzeń, inicjowania zmian konfiguracji, takich jak aktualizacje oprogramowania układowego, monitorowania postępu i rozwiązywania pojawiających się problemów.

## <a name="iot-hardware-manufacturerintegrator"></a>Producent/integrator sprzętu IoT

Poniżej przedstawiono najlepsze rozwiązania dla producentów sprzętu i integratorów zajmujących się tworzeniem oprogramowania wbudowanego:

* **Zaimplementuj [bliźniacze urządzenia:](iot-hub-devguide-device-twins.md)** Bliźniacze obiekty bliźniacze urządzeń umożliwiają synchronizację żądanej konfiguracji z chmury oraz raportowanie bieżącej konfiguracji i właściwości urządzenia. Najlepszym sposobem zaimplementowania bliźniąt urządzeń w osadzonych aplikacjach jest za pośrednictwem [zestawów SDK usługi Azure IoT.](https://github.com/Azure/azure-iot-sdks) Bliźniacze urządzenia najlepiej nadają się do konfiguracji, ponieważ:

    * Obsługa komunikacji dwukierunkowej.
    * Zezwalaj zarówno na podłączone, jak i odłączone stany urządzenia.
    * Postępuj zgodnie z zasadą spójności ostatecznej.
    * Są w pełni krzepalne w chmurze.

* **Struktura bliźniaczej reprezentacji urządzenia do zarządzania urządzeniami:** Bliźniacza reprezentacja urządzenia powinna być tak skonstruowana, aby właściwości zarządzania urządzeniami były logicznie pogrupowane w sekcje. W ten sposób umożliwi zmiany konfiguracji, które mają być izolowane bez wpływu na inne sekcje bliźniaczej reprezentacji. Na przykład utwórz sekcję w obrębie żądanych właściwości oprogramowania układowego, inną sekcję oprogramowania i trzecią sekcję ustawień sieciowych. 

* **Zgłoś atrybuty urządzenia, które są przydatne do zarządzania urządzeniami:** Atrybuty takie jak fizyczne tworzenie i model urządzeń, oprogramowanie układowe, system operacyjny, numer seryjny i inne identyfikatory są przydatne do raportowania i jako parametry dla zmian konfiguracji kierowania.

* **Zdefiniuj główne stany stanu raportowania i postępu:** Stany najwyższego poziomu powinny być wyliczone, aby można je było zgłaszać do operatora. Na przykład aktualizacja oprogramowania układowego będzie raportować stan jako Bieżący, Pobieranie, Stosowanie, W toku i Błąd. Zdefiniuj dodatkowe pola, aby uzyskać więcej informacji na temat każdego stanu.

## <a name="iot-solution-developer"></a>Deweloper rozwiązań IoT

Poniżej przedstawiono najlepsze rozwiązania dla deweloperów rozwiązań IoT, którzy budują systemy oparte na platformie Azure:

* **Zaimplementuj [bliźniacze urządzenia:](iot-hub-devguide-device-twins.md)** Bliźniacze obiekty bliźniacze urządzeń umożliwiają synchronizację żądanej konfiguracji z chmury oraz raportowanie bieżącej konfiguracji i właściwości urządzenia. Najlepszym sposobem wdrożenia bliźniąt urządzeń w aplikacjach rozwiązań w chmurze jest za pośrednictwem [zestawów SDK usługi Azure IoT.](https://github.com/Azure/azure-iot-sdks) Bliźniacze urządzenia najlepiej nadają się do konfiguracji, ponieważ:

    * Obsługa komunikacji dwukierunkowej.
    * Zezwalaj zarówno na podłączone, jak i odłączone stany urządzenia.
    * Postępuj zgodnie z zasadą spójności ostatecznej.
    * Są w pełni krzepalne w chmurze.

* **Organizowanie urządzeń przy użyciu bliźniaczych tagów urządzenia:** Rozwiązanie powinno umożliwić operatorowi definiowanie pierścieni jakości lub innych zestawów urządzeń na podstawie różnych strategii wdrażania, takich jak kanarek. Organizacja urządzeń może być zaimplementowana w ramach rozwiązania przy użyciu bliźniaczych tagów urządzeń i [zapytań.](iot-hub-devguide-query-language.md) Organizacja urządzeń jest niezbędna, aby umożliwić bezpieczne i dokładne wdrożenie konfiguracji.

* **Wdrażanie [automatycznych konfiguracji urządzeń:](iot-hub-auto-device-config.md)** Automatyczne konfiguracje urządzeń wdrażają i monitorują zmiany konfiguracji dużych zestawów urządzeń IoT za pośrednictwem bliźniąt urządzeń.

   Automatyczne konfiguracje urządzeń docelowe zestawy bliźniaczych reprezentacji urządzeń za pośrednictwem **warunku docelowego,** który jest zapytanie na tagi bliźniaczej reprezentacji urządzenia lub zgłoszonych właściwości. **Zawartość docelowa** to zestaw żądanych właściwości, które zostaną ustawione w obrębie bliźniaczych urządzeń docelowych. Zawartość docelowa powinna być zgodna z strukturą bliźniaczej reprezentacji urządzenia zdefiniowaną przez producenta/integratora sprzętu IoT. **Metryki** są zapytania na bliźniaczej reprezentacji urządzenia zgłaszane właściwości i należy również wyrównać z bliźniaczej struktury urządzenia zdefiniowane przez producenta/integratora sprzętu IoT.

   Automatyczne konfiguracje urządzeń są uruchamiane po raz pierwszy krótko po utworzeniu konfiguracji, a następnie w odstępach pięciu minut. Korzystają również z usługi IoT Hub wykonywania operacji bliźniaczej reprezentacji urządzeń z szybkością, która nigdy nie przekroczy [limitów ograniczania](iot-hub-devguide-quotas-throttling.md) dla odczytów bliźniaczej reprezentacji urządzenia i aktualizacji.

* **Użyj [usługi inicjowania obsługi urządzeń:](../iot-dps/how-to-manage-enrollments.md)** Deweloperzy rozwiązań powinni używać usługi inicjowania obsługi administracyjnej urządzeń do przypisywania bliźniaczych tagów urządzeń do nowych urządzeń, tak aby były one automatycznie konfigurowane przez **automatyczne konfiguracje urządzeń,** które są przeznaczone dla bliźniaczych reprezentacji za pomocą tego tagu. 

## <a name="iot-solution-operator"></a>Operator rozwiązania IoT

Poniżej przedstawiono najlepsze rozwiązania dla operatorów rozwiązań IoT, którzy korzystają z rozwiązania IoT utworzonego na platformie Azure:

* **Organizowanie urządzeń do zarządzania:** Rozwiązanie IoT należy zdefiniować lub zezwolić na tworzenie pierścieni jakości lub innych zestawów urządzeń na podstawie różnych strategii wdrażania, takich jak kanarek. Zestawy urządzeń będą używane do wprowadzania zmian konfiguracji i wykonywania innych operacji zarządzania urządzeniami na dużą skalę.

* **Wykonuj zmiany konfiguracji za pomocą stopniowego wdrażania:**  Stopniowe wdrażanie jest ogólnym procesem, w którym operator wdraża zmiany w rozszerzonym zestawie urządzeń IoT. Celem jest stopniowe wprowadzanie zmian w celu zmniejszenia ryzyka wprowadzania zmian na szeroką skalę.Operator powinien użyć interfejsu rozwiązania do utworzenia [automatycznej konfiguracji urządzenia,](iot-hub-auto-device-config.md) a warunek kierowania powinien być ukierunkowany na początkowy zestaw urządzeń (takich jak grupa kanarek). Operator powinien następnie sprawdzić poprawność zmiany konfiguracji w początkowym zestawie urządzeń.

   Po zakończeniu sprawdzania poprawności operator zaktualizuje automatyczną konfigurację urządzenia, aby uwzględnić większy zestaw urządzeń. Operator powinien również ustawić priorytet dla konfiguracji być wyższe niż inne konfiguracje obecnie kierowane do tych urządzeń. Wdrożenie można monitorować przy użyciu metryk zgłaszanych przez automatyczną konfigurację urządzenia.

* **Wykonywanie wycofywania w przypadku błędów lub błędnych konfiguracji:**  Automatyczna konfiguracja urządzenia, która powoduje błędy lub błędne konfiguracje można wycofać, zmieniając **warunek kierowania,** tak aby urządzenia nie spełniają już warunek kierowania. Upewnij się, że inna automatyczna konfiguracja urządzenia o niższym priorytecie jest nadal przeznaczona dla tych urządzeń. Sprawdź, czy wycofywanie powiodło się, wyświetlając metryki: wycofana konfiguracja nie powinna już wyświetlać stanu nieukierunkowanych urządzeń, a metryki drugiej konfiguracji powinny teraz zawierać liczby urządzeń, które są nadal kierowane.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o implementowaniu bliźniąt urządzeń w [aplikacji Understand and use device twins in IoT Hub](iot-hub-devguide-device-twins.md).

* Przejdź przez kroki, aby utworzyć, zaktualizować lub usunąć automatyczną konfigurację urządzenia w [obszarze Konfigurowanie i monitorowanie urządzeń IoT na dużą skalę](iot-hub-auto-device-config.md).

* Zaimplementuj wzorzec aktualizacji oprogramowania układowego przy użyciu bliźniąt bliźniąt urządzeń i automatycznych konfiguracji urządzeń w [samouczku: Zaimplementuj proces aktualizacji oprogramowania układowego urządzenia](tutorial-firmware-update.md).
