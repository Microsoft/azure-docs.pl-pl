---
title: Pojęcia dotyczące architektury w Azure IoT Central | Microsoft Docs
description: Ten artykuł zawiera wprowadzenie do kluczowych pojęć związanych z architekturą Azure IoT Central
author: dominicbetts
ms.author: dobett
ms.date: 12/19/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: bcda4ca252101ed1505f71a1b5f9fe9a0d8d16b9
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107728396"
---
# <a name="azure-iot-central-architecture"></a>Architektura usługi Azure IoT Central

Ten artykuł zawiera omówienie kluczowych pojęć dotyczących Azure IoT Central architektury.

## <a name="devices"></a>Urządzenia

Urządzenia wymieniają dane z Azure IoT Central aplikacji. Urządzenie może:

- Wysyłaj pomiary, takie jak dane telemetryczne.
- Zsynchronizuj ustawienia z aplikacją.

W usłudze Azure IoT Central dane, które urządzenie może wymieniać z aplikacją, są określone w szablonie urządzenia. Aby uzyskać więcej informacji na temat szablonów urządzeń, zobacz [Szablony urządzeń](concepts-device-templates.md).

Aby dowiedzieć się więcej o tym, jak urządzenia łączą się z Azure IoT Central, zobacz [Łączność urządzenia](concepts-get-connected.md).

## <a name="azure-iot-edge-devices"></a>Urządzenia usługi Azure IoT Edge

