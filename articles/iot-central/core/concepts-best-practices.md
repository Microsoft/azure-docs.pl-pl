---
title: Najlepsze rozwiązania dotyczące projektowania urządzeń w systemie Azure IoT Central | Microsoft Docs
description: W tym artykule opisano najlepsze rozwiązania dotyczące łączności urządzeń w usłudze Azure IoT Central
author: dominicbetts
ms.author: dobett
ms.date: 03/03/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom:
- device-developer
ms.openlocfilehash: e8ae8b0173e53c0a46ded1a2690175e367997c9f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102054706"
---
# <a name="best-practices-for-device-development"></a>Najlepsze rozwiązania dotyczące projektowania urządzeń

*Ten artykuł ma zastosowanie do deweloperów urządzeń.*

Te zalecenia pokazują, jak zaimplementować urządzenia, aby skorzystać z wbudowanego odzyskiwania po awarii i automatycznego skalowania w IoT Central.

Na poniższej liście przedstawiono przepływ wysokiego poziomu, gdy urządzenie łączy się z IoT Central:

1. Użyj usługi DPS, aby zainicjować obsługę administracyjną urządzenia i uzyskać parametry połączenia z urządzeniem.

1. Połącz się z wewnętrznym IoT Hub punktu końcowego IoT Central za pomocą parametrów połączenia. Wysyłaj dane do i odbieraj dane z aplikacji IoT Central.

1. Jeśli urządzenie uzyskuje błędy połączeń, w zależności od typu błędu, ponów próbę połączenia lub zainicjuj ponownie urządzenie.

## <a name="use-dps-to-provision-the-device"></a>Udostępnianie urządzenia za pomocą DPS

Aby zainicjować obsługę administracyjną urządzenia przy użyciu usługi DPS, użyj identyfikatora zakresu, poświadczeń i identyfikatora urządzenia w aplikacji IoT Central. Aby dowiedzieć się więcej na temat typów poświadczeń, zobacz [Rejestrowanie grupowe X. 509](concepts-get-connected.md#x509-group-enrollment) i [rejestrowanie grup SAS](concepts-get-connected.md#sas-group-enrollment). Aby dowiedzieć się więcej o identyfikatorach urządzeń, zobacz [rejestrowanie urządzeń](concepts-get-connected.md#device-registration).

Po pomyślnym zalogowaniu usługa DPS zwraca parametry połączenia, za pomocą których urządzenie może nawiązać połączenie z aplikacją IoT Central. Aby rozwiązać problemy z błędami aprowizacji, zobacz [Sprawdzanie stanu aprowizacji urządzenia](troubleshoot-connection.md#check-the-provisioning-status-of-your-device).

Urządzenie może buforować parametry połączenia w celu użycia ich w późniejszych połączeniach. Jednak urządzenie musi być przygotowane do [obsługi błędów połączeń](#handle-connection-failures).

## <a name="connect-to-iot-central"></a>Połącz z IoT Central

Połącz się z wewnętrznym IoT Hub punktu końcowego IoT Central za pomocą parametrów połączenia. Połączenie umożliwia wysyłanie danych telemetrycznych do aplikacji IoT Central, synchronizowanie wartości właściwości z aplikacją IoT Central i odpowiadanie na polecenia wysyłane przez aplikację IoT Central.

## <a name="handle-connection-failures"></a>Obsługa błędów połączeń

W celu skalowania lub odzyskiwania po awarii IoT Central mogą zaktualizować swoje podstawowe Centrum IoT. Aby zapewnić łączność, kod urządzenia powinien obsługiwać konkretne błędy połączenia przez ustanowienie połączenia z nowym punktem końcowym IoT Hub.

Jeśli podczas łączenia się z urządzeniem wystąpią następujące błędy, należy ponownie wykonać krok aprowizacji za pomocą usługi DPS, aby uzyskać nowe parametry połączenia. Te błędy oznaczają parametry połączenia używane przez urządzenie nie są już prawidłowe:

- Nieosiągalny punkt końcowy IoT Hub.
- Wygasły token zabezpieczający.
- Urządzenie wyłączone w IoT Hub.

Jeśli podczas łączenia się z urządzeniem wystąpią następujące błędy, należy użyć strategii wycofywania, aby ponowić próbę nawiązania połączenia. Te błędy oznaczają parametry połączenia, które są używane przez urządzenie, są nadal prawidłowe, ale przejściowe warunki uniemożliwiają nawiązanie połączenia z urządzeniem:

- Zablokowane urządzenie operatora.
- Wystąpił błąd wewnętrzny 500 z usługi.

Aby dowiedzieć się więcej o kodach błędów urządzeń, zobacz [Rozwiązywanie problemów z połączeniami urządzeń](troubleshoot-connection.md).

## <a name="next-steps"></a>Następne kroki

Jeśli jesteś deweloperem urządzenia, Oto kilka sugerowanych następnych kroków:

- Zapoznaj się z przykładowym kodem, który pokazuje, jak używać tokenów SAS w [samouczku: Tworzenie i łączenie aplikacji klienckiej z aplikacją usługi Azure IoT Central](tutorial-connect-device.md)
- Dowiedz się, jak [łączyć urządzenia z certyfikatami X. 509 za pomocą zestawu SDK urządzeń Node.js dla IoT Central aplikacji](how-to-connect-devices-x509.md)
- Dowiedz się, jak [monitorować łączność z urządzeniem przy użyciu interfejsu wiersza polecenia platformy Azure](./howto-monitor-devices-azure-cli.md)
- Dowiedz się, jak [zdefiniować nowy typ urządzenia IoT w aplikacji usługi Azure IoT Central](./howto-set-up-template.md)
- Przeczytaj informacje o [Azure IoT Edge urządzeniach i platformie Azure IoT Central](./concepts-iot-edge.md)
