---
title: Informacje o aktualizacji urządzenia dla Azure IoT Hub Agent| Microsoft Docs
description: Informacje o aktualizacji urządzenia dla Azure IoT Hub Agent.
author: ValOlson
ms.author: valls
ms.date: 2/12/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: 0d97287657b1e1fe7d540e8811c90794aaa5fece
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739570"
---
# <a name="device-update-for-iot-hub-agent-overview"></a>Usługa Device Update dla agenta usługi IoT Hub — omówienie

Agent aktualizacji urządzenia składa się z dwóch warstw koncepcyjnych:

* Warstwa interfejsu opiera się na usłudze [Azure IoT Plug and Play (PnP),](../iot-pnp/overview-iot-plug-and-play.md) umożliwiając przepływ komunikatów między agentem aktualizacji urządzeń i usługami Device Update Services.
* Warstwa platformy jest odpowiedzialna za akcje aktualizacji wysokiego poziomu Pobierz, Zainstaluj i Zastosuj, które mogą być specyficzne dla platformy lub urządzenia.

:::image type="content" source="media/understand-device-update/client-agent-reference-implementations.png" alt-text="Implementacje agentów." lightbox="media/understand-device-update/client-agent-reference-implementations.png":::

## <a name="the-interface-layer"></a>Warstwa interfejsu

