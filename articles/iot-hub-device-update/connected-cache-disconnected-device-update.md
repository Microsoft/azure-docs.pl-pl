---
title: Informacje na temat obsługi aktualizacji urządzeń odłączonych za pomocą połączonej pamięci podręcznej Microsoft | Microsoft Docs
titleSuffix: Device Update for Azure IoT Hub
description: Informacje o obsłudze aktualizacji urządzenia odłączonego przy użyciu połączonej pamięci podręcznej firmy Microsoft
author: andyriv
ms.author: andyriv
ms.date: 2/16/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: e2b27934f58402ecfb7dabf5560dc43e45f3f7dd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "101679545"
---
# <a name="understand-support-for-disconnected-device-updates"></a>Omówienie obsługi aktualizacji odłączonych urządzeń

W przypadku niejawnego scenariusza z bramą co najmniej jedno urządzenie może przekazać swoje wiadomości za pomocą jednego urządzenia bramy, które utrzymuje połączenie z usługą Azure IoT Hub. W takich przypadkach urządzenia podrzędne mogą nie mieć łączności z Internetem lub nie mogą pobierać zawartości z Internetu. Moduł IoT Edge w wersji zapoznawczej połączonej pamięci podręcznej firmy Microsoft udostępnia aktualizacje urządzeń dla klientów platformy Azure IoT Hub z możliwością inteligentnej pamięci podręcznej, która umożliwia korzystanie z opartych na obrazach i opartych na pakietach aktualizacji urządzeń opartych na systemie operacyjnym Linux i IoT Edge bramy (podrzędne urządzenia IoT), a także pomaga zaoszczędzić przepustowość dla aktualizacji urządzeń dla klientów usługi Azure IoT Hub.

## <a name="how-does-microsoft-connected-cache-preview-for-device-update-for-azure-iot-hub-work"></a>Jak działa wersja zapoznawcza pamięci podręcznej Microsoft Connect dla aktualizacji urządzenia dla platformy Azure IoT Hub?

Połączona pamięć podręczna firmy Microsoft to inteligentna, przejrzysta pamięć podręczna dla zawartości opublikowanej dla usługi Azure IoT Hub Content i można ją dostosować do zawartości w pamięci podręcznej z innych źródeł, takich jak repozytoria pakietów. Połączona pamięć podręczna firmy Microsoft jest zimną pamięcią podręczną, która jest podgrzewana przez żądania klientów dotyczące dokładnych zakresów plików wymaganych przez klienta optymalizacji dostarczania i nie zawiera wstępnej zawartości. Na diagramie i opisie krok po kroku wyjaśniono, jak działa połączona pamięć podręczna firmy Microsoft w ramach aktualizacji urządzenia dla infrastruktury IoT Hub platformy Azure.

>[!Note]
>W przypadku definiowania tego przepływu zakłada się, że brama IoT Edge ma łączność z Internetem. W przypadku scenariusza IoT Edge podrzędnej (zagnieżdżona krawędź) scenariusz "Content Delivery Network" (CDN) może być uznawany za MCC hostowaną w nadrzędnej bramie IoT Edge.

  :::image type="content" source="media/connected-cache-overview/disconnected-device-update.png" alt-text="Odłączono aktualizację urządzenia" lightbox="media/connected-cache-overview/disconnected-device-update.png":::

1. Połączona pamięć podręczna firmy Microsoft jest wdrażana jako moduł IoT Edge na serwerze Premium.
2. Aktualizacja urządzenia dla klientów usługi Azure IoT Hub jest skonfigurowana do pobierania zawartości z połączonej pamięci podręcznej firmy Microsoft na podstawie atrybutu GatewayHostName parametrów połączenia urządzenia dla urządzeń typu liść IoT **lub** parent_hostname ustawionych w pliku config. toml dla IoT Edge urządzeń podrzędnych.
3. Aktualizacja urządzenia dla klientów usługi Azure IoT Hub w obu przypadkach Odbieraj aktualizacje poleceń pobierania zawartości z aktualizacji urządzenia dla usługi Azure IoT Hub i Żądaj aktualizacji zawartości w połączonej pamięci podręcznej firmy Microsoft, a nie w sieci CDN. Domyślnie połączona pamięć podręczna firmy Microsoft jest skonfigurowana do nasłuchiwania na porcie http 80, a klient optymalizacji dostarczania wysyła żądanie zawartości na porcie 80, dlatego element nadrzędny musi być skonfigurowany do nasłuchiwania na tym porcie.  W tej chwili jest obsługiwany tylko protokół http.
4. Serwer połączonej pamięci podręcznej firmy Microsoft pobiera zawartość z usługi CDN, umieszcza ją w lokalnej pamięci podręcznej przechowywanej na dysku i dostarcza zawartość do aktualizacji urządzenia dla klienta IoT Hub platformy Azure.
   
>[!Note]
>W przypadku korzystania z aktualizacji opartych na pakiecie serwer połączonej pamięci podręcznej firmy Microsoft zostanie skonfigurowany przez administratora z wymaganą nazwą hosta pakietu.

5. Kolejne żądania od innych aktualizacji urządzeń dla klientów IoT Hub platformy Azure dla tej samej zawartości aktualizacji będą teraz dostępne w pamięci podręcznej i połączonej pamięci podręcznej firmy Microsoft nie będą wysyłane żądania do sieci CDN dla tej samej zawartości.

### <a name="supporting-industrial-iot-iiot-with-parentchild-hosting-scenarios"></a>Wspieranie usługi przemysł IoT (IIoT) z scenariuszami hostingu nadrzędny/podrzędny

Gdy brama IoT Edge podrzędnej lub podrzędna hostuje serwer połączonej pamięci podręcznej firmy Microsoft, zostanie on skonfigurowany do żądania aktualizacji zawartości z bramy IoT Edge nadrzędnego, który hostuje serwer połączonej pamięci podręcznej firmy Microsoft. Jest to wymagane dla możliwie największej liczby poziomów przed osiągnięciem nadrzędnej bramy IoT Edge hostowania serwera połączonej pamięci podręcznej firmy Microsoft, który ma dostęp do Internetu. Z serwera połączonego z Internetem z usługi CDN jest wymagana zawartość z punktu widzenia zawartości dostarczanej z powrotem do podrzędnej bramy IoT Edge, która początkowo zażądała zawartości. Zawartość będzie przechowywana na dysku na każdym poziomie.

## <a name="access-to-the-microsoft-connected-cache-preview-for-device-update-for-azure-iot-hub"></a>Dostęp do wersji zapoznawczej połączonej pamięci podręcznej firmy Microsoft dla aktualizacji urządzenia dla platformy Azure IoT Hub

Moduł IoT Edge połączonej pamięci podręcznej firmy Microsoft jest wydawany w wersji zapoznawczej dla klientów, którzy wdrażają rozwiązania przy użyciu aktualizacji urządzenia dla platformy Azure IoT Hub. Dostęp do wersji zapoznawczej odbywa się według zaproszenia. [Zażądaj dostępu](https://aka.ms/MCCForDeviceUpdateForIoT) do wersji zapoznawczej połączonej pamięci podręcznej firmy Microsoft dla aktualizacji urządzenia IoT Wyłącz i podaj żądane informacje, jeśli chcesz uzyskać dostęp do modułu.
