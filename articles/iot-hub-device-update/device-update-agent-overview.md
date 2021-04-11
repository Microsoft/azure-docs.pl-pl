---
title: Opis aktualizacji urządzenia dla usługi Azure IoT Hub Agent | Microsoft Docs
description: Opis aktualizacji urządzenia dla usługi Azure IoT Hub Agent.
author: ValOlson
ms.author: valls
ms.date: 2/12/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: efe5d0171463668bda19a0d0445fc67f3734aaee
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105561240"
---
# <a name="device-update-for-iot-hub-agent-overview"></a>Usługa Device Update dla agenta usługi IoT Hub — omówienie

Agent aktualizacji urządzenia składa się z dwóch warstw koncepcyjnych:

* Warstwa interfejsu jest oparta na [usłudze Azure IoT Plug and Play (PNP)](../iot-pnp/overview-iot-plug-and-play.md) umożliwiająca przepływ komunikatów między agentem aktualizacji urządzeń i usługami aktualizacji urządzeń.
* Warstwa platformy jest odpowiedzialna za akcje aktualizacji wysokiego poziomu, które można pobrać, zainstalować i zastosować, które mogą być platformą lub specyficzne dla urządzenia.

:::image type="content" source="media/understand-device-update/client-agent-reference-implementations.png" alt-text="Implementacje agentów." lightbox="media/understand-device-update/client-agent-reference-implementations.png":::

## <a name="the-interface-layer"></a>Warstwa interfejsu