Warstwa Interfejs składa się z interfejsu [podstawowego ADU](https://github.com/Azure/iot-hub-device-update/tree/main/src/agent/adu_core_interface) i [interfejsu informacji o urządzeniu.](https://github.com/Azure/iot-hub-device-update/tree/main/src/agent/device_info_interface)

Te interfejsy bazują na pliku konfiguracji dla wartości domyślnych. Wartości domyślne obejmują aduc_manufacturer i aduc_model interfejsu AzureDeviceUpdateCore oraz model i producent interfejsu DeviceInformation. [Dowiedz się więcej](device-update-configuration-file.md) o pliku konfiguracji.

### <a name="adu-core-interface"></a>Interfejs podstawowy ADU

Interfejs "ADU Core" jest podstawowym kanałem komunikacyjnym między agentem aktualizacji urządzeń i usługami. [Dowiedz się więcej](device-update-plug-and-play.md#adu-core-interface) o tym interfejsie.

### <a name="device-information-interface"></a>Interfejs informacji o urządzeniu

Interfejs informacji o urządzeniu jest używany do implementacji `Azure IoT PnP DeviceInformation` interfejsu. [Dowiedz się więcej](device-update-plug-and-play.md#device-information-interface) o tym interfejsie.

## <a name="the-platform-layer"></a>Warstwa platformy

Istnieją dwie implementacje warstwy platformy. Warstwa platformy symulatora ma trywialne wdrożenie akcji aktualizacji i jest używana do szybkiego testowania i oceniania aktualizacji urządzenia dla IoT Hub i instalacji. Gdy agent aktualizacji urządzenia jest budowy za pomocą warstwy platformy symulatora, jest on odwoływyny jako agent symulatora aktualizacji urządzenia lub po prostu symulator. [Dowiedz się więcej](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-run-agent.md) na temat korzystania z agenta symulatora. Warstwa platformy systemu Linux [](https://github.com/microsoft/do-client) integruje się z Optymalizacja dostarczania do pobrania i jest używana w naszym obrazie referencyjnym urządzenia Raspberry Pi oraz wszystkich klientach uruchomionych w systemach Linux.

### <a name="simulator-platform-layer"></a>Warstwa platformy symulatora

Implementacja warstwy platformy symulatora znajduje się w warstwie i może służyć do testowania i oceniania `src/platform_layers/simulator_platform_layer` aktualizacji urządzenia na IoT Hub.  Wiele akcji w implementacji "symulatora" ma na celu ograniczenie zmian fizycznych w celu eksperymentowania z aktualizacją urządzenia na IoT Hub.  Za pomocą tej warstwy platformy można przeprowadzić "symulowaną" aktualizację. [Dowiedz się więcej](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-run-agent.md) o uruchamianiu agenta symulatora.

### <a name="linux-platform-layer"></a>Warstwa platformy systemu Linux

Implementacja warstwy platformy systemu Linux znajduje się w pliku i integruje się z klientem usługi Optymalizacja dostarczania do pobrania i jest używana w naszym obrazie referencyjnym urządzenia Raspberry Pi oraz wszystkich klientach uruchomionych w systemach `src/platform_layers/linux_platform_layer` Linux. [](https://github.com/microsoft/do-client/releases)

Tę warstwę można zintegrować z różnymi programami obsługi aktualizacji w celu zaimplementowania instalatora. Na przykład program obsługi aktualizacji wywołuje skrypt powłoki w celu wywołania do pliku wykonywalnego `SWUpdate` `SWUpdate` w celu przeprowadzenia aktualizacji.

## <a name="update-handlers"></a>Programy obsługi aktualizacji

Programy obsługi aktualizacji to składniki, które obsługują zawartość lub części aktualizacji specyficzne dla instalatora. Implementacje programu obsługi aktualizacji znajdują się w . `src/content_handlers`

### <a name="simulator-update-handler"></a>Program obsługi aktualizacji symulatora

Program obsługi aktualizacji symulatora jest używany przez warstwę platformy symulatora i może być używany z warstwą platformy systemu Linux do fałszywych interakcji z programem obsługi zawartości. Program obsługi aktualizacji symulatora implementuje interfejsy API obsługi aktualizacji, w większości bez obsługi. Implementację programu obsługi aktualizacji symulatora można znaleźć poniżej:
* [Symulator aktualizacji obrazu](https://github.com/Azure/iot-hub-device-update/blob/main/src/content_handlers/swupdate_handler/inc/aduc/swupdate_simulator_handler.hpp)
* [Symulator apt aktualizacji pakietu](https://github.com/Azure/iot-hub-device-update/blob/main/src/content_handlers/apt_handler/inc/aduc/apt_simulator_handler.hpp)

Uwaga: pole InstalledCriteria w interfejsie AzureDeviceUpdateCore PnP powinno być skrótem sha256 zawartości. Jest to ten sam skrót, który znajduje się w [obiekcie manifestu importu](import-update.md#create-a-device-update-import-manifest). [Dowiedz się więcej](device-update-plug-and-play.md) `installedCriteria` o `AzureDeviceUpdateCore` interfejsie i .

### <a name="swupdate-update-handler"></a>`SWUpdate` Program obsługi aktualizacji

Program obsługi aktualizacji" integruje się z plikiem wykonywalnym wiersza polecenia i innymi poleceniami powłoki w celu zaimplementowania aktualizacji A/B przeznaczonych specjalnie dla obrazu referencyjnego `SWUpdate` `SWUpdate` urządzenia Raspberry Pi. Najnowszy obraz referencyjny urządzenia Raspberry Pi można [znaleźć tutaj.](https://github.com/Azure/iot-hub-device-update/releases) Procedura `SWUpdate` obsługi aktualizacji jest zaimplementowana w [src/content_handlers/swupdate_content_handler](https://github.com/Azure/iot-hub-device-update/tree/main/src/content_handlers/swupdate_handler).

### <a name="apt-update-handler"></a>Procedura obsługi aktualizacji APT

Program obsługi aktualizacji APT przetwarza manifest aktualizacji specyficzny dla apt i wywołuje pakiet APT w celu zainstalowania lub zaktualizowania określonych pakietów Debian.

## <a name="self-update-device-update-agent"></a>Samodzielne aktualizowanie agenta aktualizacji urządzenia

Agenta aktualizacji urządzenia i jego zależności można zaktualizować za pomocą aktualizacji urządzenia dla IoT Hub potoku. Jeśli używasz aktualizacji opartej na obrazach, uwzględnij najnowszego agenta aktualizacji urządzenia w nowym obrazie. Jeśli używasz aktualizacji opartej na pakietach, uwzględnij agenta aktualizacji urządzenia i jego żądaną wersję w manifeście apt, tak jak każdy inny pakiet. [Dowiedz się więcej](device-update-apt-manifest.md) o manifeście apt. Zainstalowaną wersję agenta aktualizacji urządzenia i agenta usługi Optymalizacja dostarczania można sprawdzić w sekcji Właściwości urządzenia bliźniaczej reprezentacji urządzenia [IoT.](../iot-hub/iot-hub-devguide-device-twins.md) [Dowiedz się więcej o właściwościach urządzenia w obszarze AdU Core Interface (Interfejs podstawowy adu).](device-update-plug-and-play.md#device-properties)

## <a name="next-steps"></a>Następne kroki
[Informacje o pliku konfiguracji agenta aktualizacji urządzeń](device-update-configuration-file.md)