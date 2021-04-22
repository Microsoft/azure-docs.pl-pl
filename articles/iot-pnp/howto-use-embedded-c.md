---
title: Używanie IoT Plug and Play na ograniczonych urządzeniach | Microsoft Docs
description: Dowiedz się, jak wdrożyć IoT Plug and Play na ograniczonych urządzeniach przy użyciu zestawu SDK dla osadzonego języka C lub Azure RTOS.
author: EliotSeattle
ms.author: eliotgra
ms.date: 09/23/2020
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 6c792fbbb44b7dc769e7cdc56850684bd69ea40b
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107864141"
---
# <a name="implement-iot-plug-and-play-on-constrained-devices"></a>Implementowanie IoT Plug and Play na urządzeniach z ograniczeniami

Jeśli opracowujesz aplikacje dla urządzeń z ograniczeniami, możesz użyć usługi IoT Plug and Play z zestawem Azure SDK dla osadzonych bibliotek klienta [IoT języka C](https://aka.ms/embeddedcsdk) lub z Azure RTOS . [](/azure/rtos/overview-rtos) Ten artykuł zawiera linki i zasoby dla tych ograniczonych scenariuszy.

## <a name="use-the-sdk-for-embedded-c"></a>Korzystanie z zestawu SDK dla osadzonego języka C

Zestaw SDK dla osadzonego języka C oferuje lekkie rozwiązanie do łączenia ograniczonych urządzeń z usługami Azure IoT, w tym przy użyciu [IoT Plug and Play konwencje.](concepts-convention.md) Poniższe linki zawierają przykłady dla rzeczywistego urządzenia oraz dla celów edukacyjnych i debugowania.

### <a name="use-a-real-device"></a>Korzystanie z rzeczywistego urządzenia

Aby uzyskać kompletny samouczek korzystający z zestawu SDK dla osadzonego języka C, usługi Device Provisioning i usługi IoT Plug and Play na rzeczywistym urządzeniu, zobacz Temat Ponowne zastosowanie tablicy dewelopera [usługi PIC-IoT Wx](https://github.com/Azure-Samples/Microchip-PIC-IoT-Wx)w celu nawiązywania połączenia z platformą Azure za pośrednictwem usługi IoT Hub Device Provisioning Service .

### <a name="introductory-samples"></a>Przykłady wprowadzające

Zestaw SDK dla osadzonego repozytorium C zawiera [kilka przykładów,](https://github.com/Azure/azure-sdk-for-c/tree/master/sdk/samples/iot#iot-hub-plug-and-play-sample) które pokazują, jak używać IoT Plug and Play:

> [!NOTE]
> Te przykłady są wyświetlane w systemach Windows i Linux na potrzeby edukacji i debugowania. W scenariuszu produkcyjnym przykłady są przeznaczone tylko dla urządzeń z ograniczeniami.

- [Przykład termostatu z zestawem SDK dla osadzonego języka C](https://github.com/Azure/azure-sdk-for-c/blob/master/sdk/samples/iot/paho_iot_hub_pnp_sample.c)

- [Przykład kontrolera temperatury z zestawem SDK dla osadzonego języka C](https://github.com/Azure/azure-sdk-for-c/blob/master/sdk/samples/iot/paho_iot_hub_pnp_component_sample.c)

## <a name="using-azure-rtos"></a>Korzystanie z Azure RTOS

Azure RTOS zawiera lekką warstwę, która dodaje natywną łączność do usług Azure IoT w chmurze. Ta warstwa zapewnia prosty mechanizm łączenia ograniczonych urządzeń z usługą Azure IoT przy użyciu zaawansowanych funkcji usługi Azure RTOS. Aby dowiedzieć się więcej, zobacz Co to jest [Microsoft Azure RTOS.](/azure/rtos/overview-rtos)

### <a name="toolchains"></a>Toolchains (Narzędzia)

Przykłady Azure RTOS są dostarczane z różnymi rodzajami kombinacji środowiska IDE i zestawu narzędzi, takich jak:

- IAR: osadzone środowiska IDE [środowiska roboczego](https://www.iar.com/iar-embedded-workbench/) IAR
- GCC/CMake: kompilacja na podstawie open source [kompilacji CMake](https://cmake.org/) i [osadzonego zestawu](https://developer.arm.com/tools-and-software/open-source-software/developer-tools/gnu-toolchain/gnu-rm)narzędzi GNU Arm.
- MCUExpresso: [idee MCUXpresso](https://www.nxp.com/design/software/development-software/mcuxpresso-software-and-tools-/mcuxpresso-integrated-development-environment-ide:MCUXpresso-IDE) NXP
- STM32Cube: stMicroelectronic [STM32Cube IDE](https://www.st.com/en/development-tools/stm32cubeide.html)
- MPLAB: [idee MPLAB X](https://www.microchip.com/mplab/mplab-x-ide) firmy Microchip

### <a name="samples"></a>Samples

Przykłady, które pokazują, jak rozpocząć pracę na różnych urządzeniach z Azure RTOS i IoT Plug and Play, zobacz następującą tabelę:

Producent | Urządzenie | Samples |
| --- | --- | --- |
| Microchip | [ATSAME54-XPRO](https://www.microchip.com/developmenttools/productdetails/atsame54-xpro) | [GCC/CMake](https://github.com/azure-rtos/getting-started/tree/master/Microchip/ATSAME54-XPRO) • [IAR](https://aka.ms/azrtos-sample/e54-iar) • [MPLAB](https://aka.ms/azrtos-sample/e54-mplab)
| FUNKCJA MXCHIP | [AZ3166](https://aka.ms/iot-devkit) | [GCC/CMake](https://github.com/azure-rtos/getting-started/tree/master/MXChip/AZ3166)
| Nxp | [MIMXRT1060-EVK](https://www.nxp.com/design/development-boards/i-mx-evaluation-and-development-boards/mimxrt1060-evk-i-mx-rt1060-evaluation-kit:MIMXRT1060-EVK) | [GCC/CMake](https://github.com/azure-rtos/getting-started/tree/master/NXP/MIMXRT1060-EVK) • [IAR](https://aka.ms/azrtos-sample/rt1060-iar) • [MCUXpresso](https://aka.ms/azrtos-sample/rt1060-mcuxpresso)
| Stmicroelectronics | [32F746GDISCOVERY](https://www.st.com/en/evaluation-tools/32f746gdiscovery.html) | [IAR](https://aka.ms/azrtos-sample/f746g-iar) • [STM32Cube](https://aka.ms/azrtos-sample/f746g-cubeide)
| Stmicroelectronics | [B-L475E-IOT01](https://www.st.com/en/evaluation-tools/b-l475e-iot01a.html) | [GCC/CMake](https://github.com/azure-rtos/getting-started/tree/master/STMicroelectronics/STM32L4_L4%2B) • [IAR](https://aka.ms/azrtos-sample/l4s5-iar) • [STM32Cube](https://aka.ms/azrtos-sample/l4s5-cubeide)
| Stmicroelectronics | [B-L4S5I-IOT01](https://www.st.com/en/evaluation-tools/b-l4s5i-iot01a.html) | [GCC/CMake](https://github.com/azure-rtos/getting-started/tree/master/STMicroelectronics/STM32L4_L4%2B) • [IAR](https://aka.ms/azrtos-sample/l4s5-iar) • [STM32Cube](https://aka.ms/azrtos-sample/l4s5-cubeide)

## <a name="next-steps"></a>Następne kroki

Teraz, gdy już wiesz o opcjach wdrażania IoT Plug and Play na ograniczonych urządzeniach, sugerowanym następnym krokiem jest poznanie IoT Plug and Play [konwencji.](concepts-convention.md)