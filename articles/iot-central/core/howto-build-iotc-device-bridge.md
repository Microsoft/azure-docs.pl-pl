---
title: Tworzenie mostka urządzeń usługi Azure IoT Central | Microsoft Docs
description: Utwórz mostek IoT Central urządzeń, aby połączyć inne chmury IoT (SIGFOX, cząsteczek, Sieć rzeczy itp.) do aplikacji IoT Central.
services: iot-central
ms.service: iot-central
author: viv-liu
ms.author: viviali
ms.date: 07/09/2019
ms.topic: how-to
manager: peterpr
ms.custom: device-developer
ms.openlocfilehash: fc8ea41e804344735cfa2400d5d763622d8811c8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "95026254"
---
# <a name="build-the-iot-central-device-bridge-to-connect-other-iot-clouds-to-iot-central"></a>Kompiluj IoT Central mostek urządzeń, aby połączyć inne chmury IoT z IoT Central

*Ten temat ma zastosowanie do administratorów.*

IoT Central mostka urządzenia to rozwiązanie "open source", które łączy SIGFOX, cząsteczek, Sieć rzeczy i inne chmury z aplikacją IoT Central. Bez względu na to, czy są używane urządzenia śledzenia zasobów połączone z siecią SIGFOX z niską przepustowością, czy za pomocą urządzeń do monitorowania jakości powietrza w chmurze urządzenia cząsteczkowego, czy też przy użyciu urządzeń monitorujących wilgotność gleby w TTN, można bezpośrednio wykorzystać możliwości IoT Central przy użyciu mostka IoT Central urządzeń. Mostek urządzenia łączy inne chmury IoT z usługą IoT Central, przekazując dane wysyłane przez urządzenia do innych chmur za pomocą aplikacji IoT Central. W aplikacji IoT Central można tworzyć reguły i uruchamiać analizy na tych danych, tworzyć przepływy pracy w usłudze Automatyzacja i usługi Azure Logic Apps, eksportować te dane i wiele innych. Pobierz [mostek urządzenia IoT Central](https://aka.ms/iotcentralgithubdevicebridge) z usługi GitHub

## <a name="what-is-it-and-how-does-it-work"></a>Co to jest i jak to działa?
IoT Central mostka urządzenia to rozwiązanie Open Source w usłudze GitHub. Jest to gotowe do użycia przycisk "wdróż na platformie Azure" służący do wdrażania niestandardowego szablonu Azure Resource Manager z kilkoma zasobami platformy Azure w ramach subskrypcji platformy Azure. Zasoby obejmują:
-    Aplikacja funkcji platformy Azure
-    Konto usługi Azure Storage
-    Plan zużycia
-    Azure Key Vault

Aplikacja funkcji jest elementem krytycznym mostka urządzeń. Odbiera żądania HTTP POST z innych platform IoT lub wszelkich platform niestandardowych za pośrednictwem prostej integracji elementu webhook. Przedstawiono przykłady, które pokazują, jak nawiązać połączenie z chmurami SIGFOX, cząsteczek i TTN. Możesz łatwo rozszerzenie tego rozwiązania, aby nawiązać połączenie z Twoją niestandardową chmurą IoT, jeśli platforma może wysyłać żądania POST protokołu HTTP do aplikacji funkcji.
Aplikacja funkcji przekształca dane w format akceptowany przez IoT Central i przekazuje je za pośrednictwem interfejsów API usługi DPS.

![Zrzut ekranu usługi Azure Functions](media/howto-build-iotc-device-bridge/azfunctions.png)

Jeśli aplikacja IoT Central rozpoznaje urządzenie według identyfikatora urządzenia w komunikacie przekazywanym, zostanie wyświetlona Nowa miara dla tego urządzenia. Jeśli identyfikator urządzenia nie był już widoczny w aplikacji IoT Central, aplikacja funkcji podejmie próbę zarejestrowania nowego urządzenia z tym IDENTYFIKATORem urządzenia i pojawi się jako "nieskojarzone urządzenie" w aplikacji IoT Central. 

## <a name="how-do-i-set-it-up"></a>Jak mogę skonfigurować?
Instrukcje są szczegółowo wymienione w pliku Readme w repozytorium GitHub. 

## <a name="pricing"></a>Ceny
Zasoby platformy Azure będą hostowane w ramach subskrypcji platformy Azure. Więcej informacji o cenach można znaleźć w [pliku Readme](https://aka.ms/iotcentralgithubdevicebridge).

## <a name="next-steps"></a>Następne kroki
Teraz, gdy wiesz już, jak skompilować mostek IoT Central urządzeń, poniżej przedstawiono sugerowany następny krok:

> [!div class="nextstepaction"]
> [Zarządzanie urządzeniami](howto-manage-devices.md)
