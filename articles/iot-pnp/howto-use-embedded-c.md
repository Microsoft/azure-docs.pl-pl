---
title: Używanie Plug and Play IoT na urządzeniach z ograniczeniami | Microsoft Docs
description: Dowiedz się, jak zaimplementować Plug and Play IoT na ograniczonych urządzeniach przy użyciu zestawu SDK dla programu Embedded C lub Azure RTO.
author: elhorton
ms.author: elhorton
ms.date: 09/23/2020
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: a5b826bd76eb7e56620bcb5b5eec9464ebacffc6
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/30/2020
ms.locfileid: "91580419"
---
# <a name="implement-iot-plug-and-play-on-constrained-devices"></a>Implementowanie Plug and Play IoT na urządzeniach z ograniczeniami

Jeśli tworzysz aplikacje dla *urządzeń z ograniczeniami*, możesz użyć Plug and Play IoT z [zestawem Azure SDK dla bibliotek klienta IoT](https://aka.ms/embeddedcsdk) w języku C lub z [usługą Azure RTO](https://docs.microsoft.com/azure/rtos/overview-rtos). Ten artykuł zawiera linki i zasoby dla tych scenariuszy z ograniczeniami.

## <a name="use-the-sdk-for-embedded-c"></a>Korzystanie z zestawu SDK dla osadzonego języka C

Zestaw SDK dla programu Embedded C oferuje uproszczone rozwiązanie do łączenia ograniczonych urządzeń z usługami Azure IoT, w tym przy użyciu [konwencji Plug and Play IoT](concepts-convention.md). Poniższe linki obejmują przykłady dla rzeczywistego urządzenia oraz do celów edukacyjnych i debugowania.

### <a name="use-a-real-device"></a>Korzystanie z rzeczywistego urządzenia

Aby zapoznać się z kompletnym samouczkiem korzystającym z zestawu SDK dla osadzonych C, usługi Device Provisioning i Plug and Play IoT na rzeczywistym urządzeniu, zobacz temat Zmiana [celu pic WX Development Board, aby połączyć się z platformą Azure za pośrednictwem IoT Hub Device Provisioning Service](https://github.com/Azure-Samples/Microchip-PIC-IoT-Wx).

### <a name="introductory-samples"></a>Przykłady wstępne

Zestaw SDK dla repozytorium osadzonych C zawiera [kilka przykładów](https://github.com/Azure/azure-sdk-for-c/tree/master/sdk/samples/iot#iot-hub-plug-and-play-sample) , które pokazują, jak korzystać z Plug and Play IoT:

> [!NOTE]
> Te przykłady są wyświetlane w systemach Windows i Linux w celach edukacyjnych i debugowania. W scenariuszu produkcyjnym przykłady są przeznaczone tylko dla urządzeń z ograniczeniami.

- [Przykład termostatu z zestawem SDK dla osadzonego języka C](https://github.com/Azure/azure-sdk-for-c/blob/master/sdk/samples/iot/paho_iot_hub_pnp_sample.c)

- [Przykład kontrolera temperatury z zestawem SDK dla osadzonego języka C](https://github.com/Azure/azure-sdk-for-c/blob/master/sdk/samples/iot/paho_iot_hub_pnp_component_sample.c)

## <a name="using-azure-rtos"></a>Korzystanie z usługi Azure RTO

Usługa Azure RTO obejmuje warstwę uproszczoną, która dodaje natywną łączność z usługami Azure IoT Cloud Services. Ta warstwa zapewnia prosty mechanizm łączenia ograniczonych urządzeń z usługą Azure IoT przy użyciu zaawansowanych funkcji usługi Azure RTO.

### <a name="toolchains"></a>Łańcuchy narzędzi

Przykłady usługi Azure RTO są dostarczane z różnymi rodzajami kombinacji IDE i łańcucha narzędzi, takich jak:

- IAR: IAR [Embedded Workbench](https://www.iar.com/iar-embedded-workbench/) IDE
- W zatoce/CMake: kompilacja w oparciu o system kompilacji [CMAKE](https://cmake.org/) typu open source i [GNU ARM Embedded łańcucha narzędzi](https://developer.arm.com/tools-and-software/open-source-software/developer-tools/gnu-toolchain/gnu-rm).
- MCUExpresso: NXP [MCUXPRESSO IDE](https://www.nxp.com/design/software/development-software/mcuxpresso-software-and-tools-/mcuxpresso-integrated-development-environment-ide:MCUXpresso-IDE)
- STM32Cube: STMicroelectronic [STM32CUBE IDE](https://www.st.com/en/development-tools/stm32cubeide.html)
- MPLAB: [IDE MPLAB X](https://www.microchip.com/mplab/mplab-x-ide) mikrochipu

### <a name="samples"></a>Samples

Przykłady pokazujące, jak zacząć korzystać z różnych urządzeń za pomocą usługi Azure RTO i IoT Plug and Play, zapoznaj się z poniższą tabelą:

Producent | Urządzenie | Samples |
| --- | --- | --- |
| Mikroukład | [ATSAME54-XPRO](https://www.microchip.com/developmenttools/productdetails/atsame54-xpro) | W [zatoce/CMAKE](https://github.com/azure-rtos/getting-started/tree/master/Microchip/ATSAME54-XPRO) • [IAR](https://aka.ms/azrtos-sample/e54-iar) • [MPLAB](https://aka.ms/azrtos-sample/e54-mplab)
| ZESTAWU DEWELOPERSKIEGO | [AZ3166](https://aka.ms/iot-devkit) | [W zatoce/CMake](https://github.com/azure-rtos/getting-started/tree/master/MXChip/AZ3166)
| NXP | [MIMXRT1060-EVK](https://www.nxp.com/design/development-boards/i-mx-evaluation-and-development-boards/mimxrt1060-evk-i-mx-rt1060-evaluation-kit:MIMXRT1060-EVK) | W [zatoce/CMAKE](https://github.com/azure-rtos/getting-started/tree/master/NXP/MIMXRT1060-EVK) • [IAR](https://aka.ms/azrtos-sample/rt1060-iar) • [MCUXpresso](https://aka.ms/azrtos-sample/rt1060-mcuxpresso)
| STMicroelectronics | [32F746GDISCOVERY](https://www.st.com/en/evaluation-tools/32f746gdiscovery.html) | [IAR](https://aka.ms/azrtos-sample/f746g-iar) • [STM32Cube](https://aka.ms/azrtos-sample/f746g-cubeide)
| STMicroelectronics | [B-L475E-IOT01](https://www.st.com/en/evaluation-tools/b-l475e-iot01a.html) | W [zatoce/CMAKE](https://github.com/azure-rtos/getting-started/tree/master/STMicroelectronics/STM32L4_L4%2B) • [IAR](https://aka.ms/azrtos-sample/l4s5-iar) • [STM32Cube](https://aka.ms/azrtos-sample/l4s5-cubeide)
| STMicroelectronics | [B-L4S5I-IOT01](https://www.st.com/en/evaluation-tools/b-l4s5i-iot01a.html) | W [zatoce/CMAKE](https://github.com/azure-rtos/getting-started/tree/master/STMicroelectronics/STM32L4_L4%2B) • [IAR](https://aka.ms/azrtos-sample/l4s5-iar) • [STM32Cube](https://aka.ms/azrtos-sample/l4s5-cubeide)

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już, jak można zaimplementować Plug and Play IoT na urządzeniach z ograniczeniami, sugerowanym następnym krokiem jest zapoznanie się z [konwencjami Plug and Play IoT](concepts-convention.md).