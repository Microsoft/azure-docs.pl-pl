---
title: Mostek Plug and Play IoT | Microsoft Docs
description: Zapoznaj się z mostkiem Plug and Play IoT i sposobami korzystania z niego do łączenia istniejących urządzeń podłączonych do bramy systemu Windows lub Linux jako urządzeń Plug and Play IoT.
author: usivagna
ms.author: ugans
ms.date: 09/22/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 224f86a40fa812003463301f97bcae07de907f3c
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/30/2020
ms.locfileid: "91580469"
---
# <a name="iot-plug-and-play-bridge"></a>Mostek Plug and Play IoT

Mostek Plug and Play IoT to aplikacja Open Source służąca do łączenia istniejących urządzeń podłączonych do bramy systemu Windows lub Linux jako urządzeń Plug and Play IoT. Po zainstalowaniu i skonfigurowaniu aplikacji na komputerze z systemem Windows lub Linux można użyć jej do nawiązania połączenia z urządzeniami podłączonymi do centrum IoT Hub. Za pomocą mostka można mapować interfejsy Plug and Play IoT na telemetrię, które dołączone urządzenia wysyłają, pracują z właściwościami urządzenia i wywoływać polecenia.

:::image type="content" source="media/concepts-iot-pnp-bridge/iot-pnp-bridge-high-level.png" alt-text="Po lewej stronie istnieje kilka istniejących czujników dołączonych (zarówno przewodowych, jak i bezprzewodowych) do komputera z systemem Windows lub Linux zawierającym mostek Plug and Play IoT. Mostek Plug and Play IoT łączy się następnie z Centrum IoT po prawej stronie":::

Program IoT Plug and Play Bridge można wdrożyć jako autonomiczny plik wykonywalny na dowolnym urządzeniu IoT, komputerze stacjonarnym, serwerze lub bramie z systemem Windows 10 lub Linux. Można go również skompilować do kodu aplikacji. Prosty plik JSON konfiguracji informuje mostek Plug and Play IoT, które dołączone urządzenia/urządzenia peryferyjne powinny być uwidocznione na platformie Azure.

## <a name="supported-protocols-and-sensors"></a>Obsługiwane protokoły i czujniki

Program IoT Plug and Play Bridge domyślnie obsługuje następujące typy urządzeń peryferyjnych z linkami do dokumentacji karty:

