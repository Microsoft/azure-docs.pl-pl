---
title: Opis obsługi aktualizacji odłączonych urządzeń przy użyciu usługi Microsoft Connected Cache | Microsoft Docs
titleSuffix: Device Update for Azure IoT Hub
description: Opis obsługi aktualizacji odłączonych urządzeń przy użyciu usługi Microsoft Connected Cache
author: andyriv
ms.author: andyriv
ms.date: 2/16/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: e216d42ff1f279d87e657126514fcfb50960f806
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107811908"
---
# <a name="understand-support-for-disconnected-device-updates"></a>Opis obsługi aktualizacji odłączonych urządzeń

W scenariuszu przezroczystej bramy co najmniej jedno urządzenie może przekazywać komunikaty za pośrednictwem jednego urządzenia bramy, które utrzymuje połączenie z Azure IoT Hub. W takich przypadkach urządzenia podrzędne mogą nie mieć łączności z Internetem lub mogą nie mieć możliwości pobierania zawartości z Internetu. Moduł IoT Edge microsoft Connected Cache w wersji zapoznawczej zapewnia klientom usługi Azure IoT Hub funkcję inteligentnej pamięci podręcznej w sieci, która umożliwia oparte na obrazach i pakietach aktualizacje urządzeń opartych na systemie operacyjnym Linux za bramą i bramą IoT Edge (podrzędne urządzenia IoT), a także pomoże zaoszczędzić przepustowość aktualizacji urządzeń dla klientów korzystających z usługi Azure IoT Hub.

## <a name="how-does-microsoft-connected-cache-preview-for-device-update-for-azure-iot-hub-work"></a>Jak działa usługa Microsoft Connected Cache w wersji zapoznawczej dla aktualizacji Azure IoT Hub urządzenia?

Microsoft Connected Cache Preview to inteligentna, przezroczysta pamięć podręczna dla zawartości opublikowanej dla aktualizacji urządzenia dla zawartości usługi Azure IoT Hub i można ją dostosować do pamięci podręcznej zawartości z innych źródeł, takich jak repozytoria pakietów. Microsoft Connected Cache to zimna pamięć podręczna, która jest rozgrzewana przez żądania klientów dotyczące dokładnych zakresów plików żądanych przez klienta Optymalizacja dostarczania i nie zawiera wstępnej zawartości iniekcyjnej. Na poniższym diagramie i opisie krok po kroku wyjaśniono, jak usługa Microsoft Connected Cache działa w ramach aktualizacji urządzenia dla Azure IoT Hub infrastruktury.

>[!Note]
>Podczas definiowania tego przepływu przyjęto założenie, że brama IoT Edge ma łączność z Internetem. W scenariuszu bramy IoT Edge podrzędnej (zagnieżdżona brama brzegowa) "Content Delivery Network" (CDN) można uznać za mcc hostowaną w bramie nadrzędnej IoT Edge wirtualnej.

  :::image type="content" source="media/connected-cache-overview/disconnected-device-update.png" alt-text="Aktualizacja odłączonego urządzenia" lightbox="media/connected-cache-overview/disconnected-device-update.png":::

1. Usługa Microsoft Connected Cache jest wdrażana jako IoT Edge modułu na serwerze on-on-on-onm.
2. Aktualizacja urządzenia dla klientów usługi Azure IoT Hub jest skonfigurowana do pobierania zawartości z usługi Microsoft Connected Cache na podstawie atrybutu GatewayHostName parametrów połączenia urządzenia dla urządzeń liści IoT LUB parent_hostname ustawionych w pliku config.toml dla urządzeń IoT Edge podrzędnego. 
3. W obu przypadkach klienci aktualizacji Azure IoT Hub urządzeń otrzymują polecenia pobierania zawartości aktualizacji z usługi Device Update for Azure IoT Hub i żądają aktualizacji zawartości do usługi Microsoft Connected Cache zamiast usługi CDN. Domyślnie usługa Microsoft Connected Cache jest skonfigurowana do nasłuchiwać na porcie HTTP 80, a klient usługi Optymalizacja dostarczania wysyła żądanie zawartości na porcie 80, więc element nadrzędny musi być skonfigurowany do nasłuchiwać na tym porcie.  Obecnie obsługiwany jest tylko protokół HTTP.
4. Serwer microsoft Connected Cache pobiera zawartość z sieci CDN, iniekuje swoją lokalną pamięć podręczną przechowywaną na dysku i dostarcza zawartość do aktualizacji urządzenia dla Azure IoT Hub klienta.
   
>[!Note]
>W przypadku korzystania z aktualizacji opartych na pakietach Connected Cache Microsoft zostanie skonfigurowany przez administratora z wymaganą nazwą hosta pakietu.

5. Kolejne żądania od innych klientów usługi Device Update dla klientów usługi Azure IoT Hub dotyczące tej samej zawartości aktualizacji będą teraz pochodzić z pamięci podręcznej, Connected Cache microsoft Connected Cache nie będzie żądać tej samej zawartości do sieci CDN.

### <a name="supporting-industrial-iot-iiot-with-parentchild-hosting-scenarios"></a>Obsługa przemysłowego IoT (IIoT) w scenariuszach hostingu nadrzędnego/podrzędnego

Jeśli brama podrzędna lub podrzędna IoT Edge hostuje serwer Microsoft Connected Cache, zostanie skonfigurowana do żądania aktualizacji zawartości z nadrzędnej bramy IoT Edge hostowania serwera Microsoft Connected Cache. Jest to wymagane dla tak wielu poziomów, jak to konieczne przed dotarciem do nadrzędnej bramy IoT Edge hostującym serwer microsoft Connected Cache z dostępem do Internetu. Na serwerze połączonym z Internetem zawartość jest żądana od sieci CDN, w tym momencie zawartość jest dostarczana z powrotem do bramy podrzędnej usługi IoT Edge, która pierwotnie zażądała zawartości. Zawartość będzie przechowywana na dysku na każdym poziomie.

## <a name="access-to-the-microsoft-connected-cache-preview-for-device-update-for-azure-iot-hub"></a>Dostęp do wersji zapoznawczej usługi Microsoft Connected Cache aktualizacji urządzenia dla Azure IoT Hub

Moduł Microsoft Connected Cache IoT Edge jest wydany jako wersja zapoznawcza dla klientów, którzy wdrażają rozwiązania przy użyciu aktualizacji urządzenia na potrzeby Azure IoT Hub. Dostęp do wersji zapoznawczej można uzyskać za pomocą zaproszenia. [Zażądaj](https://aka.ms/MCCForDeviceUpdateForIoT) dostępu do usługi Microsoft Connected Cache (wersja zapoznawcza) dla aktualizacji urządzenia dla IoT i podaj żądane informacje, jeśli chcesz uzyskać dostęp do modułu.
