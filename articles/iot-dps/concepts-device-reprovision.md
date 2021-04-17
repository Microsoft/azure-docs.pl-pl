---
title: Azure IoT Hub Device Provisioning Service — pojęcia dotyczące urządzeń
description: W tym artykule opisano pojęcia dotyczące ponownego aprowizowania Azure IoT Hub Device Provisioning Service (DPS)
author: wesmc7777
ms.author: wesmc
ms.date: 04/16/2021
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: fbc83ec62c10fae00e371cd9ad95cf2860495fad
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107575772"
---
# <a name="iot-hub-device-reprovisioning-concepts"></a>IoT Hub dotyczące ponownego aprowizowania urządzeń

W trakcie cyklu życia rozwiązania IoT często przenosi się urządzenia między centrami IoT. Przyczyny tego przeniesienia mogą obejmować następujące scenariusze:

* **Geolokalizacja/GeoLatency:** gdy urządzenie przemieszcza się między lokalizacjami, opóźnienie sieci jest poprawiane przez migrowanie urządzenia do bliższego centrum IoT.

* **Wielosieć:** urządzenie może być używane w ramach tego samego rozwiązania IoT i ponownie przypisana do nowego klienta lub witryny klienta. Ten nowy klient może być serwisowany przy użyciu innego centrum IoT.

* **Zmiana rozwiązania:** urządzenie można przenieść do nowego lub zaktualizowanego rozwiązania IoT. To ponowne przypisanie może wymagać, aby urządzenie komunikło się z nowym centrum IoT, które jest połączone z innymi składnikami back-end.

* **Kwarantanna:** Podobnie jak w przypadku zmiany rozwiązania. Urządzenie, które działa nieprawidłowo, zostało naruszone lub jest aktualne, może zostać ponownie przypisana do centrum IoT, które może aktualizować i wrócić do zgodności. Gdy urządzenie działa prawidłowo, jest następnie migrowane z powrotem do głównego centrum.

Obsługa ponownego aprowizowania w usłudze Device Provisioning Service odpowiada na te potrzeby. Urządzenia można automatycznie ponownie przypisać do nowych centrów IoT na podstawie zasad ponownego aprowizowania skonfigurowanych we wpisie rejestracji urządzenia.

## <a name="device-state-data"></a>Dane stanu urządzenia

Dane stanu urządzenia składają się z możliwości [bliźniaczej reprezentacji](../iot-hub/iot-hub-devguide-device-twins.md) urządzenia i urządzenia. Te dane są przechowywane w wystąpieniu usługi Device Provisioning Service i centrum IoT, do których przypisano urządzenie.

![Diagram przedstawiający sposób, w jaki aprowizowanie współpracuje z usługą Device Provisioning Service.](./media/concepts-device-reprovisioning/dps-provisioning.png)

Gdy urządzenie jest początkowo aprowowane przy użyciu wystąpienia usługi Device Provisioning Service, wykonywane są następujące kroki:

1. Urządzenie wysyła żądanie aprowizowania do wystąpienia usługi Device Provisioning Service. Wystąpienie usługi uwierzytelnia tożsamość urządzenia na podstawie wpisu rejestracji i tworzy początkową konfigurację danych stanu urządzenia. Wystąpienie usługi przypisuje urządzenie do centrum IoT Hub na podstawie konfiguracji rejestracji i zwraca to przypisanie centrum IoT hub do urządzenia.

2. Wystąpienie usługi aprowizowania przekazuje kopię wszystkich początkowych danych o stanie urządzenia do przypisanego centrum IoT. Urządzenie łączy się z przypisanym centrum IoT i rozpoczyna operacje.

