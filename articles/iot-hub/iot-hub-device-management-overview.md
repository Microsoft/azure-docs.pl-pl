---
title: Omówienie zarządzania urządzeniami za pomocą usługi Azure IoT Hub | Microsoft Docs
description: Omówienie zarządzania urządzeniami w usłudze Azure IoT hu — cykl życia i zarządzanie urządzeniami w przedsiębiorstwie, takie jak, ponowne uruchamianie, Resetowanie do ustawień fabrycznych, aktualizacja oprogramowania układowego, konfiguracja, bliźniaczych reprezentacji urządzeń, zapytania i zadania.
author: bzurcher
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/24/2017
ms.author: briz
ms.custom:
- 'Role: Cloud Development'
- 'Role: IoT Device'
- 'Role: System Architecture'
ms.openlocfilehash: 9ff8ebeaf7c4a219bc403c81dfea62f4657a1fd8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92148339"
---
# <a name="overview-of-device-management-with-iot-hub"></a>Omówienie zarządzania urządzeniami za pomocą usługi IoT Hub

Usługa Azure IoT Hub udostępnia funkcje i model rozszerzeń, który umożliwia deweloperom urządzeń i deweloperom zaplecza tworzenie niezawodnych rozwiązań służących do zarządzania urządzeniami IoT. Istnieje wiele urządzeń — od czujników z rygorystycznymi ograniczeniami i pojedynczych mikrokontrolerów przeznaczonych do ściśle określonych celów po zaawansowane bramy służące do kierowania komunikacją grup urządzeń.  Ponadto przypadki użycia i wymagania dla operatorów IoT znacznie się różnią w zależności od branży.  Pomimo tych różnic zarządzanie urządzeniami usługi IoT Hub udostępnia możliwości, wzorce i biblioteki kodu, które spełniają potrzeby różnego zestawu urządzeń i użytkowników końcowych.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Kluczowym elementem procesu tworzenia prawidłowo działającego rozwiązania IoT w przedsiębiorstwie jest opracowanie strategii dotyczącej metody bieżącego zarządzania kolekcją urządzeń przez operatorów. Operatorzy urządzeń IoT muszą mieć do dyspozycji proste oraz niezawodne narzędzia i aplikacje, dzięki którym będą mogli skoncentrować się na ważniejszych aspektach swojej pracy. Ten artykuł zawiera:

* Krótkie omówienie podejścia do zarządzania urządzeniami w usłudze Azure IoT Hub.
* Opis typowych zasad dotyczących zarządzania urządzeniami.
* Opis cyklu życia urządzenia.
* Przegląd typowych wzorców zarządzania urządzeniami.

## <a name="device-management-principles"></a>Zasady zarządzania urządzeniami

Środowisko IoT charakteryzuje się pewnymi problemami w zakresie zarządzania urządzeniami i każde rozwiązanie klasy korporacyjnej musi uwzględniać następujące zasady:

![Ilustracja dotycząca zasad zarządzania urządzeniami](media/iot-hub-device-management-overview/image4.png)

* **Skalowanie i automatyzacja**: rozwiązania IoT wymagają prostych narzędzi, które pozwalają zautomatyzować rutynowe zadania i umożliwiają stosunkowo małej grupie pracowników operacyjnych zarządzanie milionami urządzeń. Na co dzień operatorzy chcą zdalnie i zbiorczo obsługiwać operacje związane z urządzeniami oraz chcą być powiadamiani tylko o problemach wymagających ich bezpośredniej uwagi.

* **Otwartość i zgodność**: ekosystem urządzeń jest bardzo zróżnicowany. Narzędzia do zarządzania muszą być zgodne z wieloma klasami urządzeń, platformami i protokołami. Operatorzy muszą mieć możliwość zapewnienia obsługi dla wielu typów urządzeń — od wbudowanych układów wykonujących pojedyncze procesy po zaawansowane i w pełni funkcjonalne komputery.

* **Uwzględnianie kontekstu**: środowiska IoT są dynamiczne i nieustannie się zmieniają. Najważniejszą kwestią jest niezawodność usługi. Operacje zarządzania urządzeniami muszą uwzględniać następujące czynniki w celu zapewnienia, że przestój w ramach konserwacji nie wpłynie na operacje o krytycznym znaczeniu dla firmy ani nie spowoduje powstania niebezpiecznych warunków:

    * Okna obsługi w umowie SLA
    * Stany sieci i zasilania
    * Warunki użycia
    * Geolokalizacja urządzenia

* **Obsługa wielu ról**: obsługa unikatowych przepływów pracy i procesów ról operacji IoT jest niezwykle ważna. Pracownicy operacyjni muszą zachować zgodność z ograniczeniami wewnętrznych działów IT.  Muszą również znaleźć niezawodne sposoby udostępniania nadzorcom i innym osobom odpowiedzialnym za zarządzanie informacji dotyczących odpowiednich operacji na urządzeniach.

