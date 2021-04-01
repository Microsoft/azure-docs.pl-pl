---
title: Azure IoT Hub Device Provisioning Service — koncepcje dotyczące urządzeń
description: Opis pojęć dotyczących ponownego inicjowania obsługi administracyjnej urządzeń IoT Hub Device Provisioning Service platformy Azure (DPS)
author: wesmc7777
ms.author: wesmc
ms.date: 04/04/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 9653a584382584d982c55008a6e8547de28691b7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91842856"
---
# <a name="iot-hub-device-reprovisioning-concepts"></a>Pojęcia dotyczące ponownego inicjowania obsługi administracyjnej urządzenia IoT Hub

W trakcie cyklu życia rozwiązania IoT typowe jest przenoszenie urządzeń między centrami IoT. Przyczyny tego przeniesienia mogą obejmować następujące scenariusze:

* **Geolokalizacja/geoopóźnienie**: w miarę przemieszczania się urządzenia między lokalizacjami opóźnienie sieci jest zwiększane, ponieważ urządzenie jest migrowane do bliższego Centrum IoT Hub.

* **Wielodostępność**: urządzenie może być używane w ramach tego samego rozwiązania IoT i ponownie przypisane do nowego klienta lub do lokacji klienta. Ten nowy klient może być serwisowany przy użyciu innego Centrum IoT Hub.

* **Zmiana rozwiązania**: urządzenie można przenieść do nowego lub zaktualizowanego rozwiązania IoT. To ponowne przypisanie może wymagać, aby urządzenie komunikuje się z nowym centrum IoT, które jest połączone z innymi składnikami zaplecza.

* **Kwarantanna**: podobnie jak w przypadku zmiany rozwiązania. Urządzenie, które jest nieprawidłowo, naruszone lub nieaktualne, może zostać ponownie przypisane do centrum IoT Hub, które może być aktualizowane i przywracane tylko w celu zapewnienia zgodności. Po poprawnym działaniu urządzenie zostanie zmigrowane z powrotem do głównego centrum.

Ponowne Inicjowanie obsługi administracyjnej w ramach usługi Device Provisioning rozwiązuje te wymagania. Urządzenia można automatycznie przypisywać do nowych centrów IoT na podstawie zasad ponownego aprowizacji skonfigurowanych we wpisie rejestracji urządzenia.

## <a name="device-state-data"></a>Dane stanu urządzenia

Dane stanu urządzenia [składają się z urządzeń i urządzeń](../iot-hub/iot-hub-devguide-device-twins.md) . Te dane są przechowywane w wystąpieniu usługi Device Provisioning i centrum IoT, do którego urządzenie jest przypisane.

![Diagram pokazujący, jak obsługa administracyjna współpracuje z usługą Device Provisioning.](./media/concepts-device-reprovisioning/dps-provisioning.png)

Po wstępnym aprowizacji urządzenia za pomocą wystąpienia usługi Device Provisioning należy wykonać następujące czynności:

1. Urządzenie wysyła żądanie aprowizacji do wystąpienia usługi Device Provisioning. Wystąpienie usługi uwierzytelnia tożsamość urządzenia na podstawie wpisu rejestracji i tworzy początkową konfigurację danych stanu urządzenia. Wystąpienie usługi przypisuje urządzenie do centrum IoT Hub na podstawie konfiguracji rejestracji i zwraca przypisanie usługi IoT Hub do urządzenia.

2. Wystąpienie usługi aprowizacji zawiera kopię wszystkich początkowych danych stanu urządzenia do przypisanego Centrum IoT. Urządzenie nawiązuje połączenie z przypisanym Centrum IoT Hub i rozpoczyna operacje.

