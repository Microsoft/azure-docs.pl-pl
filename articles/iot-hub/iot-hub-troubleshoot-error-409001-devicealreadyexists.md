---
title: Rozwiązywanie problemów z usługą Azure IoT Hub błąd 409001 DeviceAlreadyExists
description: Dowiedz się, jak naprawić błąd 409001 DeviceAlreadyExists
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: b075519fff2c7c328778d770ef68e9751922807b
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106061132"
---
# <a name="409001-devicealreadyexists"></a>409001 DeviceAlreadyExists

W tym artykule opisano przyczyny i rozwiązania **409001 błędów DeviceAlreadyExists** .

## <a name="symptoms"></a>Objawy

Podczas próby zarejestrowania urządzenia w IoT Hub żądanie kończy się niepowodzeniem z błędem **409001 DeviceAlreadyExists**.

## <a name="cause"></a>Przyczyna

Istnieje już urządzenie o tym samym IDENTYFIKATORze urządzenia w usłudze IoT Hub. 

## <a name="solution"></a>Rozwiązanie

Użyj innego identyfikatora urządzenia i spróbuj ponownie.