## <a name="device-lifecycle"></a>Cykl wsparcia technicznego urządzenia
Istnieje zestaw ogólnych etapów zarządzania urządzeniami, które są wspólne dla wszystkich projektów IoT. W usłudze Azure IoT istnieje pięć etapów cyklu życia urządzenia:

![Pięć faz cyklu życia urządzenia usługi Azure IoT: planowanie, aprowizowanie, konfigurowanie, monitorowanie, wycofywanie](./media/iot-hub-device-management-overview/image5.png)

W każdym z tych pięciu etapów istnieje kilka wymagań dotyczących operatora urządzenia, które powinny zostać spełnione, aby zapewnić kompletne rozwiązanie:

* **Planowanie**: umożliwienie operatorom utworzenia schematu metadanych urządzenia, który pozwoli na łatwe oraz dokładne wykonanie zapytania dotyczącego grupy urządzeń i użycie jej na potrzeby zbiorczych operacji zarządzania. Bliźniaczej reprezentacji urządzenia można użyć do przechowywania metadanych tego urządzenia w postaci tagów i właściwości.
  
    *Dalsze odczytywanie*: 
    * [Wprowadzenie do zarządzania bliźniaczymi reprezentacjami urządzeń](iot-hub-node-node-twin-getstarted.md)
    * [Opis bliźniaczych reprezentacji urządzeń](iot-hub-devguide-device-twins.md)
    * [Jak korzystać z właściwości sznurka urządzenia](tutorial-device-twins.md)
    * [Najlepsze rozwiązania dotyczące konfiguracji urządzeń w ramach rozwiązania IoT](iot-hub-configuration-best-practices.md)

