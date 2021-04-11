---
title: Informacje na temat aktualizacji urządzeń IoT Hub platformy Azure | Microsoft Docs
description: Informacje na temat aktualizacji urządzeń IoT Hub platformy Azure
author: vimeht
ms.author: vimeht
ms.date: 2/11/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: ba43889b885252f68bb3b4b158b5626411aac3d5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101663644"
---
# <a name="device-update-resources"></a>Zasoby aktualizacji urządzeń

Aby można było użyć aktualizacji urządzenia IoT Hub, należy utworzyć konto aktualizacji urządzenia i zasób wystąpienia. 

## <a name="device-update-account"></a>Konto aktualizacji urządzenia

Konto aktualizacji urządzenia jest zasobem utworzonym w ramach subskrypcji platformy Azure. Na poziomie konta aktualizacja urządzenia można wybrać region, w którym zostanie utworzone konto aktualizacji urządzenia. Możesz również ustawić uprawnienia, aby autoryzować użytkowników, którzy będą mieli dostęp do aktualizacji urządzenia.


## <a name="device-update-instance"></a>Wystąpienie aktualizacji urządzenia
Po utworzeniu konta należy utworzyć wystąpienie aktualizacji urządzenia. Wystąpienie jest kontenerem logicznym, który zawiera aktualizacje i wdrożenia skojarzone z konkretnym Centrum IoT. Aktualizacja urządzenia używa usługi IoT Hub jako katalogu urządzeń oraz kanału komunikacyjnego z urządzeniami. 

W publicznej wersji zapoznawczej można utworzyć dwa konta aktualizacji urządzeń dla każdej subskrypcji. Ponadto dla każdego konta można utworzyć dwa wystąpienia aktualizacji urządzeń.

## <a name="configuring-device-update-linked-iot-hub"></a>Konfigurowanie połączonej IoT Hub aktualizacji urządzeń 

Aby aktualizacja urządzenia mogła odbierać powiadomienia o zmianach z IoT Hub, aktualizacja urządzenia jest zintegrowana z centrum zdarzeń "wbudowane". Kliknięcie przycisku "Konfiguruj IoT Hub" w tym wystąpieniu spowoduje skonfigurowanie wymaganych tras komunikatów i zasad dostępu wymaganych do komunikowania się z urządzeniami IoT. 

Następujące trasy komunikatów zostały skonfigurowane dla aktualizacji urządzenia:

|   Nazwa trasy    | Zapytanie routingu  | Opis  |
| :--------- | :---- |:---- |
|  DeviceUpdate.DigitalTwinChanges | true |Nasłuchuje zdarzeń cyfrowych bliźniaczych zmian  |
|  DeviceUpdate.DeviceLifeCycle | opType = "deleteDeviceIdentity"  | Nasłuchuje urządzeń, które zostały usunięte |
|  DeviceUpdate.TelemetryModelInformation | iothub-Interface-ID = "urn: azureiot: ModelDiscovery: ModelInformation: 1 | Nasłuchiwanie nowych typów urządzeń |
|  DeviceUpdate.DeviceTwinEvents| (opType = ' updateTwin' lub opType = ' replaceTwin') I IS_DEFINED ($body. Tags. ADUGroup) | Nasłuchuje nowych grup aktualizacji urządzeń |

## <a name="next-steps"></a>Następne kroki

[Tworzenie zasobów aktualizacji urządzeń](./create-device-update-account.md)