W czasie dane stanu urządzenia w centrum IoT hub mogą być aktualizowane za pomocą operacji [urządzenia](../iot-hub/iot-hub-devguide-device-twins.md#device-operations) i [operacji na załocie.](../iot-hub/iot-hub-devguide-device-twins.md#back-end-operations) Informacje o początkowym stanie urządzenia przechowywane w wystąpieniu usługi Device Provisioning Service pozostają niezmienione. Te niezmienione dane stanu urządzenia to konfiguracja początkowa.

![Aprowizowanie za pomocą usługi Device Provisioning Service](./media/concepts-device-reprovisioning/dps-provisioning-2.png)

W zależności od scenariusza, gdy urządzenie przemieszcza się między centrami IoT, może być również konieczne zmigrowanie stanu urządzenia zaktualizowanego w poprzednim centrum IoT do nowego centrum IoT. Ta migracja jest obsługiwana przez ponowne aprowizowanie zasad w usłudze Device Provisioning Service.

## <a name="reprovisioning-policies"></a>Zasady ponownego aprowizowania

W zależności od scenariusza urządzenie zazwyczaj wysyła żądanie do wystąpienia usługi aprowizowania po ponownym uruchomieniu. Obsługuje również metodę ręcznego wyzwalania aprowacji na żądanie. Zasady ponownego aprowizowania we wpisie rejestracji określają, jak wystąpienie usługi device provisioning service obsługuje te żądania aprowizowania. Zasady określają również, czy dane stanu urządzenia powinny być migrowane podczas ponownego aprowizowania. Te same zasady są dostępne dla rejestracji indywidualnych i grup rejestracji:

* **Ponowne aprowizowanie i migrowanie danych:** te zasady są domyślne dla nowych wpisów rejestracji. Te zasady są akcjami, gdy urządzenia skojarzone z wpisem rejestracji przesłały nowe żądanie (1). W zależności od konfiguracji wpisu rejestracji urządzenie może zostać ponownie przypisane do innego centrum IoT. Jeśli urządzenie zmienia centra IoT Hub, rejestracja urządzenia w początkowym centrum IoT zostanie usunięta. Zaktualizowane informacje o stanie urządzenia z tego początkowego centrum IoT zostaną zmigrowane do nowego centrum IoT (2). Podczas migracji stan urządzenia będzie zgłaszany jako **Przypisywanie**.

    ![Diagram przedstawiający, że zasady podejmuje działania, gdy urządzenia skojarzone z wpisem rejestracji przesłały nowe żądanie.](./media/concepts-device-reprovisioning/dps-reprovisioning-migrate.png)

* **Ponowne aprowizowanie i** resetowanie do początkowej konfiguracji: ta zasada podejmuje działania, gdy urządzenia skojarzone z wpisem rejestracji prześliją nowe żądanie aprowowania (1). W zależności od konfiguracji wpisu rejestracji urządzenie może zostać ponownie przypisane do innego centrum IoT. Jeśli urządzenie zmienia centra IoT Hub, rejestracja urządzenia w początkowym centrum IoT zostanie usunięta. Dane konfiguracji początkowej odebrane przez wystąpienie usługi aprowizowania podczas aprowizowania urządzenia są dostarczane do nowego centrum IoT (2). Podczas migracji stan urządzenia będzie zgłaszany jako **Przypisywanie**.

    Te zasady są często używane do resetowania do ustawień fabrycznych bez zmiany centrów IoT Hub.

    ![Diagram przedstawiający sposób działania zasad, gdy urządzenia skojarzone z wpisem rejestracji przesyłają nowe żądanie aprowowania.](./media/concepts-device-reprovisioning/dps-reprovisioning-reset.png)

* **Nigdy nie należy ponownie aprowizować:** urządzenie nigdy nie jest ponownie przypisana do innego centrum. Te zasady są dostępne do zarządzania zgodnością z poprzednimi wersjami.

> [!NOTE]
> Niezależnie od zasad ponownego aprowizowania usługi DPS zawsze wywołają niestandardowy webhook alokacji w przypadku, gdy dla urządzenia istnieje nowa metoda [ReturnData.](how-to-send-additional-data.md) Jeśli zasady ponownego inicjowania obsługi administracyjnej są ustawione na nigdy ponowne aprowizowanie, zostanie wywołany obiekt webhook, ale urządzenie nie zmieni przypisanego centrum.

### <a name="managing-backwards-compatibility"></a>Zarządzanie zgodnością z poprzednimi wersjami

Przed wrześniem 2018 r. przypisania urządzeń do centrów IoT były wyklejne. Gdy urządzenie powróci do procesu aprowizowania, zostanie przypisane tylko do tego samego centrum IoT.

W przypadku rozwiązań, które są zależne od tego zachowania, usługa aprowizowania obejmuje zgodność z poprzednimi wersjami. To zachowanie jest obecnie utrzymywane dla urządzeń zgodnie z następującymi kryteriami:

1. Urządzenia nawiązą połączenie z wersją interfejsu API przed udostępnieniem natywnej obsługi ponownego aprowizowania w usłudze Device Provisioning. Zapoznaj się z tabelą interfejsów API poniżej.

2. Wpis rejestracji dla urządzeń nie ma ustawionych zasad ponownego aprowizowania.

Ta zgodność zapewnia, że wcześniej wdrożone urządzenia mają takie samo zachowanie, jak podczas wstępnego testowania. Aby zachować poprzednie zachowanie, nie zapisuj zasad ponownego aprowizowania w tych rejestracjach. Jeśli zasady ponownego aprowizowania są ustawione, zasady ponownego aprowizowania mają pierwszeństwo przed zachowaniem. Dzięki umożliwieniu pierwszeństwa zasad ponownego aprowizowania klienci mogą aktualizować zachowanie urządzenia bez konieczności ponownego odtwarzanie urządzenia.

Poniższy wykres blokowy pomaga pokazać, kiedy zachowanie jest obecne:

![wykres blokowy zgodności z poprzednimi wersjami](./media/concepts-device-reprovisioning/reprovisioning-compatibility-flow.png)

W poniższej tabeli przedstawiono wersje interfejsu API przed udostępnieniem natywnej obsługi ponownego aprowizowania w usłudze Device Provisioning Service:

| Interfejs API REST | C SDK | Zestaw SDK dla języka Python |  Zestaw SDK dla języka Node | Zestaw SDK Java | Zestaw SDK .NET |
| -------- | ----- | ---------- | --------- | -------- | -------- |
| [2018-04-01 i starsze](/rest/api/iot-dps/createorupdateindividualenrollment/createorupdateindividualenrollment#uri-parameters) | [1.2.8 i starsze](https://github.com/Azure/azure-iot-sdk-c/blob/master/version.txt) | [1.4.2 i starsze](https://github.com/Azure/azure-iot-sdk-python/blob/0a549f21f7f4fc24bc036c1d2d5614e9544a9667/device/iothub_client_python/src/iothub_client_python.cpp#L53) | [1.7.3 lub starsze](https://github.com/Azure/azure-iot-sdk-node/blob/074c1ac135aebb520d401b942acfad2d58fdc07f/common/core/package.json#L3) | [1.13.0 lub starsze](https://github.com/Azure/azure-iot-sdk-java/blob/794c128000358b8ed1c4cecfbf21734dd6824de9/device/iot-device-client/pom.xml#L7) | [1.1.0 lub starsze](https://github.com/Azure/azure-iot-sdk-csharp/blob/9f7269f4f61cff3536708cf3dc412a7316ed6236/provisioning/device/src/Microsoft.Azure.Devices.Provisioning.Client.csproj#L20)

> [!NOTE]
> Te wartości i linki mogą ulec zmianie. Jest to tylko próba symbolu zastępczego w celu określenia, gdzie klient może określić wersje i jakie będą oczekiwane wersje.

## <a name="next-steps"></a>Następne kroki

* [Jak ponownie aprowizować urządzenia](how-to-reprovision.md)