* **Aprowizacja**: bezpieczna aprowizacja nowych urządzeń w usłudze IoT Hub oraz umożliwienie operatorom natychmiastowego wykrywania możliwości urządzeń.  Za pomocą rejestru tożsamości usługi IoT Hub można tworzyć elastyczne tożsamości i poświadczenia urządzeń. Aprowizacja może być wykonywana zbiorczo przy użyciu zadania. Możliwe jest tworzenie urządzeń w taki sposób, aby raportowały swoje możliwości i warunki za pośrednictwem właściwości urządzeń w bliźniaczej reprezentacji urządzenia.
  
    *Dalsze odczytywanie*: 
    * [Zarządzanie tożsamościami urządzeń](iot-hub-devguide-identity-registry.md)
    * [Zbiorcze Zarządzanie tożsamościami urządzeń](iot-hub-bulk-identity-mgmt.md)
    * [Jak korzystać z właściwości sznurka urządzenia](tutorial-device-twins.md)
    * [Najlepsze rozwiązania dotyczące konfiguracji urządzeń w ramach rozwiązania IoT](iot-hub-configuration-best-practices.md)
    * [Usługa Azure IoT Hub Device Provisioning](https://azure.microsoft.com/documentation/services/iot-dps)

* **Konfigurowanie**: ułatwienie zbiorczego stosowania zmian konfiguracji i aktualizacji oprogramowania układowego na urządzeniach przy zachowaniu kondycji i bezpieczeństwa. Wykonaj te operacje zarządzania urządzeniami zbiorczo, używając odpowiednich właściwości lub bezpośrednich metod i zadań emisji.
  
    *Dalsze odczytywanie*:
    * [Jak korzystać z właściwości sznurka urządzenia](tutorial-device-twins.md)
    * [Konfigurowanie i monitorowanie urządzeń IoT na dużą skalę](./iot-hub-automatic-device-management.md)
    * [Najlepsze rozwiązania dotyczące konfiguracji urządzeń w ramach rozwiązania IoT](iot-hub-configuration-best-practices.md)

* **Monitorowanie**: monitorowanie ogólnej kondycji kolekcji urządzeń i stanu trwających operacji w celu ostrzegania operatorów o problemach, które mogą wymagać ich uwagi.  Zastosuj bliźniaczą reprezentacją urządzenia, aby umożliwić urządzeniom raportowanie w czasie rzeczywistym warunków pracy i stanu operacji aktualizacji. Twórz zaawansowane raporty pulpitu nawigacyjnego, które służą do udostępniania najważniejszych w danym momencie problemów przy użyciu zapytań bliźniaczych reprezentacji urządzeń.
  
    *Dalsze odczytywanie*: 
    * [Jak korzystać z właściwości sznurka urządzenia](tutorial-device-twins.md)
    * [IoT Hub język zapytań dla bliźniaczych reprezentacji urządzeń, zadań i routingu komunikatów](iot-hub-devguide-query-language.md)
    * [Konfigurowanie i monitorowanie urządzeń IoT na dużą skalę](./iot-hub-automatic-device-management.md)
    * [Najlepsze rozwiązania dotyczące konfiguracji urządzeń w ramach rozwiązania IoT](iot-hub-configuration-best-practices.md)

* **Wycofywanie**: zastępowanie lub likwidowanie urządzeń po awarii, cyklu uaktualniania lub po zakończeniu okresu istnienia usługi.  Użyj bliźniaczej reprezentacji urządzenia, aby zarządzać informacjami o urządzeniu, jeśli trwa zastępowanie urządzenia fizycznego lub jeśli jest ono archiwizowane w przypadku wycofywania. Za pomocą rejestru tożsamości usługi IoT Hub można bezpiecznie odwołać tożsamości i poświadczenia urządzeń.
  
    *Dalsze odczytywanie*: 
    * [Jak korzystać z właściwości sznurka urządzenia](tutorial-device-twins.md)
    * [Zarządzanie tożsamościami urządzeń](iot-hub-devguide-identity-registry.md)

## <a name="device-management-patterns"></a>Wzorce zarządzania urządzeniami

Usługa IoT Hub udostępnia przedstawiony poniżej zestaw wzorców zarządzania urządzeniami. W [samouczkach dotyczących zarządzania urządzeniami](iot-hub-node-node-device-management-get-started.md) znajduje się bardziej szczegółowy opis sposobu rozszerzenia tych wzorców w celu dopasowania ich do danego scenariusza oraz sposobu projektowania nowych wzorców na podstawie tych szablonów podstawowych.

* **Ponowny rozruch**: aplikacja zaplecza informuje urządzenie za pośrednictwem metody bezpośredniej o zainicjowaniu ponownego uruchomienia.  Urządzenie aktualizuje stan ponownego uruchomienia urządzenia za pomocą zgłoszonych właściwości.
  
    ![Ilustracja dotycząca wzorca ponownego uruchamiania zarządzania urządzeniami](./media/iot-hub-device-management-overview/reboot-pattern.png)

* **Resetowanie do ustawień fabrycznych**: aplikacja zaplecza informuje urządzenie za pośrednictwem metody bezpośredniej o zainicjowaniu resetowania do ustawień fabrycznych. Urządzenie aktualizuje stan resetowania urządzenia do ustawień fabrycznych za pomocą zgłoszonych właściwości.
  
    ![Ilustracja dotycząca wzorca resetowania urządzenia do ustawień fabrycznych zarządzania urządzeniami](./media/iot-hub-device-management-overview/facreset-pattern.png)

* **Konfiguracja**: aplikacja zaplecza korzysta z odpowiednich właściwości w celu skonfigurowania oprogramowania działającego na urządzeniu. Urządzenie aktualizuje stan konfiguracji urządzenia za pomocą zgłoszonych właściwości.
  
    ![Ilustracja dotycząca wzorca konfiguracji zarządzania urządzeniami](./media/iot-hub-device-management-overview/configuration-pattern.png)

* **Aktualizacja oprogramowania układowego**: aplikacja zaplecza używa automatycznej konfiguracji zarządzania urządzeniami, aby wybrać urządzenia do odebrania aktualizacji, określić urządzenia, na których ma zostać znaleziona aktualizacja, oraz monitorować proces aktualizacji. Urządzenie inicjuje wieloetapowy proces pobierania, weryfikowania i stosowania obrazu oprogramowania układowego, a następnie ponownego uruchamiania urządzenia przed ponownym nawiązaniem połączenia z usługą IoT Hub. W trakcie tego wieloetapowego procesu urządzenie używa zgłoszonych właściwości do zaktualizowania postępu i stanu urządzenia.
  
    ![Ilustracja dotycząca wzorca aktualizacji oprogramowania układowego zarządzania urządzeniami](media/iot-hub-device-management-overview/fwupdate-pattern.png)

* **Raportowanie postępu i stanu**: zaplecze rozwiązania uruchamia zapytania o pojedynczej urządzeniu, w zestawie urządzeń, w celu raportowania stanu i postępu akcji wykonywanych na urządzeniach.
  
    ![Ilustracja dotycząca postępu i stanu raportowania zarządzania urządzeniami](./media/iot-hub-device-management-overview/report-progress-pattern.png)

## <a name="next-steps"></a>Następne kroki

Funkcji, wzorców i bibliotek kodu udostępnianych przez usługę IoT Hub na potrzeby zarządzania urządzeniami możesz użyć do tworzenia aplikacji IoT, które spełniają wymagania operatora IoT przedsiębiorstwa na każdym etapie cyklu życia urządzenia.

Aby kontynuować zapoznawanie się z funkcjami zarządzania urządzeniami usługi IoT Hub, zobacz samouczek [Get started with device management](iot-hub-node-node-device-management-get-started.md) (Wprowadzenie do zarządzania urządzeniami).