W miarę upływu czasu dane stanu urządzenia w usłudze IoT Hub mogą być aktualizowane przez [operacje urządzenia](../iot-hub/iot-hub-devguide-device-twins.md#device-operations) i [operacje zaplecza](../iot-hub/iot-hub-devguide-device-twins.md#back-end-operations). Początkowe informacje o stanie urządzenia przechowywane w wystąpieniu usługi Device Provisioning nie zostały naruszone. To nienaruszone dane stanu urządzenia to początkowa konfiguracja.

![Inicjowanie obsługi administracyjnej przy użyciu usługi Device Provisioning](./media/concepts-device-reprovisioning/dps-provisioning-2.png)

W zależności od tego, jak urządzenie przechodzi między centrami IoT, może być również konieczne przeprowadzenie migracji stanu urządzenia z poprzedniego Centrum IoT Hub do nowego centrum IoT Hub. Ta migracja jest obsługiwana przez ponowne inicjowanie zasad w usłudze Device Provisioning.

## <a name="reprovisioning-policies"></a>Zasady ponownego inicjowania obsługi administracyjnej

W zależności od scenariusza urządzenie zazwyczaj wysyła żądanie do wystąpienia usługi aprowizacji przy ponownym uruchomieniu. Obsługuje ona również metodę ręcznego wyzwalania aprowizacji na żądanie. Zasady ponownego inicjowania obsługi wpisu rejestracji określają, jak wystąpienie usługi Device Provisioning Service obsługuje te żądania aprowizacji. Zasady określają również, czy dane stanu urządzenia mają być migrowane podczas ponownego inicjowania obsługi administracyjnej. Te same zasady są dostępne dla poszczególnych rejestracji i grup rejestracji:

* **Ponowne Inicjowanie obsługi administracyjnej i Migrowanie danych**: te zasady są domyślne dla nowych wpisów rejestracji. Te zasady podejmują akcję, gdy urządzenia skojarzone z wpisem rejestracji przesyłają nowe żądanie (1). W zależności od konfiguracji wpisu rejestracji urządzenie może zostać ponownie przypisane do innego Centrum IoT Hub. Jeśli urządzenie zmienia centra IoT, Rejestracja urządzenia z początkowym Centrum IoT Hub zostanie usunięta. Zaktualizowane informacje o stanie urządzenia z tego początkowej usługi IoT Hub zostaną zmigrowane do nowego centrum IoT Hub (2). Podczas migracji stan urządzenia będzie raportowany jako **przypisanie**.

    ![Diagram pokazujący, że zasady podejmują akcję, gdy urządzenia skojarzone z wpisem rejestracji przesyłają nowe żądanie.](./media/concepts-device-reprovisioning/dps-reprovisioning-migrate.png)

* **Ponownie Zainicjuj obsługę administracyjną i Zresetuj do konfiguracji początkowej**: te zasady podejmują akcję, gdy urządzenia skojarzone z wpisem rejestracji prześlą nowe żądanie aprowizacji (1). W zależności od konfiguracji wpisu rejestracji urządzenie może zostać ponownie przypisane do innego Centrum IoT Hub. Jeśli urządzenie zmienia centra IoT, Rejestracja urządzenia z początkowym Centrum IoT Hub zostanie usunięta. Początkowe dane konfiguracji, które otrzymały wystąpienie usługi aprowizacji po przyłączeniu urządzenia do nowego centrum IoT Hub (2). Podczas migracji stan urządzenia będzie raportowany jako **przypisanie**.

    Te zasady są często używane do resetowania do ustawień fabrycznych bez zmiany centrów IoT.

    ![Diagram pokazujący, jak zasady podejmują akcję, gdy urządzenia skojarzone z wpisem rejestracji przesyłają nowe żądanie aprowizacji.](./media/concepts-device-reprovisioning/dps-reprovisioning-reset.png)

* **Nigdy nie należy ponownie** udostępniać: urządzenie nigdy nie jest przypisywane do innego centrum. Te zasady zapewniają zarządzanie zgodnością z poprzednimi wersjami.

### <a name="managing-backwards-compatibility"></a>Zarządzanie zgodnością z poprzednimi wersjami

Przed 2018 września, przydziały urządzeń do centrów IoT mają zachowanie podczas działania. Gdy urządzenie przeszedł z powrotem przez proces aprowizacji, zostanie ono przypisane z powrotem do tego samego centrum IoT.

W przypadku rozwiązań, które miały zależność od tego zachowania, usługa aprowizacji obejmuje zgodność z poprzednimi wersjami. To zachowanie jest obecnie obsługiwane w przypadku urządzeń zgodnie z następującymi kryteriami:

1. Urządzenia łączą się z wersją interfejsu API przed udostępnieniem natywnej obsługi administracyjnej w usłudze Device Provisioning. Zapoznaj się z poniższą tabelą interfejsu API.

2. Wpis rejestracji dla urządzeń nie ma ustawionych zasad ponownego inicjowania obsługi administracyjnej.

Ta zgodność gwarantuje, że wcześniej wdrożone urządzenia mają takie samo zachowanie, jakie jest obecne podczas wstępnego testowania. Aby zachować poprzednie zachowanie, nie należy zapisywać zasad ponownego aprowizacji dla tych rejestracji. W przypadku ustawienia zasad ponownego inicjowania obsługi administracyjnej zasady ponownego aprowizacji mają pierwszeństwo przed zachowaniem. Dzięki umożliwieniu zasad ponownego inicjowania obsługi administracyjnej klienci mogą aktualizować zachowanie urządzeń bez konieczności ponownego obrazu urządzenia.

Poniższy wykres przepływu ułatwia pokazanie, kiedy zachowanie jest obecne:

![Wykres przepływu zgodności z poprzednimi wersjami](./media/concepts-device-reprovisioning/reprovisioning-compatibility-flow.png)

W poniższej tabeli przedstawiono wersje interfejsu API przed udostępnieniem natywnej obsługi ponownego aprowizacji w usłudze Device Provisioning:

| Interfejs API REST | ZESTAW SDK JĘZYKA C | Zestaw SDK dla języka Python |  Zestaw SDK dla języka Node | Zestaw SDK Java | Zestaw SDK .NET |
| -------- | ----- | ---------- | --------- | -------- | -------- |
| [2018-04-01 i starsze](/rest/api/iot-dps/createorupdateindividualenrollment/createorupdateindividualenrollment#uri-parameters) | [1.2.8 i starsze](https://github.com/Azure/azure-iot-sdk-c/blob/master/version.txt) | [1.4.2 i starsze](https://github.com/Azure/azure-iot-sdk-python/blob/0a549f21f7f4fc24bc036c1d2d5614e9544a9667/device/iothub_client_python/src/iothub_client_python.cpp#L53) | [1.7.3 lub wcześniejszy](https://github.com/Azure/azure-iot-sdk-node/blob/074c1ac135aebb520d401b942acfad2d58fdc07f/common/core/package.json#L3) | [1.13.0 lub starszy](https://github.com/Azure/azure-iot-sdk-java/blob/794c128000358b8ed1c4cecfbf21734dd6824de9/device/iot-device-client/pom.xml#L7) | [1.1.0 lub starszy](https://github.com/Azure/azure-iot-sdk-csharp/blob/9f7269f4f61cff3536708cf3dc412a7316ed6236/provisioning/device/src/Microsoft.Azure.Devices.Provisioning.Client.csproj#L20)

> [!NOTE]
> Te wartości i linki mogą ulec zmianie. Jest to tylko symbol zastępczy próbujący określić, gdzie wersje mogą być określone przez klienta i jakie są oczekiwane wersje.

## <a name="next-steps"></a>Następne kroki

* [Jak ponownie zainicjować obsługę urządzeń](how-to-reprovision.md)