|Urządzenia|Windows|Linux|
|---------|---------|---------|
|[Urządzenie Bluetooth LE](https://aka.ms/iot-pnp-bridge-bluetooth)       |Tak|Nie|
|[Kamery](https://aka.ms/iot-pnp-bridge-camera)               |Tak|Nie|
|[Modbus](https://aka.ms/iot-pnp-bridge-modbus)                |Tak|Tak|
|[MQTT](https://aka.ms/iot-pnp-bridge-mqtt)                    |Tak|Tak|
|[Kolejną](https://aka.ms/iot-pnp-bridge-serial)                |Tak|Tak|
|[Urządzenia peryferyjne USB z systemem Windows](https://aka.ms/iot-pnp-bridge-usb)  |Tak|Nie dotyczy|

>[!Important]
>Deweloperzy mogą zwiększyć mostek Plug and Play IoT, aby obsłużyć dodatkowe protokoły urządzeń, korzystając z instrukcji w **[dokumentacji deweloperów Plug and Play IoT](https://aka.ms/iot-pnp-bridge-dev-doc)**.

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="os-platform"></a>Platforma systemu operacyjnego

Obsługiwane są następujące platformy i wersje systemu operacyjnego:

|Platforma  |Obsługiwane wersje  |
|---------|---------|
|Windows 10 |     Obsługiwane są wszystkie jednostki SKU systemu Windows. Na przykład: IoT Enterprise, Server, Desktop, IoT Core. *W przypadku funkcji monitorowania kondycji aparatu 20H1 zaleca się kompilację lub nowszą. Wszystkie inne funkcje są dostępne we wszystkich kompilacjach systemu Windows 10.*  |
|Linux     |Przetestowane i obsługiwane w systemie Ubuntu 18,04, funkcjonalność innych dystrybucji nie została przetestowana.         |
||

### <a name="hardware"></a>Sprzęt

- Dowolna platforma sprzętowa obsługująca powyższe jednostki SKU i wersje systemu operacyjnego.
- Urządzenia peryferyjne oraz czujniki USB, Bluetooth i Camera są obsługiwane natywnie. Mostek Plug and Play IoT można rozszerzyć w celu obsługi dowolnego niestandardowego urządzenia peryferyjnego lub czujnika ([patrz sekcja urządzenia peryferyjne powyżej](#iot-plug-and-play-bridge)).

### <a name="development-environment"></a>Środowisko deweloperskie

Aby kompilować, rozszerzać i opracowywać mostek Plug and Play IoT, potrzebne są:  

- Środowisko programistyczne obsługujące kompilowanie kodu C++, takie jak [Visual Studio (Community, Professional lub Enterprise)](https://visualstudio.microsoft.com/downloads/)— upewnij się, że podczas instalowania programu Visual Studio dołączysz programowanie klasyczne przy użyciu języka c++.
- [CMAKE](https://cmake.org/download/) — po zainstalowaniu CMAKE wybierz opcję `Add CMake to the system PATH` .
- W przypadku kompilowania w systemie Windows konieczne jest również pobranie zestawu Windows 17763 SDK: [https://developer.microsoft.com/windows/downloads/windows-10-sdk](https://developer.microsoft.com/windows/downloads/windows-10-sdk)
- [Zestaw SDK usługi Azure IoT Hub Device](https://github.com/Azure/azure-iot-sdk-c). Dołączone skrypty kompilacji w tym repozytorium automatycznie sklonują wymagany zestaw SDK języka C usługi Azure IoT.

### <a name="azure-iot-products-and-tools"></a>Produkty i narzędzia usługi Azure IoT

- **IoT Hub platformy Azure** — aby połączyć urządzenie z usługą, musisz mieć [usługę Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/) w ramach subskrypcji platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/). Jeśli nie masz Centrum IoT Hub, [postępuj zgodnie z tymi instrukcjami, aby je utworzyć](https://docs.microsoft.com/azure/iot-hub/iot-hub-create-using-cli).

> [!Note]
> Plug and Play IoT jest obecnie dostępna dla centrów IoT utworzonych w regionach Środkowe stany USA, Europa Północna i Japonia Wschodnia. Obsługa Plug and Play IoT nie jest uwzględniona w centrach IoT warstwy Podstawowa. Aby móc korzystać z urządzenia Plug and Play IoT, możesz użyć narzędzia Azure IoT Explorer. [Pobierz i zainstaluj najnowszą wersję programu Azure IoT Explorer](./howto-use-iot-explorer.md) dla danego systemu operacyjnego.

## <a name="iot-plug-and-play-bridge-architecture"></a>Architektura mostka Plug and Play IoT

:::image type="content" source="media/concepts-iot-pnp-bridge/iot-pnp-bridge-components.png" alt-text="Po lewej stronie istnieje kilka istniejących czujników dołączonych (zarówno przewodowych, jak i bezprzewodowych) do komputera z systemem Windows lub Linux zawierającym mostek Plug and Play IoT. Mostek Plug and Play IoT łączy się następnie z Centrum IoT po prawej stronie":::

## <a name="download-iot-plug-and-play-bridge"></a>Pobierz mostek Plug and Play IoT

Możesz pobrać wstępnie zbudowaną wersję mostka z obsługiwanymi kartami w usłudze [IoT Plug and Play w wersji Bridge](https://aka.ms/iot-pnp-bridge-releases) i rozwinąć listę zasobów dla najnowszej wersji. Pobierz najnowszą wersję aplikacji dla danego systemu operacyjnego.

Możesz również pobrać i wyświetlić kod źródłowy usługi [IoT Plug and Play Bridge w serwisie GitHub](https://aka.ms/bridge).

## <a name="next-steps"></a>Następne kroki

Teraz, gdy masz Przegląd architektury usługi IoT Plug and Play Bridge, następne kroki zawierają informacje na temat:

- [Jak korzystać z programu IoT Plug and Play Bridge](./howto-use-iot-pnp-bridge.md)
- [Zobacz informacje dotyczące deweloperów usługi GitHub dla usługi IoT Plug and Play Bridge](https://aka.ms/iot-pnp-bridge-dev-doc)
- [Mostek Plug and Play IoT w serwisie GitHub](https://aka.ms/iotplugandplaybridge)
