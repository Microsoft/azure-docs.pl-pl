---
title: Programowanie bez zestawu Azure IoT SDK | Microsoft Docs
description: Przewodnik dla deweloperów — informacje i linki do tematów, których można użyć do kompilowania aplikacji urządzeń i aplikacji zaplecza bez korzystania z zestawu Azure IoT SDK.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/12/2020
ms.custom:
- mqtt
- amqp
- 'Role: IoT Device'
- 'Role: Cloud Development'
ms.openlocfilehash: ae8b01522a248b8b1dbdd255a9fcd55f16cf2369
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96461702"
---
# <a name="develop-without-using-an-azure-iot-hub-sdk"></a>Programowanie bez użycia zestawu Azure IoT Hub SDK

Ten temat zawiera przydatne informacje i linki dla deweloperów, którzy chcą opracowywać aplikacje urządzeń lub zaplecza bez korzystania z zestawów SDK usługi Azure IoT.

Firma Microsoft zdecydowanie odradza korzystanie z zestawu Azure IoT SDK. Zestawy SDK urządzeń i usług Azure IoT są publikowane na wielu popularnych platformach. Zestawy SDK oferują wygodną warstwę, która obsługuje większość złożoności podstawowego protokołu komunikacyjnego, w tym połączenie z urządzeniem i ponowne połączenie, a także zasady ponawiania. Zestawy SDK są regularnie aktualizowane, aby zapewnić najnowsze funkcje udostępniane przez IoT Hub oraz aktualizacje zabezpieczeń. Korzystanie z zestawów SDK może pomóc w skróceniu czasu opracowywania i czasu na konserwację kodu. Aby dowiedzieć się więcej na temat zestawów SDK usługi Azure IoT, zobacz [zestawy SDK urządzeń i usług Azure IoT](iot-hub-devguide-sdks.md). Aby uzyskać więcej szczegółowych informacji na temat korzyści wynikających z używania zestawu SDK usługi Azure IoT, zapoznaj się z [korzyściami z używania zestawów SDK usługi Azure IoT i pułapek, aby uniknąć braku wpisu w](https://azure.microsoft.com/en-us/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/) blogu.

Chociaż IoT Hub obsługuje AMQP, AMQP za pośrednictwem obiektów WebSockets, HTTPS, MQTT i MQTT za pośrednictwem usługi WebSockets na potrzeby komunikacji z urządzeniami, zalecamy używanie MQTT, jeśli urządzenie je obsługuje.

## <a name="development-prerequisites"></a>Wymagania wstępne dotyczące programowania

Przed rozpoczęciem tworzenia aplikacji należy uzyskać gruntowną wiedzę na temat IoT Hub i funkcji, które mają być implementowane przez urządzenie lub aplikację zaplecza. Poniżej przedstawiono skróconą listę tematów, z którymi należy zapoznać się:

* Upewnij się, że znasz punkty końcowe udostępniane przez IoT Hub i protokoły obsługiwane przez poszczególne punkty końcowe. Aby dowiedzieć się więcej, zobacz [IoT Hub punkty końcowe](iot-hub-devguide-endpoints.md).

* W przypadku wybrania protokołu dla aplikacji urządzeń zdecydowanie zalecamy użycie MQTT. Przed wybraniem protokołu Pamiętaj o zrozumieniu ograniczeń narzuconych przez poszczególne z nich. Aby dowiedzieć się więcej, zobacz [Wybieranie protokołu komunikacyjnego](iot-hub-devguide-protocols.md).

* Aby zrozumieć uwierzytelnianie za pomocą IoT Hub, zobacz [Kontrola dostępu do IoT Hub](iot-hub-devguide-security.md).

[!INCLUDE [iot-hub-include-x509-ca-signed-support-note](../../includes/iot-hub-include-x509-ca-signed-support-note.md)]

## <a name="help-on-different-protocols"></a>Pomoc dotycząca różnych protokołów

Aby uzyskać pomoc dotyczącą korzystania z następujących protokołów bez zestawu Azure IoT SDK:

* Urządzenia lub aplikacje zaplecza w systemie **AMQP**, zobacz [AMQP support](iot-hub-amqp-support.md).

* Aplikacje urządzenia w systemie **MQTT**, zobacz [MQTT support](iot-hub-mqtt-support.md). Większość tego tematu traktuje się bezpośrednio przy użyciu protokołu MQTT. Zawiera również informacje dotyczące korzystania z [przykładowego repozytorium IoT MQTT](https://github.com/Azure-Samples/IoTMQTTSample). To repozytorium zawiera przykłady C, które umożliwiają wysyłanie komunikatów do IoT Hub przy użyciu biblioteki Mosquitto.

* Urządzenia lub aplikacje zaplecza na **protokole https** zapoznaj się z [interfejsem API REST usługi Azure IoT Hub](/rest/api/iothub/). Należy pamiętać, że zgodnie z [wymaganiami wstępnymi](#development-prerequisites)dotyczącymi programowania nie można używać uwierzytelniania urzędu certyfikacji X. 509 przy użyciu protokołu HTTPS.

W przypadku urządzeń zdecydowanie zalecamy użycie usługi MQTT, jeśli jest ona obsługiwana przez urządzenie.

## <a name="next-steps"></a>Następne kroki

* [Obsługa protokołu MQTT](iot-hub-mqtt-support.md)