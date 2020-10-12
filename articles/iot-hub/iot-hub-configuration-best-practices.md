---
title: Najlepsze rozwiązania związane z konfiguracją urządzeń dla systemu Azure IoT Hub | Microsoft Docs
description: Poznaj najlepsze rozwiązania dotyczące korzystania z funkcji automatycznego zarządzania urządzeniami w celu zminimalizowania powtarzających się i złożonych zadań związanych z zarządzaniem urządzeniami IoT na dużą skalę.
author: robinsh
ms.author: robinsh
ms.date: 06/28/2019
ms.topic: conceptual
ms.service: iot-hub
services: iot-hub
ms.openlocfilehash: 67f0d9eb1fdac603ee82d568644e8ad8550d1c80
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "82024782"
---
# <a name="best-practices-for-device-configuration-within-an-iot-solution"></a>Najlepsze rozwiązania dotyczące konfiguracji urządzeń w ramach rozwiązania IoT

Automatyczne zarządzanie urządzeniami w systemie Azure IoT Hub automatyzuje wiele powtarzających się i złożonych zadań związanych z zarządzaniem dużymi flotami urządzeń w całości z ich cyklów życia. W tym artykule opisano wiele najlepszych rozwiązań dla różnych ról związanych z programowaniem i działaniem rozwiązania IoT.

* **Producent/Integrator sprzętu IoT:** Producenci sprzętu IoT, integratorzy asembler sprzętu od różnych producentów lub dostawcy dostarczający sprzęt do wdrożenia IoT wytwarzanego lub zintegrowanego przez innych dostawców. Dotyczy opracowywania i integracji oprogramowania układowego, osadzonych systemów operacyjnych i oprogramowania osadzonego.

* **Deweloper rozwiązań IoT:** Opracowywanie rozwiązania IoT jest zwykle wykonywane przez dewelopera rozwiązań. Ten deweloper może być częścią zespołu pracującego w domu lub integratora systemu w ramach tego działania. Deweloperzy rozwiązań IoT mogą opracowywać różne składniki rozwiązania IoT od podstaw, zintegrować różne składniki standardowe lub typu open-source lub dostosowując [Akcelerator rozwiązań IoT](/azure/iot-accelerators/).

* **Operator rozwiązania IoT:** Po wdrożeniu rozwiązania IoT wymagane jest długoterminowe operacje, monitorowanie, uaktualnienia i konserwacja. Te zadania mogą być wykonywane przez zespół pracujący w firmie, który składa się z specjalistów technologicznych, operacji sprzętowych i zespołów konserwacyjnych oraz specjalistów ds. domen, którzy monitorują prawidłowe zachowanie ogólnej infrastruktury IoT.

## <a name="understand-automatic-device-management-for-configuring-iot-devices-at-scale"></a>Informacje o automatycznym zarządzaniu urządzeniami do konfigurowania urządzeń IoT na dużą skalę

Automatyczne zarządzanie urządzeniami obejmuje wiele zalet [bliźniaczych reprezentacji urządzeń](iot-hub-devguide-device-twins.md) i [bliźniaczych reprezentacji modułu](iot-hub-devguide-module-twins.md) do synchronizowania żądanych i raportowanych stanów między chmurą i urządzeniami. [Automatyczne konfiguracje urządzeń](iot-hub-auto-device-config.md) automatycznie aktualizują duże zestawy bliźniaczych reprezentacji oraz podsumowania postępu i zgodności. Poniższe kroki wysokiego poziomu opisują sposób opracowania i użycia automatycznego zarządzania urządzeniami:

* **Producent/Integrator sprzętu IoT** implementuje funkcje zarządzania urządzeniami w aplikacji osadzonej przy użyciu usługi [Device bliźniaczych reprezentacji](iot-hub-devguide-device-twins.md). Te funkcje mogą obejmować aktualizacje oprogramowania układowego, instalację oprogramowania i aktualizację oraz zarządzanie ustawieniami.

* **Deweloperzy rozwiązań IoT** implementują warstwę zarządzania operacji zarządzania urządzeniami przy użyciu [bliźniaczych reprezentacji urządzeń](iot-hub-devguide-device-twins.md) i [konfiguracji urządzeń automatycznych](iot-hub-auto-device-config.md). Rozwiązanie powinno obejmować Definiowanie interfejsu operatora do wykonywania zadań związanych z zarządzaniem urządzeniami.

* **Operator rozwiązania IoT** korzysta z rozwiązania IoT do wykonywania zadań związanych z zarządzaniem urządzeniami, szczególnie w celu pogrupowania urządzeń, inicjowania zmian konfiguracji, takich jak aktualizacje oprogramowania układowego, monitorowania postępu i rozwiązywania problemów.