Podobnie jak w przypadku urządzeń utworzonych przy użyciu zestawów SDK usługi [Azure IoT,](https://github.com/Azure/azure-iot-sdks)można również połączyć Azure IoT Edge [z](../../iot-edge/about-iot-edge.md) aplikacją IoT Central azure. IoT Edge umożliwia uruchamianie analizy w chmurze i niestandardowej logiki bezpośrednio na urządzeniach IoT zarządzanych przez usługę IoT Central. Środowisko IoT Edge uruchomieniowe umożliwia:

- Instaluje i aktualizuje pakiety robocze na urządzeniu.
- Zachowaj IoT Edge zabezpieczeń na urządzeniu.
- Zapewnia nieprzerwane działanie modułów usługi IoT Edge.
- Przesyła raporty o kondycji modułów do chmury na potrzeby zdalnego monitorowania.
- Zarządza komunikacją między podrzędnymi urządzeniami liścia usługi IoT Edge, między modułami na urządzeniu usługi IoT Edge oraz między urządzeniem usługi IoT Edge a chmurą.

![Azure IoT Central z Azure IoT Edge](./media/concepts-architecture/iotedge.png)

IoT Central zapewnia następujące możliwości dla IoT Edge urządzeń:

- Szablony urządzeń do opisywania możliwości urządzenia IoT Edge, takie jak:
  - Możliwość przekazywania manifestu wdrożenia, która ułatwia zarządzanie manifestem dla floty urządzeń.
  - Moduły uruchamiane na IoT Edge urządzeniu.
  - Dane telemetryczne wysyłane przez każdy moduł.
  - Właściwości poszczególnych modułów są raportami.
  - Polecenia, na które reaguje każdy moduł.
  - Relacje między urządzeniem IoT Edge a urządzeniem nadrzędnym.
  - Właściwości chmury, które nie są przechowywane na IoT Edge urządzeniu.
  - Dostosowania, pulpity nawigacyjne i formularze, które są częścią IoT Central aplikacji.

  Aby uzyskać więcej informacji, zobacz artykuł [Connect Azure IoT Edge devices to an Azure IoT Central application (Łączenie](./concepts-iot-edge.md) urządzeń Azure IoT Central z aplikacją).

- Możliwość aprowizowania urządzeń IoT Edge na dużą skalę przy użyciu usługi Azure IoT Device Provisioning
- Reguły i akcje.
- Niestandardowe pulpity nawigacyjne i analiza.
- Ciągły eksport danych telemetrycznych z IoT Edge danych.

### <a name="iot-edge-device-types"></a>IoT Edge typów urządzeń

IoT Central klasyfikuje IoT Edge typów urządzeń w następujący sposób:

- Urządzenia liścia. Urządzenie IoT Edge może mieć podrzędne urządzenia liścia, ale te urządzenia nie są aprowowane w IoT Central.
- Urządzenia bramy z urządzeniami nadrzędnymi. Zarówno urządzenie bramy, jak i urządzenia podrzędne są aprowowane w IoT Central

![IoT Central with IoT Edge Overview](./media/concepts-architecture/gatewayedge.png)

### <a name="iot-edge-patterns"></a>IoT Edge wzorców

IoT Central obsługuje następujące wzorce IoT Edge urządzeń:

#### <a name="iot-edge-as-leaf-device"></a>IoT Edge jako urządzenie liścia

![IoT Edge jako urządzenie liścia](./media/concepts-architecture/edgeasleafdevice.png)

Urządzenie IoT Edge jest aprowowane w IoT Central, a wszystkie urządzenia podrzędne i ich dane telemetryczne są reprezentowane jako pochodzące z IoT Edge danych. Urządzenia podrzędne podłączone do IoT Edge nie są aprowowane w IoT Central.

#### <a name="iot-edge-gateway-device-connected-to-downstream-devices-with-identity"></a>IoT Edge bramy połączone z urządzeniami nadrzędnymi przy użyciu tożsamości

![IoT Edge z tożsamością urządzenia podrzędnego](./media/concepts-architecture/edgewithdownstreamdeviceidentity.png)

Urządzenie IoT Edge jest aprowowane w IoT Central razem z urządzeniami nadrzędnymi podłączonymi do IoT Edge urządzenia. Obsługa środowiska uruchomieniowego do aprowizowania urządzeń nadrzędnych za pośrednictwem bramy nie jest obecnie obsługiwana.

#### <a name="iot-edge-gateway-device-connected-to-downstream-devices-with-identity-provided-by-the-iot-edge-gateway"></a>IoT Edge połączone z urządzeniami nadrzędnymi przy użyciu tożsamości dostarczonej przez bramę IoT Edge wirtualnej

![IoT Edge z urządzeniem nadrzędnym bez tożsamości](./media/concepts-architecture/edgewithoutdownstreamdeviceidentity.png)

Urządzenie IoT Edge jest aprowowane w IoT Central razem z urządzeniami nadrzędnymi podłączonymi do IoT Edge urządzenia. Obsługa środowiska uruchomieniowego bramy zapewniającej tożsamość dla urządzeń dalszych i aprowizowanie urządzeń dalszych nie jest obecnie obsługiwana. Jeśli używasz własnego modułu tłumaczenia tożsamości, IoT Central może obsługiwać ten wzorzec.

## <a name="cloud-gateway"></a>Brama w chmurze

Azure IoT Central używa Azure IoT Hub jako bramy w chmurze, która umożliwia łączność urządzeń. IoT Hub włącza:

- Pozyskiwanie danych na dużą skalę w chmurze.
- Zarządzanie urządzeniami.
- Bezpieczna łączność urządzenia.

Aby dowiedzieć się więcej na temat IoT Hub, zobacz [Azure IoT Hub](../../iot-hub/index.yml).

Aby dowiedzieć się więcej na temat łączności urządzeń w Azure IoT Central, zobacz [Łączność urządzeń](concepts-get-connected.md).

## <a name="data-stores"></a>Magazyny danych

Azure IoT Central przechowuje dane aplikacji w chmurze. Przechowywane dane aplikacji obejmują:

- Szablony urządzeń.
- Tożsamości urządzeń.
- Metadane urządzenia.
- Dane użytkownika i roli.

Azure IoT Central korzysta z magazynu szeregów czasowych na dane pomiarów wysyłane z urządzeń. Dane szeregów czasu z urządzeń używanych przez usługę analizy.

## <a name="data-export"></a>Eksport danych

W aplikacji Azure IoT Central można [stale](howto-export-data.md) eksportować dane do własnych Azure Event Hubs i Azure Service Bus wystąpień. Możesz również okresowo eksportować dane na konto usługi Azure Blob Storage. IoT Central eksportować miary, urządzenia i szablony urządzeń.

## <a name="batch-device-updates"></a>Aktualizacje urządzenia usługi Batch

W aplikacji Azure IoT Central można tworzyć i uruchamiać zadania [w](howto-run-a-job.md) celu zarządzania połączonymi urządzeniami. Te zadania umożliwiają zbiorcze aktualizacje właściwości lub ustawień urządzenia albo uruchamianie poleceń. Można na przykład utworzyć zadanie zwiększenia szybkości wentylatora dla wielu chłodniczych automatów do sprzedaży.

## <a name="role-based-access-control-rbac"></a>Kontrola dostępu oparta na rolach (RBAC)

Każda IoT Central ma własny wbudowany system RBAC. Administrator [może zdefiniować reguły dostępu dla](howto-manage-users-roles.md) aplikacji Azure IoT Central przy użyciu jednej ze wstępnie zdefiniowanych ról lub tworząc rolę niestandardową. Role określają, do jakich obszarów aplikacji użytkownik ma dostęp i jakie akcje może wykonywać.

## <a name="security"></a>Zabezpieczenia

Funkcje zabezpieczeń w Azure IoT Central obejmują:

- Dane są szyfrowane podczas przesyłania i przechowywania.
- Uwierzytelnianie jest zapewniane przez Azure Active Directory lub konto Microsoft. Obsługiwane jest uwierzytelnianie dwuskładnikowe.
- Pełna izolacja dzierżawy.
- Zabezpieczenia na poziomie urządzenia.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy już wiesz, jaka jest architektura Azure IoT Central, następnym sugerowanym [](concepts-get-connected.md) krokiem jest nauczenia się łączności urządzeń w Azure IoT Central.