Warstwa interfejsu składa się z [interfejsu adu Core](https://github.com/Azure/iot-hub-device-update/tree/main/src/agent/adu_core_interface) i [interfejsu informacji o urządzeniu](https://github.com/Azure/iot-hub-device-update/tree/main/src/agent/device_info_interface).

Te interfejsy polegają na pliku konfiguracyjnym dla wartości domyślnych. Wartości domyślne obejmują aduc_manufacturer i aduc_model dla interfejsu AzureDeviceUpdateCore oraz modelu i producenta interfejsu DeviceInformation. [Dowiedz się więcej](device-update-configuration-file.md) o pliku konfiguracyjnym.

### <a name="adu-core-interface"></a>Interfejs ADU Core

Interfejs "ADU Core" jest podstawowym kanałem komunikacyjnym między agentem i usługami aktualizacji urządzeń. [Dowiedz się więcej](device-update-plug-and-play.md#adu-core-interface) o tym interfejsie.

### <a name="device-information-interface"></a>Interfejs informacji o urządzeniu

Interfejs informacji o urządzeniu jest używany do implementacji `Azure IoT PnP DeviceInformation` interfejsu. [Dowiedz się więcej](device-update-plug-and-play.md#device-information-interface) o tym interfejsie.

## <a name="the-platform-layer"></a>Warstwa platformy

Istnieją dwie implementacje warstwy platformy. Warstwa platformy symulatora zawiera uproszczoną implementację akcji aktualizacji i służy do szybkiego testowania i oceny aktualizacji urządzeń IoT Hub usług i konfiguracji. Gdy Agent aktualizacji urządzeń został skompilowany za pomocą warstwy platformy symulatora, odwołujemy się do niej jako agent symulatora aktualizacji urządzeń lub tylko symulator. [Dowiedz się więcej](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-run-agent.md) na temat korzystania z agenta symulatora. Warstwa platformy Linux jest zintegrowana z [optymalizacją dostarczania](https://github.com/microsoft/do-client) do pobrania i jest używana w naszym obrazie referencyjnym Raspberry Pi oraz na wszystkich klientach działających w systemach Linux.

### <a name="simulator-platform-layer"></a>Warstwa platformy symulatora

Implementację warstwy platformy symulatora można znaleźć w temacie `src/platform_layers/simulator_platform_layer` i można jej użyć do testowania i oceny aktualizacji urządzeń dla IoT Hub.  Wiele akcji w implementacji "symulatora" można skrócić, aby zmniejszyć fizyczne zmiany do eksperymentowania z aktualizacją urządzenia dla IoT Hub.  Na zakończenie "symulowanej" aktualizacji można wykonać za pomocą tej warstwy platformy. [Dowiedz się więcej](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-run-agent.md) o uruchamianiu agenta symulatora.

### <a name="linux-platform-layer"></a>Warstwa platformy Linux

Implementację warstwy platformy Linux można znaleźć w temacie `src/platform_layers/linux_platform_layer` i integruje się z [klientem optymalizacji dostarczania](https://github.com/microsoft/do-client/releases) do pobrania i jest używana w naszym obrazie referencyjnym Raspberry Pi oraz na wszystkich klientach działających w systemach Linux.

Ta warstwa może być zintegrowana z różnymi programami obsługi aktualizacji w celu zaimplementowania Instalatora. Na przykład `SWUpdate` program obsługi aktualizacji wywołuje skrypt powłoki w celu wywołania `SWUpdate` pliku wykonywalnego w celu przeprowadzenia aktualizacji.

## <a name="update-handlers"></a>Programy obsługi aktualizacji

Programy obsługi aktualizacji to składniki obsługujące informacje o części aktualizacji dotyczące zawartości lub Instalatora. Implementacje programu obsługi aktualizacji znajdują się w temacie `src/content_handlers` .

### <a name="simulator-update-handler"></a>Procedura obsługi aktualizacji symulatora

Program obsługi aktualizacji symulatora jest używany przez warstwę platformy symulatora i może być używany z warstwą platformy systemu Linux do fałszywych interakcji z obsługą zawartości. Program obsługi aktualizacji symulatora implementuje interfejsy API procedury obsługi aktualizacji za pomocą większości operacji No-Ops. Implementację programu obsługi aktualizacji symulatora można znaleźć poniżej:
* [Symulator aktualizacji obrazów](https://github.com/Azure/iot-hub-device-update/blob/main/src/content_handlers/swupdate_handler/inc/aduc/swupdate_simulator_handler.hpp)
* [Apt symulatora aktualizacji pakietu](https://github.com/Azure/iot-hub-device-update/blob/main/src/content_handlers/apt_handler/inc/aduc/apt_simulator_handler.hpp)

Uwaga: pole InstalledCriteria w interfejsie AzureDeviceUpdateCore PnP powinno być skrótem SHA256 zawartości. Jest to ten sam skrót, który jest obecny w [obiekcie manifestu importu](import-update.md#create-device-update-import-manifest). [Dowiedz się więcej](device-update-plug-and-play.md) o `installedCriteria` `AzureDeviceUpdateCore` interfejsie i.

### <a name="swupdate-update-handler"></a>`SWUpdate` Procedura obsługi aktualizacji

`SWUpdate`Procedura obsługi aktualizacji "integruje się z `SWUpdate` plikiem wykonywalnym wiersza polecenia i innymi poleceniami powłoki w celu zaimplementowania aktualizacji A/B przeznaczonych dla obrazu odniesienia Raspberry Pi. Znajdź w [tym miejscu](https://github.com/Azure/iot-hub-device-update/releases)najnowszy obraz referencyjny Raspberry Pi. `SWUpdate`Procedura obsługi aktualizacji jest zaimplementowana w elemencie [src/content_handlers/swupdate_content_handler](https://github.com/Azure/iot-hub-device-update/tree/main/src/content_handlers/swupdate_handler).

### <a name="apt-update-handler"></a>Procedura obsługi aktualizacji APT

Procedura obsługi aktualizacji APT przetwarza manifest aktualizacji specyficzny dla APT i wywołuje APT w celu zainstalowania lub zaktualizowania określonych pakietów debian.

## <a name="self-update-device-update-agent"></a>Samoobsługowa aktualizacja agenta aktualizacji urządzeń

Agenta aktualizacji urządzenia i jego zależności można zaktualizować za pomocą aktualizacji urządzenia dla potoku IoT Hub. W przypadku korzystania z aktualizacji opartej na obrazie należy uwzględnić w nowym obrazie najnowszą wersję agenta aktualizacji urządzenia. W przypadku korzystania z aktualizacji opartej na pakiecie należy uwzględnić agenta aktualizacji urządzenia i jego żądaną wersję w manifeście apt, jak każdy inny pakiet. [Dowiedz się więcej](device-update-apt-manifest.md) na temat manifestu apt. Zainstalowaną wersję agenta aktualizacji urządzeń i agenta optymalizacji dostarczania można sprawdzić w sekcji Właściwości urządzenia w ramach [sznurka urządzenia IoT](../iot-hub/iot-hub-devguide-device-twins.md). [Dowiedz się więcej o właściwościach urządzeń w interfejsie adu Core](device-update-plug-and-play.md#device-properties).

## <a name="next-steps"></a>Następne kroki
[Opis pliku konfiguracji agenta aktualizacji urządzeń](device-update-configuration-file.md)