## <a name="iot-hardware-manufacturerintegrator"></a>Producent/Integrator sprzętu IoT

Poniżej przedstawiono najlepsze rozwiązania dla producentów sprzętu i integratorów zajmujących się tworzeniem oprogramowania osadzonego:

* **Zaimplementuj [bliźniaczych reprezentacji urządzenia](iot-hub-devguide-device-twins.md):** Bliźniaczych reprezentacji urządzeń umożliwia synchronizowanie żądanej konfiguracji z chmury oraz na potrzeby raportowania bieżącej konfiguracji i właściwości urządzenia. Najlepszym sposobem implementacji bliźniaczych reprezentacji urządzeń w aplikacjach osadzonych jest użycie [zestawów SDK usługi Azure IoT](https://github.com/Azure/azure-iot-sdks). Bliźniaczych reprezentacji urządzeń najlepiej nadają się do konfiguracji, ponieważ:

    * Obsługa komunikacji dwukierunkowej.
    * Zezwalaj na stan urządzeń połączonych i odłączonych.
    * Postępuj zgodnie z zasadą spójności ostatecznej.
    * Są w pełni queriable w chmurze.

* **Struktura sznurka urządzenia do zarządzania urządzeniami:** Sznurki urządzenia powinny być strukturalne, tak że właściwości zarządzania urządzeniami są logicznie pogrupowane w sekcje. Wykonanie tej czynności spowoduje odizolowanie zmian konfiguracji bez wpływu na inne sekcje sznurka. Na przykład utwórz sekcję w obszarze odpowiednie właściwości dla oprogramowania układowego, inną sekcję dla oprogramowania i trzecią sekcję ustawień sieciowych. 

* **Zgłoś atrybuty urządzenia, które są przydatne do zarządzania urządzeniami:** Atrybuty, takie jak marka i model urządzenia fizycznego, oprogramowanie układowe, system operacyjny, numer seryjny i inne identyfikatory, są przydatne do raportowania i jako parametry dla zmian konfiguracji docelowej.

* **Zdefiniuj główne Stany dla stanu raportowania i postępu:** Stany najwyższego poziomu powinny być wyliczane, aby mogły być zgłaszane do operatora. Na przykład aktualizacja oprogramowania układowego spowodowałaby zgłoszenie stanu jako bieżący, pobrania, zastosowania, w toku i błędu. Zdefiniuj dodatkowe pola, aby uzyskać więcej informacji na temat każdego stanu.

## <a name="iot-solution-developer"></a>Deweloper rozwiązań IoT

Poniżej przedstawiono najlepsze rozwiązania dla deweloperów rozwiązań IoT, którzy tworzą systemy oparte na platformie Azure:

* **Zaimplementuj [bliźniaczych reprezentacji urządzenia](iot-hub-devguide-device-twins.md):** Bliźniaczych reprezentacji urządzeń umożliwia synchronizowanie żądanej konfiguracji z chmury oraz na potrzeby raportowania bieżącej konfiguracji i właściwości urządzenia. Najlepszym sposobem implementacji bliźniaczych reprezentacji urządzeń w aplikacjach rozwiązań w chmurze jest użycie [zestawów SDK usługi Azure IoT](https://github.com/Azure/azure-iot-sdks). Bliźniaczych reprezentacji urządzeń najlepiej nadają się do konfiguracji, ponieważ:

    * Obsługa komunikacji dwukierunkowej.
    * Zezwalaj na stan urządzeń połączonych i odłączonych.
    * Postępuj zgodnie z zasadą spójności ostatecznej.
    * Są w pełni queriable w chmurze.

* **Organizuj urządzenia przy użyciu znaczników bliźniaczych urządzeń:** Rozwiązanie powinno pozwolić operatorowi definiować pierścienie jakości lub inne zestawy urządzeń w oparciu o różne strategie wdrażania, takie jak Kanaryjskie. Organizacja urządzenia może być implementowana w ramach rozwiązania przy użyciu tagów i [zapytań](iot-hub-devguide-query-language.md)o sznurki urządzeń. Organizacja urządzeń jest niezbędna do zapewnienia bezpiecznego i dokładnego wyróżniania konfiguracji.

* **Zaimplementuj [Automatyczne konfiguracje urządzeń](iot-hub-auto-device-config.md):** Automatyczne konfiguracje urządzeń wdrażają i monitorują zmiany konfiguracji w dużych zestawach urządzeń IoT za pośrednictwem urządzenia bliźniaczych reprezentacji.

   Automatyczne konfiguracje urządzeń docelowy zestaw bliźniaczych reprezentacji urządzeń za pomocą **warunku docelowego,** który jest kwerendą w przypadku tagów sznurka urządzenia lub zgłaszanych właściwości. **Zawartość docelowa** to zbiór żądanych właściwości, które zostaną ustawione w ramach docelowego urządzenia bliźniaczych reprezentacji. Zawartość docelowa powinna być wyrównana do struktury sznurka urządzenia zdefiniowanej przez producenta/integratora sprzętu IoT. **Metryki** są zapytania dotyczące właściwości z sznurka urządzenia i powinny również być wyrównane z strukturą sznurka urządzenia zdefiniowaną przez producenta/Integrator sprzętu IoT.

   Automatyczne konfiguracje urządzeń są uruchamiane po raz pierwszy wkrótce po utworzeniu konfiguracji, a następnie co pięć minut. Mogą oni również korzystać z IoT Hub wykonywania wieloosiowych operacji na urządzeniach, które nigdy nie przekroczą [limitów ograniczania przepustowości](iot-hub-devguide-quotas-throttling.md) urządzeń i aktualizacji.

* **Korzystanie z [usługi Device Provisioning](../iot-dps/how-to-manage-enrollments.md):** deweloperzy rozwiązań powinni używać usługi Device Provisioning do przypisywania znaczników bliźniaczych urządzeń do nowych urządzeń, tak że będą one automatycznie konfigurowane przez **Automatyczne konfiguracje urządzeń** , które są przeznaczone dla bliźniaczych reprezentacji z tym tagiem. 

## <a name="iot-solution-operator"></a>Operator rozwiązania IoT

Poniżej przedstawiono najlepsze rozwiązania dla operatorów rozwiązań IoT korzystających z rozwiązania IoT utworzonego na platformie Azure:

* **Organizuj urządzenia do zarządzania:** Rozwiązanie IoT powinno definiować lub zezwalać na tworzenie pierścieni jakości lub innych zestawów urządzeń w oparciu o różne strategie wdrażania, takie jak Kanaryjskie. Zestawy urządzeń będą używane do wdrożenia zmian konfiguracji i wykonywania innych operacji zarządzania urządzeniami w skali.

* **Przeprowadź zmiany w konfiguracji przy użyciu etapowego wdrożenia:**  Etapowe wycofywanie jest procesem, w którym operator wdraża zmiany w rozszerzonym zestawie urządzeń IoT. Celem jest stopniowe wprowadzanie zmian w celu zmniejszenia ryzyka związanego z wprowadzaniem zmian w skali szerokiej.Operator powinien używać interfejsu rozwiązania, aby utworzyć [automatyczną konfigurację urządzenia](iot-hub-auto-device-config.md) , a warunek docelowy powinien wskazywać na początkowy zestaw urządzeń (na przykład grupę Kanaryjskie). Następnie operator powinien sprawdzić poprawność konfiguracji w początkowym zestawie urządzeń.

   Po zakończeniu walidacji operator zaktualizuje automatyczną konfigurację urządzenia w celu uwzględnienia większego zestawu urządzeń. Operator powinien również ustawić priorytet konfiguracji na wyższy niż inne konfiguracje, które są obecnie przeznaczone dla tych urządzeń. Przeprowadzenie można monitorować, korzystając z metryk raportowanych przez automatyczną konfigurację urządzenia.

* **Wykonaj wycofywanie w razie błędów lub błędnych konfiguracji:**  Automatyczna konfiguracja urządzeń, która powoduje, że błędy lub błędne konfiguracje można wycofać, zmieniając **warunek określania wartości docelowej** , aby urządzenia nie spełniały warunku określania wartości docelowej. Upewnij się, że dla tych urządzeń nadal są dostępne inne automatyczne konfiguracje urządzeń o niższym priorytecie. Sprawdź, czy wycofywanie zakończyło się pomyślnie, wyświetlając metryki: Konfiguracja wycofana nie powinna już wskazywać stanu nieprzeznaczonych urządzeń, a metryki drugiej konfiguracji powinny teraz zawierać liczniki dla urządzeń, które są nadal docelowe.

## <a name="next-steps"></a>Następne kroki

* Więcej informacji na temat implementowania bliźniaczych reprezentacji urządzeń w temacie [Omówienie i używanie bliźniaczych reprezentacji urządzeń w IoT Hub](iot-hub-devguide-device-twins.md).

* Zapoznaj się z instrukcjami, aby utworzyć, zaktualizować lub usunąć automatyczną konfigurację urządzenia w obszarze [Konfigurowanie i monitorowanie urządzeń IoT](iot-hub-auto-device-config.md)w odpowiedniej skali.

* Implementowanie wzorca aktualizacji oprogramowania układowego przy użyciu usługi Device bliźniaczych reprezentacji i automatycznych konfiguracji urządzeń w [samouczku: implementowanie procesu aktualizacji oprogramowania układowego urządzenia](tutorial-firmware-update.md).
