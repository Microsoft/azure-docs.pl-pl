---
title: Ponowne Inicjowanie obsługi administracyjnej urządzeń na platformie Azure IoT Hub Device Provisioning Service
description: Dowiedz się, jak ponownie zainicjować obsługę administracyjną urządzeń za pomocą wystąpienia usługi Device Provisioning (DPS) i dlaczego trzeba to zrobić.
author: wesmc7777
ms.author: wesmc
ms.date: 04/04/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: e5cc5b557aa4dff793f7e87093eeb65028da4f8c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91839796"
---
# <a name="how-to-reprovision-devices"></a>Jak ponownie zainicjować obsługę urządzeń

W trakcie cyklu życia rozwiązania IoT typowe jest przenoszenie urządzeń między centrami IoT. Przyczyny tego przeniesienia mogą obejmować następujące scenariusze:

* **Geolokalizacja**: w miarę przemieszczania się urządzenia między lokalizacjami opóźnienie sieci jest ulepszane, ponieważ urządzenie jest migrowane do centrum IoT Hub bliżej każdej lokalizacji.

* **Wielodostępność**: urządzenie może być używane w ramach tego samego rozwiązania IoT, ale ponownie przypisane lub wydzierżawione do nowego klienta lub do lokacji klienta. Ten nowy klient może być serwisowany przy użyciu innego Centrum IoT Hub.

* **Zmiana rozwiązania**: urządzenie można przenieść do nowego lub zaktualizowanego rozwiązania IoT. To ponowne przypisanie może wymagać, aby urządzenie komunikuje się z nowym centrum IoT, które jest połączone z innymi składnikami zaplecza. 

* **Kwarantanna**: podobnie jak w przypadku zmiany rozwiązania. Urządzenie, które jest uszkodzone, naruszone lub nieaktualne, może zostać ponownie przypisane do centrum IoT Hub, gdzie wszystkie te czynności mogą zostać zaktualizowane i zawracają do zgodności. Po poprawnym działaniu urządzenie zostanie zmigrowane z powrotem do głównego centrum.

Aby zapoznać się z bardziej szczegółowym omówieniem ponownego inicjowania obsługi administracyjnej, zobacz temat IoT Hub reaprowizacji [urządzeń](concepts-device-reprovision.md).


## <a name="configure-the-enrollment-allocation-policy"></a>Konfigurowanie zasad alokacji rejestracji

Zasady alokacji określają, w jaki sposób urządzenia skojarzone z rejestracją zostaną przydzielone lub przypisane do centrum IoT po ponownym zainicjowaniu obsługi administracyjnej.

Poniższe kroki konfigurują zasady alokacji na potrzeby rejestracji urządzenia:

1. Zaloguj się do [Azure Portal](https://portal.azure.com) i przejdź do wystąpienia usługi Device Provisioning.

2. Kliknij pozycję **Zarządzaj rejestracjami**, a następnie kliknij grupę rejestracji lub rejestrację indywidualną, którą chcesz skonfigurować do ponownego aprowizacji. 

3. W obszarze **Wybierz sposób przypisywania urządzeń do centrów**wybierz jedną z następujących zasad alokacji:

    * **Najniższe opóźnienie**: te zasady przypisuje urządzenia do połączonej IoT Hub, co spowoduje, że komunikacja między urządzeniem i IoT Hub jest najmniejsza. Ta opcja umożliwia urządzeniu komunikację z najbliższym centrum IoT Hub w oparciu o lokalizację. 
    
    * **Dystrybucja równomiernie ważona**: te zasady dystrybuują urządzenia przez połączone centra IoT na podstawie wagi alokacji przypisanej do każdego połączonego Centrum IoT. Te zasady umożliwiają Równoważenie obciążenia urządzeń w grupie połączonych centrów na podstawie wag alokacji ustawionych w tych centrach. W przypadku aprowizacji urządzeń tylko do jednego IoT Hub zalecamy to ustawienie. Jest to ustawienie domyślne. 
    
    * **Konfiguracja statyczna**: te zasady wymagają, aby odpowiednie IoT Hub były wymienione w wpisie rejestracji dla urządzenia do aprowizacji. Te zasady umożliwiają wyznaczenie jednego określonego Centrum IoT Hub, do którego mają zostać przypisane urządzenia.

4. W obszarze **Wybierz centra IoT, do których ta grupa może być przypisana**, wybierz połączone centra IoT, które chcesz uwzględnić w ramach zasad alokacji. Opcjonalnie dodaj nowe połączone centrum IoT Hub za pomocą przycisku **Połącz nowy IoT Hub** .

    Przy **najniższych** zasadach alokacji opóźnienia wybrane centra zostaną uwzględnione w ocenie opóźnienia, aby określić najbliższy koncentrator do przypisywania urządzeń.

    Dzięki **równomiernie ważonej** zasadzie alokacji dystrybucji urządzenia będą równoważone obciążeniem w wybranych centrach na podstawie skonfigurowanych wag alokacji i ich bieżącego obciążenia urządzenia.

    Za pomocą zasad **statycznej alokacji konfiguracji** wybierz Centrum IoT, do którego chcesz przypisać urządzenia.

4. Kliknij przycisk **Zapisz**lub przejdź do następnej sekcji, aby ustawić zasady ponownego inicjowania obsługi administracyjnej.

    ![Wybieranie zasad alokacji rejestracji](./media/how-to-reprovision/enrollment-allocation-policy.png)



## <a name="set-the-reprovisioning-policy"></a>Ustawianie zasad ponownego inicjowania obsługi administracyjnej

1. Zaloguj się do [Azure Portal](https://portal.azure.com) i przejdź do wystąpienia usługi Device Provisioning.

2. Kliknij pozycję **Zarządzaj rejestracjami**, a następnie kliknij grupę rejestracji lub rejestrację indywidualną, którą chcesz skonfigurować do ponownego aprowizacji.

3. W obszarze **Wybierz sposób, w jaki dane urządzenia mają być obsługiwane po ponownym zainicjowaniu obsługi administracyjnej w innym Centrum IoT**, wybierz jedną z następujących zasad ponownego aprowizacji:

    * **Ponowne Inicjowanie obsługi administracyjnej i Migrowanie danych**: te zasady podejmują akcję, gdy urządzenia skojarzone z wpisem rejestracji przesyłają nowe żądanie aprowizacji. W zależności od konfiguracji wpisu rejestracji urządzenie może zostać ponownie przypisane do innego Centrum IoT Hub. Jeśli urządzenie zmienia centra IoT, Rejestracja urządzenia z początkowym Centrum IoT Hub zostanie usunięta. Wszystkie informacje o stanie urządzenia z tego początkowej usługi IoT Hub zostaną zmigrowane do nowego centrum IoT Hub. Podczas migracji stan urządzenia będzie raportowany jako **przypisanie**

    * **Ponownie Zainicjuj obsługę administracyjną i Zresetuj do konfiguracji początkowej**: te zasady podejmują akcję, gdy urządzenia skojarzone z wpisem rejestracji prześlą nowe żądanie aprowizacji. W zależności od konfiguracji wpisu rejestracji urządzenie może zostać ponownie przypisane do innego Centrum IoT Hub. Jeśli urządzenie zmienia centra IoT, Rejestracja urządzenia z początkowym Centrum IoT Hub zostanie usunięta. Początkowe dane konfiguracji, które otrzymały wystąpienie usługi aprowizacji po przyłączeniu urządzenia do nowego centrum IoT Hub. Podczas migracji stan urządzenia będzie raportowany jako **przypisanie**.

4. Kliknij przycisk **Zapisz** , aby włączyć ponowne Inicjowanie obsługi urządzenia na podstawie wprowadzonych zmian.

    ![Zrzut ekranu, który podświetla wprowadzone zmiany i przycisk Zapisz.](./media/how-to-reprovision/reprovisioning-policy.png)



## <a name="send-a-provisioning-request-from-the-device"></a>Wyślij żądanie aprowizacji z urządzenia

Aby urządzenia były ponownie inicjowane na podstawie zmian konfiguracji wprowadzonych w poprzednich sekcjach, te urządzenia muszą zażądać ponownego zainicjowania obsługi administracyjnej. 

Jak często urządzenie przesyła żądanie aprowizacji, zależy od tego scenariusza. Jednak zaleca się zaprogramowanie urządzeń w celu wysłania żądania aprowizacji do wystąpienia usługi aprowizacji przy ponownym uruchomieniu i obsługę [metody](../iot-hub/iot-hub-devguide-direct-methods.md) ręcznego wyzwalania aprowizacji na żądanie. Inicjowanie obsługi może być również wyzwalane przez ustawienie [odpowiedniej właściwości](../iot-hub/iot-hub-devguide-device-twins.md#desired-property-example). 

Zasady ponownego inicjowania obsługi w wpisie rejestracji określają, jak wystąpienie usługi Device Provisioning obsługuje te żądania aprowizacji, a jeśli dane stanu urządzenia mają być migrowane podczas ponownego inicjowania obsługi administracyjnej. Te same zasady są dostępne dla poszczególnych rejestracji i grup rejestracji:

Przykładowy kod wysyłania żądań aprowizacji z urządzenia podczas sekwencji rozruchu znajduje się w temacie [Obsługa administracyjna urządzenia symulowanego](quick-create-simulated-device.md).


## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej, zobacz temat [IoT Hub ponowne Inicjowanie obsługi administracyjnej urządzeń](concepts-device-reprovision.md) 
- Aby dowiedzieć się więcej, zobacz [Jak anulować obsługę administracyjną urządzeń, które wcześniej były obsługiwane](how-to-unprovision-devices.md) . 











