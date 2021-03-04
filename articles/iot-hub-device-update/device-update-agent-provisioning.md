---
title: Aktualizacja urządzenia dla usługi Azure IoT Hub Agent | Microsoft Docs
description: Aktualizacja urządzenia dla usługi Azure IoT Hub Agent
author: ValOlson
ms.author: valls
ms.date: 2/16/2021
ms.topic: how-to
ms.service: iot-hub-device-update
ms.openlocfilehash: 01ce213f71fc2ac070ca0b09780bd45ede4e61c1
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102097758"
---
# <a name="device-update-agent"></a>Agent aktualizacji urządzeń

Aktualizacja urządzenia dla IoT Hub obsługuje dwie formy aktualizacji — oparte na obrazach i pakietach. 

* Aktualizacje obrazów zapewniają wyższy poziom zaufania w stanie końcowym urządzenia. Zwykle łatwiej jest replikować wyniki aktualizacji obrazu między środowiskiem przedprodukcyjnym i środowiskiem produkcyjnym, ponieważ nie ma ona takich samych wyzwań jak pakiety i ich zależności. Ze względu na ich charakter niepodzielny, jeden może również łatwo przyjmować model trybu failover A/B. 
* Aktualizacje oparte na pakiecie są przeznaczonymi do aktualizacji, które zmieniają tylko określony składnik lub aplikację na urządzeniu. Tym samym, co prowadzi do mniejszego zużycia przepustowości i skraca czas pobierania i instalowania aktualizacji. Aktualizacje pakietu zwykle umożliwiają zmniejszenie przestojów urządzeń podczas stosowania aktualizacji i unikania nakładów tworzenia obrazów. 

Skorzystaj z poniższych linków, aby skompilować, uruchomić i zmodyfikować agenta aktualizacji urządzenia.

## <a name="build-the-device-update-agent"></a>Kompilowanie agenta aktualizacji urządzeń

Postępuj zgodnie z instrukcjami, aby [skompilować](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-build-agent-code.md) agenta aktualizacji urządzenia ze źródła.

## <a name="run-the-device-update-agent"></a>Uruchamianie agenta aktualizacji urządzeń

Po pomyślnym skompilowaniu agenta zostanie [uruchomiony](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-run-agent.md) Agent programu.

## <a name="modifying-the-device-update-agent"></a>Modyfikowanie agenta aktualizacji urządzeń

Teraz wprowadź zmiany, które są konieczne do włączenia agenta do obrazu.  Zapoznaj się z tematem jak [zmodyfikować](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-modify-the-agent-code.md) agenta aktualizacji urządzeń, aby uzyskać wskazówki.

### <a name="troubleshooting-guide"></a>Przewodnik rozwiązywania problemów

Jeśli występują problemy, zapoznaj się z [przewodnikiem rozwiązywania problemów](troubleshoot-device-update.md) z aktualizacją urządzenia, aby uzyskać IoT Hub.

## <a name="next-steps"></a>Następne kroki

Poniżej wstępnie utworzonych obrazów i plików binarnych można łatwo zapoznać się z aktualizacją urządzeń dla IoT Hub.  

[Aktualizacja obrazu: Wprowadzenie with Raspberry Pi 3 B + Reference yocto Image](device-update-raspberry-pi.md)

[Aktualizacja obrazu: Wprowadzenie przy użyciu agenta referencyjnego symulatora Ubuntu (18,04 x64)](device-update-simulator.md)

[Aktualizacja pakietu: Wprowadzenie przy użyciu agenta pakietu Ubuntu Server 18,04 x64](device-update-ubuntu-agent.md)

